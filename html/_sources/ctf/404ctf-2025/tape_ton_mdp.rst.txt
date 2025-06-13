
Forensic - Tape ton mdp
=========================

On a un fichier PCAP.

On voit des requêts /upload qui semble intéressante, par exemple : 

.. code-block:: console

    'Sd'	&lE	.>@@ 

    @
    rC!
    Kbg--------------------------f2d3a7abf634003f
    Content-Disposition: form-data; name="files"; filename="tmp1adq2tzw"
    Content-Type: application/octet-stream

    bGxISEk=VSjAZwAAAACouQgAAAAAAAQABAAcAAAA,VSjAZwAAAACouQgAAAAAAAEAHAAAAAAA,VSjAZwAAAACouQgAAAAAAAAAAAAAAAAA,VyjAZwAAAACgAQkAAAAAAAQABADbAAAA,VyjAZwAAAACgAQkAAAAAAAEAfQABAAAA,VyjAZwAAAACgAQkAAAAAAAAAAAAAAAAA,VyjAZwAAAACcQgsAAAAAAAQABADbAAAA,VyjAZwAAAACcQgsAAAAAAAEAfQAAAAAA,VyjAZwAAAACcQgsAAAAAAAAAAAAAAAAA,VyjAZwAAAABTRgsAAAAAAAQABAA4AAAA,VyjAZwAAAABTRgsAAAAAAAEAOAABAAAA,VyjAZwAAAABTRgsAAAAAAAAAAAAAAAAA,VyjAZwAAAACiSgsAAAAAAAQABAA7AAAA,VyjAZwAAAACiSgsAAAAAAAEAOwABAAAA,VyjAZwAAAACiSgsAAAAAAAAAAAAAAAAA,VyjAZwAAAAAUUwsAAAAAAAQABAA4AAAA,VyjAZwAAAAAUUwsAAAAAAAEAOAAAAAAA,VyjAZwAAAAAUUwsAAAAAAAAAAAAAAAAA,VyjAZwAAAAAgWwsAAAAAAAQABAA7AAAA,VyjAZwAAAAAgWwsAAAAAAAEAOwAAAAAA,VyjAZwAAAAAgWwsAAAAAAAAAAAAAAAAA,WCjAZwAAAAByXQwAAAAAAAQABAAhAAAA,WCjAZwAAAAByXQwAAAAAAAEAIQABAAAA,WCjAZwAAAAByXQwAAAAAAAAAAAAAAAAA,WCjAZwAAAACF2A0AAAAAAAQABAAhAAAA,WCjAZwAAAACF2A0AAAAAAAEAIQAAAAAA,WCjAZwAAAACF2A0AAAAAAAAAAAAAAAAA,WCjAZwAAAAAv5g0AAAAAAAQABAAXAAAA,WCjAZwAAAAAv5g0AAAAAAAEAFwABAAAA,WCjAZwAAAAAv5g0AAAAAAAAAAAAAAAAA,WSjAZwAAAAAvPgAAAAAAAAQABAAXAAAA,WSjAZwAAAAAvPgAAAAAAAAEAFwAAAAAA,WSjAZwAAAAAvPgAAAAAAAAAAAAAAAAAA,WijAZwAAAABi+gMAAAAAAAQABADNAAAA,WijAZwAAAABi+gMAAAAAAAEAagABAAAA,WijAZwAAAABi+gMAAAAAAAAAAAAAAAAA,WijAZwAAAADwCQYAAAAAAAQABADNAAAA,WijAZwAAAADwCQYAAAAAAAEAagAAAAAA,WijAZwAAAADwCQYAAAAAAAAAAAAAAAAA,XCjAZwAAAADBqQAAAAAAAAQABAAcAAAA,XCjAZwAAAADBqQAAAAAAAAEAHAABAAAA,XCjAZwAAAADBqQAAAAAAAAAAAAAAAAAA,XCjAZwAAAAAIRwIAAAAAAAQABAAcAAAA,XCjAZwAAAAAIRwIAAAAAAAEAHAAAAAAA,XCjAZwAAAAAIRwIAAAAAAAAAAAAAAAAA,YCjAZwAAAAClPwcAAAAAAAQABAAqAAAA,YCjAZwAAAAClPwcAAAAAAAEAKgABAAAA,YCjAZwAAAAClPwcAAAAAAAAAAAAAAAAA,YCjAZwAAAADUkwkAAAAAAAQABAAQAAAA,YCjAZwAAAADUkwkAAAAAAAEAEAABAAAA,YCjAZwAAAADUkwkAAAAAAAAAAAAAAAAA,YCjAZwAAAABhoQoAAAAAAAQABAAqAAAA,YCjAZwAAAABhoQoAAAAAAAEAKgAAAAAA,YCjAZwAAAABhoQoAAAAAAAAAAAAAAAAA,YCjAZwAAAABZ1gsAAAAAAAQABAATAAAA,YCjAZwAAAABZ1gsAAAAAAAEAEwABAAAA,YCjAZwAAAABZ1gsAAAAAAAAAAAAAAAAA,YCjAZwAAAACA4gsAAAAAAAQABAAQAAAA,YCjAZwAAAACA4gsAAAAAAAEAEAAAAAAA,YCjAZwAAAACA4gsAAAAAAAAAAAAAAAAA,YCjAZwAAAACiYw0AAAAAAAQABAATAAAA,YCjAZwAAAACiYw0AAAAAAAEAEwAAAAAA,

    --------------------------f2d3a7abf634003f--

Si on décode le base 64 : bGxISEk= 
On obtient : llHHI

En cherchant dans google "llHHI ctf writeup" on trouve un script de write up https://ctftime.org/writeup/21148

On peut voir que c'est des events linux, donc un keylogger probablement. 

On va adapter le script avec GPT, extraire tous les uploads et mettre chaque valeur sur une ligne.

Le script de solved : 

.. code-block:: python

    import base64
    import struct

    # Format de l'événement input
    FORMAT = 'llHHI'
    EVENT_SIZE = struct.calcsize(FORMAT)

    # Table de correspondance des codes de touches (partielle, extensible)
    keymap = {
        0: "KEY_RESERVED",
        1: "KEY_ESC",
        2: "KEY_1",
        3: "KEY_2",
        4: "KEY_3",
        5: "KEY_4",
        6: "KEY_5",
        7: "KEY_6",
        8: "KEY_7",
        9: "KEY_8",
        10: "KEY_9",
        11: "KEY_0",
        12: "KEY_MINUS",
        13: "KEY_EQUAL",
        14: "KEY_BACKSPACE",
        15: "KEY_TAB",
        16: "KEY_Q",
        17: "KEY_W",
        18: "KEY_E",
        19: "KEY_R",
        20: "KEY_T",
        21: "KEY_Y",
        22: "KEY_U",
        23: "KEY_I",
        24: "KEY_O",
        25: "KEY_P",
        26: "KEY_LEFTBRACE",
        27: "KEY_RIGHTBRACE",
        28: "KEY_ENTER",
        29: "KEY_LEFTCTRL",
        30: "KEY_A",
        31: "KEY_S",
        32: "KEY_D",
        33: "KEY_F",
        34: "KEY_G",
        35: "KEY_H",
        36: "KEY_J",
        37: "KEY_K",
        38: "KEY_L",
        39: "KEY_SEMICOLON",
        40: "KEY_APOSTROPHE",
        41: "KEY_GRAVE",
        42: "KEY_LEFTSHIFT",
        43: "KEY_BACKSLASH",
        44: "KEY_Z",
        45: "KEY_X",
        46: "KEY_C",
        47: "KEY_V",
        48: "KEY_B",
        49: "KEY_N",
        50: "KEY_M",
        51: "KEY_COMMA",
        52: "KEY_DOT",
        53: "KEY_SLASH",
        54: "KEY_RIGHTSHIFT",
        55: "KEY_KPASTERISK",
        56: "KEY_LEFTALT",
        57: "KEY_SPACE",
        58: "KEY_CAPSLOCK",
        59: "KEY_F1",
        60: "KEY_F2",
        61: "KEY_F3",
        62: "KEY_F4",
        63: "KEY_F5",
        64: "KEY_F6",
        65: "KEY_F7",
        66: "KEY_F8",
        67: "KEY_F9",
        68: "KEY_F10",
        69: "KEY_NUMLOCK",
        70: "KEY_SCROLLLOCK",
        71: "KEY_KP7",
        72: "KEY_KP8",
        73: "KEY_KP9",
        74: "KEY_KPMINUS",
        75: "KEY_KP4",
        76: "KEY_KP5",
        77: "KEY_KP6",
        78: "KEY_KPPLUS",
        79: "KEY_KP1",
        80: "KEY_KP2",
        81: "KEY_KP3",
        82: "KEY_KP0",
        83: "KEY_KPDOT",

        85: "KEY_ZENKAKUHANKAKU",
        86: "KEY_102ND",
        87: "KEY_F11",
        88: "KEY_F12",
        89: "KEY_RO",
        90: "KEY_KATAKANA",
        91: "KEY_HIRAGANA",
        92: "KEY_HENKAN",
        93: "KEY_KATAKANAHIRAGANA",
        94: "KEY_MUHENKAN",
        95: "KEY_KPJPCOMMA",
        96: "KEY_KPENTER",
        97: "KEY_RIGHTCTRL",
        98: "KEY_KPSLASH",
        99: "KEY_SYSRQ",
        100: "KEY_RIGHTALT",
        101: "KEY_LINEFEED",
        102: "KEY_HOME",
        103: "KEY_UP",
        104: "KEY_PAGEUP",
        105: "KEY_LEFT",
        106: "KEY_RIGHT",
        107: "KEY_END",
        108: "KEY_DOWN",
        109: "KEY_PAGEDOWN",
        110: "KEY_INSERT",
        111: "KEY_DELETE",
        112: "KEY_MACRO",
        113: "KEY_MUTE",
        114: "KEY_VOLUMEDOWN",
        115: "KEY_VOLUMEUP",
        116: "KEY_POWER",
        117: "KEY_KPEQUAL",
        118: "KEY_KPPLUSMINUS",
        119: "KEY_PAUSE",
        120: "KEY_SCALE",

        121: "KEY_KPCOMMA",
        122: "KEY_HANGEUL",
        122: "KEY_HANGUEL",
        123: "KEY_HANJA",
        124: "KEY_YEN",
        125: "KEY_LEFTMETA",
        126: "KEY_RIGHTMETA",
        127: "KEY_COMPOSE",

        128: "KEY_STOP",
        129: "KEY_AGAIN",
        130: "KEY_PROPS",
        131: "KEY_UNDO",
        132: "KEY_FRONT",
        133: "KEY_COPY",
        134: "KEY_OPEN",
        135: "KEY_PASTE",
        136: "KEY_FIND",
        137: "KEY_CUT",
        138: "KEY_HELP",
        139: "KEY_MENU",
        140: "KEY_CALC",
        141: "KEY_SETUP",
        142: "KEY_SLEEP",
        143: "KEY_WAKEUP",
        144: "KEY_FILE",
        145: "KEY_SENDFILE",
        146: "KEY_DELETEFILE",
        147: "KEY_XFER",
        148: "KEY_PROG1",
        149: "KEY_PROG2",
        150: "KEY_WWW",
        151: "KEY_MSDOS",
        152: "KEY_COFFEE",
        152: "KEY_SCREENLOCK",
        153: "KEY_ROTATE_DISPLAY",
        153: "KEY_DIRECTION",
        154: "KEY_CYCLEWINDOWS",
        155: "KEY_MAIL",
        156: "KEY_BOOKMARKS",
        157: "KEY_COMPUTER",
        158: "KEY_BACK",
        159: "KEY_FORWARD",
        160: "KEY_CLOSECD",
        161: "KEY_EJECTCD",
        162: "KEY_EJECTCLOSECD",
        163: "KEY_NEXTSONG",
        164: "KEY_PLAYPAUSE",
        165: "KEY_PREVIOUSSONG",
        166: "KEY_STOPCD",
        167: "KEY_RECORD",
        168: "KEY_REWIND",
        169: "KEY_PHONE",
        170: "KEY_ISO",
        171: "KEY_CONFIG",
        172: "KEY_HOMEPAGE",
        173: "KEY_REFRESH",
        174: "KEY_EXIT",
        175: "KEY_MOVE",
        176: "KEY_EDIT",
        177: "KEY_SCROLLUP",
        178: "KEY_SCROLLDOWN",
        179: "KEY_KPLEFTPAREN",
        180: "KEY_KPRIGHTPAREN",
        181: "KEY_NEW",
        182: "KEY_REDO",

        183: "KEY_F13",
        184: "KEY_F14",
        185: "KEY_F15",
        186: "KEY_F16",
        187: "KEY_F17",
        188: "KEY_F18",
        189: "KEY_F19",
        190: "KEY_F20",
        191: "KEY_F21",
        192: "KEY_F22",
        193: "KEY_F23",
        194: "KEY_F24",

        200: "KEY_PLAYCD",
        201: "KEY_PAUSECD",
        202: "KEY_PROG3",
        203: "KEY_PROG4",
        204: "KEY_DASHBOARD",
        205: "KEY_SUSPEND",
        206: "KEY_CLOSE",
        207: "KEY_PLAY",
        208: "KEY_FASTFORWARD",
        209: "KEY_BASSBOOST",
        210: "KEY_PRINT",
        211: "KEY_HP",
        212: "KEY_CAMERA",
        213: "KEY_SOUND",
        214: "KEY_QUESTION",
        215: "KEY_EMAIL",
        216: "KEY_CHAT",
        217: "KEY_SEARCH",
        218: "KEY_CONNECT",
        219: "KEY_FINANCE",
        220: "KEY_SPORT",
        221: "KEY_SHOP",
        222: "KEY_ALTERASE",
        223: "KEY_CANCEL",
        224: "KEY_BRIGHTNESSDOWN",
        225: "KEY_BRIGHTNESSUP",
        226: "KEY_MEDIA",

        227: "KEY_SWITCHVIDEOMODE",
        228: "KEY_KBDILLUMTOGGLE",
        229: "KEY_KBDILLUMDOWN",
        230: "KEY_KBDILLUMUP",

        231: "KEY_SEND",
        232: "KEY_REPLY",
        233: "KEY_FORWARDMAIL",
        234: "KEY_SAVE",
        235: "KEY_DOCUMENTS",

        236: "KEY_BATTERY",

        237: "KEY_BLUETOOTH",
        238: "KEY_WLAN",
        239: "KEY_UWB",

        240: "KEY_UNKNOWN",

        241: "KEY_VIDEO_NEXT",
        242: "KEY_VIDEO_PREV",
        243: "KEY_BRIGHTNESS_CYCLE",
        244: "KEY_BRIGHTNESS_AUTO",
        244: "KEY_BRIGHTNESS_ZERO",
        245: "KEY_DISPLAY_OFF",

        246: "KEY_WWAN",
        246: "KEY_WIMAX",
        247: "KEY_RFKILL",

        248: "KEY_MICMUTE"
    }

    print("Horodatage (s)       | Touche            | Action")
    print("----------------------|--------------------|--------")

    try:
        with open("test.txt", "r") as f:
            for line in f:
                b64 = line.strip()
                if not b64:
                    continue

                try:
                    raw = base64.b64decode(b64)
                    if len(raw) != EVENT_SIZE:
                        print(f"❌ Mauvaise taille pour : {b64}")
                        continue

                    tv_sec, tv_usec, type_, code, value = struct.unpack(FORMAT, raw)

                    if type_ != 1:
                        continue  # On garde uniquement les événements clavier

                    key_name = keymap.get(code, f"KEY_UNKNOWN({code})")
                    action = {0: "RELEASE", 1: "PRESS", 2: "REPEAT"}.get(value, f"UNKNOWN({value})")

                    print(f"{tv_sec}.{tv_usec:06d} | {key_name:<18} | {action}")

                except Exception as e:
                    print(f"⚠️ Erreur lors du décodage : {e} pour {b64}")

    except FileNotFoundError:
        print("❌ Fichier 'test.txt' introuvable.")
    except Exception as e:
        print(f"❌ Erreur générale : {e}")


Résultat (extrait) : 

1740646558.231725 | KEY_4              | PRESS
1740646558.357323 | KEY_4              | RELEASE
1740646558.395849 | KEY_0              | PRESS
1740646558.503405 | KEY_4              | PRESS
1740646558.509441 | KEY_0              | RELEASE
1740646558.609730 | KEY_4              | RELEASE
1740646559.162719 | KEY_C              | PRESS
1740646559.265382 | KEY_C              | RELEASE
1740646559.273450 | KEY_T              | PRESS
1740646559.378716 | KEY_T              | RELEASE
1740646559.455071 | KEY_F              | PRESS
1740646559.566434 | KEY_F              | RELEASE
1740646559.749002 | KEY_LEFTSHIFT      | RELEASE
1740646560.024234 | KEY_RIGHTALT       | PRESS
1740646560.274442 | KEY_RIGHTALT       | REPEAT
1740646560.307499 | KEY_RIGHTALT       | REPEAT
1740646560.340538 | KEY_RIGHTALT       | REPEAT
1740646560.373594 | KEY_RIGHTALT       | REPEAT
1740646560.385441 | KEY_4              | PRESS
1740646560.484666 | KEY_4              | RELEASE
1740646560.664530 | KEY_RIGHTALT       | RELEASE
1740646561.460380 | KEY_K              | PRESS
1740646561.568184 | KEY_K              | RELEASE
1740646561.656600 | KEY_LEFTSHIFT      | PRESS
1740646561.906810 | KEY_LEFTSHIFT      | REPEAT
1740646561.939831 | KEY_LEFTSHIFT      | REPEAT
1740646561.972870 | KEY_LEFTSHIFT      | REPEAT
1740646561.999214 | KEY_3              | PRESS
1740646562.112114 | KEY_3              | RELEASE
1740646562.123711 | KEY_LEFTSHIFT      | RELEASE
1740646562.322997 | KEY_Y              | PRESS
1740646562.434651 | KEY_Y              | RELEASE
1740646563.349943 | KEY_L              | PRESS
1740646563.447529 | KEY_L              | RELEASE
1740646563.667490 | KEY_LEFTSHIFT      | PRESS
1740646563.836440 | KEY_0              | PRESS
1740646563.930870 | KEY_0              | RELEASE
1740646563.976311 | KEY_LEFTSHIFT      | RELEASE
1740646565.056828 | KEY_G              | PRESS
1740646565.174532 | KEY_G              | RELEASE
1740646565.224718 | KEY_G              | PRESS
1740646565.321751 | KEY_G              | RELEASE
1740646565.439251 | KEY_LEFTSHIFT      | PRESS
1740646565.689455 | KEY_LEFTSHIFT      | REPEAT
1740646565.722477 | KEY_LEFTSHIFT      | REPEAT
1740646565.755515 | KEY_LEFTSHIFT      | REPEAT
1740646565.788556 | KEY_LEFTSHIFT      | REPEAT
1740646565.821615 | KEY_LEFTSHIFT      | REPEAT
1740646565.825247 | KEY_3              | PRESS
1740646565.919171 | KEY_3              | RELEASE
1740646565.946478 | KEY_LEFTSHIFT      | RELEASE
1740646566.562846 | KEY_R              | PRESS
1740646566.669623 | KEY_R              | RELEASE
1740646567.437147 | KEY_8              | PRESS
1740646567.549145 | KEY_8              | RELEASE
1740646569.036172 | KEY_LEFTSHIFT      | PRESS
1740646569.286366 | KEY_LEFTSHIFT      | REPEAT
1740646569.319409 | KEY_LEFTSHIFT      | REPEAT
1740646569.352455 | KEY_LEFTSHIFT      | REPEAT
1740646569.385509 | KEY_LEFTSHIFT      | REPEAT
1740646569.418568 | KEY_LEFTSHIFT      | REPEAT
1740646569.451581 | KEY_LEFTSHIFT      | REPEAT
1740646569.484982 | KEY_LEFTSHIFT      | REPEAT
1740646569.486101 | KEY_3              | PRESS
1740646569.579554 | KEY_3              | RELEASE
1740646569.601328 | KEY_LEFTSHIFT      | RELEASE
1740646569.930968 | KEY_X              | PRESS
1740646570.026034 | KEY_X              | RELEASE
1740646570.230773 | KEY_F              | PRESS
1740646570.314498 | KEY_F              | RELEASE
1740646571.972439 | KEY_LEFTSHIFT      | PRESS
1740646572.222625 | KEY_LEFTSHIFT      | REPEAT
1740646572.255657 | KEY_LEFTSHIFT      | REPEAT
1740646572.267604 | KEY_1              | PRESS
1740646572.378373 | KEY_1              | RELEASE
1740646572.385669 | KEY_LEFTSHIFT      | RELEASE
1740646572.556651 | KEY_L              | PRESS
1740646572.661119 | KEY_L              | RELEASE
1740646572.781062 | KEY_T              | PRESS
1740646572.920582 | KEY_T              | RELEASE
1740646574.423172 | KEY_R              | PRESS
1740646574.522194 | KEY_R              | RELEASE
1740646574.772927 | KEY_LEFTSHIFT      | PRESS
1740646575.023119 | KEY_LEFTSHIFT      | REPEAT
1740646575.056148 | KEY_LEFTSHIFT      | REPEAT
1740646575.089187 | KEY_LEFTSHIFT      | REPEAT
1740646575.122232 | KEY_LEFTSHIFT      | REPEAT
1740646575.155275 | KEY_LEFTSHIFT      | REPEAT
1740646575.188320 | KEY_LEFTSHIFT      | REPEAT
1740646575.221367 | KEY_LEFTSHIFT      | REPEAT
1740646575.228611 | KEY_4              | PRESS
1740646575.343659 | KEY_4              | RELEASE
1740646575.349629 | KEY_LEFTSHIFT      | RELEASE
1740646575.603569 | KEY_T              | PRESS
1740646575.697279 | KEY_T              | RELEASE
1740646575.943457 | KEY_LEFTSHIFT      | PRESS
1740646576.160691 | KEY_1              | PRESS
1740646576.238759 | KEY_LEFTSHIFT      | RELEASE
1740646576.267248 | KEY_1              | RELEASE
1740646576.739111 | KEY_LEFTSHIFT      | PRESS
1740646576.989285 | KEY_LEFTSHIFT      | REPEAT
1740646577.022304 | KEY_LEFTSHIFT      | REPEAT
1740646577.055343 | KEY_LEFTSHIFT      | REPEAT
1740646577.088378 | KEY_LEFTSHIFT      | REPEAT
1740646577.121426 | KEY_LEFTSHIFT      | REPEAT
1740646577.154446 | KEY_LEFTSHIFT      | REPEAT
1740646577.187481 | KEY_LEFTSHIFT      | REPEAT
1740646577.204796 | KEY_0              | PRESS
1740646577.303790 | KEY_0              | RELEASE
1740646577.365241 | KEY_LEFTSHIFT      | RELEASE
1740646577.819781 | KEY_N              | PRESS
1740646577.918344 | KEY_N              | RELEASE


On obtient alors le flag : **404CTF{k3yl0gg3r_3xf1ltr4t10n}**