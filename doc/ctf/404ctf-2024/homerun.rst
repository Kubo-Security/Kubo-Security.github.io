Forensic / Osint / Divers - Coup de circuit
=================================================

Partie 1 - Forensic
-----------------------

Enoncé
~~~~~~~~~~~~

Ce challenge est le premier d'une série de trois challenges faciles. Le challenge suivant sera disponible dans la catégorie Renseignement en sources ouvertes une fois que vous aurez validé celui-ci.

C'est la catastrophe ! Je me prépare pour mon prochain match de baseball, mais on m'a volé mon mojo ! Sans lui, je vais perdre, c'est certain... Je crois qu'on m'a eu en me faisant télécharger un virus ou je ne sais quoi, et le fichier a été supprimé de mon ordinateur. J'ai demandé de l'aide à un ami expert et il a extrait des choses du PC, mais il n'a pas le temps d'aller plus loin. Vous pourriez m'aider ?

Identifiez le malware et donnez son condensat sha1. Le flag est au format suivant : 404CTF{sha1}

Auteur : Smyler 

Résolution
~~~~~~~~~~~~~

On ouvre le zip Collection.zip

On y trouve un ensemble de fichier CSV.

On tombe rapidement sur cette ligne dans le seule fichier amcache qui contient des données : 

.. code-block::console : 

    Unassociated	0006799086f2b3631ed09571eea308213bed0000ffff	04/05/2024 23:06	5cf530e19c9df091f89cede690e5295c285ece3c	False	c:\users\rick\downloads\sflgdqsfhbl.exe	sflgdqsfhbl.exe	.exe	04/05/2024 17:11		7319454				pe64_amd64	False			219273384	0	


C'est bien le fichier étrange qui a été lancé par l'utilisateur ! 

404CTF{5cf530e19c9df091f89cede690e5295c285ece3c}


OSINT - Partie 2 
------------------------

Enoncé
~~~~~~~~~~~~

Le challenge suivant sera disponible dans la catégorie Divers une fois que vous aurez validé celui-ci.
 
Super ! Grace à vous j'ai pu retirer le fichier de mon PC, mais pensez-vous qu'il serait possible d'en savoir un peu plus sur ce malware ?

Retrouvez l'interface web du panneau de Command & Control du malware.

Le flag y sera reconnaissable.

Auteur : Smyler 

Résolution
~~~~~~~~~~~~~

On va donc utiliser le SHA1 du malware, et se rendre sur virustotal ! 

On recherche le SHA1 et on retrouve rapidement notre malware : https://www.virustotal.com/gui/file/439bfbdc4ef8d94d36273714d7ef4a709e7228f7daf85aaa1cd295354ee5cb98

Dans ses relations il y a un nom de domaine qui semble suspect, qui fait bien CTF : takemeoutotheballgame.space

https://www.virustotal.com/gui/domain/takemeouttotheballgame.space

Et dans ses relations on retrouve : panel-5d4213f3bf078fb1656a3db8348282f482601690.takemeouttotheballgame.space 

https://www.virustotal.com/gui/domain/panel-5d4213f3bf078fb1656a3db8348282f482601690.takemeouttotheballgame.space 

La on est sur de tenir notre C2 ! 

On se rend dessus et flag : **404CTF{5up3r_b13n_c4Ch3_gr@cE_Au_n0M_@_r4lOngE}**


Divers - Partie 3 
------------------------

Enoncé
~~~~~~~~~~~~

Pour ce challenge, le service web à l'adresse suivante fait partie du périmètre : https://panel-5d4213f3bf078fb1656a3db8348282f482601690.takemeouttotheballgame.space. L'énumération automatique est toujours interdite. Ce challenge est un mix de plusieurs catégories.

Le fichier que l'on m'a volé se nomme Secret-Mojo.pdf. Vous pensez que l'on peut le retrouver à partir du panneau de contrôle ?

Mettez la main sur le fichier dérobé.

Auteur : Smyler 

Résolution
~~~~~~~~~~~~~

Voila ce qu'on à après avoir passé de longues heures a cherché en vain 

Les références au baseball : 

- L'image de batte de baseball
- Batte C2
- C2 : L'ancien nom de la coupe d'europe
- Le nom de domaine : takemeouttotheballgame.space
- Take me out to the ballgame c'est une chanson, un hymne non officiel du baseball
- Dans la chanson il y a notamment une référence à Cracker Jack (John the ripper)
- Coup de circuit : c'est le français de Homerun

Et le "Ze C2 to rule them all", référence au seigneur des anneaux je sais pas ce que ça vient faire la.

Soit il faut chercher le seigneur du baseball, soit c'est pour le mot "rule" est la réponse est dans les règles du baseball ? 

Y'a un jeu éclaté lords of baseball mais pas utile !

Un port 2 ouvert en http : 

- /me qui renvoie notre propre IP 
- /status qui ne renvoi rien 

Le ftp.takemeoutotheballgame.space qu'on a bien nmap aussi mais sans succès 

On sait que c'est pas du web, sinon ça serait dans web et on a déjà tenté mille et une chose. 

Franchement on sèche. 

On a chercher "BatteC2" dans google, rien. 

Finalement on trouve BatteC2 sur github : https://github.com/BribriTireuse 

A partir de la on va s'intéresser au code python notamment : app.py 

https://github.com/BribriTireuse/BatteC2/blob/main/app.py

Le mot de passe est une variable d'environnement, il n'est pas dans le code comme prévu. 

Par contre, on a le path pour télécharger les documents : 

.. code-block:: python

    PASSWORD = environ["PASSWORD"]
    c2 = C2(("0.0.0.0", 1337))
    app = Flask(__name__)
    app.secret_key = PASSWORD
    sock = Sock(app)
    download_dir = Path("227eb601e5bf8ea0c5da13a26be3eba2e5fea2cd7d75dcd2951cf615dd790da5")
    @app.route("/agent/<uuid>/download")
    @require_login
    def download_file(uuid):
        uuid = UUID(uuid)
        agent = c2.agents.get(uuid)
        if agent is None:
            return "Not found", 404
        path = request.args["file"]
        local = Path(path)
        agent.download_file(path, Path("static") / download_dir / local.name)
        return redirect(f"/static/{download_dir}/{local.name}")


On va donc récupérer le fichier Secret-Mojo.pdf : 

- https://panel-5d4213f3bf078fb1656a3db8348282f482601690.takemeouttotheballgame.space/static/227eb601e5bf8ea0c5da13a26be3eba2e5fea2cd7d75dcd2951cf615dd790da5/Secret-Mojo.pdf 

Le flag final : **404CTF{M4i5_p0urt@n7_mOn_s3rvEur_d3_C_&_c_3t4i7_s1_53cUrisE}**

