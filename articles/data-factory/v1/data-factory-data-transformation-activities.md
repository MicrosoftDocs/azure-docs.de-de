---
title: 'Datentransformation: Verarbeiten und Transformieren von Daten '
description: Erfahren Sie, wie Sie Daten in Azure Data Factory mit Hadoop, Azure Machine Learning Studio (Classic) oder Azure Data Lake Analytics transformieren oder verarbeiten.
services: data-factory
documentationcenter: ''
author: dcstwh
ms.author: weetok
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 587e8eaf36a8e9d0be86237e2db72f952853a0ff
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96495683"
---
# <a name="transform-data-in-azure-data-factory-version-1"></a>Transformieren von Daten in Azure Data Factory Version 1
> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md)  
> * [Pig](data-factory-pig-activity.md)  
> * [MapReduce](data-factory-map-reduce.md)  
> * [Hadoop-Datenströme](data-factory-hadoop-streaming-activity.md)
> * [Azure Machine Learning Studio (klassisch)](data-factory-azure-ml-batch-execution-activity.md) 
> * [Gespeicherte Prozedur](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL](data-factory-usql-activity.md)
> * [Benutzerdefinierte .NET-Aktivität](data-factory-use-custom-activities.md)

## <a name="overview"></a>Übersicht
> [!NOTE]
> Dieser Artikel gilt für Version 1 von Data Factory. Wenn Sie die aktuelle Version des Data Factory-Diensts verwenden, finden Sie weitere Informationen unter [data transformation activities in Data Factory](../transform-data.md) (Aktivitäten zur Datentransformation in Data Factory).

Dieser Artikel enthält Informationen zu Datentransformationsaktivitäten in Azure Data Factory, mit denen Sie Ihre Rohdaten in Vorhersagen und Einblicke transformieren und verarbeiten können. Transformationsaktivitäten erfolgen in einer Compute-Umgebung wie Azure HDInsight-Cluster oder einem Azure Batch. Sie finden hier Links zu Artikeln mit detaillierten Informationen zu jeder Transformationsaktivität.

Data Factory unterstützt die folgenden Transformationsaktivitäten, die [Pipelines](data-factory-create-pipelines.md) entweder einzeln oder mit einer anderen Aktivität verkettet hinzugefügt werden können.

> [!NOTE]
> Eine exemplarische Vorgehensweise mit einer detaillierten Anleitung finden Sie im Artikel [Erstellen einer Pipeline mit Hive-Transformation](data-factory-build-your-first-pipeline.md) .  
> 
> 

## <a name="hdinsight-hive-activity"></a>HDInsight Hive-Aktivität
Die HDInsight Hive-Aktivität in einer Data Factory-Pipeline wendet Hive-Abfragen auf Ihren eigenen oder bedarfsgesteuerten Windows-/Linux-basierten HDInsight-Cluster an. Im Artikel [Hive-Aktivität](data-factory-hive-activity.md) finden Sie Details zu dieser Aktivität. 

## <a name="hdinsight-pig-activity"></a>HDInsight Pig-Aktivität
Die HDInsight Pig-Aktivität in einer Data Factory-Pipeline wendet Pig-Abfragen auf Ihren eigenen oder bedarfsgesteuerten Windows-/Linux-basierten HDInsight-Cluster an. Im Artikel [Pig-Aktivität](data-factory-pig-activity.md) finden Sie Details zu dieser Aktivität. 

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce-Aktivität
Die HDInsight MapReduce-Aktivität in einer Data Factory-Pipeline wendet MapReduce-Programme auf Ihren eigenen oder bedarfsgesteuerten Windows-/Linux-basierten HDInsight-Cluster an. Im Artikel [MapReduce-Aktivität](data-factory-map-reduce.md) finden Sie Details zu dieser Aktivität.

## <a name="hdinsight-streaming-activity"></a>HDInsight-Streamingaktivität
Die HDInsight-Streamingaktivität in einer Data Factory-Pipeline wendet Hadoop-Streamingprogramme auf Ihren eigenen oder bedarfsgesteuerten Windows-/Linux-basierten HDInsight-Cluster an. Unter [HDInsight-Streamingaktivität](data-factory-hadoop-streaming-activity.md) finden Sie ausführliche Informationen zu dieser Aktivität.

## <a name="hdinsight-spark-activity"></a>HDInsight Spark-Aktivität
Die HDInsight Spark-Aktivität in einer Data Factory-Pipeline führt Spark-Programme in Ihrem eigenen HDInsight-Cluster aus. Weitere Informationen finden Sie unter [Aufrufen von Spark-Programmen aus Azure Data Factory](data-factory-spark.md). 

## <a name="azure-machine-learning-studio-classic-activities"></a>Aktivitäten von Azure Machine Learning Studio (Classic)
Azure Data Factory ermöglicht die einfache Erstellung von Pipelines, die einen veröffentlichten Azure Machine Learning Studio (Classic)-Webdienst für Predictive Analytics nutzen. Mithilfe der [Batchausführungsaktivität](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) in einer Azure Data Factory-Pipeline können Sie einen Webdienst für Studio (Classic) aufrufen, um Vorhersagen zu den Daten im Batch zu machen.

Im Laufe der Zeit müssen die Vorhersagemodelle in den Bewertungsexperimenten von Studio (Classic) mit neuen Eingabedatasets neu trainiert werden. Wenn Sie mit dem erneuten Trainieren fertig sind, sollten Sie den Bewertungswebdienst mit dem neu trainierten Machine Learning-Modell aktualisieren. Verwenden Sie die [Ressourcenaktualisierungsaktivität](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) , um den Webdienst mit dem neu trainierten Modell zu aktualisieren.  

Ausführliche Informationen zu diesen Aktivitäten in Studio (Classic) finden Sie unter [Erstellen einer Vorhersagepipeline mithilfe von Azure Machine Learning Studio (Classic) und Azure Data Factory](data-factory-azure-ml-batch-execution-activity.md). 

## <a name="stored-procedure-activity"></a>Aktivität „Gespeicherte Prozedur“
Sie können die SQL Server-Aktivität „Gespeicherte Prozedur“ in einer Data Factory-Pipeline verwenden, um eine gespeicherte Prozedur in einem der folgenden Datenspeicher aufzurufen: Azure SQL-Datenbank, Azure Synapse Analytics, SQL Server-Datenbank in Ihrem Unternehmen oder auf einem virtuellen Azure-Computer. Unter [Aktivität „Gespeicherte Prozedur“](data-factory-stored-proc-activity.md) finden Sie Details.  

## <a name="data-lake-analytics-u-sql-activity"></a>U-SQL-Aktivität für Data Lake Analytics
Die U-SQL-Aktivität für Data Lake Analytics wendet ein U-SQL-Skript auf einen Azure Data Lake Analytics-Cluster an. Unter [U-SQL-Aktivität für Data Lake Analytics](data-factory-usql-activity.md) finden Sie Details. 

## <a name="net-custom-activity"></a>Benutzerdefinierte .NET-Aktivität
Wenn Sie Daten auf eine Weise transformieren/verarbeiten müssen, die von Data Factory nicht unterstützt wird, können Sie eine benutzerdefinierte Aktivität mit Ihrer eigenen Datenverarbeitungslogik erstellen und in der Pipeline verwenden. Sie können die benutzerdefinierte .NET-Aktivität so konfigurieren, dass sie entweder mithilfe eines Azure Batch-Diensts oder eines Azure HDInsight-Clusters ausgeführt wird. Unter [Verwenden benutzerdefinierter Aktivitäten](data-factory-use-custom-activities.md) finden Sie Einzelheiten. 

Sie können eine benutzerdefinierte Aktivität erstellen, um R-Skripts in Ihrem HDInsight-Cluster mit installiertem R auszuführen. Informationen hierzu finden Sie unter [RunRScriptUsingADFSample](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample). 

## <a name="compute-environments"></a>Compute-Umgebungen
Sie erstellen einen verknüpften Dienst für die Computeumgebung und verwenden dann den verknüpften Dienst, wenn Sie eine Transformationsaktivität definieren. Es gibt zwei Typen von Compute-Umgebungen, die von Data Factory unterstützt werden. 

1. **Bei Bedarf:**  In diesem Fall wird die Compute-Umgebung vollständig von Data Factory verwaltet. Der Data Factory-Dienst erstellt diese Umgebung automatisch, bevor ein Auftrag zur Verarbeitung von Daten übermittelt wird. Sobald der Auftrag abgeschlossen wurde, wird die Umgebung entfernt. Sie können differenzierte Einstellungen für die bedarfsgesteuerte Computeumgebung zur Auftragsausführung, Clusterverwaltung sowie für Bootstrappingaktionen konfigurieren und steuern. 
2. **Bring Your Own:** In diesem Fall können Sie Ihre eigene Compute-Umgebung (z.B. HDInsight-Cluster) als verknüpften Dienst in Data Factory registrieren. Die Compute-Umgebung wird von Ihnen verwaltet und von Data Factory zum Ausführen von Aktivitäten verwendet. 

Unter dem Artikel [Verknüpfte Computedienste](data-factory-compute-linked-services.md) finden Sie Informationen zu Computediensten, die von Data Factory unterstützt werden. 

## <a name="summary"></a>Zusammenfassung
Azure Data Factory unterstützt für die Aktivitäten die folgenden Datentransformationsaktivitäten und Compute-Umgebungen. Die Transformationsaktivitäten werden Pipelines entweder einzeln oder mit einer anderen Aktivität verkettet hinzugefügt.

| Datentransformationsaktivität | Compute-Umgebung |
|:--- |:--- |
| [Hive](data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop-Datenströme](data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Aktivitäten von Azure Machine Learning Studio (Classic): Batchausführung und Ressourcenaktualisierung](data-factory-azure-ml-batch-execution-activity.md) |Azure VM |
| [Gespeicherte Prozedur](data-factory-stored-proc-activity.md) |Azure SQL, Azure Synapse Analytics oder SQL Server |
| [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](data-factory-use-custom-activities.md) |HDInsight [Hadoop] oder Azure Batch |

