## Parallel-Block-Copy-HDFS
### Introduction
In HDFS disk balancer tool, each step need to copy blocks fromm one source volume to a destination volume,the deafualt tool will execute the steps one by one, or in sequential order.This the simple and safe for each volume should not be modified concurrently.
However, when there are muti volumes, sequential execution is an extremely inefficient process.Our work is try to execute the steps in parallel, which is much efficient and safe as well.
### The Idea of Parallel
Each step contains two volumes:source and destination.If one of them is being copied data from or to, the copy of current volume pair should not be executed.However,the volume pair, whose des and source are both not in working, can be executed at the same time with other volume pairs.The design detail is showed in the following fig.
![highload](https://github.com/liumihust/gitTset/blob/master/parallel1.PNG)
### Test On Aliyun ECS
#### Init Status
```
[root@node1 hadoop]# df
Filesystem     1K-blocks     Used Available Use% Mounted on
/dev/vda1       41152832 20989356  18066376  54% /
tmpfs            4030552        0   4030552   0% /dev/shm
/dev/vdb       103081248  3185896  94652472   4% /tmp/hadoop1
/dev/vdc       103081248  2923240  94915128   3% /tmp/hadoop2
/dev/vdd       103081248    61116  97777252   1% /tmp/hadoop3
/dev/vde       103081248    61116  97777252   1% /tmp/hadoop4
```
#### Executing Stage(Parallel)
```
[root@node1 hadoop]# df
Filesystem     1K-blocks     Used Available Use% Mounted on
/dev/vda1       41152832 20990328  18065404  54% /
tmpfs            4030552        0   4030552   0% /dev/shm
/dev/vdb       103081248  2886628  94951740   3% /tmp/hadoop1
/dev/vdc       103081248  2490408  95347960   3% /tmp/hadoop2
/dev/vdd       103081248   493376  97344992   1% /tmp/hadoop3
/dev/vde       103081248   393824  97444544   1% /tmp/hadoop4

[root@node1 hadoop]# df
Filesystem     1K-blocks     Used Available Use% Mounted on
/dev/vda1       41152832 20990504  18065228  54% /
tmpfs            4030552        0   4030552   0% /dev/shm
/dev/vdb       103081248  2155992  95682376   3% /tmp/hadoop1
/dev/vdc       103081248  1724704  96113664   2% /tmp/hadoop2
/dev/vdd       103081248  1391180  96447188   2% /tmp/hadoop3
/dev/vde       103081248  1196284  96642084   2% /tmp/hadoop4
```
As we can see, these four disks is excuting in parallel
#### Result
```
[root@node1 hadoop]# df
Filesystem     1K-blocks     Used Available Use% Mounted on
/dev/vda1       41152832 20991196  18064536  54% /
tmpfs            4030552        0   4030552   0% /dev/shm
/dev/vdb       103081248  1724624  96113744   2% /tmp/hadoop1
/dev/vdc       103081248  1592604  96245764   2% /tmp/hadoop2
/dev/vdd       103081248  1390288  96448080   2% /tmp/hadoop3
/dev/vde       103081248  1522388  96315980   2% /tmp/hadoop4
```
balance and efficient!
