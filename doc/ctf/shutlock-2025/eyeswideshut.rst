Eyes Wide Shut 
================

Enoncé 
-----------
Lors de sa candidature, un candidat a mentionné un pseudo utilisé sur les réseaux sociaux : @pierot_j25.

Votre mission, si vous l'acceptez, sera de retracer sa journée du 15/04/2025. Pour cela, nous aurons besoin des informations suivantes :

- Heure de la séance de cinéma
- Cocktail qu'il a consommé
- Hôtel où il a résidé À vous de jouer
- Format : SHLK{HeureSéance_NomCocktail_NomHôtel}

Exemple : {14-35_Espresso-Martini_Plaza-Athenee}

Précision : Le mot “hôtel” n’est pas compris dans le nom (Hôtel Plaza Athénée est : Plaza Athénée)


Résolution 
-------------

Le cinema 
~~~~~~~~~~

On commence par faire des recherches sur pierot_j25

On trouve rapidement un compte twitter : https://x.com/pierot_j25 

Sur le twitter on a des informations sur le cinéma et le film qui a été vu. 

On retrouve ce cinéma : https://maxlinder.com/#programme grâce à l'image et a google image.

Pas beaucoup de cinéma tout noir avec des balcons à paris 

Et les informations sur Nathan Nazario (nom écrit au générique) nous donne la liste des films sur lesquels il a participé : https://www.imdb.com/fr/name/nm1018585/ 

Depuis la wayback machine on voit bien qu'il y a une séance le 15/04 pour Requiem For a Dream : https://web.archive.org/web/20250409185444/https://maxlinder.com/#programme 

On peut vérifier que c'est bien le bon film avec https://www.youtube.com/watch?v=3tGNroBDAko a 3m11 on a exactement la photo.

On est donc bien dans le bon cinéma, avec le bon film, mais a quelle séance ? 

19h30 ou 21h30 car il indique avoir le film le soir. On pourra toujours tester les deux. 

Un peu plus tard dans nos recherches on trouve : https://letterboxd.com/pierot_j25 

On peut donc confirmer que la séance est celle de 21h30

Le cocktail
~~~~~~~~~~~~~~

On trouve égalemenet un instagram ave son pseudo avec une image d'une boisson dans la story : 

https://www.instagram.com/pierot_j25/ 

A priori on cherche un bar a jeux dans paris.

A force de chercher des bars a jeux je suis tombé sur cette image et ce blog dans google image avec la recherche "Paris bar a jeux" : https://www.elle.fr/Loisirs/Sorties/Bars/Bars-a-jeux/Le-bar-a-jeux-Le-dernier-bar-avant-la-fin-du-monde 

En regardant un peu plus les photos du bar, je suis certain que c'est la ! 

https://www.google.fr/maps/place/Dernier+Bar+avant+la+Fin+du+Monde/@48.857885,2.3463897,3a,75y,90t/data=!3m8!1e2!3m6!1sCIHM0ogKEICAgID41f_lwwE!2e10!3e12!6shttps:%2F%2Flh3.googleusercontent.com%2Fgps-cs-s%2FAC9h4nqjbTzqK5wDNPeGoErdcoa1we5MUp-sfb3rVZYk_qxHMJQ_Hktqm-1UEvK248MjKe15yOQr4HSgjrTNq4tXrLEft4n9TYS9xxZov9OHEmfGrYS11v0wTM6ObzqJPLbMfY_tcQjNJQ%3Dw203-h114-k-no!7i4160!8i2340!4m9!3m8!1s0x47e66e1fbb62c9cb:0x6e4a84bf271f7439!8m2!3d48.857885!4d2.3463897!10e5!14m1!1BCgIYIg!16s%2Fg%2F11cfclz4q?hl=fr&entry=ttu&g_ep=EgoyMDI1MDYxNy4wIKXMDSoASAFQAw%3D%3D

On a l'étagère, la statue dans le cube, les barreaux.

Leur site : https://dernierbar.com/pages/paris 

Le menu : https://cdn.shopify.com/s/files/1/0558/4073/5364/files/Kraken_Tribune_-_Cthulhu.pdf?v=1742814218

Dans le menu on peut lire ceci : 

.. code-block:: console

    Arrakis (Short drink, Épicé) ...............................................10,00 €
    “L’espérance ternit l’observation”
    Tequila, jus d’ananas, jus de citron vert, jus de pomme infusé au piment vert,
    sel, piment d’Espelette et crème de framboise.

Avec la citation pas de doute, c'est le bon cocktail ! 

L'hotel
~~~~~~~~~~~~~~

On peut penser à un hotel proche du cinéma, mais il semble y en avoir beaucoup, on est en plein Paris quoi ! 

https://www.google.fr/maps/place/Max+Linder+Panorama/@48.8714006,2.3440201,18.5z/data=!3m1!5s0x47e66e3e084488c5:0x13a57db3692579e3!4m6!3m5!1s0x47e66e3e08373b25:0x37db9d405df3b2ab!8m2!3d48.871381!4d2.3448472!16s%2Fm%2F0w6dqw5?hl=fr&entry=ttu&g_ep=EgoyMDI1MDYxNy4wIKXMDSoASAFQAw%3D%3D 

Sur l'image du cocktail on peut voir un truc rose en caoutch avec 3 étoiles, peut être un hotel 3 étoiles ? 

Ou un hotel entre le cinéma et le bar a jeu 

En cherchant porte clé hotel rose on trouve des portes clés de ce style : 

.. figure:: ../_static/img/shutlock-2025/clehotel.png
    :alt: Pride and notice
    :align: center
    :width: 1200

C'est pas exactement le même mais ça ressemble fort, c'est donc bien la bonne piste pour retrouver l'hotel ! 

https://www.sororityshop.com/collections/motel-key-chains

Après avoir testé plein d'hotel rose, et des hotels pas en France qui ont un porte clé similaire, c'était finalement la réponse la plus "évidente". 

On a testé **Grand Budapest** et c'était bien ça ! C'est en lien avec le thème du CTF !

Flag : **SHLK{21-30_Arrakis_Grand-Budapest}**

