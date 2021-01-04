---
title: Kontingente im Azure Cosmos DB-Dienst
description: Kontingente und Standardbeschränkungen für unterschiedliche Ressourcen im Azure Cosmos DB-Dienst.
author: abhijitpai
ms.author: abpai
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/19/2020
ms.openlocfilehash: 9d351bc945c08c82e96791059b00dc94eb94b918
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/13/2020
ms.locfileid: "97368737"
---
# <a name="azure-cosmos-db-service-quotas"></a>Kontingente im Azure Cosmos DB-Dienst

[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Dieser Artikel enthält eine Übersicht über die Standardkontingente für unterschiedliche Ressourcen in Azure Cosmos DB.

## <a name="storage-and-database-operations"></a>Speicher und Datenbankvorgänge

Nach der Erstellung eines Azure Cosmos-Kontos unter Ihrem Abonnement können Sie Daten in Ihrem Konto verwalten, indem Sie [Datenbanken, Container und Elemente](account-databases-containers-items.md) erstellen.

### <a name="provisioned-throughput"></a>Bereitgestellter Durchsatz

Sie können Durchsatz auf einer Container- oder Datenbankebene in Form von [Anforderungseinheiten (Request Units, RU/s oder RUs)](request-units.md) bereitstellen. Die folgende Tabelle enthält die Grenzwerte für Speicher und Durchsatz pro Container/Datenbank.

| Resource | Standardlimit |
| --- | --- |
| Maximale Anzahl RUs pro Container ([Bereitstellungsmodus für dedizierten Durchsatz](account-databases-containers-items.md#azure-cosmos-containers)) | Standardmäßig 1.000.000. Sie können die Anzahl erhöhen, indem Sie [ein Azure-Supportticket senden](create-support-request-quota-increase.md). |
| Maximale Anzahl RUs pro Datenbank ([Bereitstellungsmodus für gemeinsam genutzten Durchsatz](account-databases-containers-items.md#azure-cosmos-containers)) | Standardmäßig 1.000.000. Sie können die Anzahl erhöhen, indem Sie [ein Azure-Supportticket senden](create-support-request-quota-increase.md). |
| Maximale Anzahl RUs pro (logischer) Partition | 10.000 |
| Maximale Speicherkapazität aller Elemente pro (logischer) Partition | 20 GB |
| Maximale Anzahl unterschiedlicher (logischer) Partitionsschlüssel | Unbegrenzt |
| Maximale Speicherkapazität pro Container | Unbegrenzt |
| Maximale Speicherkapazität pro Datenbank | Unbegrenzt |
| Maximale Anlagengröße pro Konto (Anlagenfeature wird eingestellt) | 2 GB |
| Mindestens erforderliche RU/s pro 1 GB | 10 RU/s<br>**Hinweis:** Wenn Ihr Container oder Ihre Datenbank mehr als ein TB an Daten enthält, ist Ihr Konto möglicherweise für das Programm [High Storage/Low Throughput](set-throughput.md#high-storage-low-throughput-program) (Hohe Speicherkapazität/geringer Durchsatz) qualifiziert. |

> [!NOTE]
> Informationen zu bewährten Methoden für die Verwaltung von Workloads, bei denen Partitionsschlüssel höhere Grenzwerte für Speicher oder Durchsatz erfordern, finden Sie unter [Erstellen eines synthetischen Partitionsschlüssels](synthetic-partition-keys.md).

### <a name="minimum-throughput-limits"></a>Mindestdurchsatzwerte

Ein Cosmos-Container (oder eine Datenbank mit gemeinsam genutztem Durchsatz) muss einen Mindestdurchsatz von 400 RU/s haben. Mit zunehmendem Wachstum des Containers erfordert Cosmos DB einen Mindestdurchsatz, um sicherzustellen, dass die Datenbank oder der Container über genügend Ressourcen für die jeweiligen Vorgänge verfügt.

Der aktuelle und minimale Durchsatz eines Containers oder einer Datenbank kann über das Azure-Portal oder die SDKs abgerufen werden. Weitere Informationen finden Sie unter [Bereitstellen des Durchsatzes für Container und Datenbanken](set-throughput.md). 

Der tatsächliche Mindestwert an RU/s kann je nach Kontokonfiguration variieren. Sie können [Azure Monitor-Metriken](monitor-cosmos-db.md#view-operation-level-metrics-for-azure-cosmos-db) verwenden, um den Verlauf des bereitgestellten Durchsatzes (RU/s) und des Speichers für eine Ressource anzuzeigen. 

#### <a name="minimum-throughput-on-container"></a>Mindestdurchsatz für Container 

Um den Mindestdurchsatz zu schätzen, der für einen Container mit manuellem Durchsatz erforderlich ist, ermitteln Sie die folgenden Maximalwerte:

* 400 RU/s 
* Aktueller Speicher in GB * 10 RU/s
* Höchstwert bereitgestellter RU/s für den Container / 100

Beispiel: Angenommen, Sie verfügen über einen Container, für den 400 RU/s und 0 GB Speicher bereitgestellt werden. Sie erhöhen den Durchsatz auf 50.000 RU/s und importieren 20 GB an Daten. Die minimale Anzahl von RU/s beträgt jetzt `MAX(400, 20 * 10 RU/s per GB, 50,000 RU/s / 100)` = 500 RU/s. Im Laufe der Zeit wächst der Speicher auf 200 GB an. Die minimale Anzahl von RU/s beträgt jetzt `MAX(400, 200 * 10 RU/s per GB, 50,000 / 100)` = 2000 RU/s. 

**Hinweis:** Wenn Ihr Container oder Ihre Datenbank mehr als 1 TB an Daten enthält, ist Ihr Konto möglicherweise für das Programm [High Storage/Low Throughput](set-throughput.md#high-storage-low-throughput-program) (Hohe Speicherkapazität/geringer Durchsatz) qualifiziert.

#### <a name="minimum-throughput-on-shared-throughput-database"></a>Mindestdurchsatz für eine Datenbank mit gemeinsam genutztem Durchsatz 
Um den Mindestdurchsatz zu schätzen, der für eine Datenbank mit gemeinsam genutztem Durchsatz erforderlich ist, ermitteln Sie die folgenden Maximalwerte:

* 400 RU/s 
* Aktueller Speicher in GB * 10 RU/s
* Höchstwert bereitgestellter RU/s für die Datenbank / 100
* 400 + MAX(Containeranzahl - 25, 0) * 100 RU/s

Beispiel: Angenommen, Sie verfügen über eine Datenbank, für die 400 RU/s, 15 GB Speicher und 10 Container bereitgestellt werden. Die minimale Anzahl von RU/s beträgt `MAX(400, 15 * 10 RU/s per GB, 400 / 100, 400 + 0 )` = 400 RU/s. Wenn in der Datenbank 30 Container vorhanden sind, beträgt die minimale Anzahl von RU/s `400 + MAX(30 - 25, 0) * 100 RU/s` = 900 RU/s. 

**Hinweis:** Wenn Ihr Container oder Ihre Datenbank mehr als 1 TB an Daten enthält, ist Ihr Konto möglicherweise für das Programm [High Storage/Low Throughput](set-throughput.md#high-storage-low-throughput-program) (Hohe Speicherkapazität/geringer Durchsatz) qualifiziert.

Hier sehen Sie eine Zusammenfassung der RU-Mindestgrenzwerte. 

| Resource | Standardlimit |
| --- | --- |
| Minimale Anzahl RU/s pro Container ([Bereitstellungsmodus für dedizierten Durchsatz](./account-databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Minimale Anzahl RU/s pro Datenbank ([Bereitstellungsmodus für gemeinsam genutzten Durchsatz](./account-databases-containers-items.md#azure-cosmos-containers)) | 400 RU/s für die ersten 25 Container. Zusätzliche 100 RU/s für jeden weiteren Container. |

Cosmos DB unterstützt die programmgesteuerte Skalierung von Durchsatz (RU/s) pro Container bzw. Datenbank über die SDKs oder das Portal.    

Je nach aktuell bereitgestellten RU/s und Ressourceneinstellungen kann jede Ressource synchron und sofort zwischen der minimalen Anzahl von RU/s und dem hundertfachen der minimalen Anzahl von RU/s skaliert werden. Wenn der angeforderte Durchsatzwert außerhalb des Bereichs liegt, wird die Skalierung asynchron durchgeführt. Die asynchrone Skalierung kann je nach angefordertem Durchsatz und Datenspeichergröße im Container Minuten bis Stunden dauern.  

### <a name="serverless"></a>Serverlos

Mit der Option [Serverlos](serverless.md) können Sie Ihre Azure Cosmos DB-Ressourcen nutzungsbasiert verwenden. Die folgende Tabelle enthält die Grenzwerte für Speicher und Durchsatzburstfähigkeit pro Container/Datenbank.

| Resource | Begrenzung |
| --- | --- |
| Maximale Anzahl RU/s pro (logischer) Partition | 5\.000 |
| Maximale Speicherkapazität aller Elemente pro (logischer) Partition | 20 GB |
| Maximale Anzahl unterschiedlicher (logischer) Partitionsschlüssel | Unbegrenzt |
| Maximale Speicherkapazität pro Container | 50 GB |

## <a name="control-plane-operations"></a>Vorgänge auf der Steuerungsebene

Sie können mithilfe von Azure-Portal, Azure PowerShell, der Azure-Befehlszeilenschnittstelle und Azure Resource Manager-Vorlagen Ihr [Azure Cosmos-Konto bereitstellen und verwalten](how-to-manage-database-account.md). Die folgende Tabelle enthält die Grenzwerte pro Abonnement, Konto und Anzahl der Vorgänge.

| Resource | Standardlimit |
| --- | --- |
| Maximale Anzahl von Datenbankkonten pro Abonnement | Standardmäßig 50. Sie können die Anzahl erhöhen, indem Sie [ein Azure-Supportticket senden](create-support-request-quota-increase.md).|
| Maximale Anzahl von regionalen Failovern | Standardmäßig 1/Stunde. Sie können die Anzahl erhöhen, indem Sie [ein Azure-Supportticket senden](create-support-request-quota-increase.md).|

> [!NOTE]
> Regionale Failover gelten nur für Konten für Schreibvorgänge in einer einzelnen Region. Konten für Schreibvorgänge in mehreren Regionen erfordern nicht das Ändern der Schreibregion oder haben keine diesbezüglichen Einschränkungen.

Cosmos DB erstellt in regelmäßigen Abständen automatisch Sicherungen Ihrer Daten. Weitere Informationen zu Intervallen der Sicherungsaufbewahrung und Windows finden Sie unter [Onlinesicherung und bedarfsgesteuerte Wiederherstellung in Azure Cosmos DB](online-backup-and-restore.md).

## <a name="per-account-limits"></a>Grenzwerte pro Konto

### <a name="provisioned-throughput"></a>Bereitgestellter Durchsatz

| Resource | Standardlimit |
| --- | --- |
| Maximale Anzahl von Datenbanken | Unbegrenzt |
| Maximale Anzahl von Containern pro Datenbank mit freigegebenem Durchsatz |25 |
| Maximale Anzahl von Containern pro Datenbank oder Konto mit dediziertem Durchsatz  |Unbegrenzt |
| Maximale Anzahl von Regionen | Keine Einschränkung (alle Azure-Regionen) |

### <a name="serverless"></a>Serverlos

| Resource | Begrenzung |
| --- | --- |
| Maximale Anzahl von Datenbanken | Unbegrenzt |
| Maximale Anzahl von Containern pro Konto  | 100 |
| Maximale Anzahl von Regionen | 1 (beliebige Azure-Region) |

## <a name="per-container-limits"></a>Grenzwerte pro Container

Abhängig von der API, die Sie verwenden, kann ein Azure Cosmos-Container entweder eine Sammlung, eine Tabelle oder einen Graph darstellen. Container unterstützen Konfigurationen für [UNIQUE KEY-Einschränkungen](unique-keys.md), [gespeicherte Prozeduren, Trigger und UDFs](stored-procedures-triggers-udfs.md) sowie [Indizierungsrichtlinien](how-to-manage-indexing-policy.md). Die folgende Tabelle enthält die spezifischen Grenzwerte für Konfigurationen innerhalb eines Containers. 

| Resource | Standardlimit |
| --- | --- |
| Maximale Länge von Datenbank- oder Containername | 255 |
| Maximale Anzahl gespeicherter Prozeduren pro Container | 100 <sup>*</sup>|
| Maximale Anzahl UDFs pro Container | 25 <sup>*</sup>|
| Maximale Anzahl von Pfaden in Indizierungsrichtlinie| 100 <sup>*</sup>|
| Maximale Anzahl von eindeutigen Schlüsseln pro Container|10 <sup>*</sup>|
| Maximale Anzahl von Pfaden pro UNIQUE KEY-Einschränkung|16 <sup>*</sup>|
| Maximaler TTL-Wert |2147483647|

<sup>*</sup> Sie können jeden dieser Grenzwerte pro Container erhöhen, indem Sie eine [Azure-Supportanfrage](create-support-request-quota-increase.md) erstellen.

## <a name="per-item-limits"></a>Grenzwerte pro Element

Abhängig von der API, die Sie verwenden, kann ein Azure Cosmos-Element ein Dokument in einer Sammlung, eine Zeile in einer Tabelle oder einen Knoten oder Edge in einem Graph darstellen. Die folgende Tabelle zeigt die Grenzwerte pro Element in Cosmos DB. 

| Resource | Standardlimit |
| --- | --- |
| Maximale Größe eines Elements | 2MB (UTF-8-Länge von JSON-Darstellung) |
| Maximale Länge des Partitionsschlüsselwerts | 2\.048 Bytes |
| Maximale Länge des ID-Werts | 1\.023 Bytes |
| Maximale Anzahl der Eigenschaften pro Element | Praktisch unbegrenzt |
| Maximale Länge des Eigenschaftsnamens | Praktisch unbegrenzt |
| Maximale Länge des Eigenschaftswerts | Praktisch unbegrenzt |
| Maximale Länge des Zeichenfolgen-Eigenschaftswerts | Praktisch unbegrenzt |
| Maximale Länge des numerischen Eigenschaftswerts | IEEE754 mit doppelter Genauigkeit 64-Bit |
| Maximale Schachtelungsebene für eingebettete Objekte/Arrays | 128 |
| Maximaler TTL-Wert |2147483647|

Mit Ausnahme von Längeneinschränkungen bei Partitionsschlüssel- und ID-Werten sowie der Einschränkung der Gesamtgröße auf 2 MB gibt es bei den Elementnutzlasten keine Einschränkungen (also etwa für die Anzahl von Eigenschaften und die Schachtelungstiefe). Sie müssen möglicherweise eine Indizierungsrichtlinie für Container mit großen oder komplexen Elementstrukturen konfigurieren, um den RU-Verbrauch zu reduzieren. Unter [Modellieren und Partitionieren von Daten in Azure Cosmos DB anhand eines praktischen Beispiels](how-to-model-partition-example.md) finden Sie ein praktisches Beispiel und Muster zum Verwalten großer Elemente.

## <a name="per-request-limits"></a>Grenzwerte pro Anforderung

Azure Cosmos DB unterstützt [CRUD- und Abfragevorgänge](/rest/api/cosmos-db/) für Ressourcen wie Container, Elemente und Datenbanken. Außerdem werden [transaktionale Batchanforderungen](/dotnet/api/microsoft.azure.cosmos.transactionalbatch) für mehrere Elemente mit demselben Partitionsschlüssel in einem Container unterstützt.

| Resource | Standardlimit |
| --- | --- |
| Maximale Ausführungszeit für einen einzelnen Vorgang (z.B. die Ausführung einer gespeicherten Prozedur oder Abrufen einer einzelnen Abfrageseite)| 5 Sekunden |
| Maximale Größe der Anforderung (z.B. gespeicherte Prozedur, CRUD)| 2 MB |
| Maximale Größe der Antwort (z.B. paginierte Abfrage) | 4 MB |
| Maximale Anzahl von Vorgängen in einem transaktionalen Batch | 100 |

Sobald das Ausführungstimeout oder der Antwortgrößen-Grenzwert für einen Vorgang wie eine Abfrage erreicht ist, wird an den Client eine Seite mit Ergebnissen und ein Fortsetzungstoken zum Fortsetzen der Ausführung zurückgegeben. Es gibt praktisch keine Begrenzung der Dauer der Ausführung einer einzelnen Abfrage hinsichtlich Seiten/Fortsetzungen.

Cosmos DB verwendet einen HMAC zur Autorisierung. Sie können entweder einen Primärschlüssel oder ein [Ressourcentoken](secure-access-to-data.md) für differenzierte Steuerung des Zugriffs auf Ressourcen wie Container, Partitionsschlüssel oder Elemente verwenden. Die folgende Tabelle enthält die Grenzwerte für Autorisierungstoken in Cosmos DB.

| Resource | Standardlimit |
| --- | --- |
| Maximale Ablaufzeit für primäre Token | 15 Min.  |
| Minimale Ablaufzeit für Ressourcentoken | 10 Min.  |
| Maximale Ablaufzeit für Ressourcentoken | Standardmäßig 24 Stunden. Sie können die Anzahl erhöhen, indem Sie [ein Azure-Supportticket senden](create-support-request-quota-increase.md).|
| Maximale Zeitabweichung für Tokenautorisierung| 15 Min. |

Cosmos DB unterstützt die Ausführung von Triggern während Schreibvorgängen. Der Dienst unterstützt maximal einen vorangestellten und einen nachgestellten Trigger pro Schreibvorgang.

## <a name="metadata-request-limits"></a>Anforderungsgrenzwerte für Metadaten

Azure Cosmos DB verwaltet Systemmetadaten für jedes Konto. Diese Metadaten ermöglichen es Ihnen, Sammlungen, Datenbanken, andere Azure Cosmos DB-Ressourcen und deren Konfiguration kostenlos aufzuzählen.

| Resource | Standardlimit |
| --- | --- |
|Maximale Sammlungserstellungsrate pro Minute|    100|
|Maximale Datenbankerstellungsrate pro Minute|    100|
|Maximal Aktualisierungsrate des bereitgestellten Durchsatzes pro Minute|    5|

## <a name="limits-for-autoscale-provisioned-throughput"></a>Grenzwerte für den per Autoskalierung bereitgestellten Durchsatz

Die Durchsatz- und Speichergrenzwerte im Zusammenhang mit der Autoskalierung werden im [Artikel zur Autoskalierung](provision-throughput-autoscale.md#autoscale-limits) sowie in den [häufig gestellten Fragen](autoscale-faq.md#lowering-the-max-rus) ausführlicher erläutert.

| Resource | Standardlimit |
| --- | --- |
| Maximale Anzahl von RU/s, auf die das System skaliert werden kann |  `Tmax`, die vom Benutzer festgelegte maximale Anzahl von RU/s für die Autoskalierung|
| Minimale Anzahl von RU/s, auf die das System skaliert werden kann | `0.1 * Tmax`|
| Aktuelle Anzahl von RU/s, auf die das System skaliert ist  |  `0.1*Tmax <= T <= Tmax`, basierend auf der Nutzung|
| Minimale Anzahl abrechenbarer RU/s pro Stunde| `0.1 * Tmax` <br></br>Die Abrechnung erfolgt auf Stundenbasis. Ihnen wird entweder die höchste Anzahl von RU/s, auf die das System in der Stunde skaliert wurde, oder `0.1*Tmax` berechnet (je nachdem, welcher Wert höher ist). |
| Mindestwert für die maximale Anzahl von RU/s für die Autoskalierung für einen Container  |  `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100)`, gerundet auf volle 1.000 RU/s |
| Mindestwert für die maximale Anzahl von RU/s für die Autoskalierung für eine Datenbank  |  `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100,  4000 + (MAX(Container count - 25, 0) * 1000))`, gerundet auf volle 1000 RU/s <br></br>Hinweis: Wenn Ihre Datenbank mehr als 25 Container umfasst, erhöht sich der Mindestwert für die maximale Anzahl von RU/s für die Autoskalierung pro zusätzlichem Container um jeweils 1.000 RU/s. Wenn Sie also beispielsweise über 30 Container verfügen, müssen für die maximale Anzahl von RU/s für die Autoskalierung mindestens 9.000 RU/s (Skalierung zwischen 900 und 9.000 RU/s) festgelegt werden.

## <a name="sql-query-limits"></a>Grenzwerte für SQL-Abfragen

Cosmos DB unterstützt das Abfragen von Elementen mithilfe von [SQL](./sql-query-getting-started.md). In der folgenden Tabelle werden die Einschränkungen in Abfrageanweisungen beschrieben, z.B. in Bezug auf die Anzahl der Klauseln oder die Länge der Abfrage.

| Resource | Standardlimit |
| --- | --- |
| Maximale Länge der SQL-Abfrage| 256 KB |
| Maximale Anzahl der JOINs pro Abfrage| 5 <sup>*</sup>|
| Maximale Anzahl der UDFs pro Abfrage| 10 <sup>*</sup>|
| Maximale Anzahl der Punkte pro Polygon| 4096 |
| Maximale Anzahl von eingeschlossenen Pfaden pro Container| 500 |
| Maximale Anzahl von ausgeschlossenen Pfaden pro Container| 500 |
| Maximale Anzahl von Eigenschaften in einem zusammengesetzten Index| 8 |

<sup>*</sup> Sie können jeden dieser Grenzwerte für SQL-Abfragen erhöhen, indem Sie eine [Azure-Supportanfrage](create-support-request-quota-increase.md) erstellen.

## <a name="mongodb-api-specific-limits"></a>MongoDB-API-spezifische Grenzwerte

Cosmos DB unterstützt MongoDB Wire Protocol für Anwendungen, die für MongoDB geschrieben wurden. Sie finden die unterstützten Befehle und Protokollversionen unter [Azure Cosmos DB-API für MongoDB: unterstützte Features und Syntax](mongodb-feature-support.md).

Die folgende Tabelle enthält die spezifischen Grenzwerte für die Unterstützung von MongoDB-Features. Andere bereits für die SQL-API bzw. SQL-Core-API erwähnte Diensteinschränkungen gelten auch für die MongoDB-API.

| Resource | Standardlimit |
| --- | --- |
| Maximale Speichergröße für MongoDB-Abfragen (diese Einschränkung gilt ausschließlich für die Serverversion 3.2) | 40MB |
| Maximale Ausführungszeit für MongoDB-Vorgänge| 30 Sek. |
| Zeitlimit für Leerlaufverbindung für serverseitige Verbindungsschließung* | 30 Minuten |

\* Wir empfehlen, dass Clientanwendungen das Zeitlimit für die Leerlaufverbindung in den Treibereinstellungen auf 2-3 Minuten festlegen, da das [Standardzeitlimit für Azure Load Balancer 4 Minuten beträgt](../load-balancer/load-balancer-tcp-idle-timeout.md).  Dieses Zeitlimit stellt sicher, dass Leerlaufverbindungen nicht durch einen zwischengeschalteten Lastenausgleich zwischen dem Clientcomputer und Azure Cosmos DB geschlossen werden.

## <a name="try-cosmos-db-free-limits"></a>Grenzwerte für „Azure Cosmos DB kostenlos testen“

Die folgende Tabelle enthält die Grenzwerte für die [Azure Cosmos DB kostenlos testen](https://azure.microsoft.com/try/cosmosdb/)-Testversion.

| Resource | Standardlimit |
| --- | --- |
| Dauer der Testversion | 30 Tage (danach kann eine neue Testversion angefordert werden) <br> Nach dem Ablauf werden die gespeicherten Informationen gelöscht. |
| Maximale Anzahl Container pro Abonnement (SQL, Gremlin, Tabellen-API) | 1 |
| Maximale Anzahl Container pro Abonnement (MongoDB-API) | 3 |
| Maximaler Durchsatz pro Container | 5.000 |
| Maximaler Durchsatz pro Datenbank mit gemeinsam genutztem Durchsatz | 20000 |
| Max. Gesamtspeicher pro Konto | 10 GB |

„Azure Cosmos DB kostenlos testen“ unterstützt die globale Verteilung nur in den Regionen „USA, Mitte“, „Europa, Norden“ und „Asien, Südosten“. Für „Azure Cosmos DB kostenlos testen“-Konten können keine Azure-Supporttickets erstellt werden. Abonnenten mit bestehenden Supportplänen wird allerdings Support gewährt.

## <a name="free-tier-account-limits"></a>Kontolimits für den Free-Tarif
Die folgende Tabelle enthält die Limits für [Free-Tarif-Konten in Azure Cosmos DB](optimize-dev-test.md#azure-cosmos-db-free-tier).

| Resource | Standardlimit |
| --- | --- |
| Anzahl der Free-Tarif-Konten pro Azure-Abonnement | 1 |
| Dauer des Free-Tarif-Rabatts | Gültigkeitsdauer des Kontos. Aktivierung ist während der Kontoerstellung erforderlich. |
| Maximale Anzahl kostenloser RU/s | 400 RU/s |
| Maximaler kostenloser Speicherplatz | 5 GB |
| Maximale Anzahl von Datenbanken mit gemeinsam genutztem Durchsatz | 5 |
| Maximale Anzahl von Containern in einer Datenbank mit gemeinsam genutztem Durchsatz | 25 <br>Bei Konten mit Free-Tarif hat eine Datenbank mit gemeinsam genutztem Durchsatz mit bis zu 25 Containern mindestens 400 RU/s. |

  Zusätzlich zu den oben genannten Einschränkungen gelten die [Grenzwerte pro Konto](#per-account-limits) auch für Free-Tarif-Konten.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die grundlegenden Konzepte von Cosmos DB: [globale Verteilung](distribute-data-globally.md), [Partitionierung](partitioning-overview.md) und [bereitgestellter Durchsatz](request-units.md).

Erste Schritte mit Azure Cosmos DB mit einem unserer Schnellstarts:

* [Erste Schritte mit der SQL-API von Azure Cosmos DB](create-sql-api-dotnet.md)
* [Erste Schritte mit der Azure Cosmos DB-API für MongoDB](create-mongodb-nodejs.md)
* [Erste Schritte mit der Cassandra-API von Azure Cosmos DB](create-cassandra-dotnet.md)
* [Erste Schritte mit der Gremlin-API von Azure Cosmos DB](create-graph-dotnet.md)
* [Erste Schritte mit der Tabellen-API von Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Azure Cosmos DB kostenlos testen](https://azure.microsoft.com/try/cosmosdb/)
