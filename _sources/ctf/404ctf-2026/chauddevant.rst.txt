OSINT - Chaud Devant
=======================

Description 
-----------------
Derrière une mort spectaculaire se cache parfois une grande scientifique... Une fois que vous l'aurez retrouvée, un petit voyage en ligne vous permettra d'en savoir plus sur elle. Dans l'ordre, il vous faudra retrouver :

L'année de décès
le mont où elle est décédée,
son nom de famille,
son jour de naissance
et le mois de naissance de son mari.

Format du flag : 404CTF{2026_Everest_Curie_01_01}

Solution 
----------------

Reverse de l'image, on tombe directement sur "find a grave"

On récupère la liste des personnes enterrés la bas : https://fr.findagrave.com/cemetery/2801783/memorial-search?cemeteryName=Cimeti%C3%A8re&page=5#sr-280581728 

On trouve Katia Krafft https://fr.findagrave.com/memorial/269972054/catherine-jos%C3%A9phine-krafft 

On va sur Wikipedia : https://fr.wikipedia.org/wiki/Katia_et_Maurice_Krafft 

Volcanologue décédé lors d'une éruption, donc correspond a la description du challenge

Année : **1991**
Mont : **Unzen** 
Nom de famille : **Krafft**
Jour de naissance : **17**
Mois de naissance : **03**

Flag : **404CTF{1991_Unzen_Krafft_17_03}**