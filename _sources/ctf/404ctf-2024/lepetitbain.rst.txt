Le petit bain
================================

C'est clairement la suite du bébé nageur

Donc on va reprendre le même fonctionnement, le code du challenge avec mes annotations pour chaque fonction et les calculs de A et B : 

.. code-block:: python

    import random as rd
	from flag import FLAG

	assert FLAG[:12] == "404CTF{tHe_c"

	charset = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789{}_-!"
	n = len(charset)

	def f(a,b,n,x):
		return (a*x+b)%n


	# OUTPUT : C_ef8K8rT83JC8I0fOPiN6P!liE03W2NXFh1viJCROAqXb6o
	# INPOUT : 404CTF{tHe_c                                   }

	# 4 = 56 
	# 28 = (a*56+b)%67
	# { = 62
	# 60 = (a*62+b)%67
	# A=50
	# B=42

	# 0 = 52
	# 64 = (a*52+b)%67
	# t = 19
	# 17 = (a*19+b)%67
	# A=40
	# B=61

	# 4 = 56
	# 4 = (a*56+b)%67
	# H = 33
	# 45 = (a*33+b)%67
	# A=39
	# B=31

	# C = 28
	# 5 = (a*28+b)%67
	# e = 4
	# 60 = (a*4+b)%67
	# A = 34
	# B = 58

	# T = 45
	# 60 = (a*45+b)%67
	# _ = 64
	# 55 = (a*64+b)%67
	# A=35
	# B=26

	# F = 31
	# 36 = (a*31+b)%67
	# c = 2
	# 35 = (a*2+b)%67
	# A=37
	# B=28

	# A = 50,40,39,34,35,37
	# B= 42,61,31,58,26,28


	# fonction Round
	# Pour i de 0 a 48
	# x c'est l'index des caractères 1 a 1 
	# a c'est le premier de la liste jusqu'au 6eme
	# Pareil pour B
	# on envoi tout a la meme fonction que le challenge d'avant
	def round(message,A,B,n):
		encrypted = ""
		for i in range(len(message)):
			x = charset.index(message[i])
			a = A[i%6]
			b = B[i%6]
			x = f(a,b,n,x)
			encrypted += charset[x]
		return permute(encrypted)

	# Fonction encryt
	# Pour K entre 0 et 6
	# A = Liste aléatoire de 6 nombre entre 2 et 67
	# B = Liste aléatoire de 6 nombre entre 1 et 67
	# Envoi le flag (encrytped), A, B et n=67
	def encrypt(message):
		encrypted = message
		for k in range(6):
			A = [ rd.randint(2,n-1) for i in range(6)]
			B = [ rd.randint(1,n-1) for i in range(6)]
			encrypted = round(encrypted,A,B,n)
		return encrypted


	def permute(message):
		p = [4, 3, 0, 5, 1, 2, 10, 9, 6, 11, 7, 8, 16, 15, 12, 17, 13, 14, 22, 21, 18, 23, 19, 20, 28, 27, 24, 29, 25, 26, 34, 33, 30, 35, 31, 32, 40, 39, 36, 41, 37, 38, 46, 45, 42, 47, 43, 44]
		permuted = [ message[p[i]] for i in range(len(message))]
		return ''.join(permuted)

	print(encrypt(FLAG))


On a récupérer tous les index avec : 

.. code-block:: python

    charset = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789{}_-!"

    message2 = "C_ef8K8rT83J"
    message = "404CTF{tHe_c"

    # Calculer et afficher les index de chaque caractère du message dans le charset
    for char in message:
        index = charset.index(char)
        print(f"Le caractère '{char}' a l'index {index} dans charset.")

    for char in message2:
        index = charset.index(char)
        print(f"Le caractère '{char}' a l'index {index} dans charset.")



On résoud les équations en modifiant les deux équations a chaque fois (on aurait aussi pu mettre les 6 dans le meme script) : 

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

Et on décode le tout une fois qu'on à la liste des A et des B, on sait pas pourquoi, la fonction permute sert a rien, pas besoin de la reverse : 

.. code-block:: python

    # Définissez votre charset et n.
    charset = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789{}_-!"
    n = len(charset)  # n = 67

    # Fonction pour calculer l'inverse modulaire d'un nombre
    def mod_inverse(a, n):
        t, new_t = 0, 1
        r, new_r = n, a
        while new_r != 0:
            quotient = r // new_r
            t, new_t = new_t, t - quotient * new_t
            r, new_r = new_r, r - quotient * new_r
        if r != 1:
            raise ValueError(f"Aucun inverse pour {a} modulo {n}.")
        if t < 0:
            t += n
        return t

    # Fonction de déchiffrement d'un caractère
    def decrypt_char(encrypted_char, a, b):
        # Trouver l'index du caractère chiffré dans le charset
        encrypted_index = charset.index(encrypted_char)
        
        # Calculer l'inverse de a modulo n
        inv_a = mod_inverse(a, n)
        
        # Déchiffrer l'index du caractère original
        original_index = (inv_a * (encrypted_index - b)) % n
        
        # Retourner le caractère original
        return charset[original_index]

    # Fonction pour déchiffrer un message entier
    def decrypt(encrypted_message, A, B):
        decrypted_message = []
        
        # Déchiffrer chaque caractère du message
        for i in range(len(encrypted_message)):
            # Obtenir les coefficients a et b appropriés pour la position actuelle
            a = A[i % 6]
            b = B[i % 6]
            
            # Déchiffrer le caractère
            decrypted_char = decrypt_char(encrypted_message[i], a, b)
            decrypted_message.append(decrypted_char)
        
        # Convertir la liste de caractères en chaîne de caractères
        return ''.join(decrypted_message)

    # Les listes de coefficients
    A = [50, 40, 39, 34, 35, 37]
    B = [42, 61, 31, 58, 26, 28]

    # Le message chiffré
    encrypted_message = "C_ef8K8rT83JC8I0fOPiN6P!liE03W2NXFh1viJCROAqXb6o"

    # Déchiffrer le message
    decrypted_message = decrypt(encrypted_message, A, B)

    # Afficher le message déchiffré
    print("Le message déchiffré est :", decrypted_message)


Le flag : **404CTF{tHe_c4fF31ne_MakE5_m3_StR0nG3r_th4n_y0u!}**