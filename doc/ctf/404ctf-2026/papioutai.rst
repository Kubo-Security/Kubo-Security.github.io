OSINT - Papioutai
==============================

Description 
-----------------
Ahlala, l'ENS, cette école qui a produit tant d'esprits savants ! Une amie aimerait que vous l'aididez à retrouver des informations sur son bisaïeul, Jean Roux, ancien Ulmien mort pour la France en 14-18 dans un lieu dont le nom finit par demanges. Saurez-vous retrouver l'année de promotion de Jean Roux, ainsi que sa ville de naissance ? Format : 404CTF{1876_saint-germain-en-hyères}


Solution
-----------------

Ulmien signifie qui a fait l'école normale supérieur à Paris, on va donc chercher la liste de tous les diplomés de l'ENS de Paris 

https://www.archicubes.ens.fr/annuaire/annuaire_chercher?identite=roux 

Jean Roux 1912, on a donc l'année.

On cherche les villes en "demanges", je n'ai trouvé que courdemanges 

On cherche les gens décédés dans cette ville pendant la guerre : 

https://geneafrance.com/?v=Courdemanges&i=51184

On trouve notre ville : saint-estèphe 



Notre flag : **404CTF{1912_saint-estèphe}**