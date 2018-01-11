---
layout: post
title: PHP生成静态html文件的方法
date: 2012-02-14 10:21
author: abanger
comments: true
categories: PHP
---
<div>
<div id="_mcePaste">1,下面使用模版的一个方法!</div>
<div id="_mcePaste">&lt;?php</div>
<div id="_mcePaste">$fp = fopen (“templets.html”,”a”);</div>
<div id="_mcePaste">if ($fp){</div>
<div id="_mcePaste">$fup = fread ($fp,filesize(“templets.html”));</div>
<div id="_mcePaste">$fp2 = fopen (“html.shtml”,”w”);</div>
<div id="_mcePaste">if ($fwrite ($fp2,$fup)){</div>
<div id="_mcePaste">$fclose ($fp);</div>
<div id="_mcePaste">$fcolse ($fp2);</div>
<div id="_mcePaste">die (“写入模板成功”);</div>
<div id="_mcePaste">} else {</div>
<div id="_mcePaste">fclose ($fp);</div>
<div id="_mcePaste">die (“写入模板失败!”);</div>
<div id="_mcePaste">}</div>
<div id="_mcePaste">}</div>
<div id="_mcePaste">?&gt;</div>
<div id="_mcePaste">简单的将模板写进一个文件中存为html.html</div>
<div><!--more--></div>
<div id="_mcePaste">2,按时间生成html文件名</div>
<div id="_mcePaste">&lt;?</div>
<div id="_mcePaste">$content = “这是一个以日期时间为文件名的静态生成网页的测试文件，</div>
<div id="_mcePaste">文件名格式一般为&lt;font color=#ff0000&gt;年月日时分秒.html&lt;/font&gt;”;</div>
<div id="_mcePaste">$date = date(‘YmdHis’);</div>
<div id="_mcePaste">$fp = fopen (date(‘YmdHis’) . ‘.html’,”w”);</div>
<div id="_mcePaste">//本函数可用来打开本地或者远端的文件 ‘w’ 开文件方式为写入，</div>
<div id="_mcePaste">文件指针指到开始处，并将原文件的长度设为 0。若文件不存在，</div>
<div id="_mcePaste">则建立新文件。</div>
<div id="_mcePaste">if (fwrite ($fp,$content)){</div>
<div id="_mcePaste">//格式是.int fwrite(int fp(文件名), string string(内容),</div>
<div id="_mcePaste">int [length](长度));本函数将字符串 string 写入文件资料流的指针 fp 上。</div>
<div id="_mcePaste">若有指定长度 length，则会写入指定长度字符串，或是写到字符串结束。</div>
<div id="_mcePaste">fclose ($fp);//函数用来关闭已经打开的文件的指针 fp。</div>
<div id="_mcePaste">成功返回 true，失败则返回 false。</div>
<div id="_mcePaste">die (“写入模板成功”);</div>
<div id="_mcePaste">} else {</div>
<div id="_mcePaste">fclose ($fp);</div>
<div id="_mcePaste">die (“写入模板失败!”);</div>
<div id="_mcePaste">}</div>
<div id="_mcePaste">echo ($content);</div>
<div id="_mcePaste">?&gt;</div>
<div id="_mcePaste">3,下面为转换文件名的一个方法</div>
<div id="_mcePaste">&lt;?php</div>
<div id="_mcePaste">$s_fname = “93e.php”;</div>
<div id="_mcePaste">$o_fname = “93e.htm”;</div>
<div id="_mcePaste">ob_end_clean();</div>
<div id="_mcePaste">ob_start();</div>
<div id="_mcePaste">include($s_fname);</div>
<div id="_mcePaste">$length = ob_get_length();</div>
<div id="_mcePaste">$buffer = ob_get_contents();</div>
<div id="_mcePaste">$buffer = eregi_replace(“r”,”",$buffer);</div>
<div id="_mcePaste">ob_end_clean();</div>
<div id="_mcePaste">$fp = fopen($o_fname,”w+”);</div>
<div id="_mcePaste">fwrite($fp,$buffer);</div>
<div id="_mcePaste">fclose($fp);</div>
<div id="_mcePaste">?&gt;</div>
<div id="_mcePaste">这样就可以把 93e.php转化为静态的HTML文件了 。要注意的是待转换的文件里不能有 ob_end_clean();和 ob_start();语句，且目录要有写权限。</div>
1,下面使用模版的一个方法!

&lt;?php $fp = fopen (“templets.html”,”a”); if ($fp){ $fup = fread ($fp,filesize(“templets.html”)); $fp2 = fopen (“html.shtml”,”w”); if ($fwrite ($fp2,$fup)){ $fclose ($fp); $fcolse ($fp2); die (“写入模板成功”); } else { fclose ($fp); die (“写入模板失败!”); } } ?&gt; 简单的将模板写进一个文件中存为html.html
2,按时间生成html文件名

&lt;? $content = “这是一个以日期时间为文件名的静态生成网页的测试文件，文件名格式一般为&lt;font color=#ff0000&gt;年月日时分秒.html&lt;/font&gt;”; $date = date(‘YmdHis’); $fp = fopen (date(‘YmdHis’) . ‘.html’,”w”);//本函数可用来打开本地或者远端的文件 ‘w’ 开文件方式为写入，文件指针指到开始处，并将原文件的长度设为 0。若文件不存在，则建立新文件。 if (fwrite ($fp,$content)){//格式是.int fwrite(int fp(文件名), string string(内容), int [length](长度));本函数将字符串 string 写入文件资料流的指针 fp 上。若有指定长度 length，则会写入指定长度字符串，或是写到字符串结束。 fclose ($fp);//函数用来关闭已经打开的文件的指针 fp。成功返回 true，失败则返回 false。 die (“写入模板成功”); } else { fclose ($fp); die (“写入模板失败!”); } echo ($content); ?&gt;
3,下面为转换文件名的一个方法

&lt;?php $s_fname = “93e.php”; $o_fname = “93e.htm”; ob_end_clean(); ob_start(); include($s_fname); $length = ob_get_length(); $buffer = ob_get_contents(); $buffer = eregi_replace(“r”,”",$buffer); ob_end_clean();
$fp = fopen($o_fname,”w+”); fwrite($fp,$buffer); fclose($fp); ?&gt;
这样就可以把 93e.php转化为静态的HTML文件了 。要注意的是待转换的文件里不能有 ob_end_clean();和 ob_start();语句，且目录要有写权限。

</div>
