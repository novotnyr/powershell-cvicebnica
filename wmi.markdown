---
title: Cvičebnica z Powershellu, WMI
layout: page
---

WMI (Windows Management Instrumentation)
========================================

Zistite veľkosť RAMky v počítači
--------------------------------

	Get-WmiObject Win32_ComputerSystem | Select-Object TotalPhysicalMemory

WMI objekt sa tvári ako štandardný powershellovský objekt: má inštančné premenné a metódy.
V tomto prípade vidíme výsledok vo vlastnosti `TotalPhysicalMemory`.

### Alternatívne riešenie: gigabajty

	(Get-WmiObject Win32_ComputerSystem).TotalPhysicalMemory / 1GB

Výsledok bude:

	7,88347625732422

### Alternatívne riešenie: skrátený zápis

    gwmi Win32_ComputerSystem | % TotalPhysicalMemory

Zistite, koľko modulov RAM je v stroji
--------------------------------------

	Get-WmiObject Win32_PhysicalMemory | 
	    Select-Object BankLabel, Capacity

Výsledok:

	BankLabel   Capacity
	---------   --------
	BANK 0    4294967296
	BANK 2    4294967296

Máme teda 2 moduly po 4GB.

Vypíšte zoznam všetkých tlačiarní pripojených k počítaču
--------------------------------------------------------

	Get-WmiObject Win32_Printer

Vypíšte názov implicitnej tlačiarne
-----------------------------------

	Get-WmiObject Win32_Printer | 
	    Where-Object Default | 
	        Select-Object -ExpandProperty Name

Zistite zoznam všetkých fyzických diskov pripojených k počítaču
---------------------------------------------------------------
	
	Get-WmiObject Win32_DiskDrive

`Win32_DiskDrive` je trieda zodpovedajúca fyzickému disku vo Windowse.

Zistite zoznam všetkých vlastností / metód, ktoré možno zistiť o fyzických diskoch.
-----------------------------------------------------------------------------------

	Get-WmiObject Win32_DiskDrive | Get-Member

Zistite zoznam fyzických diskov, ktoré podporujú technológiu SMART
------------------------------------------------------------------

	Get-WmiObject Win32_DiskDrive | 
	    Where-Object Capabilities -contains 10 | 
	        Select-Object Caption

Technológia SMART (Self-Monitoring, Analysis, and Reporting Technology)
umožňuje monitorovanie a varovanie pred zlyhaním disku. Podporuje ju
takmer každý dostupný pevný disk.

Trieda [`Win32_DiskDrive`](http://msdn.microsoft.com/en-us/library/aa394132%28v=VS.85%29.aspx#properties)
obsahuje vlastnosť `Capabilities` reprezentovanú poľom integerov. Ak pole
obsahuje hodnotu 10, zariadenie podporuje SMART.

Nájdite fyzický disk s najväčšou kapacitou.
-------------------------------------------

	Get-WmiObject Win32_DiskDrive | 
	    Sort-Object Size -Desc | 
	        Select-Object -First 1

Dôležitá vlastnosť je `Size`.

Vypíšte zoznam všetkých logických jednotiek dostupných v systéme
----------------------------------------------------------------

	Get-WmiObject Win32_LogicalDisk | Where-Object DriveType -eq 3

[Dokumentácia](http://msdn.microsoft.com/en-us/library/aa394173(VS.85).aspx) udáva 3
ako konštantu pre lokálnu jednotku.

Vypíšte zoznam všetkých MP3jek na všetkých lokálnych jednotkách
---------------------------------------------------------------

	Get-WmiObject Win32_LogicalDisk |  
	    Where-Object DriveType -eq 3 | 
	        ForEach-Object { Get-Child-Item $_.Caption -Recurse } | 
	            Where-Object { $_ .Extension -eq ".mp3" }  |
	                Select-Object Name

Zistite zoznam bežiacich procesov cez WMI
-----------------------------------------

	Get-WmiObject Win32_Process |
        Select-Object ProcessId, Name, ExecutablePath

Alternatívne cez dopyty v jazyku WQL ([WMI Query Language](http://msdn.microsoft.com/en-us/library/aa392902(v=vs.85).aspx)):

	Get-WmiObject -Query 'select ProcessId, Name, ExecutablePath from Win32_Process'

WQL je podobný jazyku SQL. Názov triedy zodpovedá tabuľke, inštačné premenné stĺpcom.

Zistite zoznam bežiacich procesov z `C:\Windows`
------------------------------------------------

	Get-WmiObject -Query 'select Name from Win32_Process where ExecutablePath like "%C:\\Windows%"'

Alternatívne cez pretypovanie reťazca na objekt typu `wmisearcher`:

	$q = [wmisearcher] 'select Name from Win32_Process where ExecutablePath like "%C:\\Windows%"'
	$q.get()

Zistite info o procese s ID 0
-----------------------------

	Get-WmiObject -Query "SELECT * FROM Win32_Process WHERE Handle = 0"

alebo 

	Get-WmiObject Win32_Process -filter "Handle=0"

alebo pretypovaním reťazca na objekt typu `[wmi]`:

	[wmi] "Win32_Process.Handle = 0"

Pozor, táto posledná finta funguje len pre niektoré vlastnosti. Z dokumentácie
pre konkrétnu triedu zistite, ktoré vlastnosti majú kvalifikátor
(*qualifier*) **Key**. Napríklad
[`Win32_Process`](http://msdn.microsoft.com/en-us/library/aa394372%28v=vs.85%29.aspx)
má len vlastnosti `CreationClassName` a `Handle`. Podrobnejšie vysvetlenie
je na [MSDN Blogu](http://blogs.msdn.com/b/powershell/archive/2008/04/15/wmi-object-identifiers-and-keys.aspx).

Reštartujte počítač
-------------------
	
	(Get-WmiObject Win32_OperatingSystem).Reboot()

`Get-WmiObject` pošle do rúry jeden objekt, na ktorom môžeme volať metódy z dokumentácie.

Reštartujte počítač [`Restart-Computer`]
----------------------------------------

	Restart-Computer

WMI ťahák
---------
Bežná kombinácia `Get-WmiObject` a výberu s filtráciou:

	Get-WmiObject Win32_Process |
            Select-Object ProcessId, Name, ExecutablePath |
                Where-Object Name -eq powershell.exe

Výber pomocou dopytu jazyka *WMI Query Language*:

    Get-WmiObject -Query "SELECT Name FROM Win32_Process WHERE Name = 'powershell.exe'"

Dopytovanie s filtráciou:

    Get-WmiObject -Filter "Name = 'powershell.exe'"

Dopytovanie s pretypovaním reťazca na `wmisearcher`:

    $wmi = [wmisearcher] "SELECT Name FROM Win32_Process WHERE Name = 'powershell.exe'"
    $wmi.get()    

Vyhľadávanie podľa primárneho kľúča (*key*):

   [wmi] "Win32_Process.Handle = 0"