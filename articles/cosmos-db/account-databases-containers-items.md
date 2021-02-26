---
title: Azure Cosmos DB-Ressourcenmodell
description: In diesem Artikel wird das Azure Cosmos DB-Ressourcenmodell beschrieben, das das Azure Cosmos-Konto, die Datenbank, den Container und die Elemente umfasst. Außerdem wird die Hierarchie dieser Elemente in einem Azure Cosmos-Konto behandelt.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.reviewer: sngun
ms.openlocfilehash: 4ed881b74f240946d98d9868344c898d3e9a9dad
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627330"
---
# <a name="azure-cosmos-db-resource-model"></a>Azure Cosmos DB-Ressourcenmodell
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB ist ein vollständig verwalteter PaaS-Dienst (Platform-as-a-Service, PaaS). Damit Sie Azure Cosmos DB verwenden können, müssen Sie in Ihrem Azure-Abonnement zunächst ein Azure Cosmos-Konto erstellen und darin dann Datenbanken, Container und Elemente. In diesem Artikel werden das Azure Cosmos DB-Ressourcenmodell und verschiedene Entitäten in der Ressourcenmodellhierarchie beschrieben.

Das Azure Cosmos-Konto ist die grundlegende Einheit für globale Verteilung und Hochverfügbarkeit. Ihr Azure Cosmos-Konto enthält einen eindeutigen DNS-Namen, und für die Kontoverwaltung können Sie das Azure-Portal, die Azure-Befehlszeilenschnittstelle oder verschiedene sprachspezifische SDKs verwenden. Weitere Informationen finden Sie unter [Verwalten von Datenbankkonten in Azure Cosmos DB](how-to-manage-database-account.md). Sie können Ihrem Konto jederzeit Azure-Regionen hinzufügen oder Azure-Regionen daraus entfernen, um Ihre Daten und Ihren Durchsatz global auf mehrere Azure-Regionen zu verteilen. Ihr Konto kann mit einer einzelnen Schreibregion oder mit mehreren Schreibregionen konfiguriert werden. Weitere Informationen finden Sie unter [Verwalten eines Azure Cosmos-Kontos](how-to-manage-database-account.md). Sie können die [Standardkonsistenz](consistency-levels.md) eines Kontos konfigurieren.

## <a name="elements-in-an-azure-cosmos-account"></a>Elemente in einem Azure Cosmos-Konto

Ein Azure Cosmos-Container ist die grundlegende Einheit für die Skalierbarkeit. Ein Container kann über nahezu unbegrenzten bereitgestellten Durchsatz (RU/s) und Speicher verfügen. Azure Cosmos DB partitioniert Ihren Container transparent unter Verwendung des logischen Partitionsschlüssels, den Sie angeben, um die elastische Skalierung Ihres bereitgestellten Durchsatzes und Speichers zu ermöglichen.

Derzeit können Sie in einem Azure-Abonnement maximal 50 Azure Cosmos-Konten erstellen (dies ist ein weicher Grenzwert, der über eine Supportanfrage erhöht werden kann). Ein einzelnes Azure Cosmos-Konto kann eine nahezu unbegrenzte Menge an Daten und bereitgestelltem Durchsatz verwalten. Zur Verwaltung Ihrer Daten und Ihres bereitgestellten Durchsatzes können Sie unter Ihrem Konto eine oder mehrere Azure Cosmos-Datenbanken und innerhalb der jeweiligen Datenbank einen oder mehrere Container erstellen. Die folgende Abbildung zeigt die Hierarchie der Elemente in einem Azure Cosmos-Konto:

:::image type="content" source="./media/account-databases-containers-items/hierarchy.png" alt-text="Hierarchie eines Azure Cosmos-Kontos" border="false":::

Nach der Erstellung eines Kontos in Ihrem Azure-Abonnement können Sie die Daten in Ihrem Konto verwalten, indem Sie Datenbanken, Container und Elemente erstellen. 

In der folgenden Abbildung ist die Hierarchie der verschiedenen Entitäten in einem Azure Cosmos DB-Konto dargestellt:

:::image type="content" source="./media/account-databases-containers-items/cosmos-entities.png" alt-text="Entitäten in einem Azure Cosmos-Konto" border="false":::

## <a name="azure-cosmos-databases"></a>Azure Cosmos-Datenbanken

Sie können unter Ihrem Konto eine oder mehrere Azure Cosmos-Datenbanken erstellen. Eine Datenbank ist analog zu einem Namespace. Sie ist eine Verwaltungseinheit für eine Gruppe von Azure Cosmos-Containern. In der folgenden Tabelle ist dargestellt, wie eine Datenbank verschiedenen API-spezifischen Entitäten zugeordnet wird:

| Azure Cosmos-Entität | SQL-API | Cassandra-API | Azure Cosmos DB-API für MongoDB | Gremlin-API | Tabelle-API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-Datenbank | Datenbank | Keyspace | Datenbank | Datenbank | Nicht verfügbar |

> [!NOTE]
> Wenn Sie in Tabellen-API-Konten die erste Tabelle erstellen, wird in Ihrem Azure Cosmos-Konto automatisch eine Standarddatenbank erstellt.

### <a name="operations-on-an-azure-cosmos-database"></a>Vorgänge in einer Azure Cosmos-Datenbank

Mit einer Azure Cosmos-Datenbank können Sie über die Azure Cosmos-APIs in der folgenden Tabelle interagieren:

| Vorgang | Azure CLI | SQL-API | Cassandra-API | Azure Cosmos DB-API für MongoDB | Gremlin-API | Tabelle-API |
| --- | --- | --- | --- | --- | --- | --- |
|Auflisten aller Datenbanken| Ja | Ja | Ja (Datenbank ist einem Keyspace zugeordnet) | Ja | Nicht verfügbar | Nicht verfügbar |
|Lesen der Datenbank| Ja | Ja | Ja (Datenbank ist einem Keyspace zugeordnet) | Ja | Nicht verfügbar | Nicht verfügbar |
|Neue Datenbank erstellen| Ja | Ja | Ja (Datenbank ist einem Keyspace zugeordnet) | Ja | Nicht verfügbar | Nicht verfügbar |
|Aktualisieren der Datenbank| Ja | Ja | Ja (Datenbank ist einem Keyspace zugeordnet) | Ja | Nicht verfügbar | Nicht verfügbar |

## <a name="azure-cosmos-containers"></a>Azure Cosmos-Container

Ein Azure Cosmos-Container ist die Skalierungseinheit für den bereitgestellten Durchsatz und den Speicher. Ein Container wird horizontal partitioniert und dann in mehreren Regionen repliziert. Die Elemente, die Sie dem Container hinzufügen, werden basierend auf dem Partitionsschlüssel automatisch in logische Partitionen gruppiert und auf physische Partitionen verteilt. Der Durchsatz für einen Container wird gleichmäßig auf die physischen Partitionen verteilt. Weitere Informationen zur Partitionierung und zu Partitionsschlüsseln finden Sie unter [Partitionsdaten](partitioning-overview.md). 

Beim Erstellen eines Containers konfigurieren Sie den Durchsatz in einem der folgenden Modi:

* **Dedizierter bereitgestellter Durchsatz:** Der für einen Container bereitgestellte Durchsatz ist ausschließlich für diesen Container reserviert und wird durch die SLAs zugesichert. Weitere Informationen finden Sie unter [Bereitstellen von Standarddurchsatz (manuell) für einen Azure Cosmos-Container](how-to-provision-container-throughput.md).

* **Bereitgestellter, gemeinsam genutzter Durchsatz:** Die Container teilen sich den bereitgestellten Durchsatz mit anderen Containern in der gleichen Datenbank (mit Ausnahme der Container, die mit dediziert bereitgestelltem Durchsatz konfiguriert wurden). Das heißt, der bereitgestellte Durchsatz für die Datenbank wird von allen Containern mit freigegebenem Durchsatz genutzt. Weitere Informationen finden Sie unter [Bereitstellen von Standarddurchsatz (manuell) für eine Datenbank in Azure Cosmos DB](how-to-provision-database-throughput.md).

> [!NOTE]
> Sie können freigegebenen und dedizierten Durchsatz nur beim Erstellen der Datenbank und des Containers konfigurieren. Wenn Sie nach dem Erstellen eines Containers vom Modus des dedizierten Durchsatzes zum Modus des freigegebenen Durchsatzes (und umgekehrt) wechseln möchten, müssen Sie einen neuen Container erstellen und die Daten zu diesem neuen Container migrieren. Sie können die Daten mit dem Azure Cosmos DB-Feature für Änderungsfeeds migrieren.

Ein Azure Cosmos-Container kann elastisch skaliert werden, und zwar unabhängig davon, ob Sie Container mit dediziertem oder gemeinsam genutztem bereitgestelltem Durchsatz erstellen.

Ein Container ist ein schemaunabhängiger Container für Elemente. Elemente innerhalb eines Containers können beliebige Schemas aufweisen. Beispielsweise können ein Element, das eine Person darstellt, und ein Element, das ein Fahrzeug darstellt, im *selben Container* platziert werden. Standardmäßig werden alle Elemente, die Sie einem Container hinzufügen, automatisch indiziert, ohne dass eine explizite Index- oder Schemaverwaltung erforderlich ist. Sie können das Indizierungsverhalten anpassen, indem Sie die [Indizierungsrichtlinie](index-overview.md) für einen Container konfigurieren. 

Sie können die [Gültigkeitsdauer (Time To Live, TTL)](time-to-live.md) für ausgewählte Elemente in einem Container oder für den gesamten Container festlegen, um die Elemente ordnungsgemäß aus dem System zu löschen. Die Elemente werden nach Ablauf der Gültigkeitsdauer in Azure Cosmos DB automatisch gelöscht. Zudem wird sichergestellt, dass eine für den Container ausgeführte Abfrage die abgelaufenen Elemente innerhalb einer festen Grenze nicht zurückgibt. Weitere Informationen finden Sie unter [Konfigurieren der Gültigkeitsdauer in Ihrem Container](how-to-time-to-live.md).

Über den [Änderungsfeed](change-feed.md) können Sie das Vorgangsprotokoll abonnieren, das für jede logische Partition des Containers verwaltet wird. Der Änderungsfeed enthält das Protokoll aller Aktualisierungen, die für den Container durchgeführt wurden, sowie die Vorher- und Nachher-Images der Elemente. Weitere Informationen finden Sie unter [Erstellen reaktiver Anwendungen mithilfe des Änderungsfeeds](serverless-computing-database.md). Sie können auch die Vermerkdauer für den Änderungsfeed über die Änderungsfeedrichtlinie für den Container konfigurieren.

Sie können [gespeicherte Prozeduren, Trigger, benutzerdefinierte Funktionen (User-Defined Function, UDF)](stored-procedures-triggers-udfs.md) und [Zusammenführungsprozeduren](how-to-manage-conflicts.md) für Ihren Container registrieren.

Sie können eine [eindeutige Schlüsseleinschränkung](unique-keys.md) für den Azure Cosmos-Container angeben. Durch Erstellen einer Richtlinie für eindeutige Schlüssel wird die Eindeutigkeit von einem oder mehreren Werten pro logischem Partitionsschlüssel sichergestellt. Wenn Sie einen Container mit einer Richtlinie für eindeutige Schlüssel erstellen, sind keine neuen oder aktualisierten Elemente mit Werten möglich, die die durch die Einschränkung für eindeutige Schlüssel festgelegten Werte duplizieren. Weitere Informationen finden Sie unter [Einschränkungen für eindeutige Schlüssel](unique-keys.md).

Container werden API-spezifischen Entitäten wie in der folgenden Tabelle zu sehen zugeordnet:

| Azure Cosmos-Entität | SQL-API | Cassandra-API | Azure Cosmos DB-API für MongoDB | Gremlin-API | Tabelle-API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-Container | Container | Tabelle | Collection | Graph | Tabelle |

> [!NOTE]
> Stellen Sie beim Erstellen von Containern sicher, dass Sie nicht zwei Container mit demselben Namen, aber unterschiedlicher Groß-/Kleinschreibung erstellen. Der Grund dafür ist, dass bei einigen Teilen der Azure-Plattform die Groß-/Kleinschreibung nicht beachtet wird, und dies kann zu Verwechslungen/Kollisionen von Telemetriedaten und Aktionen für Container mit solchen Namen führen.

### <a name="properties-of-an-azure-cosmos-container"></a>Eigenschaften eines Azure Cosmos-Containers

Ein Azure Cosmos-Container enthält einen Satz von systemdefinierten Eigenschaften. Abhängig von der verwendeten API können einige Eigenschaften möglicherweise nicht direkt verfügbar gemacht werden. In der folgenden Tabelle werden die systemdefinierten Eigenschaften beschrieben:

| Systemseitig definierte Eigenschaft | Vom System generiert oder vom Benutzer konfigurierbar | Zweck | SQL-API | Cassandra-API | Azure Cosmos DB-API für MongoDB | Gremlin-API | Tabelle-API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | Vom System generiert | Eindeutiger Bezeichner des Containers | Ja | Nein | Nein | Nein | Nein |
|\_etag | Vom System generiert | Entitätstag, das zur Steuerung der optimistischen Nebenläufigkeit genutzt wird | Ja | Nein | Nein | Nein | Nein |
|\_ts | Vom System generiert | Zuletzt aktualisierter Zeitstempel des Containers | Ja | Nein | Nein | Nein | Nein |
|\_self | Vom System generiert | Adressierbarer URI des Containers | Ja | Nein | Nein | Nein | Nein |
|id | Vom Benutzer konfigurierbar | Benutzerdefinierter eindeutiger Name des Containers | Ja | Ja | Ja | Ja | Ja |
|indexingPolicy | Vom Benutzer konfigurierbar | Bietet die Möglichkeit, den Indexpfad, den Indextyp und den Indexmodus zu ändern | Ja | Nein | Nein | Nein | Ja |
|timeToLive | Vom Benutzer konfigurierbar | Bietet die Möglichkeit, Elemente nach einem festgelegten Zeitraum automatisch aus einem Container zu löschen. Weitere Informationen finden Sie unter [Gültigkeitsdauer](time-to-live.md). | Ja | Nein | Nein | Nein | Ja |
|changeFeedPolicy | Vom Benutzer konfigurierbar | Wird zum Lesen von Änderungen verwendet, die an Elementen in einem Container vorgenommen wurden. Weitere Informationen finden Sie unter [Änderungsfeed](change-feed.md). | Ja | Nein | Nein | Nein | Ja |
|uniqueKeyPolicy | Vom Benutzer konfigurierbar | Stellt die Eindeutigkeit von Werten innerhalb einer logischen Partition sicher. Weitere Informationen finden Sie unter [Einschränkungen für eindeutige Schlüssel](unique-keys.md). | Ja | Nein | Nein | Nein | Ja |

### <a name="operations-on-an-azure-cosmos-container"></a>Vorgänge in einem Azure Cosmos-Container

In einem Azure Cosmos-Container werden die folgenden Vorgänge über die Azure Cosmos-APIs unterstützt:

| Vorgang | Azure CLI | SQL-API | Cassandra-API | Azure Cosmos DB-API für MongoDB | Gremlin-API | Tabelle-API |
| --- | --- | --- | --- | --- | --- | --- |
| Auflisten von Containern in einer Datenbank | Ja | Ja | Ja | Ja | Nicht verfügbar | Nicht verfügbar |
| Lesen eines Containers | Ja | Ja | Ja | Ja | Nicht verfügbar | Nicht verfügbar |
| Erstellen eines neuen Containers | Ja | Ja | Ja | Ja | Nicht verfügbar | Nicht verfügbar |
| Aktualisieren eines Containers | Ja | Ja | Ja | Ja | Nicht verfügbar | Nicht verfügbar |
| Löschen eines Containers | Ja | Ja | Ja | Ja | Nicht verfügbar | Nicht verfügbar |

## <a name="azure-cosmos-items"></a>Azure Cosmos-Elemente

Abhängig von der API, die Sie verwenden, kann ein Azure Cosmos-Element ein Dokument in einer Sammlung, eine Zeile in einer Tabelle oder einen Knoten oder Edge in einem Graph darstellen. In der folgenden Tabelle ist die Zuordnung von API-spezifischen Entitäten zu einem Azure Cosmos-Element dargestellt:

| Cosmos-Entität | SQL-API | Cassandra-API | Azure Cosmos DB-API für MongoDB | Gremlin-API | Tabelle-API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-Element | Element | Zeile | Dokument | Knoten oder Edge | Element |

### <a name="properties-of-an-item"></a>Eigenschaften eines Elements

Jedes Azure Cosmos-Element verfügt über die folgenden systemdefinierten Eigenschaften. Abhängig von der verwendeten API können einige davon möglicherweise nicht direkt verfügbar gemacht werden.

| Systemseitig definierte Eigenschaft | Vom System generiert oder vom Benutzer konfigurierbar| Zweck | SQL-API | Cassandra-API | Azure Cosmos DB-API für MongoDB | Gremlin-API | Tabelle-API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_rid | Vom System generiert | Eindeutiger Bezeichner des Elements | Ja | Nein | Nein | Nein | Nein |
|\_etag | Vom System generiert | Entitätstag, das zur Steuerung der optimistischen Nebenläufigkeit genutzt wird | Ja | Nein | Nein | Nein | Nein |
|\_ts | Vom System generiert | Zeitstempel der letzten Aktualisierung des Elements | Ja | Nein | Nein | Nein | Nein |
|\_self | Vom System generiert | Adressierbarer URI des Elements | Ja | Nein | Nein | Nein | Nein |
|id | Sowohl als auch | Benutzerdefinierter eindeutiger Name innerhalb einer logischen Partition. | Ja | Ja | Ja | Ja | Ja |
|Beliebige benutzerdefinierte Eigenschaften | Benutzerdefiniert | Benutzerdefinierte Eigenschaften, die in einer API-nativen Darstellung (einschließlich JSON, BSON und CQL) dargestellt werden | Ja | Ja | Ja | Ja | Ja |

> [!NOTE]
> Die Eindeutigkeit der Eigenschaft `id` wird nur innerhalb der einzelnen logischen Partitionen erzwungen. Mehrere Dokumente können dieselbe `id`-Eigenschaft mit unterschiedlichen Partitionsschlüsselwerten aufweisen.

### <a name="operations-on-items"></a>Vorgänge in Entitäten

Azure Cosmos-Elemente unterstützen die folgenden Vorgänge. Sie können für die Vorgänge beliebige der Azure-Cosmos-APIs verwenden.

| Vorgang | Azure CLI | SQL-API | Cassandra-API | Azure Cosmos DB-API für MongoDB | Gremlin-API | Tabelle-API |
| --- | --- | --- | --- | --- | --- | --- |
| Einfügen, Ersetzen, Löschen, Upsert, Lesen | Nein | Ja | Ja | Ja | Ja | Ja |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Verwalten Ihres Azure Cosmos-Kontos und zu anderen Konzepten:

* [Verwalten von Datenbankkonten in Azure Cosmos DB](how-to-manage-database-account.md)
* [Globale Verteilung](distribute-data-globally.md)
* [Konsistenzebenen](consistency-levels.md)
* [Sicherer Zugriff auf ein Azure Cosmos DB-Konto durch Verwenden eines Azure Virtual Network-Dienstendpunkts](how-to-configure-vnet-service-endpoint.md)
* [Azure Cosmos DB-Firewallunterstützung](how-to-configure-firewall.md)
* [Hinzufügen/Entfernen von Regionen für Ihr Datenbankkonto](how-to-manage-database-account.md)
* [SLA für Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
