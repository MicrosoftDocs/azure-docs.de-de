---
title: 'Tutorial: Erstellen einer Azure Data Factory-Pipeline mithilfe der REST-API | Microsoft-Dokumentation'
description: In diesem Tutorial erstellen Sie mit der REST-API eine Azure Data Factory-Pipeline mit einer Kopieraktivität, um Daten aus Azure Blob Storage in Azure SQL-Datenbank zu kopieren.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: ''
editor: ''
ms.assetid: 1704cdf8-30ad-49bc-a71c-4057e26e7350
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d83b659cc04218fad66ea95216e69682b265dc83
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66151619"
---
# <a name="tutorial-use-rest-api-to-create-an-azure-data-factory-pipeline-to-copy-data"></a>Tutorial: Verwenden der REST-API zum Erstellen einer Azure Data Factory-Pipeline zum Kopieren von Daten 
> [!div class="op_single_selector"]
> * [Übersicht und Voraussetzungen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Kopier-Assistent](data-factory-copy-data-wizard-tutorial.md)
> * [Azure-Portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Azure Resource Manager-Vorlage](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [REST-API](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen im [Tutorial zur Kopieraktivität](../quickstart-create-data-factory-rest-api.md). 

In diesem Artikel erfahren Sie, wie Sie mithilfe der REST-API eine Data Factory mit einer Pipeline erstellen, die Daten aus Azure Blob Storage in Azure SQL-Datenbank kopiert. Wenn Sie mit Azure Data Factory nicht vertraut sind, lesen Sie vor der Durchführung dieses Tutorials den Artikel [Einführung in Azure Data Factory](data-factory-introduction.md).   

In diesem Tutorial erstellen Sie eine Pipeline mit nur einer Aktivität: der Copy-Aktivität. Die Kopieraktivität kopiert die Daten aus einem unterstützten Datenspeicher in einen unterstützten Senkendatenspeicher. Eine Liste der Datenspeicher, die als Quellen und Senken unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Sie basiert auf einem global verfügbaren Dienst, mit dem Daten zwischen verschiedenen Datenspeichern sicher, zuverlässig und skalierbar kopiert werden können. Weitere Informationen zur Kopieraktivität finden Sie unter [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md).

Eine Pipeline kann mehrere Aktivitäten enthalten. Sie können zwei Aktivitäten verketten (nacheinander ausführen), indem Sie das Ausgabedataset einer Aktivität als Eingabedataset der anderen Aktivität festlegen. Weitere Informationen finden Sie unter [Mehrere Aktivitäten in einer Pipeline](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

> [!NOTE]
> In diesem Artikel werden nicht alle Komponenten der Data Factory-REST-API behandelt. In der [Data Factory REST-API-Referenz](/rest/api/datafactory/) finden Sie eine umfassende Dokumentation zu Data Factory-Cmdlets.
>  
> Die Datenpipeline in diesem Tutorial kopiert Daten aus einem Quelldatenspeicher in einen Zieldatenspeicher. Ein Tutorial zum Transformieren von Daten mithilfe von Azure Data Factory finden Sie unter [Tutorial: Erstellen Ihrer ersten Pipeline zur Transformierung von Daten mithilfe eines Hadoop-Clusters](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Lesen Sie sich die [Übersicht über das Tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) durch, und führen Sie die Schritte zur Erfüllung der **Voraussetzungen** aus.
* Installieren Sie [Curl](https://curl.haxx.se/dlwiz/) auf Ihrem Computer. Sie können das Curl-Tool mit REST-Befehlen zum Erstellen einer Data Factory verwenden. 
* Befolgen Sie die Anleitung in [diesem Artikel](../../active-directory/develop/howto-create-service-principal-portal.md) , um Folgendes durchzuführen: 
  1. Erstellen Sie eine Webanwendung mit dem Namen **ADFCopyTutorialApp** in Azure Active Directory.
  2. Beschaffen Sie die **Client-ID** und den **geheimen Schlüssel**. 
  3. Beschaffen Sie die **Mandanten-ID**. 
  4. Weisen Sie die Anwendung **ADFCopyTutorialApp** der Rolle **Mitwirkender von Data Factory** zu.  
* Installieren Sie [Azure PowerShell](/powershell/azure/overview).  
* Starten Sie **PowerShell**, und führen Sie die unten angegebenen Schritte aus. Lassen Sie Azure PowerShell bis zum Ende dieses Tutorials geöffnet. Wenn Sie PowerShell schließen und erneut öffnen, müssen Sie die Befehle erneut ausführen.
  
  1. Führen Sie den folgenden Befehl aus, und geben Sie den Benutzernamen und das Kennwort ein, den bzw. das Sie bei der Anmeldung am Azure-Portal verwendet haben:
    
     ```PowerShell 
     Connect-AzAccount
     ```   
  2. Führen Sie den folgenden Befehl aus, um alle Abonnements für dieses Konto anzuzeigen:

     ```PowerShell     
     Get-AzSubscription
     ``` 
  3. Führen Sie den folgenden Befehl aus, um das gewünschte Abonnement auszuwählen: Ersetzen Sie **&lt;NameOfAzureSubscription**&gt; durch den Namen Ihres Azure-Abonnements. 
     
     ```PowerShell
     Get-AzSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzContext
     ```
  4. Erstellen Sie eine Azure-Ressourcengruppe mit dem Namen **ADFTutorialResourceGroup** , indem Sie in der PowerShell den folgenden Befehl ausführen:  

     ```PowerShell     
      New-AzResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
     ```
     
      Falls die Ressourcengruppe bereits vorhanden ist, können Sie angeben, ob sie aktualisiert (J) oder beibehalten (N) werden soll. 
     
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
    "name": "ADFCopyTutorialDF",  
    "location": "WestUS"
}  
```

### <a name="azurestoragelinkedservicejson"></a>azurestoragelinkedservice.json
> [!IMPORTANT]
> Ersetzen Sie **accountname** und **accountkey** durch den Namen bzw. Schlüssel Ihres Azure-Speicherkontos. Informationen zum Abrufen Ihres Speicherzugriffsschlüssels finden Sie unter [Informationen zu Azure-Speicherkonten](../../storage/common/storage-account-manage.md#access-keys).

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

Ausführliche Informationen zu JSON-Eigenschaften finden Sie unter [Mit Azure-Speicher verknüpfter Dienst](data-factory-azure-blob-connector.md#azure-storage-linked-service).

### <a name="azuresqllinkedservicejson"></a>azuresqllinkedservice.json
> [!IMPORTANT]
> Ersetzen Sie **servername**, **databasename**, **username** und **password** durch den Namen Ihres Azure SQL-Servers, den Namen der SQL-Datenbank, das Benutzerkonto bzw. das Kennwort für das Konto.  
> 
>

```JSON
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
        }
    }
}
```

Ausführliche Informationen zu JSON-Eigenschaften finden Sie unter [Mit Azure SQL verknüpfter Dienst](data-factory-azure-sql-connector.md#linked-service-properties).

### <a name="inputdatasetjson"></a>inputdataset.json

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "structure": [
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "type": "AzureBlob",
    "linkedServiceName": "AzureStorageLinkedService",
    "typeProperties": {
      "folderPath": "adftutorial/",
      "fileName": "emp.txt",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ","
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

Die folgende Tabelle enthält eine Beschreibung der JSON-Eigenschaften, die im Codeausschnitt verwendet werden:

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| type | Die Eigenschaft „type“ wird auf **AzureBlob** festgelegt, da sich Daten in Azure Blob Storage befinden. |
| linkedServiceName | Diese Eigenschaft verweist auf den **AzureStorageLinkedService**-Dienst, den Sie zuvor erstellt haben. |
| folderPath | Diese Eigenschaft gibt den **Blobcontainer** und den **Ordner** an, der Eingabeblobs enthält. In diesem Tutorial ist „adftutorial“ der Blobcontainer und „folder“ der Stammordner. | 
| fileName | Diese Eigenschaft ist optional. Wenn Sie diese Eigenschaft nicht angeben, werden alle Dateien in „folderPath“ übernommen. In diesem Tutorial wurde **emp.txt** für „fileName“ angegeben. Daher wird nur diese Datei für die Verarbeitung gewählt. |
| Format -> Typ |Die Eingabedatei weist das Textformat auf. Daher verwenden wir **TextFormat**. |
| columnDelimiter | Die Spalten in der Eingabedatei werden per **Komma (`,`)** voneinander getrennt. |
| frequency/interval | „frequency“ wird auf **Hour** und „interval“ auf **1** festgelegt, was bedeutet, dass die Eingabeslices **stündlich** verfügbar sind. Der Data Factory-Dienst sucht also stündlich im Stammordner des angegebenen Blobcontainers (**adftutorial**) nach Eingabedaten. Er sucht innerhalb der Start- und Endzeit der Pipeline nach den Daten, nicht vor oder nach diesen Zeiten.  |
| external | Diese Eigenschaft wird auf **true** festgelegt, wenn die Daten nicht von dieser Pipeline generiert werden. Die Eingabedaten in diesem Tutorial sind in der Datei „emp.txt“ enthalten, die nicht von dieser Pipeline generiert wurde. Daher legen wir diese Eigenschaft auf „true“ fest. |

Weitere Informationen zu diesen JSON-Eigenschaften finden Sie im Artikel [Azure Blob-Connector](data-factory-azure-blob-connector.md#dataset-properties).

### <a name="outputdatasetjson"></a>outputdataset.json

```JSON
{
  "name": "AzureSqlOutput",
  "properties": {
    "structure": [
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "emp"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
Die folgende Tabelle enthält eine Beschreibung der JSON-Eigenschaften, die im Codeausschnitt verwendet werden:

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| type | Die Eigenschaft „type“ wird auf **AzureSqlTable** festgelegt, da Daten in eine Tabelle in Azure SQL-Datenbank kopiert werden. |
| linkedServiceName | Diese Eigenschaft verweist auf den **AzureSqlLinkedService**-Dienst, den Sie zuvor erstellt haben. |
| tableName | Diese Eigenschaft gibt die **Tabelle** an, in die die Daten kopiert werden. | 
| frequency/interval | „frequency“ wird auf **Hour** und „interval“ auf **1** festgelegt, was bedeutet, dass die Ausgabeslices innerhalb der Start- und Endzeit der Pipeline **stündlich** erstellt werden, nicht vor oder nach diesen Zeiten.  |

Die emp-Tabelle in der Datenbank enthält drei Spalten: **ID**, **FirstName** und **LastName**. Da es sich bei „ID“ um eine Identitätsspalte handelt, müssen Sie hier lediglich **FirstName** und **LastName** angeben.

Weitere Informationen zu diesen JSON-Eigenschaften finden Sie im Artikel [Azure SQL-Connector](data-factory-azure-sql-connector.md#dataset-properties).

### <a name="pipelinejson"></a>pipeline.json

```JSON
{
  "name": "ADFTutorialPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2017-05-11T00:00:00Z",
    "end": "2017-05-12T00:00:00Z"
  }
}
```

Beachten Sie folgende Punkte:

- Der Abschnitt „Activities“ enthält nur eine Aktivität, deren **Typ** auf **Copy** festgelegt ist. Weitere Informationen zur Kopieraktivität finden Sie unter [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md). In Data Factory-Lösungen können Sie auch [Datentransformationsaktivitäten](data-factory-data-transformation-activities.md) verwenden.
- Die Eingabe für die Aktivität ist auf **AzureBlobInput** und die Ausgabe für die Aktivität ist auf **AzureSqlOutput** festgelegt. 
- Im Abschnitt **typeProperties** ist **BlobSource** als Quelltyp und **SqlSink** als Senkentyp angegeben. Eine vollständige Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität unterstützt werden, finden Sie unter [Unterstützte Datenspeicher](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Um Informationen zum Verwenden eines bestimmten unterstützten Datenspeichers als Quelle/Senke zu erhalten, klicken Sie auf den Link in der Tabelle.  
 
Ersetzen Sie den Wert der **start**-Eigenschaft durch den aktuellen Tag und den der **end**-Eigenschaft durch den nächsten Tag. Sie können auch nur den Datumsteil angeben und den Uhrzeitteil überspringen. „2017-02-03“ entspricht beispielsweise „2017-02-03T00:00:00Z“.
 
Die Start- und Endzeit von Datums-/Uhrzeitangaben müssen im [ISO-Format](https://en.wikipedia.org/wiki/ISO_8601)angegeben werden. Beispiel:  2016-10-14T16:32:41Z. Die Zeitangabe **end** ist optional, wird aber in diesem Tutorial verwendet. 
 
Wenn für die **end**-Eigenschaft kein Wert angegeben wird, wird sie als „**start + 48 Stunden**“ berechnet. Um die Pipeline auf unbestimmte Zeit auszuführen, geben Sie als Wert für die **end**-Eigenschaft **9999-09-09** an.
 
Im obigen Beispiel ergeben sich 24 Datenslices, da jede Stunde ein Datenslice erstellt wird.

Beschreibungen der JSON-Eigenschaften in einer Pipelinedefinition finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). Beschreibungen der JSON-Eigenschaften in der Definition einer Kopieraktivität finden Sie unter [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md). Beschreibungen der JSON-Eigenschaften, die von BlobSource unterstützt werden, finden Sie im Artikel [Azure Blob-Connector](data-factory-azure-blob-connector.md). Beschreibungen der JSON-Eigenschaften, die von SqlSink unterstützt werden, finden Sie im Artikel [Azure SQL-Datenbank-Connector](data-factory-azure-sql-connector.md).

## <a name="set-global-variables"></a>Festlegen von globalen Variablen
Führen Sie in Azure PowerShell die folgenden Befehle aus, nachdem Sie die Werte durch Ihre eigenen Werte ersetzt haben:

> [!IMPORTANT]
> Informationen zur Beschaffung von Client-ID, geheimem Clientschlüssel, Mandanten-ID und Abonnement-ID finden Sie im Abschnitt [Voraussetzungen](#prerequisites) .   
> 
> 

```JSON
$client_id = "<client ID of application in AAD>"
$client_secret = "<client key of application in AAD>"
$tenant = "<Azure tenant ID>";
$subscription_id="<Azure subscription ID>";

$rg = "ADFTutorialResourceGroup"
```

Führen Sie nach der Aktualisierung des Namens der verwendeten Data Factory den folgenden Befehl aus: 

```
$adf = "ADFCopyTutorialDF"
```

## <a name="authenticate-with-aad"></a>Authentifizieren mit AAD
Führen Sie den folgenden Befehl aus, um die Authentifizierung für Azure Active Directory (AAD) durchzuführen: 

```PowerShell
$cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
$responseToken = Invoke-Command -scriptblock $cmd;
$accessToken = (ConvertFrom-Json $responseToken).access_token;

(ConvertFrom-Json $responseToken) 
```

## <a name="create-data-factory"></a>Erstellen einer Data Factory
In diesem Schritt erstellen Sie eine Azure Data Factory mit dem Namen **ADFCopyTutorialDF**. Eine Data Factory kann eine oder mehrere Pipelines haben. Eine Pipeline kann eine oder mehrere Aktivitäten aufweisen. Beispiel: eine Kopieraktivität zum Kopieren von Daten aus einer Quelle an einen Zieldatenspeicher. Eine HDInsight-Hive-Aktivität zum Ausführen eines Hive-Skripts zum Transformieren von Eingabedaten in Produktausgabedaten. Führen Sie die folgenden Befehle zum Erstellen der Data Factory aus: 

1. Weisen Sie den Befehl einer Variablen mit dem Namen **cmd**zu. 
   
    > [!IMPORTANT]
    > Stellen Sie sicher, dass der hier angegebene Name der Data Factory (ADFCopyTutorialDF) mit dem Namen übereinstimmt, der in der Datei **datafactory.json**angegeben ist. 
   
    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/ADFCopyTutorialDF0411?api-version=2015-10-01};
    ```
2. Führen Sie den Befehl mithilfe von **Invoke-Command**aus.
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Zeigen Sie die Ergebnisse an. Wenn die Erstellung der Data Factory erfolgreich war, wird der JSON-Code für die Data Factory unter **Ergebnisse** angezeigt. Andernfalls sehen Sie eine Fehlermeldung.  
   
    ```
    Write-Host $results
    ```

Beachten Sie folgende Punkte:

* Der Name der Azure Data Factory muss global eindeutig sein. Führen Sie die folgenden Schritte aus, wenn der Fehler **Der Data Factory-Name „ADFCopyTutorialDF“ ist nicht verfügbar** in Ergebnissen angezeigt wird:  
  
  1. Ändern Sie den Namen (z.B. „ihrnameADFCopyTutorialDF“) in der Datei **datafactory.json**.
  2. Ersetzen Sie im ersten Befehl an der Stelle, an der der Variablen **$cmd** ein Wert zugewiesen wird, „ADFCopyTutorialDF“ durch den neuen Namen, und führen Sie den Befehl aus. 
  3. Führen Sie die nächsten beiden Befehle zum Aufrufen der REST-API aus, um die Data Factory zu erstellen und die Ergebnisse des Vorgangs auszugeben. 
     
     Benennungsregeln für Data Factory-Artefakte finden Sie im Thema [Data Factory – Benennungsregeln](data-factory-naming-rules.md) .
* Data Factory-Instanzen können nur von Mitwirkenden/Administratoren des Azure-Abonnements erstellt werden.
* Der Name der Data Factory kann in Zukunft als DNS-Name registriert und so öffentlich sichtbar werden.
* Wenn der Fehler „**Dieses Abonnement ist nicht zur Verwendung des Microsoft.DataFactory-Namespaces registriert**“ auftritt, führen Sie einen der folgenden Schritte aus, und versuchen Sie, die Veröffentlichung erneut durchzuführen: 
  
  * Führen Sie in Azure PowerShell den folgenden Befehl aus, um den Data Factory-Anbieter zu registrieren: 

    ```PowerShell    
    Register-AzResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
    Sie können den folgenden Befehl ausführen, um sich zu vergewissern, dass der Data Factory-Anbieter registriert ist. 
    
    ```PowerShell
    Get-AzResourceProvider
    ```
  * Melden Sie sich mit dem Azure-Abonnement beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu einem Data Factory-Blatt, oder erstellen Sie eine Data Factory im Azure-Portal. Mit dieser Aktion wird der Anbieter automatisch für Sie registriert.

Vor dem Erstellen einer Pipeline müssen Sie zunächst einige Data Factory-Entitäten erstellen. Zuerst erstellen Sie verknüpfte Dienste, um den Quell- und den Zieldatenspeicher mit Ihrem Datenspeicher zu verknüpfen. Anschließend definieren Sie Ein- und Ausgabedatasets, um die Daten in verknüpften Datenspeichern darzustellen. Zum Schluss erstellen Sie die Pipeline mit einer Aktivität, die diese Datasets verwendet.

## <a name="create-linked-services"></a>Erstellen von verknüpften Diensten
Um Ihre Datenspeicher und Compute Services mit der Data Factory zu verknüpfen, können Sie verknüpfte Dienste in einer Data Factory erstellen. In diesem Tutorial werden keine Compute Services wie Azure HDInsight oder Azure Data Lake Analytics verwendet. Sie verwenden zwei Datenspeicher vom Typ „Azure Storage“ (Quelle) und „Azure SQL-Datenbank“ (Ziel). Aus diesem Grund erstellen Sie zwei verknüpfte Dienste mit dem Namen „AzureStorageLinkedService“ und „AzureSqlLinkedService“ vom Typ „AzureStorage“ und „AzureSqlDatabase“.  

Die AzureStorageLinkedService-Instanz verknüpft Ihr Azure Storage-Konto mit der Data Factory. Dieses Speicherkonto ist das Konto, in dem Sie im Rahmen der Schritte zur Erfüllung der [Voraussetzungen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) einen Container erstellt und die Daten hochgeladen haben.   

AzureSqlLinkedService verknüpft Azure SQL-Datenbank mit der Data Factory. Die aus Blob Storage kopierten Daten werden in dieser Datenbank gespeichert. Sie haben im Rahmen der Schritte zur Erfüllung der [Voraussetzungen](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) die Tabelle „emp“ in dieser Datenbank erstellt.  

### <a name="create-azure-storage-linked-service"></a>Erstellen des mit Azure Storage verknüpften Diensts
In diesem Schritt verknüpfen Sie Ihr Azure Storage-Konto mit Ihrer Data Factory. In diesem Abschnitt geben Sie Name und Schlüssel Ihres Azure Storage-Kontos an. Informationen zu JSON-Eigenschaften zum Definieren eines mit Azure Storage verknüpften Diensts finden Sie unter [Mit Azure Storage verknüpfter Dienst](data-factory-azure-blob-connector.md#azure-storage-linked-service).  

1. Weisen Sie den Befehl einer Variablen mit dem Namen **cmd**zu. 

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@azurestoragelinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
    ```
2. Führen Sie den Befehl mithilfe von **Invoke-Command**aus.

    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Zeigen Sie die Ergebnisse an. Wenn die Erstellung des verknüpften Diensts erfolgreich war, wird der JSON-Code für den verknüpften Dienst unter **Ergebnisse** angezeigt. Andernfalls sehen Sie eine Fehlermeldung.

    ```PowerShell   
    Write-Host $results
    ```

### <a name="create-azure-sql-linked-service"></a>Erstellen eines mit Azure SQL verknüpften Diensts
In diesem Schritt verknüpfen Sie die Azure SQL-Datenbank mit Ihrer Data Factory. In diesem Abschnitt geben Sie den Namen der Azure SQL Server-Instanz, den Datenbanknamen, den Benutzernamen und das Benutzerkennwort an. Informationen zu JSON-Eigenschaften zum Definieren eines mit Azure SQL verknüpften Diensts finden Sie unter [Mit Azure SQL verknüpfter Dienst](data-factory-azure-sql-connector.md#linked-service-properties).

1. Weisen Sie den Befehl einer Variablen mit dem Namen **cmd**zu. 
   
    ```PowerShell
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azuresqllinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureSqlLinkedService?api-version=2015-10-01};
    ```
2. Führen Sie den Befehl mithilfe von **Invoke-Command**aus.
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Zeigen Sie die Ergebnisse an. Wenn die Erstellung des verknüpften Diensts erfolgreich war, wird der JSON-Code für den verknüpften Dienst unter **Ergebnisse** angezeigt. Andernfalls sehen Sie eine Fehlermeldung.
   
    ```PowerShell
    Write-Host $results
    ```

## <a name="create-datasets"></a>Erstellen von Datasets
Im vorherigen Schritt haben Sie verknüpfte Dienste erstellt, um Ihr Azure Storage-Konto und Azure SQL-Datenbank mit Ihrer Data Factory zu verknüpfen. In diesem Schritt definieren Sie die beiden Datasets „AzureBlobInput“ und „AzureSqlOutput“. Sie stellen Eingabe- und Ausgabedaten dar, die in den Datenspeichern gespeichert werden, auf die mit „AzureStorageLinkedService“ und „AzureSqlLinkedService“ verwiesen wird.

Der mit Azure Storage verknüpfte Dienst gibt die Verbindungszeichenfolge an, die der Data Factory-Dienst zur Laufzeit für die Herstellung einer Verbindung zu Ihrem Azure Storage-Konto verwendet. Das Eingabeblobdataset (AzureBlobInput) gibt den Container und den Ordner an, der die Eingabedaten enthält.  

Gleichermaßen gilt: Der mit Azure SQL-Datenbank verknüpfte Dienst gibt die Verbindungszeichenfolge an, die der Data Factory-Dienst zur Laufzeit für die Herstellung einer Verbindung zu Azure SQL-Datenbank verwendet. Zudem gibt das SQL-Tabellen-Ausgabedataset („OututDataset“) die Tabelle in der Datenbank an, in die die Daten aus Blob Storage kopiert werden. 

### <a name="create-input-dataset"></a>Erstellen eines Eingabedatasets
In diesem Schritt erstellen Sie ein Dataset namens „AzureBlobInput“, das auf eine Blobdatei (emp.txt) im Stammordner eines Blobcontainers (adftutorial) in Azure Storage (dargestellt durch den verknüpften Dienst „AzureStorageLinkedService“) verweist. Wenn Sie keinen Wert für „fileName“ festlegen (oder diesen überspringen), werden Daten aus allen Blobs im Eingabeordner in das Ziel kopiert. In diesem Tutorial legen Sie einen Wert für „fileName“ fest. 

1. Weisen Sie den Befehl einer Variablen mit dem Namen **cmd**zu. 

    ```PowerSHell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
    ```
2. Führen Sie den Befehl mithilfe von **Invoke-Command**aus.
   
    ```PowerShell
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Zeigen Sie die Ergebnisse an. Wenn die Erstellung des Datasets erfolgreich war, wird der JSON-Code für das Dataset unter **Ergebnisse** angezeigt. Andernfalls sehen Sie eine Fehlermeldung.
   
    ```PowerShell
    Write-Host $results
    ```

### <a name="create-output-dataset"></a>Erstellen des Ausgabedatasets
Der mit Azure SQL-Datenbank verknüpfte Dienst gibt die Verbindungszeichenfolge an, die der Data Factory-Dienst zur Laufzeit für die Herstellung einer Verbindung mit der Azure SQL-Datenbank verwendet. Das in diesem Schritt erstellte SQL-Tabellenausgabedataset (OututDataset) gibt die Tabelle in der Datenbank an, in die die Daten aus dem Blobspeicher kopiert werden.

1. Weisen Sie den Befehl einer Variablen mit dem Namen **cmd**zu.

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureSqlOutput?api-version=2015-10-01};
    ```
2. Führen Sie den Befehl mithilfe von **Invoke-Command**aus.
    
    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Zeigen Sie die Ergebnisse an. Wenn die Erstellung des Datasets erfolgreich war, wird der JSON-Code für das Dataset unter **Ergebnisse** angezeigt. Andernfalls sehen Sie eine Fehlermeldung.
   
    ```PowerShell
    Write-Host $results
    ``` 

## <a name="create-pipeline"></a>Erstellen der Pipeline
In diesem Schritt erstellen Sie eine Pipeline mit einer **Kopieraktivität**, für die **AzureBlobInput** als Eingabe und **AzureSqlOutput** als Ausgabe verwendet wird.

Derzeit steuert das Ausgabedataset den Zeitplan. In diesem Tutorial wird ein Ausgabedataset konfiguriert, um einmal pro Stunde einen Slice zu erzeugen. Für die Pipeline ist eine Start- und Endzeit festgelegt, die einen Tag, d.h. 24 Stunden, auseinander liegen. Aus diesem Grund werden 24 Ausgabedatasetslices von der Pipeline erzeugt. 

1. Weisen Sie den Befehl einer Variablen mit dem Namen **cmd**zu.

    ```PowerShell   
    $cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
    ```
2. Führen Sie den Befehl mithilfe von **Invoke-Command**aus.

    ```PowerShell   
    $results = Invoke-Command -scriptblock $cmd;
    ```
3. Zeigen Sie die Ergebnisse an. Wenn die Erstellung des Datasets erfolgreich war, wird der JSON-Code für das Dataset unter **Ergebnisse** angezeigt. Andernfalls sehen Sie eine Fehlermeldung.  

    ```PowerShell   
    Write-Host $results
    ```

**Glückwunsch!**  Sie haben die Erstellung einer Azure Data Factory mit einer Pipeline, die Daten aus Azure Blob Storage in Azure SQL-Datenbank kopiert, erfolgreich abgeschlossen.

## <a name="monitor-pipeline"></a>Überwachen der Pipeline
In diesem Schritt verwenden Sie die Data Factory-REST-API zum Überwachen von Slices, die von der Pipeline erstellt werden.

```PowerShell
$ds ="AzureSqlOutput"
```

> [!IMPORTANT] 
> Stellen Sie sicher, dass die im folgenden Befehl angegebenen Start- und Endzeiten mit den Start- und Endzeiten der Pipeline übereinstimmen. 

```PowerShell
$cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=2017-05-11T00%3a00%3a00.0000000Z"&"end=2017-05-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};
```

```PowerShell
$results2 = Invoke-Command -scriptblock $cmd;
```

```PowerShell
IF ((ConvertFrom-Json $results2).value -ne $NULL) {
    ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
} else {
        (convertFrom-Json $results2).RemoteException
}
```

Führen Sie „Invoke-Command“ und den darauffolgenden Befehl aus, bis für den Slice der Status **Bereit** oder **Fehler** angezeigt wird. Wenn sich der Slice im Status „Bereit“ befindet, können Sie die Tabelle **emp** in Ihrer Azure SQL-Datenbank auf Ausgabedaten prüfen. 

Für jeden Slice werden zwei Zeilen mit Daten aus der Quelldatei in die Tabelle „emp“ in der Azure SQL-Datenbank kopiert. Aus diesem Grund sehen Sie in der Tabelle „emp“ 24 neue Datensätze, wenn alle Slices erfolgreich verarbeitet werden (Status „Bereit“). 

## <a name="summary"></a>Zusammenfassung
In diesem Tutorial haben Sie mit der REST-API eine Azure Data Factory erstellt, um Daten aus einem Azure-Blob in eine Azure SQL-Datenbank zu kopieren. Im Anschluss sind die allgemeinen Schritte aufgeführt, die Sie in diesem Tutorial ausgeführt haben:  

1. Sie haben eine Azure **Data Factory**erstellt.
2. Sie haben **verknüpfte Dienste**erstellt:
   1. Einen verknüpften Azure Storage-Dienst zum Verknüpfen Ihres Azure Storage-Kontos, in dem Eingabedaten enthalten sind.     
   2. Einen verknüpften Azure SQL-Dienst zum Verknüpfen Ihrer Azure SQL-Datenbank, in der die Ausgabedaten enthalten sind. 
3. Sie haben **Datasets**erstellt, mit denen Eingabedaten und Ausgabedaten für Pipelines beschrieben werden.
4. Sie haben eine **Pipeline** mit einer Kopieraktivität und „BlobSource“ als Quelle und „SqlSink“ als Senke erstellt. 

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie Azure Blob Storage als Quelldatenspeicher und Azure SQL-Datenbank als Zieldatenspeicher in einem Kopiervorgang verwendet. Die folgende Tabelle enthält eine Liste der Datenspeicher, die als Quellen oder Ziele für die Kopieraktivität unterstützt werden: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Um weitere Informationen zum Kopieren von Daten in einen bzw. aus einem Datenspeicher zu erhalten, klicken Sie in der Tabelle auf den Link für den Datenspeicher.
