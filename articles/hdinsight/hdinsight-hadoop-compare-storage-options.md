---
title: Vergleich der Speicheroptionen für die Verwendung mit Azure HDInsight-Clustern
description: Dieser Artikel bietet einen Überblick über die Speichertypen und deren Funktionsweise mit Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/08/2019
ms.openlocfilehash: 320b8f948d08e46c43085e174dfbe838f44bac79
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479159"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Vergleich der Speicheroptionen für die Verwendung mit Azure HDInsight-Clustern

Sie haben beim Erstellen von HDInsight-Clustern die Auswahl zwischen verschiedenen Azure Storage-Diensten:

* Azure Storage
* Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen1

Dieser Artikel bietet eine Übersicht über diese Speichertypen und ihre besonderen Merkmale.

Die folgende Tabelle enthält die Azure Storage-Dienste, die mit den unterschiedlichen Versionen von HDInsight unterstützt werden:

| Speicherdienst | Kontotyp | Namespacetyp | Unterstützte Dienste | Unterstützte Leistungsstufen | Unterstützte Zugriffsebenen | HDInsight-Version | Clustertyp |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| Allgemein v2 | Hierarchisch (Dateisystem) | Blob | Standard | Heiß, Kalt, Archiv | 3.6 und höher | Alle |
|Azure Storage| Allgemein v2 | Object | Blob | Standard | Heiß, Kalt, Archiv | 3.6 und höher | Alle |
|Azure Storage| Allgemein v1 | Object | Blob | Standard | – | Alle | Alle |
|Azure Storage| Blob Storage** | Object | Blockblob | Standard | Heiß, Kalt, Archiv | Alle | Alle |
|Azure Data Lake Storage Gen1| – | Hierarchisch (Dateisystem) | – | – | – | Nur 3.6 | Alle mit Ausnahme von HBase |

**Für HDInsight-Cluster können nur sekundäre Speicherkonten den Typ „BlobStorage“ aufweisen.

Weitere Informationen zu Azure Storage-Kontotypen finden Sie unter [Azure-Speicherkonten – Übersicht](../storage/common/storage-account-overview.md).

Weitere Informationen zu Azure Storage-Zugriffsebenen finden Sie unter [Azure Blob Storage: Speicherebenen „Premium“ (Vorschauversion), „Heiß“, „Kalt“ und „Archiv“](../storage/blobs/storage-blob-storage-tiers.md).

Sie können einen Cluster mit anderen Kombinationen von Diensten für den primären und optional einen sekundären Speicher erstellen. Die folgende Tabelle enthält die Clusterspeicherkonfigurationen, die derzeit in HDInsight unterstützt werden:

| HDInsight-Version | Primärer Speicher | Sekundärer Speicher | Unterstützt |
|---|---|---|---|
| 3.6 und 4.0 | Universell V1, Universell V2 | Universell V1, Universell V2, BlobStorage (Blockblobs) | Ja |
| 3.6 und 4.0 | Universell V1, Universell V2 | Data Lake Storage Gen2 | Nein |
| 3.6 und 4.0 | Universell V1, Universell V2 | Data Lake Storage Gen1 | Ja |
| 3.6 und 4.0 | Data Lake Storage Gen2* | Data Lake Storage Gen2 | Ja |
| 3.6 und 4.0 | Data Lake Storage Gen2* | Universell V1, Universell V2, BlobStorage (Blockblobs) | Ja |
| 3.6 und 4.0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | Nein |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | Ja |
| 3.6 | Data Lake Storage Gen1 | Universell V1, Universell V2, BlobStorage (Blockblobs) | Ja |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | Nein |
| 4,0 | Data Lake Storage Gen1 | Beliebig | Nein |

* Dies können etwa Data Lake Storage Gen2-Konten sein, sofern sie alle für die Verwendung derselben verwalteten Identität für den Clusterzugriff eingerichtet wurden.

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Verwenden von Azure Data Lake Storage Gen2 mit Apache Hadoop in Azure HDInsight

Azure Data Lake Storage Gen2 integriert Kernfeatures von Azure Data Lake Storage Gen1 in Azure Blob Storage. Zu diesen Features gehört ein Dateisystem, das mit Hadoop, Azure Active Directory (Azure AD) und POSIX-basierten Zugriffssteuerungslisten (ACLs) kompatibel ist. Durch diese Kombination können Sie die Leistung von Azure Data Lake Storage Gen1 nutzen und gleichzeitig das Tiering und die Verwaltung des Datenlebenszyklus von Blob Storage verwenden.

Weitere Informationen zu Data Lake Storage Gen2 finden Sie in der [Einführung in Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md).

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Kernfunktionen von Azure Data Lake Storage Gen2

* **Mit Hadoop kompatibler Zugriff:** In Azure Data Lake Storage Gen2 können Sie ebenso auf Daten zugreifen und diese verwalten wie mit einem Hadoop Distributed File System (HDFS). Der ABFS-Treiber (Azure Blob File System) ist in allen Apache Hadoop-Umgebungen verfügbar, einschließlich Azure HDInsight und Azure Databricks. Mit ABFS können Sie auf die in Data Lake Storage Gen2 gespeicherten Daten zugreifen.

* **Obermenge von POSIX-Berechtigungen:** Das Sicherheitsmodell für Data Lake Gen2 unterstützt Zugriffssteuerungslisten und POSIX-Berechtigungen sowie zusätzliche Granularität speziell für Data Lake Storage Gen2. Die Einstellungen können über Verwaltungstools oder Frameworks wie Apache Hive und Apache Spark konfiguriert werden.

* **Kosteneffizienz:** Data Lake Storage Gen2 bietet kostengünstige Speicherkapazität und -transaktionen. Features wie der Lebenszyklus von Azure Blob Storage helfen beim Senken der Kosten, indem sie die Abrechnungsraten anpassen, während die Daten den Lebenszyklus durchlaufen.

* **Kompatibilität mit Blob Storage-Tools, -Frameworks und -Apps:** Data Lake Storage Gen2 funktioniert weiterhin mit einer Vielzahl von Tools, Frameworks und Anwendungen für Blob Storage.

* **Optimierter Treiber:** Der ABFS-Treiber ist speziell für Big Data-Analysen optimiert. Die entsprechenden REST-APIs werden über den DFS-Endpunkt (Distributed File System, verteiltes Dateisystem) dfs.core.windows.net bereitgestellt.

### <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Neues bei Azure Data Lake Storage Gen2

#### <a name="managed-identities-for-secure-file-access"></a>Verwaltete Identitäten für sicheren Dateizugriff

Azure HDInsight verwendet verwaltete Identitäten, um den Clusterzugriff auf Dateien in Azure Data Lake Storage Gen2 zu sichern. Verwaltete Identitäten sind ein Feature von Azure Active Directory, das Azure-Diensten eine Reihe von automatisch verwalteten Anmeldeinformationen zur Verfügung stellt. Diese Anmeldeinformationen können zur Authentifizierung bei jedem Dienst verwendet werden, der Active Directory-Authentifizierung unterstützt. Die Verwendung verwalteter Identitäten erfordert nicht, dass Sie Anmeldeinformationen in Code- oder Konfigurationsdateien speichern.

Weitere Informationen finden Sie unter [Verwaltete Identitäten für Azure-Ressourcen](../active-directory/managed-identities-azure-resources/overview.md).

#### <a name="azure-blob-file-system-driver"></a>Azure Blob File System-Treiber

Apache Hadoop-Anwendungen erwarten nativ, dass sie Daten von lokalen Datenträgerspeichern lesen und auf diese schreiben. Ein Hadoop-Dateisystemtreiber wie ABFS ermöglicht es Hadoop-Anwendungen, mit Cloudspeicher zu arbeiten, indem reguläre Hadoop-Dateisystemvorgänge emuliert werden. Der Treiber wandelt diese anschließend in Vorgänge um, die von der eigentlichen Cloudspeicherplattform interpretiert werden können.

Bisher wurden mit dem Hadoop-Dateisystemtreiber alle Dateisystemoperationen auf Clientseite in Azure Storage-REST-API-Aufrufe umgewandelt, und dann wurde die REST-API aufgerufen. Diese clientseitige Konvertierung führte jedoch zu mehreren REST-API-Aufrufen für einzelne Dateisystemvorgänge wie das Umbenennen einer Datei. Mit ABFS wurde ein Teil der Hadoop-Dateisystemlogik von der Clientseite zur Serverseite verschoben. Die Azure Data Lake Storage Gen2-API wird jetzt parallel zur Blob-API ausgeführt. Diese Migration verbessert die Leistung, da jetzt gängige Hadoop-Dateisystemvorgänge mit einem REST-API-Aufruf ausgeführt werden können.

Weitere Informationen finden Sie unter [Azure-Blobdateisystemtreiber (ABFS): Ein dedizierter Azure Storage-Treiber für Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md).

#### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>URI-Schema für Azure Data Lake Storage Gen2 

Azure Data Lake Storage Gen2 verwendet für den Zugriff auf Dateien in Azure Storage aus HDInsight ein neues URI-Schema:

`abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Das URI-Schema bietet SSL-verschlüsselten (Präfix `abfss://`) und unverschlüsselten Zugriff (Präfix `abfs://`). Verwenden Sie nach Möglichkeit `abfss`, auch für den Zugriff auf Daten, die sich in derselben Region in Azure befinden.

`<FILE_SYSTEM_NAME>` gibt den Pfad des Dateisystems in Data Lake Storage Gen2 an.

`<ACCOUNT_NAME>` gibt den Namen des Azure Storage-Kontos an. Ein vollqualifizierter Domänenname (FQDN) ist erforderlich.

`<PATH>` ist der HDFS-Pfadname für die Datei oder das Verzeichnis.

Wenn keine Werte für `<FILE_SYSTEM_NAME>` und `<ACCOUNT_NAME>` angegeben sind, wird das Standarddateisystem verwendet. Für die Dateien im Standarddateisystem können Sie relative oder absolute Pfade verwenden. Auf die Datei `hadoop-mapreduce-examples.jar`, die sich in HDInsight-Clustern befindet, kann z. B. mithilfe eines der folgenden Pfade verwiesen werden:

```
abfss://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfss:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> In HDInsight-Clustern der Version 2.1 und 1.6 lautet der Dateiname `hadoop-examples.jar`. Wenn Sie Dateien außerhalb von HDInsight verwenden, wird das ABFS-Format von den meisten Hilfsprogrammen nicht erkannt. Stattdessen wird ein einfaches Pfadformat wie `example/jars/hadoop-mapreduce-examples.jar` erwartet.

Weitere Informationen finden Sie unter [Verwenden des Azure Data Lake Storage Gen2-URI](../storage/blobs/data-lake-storage-introduction-abfs-uri.md).

## <a name="azure-storage"></a>Azure Storage

Azure Storage stellt eine robuste universelle Speicherlösung dar, die nahtlos mit HDInsight integriert werden kann. In HDInsight kann ein Blobcontainer in Azure Storage als Standarddateisystem für den Cluster verwendet werden. Über eine HDFS-Schnittstelle können sämtliche Komponenten in HDInsight direkt mit strukturierten oder unstrukturierten Daten arbeiten, die als Blobs gespeichert sind.

Es wird empfohlen, separate Speichercontainer für Ihren Standardclusterspeicher und Ihre Geschäftsdaten zu verwenden, um die HDInsight-Protokolle und die temporären Dateien von Ihren eigenen Geschäftsdaten zu isolieren. Wir empfehlen, die Standardblobcontainer mit den Anwendungs- und Systemprotokollen nach jeder Verwendung zu löschen, um die Speicherkosten zu verringern. Stellen Sie sicher, dass Sie die Protokolle abrufen, bevor Sie den Container löschen.

Wenn Sie sich dazu entscheiden, Ihr Speicherkonto mit den **Firewalls und virtuelle Netzwerke**-Einschränkungen auf **Ausgewählte Netzwerke** zu schützen, denken Sie daran, die Ausnahme **Vertrauenswürdige Microsoft-Dienste zulassen…** zu aktivieren, damit HDInsight auf Ihr Speicherkonto zugreifen kann.

### <a name="hdinsight-storage-architecture"></a>HDInsight-Speicherarchitektur

Das folgende Diagramm bietet eine verallgemeinerte Übersicht über die HDInsight-Architektur von Azure Storage:

![Diagramm der Verwendung der HDFS-API durch Hadoop-Cluster, um auf strukturierte und unstrukturierte Daten in Blob Storage zuzugreifen und diese zu speichern](./media/hdinsight-hadoop-compare-storage-options/HDI.WASB.Arch.png "HDInsight Storage-Architektur")

HDInsight bietet Zugang zum verteilten Dateisystem, das lokal an die Rechenknoten angefügt ist. Auf dieses Dateisystem kann über den vollständig qualifizierten URI zugegriffen werden. Zum Beispiel:

    hdfs://<namenodehost>/<path>

Mit HDInsight können Sie auch auf Daten in Azure Storage zugreifen. Die Syntax lautet wie folgt:

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

Berücksichtigen Sie die folgenden Prinzipien, wenn Sie ein Azure Storage-Konto mit HDInsight-Clustern verwenden:

* **Container in Speicherkonten, die mit einem Cluster verbunden sind:** Da Kontoname und Schlüssel dem Cluster bei der Erstellung zugeordnet werden, haben Sie vollständigen Zugriff auf die Blobs in diesen Containern.

* **Öffentliche Container oder öffentliche Blobs in Speicherkonten, die *nicht* mit einem Cluster verbunden sind:** Sie verfügen über Leserechte für die Blobs in den Containern.
  
  > [!NOTE]  
  > Öffentliche Container erlauben das Abrufen einer Liste aller im Container verfügbaren Blobs und der Container-Metadaten. Auf öffentliche Blobs haben Sie nur Zugriff, wenn Sie die exakte URL kennen. Weitere Informationen finden Sie unter [Verwalten des anonymen Lesezugriffs auf Container und Blobs](../storage/blobs/storage-manage-access-to-resources.md).

* **Private Container in Speicherkonten, die *nicht* mit einem Cluster verbunden sind:** Sie können nicht auf die Blobs in den Containern zugreifen, es sei denn, Sie definieren beim Senden der WebHCat-Aufträge das Speicherkonto. 

Die bei der Erstellung definierten Speicherkonten und ihre Schlüssel werden in der Datei „%HADOOP_HOME%/conf/core-site.xml“ auf den Clusterknoten gespeichert. Standardmäßig verwendet HDInsight die in der Datei „core-site.xml“ definierten Speicherkonten. Sie können diese Einstellung mit [Apache Ambari](./hdinsight-hadoop-manage-ambari.md) ändern.

Verschiedene WebHCat-Aufträge, darunter Apache Hive, MapReduce, Apache Hadoop Streaming und Apache Pig, können eine Beschreibung von Speicherkonten und Metadaten enthalten. (Dies funktioniert momentan nur für Pig mit Speicherkonten, nicht für Metadaten.) Weitere Informationen finden Sie unter [Verwenden eines HDInsight-Clusters mit alternativen Speicherkonten und Metastores](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Blobs können für strukturierte und unstrukturierte Daten verwendet werden. In Blobcontainern werden Daten als Schlüssel-Wert-Paare gespeichert, und es gibt keine Verzeichnishierarchie. Der Schlüsselname kann jedoch einen Schrägstrich (/) enthalten, damit es so aussieht, als wäre eine Datei in einer Verzeichnisstruktur gespeichert. Der Schlüssel eines Blobs kann z.B. `input/log1.txt` lauten. Es gibt kein Verzeichnis `input`, aber aufgrund des Schrägstrichs im Namen sieht der Schlüssel wie ein Dateipfad aus.

### <a id="benefits"></a>Vorteile von Azure Storage
Für Computecluster und Speicherressourcen, die sich nicht am selben Ort befinden, fallen implizite Leistungskosten an. Diese Kosten können dadurch verringert werden, dass Computecluster nahe bei den Speicherkontoressourcen innerhalb der Azure-Region erstellt werden. In dieser Region können die Serverknoten effizient über das Hochgeschwindigkeitsnetzwerk in Azure Storage auf die Daten zugreifen.

Wenn Sie die Daten in Azure Storage anstelle von HDFS speichern, erhalten Sie mehrere Vorteile:

* **Datenwiederverwendung und -freigabe:** Die Daten im HDFS befinden sich innerhalb des Computeclusters. Nur die Anwendungen, die Zugriff auf den Rechencluster haben, können die Daten über die HDFS-API verwenden. Auf die Daten in Azure Storage kann im Gegensatz dazu über die HDFS-APIs oder über die Blob Storage-REST-APIs zugegriffen werden. Dadurch kann eine größere Menge von Anwendungen (darunter andere HDInsight-Cluster) und Tools verwendet werden, um die Daten zu produzieren und abzurufen.

* **Datenarchivierung:** Wenn Daten in Azure Storage gespeichert werden, kann der für Berechnungen verwendete HDInsight-Cluster sicher gelöscht werden, ohne Benutzerdaten zu verlieren.

* **Datenspeicherkosten:** Die langfristige Datenspeicherung in DFS ist kostspieliger als die Datenspeicherung in Azure Storage, da die Kosten eines Computeclusters höher als die Kosten für Azure Storage sind. Da die Daten nicht für jede Erzeugung eines neues Computeclusters neu geladen werden müssen, sparen Sie außerdem Kosten für das Laden von Daten.

* **Elastische horizontale Skalierung:** Auch wenn HDFS ein horizontal skaliertes Dateisystem bietet, wird die Skalierung durch die Anzahl der Knoten bestimmt, die Sie für Ihren Cluster erstellen. Eine Änderung der Skalierung kann weitaus schwieriger sein, als auf die Kapazitäten der elastischen Skalierung zu vertrauen, die Ihnen Azure Storage automatisch bietet.

* **Georeplikation:** Für Azure Storage kann eine Georeplikation durchgeführt werden. Obwohl die Georeplikation geografische Wiederherstellung und Datenredundanz bietet, wirkt sich ein Failover auf den georeplizierten Standort stark auf die Leistung aus und kann zusätzliche Kosten nach sich ziehen. Sie sollten die Georeplikation mit Bedacht und nur dann auswählen, wenn der Wert der Daten die zusätzlichen Kosten rechtfertigt.

Bestimmte MapReduce-Aufträge und -Pakete können zu Zwischenergebnissen führen, die Sie nicht in Azure Storage speichern möchten. In diesem Fall können Sie die Dateien auch im lokalen HDFS speichern. HDInsight verwendet für einige dieser Zwischenergebnisse in Hive-Aufträge und anderen Prozessen DFS.

> [!NOTE]  
> Die meisten HDFS-Befehle (z.B. `ls`, `copyFromLocal` und `mkdir`) funktionieren in Azure Storage wie erwartet. Nur die für die native (als DFS bezeichnete) HDFS-Implementierung spezifischen Befehle wie `fschk` und `dfsadmin` weisen in Azure Storage ein anderes Verhalten auf.

## <a name="overview-of-azure-data-lake-storage-gen1"></a>Übersicht über Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 ist ein unternehmensweites Hyperscale-Repository für Big Data-Analyseworkloads. Mit Azure Data Lake können Sie Daten von beliebiger Größe, Art und Erfassungsgeschwindigkeit für operative und explorative Analysen an einem Ort erfassen.

Greifen Sie über Hadoop (verfügbar mit einem HDInsight-Cluster) mithilfe der WebHDFS-kompatiblen REST-APIs auf Data Lake Storage Gen1 zu. Data Lake Storage Gen1 wurde speziell für Analysen der gespeicherten Daten konzipiert und ist für eine hohe Leistung in Datenanalyseszenarien optimiert. Es umfasst bereits vorkonfiguriert Funktionen, die für echte Anwendungsfälle von Unternehmen unerlässlich sind. Zu den Funktionen gehören Sicherheit, Verwaltbarkeit, Skalierbarkeit, Zuverlässigkeit und Verfügbarkeit.

Weitere Informationen zu Data Lake Storage Gen1 finden Sie in der ausführlichen [Übersicht über Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-overview.md).

Data Lake Storage Gen1 bietet u.a. die folgenden wichtigen Funktionen.

### <a name="compatibility-with-hadoop"></a>Kompatibilität mit Hadoop

Data Lake Storage Gen1 ist ein Apache Hadoop-Dateisystem, das mit HDFS kompatibel ist und mit dem Hadoop-Ökosystem funktioniert.  Ihre vorhandenen HDInsight-Anwendungen oder -Dienste, die die WebHDFS-API verwenden, können problemlos in Data Lake Storage Gen1 integriert werden. Data Lake Storage Gen1 macht auch eine WebHDFS-kompatible REST-Schnittstelle für Anwendungen verfügbar.

In Data Lake Storage Gen1 gespeicherte Daten können mühelos mit Hadoop-Analyseframeworks wie MapReduce oder Hive analysiert werden. Azure HDInsight-Cluster können für den direkten Zugriff auf in Data Lake Storage Gen1 gespeicherte Daten bereitgestellt und konfiguriert werden.

### <a name="unlimited-storage-petabyte-files"></a>Unbegrenzter Speicher, Dateigrößen bis in den Petabytebereich

Data Lake Storage Gen1 bietet unbegrenzten Speicher und eignet sich zum Speichern unterschiedlichster Daten für Analysezwecke. Es gibt keine Einschränkungen für Kontogrößen, Dateigrößen oder die Menge an Daten, die in einem Data Lake gespeichert werden kann. Data Lake Storage Gen1 unterstützt Dateigrößen vom Kilobyte- bis in den Petabytebereich und ist somit eine gute Wahl für die Speicherung von Daten jeglicher Art. Daten werden dauerhaft gespeichert, indem mehrere Kopien erstellt werden, die dann für unbegrenzte Zeit im Data Lake verbleiben können.

### <a name="performance-tuning-for-big-data-analytics"></a>Leistungsoptimierung für Big Data-Analysen

Data Lake Storage Gen1 ist für die Ausführung großer Analysesysteme ausgelegt, die zum Abfragen und Analysieren großer Datenmengen einen enormen Durchsatz erfordern. Der Data Lake verteilt Teile einer Datei auf mehrere einzelne Speicherserver. Dieses Setup verbessert beim Analysieren der Daten den Lesedurchsatz, wenn die Datei parallel gelesen wird.

### <a name="readiness-for-enterprise-highly-available-and-secure"></a>Bereitschaft für Unternehmen: Hoch verfügbar und sicher

Data Lake Storage Gen1 bietet Verfügbarkeit und Zuverlässigkeit nach Branchenstandard. Datenressourcen werden dauerhaft gespeichert: Redundante Kopien schützen vor unerwarteten Fehlern. Unternehmen können Data Lake Storage Gen1 in ihren Lösungen als wesentlichen Bestandteil ihrer vorhandenen Datenplattform einsetzen.

Data Lake Storage Gen1 bietet außerdem Sicherheit auf Unternehmensniveau für gespeicherte Daten. Weitere Informationen finden Sie unter [Schützen von Daten in Data Lake Storage Gen1](#DataLakeStoreSecurity).

### <a name="flexible-data-structures"></a>Flexible Datenstrukturen

Data Lake Storage Gen1 kann beliebige Daten ohne vorherige Umwandlung im nativen Format speichern. Es muss für Data Lake Storage Gen1 kein Schema definiert werden, bevor die Daten geladen werden. Das jeweilige Analyseframework interpretiert die Daten und definiert ein Schema zum Zeitpunkt der Analyse. Da Dateien beliebiger Größe und Formate gespeichert werden können, kann Data Lake Storage Gen1 strukturierte, teilweise strukturierte und unstrukturierte Daten verarbeiten.

Data Lake Storage Gen1-Container für Daten sind im Wesentlichen Ordner und Dateien. Sie arbeiten mit den gespeicherten Daten über SDKs, das Azure-Portal und Azure PowerShell. Solange Sie Ihre Daten unter Verwendung dieser Schnittstellen im Speicher ablegen und die entsprechenden Container verwenden, können Sie alle Datentypen speichern. Data Lake Storage Gen1 führt keine spezielle Verarbeitung von Daten basierend auf dem Typ der gespeicherten Daten durch.

## <a name="DataLakeStoreSecurity"></a>Datensicherheit in Data Lake Storage Gen1
Data Lake Storage Gen1 verwendet Azure Active Directory zur Authentifizierung und Zugriffssteuerungslisten (ACLs) zum Verwalten des Zugriffs auf Ihre Daten.

| **Feature** | **Beschreibung** |
| --- | --- |
| Authentication |Zur Identitäts- und Zugriffsverwaltung für alle in Data Lake Storage Gen1 gespeicherten Daten ist Data Lake Storage Gen1 in Azure Active Directory (Azure AD) integriert. Aufgrund dieser Integration profitiert Data Lake Storage Gen1 von allen Azure AD-Features. Dazu gehören mehrstufige Authentifizierung, bedingter Zugriff, rollenbasierte Zugriffssteuerung, Überwachung der Anwendungsnutzung, Sicherheitsüberwachung und -warnungen usw. Data Lake Storage Gen1 unterstützt das OAuth 2.0-Protokoll für die Authentifizierung mit der REST-Schnittstelle. Weitere Informationen finden Sie unter [Authentifizierung bei Azure Data Lake Storage Gen1 mit Azure Active Directory](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md).|
| Zugriffssteuerung |Data Lake Storage Gen1 ermöglicht eine Zugriffssteuerung durch die Unterstützung von POSIX-Berechtigungen, die durch das WebHDFS-Protokoll verfügbar gemacht werden. ACLs können für Stammordner, Unterordner und einzelne Dateien aktiviert werden. Weitere Informationen zur Funktionsweise von ACLs im Kontext von Data Lake Storage Gen1 finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md). |
| Verschlüsselung |Data Lake Storage Gen1 bietet auch eine Verschlüsselung für Daten, die im Konto gespeichert sind. Beim Erstellen eines Data Lake Storage Gen1-Kontos geben Sie die Einstellungen für die Verschlüsselung an. Sie können auswählen, ob Ihre Daten verschlüsselt werden sollen. Weitere Informationen finden Sie unter [Verschlüsselung von Daten in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-encryption.md). Anweisungen zur Bereitstellung einer verschlüsselungsbezogenen Konfiguration finden Sie unter [Erste Schritte mit Azure Data Lake Storage Gen1 über das Azure-Portal](../data-lake-store/data-lake-store-get-started-portal.md). |

Weitere Informationen zum Schutz von Daten in Data Lake Storage Gen1 finden Sie unter [Sichern von in Azure Data Lake Storage Gen1 gespeicherten Daten](../data-lake-store/data-lake-store-secure-data.md).

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Mit Data Lake Storage Gen1 kompatible Anwendungen
Data Lake Storage Gen1 ist mit den meisten Open Source-Komponenten im Hadoop-Ökosystem kompatibel. Außerdem ist eine problemlose Integration in andere Azure-Dienste möglich.  Über die folgenden Links erhalten Sie weitere Informationen zur Verwendung von Data Lake Storage Gen1 mit Open Source-Komponenten und anderen Azure-Diensten.

* Eine Liste der mit Data Lake Storage Gen1 interoperablen Open-Source-Anwendungen finden Sie unter [Open-Source-Big Data-Anwendungen, die mit Azure Data Lake Storage Gen1 verwendet werden können](../data-lake-store/data-lake-store-compatible-oss-other-applications.md).
* Informationen dazu, wie Data Lake Storage Gen1 mit anderen Azure-Diensten verwendet werden kann, um ein breiteres Spektrum von Szenarien zu ermöglichen, finden Sie unter [Integration von Azure Data Lake Storage Gen1 in andere Azure-Dienste](../data-lake-store/data-lake-store-integrate-with-other-services.md).
* Informationen dazu, wie Data Lake Storage Gen1 in Szenarien wie dem Erfassen, Verarbeiten, Herunterladen und Visualisieren von Daten verwendet werden kann, finden Sie unter [Verwenden von Azure Data Lake Storage Gen1 für Big Data-Anforderungen](../data-lake-store/data-lake-store-data-scenarios.md).

## <a name="data-lake-storage-gen1-file-system-adl"></a>Data Lake Storage Gen1-Dateisystem (adl://)
In Hadoop-Umgebungen (verfügbar mit HDInsight-Cluster) kann über das neue Dateisystem AzureDataLakeFilesystem (adl://) auf Data Lake Storage Gen1 zugegriffen werden. Für die Optimierung der Leistung von Anwendungen und Diensten, die adl:// verwenden, stehen Möglichkeiten zur Verfügung, die derzeit in WebHDFS nicht verfügbar sind. Daher bietet Ihnen Data Lake Storage Gen1 die Flexibilität, mit adl:// die optimale Leistung zu nutzen (empfohlen) oder vorhandenen Code beizubehalten, indem Sie die WebHDFS-API weiterhin direkt verwenden. Azure HDInsight nutzt die Möglichkeiten des AzureDataLakeFilesystem voll aus, um eine optimale Leistung für Data Lake Storage Gen1 bereitzustellen.

Sie können mithilfe der folgenden Elemente auf Ihre Daten in Data Lake Storage Gen1 zugreifen:

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Weitere Informationen zum Zugriff auf die Daten in Data Lake Storage Gen1 finden Sie unter [Verfügbare Aktionen mit den gespeicherten Daten](../data-lake-store/data-lake-store-get-started-portal.md#properties).



## <a name="next-steps"></a>Nächste Schritte

* [Einführung in Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Einführung in Azure Storage](../storage/common/storage-introduction.md)
