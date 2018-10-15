---
title: Cvičebnica z Powershellu -- Formátovače
layout: page
---

Formátovače
===========

Vypíšte obsah adresára
----------------------
Štandardný výpis použije tabuľkový výpis

    Get-ChildItem
    
Výsledok
    
        Directory: C:\Users\novotnyr

    Mode                LastWriteTime     Length Name
    ----                -------------     ------ ----
    d-r--       30. 9. 2016     10:12            Contacts
    d-r--       1. 10. 2016     14:17            Desktop
    d-r--       30. 9. 2016     10:12            Documents
    d-r--      16. 10. 2016     15:59            Downloads
    d-r--       30. 9. 2016     10:12            Favorites
    d-r--       30. 9. 2016     10:12            Links
    d-r--       30. 9. 2016     10:12            Music
    d-r--       30. 9. 2016     10:12            Pictures
    d-r--       30. 9. 2016     10:12            Saved Games
    d-r--       30. 9. 2016     10:12            Searches
    d-r--       30. 9. 2016     10:12            Videos

Cmdlet štandardne vypíše len niekoľko vybraných vlastností objektu v
tabuľkovom formáte.

Vypíšte názov a plnú cestu k súboru v užšom formáte
---------------------------------------------------

	ls | select Name, FullName | Format-Table -AutoSize

alebo

	ls | select Name, FullName | ft -a

Na konci každej rúry je formátovač, ktorý zapíše objekty v primeranom tvare na konzolu. Ak neuvedieme žiaden formátovač, použije sa zväčša tabuľkový formátovač `Format-Table`.

Parametrom `-AutoSize` nastavíme automatické šírky stĺpcov podľa najširšej položky.

Vypíšte obsah aktuálneho adresára do mriežky [`Format-Wide`]
------------------------------------------------------------

    Get-ChildItem | Format-Wide

Výsledok:

        Directory: C:\Users\rn

    [Contacts]                                                  [Desktop]
    [Documents]                                                 [Downloads]
    [Favorites]                                                 [Links]
    [Music]                                                     [Pictures]
    [Saved Games]                                               [Searches]
    [Videos]                                                    items.xml
    rss.xml

Vypíšte obsah aktuálneho adresára [`Format-Wide` a `AutoSize`]
--------------------------------------------------------------

    Get-ChildItem | Format-Wide -AutoSize

Výpis naformátuje do primeraného počtu stĺpcov:

        Directory: C:\Users\novotnyr

    [Contacts]     [Desktop]      [Documents]    [Downloads]    [Favorites]    
    [Links]        [Music]        [Pictures]     [Saved Games]  [Searches]
    [Videos]       items.xml      rss.xml

Vypíšte obsah aktuálneho adresára pod seba [`Format-List`]
----------------------------------------------------------

    Get-ChildItem -File | Format-List -Property Name, Length, LastWriteTime

Výstup ukáže jednotlivé objekty pod seba, s vlastnosťami pod sebou:

    Name          : items.xml
    Length        : 57
    LastWriteTime : 10/7/2018 10:07:11 AM
    
    Name          : rss.xml
    Length        : 8929
    LastWriteTime : 10/9/2018 5:32:33 AM

### Alternatívne riešenie: skrátený zápis [alias `fl`]

    gci -file | fl name, length, lastwritetime