OSINT - Secret Training
=================================

Partie 1 : Retrouver son identité
-----------------------------------

ColdSteelShoot :
https://twitter.com/ColdSteelShoot/with_replies

On découvre tous les mots spécifique à des régions de france sur le twitter : 

- La triple bise
- La poche 
- Les chocolatines
- L'étendoir
- Le crayon a papier
- Carafe
- Averse

On va donc récupérer les cartes de France et superposer les zones utilisants tous ces mots. 

Avec ça on trouve un seul département : L'aveyron

On sait que : 

Zone 1 : 
- 28 mars il fait beau
- 29 mars a priori il fait du vent
- 30 Mars MAtin il pleut
- Il n'y a pas de neige en ce moment mais elle semble être dans une station de ski
- Elle fait du rollers pour s'entrainer
- 4 Avril, beaucoup de vent
- 8 Avril, beaucoup de vent également
- 9 Avril : Elle va rentrer chez ses parents
- 10 Avril, il fait super chaud, toujours au même endroit

Zone 2 (L'aveyron, d'ou elle vient) :

- 12 Avril, arrivé chez les parents, c'est une petite ville ! 
- Dans la petite ville, il a 2 boulangeries qui porte le même nom a moins e 3 KM de distanc
- Elle rentre dans la zone 1 le 18

Avec Overpass Turbo, on retrouve assez facilement les boulangeries qui ont exactement le même nom et qui sont a moins de 3KM de distance : 

.. code-block:: console

    node["shop"="bakery"](area)({{bbox}})->.a;
        foreach.a->.elem(
    node.a(around.elem:3000)
        (if:istag("name")&&t["name"]==elem.u(t["name"]));
    (.; - .elem;);
    out;
    );

Les villes qui ressortent sont : Saint Affrique, Rodez, Ville-France-De-Rouergue
Malheureusement on ne trouve aucun commentaire sur aucune de ces  boulangeries. 

On va finalement, prendre la liste des petites villes de l'aveyron (entre 3k et 2k Habitant) et allez voir les commentaires de TOUTES les boulangeries une par une 

Saint Afrique 8k6
- Le fournil : non
- La grignote  : non
- Le beaulieu 1 : non
- Le beaulieu 2 : non
- Augier stéphane : non
- Bourrel Jean Marc : non
- Pétrin versolien : non
- saint jacques : non 
- anduz : non 
- azais : non

Villefranche de Rouergue 12k : 
- Marcel et thomas : non 
- Fournier mirau : non
- Maison bedel : non 
- marie blachère : non
- La panetière 1 : non 
- Maison viguié : non
- L'estanco, maison viguié : non
- La pannetière 2 : non 
- Fournil Arcanhac : non
- L'amandier : non 
- Verdier dominique : non 

Baraque ville 3k2 :
- L'épi du rouergue : non 
- Palous Michel : non
- Victoire : non
- Le serayet : non
- Lacombe Cyril : non 

Brozoul 3k : 
- Sainte fauste : non 
- Lacan Jean Raymond : non 
- Bouzou : non 
- Brosseau Alain : non 
- Lévézou : non 

Capdenac gare 4k5 : 
- Le plaisir du fournil : non 
- Le four a pain : non 
- Guibal Jérôme : non 
- Cabrit Philippe : non 
- Fizes : non 
- Montheuil : non 

Decazeville 5k4 : 
- Ptit blé doré : non
- Halles de la découverte : non
- Pon-pon : non 
- La Pannetière : non 
- Blanc Pascal : non 

Aubin 3k7 : 
- Le paradis des saveur : Bingo !
https://www.google.com/maps/contrib/105348503067788767785/reviews/@44.5274042,2.2440333,17z/data=!3m1!4b1!4m3!8m2!3m1!1e1?hl=fr&entry=ttu
- Boulangerie Aubin : 
- Boulangerie Saint Aubin : 

Pas si simple ! 

Partie 2 : Retrouver son hôtel
----------------------------------

Pour retrouver le lieu d'entraînement, il faut d'abord retrouver la zone 1 ! 

28 Mars : beau temps
29 mars : vent
30 Mars : averse dans la matinée
2 avril : Entrainement roller plus de neige
3 Avril : elle fait des course entre l'entrainement et l'hotel
4 Avril : Vent 21H
https://www.meteociel.fr/observations-meteo/temps-reel.php?archive=1&region=&jour=4&mois=4&annee=2024&heure=19&mode=&sub=OK

8 avril : Vent 12h51
https://www.meteociel.fr/observations-meteo/temps-reel.php?archive=1&region=&jour=8&mois=4&annee=2024&heure=13&mode=&sub=OK

10 Avril : très chaud 10h30
https://www.meteociel.fr/observations-meteo/temps-reel.php?archive=1&region=&jour=10&mois=4&annee=2024&heure=10&mode=&sub=OK

Je pense qu'il faut se concentré sur le vent, car très chaud, pas sûr que ça aide vraiment

On peut aussi corrélé avec les club de biathlon, ou de rollers : 

- Biathon : https://monclubpresdechezmoi.com/sport/444/
- Roller : https://monclubpresdechezmoi.com/sport/259/ 

On va commencer par Biathlon.

Finalement on se rend compte que la météo ne semble pas être la bonne piste, mais juste un prétexte pour placer les mots bourrasque, averse, etc 

On va donc reparti de son nom Lucie Crète et chercher d'autres pseudo

C'est idcrawl.com qui nous donnera la suite du challenge ! Un compte pinterest creteluciesport 

Avec les photos, on va chercher la route qui est indiqué sur le petit panneau blanc, 411 ou 471

On va allez sur overpass turbo et chercher toutes les départementales 411 et 471, et on regarde lesquel semble faire au moins 34 km et qui sont dans des régions de montagnes : 

.. code-block:: console

    /*
    Cette requête est générée par l'outil overpass-turbo.
    La recherche initiale est 
    "Routes nationales"
    */
    [out:json][timeout:200];

    // récupération des résultats

    ( 
    way["highway"]["ref"~"^D 411"]({{bbox}});
    way["highway"]["ref"~"^D 471"]({{bbox}});
    );

    // afficher les résultats
    out body;
    >;
    out skel qt;

    /* feuille de style MapCSS */
    {{style:

    way {color: #f01d08;  width: 3;}
    
    }}

Le panneau 34 : 
https://www.google.fr/maps/@46.7557243,5.9340233,3a,75y,313.57h,90t/data=!3m10!1e1!3m8!1sBD37giOwxsXSxHglA4DV5w!2e0!6shttps:%2F%2Fstreetviewpixels-pa.googleapis.com%2Fv1%2Fthumbnail%3Fpanoid%3DBD37giOwxsXSxHglA4DV5w%26cb_client%3Dmaps_sv.tactile.gps%26w%3D203%26h%3D100%26yaw%3D299.90137%26pitch%3D0%26thumbfov%3D100!7i16384!8i8192!9m2!1b1!2i52?ucbcb=1&entry=ttu

Le rond point : 
https://www.google.fr/maps/@46.7465587,5.9226095,3a,75y,314.48h,89.75t/data=!3m10!1e1!3m8!1sSkJmM60Mmxg7Svrq8bAJKQ!2e0!6shttps:%2F%2Fstreetviewpixels-pa.googleapis.com%2Fv1%2Fthumbnail%3Fpanoid%3DSkJmM60Mmxg7Svrq8bAJKQ%26cb_client%3Dmaps_sv.tactile.gps%26w%3D203%26h%3D100%26yaw%3D7.844741%26pitch%3D0%26thumbfov%3D100!7i16384!8i8192!9m2!1b1!2i52?ucbcb=1&entry=ttu

On va tester l'hotel le plus proche : le bois dormant et c'était la bonne réponse !