---
title: Cvičebnica z Powershellu -- súbory
layout: page
---
Súbory
======

Vytvorte v domovskom adresári súbor `ahoj.txt` s obsahom `AHOJ` [`Set-Content`]
-------------------------------------------------------------------------------
Pošlime do rúry reťazec, ktorý zapíšeme do súboru pomocou cmdletu
`Set-Content`.

	"AHOJ" | Set-Content ahoj.txt

<div class="note alias" markdown="1">
Aliasy `Set-Content`:

* `sc`

</div>

### Alternatívne riešenie: `echo` a presmerovanie

	echo "Ahoj" > ahoj.txt

Toto je klasická finta známa ešte z čias MS-DOSu (a fungujúca aj na Linuxe).
Operátor `>` presmeruje výstup príkazu do súboru.

Vypíšte obsah súboru `ahoj.txt` [`Get-Content`]
-----------------------------------------------

	Get-Content ahoj.txt 

Cmdlet `Get-Content` načíta súbor a každý riadok pošle do rúry. Ak na
konci rúry nie je žiadny explicitný cmdlet, riadky sa vypíšu do konzoly.

<div class="note alias" markdown="1">
Aliasy `Get-Content`:

* skratka `gc`
* linuxový alias na Windowse `cat`

</div>

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

    Get-ChildItem ~ *.txt | 
        Get-Content | 
            Measure-Object -Word | 
                Select-Object -ExpandProperty Words

Najprv získame všetky textové súbory. Pre každý takýto súbor
vyvoláme cmdlet `Get-Content`, čím získame riadky príslušného súboru.
Všetky riadky sa postupne budú zbierať v meracom nástroji `Measure-Object`,
ktorý nám zráta slová.

<div class="note minified" markdown="1">
    (gci ~ *.txt | gci | measure -w).words
</div>
