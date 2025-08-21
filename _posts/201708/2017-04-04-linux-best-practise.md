---
title: linux best practice
date: 2017-04-04 08:32:31 Z
categories: linux
layout: post
---

Linux习惯 

* 在单个命令中创建目录树。 
* 更改路径,不要移动存档。 
* 将命令与控制操作符组合使用。 
* 谨慎引用变量。 
* 使用转义序列来管理较长的输入。 
* 在列表中对命令分组。 
* 在 find 之外使用 xargs 。 
* 了解何时 grep 应该执行计数——何时应该绕过。 
* 匹配输出中的某些字段，而不只是对行进行匹配。 
* 停止对 cat 使用管道。 

### 在单个命令中创建目录树 

```             
~ $ mkdir -p tmp/a/b/c             
~ $ mkdir -p project/{lib/ext,bin,src,doc/{html,info,pdf},demo/stat/a}
```          
### 更改路径,不要移动存档 

```               
~ $ tar xvf -C tmp/a/b/c newarc.tar.gz
```
### 将命令与控制操作符组合使用

```                       
~ $ cd tmp/a/b/c && tar xvf ~/archive.tar              
~ $ cd tmp/a/b/c || mkdir -p tmp/a/b/c               
~ $ cd tmp/a/b/c || mkdir -p tmp/a/b/c && tar xvf -C tmp/a/b/c ~/archive.tar
```          
           
### 谨慎引用变量 
始终要谨慎使用 Shell 扩展和变量名称。一般最好将变量调用包括在双引号中，除非您有不这样做的足够理由。类似地，如果您直接在字母数字文本后面使用变量名称，则还要确保将该变量名称包括在方括号 ([]) 中，以使其与周围的文本区分开来。否则，Shell 将把尾随文本解释为变量名称的一部分——并且很可能返回一个空值。清单 8 提供了变量的各种引用和非引用及其影响的示例。 

```
~ $ ls tmp/
a b
~ $ VAR="tmp/*"
~ $ echo $VAR
tmp/a tmp/b
~ $ echo "$VAR"
tmp/*
~ $ echo $VARa

~ $ echo "$VARa"

~ $ echo "${VAR}a"
tmp/*a
~ $ echo ${VAR}a
tmp/a
~ $
```

### 将反斜杠用于长输入

```               
~ $ cd tmp/a/b/c || \
> mkdir -p tmp/a/b/c && \
> tar xvf -C tmp/a/b/c ~/archive.tar
            或者，也可以使用以下配置： 清单 10. 好习惯 5 的替代示例：将反斜杠用于长输入                 
~ $ cd tmp/a/b/c \
>                 || \
> mkdir -p tmp/a/b/c \
>                    && \
> tar xvf -C tmp/a/b/c ~/archive.tar
```

### 在列表中对命令分组 
在 Subshell 中运行命令列表

```            
~ $ ( cd tmp/a/b/c/ || mkdir -p tmp/a/b/c && \
> VAR=$PWD; cd ~; tar xvf -C $VAR archive.tar ) \
> | mailx admin -S "Archive contents"
```
在此示例中，该存档的内容将提取到 tmp/a/b/c/ 目录中，同时将分组命令的输出（包括所提取文件的列表）通过邮件发送到地址 admin。 当您在命令列表中重新定义环境变量，并且您不希望将那些定义应用于当前 Shell 时，使用 Subshell 更可取。 在当前 Shell 中运行命令列表 将命令列表用大括号 ({}) 括起来，以在当前 Shell 中运行。确保在括号与实际命令之间包括空格，否则 Shell 可能无法正确解释括号。此外，还要确保列表中的最后一个命令以分号结尾.


### 在 find 之外使用 xargs 

```           
~ $ find some-file-criteria some-file-path | \
> xargs some-great-command-that-needs-filename-arguments
                        
~/tmp $ ls -1 | xargs
December_Report.pdf README a archive.tar mkdirhier.sh
~/tmp $ ls -1 | xargs file
December_Report.pdf: PDF document, version 1.3
README: ASCII text
a: directory
archive.tar: POSIX tar archive
mkdirhier.sh: Bourne shell script text executable
~/tmp $
```

### 了解何时 grep 应该执行计数——何时应该绕过 
避免通过管道将 grep 发送到 wc -l 来对输出行数计数。grep 的 -c 选项提供了对与特定模式匹配的行的计数，并且一般要比通过管道发送到 wc 更快

```
~ $ time grep and tmp/a/longfile.txt | wc -l
2811
real    0m0.097s
user    0m0.006s
sys     0m0.032s
~ $ time grep -c and tmp/a/longfile.txt
2811
real    0m0.013s
user    0m0.006s
sys     0m0.005s
~ $ 
```

 除了速度因素外，-c 选项还是执行计数的好方法。对于多个文件，带 -c 选项的 grep 返回每个文件的单独计数，每行一个计数，而针对 wc 的管道则提供所有文件的组合总计数。 然而，不管是否考虑速度，此示例都表明了另一个要避免地常见错误。这些计数方法仅提供包含匹配模式的行数——如果那就是您要查找的结果，这没什么问题。但是在行中具有某个特定模式的多个实例的情况下，这些方法无法为您提供实际匹配实例数量 的真实计数。归根结底，若要对实例计数，您还是要使用 wc 来计数。 
 
### 匹配输出中的某些字段，而不只是对行进行匹配 
当您只希望匹配输出行中特定字段 中的模式时，诸如 awk 等工具要优于 grep。 下面经过简化的示例演示了如何仅列出 12 月修改过的文件。 清单 19. 坏习惯 9 的示例：使用 grep 来查找特定字段中的模式 

```                
~/tmp $ ls -l /tmp/a/b/c | grep Dec
-rw-r--r--  7 joe joe  12043 Jan 27 20:36 December_Report.pdf
-rw-r--r--  1 root root  238 Dec 03 08:19 README
-rw-r--r--  3 joe joe   5096 Dec 14 14:26 archive.tar
~/tmp $
```
 在此示例中，grep 对行进行筛选，并输出其修改日期和名称中带 Dec 的所有文件。因此，诸如 December_Report.pdf 等文件是匹配的，即使它自从一月份以来还未修改过。这可能不是您希望的结果。为了匹配特定字段中的模式，最好使用 awk，其中的一个关系运算符对确切的字段进行匹配，如以下示例所示： 清单 20. 好习惯 9 的示例：使用 awk 来查找特定字段中的模式                 

```
~/tmp $ ls -l | awk '$6 == "Dec"'
-rw-r--r--  3 joe joe   5096 Dec 14 14:26 archive.tar
-rw-r--r--  1 root root  238 Dec 03 08:19 README
~/tmp $
```
 
### 停止对 cat 使用管道 
 grep 的一个常见的基本用法错误是通过管道将 cat 的输出发送到 grep 以搜索单个文件的内容。这绝对是不必要的，纯粹是浪费时间，因为诸如 grep 这样的工具接受文件名作为参数。您根本不需要在这种情况下使用 cat

```                
~ $ time cat tmp/a/longfile.txt | grep and
2811

real    0m0.015s
user    0m0.003s
sys     0m0.013s
~ $ time grep and tmp/a/longfile.txt
2811

real    0m0.010s
user    0m0.006s
sys     0m0.004s
~ $ 
```