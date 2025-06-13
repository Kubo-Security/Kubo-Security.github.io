Mission 3
=============

Enoncé 
---------
Brief de mission
~~~~~~~~~~~~~~~~~~~~~~~

La nouvelle vient d'être annoncée : l'entreprise Quantumcore a été compromise, vraisemblablement à cause d'un exécutable téléchargé sur un appareil issu du shadow IT, dont l'entreprise ignorait l'existence.

Par chance — et grâce à de bons réflexes cyber — un administrateur système a réussi à récupérer une image de la machine virtuelle suspecte, ainsi qu'un fichier de capture réseau (PCAP) juste avant que l'attaquant ne couvre complètement ses traces. À vous d'analyser ces éléments et comprendre ce qu'il s'est réellement passé.

L'entreprise vous met à disposition :
- L'image de la VM compromise
- Le fichier PCAP contenant une portion du trafic réseau suspect
- Utilisateur : johndoe
- Mot de passe : MC2BSNRbgk

L'heure tourne, la pression monte, et chaque minute compte. À vous de jouer, analyste.

Objectifs de la mission
~~~~~~~~~~~~~~~~~~~~~~~
Identifier le vecteur d'intrusion.
Retracer les actions de l'attaquant.
Évaluer les données compromises.

Exploit 
-------------

Dans le fichier PCAP on trouve un fichier install_nptdate.sh qui montre comme le malware est installé

On retrouve le fichier du malware dans : /opt/fJQsJUNS/.sys

On retrouve également la commande crontab qui le lance : 

.. code-block:: console

    cat /etc/cron.d/.ntpdate_sync 
    @reboot root PYTHONPATH=/home/johndoe/.local/lib/python3.7/site-packages python3.7 /opt/fJQsJUNS/.sys &

On récupère le fichier .sys et on le transforme en .pyc pour le décompiler avec : uncompyle6 -o ./ test.pyc

On comprend en lisant le code que les données sont envoyés via des requêtes ping et chiffrées avec une clé AES

On va dans Cyberchef, on saisit la clé AES et l'IV, et on déchiffre les 3 premiers ping :

.. code-block:: console

    1 : a08508333fbef26b26cd9e17100edf59 == RM{986b8674b18e
    2 : 8eef494a4028c635873e2b16293b0e7d == 7f3c36b24cf8c81
    3 : c1d24536d469f7cffac27bac054c66ab == 95b36bba01d61}

Flag : **RM{986b8674b18e7f3c36b24cf8c8195b36bba01d61}**