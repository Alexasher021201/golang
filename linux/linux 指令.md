### 文件目录操作

**ls**

- ls 路径	展示该目录的内容，为空时认为是当前目录
- ls -l 路径     -l 为展示详细信息，包括文件创建者，创建时间，权限的等信息
- ls -a 路径    -a 为显示隐藏文件

**cd**

- cd 路径      切换路径
- cd ～         切换到用户家路径
- cd /            切换到用户根目录

**pwd**

- pwd           打印绝对路径

**mkdir**

- mkdir 	创建文件夹

**touch**

- touch file.txt    创建空文件

**mv**

- mv old.txt new.txt	重命名文件
- mv *.png 路径                将所有.png 格式的文件，移动到某路径下

**cp**

- cp -r source_dir/ dest_dir/       -r：递归复制    递归复制所有目录，包括父目录子目录
- cp file.txt dest_dir/                    将 file 复制到目标文件夹中

**rm**

- rm -rf tmp/                -rf：强制（慎用）     强制删除目录
- rm -i *.tmp                -i：删除前确认            交互式删除

**find**

- find /home -name "*.txt" -type f        在 /home 下查找所有后缀为.txt 的文件

### 文件内容操作

**cat**

- cat file.txt              显示file 的内容
- cat file1 file2 > merged.txt              将 file1 和 file2 的内容合并到 merged.txt 中
- cat file.txt | grep "abc"            显示 file.txt 中包含 abc 内容的行

**less**

- less /var/log/syslog           分页查看文件，支持上下滚动

**head**

- head -n 20 file.txt             显示 file 的前 20 行，-n xxx 表示显示 前xxx 行
- head file.txt                       显示 file 的前10行（默认）

**tail**

- tail -f app.log		-f  实时追踪文件 ； 这里默认显示文件尾部 10 行
- tail -n 100 -f debug.log          显示文件尾部的 100 行

**grep**

- grep -i "error" /var/log/*          -i：不区分大小写，查找/var/log 下所有文件中包含"error"字段的行
- grep -r "error" /var/log            -r：递归搜索某个目录下所有包含“errror”字段的文件的行

**sort**

- sort -r filename.txt.   		反向排序文件
- sort -f filename.txt                    忽略大小写排序

**ln**（创建链接link）

- ln source_file hard_link_name        创建一个名为 hard_link_name 的硬链接，指向 source_file 文件，直接指向文件数据，删除原文件不影响 hardlink
- ln -s source_file symbolic_link_name            这会创建一个名为 `symbolic_link_name` 的软链接，指向 `source_file` 文件，软链接相当于指向原始路径的快捷方式，删除源文件会使其失效

**ldd**

- ldd executable                显示可执行文件的共享库依赖关系(只显示动态库，无法显示静态库)，executable 表示可执行文件的路径
  - eg. ldd /usr/bin/python3

### 系统监控与管理

**top**

- Top                 实时进程监控，动态刷新界面

**ps**

- ps aux             aux：显示所有进程
- ps aux | grep “alex”      筛选出包含 alex 字段的进程行
- ps -ef --forest              显示进程树

**free**

- free -h               -h：自动转化单位，变成人类可读形式

**df**

- df                        显示所有磁盘情况
- df -h                    显示所有磁盘情况并已人类可读形式展示
- df -hT                  在上一个的基础上，多显示一栏文件系统类型信息

- df -hT /home       显示/home 分区类型及空间，-T：显示文件系统类型

**du**（目录空间分析）

- du -sh * | sort -h         -s：总计，-sh：总计并以人类可读展示，*表示当前目录下的所有文件和目录

**netstat**

- netstat -tuln ｜ grep :80 	监听 80 端口进程，查看该端口 tcp，udp，监听状态信息。-t：TCP，-u：UDP，-l：监听状态

### 网络操作

**ping**

- Ping -c 5 google.com	向 google.com 发送 5 个包来检验网络连通性。-c 指定次数

**curl**

- curl -o file.zip

**wget**

- wget -c http://example.com/big.iso (断点续传)  	-c：继续中断的下载
- wget -r ftp://files.com/ (递归下载目录)

**ssh**

- ssh user@192.168.1.100
- ssh -p 2222 user@host

**scp**

- scp file.txt user@remote:/path/           将 file.txt 上传到 remote:/path/的路径下

**ip**

- ip addr show		显示网络接口名称，如 eth0，wlan0，lo（本地回环接口）等。显示分配给每个网络接口的 IPv4 或 IPv6 地址。显示子网掩码，广播地址，MAC 地址，网络接口状态等

### 权限与用户管理

**chmod**

- chmod u+x script.sh	给文件所有者添加执行权限
- chmod 755 dir/          （rwxr-xr-x）
  - 数字模式：7=rwx，6=rw-，5=r-x，4=r--，3=-wx，2=-w-，1=--x
  - 左到右对应，读写执行

**chown**

- chown user file.txt	更改文件的所有者

- chown user:group file.txt       更改文件的所有者和组
- chown -R username /path/to/directory        递归更改目录的所有者
- chown :group /path/to/directory           仅更改目录的组

**sudo**

- sudo <command>        以超级用户权限运行命令

**passwd**

- passwd              修改当前用户密码
- sudo passwd root               修改 root 用户密码

**useradd**

**usermod**

### 压缩与解压

**tar**（.tar 格式文件）

- tar -cvf out.tar dir/           压缩命令，-c：创建，-x：解压，-v：显示过程，-f：指定归档文件的名称
- tar -xvf file.tar                   解压命令

**gz**（.gz 格式文件）

- gzip file			压缩命令
- gunzip file.gz              解压命令

**tar.gz**

- tar -zcvf out.tar.gz dir/	压缩命令，-z 处理 gzip 压缩
- tar -zxvf file.tar.gz                解压命令

**zip**

- zip -r out.zip dir/       压缩，-r：递归压缩目录
- unzip file.zip              解压

### 进程与系统控制

**kill**（向进程发送信号，通常为终止信号）

- kill pid -2               向某个 pid 发送信号 2
  - `SIGABRT`（信号 6）：异常终止，通常由 `abort` 函数触发。
  - `SIGALRM`（信号 14）：计时器信号，用于闹钟。
  - `SIGBUS`（信号 7）：总线错误，如访问无效内存。
  - `SIGCHLD`（信号 17）：子进程结束或停止。
  - `SIGCONT`（信号 18）：继续执行，用于继续被停止的进程。
  - `SIGFPE`（信号 8）：浮点异常，如除以零。
  - `SIGHUP`（信号 1）：挂起信号，通常用于重启服务。
  - `SIGILL`（信号 4）：非法指令。
  - `SIGINT`（信号 2）：中断信号，通常由 Ctrl+C 触发。
  - `SIGKILL`（信号 9）：杀死进程，无法被捕获或忽略。
  - `SIGPIPE`（信号 13）：管道破裂，如写入已关闭的管道。
  - `SIGQUIT`（信号 3）：退出信号，通常由 Ctrl+\ 触发。
  - `SIGSEGV`（信号 11）：段错误，如访问无效内存。
  - `SIGSTOP`（信号 19）：停止执行，无法被进程捕获、阻塞或忽略。
  - `SIGTERM`（信号 15）：终止信号，可以被进程捕获或忽略，用于请求进程正常退出。
  - `SIGTSTP`（信号 20）：停止信号，通常由 Ctrl+Z 触发。
  - `SIGTTIN`（信号 21）：后台进程试图读终端。
  - `SIGTTOU`（信号 22）：后台进程试图写终端。
  - `SIGUSR1` 和 `SIGUSR2`（信号 10 和 12）：用户定义的信号，可以用于任何目的。

**systemctl**

- systemctl start/stop/restart/kill servicename.service     	启动/停止/重启/杀死服务
- systemctl reboot        重启系统
- systemctl status servicename.service          查看服务状态