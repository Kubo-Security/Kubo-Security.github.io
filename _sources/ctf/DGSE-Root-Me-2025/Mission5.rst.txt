Mission 5
=============

Enoncé 
---------
Brief de mission
~~~~~~~~~~~~~~~~~~~~~~~

Lors d'une arrestation au domicile d'un des attaquants précédemment identifiés, l'équipe a saisi une vielle tablette Google utilisée pour leurs communications.
Durant son analyse, une application de chat semble être chiffrée, rendant impossible l'accès et la découverte de son contenu.

Objectifs de la mission
~~~~~~~~~~~~~~~~~~~~~~~

Analyser l'application mobile.
Accéder aux informations chiffrées.

Exploit 
-------------

On va décompiler l'application avec mobSF

On trouve rapidement des informations intéressantes : 

- Un serveur API : private static final String BASE_URL = "http://163.172.67.201:8000/";
- Une route de l'API :
   
.. code-block:: console

    @GET("messages")
    Object getMessages(@Query("id") String str, Continuation<? super Response<ApiResponse>> continuation);


Si on se rend sur la route http://163.172.67.201:8000/messages?id=1 on obtient ceci : 

A priori les messages à déchiffrer : 

.. code-block:: console

    {
    "messages": [
        {
        "content": "M2geCVKOzPlyug9p9DvthxPip0oe9BPiT2sDfFhWy7iC3+JQI4SfO7+SLAlFSUmu8LoGj1hrUWil/uNXvc+5mKBMrRNFQT8ijBK14P0Z8qA=",
        "isEncrypted": true,
        "sender": "Agent-02",
        "timestamp": "2025-04-01 08:00:00"
        },
        {
        "content": "//5PBsYWhHlgqhVgG1omUyevzmlErLZVsTCLO78Rbb9qBMPnsKCS5/RZ4GEdWRBPiZ4BtO5h7j2PuIutfqf7ag==",
        "isEncrypted": true,
        "sender": "Agent-1337",
        "timestamp": "2025-04-01 10:00:00"
        },
        {
        "content": "2uNMSnJZa5JExhYgNA+V3RAiafhuLkj8Jnr4U+lSZOrrpMWjyA13w0Do3IIPcVBgK070rmweRKX/GkCAxat4i3JfWk1UvWNSmEZbHQlFznR7VFW6FKK84iJKhiDOp8Tk",
        "isEncrypted": true,
        "sender": "Agent-01",
        "timestamp": "2025-04-02 15:30:00"
        },
        {
        "content": "Swz/ycaTlv3JM9iKJHaY+f1SRyKvfQ5miG6I0/tUb8bvbOO+wyU5hi+bGsmcJD3141FrmrDcBQhtWpYimospymABi3bzvPPi01rPI8pNBq8=",
        "isEncrypted": true,
        "sender": "Agent-02",
        "timestamp": "2025-04-03 13:20:00"
        },
        {
        "content": "NAe44oieygG7xzLQT3j0vN+0NoPNUu0TAaid9Az3IlpcKwR0lSKaPT8F4y1zpbArWFIGpgzsPZtPAwL50qocTRMG/g5u+/wcc1nxmhBjCbg=",
        "isEncrypted": true,
        "sender": "Agent-04",
        "timestamp": "2025-04-04 08:30:00"
        },
        {
        "content": "dfeKlZP/gIntHySBYine2YUlNiX3LjlMOLu7y9tgprFyJIIcQpfghlQXut6cJUG2wtzGBVQUm7ITdpLNeVaZjamQHhPWEtNIJE/xtFg66Klui1qCKYKSrmZ4wm1CG/ZPy4csqbM28Ur8dts7XoV5FA==",
        "isEncrypted": true,
        "sender": "Agent-04",
        "timestamp": "2025-04-05 16:45:00"
        },
        {
        "content": "HgVONjPe9UpULkg8e9Ps5++m3t4r6RK0pPfMUQJK/Ok5NinC3UJqkltlEukrvehfyas/uOQygGwMFYdRWT6m4gQBq/TdHf9Xpf4kLJl+o9l2shuwBGFpayRLMkRZ0yX1",
        "isEncrypted": true,
        "sender": "Agent-03",
        "timestamp": "2025-04-06 11:15:00"
        },
        {
        "content": "M+bWr4Az5IbaOw4oGRKZ0pWrGjK7hhLeXVpOiaOAFw91v8KGwhl5b6bXoEl3qqz5APuiy9gLQp7x7GGDod4mLOBWRby48g2RjABqGa6mg3g=",
        "isEncrypted": true,
        "sender": "Agent-01",
        "timestamp": "2025-04-06 14:20:00"
        },
        {
        "content": "Z56plvPjUwczUuTZajCJNqvxB0ArZ9vk38PJnGHSlayR6xe9la8wB4sMOdChcOozbXsjwMRY78wuogxsY57R3iiKe1O3nDpwt3y85BXl9sLEF15wZ8wxc5IfpjVUnRJT",
        "isEncrypted": true,
      "sender": "Agent-00",
      "timestamp": "2025-04-07 09:00:00"
            }
        ]
    }

Mais coment faire ?

On trouve également dans le code : 

- Un SALT : private static final String STATIC_SALT = "s3cr3t_s@lt";
- Un IV : private static final String STATIC_IV = "LJo+0sanl6E3cvCHCRwyIg==";

La fonction de création du hashDeviceID : 

.. code-block:: console

    public final String hashDeviceId(String model, String brand) {
            String str = model + ':' + brand;
            MessageDigest messageDigest = MessageDigest.getInstance("SHA-256");
            Charset UTF_8 = StandardCharsets.UTF_8;
            Intrinsics.checkNotNullExpressionValue(UTF_8, "UTF_8");
            byte[] bytes = str.getBytes(UTF_8);
            Intrinsics.checkNotNullExpressionValue(bytes, "this as java.lang.String).getBytes(charset)");
            String encodeToString = Base64.encodeToString(messageDigest.digest(bytes), 2);
            Intrinsics.checkNotNullExpressionValue(encodeToString, "encodeToString(...)");
            return encodeToString;
        }


Et la fonction de déchiffrement des messages : 

.. code-block:: console

    public final String decryptMessage(String encryptedMessage) {
        try {
            String MODEL = Build.MODEL;
            Intrinsics.checkNotNullExpressionValue(MODEL, "MODEL");
            String BRAND = Build.BRAND;
            Intrinsics.checkNotNullExpressionValue(BRAND, "BRAND");
            byte[] deriveKey = deriveKey(hashDeviceId(MODEL, BRAND), STATIC_SALT);
            byte[] decode = Base64.decode(STATIC_IV, 0);
            Cipher cipher = Cipher.getInstance("AES/CBC/PKCS5Padding");
            cipher.init(2, new SecretKeySpec(deriveKey, "AES"), new IvParameterSpec(decode));
            byte[] doFinal = cipher.doFinal(Base64.decode(encryptedMessage, 0));
            Intrinsics.checkNotNull(doFinal);
            Charset UTF_8 = StandardCharsets.UTF_8;
            Intrinsics.checkNotNullExpressionValue(UTF_8, "UTF_8");
            return new String(doFinal, UTF_8);
        } catch (Exception e) {
            Log.e("DECRYPT_ERROR", "Error decrypting message", e);
            return "[Encrypted] This message was encrypted with old device credentials";
        }
    }

On va récupérer la liste des devices google : https://storage.googleapis.com/play_public/supported_devices.html

- Google;guybrush
- Google;skyrim
- Google;zork
- Google;grunt
- Google;kalista
- Google;Acer Chromebook 14 (CB3-431)
- Google;Chromebook 14 (CB3-431)
- Google;Google Chromebook Pixel (2015)
- Google;Chromebox Reference
- Google;fizz
- Google;puff
- Google;Chromecast
- Google;Chromecast HD
- Google;AOSP on IA Emulator
- Google;Android SDK built for x86
- Google;Android SDK built for x86_64
- Google;GT-I9505G
- Google;sdk_gphone_x86
- Google;Android SDK built for x86_64
- Google;Car on x86_64 emulator
- Google;HPE device
- Google;Google TV Streamer
- Google;brya
- Google;brask
- Google;nissa
- Google;rammus
- Google;coral
- Google;ASUS Chromebook C213NA
- Google;Intel Apollo Lake Chromebook
- Google;reef
- Google;Braswell Chrome OS Device
- Google;Intel Braswell Chromebook
- Google;Intel Braswell Chromebook
- Google;hatch
- Google;octopus
- Google;dedede
- Google;keeby
- Google;nami
- Google;rex
- Google;volteer
- Google;jacuzzi
- Google;kukui
- Google;corsola
- Google;staryu
- Google;geralt
- Google;asurada
- Google;cherry
- Google;Lenovo N23 Yoga/Flex 11 Chromebook
- Google;Mediatek MT8173 Chromebook
- Google;Mediatek MTK8173 Chromebook
- Google;Pixel
- Google;Pixel 2
- Google;Pixel 2 XL
- Google;Pixel 3
- Google;Pixel 3 XL
- Google;Pixel 3a
- Google;Pixel 3a XL
- Google;Pixel 4
- Google;Pixel 4 XL
- Google;Pixel 4a
- Google;Pixel 4a (5G)
- Google;Pixel 5
- Google;Pixel 5a
- Google;Pixel 6
- Google;Pixel 6 Pro
- Google;Pixel 6a
- Google;Pixel 7
- Google;Pixel 7 Pro
- Google;Pixel 7a
- Google;Pixel 8
- Google;Pixel 8 Pro
- Google;Pixel 8a
- Google;Pixel 9
- Google;Pixel 9 Pro
- Google;Pixel 9 Pro Fold
- Google;Pixel 9 Pro XL
- Google;Pixel 9a
- Google;Pixel C
- Google;Pixel Fold
- Google;Google Pixel Slate
- Google;Pixel Slate
- Google;nocturne
- Google;Pixel Tablet
- Google;Google Pixel Watch
- Google;Google Pixel Watch
- Google;Google Pixel Watch 2
- Google;Google Pixel Watch 2
- Google;Pixel XL
- Google;Pixel Watch 3
- Google;Pixel Watch 3
- Google;Pixel Watch 3
- Google;Pixel Watch 3
- Google;Google Pixelbook Go
- Google;atlas
- Google;Google Pixelbook
- Google;Project Tango Tablet Development Kit
- Google;Yellowstone
- Google;trogdor
- Google;trogdor64
- Google;strongbad
- Google;RK3288 Chrome OS Device
- Google;Rockchip RK3288 Chromebook
- Google;RK3288 Chrome OS Device
- Google;Rockchip RK3288 Chromebook
- Google;sdk_gphone64_x86_64
- Google;Automotive SDK built for x86_64
- Google;AOSP on x86
- Google;Automotive SDK built for x86


On fait un script python qui permet de détecter le bon couple Model / Brand : Yellowstone/Google

.. code-block:: python 

    import base64
    import hashlib
    from Crypto.Cipher import AES
    from Crypto.Util.Padding import unpad

    # === Constantes ===
    STATIC_SALT = b's3cr3t_s@lt'  # Sel statique
    STATIC_IV = base64.b64decode("LJo+0sanl6E3cvCHCRwyIg==")  # IV statique

    def hash_device_id(model: str, brand: str) -> str:
        """Hachage SHA-256 du modèle et de la marque, puis encodage en Base64."""
        # Concatenation du modèle et de la marque
        device_id = f"{model}:{brand}"
        
        # Hachage SHA-256
        sha256_hash = hashlib.sha256(device_id.encode('utf-8')).digest()
        
        # Encodage du hash en Base64
        return base64.b64encode(sha256_hash).decode('utf-8')

    def derive_key(device_id_b64: str, salt: bytes) -> bytes:
        """Dérive la clé en concaténant le device_id en Base64 et le sel, puis en hachant le tout avec SHA-256."""
        # Concaténation du device_id en Base64 et du sel (en tant que chaînes de caractères)
        str_to_hash = f"{device_id_b64}:{salt.decode('utf-8')}"
        
        # Hachage SHA-256 pour dériver la clé
        return hashlib.sha256(str_to_hash.encode('utf-8')).digest()

    def decrypt_message(encrypted_message: str, model: str, brand: str) -> str:
        """Déchiffre le message en utilisant AES CBC et la clé dérivées."""
        try:
            # Génération de la clé à partir du modèle et de la marque
            device_hash_b64 = hash_device_id(model, brand)
            derived_key = derive_key(device_hash_b64, STATIC_SALT)
            
            # Décodage du message chiffré
            encrypted_bytes = base64.b64decode(encrypted_message)
            
            # Décryptage avec AES CBC
            cipher = AES.new(derived_key, AES.MODE_CBC, STATIC_IV)
            decrypted = cipher.decrypt(encrypted_bytes)
            
            # Suppression du padding
            plaintext = unpad(decrypted, AES.block_size).decode('utf-8')

            return plaintext
        
        except Exception as e:
            # Gestion des erreurs
            print(f"❌ Erreur de déchiffrement pour {model}/{brand} : {e}")
            return "[Encrypted] Ce message a été chiffré avec de anciennes informations d'appareil."

    def process_devices(file_path: str, encrypted_message: str):
        """Processus principal pour parcourir les modèles et marques et déchiffrer les messages."""
        with open(file_path, 'r') as file:
            for line in file:
                brand, model = line.strip().split(';')
                print(f"\n🔍 Décryptage pour {model}/{brand}")
                decrypted_message = decrypt_message(encrypted_message, model, brand)
                print(f"Message déchiffré pour {model}/{brand}: {decrypted_message}")

    # === Point d’entrée ===
    if __name__ == "__main__":
        # Exemple de message chiffré (à remplacer par le message réel)
        encrypted_message = "M2geCVKOzPlyug9p9DvthxPip0oe9BPiT2sDfFhWy7iC3+JQI4SfO7+SLAlFSUmu8LoGj1hrUWil/uNXvc+5mKBMrRNFQT8ijBK14P0Z8qA="

        # Chemin vers le fichier devices.txt
        devices_file = "devices.txt"
        
        # Traitement des appareils
        process_devices(devices_file, encrypted_message)


On peut maintenant déchiffrer le reste des messages avec ce script : 

.. code-block:: python

    import base64
    import hashlib
    from Crypto.Cipher import AES
    from Crypto.Util.Padding import unpad

    # === Constantes ===
    STATIC_SALT = b's3cr3t_s@lt'  # Sel statique
    STATIC_IV = base64.b64decode("LJo+0sanl6E3cvCHCRwyIg==")  # IV statique

    def hash_device_id(model: str, brand: str) -> str:
        """Hachage SHA-256 du modèle et de la marque, puis encodage en Base64."""
        # Concatenation du modèle et de la marque
        device_id = f"{model}:{brand}"
        
        # Hachage SHA-256
        sha256_hash = hashlib.sha256(device_id.encode('utf-8')).digest()
        
        # Encodage du hash en Base64
        return base64.b64encode(sha256_hash).decode('utf-8')

    def derive_key(device_id_b64: str, salt: bytes) -> bytes:
        """Dérive la clé en concaténant le device_id en Base64 et le sel, puis en hachant le tout avec SHA-256."""
        # Concaténation du device_id en Base64 et du sel (en tant que chaînes de caractères)
        str_to_hash = f"{device_id_b64}:{salt.decode('utf-8')}"
        
        # Hachage SHA-256 pour dériver la clé
        return hashlib.sha256(str_to_hash.encode('utf-8')).digest()

    def decrypt_message(encrypted_message: str, model: str, brand: str) -> str:
        """Déchiffre le message en utilisant AES CBC et la clé dérivées."""
        try:
            # Génération de la clé à partir du modèle et de la marque
            device_hash_b64 = hash_device_id(model, brand)
            derived_key = derive_key(device_hash_b64, STATIC_SALT)
            
            # Décodage du message chiffré
            encrypted_bytes = base64.b64decode(encrypted_message)
            
            # Décryptage avec AES CBC
            cipher = AES.new(derived_key, AES.MODE_CBC, STATIC_IV)
            decrypted = cipher.decrypt(encrypted_bytes)
            
            # Suppression du padding
            plaintext = unpad(decrypted, AES.block_size).decode('utf-8')

            return plaintext
        
        except Exception as e:
            # Gestion des erreurs
            print(f"❌ Erreur de déchiffrement : {e}")
            return "[Encrypted] Ce message a été chiffré avec de anciennes informations d'appareil."

    def decrypt_all_messages(messages, model, brand):
        """Déchiffre tous les messages dans la liste."""
        decrypted_messages = []
        for message in messages:
            if message['isEncrypted']:
                decrypted_text = decrypt_message(message['content'], model, brand)
                decrypted_messages.append({
                    "sender": message['sender'],
                    "timestamp": message['timestamp'],
                    "decrypted_content": decrypted_text
                })
            else:
                decrypted_messages.append(message)  # Message non chiffré
        return decrypted_messages

    # === Point d’entrée ===
    if __name__ == "__main__":
        # Messages chiffrés à déchiffrer (les données JSON fournies)
        encrypted_messages = [
            {"content": "M2geCVKOzPlyug9p9DvthxPip0oe9BPiT2sDfFhWy7iC3+JQI4SfO7+SLAlFSUmu8LoGj1hrUWil/uNXvc+5mKBMrRNFQT8ijBK14P0Z8qA=", "isEncrypted": True, "sender": "Agent-02", "timestamp": "2025-04-01 08:00:00"},
            {"content": "//5PBsYWhHlgqhVgG1omUyevzmlErLZVsTCLO78Rbb9qBMPnsKCS5/RZ4GEdWRBPiZ4BtO5h7j2PuIutfqf7ag==", "isEncrypted": True, "sender": "Agent-1337", "timestamp": "2025-04-01 10:00:00"},
            {"content": "2uNMSnJZa5JExhYgNA+V3RAiafhuLkj8Jnr4U+lSZOrrpMWjyA13w0Do3IIPcVBgK070rmweRKX/GkCAxat4i3JfWk1UvWNSmEZbHQlFznR7VFW6FKK84iJKhiDOp8Tk", "isEncrypted": True, "sender": "Agent-01", "timestamp": "2025-04-02 15:30:00"},
            {"content": "Swz/ycaTlv3JM9iKJHaY+f1SRyKvfQ5miG6I0/tUb8bvbOO+wyU5hi+bGsmcJD3141FrmrDcBQhtWpYimospymABi3bzvPPi01rPI8pNBq8=", "isEncrypted": True, "sender": "Agent-02", "timestamp": "2025-04-03 13:20:00"},
            {"content": "NAe44oieygG7xzLQT3j0vN+0NoPNUu0TAaid9Az3IlpcKwR0lSKaPT8F4y1zpbArWFIGpgzsPZtPAwL50qocTRMG/g5u+/wcc1nxmhBjCbg=", "isEncrypted": True, "sender": "Agent-04", "timestamp": "2025-04-04 08:30:00"},
            {"content": "dfeKlZP/gIntHySBYine2YUlNiX3LjlMOLu7y9tgprFyJIIcQpfghlQXut6cJUG2wtzGBVQUm7ITdpLNeVaZjamQHhPWEtNIJE/xtFg66Klui1qCKYKSrmZ4wm1CG/ZPy4csqbM28Ur8dts7XoV5FA==", "isEncrypted": True, "sender": "Agent-04", "timestamp": "2025-04-05 16:45:00"},
            {"content": "HgVONjPe9UpULkg8e9Ps5++m3t4r6RK0pPfMUQJK/Ok5NinC3UJqkltlEukrvehfyas/uOQygGwMFYdRWT6m4gQBq/TdHf9Xpf4kLJl+o9l2shuwBGFpayRLMkRZ0yX1", "isEncrypted": True, "sender": "Agent-03", "timestamp": "2025-04-06 11:15:00"},
            {"content": "M+bWr4Az5IbaOw4oGRKZ0pWrGjK7hhLeXVpOiaOAFw91v8KGwhl5b6bXoEl3qqz5APuiy9gLQp7x7GGDod4mLOBWRby48g2RjABqGa6mg3g=", "isEncrypted": True, "sender": "Agent-01", "timestamp": "2025-04-06 14:20:00"},
            {"content": "Z56plvPjUwczUuTZajCJNqvxB0ArZ9vk38PJnGHSlayR6xe9la8wB4sMOdChcOozbXsjwMRY78wuogxsY57R3iiKe1O3nDpwt3y85BXl9sLEF15wZ8wxc5IfpjVUnRJT", "isEncrypted": True, "sender": "Agent-00", "timestamp": "2025-04-07 09:00:00"}
        ]
        
        # Déchiffrement de tous les messages
        decrypted_messages = decrypt_all_messages(encrypted_messages, "Yellowstone", "Google")
        
        # Affichage des messages déchiffrés
        for msg in decrypted_messages:
            print(f"Sender: {msg['sender']} | Timestamp: {msg['timestamp']} | Decrypted Content: {msg['decrypted_content']}")


Résultalt : 

.. code-block:: console

    Sender: Agent-02 | Timestamp: 2025-04-01 08:00:00 | Decrypted Content: Target acquired. Hospital network vulnerable. Initiating ransomware deployment.
    Sender: Agent-1337 | Timestamp: 2025-04-01 10:00:00 | Decrypted Content: Keep this safe. RM{788e6f3e63e945c2a0f506da448e0244ac94f7c4}
    Sender: Agent-01 | Timestamp: 2025-04-02 15:30:00 | Decrypted Content: New target identified. School district network. Estimated payout: 500k in crypto.
    Sender: Agent-02 | Timestamp: 2025-04-03 13:20:00 | Decrypted Content: New ransomware strain ready for deployment. Testing phase complete.
    Sender: Agent-04 | Timestamp: 2025-04-04 08:30:00 | Decrypted Content: Security patch released. Need to modify attack vector. Meeting at usual place.
    Sender: Agent-04 | Timestamp: 2025-04-05 16:45:00 | Decrypted Content: New zero-day exploit in a linux binary discovered. Perfect for next operation. Details incoming.
    Sender: Agent-03 | Timestamp: 2025-04-06 11:15:00 | Decrypted Content: [Encrypted] Ce message a été chiffré avec de anciennes informations d'appareil.
    Sender: Agent-01 | Timestamp: 2025-04-06 14:20:00 | Decrypted Content: [Encrypted] Ce message a été chiffré avec de anciennes informations d'appareil.
    Sender: Agent-00 | Timestamp: 2025-04-07 09:00:00 | Decrypted Content: [Encrypted] Ce message a été chiffré avec de anciennes informations d'appareil.
                                                                                                                                                            
