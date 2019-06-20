---
title: Arbeiten mit Datenbanken, Containern und Elementen in Azure Cosmos DB
description: In diesem Artikel wird die Erstellung und Verwendung von Datenbanken, Containern und Elementen in Azure Cosmos DB beschrieben.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 574dd9fd6189b6d0f1e5d455146d6d083ad7ff77
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389465"
---
# <a name="work-with-databases-containers-and-items-in-azure-cosmos-db"></a>Arbeiten mit Datenbanken, Containern und Elementen in Azure Cosmos DB

Nach der Erstellung eines [Azure Cosmos DB-Kontos](account-overview.md) unter Ihrem Azure-Abonnement können Sie Daten in Ihrem Konto verwalten, indem Sie Datenbanken, Container und Elemente erstellen. In diesem Artikel werden alle diese Entitäten beschrieben. 

In der folgenden Abbildung ist die Hierarchie der verschiedenen Entitäten in einem Azure Cosmos DB-Konto dargestellt:

![Entitäten in einem Azure Cosmos-Konto](./media/databases-containers-items/cosmos-entities.png)

## <a name="azure-cosmos-databases"></a>Azure Cosmos-Datenbanken

Sie können unter Ihrem Konto eine oder mehrere Azure Cosmos-Datenbanken erstellen. Eine Datenbank ist analog zu einem Namespace. Sie ist eine Verwaltungseinheit für eine Gruppe von Azure Cosmos-Containern. In der folgenden Tabelle ist dargestellt, wie eine Azure Cosmos-Datenbank verschiedenen API-spezifischen Entitäten zugeordnet wird:

| Azure Cosmos-Entität | SQL-API | Cassandra-API | Azure Cosmos DB-API für MongoDB | Gremlin-API | Tabelle-API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-Datenbank | Datenbank | Keyspace | Datenbank | Datenbank | Nicht verfügbar |

> [!NOTE]
> Wenn Sie in Tabellen-API-Konten die erste Tabelle erstellen, wird in Ihrem Azure Cosmos-Konto automatisch eine Standarddatenbank erstellt.

### <a name="operations-on-an-azure-cosmos-database"></a>Vorgänge in einer Azure Cosmos-Datenbank

Mit einer Azure Cosmos-Datenbank können Sie über die Azure Cosmos-APIs in der folgenden Tabelle interagieren:

| Vorgang | Azure-Befehlszeilenschnittstelle | SQL-API | Cassandra-API | Azure Cosmos DB-API für MongoDB | Gremlin-API | Tabelle-API |
| --- | --- | --- | --- | --- | --- | --- |
|Auflisten aller Datenbanken| Ja | Ja | Ja (Datenbank ist einem Keyspace zugeordnet) | Ja | Nicht verfügbar | Nicht verfügbar |
|Lesen der Datenbank| Ja | Ja | Ja (Datenbank ist einem Keyspace zugeordnet) | Ja | Nicht verfügbar | Nicht verfügbar |
|Neue Datenbank erstellen| Ja | Ja | Ja (Datenbank ist einem Keyspace zugeordnet) | Ja | Nicht verfügbar | Nicht verfügbar |
|Aktualisieren der Datenbank| Ja | Ja | Ja (Datenbank ist einem Keyspace zugeordnet) | Ja | Nicht verfügbar | Nicht verfügbar |


## <a name="azure-cosmos-containers"></a>Azure Cosmos-Container

Ein Azure Cosmos-Container ist die Skalierungseinheit für den bereitgestellten Durchsatz und den Speicher. Ein Container wird horizontal partitioniert und dann in mehreren Regionen repliziert. Die Elemente, die Sie dem Container hinzuzufügen, und der Durchsatz, den Sie für den Container bereitstellen, werden basierend auf dem Partitionsschlüssel automatisch auf eine Gruppe von logischen Partitionen verteilt. Weitere Informationen zur Partitionierung und zu Partitionsschlüsseln finden Sie unter [Partitionsdaten](partition-data.md). 

Beim Erstellen eines Azure Cosmos-Containers konfigurieren Sie den Durchsatz in einem der folgenden Modi:

* **Dedizierter bereitgestellter Durchsatz:** Der für einen Container bereitgestellte Durchsatz ist ausschließlich für diesen Container reserviert und wird durch die SLAs zugesichert. Weitere Informationen finden Sie unter [Bereitstellen von Durchsatz für einen Azure Cosmos-Container](how-to-provision-container-throughput.md).

* **Bereitgestellter, gemeinsam genutzter Durchsatz:** Die Container teilen sich den bereitgestellten Durchsatz mit anderen Containern in der gleichen Datenbank (mit Ausnahme der Container, die mit dediziert bereitgestelltem Durchsatz konfiguriert wurden). Das heißt, der bereitgestellte Durchsatz für die Datenbank wird von allen Containern mit freigegebenem Durchsatz genutzt. Weitere Informationen finden Sie unter [Bereitstellen von Durchsatz für eine Azure Cosmos-Datenbank](how-to-provision-database-throughput.md).

> [!NOTE]
> Sie können freigegebenen und dedizierten Durchsatz nur beim Erstellen der Datenbank und des Containers konfigurieren. Wenn Sie nach dem Erstellen eines Containers vom Modus des dedizierten Durchsatzes zum Modus des freigegebenen Durchsatzes (und umgekehrt) wechseln möchten, müssen Sie einen neuen Container erstellen und die Daten zu diesem neuen Container migrieren. Sie können die Daten mit dem Azure Cosmos DB-Feature für Änderungsfeeds migrieren.

Ein Azure Cosmos-Container kann elastisch skaliert werden, und zwar unabhängig davon, ob Sie Container mit dediziertem oder gemeinsam genutztem bereitgestelltem Durchsatz erstellen.

Ein Azure Cosmos-Container ist ein schemaunabhängiger Container für Elemente. Elemente innerhalb eines Containers können beliebige Schemas aufweisen. Beispielsweise können ein Element, das eine Person darstellt, und ein Element, das ein Fahrzeug darstellt, im *selben Container* platziert werden. Standardmäßig werden alle Elemente, die Sie einem Container hinzufügen, automatisch indiziert, ohne dass eine explizite Index- oder Schemaverwaltung erforderlich ist. Sie können das Indizierungsverhalten anpassen, indem Sie die [Indizierungsrichtlinie](index-overview.md) für einen Container konfigurieren. 

Sie können die [Gültigkeitsdauer (TTL)](time-to-live.md) für ausgewählte Elemente in einem Azure Cosmos-Container oder für den gesamten Container festlegen, um die Elemente ordnungsgemäß aus dem System zu löschen. Die Elemente werden nach Ablauf der Gültigkeitsdauer in Azure Cosmos DB automatisch gelöscht. Zudem wird sichergestellt, dass eine für den Container ausgeführte Abfrage die abgelaufenen Elemente innerhalb einer festen Grenze nicht zurückgibt. Weitere Informationen finden Sie unter [Konfigurieren der Gültigkeitsdauer in Ihrem Container](how-to-time-to-live.md).

Über den [Änderungsfeed](change-feed.md) können Sie das Vorgangsprotokoll abonnieren, das für jede logische Partition des Containers verwaltet wird. Der Änderungsfeed enthält das Protokoll aller Aktualisierungen, die für den Container durchgeführt wurden, sowie die Vorher- und Nachher-Images der Elemente. Weitere Informationen finden Sie unter [Erstellen reaktiver Anwendungen mithilfe des Änderungsfeeds](serverless-computing-database.md). Sie können auch die Vermerkdauer für den Änderungsfeed über die Änderungsfeedrichtlinie für den Container konfigurieren. 

Sie können [gespeicherte Prozeduren, Trigger, benutzerdefinierte Funktionen (UDFs)](stored-procedures-triggers-udfs.md) und [Mergeprozeduren](how-to-manage-conflicts.md) beim Azure Cosmos-Container registrieren. 

Sie können eine [eindeutige Schlüsseleinschränkung](unique-keys.md) für den Azure Cosmos-Container angeben. Durch Erstellen einer Richtlinie für eindeutige Schlüssel wird die Eindeutigkeit von einem oder mehreren Werten pro logischem Partitionsschlüssel sichergestellt. Wenn Sie einen Container mit einer Richtlinie für eindeutige Schlüssel erstellen, sind keine neuen oder aktualisierten Elemente mit Werten möglich, die die durch die Einschränkung für eindeutige Schlüssel festgelegten Werte duplizieren. Weitere Informationen finden Sie unter [Einschränkungen für eindeutige Schlüssel](unique-keys.md).

Ein Azure Cosmos-Container wird wie in der folgenden Tabelle gezeigt in API-spezifische Entitäten kategorisiert:

| Azure Cosmos-Entität | SQL-API | Cassandra-API | Azure Cosmos DB-API für MongoDB | Gremlin-API | Tabelle-API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos-Container | Sammlung | Table | Sammlung | Graph | Table |

### <a name="properties-of-an-azure-cosmos-container"></a>Eigenschaften eines Azure Cosmos-Containers

Ein Azure Cosmos-Container enthält einen Satz von systemdefinierten Eigenschaften. Abhängig von der verwendeten API können einige Eigenschaften möglicherweise nicht direkt verfügbar gemacht werden. In der folgenden Tabelle werden die systemdefinierten Eigenschaften beschrieben:

| Systemseitig definierte Eigenschaft | Vom System generiert oder vom Benutzer konfigurierbar | Zweck | SQL-API | Cassandra-API | Azure Cosmos DB-API für MongoDB | Gremlin-API | Tabelle-API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_id | Vom System generiert | Eindeutiger Bezeichner des Containers | Ja | Nein | Nein | Nein | Nein |
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

| Vorgang | Azure-Befehlszeilenschnittstelle | SQL-API | Cassandra-API | Azure Cosmos DB-API für MongoDB | Gremlin-API | Tabelle-API |
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
|Azure Cosmos-Element | Dokument | Zeile | Dokument | Knoten oder Edge | Item |

### <a name="properties-of-an-item"></a>Eigenschaften eines Elements

Jedes Azure Cosmos-Element verfügt über die folgenden systemdefinierten Eigenschaften. Abhängig von der verwendeten API können einige davon möglicherweise nicht direkt verfügbar gemacht werden.

| Systemseitig definierte Eigenschaft | Vom System generiert oder vom Benutzer konfigurierbar| Zweck | SQL-API | Cassandra-API | Azure Cosmos DB-API für MongoDB | Gremlin-API | Tabelle-API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_id | Vom System generiert | Eindeutiger Bezeichner des Elements | Ja | Nein | Nein | Nein | Nein |
|\_etag | Vom System generiert | Entitätstag, das zur Steuerung der optimistischen Nebenläufigkeit genutzt wird | Ja | Nein | Nein | Nein | Nein |
|\_ts | Vom System generiert | Zeitstempel der letzten Aktualisierung des Elements | Ja | Nein | Nein | Nein | Nein |
|\_self | Vom System generiert | Adressierbarer URI des Elements | Ja | Nein | Nein | Nein | Nein |
|id | Sie können das | Benutzerdefinierter eindeutiger Name innerhalb einer logischen Partition. Wenn der Benutzer die ID nicht angibt, wird vom System automatisch eine generiert. | Ja | Ja | Ja | Ja | Ja |
|Beliebige benutzerdefinierte Eigenschaften | Benutzerdefiniert | Benutzerdefinierte Eigenschaften, die in einer API-nativen Darstellung (einschließlich JSON, BSON und CQL) dargestellt werden | Ja | Ja | Ja | Ja | Ja |

### <a name="operations-on-items"></a>Vorgänge in Entitäten

Azure Cosmos-Elemente unterstützen die folgenden Vorgänge. Sie können für die Vorgänge beliebige der Azure-Cosmos-APIs verwenden.

| Vorgang | Azure-Befehlszeilenschnittstelle | SQL-API | Cassandra-API | Azure Cosmos DB-API für MongoDB | Gremlin-API | Tabelle-API |
| --- | --- | --- | --- | --- | --- | --- |
| Einfügen, Ersetzen, Löschen, Upsert, Lesen | Nein | Ja | Ja | Ja | Ja | Ja |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu diesen Aufgaben und Konzepten:

* [Bereitstellen von Durchsatz für eine Azure Cosmos-Datenbank](how-to-provision-database-throughput.md)
* [Bereitstellen von Durchsatz für einen Azure Cosmos-Container](how-to-provision-container-throughput.md)
* [Arbeiten mit logischen Partitionen](partition-data.md)
* [Konfigurieren der Gültigkeitsdauer in Azure Cosmos-Containern](how-to-time-to-live.md)
* [Erstellen von reaktiven Anwendungen mit dem Änderungsfeed](change-feed.md)
* [Konfigurieren einer Einschränkung für eindeutige Schlüssel in Azure Cosmos-Containern](unique-keys.md)
