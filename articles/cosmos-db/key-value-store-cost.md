---
title: Gebühren für Anforderungseinheiten für Azure Cosmos DB als Schlüsselwertspeicher
description: Informationen zu den Gebühren für Anforderungseinheiten von Azure Cosmos DB für einfache Schreib- und Lesevorgänge bei Verwendung als Schlüsselwertspeicher.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: a3b4886bc175311e9a0e61f3dfb99b1fe7e1a679
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236758"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure Cosmos DB als Schlüsselwertspeicher – Kostenübersicht

Azure Cosmos DB ist ein global verteilter Datenbankdienst mit mehreren Modellen für die mühelose Erstellung hochverfügbarer, umfangreicher Anwendungen. Standardmäßig indiziert Azure Cosmos DB automatisch alle erfassten Daten effizient. Dies ermöglicht schnelle und konsistente [SQL](how-to-sql-query.md)-Abfragen (und [JavaScript](stored-procedures-triggers-udfs.md)-Abfragen) für jede Art von Daten. 

In diesem Artikel werden die Kosten von Azure Cosmos DB für einfache Schreib- und Lesevorgänge bei Verwendung als Schlüssel-/Wertspeicher beschrieben. Zu den Schreibvorgängen zählen Einfügungen, Ersetzungen, Löschungen und Upserts von Dokumenten. Neben der Garantie einer SLA mit einer Verfügbarkeit von 99,99 Prozent für alle Konten mit einer einzelnen Region und für alle Konten mit mehreren Regionen und gelockerter Konsistenz sowie einer Leseverfügbarkeit von 99,999 Prozent für alle Datenbankkonten mit mehreren Regionen bietet Azure Cosmos DB eine garantierte Wartezeit von weniger als 10 ms für Lesevorgänge bzw. von weniger als 15 ms für die (indizierten) Schreibvorgänge (im 99 Perzentil). 

## <a name="why-we-use-request-units-rus"></a>Gründe für die Verwendung von Anforderungseinheiten (Request Units, RUs)

Die Azure Cosmos DB-Leistung basiert auf der Menge der für die Partition bereitgestellten [Anforderungseinheiten](request-units.md) (Request Units, RU). Die Bereitstellung erfolgt mit einer zweiten Granularität und wird in RUs pro Sekunde erworben ([nicht zu verwechseln mit der stündlichen Abrechnung](https://azure.microsoft.com/pricing/details/cosmos-db/)). Betrachten Sie RUs als eine Währung, die die Bereitstellung des für die Anwendung erforderlichen Durchsatzes vereinfacht. Unsere Kunden müssen nicht zwischen Lese- und Schreibkapazitätseinheiten differenzieren. Das einzelne Währungsmodell der RUs sorgt für die nötige Effizienz, um die bereitgestellte Kapazität zwischen Lese- und Schreibvorgängen aufzuteilen. Dieses bereitgestellte Kapazitätsmodell ermöglicht dem Dienst, einen vorhersagbaren und konsistenten Durchsatz, garantiert niedrige Latenz und Hochverfügbarkeit zu bieten. Außerdem verwenden wir zwar RUs für Durchsatzmodelle, aber jede bereitgestellte RU verfügt auch über eine definierte Menge von Ressourcen (Arbeitsspeicher, Kern). RU/Sekunde ist nicht nur ein IOPS-Wert.

Als global verteiltes Datenbanksystem ist Cosmos DB der einzige Azure-Dienst, der außer zur Hochverfügbarkeit eine SLA zu Latenz, Durchsatz und Konsistenz bietet. Der Durchsatz, den Sie bereitstellen, wird auf jede der Regionen angewendet, die Ihrem Cosmos DB-Datenbankkonto zugeordnet ist. Für Lesevorgänge bietet Cosmos DB mehrere klar definierte [Konsistenzebenen](consistency-levels.md), zwischen denen Sie wählen können. 

Die folgende Tabelle gibt anhand der Dokumentgrößen 1 KB und 100 KB Aufschluss über die erforderliche Anzahl von RUs für Lese- und Schreibtransaktionen:

|Elementgröße|1 Lesevorgang|1 Schreibvorgang|
|-------------|------|-------|
|1 KB|1 RU|5 RUs|
|100 KB|10 RUs|50 RUs|

## <a name="cost-of-reads-and-writes"></a>Kosten für Lese- und Schreibvorgänge

Wenn Sie 1.000 RUs/Sekunde bereitstellen, ergibt dies 3,6 Mio. RUs/Stunde und kostet 0,08 US-Dollar pro Stunde (in den USA und Europa). Für ein Dokument mit einer Größe von 1 KB können Sie demnach mit dem von Ihnen bereitgestellten Durchsatz 3,6 Mio. Lesevorgänge oder 0,72 Mio. Schreibvorgänge (3,6 Mio. RU / 5) durchführen. Normalisiert auf eine Million Lese- und Schreibvorgänge würden die Kosten für Lesevorgänge 0,022 US-Dollar/Million (0,08 US-Dollar / 3,6) und für Schreibvorgänge 0,111 US-Dollar/Million (0,08 US-Dollar / 0,72) betragen. Wie in der folgenden Tabelle gezeigt, werden die Kosten pro Million minimal.

|Elementgröße|Eine Million Lesevorgänge|Eine Million Schreibvorgänge|
|-------------|-------|--------|
|1 KB|$0.022|$0.111|
|100 KB|$0.222|$1.111|


Die meisten grundlegenden Blob- oder Objektspeicherdienste berechnen Gebühren von 0,40 US-Dollar pro Million Lesetransaktionen und 5 US-Dollar pro Million Schreibtransaktionen. Bei optimaler Nutzung kann Cosmos DB bis zu 98 Prozent günstiger sein als diese anderen Lösungen (bei Transaktionen von 1 KB).

## <a name="next-steps"></a>Nächste Schritte

Bleiben Sie auf dem Laufenden, was neue Artikel zum Optimieren der Azure Cosmos DB-Ressourcenbereitstellung betrifft. In der Zwischenzeit laden wir Sie ein, unseren [RU-Rechner](https://www.documentdb.com/capacityplanner) zu nutzen.

