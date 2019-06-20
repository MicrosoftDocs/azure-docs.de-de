---
title: Hochladen großer Datenmengen in Azure Data Lake Storage Gen1 mit Offlinemethoden | Microsoft-Dokumentation
description: Verwenden des Tools AdlCopy zum Kopieren von Daten aus Azure Storage-Blobs in Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 45321f6a-179f-4ee4-b8aa-efa7745b8eb6
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 4a8126d658f227d9eed372cd51cf06f8f12c99f9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60194976"
---
# <a name="use-the-azure-importexport-service-for-offline-copy-of-data-to-azure-data-lake-storage-gen1"></a>Verwenden von Azure Import/Export zum Kopieren von Daten in Data Lake Storage Gen1 im Offlinemodus
In diesem Artikel erfahren Sie, wie Sie große Datasets (>200 GB) mit Kopiermethoden im Offlinemodus, z.B. mit dem [Azure Import/Export-Dienst](../storage/common/storage-import-export-service.md), nach Azure Data Lake Storage Gen1 kopieren können. Die in diesem Artikel als Beispiel verwendete Datei hat eine Größe von 339.420.860.416 Byte, d.h. etwa 319GB auf dem Datenträger. Der Name dieser Datei lautet „319GB.tsv“.

Mit dem Import/Export-Dienst von Azure können Sie große Datenmengen auf sicherere Weise in Azure Blob Storage übertragen, indem Sie Festplattenlaufwerke an ein Azure-Rechenzentrum schicken.

## <a name="prerequisites"></a>Voraussetzungen
Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
* **Ein Azure-Speicherkonto**.
* **Ein Azure Data Lake Storage Gen1-Konto**. Eine Anleitung zur Erstellung finden Sie unter [Erste Schritte mit Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).

## <a name="preparing-the-data"></a>Vorbereiten der Daten

Vor der Verwendung des Import/Export-Diensts muss die zu übertragende Datendatei **in Kopien mit einer Größe von weniger als 200GB aufgeteilt** werden. Das Importtool kann nicht für Dateien mit einer Größe von mehr als 200GB ausgeführt werden. In diesem Tutorial teilen wir die Datei in Abschnitte von jeweils 100GB auf. Hierfür können Sie [Cygwin](https://cygwin.com/install.html) verwenden. Cygwin unterstützt Linux-Befehle. Führen in diesem Fall den folgenden Befehl aus:

    split -b 100m 319GB.tsv

Mit dem Splitvorgang werden Dateien mit den folgenden Namen erstellt.

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>Vorbereiten von Festplatten mit Daten
Befolgen Sie die Anweisungen unter [Verwenden des Azure Import/Export-Diensts](../storage/common/storage-import-export-service.md) (im Abschnitt **Vorbereiten Ihrer Laufwerke**) zum Vorbereiten der Festplattenlaufwerke. Hier ist die gesamte Sequenz:

1. Verwenden Sie eine Festplatte, die die Anforderung zur Verwendung mit dem Azure Import/Export-Dienst erfüllt.
2. Bestimmen Sie ein Azure-Speicherkonto, in das die Daten kopiert werden, nachdem die Festplatte an das Azure-Rechenzentrum gesendet wurde.
3. Verwenden Sie das [Azure Import/Export-Tool](https://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409) (ein Befehlszeilenprogramm). Nachfolgend finden Sie einen Codeausschnitt als Beispiel für die Verwendung des Tools.

    ```
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ```
    Unter [Verwenden des Azure Import/Export-Diensts](../storage/common/storage-import-export-service.md) finden Sie weitere Beispielcodeausschnitte.
4. Mit dem vorhergehenden Befehl wird eine Journaldatei am angegebenen Speicherort erstellt. Erstellen Sie mit dieser Journaldatei einen Importauftrag über das [Azure-Portal](https://portal.azure.com).

## <a name="create-an-import-job"></a>Erstellen eines Importauftrags
Nun können Sie anhand der Anweisungen unter [Verwenden des Azure Import/Export-Diensts](../storage/common/storage-import-export-service.md) (im Abschnitt **Erstellen des Importauftrags**) einen Importauftrag erstellen. Geben Sie für diesen Importauftrag neben anderen Informationen auch die bei der Vorbereitung der Festplattenlaufwerke erstellte Journaldatei an.

## <a name="physically-ship-the-disks"></a>Versenden der Festplatten
Jetzt können Sie die Datenträger an ein Azure-Rechenzentrum versenden. Dort werden die Daten in die Azure Storage-Blobs kopiert, die Sie beim Erstellen des Importauftrags angegeben haben. Wenn Sie beim Erstellen des Auftrags die Nachverfolgungsinformationen zunächst ausgelassen haben, können Sie nun zu Ihrem Importauftrag zurückkehren und die Nachverfolgungsnummer angeben.

## <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>Kopieren von Daten aus Azure Storage-Blobs in Azure Data Lake Storage Gen1
Wenn der Status „Abgeschlossen“ für den Importauftrag angezeigt wird, können Sie überprüfen, ob die Daten in den angegebenen Azure Storage-Blobs verfügbar sind. Anschließend können Sie diese Daten mit den verschiedensten Methoden aus den Blobs in Azure Data Lake Storage Gen1 verschieben. Alle verfügbaren Optionen zum Hochladen von Daten finden Sie unter [Erfassen von Daten in Data Lake Storage Gen1](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-storage-gen1).

In diesem Abschnitt sind die JSON-Definitionen angegeben, die Sie zum Erstellen einer Azure Data Factory-Pipeline zum Kopieren von Daten verwenden können. Sie können diese JSON-Definitionen im [Azure-Portal](../data-factory/tutorial-copy-data-portal.md) oder in [Visual Studio](../data-factory/tutorial-copy-data-dot-net.md) verwenden.

### <a name="source-linked-service-azure-storage-blob"></a>Mit der Quelle verknüpfter Dienst (Azure Storage-Blob)
```
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="target-linked-service-azure-data-lake-storage-gen1"></a>Mit dem Ziel verknüpfter Dienst (Azure Data Lake Storage Gen1)
```
{
    "name": "AzureDataLakeStorageGen1LinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Storage Gen1 account with your access rights>",
            "dataLakeStoreUri": "https://<adlsg1_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
```
### <a name="input-data-set"></a>Eingabedataset
```
{
    "name": "InputDataSet",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "importcontainer/vf1/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```
### <a name="output-data-set"></a>Ausgabedataset
```
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStorageGen1LinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
```
### <a name="pipeline-copy-activity"></a>Pipeline (Kopieraktivität)
```
{
    "name": "CopyImportedData",
    "properties": {
        "description": "Pipeline with copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity"
            }
        ],
        "start": "2016-02-08T22:00:00Z",
        "end": "2016-02-08T23:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```
Weitere Informationen finden Sie unter [Verschieben von Daten in und aus Azure Data Lake Storage Gen1 mithilfe von Azure Data Factory](../data-factory/connector-azure-data-lake-store.md).

## <a name="reconstruct-the-data-files-in-azure-data-lake-storage-gen1"></a>Wiederherstellen der Datendateien in Azure Data Lake Storage Gen1

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Wir haben eine Datei mit einer Größe von 319GB verwendet und sie in kleinere Dateien aufgeteilt, sodass sie mithilfe des Azure Import/Export-Diensts übertragen werden konnte. Da die Daten nun in Azure Data Lake Storage Gen1 gespeichert sind, kann die Datei in ihrer ursprünglichen Größe wiederhergestellt werden. Dazu können die folgenden Azure PowerShell-Cmdlets verwendet werden.

```
# Login to our account
Connect-AzAccount

# List your subscriptions
Get-AzSubscription

# Switch to the subscription you want to work with
Set-AzContext -SubscriptionId
Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzDataLakeStoreItem -AccountName "<adlsg1_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv"
```

## <a name="next-steps"></a>Nächste Schritte
* [Schützen von Daten in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Verwenden von Azure Data Lake Analytics mit Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Verwenden von Azure HDInsight mit Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
