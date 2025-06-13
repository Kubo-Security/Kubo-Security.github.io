Forensic - I want my tickets backs 1/5
=======================================

Enoncé
---------

- A compléter

Résolution
-------------

En ouvrant le fichier, on est amené vers un site web qui télécharge automatiquement un ISO

On monte l'iso avec : 

.. code-block:: console

    sudo mount -o loop JO-PARIS2024-Billets.iso test/

Dans l'iso on retrouve 4 fichier : 

- Une image inutile
- Un fichier html
- Je ne sais plus mais inutile aussi
- Une fausse image qui est en faites l'exécutable malveillant

La technique utilisé par le fichier HTML
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- Recherche google "Mitre attack HTML File"
- https://attack.mitre.org/techniques/T1027/006/ 
- On récupère l'ID de la technique

Hash du malware
~~~~~~~~~~~~~~~~~

- On a mis le png dans virustotal pour vérifier que c'était bien le malware
- Virustotal fournit le hash du fichier : https://www.virustotal.com/gui/file/3cba38fdf84cf7ea3334040c8b4539403e73adc185d612085628042a695e8da3/community

