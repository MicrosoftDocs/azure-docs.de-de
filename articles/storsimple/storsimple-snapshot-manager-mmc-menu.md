---
title: StorSimple Snapshot Manager-MMC-Menüaktionen | Microsoft Docs
description: Beschreibt die Verwendung der Standardmenüaktionen der Microsoft Management Console (MMC) im StorSimple Snapshot Manager.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 78ef81af-0d3a-4802-be54-ad192f9ac8a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: be761f0a2e59878139ae54dba62634a53b172711
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64715274"
---
# <a name="use-the-mmc-menu-actions-in-storsimple-snapshot-manager"></a>Verwenden der MMC-Menüaktionen im StorSimple Snapshot Manager

## <a name="overview"></a>Übersicht
In StorSimple Snapshot Manager werden die folgenden Aktionen in allen Aktionsmenüs und allen Varianten des Bereichs **Aktionen** aufgeführt.

* Sicht
* Neues Fenster hier öffnen 
* Aktualisieren 
* Liste exportieren 
* Hilfe 

Diese Aktionen sind Teil der Microsoft Management Console (MMC) und nicht spezifisch für StorSimple Snapshot Manager. In diesem Lernprogramm werden diese Aktionen und ihre Verwendung in StorSimple Snapshot Manager beschrieben.

## <a name="view"></a>Sicht
Sie können mit der Option **Ansicht** die Anzeige im Bereich **Ergebnisse** und im Konsolenfenster ändern. 

#### <a name="to-change-the-results-pane-view"></a>So ändern Sie die Ansicht im Ergebnisbereich
1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten.
2. Klicken Sie im Fensterbereich **Bereich** mit der rechten Maustaste auf einen beliebigen Knoten, oder erweitern Sie diesen, und klicken Sie mit der rechten Maustaste auf ein Element im Bereich **Ergebnisse**, und klicken Sie dann auf die Option **Ansicht**. 
3. Um im Bereich **Ergebnisse** Spalten hinzuzufügen oder zu entfernen, klicken Sie auf **Spalten hinzufügen/entfernen**. Das Dialogfeld **Spalten hinzufügen/entfernen** wird angezeigt.
   
    ![Hinzufügen oder Entfernen von Spalten aus dem Ergebnisbereich](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Add_remove_columns.png) 
4. Füllen Sie das Formular wie folgt aus:
   
   * Wählen Sie Elemente aus der Liste **Verfügbare Spalten** aus, und klicken Sie auf **Hinzufügen**, um sie der Liste **Angezeigte Spalten** hinzuzufügen. 
   * Klicken Sie auf Elemente in der Liste **Angezeigte Spalten**, und klicken Sie auf **Entfernen**, um sie aus der Liste zu entfernen. 
   * Wählen Sie ein Element aus der Liste **Angezeigte Spalten** aus, und klicken Sie auf **Nach oben** oder **Nach unten**, um das Element in der Liste nach oben bzw. unten zu verschieben. 
   * Klicken Sie auf **Standardeinstellungen wiederherstellen**, um die Standardkonfiguration des Bereichs **Ergebnisse** wiederherzustellen. 
5. Wenn Sie mit der Auswahl fertig sind, klicken Sie auf **OK**. 

#### <a name="to-change-the-console-window-view"></a>So ändern Sie die Ansicht des Konsolenfensters
1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten.
2. Klicken Sie im Fensterbereich **Bereich** mit der rechten Maustaste auf einen beliebigen Knoten, klicken Sie auf **Ansicht** und dann auf **Anpassen**. Das Dialogfeld **Anpassen** wird angezeigt.
   
    ![Anpassen des Konsolenfensters](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Customize.png) 
3. Aktivieren oder deaktivieren Sie die Kontrollkästchen, um die jeweiligen Elemente im Konsolenfenster ein- oder auszublenden. Wenn Sie mit der Auswahl fertig sind, klicken Sie auf **OK**.

## <a name="new-window-from-here"></a>New Window from Here
Mit der Option **New Window from Here** können Sie ein neues Fenster öffnen.

#### <a name="to-open-a-new-console-window"></a>So öffnen Sie ein neues Konsolenfenster
1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten.
2. Klicken Sie im Fensterbereich **Bereich** mit der rechten Maustaste auf einen beliebigen Knoten, und klicken Sie dann auf **Neues Fenster hier öffnen**. 
   
    Ein neues Fenster wird angezeigt, in dem nur der ausgewählte Gültigkeitsbereich berücksichtigt wird. Wenn Sie beispielsweise mit der rechten Maustaste auf den Knoten **Sicherungsrichtlinien** klicken, werden im neuen Fenster nur der Knoten **Sicherungsrichtlinien** im Fensterbereich **Bereich** und eine Liste der definierten Sicherungsrichtlinien im Bereich **Ergebnisse** angezeigt. Siehe folgendes Beispiel.
   
    ![Neues Fenster hier öffnen](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_NewWindow.png) 

## <a name="refresh"></a>Aktualisieren
Mit der Aktion **Aktualisieren** können Sie das Konsolenfenster aktualisieren.

#### <a name="to-update-the-console-window"></a>So aktualisieren Sie das Konsolenfenster
1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten.
2. Klicken Sie im Fensterbereich **Bereich** mit der rechten Maustaste auf einen beliebigen Knoten, oder erweitern Sie diesen, klicken Sie mit der rechten Maustaste auf ein Element im Bereich **Ergebnisse**, und klicken Sie dann auf **Aktualisieren**. 

## <a name="export-list"></a>Liste exportieren
Mit der Aktion **Liste exportieren** können Sie eine Liste in einer Datei mit kommagetrennten Werten (CSV) speichern. Beispielsweise können Sie die Liste der Sicherungsrichtlinien oder den Sicherungskatalog exportieren. Sie können die CSV-Datei dann zur Analyse in eine Tabellenkalkulationsanwendung importieren.

#### <a name="to-save-a-list-in-a-comma-separated-value-csv-file"></a>So speichern Sie eine Liste in einer Datei mit kommagetrennten Werten (CSV)
1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten. 
2. Klicken Sie im Fensterbereich **Bereich** mit der rechten Maustaste auf einen beliebigen Knoten, oder erweitern Sie diesen, klicken Sie mit der rechten Maustaste auf ein Element im Bereich **Ergebnisse**, und klicken Sie dann auf **Liste exportieren**. 
3. Das Dialogfeld **Liste exportieren** wird angezeigt. Füllen Sie das Formular wie folgt aus: 
   
   1. Geben Sie im Feld **Dateiname** einen Namen für die CSV-Datei ein, oder klicken Sie auf den Pfeil, um einen Namen aus der Dropdownliste auszuwählen.
   2. Klicken Sie im Feld **Dateityp** auf den Pfeil, und wählen Sie einen Dateityp aus der Dropdownliste aus.
   3. Um nur ausgewählte Elemente zu speichern, markieren Sie die Zeilen, und klicken Sie dann auf das Kontrollkästchen **Nur markierte Zeilen speichern** . Um alle exportierten Listen zu speichern, deaktivieren Sie das Kontrollkästchen **Nur markierte Zeilen speichern** .
   4. Klicken Sie auf **Speichern**.
      
      ![Exportieren von Listen als CSV-Datei](./media/storsimple-snapshot-manager-mmc-menu/HCS_SSM_Export_List.png) 

## <a name="help"></a>Hilfe
Über das Menü **Hilfe** können Sie die verfügbare Onlinehilfe für den StorSimple Snapshot Manager und die MMC anzeigen.

#### <a name="to-view-available-online-help"></a>So zeigen Sie die verfügbare Onlinehilfe an
1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten.
2. Klicken Sie im Fensterbereich **Bereich** mit der rechten Maustaste auf einen beliebigen Knoten, oder erweitern Sie diesen, klicken Sie mit der rechten Maustaste auf ein Element im Bereich **Ergebnisse**, und klicken Sie dann auf **Hilfe**. 

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen über die [Benutzeroberfläche des StorSimple Snapshot Managers](storsimple-use-snapshot-manager.md).
* Weitere Informationen zum [Verwenden von StorSimple Snapshot Manager zum Verwalten der StorSimple-Lösung](storsimple-snapshot-manager-admin.md).

