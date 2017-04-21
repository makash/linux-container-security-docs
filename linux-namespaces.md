### What are the Linux name spaces?

#### PID namespace

 PID namespaces isolate the process ID number space, meaning that processes in different PID namespaces can have the same PID.  PID namespaces allow containers to provide functionality such as suspending/resuming the set of processes in the container and migrating the container to a new host while the processes inside the container maintain the same PIDs.

For example, while running nginx docker container inside container we always see nginx PID 1 but outside host we see different PID like `9989`

```
$ docker run --name=samplewebapp1 -d nginx:alpine
$ ps auxxx | grep nginx
$ docker exec -it samplewebapp1 sh
# top

$ docker run --name=samplewebapp2 -d nginx:alpine
$ ps auxxx | grep nginx
$ docker exec -it samplewebapp2 sh
# top
```


#### Mount namespace

> Mount namespaces provide isolation of the list of mount points seen by the processes in each namespace instance.  Thus, the processes in each of the mount namespace instances will see distinct single-directory hierarchies.

For example, in docker we use `-v` option to volume mount the directories from host system to container.

```
$ docker run --name=sampleapp1 -d alpine sleep 1000
$ sudo cat /proc/[PID]/mountstats
$ docker exec -it sampleapp1 sh
# ls /data/

$ mkdir testmount
$ touch testmount/samplefile.txt
$ docker run --name=sampleapp2 -d -v testmount:/data alpine sleep 1000
$ sudo cat /proc/[PID]/mountstats
$ docker exec -it sampleapp2 sh
# ls /data/
```


#### UTS namespace

UTS namespaces provide isolation of two system identifiers: the hostname and the NIS domain name.

For example, we can see these by running the below commands

```
$ hostname

$ uname


$ docker run -it alpine sh
# hostname

# uname
```


#### Network namespace

Network namespace provide isolation of network controllers, system resources associated with networking, firewall and routing tables. This allows container to use separate virtual network stack, loopback device and process space. You can add virtual or real devices to the container, assign them their own IP Addresses and even full iptables rules. 

For example, we can view the different network settings by executing the `ip addr` command on the host and inside the container.

```
$ ip addr

$ docker run --name=samplenetapp -d alpine sleep 1000
$ ps auxxx | grep sleep
$ sudo ls /proc/[pid]/net/
$ docker exec -it samplenetapp sh
# ip addr
```


#### IPC namespace

Each IPC namespace has its own set of System V IPC identifiers and its own POSIX message queue filesystem.  Objects created in an IPC namespace are visible to all other processes that are members of that namespace, but are not visible to processes in other IPC namespaces.

For example, check the `/proc/sysvipc` in system to see the (sem, shm, msg)


#### User namespace

User namespaces isolate security-related identifiers and attributes, in particular, user IDs and group IDs, the root directory, keys, and capabilities.

In particular, a process can have a normal unprivileged user ID outside a user namespace while at the same time having a user ID of 0 inside the namespace; in other words, the process has full privileges for operations inside the user namespace, but is unprivileged for operations outside the namespace.