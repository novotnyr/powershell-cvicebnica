---
title: Cvičebnica z Powershellu -- úvod
layout: page
---

Inštalácia
==========
V tomto tutoriále sa budeme venovať Powershellu vo verzii 3.0. I keď
to zďaleka nie je najnovšia verzia (v septembri 2017 sa už vyvíja PowerShell
6.0), ide verziu, ktorá je zrejme najviac rozšírená.

Powershell 3.0 je automaticky k dispozícii na Windows 7 Service Pack 1,
Windows Server 2008 R2 SP1 a Windows Server 2008 Service Pack 2.

Úvodné úlohy
=============

Spustite PowerShell
-------------------

	Start | powershell.exe

Spustite Powershell 3.0
------------------------

Na moderných Windowsoch sa automaticky spúšťa najnovší Powershell. Na zachovanie 
spätnej kompatibility môžete explicitne spustiť konkrétnu verziu.

	powershell -version 3.0

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
