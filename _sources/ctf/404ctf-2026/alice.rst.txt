OSINT - C'était caché
==============================

Description 
-----------------
Une très grande dame française de l'informatique est en possession d'un flag, égaré sur une plateforme bien connue. À vous de réussir à le retrouver ! Format : 404CTF{leflag}


Solution 
----------------

On a une image, on peut voir rapidement le logo Github, et surtout dans l'url "alice-reco" 

On cherche donc "alice-reco" dans github et on trouve directement le compte https://github.com/alice-recoque

Il n'y a qu'un seul repo, avec beaucoup de dossier et fichiers inutiles.

Un bon grep : 

.. code-block:: console 

    grep -R "404"         
    res:404CTF{CENSUR
    s/t/k/flag.txt:404CTF{CENSURÉ}

On va donc regarder tous les commits liés à ce fichier : 

.. code-block:: console 

    git log -p -- flag.txt
    commit a7394c2afed06d5a3015e971813c57b34a36226c
    Author: alice_recoque <alice-recoque@proton.me>
    Date:   Thu May 21 21:21:32 2026 +0200

        gros changement

    diff --git a/s/t/k/flag.txt b/s/t/k/flag.txt
    deleted file mode 100644
    index 22581d3..0000000
    --- a/s/t/k/flag.txt
    +++ /dev/null
    @@ -1,2 +0,0 @@
    -Bravo ! 
    -404CTF{PLuT0T_FriTE5_0û_P0T@T03S?}

    commit d12c5e08b84cfd0623fc70b3653af4a61f0525f4
    Author: alice_recoque <alice-recoque@proton.me>
    Date:   Thu May 21 20:42:48 2026 +0200

        gros changement

    diff --git a/s/t/k/flag.txt b/s/t/k/flag.txt
    new file mode 100644
    index 0000000..22581d3
    --- /dev/null
    +++ b/s/t/k/flag.txt
    @@ -0,0 +1,2 @@
    +Bravo ! 
    +404CTF{PLuT0T_FriTE5_0û_P0T@T03S?}

Et on trouve notre flag : **404CTF{PLuT0T_FriTE5_0û_P0T@T03S?}**