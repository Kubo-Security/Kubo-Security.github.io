Dockerflag 
=======================

On a un fichier docker au format .tar, donc provenant de la commande docker save

On va utiliser l'outil dive, pour analyser le contenu du docker avec : dive docker-archive://dockerflag.tar 

On remarque qu'un repo .git est copier puis supprimer à la fin 

L'ID du layer qui copie le .git est : c0f44320de6915ebd75512f6564344e5aac1b91cb82573690a8061f561804aad

On va donc décompresser le .tar et décompressé c0f44320de6915ebd75512f6564344e5aac1b91cb82573690a8061f561804aad.tar.gz

Dedans on récupère le .git, mais celui ci n'est pas opérationnel et on ne peut donc pas voir les commits. 

Le fichier .git/logs/HEAD nous donne la liste des commits : 

.. code-block:: console

    0000000000000000000000000000000000000000 c8e66485c89a29768dd546a3046b8544520615d6 Alba Laine <stagiare@docker.flag> 1741022248 +0000    commit (initial): Source code of website
    c8e66485c89a29768dd546a3046b8544520615d6 3d0717cb911d00b3e5033ba8c0c83df069e3e144 Alba Laine <stagiare@docker.flag> 1741022248 +0000    commit: Last commit before week-end !
    3d0717cb911d00b3e5033ba8c0c83df069e3e144 514443de0db750428f03d41d2be47e8c6d066981 Alba Laine <stagiare@docker.flag> 1741022248 +0000    commit: Add static ressources
    514443de0db750428f03d41d2be47e8c6d066981 b34c648f6790f6dee4340767ddf4b077f639132d Alba Laine <stagiare@docker.flag> 1741022248 +0000    commit: Requirements of website
    b34c648f6790f6dee4340767ddf4b077f639132d e3a5491ad536b35974022c3b521d3b48880afb68 Alba Laine <stagiare@docker.flag> 1741022248 +0000    commit: Add HTML website

On va réinitialiser le repo git sur le dernier commit : git reset --hard e3a5491ad536b35974022c3b521d3b48880afb68

A partir de la, on peut utiliser les commandes git, on va donc faire un git log, puis un git show de chaque commit jusqu'à trouver quelque chose d'intéressant.

Sur le deuxième commit : git show 3d0717cb911d00b3e5033ba8c0c83df069e3e144 

On obtient : SECRET="404CTF{492f3f38d6b5d3ca859514e250e25ba65935bcdd9f4f40c124b773fe536fee7d}

