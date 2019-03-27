---
layout: post
title:  "vi编辑命令"
date:   2019-03-20 18:06:02 +0800
categories: CentOSbook
---



## Centos常用命令之：vi编辑命令

在vi中一共有三种模式，一般模式，编辑模式与命令模。

- 一般模式
当我们用vi打开一个文档的时候，就是一般模式了，这个时候，我们可以删除字符，删除行，也可以用上下左右来操作光标。也可以复制粘贴。

- 编辑模式  
当在一般模式下按下[i, I, o, O, a, A, r, R]的时候，就进入编辑模式了。这个时候屏幕的最下方会出现[-- 插入 --][-- 替换 --]（语言根据系统的设置可能会不一样）的提示信息。这个时候就可以编辑文本的内容了。按下[Esc]键即可以退出编辑模式。

- 命令模式  
在一般模式下按下[:/?]三个字符中的任意一个，光标就会移动到最后一行，这就进入了命令模式。可以进行一些保存，退出，搜索，替换等等功能。



## 一般模式

[h]或[←]：光标向左移动一个字符

[j]或[↓]：光标向下移动一个字符

[k]或[↑]：光标向上移动一个字符

[l]或[→]：光标向右移动一个字符

但是一个一个的移动貌似有点效率非常的低，这时候我们可以使用[数字加命令]的方式来移动。

比如：30j：向下移动30行   10→：向右移动10个字符

[Ctrl] + [f] ：屏幕向下移动一页，相当于[Page Down]

[Ctrl] + [b]：屏幕上移动一页，相当于[Page UP]

[Ctrl] + [d]：屏幕向下移动半页

[Ctrl] + [u]：屏幕向上移动半页

[+]：光标移动到非空格符的下一列

[-]：光标移动到非空格符的上一列

n<space>：n代表数字，按下数字后，在按空格，光标会向右移动到这一行的n个字符。

0或[home]：这个是数字0，表示移动到这一行的最前面字符处。

$或[End]：移动到这一行的最后面字符处。

[H]：光标移动到这个屏幕的最上方那一行的第一个字符。

[M]：光标移动到这个屏幕的中央那一行的第一个字符

[L]：光标移动到这个屏幕的最下方那一行的第一个字符。

[G]：移动光标到这个文件的最后一行。

[nG]：你表示数字，移动到这个文档的第n行

[gg]：移动到这个当好的第一行，相当于[1G]

[n<Enter>]：n表示数字，光标向下移动n行

 

## 检索与替换命令

[/word]：向光标之下寻找一个名称为world的字符串

[?word]：向光标智商寻找一个名称为word的字符串

[n]：这个n是英文按键，代表重复前一个搜寻的动作，假如说你用[/name]来搜寻name之后，可以按[n]来继续查找下一个

[N]：这个和[n]相反，是用来查找上一个

[:n1,n2s/word1/word2/g]：n1和n2为两个数字，表示在n1和n2行之间寻找word1这个字符串，并将该字符串替换为word2

[:1,$s/word1/word2/g]：从第一行到最后一行寻找word1字符串，并替换为word2

[:1,$s/word1/word2/gc]：从第一行到最后一行寻找word1字符串，并替换为word2，且在替换之前显示提示字符，让用户来确认是否要替换。

 

## 删除复制命令

[x,X]：在一行当中，[x]表示向后删除一个字符，相当于[del],[X]表示向前删除一个字符，相当于[backspace]

[nx]：n为数字，表示向后连续删除n个字符

[nX]：n为数字，表示向前连续删除n个字符

[dd]：删除游标所在的那一整行。

[ndd]：n为数字，删除光标所在的行向下n行

[d1G]：删除光标所在的行到第一行的所有数据(1G表示第一行)

[dG]：删除光标所在行到最后一行的所有数据

[d$]：删除光标所在处到改行的最后一个字符

[d0]：这个是数字0，删除光标所在处到该行的最前面一个字符

[yy]：复制游标所在的那一行

[nyy]：n为数字。复制光标所在行向下n行

[y1G]：复制游标所在行到第一行的所有数据

[yG]：复制游标所在行到最后一行的所有数据

[y0]：复制光标所在字符到行首的所有数据

[y$]：复制光标所在字符到行尾的所有数据

[p,P]：p为将已复制的数据在光标下一行贴上，P为贴在光标的上一行，

[J]：将光标所在行与下一行的数据结合成一行

[c]：重复删除多个数据，例如乡下删除10行[10cj]

[u]：复原前一个动作

[Ctrl + r]：重复上一个动作

[.]：这是个小数点，表示重复前一个动作　　

 

## 编辑模式

在一般模式下按下面这些键就可以进入编辑模式。

[i,I]：进入插入模式，[i]为从光标所在处插入。[I]为在目前所在行的第一个非空格处开始插入

[a,A]：进入插入模式，[a]为从光标所在的下一个字符处开始插入,[A]为从光标所在行的最后一个字符处开始插入。

[o,O]：进入插入模式，[o]表示在目前光标所在的下一行处插入新的一行，[O]表示在目前光标所在处的上一行插入新的一行

[r,R]：进入替换模式，[r]表示取代光标所在的那一个字符一次，[R]会一直取代光标所在的文字，知道按下[Esc]为止。

[Esc]：退出编辑模式

 

## 命令模式

[:w]：保存，不退出

[:w!]：强制保存，即使权限是只读的时候，也写入

[:q]：退出vi

[:q!]：强制退出，比如有时我们修改了文档，又不想保存

[:wq]：保存并退出,[:wq!]表示强制保存并退出

[ZZ]：表似乎档案没有变动，则不储存离开，变动了，则存储离开

[:w filename]：将编辑的数据储存为另一个新的文档

[:r filename]：在编辑的数据中，读入另一个文档的数据，内容加到游标所在行的后面

[:n1,n2 w filename]：将n1到n2的内容储存到filename这个档案

[:! command]：暂时离开vi到指令列模式下执行command的显示结果。例如：[:! ls /home] 即可在vi中查看/home地下 ls的输出结果

[:set nu]：显示行号

[:set nonu]：取消显示行号 

## 例子

### 用vi打开一个文件
```
vi /root/anaconda-ks.cfg
```

```
按 i 键后  进入insert模式，进入insert模式后才能进行修改
修改完成后
按esc键进入command模式，
然后:wq 保存文件并退出vi（注意先冒号）
 ```

### 保存命令
按ESC键 跳到命令模式，然后：
 ```
　　:w 保存文件但不退出vi
　　:w file 将修改另外保存到file中，不退出vi
　　:w! 强制保存，不推出vi
　　:wq 保存文件并退出vi
　　:wq! 强制保存文件，并退出vi
　　q: 不保存文件，退出vi
　　:q! 不保存文件，强制退出vi
　　:e! 放弃所有修改，从上次保存文件开始再编辑
 ```

## 修订  
- 2019-03-20 18:07:00