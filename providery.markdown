---
title: Cvičebnica z Powershellu -- Providers
layout: page
---

Providery
=========

Zistite všetkých providerov v systéme
-------------------------------------

	Get-PSDrive

Alebo:

	gdr

Vypíšte zoznam všetkých diskových jednotiek
--------------------------------------------

	Get-PSDrive
	    | Where-Object Provider -match "FileSystem" 
	        | Select-Object Root

alebo

	gdr | ? provider -match filesystem | % root

Problém v tomto prípade spočíva v tom, že sú zahrnuté aj vymeniteľné jednotky
(DVD mechaniky), v ktorých sa nemusí nachádzať disk a teda prípadný výpis ich
obsahu zlyhá.

<div class="note" markdown="1">
V cmdlete sme na filtrovanie použili `-match`. V tomto prípade
by nefungovala presná zhoda reťazca `FileSystem` s hodnotou vlastnosti `Provider`, pretože
skutočná hodnota tejto vlastnosti sa odlišuje od zobrazovanej hodnoty.

Powershell zobrazuje len zjednodušenú verziu vlastnosti `Provider`, ktorej
skutočná hodnota je napr. `Microsoft.PowerShell.Core\FileSystem`.

Tento fakt sme zistili pomocou príkazu:

    Get-PSDrive | Select Provider

</div>

Zistite adresár, v ktorom sa nachádzajú položky Plochy.
-------------------------------------------------------

Informáciu možno získať z registrov z položky
`HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders`,
v kľúči `Desktop`.

V PowerShelli pristúpime k hodnote cez providera pre registre. Registru
`HKEY_CURRENT_USER` prislúcha jednotka (*drive*) `HKCU:`, ktorá
obsahuje nastavenia špecifické pre konkrétneho používateľa.

Ak chceme získať všetky páry hodnôt v kľúči `UserShellFolders` a z nich
vytiahnuť konkrétnu hodnotu podľa mena, môžeme použiť:

    Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders" | 
        Select-Object "Common Desktop"

Pomocou nástroja `regedit` overte, že sa naozaj v registroch nachádza.
Položka je typu expandovateľný reťazec a premenná `%USERPROFILE%` sa
automaticky expanduje na správnu hodnotu podľa aktuálne prihláseného
používateľa.

Cmdlet `Get-ItemProperty` umožňuje získavať hodnoty jednotlivých vlastností
pre jednotlivé objekty. Na rozdiel od všeobecného `Select-Object` je
tento cmdlet úzko zviazaný s konkrétnym providerom a jeho implementáciou.
Dôležité je, že nie každý provider poskytuje možnosť získavať informácie
cez tento cmdlet, ale typické providery ako `FileSystem`, či `Registry`
ho podporujú.

Výsledkom tohto cmdletu je objekt typu `System.Management.Automation.PSCustomObject`,
kde sa každá hodnota v príslušnom kľúči objaví ako vlastnosť (property) objektu.

### Alternatívne riešenie: skrátená verzia

    gp "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders" | 
        % "Common Desktop"

### Alternatívne riešenie: objektovo-orientovaný prístup.

Položky registrov sú typu `Microsoft.Win32.Registry`. Pomocou metódy
`GetValue()` vieme získať príslušnú hodnotu podľa mena:

	(Get-Item 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders').GetValue("Desktop")

Vypíšte mená všetkých nainštalovaných fontov
---------------------------------------------
Prvotný nástrel využije providera pre registre. "Jednotka" `HKLM:`
zodpovedá registru `HKEY_LOCAL_MACHINE`, nastaveniam súvisiacim s celým
systémom.

	Get-Item "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Fonts"

Výsledok:

    Hive: HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion
        
    SKC  VC Name                           Property
    ---  -- ----                           --------
      0 244 Fonts                          {Arial (TrueType), Arial Italic (TrueType), Arial Bold (TrueType), Arial Bold...

Všimnite si, že vo vlastnosti s názvom `Property` sa nachádza kolekcia
názvov fontov, presnejšie pole reťazcov (overiť to môžeme zaslaním
výsledku do `Get-Member`).

Ak chceme poslať názvy fontov do rúry, stačí rozbaliť pole: 

    Get-Item "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Fonts" | Select -ExpandProperty Property

### Alternatívne riešenie

Keďže `Get-Item` posiela do rúry len jeden objekt, môžeme vytiahnuť jeho
vlastnosť priamo. Do výsledku sa pošle pole názvov fontov, ktoré zotriedime:

	(Get-Item "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Fonts").Property | 
	    Sort-Object

Vypíšte zoznam všetkých vyhľadávacích strojov v Internet Exploreri a URL adries, ktorými sa vyhľadáva
------------------------------------------------------------------------------------------------------
Kľúč `HKEY_CURRENT_USER:\Software\Microsoft\Internet Explorer\SearchScopes`
obsahuje podkľúče reprezentujúce jednotlivé vyhľadávacie stroje.

Stačí vypísať jeho "podsúbory" a pre každý z nich vytiahnuť príslušnú
vlastnosť:

    Get-ChildItem "HKCU:\Software\Microsoft\Internet Explorer\SearchScopes" |
        Get-ItemProperty |
            Select-Object DisplayName, URL
            
Skrátená verzia:

    gci "HKCU:\Software\Microsoft\Internet Explorer\SearchScopes"
        | gp
            | select displayname, url                  

	
Zistite, či je nastavená systémová premenná `JAVA_HOME`
-----------------------------------------------------------------------------

### Priamy prístup k premennej prostredia

    $Env:JAVA_HOME

Powershell používa na premenné prostredia špeciálnu predponu premenných: `Env:`.
Ak premenná neexistuje, nevráti sa nič.

### Prístup cez providera `Env:`s

Provider `Env:` slúži na premenné prostredia.

    Get-ChildItem Env:\JAVA_HOME | Select-Object Value 

Alternatívne:

	gci Env:\JAVA_HOME | % value

Nastavte `JAVA_HOME` pre všetkých používateľov na `C:\java\jdk8` [`Set-Item-Property`]
--------------------------------------------------------------------------------------

    Set-ItemProperty "HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" JAVA_HOME C:\java\jdk8

Cmdlet `Set-ItemProperty` umožňuje nastaviť vlastnosť konkrétnemu objektu,
ktorý je pod správou providera. V našom prípade umožňuje nastaviť kľúče
a hodnoty pre položky registra.

![Nová nastavená položka](powershell-registry.png)