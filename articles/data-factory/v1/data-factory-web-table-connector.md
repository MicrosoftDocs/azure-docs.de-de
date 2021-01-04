---
title: Verschieben von Daten aus einer Webtabelle mithilfe von Azure Data Factory
description: Erfahren Sie, wie Sie Daten aus einer Tabelle auf einer Webseite mithilfe von Azure Data Factory verschieben.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: f54a26a4-baa4-4255-9791-5a8f935898e2
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d2ea038c7d7212529185d77a6ba9e64deacb1c9e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012849"
---
# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>Verschieben von Daten aus einer Webtabelle mithilfe von Azure Data Factory
> [!div class="op_single_selector" title1="Wählen Sie die von Ihnen verwendete Version des Data Factory-Diensts aus:"]
> * [Version 1](data-factory-web-table-connector.md)
> * [Version 2 (aktuelle Version)](../connector-web-table.md)

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [Sybase-Connector in V2](../connector-web-table.md).

Dieser Artikel beschreibt, wie die Kopieraktivität in Azure Data Factory verwendet wird, um Daten aus einer Webtabelle zu einem unterstützten Senkendatenspeicher zu verschieben. Dieser Artikel baut auf dem Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit der Kopieraktivität und der als Quellen/Senken unterstützten Datenspeichern darstellt.

Data Factory unterstützt derzeit nur das Verschieben von Daten aus einer Webtabelle in andere Datenspeicher, aber nicht das Verschieben aus anderen Datenspeichern in eine Webtabelle.

> [!IMPORTANT]
> Dieser Webconnector unterstützt derzeit nur das Extrahieren von Tabelleninhalten einer HTML-Seite. Verwenden Sie zum Abrufen von Daten von einem HTTP(S)-Endpunkt stattdessen den [HTTP-Connector](data-factory-http-connector.md).

## <a name="prerequisites"></a>Voraussetzungen

Um diesen Webtabellenconnector zu verwenden, müssen Sie eine selbstgehostete Integration Runtime (auch bekannt als Datenverwaltungsgateway) einrichten und die `gatewayName`-Eigenschaft im mit der Senke verknüpften Dienst konfigurieren. Um z.B. Daten aus einer Webtabelle in Azure Blob Storage zu kopieren, konfigurieren Sie den mit Azure Storage verknüpften Dienst wie folgt:

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>",
      "gatewayName": "<gateway name>"
    }
  }
}
```

## <a name="getting-started"></a>Erste Schritte
Sie können eine Pipeline mit einer Kopieraktivität erstellen, die Daten mithilfe verschiedener Tools/APIs aus einem lokalen Teradata-Datenspeicher verschiebt. 

- Am einfachsten erstellen Sie eine Pipeline mit dem **Kopier-Assistenten**. Siehe [Tutorial: Erstellen einer Pipeline mit dem Kopier-Assistenten](data-factory-copy-data-wizard-tutorial.md) finden Sie eine kurze exemplarische Vorgehensweise zum Erstellen einer Pipeline mithilfe des Assistenten zum Kopieren von Daten. 
- Sie können auch die folgenden Tools zum Erstellen einer Pipeline verwenden: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager-Vorlage**, **.NET-API** und **REST-API**. Im [Tutorial zur Kopieraktivität](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) finden Sie detaillierte Anweisungen, wie Sie eine Pipeline mit einer Kopieraktivität erstellen können. 

Unabhängig davon, ob Sie Tools oder APIs verwenden, führen Sie die folgenden Schritte aus, um eine Pipeline zu erstellen, die Daten aus einem Quelldatenspeicher in einen Senkendatenspeicher verschiebt:

1. Erstellen **verknüpfter Dienste** zum Verknüpfen von Eingabe- und Ausgabedatenspeichern mit Ihrer Data Factory.
2. Erstellen von **Datasets** zur Darstellung von Eingabe- und Ausgabedaten für den Kopiervorgang. 
3. Erstellen einer **Pipeline** mit einer Kopieraktivität, die ein Dataset als Eingabe und ein Dataset als Ausgabe akzeptiert. 

Wenn Sie den Assistenten verwenden, werden automatisch JSON-Definitionen für diese Data Factory-Entitäten (verknüpfte Diensten, Datasets und die Pipeline) erstellt. Bei Verwendung von Tools und APIs (mit Ausnahme der .NET-API) definieren Sie diese Data Factory-Entitäten im JSON-Format.  Ein Beispiel mit JSON-Definitionen für Data Factory-Entitäten, die zum Kopieren von Daten aus einer Webtabelle verwendet werden, finden Sie im Abschnitt [JSON-Beispiel: Kopieren von Daten aus einer Webtabelle in ein Azure-Blob](#json-example-copy-data-from-web-table-to-azure-blob) in diesem Artikel. 

Die folgenden Abschnitte enthalten Details zu JSON-Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für eine Webtabelle verwendet werden:

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts
Die folgende Tabelle enthält eine Beschreibung der JSON-Elemente, die für den verknüpften Webdienst spezifisch sind.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
| --- | --- | --- |
| type |Die type-Eigenschaft muss auf Folgendes festgelegt werden: **Web** |Ja |
| url |URL der Webquelle |Ja |
| authenticationType |Anonym |Ja |

### <a name="using-anonymous-authentication"></a>Verwenden der anonymen Authentifizierung

```json
{
    "name": "web",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften
Eine vollständige Liste der Abschnitte und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel [Erstellen von Datasets](data-factory-create-datasets.md). Abschnitte wie „structure“, „availability“ und „policy“ des JSON-Codes eines Datasets sind bei allen Dataset-Typen (Azure SQL, Azure-Blob, Azure-Tabelle usw.) ähnlich.

Der Abschnitt **typeProperties** unterscheidet sich bei jedem Typ von Dataset und bietet Informationen zum Speicherort der Daten im Datenspeicher. Der „typeProperties“-Abschnitt für ein Dataset des Typs **WebTable** hat die folgenden Eigenschaften:

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type |Die Art des Datasets. Muss auf **WebTable** festgelegt sein. |Ja |
| path |Eine relative URL zu der Ressource, die die Tabelle enthält. |Nein. Wenn der Pfad nicht angegeben ist, wird nur die URL verwendet, die in der Definition des verknüpften Diensts angegeben ist. |
| Index |Der Index der Tabelle in der Ressource. Im Abschnitt [Abrufen des Indexes einer Tabelle auf einer HTML-Seite](#get-index-of-a-table-in-an-html-page) werden die Schritte zum Abrufen des Indexes einer Tabelle auf einer HTML-Seite beschrieben. |Ja |

**Beispiel:**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität
Eine vollständige Liste der Abschnitte und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen und Richtlinie sind für alle Arten von Aktivitäten verfügbar.

Eigenschaften im Abschnitt typeProperties der Aktivität können dagegen je nach Aktivitätstyp variieren. Für die Kopieraktivität variieren die Eigenschaften je nach Art der Quellen und Senken.

Wenn bei der Kopieraktivität die Quelle den Typ **WebSource** hat, werden derzeit keine zusätzlichen Eigenschaften unterstützt.


## <a name="json-example-copy-data-from-web-table-to-azure-blob"></a>JSON-Beispiel: Kopieren von Daten aus einer Webtabelle in ein Azure-Blob
Dieses Beispiel zeigt Folgendes:

1. Einen verknüpften Dienst des Typs [Web](#linked-service-properties)
2. Einen verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
3. Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [WebTable](#dataset-properties)
4. Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
5. Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [WebSource](#copy-activity-properties) und [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) verwendet

Im Beispiel werden stündlich Daten aus einer Webtabelle in ein Azure-Blob kopiert. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

In diesem Beispiel wird gezeigt, wie Sie Daten aus einer Webtabelle in ein Azure-Blob kopieren. Daten können jedoch mithilfe der Kopieraktivität in Azure Data Factory direkt in die im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) aufgeführten Senken kopiert werden.

**Verknüpfter Webdienst** In diesem Beispiel wird der verknüpfte Webdienst mit anonymer Authentifizierung verwendet. Informationen zu den verschiedenen Authentifizierungstypen finden Sie im Abschnitt [Eigenschaften des verknüpften Webdiensts](#linked-service-properties) .

```json
{
    "name": "WebLinkedService",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

**Mit Azure Storage verknüpfter Dienst**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>",
      "gatewayName": "<gateway name>"
    }
  }
}
```

**WebTable-Eingabedataset** Durch Festlegen von **external** auf **true** wird dem Data Factory-Dienst mitgeteilt, dass das Dataset für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

> [!NOTE]
> Im Abschnitt [Abrufen des Indexes einer Tabelle auf einer HTML-Seite](#get-index-of-a-table-in-an-html-page) werden die Schritte zum Abrufen des Indexes einer Tabelle auf einer HTML-Seite beschrieben.  
>
>

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```


**Azure-Blob-Ausgabedataset**

Daten werden stündlich in ein neues Blob geschrieben (frequency: hour, interval: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```



**Pipeline mit Kopieraktivität**

Die Pipeline enthält eine Kopieraktivität, die für die Verwendung der Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **WebSource** und der Typ **sink** auf **BlobSink** festgelegt.

Unter „WebSource-Typeigenschaften“ finden Sie die Liste der von WebSource unterstützten Eigenschaften.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "WebTableToAzureBlob",
        "description": "Copy from a Web table to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "WebTableInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "WebSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```

## <a name="get-index-of-a-table-in-an-html-page"></a>Abrufen des Indexes einer Tabelle auf einer HTML-Seite
1. Starten Sie **Excel 2016**, und wechseln Sie zur Registerkarte **Daten**.  
2. Klicken Sie auf der Symbolleiste auf **Neue Abfrage**, zeigen Sie auf **Aus anderen Quellen**, und klicken Sie auf **Aus dem Web**.

    ![Power Query-Menü](./media/data-factory-web-table-connector/PowerQuery-Menu.png)
3. Geben Sie im Dialogfeld **Aus dem Web** die **URL**, die Sie im JSON-Code für den verknüpften Dienst verwenden möchten (Beispiel: https://en.wikipedia.org/wiki/) ), sowie den Pfad ein, den Sie für das Dataset angeben möchten (Beispiel: AFI%27s_100_Years...100_Movies), und klicken Sie anschließend auf **OK**.

    ![Aus dem Web (Dialogfeld)](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

    Die in diesem Beispiel verwendete URL lautet wie folgt: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Falls das Dialogfeld **Auf Webinhalt zugreifen** angezeigt wird, wählen Sie die richtige **URL** und **Authentifizierung** aus, und klicken Sie auf **Verbinden**.

   ![Webinhalt aufrufen (Dialogfeld)](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5. Klicken Sie in der Strukturansicht auf ein **Tabellenelement** um Inhalte aus der Tabelle anzuzeigen, und klicken Sie dann am unteren Rand auf **Bearbeiten**.  

   ![Navigator (Dialogfeld)](./media/data-factory-web-table-connector/Navigator-DialogBox.png)
6. Klicken Sie im Fenster **Abfrage-Editor** auf der Symbolleiste auf die Schaltfläche **Erweiterter Editor**.

    ![Erweiterter Editor (Schaltfläche)](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)
7. Im Dialogfeld „Erweiterter Editor“ ist die Zahl neben „Quelle“ der Index.

    ![Erweiterter Editor – Index](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)

Rufen Sie den Index bei Verwendung von Excel 2013 mit [Microsoft Power Query für Excel](https://www.microsoft.com/download/details.aspx?id=39379) ab. Ausführlichere Informationen finden Sie im Artikel [Connect to a web page](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) (Verbinden mit einer Webseite). Bei Verwendung von [Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)werden ähnliche Schritte verwendet.

> [!NOTE]
> Weitere Informationen zum Zuordnen von Spalten im Quelldataset zu Spalten im Senkendataset finden Sie unter [Zuordnen von Datasetspalten in Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Leistung und Optimierung
Der Artikel [Handbuch zur Leistung und Optimierung der Kopieraktivität](data-factory-copy-activity-performance.md) beschreibt wichtige Faktoren, die sich auf die Leistung der Datenverschiebung (Kopieraktivität) in Azure Data Factory auswirken, sowie verschiedene Möglichkeiten zur Leistungsoptimierung.
