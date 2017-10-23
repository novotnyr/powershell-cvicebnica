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

	File C:\Users\novotnyr\hello.ps1 cannot be loaded because the execution of scripts is  disabled on this system. Please see "get-help about_signing" for more details.
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

Vypíšte 10x text `Budem si robiť domáce úlohy.`
------------------------------------------------------------

	for ($i = 0; $i -lt 10; $i++) {
	    echo "Budem si robiť domáce úlohy."
	}

Cyklus `for` využíva klasickú C/C++/Java/C# syntax. Pozor na

* deklaráciu premenných
* porovnávanie dvoch hodnôt: namiesto `<` použite `-lt`.

Alternatívne: príkaz `echo` nie je povinný, keďže samotný reťazec 
predstavuje výraz, ktorý sa pošle do rúry a teda do výstupu.

	for ($i = 0; $i -lt 10; $i++) {
	    "Budem si robiť domáce úlohy."
	}

Vypíšte 10x text `Budem si robiť domáce úlohy.` [cyklus `while`]
----------------------------------------------------------------

	$i = 0
	while ($i -lt 10) {
	    "Budem si robiť domáce úlohy."
	    $i++
	}

Napíšte ľubovoľný nekonečný cyklus
----------------------------------------------------------------------
Zlé riešenie:

	while (true) {
	    "Budem si robiť domáce úlohy."
	}

Hláška je:

	The term 'true' is not recognized as the name of a cmdlet, 
	function, script file, or operable program. Check the spelling of
	 the name, or if a path was included, verify that the path is
	  correct and try again.

Výraz `true` v Powershelli nie je definovaný. 

Korektné riešenie využíva premennu `$true`, ktorá obsahuje "pravdu".

	while ($true) {
	    "Budem si robiť domáce úlohy."
	}

Prirodzene, existuje aj opačná premenná `$false` reprezentujúca
logické "nie".

### Alternatíva: nenulové výrazy sú pravdivé

	while (1) {
	    "Budem si robiť domáce úlohy."
	}

Funkcie
=======

Vytvorte funkciu `Get-Hello`, ktorá vypíše 10x "Hello World"
----------------------------------------------------------

	function Get-Hello {
		1..10 | % { "Hello World" }
	}

Funkciu zavoláme cez

	Get-Hello

Funkcie sa majú tváriť ako cmdlety a teda by mali dodržiavať konvenciu
 *sloveso*-*podstatné meno*.

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

Vytvorte funkciu, ktorá vygeneruje náhodný znak
-----------------------------------------------

    function Get-RandomChar {
        Get-Random -Min 97 -Max 122 | ForEach-Object { [char] $PSItem }
    }

Cmdlet `Get-Random` dokáže generovať náhodné číslo v zadanom intervale.
Ak využijeme vlastnosti ASCII tabuľky, vieme, že písmená od `a` po `z`
majú ASCII kód od 97 do 122. Zmenu náhodného čísla na znak urobíme
tvrdým pretypovaním čísla (*int*) na znak (*char*).

Vytvorte funkciu, ktorá vygeneruje náhodné heslo dĺžky 10 znakov
----------------------------------------------------------------

    function Get-RandomString {
        1..10 |
            ForEach-Object { Get-RandomChar } |
                Write-Host -NoNewLine
    }

Vo funkcii desaťkrát zavoláme pomocnú funkciu `Get-RandomChar`. Výsledkom
bude desať náhodných znakov, ktoré zlepíme (skonkatenujeme) do jedného
reťazca pomocou cmdletu `Write-Host`, v ktorom pomocou parametra
`-NoNewLine` zabránime vypisovaniu znaku konca riadka medzi jednotlivými
znakmi padajúcimi z rúry.

### Alternatívne riešenie: použitie `-join`

    function Get-RandomString {
        -join ( 1..10 | ForEach-Object { Get-RandomChar } )
    }

Operátor `-join` umožňuje spájať reťazce z poľa, či rúry. Jeho parameter
uzavrieme do zátvoriek, čo znamená, že všetky náhodné znaky z rúry
spojí do jedného reťazca

### Alternatívne riešenie: výber viacerých náhodných prvkov [`Get-Random`]

    function Get-RandomString {
        -join ( 97..122 | Get-Random -Count 10 | % { [char] $_ } )
    }

Najprv vygenerujeme zoznam čísiel reprezentujúcich znaky medzi `a` a `z`, teda
s ASCII kódmi medzi 97..122. Cmdlet `Get-Random` má možnosť zopakovať
náhodný výber zo sady objektov na vstupe. Použitím parametra `-Count`
môžeme vybrať zo vstupu náhodných 10 čísiel, ktoré potom prevedieme
na znaky. V úplnom závere všetky znaky zlepíme do reťazca pomocou operátora
`-join`.



Vytvorte funkciu `Get-HomeDirectory`, ktorá pre zadaného používateľa vráti jeho domovský adresár
------------------------------------------------------------------------------------------------

	function Get-HomeDirectory($username) {
	    $sid = (Get-WmiObject Win32_UserAccount -Filter "name = '$username'").sid
	    (Get-WmiObject Win32_UserProfile -filter "sid = '$sid'").LocalPath
	}

Volanie:

	Get-HomeDirectory "rn"

Výpis [na Windows 8.1]:

	C:\Users\rn

Vo funkcii využijeme:

*   získavanie informácií o používateľovi cez WMI a cmdlet `Get-WmiObject`
*   interpoláciu reťazcov. Ak máme premennú `$username`, a chceme ju
    použiť vo vnútri reťazca, nemusíme ich konkatenovať cez `+`. Dolárové
    premenné vo vnútri reťazcov sa automaticky nahradia ich hodnotami:

        "name = '$username'"

    Pre `$username = "rn"` po interpolácii vznikne reťazec `"name =
    'rn'"`.
*   funkcie nemusia používať `return`. Výsledkom funkcie je výsledok
    posledného vykonaného výrazu vo funkcii. V našej funkcii je to
    výsledok vlastnosti `LocalPath` na objekte profilu používateľa.

Vytvorte funkciu `Get-HomeDirectory`, ktorá vypíše domovský adresár používateľa z rúry
--------------------------------------------------------------------------------------

	function Get-HomeDirectory {
	    foreach($username in $input) {
	        $sid = (Get-WmiObject Win32_UserAccount -filter "name = '$username'").sid
	        (Get-WmiObject Win32_UserProfile -filter "sid = '$sid'").LocalPath
	    }
	}

Volanie vyskúšajme na viacerých reťazcoch:

	"rn", "UpdatusUser" | Get-HomeDirectory

Každá funkcia má skrytú premennú `$input`, ktorá je kolekcia s prvkami prichádzajúcimi z rúry.
 
Vytvorte funkciu, ktorá zistí celkové veľkosti adresárov (rekurzívne)
---------------------------------------------------------------------

	function Measure-Directory {
	    foreach($item in $input) {
	        $size = (Get-Item $item.FullName -Recurse | Measure-Object -Sum -Property Length).Sum
	        @{$item.FullName = $size}
	    }
	}

Vo výsledku potrebujeme pre každý adresár poslať do rúry dvojicu
názov-veľkosť. Využime na to *hashovaciu tabuľku* (hash table), ktorá je
v iných jazykoch známa aj ako *hash*, slovník (*dictionary*), či
*hashmap*. 

Volanie:

	ls D:\MP3 | Measure-Directory

Alebo:

	"D:\MP3\Ectasy of Saint Theresa", "D:\MP3\Happy Melon"  | Get-Item | Measure-Directory

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

Očíslujte položky z rúry [alternatívna syntax]
----------------------------------------------

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
*	`process`: vykoná sa pre každý objekt z rúry samostatne. V premennej `$_` sa zjaví aktuálny objekt, ktorý prichádza z rúry.

Vytvorte filter, ktorým spočítate veľkosť adresárov z rúry
-----------------------------------------------------------

	filter Measure-Directory {
        $size = (Get-Item $_.FullName -Recurse | Measure-Object -Sum -Property Length).Sum
        @{$_.FullName = $size}
	}

Funkcia, ktorá má len blok `process`, sa nazýva *filter*. Riešenie je ekvivalentné 

	function Measure-Directory {
	    foreach($item in $input) {
	        $size = (dir $item.FullName -Recurse | Measure-Object -Sum -Property Length).Sum
	        @{$item.FullName = $size}
	    }
	}

a ekvivalentné:

	function Measure-Directory {
		process {
        	$size = (dir $_.FullName -Recurse | Measure-Object -Sum -Property Length).Sum
        	@{$_.FullName = $size}
		}
	}

Zistite, ktoré procesy sú systémovo náročné (> 60% CPU)
-------------------------------------------------------

	filter Get-CpuHeavyProcess {
	    if($_.PercentProcessorTime -ge 60) {
	        $obj = New-Object PSObject | Select Name, CPU
	        $obj.Name = $_.Name;
	        $obj.CPU = $_.PercentProcessorTime;
	        $obj
	    }
	}
	gwmi Win32_PerfFormattedData_PerfProc_Process | Get-CpuHeavyProcess

Vyrobíme jednoduchý filter, ktorý sa díva na vlastnosť `PercentProcessorTime`.

Zároveň demonštrujeme dynamické vytváranie objektu. `PSObject` je
univerzálny objekt, ktorý vieme vytvoriť a ktorému vieme priradiť nové
vlastnosti (properties). Následne vieme objekt obohacovať o hodnoty cez
bodkové notácie.

