---
title: Globale Verteilung von Daten mit Azure Cosmos DB
description: Erfahren Sie mehr über weltweite Georeplikation, Multimaster, Failover und Datenwiederherstellung mithilfe der globalen Datenbanken von Azure Cosmos DB, einem global verwalteten Datenbankdienst, der mehrere Modelle unterstützt.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.openlocfilehash: e58a8cd286e4d416dd5f4e6d3fddedf1897fed1c
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65954168"
---
# <a name="global-data-distribution-with-azure-cosmos-db---overview"></a>Globale Datenverteilung mit Azure Cosmos DB: Übersicht

Moderne Anwendungen erfordern eine hohe Reaktionsschnelligkeit und müssen immer online sein. Um eine geringe Latenz und Hochverfügbarkeit zu erreichen, müssen Instanzen dieser Anwendungen in Rechenzentren eingesetzt werden, die sich in der Nähe ihrer Benutzer befinden. Diese Anwendungen werden in der Regel in mehreren Rechenzentren bereitgestellt und als global verteilt bezeichnet. Global verteilte Anwendungen benötigen eine global verteilte Datenbank, die die Daten überall auf der Welt transparent replizieren kann, damit die Anwendungen mit der Kopie der Daten arbeiten können, die sich in der Nähe ihrer Benutzer befindet. 

Azure Cosmos DB ist ein global verteilter Datenbankdienst, der kurze Wartezeiten, elastische Skalierbarkeit des Durchsatzes, gut definierte Semantik zum Gewährleisten der Datenkonsistenz und Hochverfügbarkeit bietet. Kurz gesagt: Wenn Ihre Anwendung auf der ganzen Welt schnelle Antwortzeiten garantieren muss, immer online sein muss und eine unbegrenzte sowie elastische Skalierbarkeit des Durchsatzes und Speichers erfordert, sollten Sie Ihre Anwendungen mit Azure Cosmos DB erstellen.

Sie können Ihre Datenbanken so konfigurieren, dass sie global verteilt werden und in beliebig vielen der Azure-Regionen verfügbar sind. Platzieren Sie die Daten so nah wie möglich am Standort Ihrer Benutzer, um die Wartezeiten zu verringern. Die Auswahl der erforderlichen Regionen hängt von der globalen Reichweite Ihrer Anwendung und vom Standort Ihrer Benutzer ab. Cosmos DB repliziert die Daten transparent in alle Regionen, die Ihrem Cosmos-Konto zugeordnet sind. Der Dienst stellt ein einziges Systemimage Ihrer global verteilten Azure Cosmos-Datenbank und -Container bereit, das lokal von Ihrer Anwendung gelesen und beschrieben werden kann. 

Mit Azure Cosmos DB können Sie Ihrem Konto jederzeit neue Regionen hinzufügen oder Regionen daraus entfernen. Ihre Anwendung muss nicht angehalten oder erneut bereitgestellt werden, um eine Region hinzuzufügen oder zu entfernen. Sie bleibt weiterhin jederzeit hochverfügbar, da der Dienst nativ multihostingfähig ist.

![Topologie zur Bereitstellung mit Hochverfügbarkeit](./media/distribute-data-globally/deployment-topology.png)

## <a name="key-benefits-of-global-distribution"></a>Wichtige Vorteile der globalen Verteilung

**Erstellen von globalen Aktiv/Aktiv-Apps.** Mit dem neuen Multimaster-Replikationsprotokoll unterstützt jetzt jede Region sowohl Schreib- als auch Lesevorgänge. Außerdem ermöglicht die Multimasterfunktion Folgendes:

- Unbegrenzte elastische Schreib und Leseskalierbarkeit. 
- 99,999 % Lese- und Schreibverfügbarkeit weltweit.
- Eine garantierte Verarbeitung von Lese-/Schreibvorgängen in weniger als 10 Millisekunden im 99. Perzentil.

Mithilfe der Multihosting-APIs von Azure Cosmos DB kennt Ihre Anwendung die nächstgelegene Region und sendet Anforderungen an diese Region. Die nächstgelegene Region wird ohne jede Konfigurationsänderung identifiziert. Wenn Sie Ihrem Azure Cosmos-Konto Regionen hinzufügen oder Regionen daraus entfernen, muss Ihre Anwendung nicht erneut bereitgestellt oder unterbrochen werden, und sie ist weiterhin jederzeit hoch verfügbar.

**Erstellen von extrem reaktionsschnellen Apps.** Ihre Anwendung kann Lese- und Schreibvorgänge nahezu in Echtzeit für alle Regionen ausführen, die Sie für Ihre Datenbank auswählen. Azure Cosmos DB verarbeitet die Datenreplikation zwischen Regionen intern auf eine Weise, die die ausgewählte Konsistenzebene garantiert.

**Erstellen von hochverfügbaren Apps.** Die Ausführung einer Datenbank in mehreren Regionen auf der ganzen Welt verbessert die Verfügbarkeit der Datenbank. Wenn eine Region nicht verfügbar ist, werden Anwendungsanforderungen automatisch von anderen Regionen verarbeitet. Azure Cosmos DB bietet eine Lese- und Schreibverfügbarkeit von 99,999 % für Datenbanken in mehreren Regionen.

**Geschäftskontinuität bei Ausfall einer Region.** Azure Cosmos DB unterstützt ein [automatisches Failover](how-to-manage-database-account.md#automatic-failover) beim Ausfall einer Region. Darüber hinaus sorgt Azure Cosmos DB beim Ausfall einer Region für die Einhaltung der SLAs in Bezug auf Latenz, Verfügbarkeit, Konsistenz und Durchsatz. Um zu gewährleisten, dass Ihre gesamte Anwendung hochverfügbar ist, bietet Cosmos DB eine API für ein manuelles Failover, um einen Regionsausfall zu simulieren. Mithilfe dieser API können Sie regelmäßige Übungen zur Gewährleistung der Geschäftskontinuität durchführen.

**Globale Skalierbarkeit von Lese- und Schreibvorgängen.** Sie können jede Region als schreibbar aktivieren und Lese- und Schreibvorgänge auf der ganzen Welt flexibel skalieren. Es wird garantiert, dass der Durchsatz, den Ihre Anwendung in einer Azure Cosmos-Datenbank oder einem Container konfiguriert, in allen Ihrem Azure Cosmos-Konto zugeordneten Regionen bereitgestellt wird. Der bereitgestellte Durchsatz wird immer durch [finanziell abgesicherte SLAs](https://aka.ms/acdbsla) garantiert.

**Mehrere klar definierte Konsistenzmodelle zur Auswahl.** Das Replikationsprotokoll von Azure Cosmos DB bietet fünf klar definierte, praktische und intuitive Konsistenzmodelle. Jedes Modell stellt einen klar beschriebenen Kompromiss zwischen Konsistenz und Leistung dar. Mit diesen Konsistenzmodellen können Sie problemlos global verteilte Anwendungen erstellen.

## <a id="Next Steps"></a>Nächste Schritte

In den folgenden Artikeln erfahren Sie mehr über die globale Verteilung:

* [Globale Verteilung: Hintergrundinformationen](global-dist-under-the-hood.md)
* [Konfigurieren von Multimaster in Ihren Anwendungen](how-to-multi-master.md)
* [Konfigurieren von Clients für Multihoming](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Hinzufügen/Entfernen von Regionen in Ihrem Azure Cosmos DB-Konto](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Erstellen einer benutzerdefinierten Konfliktlösungsrichtlinie für SQL-API-Konten](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)