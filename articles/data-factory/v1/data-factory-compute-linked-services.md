---
title: Von Azure Data Factory Version 1 unterstützte Compute-Umgebungen
description: Erfahren Sie mehr über Compute-Umgebungen, die in Azure Data Factory-Pipelines (wie z.B. Azure HDInsight) für die Transformation oder Verarbeitung von Daten verwendet werden können.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 07e13036a427ff7ff4f0cbbd2dfd6fbe272bf915
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377192"
---
# <a name="compute-environments-supported-by-azure-data-factory-version-1"></a>Von Azure Data Factory Version 1 unterstützte Compute-Umgebungen
> [!NOTE]
> Dieser Artikel bezieht sich auf Version 1 von Azure Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [Verknüpfte Compute Services](../compute-linked-services.md).

In diesem Artikel werden die Compute-Umgebungen beschrieben, mit denen Sie Daten verarbeiten oder transformieren können. Darüber hinaus werden Einzelheiten zu verschiedenen Konfigurationen beschrieben (bedarfsgesteuerte Compute-Umgebung im Vergleich zu einer eigenen Compute-Umgebung), die Data Factory unterstützt, wenn Sie verknüpfte Dienste konfigurieren, um diese Compute-Umgebungen mit Azure Data Factory zu verknüpfen.

Die folgende Tabelle enthält eine Liste mit Compute-Umgebungen, die von Data Factory unterstützt werden, sowie die Aktivitäten, die darin ausgeführt werden können. 

| Compute-Umgebung                      | activities                               |
| ---------------------------------------- | ---------------------------------------- |
| [Bedarfsgesteuerter Azure HDInsight-Cluster](#azure-hdinsight-on-demand-linked-service) oder [eigener HDInsight-Cluster](#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning Studio (klassisch)](#azure-machine-learning-studio-classic-linked-service) | [Aktivitäten in Studio (klassisch): Batchausführung und Ressourcenaktualisierung](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service), [Azure Synapse Analytics](#azure-synapse-analytics-linked-service), [SQL Server](#sql-server-linked-service) | [Aktivität „Gespeicherte Prozedur“](data-factory-stored-proc-activity.md) |

## <a name="hdinsight-versions-supported-in-data-factory"></a><a name="supported-hdinsight-versions-in-azure-data-factory"></a>In Data Factory unterstützte HDInsight-Versionen
Azure HDInsight unterstützt mehrere Hadoop-Clusterversionen, die Sie jederzeit bereitstellen können. Jede unterstützte Version erstellt eine bestimmte Version der HDP-Distribution (Hortonworks Data Platform) und eine Reihe von Komponenten in der Distribution. 

Die Liste der unterstützten HDInsight-Versionen wird von Microsoft mit den neuesten Komponenten und Korrekturen für das Hadoop-Ökosystem aktualisiert. Ausführliche Informationen finden Sie unter [Unterstützte HDInsight-Versionen](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

> [!IMPORTANT]
> Die Linux-basierte HDInsight-Version 3.3 wurde am 31. Juli 2017 eingestellt. Kunden mit bedarfsgesteuerten verknüpften HDInsight-Diensten der Data Factory-Version 1 konnten bis zum 15. Dezember 2017 eine neuere Version von HDInsight testen und ein Upgrade darauf durchführen. Die Windows-basierte HDInsight-Version wird am 31. Juli 2018 eingestellt.
>
> 

### <a name="after-the-retirement-date"></a>Nach der Einstellung 

Nach dem 15. Dezember 2017:

- Sie können keine Linux-basierten HDInsight-Cluster der Version 3.3 (oder einer älteren Version) mit einem bedarfsgesteuerten verknüpften HDInsight-Dienst in der Data Factory-Version 1 mehr erstellen. 
- Wenn die [Eigenschaften **osType** und **Version**](#azure-hdinsight-on-demand-linked-service) in der JSON-Definition nicht explizit für einen vorhandenen bedarfsgesteuerten verknüpften HDInsight-Dienst in Data Factory Version 1 angegeben werden, wird der Standardwert von **Version=3.1, osType=Windows** in **Version=\<latest HDI default version\>(https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning) , osType=Linux** geändert.

Nach dem 31. Juli 2018:

- Sie können keine Version von Windows-basierten HDInsight-Clustern mit einem bedarfsgesteuerten verknüpften HDInsight-Dienst in der Data Factory-Version 1 mehr erstellen. 

### <a name="recommended-actions"></a>Empfohlene Aktionen

- Aktualisieren Sie zur Verwendung der neuesten Komponenten und Korrekturen des Hadoop-Ökosystems die [Eigenschaften **osType** und **Version**](#azure-hdinsight-on-demand-linked-service) der betroffenen Definitionen von bedarfsgesteuerten verknüpften HDInsight-Diensten der Data Factory-Version 1 auf neuere Linux-basierte HDInsight-Versionen (HDInsight 3.6). 
- Testen Sie vor dem 15. Dezember 2017 die Hive-, Pig-, MapReduce- und Hadoop-Streamingaktivitäten der Data Factory-Version 1, die auf den betroffenen verknüpften Dienst verweisen. Vergewissern Sie sich, dass sie mit den neuen Standardwerten für **osType** und **Version** (**Version=3.6**, **osType=Linux**) bzw. mit der expliziten HDInsight-Version und dem Betriebssystemtyp kompatibel sind, auf die Sie das Upgrade durchführen. 
  Weitere Informationen zur Kompatibilität finden Sie unter [Migrieren von einem Windows-basierten HDInsight-Cluster zu einem Linux-basierten Cluster](../../hdinsight/index.yml) sowie unter [Hortonworks-Versionshinweise im Zusammenhang mit HDInsight-Versionen](../../hdinsight/hdinsight-component-versioning.md). 
- Legen Sie vor dem 15. Dezember 2017 **osType** explizit auf **Windows** fest, wenn Sie weiterhin einen bedarfsgesteuerten verknüpften HDInsight-Dienst der Data Factory-Version 1 verwenden möchten, um Windows-basierte HDInsight-Cluster zu erstellen. Es empfiehlt sich allerdings, bis zum 31. Juli 2018 zu Linux-basierten HDInsight-Clustern zu migrieren. 
- Wenn Sie zum Ausführen der benutzerdefinierten .NET-Aktivität der Data Factory-Version 1 einen verknüpften bedarfsgesteuerten HDInsight-Dienst verwenden, aktualisieren Sie die JSON-Definition der benutzerdefinierten .NET-Aktivität so, dass stattdessen ein verknüpfter Azure Batch-Dienst verwendet wird. Weitere Informationen finden Sie unter [Verwenden von benutzerdefinierten Aktivitäten in einer Azure Data Factory-Pipeline](./data-factory-use-custom-activities.md). 

> [!Note]
> Bei Verwendung eines eigenen, mit HDInsight verknüpften Geräts mit eigenem Cluster in der Data Factory, Version 1 oder eines eigenen bedarfsgesteuerten verknüpften HDInsight-Diensts in Azure Data Factory ist keine Aktion erforderlich. In diesen Szenarien wird die Richtlinie zur Unterstützung der neuesten Version von HDInsight-Clustern bereits erzwungen. 
>
> 


## <a name="on-demand-compute-environment"></a>Bedarfsgesteuerte Compute-Umgebung
In einer bedarfsgesteuerten Konfiguration wird die Compute-Umgebung vollständig von Data Factory verwaltet. Die Compute-Umgebung wird von Data Factory automatisch erstellt, bevor ein Auftrag für die Verarbeitung von Daten übermittelt wird. Nach Abschluss des Auftrags wird die Compute-Umgebung wieder entfernt. 

Sie können einen verknüpften Dienst für eine bedarfsgesteuerte Compute-Umgebung erstellen. Verwenden Sie den verknüpften Dienst, um die Compute-Umgebung zu konfigurieren und differenzierte Einstellungen für Auftragsausführung, Clusterverwaltung und Bootstrappingaktionen festzulegen.

> [!NOTE]
> Die bedarfsgesteuerte Konfiguration wird derzeit nur für HDInsight-Cluster unterstützt.
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Bedarfsgesteuerter verknüpfter Azure HDInsight-Dienst
Data Factory kann für die Datenverarbeitung automatisch einen bedarfsgesteuerten Windows-/Linux-basierten HDInsight-Cluster erstellen. Der Cluster wird in der gleichen Region erstellt wie das Speicherkonto, das dem Cluster zugeordnet ist. Verwenden Sie zum Erstellen des Clusters die JSON-Eigenschaft **linkedServiceName**.

Beachten Sie die folgenden *wichtigen* Hinweise zum bedarfsgesteuerten verknüpften HDInsight-Dienst:

* Der bedarfsgesteuerte HDInsight-Cluster erscheint nicht in Ihrem Azure-Abonnement. Der Data Factory-Dienst verwaltet den bedarfsgesteuerten HDInsight-Cluster für Sie.
* Die Protokolle für Aufträge, die in einem bedarfsgesteuerten HDInsight-Cluster ausgeführt werden, werden in das mit dem HDInsight-Cluster verknüpfte Speicherkonto kopiert. Auf diese Protokolle können Sie im Bereich **Details zur Aktivitätsausführung** des Azure-Portals zugreifen. Weitere Informationen finden Sie unter [Überwachen und Verwalten von Azure Data Factory-Pipelines mit dem Azure-Portal und PowerShell](data-factory-monitor-manage-pipelines.md).
* Ihnen wird nur die Zeit in Rechnung gestellt, in der der HDInsight-Cluster aktiv ist und Aufträge ausführt.

> [!IMPORTANT]
> Die bedarfsgesteuerte Bereitstellung eines HDInsight-Clusters dauert üblicherweise mindestens *20 Minuten*.
>
> 

### <a name="example"></a>Beispiel
Die folgende JSON definiert einen bedarfsgesteuerten Linux-basierten mit HDInsight verknüpften Dienst. Data Factory erstellt bei der Verarbeitung eines Datenslices automatisch einen *Linux-basierten* HDInsight-Cluster. 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.6",
            "osType": "Linux",
            "clusterSize": 1,
            "timeToLive": "00:05:00",            
            "linkedServiceName": "AzureStorageLinkedService"
        }
    }
}
```

> [!IMPORTANT]
> Der HDInsight-Cluster erstellt einen *Standardcontainer* in dem Azure-Blobspeicher, den Sie in der JSON-Eigenschaft **linkedServiceName** angeben. HDInsight löscht diesen Container nicht, wenn der Cluster gelöscht wird. Dies ist so beabsichtigt. In einem bedarfsgesteuerten verknüpften HDInsight-Dienst wird jedes Mal ein HDInsight-Cluster erstellt, wenn ein Slice verarbeitet werden muss – es sei denn, es ist bereits ein aktiver Cluster vorhanden (**timeToLive**). Der Cluster wird nach Abschluss der Verarbeitung wieder gelöscht. 
>
> Wenn mehr Slices verarbeitet werden, werden in Ihrem Blobspeicher viele Container angezeigt. Sofern Sie die Container nicht für Problembehandlungsaufträge benötigen, sollten Sie sie ggf. löschen, um die Speicherkosten zu senken. Die Namen dieser Container folgen einem Muster: `adf<your Data Factory name>-<linked service name>-<date and time>`. Container in Blob Storage können mit Tools wie dem [Microsoft Azure Storage-Explorer](https://storageexplorer.com/) gelöscht werden.
>
> 

### <a name="properties"></a>Eigenschaften
| Eigenschaft                     | BESCHREIBUNG                              | Erforderlich |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | Legen Sie die Typeigenschaft auf **HDInsightOnDemand** fest. | Ja      |
| clusterSize                  | Die Anzahl von Worker- und Datenknoten im Cluster. Der HDInsight-Cluster wird mit zwei Hauptknoten und der Anzahl von Workerknoten erstellt, die Sie für diese Eigenschaft angeben. Die Knoten haben jeweils die Größe „Standard_D3“ und verfügen somit über vier Kerne. Ein Cluster mit vier Workerknoten besitzt also 24 Kerne (4 \* 4 = 16 für die Workerknoten plus 2 \* 4 = 8 für die Hauptknoten). Ausführliche Informationen zum Tarif „Standard_D3“ finden Sie unter [Einrichten von Clustern in HDInsight mit Hadoop, Spark, Kafka usw](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). | Ja      |
| timeToLive                   | Die zulässige Leerlaufzeit für den bedarfsgesteuerten HDInsight-Cluster. Gibt an, wie lange der bedarfsgesteuerte HDInsight-Cluster nach Abschluss einer Aktivitätsausführung aktiv bleibt, wenn keine anderen aktiven Aufträge im Cluster vorhanden sind.<br /><br />Beispiel: Wenn eine Aktivitätsausführung sechs Minuten dauert und **timeToLive** auf fünf Minuten festgelegt ist, bleibt der Cluster nach den sechs Minuten, die für die Verarbeitung der Aktivitätsausführung benötigt werden, noch fünf Minuten aktiv. Wenn in dem sechsminütigen Zeitfenster eine weitere Aktivitätsausführung stattfindet, wird sie vom gleichen Cluster verarbeitet.<br /><br />Die Erstellung eines bedarfsgesteuerten HDInsight-Clusters ist ein aufwendiger Vorgang und kann daher eine Weile dauern. Verwenden Sie diese Einstellung bei Bedarf, um die Leistung einer Data Factory durch Wiederverwendung eines bedarfsgesteuerten HDInsight-Clusters zu verbessern.<br /><br />Wenn Sie **timeToLive** auf **0** festlegen, wird der Cluster unmittelbar nach Abschluss der Aktivitätsausführung gelöscht. Wenn Sie dagegen einen hohen Wert festlegen, befindet sich der Cluster unter Umständen im Leerlauf, was unnötig hohe Kosten verursachen kann. Der Wert muss daher auf Ihre individuellen Anforderungen abgestimmt werden.<br /><br />Wenn der Wert für **timeToLive** ordnungsgemäß festgelegt ist, kann die Instanz des bedarfsgesteuerten HDInsight-Clusters von mehreren Pipelines gemeinsam genutzt werden. | Ja      |
| version                      | Die Version des HDInsight-Clusters. Informationen zu den zulässigen HDInsight-Versionen finden Sie unter [Unterstützte HDInsight-Versionen](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions). Ohne Angabe dieses Werts wird die [neueste HDI-Standardversion](../../hdinsight/hdinsight-component-versioning.md) verwendet. | Nein       |
| linkedServiceName            | Der verknüpfte Azure Storage-Dienst, den der bedarfsgesteuerte Cluster zum Speichern und Verarbeiten von Daten verwendet. Der HDInsight-Cluster wird in der Region erstellt, in der sich auch das Speicherkonto befindet.<p>Derzeit können Sie keinen bedarfsgesteuerten HDInsight-Cluster erstellen, der Azure Data Lake Store als Speicher verwendet. Wenn Sie die Ergebnisdaten der HDInsight-Verarbeitung in Data Lake Store speichern möchten, kopieren Sie die Daten mithilfe der Kopieraktivität aus dem Blobspeicher in Data Lake Store. </p> | Ja      |
| additionalLinkedServiceNames | Gibt zusätzliche Speicherkonten für den verknüpften HDInsight-Dienst an. Data Factory registriert die Speicherkonten für Sie. Diese Speicherkonten müssen sich in der gleichen Region befinden wie der HDInsight-Cluster. Der HDInsight-Cluster wird in der gleichen Region erstellt wie das durch die Eigenschaft **linkedServiceName** angegebene Speicherkonto. | Nein       |
| osType                       | Die Art des Betriebssystems. Zulässige Werte sind **Linux** und **Windows**. Ohne Angabe dieses Werts wird **Linux** verwendet.  <br /><br />Es wird dringend empfohlen, Linux-basierte HDInsight-Cluster zu verwenden. Das Deaktivierungstermin für HDInsight unter Windows ist der 31. Juli 2018. | Nein       |
| hcatalogLinkedServiceName    | Der Name des verknüpften Azure SQL-Diensts, der auf die HCatalog-Datenbank verweist. Der bedarfsgesteuerte HDInsight-Cluster wird mit der SQL-Datenbank als Metastore erstellt. | Nein       |

#### <a name="example-linkedservicenames-json"></a>Beispiel: LinkedServiceNames JSON

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>Erweiterte Eigenschaften
Für eine präzisere Konfiguration des bedarfsgesteuerten HDInsight-Clusters können Sie die folgenden Eigenschaften angeben:

| Eigenschaft               | BESCHREIBUNG                              | Erforderlich |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Gibt die Core-Konfigurationsparameter (core-site.xml) für den HDInsight-Cluster an, der erstellt werden soll. | Nein       |
| hBaseConfiguration     | Gibt die HBase-Konfigurationsparameter (hbase-site.xml) für den HDInsight-Cluster an. | Nein       |
| hdfsConfiguration      | Gibt die HDFS-Konfigurationsparameter (hdfs-site.xml) für den HDInsight-Cluster an. | Nein       |
| hiveConfiguration      | Gibt die Hive-Konfigurationsparameter (hive-site.xml) für den HDInsight-Cluster an. | Nein       |
| mapReduceConfiguration | Gibt die MapReduce-Konfigurationsparameter (mapred-site.xml) für den HDInsight-Cluster an. | Nein       |
| oozieConfiguration     | Gibt die Oozie-Konfigurationsparameter (oozie-site.xml) für den HDInsight-Cluster an. | Nein       |
| stormConfiguration     | Gibt die Storm-Konfigurationsparameter (storm-site.xml) für den HDInsight-Cluster an. | Nein       |
| yarnConfiguration      | Gibt die YARN-Konfigurationsparameter (yarn-site.xml) für den HDInsight-Cluster an. | Nein       |

#### <a name="example-on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>Beispiel: Konfiguration eines bedarfsgesteuerten HDInsight-Clusters mit erweiterten Eigenschaften

```json
{
  "name": " HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "version": "3.6",
      "osType": "Linux",
      "clusterSize": 16,
      "timeToLive": "01:30:00",
      "linkedServiceName": "adfods1",
      "coreConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "hiveConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "mapReduceConfiguration": {
        "mapreduce.reduce.java.opts": "-Xmx4000m",
        "mapreduce.map.java.opts": "-Xmx4000m",
        "mapreduce.map.memory.mb": "5000",
        "mapreduce.reduce.memory.mb": "5000",
        "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
      },
      "yarnConfiguration": {
        "yarn.app.mapreduce.am.resource.mb": "5000",
        "mapreduce.map.memory.mb": "5000"
      },
      "additionalLinkedServiceNames": [
        "datafeeds",
        "adobedatafeed"
      ]
    }
  }
}
```

### <a name="node-sizes"></a>Knotengrößen
Verwenden Sie die folgenden Eigenschaften, um die Größe der Haupt-, Daten- und Zookeeper-Knoten anzugeben: 

| Eigenschaft          | BESCHREIBUNG                              | Erforderlich |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Gibt die Größe des Hauptknotens an. Standardwert: **Standard_D3**. Weitere Informationen finden Sie unter [Angeben von Knotengrößen](#specify-node-sizes). | Nein       |
| dataNodeSize      | Legt die Größe des Datenknotens fest. Standardwert: **Standard_D3**. | Nein       |
| zookeeperNodeSize | Legt die Größe des ZooKeeper-Knotens fest. Standardwert: **Standard_D3**. | Nein       |

#### <a name="specify-node-sizes"></a>Angeben von Knotengrößen
Informationen zu Zeichenfolgenwerten, die Sie für die im vorherigen Abschnitt beschriebenen Eigenschaften angeben müssen, finden Sie unter [Größen für virtuelle Linux-Computer in Azure](../../virtual-machines/sizes.md). Die Werte müssen den Cmdlets und APIs entsprechen, auf die in [Größen für virtuelle Linux-Computer in Azure](../../virtual-machines/sizes.md) verwiesen wird. Die standardmäßige große Datenknotengröße verfügt über 7 GB Arbeitsspeicher. Dies ist für Ihr Szenario unter Umständen nicht ausreichend. 

Wenn Sie Haupt- und Workerknoten mit der Größe „D4“ erstellen möchten, geben Sie für die Eigenschaften **headNodeSize** und **dataNodeSize** jeweils den Wert **Standard_D4** an: 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Sollten Sie für diese Eigenschaften einen falschen Wert festgelegt haben, erscheint unter Umständen die folgende Meldung:

  Der Cluster wurde nicht erstellt. Ausnahme: Vorgang der Clustererstellung kann nicht abgeschlossen werden. Vorgang mit Code ‚400‘ fehlgeschlagen. Cluster hinterließ folgenden Status: 'Error'. Meldung: 'PreClusterCreationValidationFailure'. 
  
Sollte diese Meldung angezeigt werden, vergewissern Sie sich, dass Sie die Cmdlet- und API-Namen aus der Tabelle unter [Größen für virtuelle Linux-Computer in Azure](../../virtual-machines/sizes.md) verwenden.  

> [!NOTE]
> Data Factory unterstützt derzeit keine HDInsight-Cluster, die Data Lake Store als primären Speicher verwenden. Verwenden Sie Azure Storage als primären Speicher für HDInsight-Cluster. 
>
> 


## <a name="bring-your-own-compute-environment"></a>Eigene Compute-Umgebung
Sie können eine bereits vorhandene Compute-Umgebung als verknüpften Dienst in Data Factory registrieren. Die Compute-Umgebung wird von Ihnen verwaltet. Der Data Factory-Dienst verwendet die Compute-Umgebung zum Ausführen von Aktivitäten.

Diese Art von Konfiguration wird für die folgenden Compute-Umgebungen unterstützt:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning Studio (klassisch)
* Azure Data Lake Analytics
* Azure SQL-Datenbank, Azure Synapse Analytics, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Mit Azure HDInsight verknüpfter Dienst
Sie können einen verknüpften HDInsight-Dienst erstellen, um Ihren eigenen HDInsight-Cluster bei Data Factory zu registrieren.

### <a name="example"></a>Beispiel

```json
{
  "name": "HDInsightLinkedService",
  "properties": {
    "type": "HDInsight",
    "typeProperties": {
      "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
      "userName": "admin",
      "password": "<password>",
      "linkedServiceName": "MyHDInsightStoragelinkedService"
    }
  }
}
```

### <a name="properties"></a>Eigenschaften
| Eigenschaft          | BESCHREIBUNG                              | Erforderlich |
| ----------------- | ---------------------------------------- | -------- |
| type              | Legen Sie die Typeigenschaft auf **HDInsight** fest. | Ja      |
| clusterUri        | Der URI des HDInsight-Clusters.        | Ja      |
| username          | Der Name des Benutzerkontos, das zum Herstellen einer Verbindung mit einem vorhandenen HDInsight-Cluster verwendet werden soll. | Ja      |
| password          | Das Kennwort für das Benutzerkonto   | Ja      |
| linkedServiceName | Der Name des verknüpften Speicherdiensts, der auf den von diesem HDInsight-Cluster verwendeten Blobspeicher verweist. <p>Für diese Eigenschaft kann derzeit kein verknüpfter Data Lake Store-Dienst angegeben werden. Wenn der HDInsight-Cluster Zugriff auf Data Lake Store hat, können Sie über Hive-/Pig-Skripts auf Daten in Data Lake Store zugreifen. </p> | Ja      |

## <a name="azure-batch-linked-service"></a>Verknüpfter Azure Batch-Dienst
Sie können einen verknüpften Batch-Dienst erstellen, um einen Batch-Pool mit virtuellen Computern bei einer Data Factory zu registrieren. Benutzerdefinierte Microsoft .NET-Aktivitäten können mit Batch oder HDInsight ausgeführt werden.

Wenn Sie noch nicht mit der Verwendung des Batch-Diensts vertraut sind, haben Sie folgende Möglichkeiten:

* Machen Sie sich mit den [Grundlagen von Azure Batch](../../azure-sql/database/sql-database-paas-overview.md) vertraut.
* Informieren Sie sich über das Cmdlet [New-AzureBatchAccount](/previous-versions/azure/mt125880(v=azure.100)). Dieses Cmdlet dient zum Erstellen eines Batch-Kontos. Sie können das Batch-Konto aber auch über das [Azure-Portal](../../batch/batch-account-create-portal.md) erstellen. Ausführliche Informationen zur Verwendung dieses Cmdlets finden Sie unter [Using Azure PowerShell to Manage Azure Batch Account](/archive/blogs/windowshpc/using-azure-powershell-to-manage-azure-batch-account) (Verwenden von Azure PowerShell zum Verwalten eines Azure Batch-Kontos).
* Informieren Sie sich über das Cmdlet [New-AzureBatchPool](/previous-versions/azure/mt125936(v=azure.100)). Dieses Cmdlet dient zum Erstellen eines Batch-Pools.

### <a name="example"></a>Beispiel

```json
{
  "name": "AzureBatchLinkedService",
  "properties": {
    "type": "AzureBatch",
    "typeProperties": {
      "accountName": "<Azure Batch account name>",
      "accessKey": "<Azure Batch account key>",
      "poolName": "<Azure Batch pool name>",
      "linkedServiceName": "<Specify associated storage linked service reference here>"
    }
  }
}
```

Fügen Sie für die **accountName**-Eigenschaft die Zeichenfolge **.\<region name\>** an den Namen Ihres Batch-Kontos an. Beispiel:

```json
"accountName": "mybatchaccount.eastus"
```

Eine weitere Möglichkeit ist die Angabe des Endpunkts **batchUri**. Beispiel:

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>Eigenschaften
| Eigenschaft          | BESCHREIBUNG                              | Erforderlich |
| ----------------- | ---------------------------------------- | -------- |
| type              | Legen Sie die Typeigenschaft auf **AzureBatch** fest. | Ja      |
| .<Name der Region       | Der Name des Batch-Kontos.         | Ja      |
| accessKey         | Der Zugriffsschlüssel für das Batch-Konto.  | Ja      |
| poolName          | Der Name des VM-Pools.    | Ja      |
| linkedServiceName | Der Name des verknüpften Speicherdiensts, der diesem verknüpften Batch-Dienst zugeordnet ist. Dieser verknüpfte Dienst wird für das Staging von Dateien, die für die Ausführung der Aktivität benötigt werden, sowie für die Speicherung der Aktivitätsausführungsprotokolle verwendet. | Ja      |

## <a name="azure-machine-learning-studio-classic-linked-service"></a>Verknüpfter Azure Machine Learning Studio (Classic)-Dienst
Sie können einen mit Azure Machine Learning Studio (klassisch) verknüpften Dienst erstellen, um einen Batchbewertungsendpunkt von Studio (klassisch) für eine Data Factory zu registrieren.

### <a name="example"></a>Beispiel

```json
{
  "name": "AzureMLLinkedService",
  "properties": {
    "type": "AzureML",
    "typeProperties": {
      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
      "apiKey": "<apikey>"
    }
  }
}
```

### <a name="properties"></a>Eigenschaften
| Eigenschaft   | BESCHREIBUNG                              | Erforderlich |
| ---------- | ---------------------------------------- | -------- |
| type       | Legen Sie die Typeigenschaft auf **AzureML** fest. | Ja      |
| mlEndpoint | Die Batchbewertungs-URL.                   | Ja      |
| apiKey     | Die veröffentlichte API des Arbeitsbereichsmodells.     | Ja      |

## <a name="azure-data-lake-analytics-linked-service"></a>Mit Azure Data Lake Analytics verknüpfter Dienst
Sie können einen verknüpften Data Lake Analytics-Dienst erstellen, um einen Data Lake Analytics-Computedienst mit einer Azure Data Factory zu verknüpfen. Die Data Lake Analytics-U-SQL-Aktivität in der Pipeline verweist auf diesen verknüpften Dienst. 

In der folgenden Tabelle werden die allgemeinen Eigenschaften beschrieben, die in der JSON-Definition verwendet werden:

| Eigenschaft                 | BESCHREIBUNG                              | Erforderlich                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| type                 | Legen Sie die Typeigenschaft auf **AzureDataLakeAnalytics** fest. | Ja                                      |
| .<Name der Region          | Der Name des Data Lake Analytics-Kontos.  | Ja                                      |
| dataLakeAnalyticsUri | Der Data Lake Analytics-URI.           | Nein                                       |
| subscriptionId       | Die Azure-Abonnement-ID.                    | Nein<br /><br />(Ohne Angabe wird das Data Factory-Abonnement verwendet.) |
| resourceGroupName    | Der Azure-Ressourcengruppenname.                | Nein<br /><br /> (Ohne Angabe wird die Data Factory-Ressourcengruppe verwendet.) |

### <a name="authentication-options"></a>Authentifizierungsoptionen
Für Ihren verknüpften Data Lake Analytics-Dienst können Sie zwischen Dienstprinzipalauthentifizierung und Authentifizierung mit Benutzeranmeldeinformationen wählen.

#### <a name="service-principal-authentication-recommended"></a>Dienstprinzipalauthentifizierung (empfohlen)
Wenn Sie die Dienstprinzipalauthentifizierung verwenden möchten, registrieren Sie in Azure Active Directory (Azure AD) eine Anwendungsentität. Gewähren Sie Azure AD anschließend Zugriff auf Data Lake Store. Eine ausführliche Anleitung finden Sie unter [Dienst-zu-Dienst-Authentifizierung](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md). Notieren Sie sich die folgenden Werte, die Sie zum Definieren des verknüpften Diensts verwenden:
* Anwendungs-ID
* Anwendungsschlüssel 
* Mandanten-ID

Verwenden Sie die Dienstprinzipalauthentifizierung, indem Sie die folgenden Eigenschaften angeben:

| Eigenschaft                | BESCHREIBUNG                              | Erforderlich |
| :---------------------- | :--------------------------------------- | :------- |
| servicePrincipalId  | Die Client-ID der Anwendung.     | Ja      |
| servicePrincipalKey | Der Schlüssel der Anwendung.           | Ja      |
| tenant              | Die Mandanteninformationen (Domänenname oder Mandanten-ID) für den Ort Ihrer Anwendung. Zeigen Sie zum Ermitteln dieser Informationen im Azure-Portal mit der Maus auf den Bereich in der rechten oberen Ecke. | Ja      |

**Beispiel: Dienstprinzipalauthentifizierung**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="user-credential-authentication"></a>Authentifizierung mit Benutzeranmeldeinformationen
Geben Sie für die Authentifizierung mit Benutzeranmeldeinformationen für Data Lake Analytics folgende Eigenschaften an:

| Eigenschaft          | BESCHREIBUNG                              | Erforderlich |
| :---------------- | :--------------------------------------- | :------- |
| authorization | Klicken Sie im Data Factory-Editor auf die Schaltfläche **Autorisieren**. Geben Sie die Anmeldeinformationen ein, die die automatisch generierte URL dieser Eigenschaft zuweist. | Ja      |
| sessionID     | Die OAuth-Sitzungs-ID aus der OAuth-Autorisierungssitzung. Jede Sitzungs-ID ist eindeutig und darf nur einmal verwendet werden. Diese Einstellung wird automatisch generiert, wenn Sie den Data Factory-Editor verwenden. | Ja      |

**Beispiel: Authentifizierung mit Benutzeranmeldeinformationen**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>Tokenablauf
Der Autorisierungscode, den Sie mit der Schaltfläche **Autorisieren** generiert haben, läuft nach einer bestimmten Zeit ab. 

Wenn das Authentifizierungstoken abläuft, wird möglicherweise folgende Fehlermeldung angezeigt: 

  Fehler beim Anmeldevorgang: invalid_grant – AADSTS70002: Fehler beim Überprüfen der Anmeldeinformationen. AADSTS70008: Die angegebene Zugriffserteilung ist abgelaufen oder wurde widerrufen. Ablaufverfolgungs-ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Korrelations-ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Zeitstempel: 2015-12-15 21:09:31Z

Die folgende Tabelle enthält Ablaufzeiten nach Benutzerkontotyp: 

| Benutzertyp                                | Läuft ab nach                            |
| :--------------------------------------- | :--------------------------------------- |
| Benutzerkonten, die *nicht* von Azure AD verwaltet werden (Hotmail, Live usw.) | 12 Stunden                                 |
| Benutzerkonten, die *von* Azure AD verwaltet werden | 14 Tage nach der letzten Sliceausführung. <br /><br />90 Tage, wenn ein Slice, das auf einem verknüpften OAuth-Dienst basiert, mindestens einmal alle 14 Tage ausgeführt wird |

Um diesen Fehler zu vermeiden oder zu beheben, führen Sie durch Klicken auf die Schaltfläche **Autorisieren** eine erneute Autorisierung durch, wenn das Token abläuft. Stellen Sie den verknüpften Dienst anschließend erneut bereit. Sie können Werte für die Eigenschaften **sessionId** und **authorization** auch programmgesteuert generieren. Verwenden Sie hierzu den folgenden Code:

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

Weitere Informationen zu den Data Factory-Klassen aus diesem Codebeispiel finden Sie hier:
* [AzureDataLakeStoreLinkedService-Klasse](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice)
* [AzureDataLakeAnalyticsLinkedService-Klasse](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)
* [AuthorizationSessionGetResponse-Klasse](/dotnet/api/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse)

Fügen Sie für die Klasse **WindowsFormsWebAuthenticationDialog** einen Verweis auf „Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll“ hinzu. 

## <a name="azure-sql-linked-service"></a>Verknüpfter Azure SQL-Dienst
Sie können einen verknüpften SQL-Dienst erstellen und mit der [Aktivität „Gespeicherte Prozedur“](data-factory-stored-proc-activity.md) verwenden, um eine gespeicherte Prozedur über eine Data Factory-Pipeline aufzurufen. Weitere Informationen finden Sie unter [Azure SQL-Connector](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="azure-synapse-analytics-linked-service"></a>Mit Azure Synapse Analytics verknüpfter Dienst
Sie können einen mit Azure Synapse Analytics verknüpften Dienst erstellen und mit der [Aktivität „Gespeicherte Prozedur“](data-factory-stored-proc-activity.md) verwenden, um eine gespeicherte Prozedur in einer Data Factory-Pipeline aufzurufen. Weitere Informationen finden Sie unter [Azure Synapse Analytics-Connector](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties).

## <a name="sql-server-linked-service"></a>Mit SQL Server verknüpfter Dienst
Sie können einen verknüpften SQL Server-Dienst erstellen und mit der [Aktivität „Gespeicherte Prozedur“](data-factory-stored-proc-activity.md) verwenden, um eine gespeicherte Prozedur über eine Data Factory-Pipeline aufzurufen. Weitere Informationen finden Sie unter [SQL Server-Connector](data-factory-sqlserver-connector.md#linked-service-properties).