---
title: Verknüpfte Diensten in Azure Data Factory | Microsoft-Dokumentation
description: Informationen über verknüpfte Dienste in Data Factory. Verknüpfte Dienste verknüpfen Compute/-Datenspeicher mit einer Data Factory.
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
ms.openlocfilehash: ba2041495e1e3c63ee322a0b748753ad6cb68914
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64871127"
---
# <a name="linked-services-in-azure-data-factory"></a>Verknüpfte Dienste in Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-create-datasets.md)
> * [Aktuelle Version](concepts-datasets-linked-services.md)

In diesem Artikel ist beschrieben, was verknüpfte Dienste sind, wie sie im JSON-Format definiert werden, und wie sie in Azure Data Factory-Pipelines verwendet werden.

Wenn Sie noch nicht mit Data Factory vertraut sind, sollten Sie [Einführung in Azure Data Factory](introduction.md) lesen, um eine Übersicht zu erhalten.

## <a name="overview"></a>Übersicht
Eine Data Factory kann eine oder mehrere Pipelines haben. Bei einer **Pipeline** handelt es sich um eine logische Gruppierung von **Aktivitäten**, die zusammen eine Aufgabe bilden. Die Aktivitäten in einer Pipeline definieren Aktionen, die Sie auf Ihre Daten anwenden. Sie könnten z.B. mit einer Kopieraktivität Daten aus einer lokalen SQL Server-Instanz in eine Instanz von Azure Blob Storage kopieren. Anschließend könnten Sie eine Hive-Aktivität verwenden, die ein Hive-Skript für einen Azure HDInsight-Cluster ausführt, um Daten aus dem Blob Storage zu verarbeiten, um Ausgabedaten zu produzieren. Schließlich könnten Sie die Ausgabedaten mit einer zweiten Kopieraktivität in ein Azure SQL Data Warehouse kopieren, auf Basis dessen Business Intelligence-Berichtslösungen (BI) erstellt werden. Weitere Informationen zu Pipelines und Aktivitäten finden Sie unter [Pipelines und Aktivitäten in Azure Data Factory](concepts-pipelines-activities.md).

Ein **Datensatz** ist eine benannte Ansicht von Daten, die einfach auf die Daten verweist, die Sie in Ihren **Aktivitäten** als Ein- und Ausgabe verwenden möchten.

Bevor Sie ein Dataset erstellen, müssen Sie einen **verknüpften Dienst** erstellen, um Ihren Datenspeicher mit der Data Factory zu verknüpfen. Verknüpfte Dienste ähneln Verbindungszeichenfolgen, mit denen die Verbindungsinformationen definiert werden, die für Data Factory zum Herstellen einer Verbindung mit externen Ressourcen erforderlich sind. Sie können sich dies wie folgt vorstellen: Das Dataset stellt die Struktur der Daten innerhalb des verknüpften Datenspeichers dar, und der verknüpfte Dienst definiert die Verbindung mit der Datenquelle. Ein mit Azure Storage verknüpfter Dienst verbindet z.B. ein Speicherkonto mit der Data Factory. Ein Azure-Blob-Dataset stellt den Blobcontainer und den Ordner innerhalb des Azure-Speicherkontos dar, das die zu verarbeitenden Eingabeblobs enthält.

Hier ist ein Beispielszenario. Um Daten aus Blob Storage in eine SQL-Datenbank zu kopieren, erstellen Sie zwei verknüpfte Dienste: Azure Storage und Azure SQL-Datenbank. Erstellen Sie anschließend zwei Datasets: Azure-Blobdataset (das sich auf den mit Azure Storage verknüpften Dienst bezieht) und Azure SQL-Tabellendataset (das sich auf den mit Azure SQL-Datenbank verknüpften Dienst bezieht). Die mit Azure Storage und Azure SQL-Datenbank verknüpften Dienste enthalten Verbindungszeichenfolgen, die Data Factory zur Laufzeit verwendet, um die Verbindung mit Ihrem Azure Storage bzw. mit Ihrer Instanz von Azure SQL-Datenbank herzustellen. Das Azure-Blobdataset gibt den Blobcontainer und Blobordner an, der die Eingabeblobs in Ihrer Blob Storage-Instanz enthält. Das Azure SQL-Tabellendataset gibt die SQL-Tabelle in Ihrer SQL-Datenbank an, in die die Daten kopiert werden sollen.

Das folgende Diagramm zeigt die Beziehung zwischen Pipeline, Aktivität, Dataset und verknüpftem Dienst in der Data Factory an:

![Beziehung zwischen Pipeline, Aktivität, Dataset und verknüpften Diensten](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>JSON-Text für verknüpfte Dienste
Ein verknüpfter Dienst in Data Factory wird wie folgt im JSON-Format definiert:

```json
{
    "name": "<Name of the linked service>",
    "properties": {
        "type": "<Type of the linked service>",
        "typeProperties": {
              "<data store or compute-specific type properties>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

In der folgenden Tabelle werden die Eigenschaften im obigen JSON-Code beschrieben:

Eigenschaft | BESCHREIBUNG | Erforderlich |
-------- | ----------- | -------- |
name | Name des verknüpften Diensts. Siehe [Azure Data Factory – Benennungsregeln](naming-rules.md). |  Ja |
type | Typ des verknüpften Diensts. Beispiel:  AzureStorage (Datenspeicher) oder AzureBatch (Compute). Siehe die Beschreibung von „typeProperties“. | Ja |
typeProperties | Die Typeigenschaften unterscheiden sich für jeden Datenspeicher- oder Computetyp. <br/><br/> Informationen zu den unterstützten Datenspeichertypen und ihren Typeigenschaften finden Sie in der Tabelle [Datensatztyp](concepts-datasets-linked-services.md#dataset-type) in diesem Artikel. Navigieren Sie zum Artikel über den Datenspeicherconnector, um mehr über die für einen Datenspeicher spezifischen Typeigenschaften zu erfahren. <br/><br/> Informationen zu den unterstützten Computetypen und ihren Typeigenschaften finden Sie unter [Verknüpfte Computedienste](compute-linked-services.md). | Ja |
connectVia | Die [Integrationslaufzeit](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden muss. Sie können die Azure Integration Runtime oder selbstgehostete Integration Runtime verwenden (sofern sich Ihr Datenspeicher in einem privaten Netzwerk befindet). Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. | Nein

## <a name="linked-service-example"></a>Beispiel für einen verknüpften Dienst
Der folgende verknüpfte Dienst ist ein mit Azure Storage verknüpfter Dienst. Beachten Sie, dass „type“ auf „AzureStorage“ festgelegt ist. Die Typeigenschaften für den verknüpften Azure Storage-Dienst umfassen eine Verbindungszeichenfolge. Der Data Factory-Dienst verwendet diese Verbindungszeichenfolge für die Verbindung mit dem Datenspeicher zur Laufzeit.

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="create-linked-services"></a>Erstellen von verknüpften Diensten
Verknüpfte Dienste können Sie mit einem dieser Tools oder SDKs erstellen: [.NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [REST-API](quickstart-create-data-factory-rest-api.md), Azure Resource Manager-Vorlage und Azure-Portal

## <a name="data-store-linked-services"></a>Verknüpfte Dienste von Datenspeichern
Informationen zur Verbindung mit Datenspeichern finden Sie unter [Unterstützte Datenspeicher und Formate](copy-activity-overview.md#supported-data-stores-and-formats). In der Liste finden Sie bestimmte Verbindungseigenschaften, die für verschiedene Speicher erforderlich sind.

## <a name="compute-linked-services"></a>Verknüpfte Computedienste
Weitere Informationen zu den verschiedenen Compute-Umgebungen, mit denen Sie sich von Ihrer Data Factory aus verbinden können, sowie zu den verschiedenen Konfigurationen finden Sie unter [Unterstützte Compute-Umgebungen](compute-linked-services.md).

## <a name="next-steps"></a>Nächste Schritte
Im folgenden Tutorial finden Sie schrittweise Anleitungen zum Erstellen von Pipelines und Datasets mit einem dieser Tools oder SDKs.

- [Schnellstart: Erstellen einer Data Factory mithilfe von .NET](quickstart-create-data-factory-dot-net.md)
- [Schnellstart: Erstellen einer Data Factory mithilfe von PowerShell](quickstart-create-data-factory-powershell.md)
- [Schnellstart: Erstellen einer Data Factory mithilfe der REST-API](quickstart-create-data-factory-rest-api.md)
- [Schnellstart: Erstellen einer Data Factory mithilfe des Azure-Portals](quickstart-create-data-factory-portal.md)
