---
title: Cvičebnica z Powershellu -- Objekty
layout: page
---

Cmdlet posiela do rúry objekty
-----

Cmdlet obvykle posiela do rúry jeden, či viacero objektov

	Get-ChildItem

Ako zistiť typ objektu posielaných cmdletom?
------------------------------------------

Výsledok stačí poslať do cmdletu `Get-Member`

	Get-ChildItem | Get-Member

Ako vybrať z objektov v rúre len niektoré?
------------------------------------------

Filtrujte objekty cez `Where-Object`.

	Get-ChildItem | Where-Object { $_.Length -gt 20 MB }

Ako vybrať z objektov v rúre len niektoré vlastnosti [PowerShell 3.0]
---------------------------------------------------------------------

V PowerShelli 3.0 a novšom je skrátený zápis pre prípady, že v podmienke overujete len jednu vlastnosť. Zložené zátvorky i špeciálnu premennú `$_` vynecháme:

	Get-ChildItem | Where-Object Length -gt 20MB 

Takto môžeme zápisy skracovať na minimum:

	gci | ? Length -gt 20MB

Ako vybrať z objektu len niektoré vlastnosti?
-----

Stačí použiť `Select-Object`.

	Get-Process | Select-Object ID, ProcessName

Cmdlet vyrobí pre každý objekt na vstupe nový objekt, ktorý obsahuje len uvedené vlastnosti. Powershell vyrobí špeciálnu triedu v mennom priestore `Selected`.

V príklade sa do rúry pošlú objekty typu `Selected.System.Diagnostics.Process`, hoci pôvodné objekty boli z priestoru `System.Diagnostics.Process`.

Ako vybrať z objektu výhradne jednu vlastnosť?
----------------------------------------------

Použime `Select-Object` a parameter `-ExpandProperty`:

	Get-ChildItem | Select-Object -ExpandProperty FullName

V príklade posielame do rúry objekty pre súbory či adresáre a cmdlet `Select-Object` vytiahne z každého objektu vlastnosť `FullName` a zapíše ju do rúry. Keďže táto vlastnosť je reťazcového typu, do rúry pošle len reťazec.

### Bodkové notácie

Ak cmdlety vracajú do rúry len jeden objekt, môžeme vlastnosť vytiahnuť pomocou bodkovej notácie.

	(Get-Item C:/Windows/win.ini).Length

Ako vybrať z objektu výhradne jednu vlastnosť? [Powershell 3.0] 
---------------------------------------------------------------
Powershell 3.0 podporuje bodkovú notáciu aj pre skupinu objektov.

    (Get-ChildItem *.mp3).FullName

Cmdlet `Get-ChildItem` s filtrom pre vyhľadávanie MP3 súborov pošle do
rúry jeden alebo viacero súborov. Vďaka použitiu zátvoriek a bodkovej
notácii vieme vrátiť len vlastnosť `FullName` pre každý z objektov.

Powershell 2.0 sa správa menej pohodlne: ak je výsledkom cmdletu viacero
objektov, použitie zátvoriek a bodkovej notácie nebude fungovať.

Ako spracovať objekty v rúre?
-----------------------------
Ak chceme komplexným spôsobom previesť každý objekt v rúre na nejaký iný
objekt, alebo ho prispôsobiť, použime cmdlet `ForEach-Object`.

Situácie:

*   prevod objektu z rúry na iný objekt (napr. súbor prevedieme na reťazec)
*   výber viacerých vlastností objektu z rúry a ich spracovanie

Vypíšme názvy súboru a každý riadok uveďme hviezdičkou:

    Get-ChildItem | ForEach-Object { "* " + $_.Name }
    
Medzi zložené zátvorky `{` a `}` sa uvádza skript, ktorý sa vykoná pre
každý objekt, ktorý príde z rúry do cmdletu `ForEach-Object`. Tento
objekt k dispozícii v skripte v špeciálnej premennej `$_`.
 
V Powershelli 3.0 je objekt k dispozícii aj ako premenná `$PSItem`.
    
Skrátený alias:

    Get-ChildItem | % { "* " + $_.Name }

###  `ForEach-Object` ako `Select-Object`
Cmdlet `ForEach-Object` sa vie správať ako `Select-Object`:

    Get-ChildItem | Select-Object -ExpandProperty Name
    Get-ChildItem | ForEach-Object { $_.Name }

V Powershelli 3.0 je aj skrátená možnosť:

    Get-ChildItem | ForEach-Object Name
    
Ako zotriediť objekty?
----------------------
Použime cmdlet `Sort-Object`:

    Get-ChildItem | Sort-Object -Property LastWriteTime -Descending

Ako zoskupiť objekty podľa vlastnosti?
--------------------------------------

    Get-ChildItem | Group-Object -Property Extension

Výsledkom je objekt s vlastnosťami `Count` (počet objektov v skupine),
`Name` (hodnota vlastnosti, podľa ktorej sa zoskupuje), či `Group`, kde
sú konkrétne zoskupené objekty.

    Count Name                      Group
    ----- ----                      -----
       12                           {Contacts, Desktop, Documents, Downloads...}
