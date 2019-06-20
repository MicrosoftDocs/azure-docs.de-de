---
title: Benutzerdefinierte Felder in Azure Monitor | Microsoft-Dokumentation
description: Mit dem Azure Monitor-Feature „Benutzerdefinierte Felder“ können Sie auf der Grundlage von Datensätzen in einem Log Analytics-Arbeitsbereich eigene durchsuchbare Felder erstellen, um die Eigenschaften gesammelter Datensätze zu erweitern.  Dieser Artikel beschreibt die Erstellung eines benutzerdefinierten Felds und enthält eine ausführliche exemplarische Vorgehensweise mit einem Beispielereignis.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 31572b51-6b57-4945-8208-ecfc3b5304fc
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2019
ms.author: bwren
ms.openlocfilehash: 974a3391c592a1caf7bdcc6d9e01032f0c73aaa6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61461817"
---
# <a name="create-custom-fields-in-a-log-analytics-workspace-in-azure-monitor"></a>Erstellen von benutzerdefinierten Feldern in einem Log Analytics-Arbeitsbereich in Azure Monitor

> [!NOTE]
> In diesem Artikel wird beschrieben, wie Sie Textdaten in einem Log Analytics-Arbeitsbereich beim Sammeln analysieren können. Es gibt Vorteile beim Analysieren von Textdaten in einer Abfrage, nachdem sie gesammelt wurden, wie in [Analysieren von Textdaten in Azure Monitor](../log-query/parse-text.md) beschrieben.

Mit dem Feature **Benutzerdefinierte Felder** von Azure Monitor können Sie vorhandene Datensätze in Ihrem Log Analytics-Arbeitsbereich durch eigene durchsuchbare Felder erweitern.  Benutzerdefinierte Felder werden automatisch auf der Grundlage von Daten aufgefüllt, die aus anderen Eigenschaften im gleichen Datensatz extrahiert wurden.

![Übersicht](media/custom-fields/overview.png)

Der folgende Beispieldatensatz enthält beispielsweise weitere hilfreiche Daten in der Ereignisbeschreibung. Die Extraktion dieser Daten in eine separate Eigenschaft ermöglicht Aktionen wie Sortieren und Filtern.

![Beispielextraktion](media/custom-fields/sample-extract.png)

> [!NOTE]
> In der Vorschauversion können Sie in Ihrem Arbeitsbereich maximal 100 benutzerdefinierte Felder verwenden.  Diese Obergrenze wird erhöht, wenn das Feature allgemein verfügbar wird.

## <a name="creating-a-custom-field"></a>Erstellen eines benutzerdefinierten Felds
Wenn Sie ein benutzerdefiniertes Feld erstellen, muss Log Analytics wissen, mit welchen Daten dessen Wert aufgefüllt werden soll.  Zur schnellen Ermittlung dieser Daten kommt eine von Microsoft Research entwickelte Technologie namens FlashExtract zum Einsatz.  Die zu extrahierenden Daten werden dabei von Azure Monitor anhand von bereitgestellten Beispielen ermittelt, sodass Sie keine expliziten Anweisungen angeben müssen.

In den folgenden Abschnitten wird die Vorgehensweise zum Erstellen eines benutzerdefinierten Felds erläutert.  Am Ende dieses Artikels befindet sich eine exemplarische Vorgehensweise für eine Beispielextraktion.

> [!NOTE]
> Das benutzerdefinierte Feld wird aufgefüllt, wenn dem Log Analytics-Arbeitsbereich Datensätze hinzugefügt werden, die den angegebenen Kriterien entsprechen. Das Feld ist also nur für Datensätze verfügbar, die nach der Erstellung des benutzerdefinierten Felds gesammelt werden.  Das benutzerdefinierte Feld wird nicht zu Datensätzen hinzugefügt, die zum Zeitpunkt der Felderstellung bereits im Datenspeicher vorhanden sind.
> 

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>Schritt 1: Angeben der Datensätze, die über das benutzerdefinierte Feld verfügen sollen
Als Erstes müssen die Datensätze angegeben werden, die über das benutzerdefinierte Feld verfügen sollen.  Hierzu führen Sie zunächst eine [Standard-Protokollabfrage](../log-query/log-query-overview.md) durch und wählen dann einen Datensatz aus, der Azure Monitor als Modell für die Ermittlung dient.  Wenn Sie angeben, dass Sie Daten in ein benutzerdefiniertes Feld extrahieren möchten, wird der **Feldextraktions-Assistent** geöffnet, in dem Sie die Kriterien überprüfen und anpassen können.

1. Wechseln Sie zu **Protokolle**, und verwenden Sie eine [Abfrage zum Abrufen der Datensätze](../log-query/log-query-overview.md), die über das benutzerdefinierte Feld verfügen sollen.
2. Wählen Sie einen Datensatz aus, der Log Analytics als Modell für die Extraktion von Daten zur Auffüllung des benutzerdefinierten Felds dienen soll.  Nachdem Sie die Daten angegeben haben, die aus diesem Datensatz extrahiert werden sollen, ermittelt Log Analytics auf der Grundlage dieser Informationen die Logik, mit der das benutzerdefinierte Felds für alle ähnlichen Datensätze aufgefüllt werden kann.
3. Erweitern Sie die Datensatzeigenschaften, klicken Sie auf die Auslassungspunkte links neben der oberen Eigenschaft des Datensatzes, und wählen Sie **Felder aus „ “ extrahieren** aus.
4. Der **Feldextraktions-Assistent** wird geöffnet, und der ausgewählte Datensatz wird in der Spalte **Hauptbeispiel** angezeigt.  Das benutzerdefinierte Feld wird für Datensätze mit den gleichen Werten in den ausgewählten Eigenschaften definiert.  
5. Falls die Auswahl noch nicht ganz Ihren Vorstellungen entspricht, können Sie weitere Felder auswählen, um die Kriterien einzugrenzen.  Wenn Sie die Feldwerte für die Kriterien ändern möchten, müssen Sie den Vorgang abbrechen und einen anderen Datensatz auswählen, der den gewünschten Kriterien entspricht.

### <a name="step-2---perform-initial-extract"></a>Schritt 2: Ausführen der ersten Extraktion
Nach Angabe der Datensätze, die über das benutzerdefinierte Feld verfügen sollen, müssen die zu extrahierenden Daten angegeben werden.  Auf der Grundlage dieser Informationen ermittelt Log Analytics dann ähnliche Muster in ähnlichen Datensätzen.  Im nächsten Schritt können Sie die Ergebnisse überprüfen und weitere Details angeben, die Log Analytics bei der Analyse berücksichtigen soll.

1. Markieren Sie im Beispieldatensatz den Text, mit dem das benutzerdefinierte Feld aufgefüllt werden soll.  Daraufhin erscheint ein Dialogfeld, in dem Sie einen Namen und einen Datentyp für das Feld angeben und die erste Extraktion durchführen können.  Die Zeichen **\_CF** werden automatisch angefügt.
2. Klicken Sie auf **Extrahieren** , um eine Analyse der gesammelten Datensätze durchzuführen.  
3. In den Abschnitten **Zusammenfassung** und **Suchergebnisse** werden die Ergebnisse der Extraktion zur Überprüfung angezeigt.  **Zusammenfassung** enthält die Kriterien, die zur Ermittlung der Datensätze verwendet wurden, sowie die Anzahl der einzelnen ermittelten Datenwerte.  **Suchergebnisse** enthält eine detaillierte Liste mit Datensätzen, die den Kriterien entsprechen.

### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>Schritt 3: Überprüfen der Extraktion und Erstellen des benutzerdefinierten Felds
Im Anschluss an die erste Extraktion zeigt Log Analytics die Ergebnisse an, die auf der Grundlage bereits gesammelter Daten ermittelt wurden.  Wenn die Ergebnisse Ihren Vorstellungen entsprechen, können Sie das benutzerdefinierte Feld ohne weitere Schritte erstellen.  Andernfalls können Sie die Ergebnisse optimieren, damit Log Analytics seine Logik verbessern kann.

1. Sollte die erste Extraktion falsche Werte enthalten, klicken Sie neben einem falschen Datensatz auf das **Bearbeitungssymbol**, und wählen Sie **Diese Markierung ändern** aus, um die Auswahl zu ändern.
2. Der Eintrag wird in den Abschnitt **Zusätzliche Beispiele** kopiert, der sich unter dem Abschnitt **Hauptbeispiel** befindet.  Hier können Sie die Markierung anpassen, um Log Analytics mitzuteilen, wie die Auswahl hätte aussehen sollen.
3. Klicken Sie auf **Extrahieren** , um alle vorhandenen Datensätze auf der Grundlage dieser neuen Informationen zu untersuchen.  Dadurch ändern sich unter Umständen auch die Ergebnisse anderer Datensätze.
4. Nehmen Sie weitere Korrekturen vor, bis für alle Datensätze in der Extraktion genau die Daten angeben werden, mit denen das neue benutzerdefinierte Feld aufgefüllt werden soll.
5. Klicken Sie auf **Save Extract** (Extraktion speichern), wenn Sie mit den Ergebnissen zufrieden sind.  Das benutzerdefinierte Feld ist nun zwar definiert, wird aber noch keinen Datensätzen hinzugefügt.
6. Warten Sie, bis neue, den angegebenen Kriterien entsprechende Datensätze gesammelt wurden, und führen Sie die Protokollsuche dann erneut aus. Die neuen Datensätze verfügen nun über das benutzerdefinierte Feld.
7. Das benutzerdefinierte Feld kann wie jede andere Datensatzeigenschaft verwendet werden.  Sie können damit Daten aggregieren und gruppieren und sogar neue Insights generieren.

## <a name="viewing-custom-fields"></a>Anzeigen benutzerdefinierter Felder
Sie können im Azure-Portal über das Menü **Erweiterte Einstellungen** des Log Analytics-Arbeitsbereichs eine Liste aller benutzerdefinierten Felder in Ihrer Verwaltungsgruppe anzeigen.  Wählen Sie **Daten** und anschließend **Benutzerdefinierte Felder** aus, um eine Liste mit allen benutzerdefinierten Feldern in Ihrem Arbeitsbereich anzuzeigen.  

![Benutzerdefinierte Felder](media/custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Entfernen eines benutzerdefinierten Felds
Benutzerdefinierte Felder können auf zwei Arten entfernt werden:  Sie können in der weiter oben beschriebenen Liste für jedes Feld die Option **Entfernen** verwenden.  Alternativ können Sie einen Datensatz abrufen und auf die Schaltfläche links neben dem Feld klicken.  Das Menü enthält eine Option zum Entfernen des benutzerdefinierten Felds.

## <a name="sample-walkthrough"></a>Exemplarische Vorgehensweise
Der folgende Abschnitt enthält ein vollständiges Beispiel für die Erstellung eines benutzerdefinierten Felds.  In diesem Beispiel wird in Windows-Ereignissen, die auf eine Zustandsänderung bei einem Dienst hindeuten, der Dienstname extrahiert.  Als Grundlage dienen Ereignisse, die auf Windows-Computern beim Start des Systems vom Dienststeuerungs-Manager erstellt werden.  Wenn Sie dieses Beispiel nachvollziehen möchten, müssen Sie [Informationsereignisse für das Systemprotokoll sammeln](data-sources-windows-events.md).

Wir geben die folgende Abfrage ein, die alle Ereignisse des Dienststeuerungs-Managers mit der Ereignis-ID 7036 zurückgibt. (Dieses Ereignis gibt an, dass ein Dienst gestartet oder beendet wird.)

![Abfragen](media/custom-fields/query.png)

Anschließend wählen wir alle Datensätze mit der Ereignis-ID 7036 aus und erweitern sie.

![Quelldatensatz](media/custom-fields/source-record.png)

Benutzerdefinierte Felder werden durch Klicken auf die Auslassungspunkte neben der oberen Eigenschaft definiert.

![Felder extrahieren](media/custom-fields/extract-fields.png)

Der **Feldextraktions-Assistent** wird geöffnet, und in der Spalte **Hauptbeispiel** sind die Felder **EventLog** und **EventID** ausgewählt.  Das benutzerdefinierte Feld wird also für Ereignisse aus dem Systemprotokoll mit der Ereignis-ID 7036 definiert.  Diese Angaben sind ausreichend, und es müssen keine weiteren Felder ausgewählt werden.

![Main Example](media/custom-fields/main-example.png)

Wir markieren den Namen des Diensts in der **RenderedDescription**-Eigenschaft und verwenden **Service**, um den Dienstnamen anzugeben.  Der Name des benutzerdefinierten Felds lautet **Service_CF**. Der Feldtyp ist in diesem Fall eine Zeichenfolge, sodass wir ihn unverändert lassen können.

![Feldtitel](media/custom-fields/field-title.png)

Wir stellen fest, dass der Dienstname nicht bei allen Datensätzen korrekt ermittelt wird.   In den **Suchergebnissen** sehen wir, dass bei **WMI-Leistungsadapter** ein Teil des Namens nicht ausgewählt wurde.  Die **Zusammenfassung** zeigt, dass ein Datensatz **Modules Installer** anstelle von **Windows Modules Installer** identifiziert hat.  

![Suchergebnisse](media/custom-fields/search-results-01.png)

Kümmern wir uns zunächst um den Datensatz **WMI Performance Adapter** .  Wir klicken auf das Bearbeitungssymbol und anschließend auf **Modify this highlight**(Diese Markierung ändern).  

![Markierung ändern](media/custom-fields/modify-highlight.png)

Wir erweitern die Markierung um das Wort **WMI** und wiederholen dann den Extraktionsvorgang.  

![Zusätzliches Beispiel](media/custom-fields/additional-example-01.png)

Wir sehen, dass Log Analytics auf der Grundlage dieser Informationen nicht nur die Einträge für **WMI-Leistungsadapter**, sondern auch die Einträge für **Windows Modules Installer** korrigiert hat.

![Suchergebnisse](media/custom-fields/search-results-02.png)

Wir können jetzt eine Abfrage ausführen, die überprüft, ob **Service_CF** zwar erstellt, aber noch keinen Datensätzen hinzugefügt wurde. Dies liegt daran, dass das benutzerdefinierte Feld für bestehende Datensätze nicht funktioniert, sodass wir warten müssen, bis neue Datensätze gesammelt werden.

![Anfängliche Anzahl](media/custom-fields/initial-count.png)

Nachdem etwas Zeit vergangen ist und neue Ereignisse gesammelt wurden, wird das Feld **Service_CF** jetzt Datensätzen hinzugefügt, die unseren Kriterien entsprechen.

![Endergebnis](media/custom-fields/final-results.png)

Nun können wir das benutzerdefinierte Feld wie jede andere Datensatzeigenschaft verwenden.  Zur Veranschaulichung erstellen wir eine Abfrage, die auf der Grundlage des neuen Felds **Service_CF** eine Gruppierung vornimmt, um zu prüfen, welche Dienste besonders aktiv sind.

![Gruppierungsabfrage](media/custom-fields/query-group.png)

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über [Protokollabfragen](../log-query/log-query-overview.md), um Abfragen mithilfe von benutzerdefinierten Feldern für die Kriterien zu erstellen.
* Überwachen Sie [benutzerdefinierte Protokolldateien](data-sources-custom-logs.md), die Sie mithilfe von benutzerdefinierten Feldern analysieren.

