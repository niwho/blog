title: webapp
date: 2016-01-24 23:11:13
updated: 2016-01-24 23:11:22
tags:
- hbuilder
- vue-router
- vuejs
- webapp
categories: 
- app
---

### 目标
做一个app，[后端源码](https://github.com/niwho/easygo), [前端代码](https://github.com/niwho/easygoapp)

<!--more-->

### vue-router
单页，避免页面间传值、webview窗口创建

### vue
前端数据绑定的mvvm框架，简单易用（上手很快）

### hbuilder
实现了h5接口，自带了mui的框架（暂不采用，原因…）

### 数据刷新视图
折腾了一天。。。，iscroll模拟app的下拉刷新，上拉加载更多。iscroll使用版本5，简单（参考官方demo）,版本4官方的demo就有个bug：上拉可能会卡住在提示那，不过4.2版有更好的动画，后面再考虑依葫芦画瓢加上。包装为vue组件，在vue-router中加载使用，注意数据更新，需要延迟回调 Vue.nextTick(function() {})的方式，刷新iscroll，vue-router有data回调（这里本来是设计为更新子路由组件的数据并保证完成更新到了视图这样可以解决需要知道数据完成渲染的时间点的问题，就像这里需要更新iscroll，其实这个设计方式初步看起来有点不合理，异步加载需要额外处理，不如直接在原始组件中异步加载方便，时间节点的问题用nextTick解决）

### 数据爬取(定向)
(具体爬取的网址。。。 )

主要是网络访问主要是网络访问io是决定性因素，使用协程，python的扩展gevent：pool设置并发量，gevent.spawn循环设置，注意join设置同步点
解析BeautifulSoup
数据库，django的orm，替换driver为支持异步访问的pymysql，注意monkey.path_all() 设置在manage.py文件里（command文件里不行）
目前进度
大体完成，h5的体验(部分原因可能用优化可以解决)感觉不够流畅，渲染的数据多了，能感觉到一些些的停顿（但没有白屏的切换问题，更好的形容是”延时”)

### todo&问题
* ~~资源下载列表重新设计 及其 加载也异步（一次加载全部，对几千条的数据，会有近3秒的加载，主要是获取数据耗时，总资源列表是下拉异步加载，有一项资源后来发现也有过多的问题。。。）~~
* ~~iscroll5 “控件”和vuejs结合使用（不确定单独使用是否也有同样的问题），其它页面的div（属性overflow：scroll）会失效不能正常滑动， 目前暂时解决 自己监听scroll实现上拉加载(简单实现，体验不好)~~
* ~~手机端的div overflow和pc端有差异，手机端不需要设置div的高度（否则滚动条不可见），pc端不设置高度无法滚动~~
* 后端数据爬取，目前是全量更新（考虑增量）另外貌似有数据丢失待确定
* 搜索功能(elasticsearch)
