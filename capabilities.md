### What is capabilities?

> Capabilities turn the binary “root/non-root” dichotomy into a fine-grained access control system. Processes (like web servers) that just need to bind on a port below 1024 do not have to run as root: they can just be granted the `net_bind_service` capability instead. And there are many other capabilities, for almost all the specific areas where root privileges are usually needed.

Starting with kernel 2.2, Linux divides the privileges traditionally associated with superuser into distinct units, known as capabilities, which can be independently enabled and disabled.

For exmaple, if you run a container that run ntpd, which needs to modify the host system time. The container would not run because it requires `SYS_TIME`. 


### Scenario Demo 

If you specify the --privileged=true option to docker create or docker run, the container has access to all the devices on the host, which can present a security risk. For more precise control, you can use the `--cap-add` and `--cap-drop` options

```
$ docker run -it alpine sh
# ip route
# ip route del default

$ docker run --cap-add=NET_ADMIN -it alpine sh
# ip route
# ip route del default
```
