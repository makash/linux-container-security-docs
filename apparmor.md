### What is apparmor?

AppArmor is a Linux kernel security module that allows system administrator to restrict programs' capabilities with per-program profiles. Profiles can allow capabilities like network access, raw socket access, and the permission to read, write, or execute files on matching paths. (wikipedia)

Note: AppArmor supplements the traditional Unix discretionary access control (DAC) model by providing mandatory access control (MAC).


### AppArmor Modes

AppArmor works in two different modes

***Enforcement***

In the enforce mode, system begins enforcing the rules and report the violation attempts in syslog or auditd (only if auditd is installed) and operation will not be permitted.

***Complain***

In the complain mode, system doesn’t enforce any rules. It will only log the violation attempts.


### Scenario Demo

- Create two directories for static content

```
$ sudo mkdir -p /data/www/safe
$ sudo mkdir -p /data/www/unsafe
```

- Add a file with below content `sudo vi /data/www/safe/index.html`

```
<html>
    <b>Hello! Accessing this file is allowed.</b>
</html>
```

- Add a file with below content `sudo vi /data/www/unsafe/index.html`

```
<html>
    <b>Hello! Accessing this file is NOT allowed. It has secrets</b>
</html>
```

- Now let's configure nginx to serve the static content `sudo vi /etc/nginx/nginx.conf`

```
user www-data;
worker_processes 4;
pid /run/nginx.pid;

events {
    worker_connections 768;
}

http {
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;

    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;

    gzip on;
    gzip_disable "msie6";

    include /etc/nginx/conf.d/*.conf;

    server {
        listen 8080;
        location / { 
                root /data/www;
        }
    }
}
```

- Save and update the changes by running the below command

```
$ sudo nginx -s reload
```

- Now let's configure apparmor

```
$ cd /etc/apparmor.d/
$ sudo aa-autodep nginx
```

- The `aa-autodep` command to create a new blank profile. The profile will be created in `/etc/apparmor.d`.

- Once the profile is created, use aa-complain to put the profile in complain mode.

```
$ sudo aa-complain nginx
```

- Restart nginx service

```
$ sudo service nginx restart
```

- Open a browser, and visit `http://<server-IP>:8080/safe/index.html`. This will trigger the normal entries for accessing the safe website to appear in your Nginx logs.

- Now we'll use an AppArmor utility to go through the Nginx logs and approve or disapprove each action it finds there.

```
$ sudo aa-logprof
```

- Select which has to allow and which has to deny and save
- You can also edit the profile by `sudo vi /etc/apparmor.d/usr.sbin.nginx`

```
#include <tunables/global>

/usr/sbin/nginx {
  #include <abstractions/apache2-common>
  #include <abstractions/base>
  #include <abstractions/nis>

  capability setgid,
  capability setuid,

  /data/www/safe/* r,
  deny /data/www/unsafe/* r,
  /etc/group r,
  /etc/nginx/conf.d/ r,
  /etc/nginx/mime.types r,
  /etc/nginx/nginx.conf r,
  /etc/nsswitch.conf r,
  /etc/passwd r,
  /etc/ssl/openssl.cnf r,
  /run/nginx.pid rw,
  /usr/sbin/nginx mr,
  /var/log/nginx/access.log w,
  /var/log/nginx/error.log w,
}
```

- The AppArmor Nginx profile is ready. Use the aa-enforce to put the profile in enforce mode.

```
$ sudo aa-enforce nginx
```

- To apply the change restart the services

```
$ sudo /etc/init.d/apparmor reload
$ sudo service nginx restart
```

- You can check the apparmor status by running

```
$ sudo apparmor_status
```

- Go back to the browser and visit `http://<server-IP>:8080/safe/index.html`. You should be able to see the page. Then visit `http://<server-IP>:8080/unsafe/index.html`. 
- You can see the error logs

```
$ sudo tail -f /var/log/nginx/error.log
```

- Check out references and resource section for more. 