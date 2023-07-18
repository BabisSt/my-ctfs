#CTF 

_Identify the critical security flaw in the most powerful and trusted network monitoring software on the market, that allows an user authenticated execute remote code execution._

## Recon

```
PORT     STATE SERVICE    VERSION
22/tcp   open  ssh        OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 62:1d:d9:88:01:77:0a:52:bb:59:f9:da:c1:a6:e3:cd (RSA)
|   256 af:67:7d:24:e5:95:f4:44:72:d1:0c:39:8d:cc:21:15 (ECDSA)
|_  256 20:28:15:ef:13:c8:9f:b8:a7:0f:50:e6:2f:3b:1e:57 (ED25519)
25/tcp   open  smtp       Postfix smtpd
|_ssl-date: TLS randomness does not represent time
|_smtp-commands: ubuntu.localdomain, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN
| ssl-cert: Subject: commonName=ubuntu
| Not valid before: 2020-03-23T23:42:04
|_Not valid after:  2030-03-21T23:42:04
80/tcp   open  http       Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
389/tcp  open  ldap       OpenLDAP 2.2.X - 2.3.X
443/tcp  open  ssl/http   Apache httpd 2.4.18 ((Ubuntu))
|_ssl-date: TLS randomness does not represent time
|_http-title: Site doesn't have a title (text/html).
| tls-alpn: 
|_  http/1.1
|_http-server-header: Apache/2.4.18 (Ubuntu)
| ssl-cert: Subject: commonName=192.168.85.153/organizationName=Nagios Enterprises/stateOrProvinceName=Minnesota/countryName=US
| Not valid before: 2020-03-24T00:14:58
|_Not valid after:  2030-03-22T00:14:58
5667/tcp open  tcpwrapped
Service Info: Host:  ubuntu.localdomain; OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

- 389 : LDAP (Lightweight Directory Access Protocol) - an Internet protocol, used my MS Active Directory, as well as some email programs to look up contact information from a server.
- 5667 : NSCA (Nagios), MOHAA Reverend


```
===============================================================
2023/07/16 12:43:32 Starting gobuster in directory enumeration mode
===============================================================
/index.html           (Status: 200) [Size: 1332]
/.php                 (Status: 403) [Size: 276]
/index.php            (Status: 200) [Size: 2968]
/.html                (Status: 403) [Size: 276]
/javascript           (Status: 301) [Size: 315] [--> http://10.10.131.4/javascript/]                                                            
/nagios               (Status: 401) [Size: 458]
/.html                (Status: 403) [Size: 276]
/.php                 (Status: 403) [Size: 276]
/server-status        (Status: 403) [Size: 276]
```

The website has this

```
     ,+++77777++=:,                    +=                      ,,++=7++=,,
		    7~?7   +7I77 :,I777  I          77 7+77 7:        ,?777777??~,=+=~I7?,=77 I
		=7I7I~7  ,77: ++:~+777777 7     +77=7 =7I7     ,I777= 77,:~7 +?7, ~7   ~ 777?
		77+7I 777~,,=7~  ,::7=7: 7 77   77: 7 7 +77,7 I777~+777I=   =:,77,77  77 7,777,
		  = 7  ?7 , 7~,~  + 77 ?: :?777 +~77 77? I7777I7I7 777+77   =:, ?7   +7 777?
		      77 ~I == ~77=77777~: I,+77?  7  7:?7? ?7 7 7 77 ~I   7I,,?7 I77~
		       I 7=77~+77+?=:I+~77?     , I 7? 77 7   777~ +7 I+?7  +7~?777,77I
		         =77 77= +7 7777         ,7 7?7:,??7     +7    7   77??+ 7777,
		             =I, I 7+:77?         +7I7?7777 :             :7 7
		                7I7I?77 ~         +7:77,     ~         +7,::7   7
		               ,7~77?7? ?:         7+:77           77 :7777=
		                ?77 +I7+,7         7~  7,+7  ,?       ?7?~?777:
		                   I777=7777 ~     77 :  77 =7+,    I77  777
		                     +      ~?     , + 7    ,, ~I,  = ? ,
		                                    77:I+
		                                    ,7
		                                     :777
		                                        :
						Welcome to elements.
					Ag - Hg - Ta - Sb - Po - Pd - Hg - Pt - Lr
```

Also found this comment on the source : `<! --/nagiosxi/ -->`


I can connect to port 25 with telnet

Ok , the next part is rather bazaar and I had to google it.
Above there are the elements
`Ag - Hg - Ta - Sb - Po - Pd - Hg - Pt - Lr`
Searching these elements on the periodic table, I get these atomic numbers
`47 80 73 51 84 46 80 78 77`
If I use these number from ASCII to text I get this
`/PI3T.PNg`
I navigate to this subdomain and I get presented with and image.

The next question of the task is _Who is the creator of the file?_ so I know I have to search on the metadata for it. I use exiftool

```
ExifTool Version Number         : 12.63
File Name                       : PI3T.PNg
Directory                       : .
File Size                       : 982 kB
File Modification Date/Time     : 2023:07:17 14:50:26-04:00
File Access Date/Time           : 2023:07:17 14:50:25-04:00
File Inode Change Date/Time     : 2023:07:17 14:50:26-04:00
File Permissions                : -rw-r--r--
File Type                       : PNG
File Type Extension             : png
MIME Type                       : image/png
Image Width                     : 990
Image Height                    : 990
Bit Depth                       : 8
Color Type                      : Palette
Compression                     : Deflate/Inflate
Filter                          : Adaptive
Interlace                       : Noninterlaced
Palette                         : (Binary data 768 bytes, use -b option to extract)
Transparency                    : (Binary data 256 bytes, use -b option to extract)
Artist                          : Piet Mondrian
Copyright                       : Piet Mondrian, tryhackme 2020
Image Size                      : 990x990
Megapixels                      : 0.980
```

The next questions was _If you get an error running the tool on your downloaded image about an unknown ppm format -- open it with gimp or another paint program and export to ppm format, and try again!_

And after a bit of search I found about Piet steganography and tried to decode the image [here](http://www.bertnase.de/npiet/npiet-execute.php)
