title: scarpy爬虫
date: 2015-08-05 15:57:01
updated: 2015-08-05 15:57:06
tags: 
- python
- 基础
categories:
- python
---

>以下内容适用scrapy 1.0.1版本

<!--more-->

#架构
![architectrue](http://7xklyj.com1.z0.glb.clouddn.com/scrapy_.png)

>##Scrapy Engine
引擎，控管scrapy各部件的数据流，触发事件

>##Scheduler
调度器，从引擎获取请求，并发给引擎（主动请求）

>##Downloader
下载器，负责获取网页，发送给引擎，引擎再发给

>##Spiders
网虫(个人意译)，用户自定义类用来解析响应数据，从中提取爬取的项及url链接

>##Item Pipeline
爬取项流水线，负责处理网虫每次（yield）提取的项。通常的任务包括 验证、存储等

>##Downloader middlewares
下载中间件，位于引擎和下载器之间的钩子（们），处理从引擎到下载器的请求和下载器到引擎的响应。钩子提供了一个方便的机制通过插件来扩展scarpy的功能性

>##Spider middlewares
网虫中间件，位于引擎和网虫之间的钩子（们），性质类同上。

#数据流
scarpy的运行过程如下：
1. 引擎打开主域，定位网虫处理这个域名，请求网虫爬去第一个url
2. 引擎获取到这个url，向调度器发送这个url
3. 引擎让调度器返回下一个要爬取的链接
4. 调度器返回待爬取的下一个连接给引擎，引擎发送这个链接给下载器，如果有下载中间件则要通过中间件的处理最后送达下载器
5. 
（发现有中文文档了，这些内容没必要罗列了）


