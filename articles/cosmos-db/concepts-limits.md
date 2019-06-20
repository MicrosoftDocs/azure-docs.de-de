---
title: Grenzwerte in Azure Cosmos DB
description: Dieser Artikel beschreibt die Grenzwerte in Azure Cosmos DB.
author: arramac
ms.author: arramac
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/19/2019
ms.openlocfilehash: 0086327661df637dc0ae60208ed9424b4610ef0e
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969455"
---
# <a name="limits-in-azure-cosmos-db"></a>Grenzwerte in Azure Cosmos DB

Dieser Artikel bietet eine Übersicht über die Grenzwerte im Azure Cosmos DB-Dienst.

## <a name="storage-and-throughput"></a>Speicher und Durchsatz

Nach der Erstellung eines Azure Cosmos-Kontos unter Ihrem Abonnement können Sie Daten in Ihrem Konto verwalten, indem Sie [Datenbanken, Container und Elemente](databases-containers-items.md) erstellen. Sie können Durchsatz auf einer Container- oder Datenbankebene in Form von [Anforderungseinheiten (Request Units, RU/s oder RUs)](request-units.md) bereitstellen. Die folgende Tabelle enthält die Grenzwerte für Speicher und Durchsatz pro Container/Datenbank.

| Resource | Standardlimit |
| --- | --- |
| Maximale Anzahl RUs pro Container ([Bereitstellungsmodus für dedizierten Durchsatz](databases-containers-items.md#azure-cosmos-containers)) | Standardmäßig 1.000.000. Sie können sie erhöhen, indem Sie [ein Azure-Supportticket senden](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) oder uns über [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) kontaktieren. |
| Maximale Anzahl RUs pro Datenbank ([Bereitstellungsmodus für gemeinsam genutzten Durchsatz](databases-containers-items.md#azure-cosmos-containers)) | Standardmäßig 1.000.000. Sie können sie erhöhen, indem Sie [ein Azure-Supportticket senden](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) oder uns über [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) kontaktieren. |
| Maximale Anzahl RUs pro (logischem) Partitionsschlüssel | 10.000 |
| Maximale Speicherkapazität aller Elemente pro (logischem) Partitionsschlüssel| 10 GB |
| Maximale Anzahl unterschiedlicher (logischer) Partitionsschlüssel | Unbegrenzt |
| Maximale Speicherkapazität pro Container | Unbegrenzt |
| Maximale Speicherkapazität pro Datenbank | Unbegrenzt |

> [!NOTE]
> Bewährte Methoden für die Verwaltung von Workloads mit Partitionsschlüsseln, die höhere Grenzwerte für Speicher oder Durchsatz benötigen, finden Sie unter [Erstellen eines synthetischen Partitionsschlüssels](synthetic-partition-keys.md).
>

Ein Cosmos-Container (oder eine Datenbank mit gemeinsam genutztem Durchsatz) muss einen Mindestdurchsatz von 400 RUs aufweisen. Mit zunehmendem Wachstum des Containers hängt der unterstützte Mindestdurchsatz auch von folgenden Faktoren ab:

* Die maximale Größe des im Container verbrauchten Speichers wird in Inkrementen von 40 RUs pro GB an verbrauchtem Speicher gemessen. Wenn ein Container z.B. 100GB Daten enthält, muss der Durchsatz mindestens 4.000 RUs betragen
* Der maximale Durchsatz, der jemals im Container bereitgestellt wurde. Der Dienst unterstützt das Senken des Durchsatzes eines Containers auf 10% des bereitgestellten Maximums. Wenn z.B. der Durchsatz auf 10.000 RUs erhöht wurde, würde der niedrigstmögliche bereitgestellte Durchsatz 1.000 RUs betragen
* Die Gesamtzahl der Container, die Sie jemals in einer Datenbank mit gemeinsam genutztem Durchsatz erstellt haben, gemessen in 100 RUs pro Container. Wenn Sie z.B. fünf Container in einer Datenbank mit gemeinsam genutztem Durchsatz erstellt haben, muss der Durchsatz mindestens 500 RUs betragen

Der aktuelle und minimale Durchsatz eines Containers oder einer Datenbank kann über das Azure-Portal oder die SDKs abgerufen werden. Weitere Informationen finden Sie unter [Bereitstellen des Durchsatzes für Container und Datenbanken](set-throughput.md). Hier sehen Sie eine Zusammenfassung der RU-Mindestgrenzwerte. 

| Resource | Standardlimit |
| --- | --- |
| Minimale Anzahl RUs pro Container ([Bereitstellungsmodus für dedizierten Durchsatz](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Minimale Anzahl RUs pro Datenbank ([Bereitstellungsmodus für gemeinsam genutzten Durchsatz](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| Minimale Anzahl RUs pro Container in einer Datenbank mit gemeinsam genutztem Durchsatz | 100 |
| Minimale Anzahl RUs pro GB an verbrauchten Speicher | 40 |

Cosmos DB unterstützt flexible Skalierung von Durchsatz (RUs) pro Container bzw. Datenbank über die SDKs oder das Portal. Jeder Container kann synchron und sofort innerhalb eines Skalierungsbereichs von 10 bis 100 Mal zwischen Minimal- und Maximalwert skaliert werden. Wenn der angeforderte Durchsatzwert außerhalb des Bereichs liegt, wird die Skalierung asynchron durchgeführt. Die asynchrone Skalierung kann je nach angefordertem Durchsatz und Datenspeichergröße im Container Minuten bis Stunden dauern.  

## <a name="control-plane-operations"></a>Vorgänge auf der Steuerungsebene

Sie können mithilfe von Azure-Portal, Azure PowerShell, der Azure-Befehlszeilenschnittstelle und Azure Resource Manager-Vorlagen Ihr [Azure Cosmos-Konto bereitstellen und verwalten](how-to-manage-database-account.md). Die folgende Tabelle enthält die Grenzwerte pro Abonnement, Konto und Anzahl der Vorgänge.

| Resource | Standardlimit |
| --- | --- |
| Maximale Anzahl von Datenbankkonten pro Abonnement | Standardmäßig 50. Sie können sie erhöhen, indem Sie [ein Azure-Supportticket senden](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) oder uns über [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) kontaktieren.|
| Maximale Anzahl von regionalen Failovern | Standardmäßig 1/Stunde. Sie können sie erhöhen, indem Sie [ein Azure-Supportticket senden](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) oder uns über [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) kontaktieren.|

> [!NOTE]
> Regionale Failover gelten nur für Konten für Schreibvorgänge in einer einzelnen Region. Konten für Schreibvorgänge in mehreren Regionen erfordern nicht das Ändern der Schreibregion oder haben keine diesbezüglichen Einschränkungen.

Cosmos DB erstellt in regelmäßigen Abständen automatisch Sicherungen Ihrer Daten. Weitere Informationen zu Intervallen der Sicherungsaufbewahrung und Windows finden Sie unter [Onlinesicherung und bedarfsgesteuerte Wiederherstellung in Azure Cosmos DB](online-backup-and-restore.md).

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

<sup>*</sup> Sie können jeden dieser Grenzwerte pro Container erhöhen, indem Sie sich an den Azure-Support wenden oder über [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) an uns.

## <a name="per-item-limits"></a>Grenzwerte pro Element

Abhängig von der API, die Sie verwenden, kann ein Azure Cosmos-Element ein Dokument in einer Sammlung, eine Zeile in einer Tabelle oder einen Knoten oder Edge in einem Graph darstellen. Die folgende Tabelle zeigt die Grenzwerte pro Element in Cosmos DB. 

| Resource | Standardlimit |
| --- | --- |
| Maximale Größe eines Elements | 2MB (UTF-8-Länge von JSON-Darstellung) |
| Maximale Länge des Partitionsschlüsselwerts | 2.048 Bytes |
| Maximale Länge des ID-Werts | 1.024 Bytes |
| Maximale Anzahl der Eigenschaften pro Element | Praktisch unbegrenzt |
| Maximale Schachtelungstiefe | Praktisch unbegrenzt |
| Maximale Länge des Eigenschaftsnamens | Praktisch unbegrenzt |
| Maximale Länge des Eigenschaftswerts | Praktisch unbegrenzt |
| Maximale Länge des Zeichenfolgen-Eigenschaftswerts | Praktisch unbegrenzt |
| Maximale Länge des numerischen Eigenschaftswerts | IEEE754 mit doppelter Genauigkeit 64-Bit |

Es gibt keine Einschränkungen für die Elementnutzlasten wie z.B. Anzahl von Eigenschaften und Schachtelungstiefe, mit Ausnahme von Längeneinschränkungen bei Partitionsschlüssel- und ID-Werten und der Einschränkung der gesamten Größe auf 2MB. Sie müssen möglicherweise eine Indizierungsrichtlinie für Container mit großen oder komplexen Elementstrukturen konfigurieren, um den RU-Verbrauch zu reduzieren. Unter [Modellieren und Partitionieren von Daten in Azure Cosmos DB anhand eines praktischen Beispiels](how-to-model-partition-example.md) finden Sie ein praktisches Beispiel und Muster zum Verwalten großer Elemente.

## <a name="per-request-limits"></a>Grenzwerte pro Anforderung

Cosmos DB unterstützt [CRUD- und Abfragevorgänge](https://docs.microsoft.com/rest/api/cosmos-db/) für Ressourcen wie Datenbanken, Elemente und Container.  

| Resource | Standardlimit |
| --- | --- |
| Maximale Ausführungszeit für einen einzelnen Vorgang (z.B. die Ausführung einer gespeicherten Prozedur oder Abrufen einer einzelnen Abfrageseite)| 5 Sekunden |
| Maximale Größe der Anforderung (gespeicherte Prozedur, CRUD)| 2 MB |
| Maximale Größe der Antwort (z.B. paginierte Abfrage) | 4 MB |

Sobald das Ausführungstimeout oder der Antwortgrößen-Grenzwert für einen Vorgang wie eine Abfrage erreicht ist, wird an den Client eine Seite mit Ergebnissen und ein Fortsetzungstoken zum Fortsetzen der Ausführung zurückgegeben. Es gibt praktisch keine Begrenzung der Dauer der Ausführung einer einzelnen Abfrage hinsichtlich Seiten/Fortsetzungen.

Cosmos DB verwendet einen HMAC zur Autorisierung. Sie können entweder einen Hauptschlüssel oder ein [Ressourcentoken](secure-access-to-data.md) für differenzierte Steuerung des Zugriffs auf Ressourcen wie Container, Partitionsschlüssel oder Elemente verwenden. Die folgende Tabelle enthält die Grenzwerte für Autorisierungstoken in Cosmos DB.

| Resource | Standardlimit |
| --- | --- |
| Maximale Ablaufzeit für Mastertoken | 15 Min.  |
| Minimale Ablaufzeit für Ressourcentoken | 10 Min.  |
| Maximale Ablaufzeit für Ressourcentoken | Standardmäßig 24 Stunden. Sie können sie erhöhen, indem Sie [ein Azure-Supportticket senden](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) oder uns über [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) kontaktieren.|
| Maximale Zeitabweichung für Tokenautorisierung| 15 Min. |

Cosmos DB unterstützt die Ausführung von Triggern während Schreibvorgängen. Der Dienst unterstützt maximal einen vorangestellten und einen nachgestellten Trigger pro Schreibvorgang. 

## <a name="sql-query-limits"></a>Grenzwerte für SQL-Abfragen

Cosmos DB unterstützt das Abfragen von Elementen mithilfe von [SQL](how-to-sql-query.md). In der folgenden Tabelle werden die Einschränkungen in Abfrageanweisungen beschrieben, z.B. in Bezug auf die Anzahl der Klauseln oder die Länge der Abfrage.

| Resource | Standardlimit |
| --- | --- |
| Maximale Länge der SQL-Abfrage| 256KB <sup>*</sup>|
| Maximale Anzahl der JOINs pro Abfrage| 5 <sup>*</sup>|
| Maximale Anzahl der ANDs pro Abfrage| 2.000 <sup>*</sup>|
| Maximale Anzahl der ORs pro Abfrage| 2.000 <sup>*</sup>|
| Maximale Anzahl der UDFs pro Abfrage| 10 <sup>*</sup>|
| Maximale Anzahl der Argumente pro IN-Ausdrucks| 6.000 <sup>*</sup>|
| Maximale Anzahl der Punkte pro Polygon| 4.096 <sup>*</sup>|

<sup>*</sup> Sie können jeden dieser Grenzwerte für SQL-Abfragen erhöhen, indem Sie sich an den Azure-Support wenden oder über [askcosmosdb@microsoft.com](mailto:askcosmosdb@microsoft.com) an uns.

## <a name="mongodb-api-specific-limits"></a>MongoDB-API-spezifische Grenzwerte

Cosmos DB unterstützt MongoDB Wire Protocol für Anwendungen, die für MongoDB geschrieben wurden. Sie finden die unterstützten Befehle und Protokollversionen unter [Azure Cosmos DB-API für MongoDB: unterstützte Features und Syntax](mongodb-feature-support.md).

Die folgende Tabelle enthält die spezifischen Grenzwerte für die Unterstützung von MongoDB-Features. Andere bereits für die SQL-API bzw. SQL-Core-API erwähnte Diensteinschränkungen gelten auch für die MongoDB-API.

| Resource | Standardlimit |
| --- | --- |
| Maximale Speichergröße für MongoDB-Abfragen | 40MB |
| Maximale Ausführungszeit für MongoDB-Vorgänge| 30 Sek. |

## <a name="try-cosmos-db-free-limits"></a>Grenzwerte für „Azure Cosmos DB kostenlos testen“

Die folgende Tabelle enthält die Grenzwerte für die [Azure Cosmos DB kostenlos testen](https://azure.microsoft.com/try/cosmosdb/)-Testversion.

| Resource | Standardlimit |
| --- | --- |
| Dauer der Testversion | 30 Tage (kann beliebig oft erneuert werden) |
| Maximale Anzahl Container pro Abonnement (SQL, Gremlin, Tabellen-API) | 1 |
| Maximale Anzahl Container pro Abonnement (MongoDB-API) | 3 |
| Maximaler Durchsatz pro Container | 5.000 |
| Maximaler Durchsatz pro Datenbank mit gemeinsam genutztem Durchsatz | 20000 |
| Max. Gesamtspeicher pro Konto | 10 GB |

„Azure Cosmos DB kostenlos testen“ unterstützt die globale Verteilung nur in den Regionen „USA, Mitte“, „Europa, Norden“ und „Asien, Südosten“. Für „Azure Cosmos DB kostenlos testen“-Konten können keine Azure-Supporttickets erstellt werden. Abonnenten mit bestehenden Supportplänen wird allerdings Support gewährt.

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
