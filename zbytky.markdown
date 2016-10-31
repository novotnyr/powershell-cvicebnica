---
title: Cvičebnica z Powershellu — zbytky
layout: page
---

Zbytky
======

Zistite frekvencie jednotlivých slov v súbore
---------------------------------------------

	gc babel.txt | % { 
	    $_.split() | % { 
	        $_.toLower().Trim() -replace "[^a-z]", "" | ? {$_.Length -ge 0 }
	    }  
	} | Group-Object | Sort Count


*	Vyjdime z cmdletu `Get-Content`, ktorý pošle do rúry jednotlivé riadky súboru. 
*	Každý riadok rozdelíme na slová cez `Split`: vzniknú tým jednotlivé slová, ktoré preiterujeme v podrúre.
*	Každé slovo zhodíme na malé písmená, odsekneme začiatočné a koncové medzery a každé nepísmeno nahradíme prázdnym znakom. Zároveň do rúry pošleme len slová, ktorá po upratovaní majú stále nenulovú dĺžku.
*	Všetky slová zgrupneme cez `Group-Object`, čím získame frekvencie.
*	Tie už len potriedime podľa počtu a sme hotoví.


Alternatívne riešenie:

	cat capybara.txt | % { $_.split() } | % { $_.toLower().Trim() -replace "\W", "" } | ? Length -gt 0 | group | select name, count | sort-object count -desc

Vypíšte zoznam fontov v systéme
-------------------------------

	[System.Reflection.Assembly]::LoadWithPartialName("System.Drawing")
	$installedFonts = New-Object System.Drawing.Text.InstalledFontCollection
	$installedFonts.Families | % {$_.Name}

Spočítajte počty riadkov textových súborov v aktuálnom adresári
---------------------------------------------------------------


    gci -File | % { $_.Name + " " +  (get-content $_ | measure -line | select -exp lines  ) }

Alebo:

    gci -File | % { $_.Name + " " +  (get-content $_).count  }

Alebo:

    gci -r | where {! $_.psiscontainer -and $_.extension -match "txt" } | % { $_.Name + " " +  @($_ | get-content).count  }