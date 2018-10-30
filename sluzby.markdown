---
title: Cvičebnica z Powershellu -- Služby
layout: page
---

Služby
======

Vypíšte zoznam všetkých služieb vo Windowse
-------------------------------------------

	Get-Service

<div class="note alias" markdown="1">
Aliasy `Get-Service`:

* Skratka: `gsv`

</div>

Vypíšte zoznam všetkých bežiacich služieb
-----------------------------------------

	Get-Service | Where-Object Status -eq "Running"

Služba je reprezentovaná objektami typu `System.ServiceProcess.ServiceController`,
ktoré majú vlastnosť `Status`. Bežiace služby majú hodnotu tejto vlastnosti
rovnú `Running`.

<div class="note minified" markdown="1">
	gsv | ? status -match run
</div>

Zastavte službu s názvom Themes
-------------------------------

	Stop-Service Themes

alebo dlhšie, ale v súlade s filozofiou `ps`/`kill`:

	Get-Service Themes | Stop-Service

Znovu spustite službu s názvom Themes
-------------------------------------

	Start-Service Themes

alebo dlhšie, ale v súlade s filozofiou `ps`/`kill`:

	Get-Service Themes | Start-Service

Zistite, aké atribúty má objekt pre službu
-------------------------------------------

	Get-Service | Get-Member

Vypíšte služby spolu so závislými službami
------------------------------------------

	Get-Service | Select Name, ServicesDependedOn

Vypíšte služby, ktoré nezávisia na žiadnej službe.
-------------------------------------------

	Get-Service | 
	    Where-Object ServicesDependedOn | 
	        Select Name

Atribút `ServicesDependedOn` je zoznamom. Hoci podmienka vo `Where-Object`
očakáva booleovský výraz, v prípade zoznamov platí, že podmienka je
splnená vtedy, ak je zoznam neprázdny.

Zistite, ktoré služby závisia na službe HTTP
-------------------------------------------

	Get-Service HTTP -DependentServices

Pre každú službu vypíšte služby, na ktorých táto služba závisí
-------------------------------------------

	Get-Service | 
	    Select-Object DisplayName, DependentServices | 
	        Sort-Object DisplayName

<div class="note minified" markdown="1">
    gsv | sort-object displayname | select displayname, dependentservices
</div>


