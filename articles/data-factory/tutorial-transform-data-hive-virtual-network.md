---
title: Transformieren von Daten mithilfe von Hive in Azure Virtual Network
description: Verwenden Sie Azure PowerShell, um eine Data Factory-Pipeline zu erstellen, die Daten mithilfe einer Hive-Aktivität in einem HDInsight-Cluster transformiert, der sich in einem virtuellen Azure-Netzwerk (VNET) befindet.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.topic: tutorial
ms.custom: seo-dt-2019
ms.date: 01/22/2018
ms.openlocfilehash: 85f51fd52ce3224b37c27cea6c49a8a386fbea2c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377770"
---
# <a name="transform-data-in-azure-virtual-network-using-hive-activity-in-azure-data-factory"></a>Transformieren von Daten in Azure Virtual Network mithilfe einer Hive-Aktivität in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Tutorial verwenden Sie Azure PowerShell, um eine Data Factory-Pipeline zu erstellen, die Daten mithilfe einer Hive-Aktivität in einem HDInsight-Cluster transformiert, der sich in einem virtuellen Azure-Netzwerk (VNet) befindet. In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Erstellen einer Data Factory. 
> * Erstellen und Einrichten einer selbstgehosteten Integration Runtime
> * Erstellen und Bereitstellen von verknüpften Diensten
> * Erstellen und Bereitstellen einer Pipeline, die eine Hive-Aktivität enthält
> * Starten einer Pipelineausführung
> * Überwachen der Pipelineausführung 
> * Überprüfen der Ausgabe 

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure Storage-Konto**. Erstellen Sie ein Hive-Skript, und laden Sie es in Azure Storage hoch. Die Ausgabe des Hive-Skripts wird in diesem Storage-Konto gespeichert. In diesem Beispiel verwendet der HDInsight-Cluster dieses Azure Storage-Konto als primären Speicher. 
- **Azure Virtual Network.** Wenn Sie noch nicht über ein Azure Virtual Network verfügen, erstellen Sie ein solches anhand [dieser Anweisungen](../virtual-network/quick-create-portal.md). In diesem Beispiel befindet sich HDInsight in einem Azure Virtual Network. Hier finden Sie die Beispielkonfiguration von Azure Virtual Network. 

    ![Virtuelles Netzwerk erstellen](media/tutorial-transform-data-using-hive-in-vnet/create-virtual-network.png)
- **HDInsight-Cluster.** Erstellen Sie einen HDInsight-Cluster, und binden Sie ihn in das virtuelle Netzwerk ein, das Sie im vorherigen Schritt erstellt haben. Befolgen Sie dazu die Anweisungen in folgendem Artikel: [Erweitern von Azure HDInsight per Azure Virtual Network](../hdinsight/hdinsight-plan-virtual-network-deployment.md). Hier finden Sie die Beispielkonfiguration von HDInsight in einem virtuellen Netzwerk. 

    ![HDInsight in einem virtuellen Netzwerk](media/tutorial-transform-data-using-hive-in-vnet/hdinsight-in-vnet-configuration.png)
- **Azure PowerShell**. Befolgen Sie die Anweisungen unter [Get started with Azure PowerShell cmdlets](/powershell/azure/install-Az-ps) (Erste Schritte mit Azure PowerShell-Cmdlets).

### <a name="upload-hive-script-to-your-blob-storage-account"></a>Hochladen eines Hive-Skripts in Ihr Blob Storage-Konto

1. Erstellen Sie eine Hive SQL-Datei mit dem Namen **hivescript.hql** und dem folgenden Inhalt:

   ```sql
   DROP TABLE IF EXISTS HiveSampleOut; 
   CREATE EXTERNAL TABLE HiveSampleOut (clientid string, market string, devicemodel string, state string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
   STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

   INSERT OVERWRITE TABLE HiveSampleOut
   Select 
       clientid,
       market,
       devicemodel,
       state
   FROM hivesampletable
   ```
2. Erstellen Sie in Azure Blob Storage einen Container mit dem Namen **adftutorial**, falls dieser noch nicht vorhanden ist.
3. Erstellen Sie einen Ordner mit dem Namen **hivescripts**.
4. Laden Sie die Datei **hivescript.hql** in den Unterordner **hivescripts** hoch.

  

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory


1. Legen Sie den Namen der Ressourcengruppe fest. Im Rahmen dieses Tutorials wird eine Ressourcengruppe erstellt. Sie können aber auch eine bereits vorhandene Ressourcengruppe verwenden. 

    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup" 
    ```
2. Geben Sie den Data Factory-Namen an. Dieser muss global eindeutig sein.

    ```powershell
    $dataFactoryName = "MyDataFactory09142017"
    ```
3. Geben Sie einen Namen für die Pipeline an. 

    ```powershell
    $pipelineName = "MyHivePipeline" # 
    ```
4. Geben Sie einen Namen für die selbstgehostete Integration Runtime-Instanz an. Eine selbstgehostete Integration Runtime-Instanz wird benötigt, wenn die Data Factory auf Ressourcen innerhalb eines VNets (beispielsweise auf Azure SQL-Datenbank) zugreifen muss. 
    ```powershell
    $selfHostedIntegrationRuntimeName = "MySelfHostedIR09142017" 
    ```
2. Starten Sie **PowerShell**. Lassen Sie Azure PowerShell bis zum Ende dieser Schnellstartanleitung geöffnet. Wenn Sie PowerShell schließen und erneut öffnen, müssen Sie die Befehle erneut ausführen. Eine Liste der Azure-Regionen, in denen Data Factory derzeit verfügbar ist, finden Sie, indem Sie die für Sie interessanten Regionen auf der folgenden Seite auswählen und dann **Analysen** erweitern, um **Data Factory** zu finden: [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/). Die von der Data Factory verwendeten Datenspeicher (Azure Storage, Azure SQL-Datenbank usw.) und Computedienste (HDInsight usw.) können sich in anderen Regionen befinden.

    Führen Sie den folgenden Befehl aus, und geben Sie den Benutzernamen und das Kennwort ein, den bzw. das Sie bei der Anmeldung beim Azure-Portal verwendet haben:
        
    ```powershell
    Connect-AzAccount
    ```        
    Führen Sie den folgenden Befehl aus, um alle Abonnements für dieses Konto anzuzeigen:

    ```powershell
    Get-AzSubscription
    ```
    Führen Sie den folgenden Befehl aus, um das gewünschte Abonnement auszuwählen: Ersetzen Sie **SubscriptionId** durch die ID Ihres Azure-Abonnements:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"    
    ```  
3. Erstellen Sie die Ressourcengruppe: „ADFTutorialResourceGroup“, wenn diese in Ihrem Abonnement noch nicht vorhanden ist. 

    ```powershell
    New-AzResourceGroup -Name $resourceGroupName -Location "East Us" 
    ```
4. Erstellen Sie die Data Factory. 

    ```powershell
     $df = Set-AzDataFactoryV2 -Location EastUS -Name $dataFactoryName -ResourceGroupName $resourceGroupName
    ```

    Führen Sie den folgenden Befehl aus, um die Ausgabe anzuzeigen: 

    ```powershell
    $df
    ```

## <a name="create-self-hosted-ir"></a>Erstellen einer selbstgehosteten IR
In diesem Abschnitt erstellen Sie eine selbstgehostete Integration Runtime und ordnen diese einem virtuellen Azure-Computer im gleichen Azure Virtual Network zu, in dem sich Ihr HDInsight-Cluster befindet.

1. Erstellen Sie die selbstgehostete Integration Runtime. Verwenden Sie einen eindeutigen Namen. Es darf keine andere Integration Runtime mit dem gleichen Namen vorhanden sein.

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted
   ```
    Dieser Befehl erstellt eine logische Registrierung der selbstgehosteten Integration Runtime. 
2. Verwenden Sie PowerShell, um Authentifizierungsschlüssel zur Registrierung der selbstgehosteten Integration Runtime abzurufen. Kopieren Sie einen der Schlüssel zum Registrieren der selbstgehosteten Integration Runtime.

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName | ConvertTo-Json
   ```

   Hier ist die Beispielausgabe: 

   ```powershell
   {
       "AuthKey1":  "IR@0000000000000000000000000000000000000=",
       "AuthKey2":  "IR@0000000000000000000000000000000000000="
   }
   ```
    Notieren Sie sich den Wert von **AuthKey1** ohne Anführungszeichen. 
3. Erstellen Sie einen virtuellen Azure-Computer, und binden Sie ihn in das gleiche virtuelle Netzwerk ein, das Ihren HDInsight-Cluster enthält. Ausführliche Informationen finden Sie unter [Erstellen virtueller Computer](../virtual-network/quick-create-portal.md#create-virtual-machines). Binden Sie sie in ein Azure Virtual Network ein. 
4. Laden Sie die [selbstgehostete Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) auf den virtuellen Azure-Computer herunter. Verwenden Sie den Authentifizierungsschlüssel, den Sie im vorherigen Schritt abgerufen haben, um die selbstgehostete Integration Runtime manuell zu registrieren. 

   ![Registrieren der Integration Runtime](media/tutorial-transform-data-using-hive-in-vnet/register-integration-runtime.png)

   Folgende Meldung wird angezeigt, wenn die selbstgehostete Integration Runtime erfolgreich registriert wurde: ![Erfolgreich registriert](media/tutorial-transform-data-using-hive-in-vnet/registered-successfully.png)

   Folgende Seite wird angezeigt, wenn der Knoten mit dem Clouddienst verbunden ist: ![Knoten ist verbunden](media/tutorial-transform-data-using-hive-in-vnet/node-is-connected.png)

## <a name="author-linked-services"></a>Erstellen verknüpfter Dienste

In diesem Abschnitt erstellen Sie zwei verknüpfte Dienste und stellen sie bereit:
- Einen verknüpften Azure Storage-Dienst, der ein Azure Storage-Konto mit der Data Factory verbindet. Dies ist der primäre Speicher, der von Ihrem HDInsight-Cluster verwendet wird. In diesem Fall verwenden wir dieses Azure Storage-Konto auch zum Speichern des Hive-Skripts und der Skriptausgabe.
- Einen verknüpften HDInsight-Dienst. Azure Data Factory übermittelt das Hive-Skript zur Ausführung an diesen HDInsight-Cluster.

### <a name="azure-storage-linked-service"></a>Mit Azure Storage verknüpfter Dienst

Erstellen Sie in Ihrem bevorzugten Editor eine JSON-Datei, kopieren Sie die folgende JSON-Definition eines verknüpften Azure Storage-Diensts hinein, und speichern Sie die Datei als **MyStorageLinkedService.json**.

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>"
      },
      "connectVia": {
        "referenceName": "MySelfhostedIR",
        "type": "IntegrationRuntimeReference"
      }  
    }
}
```

Ersetzen Sie **&lt;accountname&gt; und &lt;accountkey&gt;** durch den Namen bzw. Schlüssel Ihres Azure Storage-Kontos.

### <a name="hdinsight-linked-service"></a>Verknüpfter HDInsight-Dienst

Erstellen Sie in Ihrem bevorzugten Editor eine JSON-Datei, kopieren Sie die folgende JSON-Definition eines verknüpften Azure HDInsight-Diensts hinein, und speichern Sie die Datei als **MyHDInsightLinkedService.json**.

```
{
  "name": "MyHDInsightLinkedService",
  "properties": {     
      "type": "HDInsight",
      "typeProperties": {
          "clusterUri": "https://<clustername>.azurehdinsight.net",
          "userName": "<username>",
          "password": {
            "value": "<password>",
            "type": "SecureString"
          },
          "linkedServiceName": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
          }
      },
      "connectVia": {
        "referenceName": "MySelfhostedIR",
        "type": "IntegrationRuntimeReference"
      }
  }
}
```

Aktualisieren Sie die Werte der folgenden Eigenschaften in der Definition des verknüpften Diensts:

- **userName**. Der Name des Benutzers, der sich beim Cluster anmeldet und den Sie beim Erstellen des Clusters angegeben haben. 
- **password**. Das Kennwort für den Benutzer.
- **clusterUri**. Geben Sie die URL Ihres HDInsight-Clusters im folgenden Format ein: `https://<clustername>.azurehdinsight.net`.  In diesem Artikel wird davon ausgegangen, dass Sie über das Internet auf den Cluster zugreifen können. Sie müssen beispielsweise unter `https://clustername.azurehdinsight.net` eine Verbindung mit dem Cluster herstellen können. Diese Adresse verwendet das öffentliche Gateway, das nicht verfügbar ist, wenn Sie über Netzwerksicherheitsgruppen (Network Security Groups, NSGs) oder benutzerdefinierte Routen (User-Defined Routes, UDRs) den Zugriff aus dem Internet beschränken. Damit Data Factory Aufträge an HDInsight-Cluster in Ihrem virtuellen Azure-Netzwerk übermitteln kann, muss das Netzwerk so konfigurieren sein, dass die URL zur privaten IP-Adresse des von HDInsight verwendeten Gateways aufgelöst werden kann.

  1. Öffnen Sie im Azure-Portal das virtuelle Netzwerk, in dem sich HDInsight befindet. Öffnen Sie die Netzwerkschnittstelle, deren Name mit `nic-gateway-0` beginnt. Notieren Sie die private IP-Adresse. Beispiel: 10.6.0.15. 
  2. Wenn in Ihrem Azure Virtual Network DNS-Server vorhanden sind, aktualisieren Sie den DNS-Eintrag, sodass die URL des HDInsight-Clusters, `https://<clustername>.azurehdinsight.net`, in `10.6.0.15` aufgelöst werden kann. Dies ist die empfohlene Vorgehensweise. Wenn in Ihrem virtuellen Azure-Netzwerk kein DNS-Server vorhanden ist, können Sie dieses Problem vorübergehend umgehen, indem Sie die Hostdatei (C:\Windows\System32\drivers\etc) aller virtuellen Computer bearbeiten, die als Knoten für die selbstgehostete Integration Runtime-Instanz registriert sind. Fügen Sie hierzu einen Eintrag wie den folgenden hinzu: 
  
        `10.6.0.15 myHDIClusterName.azurehdinsight.net`

## <a name="create-linked-services"></a>Erstellen von verknüpften Diensten
Wechseln Sie in PowerShell zu dem Ordner, in dem Sie JSON-Dateien erstellt haben, und führen Sie den folgenden Befehl aus, um die verknüpften Dienste bereitzustellen: 

1. Wechseln Sie in PowerShell zu dem Ordner, in dem Sie JSON-Dateien erstellt haben.
2. Führen Sie den folgenden Befehl aus, um einen verknüpften Azure Storage-Dienst zu erstellen: 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyStorageLinkedService" -File "MyStorageLinkedService.json"
    ```
3. Führen Sie den folgenden Befehl aus, um einen verknüpften Azure HDInsight-Dienst zu erstellen: 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyHDInsightLinkedService" -File "MyHDInsightLinkedService.json"
    ```

## <a name="author-a-pipeline"></a>Erstellen einer Pipeline
In diesem Schritt erstellen Sie eine neue Pipeline mit einer Hive-Aktivität. Die Aktivität führt ein Hive-Skript aus, um Daten aus einer Beispieltabelle zurückzugeben und in einem von Ihnen definierten Pfad zu speichern. Erstellen Sie in Ihrem bevorzugten Editor eine JSON-Datei, kopieren Sie die folgende JSON-Definition einer Pipelinedefinition hinein, und speichern Sie die Datei als **MyHivePipeline.json**.


```json
{
  "name": "MyHivePipeline",
  "properties": {
    "activities": [
      {
        "name": "MyHiveActivity",
        "type": "HDInsightHive",
        "linkedServiceName": {
            "referenceName": "MyHDILinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "scriptPath": "adftutorial\\hivescripts\\hivescript.hql",
          "getDebugInfo": "Failure",
          "defines": {           
            "Output": "wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/"
          },
          "scriptLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }
    ]
  }
}

```

Beachten Sie folgende Punkte:

- **scriptPath** zeigt auf den Pfad zu dem Hive-Skript im Azure Storage-Konto, das Sie für „MyStorageLinkedService“ verwendet haben. Der Pfad berücksichtigt die Groß- und Kleinschreibung.
- **Output** ist ein im Hive-Skript verwendetes Argument. Verwenden Sie das Format `wasb://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/`, um auf einen vorhandenen Ordner in Ihrem Azure Storage zu zeigen. Der Pfad berücksichtigt die Groß- und Kleinschreibung. 

Wechseln Sie zu dem Ordner, in dem Sie die JSON-Dateien erstellt haben, und führen Sie den folgenden Befehl aus, um die Pipeline bereitzustellen: 


```powershell
Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $pipelineName -File "MyHivePipeline.json"
```

## <a name="start-the-pipeline"></a>Starten der Pipeline 

1. Starten einer Pipelineausführung Die ID der Pipelineausführung wird für die zukünftige Überwachung ebenfalls erfasst.

    ```powershell
    $runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
   ```
2. Führen Sie das folgende Skript aus, um den Status der Pipelineausführung kontinuierlich zu überwachen, bis sie beendet ist.

    ```powershell
    while ($True) {
        $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if(!$result) {
            Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
        }
        elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
        }
        else {
            Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
        ($result | Format-List | Out-String)
        Start-Sleep -Seconds 15
    }
    
    Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"

    Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```

   Hier ist die Ausgabe der Beispielausführung:

    ```json
    Pipeline run status: In Progress
    
    ResourceGroupName : ADFV2SampleRG2
    DataFactoryName   : SampleV2DataFactory2
    ActivityName      : MyHiveActivity
    PipelineRunId     : 000000000-0000-0000-000000000000000000
    PipelineName      : MyHivePipeline
    Input             : {getDebugInfo, scriptPath, scriptLinkedService, defines}
    Output            :
    LinkedServiceName :
    ActivityRunStart  : 9/18/2017 6:58:13 AM
    ActivityRunEnd    :
    DurationInMs      :
    Status            : InProgress
    Error             :
    
    Pipeline ' MyHivePipeline' run finished. Result:
    
    ResourceGroupName : ADFV2SampleRG2
    DataFactoryName   : SampleV2DataFactory2
    ActivityName      : MyHiveActivity
    PipelineRunId     : 0000000-0000-0000-0000-000000000000
    PipelineName      : MyHivePipeline
    Input             : {getDebugInfo, scriptPath, scriptLinkedService, defines}
    Output            : {logLocation, clusterInUse, jobId, ExecutionProgress...}
    LinkedServiceName :
    ActivityRunStart  : 9/18/2017 6:58:13 AM
    ActivityRunEnd    : 9/18/2017 6:59:16 AM
    DurationInMs      : 63636
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity Output section:
    "logLocation": "wasbs://adfjobs@adfv2samplestor.blob.core.windows.net/HiveQueryJobs/000000000-0000-47c3-9b28-1cdc7f3f2ba2/18_09_2017_06_58_18_023/Status"
    "clusterInUse": "https://adfv2HivePrivate.azurehdinsight.net"
    "jobId": "job_1505387997356_0024"
    "ExecutionProgress": "Succeeded"
    "effectiveIntegrationRuntime": "MySelfhostedIR"
    Activity Error section:
    "errorCode": ""
    "message": ""
    "failureType": ""
    "target": "MyHiveActivity"
    ```
4. Suchen Sie im Ordner `outputfolder` nach der neuen Datei, die als Ergebnis der Hive-Abfrage erstellt wurde. Der Inhalt sollte in etwa wie die folgende Beispielausgabe aussehen: 

   ```
   8 en-US SCH-i500 California
   23 en-US Incredible Pennsylvania
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   212 en-US SCH-i500 New York
   246 en-US SCH-i500 District Of Columbia
   246 en-US SCH-i500 District Of Columbia
   ```

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie die folgenden Schritte ausgeführt: 

> [!div class="checklist"]
> * Erstellen einer Data Factory. 
> * Erstellen und Einrichten einer selbstgehosteten Integration Runtime
> * Erstellen und Bereitstellen von verknüpften Diensten
> * Erstellen und Bereitstellen einer Pipeline, die eine Hive-Aktivität enthält
> * Starten einer Pipelineausführung
> * Überwachen der Pipelineausführung 
> * Überprüfen der Ausgabe 

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie mithilfe eines Spark-Clusters in Azure Daten transformieren:

> [!div class="nextstepaction"]
>[Branchen und Verketten der Data Factory-Ablaufsteuerung](tutorial-control-flow.md)