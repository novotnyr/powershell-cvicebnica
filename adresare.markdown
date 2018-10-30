---
title: Cvičebnica z Powershellu -- adresáre
layout: page
---

Súborový systém a adresáre
==========================

Zistite, v ktorom adresári sa práve nachádzate
----------------------------------------------

Zistiť to môžeme pohľadom na výzvu (prompt).

	PS C:\Users\rn>

Zistite, v ktorom adresári sa práve nachádzate pomocou [`Get-Location`]
-----------------------------------------------------------------------

    Get-Location

### Alternatívne riešenie: vynechanie `Get`

    Location

Ak má cmdlet názov `Get-[podstatné-meno]`, môžeme predponu `Get`
vynechať.

<div class="note alias" markdown="1">
Aliasy `Get-Location`:

* Skratka: `gl`
* Linuxovský alias: `pwd`
</div>

Zobrazte súbory a adresáre v aktuálnom podadresári [`Get-ChildItem`]
--------------------------------------------------------------------

	Get-ChildItem

<div class="note alias" markdown="1">
Aliasy `Get-ChildItem`:

* Skratka: `gci`
* Pre pamätníkov MS-DOSu a starého shellu: `dir`
* Linuxovský alias na Windowse: `ls`
</div>

Získajte dokumentáciu ku cmdletu `Get-ChildItem`
------------------------------------------------

    Get-Help Get-ChildItem

<div class="note minified" markdown="1">
	help gci
</div>

<div class="note alias" markdown="1">
Aliasy `Get-Help`:

* Skratka: `help`
* Pre pamätníkov MS-DOSu a starého shellu: `dir`
* Linuxovský alias na Windowse: `man`
</div>

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

<div class="note minified" markdown="1">

	Get-ChildItem -n
	gci -n

Názvy parametrov možno skrátiť, pokiaľ nedôjde k nejednoznačnostiam.
</div>


Zapíšte názvy súborov a adresárov do externého súboru
-----------------------------------------------------

	Get-ChildItem -Name > subory.txt

Výstup ľubovoľného cmdletu možno zapísať do textového súboru použitím presmerovania cez `>`.

Vypíšte všetky súbory a adresáre v aktuálnom adresári, vrátane skrytých
-----------------------------------------------------------------------

    Get-ChildItem -Force

Overte existenciu súboru `subory.txt` [`Get-Item`]
--------------------------------------------------

	Get-Item subory.txt

<div class="note alias" markdown="1">
Aliasy `Get-Item`:

* Skratka: `gi`
</div>

Otvorte `subory.txt` v textovom editore
---------------------------------------

	notepad subory.txt

Adresáre
========

Vypíšte obsah vlastného domovského adresára
-------------------------------------------

	Get-ChildItem ~

Vlnka `~` je skratka pre cestu do domovského adresára.

Presuňte sa do adresára `C:\Windows` [`Set-Location`
----------------------------------------------------

	Set-Location C:\Windows

V praxi sa však oveľa lepšie používa osvedčený alias:

	cd C:\Windows

Vypíšte všetky súbory a adresáre v `C:/Users`
---------------------------------------------

	Get-ChildItem C:\Users

Windows rozlišuje obyčajné i spätné lomky:

	Get-ChildItem C:/Users

Vypíšte všetky súbory a adresáre v `C:/Users`, ktoré sa začínajú na „P“.
------------------------------------------------------------------------

	Get-ChildItem C:\Users\P*

Vypíšte všetky súbory typu XML v domovskom adresári
---------------------------------------------------

	Get-ChildItem ~ *.xml

Vypíšte všetky súbory v aktuálnom adresári a vo všetkých jeho podadresároch
---------------------------------------------------------------------------

	Get-ChildItem –Recurse

<div class="note minified" markdown="1">
    gci -r
</div>

Vypíšte všetky XML súbory v domovskom adresári a vo všetkých jeho podadresároch
-------------------------------------------------------------------------------

	Get-ChildItem -Path . -Filter *.xml -Recurse 

### Skrátený zápis

	Get-ChildItem ~ *.xml -Recurse

<div class="note" markdown="1">
Všimnime si [dokumentáciu](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-childitem), získanú napr. cez `Get-Help Get-ChildItem`:

    Get-ChildItem
       [[-Path] <String[]>]
       [[-Filter] <String>]
       ...
       [-Recurse]
       ...

Prvé dva argumenty sú *pozičné*, teda nemusia mať svoj názov. Prvým
pozičným argumentom je adresár, v ktorom sa vypisujú súbory, a druhým
pozičným argumentom je filter.
</div>

### Alternatívne riešenie: linuxovské aliasy

	ls ~ *.xml –r

Vypíšte len plochý zoznam súborov z predošlej úlohy
---------------------------------------------------
	
	Get-ChildItem ~ *.xml -Recurse -Name

Vypíšte všetky súbory a adresáre v aktuálnom adresári
-----------------------------------------------------

    Get-ChildItem -Path ~ -Force -Recurse -ErrorAction SilentlyContinue

Ak cmdlet pri spracovávaní vstupu narazí na chybu, môžeme
prispôsobiť jeho správanie. V našom prípade sa môže stať, že k niektorým
adresárom nemáme právo prístupu, čo spôsobí chybový červený výpis
v konzole.

Ak nastavíme parameter `-ErrorAction` na `SilentlyContinue`, takéto
chyby sa budú ignorovať a výpis bude potlačený.

Možnosti sú:

- `Continue`: bežná možnosť, kde sa chyba vypíše, ale spracovávanie pokračuje
- `Stop`: ak cmdlet narazí na chybu, zastaví sa
- `SilentlyContinue`: chyby sa schovajú, ale spracovávanie pobeží ďalej
- `Inquire`: cmdlet vyzve používateľa na určenie ďalšieho postupu


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

<div class="note alias" markdown="1">
Aliasy `Measure-Object`:

* `measure`
</div>

Spočítajte, koľko miesta zaberajú všetky súbory v domovskom adresári
--------------------------------------------------------------------

Cmdlet `Measure-Object` má parameter `–Sum`, ktorý popri počte položiek vie
rátať aj sumy veľkostí. Musíme však určiť, podľa ktorej vlastnosti objektu sa má
sčítavať veľkosť. Dosiahneme to pomocou prepínača `–Property`.

	Get-ChildItem ~ -Recurse | Measure-Object -Sum –Property Length

Každý súbor má atribút `Length` udávajúci jeho veľkosť (adresáre ju majú prázdnu).

V cmdlete `Measure-Object` môžeme vynechať samotný názov parametra `-Property`:

	gci ~ *.xml -r | measure -sum length

Vypíšte len adresáre v domovskom adresári
-----------------------------------------

Cmdlet `Get-ChildItem` používaný na súboroch a adresároch súborového
systému poskytuje parameter `-Directory`, ktorý ukáže len adresáre

    Get-ChildItem -Directory

Zistite, koľko súborov sa nachádza v domovskom adresári a jeho podadresároch.
-----------------------------------------------------------------------------
Pomocou `Get-ChildItem` rekurzívne (`-Recurse`) vypíšeme všetky riadne súbory (`-File`).

	Get-ChildItem -Recurse -File | 
        Measure-Object

Skrátený variant:

	gci -r -file | measure

Vypíšte súbory a adresáre v domovskom adresári zotriedené podľa mena
--------------------------------------------------------------------

	Get-ChildItem | Sort-Object

Cmdlet `Sort-Object` usporiadava objekty podľa ľubovoľného kritéria. V tomto
prípade triedi podľa mena.

<div class="note alias" markdown="1">
Aliasy `Sort-Object`:

* Linuxová skrátka na Windowse: `sort`
</div>

Vypíšte len súbory zotriedené podľa veľkosti
--------------------------------------------

	Get-ChildItem -File | 
        Sort-Object -Property Length

Cmdlet `Sort-Object` má parameter, v ktorom určíme vlastnosť, podľa ktorého sa
má triediť. Triedime podľa veľkosti (`length`).

<div class="note minified" markdown="1">

	gci -file | sort-object length

Parameter `-Property` je pozičný, preto ho možno vynechať.
</div>

Vypíšte súbory a adresáre zotriedené podľa veľkosti zostupne
------------------------------------------------------------

	Get-ChildItem | Sort-Object -Property Length -Descending

<div class="note minified" markdown="1">

	gci | sort-object length -desc

Ak sa parameter skráti na `–d`, nastane chyba nejednoznačnosti názvu parametra,
pretože existuje `-Debug` a `-Descending`.
</div>

Nájdite najväčší súbor v domovskom adresári, bez vnárania (`Select-Object`)
---------------------------------------------------------------------------

	Get-ChildItem | 
	    Sort-Object -Property Length -Descending | 
	        Select-Object -First 1

Cmdlet `Select-Object` dokáže filtrovať prvých/posledných *n* záznamov, 
stránkovať a podobne. V našom príklade sme vybrali prvý objekt z rúry, resp.
prvý objekt zo zoznamu súborov utriedených podľa veľkosti

<div class="note alias" markdown="1">
Aliasy `Select-Object`:

* `select`

</div>

Vypíšte plné cesty k všetkým súborom a adresárom v aktuálnom adresári
---------------------------------------------------------------------

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

### Alternatívne riešenie: výpis bez dekorácie

	Get-ChildItem | Select-Object -ExpandProperty FullName

Parameter `-ExpandProperty FullName` zoberie z rúry objekt, pristúpi
k vlastnosti uvedenej v parametri, a jej hodnotu pošle do rúry.

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

### Alternatívne riešenie: prístup ku vlastnosti pre všetky objekty v rúre

	(Get-ChildItem).FullName

Zápis umožňuje získať hodnotu vlastnosti `FullName` na každom objekte
z rúry. Tento zápis sa správa úplne rovnako ako predošlý variant
so `Select-Object -ExpandProperty FullName`.

Nájdite plnú cestu a veľkosť najväčšieho súboru v domovskom adresári vrátane podadresárov
-----------------------------------------------------------------------------------------

	Get-ChildItem -Recurse | 
	    Sort-Object -Property Length -Descending | 
	        Select-Object FullName, Length -First 1

Cmdlet `Select-Object` generuje objekty s dvoma vlastnosťami: plnú cestu 
(`FullName`) a dĺžku (`Length`), a zo zoznamu vyberie len prvý objekt.

<div class="note minified" markdown="1">

	gci -r | sort-object length -desc | select fullname, length -f 1

</div>

Nájdite plné cesty k všetkým súborom väčším než 20 MB [`Where-Object`]
----------------------------------------------------------------------
Vypisujme súbory, ktoré majú isté vlastnosti, resp. ktoré spĺňajú danú
podmienku. Inými slovami, do výstupu pošleme len súbory, ktoré prejdú filtrom.
Na filtrovanie slúži cmdlet `Where-Object`:

	(Get-ChildItem -Recurse | Where-Object Length -ge 20MB).FullName

Cmdlet `Get-ChildItem` posiela do rúry objekt za objektom, adresár za adresárom,
súbor za súborom. Cmdlet `Where-Object` sa postupne pozerá na objekty prichádzajúce
z rúry. Pre každý objekt zistí, či preňho platí príslušná podmienka a ak áno,
pošle ho ďalej do rúry. Ak podmienka splnená nie je, objekt sa zahodí a
do rúry sa už nepošle.

V našom prípade podmienka hovorí, že vlastnosť (_property_) `Length`
každého objektu musí byť väčšia než 20 MB.

Ale pozor! Porovnanie sa realizuje pomocou `–ge`. Znak `>` znamená presmerovanie
a jeho použitie v porovnávaní povedie k chybe, resp. k presmerovaní do súboru 
s obskúrnym názvom.

Powershell umožňuje definovať veľkosti v čitateľnom tvare. Hodnota `20 MB`
sa automaticky prepočíta na správnu veľkosť `20971520` bajtov.


Nájdite plné cesty k všetkým súborom z roku 2015 [`Where-Object`]
-----------------------------------------------------------------
Každý súbor má vlastnosť `LastWriteTime`, ktorá obsahuje dátum a čas
posledného zápisu. Hodnota tejto vlastnosti je tiež objekt (typu `datetime`),
ktorý má opäť svoje vlastné vlastnosti. (Objekty môžu pozostávať z iných
objektov, čo si povieme v neskoršej kapitole o objektovo orientovanom programovaní).

Rok vytvorenia súboru vieme zistiť z vnorenej vlastnosti, na ktorú sa
odkážeme pomocou `LastWriteTime.Year`.

V tomto prípade však **nemôžeme** použiť tento príkaz:

    # nefunguje!
    Get-ChildItem | Where-Object LastWriteTime.Year -eq 2015

Cmdlet `Where-Object` totiž nepodporuje vnorené vlastnosti v takomto tvare.
Čo je horšie, vôbec nás neupozorní na to, že takýto zápis nie je podporovaný
a vždy vráti prázdny výsledok!

Namiesto toho musíme použiť iný zápis podmienky a to v podobe *bloku skriptu*
(*script block*), ktorý pripomína programovanie v Jave, či C.

    Get-ChildItem | Where-Object { $_.LastWriteTime.Year -eq 2015 }

Cmdlet `Where-Object` vezme objekt prichádzajúci z rúry, dosadí ho
do špeciálnej premennej `$_` a overí platnosť podmienky.

Premenná `$_` má zvláštny názov, ale jej význam je jednoduchý: reprezentuje
objekt, ktorého vlastnosť overujeme. Ak nás stále znepokojuje, môžeme
použiť aj alternatívny názov `$PSItem`.

V príklade overujeme vlastnosť `LastWriteTime.Year`, ktorú porovnávame
s príslušnou hodnotou.

<div class="note" markdown="1">
V starších skriptoch možno vidieť aj alternatívnu premennú `$PSItem`.
Má rovnakú hodnotu ako `$_`.
</div>

Nájdite plné cesty k všetkým súborom z roku 2015, ktoré sú menšie ako 1 GB [`Where-Object`]
-------------------------------------------------------------------------------------------

Budeme potrebovať spájanie podmienok. Dve podmienky môžeme spojiť
cez operátor `-and` reprezentujúci logické "a zároveň":

    Get-ChildItem | 
        Where-Object { $_.LastWriteTime.Year -eq 2015 -and $_.Length -le 1GB }

Všimnime si, že pýtame sa na dve vlastnosti objektu, ktorý je reprezentovaný
premennou `$_`: a to rok posledného zápisu a dĺžku.

Vypíšte všetky adresáre, ktoré obsahujú aspoň jednu MP3ku [`Sort-Object` a `Get-Unique`]
----------------------------------------------------------------------------------------

Najprv získame zoznam všetkých MP3 súborov. Následne pre každý súbor získame názov
nadradeného adresára a pošleme ho ďalej do rúry. Aby sme predišli
opakovaným výskytom, adresáre zotriedime (`Sort-Object`), aby sme ich
mohli prehnať cez `Get-Unique`, ktorý vyhodí duplicitné riadky.

Triedenie je potrebné, pretože bez nich `Get-Unique` nebude fungovať správne.

    (Get-ChildItem . *.mp3 -Recurse).DirectoryName
        Sort-Object | 
            Get-Unique

Spočítajte frekvencie prípon súborov v adresári [grouping, `Group-Object`]
--------------------------------------------------------------------------
Ak potrebujeme zoskupiť objekty do viacerých sád podľa rovnakej vlastnosti,
môžeme použiť cmdlet `Group-Object`. V prvom riešení zistime, koľko
súborov zdieľa spoločné prípony. Zoskupme teda súbory podľa prípony:

    Get-ChildItem -Recurse | 
        Group-Object Extension

<div class="note alias" markdown="1">
Aliasy `Group-Object`:

* Skratka: `group`
</div>

Výstupom bude:

    Count Name      Group
    ----- ----      -----
       17 .html     {index.html, default.html, page.html, adresare.html...}
       13 .markdown {adresare.markdown, dot-net.markdown...}

Ak chceme vypísať len prípony a počty, prípadne ich usporiadať podľa
frekvencie, stačí výsledok zotriediť a vybrať len príslušné vlastnosti:

    Get-ChildItem -Recurse | 
        Group-Object Extension | 
            Sort-Object Count -Descending | 
                Select-Object Name, Count
