---
layout: post
title: Linux权限管理
tags: Linux
categories: Linux
---

* TOC 
{:toc}

## `chmod`

**改变权限**

*数字法*

root@www ~]#  `chmod [-R] xyz` 文件或目录

选项与参数：

`xyz` : 就是刚刚提到的数字类型的权限属性，为 rwx 属性数值的相加。

`-R` : 进行递归(recursive)的持续变更，亦即连同次目录下的所有文件都会变更

*符号法*

chmod

ugoa

+(加入) -(除去) =(设定)

rwx

文件或目录

>例:
>
>[root@www ~]#  chmod  u=rwx,go=rx  .bashrc
>
>\#  注意！那个 u=rwx,go=rx 是连在一起的，中间并没有任何空格！

---

## `chgrp `


**改变所属群组**

chgrp [root@www ~]#  `chgrp [-R] filename ...`

选项与参数：

`-R` : 进行递归(recursive)的持续变更，亦即连同次目录下的所有文件、目录 都更新成为这个群组之意。常常用在变更某一目录内所有的文件之情况。

---

## `chown`

**改变文件拥有者**

[root@www ~]#  `chown [-R]` 账号名称 文件或目录

[root@www ~]#  `chown [-R]` 账号名称:组名 文件或目录

选项与参数：

`-R` : 进行递归(recursive)的持续变更，亦即连同次目录下的所有文件都变更

---

## `chattr`

**配置文件隐藏属性**

[root@www ~]#  `chattr [+-=][ASacdistu]` 文件或目录名称

选项与参数：

`+`   ：添加某一个特殊参数，其他原本存在参数则不动。

`-`   ：移除某一个特殊参数，其他原本存在参数则不动。

`=`   ：配置一定，且仅有后面接的参数

>`A`  ：当配置了 A 这个属性时，若你有存取此文件(或目录)时，他的存取时间 atime将不会被修改，可避免I/O较慢的机器过度的存取磁碟。这对速度较慢的计算机有帮助。

`S`  ：一般文件是非同步写入磁碟的(原理请参考第五章sync的说明)，如果加上 S 这个属性时，当你进行任何文件的修改，该更动会『同步』写入磁碟中。

`a`  ：当配置 a 之后，这个文件将只能添加数据，而不能删除也不能修改数据，只有root才能配置这个属性。

`c` ：这个属性配置之后，将会自动的将此文件『压缩』，在读取的时候将会自动解压缩，但是在储存的时候，将会先进行压缩后再储存(看来对於大文件似乎蛮有用的！)

`d`  ：当 dump 程序被运行的时候，配置 d 属性将可使该文件(或目录)不会被 dump 备份

`i`  ：这个 i 可就很厉害了！他可以让一个文件『不能被删除、改名、配置连结也无法写入或新增数据！』对於系统安全性有相当大的助益！只有 root 能配置此属性

`s`  ：当文件配置了 s 属性时，如果这个文件被删除，他将会被完全的移除出这个硬盘空间，所以如果误删了，完全无法救回来了喔！

`u`  ：与 s 相反的，当使用 u 来配置文件时，如果该文件被删除了，则数据内容其实还存在磁碟中，可以使用来救援该文件喔！

注意：属性配置常见的是 a 与 i 的配置值，而且很多配置值必须要身为 root 才能配置

---

## `lsattr`

**显示文件隐藏属性**

[root@www ~]#  `lsattr [-adR]` 文件或目录

选项与参数：

`-a` ：将隐藏档的属性也秀出来；

`-d` ：如果接的是目录，仅列出目录本身的属性而非目录内的档名；

`-R` ：连同子目录的数据也一并列出来！

---

## `umask`

**被排除的权限默认值**

*目前使用者在创建文件或目录时候的被排除的权限默认值*

[root@www ~]#  `umask`      显示`数字`型态的权限配置分数

0022          <==与一般权限有关的是后面三个数字，表示在777中被排除的权限，即该目录中新增文件或目录的默认权限为755，又因为新增文件默认不具有x权限，所以该目录中新增目录的默认权限为755，文件的默认权限为644

[root@www ~]#  `umask -S`    以`符号`类型的方式来显示出权限

u=rwx,g=rx,o=rx

[root@www ~]#  `umask 002`  `指定`权限配置分数

`!` 异或运算可排除权限

---

## 文件特殊权限

**`SUID`**

>`1` SUID 权限仅对二进位程序(binary program)有效；(对目录无效)

>`2` 运行者对於该程序需要具有 x 权限；

>`3` 运行者将具有该程序拥有者 (owner) 的权限，该权限仅在运行该程序的过程中有效 (run-time)

>例如：/etc/shadow只有root能够访问，但是普通用户对于passwd有s权限，passwd的owner是root，所以普通用户可以在运行passwd时暂时获得root权限对/etc/shadow进行操作。

`!` SUID权限指的是文件权限中owner部分x权限的特殊版

`! chmod 4---` 可以赋予SUID权限，所属用户的x位将变成s, 若不具备x权限，s将变成S

**`SGID`**

*当为文件时*

>`1` SGID 仅对二进位程序有用；
>
>`2` 运行者对於该程序需具有x 权限；
>
>`3` 运行者在运行的过程中将会获得该程序群组（group）的权限,该权限仅在运行该程序的过程中有效 (run-time) --- 基本同SUID

*当为目录时*

>`1` 使用者若对於此目录具有 r 与 x 的权限时，该使用者能够进入此目录；
>
>`2` 使用者在此目录下的有效群组(effective group)将会变成该目录所属的群组；用途：若使用者在此目录下具有 w 的权限(可以新建文件)，则使用者所创建的新文件的群组与此目录所属的群组相同。

`!` SGID权限指的是文件或目录权限中group部分x权限的特殊版
`! chmod 2---` 可以赋予SGID权限，所属组的x位将变成s, 若不具备x权限，s将变成S

**`SBIT`**

·SBIT权限仅对目录有效；（对文件无效）

·使用者对於此目录需要具有 w, x 权限；

·当使用者在该目录下创建文件或目录时，仅有自己与 root 才有权力删除该文件

`! chmod 1---` 可以赋予SBIT权限，其他的x位将变成t