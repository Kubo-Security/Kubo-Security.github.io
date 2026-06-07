OSINT - Monsieur C : Découverte
=================================

Description 
-----------------
Un étrange personnage se présentant comme "Monsieur C" est venu me voir pour faire de la pub pour son nouveau blog en partageant son URL. Il était si sympathique que je n'ai pu refuser. Quel est l'URL du blog ?

Format du flag : 404CTF{blog.404ctf.fr}

Solution 
----------------

crt.sh/?q=404ctf.fr 

On y trouve : https://lab.c.404ctf.fr/

On le visite, c'est bien notre site.

Flag : **40CTF{lab.c.404ctf.fr}


OSINT - Monsieur C : Voyage voyage
=====================================

Description 
-----------------

Monsieur C est récemment parti en vacances. Il y a quelques siècles, un explorateur français s'était lui aussi rendu pour la première fois sur ce lieu, accompagné d'un naturaliste. Comment s'appelle ce naturaliste ?

Format du flag : 404CTF{jean-jean_quete}

Solution 
----------------

Grace a son blog on sait qu'il a une chaine youtube. 

On trouve sa chaine Youtube avec son nom : https://www.youtube.com/@IntCarlos

Sa vidéo de vacances : https://www.youtube.com/watch?v=Y-mu1x0LMZs 

On y voit cette fleur : Leucheria suaveolens 

.. code-block:: console 

    https://identify.plantnet.org/fr/k-world-flora/species/Leucheria%20suaveolens%20(d'Urv.)%20Speg./data 

    https://en.wikipedia.org/wiki/Leucheria_suaveolens 

Elle est endémique des iles malouines : https://en.wikipedia.org/wiki/Falkland_Islands 

On cherche "explorateur français iles malouines", on trouve : https://fr.wikipedia.org/wiki/Louis-Antoine_de_Bougainville 

CTRL+f "naturaliste" : "Il est accompagné pour ce voyage d'Antoine-Joseph Pernety qui officie en tant qu’aumônier et naturaliste."

Flag : **404CTF{antoine-joseph_pernety}**



OSINT - Monsieur C : Somebody Is Watching Me
=================================================

Description 
-----------------

L'individu surveillé cache probablement des informations d'importance capitale chez lui. D'après nos renseignements, il dispose d'un dispositif de surveillance à son domicile. Encore faut-il trouver un moyen d'y accéder...

Solution 
----------------

Dans l'une des images de cette vidéo on peut voir un tableau avec un login et un mot de passe : https://www.youtube.com/shorts/cR_RMPZ6QF8 

rootcam:W19ub7d08! 

On se connecte https://lab.c.404ctf.fr/login 

Et on obtient le flag : **404CTF{67}**


OSINT - Monsieur C : Fan de sciences
=======================================

Description 
-----------------

Qui est le scientifique préféré de "Monsieur C" ?

Format du flag : 404CTF{jean_quete}

Solution 
----------------

Après de nombreuses recherches, on s'est enfin intéressé au numéro de téléphone,

Qui nous donne un compte duolingo datant de 2022, avec Epieos, cela ne semblait pas être une piste convenable. 

Cependant en l'ajoutant au répertoire de contact de notre téléphone, et en regardant dans WhatsApp, on découvre un compte avec une photo. 

La personne sur la photo est Camille Guérin 

Flag : **404CTF{camille_guerin}