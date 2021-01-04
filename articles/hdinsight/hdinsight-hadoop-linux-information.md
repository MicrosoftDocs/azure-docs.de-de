---
title: Tipps zur Verwendung von Hadoop unter Linux-basiertem HDInsight – Azure
description: Hier erhalten Sie Implementierungstipps für die Verwendung von Linux-basierten HDInsight (Hadoop)-Clustern in einer vertrauten Linux-Umgebung, die in der Azure-Cloud ausgeführt wird.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,seoapr2020
ms.topic: conceptual
ms.date: 04/29/2020
ms.openlocfilehash: 0f0073c72c28395d89cec74a489cbc36a8f3ffe7
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546108"
---
# <a name="information-about-using-hdinsight-on-linux"></a>Informationen zur Verwendung von HDInsight unter Linux

Azure HDInsight-Cluster stellen Apache Hadoop in einer vertrauten Linux-Umgebung bereit, die in der Azure-Cloud ausgeführt wird. Die Lösung sollte sich größtenteils genauso wie jede andere Installation von Hadoop unter Linux verhalten. In diesem Dokument werden bestimmte Unterschiede aufgeführt, die Sie kennen sollten.

## <a name="prerequisites"></a>Voraussetzungen

In vielen Schritten in diesem Dokument werden die folgenden Hilfsprogramme verwendet. Sie müssen diese also möglicherweise auf Ihrem System installieren.

* [cURL](https://curl.haxx.se/) – wird für die Kommunikation mit webbasierten Diensten verwendet.
* **jq** , ein JSON-Befehlszeilenprozessor.  Siehe [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).
* [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli) – wird zur Remoteverwaltung von Azure-Diensten verwendet.
* **SSH-Client** . Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit HDInsight (Hadoop) per SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="users"></a>Benutzer

Sofern es nicht [in eine Domäne eingebunden](./domain-joined/hdinsight-security-overview.md) ist, sollte HDInsight als **Einzelbenutzersystem** betrachtet werden. Ein SSH-Einzelbenutzerkonto wird mit dem Cluster mit Administratorrechten erstellt. Zusätzliche SSH-Konten können erstellt werden, haben jedoch auch Administratorzugriff auf den Cluster.

In die Domäne eingebundenes HDInsight bietet Unterstützung mehrerer Benutzer sowie präzisere Einstellungen für Berechtigungen und Rollen. Weitere Informationen finden Sie unter [Manage Domain-joined HDInsight clusters](./domain-joined/apache-domain-joined-manage.md) (Verwalten von in die Domäne eingebundenen HDInsight-Clustern).

## <a name="domain-names"></a>Domänennamen

Der vollqualifizierte Domänenname (FQDN), der beim Herstellen einer Verbindung mit dem Cluster über das Internet verwendet wird, lautet `CLUSTERNAME.azurehdinsight.net` oder `CLUSTERNAME-ssh.azurehdinsight.net` (nur für SSH).

Intern verfügt jeder Knoten im Cluster über einen Namen, der während der Konfiguration des Clusters zugewiesen wird. Die Clusternamen finden Sie auf der Ambari-Webbenutzeroberfläche auf der Seite **Hosts** . Sie können auch Folgendes verwenden, um eine Liste mit Hosts aus der Ambari-REST-API abzurufen:

```console
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'
```

Ersetzen Sie `CLUSTERNAME` durch den Namen Ihres Clusters. Geben Sie bei entsprechender Aufforderung das Kennwort des Administratorkontos für den Cluster ein. Dieser Befehl gibt ein JSON-Dokument zurück, das eine Liste der Hosts im Cluster enthält. [jq](https://stedolan.github.io/jq/) wird zum Extrahieren des Werts des Elements `host_name` für jeden Host verwendet.

Wenn Sie den Namen des Knotens für einen bestimmten Dienst suchen müssen, können Sie Ambari nach dieser Komponente abfragen. Verwenden Sie beispielsweise den folgenden Befehl, um nach den Hosts für den HDFS-Namensknoten zu suchen:

```console
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'
```

Mit diesem Befehl wird ein JSON-Dokument mit einer Beschreibung des Diensts zurückgegeben. Anschließend ruft [jq](https://stedolan.github.io/jq/) nur den `host_name`-Wert für die Hosts ab.

## <a name="remote-access-to-services"></a>Remotezugriff auf Dienste

* **Ambari (Web)**  - `https://CLUSTERNAME.azurehdinsight.net`

    Authentifizieren Sie sich mit dem Benutzernamen und Kennwort des Clusteradministrators, und melden Sie sich anschließend bei Ambari an.

    Die Authentifizierung erfolgt unverschlüsselt. Verwenden Sie immer HTTPS, um eine sichere Verbindung zu gewährleisten.

    > [!IMPORTANT]  
    > Einige der über Ambari verfügbaren Webbenutzeroberflächen greifen über einen internen Domänennamen auf Knoten zu. Auf interne Domänennamen besteht kein öffentlicher Zugriff über das Internet. Sie erhalten ggf. die Fehlermeldung, dass der Server nicht gefunden wurde, wenn Sie versuchen, auf einige Features über das Internet zuzugreifen.
    >
    > Damit Sie die Funktionalität der Ambari-Webbenutzeroberfläche vollständig nutzen können, verwenden Sie einen SSH-Tunnel, um den Webdatenverkehr per Proxy an den Clusterhauptknoten weiterzuleiten. Weitere Informationen finden Sie unter [Verwenden von SSH-Tunneling zum Zugriff auf die Apache Ambari-Webbenutzeroberfläche, ResourceManager, JobHistory, NameNode, Oozie und andere Webbenutzeroberflächen](hdinsight-linux-ambari-ssh-tunnel.md).

* **Ambari (REST)**  - `https://CLUSTERNAME.azurehdinsight.net/ambari`

    > [!NOTE]  
    > Authentifizieren Sie sich mit dem Benutzernamen und Kennwort des Clusteradministrators.
    >
    > Die Authentifizierung erfolgt unverschlüsselt. Verwenden Sie immer HTTPS, um eine sichere Verbindung zu gewährleisten.

* **WebHCat (Templeton)**  - `https://CLUSTERNAME.azurehdinsight.net/templeton`

    > [!NOTE]  
    > Authentifizieren Sie sich mit dem Benutzernamen und Kennwort des Clusteradministrators.
    >
    > Die Authentifizierung erfolgt unverschlüsselt. Verwenden Sie immer HTTPS, um eine sichere Verbindung zu gewährleisten.

* **SSH** – CLUSTERNAME-ssh.azurehdinsight.net an Port 22 oder 23. Port 22 dient zum Herstellen einer Verbindung mit dem primären Hauptknoten, während 23 zum Herstellen einer Verbindung mit dem sekundären Knoten verwendet wird. Weitere Informationen zu Hauptknoten finden Sie unter [Verfügbarkeit und Zuverlässigkeit von Apache Hadoop-Clustern in HDInsight](./hdinsight-business-continuity.md).

    > [!NOTE]  
    > Sie können auf einem Clientcomputer nur über SSH auf die Hauptknoten des Clusters zugreifen. Nachdem die Verbindung hergestellt wurde, können Sie von einem Hauptknoten aus über SSH auf die Workerknoten zugreifen.

Weitere Informationen finden Sie im Dokument [Ports für Apache Hadoop-Dienste in HDInsight](hdinsight-hadoop-port-settings-for-services.md).

## <a name="file-locations"></a>Dateispeicherorte

Zu Hadoop zugehörige Dateien befinden sich auf den Clusterknoten in `/usr/hdp`. Dieses Verzeichnis enthält die folgenden Unterverzeichnisse:

* **2.6.5.3009-43** : Der Name des Verzeichnisses ist die Version der in HDInsight verwendeten Hadoop-Plattform. Der Wert Ihres Clusters unterscheidet sich möglicherweise vom hier aufgeführten.
* **current** : Dieses Verzeichnis enthält Links zu den Unterverzeichnissen im Verzeichnis **2.6.5.3009-43** . Da dieses Verzeichnis vorhanden ist, müssen Sie sich nicht die Versionsnummer merken.

Beispieldaten und JAR-Dateien finden Sie im Hadoop Distributed File System unter `/example` und `/HdiSamples`.

## <a name="hdfs-azure-storage-and-data-lake-storage"></a>HDFS, Azure Storage und Data Lake Storage

In den meisten Hadoop-Distributionen werden die Daten in HDFS gespeichert. HDFS wird auf den Computern im Cluster durch lokalen Speicher gesichert. Die Nutzung von lokalem Speicher kann für eine cloudbasierte Lösung mit zeitabhängiger Abrechnung (pro Stunde oder pro Minute) für Computeressourcen kostenintensiv sein.

Bei Verwendung von HDInsight werden die Datendateien anpassbar und zuverlässig mithilfe von Azure Blob Storage und optional Azure Data Lake Storage Gen1/Gen2 in der Cloud gespeichert. Diese Dienste bieten die folgenden Vorteile:

* Kostengünstige langfristige Speicherung
* Zugriff über externe Dienste wie Websites, Hilfsprogramme zum Hochladen/Herunterladen von Dateien, SDKs für verschiedene Sprachen und Webbrowser
* Große Dateikapazität und großer anpassbarer Speicher.

Weitere Informationen finden Sie unter [Einführung in die zentralen Azure Storage-Dienste](../storage/common/storage-introduction.md), [Was ist Azure Data Lake Storage Gen1?](../data-lake-store/data-lake-store-overview.md) und [Einführung in Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

Wenn Sie entweder Azure Blob Storage oder Azure Data Lake Storage Gen1 bzw. Gen2 nutzen, müssen Sie in HDInsight für den Datenzugriff keine besonderen Schritte ausführen. Mit dem folgenden Befehl werden Dateien im Ordner `/example/data` beispielsweise unabhängig davon aufgelistet, ob dieser in Azure Storage oder Data Lake Storage gespeichert ist:

```console
hdfs dfs -ls /example/data
```

In HDInsight werden die Datenspeicherressourcen (Azure Blob Storage und Azure Data Lake Store) von den Computerressourcen entkoppelt. Sie können HDInsight-Cluster nach Bedarf für Berechnungen erstellen und die Cluster später löschen, wenn die Arbeit abgeschlossen ist. In der Zwischenzeit verbleiben Ihre Datendateien sicher im Cloudspeicher, solange Sie sie benötigen.

### <a name="uri-and-scheme"></a><a name="URI-and-scheme"></a>URI und Schema

Einige Befehle erfordern ggf. die Angabe des Schemas als Teil des URI, wenn auf eine Datei zugegriffen wird. Beispielsweise erfordert die Komponente Storm-HDFS, dass Sie das Schema angeben. Bei Verwendung von nicht standardmäßigem Speicher (der dem Cluster als „zusätzlicher“ Speicher hinzugefügt wird), müssen Sie stets das Schema als Teil des URI angeben.

Nutzen Sie bei Verwendung von [**Azure Storage**](./hdinsight-hadoop-use-blob-storage.md) eines der folgenden URI-Schemas:

* `wasb:///`: Zugriff auf Standardspeicher über unverschlüsselte Verbindungen.

* `wasbs:///`: Zugriff auf Standardspeicher über verschlüsselte Verbindungen.  Das wasbs-Schema wir nur ab HDInsight-Version 3.6 unterstützt.

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`: Wird bei einer Verbindung mit einem nicht standardmäßigen Speicherkonto verwendet. Beispielsweise wenn Sie ein zusätzliches Speicherkonto haben oder auf Daten in einem öffentlich zugänglichen Speicherkonto zugreifen.

Nutzen Sie bei Verwendung von [**Azure Data Lake Storage Gen2**](./hdinsight-hadoop-use-data-lake-storage-gen2.md) das folgende URI-Schema:

* `abfs://`: Zugriff auf Standardspeicher über verschlüsselte Verbindungen.

* `abfs://<container-name>@<account-name>.dfs.core.windows.net/`: Wird bei einer Verbindung mit einem nicht standardmäßigen Speicherkonto verwendet. Beispielsweise wenn Sie ein zusätzliches Speicherkonto haben oder auf Daten in einem öffentlich zugänglichen Speicherkonto zugreifen.

Nutzen Sie bei Verwendung von [**Azure Data Lake Storage Gen1**](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md) eines der folgenden URI-Schemas:

* `adl:///`: Zugriff auf die standardmäßige Data Lake Storage-Instanz für den Cluster

* `adl://<storage-name>.azuredatalakestore.net/`: Wird bei einer Verbindung mit einer nicht standardmäßigen Data Lake Storage-Instanz verwendet. Wird auch verwendet, um auf Daten außerhalb des Stammverzeichnisses Ihres HDInsight-Clusters zuzugreifen.

> [!IMPORTANT]  
> Wenn Data Lake Storage als Standardspeicher für HDInsight verwendet wird, müssen Sie einen Pfad innerhalb des Speichers als Stamm des HDInsight-Speichers angeben. Der Standardpfad lautet `/clusters/<cluster-name>/`.
>
> Bei Verwendung von `/` oder `adl:///` für den Zugriff auf Daten können Sie nur auf Daten zugreifen, die im Stammverzeichnis (z.B. `/clusters/<cluster-name>/`) des Clusters gespeichert sind. Um auf Daten an beliebiger Stelle im Speicher zuzugreifen, verwenden Sie das Format `adl://<storage-name>.azuredatalakestore.net/`.

### <a name="what-storage-is-the-cluster-using"></a>Der vom Cluster verwendete Speicher

Mithilfe von Ambari können Sie die Standardspeicherkonfiguration des Clusters abrufen. Verwenden Sie den folgenden Befehl, um HDFS-Konfigurationsinformationen über „curl“ abzurufen, und filtern Sie sie mit [jq](https://stedolan.github.io/jq/):

```bash
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

> [!NOTE]  
> Durch diesen Befehl wird die erste auf den Server angewendete Konfiguration (`service_config_version=1`) zurückgegeben, die diese Informationen enthält. Möglicherweise müssen Sie alle Konfigurationsversionen auflisten, um die neueste zu finden.

Dieser Befehl gibt einen Wert zurück, der in etwa wie die folgenden URIs aussieht:

* `wasb://<container-name>@<account-name>.blob.core.windows.net` bei Verwenden eines Azure Storage-Kontos.

    Der Kontoname ist der Name Ihres Azure Storage-Kontos Der Containername in der Blobcontainer, der der Stamm des Clusterspeichers ist.

* `adl://home`, bei Verwendung von Azure Data Lake Storage. Verwenden Sie zum Abrufen des Data Lake Storage-Namens den folgenden REST-Aufruf:

     ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    Mit diesem Befehl wird der folgende Hostname zurückgegeben: `<data-lake-store-account-name>.azuredatalakestore.net`.

    Verwenden Sie zum Abrufen des Verzeichnisses innerhalb des Speichers, der das Stammverzeichnis für HDInsight ist, den folgenden REST-Aufruf:

    ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    Mit diesem Befehl wird ein Pfad zurückgegeben, der in etwa wie folgt aussieht: `/clusters/<hdinsight-cluster-name>/`.

Sie können die Speicherinformationen auch über das Azure-Portal finden, indem Sie die folgenden Schritte ausführen:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/)Ihren HDInsight-Cluster aus.

2. Wählen Sie im Abschnitt **Eigenschaften** den Eintrag **Speicherkonten** aus. Die Speicherinformationen für den Cluster werden angezeigt.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>Zugreifen auf Daten außerhalb von HDInsight

Es gibt viele verschiedene Möglichkeiten, wie Sie außerhalb des HDInsight-Clusters auf Daten zugreifen können. Es folgen einige Links zu Hilfsprogrammen und SDKs, die zum Arbeiten mit Ihren Daten verwendet werden können:

Wenn Sie __Azure Blob Storage__ nutzen, finden Sie unter den folgenden Links Methoden für den Zugriff auf Ihre Daten:

* [Azure CLI](/cli/azure/install-az-cli2): Befehlszeilenschnittstelle für die Arbeit mit Azure. Verwenden Sie nach der Installation den Befehl `az storage`, um Hilfe zur Speicherung abzurufen, oder `az storage blob` für blobspezifische Befehle.
* [blobxfer.py](https://github.com/Azure/blobxfer): Python-Skript zum Arbeiten mit Blobs in Azure Storage.
* Verschiedene SDKs:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.js](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [Storage-REST-API](/rest/api/storageservices/Blob-Service-REST-API)

Wenn Sie __Azure Data Lake Storage Gen1__ nutzen, finden Sie unter den folgenden Links Methoden für den Zugriff auf Ihre Daten:

* [Webbrowser](../data-lake-store/data-lake-store-get-started-portal.md)
* [PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [Azure-Befehlszeilenschnittstelle](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [WebHDFS-REST-API](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Data Lake-Tools für Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling-your-cluster"></a><a name="scaling"></a>Skalieren des Clusters

Mithilfe der Clusterskalierung können Sie die Anzahl der von einem Cluster verwendeten Datenknoten ändern. Sie können Skalierungsvorgänge ausführen, während andere Aufträge oder Prozesse in einem Cluster ausgeführt werden.  Weitere Informationen finden Sie unter [Skalieren von HDInsight-Clustern](./hdinsight-scaling-best-practices.md)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Wie installiere ich Hue (oder eine andere Hadoop-Komponente)?

HDInsight ist ein verwalteter Dienst. Wenn Azure ein Problem mit dem Cluster erkennt, kann der betroffene Knoten gelöscht und ein Ersatzknoten erstellt werden. Falls Sie Komponenten manuell im Cluster installieren, werden sie nicht beibehalten, wenn dieser Vorgang ausgeführt wird. Verwenden Sie stattdessen [HDInsight-Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md). Sie können eine Skriptaktion verwenden, um die folgenden Änderungen vorzunehmen:

* Installieren und Konfigurieren eines Diensts oder einer Website.
* Installieren und Konfigurieren einer Komponente, für die Konfigurationsänderungen auf mehreren Knoten im Cluster erforderlich sind.

Bei Skriptaktionen handelt es sich um Bash-Skripts. Die Skripts werden während der Clustererstellung ausgeführt und können zur Installation und Konfiguration zusätzlicher Komponenten verwendet werden. Informationen zum Entwickeln eigener Skriptaktionen finden Sie unter [Entwickeln von Skriptaktionen mit HDInsight](hdinsight-hadoop-script-actions-linux.md).

### <a name="jar-files"></a>JAR-Dateien

Einige Hadoop-Technologien stellen eigenständige JAR-Dateien bereit. Diese Dateien enthalten Funktionen, die im Rahmen eines MapReduce-Auftrags oder in Pig oder Hive verwendet werden. Dafür ist häufig keine Einrichtung notwendig, und sie können nach der Erstellung im Cluster hochgeladen und direkt verwendet werden. Wenn Sie sicherstellen möchten, dass die Komponente bei einer erneuten Imageerstellung des Clusters nicht beschädigt wird, speichern Sie die JAR-Datei im Standardspeicher des Clusters.

Wenn Sie beispielsweise die neueste Version von [Apache DataFu](https://datafu.incubator.apache.org/)verwenden möchten, können Sie eine JAR-Datei mit dem Projekt herunterladen und in den HDInsight-Cluster hochladen. Führen Sie dann die Anweisungen in der DataFu-Dokumentation aus, um sie aus Pig oder Hive zu verwenden.

> [!IMPORTANT]  
> Einige Komponenten, bei denen es sich um eigenständige JAR-Dateien handelt, werden mit HDInsight bereitgestellt, sind jedoch nicht im Pfad vorhanden. Wenn Sie eine bestimmte Komponente suchen, können Sie sie hiermit im Cluster suchen:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Mit diesem Befehl wird der Pfad aller übereinstimmenden JAR-Dateien zurückgegeben.

Um eine andere Version einer Komponente zu verwenden, laden Sie die benötigte Version hoch, und verwenden Sie sie in Ihren Aufträgen.

> [!IMPORTANT]
> Komponenten, die mit dem HDInsight-Cluster bereitgestellt werden, werden vollständig unterstützt, und Microsoft Support hilft Ihnen, Probleme im Zusammenhang mit diesen Komponenten zu isolieren und zu beheben.
>
> Für benutzerdefinierte Komponenten steht kommerziell angemessener Support für eine weiterführende Behebung des Problems zur Verfügung. Auf diese Weise kann das Problem behoben werden, ODER Sie werden aufgefordert, verfügbare Kanäle für Open-Source-Technologien in Anspruch zu nehmen, die über umfassende Kenntnisse für diese Technologien verfügen. So können z. B. viele Communitywebsites verwendet werden, wie: [Frageseite von Microsoft Q&A (Fragen und Antworten) für HDInsight](/answers/topics/azure-hdinsight.html), [https://stackoverflow.com](https://stackoverflow.com). Für Apache-Projekte gibt es auch Projektwebsites auf [https://apache.org](https://apache.org). Beispiel: [Hadoop](https://hadoop.apache.org/), [Spark](https://spark.apache.org/).

## <a name="next-steps"></a>Nächste Schritte

* [Verwalten von HDInsight-Clustern mithilfe der Apache Ambari-REST-API](./hdinsight-hadoop-manage-ambari-rest-api.md)
* [Verwenden von Apache Hive mit HDInsight](hadoop/hdinsight-use-hive.md)
* [Verwenden von MapReduce-Aufträgen mit HDInsight](hadoop/hdinsight-use-mapreduce.md)