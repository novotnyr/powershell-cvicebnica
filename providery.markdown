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

	Get-PSDrive | Where {$_.Provider -match "FileSystem"} | Select Root

alebo

	gdr | ? {$_.Provider -match "FileSystem"} | Select Root

Problém v tomto prípade spočíva v tom, že sú zahrnuté aj vymeniteľné jednotky (DVD mechaniky), v ktorých sa nemusí nachádzať disk a teda prípadný výpis ich obsahu zlyhá.

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

Alternatívne riešenie:

Keďže `Get-Item` posiela do rúry len jeden objekt, môžeme vytiahnuť jeho
vlastnosť priamo. Do výsledku sa pošle pole názvov fontov, ktoré zotriedime:

	(Get-Item "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Fonts").Property | 
	    Sort-Object

Vypíšte zoznam všetkých vyhľadávacích strojov v Internet Exploreri a URL adries, ktorými sa vyhľadáva
------------------------------------------------------------------------------------------------------
Využijeme providera pre registre. "Jednotka" `HKCU:` zodpovedá registru
`HKEY_CURRENT_USER`, teda nastaveniam špecifickým pre konkrétneho
používateľa.

Nastavenia pod konkrétnym kľúčom registra (*registry key*, čiže priečinok v registri)
získame podobne ako pri výpise bežného adresára súborového systému a to
cez `Get-ChildItem`.

Registre vracajú položku `RegistryKey`, čo je kvázi-mapa z kľúčov do
hodnôt. Metódou `GetValue()` vieme získať hodnotu na základe daného
kľúča.
	
	Get-ChildItem "HKCU:\Software\Microsoft\Internet Explorer\SearchScopes" | 
	    ForEach-Object { $_.GetValue("DisplayName") + ": " + $_.GetValue("URL") }
	
Alternatívne:	
	
	ls "HKCU:\Software\Microsoft\Internet Explorer\SearchScopes" | 
	    % { $_.GetValue("DisplayName") + ": " + $_.GetValue("URL") }


Zistite, či je nastavená systémová premenná `JAVA_HOME` [premenné prostredia]
-----------------------------------------------------------------------------
Provider `Env:` slúži na premenné prostredia.

    Get-ChildItem Env:\JAVA_HOME | Select-Object Value 

Alternatívne:

	dir Env:\JAVA_HOME | select value

Zistite, či je nastavená systémová premenná `JAVA_HOME` [registre]
------------------------------------------------------------------

	(Get-Item "HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment")
	    .GetValue("JAVA_HOME")

Nastavte `JAVA_HOME` pre všetkých používateľov na `C:\java\jdk6`.
----------------------------------------------------------------
 
	(Get-Item "HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment")
	    .SetValue("JAVA_HOME", "C:\Java\JDK")
