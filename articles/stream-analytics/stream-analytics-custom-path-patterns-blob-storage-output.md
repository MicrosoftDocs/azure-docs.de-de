---
title: Benutzerdefinierte Blobausgabepartitionierung in Azure Stream Analytics
description: In diesem Artikel werden die Funktionen für benutzerdefinierte DateTime-Pfadmuster und für benutzerdefinierte Felder oder Attribute für die Blobspeicherausgabe von Azure Stream Analytics-Aufträgen beschrieben.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/07/2019
ms.custom: seodec18
ms.openlocfilehash: 9763a0ac3cba15dcfd66b8fad83230e2b0eb356b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96491671"
---
# <a name="azure-stream-analytics-custom-blob-output-partitioning"></a>Benutzerdefinierte Blobausgabepartitionierung in Azure Stream Analytics

Azure Stream Analytics unterstützt die benutzerdefinierte Blobausgabepartitionierung mit benutzerdefinierten Feldern oder Attributen und benutzerdefinierten DateTime-Pfadmustern. 

## <a name="custom-field-or-attributes"></a>Benutzerdefiniertes Feld oder Attribute

Durch Verwendung eines benutzerdefinierten Felds oder von Eingabeattributen ist eine größere Kontrolle über die Ausgabe möglich, sodass Workflows zur Verarbeitung von Downstreamdaten und zur Berichterstellung verbessert werden.

### <a name="partition-key-options"></a>Optionen für den Partitionsschlüssel

Der für die Partitionierung von Eingabedaten verwendete Partitionsschlüssel oder Spaltenname kann alphanumerische Zeichen mit Bindestrichen, Unterstrichen und Leerzeichen enthalten. Es ist nicht möglich, geschachtelte Felder als Partitionsschlüssel zu verwenden, es sei denn, sie werden in Verbindung mit Aliasen verwendet. Der Partitionsschlüssel muss einen der Typen NVARCHAR(MAX), BIGINT, FLOAT oder BIT (Kompatibilitätsgrad 1.2 oder höher) aufweisen. Weitere Informationen finden Sie unter [Azure Stream Analytics-Datentypen](/stream-analytics-query/data-types-azure-stream-analytics).

### <a name="example"></a>Beispiel

Angenommen, in einem Auftrag werden Eingabedaten von Live-Benutzersitzungen erfasst, die mit einem externen Dienst für Videospiele verbunden sind, wobei die erfassten Daten zum Identifizieren der Sitzungen die Spalte **client_id** enthalten. Zum Partitionieren der Daten nach **client_id** legen Sie das Feld für das Blobpfadmuster so fest, dass es beim Erstellen eines Auftrags das Partitionstoken **{client_id}** in Blobausgabeeigenschaften enthält. Da Daten mit verschiedenen Werten für **client_id** durch den Stream Analytics-Auftrag geleitet werden, erfolgt die Speicherung der Ausgabedaten basierend auf einem einzelnen **client_id**-Wert pro Ordner in separaten Ordnern.

![Pfadmuster mit Client-ID](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-path-pattern-client-id.png)

Wenn es sich bei der Auftragseingabe um Sensordaten von Millionen Sensoren handelt und jedem Sensor ein **sensor_id**-Wert zugewiesen ist, lautet das Pfadmuster analog zum Beispiel oben **{sensor_id}** , um die jeweiligen Sensordaten in unterschiedlichen Ordnern zu partitionieren.  


Bei Verwendung der REST-API sieht der Ausgabeausschnitt einer für diese Anforderung verwendeten JSON-Datei beispielsweise wie folgt aus:  

![REST-API-Ausgabe](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-rest-output.png)

Sobald der Auftrag ausgeführt wird, sieht der *clients*-Container z.B. wie folgt aus:  

![clients-Container](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-clients-container.png)

Jeder Ordner kann mehrere Blobs enthalten, und jedes Blob enthält einen oder mehrere Datensätze. Im Beispiel oben ist ein einzelnes Blob in einem Ordner mit der Bezeichnung „06000000“ mit den folgenden Inhalten enthalten:

![Blobinhalte](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-contents.png)

Beachten Sie, dass jeder Datensatz im Blob die Spalte **client_id** enthält, die mit dem Ordnernamen übereinstimmt, da **client_id** die Spalte war, die für die Partitionierung der Ausgabe im Ausgabepfad verwendet wurde.

### <a name="limitations"></a>Einschränkungen

1. Nur ein benutzerdefinierter Partitionsschlüssel ist in der Blobausgabeeigenschaft des Pfadmusters zulässig. Alle der folgenden Pfadmuster sind gültig:

   * cluster1/{date}/{aFieldInMyData}  
   * cluster1/{time}/{aFieldInMyData}  
   * cluster1/{aFieldInMyData}  
   * cluster1/{date}/{time}/{aFieldInMyData} 
   
2. Bei Partitionsschlüsseln wird die Groß-/Kleinschreibung nicht beachtet, sodass Partitionsschlüssel wie „John“ und „john“ identisch sind. Außerdem können Ausdrücke nicht als Partitionsschlüssel verwendet werden. Beispielsweise kann **{columnA + columnB}** nicht verwendet werden.  

3. Wenn ein Eingabestream aus Datensätzen mit einer Kardinalität des Partitionsschlüssels unter 8.000 besteht, werden die Datensätze an vorhandene Blobs angefügt und neue Blobs nur bei Bedarf erstellt. Wenn die Kardinalität über 8.000 liegt, gibt es keine Garantie dafür, dass Daten in vorhandene Blobs geschrieben und neue Blobs nicht für eine beliebige Anzahl von Datensätzen mit dem gleichen Partitionsschlüssel erstellt werden.

4. Wenn die Blobausgabe [als unveränderlich konfiguriert](../storage/blobs/storage-blob-immutable-storage.md) ist, erstellt Stream Analytics bei jedem Senden von Daten ein neues Blob.

## <a name="custom-datetime-path-patterns"></a>Benutzerdefinierte DateTime-Pfadmuster

Mit benutzerdefinierten DateTime-Pfadmustern können Sie ein Ausgabeformat festlegen, das mit den Hive Streaming-Konventionen übereinstimmt, damit Azure Stream Analytics Daten an Azure HDInsight und Azure Databricks zur weiteren Verarbeitung senden kann. Benutzerdefinierte DateTime-Pfadmuster können einfach mit dem Schlüsselwort `datetime` im Feld „Pfadpräfix“ der Blobausgabe und dem Formatbezeichner implementiert werden. Beispiel: `{datetime:yyyy}`.

### <a name="supported-tokens"></a>Unterstützte Token

Die folgenden Formatbezeichnertoken können einzeln oder in Kombination verwendet werden, um benutzerdefinierte DateTime-Formate zu erhalten:

|Formatbezeichner   |BESCHREIBUNG   |Ergebnisse zur Beispielzeit 2018-01-02T10:06:08|
|----------|-----------|------------|
|{datetime:yyyy}|Das Jahr als vierstellige Zahl|2018|
|{datetime:MM}|Monat von 01 bis 12|01|
|{datetime:M}|Monat von 1 bis 12|1|
|{datetime:dd}|Tag von 01 bis 31|02|
|{datetime:d}|Tag von 1 bis 31|2|
|{datetime:HH}|Stunde im 24-Stunden-Format, von 00 bis 23|10|
|{datetime:mm}|Minuten von 00 bis 60|06|
|{datetime:m}|Minuten von 0 bis 60|6|
|{datetime:ss}|Sekunden von 00 bis 60|08|

Wenn Sie keine benutzerdefinierten DateTime-Muster verwenden möchten, können Sie das Token {date} und/oder {time} zum Pfadpräfix hinzufügen, um eine Dropdownliste mit integrierten DateTime-Formaten zu generieren.

![Alte DateTime-Formate von Stream Analytics](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

### <a name="extensibility-and-restrictions"></a>Erweiterbarkeit und Einschränkungen

Sie können beliebig viele Token, `{datetime:<specifier>}`, im Pfadmuster verwenden, bis Sie den Zeichengrenzwert für das Pfadpräfix erreichen. Formatbezeichner können innerhalb eines einzelnen Tokens nicht über die bereits in den Dropdownlisten für Datum und Uhrzeit aufgeführten Kombinationen hinaus kombiniert werden. 

Für eine Pfadpartition von `logs/MM/dd`:

|Gültiger Ausdruck   |Ungültiger Ausdruck   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

Sie können denselben Formatbezeichner mehrmals im Pfadpräfix verwenden. Das Token muss jedes Mal wiederholt werden.

### <a name="hive-streaming-conventions"></a>Hive-Streaming-Konventionen

Benutzerdefinierte Pfadmuster für Blob Storage können mit der Hive-Streaming-Konvention verwendet werden, die erwartet, dass Ordner im Ordnernamen mit `column=` gekennzeichnet sind.

Beispiel: `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}`.

Die benutzerdefinierte Ausgabe erübrigt das mühevolle Ändern von Tabellen und das manuelle Hinzufügen von Partitionen zu Portdaten zwischen Azure Stream Analytics und Hive. Stattdessen können viele Ordner automatisch wie folgt hinzugefügt werden:

```SQL
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>Beispiel

Erstellen Sie ein Speicherkonto, eine Ressourcengruppe, einen Stream Analytics-Auftrag und eine Eingabequelle gemäß der Schnellstartanleitung [Schnellstart: Erstellen eines Stream Analytics-Auftrags mithilfe des Azure-Portals](stream-analytics-quick-create-portal.md). Verwenden Sie dieselben Beispieldaten wie in der Schnellstartanleitung, die auch auf [GitHub](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) verfügbar sind.

Erstellen Sie eine Blobausgabesenke mit der folgenden Konfiguration:

![Stream Analytics erstellt Blobausgabesenke](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

Das vollständige Pfadmuster sieht wie folgt aus:


`year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}`


Wenn Sie den Auftrag starten, wird in Ihrem Blobcontainer eine Ordnerstruktur basierend auf dem Pfadmuster erstellt. Sie können einen Drilldown bis zur Tagesebene ausführen.

![Stream Analytics-Blobausgabe mit benutzerdefiniertem Pfadmuster](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>Nächste Schritte

* [Grundlegendes zu den Ausgaben von Azure Stream Analytics](stream-analytics-define-outputs.md)