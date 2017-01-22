原文: [https://wiki.python.org/moin/Python2orPython3](https://wiki.python.org/moin/Python2orPython3)

# 在项目开发中，我应该用python2还是python3?

## 它们之间有什么区别？

>简而言之，python2.x是过去；python3.x是现在、是将来。

在2008年的时候，python3.0发布。python2.x的最后版--v2.7也于2010年年中发布，并且声明了对最后一版的延长支持。在这之后，python2.x分支就没有任何主要的新属性发布。3.x版本一直持续开发，并且已经发布了5年的稳定版本。2012年发布3.3，2014年发布3.4，2015年发布3.5，2016年发布3.6。这意味着新开发的标准库只在python3.x中展现。

在最新的2.x发布版本中，Guido van Rossum（python的创造者）决定适当的清理python2.x，以减少向后兼容性。最大的改变是更好的unicode编码支持（默认情况下所有文字都转换成unicode）与更加健全的bytes/unicode编码分离。

此外，在几个核心语言层面，做了一些调整（如，print与exec是声明，整数使用向下取整）。这些改变对初学者来说，更加容易学习。对Python其他部分也更加易用。一些老旧的丑陋的代码被移除，举个例子，所有的类都是new-style,"range"函数不再像python2.x一样返回list, 而是返回一个内存利用率高的迭代器。

[《python3.0中的新特性》](https://docs.python.org/3/whatsnew/3.0.html)对于主要的语言层改变以及与python2.x源码可能存在的兼用性做了一个很好的概述。Nick Coghlan（CPython开发者之一）也创建了一个[relatively extensive FAQ](http://python-notes.curiousefficiency.org/en/latest/python3/questions_and_answers.html)来处理python版本过渡问题。

然而，在过去的时间里，庞大的python生态系统已经累积了大量的重要的高质量的软件。对python3.x的兼容的最大问题就是，某些软件（尤其是一些公司内部软件）任然不能再python3.x下正常运行。

## 该如何选择我要使用的版本？

如何选择版本，主要依赖于你想要做什么。

如果你明确的知道用python3想要做什么。很好，这里有少许次要的缺陷或不足，如不是太严重的软件库支持问题，还有一个客观事实是，现在的很多linux发行版和mac都是将python2作为默认python版本（虽然在很多系统中，python3也被安装了），python3有着一个语言应该有的所有特性。如果你能控制你所安装的环境并且你明确知道你不需要python2的模块，使用python3是一个很好的选择。现在，很多的linux发行版已经内置了python3。并且所有这些都适用于终端用户。一些linux已经开始淘汰python2作为内置python版本。

特别指出的是，很多教师或书籍在介绍python的时候都会首先考虑python3，而后，如果有必要，再介绍python3与python2的不同之处。因为python3移除了一些，初学者学习python2而踩的一些不必要的坑。

然而，任然有一些问题需要你必须用python2而不是用python3。

- 你的应用所部署的环境是一个你不能控制的。这个环境中使用了特殊版本的python，而不是让你自由选择python版本。
- 你依赖的三方库没有兼容python3。并且这个库是一个不可或缺或及其重要的库。

python3在使用创建GUI应用已经得到了广泛的支持。包Tkinter作为标准库。从python3发布开始，PyQt也被支持。 在2011年，PySide被支持。PyGObject作为PyGtk的替代品也被支持。

这里列举了一些主要的支持python3的包：

- [NumPy](https://www.numpy.org/) 与 [SciPy](https://www.scipy.org/)(科学计算)
- [Django](https://www.djangoproject.com/)、[Flask](http://flask.pocoo.org/)、[CherryPy](https://wiki.python.org/moin/CherryPy)、[Pyramid](http://pyramid.readthedocs.io/en/latest/)(web开发)
- [Pyramid](https://python-pillow.org/)(图片处理，代替PIL)
- [cx_Freeze](http://cx-freeze.sourceforge.net/)(将python文件打包成可执行文件)
- [py2exe](http://www.py2exe.org/index.cgi/Tutorial)(打包成window可执行文件)
- [OpenCV 3](http://opencv.org/)(开源的机器学习与计算机图形库)
- [requests](http://docs.python-requests.org/en/master/)(http请求库)
- [lxml](http://lxml.de/)(python xml解析库)
- [BeautifulSoup4](https://www.crummy.com/software/BeautifulSoup/)(html dom解析库)
- [ipython](http://ipython.org/)与[jupyter](https://jupyter.org/)(交互计算)
- 等等

如果你想使用python3,但是你又害怕依赖兼容性问题。使用之前，做一些调研是值得的。这是一个持续跟进的过程，这篇wiki也可能过时。此外，有着对于python2.6+与python3.3+的大量支持，现在很多的python代码都不需要做较大的修改就可以运行在python3上。尤其是用于web与GUI框架的代码，这些框架强制应用区别二进制数据与文本([six compatiblility module](https://pypi.python.org/pypi/six)可以用来修补这些问题)。

虽然[官方文档](https://docs.python.org/3/)与[tutorial](https://docs.python.org/3/tutorial/)对python3镜像完整的更新。但是在网上与一些相关的书籍中的大量的文档是使用python2。这些文档虽然也在不断的更新。当用python3来运行的时候需要做一些调整。

有些人不想用python3。这是他们的权利。毕竟是少数人。

如果你想用其他一些python执行环境，如IronPython,Jython 或Pyston等等，使用python3是不值得的。在这些平台中python3的支持还是不太理想。当你应为系统的完整性或性能等等原因而选择前面所说的执行环境的时候，这个因素（在这些平台上，python3支持有限）将会影响你。

## 难道我不想避免使用python2? python2是一门有许多错误的老语言了，python已经开了一个主版本来移除这些错误。

good, 也不完全是，一些python3.0,python3.1的断层式的修改已经各自被移植到python2.6, 2.7。更多的移植相关，参见[What's New in Python2.6](https://docs.python.org/2.6/whatsnew/2.6.html)与[What's New in Python2.7](https://docs.python.org/2.7/whatsnew/2.7.html)

对于哪些属性只能用在python3与哪些属性不能移植到python2没有一个详尽的列表列出。

- 字符串默认编码为Unicode
- 清除unicode与bytes分离
- 异常链
- 函数注解
- 关键词参数语法
- 扩展的tuple解包
- 没有局部变量定义

语言版本的更迭，并没有限制核心代码的改变。在标准库中，一些在python3中的改善并没有直接移植到python2。参见[What's New in Python3](https://docs.python.org/3/whatsnew/)。一些标准库的提升可以通过PyPI找到。

也就是说，用python2写的代码更加像python3的代码。这可以代表很多事情。包括使用新类型的classe，不使用古老的弃用的print用法，使用懒加载的迭代器。举个例子，好的python2代码会用xrange来代替range。xrange最开始在python3上的range实现(当然，range在python3中表现更好一些，因为他可以handle住超过sys.maxint的值)。有点值得注意的是xrange()在python3中没有被定义。

最终要的是，python2或python3只是些小问题，你真正应该做的是写好好代码。这些代码包含完整的单元测试，正确的使用unicode。(在unicode与bytes问题上，python3相对于python2更少的操心。这是一件好事情，虽然这让一些软件包的移植变得相对来说比较恶心)

## 我想用Python3,但是想用的一些库只有python2。难道我只能不得不重新回去使用python2或者放弃使用这个库？

假设你找不到在python3中支持替代包，你可以看看下面的几种建议：

- 移植这个库到python3 ("porting"意味着你需要让这个库能正常的在python3上运行)
- 如果实在是实现比较困难，并且你的其他依赖也是用python2,你可以开始的时候用python2。随着库在其他地方被移植的，一旦每一个依赖库度偶做好了移植，好的python2代码可以进行轻松的切换。
- 好好想想这个库是否真的很重要？或许你可以不使用它。

最理想的状态是你试着移植到python3,你经常发现有人使用它，即使不是当前库，其他成员通常会感激你的。尤其是移植中发现原始的bug。这样做可以提高原始版本与python3移植版本的质量。移植不是一帆风顺的,但总是比你自己从头开始写容易。

在[Python2 porting guide](https://docs.python.org/3/howto/pyporting.html)中，你可以知道如何移植库。最基本的思想是使用python2库，在python2中使用-3 命令切换，检查所有的单元测试通过，而没有警告。如果测试失败，或者发出了警告信息。修改源码，再一次做单元测试（这可能需要在老的版本中降低兼容性）。当没有警告信息的时候，可以试着用python3运行这个库了。最好的可能的状态是运行的代码是python2,python3兼容的，这时候，移植完成。

如果在python3中，单元测试任然失败。那么标准库中的2to3组件能够自动生成运行在python3下的版本。或者Armin Ronacher的[python-modernize](https://pypi.python.org/pypi/modernize)组件可以用于python2.6+和3.2+或者3.3+(这依赖于命令行输出参数)。如果你使用python-modernize，同样需要在Python2下做单元测试。

任一种方法都可以从单个python2代码库并行支持python2和python3。将python2与python3分开维护更加容易一些。(可以问问核心代码开发者，他们这么做已经很多年了)

如果自动转换后，测试失败，有可能是因为python3与python2的语义变化。这些变化有可能导致转换器自动转换失败，-3 switch也没有检测出来。这些问题不多，但是也是存在的。当你遇到的时候，提交一个bug给CPython,请求一个新的 -3 警告，是非常值得的。

如果包含c扩展或者工程没有使用想CPython,cffi,SWIG这些能够值得处理python2与python3之间的差异的包裹生成器，移植过程可能更加复杂。但是还是会比你自己开发相同功能的包更容易一些。[extension porting guide](https://docs.python.org/3/howto/cporting.html)介绍了一些它们之间的主要的不同之处。

这里是一些比较深入的guide:[PortingPythonToPy3k](https://wiki.python.org/moin/PortingPythonToPy3k),[PortingExtensionModulesToPy3k](https://wiki.python.org/moin/PortingExtensionModulesToPy3k)

## 我想用python3写一些东西，但是有些人只用python2,我该怎么办？

除了有能够从python2代码生成python3代码的2to3工具，还有转换python3代码到python2代码的[3to2](https://wiki.python.org/moin/3to2)工具。理论上，可以3to2可能比2to3工作得更好，因为python3对转换器转换来数，清理掉了许多恶心的死角问题（毕竟，尽可能摆脱更多的这种问题是打破向后兼容性的主要原因之一）。然而，那些严重依赖python3特有属性的代码（例如函数注解，扩展版tuple unpacking）是不可能转换成功的。

可以举个恰当的比方，3to2相对于2to3来说是一条更少旅人的路你可能遇到一些边边角角。如果你想用python3, 这些是值得探索的。

## 在一些通用模块代码中支持python2与python3

python2.6+与python3.3+有大量的相同之处。例如,在python3中对unicode字符串前面加'u'字符的恢复意味着语义上正确的python2.6+代码可以与python3.3+兼容，同时保留着大量的惯用的python。主要的不同是来自不同地方的代码需要被修改，这样才可以处理python2与python3之间的命名问题。

因此，[six compatibility package](https://pythonhosted.org/six/)是在单一库中支持python2与python3最主要的实用程序。

[future compatibility package](http://python-future.org/index.html)任然在测试中，并且不像six包一样支持如此多的python版本（future只支持到python2.6, six可以支持到python2.4）。但是future运行python2兼容代码可以写得跟python3通用代码一样的风格（例如，它包含了python2兼容python3中bytes类型实现代码，而不是依赖于python2中的字节字符串类型，他们两个是不同的API）。

其他一些确定标准库的主要的因素是是否存在一个更新的对PyPi的移植，这些移植优先用于python2标准库。下面所列的这些模块要么是对PyPI的移植，要么是原生支持python2.7与python3标准库：

- [unittest2](https://pypi.python.org/pypi/unittest2)(Michael Foord, 标准库unittest维护者, 需要2.6支持)
- [mock](https://pypi.python.org/pypi/mock)(Michael Foord, 标准库unittest.mock维护者)
- [contextlib2](https://pypi.python.org/pypi/contextlib2)(Nick Coghlan, 标准库contextlib维护者)
- [configparser](https://pypi.python.org/pypi/configparser)(Łukasz Langa, 标准库configparser维护者)
- [futures](https://pypi.python.org/pypi/futures)(Alex Grönholm and Brian Quinlan, 标准库concurrent.futures维护者)
- [argparse](https://pypi.python.org/pypi/argparse)(Steven Bethard, 标准库argparse维护者, 需要2.6支持)
- [faulthandler](https://pypi.python.org/pypi/faulthandler)(Victor Stinner,标准库faulthandler维护者)
- [cdecimal](https://pypi.python.org/pypi/cdecimal/2.3)(Stefan Krah, 标准库decimal维护者)
- [ipaddr](https://pypi.python.org/pypi/ipaddr)((Peter Moody, 标准库ipaddress维护者)
- [stats](https://pypi.python.org/pypi/stats)(Steven D'Aprano, 标准库statistics维护者)
- [enum34](https://pypi.python.org/pypi/enum34)(Ethan Furman,标准库enum维护者)
- [funcsigs](https://pypi.python.org/pypi/funcsigs)(Aaron Iles,移植于函数signature objects)
- [shared namespace module for backports](https://pypi.python.org/pypi/backports)(Brandon Craig Rhodes)
- [backports.inspect](https://pypi.python.org/pypi/backports.inspect)(Tripp Lilley, 额外的inspect移植,基于funcsigs)
- [backports.datetime_timestamp](https://pypi.python.org/pypi/backports.datetime_timestamp)(Jason R. Coombs, datetime.timestamp的移植)
- [backports.pbkdf2](https://pypi.python.org/pypi/backports.pbkdf2)(Christian Heimes, 标准库hashlib维护者, 对hashlib.pbkdf2_hmac的移植)
- [backports.ssl_match_hostname](https://pypi.python.org/pypi/backports.ssl_match_hostname)(Brandom Craig Rhodes 与 Toshio Kuratomi, ssl.match_hostname的移植)
- [backports.lzma](https://pypi.python.org/pypi/backports.lzma)(Peter Cock, lzma wrapper模块的移植)
- [lzmaffi](https://pypi.python.org/pypi/lzmaffi)(Tomer Chachamu, lzma的移植)
- [tracemalloc](https://pypi.python.org/pypi/tracemalloc)(Victor Stinner, 标准库tracemalloc的维护者)
- [pathlib](https://pypi.python.org/pypi/pathlib)(Antoine Pitrou, 标准库pathlib的维护者)
- [selectors34](https://pypi.python.org/pypi/selectors34)(Berker Peksag,标准库selectors的移植)

当在交叉版本的标准库之间使用，使用移植版本的命名空间模块可以清楚的指明。原始版本的属性可以使用而不会产生冲突。

下面的模块不是移植版本。但是可以替换标准版本的各个版本兼容的库。

- [requests](https://pypi.python.org/pypi/requests)(对http/https更高级封装的api)
- [regex](https://pypi.python.org/pypi/regex)(一个替代的正则表达式引擎)
- [lxml.etree](https://pypi.python.org/pypi/lxml/)(ElementTree XML API的替代实现)

上面的这些模块也支持python2。在python3.4中，asyncio模块被添加到标准库中。

- [asyncio](https://pypi.python.org/pypi/asyncio)(Guido van Rossum, BDFL and标准库 asyncio维护者)

## 其他一些有助于在python2，python3做选择的资源

- [Community Web site to promote Python 3](http://getpython3.com/)
- [Nick Efford 关于使用python3教学的一些点评](http://www.comp.leeds.ac.uk/nde/papers/teachpy3.html)
- Mark Pilgrim 所写Python3版的《Dive Into Python》，[http://getpython3.com/diveintopython3/](http://getpython3.com/diveintopython3/)
- Swaroop C H 的《A Byte of Python》python3版本,[http://www.swaroopch.com/notes/Python](http://www.swaroopch.com/notes/Python)
- [What an IronPython user should know about Python 3](http://www.itworld.com/development/104506/python-3-and-ironpython)
-  Paul Barry的[Head First into Python 3](http://vimeo.com/groups/pyconireland/videos/14354395)
- Mark Summerfield所写的关于python2与python3不同的文章：[Moving from Python 2 to Python 3](http://ptgmedia.pearsoncmg.com/imprint_downloads/informit/promotions/python/python2python3.pdf)
- Wesley Chun所写的关于python3的两篇文章：[Python 3: the Evolution of a Programming Language (Mar 2009) ](http://www.informit.com/articles/article.aspx?p=1328795)与[Python's "New" Division: Python 2 Versus Python 3 (Jan 2010)](http://www.informit.com/articles/article.aspx?p=1439189)
- Wesley Chun的[Python 3: the Next Generation talk & slides](http://us.pycon.org/2010/conference/schedule/event/29)
- James Bennett 的一篇有趣的讨论:[why Python 3.0 exists at all](http://www.b-list.org/weblog/2008/dec/05/python-3000/)
- [how to get Unicode versus bytes semantics in 2.x similar to the ones in 3.x ](http://washort.twistedmatrix.com/2010/11/unicode-in-python-and-how-to-prevent-it.html)
- Nick Coghlan 的关于python3的问答：[Q&A](http://python-notes.curiousefficiency.org/en/latest/python3/questions_and_answers.html)

## 补充说明
- 任然在维护的包：[https://python3wos.appspot.com/](https://python3wos.appspot.com/)
- 主要的linux系统将python链接到python3, Ubuntu与Fedora设置为默认值：[https://wiki.ubuntu.com/Python/3](https://wiki.ubuntu.com/Python/3)与[https://fedoraproject.org/wiki/Changes/Python_3_as_Default ](https://fedoraproject.org/wiki/Changes/Python_3_as_Default )