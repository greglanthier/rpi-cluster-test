```
$ ssh pirate@dc1-pi01.local
dc1-pi01$ sudo apt install -y --force-yes apt-cacher-ng
```

From there it looks like I need to create an apt.conf file on each of the nodes that points to the mirror.

```
ssh pirate@dc1-pi02.local
dc1-pi02$ cat /etc/apt/apt.conf
Acquire::http::Proxy "http://dc1-pi01:3142";
$
```

It looks like I'll need to do some configuration to apt-cacher-ng.

See https://github.com/moby/moby/issues/9592#issuecomment-77310051 and https://blog.packagecloud.io/eng/2015/05/05/using-apt-cacher-ng-with-ssl-tls/

```
Or even better. Follow the notes in the apt-cacher-ng config file that show how SSL can be passed through.

In your apt-cacher-ng config file (acng.conf) add the following line:

PassThroughPattern: .*
```

Apparently we'd need some dirty tricks to cache artifacts from ssl repos.

```
$ sudo apt update
...
$ sudo apt upgrade -y --force-yes
...
$
```
