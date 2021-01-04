---
title: Gebühren für Anforderungseinheiten für Azure Cosmos DB als Schlüsselwertspeicher
description: Informationen zu den Gebühren für Anforderungseinheiten von Azure Cosmos DB für einfache Schreib- und Lesevorgänge bei Verwendung als Schlüsselwertspeicher.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 9354ae0a22ef2e8ab4ee6a57563d3f3c4c8e4547
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93339300"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>Azure Cosmos DB als Schlüsselwertspeicher – Kostenübersicht
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB ist ein global verteilter Datenbankdienst mit mehreren Modellen für die mühelose Erstellung hochverfügbarer, umfangreicher Anwendungen. Standardmäßig indiziert Azure Cosmos DB automatisch und effizient alle erfassten Daten. Dies ermöglicht schnelle und konsistente [SQL](./sql-query-getting-started.md)-Abfragen (und [JavaScript](stored-procedures-triggers-udfs.md)-Abfragen) für die Daten. 

In diesem Artikel werden die Kosten von Azure Cosmos DB für einfache Schreib- und Lesevorgänge bei Verwendung als Schlüssel-/Wertspeicher beschrieben. Zu den Schreibvorgängen zählen Einfügungen, Ersetzungen, Löschungen und Upserts von Datenelementen. Neben der garantierten SLA mit 99,999 % Verfügbarkeit für alle Konten mit mehreren Regionen bietet Azure Cosmos DB garantiert unter 10 ms Latenz für Lesevorgänge und für die (indizierten) Schreibvorgänge im 99. Perzentil. 

## <a name="why-we-use-request-units-rus"></a>Gründe für die Verwendung von Anforderungseinheiten (Request Units, RUs)

Die Azure Cosmos DB-Leistung basiert auf der Menge des bereitgestellten Durchsatzes, der in [Anforderungseinheiten](request-units.md) (Request Units, RU) angegeben wird. Die Bereitstellung erfolgt mit einer zweiten Granularität und wird in RU pro Sekunde erworben ([nicht zu verwechseln mit der stündlichen Abrechnung](https://azure.microsoft.com/pricing/details/cosmos-db/)). Betrachten Sie RUs als logische Abstraktion (eine Währung), die die Bereitstellung des für die Anwendung erforderlichen Durchsatzes vereinfacht. Benutzer müssen nicht zwischen Lese- und Schreibdurchsatz differenzieren. Das einzelne Währungsmodell der RUs sorgt für die nötige Effizienz, um die bereitgestellte Kapazität zwischen Lese- und Schreibvorgängen aufzuteilen. Mit diesem bereitgestellten Kapazitätsmodell kann der Dienst einen **vorhersagbaren und konsistenten Durchsatz, garantiert niedrige Latenz und Hochverfügbarkeit bieten**. Das RU-Modell wird außerdem zwar zur Darstellung des Durchsatzes verwendet, aber jede bereitgestellte RU verfügt auch über eine definierte Menge von Ressourcen (z. B. Arbeitsspeicher, Kerne/CPU und IOPS).

Als global verteiltes Datenbanksystem ist Cosmos DB der einzige Azure-Dienst, der umfassende SLAs zu Latenz, Durchsatz, Konsistenz und Hochverfügbarkeit bietet. Der Durchsatz, den Sie bereitstellen, wird auf jede der Regionen angewandt, die Ihrem Cosmos-Konto zugeordnet sind. Für Lesevorgänge bietet Cosmos DB mehrere klar definierte [Konsistenzebenen](consistency-levels.md), zwischen denen Sie wählen können. 

In der folgenden Tabelle ist die Anzahl der erforderlichen RUs für Lese- und Schreibvorgänge basierend auf einem Datenelement mit einer Größe von 1 KB und 100 KB/s angegeben, während die standardmäßige automatische Indizierung deaktiviert ist. 

|Elementgröße|1 Lesevorgang|1 Schreibvorgang|
|-------------|------|-------|
|1 KB|1 RU|5 RUs|
|100 KB|10 RUs|50 RUs|

## <a name="cost-of-reads-and-writes"></a>Kosten für Lese- und Schreibvorgänge

Wenn Sie 1.000 RU/Sekunde bereitstellen, ergibt dies 3,6 Millionen RU/Stunde und kostet 0,08 US-Dollar pro Stunde (in den USA und Europa). Für ein Datenelement mit einer Größe von 1 KB können Sie demnach mit dem bereitgestellten Durchsatz 3,6 Millionen Lesevorgänge oder 0,72 Millionen Schreibvorgänge (3,6 Mio. RU / 5) durchführen. Normalisiert auf eine Million Lese- und Schreibvorgänge betragen die Kosten für Lesevorgänge 0,022 US-Dollar/Million (0,08 US-Dollar / 3,6) und für Schreibvorgänge 0,111 US-Dollar/Million (0,08 US-Dollar / 0,72). Wie in der folgenden Tabelle gezeigt, werden die Kosten pro Million minimal.

|Elementgröße|Kosten von 1 Million Lesevorgängen|Kosten von 1 Million Schreibvorgängen|
|-------------|-------|--------|
|1 KB|$0.022|$0.111|
|100 KB|$0.222|$1.111|


Die meisten grundlegenden Blob- oder Objektspeicherdienste berechnen Gebühren von 0,40 US-Dollar pro Million Lesetransaktionen und 5 US-Dollar pro Million Schreibtransaktionen. Bei optimaler Nutzung kann Cosmos DB bis zu 98 % günstiger sein als diese anderen Lösungen (bei Transaktionen von 1 KB).

## <a name="next-steps"></a>Nächste Schritte

* Verwenden Sie den [RU-Rechner](https://cosmos.azure.com/capacitycalculator/) für die Schätzung des Durchsatzes für Ihre Workloads.