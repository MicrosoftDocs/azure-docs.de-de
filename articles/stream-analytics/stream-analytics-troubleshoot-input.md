---
title: Eingaben zur Behandlung von Problemen bei Azure Stream Analytics
description: In diesem Artikel werden Verfahren zur Problembehandlung Ihrer Eingangsverbindungen in Azure Stream Analytics-Aufträgen beschrieben.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: b5ed614fdd378b36d8f95fc90ce7ff98d63ef31a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60761871"
---
# <a name="troubleshoot-input-connections"></a>Problembehandlung für Eingangsverbindungen

Auf dieser Seite werden häufige Probleme mit Eingangsverbindungen und deren Behandlung beschrieben.

## <a name="input-events-not-received-by-job"></a>Vom Auftrag nicht empfangene Eingabeereignisse 
1.  Testen Sie die Verbindung. Überprüfen Sie die Verbindung mit Ein- und Ausgaben mithilfe der Schaltfläche **Verbindung testen** für die einzelnen Ein- und Ausgaben.

2.  Überprüfen Sie die Eingabedaten.

    Stellen Sie mit [Service Bus-Explorer](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a) eine Verbindung mit Azure Event Hub her (bei Verwendung der Event Hub-Eingabe), um zu überprüfen, ob Eingabedaten an Event Hub übertragen werden.
        
    Verwenden Sie die Schaltfläche [**Beispieldaten**](stream-analytics-sample-data-input.md) für jede Eingabe, und laden Sie die Eingabebeispieldaten herunter.
        
    Überprüfen Sie die Beispieldaten, um die Form der Daten zu verstehen: das Schema und die [Datentypen](https://msdn.microsoft.com/library/azure/dn835065.aspx).

## <a name="malformed-input-events-causes-deserialization-errors"></a>Falsch formatierte Eingabeereignisse führen zu Deserialisierungsfehlern 
Deserialisierungsprobleme werden verursacht, wenn der Eingabedatenstrom Ihres Stream Analytics-Auftrags falsch formatierte Nachrichten enthält. Beispielsweise kann eine falsch formatierte Nachricht durch eine fehlende Klammer oder geschweifte Klammer in einem JSON-Objekt oder ein falsches Zeitstempelformat im Zeitfeld verursacht werden. 
 
Wenn ein Stream Analytics-Auftrag eine falsch formatierte Nachricht aus einer Eingabe empfängt, wird diese gelöscht und Sie werden mit einer Warnung benachrichtigt. Auf der Kachel **Eingaben** des Stream Analytics-Auftrags wird ein Warnsymbol angezeigt. Dieses Warnzeichen existiert, solange sich der Auftrag im Ausführungszustand befindet:

![Azure Stream Analytics-Kachel „Eingaben“](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Aktivieren Sie die Diagnoseprotokolle, um Details zur Warnung anzuzeigen. Bei falsch formatierten Eingabeereignissen enthalten die Ausführungsprotokolle einen Eintrag mit der Meldung, die etwa wie folgt aussieht: 
```
Could not deserialize the input event(s) from resource <blob URI> as json.
```

### <a name="what-caused-the-deserialization-error"></a>Ursache des Deserialisierungsfehlers
Sie können die folgenden Schritte durchführen, um die Eingabeereignisse im Detail zu analysieren, um zu verstehen, wodurch der Deserialisierungsfehler verursacht wurde. Sie können dann die Ereignisquelle reparieren, um Ereignisse im richtigen Format zu generieren, damit dieses Problem nicht erneut auftritt.

1. Navigieren Sie zur Eingabekachel, und klicken Sie auf die Warnsymbole, um die Liste der Probleme anzuzeigen.

2. Auf der Kachel „Eingabedetails“ wird eine Liste von Warnungen mit Details zu den einzelnen Problemen angezeigt. Die folgende exemplarische Warnmeldung enthält die Partitions-, Offset- und Sequenznummern, bei denen falsch formatierte JSON-Daten vorliegen. 

   ![Stream Analytics-Warnmeldung mit Offset](media/stream-analytics-malformed-events/warning-message-with-offset.png)
   
3. Um die JSON-Daten mit dem falschen Format zu finden, führen Sie den Code in „CheckMalformedEvents.cs“ aus. Diese Datei ist über das [GitHub-Beispielrepository](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH) verfügbar. Dieser Code liest die Partitions-ID und den Offset und gibt die Daten aus dem Offset aus. 

4. Nach dem Lesen der Daten können Sie das Serialisierungsformat analysieren und korrigieren.

5. Sie können auch [Ereignisse von einem IoT Hub mit dem Service Bus-Explorer lesen](https://code.msdn.microsoft.com/How-to-read-events-from-an-1641eb1b).

## <a name="job-exceeds-maximum-event-hub-receivers"></a>Auftrag überschreitet die maximale Anzahl von Event Hub-Empfängern
Eine bewährte Methode beim Arbeiten mit Event Hubs ist die Verwendung mehrerer Consumergruppen, um die Skalierbarkeit von Aufträgen sicherzustellen. Die Anzahl der Leser im Stream Analytics-Auftrag für eine bestimmte Eingabe wirkt sich auf die Anzahl der Leser in einer einzelnen Consumergruppe aus. Die genaue Anzahl der Empfänger basiert auf internen Implementierungsdetails für die Logik der horizontalen Skalierungstopologie und wird nicht extern zur Verfügung gestellt. Die Anzahl der Leser kann sich beim Start eines Auftrags oder bei Auftrags-Upgrades ändern.

Der folgende Fehler wird angezeigt, wenn die maximale Anzahl der Empfänger überschritten wird: `The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> Wenn sich die Anzahl der Leser während der Aktualisierung des Auftrags ändert, werden Warnungen zu vorübergehenden Problemen in Überwachungsprotokolle geschrieben. Stream Analytics-Aufträge werden automatisch von diesen vorübergehenden Problemen behoben.

### <a name="add-a-consumer-group-in-event-hubs"></a>Hinzufügen einer Consumergruppe in Event Hubs
Führen Sie die folgenden Schritte aus, um Ihrer Event Hubs-Instanz eine neue Consumergruppe hinzuzufügen:

1. Melden Sie sich beim Azure-Portal an.

2. Suchen Sie nach Ihrer Event Hubs-Instanz.

3. Wählen Sie unter der Überschrift **Entitäten** den Eintrag **Event Hubs** aus.

4. Wählen Sie den Event Hub anhand des Namens aus.

5. Wählen Sie auf der Seite **Event Hubs-Instanz** unter der Überschrift **Entitäten** den Eintrag **Consumergruppen** aus. Es wird eine Consumergruppe mit dem Namen **$Default** aufgelistet.

6. Wählen Sie **+ Consumergruppe** aus, um eine neue Consumergruppe hinzuzufügen. 

   ![Hinzufügen einer Consumergruppe in Event Hubs](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Als Sie die Eingabe im Stream Analytics-Auftrag erstellt haben, um auf den Event Hub zu verweisen, haben Sie dort die Consumergruppe angegeben. Wenn keine Angabe erfolgt, wird „$Default“ verwendet. Bearbeiten Sie nach der Erstellung einer neuen Consumergruppe die Event Hub-Eingabe im Stream Analytics-Auftrag, und geben Sie den Namen der neuen Consumergruppe an.


## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>Anzahl der Leser pro Partition überschreitet den Event Hubs-Grenzwert

Wenn Ihre Streamingabfragesyntax mehrmals auf die gleiche Event Hub-Eingaberessource verweist, kann das Auftragsmodul mehrere Leser pro Abfrage aus derselben Consumergruppe verwenden. Wenn es zu viele Verweise auf dieselbe Consumergruppe gibt, kann der Auftrag den Grenzwert 5 überschreiten und einen Fehler auslösen. In diesen Fällen können Sie mithilfe der im folgenden Abschnitt beschriebenen Lösung eine weitere Unterteilung vornehmen, indem Sie mehrere Eingaben für mehrere Consumergruppen verwenden. 

Es folgen Szenarien, in denen die Anzahl der Leser pro Partition den Event Hubs-Grenzwert 5 überschreitet:

* Mehrere SELECT-Anweisungen: Bei Verwendung mehrerer SELECT-Anweisungen, die auf die **gleiche** Event Hub-Eingabe verweisen, bewirkt jede SELECT-Anweisung, dass ein neuer Empfänger erstellt wird.
* UNION: Wenn Sie UNION verwenden, es ist möglich, dass mehrere Eingaben auf den **gleichen** Event Hub und die gleiche Consumergruppe verweisen.
* SELF JOIN: Wenn Sie einen SELF JOIN-Vorgang verwenden, es ist möglich, dass auf den **gleichen** Event Hub mehrmals verwiesen wird.

Es folgen bewährte Methoden zum Vermeiden von Szenarien, in denen die Anzahl der Leser pro Partition den Event Hubs-Grenzwert 5 überschreitet.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>Unterteilen der Abfrage mithilfe einer WITH-Klausel in mehrere Schritte

Die WITH-Klausel gibt ein temporäres benanntes Resultset an, auf das in der Abfrage in einer FROM-Klausel verwiesen werden kann. Sie definieren die WITH-Klausel im Ausführungsbereich einer einzelnen SELECT-Anweisung.

Verwenden Sie beispielsweise anstelle dieser Abfrage:

```SQL
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

Diese Abfrage:

```SQL
WITH data AS (
   SELECT * FROM inputEventHub
)

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Sicherstellen, dass Eingaben an verschiedene Consumergruppen gebunden werden

Erstellen Sie für Abfragen, bei denen drei oder mehr Eingaben mit der gleichen Event Hubs-Consumergruppe verbunden sind, separate Consumergruppen. Hierfür ist die Erstellung zusätzlicher Stream Analytics-Eingaben erforderlich.

## <a name="get-help"></a>Hier erhalten Sie Hilfe

Um Hilfe zu erhalten, nutzen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Nächste Schritte

* [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
* [Erste Schritte mit Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
