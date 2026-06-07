Echauffement
===========================

Enoncé
----------

Un bon échauffement permet non seulement d'éviter des blessures, mais aussi de conditionner son corps et son esprit au combat qui va suivre. Ce crackme devrait constituer un exercice adéquat.

 
Auteur : Izipak (_hdrien)


Résolution
-------------


Bon je ne connaisque GDB, alors go ! 

On désassemble la fonction main et la fonction secret_func_dont_look_here

On met des points d'arrêts aux comparaison qui sont faites dans la fonction secret_func_dont_look_here

On voit qu'il y a une boucle qui fait 34 tour, et on récupère 34 valeurs dont on sait pas quoi faire pour l'instant.

Ensuite on a demandé un peu d'aide parce que c'est un chall d'intro : il faut récupérer le pseudo code

Donc ensuite, on lance ghidra, pour voir le pseudo code, et donc la on peut voir que la fonction compare les caractères saisies, avec les caractères du mot de passe, mais dont la valeur est multiplié par 2, et auquel on soustrait la valeur de la boucle ! 

On récupère donc toutes les valeurs, et on fait l'opération inverse avec python : 

.. code-block:: python

    def inverse_operation(order, hex_value):
    initial_value = (int(hex_value, 16) + order) // 2
    return chr(initial_value)

    characters = {
        0: "68",
        1: "5F",
        2: "66",
        3: "83",
        4: "a4",
        5: "87",
        6: "f0",
        7: "d1",
        8: "b6",
        9: "c1",
        10: "bc",
        11: "c5",
        12: "5c",
        13: "dd",
        14: "be",
        15: "bd",
        16: "56",
        17: "c9",
        18: "54",
        19: "c9",
        20: "d4",
        21: "a9",
        22: "50",
        23: "cf",
        24: "d0",
        25: "a5",
        26: "ce",
        27: "4b",
        28: "c8",
        29: "bd",
        30: "44",
        31: "bd",
        32: "aa",
        33: "d9"
    }

    decoded_string = ""

    for order, hex_value in characters.items():
        initial_character = inverse_operation(order, hex_value)
        decoded_string += initial_character
        print(f"Caractère {order}: Valeur initiale = {initial_character}")

    print("Chaîne complète de caractères décodée :", decoded_string)


Et on obtient le flag : **404CTF{l_ech4uff3m3nt_3st_t3rm1ne}**