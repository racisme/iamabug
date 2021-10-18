#!/bin/bash

mode=$1
value=$2

if [ -n $value ] && [[ ! $value =~ ^[0-9]*$ ]]
then
echo "USAGE: iamabug.sh mode(cpu|mem|cpu_rec|mem_rec) value"
exit 1
fi

case $mode in
mem)
mkdir /tmp/memory
asize=`free -m|grep Mem|awk '{print $4}'`
bsize=500
val=`expr $asize - $bsize`
mount -t tmpfs -o size=${val}m tmpfs /tmp/memory
dd if=/dev/zero of=/tmp/memory/block
;;

cpu)
if [ $# -lt 2 ]; then
value=80
fi
cpu=`vmstat 1 2|awk '{print $15}'|sed -n 4p`
while [ $cpu -lt $value ];
do
nohup echo "scale=99999999; 4*a(1)" | bc -lq >/dev/null &
sleep 5
cpu=`vmstat 1 2|awk '{print $15}'|sed -n 4p`
done
;;

mem_rec)
rm -rf /tmp/memory/block
umount /tmp/memory
rmdir /tmp/memory
;;

cpu_rec)
pkill -9 bc
;;

*)
echo "USAGE: iamabug.sh mode(cpu|mem|cpu_rec|mem_rec) value"
;;
esac
