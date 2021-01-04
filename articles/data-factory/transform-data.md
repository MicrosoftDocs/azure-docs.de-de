---
title: Transformieren von Daten
description: Transformieren oder verarbeiten Sie Daten in Azure Data Factory mit Hadoop, Azure Machine Learning Studio (Classic) oder Azure Data Lake Analytics.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 07/31/2018
ms.openlocfilehash: 37eac4acab7232e44f94e852b1c04c5549447b09
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92637682"
---
# <a name="transform-data-in-azure-data-factory"></a>Transformieren von Data in Azure Data Factory

> [!div class="op_single_selector"]
> * [Mapping Data Flow](data-flow-create.md)
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [HDInsight-Streaming](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [Azure Machine Learning Studio (klassisch)](transform-data-using-machine-learning.md) 
> * [Gespeicherte Prozedur](transform-data-using-stored-procedure.md)
> * [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md)
> * [Databricks-Notebook](transform-data-databricks-notebook.md)
> * [JAR in Databricks](transform-data-databricks-jar.md)
> * [Python in Databricks](transform-data-databricks-python.md)
> * [Benutzerdefinierte .NET-Aktivität](transform-data-using-dotnet-custom-activity.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>Übersicht
Dieser Artikel enthält Informationen zu Datentransformationsaktivitäten in Azure Data Factory, mit denen Sie Ihre Rohdaten in Vorhersagen und Einblicke im gewünschten Umfang transformieren und verarbeiten können. Eine Transformationsaktivität erfolgt in einer Computing-Umgebung wie Azure Databricks oder Azure HDInsight. Sie finden hier Links zu Artikeln mit detaillierten Informationen zu jeder Transformationsaktivität.

Data Factory unterstützt die folgenden Transformationsaktivitäten, die [Pipelines](concepts-pipelines-activities.md) entweder einzeln oder mit einer anderen Aktivität verkettet hinzugefügt werden können.

## <a name="transform-natively-in-azure-data-factory-with-data-flows"></a>Systeminternes Transformieren in Azure Data Factory mit Datenflüssen

### <a name="mapping-data-flows"></a>Zuordnen von Datenflüssen

Mapping Data Flows (Zuordnungsdatenflüsse) sind visuell entworfene Datentransformationen in Azure Data Factory. Mit Data Flows können Data Engineers grafische Datentransformationslogik entwickeln, ohne Code schreiben zu müssen. Die daraus resultierenden Datenflüsse werden als Aktivitäten in Azure Data Factory-Pipelines ausgeführt, für die horizontal hochskalierte Spark-Cluster verwendet werden. Datenflussaktivitäten können über vorhandene Planungs-, Steuerungs-, Fluss- und Überwachungsfunktionen in Data Factory operationalisiert werden. Weitere Informationen finden Sie unter [Mapping Data Flows (Zuordnungsdatenflüsse)](concepts-data-flow-overview.md).

### <a name="wrangling-data-flows"></a>Wranglingdatenflüsse

Wranglingdatenflüsse in Azure Data Factory ermöglicht Ihnen das iterative Vorbereiten von Daten ohne Code für die Cloud. Wranglingdatenflüsse können in [Power Query Online](/power-query/) integriert werden und stellen Power Query M-Funktionen für Data Wrangling in der Cloud über Spark-Ausführung bereit. Weitere Informationen finden Sie unter [Wranglingdatenflüsse](wrangling-data-flow-overview.md).

## <a name="external-transformations"></a>Externe Transformationen

Optional können Sie Transformationen manuell kodieren und die externe Compute-Umgebung selbst verwalten.

### <a name="hdinsight-hive-activity"></a>HDInsight Hive-Aktivität
Die HDInsight Hive-Aktivität in einer Data Factory-Pipeline wendet Hive-Abfragen auf Ihren eigenen oder bedarfsgesteuerten Windows-/Linux-basierten HDInsight-Cluster an. Im Artikel [Hive-Aktivität](transform-data-using-hadoop-hive.md) finden Sie Details zu dieser Aktivität. 

### <a name="hdinsight-pig-activity"></a>HDInsight Pig-Aktivität
Die HDInsight Pig-Aktivität in einer Data Factory-Pipeline wendet Pig-Abfragen auf Ihren eigenen oder bedarfsgesteuerten Windows-/Linux-basierten HDInsight-Cluster an. Im Artikel [Pig-Aktivität](transform-data-using-hadoop-pig.md) finden Sie Details zu dieser Aktivität. 

### <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce-Aktivität
Die HDInsight MapReduce-Aktivität in einer Data Factory-Pipeline wendet MapReduce-Programme auf Ihren eigenen oder bedarfsgesteuerten Windows-/Linux-basierten HDInsight-Cluster an. Im Artikel [MapReduce-Aktivität](transform-data-using-hadoop-map-reduce.md) finden Sie Details zu dieser Aktivität.

### <a name="hdinsight-streaming-activity"></a>HDInsight-Streamingaktivität
Die HDInsight-Streamingaktivität in einer Data Factory-Pipeline wendet Hadoop-Streamingprogramme auf Ihren eigenen oder bedarfsgesteuerten Windows-/Linux-basierten HDInsight-Cluster an. Unter [HDInsight-Streamingaktivität](transform-data-using-hadoop-streaming.md) finden Sie ausführliche Informationen zu dieser Aktivität.

### <a name="hdinsight-spark-activity"></a>HDInsight Spark-Aktivität
Die HDInsight Spark-Aktivität in einer Data Factory-Pipeline führt Spark-Programme in Ihrem eigenen HDInsight-Cluster aus. Weitere Informationen finden Sie unter [Aufrufen von Spark-Programmen aus Azure Data Factory](transform-data-using-spark.md). 

### <a name="azure-machine-learning-studio-classic-activities"></a>Aktivitäten in Azure Machine Learning Studio (Classic)
Azure Data Factory ermöglicht die einfache Erstellung von Pipelines, die einen veröffentlichten Azure Machine Learning Studio (Classic)-Webdienst für Predictive Analytics nutzen. Mithilfe der [Batchausführungsaktivität](transform-data-using-machine-learning.md) in einer Azure Data Factory-Pipeline können Sie einen Studio (Classic)-Webdienst aufrufen, um Vorhersagen für die Daten im Batch zu treffen.

Im Laufe der Zeit müssen die Vorhersagemodelle in den Bewertungsexperimenten von Azure Machine Learning Studio (Classic) mit neuen Eingabedatasets neu trainiert werden. Wenn Sie mit dem erneuten Trainieren fertig sind, sollten Sie den Bewertungswebdienst mit dem neu trainierten Machine Learning-Modell aktualisieren. Verwenden Sie die [Ressourcenaktualisierungsaktivität](update-machine-learning-models.md), um den Webdienst mit dem neu trainierten Modell zu aktualisieren.  

Ausführliche Informationen zu diesen Aktivitäten in Studio (Classic) finden Sie unter [Erstellen einer Vorhersagepipeline mithilfe von Azure Machine Learning Studio (Classic) und Azure Data Factory](transform-data-using-machine-learning.md). 

### <a name="stored-procedure-activity"></a>Aktivität „Gespeicherte Prozedur“
Sie können die SQL Server-Aktivität „Gespeicherte Prozedur“ in einer Data Factory-Pipeline verwenden, um eine gespeicherte Prozedur in einem der folgenden Datenspeicher aufzurufen: Azure SQL-Datenbank, Azure Synapse Analytics (ehemals SQL Data Warehouse), SQL Server-Datenbank in Ihrem Unternehmen oder auf einem virtuellen Azure-Computer. Unter [Aktivität „Gespeicherte Prozedur“](transform-data-using-stored-procedure.md) finden Sie Details.  

### <a name="data-lake-analytics-u-sql-activity"></a>U-SQL-Aktivität für Data Lake Analytics
Die U-SQL-Aktivität für Data Lake Analytics wendet ein U-SQL-Skript auf einen Azure Data Lake Analytics-Cluster an. Unter [U-SQL-Aktivität für Data Lake Analytics](transform-data-using-data-lake-analytics.md) finden Sie Details. 

### <a name="databricks-notebook-activity"></a>Databricks-Notebook-Aktivität

Die Azure Databricks-Notebook-Aktivität in einer Data Factory-Pipeline führt ein Databricks-Notebook in Ihrem Azure Databricks-Arbeitsbereich aus. Azure Databricks ist eine verwaltete Plattform für die Ausführung von Apache Spark. Informationen finden Sie unter [Transformieren von Daten durch Ausführen eines Databricks-Notebooks](transform-data-databricks-notebook.md).

### <a name="databricks-jar-activity"></a>Databricks-JAR-Aktivität

Die JAR-Aktivität in Azure Databricks in einer Data Factory-Pipeline führt eine Spark JAR-Datei in Ihrem Azure Databricks-Cluster aus. Azure Databricks ist eine verwaltete Plattform für die Ausführung von Apache Spark. Informationen finden Sie unter [Transformieren von Daten durch Ausführen einer JAR-Aktivität in Azure Databricks](transform-data-databricks-jar.md).

### <a name="databricks-python-activity"></a>Databricks-Python-Aktivität

Die Python-Aktivität in Azure Databricks in einer Data Factory-Pipeline führt eine Python-Datei in Ihrem Azure Databricks-Cluster aus. Azure Databricks ist eine verwaltete Plattform für die Ausführung von Apache Spark. Informationen finden Sie unter [Transformieren von Daten durch Ausführen einer Python-Aktivität in Azure Databricks](transform-data-databricks-python.md).

### <a name="custom-activity"></a>Benutzerdefinierte Aktivität
Wenn Sie Daten auf eine Weise transformieren/verarbeiten müssen, die von Data Factory nicht unterstützt wird, können Sie eine benutzerdefinierte Aktivität mit Ihrer eigenen Datenverarbeitungslogik erstellen und in der Pipeline verwenden. Sie können die benutzerdefinierte .NET-Aktivität so konfigurieren, dass sie entweder mithilfe eines Azure Batch-Diensts oder eines Azure HDInsight-Clusters ausgeführt wird. Unter [Verwenden benutzerdefinierter Aktivitäten](transform-data-using-dotnet-custom-activity.md) finden Sie Einzelheiten. 

Sie können eine benutzerdefinierte Aktivität erstellen, um R-Skripts in Ihrem HDInsight-Cluster mit installiertem R auszuführen. Informationen hierzu finden Sie unter [RunRScriptUsingADFSample](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). 

### <a name="compute-environments"></a>Compute-Umgebungen
Sie erstellen einen verknüpften Dienst für die Computeumgebung und verwenden dann den verknüpften Dienst, wenn Sie eine Transformationsaktivität definieren. Es gibt zwei Typen von Compute-Umgebungen, die von Data Factory unterstützt werden. 

- **Bei Bedarf:** In diesem Fall wird die Compute-Umgebung vollständig von Data Factory verwaltet. Der Data Factory-Dienst erstellt diese Umgebung automatisch, bevor ein Auftrag zur Verarbeitung von Daten übermittelt wird. Sobald der Auftrag abgeschlossen wurde, wird die Umgebung entfernt. Sie können differenzierte Einstellungen für die bedarfsgesteuerte Computeumgebung zur Auftragsausführung, Clusterverwaltung sowie für Bootstrappingaktionen konfigurieren und steuern. 
- **Bring Your Own:** In diesem Fall können Sie Ihre eigene Compute-Umgebung (z. B. HDInsight-Cluster) als verknüpften Dienst in Data Factory registrieren. Die Compute-Umgebung wird von Ihnen verwaltet und von Data Factory zum Ausführen von Aktivitäten verwendet. 

Unter dem Artikel [Verknüpfte Computedienste](compute-linked-services.md) finden Sie Informationen zu Computediensten, die von Data Factory unterstützt werden. 

## <a name="next-steps"></a>Nächste Schritte
Ein Beispiel für die Verwendung einer Transformationsaktivität finden Sie im folgenden Tutorial: [Transformieren von Daten mit Spark](tutorial-transform-data-spark-powershell.md)