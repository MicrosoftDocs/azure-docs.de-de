---
title: Apache Hadoop-Komponenten und -Versionen – Azure HDInsight
description: Erfahren Sie mehr zu den Apache Hadoop-Komponenten und -Versionen in HDInsight und die in dieser Cloud-Distribution von Hortonworks Data Platform verfügbaren Servicelevels.
keywords: Hadoop-Versionen, Komponenten des Hadoop-Ökosystems, Hadoop-Komponenten, Hadoop-Version überprüfen
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: overview
ms.date: 03/26/2019
ms.openlocfilehash: 16a168f69c635ca86f40a08ba38a26d68c95954c
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66495763"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>Welche Apache Hadoop-Komponenten und -Versionen sind in HDInsight verfügbar?

Erfahren Sie mehr über die Komponenten und Versionen des [Apache Hadoop](https://hadoop.apache.org/)-Ökosystems in Microsoft Azure HDInsight sowie über das Enterprise Security Package. Darüber hinaus erfahren Sie, wie Sie Hadoop-Komponentenversionen in HDInsight überprüfen.

Jede HDInsight-Version ist eine Cloud-Distribution von Hortonworks Data Platform (HDP).

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>Verfügbare Apache Hadoop-Komponenten in verschiedenen Versionen von HDInsight

Azure HDInsight unterstützt mehrere Hadoop-Clusterversionen, die jederzeit bereitgestellt werden können. Jede ausgewählte Version erstellt eine bestimmte Version der HDP-Distribution und eine Reihe von Komponenten innerhalb dieser Distribution. Ab dem 4. April 2017 ist die in Azure HDInsight derzeit als Standardclusterversion verwendete Version die Version 3.6, die auf HDP 2.6 basiert.

Die den HDInsight-Clusterversionen zugeordneten Komponentenversionen sind in der folgenden Tabelle aufgeführt: 

> [!NOTE]  
> Die Standardversion für den HDInsight-Dienst kann ohne vorherige Ankündigung geändert werden. Wenn eine Versionsabhängigkeit vorliegt, geben Sie die HDInsight-Version an, wenn Sie Ihre Cluster mit dem .NET SDK mit Azure PowerShell und der klassischen Azure-Befehlszeilenschnittstelle erstellen.

| Komponente | HDInsight 4.0 | HDInsight 3.6 (Standard) | HDInsight 3.5 | HDInsight 3.4 | HDInsight 3.3 | HDInsight 3.2 |
|---------------------------|---------------|-----------------------------|---------------|---------------|---------------|----------------------|
| Hortonworks Data Platform | 3.0 | 2.6 | 2.5 | 2.4 | 2.3 | 2.2 |
| Apache Hadoop und YARN | 3.1.1 | 2.7.3 | 2.7.3 | 2.7.1 | 2.7.1 | 2.6.0 |
| Apache Tez | 0.9.1 | 0.7.0 | 0.7.0 | 0.7.0 | 0.7.0 | 0.5.2 |
| Apache Pig | 0.16.0 | 0.16.0 | 0.16.0 | 0.15.0 | 0.15.0 | 0.14.0 |
| Apache Hive und HCatalog | - | 1.2.1 | 1.2.1 | 1.2.1 | 1.2.1 | 0.14.0 |
| Apache Hive | 3.1.0 | 2.1.0 | - | - | - | - |
| Apache Tez Hive2 | - | 0.8.4 | - | - | - | - |
| Apache Ranger | 1.1.0 | 0.7.0 | 0.6.0 | - | - | - |
| Apache HBase | 2.0.1 | 1.1.2 | 1.1.2 | 1.1.2 | 1.1.1 | 0.98.4 |
| Apache Sqoop | 1.4.7 | 1.4.6 | 1.4.6 | 1.4.6 | 1.4.6 | 1.4.5 |
| Apache Oozie | 4.3.1 | 4.2.0 | 4.2.0 | 4.2.0 | 4.2.0 | 4.1.0 |
| Apache Zookeeper | 3.4.6 | 3.4.6 | 3.4.6 | 3.4.6 | 3.4.6 | 3.4.6 |
| Apache Storm | - | 1.1.0 | 1.0.1 | 0.10.0 | 0.10.0 | 0.9.3 |
| Apache Mahout | - | 0.9.0+ | 0.9.0+ | 0.9.0+ | 0.9.0+ | 0.9.0 |
| Apache Phoenix | 5 | 4.7.0 | 4.7.0 | 4.4.0 | 4.4.0 | 4.2.0 |
| Apache Spark | 2.3.2 | 2.3.0, 2.2.0, 2.1.0 | 1.6.2, 2.0 | 1.6.0 | 1.5.2 | 1.3.1 (nur für Windows) |
| Apache Livy | 0,5 | 0,4 | 0,3 | 0,3 | 0,2 | - |
| Apache Kafka | 1.1.1 | 1.1, 1.0 * (Siehe Hinweis unten) | 0.10.0 | 0.9.0 | - | - |
| Apache Ambari | 2.7.0 | 2.6.0 | 2.4.0 | 2.2.1 | 2.1.0 | - |
| Apache Zeppelin | 0.8.0 | 0.7.0 | - | - | - | - |
| Mono | 4.2.1 | 4.2.1 | 4.2.1 | 3.2.8 | - | - |

> [!NOTE]
> Aufgrund von Überlegungen zur Systemleistung ist die Unterstützung für Kafka Version 0.10 im März 2019 abgelaufen.

## <a name="check-for-current-hadoop-component-version-information"></a>Überprüfen der Informationen zur aktuellen Hadoop-Komponentenversion

Die mit den HDInsight-Clusterversionen verknüpften Komponentenversionen des Hadoop-Ökosystems können sich bei Updates für HDInsight ändern. Um die Hadoop-Komponenten zu überprüfen und zu ermitteln, welche Versionen für einen Cluster verwendet werden, verwenden Sie die Ambari-REST-API. Mit dem Befehl **GetComponentInformation** rufen Sie Informationen zu Dienstkomponenten ab. Ausführliche Informationen finden Sie in der [Apache Ambari-Dokumentation][ambari-docs].

> [!IMPORTANT]    
> Linux ist das einzige Betriebssystem, das unter HDInsight Version 3.4 oder höher verwendet wird. Weitere Informationen finden Sie unter [Welche Hadoop-Komponenten und -Versionen sind in HDInsight verfügbar?](#hdinsight-windows-retirement).

### <a name="release-notes"></a>Versionshinweise

In den [HDInsight-Versionshinweisen](hdinsight-release-notes.md) finden Sie zusätzliche Anmerkungen zu den aktuellen Versionen von HDInsight.

## <a name="supported-hdinsight-versions"></a>Unterstützte HDInsight-Versionen

In den folgenden Tabellen sind die Versionen von HDInsight aufgeführt. HDP-Versionen, die jeder HDInsight-Version entsprechen, werden zusammen mit der Produkt-Releasedaten aufgeführt. Der Ablauf der Unterstützung und die Deaktivierungstermine werden ebenfalls bereitgestellt, wenn sie bekannt sind.

### <a name="available-versions"></a>Verfügbare Versionen

Die folgende Tabelle enthält die im Azure-Portal verfügbaren Versionen von HDInsight sowie andere Bereitstellungsmethoden wie z.B. PowerShell und .NET SDK.

| HDInsight-Version | HDP-Version | Betriebssystem des virtuellen Computers | Herausgabedatum | Supportablaufdatum | Deaktivierungstermin | Hochverfügbarkeit |  Die Verfügbarkeit im Azure-Portal | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |HDP 3.0 |Ubuntu 16.0.4 LTS |24. September 2018 | | |Ja |Ja |
| HDInsight 3.6 |HDP 2.6 |Ubuntu 16.0.4 LTS |4. April 2017 | 30. Juni 2020 |31. Dezember 2020 |Ja |Ja |


> [!NOTE]  
> Nachdem die Unterstützung für eine Version abgelaufen ist, ist sie möglicherweise nicht mehr über das Microsoft Azure-Portal verfügbar. Clusterversionen stehen jedoch bis zum Zeitpunkt ihrer Deaktivierung weiterhin über den Parameter `Version` im Windows PowerShell-Befehl [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) und das .NET SDK zur Verfügung.
>

### <a name="retired-versions"></a>Eingestellte Versionen

Die folgende Tabelle enthält die Versionen von HDInsight, die derzeit **nicht** im Azure-Portal verfügbar sind.

| HDInsight-Version | HDP-Version | Betriebssystem des virtuellen Computers | Herausgabedatum | Supportablaufdatum | Deaktivierungstermin | Hochverfügbarkeit |  Die Verfügbarkeit im Azure-Portal | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.5 <br> (kein Spark) |HDP 2.5 |Ubuntu 16.0.4 LTS |30. September 2016 |5. September 2017 |28. Juni 2018 |Ja |Nein |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |29. März 2016 |29. Dezember 2016 |9. Januar 2018 |Ja |Nein |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |2. Dezember 2015 |27. Juni 2016 |31. Juli 2018 |Ja |Nein |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2. Dezember 2015 |27. Juni 2016 |31. Juli 2017 |Ja |Nein |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS oder Windows Server 2012 R2 |18. Februar 2015 |1. März 2016 |1. April 2017 |Ja |Nein |
| HDInsight 3.1 |HDP 2,1 |Windows Server 2012 R2 |24. Juni 2014 |18. Mai 2015 |30. Juni 2016 |Ja |Nein |
| HDInsight 3.0 |HDP 2,0 |Windows Server 2012 R2 |11. Februar 2014 |17. September 2014 |30. Juni 2015 |Ja |Nein |
| HDInsight 2.1 |HDP 1,3 |Windows Server 2012 R2 |28. Oktober 2013 |12. Mai 2014 |31. Mai 2015 |Ja |Nein |
| HDInsight 1.6 |HDP 1.1 | |28. Oktober 2013 |26. April 2014 |31. Mai 2015 |Nein |Nein |

> [!NOTE]  
> Hochverfügbare Cluster mit zwei Hauptknoten werden standardmäßig für HDInsight Version 2.1 und höher bereitgestellt. Für HDInsight Version 1.6-Cluster sind sie nicht verfügbar.

## <a name="enterprise-security-package-for-hdinsight"></a>Enterprise Security Package für HDInsight

Enterprise Security für HDInsight ist ein optionales Paket, das Sie während der Clustererstellung zu Ihrem HDInsight-Cluster hinzufügen können. Das Enterprise Security Package unterstützt Folgendes:

- Integration in Active Directory für die Authentifizierung

    Bisher konnten Sie HDInsight-Cluster ausschließlich mit einem lokalen Administratorbenutzer oder einem lokalen SSH-Benutzer erstellen. Der lokale Administratorbenutzer kann auf alle Dateien, Ordner, Tabellen und Spalten zugreifen.  Mit dem Enterprise Security Package können Sie die rollenbasierte Zugriffssteuerung aktivieren, indem Sie HDInsight-Cluster in Ihr eigenes Active Directory integrieren, zu dem das lokale Active Directory, Azure Active Directory Domain Services oder Active Directory auf dem virtuellen IaaS-Computer gehören. Der Domänenadministrator des Clusters kann Benutzern die Berechtigung erteilen, ihren eigenen Firmen- oder Domänenbenutzernamen und ihr eigenes Kennwort für den Zugriff auf den Cluster zu verwenden. 

    Weitere Informationen finden Sie unter

    - [Einführung in die Apache Hadoop-Sicherheit mit in die Domäne eingebundenen HDInsight-Clustern](./domain-joined/apache-domain-joined-introduction.md)
    - [Planen von in die Azure-Domäne eingebundenen Apache Hadoop-Clustern in HDInsight](./domain-joined/apache-domain-joined-architecture.md)
    - [Konfigurieren einer in die Domäne eingebundenen Sandboxumgebung](./domain-joined/apache-domain-joined-configure.md)
    - [Konfigurieren von in die Domäne eingebundenen HDInsight-Clustern mit Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- Autorisierung für Daten

  - Integration in Apache Ranger für die Autorisierung für Hive-, Spark SQL- und Yarn-Warteschlangen
  - Sie können die Zugriffssteuerung für Dateien und Ordner festlegen.

    Weitere Informationen finden Sie unter

  - [Konfigurieren von Apache Hive-Richtlinien in HDInsight mit Domänenverknüpfung](./domain-joined/apache-domain-joined-run-hive.md)

- Anzeigen der Überwachungsprotokolle zum Überwachen der Zugriffe und konfigurierten Richtlinien 

### <a name="supported-cluster-types"></a>Unterstützte Clustertypen

Derzeit wird das Enterprise Security Package nur durch die folgenden Clustertypen unterstützt:

- Hadoop (nur HDInsight 3.6)
- Spark
- Interactive Query

### <a name="support-for-azure-data-lake-storage"></a>Unterstützung für Azure Data Lake Storage

Das Enterprise-Sicherheitspaket unterstützt die Verwendung von Azure Data Lake Storage sowohl als Primärspeicher als auch als Zusatzspeicher.

### <a name="pricing-and-sla"></a>Preise und SLA

Informationen zu Preisen und SLA für das Enterprise Security Package finden Sie unter [HDInsight-Preise](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="hdinsight-windows-retirement"></a>HDInsight-Windows-Deaktivierung

Microsoft Azure HDInsight-Version 3.3 war die letzte Version von HDInsight unter Windows. Das Deaktivierungstermin für HDInsight unter Windows ist der 31. Juli 2018. Wenn Sie HDInsight-Cluster unter Windows 3.3 oder früheren Versionen haben, müssen Sie HDInsight vor dem 31. Juli 2018 in Linux migrieren (HDInsight Version 3.5 oder höher). Durch die Migration in das Linux-Betriebssystem können Sie weiterhin HDInsight-Cluster erstellen und in der Größe anpassen. Unterstützung für HDInsight Version 3.3 unter Windows ist am 27. Juni 2016 abgelaufen.

Ab HDInsight Version 3.4 hat Microsoft HDInsight nur für das Linux-Betriebssystem bereitgestellt. Daher sind einige der Komponenten in HDInsight nur für Linux verfügbar. Dies schließt [Apache Ranger](https://ranger.apache.org/), [Apache Kafka](https://kafka.apache.org/), Interactive Query, [Apache Spark](https://spark.apache.org/), HDInsight-Anwendungen und Azure Data Lake Storage als primäres Dateisystem ein. Zukünftige Releases von HDInsight stehen nur unter Linux zur Verfügung. Es wird keine zukünftige Version von HDInsight unter Windows geben. 

## <a name="faqs"></a>Häufig gestellte Fragen

### <a name="what-is-the-timeline-for-retiring-hdinsight-on-windows"></a>Was ist die Zeitachse für das Deaktivieren von HDInsight unter Windows?

Das Deaktivierungstermin für HDInsight unter Windows ist der 31. Juli 2018. Wenn der geplante Deaktivierungstermin für Ihre Region abweicht, werden Sie separat benachrichtigt. 

### <a name="what-is-the-impact-of-retiring-hdinsight-on-windows-for-existing-customers"></a>Welche Auswirkungen hat die Deaktivierung von HDInsight unter Windows für bestehende Kunden?

Nachdem HDInsight unter Windows deaktiviert wurde, können Sie keine neuen HDInsight-Cluster unter Windows erstellen oder die Größe eines vorhandenen HDInsight-Windows-Clusters ändern. Die Unterstützung für HDInsight Version 3.3 ist am 27. Juni 2016 abgelaufen. Daher gibt es keinen Support oder bei Fehlerbehebungen für HDInsight 3.3 oder frühere Versionen. Zukünftige Releases von HDInsight stehen nur unter Linux zur Verfügung. Es wird keine zukünftige Version von HDInsight unter Windows geben.
 
### <a name="which-versions-of-hdinsight-on-windows-are-affected"></a>Welche Versionen von HDInsight unter Windows sind betroffen?

Azure HDInsight-Version 3.3 ist die letzte Version von HDInsight für Windows. Bevor HDInsight für Windows deaktiviert wird, müssen alle HDInsight-Windows-Cluster der Version 3.3 oder früher in HDInsight unter Linux Version 3.5 oder höher migriert werden. Durch das Migrieren Ihrer Cluster zu HDInsight unter Linux haben Sie weiterhin die Möglichkeit, neue Cluster zu erstellen oder die Größe vorhandener Cluster zu ändern. 

### <a name="what-do-i-need-to-do"></a>Was muss ich tun?

Migrieren Sie Ihr HDInsight-Windows-Cluster vor dem 31. Juli 2018 in ein unterstütztes HDInsight-Linux-Cluster. Weitere Informationen finden Sie im [HDInsight-Migrationsdokument](hdinsight-migrate-from-windows-to-linux.md). Weitere Informationen zu den Azure HDInsight-Versionen finden Sie in der [Liste der unterstützten Versionen](hdinsight-component-versioning.md#supported-hdinsight-versions). 

### <a name="where-do-i-find-the-cluster-os-type"></a>Wo finde ich den Betriebssystemtyp des Clusters?

Wechseln Sie im Azure-Portal zur Übersichtsseite des HDInsight-Clusters, und suchen Sie unter **Essentials** nach dem **Clustertyp**. Die Betriebssystemtypen des Clusters werden auf dieser Seite angezeigt. 

### <a name="i-cant-migrate-to-an-hdinsight-linux-cluster-by-july-31-2018-what-is-the-impact-to-my-hdinsight-windows-cluster"></a>Ich kann vor dem 31. Juli 2018 nicht in ein HDInsight-Linux-Cluster migrieren. Welche Auswirkung hat das auf mein HDInsight-Windows-Cluster?

Das HDInsight-Windows-Cluster wird wie gehabt ausgeführt, Sie können aber keine neuen HDInsight-Cluster unter Windows erstellen oder die Größe eines vorhandenen HDInsight-Windows-Clusters ändern. 

### <a name="my-cluster-has-a-net-dependency-how-do-i-resolve-this-dependency-on-linux"></a>Meine Cluster hat eine .NET-Abhängigkeit. Wie beheben ich diese Abhängigkeit unter Linux?

Sie können Ihre Linux-Cluster-Abhängigkeit mit dem [Mono-Projekt](https://www.mono-project.com/) beheben. Diese Open Source-Implementierung von .NET ist für HDInsight-Linux-Cluster verfügbar. Weitere Informationen finden Sie im [HDInsight-Migrationsdokument](hdinsight-migrate-from-windows-to-linux.md). 

### <a name="im-a-new-customer-for-hdinsight-on-windows-how-can-i-create-an-hdinsight-windows-cluster"></a>Ich bin ein neuer Kunde von HDInsight unter Windows. Wie kann ich ein HDInsight-Windows-Cluster erstellen?

Seit dem 3. Juli 2017 können nur bestehende HDInsight-Windows-Kunden neue HDInsight-Windows-Cluster erstellen. Neue Kunden können keine HDInsight-Windows-Cluster über das Azure-Portal mit PowerShell oder dem SDK erstellen. Es wird empfohlen neuen Kunden, ein Linux-HDInsight-Cluster zu erstellen. Bestehende Kunden können bis zum Deaktivierungstermin von HDInsight unter Windows neue HDInsight-Windows-Cluster erstellen. 

### <a name="is-there-a-pricing-impact-associated-with-moving-from-hdinsight-on-windows-to-hdinsight-on-linux"></a>Verändern sich die Kosten durch die Migration von HDInsight unter Windows in HDInsight unter Linux?

Nein, die Preise für HDInsight sind für alle Betriebssysteme gleich. 

### <a name="what-are-the-customer-advantages-associated-with-the-move-to-only-using-hdinsight-on-linux"></a>Welche Vorteile haben Kunden dadurch, dass HDInsight nur noch unter Linux verwendet werden kann?

* Schnellere Markteinführungszeiten für Open Source-Technologien für Big Data mithilfe von HDInsight
* Support durch eine große Community und ein umfangreiches Ökosystem
* Möglichkeit der aktiven Entwicklung durch die Open Source-Community für Hadoop und andere Big Data-Technologien

### <a name="does-hdinsight-on-linux-provide-additional-functionality-beyond-what-is-available-in-hdinsight-on-windows"></a>Bietet HDInsight unter Linux zusätzliche Funktionalität, die über die unter Windows verfügbare hinausgeht?

Ab HDInsight Version 3.4 hat Microsoft HDInsight nur für das Linux-Betriebssystem bereitgestellt. Daher sind einige der Komponenten in HDInsight nur für Linux verfügbar. Dies schließt Apache Ranger, Kafka, Interactive Query, Spark HDInsight-Anwendungen und Azure Data Lake Storage als primäres Dateisystem ein. 

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Die Vereinbarung zum Servicelevel (Service Level Agreement, SLA) für HDInsight-Clusterversionen

Die Vereinbarung zum Servicelevel (Service Level Agreement, SLA) ist als _Supportfenster_ definiert. Ein Supportfenster ist ein Zeitraum, in dem eine HDInsight-Clusterversion vom Microsoft-Kundendienst und -Support unterstützt wird. Wenn die Version ein verstrichenes _Supportablaufdatum_ hat, befindet sich das HDInsight-Cluster außerhalb des Supportfensters. Weitere Informationen zu unterstützten Versionen finden Sie in der Liste der [unterstützten Versionen von HDInsight-Clustern](hdinsight-migrate-from-windows-to-linux.md). Das Supportablaufdatum für eine bestimmte HDInsight-Version X (nachdem eine neuere X+1-Version verfügbar ist) wird berechnet als das spätere Datum von:  

* Formel 1: Addieren Sie 180 Tage zum Releasedatum von HDInsight-Clusterversion X.
* Formel 2: Addieren Sie 90 Tage zum Releasedatum von HDInsight-Clusterversion X+1 im Azure-Portal.

Das _Deaktivierungsdatum_ ist das Datum, nach dem die Clusterversion nicht mehr in HDInsight erstellt werden kann. Ab dem 31. Juli 2017 kann die Größe eines HDInsight-Clusters nicht mehr geändert werden. 

> [!NOTE]  
> Windows-basierte HDInsight-Cluster (einschließlich Version 2.1, 3.0, 3.1, 3.2 und 3.3) laufen unter der Azure-Gast-BS-Familie Version 4, die die 64-Bit-Version von Windows Server 2012 R2 verwendet. Die Azure-Gast-BS-Familie Version 4 unterstützt die .NET Framework-Versionen 4.0, 4.5, 4.5.1 und 4.5.2.

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Hortonworks-Versionshinweise im Zusammenhang mit HDInsight-Versionen

Dieser Abschnitt enthält Links zu den Anmerkungen zu dieser Version für Hortonworks Data Platform-Verteilungen und den Apache-Komponenten, die mit HDInsight verwendet werden.
* HDInsight-Clusterversion 4.0 verwendet eine Hadoop-Distribution, die auf [Hortonworks Data Platform 3.0](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/relnotes.html) basiert.
* HDInsight-Clusterversion 3.6 verwendet eine Hadoop-Distribution, die auf [Hortonworks Data Platform 2.6](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html) basiert.
* HDInsight-Clusterversion 3.5 verwendet eine Hadoop-Distribution, die auf [Hortonworks Data Platform 2.5](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html) basiert. HDInsight-Clusterversion 3.5 ist das _Standard_-Hadoop-Cluster, das im Azure-Portal erstellt wird.
* HDInsight-Clusterversion 3.4 verwendet eine Hadoop-Distribution, die auf [Hortonworks Data Platform 2.4](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html)basiert.
* HDInsight-Clusterversion 3.3 verwendet eine Hadoop-Distribution, die auf [Hortonworks Data Platform 2.3](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html)basiert.

  * Auf der Apache-Website finden Sie die [Anmerkungen zur Version von Apache Storm](https://storm.apache.org/2015/11/05/storm0100-released.html).
  * Auf der Apache-Website finden Sie die [Anmerkungen zur Version von Apache Hive](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843).
* HDInsight-Clusterversion 3.2 verwendet eine Hadoop-Distribution, die auf [Hortonworks Data Platform 2.2][hdp-2-2] basiert.

  * Versionshinweise für bestimmte Apache-Komponenten sind wie folgt verfügbar: [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112) und [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* HDInsight-Clusterversion 3.1 verwendet eine Hadoop-Distribution, die auf [Hortonworks Data Platform 2.1.7][hdp-2-1-7] basiert. Vor dem 7. November 2014 erstellte HDInsight 3.1-Cluster basierten auf [Hortonworks Data Platform 2.1.1][hdp-2-1-1].
* HDInsight-Clusterversion 3.0 verwendet eine Hadoop-Distribution, die auf [Hortonworks Data Platform 2.0][hdp-2-0-8] basiert.
* HDInsight-Clusterversion 2.1 verwendet eine Hadoop-Distribution, die auf [Hortonworks Data Platform 1.3][hdp-1-3-0] basiert.
* HDInsight-Clusterversion 1.6 verwendet eine Hadoop-Distribution, die auf [Hortonworks Data Platform 1.1][hdp-1-1-0] basiert.

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Standardknotenkonfiguration und Standardgrößen von virtuellen Computern für Cluster

Die folgenden Tabellen enthalten die Standard-VM-Größen für HDInsight-Cluster.  Dieses Diagramm ist erforderlich, um die VM-Größen zu verstehen, die beim Erstellen von PowerShell- oder Azure CLI-Skripten für die Bereitstellung von HDInsight-Clustern verwendet werden.

> [!IMPORTANT]  
> Wenn Sie mehr als 32 Workerknoten benötigen, müssen Sie eine Hauptknotengröße von mindestens 8 Kernen und 14 GB Arbeitsspeicher (RAM) auswählen.

* Alle unterstützten Regionen, mit Ausnahme von „Brasilien, Süden“ und „Japan, Westen“:

|Clustertyp|Hadoop|hbase|Interactive Query|Storm|Spark|ML Server|Kafka|
|---|---|---|---|---|---|---|---|
|Head – Standard-VM-Größe|D12 v2|D12 v2|D13 v2|A3|D12 v2|D12 v2|D3V2|
|Head – empfohlene VM-Größen|D3 v2|D3 v2|D13|A4 v2|D12 v2|D12 v2|A2m v2|
||D4 v2|D4 v2|D14|A8 v2|D13 v2|D13 v2|D3 v2|
||D12 v2|D12 v2|E16 v3|A2m v2|D14 v2|D14 v2|D4 v2|
||E4 v3|E4 v3|E32 v3|E4 v3|E4 v3|E4 v3|D12 v2|
|Worker – Standard-VM-Größe|D4 v2|D4 v2|D14 v2|D3 v2|D13 v2|D4 v2|4 D12v2 mit 2 S30-Datenträgern pro Broker|
|Worker – empfohlene VM-Größen|D3 v2|D3 v2|D13|D3 v2|D4 v2|D4 v2|D13 v2|
||D4 v2|D4 v2|D14|D4 v2|D12 v2|D12 v2|DS12 v2|
||D12 v2|D12 v2|E16 v3|D12 v2|D13 v2|D13 v2|DS13 v2|
||E4 v3|E4 v3|E20 v3|E4 v3|D14 v2|D14 v2|E4 v3|
||||E32 v3||E16 v3|E16 v3|ES4 v3|
||||E64 v3||E20 v3|E20 v3|E8 v3|
||||||E32 v3|E32 v3|ES8 v3|
||||||E64 v3|E64 v3||
|Zookeeper – Standard-VM-Größen||A4 v2|A4 v2|A4 v2||A2 v2|D3V2|
|Zookeeper – empfohlene VM-Größen||A4 v2||A2 v2|||A2m v2|
|||A8 v2||A4 v2|||D3 v2|
|||A2m v2||A8 v2|||E8 v3|
|Edge – Standard-VM-Größe||||||D4 v2||
|Edge – empfohlene VM-Größe||||||D4 v2||
|||||||D12 v2||
|||||||D13 v2||
|||||||D14 v2||
|||||||E16 v3||
|||||||E20 v3||
|||||||E32 v3||
|||||||E64 v3||

* Nur „Brasilien, Süden“ und „Japan, Westen“ (hier keine v2-Größen):

  | Clustertyp | Hadoop | hbase | Interactive Query |Storm | Spark | ML Services |
  | --- | --- | --- | --- | --- | --- | --- |
  | Head – Standard-VM-Größe |D12 |D12  | D13 |A3 |D12 |D12 |
  | Head – empfohlene VM-Größen |D3,<br/> D4,<br/> D12 |D3,<br/> D4,<br/> D12  | D13,<br/> D14 |A3,<br/> A4,<br/> A5 |D12,<br/> D13,<br/> D14 |D12,<br/> D13,<br/> D14 |
  | Worker – Standard-VM-Größe |D4 |D4  |  D14 |D3 |D13 |D4 |
  | Worker – empfohlene VM-Größen |D3,<br/> D4,<br/> D12 |D3,<br/> D4,<br/> D12  | D13,<br/> D14 |D3,<br/> D4,<br/> D12 |D4,<br/> D12,<br/> D13,<br/> D14 | D4,<br/> D12,<br/> D13,<br/> D14 |
  | Zookeeper – Standard-VM-Größen | |A4 v2 | A4 v2| A4 v2 | | A2 v2|
  | Zookeeper – empfohlene VM-Größen | |A2,<br/> A3,<br/> A4 | |A2,<br/> A3,<br/> A4 | | |
  | Edge – Standard-VM-Größen | | | | | |D4 |
  | Edge – empfohlene VM-Größen | | | | | |D4,<br/> D12,<br/> D13,<br/> D14 |

> [!NOTE]
> - Head wird für den Storm-Clustertyp als *Nimbus* bezeichnet.
> - Worker wird für den Storm-Clustertyp als *Supervisor* bezeichnet.
> - Worker wird für den HBase-Clustertyp als *Region* bezeichnet.

## <a name="next-steps"></a>Nächste Schritte
- [Einrichten von Clustern in HDInsight mit Apache Hadoop, Spark, Kafka usw.](hdinsight-hadoop-provision-linux-clusters.md)
- [Verwenden eines Windows-Computers mit Apache Hadoop in HDInsight](hdinsight-hadoop-windows-tools.md)

[Supported HDInsight versions]:(#supported-hdinsight-versions)

[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: https://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.9/bk_HDP_RelNotes/content/ch_relnotes_v229.html

[hdp-2-1-7]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.1.1.16_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: https://zookeeper.apache.org/
