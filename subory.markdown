---
title: Cvičebnica z Powershellu -- súbory
layout: page
---
Súbory
======

Vytvorte v domovskom adresári súbor `ahoj.txt` s obsahom `AHOJ`
---------------------------------------------------------------

	echo "Ahoj" > ahoj.txt

Toto je klasická finta známa ešte z čias MS-DOSu (a fungujúca aj na Linuxe).

Vytvorte v domovskom adresári súbor `ahoj.txt` s obsahom `AHOJ` [`Set-Content`]
-------------------------------------------------------------------------------
Pošlime do rúry reťazec, ktorý zapíšeme do súboru pomocou cmdletu
`Set-Content`.

	"AHOJ" | Set-Content ahoj.txt

Cmdlet `Set-Content` má alias:

	"AHOJ" | sc ahoj.txt

Vypíšte obsah súboru `ahoj.txt` [`Get-Content`]
-----------------------------------------------
	Get-Content ahoj.txt 

Cmdlet `Get-Content` načíta súbor a každý riadok pošle do rúry. Ak na
konci rúry nie je žiadny explicitný cmdlet, riadky sa vypíšu do konzoly.

Aliasy:

	gc ahoj.txt

Linuxový alias:

	cat ahoj.txt 

Príkaz zo starého shellu Windowsu, ktorý však nemá výhody cmdletov:

	more ahoj.txt 

Vytvorte v domovskom adresári zoznam desiatich súborov tyzden1.txt ... tyzden10.txt
-----------------------------------------------------------

	1..10 | ForEach-Object { Set-Content tyzden$_.txt -Value "" }

V textovom editore vytvorte súbor mena.txt s nasledovným obsahom:
-----------------------------------------------------------

	Jozef Košický,študent,Hlavná 25,Košice
	Milan Košický,študent,Leninova 66,Košice
	Zuzana Mikulášska,zamestnankyňa,Poľná 25, Liptovský Mikuláš
	Júlia Spišskohôrna,študentka,Alana Turinga 25, Spišská Hôrna
	Jeremiáš Prešovský,školník,Industriálna 15, Prešov

Vypíšte len riadky, ktoré obsahujú študentov [`like`]
-----------------------------------------------------
	
	Get-Content mena.txt | Where-Object {$_ -like "*študent*"}

Operátor `-like` funguje podobne ako v jazyku SQL. 

*   Hviezdička `*` predstavuje zástupný symbol (žolík) za viaceré znaky
*   Otáznik `?` je zástupný symbol pre jeden znak.

Rýchly výpís využíva alias: 

	gc mena.txt | ? {$_ -like "*študent*"}

Vypíšte len riadky, ktoré obsahujú študentov [`match`]
------------------------------------------------------

	Get-Content mena.txt | Where-Object {$_ -match "študent"}

Operátor `-match` umožňuje nájsť rýchlu zhodu reťazca s riadkami. Na
rozdiel od `-like` nie je nutné používať hviezdičky a otázniky, na
druhej strane môže dôjsť k nečakaným prekvapeniam. `-match` totiž
umožňuje zložité vyhľadávanie pomocou regulárnych výrazov, ktoré budú
demonštrované neskôr.

Súbor je reprezentovaný ako zoznam reťazcových riadkov, overte to:
-----------------------------------------------------------

	Get-Content mena.txt | Get-Member

Vypíšte len riadky, ktoré obsahujú študentov začínajúcich sa na „J“
-----------------------------------------------------------

	Get-Content mena.txt | Where-Object { $_ -like "J*" }

Vypíšte len riadky, ktoré sa začínajú sa na „J“ a reprezentujú len zamestnancov
-----------------------------------------------------------

	Get-Content mena.txt | 
	    Where-Object { ($_ -like "J*") -and ($_ -notlike "*zamestnanec*") }

Vypíšte jednotlivé slová z gramatickej vety zadanej na vstupe [`-split`]
------------------------------------------------------------------------
Operátor `-split` rozdelí reťazec na podreťazce na základe oddeľovača:

	"Byť, či nebyť, to je otázka" -split " "

Výsledkom je zoznam jednotlivých slov, pretože oddeľovačom je medzera.

Z textu vypíšte len prvé slovo (metódy `split()`)
-------------------------------------------------
Reťazce v Powershelli sú objekty, ktoré majú schopnosti reprezentované
*metódami*. Metóda `split()` rozdelí reťazec podľa oddeľovača do poľa 
reťazcov.

	"Byť, či nebyť, to je otázka".split()

K zoznamu môžeme pristupovať pomocou poľovej notácie.

    "Byť, či nebyť, to je otázka".split()[0]

Ak nepoužijeme žiaden oddeľovač, použijú sa znaky bielych miest
(whitespace, teda medzery, nové riadky atď).

Vypíšte len mená zo súboru `mena.txt`
-------------------------------------

	Get-Content mena.txt | ForEach-Object { $_.split(",")[0] }

Použili sme oddeľovač čiarky, teda `,`.

Vypíšte mená, ktoré sa končia na „A“
------------------------------------

	Get-Content mena.txt | 
	    ForEach-Object { $_.split(",")[0] } | 
	        Where-Object {$_ -like "*a"}

Vypíšte súbor `mena.txt`, ale nahraďte výskyt „študent“ výskytom „poslucháč“ [`-replace`]
-----------------------------------------------------------------------------------------
Operátor `-replace` nahrádza výskyt reťazca iným reťazcom

    "študentka" -replace "študent", "poslucháč"

Výsledkom je:

    poslucháčka

Celá rúra cmdletov:

	Get-Content mena.txt | 
	    Where-Object { $_ -replace "študent", "poslucháč" } | 

Nahraďte v súbore `mena.txt` výskyty „študent“ výskytom „poslucháč“ [`-replace`]
--------------------------------------------------------------------------------

Prvý chybný nástrel:

	Get-Content mena.txt | 
	    ForEach-Object { $_ -replace "študent", "poslucháč" } | 
	        Set-Content mena.txt

Dostaneme hlášku `The process cannot access the file because it is being 
used by another process`. 

`Get-Content` načítava postupne riadok za riadkom zo súboru, a posiela
ho do `Where-Object`, a následne do `Set-Content`. Toto spracovávanie sa
deje paralelne (kým sa zapisuje prvý riadok, už sa môže načítavať tretí
riadok). Žiaľ, cmdlety pracujú nad jedným súborom a "šliapu si po
prstoch", čo nie je dovolené.

### Správne riešenie
Cmdlet, ktorý je uvedený v zátvorkách, nebude posielať položky do rúry
postupne, ale najprv ich všetky spracuje a do rúry ich pošle naraz.

	( Get-Content mena.txt ) | 
	    ForEach-Object { $_ -replace "študent", "poslucháč" } | 
	        Set-Content mena.txt

Cmdlet `Get-Content` načíta všetky riadky zo súboru, potom ich pošle 
do rúry, kde sa následne vykoná nahrádzanie a zápis do súboru.
