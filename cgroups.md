### What are Cgroups

The kernel uses cgroups to group processes for the purpose of system resource management. Cgroups allocate CPU time, system memory, network bandwidth, or combinations of these among user-defined groups of tasks.


```
$ docker run -d --name='low_priority' --cpuset-cpus=0 --cpu-shares=20 alpine md5sum /dev/urandom
$ docker run -d --name='high_priority' --cpuset-cpus=0 --cpu-shares=80 alpine md5sum /dev/urandom
$ htop

$ docker stop low_priority high_priority
$ docker rm low_priority high_priority

$ docker run -d --name='low_priority' alpine md5sum /dev/urandom
$ docker run -d --name='high_priority' alpine md5sum /dev/urandom
$ htop
```
