---
layout: post
title: zimbra修改
date: 2012-02-12 13:17
author: abanger
comments: true
categories: zimbra
---
1.zimbra不紧紧是一个web应用，是一个直接可运行的系统。在14机器结构如下：

/opt/zimbra:

bin/启动zimbra的文件

jetty:zimbra的Web服务器

java:zimbra的Java环境

memcache:缓存

httpd:apache httpd服务器

mysql:数据库

等。

启动zimbra命令：

/opt/zimbra/bin/zmcontrol stop

/opt/zimbra/bin/zmcontrol start

邮件系统的启动：

/opt/zimbra/bin/zmmailboxdctl stop

/opt/zimbra/bin/zmmailboxdctl start

/opt/zimbra/bin/zmmailboxdctl restart

2.zimbra修改内容

2.1 jsp文件：目录为/opt/zimbra/jetty/webapps/zimbra/public/

如修改login.jsp,将图片和商标的html去掉。

其他jsp文件暂时还没细读。

2.2 修改国际化中文编码

编码文件路径：zimbra/WEB-INF/classes/messages/*_zh_CN.properties

操作,先将unicode编码的文件转为中文，然后修改后再进行编码
native2ascii -reverse ZaMsg_zh_CN.properties a.txt
修改完a.txt后：
native2ascii a.txt ZaMsg_zh_CN.properties

<!--more-->

2.3 linux下修改全部文件内容（此文件会替换全部文件夹下的文字。不能在类文件夹上使用，会把class文件的内容也替换掉。导致类文件检验出错）<a>：</a>

将<a href="http://www.zimbra.com/">http://www.zimbra.com </a>替换为<a href="http://www.logerp.com/">http://www.logerp.com </a>：
find -type f | xargs perl -i -pe s%http://www.zimbra.com%http://www.logerp.com%g

2.4 skin的修改：

对应skin文件夹下，图标在skin/yahoo/logos,(其中yahoo为你选择的页面样式)，每个样式下都有logos文件夹，可以进行替换图片。

3.源码

源码需要到网上下载，但是文件比较多，还没有进行编译和调试。

对代码阅读了一下，

3.1 skin对应请求为SkinResource.java(servlet),接受css/*,html/*的请求

然后读取后台skin文件夹的配置文件:skin/yahoo/base:

mainfest.xml,skin.css,skin.html,skin.properties

在请求资源时，根据配置文件对内容进行替换。

3.2 properties2javascript

国际化通过Props2JsServlet.java实现。

4.由于把/webapps/zimbra修改为/webapps/logerp后，需要改动很多文件，故出现了一些问题。导致应用无法启动。
经过排查。是在linux下执行替换文件时，把class文件,jar包里面的文字也改掉了，导致无法无法启动。
通过替换备份的文件。现在已经启动成功。
如果需要修改，请先备份，再做修改。

5.请求处理流程

a.进入/zimbra/进入SetHeadFilter,并转发到/service/user或/service/home请求

b./service/home对应UserServlet

c.UserServlet处理完毕后,调用不同的formater,对应网站就是HtmlFormater,跳转/zimbra/h/rest路径下

d./zimbra/h/rest不是jsp页面,内容是大量jstl,包括自定义jstl,这块代码难以阅读。

e.jstl处理完后返回html,css,js,其中js,css会通过SkinFilter进行替换,支持不同样式与中文

6.zimbra 前端视图

zimbra页面大量使用jstl生成页面：

1.jsp页面：部分页面使用jsp页面与标签库,在public/*.jsp,如login.jsp页面

2.jstl,自定义标签库。如h/search,search使用app标签,app:view,app:convListView,app:overView

3.js……

今天着重对zimbra的首页进行研究,普通版首页路径:/zimbra/h/search,结合图片说明：

<img title="点击查看原始大小图片" alt="" width="700" height="488" />

a./h/search代码：

&lt; c:when test=”${context.isConversationSearch}”&gt;
&lt; app:convListView context=”${context}”/ &gt;
&lt; / c:when &gt;

以上使用app:convListView显示页面

b.app:convListView对应tag为/WEB-INF/tag/conv/convListView.tag

代码：&lt; app:view mailbox=”${mailbox}”

通过app:view显示

c.app:view为/WEB-INF/tag/infra/view.tag

view.tag代码使用

app:overView为左侧

d.中间内容为：&lt; app:view mailbox=”${mailbox}”&gt;&lt; / app:view &gt;内部标签

7.admin用户登陆：<a href="https://localhost:7071/zimbraAdmin/">https://localhost:7071/zimbraAdmin/</a>

zimbra创建新用户流程：
.service/admin/soap/CreateAccountRequest
//创建账户

证书导入：
1.keytool -import -file myca.cert -keystore %JAVA_HOME%\jre\lib\security\cacerts -file myca.cert -alias tomcat
2.此时命令行会提示你输入cacerts证书库的密码，你敲入changeit就行了，这是java中cacerts证书库的默认密码，

8.获得未读邮件数代码
<div>
<div>
<div>Java代码 <object width="14" height="15" classid="clsid:d27cdb6e-ae6d-11cf-96b8-444553540000" codebase="http://download.macromedia.com/pub/shockwave/cabs/flash/swflash.cab#version=6,0,40,0"><param name="src" value="http://jiangyongyuan.javaeye.com/javascripts/syntaxhighlighter/clipboard_new.swf" /><embed width="14" height="15" type="application/x-shockwave-flash" src="http://jiangyongyuan.javaeye.com/javascripts/syntaxhighlighter/clipboard_new.swf" /></object></div>
</div>
<ol>
	<li>public int getInbox(String name, String password) throws Exception {</li>
	<li>  // 构造请求参数</li>
	<li>  HashMap&lt;String, String&gt; map = new HashMap&lt;String, String&gt;();</li>
	<li>  map.put(“client”, ”adsf”);// 此处不能设置mobile,原因未明</li>
	<li>  map.put(“loginOp”, ”login”);</li>
	<li>  map.put(“password”, password);</li>
	<li>  map.put(“username”, name);</li>
	<li></li>
	<li>  StringBuffer bf = new StringBuffer();</li>
	<li>  for (Map.Entry&lt;String, String&gt; entry : map.entrySet()) {</li>
	<li>   bf.append(“&amp;” + entry.getKey() + ”=”</li>
	<li>     + URLEncoder.encode(entry.getValue(), ”UTF-8″));</li>
	<li>  }</li>
	<li>  String query = bf.toString().substring(1);</li>
	<li></li>
	<li>  // 创建url连接</li>
	<li>  URL url = new URL(“http://172.17.1.14/zimbra/”);</li>
	<li>  HttpURLConnection connection = (HttpURLConnection) url.openConnection();</li>
	<li>  connection.setDoInput(true);</li>
	<li>  connection.setDoOutput(true);</li>
	<li>  connection.setRequestProperty(“Host”, ”172.17.1.14″);</li>
	<li>  connection</li>
	<li>    .setRequestProperty(</li>
	<li>      ”User-Agent”,</li>
	<li>      ”Mozilla/5.0 (Windows; U; Windows NT 5.1; zh-CN; rv:1.9.1.6) Gecko/20091201 Firefox/3.5.6 GTBDFff GTB7.0″);</li>
	<li>  connection</li>
	<li>    .setRequestProperty(“Accept”,</li>
	<li>      ”text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8″);</li>
	<li>  connection.setRequestProperty(“Accept-Language”, ”zh-cn,zh;q=0.5″);</li>
	<li>  connection.setRequestProperty(“Accept-Encoding”, ”deflate”);</li>
	<li>  connection.setRequestProperty(“Accept-Charset”,</li>
	<li>    ”GB2312,utf-8;q=0.7,*;q=0.7″);</li>
	<li>  connection.setRequestProperty(“Keep-Alive”, ”300″);</li>
	<li>  connection.setRequestProperty(“Connection”, ”keep-alive”);</li>
	<li>  connection.setRequestProperty(“Referer”,</li>
	<li>    ”http://172.17.1.14/zimbra/?loginOp=logout&amp;client=mobile”);</li>
	<li>  connection.setRequestProperty(“Cookie”, ”ZM_TEST=true”);</li>
	<li>  connection.setRequestMethod(“POST”);</li>
	<li></li>
	<li>  // 输出数据</li>
	<li>  DataOutputStream output = new DataOutputStream(connection</li>
	<li>    .getOutputStream());</li>
	<li>  output.write(query.getBytes(“UTF-8″));</li>
	<li>  output.flush();</li>
	<li>  output.close();</li>
	<li></li>
	<li>  // 读取信息</li>
	<li>  InputStream in = connection.getInputStream();</li>
	<li>  BufferedReader bufferedReader = new BufferedReader(</li>
	<li>    new InputStreamReader(in));</li>
	<li>  StringBuffer temp = new StringBuffer();</li>
	<li>  String line = bufferedReader.readLine();</li>
	<li></li>
	<li>  while (line != null) {</li>
	<li>   temp.append(line).append(“\r\n”);</li>
	<li>   line = bufferedReader.readLine();</li>
	<li>  }</li>
	<li>  bufferedReader.close();</li>
	<li></li>
	<li>  String ecod = connection.getContentEncoding();</li>
	<li>  if (ecod == null)</li>
	<li>   ecod = ”UTF-8″;</li>
	<li></li>
	<li>  // 设置cookie</li>
	<li>  String cookie = ”";</li>
	<li>  Map&lt;String, List&lt;String&gt;&gt; headerFields = connection.getHeaderFields();</li>
	<li>  for (Map.Entry&lt;String, List&lt;String&gt;&gt; entry : headerFields.entrySet()) {</li>
	<li>   if (“Set-Cookie”.equals(entry.getKey())) {</li>
	<li>    for (String s : entry.getValue()) {</li>
	<li>     cookie = s.substring(0, s.indexOf(“;”));</li>
	<li>     break;</li>
	<li>    }</li>
	<li>   }</li>
	<li>  }</li>
	<li></li>
	<li>  // 请求mobile页面</li>
	<li>  URL url2 = new URL(“http://172.17.1.14/zimbra/m/mofolders”);</li>
	<li>  HttpURLConnection connection2 = (HttpURLConnection) url2</li>
	<li>    .openConnection();</li>
	<li>  connection2.setDoInput(true);</li>
	<li>  connection2.setDoOutput(true);</li>
	<li>  connection2.setRequestProperty(“Cookie”, ”ZM_TEST=true;” + cookie);</li>
	<li>  connection2.setRequestMethod(“GET”);</li>
	<li>  // read in each character until end-of-stream is detected</li>
	<li>  InputStream in2 = connection2.getInputStream();</li>
	<li>  BufferedReader bufferedReader2 = new BufferedReader(</li>
	<li>    new InputStreamReader(in2));</li>
	<li>  StringBuffer temp2 = new StringBuffer();</li>
	<li>  String line2 = bufferedReader2.readLine();</li>
	<li></li>
	<li>  String info = ”";</li>
	<li>  while (line2 != null) {</li>
	<li>   if (!”".equals(line2.trim())) {</li>
	<li>    line2 = new String(line2.trim().getBytes(), ecod);</li>
	<li>    // 截取收件箱行</li>
	<li>    if (line2.trim().startsWith(“收件”))</li>
	<li>     info = line2;</li>
	<li>    temp2.append(line2).append(“\r\n”);</li>
	<li>   }</li>
	<li>   line2 = bufferedReader2.readLine();</li>
	<li>  }</li>
	<li>  bufferedReader2.close();</li>
	<li></li>
	<li>  // info = 收件箱(1)</li>
	<li>  // 其中1为收件箱未读数</li>
	<li>  if (info.indexOf(“(“) &gt; 0) {</li>
	<li>   String num = info.substring(info.indexOf(“(“) + 1, info</li>
	<li>     .indexOf(“)”));</li>
	<li>   return Integer.parseInt(num);</li>
	<li>  } else</li>
	<li>   return 0;</li>
	<li> }</li>
</ol>
</div>
