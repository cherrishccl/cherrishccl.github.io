---
layout: post
title: Linux定时任务
tags: Linux 定时任务
categories: Linux
---

* TOC
{:toc}


## `at`

at 是个可以处理**仅运行一次**就结束计划任务的命令，不过要运行 at 时， 必须要有 atd 这个服务的支持才行。

[root@www ~]#  `/etc/init.d/atd restart`

使用 at 这个命令来产生所要运行的任务，并将任务以文件的方式写入 `/var/spool/at` 目录内，该任务便能被 atd 服务运行

>对at进行权限控制
>
>* 先找寻 `/etc/at.allow` 这个文件，写在这个文件中的使用者才**能**使用 at ，没有在这个文件中的使用者则不能使用 at (即使没有写在 at.deny 当中)；
>
>* 如果 /etc/at.allow 不存在，就寻找 `/etc/at.deny` 这个文件，写在这个 at.deny 的使用者**不能**使用 at ，而没有在这个 at.deny 文件中的使用者，就可以使用 at 咯；
>
>* 如果两个文件都不存在，那么只有 root 可以使用 at 命令。

[root@www ~]#  `at [-mldv] TIME`

[root@www ~]#  `at -c 任务号码`

选项与参数：

`-m`  ：当 at 的任务完成后，即使没有输出信息，亦以 email 通知使用者该任务已完成。

`-l`  ：at -l 相当於 `atq`，列出目前系统上面的所有该使用者的 at 任务；

`-d`  ：at -d 相当於 `atrm` ，可以取消一个在 at 排程中的任务；

`-v`  ：可以使用较明显的时间格式列出 at 排程中的任务列表；

`-c`  ：可以列出后面接的该项工作的实际命令内容。

>TIME：时间格式，这里可以定义出『什么时候要进行 at 这项工作』的时间，格式有：
>
>*  `HH:MM`
>
>	ex> 04:00
>
>	在今日的 HH:MM 时刻进行，若该时刻已超过，则明天的 HH:MM 进行此工作。
>
>*  `HH:MM YYYY-MM-DD`
>
>	ex> 04:00 2009-03-17
>
>	强制在某年某月某日的某时刻进行！
>
>*  `HH:MM[am|pm] [Month] [Date]`
>
>	ex> 04pm March 17
>
>	强制在某年某月某日的某时刻进行！
>
>*  `HH:MM[am|pm] + number [minutes|hours|days|weeks]`
>
>	ex> now + 5 minutes	ex> 04pm + 3 days
>
>	在某个时间点『再加几个时间后』才进行。

`!`at命令会进入atshell环境，使用绝对路径输入指令不易出现问题

`!`at 的运行与终端机环境无关，想要查看io结果，需要指定终端`echo "Hello" > /dev/tty1`

`!`at 的运行独立于bash，所以at任务可以离线运行

**`batch`**：系统有空(负载低于0.8 )时才进行背景任务，用法与at完全相同，本质上是是at命令加一些控制参数



## `crontab` 

crontab 这个命令所配置的工作将会循环的**一直进行**下去， 可编辑 /etc/crontab 来支持，让 crontab 生效的服务是 `crond`

[root@www ~]#  ·`/etc/init.d/crond restart`

当使用者使用 crontab 这个命令来创建工作排程之后，该项工作就会被纪录到 `/var/spool/cron/`目录内

cron 运行的每一项工作都会被纪录到 `/var/log/cron` 内

>权限控制(与at相同，默认保留/etc/cron.deny )
>
>* `/etc/cron.allow`：将可以使用 crontab 的帐号写入其中，若不在这个文件内的使用者则不可使用 crontab；
>
>* `/etc/cron.deny`：将不可以使用 crontab 的帐号写入其中，若未记录到这个文件当中的使用者，就可以使用 crontab

[root@www ~]#  `crontab [-u username] [-l|-e|-r]`

选项与参数：

`-u`  ：只有 root 才能进行这个任务，亦即帮其他使用者创建/移除 crontab 工作排程；

`-e`  ：编辑 crontab 的工作内容

`-l`  ：查阅 crontab 的工作内容

`-r`  ：移除所有的 crontab 的工作内容，若仅要移除一项，请用 -e 去编辑。

[dmtsai@www ~]$ `crontab -e`

>`0 12 * * * <command>`
>
>`分 时 日 月 周 <命令串>`

特殊字符

`*`代表任何时刻都接受的意思

`,`代表分隔时段的意思

`-`代表一段时间范围内

`/n`代表每间隔n单位

系统计划任务(不受crontab -e命令管理)
编辑`/etc/crontab`文件，该文件每1分钟被检查一次


## `anacron`

侦测系统未进行的 crontab 任务(例如 crontab 设置在周末运行，但是周末关机了)

[root@www ~]#  `anacron [-sfn] [job]..`

[root@www ~]#  `anacron -u [job]..`

选项与参数：

`-s`  ：开始一连续的运行各项工作 (job)，会依据时间记录档的数据判断是否进行；

`-f` ：强制进行，而不去判断时间记录档的时间戳记；

`-n`  ：立刻进行未进行的任务，而不延迟 (delay) 等待时间；

`-u`  ：仅升级时间记录档的时间戳记，不进行任何工作。

`job` ：由 **/etc/anacrontab** 定义的各项工作名称。

配置文件位于`/etc/anacrontab`

>[root@www ~]#  cat /etc/anacrontab
>
>SHELL=/bin/sh
>
>PATH=/sbin:/bin:/usr/sbin:/usr/bin
>
>MAILTO=root

>1       65      cron.daily     run-parts /etc/cron.daily
>
>7       70      cron.weekly    run-parts /etc/cron.weekly
>
>30      75      cron.monthly   run-parts /etc/cron.monthly

天数   延迟时间（分） 工作名称定义   实际要进行的命令串

任务文件位于`/var/spool/anacron/`
