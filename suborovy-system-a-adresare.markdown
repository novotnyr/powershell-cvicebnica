---
title: Cvičebnica z Powershellu -- súborový systém a adresáre
layout: page
---

Súborový systém a adresáre
==========================

Zistite, v ktorom adresári sa práve nachádzate.
-----------

Zistiť to môžeme pohľadom na výzvu (prompt).

	PS C:\Users\rn>

Zistite, v ktorom adresári sa práve nachádzate pomocou [`Get-Location`]
-----------------------------------------------------------------------

    Get-Location
    
Alias pre linuxových znalcov:

	pwd

Zobrazte súbory a adresáre v aktuálnom podadresári [`Get-ChildItem`]
----------------------------------------------------------------------------

	Get-ChildItem

Zobrazte súbory a adresáre v aktuálnom podadresári [`gci`]
----------------------------------------------------------
Príkaz možno skrátiť:

	gci

Zobrazte súbory a adresáre v aktuálnom podadresári [`ls`].
----------------------------------------------------------
Linuxácky alias:
	
	ls

Zobrazte súbory a adresáre v aktuálnom podadresári [`dir`]
-----------------------------------------------------------

Ak ste veteráni MS-DOSu a klasického windowsovského shellu `cmd.exe`,
môžete použiť:

	dir

Preštudujte dokumentáciu k `ls` / `dir` / `Get-ChildItem`
---------------------------------------------------------

Základný cmdlet pre získanie pomocníka pre cmdlet:

    Get-Help Get-ChildItem

Cmdlet má viacero aliasov:

    help Get-ChildItem
    
Samozrejme, funguje aj pre aliasy:
    
    help dir
    
Fungujú aj linuxové alternatívy:
	
	man ls

Príklady môžete získať cez parameter `-examples`:

	Get-Help Get-ChildItem -examples

Zistite všetky alternatívne názvy pre výpis súborov a adresárov
---------------------------------------------------------------
PowerShell neraz definuje pre jeden príkaz viacero alternatívnych
názvov. Základný príkaz pre zistenie plného mena cmdletu je:
    
    Get-Alias dir
    
Skrátená verzia, alebo *alias pre alias*:

	alias dir

alebo

	alias ls

Zobrazte len názvy súborov či adresárov v aktuálnom adresári
------------------------------------------------------------

    Get-ChildItem -Name
    
Použitie s linuxovým aliasom:

	ls –Name

Názvy parametrov možno skrátiť, pokiaľ nedôjde k nejednoznačnostiam.
	
	Get-ChildItem -n
	gci -n
	ls -n

Zapíšte názvy súborov a adresárov do externého súboru
-----------------------------------------------------

	Get-Child-Item -Name > subory.txt

Výstup ľubovoľného cmdletu možno zapísať do textového súboru použitím presmerovania cez `>`.

Overte existenciu súboru `subory.txt`
--------------

	Get-ChildItem subory.txt

Alternatívne:
	
    ls subory.txt	
	
Alternatívne pomocou cmdletu `Get-Item`˛

	Get-Item subory.txt

Alternatívne pomocou cmdletu `gi`, aliasu pre `Get-Item`

	gi subory.txt

Otvorte `subory.txt` v textovom editore
---------------

	notepad subory.txt

Adresáre
=========

Vypíšte obsah vlastného domovského adresára.
----------
Ak ste v domovskom adresári, tak:

	Get-ChildItem ~

Vlnka `~` je skratka pre cestu do domovského adresára.

Alternatívne:

	ls ~

Alternatívne:

	gci ~


Presuňte sa do adresára `C:\Windows`
-----------------------------------

	Set-Location C:\Windows

V praxi sa však oveľa lepšie používa osvedčený alias:

	cd C:\Windows

Vypíšte všetky súbory a adresáre v `C:/Users`
------

	Get-ChildItem C:\Users

Windows rozlišuje obyčajné i spätné lomky:

	Get-ChildItem C:/Users

Alternatívne:

	ls C:/Users

Alternatívne:

	gci C:\Users

Vypíšte všetky súbory a adresáre v `C:/Users`, ktoré sa začínajú na „P“.
------------------------------------------------------------
Možnosť 1:

	cd C:\Users
	Get-ChildItem P*

Možnosť 2:

	Get-ChildItem C:\Users\P*

Vypíšte všetky súbory typu XML v domovskom adresári.
-------------------------------------------

	Get-ChildItem ~ *.xml

Alternatívne:

	ls ~ *.xml

Vypíšte všetky súbory v aktuálnom adresári a vo všetkých jeho podadresároch.
---------------------------------

	Get-ChildItem –Recurse

Parametre možno skracovať, kým nedôjde k nejednoznačnosti:
	
	gci -r

Vypíšte všetky XML súbory v domovskom adresári a vo všetkých jeho podadresároch.
------------------------------------------
Pozor! Linuxáci skúsia použiť:

	ls ~ *.xml –r # nefunguje!

Prvým nepomenovaným argumentom musí byť aktuálny adresár, druhým je filter. Korektný príkaz je 

	Get-ChildItem ~ *.xml -Recurse

čo je skrátenina pre

	Get-ChildItem -Path . -Filter *.xml -Recurse 

Správna linuxácka skrátená verzia je:

	ls ~ *.xml –r

Prvý parameter je bodka indikujúca aktuálny adresár, druhý parameter je filter pre XML súbory.

<div class="note" markdown="1">
Pozrime si pomocníka k príkazu `Get-ChildItem`:

    Get-Help Get-ChildItem
    
Výsledkom bude:
    
    NAME
        Get-ChildItem
    
    SYNOPSIS
        Gets the items and child items in one or more specified locations.
    
    
    SYNTAX
        Get-ChildItem [[-Path] <string[]>] [[-Filter] <string>] [-Exclude <string[]>] [-Force] [-Include <string[]>] [-Name
        ] [-Recurse] [-UseTransaction] [<CommonParameters>]
        
Vidno, že najprv musíme uviesť parameter s cestou (`-Path`) a za ním
filter (`-Filter`).
</div>

Vypíšte len plochý zoznam súborov z predošlej úlohy
----------------
	
	Get-ChildItem ~ *.xml -Recurse -Name

Spočítajte, koľko je súborov typu XML v aktuálnom adresári a podadresároch
--------------------------------------------------------------------------

    (Get-ChildItem -Path . -Filter *.xml -Recurse).Count 

Každý cmdlet posiela do rúry niekoľko objektov. Všetky tieto objekty
možno chápať vcelku, ako *pole objektov*, ktoré je tak isto len objekt s
vlastnosťami (properties) a schopnosťami (metódami).

V tomto prípade sme použitím zátvoriek zobrali všetky objekty vytvorené
cmdletom `Get-ChildItem`, pozreli sa na ne ako na *pole* a zistili ich
počet pomocou bodkovej notácie a vlastnosti `.Count`.

Spočítajte, koľko je súborov typu XML v aktuálnom adresári a podadresároch (rúra, `Measure-Object`)
---------------------------------------------------------------------------------------------------

	Get-ChildItem ~ *.xml -Recurse | Measure-Object
	
Objekty, ktoré vytvoril cmdlet `Get-ChildItem` môžeme použiť ako vstup
iného cmdletu. Dva cmdlety vieme spojiť **rúrou** (objektovodom,
dátovodom), ktorým putujú objekty z jedného cmdletu do druhého.

Vystavajme rúru a výsledok z `Get-ChildItem` pošlime do cmdletu `Measure-Object`, ktorý
vie robiť štatistické výpočty.

Vo výslednej položke `Count` nájdeme výsledok.

Cmdlet `Measure-Object` má alias `Measure`:

	ls ~ *.xml -r | measure

Spočítajte, koľko miesta zaberajú všetky súbory v domovskom adresári
--------------------------------------------------------------------

Cmdlet `Measure-Object` má parameter `–Sum`, ktorý popri počte položiek vie
rátať aj sumy veľkostí. Musíme však určiť, podľa ktorej vlastnosti objektu sa má
sčítavať veľkosť. Dosiahneme to pomocou prepínača `–Property`.

	Get-ChildItem ~ -Recurse | Measure-Object -Sum –Property Length

Každý súbor má atribút `Length` udávajúci jeho veľkosť (adresáre ju majú prázdnu).

V cmdlete `Measure-Object` môžeme vynechať samotný názov parametra `-Property`:

	ls ~ *.xml -r | measure -sum length

Vypíšte len adresáre v domovskom adresári [`Where-Object`]
------------------------------

Vypisujme adresáre, ktoré majú isté vlastnosti, resp. ktoré spĺňajú danú 
podmienku. Inými slovami, do výstupu pošleme len adresáre, ktoré prejdú filtrom.
Na filtrovanie slúži cmdlet `Where-Object`:

	Get-ChildItem | Where-Object { $_.PSIsContainer }

V kučeravých zátvorkách sa nachádza výraz (*expression*) s booleovskou podmienkou. 

Cmdlet `Get-ChildItem` posiela do rúry objekt za objektom, adresár za adresárom,
súbor za súborom. Cmdlet `Where-Object` sa pozerá na objekt prichádzajúce z
rúry, dosadí ho do špeciálnej premennej `$_` a v prípade, že je podmienka
splnená, ho pošle ďalej do rúry. Ak podmienka splnená nie je, objekt sa zahodí a
do rúry sa už nepošle.

Každý adresár alebo súbor má vlastnosť `PSIsContainer`, ktorá je pravdivá pre
adresáre, ale pre súbory už nie. Vieme ju použiť v podmienke.

Alternatívny alias je `?`

	ls | ? { $_.PSIsContainer }

<div markdown="1" class="alternative-solution">
V PowerShelli 3.0 a novšom možno použiť parameter `-Directory` na cmdlete `Get-ChildItem` a filtrovanie môžeme vynechať:

	Get-ChildItem -Directory

</div>

Zistite, koľko súborov sa nachádza v domovskom adresári a jeho podadresároch.
---------------------------------------

	Get-ChildItem -Recurse | 
	    Where-Object {-not $_.PSIsContainer} | 
	        Measure-Object

V podmienke filtra sme použili negáciu cez `-not`. Alternatívne môžeme použiť aj výkričník:

	Get-ChildItem -Recurse | 
	    Where-Object {! $_.PSIsContainer} | 
	        Measure-Object

Skrátený variant:

	ls -r | ? { ! $_.PSIsContainer} | measure

Vypíšte súbory a adresáre v domovskom adresári zotriedené podľa mena
------------------------------

	Get-ChildItem | Sort-Object

Cmdlet `Sort-Object` usporiadava objekty podľa ľubovoľného kritéria. V tomto
prípade triedi podľa mena.

Skrátený alias:

	ls | sort

Vypíšte len súbory zotriedené podľa veľkosti
--------

	Get-ChildItem | 
	    Where-Object { -not $_.PSIsContainer } | 
	        Sort-Object -Property Length

Cmdlet `Sort-Object` má parameter, v ktorom určíme vlastnosť, podľa ktorého sa
má triediť. Triedime podľa veľkosti (`length`).

Skrátený alias:

	ls | ? { ! $_.PSIsContainer } | sort length

Vypíšte súbory a adresáre zotriedené podľa veľkosti zostupne.
-----------------------------------

	Get-ChildItem | Sort-Object -Property Length -Descending

Parameter možno skrátiť:

	ls | sort length –desc

Ak sa parameter skráti na `–d`, nastane chyba nejednoznačnosti názvu parametra 
(pri sorte existujú dva: `-debug` a `–descending`)

Nájdite najväčší súbor v domovskom adresári, bez vnárania ([`Select`])
---------------------------------------------

	Get-ChildItem | Sort-Object -Property Length -Descending | Select-Object -First 1

Cmdlet `Select-Object` dokáže filtrovať prvých/posledných *n* záznamov, 
stránkovať a podobne. V našom príklade sme vybrali prvý objekt z rúry, resp. prvý objekt zo zoznamu súborov utriedených podľa veľkosti

	ls | sort length -Desc | select -f 1

Vypíšte plné cesty k všetkým súborom a adresárom v aktuálnom adresári
---------------

	Get-ChildItem | Select-Object FullName

Cmdlet `Select-Object` vie tiež vytiahnuť z každého objektu, ktorý príde z rúry,
 len vybrané vlastnosti:

	FullName
	--------
	C:\Users\novotnyr\Contacts
	C:\Users\novotnyr\Desktop
	C:\Users\novotnyr\Documents
	C:\Users\novotnyr\Downloads
	C:\Users\novotnyr\Favorites
	C:\Users\novotnyr\Links
	C:\Users\novotnyr\Music
	C:\Users\novotnyr\Pictures
	C:\Users\novotnyr\Saved Games
	C:\Users\novotnyr\Searches
	C:\Users\novotnyr\tyzdne
	C:\Users\novotnyr\Videos

Pre každý objekt, ktorý príde z rúry, pošle `Select-Object` do rúry nový objekt
 len s tými vlastnosťami, ktoré boli spomenuté v parametri. V príklade pošle cmdlet do výsledku objekty s jedným atribútom `FullName`.

Alias:

	ls | select fullname

<div class="note" markdown="1">
Pri výpise adresára cez `Get-ChildItem` sme videli len štyri vlastnosti (`Mode`,
 `LastWriteTime`, `Length` a `Name`). Odkiaľ sme sa dozvedeli o vlastnosti 
 `FullName`? Na to sa dá použiť cmdlet-inšpektor `Get-Member`, ktorý vie 
 preskúmať dostupné vlastnosti objektu v rúre. O tomto cmdlete si povieme neskôr.  
</div>

Vypíšte len plné cesty k všetkým súborom a adresárom v aktuálnom adresári, bez dekorácie
---------------

	Get-ChildItem | Select-Object -ExpandProperty FullName

Parameter `-ExpandProperty FullName` zoberie z rúry objekt, pristúpi k vlastnosti uvedenej v parametri, a jej hodnotu pošle do rúry.

	C:\Users\novotnyr\Contacts
	C:\Users\novotnyr\Desktop
	C:\Users\novotnyr\Documents
	C:\Users\novotnyr\Downloads
	C:\Users\novotnyr\Favorites
	C:\Users\novotnyr\Links
	C:\Users\novotnyr\Music
	C:\Users\novotnyr\Pictures
	C:\Users\novotnyr\Saved Games
	C:\Users\novotnyr\Searches
	C:\Users\novotnyr\tyzdne
	C:\Users\novotnyr\Videos

V tomto prípade pošleme do rúry zoznam reťazcov s plnými názvami súborov.

Alternatívny zápis:

	ls | select -exp fullname

Nájdite plnú cestu a veľkosť najväčšieho súboru v domovskom adresári vrátane podadresárov.
--------------------

	Get-ChildItem -Recurse | 
	    Sort-Object -Property Length -Descending | 
	        Select-Object FullName, Length -First 1

Cmdlet `Select-Object` generuje objekty s dvoma vlastnosťami: plnú cestu 
(`FullName`) a dĺžku (`Length`), a zo zoznamu vyberie len prvý objekt.

Alternatívny zápis:

	ls -r | sort length -desc | select FullName, Length -first 1

Nájdite plné cesty k všetkým súborom väčším než 20 MB.
-------------------------------

	Get-ChildItem | 
	    Where-Object { $_.Length -ge 20MB } | 
	        Select-Object -ExpandProperty FullName

Pozor! Porovnanie sa realizuje pomocou `–ge`. Znak `>` znamená presmerovanie 
a jeho použitie v porovnávaní povedie k chybe, resp. k presmerovaní do súboru 
s obskúrnym názvom.

Alternatívny zápis:

	ls -r | ? {$_.length -ge 20MB} | select FullName

<div markdown="1" class="alternative-solution">
Alternatívny zápis pre Powershell 3.0: 

	ls -r | ? length -ge 20MB | select FullName

</div>

Vypíšte súbory a adresáre určené len na čítanie.
----------------------------
Využime vlastnosť `Mode`:

	Get-ChildItem | Where-Object { $_.Mode -like "*r*" }

Operátor `–like` funguje podobne ako v databázach, pričom vieme vyhľadávať s použitím žolíkov. Znak `*` funguje ako zástupný symbol pre jeden či viac znakov.

	ls | ? {$_.mode -like "*r*"}

Alternatívna, ale mylná možnosť je využiť vlastnosť `isReadOnly`; tá však funguje len na súboroch.


