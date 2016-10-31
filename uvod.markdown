---
title: Cvičebnica z Powershellu -- úvod
layout: page
---

Inštalácia
=========

**PowerShell 2.0** je automaticky dostupný vo Windows 7 a Windows 2008 R2. Pre
staršie verzie Windowsu navštívte
[http://support.microsoft.com/kb/968929](http://support.microsoft.com/kb/968929)
a stiahnite *Windows Management Framework Core (WinRM 2.0 and Windows PowerShell
2.0)*.

Od septembra 2012 je k dispozícii **PowerShell 3.0** pre Windows 7 Service Pack
1, Windows Server 2008 R2 SP1, Windows Server 2008 Service Pack 2.

V tomto tutoriále sa však stále budeme venovať verzii 2.0.

Úvodné úlohy
=============

Spustite PowerShell
-------------------

	Start | powershell.exe

Spustite Powershell 2.0
-----------------------

Na moderných Windowsoch sa automaticky spúšťa najnovší Powershell. Na zachovanie 
spätnej kompatibility môžete explicitne spustiť konkrétnu verziu.

	powershell -version 2.0

Overte následne spustenú verziu.

Vyskúšajte vlastnosti kalkulačky, zrátajte (2 + 3) * 10.
-------------

	(2 + 3) * 10

Zistite, akú verziu PowerShellu používate.
------------------------------------------

	Get-Host

Príkazy nerozlišujú veľké a malé písmena.

<div markdown="1" class="alternative-solution">
Podobnú informáciu obsahuje aj premenná `$PSVersionTable`.
</div>


Naučte sa pohybovať po prostredí Powershellu
--------------------------------------------
* Šípky hore a šípky dole sa pohybujú po histórii príkazov
* Tabulátor realizuje automatické dopĺňanie.
* Kliknutím myši a ťahaním môžete vybrať text a pravým klikom ho skopírovať do
  schránky.
* cmdlet `cls` / `Clear-Host` zmaže obrazovku.

Spustite Prieskumníka
---------------------

	explorer

alebo

	explorer.exe

Možno spúšťať bežné programy, ktoré sú dostupné v systéme.
