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

| 特性 | Web App（网页应用） | Hybrid App（混合应用） | Native App（原生应用）|
| - | :-: | -: | |
| Harry Potter | Gryffindor| 90 | |
| Hermione Granger | Gryffindor | 100 | |
| Draco Malfoy | Slytherin | 90 ||
--------------------- 
