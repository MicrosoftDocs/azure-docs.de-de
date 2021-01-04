---
title: Azure HDInsight – Häufig gestellte Fragen
description: Häufig gestellte Fragen zu HDInsight
keywords: Häufig gestellte Fragen, FAQ
author: Ramakoni1
ms.author: ramakoni
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,seoapr2020
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: 0240510a2232bd12a94d5cdd59672270289e5e8f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011828"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight: Häufig gestellte Fragen

Dieser Artikel bietet Antworten auf einige der häufigsten Fragen zur Ausführung von [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="creating-or-deleting-hdinsight-clusters"></a>Erstellen oder Löschen von HDInsight-Clustern

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>Wie stelle ich einen HDInsight-Cluster bereit?

Die verfügbaren HDInsight-Clustertypen und die Bereitstellungsmethoden finden Sie unter [Einrichten von Clustern in HDInsight mit Apache Hadoop, Apache Spark, Apache Kafka usw.](./hdinsight-hadoop-provision-linux-clusters.md)

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>Wie lösche ich einen vorhandenen HDInsight-Cluster?

Weitere Informationen zum Löschen eines Clusters, der nicht mehr verwendet wird, finden Sie unter [Löschen eines HDInsight-Clusters](hdinsight-delete-cluster.md).

Versuchen Sie, zwischen Erstell- und Löschvorgängen mindestens 30 bis 60 Minuten zu warten. Andernfalls tritt bei dem Vorgang unter Umständen ein Fehler mit der folgenden Fehlermeldung auf:

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>Wie wähle ich die richtige Anzahl von Kernen oder Knoten für meine Workload aus?

Die geeignete Anzahl von Kernen und weitere Konfigurationsoptionen hängen von verschiedenen Faktoren ab.

Weitere Informationen finden Sie unter [Kapazitätsplanung für HDInsight-Cluster](./hdinsight-capacity-planning.md).

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>Welche unterschiedlichen Knotentypen gibt es in einem HDInsight-Cluster?

Lesen Sie dazu [Ressourcentypen in Azure HDInsight-Clustern](hdinsight-virtual-network-architecture.md#resource-types-in-azure-hdinsight-clusters).

### <a name="what-are-the-best-practices-for-creating-large-hdinsight-clusters"></a>Wie lauten die bewährten Methoden zur Erstellung von großen HDInsight-Clustern?

1. Wir empfehlen Ihnen, HDInsight-Cluster mit einer [benutzerdefinierten Ambari-Datenbank](./hdinsight-custom-ambari-db.md) einzurichten, um die Clusterskalierbarkeit zu verbessern.
2. Verwenden Sie [Azure Data Lake Storage Gen2](./hdinsight-hadoop-use-data-lake-storage-gen2.md) für die Erstellung von HDInsight-Clustern, um die höhere Bandbreite und weitere Leistungsmerkmale von Azure Data Lake Storage Gen2 zu nutzen.
3. Hauptknoten sollten eine ausreichende Größe haben, um mehrere Masterdienste abdecken zu können, die auf diesen Knoten ausgeführt werden.
4. Für einige spezifische Workloads, z. B. Interactive Query, werden ebenfalls größere Zookeeper-Knoten benötigt. Beachten Sie hierbei die Mindestanzahl von acht Kern-VMs.
5. Verwenden Sie bei Hive und Spark den [externen Hive-Metastore](./hdinsight-use-external-metadata-stores.md).

## <a name="individual-components"></a>Einzelne Komponenten

### <a name="can-i-install-additional-components-on-my-cluster"></a>Kann ich zusätzliche Komponenten in meinem Cluster installieren?

Ja. Zum Installieren zusätzlicher Komponenten oder Anpassen der Clusterkonfiguration können Sie Folgendes verwenden:

- Skripts während oder nach der Erstellung. Skripts werden über [Skriptaktion](./hdinsight-hadoop-customize-cluster-linux.md) aufgerufen. „Skriptaktion“ ist eine Konfigurationsoption, die im Azure-Portal, in HDInsight Windows PowerShell-Cmdlets oder beim HDInsight .NET SDK verwendet werden kann. Diese Konfigurationsoption kann im Azure-Portal, in HDInsight Windows PowerShell-Cmdlets oder mit dem HDInsight .NET SDK verwendet werden.

- [HDInsight-Anwendungsplattform](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) zur Installation von Anwendungen.

Eine Liste der unterstützten Komponenten finden Sie unter [Verfügbare Apache Hadoop-Komponenten in verschiedenen Versionen von HDInsight](./hdinsight-component-versioning.md#apache-components-available-with-different-hdinsight-versions).

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>Kann ich die einzelnen Komponenten aktualisieren, die im Cluster vorinstalliert sind?

Wenn Sie integrierte Komponenten oder Anwendungen aktualisieren, die in Ihrem Cluster vorinstalliert sind, wird die resultierende Konfiguration von Microsoft nicht unterstützt. Diese Systemkonfigurationen wurden nicht von Microsoft getestet. Versuchen Sie, eine andere Version des HDInsight-Clusters zu verwenden, in der die aktualisierte Version der Komponente möglicherweise bereits vorinstalliert ist.

Beispielsweise wird das Aktualisieren von Hive als einzelne Komponente nicht unterstützt. HDInsight ist ein verwalteter Dienst, und viele Dienste sind in den Ambari-Server integriert und wurden getestet. Wenn Hive eigenständig aktualisiert wird, werden die indizierten Binärdateien anderer Komponenten geändert. Das führt zu Integrationsproblemen mit Komponenten in Ihrem Cluster.

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>Können Spark und Kafka im selben HDInsight-Cluster ausgeführt werden?

Nein, Apache Kafka und Apache Spark können nicht im selben HDInsight-Cluster ausgeführt werden. Erstellen Sie separate Cluster für Kafka und Spark, um Probleme aufgrund von Ressourcenkonflikten zu vermeiden.

### <a name="how-do-i-change-timezone-in-ambari"></a>Wie ändere ich die Zeitzone in Ambari?

1. Öffnen Sie die Ambari-Webbenutzeroberfläche unter `https://CLUSTERNAME.azurehdinsight.net`. Dabei entspricht CLUSTERNAME dem Namen Ihres Clusters.
2. Wählen Sie oben rechts „Admin (Administrator) | Settings (Einstellungen)“ aus. 

   ![Ambari-Einstellungen](media/hdinsight-faq/ambari-settings.png)

3. Wählen Sie im Fenster „User Settings“ (Benutzereinstellungen) die neue Zeitzone aus der Dropdownliste „Timezone“ (Zeitzone) aus, und klicken Sie auf „Save“ (Speichern).

   ![Ambari-Benutzereinstellungen](media/hdinsight-faq/ambari-user-settings.png)

## <a name="metastore"></a>Metastore

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-database"></a>Wie kann ich vom vorhandenen Metastore zu Azure SQL-Datenbank migrieren? 

Informationen zum Migrieren von SQL Server zu Azure SQL-Datenbank finden Sie unter [Tutorial: Offlinemigration von SQL Server zu einer Einzel- oder Pooldatenbank in Azure SQL-Datenbank mit DMS](../dms/tutorial-sql-server-to-azure-sql.md).

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>Wird der Hive-Metastore zusammen mit dem Cluster gelöscht?

Dies hängt vom Typ des Metastores ab, der für den Cluster konfiguriert ist.

Standardmetastore: Der Standardmetastore ist Teil des Clusterlebenszyklus. Wenn Sie einen Cluster löschen, werden der entsprechende Metastore und die jeweiligen Metadaten ebenfalls gelöscht.

Benutzerdefinierter Metastore: Der Lebenszyklus des Metastores ist nicht an den Lebenszyklus eines Clusters gebunden. Deshalb können Sie Cluster erstellen und löschen, ohne Metadaten zu verlieren. Metadaten, wie z. B. Ihre Hive-Schemas, bleiben auch erhalten, nachdem der HDInsight-Cluster gelöscht und neu erstellt wurde.

Weitere Informationen finden Sie unter [Verwenden von externen Metadatenspeichern in Azure HDInsight](hdinsight-use-external-metadata-stores.md).

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>Werden bei der Migration eines Hive-Metastores auch die Standardrichtlinien der Ranger-Datenbank migriert?

Nein, die Richtliniendefinition ist in der Ranger-Datenbank enthalten. Daher wird die Richtlinie bei der Migration der Ranger-Datenbank migriert.

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-the-other-way-around"></a>Kann ich einen Hive-Metastore von einem Cluster mit Enterprise-Sicherheitspaket (ESP) zu einem Cluster ohne ESP migrieren und umgekehrt?

Ja, Sie können eine Hive-Metastore von einem Cluster mit Enterprise-Sicherheitspaket zu einem Cluster ohne ESP migrieren.

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>Wie kann ich die Größe einer Hive-Metastore-Datenbank schätzen?

In einem Hive-Metastore werden die Metadaten für Datenquellen gespeichert, die vom Hive-Server verwendet werden. Die Größenanforderungen sind teilweise abhängig von der Anzahl und Komplexität Ihrer Hive-Datenquellen. Diese Elemente können nicht vorab geschätzt werden. Wie in den [Richtlinien für Hive-Metastore](hdinsight-use-external-metadata-stores.md#hive-metastore-guidelines) beschrieben, können Sie mit einem S2-Tarif beginnen. Dieser Tarif bietet 50 DTU und 250 GB Speicher, und wenn ein Engpass angezeigt wird, skalieren Sie die Datenbank hoch.

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>Werden andere Datenbanken als Azure SQL-Datenbank als externer Metastore unterstützt?

Nein, Microsoft unterstützt nur Azure SQL-Datenbank als externen benutzerdefinierten Metastore.

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>Kann ich einen Metastore für mehrere Cluster verwenden?

Ja, Sie können einen benutzerdefinierten Metastore für mehrere Cluster verwenden, sofern sie dieselbe HDInsight-Version verwenden.

## <a name="connectivity-and-virtual-networks"></a>Konnektivität und virtuelle Netzwerke  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>Was geschieht, wenn ich die Ports 22 und 23 in meinem Netzwerk blockiere?

Wenn Sie die Ports 22 und 23 blockieren, haben Sie keinen SSH-Zugriff auf den Cluster. Diese Ports werden vom HDInsight-Dienst nicht genutzt.

Weitere Informationen finden Sie in den folgenden Dokumenten:

- [Ports für Apache Hadoop-Dienste in HDInsight](./hdinsight-hadoop-port-settings-for-services.md)

- [Sichern des eingehenden Datenverkehrs für HDInsight-Cluster in einem virtuellen Netzwerk mit privatem Endpunkt](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)

- [HDInsight-Verwaltungs-IP-Adressen](./hdinsight-management-ip-addresses.md)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>Kann ich einen zusätzlichen virtuellen Computer im selben Subnetz wie einen HDInsight-Cluster bereitstellen?

Ja, Sie können einen zusätzlichen virtuellen Computer im selben Subnetz wie einen HDInsight-Cluster bereitstellen. Die folgenden Konfigurationen sind möglich:

- Edgeknoten: Sie können dem Cluster einen weiteren Edgeknoten hinzufügen, wie unter [Verwenden leerer Edgeknoten in Apache Hadoop-Clustern in HDInsight](hdinsight-apps-use-edge-node.md) beschrieben.

- Eigenständige Knoten:  Sie können demselben Subnetz einen eigenständigen virtuellen Computer hinzufügen und über diesen virtuellen Computer auf den Cluster zugreifen, indem Sie den privaten Endpunkt `https://<CLUSTERNAME>-int.azurehdinsight.net` verwenden. Weitere Informationen finden Sie unter [Steuern des Netzwerkdatenverkehrs](./control-network-traffic.md).

### <a name="should-i-store-data-on-the-local-disk-of-an-edge-node"></a>Sollten Daten auf dem lokalen Datenträger eines Edgeknotens gespeichert werden?

Nein, das Speichern von Daten auf einem lokalen Datenträger ist keine gute Idee. Wenn der Knoten ausfällt, gehen alle lokal gespeicherten Daten verloren. Es wird empfohlen, Daten in Azure Data Lake Storage Gen2 oder Azure Blob Storage zu speichern, oder eine Azure Files-Freigabe zum Speichern der Daten bereitzustellen.


### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>Kann ich einen vorhandenen HDInsight-Cluster einem anderen virtuellen Netzwerk hinzufügen?

Nein, das ist nicht möglich. Das virtuelle Netzwerk sollte zum Zeitpunkt der Bereitstellung angegeben werden. Wenn während der Bereitstellung kein virtuelles Netzwerk angegeben wird, wird ein internes Netzwerk erstellt, auf das ein externer Zugriff nicht möglich ist. Weitere Informationen finden Sie unter [Hinzufügen von HDInsight zu einem vorhandenen virtuellen Netzwerk](hdinsight-plan-virtual-network-deployment.md#existingvnet).

## <a name="security-and-certificates"></a>Sicherheit und Zertifikate

### <a name="what-are-the-recommendations-for-malware-protection-on-azure-hdinsight-clusters"></a>Welche Empfehlungen gibt es für den Schutz vor Schadsoftware in Azure HDInsight-Clustern?

Informationen zum Schutz vor Schadsoftware finden Sie unter [Microsoft Antimalware für Azure Cloud Services und Virtual Machines](../security/fundamentals/antimalware.md).

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>Wie erstelle ich eine Keytab-Datei für einen HDInsight-Cluster mit Enterprise-Sicherheitspaket?

Erstellen Sie eine Kerberos-Keytab-Datei für Ihren Domänenbenutzernamen. Sie können diese Keytab-Datei später für die kennwortlose Authentifizierung bei Remoteclustern verwenden, die in die Domäne eingebunden sind. Der Domänenname wird in Großbuchstaben angegeben:

```shell
ktutil
ktutil: addent -password -p <username>@<DOMAIN.COM> -k 1 -e RC4-HMAC
Password for <username>@<DOMAIN.COM>: <password>
ktutil: wkt <username>.keytab
ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>Kann ich einen vorhandenen Azure Active Directory-Mandanten verwenden, um einen HDInsight-Cluster mit Enterprise-Sicherheitspaket zu erstellen?

Aktivieren Sie Azure Active Directory Domain Services (Azure AD DS), bevor Sie mit der Erstellung eines HDInsight-Clusters mit ESP beginnen. Open-Source-Hadoop nutzt für die Authentifizierung Kerberos (und nicht OAuth).

Um virtuelle Computer in eine Domäne einzubinden, ist ein Domänencontroller erforderlich. Azure AD DS ist der verwaltete Domänencontroller und wird als Erweiterung von Azure Active Directory angesehen. Azure AD DS erfüllt alle Kerberos-Anforderungen zum Erstellen eines sicheren Hadoop-Clusters auf verwaltete Weise. HDInsight wird als verwalteter Dienst in Azure AD DS integriert, um Sicherheit zu gewährleisten.

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>Kann ich in einem AAD DS-Setup mit Secure LDAP ein selbstsigniertes Zertifikat verwenden und einen Cluster mit Enterprise-Sicherheitspaket bereitstellen?

Die Verwendung eines von einer Zertifizierungsstelle ausgestellten Zertifikats wird empfohlen. Doch unter ESP wird auch die Verwendung eines selbstsignierten Zertifikats unterstützt. Weitere Informationen finden Sie unter

- [Aktivieren von Azure Active Directory Domain Services](domain-joined/apache-domain-joined-configure-using-azure-adds.md#enable-azure-ad-ds)

- [Tutorial: Konfigurieren von Secure LDAP (LDAPS) für eine verwaltete Azure AD Domain Services-Domäne](../active-directory-domain-services/tutorial-configure-ldaps.md)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>Wie kann ich in Ranger angezeigte Anmeldeaktivitäten abrufen?

Microsoft empfiehlt, Azure Monitor-Protokolle zu aktivieren, wie in [Verwenden von Azure Monitor-Protokollen zum Überwachen von HDInsight-Clustern](./hdinsight-hadoop-oms-log-analytics-tutorial.md) beschrieben, um Überwachungsanforderungen zu erfüllen.

### <a name="can-i-disable-clamscan-on-my-cluster"></a>Kann ich `Clamscan` auf meinem Cluster deaktivieren?

`Clamscan` ist die im HDInsight-Cluster ausgeführte Antivirensoftware. Sie wird vom Azure-Sicherheitsprozess (azsecd) verwendet, um Ihre Cluster vor Virenangriffen zu schützen. Microsoft rät dringend davon ab, dass Benutzer Änderungen an der Standardkonfiguration von `Clamscan` vornehmen.

Durch diesen Prozess werden Zyklen anderer Prozesse weder beeinträchtigt noch verbraucht. Der andere Prozess hat immer Vorrang. Durch `Clamscan` verursachte CPU-Spitzen sollten nur auftreten, wenn sich das System im Leerlauf befindet.  

Für Szenarien, die eine zeitliche Kontrolle erfordern, können Sie die folgenden Schritte ausführen:

1. Deaktivieren Sie die automatische Ausführung mit dem folgenden Befehl:
   
   `/usr/local/vbin/azsecd config -s clamav -d Disabled`
   
1. Fügen Sie einen Cron-Auftrag hinzu, der folgenden Befehl als Root ausführt:
   
   `/usr/local/bin/azsecd manual -s clamav`

Weitere Informationen zum Einrichten und Ausführen eines Cron-Auftrags finden Sie im Thema zum [Einrichten eines Cron-Auftrags](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job).

### <a name="why-is-llap-available-on-spark-esp-clusters"></a>Weshalb ist LLAP in Spark-ESP-Clustern verfügbar?
LLAP ist aus Sicherheitsgründen (Apache Ranger) aktiviert, nicht aus Leistungsgründen. Verwenden Sie virtuelle Computer mit größeren Knoten, um die Ressourcennutzung von LLAP zu berücksichtigen (z. B. mindestens D13V2). 

### <a name="how-can-i-add-additional-aad-groups-after-creating-an-esp-cluster"></a>Wie kann ich nach dem Erstellen eines ESP-Clusters zusätzliche AAD-Gruppen hinzufügen?
Zur Erreichung dieses Ziels gibt es zwei Möglichkeiten: 1. Sie können den Cluster neu erstellen und die zusätzliche Gruppe bei der Clustererstellung hinzufügen. Wenn Sie die bereichsbezogene Synchronisierung in AAD DS verwenden, stellen Sie sicher, dass Gruppe B in dieser Synchronisierung enthalten ist.
2\. Fügen Sie die Gruppe als geschachtelte Untergruppe der vorherigen Gruppe hinzu, die zum Erstellen des ESP-Clusters verwendet wurde. Wenn Sie beispielsweise einen ESP-Cluster mit der Gruppe `A` erstellt haben, können Sie zu einem späteren Zeitpunkt die Gruppe `B` als geschachtelte Untergruppe von `A` hinzufügen. Nach ungefähr einer Stunde wird sie synchronisiert und steht im Cluster automatisch zur Verfügung. 

## <a name="storage"></a>Storage

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>Kann ich einem vorhandenen HDInsight-Cluster Azure Data Lake Storage Gen2 als zusätzliches Speicherkonto hinzufügen?

Nein, derzeit ist es nicht möglich, einem Cluster, der Blob Storage als primären Speicher nutzt, ein Azure Data Lake Storage Gen2-Speicherkonto hinzuzufügen. Weitere Informationen finden Sie unter [Vergleichen von Speicheroptionen](hdinsight-hadoop-compare-storage-options.md).

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>Wie finde ich den Dienstprinzipal für ein Data Lake-Speicherkonto, der aktuell verknüpft ist?

Sie finden Ihre Einstellungen im Azure-Portal in den Clustereigenschaften unter **Data Lake Storage Gen1-Zugriff**. Weitere Informationen finden Sie unter [Überprüfen des Clustersetups](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#verify-cluster-set-up).
 
### <a name="how-can-i-calculate-the-usage-of-storage-accounts-and-blob-containers-for-my-hdinsight-clusters"></a>Wie berechne ich die Verwendung von Speicherkonten und BLOB-Containern für meine HDInsight-Cluster?

Führen Sie eine der folgenden Aktionen aus:

- [Verwenden von PowerShell](../storage/scripts/storage-blobs-container-calculate-size-powershell.md)

- Ermitteln Sie die Größe des Ordners */user/hive/.Trash/* im HDInsight-Cluster über die folgende Befehlszeile:
  
  `hdfs dfs -du -h /user/hive/.Trash/`

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>Wie richte ich die Überwachung für mein BLOB-Speicherkonto ein?

Um Blobspeicherkonten zu überwachen, konfigurieren Sie die Überwachung entsprechend den unter [Überwachen eines Speicherkontos im Azure-Portal](../storage/common/storage-monitor-storage-account.md) beschriebenen Schritten. Ein HDFS-Überwachungsprotokoll enthält nur Überwachungsinformationen für das lokale HDFS-Dateisystem (hdfs://mycluster).  Im Remotespeicher ausgeführte Vorgänge werden nicht einbezogen.

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>Wie übertrage ich Dateien zwischen einem BLOB-Container und einem HDInsight-Hauptknoten?

Führen Sie ein mit dem folgenden Shellskript vergleichbares Skript auf dem Hauptknoten aus:

```shell
for i in cat filenames.txt
do
   hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> Die Datei *filenames.txt* enthält den absoluten Pfad der Dateien in den BLOB-Containern.
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>Gibt es Ranger-Plug-Ins für den Speicher?

Derzeit sind keine Ranger-Plug-Ins für Blob Storage und Azure Data Lake Storage Gen1 oder Gen2 verfügbar. Bei ESP-Clustern sollten Sie Azure Data Lake Storage verwenden. Auf Dateisystemebene können Sie mithilfe der HDFS-Tools mindestens differenzierte Berechtigungen manuell festlegen. Darüber hinaus wird bei Verwendung von Azure Data Lake Storage der Dateisystemzugriff bei Clustern mit Enterprise-Sicherheitspaket teilweise auch über Azure Active Directory auf Clusterebene gesteuert. 

Über Azure Storage-Explorer können Sie den Sicherheitsgruppen Ihrer Benutzer Datenzugriffsrichtlinien zuweisen. Weitere Informationen finden Sie unter

- [Wie richte ich Berechtigungen für Azure AD-Benutzer zum Abfragen von Daten in Data Lake Storage Gen2 mit Hive oder anderen Diensten ein?](hdinsight-hadoop-use-data-lake-storage-gen2.md#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
- [Festlegen von Berechtigungen auf Datei- und Verzeichnisebene mithilfe des Azure Storage-Explorers mit Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-explorer.md)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>Kann ich den HDFS-Speicher in einem Cluster vergrößern, ohne dass die Datenträgergröße von Workerknoten zunimmt?

Nein. Sie können die Datenträgergröße eines Workerknotens nicht erhöhen. Die Datenträgergröße kann nur erhöht werden, indem der Cluster gelöscht und mit größeren Worker-VMs neu erstellt wird. Verwenden Sie HDFS nicht zum Speichern von HDInsight-Daten, da beim Löschen des Clusters auch die Daten gelöscht werden. Speichern Sie die Daten stattdessen in Azure. Sie können die Kapazität des HDInsight-Clusters auch erweitern, indem Sie den Cluster zentral hochskalieren.

## <a name="edge-nodes"></a>Edgeknoten

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>Kann ich nach der Erstellung des Clusters einen Edgeknoten hinzufügen?

Lesen Sie dazu [Verwenden leerer Edgeknoten in Apache Hadoop-Clustern in HDInsight](hdinsight-apps-use-edge-node.md).

### <a name="how-can-i-connect-to-an-edge-node"></a>Wie stelle ich eine Verbindung mit einem Edgeknoten her?

Nachdem Sie einen Edgeknoten erstellt haben, können Sie über SSH an Port 22 eine Verbindung mit dem Edgeknoten herstellen. Den Namen des Edgeknotens finden Sie im Clusterportal. Die Namen enden im Allgemeinen auf *-ed*.

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>Warum werden permanente Skripts auf neu erstellten Edgeknoten nicht automatisch ausgeführt?

Sie verwenden permanente Skripts, um mithilfe von Skalierungsvorgängen neue Workerknoten anzupassen, die dem Cluster hinzugefügt wurden. Persistente Skripts gelten nicht für Edgeknoten.

## <a name="rest-api"></a>REST-API

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>Wie lauten die REST-API-Aufrufe zum Abrufen einer Tez Query-Ansicht aus dem Cluster?

Sie können die folgenden REST-Endpunkte verwenden, um die erforderlichen Informationen im JSON-Format abzurufen. Verwenden Sie standardmäßige Authentifizierungsheader, um diese Anforderungen auszuführen.

- `Tez Query View`: *https:\//\<cluster name>.azurehdinsight.net/ws/v1/timeline/HIVE_QUERY_ID/*
- `Tez Dag View`: *https:\//\<cluster name>.azurehdinsight.net/ws/v1/timeline/TEZ_DAG_ID/*

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>Wie rufe ich die Konfigurationsdetails aus dem HDI-Cluster mithilfe eines Azure Active Directory-Benutzers ab?

Um mit dem AAD-Benutzer korrekte Authentifizierungstoken auszuhandeln, verwenden Sie das folgende Format, um das Gateway zu passieren:

* https://`<cluster dnsname>`.azurehdinsight.net/api/v1/clusters/testclusterdem/stack_versions/1/repository_versions/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>Wie verwende ich die RESTful-API von Ambari, um die YARN-Leistung zu überwachen?

Wenn Sie den cURL-Befehl im selben virtuellen Netzwerk oder in einem virtuellen Netzwerk mit Peering aufrufen, verwenden Sie folgenden Befehl:

```curl
curl -u <cluster login username> -sS -G
http://<headnodehost>:8080/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
```
 
Wenn Sie den Befehl außerhalb des virtuellen Netzwerks oder in einem virtuellen Netzwerk ohne Peering aufrufen, lautet das Befehlsformat wie folgt:

- Cluster ohne Enterprise-Sicherheitspaket:
  
  ```curl
  curl -u <cluster login username> -sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

- Cluster mit Enterprise-Sicherheitspaket:
  
  ```curl
  curl -u <cluster login username>-sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

> [!NOTE]
> Von Curl wird ein Kennwort angefordert. Sie müssen ein gültiges Kennwort für den Benutzernamen für die Clusteranmeldung eingeben.

## <a name="billing"></a>Abrechnung

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>Wie viel kostet die Bereitstellung eines HDInsight-Clusters?

Weitere Informationen zu Preisen und häufig gestellte Fragen zur Abrechnung finden Sie auf der Seite [Azure HDInsight: Preise](https://azure.microsoft.com/pricing/details/hdinsight/).

### <a name="when-does-hdinsight-billing-start--stop"></a>Wann beginnt und endet die HDInsight-Abrechnung?

Die Abrechnung für einen HDInsight-Cluster beginnt, sobald der Cluster erstellt wurde, und endet mit dem Löschen des Clusters. Die Abrechnung erfolgt pro Minute.

### <a name="how-do-i-cancel-my-subscription"></a>Wie kann ich mein Abonnement kündigen?

Informationen zum Kündigen Ihres Abonnements finden Sie unter [Kündigen Ihres Azure-Abonnements](../cost-management-billing/manage/cancel-azure-subscription.md).

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>Was geschieht bei Abonnements mit nutzungsbasierter Bezahlung, nachdem ich mein Abonnement gekündigt habe?

Informationen zu Ihrem gekündigten Abonnement finden Sie unter [Was geschieht, nachdem ich mein Abonnement gekündigt habe?](../cost-management-billing/manage/cancel-azure-subscription.md)

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-im-running-an-hdinsight-36-cluster"></a>Warum wird in der Ambari-Benutzeroberfläche die Hive-Version 1.2.1000 statt 2.1 angezeigt, obwohl ich einen HDInsight-Cluster der Version 3.6 ausführe?

Obwohl in der Ambari-Benutzeroberfläche nur 1.2 angezeigt wird, umfasst HDInsight 3.6 sowohl Hive 1.2 als auch Hive 2.1.

## <a name="other-faq"></a>Weitere häufig gestellte Fragen

### <a name="what-does-hdinsight-offer-for-real-time-stream-processing-capabilities"></a>Welche Funktionen bietet HDInsight für die Streamverarbeitung in Echtzeit?

Informationen zu den Integrationsfunktionen für die Streamverarbeitung finden Sie unter [Auswählen einer Technologie für die Datenstromverarbeitung in Azure](/azure/architecture/data-guide/technology-choices/stream-processing).

### <a name="is-there-a-way-to-dynamically-kill-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>Gibt es eine Möglichkeit, den Hauptknoten des Clusters dynamisch zu beenden, wenn sich der Cluster eine bestimmte Zeit im Leerlauf befindet?

Diese Aktion ist bei HDInsight-Clustern nicht möglich. Für diese Szenarien können Sie Azure Data Factory verwenden.

### <a name="what-compliance-offerings-does-hdinsight-offer"></a>Welche Complianceangebote umfasst HDInsight?

Informationen zur Compliance finden Sie im [Microsoft Trust Center](https://www.microsoft.com/trust-center) und unter [Overview of Microsoft Azure compliance](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).
