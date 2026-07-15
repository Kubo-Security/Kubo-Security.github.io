OSINT - Stand pas standard
==============================

Description 
-----------------
Un ami chercheur vous contacte : "En allant à un colloque il y a six ou sept ans, je suis tombé sur un super stand de marché, mais impossible de me rappeler ce qu'ils y vendaient ! Je t'envoie quelques photos avec des flèches pour te montrer son emplacement approximatif... Si ça peut t'aider, c'était le stand situé le plus au nord-est de tous." Arriverez-vous à retrouver la catégorie d'activité du stand auquel pense votre ami ? Format : 404CTF{apiculture}

Solution
-----------------

En faisant un reverse image et en prenant le batiment de droite avec la flèche rouge dessus on retrouve cette image : https://fr.pinterest.com/pin/705024516709318705/ 

On cherche donc projet Casa Cerá Rennes, et on retrouve l'emplacement. 

Le marché sur cette place s'appelle le Marché Bio du Mail. 

Après avoir fait plusieurs tentatives avec des photos facebook pour retrouver les emplacements, notamment : 

https://www.facebook.com/photo/?fbid=1627096914129158&set=pb.100064824035738.-2207520000&locale=fr_FR : boulangerie

https://www.facebook.com/photo/?fbid=634235768747219&set=pb.100064824035738.-2207520000&locale=fr_FR : primeur

https://www.facebook.com/photo/?fbid=10218013080903573&set=a.1638008235063&locale=fr_FR : crêperie 

L'admin nous à indiqué que la réponse n'était pas sujet à interprétation, qu'on trouve la réponse à coup sur, de façon fiable. 

On va se concentrer sur les données publiques : 

https://data.rennesmetropole.fr/explore/dataset/les-emplacements-sur-les-marches-dedies-au-commerce-non-sedentaire-sur-la-ville-/map/?location=22,48.11024,-1.68662&basemap=0a029a

On va pouvoir déterminer le numéro d'emplacement du stand : 97

Puis sur : 

https://www.data.gouv.fr/datasets/les-emplacements-sur-les-marches-dedies-au-commerce-non-sedentaire-sur-la-ville-de-rennes/informations 

En faisant "Explorer les données" 

Et en filtrant sur l'emplacement 97, on trouve "maraichage" pour le Marché bio du Mail. 

Finalement c'était peut être https://www.facebook.com/photo/?fbid=634235768747219&set=pb.100064824035738.-2207520000&locale=fr_FR, sauf qu'on dit pas primeur, ça c'est le nom du métier .. 

Flag : **404CTF{maraichage}**