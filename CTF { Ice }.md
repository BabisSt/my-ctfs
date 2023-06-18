#CTF #Windows 

## Recon

```
PORT      STATE SERVICE       VERSION
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds  Windows 7 Professional 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
3389/tcp  open  ms-wbt-server Microsoft Terminal Service
| ssl-cert: Subject: commonName=Dark-PC
| Not valid before: 2023-06-03T18:02:45
|_Not valid after:  2023-12-03T18:02:45
|_ssl-date: 2023-06-04T18:26:16+00:00; -1s from scanner time.
5357/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Service Unavailable
8000/tcp  open  http          Icecast streaming media server
|_http-title: Site doesn't have a title (text/html).
49152/tcp open  msrpc         Microsoft Windows RPC
49153/tcp open  msrpc         Microsoft Windows RPC
49154/tcp open  msrpc         Microsoft Windows RPC
49158/tcp open  msrpc         Microsoft Windows RPC
49159/tcp open  msrpc         Microsoft Windows RPC
49160/tcp open  msrpc         Microsoft Windows RPC
MAC Address: 02:78:13:67:F4:5D (Unknown)
Service Info: Host: DARK-PC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: -1s, deviation: 0s, median: -1s
|_nbstat: NetBIOS name: DARK-PC, NetBIOS user: <unknown>, NetBIOS MAC: 02:78:13:67:f4:5d (unknown)
| smb-os-discovery: 
|   OS: Windows 7 Professional 7601 Service Pack 1 (Windows 7 Professional 6.1)
|   OS CPE: cpe:/o:microsoft:windows_7::sp1:professional
|   Computer name: Dark-PC
|   NetBIOS computer name: DARK-PC\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2023-06-04T13:26:16-05:00
| smb-security-mode: 
|   account_used: <blank>
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2023-06-04 19:26:16
|_  start_date: 2023-06-04 19:02:21

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .

```
## Gain Access

Starting Metasploit and searching for Icecast. I select the module and set RHOSTS to target ip and exploit. And I am in.


## Escalate

Running `run post/multi/recon/local_exploit_suggester`
I am going to use `exploit/windows/local/bypassuac_eventvwr`
Background the meterpreter, then select it and set SESSION to the meterpreter session.
Then run it and we have root access.
`getprivs` -> SeTakeOwnershipPrivilege

## Looting
Migrate to this process now with the command `migrate -N spoolsv.exe`
And now I am `NT AUTHORITY\SYSTEM`

Now I am going to use Kiwi, the updated version on Mimikatz to dump passwords.
Typing `load kiwi`
To get all the passwords I use `creds_all`

## Post-Exploitation

Before we start our post-exploitation, let's revisit the help menu one last time in the meterpreter shell. We'll answer the following questions using that menu.

What command allows us to dump all of the password hashes stored on the system? We won't crack the Administrative password in this case as it's pretty strong (this is intentional to avoid password spraying attempts)
`hashdump`

While more useful when interacting with a machine being used, what command allows us to watch the remote user's desktop in real time?
`screenshare`

How about if we wanted to record from a microphone attached to the system?
`record_mic`

To complicate forensics efforts we can modify timestamps of files on the system. What command allows us to do this? Don't ever do this on a pentest unless you're explicitly allowed to do so! This is not beneficial to the defending team as they try to breakdown the events of the pentest after the fact.
`timestomp`

Mimikatz allows us to create what's called a `golden ticket`, allowing us to authenticate anywhere with ease. What command allows us to do this?

Golden ticket attacks are a function within Mimikatz which abuses a component to Kerberos (the authentication system in Windows domains), the ticket-granting ticket. In short, golden ticket attacks allow us to maintain persistence and authenticate as any user on the domain.
`golden_ticket_create`


One last thing to note. As we have the password for the user 'Dark' we can now authenticate to the machine and access it via remote desktop (MSRDP). As this is a workstation, we'd likely kick whatever user is signed onto it off if we connect to it, however, it's always interesting to remote into machines and view them as their users do. If this hasn't already been enabled, we can enable it via the following Metasploit module: `run post/windows/manage/enable_rdp`