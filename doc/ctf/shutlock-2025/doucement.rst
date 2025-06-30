Doucement le matin, pas trop vite le soir
=============================================

Enoncé 
-----------
Jacques Huppert, informaticien de 45 ans, passionné de cinéma, a postulé à un poste de développeur au sein du Ministère de l'Intérieur. Tout semble indiquer qu’il s’agit d’un professionnel expérimenté. Pourtant ses traces numériques racontent une toute autre histoire…

Éléments extraits de sa fiche de candidature :

- Champ : Valeur
- Prénom : Jacques
- Nom : Huppert
- Âge : 45 ans
- Profession : Informaticien
- Passion : Cinéma
- Instagram	: @jacques.huppert
- Threads : @jacques.huppert

Format du flag : SHLK{AdresseEmailSecondaireDeJacques@exemple.com}

Résolution 
-------------

On commence par thread : 

https://www.threads.com/@jacques.huppert?xmt=AQF0zpUy2vE7Rt-okbOb9CP3iMlw1nm5pTTAyw_u-9IfUlU 

Dans l'onglet réponse on trouve rapidement un lien vers : https://www.senscritique.com/H_Jacques 

Sur ce profil, on peut cliquer sur un site web (la petite planète a côté de l'âge) qui nous amène ici : https://sites.google.com/view/moviesfromjacques/home

Ce qui nous emmène vers un backup ici : https://drive.google.com/drive/folders/1AzVjea2BfPuC4QZLiqL1ogHjPTvuO5_C 

On a donc plusieurs pseudo : 

- jacques.huppert
- H_Jacques
- jacques.pot.pro
- moviesfromjacques

On y apprend : 

- Qu'il a un compte Gitlab
- Qu'il a un compte Discord

On trouve également un zip avec une facture protégé par un mot de passe.

On trouve également cette adresse email : jacques.pot.pro@gmail.com 

Finalement en continuant a chercher on trouve un compte pastebin avec H_jacques 

Et notamment ceci : https://pastebin.com/ntG83D1V 

"Si tu oublies le mdp du zip, c'est la place près de la maison à Cannes en Pascal Case."

En Psacal Case : ça veut dire tout les mots sans espaces avec une majuscule 

On retourne sur Thread et on trouve "Franchement, je pige toujours pas comment le parking Lamy à Cannes est constamment plein, même en pleine soirée."

Il ne doit pas habiter très loin, allons voir sur Gmaps : PlaceDuCommandantLamy
Place du commandant Lamy

On va faire une wordlist : 

PlaceDuCommandantLamy
PlaceCommandantLamy
CommandantLamy
DuCommandantLamy
PlaceDuCommandantFrançoisLamy
PlaceCommandantFrançoisLamy
CommandantFrançoisLamy
DuCommandantFrançoisLamy
PlaceDuCommandantFrancoisLamy
PlaceCommandantFrancoisLamy
CommandantFrancoisLamy
DuCommandantFrancoisLamy
PlaceDuCommandantFrançois-Lamy
PlaceCommandantFrançois-Lamy
CommandantFrançois-
DuCommandantFrançois-Lamy
PlaceDuCommandantFrancois-Lamy
PlaceCommandantFran-coisLamy
CommandantFrancois-Lamy
DuCommandantFrancois-Lamy
PlaceFrancoisLamy
PlaceFrançoisLamy
FrancoisLamy
FrançoisLamy
PlaceGambetta
Gambetta
PlaceDu18Juin
18Juin
Du18Juin
PlaceDuDixHuitJuin
DixHuitJuin
DuDixHuitJuin
PlaceDuDix-Huit-Juin
Dix-Huit-Juin
DuDix-Huit-Juin
PlaceBernard-Cornut-Gentille
Bernard-Cornut-Gentille
PlaceBernardCornutGentille
BernardCornutGentille
PlaceBernardCornut-Gentille
BernardCornut-Gentille
PlaceDuGénéral-De-Gaulle
Général-De-Gaulle
DuGénéral-De-Gaulle
PlaceDuGeneral-De-Gaulle
General-De-Gaulle
DuGeneral-De-Gaulle
PlaceDuGeneralDeGaulle
GeneralDeGaulle
DuGeneralDeGaulle
PlaceDeLaGare
DeLaGare
Gare
PlaceDeLaCastre
DeLaCastre
LaCastre
Castre
PlaceSommitaleDuSuquet
SommitaleDuSuquet
PlaceDuCommandantMaria
PlaceDuCommandant-Maria
CommandantMaria
DuCommandantMaria
Commandant-Maria
DuCommandant-Maria
PlaceFranklin-Roosevelt
PlaceFranklinRoosevelt
FranklinRoosevelt
PlaceMassuque
Massuque
PlaceDuSuquet
DuSuquet
Suquet
PlaceStanislas
Stanislas
PlaceVauban 
Vauban
SquareMérimée
LeSquareMérimée
SquareMerimee
LeSquareMerimee
SquareJosephBartelemy
JosephBartelemy
SquareDeMores
Mores
PlaceDeLaPantiero
DeLaPantiero
Pantiero
SquareDu8Mai1945
Du8Mai1945
PlaceDuChanoinePaulGrau
DuChanoinePaulGrau
ChanoinePaulGrau
SquareMéro
SquareMero
SquareLeoCallandry
SquareLéoCallandry
LeoCallandry
LéoCallandry
PlaceRocheville
PlaceDeRocheville
Rocheville
PlaceBellevue
Bellevue
DèsirèPignatta
DesirePignatta
DesPins
DeVilaDoConde
DuMarché

On fait un fcrackzip avec cette wordlist et on trouve le mdp : BernardCornutGentille

Dans le fichier PDF on trouve bien le second mail de Jacques : **SHLK{JacquesAimeLeCinema@proton.me}**