---
title: Bewährte Methoden zur Verwendung von Azure Data Lake Storage Gen2 | Microsoft-Dokumentation
description: Erfahren Sie mehr über bewährte Methoden zur Datenerfassung, Datensicherheit und Leistung bei Verwendung von Azure Data Lake Storage Gen2 (ehemals Azure Data Lake Store).
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: sachins
ms.openlocfilehash: 0efcdfd1b14479edf84dc1892e7e1d9afabd5a81
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95913554"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen2"></a>Bewährte Methoden zur Verwendung von Azure Data Lake Storage Gen2

In diesem Artikel erfahren Sie mehr über die bewährten Methoden und Aspekte bei der Arbeit mit Azure Data Lake Storage Gen2. Der Artikel enthält Informationen zu den Bereichen Sicherheit, Leistung, Resilienz und Überwachung für Data Lake Storage Gen2. Vor der Einführung von Data Lake Storage Gen2 war die Arbeit mit wirklich großen Datenmengen (Big Data) in Diensten wie Azure HDInsight sehr komplex. Daten mussten per Sharding auf mehrere Blobspeicherkonten verteilt werden, um die Speicherung im Petabyte-Bereich und eine entsprechende optimale Leistung zu erzielen. Data Lake Storage Gen2 unterstützt individuelle Dateigrößen bis zu 5 TB, und die meisten festen Leistungsgrenzen wurden entfernt. In diesem Artikel werden aber trotzdem noch einige Aspekte beschrieben, damit Sie für Data Lake Storage Gen2 die bestmögliche Leistung erzielen können.

## <a name="security-considerations"></a>Sicherheitshinweise

Azure Data Lake Storage Gen2 ermöglicht POSIX-Zugriffssteuerungen für Azure Active Directory-Benutzer, -Gruppen und -Dienstprinzipale. Diese Zugriffssteuerungen können für vorhandene Dateien und Verzeichnisse festgelegt werden. Außerdem können die Zugriffssteuerungen zum Erstellen von Standardberechtigungen verwendet werden, die auf neue Dateien oder Verzeichnisse automatisch angewendet werden können. Weitere Details zu Data Lake Storage Gen2-Zugriffssteuerungslisten finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](./data-lake-storage-access-control.md).

### <a name="use-security-groups-versus-individual-users"></a>Vergleich der Verwendung von Sicherheitsgruppen und einzelnen Benutzern

Bei der Arbeit mit großen Datenmengen in Data Lake Storage Gen2 wird meist ein Dienstprinzipal eingesetzt, damit Dienste wie Azure HDInsight die Daten nutzen können. Es kann aber auch Fälle geben, in denen einzelne Benutzer ebenfalls Zugriff auf die Daten benötigen. Erwägen Sie in allen Fällen die Nutzung von Azure Active Directory-[Sicherheitsgruppen](../common/storage-auth-aad.md), anstatt einzelne Benutzer Verzeichnissen und Dateien zuzuweisen.

Nachdem einer Sicherheitsgruppe Berechtigungen zugewiesen wurden, sind für das Hinzufügen oder Entfernen von Benutzern der Gruppe keine Aktualisierungen für Data Lake Storage Gen2 erforderlich. Dies hilft auch sicherzustellen, dass Sie die maximale Anzahl von Zugriffssteuerungseinträgen pro Zugriffssteuerungsliste (ACL) nicht überschreiten. Derzeit ist dieser Wert 32 (einschließlich der vier ACLs im POSIX-Format, die jeder Datei und jedem Verzeichnis stets zugeordnet sind: der zuständige Benutzer, die zuständige Gruppe, die Maske und andere). Jedes Verzeichnis kann zwei Arten von ACL aufweisen, die Zugriffs-ACL und die Standard-ACL, was insgesamt 64 Zugriffssteuerungseinträgen entspricht. Weitere Informationen zu diesen ACLs finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="security-for-groups"></a>Sicherheit für Gruppen

Wenn Sie oder Ihre Benutzer Zugriff auf Daten in einem Speicherkonto mit aktiviertem hierarchischen Namespace benötigen, empfiehlt es sich, Azure Active Directory-Sicherheitsgruppen zu verwenden. Einige empfohlene Gruppen für den Einstieg sind beispielsweise **ReadOnlyUsers**, **WriteAccessUsers** und **FullAccessUsers** für das Stammverzeichnis des Containers und separate Gruppen für wichtige Unterverzeichnisse. Falls andere antizipierte Gruppen von Benutzern vorhanden sind, die ggf. später hinzugefügt werden sollen, aber noch nicht identifiziert wurden, können Sie die Erstellung von Dummy-Sicherheitsgruppen erwägen, die Zugriff auf bestimmte Ordner haben. Durch die Verwendung von Sicherheitsgruppen lassen sich lange Verarbeitungszeiten vermeiden, wenn Tausenden von Dateien neue Berechtigungen zugewiesen werden.

### <a name="security-for-service-principals"></a>Sicherheit für Dienstprinzipale

Azure Active Directory-Dienstprinzipale werden normalerweise von Diensten wie Azure Databricks verwendet, um in Data Lake Storage Gen2 auf Daten zuzugreifen. Für viele Kunden kann ein einzelner Azure Active Directory-Dienstprinzipal ausreichen, der für den Stammordner des Data Lake Storage Gen2-Containers über vollständige Berechtigungen verfügt. Andere Kunden benötigen ggf. mehrere Cluster mit unterschiedlichen Dienstprinzipalen, wobei ein Cluster über Vollzugriff auf die Daten und ein anderer Cluster nur über Lesezugriff verfügt. 

### <a name="enable-the-data-lake-storage-gen2-firewall-with-azure-service-access"></a>Aktivieren der Data Lake Storage Gen2-Firewall beim Zugriff auf Azure-Dienste

Data Lake Storage Gen2 unterstützt die Option zum Aktivieren einer Firewall und Beschränken des Zugriffs auf ausschließlich Azure-Dienste, was zum Begrenzen der Angriffsfläche für externe Angriffe empfohlen wird. Die Firewall kann im Azure-Portal für das Speicherkonto über die Option **Firewall** > **Firewall aktivieren (EIN)**  > **Zugriff auf Azure-Dienste erlauben** aktiviert werden.

Wenn Sie über Azure Databricks auf Ihr Speicherkonto zugreifen möchten, stellen Sie Azure Databricks in Ihrem virtuellen Netzwerk bereit, und fügen Sie dann Ihrer Firewall das virtuelle Netzwerk hinzu. Weitere Informationen finden Sie unter [Konfigurieren von Azure Storage-Firewalls und virtuellen Netzwerken](../common/storage-network-security.md).

## <a name="resiliency-considerations"></a>Überlegungen zur Resilienz

Beim Erstellen der Systemarchitektur mit Data Lake Storage Gen2 oder einem beliebigen Clouddienst müssen Sie Ihre Verfügbarkeitsanforderungen und die Reaktion auf mögliche Unterbrechungen des Diensts berücksichtigen. Ein Problem kann ggf. einer bestimmten Instanz oder einer gesamten Region zugeordnet sein, sodass es wichtig ist, für beide Fälle über einen Plan zu verfügen. Je nach SLAs für Recovery Time Objective und Recovery Point Objective für Ihre Workload können Sie für Hochverfügbarkeit und Notfallwiederherstellung eine aggressivere oder weniger aggressive Strategie wählen.

### <a name="high-availability-and-disaster-recovery"></a>Hochverfügbarkeit und Notfallwiederherstellung

Hochverfügbarkeit (High availability, HA) und Notfallwiederherstellung (Disaster Recovery, DR) können auch kombiniert werden, obwohl die Strategien sich leicht unterscheiden – vor allem in Bezug auf die Daten. Data Lake Storage Gen2 bietet bereits standardmäßig eine Dreifachreplikation als Schutz vor lokalen Hardwarefehlern. Darüber wird Hochverfügbarkeit durch andere Replikationsoptionen wie zonenredundanter Speicher (ZRS) oder geozonenredundanter Speicher (GZRS) verbessert, während georedundanter Speicher (GRS) und georedundanter Speicher mit Lesezugriff (RA-GRS) die Notfallwiederherstellung optimieren. Beim Erstellen eines Plans für die Hochverfügbarkeit benötigt die Workload bei einer Dienstunterbrechung so schnell wie möglich Zugriff auf die aktuellen Daten, indem zu einer separat replizierten Instanz gewechselt wird, die sich am lokalen Standort oder in einer neuen Region befindet.

Bei einer Strategie für die Notfallwiederherstellung ist es als Vorbereitung auf den unwahrscheinlichen Fall, dass eine Region aufgrund einer Katastrophe ausfällt, außerdem wichtig, dass Daten mithilfe der Replikation per georedundantem Speicher oder georedundantem Speicher mit Lesezugriff in eine andere Region repliziert werden. Sie müssen außerdem Ihre Anforderungen in Bezug auf Sonderfälle berücksichtigen, z. B. Datenbeschädigungen, in denen es ratsam sein kann, regelmäßige Momentaufnahmen als Fallbacklösung zu erstellen. Je nach Wichtigkeit und Größe der Daten können Sie erwägen, rollierende Deltamomentaufnahmen für Zeiträume von 1 Stunde, 6 Stunden und 24 Stunden zu erstellen. Dies richtet sich nach Risikotoleranzen.

Für Datenresilienz mit Data Lake Storage Gen2 wird empfohlen, für Ihre Daten eine Georeplikation mithilfe von georedundantem Speicher oder georedundantem Speicher mit Lesezugriff durchzuführen, die für Ihre Anforderungen an Hochverfügbarkeit und Notfallwiederherstellung geeignet ist. Außerdem sollten Sie sich überlegen, auf welche Weise für die Anwendung, für die Data Lake Storage Gen2 genutzt wird, ein automatisches Failover in die sekundäre Region erfolgen kann. Hierfür können Trigger oder die Länge von fehlerhaften Versuchen überwacht werden, oder es kann als Mindestmaßnahme eine Benachrichtigung an Administratoren gesendet werden, damit diese manuell eingreifen können. Beachten Sie, dass abgewogen werden sollte, ob ein Failover oder das Warten auf die Wiedererlangung des Onlinezustands für einen Dienst mit weniger Nachteilen verbunden ist.

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Verwenden von Distcp für Datenverschiebungen zwischen zwei Standorten

DistCp ist die Abkürzung von „distributed copy“. Es handelt sich um ein Linux-Befehlszeilentool, das zum Funktionsumfang von Hadoop gehört und verteilte Datenverschiebungen zwischen zwei Speicherorten ermöglicht. Die beiden Speicherorte können Data Lake Storage Gen2, HDFS, WASB oder S3 sein. Bei diesem Tool werden MapReduce-Aufträge in einem Hadoop-Cluster (z.B. HDInsight) verwendet, um das Aufskalieren auf allen Knoten durchzuführen. Distcp ist die schnellste Möglichkeit zum Verschieben von Big Data-Datenmengen ohne spezielle Einrichtungen für die Netzwerkkomprimierung. Distcp verfügt auch über eine Option zum alleinigen Aktualisieren von Deltadaten zwischen zwei Standorten und ermöglicht die Durchführung von automatischen Wiederholungsversuchen und die dynamische Computeskalierung. Dieser Ansatz ist äußerst effizient, was das Replizieren von Elementen wie Hive/Spark-Tabellen betrifft, die über viele große Dateien in einem einzelnen Verzeichnis verfügen, wenn Sie lediglich die geänderten Daten kopieren möchten. Aus diesen Gründen ist Distcp das empfohlene Tool zum Kopieren von Daten zwischen Big Data-Speichern.

Kopieraufträge können von Apache Oozie-Workflows ausgelöst werden, indem Häufigkeits- oder Datentrigger verwendet werden, und über Linux-Cron-Aufträge. Für intensive Replikationsaufträge wird empfohlen, einen separaten HDInsight Hadoop-Cluster zu erstellen, der speziell für die Kopieraufträge optimiert und skaliert werden kann. So wird sichergestellt, dass es für Kopieraufträge nicht zu Konflikten mit kritischen Aufträgen kommt. Falls die Replikation mit einer ausreichend geringen Häufigkeit erfolgt, kann der Cluster zwischen den einzelnen Aufträgen ggf. sogar heruntergefahren werden. Stellen Sie beim Ausführen eines Failovers in eine sekundäre Region sicher, dass in der sekundären Region ein weiterer Cluster erstellt wird, damit neue Daten wieder unter dem primären Data Lake Storage Gen2-Konto repliziert werden können, sobald es wieder verfügbar ist. Beispiele für die Verwendung von DistCp finden Sie unter [Kopieren von Daten zwischen Azure Storage-Blobs und Data Lake Storage Gen2 mithilfe von DistCp](../blobs/data-lake-storage-use-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Verwenden von Azure Data Factory zum Planen von Kopieraufträgen

Azure Data Factory kann auch verwendet werden, um Kopieraufträge mit einer Kopieraktivität zu planen, und über den Kopier-Assistenten kann sogar ein Takt dafür eingerichtet werden. Beachten Sie hierbei, dass für Azure Data Factory eine Begrenzung in Bezug auf die Einheiten der Clouddatenverschiebung (Data Movement Units, DMUs) gilt und für große Datenworkloads irgendwann eine Durchsatz-/Computebeschränkung greift. Außerdem sind mit Azure Data Factory derzeit keine Deltaupdates zwischen Data Lake Storage Gen2-Konten möglich, sodass für Verzeichnisse wie Hive-Tabellen für die Replikation eine vollständige Kopie erforderlich ist. Weitere Informationen zum Kopieren mit Data Factory finden Sie im [Artikel zu Data Factory](../../data-factory/load-azure-data-lake-storage-gen2.md).

## <a name="monitoring-considerations"></a>Aspekte der Überwachung

Für Data Lake Storage Gen2 werden im Azure-Portal unter dem Data Lake Storage Gen2-Konto und in Azure Monitor Metriken bereitgestellt. Die Verfügbarkeit von Data Lake Storage Gen2 wird im Azure-Portal angezeigt. Sie müssen Ihre eigenen synthetischen Tests durchführen, um die Verfügbarkeit zu überprüfen, wenn Sie die aktuellste Verfügbarkeit eines Data Lake Storage Gen2-Kontos ermitteln möchten. Andere Metriken wie „Speichernutzung gesamt“, „Lese-/Schreibanforderungen“ und „Eingehend/Ausgehend“ stehen zur Verfügung, um von Überwachungsanwendungen genutzt zu werden, und können auch Warnungen auslösen, wenn Schwellenwerte (z. B. durchschnittliche Wartezeit oder Anzahl der Fehler pro Minute) überschritten werden.

## <a name="directory-layout-considerations"></a>Aspekte des Verzeichnislayouts

Beim Einfügen von Daten in eine Data Lake-Struktur ist es wichtig, die Struktur der Daten im Voraus zu planen, damit Sicherheit, Partitionierung und Verarbeitung effektiv genutzt werden können. Viele der folgenden Empfehlungen gelten für alle Big Data-Workloads. Für jede Workload gelten andere Anforderungen in Bezug auf die Nutzung der Daten, aber unten sind einige häufig verwendete Layouts angegeben, die bei IoT- und Batchszenarien berücksichtigt werden sollten.

### <a name="iot-structure"></a>IoT-Struktur

Bei IoT-Workloads können große Datenmengen im Datenspeicher landen, der übergreifend für verschiedene Produkte, Geräte, Organisationen und Kunden verwendet wird. Es ist wichtig, das Verzeichnislayout für nachgeschaltete Consumer in Bezug auf die Organisation, Sicherheit und effiziente Verarbeitung vorauszuplanen. Das folgende Layout kann hierbei als allgemeine Vorlage dienen:

*{Region}/{SubjectMatter(s)}/jjjj/{mm}/{tt}/{hh}/*

Die Zieltelemetrie für ein Flugzeugtriebwerk im Vereinigten Königreich kann beispielsweise wie die folgende Struktur aussehen:

*UK/Planes/BA1293/Engine1/2017/08/11/12/*

Es gibt einen wichtigen Grund dafür, das Datum am Ende der Verzeichnisstruktur anzuordnen. Falls Sie bestimmte Regionen oder Themen für Benutzer oder Gruppen sperren möchten, ist dies mit POSIX-Berechtigungen leicht möglich. Falls die Anforderung besteht, eine bestimmte Sicherheitsgruppe auf die Anzeige der Daten für das Vereinigte Königreich oder bestimmte Ebenen zu beschränken, wäre bei einer höheren Anordnung der Datumsstruktur andernfalls eine separate Berechtigung für viele Verzeichnisse unter jedem Stundenverzeichnis erforderlich. Außerdem würde sich die Anzahl von Verzeichnissen im Laufe der Zeit exponentiell erhöhen, wenn die Datumsstruktur höher angeordnet wäre.

### <a name="batch-jobs-structure"></a>Struktur von Batchaufträgen

Ein häufiger allgemeiner Ansatz bei der Batchverarbeitung ist die Anordnung der Daten in einem Verzeichnis des Typs „Eingang“ (In). Nachdem die Daten verarbeitet wurden, werden die neuen Daten dann in einem Verzeichnis des Typs „Ausgang“ (Out) angeordnet, damit sie von nachgelagerten Prozessen genutzt werden können. Diese Verzeichnisstruktur wird manchmal bei Aufträgen verwendet, bei denen einzelne Dateien verarbeitet werden müssen und ggf. kein Massively Parallel Processing für große Datasets erforderlich ist. Wie bei der oben empfohlenen IoT-Struktur auch, verfügt eine gute Verzeichnisstruktur über übergeordnete Verzeichnisse für Dinge wie Region und Themen (z. B. Organisation, Produkt/Hersteller). Diese Struktur ist hilfreich beim Schützen der Daten Ihrer gesamten Organisation und eine Verbesserung der Verwaltung von Daten in Ihren Workloads. Berücksichtigen Sie in der Struktur auch Datum und Uhrzeit, um eine bessere Organisation, gefilterte Suchen, Sicherheit und Automatisierung der Verarbeitung zu ermöglichen. Der Granularitätsgrad für die Datumsstruktur wird durch das Intervall bestimmt, nach dem die Daten hochgeladen oder verarbeitet werden, z.B. stündlich, täglich oder auch monatlich.

Es kann vorkommen, dass die Dateiverarbeitung nicht erfolgreich ist, weil Daten beschädigt sind oder ein unerwartetes Format haben. In diesen Fällen kann für die Verzeichnisstruktur die Nutzung des Ordners **/bad** vorteilhaft sein, in den die Dateien zur weiteren Untersuchung verschoben werden können. Über den Batchauftrag können ggf. auch die Berichterstellung oder die Benachrichtigungsvorgänge für diese fehlerhaften Dateien (*bad* files) abgewickelt werden, um einen manuellen Eingriff zu ermöglichen. Erwägen Sie die Verwendung der folgenden Vorlagenstruktur:

*{Region}/{SubjectMatter(s)}/In/jjjj/{mm}/{tt}/{hh}/* \
*{Region}/{SubjectMatter(s)}/Out/jjjjj/{mm}/{tt}/{hh}/* \
*{Region}/{SubjectMatter (e)}/Bad/jjjj/{mm}/{tt}/{hh}/*

Es kann beispielsweise sein, dass ein Marketingunternehmen tägliche Datenextrakte aus Kundenupdates von seinen Kunden in Nordamerika erhält. Vor und nach der Verarbeitung kann dies ggf. wie im folgenden Codeausschnitt aussehen:

*NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv*\
*NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv*

Bei der normalen Verarbeitung von Batchdaten direkt in Datenbanken, z.B. Hive oder herkömmlichen SQL-Datenbanken, sind die Ordner **/in** und **/out** nicht erforderlich, da die Ausgabe bereits in einem separaten Ordner für die Hive-Tabelle oder externe Datenbank angeordnet wird. Die täglichen Extrakte von Kunden werden dann beispielsweise in die entsprechenden Ordner eingefügt, und die Orchestrierung per Azure Data Factory, Apache Oozie oder Apache Airflow würde einen täglichen Hive- oder Spark-Auftrag auslösen, mit dem die Daten verarbeitet und in eine Hive-Tabelle geschrieben werden.