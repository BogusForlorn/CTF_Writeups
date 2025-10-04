Since the chall describes “recover the password to authenticate the domain”, we don't need to look for local accounts or random app credentials. Just the domain account. From that, we just scour the entire image for any domain accounts lol
Initially I found the SYSTEM and SECURITY. But that wasn't it. It didn't return any DCC2 entries so no hash route
```
$ impacket-secretsdump -system SYSTEM -security SECURITY LOCAL -outputfile cached.txt
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies
[*] Target system bootKey: 0xc29fc95250a61859970066fef6a347df
[*] Dumping cached domain logon information (domain/username:hash)
[*] Dumping LSA Secrets
[*] DPAPI_SYSTEM
dpapi_machinekey:0x2121131020f13977cdca7aae8e95b35bcfbc4d6f
dpapi_userkey:0xf20c9da1e7cbb1c2eb284d3cebecbebe54f05109
[*] NL$KM
0000 DA 9A 50 E1 02 54 F2 72 F0 15 67 C9 71 EB 60 DE ..P..T.r..g.q.`.
0010 4E 9A 98 F2 54 CA 47 67 2C 5C 77 8D 8D DE 41 15 N...T.Gg,\w...A.
0020 36 16 DC 89 B1 56 49 69 F4 5F C7 4D 0E 26 35 C3 6....VIi._.M.&5.
0030 AE DD 0C 8B 02 5A 42 71 FD BB 97 A1 E1 74 3A CA .....ZBq.....t:.
NL$KM:da9a50e10254f272f01567c971eb60de4e9a98f254ca47672c5c778d8dde41153616dc89b1564969f45fc74d0e2635c3aedd0c8b025a4271fdbb97a1e1743aca
[*] Cleaning up...
```

So then I tried moving to DPAPI. From digging around the image looking for accounts and credentials, I found some under the microsoft paths in AppData:
Extract the DP API artifacts from the image
```
\AppData\Roaming\Microsoft\Protect\S-1-5-21-2614640294-1474365119-3719948198-500\Preferred
\AppData\Roaming\Microsoft\Protect\S-1-5-21-2614640294-1474365119-3719948198-500\be259595-a91d-4fa6-ba1a-98dc06d523e2
\AppData\Loccal\Microsoft\Credentials\DFBE70A7E5CC19A398EBF1B96859CE5D
\AppData\Roaming\Microsoft\Credentials\ 5294ADE15AD956FD6FA8A96873C141FE & 7BD4FDF69A37D9AC6B211FD092C3998C
```

With these creds, we can just use impacket to crack the password. There's a hint in the title "Keys to the Domain" which is to use the DPAPI MasterKeys
So the first thing to do is to get the key:
```
$ impacket-dpapi masterkey -file "S-1-5-21-2614640294-1474365119-3719948198-500/be259595-a91d-4fa6-ba1a-98dc06d523e2" -sid "S-1-5-21-2614640294-1474365119-3719948198-500" -password 'Welcome1'
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies
[MASTERKEYFILE]
Version : 2 (2)
Guid : be259595-a91d-4fa6-ba1a-98dc06d523e2
Flags : 5 (5)
Policy : 0 (0)
MasterKeyLen: 000000b0 (176)
BackupKeyLen: 00000090 (144)
CredHistLen : 00000014 (20)
DomainKeyLen: 00000000 (0)
Decrypted key with User Key (SHA1)
Decrypted key: 0xe1b1b36ebbf138a35b7940cacbebd6dc76d4c15ab76a2a037588f56b70c67914a6170aaf84fb5dbc9f07a90208d3fcf0d5fc13bb2398b4240b0514b1ce029c15
Then with the key, we try to decrypt the creds to get the password
$ KEY='0xe1b1b36ebbf138a35b7940cacbebd6dc76d4c15ab76a2a037588f56b70c67914a6170aaf84fb5dbc9f07a90208d3fcf0d5fc13bb2398b4240b0514b1ce029c15'
# Roaming\Microsoft\Credentials
$ impacket-dpapi credential -file "Credentials/5294ADE15AD956FD6FA8A96873C141FE" -key "$KEY"
$ impacket-dpapi credential -file "Credentials/7BD4FDF69A37D9AC6B211FD092C3998C" -key "$KEY"
# Local\AppData\Local\Microsoft\Credentials
$ impacket-dpapi credential -file "Local MS Creds/DFBE70A7E5CC19A398EBF1B96859CE5D" -key "$KEY"
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies
[CREDENTIAL]
…
(2nd output)
LastWritten : 2025-08-13 15:41:33+00:00
Flags : 0x00000030 (CRED_FLAGS_REQUIRE_CONFIRMATION|CRED_FLAGS_WILDCARD_MATCH)
Persist : 0x00000003 (CRED_PERSIST_ENTERPRISE)
Type : 0x00000002 (CRED_TYPE_DOMAIN_PASSWORD)
Target : Domain:target=flag.dllm.hk
Description :
Unknown :
Username : dllm.hk\sum.tingwong Unknown : SIBER25{St0REd_cR3DEnT1aLs}
…
KeyWord : Microsoft_WindowsLive:authstate:31
Data :
0000 0F E8 CF A9 6E B3 A1 D1 B1 B5 41 2C 92 EC DD FE ....n.....A,....
0010 DA 40 66 65 27 D3 83 17 77 25 E4 37 54 45 6A 91 .@fe'...w%.7TEj.
0020 98 DE 40 00 00 00 17 5C 56 3E F3 50 A0 0D 22 8A ..@....\V>.P..".
0030 C8 9C F9 E5 BE C4 A4 7A 6A B6 74 D0 14 7E 00 59 .......zj.t..~.Y
0040 1E 53 6D 16 BE E7 6A 48 98 43 E5 0A F0 ED F9 41 .Sm...jH.C.....A
0050 0D 68 F9 7A 51 12 E3 1B 76 07 18 5A F7 6A 5C 76 .h.zQ...v..Z.j\v
0060 6F DF 58 57 19 8A o.XW..
```

Flag: SIBER25{St0REd_cR3DEnT1aLs}
