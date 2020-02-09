# LinuxLearning
## Linux
内核 www.kernel.org</br>
厂商在内核上在开发就完成了开发版</br>
常见发行版： redhat/debian/ubuntu</br>
常用**centos**作为服务器

### 系统
#### 系统分区
正确的分区可以加快读取和写入的效率

##### 分区类型
- 主分区 最多只能有4个
- 扩展分区
	- 最多只能有1个
	- 主分区+扩展分区最多4个
	- 不能写入数据，只能包含逻辑分区
- 逻辑分区 可正常格式化，且写入数据

[举例]</br>
主分区1，主分区2，主分区3，扩展分区4（逻辑分区5，逻辑分区6）

##### 格式化
写入文件系统(FAT16, EXT2, EXT3, EXT4)。EXT4表示每4kb一个小隔断</br>
1. 把硬盘分割成等大小的数据块Block</br>
2. 每个文件有一个inode号，用于查找数据块

##### 硬件设备文件名（这一步windows没有）
所有硬件都被视为文件</br>
比如IDE硬盘是`/dev/hd[a-d]` 共四个</br>
/dev/hda1/ 表示是第一个硬盘的第一个分区</br>
**5总是代表第一个逻辑分区**

##### 挂载
给分区指定挂载点

- 必须分区
	- / （根分区）
	- swap分区（交换分区，内存2倍，不超过2gb）</br> **可以理解为虚拟分区，当内存不足时，用它代替**
- 推荐分区
	- /boot （启动分区，200mb）</br> **防止根分区写满了，导致电脑无法启动**

#### 文件系统结构
- /
	- /boot
	- /etc
		- /etc/a
		- /etc/b
		- /etc/c
	- /home

其在硬盘上的分配可能是：
/ -> /dev/sda3</br>
/boot -> /dev/sda1</br>
/home -> /dev/sda2</br>
可以每个目录有自己独立的子空间

Linux不依赖拓展名区分文件类型</br>
Linux所有存储设备都必须挂载之后才可使用

### 服务器管理和维护
#### 各个目录的作用
- /bin/ 存放系统命令
- /sbin/ 只有管理员才可执行的命令目录
- /usr/bin/ 单用户模式下的命令目录
- /usr/sbin/ 单用户模式下管理员的命令目录
- /boot/ 系统启动目录
- /dev/ 设备文件保存位置
- /etc/ 默认的配置文件位置
- /home/ 普通用户家目录 比如:/home/wss/
- /lib/ 函数库
- /lost+found/ 意外关机而产生一些文件碎片
- /media/ 挂载多媒体设备
- /mnt/ 挂载U盘
- /misc/ 挂载NFS服务的共享服务
- /opt/ 第三方安装软件位置
- /proc/ 虚拟文件系统
- /sys/ 存放在内存中内核
- /root/ 超级用户家目录
- /srv/ 服务器目录
- /tmp/ 临时目录
- /usr/ 系统资源目录
- /var/ 动态数据保存位置

### Linux常用命令
#### 文件处理命令
- `ls [-ald] [文件]`
	- `-a` .开头文件的隐藏文件
	- `-l` 看属性。权限，引用计数，所有者，所属组，字节大小，最后修改时间，文件名
	- `-lh` 用合适的单位表示文件大小
	- `-d` 显示文件夹本身信息，而不是文件夹下的东西
	- `-i` 查看inode

[权限]

- `-开头`是文件。`d开头`文件夹。`l开头`软连接
- `-rw-r--r--` 类型(1),所有者u(2,4),所属组g(5,7),其他人o(8,10)
- r读 w写 x执行

#### 目录处理
- `mkdir [-p] [目录名]`
	- `-p` 递归创建
- `cd [目录]`
- `pwd` 查看当前绝对路径
- `rmdir [目录名]` 删除**空**目录
- `cp [-rp] [源文件] [目标]` 复制
	- `-r` 复制目录文件夹
	- `-p` 保持原文件属性
- `mv [原文件] [目标]` 剪切 更名
- `rm [-rf] [文件]` 删除文件
	- `-r` 删除目录
	- `-f` 强制执行
	- `rm -rf *` 删除文件夹下所有文件
- `touch 文件名` 创建空文件
- `cat [-n] 文件名` 显示文件内容
	- `—n` 显示行号
- `tac`倒着显示cat
- `more [文件名]` 分页显示文件内容
- `less [文件名]`
- `head -n 数字 文件` 看文件的前几行
- `tail -n 数字 文件` 看末尾的几行
	- `-f` 动态显示，实时变化显示文件

#### 链接命令
- `ls [-s] [原文件] [目标文件]`
	- `-s` 创建软连接

[软连接]
软连接类似于windows快捷方式</br>
其到底有什么权限是由原文件决定。软连接的权限是lrwxrwxrwx</br>
inode与原文件不同。

[硬链接]
除了文件名，其他一模一样</br>
与cp -p最大的区别是可以**同步更新**</br>
如果原文件丢失，硬链接仍可访问</br>
inode与原文件相同</br>
不可跨分区，不可针对目录使用

#### 权限管理
只有所有者和root可以更改权限

- `chmod [{ugoa}{+-=}{rwx}][文件]` 或者 `chmod[mode=421][文件]`
	- `-R` 递归修改
	- `chmod u+x readme.txt` 给这个文件的所有者加上执行权限
	- `chmod 764 readme.txt` 结果为rwxrw-r--
		- r:4 w:2 x:1

- `umask [-s]` 显示权限的默认值
	- `umask 077` 将默认权限改为700 rwx------

[r权限]

- 对于文件来说：cat/more/less/head/tail 可以看内容
- 对于目录来说： ls  列出内容

[w权限]

- 对于文件来说： vim修改文件
- 对于目录来说： touch/mkdir/rmdir/rm 创建删除文件

[x权限]
- 对于文件来说：可以执行
- 对于目录来说：cd 可以进入</br>**有r一定有x**

#### 常用搜索命令
- `find [搜索范围] [匹配条件]`
	- `find /etc -name init` 在/etc下搜所有叫init的
	- `-iname` 不区分大小写
	- `-size +n` 文件大于n
	- `-user` 所有者
	- `-amin` 访问时间
	- `-cmin` 文件属性时间
	- `-mmin` 内容时间
	- `-a` 表示and 同时满足条件
	- `-o` or
	- `-type [d f]` d目录 f文件
- `locate [文件名]` 在资料库中搜索文件，速度更快，但是新建的文件会找不到。可以通过`updatedb` 更新资料库
- `which 命令` 查询命令放在哪
- `whereis` 同which 也可以找到帮助文档
- `grep [关键词] [文件]` 在文件内找到关键词
	- `-i` 不区分大小写
	- `-v` 排除关键词行不看

#### 帮助命令
- `man [命令或配置]
	- `man 5 passwd` 当命令和配置重名 5看配置 1看命令
- `whatis [命令]`
	- `whatis ls` 一行较短的信息
- `apropos [配置]` 一行配置信息
- `[命令] --help` 查看主要选项
- `help [命令]` 内置命令
	- 不能看cd，因为cd是内置命令，而不是在/usr/bin中

#### 用户管理命令	
- `useradd 用户名` 增加用户
- `passwd 用户名` 改密码
- `who` 查看当前用户
- `w` 比`who`信息更全

#### 压缩解压命令
- `gzip 文件名` 压缩
- `gunzip 文件名` 解压 **不保留原文件** 'gzip -d`也可
- `tar [-cvfxz] [压缩后文件名] [目录]`
	- `-c` 打包
	- `-v` 显示详细信息
	- `-f` 指定文件名
	- `-z` 打包同时压缩
	- `tar -xjf wss.tar.bz2 wss` 压缩
	- `-x` 解包
	- `-z` 解压缩
	- `tar -zxf wss.tar.gz`
- `zip [-r目录] [压缩后文件名] [目录]` 保留原文件
- `unzip [文件名]`
- `bzip2 [-k保留原文件] [文件] 压缩比大`
- `bunzip2 [-k]`

格式 | 压缩 |  解压  
---|---|---
.gz | gzip | gunzip(gzip -d) |
.tar | tar -cf | tar -xf |
.tar.gz | tar -zcf | tar -zxf |
.zip | zip -r | unzip |
.bz2 | bzip2 | bunzip2 |
.tar.bz2(目录) | tar -cjf | tar -xjf

#### 网络命令
- `write [用户名]` 给指定用户发送信息
- `wall [信息]` 给所有用户发送信息
- `ping [-c指定发送次数] IP地址`
- `ifconfig` 查看网卡信息
- `mail [用户名]` 发信
- `last` 历史用户登录信息 可查看何时重启
- `lastlog` 最后一次登录信息
- `traceroute` 访问网页的路径
- `netstat [选项]`
	- `-t` tcp
	- `-u` udp
	- `-l` 监听
	- `-r` 路由
	- `-n` 显示IP地址和端口
- `setup` 改ip配置网络
- `mount [-t 文件系统] 舍命文件名 挂载点` 挂载命令

#### 开机重启
- `shutdown [-hrc] [时间]`
	- `-c` 取消前一个命令
	- `-h` 关机
	- `-r` 重启
	- `shutdown -h 20:30`
	- `shutdown -h now`

### Vim
#### 常用操作
插入模式 -ESC-> 命令模式 -:-> 编辑模式</br>
插入模式 <-i,a,o- 命令模式 <-回车- 编辑模式

- `a` 在光标所在字符后插入
- `A` 在光标所在行尾插入
- `i` 字符前插入
- `I` 行首插入
- `o` 光标下插入新行
- `O` 光标上插入新行
- `:set nu` 设置行号
- `:set nonu` 取消行号
- `gg` 到第一行
- `G`  到最后一行
- `:n` 到n行
- `$` 行尾
- `0` 行首
- `x` 删除字符光标所在处
- `nx` 删除n个字符
- `dd` 删除一行
- `dG` 删除这一行到最后一行
- `D` 光标到行尾删除
- `:n1,n2d` 删指定行
- `yy` 复制
- `nyy` 复制n行
- `p,P` 粘贴到所在行下或上
- `dd` 剪切
- `r` 替换单个
- `R` 一直替换到ESC
- `u` 撤回
- `/string` 搜索string
- `:n1,n2s/old/new/g` n1到n2行old替换为new
- `:%s/old/new/g` 全文替换
- `:w` 保存
- `:wq` 保存退出
- `:w 路径` 另存为
- `:q!` 不保存退出
- `:wq!` 保存及退出 只有所有者和root可以

### 软件安装
#### 管理简介
[分类]

- 源码包
- 二进制包（由源码编译而成）

#### rpm
- `rpm [-ivh] 包全名` 安装
	- `-i` 安装
	- `-v` 显示详细信息
	- `-h` 显示进度
	- `--nodeps` 不检查依赖
	- `rpm -Uvh 包全名` 省级
	- `rpm -e 包名`

#### yum
解决依赖性

- `yum list`
- `yum search 关键字`
- `yum -y install 包名` -y自动安装 不询问
- `yun -y update 包名`
- `yum -y remove 包名` 尽量不要用 可能删除依赖导致系统问题

### Shell基础
命令行解释器</br>
脚本语言
#### Shell的执行方式
- `echo [选项] [文字]` 输出指令

**.sh文件第一行必须写 `#!/bin/bash`**</br>

```
#执行方式1
chmod 755 hello.sh
./hello.sh

#执行方式2
bash ./hello.sh
```
##### dos2unix [文件名]
将windows格式变为unix格式

##### unix2dos [文件名]
将unix格式转化为windows

#### Bash的基本功能
##### 别名
```alias 别名='原命令'```

只会临时生效</br>
让别名永久生效 在/root/.bashrc中做修改
##### 常用快捷键
- ctrl+C 强制终止当前命令
- ctrl+L 清屏
- ctrl+U 删除或剪切光标之前的命令
- ctrl+Y 粘贴ctrl+U或ctrl+k的内容
- ctrl+R 在历史命令中搜索
- ctrl+D 退出当前终端

##### 输入输出重定向
[输出重定向]

- ``` 命令>文件``` 覆盖的方式 正确的输出存入文件
- ```命令>>文件``` 追加的方式
- ```命令 2>文件``` 报错输出存入文件
- ```命令 &> 文件名``` 同时保存正确错误的输出 [最有用]
- ```命令 &>/dev/null``` 将所有输出都不要看，这个文件等于是垃圾箱
- ```命令 >> 文件1 2>> 文件2```  把正确和错误的输出分开文件放

[输入重定向]

- ```wc < 文件名``` 统计该文件的字数

##### 多命令顺序执行与管道
[多命令顺序执行]

- `命令1;命令2` 不管1是否能正确执行 都会执行2
	- 训练时可以用 date; python train.py; date 查看训练时间
- `命令1&&命令2` 只有1执行了才会执行2
- `命令1||命令2` 只有1报错 才会执行2 如果1执行了 则2不执行
	- `命令 && echo yes || echo no` 命令执行则输出yes

[管道符]命令1的输出作为2的操作对象</br>
`命令1 | 命令2`

- `命令1 | grep 关键词` 查找命令1中的关键词

##### 通配符与特殊符号
- `?` 匹配一个任意字符
- `*` 匹配任意0个或多个，匹配任何内容
- `[]` 任意一个字符，比如[abc]匹配其中一个
- `[-]` [a-z]
- `[^]` 代表非
- `''` 所有特殊符号成普通符号
- `""` 特殊符号还是特殊意思
- \`\` （反引号）表示内部的是一个命令 同`$()`
- `#` 注释
- `$` 表示调用变量

##### Bash的变量
默认为字符串型，进行数值运算要指定为数值型</br>
[自定义变量]

- `unset name` 删除变量

[环境变量]

- `export 变量名=变量值` 申明变量
- `env` 查询变量

- `PATH` 系统查找命令路径
- `PS1` 定义系统提示符的变量 就是每条命令最前面现实的东西[root@wyj]
	- PS1='' 可修改

[位置参数变量]

- `$n` 表示第n个参数 第一个位置为0命令本身
- `$*` 所有参数，看做一个整体
- `$@` 所有参数，把每个参数区别对待
- `$#` 表示参数个数

[预定义变量]

- `$?` 判断上一个返回状态 若为0则上一个命令正确
- `$$` 当前进程的进程号PID
- `$!` 后台运行的最后一个PID
- `read [选项] [变量名]` 接收键盘输入
	- `-p` 提示信息
	- `-t` 等待秒数
	- `-n` 字符数
	- `-s` 隐藏输入的数据
 
#####  Bash的运算符
变量的类型默认是字符串

- `declare` [+/-][选项] 变量名
	- `-` 给变量设定类型属性
	- `+` 取消变量的类型属性
	- `-i` 将变量声明为整数型
	- `-x` 将变量声明为环境变量
	- `-p` 显示指定变量的被声明的类型
- `declare -i cc=$aa+$bb` 完成加法
- `cc=$(($aa+$bb))` **推荐**

##### 环境变量配置

- `source 配置文件` 更改过后使配置文件生效 同`. 配置文件`

[环境变量配置文件]

- 对任何用户生效
	- /etc/profile 
	- /etc/profile.d/*.sh
	- /etc/bashrc
- 针对固定用户
	- ~/.bash_profile
	- ~/.bashrc

### Shell编程
#### 基础正则表达式
正则表达式：在文件中匹配字符串，为包含匹配</br>
通配符：匹配文件名，为完全匹配

- `*` 前一个字符匹配0或任意多次
- `.` 任意一个字符
- `^` 代表行首
- `$` 匹配行尾
- `[]` []中任意一个字符
- `[^]` 除括号中以外的字符
- `\` 转义符
- `\{n\}` 表示前面字符出现n次
- `\{n,\}` 前面字符出现不小于n次
- `\{n,m\}` 出现n,m次

#### 字符截取

- `cut [选项] 文件名`  提取第几列
	- `-f` 提取第几列
	- `-d` 指定分隔符分割列 比如冒号，封号，句号
- `printf '输出格式' 输出内容` 格式化输出
	- `%ns` 输出字符串，n代表个数
	- `%ni` n个整数
	- `%m.nf` 输出m位数，其中n位数小数
- `awk '条件1 {动作1} 条件2 {动作2} ...' 文件名`
	- `awk '{print $2 "\t" $6}' student.txt` 输出第二第六列</br>
	一列一列读取文件，可以识别空格分隔符，而cut不行
	- `'BEGIN{...}'`  先执行BEGIN里的
		- `'BEGIN{FS=":"}'`  将分隔符设为冒号
	- `'END{...}'` 在最后执行
- `sed [选项] '动作'` 可以从管道符接收数据并进行修改，而vim只能修改文件</br>
   [选项]
	- `-n` 经过处理的显示到屏幕
	- `-e` 允许多条sed命令编辑
	- `-i` 直接修改读取数据的文件，而不是输出到屏幕
	
	[动作]
	
	- `a` 追加行 未结束用\
	- `c` 行替换
	- `i` 插入
	- `d` 删除
	- `p` 打印
	- `s` 字符替换 `sed 's/旧/新/g'`

#### 字符处理

- `sort [选项] 文件名` 排序
	- `-f` 忽略大小写
	- `-n` 以数值进行排序
	- `-r` 反向排序
	- `-t` 指定分隔符
	- `-k n,m` 指定字段范围排序 从第n开始到m结束

#### 条件判断

- `test [选项] 文件` 或 `[ 选项 文件 ]`  注意空格
	- `-d 文件` 是否存在，且为目录文件
	- `-e 文件` 是否存在
	- `-f 文件` 是否存在 且为普通文件
	- `-r -w -x` 读写执行权限是否有
	- `整数1 -eq 整数2` 判断相等 还有`-ne -gt -lt -ge -le`
	- `-z` 判断是否为空
	- ` 字符串1==字符串2 ` 字符串是否相等
	- `-a -o !` 与 或 非 

`echo $?` 来显示上一条命令的结果</br>
`[ -d /root ] && echo 'yes' || echo 'no'`

#### 流程控制
##### if
```
if [ 条件 ]; then
	程序
fi
```
或</br>

```
if [ 条件判断 ]
	then
		程序
	elif
	else
if

# 判断数字
# 思路：将数字替换为空，如果y空则sum全是数字
y=$(echo $sum | sed 's/^[0-9]*//g')
if[ -z "$y"]
```
##### case
```
case $变量名 in
	"值1")
		...
		;;
	"值2")
		...
		;;
	*)
		...
		;;
esac
```

##### for
```
# 语法1
# 循环3次 类似python
for 变量 in 值1，值2，值3
	do
		程序
	done

[批量解压]
cd /lamp
ls *.tar.gz>ls.log
for i in $(cat ls.log)
	do
		tar -zxf $i &>/dev/null
	done
rm -rf /lamp/ls.log
```

```
# 语法2
# 类似c
for((初始值;循环控制条件;变量变化))
	do
		程序
	done

[从1加到100]
s=0
for((i=1;i<=100;i=i+1))
	do
		s=$(($s+$i))
	done

```

##### while和until
```
# 未达到条件则不再循环
while [条件]
	do
		程序
	done

```

```
# 达到条件则不再循环
untile [条件]
	do
		程序
	done
```

