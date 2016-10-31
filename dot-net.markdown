---
title: Cvičebnica z Powershellu -- .NET Framework
layout: page
---

.NET Framework
==============

Vypočítajte druhú odmocninu z 25
--------------------------------

	[Math]::Sqrt(25)

Použime statickú metódu `Sqrt()` na objekte `System.Math`.

Zahrajte štandardný zvuk Pípania vo Windowse
-------------------------------------------
	
	[Media.SystemSounds]::Beep.Play()

Stačí zavolať statickú inštančnú premennú `Beep` na triede `System.Media.SystemSounds`. (Namespace `System` je automaticky importnutý.) 

Premenná `Beep` je typu `System.Media.SystemSound` (ľahko overíme pomocou cmdletu `Get-Member`):

	[Media.SystemSounds]::Asterisk | Get-Member

Na `SystemSound`-e môžeme zavolať metódu `Play()`

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

