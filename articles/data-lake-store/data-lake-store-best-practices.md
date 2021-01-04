---
title: Bewährte Methoden zur Verwendung von Azure Data Lake Storage Gen1 | Microsoft-Dokumentation
description: Enthält eine Beschreibung der bewährten Methoden zur Datenerfassung, Datensicherheit und Leistung in Bezug auf die Verwendung von Azure Data Lake Storage Gen1 (ehemals Azure Data Lake Store).
services: data-lake-store
documentationcenter: ''
author: sachinsbigdata
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: sachins
ms.openlocfilehash: 9a5c5f9a4033b70a664071d6077a69f38c905093
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452215"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen1"></a>Bewährte Methoden zur Verwendung von Azure Data Lake Storage Gen1

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

In diesem Artikel erfahren Sie mehr über die bewährten Methoden und Aspekte in Bezug auf die Arbeit mit Azure Data Lake Storage Gen1. Der Artikel enthält Informationen zu den Bereichen Sicherheit, Leistung, Resilienz und Überwachung für Data Lake Storage Gen1. Vor der Einführung von Data Lake Storage Gen1 war die Arbeit mit wirklich großen Datenmengen (Big Data) in Diensten wie Azure HDInsight sehr komplex. Daten mussten per Sharding auf mehrere Blobspeicherkonten verteilt werden, um die Speicherung im Petabyte-Bereich und eine entsprechende optimale Leistung zu erzielen. Dank Data Lake Storage Gen1 gelten die meisten festen Grenzwerte im Hinblick auf die Größe und Leistung nicht mehr. In diesem Artikel werden aber trotzdem noch einige Aspekte beschrieben, damit Sie für Data Lake Storage Gen1 die bestmögliche Leistung erzielen können.

## <a name="security-considerations"></a>Sicherheitshinweise

Azure Data Lake Storage Gen1 ermöglicht POSIX-Zugriffssteuerungen und eine detaillierte Überwachung für Azure Active Directory-Benutzer, -Gruppen und -Dienstprinzipale. Diese Zugriffssteuerungen können auf vorhandene Dateien und Ordner festgelegt werden. Außerdem können die Zugriffssteuerungen zum Erstellen von Standardwerten verwendet werden, die auf neue Dateien oder Ordner angewendet werden können. Wenn Berechtigungen auf vorhandene Ordner und untergeordnete Objekte festgelegt werden, müssen die Berechtigungen rekursiv auf die einzelnen Objekte verteilt werden. Bei einer großen Anzahl von Dateien kann das Verteilen der Berechtigungen sehr lange dauern. Die Anzahl von Objekten, die pro Sekunde verarbeitet werden, kann zwischen 30 und 50 liegen. Planen Sie die Ordnerstruktur und die Benutzergruppen daher entsprechend. Andernfalls kann es beim Arbeiten mit Ihren Daten zu unvorhergesehenen Verzögerungen und Problemen kommen.

Angenommen, Sie verfügen über einen Ordner mit 100.000 untergeordneten Objekten. Wenn Sie den unteren Wert von 30 verarbeiteten Objekten pro Sekunde ansetzen, kann das Aktualisieren der Berechtigungen für den gesamten Ordner eine Stunde dauern. Weitere Details zu Data Lake Storage Gen1-Zugriffssteuerungslisten finden Sie unter [Zugriffssteuerung in Azure Data Lake Storage Gen1](data-lake-store-access-control.md). Zur Verbesserung der Leistung beim rekursiven Zuweisen von Zugriffssteuerungslisten können Sie das Azure Data Lake-Befehlszeilentool verwenden. Mit dem Tool werden mehrere Threads und die Logik für die rekursive Navigation erstellt, damit Zugriffssteuerungslisten schnell auf Millionen von Dateien angewendet werden können. Das Tool ist für Linux und Windows verfügbar, und die dazugehörige [Dokumentation](https://github.com/Azure/data-lake-adlstool) und [Downloads](https://aka.ms/adlstool-download) finden Sie auf GitHub. Sie können die gleichen Leistungsverbesserungen mit Ihren eigenen Tools erreichen, die Sie mit den [.NET](data-lake-store-data-operations-net-sdk.md) und [Java](data-lake-store-get-started-java-sdk.md) SDKs für Data Lake Storage Gen1 erstellt haben.

### <a name="use-security-groups-versus-individual-users"></a>Vergleich der Verwendung von Sicherheitsgruppen und einzelnen Benutzern

Beim Arbeiten mit Big Data in Data Lake Storage Gen1 wird in den meisten Fällen ein Dienstprinzipal genutzt, damit Dienste wie Azure HDInsight die Daten verwenden können. Es kann aber auch Fälle geben, in denen einzelne Benutzer ebenfalls Zugriff auf die Daten benötigen. In diesen Fällen müssen Sie Azure Active Directory-[Sicherheitsgruppen](data-lake-store-secure-data.md#create-security-groups-in-azure-active-directory) nutzen, anstatt Ordnern und Dateien einzelne Benutzer zuzuweisen.

Nachdem einer Sicherheitsgruppe Berechtigungen zugewiesen wurden, sind für das Hinzufügen oder Entfernen von Benutzern der Gruppe keine Updates für Data Lake Storage Gen1 erforderlich. So können Sie auch sicherstellen, dass die Grenze von [32 Zugriffs- und Standard-ACLs](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-storage-limits) nicht überschritten wird (dies umfasst auch die vier ACLs im POSIX-Format, die jeder Datei und jedem Ordner immer zugeordnet sind: [der zuständige Benutzer](data-lake-store-access-control.md#the-owning-user), [die zuständige Gruppe](data-lake-store-access-control.md#the-owning-group), [die Maske](data-lake-store-access-control.md#the-mask) und andere).

### <a name="security-for-groups"></a>Sicherheit für Gruppen

Wie bereits erwähnt, ist die Verwendung von Azure Active Directory-Sicherheitsgruppen am besten geeignet, wenn Benutzer auf Data Lake Storage Gen1 zugreifen müssen. Einige empfohlene Gruppen für den Einstieg sind beispielsweise **ReadOnlyUsers**, **WriteAccessUsers** und **FullAccessUsers** für den Stammordner des Kontos und separate Gruppen für wichtige Unterordner. Falls andere antizipierte Gruppen von Benutzern vorhanden sind, die ggf. später hinzugefügt werden sollen, aber noch nicht identifiziert wurden, können Sie die Erstellung von Dummy-Sicherheitsgruppen erwägen, die Zugriff auf bestimmte Ordner haben. Durch die Verwendung von Sicherheitsgruppen wird sichergestellt, dass Sie später keine langen Verarbeitungsdauern benötigen, um neue Berechtigungen für Tausende von Dateien zuzuweisen.

### <a name="security-for-service-principals"></a>Sicherheit für Dienstprinzipale

Azure Active Directory-Dienstprinzipale werden normalerweise von Diensten wie Azure HDInsight verwendet, um in Data Lake Storage Gen1 auf Daten zuzugreifen. Je nach den Zugriffsanforderungen der verschiedenen Workloads müssen unter Umständen einige Punkte beachtet werden, um die interne und externe Sicherheit der Organisation sicherzustellen. Für viele Kunden kann ein einzelner Azure Active Directory-Dienstprinzipal ausreichen, der für den Stammordner des Data Lake Storage Gen1-Kontos über vollständige Berechtigungen verfügt. Andere Kunden benötigen ggf. mehrere Cluster mit unterschiedlichen Dienstprinzipalen, wobei ein Cluster über Vollzugriff auf die Daten und ein anderer Cluster nur über Lesezugriff verfügt. Wie bei den Sicherheitsgruppen können Sie erwägen, einen Dienstprinzipal für jedes antizipierte Szenario (Lesen, Schreiben, Vollzugriff) zu erstellen, nachdem ein Data Lake Storage Gen1-Konto erstellt wurde.

### <a name="enable-the-data-lake-storage-gen1-firewall-with-azure-service-access"></a>Aktivieren der Data Lake Storage Gen1-Firewall mit Azure-Dienstzugriff

Data Lake Storage Gen1 unterstützt die Option zum Aktivieren einer Firewall und Beschränken des Zugriffs ausschließlich auf Azure-Dienste. Dies wird für einen kleineren Angriffsvektor gegen Angriffe von außen empfohlen. Die Firewall kann im Azure-Portal über das Data Lake Storage Gen1-Konto aktiviert werden, indem die Option **Firewall** > **Firewall aktivieren (EIN)**  > **Allow access to Azure services** (Zugriff auf Azure-Dienste erlauben) verwendet wird.

![Firewalleinstellungen in Data Lake Storage Gen1](./media/data-lake-store-best-practices/data-lake-store-firewall-setting.png "Firewalleinstellungen in Data Lake Storage Gen1")

Nachdem die Firewall aktiviert wurde, haben nur Azure-Dienste wie HDInsight, Data Factory, Azure Synapse Analytics usw. Zugriff auf Data Lake Storage Gen1. Aufgrund der von Azure verwendeten internen Netzwerkadressübersetzung unterstützt die Data Lake Storage Gen1-Firewall die Einschränkung bestimmter Dienste per IP-Adresse nicht. Dies ist nur für Einschränkungen von Endpunkten außerhalb von Azure gedacht, z.B. lokale Standorte.

## <a name="performance-and-scale-considerations"></a>Leistungs- und Skalierungsaspekte

Eines der leistungsstärksten Features von Data Lake Storage Gen1 ist die Aufhebung der festen Grenzwerte für den Datendurchsatz. Durch die Beseitigung der Grenzwerte können Kunden ihren Datenumfang und die dazugehörigen Leistungsanforderungen steigern, ohne für die Daten das Sharding durchführen zu müssen. Einer der wichtigsten Aspekte zur Optimierung der Data Lake Storage Gen1-Leistung ist, dass die Leistung am höchsten ist, wenn für Parallelität gesorgt ist.

### <a name="improve-throughput-with-parallelism"></a>Verbessern des Durchsatzes mit Parallelität

Erwägen Sie die Bereitstellung von 8 bis 12 Threads pro Core, um den bestmöglichen Lese-/Schreibdurchsatz zu erzielen. Der Grund ist, dass Lese-/Schreibvorgänge bei nur einem Thread ggf. blockiert werden und eine höhere Zahl von Threads auf der VM zu einem höheren Gleichzeitigkeitsgrad führt. Überwachen Sie die CPU-Auslastung der VM, um sicherzustellen, dass die Ebenen fehlerfrei sind und die Parallelität erhöht werden kann.

### <a name="avoid-small-file-sizes"></a>Vermeiden geringer Dateigrößen

POSIX-Berechtigungen und die Überwachung in Data Lake Storage Gen1 führen zu Mehraufwand, der deutlich wird, wenn viele kleine Dateien verwendet werden. Eine bewährte Methode besteht darin, die Daten zu größeren Dateien zusammenzufassen, anstatt Tausende oder Millionen von kleinen Dateien in Data Lake Storage Gen1 zu schreiben. Die Vermeidung von geringen Dateigrößen kann mehrere Vorteile mit sich bringen, z.B.:

* Reduzierung der Authentifizierungsprüfungen für eine große Zahl von Dateien
* Reduzierter Verbindungsaufwand zum Öffnen von Dateien
* Schnelles Kopieren/Replizieren
* Weniger zu verarbeitende Dateien beim Aktualisieren von Data Lake Storage Gen1-POSIX-Berechtigungen

Für Dateien empfiehlt sich eine Größe von mindestens 256 MB (je nachdem, welche Dienste und Workloads die Daten verwenden). Falls die Dateigrößen vor dem Eintreffen in Data Lake Storage Gen1 nicht zu Batches zusammengefasst werden können, können Sie einen separaten Komprimierungsauftrag verwenden, mit dem diese Dateien zu größeren Dateien zusammengefasst werden. Weitere Informationen und Empfehlungen zu Dateigrößen und zur Organisation der Daten in Data Lake Storage Gen1 finden Sie unter [Strukturieren Ihres Datasets](data-lake-store-performance-tuning-guidance.md#structure-your-data-set).

### <a name="large-file-sizes-and-potential-performance-impact"></a>Große Dateien und potenzielle Auswirkungen auf die Leistung

Data Lake Storage Gen1 unterstützt zwar große Dateien bis in den Petabytebereich, aber zur Erzielung der optimalen Leistung und je nach dem Prozess zum Lesen der Daten ist es unter Umständen nicht ideal, wenn die durchschnittliche Größe oberhalb von 2 GB liegt. Wenn Sie beispielsweise **Distcp** zum Kopieren von Daten zwischen Standorten oder unterschiedlichen Speicherkonten verwenden, sind Dateien die feinste Granularitätsebene, die zum Ermitteln von Zuordnungsaufgaben herangezogen wird. Wenn Sie also zehn Dateien kopieren, die jeweils eine Größe von 1 TB haben, werden maximal zehn Zuordnungen durchgeführt. Falls Sie über eine hohe Zahl von Dateien mit zugewiesenen Zuordnungen verfügen, arbeiten die Zuordnungen anfänglich außerdem parallel, um die großen Dateien zu verschieben. Wenn der Auftrag dem Ende entgegen geht, sind nur noch wenige Zuordnungen vorhanden, und es kann passieren, dass nur noch eine Zuordnung zu einer großen Datei besteht. Microsoft hat Verbesserungsvorschläge an Distcp gesendet, damit dieses Problem in zukünftigen Hadoop-Versionen behoben werden kann.

Ein weiteres Beispiel, das berücksichtigt werden sollte, ist die Verwendung von Azure Data Lake Analytics mit Data Lake Storage Gen1. Je nach der Verarbeitung, die vom Extraktor durchgeführt wird, kann die Leistung für einige unteilbare Dateien (z.B. XML oder JSON) beeinträchtigt werden, wenn die Größe 2 GB übersteigt. In Fällen, in denen Dateien von einem Extraktor geteilt werden können (z.B. CSV), sind große Dateien zu bevorzugen.

### <a name="capacity-plan-for-your-workload"></a>Kapazitätsplan für Ihre Workload

Azure Data Lake Storage Gen1 entfernt die festen Grenzwerte für die E/A-Drosselung, die für Blob Storage-Konten gelten. Es sind aber noch weiche Grenzwerte vorhanden, die berücksichtigt werden müssen. Die Standardgrenzwerte für die Eingangs- und Ausgangsdrosselung erfüllen die Anforderungen der meisten Szenarien. Wenden Sie sich an den Microsoft-Support, falls die Grenzwerte für Ihre Workload erhöht werden müssen. Sehen Sie sich auch die Grenzwerte während der Proof-of-Concept-Phase an, damit die Grenzwerte für die E/A-Drosselung während der Produktion nicht erreicht werden. Wenn dies passiert, kann es erforderlich sein, auf eine manuelle Erhöhung durch das Technikerteam von Microsoft zu warten. Wenn es zu einer E/A-Drosselung kommt, gibt Azure Data Lake Storage Gen1 den Fehlercode 429 zurück. Idealerweise sollte dann eine geeignete Richtlinie für Wiederholungsversuche mit ansteigender Wartezeit verwendet werden.

### <a name="optimize-writes-with-the-data-lake-storage-gen1-driver-buffer"></a>Optimieren von „Schreibvorgängen“ mit dem Data Lake Storage Gen1-Treiberpuffer

Führen Sie Schreibvorgänge so eng an der Größe des Data Lake Storage Gen1-Treiberpuffers wie möglich durch, um die Leistung zu optimieren und den IOPS-Wert beim Schreiben in Data Lake Storage Gen1 von Hadoop aus zu reduzieren. Versuchen Sie, die Puffergröße vor dem Leeren nicht zu überschreiten, z.B. beim Streamen mit Apache Storm- oder Spark-Streamingworkloads. Beim Schreiben in Data Lake Storage Gen1 aus HDInsight/Hadoop ist es wichtig zu wissen, dass Data Lake Storage Gen1 über einen Treiber mit einem Puffer von 4 MB verfügt. Wie viele Dateisystemtreiber auch, kann dieser Puffer manuell geleert werden, bevor die Größe von 4 MB erreicht wird. Falls nicht, wird das Leeren sofort in den Speicher durchgeführt, wenn der nächste Schreibvorgang die maximale Größe des Puffers überschreitet. Soweit möglich, sollten Sie eine Überschreitung oder eine signifikante Unterschreitung des Puffers vermeiden, wenn eine Richtlinie für die Synchronisierung oder das Leeren nach Anzahl oder Zeitfenster verwendet wird.

## <a name="resiliency-considerations"></a>Überlegungen zur Resilienz

Beim Erstellen der Systemarchitektur mit Data Lake Storage Gen1 oder einem beliebigen Clouddienst müssen Sie Ihre Verfügbarkeitsanforderungen und die Reaktion auf mögliche Unterbrechungen des Diensts berücksichtigen. Ein Problem kann ggf. einer bestimmten Instanz oder einer gesamten Region zugeordnet sein, sodass es wichtig ist, für beide Fälle über einen Plan zu verfügen. Je nach den **Recovery Time Objective**- und **Recovery Point Objective**-SLAs für Ihre Workload können Sie eine aggressivere oder weniger aggressive Strategie für die Hochverfügbarkeit und Notfallwiederherstellung wählen.

### <a name="high-availability-and-disaster-recovery"></a>Hochverfügbarkeit und Notfallwiederherstellung

Hochverfügbarkeit (High availability, HA) und Notfallwiederherstellung (Disaster Recovery, DR) können auch kombiniert werden, obwohl die Strategien sich leicht unterscheiden – vor allem in Bezug auf die Daten. Data Lake Storage Gen1 ermöglicht bereits im Hintergrund eine Dreifachreplikation als Schutz vor lokalen Hardwarefehlern. Da die regionsübergreifende Replikation außerdem nicht integriert ist, müssen Sie dies selbst verwalten. Beim Erstellen eines Plans für die Hochverfügbarkeit benötigt die Workload bei einer Dienstunterbrechung so schnell wie möglich Zugriff auf die aktuellen Daten, indem zu einer separat replizierten Instanz gewechselt wird, die sich am lokalen Standort oder in einer neuen Region befindet.

Bei einer Strategie für die Notfallwiederherstellung ist es als Vorbereitung auf den unwahrscheinlichen Fall, dass eine Region aufgrund einer Katastrophe ausfällt, außerdem wichtig, dass Daten in einer anderen Region repliziert werden. Diese Daten können ursprünglich den replizierten Hochverfügbarkeitsdaten entsprechen. Sie müssen aber außerdem Ihre Anforderungen in Bezug auf Edgefälle berücksichtigen, z.B. Datenbeschädigungen, in denen es ratsam sein kann, regelmäßige Momentaufnahmen als Fallbacklösung zu erstellen. Je nach Wichtigkeit und Größe der Daten können Sie erwägen, rollierende Deltamomentaufnahmen für Zeiträume von 1 Stunde, 6 Stunden und 24 Stunden im lokalen bzw. sekundären Speicher zu erstellen. Dies richtet sich nach den Risikotoleranzen.

Für die Datenresilienz mit Data Lake Storage Gen1 wird empfohlen, für Ihre Daten in einer separaten Region die Georeplikation mit einer Häufigkeit durchzuführen, die für Ihre HA/DR-Anforderungen geeignet ist. Ideal ist hierbei die stündliche Durchführung. Bei dieser Replikationshäufigkeit wird die Anzahl von umfangreichen Datenverschiebungen verringert, bei denen es zu Durchsatzkonflikten mit dem Hauptsystem kommen kann, und es wird eine Verbesserung des RPO-Werts (Recovery Point Objective) erzielt. Außerdem sollten Sie sich überlegen, auf welche Weise für die Anwendung, für die Data Lake Storage Gen1 genutzt wird, ein automatisches Failover auf das sekundäre Konto ausgeführt werden kann. Hierfür können Trigger oder die Länge von fehlerhaften Versuchen überwacht werden, oder es kann als Mindestmaßnahme eine Benachrichtigung an Administratoren gesendet werden, damit diese manuell eingreifen können. Beachten Sie, dass abgewogen werden sollte, ob ein Failover oder das Warten auf die Wiedererlangung des Onlinezustands für einen Dienst mit weniger Nachteilen verbunden ist. Wenn die Replikation der Daten noch nicht abgeschlossen ist, kann ein Failover unter Umständen zu Datenverlust, Inkonsistenzen oder einer komplexen Datenzusammenführung führen.

Unten sind die wichtigsten drei Empfehlungen für die Orchestrierung der Replikation zwischen Data Lake Storage Gen1-Konten und die wichtigsten Unterschiede angegeben.

|  |Distcp  |Azure Data Factory  |AdlCopy  |
|---------|---------|---------|---------|
|**Skalierungslimits**     | Durch Workerknoten begrenzt        | Durch maximale Einheiten für Clouddatenverschiebungen begrenzt        | Durch Analytics-Einheiten begrenzt        |
|**Unterstützt das Kopieren von Deltas**     |   Ja      | Nein         | Nein         |
|**Integrierte Orchestrierung**     |  Nein (Verwendung von Oozie Airflow- oder Cron-Aufträgen)       | Ja        | Nein (Verwendung von Azure Automation oder Windows-Aufgabenplanung)         |
|**Unterstützte Dateisysteme**     | ADL, HDFS, WASB, S3, GS, CFS        |Verschiedene, siehe [Connectors](../data-factory/connector-azure-blob-storage.md).         | ADL zu ADL, WASB zu ADL (nur in derselben Region)        |
|**Betriebssystemunterstützung**     |Beliebiges Betriebssystem mit Hadoop         | –          | Windows 10         |

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Verwenden von Distcp für Datenverschiebungen zwischen zwei Standorten

„Distcp“ ist die Abkürzung von „distributed copy“. Es handelt sich hierbei um ein Linux-Befehlszeilentool, das Teil von Hadoop ist und verteilte Datenverschiebungen zwischen zwei Standorten ermöglicht. Die beiden Standorte können Data Lake Storage Gen1, HDFS, WASB oder S3 sein. Bei diesem Tool werden MapReduce-Aufträge in einem Hadoop-Cluster (z.B. HDInsight) verwendet, um das Aufskalieren auf allen Knoten durchzuführen. Distcp ist die schnellste Möglichkeit zum Verschieben von Big Data-Datenmengen ohne spezielle Einrichtungen für die Netzwerkkomprimierung. Distcp verfügt auch über eine Option zum alleinigen Aktualisieren von Deltadaten zwischen zwei Standorten und ermöglicht die Durchführung von automatischen Wiederholungsversuchen und die dynamische Computeskalierung. Dieser Ansatz ist äußerst effizient, was das Replizieren von Elementen wie Hive/Spark-Tabellen betrifft, die über viele große Dateien in einem einzelnen Verzeichnis verfügen, wenn Sie lediglich die geänderten Daten kopieren möchten. Aus diesen Gründen ist Distcp das empfohlene Tool zum Kopieren von Daten zwischen Big Data-Speichern.

Kopieraufträge können von Apache Oozie-Workflows ausgelöst werden, indem Häufigkeits- oder Datentrigger verwendet werden, und über Linux-Cron-Aufträge. Für intensive Replikationsaufträge wird empfohlen, einen separaten HDInsight Hadoop-Cluster zu erstellen, der speziell für die Kopieraufträge optimiert und skaliert werden kann. So wird sichergestellt, dass es für Kopieraufträge nicht zu Konflikten mit kritischen Aufträgen kommt. Falls die Replikation mit einer ausreichend geringen Häufigkeit erfolgt, kann der Cluster zwischen den einzelnen Aufträgen ggf. sogar heruntergefahren werden. Stellen Sie beim Ausführen von Failovern in eine sekundäre Region sicher, dass in der sekundären Region ein weiterer Cluster erstellt wird, damit neue Daten wieder unter dem primären Data Lake Storage Gen1-Konto repliziert werden können, nachdem es wieder verfügbar ist. Beispiele für die Verwendung von Distcp finden Sie unter [Kopieren von Daten zwischen Azure Storage-Blobs und Data Lake Storage Gen1 mithilfe von DistCp](data-lake-store-copy-data-wasb-distcp.md).

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Verwenden von Azure Data Factory zum Planen von Kopieraufträgen

Azure Data Factory kann auch verwendet werden, um Kopieraufträge mit einer **Kopieraktivität** zu planen, und über den **Kopier-Assistenten** kann sogar eine Häufigkeit dafür eingerichtet werden. Beachten Sie hierbei, dass für Azure Data Factory eine Begrenzung in Bezug auf die Einheiten der Clouddatenverschiebung (Data Movement Units, DMUs) gilt und für große Datenworkloads irgendwann eine Durchsatz-/Computebeschränkung greift. Außerdem sind mit Azure Data Factory derzeit keine Deltaupdates zwischen Data Lake Storage Gen1-Konten möglich, sodass für Ordner wie Hive-Tabellen für die Replikation eine vollständige Kopie erforderlich ist. Weitere Informationen zum Kopieren mit Data Factory finden Sie im [Handbuch zur Leistung und Optimierung der Kopieraktivität](../data-factory/copy-activity-performance.md).

### <a name="adlcopy"></a>AdlCopy

AdlCopy ist ein Windows-Befehlszeilentool, mit dem Sie Daten zwischen zwei Data Lake Storage Gen1-Konten nur in derselben Region kopieren können. Das Tool AdlCopy verfügt über eine Standalone-Option oder die Option zum Verwenden eines Azure Data Lake Analytics-Kontos zur Ausführung Ihres Kopierauftrags. Obwohl das Tool ursprünglich für bedarfsabhängige Kopien erstellt wurde und nicht als robuste Anwendung gedacht war, verfügt es noch über eine andere Option, um verteilte Kopiervorgänge für Data Lake Storage Gen1-Konten in derselben Region durchzuführen. Aus Gründen der Zuverlässigkeit wird empfohlen, die Data Lake Analytics-Premium-Option für alle Produktionsworkloads zu verwenden. Die Standalone-Version kann Antworten vom Typ „Ausgelastet“ zurückgeben und verfügt über eine begrenzte Skalierung und Überwachung.

Wie bei Distcp auch, muss AdlCopy mit einer Anwendung wie Azure Automation oder der Windows-Aufgabenplanung orchestriert werden. Ebenso wie Data Factory weist auch AdlCopy keine Unterstützung dafür auf, nur aktualisierte Dateien zu kopieren, sondern es wird erneut kopiert, und vorhandene Dateien werden überschrieben. Weitere Informationen und Beispiele für die Verwendung von AdlCopy finden Sie unter [Kopieren von Daten aus Azure Storage-Blobs in Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md).

## <a name="monitoring-considerations"></a>Aspekte der Überwachung

Data Lake Storage Gen1 verfügt über ausführliche Diagnoseprotokolle und Überwachungsfunktionen. Für Data Lake Storage Gen1 werden im Azure-Portal unter dem Data Lake Storage Gen1-Konto und in Azure Monitor einige grundlegende Metriken bereitgestellt. Die Verfügbarkeit von Data Lake Storage Gen1 wird im Azure-Portal angezeigt. Diese Metrik wird aber alle sieben Minuten aktualisiert und kann nicht über eine öffentlich verfügbar gemachte API abgefragt werden. Sie müssen Ihre eigenen synthetischen Tests durchführen, um die Verfügbarkeit zu überprüfen, wenn Sie die aktuellste Verfügbarkeit eines Data Lake Storage Gen1-Kontos ermitteln möchten. Für andere Metriken, z.B. die gesamte Speicherauslastung, Lese-/Schreibanforderungen und Eingang/Ausgang, kann die Aktualisierung bis zu 24 Stunden dauern. Aktuellere Metriken über Hadoop-Befehlszeilentools oder die Aggregierung von Protokollinformationen müssen also manuell berechnet werden. Die schnellste Möglichkeit zur Erzielung der aktuellsten Speicherauslastung ist die Ausführung dieses HDFS-Befehls über einen Hadoop-Clusterknoten (z.B. Hauptknoten):

```console
hdfs dfs -du -s -h adl://<adlsg1_account_name>.azuredatalakestore.net:443/
```

### <a name="export-data-lake-storage-gen1-diagnostics"></a>Exportieren der Data Lake Storage Gen1-Diagnose

Eine der schnellsten Möglichkeiten, um aus Data Lake Storage Gen1 Zugriff auf durchsuchbare Protokolle zu erhalten, ist die Aktivierung des Protokollversands für **Log Analytics**. Verwenden Sie hierfür das Blatt **Diagnose** für das Data Lake Storage Gen1-Konto. Sie haben so direkten Zugriff auf eingehende Protokolle mit Zeit- und Inhaltsfiltern und können Warnungsoptionen (E-Mail/Webhook) nutzen, die in 15-Minuten-Intervallen ausgelöst werden. Eine Anleitung hierzu finden Sie unter [Zugreifen auf Diagnoseprotokolle für Azure Data Lake Storage Gen1](data-lake-store-diagnostic-logs.md).

Falls Sie mehr Echtzeitwarnungen nutzen und mehr Kontrolle darüber haben möchten, wo die Protokolle verfügbar sein sollen, können Sie die Protokolle nach Azure EventHub exportieren. Hier können Inhalte einzeln oder in einem Zeitfenster analysiert werden, um Echtzeitbenachrichtigungen an eine Warteschlange zu senden. Eine separate Anwendung, z.B. eine [Logik-App](../connectors/connectors-create-api-azure-event-hubs.md), kann die Warnungen dann nutzen und an den entsprechenden Kanal kommunizieren und Metriken an Überwachungstools wie NewRelic, Datadog oder AppDynamics senden. Wenn Sie Drittanbietertools verwenden, z.B. ElasticSearch, können Sie die Protokolle alternativ dazu nach Blob Storage exportieren und das [Azure Logstash-Plug-In](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob) verwenden, um die Daten in Ihrem Elasticsearch-, Kibana- und Logstash-Stack (ELK) zu nutzen.

### <a name="turn-on-debug-level-logging-in-hdinsight"></a>Aktivieren der Protokollierung auf Debugebene in HDInsight

Wenn das Senden von Data Lake Storage Gen1-Protokollen nicht aktiviert ist, ermöglicht Azure HDInsight auch das Aktivieren der [clientseitigen Protokollierung für Data Lake Storage Gen1](data-lake-store-performance-tuning-mapreduce.md) über log4j. Sie müssen unter **Ambari** > **YARN** > **Config** > **Advanced yarn-log4j configurations** die folgende Eigenschaft festlegen:

`log4j.logger.com.microsoft.azure.datalake.store=DEBUG`

Nachdem die Eigenschaft festgelegt wurde und die Knoten neu gestartet wurden, wird die Data Lake Storage Gen1-Diagnose in die YARN-Protokolle auf den Knoten (/tmp/\<user\>/yarn.log) geschrieben, und wichtige Details wie Fehler oder die Drosselung (Fehlercode HTTP 429) können überwacht werden. Diese Informationen können auch in Azure Monitor-Protokollen oder an jedem anderen Ziel überwacht werden, das im Data Lake Storage Gen1-Konto auf dem Blatt [Diagnose](data-lake-store-diagnostic-logs.md) für das Empfangen von Protokollen festgelegt ist. Es wird empfohlen, mindestens die clientseitige Protokollierung zu aktivieren oder die Protokollversandoption von Data Lake Storage Gen1 für die Sichtbarkeit von Vorgängen und das vereinfachte Debuggen zu nutzen.

### <a name="run-synthetic-transactions"></a>Ausführen von synthetischen Transaktionen

Derzeit weist die Dienstverfügbarkeitsmetrik für Data Lake Storage Gen1 im Azure-Portal ein Aktualisierungszeitfenster von sieben Minuten auf. Außerdem kann zum Abfragen keine öffentlich verfügbar gemachte API verwendet werden. Aus diesem Grund wird empfohlen, eine einfache Anwendung zu erstellen, mit der synthetische Transaktionen für Data Lake Storage Gen1 durchgeführt werden, um eine minutengenaue Verfügbarkeit zu erzielen. Ein Beispiel kann die Erstellung eines WebJobs, einer Logik-App oder einer Azure-Funktionen-App sein, um einen Lese-, Erstellungs- und Aktualisierungsvorgang für Data Lake Storage Gen1 durchzuführen und die Ergebnisse an Ihre Überwachungslösung zu senden. Die Vorgänge können in einem temporären Ordner erfolgen und nach dem Test dann gelöscht werden. Der Test kann je nach den jeweiligen Anforderungen beispielsweise alle 30 bis 60 Sekunden durchgeführt werden.

## <a name="directory-layout-considerations"></a>Aspekte des Verzeichnislayouts

Beim Einfügen von Daten in eine Data Lake-Struktur ist es wichtig, die Struktur der Daten im Voraus zu planen, damit Sicherheit, Partitionierung und Verarbeitung effektiv genutzt werden können. Viele der folgenden Empfehlungen treffen für Azure Data Lake Storage Gen1, Blob Storage und HDFS zu. Für jede Workload gelten andere Anforderungen in Bezug auf die Nutzung der Daten, aber unten sind einige häufig verwendete Layouts angegeben, die bei IoT- und Batchszenarien berücksichtigt werden sollten.

### <a name="iot-structure"></a>IoT-Struktur

Bei IoT-Workloads können große Datenmengen im Datenspeicher landen, der übergreifend für verschiedene Produkte, Geräte, Organisationen und Kunden verwendet wird. Es ist wichtig, das Verzeichnislayout für nachgeschaltete Consumer in Bezug auf die Organisation, Sicherheit und effiziente Verarbeitung vorauszuplanen. Das folgende Layout kann hierbei als allgemeine Vorlage dienen:

```console
{Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/
```

Die Zieltelemetrie für ein Flugzeugtriebwerk im Vereinigten Königreich kann beispielsweise wie die folgende Struktur aussehen:

```console
UK/Planes/BA1293/Engine1/2017/08/11/12/
```

Es gibt einen wichtigen Grund dafür, das Datum am Ende der Ordnerstruktur anzuordnen. Falls Sie bestimmte Regionen oder Themen für Benutzer oder Gruppen sperren möchten, ist dies mit POSIX-Berechtigungen leicht möglich. Falls die Anforderung besteht, eine bestimmte Sicherheitsgruppe auf die Anzeige der Daten für das Vereinigte Königreich oder bestimmte Ebenen zu beschränken, wäre bei einer höheren Anordnung der Datumsstruktur andernfalls eine separate Berechtigung für viele Ordner unter jedem Stundenordner erforderlich. Außerdem würde sich die Anzahl von Ordnern im Laufe der Zeit exponentiell erhöhen, wenn die Datumsstruktur höher angeordnet wäre.

### <a name="batch-jobs-structure"></a>Struktur von Batchaufträgen

Ein häufiger allgemeiner Ansatz bei der Batchverarbeitung ist die Anordnung der Daten in einem Ordner vom Typ „Eingang“ (In). Nachdem die Daten verarbeitet wurden, werden die neuen Daten dann in einem Ordner vom Typ „Ausgang“ (Out) angeordnet, damit sie von nachgeschalteten Prozessen genutzt werden können. Diese Verzeichnisstruktur wird manchmal bei Aufträgen verwendet, bei denen einzelne Dateien verarbeitet werden müssen und ggf. kein Massively Parallel Processing für große Datasets erforderlich ist. Wie bei der oben empfohlenen IoT-Struktur auch, verfügt eine gute Verzeichnisstruktur über übergeordnete Ordner für Dinge wie Region und Themen (z.B. Organisation, Produkt/Producer). Diese Struktur ist hilfreich beim Schützen der Daten Ihrer gesamten Organisation und eine Verbesserung der Verwaltung von Daten in Ihren Workloads. Berücksichtigen Sie in der Struktur auch Datum und Uhrzeit, um eine bessere Organisation, gefilterte Suchen, Sicherheit und Automatisierung der Verarbeitung zu ermöglichen. Der Granularitätsgrad für die Datumsstruktur wird durch das Intervall bestimmt, nach dem die Daten hochgeladen oder verarbeitet werden, z.B. stündlich, täglich oder auch monatlich.

Es kann vorkommen, dass die Dateiverarbeitung nicht erfolgreich ist, weil Daten beschädigt sind oder ein unerwartetes Format haben. In diesen Fällen kann für die Verzeichnisstruktur die Nutzung des Ordners **/bad** vorteilhaft sein, in den die Dateien zur weiteren Untersuchung verschoben werden können. Über den Batchauftrag können ggf. auch die Berichterstellung oder die Benachrichtigungsvorgänge für diese fehlerhaften Dateien (*bad* files) abgewickelt werden, um einen manuellen Eingriff zu ermöglichen. Erwägen Sie die Verwendung der folgenden Vorlagenstruktur:

```console
{Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
{Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
{Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/
```

Es kann beispielsweise sein, dass ein Marketingunternehmen tägliche Datenextrakte aus Kundenupdates von seinen Kunden in Nordamerika erhält. Vor und nach der Verarbeitung kann dies ggf. wie im folgenden Codeausschnitt aussehen:

```console
NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv
```

Bei der normalen Verarbeitung von Batchdaten direkt in Datenbanken, z.B. Hive oder herkömmlichen SQL-Datenbanken, sind die Ordner **/in** und **/out** nicht erforderlich, da die Ausgabe bereits in einem separaten Ordner für die Hive-Tabelle oder externe Datenbank angeordnet wird. Die täglichen Extrakte von Kunden werden dann beispielsweise in die entsprechenden Ordner eingefügt, und die Orchestrierung per Azure Data Factory, Apache Oozie oder Apache Airflow würde einen täglichen Hive- oder Spark-Auftrag auslösen, mit dem die Daten verarbeitet und in eine Hive-Tabelle geschrieben werden.

## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Zugriffssteuerung in Azure Data Lake Storage Gen1](data-lake-store-access-control.md)
* [Sicherheit in Azure Data Lake Storage Gen1](data-lake-store-security-overview.md)
* [Optimieren der Leistung von Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-guidance.md)
* [Anleitung für die Leistungsoptimierung für Spark in HDInsight und Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-spark.md)
* [Anleitung für die Leistungsoptimierung für Hive in HDInsight und Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-hive.md)
* [Erstellen von HDInsight-Clustern mit Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
