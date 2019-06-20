---
title: Verwenden von MapReduce und Curl mit Hadoop in HDInsight – Azure
description: Erfahren Sie, wie Sie MapReduce-Aufträge mit Apache Hadoop in HDInsight mithilfe von Curl remote ausführen.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: e4968310459097fc6a00f7c453846fe61726c3d5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64716116"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>Ausführen von MapReduce-Aufträgen mit Apache Hadoop in HDInsight mithilfe von REST

Erfahren Sie, wie mithilfe der Apache Hive WebHCat-REST-API MapReduce-Aufträge auf einem Apache Hadoop-Cluster in HDInsight ausführen. Curl dient zum Veranschaulichen der Interaktion mit HDInsight über unformatierte HTTP-Anforderungen zum Ausführen von MapReduce-Aufträgen.

> [!NOTE]  
> Wenn Sie bereits mit der Verwendung von Linux-basierten Hadoop-Servern vertraut sind, nicht jedoch mit HDInsight, lesen Sie den Artikel [Informationen zu Linux-basiertem Apache Hadoop in HDInsight](../hdinsight-hadoop-linux-information.md).


## <a id="prereq"></a>Voraussetzungen

* Hadoop in einem HDInsight-Cluster
* Windows PowerShell oder [cURL](https://curl.haxx.se/) und [jq](https://stedolan.github.io/jq/)

## <a id="curl"></a>Ausführen eines MapReduce-Auftrags

> [!NOTE]  
> Wenn Sie Curl oder eine andere REST-Kommunikation mit WebHCat verwenden, müssen Sie die Anforderungen authentifizieren, indem Sie den Benutzernamen und das Kennwort des Administrators des HDInsight-Clusters bereitstellen. Sie müssen den Clusternamen als Teil des URI verwenden, der zum Senden der Anforderungen an den Server genutzt wird.
>
> Die REST-API wird durch [Standardauthentifizierung](https://en.wikipedia.org/wiki/Basic_access_authentication)gesichert. Sie sollten Anforderungen immer über HTTPS stellen, um sicherzustellen, dass Ihre Anmeldeinformationen sicher an den Server gesendet werden.

1. Um die Clusteranmeldung festzulegen, die von den Skripts in diesem Dokument verwendet wird, verwenden Sie einen der folgenden Befehle:

    ```bash
    read -p "Enter your cluster login account name: " LOGIN
    ```

    ```powershell
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login name and password"
    ```

2. Um den Namen des Clusters festzulegen, verwenden Sie einen der folgenden Befehle:

    ```bash
    read -p "Enter the HDInsight cluster name: " CLUSTERNAME
    ```

    ```powershell
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    ```

3. Verwenden Sie den folgenden Befehl in einer Befehlszeile, um zu überprüfen, ob Sie die Verbindung zum HDInsight-Cluster herstellen können:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clustername.azurehdinsight.net/templeton/v1/status" `
        -Credential $creds `
        -UseBasicParsing
    $resp.Content
    ```

    Sie erhalten eine Antwort, die in etwa dem folgenden JSON entspricht:

        {"status":"ok","version":"v1"}

    Folgende Parameter werden in diesem Befehl verwendet:

   * **-u**: Gibt den Benutzernamen und das Kennwort für die Authentifizierung der Anforderung an
   * **-G**: Gibt an, dass dieser Vorgang eine GET-Anforderung ist

   Der Anfang des URI ( **https://CLUSTERNAME.azurehdinsight.net/templeton/v1** ) ist für alle Anforderungen gleich.

4. Verwenden Sie den folgenden Befehl, um einen MapReduce-Auftrag zu übermitteln.

    ```bash
    JOBID=`curl -u $LOGIN -d user.name=$LOGIN -d jar=/example/jars/hadoop-mapreduce-examples.jar -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/output https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/mapreduce/jar | jq .id`
    echo $JOBID
    ```

    ```powershell
    $reqParams = @{}
    $reqParams."user.name" = "admin"
    $reqParams.jar = "/example/jars/hadoop-mapreduce-examples.jar"
    $reqParams.class = "wordcount"
    $reqParams.arg = @()
    $reqParams.arg += "/example/data/gutenberg/davinci.txt"
    $reqparams.arg += "/example/data/output"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/mapreduce/jar" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    Das Ende des URIs (/mapreduce/jar) weist WebHCat darauf hin, dass diese Anforderung einen MapReduce-Auftrag aus einer Klasse in einer JAR-Datei startet. Folgende Parameter werden in diesem Befehl verwendet:

   * **-d**: `-G` wird nicht verwendet, daher verwendet die Anforderung standardmäßig die POST-Methode. `-d` gibt die Datenwerte an, die mit der Anforderung gesendet werden.
     * **user.name**: Der Benutzer, der den Befehl ausführt
     * **jar**: Der Speicherort der JAR-Datei, die die auszuführende Klasse enthält
     * **class**: Die Klasse, die die MapReduce-Logik enthält
     * **arg**: Die Argumente, die an den MapReduce-Auftrag übergeben werden, in diesem Fall die Eingabetextdatei und das Verzeichnis für die Ausgabe

   Dieser Befehl sollte eine Auftrags-ID zurückgeben, mit der der Status des Auftrags überprüft werden kann:

       job_1415651640909_0026

5. Verwenden Sie den folgenden Befehl, um den Status des Auftrags zu prüfen:

    ```bash
    curl -G -u $LOGIN -d user.name=$LOGIN https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/$JOBID | jq .status.state
    ```

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing
    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

    Wenn der Auftrag abgeschlossen ist, wird der Status `SUCCEEDED` angezeigt.

   > [!NOTE]  
   > Diese Curl-Anforderung gibt ein JSON-Dokument mit Informationen zum Auftrag zurück. Mithilfe von „jq“ wird nur der Statuswert abgerufen.

6. Sobald der Status des Auftrags in `SUCCEEDED` wechselt, können Sie die Ergebnisse des Auftrags aus Azure Blob Storage abrufen. Der mit der Abfrage übergebene Parameter `statusdir` enthält den Speicherort der Ausgabedatei. In diesem Beispiel ist dies `/example/curl`. Diese Adresse speichert die Ausgabe des Auftrags unter `/example/curl` im Standardspeicher des Clusters.

Sie können diese Dateien mithilfe der [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/install-azure-cli) auflisten und herunterladen. Weitere Informationen zur Verwendung von Blobs über die Azure-Befehlszeilenschnittstelle finden Sie im Artikel [Verwenden der Azure-Befehlszeilenschnittstelle mit Azure-Speicher](../../storage/common/storage-azure-cli.md#create-and-manage-blobs).

## <a id="nextsteps"></a>Nächste Schritte

Allgemeine Informationen zu MapReduce-Aufträgen in HDInsight:

* [Verwenden von MapReduce mit Apache Hadoop in HDInsight](hdinsight-use-mapreduce.md)

Informationen zu anderen Möglichkeiten, wie Sie mit Hadoop in HDInsight arbeiten können:

* [Verwenden von Apache Hive mit Apache Hadoop in HDInsight](hdinsight-use-hive.md)
* [Verwenden von Apache Pig mit Apache Hadoop in HDInsight](hdinsight-use-pig.md)

Weitere Informationen zur REST-Schnittstelle, die in diesem Artikel verwendet wird, finden Sie in der [WebHCat-Referenz](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).
