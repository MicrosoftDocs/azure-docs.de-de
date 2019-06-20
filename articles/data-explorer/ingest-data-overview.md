---
title: Datenerfassung im Azure-Daten-Explorer
description: Erfahren Sie mehr über die verschiedenen Möglichkeiten, wie Sie Daten im Azure-Daten-Explorer erfassen (laden) können.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 891d2acc42f8d6f03976f0553e2e3127bc6d16f7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60759323"
---
# <a name="azure-data-explorer-data-ingestion"></a>Datenerfassung im Azure-Daten-Explorer

Die Datenerfassung ist der Prozess, mit dem Datensätze aus einer oder mehreren Quellen geladen werden, um eine Tabelle im Azure-Daten-Explorer zu erstellen oder zu aktualisieren. Nach der Erfassung sind die Daten für Abfragen verfügbar. Das folgende Diagramm zeigt den vollständigen Ablauf für die Arbeit im Azure-Daten-Explorer, einschließlich der Datenerfassung.

![Datenfluss](media/ingest-data-overview/data-flow.png)

Der Datenverwaltungsdienst von Azure-Daten-Explorer, der für die Datenerfassung zuständig ist, bietet die folgenden Funktionen:

1. **Datenabruf**: Abrufen von Daten aus externen Quellen (Event Hubs) oder Lesen von Erfassungsanforderungen aus einer Azure-Warteschlange.

1. **Batchverarbeitung**: Batchdaten, die an dieselbe Datenbank und Tabelle gesendet werden, um den Durchsatz der Datenerfassung zu optimieren.

1. **Validierung**: Vorläufige Validierung und Formatkonvertierung, falls erforderlich.

1. **Datenbearbeitung**: Abgleich des Schemas, Organisation, Indizierung, Codierung und Komprimierung der Daten.

1. **Persistenzpunkt im Erfassungsablauf**: Verwalten der Erfassungslast der Engine und Verarbeiten von Wiederholungsversuchen bei vorübergehenden Ausfällen.

1. **Committen der Datenerfassung**: Stellt die Daten für Abfragen zur Verfügung.

## <a name="ingestion-methods"></a>Erfassungsmethoden

Azure-Daten-Explorer unterstützt mehrere Erfassungsmethoden mit jeweils eigenen Zielszenarien, Vor- und Nachteilen. Azure-Daten-Explorer bietet Pipelines und Konnektoren für gängige Dienste, programmgesteuerte Erfassung über SDKs und direkten Zugriff auf die Engine für Untersuchungszwecke.

### <a name="ingestion-using-pipelines-connectors-and-plugins"></a>Erfassung mit Pipelines, Connectors und Plug-Ins

Azure Data Explorer unterstützt derzeit Folgendes:

* Event Grid-Pipeline, die mit dem Verwaltungs-Assistenten im Azure-Portal verwaltet werden kann. Weitere Informationen finden Sie unter [Erfassen von Azure-Blobs in Azure Data Explorer](ingest-data-event-grid.md).

* Event Hub-Pipeline, die mit dem Verwaltungs-Assistenten im Azure-Portal verwaltet werden kann. Weitere Informationen finden Sie unter [Erfassen von Daten aus Event Hub in Azure Data Explorer](ingest-data-event-hub.md).

* Logstash-Plug-In, siehe [Erfassen von Daten aus Logstash in Azure Data Explorer](ingest-data-logstash.md).

* Kafka-Connector, siehe [Erfassen von Daten aus Kafka in Azure Data Explorer](ingest-data-kafka.md).

### <a name="ingestion-using-integration-services"></a>Erfassung mit Integrationsdiensten

* Azure Data Factory (ADF) ist ein vollständig verwalteter Datenintegrationsdienst für Analyseworkloads in Azure, mit dem mithilfe [unterstützter Datenspeicher und -formate](/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats) Daten in und aus Azure Data Explorer kopiert werden können. Weitere Informationen finden Sie unter [Copy data to Azure Data Explorer using Azure Data Factory (Kopieren von Daten aus Azure Data Factory in Azure Data Explorer)](/azure/data-explorer/data-factory-load-data).

### <a name="programmatic-ingestion"></a>Programmgesteuerte Erfassung

Azure-Daten-Explorer bietet SDKs, die für Abfragen und die Datenerfassung verwendet werden können. Die programmgesteuerte Erfassung ist dafür optimiert, die Erfassungskosten (COGs) zu senken, indem Speichertransaktionen während und nach dem Erfassungsprozess minimiert werden.

**Verfügbare SDKs und Open-Source-Projekte**:

Kusto bietet Client SDKs, die zum Erfassen und Abfragen von Daten mit folgenden Programmen genutzt werden können:

* [Python SDK](/azure/kusto/api/python/kusto-python-client-library)

* [.NET SDK](/azure/kusto/api/netfx/about-the-sdk)

* [Java SDK](/azure/kusto/api/java/kusto-java-client-library)

* [Node SDK](/azure/kusto/api/node/kusto-node-client-library)

* [REST-API](/azure/kusto/api/netfx/kusto-ingest-client-rest)

**Techniken der programmgesteuerten Erfassung**:

* Erfassen von Daten über den Datenverwaltungsdienst von Azure-Daten-Explorer (hoher Durchsatz und zuverlässige Erfassung):

    [**Batch-Erfassung** ](/azure/kusto/api/netfx/kusto-ingest-queued-ingest-sample) (bereitgestellt vom SDK): Der Client lädt die Daten auf den Azure Blob Storage hoch (der vom Datenverwaltungsdienst des Azure-Daten-Explorers angegeben wird) und sendet eine Benachrichtigung an eine Azure-Warteschlange. Batch-Erfassung ist die empfohlene Technik für die hochvolumige, zuverlässige und kostengünstige Datenerfassung.

* Erfassen von Daten direkt in die Azure-Daten-Explorer-Engine (am besten geeignet für Untersuchungen und Prototyping):

  * **Inline-Erfassung**: Der Steuerbefehl (.ingest inline) mit In-Band-Daten ist für Ad-hoc-Tests vorgesehen.

  * **Erfassen aus der Abfrage**: Der Steuerbefehl (.set,.set,.set-or-append,.set-or-replace), der auf Abfrageergebnisse verweist, wird zum Erzeugen von Berichten oder kleinen temporären Tabellen verwendet.

  * **Erfassen aus dem Speicher**:Der Steuerbefehl (.ingest in) mit extern gespeicherten Daten (beispielsweise Azure Blob Storage) ermöglicht eine effiziente Sammelerfassung von Daten.

**Latenz der verschiedenen Methoden**:

| Methode | Latency |
| --- | --- |
| **Inline-Erfassung** | Unmittelbar |
| **Erfassen aus der Abfrage** | Abfragezeit + Verarbeitungszeit |
| **Erfassung aus dem Speicher** | Downloadzeit + Verarbeitungszeit |
| **Erfassen aus der Warteschlange** | Batchverarbeitungszeit + Verarbeitungszeit |
| |

Verarbeitungszeit hängt von der Größe der Daten ab – weniger als einige Sekunden. Die Batchverarbeitungszeit beträgt standardmäßig fünf Minuten.

## <a name="choosing-the-most-appropriate-ingestion-method"></a>Auswählen der optimalen Erfassungsmethode

Bevor Sie mit der Datenerfassung beginnen, sollten Sie sich folgende Fragen stellen.

* Wo befinden sich meine Daten? 
* In welchem Format liegen die Daten vor und kann das geändert werden? 
* Welche Felder müssen abgefragt werden? 
* Wie hoch ist die erwartete Datenmenge und die Geschwindigkeit? 
* Wie viele Ereignistypen werden erwartet (entsprechend der Anzahl von Tabellen)? 
* Wie oft wird eine Änderung des Ereignisschemas erwartet? 
* Wie viele Knoten werden die Daten generieren? 
* Welches Quell-BS wird verwendet? 
* Wie lauten die Latenzanforderungen? 
* Kann eine der vorhandenen verwalteten Erfassungspipelines verwendet werden? 

Für Organisationen mit einer vorhandenen Infrastruktur, die auf einem Messaging-Dienst wie Event Hub basieren, ist die Verwendung eines Connektors wahrscheinlich die am besten geeignete Lösung. Die Erfassung in der Warteschlange eignet sich für große Datenmengen.

## <a name="supported-data-formats"></a>Unterstützte Datenformate

Formatieren Sie die Daten für alle Erfassungsmethoden außer Erfassen aus der Abfrage so, dass sie von Azure Data Explorer analysiert werden können. Die folgenden Datenformate werden unterstützt:

* CSV, TSV, PSV, SCSV, SOH
* JSON (getrennte Zeilen, mehrzeilig) Avro
* ZIP und GZIP 

> [!NOTE]
> Beim Erfassen von Daten werden Datentypen aus den Spalten der Zieltabelle abgeleitet. Wenn ein Datensatz unvollständig ist oder ein Feld nicht als erforderlicher Datentyp analysiert werden kann, werden die entsprechenden Tabellenspalten mit Nullwerten gefüllt.

## <a name="ingestion-recommendations-and-limitations"></a>Erfassungsempfehlungen und -einschränkungen

* Die effektive Aufbewahrungsrichtlinie der erfassten Daten wird von der Aufbewahrungsrichtlinie der Datenbank abgeleitet. Weitere Informationen finden Sie in [Aufbewahrungsrichtlinie](/azure/kusto/concepts/retentionpolicy). Für das Erfassen von Daten sind Berechtigungen für einen **Tabelleningestor** oder einen **Datenbankingestor** erforderlich.
* Erfassung unterstützt eine maximale Dateigröße von 5 GB. Es wird empfohlen, Dateien zwischen 100 MB und 1 GB zu erfassen.

## <a name="schema-mapping"></a>Schemazuordnung

Die Schemazuordnung hilft dabei, Quelldatenfelder an Spalten der Zieltabelle zu binden.

* [CSV-Zuordnung](/azure/kusto/management/mappings?branch=master#csv-mapping) (optional) funktioniert mit allen ordinalbasierten Formaten. Sie kann über den Erfassungsbefehlsparameter vorgenommen oder [in der Tabelle vorab erstellt](/azure/kusto/management/tables?branch=master#create-ingestion-mapping) und vom Erfassungsbefehlsparameter referenziert werden.
* Die [JSON-Zuordnung](/azure/kusto/management/mappings?branch=master#json-mapping) (obligatorisch) und die [Avro-Zuordnung](/azure/kusto/management/mappings?branch=master#avro-mapping) (obligatorisch) können mit dem Erfassungsbefehlsparameter durchgeführt werden. Diese Vorgänge können auch [in der Tabelle vorab erstellt](/azure/kusto/management/tables#create-ingestion-mapping) und vom Erfassungsbefehlsparameter referenziert werden.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erfassen von Daten aus Event Hub in Azure Data Explorer](ingest-data-event-hub.md)

> [!div class="nextstepaction"]
> [Erfassen von Azure-Blobs in Azure Data Explorer durch Abonnieren von Event Grid-Benachrichtigungen](ingest-data-event-grid.md)

> [!div class="nextstepaction"]
> [Erfassen von Daten aus Kafka in Azure Data Explorer](ingest-data-kafka.md)

> [!div class="nextstepaction"]
> [Erfassen von Daten mit der Azure Data Explorer-Bibliothek für Python](python-ingest-data.md)

> [!div class="nextstepaction"]
> [Erfassen von Daten mit der Azure Data Explorer-Bibliothek für Node](node-ingest-data.md)

> [!div class="nextstepaction"]
> [Erfassen von Daten mit dem .NET Standard SDK für Azure Data Explorer (Vorschauversion)](net-standard-ingest-data.md)

> [!div class="nextstepaction"]
> [Erfassen von Daten aus Logstash in Azure Data Explorer](ingest-data-logstash.md)
