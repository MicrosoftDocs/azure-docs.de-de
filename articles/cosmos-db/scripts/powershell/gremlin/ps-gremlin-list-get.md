---
title: 'Azure PowerShell-Skript: list- und get-Vorgänge von Azure Cosmos DB für die Gremlin-API'
description: 'Azure PowerShell-Skript: list- und get-Vorgänge von Azure Cosmos DB für die Gremlin-API'
author: markjbrown
ms.service: cosmos-db
ms.topic: samples
ms.date: 05/18/2019
ms.author: mjbrown
ms.openlocfilehash: 507c17d62473ee8f9166ef822ef0f6fa4ae8008e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245511"
---
# <a name="list-and-get-databases-and-graphs-for-azure-cosmos-db---gremlin-api"></a>Auflisten und Abrufen von Datenbanken und Graphen für Azure Cosmos DB: Gremlin-API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Beispielskript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-list-get.ps1 "List or get databases or graphs for Gremlin API")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Nach Ausführung des Skriptbeispiels können mit dem folgenden Befehl die Ressourcengruppe und alle damit verbundenen Ressourcen entfernt werden.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
|**Azure-Ressourcen**| |
| [New-AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | Dient zum Erstellen einer Ressource. |
|**Azure-Ressourcengruppen**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |
|||

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](https://docs.microsoft.com/powershell/).

Zusätzliche PowerShell-Skriptbeispiele für Azure Cosmos DB finden Sie unter [PowerShell-Skripts für Azure Cosmos DB](../../../powershell-samples.md).