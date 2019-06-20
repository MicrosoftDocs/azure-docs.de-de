---
title: Datasets in Azure Data Factory | Microsoft-Dokumentation
description: Informationen zu Datasets in Data Factory. Datasets stellen Eingabe/Ausgabe-Daten dar.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: shlo
ms.openlocfilehash: 6b74f217d296b5de8886f608b1bc92e908b5d8b4
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866478"
---
# <a name="datasets-in-azure-data-factory"></a>Datasets in Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-create-datasets.md)
> * [Aktuelle Version](concepts-datasets-linked-services.md)

In diesem Artikel ist beschrieben, was Datasets sind, wie sie im JSON-Format definiert werden, und wie sie in Azure Data Factory-Pipelines verwendet werden.

Wenn Sie noch nicht mit Data Factory vertraut sind, sollten Sie [Einführung in Azure Data Factory](introduction.md) lesen, um eine Übersicht zu erhalten.

## <a name="overview"></a>Übersicht
Eine Data Factory kann eine oder mehrere Pipelines haben. Bei einer **Pipeline** handelt es sich um eine logische Gruppierung von **Aktivitäten**, die zusammen eine Aufgabe bilden. Die Aktivitäten in einer Pipeline definieren Aktionen, die Sie auf Ihre Daten anwenden. Ein **Datensatz** ist eine benannte Ansicht von Daten, die einfach auf die Daten verweist, die Sie in Ihren **Aktivitäten** als Ein- und Ausgabe verwenden möchten. Datasets bestimmen Daten in verschiedenen Datenspeichern, z.B. Tabellen, Dateien, Ordnern und Dokumenten. Ein Azure-Blobdataset kann beispielsweise den Blobcontainer und -ordner in Blob Storage angeben, aus dem die Aktivität die Daten lesen soll.

Bevor Sie ein Dataset erstellen, müssen Sie einen [**verknüpften Dienst**](concepts-linked-services.md) erstellen, um Ihren Datenspeicher mit der Data Factory zu verknüpfen. Verknüpfte Dienste ähneln Verbindungszeichenfolgen, mit denen die Verbindungsinformationen definiert werden, die für Data Factory zum Herstellen einer Verbindung mit externen Ressourcen erforderlich sind. Sie können sich dies wie folgt vorstellen: Das Dataset stellt die Struktur der Daten innerhalb des verknüpften Datenspeichers dar, und der verknüpfte Dienst definiert die Verbindung mit der Datenquelle. Ein mit Azure Storage verknüpfter Dienst verbindet z.B. ein Speicherkonto mit der Data Factory. Ein Azure-Blob-Dataset stellt den Blobcontainer und den Ordner innerhalb des Azure-Speicherkontos dar, das die zu verarbeitenden Eingabeblobs enthält.

Hier ist ein Beispielszenario. Um Daten aus Blob Storage in eine SQL-Datenbank zu kopieren, erstellen Sie zwei verknüpfte Dienste: Azure Storage und Azure SQL-Datenbank. Erstellen Sie anschließend zwei Datasets: Azure-Blobdataset (das sich auf den mit Azure Storage verknüpften Dienst bezieht) und Azure SQL-Tabellendataset (das sich auf den mit Azure SQL-Datenbank verknüpften Dienst bezieht). Die mit Azure Storage und Azure SQL-Datenbank verknüpften Dienste enthalten Verbindungszeichenfolgen, die Data Factory zur Laufzeit verwendet, um die Verbindung mit Ihrem Azure Storage bzw. mit Ihrer Instanz von Azure SQL-Datenbank herzustellen. Das Azure-Blobdataset gibt den Blobcontainer und Blobordner an, der die Eingabeblobs in Ihrer Blob Storage-Instanz enthält. Das Azure SQL-Tabellendataset gibt die SQL-Tabelle in Ihrer SQL-Datenbank an, in die die Daten kopiert werden sollen.

Das folgende Diagramm zeigt die Beziehung zwischen Pipeline, Aktivität, Dataset und verknüpftem Dienst in der Data Factory an:

![Beziehung zwischen Pipeline, Aktivität, Dataset und verknüpften Diensten](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>JSON-DataSet
Ein Dataset in Data Factory wird im folgenden JSON-Format definiert:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```
In der folgenden Tabelle werden die Eigenschaften im obigen JSON-Code beschrieben:

Eigenschaft | BESCHREIBUNG | Erforderlich |
-------- | ----------- | -------- |
name | Name des Datasets. Siehe [Azure Data Factory – Benennungsregeln](naming-rules.md). |  Ja |
type | Typ des Datasets. Geben Sie einen der von Data Factory unterstützten Typen an (z.B. AzureBlob, AzureSqlTable). <br/><br/>Weitere Informationen finden Sie unter [Datasettyp](#dataset-type). | Ja |
structure | Schema des Datasets. Weitere Informationen finden Sie unter [Datasetschema](#dataset-structure-or-schema). | Nein |
typeProperties | Die Typeigenschaften unterscheiden sich je nach Typ (z.B. Azure-Blob, Azure SQL-Tabelle). Ausführliche Informationen über die unterstützten Typen und deren Eigenschaften finden Sie unter [Dataset: type](#dataset-type). | Ja |

### <a name="data-flow-compatible-dataset"></a>Data Flow-kompatibles Dataset

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Eine Liste der mit dem [Datenfluss](concepts-data-flow-overview.md) kompatiblen Datasettypen, finden Sie unter [Unterstützte Datasettypen](#dataset-type). Mit dem Datenfluss kompatible Datasets erfordern detaillierte Datasetdefinitionen für Transformationen. Daher variiert die JSON-Definition leicht. Anstelle einer _structure_-Eigenschaft verfügen Datasets, die mit dem Datenfluss kompatibel sind, über eine _schema_-Eigenschaft.

Datasets im Datenfluss werden in Transformationen von Quellen und Senken verwendet. Die Datasets definieren die grundlegenden Datenschemas. Wenn Ihre Daten kein Schema aufweisen, können Sie die Schemaabweichung für Ihre Quelle und Senke verwenden. Das Schema im Dataset stellt den physischen Datentyp und die Form dar.

Wenn Sie das Schema über das Dataset definieren, erhalten Sie die zugehörigen Datentypen, Datenformate, Dateispeicherort und Verbindungsinformationen aus dem zugehörigen verknüpften Dienst. Metadaten aus den Datasets werden in Ihrer Quelltransformation als *Projektion* der Quelle angezeigt. Die Projektion in der Quelltransformation stellt die Datenflussdaten mit definierten Namen und Typen dar.

Wenn Sie das Schema eines Datenflussdatasets importieren, wählen Sie die Schaltfläche **Schema importieren** und anschließend das Importieren aus der Quelle oder aus einer lokalen Datei aus. In den meisten Fällen importieren Sie das Schema direkt aus der Quelle. Wenn Sie jedoch bereits über eine lokale Schemadatei (eine Parquet-Datei oder CSV-Datei mit Headern) verfügen, können Sie Data Factory anweisen, das Schema auf dieser Datei aufzubauen.


```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "schema": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```

In der folgenden Tabelle werden die Eigenschaften im obigen JSON-Code beschrieben:

Eigenschaft | BESCHREIBUNG | Erforderlich |
-------- | ----------- | -------- |
name | Name des Datasets. Siehe [Azure Data Factory – Benennungsregeln](naming-rules.md). |  Ja |
type | Typ des Datasets. Geben Sie einen der von Data Factory unterstützten Typen an (z.B. AzureBlob, AzureSqlTable). <br/><br/>Weitere Informationen finden Sie unter [Datasettyp](#dataset-type). | Ja |
schema | Schema des Datasets. Weitere Informationen finden Sie unter [Datenfluss-kompatible Datasets](#dataset-type). | Nein |
typeProperties | Die Typeigenschaften unterscheiden sich je nach Typ (z.B. Azure-Blob, Azure SQL-Tabelle). Ausführliche Informationen über die unterstützten Typen und deren Eigenschaften finden Sie unter [Dataset: type](#dataset-type). | Ja |


## <a name="dataset-example"></a>Datasetbeispiel
Im folgenden Beispiel stellt das Dataset eine Tabelle namens MyTable in einer SQL-Datenbank dar.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": {
                "referenceName": "MyAzureSqlLinkedService",
                "type": "LinkedServiceReference",
        },
        "typeProperties":
        {
            "tableName": "MyTable"
        },
    }
}

```
Beachten Sie folgende Punkte:

- „type“ ist auf „AzuresqlTable“ festgelegt.
- Als tableName-Typeigenschaft (spezifisch für AzureSplTable-Typ) ist „MyTable“ festgelegt.
- linkedServiceName verweist auf einen verknüpften Dienst vom Typ AzureSqlDatabase, der im nächsten JSON-Codeausschnitt definiert ist.

## <a name="dataset-type"></a>Datasettyp
Es gibt viele verschiedene Typen von Datasets, je nach eingesetztem Datenspeicher. In der folgenden Tabelle finden Sie eine Liste der von Data Factory unterstützten Datenspeicher. Klicken Sie auf einen Datenspeicher, um Informationen zum Erstellen eines verknüpften Diensts und eines Datasets für diesen Datenspeicher zu erhalten.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-dataflow.md)]

Im vorherigen Abschnitt ist im Beispiel der Typ des Datasets auf **AzureSqlTable** festgelegt. Ebenso ist für ein Azure-Blobdataset der Typ des Datasets auf **AzureBlob** festgelegt, wie im folgenden JSON-Code dargestellt:

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
                "referenceName": "MyAzureStorageLinkedService",
                "type": "LinkedServiceReference",
        },

        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        }
    }
}
```

## <a name="dataset-structure-or-schema"></a>Datasetstruktur oder -schema
Die Datasets des Abschnitts **structure** oder **schema** (Datenfluss-kompatibel) sind optional. In ihm ist das Schema des Datasets in einer Sammlung der Namen und Datentypen der Spalten definiert. Im Abschnitt „structure“ legen Sie Typinformationen fest, die zum Konvertieren von Typen und Zuordnen von Spalten von der Quelle zum Ziel bereitgestellt werden.

Jede Spalte im Abschnitt „structure“ enthält die folgenden Eigenschaften:

Eigenschaft | BESCHREIBUNG | Erforderlich
-------- | ----------- | --------
name | Name der Spalte. | Ja
type | Datentyp der Spalte. Data Factory unterstützt die folgenden Zwischendatentypen als zulässige Werte: **„Int16“, „Int32“, „Int64“, „Single“, „Double“, „Decimal“, „Byte[]“, „Boolean“, „String“, „Guid“, „Datetime“, „Datetimeoffset“ und „Timespan“** . | Nein
culture | Zu verwendendes .NET-basiertes Gebietsschema, wenn der Typ ein .NET-Typ ist: `Datetime` oder `Datetimeoffset`. Der Standardwert lautet `en-us`. | Nein
format | Zu verwendende Formatzeichenfolge, wenn der Typ ein .NET-Typ ist: `Datetime` oder `Datetimeoffset`. Informationen zum Formatieren von Datum und Uhrzeit finden Sie unter [Benutzerdefinierte Formatzeichenfolgen für Datum und Uhrzeit](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). | Nein

### <a name="example"></a>Beispiel
Nehmen Sie für das folgende Beispiel an, dass die Quellblobdaten im CSV-Format vorliegen und drei Spalten aufweisen: „userid“, „name“ und „lastlogindate“. Sie haben den Typ „Int64“, „String“ bzw. „Datetime“ mit einem benutzerdefinierten datetime-Format mit abgekürzten französischen Namen für die Wochentage.

Definieren Sie die Blobdatasetstruktur wie folgt zusammen mit Typdefinitionen für die Spalten:

```json
"structure":
[
    { "name": "userid", "type": "Int64"},
    { "name": "name", "type": "String"},
    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
]
```

### <a name="guidance"></a>Anleitungen

Anhand der folgenden Anleitungen können Sie erkennen, wann der Abschnitt **structure** mit welchen Informationen verwendet werden sollte. Hier finden Sie weitere Informationen darüber, wie Data Factory Quelldaten zu Senken zuordnet und wann Strukturinformationen der [Schema- und Typzuordnung](copy-activity-schema-and-type-mapping.md) angegeben werden müssen.

- **Für Datenquellen mit festem Schema** geben Sie den Abschnitt „structure“ nur an, wenn Sie Quellspalten zu Senkenspalten zuordnen möchten und die Spaltennamen nicht identisch sind. Diese Art von strukturierter Datenquelle speichert Informationen zu Datenschema und Datentyp zusammen mit den Daten selbst. Zu Beispielen für strukturierte Datenquellen gehören SQL Server, Oracle und Azure SQL-Datenbank.<br/><br/>Da Typinformationen für strukturierte Datenquellen bereits verfügbar sind, sollten Sie keine Typinformationen einschließen, wenn Sie sich für die Verwendung des Abschnitts „structure“ entscheiden.
- **Geben Sie für Datenquellen ohne Schema oder mit schwachem Schema, z.B. eine Textdatei im Blobspeicher**, „structure“ auch an, wenn das Dataset eine Eingabe für eine Kopieraktivität ist und die Datentypen des Quelldatasets für die Senke in native Typen konvertiert werden müssen. Geben Sie „structure“ darüber hinaus an, wenn Sie Quellspalten zu Senkenspalten zuordnen möchten.

## <a name="create-datasets"></a>Erstellen von Datasets
Datasets können Sie mit einem dieser Tools oder SDKs erstellen: [.NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [REST-API](quickstart-create-data-factory-rest-api.md), Azure Resource Manager-Vorlage und Azure-Portal

## <a name="current-version-vs-version-1-datasets"></a>Gegenüberstellung von Datasets der aktuellen Version und Datasets der Version 1

Im Anschluss sind einige Unterschiede zwischen Data Factory-Datasets und Data Factory-Datasets der Version 1aufgeführt:

- Die externe Eigenschaft wird in der aktuellen Version nicht unterstützt. Sie wird durch einen [Trigger](concepts-pipeline-execution-triggers.md) ersetzt.
- Die Richtlinien- und Verfügbarkeitseigenschaften werden in der aktuellen Version nicht unterstützt. Die Startzeit für eine Pipeline hängt von [Triggern](concepts-pipeline-execution-triggers.md) ab.
- Bereichsbezogene (in einer Pipeline definierte) Datasets werden in der aktuellen Version nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte
Im folgenden Tutorial finden Sie schrittweise Anleitungen zum Erstellen von Pipelines und Datasets mit einem dieser Tools oder SDKs.

- [Schnellstart: Erstellen einer Data Factory mithilfe von .NET](quickstart-create-data-factory-dot-net.md)
- [Schnellstart: Erstellen einer Data Factory mithilfe von PowerShell](quickstart-create-data-factory-powershell.md)
- [Schnellstart: Erstellen einer Data Factory mithilfe der REST-API](quickstart-create-data-factory-rest-api.md)
- [Schnellstart: Erstellen einer Data Factory mithilfe des Azure-Portals](quickstart-create-data-factory-portal.md)
