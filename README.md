#!/bin/bash

if [ $# != 1 ]; then
mode=simulate
else
mode=$1
fi

case $mode in
simulate)
mkdir /tmp/memory
asize=`free -m|grep Mem|awk '{print $4}'`
bsize=500
val=`expr $asize - $bsize`
mount -t tmpfs -o size=${val}m tmpfs /tmp/memory
dd if=/dev/zero of=/tmp/memory/block
;;

recover)
rm -rf /tmp/memory/block
umount /tmp/memory
rmdir /tmp/memory
;;

*)
echo "USAGE: parameter should be simulate or recover"
;;
esac
