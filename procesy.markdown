---
title: Cvičebnica z Powershellu -- procesy
layout: page
---
Procesy
========

Vypíšte zoznam všetkých bežiacich procesov [`Get-Process`]
----------------------------------------------------------

	Get-Process

<div class="note alias" markdown="1">
Aliasy:

* Powershell: `gps`
* Linuxovský alias na Windowse: `ps`
</div>

Vypíšte počet bežiacich inštancií `svchost`u. [`Get-Process`, `Measure-Object`]
-------------------------------------------------------------------------------

    Get-Process svchost | Measure-Object

<div class="note minified" markdown="1">
	gps svchost | measure
</div>

Vypíšte zoznam atribútov, na ktoré sa môžete dopytovať pri procese
-------------------------

    Get-Process | Get-Member

<div class="note minified" markdown="1">
	gps | gm
</div>

Vypíšte zoznam procesov a ku každému procesu vypíšte cestu k spúšťaciemu súboru
-------------------------------------------------------------------------------

    Get-Process | Select-Object Name, Path

Zistite, koľkokrát beží proces spustený súborom `C:\Windows\system32\notepad.exe`
-------------------------

	Get-Process | 
	    Where-Object Path -eq "C:\Windows\system32\notepad.exe" | 
	        Measure-Object

<div class="note minified" markdown="1">
	(gps | ? Path -eq "C:\Windows\system32\notepad.exe").Count
</div>


Vypíšte mená a cesty procesov, ktoré boli spustené z `C:\Program Files`
-----------------------------------------------------------------------

    Get-Process | 
        Where-Object Path -match "C:\\Program Files" | 
            Select-Object Name, Path

Potrebné je zdvojenie lomiek (`\\`), pretože lomka má v regulárnych
výrazoch špeciálny význam.

<div class="note minified" markdown="1">
	(gps | ? Path -match "C:\\Program Files") | Select Name, Path
</div>

Vypíšte procesy a ich vyťaženie CPU v zostupnom usporiadaní
-----------------------------------------------------------

	Get-Process | 
	    Sort-Object -Descending CPU | 
	        Select Name, CPU

Vypíšte jedno meno procesu, ktoré najviac vyťažuje procesor
-----------------------------------------------------------

    Get-Process |
        Sort-Object -Descending CPU |
            Select-Object -First 1 Name, CPU 

<div class="note minified windows" markdown="1">
	ps | sort -desc cpu | select name,cpu -first 1
</div>

Spustite novú inštanciu Skicára
-------------------------------
Klasická možnosť:

	mspaint

Alternatívne:

	start mspaint

Cmdlet `start` je alias pre `Start-Process`

	Start-Process mspaint

Ukončite všetky bežiace inštancie Skicára
------------------------------------------

    Get-Process mspaint | Stop-Process

Na rozdiel od linuxového variantu nemusíme naháňať PIDy (ID procesu),
ale môžeme do rúry poslať inštancie objektov, ktoré následne `Stop-Process`
zabije.

Skrátený zápis:

	ps mspaint | kill


Ukončite ešte raz všetky bežiace inštancie Skicára a pozorujte chybovú hlášku.
----------------------------------------

	Get-Process mspaint | Stop-Process
