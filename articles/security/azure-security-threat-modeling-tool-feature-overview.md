---
title: Microsoft Threat Modeling Tool – Azure | Microsoft-Dokumentation
description: Erfahren Sie mehr über alle Funktionen im Threat Modeling Tool
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 601f3bf05388406c8f96a7351f7fb3aa4de2650a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60588795"
---
# <a name="threat-modeling-tool-feature-overview"></a>Übersicht über die Funktionen des Threat Modeling Tools

Das Threat Modeling Tool kann Sie bei der Bedrohungsmodellierung unterstützen. Eine allgemeine Einführung in das Tool finden Sie unter [Erste Schritte mit dem Threat Modeling Tool](./azure-security-threat-modeling-tool-getting-started.md).

> [!NOTE]
>Da das Threat Modeling Tool häufig aktualisiert wird, sollten Sie diesen Leitfaden oft auf die neuesten Features und Verbesserungen überprüfen.

Um eine leere Seite zu öffnen, wählen Sie **Modell erstellen** aus.

![Leere Seite](./media/azure-security-threat-modeling-tool-feature-overview/tmtstart.png)

In dem von unserem Team im Artikel [Erste Schritte](./azure-security-threat-modeling-tool-getting-started.md) erstellten Bedrohungsmodell finden Sie die Features, die das Tool derzeit zu bieten hat.

![Grundlegendes Bedrohungsmodell](./media/azure-security-threat-modeling-tool-feature-overview/basictmt.png)

## <a name="navigation"></a>Navigation

Bevor wir die integrierten Features erläutern, wollen wir die wichtigsten Komponenten des Tools betrachten.

### <a name="menu-items"></a>Menüelemente

Die Benutzeroberfläche ist ähnlich wie bei anderen Microsoft-Produkten. Betrachten wir die Menüelemente der obersten Ebene.

![Menüelemente](./media/azure-security-threat-modeling-tool-feature-overview/menuitems.png)

| Bezeichnung                               | Details      |
| --------------------------------------- | ------------ |
| **Datei** | <ul><li>Dateien öffnen, speichern und schließen</li><li>Bei OneDrive-Konten an- und abmelden</li><li>Links teilen (Anzeigen und Bearbeiten)</li><li>Dateiinformationen anzeigen</li><li>Neue Vorlage auf vorhandene Modelle anwenden</li></ul> |
| **Bearbeiten** | Aktionen rückgängig machen und wiederholen sowie Kopieren, Einfügen und Löschen |
| **Ansicht** | <ul><li>Zwischen den Ansichten **Analyse** und **Entwurf** wechseln</li><li>Geschlossene Fenster öffnen (z.B. Schablonen, Elementeigenschaften und Meldungen)</li><li>Layout auf die Standardeinstellungen zurücksetzen</li></ul> |
| **Diagramm** | Diagramme hinzufügen und löschen und durch die Registerkarten von Diagrammen navigieren |
| **Berichte** | HTML-Berichte zum Teilen mit anderen erstellen |
| **Hilfe** | Anleitungen für die Nutzung des Tools suchen |

Die Symbole sind Verknüpfungen für die Menüs der obersten Ebene:

| Symbol                               | Details      |
| --------------------------------------- | ------------ |
| **Öffnen** | Öffnet eine neue Datei. |
| **Speichern** | Speichert die aktuelle Datei. |
| **Entwurf** | Öffnet die **Entwurfsansicht**, in der Sie Modelle erstellen können. |
| **Analysieren** | Zeigt generierte Bedrohungen und deren Eigenschaften an. |
| **Diagramm hinzufügen** | Fügt ein neues Diagramm hinzu (vergleichbar mit neuen Registerkarten in Excel). |
| **Diagramm löschen** | Löscht das aktuelle Diagramm. |
| **Kopieren/Ausschneiden/Einfügen** | Kopiert Elemente, schneidet Elemente aus bzw. fügt Elemente ein. |
| **Rückgängig/Wiederholen** | Macht Aktionen rückgängig oder wiederholt sie. |
| **Vergrößern/Verkleinern** | Dient zum Vergrößern und Verkleinern des Diagramms für eine bessere Ansicht. |
| **Feedback** | Öffnet das MSDN-Forum. |

### <a name="canvas"></a>Canvas

Dies ist der Bereich, in dem Sie gezogene Elemente ablegen. Ziehen und Ablegen ist der schnellste und effizienteste Weg, Modelle zu erstellen. Sie können auch mit der rechten Maustaste klicken und Elemente im Menü auswählen, um generische Versionen von Elementen hinzuzufügen, wie unten gezeigt:

#### <a name="drop-the-stencil-on-the-canvas"></a>Ablegen der Schablone im Zeichenbereich

![Ablegen im Zeichenbereich](./media/azure-security-threat-modeling-tool-feature-overview/canvasdrop1.png)

#### <a name="select-the-stencil"></a>Auswählen der Schablone

![Elementeigenschaften](./media/azure-security-threat-modeling-tool-feature-overview/canvasdrop2.png)

### <a name="stencils"></a>Schablonen

Die Ihnen zur Verfügung stehenden Schablonen hängen von der ausgewählten Vorlage ab. Wenn Sie die richtigen Elemente nicht finden können, verwenden Sie eine andere Vorlage. Stattdessen können Sie auch eine Vorlage Ihren Anforderungen entsprechend ändern. Im Allgemeinen finden Sie eine Kombination von Kategorien wie die folgenden:

| Schablonenname                               | Details      |
| --------------------------------------- | ------------ |
| **Prozess** | Anwendungen, Browser-Plug-Ins, Threads, virtuelle Computer |
| **Externe Einheit** | Authentifizierungsanbieter, Browser, Benutzer, Webanwendungen |
| **Datenspeicher** | Cache, Speicher, Konfigurationsdateien, Datenbanken, Registrierung |
| **Datenfluss** | Binärdatei, ALPC, HTTP, HTTPS/TLS/SSL, IOCTL, IPsec, Named Pipe, RPC/DCOM, SMB, UDP |
| **Vertrauensstellungsgrenze** | Unternehmensnetzwerke, Internet, Computer, Sandkasten, Benutzer-/Kernelmodus |

### <a name="notesmessages"></a>Notizen/Meldungen

| Komponente                               | Details      |
| --------------------------------------- | ------------ |
| **Meldungen** | Interne Toollogik zum Benachrichtigen von Benutzern, wenn ein Fehler vorliegt, wie z.B. kein Datenfluss zwischen Elementen. |
| **Hinweise** | Während des gesamten Entwurfs- und Überprüfungsprozesses werden der Datei von Entwicklerteams manuelle Notizen hinzugefügt. |

### <a name="element-properties"></a>Elementeigenschaften

Die Elementeigenschaften variieren je nach den ausgewählten Elementen. Abgesehen von Vertrauensstellungsgrenzen enthalten alle anderen Elemente drei allgemeine Optionen:

| Elementeigenschaft                               | Details      |
| --------------------------------------- | ------------ |
| **Name** | Nützlich zum Benennen Ihrer Prozesse, Speicher, Einheiten und Flüsse, damit diese einfach erkannt werden. |
| **Außerhalb des gültigen Bereichs** | Falls ausgewählt, wird das Element aus der Bedrohungsgenerierungsmatrix entfernt (nicht empfohlen). |
| **Grund für „Außerhalb des gültigen Bereichs“** | Feld zur Begründung, um Benutzer wissen zu lassen, warum diese Option gewählt wurde. |

Eigenschaften werden in jeder Elementkategorie geändert. Wählen Sie die einzelnen Elemente aus, um die verfügbaren Optionen zu überprüfen. Alternativ können Sie die Vorlage öffnen, um weitere Informationen zu erhalten. Lassen Sie uns die Features betrachten.

## <a name="welcome-screen"></a>Bildschirm „Willkommen“

Wenn Sie die App zu öffnen, wird der Bildschirm **Willkommen** angezeigt.

### <a name="open-a-model"></a>Öffnen eines Modells

Zeigen Sie auf **Modell öffnen** um zwei Optionen anzuzeigen: **Auf diesem Computer öffnen** und **Auf OneDrive öffnen**. Mit der ersten Option wird der Bildschirm **Datei öffnen** angezeigt. Die zweite Option führt Sie durch den Anmeldevorgang für OneDrive. Nach erfolgreicher Authentifizierung können Sie Ordner und Dateien auswählen.

![Modell öffnen](./media/azure-security-threat-modeling-tool-feature-overview/openmodel.png)

![Auf Computer oder OneDrive öffnen](./media/azure-security-threat-modeling-tool-feature-overview/openmodel2.png)

### <a name="feedback-suggestions-and-issues"></a>Feedback, Vorschläge und Probleme

Wenn Sie **Feedback, Vorschläge und Probleme** auswählen, gelangen Sie zum MSDN-Forum für SDL-Tools. Sie können lesen, was andere Benutzer zum Tool zu sagen haben, sowie Problemumgehungen und neue Ideen finden.

![Feedback](./media/azure-security-threat-modeling-tool-feature-overview/feedback.png)

## <a name="design-view"></a>Entwurfsansicht

Beim Öffnen oder Erstellen eines neuen Modells wird die **Entwurfsansicht** geöffnet.

### <a name="add-elements"></a>Hinzufügen von Elementen

Sie können dem Raster Elemente auf zwei Arten hinzufügen:

- **Drag & Drop**: Ziehen Sie das gewünschte Element in das Raster. Verwenden Sie dann die Elementeigenschaften, um zusätzliche Informationen anzugeben.
- **Klicken mit der rechten Maustaste**: Klicken Sie mit der rechten Maustaste auf das Raster, und wählen Sie Optionen im Dropdownmenü aus. Auf dem Bildschirm wird eine generische Darstellung des ausgewählten Elements angezeigt.

### <a name="connect-elements"></a>Verbinden von Elementen

Sie können Elemente auf zwei Arten verbinden:

- **Drag & Drop**: Ziehen Sie den gewünschten Datenfluss in das Raster, und verbinden Sie beide Enden mit den entsprechenden Elementen.
- **Klicken bei gedrückter UMSCHALTTASTE**: Klicken Sie auf das erste Element (Daten werden gesendet), halten Sie die UMSCHALTTASTE gedrückt, und wählen Sie dann das zweite Element aus (Daten werden empfangen). Klicken Sie mit der rechten Maustaste, und wählen Sie **Verbinden** aus. Wenn Sie einen bidirektionalen Datenfluss verwenden, ist die Reihenfolge nicht so wichtig.

### <a name="properties"></a>Eigenschaften

 Um die Eigenschaften anzuzeigen, die für Schablonen geändert werden können, wählen Sie die Schablone aus, und das Eigenschaftenfenster wird entsprechend aufgefüllt. Das folgende Beispiel zeigt das Fenster vor und nach dem Ziehen der Schablone **Datenbank** auf das Diagramm:

#### <a name="before"></a>Vorher

![Vorher](./media/azure-security-threat-modeling-tool-feature-overview/properties1.png)

#### <a name="after"></a>Nachher

![Nachher](./media/azure-security-threat-modeling-tool-feature-overview/properties2.png)

### <a name="messages"></a>Meldungen

Wenn Sie ein Bedrohungsmodell erstellen und vergessen, Datenflüsse mit Elementen zu verbinden, werden Sie benachrichtigt. Sie können die Meldung ignorieren oder die Anweisungen zum Beheben des Problems befolgen. 

![Meldungen](./media/azure-security-threat-modeling-tool-feature-overview/messages.png)

### <a name="notes"></a>Notizen

Um dem Diagramm Notizen hinzuzufügen, wechseln Sie von der Registerkarte **Meldungen** zur Registerkarte **Notizen**.

## <a name="analysis-view"></a>Analyseansicht

Nachdem Sie das Diagramm erstellt haben, wählen Sie auf der Symbolleiste das Symbol **Analyse** (die Lupe) aus, um zur **Analyseansicht** zu wechseln.

![Analyseansicht](./media/azure-security-threat-modeling-tool-feature-overview/analysisview.png)

### <a name="generated-threat-selection"></a>Generierte Bedrohungsauswahl

Wenn Sie eine Bedrohung auswählen, können Sie drei verschiedene Funktionen verwenden:

| Feature                               | Information      |
| --------------------------------------- | ------------ |
| **Markierung „Gelesen“** | <p>Die Bedrohung ist als „Gelesen“ markiert, sodass Sie leichter den Überblick über die bereits überprüften Elemente behalten.</p><p>![Markierung „Gelesen/Nicht gelesen“](./media/azure-security-threat-modeling-tool-feature-overview/readmode.png)</p> |
| **Interaktionsfokus** | <p>Die zu einer Bedrohung gehörende Interaktion wird hervorgehoben.</p><p>![Interaktionsfokus](./media/azure-security-threat-modeling-tool-feature-overview/interactionfocus.png)</p> |
| **Bedrohungseigenschaften** | <p>Im Fenster **Bedrohungseigenschaften** werden zusätzliche Informationen über die Bedrohung angezeigt.</p><p>![Bedrohungseigenschaften](./media/azure-security-threat-modeling-tool-feature-overview/threatproperties.png)</p> |

### <a name="priority-change"></a>Ändern der Priorität

Sie können die Prioritätsstufe jeder generierten Bedrohung ändern. Unterschiedliche Farben erleichtern die Unterscheidung zwischen Bedrohungen hoher, mittlerer und niedriger Priorität.

![Ändern der Priorität](./media/azure-security-threat-modeling-tool-feature-overview/prioritychange.png)

### <a name="threat-properties-editable-fields"></a>Editierbare Felder von Bedrohungseigenschaften

Wie in der vorherigen Abbildung dargestellt, können Sie die vom Tool generierten Informationen ändern. Sie können auch bestimmten Feldern, z.B. dem Feld für die Begründung, Informationen hinzufügen. Diese Felder werden durch die Vorlage generiert. Wenn Sie weitere Informationen für die einzelnen Bedrohungen benötigen, können Sie Änderungen vornehmen.

![Bedrohungseigenschaften](./media/azure-security-threat-modeling-tool-feature-overview/threatproperties.png)

## <a name="reports"></a>Berichte

Nachdem Sie das Ändern von Prioritäten und Aktualisieren des Status der einzelnen generierten Bedrohungen abgeschlossen haben, können Sie die Datei speichern und/oder einen Bericht ausgeben. Wechseln Sie zu **Bericht** > **Vollständigen Bericht erstellen**. Benennen Sie den Bericht. Anschließend wird ein Bericht wie etwa in der folgenden Abbildung angezeigt:

![Bericht](./media/azure-security-threat-modeling-tool-feature-overview/report.png)

## <a name="next-steps"></a>Nächste Schritte

- Senden Sie Ihre Fragen, Kommentare und Probleme an tmtextsupport@microsoft.com. **[Laden Sie das Threat Modeling Tool herunter](https://aka.ms/threatmodelingtool)** , um die ersten Schritte zu unternehmen.
- Informationen dazu, wie Sie eine Vorlage zur Community beitragen können, finden Sie auf unserer [GitHub](https://github.com/Microsoft/threat-modeling-templates)-Seite.