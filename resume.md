<center><h1>个人简历</h1></center>

## 个人信息

- 谭明/男/1994
- 本科/西南交通大学(211)-计算机系
- 工作年限：2年
- 技术博客：[http://www.jianshu.com/users/5a327aab786a/latest_articles](http://www.jianshu.com/users/5a327aab786a/latest_articles)
- Github： [https://github.com/hirudy](https://github.com/hirudy)
- 期望职位：后端工程师(java/python/php)
- 期望薪资：月薪16k
- 期望城市：深圳
- 手机： 18583709203
- 邮箱：hirudy@qq.com、hirudytan@gmail.com
- QQ：841927469

## 技能清单

- 基础：常用数据结构与算法、linux操作系统、数据库原理、tcp/udp/http、dfa、部分设计模式、协程
- 程序语言工具：熟悉java、php、python、javascript/html/css,了解c、lua
- 开发框架：熟悉yii、jquery、thrift、workerman, 了解swoole
- 数据库：熟悉mysql、redis、beanstalk消息队列， 了解memcache、zookeeper、mongodb
- web容器：熟悉nginx, 了解apache
- 操作系统：熟悉linux/unix，熟悉crontab、shell，了解awk
- 工程相关：熟悉svn/maven/pecl/jinkens/禅道/阿里rap, 了解git/jumpserver

## 课外书单
- 基础: 《算法-第4版》、《TCP/IP详解 卷一》、《Head First 设计模式》、《HTTP权威指南》(部分)
- 程序语言工具: 《Java核心技术 卷1/2》、《深入理解Java虚拟机》、《Java NIO》、《Java程序员修炼之道》、《PHP 核心技术与最佳实践》、《高性能PHP应用开发》、《Python参考手册》
- 数据库: 《深入浅出mysql》、《REDIS入门指南》、《Redis设计与实现》
- web前端: 《CSS权威指南》、《Javascript高级程序设计》、《HTML5与CSS3权威指南》
- 其他:《Hadoop实战》(部分)、《Lua中文教程》(部分)、《memcached权威指南》(部分)

## 工作经历
#### 掌游宝(2015.06 - 至今)
成都我趣科技公司的掌游宝是一款游戏综合社区。注册用户3000万，日活200万。员工大部分是腾讯出来，我的leader是腾讯T3.3高级工程师。

***1. 外部数据服务***

- tgp中lol战绩查询/战网中dota2战绩查询：独立负责，用30台服务器，1500个qq提供登录服务，获取lol游戏中玩家数据信息。接口调用量。接口调用量`30万/小时`,使用技术将lua嵌入客户端，使用python编写后台服务，提供策略下发，lua脚本更新。使用beanstalkd消息队列做中转。memecache做缓存。
- 视频解析：独立负责，提供掌游宝所有产品的视频解析服务。设计思路同战绩查询。
- 守望先锋战绩查询：独立负责，守望先锋战绩只有网页版。同微信jssdk实现原理。在客户端嵌入javascript脚本解析，java提供服务，并且对接口做对称加密。达到效果：比竞品更早更好更快提供查询服务。因此获得当月公司`最高kpi考核-S`。
- 精灵go地图信息查询：独立负责，利用开源精灵api和tornado(python)中的协程实现。
- 各种爬虫：独立负责，一开始使用python中的scrapy来做，后来发现不在掌控中，自己利用php的协程与事件选择器实现了`TSpider（见github）`框架来完成后来的爬虫任务。
- dnf时装模拟数据：独立负责，事实上，这个是没啥技术含量的。是一些大量的重复性工作。之所以写出来。是我认为这是一个需要耐心的项目。做的比较揪心。

- 离线爬虫系统：独立负责，完成对多个网站的爬取与维护，使用redis做消息队列，python完成具体开发。具体设计架构参见我的博客《2016年，我对爬虫的总结》

***2. 文字直播/赛事弹幕服务***

- 赛事弹幕：独立负责，利用workerman(php)来实现长连接，消息推送，利用redis来做数据中转，房间分配。gateway部署2台机器，worker部署2台机器，register部署1台，可平行扩容。同时在线人数最高`1100人/场`，平均同时赛事为2场;
- 文字直播：维护与后期开发，提供我趣文字直播。大致结构同赛事弹幕。

***3. 广告系统***

- 广告服务：参与设计与部分接口实现。设计架构是我们典型的设计。网关(php + redis) + thrift + java服务(advcache+smartche) + mysql。接口调用量`400万/小时`。
- 广告服务cms: 独立设计与实现。为广告服务服务。主要使用yii,jquery,腾讯的前端渲染引擎template.js。遵循前后端分离原则。实现较复杂的交互设计。

***4. 用户中心/充值支付服务***

- 充值支付：维护与后期开发，我趣充值支付对接两家服务微信、支付宝。设计货币，钻石/金币/掌豆。利用订单号与mysql中的写锁（for update）保证数据一致性。典型的我趣架构设计。月流水百万。

***5. 脏词过滤服务***

- 脏词过滤：维护与后期开发，消息队列中转，具体脏词通过java服务事项，字典树。过滤用户评论。

***6. 其他***

dnf时装模拟数据、lol web页面改造、IM即时通讯接入、优酷视频上传、内容运营数据统计、chaoorder日志统计服务、lol游戏新老架构迁移、捕鱼支付系统

#### 实习僧(2014.05 - 2015.03)
个人创业项目，该项目开始于2013年，目前已拿到天使投资500万，百度搜索“实习生”词条第一行。

***1. 责任人***

搭建其原始版本的web服务。一个典型的lamp + memcached。我是两个主程之一。我参与后端开发，负责前端开发。