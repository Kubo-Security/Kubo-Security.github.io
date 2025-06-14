
Reverse - Bugdroid Fight
============================

Enoncé
----------


Résolution
---------------

On met l'APK dans MobSF : https://mobsf.live/static_analyzer/b6b8c45a4243a6de63fcdaf86a1f6a10/

Dans org.example.reverseintro on trouve le fichier MainActivity. 

Dedans on trouve : 

.. code-block:: console
		
  public static final String element = "Br4v0_tU_as_"; 
  
  if (!Intrinsics.areEqual(name, element + StringResources_androidKt.stringResource(R.string.attr_special, startRestartGroup, 0) + new Utils().lastPart)) {
      startRestartGroup.startReplaceableGroup(-332944370);
      Modifier modifier2 = companion;
      TextKt.m1550Text4IGK_g("Bien joué, un bugdroid de vaincu !", modifier2, 0L, 0L, (FontStyle) null, (FontWeight) null, (FontFamily) null, 0L, (TextDecoration) null, (TextAlign) null, 0L, 0, false, 0, 0, (Function1<? super TextLayoutResult, Unit>) null, (TextStyle) null, startRestartGroup, (i4 & 112) | 6, 0, 131068);
      startRestartGroup.endReplaceableGroup();



On a donc "element", maintenant il nous faut le string attr_special, et le Utils.lastpart()

On trouve rapidement le Utils.lastpart() dans le fichier Utils.java dans le même dossier : 

.. code-block:: console
		
	String lastPart = "_m3S5ag3!";


Enfin, on retourne sur la page de l'APK dans MOBSF, on dépile la liste de toutes les STRINGS et on cherche "attr_special" : 

.. code-block:: console
		
	"attr_special" : "tr0uv3_m0N"

On combine les 3 parties et on obtient le flag : **404CTF{Br4v0_tU_as_tr0uv3_m0N_m3S5ag3!}**