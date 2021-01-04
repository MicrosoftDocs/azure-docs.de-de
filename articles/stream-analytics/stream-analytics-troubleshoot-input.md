---
title: Eingaben zur Behandlung von Problemen bei Azure Stream Analytics
description: In diesem Artikel werden Verfahren zur Problembehandlung Ihrer Eingangsverbindungen in Azure Stream Analytics-Aufträgen beschrieben.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: troubleshooting
ms.date: 05/01/2020
ms.custom: seodec18
ms.openlocfilehash: 760c98ce9464e4d40f01256a973e07d9084c6dfe
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93123284"
---
# <a name="troubleshoot-input-connections"></a>Problembehandlung für Eingangsverbindungen

In diesem Artikel werden häufige Probleme mit Azure Stream Analytics-Eingabeverbindungen beschrieben, die Behandlung von Eingabeproblemen und die Behebung der Probleme. Viele Troubleshootingschritte erfordern die Aktivierung von Ressourcenprotokollen für Ihren Stream Analytics-Auftrag. Wenn Sie Ressourcenprotokolle nicht aktiviert haben, finden Sie weitere Informationen unter [Problembehandlung von Azure Stream Analytics mit Ressourcenprotokollen](stream-analytics-job-diagnostic-logs.md).

## <a name="input-events-not-received-by-job"></a>Vom Auftrag nicht empfangene Eingabeereignisse 

1.  Testen Sie Ihre Ein- und Ausgabekonnektivität. Überprüfen Sie die Verbindung mit Ein- und Ausgaben mithilfe der Schaltfläche **Verbindung testen** für die einzelnen Ein- und Ausgaben.

2.  Überprüfen Sie die Eingabedaten.

    1. Verwenden Sie die Schaltfläche [**Beispieldaten**](./stream-analytics-test-query.md) für jede Eingabe. Laden Sie die Beispieldaten für die Eingabe herunter.
        
    1. Untersuchen Sie die Beispieldaten, um das Schema und die [Datentypen](/stream-analytics-query/data-types-azure-stream-analytics) zu verstehen.
    
    1. Überprüfen Sie [Event Hub-Metriken](../event-hubs/event-hubs-metrics-azure-monitor.md), um sicherzustellen, dass Ereignisse gesendet werden. Die Nachrichtenmetriken sollten größer als null sein, wenn Event Hubs Nachrichten empfangen.

3.  Vergewissern Sie sich, dass Sie einen Zeitbereich in der Eingabevorschau ausgewählt haben. Wählen Sie **Zeitbereich auswählen** aus, und geben Sie dann eine Beispieldauer ein, bevor Sie die Abfrage testen.

## <a name="malformed-input-events-causes-deserialization-errors"></a>Falsch formatierte Eingabeereignisse führen zu Deserialisierungsfehlern 

Deserialisierungsprobleme werden verursacht, wenn der Eingabedatenstrom Ihres Stream Analytics-Auftrags falsch formatierte Nachrichten enthält. Beispielsweise kann eine falsch formatierte Nachricht durch eine fehlende Klammer oder geschweifte Klammer in einem JSON-Objekt oder ein falsches Zeitstempelformat im Zeitfeld verursacht werden. 
 
Wenn ein Stream Analytics-Auftrag eine falsch formatierte Nachricht aus einer Eingabe empfängt, wird diese gelöscht und Sie werden mit einer Warnung benachrichtigt. Auf der Kachel **Eingaben** des Stream Analytics-Auftrags wird ein Warnsymbol angezeigt. Das folgende Warnsymbol ist so lange vorhanden, wie sich der Auftrag im Zustand „Wird ausgeführt“ befindet:

![Azure Stream Analytics-Kachel „Eingaben“](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Aktivieren Sie Ressourcenprotokolle, um die Details des Fehlers sowie die Nachricht (Nutzlast) anzuzeigen, die den Fehler verursacht hat. Es gibt mehrere Gründe, warum Deserialisierungsfehler auftreten können. Weitere Informationen zu spezifischen Deserialisierungsfehlern finden Sie unter [Eingabedatenfehler](data-errors.md#input-data-errors). Wenn Ressourcenprotokolle nicht aktiviert sind, wird eine kurze Benachrichtigung im Azure-Portal angezeigt.

![Warnungsbenachrichtigung zu Eingabedetails](media/stream-analytics-malformed-events/warning-message-with-offset.png)

In Fällen, in denen die Nutzlast der Nachricht größer als 32 KB ist oder im Binärformat vorliegt, führen Sie den „CheckMalformedEvents.cs“-Code aus, der im [GitHub-Beispielrepository](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH) verfügbar ist. Dieser Code liest die Partitions-ID und den Offset und gibt die Daten aus dem Offset aus. 

## <a name="job-exceeds-maximum-event-hub-receivers"></a>Auftrag überschreitet die maximale Anzahl von Event Hub-Empfängern

Eine bewährte Methode beim Arbeiten mit Event Hubs ist die Verwendung mehrerer Consumergruppen für die Skalierbarkeit von Aufträgen. Die Anzahl der Leser im Stream Analytics-Auftrag für eine bestimmte Eingabe wirkt sich auf die Anzahl der Leser in einer einzelnen Consumergruppe aus. Die genaue Anzahl der Empfänger basiert auf internen Implementierungsdetails für die Logik der horizontalen Skalierungstopologie und wird nicht extern zur Verfügung gestellt. Die Anzahl der Leser kann sich beim Start eines Auftrags oder bei Auftrags-Upgrades ändern.

Die folgenden Fehlermeldungen werden angezeigt, wenn die maximale Anzahl der Empfänger überschritten wird. Die Fehlermeldung enthält eine Liste vorhandener Verbindungen, die unter einer Consumergruppe mit Event Hub hergestellt wurden. Das Tag `AzureStreamAnalytics` gibt an, dass die Verbindungen vom Azure Streaming-Dienst hergestellt wurden.

```
The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.

The following information may be helpful in identifying the connected receivers: Exceeded the maximum number of allowed receivers per partition in a consumer group which is 5. List of connected receivers – 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1, 
AzureStreamAnalytics_c4b65e4a-f572-4cfc-b4e2-cf237f43c6f0_1.
```

> [!NOTE]
> Wenn sich die Anzahl der Leser während der Aktualisierung des Auftrags ändert, werden Warnungen zu vorübergehenden Problemen in Überwachungsprotokolle geschrieben. Stream Analytics-Aufträge werden automatisch von diesen vorübergehenden Problemen behoben.

### <a name="add-a-consumer-group-in-event-hubs"></a>Hinzufügen einer Consumergruppe in Event Hubs

Führen Sie die folgenden Schritte aus, um Ihrer Event Hubs-Instanz eine neue Consumergruppe hinzuzufügen:

1. Melden Sie sich beim Azure-Portal an.

2. Suchen Sie nach Ihrer Event Hub-Instanz.

3. Wählen Sie unter der Überschrift **Entitäten** den Eintrag **Event Hubs** aus.

4. Wählen Sie den Event Hub anhand des Namens aus.

5. Wählen Sie auf der Seite **Event Hubs-Instanz** unter der Überschrift **Entitäten** den Eintrag **Consumergruppen** aus. Es wird eine Consumergruppe mit dem Namen **$Default** aufgelistet.

6. Wählen Sie **+ Consumergruppe** aus, um eine neue Consumergruppe hinzuzufügen. 

   ![Hinzufügen einer Consumergruppe in Event Hubs](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Als Sie die Eingabe im Stream Analytics-Auftrag erstellt haben, um auf den Event Hub zu verweisen, haben Sie dort die Consumergruppe angegeben. Wenn keine Angabe erfolgt, wird **$Default** verwendet. Bearbeiten Sie nach der Erstellung einer neuen Consumergruppe die Event Hub-Eingabe im Stream Analytics-Auftrag, und geben Sie den Namen der neuen Consumergruppe an.

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

### <a name="create-separate-inputs-with-different-consumer-groups"></a>Erstellen separater Eingaben mit unterschiedlichen Consumergruppen

Sie können separate Eingaben mit unterschiedlichen Consumergruppen für denselben Event Hub erstellen. Die folgende UNION-Abfrage ist ein Beispiel, bei dem *InputOne* und *InputTwo* auf dieselbe Event Hub-Quelle verweisen. Alle Abfragen können separate Eingaben mit unterschiedlichen Consumergruppen enthalten. Die UNION-Abfrage ist nur ein Beispiel.

```sql
WITH 
DataOne AS 
(
SELECT * FROM InputOne 
),

DataTwo AS 
(
SELECT * FROM InputTwo 
),

SELECT foo FROM DataOne
UNION 
SELECT foo FROM DataTwo

```

## <a name="readers-per-partition-exceeds-iot-hub-limit"></a>Anzahl der Leser pro Partition überschreitet das IoT Hub-Limit

Stream Analytics-Aufträge verwenden den integrierten [Event Hub-kompatiblen Endpunkt](../iot-hub/iot-hub-devguide-messages-read-builtin.md) in IoT Hub, um Ereignisse aus IoT Hub zu verbinden und zu lesen. Wenn Ihre Anzahl von Lesevorgängen pro Partition die Limits von IoT Hub überschreitet, können Sie die [Lösungen für Event Hub](#readers-per-partition-exceeds-event-hubs-limit) verwenden, um dies zu beheben. Sie können eine Consumergruppe für den integrierten Endpunkt über eine IoT Hub-Portalendpunktsitzung oder über das [IoT Hub SDK-](/rest/api/iothub/IotHubResource/CreateEventHubConsumerGroup) erstellen.

## <a name="get-help"></a>Hier erhalten Sie Hilfe

Weitere Unterstützung finden Sie auf der [Frageseite von Microsoft Q&A (Fragen und Antworten) zu Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Nächste Schritte

* [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
* [Erste Schritte mit Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
* [Stream Analytics Query Language Reference (in englischer Sprache)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](/rest/api/streamanalytics/)