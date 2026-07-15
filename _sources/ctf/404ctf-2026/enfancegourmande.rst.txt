OSINT - Enfance Gourmande
=============================

Description 
-----------------
Quand vous étiez jeune, vous accompagniez régulièrement votre père chercheur durant ses déplacements. Mais un repas dans un hôtel en particulier brûle en vos souvenirs : un peu avant le réveillon, en plein confinement, vous y aviez mangé "l'entrecôte de boeuf cuite au barbecue, accompagnée des légumes du potager du Roi". Mais vous avez beau vous creuser la cervelle, impossible de retrouver le nom du lieu ! Seules quelques images vous reviennent par flash quand vous essayez de vous souvenir. Parviendrez vous à retrouver le nom de l'hôtel de ce restaurant, et le prix du plat ? Format : 404CTF{plaza_athenee-12}

Solution 
----------------

Une vidéo est fournie pour ce challenge, on y voit "Relais & Chateaux", puis la vue d'un etang, et enfin la tour effeil.

On peut retrouver facilement la liste des hotels / restaurant relais & chateaux ici : https://www.relaischateaux.com/fr/destinations/europe/france/autour-de-paris/ 

On en trouve seulement quelques un autour de Paris. 

En regardant un peu mieux la vidéo, on peut voir que le jour se lève derrière la tour effeil. Donc on est à l'ouest de cette dernière. 

Et le seul proche d'un etang, et qui est à l'ouest c'est : https://www.relaischateaux.com/fr/hotel/les-etangs-de-corot/ 

On peut confirmer que c'est bien le restaurant car le chef Rémi Chambard, se fournit au potager du roi. 

On a donc la première partie du flag : **les_etangs_de_corot**.

Pour le prix de l'entrecôte, après avoir fait tout le site et une multitude de dorks, toujours rien. 

On sait que la personne a mangé son entrecôte entre le 30 octobre et le 15 décembre 2020 ("pendant le confinement peu de temps avant le reveillon"). 

On va donc regarder les réseaux sociaux de l'établissement. On trouve rapidement un compte facebook : 

https://www.facebook.com/LesEtangsDeCorot/photos?locale=fr_FR 

On cherche à la bonne période et on trouve cette photo : https://www.facebook.com/photo.php?fbid=3756206437744047&set=pb.100063735846176.-2207520000&type=3&locale=fr_FR

Qui contient un lien shopify : https://etangs-de-corot.myshopify.com/ 

Mais le lien est mort, c'est là qu'on utilise la wayback machine : 
https://web.archive.org/web/20201001000000*/https://etangs-de-corot.myshopify.com/ 

On a donc notre prix : **60** et notre flag : **404CTF{les_etangs_de_corot-60}**