---
title: Cvičebnica z Powershellu — zbytky
layout: page
---

Zbytky
======

Zistite názvy najčítanejších článkov za posledné 4 hodiny na portáli SME.sk.
----------------------------------------------------------------------------

    $web = New-Object System.Net.WebClient
	$rss = $web.DownloadString("http://rss.sme.sk/rss/rss.asp?id=smenajcit4")
	$xml = [xml] $rss;
	$xml.rss.channel.item.title

Cmdlet `New-Object` slúži na vytváranie nových objektov, čiže na
vytváraní nových inštancií tried z .NET Frameworku.

Najprv vytvoríme nový objekt pre HTTP klienta, teda inštanciu triedy
`System.Net.WebClient`. Následne stiahneme obsah RSS súboru ako reťazec,
ktorý pretypujeme na XML objekt reprezentovaný typom `xml`.

XML objekt reprezentuje magický strom, kde dokážeme bodkovou notáciou
vchádzať do uzlov a poduzlov. Atribúty z XML, resp. HTML sa v tomto
objekte tiež zjavia ako *properties*, čo môžeme tiež využiť pri bodkovej
notácii.


Zistite kurz českej koruny z http://www.ecb.europa.eu/stats/eurofxref/eurofxref-daily.xml
-----------------------------------------------------------------------------------------

	$currency = "CZK"
	$xml = [xml] (New-Object System.Net.WebClient).DownloadString("http://www.ecb.europa.eu/stats/eurofxref/eurofxref-daily.xml")
	$xml.Envelope.Cube.Cube.Cube | ? {$_.currency -eq $currency } | select rate

Riešenie je podobné ako pri úlohe s RSS. Všimnime si, že v tomto prípade
vôbec neriešime menné priestory XML.


Zistite frekvencie jednotlivých slov v súbore
---------------------------------------------

    Get-Content babel.txt | 
        ForEach-Object { $_.split() } | 
            ForEach-Object { $_.toLower().trim() -replace "\W", "" } | 
                Where-Object Length -gt 0 | 
                    Group-Object | 
                        Sort-Object -Property Count

*	Vyjdime z cmdletu `Get-Content`, ktorý pošle do rúry jednotlivé riadky súboru. 
*	Každý riadok rozdelíme na slová cez `Split`: vzniknú tým jednotlivé slová, ktoré preiterujeme v podrúre.
*	Každé slovo zhodíme na malé písmená, odsekneme začiatočné a koncové medzery a každé nepísmeno nahradíme prázdnym znakom.
*	Odfiltrujeme slová s nulovou dĺžkou, ktoré vznikli z pseudoslov ako pomlčky.
*	Všetky slová zgrupneme cez `Group-Object`, čím získame frekvencie.
*	Tie už len potriedime podľa počtu a sme hotoví.

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
