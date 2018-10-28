# hybrid的起源
## 简介
  随着移动浪潮的兴起，各种APP层出不穷，极速的业务扩展提升了团队对开发效率的要求，这个时候使用IOS&Andriod开发一个APP似乎成本有点过高了，而H5的低成本、高效率、跨平台等特性马上被利用起来形成了一种新的开发模式：Hybrid APP。Hybrid App（混合模式移动应用）是指介于web-app、native-app这两者之间的app，兼具“Native App良好用户交互体验的优势”和“Web App跨平台开发的优势”。
  它虽然看上去是一个Native App，但只有一个UI WebView，里面访问的是一个Web App，所以可以简单的将Hybrid App看作包了个客户端的壳的网页。
## 一个例子
  最初携程的应用全部是Native的，H5站点只占其流量很小的一部分，当时Native有200人红红火火，而H5开仅有5人左右在打酱油。后面无线团队来了一个执行力十分强的服务器端出身的leader，他为了了解前端开发，使用jQuery Mobile开发了第一版程序，虽然很快方案便被推翻，但是H5团队开始发力，在短时间内已经赶上了Native的业务进度：
![avatar](https://images2015.cnblogs.com/blog/294743/201510/294743-20151029205836497-237939989.png)![avatar](https://images2015.cnblogs.com/blog/294743/201510/294743-20151029205853357-699032575.jpg)

后来有一天andriod Native的人表示andriod中有一个方法最大树限制，可能一些页面需要内嵌H5的页面，于是Native与H5框架团队牵头做了第一个Hybrid项目，携程第一次出现了一套代码兼容三端的情况。这个开发效率很高，整个团队尝到了甜头，于是乎后续的频道基本都开始了Hybrid开发。
  这个例子很好的说明了Hybrid开发效率高、跨平台、低成本的特点。
# hybrid的优劣
| item | Web App | Hybrid App | Native App |
| - | :-: | :-: | :-: |
| 开发成本 | 低| 中 | 高 |
| 维护更新 | 简单 | 简单 | 复杂 |
| 体验 | 差 | 中 | 优 |
| Store或market认可 | 不认可 | 认可 | 认可 | 
| 安装 | 不需要 | 需要 | 需要 |
| 跨平台 | 优 | 差 | 差 |
--------------------- 
# hybrid的设计与实现
## Url Schema
  H5与Native交互的桥梁为Webview，而“联系”的方式可由url schema的方式实现的，在用户安装app后，app可以自定义url schema，并且把自定义的url注册在调度中心， 例如
  
ctrip://wireless 打开携程App
weixin:// 打开微信

事实上Native能捕捉webview发出的一切请求，所以就算这里不是这种协议，Native也能捕捉，这个协议的意义在于可以在浏览器中直接打开APP。

交互模型图

![avatar](https://images2015.cnblogs.com/blog/294743/201605/294743-20160525231137303-2013494324.png)

我们在H5获取Native方法时一般是会构造一个这样的请求，使用iframe发出（设置location会有多次请求覆盖的问题）：
~~~ javascript
requestHybrid({
  //创建一个新的webview对话框窗口
  tagname: 'hybridapi',
  //请求参数，会被Native使用
  param: {},
  //Native处理成功后回调前端的方法
  callback: function (data) {
  }
});
//=====>
hybridschema://hybridapi?callback=hybrid_1446276509894&param=%7B%22data1%22%3A1%2C%22data2%22%3A2%7D
~~~

多数情况下这种方式没有问题，但是我们在后续的开发中，为了统一鉴权，将所有的请求全部代理到了Native发出，比如这样：

~~~ javascript
requestHybrid({
    tagname: 'post',
    param: {
        url: 'http://api.kuai.baidu.com/city/getstartcitys',
        param1: 'param1',
        param2: 'param2'
    },
    callback: function(data) {
    }
});
~~~
## JavaScriptCore
在ios7后，Apple新增了一个JavaScriptCore让Native可以与H5更好的交互（Android早就有了），我们这里主要讨论js如何与Native通信，这里举一个简单的例子：
① 首先定义一个js方法，这里注意其中调用了一个没有声明的方法：
~~~ javascript
function printHello() {
    //未声明方法
    print("Hello, World!");
}
~~~
然后，上述未声明方法事实上是Native注入给window对象的：
~~~ javascript
NSString *scriptPath = [[NSBundle mainBundle] pathForResource:@"hello" ofType:@"js"];
NSString *scriptString = [NSString stringWithContentsOfFile:scriptPath encoding:NSUTF8StringEncoding error:nil];

JSContext *context = [[JSContext alloc] init];
[context evaluateScript:scriptString];

self.context[@"print"] = ^(NSString *text) {
    NSLog(@"%@", text");
};

JSValue *function = self.context[@"printHello"];
~~~

这个样子，JavaScript就可以调用Native的方法了，这里Native需要注意方法注入的时机，一般是一旦载入页面便需要载入变量，这里的交互模型是：

![avatar](https://images2015.cnblogs.com/blog/294743/201605/294743-20160526004714709-342981925.png)

于是，我们这里只需要将原来底层的通信一块改下即可（Android本身就支持类似的实现，这里暂时不予关注）：

~~~ javascript
//使用jsCore与native通信
window.requestNative && requestNative(JSON.stringify(params));
return;
//兼容ios6
var ifr = $('<iframe style="display: none;" src="' + url + '"/>');
$('body').append(ifr);
setTimeout(function () {
    ifr.remove();
    ifr = null;
}, 1000)
~~~
