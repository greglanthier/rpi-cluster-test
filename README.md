## Code

Install the Hypriot flash tool from https://github.com/hypriot/flash.

```
curl -O https://raw.githubusercontent.com/hypriot/flash/master/$(uname -s)/flash
chmod +x flash
sudo mv flash /usr/local/bin/flash
```

Flash the SD cards...

```
flash --hostname dc1-pi01 https://github.com/hypriot/image-builder-rpi/releases/download/v1.4.0/hypriotos-rpi-v1.4.0.img.zip
...
flash --hostname dc1-piNN https://github.com/hypriot/image-builder-rpi/releases/download/v1.4.0/hypriotos-rpi-v1.4.0.img.zip
```

Setup password-less login

```
$ ssh-copy-id pirate@dc1-pi01.local
/usr/local/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/Users/greg/.ssh/id_rsa.pub"
The authenticity of host 'dc1-pi01.local (2607:fea8:bd60:88a:ba27:ebff:fe3d:1099)' can't be established.
ECDSA key fingerprint is SHA256:V6fWwtRe/QeGRMqikf2//uBoyoOqExsUOMo/d2h0gx8.
Are you sure you want to continue connecting (yes/no)? yes
/usr/local/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/local/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
pirate@dc1-pi01.local's password: <hypriot>

Number of key(s) added:        1

Now try logging into the machine, with:   "ssh 'pirate@dc1-pi01.local'"
and check to make sure that only the key(s) you wanted were added.

$ ssh pirate@dc1-pi01.local

HypriotOS (Debian GNU/Linux 8)

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
HypriotOS/armv7: pirate@dc1-pi01.local in ~
$ logout
Connection to dc1-pi01.local closed.
$
```

Note that if you've re-imaged one of the nodes in the cluster you may need to remove
stale entries from your ~/.ssh/known_hosts file.

See `ssh-keygen -R hostname [-f known_hosts_file]`.

Repeat for each node.

Confirm that the ssh keys have been setup properly.

```
$ cat hosts.txt
dc1-pi01.local
dc1-pi02.local
dc1-pi03.local
dc1-pi04.local
dc1-pi05.local

$ pssh -h hosts.txt -l pirate -t 0 hostname
[1] 21:34:15 [SUCCESS] dc1-pi05.local
[2] 21:34:15 [SUCCESS] dc1-pi02.local
[3] 21:34:15 [SUCCESS] dc1-pi04.local
[4] 21:34:15 [SUCCESS] dc1-pi03.local
[5] 21:34:15 [SUCCESS] dc1-pi01.local
$
```

Perform 'apt-get update' on all the nodes.

```
$ pssh -h hosts.txt -l pirate -t 0 sudo apt-get update
[1] 21:37:38 [SUCCESS] dc1-pi03.local
[2] 21:37:38 [SUCCESS] dc1-pi05.local
[3] 21:37:38 [SUCCESS] dc1-pi04.local
[4] 21:37:40 [SUCCESS] dc1-pi01.local
[5] 21:38:02 [SUCCESS] dc1-pi02.local
$
```

Maybe we should use avahi to generate dndmasq.conf files on each slave...

```
$ sudo apt-get install avahi-utils
$ avahi-browse -rpt _workstation._tcp
+;docker0;IPv4;dc1-pi05\032\091de\058e9\05817\058d3\05887\05886\093;Workstation;local
+;eth0;IPv6;dc1-pi01\032\091b8\05827\058eb\0583d\05810\05899\093;Workstation;local
+;eth0;IPv6;dc1-pi04\032\091b8\05827\058eb\058a5\0588f\058d2\093;Workstation;local
+;eth0;IPv6;dc1-pi02\032\091b8\05827\058eb\05871\0585d\058c2\093;Workstation;local
+;eth0;IPv6;dc1-pi03\032\091b8\05827\058eb\05824\058d5\0586d\093;Workstation;local
+;eth0;IPv6;dc1-pi05\032\091b8\05827\058eb\05886\0584e\058d7\093;Workstation;local
+;eth0;IPv4;dc1-pi04\032\091b8\05827\058eb\058a5\0588f\058d2\093;Workstation;local
+;eth0;IPv4;dc1-pi01\032\091b8\05827\058eb\0583d\05810\05899\093;Workstation;local
+;eth0;IPv4;dc1-pi02\032\091b8\05827\058eb\05871\0585d\058c2\093;Workstation;local
+;eth0;IPv4;dc1-pi03\032\091b8\05827\058eb\05824\058d5\0586d\093;Workstation;local
+;eth0;IPv4;dc1-pi05\032\091b8\05827\058eb\05886\0584e\058d7\093;Workstation;local
=;eth0;IPv6;dc1-pi04\032\091b8\05827\058eb\058a5\0588f\058d2\093;Workstation;local;dc1-pi04.local;fd00:bc4d:fb76:74f2:ba27:ebff:fea5:8fd2;9;
=;eth0;IPv4;dc1-pi04\032\091b8\05827\058eb\058a5\0588f\058d2\093;Workstation;local;dc1-pi04.local;192.168.0.21;9;
=;eth0;IPv6;dc1-pi05\032\091b8\05827\058eb\05886\0584e\058d7\093;Workstation;local;dc1-pi05.local;fd00:bc4d:fb76:74f2:ba27:ebff:fe86:4ed7;9;
=;docker0;IPv4;dc1-pi05\032\091de\058e9\05817\058d3\05887\05886\093;Workstation;local;dc1-pi05.local;172.17.0.1;9;
=;eth0;IPv4;dc1-pi05\032\091b8\05827\058eb\05886\0584e\058d7\093;Workstation;local;dc1-pi05.local;192.168.0.23;9;
=;eth0;IPv6;dc1-pi03\032\091b8\05827\058eb\05824\058d5\0586d\093;Workstation;local;dc1-pi03.local;fd00:bc4d:fb76:74f2:ba27:ebff:fe24:d56d;9;
=;eth0;IPv4;dc1-pi03\032\091b8\05827\058eb\05824\058d5\0586d\093;Workstation;local;dc1-pi03.local;192.168.0.22;9;
=;eth0;IPv6;dc1-pi02\032\091b8\05827\058eb\05871\0585d\058c2\093;Workstation;local;dc1-pi02.local;2607:fea8:bd60:88a:ba27:ebff:fe71:5dc2;9;
=;eth0;IPv4;dc1-pi02\032\091b8\05827\058eb\05871\0585d\058c2\093;Workstation;local;dc1-pi02.local;192.168.0.19;9;
=;eth0;IPv6;dc1-pi01\032\091b8\05827\058eb\0583d\05810\05899\093;Workstation;local;dc1-pi01.local;2607:fea8:bd60:88a:ba27:ebff:fe3d:1099;9;
=;eth0;IPv4;dc1-pi01\032\091b8\05827\058eb\0583d\05810\05899\093;Workstation;local;dc1-pi01.local;192.168.0.20;9;
$

```

```
$ sudo apt-get install dnsutils
$ dig dc1-pi05
```

```
$ avahi-browse -rpt _workstation._tcp | sort | awk -F';' '$1 == "=" && $2 == "eth0" {printf("%s %s\n", $4, $8)}'
dc1-pi01\032\091b8\05827\058eb\0583d\05810\05899\093 192.168.0.20
dc1-pi02\032\091b8\05827\058eb\05871\0585d\058c2\093 192.168.0.19
dc1-pi03\032\091b8\05827\058eb\05824\058d5\0586d\093 192.168.0.22
dc1-pi04\032\091b8\05827\058eb\058a5\0588f\058d2\093 192.168.0.21
dc1-pi05\032\091b8\05827\058eb\05886\0584e\058d7\093 192.168.0.23
dc1-pi01\032\091b8\05827\058eb\0583d\05810\05899\093 2607:fea8:bd60:88a:ba27:ebff:fe3d:1099
dc1-pi02\032\091b8\05827\058eb\05871\0585d\058c2\093 2607:fea8:bd60:88a:ba27:ebff:fe71:5dc2
dc1-pi03\032\091b8\05827\058eb\05824\058d5\0586d\093 fd00:bc4d:fb76:74f2:ba27:ebff:fe24:d56d
dc1-pi04\032\091b8\05827\058eb\058a5\0588f\058d2\093 fd00:bc4d:fb76:74f2:ba27:ebff:fea5:8fd2
dc1-pi05\032\091b8\05827\058eb\05886\0584e\058d7\093 fd00:bc4d:fb76:74f2:ba27:ebff:fe86:4ed7
$
```

```
$ sudo apt-get install resolvconf dnsmasq avahi-utils
$
```
