原文：[https://greenlet.readthedocs.io/en/latest/](https://greenlet.readthedocs.io/en/latest/)

# 背景

`greenlet`包是[Stackless](https://bitbucket.org/stackless-dev/stackless/wiki/Home)的衍生产品，它是一个支持微线程（叫tasklets）的CPython版本。Tasklets运行在伪并发模式下（通常在一个或少许的OS级别的线程），他们通过“channels”来交互数据。

另一方面来说， 一个“greenlet”任然是一个没有内部调度的关于微线程的较为原始的概念。换句话说，当你想要在你代码运行时做到准确控制，“greenlet”是一种很有用的方式。在greenlet基础之上，你可以定义自己的微线程调度策略。不管怎样，greenlets也可以以一种高级控制流结构的方式用于他们自己。举个例子，我们可以重新生成迭代器。python自带的生成器与greenlet的生成器之间的区别是greenlet的生成器可以嵌套调用函数，并且嵌套函数也会yield值（补充说明的是，你不需要使用yield关键词，参见例子：[test_generator.py](https://github.com/python-greenlet/greenlet/blob/master/tests/test_generator.py)）。

# 例子

我们来考虑一个用户输入命令的终端控制台系统。假设输入是逐个字符输入。在这样的一个系统中，有个典型的循环如下所示：

```python
def process_commands(*args):
    while True:
        line = ''
        while not line.endswith('\n'):
            line += read_next_char()
        if line == 'quit\n':
            print "are you sure?"
            if read_next_char() != 'y':
                continue    # ignore the command
        process_command(line)
```

现在，假设你将程序移植到GUI程序中，绝大部分的GUI成套工具是基于事件驱动的。他们为每一个用户字符输入调用一个回调函数。(将“GUI”替换成“XML expat parser”，对你来说应该更加熟悉了)。在这样的情形中，执行下面的函数read_next_char()是很困难的。这里是两个不兼容的函数：

```python
def event_keydown(key):
    ??

def read_next_char():
    ?? should wait for the next event_keydown() call
```

你可能考虑用线程的方式来实现这个了。greenlets是另一种不需要关联锁与没有当机问题的可选的解决方案。你执行process_commands()，独立的greenlet。通过如下方式输入字符串。

```python
def event_keydown(key):
         # jump into g_processor, sending it the key
    g_processor.switch(key)

def read_next_char():
        # g_self is g_processor in this simple example
    g_self = greenlet.getcurrent()
        # jump to the parent (main) greenlet, waiting for the next key
    next_char = g_self.parent.switch()
    return next_char

g_processor = greenlet(process_commands)
g_processor.switch(*args)   # input arguments to process_commands()

gui.mainloop()
```
这个例子中，执行流程如下：

- 当作为g_processor greenlet一部分的read_next_char()函数被调用，所以当接收到输入切换到上级greenlet, 程序恢复到主循环（GUI）执行。
- 当GUI调用event_keydown()的时候，程序切换到g_processor。这就意味着程序跳出，无论它被挂起在这个greenlet什么地方。在这个例子中，切换到read_next_char(),并且在event_keydown()中被按下的key作为switch()的结果返回给了read_next_char()。

需要说明的是read_next_char()的挂起与恢复都保留其调用堆栈。以便在prprocess_commands()中根据他来的地方恢复到不同的位置。这使得以一种好的控制流来控制程序逻辑成为可能。我们不必完整的重写process_commands(),将其转换为状态机。

# 用法

## 序言

“greenlet” 是微型的独立的伪线程。考虑到作为一个帧堆栈。最远的帧（最底层）是你调用的最初的函数，最外面的帧（最顶层）是在当前greenlet被压进去的。当你使用greenlets的时候是通过创建一系列的这种堆栈，然后在他们之间跳转执行。这种跳转将会导致先前的帧挂起，最后的帧从挂起状态恢复。在greenlets之间的跳转关系叫做“switching(切换)”。

当你创建一个greenlet,它将有一个初始化的空堆栈。当你第一次切换到它，它开始运行一个具体的函数。在这个函数中可能调用其他的函数，从当前greenlet中切换出去，等等。当最底层的函数完成执行，greenlet的栈再次为空，这时，greenlet死亡。greenlet也可能应一个未捕获的异常而终止。

举个例子：
```python
from greenlet import greenlet

def test1():
    print 12
    gr2.switch()
    print 34

def test2():
    print 56
    gr1.switch()
    print 78

gr1 = greenlet(test1)
gr2 = greenlet(test2)
gr1.switch()
```

- 最后一行跳转到test1, 然后打印12，
- 跳转到test2, 然后打印56
- 跳转回test1, 打印34， test1完成，并且gr1死亡。与此同时，程序执行返回到`gr1.switch()`调用。
- 需要说明的是78从来都没有打印。

## 父级greenlet

让我们看看当greenlet死亡的时候，程序执行到哪里去了。每一个greenlet都有一个父级greenlet。最初的父级是创建greenlet的那一个greenlet（父级greenlet是可以在任何时候被改变）。父级greenlet是当一个greenlet死亡的时候程序继续执行的地方。这种方式，程序组织成一颗树。不在用户创建的greenlet中运行的顶层代码在隐式的主greenlet中运行，它是堆栈数的根。

在上面的例子中，gr1与gr2将主greenlet作为父级greenlet。无论它们中的谁执行完毕，程序执行都会返回到"main"greenlet中。

没有捕获的异常将抛出到父级greenlet中。举个例子，如果上面的test2()包含一个语法错误，它将生成一个杀死gr2的NameError错误，这个错误将直接跳转到主greenlet。错误堆栈将显示test2,而不会是test1。需要注意的是，switches不是调用，而是程序在并行的"stack container(堆栈容器)"直接执行的跳转，“parent”定义了逻辑上位于当前greenlet之下的堆栈。

## 实例化对象

`greenlet.greenlet`是一个协程类型，它支持一下操作：

- `greenlet(run=None,parent=None)`：创建一个新的greenlet对象（还没有开始运行）。`run`是一个可调用的函数，用来被调用。`parent`定义父级greenlet，默认是当前greenlet。
- `greenlet.getcurrent()`：获取当前greenlet(即，调用该函数的greenlet)
- `greenlet.GreenletExit`：这个特殊的异常不会抛出到父级greenlet中，这可以用来杀死一个单一的greenlet。

`greenlet`类型可以被子类化。通过调用在greenlet创建的时候初始化的`run`属性来执行一个greenlet。但是对于子类来说，定义一个`run`方法比提供一个`run`参数给构造器更有意义。

## 切换

当在一个greenlet中调用方法switch()，在greenlet之间的切换将发生，正常情况下，程序执行跳转到switch()被调用的greenlet中。或者当一个greenlet死亡，程序执行将跳转到父级greenlet程序中，当发生切换的时候，一个对象或一个异常被发送到目标greenlet中。这是一种在两个greenlet中传递信息的便利的方式。举个例子：

```python
def test1(x, y):
    z = gr2.switch(x+y)
    print z

def test2(u):
    print u
    gr1.switch(42)

gr1 = greenlet(test1)
gr2 = greenlet(test2)
gr1.switch("hello", " world")
```
已与之前例子相同顺序执行，它将会打印“hello world”与42。多说一句，test1(),test2()的参数不是在greenlet创建的时候给的，而是在第一次切换的时候给出。

这里给出了关于发送的数据的明确的规则：

`g.switch(*args, **kwargs)`：切换执行到greenlet `g`,发送数据，作为一个特殊的例子，如果`g`没有执行，它将开始执行。

对于将死的greenlet。当run()完成的时候，将会发生对象给父级greenlet。如果greenlet因为异常而终止，这个异常将会抛出到父级greenlet中（greenlet.GreenletExit例外，这个异常被捕获了并且直接退出到父级greenlet中）。

除了上面例子描述的，通常目标greenlet(父级greenlet)接收之前调用switch()挂起，执行完毕返回的返回值作为结果。事实上，虽然对switch()的调用不会立即返回结果，但是当其他一些greenlet切换回来的时候，在将来的某个点将会返回结果。当切换发生的时候，程序将在它之前挂起的地方恢复。switch()自己返回发生的对象。这就意味着`x=g.switch(y)`将`y`给`g`,稍后将返回从某个不关联的greenlet中返回的不关联的对象给`x`变量。

提醒一下，任何试图切换到一个死亡的greenlet的将会走到死亡greenlet的父级，或者父级的父级，以此类推（最终的父级是“main” greenlet,它是从来不会死掉的）。

## greenlets的方法与属性

- `g.switch(*args, **kwargs)`：切换程序到greenlet `g`中执行，参见上面。
- `g.run`：当它开始的时候，`g`的回调将会被执行，当`g`已经开始执行了，这个属性将不会存在了。
- `g.parent`：父级greenlet。这是可编辑属性，但是不能够写成了死循环。
- `g.gr_frame`：最顶层的结构，或者等于None。
- `g.dead`： bool值，当`g`死亡了，值为True。
- `bool(g)`：bool值，当返回结构是True，表示`g`还活跃，如果是False，表示它死亡了或者还没开始。
- `g.throw([typ, [val, [tb]]])`：切换到`g`执行，但是立马抛出一个给定的异常。如果没有参数提供，默认异常是`greenlet.GreenletExit`。同上面描述一样，正常的异常传递规则生效。调用该方法同下面代码是几乎等价的：

    ```python
    def raiser():
        raise typ, val, tb
    g_raiser = greenlet(raiser, parent=g)
    g_raiser.switch()
    ```
    
    有一点不同的是，这段代码不能用于`greenlet.GreenletExit`异常，这个异常将不会从`g_raiser`传播到`g`。

## Greenlets与python的线程

Greenlets将可以和python线程结合起来。这种情况下，每一个线程包含一个独立的带有一个子greenlets树的“main” greenlet。混合或切换在不同线程中的greenlets是不可能的事情。

## greenlets的垃圾回收生命周期

如果对一个greenlet的所有关联都已经失效（包括来自其他greenlets中的父级属性的关联），这时候，没有任何一种方式切换回该greenlet中。这种情况下，GreenletExit异常将会产生。这是一个greenlet接受异步执行的唯一方式。使用`try:finally:`语句块来清理被这个greenlet使用的资源。这种属性支持一种编程风格，greenlet无限循环等待数据并且执行。当对该greenlet的最后关联失效，这种循环将自动终止。

如果greenlet要么死亡，要么根据存在某个地方的关联恢复。只需要捕获与忽略可能导致无限循环的GreenletExit。

Greenlets不参与垃圾回收。循环那些在greenlet框架中的数据时候，这些数据将不会被检测到。循环的存储其他greenlets的引用将可能导致内存泄漏。

## 错误堆栈支持

当使用greenlet的时候，标准的python错误堆栈与描述将不会按照预期的运行，因为堆栈与框架的切换发生在相同的线程中。使用传统的方法可靠的检测greenlet切换是一件很困难的事情。因此，为了改善对greenlet基础代码的调试，错误堆栈，问题描述的支持，在greenlet模块中，有一些新的方法：

- `greenlet.gettrace()`：返回先前已有的调用堆栈方法，或者None。
- `greenlet.settrace(callback)`：设置一个新的调用堆栈方法，返回前期已有的方法或者None。当某些事件发生时，这个回调函数被调用，可以永安里做一下信号处理。

    ```python
    def callback(event, args):
        if event == 'switch':
            origin, target = args
            # Handle a switch from origin to target.
            # Note that callback is running in the context of target
            # greenlet and any exceptions will be passed as if
            # target.throw() was used instead of a switch.
            return
        if event == 'throw':
            origin, target = args
            # Handle a throw from origin to target.
            # Note that callback is running in the context of target
            # greenlet and any exceptions will replace the original, as
            # if target.throw() was used with the replacing exception.
            return
    ```

    为了兼容，当事件要么是`switch`要么是`throw`,而不是其他可能的事件时候，将参数解包成tuple。这样，API可能扩展出于`sys.settrace()`相似的新的事件。

# C API 相关

Greenlets可以通过用C/C++写的扩展模块来生成与维护，或者来自于嵌入到python中的应用。`greenlet.h` 头文件被提供，用来展示对原生的python模块的完整的API访问。

## 类型

|Type name|Python name|
|---|---|
|PyGreenlet|greenlet.greenlet|

## 异常

|Type name|Python name|
|---|---|
|PyExc_GreenletError|greenlet.error|
|PyExc_GreenletExit|greenlet.GreenletExit|

## 关联

- `PyGreenlet_Import()`：一个宏定义，导入greenlet模块，初始化C API。必须在每一个用到greenlet C API的模块中调用一次。
- `int PyGreenlet_Check(PyObject *p)`：一个宏定义，如果参数是PyGreenlet返回true。
- `int PyGreenlet_STARTED(PyGreenlet *g)`：一个宏定义，如果greenlet在开始了返回true。
- `int PyGreenlet_ACTIVE(PyGreenlet *g)`：一个宏定义，如果greenlet在活动中返回true。
- `PyGreenlet *PyGreenlet_GET_PARENT(PyGreenlet *g)`：一个宏定义，返回greenlet中的父级greenlet。
- `int PyGreenlet_SetParent(PyGreenlet *g, PyGreenlet *nparent)`：设置父级greenlet。返回0为设置成功，-1,表示`g`不是一有效的PyGreenlet指针，AttributeError将抛出。
- `PyGreenlet *PyGreenlet_GetCurrent(void)`：返回当前活跃的greenlet对象。
- `PyGreenlet *PyGreenlet_New(PyObject *run, PyObject *parent)`：使用`run`与`parent`创建一个新的greenlet对象。这两个参数是可选的。如果`run`是NULL。这个greenlet创建，如果切换开始将失败。如果parent是NULL。这个parent将自动设置成当前greenlet。
- `PyObject *PyGreenlet_Switch(PyGreenlet *g, PyObject *args, PyObject *kwargs)`：切换到greenet `g`。`args`与`kwargs`是可选的，可以为NULL。如果`args`为NULL,一个空的tuple将发送给目标greenlet `g`。如果`kwargs`是NULL的。没有key-value参数发送。如果指定参数，那么`args`应该是一个tuple,`kwargs`应该是一个dict。
- `PyObject *PyGreenlet_Throw(PyGreenlet *g, PyObject *typ, PyObject *val, PyObject *tb)`：切换到greenlet `g`,并且立马抛出`typ`参数(携带的值`val`)指定的异常，调用堆栈对象`tb`是可选的，并且可以为NULL。

# 索引与表

- [Search Page](https://greenlet.readthedocs.io/en/latest/search.html)