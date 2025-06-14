Bébé nageur
=======================

Merci Cédric pour la méthode ! 
Et chat GPT pour le code !


Le code du challenge, avec mes annotations en dessous : 

.. code-block:: python

    from flag import FLAG
    import random as rd

    charset = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789{}_-!"

    def f(a,b,n,x):
        return (a*x+b)%n

    def encrypt(message,a,b,n):
        encrypted = ""
        for char in message:
            x = charset.index(char)
            x = f(a,b,n,x)
            encrypted += charset[x]

        return encrypted

    n = len(charset) # 67
    a = rd.randint(2,n-1) # entre 2 et 66
    b = rd.randint(1,n-1) # Entre 1 et 66

    print(encrypt(FLAG,a,b,n))

    # ENCRYPTED FLAG : -4-c57T5fUq9UdO0lOqiMqS4Hy0lqM4ekq-0vqwiNoqzUq5O9tyYoUq2_*
    # DECRYPTED FLAG : 404CTF{                                                  }

    # 4 = -
    # 0 = 4 
    # C = c
    # On a 4 envoyé a decrypt
    # x = 56
    # 65 = (a* 56+b) Modulo 67

    # Avec 0 envoyé a decrypt
    # x = 52
    # 56 = (a* 52+b) modulo 67
    # 56 = (19* 52+6) modulo 67

    # On envoi C donc 28
    # 2 = ( a*28+b ) modulo 66 

Le script pour découvrir les valeurs de A et de B en se basant sur les équations (on teste toutes les possibilités) : 

.. code-block:: python 

    # Définition des équations congruentes à résoudre
    n = 67  # La taille du module

    # Nous parcourons toutes les valeurs de a de 1 à 66
    for a in range(1, n):
        # Nous parcourons toutes les valeurs de b de 1 à 66
        for b in range(1, n):
            # Testons si les valeurs de a et b satisfont les deux équations congruentes
            equation1 = (a * 56 + b) % n == 65  # Première équation congruente
            equation2 = (a * 52 + b) % n == 56  # Deuxième équation congruente
            
            # Si a et b satisfont les deux équations, afficher les valeurs de a et b
        if equation1 and equation2:
            print(f"Valeurs de a et b trouvées : a = {a}, b = {b}")
            break  # Sortir de la boucle si une solution est trouvée
        else:
        # Si les valeurs de a et b ne satisfont pas les équations, passer à l'itération suivante
            continue
        # Si une solution est trouvée, sortir de la boucle principale
        break


Et ensuite avec les valeurs de A et de B on décode le tout : 

.. code-block:: python

    # Définition de charset (alphabet utilisé pour le chiffrement)
    charset = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789{}_-!"

    # Valeurs trouvées pour a et b
    a = 19
    b = 6
    n=67

    # Calcul de l'inverse de a modulo 67
    def mod_inverse(a, n):
        """Calcule l'inverse de a modulo n."""
        t, new_t = 0, 1
        r, new_r = n, a
        while new_r != 0:
            quotient = r // new_r
            t, new_t = new_t, t - quotient * new_t
            r, new_r = new_r, r - quotient * new_r
        if r > 1:
            raise ValueError(f"{a} n'a pas d'inverse modulo {n}")
        if t < 0:
            t += n
        return t

    # Inverse de a modulo 67
    inverse_a = mod_inverse(a, 67)

    # Fonction de décryptage
    def decrypt(encrypted_message, a, b, n):
        decrypted = ""
        for char in encrypted_message:
            try:
                # Trouver l'index du caractère dans charset
                x = charset.index(char)
            except ValueError:
                print(f"Caractère {char} non trouvé dans charset")
                return None
            
            # Calculer la valeur inverse de la fonction de chiffrement
            # Utiliser l'inverse de a et b pour inverser la fonction
            x = (inverse_a * (x - b)) % n
            
            # Ajouter le caractère décrypté à la chaîne
            decrypted += charset[x]
            print(decrypted)
        return decrypted

    # Message chiffré à décrypter
    encrypted_flag = "-4-c57T5fUq9UdO0lOqiMqS4Hy0lqM4ekq-0vqwiNoqzUq5O9tyYoUq2_" #On a retiré une étoile

    # Décrypter le message chiffré
    decrypted_flag = decrypt(encrypted_flag, a, b, n)

    # Afficher le FLAG décrypté
    print("Le flag décrypté est :", decrypted_flag)



Le flag : **404CTF{Th3_r3vEnGE_1S_c0minG_S0oN_4nD_w1Ll_b3_TErRiBl3_!}**