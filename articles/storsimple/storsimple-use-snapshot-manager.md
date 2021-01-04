---
title: Benutzeroberfläche des StorSimple Snapshot Managers | Microsoft Docs
description: Beschreibt die Benutzeroberfläche des StorSimple Snapshot Managers und erläutert, wie dieser zum Verwalten von Sicherungsaufträgen und des Sicherungskatalogs verwendet wird.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: c7d91892-2881-41a2-a7a2-908dc3646493
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.custom: ''
ms.openlocfilehash: 35fa0fc134045585eebd21324352f9b4ff272e87
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954238"
---
# <a name="use-storsimple-snapshot-manager-user-interface-to-manage-backup-jobs-and-backup-catalog"></a>Verwenden der Benutzeroberfläche des StorSimple Snapshot Managers zum Verwalten von Sicherungsaufträgen und Sicherungskatalog

## <a name="overview"></a>Übersicht
Der StorSimple Snapshot Manager verfügt über eine intuitive Benutzeroberfläche, über die Sie Sicherungen erstellen und verwalten können. Dieses Tutorial bietet eine Einführung in die Benutzeroberfläche und erläutert, wie die einzelnen Komponenten verwendet werden. Eine ausführliche Beschreibung des StorSimple Snapshot Managers finden Sie unter [Was ist der StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="console-description"></a>Konsolenbeschreibung
Um die Benutzeroberfläche anzuzeigen, klicken Sie auf das Symbol des StorSimple Snapshot Managers auf Ihrem Desktop. Das Konsolenfenster wird angezeigt, wie in der folgenden Abbildung dargestellt.

![StorSimple Snapshot Manager – Bereiche](./media/storsimple-use-snapshot-manager/HCS_SSM_gui_panes.png)

Das Konsolenfenster besitzt fünf Hauptelemente. Klicken Sie auf den entsprechenden Link für eine vollständige Beschreibung des jeweiligen Elements.

* [Menüleiste](#menu-bar) 
* [Symbolleiste](#tool-bar) 
* [Bereichsfenster](#scope-pane) 
* [Ergebnisbereich](#results-pane) 
* [Bereich "Aktionen"](#actions-pane) 

Darüber hinaus unterstützt der StorSimple Snapshot Manager [die Tastaturnavigation und eine Reihe von Tastenkombinationen](#keyboard-navigation-and-shortcuts).

### <a name="console-accessibility"></a>Konsolenbarrierefreiheit
Die Benutzeroberfläche des StorSimple Snapshot Managers unterstützt die Features zur Barrierefreiheit des Windows-Betriebssystems und der Microsoft Management Console (MMC) sowie einige StorSimple Snapshot Manager-spezifische Tastenkombinationen. 

* Eine Beschreibung der Windows-Features zur Barrierefreiheit finden Sie unter [Tastenkombinationen für Windows](https://support.microsoft.com/kb/126449). 
* Eine Beschreibung der MMC-Barrierefreiheitsfeatures finden Sie unter [Barrierefreiheit für MMC 3.0](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc766075(v=ws.11))
* Eine Beschreibung der Features zur Barrierefreiheit des StorSimple Snapshot Managers finden Sie unter [Tastaturnavigation und Tastenkombinationen](#keyboard-navigation-and-shortcuts).

## <a name="menu-bar"></a>Menüleiste
Die Menüleiste am oberen Rand des Konsolenfensters enthält die Menüs [Datei](#file-menu), [Aktion](#action-menu), [Ansicht](#view-menu), [Favoriten](#favorites-menu), [Fenster](#window-menu) und [Hilfe](#help-menu).

Klicken Sie auf ein Element auf der Menüleiste, um eine Liste der verfügbaren Befehle in diesem Menü anzuzeigen. Das folgende Beispiel zeigt das ausgewählte Menü **Ansicht** auf der Menüleiste.

![Ausgewähltes Menü "Ansicht"](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png)

### <a name="file-menu"></a>Menü „Datei“
Das Menü **Datei** enthält Standardbefehle der Microsoft Management Console (MMC).

#### <a name="menu-access"></a>Menüzugriff
Klicken Sie zum Anzeigen des Menüs **Datei** auf der Menüleiste auf **Datei**. Das folgende Menü wird angezeigt.

![StorSimple Snapshot Manager-Menü "Datei"](./media/storsimple-use-snapshot-manager/HCS_SSM_FileMenu.png) 

#### <a name="menu-description"></a>Menübeschreibung
In der folgenden Tabelle werden die Elemente im Menü **Datei** beschrieben.

| Menüelement | BESCHREIBUNG |
|:--- |:--- |
| Neu |Klicken Sie auf **Neu** , um mithilfe des StorSimple Snapshot Managers eine neue Konsole zu erstellen. |
| Öffnen |Klicken Sie auf **Öffnen** , um eine vorhandene Konsole zu öffnen. |
| Speichern |Klicken Sie auf **Speichern** , um die aktuelle Konsole zu speichern. |
| Speichern unter |Klicken Sie auf **Speichern unter** , um eine neue, umbenannte Instanz der aktuellen Konsole zu erstellen. Verwenden Sie die Option **Speichern unter** , um eine Ansicht anzupassen und für den späteren Abruf zu speichern. Sie können beispielsweise StorSimple Snapshot Manager-Snap-Ins erstellen, die auf bestimmte Server verweisen. |
| Snap-In hinzufügen/entfernen |Klicken Sie auf **Snap-In hinzufügen/entfernen**, um Snap-Ins hinzuzufügen oder zu entfernen und um die Knoten im **Bereichsfenster** zu organisieren. Weitere Informationen finden Sie unter [Hinzufügen, Entfernen und Organisieren von Snap-Ins und Erweiterungen in MMC 3.0](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc722035(v=ws.11)). |
| Tastatur |Klicken Sie auf **Optionen** , um das Konsolensymbol zu ändern, Benutzerzugriffsmodi und Berechtigungen anzugeben oder Konsolendateien zu löschen, um Speicherplatz freizugeben. |
| Liste von Dateipfaden |Klicken Sie auf einen Pfad in der nummerierten Liste, um eine der Dateien, die Sie zuletzt geöffnet hatten, erneut zu öffnen. |
| Beenden |Klicken Sie auf **Beenden**, um das Menü **Datei** zu schließen. |

### <a name="action-menu"></a>Menü "Aktion"
Im Menü **Aktion** können Sie aus verschiedenen verfügbaren Aktionen auswählen. Die verfügbaren Elemente hängen von Ihrer Auswahl im **Bereichsfenster** oder im **Ergebnisbereich** ab.

#### <a name="menu-access"></a>Menüzugriff
Führen Sie zum Anzeigen des Menüs **Aktion** einen der folgenden Schritte aus:

* Klicken Sie mit der rechten Maustaste auf ein Element im **Bereichsfenster** oder im **Ergebnisbereich**.
* Wählen Sie ein Element im **Bereichsfenster** oder im **Ergebnisbereich** aus, und klicken Sie dann auf der Menüleiste auf **Aktion**. 

Wenn Sie z.B. den obersten Knoten im **Bereichsfenster** auswählen und dann mit der rechten Maustaste klicken oder auf der Menüleiste auf **Aktion** klicken, wird das folgende Menü angezeigt.

![StorSimple Snapshot Manager-Menü "Aktion"](./media/storsimple-use-snapshot-manager/HCS_SSM_Action_menu.png)

Der Bereich **Aktionen** (rechts in der Konsole) enthält die gleiche Liste von Aktionen wie das Menü **Aktion**. Darüber hinaus enthält der Bereich **Aktionen** die Optionen des Menüs **Ansicht**, mit denen Sie eine benutzerdefinierte Ansicht des **Ergebnisbereichs** erstellen können.

![Bereich "Aktionen" mit geöffnetem Menü "Ansicht"](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

#### <a name="menu-description"></a>Menübeschreibung
Die folgende Tabelle enthält eine alphabetische Liste aller StorSimple Snapshot Manager-Aktionen. 

* Die Spalte **Aktion** enthält die Aktionen, die Sie für Knoten und Ergebnisse ausführen können. 
* Die Spalte **Navigation** erläutert, wie Sie die entsprechende Option im Menü **Aktion** anzeigen, um die Aktion auszuwählen. Einige Aktionen werden in mehreren Menüs **Aktion** angezeigt. Für diese Aktionen wählen Sie eine Option zur **Navigation** aus der Liste aus. 
* In der Spalte **Beschreibung** werden die einzelnen Aktionen im Menü **Aktion** bzw. im Bereich „Aktionen“ und ihre Funktionsweise erläutert.

> [!NOTE]
> Der Bereich **Aktionen** und das Menü **Aktion** enthalten zusätzliche Optionen, wie z.B. **Anzeigen**, **Neues Fenster hier öffnen**, **Aktualisieren**, **Liste exportieren** und **Hilfe**. Diese Optionen werden von der MMC zur Verfügung gestellt und sind nicht spezifisch für den StorSimple Snapshot Manager. Die Tabelle enthält Beschreibungen dieser Optionen.
> 
> 

| Aktion | Navigation | BESCHREIBUNG |
|:--- |:--- |:--- |
| Authenticate |Klicken Sie auf den Knoten **Geräte**, und klicken Sie dann im **Ergebnisbereich** mit der rechten Maustaste auf ein Gerät. |Klicken Sie auf **Authentifizieren** , um das für das Gerät konfigurierte Kennwort einzugeben. |
| Klon |Erweitern Sie nacheinander **Sicherungskatalog** und **Cloudmomentaufnahmen**, klicken Sie auf eine datierte Sicherung, und wählen Sie dann ein Volume im **Ergebnisbereich** aus. |Klicken Sie auf **Klonen** , um eine Kopie einer Cloudmomentaufnahme zu erstellen und in einem von Ihnen festgelegten Verzeichnis zu speichern. |
| Gerät konfigurieren |Klicken Sie mit der rechten Maustaste auf den Knoten **Geräte** . |Klicken Sie auf **Gerät konfigurieren** , um ein oder mehrere Geräte für die Verbindung mit dem Windows-Host zu konfigurieren. |
| Sicherungsrichtlinie erstellen |Führen Sie eines der folgenden Verfahren aus:<ul><li>Klicken Sie mit der rechten Maustaste auf **Sicherungsrichtlinien**.</li><li>Erweitern Sie **Volumegruppen**, oder klicken Sie darauf, und klicken Sie dann mit der rechten Maustaste auf eine Volumegruppe.</li><li>Erweitern Sie **Sicherungskatalog**, oder klicken Sie darauf, und klicken Sie dann mit der rechten Maustaste auf eine Volumegruppe.</li></ul> |Klicken Sie auf **Sicherungsrichtlinie erstellen** , um eine geplante Sicherung für eine Volumegruppe zu konfigurieren. |
| Volumegruppe erstellen |Führen Sie eines der folgenden Verfahren aus:<ul><li>Klicken Sie auf den Knoten **Volumes**, und klicken Sie dann im **Ergebnisbereich** mit der rechten Maustaste auf ein Volume.</li><li>Klicken Sie mit der rechten Maustaste auf den Knoten **Volumegruppen** .</li></ul> |Klicken Sie auf **Volumegruppe erstellen** , um einer Volumegruppe Volumes zuweisen. |
| Löschen |Klicken Sie auf einen Knoten oder ein Ergebnis. (Dieses Element wird häufig im Menü **Aktion** und im Bereich **Aktionen** angezeigt.) |Klicken Sie auf **Löschen** , um den ausgewählten Knoten oder das ausgewählte Ergebnis zu löschen. Wenn das Bestätigungsdialogfeld angezeigt wird, können Sie den Löschvorgang bestätigen oder abbrechen. |
| Details |Klicken Sie auf den Knoten **Geräte**, und klicken Sie dann im **Ergebnisbereich** mit der rechten Maustaste auf ein Gerät. |Klicken Sie auf **Details** , um die Konfigurationsdetails für ein Gerät anzuzeigen. |
| Edit (Bearbeiten) |Klicken Sie auf **Sicherungsrichtlinien**, und klicken Sie dann im **Ergebnisbereich** mit der rechten Maustaste auf eine Richtlinie. |Klicken Sie auf **Bearbeiten** , um den Sicherungszeitplan für eine Volumegruppe zu ändern. |
| Liste exportieren |Klicken Sie auf einen Knoten oder ein Ergebnis. (Dieses Element wird immer im Menü **Aktion** und im Bereich **Aktionen** angezeigt.) |Klicken Sie auf **Liste exportieren** , um eine Liste in einer Datei mit durch Trennzeichen getrennten Werten (CSV) zu speichern. Sie können diese Datei dann für die Analyse in eine Tabellenkalkulationsanwendung importieren. |
| Hilfe |Klicken Sie auf einen Knoten oder ein Ergebnis. (Dieses Element wird immer im Menü **Aktion** und im Bereich **Aktionen** angezeigt.) |Klicken Sie auf **Hilfe** , um die Onlinehilfe in einem separaten Browserfenster zu öffnen. |
| Neues Fenster hier öffnen |Klicken Sie auf einen Knoten oder ein Ergebnis. (Dieses Element wird immer im Menü **Aktion** und im Bereich **Aktionen** angezeigt.) |Klicken Sie auf **Neues Fenster hier öffnen** , um ein neues StorSimple Snapshot Manager-Fenster zu öffnen. |
| Aktualisieren |Klicken Sie auf einen Knoten oder ein Ergebnis. (Dieses Element wird immer im Menü **Aktion** und im Bereich **Aktionen** angezeigt.) |Klicken Sie auf **Aktualisieren** , um das aktuell angezeigte StorSimple Snapshot Manager-Fenster zu aktualisieren. |
| Gerät aktualisieren |Klicken Sie auf den Knoten **Geräte**, und klicken Sie dann im **Ergebnisbereich** mit der rechten Maustaste auf ein Gerät. |Klicken Sie auf **Gerät aktualisieren** , um ein bestimmtes verbundenes Gerät mit StorSimple Snapshot Manager zu synchronisieren. |
| Geräte aktualisieren |Klicken Sie mit der rechten Maustaste auf den Knoten **Geräte** . |Klicken Sie auf **Geräte aktualisieren** , um die Liste verbundener Geräte mit StorSimple Snapshot Manager zu synchronisieren. |
| Erneutes Einlesen von Volumes |Klicken Sie mit der rechten Maustaste auf den Knoten **Volumes** . |Klicken Sie auf **Volumes erneut überprüfen**, um die Liste der Volumes im **Ergebnisbereich** zu aktualisieren. |
| Restore |Erweitern Sie nacheinander **Sicherungskatalog**, eine Volumegruppe, **Lokale Momentaufnahmen** oder **Cloudmomentaufnahmen**, und klicken Sie dann mit der rechten Maustaste auf eine Sicherung. |Klicken Sie auf **Wiederherstellen** , um die aktuellen Volumegruppendaten durch die Daten aus der ausgewählten Sicherung zu ersetzen. |
| Sicherung anlegen |Führen Sie eines der folgenden Verfahren aus:<ul><li>Erweitern Sie **Volumegruppen**, und klicken Sie dann mit der rechten Maustaste auf eine Volumegruppe.</li><li>Erweitern Sie **Sicherungskatalog**, und klicken Sie dann mit der rechten Maustaste auf eine Volumegruppe.</li></ul> |Klicken Sie auf **Sicherung anlegen** , um sofort einen Sicherungsauftrag zu starten. |
| Importanzeige umschalten |Klicken Sie im **Bereichsfenster** mit der rechten Maustaste auf den obersten Knoten (in den Beispielen der Knoten **StorSimple Snapshot Manager**). |Klicken Sie auf **Importanzeige umschalten**, um die Volumegruppen und die zugeordneten Sicherungen, die aus dem Dashboard des StorSimple-Geräte-Manager-Diensts importiert wurden, ein- oder auszublenden. |

### <a name="view-menu"></a>Menü "Ansicht"
Verwenden Sie das Menü **Ansicht**, um eine benutzerdefinierte Ansicht der Inhalte im **Ergebnisbereich** zu erstellen. Das Menü **Ansicht** enthält die Optionen **Spalten hinzufügen/entfernen** und **Anpassen**.

#### <a name="menu-access"></a>Menüzugriff
Sie finden das Menü **Ansicht** auf der Menüleiste oder im Bereich **Aktionen**.

![StorSimple Snapshot Manager-Menü "Ansicht"](./media/storsimple-use-snapshot-manager/HCS_SSM_View_menu.png) 

#### <a name="menu-description"></a>Menübeschreibung
In der folgenden Tabelle werden die Elemente im Menü **Ansicht** beschrieben.

| Menüelement | BESCHREIBUNG |
|:--- |:--- |
| Spalten hinzufügen/entfernen |Klicken Sie auf **Spalten hinzufügen/entfernen**, um Spalten im **Ergebnisbereich** hinzuzufügen oder daraus zu entfernen. |
| Anpassen |Klicken Sie auf **Anpassen** , um Elemente im StorSimple Snapshot Manager-Konsolenfenster ein-oder auszublenden. |

### <a name="favorites-menu"></a>Menü "Favoriten"
Verwenden Sie das Menü **Favoriten** zum Hinzufügen, Entfernen und Organisieren von Seitenansichten und Aufgaben, die Sie häufig verwenden. 

#### <a name="menu-access"></a>Menüzugriff
Sie finden das Menü **Favoriten** auf der Menüleiste.

![StorSimple Snapshot Manager-Menü "Favoriten"](./media/storsimple-use-snapshot-manager/HCS_SSM_FavoritesMenu.png)

#### <a name="menu-description"></a>Menübeschreibung
In der folgenden Tabelle werden die Elemente im Menü **Favoriten** beschrieben.

| Menüelement | BESCHREIBUNG |
|:--- |:--- |
| Zu Favoriten hinzufügen |Klicken Sie auf **Zu Favoriten hinzufügen** , um Ihrer Favoritenliste die aktuelle Ansicht hinzuzufügen. |
| Favoriten verwalten |Klicken Sie auf **Favoriten verwalten** , um den Inhalt des Ordners "Favoriten" zu organisieren. |

### <a name="window-menu"></a>Menü "Fenster"
Verwenden Sie das Menü **Fenster** zum Hinzufügen und Anordnen von StorSimple Snapshot Manager-Konsolenfenstern.

#### <a name="menu-access"></a>Menüzugriff
Sie finden das Menü **Fenster** auf der Menüleiste.

![StorSimple Snapshot Manager-Menü "Fenster"](./media/storsimple-use-snapshot-manager/HCS_SSM_WindowMenu.png)

Die nummerierte Liste am unteren Rand des Menüs enthält die zurzeit geöffneten Fenster. Klicken Sie auf ein Fenster in der Liste, um es im Vordergrund anzuzeigen. 

#### <a name="menu-description"></a>Menübeschreibung
In der folgenden Tabelle werden die Elemente im Menü "Fenster" beschrieben.

| Menüelement | BESCHREIBUNG |
|:--- |:--- |
| Neues Fenster |Klicken Sie auf **Neues Fenster** , um ein neues Konsolenfenster (zusätzlich zum vorhandenen Fenster) zu öffnen. |
| Cascade |Klicken Sie auf **Überlappend** , um die offenen Konsolenfenster überlappend anzuzeigen. |
| Untereinander |Klicken Sie auf **Untereinander**, um die offenen Konsolenfenster in einem Kachel- oder Rasterlayout anzuzeigen. |
| Symbole anordnen |Wenn Sie mehrere Konsolenfenster geöffnet haben, die verteilt auf Ihrem Desktop angezeigt werden, können Sie diese minimieren. Klicken Sie anschließend auf **Symbole anordnen**, um sie unten auf dem Bildschirm in einer horizontalen Zeile anzuordnen. |

### <a name="help-menu"></a>Menü "Hilfe"
Verwenden Sie das Menü **Hilfe** zum Anzeigen der verfügbaren Onlinehilfe zum StorSimple Snapshot Manager und zur MMC. Sie können auch Informationen über die derzeit auf Ihrem System installierten Softwareversionen von MMC und StorSimple Snapshot Manager anzeigen. 

Sie finden das Menü **Hilfe** auf der Menüleiste. Sie können auch über den Bereich **Aktionen** auf die Hilfethemen zum StorSimple Snapshot Manager zugreifen.

![StorSimple Snapshot Manager-Menü "Hilfe"](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpMenu.png)

#### <a name="menu-description"></a>Menübeschreibung
In der folgenden Tabelle werden die Elemente im Menü "Hilfe" beschrieben.

| Menüelement | BESCHREIBUNG |
|:--- |:--- |
| Hilfe zum StorSimple Snapshot Manager |Klicken Sie auf **Hilfe zum StorSimple Snapshot Manager** , um die StorSimple Snapshot Manager-Hilfe in einem separaten Fenster zu öffnen. |
| Hilfethemen |Klicken Sie auf **Hilfethemen** , um die MMC-Onlinehilfe in einem separaten Fenster zu öffnen. |
| TechCenter-Website |Klicken Sie auf **TechCenter-Website** , um die Startseite des TechCenters in Microsoft TechNet in einem separaten Fenster zu öffnen. |
| Info |Klicken Sie auf **Info** , um anzuzeigen, welche Version der Microsoft Management Console auf dem System installiert ist. |
| Info zum StorSimple Snapshot Manager |Klicken Sie auf **Info zum StorSimple Snapshot Manager** , um anzuzeigen, welche Version des Snap-Ins auf dem System installiert ist. |

## <a name="tool-bar"></a>Symbolleiste
Die Symbolleiste unterhalb der Menüleiste enthält Navigations- und Aufgabensymbole. Jedes Symbol stellt eine Verknüpfung zu einer bestimmten Aufgabe dar.

### <a name="icon-descriptions"></a>Symbolbeschreibungen
In der folgenden Tabelle werden die Symbole auf der Symbolleiste beschrieben. 

| Symbol | BESCHREIBUNG |
|:--- |:--- |
| ![Pfeil nach links](./media/storsimple-use-snapshot-manager/HCS_SSM_LeftArrow.png) |Klicken Sie auf den Pfeil nach links, um zur vorherigen Seite zurückzukehren. |
| ![Pfeil nach rechts](./media/storsimple-use-snapshot-manager/HCS_SSM_RightArrow.png) |Klicken Sie auf den Pfeil nach rechts, um zur nächsten Seite zu wechseln (wenn der Pfeil grau ist, ist die Aktion nicht verfügbar). |
| ![Symbol "Nach oben"](./media/storsimple-use-snapshot-manager/HCS_SSM_Up.png) |Klicken Sie auf dieses Symbol, um in der Konsolenstruktur eine Ebene nach oben zu wechseln ( **Bereichsfenster** ). |
| ![Konsolenstruktur ein-/ausblenden](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowConsoleTree.png) |Klicken Sie auf dieses Symbol, um das **Bereichsfenster** ein- oder auszublenden. |
| ![Liste exportieren](./media/storsimple-use-snapshot-manager/HCS_SSM_ExportListIcon.png) |Klicken Sie auf dieses Symbol, um eine Liste in eine von Ihnen angegebene CSV-Datei zu exportieren. |
| ![Symbol "Hilfe"](./media/storsimple-use-snapshot-manager/HCS_SSM_HelpIcon.png) |Klicken Sie auf das Hilfesymbol, um ein Thema in der MMC-Onlinehilfe zu öffnen. |
| ![Bereich "Aktionen" ein-/ausblenden](./media/storsimple-use-snapshot-manager/HCS_SSM_ShowAction.png) |Klicken Sie auf dieses Symbol für den Bereich **Aktionen**, um den Bereich **Aktionen** ein- oder auszublenden. |

## <a name="scope-pane"></a>Bereichsfenster
Das **Bereichsfenster** ist der ganz linke Bereich in der StorSimple Snapshot Manager-Benutzeroberfläche. Es enthält die Konsolenstruktur (oder Knotenstruktur) und bildet den primären Navigationsbereich für StorSimple Snapshot Manager. 

### <a name="scope-pane-structure"></a>Struktur des Bereichsfensters
Das **Bereichsfenster** enthält eine Reihe von Objekten (Knoten), die in einer Baumstruktur angeordnet sind und auf die geklickt werden kann. 

![Bereichsfenster](./media/storsimple-use-snapshot-manager/HCS_SSM_Scope_pane.png) 

* Zum Erweitern oder Reduzieren eines Knotens klicken Sie auf den Pfeil neben dem Knotennamen.
* Um den Status oder den Inhalt eines Knotens anzuzeigen, klicken Sie auf den Knotennamen. Die Informationen werden im **Ergebnisbereich** angezeigt. 

Das **Bereichsfenster** enthält die folgenden Knoten: 

* [Knoten "Geräte"](#devices-node) 
* [Knoten "Volumes"](#volumes-node) 
* [Knoten "Volumegruppen"](#volume-groups-node) 
* [Knoten "Sicherungsrichtlinien"](#backup-policies-node) 
* [Knoten "Sicherungskatalog"](#backup-catalog-node) 
* [Knoten "Aufträge"](#jobs-node) 

### <a name="scope-pane-tasks"></a>Aufgaben im Bereichsfenster
Sie können im **Bereichsfenster** Aktionen für einen bestimmten Knoten ausführen. Um eine Aufgabe auszuwählen, führen Sie einen der folgenden Schritte aus:

* Klicken Sie mit der rechten Maustaste auf den Knoten, und wählen Sie dann die Aufgabe aus dem angezeigten Menü aus.
* Klicken Sie auf den Knoten, und klicken Sie dann auf der Menüleiste auf **Aktion** . Wählen Sie die Aufgabe aus dem angezeigten Menü aus.
* Klicken Sie auf den Knoten, und wählen Sie dann die Aktion im Bereich **Aktionen** aus.

Wenn Sie einen Knoten auswählen und mithilfe einer dieser Methoden eine Aufgabenliste anzeigen, werden nur die Aktionen angezeigt, die auf diesem Knoten ausgeführt werden können.

### <a name="devices-node"></a>Knoten "Geräte"
Der Knoten **Geräte** stellt die StorSimple-Geräte und die virtuellen StorSimple-Geräte dar, die mit dem StorSimple Snapshot Manager verknüpft sind. Wählen Sie diesen Knoten aus, um ein Gerät zu verbinden und zu konfigurieren und um seine zugehörigen Volumes, Volumegruppen und vorhandenen Sicherungskopien zu importieren. Es können mehrere Geräte mit einem Host verbunden sein.

* Zum Erweitern des Knotens klicken Sie auf das Pfeilsymbol neben **Geräte**.
* Um ein Menü mit den verfügbaren Aktionen anzuzeigen, klicken Sie mit der rechten Maustaste auf den Knoten **Geräte** oder auf einen der Knoten in der erweiterten Ansicht.
* Zum Anzeigen einer Liste der konfigurierten Geräte klicken Sie im **Bereichsfenster** auf **Geräte**. Die Liste der Geräte wird zusammen mit Informationen über die einzelnen Geräte im **Ergebnisbereich** angezeigt.

### <a name="volumes-node"></a>Knoten "Volumes"
Der Knoten **Volumes** stellt die vom Host bereitgestellten Laufwerke dar. Dazu gehören auch die per iSCSI und über ein Gerät erkannten Laufwerke. Verwenden Sie diesen Knoten, um eine Liste der verfügbaren Volumes anzuzeigen und einzelne Volumes Volumegruppen zuzuweisen.

* Zum Erweitern des Knotens klicken Sie auf das Pfeilsymbol neben **Volumes**.
* Um ein Menü der verfügbaren Aktionen anzuzeigen, klicken Sie mit der rechten Maustaste auf den Knoten **Volumes** oder auf einen der Knoten in der erweiterten Ansicht.
* Um eine Liste der Volumes anzuzeigen, klicken Sie im **Bereichsfenster** auf **Volumes**. Die Liste der Volumes wird zusammen mit Informationen über die einzelnen Volumes im **Ergebnisbereich** angezeigt.

### <a name="volume-groups-node"></a>Knoten "Volumegruppen"
Volumegruppen werden auch als Konsistenzgruppen bezeichnet. Jede Volumegruppe ist ein Pool von anwendungsbezogenen Volumes, der die Anwendungskonsistenz bei Sicherungsvorgängen sicherstellen hilft. Verwenden Sie den Knoten **Volumegruppen** zum Konfigurieren dieser Gruppen und zum Erstellen interaktiver Sicherungen oder von Sicherungszeitplänen. 

* Zum Erweitern des Knotens klicken Sie auf das Pfeilsymbol neben **Volumegruppen**.
* Um ein Menü der verfügbaren Aktionen anzuzeigen, klicken Sie mit der rechten Maustaste auf den Knoten **Volumegruppen** oder auf einen der Knoten in der erweiterten Ansicht.
* Um eine Liste der Volumegruppen anzuzeigen, klicken Sie im **Bereichsfenster** auf **Volumegruppen**. Die Liste der Volumegruppen wird zusammen mit Informationen über die einzelnen Volumegruppen im **Ergebnisbereich** angezeigt.

### <a name="backup-policies-node"></a>Knoten "Sicherungsrichtlinien"
Sicherungsrichtlinien sind Auftragszeitpläne für lokale und Cloudmomentaufnahmen. Über den Knoten **Sicherungsrichtlinien** können Sie angeben, wie oft eine Sicherung erstellt wird und wie lange eine Sicherung aufbewahrt werden soll. 

* Zum Erweitern des Knotens klicken Sie auf das Pfeilsymbol neben **Sicherungsrichtlinien**.
* Um ein Menü der verfügbaren Aktionen anzuzeigen, klicken Sie mit der rechten Maustaste auf den Knoten **Sicherungsrichtlinien** oder auf einen der Knoten in der erweiterten Ansicht.
* Klicken Sie zum Anzeigen einer Liste der Sicherungsrichtlinien im **Bereichsfenster** auf **Sicherungsrichtlinien**. Die Liste der Sicherungsrichtlinien wird zusammen mit Informationen über die einzelnen Sicherungsrichtlinien im **Ergebnisbereich** angezeigt.

> [!NOTE]
> Sie können maximal 64 Sicherungen speichern.


### <a name="backup-catalog-node"></a>Knoten "Sicherungskatalog"
Der Knoten **Sicherungskatalog** enthält Listen von Sicherungen von Azure StorSimple-Volumes, die lokal oder an einem anderen Standort gespeichert sind. Dieser Knoten wird über Volumegruppen organisiert, wobei jeder Volumegruppencontainer separate Strukturen für lokale Momentaufnahmen (Knoten **Lokale Momentaufnahmen**) und Cloudmomentaufnahmen (Knoten **Cloudmomentaufnahmen**) enthält. Wenn der Knoten erweitert wird, werden für jeden Volumegruppencontainer die erfolgreichen Sicherungen angezeigt, die interaktiv oder durch eine konfigurierte Richtlinie erstellt wurden.

* Zum Erweitern des Knotens klicken Sie auf das Pfeilsymbol neben **Sicherungskatalog**.
* Um ein Menü der verfügbaren Aktionen anzuzeigen, klicken Sie mit der rechten Maustaste auf den Knoten **Sicherungskatalog** oder auf einen der Knoten in der erweiterten Ansicht.
* Klicken Sie zum Anzeigen einer Liste der Sicherungsmomentaufnahmen im **Bereichsfenster** auf **Sicherungskatalog**. Die Liste der Momentaufnahmen wird zusammen mit Informationen über die einzelnen Momentaufnahmen im **Ergebnisbereich** angezeigt.

### <a name="local-snapshots-node"></a>Knoten "Lokale Momentaufnahmen"
Im Knoten **Lokale Momentaufnahmen** werden die lokalen Momentaufnahmen für eine bestimmte Volumegruppe aufgelistet. Der Knoten befindet sich im **Bereichsfenster** unter dem Knoten **Sicherungskatalog**. Lokale Momentaufnahmen sind Zeitpunktkopien der Volumedaten, die auf dem Azure StorSimple-Gerät gespeichert sind. Diese Art der Sicherung kann i. d. R. schnell erstellt und wiederhergestellt werden. Sie können eine lokale Momentaufnahme wie eine lokale Sicherungskopie verwenden.

* Zum Erweitern des Knotens klicken Sie auf das Pfeilsymbol neben **Lokale Momentaufnahmen**.
* Um ein Menü der verfügbaren Aktionen anzuzeigen, klicken Sie mit der rechten Maustaste auf den Knoten **Lokale Momentaufnahmen** oder auf einen der Knoten in der erweiterten Ansicht.
* Um eine Liste der lokalen Momentaufnahmen anzuzeigen, klicken Sie im **Bereichsfenster** auf **Lokale Momentaufnahmen**. Die Liste der Momentaufnahmen wird zusammen mit Informationen über die einzelnen Momentaufnahmen im **Ergebnisbereich** angezeigt.

### <a name="cloud-snapshots-node"></a>Knoten "Cloudmomentaufnahmen"
Im Knoten **Cloudmomentaufnahmen** werden die Cloudmomentaufnahmen für eine bestimmte Volumegruppe aufgelistet. Der Knoten befindet sich im **Bereichsfenster** unter dem Knoten **Sicherungskatalog**. Cloudmomentaufnahmen sind Zeitpunktkopien der Volumedaten, die in der Cloud gespeichert werden. Eine Cloudmomentaufnahme entspricht einer Momentaufnahme, die auf einem anderen, externen Speichersystem repliziert wird. Cloudmomentaufnahmen sind besonders nützlich für Notfallwiederherstellungszenarios.

* Zum Erweitern des Knotens klicken Sie auf das Pfeilsymbol neben **Cloudmomentaufnahmen**.
* Um ein Menü der verfügbaren Aktionen anzuzeigen, klicken Sie mit der rechten Maustaste auf den Knoten **Cloudmomentaufnahmen** oder auf einen der Knoten in der erweiterten Ansicht.
* Um eine Liste der Cloudmomentaufnahmen anzuzeigen, klicken Sie im **Bereichsfenster** auf **Cloudmomentaufnahmen**. Die Liste der Momentaufnahmen wird zusammen mit Informationen über die einzelnen Momentaufnahmen im **Ergebnisbereich** angezeigt.

### <a name="jobs-node"></a>Knoten "Aufträge"
Der Knoten **Aufträge** enthält Informationen zu geplanten, ausgeführten und kürzlich abgeschlossenen Sicherungsaufträgen. 

* Zum Erweitern des Knotens klicken Sie auf das Pfeilsymbol neben **Aufträge**.
* Um ein Menü der verfügbaren Aktionen anzuzeigen, klicken Sie mit der rechten Maustaste auf den Knoten **Aufträge** oder auf einen der Knoten in der erweiterten Ansicht.
* Zum Anzeigen einer Liste der geplanten Aufträge erweitern Sie den Knoten **Aufträge** und klicken dann auf **Geplant**. Die Liste der zuvor konfigurierten Aufträge mit Informationen zu den einzelnen Aufträgen wird im **Ergebnisbereich** angezeigt. 
* Zum Anzeigen einer Liste der zuletzt abgeschlossenen Aufträge erweitern Sie den Knoten **Aufträge**, und klicken Sie dann auf **Letzte 24 Stunden**. Eine Liste der Aufträge, die in den letzten 24 Stunden abgeschlossen wurden, wird im **Ergebnisbereich** angezeigt. Der **Ergebnisbereich** enthält außerdem Informationen zu jedem abgeschlossenen Auftrag.
* Zum Anzeigen einer Liste der zurzeit ausgeführten Aufträge erweitern Sie den Knoten **Aufträge**, und klicken Sie dann auf **Wird ausgeführt**. Die Liste der zurzeit ausgeführten Aufträge mit Informationen zu den einzelnen Aufträgen wird im **Ergebnisbereich** angezeigt.

## <a name="results-pane"></a>Ergebnisbereich
Der **Ergebnisbereich** ist der zentrale Bereich der StorSimple Snapshot Manager-Benutzeroberfläche. Er enthält Listen und ausführliche Statusinformationen für den im **Bereichsfenster** ausgewählten Knoten.

### <a name="example"></a>Beispiel
Zum Anzeigen des folgenden Beispiels klicken Sie im **Bereichsfenster** auf den Knoten **Volumegruppen**. Im **Ergebnisbereich** wird eine Liste von Volumegruppen mit Details zu den einzelnen Gruppen angezeigt.

![Ergebnisbereich](./media/storsimple-use-snapshot-manager/HCS_SSM_Results_pane.png) 

Sie können die im **Ergebnisbereich** angezeigten Details konfigurieren: Klicken Sie mit der rechten Maustaste im **Bereichsfenster** auf **Ansicht**, und klicken Sie dann auf **Spalten hinzufügen/entfernen**.

## <a name="actions-pane"></a>Bereich "Aktionen"
Der Bereich **Aktionen** ist der rechte Bereich der StorSimple Snapshot Manager-Benutzeroberfläche. Er enthält ein Menü mit Aktionen, die Sie für im **Bereichsfenster** oder im **Ergebnisbereich** ausgewählte Knoten, Ansichten oder Daten ausführen können. Der Bereich **Aktionen** enthält dieselben Befehle wie die Menüs **Aktion**, die für Elemente im **Bereichsfenster** und im **Ergebnisbereich** bereitstehen. Eine Beschreibung der einzelnen Aktionen finden Sie in der Tabelle im Abschnitt zum Menü **Aktion** .

### <a name="examples"></a>Beispiele
Zum Anzeigen des folgenden Beispiels erweitern Sie im **Bereichsfenster** den Knoten **Aufträge**, und klicken Sie dann auf **Geplant**. Im Bereich **Aktionen** werden die verfügbaren Aktionen für den Knoten **Geplant** angezeigt.

![Bereich "Aktionen" mit Beispiel für geplante Aufträge](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane.png) 

Zum Anzeigen weiterer Optionen erweitern Sie im **Bereichsfenster** den Knoten **Aufträge**. Klicken Sie anschließend auf **Geplant** und dann im **Ergebnisbereich** auf einen geplanten Auftrag. Im Bereich **Aktionen** werden die verfügbaren Aktionen für den geplanten Auftrag angezeigt, wie im folgenden Beispiel gezeigt.

![Bereich "Aktionen" mit Beispiel für Auftragsaktionen](./media/storsimple-use-snapshot-manager/HCS_SSM_ActionsPane_Results.png)

## <a name="keyboard-navigation-and-shortcuts"></a>Tastaturnavigation und Tastenkombinationen
Der StorSimple Snapshot Manager bietet eine Reihe von Features zur Barrierefreiheit von Windows und der Microsoft Management Console (MMC). Darüber hinaus stellt er einige Features zur Tastaturnavigation und Tastenkombinationen bereit, die speziell für den StorSimple Snapshot Manager gelten. Eine Beschreibung dieser Features finden Sie in den folgenden Abschnitten.

* [Tastaturnavigationstasten](#keyboard-navigation-keys) 
* [Tastenkombinationen auf der Menüleiste](#menu-bar-shortcut-keys) 
* [Tastenkombinationen im Bereichsfenster](#scope-pane-shortcut-keys) 

### <a name="keyboard-navigation-keys"></a>Tastaturnavigationstasten
In der folgenden Tabelle werden die Tasten beschrieben, mit denen Sie auf der Benutzeroberfläche des StorSimple Snapshot Managers navigieren können. 

| Navigationstaste | Aktion |
|:--- |:--- |
| NACH-UNTEN-TASTE |Verwenden Sie die NACH-UNTEN-TASTE, um vertikal zum nächsten Element in einem Menü oder Bereich zu wechseln. |
| EINGABETASTE |Drücken Sie die EINGABETASTE, um eine Aktion abzuschließen und dann mit dem nächsten Schritt fortzufahren. Sie können z.B. die EINGABETASTE drücken, um **Weiter**, **OK** oder **Erstellen** auszuwählen und anschließend mit dem nächsten Schritt in einem Assistenten fortzufahren. |
| Esc |Drücken Sie die ESC-Taste, um ein Menü zu schließen oder um eine Seite abzubrechen und zu schließen. |
| F1 |Drücken Sie F1, um ein Hilfethema zum derzeit aktiven Fenster anzuzeigen. |
| F5 |Drücken Sie F5, um einen Knoten zu aktualisieren. |
| F6 |Drücken Sie F6, um vom **Bereichsfenster** zum **Ergebnisbereich** zu wechseln. |
| F10 |Drücken Sie F10, um auf die Menüleiste zu wechseln. |
| NACH-LINKS-TASTE |Verwenden Sie die NACH-LINKS-TASTE, um horizontal von einer Menüleistenoption zur vorherigen Option zu wechseln. Wenn Sie auf der Menüleiste zum vorherigen Element wechseln, wird das Menü "Aktion" (oder das Kontextmenü) für das vorherige Element angezeigt. |
| NACH-RECHTS-TASTE |Verwenden Sie die NACH-RECHTS-TASTE, um horizontal von einer Menüleistenoption zur nächsten Option zu wechseln. Wenn Sie auf der Menüleiste zum nächsten Element wechseln, wird das Menü "Aktion" (oder das Kontextmenü) für das nächste Element angezeigt. |
| TAB-TASTE |Verwenden Sie die TAB-TASTE, um zum nächsten Bereich in der Konsole oder zur nächsten Auswahl oder dem nächsten Textfeld auf einer Seite zu wechseln. |
| NACH-OBEN-TASTE |Verwenden Sie die NACH-OBEN-TASTE, um vertikal zum vorherigen Element in einem Menü oder Bereich zu wechseln. |

### <a name="menu-bar-shortcut-keys"></a>Tastenkombinationen auf der Menüleiste
In der folgenden Tabelle werden die Tastenkombinationen für die Menüleiste beschrieben. Nachdem Sie die Tastenkombination gedrückt haben und das Menü geöffnet wurde, können Sie die Menütastenkombinationen verwenden (unterstrichene Tasten im Menü). Weitere Informationen zur Menüleiste finden Sie unter [Menüleiste](#menu-bar).

| Tastenkombination | Ergebnis | Tastenkombination im Menü | Ergebnis |
|:--- |:--- |:--- |:--- |
| ALT+F |Öffnet das Menü **Datei** . |N |Öffnet eine neue Konsoleninstanz. |
|  |O |Öffnet die Seite **Verwaltung** . | |
|  |E |Speichert die StorSimple Snapshot Manager-Konsole. | |
|  |Ein |Öffnet die Seite **Speichern unter** . | |
|  |M |Öffnet die Seite **Snap-In hinzufügen/entfernen** . | |
|  |P |Öffnet die Seite **Optionen** . | |
|  |H |Öffnet die Onlinehilfe. | |
| ALT+A |Öffnet das Menü **Aktion** . |I |Aktiviert bzw. deaktiviert die Importanzeigeoption. |
|  |W |Öffnet eine neue StorSimple Snapshot Manager-Konsole. | |
|  |F |Aktualisiert die StorSimple Snapshot Manager-Konsole. | |
|  |L |Öffnet die Seite **Liste exportieren** . | |
|  |H |Öffnet die Onlinehilfe. | |
| ALT+V |Öffnet das Menü **Ansicht** . |Ein |Öffnet die Seite **Spalten hinzufügen/entfernen** . |
|  |U |Öffnet die Seite **Ansicht anpassen** . | |
| ALT+O |Öffnet das Menü **Favoriten** . |Ein |Öffnet die Seite **Zu Favoriten hinzufügen** . |
|  |O |Öffnet die Seite **Favoriten verwalten** . | |
| ALT+W |Öffnet das Menü **Fenster** . |N |Öffnet ein weiteres StorSimple Snapshot Manager-Fenster. |
|  |C |Zeigt alle offenen Konsolenfenster überlappend an. | |
|  |T |Zeigt alle offenen Konsolenfenster in einem Raster an. | |
|  |I |Ordnet die Symbole in einer horizontalen Zeile am unteren Bildschirmrand an. | |
| ALT+H |Öffnet das Menü **Hilfe** . |H |Öffnet die Onlinehilfe. |
|  |T |Öffnet die Webseite von Microsoft TechNet TechCenter. | |
|  |Ein |Öffnet die Seite **Info** . | |

### <a name="scope-pane-shortcut-keys"></a>Tastenkombinationen im Bereichsfenster
In den folgenden Tabellen werden die Tastenkombinationen für die einzelnen Knoten im **Bereichsfenster** aufgelistet. 

* [Tastenkombinationen für den Knoten "Geräte"](#devices-node-shortcut-keys)
* [Tastenkombinationen für den Knoten "Volumes"](#volumes-node-shortcut-keys)
* [Tastenkombinationen für den Knoten "Volumegruppen"](#volume-groups-node-shortcut-keys)
* [Tastenkombinationen für den Knoten "Sicherungsrichtlinien"](#backup-policies-node-shortcut-keys)
* [Tastenkombinationen für den Knoten "Sicherungskatalog"](#backup-catalog-node-shortcut-keys)
* [Tastenkombinationen für den Knoten "Aufträge"](#jobs-node-shortcut-keys)

#### <a name="devices-node-shortcut-keys"></a>Tastenkombinationen für den Knoten "Geräte"
| Tastenkombination im Menü | Ergebnis |
|:--- |:--- |
| C |Öffnet die Seite **Gerät konfigurieren** . |
| D |Aktualisiert die Liste der Geräte und die Gerätedetails. |
| V |Öffnet das Menü **Ansicht** . |
| W |Öffnet eine neue StorSimple Snapshot Manager-Konsole mit Fokus auf dem Knoten **Details** . |
| F |Aktualisiert die StorSimple Snapshot Manager-Konsole. |
| L |Öffnet die Seite **Liste exportieren** . |
| H |Öffnet die Onlinehilfe. |

#### <a name="volumes-node-shortcut-keys"></a>Tastenkombinationen für den Knoten "Volumes"
| Tastenkombination im Menü | Ergebnis |
|:--- |:--- |
| V |Aktualisiert die Liste der Volumes. |
| V (zweimal drücken) |Öffnet das Menü **Ansicht** . |
| W |Öffnet eine neue StorSimple Snapshot Manager-Konsole mit Fokus auf dem Knoten **Volumes** . |
| F |Aktualisiert die StorSimple Snapshot Manager-Konsole. |
| L |Öffnet die Seite **Liste exportieren** . |
| H |Öffnet die Onlinehilfe. |

#### <a name="volume-groups-node-shortcut-keys"></a>Tastenkombinationen für den Knoten "Volumegruppen"
| Tastenkombination im Menü | Ergebnis |
|:--- |:--- |
| G |Öffnet die Seite **Volumegruppe erstellen** . |
| V |Öffnet das Menü **Ansicht** . |
| W |Öffnet eine neue StorSimple Snapshot Manager-Konsole mit Fokus auf dem Knoten **Volumegruppen** . |
| F |Aktualisiert die StorSimple Snapshot Manager-Konsole. |
| L |Öffnet die Seite **Liste exportieren** . |
| H |Öffnet die Onlinehilfe. |

#### <a name="backup-policies-node-shortcut-keys"></a>Tastenkombinationen für den Knoten "Sicherungsrichtlinien"
| Tastenkombination im Menü | Ergebnis |
|:--- |:--- |
| B |Öffnet die Seite **Richtlinie erstellen** . |
| V |Öffnet das Menü **Ansicht** . |
| W |Öffnet eine neue StorSimple Snapshot Manager-Konsole mit Fokus auf dem Knoten **Volumegruppen** . |
| F |Aktualisiert die StorSimple Snapshot Manager-Konsole. |
| L |Öffnet die Seite **Liste exportieren** . |
| H |Öffnet die Onlinehilfe. |

#### <a name="backup-catalog-node-shortcut-keys"></a>Tastenkombinationen für den Knoten "Sicherungskatalog"
| Tastenkombination im Menü | Ergebnis |
|:--- |:--- |
| W |Öffnet eine neue StorSimple Snapshot Manager-Konsole mit Fokus auf dem Knoten **Volumegruppen** . |
| F |Aktualisiert die StorSimple Snapshot Manager-Konsole. |
| H |Öffnet die Onlinehilfe. |

#### <a name="jobs-node-shortcut-keys"></a>Tastenkombinationen für den Knoten "Aufträge"
| Tastenkombination im Menü | Ergebnis |
|:--- |:--- |
| V |Öffnet das Menü **Ansicht** . |
| W |Öffnet eine neue StorSimple Snapshot Manager-Konsole mit Fokus auf dem Knoten **Aufträge** . |
| F |Aktualisiert die StorSimple Snapshot Manager-Konsole. |
| L |Öffnet die Seite **Liste exportieren** . |
| H |Öffnet die Onlinehilfe. |

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über das [Verwenden von StorSimple Snapshot Manager zum Verwalten der StorSimple-Lösung](storsimple-snapshot-manager-admin.md).
* Erfahren Sie, wie Sie [mit dem StorSimple Snapshot Manager Verbindungen mit Geräten herstellen und diese verwalten](storsimple-snapshot-manager-manage-devices.md).