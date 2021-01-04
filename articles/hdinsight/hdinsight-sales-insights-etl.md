---
title: 'Tutorial: Erstellen einer End-to-End-ETL-Pipeline zum Gewinnen von Erkenntnissen aus Vertriebsdaten in Azure HDInsight'
description: Hier erfahren Sie, wie Sie ETL-Pipelines mit Azure HDInsight erstellen, um mithilfe von bedarfsgesteuerten Spark-Clustern und Power BI Erkenntnisse aus Vertriebsdaten zu gewinnen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 04/15/2020
ms.openlocfilehash: 1031c34a44a253c7458ef78c6371b88014e882ed
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746467"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights-in-azure-hdinsight"></a>Tutorial: Erstellen einer End-to-End-Datenpipeline zum Gewinnen von Erkenntnissen aus Vertriebsdaten in Azure HDInsight

In diesem Tutorial erstellen Sie eine End-to-End-Datenpipeline, die Extrahierungs-, Transformations- und Ladevorgänge (ETL) durchführt. Die Pipeline verwendet in Azure HDInsight ausgeführte [Apache Spark](./spark/apache-spark-overview.md)- und Apache Hive-Cluster zum Abfragen und Bearbeiten der Daten. Sie verwenden auch Technologien wie Azure Data Lake Storage Gen2 für die Datenspeicherung und Power BI für die Visualisierung.

Diese Datenpipeline kombiniert die Daten aus unterschiedlichen Speichern, entfernt unerwünschte Daten, fügt neue Daten an und lädt sie dann alle wieder in den Speicher, um geschäftliche Erkenntnisse zu visualisieren. Weitere Informationen zu ETL-Pipelines finden Sie unter [Bedarfsorientiertes Extrahieren, Transformieren und Laden (ETL)](./hadoop/apache-hadoop-etl-at-scale.md).

![ETL-Architektur](./media/hdinsight-sales-insights-etl/architecture.png)

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* Mindestens Version 2.2.0 der Azure CLI. Weitere Informationen finden Sie unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).

* jq, ein JSON-Befehlszeilenprozessor.  Siehe [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* Mitglied der [integrierten Azure-Rolle „Besitzer“](../role-based-access-control/built-in-roles.md)

* Wenn Sie PowerShell zum Auslösen der Data Factory-Pipeline verwenden, benötigen Sie das [Az-Modul](/powershell/azure/).

* [Power BI Desktop](https://aka.ms/pbiSingleInstaller), um am Ende dieses Tutorials geschäftliche Erkenntnisse zu visualisieren

## <a name="create-resources"></a>Erstellen von Ressourcen

### <a name="clone-the-repository-with-scripts-and-data"></a>Klonen des Repositorys mit Skripts und Daten

1. Melden Sie sich bei Ihrem Azure-Abonnement an. Wenn Sie Azure Cloud Shell verwenden möchten, wählen Sie in der rechten oberen Ecke des Codeblocks die Option **Ausprobieren** aus. Geben Sie andernfalls den nachstehenden Befehl ein:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Stellen Sie sicher, dass Sie Mitglied der Azure-Rolle [Besitzer](../role-based-access-control/built-in-roles.md) sind. Ersetzen Sie `user@contoso.com` durch Ihr Konto, und geben Sie dann den folgenden Befehl ein:

    ```azurecli
    az role assignment list \
    --assignee "user@contoso.com" \
    --role "Owner"
    ```

    Wird kein Datensatz zurückgegeben, sind Sie kein Mitglied und können dieses Tutorial nicht durchführen.

1. Laden Sie die Daten und Skripts für dieses Tutorial aus dem [Repository für das HDInsight-ETL-Szenario mit Vertriebsdaten](https://github.com/Azure-Samples/hdinsight-sales-insights-etl) herunter. Geben Sie den folgenden Befehl ein:

    ```bash
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. Vergewissern Sie sich, ob `salesdata scripts templates` erstellt wurde. Verwenden Sie dazu den folgenden Befehl:

   ```bash
   ls
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>Bereitstellen der erforderlichen Azure-Ressourcen für die Pipeline

1. Fügen Sie Ausführungsberechtigungen für alle Skripts hinzu, indem Sie Folgendes eingeben:

    ```bash
    chmod +x scripts/*.sh
    ````

1. Legen Sie die Variable für die Ressourcengruppe fest. Ersetzen Sie `RESOURCE_GROUP_NAME` durch den Namen einer vorhandenen oder neuen Ressourcengruppe, und geben Sie dann den folgenden Befehl ein:

    ```bash
    resourceGroup="RESOURCE_GROUP_NAME"
    ```

1. Führen Sie das Skript aus. Ersetzen Sie `LOCATION` durch den gewünschten Wert, und geben Sie anschließend den folgenden Befehl ein:

    ```bash
    ./scripts/resources.sh $resourceGroup LOCATION
    ```

    Falls Sie nicht wissen, welche Region Sie angeben sollen, können Sie mit dem Befehl [az account list-locations](/cli/azure/account#az-account-list-locations) eine Liste der unterstützten Regionen für Ihr Abonnement abrufen.

    Mit dem Befehl werden die folgenden Ressourcen bereitgestellt:

    * Ein Azure Blob Storage-Konto. Dieses Konto enthält die Vertriebsdaten des Unternehmens.
    * Ein Azure Data Lake Storage Gen2-Konto. Dieses Konto dient als Speicherkonto für beide HDInsight-Cluster. Weitere Informationen zu HDInsight und Data Lake Storage Gen2 finden Sie unter [Azure HDInsight-Integration in Data Lake Storage Gen2](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
    * Eine vom Benutzer zugewiesene verwaltete Identität. Dieses Konto gewährt den HDInsight-Clustern Zugriff auf das Data Lake Storage Gen2-Konto.
    * Einen Apache Spark-Cluster. Dieser Cluster wird zum Bereinigen und Transformieren der Rohdaten verwendet.
    * Einen [Interactive Query](./interactive-query/apache-interactive-query-get-started.md)-Cluster in Apache Hive. Mit diesem Cluster können die Vertriebsdaten abgefragt und mit Power BI visualisiert werden.
    * Ein virtuelles Azure-Netzwerk, das von NSG-Regeln (Netzwerksicherheitsgruppe) unterstützt wird. Dieses virtuelle Netzwerk ermöglicht die Kommunikation der Cluster und sichert zudem ihre Kommunikation.

Die Erstellung des Clusters dauert ungefähr 20 Minuten.

Das Standardkennwort für den SSH-Zugriff auf die Cluster ist `Thisisapassword1`. Wenn Sie das Kennwort ändern möchten, rufen Sie die `./templates/resourcesparameters_remainder.json` auf, und ändern Sie das Kennwort für die Parameter `sparksshPassword`, `sparkClusterLoginPassword`, `llapClusterLoginPassword` und `llapsshPassword`.

### <a name="verify-deployment-and-collect-resource-information"></a>Überprüfen der Bereitstellung und Sammeln von Ressourceninformationen

1. Um den Status der Bereitstellung zu überprüfen, navigieren Sie im Azure-Portal zur Ressourcengruppe. Wählen Sie unter **Einstellungen** die Option **Bereitstellungen** und dann Ihre Bereitstellung aus. Hier sehen Sie die Ressourcen, die erfolgreich bereitgestellt wurden, und jene, deren Bereitstellung noch läuft.

1. Geben Sie zum Anzeigen der Clusternamen den folgenden Befehl ein:

    ```bash
    sparkClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.sparkClusterName.value')
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')

    echo "Spark Cluster" $sparkClusterName
    echo "LLAP cluster" $llapClusterName
    ```

1. Geben Sie den folgenden Befehl ein, um das Azure-Speicherkonto und den Zugriffsschlüssel anzuzeigen:

    ```azurecli
    blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')

    blobKey=$(az storage account keys list \
        --account-name $blobStorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $blobStorageName
    echo $blobKey
    ```

1. Geben Sie den folgenden Befehl ein, um das Data Lake Storage Gen2-Konto und den Zugriffsschlüssel anzuzeigen:

    ```azurecli
    ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

    adlsKey=$(az storage account keys list \
        --account-name $ADLSGen2StorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $ADLSGen2StorageName
    echo $adlsKey
    ```

### <a name="create-a-data-factory"></a>Erstellen einer Data Factory

Azure Data Factory (ADF) ist ein Tool, mit dem Sie Azure Pipelines automatisieren können. Es ist nicht die einzige Möglichkeit, um diese Aufgaben durchzuführen, eignet sich aber hervorragend zum Automatisieren der Prozesse. Weitere Informationen zu Azure Data Factory finden Sie in der [Dokumentation zu Azure Data Factory](https://azure.microsoft.com/services/data-factory/).

Diese Data Factory enthält eine Pipeline mit zwei Aktivitäten:

* Die erste Aktivität kopiert die Daten aus dem Azure Blob Storage-Konto in das Data Lake Storage Gen2-Speicherkonto, um die Datenerfassung zu simulieren.
* Die zweite Aktivität transformiert die Daten im Spark-Cluster. Das Skript transformiert die Daten, indem unerwünschte Spalten entfernt werden. Es fügt auch eine neue Spalte an, die den durch eine einzelne Transaktion generierten Umsatz berechnet.

Führen Sie zum Einrichten der Azure Data Factory-Pipeline den folgenden Befehl aus.  Sie sollten sich immer noch im Verzeichnis `hdinsight-sales-insights-etl` befinden.

```bash
blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')
ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

./scripts/adf.sh $resourceGroup $ADLSGen2StorageName $blobStorageName
```

Das Skript führt folgende Schritte aus:

1. Es erstellt einen Dienstprinzipal mit `Storage Blob Data Contributor`-Berechtigungen für das Data Lake Storage Gen2-Speicherkonto.
1. Es ruft ein Authentifizierungstoken zum Autorisieren von POST-Anforderungen an die [REST-API für das Data Lake Storage Gen2-Dateisystem](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create) ab.
1. Es trägt in den Dateien `sparktransform.py` und `query.hql` den tatsächlichen Namen Ihres Data Lake Storage Gen2-Speicherkontos ein.
1. Es ruft Speicherschlüssel für die Data Lake Storage Gen2- und Blob Storage-Konten ab.
1. Es erstellt eine weitere Ressourcenbereitstellung, um eine Azure Data Factory-Pipeline mit den zugehörigen verknüpften Diensten und Aktivitäten zu erstellen. Es übergibt die Speicherschlüssel als Parameter an die Vorlagendatei, sodass die verknüpften Dienste korrekt auf die Speicherkonten zugreifen können.

## <a name="run-the-data-pipeline"></a>Ausführen der Datenpipeline

### <a name="trigger-the-data-factory-activities"></a>Auslösen der Data Factory-Aktivitäten

Die erste Aktivität in der von Ihnen erstellten Data Factory-Pipeline verschiebt die Daten aus Blob Storage in Data Lake Storage Gen2. Die zweite Aktivität wendet die Spark-Transformationen auf die Daten an und speichert die transformierten CSV-Dateien an einem neuen Speicherort. Die Ausführung der gesamten Pipeline kann einige Minuten dauern.

Geben Sie den folgenden Befehl ein, um den Data Factory-Namen abzurufen:

```azurecli
cat resourcesoutputs_adf.json | jq -r '.properties.outputs.factoryName.value'
```

Sie können die Pipeline wie folgt auslösen:

* Lösen Sie die Data Factory-Pipeline in PowerShell aus. Ersetzen Sie `RESOURCEGROUP` und `DataFactoryName` durch die entsprechenden Werte, und führen Sie dann die folgenden Befehle aus:

    ```powershell
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

    $resourceGroup="RESOURCEGROUP"
    $dataFactory="DataFactoryName"

    $pipeline =Invoke-AzDataFactoryV2Pipeline `
        -ResourceGroupName $resourceGroup `
        -DataFactory $dataFactory `
        -PipelineName "IngestAndTransform"

    Get-AzDataFactoryV2PipelineRun `
        -ResourceGroupName $resourceGroup  `
        -DataFactoryName $dataFactory `
        -PipelineRunId $pipeline
    ```

    Führen Sie `Get-AzDataFactoryV2PipelineRun` nach Bedarf erneut aus, um den Status zu überwachen.

    oder

* Öffnen Sie die Data Factory, und wählen Sie **Erstellen und überwachen** aus. Lösen die Pipeline `IngestAndTransform` über das Portal aus. Informationen zum Auslösen von Pipelines über das Portal finden Sie unter [Erstellen bedarfsgesteuerter Apache Hadoop-Cluster in HDInsight mit Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline).

Zum Überprüfen, ob die Pipeline ausgeführt wurde, können Sie einen der beiden folgenden Schritte ausführen:

* Rufen Sie im Portal den Abschnitt **Überwachen** in Ihrer Data Factory auf.
* Rufen Sie im Azure Storage-Explorer Ihr Data Lake Storage Gen2-Speicherkonto auf. Wechseln Sie zum Dateisystem `files`, navigieren Sie zum Ordner `transformed`, und überprüfen Sie den Inhalt, um festzustellen, ob die Pipeline erfolgreich ausgeführt wurde.

Weitere Möglichkeiten zum Transformieren von Daten mithilfe von HDInsight finden Sie in [diesem Artikel zur Verwendung von Jupyter Notebook](/azure/hdinsight/spark/apache-spark-load-data-run-query).

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Erstellen einer Tabelle im Interactive Query-Cluster zum Anzeigen von Daten in Power BI

1. Kopieren Sie die Datei `query.hql` mit SCP in den LLAP-Cluster. Geben Sie den folgenden Befehl ein:

    ```bash
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')
    scp scripts/query.hql sshuser@$llapClusterName-ssh.azurehdinsight.net:/home/sshuser/
    ```

    Erinnerung: Das Standardkennwort lautet `Thisisapassword1`.

1. Verwenden Sie SSH für den Zugriff auf den LLAP-Cluster. Geben Sie den folgenden Befehl ein:

    ```bash
    ssh sshuser@$llapClusterName-ssh.azurehdinsight.net
    ```

1. Verwenden Sie den folgenden Befehl, um das Skript auszuführen:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

    Dieses Skript erstellt eine verwaltete Tabelle im Interactive Query-Cluster, auf die Sie über Power BI zugreifen können.

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Erstellen eines Power BI-Dashboards anhand von Vertriebsdaten

1. Öffnen Sie Power BI Desktop.

1. Navigieren Sie im Menü zu **Daten abrufen** > **Mehr...**  > **Azure** > **HDInsight Interactive Query** .

1. Wählen Sie **Verbinden** .

1. Führen Sie im Dialogfeld **HDInsight Interactive Query** die folgenden Schritte aus:
    1. Geben Sie im Textfeld **Server** den Namen Ihres LLAP-Clusters im folgenden Format ein: `https://LLAPCLUSTERNAME.azurehdinsight.net`.
    1. Geben Sie im Textfeld **Datenbank** die Zeichenfolge `default` ein.
    1. Klicken Sie auf **OK** .

1. Führen Sie im Dialogfeld **AzureHive** die folgenden Schritte aus:
    1. Geben Sie im Textfeld **Benutzername** den Namen `admin` ein.
    1. Geben Sie in das Textfeld **Kennwort** das Kennwort `Thisisapassword1` ein.
    1. Wählen Sie **Verbinden** .

1. Wählen Sie im **Navigator** den Eintrag `sales` und/oder `sales_raw` aus, um eine Vorschau der Daten anzuzeigen. Nachdem die Daten geladen wurden, können Sie mit dem Dashboard experimentieren, das Sie erstellen möchten. Informationen zu den ersten Schritten mit Power BI-Dashboards finden Sie unter den folgenden Links:

* [Einführung in Dashboards für Power BI-Designer](https://docs.microsoft.com/power-bi/service-dashboards)
* [Tutorial: Erste Schritte mit dem Power BI-Dienst](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diese Anwendung nicht weiterverwenden möchten, löschen Sie mit dem folgenden Befehl alle Ressourcen, damit sie Ihnen nicht in Rechnung gestellt werden.

1. Geben Sie zum Entfernen der Ressourcengruppe den folgenden Befehl ein:

    ```azurecli
    az group delete -n $resourceGroup
    ```

1. Geben Sie zum Entfernen des Dienstprinzipals die folgenden Befehle ein:

    ```azurecli
    servicePrincipal=$(cat serviceprincipal.json | jq -r '.name')
    az ad sp delete --id $servicePrincipal
    ```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bedarfsorientiertes Extrahieren, Transformieren und Laden (ETL)](./hadoop/apache-hadoop-etl-at-scale.md)
