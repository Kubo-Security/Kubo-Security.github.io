OSINT - Canular Savant 1/3
==============================

Description 
-----------------
Je (1.85m) me baladais à Paris, jusqu'à me retrouver à un rond-point bordé de plusieurs fontaines.

De quel rond-point s'agissait-il ?

Format du flag : 404CTF{carrefour-etoile_du_porte-avion-charles-de-gaulle}

Les flags de cette série ne comportent pas d'accents

Solution 
----------------

On cherche dans google "rond point paris fontaines"

On tombe rapidement sur le rond point des champs Elysées que l'on peut facilement parcourir sur Maps et voir les fontaines qui l'entoure.

Ensuite on se bat un peu avec le format flag et on valide. 

Flag : **404CTF{rond-point_des_champs-elysees-marcel-dassault}**


OSINT - Canular Savant 2/3
=============================

Description 
-----------------
J’allais vers le nord, et alors que j’attendais que le feu passe au vert, je me suis rendu compte que j’écoutais un titre d’un célèbre groupe de musique alternative originaire de Columbus. J’ai été surpris d’entendre le nom d’un groupe de scientifiques français dans les paroles : de qui s’agissait-il ?

Format du flag : 404CTF{marie-curie}

(404CTF{curie} accepté également)

Les flags de cette série ne comportent pas d'accents

Solution 
----------------

Dans google "célèbre groupe de musique alternative originaire de Columbus"

On tombe rapidement sur https://fr.wikipedia.org/wiki/Twenty_One_Pilots "Twenty One Pilots (stylisé twenty øne piløts, parfois abrégé TØP) est un groupe de musique alternative américain, originaire de Columbus dans l'Ohio"

On cherche dans google "Twenty One Pilots french scientist"

On tombe sur cette page wikipedia : https://en.wikipedia.org/wiki/Nicolas_Bourbaki 

On y trouve "In 2018, the American musical duo Twenty One Pilots released a concept album named Trench. The album's conceptual framework was the mythical city of "Dema" ruled by nine "bishops"; one of the bishops was named "Nico", short for Nicolas Bourbaki"

Flag : **404CTF{bourbaki}**



OSINT - Canular Savant 3/3
================================

Description 
-----------------
J'ai des souvenirs très précis de cette promenade. Alors que je me trouvais au centre du passage piéton, j'ai tourné la tête et vu une scène magnifique... mais j'ai oublié de prendre une photo ! Le soleil était comme posé sur le feu de signalisation diamétralement opposé à moi... j'y retournerais bien pour prendre une photo ! Par contre, malgré tout ces souvenirs, la seule chose dont je me souviens est que, quelques jours avant, environ une semaine je crois, c'était l'anniversaire d'un des anciens membres du groupe de scientifiques que j'ai mentionné précédemment. Si seulement je pouvais me souvenir de quel membre il s'agissait, il me semble qu'il n'est aujourd'hui plus de ce monde... Pensez-vous pouvoir m'aider ?

Format du flag : 404CTF{marie-curie}

Les flags de cette série ne comportent pas d'accents

Solution 
----------------

On a seulement 2 tentatives alors il ne faut pas se tromper. 

Depuis le rond point des champs elysées, on peut voir un "magnifique" spectacle lorsque le soleil se couche : https://saf-astronomie.fr/soleil-couchant-paris/ 

Donc on serait autour du 10 mai.

On peut s'aider de https://jveuxdusoleil.fr/#18/48.86896/2.31001 pour voir que le 10 mai, l'angle semble être le bon.

On a la liste des membres, notamment décédé ici : https://en.wikipedia.org/wiki/Nicolas_Bourbaki 

Il y en a 2 qui sont nés début mai : 

Claude Chabauty : 4 mai 1910
André Weil : 6 mai 1906

On va en testé les deux en priant, et on a bien le bon !

Flag : **404CTF{andre-weil}**

