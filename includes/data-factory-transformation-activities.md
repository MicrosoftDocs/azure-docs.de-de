---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: eabfca64a4bdf1f72375575161111ddaff55eba3
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178556"
---
Azure Data Factory unterstützt die folgenden Transformationsaktivitäten, die Pipelines entweder einzeln oder mit einer anderen Aktivität verkettet hinzugefügt werden können.

| Datentransformationsaktivität | Compute-Umgebung |
|:--- |:--- |
| [Hive](../articles/data-factory/v1/data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](../articles/data-factory/v1/data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](../articles/data-factory/v1/data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop-Datenströme](../articles/data-factory/v1/data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Spark](../articles/data-factory/v1/data-factory-spark.md) | HDInsight [Hadoop] |
| [Machine Learning-Aktivitäten: Batchausführung und Ressourcenaktualisierung](../articles/data-factory/v1/data-factory-azure-ml-batch-execution-activity.md) |Azure-VM |
| [Gespeicherte Prozedur](../articles/data-factory/v1/data-factory-stored-proc-activity.md) |Azure SQL, Azure SQL Data Warehouse oder SQL Server |
| [Data Lake Analytics U-SQL](../articles/data-factory/v1/data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](../articles/data-factory/v1/data-factory-use-custom-activities.md) |HDInsight [Hadoop] oder Azure Batch |

> [!NOTE]
> Sie können die MapReduce-Aktivität verwenden, um Spark-Programme in Ihrem HDInsight Spark-Cluster auszuführen. Weitere Informationen finden Sie unter [Aufrufen von Spark-Programmen aus Data Factory](../articles/data-factory/v1/data-factory-spark.md) .
> Sie können eine benutzerdefinierte Aktivität erstellen, um R-Skripts in Ihrem HDInsight-Cluster mit installiertem R auszuführen. Informationen hierzu finden Sie unter [RunRScriptUsingADFSample](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample).
> 
> 

