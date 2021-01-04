---
title: Transformieren von Daten in der Cloud mithilfe von PowerShell
description: Dieses PowerShell-Skript transformiert Daten in der Cloud durch Ausführen eines Spark-Programms in einem Azure HDInsight Spark-Cluster.
author: dcstwh
ms.author: weetok
manager: anandsub
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/12/2017
ms.openlocfilehash: 9fe0efcb14c1da84ca5284d55366b6c7b79727b1
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96497009"
---
# <a name="powershell-script---transform-data-in-cloud-using-azure-data-factory"></a>PowerShell-Skript: Transformieren von Daten in der Cloud mithilfe von Azure Data Factory

Dieses PowerShell-Beispielskript erstellt eine Pipeline, die Daten in der Cloud durch Ausführen eines Spark-Programms in einem Azure HDInsight Spark-Cluster transformiert. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Voraussetzungen
* **Azure Storage-Konto**. Erstellen Sie ein Python-Skript und eine Eingabedatei, und laden Sie diese in Azure Storage hoch. Die Ausgabe des Spark-Programms wird in diesem Storage-Konto gespeichert. Der bedarfsgesteuerte Spark-Cluster verwendet dieses Storage-Konto als primären Speicher.  

### <a name="upload-python-script-to-your-blob-storage-account"></a>Hochladen eines Python-Skripts in Ihr Blob Storage-Konto
1. Erstellen Sie eine Python-Datei mit dem Namen **WordCount_Spark.py** und dem folgenden Inhalt: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. Ersetzen Sie **&lt;storageAccountName&gt;** durch den Namen Ihres Azure Storage-Kontos. Speichern Sie dann die Datei. 
3. Erstellen Sie in Azure Blob Storage einen Container mit dem Namen **adftutorial**, falls dieser noch nicht vorhanden ist. 
4. Erstellen Sie einen Ordner mit dem Namen **spark**.
5. Erstellen Sie unterhalb des Ordners **spark** einen Unterordner mit dem Namen **script**. 
6. Laden Sie die Datei **WordCount_Spark.py** in den Unterordner **script** hoch. 


### <a name="upload-the-input-file"></a>Hochladen der Eingabedatei
1. Erstellen Sie eine Datei mit dem Namen **minecraftstory.txt** und etwas Text darin. Das Spark-Programm zählt die Wörter in diesem Text. 
2. Erstellen Sie im Blobcontainer im Ordner `spark` einen Unterordner mit dem Namen `inputfiles`. 
3. Laden Sie `minecraftstory.txt` in den Unterordner `inputfiles` hoch. 

## <a name="sample-script"></a>Beispielskript
> [!IMPORTANT]
> Dieses Skript erstellt JSON-Dateien, die Data Factory-Entitäten (verknüpften Dienst, Dataset und Pipeline) auf der Festplatte im Ordner „c:\“ definieren.

[!code-powershell[main](../../../powershell_scripts/data-factory/transform-data-using-spark/transform-data-using-spark.ps1 "Transform data using Spark")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Nach der Ausführung des Beispielskripts können Sie den folgenden Befehl ausführen, um die Ressourcengruppe und alle damit verbundenen Ressourcen zu entfernen:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Führen Sie den folgenden Befehl aus, um die Data Factory aus der Ressourcengruppe zu entfernen: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle:

| Get-Help | Notizen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Erstellt eine Ressourcengruppe, in der alle Ressourcen gespeichert sind. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Erstellen einer Data Factory. |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/set-Azdatafactoryv2linkedservice) | Erstellt einen verknüpften Dienst in der Data Factory. Ein verknüpfter Dienst verbindet einen Datenspeicher oder ein Compute mit einer Data Factory. |
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/set-Azdatafactoryv2pipeline) | Erstellt eine Pipeline in der Data Factory. Eine Pipeline enthält eine oder mehrere Aktivitäten zur Ausführung eines bestimmten Vorgangs. In dieser Pipeline transformiert eine Spark-Aktivität Daten durch Ausführen eines Programms in einem Azure HDInsight Spark-Cluster. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/invoke-Azdatafactoryv2pipeline) | Erstellt eine Ausführung für die Pipeline. Soll heißen, führt die Pipeline aus. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Ruft Details zur Ausführung der Aktivität (Aktivitätsausführung) in der Pipeline ab. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |
|||

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](/powershell/).

Zusätzliche PowerShell-Skriptbeispiele für Azure Data Factory finden Sie unter [Azure PowerShell-Beispiele für Azure Data Factory](../samples-powershell.md).