---
title: Ausgaben von Azure Stream Analytics
description: In diesem Artikel werden die Datenausgabeoptionen beschrieben, die für Azure Stream Analytics verfügbar sind.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/2/2020
ms.openlocfilehash: 022e2e25c96473f49468f2bd48e5ee997933baea
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348711"
---
# <a name="outputs-from-azure-stream-analytics"></a>Ausgaben von Azure Stream Analytics

Ein Azure Stream Analytics-Auftrag besteht aus einer Eingabe, Abfrage und Ausgabe. Transformierte Daten können an verschiedene Ausgabetypen gesendet werden. In diesem Artikel werden die unterstützten Stream Analytics-Ausgaben aufgeführt. Verweisen Sie beim Entwerfen Ihrer Stream Analytics-Abfrage auf den Namen der Ausgabe, indem Sie die [INTO-Klausel](/stream-analytics-query/into-azure-stream-analytics) verwenden. Sie können eine einzelne Ausgabe pro Auftrag oder (bei Bedarf) auch mehrere Ausgaben pro Streamingauftrag verwenden, indem Sie der Abfrage mehrere INTO-Klauseln hinzufügen.

Zum Erstellen, Bearbeiten und Testen von Stream Analytics-Auftragsausgaben können Sie das [Azure-Portal](stream-analytics-quick-create-portal.md#configure-job-output), [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), die [.NET-API](/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations), die [REST-API](/rest/api/streamanalytics/) und [Visual Studio](stream-analytics-quick-create-vs.md) verwenden.

Für einige Ausgabetypen wird die [Partitionierung](#partitioning) unterstützt, und die [Ausgabebatchgrößen](#output-batch-size) variieren, um den Durchsatz zu optimieren. Die folgende Tabelle zeigt die Features, die für die jeweiligen Ausgabetypen unterstützt werden:

| Ausgabetyp | Partitionierung | Sicherheit | 
|-------------|--------------|----------|
|[Azure Data Lake Storage Gen 1](azure-data-lake-storage-gen1-output.md)|Ja|Azure Active Directory-Benutzer </br> MSI|
|[Azure SQL-Datenbank](sql-database-output.md)|Ja, optional|SQL-Benutzerauthentifizierung </br> MSI (Vorschau)|
|[Azure Synapse Analytics](azure-synapse-analytics-output.md)|Ja|SQL-Benutzerauthentifizierung|
|[Blob Storage und Azure Data Lake Gen 2](blob-storage-azure-data-lake-gen2-output.md)|Ja|MSI </br> Zugriffsschüssel|
|[Azure Event Hubs](event-hubs-output.md)|Ja, Sie müssen die Partitionsschlüsselspalte in der Ausgabekonfiguration festlegen.|Zugriffsschüssel|
|[Power BI](power-bi-output.md)|Nein|Azure Active Directory-Benutzer </br> MSI|
|[Azure Table Storage](table-storage-output.md)|Ja|Kontoschlüssel|
|[Azure Service Bus-Warteschlangen](service-bus-queues-output.md)|Ja|Zugriffsschüssel|
|[Azure Service Bus-Themen](service-bus-topics-output.md)|Ja|Zugriffsschüssel|
|[Azure Cosmos DB](azure-cosmos-db-output.md)|Ja|Zugriffsschüssel|
|[Azure-Funktionen](azure-functions-output.md)|Ja|Zugriffsschüssel|

## <a name="partitioning"></a>Partitionierung

Stream Analytics unterstützt Partitionen für alle Ausgaben außer für Power BI. Weitere Informationen zu Partitionsschlüsseln und der Anzahl von Ausgabeschreibern finden Sie im Artikel zum jeweiligen Ausgabetyp. In der Tabelle oben finden Sie Links zu den Artikeln für die verschiedenen Ausgabetypen.  

Wenn Sie die Partitionen zusätzlich weiter optimieren möchten, können Sie die Anzahl von Ausgabeschreibern mithilfe einer `INTO <partition count>`-Klausel (siehe [INTO](/stream-analytics-query/into-azure-stream-analytics#into-shard-count)) in Ihrer Abfrage steuern. Dies kann hilfreich sein, um die gewünschte Auftragstopologie zu erzielen. Wenn der Ausgabeadapter nicht partitioniert ist, führt das Fehlen von Daten in einer Eingabepartition zu einer Verzögerung. Diese kann die gesamte Zeitspanne der Eingangsverzögerung umfassen. In solchen Fällen wird die Ausgabe in einen einzigen Writer zusammengeführt, was Engpässe in Ihrer Pipeline verursachen kann. Weitere Informationen zur Richtlinie bei Eingangsverzögerung finden Sie unter [Überlegungen zur Ereignisreihenfolge in Azure Stream Analytics](./stream-analytics-time-handling.md).

## <a name="output-batch-size"></a>Ausgabebatchgröße

Alle Ausgaben unterstützen die Batchverarbeitung, aber nur einige unterstützen die explizite Angabe von Batchgrößen. Für Azure Stream Analytics werden Batches mit variabler Größe verwendet, um Ereignisse zu verarbeiten und in Ausgaben zu schreiben. Normalerweise schreibt das Stream Analytics-Modul nicht nur jeweils eine Nachricht, sondern Batches, um die Effizienz zu steigern. Wenn die Rate der eingehenden und ausgehenden Ereignisse hoch ist, verwendet Stream Analytics größere Batches. Falls die Ausgangsrate niedrig ist, werden kleinere Batches genutzt, um die Latenz gering zu halten.

## <a name="parquet-output-batching-window-properties"></a>Eigenschaften des Zeitfensters der Batchverarbeitung für Parquet-Ausgaben

Bei Bereitstellungen über Azure Resource Manager-Vorlagen oder die REST-API stehen zwei Eigenschaften für das Zeitfenster der Batchverarbeitung zur Verfügung:

1. *timeWindow*

   Die maximale Wartezeit pro Batch. Dieser Wert sollte eine Zeichenfolge für die Zeitspanne sein. Beispiel: „00:02:00“ für zwei Minuten. Nach Ablauf dieser Zeit wird der Batch auch dann in die Ausgabe geschrieben, wenn die Anforderung an die Mindestanzahl von Zeilen nicht erfüllt ist. Der Standardwert beträgt 1 Minute, der zulässige Höchstwert 2 Stunden. Wenn Ihre Blobausgabe eine Pfadmusterhäufigkeit aufweist, kann die Wartezeit nicht über dem Partitionszeitbereich liegen.

2. *sizeWindow*

   Die minimale Zeilenanzahl pro Batch. Für Parquet erstellt jeder Batch eine neue Datei. Der aktuelle Standardwert beträgt 2.000 Zeilen und der zulässige Höchstwert 10.000 Zeilen.

Diese Eigenschaften des Zeitfensters der Batchverarbeitung werden nur von der API-Version **2017-04-01-preview** unterstützt. Nachstehend finden Sie ein Beispiel für die JSON-Nutzdaten für einen REST-API-Aufruf:

```json
"type": "stream",
      "serialization": {
        "type": "Parquet",
        "properties": {}
      },
      "timeWindow": "00:02:00",
      "sizeWindow": "2000",
      "datasource": {
        "type": "Microsoft.Storage/Blob",
        "properties": {
          "storageAccounts" : [
          {
            "accountName": "{accountName}",
            "accountKey": "{accountKey}",
          }
          ],
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
>
> [Schnellstart: Erstellen eines Stream Analytics-Auftrags mithilfe des Azure-Portals](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/