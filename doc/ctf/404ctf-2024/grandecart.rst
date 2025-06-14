Stegano - Le grand ecart
==============================

On a le livre de mobydick en version .txt mais avec des caractères super étrange ! 

On va donc récupérer le document original mobydick.txt : https://gist.githubusercontent.com/StevenClontz/4445774/raw/1722a289b665d940495645a5eaaad4da8e3ad4c7/mobydick.txt 

Ensuite, on compare les fichiers, et on remarque de très nombreuses différences

On va donc récupérer dans deux fichiers différents tous les caractères deux a deux différents : 

.. code-block:: python 


    # A faire deux fois pour obtenir les deux différences
    def read_hex_file(filename):
        with open(filename, 'r') as file:
            hex_data = file.read().strip().replace('\n', '')  # Lire le contenu du fichier et supprimer les sauts de ligne
        return hex_data

    def find_differences(hex_data1, hex_data2):
        differences = []
        # Assurer que les deux chaînes hexadécimales ont la même longueur
        min_length = min(len(hex_data1), len(hex_data2))
        # Comparer les paires de caractères hexadécimaux deux par deux
        for i in range(0, min_length, 2):
            pair1 = hex_data1[i:i+2]
            pair2 = hex_data2[i:i+2]
            if pair1 != pair2:
                differences.extend(pair2)
        # Ajouter les paires de caractères supplémentaires du deuxième fichier si la longueur est différente
        if len(hex_data2) > min_length:
            differences.extend(hex_data2[min_length:])
        return differences

    def main():
        # Chemins des fichiers
        file2_path = 'challenge_hex.txt'
        file1_path = 'mobydick_hex.txt'

        # Lire les données hexadécimales des fichiers
        hex_data1 = read_hex_file(file1_path)
        hex_data2 = read_hex_file(file2_path)

        # Trouver les différences entre les fichiers
        different_hex_values = find_differences(hex_data1, hex_data2)

        # Écrire les valeurs hexadécimales différentes du deuxième fichier dans un fichier resultat.txt
        with open('resultat_1.txt', 'w') as result_file:
            for hex_value in different_hex_values:
                result_file.write(hex_value)

        print("Les valeurs hexadécimales différentes du deuxième fichier ont été enregistrées dans resultat.txt")

    if __name__ == "__main__":
        main()


Ensuite, vu le nom du challenge, on les compare deux a deux et on fait plus grand moins plus petit.

Malheureusement ça ne donne rien

En faite il faut faire un xor entre les deux ! 

.. code-block:: python


    ## Permet de faire le XOR entre le premier et le deuxieme fichier
    def read_hex_file(file_path):
        """Lire un fichier hexadécimal et retourner une liste d'octets."""
        with open(file_path, 'r') as f:
            hex_data = f.read().strip().replace('\n', '')
            # Assurez-vous que la longueur est un multiple de 2 (un octet)
            if len(hex_data) % 2 != 0:
                raise ValueError("Le fichier hexadécimal n'est pas valide.")
            return [hex_data[i:i+2] for i in range(0, len(hex_data), 2)]

    def write_hex_file(file_path, hex_data):
        """Écrire des données hexadécimales dans un fichier."""
        with open(file_path, 'w') as f:
            f.write(''.join(hex_data))

    def xor_hex_files(file1_path, file2_path):
        """Effectue un XOR entre deux fichiers contenant des données hexadécimales."""
        hex_data1 = read_hex_file(file1_path)
        hex_data2 = read_hex_file(file2_path)
        
        if len(hex_data1) != len(hex_data2):
            raise ValueError("Les fichiers n'ont pas la même taille.")
        
        xored_data = [format(int(hex1, 16) ^ int(hex2, 16), '02x') for hex1, hex2 in zip(hex_data1, hex_data2)]
        
        return xored_data

    def main():
        # Chemin vers les fichiers contenant les données hexadécimales
        file1_path = 'resultat_1.txt'
        file2_path = 'resultat_2.txt'
        
        # XOR les fichiers
        xored_data = xor_hex_files(file1_path, file2_path)
        
        # Écrire les résultats dans un nouveau fichier
        output_file_path = 'resultat_xor.hex'
        write_hex_file(output_file_path, xored_data)

        print(f"Le résultat du XOR a été écrit dans '{output_file_path}'.")

    if __name__ == "__main__":
        main()


Mais le fichier PNG est illisible

C'est parce qu'on a oublié toutes les lignes identiques, qui permettent de former les 00 qui vont servir à construire le fichier ! 

Donc on recommence, on reprend les fichiers txt.

On les converti en hexa pour faciliter le traitement des différences (xxd -p fichier > fichier_hex.txt)

On récupère le premier code hexa (donc les deux premiers caractères) de chaque ligne

.. code-block:: python

    def extract_first_two_chars(input_file, output_file):
        # Liste pour stocker les premiers deux caractères de chaque ligne
        extracted_chars = []

        # Ouverture et lecture du fichier d'entrée
        with open(input_file, 'r') as f_in:
            lines = f_in.readlines()
            for line in lines:
                # Extrait les deux premiers caractères de chaque ligne
                extracted_chars.append(line[:2])

        # Écriture des caractères extraits dans le fichier de sortie
        with open(output_file, 'w') as f_out:
            for char in extracted_chars:
                f_out.write(char)

        print(f"Les deux premiers caractères de chaque ligne du fichier {input_file} ont été extraits avec succès et écrits dans {output_file}.")

    if __name__ == "__main__":
        input_file1 = input("Entrez le chemin du premier fichier d'entrée : ")
        input_file2 = input("Entrez le chemin du deuxième fichier d'entrée : ")
        output_file1 = input("Entrez le chemin du fichier de sortie pour le premier fichier : ")
        output_file2 = input("Entrez le chemin du fichier de sortie pour le deuxième fichier : ")

        extract_first_two_chars(input_file1, output_file1)
        extract_first_two_chars(input_file2, output_file2)


Maintenant on peut faire le XOR, on utilisera cyberchef pour le coup : 

- On remet le fichier resultat_1.txt en binaire ==> xxd -p -r resultat_1.txt > file1
- On l'envoi en input dans cyberchef
- On met l'autre fichier en hexa en tant que clé
- On récupère le résultat

Cela ne fonctionne toujours pas, car il fallait retirer les 10 premières lignes, donc on retire les 20 premiers caractères de chaque fichiers et on recommence

On obtient bien la bonne image mais pas de flag, il faut continuer a investiguer ! 

On fait un petit zsteg -a result.png et on voit le flag apparaitre : **404CTF{you_spot_w3ll_the_differences}**