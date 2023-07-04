#CTF 

Can you root this Mr. Robot styled machine? This is a virtual machine meant for beginners/intermediate users. There are 3 hidden keys located on the machine, can you find them?

Credit to [Leon Johnson](https://twitter.com/@sho_luv) for creating this machine. **This machine is used here with the explicit permission of the creator <3**

## Recon

We start with Nmap as always and we get:

```
PORT    STATE  SERVICE  VERSION
22/tcp  closed ssh
80/tcp  open   http     Apache httpd
|_http-server-header: Apache
|_http-title: Site doesn't have a title (text/html).
443/tcp open   ssl/http Apache httpd
|_http-server-header: Apache
|_http-title: Site doesn't have a title (text/html).
| ssl-cert: Subject: commonName=www.example.com
| Not valid before: 2015-09-16T10:45:03
|_Not valid after:  2025-09-13T10:45:03
MAC Address: 02:82:46:B0:66:D3 (Unknown)
Aggressive OS guesses: Linux 3.13 (96%), Linux 3.10 - 4.8 (89%), Linux 3.11 (89%), Linux 3.12 (89%), Linux 3.13 or 4.2 (89%), Linux 3.2 - 3.5 (89%), Linux 3.2 - 3.8 (89%), Linux 4.4 (89%), Crestron XPanel control system (88%), Linux 4.2 (88%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 1 hop

TRACEROUTE
HOP RTT     ADDRESS
1   0.50 ms ip-10-10-200-68.eu-west-1.compute.internal (10.10.200.68)
```

So we have two web ports 80 and 443 both open and a ssh closed port.
Lets visit the the web ports
##### First 80:

We are pressented with an animation of a shell with a login and the we have some options and we can actually type in the console.
The options are prepare ,fsociety,inform,question,wakeup and join.

- Prepare plays a clip from the show.
- fsociety plays a small clip and the then again on the console
- inform presents a carousel of articles with test bellow. **A lot of information here! **


## Flags

Searching the /robots.txt directory I am presented with
```
User-agent: *
fsocity.dic
key-1-of-3.txt
```

So I navigate to key-1-of-3.txt and get my first flag
`073403c8a58a1f80d943455fb30724b9`


Next I download the dictionary fsocity.dic 
And scan the machine with [[Gobuster]] 
`gobuster dir -w fsocity.dic -u [ip]`

I found `/wp-login` that has a login page.
I tried a couple of  default user-passwords combinations but the error message always was
`**ERROR**: Invalid username. [Lost your password?](http://10.10.71.147/wp-login.php?action=lostpassword)`

But then I tried Elliot as username and got back
`**ERROR**: The password you entered for the username **Elliot** is incorrect. [Lost your password?](http://10.10.71.147/wp-login.php?action=lostpassword)`

which means that Elliot is registered as a valid username.
I will try to attach the login page with [[Burpsuite]].
I open burp, capture the login request with proxy and send it to the intruder.
I use a sniper attack and as payload I use the fsocity.dic.
After sometime I find the password
So I have the creds `Elliot    ER28-0652`