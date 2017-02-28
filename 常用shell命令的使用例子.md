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
