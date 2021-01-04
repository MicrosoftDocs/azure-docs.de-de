---
title: Senkentransformation in einem Zuordnungsdatenfluss
description: Hier erfahren Sie, wie Sie im Zuordnungsdatenfluss eine Senkentransformation konfigurieren.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/08/2020
ms.openlocfilehash: 242249e3ab7fbedf9f19f3cb9a49fc4a8359f4ae
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929343"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Senkentransformation in einem Zuordnungsdatenfluss

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Nachdem Sie die Datentransformation abgeschlossen haben, schreiben Sie sie mithilfe der Senkentransformation in einen Zielspeicher. Jeder Datenfluss erfordert mindestens eine Senkentransformation. Sie können aber so viele Senken wie erforderlich hinzufügen, um Ihren Transformationsfluss zu realisieren. Um in weitere Senken zu schreiben, erstellen Sie über neue Verzweigungen und bedingte Teilungen neue Datenströme.

Jede Senkentransformation ist genau einem Azure Data Factory-Datasetobjekt oder einem verknüpften Dienst zugeordnet. Die Form und Position der Daten, in die geschrieben werden soll, richtet sich nach der Senkentransformation.

## <a name="inline-datasets"></a>Inlinedatasets

Beim Erstellen einer Senkentransformation legen Sie fest, ob Ihre Senkeninformationen innerhalb eines Datasetobjekts oder innerhalb der Senkentransformation definiert sind. Die meisten Formate sind nur in einem von beiden verfügbar. Weitere Informationen zur Verwendung eines bestimmten Connectors finden Sie im Dokument zu diesem Connector.

Wenn ein Format sowohl für Inline- als auch in einem Datasetobjekt unterstützt wird, hat beides seine Vorteile. Datasetobjekte sind wiederverwendbare Entitäten, die in anderen Datenflüssen und Aktivitäten wie Kopiervorgängen genutzt werden können. Diese wiederverwendbaren Entitäten sind besonders nützlich, wenn Sie ein verstärktes Schema verwenden. Datasets befinden sich nicht in Spark. Gelegentlich müssen Sie bestimmte Einstellungen oder Schemaprojektionen in der Senkentransformation überschreiben.

Inlinedatasets werden empfohlen, wenn flexible Schemas, einmalig genutzte Senkeninstanzen oder parametrisierte Senken verwendet werden. Wenn Ihre Senke stark parametrisiert ist, können Sie mit Inline-Datasets kein „Dummy“-Objekt erstellen. Inline-Datasets befinden sich in Spark, und ihre Eigenschaften sind für den Datenfluss nativ.

Um ein Inline-Dataset zu verwenden, wählen Sie das gewünschte Format im Selektor **Senkentyp** aus. Anstatt ein Senkendataset auszuwählen, wählen Sie den verknüpften Dienst aus, mit dem Sie eine Verbindung herstellen möchten.

![Screenshot mit hervorgehobener Auswahl von „Inline“.](media/data-flow/inline-selector.png "Screenshot mit hervorgehobener Auswahl von „Inline“")

##  <a name="supported-sink-types"></a><a name="supported-sinks"></a> Unterstützte Senkentypen

Der Zuordnungsdatenfluss folgt einem Ansatz zum Extrahieren, Laden und Transformieren (ELT) und funktioniert mit *Stagingdatasets* in Azure. Derzeit können die folgenden Datasets in einer Quelltransformation verwendet werden.

| Connector | Format | Dataset/Inline |
| --------- | ------ | -------------- |
| [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties) <br>[Text mit Trennzeichen](format-delimited-text.md#mapping-data-flow-properties) <br>[Delta](format-delta.md) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>✓/- <br>-/✓ <br>✓/- <br>✓/✓<br>✓/- |
| [Azure Cosmos DB (SQL-API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties) <br>[Text mit Trennzeichen](format-delimited-text.md#mapping-data-flow-properties) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>✓/- <br>✓/- <br>✓/✓<br>✓/- |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties) <br/>[Common Data Model](format-common-data-model.md#sink-properties)<br>[Text mit Trennzeichen](format-delimited-text.md#mapping-data-flow-properties) <br>[Delta](format-delta.md) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>-/✓ <br>✓/- <br>-/✓ <br>✓/-<br>✓/✓ <br>✓/- |
| [Azure-Datenbank für PostgreSQL](connector-azure-database-for-postgresql.md) |  | ✓/✓ |
| [Azure SQL-Datenbank](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Azure SQL Managed Instance (Vorschau)](connector-azure-sql-managed-instance.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

Die für diese Connectors spezifischen Einstellungen befinden sich auf der Registerkarte **Einstellungen**. Informationen und Beispiele zu Datenflussskripts zu diesen Einstellungen finden Sie in der Connectordokumentation.

Azure Data Factory hat Zugriff auf mehr als [90 native Connectors](connector-overview.md). Um Daten aus Ihrem Datenfluss in diese anderen Quellen zu schreiben, verwenden Sie die Kopieraktivität zum Laden der Daten aus einer unterstützten Senke.

## <a name="sink-settings"></a>Senkeneinstellungen

Nachdem Sie eine Senke hinzugefügt haben, konfigurieren Sie sie auf der Registerkarte **Senke**. Hier können Sie das Dataset auswählen oder erstellen, in das die Senke schreibt. Entwicklungswerte für Datasetparameter können in [Debugeinstellungen](concepts-data-flow-debug-mode.md) konfiguriert werden. (Der Debugmodus muss aktiviert sein.)

Im folgenden Video werden verschiedene Senkenoptionen für Dateitypen mit Texttrennzeichen beschrieben.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4tf7T]

![Screenshot mit Hervorhebung von „Senkeneinstellungen“.](media/data-flow/sink-settings.png "Screenshot mit Hervorhebung von „Senkeneinstellungen“.")

**Schemaabweichung**: [Schemaabweichung](concepts-data-flow-schema-drift.md) ist die Fähigkeit von Data Factory, flexible Schemas in Ihren Datenflüssen nativ zu verarbeiten, ohne Spaltenänderungen explizit definieren zu müssen. Aktivieren Sie **Schemaabweichung zulassen**, wenn Sie zusätzliche Spalten zusätzlich zur Definition im Datenschema der Senke schreiben möchten.

**Schema überprüfen**: Wenn „Schema überprüfen“ ausgewählt ist, führt der Datenfluss zu einem Fehler, wenn eine der Spalten des Eingangsquellschemas in der Quellprojektion nicht gefunden wird oder wenn die Datentypen nicht übereinstimmen. Verwenden Sie diese Einstellung, um eine Übereinstimmung der Quelldaten mit dem Vertrag Ihrer definierten Projektion zu erzwingen. Dies ist in Szenarien mit Datenbankquellen nützlich, um anzugeben, dass die Spaltennamen oder -typen geändert wurden.

## <a name="cache-sink"></a>Cachesenke

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4HKt1]

Von einer *Cachesenke* wird dann gesprochen, wenn Daten von einem Datenfluss in den Spark-Cache statt in einen Datenspeicher geschrieben werden. Bei Zuordnungsdatenflüssen kann mithilfe einer *Cachesuche* mehrmals innerhalb desselben Datenflusses auf diese Daten verwiesen werden. Dies ist nützlich, wenn Sie auf Daten als Teil eines Ausdrucks verweisen möchten, die Spalten jedoch nicht explizit damit verknüpfen möchten. Gängige Beispiele, in denen eine Cachesenke hilfreich sein kann, ist das Suchen eines Höchstwerts in einem Datenspeicher und das Abgleichen von Fehlercodes mit einer Fehlermeldungsdatenbank. 

Um in eine Cachesenke zu schreiben, fügen Sie eine Senkentransformation hinzu, und wählen Sie **Cache** als Senkentyp aus. Im Gegensatz zu anderen Senkentypen müssen Sie kein Dataset oder keinen verknüpften Dienst auswählen, da Sie nicht in einen externen Speicher schreiben. 

![Auswählen der Cachesenke](media/data-flow/select-cache-sink.png "Auswählen der Cachesenke")

In den Senkeneinstellungen können Sie optional die Schlüsselspalten der Cachesenke angeben. Diese werden bei Verwendung der Funktion `lookup()` in einer Cachesuche als Übereinstimmungsbedingungen verwendet. Wenn Sie Schlüsselspalten angeben, können Sie die Funktion `outputs()` nicht in einer Cachesuche verwenden. Weitere Informationen zur Syntax für die Cachesuche finden Sie unter [zwischengespeicherten Suchen](concepts-data-flow-expression-builder.md#cached-lookup).

![Schlüsselspalten der Cachesenke](media/data-flow/cache-sink-key-columns.png "Schlüsselspalten der Cachesenke")

Wenn Sie beispielsweise eine einzelne Schlüsselspalte `column1` in einer Cachesenke namens `cacheExample` angeben, wird beim Aufruf von `cacheExample#lookup()` mit einem Parameter festgelegt, mit welcher Zeile in der Cachesenke der Vergleich durchgeführt werden soll. Die Funktion gibt eine einzelne komplexe Spalte mit Unterspalten für jede zugeordnete Spalte aus.

> [!NOTE]
> Eine Cachesenke muss sich in einem vollständig unabhängigen Datenstrom von allen Transformationen befinden, die über eine Cachesuche darauf verweisen. Eine Cachesenke muss außerdem als erste Senke geschrieben werden. 

## <a name="field-mapping"></a>Feldzuordnung

Ähnlich wie bei einer Auswahltransformation können Sie auf der Registerkarte **Zuordnung** der Senke festlegen, welche eingehenden Spalten geschrieben werden. Standardmäßig werden alle Eingabespalten – auch abweichende Spalten – zugeordnet. Dieses Verhalten wird als *automatische Zuordnung* bezeichnet.

Wenn Sie die automatische Zuordnung deaktivieren, können Sie feste spaltenbasierte Zuordnungen oder regelbasierte Zuordnungen hinzuzufügen. Bei regelbasierten Zuordnungen können Sie Ausdrücke mit Musterabgleich schreiben. Bei der festen Zuordnung werden logische und physische Spaltennamen zugeordnet. Weitere Informationen zur regelbasierten Zuordnung finden Sie unter [Spaltenmuster im Zuordnungsdatenfluss](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink).

## <a name="custom-sink-ordering"></a>Benutzerdefinierte Senkenreihenfolge

Standardmäßig werden Daten ohne festgeschriebene Reihenfolge in mehrere Senken geschrieben. Die Ausführungs-Engine schreibt im Verlauf der Transformationslogik Daten parallel. Dabei kann bei jeder Ausführung die Senkenreihenfolge variieren. Um die genaue Reihenfolge der Senken anzugeben, aktivieren Sie auf der Registerkarte **Allgemein** des Datenflusses **Benutzerdefinierte Senkenreihenfolge**. Falls aktiviert, erfolgt in den Senken das Schreiben sequenziell in aufsteigender Reihenfolge.

![Screenshot mit Hervorhebung von „Benutzerdefinierte Senkenreihenfolge“](media/data-flow/custom-sink-ordering.png "Screenshot mit Hervorhebung von „Benutzerdefinierte Senkenreihenfolge“")

> [!NOTE]
> Bei Verwendung von [zwischengespeicherten Suchen](./concepts-data-flow-expression-builder.md#cached-lookup) müssen Sie sicherstellen, dass die zwischengespeicherten Senken in der Senkenreihenfolge auf 1 festgelegt sind, wobei es sich um den niedrigsten (oder ersten) Rang handelt.

![Benutzerdefinierte Senkenreihenfolge](media/data-flow/cache-2.png "Benutzerdefinierte Senkenreihenfolge")

### <a name="sink-groups"></a>Senkengruppen

Sie können Senken gruppieren, indem Sie für eine Reihe von Senken die gleiche Auftragsnummer anwenden. ADF behandelt diese Senken als Gruppen, die parallel ausgeführt werden können. Optionen für die parallele Ausführung werden in der Pipeline-Datenflussaktivität angezeigt.

## <a name="error-row-handling"></a>Fehlerzeilenbehandlung

Beim Schreiben in Datenbanken können aufgrund von Einschränkungen durch das Ziel Fehler bei bestimmten Datenzeilen auftreten. Standardmäßig scheitert eine Datenflussausführung beim ersten erhaltenen Fehler. In bestimmten Connectors können Sie eine Option **Bei Fehler fortsetzen** auswählen, die einen Abschluss des Datenflusses auch dann ermöglicht, wenn einzelne Zeilen Fehler aufweisen. Diese Funktion ist derzeit nur in Azure SQL-Datenbank verfügbar. Weitere Informationen finden Sie unter [Fehlerzeilenbehandlung in Azure SQL-Datenbank](connector-azure-sql-database.md#error-row-handling).

Es folgt ein Videotutorial zur automatischen Verwendung der Fehlerzeilenbehandlung für Datenbanken in Ihrer Senkentransformation.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4IWne]

## <a name="data-preview-in-sink"></a>Datenvorschau in Senken

Beim Abrufen einer Datenvorschau in einem Debugcluster werden keine Daten in die Senke geschrieben. Es wird eine Momentaufnahme der Daten zurückgegeben, aber nichts in das Ziel geschrieben. Um das Schreiben von Daten in die Senke zu überprüfen, führen Sie einen Pipelinedebugvorgang in der Pipelinecanvas aus.

## <a name="next-steps"></a>Nächste Schritte

Da Sie nun Ihren Datenfluss erstellt haben, fügen Sie [Ihrer Pipeline eine Aktivität zur Ausführung eines Datenflusses](concepts-data-flow-overview.md) hinzu.