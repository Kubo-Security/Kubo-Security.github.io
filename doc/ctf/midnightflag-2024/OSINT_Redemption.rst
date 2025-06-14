OSINT - OSINT_Redemption
===========================

Enoncé
----------

- A compléter


Résolution
-------------

- https://www.youtube.com/watch?v=24fBVqx0kek

On voit que les 3 personnes qui pourraient correspondre aux voyageurs du temps arrivent en Dacia.

La piste semble étrange pour un "vaisseau… très spécial"

On part donc sur les coordonnées GPS qui apparaissent dans la video.

Les premières  :


.. figure:: ../../_static/img/midnightflag/osintredemption_1.png
    :alt: Osint
    :align: center
    :width: 400
    
Nous amènes ici sur Maps :

.. figure:: ../../_static/img/midnightflag/osintredemption_2.png
    :alt: Osint
    :align: center
    :width: 400

On suppose que l'avion peut être la piste. On zoome donc dessus

.. figure:: ../../_static/img/midnightflag/osintredemption_3.png
    :alt: Osint
    :align: center
    :width: 400

On voit que l'avion semble être de la compagnie RYAN AIR.

En recherchant les avions de la flotte de la compagnie, on voit qu'ils n'ont que des Boeing 737

En faisant un Google Lens sur l'image de l'avion, on tombe également sur des Boeing 737

On cherche alors Boeing 737 engine code dans Google et on tombe sur CFM56-3B1

Cependant, le flag ne fonctionne pas

On teste les autres coordonnées qui apparaissent dans la video mais elles ne donnent rien

On repart alors sur la piste Dacia non suivie précédemment

Lorsque la voiture se gare, on voit que ça semble être une Dacia Sandero. On peut également voir la plaque d'immatriculation :

.. figure:: ../../_static/img/midnightflag/osintredemption_4.png
    :alt: Osint
    :align: center
    :width: 400

On va donc sur Oscaro.com et on tape la plaque pour avoir le modèle exact

.. figure:: ../../_static/img/midnightflag/osintredemption_5.png
    :alt: Osint
    :align: center
    :width: 400

Ne reste plus qu'à retrouver le code moteur avec Google

En descendant un peu, on trouve le modèle exact avec le code moteur

On a donc le flag :

**MCTF{B4DH419x}**
