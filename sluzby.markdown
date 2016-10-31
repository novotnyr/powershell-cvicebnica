---
title: Cvičebnica z Powershellu -- Služby
layout: page
---

Služby
======

Vypíšte zoznam všetkých služieb vo Windowse.
--------------------------------------------

	Get-Service

Alias:

	gsv

Vypíšte zoznam všetkých bežiacich služieb.
-------------------------------------------

	Get-Service | Where-Object { $_.Status -eq "Running" }

Zastavte službu s názvom Themes.
--------------------------------

	Stop-Service Themes

alebo dlhšie, ale v súlade s filozofiou `ps`/`kill`:

	Get-Service Themes | Stop-Service

Znovu spustite službu s názvom Themes.
-------------------------------------------

	Start-Service Themes

alebo dlhšie, ale v súlade s filozofiou `ps`/`kill`:

	Get-Service Themes | Start-Service

Zistite, aké atribúty má objekt pre službu
-------------------------------------------

	Get-Service | Get-Member

alebo

	gsv | gm

Vypíšte služby spolu so závislými službami.
-------------------------------------------

	Get-Service | ft Name, ServicesDependedOn –a

Vypíšte služby, ktoré nezávisia na žiadnej službe.
-------------------------------------------

	Get-Service | 
	    Where-Object { $_.ServicesDependedOn } | 
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

Skrátene:

    gsv | sort displayname | ft displayname, dependentservices -a        

