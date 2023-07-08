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
Next I find this `http://internal.thm/blog/wp-login.php`, which is a similar to [[CTF { Mr Robot }]] log-in form. So I try some default combinations and I get the required error on username:admin
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