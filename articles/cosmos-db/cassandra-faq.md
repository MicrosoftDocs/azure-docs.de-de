---
title: Häufig gestellte Fragen zur Cassandra-API für Azure Cosmos DB
description: Hier finden Sie Antworten auf häufig gestellte Fragen zur Cassandra-API für Azure Cosmos DB.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 08/12/2020
ms.author: thvankra
ms.openlocfilehash: 1368a3174af08f557b6d08f298fba015601d568c
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030831"
---
# <a name="frequently-asked-questions-about-the-cassandra-api-in-azure-cosmos-db"></a>Häufig gestellte Fragen zur Cassandra-API in Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

In diesem Artikel werden die Funktionsunterschiede zwischen Apache Cassandra und der Cassandra-API in Azure Cosmos DB beschrieben. Außerdem finden Sie hier Antworten auf häufig gestellte Fragen zur Cassandra-API in Azure Cosmos DB.

## <a name="key-differences-between-apache-cassandra-and-the-cassandra-api"></a>Wichtige Unterschiede zwischen Apache Cassandra und der Cassandra-API

- Apache Cassandra empfiehlt für Partitionsschlüssel eine Größenbeschränkung von 100 MB. Die Cassandra-API für Azure Cosmos DB lässt bis zu 20 GB pro Partition zu.
- Apache Cassandra ermöglicht Ihnen das Deaktivieren von dauerhaften Commits. Sie können das Schreiben in das Commitprotokoll überspringen und direkt zu den Memtables wechseln. Dies kann zu Datenverlusten führen, wenn der Knoten ausfällt, bevor Memtables in SSTables auf dem Datenträger geleert werden. Azure Cosmos DB führt immer dauerhafte Commits aus, um Datenverluste zu verhindern.
- Bei Apache Cassandra kann es zu einer verringerten Leistung kommen, wenn die Workload viele Ersetzungen oder Löschungen umfasst. Der Grund dafür sind Tombstones, die von der Leseworkload übersprungen werden müssen, um die neuesten Daten abzurufen. Bei der Cassandra-API kommt es nicht zu einer verringerten Leseleistung, wenn die Workload viele Ersetzungen oder Löschungen umfasst.
- In Szenarien mit hohen Ersetzungsworkloads muss die Komprimierung ausgeführt werden, um SSTables auf dem Datenträger zusammenzuführen. (Die Zusammenführung ist erforderlich, weil Apache Cassandra-Schreibvorgänge nur angefügt werden. Mehrere Aktualisierungen werden als einzelne SSTable-Einträge gespeichert, die regelmäßig zusammengeführt werden müssen.) Diese Situation kann auch zu einer geringeren Leseleistung während der Komprimierung führen. Bei der Cassandra-API kommt es nicht zu dieser Leistungsbeeinträchtigung, weil die API keine Komprimierung implementiert.
- Das Festlegen eines Replikationsfaktors von „1“ ist mit Apache Cassandra möglich. Es führt jedoch zu geringer Verfügbarkeit, wenn der einzige Knoten mit den Daten ausfällt. Dies ist bei der Cassandra-API für Azure Cosmos DB kein Problem, weil immer ein Replikationsfaktor von „4“ (Quorum von „3“) verwendet wird.
- Das Hinzufügen oder Entfernen von Knoten in Apache Cassandra erfordert manuelle Eingriffe sowie eine hohe CPU-Auslastung auf dem neuen Knoten, während vorhandene Knoten einige ihrer Tokenbereiche auf den neuen Knoten verschieben. Das ist auch bei der Außerbetriebnahme eines vorhandenen Knotens der Fall. Die Cassandra-API kann jedoch horizontal skaliert werden, ohne dass Probleme beim Dienst oder bei der Anwendung auftreten.
- Sie müssen nicht wie in Apache Cassandra **num_tokens** auf jedem Knoten im Cluster festlegen. Knoten und Tokenbereiche werden von Azure Cosmos DB vollständig verwaltet.
- Die Cassandra-API wird vollständig verwaltet. Sie benötigen nicht die **nodetool**-Befehle wie „Repair“ und „Decommission“, die in Apache Cassandra verwendet werden.

## <a name="other-frequently-asked-questions"></a>Weitere häufig gestellte Fragen

### <a name="what-protocol-version-does-the-cassandra-api-support"></a>Welche Protokollversion unterstützt die Cassandra-API?

Die Cassandra-API für Azure Cosmos DB unterstützt CQL, Version 3.x. Die CQL-Kompatibilität basiert auf dem öffentlichen [Apache Cassandra-GitHub-Repository](https://github.com/apache/cassandra/blob/trunk/doc/cql3/CQL.textile). Wenn Sie Feedback zur Unterstützung anderer Protokolle haben, teilen Sie uns dies per [Uservoice-Feedback](https://feedback.azure.com/forums/263030-azure-cosmos-db) mit, oder senden Sie eine E-Mail an [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="why-is-choosing-throughput-for-a-table-a-requirement"></a>Warum muss der Durchsatz für eine Tabelle ausgewählt werden?

Azure Cosmos DB legt den Standarddurchsatz für Ihren Container abhängig davon fest, ob Sie die Tabelle im Azure-Portal oder über CQL erstellt haben.

Azure Cosmos DB bietet Garantien für Leistung und Wartezeit (mit Obergrenzen für Vorgänge). Diese Garantien sind möglich, wenn das Modul Governance für die Vorgänge des Mandanten erzwingen kann. Durch Festlegen des Durchsatzes wird sichergestellt, dass Sie den garantierten Durchsatz und die garantierte Wartezeit erhalten. Hierzu wird die entsprechende Kapazität von der Plattform reserviert, um den Erfolg des Vorgangs zu gewährleisten.
Sie können den [Durchsatz flexibel anpassen](manage-scale-cassandra.md), um von Schwankungen bei der Nutzung Ihrer Anwendung zu profitieren und Kosten zu sparen.

Das Durchsatzkonzept wird im Artikel [Anforderungseinheiten in Azure Cosmos DB](request-units.md) erläutert. Der Durchsatz für eine Tabelle wird gleichmäßig auf die zugrunde liegenden physischen Partitionen verteilt.

### <a name="what-is-the-throughput-of-a-table-thats-created-through-cql"></a>Wie hoch ist der Durchsatz einer über CQL erstellten Tabelle?

Azure Cosmos DB verwendet als Einheit für den Durchsatz Anforderungseinheiten pro Sekunde (Request Units per Second, RU/s). Für über CQL erstellte Tabellen lautet der Standardwert 400 RUs. Sie können die Anforderungseinheiten im Azure-Portal ändern.

CQL

```shell
CREATE TABLE keyspaceName.tablename (user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=1200
```

.NET

```csharp
int provisionedThroughput = 400;
var simpleStatement = new SimpleStatement($"CREATE TABLE {keyspaceName}.{tableName} (user_id int PRIMARY KEY, lastname text)");
var outgoingPayload = new Dictionary<string, byte[]>();
outgoingPayload["cosmosdb_provisioned_throughput"] = Encoding.UTF8.GetBytes(provisionedThroughput.ToString());
simpleStatement.SetOutgoingPayload(outgoingPayload);
```

### <a name="what-happens-when-throughput-is-used-up"></a>Was geschieht, wenn der Durchsatz verbraucht wird?

Azure Cosmos DB bietet Garantien für Leistung und Wartezeit (mit Obergrenzen für Vorgänge). Diese Garantien sind möglich, wenn das Modul Governance für die Vorgänge des Mandanten erzwingen kann. Durch Festlegen des Durchsatzes wird sichergestellt, dass Sie den garantierten Durchsatz und die garantierte Wartezeit erhalten. Hierzu wird die entsprechende Kapazität von der Plattform reserviert, um den Erfolg des Vorgangs zu gewährleisten.

Wenn diese Kapazität überschritten wird, erhalten Sie die folgende Fehlermeldung, aus der hervorgeht, dass die Kapazität aufgebraucht wurde:

**0x1001 Überladen: Die Anforderung kann nicht verarbeitet werden – „Anforderungsrate ist groß“.** 

Es ist wichtig, dass Sie überprüfen, welche Vorgänge (mit welchem Umfang) dieses Problem verursachen. Informationen über die verbrauchte Kapazität oberhalb der bereitgestellten Kapazität bieten die Metriken im Azure-Portal. Sie müssen dann sicherstellen, dass die Kapazität von allen zugrunde liegenden Partitionen nahezu gleichmäßig verbraucht wird. Wenn Sie feststellen, dass der größte Teil des Durchsatzes von einer Partition verbraucht wird, führt dies zu einer ungleichmäßigen Arbeitsauslastung.

Es sind Metriken verfügbar, die Ihnen zeigen, wie der Durchsatz von einzelnen Partitionen oder in aggregierter Form über Stunden, über Tage und pro Woche (sieben Tage) verwendet wird. Weitere Informationen finden Sie unter [Überwachen und Debuggen mit Metriken in Azure Cosmos DB](use-metrics.md).

Diagnoseprotokolle werden im Artikel [Diagnoseprotokollierung für Azure Cosmos DB](./monitor-cosmos-db.md) erläutert.

### <a name="does-the-primary-key-map-to-the-partition-key-concept-of-azure-cosmos-db"></a>Entspricht die Primärschlüsselzuordnung dem Konzept von Partitionsschlüsseln von Azure Cosmos DB?

Ja. Der Partitionsschlüssel wird verwendet, um die Entität am richtigen Speicherort zu platzieren. In Azure Cosmos DB wird er zum Suchen der richtigen logischen Partition auf einer physischen Partition verwendet. Das Konzept der Partitionierung wird im Artikel [Partitionieren und Skalieren in Azure Cosmos DB](partitioning-overview.md) sehr gut erläutert. Die wesentliche Lektion ist hierbei, dass eine logische Partition die 20-GB-Grenze nicht überschreiten sollte.

### <a name="what-happens-when-i-get-a-notification-that-a-partition-is-full"></a>Was passiert, wenn ich eine Benachrichtigung darüber erhalte, dass eine Partition voll ist?

Azure Cosmos DB ist ein auf einer Vereinbarung zum Servicelevel (Service-Level Agreement, SLA) basierendes System, das unbegrenzte Skalierbarkeit mit Garantien für Wartezeit, Durchsatz, Verfügbarkeit und Konsistenz bietet. Dieser unbegrenzte Speicher basiert auf einer horizontalen Skalierung der Daten mit der Partitionierung als grundlegendem Konzept. Das Konzept der Partitionierung wird im Artikel [Partitionieren und Skalieren in Azure Cosmos DB](partitioning-overview.md) sehr gut erläutert.

Sie sollten das Limit von 20 GB für die Anzahl von Entitäten oder Elementen pro logischer Partition einhalten. Um sicherzustellen, dass Ihre Anwendung gut skaliert werden kann, sollten Sie *keine* Hot Partition erstellen, indem Sie alle Informationen in einer Partition speichern und abfragen. Dieser Fehler kann nur bei einer Datenscherung auftreten, also wenn Sie über viele Daten für einen Partitionsschlüssel (mehr als 20 GB) verfügen. Sie können die Verteilung der Daten mithilfe des Storage-Portals ermitteln. Um diesen Fehler zu beheben, müssen Sie die Tabelle neu erstellen und einen präzisen Primärschlüssel (Partitionsschlüssel) auswählen, der eine bessere Verteilung der Daten ermöglicht.

### <a name="can-i-use-the-cassandra-api-as-a-key-value-store-with-millions-or-billions-of-partition-keys"></a>Kann ich die Cassandra-API als Schlüsselwertspeicher mit Millionen oder Milliarden von Partitionsschlüsseln verwenden?

Azure Cosmos DB kann eine beliebige Datenmenge speichern, indem der Speicher horizontal hochskaliert wird. Diese Speicherung ist unabhängig vom Durchsatz. Ja. Grundsätzlich können Sie die Cassandra-API nur zum Speichern und Abrufen von Schlüsseln und Werten verwenden, indem Sie den richtigen Primär-/Partitionsschlüssel angeben. Diese einzelnen Schlüssel erhalten jeweils ihre eigene logische Partition und befinden sich auf einer physischen Partition.

### <a name="can-i-create-more-than-one-table-with-the-cassandra-api"></a>Kann ich mit der Cassandra-API mehr als eine Tabelle erstellen?

Ja, mit der Cassandra-API können mehrere Tabellen erstellt werden. Jede dieser Tabellen wird bei Durchsatz und Speicherung als Einheit behandelt.

### <a name="can-i-create-more-than-one-table-in-succession"></a>Kann ich mehrere Tabellen nacheinander erstellen?

Azure Cosmos DB ist ein ressourcengesteuertes System für Aktivitäten auf Daten- und Steuerungsebene. Container (wie z. B. Sammlungen und Tabellen) sind Laufzeitentitäten, die für eine bestimmte Durchsatzkapazität bereitgestellt werden. Die Erstellung dieser Container in schneller Folge ist eine unerwartete Aktivität und wird möglicherweise gedrosselt. Wenn Sie Tests durchführen, bei denen Tabellen sofort abgelegt oder erstellt werden, sollten Sie diese Vorgänge voneinander trennen.

### <a name="what-is-the-maximum-number-of-tables-that-i-can-create"></a>Wie viele Tabellen kann ich maximal erstellen?

Es besteht keine physische Beschränkung für die Anzahl von Tabellen. Wenn Sie sehr viele Tabellen (mit einer konstanten Gesamtgröße von mehr als 10 TB an Daten) erstellen müssen, also nicht nur die übliche Anzahl von Dutzenden oder Hunderten, senden Sie eine E-Mail an [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="what-is-the-maximum-number-of-keyspaces-that-i-can-create"></a>Wie viele Keyspaces kann ich maximal erstellen?

Es besteht keine physische Beschränkung für die Anzahl von Keyspaces, weil es sich dabei um Metadatencontainer handelt. Wenn Sie über eine große Anzahl von Keyspaces verfügen, senden Sie eine E-Mail an [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="can-i-bring-in-a-lot-of-data-after-starting-from-a-normal-table"></a>Kann ich mit einer normalen Tabelle beginnen und später viele Daten hinzufügen?

Ja. Unter der Voraussetzung, dass die Partitionen gleichmäßig verteilt sind, wird die Speicherkapazität automatisch verwaltet und erhöht, wenn Sie weitere Daten hinzufügen. Daher können Sie problemlos beliebig viele Daten importieren, ohne Knoten verwalten und bereitstellen zu müssen. Wenn Sie jedoch ein hohes Maß an sofortigem Datenwachstum erwarten, ist es sinnvoller, [den erwarteten Durchsatz direkt bereitzustellen](set-throughput.md), statt mit einer kleineren Menge zu beginnen und diese sofort zu erhöhen.

### <a name="can-i-use-yaml-file-settings-to-configure-api-behavior"></a>Kann ich das API-Verhalten mithilfe von YAML-Dateieinstellungen konfigurieren?

Die Cassandra-API für Azure Cosmos DB bietet zum Ausführen von Vorgängen Kompatibilität auf Protokollebene. Er vereinfacht die Verwaltung, Überwachung und Konfiguration. Als Entwickler/Benutzer müssen Sie sich keine Gedanken über Verfügbarkeit, Tombstones, Schlüsselcache, Zeilencache, Bloomfilter und zahlreiche andere Einstellungen machen. Bei der Cassandra-API steht die Bereitstellung der benötigten Lese- und Schreibleistung ohne Mehraufwand durch Konfiguration und Verwaltung im Mittelpunkt.

### <a name="will-the-cassandra-api-support-node-addition-cluster-status-and-node-status-commands"></a>Unterstützt die Cassandra-API Befehle für das Hinzufügen von Knoten sowie für den Clusterstatus und den Knotenstatus?

Die Cassandra-API vereinfacht die Kapazitätsplanung und die Reaktion auf Elastizitätsanforderungen für Durchsatz und Speicher. Mit Azure Cosmos DB stellen Sie den benötigten Durchsatz bereit. Sie können diesen dann beliebig oft zentral hoch- und herunterskalieren, ohne sich Gedanken über das Hinzufügen, Löschen oder Verwalten von Knoten machen zu müssen. Sie müssen keine Tools für die Knoten- und Clusterverwaltung verwenden.

### <a name="what-happens-with-various-configuration-settings-for-keyspace-creation-like-simplenetwork"></a>Wie sieht es mit verschiedenen Konfigurationseinstellungen (einfach/Netzwerk) für die Keyspaceerstellung aus?

Azure Cosmos DB bietet aus Gründen der Verfügbarkeit und der geringen Wartezeit bereits eine vorkonfigurierte globale Verteilung. Sie müssen keine Replikate oder andere Elemente einrichten. Schreibvorgänge werden in jeder Region, in der Sie Daten schreiben, immer dauerhaft im Quorum committet, wobei Leistungsgarantien geboten werden.

### <a name="what-happens-with-various-settings-for-table-metadata"></a>Wie sieht es mit verschiedenen Einstellungen für Tabellenmetadaten aus?

Azure Cosmos DB bietet Leistungsgarantien für Lese- und Schreibvorgänge sowie für den Durchsatz. Sie müssen sich also keine Sorgen machen, dass Sie die Konfigurationseinstellungen bearbeiten und versehentlich ändern. Diese Einstellungen umfassen den Bloomfilter, die Zwischenspeicherung, Lesereparaturänderungen, „gc_grace“ und „compression memtable_flush_period“.

### <a name="is-time-to-live-supported-for-cassandra-tables"></a>Wird für Cassandra-Tabellen die Gültigkeitsdauer (Time To Live, TTL) unterstützt?

Ja. TTL wird unterstützt.

### <a name="how-can-i-monitor-infrastructure-along-with-throughput"></a>Wie kann ich die Infrastruktur zusammen mit dem Durchsatz überwachen?

Azure Cosmos DB ist ein Plattformdienst, mit dem Sie Ihre Produktivität steigern können, ohne sich Gedanken über die Verwaltung und Überwachung der Infrastruktur zu machen. Sie müssen beispielsweise nicht erst den Knotenstatus, Replikatstatus, GC und Betriebssystemparameter mit verschiedenen Tools überwachen. Sie müssen nur auf den Durchsatz achten, der in den Metriken im Portal angegeben ist, um zu erkennen, ob Ihr Durchsatz gedrosselt wird, und dann können Sie diesen Durchsatz erhöhen oder verringern. Ihre Möglichkeiten:

- Überwachen von [SLAs](./monitor-cosmos-db.md)
- Verwenden von [Metriken](use-metrics.md)
- Verwenden von [Diagnoseprotokollen](./monitor-cosmos-db.md)

### <a name="which-client-sdks-can-work-with-the-cassandra-api"></a>Welche Client-SDKs können mit der Cassandra-API verwendet werden?

Für Clientprogramme wurden die Clienttreiber des Apache Cassandra SDK verwendet, die CQLv3 verwenden. Wenn Sie andere Treiber verwenden oder bei Ihnen Probleme auftreten, senden Sie eine E-Mail an [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com).

### <a name="are-composite-partition-keys-supported"></a>Werden zusammengesetzte Partitionsschlüssel unterstützt?

Ja. Sie können unter Verwendung der regulären Syntax zusammengesetzte Partitionsschlüssel erstellen.

### <a name="can-i-use-sstableloader-for-data-loading"></a>Kann ich Daten mithilfe von sstableloader laden?

Nein, sstableloader wird nicht unterstützt.

### <a name="can-i-pair-an-on-premises-apache-cassandra-cluster-with-the-cassandra-api"></a>Kann ich einen lokalen Apache Cassandra-Cluster mit der Cassandra-API koppeln?

Derzeit ist Azure Cosmos DB für Cloudumgebungen optimiert und kann ohne zusätzlichen Aufwand betrieben werden. Wenn Sie Kopplung benötigen, senden Sie eine E-Mail mit einer Beschreibung Ihres Szenarios an [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com). Wir arbeiten an einem Angebot, um die Kopplung des lokalen oder cloudbasierten Cassandra-Clusters mit der Cassandra-API für Azure Cosmos DB zu unterstützen.

### <a name="does-the-cassandra-api-provide-full-backups"></a>Bietet die Cassandra-API vollständige Sicherungen?

Azure Cosmos DB bietet zwei kostenlose vollständige Sicherungen, die in Vier-Stunden-Intervallen für alle APIs erstellt werden. Sie müssen also keinen Sicherungszeitplan einrichten. 

Wenn Sie die Aufbewahrungsdauer und die Häufigkeit ändern möchten, senden Sie eine E-Mail an [askcosmosdbcassandra@microsoft.com](mailto:askcosmosdbcassandra@microsoft.com), oder erstellen Sie eine Supportanfrage. Informationen zu Sicherungsfunktion finden Sie im Artikel [Automatische Onlinesicherung und -wiederherstellung mit Azure Cosmos DB](online-backup-and-restore.md).

### <a name="how-does-the-cassandra-api-account-handle-failover-if-a-region-goes-down"></a>Wie läuft mit dem Cassandra-API-Konto das Failover bei einem Ausfall einer Region ab?

Die Cassandra-API nutzt Teile der global verteilten Plattform von Azure Cosmos DB. Aktivieren Sie im Azure-Portal mindestens eine weitere Region für das Konto, um sicherzustellen, dass Ihre Anwendung Ausfallzeiten von Rechenzentren tolerieren kann. Weitere Informationen finden Sie unter [Hochverfügbarkeit mit Azure Cosmos DB](high-availability.md).

Sie können für das Konto beliebig viele Regionen hinzufügen und steuern, wohin das Failover erfolgen kann, indem Sie eine Failoverpriorität festlegen. Für die Verwendung der Datenbank müssen Sie dafür eine Anwendung bereitstellen. Wenn Sie so vorgehen, treten für Ihre Kunden keine Ausfallzeiten auf.

### <a name="does-the-cassandra-api-index-all-attributes-of-an-entity-by-default"></a>Indiziert die Cassandra-API standardmäßig alle Attribute einer Entität?

Nein. Die Cassandra-API unterstützt [sekundäre Indizes](cassandra-secondary-index.md). Dieses Verhalten ähnelt dem von Apache Cassandra. Die API indiziert nicht standardmäßig jedes Attribut.  


### <a name="can-i-use-the-new-cassandra-api-sdk-locally-with-the-emulator"></a>Kann ich das neue SDK der Cassandra-API lokal mit dem Emulator verwenden?

Ja, diese Möglichkeit wird unterstützt. Ausführliche Informationen zum Aktivieren dieser Option finden Sie im Artikel [Verwenden des Azure Cosmos DB-Emulators für lokale Entwicklungs- und Testvorgänge](local-emulator.md#cassandra-api).


### <a name="how-can-i-migrate-data-from-apache-cassandra-clusters-to-azure-cosmos-db"></a>Wie kann ich Daten aus Apache Cassandra-Clustern zu Azure Cosmos DB migrieren?

Informationen zu den Migrationsoptionen finden Sie im Tutorial [Migrieren Ihrer Daten zu einem Cassandra-API-Konto in Azure Cosmos DB](cassandra-import-data.md).


### <a name="where-can-i-give-feedback-on-cassandra-api-features"></a>Wo kann ich Feedback zu Funktionen der Cassandra-API geben?

Übermitteln Sie Ihr Feedback über [Uservoice-Feedback](https://feedback.azure.com/forums/263030-azure-cosmos-db).

[azure-portal]: https://portal.azure.com
[query]: ./sql-query-getting-started.md

## <a name="next-steps"></a>Nächste Schritte

- Beginnen Sie mit dem [elastischen Skalieren eines Cassandra-API-Kontos für Azure Cosmos DB](manage-scale-cassandra.md).
