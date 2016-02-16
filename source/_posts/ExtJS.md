title: extjs/java web/...
date: 2015-04-15 22:55:17
tags: [extjs, HTML]
categories: 毕设 

---
2016.2.16 补充说明:
    做毕设涉及到的一些零碎知识点，可能一些链接失效了。搬运过来的，毕竟是成长的一部分。
<!--more-->
## 2015/04/15
[HTML中的ID和NAME的区别]( http://www.cnblogs.com/cs_net/articles/1958320.html)
[ExtJs4常用函数]( http://extjs.org.cn/node/745 )
[Extjs MVC开发模式详解](http://extjs.org.cn/node/742)
[ExtJs4 笔记（12） Ext.toolbar.Toolbar 工具栏、Ext.toolbar.Paging 分页栏、Ext.ux.statusbar.StatusBar 状态栏](http://www.cnblogs.com/lipan/archive/2011/12/23/2298746.html)

Taglib 指令: 定义一个 标签库 以及其 自定义标签 的前缀.
JSP(Java Server Pages) 语法
```
<%@ taglib uri="URIToTagLibrary" prefix="tagPrefix" %>
```
<% @ taglib %> 指令声明此JSP文件使用了自定义的标签，同时引用 标签库，也指定了他们的标签的前缀。
这里自定义的标签含有标签和元素之分。因为JSP文件能够转化为XML,所以了解标签和元素之间的联系很重要。标签只不过是一个在意义上被抬高了点的标记，是JSP元素的一部分。JSP元素是JSP语法的一部分，和XML一样有开始标记和结束标记。元素也可以包含其它的文本，标记，元素。比如，一个jsp:plugin元素有<jsp:plugin>开始标记和</jsp:plugin>结束标记，同样也可以有<jsp:params>和<jsp:fallback>元素.

Page为 计算机编程语言 脚本 ，定义JSP文件中的全局属性。
属性:
**language="java"**  //声明脚本语言的种类，暂时只能用"java"
**extends="package.class"**  //标明JSP编译时需要加入的Java Class的全名，但是得慎重的使用它，它会限制JSP的编译能力.
**import="{package.class | package.* },..."**  //需要导入的Java包的列表，这些包就作用于程序段，表达式，以及声明。
   下面的包在JSP编译时已经导入了，所以你就不需要再指明了:
   java.lang.*
  javax.servlet.*
  javax.servlet.jsp.*
   javax.servlet.http.*
**session="true | false"**
设定客户是否需要HTTP Session.（学过ASP的人，应该对它不陌生）如果它为true，那么Session是有用的。
如果它有false，那么你就不能使用session对象，以及定义了scope=session的<jsp:useBean>； 元素。这样的使用会导致错误.
缺省值是true.
**buffer="none | 8kb | sizekb"** buffer的大小被out对象用于处理执行后的JSP对客户 浏览器的输出。 缺省值是8kb
**autoFlush="true | false"**  设置如果buffer溢出，是否需要 强制输出，如果其值被定义为true（ 缺省值），输出正常，如果它被设置为false，如果这个buffer溢出，就会导致一个意外错误的发生.如果你把buffer设置为none，那么你就不能把autoFlush设置为false.
**isThreadSafe="true | false"** 设置Jsp文件是否能 多线程使用。缺省值是true，也就是说，JSP能够同时处理多个用户的请求，如果设置为false，一个jsp只能一次处理一个请求
**info="text"**  一个文本在执行JSP将会被逐字加入JSP中，你能够使用Servlet.getServletInfo方法取回。
**errorPage="relativeURL"** 设置处理异常事件的JSP文件。
**isErrorPage="true | false"** 设置此页是否为出错页，如果被设置为true，你就能使用exception对象.
**contentType="mimeType [ ;charset=characterSet ]" | "text/html;charset=ISO-8859-1"**  设置MIME类型。缺省MIME 类型是： text/html，缺省 字符集为 ISO-8859-1.


## 2015/04/16
[实例管理器EntityManager——执行数据库更新](http://www1.huachu.com.cn/read/readbookinfo.asp?sectionid=1000000622)
[DTO](http://blog.csdn.net/dchjmichael/article/details/7905766)

## 2015/04/18
[Ext.ComponentQuery.query()](http://blog.csdn.net/jiushuai/article/details/7938476)
[小谈chrome调试命令：console.log的使用](http://sentsin.com/web/11.html)
[Jquery超简单遮罩层实现代码](http://blog.csdn.net/tolcf/article/details/38712343)


**var之JavaScript**
var a=10; //正确
a=10; //正确
   在javascript中，以上两种方法都是定义变量的正确方法。微软的Script56.CHM中是这样解释的：
   尽管并不安全，但声明语句中忽略var关键字是合法的 JScript 语法。这时，JScript 解释器给予变量全局范围的可见度。当在过程级中声明一个变量时，它不能用于全局范围；这种情况下，变量声明必须用var关键字。
   从上面的描述看来，对待这两种定义方法要区分以下两种情况：
   ⒈在一个过程级中（即位于function的定义范围内，无论是函数，还是类）的任何地方，包括在一个区块里（for,while,if……），定义变量时，使用var定义，则此变量只在这个过程级内起作用，反之为全局变量。
   ⒉在过程级外定义变量时，无论是否忽略var，都将定义一个全局变量。
   从这点看来，JS和其他语言有不一样的地方，变量的范围不以“{}”作为边界，而是以"function(){}"为边界，而且在过程内可以很轻松的定义全局变量。如果不注意这个问题的话，是很容易产生不可预知的错误的。
   对于使用var，我的建议是要养成好的使用习惯：
   ⒈在程序的开头，统一定义全局变量；
   ⒉所有的变量在定义时都要加上var；
   ⒊尽量不要在不同的过程中使用相同的变量名。
   
   
## 2015/04/19
[JPA的查询语言—JPQL的命名查询@NamedQuery](http://irisandkevin.iteye.com/blog/1567939)
[EntityManager方法简介  ](http://blog.163.com/zhouhuoxiang12@126/blog/static/88776461200945112658420/)
[Jersey 入门与Javabean](http://www.cnblogs.com/xinsheng/p/3897289.html)
[extjs textField disabled readOnly属性](http://blog.csdn.net/serenada/article/details/6177878)

## 2015/4/22
[ ExtJS 4 官方指南翻译：Component 组件](http://blog.csdn.net/zhangxin09/article/details/6914882):关于渲染问题
[ExtJS学习之路第二步：Ext.Component 和 Ext.dom.Element 的区别 - 易小亨](http://www.tuicool.com/articles/zqaeEzE): Component中可以通过el属性来访问该Component所依赖的Element

## 2015/04/27
[UML类图几种关系的总结](http://www.open-open.com/lib/view/open1328059700311.html)

## 2015/05/02
[UML用例图设计中用例之间关系](http://blog.csdn.net/piaoxue820/article/details/7282526)
[UML建模之用例图关系](http://www.cnblogs.com/lyp3314/archive/2011/11/16/2251906.html)

## 2015/05/04
[Ext表单提示方式：msgTarget](http://www.cnblogs.com/mingforyou/archive/2013/08/26/3282169.html)

## 2015/06/10
[百度消息推送SDK探究](http://www.tuicool.com/articles/2YbYj2)
