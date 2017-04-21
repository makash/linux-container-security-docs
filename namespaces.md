### What are the Linux Namespaces?

> The kernel provides process isolation by creating separate namespaces for containers. Namespaces enable creating an abstraction of a particular global system resource and make it appear as a separated instance to processes within a namespace. Consequently, several containers can use the same resource simultaneously without creating a conflict.


### Some of the namespaces in Linux
- Mount namespaces
- UTS namespaces (UNIX Timesharing System)
- IPC namespaces (Interprocess Communication)
- PID namespaces (Process Identification)
- Network namespaces

Note: Namespaces are a feature of the Linux kernel that isolates and virtualizes system resources of a collection of processes. (wikipedia)


> There is another type of namespace called user namespace. User namespaces are similar to PID namespaces, they allow you to specify a range of host UIDs dedicated to the container. Consequently, a process can have full root privileges for operations inside the container, and at the same time be unprivileged for operations outside the container.


### Sample overview demo

```
$ docker run -d alpine sleep 1000
$ sudo ls /proc/[pid]/ns/
```
