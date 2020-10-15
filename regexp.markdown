---
title: Cvičebnica z Powershellu -- regulárne výrazy
layout: page
---

Regulárne výrazy
================

Regulárny výraz je reťazec predstavujúci predpis pre vyhľadávanie zložitých vzorcov v textoch. Podobnú, aj keď jednoduchšiu ideu, predstavujú zástupné znaky pri vyhľadávaní v názvoch súboroch, kam patrí známa `*` (predstavujúca neznáme znaky), či otáznik (reprezentujúci jeden znak).

Regulárne výrazy však môžu obsahovať veľmi zložité predpisy pre vyhľadávanie, ktoré by sa inak muselo zložitým spôsobom programovať. Ide o situácie, keď chceme hľadať opakovania reťazcov, vyhľadávať so sadou povolených, či zakázaných znakov, alebo hľadať viacero podreťazcov súčasne.

Ukážkové dáta
-------------

Predstavme si ukážkové dáta v súbore `windows.txt`.

	1995-08-24 Windows 95 [Chicago] #win 
	1998-06-25 Windows 98 [Memphis] #win
	2000-02-17 Windows 2000 [NT 5.0] #win-nt
	2000-09-14 Windows ME [Millenium] #win
	2001-10-25 Windows XP [Whistler] #win #win-nt
	2003-04-24 Windows Server 2003 [Whistler Server] #win #win-nt #server
	2006-11-30 Windows Vista [Whistler] #win #win-nt
	2008-02-27 Windows Server 2008 [Longhorn Server] #win #win-nt #server
	2009-10-22 Windows 7 [Vienna] #win #win-nt
	2009-10-22 Windows Server 2008 R2 [Longhorn Server] #win #win-nt #server
	2012-09-04 Windows Server 2012 [Windows Server 8] #win #win-nt #server
	2012-10-26 Windows 8 [8] #win #win-nt
	2013-10-18 Windows 8.1 [Blue] #win #win-nt
	2013-10-18 Windows Server 2012 R2 [Windows Server 8] #win #win-nt #server
	2015-07-29 Windows 10 [Threshold] #win #win-nt
	2016-09-26 Windows Server 2016 [vNext] #win #win-nt #server

Vypíšte vydania Windowsu vydané v roku 2015 [`Where-Object` a `match`]
----------------------------------------------------------------------

	Get-Content windows.txt | Select-String 2015

Každý riadok súboru sa odošle do rúry, kde je filtrovaný pomocou operátora `-match`. Tento operátor berie ako parameter *regulárny výraz*, ktorý sa musí zhodovať s riadkom súboru.

`2015` v ukážke je najjednoduchší regulárny výraz: reprezentuje doslovné hľadanie reťazca 2015 na každom riadku zo súboru.


Vypíšte vydania Windowsu vydané v roku 2015
-------------------------------------------

	Select-String 2015 windows.txt

Cmdlet `Select-String` filtruje objekty podľa toho, či sa s nimi zhoduje regulárny výraz alebo nie. V tejto podobe berie dva parametre:

*	regulárny výraz
*	názov súboru alebo predpis so zástupnými znakmi pre súbory, v ktorých sa má hľadať regulárny výraz

Výsledok bude:

	windows.txt:15:2015-07-29 Windows 10 [Threshold] #win #win-nt

`Select-String` vypisuje podrobné informácie o zhode obsahujúce názov súboru a číslo riadku, na ktorom sa zhoda našla. Hodí sa to pri vyhľadávaní vo viacerých súboroch.

Akýkoľvek zložitejší regulárny výraz sa silne odporúča dávať do úvodzoviek,
čím sa predíde nečakaným prekvapeniam.

<div markdown="1" class="alternative-solution">
`Select-String` je powershellovský protipól linuxového príkazu `grep`.
</div>

Vypíšte vydania Windowsu vydané v roku 2015 [bez názvov súborov a výskytov]
---------------------------------------------------------------------------

	Get-Content windows.txt | Select-String 2015

Tento variant, kde `Select-String` použijeme ako filter v rúre, sa na výstupe nebudú ukazovať informácie o názve súboru, či číslach riadkov.

Vypíšte vydania Windowsu vydané v roku 2013
-------------------------------------------
Nemôžeme hľadať jednoduchú zhodu cez `2012`, pretože by sme dostali aj *Windows Server 2012 R2* z roku 2013. 

Použime regulárny výraz ukotvený k začiatku riadku. Znak `^` predstavuje ukotvenie vyhľadávaného predpisu ku začiatku riadku.

	Get-Content windows.txt | Select-String "^2013"

Vypíšte serverovské edície
--------------------------

Použime ukotvenie ku koncu riadku

	Get-Content windows.txt | Select-String "#server$"

Alternatívne hľadajme len *Windows Server*:

	Get-Content windows.txt | Select-String "Windows Server"

Vypíšte systémy vydané v októbri [bodka ako zástupný znak]
----------------------------------------------------------

Hrubé riešenie použije bodku `.` ako zástupný symbol pre jeden znak. Použijeme štyri bodky pre štyri cifry v roku,
čo zodpovedá regulárnemu výrazu `....-10`.


	Get-Content windows.txt | 
	    Select-String "....-10" 

Vypíšte systémy vydané v októbri [opakovania]
---------------------------------------------
Opakovanie, kde je jasný presný počet, vyjadríme výrazom v zložených zátvorkách. Výraz `.{4}` znamená „ľubovoľný znak opakovaný štyrikrát“.

	Get-Content windows.txt | 
	    Select-String ".{4}-10"

Vypíšte systémy v rokoch 2000-2009 [sady znakov]
------------------------------------------------

V hranatých zátvorkách môžeme vyjadriť sadu povolených znakov.

	Get-Content windows.txt | 
	    Select-String "^200[0123456789]"

Toto riešenie je však ťažkopádne, keď vieme používať rozsahy povolených znakov.

Vypíšte systémy v rokoch 2000-2009 [rozsahy znakov]
---------------------------------------------------

V hranatých zátvorkách môžeme vyjadriť sadu povolených znakov. Výraz `[0-9]` reprezentuju znaky od nuly po deviatku.

	Get-Content windows.txt | 
	    Select-String "^200[0-9]"

Vypíšte systémy v rokoch 2000-2009 [rozsahy znakov]
---------------------------------------------------

Regulárne výrazy často definujú skratky pre typické rozsahy znakov.
Znak reprezentujúci cifru možno vyjadriť buď sadou `[0-9]` alebo
skratkou `\d` (*d*ecimal).

Výraz sa potom skráti na nasledovnú rúru:

	Get-Content windows.txt | 
	    Select-String "^200\d"

Vypíšte systémy, ktoré nemali číslo verzie v oficiálnom názve [negácia rozsahu znakov]
--------------------------------------------------------------------------------------

Ak chceme použiť sadu *zakázaných* znakov, v hranatých zátvorkách je na prvom mieste strieška `^`. 

V úlohe stačí vylúčiť čísla a bodku, čo dosiahneme výrazom:

	[^0-9.]

Výraz čítame ako „nie je povolená nula až deviatka a bodka“.

	Get-Content windows.txt | 
	    Select-String "Windows [^0-9.]"

V úlohe sú dve nečakané situácie:

*	strieška má v regulárnych výrazoch dva odlišné významy: znamená ukotvenie k začiatku riadku a zároveň reprezentuje negáciu pri sade znakov.
*	bodka v sade znakov neznamená zástupný symbol pre ľubovoľný znak, ale doslovný znak bodky.

### Alternatívne riešenie [`\d`, `\D`]

Regulárny výraz `[^0-9]` môžeme zapísať skrátením cez:

    [^\d]

To reprezentuje sadu znakov „nie cifry“. Tento zápis možno ešte skrátiť na:

    \D

Reprezentuje to „nečíselný znak“.

	Get-Content windows.txt | 
	    Select-String "Windows \D"

Vypíšte systémy, ktoré majú v názve číslo roku [rozsahy znakov s opakovaním]
----------------------------------------------------------------------------

Hľadáme systémy, ktoré majú v názve *Windows*, a chceme pokryť dvojciferné verzie (ako napr. 98), ale aj typické štvorciferné Windows 2000. 

	Get-Content windows.txt | 
	    Select-String "Windows [0-9]{2,4}"

### Alternatívne riešenie so sadou znakou [`\d`]

Ak použijeme skratku pre znak reprezentujúci číslo (`\d`), cmdlet sa skráti:

	Get-Content windows.txt | 
	    Select-String "Windows \d{2,4}"


Vypíšte systémy, ktoré majú v názve číslo roku, ale neignorujme serverovské edície [rozsahy znakov s opakovaním, zástupný znak hviezdička]
------------------------------------------------------------------------------------------------------------------------------------------
Hľadáme systémy, ktoré majú v názve *Windows*, a chceme pokryť dvojciferné
verzie (ako napr. 98), ale aj typické štvorciferné Windows 2000. Zároveň
potrebujeme pokryť situácie, kde medzi *Windows* a číslom verzie je
nepovinný *Server*. To vyjadríme cez opakovanie ľubovoľného znaku, čo
reprezentuje cez výraz `.*`.

	Get-Content windows.txt | 
	    Select-String "Windows.*[0-9]{2,4}"

Výraz znamená „hľadaj najprv *Windows*, za ním ľubovoľný znak opakovaný nula
či viakrát a za ním idú dve alebo štyri čísla“.

### Alternatívne riešenie

	Get-Content windows.txt | 
	    Select-String "Windows.*\d{2,4}"


Vypíšte systémy, ktoré majú v názve číslo roku, ale neignorujme serverovské edície [skupiny, voliteľný reťazec]
---------------------------------------------------------------------------------------------------------------

Hľadáme systémy, ktoré majú v názve *Windows*, nasledovaný nepovinným serverom a číslom roku (či už dvojciferným alebo štvorciferným). 

Nepovinný reťazec môžeme označiť ako **skupinu znakov** opakovanú nula alebo raz. Skupiny ohraničujeme bežnými zátvorkami.

Regulárny výraz bude vyzerať nasledovne:

    Windows (Server ){0,1}[0-9]{2,4}

Regulárny výraz čítame postupne zľava doprava:

*	najprv ide reťazec *Windows*
*	za ním ide skupina s reťazcom *Server* a medzerou opakovaná nula alebo raz, čo z nej robí nepovinný reťazec
*	za ním ide skupina dvoch až štyroch cifier

Pozor, skupina (*group*) nie je to isté ako sada znakov (*character set*)! Skupina je označená
v zátvorkách a znamená presnú postupnosť znakov v danom poradí. Sada znakov reprezentuje množinu znakov,
ku ktorej sa hľadá zhoda (*match*).

	Get-Content windows.txt | 
	    Select-String "Windows (Server ){0,1}[0-9]{2,4}"

### Alternatívne riešenie [skratka pre nepovinné skupiny, skratka pre čísla]

Skupinu s nula alebo jedným výskytom, teda nepovinný reťazec, môžeme zapísať
pomocou otáznika.

Nasledovné dva regulárne výrazy sú ekvivalentné:

    Windows (Server ){0,1}[0-9]{2,4}   

Zápis `{0,1}` je rovnaký ako `?`:

    Windows (Server )?[0-9]{2,4}

Ak využijeme ďalšie skrátenie pre cifry, vieme získať „optimalizovaný“
výraz:

    Windows (Server )?\d{2,4}

Vypíšte len roky, keď vyšiel Windows [premenná `$matches`]
----------------------------------------------------------
V tejto úlohe nebudeme len zisťovať, či riadok súboru zodpovedá regulárnemu
výrazu, ale budeme získavať časti riadka, ktoré vypíšeme.

Doteraz sme totiž vždy vypisovali celé riadky, ktoré zodpovedali podmienke,
ale v tomto prípade nás zaujíma len ich konkrétna časť.

Túto časť pokryjeme výrazom `^[0-9]{4}`, ktorým pokryjeme roky, teda štyri
cifry na začiatku riadku.

Cmdlet `Select-String` v skutočnosti posiela do rúry objekty typu
`Microsoft.PowerShell.Commands.MatchInfo` reprezentujúce výsledky
zhody reťazcov s regulárnym výrazom.

Tento objekt má vlastnosť `Matches` reprezentujúcu pole všetkých zhôd
(v našom prípade hľadáme len jednu zhodu). Toto pole zhôd môžeme
iterovať pomocou cmdletu `For-Each`, zistiť z neho vlastnosť `Value`
a tak získať celý výsledok:

	Get-Content windows.txt | 
		Select-String "^[0-9]{4}" |
            ForEach-Object { $_.Matches.Value }		    

Pozn.: hoci by sa zdalo, že výsledok získame cez `Select-Object`,
nesmieme zabúdať, že tento cmdlet nepodporuje prístup k vnoreným vlastnostiam.

### Alternatívne riešenie

K vnoreným hodnotám môžeme pristúpiť aj dvojkombináciou `For-Each`:

    Select-String "^\d{4}" | % Matches | % Value

### Alternatívne riešenie

Powershell dokáže poslať výsledok zhody regulárneho výrazu so vstupom do
špeciálnej premennej `$matches`, na ktorú sa môžeme odkázať v rúre. Premenná je
typu pole, ktoré obsahuje na nultej pozícii podreťazec vyhovujúci regulárnemu
výrazu.

Rok na každom riadku sa objaví na nultom prvku premennej `$matches`, a
pristúpime k nemu cez `$matches[0]`.

	Get-Content windows.txt | 
		Where-Object { $_ -match "^\d{4}" } | 
			ForEach-Object { $matches[0] }

Vypíšte len kódové označenia [escape a `$matches`]
--------------------------------------------------

V úlohe sú kódové označenia medzi hranatými zátvorkami. Prvý nápad pre výraz by
znamenal ľubovoľný znak medzi zátvorkami, teda `[.+]`. To však nie je správne!
Tento výraz totiž znamená sadu znakov obsahujúci bodku a plus, čo určite nemá
zhodu so žiadnym riadkom súboru.

V tomto prípade musíme znaky pre hranatú zátvorku *escape*núť, teda označiť ako
doslovný znak zátvorky a nie indikátory sady znakov:

	\[.*\]

Výsledná rúra bude:

    Get-Content windows.txt | 
        Select-String "\[.*\]" | 
            ForEach-Object { $_.Matches.Value }

Výsledok však bude obsahovať aj samotné zátvorky.

### Alternatívne riešenie

	Get-Content windows.txt | 
		Where-Object { $_ -match "\[.*\]" } | 
			ForEach-Object { $matches[0] }

Vypíšte len kódové označenia bez zátvoriek [skupiny a `$matches`]
-----------------------------------------------------------------

Pri výpise kódových označení nechceme vidieť zátvorky. Ak si vnútro medzi
zátvorkami označíme ako skupinu, získame možnosť získať jeho obsah.

Vytvorme výraz pre vnútro kódového označenia operačného systému:

	\[(.*)\]

Čítame postupne:

*	lomka a zátvorka znamenajú doslovný znak `[`
*	nasleduje otváracia zátvorka skupiny `(`
*	nasleduje ľubovoľný znak (`.`) opakovaný nula až viackrát (`*`)
*	nasleduje koncová zátvorka skupiny `)`
*	nasleduje lomka a zátvorka reprezentujúca doslovný znak `]`.

Výraz použijeme nasledovne:

    Get-Content windows.txt | 
        Select-String "\[.*\]" | 
            ForEach-Object { $_.Matches.Groups[1].Value }

Vlastnosť `Groups` reprezentuje pole skupín, ktoré sa naplnili pri
nájdení zhody. V úspešnom prípade bude mať toto pole dva prvky. Na nultej
pozícii sa nachádza celý reťazec, ktorý sa zhodol s regulárnym výrazom, a
na ďalších pozíciach sú zhody s jednotlivými skupinami v regulárnom
výraze.

    Get-Content windows.txt | 
        Select-String "\[(.*)\]" | 
            ForEach-Object { $matches[0] }

### Alternatívne riešenie

Ak používame `Where-Object` a operátor `-match`, prvá skupina sa objaví
v premennej `$matches[1]` na prvej pozícii. Nultá skupina, reprezentujúca
podreťazec, ktorý sa zhodol s regulárnym výrazom, je v premennej `$matches[0]`.

	Get-Content windows.txt | 
		Where-Object { $_ -match "\[(.*)\]" } | 
			ForEach-Object { $matches[1] }

Vypíšte názvy Windowsov a roky vydania (`$matches`)
---------------------------------------------------

	Get-Content windows.txt | 
		Where-Object { $_ -match "([0-9]{4}).*(Windows .*) \[" } | 
			ForEach-Object { $matches[2] + " (" + $matches[1] + ")"}

Vytvoríme regulárny výraz, v ktorom označíme skupiny pre roky a názvy vydaní.

	([0-9]{4}).*(Windows .*) \[

*	prvá skupina obsahuje štyri cifry z roka
*	za ňou nasledujú ľubovoľné znaky, kým sa nenarazí na `Windows`
*	`Windows` a text až po medzeru pred hranatou zátvorkou je názov vydania. Označíme ho ako druhú skupinu
*	posledný znak `\[` predstavuje explicitnú medzeru

Obe skupiny sa objavia na prvom a druhom prvku v poli `$matches`.

Vypíšte názov edície a všetky heštagy (pažravosť operátorov)
------------------------------------------------------------
Naivný nápad by vyzeral takto:

	(Windows .*) \[.*(#.*)$

*	hľadáme `Windows` nasledovaný ľubovoľným počtom znakov až po medzeru pred zátvorkou `[`. Ten si vhodne označíme ako skupinu s názvom edície
*	za zátvorkou `[` nasleduje ľubovoľný počet znakov až po prvú mriežku `#`
*	za mriežkou `#` nasleduje ľubovoľný počet znakov ľubovoľne veľa krát až do konca riadku (`$`) ktorý si poznačíme do druhej skupiny. Tá bude reprezentovať všetky heštagy

Toto však fungovať nebude, čo si všimnime na výsledku, kde prvú skupinu (edícia) oddelíme od heštagov v druhej skupine rúrou:

	Get-Content windows.txt | 
		Where-Object { $_ -match "(Windows .*) \[.*(#.*)$" } | 
			ForEach-Object { $matches[1] + "|" + $matches[2] }

Výsledok:

	Windows 95|#win
	Windows 98|#win
	Windows 2000|#win-nt
	Windows ME|#win
	Windows XP|#win-nt
	Windows Server 2003|#server
	Windows Vista|#win-nt
	Windows Server 2008|#server
	Windows 7|#win-nt
	Windows Server 2008 R2|#server
	Windows Server 2012|#server
	Windows 8|#win-nt
	Windows 8.1|#win-nt
	Windows Server 2012 R2|#server
	Windows 10|#win-nt
	Windows Server 2016|#server

Všimnime si, že v každom riadku sa zjaví len posledný heštag! Problém je v pažravosti hviezdičky (*greediness*). Regulárne výrazy totiž hľadajú najdlhšiu možno zhodu! Ak máme vstupný text:

	2003-04-24 Windows Server 2003 [Whistler Server] #win #win-nt #server

tak najdlhšia možná zhoda pre výraz `(Windows .*) \[.*(#.*)$` a jej pažravosť sa prejaví v časti:

	\[.*(#.*)$

Od hranatej zátvorky nasleduje ľubovoľný počet znakov po mriežku, čo sa rozbehne po reťazci, „zožerie“ prvú mriežku pri `#win`, zožerie druhú mriežku pri `#win-nt` a zhodu ukončí až pri poslednej mriežke v `#server`, kde do skupiny vloží všetko od mriežky po koniec riadka.

Správne riešenie minimalizuje počet všežravých hviezdičiek. Môžeme napríklad využiť hranaté zátvorky obsahujúce kódové označenie Windowsu ako pevný bod:

	"(Windows .*) \[.*\] (#.*)"

Iné riešenie používa trik, kde nehľadáme ľubovoľné znaky, ale len tie, ktoré *ne*obsahujú hraničný znak. Ak hľadáma výskyt prvej mriežky `#`, výraz bude využívať sadu `[^#]*`.

	"(Windows .*) \[[^#]* (#.*)"


Rady pre regulárne výrazy
=========================
Regulárne výrazy sa ľahko píšu, ale zložito čítajú. Niekoľko rád, ako uľahčiť ich tvorbu:

*	pri tvorbe sa treba zamerať na „pevné body“, teda znaky, ktoré sa určite zhodnú. Zvýši sa tým výkon hľadania zhody a zároveň sa ujasní budúcemu čitateľovi orientácia v zápise výrazu.

		(Windows .*) \[.*\] (#.*)$

*	namiesto všeobecnej bodky hľadajúcej jeden znak, je lepšie použiť sadu znakov. Namiesto `....` ako štyroch cifier je často lepšie uviesť `[0-9]{4}`
*	treba dať pozor na pažravosť hviezdičky, čo vedie k prekvapeniam. Výraz, ktorý má hľadať zhodu k prvej mriežke cez `.*#` nefunguje. Namiesto neho obvykle pomáha hľadanie „nemriežky“, čiže `[^#]*#`
