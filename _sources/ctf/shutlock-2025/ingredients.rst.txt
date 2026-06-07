L'ingrédient mystère
==========================

Enonce
------------


Résolution
------------

On commence par regarder le pdf, rien de très intéressant a l'intérieur à priori, si ce n'est que ça parle de Netlify. 

Les données exifs : 

.. code-block:: console

    ExifTool Version Number         : 13.25
    File Name                       : start.pdf
    Directory                       : .
    File Size                       : 1076 kB
    File Modification Date/Time     : 2025:06:27 19:01:53+02:00
    File Access Date/Time           : 2025:06:27 19:01:53+02:00
    File Inode Change Date/Time     : 2025:06:27 19:01:53+02:00
    File Permissions                : -rw-rw-r--
    File Type                       : PDF
    File Type Extension             : pdf
    MIME Type                       : application/pdf
    PDF Version                     : 1.4
    Linearized                      : No
    Page Count                      : 10
    XMP Toolkit                     : Image::ExifTool 13.25
    Location                        : tresser.immortel.flambante
    Format                          : application/pdf
    Title                           : Untitled
    Producer                        : GPL Ghostscript 9.55.0
    Create Date                     : 2024:04:25 08:10:26Z
    Creator Tool                    : Adobe InDesign 18.1 (Macintosh)
    Modify Date                     : 2024:04:25 08:10:26Z
    Document ID                     : uuid:d26acf66-3af7-11fa-0000-ce72ce21b9cd
    Creator                         : Adobe InDesign 18.1 (Macintosh)


On voit clairement une valeur inhabituelle : tresser.immortel.flambante

On remarque tout de suite le what3words : https://what3words.com/tresser.immortel.flambante 

Cela nous amène sur un restaurant parthenopi à cannes évidemment.

On va google "Parthenopi", sur la 2e page de résultat on trouve un site lié à Parthenopi et a Netlify : https://les-delices-de-mykonos.netlify.app/restaurants/parthenopi

C'est sur, on tient notre piste !

Dessus on trouve des recettes, des critiques de restaurants et une section "A propos".

Dedans on trouve : 

- Nom prénom : Langley Duval
- Lien vers compte instagram : https://www.instagram.com/tzatzival/
- Lien vers compte NGL : https://ngl.link/tzatzival

Dans la story une image attire notre attention, la 4eme. Elle indique "un ajustement" dans la recette du pain pita. 

.. figure:: ../../_static/img/shutlock2025/ingredients.jpg
    :alt: ingredient
    :align: center
    :width: 1200

Après de longues recherches, une idée me vient, est ce qu'on trouve la recette du pain pita sur son site ? 

D'après le site, et le code des pages html : non 

Mais essayons quand même : https://les-delices-de-mykonos.netlify.app/recettes/pita 

Super, la page existe et elle contient un ingrédient inhabituel : azodicarbonamide 

On va vérifier qu'il est bien interdit Google "azobicarbonamide interdit France" et on trouve : "L'azodicarboxamide est utilisé comme un agent levant. Il est ajouté dans la farine comme améliorateur de pâte. Ces utilisations d'azodicarbonamide comme additif alimentaire (E 927) ne sont plus autorisés dans l'Union européenne."

On a donc notre flag : **SHLK{pita_azodicarbonamide}**