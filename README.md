## Code

Install the Hypriot flash tool from https://github.com/hypriot/flash.

```
curl -O https://raw.githubusercontent.com/hypriot/flash/master/$(uname -s)/flash
chmod +x flash
sudo mv flash /usr/local/bin/flash
```

Flash the SD cards...

```
flash --hostname dc1-pi01.local https://github.com/hypriot/image-builder-rpi/releases/download/v1.1.3/hypriotos-rpi-v1.1.3.img.zip
...
flash --hostname dc1-piNN.local https://github.com/hypriot/image-builder-rpi/releases/download/v1.1.3/hypriotos-rpi-v1.1.3.img.zip
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
