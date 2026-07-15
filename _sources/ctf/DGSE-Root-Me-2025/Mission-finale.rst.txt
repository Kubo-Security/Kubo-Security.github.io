Mission finale
================

Enonce 
---------

Brief de mission
~~~~~~~~~~~~~~~~~~~~

Vous avez mené, avec brio, l'ensemble des missions qui vous ont été confiées, depuis l'identification jusqu'à l'intrusion du groupe attaquant. Cependant, une question demeure : qui se cache réellement derrière ce groupe ?

Nous vous confions cette ultime mission dans le but de retrouver et d'arrêter une bonne fois pour toutes le groupe NullVastation. Nous souhaiterions obtenir le nom et le prénom d'un de ses membres. Il est possible que, durant les autres missions, vous ayez relevé des indices susceptibles de vous aider à l'identifier.

Bonne chance, agent.

Objectifs de la mission
~~~~~~~~~~~~~~~~~~~~~~~~~
Récupérer le nom et le prénom d'un membre du groupe NullVastation.
Analyser les preuves et informations collectées.
Si besoins, infiltrez leurs serveurs.
Le flag est sous la forme RM{nom.prenom}

Exploit
--------


D'abord on re vérifie ce qu'on a trouvé dans les anciens challenges.
Dans le vault.kdbx on trouve ceci : 

- Username : operator
- Password : LGSA5l1%YHngd&GbjxR4Or
- Notes : SSH password for the attacking machine. The IP address changes regularly, please refer to the last operation to obtain it.

On va donc rechercher une IP qui correspondrait. 

On trouve cette IP dans le challenge APK : 163.172.67.201

Sur le serveur on trouve 3 tools dans un dossier, le README de nightshade contient notamment ceci : "by voidSyn42"

On cherche donc ce pseudo dans google, et on tombe sur ce github : https://github.com/voidsyn42/

Cela nous permet de récupérer cette adresse mail : syn.pl42@proton.me

On va sur epios, on retrouve son compte google, lié a Googlemap et avec son nom prénom : Pierre Lapresse