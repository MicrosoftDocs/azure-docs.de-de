---
title: Verhindern von Ratenbegrenzungsfehlern bei Vorgängen der Azure Cosmos DB-API für MongoDB
description: Erfahren Sie, wie Sie Ratenbegrenzungsfehler bei Vorgängen Ihrer Azure Cosmos DB-API für MongoDB mithilfe des SSR-Features (Server-Side Retry, serverseitige Wiederholung) vermeiden können.
author: gahl-levy
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 01/13/2021
ms.author: gahllevy
ms.openlocfilehash: 1e9062b111c30efa90b98c4ebcee710b1d975a1d
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99507929"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-mongodb-operations"></a>Verhindern von Ratenbegrenzungsfehlern bei Vorgängen der Azure Cosmos DB-API für MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Bei Vorgängen der Azure Cosmos DB-API für MongoDB treten möglicherweise Ratenbegrenzungsfehler (16500/429) auf, wenn sie den Durchsatzgrenzwert (in RUs) einer Sammlung überschreiten. 

Sie können das Feature für die serverseitige Wiederholung (Server-Side Retry, SSR) aktivieren, damit der Server diese Vorgänge automatisch wiederholt. Die Anforderungen werden nach einer kurzen Verzögerung für alle Sammlungen in Ihrem Konto wiederholt. Dieses Feature ist eine praktische Alternative zur Behandlung von Ratenbegrenzungsfehlern in der Clientanwendung.

## <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Navigieren Sie zu Ihrem Konto für die Azure Cosmos DB-API für MongoDB.

1. Wechseln Sie im Abschnitt **Einstellungen** zum Bereich **Features**.

1. Wählen Sie **Serverseitige Wiederholung** aus.

1. Klicken Sie auf **Aktivieren**, um dieses Feature für alle Sammlungen in Ihrem Konto zu aktivieren.

:::image type="content" source="./media/prevent-rate-limiting-errors/portal-features-server-side-retry.png" alt-text="Screenshot des Features zur serverseitigen Wiederholung für die Azure Cosmos DB-API für MongoDB":::

## <a name="use-the-azure-cli"></a>Verwenden der Azure-CLI

1. Überprüfen Sie, ob SSR für Ihr Konto bereits aktiviert ist:
```bash
az cosmosdb show --name accountname --resource-group resourcegroupname
```
2. **Aktivieren** Sie SSR für alle Sammlungen in Ihrem Datenbankkonto. Es kann bis zu 15 Minuten dauern, bis diese Änderung wirksam wird.
```bash
az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo DisableRateLimitingResponses
```
Mit dem folgenden Befehl wird SSR für alle Sammlungen in Ihrem Datenbankkonto **deaktiviert**, indem „DisableRateLimitingResponses“ aus der Funktionenliste entfernt wird. Es kann bis zu 15 Minuten dauern, bis diese Änderung wirksam wird.
```bash
az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableMongo
```

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen
* Wie werden Anforderungen wiederholt?
    * Anforderungen werden fortlaufend (also immer wieder) wiederholt, bis ein 60-Sekunden-Timeout erreicht wird. Bei Erreichen des Timeouts empfängt der Client eine [Ausnahme vom Typ „ExceededTimeLimit“ (50)](mongodb-troubleshoot.md).
*  Wie kann ich die Auswirkungen von SSR überwachen?
    *  Sie können sich im Bereich mit den Cosmos DB-Metriken die serverseitig wiederholten Ratenbegrenzungsfehler (429) ansehen. Beachten Sie, dass diese Fehler nicht an den Client gesendet werden, wenn SSR aktiviert ist, da sie serverseitig behandelt und wiederholt werden. 
    *  Sie können in Ihren [Cosmos DB-Ressourcenprotokollen](cosmosdb-monitor-resource-logs.md) nach Protokolleinträgen suchen, die „estimatedDelayFromRateLimitingInMilliseconds“ enthalten.
*  Wirkt sich SSR auf die Konsistenz aus?
    *  SSR hat keine Auswirkung auf die Konsistenz einer Anforderung. Anforderungen werden serverseitig wiederholt, wenn bei ihnen eine Ratenbegrenzung vorliegt (Fehler vom Typ 429). 
*  Hat SSR Auswirkungen auf Fehlertypen, die ggf. von meinem Client empfangen werden?
    *  Nein. SSR hat nur Auswirkungen auf Ratenbegrenzungsfehler (429), indem diese serverseitig wiederholt werden. Dieses Feature sorgt dafür, dass Ratenbegrenzungsfehler nicht in der Clientanwendung behandelt werden müssen. Alle [anderen Fehler](mongodb-troubleshoot.md) werden an den Client gesendet. 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung bei häufigen Fehlern finden Sie in diesem Artikel:

* [Behandeln häufiger Probleme in der Azure Cosmos DB-API für MongoDB](mongodb-troubleshoot.md)
