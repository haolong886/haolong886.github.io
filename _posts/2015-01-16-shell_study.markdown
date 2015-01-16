---
title: shell小问题
layout: post
tags:
  - work
  - shell
---
在工作中遇到了两个问题，一个是关于expect，还有一个是read的问题。这两个问题都是在做一个关于密码复杂度需求过程中发现的，现在分别记下，以供日后参考。

### I. expect问题

expect中有send命令，我们通过manual expect可以了解到，其实expect可以看作是类似shell的执行环境，里面带有自己的命令，send就是其中一个很常用的命令。需求中要求密码是支持键盘中所有字符的，并且可以以任意字符开头。密码需要用expect来验证，测试的过程中就发现一个以中杠“-”开头的密码会造成expect报错，错误如下：

> expect1.1> send "-xxx"  
bad flag "-xxx": must be -i, -h, -s, -null, -0, -raw, -break, or --  
while executing  
"send "-xxx""  
expect1.2>

可以看到，send把带中杠的字符串当作是传递参数，但其又无“xxx”的参数，最终报错。
通过查看expect的手册可以看到下图所示的表述：

[![sendmanual](/media/files/20150116shell/sendmanual.png)](/media/files/20150116shell/sendmanual.png)

这段话很明白的指出了send默认的行为是会把以中杠“-”开头的字符串当作flag进行处理。而同时提供了“--”参数来强制使之后的参数都作为普通字符串处理。
由此，当我们在用expect的send密码等可能以中杠“-”开头的字符串时，最好都带上“--”参数，以避免传入带中杠的字符串而使脚本异常退出。

### II. read问题

read有个默认行为是当用户输入的字符串以“\”结尾时，会认为是用户重启一行输入而继续读入用户的输入。这带来的一个问题是当输入的密码以“\”结尾时，read会出现阻塞的现象，而实际上它在还在等待用户输入，直到遇到以换行符结尾。要关掉这种默认行为可以加“-r”参数，打开这个开关时，read会把结尾的“\”当作普通字符读入，这样就不会出现脚本卡死的现象。
