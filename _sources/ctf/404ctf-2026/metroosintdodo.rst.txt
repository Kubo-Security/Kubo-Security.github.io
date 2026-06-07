OSINT - Metro Osint Dodo
=============================

Description 
-----------------
Il y a quelques mois, en fin de matinée, j’ai mis plus d’une demie heure à faire un trajet qui normalement prend 11 à 12 minutes. Tout ça à cause d’un malaise voyageur à Châtelet. Mon trajet était entre deux stations comportant des noms de scientifiques.

Quelles étaient ces deux stations ?

Format du flag : 404CTF{les-halles_les-halles}

Solution 
----------------

On récupère les plans de métros : https://planmetro.paris/ligne-7/

La ligne 7 semble être un bon candidat avec plusieurs noms de scientifiques (cadet, jussieu, pierre et marie curie) mais rien ne correspond.

Les autres lignes qui passent par Chatelet sont : 1, 4, 11 et 14 

Sur la ligne 4 on trouve les arrêts : 
- Reaumur Sebastopol (René Antoine Ferchault de Réaumur)
- Montparnasse Bienvenüe (Fulgence Bienvenüe)

La distance entre ces deux arrêts est de 11 minutes (d'après google, parce qu'en vrai c'est plutôt 14-15 haha)

Et cela correspond parfaitement a notre format flag. 

Flag : **404CTF{reaumur-sebastopol_montparnasse-bienenue}**