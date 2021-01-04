---
title: Verwenden von Apache Hive mit PowerShell in HDInsight – Azure
description: Ausführen von Apache Hive-Abfragen mit Apache Hadoop in Azure HDInsight mithilfe von PowerShell
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: 013eb8215064173e002aea23043bc2e9ba92b525
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545241"
---
# <a name="run-apache-hive-queries-using-powershell"></a>Ausführen von Apache Hive-Abfragen mit PowerShell

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Dieses Dokument enthält ein Beispiel für die Verwendung von Azure PowerShell zum Ausführen von Apache Hive-Abfragen in einem Apache Hadoop-Cluster in HDInsight.

> [!NOTE]  
> Dieses Dokument bietet keine detaillierte Beschreibung dazu, wie die in diesem Beispiel verwendeten HiveQL-Anweisungen vorgehen. Informationen zur HiveQL, die in diesem Beispiel verwendet wird, finden Sie unter [Verwenden von Apache Hive mit Apache Hadoop in HDInsight](hdinsight-use-hive.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Apache Hadoop-Cluster in HDInsight. Weitere Informationen finden Sie unter [Erste Schritte mit HDInsight unter Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Das PowerShell-[Az-Modul](/powershell/azure/) ist installiert.

## <a name="run-a-hive-query"></a>Ausführen einer Hive-Abfrage

Azure PowerShell stellt *cmdlets* bereit, mit denen Sie Hive-Abfragen in HDInsight remote ausführen können. Intern führen die Cmdlets REST-Aufrufe von [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) im HDInsight-Cluster durch.

Die folgenden Cmdlets werden zum Ausführen der Hive-Abfragen auf einem HDInsight-Remotecluster verwendet:

* `Connect-AzAccount`: Authentifiziert Azure PowerShell für Ihr Azure-Abonnement.
* `New-AzHDInsightHiveJobDefinition`: Erstellt mithilfe der angegebenen HiveQL-Anweisungen eine *Auftragsdefinition*.
* `Start-AzHDInsightJob`: Sendet die Auftragsdefinition an HDInsight und startet den Auftrag. Ein *Auftragsobjekt* wird zurückgegeben.
* `Wait-AzHDInsightJob`: Verwendet das Auftragsobjekt, um den Status des Auftrags zu prüfen. Es wird gewartet, bis der Auftrag abgeschlossen oder die Wartezeit überschritten ist.
* `Get-AzHDInsightJobOutput`: Wird verwendet, um die Ausgabe des Auftrags abzurufen.
* `Invoke-AzHDInsightHiveJob`: Wird verwendet, um HiveQL-Anweisungen auszuführen. Mit diesem Cmdlet wird die Abfrage vollständig blockiert, und anschließend werden die Ergebnisse zurückgegeben.
* `Use-AzHDInsightCluster`: Legt den aktuellen Cluster fest, der für den Befehl `Invoke-AzHDInsightHiveJob` verwendet werden soll.

Die folgenden Schritte veranschaulichen, wie diese Cmdlets zum Ausführen eines Auftrags auf dem HDInsight-Cluster verwendet werden:

1. Speichern Sie den folgenden Code mithilfe eines Editors als `hivejob.ps1`.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Öffnen Sie eine neue **Azure PowerShell** -Befehlsaufforderung. Navigieren Sie zum Speicherort der Datei `hivejob.ps1`, und verwenden Sie folgenden Befehl zum Ausführen des Skripts:

    ```azurepowershell
    .\hivejob.ps1
    ```

    Beim Ausführen des Skripts werden Sie aufgefordert, den Clusternamen und die Anmeldeinformationen für das HTTPS-/Clusteradministratorkonto einzugeben. Sie können auch zur Anmeldung bei Ihrem Azure-Abonnement aufgefordert werden.

3. Nachdem der Auftrag abgeschlossen ist, werden Informationen zurückgegeben, die folgendem Text ähneln:

    ```output
    Display the standard output...
    2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
    2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
    2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id
    ```

4. Wie bereits erwähnt, kann `Invoke-Hive` dazu verwendet werden, eine Abfrage auszuführen und auf die Antwort zu warten. Mit dem folgenden Skript können Sie veranschaulichen, wie „Invoke-Hive“ funktioniert:

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    Die Ausgabe sieht in etwa wie folgt aus:

    ```output
    2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
    2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
    2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id
    ```

   > [!NOTE]  
   > Für längere HiveQL-Abfragen können Sie Azure PowerShell **Here-Strings** -Cmdlet oder HiveQL-Skriptdateien verwenden. Der folgende Codeausschnitt zeigt, wie Sie eine HiveQL-Skriptdatei mit dem Cmdlet `Invoke-Hive` ausführen können. Die HiveQL-Skriptdatei muss auf wasbs:// hochgeladen werden.
   >
   > `Invoke-AzHDInsightHiveJob -File "wasbs://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > Weitere Informationen zu **Here-Strings** finden Sie unter [HERE-STRINGS](/powershell/module/microsoft.powershell.core/about/about_quoting_rules#here-strings).

## <a name="troubleshooting"></a>Problembehandlung

Wenn keine Informationen zurückgegeben werden, wenn der Auftrag abgeschlossen ist, zeigen Sie die Fehlerprotokolle an. Um Fehlerinformationen für diesen Auftrag anzuzeigen, fügen Sie Folgendes am Ende der Datei `hivejob.ps1` hinzu. Speichern Sie die Datei anschließend, und führen Sie sie dann erneut aus.

```powershell
# Print the output of the Hive job.
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Mit diesem Cmdlet werden die Informationen zurückgegeben, die bei der Auftragsverarbeitung in STDERR geschrieben wurden.

## <a name="summary"></a>Zusammenfassung

Wie Sie sehen können, bietet Azure PowerShell eine einfache Möglichkeit, um Hive-Abfragen auf einem HDInsight-Cluster auszuführen, den Auftragsstatus zu überwachen und die Ausgabe abzurufen.

## <a name="next-steps"></a>Nächste Schritte

Allgemeine Informationen zu Hive in HDInsight:

* [Verwenden von Apache Hive mit Apache Hadoop in HDInsight](hdinsight-use-hive.md)

Informationen zu anderen Möglichkeiten, wie Sie mit Hadoop in HDInsight arbeiten können:

* [Verwenden von MapReduce mit Apache Hadoop in HDInsight](hdinsight-use-mapreduce.md)
