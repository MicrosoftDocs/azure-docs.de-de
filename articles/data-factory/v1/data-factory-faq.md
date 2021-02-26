---
title: Azure Data Factory – Häufig gestellte Fragen
description: Häufig gestellte Fragen zu Azure Data Factory
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 4ddadec327f1a4f6aa22d51ac073b9aebb0dee82
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383193"
---
# <a name="azure-data-factory---frequently-asked-questions"></a>Azure Data Factory – Häufig gestellte Fragen
> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Bei Verwendung der aktuellen Version des Data Factory-Diensts finden Sie weitere Informationen unter [frequently asked question - Data Factory](../frequently-asked-questions.md) (Häufig gestellte Fragen: Data Factory).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Allgemeine Fragen
### <a name="what-is-azure-data-factory"></a>Was ist Azure Data Factory?
Data Factory ist ein cloudbasierter Datenintegrationsdienst, der das **Verschieben und Transformieren von Daten automatisiert**. Genau wie ein Betrieb, in dem Anlagen Rohmaterialien verarbeiten und in Endprodukte umwandeln, organisiert Data Factory vorhandene Dienste so, dass Rohdaten gesammelt und in nutzbare Informationen transformiert werden.

Data Factory ermöglicht das Erstellen von datengesteuerten Workflows zum Verschieben von Daten zwischen lokalen und cloudbasierten Datenspeichern sowie zum Verarbeiten/Transformieren von Daten mithilfe von Computediensten wie Azure HDInsight und Azure Data Lake Analytics. Nachdem Sie eine Pipeline erstellt haben, die die gewünschte Aktion ausführt, können Sie die regelmäßige Ausführung der Pipeline planen (stündlich, täglich, wöchentlich usw.).   

Weitere Informationen finden Sie unter [Übersicht und wichtige Konzepte](data-factory-introduction.md).

### <a name="where-can-i-find-pricing-details-for-azure-data-factory"></a>Wo finde ich Preisinformationen zu Azure Data Factory?
Preisinformationen zu Azure Data Factory finden Sie auf der Seite [Data Factory – Preisübersicht][adf-pricing-details].  

### <a name="how-do-i-get-started-with-azure-data-factory"></a>F: Was sind die ersten Schritte mit Azure Data Factory?
* Eine Übersicht über Azure Data Factory finden Sie unter [Einführung in Azure Data Factory](data-factory-introduction.md).
* Ein Tutorial zum **Kopieren/Verschieben von Daten** mit der Kopieraktivität finden Sie unter [Kopieren von Daten aus Azure Blob Storage in Azure SQL-Datenbank](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Ein Tutorial zum **Transformieren von Daten** mit der HDInsight Hive-Aktivität finden Sie unter [Verarbeiten von Daten durch Ausführen eines Hive-Skripts in einem Hadoop-Cluster](data-factory-build-your-first-pipeline.md).

### <a name="what-is-the-data-factorys-region-availability"></a>In welchen Regionen ist Data Factory verfügbar?
Data Factory ist in den Regionen **USA, Westen** sowie in **Europa, Norden** verfügbar. Die von Data Factory verwendeten Rechen- und Speicherdienste können in anderen Regionen verfügbar sein. Siehe [Unterstützte Regionen](data-factory-introduction.md#supported-regions).

### <a name="what-are-the-limits-on-number-of-data-factoriespipelinesactivitiesdatasets"></a>Welche Grenzwerte sind hinsichtlich der Anzahl der Data Factorys/Pipelines/Aktivitäten/Datasets gegeben?
Weitere Informationen finden Sie im Abschnitt **Einschränkungen von Azure Data Factory** unter dem Artikel [Begrenzungen, Kontingente und Einschränkungen von Azure-Abonnements und -Diensten](../../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits) .

### <a name="what-is-the-authoringdeveloper-experience-with-azure-data-factory-service"></a>Was sieht die Erstellung-/Entwicklungsumgebung im Azure Data Factory-Dienst aus?
Sie können Data Factorys mithilfe eines der folgenden Werkzeuge/SDKs erstellen:

* **Visual Studio** : Sie können mit Visual Studio eine Azure Data Factory erstellen. Unter [Erstellen der ersten Azure Data Factory-Pipeline mit Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) finden Sie weitere Informationen.
* **Azure PowerShell** : Unter [Erstellen der ersten Azure Data Factory mit Azure PowerShell](data-factory-build-your-first-pipeline-using-powershell.md) finden Sie ein Tutorial und eine exemplarische Vorgehensweise zum Erstellen einer Data Factory mithilfe von PowerShell. In der [Data Factory-Cmdlet-Referenz][adf-powershell-reference] in der MSDN-Bibliothek finden Sie eine umfassende Dokumentation zu Data Factory-Cmdlets.
* **.NET-Klassenbibliothek** Sie können Data Factorys mithilfe des Data Factory .NET SDK programmgesteuert erstellen. Unter [Erstellen, Überwachen und Verwalten von Daten Factorys mit dem .NET SDK](data-factory-create-data-factories-programmatically.md) finden Sie eine exemplarische Vorgehensweise zum Erstellen einer Data Factory mit dem .NET SDK. In der [Data Factory-Klassenbibliotheksreferenz][msdn-class-library-reference] finden Sie eine umfassende Dokumentation zum Data Factory .NET SDK.
* **REST-API** Sie können auch die vom Azure-Data Factory-Dienst verfügbar gemachte REST-API zum Erstellen und Bereitstellen von Data Factorys nutzen. In der [Data Factory-REST-API-Referenz][msdn-rest-api-reference] finden Sie eine umfassende Dokumentation zur Data Factory-REST-API.
* **Azure Resource Manager-Vorlage** Weitere Informationen finden Sie unter [Tutorial: Erstellen der ersten Azure Data Factory mit einer Azure Resource Manager-Vorlage](data-factory-build-your-first-pipeline-using-arm.md).

### <a name="can-i-rename-a-data-factory"></a>Können Data Factorys umbenannt werden?
Nein. Wie bei anderen Azure-Ressourcen auch kann der Name einer Azure Data Factory nicht geändert werden.

### <a name="can-i-move-a-data-factory-from-one-azure-subscription-to-another"></a>Kann ich eine Data Factory aus einem Azure-Abonnement in ein anderes verschieben?
Ja. Verwenden Sie die Schaltfläche **Verschieben** auf Ihrem Data Factory-Blatt, wie im folgenden Diagramm dargestellt:

![Data Factory verschieben](media/data-factory-faq/move-data-factory.png)

### <a name="what-are-the-compute-environments-supported-by-data-factory"></a>Welche Compute-Umgebungen werden von Data Factory unterstützt?
Die folgende Tabelle enthält eine Liste von Compute-Umgebungen, die von Data Factory unterstützt werden, und die Aktivitäten, die darin ausgeführt werden können.

| Compute-Umgebung | activities |
| --- | --- |
| [Bedarfsgesteuerter HDInsight-Cluster](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) oder [Eigener HDInsight-Cluster](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) |[DotNet](data-factory-use-custom-activities.md), [Hive](data-factory-hive-activity.md), [Pig](data-factory-pig-activity.md), [MapReduce](data-factory-map-reduce.md), [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](data-factory-compute-linked-services.md#azure-batch-linked-service) |[DotNet](data-factory-use-custom-activities.md) |
| [Azure Machine Learning Studio (klassisch)](data-factory-compute-linked-services.md#azure-machine-learning-studio-classic-linked-service) |[Aktivitäten in Studio (klassisch): Batchausführung und Ressourcenaktualisierung](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](data-factory-compute-linked-services.md#azure-data-lake-analytics-linked-service) |[Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](data-factory-compute-linked-services.md#azure-sql-linked-service), [Azure Synapse Analytics](data-factory-compute-linked-services.md#azure-synapse-analytics-linked-service), [SQL Server](data-factory-compute-linked-services.md#sql-server-linked-service) |[Gespeicherte Prozedur](data-factory-stored-proc-activity.md) |

### <a name="how-does-azure-data-factory-compare-with-sql-server-integration-services-ssis"></a>Wie unterscheidet sich Azure Data Factory von SQL Server Integration Services (SSIS)? 
Siehe die Präsentation [Azure Data Factory vs. SSIS](https://www.sqlbits.com/Sessions/Event15/Azure_Data_Factory_vs_SSIS) (in englischer Sprache) von einem unserer MVPs (Most Valued Professionals): Reza Rad. Einige der kürzlich in Data Factory vorgenommenen Änderungen werden möglicherweise in der Präsentation nicht aufgeführt. Azure Data Factory werden fortlaufend weitere Funktionen hinzugefügt. Azure Data Factory werden fortlaufend weitere Funktionen hinzugefügt. Diese Aktualisierungen werden irgendwann im Laufe dieses Jahres in den Vergleich der Datenintegrationstechnologien von Microsoft aufgenommen.   

## <a name="activities---faq"></a>Aktivitäten – Häufig gestellte Fragen
### <a name="what-are-the-different-types-of-activities-you-can-use-in-a-data-factory-pipeline"></a>Welche verschiedenen Arten von Aktivitäten können in einer Data Factory-Pipeline verwendet werden?
* [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) zum Verschieben von Daten.
* [Datentransformationsaktivitäten](data-factory-data-transformation-activities.md) zum Verarbeiten/Transformieren von Daten.

### <a name="when-does-an-activity-run"></a>Wann wird eine Aktivität ausgeführt?
Die Konfigurationseinstellung **availability** in der Ausgabedatentabelle bestimmt, wann die Aktivität erfolgt. Wenn Eingabedatasets angegeben sind, prüft die Aktivität, ob alle Eingabedatenabhängigkeiten erfüllt sind (den Status **Bereit** aufweisen), bevor die Ausführung beginnt.

## <a name="copy-activity---faq"></a>Kopieraktivität – Häufig gestellte Fragen
### <a name="is-it-better-to-have-a-pipeline-with-multiple-activities-or-a-separate-pipeline-for-each-activity"></a>Ist es besser, eine Pipeline mit mehreren Aktivitäten oder eine separate Pipeline für jede Aktivität einzurichten?
Pipelines dienen zum Bündeln verwandter Aktivitäten. Sie können die Aktivitäten in einer Pipeline halten, wenn die Datasets, die diese verbinden, nicht von anderen Aktivitäten außerhalb der Pipeline genutzt werden. Auf diese Weise müssen Sie Pipelineaktivitäten nicht verknüpfen, damit diese sich aneinander ausrichten. Darüber hinaus kann die Datenintegrität in den Tabellen, die für die Pipeline intern sind, beim Aktualisieren der Pipeline besser beibehalten werden. Bei einer Pipelineaktualisierung werden alle Aktivitäten in der Pipeline beendet, entfernt und neu erstellt. Aus Erstellungssicht kann es auch einfacher sein, den Datenfluss innerhalb der zugehörigen Aktivitäten in einer JSON-Datei für die Pipeline nachzuverfolgen.

### <a name="what-are-the-supported-data-stores"></a>Welche Datenspeicher werden unterstützt?
Die Kopieraktivität in Data Factory kopiert die Daten aus einem Quelldatenspeicher in einen Senkendatenspeicher. Data Factory unterstützt die folgenden Datenspeicher. Daten aus beliebigen Quellen können in beliebige Senken geschrieben werden. Klicken Sie auf einen Datenspeicher, um zu erfahren, wie Daten in diesen/aus diesem Speicher kopiert werden.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Datenspeicher mit * können lokal oder in Azure IaaS verfügbar sein. Für ihre Verwendung müssen Sie das [Datenverwaltungsgateway](data-factory-data-management-gateway.md) auf einem lokalen oder einem Azure IaaS-Computer installieren.

### <a name="what-are-the-supported-file-formats"></a>Welche Dateiformate werden unterstützt?
[!INCLUDE [data-factory-file-format](../../../includes/data-factory-file-format.md)]

### <a name="where-is-the-copy-operation-performed"></a>Wo wird der Kopiervorgang ausgeführt?
Ausführliche Informationen finden Sie im Abschnitt [Global verfügbare Datenverschiebung](data-factory-data-movement-activities.md#global) . Kurz gesagt: Wenn ein lokaler Datenspeicher beteiligt ist, wird der Kopiervorgang vom Datenverwaltungsgateway in Ihrer lokalen Umgebung ausgeführt. Wenn Daten zwischen zwei Cloudspeichern bewegt werden, wird der Kopiervorgang in der Region ausgeführt, die dem Standort der Senke in der gleichen geografischen Region am nächsten liegt.

## <a name="hdinsight-activity---faq"></a>HDInsight-Aktivität – Häufig gestellte Fragen
### <a name="what-regions-are-supported-by-hdinsight"></a>In welchen Regionen wird HDInsight unterstützt?
Weitere Informationen finden Sie im Abschnitt zur geografischen Verfügbarkeit des folgenden Artikels oder unter [HDInsight – Preisübersicht][hdinsight-supported-regions].

### <a name="what-region-is-used-by-an-on-demand-hdinsight-cluster"></a>Welche Region wird von einem bedarfsgesteuerten HDInsight-Cluster verwendet?
Der bedarfsgesteuerte HDInsight-Cluster wird in derselben Region erstellt, in der sich der Speicher befindet, den Sie für die Verwendung mit dem Cluster angegeben haben.    

### <a name="how-to-associate-additional-storage-accounts-to-your-hdinsight-cluster"></a>Wie können weitere Speicherkonten mit Ihrem HDInsight-Cluster verknüpft werden?
Wenn Sie Ihren eigenen HDInsight-Cluster (BYOC - Bring Your Own Cluster) verwenden, lesen Sie die folgenden Themen:

* [Verwenden eines HDInsight-Clusters mit alternativen Speicherkonten und Metastores][hdinsight-alternate-storage]
* [Verwenden zusätzlicher Speicherkonten mit HDInsight Hive][hdinsight-alternate-storage-2]

Wenn Sie einen bedarfsgesteuerten Cluster verwenden, der vom Data Factory-Dienst erstellt wird, geben Sie zusätzliche Speicherkonten für den verknüpften HDInsight-Dienst an, damit der Data Factory-Dienst diese in Ihrem Auftrag registrieren kann. Verwenden Sie in der JSON-Definition des bedarfsgesteuerten verknüpften Diensts die **additionalLinkedServiceNames** -Eigenschaft, um alternative Speicherkonten anzugeben, wie im folgenden JSON-Codeausschnitt gezeigt:

```JSON
{
    "name": "MyHDInsightOnDemandLinkedService",
    "properties":
    {
        "type": "HDInsightOnDemandLinkedService",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "linkedServiceName": "LinkedService-SampleData",
            "additionalLinkedServiceNames": [ "otherLinkedServiceName1", "otherLinkedServiceName2" ]
        }
    }
}
```
Im obigen Beispiel stellen "otherLinkedServiceName1" und "otherLinkedServiceName2" verknüpfte Dienste dar, deren Definitionen Anmeldeinformationen enthalten, die der HDInsight-Cluster für den Zugriff auf alternative Speicherkonten benötigt.

## <a name="slices---faq"></a>Slices – Häufig gestellte Fragen
### <a name="why-are-my-input-slices-not-in-ready-state"></a>Wieso weisen meine Eingabeslices nicht den Status „Bereit“ auf?
Ein weit verbreiteter Fehler besteht darin, die **external**-Eigenschaft im Eingabedataset nicht auf **true** festzulegen, wenn die Eingabedaten für die Data Factory extern sind (also nicht von der Data Factory erstellt wurden).

Im folgenden Beispiel müssen Sie nur für **dataset1** den Wert **external** auf „true“ festlegen.  

**DataFactory1** Pipeline 1: dataset1 &gt; activity1 &gt; dataset2 &gt; activity2 &gt; dataset3 Pipeline 2: dataset3 &gt; activity3 &gt; dataset4

Wenn Sie über eine andere Data Factory mit einer Pipeline, die Dataset4 nimmt, verfügen (erstellt von Pipeline 2 in Data Factory 1), markieren Sie Dataset4 als externes Dataset, da das Dataset von einer anderen Data Factory (DataFactory1, nicht DataFactory2) erstellt wird.  

**DataFactory2**    
Pipeline 1: dataset4 > activity4 > dataset5

Wenn die Eigenschaft „external“ richtig festgelegt wurde, überprüfen Sie, ob die Eingabedaten an dem Speicherort existieren, der in der Definition des Eingabedatasets angegeben wurde.

### <a name="how-to-run-a-slice-at-another-time-than-midnight-when-the-slice-is-being-produced-daily"></a>Wie kann ein Slice zu einer anderen Zeit als Mitternacht ausgeführt werden, wenn der Slice täglich erstellt wird?
Verwenden Sie die **offset** -Eigenschaft, um die Zeit anzugeben, zu der der Slice erstellt werden soll. Weitere Informationen zu dieser Eigenschaft finden Sie im Abschnitt [Dataset: Availability](data-factory-create-datasets.md#dataset-availability) . Hier ist ein kurzes Beispiel:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
Tägliche Slices starten anstatt zur Standardzeit (Mitternacht) um **6:00 Uhr** .     

### <a name="how-can-i-rerun-a-slice"></a>Wie kann ich einen Slice erneut ausführen?
Sie können einen Slice auf eine der folgenden Arten erneut ausführen:

* Verwenden Sie die App „Überwachen und Verwalten“, um ein Aktivitätsfenster oder einen Slice erneut auszuführen. Anweisungen finden Sie unter [Wiederholen ausgewählter Aktivitätsfenster](data-factory-monitor-manage-app.md#perform-batch-actions) .   
* Klicken Sie im Azure-Portal auf der Befehlsleiste für den Slice auf dem Blatt **DATENSLICE** auf **Ausführen**.
* Führen Sie das Cmdlet **Set-AzDataFactorySliceStatus** aus, wobei der Status des Slice auf **Waiting** festgelegt ist.   

    ```powershell
    Set-AzDataFactorySliceStatus -Status Waiting -ResourceGroupName $ResourceGroup -DataFactoryName $df -TableName $table -StartDateTime "02/26/2015 19:00:00" -EndDateTime "02/26/2015 20:00:00"
    ```
  Unter [Set-AzDataFactorySliceStatus][set-azure-datafactory-slice-status] finden Sie ausführliche Informationen zum Cmdlet.

### <a name="how-long-did-it-take-to-process-a-slice"></a>Wie lange hat die Verarbeitung eines Slices gedauert?
Verwenden Sie den Aktivitätsfenster-Explorer in der App „Überwachen und Verwalten“, um zu erfahren, wie lange es gedauert hat, einen Datenslice zu verarbeiten. Weitere Informationen finden Sie unter [Aktivitätsfenster-Explorer](data-factory-monitor-manage-app.md#activity-window-explorer) .

Sie können auch wie folgt im Azure-Portal vorgehen:  

1. Klicken Sie auf der Kachel **Datasets** auf das Blatt **DATA FACTORY** für Ihre Data Factory.
2. Klicken Sie auf dem Blatt **Datasets** auf das gewünschte Dataset.
3. Wählen Sie auf dem Blatt **TABELLE** in der Liste **Zuletzt verwendete Slices** den gewünschten Slice aus.
4. Klicken Sie auf dem Blatt **DATENSLICE** in der Liste **Aktivitätsausführungen** auf die Aktivitätsausführung.
5. Klicken Sie auf der Kachel **Eigenschaften** auf das Blatt **DETAILS ZUR AKTIVITÄTSAUSFÜHRUNG**.
6. Daraufhin sollte das Feld **Dauer** mit einem Wert angezeigt werden. Dieser Wert ist die Verarbeitungszeit des Slices.   

### <a name="how-to-stop-a-running-slice"></a>Wie wird ein ausgeführter Slice beendet?
Wenn Sie die Ausführung der Pipeline beenden müssen, können Sie das Cmdlet [Suspend-AzDataFactoryPipeline](/powershell/module/az.datafactory/suspend-azdatafactorypipeline) verwenden. Derzeit werden laufende Sliceausführungen bei Anhalten der Pipeline nicht beendet. Sobald die laufenden Ausführungen abgeschlossen sind, wird kein zusätzlicher Slice ausgewählt.

Wenn Sie alle Ausführungen wirklich sofort beenden möchten, ist die einzige Möglichkeit das Löschen und erneute Erstellen der Pipeline. Wenn Sie die Pipeline löschen, müssen Sie keine Tabellen und verknüpften Dienste löschen, die von der Pipeline verwendet werden.

[create-factory-using-dotnet-sdk]: data-factory-create-data-factories-programmatically.md
[msdn-class-library-reference]: /dotnet/api/microsoft.azure.management.datafactories.models
[msdn-rest-api-reference]: /rest/api/datafactory/

[adf-powershell-reference]: /powershell/module/az.datafactory/
[azure-portal]: https://portal.azure.com
[set-azure-datafactory-slice-status]: /powershell/module/az.datafactory/set-Azdatafactoryslicestatus

[adf-pricing-details]: https://go.microsoft.com/fwlink/?LinkId=517777
[hdinsight-supported-regions]: https://azure.microsoft.com/pricing/details/hdinsight/
[hdinsight-alternate-storage]: https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
[hdinsight-alternate-storage-2]: /archive/blogs/cindygross/use-additional-storage-accounts-with-hdinsight-hive