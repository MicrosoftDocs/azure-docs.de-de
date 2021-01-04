---
title: Troubleshooting für Timeoutausnahmen bei Dienstanforderungen (Service Requests) in Azure Cosmos DB
description: Hier erfahren Sie etwas zu Diagnose und Fehlerbehebung für Timeoutausnahmen bei Dienstanforderungen (Service Requests) in Azure Cosmos DB.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 5b188021de30561222f098e2b5782bada25d7ce0
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94411251"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-timeout-exceptions"></a>Diagnose und Troubleshooting für Anforderungstimeoutausnahmen in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In Azure Cosmos DB wurde ein HTTP-408-Anforderungstimeout zurückgegeben.

## <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung
Die folgende Liste enthält bekannte Gründe und Lösungen für Anforderungstimeoutausnahmen.

### <a name="check-the-sla"></a>Überprüfen des SLA
Überprüfen Sie unter [Azure Cosmos DB-Überwachung](monitor-cosmos-db.md), ob die Anzahl der 408-Ausnahmen gegen die Azure Cosmos DB-SLA verstößt.

#### <a name="solution-1-it-didnt-violate-the-azure-cosmos-db-sla"></a>Lösung 1: Die Anzahl hat nicht gegen die Azure Cosmos DB-SLA verstoßen.
Die Anwendung sollte mit diesem Szenario umgehen können und für diese vorübergehenden Fehler jeweils wiederholte Versuche durchführen.

#### <a name="solution-2-it-did-violate-the-azure-cosmos-db-sla"></a>Lösung 2: Die Anzahl hat gegen die Azure Cosmos DB-SLA verstoßen.
Wenden Sie sich an den [Azure-Support](https://aka.ms/azure-support).
 
### <a name="hot-partition-key"></a>Partitionsschlüssel auf heißer Ebene
Azure Cosmos DB verteilt den gesamten bereitgestellten Durchsatz gleichmäßig auf die physischen Partitionen. Wenn es eine heiße Partition gibt, werden alle Anforderungseinheiten pro Sekunde (RU/s) einer physischen Partition von einem logischen Partitionsschlüssel (oder mehreren logischen Partitionsschlüsseln) darauf verbraucht. Gleichzeitig werden die RU/s auf anderen physischen Partitionen nicht genutzt. Dies zeigt sich darin, dass die Gesamtanzahl der verbrauchten RU/s geringer als die insgesamt bereitgestellten RU/s in der Datenbank oder im Container ist. Bei den Anforderungen für den Schlüssel für die heiße logische Partition wird weiterhin „Drosselung (429s)“ angezeigt. Im Artikel [Überwachen von normalisierten RU/s für einen Azure Cosmos-Container oder ein -Konto](monitor-normalized-request-units.md) finden Sie weitere Informationen, wie Sie erfahren, ob die Workload Probleme mit der Partition auf heißer Ebene hat. 

#### <a name="solution"></a>Lösung:
Wählen Sie einen geeigneten Partitionsschlüssel aus, der Anforderungsvolume und -speicher gleichmäßig verteilt. Weitere Informationen finden Sie unter [Ändern des Partitionsschlüssels in Azure Cosmos DB](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

## <a name="next-steps"></a>Nächste Schritte
* Informieren Sie sich zu [Diagnostizieren und Behandeln](troubleshoot-dot-net-sdk.md) von Problemen bei Verwendung des .NET SDK für Azure Cosmos DB.
* Informieren Sie sich zu Leistungsrichtlinien für [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) und [.NET v2](performance-tips.md).
* [Diagnostizieren und Behandeln](troubleshoot-java-sdk-v4-sql.md) von Problemen bei Verwendung des Java v4 SDK für Azure Cosmos DB.
* Weitere Informationen zu Leistungsrichtlinien für das [Java v4 SDK](performance-tips-java-sdk-v4-sql.md).