---
title: Von Azure Data Factory unterstützte Compute-Umgebungen
description: Compute-Umgebungen, die in Azure Data Factory-Pipelines (wie z. B. Azure HDInsight) für die Transformation oder Verarbeitung von Daten verwendet werden können.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.date: 05/08/2019
ms.openlocfilehash: a454c1297b0f25c64b11217811999d4331148205
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "96022461"
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Von Azure Data Factory unterstützte Compute-Umgebungen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel werden verschiedene Compute-Umgebungen beschrieben, mit denen Sie Daten verarbeiten oder transformieren können. Darüber hinaus werden Einzelheiten zu verschiedenen Konfigurationen beschrieben (bedarfsgesteuerte Compute-Umgebung im Vergleich zu einer eigenen Compute-Umgebung). Diese beiden Konfigurationen werden von Data Factory unterstützt, wenn Sie verknüpfte Dienste konfigurieren, um diese Compute-Umgebungen mit Azure Data Factory zu verknüpfen.

Die folgende Tabelle enthält eine Liste von Compute-Umgebungen, die von Data Factory unterstützt werden, und die Aktivitäten, die darin ausgeführt werden können. 

| Compute-Umgebung                                          | activities                                                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Bedarfsgesteuerter HDInsight-Cluster](#azure-hdinsight-on-demand-linked-service) oder [Eigener HDInsight-Cluster](#azure-hdinsight-linked-service) | [Hive](transform-data-using-hadoop-hive.md), [Pig](transform-data-using-hadoop-pig.md), [Spark](transform-data-using-spark.md), [MapReduce](transform-data-using-hadoop-map-reduce.md), [Hadoop Streaming](transform-data-using-hadoop-streaming.md) |
| [Azure Batch](#azure-batch-linked-service)                   | [Benutzerdefiniert](transform-data-using-dotnet-custom-activity.md)     |
| [Azure Machine Learning Studio (klassisch)](#azure-machine-learning-studio-classic-linked-service) | [Aktivitäten von Machine Learning Studio (Classic): Batchausführung und Ressourcenaktualisierung](transform-data-using-machine-learning.md) |
| [Azure Machine Learning](#azure-machine-learning-linked-service) | [Azure Machine Learning-Pipelineausführung](transform-data-machine-learning-service.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md) |
| [Azure SQL](#azure-sql-database-linked-service), [Azure Synapse Analytics (ehemals SQL Data Warehouse)](#azure-synapse-analytics-linked-service), [SQL Server](#sql-server-linked-service) | [Gespeicherte Prozedur](transform-data-using-stored-procedure.md) |
| [Azure Databricks](#azure-databricks-linked-service)         | [Notebook](transform-data-databricks-notebook.md), [Jar](transform-data-databricks-jar.md), [Python](transform-data-databricks-python.md) |
| [Azure-Funktion](#azure-function-linked-service)         | [Aktivität „Azure Function“](control-flow-azure-function-activity.md)
>  

## <a name="hdinsight-compute-environment"></a>HDInsight-Compute-Umgebung

In der folgenden Tabelle finden Sie ausführliche Informationen zu den unterstützten, mit Storage verknüpften Diensttypen für die Konfiguration in bedarfsgesteuerter und eigener Compute-Umgebung.

| In Compute verknüpfter Dienst | Eigenschaftenname                | BESCHREIBUNG                                                  | Blob | ADLS Gen2 | Azure SQL-Datenbank | ADLS Gen 1 |
| ------------------------- | ---------------------------- | ------------------------------------------------------------ | ---- | --------- | ------------ | ---------- |
| Bei Bedarf                 | linkedServiceName            | Der verknüpfte Azure Storage-Dienst, den der bedarfsgesteuerte Cluster zum Speichern und Verarbeiten von Daten nutzen soll. | Ja  | Ja       | Nein           | Nein         |
|                           | additionalLinkedServiceNames | Gibt zusätzliche Speicherkonten für den verknüpften HDInsight-Dienst an, damit der Data Factory-Dienst diese für Sie registrieren kann. | Ja  | Nein        | Nein           | Nein         |
|                           | hcatalogLinkedServiceName    | Der Name des mit Azure SQL verknüpften Diensts, der auf die HCatalog-Datenbank verweist. Der bedarfsgesteuerte HDInsight-Cluster wird mithilfe der Azure SQL-Datenbank als Metastore erstellt. | Nein   | Nein        | Ja          | Nein         |
| BYOC                      | linkedServiceName            | Der Verweis auf den mit Azure Storage verknüpften Dienst.                | Ja  | Ja       | Nein           | Nein         |
|                           | additionalLinkedServiceNames | Gibt zusätzliche Speicherkonten für den verknüpften HDInsight-Dienst an, damit der Data Factory-Dienst diese für Sie registrieren kann. | Nein   | Nein        | Nein           | Nein         |
|                           | hcatalogLinkedServiceName    | Ein Verweis auf den verknüpften Azure SQL-Dienst, der wiederum auf die HCatalog-Datenbank verweist. | Nein   | Nein        | Nein           | Nein         |

### <a name="azure-hdinsight-on-demand-linked-service"></a>Bedarfsgesteuerter verknüpfter Azure HDInsight-Dienst

Bei dieser Konfiguration wird die Compute-Umgebung vollständig vom Azure Data Factory-Dienst verwaltet. Der Data Factory-Dienst erstellt diese Umgebung automatisch, bevor ein Auftrag zur Verarbeitung von Daten übermittelt wird. Sobald der Auftrag abgeschlossen wurde, wird die Umgebung entfernt. Sie können einen verknüpften Dienst für die bedarfsgesteuerte Compute-Umgebung erstellen, diesen konfigurieren und differenzierte Einstellungen für Auftragsausführung, Clusterverwaltung und Bootstrappingaktionen festlegen.

> [!NOTE]
> Die bedarfsgesteuerte Konfiguration wird gegenwärtig nur für Azure HDInsight-Cluster unterstützt. Azure Databricks unterstützt auch bedarfsgesteuerte Aufträge mithilfe von Auftragsclustern. Weitere Informationen finden Sie unter [Mit Azure Databricks verknüpfter Dienst](#azure-databricks-linked-service).

Der Azure Data Factory-Dienst kann zum Verarbeiten von Daten automatisch einen bedarfsgesteuerten HDInsight-Cluster erstellen. Der Cluster wird in derselben Region erstellt wie das Speicherkonto (Eigenschaft „linkedServiceName“ in JSON), das dem Cluster zugeordnet ist. Das Speicherkonto `must` (muss) ein allgemeines Azure Storage-Standardkonto sein. 

Beachten Sie die folgenden **wichtigen** Hinweise zum bedarfsgesteuerten verknüpften HDInsight-Dienst:

* Der bedarfsgesteuerte HDInsight-Cluster wird in Ihrem Azure-Abonnement erstellt. Der Cluster wird in Ihrem Azure-Portal angezeigt, wenn der Cluster ausgeführt wird. 
* Die Protokolle für Aufträge, die in einem bedarfsgesteuerten HDInsight-Cluster ausgeführt werden, werden in das mit dem HDInsight-Cluster verknüpfte Speicherkonto kopiert. Die in Ihrer Definition des verknüpften Diensts definierten Elemente clusterUserName, clusterPassword, clusterSshUserName und clusterSshPassword werden während des Lebenszyklus des Clusters zur Anmeldung bei dem Cluster für die eingehende Problembehandlung verwendet. 
* Ihnen wird nur die Zeit in Rechnung gestellt, in der der HDInsight-Cluster verfügbar ist und Aufträge ausführt.
* Sie können mit dem bedarfsgesteuerten verknüpften Azure HDInsight-Dienst eine **Skriptaktion** verwenden.  

> [!IMPORTANT]
> Die bedarfsgesteuerte Bereitstellung eines Azure HDInsight-Clusters dauert üblicherweise **20 Minuten** oder länger.

#### <a name="example"></a>Beispiel

Die folgende JSON definiert einen bedarfsgesteuerten Linux-basierten mit HDInsight verknüpften Dienst. Der Data Factory-Dienst erstellt automatisch einen **Linux-basierten** HDInsight-Cluster zur Verarbeitung der angeforderten Aktivität. 

```json
{
  "name": "HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "clusterType": "hadoop",
      "clusterSize": 1,
      "timeToLive": "00:15:00",
      "hostSubscriptionId": "<subscription ID>",
      "servicePrincipalId": "<service principal ID>",
      "servicePrincipalKey": {
        "value": "<service principal key>",
        "type": "SecureString"
      },
      "tenant": "<tenent id>",
      "clusterResourceGroup": "<resource group name>",
      "version": "3.6",
      "osType": "Linux",
      "linkedServiceName": {
        "referenceName": "AzureStorageLinkedService",
        "type": "LinkedServiceReference"
      }
    },
    "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
  }
}
```

> [!IMPORTANT]
> Der HDInsight-Cluster erstellt einen **Standardcontainer** im Blobspeicher, den Sie im JSON-Code angegeben haben (**linkedServiceName**). HDInsight löscht diesen Container nicht, wenn der Cluster gelöscht wird. Dieses Verhalten ist beabsichtigt. Durch den bedarfsgesteuerten, mit HDInsight verknüpften Dienst wird jedes Mal ein HDInsight-Cluster erstellt, wenn ein Slice verarbeitet werden muss, es sei denn, ein aktiver Cluster (**timeToLive**) ist vorhanden und wird gelöscht, nachdem die Verarbeitung abgeschlossen ist. 
>
> Wenn weitere Aktivitäten ausgeführt werden, werden in Azure Blob Storage viele Container angezeigt. Falls Sie diese für die Problembehandlung der Aufträge nicht benötigen, sollten Sie sie ggf. löschen, um die Speicherkosten zu verringern. Die Namen dieser Container folgen einem Muster: `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp`. Verwenden Sie Tools wie den [Microsoft Azure Storage-Explorer](https://storageexplorer.com/), um Container in Ihrem Azure-Blobspeicher zu löschen.

#### <a name="properties"></a>Eigenschaften

| Eigenschaft                     | Beschreibung                              | Erforderlich |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | Legen Sie die Typeigenschaft auf **HDInsightOnDemand** fest. | Ja      |
| clusterSize                  | Anzahl der Worker-/Datenknoten im Cluster. Der HDInsight-Cluster wird mit zwei Hauptknoten sowie der Anzahl der Workerknoten erstellt, die Sie für diese Eigenschaft angeben. Die Knoten haben die Größe Standard_D3, die vier Kerne aufweist. Ein Cluster mit vier Workerknoten nutzt also 24 Kerne (4 \* 4 = 16 für die Workerknoten + 2 \* 4 = 8 für die Hauptknoten). Nähere Informationen finden Sie unter [Einrichten von Clustern in HDInsight mit Hadoop, Spark, Kafka usw.](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) | Ja      |
| linkedServiceName            | Der verknüpfte Azure Storage-Dienst, den der bedarfsgesteuerte Cluster zum Speichern und Verarbeiten von Daten nutzt. Der HDInsight-Cluster wird in der gleichen Region wie das Azure Storage-Konto erstellt. Die Gesamtanzahl von Kernen, die Sie in jeder von Azure HDInsight unterstützten Azure-Region verwenden können, ist begrenzt. Stellen Sie sicher, dass Sie über genügend Kernekontingente in dieser Azure-Region verfügen, um die Anforderungen von clusterSize zu erfüllen. Nähere Informationen finden Sie unter [Einrichten von Clustern in HDInsight mit Hadoop, Spark, Kafka usw.](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)<p>Derzeit können Sie keinen bedarfsgesteuerten HDInsight-Cluster erstellen, der einen Azure Data Lake Storage (Gen 2) als Speicher verwendet. Wenn Sie die Ergebnisdaten der HDInsight-Verarbeitung in einem Azure Data Lake Storage (Gen 2) speichern möchten, kopieren Sie die Daten mittels einer Kopieraktivität aus dem Azure Blob Storage in den Azure Data Lake Storage (Gen 2). </p> | Ja      |
| clusterResourceGroup         | Der HDInsight-Cluster wird in dieser Ressourcengruppe erstellt. | Ja      |
| timetolive                   | Die zulässige Leerlaufzeit für den bedarfsgesteuerten HDInsight-Cluster. Gibt an, wie lange der bedarfsgesteuerte HDInsight-Cluster nach dem Abschluss einer Aktivitätsausführung aktiv bleibt, wenn keine anderen aktiven Aufträge im Cluster vorhanden sind. Der minimal zulässige Wert beträgt 5 Minuten (00:05:00).<br/><br/>Beispiel: Wenn eine Aktivitätsausführung 6 Minuten dauert und „timetolive“ auf 5 Minuten festgelegt ist, bleibt der Cluster für 5 Minuten nach den 6 Minuten für die Verarbeitung der Aktivitätsausführung aktiv. Wenn eine weitere Aktivitätsausführung mit einem Zeitfenster von 6 Minuten ausgeführt wird, wird sie von demselben Cluster verarbeitet.<br/><br/>Das Erstellen eines bedarfsgesteuerten HDInsight-Clusters ist ein aufwändiger Vorgang (er kann eine Weile dauern). Verwenden Sie daher diese Einstellung bei Bedarf, um die Leistung einer Data Factory zu verbessern, indem Sie einen bedarfsgesteuerten HDInsight-Cluster wiederverwenden.<br/><br/>Wenn der timetolive-Wert auf 0 festgelegt wird, wird der Cluster gelöscht, sobald die Aktivitätsausführung abgeschlossen ist. Wenn Sie hingegen einen hohen Wert festlegen, könnte der Cluster im Leerlauf bleiben, damit Sie sich zur Problembehandlung anmelden können, aber dies könnte hohe Kosten verursachen. Aus diesem Grund ist es wichtig, dass Sie den entsprechenden Wert basierend auf Ihren Anforderungen festlegen.<br/><br/>Wenn der Wert der Eigenschaft „timetolive“ ordnungsgemäß festgelegt wird, können mehrere Pipelines die Instanz des bedarfsgesteuerten HDInsight-Clusters verwenden. | Ja      |
| clusterType                  | Der Typ des zu erstellenden HDInsight-Clusters. Zulässige Werte sind „hadoop“ und „spark“. Wenn Sie hier nichts angeben, lautet der Standardwert „hadoop“. Ein für das Enterprise-Sicherheitspaket aktivierter Cluster kann nicht bedarfsweise erstellt werden. Verwenden Sie stattdessen einen [vorhandenen Cluster/eine eigene Compute-Umgebung](#azure-hdinsight-linked-service). | Nein       |
| version                      | Version des HDInsight-Clusters. Wenn nichts angegeben wird, ist dies die aktuelle definierte HDInsight-Standardversion. | Nein       |
| hostSubscriptionId           | Die Azure-Abonnement-ID, die zum Erstellen des HDInsight-Clusters verwendet wird. Wenn nicht angegeben, wird die Abonnement-ID Ihres Azure-Anmeldungskontexts verwendet. | Nein       |
| clusterNamePrefix           | Das Präfix des HDI-Clusternamens, ein Zeitstempel, wird am Ende des Clusternamens automatisch angefügt.| Nein       |
| sparkVersion                 | Die Spark-Version, wenn der Clustertyp „Spark“ ist. | Nein       |
| additionalLinkedServiceNames | Gibt zusätzliche Speicherkonten für den verknüpften HDInsight-Dienst an, damit der Data Factory-Dienst diese für Sie registrieren kann. Diese Speicherkonten müssen sich in der gleichen Region befinden wie der HDInsight-Cluster, der in der gleichen Region erstellt wird wie das von „linkedServiceName“ angegebene Speicherkonto. | Nein       |
| osType                       | Typ des Betriebssystems. Zulässige Werte sind: Linux und Windows (nur für HDInsight 3.3). Der Standardwert ist „Linux“. | Nein       |
| hcatalogLinkedServiceName    | Der Name des mit Azure SQL verknüpften Diensts, der auf die HCatalog-Datenbank verweist. Der bedarfsgesteuerte HDInsight-Cluster wird mit der Azure SQL-Datenbank als Metastore erstellt. | Nein       |
| connectVia                   | Die Integration Runtime, mit der die Aktivitäten diesem verknüpften HDInsight-Dienst zugeteilt werden. Für einen bedarfsgesteuerten verknüpften HDInsight-Dienst wird nur Azure Integration Runtime unterstützt. Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. | Nein       |
| clusterUserName                   | Der Benutzername zum Zugriff auf den Cluster. | Nein       |
| clusterPassword                   | Das Kennwort als sichere Zeichenfolge zum Zugriff auf den Cluster. | Nein       |
| clusterSshUserName         | Der Benutzername zum Herstellen einer SSH-Remoteverbindung mit dem Knoten des Clusters (für Linux). | Nein       |
| clusterSshPassword         | Das Kennwort als sichere Zeichenfolge zum Herstellen einer SSH-Remoteverbindung mit dem Knoten des Clusters (für Linux). | Nein       |
| scriptActions | Geben Sie beim Erstellen bedarfsgesteuerter Cluster Skripts für [HDInsight-Clusteranpassungen](../hdinsight/hdinsight-hadoop-customize-cluster-linux.md) an. <br />Derzeit unterstützt das Erstellungstool für die Benutzeroberfläche der Azure Data Factory die Angabe von nur einer Skriptaktion. Sie können diese Einschränkung jedoch durch die Angabe mehrerer Skriptaktionen im JSON-Code beseitigen. | Nein |


> [!IMPORTANT]
> HDInsight unterstützt mehrere Hadoop-Clusterversionen, die bereitgestellt werden können. Jede ausgewählte Version erstellt eine bestimmte Version der HDP-Distribution (Hortonworks Data Platform) und eine Reihe von Komponenten innerhalb dieser Distribution. Die Liste der unterstützten Versionen von HDInsight wird ständig aktualisiert, um die aktuellsten Komponenten und Fixes für das Hadoop-Ökosystem bereitzustellen. Nutzen Sie unbedingt stets die aktuellsten Informationen über [Unterstützte HDInsight-Versionen](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions), um sicherzustellen, dass Sie die unterstützte Version von HDInsight verwenden. 
>
> [!IMPORTANT]
> Derzeit werden HBase-, Interactive Query- (Hive LLAP) und Storm-Cluster von verknüpften HDInsight-Diensten nicht unterstützt. 

* additionalLinkedServiceNames (JSON-Beispiel)

```json
"additionalLinkedServiceNames": [{
    "referenceName": "MyStorageLinkedService2",
    "type": "LinkedServiceReference"          
}]
```

#### <a name="service-principal-authentication"></a>Dienstprinzipalauthentifizierung

Der verknüpfte, bedarfsgesteuerte HDInsight-Dienst erfordert eine Dienstprinzipalauthentifizierung, um HDInsight-Cluster in Ihrem Namen zu erstellen. Um die Dienstprinzipalauthentifizierung zu verwenden, registrieren Sie eine Anwendungsentität in Azure Active Directory (Azure AD), und teilen Sie ihr die Rolle **Mitwirkender** des Abonnements oder der Ressourcengruppe zu, wo der HDInsight-Cluster erstellt wird. Nähere Informationen finden Sie unter [Erstellen einer Azure Active Directory-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff mithilfe des Portals](../active-directory/develop/howto-create-service-principal-portal.md). Notieren Sie sich die folgenden Werte, die Sie zum Definieren des verknüpften Diensts verwenden:

- Anwendungs-ID
- Anwendungsschlüssel 
- Mandanten-ID

Verwenden Sie die Dienstprinzipalauthentifizierung, indem Sie die folgenden Eigenschaften angeben:

| Eigenschaft                | Beschreibung                              | Erforderlich |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Geben Sie die Client-ID der Anwendung an.     | Ja      |
| **servicePrincipalKey** | Geben Sie den Schlüssel der Anwendung an.           | Ja      |
| **tenant**              | Geben Sie die Mandanteninformationen (Domänenname oder Mandanten-ID) für Ihre Anwendung an. Diese können Sie abrufen, indem Sie im Azure-Portal mit der Maus auf den Bereich oben rechts zeigen. | Ja      |

#### <a name="advanced-properties"></a>Erweiterte Eigenschaften

Für eine präzisere Konfiguration des bedarfsgesteuerten HDInsight-Clusters können Sie die folgenden Eigenschaften festlegen.

| Eigenschaft               | Beschreibung                              | Erforderlich |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | Gibt die wichtigsten Konfigurationsparameter (wie in "core-site.xml") für den HDInsight-Cluster an, der erstellt werden soll. | Nein       |
| hBaseConfiguration     | Gibt die HBase-Konfigurationsparameter (hbase-site.xml) für den HDInsight-Cluster an. | Nein       |
| hdfsConfiguration      | Gibt die HDFS-Konfigurationsparameter (hdfs-site.xml) für den HDInsight-Cluster an. | Nein       |
| hiveConfiguration      | Gibt die Hive-Konfigurationsparameter (hive-site.xml) für den HDInsight-Cluster an. | Nein       |
| mapReduceConfiguration | Gibt die MapReduce-Konfigurationsparameter (mapred-site.xml) für den HDInsight-Cluster an. | Nein       |
| oozieConfiguration     | Gibt die Oozie-Konfigurationsparameter (oozie-site.xml) für den HDInsight-Cluster an. | Nein       |
| stormConfiguration     | Gibt die Storm-Konfigurationsparameter (storm-site.xml) für den HDInsight-Cluster an. | Nein       |
| yarnConfiguration      | Gibt die Yarn-Konfigurationsparameter (yarn-site.xml) für den HDInsight-Cluster an. | Nein       |

* Beispiel: Konfiguration eines bedarfsgesteuerten HDInsight-Clusters mit erweiterten Eigenschaften

```json
{
    "name": " HDInsightOnDemandLinkedService",
    "properties": {
      "type": "HDInsightOnDemand",
      "typeProperties": {
          "clusterSize": 16,
          "timeToLive": "01:30:00",
          "hostSubscriptionId": "<subscription ID>",
          "servicePrincipalId": "<service principal ID>",
          "servicePrincipalKey": {
            "value": "<service principal key>",
            "type": "SecureString"
          },
          "tenant": "<tenent id>",
          "clusterResourceGroup": "<resource group name>",
          "version": "3.6",
          "osType": "Linux",
          "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
            },
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
            "additionalLinkedServiceNames": [{
                "referenceName": "MyStorageLinkedService2",
                "type": "LinkedServiceReference"          
            }]
        }
    },
      "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
}
```

#### <a name="node-sizes"></a>Knotengrößen
Sie können die Größe der Head-, Daten- und Zookeeper-Knoten mit den folgenden Eigenschaften angeben: 

| Eigenschaft          | Beschreibung                              | Erforderlich |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | Gibt die Größe des Hauptknotens an. Der Standardwert lautet: Standard_D3. Weitere Details finden Sie im Abschnitt **Knotengrößen angeben**. | Nein       |
| dataNodeSize      | Gibt die Größe des Datenknotens an. Der Standardwert lautet: Standard_D3. | Nein       |
| zookeeperNodeSize | Gibt die Größe des Zoo Keeper-Knotens an. Der Standardwert lautet: Standard_D3. | Nein       |

* Für die Angabe von Knotengrößen lesen Sie den Artikel [Größen von virtuellen Computern](../virtual-machines/sizes.md), um Näheres zu Zeichenfolgenwerten zu erfahren, die Sie für die im vorherigen Abschnitt erwähnten Eigenschaften angeben müssen. Die Werte müssen den **CMDLETs und APIs** entsprechen, auf die im Artikel verwiesen wird. Wie Sie in diesem Artikel sehen können, hat der Datenknoten „Large“ (Standard) 7 GB Arbeitsspeicher, was für Ihr Szenario möglicherweise nicht ausreichend ist. 

Wenn Sie Hauptknoten und Workerknoten der Größe D4 erstellen möchten, geben Sie **Standard_D4** als Wert für die Eigenschaften „headNodeSize“ und „dataNodeSize“ an. 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

Wenn Sie einen falschen Wert für diese Eigenschaften angeben, erhalten Sie möglicherweise den folgenden **Fehler**: Der Cluster wurde nicht erstellt. Ausnahme: Vorgang der Clustererstellung kann nicht abgeschlossen werden. Vorgang mit Code ‚400‘ fehlgeschlagen. Cluster hinterließ folgenden Status: 'Error'. Meldung: 'PreClusterCreationValidationFailure'. Wenn Sie diesen Fehler erhalten, achten Sie darauf, dass Sie den Namen der **Cmdlets und APIs** aus der Tabelle im Artikel [Größen für virtuelle Computer](../virtual-machines/sizes.md) verwenden.          

### <a name="bring-your-own-compute-environment"></a>Eigene Compute-Umgebung
Bei dieser Konfiguration können Benutzer eine bereits vorhandene Compute-Umgebung als verknüpften Dienst in Data Factory registrieren. Die Compute-Umgebung wird vom Benutzer verwaltet und von Data Factory zum Ausführen von Aktivitäten verwendet.

Diese Art von Konfiguration wird für die folgenden Compute-Umgebungen unterstützt:

* Azure HDInsight
* Azure Batch
* Azure Machine Learning
* Azure Data Lake Analytics
* Azure SQL DB, Azure Synapse Analytics, SQL Server

## <a name="azure-hdinsight-linked-service"></a>Mit Azure HDInsight verknüpfter Dienst
Sie können einen verknüpften Azure HDInsight-Dienst erstellen, um Ihren eigenen HDInsight-Cluster für Data  Factory zu registrieren.

### <a name="example"></a>Beispiel

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
      "type": "HDInsight",
      "typeProperties": {
        "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
        "userName": "username",
        "password": {
            "value": "passwordvalue",
            "type": "SecureString"
          },
        "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```

### <a name="properties"></a>Eigenschaften
| Eigenschaft          | Beschreibung                                                  | Erforderlich |
| ----------------- | ------------------------------------------------------------ | -------- |
| type              | Legen Sie die Typeigenschaft auf **HDInsight** fest.            | Ja      |
| clusterUri        | Der URI des HDInsight-Clusters.                            | Ja      |
| username          | Geben Sie den Namen des Benutzers ein, der mit einem vorhandenen HDInsight-Cluster verbunden werden soll. | Ja      |
| password          | Geben Sie ein Kennwort für das Benutzerkonto an.                       | Ja      |
| linkedServiceName | Der Name des verknüpften Azure Storage-Diensts für die von diesem HDInsight-Cluster verwendete Azure Blob Storage-Instanz. <p>Derzeit können Sie keinen verknüpften Azure Data Lake Storage (Gen 2)-Dienst für diese Eigenschaft angeben. Wenn der HDInsight-Cluster Zugriff auf den Data Lake Store hat, können Sie auf Daten im Azure Data Lake Storage (Gen 2) über Hive-/Pig-Skripts zugreifen. </p> | Ja      |
| isEspEnabled      | Geben Sie *TRUE* an, wenn der HDInsight-Cluster für das [Enterprise-Sicherheitspaket](../hdinsight/domain-joined/apache-domain-joined-architecture.md) aktiviert ist. Die Standardeinstellung lautet *FALSE*. | Nein       |
| connectVia        | Die Integration Runtime, mit der die Aktivitäten diesem verknüpften Dienst zugeteilt werden. Sie können Azure Integration Runtime oder selbstgehostete Integration Runtime verwenden. Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. <br />Verwenden Sie für einen für das Enterprise-Sicherheitspaket (ESP) aktivierten HDInsight-Cluster eine selbstgehostete Integration Runtime, die auf den Cluster zugreifen kann oder in demselben virtuellen Netzwerk wie der ESP-HDInsight-Cluster bereitgestellt werden muss. | Nein       |

> [!IMPORTANT]
> HDInsight unterstützt mehrere Hadoop-Clusterversionen, die bereitgestellt werden können. Jede ausgewählte Version erstellt eine bestimmte Version der HDP-Distribution (Hortonworks Data Platform) und eine Reihe von Komponenten innerhalb dieser Distribution. Die Liste der unterstützten Versionen von HDInsight wird ständig aktualisiert, um die aktuellsten Komponenten und Fixes für das Hadoop-Ökosystem bereitzustellen. Nutzen Sie unbedingt stets die aktuellsten Informationen über [Unterstützte HDInsight-Versionen](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions), um sicherzustellen, dass Sie die unterstützte Version von HDInsight verwenden. 
>
> [!IMPORTANT]
> Derzeit werden HBase-, Interactive Query- (Hive LLAP) und Storm-Cluster von verknüpften HDInsight-Diensten nicht unterstützt. 
>
> 

## <a name="azure-batch-linked-service"></a>Verknüpfter Azure Batch-Dienst

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Sie können einen verknüpften Azure Batch-Dienst erstellen, um einen Batch-Pool mit virtuellen Computern für Data Factory zu registrieren. Sie können benutzerdefinierte Aktivität mithilfe von Azure Batch ausführen.

Lesen Sie die folgenden Artikel, wenn Sie noch nicht mit dem Azure Batch-Dienst vertraut sind:

* [Azure Batch – Grundlagen](../batch/batch-technical-overview.md) finden Sie eine Übersicht über den Azure Batch-Dienst.
* Erstellen Sie ein Azure Batch-Konto mit dem Cmdlet [New-AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount), oder erstellen Sie das Azure Batch-Konto über das [Azure-Portal](../batch/batch-account-create-portal.md). Im Artikel [Verwenden von PowerShell zum Verwalten eines Azure Batch-Kontos](/archive/blogs/windowshpc/using-azure-powershell-to-manage-azure-batch-account) ist die Verwendung dieses Cmdlets im Detail beschrieben.
* [New-AzBatchPool](/powershell/module/az.batch/New-AzBatchPool), um einen Azure Batch-Pool zu erstellen.

### <a name="example"></a>Beispiel

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
      "type": "AzureBatch",
      "typeProperties": {
        "accountName": "batchaccount",
        "accessKey": {
          "type": "SecureString",
          "value": "access key"
        },
        "batchUri": "https://batchaccount.region.batch.azure.com",
        "poolName": "poolname",
        "linkedServiceName": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```


### <a name="properties"></a>Eigenschaften
| Eigenschaft          | Beschreibung                              | Erforderlich |
| ----------------- | ---------------------------------------- | -------- |
| type              | Legen Sie die Typeigenschaft auf **AzureBatch** fest. | Ja      |
| .<Name der Region       | Der Name des Azure Batch-Kontos.         | Ja      |
| accessKey         | Der Zugriffsschlüssel für das Azure Batch-Konto.  | Ja      |
| batchUri          | URL zu Ihrem Azure Batch-Konto im Format „https://*batchaccountname.region*. batch.azure.com“. | Ja      |
| poolName          | Der Name des Pools mit virtuellen Computern.    | Ja      |
| linkedServiceName | Der Name des verknüpften Azure Storage-Diensts, der diesem verknüpften Azure Batch-Dienst zugeordnet ist. Dieser verknüpfte Dienst wird für Stagingdateien verwendet, die für die Ausführung der Aktivität benötigt werden. | Ja      |
| connectVia        | Die Integration Runtime, mit der die Aktivitäten diesem verknüpften Dienst zugeteilt werden. Sie können Azure Integration Runtime oder selbstgehostete Integration Runtime verwenden. Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. | Nein       |

## <a name="azure-machine-learning-studio-classic-linked-service"></a>Verknüpfter Azure Machine Learning Studio (Classic)-Dienst
Sie können einen mit Azure Machine Learning Studio (Classic) verknüpften Dienst erstellen, um einen Batchbewertungsendpunkt von Machine Learning Studio (Classic) für Data Factory zu registrieren.

### <a name="example"></a>Beispiel

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
      "type": "AzureML",
      "typeProperties": {
        "mlEndpoint": "https://[batch scoring endpoint]/jobs",
        "apiKey": {
            "type": "SecureString",
            "value": "access key"
        }
     },
     "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
}
```

### <a name="properties"></a>Eigenschaften
| Eigenschaft               | Beschreibung                              | Erforderlich                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| type                   | Legen Sie die type-Eigenschaft auf **AzureML** fest. | Ja                                      |
| mlEndpoint             | Die Batchbewertungs-URL.                   | Ja                                      |
| apiKey                 | Die veröffentlichte API des Arbeitsbereichsmodells.     | Ja                                      |
| updateResourceEndpoint | Die Ressourcenupdate-URL für einen Webdienst-Endpunkt von Azure Machine Learning Studio (Classic), mit der der Vorhersagewebdienst mit der Datei des trainierten Modells aktualisiert wird. | Nein                                       |
| servicePrincipalId     | Geben Sie die Client-ID der Anwendung an.     | Erforderlich, wenn updateResourceEndpoint angegeben wird |
| servicePrincipalKey    | Geben Sie den Schlüssel der Anwendung an.           | Erforderlich, wenn updateResourceEndpoint angegeben wird |
| tenant                 | Geben Sie die Mandanteninformationen (Domänenname oder Mandanten-ID) für Ihre Anwendung an. Diese können Sie abrufen, indem Sie im Azure-Portal mit der Maus auf den Bereich oben rechts zeigen. | Erforderlich, wenn updateResourceEndpoint angegeben wird |
| connectVia             | Die Integration Runtime, mit der die Aktivitäten diesem verknüpften Dienst zugeteilt werden. Sie können Azure Integration Runtime oder selbstgehostete Integration Runtime verwenden. Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. | Nein                                       |

## <a name="azure-machine-learning-linked-service"></a>Mit Azure Machine Learning verknüpfter Dienst
Sie können einen mit Azure Machine Learning verknüpften Dienst erstellen, um einen Azure Machine Learning-Arbeitsbereich mit einer Data Factory zu verbinden.

> [!NOTE]
> Derzeit wird nur die Dienstprinzipalauthentifizierung für den mit Azure Machine Learning verknüpften Dienst unterstützt.

### <a name="example"></a>Beispiel

```json
{
    "name": "AzureMLServiceLinkedService",
    "properties": {
        "type": "AzureMLService",
        "typeProperties": {
            "subscriptionId": "subscriptionId",
            "resourceGroupName": "resourceGroupName",
            "mlWorkspaceName": "mlWorkspaceName",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime?",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>Eigenschaften
| Eigenschaft               | Beschreibung                              | Erforderlich                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| type                   | Legen Sie die type-Eigenschaft auf **AzureMLService**. | Ja                                      |
| subscriptionId         | Azure-Abonnement-ID              | Ja                                      |
| resourceGroupName      | name | Ja                                      |
| mlWorkspaceName        | Name des Azure Machine Learning-Arbeitsbereichs | Ja  |
| servicePrincipalId     | Geben Sie die Client-ID der Anwendung an.     | Nein |
| servicePrincipalKey    | Geben Sie den Schlüssel der Anwendung an.           | Nein |
| tenant                 | Geben Sie die Mandanteninformationen (Domänenname oder Mandanten-ID) für Ihre Anwendung an. Diese können Sie abrufen, indem Sie im Azure-Portal mit der Maus auf den Bereich oben rechts zeigen. | Erforderlich, wenn updateResourceEndpoint angegeben wird | Nein |
| connectVia             | Die Integration Runtime, mit der die Aktivitäten diesem verknüpften Dienst zugeteilt werden. Sie können Azure Integration Runtime oder selbstgehostete Integration Runtime verwenden. Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. | Nein |

## <a name="azure-data-lake-analytics-linked-service"></a>Mit Azure Data Lake Analytics verknüpfter Dienst
Sie erstellen einen mit **Azure Data Lake Analytics** verknüpften Dienst, um einen Azure Data Lake Analytics-Computedienst mit einer Azure Data Factory zu verknüpfen. Die Data Lake Analytics-U-SQL-Aktivität in der Pipeline verweist auf diesen verknüpften Dienst. 

### <a name="example"></a>Beispiel

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics URI",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID",
            "subscriptionId": "<optional, subscription ID of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>Eigenschaften

| Eigenschaft             | Beschreibung                              | Erforderlich                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| type                 | Legen Sie die type-Eigenschaft auf **AzureDataLakeAnalytics** fest. | Ja                                      |
| .<Name der Region          | Name des Azure Data Lake Analytics-Kontos.  | Ja                                      |
| dataLakeAnalyticsUri | URI des Azure Data Lake Analytics-Kontos.           | Nein                                       |
| subscriptionId       | Azure-Abonnement-ID                    | Nein                                       |
| resourceGroupName    | Azure-Ressourcengruppenname                | Nein                                       |
| servicePrincipalId   | Geben Sie die Client-ID der Anwendung an.     | Ja                                      |
| servicePrincipalKey  | Geben Sie den Schlüssel der Anwendung an.           | Ja                                      |
| tenant               | Geben Sie die Mandanteninformationen (Domänenname oder Mandanten-ID) für Ihre Anwendung an. Diese können Sie abrufen, indem Sie im Azure-Portal mit der Maus auf den Bereich oben rechts zeigen. | Ja                                      |
| connectVia           | Die Integration Runtime, mit der die Aktivitäten diesem verknüpften Dienst zugeteilt werden. Sie können Azure Integration Runtime oder selbstgehostete Integration Runtime verwenden. Wenn keine Option angegeben ist, wird die standardmäßige Azure Integration Runtime verwendet. | Nein                                       |



## <a name="azure-databricks-linked-service"></a>Mit Azure Databricks verknüpfter Dienst
Sie können einen **mit Azure Databricks verknüpften Dienst** erstellen, um den Databricks-Arbeitsbereich zu registrieren, den Sie für die Ausführung der Databricks-Workloads (Notebook, JAR, Python) verwenden möchten. 
> [!IMPORTANT]
> Mit Databricks verknüpfte Dienste unterstützen [Instanzenpools](https://aka.ms/instance-pools) und die Authentifizierung der systemseitig zugewiesenen verwalteten Identität.

### <a name="example---using-new-job-cluster-in-databricks"></a>Beispiel: Verwenden eines neuen Auftragsclusters in Databricks

```json
{
    "name": "AzureDatabricks_LS",
    "properties": {
        "type": "AzureDatabricks",
        "typeProperties": {
            "domain": "https://eastus.azuredatabricks.net",
            "newClusterNodeType": "Standard_D3_v2",
            "newClusterNumOfWorker": "1:10",
            "newClusterVersion": "4.0.x-scala2.11",
            "accessToken": {
                "type": "SecureString",
                "value": "dapif33c9c721144c3a790b35000b57f7124f"
            }
        }
    }
}

```

### <a name="example---using-existing-interactive-cluster-in-databricks"></a>Beispiel: Verwenden eines vorhandenen interaktiven Auftragsclusters in Databricks

```json
{
    "name": " AzureDataBricksLinedService",
    "properties": {
      "type": " AzureDatabricks",
      "typeProperties": {
        "domain": "https://westeurope.azuredatabricks.net",
        "accessToken": {
            "type": "SecureString", 
            "value": "dapif33c9c72344c3a790b35000b57f7124f"
          },
        "existingClusterId": "{clusterId}"
        }
}

```

### <a name="properties"></a>Eigenschaften

| Eigenschaft             | Beschreibung                              | Erforderlich                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| name                 | Name des verknüpften Diensts               | Ja   |
| type                 | Legen Sie die type-Eigenschaft auf **Azure Databricks**: | Ja                                      |
| Domäne               | Geben Sie die Azure-Region entsprechend der Region des Databricks-Arbeitsbereichs an. Beispiel: https://eastus.azuredatabricks.net | Ja                                 |
| accessToken          | Für die Authentifizierung bei Azure Databricks ist ein Zugriffstoken erforderlich. Das Zugriffstoken muss im Databricks-Arbeitsbereich generiert werden. Ausführlichere Informationen zum Auffinden des Zugriffstokens finden Sie [hier](https://docs.azuredatabricks.net/api/latest/authentication.html#generate-token).  | Nein                                       |
| MSI          | Verwenden Sie die verwaltete Identität von Data Factory (systemseitig zugewiesen) zur Authentifizierung bei Azure Databricks. Wenn Sie die MSI-Authentifizierung verwenden, benötigen Sie kein Zugriffstoken.  | Nein                                       |
| existingClusterId    | Cluster-ID eines vorhandenen Clusters, in dem alle Aufträge ausgeführt werden. Dabei sollte es sich um einen bereits erstellten interaktiven Cluster handeln. Möglicherweise müssen Sie den Cluster manuell neu starten, falls er nicht mehr reagiert. Databricks empfiehlt, Aufträge in neuen Clustern auszuführen, um die Zuverlässigkeit zu erhöhen. Sie finden die Cluster-ID eines interaktiven Clusters unter: Databricks-Arbeitsbereich -> Cluster -> Name des interaktiven Clusters -> Konfiguration -> Tags. [Weitere Informationen](https://docs.databricks.com/user-guide/clusters/tags.html) | Nein 
| instancePoolId    | Instanzenpool-ID eines vorhandenen Pools im Databricks-Arbeitsbereich.  | Nein  |
| newClusterVersion    | Die Spark-Version des Clusters. Damit wird ein Auftragscluster in Databricks erstellt. | Nein  |
| newClusterNumOfWorker| Die Anzahl der Workerknoten, die dieser Cluster haben sollte. Ein Cluster hat einen Spark Driver und Executors entsprechend der Workeranzahl, also insgesamt Workeranzahl + 1 Spark-Knoten. Eine als Int32 formatierte Zeichenfolge wie „1“ bedeutet, dass numOfWorker den Wert 1 hat. „1:10“ steht für eine Autoskalierung von 1 als Minimum und 10 als Maximum.  | Nein                |
| newClusterNodeType   | Dieses Feld codiert mithilfe eines einzigen Werts die Ressourcen, die jedem der Spark-Knoten in diesem Cluster zur Verfügung stehen. Beispielsweise können die Spark-Knoten für arbeitsspeicher- oder rechenintensive Workloads bereitgestellt und optimiert werden. Dieses Feld wird für neue Cluster benötigt.                | Nein               |
| newClusterSparkConf  | Eine Gruppe optionaler, benutzerdefinierter Spark-Konfigurationsschlüssel-Wert-Paare. Benutzer können auch über „spark.driver.extraJavaOptions“ bzw. „spark.executor.extraJavaOptions“ eine Zeichenfolge mit zusätzlichen JVM-Optionen an den Driver und die Executors übergeben. | Nein  |
| newClusterInitScripts| Eine Gruppe optionaler, benutzerdefinierter Initialisierungsskripts für den neuen Cluster. Diese geben den DBFS-Pfad zu den Initialisierungsskripts an. | Nein  |


## <a name="azure-sql-database-linked-service"></a>Mit Azure SQL-Datenbank verknüpfter Dienst

Sie erstellen einen mit Azure SQL verknüpften Dienst und verwenden ihn mit der [Aktivität "Gespeicherte Prozedur"](transform-data-using-stored-procedure.md) zum Aufrufen einer gespeicherten Prozedur in einer Data Factory-Pipeline. Im Artikel [Azure SQL-Connector](connector-azure-sql-database.md#linked-service-properties) finden Sie weitere Informationen zu diesem verknüpften Dienst.

## <a name="azure-synapse-analytics-linked-service"></a>Mit Azure Synapse Analytics verknüpfter Dienst

Sie erstellen einen mit Azure Synapse Analytics (ehemals SQL Data Warehouse) verknüpften Dienst und verwenden ihn mit der [Aktivität „Gespeicherte Prozedur“](transform-data-using-stored-procedure.md) zum Aufrufen einer gespeicherten Prozedur in einer Data Factory-Pipeline. Im Artikel [Azure Synapse Analytics-Connector (ehemals SQL Data Warehouse)](connector-azure-sql-data-warehouse.md#linked-service-properties) finden Sie weitere Informationen zu diesem verknüpften Dienst.

## <a name="sql-server-linked-service"></a>Mit SQL Server verknüpfter Dienst

Sie erstellen einen mit SQL Server verknüpften Dienst und verwenden ihn mit der [Aktivität „Gespeicherte Prozedur“](transform-data-using-stored-procedure.md) zum Aufrufen einer gespeicherten Prozedur in einer Data Factory-Pipeline. Im Artikel [SQL Server-Connector](connector-sql-server.md#linked-service-properties) finden Sie weitere Informationen zu diesem verknüpften Dienst.

## <a name="azure-function-linked-service"></a>Verknüpfter Dienst der Azure-Funktion

Sie erstellen einen verknüpften Dienst der Azure-Funktion und verwenden ihn mit der [Aktivität „Azure Function“](control-flow-azure-function-activity.md), um Azure Functions in einer Data Factory-Pipeline auszuführen. Der Rückgabetyp der Azure-Funktion muss ein gültiges `JObject` sein. (Beachten Sie, dass [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm)*kein*`JObject` ist.) Jeder andere Rückgabetyp als `JObject` führt zu dem Benutzerfehler *Antwortinhalt ist kein gültiges JObject*.

| **Eigenschaft** | **Beschreibung** | **Erforderlich** |
| --- | --- | --- |
| type   | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **AzureFunction** | ja |
| Funktions-App-URL | URL für die Azure-Funktions-App. Das Format lautet `https://<accountname>.azurewebsites.net`. Diese URL ist der Wert unter dem Abschnitt **URL**, wenn Sie Ihre Funktions-App im Azure-Portal anzeigen.  | ja |
| Funktionsschlüssel | Der Zugriffsschlüssel für die Azure-Funktion. Klicken Sie in den Abschnitt **Verwalten** der jeweiligen Funktion, und kopieren Sie entweder den **Funktionsschlüssel** oder den **Hostschlüssel**. Weitere Informationen finden Sie hier: [HTTP-Trigger und -Bindungen in Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) | ja |
|   |   |   |

## <a name="next-steps"></a>Nächste Schritte

Eine Liste der Transformationsaktivitäten, die von Azure Data Factory unterstützt werden, finden Sie unter [Transform data in Azure Data Factory](transform-data.md) (Transformieren von Daten in Azure Data Factory).
