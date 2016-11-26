## 简介
JUnit是一个Java语言的单元测试框架。它由Kent Beck和Erich Gamma建立，逐渐成为源于Kent Beck的sUnit的xUnit家族中最为成功的一个。 

>测试用例不是用来证明你（的逻辑）是对的，而是用来证明你（的断言）没有错。

## junit3与junit4区别
1. 在JUnit3中需要继承TestCase类，JUnit4不需要继承任何类;
2. 在JUnit3中需要覆盖TestCase中的setUp和tearDown方法，其中setUp方法会在测试执行前被调用以完成初始化工作，而tearDown方法则在结束测试结果时被调用，用于释放测试使用中的资源，而在JUnit4中，只需要在方法前加上@Before，@After ;
3. 在JUnit3中对某个方法进行测试时，测试方法的命令是固定的，例如对addBook这个方法进行测试，需要编写名字为tetAddBook的测试方法，而在JUnit4中没有方法命令的约束，在方法的前面加上@Test,这就代表这个方法是测试用例中的测试方法;
4. 新的断言assertThat;
5. @BeforeClass 和 @AfterClass 。在JUnit3，如果所有的test case仅调用一次setUp()和tearDown()需要使用TestSetup类；
6. 测试异常处理@Test(expected = DataFormatException.class)；
7. 设置超时@Test(timeout = 1000)；
8. 忽略测试@Ignore；
9. 集成测试（suiteTest）。

最大的不同是junit4基本用注解实现，更加灵活。

## junit4与maven
添加进入maven的pom.xml的依赖中。设置scope为test;
```xml
<dependency>
  <groupId>junit</groupId>
  <artifactId>junit</artifactId>
  <version>4.12</version>
  <scope>test</scope>
</dependency>
```

## 常用注解
- @BeforeClass: public static void方法，当前测试类，所有的测试方法运行前被执行;
- @Before: public void方法，当前测试类，他会在所有方法运行结束后被执行;
- @Test: public void方法，将一个普通的方法修饰成为一个测试方法。具有两个参数（可选）：
    + timeout: 该测试方法允许执行的最大时间长度。单位ms
    + expected: 捕获抛出的异常。xx.class
- @After: public void方法，与@Before组成一对，会在每个测试方法被运行后执行一次;
- @AfterClass: public static void方法，与@BeforeClass组成一对，在当前测试类做完所有测试后执行的一个方法;
- @Ignore: 所修饰的测试方法会被测试运行器忽略;
- @RunWith: 更改测试运行器，自定义运行器需要继承于org.junit.runner.Runner。

## junit4的hello world
```java
import org.junit.*;

public class HelloTest {
    @Test
    public void testAdd(){
        Assert.assertEquals("test add", 3, 1 + 2);
        System.out.print("Test add Ok");
    }
}
```

- 测试方法上必须使用@Test进行修饰;
- 测试方法必须使用public void 进行修饰，不能待任何的参数;
- 测试单元中的每个方法必须可以独立测试，测试方法间不能有任何的依赖;
- 测试类使用Test作为类名的后缀(可选);
- 测试方法使用test作为方法名的前缀(可选);

## junit4运行流程
```java
public class FlowTest {

    @BeforeClass
    public static void init(){
        System.out.println("test class before");
    }

    @AfterClass
    public static void destory(){
        System.out.println("test class after");
    }

    @Before
    public void beforeTest(){
        System.out.println("before test");
    }

    @After
    public void afterTest(){
        System.out.println("after test");
    }

    @Test
    public void testAdd(){
        Assert.assertEquals("test add", 3, 1 + 2);
        System.out.println("test add ok");
    }

    @Test
    public void testSub(){
        Assert.assertEquals("test subtraction", 3, 4 - 1);
        System.out.println("test sub ok");
    }
}
```

运行结果
```bash
test class before
before test
test add ok
after test
before test
test sub ok
after test
test class after
```

- @BeforeClass修饰的方法会在所有测试方法调用前执行。该方法为静态方法，比较适合加载配置文件。
- @AfterClass修饰的方法会在所有测试方法调用后执行。该方法为静态方法，通常用来对资源的清理。比如关闭数据库连接。
- @Before与@After在每个测试方法前后各执行一次。

## @Test中的参数
```java
public class TimeOutTest {

    @Test(timeout = 2000)
    public void testTimeOut(){
        while (true){
            System.out.println("I'm running!");
            try {
                Thread.sleep(1000 *1);
            } catch (InterruptedException ignore) {
            }
        }
    }
    
    @Test(expected = ArithmeticException.class)
    public void testException(){
        Assert.assertEquals(3,6/0);
    }
}
```
运行结果
```bash
I'm running!
I'm running!
I'm running!

org.junit.runners.model.TestTimedOutException: test timed out after 2000 milliseconds
	at java.lang.Thread.sleep(Native Method)
	at TimeOutTest.testTimeOut(TimeOutTest.java:15)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        ...
```
- testTimeOut虽然是死循环，但加了@Test(timeout)参数后还是在2秒运行结束。
- testException方法没有抛出ArithmeticException，@Test中的expected起作用了。

## 批量运行测试类-测试套件(Suite)
我们想运行所有的测试类的测试方法，难道我们只能一个一个运行每一个测试类么，这多累啊。幸好，junit4给我们提供了一种方式一次运行所有的测试类-`测试套件`。使用例子如下：

```java
import org.junit.runner.RunWith;
import org.junit.runners.Suite;

@RunWith(Suite.class)
@Suite.SuiteClasses({FlowTest.class,TimeOutTest.class})
public class SuiteTest {
}
```
- 测试suite就是组织测试类一起运行；
- 写一个作为测试suite的入口类。这个类不包含任何方法;
- 更改suite入口类的测试运行器为Suite.class;
- 将要运行的测试类作为数组传入到@Suite.SuiteClasses({})中。

## 批量运行多组测试用例-测试参数化设置
很多时候，我们需要对一个测试，输入多组测试用例来验证代码的正确性。在junit4中，我们不需要编写n个测试方法。示例如下：
```java
import org.junit.Assert;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.junit.runners.Parameterized;
import java.util.Arrays;
import java.util.Collection;

@RunWith(Parameterized.class)
public class ParamsTest {
    private int expected;
    private int input1;
    private int input2;
    
    public ParamsTest(int expected, int input1, int input2){
        this.expected = expected;
        this.input1 = input1;
        this.input2 = input2;
    }

    @Parameterized.Parameters
    public static Collection<Object[]> params(){
        return Arrays.asList(new Object[][]{
                {3,2,1},
                {4,1,4}
        });
    }

    @Test
    public void testAdd(){
        Assert.assertEquals("add function",this.expected,this.input1 + this.input2);
    }
}
```
运行结果
```bash
java.lang.AssertionError: add function 
Expected :4
Actual   :5
 <Click to see difference>
	at org.junit.Assert.fail(Assert.java:88)
	at org.junit.Assert.failNotEquals(Assert.java:834)
	at org.junit.Assert.assertEquals(Assert.java:645)
	at ParamsTest.testAdd(ParamsTest.java:34)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        ...
```
第一组测试通过，第2组没有

- 更改默认的测试运行器为@RunWith(Parameterized.class);
- 声明变量来存放预期值(随便起名字,class中自己使用，expected/input1/input2);
- 为测试类声明一个带有参数的公共构造器，并在其中为之声明变量赋值;
- 声明一个返回值为Collection的公共静态方法 并用@Parameterized.Parameters进行修饰。

## 他山之石
- junit官网getstart: [https://github.com/junit-team/junit4/wiki/Getting-started](https://github.com/junit-team/junit4/wiki/Getting-started)
- junit4入门视频: [http://www.imooc.com/learn/356](http://www.imooc.com/learn/356)
- junit4手册:[http://wiki.jikexueyuan.com/project/junit/suite-test.html](http://wiki.jikexueyuan.com/project/junit/suite-test.html)