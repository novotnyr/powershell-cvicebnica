---
title: Cvičebnica z Powershellu -- súbory
layout: page
---
Súbory
======

Vytvorte v domovskom adresári súbor `ahoj.txt` s obsahom `AHOJ`
---------------------------------------------------------------

Vytvorte v domovskom adresári súbor `ahoj.txt` s obsahom `AHOJ` [`Set-Content`]
-------------------------------------------------------------------------------
Pošlime do rúry reťazec, ktorý zapíšeme do súboru pomocou cmdletu
`Set-Content`.

	"AHOJ" | Set-Content ahoj.txt

Cmdlet `Set-Content` má alias:

	"AHOJ" | sc ahoj.txt

### Alternatívne riešenie: `echo` a presmerovanie

	echo "Ahoj" > ahoj.txt

Toto je klasická finta známa ešte z čias MS-DOSu (a fungujúca aj na Linuxe).
Operátor `>` presmeruje výstup príkazu do súboru.

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

Spočítajte počet riadkov v súbore `ahoj.txt` [`Get-Content`, `Measure-Object`]
------------------------------------------------------------------------------

    Get-Content ahoj.txt | Measure-Object 

Keďže `Get-Content` posiela obsah súboru do rúry po riadkoch, počítanie
cez `Measure-Object` priamočiaro spočíta počet riadkov, teda počet
objektov v rúre.

Spočítajte počet slov v súbore
------------------------------

    Get-Content ahoj.txt | Measure-Object -Word

Cmdlet `Measure-Object` vie počítať aj slová v reťazcoch prichádzajúcich
z rúry.

Spočítajte počet slov vo všetkých textových súboroch v domovskom adresári
-------------------------------------------------------------------------
Najprv získame všetky textové súbory. Pre každý takýto súbor
vyvoláme cmdlet `Get-Content`, čím získame riadky príslušného súboru.
Všetky riadky sa postupne budú zbierať v meracom nástroji `Measure-Object`,
ktorý nám zráta slová.

    Get-ChildItem ~ *.txt | 
        Get-Content | 
            Measure-Object -Word | 
                Select-Object -ExpandProperty Words

