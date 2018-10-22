---
title: Cvičebnica z Powershellu -- viacriadkové skripty a ISE
layout: page
---
PowerShell ISE
==============

Zoznámte sa s PowerShell ISE
----------------------------

Spustite ISE pod právami administrátora
---------------------------------------

Overte správanie pri otváraní uložených skriptov
------------------------------------------------
Otvorenie existujúcich skriptov spôsobí problémy pri spúšťaní.

	File C:\Users\novotnyr\hello.ps1 cannot be loaded because the execution of scripts is disabled on this system. Please see "get-help about_signing" for more details.
	At line:0 char:0

Zmeniť možno politiku vykonávania skriptov

	Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser

Skrátený alias:

    Set-ExecutionPolicy RemoteSigned CurrentUser

Následne treba odsúhlasiť v GUI zmenu politiky. Táto politika umožní spúšťať všetky skripty, ale tie, ktoré sa stiahli z webu, musia byť digitálne podpísané.

Konštrukcie z procedurálneho programovania
==========================================

Oboznámte sa so syntaxou premenných. Vypočítajte cenu s DPH.
------------------------------------------------------------

	$cena = 1200
	$dph = 0.21
	($cena * $dph) + $dph

Premenné v Powershelli sú uvádzané dolármi, podobne ako v PHP, či Perli. 

Overte dátový typ premennej `$cena`
------------------------------------------------------------
	
	$cena | Get-Member

Dátové typy využívajú objektový model z .NET Frameworku.

Vypíšte login používateľa a jeho doménu v tvare používateľ@doména [interpolácia reťazcov]
-----------------------------------------------------------------------------------------

    "$Env:USERNAME@$Env:USERDOMAIN"

Využijeme premenné prostredia, ktoré majú v Powershelli predponu `Env:`.
Okrem toho využijeme *interpoláciu reťazcov*: ak sa v reťazci vyskytne
premenná, pri vyhodnotení sa meno premenné priamo nahradí jej obsahom. To
nám pomôže vyhnúť sa zbytočnému a neprehľadnému konkatenovaniu reťazcov cez
operátor `+`.

Otestujte sieťovú dostupnosť troch serverov [polia, `Test-Connection`]
----------------------------------------------------------------------

    $hosts = "maps.google.com", "drive.google.com", "gmail.com"
    Test-Connection -ComputerName hosts

Premenná `$hosts` reprezentuje pole (*array*). Pri deklarácii
oddeľujeme položky čiarkou.

Prázdne pole a jednoprvkové pole uvádzame do `@(...)`, aby sme ich
odlíšili od jediného prvku v rúre.

Prázdne pole:

    $hosts = @()

Jednoprvkové pole:

    $hosts = @("maps.google.com")

Z URL adresy zistite názov servera [`New-Object`]
-------------------------------------------------

    $uri = New-Object Uri "http://google.com"
    $uri.Host

Cmdlet `New-Object` vie vytvoriť ľubovoľnú inštanciu
objektu z frameworku `.NET`. V tomto prípade vytvoríme
nový objekt typu `System.Uri`, z ktorého následne získame jeho vlastnosť `Host`.

Parametre konštruktora uvádzame ako parametre cmdletu.

Zistite cestu k priečinku Plochy (Desktop) [objekty COM]
--------------------------------------------------------

    $WshShell = New-Object -ComObject WScript.Shell
    $WshShell.SpecialFolders.Item("Desktop")

Trieda `WScript.Shell` z frameworku COM reprezentuje používateľské prostredie.
Vlastnosť `SpecialFolder` obsahuje lokácie typických priečinkov, napr.
cestu k ploche.

Parameter `-ComObject` indikuje vytvorenie COM objektu,
pretože štandardne sa vytvárajú .NET objekty.

<div class="note" markdown="1">
Objektový model COM (Component Object Model) je vo Windowse prítomný
od dávnych čias, a definuje samostatnú sadu komponentov, ktorá dopĺňa
funkcionalitu frameworku .NET.
</div>

Vypočítajte druhú odmocninu z 25
--------------------------------

	[Math]::Sqrt(25)

Použime statickú metódu `Sqrt()` na objekte `System.Math`.

Otestujte sieťovú dostupnosť troch serverov z rovnakej domény [`foreach]
------------------------------------------------------------------------

    $hosts = "maps", "drive", "hangouts"
    foreach($host in $hosts) {
        Test-Connection -ComputerName "$host.google.com"
    }

Cyklus `foreach` prechádza kolekciou, a každý prvok vloží do premennej
`$host`. V príklade sme zároveň použili interpoláciu reťazcov na vybudovanie
plného mena stroja, ktorý sa má overiť.

### Alternatívne riešenie: cmdlet For-Each

    "maps", "drive", "hangouts" | 
        ForEach-Object { Test-Connection -ComputerName "$_.google.com" }

Cyklus možno často nahradiť priamym zaslaním objektov do rúry a ich
následným spracovaním pomocou cmdletu `ForEach-Object`.

Vypíšte 10x text `Budem si robiť domáce úlohy.` [cyklus `for`]
--------------------------------------------------------------

	for ($i = 0; $i -lt 10; $i++) {
	    "Budem si robiť domáce úlohy."
	}

Cyklus `for` využíva klasickú C/C++/Java/C# syntax. Pozor na

* deklaráciu premenných
* porovnávanie dvoch hodnôt: namiesto `<` použime `-lt`.

Samotný text nemusíme vypisovať pomocou žiadneho príkazu, pretože
reťazce sa automaticky pošlú do rúry.

Vypíšte 10x text `Budem si robiť domáce úlohy.` [rozsahy]
---------------------------------------------------------

    1..10 | ForEach-Object { "Budem si robiť domáce úlohy." }

Dátový typ rozsahu (*range*) umožňuje rýchlo vytvárať zoznamy
čísiel z daného intervalu. Rozsah `1..10` reprezentuje
pole čísiel od 1 do 10 (vrátane), resp. pošle do rúry
čísla od 1 po 10. To možno s výhodou použiť na vykonávanie
opakovaných akcií namiesto cyklu `for`.

Alternatívne riešenie: skrátený zápis
-------------------------------------

    1..10 | % { "Budem si robiť domáce úlohy." }


Vypíšte 10x text `Budem si robiť domáce úlohy.` [cyklus `while`]
----------------------------------------------------------------

	$i = 0
	while ($i -lt 10) {
	    "Budem si robiť domáce úlohy."
	    $i++
	}

Napíšte ľubovoľný nekonečný cyklus
----------------------------------------------------------------------

	while ($true) {
	    "Budem si robiť domáce úlohy."
	}

Špeciálna premenná `$true` obsahuje "pravdu", a jej opak `$false`
predstavuje booleovskú nepravdivú hodnotu.

<div class="note" markdown="1">
Výraz `true` v Powershelli nie je definovaný. Pokus o `while(true)`
povedie k chybe:

	The term 'true' is not recognized as the name of a cmdlet, 
	function, script file, or operable program. 
</div>

### Alternatíva: nenulové výrazy sú pravdivé

	while (1) {
	    "Budem si robiť domáce úlohy."
	}

Pri vyhodnocovaní booleovských podmienok je 0 považovaná za nepravdu
a hocijaká iná hodnota za pravdu `$true`.

Funkcie
=======

Vytvorte funkciu pre generovanie náhodného dvadsaťznakového hesla
-----------------------------------------------------------------

    function Get-RandomPassword {
        Get-Random -Min 97 -Max 122 -Count 2O | ForEach-Object { [char] $_ }
    }

Funkcie sa majú tváriť ako cmdlety a teda by mali dodržiavať konvenciu
 *sloveso*-*podstatné meno*.

Cmdlet `Get-Random` dokáže generovať náhodné číslo v zadanom intervale.
Ak využijeme vlastnosti ASCII tabuľky, vieme, že písmená od `a` po `z`
majú ASCII kód od 97 do 122. Zmenu náhodného čísla na znak urobíme
tvrdým pretypovaním čísla (*int*) na znak (*char*).

### Alternatívne riešenie: intervaly znakov [Powershell 6]

    function Get-RandomPassword {
        ('a'..'z' | Get-Random -Count 20) -join ""
    }

PowerShell 6 podporuje aj znakové intervaly. Necháme si vygenerovať znaky
od `a` po `z` (teda rúru z 26 znakov), z ktorých následne vyberiem 20 znakov.
Celú náhodnú rúru uvedieme do zátvorky,
aby sme ju mohli považovať za pole dvadsiatich znakov, a následne skonkatenovať
(spojiť) do jedného reťazca pomocou operátora `-join`.

Zavolajte funkciu generujúcu náhodné heslo
------------------------------------------

    Get-RandomPassword

Na rozdiel od iných programovacích jazykov sa za názov funkcie nepíšu
zátvorky. Nezabudnime, že funkcie sa majú tváriť ako cmdlety, v ktorých
sa zátvorky takisto nepíšu.

Vytvorte funkciu, ktorá vygeneruje náhodné heslo dĺžky *n* znakov
-----------------------------------------------------------------

    function Get-RandomPassword($length) {
        Get-Random -Min 97 -Max 122 -Count $length 
            | ForEach-Object { [char] $_ }
    }

Parametre funkcie uvedieme do zátvoriek, pričom ich uvádzame s dolárom
(sú to koniec-koncov premenné). Dátové typy nie sú povinné.

Pozor na syntax volania! Správny spôsob volania je bezzátvorkový:

    Get-RandomPassword 15

Obohaťme funkciu `Get-RandomPassword` o dátové typy
---------------------------------------------------

    function Get-RandomPassword([int] $length = 20) {
        Get-Random -Min 97 -Max 122 -Count $length 
            | ForEach-Object { [char] $_ }
    }

Parameter môže mať dátový typ "celé číslo" (`int`) a môže mať implicitnú
hodnotu, napr. desať:

    [int] $length = 20

Ak parameter vynecháme, vygeneruje sa dvadsaťznakové heslo.

Vytvorte funkciu `Get-HomeDirectory`, ktorá pre zadaného používateľa vráti jeho domovský adresár
------------------------------------------------------------------------------------------------

	function Get-HomeDirectory($username) {
	    $account = Get-WmiObject Win32_UserAccount -Filter "name = '$username'"
	    $sid = account.sid
	    $profile = Get-WmiObject Win32_UserProfile -Filter "sid = '$sid'"
	    $profile.LocalPath
	}

Volanie:

	Get-HomeDirectory "rn"

Výpis (na Windows 8.1):

	C:\Users\rn

Vo funkcii využijeme:

*   získavanie informácií o používateľovi cez WMI a cmdlet `Get-WmiObject`
*   interpoláciu reťazcov.

        "name = '$username'"

    Pre `$username = "rn"` po interpolácii vznikne reťazec `"name =
    'rn'"`.
*   funkcie nemusia používať `return`. Výsledkom funkcie je výsledok
    posledného vykonaného výrazu vo funkcii. V našej funkcii je to
    výsledok vlastnosti `LocalPath` na objekte profilu používateľa z premennej
    `$profile`

Vytvorte funkciu `Get-HomeDirectory`, ktorá vypíše domovský adresár používateľa z rúry
--------------------------------------------------------------------------------------

	function Get-HomeDirectory {
	    foreach($user in $input) {
            $account = Get-WmiObject Win32_UserAccount -Filter "name = '$user'"
            $sid = account.sid
            $profile = Get-WmiObject Win32_UserProfile -Filter "sid = '$sid'"
            $profile.LocalPath
	    }
	}

Volanie vyskúšajme na viacerých reťazcoch:

	"rn", "UpdatusUser" | Get-HomeDirectory

Každá funkcia má skrytú premennú `$input`, ktorá je kolekcia s prvkami prichádzajúcimi z rúry.

### Alternatívne riešenie: blok `process`

 	function Get-HomeDirectory {
 	    process {
             $account = Get-WmiObject Win32_UserAccount -Filter "name = '$_'"
             $sid = account.sid
             $profile = Get-WmiObject Win32_UserProfile -Filter "sid = '$sid'"
             $profile.LocalPath
 	    }
 	}

Funkcia môže obsahovať blok `process`, ktorý bude volaný opakovane
pre každý objekt z rúry. Vo vnútri tohto bloku je k dispozícii premenná
`$_`, resp. `$PSItem` s aktuálne spracovávaným objektom. (Blok `process`
možno chápať ako vnútro cyklu `for`).

Vytvorte funkciu, ktorá zistí celkové veľkosti adresárov (rekurzívne)
---------------------------------------------------------------------

    function Measure-Directory {
        process {
            $size = Get-ChildItem $_ -File -Recurse -Force -ErrorAction SilentlyContinue |
                        Measure-Object -Sum -Property Length |
                            Select-Object -ExpandProperty Sum
            @{$item.FullName = $size}
        }
    }

Vo výsledku potrebujeme pre každý adresár poslať do rúry dvojicu
názov-veľkosť. Využime na to *hashovaciu tabuľku* (hash table), ktorá je
v iných jazykoch známa aj ako *hash*, slovník (*dictionary*), či
*hashmap*. 

Volanie:

	Get-Item D:\MP3 | Measure-Directory

Alebo:

	"D:\MP3\Ectasy of Saint Theresa", "D:\MP3\Happy Melon" | Get-Item | Measure-Directory

Očíslujte položky z rúry
------------------------

	function Enumerate-Items {
		$itemIndex = 0;
		foreach($item in $input) {
			@{$itemIndex = $item}
			$itemIndex++
		}
	}

Volanie:

	dir | Enumerate-Items

### Alternatívne riešenie: bloky `begin`, `process` a `end`

	function Enumerate-Items {
		begin {
			$itemIndex = 0;
		}
		process {
			@{$itemIndex = $_}
			$itemIndex++
		}
	}

Funkcia môže pozostávať z troch blokov:

*	`begin`: vykoná sa pred spracovaním prvého objektu z rúry
*	`end`: vykoná sa po spracovaní posledného objektu z rúry
*	`process`: vykoná sa pre každý objekt z rúry samostatne.


Zistite, ktoré procesy sú systémovo náročné (> 60% CPU) [filtre]
----------------------------------------------------------------

	filter Get-CpuHeavyProcess {
	    if($_.PercentProcessorTime -ge 60) {
	        $obj = New-Object PSObject | Select Name, CPU
	        $obj.Name = $_.Name;
	        $obj.CPU = $_.PercentProcessorTime;
	        $obj
	    }
	}
	gwmi Win32_PerfFormattedData_PerfProc_Process | Get-CpuHeavyProcess

Filter je funkcia, ktorá obsahuje jedine blok `process`. V našom
prípade vytvoríme filter, ktorý sa pozerá na objekty prichádzajúce
z rúry, teda na premennú s názvom `$_` a filtruje ho podľa vyťazenia
vo vlastnosti `PercentProcessorTime`.

Zároveň demonštrujeme dynamické vytváranie objektu. `PSObject` je
univerzálny objekt, ktorý vieme vytvoriť a ktorému vieme priradiť nové
vlastnosti (properties). Následne vieme objekt obohacovať o hodnoty cez
bodkové notácie.

