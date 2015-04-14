---
layout: post
title:  "Falcon's Story"
date:   2015-04-13 17:25:12
categories: tekni:k
---

#Falcon's Story
Falcon, 猎鹰, 猎头的助手, 整合最新最好(设置搜索条件)的简历给猎头HR. 

以上是广告, technically, Falcon是一个操作和抓取网页及其数据并做适当展现的app.

###v1.0
本着Lean Startup的精神, 所以第一个版本是Turk的. 在跟HR聊了基本的需求后, 我每天会手工浏览招聘网站并将筛选的信息发邮件给HR.

最早是酱的:

<img src="{{ site.url }}/asset/falcon_1.png" style="width: 600px;"/>

当时的需求:

1. 每天收到最新的简历
2. 简历数不需要太多, 暂定为5
3. 搜索条件固定, 后期将可自定义
4. 收取形式暂定为邮件

由于是Turk的, 所以效率很高, 这点上得到反馈很好.

###v1.1
几天后得到了一些需要改进的反馈

1. 增加'自我评价'列
2. 通过链接访问完整简历是仍需要登录
3. 自定义搜索条件

于是增加了'自我评价'列, 并用附件形式附上完整简历, Turk时间变长, 并且经常忘记附件.

<img src="{{ site.url }}/asset/falcon_2.png" style="width: 600px;"/>

###v2.0
由于改变了展现形式, 所以升级版本号到2.0, <http://falcon.meteor.com/>. 当时考虑需求基本确定, 所以改用网页来展示结果, 更为友好直接. 使用了Meteor, UI还是很不错的.

<img src="{{ site.url }}/asset/falcon_3.png" style="width: 600px;"/>

这个阶段的反馈

1. 不支持自定义搜索条件
2. 希望显示完整简历

对于第二点反馈, 其实我觉得并不是很make sense, 这样下去会做成一个一样的招聘网站, 所以一直没做.

###v2.1
这个时候同时也在做一些自动化的工作, 希望能让<http://falcon.meteor.com/>定时自动更新, 或者由某个按钮trigger. 但是, 总有个但是, 对Meteor不是很熟, 所以技术上block了...

###v2.2
跟Charley聊天, 他建议我用Travis CI来做数据抓取, 这样就可以用任何熟悉的语言. 于是果断注册搞起, 来一张截图.

<img src="{{ site.url }}/asset/falcon_4.png" style="width: 600px;"/>

Hummer, 蜂鸟, 猎鹰的小弟. 这一阶段已经能够抓取数据, 于是考虑将其注入<http://falcon.meteor.com/>.

<img src="{{ site.url }}/asset/falcon_5.png" style="width: 600px;"/>

在做的过程中, 恍然醒悟, 如果CI能获取数据并放在什么地方的话, 不如就直接放在静态页面里, 也不用维护另一个网站了. 于是有了3.0.

###v3.0
这阶段首先换了CI, 用了Circle CI, 本意是想找个有定时构建功能的, 结果发现Circle CI 性能更好.
 还有TW的Snap CI, 对比劣势明显, 不表.
<img src="{{ site.url }}/asset/falcon_6.png" style="width: 600px;"/>

可以看到挂得很多, 然后第二天又都绿了. 其实是招聘网站的安全机制启用了, 登陆次数太多, 要求输入验证码, 所以CI不能登录, 就都挂了.

###v3.1
静态页面, 那Github Page最合适不过了. 选了个Github Page的模板, 全部hardcode, 获取数据然后写成html, 于是就有了新的[Falcon](http://micus.science/Falcon/)的页面.

<img src="{{ site.url }}/asset/falcon_7.png" style="width: 600px;"/>

点击ID就是获取的完整简历.

<img src="{{ site.url }}/asset/falcon_8.png" style="width: 600px;"/>

目前为止, 实现了显示完整家里的需求. 还有两点需要做的:
1. 自定义搜索条件
2. 到原网站的链接










