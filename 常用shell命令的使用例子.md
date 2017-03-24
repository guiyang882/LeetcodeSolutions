## 查看系统
```bash
➜  ~ uname -mprsv
Darwin 16.4.0 Darwin Kernel Version 16.4.0: Thu Dec 22 22:53:21 PST 2016; root:xnu-3789.41.3~3/RELEASE_X86_64 x86_64 i386
➜  ~ uname -p
i386
➜  ~ uname -m
x86_64
➜  ~ uname -r
16.4.0
➜  ~ uname -s
Darwin
➜  ~ uname -v
Darwin Kernel Version 16.4.0: Thu Dec 22 22:53:21 PST 2016; root:xnu-3789.41.3~3/RELEASE_X86_64
```
## Linux 中网络流量查看工具
```
系统运行环境
centos 7.x
Linux 3.10.0-327.36.3.el7.x86_64 #1 UTC 2016 X86_64 x86_64
```

```bash
yum install iptraf-ng
sudo iptraf-ng
```

```bash
watch more /proc/net/dev

#!/bin/bash

usage() {
        echo "Useage : $0"
        echo "eg. sh $0 eth0 2"
        exit 1
}

if [ $# -lt 2 ]
then
        usage
fi

eth=$1
timer=$2

in_old=$(cat /proc/net/dev | grep $eth | sed -e "s/\(.*\)\:\(.*\)/\2/g" | awk '{print $1 }')
out_old=$(cat /proc/net/dev | grep $eth | sed -e "s/\(.*\)\:\(.*\)/\2/g" | awk '{print $9 }')

while true
do
        sleep ${timer}
        in=$(cat /proc/net/dev | grep $eth | sed -e "s/\(.*\)\:\(.*\)/\2/g" | awk '{print $1 }')
        out=$(cat /proc/net/dev | grep $eth | sed -e "s/\(.*\)\:\(.*\)/\2/g" | awk '{print $9 }')
        dif_in=$(((in-in_old)/timer))
        dif_in=$((dif_in/1024))
        dif_out=$(((out-out_old)/timer))
        dif_out=$((dif_out/1024))
        ct=$(date +"%F %H:%M:%S")
        echo "${ct} -- IN: ${dif_in} KByte/s     OUT: ${dif_out} KByte/s"
        in_old=${in}
        out_old=${out}
done

exit 0
```

```bash
对于上述脚本使用运行命令，
./x.sh eth0 5

fighter@pc:~$ ./a.sh eno1 5
2017-02-28 09:06:33 -- IN: 0 KByte/s     OUT: 0 KByte/s
2017-02-28 09:06:38 -- IN: 0 KByte/s     OUT: 0 KByte/s
2017-02-28 09:06:43 -- IN: 0 KByte/s     OUT: 0 KByte/s
2017-02-28 09:06:48 -- IN: 0 KByte/s     OUT: 0 KByte/s
2017-02-28 09:06:53 -- IN: 0 KByte/s     OUT: 0 KByte/s
2017-02-28 09:06:58 -- IN: 0 KByte/s     OUT: 0 KByte/s
2017-02-28 09:07:03 -- IN: 0 KByte/s     OUT: 0 KByte/s
2017-02-28 09:07:08 -- IN: 0 KByte/s     OUT: 0 KByte/s
2017-02-28 09:07:13 -- IN: 1 KByte/s     OUT: 0 KByte/s
2017-02-28 09:07:18 -- IN: 0 KByte/s     OUT: 0 KByte/s
可以使用wget下载一个大文件进行测试
```

也可以针对linux中的具体文件进行统计，其中可以直接将网卡接受和发送的数据从文件中直接读出

```bash
#!/bin/bash
 
INTERVAL="1"  # update interval in seconds
 
if [ -z "$1" ]; then
        echo
        echo usage: $0 [network-interface]
        echo
        echo e.g. $0 eth0
        echo
        exit
fi
 
IF=$1
 
while true
do
        R1=`cat /sys/class/net/$1/statistics/rx_bytes`
        T1=`cat /sys/class/net/$1/statistics/tx_bytes`
        sleep $INTERVAL
        R2=`cat /sys/class/net/$1/statistics/rx_bytes`
        T2=`cat /sys/class/net/$1/statistics/tx_bytes`
        TBPS=`expr $T2 - $T1`
        RBPS=`expr $R2 - $R1`
        TKBPS=`expr $TBPS / 1024`
        RKBPS=`expr $RBPS / 1024`
        echo "TX $1: $TKBPS kB/s RX $1: $RKBPS kB/s"
done
```

```bash
fighter@pc:~$ ./a.sh eno1
TX eno1: 0 kB/s RX eno1: 0 kB/s
TX eno1: 0 kB/s RX eno1: 0 kB/s
TX eno1: 0 kB/s RX eno1: 0 kB/s
TX eno1: 0 kB/s RX eno1: 0 kB/s
TX eno1: 0 kB/s RX eno1: 1 kB/s
```

## Linux系统中文件资源相关命令的使用
```bash
系统设置命令  ulimit
语　　法：
ulimit [-aHS][-c <core文件上 限>][-d <数据节区大小>][-f <文件大小>][-m <内存大小>][-n <文件数 目>][-p <缓冲区大小>][-s <堆叠大小>][-t <CPU时间>][-u <程序数 目>][-v <虚拟内存大小>]

fighter@pc:~$ ulimit -a
core file size          (blocks, -c) 0
data seg size           (kbytes, -d) unlimited
scheduling priority             (-e) 0
file size               (blocks, -f) unlimited
pending signals                 (-i) 31427
max locked memory       (kbytes, -l) 64
max memory size         (kbytes, -m) unlimited
open files                      (-n) 1024
pipe size            (512 bytes, -p) 8
POSIX message queues     (bytes, -q) 819200
real-time priority              (-r) 0
stack size              (kbytes, -s) 8192
cpu time               (seconds, -t) unlimited
max user processes              (-u) 31427
virtual memory          (kbytes, -v) unlimited
file locks                      (-x) unlimited
```

```bash
fighter@pc:~$ lsof | more
lsof: WARNING: can't stat() tracefs file system /sys/kernel/debug/tracing
      Output information may be incomplete.
lsof: WARNING: can't stat() fuse.gvfsd-fuse file system /run/user/120/gvfs
      Output information may be incomplete.
lsof: WARNING: can't stat() fuse.gvfsd-fuse file system /run/user/1000/gvfs
      Output information may be incomplete.
lsof: WARNING: can't stat() fuseblk file system /media/duc/新加卷
      Output information may be incomplete.
lsof: WARNING: can't stat() fuseblk file system /media/duc/Data02
      Output information may be incomplete.
lsof: WARNING: can't stat() udf file system /media/duc/01 04 2017
      Output information may be incomplete.
lsof: WARNING: can't stat() tmpfs file system /var/lib/docker/containers/f2584abbaf1e3d73c19048cb57c0a8597bde76cbd9154b7e54e24665d8204717/shm
      Output information may be incomplete.
lsof: WARNING: can't stat() nsfs file system /run/docker/netns/b33bca4eb633
      Output information may be incomplete.
COMMAND     PID   TID             USER   FD      TYPE             DEVICE SIZE/OFF       NODE NAME
systemd       1                   root  cwd   unknown                                        /proc/1/cwd (readlink: Permission denied)
systemd       1                   root  rtd   unknown                                        /proc/1/root (readlink: Permission denied)
systemd       1                   root  txt   unknown                                        /proc/1/exe (readlink: Permission denied)
systemd       1                   root NOFD                                                  /proc/1/fd (opendir: Permission denied)
kthreadd      2                   root  cwd   unknown                                        /proc/2/cwd (readlink: Permission denied)
kthreadd      2                   root  rtd   unknown                                        /proc/2/root (readlink: 
```

```bash
可以通过lsof | wc -l 指令进行产看系统中所有的文件使用的数量
通过 lsof -p pid | wc -l
查看某个进程使用的文件资源的数量
```
## netstat 日常命令
```bash
netstat -r route
netstat -i ifconfig
netstat -t tcp socket
netstat -u udp socket
netstat -a all running and monitoring socket
fighter@pc:~$ netstat -i
Kernel Interface table
Iface   MTU Met   RX-OK RX-ERR RX-DRP RX-OVR    TX-OK TX-ERR TX-DRP TX-OVR Flg
br-b7e81db96382  1500 0         0      0      0 0             0      0      0      0 BMU
docker0    1500 0    728239      0      0 0        987989      0      0      0 BMRU
eno1       1500 0   5437905   1212      0 0       1931248      0      0      0 BMRU
lo        65536 0  1179786874      0      0 0      1179786874      0      0      0 LRU
vetha91e02b  1500 0    152293      0      0 0        226785      0      0      0 BMRU
fighter@pc:~$ netstat -r
Kernel IP routing table
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
default         Hiwifi.lan      0.0.0.0         UG        0 0          0 eno1
link-local      *               255.255.0.0     U         0 0          0 eno1
172.17.0.0      *               255.255.0.0     U         0 0          0 docker0
172.18.0.0      *               255.255.0.0     U         0 0          0 br-b7e81db96382
192.168.199.0   *               255.255.255.0   U         0 0          0 eno1
```

## how to fix the problem about the LC_LOCAL
```bash
## 安裝語系檔
$ sudo locale-gen "en_US.UTF-8"
## 重新設定語系檔
$ sudo dpkg-reconfigure locales
## 設定檔
$ vim /etc/default/locale
加入
LC_ALL="en_US.UTF-8"
然後我就會重新啟動 server，基本上就不會有錯誤了
```

## iostat/top
```bash
apt-get install sysstat
iostat -d /dev/sda7/ {-m-k-g}
watch -n 1 iostat -d /dev/sda7/ -m-k-g

top -p pidNum
watch -n 1 top -p 123456
```
