Pride and Notice 1/2
=======================

Enoncé 
---------

Une candidature spontanée d'une certaine "Emily P." nous intrigue. Certains indices nous laissent penser qu'elle opère sous un alias : "Emma L.", influenceuse lifestyle au goût prononcé pour le luxe et les voyages.

Vous devez découvrir dans quel hôtel elle séjourne.

SHLK{nom-d-hotel}

Résolution
--------------

On recherche le pseudo de l'adresse mail fournit dans le cv "prideandpalm78" dans idcrawl, on trouve un compte pinterest : https://fr.pinterest.com/prideandpalm78/_created/

Sur ce compte pinterest il y a une image avec un autre pseudo : emmakesmistakes 

On va faire un idcrawl sur ce pseudo, et on trouve un compte instagram : https://www.instagram.com/emmakesmistakes/

Sur le compte Instagram on trouve une story avec des images d'une chambre avec un rideau un peu particulier

On va faire un google image sur le rideau avec en recherche "paris hotel" 

On tombe finalement sur cet hotel qui semble correspondre : https://www.booking.com/hotel/fr/de-senlis.fr.html?activeTab=photosGallery 

On tente le flag : **SHLK{madeleine-de-senslis}** et c'est bon !


Pride and Notice 2/2
=======================

Enoncé 
-----------

Emma a donc menti sur son identité. Le but est d'en apprendre le plus possible sur ses fréquentations. Nous la soupçonnons d'avoir communiqué avec un potentiel recruteur d'un pays étranger vers le mois d'avril.

Nous avons obtenu un autre pseudo qu'elle semble utiliser sur les réseaux : LaGafferie22.

Trouvez l'endroit de la rencontre.

Format du flag : SHLK{RUE1,RUE2}

Résolution
-----------

On commence par un idcrawl avec le nouveau pseudo : LaGafferie22 

On trouve ce compte twitter : https://x.com/LaGafferie22 

Ce tweet est intéressant : https://x.com/LaGafferie22/status/1918612603196039369 

On chercherait un email avec prenon.nom.anneedenaissance@gmail.com 

Il nous manque son année de naissance. 

On va tester dans Epieos "en mode bruteforce" : 

- emma.lagaffe.2003 : Non
- emma.lagaffe.2002 : Oui 

On obtient donc son adresse gmail : emma.lagaffe.2002@gmail.com

Et surtout son calendrier google : https://calendar.google.com/calendar/u/0/embed?src=emma.lagaffe.2002@gmail.com 

Il y a un rendez vous le 17 Avril 2025, avec un lien ves une image : https://imgur.com/a/j9qqsxb 

Ce qu'on peut déduire avec cette image : 

- La rue se situe au Canada, à Montréal (les panneaux dos d'âne et stationnement interdit)
- La rue est sens unique (toutes les voitures dans le même sens)
- Il y a probablement une piste cyclable sur la droite
- Il y a une église avec 2 tours pointues à la même hauteur au loin
- Le sens unique va vers l'église 
- A priori le quartier rosemont (google lens, rue similaire dans ce quartier)
- On n'est pas dans la rue de l'église, mais une a deux rues à côté

On va donc regarder un peu les églises dans ce quartier, soit sur gmaps soit avec google earth directement on en trouve deux : 

- Eglise catholique de Sainte Cécile 
- Bureau de la Paroisse Saint-Edouard 

Avec google earth, on va essayer de se positionner au même endroit que la prise de la photo : 

.. figure:: ../_static/img/shutlock-2025/prideandnotice.png
    :alt: Pride and notice
    :align: center
    :width: 1200

On a bien l'église en face, on est dans une rue en sens unique, et il y a même le tag sur le mur en haut a gauche. 

On vérifie avec Google Maps, et on trouve les deux rues : 

- Rue Saint-Vallier
- Boulevard Rosemont 

On a notre flag : **SHLK{SAINT-VALLIER,ROSEMONT}**