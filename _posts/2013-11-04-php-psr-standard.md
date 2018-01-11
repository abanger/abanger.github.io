---
layout: post
title: PHP的PSR-0命名标准
date: 2013-11-04 10:54
author: abanger
comments: true
categories: PHP
---
<a href="https://github.com/php-fig/fig-standards" target="_blank">PSR</a>是Proposing a Standards Recommendation（提出标准建议）的缩写，是由PHP Framework Interoperability Group（PHP通用性框架小组，简称<a href="https://github.com/php-fig" target="_blank">PHP-FIG</a>）发起的，通过他们命名就可以看出，这是个主要是针对框架通用性而做努力的开放性小组，他们的在Github上有自己的<a href="https://github.com/php-fig/fig-standards" target="_blank">仓库地址</a>，目前只有一个被接受的标准，那就是<a href="https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md" target="_blank">PSR-0标准</a>，标准定义了PHP自动加载的命名规范和文件路径规范。 针对PSR-0标准主要提到了以下几点：
<h3>要求</h3>
<ul>
	<li>一个完全合格的命名空间和类名必须有以下的结构“\&lt;提供者名称&gt;\(&lt;命名空间&gt;\)*&lt;类名&gt;”</li>
	<li>每个命名空间必须有顶级的命名空间（“提供者”）</li>
	<li>每个命名空间可以有任意多个子命名空间</li>
	<li>每个命名空间在被从文件系统加载时必须被转换为“操作系统路径分隔符”（DIRECTORY_SEPARATOR ）</li>
	<li>每个“_”字符在“类名”中被转换为DIRECTORY_SEPARATOR 。“_”符号在命名空间中没有这个含义</li>
	<li>符合命名标准的命名空间和类名必须以“.php”结尾来加载文件</li>
	<li>提供商名称，命名空间，类名可以由大小写字母组成，其中命名空间和类名是大小写敏感的以保证多系统兼容性</li>
	<li>如果文件不存在需要返回false</li>
</ul>
&nbsp;

来源：<a href="http://feiyang.me/2012/05/php-psr-0-a-naming-standard/">http://feiyang.me/2012/05/php-psr-0-a-naming-standard/</a>

&nbsp;
