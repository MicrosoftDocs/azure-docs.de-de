---
title: 'Verwenden von MapReduce und PowerShell mit Apache Hadoop: Azure HDInsight'
description: Hier erfahren Sie, wie MapReduce-Aufträge mithilfe von PowerShell mit Apache Hadoop in HDInsight remote ausgeführt werden.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/08/2020
ms.openlocfilehash: 8b5f5a9fb8ddd25f750b19044bac7253bbff2f98
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92545207"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-powershell"></a>Ausführen von MapReduce-Aufträgen mit Apache Hadoop in HDInsight mithilfe von PowerShell

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Dieses Dokument enthält ein Beispiel zur Verwendung von Azure PowerShell zum Ausführen eines MapReduce-Auftrags in einem Hadoop-Cluster in HDInsight.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Apache Hadoop-Cluster in HDInsight. Siehe [Erstellen von Apache Hadoop-Clustern im Azure-Portal](../hdinsight-hadoop-create-linux-clusters-portal.md).

* Das PowerShell-[Az-Modul](/powershell/azure/) ist installiert.

## <a name="run-a-mapreduce-job"></a>Ausführen eines MapReduce-Auftrags

Azure PowerShell stellt *Cmdlets* bereit, mit denen Sie MapReduce-Aufträge in HDInsight remote ausführen können. Intern sendet PowerShell REST-Aufrufe an die auf dem HDInsight-Cluster ausgeführte Anwendung [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (früher Templeton genannt).

Die folgenden Cmdlets werden zum Ausführen der MapReduce-Aufträge in einem HDInsight-Remotecluster verwendet.

|Cmdlet | BESCHREIBUNG |
|---|---|
|Connect-AzAccount|Authentifiziert Azure PowerShell für Ihr Azure-Abonnement.|
|New-AzHDInsightMapReduceJobDefinition|Erstellt aus den angegebenen MapReduce-Informationen eine neue *Auftragsdefinition* .|
|Start-AzHDInsightJob|Sendet die Auftragsdefinition an HDInsight und startet den Auftrag. Ein *Auftragsobjekt* wird zurückgegeben.|
|Wait-AzHDInsightJob|Verwendet das Auftragsobjekt, um den Status des Auftrags zu prüfen. Es wird gewartet, bis der Auftrag abgeschlossen oder die Wartezeit überschritten ist.|
|Get-AzHDInsightJobOutput|Wird verwendet, um die Ausgabe des Auftrags abzurufen.|

Die folgenden Schritte veranschaulichen, wie diese Cmdlets zum Ausführen eines Auftrags in einem HDInsight-Cluster verwendet werden.

1. Speichern Sie den folgenden Code in einem Editor in einer Datei namens **mapreducejob.ps1** .

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. Öffnen Sie eine neue **Azure PowerShell** -Befehlsaufforderung. Navigieren Sie zum Speicherort der Datei **mapreducejob.ps1** , und verwenden Sie folgenden Befehl zum Ausführen des Skripts.

    ```azurepowershell
    .\mapreducejob.ps1
    ```

    Wenn Sie das Skript ausführen, werden Sie dazu aufgefordert, den Namen des HDInsight-Clusters und die Anmeldedaten für den Cluster einzugeben. Sie werden außerdem möglicherweise dazu aufgefordert, sich bei Ihrem Azure-Abonnement zu authentifizieren.

3. Nach Abschluss des Auftrags erhalten Sie eine Ausgabe ähnlich der folgenden:

    ```output
    Cluster         : CLUSTERNAME
    ExitCode        : 0
    Name            : wordcount
    PercentComplete : map 100% reduce 100%
    Query           :
    State           : Completed
    StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
    SubmissionTime  : 12/5/2014 8:34:09 PM
    JobId           : job_1415949758166_0071
    ```

    Diese Ausgabe gibt an, dass der Auftrag erfolgreich abgeschlossen wurde.

    > [!NOTE]  
    > Wenn sich für **ExitCode** ein anderer Wert als 0 ergibt, finden Sie weitere Informationen unter [Troubleshooting](#troubleshooting).

    In diesem Beispiel werden außerdem die heruntergeladenen Dateien in der Datei **output.txt** in dem Verzeichnis gespeichert, von dem aus Sie das Skript ausführen.

### <a name="view-output"></a>Anzeigen der Ausgabe

Öffnen Sie die Datei **output.txt** in einem Texteditor, um die von dem Auftrag erzeugten Wörter und Zählerstände anzuzeigen.

> [!NOTE]  
> Die Ausgabedateien eines MapReduce-Auftrags sind unveränderlich. Wenn Sie dieses Beispiel erneut ausführen, müssen Sie daher den Namen der Ausgabedatei ändern.

## <a name="troubleshooting"></a>Problembehandlung

Wenn keine Informationen zurückgegeben werden, wenn der Auftrag abgeschlossen ist, zeigen Sie die Fehlermeldungen für den Auftrag an. Um Fehlerinformationen für diesen Auftrag anzuzeigen, fügen Sie folgenden Befehl am Ende der Datei **mapreducejob.ps1** hinzu. Speichern Sie anschließend die Datei, und führen Sie das Skript erneut aus.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Mit diesem Cmdlet werden die Informationen zurückgegeben, die bei der Auftragsverarbeitung in STDERR geschrieben wurden.

## <a name="next-steps"></a>Nächste Schritte

Wie Sie sehen können, bietet Azure PowerShell eine einfache Möglichkeit, um MapReduce-Aufträge auf einem HDInsight-Cluster auszuführen, den Auftragsstatus zu überwachen und die Ausgabe abzurufen. Informationen zu anderen Möglichkeiten, wie Sie mit Hadoop in HDInsight arbeiten können:

* [Verwenden von MapReduce mit Hadoop in HDInsight](hdinsight-use-mapreduce.md)
* [Verwenden von Apache Hive mit Apache Hadoop in HDInsight](hdinsight-use-hive.md)