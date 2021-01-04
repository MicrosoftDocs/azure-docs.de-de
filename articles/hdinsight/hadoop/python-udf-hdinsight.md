---
title: Python-UDF mit Apache Hive und Apache Pig – Azure HDInsight
description: Erfahren Sie, wie Sie benutzerdefinierte Python-Funktionen mit Apache Hive und Apache Pig in HDInsight, dem Apache Hadoop-Technologiestapel in Azure, verwenden können.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/15/2019
ms.custom: H1Hack27Feb2017,hdinsightactive, devx-track-python
ms.openlocfilehash: 0179fd10e75af0ced55b4bb41f9525dc26b3efe5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023073"
---
# <a name="use-python-user-defined-functions-udf-with-apache-hive-and-apache-pig-in-hdinsight"></a>Verwenden benutzerdefinierter Python-Funktionen mit Apache Hive und Apache Pig in HDInsight

Erfahren Sie, wie Sie benutzerdefinierte Python-Funktionen (User-Defined Functions, UDFs) mit Apache Hive und Apache Pig in Apache Hadoop in Azure HDInsight verwenden.

## <a name="python-on-hdinsight"></a><a name="python"></a>Python in HDInsight

Python 2.7 wird in der Version HDInsight 3.0 und höher standardmäßig installiert. Apache Hive kann mit dieser Version von Python zur Streamverarbeitung verwendet werden. Die Streamverarbeitung nutzt STDOUT und STDIN, um Daten zwischen Hive und der benutzerdefinierten Funktion zu übergeben.

HDInsight enthält außerdem Jython, eine in Java geschriebene Python-Implementierung. Jython wird direkt auf der Java Virtual Machine ausgeführt und verwendet kein Streaming. Jython wird bei Verwendung von Python mit Pig als Python-Interpreter empfohlen.

## <a name="prerequisites"></a>Voraussetzungen

* **Einen Hadoop-Cluster in HDInsight**. Weitere Informationen finden Sie unter [Erste Schritte mit HDInsight unter Linux](apache-hadoop-linux-tutorial-get-started.md).
* **SSH-Client**. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).
* Das [URI-Schema](../hdinsight-hadoop-linux-information.md#URI-and-scheme) für Ihren primären Clusterspeicher. Dies ist `wasb://` für Azure Storage, `abfs://` für Azure Data Lake Storage Gen2 oder adl:// für Azure Data Lake Storage Gen1. Wenn die sichere Übertragung für Azure Storage aktiviert ist, lautet der URI wasbs://.  Siehe auch [Vorschreiben einer sicheren Übertragung in Azure Storage](../../storage/common/storage-require-secure-transfer.md).
* **Mögliche Änderungen an der Speicherkonfiguration**.  Wenn Sie ein Speicherkonto vom Typ `BlobStorage` verwenden, helfen Ihnen die Informationen unter [Speicherkonfiguration](#storage-configuration) weiter.
* Optional.  Wenn Sie PowerShell verwenden möchten, müssen Sie das [Az-Modul](/powershell/azure/new-azureps-module-az) installieren.

> [!NOTE]  
> Das in diesem Artikel verwendete Speicherkonto war vom Typ „Azure Storage mit aktivierter [sicherer Übertragung](../../storage/common/storage-require-secure-transfer.md)“, und deshalb wird im gesamten Artikel `wasbs` verwendet.

## <a name="storage-configuration"></a>Speicherkonfiguration

Es ist keine Aktion erforderlich, wenn das verwendete Speicherkonto vom Typ `Storage (general purpose v1)` oder `StorageV2 (general purpose v2)` ist.  Der Prozess in diesem Artikel wird mindestens zu einer Ausgabe in `/tezstaging` führen.  In einer Hadoop-Standardkonfiguration ist `/tezstaging` in der Konfigurationsvariablen `fs.azure.page.blob.dir` in der Datei `core-site.xml` des `HDFS`-Diensts enthalten.  Diese Konfiguration bewirkt, dass es sich bei der Ausgabe im Verzeichnis um Seitenblobs handelt. Diese werden für Speicherkonten vom Typ `BlobStorage` aber nicht unterstützt.  Entfernen Sie `/tezstaging` aus der Konfigurationsvariablen `fs.azure.page.blob.dir`, damit Sie `BlobStorage` im Rahmen dieses Artikels verwenden können.  Zugriff auf die Konfiguration besteht über die [Ambari-Benutzeroberfläche](../hdinsight-hadoop-manage-ambari.md).  Andernfalls erhalten Sie diese Fehlermeldung: `Page blob is not supported for this account type.`

> [!WARNING]  
> Für die Schritte in diesem Dokument gelten die folgenden Annahmen:  
>
> * Sie erstellen die Python-Skripts in der lokalen Entwicklungsumgebung.
> * Sie laden die Skripts entweder mit dem `scp`-Befehl oder dem bereitgestellten PowerShell-Skript in HDInsight hoch.
>
> Gehen Sie wie folgt vor, wenn Sie die [Azure Cloud Shell (Bash)](../../cloud-shell/overview.md) für die Verwendung von HDInsight verwenden möchten:
>
> * die Skripts in der Cloud Shell-Umgebung erstellen.
> * `scp` zum Hochladen der Dateien aus Cloud Shell in HDInsight verwenden.
> * `ssh` aus Cloud Shell zum Herstellen einer Verbindung mit HDInsight verwenden und die Beispiele ausführen.

## <a name="apache-hive-udf"></a><a name="hivepython"></a>Benutzerdefinierte Apache Hive-Funktion

Python kann mittels der Hive QL-`TRANSFORM`-Anweisung als UDF von Hive aus verwendet werden. Beispielsweise ruft die folgende HiveQL-Anweisung die im standardmäßigen Azure Storage-Konto für den Cluster gespeicherte `hiveudf.py`-Datei auf.

```hiveql
add file wasbs:///hiveudf.py;

SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'python hiveudf.py' AS
    (clientid string, phoneLabel string, phoneHash string)
FROM hivesampletable
ORDER BY clientid LIMIT 50;
```

Das Beispiel bewirkt Folgendes:

1. Die `add file`-Anweisung am Anfang der Datei fügt die `hiveudf.py`-Datei dem verteilten Cache hinzu, sodass sie von allen Knoten im Cluster aus zugänglich ist.
2. Die `SELECT TRANSFORM ... USING`-Anweisung wählt Daten aus der `hivesampletable` aus. Sie übergibt außerdem die Werte „clientid“, „devicemake“ und „devicemodel“ an das `hiveudf.py`-Skript .
3. Die `AS`-Klausel beschreibt die von `hiveudf.py` zurückgegebenen Felder.

<a name="streamingpy"></a>

### <a name="create-file"></a>Datei erstellen

Erstellen Sie in Ihrer Entwicklungsumgebung eine Textdatei namens `hiveudf.py`. Fügen Sie der Datei den folgenden Code als Inhalt hinzu:

```python
#!/usr/bin/env python
import sys
import string
import hashlib

while True:
    line = sys.stdin.readline()
    if not line:
        break

    line = string.strip(line, "\n ")
    clientid, devicemake, devicemodel = string.split(line, "\t")
    phone_label = devicemake + ' ' + devicemodel
    print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])
```

Dieses Skript führt folgende Aktionen aus:

1. Eine Datenzeile wird aus STDIN gelesen.
2. Das nachfolgende Zeilenumbruchzeichen wird mit `string.strip(line, "\n ")` entfernt.
3. Bei der Streamverarbeitung enthält eine einzelne Zeile alle Werte, jeweils getrennt durch ein Tabulatorzeichen. Deshalb kann `string.split(line, "\t")` zum Unterteilen der Eingabe bei jedem Tabstopp verwendet werden, sodass nur die Felder zurückgeben werden.
4. Wenn die Verarbeitung abgeschlossen ist, muss die Ausgabe als eine Zeile nach STDOUT geschrieben werden mit einem Tabulator zwischen jedem Feld. Beispiel: `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])`.
5. Die `while`-Schleife wird wiederholt, bis keine `line` mehr gelesen wird.

Die Skriptausgabe ist eine Verkettung der Eingabewerte für `devicemake` und `devicemodel` und ein Hash der verketteten Werte.

### <a name="upload-file-shell"></a>Hochladen einer Datei (Shell)

Ersetzen Sie `sshuser` in den folgenden Befehlen durch den tatsächlichen Benutzernamen, falls dieser abweicht.  Ersetzen Sie `mycluster` durch den tatsächlichen Clusternamen.  Stellen Sie sicher, dass das Arbeitsverzeichnis das Verzeichnis ist, in dem sich die Datei befindet.

1. Verwenden Sie `scp` , um die Dateien in Ihren HDInsight-Cluster zu kopieren. Bearbeiten Sie den folgenden Befehl, und geben Sie ihn ein:

    ```cmd
    scp hiveudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Verwenden Sie SSH zum Herstellen einer Verbindung mit dem Cluster.  Bearbeiten Sie den folgenden Befehl, und geben Sie ihn ein:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Fügen Sie die Python-Dateien, die zuvor hochgeladen wurden, in der SSH-Sitzung dem Speicher für den Cluster hinzu.

    ```bash
    hdfs dfs -put hiveudf.py /hiveudf.py
    ```

### <a name="use-hive-udf-shell"></a>Verwenden der Hive-UDF (Shell)

1. Verwenden Sie den folgenden Befehl in Ihrer geöffneten SSH-Sitzung, um eine Verbindung mit Hive herzustellen:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

    Dieser Befehl startet den Beeline-Client.

2. Geben Sie bei der Eingabeaufforderung `0: jdbc:hive2://headnodehost:10001/>` folgende Abfrage ein:

   ```hive
   add file wasbs:///hiveudf.py;
   SELECT TRANSFORM (clientid, devicemake, devicemodel)
       USING 'python hiveudf.py' AS
       (clientid string, phoneLabel string, phoneHash string)
   FROM hivesampletable
   ORDER BY clientid LIMIT 50;
   ```

3. Nach Eingabe der letzten Zeile sollte der Auftrag gestartet werden. Nach Abschluss des Auftrags wird eine Ausgabe ähnlich der folgenden zurückgegeben:

    ```output
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100041    RIM 9650    d476f3687700442549a83fac4560c51c
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
    ```

4. Geben Sie zum Beenden von Beeline den folgenden Befehl ein:

    ```hive
    !q
    ```

### <a name="upload-file-powershell"></a>Hochladen einer Datei (PowerShell)

PowerShell kann auch zur Remoteausführung von Hive-Abfragen verwendet werden. Stellen Sie sicher, dass das Arbeitsverzeichnis das Verzeichnis ist, in dem sich die Datei `hiveudf.py` befindet.  Verwenden Sie das folgende PowerShell-Skript zum Ausführen einer Hive-Abfrage, für die das `hiveudf.py`-Skript verwendet wird:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Revise file path as needed
$pathToStreamingFile = ".\hiveudf.py"

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToStreamingFile `
    -Blob "hiveudf.py" `
    -Container $container `
    -Context $context
```

> [!NOTE]  
> Weitere Informationen zum Hochladen von Dateien finden Sie im Dokument [Hochladen von Daten für Apache Hadoop-Aufträge in HDInsight](../hdinsight-upload-data.md).

#### <a name="use-hive-udf"></a>Verwenden der Hive-UDF

```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"

$HiveQuery = "add file wasbs:///hiveudf.py;" +
                "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
                "USING 'python hiveudf.py' AS " +
                "(clientid string, phoneLabel string, phoneHash string) " +
                "FROM hivesampletable " +
                "ORDER BY clientid LIMIT 50;"

# Create Hive job object
$jobDefinition = New-AzHDInsightHiveJobDefinition `
    -Query $HiveQuery

# For status bar updates
$activity="Hive query"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting query..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting on query to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -JobId $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
   -Clustername $clusterName `
   -JobId $job.JobId `
   -HttpCredential $creds `
   -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

Die Ausgabe für den **Hive**-Auftrag sollte ungefähr folgendem Beispiel entsprechen:

```output
100041    RIM 9650    d476f3687700442549a83fac4560c51c
100041    RIM 9650    d476f3687700442549a83fac4560c51c
100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
100042    Apple iPhone 4.2.x    375ad9a0ddc4351536804f1d5d0ea9b9
```

## <a name="apache-pig-udf"></a><a name="pigpython"></a>Benutzerdefinierte Apache Pig-Funktion

Ein Python-Skript kann mit der `GENERATE`-Anweisung von Pig aus als UDF verwendet werden. Sie können das Skript entweder mit Jython oder C-Python ausführen.

* Jython wird auf der JVM ausgeführt und kann nativ von Pig aufgerufen werden.
* C-Python ist ein externer Prozess, sodass die Daten aus Pig auf der JVM an das Skript gesendet werden, das in einem Python-Prozess ausgeführt wird. Die Ausgabe des Python-Skripts wird wieder an Pig gesendet.

Verwenden Sie zum Angeben des Python-Interpreters `register`, wenn Sie auf das Python-Skript verweisen. In den folgenden Beispielen werden Skripts mit Pig als `myfuncs` registriert:

* **Verwendung von Jython**: `register '/path/to/pigudf.py' using jython as myfuncs;`
* **Verwendung von C Python**: `register '/path/to/pigudf.py' using streaming_python as myfuncs;`

> [!IMPORTANT]  
> Bei Verwendung von Jython kann der Pfad zur Datei „pig_jython“ entweder ein lokaler oder ein „WASBS://“-Pfad sein. Jedoch müssen Sie bei der Verwendung von C-Python auf eine Datei auf dem lokalen Dateisystem des Knotens verweisen, den Sie verwenden, um den Pig-Job zu übermitteln.

Nach der Registrierung ist Pig Latin in diesem Beispiel für beides identisch:

```pig
LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
LOG = FILTER LOGS by LINE is not null;
DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
DUMP DETAILS;
```

Das Beispiel bewirkt Folgendes:

1. Die erste Zeile lädt die Datei mit den Beispieldaten `sample.log` in `LOGS`. Sie definiert außerdem jeden Datensatz als ein `chararray`.
2. Die nächste Zeile filtert etwaige NULL-Werte heraus und speichert das Ergebnis des Vorgangs in `LOG`.
3. Dann folgt eine Iteration über die Datensätze in `LOG`, und mit `GENERATE` wird die Methode `create_structure` aufgerufen, die im Python/Jython-Skript enthalten ist, das als `myfuncs` geladen wird. `LINE` wird für die Übergabe des aktuellen Datensatzes an die Funktion verwendet.
4. Schließlich werden die ausgegebenen Daten mit dem Befehl `DUMP` in STDOUT geschrieben. Dieser Befehl zeigt die Ergebnisse nach Abschluss des Vorgangs.

### <a name="create-file"></a>Datei erstellen

Erstellen Sie in Ihrer Entwicklungsumgebung eine Textdatei namens `pigudf.py`. Fügen Sie der Datei den folgenden Code als Inhalt hinzu:

<a name="streamingpy"></a>

```python
# Uncomment the following if using C Python
#from pig_util import outputSchema


@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
def create_structure(input):
    if (input.startswith('java.lang.Exception')):
        input = input[21:len(input)] + ' - java.lang.Exception'
    date, time, classname, level, detail = input.split(' ', 4)
    return date, time, classname, level, detail
```

Im Pig Latin-Beispiel wird die `LINE`-Eingabe als chararray-Typ definiert, da es kein gleichbleibendes Schema für die Eingabe gibt. Das Python-Skript transformiert die Daten für die Ausgabe in ein gleichbleibendes Schema.

1. Die `@outputSchema`-Anweisung definiert das Format der Daten, die an Pig zurückgegeben werden. In diesem Fall ist das ein **Datenbehälter**, also ein Pig-Datentyp. Der Behälter enthält folgende Felder, die alle Chararray (Zeichenfolgen) sind:

   * date – das Datum, an dem der Protokolleintrag erstellt wurde
   * time – die Zeit, zu der der Protokolleintrag erstellt wurde
   * classname – der Klassenname, für den der Eintrag erstellt wurde
   * level – die Protokollierungsebene
   * detail – ausführliche Details des Protokolleintrags

2. Dann definiert `def create_structure(input)` die Funktion, an die Pig Positionen übergibt.

3. Die Beispieldatei `sample.log` entspricht weitgehend dem Schema für Datum, Uhrzeit, Klassenname, Ebene und Detail. Sie enthält jedoch ein paar Zeilen, die mit `*java.lang.Exception*` beginnen. Diese Zeilen müssen geändert werden, um dem Schema zu entsprechen. Die Anweisung `if` überprüft deren Vorhandensein, weist dann die Eingabedaten an, die Zeichenfolge `*java.lang.Exception*` ans Ende zu stellen, sodass die Daten dem erwarteten Ausgabeschema entsprechen.

4. Als Nächstes wird der Befehl `split` zum Aufteilen der Daten bei den ersten vier Leerzeichen verwendet. Die Ausgabe wird in `date`, `time`, `classname`, `level` und `detail` zugewiesen.

5. Zuletzt werden die Werte an Pig zurückgegeben.

Wenn die Daten an Pig zurückgegeben werden, haben sie ein gleichbleibendes Schema gemäß Definition in der `@outputSchema`-Anweisung.

### <a name="upload-file-shell"></a>Hochladen einer Datei (Shell)

Ersetzen Sie `sshuser` in den folgenden Befehlen durch den tatsächlichen Benutzernamen, falls dieser abweicht.  Ersetzen Sie `mycluster` durch den tatsächlichen Clusternamen.  Stellen Sie sicher, dass das Arbeitsverzeichnis das Verzeichnis ist, in dem sich die Datei befindet.

1. Verwenden Sie `scp` , um die Dateien in Ihren HDInsight-Cluster zu kopieren. Bearbeiten Sie den folgenden Befehl, und geben Sie ihn ein:

    ```cmd
    scp pigudf.py sshuser@mycluster-ssh.azurehdinsight.net:
    ```

2. Verwenden Sie SSH zum Herstellen einer Verbindung mit dem Cluster.  Bearbeiten Sie den folgenden Befehl, und geben Sie ihn ein:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

3. Fügen Sie die Python-Dateien, die zuvor hochgeladen wurden, in der SSH-Sitzung dem Speicher für den Cluster hinzu.

    ```bash
    hdfs dfs -put pigudf.py /pigudf.py
    ```

### <a name="use-pig-udf-shell"></a>Verwenden von Pig-UDF (Shell)

1. Verwenden Sie den folgenden Befehl aus Ihrer geöffneten SSH-Sitzung, um eine Verbindung mit Pig herzustellen:

    ```bash
    pig
    ```

2. Geben Sie in der `grunt>`-Befehlszeile die folgenden Anweisungen ein:

   ```pig
   Register wasbs:///pigudf.py using jython as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

3. Nach Eingabe der folgenden Zeile sollte der Auftrag gestartet werden. Nach Abschluss des Auftrags wird eine Ausgabe zurückgegeben, die folgenden Daten ähnelt:

    ```output
    ((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
    ((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
    ((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
    ((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
    ((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))
    ```

4. Verwenden Sie `quit` zum Beenden der Grunt-Shell und dann Folgendes zum Bearbeiten der Datei „pigudf.py“ auf dem lokalen Dateisystem:

    ```bash
    nano pigudf.py
    ```

5. Heben Sie die Kommentierung der folgenden Zeilen auf, indem Sie das Zeichen `#` vom Zeilenanfang entfernen:

    ```bash
    #from pig_util import outputSchema
    ```

    Mit dieser Zeile wird das Python-Skript so angepasst, dass es mit C Python (anstelle von Jython) verwendet werden kann. Nachdem die Änderung vorgenommen wurde, beenden Sie den Editor mit **STRG+X**. Wählen Sie **Y** und dann **EINGABE**, um die Änderungen zu speichern.

6. Verwenden Sie den `pig` -Befehl, um die Shell neu zu starten. Geben Sie Folgendes an der `grunt>` -Eingabeaufforderung ein, um das Python-Skript mit dem C-Python-Interpreter auszuführen.

   ```pig
   Register 'pigudf.py' using streaming_python as myfuncs;
   LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
   LOG = FILTER LOGS by LINE is not null;
   DETAILS = foreach LOG generate myfuncs.create_structure(LINE);
   DUMP DETAILS;
   ```

    Wenn dieser Auftrag abgeschlossen ist, sollte die Ausgabe derjenigen bei der vorherigen Skriptausführung mit Jython entsprechen.

### <a name="upload-file-powershell"></a>Hochladen einer Datei (PowerShell)

PowerShell kann auch zur Remoteausführung von Hive-Abfragen verwendet werden. Stellen Sie sicher, dass das Arbeitsverzeichnis das Verzeichnis ist, in dem sich die Datei `pigudf.py` befindet.  Verwenden Sie das folgende PowerShell-Skript zum Ausführen einer Hive-Abfrage, für die das `pigudf.py`-Skript verwendet wird:

```PowerShell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

# Revise file path as needed
$pathToJythonFile = ".\pigudf.py"


# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
$container=$clusterInfo.DefaultStorageContainer
$storageAccountKey=(Get-AzStorageAccountKey `
   -ResourceGroupName $resourceGroup `
   -Name $storageAccountName)[0].Value

# Create an Azure Storage context
$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

# Upload local files to an Azure Storage blob
Set-AzStorageBlobContent `
    -File $pathToJythonFile `
    -Blob "pigudf.py" `
    -Container $container `
    -Context $context
```

### <a name="use-pig-udf-powershell"></a>Verwenden der Pig-UDF (PowerShell)

> [!NOTE]  
> Wenn Sie einen Auftrag mithilfe von PowerShell remote übermitteln, können Sie C-Python nicht als Interpreter verwenden.

PowerShell kann auch zum Ausführen von Pig Latin-Aufträgen verwendet werden. Verwenden Sie das folgende PowerShell-Skript, um einen Pig-Latin-Auftrag mit dem `pigudf.py`-Skript auszuführen:

```PowerShell
# Script should stop on failures
$ErrorActionPreference = "Stop"

# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -UserName "admin" -Message "Enter the login for the cluster"


$PigQuery = "Register wasbs:///pigudf.py using jython as myfuncs;" +
            "LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);" +
            "LOG = FILTER LOGS by LINE is not null;" +
            "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
            "DUMP DETAILS;"

# Create Pig job object
$jobDefinition = New-AzHDInsightPigJobDefinition -Query $PigQuery

# For status bar updates
$activity="Pig job"

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Starting job..."

# Start defined Azure HDInsight job on specified cluster.
$job = Start-AzHDInsightJob `
    -ClusterName $clusterName `
    -JobDefinition $jobDefinition `
    -HttpCredential $creds

# Progress bar (optional)
Write-Progress -Activity $activity -Status "Waiting for the Pig job to complete..."

# Wait for completion or failure of specified job
Wait-AzHDInsightJob `
    -Job $job.JobId `
    -ClusterName $clusterName `
    -HttpCredential $creds

# Uncomment the following to see stderr output
<#
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds `
    -DisplayOutputType StandardError
#>

# Progress bar (optional)
Write-Progress -Activity $activity "Retrieving output..."

# Gets the log output
Get-AzHDInsightJobOutput `
    -Clustername $clusterName `
    -JobId $job.JobId `
    -HttpCredential $creds
```

Die Ausgabe für den **Pig**-Job sollte ungefähr folgenden Daten entsprechen:

```output
((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))
```

## <a name="troubleshooting"></a><a name="troubleshooting"></a>Problembehandlung

### <a name="errors-when-running-jobs"></a>Fehler beim Ausführen von Aufträgen

Bei der Ausführung des Hive-Auftrags kann ein ähnlicher Fehler wie der folgende Text auftreten:

```output
Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.
```

Dieses Problem kann durch die Zeilenenden in der Python-Datei verursacht werden. Viele Windows-Editoren verwenden als Zeilenende standardmäßig CRLF, Linux-Anwendung erwarten jedoch i. d. R. LF.

Sie können die folgenden PowerShell-Anweisungen verwenden, um die CR-Zeichen zu entfernen, bevor Sie die Datei in HDInsight hochladen:

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=148-150)]

### <a name="powershell-scripts"></a>PowerShell-Skripts

Beide für das Ausführen der Beispiele verwendeten PowerShell-Beispielskripts enthalten eine Kommentarzeile, die Fehler bei der Ausgabe des Jobs anzeigt. Wenn Sie nicht die erwartete Ausgabe für den Job sehen, heben Sie die Ausgabenkommentierung der folgenden Zeile auf, und sehen Sie nach, ob die Fehlerinformation auf ein Problem hinweist.

[!code-powershell[main](../../../powershell_scripts/hdinsight/run-python-udf/run-python-udf.ps1?range=135-139)]

Die Fehlerinformationen (STDERR) und das Ergebnis des Auftrags (STDOUT) werden auch in Ihrem HDInsight-Speicher protokolliert.

| Für diesen Job... | Sehen Sie sich diese Dateien im BLOB-Container an |
| --- | --- |
| Hive |/HivePython/stderr<p>/HivePython/stdout |
| Pig |/PigPython/stderr<p>/PigPython/stdout |

## <a name="next-steps"></a><a name="next"></a>Nächste Schritte

Wenn Sie Python-Module laden müssen, die standardmäßig nicht bereitgestellt werden, lesen Sie [Bereitstellen eines Moduls für Azure HDInsight](/archive/blogs/benjguin/how-to-deploy-a-python-module-to-windows-azure-hdinsight).

Informationen zu anderen Möglichkeiten der Verwendung von Pig und Hive sowie Informationen zur Verwendung von MapReduce finden Sie in diesen Dokumenten:

* [Verwenden von Apache Hive mit HDInsight](hdinsight-use-hive.md)
* [Verwenden von MapReduce mit HDInsight](hdinsight-use-mapreduce.md)