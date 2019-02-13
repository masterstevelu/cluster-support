# PBS命令

## 提交任务qsub

语法：

qsub [-a date_time] [-A account_string] [-e path] [-h] [-I] [-j join] [-k keep] [-l resource_list] [-m mail_options] [-n Node_allocation_Method [-L v1,[v2,[v3,[v4]]]]] [-M user_list] [-N name] [-o path] [-p priority] [-q pool] [-r y|n] [-u user_list] [-v variable_list] [-V] [script]

选项：
	参数: script参数被省略时，该命令可以从标准输入获得脚本文件名。
   -a   时间。格式为[[[[CC]YY]MM]DD]hhmm[.SS]。CC表示世纪，YY表示年（后两位数字），MM表示月（两位数字），DD表示天（两位数 字），hh表示小时（两位数字），mm表示分（两位数字），SS表示秒（两位数字）。如果DD指定的是未来日子，而未指定MM，则MM缺省值为当前月，否 则，MM的缺省值为下个月。如果hhmm指定的未来时间，而未指定DD,则DD的缺省值为当天，否则，DD的缺省值为明天。如果提交作业时使用该选项，当 指定时间还没到时，作业状态显示为”W”。
   -e   指定错误输出文件名，格式为[hostname:]path_home。Hostname是返回错误输出文件的主机名，path_home是错误输出文件 的绝对路径，如果指定了相对路径，则相对用户的主目录。不使用该选项时，缺省值是在用户主目录下，以“作业名.e作业ID”命名的文件
   -o   指定输出文件名，格式为[hostname:]path_home。缺省值是在用户主目录下，以“作业名.e作业ID”命名的文件
   -h   指定在提交作业时，设置用户级’u’挂起。如果不指定，则设置挂起类型为’n’,即不挂起。
   -I   指定作业以交互方式运行。
   -j   指定合并错误输出和实际输出。如果指定’oe’，则合并到标准输出文件中；如果指定’eo’，则合并到标准错误输出文件中.
   -k   指定执行主机是否保留错误输出和实际输出。如果指定‘o’,则仅保留标准输出；如果指定’e’，则仅保留标准错误输出；如果指定’oe’或‘eo’,则保留标准输出和标准错误输出；如果指定’n’,则不保留任何输出。
   -l   指定作业所需要的资源，设定对可消耗资源的限制。如果不设置，则无限制。例如： resource_name[=[value]][,resource_name[=[value]],…]
其中，结点的属性和主机名或数目之间通过“：”分隔。如果不指定结点数，则缺省为1。结点的属性包括ppn（每个结点上的进程数,缺省为1）和系统管理员设置的属性（如batch、bigmem）
例如：请求12个结点，不管其属性
   -l nodes=12
请求12个属性为batch的结点和14个属性为bigmem的结点
   -l nodes=12:batch+14:bigmem
请求4个结点，每个结点上使用2个CPU
   -l nodes=4:ppn=2

## 显示状态qstat

语法：qstat [-f][-W site_specific] [job_identifier... | destination...]
       qstat -Q [-f][-W site_specific] [destination...]
       qstat -B [-f] [-W site_specific] [server_name...]
   参数：destination可以为pool，@server，pool@server
   作用：查看作业、结点池和批处理服务器的状态。命令格式一可以输出所指定作业ID或者结点池中所有作业的状态，命令格式二可以输出每个结点池的状态信息，命令格式三可以输出服务器的状态。
   举例：
   1. 显示已经配置的所有结点池状态信息。
   qstat -q
   2. 显示已经提交的作业状态信息
   qstat -a
   3. 显示指定作业的所有状态信息
   $ qstat -f 91
   4. 显示服务器的状态
   $qstat –B
   Server Max Tot Que Run Hld Wat Trn Ext Status
   console 0 0 0 0 0 0 0 0 Active

## 查询作业qselect

   语法：qselect [-a [op]date_time] [-A account_string] [-h old_list] [-l resource_list] [-N name] [-p [op]priority] [-q destination] [-r y|n] [-s states] [-u user_list]
   参数：op表示某一个作业属性值和选项参数值之间的关系。如
   .eq. （等于）
   .ne. （不等于）
   .ge. （大于或等于）
   .gt. （大于）
   .le. （小于）
   其它参数含义见qsub命令。
作用：列出符合选项要求的作业ID。这些作业来自于单个服务器。如果没有任何选项，该命令则列出该用户被授权的服务器上的所有作业。对那些普通用户来说，该命令只显示该用户所提交的作业。

## 挂起作业qhold
语法：qhold [-h hold_list] job_identifier …
作用：挂起批处理作业。挂起有三种类型：普通用户级‘u’、管理员级（操作员级）‘o’、系统级‘s’，缺省值为不挂起‘n’。用户只能在用户级别挂起自己提交的作业，操作员可以在用户级和操作员级挂起任何作业，系统管理员可以在任何级别上挂起任何作业。
在执行该命令时，如果作业在运行结点池里排队，那么作业将直接被挂起；如果作业处于运行状态，为了中断作业的执行，必须采取其他办法。如果被挂起作业的主 机系统支持一致点检查或者重新启动，则挂起正在运行作业将引发以下操作：首先检查作业的一致性，然后释放该作业所占用的资源，最后该作业位于执行结点池 中，处于挂起状态；如果被挂起作业的主机系统不支持一致点检查或者重新启动，则仅设置指定的挂起作业类型，而实际上并不能挂起，除非调用qrerun命令 重新运行该作业时，该挂起请求才生效。

## 释放作业qrls
语法：qrls [-h hold_list] job_identifier ...
作用：释放被挂起的批处理作业。由于作业的挂起有三种类型：USER、OPERATOR和SYSTEM。所以，要释放不同类型的作业挂起，用户就必须具有相应的权限。缺省为USER级

## 重新运行作业qrerun
语法：qrerun job_identifier ...
作用：重新运行所指定的作业。

## 更改作业属性qalter
语法：qalter [-a date_time] [-A account_string] [-e path] [-h hold_list] [-j join] [-k keep] [-l resource_list] [-m mail_options] [-M user_list] [-N name] [-o path] [-p priority] [-r c] [-u user_list] job_identifier...
参数：各参数的含义见qsub命令。
作用：更改批处理作业的属性。主要修改所指定作业ID的相关属性（选项表中所列出的属性）。
删除作业qdel
语法：qdel [-W delay|force] job_identifier ...
参数：-W 当指定delay 时，表示在删除作业前需要等待的时间（秒）
当指定force时，强制删除该作业。
作用：删除批处理作业。按照命令行中所指定的作业ID的顺序来删除作业。
给作业发送消息qmsg
语法：qmsg [-E] [-O] message_string job_identifier ...
参数：-E 将消息串写入错误输出文件
  -O将消息串写入输出文件
作用：给批处理作业发送消息。该命令通过给作业的所有者（批处理服务器）发送消息，从而把消息写入作业的输出文件，也就是说，该命令并不是直接把消息写入作业的输出文件。
