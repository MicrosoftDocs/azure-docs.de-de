---
title: Verwenden von Apache Hadoop Hive mit Curl in HDInsight – Azure
description: Erfahren Sie, wie Sie Apache Pig-Aufträge mithilfe von Curl remote an Azure HDInsight übermitteln.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/06/2020
ms.openlocfilehash: 956406ec5ac99be5973f1928bbb89db10e68b339
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000493"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>Ausführen von Apache Hive-Abfragen mit Apache Hadoop in HDInsight mit REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Erfahren Sie, wie Sie die WebHCat-REST-API verwenden, um Apache Hive-Abfragen mit Apache Hadoop in Azure HDInsight-Clustern auszuführen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Apache Hadoop-Cluster in HDInsight. Weitere Informationen finden Sie unter [Erste Schritte mit HDInsight unter Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Ein REST-Client. In diesem Dokument wird [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) unter Windows PowerShell und [Curl](https://curl.haxx.se/) unter [Bash](/windows/wsl/install-win10) verwendet.

* Wenn Sie Bash verwenden, benötigen Sie außerdem jq, einen Befehlszeilen-JSON-Prozessor.  Siehe [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

## <a name="base-uri-for-rest-api"></a>Basis-URI für Rest-API

Der Basis-URI (Uniform Resource Identifier) für die REST-API unter HDInsight lautet `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME`, wobei `CLUSTERNAME` der Name Ihres Clusters ist.  Bei Clusternamen in URIs wird die **Groß-/Kleinschreibung** beachtet.  Beim Clusternamen im vollqualifizierten Domänennamen (FQDN) innerhalb des URI (`CLUSTERNAME.azurehdinsight.net`) wird die Groß-/Kleinschreibung nicht beachtet, bei anderen Vorkommen im URI dagegen schon.

## <a name="authentication"></a>Authentifizierung

Wenn Sie cURL oder eine andere REST-Kommunikation mit WebHCat verwenden, müssen Sie die Anforderungen authentifizieren, indem Sie den Benutzernamen und das Kennwort des Administrators des HDInsight-Clusters bereitstellen. Die REST-API wird durch [Standardauthentifizierung](https://en.wikipedia.org/wiki/Basic_access_authentication)gesichert. Stellen Sie Anforderungen immer über HTTPS (Secure HTTP), um sicherzustellen, dass Ihre Anmeldeinformationen sicher an den Server gesendet werden.

### <a name="setup-preserve-credentials"></a>Setup (Anmeldeinformationen speichern)

Speichern Sie Ihre Anmeldeinformationen, um zu vermeiden, dass Sie sie für jedes Beispiel neu eingeben müssen.  Der Clustername wird in einem separaten Schritt gespeichert.

**A. Bash**  
Bearbeiten Sie das unten angegebene Skript, indem Sie `PASSWORD` durch Ihr Kennwort ersetzen.  Geben Sie anschließend den Befehl ein.

```bash
export password='PASSWORD'
```  

**B. PowerShell** Führen Sie den folgenden Code aus, und geben Sie Ihre Anmeldeinformationen im Popupfenster ein:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Identifizieren von Clusternamen mit richtiger Groß-/Kleinschreibung

Die tatsächliche Schreibweise des Clusternamens kann je nach Clustererstellung anders sein als erwartet.  Mit den hier angegebenen Schritten wird die tatsächliche Schreibweise angezeigt und dann für alle späteren Beispiele in einer Variable gespeichert.

Bearbeiten Sie die unten angegebenen Skripts, um `CLUSTERNAME` durch den Namen Ihres Clusters zu ersetzen. Geben Sie anschließend den Befehl ein. (Beim Clusternamen für den FQDN wird die Groß-/Kleinschreibung nicht beachtet.)

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

## <a name="run-a-hive-query"></a>Ausführen einer Hive-Abfrage

1. Verwenden Sie einen der folgenden Befehle, um zu überprüfen, ob Sie eine Verbindung mit Ihrem HDInsight-Cluster herstellen können:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Sie empfangen eine Antwort, die in etwa dem folgenden Text entspricht:

    ```json
    {"status":"ok","version":"v1"}
    ```

    Folgende Parameter werden in diesem Befehl verwendet:

    * `-u`: Der Benutzername und das Kennwort für die Authentifizierung der Anforderung.
    * `-G`: Gibt an, dass diese Anforderung ein GET-Vorgang ist.

1. Der Anfang der URL (`https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`) ist für alle Anforderungen gleich. Der Pfad (`/status`) gibt an, dass die Anforderung einen Status von WebHCat (auch als Templeton bezeichnet) für den Server zurückgeben soll. Sie können mit dem folgenden Befehl auch die Version von Hive anfordern:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Die Anforderung gibt in etwa folgenden Text zurück:

    ```json
    {"module":"hive","version":"1.2.1000.2.6.5.3008-11"}
    ```

1. Verwenden Sie Folgendes, um eine Tabelle namens **log4jLogs** zu erstellen:

    ```bash
    jobid=$(curl -s -u admin:$password -d user.name=admin -d execute="DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$clusterName.azurehdinsight.net/templeton/v1/hive | jq -r .id)
    echo $jobid
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/hive" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    Diese Anforderung verwendet die POST-Methode, die Daten als Teil der Anforderung an die REST-API sendet. Die folgenden Datenwerte werden mit der Anforderung gesendet:

     * `user.name`: Der Benutzer, der den Befehl ausführt.
     * `execute`: Die auszuführenden HiveQL-Anweisungen.
     * `statusdir`: Das Verzeichnis, in das der Status für diesen Auftrag geschrieben wird.

   Diese Anweisungen führen die folgenden Aktionen aus:

   * `DROP TABLE`: Wenn die Tabelle bereits vorhanden ist, wird sie gelöscht.
   * `CREATE EXTERNAL TABLE`: Erstellt eine neue „externe“ Tabelle in Hive. Externe Tabellen speichern nur die Tabellendefinition in Hive. Die Daten verbleiben an ihrem ursprünglichen Speicherort.

     > [!NOTE]  
     > Externe Tabellen sollten Sie verwenden, wenn Sie erwarten, dass die zugrunde liegenden Daten aus einer externen Quelle aktualisiert werden. Das könnte z.B. ein automatisierter Datenupload oder ein anderer MapReduce-Vorgang sein.
     >
     > Durch das Löschen einer externen Tabelle werden **nicht** die Daten, sondern nur die Tabellendefinitionen gelöscht.

   * `ROW FORMAT`: Gibt an, wie die Daten formatiert werden. Die Felder werden in den einzelnen Protokollen durch Leerzeichen getrennt.
   * `STORED AS TEXTFILE LOCATION`: der Speicherort der Daten (das Verzeichnis „example/data“) und die Angabe, dass sie als Text gespeichert werden.
   * `SELECT`: Wählt die Anzahl aller Zeilen aus, bei denen die Spalte **t4** den Wert **[ERROR]** enthält. Mit dieser Anweisung wird der Wert **3** zurückgegeben, da dieser Wert in drei Zeilen enthalten ist.

     > [!NOTE]  
     > Beachten Sie, dass die Leerzeichen zwischen HiveQL-Anweisungen bei Curl durch das Zeichen `+` ersetzt werden. Werte in Anführungszeichen, die ein Leerzeichen enthalten, z. B. das Trennzeichen, dürfen nicht durch `+` ersetzt werden.

      Dieser Befehl gibt eine Auftrags-ID zurück, mit der der Status des Auftrags überprüft werden kann.

1. Verwenden Sie den folgenden Befehl, um den Status des Auftrags zu prüfen:

    ```bash
    curl -u admin:$password -d user.name=admin -G https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobid | jq .status.state
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

    Wenn der Auftrag abgeschlossen ist, wird der Status **ERFOLGREICH** angezeigt.

1. Sobald der Status des Auftrags zu **ERFOLGREICH** wechselt, können Sie die Ergebnisse des Auftrags aus Azure Blob Storage abrufen. Der mit der Abfrage übergebene Parameter `statusdir` enthält den Speicherort der Ausgabedatei. In diesem Fall ist dies `/example/rest`. Diese Adresse speichert die Ausgabe im Verzeichnis `example/curl` im Standardspeicher des Clusters.

    Sie können diese Dateien mithilfe der [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli) auflisten und herunterladen. Weitere Informationen zur Verwendung der Azure-Befehlszeilenschnittstelle mit Azure Storage finden Sie im Dokument [Verwenden der Azure CLI 2.0 mit Azure Storage](../../storage/blobs/storage-quickstart-blobs-cli.md).

## <a name="next-steps"></a>Nächste Schritte

Informationen zu anderen Möglichkeiten, wie Sie mit Hadoop in HDInsight arbeiten können:

* [Verwenden von Apache Hive mit Apache Hadoop in HDInsight](hdinsight-use-hive.md)
* [Verwenden von MapReduce mit Apache Hadoop in HDInsight](hdinsight-use-mapreduce.md)

Weitere Informationen zu der in diesem Artikel verwendeten REST-API finden Sie in der [WebHCat-Referenz](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).