---
title: Verarbeiten von IoT-Datenströmen in Echtzeit mit Azure Stream Analytics
description: IoT-Sensortags und -Datenströme mit Stream Analytics und Echtzeit-Datenverarbeitung
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 11/26/2019
ms.openlocfilehash: 311aca139220622a0436d490e73a536c3fc898c9
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93129013"
---
# <a name="process-real-time-iot-data-streams-with-azure-stream-analytics"></a>Verarbeiten von IoT-Datenströmen in Echtzeit mit Azure Stream Analytics

In diesem Artikel erfahren Sie, wie Sie Datenstrom-Verarbeitungslogik erstellen, um Daten von IoT-Geräten (Internet of Things, Internet der Dinge) zu erfassen. Sie verwenden einen echten IoT-Anwendungsfall aus der Praxis, um zu zeigen, wie Sie Ihre Lösung schnell und wirtschaftlich erstellen.

## <a name="prerequisites"></a>Voraussetzungen

* Erstellen eines kostenlosen [Azure-Abonnements](https://azure.microsoft.com/pricing/free-trial/)
* Herunterladen der Beispielabfrage und Datendateien bei [GitHub](https://aka.ms/azure-stream-analytics-get-started-iot)

## <a name="scenario"></a>Szenario

Contoso, ein Unternehmen im Bereich der industriellen Automation, hat seinen Fertigungsprozess vollständig automatisiert. Die Maschinen im Werk verfügen über Sensoren, die Datenströme in Echtzeit ausgeben können. In diesem Szenario möchte ein Production Floor Manager in Echtzeit Einblick in die Sensordaten erhalten, um darin nach Mustern zu suchen und entsprechend darauf zu reagieren. Sie können die Stream Analytics-Abfragesprache (SAQL, Stream Analytics Query Language) für die Sensordaten verwenden, um für den eingehenden Datenstrom interessante Muster zu ermitteln.

In diesem Beispiel werden die Daten mit einem SensorTag-Gerät von Texas Instruments generiert. Die Nutzlast der Daten liegt im JSON-Format vor und sieht wie folgt aus:

```json
{
    "time": "2016-01-26T20:47:53.0000000",  
    "dspl": "sensorE",  
    "temp": 123,  
    "hmdt": 34  
}  
```

In der Praxis können Hunderte dieser Sensoren einen Datenstrom mit Ereignissen generieren. Idealerweise führt ein Gatewaygerät hierbei Code aus, um diese Ereignisse mittels Push an [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) oder [Azure IoT Hubs](https://azure.microsoft.com/services/iot-hub/) zu übertragen. Ihr Stream Analytics-Auftrag erfasst diese Ereignisse über Event Hubs und führt in Echtzeit Analyseabfragen für die Datenströme aus. Die Ergebnisse können dann an eine der [unterstützten Ausgaben](stream-analytics-define-outputs.md) gesendet werden.

Zur einfacheren Verwendung enthält dieser Leitfaden zu den ersten Schritten eine Datei mit Beispieldaten, die mit echten SensorTag-Geräten erfasst wurden. Sie können Abfragen für die Beispieldaten ausführen und Ergebnisse anzeigen. In den nachfolgenden Tutorials erfahren Sie, wie Sie Ihren Auftrag mit Eingaben und Ausgaben verbinden und für den Azure-Dienst bereitstellen.

## <a name="create-a-stream-analytics-job"></a>Erstellen eines Stream Analytics-Auftrags

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) im linken Navigationsmenü **+ Ressource erstellen** aus. Wählen Sie anschließend aus **Analytics** **Stream Analytics-Auftrag** aus.
   
    ![Neuen Stream Analytics-Auftrag erstellen](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-02.png)

1. Geben Sie einen eindeutigen Auftragsnamen ein, und überprüfen Sie, ob das richtige Abonnement für Ihren Auftrag angegeben ist. Erstellen Sie eine neue Ressourcengruppe, oder wählen Sie eine vorhandene Ressourcengruppe Ihres Abonnements aus.

1. Wählen Sie einen Speicherort für Ihren Auftrag aus. Verwenden Sie den gleichen Speicherort für Ihre Ressourcengruppe wie für alle Ressourcen, um die Verarbeitungsgeschwindigkeit zu erhöhen und die Kosten zu reduzieren. Nachdem Sie die Konfigurationen vorgenommen haben, wählen Sie **Erstellen** aus.
   
    ![Erstellen eines neuen Stream Analytics-Auftrags – Details](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-03.png)

## <a name="create-an-azure-stream-analytics-query"></a>Erstellen einer Azure Stream Analytics-Abfrage
Der nächste Schritt nach dem Erstellen des Auftrags besteht darin, eine Abfrage zu schreiben. Sie können Abfragen für Beispieldateien testen, ohne eine Eingabe oder Ausgabe mit Ihrem Auftrag zu verbinden.

Laden Sie [HelloWorldASA-InputStream.json](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json
) von GitHub herunter. Navigieren Sie dann im Azure-Portal zu Ihrem Azure Stream Analytics-Auftrag.

Wählen Sie **Abfrage** unter **Auftragstopologie** aus dem linken Menü aus. Wählen Sie dann **Beispieleingabe hochladen** aus. Laden Sie die `HelloWorldASA-InputStream.json`-Datei hoch, und wählen Sie **OK** aus.

![Abfragekachel im Stream Analytics-Dashboard](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-05.png)

Beachten Sie, dass eine Vorschauversion der Daten in der Tabelle **Eingabevorschau** automatisch ausgefüllt wird.

![Vorschauversion der Beispieleingabedaten](./media/stream-analytics-get-started-with-iot-devices/input-preview.png)

### <a name="query-archive-your-raw-data"></a>Abfrage: Archivieren von Rohdaten

Die einfachste Form einer Abfrage ist eine Passthrough-Abfrage, bei der alle Eingabedaten unter dem angegebenen Ausgabeort archiviert werden. Diese Abfrage ist die Standardabfrage, die in einem neuen Azure Stream Analytics-Auftrag ausgefüllt wird.

```sql
SELECT 
    *
INTO
    Output
FROM
    InputStream
```

Wählen Sie **Abfrage testen** aus, und sehen Sie sich die Ergebnisse in der Tabelle **Testergebnisse** an.

![Testergebnisse für die Stream Analytics-Abfrage](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-07.png)

### <a name="query-filter-the-data-based-on-a-condition"></a>Abfrage: Filtern der Daten basierend auf einer Bedingung

Es wird nun versucht, die auf einer Bedingung basierenden Ergebnisse zu filtern. Wir möchten Ihnen Ergebnisse nur für die Ereignisse anzeigen, die von "sensorA" kommen.

```sql
SELECT 
    time,
    dspl AS SensorName,
    temp AS Temperature,
    hmdt AS Humidity
INTO
   Output
FROM
    InputStream
WHERE dspl='sensorA'
```

Fügen Sie die Abfrage in den Editor ein, und wählen Sie **Testabfrage** aus, um die Ergebnisse zu überprüfen.

![Filtern eines Datenstroms](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-08.png)

### <a name="query-alert-to-trigger-a-business-workflow"></a>Abfrage: Warnung zur Auslösung eines Geschäftsworkflows

Wir fügen unserer Abfrage nun weitere Details hinzu. Für jeden Typ von Sensor möchten wir die Durchschnittstemperatur pro 30-Sekunden-Fenster überwachen und die Ergebnisse nur anzeigen, wenn die Durchschnittstemperatur über 100 Grad liegt.

```sql
SELECT 
    System.Timestamp AS OutputTime,
    dspl AS SensorName,
    Avg(temp) AS AvgTemperature
INTO
   Output
FROM
    InputStream TIMESTAMP BY time
GROUP BY TumblingWindow(second,30),dspl
HAVING Avg(temp)>100
```

![Filterabfrage (30 Sekunden)](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-10.png)

Die Ergebnisse umfassen nur noch 245 Zeilen, und es werden die Namen der Sensoren aufgeführt, bei denen die Durchschnittstemperatur höher als 100 ist. Bei dieser Abfrage wird der Datenstrom mit den Ereignissen nach **dspl** (Sensorname) gruppiert, und es wird ein **rollierendes Fenster** von 30 Sekunden verwendet. Bei zeitlichen Abfragen muss angegeben werden, wie der Zeitablauf erfolgen soll. Mit der **TIMESTAMP BY** -Klausel haben Sie die Spalte **OUTPUTTIME** angegeben, um Zeiten allen Zeitberechnungen zuzuordnen. Ausführliche Informationen finden Sie in den Artikeln [Zeitverwaltung (Azure Stream Analytics)](/stream-analytics-query/time-management-azure-stream-analytics) und [Windowing-Funktionen (Azure Stream Analytics)](/stream-analytics-query/windowing-azure-stream-analytics).

### <a name="query-detect-absence-of-events"></a>Abfrage: Abwesenheit von Ereignissen erkennen

Wie können wir eine Abfrage schreiben, die einen Mangel an Eingabeereignissen findet? Hierzu wird ermittelt, wann ein Sensor zum letzten Mal Daten gesendet und danach fünf Sekunden lang keine weiteren Ereignisse gesendet hat.

```sql
SELECT 
    t1.time,
    t1.dspl AS SensorName
INTO
   Output
FROM
    InputStream t1 TIMESTAMP BY time
LEFT OUTER JOIN InputStream t2 TIMESTAMP BY time
ON
    t1.dspl=t2.dspl AND
    DATEDIFF(second,t1,t2) BETWEEN 1 and 5
WHERE t2.dspl IS NULL
```

![Abwesenheit von Ereignissen erkennen](./media/stream-analytics-get-started-with-iot-devices/stream-analytics-get-started-with-iot-devices-11.png)

Hier verwenden wir den Verknüpfungsvorgang **LEFT OUTER** für den gleichen Datenstrom (Selbstverknüpfung). Für eine innere Verknüpfung ( **INNER** ) wird nur dann ein Ergebnis zurückgegeben, wenn eine Übereinstimmung gefunden wird.  Wenn ein Ereignis von der linken Seite der Verknüpfung keine Übereinstimmung besitzt, wird bei **LEFT OUTER** für alle Spalten der rechten Seite eine Zeile mit NULL zurückgegeben. Dieses Verfahren ist äußerst hilfreich, um die Abwesenheit von Ereignissen zu ermitteln. Weitere Informationen finden Sie unter [JOIN (Azure Stream Analytics)](/stream-analytics-query/join-azure-stream-analytics).

## <a name="conclusion"></a>Zusammenfassung

In diesem Artikel erfahren Sie, wie Sie verschiedene Abfragen in der Stream Analytics-Abfragesprache schreiben und die Ergebnisse im Browser anzeigen. Dies sind aber nur die ersten Schritte. Stream Analytics unterstützt verschiedenste Ein- und Ausgaben und kann sogar Funktionen in Azure Machine Learning nutzen. Dies macht Stream Analytics zu einem zuverlässigen Tool für die Datenstromanalyse. Weitere Informationen zum Schreiben von Abfragen finden Sie im Artikel zu [gängigen Abfragemustern](stream-analytics-stream-analytics-query-patterns.md).