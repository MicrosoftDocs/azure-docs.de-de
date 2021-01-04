---
title: 'Speicher: Migrieren lokaler Apache Hadoop-Cluster zu Azure HDInsight'
description: Erfahren Sie mehr über bewährte Speichermethoden für die Migration von lokalen Hadoop-Clustern zu Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/10/2019
ms.openlocfilehash: 0594774533f306421f6f3d1260d074bd92b9c919
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92544867"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight"></a>Migrieren lokaler Apache Hadoop-Cluster zu Azure HDInsight

Dieser Artikel enthält Empfehlungen für die Datenspeicherung in Azure HDInsight-Systemen. Er ist Teil einer Reihe von Artikeln, die bewährte Methoden für die Migration von lokalen Apache Hadoop-Systemen zu Azure HDInsight enthalten.

## <a name="choose-right-storage-system-for-hdinsight-clusters"></a>Auswählen des richtigen Speichersystems für HDInsight-Cluster

Die Verzeichnisstruktur des lokalen Apache Hadoop-Dateisystems (HDFS) kann in Azure Blob Storage oder Azure Data Lake Storage neu erstellt werden. Sie können dann HDInsight-Cluster, die für Berechnungen verwendet werden, sicher löschen, ohne dass Benutzerdaten verloren gehen. Beide Dienste können sowohl als Standarddateisystem als auch ein zusätzliches Dateisystem für einen HDInsight-Cluster verwendet werden. Der HDInsight-Cluster und das Speicherkonto müssen sich in der gleichen Region befinden.

### <a name="azure-storage"></a>Azure Storage

HDInsight-Cluster können den Blobcontainer in Azure Storage entweder als Standarddateisystem oder als ein zusätzliches Dateisystem verwenden.  Das Speicherkonto im Standard-Tarif wird für die Verwendung mit HDInsight-Clustern unterstützt. Der Premium-Tarif wird nicht unterstützt. Der standardmäßige Blobcontainer speichert clusterspezifische Informationen wie etwa Auftragsverlauf und Protokolle.  Das Freigeben eines Blobcontainers als Standarddateisystem für mehrere Cluster wird nicht unterstützt.

Die bei der Erstellung definierten Speicherkonten und ihre jeweiligen Schlüssel werden in `%HADOOP_HOME%/conf/core-site.xml` auf den Clusterknoten gespeichert. Auf sie kann auch im Abschnitt „Custom core site“ der HDFS-Konfiguration auf der Ambari-Benutzeroberfläche zugegriffen werden. Der Speicherkontoschlüssel ist standardmäßig verschlüsselt, und ein benutzerdefiniertes Entschlüsselungsskript wird verwendet, um die Schlüssel vor der Übergabe an Hadoop-Daemons zu entschlüsseln. Die Aufträge, darunter Hive, MapReduce, Hadoop Streaming und Pig, enthalten eine Beschreibung von Speicherkonten und Metadaten.

Für Azure Storage kann eine Georeplikation durchgeführt werden. Obwohl die Georeplikation geographische Wiederherstellung und Datenredundanz bietet, wirkt sich ein Failover auf den georeplizierten Standort stark auf die Leistung aus und kann zusätzliche Kosten nach sich ziehen. Es wird empfohlen, die Georeplikation mit Bedacht und nur dann auszuwählen, wenn der Nutzen der Daten die zusätzlichen Kosten rechtfertigt.

Für den Zugriff auf Daten, die in Azure Storage gespeichert sind, kann eines der folgenden Formate verwendet werden:

|Datenzugriffsformat |BESCHREIBUNG |
|---|---|
|`wasb:///`|Zugriff auf Standardspeicher über unverschlüsselte Verbindungen.|
|`wasbs:///`|Zugriff auf Standardspeicher über verschlüsselte Verbindungen.|
|`wasb://<container-name>@<account-name>.blob.core.windows.net/`|Wird bei einer Verbindung mit einem nicht standardmäßigen Speicherkonto verwendet. |

Unter [Skalierbarkeitsziele für Storage Standard-Konten](../../storage/common/scalability-targets-standard-account.md) sind die aktuellen Beschränkungen für Azure Storage-Konten aufgelistet. Wenn die Anforderungen der Anwendung die Skalierbarkeitsziele eines einzelnen Speicherkontos überschreiten, kann die Anwendung so erstellt werden, dass mehrere Speicherkonten verwendet werden, und dann können Datenobjekte basierend auf diesen Speicherkonten partitioniert werden.

[Azure Storage Analytics](../../storage/common/storage-analytics.md) bietet Metriken für alle Speicherdienste, und das Azure-Portal kann so konfiguriert werden, dass Metriken zur Visualisierung anhand von Diagrammen gesammelt werden. Es können Warnungen erstellt werden, damit Benachrichtigungen erfolgen, sobald Schwellenwerte für Speicherressourcenmetriken erreicht werden.

Azure Storage ermöglicht das [vorläufige Löschen für Blobobjekte](../../storage/blobs/soft-delete-blob-overview.md), sodass Daten einfacher wiederhergestellt werden können, wenn sie versehentlich von einer Anwendung oder einem anderen Benutzer des Speicherkontos geändert oder gelöscht wurden.

Sie können [Blob-Momentaufnahmen](/rest/api/storageservices/creating-a-snapshot-of-a-blob) erstellen. Eine Momentaufnahme ist eine schreibgeschützte Version eines Blobs, die zu einem bestimmten Zeitpunkt erstellt wird und eine Möglichkeit zum Sichern eines Blobs darstellt. Nachdem eine Momentaufnahme erstellt wurde, kann sie zwar gelesen, kopiert oder gelöscht, aber nicht geändert werden.

> [!Note]
> Bei älteren Versionen lokaler Hadoop-Distributionen ohne „wasbs“-Zertifikat muss dieses in den Java-Vertrauensspeicher importiert werden.

Die folgenden Methoden können zum Importieren von Zertifikaten in den Java-Vertrauensspeicher verwendet werden:

Laden Sie das Azure Blob-TLS/SSL-Zertifikat in eine Datei herunter.

```bash
echo -n | openssl s_client -connect <storage-account>.blob.core.windows.net:443 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > Azure_Storage.cer
```

Importieren Sie die oben angegebene Datei in den Java-Vertrauensspeicher auf allen Knoten.

```bash
keytool -import -trustcacerts -keystore /path/to/jre/lib/security/cacerts -storepass changeit -noprompt -alias blobtrust -file Azure_Storage.cer
```

Stellen Sie sicher, dass sich das hinzugefügte Zertifikat im Vertrauensspeicher befindet.

```bash
keytool -list -v -keystore /path/to/jre/lib/security/cacerts
```

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Verwenden von Azure Storage mit Azure HDInsight-Clustern](../hdinsight-hadoop-use-blob-storage.md)
- [Skalierbarkeitsziele für Storage Standard-Konten](../../storage/common/scalability-targets-standard-account.md)
- [Skalierbarkeits- und Leistungsziele für Blob Storage](../../storage/blobs/scalability-targets.md).
- [Checkliste zu Leistung und Skalierbarkeit von Microsoft Azure Storage](../../storage/blobs/storage-performance-checklist.md)
- [Microsoft Azure-Speicher: Überwachung, Diagnose und Problembehandlung](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)
- [Überwachen eines Speicherkontos im Azure-Portal](../../storage/common/storage-monitor-storage-account.md)

### <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 implementiert das HDFS- und POSIX-Zugriffssteuerungsmodell. Es bietet erstklassige Integration mit Azure AD für detaillierte Zugriffssteuerung. Es gibt keine Beschränkungen hinsichtlich der Größe von Daten, die gespeichert werden können, oder der Möglichkeit zum Ausführen hochgradiger paralleler Analysen.

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Erstellen von HDInsight-Clustern mit Data Lake Storage Gen1 mithilfe des Azure-Portals](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
- [Verwenden von Data Lake Storage Gen1 mit Azure HDInsight-Clustern](../hdinsight-hadoop-use-data-lake-storage-gen1.md)

### <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 ist das neueste Speicherangebot. Es vereint die Kernfunktionen der ersten Generation von Azure Data Lake Storage Gen1 mit einem Hadoop-kompatiblen Dateisystemendpunkt, der direkt in Azure Blob Storage integriert ist. Diese Erweiterung verbindet die Vorteile eines Objektspeichers hinsichtlich Skalierbarkeit und Kosten mit der Zuverlässigkeit und Leistung, die normalerweise nur lokale Dateisysteme bieten.

Azure Data Lake Storage Gen2 basiert auf [Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md) und ermöglicht Ihnen das Arbeiten mit Daten über das Dateisystem und den Objektspeicher. Funktionen von [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml), wie z. B. Dateisystemsemantik, Sicherheit auf Dateiebene und Skalierbarkeit, werden mit den kostengünstigen, mehrstufigen Speicherlösungen, Hochverfügbarkeits- und Notfallwiederherstellungsfunktionen und einem umfassenden Ökosystem aus SDKs und Tools von [Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md) kombiniert. In Data Lake Storage Gen2 bleiben alle Vorzüge des Objektspeichers erhalten, während gleichzeitig die Vorteile einer für Analyseworkloads optimierten Dateisystemschnittstelle zum Tragen kommen.

Ein wesentliches Merkmal von Data Lake Storage Gen2 ist die Erweiterung des Blob Storage-Diensts um einen [hierarchischen Namespace](../../storage/blobs/data-lake-storage-namespace.md), der Objekte und Dateien für einen leistungsstarken Datenzugriff in einer Verzeichnishierarchie organisiert.  Die hierarchische Struktur macht es möglich, dass Vorgänge wie das Umbenennen oder Löschen eines Verzeichnisses zu einzelnen atomaren Metadatenvorgängen im Verzeichnis werden, anstatt alle Objekte aufzuzählen und zu verarbeiten, die das Namenspräfix des Verzeichnisses gemeinsam haben.

In der Vergangenheit mussten bei cloudbasierten Analysen Kompromisse in puncto Leistung, Verwaltung und Sicherheit eingegangen werden. Hier die wichtigste Features von Azure Data Lake Storage (ADLS) Gen2:

- **Hadoop-kompatibler Zugriff:** Azure Data Lake Storage Gen2 ermöglicht einen mit einem [Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) vergleichbaren Zugriff auf Daten sowie deren Verwaltung. Der neue [ABFS-Treiber](../../storage/blobs/data-lake-storage-abfs-driver.md) ist in allen Apache Hadoop-Umgebungen verfügbar, die in [Azure HDInsight](../index.yml) enthalten sind. Dieser Treiber ermöglicht Ihnen den Zugriff auf Daten, die in Data Lake Storage Gen2 gespeichert sind.

- **Obermenge von POSIX-Berechtigungen:** Das Sicherheitsmodell für Data Lake Gen2 unterstützt vollständig Zugriffssteuerungslisten und POSIX-Berechtigungen sowie zusätzliche Granularität speziell für Data Lake Storage Gen2. Die Einstellungen können über Verwaltungstools oder Frameworks wie Hive und Spark konfiguriert werden.

- **Kosteneffizienz:** Data Lake Storage Gen2 bietet kostengünstige Speicherkapazität und -transaktionen. Während Daten ihren gesamten Lebenszyklus durchlaufen, ändern sich Abrechnungstarife, um Kosten durch integrierte Funktionen wie [Azure Blob Storage-Lebenszyklus](../../storage/blobs/storage-lifecycle-management-concepts.md) zu minimieren.

- **Unterstützung von Blob Storage-Tools, -Frameworks und -Apps:** Data Lake Storage Gen2 funktioniert weiterhin mit einer Vielzahl von Tools, Frameworks und Anwendungen, die es heute bereits für Blob Storage gibt.

- **Optimierter Treiber:** Der ABFS-Treiber (Azure Blob File System) ist speziell für Big Data-Analysen [optimiert](../../storage/blobs/data-lake-storage-abfs-driver.md). Die entsprechenden REST-APIs werden über den DFS-Endpunkt (dfs.core.windows.net) bereitgestellt.

Für den Zugriff auf Daten, die in ADLS Gen2 gespeichert sind, kann eines der folgenden Formate verwendet werden:
- `abfs:///`: Zugriff auf die standardmäßige Data Lake Storage-Instanz für den Cluster
- `abfs://file_system@account_name.dfs.core.windows.net`: Wird bei einer Verbindung mit einer nicht standardmäßigen Data Lake Storage-Instanz verwendet.

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Einführung in Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-introduction.md)
- [Der Azure-Blobdateisystemtreiber (ABFS.md)](../../storage/blobs/data-lake-storage-abfs-driver.md)
- [Verwenden von Azure Data Lake Storage Gen2 mit Azure HDInsight-Clustern](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

## <a name="secure-azure-storage-keys-within-on-premises-hadoop-cluster-configuration"></a>Schützen von Azure Storage-Schlüsseln innerhalb der lokalen Hadoop-Clusterkonfiguration

Die Azure Storage-Schlüssel, die den Hadoop-Konfigurationsdateien hinzugefügt werden, sorgen für Konnektivität zwischen lokalem HDFS und Azure Blob Storage. Diese Schlüssel können durch Verschlüsseln mit dem Framework des Hadoop-Anmeldeinformationsanbieters geschützt werden. Nach der Verschlüsselung können sie gespeichert und auf sichere Weise darauf zugegriffen werden.

**So stellen Sie die Anmeldeinformationen bereit:**

```bash
hadoop credential create fs.azure.account.key.account.blob.core.windows.net -value <storage key> -provider jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks/file
```

**So fügen Sie den oben angegebenen Anbieterpfad zur Datei „core-site.xml“ oder zur Ambari-Konfiguration unter „Custom core-site“ hinzu:**

```xml
<property>
    <name>hadoop.security.credential.provider.path</name>
        <value>
        jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks
        </value>
    <description>Path to interrogate for protected credentials.</description>
</property>
```

> [!Note]
> Die Anbieterpfadeigenschaft kann auch statt der Speicherung des Schlüssels auf Clusterebene in „core-site.xml“ wie folgt zur „distcp“-Befehlszeile hinzugefügt werden:

```bash
hadoop distcp -D hadoop.security.credential.provider.path=jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks /user/user1/ wasb:<//yourcontainer@youraccount.blob.core.windows.net/>user1
```

## <a name="restrict-azure-storage-data-access-using-sas"></a>Einschränken des Azure Storage-Datenzugriffs mithilfe von SAS

HDInsight hat standardmäßig vollen Zugriff auf Daten in Azure Storage-Konten, die mit dem Cluster verbunden sind. Shared Access Signatures (SAS) für den Blobcontainer können zum Einschränken des Zugriffs auf die Daten verwendet werden, indem beispielsweise Benutzer schreibgeschützten Zugriff auf die Daten erhalten.

### <a name="using-the-sas-token-created-with-python"></a>Verwenden des mit Python erstellten SAS-Tokens

1. Öffnen Sie die Datei [SASToken.py](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature/blob/master/Python/SASToken.py), und ändern Sie die folgenden Werte:

    |Tokeneigenschaft|BESCHREIBUNG|
    |---|---|
    |policy_name|Der zu verwendende Name für die gespeicherte Richtlinie, die erstellt wird.|
    |storage_account_name|Der Name Ihres Speicherkontos.|
    |storage_account_key|Der Schlüssel für das Speicherkonto.|
    |storage_container_name|Der Container im Speicherkonto, auf den Sie den Zugriff beschränken möchten.|
    |example_file_path|Der Pfad zu einer Datei, die in den Container hochgeladen wird.|

2. Die Datei „SASToken.py“ enthält bereits die Berechtigungen `ContainerPermissions.READ + ContainerPermissions.LIST` und kann basierend auf dem Anwendungsfall angepasst werden.

3. Führen Sie das Skript wie folgt aus: `python SASToken.py`

4. Nach Abschluss des Skripts wird ein SAS-Token ähnlich dem folgenden Text angezeigt: `sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14`

5. Um den Zugriff auf einen Container mit Shared Access Signature zu beschränken, fügen Sie einen benutzerdefinierten Eintrag zur „core-site“-Konfiguration für den Cluster in der „Add“-Eigenschaft im Bereich „Custom core-site“ unter den erweiterten Einstellungen für HDFS-Konfigurationen in Ambari hinzu.

6. Verwenden Sie für die Felder **Key** und **Value** die folgenden Werte:

    **Key** : `fs.azure.sas.YOURCONTAINER.YOURACCOUNT.blob.core.windows.net` **Value** : Der SAS-Schlüssel, der von der Python-Anwendung in Schritt 4 oben zurückgegeben wurde

7. Klicken Sie auf die Schaltfläche **Add** , um diesen Schlüssel und Wert zu speichern. Klicken Sie dann auf die Schaltfläche **Save** , um die Konfigurationsänderungen zu speichern. Fügen Sie bei Aufforderung eine Beschreibung der Änderung hinzu (z.B. „Hinzufügen des SAS-Speicherzugriffs“), und klicken Sie anschließend auf **Speichern**.

8. Wählen Sie auf der Ambari-Webbenutzeroberfläche in der Liste auf der linken Seite „HDFS“ und dann in der Dropdownliste „Service Actions“ (Dienstaktionen) auf der rechten Seite **Restart All Affected** (Alle betroffenen Dienste neu starten) aus. Klicken Sie bei entsprechender Aufforderung auf **Confirm Restart All** (Neustart aller Dienste bestätigen).

9. Wiederholen Sie diesen Vorgang für MapReduce2 und YARN.

Bei der Verwendung von SAS-Token in Azure sind drei wichtige Punkte zu beachten:

1. Wenn SAS-Token mit Berechtigungen zum Lesen und Auflisten (READ + LIST) erstellt werden, können Benutzer, die auf den Blobcontainer mit diesem SAS-Token zugreifen, keine Daten schreiben und löschen. Benutzer, die auf den Blobcontainer mit diesem SAS-Token zugreifen und einen Schreib- oder Löschvorgang ausführen möchten, erhalten eine Meldung wie `"This request is not authorized to perform this operation"`.

2. Wenn die SAS-Token mit den Berechtigungen `READ + LIST + WRITE` generiert werden (um nur `DELETE` einzuschränken), wird mit Befehlen wie `hadoop fs -put` zuerst in eine `\_COPYING\_`-Datei geschrieben und dann versucht, die Datei umzubenennen. Dieser HDFS-Vorgang ist `copy+delete` für WASB zugeordnet. Da die Berechtigung `DELETE` nicht angegeben wurde, treten beim „-put“-Vorgang Fehler auf. Der Vorgang `\_COPYING\_` ist ein Hadoop-Feature, das der Gleichzeitigkeitssteuerung dienen soll. Derzeit besteht keine Möglichkeit, nur den Löschvorgang (DELETE) ohne Auswirkungen auf den Schreibvorgang (WRITE) zu beschränken.

3. Leider funktionieren der Hadoop-Anmeldeinformationsanbieter und der Anbieter des Entschlüsselungsschlüssels (ShellDecryptionKeyProvider) aktuell nicht mit SAS-Token, sodass derzeit kein Schutz vor Sichtbarkeit besteht.

Weitere Informationen finden Sie unter [Verwenden von Azure Storage Shared Access Signatures zum Einschränken des Zugriffs auf Daten mit HDInsight](../hdinsight-storage-sharedaccesssignature-permissions.md).

## <a name="use-data-encryption-and-replication"></a>Verwenden von Datenverschlüsselung und Replikation

Alle Daten werden automatisch mit [Storage Service Encryption (SSE)](../../storage/common/storage-service-encryption.md) verschlüsselt, wenn sie in Azure Storage geschrieben werden. Die Daten im Azure Storage-Konto werden immer für Hochverfügbarkeit repliziert.  Wenn Sie ein Speicherkonto erstellen, können Sie eine der folgenden Replikationsoptionen auswählen:

- [Lokal redundanter Speicher (LRS)](../../storage/common/storage-redundancy.md#locally-redundant-storage)
- [Zonenredundanter Speicher (ZRS)](../../storage/common/storage-redundancy.md#zone-redundant-storage)
- [Georedundanter Speicher (GRS)](../../storage/common/storage-redundancy.md#geo-redundant-storage)
- [Georedundanter Speicher mit Lesezugriff (RA-GRS)](../../storage/common/storage-redundancy.md)

Azure Storage bietet lokal redundanten Speicher (LRS), aber Sie sollten wichtige Daten auch in ein anderes Azure Storage-Konto in einer anderen Region kopieren, und zwar mit einer Häufigkeit, die den Anforderungen des Notfallwiederherstellungsplans entspricht. Es gibt verschiedene Methoden zum Kopieren von Daten, z. B. [ADLCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md),[DistCp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html), [Azure PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md) oder [Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md).  Außerdem wird empfohlen, Zugriffsrichtlinien für das Azure Storage-Konto zu erzwingen, um versehentliches Löschen zu verhindern.

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Azure Storage-Replikation](../../storage/common/storage-redundancy.md)
- [Leitfaden zur Notfallwiederherstellung für Azure Data Lake Storage Gen1 (ADLS)](../../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

## <a name="attach-additional-azure-storage-accounts-to-cluster"></a>Anfügen zusätzlicher Azure Storage-Konten an den Cluster

Beim HDInsight-Erstellungsprozess wird ein Azure Storage-, Azure Data Lake Storage Gen1- oder Azure Data Lake Storage Gen2-Konto als Standarddateisystem ausgewählt. Zusätzlich zu diesem Standardspeicherkonto können während oder nach der Clustererstellung weitere Speicherkonten aus dem gleichen oder einem anderen Azure-Abonnement hinzugefügt werden.

Ein zusätzliches Speicherkonto kann auf eine der folgenden Arten hinzugefügt werden:
- Hinzufügen des Speicherkontonamens im Bereich „Custom core-site“ unter den erweiterten Einstellungen für HDFS-Konfigurationen in Ambari und Neustarten der Dienste
- Mithilfe einer [Skriptaktion](../hdinsight-hadoop-add-storage.md) durch Übergeben von Speicherkontoname und -schlüssel

> [!Note]
> In berechtigten Anwendungsfällen können die Grenzwerte für Azure Storage über eine Anforderung beim [Azure-Support](https://azure.microsoft.com/support/faq/) erhöht werden.

Weitere Informationen finden Sie unter [Hinzufügen zusätzlicher Speicherkonten zu HDInsight](../hdinsight-hadoop-add-storage.md).

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie den nächsten Artikel in dieser Reihe: [Bewährte Methoden für die Datenmigration von lokalen Hadoop-Clustern zu Azure HDInsight](apache-hadoop-on-premises-migration-best-practices-data-migration.md)
