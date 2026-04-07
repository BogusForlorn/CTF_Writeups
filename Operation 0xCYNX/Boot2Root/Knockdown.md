# B2R Knockdown Solution

Challenge: Knockdown

Difficulty: Hard

# Recon

```
$ nmap -sC -sV -p 80,7000,8000,9000,10000 192.168.203.141
Starting Nmap 7.98 ( https://nmap.org ) at 2026-03-01 22:35 -0500
Nmap scan report for 192.168.203.141
Host is up (0.00088s latency).

PORT      STATE SERVICE           VERSION
80/tcp    open  http              Microsoft IIS httpd 10.0
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-title: IIS Windows Server
7000/tcp  open  afs3-fileserver?
8000/tcp  open  napster           WinMX or Lopster Napster P2P client
9000/tcp  open  cslistener?
10000/tcp open  snet-sensor-mgmt?
3 services unrecognized despite returning data. If you know the service/version, please submit the following fingerprints at https://nmap.org/cgi-bin/submit.cgi?new-service :
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port7000-TCP:V=7.98%I=7%D=3/1%Time=69A50588%P=x86_64-pc-linux-gnu%r(NUL
SF:L,2,"3\n");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port9000-TCP:V=7.98%I=7%D=3/1%Time=69A50588%P=x86_64-pc-linux-gnu%r(NUL
SF:L,2,"2\n");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port10000-TCP:V=7.98%I=7%D=3/1%Time=69A50588%P=x86_64-pc-linux-gnu%r(NU
SF:LL,2,"4\n");
MAC Address: 00:0C:29:93:80:28 (VMware)
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.58 seconds
```

Like most B2R boxes, we start off with port scanning. The nmap result above shows us that we have the typical 80/http port open (leads to nowhere), and 4 other ports running unknown services. When nmap finds unrecognized services, it would send NULL probes to see what the server expects (via the error codes or in this case, the fingerprintining). The output only shows numbers in ascending order, like a sequence, likely telling us which port in which sequence. This is referred to as "Port Knocking". Its a cool concept of security through obscurity, but its largely impractical in the real world.
We'll give the port knocking a try:

``` $ knock 192.168.203.141 8000 9000 7000 10000 # knock the ports in these sequences according to the service fingerprint ```

Now, if we perform another nmap scan, we should be able to see more. This is sorta like a "knock on the door passcode" to tell the server to do something. In this case, it is opening more ports to services.

```
$ nmap -sC -sV -p- -Pn 192.168.203.141 --min-rate 2000
Starting Nmap 7.98 ( https://nmap.org ) at 2026-03-01 22:45 -0500
Stats: 0:00:01 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 1.27% done; ETC: 22:47 (0:01:17 remaining)
Stats: 0:00:56 elapsed; 0 hosts completed (1 up), 1 undergoing SYN Stealth Scan
SYN Stealth Scan Timing: About 84.92% done; ETC: 22:46 (0:00:10 remaining)
Stats: 0:01:22 elapsed; 0 hosts completed (1 up), 1 undergoing Service Scan
Service scan Timing: About 60.00% done; ETC: 22:47 (0:00:11 remaining)
Nmap scan report for 192.168.203.141
Host is up (0.00076s latency).
Not shown: 65520 filtered tcp ports (no-response)
PORT      STATE SERVICE           VERSION
22/tcp    open  ssh               OpenSSH for_Windows_9.2 (protocol 2.0)
| ssh-hostkey: 
|   256 58:09:68:62:33:50:1a:6b:4f:c2:33:68:64:44:57:59 (ECDSA)
|_  256 82:8b:0f:4a:2f:87:6b:e6:59:00:e5:db:3a:d3:7c:8f (ED25519)
80/tcp    open  http              Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
135/tcp   open  msrpc             Microsoft Windows RPC
139/tcp   open  netbios-ssn       Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds      Windows Server 2019 Standard 17763 microsoft-ds
7000/tcp  open  afs3-fileserver?
8000/tcp  open  napster           WinMX or Lopster Napster P2P client
9000/tcp  open  cslistener?
10000/tcp open  snet-sensor-mgmt?
49664/tcp open  msrpc             Microsoft Windows RPC
49665/tcp open  msrpc             Microsoft Windows RPC
49666/tcp open  msrpc             Microsoft Windows RPC
49667/tcp open  msrpc             Microsoft Windows RPC
49668/tcp open  msrpc             Microsoft Windows RPC
49670/tcp open  msrpc             Microsoft Windows RPC
3 services unrecognized despite returning data. If you know the service/version, please submit the following fingerprints at https://nmap.org/cgi-bin/submit.cgi?new-service :
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port7000-TCP:V=7.98%I=7%D=3/1%Time=69A50829%P=x86_64-pc-linux-gnu%r(NUL
SF:L,2,"3\n");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port9000-TCP:V=7.98%I=7%D=3/1%Time=69A50829%P=x86_64-pc-linux-gnu%r(NUL
SF:L,2,"2\n");
==============NEXT SERVICE FINGERPRINT (SUBMIT INDIVIDUALLY)==============
SF-Port10000-TCP:V=7.98%I=7%D=3/1%Time=69A50829%P=x86_64-pc-linux-gnu%r(NU
SF:LL,2,"4\n");
MAC Address: 00:0C:29:93:80:28 (VMware)
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb-os-discovery: 
|   OS: Windows Server 2019 Standard 17763 (Windows Server 2019 Standard 6.3)
|   Computer name: WIN-SJR239VHPGG
|   NetBIOS computer name: WIN-SJR239VHPGG\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2026-03-02T11:47:44+08:00
|_clock-skew: mean: -2h39m59s, deviation: 4h37m07s, median: 0s
|_nbstat: NetBIOS name: WIN-SJR239VHPGG, NetBIOS user: <unknown>, NetBIOS MAC: 00:0c:29:93:80:28 (VMware)
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2026-03-02T03:47:43
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 160.75 seconds
```

We clearly see more ports open now, and we can proceed to cracking this box open.
First, I want to see if I can do null auth on SMB:
```
$ smbmap -H 192.168.203.141 -u Guest -p ""             

    ________  ___      ___  _______   ___      ___       __         _______
   /"       )|"  \    /"  ||   _  "\ |"  \    /"  |     /""\       |   __ "\
  (:   \___/  \   \  //   |(. |_)  :) \   \  //   |    /    \      (. |__) :)
   \___  \    /\  \/.    ||:     \/   /\   \/.    |   /' /\  \     |:  ____/
    __/  \   |: \.        |(|  _  \  |: \.        |  //  __'  \    (|  /
   /" \   :) |.  \    /:  ||: |_)  :)|.  \    /:  | /   /  \   \  /|__/ \
  (_______/  |___|\__/|___|(_______/ |___|\__/|___|(___/    \___)(_______)
-----------------------------------------------------------------------------
SMBMap - Samba Share Enumerator v1.10.7 | Shawn Evans - ShawnDEvans@gmail.com
                     https://github.com/ShawnDEvans/smbmap

[*] Detected 1 hosts serving SMB                                                                                                  
[*] Established 1 SMB connections(s) and 1 authenticated session(s)                                                      
                                                                                                                             
[+] IP: 192.168.203.141:445     Name: staff.0nyx.void           Status: Authenticated
        Disk                                                    Permissions     Comment
        ----                                                    -----------     -------
        ADMIN$                                                  NO ACCESS       Remote Admin
        archive                                                 NO ACCESS
        C$                                                      NO ACCESS       Default share
        Comms                                                   READ ONLY
        IPC$                                                    READ ONLY       Remote IPC
        Loot_Drop                                               READ ONLY
        Tools                                                   READ ONLY
[*] Closed 1 connections
```

I was able to authenticate and connect to SMB via the Guest account with no password. 


I tried to see if I could get some local user accounts 

```
$ impacket-lookupsid Guest@192.168.203.141 -no-pass
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[*] Brute forcing SIDs at 192.168.203.141
[*] StringBinding ncacn_np:192.168.203.141[\pipe\lsarpc]
[*] Domain SID is: S-1-5-21-730879702-844948771-66853788
500: WIN-SJR239VHPGG\Administrator (SidTypeUser)
501: WIN-SJR239VHPGG\Guest (SidTypeUser)
503: WIN-SJR239VHPGG\DefaultAccount (SidTypeUser)
504: WIN-SJR239VHPGG\WDAGUtilityAccount (SidTypeUser)
513: WIN-SJR239VHPGG\None (SidTypeGroup)
1000: WIN-SJR239VHPGG\r007 (SidTypeUser)
1001: WIN-SJR239VHPGG\crYp7 (SidTypeUser)
1002: WIN-SJR239VHPGG\backup-svc (SidTypeUser)
```

With the local accounts, I wanted to see if I could reuse the username as the password.
