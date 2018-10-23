---
title: Cvičebnica z Powershellu, CIM
layout: page
---

CIM (Common Information Model)
==============================

CIM (Common Information Model) je otvorený štandard pre objektovú
reprezentáciu manažovateľných objektov. Microsoft implementoval
tento štandard pod názvom WMI. Pre jednoduchosť možno považovať
CIM a WMI za ekvivalentné skratky.

Zistite veľkosť RAMky v počítači
--------------------------------

    (Get-CimInstance Win32_ComputerSystem).TotalPhysicalMemory

Objekt CIM sa tvári ako štandardný powershellovský objekt: má inštančné
premenné a metódy. V tomto prípade vidíme výsledok vo vlastnosti
`TotalPhysicalMemory`.

### Alternatívne riešenie: alias `gcim` a gigabajty

	(gcim Win32_ComputerSystem).TotalPhysicalMemory / 1GB

Výsledok bude:

	7,88347625732422

### Alternatívne riešenie: `Get-WmiObject`

    Get-WmiObject Win32_ComputerSystem

Cmdlet `Get-WmiObject` je zastaraný, i keď stále často používaný
cmdlet na získavanie informácií o CIM/WMI objektoch. Jeho modernejšia
verzia `Get-CimInstance` je prívetivejšia a a výkonnejšia.

Zistite, koľko modulov RAM je v stroji
--------------------------------------

	Get-CimInstance Win32_PhysicalMemory | 
	    Select-Object BankLabel, Capacity

Vypíšte názov implicitnej tlačiarne
-----------------------------------

	Get-CimInstance Win32_Printer | 
	    Where-Object Default | 
	        ForEach-Object Name

### Alternatívne riešenie: skrátený zápis

    gcim win32_printer | ? default | % name

Zistite zoznam všetkých pevných a vymeniteľných diskov
------------------------------------------------------

    (Get-CimInstance Win32_DiskDrive).Caption ; 
    (Get-CimInstance Win32_CDROMDrive).Caption 

`Win32_DiskDrive` je trieda zodpovedajúca fyzickému disku vo Windowse,
a `Win32_CDROMDrive) je trieda zodpovedajúca vymeniteľným DVD a CD mechanikám.

Oba príkazy oddelíme bodkočiarkou a získame len názov `Caption`.

Vypíšte zoznam všetkých logických jednotiek dostupných v systéme
----------------------------------------------------------------

    Get-CimInstance Win32_LogicalDisk | Where-Object DriveType -eq 3

[Dokumentácia](http://msdn.microsoft.com/en-us/library/aa394173(VS.85).aspx) udáva 3
ako konštantu pre lokálnu jednotku.

Vypíšte zoznam všetkých MP3jek na všetkých lokálnych jednotkách
---------------------------------------------------------------

    Get-CimInstance Win32_LogicalDisk |  
        Where-Object DriveType -eq 3 |
            Select-Object -ExpandProperty Caption |
                Get-ChildItem -Recurse -Filter *.mp3 |
                    Select -ExpandProperty FullName

Najprv vypíšeme všetky lokálne jednotky. Z objektov, ktoré reprezentujú,
vyberieme len vlastnosť `Caption` obsahujúcu písmeno jednotky. Tú
následne pošleme skrz rúru do cmdletu `Get-ChildItem`, ktorý ju
použije ako koreňový adresár na rekurzívne prehľadávanie súborov typu MP3.

### Alternatívne riešenie: skrátený zápis

    gcim win32_logicaldisk | ? drivetype -eq 3 |
        % caption | gci -fi *.mp3 -r | 
            % fullname

Zistite zoznam používateľských účtov
------------------------------------

	Get-CimInstance﻿Win32_UserAccount |
        Select-Object Name, SID

SID predstavuje *secure ID*, unikátny identifikátor používateľa.

Zistite zoznam bežiacich služieb
--------------------------------

    Get-CimInstance -Query `
        "SELECT DisplayName FROM Win32_BaseService WHERE Started = true" | 
            ForEach-Object DisplayName

V parametri `Query` môžeme uviesť dopyt v jazyku WQL, ktorý je veľmi podobný
databázovému dopytovaciemu jazyku SQL.
Názov triedy zodpovedá tabuľke, inštančné premenné stĺpcom.

Zistite stav služby DHCP
------------------------

    Get-CimInstance Win32_BaseService -Filter "DisplayName LIKE '%dhcp%'" |
        Select-Object -ExpandProperty State

Parameter `Filter` obsahuje `WHERE` podmienku pre filtrovanie objektov.

Zistite zoznam sieťových adaptérov, ktoré sú pripojené k sieti
--------------------------------------------------------------

    ([wmisearcher] "SELECT * FROM Win32_NetworkAdapter WHERE NetEnabled = true").Get()

Akcelerátor `[wmisearcher]` umožní pretypovať reťazec s dopytom WQL
na vyhľadávací objekt. Na tomto objekte môžeme zavolať metódu `Get()`
a získať výsledok.

Adaptér pripojený k sieti má vlastnosť `NetEnabled` nastavenú na `true`.

Zistite info o procese s ID 0
-----------------------------

    [wmi] "Win32_Process.Handle=0"

Akcelerátor `[wmi]` umožní pretypovať reťazec s podmienkou na vyhľadávanie
konkrétneho objektu CIM.

Pozor, táto posledná finta funguje len pre niektoré vlastnosti. Z dokumentácie
pre konkrétnu triedu zistite, ktoré vlastnosti majú kvalifikátor
(*qualifier*) **Key**. Napríklad
[`Win32_Process`](http://msdn.microsoft.com/en-us/library/aa394372%28v=vs.85%29.aspx)
má len vlastnosti `CreationClassName` a `Handle`. Podrobnejšie vysvetlenie
je na [MSDN Blogu](http://blogs.msdn.com/b/powershell/archive/2008/04/15/wmi-object-identifiers-and-keys.aspx).

Navyše, pozor na syntax: okolo znaku `=` nesmú byť medzery!

Reštartujte počítač
-------------------

	(Get-CimInstance Win32_OperatingSystem).Reboot()

`Get-CimInstance` pošle do rúry jeden objekt, na ktorom môžeme volať metódy z dokumentácie.

Reštartujte počítač [`Restart-Computer`]
----------------------------------------

	Restart-Computer

CIM ťahák
---------
Bežná kombinácia `Get-WmiObject` a výberu s filtráciou:

	Get-WmiObject Win32_Process |
            Select-Object ProcessId, Name, ExecutablePath |
                Where-Object Name -eq powershell.exe

Výber pomocou dopytu jazyka *WMI Query Language*:

    Get-WmiObject Win32_Process `
        -Query "SELECT Name FROM Win32_Process WHERE Name = 'powershell.exe'"

Dopytovanie s filtráciou:

    Get-WmiObject Win32_Process -Filter "Name = 'powershell.exe'"

Dopytovanie s pretypovaním reťazca na `wmisearcher`:

    $wmi = [wmisearcher] "SELECT Name FROM Win32_Process WHERE Name = 'powershell.exe'"
    $wmi.get()    

Vyhľadávanie podľa primárneho kľúča (*key*):

    [wmi] "Win32_Process.Handle=0"

