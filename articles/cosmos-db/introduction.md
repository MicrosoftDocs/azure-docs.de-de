---
title: Einführung in Azure Cosmos DB
description: Hier finden Sie Informationen zu Azure Cosmos DB. Diese global verteilte Datenbank mit mehreren Modellen ist für geringe Wartezeiten, flexible Skalierbarkeit und Hochverfügbarkeit konzipiert und bietet native Unterstützung für NoSQL-Daten.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: overview
ms.date: 10/23/2020
ms.openlocfilehash: 37ba5fc7ce118e2e8532ea4db18634265aaacb64
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99556857"
---
# <a name="welcome-to-azure-cosmos-db"></a>Willkommen bei Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Moderne Anwendungen erfordern eine hohe Reaktionsschnelligkeit und müssen immer online sein. Um eine geringe Latenz und Hochverfügbarkeit zu erreichen, müssen Instanzen dieser Anwendungen in Rechenzentren eingesetzt werden, die sich in der Nähe ihrer Benutzer befinden. Anwendungen müssen in Echtzeit auf große Nutzungsänderungen zu Spitzenzeiten reagieren, immer größere Datenmengen speichern und diese den Benutzern innerhalb von Millisekunden zur Verfügung stellen.

Azure Cosmos DB ist eine vollständig verwaltete NoSQL-Datenbank für die moderne App-Entwicklung. Antwortzeiten im einstelligen Millisekundenbereich sowie automatische und sofortige Skalierbarkeit garantieren Geschwindigkeit in jeder Größenordnung. Geschäftskontinuität wird durch [SLA-gestützte](https://azure.microsoft.com/support/legal/sla/cosmos-db) Verfügbarkeit und Sicherheit auf Unternehmensniveau gewährleistet. Sofort einsatzbereite globale Datenverteilung in mehreren Regionen sowie Open-Source-APIs und -SDKs für gängige Sprachen machen die App-Entwicklung schneller und produktiver. Als vollständig verwalteter Dienst nimmt Ihnen Azure Cosmos DB die Datenbankverwaltung durch automatische Verwaltung, Updates und Patches ab. Außerdem übernimmt der Dienst die Kapazitätsverwaltung mit kostengünstigen serverlosen und automatischen Skalierungsoptionen, die auf Anwendungsanforderungen reagieren, um die Kapazität an den Bedarf anzupassen.

Sie können [Azure Cosmos DB kostenlos ohne Azure-Abonnement und ohne Verpflichtungen testen](https://azure.microsoft.com/try/cosmosdb/) oder ein [Azure Cosmos DB-Konto im Tarif „Free“](optimize-dev-test.md#azure-cosmos-db-free-tier) nutzen, bei dem die ersten 400 RU/s und 5 GB Speicher kostenlos sind.

> [!div class="nextstepaction"]
> [Azure Cosmos DB kostenlos testen](https://azure.microsoft.com/try/cosmosdb/)

:::image type="content" source="./media/introduction/azure-cosmos-db.png" alt-text="Azure Cosmos DB ist eine vollständig verwaltete NoSQL-Datenbank für die moderne App-Entwicklung." border="false":::

## <a name="key-benefits"></a>Hauptvorteile

### <a name="guaranteed-speed-at-any-scale"></a>Garantierte Geschwindigkeit in jeder Größenordnung

Profitieren Sie von einzigartiger [SLA-gestützter](https://azure.microsoft.com/support/legal/sla/cosmos-db) Geschwindigkeit und entsprechendem Durchsatz, schnellem globalem Zugriff und sofortiger Elastizität.

- Echtzeitzugriff mit kurzen Wartezeiten bei Lese- und Schreibvorgängen auf der ganzen Welt sowie Durchsatz und Konsistenz, jeweils auf der Grundlage von [SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db).
- Schreibvorgänge in mehreren Regionen sowie Datenverteilung in jeder beliebigen Azure-Region auf Knopfdruck.
- Unabhängiges und elastisches Skalieren von Speicher und Durchsatz in jeder beliebigen Azure-Region (selbst bei unvorhersehbaren Datenverkehrsspitzen) für unbegrenzte weltweite Skalierbarkeit.

### <a name="simplified-application-development"></a>Vereinfachte Anwendungsentwicklung

Beschleunigen Sie Ihre Entwicklung durch Open-Source-APIs, mehreren SDKs, schemalose Daten und Analysen ohne ETL für operative Daten.

- Umfassende Integration in zentrale Azure-Dienste, die bei der (cloudnativen) App-Entwicklung zum Einsatz kommen. Hierzu zählen unter anderem Azure Functions, IoT Hub, AKS (Azure Kubernetes Service) und App Service.
- Mehrere verfügbare Datenbank-APIs, einschließlich nativer Core-API (SQL), API für MongoDB, Cassandra-API, Gremlin-API und Tabellen-API.
- Erstellen Sie Apps auf der Grundlage der Core-API (SQL) unter Verwendung der Sprachen Ihrer Wahl mit SDKs für .NET, Java, Node.js und Python. Sie haben aber auch die Möglichkeit, die gewünschten Treiber für die anderen Datenbank-APIs zu verwenden.
- Führen Sie mit Azure Synapse Analytics Analysen ohne ETL für die operativen Daten durch, die nahezu in Echtzeit in Azure Cosmos DB gespeichert werden.
- Mithilfe des Änderungsfeeds können Sie ganz einfach Änderungen an Datenbankcontainern nachverfolgen und verwalten sowie ausgelöste Ereignisse mit Azure Functions erstellen.
- Durch den schemalosen Dienst von Azure Cosmos DB werden alle Ihre Daten unabhängig vom Datenmodell automatisch indiziert, um blitzschnelle Abfragen zu ermöglichen.

### <a name="mission-critical-ready"></a>Geeignet für unternehmenskritische Workloads

Gewährleisten Sie Geschäftskontinuität, eine Verfügbarkeit von 99,999 Prozent sowie Sicherheit auf Unternehmensniveau für jede Anwendung.

- Azure Cosmos DB bietet eine umfassende Suite von [SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db) mit branchenführender weltweiter Verfügbarkeit.
- Mit der automatischen Datenreplikation lassen sich Daten ganz einfach auf jede beliebige Azure-Region verteilen. Profitieren Sie von einer Umgebung ohne Ausfallzeit mit Schreibvorgängen in mehreren Regionen oder RPO 0 bei Verwendung starker Konsistenz.
- Nutzen Sie die für den professionellen Einsatz konzipierte Verschlüsselung ruhender Daten mit selbstverwalteten Schlüsseln.
- Die rollenbasierte Zugriffssteuerung von Azure trägt zum Schutz Ihrer Daten bei und bietet differenzierte Steuerungsmöglichkeiten.

### <a name="fully-managed-and-cost-effective"></a>Vollständig verwaltet und kostengünstig

End-to-End-Datenbankverwaltung mit serverloser und automatischer Skalierung, abgestimmt auf Ihre Anforderungen in puncto Anwendung und Gesamtkosten

- Vollständig verwalteter Datenbankdienst. Automatisierung von Wartung, Patching und Updates, um die Entwicklung zu beschleunigen und die Kosten zu senken.
- Kostengünstige Optionen für unvorhersehbare oder sporadische Workloads mit beliebiger Größe oder beliebigem Umfang, sodass Entwickler ganz einfach und ohne Kapazitätsplanung oder -verwaltung loslegen können.
- Serverloses Modell mit automatischem, schnell reagierendem Dienst für Workloads mit Lastspitzen zur bedarfsgerechten Datenverkehrsverwaltung.
- Per Autoskalierung bereitgestellter Durchsatz zur automatischen und sofortigen Skalierung der Kapazität für unvorhersehbare Workloads bei gleichzeitiger Erfüllung von [SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db).

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Lösungen, die von Azure Cosmos DB profitieren

Von der [garantierten Hochverfügbarkeit](https://azure.microsoft.com/support/legal/sla/cosmos-db/), dem hohen Durchsatz, der geringen Latenz und der abstimmbaren Konsistenz von Cosmos DB profitieren alle [Web-, Mobil-, Gaming- und IoT-Anwendungen](use-cases.md), die auf [globaler Ebene](distribute-data-globally.md) große Datenmengen und zahlreiche Lese- und Schreibvorgänge mit Antwortzeiten nahezu in Echtzeit für verschiedenste Daten bewältigen müssen. Erfahren Sie mehr darüber, wie Azure Cosmos DB für die Erstellung von [IoT und Telematik](use-cases.md#iot-and-telematics), [Einzelhandel und Marketing](use-cases.md#retail-and-marketing), [Gaming](use-cases.md#gaming) sowie für [mobile Anwendungen und Webanwendungen](use-cases.md#web-and-mobile-applications) verwendet werden kann.

## <a name="next-steps"></a>Nächste Schritte

Erste Schritte mit Azure Cosmos DB mit einem unserer Schnellstarts:

- [Erste Schritte mit der SQL-API von Azure Cosmos DB](create-sql-api-dotnet.md)
- [Erste Schritte mit der Azure Cosmos DB-API für MongoDB](create-mongodb-nodejs.md)
- [Erste Schritte mit der Cassandra-API von Azure Cosmos DB](create-cassandra-dotnet.md)
- [Erste Schritte mit der Gremlin-API von Azure Cosmos DB](create-graph-dotnet.md)
- [Erste Schritte mit der Tabellen-API von Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Azure Cosmos DB kostenlos testen](https://azure.microsoft.com/try/cosmosdb/)
