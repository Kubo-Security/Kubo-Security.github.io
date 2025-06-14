Toortik triflexation 1/2
====================================

Voyons le type de machine ; 
vol -s . -f toortik_triflexation.elf banners: 

A COMPLETE

On récupre le profil sur https://github.com/Abyss-W4tcher 

On l'ajoute au dossier avec le challenge, et on peut ensuite continuer avec : 

On regarde les processus :

.. code-block:: console

    vol -s . -f toortik_triflexation.elf linux.pslist
    0x17ae28c0 100.02642    2642    2605kingwireshark       TASK_RUNNING
    0x8fffd7ae28c0  2642    2642    2605    wireshark       0       0       0       0       2025-05-03 12:53:41.118799 UTC  Disabled

Seul Wireshark semble intéressant dans la liste, mais pour la 2eme partie seulement. 

Dans les fichiers on trouve également : 

.. code-block:: console

    vol -s . -f toortik_triflexation.elf linux.lsof
    2642    3602    Thread (pooled) 20      /tmp/wireshark_enp0s32V7Y52.pcapng      8:2     1310745 REG     -rw-------      2025-05-03 13:00:01.000000 UTC  2025-05-03 13:00:01.000000 UTC  2025-05-03 13:00:01.000000 UTC  5958600
    2642    3602    Thread (pooled) 21      /tmp/wireshark_enp0s32V7Y52.pcapng      8:2     1310745 REG     -rw-------      2025-05-03 13:00:01.000000 UTC  2025-05-03 13:00:01.000000 UTC  2025-05-03 13:00:01.000000 UTC  5958600
    0x9000c669e000  /       8:2     1310745 0x8fffcdaf8128  REG     1455    1455    -rw-------      2025-05-03 13:00:01.000000 UTC  2025-05-03 13:00:01.000000 UTC  2025-05-03 13:00:01.000000 UTC  /tmp/wireshark_enp0s32V7Y52.pcapng      5958600

Il y a un plugin "hidden_modules", allons voir ça : 

.. code-block:: console

    vol -s . -f toortik_triflexation.elf linux.hidden_modules
    Offset  Module Name     Code Size       Taints  Load Arguments  File Output
    0xffffc08dc1c0  chall   0x3000  OOT_MODULE,UNSIGNED_MODULE              N/A

Nom du module : chall

On sait qu'on cherche une tâche répété, donc on va utiliser grep pour obtenir les taches cron : 

.. code-block:: console

    strings toortik_triflexation.elf | grep '\* \* \*'
    /10 * * * * /snap/firefox/.config/config-firefox
    /10 * * * * /snap/firefox/.config/config-firefox
    17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
    25 6    * * *   root    test -x /usr/sbin/anacron || { cd / && run-parts --report /etc/cron.daily; }
    5-55/10 * * * * root command -v debian-sa1 > /dev/null && debian-sa1 1 1
    59 23 * * * root command -v debian-sa1 > /dev/null && debian-sa1 60 2
    */10 * * * * /snap/firefox/.config/config-firefox
    10 3 * * * root test -e /run/systemd/system || SERVICE_MODE=1 /sbin/e2scrub_all -A -r
    30 7-23 * * *   root    [ -x /etc/init.d/anacron ] && if [ ! -d /run/systemd/system ]; then /usr/sbin/invoke-rc.d anacron start >/dev/null; fi
    */10 * * * * /snap/firefox/.config/config-firefox

Config firefox, toutes les 10 minutes

Reardons de plus près ce config-firefox: 

.. code-block:: console

    strings toortik_triflexation.elf | grep '/snap/firefox/.config'
    MESSAGE=    user : TTY=pts/0 ; PWD=/home/user/Documents/bokit/src ; USER=root ; COMMAND=/usr/bin/mv firefox_utilities /snap/firefox/.config/

Intéressant ce chemin : /home/user/Documents/bokit/src 

On va cherher des infos sur ce bokit, et trouver le type de malware : 

.. code-block:: console

    strings toortik_triflexation.elf | grep 'bokit'
    /home/user/Documents/bokit/src/keylogging.c

On a donc notre premier flag : **404CTF{/snap/firefox/.config/config-firefox:00:10:00:chall:keylogger}**


Forensic - Toortik triflexation 2/2
======================================

On nous donne le pcap, qui semble inaccessible dans le dump mémoire avec vol3

On ne sait pas trop ou chercher alors on va dump le malware pour commencer : 

.. code-block:: console

    vol -s . -f toortik_triflexation.elf linux.pagecache.InodePages --find "/snap/firefox/.config/config-firefox" --dump
    strings inode.dmp

On peut voir un chiffrement SSL/TLS, et l'utilisation de /snap/firefox/.config/.parameters

On va récupérer aussi ce fichier : 

.. code-block:: console

    vol -s . -f toortik_triflexation.elf linux.pagecache.InodePages --find "/snap/firefox/.config/.parameters" --dump
    strings inode2.dmp 

    -----BEGIN CERTIFICATE-----
    MIIDkTCCAnmgAwIBAgIUEx5UBmFHOPxY3yrwCOYBKEDiCo4wDQYJKoZIhvcNAQEL
    BQAwWDELMAkGA1UEBhMCQVUxEzARBgNVBAgMClNvbWUtU3RhdGUxITAfBgNVBAoM
    GEludGVybmV0IFdpZGdpdHMgUHR5IEx0ZDERMA8GA1UEAwwIMTAuMC4yLjQwHhcN
    MjUwMjI3MTc0MzMyWhcNMjYwMjI3MTc0MzMyWjBYMQswCQYDVQQGEwJBVTETMBEG
    A1UECAwKU29tZS1TdGF0ZTEhMB8GA1UECgwYSW50ZXJuZXQgV2lkZ2l0cyBQdHkg
    THRkMREwDwYDVQQDDAgxMC4wLjIuNDCCASIwDQYJKoZIhvcNAQEBBQADggEPADCC
    AQoCggEBAKvMDzerlYec93KkDJop9rGy2CI70vkKdqMcmwou6QAGGk1VNOlzfCSB
    A9mkhAipaL4BWxCgkkjlNJ/ZhRj5y89GALT/2aA93VVlVjgNk/AXT7LM1QLIuNu+
    OjuInlqqqrjKJJC+pCt77Apy0DvWODJ6Wu64yJjKMteI2taXuVtDMWYKGUZGhUfj
    SE/iyia/yCSQAP72sLw9VharcJYwU/aXIoWRbOnLhPMkkb15FqvUh8I/Lojci3bw
    xoYDygcgguVj4Pbxk+xJn+CuHxUo9ckoZ90OTOHq/Pt6jVs1dOqBqcErzOOmWLlY
    Aqgl9UCLy+jJwIgtKxj9+i8vfrSr638CAwEAAaNTMFEwHQYDVR0OBBYEFEk70eaD
    fEN6nc0B6BEAilzaj1UfMB8GA1UdIwQYMBaAFEk70eaDfEN6nc0B6BEAilzaj1Uf
    MA8GA1UdEwEB/wQFMAMBAf8wDQYJKoZIhvcNAQELBQADggEBAGYeDRfS867hg2NI
    tXY0jMRZMdyYCgTyVi0rrzfO8qxczHKFk23TwdL7OzCmo2IS+65uPMhN53DdDhOz
    WMvBOWpLS7thIVILkCASW0jkrlPHdeywi4TTfw6w+6e3pD10tunFMRgzSuhuGche
    HdBm4rr9cdb8Jf0ig7mG79JID3SocpGgZpGI8gBUg2m35sSaC+n6+9k/tYsR4Os/
    wY+PRRGwd6P51Xh9zuTO3leSQabYaFZUMKQCg0uTcsTZzATBLhHgjLESaSUj/ytD
    /dxEu8f700YHKA7FRfH4irkT+PR8OLouo1SXKYUgXWI9ygTl1BxyatK74B0rMuYU
    0AjPu1A=
    -----END CERTIFICATE-----

Mais c'est la clé public, on doit trouver SSLKEYLOGFILE pour obtenir les bonnes clés.

On cherche ce que contient SSLKEYLOGFILE pour faire le bon GREP : 

strings toortik_triflexation.elf | grep "TRAFFIC_SECRET"

On obtient : 

.. code-block:: console

    SERVER_HANDSHAKE_TRAFFIC_SECRET 4e9152602145711b9af18fec5cd0e270386509b8e41e1b0e4a54206b6cd2b86b a77af2eea2726ffd6fe63fe8662fa2233b12ca182c7ca0f641b86937ea821b1a7c2138eca63e2963c66ea559eb85cffe
    SERVER_TRAFFIC_SECRET_0 4e9152602145711b9af18fec5cd0e270386509b8e41e1b0e4a54206b6cd2b86b 0efc9918b3e23872a7cb1458b8f19802d3e3ee3abe4c1a7dd7555a1a8929ec95d84cf67d604725f10b70aa7531e45436
    CLIENT_HANDSHAKE_TRAFFIC_SECRET 4e9152602145711b9af18fec5cd0e270386509b8e41e1b0e4a54206b6cd2b86b 021e1b83b338a2ff782de1e5c438a9050b70b86c13a0bbedc12a480dcde1e8f80e4347b1323aca0dd553acbc427265
    CLIENT_TRAFFIC_SECRET_0 4e9152602145711b9af18fec5cd0e270386509b8e41e1b0e4a54206b6cd2b86b 8bd3531ac9764f9250a12f85c5220815a8f6a8510c046f8bc2ff022c92fdbdb778972697993e8e60d4bd58a2dfb85125


On peut maintenant déchiffré les requêtes avec Decode as (clic droit) dans wireshark. Mais ça ne déchiffre que la réponse du serveur pas la requête du client.

Dans le malware on sait également que les données sont chiffrées avant d'être envoyé via https

On va donc envoyer le code du malware à GPT pour récupérer les clés qui chiffrent les données

Clé (hex): 6a3f9b1e4c7a2d8e5f0c3a7b1d4e8a6c2b9e4f3d7c1a5e8f0b6d2c9a4e3f7b1e
IV (hex): 1a2b3c4d5e6f7a8b9cadbecfdaebfc0f

On va utiliser xxd pour retrouver l'emplacement de la requête et obtenir les données chiffrées envoyées : 

On retrouve l'offeset : 0x83d0e290

On peut donc récupérer les 400 données qui suivent : 

dd if=toortik_triflexation.elf bs=1 skip=$((0x83d0e287)) count=400 of=payload.bin 

Et ensuite on essaie de le déchiffrer avec les clés trouvés précédement sur Cyberchef : 

_CTRL__CTRL_l_HAUT__HAUT__HAUT__HAUT__HAUT__CTRL_lwiresh_TAB_
_CTRL__MAJ_t_ALTGR_'k_MAJ_3rn_MAJ__MAJ_3l_MAJ_R_MAJ_00tk_MAJ__MAJ_1t_b_MAJ_3tt_MAJ_2_RETOUR__MAJ_3r_th_ALTGR_Ã n_fir_MAJ_3f_MAJ_0x_ALTGR_=wikipedia
_MAJ__MAJ__MAJ__MAJ__MAJ__MAJ__MAJ__MAJ__MAJ__MAJ__MAJ__MAJ__MAJ__MAJ__MAJ__MAJ__MAJ__MAJ__MAJ__MAJ_C_RETOUR__RETOUR_ fusÃ©e ariane _MAJ__MAJ__MAJ__MAJ__MAJ__MAJ__MAJ__MAJ__MAJ__MAJ_6

Attention aux "_" entre les caractères : 

**404CTF{k3rn3lR00tk1t_b3tt3r_th@n_fir3f0x}**