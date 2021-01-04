---
title: Erstellen Ihrer ersten Data Factory (REST)
description: In diesem Tutorial erstellen Sie eine Azure Data Factory-Beispielpipeline mit der Data Factory-REST-API.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 11/01/2017
ms.openlocfilehash: 835f72df5c0c693c90b0cf7c45f7805b767d2bcb
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96496363"
---
# <a name="tutorial-build-your-first-azure-data-factory-using-data-factory-rest-api"></a>Tutorial: Erstellen der ersten Azure Data Factory mit der Data Factory-REST-API
> [!div class="op_single_selector"]
> * [Übersicht und Voraussetzungen](data-factory-build-your-first-pipeline.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Resource Manager-Vorlage](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST-API](data-factory-build-your-first-pipeline-using-rest-api.md)
>
>


> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [Schnellstart: Erstellen einer Data Factory mithilfe von Azure Data Factory](../quickstart-create-data-factory-rest-api.md).

In diesem Artikel verwenden Sie die Data Factory-REST-API zum Erstellen Ihrer ersten Azure Data Factory. Falls Sie das Tutorial mit anderen Tools/SDKs absolvieren möchten, wählen Sie in der Dropdownliste eine andere Option aus.

Die Pipeline in diesem Tutorial enthält eine Aktivität: **HDInsight-Hive-Aktivität**. Bei dieser Aktivität wird ein Hive-Skript in einem Azure HDInsight-Cluster ausgeführt, mit dem Eingabedaten transformiert werden, um Ausgabedaten zu erhalten. Die Pipeline zwischen dem Start- und Endzeitpunkt wird einmal pro Monat ausgeführt.

> [!NOTE]
> In diesem Artikel werden nicht alle Komponenten der REST-API behandelt. In der [Data Factory REST-API-Referenz](/rest/api/datafactory/) finden Sie eine umfassende Dokumentation zur REST-API.
> 
> Eine Pipeline kann mehrere Aktivitäten enthalten. Sie können zwei Aktivitäten verketten (nacheinander ausführen), indem Sie das Ausgabedataset einer Aktivität als Eingabedataset der anderen Aktivität festlegen. Weitere Informationen finden Sie unter [Planung und Ausführung in einer Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).


## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Lesen Sie sich den Artikel mit der [Übersicht über das Tutorial](data-factory-build-your-first-pipeline.md) durch, und führen Sie die Schritte zur Erfüllung der **Voraussetzungen** aus.
* Installieren Sie [Curl](https://curl.haxx.se/dlwiz/) auf Ihrem Computer. Sie können das Curl-Tool mit REST-Befehlen zum Erstellen einer Data Factory verwenden.
* Befolgen Sie die Anleitung in [diesem Artikel](../../active-directory/develop/howto-create-service-principal-portal.md) , um Folgendes durchzuführen:
  1. Erstellen Sie eine Webanwendung mit dem Namen **ADFGetStartedApp** in Azure Active Directory.
  2. Beschaffen Sie die **Client-ID** und den **geheimen Schlüssel**.
  3. Beschaffen Sie die **Mandanten-ID**.
  4. Weisen Sie die Anwendung **ADFGetStartedApp** der Rolle **Data Factory-Mitwirkender** zu.
* Installieren Sie [Azure PowerShell](/powershell/azure/).
* Starten Sie **PowerShell** , und führen Sie den folgenden Befehl aus. Lassen Sie Azure PowerShell bis zum Ende dieses Tutorials geöffnet. Wenn Sie PowerShell schließen und erneut öffnen, müssen Sie die Befehle erneut ausführen.
  1. Führen Sie **Connect-AzAccount** aus, und geben Sie den Benutzernamen und das Kennwort ein, den bzw. das Sie für die Anmeldung beim Azure-Portal verwenden.
  2. Führen Sie **Get-AzSubscription** aus, um alle Abonnements für dieses Konto anzuzeigen.
  3. Führen Sie **Get-AzSubscription -SubscriptionName NameOfAzureSubscription | Set-AzContext** aus, um das gewünschte Abonnement auszuwählen. Ersetzen Sie **NameOfAzureSubscription** durch den Namen Ihres Azure-Abonnements:
* Erstellen Sie eine Azure-Ressourcengruppe mit dem Namen **ADFTutorialResourceGroup** , indem Sie in der PowerShell den folgenden Befehl ausführen:

    ```powershell
    New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

   Bei einigen Schritten dieses Lernprogramms wird davon ausgegangen, dass Sie die Ressourcengruppe namens ADFTutorialResourceGroup verwenden. Bei Verwendung einer anderen Ressourcengruppe müssen Sie den Namen Ihrer Ressourcengruppe anstelle von ADFTutorialResourceGroup in diesem Tutorial verwenden.

## <a name="create-json-definitions"></a>Erstellen von JSON-Definitionen
Erstellen Sie die folgenden JSON-Dateien in dem Ordner, in dem sich die Datei „curl.exe“ befindet.

### <a name="datafactoryjson"></a>datafactory.json
> [!IMPORTANT]
> Der Name muss global eindeutig sein, sodass sich für „ADFCopyTutorialDF“ die Verwendung eines Präfixes oder Suffixes anbietet, um die Eindeutigkeit sicherzustellen.
>
>

```JSON
{
    "name": "FirstDataFactoryREST",
    "location": "WestUS"
}
```

### <a name="azurestoragelinkedservicejson"></a>azurestoragelinkedservice.json
> [!IMPORTANT]
> Ersetzen Sie **accountname** und **accountkey** durch den Namen bzw. Schlüssel Ihres Azure-Speicherkontos. Weitere Informationen zum Abrufen der Speicherzugriffsschlüssel finden Sie unter [Verwalten von Zugriffsschlüsseln für Speicherkonten](../../storage/common/storage-account-keys-manage.md).
>
>

```JSON
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="hdinsightondemandlinkedservicejson"></a>hdinsightondemandlinkedservice.json

```JSON
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "AzureStorageLinkedService"
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

* Die Data Factory erstellt mit dem obigen JSON-Code einen **Linux-basierten** HDInsight-Cluster für Sie. Ausführliche Informationen finden Sie unter [Bedarfsgesteuerter verknüpfter HDInsight-Dienst](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) .
* Anstelle eines bedarfsgesteuerten HDInsight-Clusters könnten Sie **Ihren eigenen HDInsight-Cluster** verwenden. Ausführliche Informationen finden Sie unter [Verknüpfter HDInsight-Dienst](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) .
* Der HDInsight-Cluster erstellt einen **Standardcontainer** im Blobspeicher, den Sie im JSON-Code angegeben haben (**linkedServiceName**). HDInsight löscht diesen Container nicht, wenn der Cluster gelöscht wird. Dieses Verhalten ist beabsichtigt. Beim bedarfsgesteuerten verknüpften HDInsight-Dienst wird jedes Mal ein HDInsight-Cluster erstellt, wenn ein Slice verarbeitet werden muss. Dies gilt nur dann nicht, wenn ein aktiver Cluster (**timeToLive**) vorhanden ist und nach Abschluss der Verarbeitung gelöscht wird.

    Wenn mehr Segmente verarbeitet werden, werden in Azure Blob Storage viele Container angezeigt. Falls Sie diese für die Problembehandlung der Aufträge nicht benötigen, sollten Sie sie ggf. löschen, um die Speicherkosten zu verringern. Die Namen dieser Container basieren auf dem folgenden Muster: „adf **ihrdatafactoryname**-**nameverknüpfterdienst**-datumuhrzeitstempel“. Verwenden Sie Tools wie den [Microsoft Azure Storage-Explorer](https://storageexplorer.com/), um Container in Ihrem Azure-Blobspeicher zu löschen.

Ausführliche Informationen finden Sie unter [Bedarfsgesteuerter verknüpfter HDInsight-Dienst](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) .

### <a name="inputdatasetjson"></a>inputdataset.json

```JSON
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
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

### <a name="outputdatasetjson"></a>outputdataset.json

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
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

### <a name="pipelinejson"></a>pipeline.json
> [!IMPORTANT]
> Ersetzen Sie **storageaccountname** durch den Namen Ihres Azure-Speicherkontos.
>
>

```JSON
{
    "name": "MyFirstPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [{
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                "scriptLinkedService": "AzureStorageLinkedService",
                "defines": {
                    "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                    "partitionedtable": "wasb://adfgetstarted@<storageaccountname>t.blob.core.windows.net/partitioneddata"
                }
            },
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
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
        }],
        "start": "2017-07-10T00:00:00Z",
        "end": "2017-07-11T00:00:00Z",
        "isPaused": false
    }
}
```

Im JSON-Codeausschnitt erstellen Sie eine Pipeline, die aus einer einzelnen Aktivität besteht. Diese nutzt Hive, um Daten in einem HDInsight-Cluster zu verarbeiten.

Die Hive-Skriptdatei **partitionweblogs.hql** ist im Azure-Speicherkonto (das durch den scriptLinkedService-Dienst namens **StorageLinkedService** angegeben ist) und im Ordner **script** im Container **adfgetstarted** gespeichert.

Der Abschnitt **defines** dient zum Angeben der Laufzeiteinstellungen, die als Hive-Konfigurationswerte (etwa „${hiveconf:inputtable}“ oder „${hiveconf:partitionedtable}“) an das Hive-Skript übergeben werden.

Die Eigenschaften **start** und **end** der Pipeline geben den aktiven Zeitraum der Pipeline an.

Im JSON-Code der Aktivität geben Sie an, dass das Hive-Skript auf der Computeinstanz ausgeführt wird, die vom **linkedServiceName** – **HDInsightOnDemandLinkedService** angegeben wurde.

> [!NOTE]
> Ausführliche Informationen zu den im vorangehenden Beispiel verwendeten JSON-Eigenschaften finden Sie in [Pipelines und Aktivitäten in Azure Data Factory](data-factory-create-pipelines.md) unter „Pipeline-JSON“.
>
>

## <a name="set-global-variables"></a>Festlegen von globalen Variablen
Führen Sie in Azure PowerShell die folgenden Befehle aus, nachdem Sie die Werte durch Ihre eigenen Werte ersetzt haben:

> [!IMPORTANT]
> Informationen zur Beschaffung von Client-ID, geheimem Clientschlüssel, Mandanten-ID und Abonnement-ID finden Sie im Abschnitt [Voraussetzungen](#prerequisites) .
>
>

```powershell
$client_id = "<client ID of application in AAD>"
$client_secret = "<client key of application in AAD>"
$tenant = "<Azure tenant ID>";
$subscription_id="<Azure subscription ID>";

$rg = "ADFTutorialResourceGroup"
$adf = "FirstDataFactoryREST"
```


## <a name="authenticate-with-aad"></a>Authentifizieren mit AAD

```powershell
$cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
$responseToken = Invoke-Command -scriptblock $cmd;
$accessToken = (ConvertFrom-Json $responseToken).access_token;

(ConvertFrom-Json $responseToken)
```


## <a name="create-data-factory"></a>Erstellen einer Data Factory
In diesem Schritt erstellen Sie eine Azure Data Factory mit dem Namen **FirstDataFactoryREST**. Eine Data Factory kann eine oder mehrere Pipelines haben. Eine Pipeline kann eine oder mehrere Aktivitäten aufweisen. Beispielsweise eine Kopieraktivität zum Kopieren von Daten aus einer Quelle in einen Zieldatenspeicher und eine HDInsight-Hive-Aktivität zum Ausführen eines Hive-Skripts zum Transformieren von Daten. Führen Sie die folgenden Befehle zum Erstellen der Data Factory aus:

1. Weisen Sie den Befehl einer Variablen mit dem Namen **cmd** zu.

    Stellen Sie sicher, dass der hier angegebene Name der Data Factory (ADFCopyTutorialDF) mit dem Namen übereinstimmt, der in der Datei **datafactory.json** angegeben ist.

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@datafactory.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/FirstDataFactoryREST?api-version=2015-10-01};
    ```
2. Führen Sie den Befehl mithilfe von **Invoke-Command** aus.

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Zeigen Sie die Ergebnisse an. Wenn die Erstellung der Data Factory erfolgreich war, wird der JSON-Code für die Data Factory unter **Ergebnisse** angezeigt. Andernfalls sehen Sie eine Fehlermeldung.

    ```powershell
    Write-Host $results
    ```

Beachten Sie folgende Punkte:

* Der Name der Azure Data Factory muss global eindeutig sein. Führen Sie die folgenden Schritte aus, wenn der Fehler **Der Data Factory-Name „FirstDataFactoryREST“ ist nicht verfügbar** angezeigt wird:
  1. Ändern Sie den Namen in der Datei **datafactory.json** (beispielsweise in „&lt;IhrName&gt;FirstDataFactoryREST“). Benennungsregeln für Data Factory-Artefakte finden Sie im Thema [Data Factory – Benennungsregeln](data-factory-naming-rules.md) .
  2. Ersetzen Sie im ersten Befehl an der Stelle, an der der Variablen **$cmd** ein Wert zugewiesen wird, „FirstDataFactoryREST“ durch den neuen Namen, und führen Sie den Befehl aus.
  3. Führen Sie die nächsten beiden Befehle zum Aufrufen der REST-API aus, um die Data Factory zu erstellen und die Ergebnisse des Vorgangs auszugeben.
* Data Factory-Instanzen können nur von Mitwirkenden/Administratoren des Azure-Abonnements erstellt werden.
* Der Name der Data Factory kann in Zukunft als DNS-Name registriert und so öffentlich sichtbar werden.
* Führen Sie einen der folgenden Schritte aus, wenn Sie eine Fehlermeldung wie **Dieses Abonnement ist nicht zur Verwendung des Microsoft.DataFactory-Namespaces registriert** erhalten, und versuchen Sie, die Veröffentlichung erneut durchzuführen:

  * Führen Sie in Azure PowerShell den folgenden Befehl aus, um den Data Factory-Anbieter zu registrieren:

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

      Sie können den folgenden Befehl ausführen, um sicherzustellen, dass der Data Factory-Anbieter registriert ist:
    ```powershell
    Get-AzResourceProvider
    ```
  * Melden Sie sich mit dem Azure-Abonnement beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu einem Data Factory-Blatt, oder erstellen Sie eine Data Factory im Azure-Portal. Mit dieser Aktion wird der Anbieter automatisch für Sie registriert.

Vor dem Erstellen einer Pipeline müssen Sie zunächst einige Data Factory-Entitäten erstellen. Zuerst erstellen Sie verknüpfte Dienste zum Verknüpfen von Datenspeichern/Berechnungen mit Ihrem Datenspeicher, definieren die Datasets für Ein- und Ausgabe, um Daten in verknüpften Datenspeichern darzustellen.

## <a name="create-linked-services"></a>Erstellen von verknüpften Diensten
In diesem Schritt verknüpfen Sie Ihr Azure Storage-Konto und einen bedarfsgesteuerten Azure HDInsight-Cluster mit Ihrer Data Factory. Das Azure Storage-Konto enthält in diesem Beispiel die Ein- und Ausgabedaten für die Pipeline. Der verknüpfte HDInsight-Dienst wird verwendet, um ein in der Aktivität der Pipeline in diesem Beispiel angegebenes Hive-Skript auszuführen.

### <a name="create-azure-storage-linked-service"></a>Erstellen des mit Azure Storage verknüpften Diensts
In diesem Schritt verknüpfen Sie Ihr Azure Storage-Konto mit Ihrer Data Factory. In diesem Tutorial verwenden Sie das gleiche Azure Storage-Konto, um Ein-/Ausgabedaten und die HQL-Skriptdatei zu speichern.

1. Weisen Sie den Befehl einer Variablen mit dem Namen **cmd** zu.

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azurestoragelinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
    ```
2. Führen Sie den Befehl mithilfe von **Invoke-Command** aus.

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Zeigen Sie die Ergebnisse an. Wenn die Erstellung des verknüpften Diensts erfolgreich war, wird der JSON-Code für den verknüpften Dienst unter **Ergebnisse** angezeigt. Andernfalls sehen Sie eine Fehlermeldung.

    ```powershell
    Write-Host $results
    ```

### <a name="create-azure-hdinsight-linked-service"></a>Erstellen des mit Azure-HDInsight verknüpften Diensts
In diesem Schritt verknüpfen Sie einen bedarfsgesteuerten HDInsight-Cluster mit Ihrer Data Factory. Der HDInsight-Cluster wird automatisch zur Laufzeit erstellt und gelöscht, nachdem die Verarbeitung abgeschlossen und die angegebene Leerlaufzeit verstrichen ist. Anstelle eines bedarfsgesteuerten HDInsight-Clusters könnten Sie Ihren eigenen HDInsight-Cluster verwenden. Weitere Informationen finden Sie unter [Verknüpfte Computedienste](data-factory-compute-linked-services.md) .

1. Weisen Sie den Befehl einer Variablen mit dem Namen **cmd** zu.

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@hdinsightondemandlinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/hdinsightondemandlinkedservice?api-version=2015-10-01};
    ```
2. Führen Sie den Befehl mithilfe von **Invoke-Command** aus.

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Zeigen Sie die Ergebnisse an. Wenn die Erstellung des verknüpften Diensts erfolgreich war, wird der JSON-Code für den verknüpften Dienst unter **Ergebnisse** angezeigt. Andernfalls sehen Sie eine Fehlermeldung.

    ```powershell
    Write-Host $results
    ```

## <a name="create-datasets"></a>Erstellen von Datasets
In diesem Schritt erstellen Sie Datasets, um die Eingabe- und Ausgabedaten für die Hive-Verarbeitung darzustellen. Diese Datasets verweisen auf den **StorageLinkedService** , den Sie zuvor in diesem Tutorial erstellt haben. Der verknüpfte Dienst weist auf ein Azure Storage-Konto, und Datasets geben Container, Ordner und Dateiname in dem Speicher an, der Eingabe- und Ausgabedaten enthält.

### <a name="create-input-dataset"></a>Erstellen eines Eingabedatasets
In diesem Schritt erstellen Sie das Eingabedataset, das die in Azure Blob Storage gespeicherten Eingabedaten darstellt.

1. Weisen Sie den Befehl einer Variablen mit dem Namen **cmd** zu.

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
    ```
2. Führen Sie den Befehl mithilfe von **Invoke-Command** aus.

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Zeigen Sie die Ergebnisse an. Wenn die Erstellung des Datasets erfolgreich war, wird der JSON-Code für das Dataset unter **Ergebnisse** angezeigt. Andernfalls sehen Sie eine Fehlermeldung.

    ```powershell
    Write-Host $results
    ```

### <a name="create-output-dataset"></a>Erstellen des Ausgabedatasets
In diesem Schritt erstellen Sie das Ausgabedataset, das die in Azure Blob Storage gespeicherten Ausgabedaten darstellt.

1. Weisen Sie den Befehl einer Variablen mit dem Namen **cmd** zu.

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobOutput?api-version=2015-10-01};
    ```
2. Führen Sie den Befehl mithilfe von **Invoke-Command** aus.

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Zeigen Sie die Ergebnisse an. Wenn die Erstellung des Datasets erfolgreich war, wird der JSON-Code für das Dataset unter **Ergebnisse** angezeigt. Andernfalls sehen Sie eine Fehlermeldung.

    ```powershell
    Write-Host $results
    ```

## <a name="create-pipeline"></a>Erstellen der Pipeline
In diesem Schritt erstellen Sie Ihre erste Pipeline mit einer **HDInsightHive** -Aktivität. Der Eingabeslice ist monatlich verfügbar („frequency“: „Month“, „interval“: „1“), der Ausgabeslice wird monatlich erstellt, und die scheduler-Eigenschaft für die Aktivität ist ebenfalls auf ein monatliches Intervall festgelegt. Die Einstellungen für das Ausgabedataset und den Aktivitätsplaner müssen übereinstimmen. Derzeit steuert das Ausgabedataset den Zeitplan, sodass Sie auch dann ein Ausgabedataset erstellen müssen, wenn die Aktivität keine Ausgabe erzeugt. Wenn die Aktivität keine Eingabe akzeptiert, können Sie das Erstellen des Eingabedatasets überspringen.

Vergewissern Sie sich, dass Sie die Datei **input.log** im Ordner **adfgetstarted/inputdata** in Azure Blob Storage sehen, und führen Sie den folgenden Befehl aus, um die Pipeline bereitzustellen. Da die Zeiten für **start** und **end** in der Vergangenheit festgelegt sind und **isPaused** auf „false“ festgelegt ist, wird die Pipeline (Aktivität in der Pipeline) sofort nach der Bereitstellung ausgeführt.

1. Weisen Sie den Befehl einer Variablen mit dem Namen **cmd** zu.

    ```powershell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
    ```
2. Führen Sie den Befehl mithilfe von **Invoke-Command** aus.

    ```powershell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Zeigen Sie die Ergebnisse an. Wenn die Erstellung des Datasets erfolgreich war, wird der JSON-Code für das Dataset unter **Ergebnisse** angezeigt. Andernfalls sehen Sie eine Fehlermeldung.

    ```powershell
    Write-Host $results
    ```
4. Glückwunsch, Sie haben mit Azure PowerShell Ihre erste Pipeline erfolgreich erstellt!

## <a name="monitor-pipeline"></a>Überwachen der Pipeline
In diesem Schritt verwenden Sie die Data Factory-REST-API zum Überwachen von Slices, die von der Pipeline erstellt werden.

```powershell
$ds ="AzureBlobOutput"

$cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=1970-01-01T00%3a00%3a00.0000000Z"&"end=2016-08-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};

$results2 = Invoke-Command -scriptblock $cmd;

IF ((ConvertFrom-Json $results2).value -ne $NULL) {
    ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
} else {
        (convertFrom-Json $results2).RemoteException
}
```

> [!IMPORTANT]
> Die Erstellung eines bedarfsgesteuerten HDInsight-Clusters dauert in der Regel einige Zeit (etwa 20 Minuten). Daher ist damit zu rechnen, dass die Pipeline **etwa 30 Minuten** zum Verarbeiten des Slice benötigt.
>
>

Führen Sie „Invoke-Command“ und den darauffolgenden Befehl aus, bis für den Slice der Status **Bereit** oder **Fehler** angezeigt wird. Sobald der Slice den Status „Bereit“ hat, überprüfen Sie, ob die Ausgabedaten sich in Ihrem Blobspeicher im Ordner **partitioneddata** im Container **adfgetstarted** befinden.  Die Erstellung eines bedarfsgesteuerten HDInsight-Clusters dauert in der Regel einige Zeit.

![Ausgabedaten](./media/data-factory-build-your-first-pipeline-using-rest-api/three-ouptut-files.png)

> [!IMPORTANT]
> Die Eingabedatei wird bei erfolgreicher Verarbeitung des Slice gelöscht. Wenn Sie den Slice erneut ausführen oder das Tutorial nochmals durchgehen möchten, laden Sie die Eingabedatei (input.log) daher in den Ordner „inputdata“ des Containers „adfgetstarted“ hoch.
>
>

Sie können das Azure-Portal auch zum Überwachen von Slices und Durchführen der Problembehandlung verwenden. Informationen hierzu finden Sie unter [Überwachen von Pipelines mit dem Azure-Portal](data-factory-monitor-manage-pipelines.md) .

## <a name="summary"></a>Zusammenfassung
In diesem Tutorial haben Sie eine Azure Data Factory zum Verarbeiten von Daten erstellt, indem Sie ein Hive-Skript in einem HDInsight Hadoop-Cluster ausgeführt haben. Sie haben den Data Factory-Editor im Azure-Portal verwendet, um die folgenden Schritte auszuführen:

1. Sie haben eine Azure **Data Factory** erstellt.
2. Sie haben zwei **verknüpfte Dienste** erstellt:
   1. **Azure Storage:** verknüpfter Dienst zum Verknüpfen Ihrer Azure Blob Storage-Instanz, in der die Eingabe- und Ausgabedateien der Data Factory enthalten sind.
   2. **Azure HDInsight** -Dienst zum Verknüpfen eines bedarfsgesteuerten HDInsight Hadoop-Clusters mit der Data Factory. Azure Data Factory erstellt einen HDInsight Hadoop-Cluster „just in time“, um Eingabedaten zu verarbeiten und Ausgabedaten zu erzeugen.
3. Sie haben zwei **Datasets** erstellt, in denen Eingabe- und Ausgabedaten für eine HDInsight Hive-Aktivität in der Pipeline beschrieben werden.
4. Sie haben eine **Pipeline** mit einer **HDInsight Hive**-Aktivität erstellt.

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie eine Pipeline mit einer Transformationsaktivität (HDInsight-Aktivität) erstellt, die ein Hive-Skript in einem bedarfsgesteuerten Azure HDInsight-Cluster ausführt. Informationen zum Verwenden einer Kopieraktivität zum Kopieren von Daten aus einem Azure-Blob in Azure SQL finden Sie unter [Lernprogramm: Kopieren von Daten aus einem Azure-Blob in Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="see-also"></a>Weitere Informationen
| Thema | BESCHREIBUNG |
|:--- |:--- |
| [Referenz zur Data Factory-REST-API](/rest/api/datafactory/) |Umfassende Dokumentation zu Data Factory-Cmdlets |
| [Pipelines](data-factory-create-pipelines.md) |In diesem Artikel erhalten Sie Informationen zu Pipelines und Aktivitäten in Azure Data Factory und erfahren, wie diese zum Erstellen datengesteuerter End-to-End-Workflows für Ihr Szenario oder Ihr Unternehmen genutzt werden können. |
| [Datasets](data-factory-create-datasets.md) |Dieser Artikel enthält Informationen zu Datasets in Azure Data Factory. |
| [Planung und Ausführung](data-factory-scheduling-and-execution.md) |In diesem Artikel werden die Planungs- und Ausführungsaspekte des Azure Data Factory-Anwendungsmodells erläutert. |
| [Überwachen und Verwalten von Pipelines mit der Überwachungs-App](data-factory-monitor-manage-app.md) |In diesem Artikel wird das Überwachen, Verwalten und Debuggen von Pipelines mit der App für die Überwachung und Verwaltung beschrieben. |
