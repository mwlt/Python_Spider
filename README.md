# Python_Spider
This is python spider project.
[Python] [爬虫] 批量政府网站的招投标、中标信息爬取和推送的自动化爬虫——脱离Scrapy框架（提供Github地址）

https://blog.csdn.net/weixin_42015762/article/details/83828108?spm=1001.2101.3001.6650.3&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-3.opensearchhbase&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-3.opensearchhbase


目录

1.Intro

2.Details

3.Theory

4.Environment and Configuration

5.Automation

6.Conclusion

1.Intro
作为Python的拥蹩，开源支持者，深信Python大法好，每天不写点整个人就会萎靡不振，虽是GIS专业出身，除了干地信开发的老本行，也会用些奇技淫巧做点偷懒的活计。

通常以前用Python，都只是在ArcGIS中处理一些空间分析和地图操作的自动化任务，这回头一次写爬虫，也算是做过的一个比较完整的新型项目，编码耗时1个多月，维护耗时5个月，目前稳定在5.1版本，做过几次较大的更新，现在回头去看，就引发了一个问题：“这谁写的代码这么狗屎！哦好像是我自己写的.....”。

2.Details
本来打算采用Scrapy作为爬虫的主要框架，但考虑到每个模块需要定制个性化操作，而且自己也想对其他爬虫库有更深入学习等原因（其实就是不会T-T），所以脱离了Scrapy框架，而采用了很多其他的爬虫库，比如urllib2、lxml（这个是真滴好用，虽然没正则强大，好用就对了）。

由于之前没有做爬虫的经验，所以这个项目的架构算是借鉴了几个比较成熟的爬虫项目，然后意淫出来的...

项目具体分为11个模块，其中包含了9个主要模块和2个扩展模块：

Id	Module Name	Remark
1	验证模块	主要模块
2	代理池	主要模块
3	配置管理器	主要模块
4	网页下载器	主要模块
5	网页解析器	主要模块
6	数据处理器	主要模块
7	数据推送模块	主要模块
8	爬虫日志	主要模块
9	调度引擎	主要模块
10	日志监控	扩展模块
11	代理池更新	扩展模块
其中引用了20个库（不包含自己写的）：

Id	Library Name	Remark
1	urllib2	网页抓取
2	requests	HTTP库
3	scrapy（就用到一处，打脸..）	都懂的
4	lxml	网页定位元素提取数据
5	pymongo	MongoDB操作
6	selenium	浏览器自动化测试框架
7	pyExcelerator（旧方法）	excel文件处理
8	multiprocessing	多进程
9	smtplib	发邮件的（支持SMTP服务）
10	email	构造邮件的（支持SMTP服务）
11	re	正则表达式
12	socket	套接字
13	gc	垃圾回收
14	retry	重试功能库
15	datetime	时间处理
16	time	时间模块
17	sys	操作系统交互
18	random	随机库
19	os	操作系统交互
20	logging	日志库
使用Selenium时需要注意，一般推荐PhantomJS无头浏览器配合使用，也可以安装Chrome、FireFox等。

自己实现了一个库Console_Color，用于控制打印文本的颜色，本来想配合tqdm（进度条库）一起使用，非常惊艳，可是一想到是自动化的，没人看...

3.Theory
受到Agile模式（敏捷开发）的影响，列了11个小周期来做Scrum，每个小周期就相当于一个模块，会根据任务量的大小逐一完成并通过测试，然后实现迭代，完成一次Sprint，而每个模块都会对外提供实现特殊功能的方法，最后通过调度引擎统一调配和管理。

Id	Module Name	File Name	Describe
1	验证模块	authentication	
验证数据库连接状态

验证网站连通性

验证代理有效性

2	代理池	proxyPool	
代理爬取

从数据获取随机代理

3	配置管理器	configManager	存储爬虫相关配置信息，如数据库配置、网站URL、报头等
4	网页下载器	pageDownloader	获取网页源代码
5	网页解析器	pageResolver	解析网页源代码
6	数据处理器	dataDisposer	
数据存储

数据删除

数据更新

数据清洗

获得数据库对象

7	数据推送模块	dataPusher_HTML、dataPusher	
 HTML引擎，可以生成HTML文件

从数据库获取数据，更新推送标识

格式化邮件地址

邮件推送

8	爬虫日志	spiderLog	日志写入到文本，包含普通信息、警告、错误、异常等
9	调度引擎	scheduleEngine	构造代理引擎、独立代理引擎、验证引擎、网页爬取引擎、数据推送引擎，然后统一调度，完成推送
10	日志监控	log_record	爬虫执行结果写入到数据库的日志表中
11	代理池更新	rebuild_proxy	清空代理池，重新爬取代理
 



在爬虫项目下有两个文件夹（可自己修改），用于存储推送文件和关键词库，分别为：

推送文件：..\history_file

关键词库：..\keyword_file\keyword.txt

其中，关键词库存储了需要提取的关键词，用于筛选爬取的数据。

同时，有一个Lib目录，包含了自写库Console_Color，可以自己添加写好的库。

自写库：..\Lib

4.Environment and Configuration
Environment：Windows 7 及之后版本 or Windows Server 2008 及之后版本（Linux不推荐，会出现各种问题，未解决）

Language：Python 2.7.14

IDE：Pycharm 2018.2.2、Robo 3T 1.2.1

DataBase：MongoDB 3.6.5

5.Automation
Linux自动化方式：crontab（不推荐，测试了很多次，会出现各种问题，未解决）

Windows自动化方式：任务计划程序（从运行中打开，快捷键为 win + R，输入 taskschd.msc），推荐在爬虫项目下生成两个bat文件，分别用于控制调度引擎和代理池重建，填入参数分别为：

python scheduleEngine.py
python rebuild_proxy.py
然后在任务计划程序中，输入对应的参数，并且在触发器里可以设置启动条件，即可实现自动化。



6.Conclusion
文件目录一览：

https://img-blog.csdnimg.cn/20181109105033541.jpg

爬虫源码之后会放在GitHub上，具体的配置和操作会在之后的模块详解中说到，不定期更新爬虫，有些代码块比较冗余需要优化，最近有想做成分布式，然后搞点数据挖掘方法，但数据量并不是很大，作罢。最后说一句，Python大法好。

2018.11.9更新，GitHub地址：https://github.com/Asada2142/Python_Spider

