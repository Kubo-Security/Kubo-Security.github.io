OSINT - Doctor Es Langues
=============================

Description 
-----------------
En farfouillant bien, vous devriez trouver le petit compte d'un chercheur qui utilise cette image en bannière. En étudiant son CV, vous trouverez qu'il maîtrise une langue à un niveau "confirmé”. Quelle est cette langue ? 

Format : 404CTF{klingon}

Règle d'or en OSINT, n'interagissez PAS avec les personnes qui font l'objet de vos recherches. Ce n'est pas nécessaire.

Solution 
----------------

Dans les métadonnées on trouve : Image de banniere Twitter de SadiQuatrenot

Donc on se rend sur twitter : https://x.com/SadiQuatrenot 

C'est bien notre utilisateur. 

On trouve son CV ici : https://x.com/SadiQuatrenot/status/2052874427444965571/photo/1 

Et surtout son blog perso : https://www.blogger.com/profile/15995011235130208479 

Avec la wayback machine on trouve une ancienne version de son CV avec la langue "malais"

https://web.archive.org/web/20260508113340/https://quiestsadiquatrenot.blogspot.com/2026/05/presentation.html 

Flag : **404CTF{malais}**