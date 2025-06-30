Network - Slow and low
===========================

Enoncé
----------

- A compléter


Fichier
----------

- Challenge_ctf.pcap

Résolution
-------------

On ouvre le fichier dans Wireshark

On recherche le mot flag dans la liste de paquet

.. figure:: ../../_static/img/midnightflag/slowandlow_1.jpg
    :alt: Osint
    :align: center
    :width: 800
    
Les première recherches ne donnent rien dans les trames HTTP. On les retire donc via le filtre : 


.. figure:: ../../_static/img/midnightflag/slowandlow_2.jpg
    :alt: Osint
    :align: center
    :width: 200

On arrive ensuite sur une trame FTP qui ne semble pas non plus être la bonne piste

On enchaine ensuite sur des trames TCP  puis ISMP qui ne donnent rien non plus. On agrémente le filtre en conséquence : 

.. code-block:: console

    not http and not ftp and not tcp and not icmp
 
Il ne reste alors que quelques trames dont une trame DNS où on retrouve :


.. figure:: ../../_static/img/midnightflag/slowandlow_3.jpg
    :alt: Osint
    :align: center
    :width: 400

On a donc le flag :

**MCTF{flag{OnPeutMettreDuTexteDansDuDNSEtDuCoupMettreUnFlagValide}}**
