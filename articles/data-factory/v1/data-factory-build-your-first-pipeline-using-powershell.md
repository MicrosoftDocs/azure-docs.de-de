---
title: Erstellen Ihrer ersten Data Factory (PowerShell)
description: In diesem Tutorial erstellen Sie eine Azure Data Factory-Beispielpipeline mithilfe von Azure PowerShell.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/22/2018
ms.openlocfilehash: bbeb87c6e96c75e62fe97db031ae926ce30b6a19
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96496343"
---
# <a name="tutorial-build-your-first-azure-data-factory-using-azure-powershell"></a>Tutorial: Erstellen der ersten Azure Data Factory mit Azure PowerShell
> [!div class="op_single_selector"]
> * [Übersicht und Voraussetzungen](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager-Vorlage](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST-API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, helfen Ihnen die Informationen unter [Schnellstart: Erstellen einer Data Factory und Pipeline mit dem .NET SDK](../quickstart-create-data-factory-powershell.md) weiter.

In diesem Artikel verwenden Sie Azure PowerShell zum Erstellen Ihrer ersten Azure Data Factory. Falls Sie das Tutorial mit anderen Tools/SDKs absolvieren möchten, wählen Sie in der Dropdownliste eine andere Option aus.

Die Pipeline in diesem Tutorial enthält nur eine Aktivität: eine **HDInsight Hive-Aktivität**. Bei dieser Aktivität wird ein Hive-Skript in einem Azure HDInsight-Cluster ausgeführt, mit dem Eingabedaten transformiert werden, um Ausgabedaten zu erhalten. Die Pipeline zwischen dem Start- und Endzeitpunkt wird einmal pro Monat ausgeführt. 

> [!NOTE]
> Die Datenpipeline in diesem Tutorial transformiert Eingabedaten in Ausgabedaten. Sie kopiert keine Daten aus einem Quelldatenspeicher in einen Zieldatenspeicher. Ein Tutorial zum Kopieren von Daten mithilfe von Azure Data Factory finden Sie unter [Tutorial: Kopieren von Daten aus Blob Storage in SQL-Datenbank](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Eine Pipeline kann mehrere Aktivitäten enthalten. Sie können zwei Aktivitäten verketten (nacheinander ausführen), indem Sie das Ausgabedataset einer Aktivität als Eingabedataset der anderen Aktivität festlegen. Weitere Informationen finden Sie unter [Planung und Ausführung in einer Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Lesen Sie sich den Artikel mit der [Übersicht über das Tutorial](data-factory-build-your-first-pipeline.md) durch, und führen Sie die Schritte zur Erfüllung der **Voraussetzungen** aus.
* Befolgen Sie die Anweisungen im Artikel [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/) zum Installieren der neuesten Version von Azure PowerShell auf Ihrem Computer.
* (optional) In diesem Artikel werden nicht alle Data Factory-Cmdlets behandelt. In der [Data Factory-Cmdlet-Referenz](/powershell/module/az.datafactory) finden Sie eine umfassende Dokumentation zu Data Factory-Cmdlets.

## <a name="create-data-factory"></a>Erstellen einer Data Factory
In diesem Schritt erstellen Sie mit Azure PowerShell eine Azure Data Factory namens **FirstDataFactoryPSH**. Eine Data Factory kann eine oder mehrere Pipelines haben. Eine Pipeline kann eine oder mehrere Aktivitäten aufweisen. Beispielsweise eine Kopieraktivität zum Kopieren von Daten aus einer Quelle in einen Zieldatenspeicher und eine HDInsight-Hive-Aktivität zum Ausführen eines Hive-Skripts zum Transformieren von Eingabedaten. In diesem Schritt erstellen wir zunächst die Data Factory.

1. Starten Sie Azure PowerShell, und führen Sie den folgenden Befehl aus. Lassen Sie Azure PowerShell bis zum Ende dieses Tutorials geöffnet. Wenn Sie PowerShell schließen und erneut öffnen, müssen Sie die Befehle erneut ausführen.
   * Führen Sie den folgenden Befehl aus, und geben Sie den Benutzernamen und das Kennwort ein, den bzw. das Sie bei der Anmeldung beim Azure-Portal verwendet haben:
     ```PowerShell
     Connect-AzAccount
     ```    
   * Führen Sie den folgenden Befehl aus, um alle Abonnements für dieses Konto anzuzeigen:
     ```PowerShell
     Get-AzSubscription  
     ```
   * Führen Sie den folgenden Befehl aus, um das gewünschte Abonnement auszuwählen: Dieses Abonnement sollte dasselbe sein, das Sie im Azure-Portal verwendet haben.
     ```PowerShell
     Get-AzSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzContext
     ```     
2. Erstellen Sie eine Azure-Ressourcengruppe mit dem Namen **ADFTutorialResourceGroup** , indem Sie den folgenden Befehl ausführen:
    
    ```PowerShell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```
    Bei einigen Schritten dieses Lernprogramms wird davon ausgegangen, dass Sie die Ressourcengruppe namens ADFTutorialResourceGroup verwenden. Bei Verwendung einer anderen Ressourcengruppe müssen Sie diese anstelle der Ressourcengruppe ADFTutorialResourceGroup verwenden.
3. Führen Sie das Cmdlet **New-AzDataFactory** aus, um eine Data Factory mit dem Namen **FirstDataFactoryPSH** zu erstellen.

    ```PowerShell
    New-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH –Location "West US"
    ```
   Beachten Sie folgende Punkte:

* Der Name der Azure Data Factory muss global eindeutig sein. Bei Anzeige der Fehlermeldung **Data Factory-Name „FirstDataFactoryPSH“ nicht verfügbar** ändern Sie den Namen (z.B. in „IhrNameFirstDataFactoryPSH“). Verwenden Sie diesen Namen beim Ausführen der Schritte in diesem Lernprogramm anstelle von "ADFTutorialDataFactoryPSH". Benennungsregeln für Data Factory-Artefakte finden Sie im Thema [Data Factory – Benennungsregeln](data-factory-naming-rules.md) .
* Data Factory-Instanzen können nur von Mitwirkenden/Administratoren des Azure-Abonnements erstellt werden.
* Der Name der Data Factory kann in Zukunft als DNS-Name registriert und so öffentlich sichtbar werden.
* Bei Anzeige der Fehlermeldung „**Dieses Abonnement ist nicht zur Verwendung des Microsoft.DataFactory-Namespaces registriert**“ auftritt, führen Sie einen der folgenden Schritte aus, und versuchen Sie, die Veröffentlichung erneut durchzuführen:

  * Führen Sie in Azure PowerShell den folgenden Befehl aus, um den Data Factory-Anbieter zu registrieren:

    ```PowerShell
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
      Sie können den folgenden Befehl ausführen, um sicherzustellen, dass der Data Factory-Anbieter registriert ist:

    ```PowerShell
    Get-AzResourceProvider
    ```
  * Melden Sie sich mit dem Azure-Abonnement beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu einem Data Factory-Blatt, oder erstellen Sie eine Data Factory im Azure-Portal. Mit dieser Aktion wird der Anbieter automatisch für Sie registriert.

Vor dem Erstellen einer Pipeline müssen Sie zunächst einige Data Factory-Entitäten erstellen. Zuerst erstellen Sie verknüpfte Dienste zum Verknüpfen von Datenspeichern/Berechnungen mit Ihrem Datenspeicher, definieren die Datasets für Ein- und Ausgabe, um Eingabe-/Ausgabedaten in verknüpften Datenspeichern darzustellen, und erstellen dann die Pipeline mit einer Aktivität, die diese Datasets verwendet.

## <a name="create-linked-services"></a>Erstellen von verknüpften Diensten
In diesem Schritt verknüpfen Sie Ihr Azure Storage-Konto und einen bedarfsgesteuerten Azure HDInsight-Cluster mit Ihrer Data Factory. Das Azure Storage-Konto enthält in diesem Beispiel die Ein- und Ausgabedaten für die Pipeline. Der verknüpfte HDInsight-Dienst wird verwendet, um ein in der Aktivität der Pipeline in diesem Beispiel angegebenes Hive-Skript auszuführen. Identifizieren Sie, welche Datenspeicher-/Computedienste in Ihrem Szenario verwendet werden, und verknüpfen Sie diese Dienste mit der Data Factory, indem Sie verknüpfte Dienste erstellen.

### <a name="create-azure-storage-linked-service"></a>Erstellen des mit Azure Storage verknüpften Diensts
In diesem Schritt verknüpfen Sie Ihr Azure Storage-Konto mit Ihrer Data Factory. Verwenden Sie das gleiche Azure Storage-Konto, um Ein-/Ausgabedaten und die HQL-Skriptdatei zu speichern.

1. Erstellen Sie im Ordner „C:\ADFGetStarted“ eine JSON-Datei mit dem Namen „StorageLinkedService.json“ mit folgendem Inhalt: Erstellen Sie den Ordner „ADFGetStarted“, falls dieser noch nicht vorhanden ist.

    ```json
    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "description": "",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
    }
    ```
    Ersetzen Sie **Kontoname** durch den Namen Ihres Azure Storage-Kontos und **Kontoschlüssel** durch den Zugriffsschlüssel des Azure Storage-Kontos. Weitere Informationen zum Abrufen der Speicherzugriffsschlüssel finden Sie unter [Verwalten von Zugriffsschlüsseln für Speicherkonten](../../storage/common/storage-account-keys-manage.md).
2. Wechseln Sie in Azure PowerShell zum Ordner „ADFGetStarted“.
3. Verwenden Sie das Cmdlet **New-AzDataFactoryLinkedService**, um einen verknüpften Dienst zu erstellen. Für dieses Cmdlet und andere Data Factory-Cmdlets, die Sie in diesem Tutorial verwenden, ist das Eingeben von Werten für die Parameter *ResourceGroupName* und *DataFactoryName* erforderlich. Sie können auch **Get-AzDataFactory** verwenden, um ein **DataFactory**-Objekt abzurufen und das Objekt ohne Eingabe von *ResourceGroupName* und *DataFactoryName* bei jeder Ausführung eines Cmdlets zu übergeben. Führen Sie den folgenden Befehl aus, um die Ausgabe des Cmdlets **Get-AzDataFactory** der Variablen **$df** zuzuweisen.

    ```PowerShell
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH
    ```
4. Führen Sie nun das Cmdlet **New-AzDataFactoryLinkedService** aus, um den verknüpften Dienst **StorageLinkedService** zu erstellen.

    ```PowerShell
    New-AzDataFactoryLinkedService $df -File .\StorageLinkedService.json
    ```
    Wenn Sie das Cmdlet **Get-AzDataFactory** noch nicht ausgeführt und die Ausgabe nicht der Variablen **$df** zugewiesen hätten, müssten Sie für die Parameter *ResourceGroupName* und *DataFactoryName* folgende Werte angeben:

    ```PowerShell
    New-AzDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName FirstDataFactoryPSH -File .\StorageLinkedService.json
    ```
    Wenn Sie Azure PowerShell mitten im Tutorial schließen, müssen Sie das Cmdlet **Get-AzDataFactory** beim nächsten Start von Azure PowerShell ausführen, um das Tutorial abschließen zu können.

### <a name="create-azure-hdinsight-linked-service"></a>Erstellen des mit Azure-HDInsight verknüpften Diensts
In diesem Schritt verknüpfen Sie einen bedarfsgesteuerten HDInsight-Cluster mit Ihrer Data Factory. Der HDInsight-Cluster wird automatisch zur Laufzeit erstellt und gelöscht, nachdem die Verarbeitung abgeschlossen und die angegebene Leerlaufzeit verstrichen ist. Anstelle eines bedarfsgesteuerten HDInsight-Clusters könnten Sie Ihren eigenen HDInsight-Cluster verwenden. Weitere Informationen finden Sie unter [Verknüpfte Computedienste](data-factory-compute-linked-services.md) .

1. Erstellen Sie im Ordner **C:\ADFGetStarted** eine JSON-Datei mit dem Namen **HDInsightOnDemandLinkedService.json** mit folgendem Inhalt.

    ```json
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "StorageLinkedService"
            }
        }
    }
    ```
    Die folgende Tabelle enthält eine Beschreibung der JSON-Eigenschaften, die im Codeausschnitt verwendet werden:

   | Eigenschaft | BESCHREIBUNG |
   |:--- |:--- |
   | clusterSize |Gibt die Größe des HDInsight-Clusters an. |
   | timeToLive |Gibt die Leerlaufzeit des HDInsight-Clusters an, bevor er gelöscht wird. |
   | linkedServiceName |Gibt das Speicherkonto an, das verwendet wird, um die von HDInsight generierten Protokolle zu speichern. |

    Beachten Sie folgende Punkte:

   * Die Data Factory erstellt mit dem JSON-Code einen **Linux-basierten** HDInsight-Cluster für Sie. Ausführliche Informationen finden Sie unter [Bedarfsgesteuerter verknüpfter HDInsight-Dienst](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) .
   * Anstelle eines bedarfsgesteuerten HDInsight-Clusters könnten Sie **Ihren eigenen HDInsight-Cluster** verwenden. Ausführliche Informationen finden Sie unter [Verknüpfter HDInsight-Dienst](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) .
   * Der HDInsight-Cluster erstellt einen **Standardcontainer** im Blobspeicher, den Sie im JSON-Code angegeben haben (**linkedServiceName**). HDInsight löscht diesen Container nicht, wenn der Cluster gelöscht wird. Dieses Verhalten ist beabsichtigt. Beim bedarfsgesteuerten verknüpften HDInsight-Dienst wird jedes Mal ein HDInsight-Cluster erstellt, wenn ein Slice verarbeitet wird. Dies gilt nur dann nicht, wenn ein aktiver Cluster (**timeToLive**) vorhanden ist. Der Cluster wird automatisch gelöscht, nachdem die Verarbeitung abgeschlossen ist.

       Wenn mehr Segmente verarbeitet werden, werden in Azure Blob Storage viele Container angezeigt. Falls Sie diese für die Problembehandlung der Aufträge nicht benötigen, sollten Sie sie ggf. löschen, um die Speicherkosten zu verringern. Die Namen dieser Container basieren auf dem folgenden Muster: „adf **ihrdatafactoryname**-**nameverknüpfterdienst**-datumuhrzeitstempel“. Verwenden Sie Tools wie den [Microsoft Azure Storage-Explorer](https://storageexplorer.com/), um Container in Ihrem Azure-Blobspeicher zu löschen.

     Ausführliche Informationen finden Sie unter [Bedarfsgesteuerter verknüpfter HDInsight-Dienst](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) .
2. Führen Sie nun das Cmdlet **New-AzDataFactoryLinkedService** aus, um den verknüpften Dienst „HDInsightOnDemandLinkedService“ zu erstellen.
    
    ```PowerShell
    New-AzDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json
    ```

## <a name="create-datasets"></a>Erstellen von Datasets
In diesem Schritt erstellen Sie Datasets, um die Eingabe- und Ausgabedaten für die Hive-Verarbeitung darzustellen. Diese Datasets verweisen auf den **StorageLinkedService** , den Sie zuvor in diesem Tutorial erstellt haben. Der verknüpfte Dienst weist auf ein Azure Storage-Konto, und Datasets geben Container, Ordner und Dateiname in dem Speicher an, der Eingabe- und Ausgabedaten enthält.

### <a name="create-input-dataset"></a>Erstellen eines Eingabedatasets
1. Erstellen Sie im Ordner **C:\ADFGetStarted** eine JSON-Datei mit dem Namen **InputTable.json** mit folgendem Inhalt:

    ```json
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "fileName": "input.log",
                "folderPath": "adfgetstarted/inputdata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }
    ```
    Im JSON-Code wird ein Dataset mit dem Namen **AzureBlobInput** definiert, das Eingabedaten für eine Aktivität in der Pipeline darstellt. Darüber hinaus geben Sie an, dass die Eingabedaten im Blobcontainer **adfgetstarted** und im Ordner **inputdata** gespeichert werden.

    Die folgende Tabelle enthält eine Beschreibung der JSON-Eigenschaften, die im Codeausschnitt verwendet werden:

   | Eigenschaft | BESCHREIBUNG |
   |:--- |:--- |
   | type |Die Type-Eigenschaft wird auf „AzureBlob“ festgelegt, da sich Daten in Azure Blob Storage befinden. |
   | linkedServiceName |verweist auf den „StorageLinkedService“, den Sie zuvor erstellt haben. |
   | fileName |Diese Eigenschaft ist optional. Wenn Sie diese Eigenschaft nicht angeben, werden alle Dateien in „folderPath“ übernommen. In diesem Fall wird nur „input.log“ verarbeitet. |
   | type |Da die Protokolldateien im Textformat vorliegen, verwenden wir „TextFormat“. |
   | columnDelimiter |Spalten werden in den Protokolldateien per Komma (,) voneinander getrennt. |
   | frequency/interval |„frequency“ wird auf „Month“ und „interval“ auf „1“ festgelegt, was bedeutet, dass die Eingabeslices monatlich verfügbar sind. |
   | external |Diese Eigenschaft wird auf „true“ festgelegt, wenn die Eingabedaten nicht vom Data Factory-Dienst generiert werden. |
2. Führen Sie in Azure PowerShell den folgenden Befehl zum Erstellen des Data Factory-Datasets aus:

    ```PowerShell
    New-AzDataFactoryDataset $df -File .\InputTable.json
    ```

### <a name="create-output-dataset"></a>Erstellen des Ausgabedatasets
Nun erstellen Sie das Ausgabedataset, das die in Azure Blob Storage gespeicherten Ausgabedaten darstellt.

1. Erstellen Sie im Ordner **C:\ADFGetStarted** eine JSON-Datei mit dem Namen **OutputTable.json** mit folgendem Inhalt:

    ```json
    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "adfgetstarted/partitioneddata",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "availability": {
          "frequency": "Month",
          "interval": 1
        }
      }
    }
    ```
    Im JSON-Code wird ein Dataset mit dem Namen **AzureBlobOutput** definiert, das Ausgabedaten für eine Aktivität in der Pipeline darstellt. Darüber hinaus geben Sie an, dass die Ergebnisse im Blobcontainer **adfgetstarted** und im Ordner **partitioneddata** gespeichert werden. Der Abschnitt **availability** gibt an, dass das Ausgabe-DataSet monatlich erzeugt wird.
2. Führen Sie in Azure PowerShell den folgenden Befehl zum Erstellen des Data Factory-Datasets aus:

    ```PowerShell
    New-AzDataFactoryDataset $df -File .\OutputTable.json
    ```

## <a name="create-pipeline"></a>Erstellen der Pipeline
In diesem Schritt erstellen Sie Ihre erste Pipeline mit einer **HDInsightHive** -Aktivität. Der Eingabeslice ist monatlich verfügbar („frequency“: „Month“, „interval“: „1“), der Ausgabeslice wird monatlich erstellt, und die scheduler-Eigenschaft für die Aktivität ist ebenfalls auf ein monatliches Intervall festgelegt. Die Einstellungen für das Ausgabedataset und den Aktivitätsplaner müssen übereinstimmen. Derzeit steuert das Ausgabedataset den Zeitplan, sodass Sie auch dann ein Ausgabedataset erstellen müssen, wenn die Aktivität keine Ausgabe erzeugt. Wenn die Aktivität keine Eingabe akzeptiert, können Sie das Erstellen des Eingabedatasets überspringen. Am Ende dieses Abschnitts werden die in der folgenden JSON verwendeten Eigenschaften erläutert.

1. Erstellen Sie im Ordner „C:\ADFGetStarted“ eine JSON-Datei mit dem Namen „MyFirstPipelinePSH.json“ mit folgendem Inhalt:

   > [!IMPORTANT]
   > Ersetzen Sie im JSON-Code **storageaccountname** durch den Namen Ihres Speicherkontos.
   >
   >

    ```json
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "StorageLinkedService",
                        "defines": {
                            "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                            "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "policy": {
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                    },
                    "name": "RunSampleHiveActivity",
                    "linkedServiceName": "HDInsightOnDemandLinkedService"
                }
            ],
            "start": "2017-07-01T00:00:00Z",
            "end": "2017-07-02T00:00:00Z",
            "isPaused": false
        }
    }
    ```
    Im JSON-Codeausschnitt erstellen Sie eine Pipeline, die aus einer einzelnen Aktivität besteht. Diese nutzt Hive, um Daten in einem HDInsight-Cluster Daten zu verarbeiten.

    Die Hive-Skriptdatei **partitionweblogs.hql** ist im Azure Storage-Konto (das durch den scriptLinkedService-Dienst namens **StorageLinkedService** angegeben ist) und im Ordner **script** im Container **adfgetstarted** gespeichert.

    Der Abschnitt **defines** dient zum Angeben der Laufzeiteinstellungen, die als Hive-Konfigurationswerte (z.B. „${hiveconf:inputtable}“, „${hiveconf:partitionedtable}“) an das Hive-Skript übergeben werden.

    Die Eigenschaften **start** und **end** der Pipeline geben den aktiven Zeitraum der Pipeline an.

    Im JSON-Code der Aktivität geben Sie an, dass das Hive-Skript auf der Computeinstanz ausgeführt wird, die vom **linkedServiceName** – **HDInsightOnDemandLinkedService** angegeben wurde.

   > [!NOTE]
   > Ausführliche Informationen zu den in diesem Beispiel verwendeten JSON-Eigenschaften finden Sie in [Pipelines und Aktivitäten in Azure Data Factory](data-factory-create-pipelines.md) unter „Pipeline-JSON“.

2. Vergewissern Sie sich, dass Sie die Datei **input.log** im Ordner **adfgetstarted/inputdata** in Azure Blob Storage sehen, und führen Sie den folgenden Befehl aus, um die Pipeline bereitzustellen. Da die Zeiten für **start** und **end** in der Vergangenheit festgelegt sind und **isPaused** auf „false“ festgelegt ist, wird die Pipeline (Aktivität in der Pipeline) sofort nach der Bereitstellung ausgeführt.

    ```PowerShell
    New-AzDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json
    ```
3. Glückwunsch, Sie haben mit Azure PowerShell Ihre erste Pipeline erfolgreich erstellt!

## <a name="monitor-pipeline"></a>Überwachen der Pipeline
In diesem Schritt verwenden Sie Azure PowerShell zur Überwachung der Aktivitäten in einer Azure Data Factory.

1. Führen Sie **Get-AzDataFactory** aus, und weisen Sie die Ausgabe der Variablen **$df** zu.

    ```PowerShell
    $df=Get-AzDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name FirstDataFactoryPSH
    ```
2. Führen Sie **Get-AzDataFactorySlice** aus, um Details zu allen Slices in der Tabelle **EmpSQLTable** zu erhalten. Dies ist die Ausgabetabelle der Pipeline.

    ```PowerShell
    Get-AzDataFactorySlice $df -DatasetName AzureBlobOutput -StartDateTime 2017-07-01
    ```
    Beachten Sie, dass der "StartDateTime"-Wert, den Sie hier angeben, der im JSON-Code der Pipeline angegebenen Startzeit entspricht. Hier ist die Beispielausgabe:

    ```PowerShell
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : FirstDataFactoryPSH
    DatasetName       : AzureBlobOutput
    Start             : 7/1/2017 12:00:00 AM
    End               : 7/2/2017 12:00:00 AM
    RetryCount        : 0
    State             : InProgress
    SubState          :
    LatencyStatus     :
    LongRetryCount    : 0
    ```
3. Führen Sie **Get-AzDataFactoryRun** aus, um die Details der Aktivitätsausführungen für einen bestimmten Slice abzurufen.

    ```PowerShell
    Get-AzDataFactoryRun $df -DatasetName AzureBlobOutput -StartDateTime 2017-07-01
    ```

    Hier ist die Beispielausgabe: 

    ```PowerShell
    Id                  : 0f6334f2-d56c-4d48-b427-d4f0fb4ef883_635268096000000000_635292288000000000_AzureBlobOutput
    ResourceGroupName   : ADFTutorialResourceGroup
    DataFactoryName     : FirstDataFactoryPSH
    DatasetName         : AzureBlobOutput
    ProcessingStartTime : 12/18/2015 4:50:33 AM
    ProcessingEndTime   : 12/31/9999 11:59:59 PM
    PercentComplete     : 0
    DataSliceStart      : 7/1/2017 12:00:00 AM
    DataSliceEnd        : 7/2/2017 12:00:00 AM
    Status              : AllocatingResources
    Timestamp           : 12/18/2015 4:50:33 AM
    RetryAttempt        : 0
    Properties          : {}
    ErrorMessage        :
    ActivityName        : RunSampleHiveActivity
    PipelineName        : MyFirstPipeline
    Type                : Script
    ```
    Sie können dieses Cmdlet weiter ausführen, bis der Slice den Status **Bereit** oder **Fehler** hat. Sobald der Slice den Status „Bereit“ hat, überprüfen Sie, ob die Ausgabedaten sich in Ihrem Blobspeicher im Ordner **partitioneddata** im Container **adfgetstarted** befinden.  Die bedarfsgesteuerte Erstellung eines HDInsight-Clusters dauert in der Regel einige Zeit.

    ![Ausgabedaten](./media/data-factory-build-your-first-pipeline-using-powershell/three-ouptut-files.png)

> [!IMPORTANT]
> Die Erstellung eines bedarfsgesteuerten HDInsight-Clusters dauert in der Regel einige Zeit (etwa 20 Minuten). Daher ist damit zu rechnen, dass die Pipeline **etwa 30 Minuten** zum Verarbeiten des Slice benötigt.
>
> Die Eingabedatei wird bei erfolgreicher Verarbeitung des Slice gelöscht. Wenn Sie den Slice erneut ausführen oder das Tutorial nochmals durchgehen möchten, laden Sie die Eingabedatei (input.log) daher in den Ordner „inputdata“ des Containers „adfgetstarted“ hoch.
>
>

## <a name="summary"></a>Zusammenfassung
In diesem Tutorial haben Sie eine Azure Data Factory zum Verarbeiten von Daten erstellt, indem Sie ein Hive-Skript in einem HDInsight Hadoop-Cluster ausgeführt haben. Sie haben den Data Factory-Editor im Azure-Portal verwendet, um die folgenden Schritte auszuführen:

1. Sie haben eine Azure **Data Factory** erstellt.
2. Sie haben zwei **verknüpfte Dienste** erstellt:
   1. **Azure Storage:** verknüpfter Dienst zum Verknüpfen Ihrer Azure Blob Storage-Instanz, in der die Eingabe- und Ausgabedateien der Data Factory enthalten sind.
   2. **Azure HDInsight** -Dienst zum Verknüpfen eines bedarfsgesteuerten HDInsight Hadoop-Clusters mit der Data Factory. Azure Data Factory erstellt einen HDInsight Hadoop-Cluster „just in time“, um Eingabedaten zu verarbeiten und Ausgabedaten zu erzeugen.
3. Sie haben zwei **Datasets** erstellt, in denen Eingabe- und Ausgabedaten für eine HDInsight Hive-Aktivität in der Pipeline beschrieben werden.
4. Sie haben eine **Pipeline** mit einer **HDInsight Hive**-Aktivität erstellt.

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie eine Pipeline mit einer Transformationsaktivität (HDInsight-Aktivität) erstellt, die ein Hive-Skript in einem bedarfsgesteuerten Azure HDInsight-Cluster ausführt. Informationen dazu, wie Sie eine Copy-Aktivität zum Kopieren von Daten aus einem Azure-Blob nach Azure SQL verwenden finden Sie unter [Tutorial: Kopieren von Daten aus einem Azure-Blob in Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="see-also"></a>Weitere Informationen

| Thema | BESCHREIBUNG |
|:--- |:--- |
| [Cmdlet-Referenz zu Data Factory](/powershell/module/az.datafactory) |Umfassende Dokumentation zu Data Factory-Cmdlets |
| [Pipelines](data-factory-create-pipelines.md) |In diesem Artikel erhalten Sie Informationen zu Pipelines und Aktivitäten in Azure Data Factory und erfahren, wie diese zum Erstellen datengesteuerter End-to-End-Workflows für Ihr Szenario oder Ihr Unternehmen genutzt werden können. |
| [Datasets](data-factory-create-datasets.md) |Dieser Artikel enthält Informationen zu Datasets in Azure Data Factory. |
| [Planung und Ausführung](data-factory-scheduling-and-execution.md) |In diesem Artikel werden die Planungs- und Ausführungsaspekte des Azure Data Factory-Anwendungsmodells erläutert. |
| [Überwachen und Verwalten von Pipelines mit der Überwachungs-App](data-factory-monitor-manage-app.md) |In diesem Artikel wird das Überwachen, Verwalten und Debuggen von Pipelines mit der App für die Überwachung und Verwaltung beschrieben. |
