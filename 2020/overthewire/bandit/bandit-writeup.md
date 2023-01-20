-------
Level 0:

Level Goal

The goal of this level is for you to log into the game using SSH. The host to which you need to connect is bandit.labs.overthewire.org, on port 2220. The username is bandit0 and the password is bandit0. Once logged in, go to the Level 1 page to find out how to beat Level 1.
Commands you may need to solve this level

ssh

---
ssh bandit0@bandit.labs.overthewire.org -p 2220
Password: bandit0

In!
-------

Bandit Level 0 → Level 1
Level Goal

The password for the next level is stored in a file called readme located in the home directory. Use this password to log into bandit1 using SSH. Whenever you find a password for a level, use SSH (on port 2220) to log into that level and continue the game.
Commands you may need to solve this level

ls, cd, cat, file, du, find
---
bandit0@bandit:~$ cat readme
boJ9jbbUNNfktd78OOpsqOltutMc3MY1

ssh bandit1@bandit.labs.overthewire.org -p 2220
Password: boJ9jbbUNNfktd78OOpsqOltutMc3MY1

Done!
-------
Bandit Level 1 → Level 2
Level Goal

The password for the next level is stored in a file called - located in the home directory
Commands you may need to solve this level

ls, cd, cat, file, du, find
---
bandit1@bandit:~$ cat ./-
CV1DtqXWVFXTvM2F0k09SHz0YwRINYA9

ssh bandit2@bandit.labs.overthewire.org -p 2220
Password: CV1DtqXWVFXTvM2F0k09SHz0YwRINYA9

Done!
-------
Bandit Level 2 → Level 3
Level Goal

The password for the next level is stored in a file called spaces in this filename located in the home directory
Commands you may need to solve this level

ls, cd, cat, file, du, find
---
bandit2@bandit:~$ cat spaces\ in\ this\ filename
UmHadQclWmgdLOKQ3YNgjWxGoRMb5luK

ssh bandit3@bandit.labs.overthewire.org -p 2220
Password: UmHadQclWmgdLOKQ3YNgjWxGoRMb5luK

Done!
-------
Bandit Level 3 → Level 4
Level Goal

The password for the next level is stored in a hidden file in the inhere directory.
Commands you may need to solve this level

ls, cd, cat, file, du, find
---
bandit3@bandit:~$ cd inhere/
bandit3@bandit:~/inhere$ cat .hidden
pIwrPrtPN36QITSp3EQaw936yaFoFgAB

ssh bandit4@bandit.labs.overthewire.org -p 2220
Password: pIwrPrtPN36QITSp3EQaw936yaFoFgAB

Done!
-------

Bandit Level 4 → Level 5
Level Goal

The password for the next level is stored in the only human-readable file in the inhere directory. Tip: if your terminal is messed up, try the “reset” command.
Commands you may need to solve this level

ls, cd, cat, file, du, find

---

bandit4@bandit:~/inhere$ find . -type f | xargs file | grep text
./-file07: ASCII text
bandit4@bandit:~/inhere$ cat ./-file07
koReBOKuIDDepwhWk7jZC0RTdopnAYKh

ssh bandit5@bandit.labs.overthewire.org -p 2220
Password: koReBOKuIDDepwhWk7jZC0RTdopnAYKh

Done
-------

Bandit Level 5 → Level 6
Level Goal

The password for the next level is stored in a file somewhere under the inhere directory and has all of the following properties:

    human-readable
    1033 bytes in size
    not executable

Commands you may need to solve this level

ls, cd, cat, file, du, find
---
bandit5@bandit:~/inhere$ find . -type f ! -executable -readable -size 1033c
./maybehere07/.file2

bandit5@bandit:~/inhere$ cat ./maybehere07/.file2
DXjZPULLxYr17uwoI01bNLQbtFemEgo7

ssh bandit6@bandit.labs.overthewire.org -p 2220
Password: DXjZPULLxYr17uwoI01bNLQbtFemEgo7

Done!
-------

Bandit Level 6 → Level 7
Level Goal

The password for the next level is stored somewhere on the server and has all of the following properties:

    owned by user bandit7
    owned by group bandit6
    33 bytes in size

Commands you may need to solve this level

ls, cd, cat, file, du, find, grep

---
bandit6@bandit:~$ find / -size 33c -user bandit7 -group bandit6 2>/dev/null
/var/lib/dpkg/info/bandit7.password

bandit6@bandit:~$ cat /var/lib/dpkg/info/bandit7.password
HKBPTKQnIay4Fw76bEy8PVxKEDQRKTzs

ssh bandit7@bandit.labs.overthewire.org -p 2220
Password: HKBPTKQnIay4Fw76bEy8PVxKEDQRKTzs

Done
-------

Bandit Level 7 → Level 8
Level Goal

The password for the next level is stored in the file data.txt next to the word millionth
Commands you may need to solve this level

grep, sort, uniq, strings, base64, tr, tar, gzip, bzip2, xxd

bandit7@bandit:~$ cat data.txt | grep millionth
millionth       cvX2JJa4CFALtqS87jk27qwqGhBM9plV

ssh bandit8@bandit.labs.overthewire.org -p 2220
Password: cvX2JJa4CFALtqS87jk27qwqGhBM9plV

Done!
-------
Bandit Level 8 → Level 9
Level Goal

The password for the next level is stored in the file data.txt and is the only line of text that occurs only once
Commands you may need to solve this level

grep, sort, uniq, strings, base64, tr, tar, gzip, bzip2, xxd
---
bandit8@bandit:~$ sort data.txt | uniq -u
UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR

ssh bandit9@bandit.labs.overthewire.org -p 2220
Password: UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR

Done!
---


Bandit Level 9 → Level 10
Level Goal

The password for the next level is stored in the file data.txt in one of the few human-readable strings, preceded by several ‘=’ characters.
Commands you may need to solve this level

grep, sort, uniq, strings, base64, tr, tar, gzip, bzip2, xxd
---
bandit9@bandit:~$ strings data.txt | grep =====*
========== the*2i"4
========== password
Z)========== is
&========== truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk



ssh bandit10@bandit.labs.overthewire.org -p 2220
Password: truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk

Done!
---
Bandit Level 10 → Level 11
Level Goal

The password for the next level is stored in the file data.txt, which contains base64 encoded data
Commands you may need to solve this level

grep, sort, uniq, strings, base64, tr, tar, gzip, bzip2, xxd
---

bandit10@bandit:~$ base64 -d data.txt
The password is IFukwKGsFW8MOq3IRFqrxE1hxTNEbUPR

ssh bandit11@bandit.labs.overthewire.org -p 2220
Password: IFukwKGsFW8MOq3IRFqrxE1hxTNEbUPR

Done!
---
Bandit Level 11 → Level 12
Level Goal

The password for the next level is stored in the file data.txt, where all lowercase (a-z) and uppercase (A-Z) letters have been rotated by 13 positions
Commands you may need to solve this level

grep, sort, uniq, strings, base64, tr, tar, gzip, bzip2, xxd
---
bandit11@bandit:~$ cat data.txt | tr '[a-z][A-Z]' '[n-za-m][N-ZA-M]'
The password is 5Te8Y4drgCRfCx8ugdwuEX8KFC6k2EUu

ssh bandit12@bandit.labs.overthewire.org -p 2220
Password: 5Te8Y4drgCRfCx8ugdwuEX8KFC6k2EUu

Done!
---
Bandit Level 12 → Level 13
Level Goal

The password for the next level is stored in the file data.txt, which is a hexdump of a file that has been repeatedly compressed. For this level it may be useful to create a directory under /tmp in which you can work using mkdir. For example: mkdir /tmp/myname123. Then copy the datafile using cp, and rename it using mv (read the manpages!)
Commands you may need to solve this level

grep, sort, uniq, strings, base64, tr, tar, gzip, bzip2, xxd, mkdir, cp, mv, file
---
```
bandit12@bandit:/tmp/t$ xxd -r data.txt > file
bandit12@bandit:/tmp/t$ file file
file: gzip compressed data, was "data2.bin", last modified: Thu May  7 18:14:30 2020, max compression, from Unix
bandit12@bandit:/tmp/t$ mv file data2.bin.gz
bandit12@bandit:/tmp/t$ gzip -d data2.bin.gz
bandit12@bandit:/tmp/t$ file data2.bin
data2.bin: bzip2 compressed data, block size = 900k
bandit12@bandit:/tmp/t$ bzip2 -d data2.bin
bzip2: Can't guess original name for data2.bin -- using data2.bin.out
bandit12@bandit:/tmp/t$ ls
data2.bin.out  data.txt  data.txt.BAK
bandit12@bandit:/tmp/t$ file data2.bin.out
data2.bin.out: gzip compressed data, was "data4.bin", last modified: Thu May  7 18:14:30 2020, max compression, from Unix
bandit12@bandit:/tmp/t$ mv data2.bin.out data4.bin.gz
bandit12@bandit:/tmp/t$ ls
data4.bin.gz  data.txt  data.txt.BAK
bandit12@bandit:/tmp/t$ gzip -d data4.bin.gz
bandit12@bandit:/tmp/t$ ls
data4.bin  data.txt  data.txt.BAK
bandit12@bandit:/tmp/t$ file data4.bin
data4.bin: POSIX tar archive (GNU)
bandit12@bandit:/tmp/t$ tar -xvf data4.bin
data5.bin
bandit12@bandit:/tmp/t$ ls
data4.bin  data5.bin  data.txt  data.txt.BAK
bandit12@bandit:/tmp/t$ file data5.bin
data5.bin: POSIX tar archive (GNU)
bandit12@bandit:/tmp/t$ tar -xvf data5.bin
data6.bin
bandit12@bandit:/tmp/t$ file data6.bin
data6.bin: bzip2 compressed data, block size = 900k
bandit12@bandit:/tmp/t$ bzip2 -d data6.bin
bzip2: Can't guess original name for data6.bin -- using data6.bin.out
bandit12@bandit:/tmp/t$ file data6.bin.out
data6.bin.out: POSIX tar archive (GNU)
bandit12@bandit:/tmp/t$ tar -xvf data6.bin.out
data8.bin
bandit12@bandit:/tmp/t$ file data8.bin
data8.bin: gzip compressed data, was "data9.bin", last modified: Thu May  7 18:14:30 2020, max compression, from Unix
bandit12@bandit:/tmp/t$ mv data8.bin data8.bin.gz
bandit12@bandit:/tmp/t$ gzip -d data8.bin.gz
bandit12@bandit:/tmp/t$ file data8.bin
data8.bin: ASCII text
bandit12@bandit:/tmp/t$ cat data8.bin
The password is 8ZjyCRiBWFYkneahHwxCv3wb2a1ORpYL
```

ssh bandit13@bandit.labs.overthewire.org -p 2220
Password: 8ZjyCRiBWFYkneahHwxCv3wb2a1ORpYL

Done!
---

Bandit Level 13 → Level 14
Level Goal

The password for the next level is stored in /etc/bandit_pass/bandit14 and can only be read by user bandit14. For this level, you don’t get the next password, but you get a private SSH key that can be used to log into the next level. Note: localhost is a hostname that refers to the machine you are working on
Commands you may need to solve this level

ssh, telnet, nc, openssl, s_client, nmap
---

bandit13@bandit:~$ ssh bandit14@localhost -i sshkey.private

---
Bandit Level 14 → Level 15
Level Goal

The password for the next level can be retrieved by submitting the password of the current level to port 30000 on localhost.
Commands you may need to solve this level

ssh, telnet, nc, openssl, s_client, nmap

1. bandit14@bandit:/etc/bandit_pass$ cat bandit14
4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e
(from previous level)

2.
bandit14@bandit:/etc/bandit_pass$ nc localhost 30000
4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e
Correct!
BfMYroe26WYalil77FoDi9qh59eK5xNr

ssh bandit15@localhost
Password: BfMYroe26WYalil77FoDi9qh59eK5xNr

Done!
---
Bandit Level 15 → Level 16
Level Goal

The password for the next level can be retrieved by submitting the password of the current level to port 30001 on localhost using SSL encryption.

Helpful note: Getting “HEARTBEATING” and “Read R BLOCK”? Use -ign_eof and read the “CONNECTED COMMANDS” section in the manpage. Next to ‘R’ and ‘Q’, the ‘B’ command also works in this version of that command…
Commands you may need to solve this level

ssh, telnet, nc, openssl, s_client, nmap

bandit15@bandit:~$ cat .bandit14.password
4wcYUJFw0k0XLShlDzztnTBHiqxU3b3e

```
bandit15@bandit:~$ openssl s_client -connect localhost:30001
CONNECTED(00000003)
depth=0 CN = localhost
verify error:num=18:self signed certificate
verify return:1
depth=0 CN = localhost
verify return:1
---
Certificate chain
 0 s:/CN=localhost
   i:/CN=localhost
---
Server certificate
-----BEGIN CERTIFICATE-----
MIICBjCCAW+gAwIBAgIEDU18oTANBgkqhkiG9w0BAQUFADAUMRIwEAYDVQQDDAls
b2NhbGhvc3QwHhcNMjAwNTA3MTgxNTQzWhcNMjEwNTA3MTgxNTQzWjAUMRIwEAYD
VQQDDAlsb2NhbGhvc3QwgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAK3CPNFR
FEypcqUa8NslmIMWl9xq53Cwhs/fvYHAvauyfE3uDVyyX79Z34Tkot6YflAoufnS
+puh2Kgq7aDaF+xhE+FPcz1JE0C2bflGfEtx4l3qy79SRpLiZ7eio8NPasvduG5e
pkuHefwI4c7GS6Y7OTz/6IpxqXBzv3c+x93TAgMBAAGjZTBjMBQGA1UdEQQNMAuC
CWxvY2FsaG9zdDBLBglghkgBhvhCAQ0EPhY8QXV0b21hdGljYWxseSBnZW5lcmF0
ZWQgYnkgTmNhdC4gU2VlIGh0dHBzOi8vbm1hcC5vcmcvbmNhdC8uMA0GCSqGSIb3
DQEBBQUAA4GBAC9uy1rF2U/OSBXbQJYuPuzT5mYwcjEEV0XwyiX1MFZbKUlyFZUw
rq+P1HfFp+BSODtk6tHM9bTz+p2OJRXuELG0ly8+Nf/hO/mYS1i5Ekzv4PL9hO8q
PfmDXTHs23Tc7ctLqPRj4/4qxw6RF4SM+uxkAuHgT/NDW1LphxkJlKGn
-----END CERTIFICATE-----
subject=/CN=localhost
issuer=/CN=localhost
---
No client certificate CA names sent
Peer signing digest: SHA512
Server Temp Key: X25519, 253 bits
---
SSL handshake has read 1019 bytes and written 269 bytes
Verification error: self signed certificate
---
New, TLSv1.2, Cipher is ECDHE-RSA-AES256-GCM-SHA384
Server public key is 1024 bit
Secure Renegotiation IS supported
Compression: NONE
Expansion: NONE
No ALPN negotiated
SSL-Session:
    Protocol  : TLSv1.2
    Cipher    : ECDHE-RSA-AES256-GCM-SHA384
    Session-ID: E527795E8A07A48A61F6CF41E1626BE27571EC032BFCE3CEAE67188A9A10628B
    Session-ID-ctx:
    Master-Key: 151802257B40F8A4001E6980A8515F80BFF773F5D9286907561384C70890FC44C2473D0487AF36FEBAD429A02818F1D6
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    TLS session ticket lifetime hint: 7200 (seconds)
    TLS session ticket:
    0000 - aa 02 e6 3a 2e 0b c8 5d-6f 54 4a 1b 5a e0 2c 0e   ...:...]oTJ.Z.,.
    0010 - f9 0a 5d df f5 6b 65 6d-3a 87 f0 cd 35 d4 32 b2   ..]..kem:...5.2.
    0020 - c7 15 24 e0 04 9f a9 7c-69 4f 7d e3 0f 2f fb 35   ..$....|iO}../.5
    0030 - 79 77 d2 e0 9c 41 91 cc-95 ab 89 39 a8 dd 31 8b   yw...A.....9..1.
    0040 - 3f 49 83 2a da af 67 f4-12 1f 29 f6 34 30 d3 c6   ?I.*..g...).40..
    0050 - 0c 65 06 3e cc 87 6f 1f-0f 65 ee 82 4f b0 f7 85   .e.>..o..e..O...
    0060 - 9c f0 a4 b6 15 9f 4a 33-d5 df 00 b3 72 ca ee 1f   ......J3....r...
    0070 - 1d 3f a3 e3 36 38 68 0d-8c 4e 2f a1 f8 b2 5e 12   .?..68h..N/...^.
    0080 - a2 fe 12 05 e4 5b 04 39-4d e4 1b 74 4d 14 ef 0b   .....[.9M..tM...
    0090 - 12 35 3c 8e 51 ab 4a 96-bc 44 e8 94 34 69 2d 29   .5<.Q.J..D..4i-)

    Start Time: 1594002245
    Timeout   : 7200 (sec)
    Verify return code: 18 (self signed certificate)
    Extended master secret: yes
---
BfMYroe26WYalil77FoDi9qh59eK5xNr
Correct!
cluFn7wTiGryunymYOu4RcffSxQluehd

closed
```

ssh bandit16@localhost
Password: cluFn7wTiGryunymYOu4RcffSxQluehd
Done!
---
Bandit Level 16 → Level 17
Level Goal

The credentials for the next level can be retrieved by submitting the password of the current level to a port on localhost in the range 31000 to 32000. First find out which of these ports have a server listening on them. Then find out which of those speak SSL and which don’t. There is only 1 server that will give the next credentials, the others will simply send back to you whatever you send to it.
Commands you may need to solve this level

ssh, telnet, nc, openssl, s_client, nmap
---
bandit16@bandit:~$ cat .bandit15.password
BfMYroe26WYalil77FoDi9qh59eK5xNr

```
bandit16@bandit:~$ nmap -sV -A -p31000-32000 localhost

Starting Nmap 7.40 ( https://nmap.org ) at 2020-07-06 04:26 CEST
Stats: 0:00:42 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 60.00% done; ETC: 04:28 (0:00:28 remaining)
Stats: 0:01:10 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 80.00% done; ETC: 04:28 (0:00:17 remaining)
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00023s latency).
Not shown: 996 closed ports
PORT      STATE SERVICE     VERSION
31046/tcp open  echo
31518/tcp open  ssl/echo
| ssl-cert: Subject: commonName=localhost
| Subject Alternative Name: DNS:localhost
| Not valid before: 2020-05-14T12:03:38
|_Not valid after:  2021-05-14T12:03:38
|_ssl-date: TLS randomness does not represent time
31691/tcp open  echo
31790/tcp open  ssl/unknown
| fingerprint-strings:
|   FourOhFourRequest, GenericLines, GetRequest, HTTPOptions, Help, Kerberos, LDAPSearchReq, LPDString, RTSPRequest, SIPOptions, SSLSessionReq, TLSSessionReq:
|_    Wrong! Please enter the correct current password
| ssl-cert: Subject: commonName=localhost
| Subject Alternative Name: DNS:localhost
| Not valid before: 2020-05-14T12:03:38
|_Not valid after:  2021-05-14T12:03:38
|_ssl-date: TLS randomness does not represent time
31960/tcp open  echo
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port31790-TCP:V=7.40%T=SSL%I=7%D=7/6%Time=5F028BFD%P=x86_64-pc-linux-gn
SF:u%r(GenericLines,31,"Wrong!\x20Please\x20enter\x20the\x20correct\x20cur
SF:rent\x20password\n")%r(GetRequest,31,"Wrong!\x20Please\x20enter\x20the\
SF:x20correct\x20current\x20password\n")%r(HTTPOptions,31,"Wrong!\x20Pleas
SF:e\x20enter\x20the\x20correct\x20current\x20password\n")%r(RTSPRequest,3
SF:1,"Wrong!\x20Please\x20enter\x20the\x20correct\x20current\x20password\n
SF:")%r(Help,31,"Wrong!\x20Please\x20enter\x20the\x20correct\x20current\x2
SF:0password\n")%r(SSLSessionReq,31,"Wrong!\x20Please\x20enter\x20the\x20c
SF:orrect\x20current\x20password\n")%r(TLSSessionReq,31,"Wrong!\x20Please\
SF:x20enter\x20the\x20correct\x20current\x20password\n")%r(Kerberos,31,"Wr
SF:ong!\x20Please\x20enter\x20the\x20correct\x20current\x20password\n")%r(
SF:FourOhFourRequest,31,"Wrong!\x20Please\x20enter\x20the\x20correct\x20cu
SF:rrent\x20password\n")%r(LPDString,31,"Wrong!\x20Please\x20enter\x20the\
SF:x20correct\x20current\x20password\n")%r(LDAPSearchReq,31,"Wrong!\x20Ple
SF:ase\x20enter\x20the\x20correct\x20current\x20password\n")%r(SIPOptions,
SF:31,"Wrong!\x20Please\x20enter\x20the\x20correct\x20current\x20password\
SF:n");

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 90.54 seconds
```
We'll try port 31790 (since the other SSL has echo running, so assume it's just going to give back what we type).

```
bandit16@bandit:~$ openssl s_client -connect localhost:31790
CONNECTED(00000003)
depth=0 CN = localhost
verify error:num=18:self signed certificate
verify return:1
depth=0 CN = localhost
verify return:1
---
Certificate chain
 0 s:/CN=localhost
   i:/CN=localhost
---
Server certificate
-----BEGIN CERTIFICATE-----
MIICBjCCAW+gAwIBAgIEUnONgjANBgkqhkiG9w0BAQUFADAUMRIwEAYDVQQDDAls
b2NhbGhvc3QwHhcNMjAwNTE0MTIwMzM4WhcNMjEwNTE0MTIwMzM4WjAUMRIwEAYD
VQQDDAlsb2NhbGhvc3QwgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBALOw+M6/
qSBJExGueg6T0HQRqfr80ysnqbuIAeQJ3VOwXg3BB8u7HtlA6JUrvQy66TWw5szi
uLBAyCffNHMx7Y2DF6L2vdSTxoOuDTLynRj7Xrw4f39NbgezfpfPbOd7/m3qNpcG
766Y46MT8w8j144VKK6qWhkBl9CPy8E2/frdAgMBAAGjZTBjMBQGA1UdEQQNMAuC
CWxvY2FsaG9zdDBLBglghkgBhvhCAQ0EPhY8QXV0b21hdGljYWxseSBnZW5lcmF0
ZWQgYnkgTmNhdC4gU2VlIGh0dHBzOi8vbm1hcC5vcmcvbmNhdC8uMA0GCSqGSIb3
DQEBBQUAA4GBAEj2rWLwLHxQMk8uuUTFHnXrtnpXP3GDch8zdUbiln4chTISKG9O
akG/gohigTEo9V3PupKcaO/zXqAbuB6iaJxOEezuLEmoGAMThHqeXusLNEPtYl5N
nM/qYplbcQtOqvYYODdP9N5dQFa54xkNmkP7oPiQkOFFKIucVzpxwzuo
-----END CERTIFICATE-----
subject=/CN=localhost
issuer=/CN=localhost
---
No client certificate CA names sent
Peer signing digest: SHA512
Server Temp Key: X25519, 253 bits
---
SSL handshake has read 1019 bytes and written 269 bytes
Verification error: self signed certificate
---
New, TLSv1.2, Cipher is ECDHE-RSA-AES256-GCM-SHA384
Server public key is 1024 bit
Secure Renegotiation IS supported
Compression: NONE
Expansion: NONE
No ALPN negotiated
SSL-Session:
    Protocol  : TLSv1.2
    Cipher    : ECDHE-RSA-AES256-GCM-SHA384
    Session-ID: 48E91EA8EA2622AAEEC2D6D58D65CD7137C980B992FF2F952E80BC733BF66D05
    Session-ID-ctx:
    Master-Key: EC9A1B760CCAFBACD4DA2980D8667653B6741FD1B7D1A7D26E74F193DECD9EFCF79E4D01706560BE6571867ADF0D5EE6
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    TLS session ticket lifetime hint: 7200 (seconds)
    TLS session ticket:
    0000 - 15 f8 3e c0 71 51 2c d3-79 e7 dc 31 91 05 ba 3b   ..>.qQ,.y..1...;
    0010 - 89 e7 b0 54 b2 ae 09 0f-55 d1 68 80 e9 fe 6a 8b   ...T....U.h...j.
    0020 - 6d 0b 84 4c e6 ec 1c f2-9b b1 db a3 05 92 6e ae   m..L..........n.
    0030 - 13 1c da ca ec 6f 34 db-80 fb 7a 5a 00 9d c7 9d   .....o4...zZ....
    0040 - 54 d1 b4 f3 2d d6 49 e2-54 4a cf 07 66 91 be 43   T...-.I.TJ..f..C
    0050 - 8c 57 d2 80 cf 14 e8 22-9e 0a d0 39 6f 84 01 bf   .W....."...9o...
    0060 - 1b c8 dc f6 32 97 2d 82-ca 0f 37 fb 27 77 cf 56   ....2.-...7.'w.V
    0070 - 13 2f 47 22 74 71 15 29-73 36 ba e5 ef 56 c1 46   ./G"tq.)s6...V.F
    0080 - 30 38 5a f3 a3 87 8a 83-12 cd c0 f2 ca 56 43 0a   08Z..........VC.
    0090 - e4 12 1e 48 f2 f4 51 d7-ab 10 7d ca fb 77 f9 9b   ...H..Q...}..w..

    Start Time: 1594002625
    Timeout   : 7200 (sec)
    Verify return code: 18 (self signed certificate)
    Extended master secret: yes
---
cluFn7wTiGryunymYOu4RcffSxQluehd
Correct!
-----BEGIN RSA PRIVATE KEY-----
MIIEogIBAAKCAQEAvmOkuifmMg6HL2YPIOjon6iWfbp7c3jx34YkYWqUH57SUdyJ
imZzeyGC0gtZPGujUSxiJSWI/oTqexh+cAMTSMlOJf7+BrJObArnxd9Y7YT2bRPQ
Ja6Lzb558YW3FZl87ORiO+rW4LCDCNd2lUvLE/GL2GWyuKN0K5iCd5TbtJzEkQTu
DSt2mcNn4rhAL+JFr56o4T6z8WWAW18BR6yGrMq7Q/kALHYW3OekePQAzL0VUYbW
JGTi65CxbCnzc/w4+mqQyvmzpWtMAzJTzAzQxNbkR2MBGySxDLrjg0LWN6sK7wNX
x0YVztz/zbIkPjfkU1jHS+9EbVNj+D1XFOJuaQIDAQABAoIBABagpxpM1aoLWfvD
KHcj10nqcoBc4oE11aFYQwik7xfW+24pRNuDE6SFthOar69jp5RlLwD1NhPx3iBl
J9nOM8OJ0VToum43UOS8YxF8WwhXriYGnc1sskbwpXOUDc9uX4+UESzH22P29ovd
d8WErY0gPxun8pbJLmxkAtWNhpMvfe0050vk9TL5wqbu9AlbssgTcCXkMQnPw9nC
YNN6DDP2lbcBrvgT9YCNL6C+ZKufD52yOQ9qOkwFTEQpjtF4uNtJom+asvlpmS8A
vLY9r60wYSvmZhNqBUrj7lyCtXMIu1kkd4w7F77k+DjHoAXyxcUp1DGL51sOmama
+TOWWgECgYEA8JtPxP0GRJ+IQkX262jM3dEIkza8ky5moIwUqYdsx0NxHgRRhORT
8c8hAuRBb2G82so8vUHk/fur85OEfc9TncnCY2crpoqsghifKLxrLgtT+qDpfZnx
SatLdt8GfQ85yA7hnWWJ2MxF3NaeSDm75Lsm+tBbAiyc9P2jGRNtMSkCgYEAypHd
HCctNi/FwjulhttFx/rHYKhLidZDFYeiE/v45bN4yFm8x7R/b0iE7KaszX+Exdvt
SghaTdcG0Knyw1bpJVyusavPzpaJMjdJ6tcFhVAbAjm7enCIvGCSx+X3l5SiWg0A
R57hJglezIiVjv3aGwHwvlZvtszK6zV6oXFAu0ECgYAbjo46T4hyP5tJi93V5HDi
Ttiek7xRVxUl+iU7rWkGAXFpMLFteQEsRr7PJ/lemmEY5eTDAFMLy9FL2m9oQWCg
R8VdwSk8r9FGLS+9aKcV5PI/WEKlwgXinB3OhYimtiG2Cg5JCqIZFHxD6MjEGOiu
L8ktHMPvodBwNsSBULpG0QKBgBAplTfC1HOnWiMGOU3KPwYWt0O6CdTkmJOmL8Ni
blh9elyZ9FsGxsgtRBXRsqXuz7wtsQAgLHxbdLq/ZJQ7YfzOKU4ZxEnabvXnvWkU
YOdjHdSOoKvDQNWu6ucyLRAWFuISeXw9a/9p7ftpxm0TSgyvmfLF2MIAEwyzRqaM
77pBAoGAMmjmIJdjp+Ez8duyn3ieo36yrttF5NSsJLAbxFpdlc1gvtGCWW+9Cq0b
dxviW8+TFVEBl1O4f7HVm6EpTscdDxU+bCXWkfjuRb7Dy9GOtt9JPsX8MBTakzh3
vBgsyi/sN3RqRBcGU40fOoZyfAMT8s1m/uYv52O6IgeuZ/ujbjY=
-----END RSA PRIVATE KEY-----

closed
```
Found a SSH key!

```
bandit16@bandit:~$ cd /tmp
bandit16@bandit:/tmp$ mkdir 12345
bandit16@bandit:/tmp$ cd 12345
bandit16@bandit:/tmp/12345$ nano id_rsa
## Add RSA key into there (copy paste)
andit16@bandit:/tmp/12345$ chmod 600 id_rsa
bandit16@bandit:/tmp/12345$ ssh bandit17@localhost -i id_rsa
```
Done!
---

Bandit Level 17 → Level 18
Level Goal

There are 2 files in the homedirectory: passwords.old and passwords.new. The password for the next level is in passwords.new and is the only line that has been changed between passwords.old and passwords.new

NOTE: if you have solved this level and see ‘Byebye!’ when trying to log into bandit18, this is related to the next level, bandit19
Commands you may need to solve this level

cat, grep, ls, diff
---
```
bandit17@bandit:~$ diff passwords.new passwords.old
42c42
< kfBf3eYk5BPBRzwjqutbbfE887SVc5Yd
---
> w0Yfolrc5bwjS4qw5mq1nnQi6mF03bii
```

Password = kfBf3eYk5BPBRzwjqutbbfE887SVc5Yd

Tried to login:
bandit17@bandit:~$ ssh bandit18@localhost

But got:
...
...
...
Byebye !
Connection to localhost closed.
---

Bandit Level 18 → Level 19
Level Goal

The password for the next level is stored in a file readme in the homedirectory. Unfortunately, someone has modified .bashrc to log you out when you log in with SSH.
Commands you may need to solve this level

ssh, ls, cat
---

```
bandit17@bandit:~$ ssh -t bandit18@localhost cat readme
The authenticity of host 'localhost (127.0.0.1)' can't be established.
ECDSA key fingerprint is SHA256:98UL0ZWr85496EtCRkKlo20X3OPnyPSB5tB5RPbhczc.
Are you sure you want to continue connecting (yes/no)? yes
Failed to add the host to the list of known hosts (/home/bandit17/.ssh/known_hosts).
This is a OverTheWire game server. More information on http://www.overthewire.org/wargames

@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions 0640 for '/home/bandit17/.ssh/id_rsa' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
Load key "/home/bandit17/.ssh/id_rsa": bad permissions
bandit18@localhost's password:
IueksS7Ubh8G3DCwVzrTd8rAVOwq3M5x
Connection to localhost closed.
```
ssh bandit19@localhost
IueksS7Ubh8G3DCwVzrTd8rAVOwq3M5x

Done!

---
Bandit Level 19 → Level 20
Level Goal

To gain access to the next level, you should use the setuid binary in the homedirectory. Execute it without arguments to find out how to use it. The password for this level can be found in the usual place (/etc/bandit_pass), after you have used the setuid binary.
---
bandit19@bandit:~$ ./bandit20-do cat /etc/bandit_pass/bandit20
GbKksEFF4yrVs6il55v6gwY5aVje5f0j

bandit19@bandit:~$ ssh bandit20@localhost

Done!
---

Bandit Level 20 → Level 21
Level Goal

There is a setuid binary in the homedirectory that does the following: it makes a connection to localhost on the port you specify as a commandline argument. It then reads a line of text from the connection and compares it to the password in the previous level (bandit20). If the password is correct, it will transmit the password for the next level (bandit21).

NOTE: Try connecting to your own network daemon to see if it works as you think
Commands you may need to solve this level

ssh, nc, cat, bash, screen, tmux, Unix ‘job control’ (bg, fg, jobs, &, CTRL-Z, …)
---

bandit20@bandit:~$ nmap localhost

Starting Nmap 7.40 ( https://nmap.org ) at 2020-07-06 05:03 CEST
Nmap scan report for localhost (127.0.0.1)
Host is up (0.00031s latency).
Not shown: 997 closed ports
PORT      STATE SERVICE
22/tcp    open  ssh
113/tcp   open  ident
30000/tcp open  ndmps

On first Kali VM Terminal:
bandit20@bandit:~$ echo "GbKksEFF4yrVs6il55v6gwY5aVje5f0j" | nc -l -p 60000

Open up a new terminal, log in to bandit0, then log in to bandit20:
kali@kali:~/labs/overthewire/bandit$ ssh bandit0@bandit.labs.overthewire.org -p 2220
Pwd: bandit0

bandit0@bandit:~$ ssh bandit20@localhost
Password: GbKksEFF4yrVs6il55v6gwY5aVje5f0j

./suconnect 60000

On "echo" terminal (where you echoed), you get:
bandit20@bandit:~$ echo "GbKksEFF4yrVs6il55v6gwY5aVje5f0j" | nc -l -p 60000
gE269g2h3mw3pwgrj0Ha9Uoqen1c9DGr

ssh bandit21@localhost
Password: gE269g2h3mw3pwgrj0Ha9Uoqen1c9DGr

Done!

---
Bandit Level 21 → Level 22
Level Goal

A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.
Commands you may need to solve this level

cron, crontab, crontab(5) (use “man 5 crontab” to access this)
---

bandit21@bandit:/etc/cron.d$ cat cronjob_bandit22
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null

bandit21@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit22.sh
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv

bandit21@bandit:/etc/cron.d$ cat /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
Yk7owGAcWjwMVRwrTesJEwB7WVOiILLI

ssh bandit22@localhost
Password: Yk7owGAcWjwMVRwrTesJEwB7WVOiILLI
---

Bandit Level 22 → Level 23
Level Goal

A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

NOTE: Looking at shell scripts written by other people is a very useful skill. The script for this level is intentionally made easy to read. If you are having problems understanding what it does, try executing it to see the debug information it prints.
Commands you may need to solve this level

cron, crontab, crontab(5) (use “man 5 crontab” to access this)
---

```
bandit22@bandit:~$ ls -la /etc/cron.d
total 36
drwxr-xr-x  2 root root 4096 May 14 14:04 .
drwxr-xr-x 87 root root 4096 May 14 09:41 ..
-rw-r--r--  1 root root   62 May 14 13:40 cronjob_bandit15_root
-rw-r--r--  1 root root   62 May 14 14:03 cronjob_bandit17_root
-rw-r--r--  1 root root  120 May  7 20:14 cronjob_bandit22
-rw-r--r--  1 root root  122 May  7 20:14 cronjob_bandit23
-rw-r--r--  1 root root  120 May 14 09:41 cronjob_bandit24
-rw-r--r--  1 root root   62 May 14 14:04 cronjob_bandit25_root
-rw-r--r--  1 root root  102 Oct  7  2017 .placeholder
bandit22@bandit:~$ cat /etc/cron.d/cronjob_bandit23
@reboot bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
bandit22@bandit:~$ cat /usr/bin/cronjob_bandit23.sh
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget
```

So, this is saying:

1. whoami = bandit23
2. mytarget = 8ca319486bfbbc3663ea0fbe81326349
3. Copies file to /tmp/8ca319486bfbbc3663ea0fbe81326349

So we just need to cat it out :)

bandit22@bandit:~$ cat /tmp/8ca319486bfbbc3663ea0fbe81326349
jc1udXuA1tiHqjIsL8yaapX5XIAI6i0n

ssh bandit23@localhost
Password: jc1udXuA1tiHqjIsL8yaapX5XIAI6i0n

Done!

---

Bandit Level 23 → Level 24
Level Goal

A program is running automatically at regular intervals from cron, the time-based job scheduler. Look in /etc/cron.d/ for the configuration and see what command is being executed.

NOTE: This level requires you to create your own first shell-script. This is a very big step and you should be proud of yourself when you beat this level!

NOTE 2: Keep in mind that your shell script is removed once executed, so you may want to keep a copy around…
Commands you may need to solve this level

cron, crontab, crontab(5) (use “man 5 crontab” to access this)
---
```
bandit23@bandit:~$ cat /etc/cron.d/cronjob_bandit24
@reboot bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
* * * * * bandit24 /usr/bin/cronjob_bandit24.sh &> /dev/null
bandit23@bandit:~$ cat /usr/bin/cronjob_bandit24.sh
#!/bin/bash

myname=$(whoami)

cd /var/spool/$myname
echo "Executing and deleting all scripts in /var/spool/$myname:"
for i in * .*;
do
    if [ "$i" != "." -a "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" ./$i)"
        if [ "${owner}" = "bandit23" ]; then
            timeout -s 9 60 ./$i
        fi
        rm -f ./$i
    fi
done
```

This is saying:
1. myname = bandit24
2. cd /var/spool/bandit24
3. For files in *.*:
a. If file i is NOT equal to .

So it executes bandit23 code!

Write code as follows:

bandit23@bandit:/tmp/hello$ cat script.sh
#!/bin/bash
cat /etc/bandit_pass/bandit24 >> /tmp/hello/bandit24password

Then:
chmod 777 script.sh
cp script.sh /var/spool/bandit24

This SHOULD work, but for some reason it didn't. I found that the previous method (MD5) works the same for this level!


bandit23@bandit:/tmp/hello$ cat /tmp/ee4ee1703b083edac9f8183e4ae70293
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ

ssh bandit24@localhost
UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ

Done!

---

Bandit Level 24 → Level 25
Level Goal

A daemon is listening on port 30002 and will give you the password for bandit25 if given the password for bandit24 and a secret numeric 4-digit pincode. There is no way to retrieve the pincode except by going through all of the 10000 combinations, called brute-forcing.
---

bandit24@bandit:/tmp/1$ cat bruteforce.py
#!/usr/bin/env python3
# coding: utf-8

import sys
import socket

pincode = 0
password = "UoMYTrfrBFHyQXmg6gzctqAwOmw1IohZ"

try:
    # Connect to server
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.connect(("127.0.0.1", 30002))

    # Print Welcome Message
    welcome_msg = s.recv(2048)
    print(welcome_msg)

    # Try Brute-Forcing
    while pincode < 10000:
        pincode_string = str(pincode).zfill(4)
        message=password+" "+pincode_string+"\n"

        # Send Message
        s.sendall(message.encode())
        receive_msg = s.recv(1024)

        # Check Result
        if "Wrong" in receive_msg:
            print("Wrong PINCODE: %s" % pincode_string)
        else:
            print(receive_msg)
            break
        pincode += 1

finally:
    sys.exit(1)

python ./bruteforce.py

Let it run. This will take time.
...
Wrong PINCODE: 2577
Wrong PINCODE: 2578
Wrong PINCODE: 2579
Wrong PINCODE: 2580
Wrong PINCODE: 2581
Wrong PINCODE: 2582
Wrong PINCODE: 2583
Wrong PINCODE: 2584
Wrong PINCODE: 2585
Wrong PINCODE: 2586
Wrong PINCODE: 2587
Correct!
The password of user bandit25 is uNG9O58gUE7snukf3bvZ0rxhtnjzSGzG

ssh bandit25@localhost
uNG9O58gUE7snukf3bvZ0rxhtnjzSGzG

Done!

---

Bandit Level 25 → Level 26
Level Goal

Logging in to bandit26 from bandit25 should be fairly easy… The shell for user bandit26 is not /bin/bash, but something else. Find out what it is, how it works and how to break out of it.
Commands you may need to solve this level

ssh, cat, more, vi, ls, id, pwd
---
cat /etc/passwd:

...
bandit26:x:11026:11026:bandit level 26:/home/bandit26:/usr/bin/showtext
...

Solution:
Shrink terminal screen horizontally (to say 1 line) so it forces the "more" option to show.
Type v
Enlarge it, type
:e /etc/bandit_pass/bandit26

And you can read it!

ssh bandit26@localhost
Password: 5czgV9L3Xx8JPOyRbXh6lQbmIOWvPT6Z
Done!

To break out of the current showtext shell, we:

Shrink terminal horizontally.
SSH into bandit26.
Type v when the "more" comes up
Esc
:set shell=/bin/bash
Esc
:shell

---
Bandit Level 26 → Level 27
Level Goal

Good job getting a shell! Now hurry and grab the password for bandit27!

bandit26@bandit:~$ ./bandit27-do cat /etc/bandit_pass/bandit27
3ba3118a22e93127a4ed485be72ef5ea

ssh bandit27@localhost
3ba3118a22e93127a4ed485be72ef5ea

Done!
---


Bandit Level 27 → Level 28
Level Goal

There is a git repository at ssh://bandit27-git@localhost/home/bandit27-git/repo. The password for the user bandit27-git is the same as for the user bandit27.

Clone the repository and find the password for the next level.
Commands you may need to solve this level

git

bandit27@bandit:~$ mkdir /tmp/gbgb
bandit27@bandit:~$ cd /tmp/gbgb

bandit27@bandit:/tmp/gbgb$ git clone ssh://bandit27-git@localhost/home/bandit27-git/repo
Password: 3ba3118a22e93127a4ed485be72ef5ea

bandit27@bandit:/tmp/gbgb$ cd repo
bandit27@bandit:/tmp/gbgb/repo$ cat README
The password to the next level is: 0ef186ac70e04ea33b4c1853d2526fa2

ssh bandit28@localhost
0ef186ac70e04ea33b4c1853d2526fa2

Done!

---

Bandit Level 28 → Level 29
Level Goal

There is a git repository at ssh://bandit28-git@localhost/home/bandit28-git/repo. The password for the user bandit28-git is the same as for the user bandit28.

Clone the repository and find the password for the next level.
Commands you may need to solve this level

git

---
bandit28@bandit:~$ mkdir /tmp/gg && cd /tmp/gg && git clone ssh://bandit28-git@localhost/home/bandit28-git/repo

bandit28@bandit:/tmp/gg$ cd repo
bandit28@bandit:/tmp/gg/repo$ cat README.md
# Bandit Notes
Some notes for level29 of bandit.

## credentials

- username: bandit29
- password: xxxxxxxxxx

git log -p
or
git log -lp

diff --git a/README.md b/README.md
index 3f7cee8..5c6457b 100644
--- a/README.md
+++ b/README.md
@@ -4,5 +4,5 @@ Some notes for level29 of bandit.
 ## credentials

 - username: bandit29
-- password: bbc96594b4e001778eee9975372716b2
+- password: xxxxxxxxxx


ssh bandit29@localhost
bbc96594b4e001778eee9975372716b2

Done!

---

Bandit Level 29 → Level 30
Level Goal

There is a git repository at ssh://bandit29-git@localhost/home/bandit29-git/repo. The password for the user bandit29-git is the same as for the user bandit29.

Clone the repository and find the password for the next level.
Commands you may need to solve this level

git

bandit29@bandit:~$ mkdir /tmp/ab && cd /tmp/ab && git clone ssh://bandit29-git@localhost/home/bandit29-git/repo

Password: bbc96594b4e001778eee9975372716b2

bandit29@bandit:/tmp/ab/repo$ git show
commit 208f463b5b3992906eabf23c562eda3277fea912
Author: Ben Dover <noone@overthewire.org>
Date:   Thu May 7 20:14:51 2020 +0200

    fix username

diff --git a/README.md b/README.md
index 2da2f39..1af21d3 100644
--- a/README.md
+++ b/README.md
@@ -3,6 +3,6 @@ Some notes for bandit30 of bandit.

## credentials

-- username: bandit29
+- username: bandit30
- password: <no passwords in production!>

bandit29@bandit:/tmp/ab/repo$ git branch -a
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/dev
  remotes/origin/master
  remotes/origin/sploits-dev

bandit29@bandit:/tmp/ab/repo$ git checkout origin/dev

bandit29@bandit:/tmp/ab/repo$ cat README.md
# Bandit Notes
Some notes for bandit30 of bandit.

## credentials

- username: bandit30
- password: 5b90576bedb2cc04c86a9e924ce42faf

ssh bandit30@localhost
5b90576bedb2cc04c86a9e924ce42faf

Done!

---

Bandit Level 30 → Level 31
Level Goal

There is a git repository at ssh://bandit30-git@localhost/home/bandit30-git/repo. The password for the user bandit30-git is the same as for the user bandit30.

Clone the repository and find the password for the next level.
Commands you may need to solve this level

git

---
bandit30@bandit:~$ mkdir /tmp/v && cd /tmp/v && git clone ssh://bandit30-git@localhost/home/bandit30-git/repo

bandit30@bandit:/tmp/v/repo$ cat README.md
just an epmty file... muahaha

bandit30@bandit:/tmp/v/repo$ git branch -a
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/master

bandit30@bandit:/tmp/v/repo$ git tag
secret

bandit30@bandit:/tmp/v/repo$ git show secret
47e603bb428404d265f59c42920d81e5

ssh bandit31@localhost
47e603bb428404d265f59c42920d81e5

Done!

---

Bandit Level 31 → Level 32
Level Goal

There is a git repository at ssh://bandit31-git@localhost/home/bandit31-git/repo. The password for the user bandit31-git is the same as for the user bandit31.

Clone the repository and find the password for the next level.
Commands you may need to solve this level

git
---
bandit31@bandit:~$ mkdir /tmp/q && cd /tmp/q && git clone ssh://bandit31-git@localhost/home/bandit31-git/repo && cd repo

47e603bb428404d265f59c42920d81e5

bandit31@bandit:/tmp/q/repo$ cat README.md
This time your task is to push a file to the remote repository.

Details:
    File name: key.txt
    Content: 'May I come in?'
    Branch: master

bandit31@bandit:/tmp/q/repo$ echo "May I come in?" > key.txt

bandit31@bandit:/tmp/q/repo$ git add key.txt
The following paths are ignored by one of your .gitignore files:
key.txt
Use -f if you really want to add them.

bandit31@bandit:/tmp/q/repo$ rm .gitignore
bandit31@bandit:/tmp/q/repo$ git add key.txt
bandit31@bandit:/tmp/q/repo$ git commit -m "Added key.txt"
[master bb78878] Added key.txt
 1 file changed, 1 insertion(+)
 create mode 100644 key.txt

bandit31@bandit:/tmp/q/repo$ git push
    Could not create directory '/home/bandit31/.ssh'.
    The authenticity of host 'localhost (127.0.0.1)' can't be established.
    ECDSA key fingerprint is SHA256:98UL0ZWr85496EtCRkKlo20X3OPnyPSB5tB5RPbhczc.
    Are you sure you want to continue connecting (yes/no)? yes
    Failed to add the host to the list of known hosts (/home/bandit31/.ssh/known_hosts).
    This is a OverTheWire game server. More information on http://www.overthewire.org/wargames

    bandit31-git@localhost's password:
    Counting objects: 3, done.
    Delta compression using up to 2 threads.
    Compressing objects: 100% (2/2), done.
    Writing objects: 100% (3/3), 325 bytes | 0 bytes/s, done.
    Total 3 (delta 0), reused 0 (delta 0)
    remote: ### Attempting to validate files... ####
    remote:
    remote: .oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.
    remote:
    remote: Well done! Here is the password for the next level:
    remote: 56a9bf19c63d650ce78e6ec0354ee45e
    remote:
    remote: .oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.oOo.
    remote:
    To ssh://localhost/home/bandit31-git/repo
     ! [remote rejected] master -> master (pre-receive hook declined)
    error: failed to push some refs to 'ssh://bandit31-git@localhost/home/bandit31-git/repo'
    bandit31@bandit:/tmp/q/repo$

ssh bandit32@localhost
56a9bf19c63d650ce78e6ec0354ee45e

Done!

---

Bandit Level 32 → Level 33

After all this git stuff its time for another escape. Good luck!
Commands you may need to solve this level

sh, man
---
$0
and you're in a normal shell.

$ cat /etc/bandit_pass/bandit33
c9c3199ddf4121b10cf581a98d51caee

ssh bandit33@localhost
c9c3199ddf4121b10cf581a98d51caee

Done!
---
