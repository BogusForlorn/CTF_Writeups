We're given an AD1 file for analysis

AD1 foren evidence for attack breach
Look through the AD1 for common artefacts left after attack (\windows, \desktop, \documents, \appdata\ local and roaming, \programdata etc)
Found in \programdata\temp for the ps1 file, hehe.txt, ntds.dit, and SYSTEM
EnableAllTokenPrivs.ps1 tells us is SeBackupPrivilege from reading the powershell script
Part 1 of the flag is found, the rest (pt 2-4) need to dig from SYSTEM and ntds.dit so I used impacket

```
$ impacket-secretsdump -ntds ntds.dit -system SYSTEM LOCAL -outputfile ntds_hashes
Impacket v0.13.0.dev0 - Copyright Fortra, LLC and its affiliated companies
 
[*] Target system bootKey: 0xc29fc95250a61859970066fef6a347df
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Searching for pekList, be patient
[*] PEK # 0 found and decrypted: b5501e9efdf289f713ab2bd9248e2be3
[*] Reading and decrypting hashes from ntds.dit
Administrator:500:aad3b435b51404eeaad3b435b51404ee:cf3a5525ee9414229e66279623ed5c58:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
…
mssql_svc$:aes128-cts-hmac-sha1-96:333616dd08c9a793a5bf5ebbc96c8176
mssql_svc$:des-cbc-md5:1315c89e7034e683
exchange_svc$:aes256-cts-hmac-sha1-96:101380f7811b1785e75567c025bcfd19e043c1302203e5e961daa99508581f9a
exchange_svc$:aes128-cts-hmac-sha1-96:757b663b2191e3dbf530367860933def
exchange_svc$:des-cbc-md5:0757dae9ec5e2c43
[*] Cleaning up...
```

Then I cracked the ntlm hashes with hashcat

```
D:\CTF\Tools\hashcat-6.2.6\hashcat-6.2.6>hashcat -m 1000 -a 0 -d 1 ntds_hashes.ntds rockyou.txt --username --show
Administrator:cf3a5525ee9414229e66279623ed5c58:Welcome1
Guest:31d6cfe0d16ae931b73c59d7e0c089c0:
dllm.hk\hildagard.deana:26ab2086d837e748eaa3af6f7710f361:bandit
dllm.hk\kassia.dotti:1c2f7f3b20a7a3c512c72c6551d5c8ae:8675309
dllm.hk\paulina.allyson:1b5fd36fd806997ad2e1f5ac2c37155b:ncc1701
dllm.hk\kerr.rhianna:1b5fd36fd806997ad2e1f5ac2c37155b:ncc1701
dllm.hk\hyacinth.rayshell:1b5fd36fd806997ad2e1f5ac2c37155b:ncc1701
dllm.hk\daffy.mirabelle:f0e5ca8c0726e882a6d08ee2fecf6010:dakota
dllm.hk\harmonia.niki:38d30167abc65c61fc1a60348e537b60:kitten
http_svc$:31c72c210ecc03d1eae94fa496069448:sunshine
```

There's the rest of our flag
```
Kassia.dotti has 8675309 for pw
The password reused is ncc1701
Our admin pw is Welcome1
```

Flag: SIBER25{SeBackupPrivilege_kassia.dotti_Welcome1_ncc1701}
