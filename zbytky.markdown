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


Vytvorte funkciu `Get-Hello`, ktorá vypíše zadaný počet krát "Hello World"
--------------------------------------------------------------------------

	function Get-Hello($count) {
		1..$count | % { "Hello World" }
	}
	
	Get-Hello 3

Vytvorte funkciu `Get-Hello`, ktorá vypíše zadaný počet krát "Hello World" [implicitné parametre]
-------------------------------------------------------------------------------------------------

Pri parametroch je často vhodné uviesť implicitnú hodnotu. Ak totiž zavoláme len

	Get-Hello

Uvidíme výpis:

	Hello World
	Hello World

Funkcia totiž bude iterovať od 1..0, čiže dvakrát. Upravme hlavičku
funkcie:

	function Get-Hello($count=1) {
		1..$count | % { "Hello World" }
	}

Vytvorte funkciu `Write-RepeatedMessage`, ktorá vypíše zadaný počet krát text
--------------------------------------------------------------------------

	function Write-RepeatedMessage($message, $count = 1) {
		1..$count | % { $message }
	}

Pozor na syntax volania!

Správny spôsob volania je bezzátvorkový:

	Write-RepeatedMessage "Hello World" 2

Zvyklosti z iných jazykov nefungujú:

	Write-RepeatedMessage("Hello World", 2)

Vypíše totiž:

	Hello World
	2

Toto nefunguje! Funkcie sa majú tváriť ako cmdlety, preto žiadne
zátvorkové volanie nefunguje. Toto nesprávne volanie zavolá funkciu s
jedným parametrom typu *dvojprvkové pole*. PowerShell ho vypíše raz a
keďže polia sa transformujú na jednotlivé prvky poslané do rúry, uvidíme
presne tento výpis.

Skúste si to napríklad s

	Write-RepeatedMessage("Hello World", 2, "Ding", "Dong")

Zlepšite správanie funkcie dodaním dátových typov
-------------------------------------------------

	function Write-RepeatedMessage([string] $message, [int] $count = 1) {
		1..$count | % { $message }
	}

Ak zavoláte

	Write-RepeatedMessage 2 2

uvidíte chybu.

Opäť pozor na bezzátvorkové správanie!

	Write-RepeatedMessage("Hello World", 2)

Výpis bude:

	Hello World 2    
