---
title: Cvičebnica z Powershellu -- procesy
layout: page
---
Procesy
========

Vypíšte zoznam všetkých bežiacich procesov [`Get-Process`]
----------------------------------------------------------

	Get-Process
	
Cmdlet má alias zodpovedajúci linuxovej konvencii:
	
    ps

Vypíšte počet bežiacich inštancií `svchost`u.
-------------------------

    Get-Process svchost | Measure-Object

Skrátený zápis:

	ps svchost | measure

Vypíšte zoznam atribútov, na ktoré sa môžete dopytovať pri procese
-------------------------

    Get-Process | Get-Member

Skrátený zápis:

	ps | gm

Vypíšte zoznam procesov a ku každému procesu vypíšte cestu k spúšťaciemu súboru.
-------------------------

    Get-Process | Select-Object Name, Path

Skrátený zápis s použitím `Format-Table`:

	ps | ft Name, Path –a

Zistite, koľkokrát beží proces spustený súborom `C:\Windows\system32\notepad.exe`
-------------------------

	Get-Process | 
	    Where-Object {$_.Path -eq "C:\Windows\system32\notepad.exe" } | 
	        Measure-Object

Vypíšte mená a cesty procesov, ktoré boli spustené z `C:\Program Files`
-----------------------------------------------------------------------

    Get-Process | 
        Where-Object { $_.Path -match "C:\\Program Files" } | 
            Format-Table Name, Path -AutoSize

Potrebné je zdvojenie lomiek (\\), pretože lomka má v regulárnych
výrazoch špeciálny význam. Regulárne výrazy preberieme neskôr.

Skrátený zápis:

	ps | ? {$_.Path -match "C:\\Program Files" } | ft Name, Path

Vypíšte procesy a ich vyťaženie CPU v zostupnom usporiadaní.
-------------------------

	Get-Process | 
	    Sort-Object -Descending CPU | 
	        Format-Table Name, CPU –Autosize

Skrátený zápis:

	ps | sort -desc CPU | ft Name, CPU –a

Vypíšte jedno meno procesu, ktoré najviac vyťažuje procesor.
-------------------------
    Get-Process |
        Sort-Object -Descending CPU |
            Select-Object Name, CPU -First 1
	
Skrátený zápis:
	
	ps | sort -desc cpu | select name -first 1 

Spustite novú inštanciu Skicára.
-------------------------
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
