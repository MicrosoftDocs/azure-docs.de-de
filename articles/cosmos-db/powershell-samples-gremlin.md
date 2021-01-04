---
title: 'Azure PowerShell-Beispiele für Azure Cosmos DB: Gremlin-API'
description: Hier erfahren Sie, wie Sie Azure PowerShell-Beispiele zum Ausführen gängiger Aufgaben in der Gremlin-API für Azure Cosmos DB abrufen.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.openlocfilehash: c406ea58bfba0128a31489aa7b834c6a586d74d3
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93332949"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-gremlin-api"></a>Azure PowerShell-Beispiele für Azure Cosmos DB: Gremlin-API
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Die unten stehende Tabelle enthält Links zu häufig verwendeten Azure PowerShell-Skripts für Azure Cosmos DB. Navigieren Sie über die Links auf der rechten Seite zu API-spezifischen Beispielen. Allgemeine Beispiele sind in allen APIs identisch. Referenzseiten für alle Azure Cosmos DB-PowerShell-Cmdlets finden Sie in der [Referenz zu Azure PowerShell](/powershell/module/az.cosmosdb). Prüfen Sie regelmäßig auf Updates für `Az.CosmosDB`. Sie können diese PowerShell-Beispiele für Cosmos DB auch aus unserem GitHub-Repository forken. Informationen dazu finden Sie in den [Cosmos DB-PowerShell-Beispielen auf GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

## <a name="common-samples"></a>Allgemeine Beispiele

|Aufgabe | BESCHREIBUNG |
|---|---|
|[Konto aktualisieren](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualisieren der Standardkonsistenzebene eines Cosmos DB-Kontos |
|[Regionen eines Kontos aktualisieren](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Aktualisieren der Regionen eines Cosmos DB-Kontos |
|[Failoverpriorität ändern oder Failover auslösen](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ändern der Priorität eines regionalen Failovers eines Azure Cosmos-Kontos oder Auslösen eines manuellen Failovers |
|[Kontoschlüssel oder Verbindungszeichenfolgen](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Abrufen von Primär- und Sekundärschlüsseln und Verbindungszeichenfolgen oder erneutes Generieren eines Kontoschlüssels eines Azure Cosmos DB-Kontos |
|[Cosmos-Konto mit IP-Firewall erstellen](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Erstellen eines Azure Cosmos DB-Kontos mit aktivierter IP-Firewall |
|||

## <a name="gremlin-api-samples"></a>Gremlin-API-Beispiele

|Aufgabe | BESCHREIBUNG |
|---|---|
|[Konto, Datenbank und Graph erstellen](scripts/powershell/gremlin/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Erstellen eines Kontos, einer Datenbank und eines Graphs in Azure Cosmos |
|[Konto, Datenbank und Graph mit Autoskalierung erstellen](scripts/powershell/gremlin/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Erstellen eines Kontos, einer Datenbank und eines Graphen mit Autoskalierung in Azure Cosmos |
|[Datenbanken oder Graphen auflisten oder abrufen](scripts/powershell/gremlin/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Auflisten oder Abrufen einer Datenbank oder eines Graphs |
|[Durchsatzvorgänge](scripts/powershell/gremlin/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Durchsatzvorgänge für eine Datenbank oder einen Graphen, z. B. Abrufen, Aktualisieren und Migrieren zwischen automatisch skaliertem und standardmäßigem Durchsatz. |
|[Sperren von Ressourcen für die Löschung](scripts/powershell/gremlin/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Verhindern des Löschens von Ressourcen mit Ressourcensperren. |
|||
