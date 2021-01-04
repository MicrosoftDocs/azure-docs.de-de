---
title: Datasets
description: Informationen zu Datasets in Data Factory. Datasets stellen Eingabe/Ausgabe-Daten dar.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/24/2020
ms.openlocfilehash: 6a9de6ff174adc5f6be9647560ad4e26367cb4ed
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500120"
---
# <a name="datasets-in-azure-data-factory"></a>Datasets in Azure Data Factory
> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](v1/data-factory-create-datasets.md)
> * [Aktuelle Version](concepts-datasets-linked-services.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


In diesem Artikel ist beschrieben, was Datasets sind, wie sie im JSON-Format definiert werden, und wie sie in Azure Data Factory-Pipelines verwendet werden.

Wenn Sie noch nicht mit Data Factory vertraut sind, sollten Sie [Einführung in Azure Data Factory](introduction.md) lesen, um eine Übersicht zu erhalten.

## <a name="overview"></a>Übersicht
Eine Data Factory kann eine oder mehrere Pipelines haben. Bei einer **Pipeline** handelt es sich um eine logische Gruppierung von **Aktivitäten**, die zusammen eine Aufgabe bilden. Die Aktivitäten in einer Pipeline definieren Aktionen, die Sie auf Ihre Daten anwenden. Ein **Datensatz** ist eine benannte Ansicht von Daten, die einfach auf die Daten verweist, die Sie in Ihren **Aktivitäten** als Ein- und Ausgabe verwenden möchten. Datasets bestimmen Daten in verschiedenen Datenspeichern, z.B. Tabellen, Dateien, Ordnern und Dokumenten. Ein Azure-Blobdataset kann beispielsweise den Blobcontainer und -ordner in Blob Storage angeben, aus dem die Aktivität die Daten lesen soll.

Bevor Sie ein Dataset erstellen, müssen Sie einen [**verknüpften Dienst**](concepts-linked-services.md) erstellen, um Ihren Datenspeicher mit der Data Factory zu verknüpfen. Verknüpfte Dienste ähneln Verbindungszeichenfolgen, mit denen die Verbindungsinformationen definiert werden, die für Data Factory zum Herstellen einer Verbindung mit externen Ressourcen erforderlich sind. Sie können sich dies wie folgt vorstellen: Das Dataset stellt die Struktur der Daten innerhalb des verknüpften Datenspeichers dar, und der verknüpfte Dienst definiert die Verbindung mit der Datenquelle. Ein mit Azure Storage verknüpfter Dienst verbindet z.B. ein Speicherkonto mit der Data Factory. Ein Azure-Blob-Dataset repräsentiert den Blobcontainer und den Ordner innerhalb des Azure Storage-Kontos, das die zu verarbeitenden Eingabeblobs enthält.

Hier ist ein Beispielszenario. Um Daten aus dem Blobspeicher in eine SQL-Datenbank zu kopieren, erstellen Sie zwei verknüpfte Dienste: Azure Blob Storage und Azure SQL-Datenbank. Erstellen Sie anschließend zwei Datasets: Dataset mit Text mit Trennzeichen (das sich auf den mit Azure Blob Storage verknüpften Dienst bezieht, vorausgesetzt, Sie besitzen Textdateien als Quelle) und Azure SQL-Tabellendataset (das sich auf den mit Azure SQL-Datenbank verknüpften Dienst bezieht). Die mit Azure Blob Storage und Azure SQL-Datenbank verknüpften Dienste enthalten Verbindungszeichenfolgen, die Data Factory zur Laufzeit verwendet, um die Verbindung mit Azure Storage bzw. mit Azure SQL-Datenbank herzustellen. Das Dataset mit Text mit Trennzeichen gibt den Blobcontainer und Blobordner, der die Eingabeblobs in Ihrer Blob Storage-Instanz enthält, sowie Formateinstellungen an. Das Azure SQL-Tabellendataset gibt die SQL-Tabelle in Ihrer SQL-Datenbank an, in die die Daten kopiert werden sollen.

Das folgende Diagramm zeigt die Beziehung zwischen Pipeline, Aktivität, Dataset und verknüpftem Dienst in der Data Factory an:

![Beziehung zwischen Pipeline, Aktivität, Dataset und verknüpften Diensten](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>JSON-DataSet
Ein Dataset in Data Factory wird im folgenden JSON-Format definiert:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: DelimitedText, AzureSqlTable etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "schema":[

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
type | Typ des Datasets. Geben Sie einen der von Data Factory unterstützten Typen an (z.B. DelimitedText, AzureSqlTable). <br/><br/>Weitere Informationen finden Sie unter [Datasettyp](#dataset-type). | Ja |
schema | Schema des Datasets, stellt den physischen Datentyp und die Form dar. | Nein |
typeProperties | Die Typeigenschaften unterscheiden sich je nach Typ. Ausführliche Informationen über die unterstützten Typen und deren Eigenschaften finden Sie unter [Dataset: type](#dataset-type). | Ja |

Wenn Sie das Schema eines Datasets importieren, wählen Sie die Schaltfläche **Schema importieren** und anschließend das Importieren aus der Quelle oder aus einer lokalen Datei aus. In den meisten Fällen importieren Sie das Schema direkt aus der Quelle. Wenn Sie jedoch bereits über eine lokale Schemadatei (eine Parquet-Datei oder CSV-Datei mit Headern) verfügen, können Sie Data Factory anweisen, das Schema auf dieser Datei aufzubauen.

In der Copy-Aktivität werden Datasets in der Quelle und in der Senke verwendet. Das im Dataset definierte Schema ist als Verweis optional. Wenn Sie die Spalten-/Feldzuordnung zwischen Quelle und Senke anwenden möchten, lesen Sie die Informationen unter [Schema- und Datentypzuordnung in Kopieraktivität](copy-activity-schema-and-type-mapping.md).

Datasets im Datenfluss werden in Transformationen von Quellen und Senken verwendet. Die Datasets definieren die grundlegenden Datenschemas. Wenn Ihre Daten kein Schema aufweisen, können Sie die Schemaabweichung für Ihre Quelle und Senke verwenden. Metadaten aus den Datasets werden in Ihrer Quelltransformation als Projektion der Quelle angezeigt. Die Projektion in der Quelltransformation stellt die Datenflussdaten mit definierten Namen und Typen dar.

## <a name="dataset-type"></a>Datasettyp

Azure Data Factory unterstützt verschiedene Typen von Datasets, je nach eingesetztem Datenspeicher. Die Liste der von Data Factory unterstützten Datenspeicher finden Sie im Artikel [Übersicht über Connectors](connector-overview.md). Klicken Sie auf einen Datenspeicher, um Informationen zum Erstellen eines verknüpften Diensts und eines Datasets dafür zu erhalten.

Bei einem Dataset mit Text mit Trennzeichen wird der Datasettyp auf **DelimitedText** festgelegt, wie im folgenden JSON-Beispiel gezeigt:

```json
{
    "name": "DelimitedTextInput",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorage",
            "type": "LinkedServiceReference"
        },
        "annotations": [],
        "type": "DelimitedText",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": "input.log",
                "folderPath": "inputdata",
                "container": "adfgetstarted"
            },
            "columnDelimiter": ",",
            "escapeChar": "\\",
            "quoteChar": "\""
        },
        "schema": []
    }
}
```

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
