---
title: PowerShell-Skript zum Aktualisieren der Standardkonsistenzebene für ein Azure Cosmos-Konto
description: 'Azure PowerShell-Skriptbeispiel: Aktualisieren der Standardkonsistenzebene für ein Azure Cosmos DB-Konto mithilfe von PowerShell'
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/21/2020
ms.author: mjbrown
ms.openlocfilehash: ff19d910c03c4957ca50a57db9c5a0587d435930
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099537"
---
# <a name="update-the-regions-on-an-azure-cosmos-db-account-using-powershell"></a>Aktualisieren der Regionen für ein Azure Cosmos DB-Konto mithilfe von PowerShell
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Beispielskript

> [!NOTE]
> Regionen und andere Cosmos-Kontoeigenschaften können nicht im gleichen Vorgang geändert werden. Dafür müssen zwei separate Vorgänge ausgeführt werden.
> [!NOTE]
> Dieses Beispiel veranschaulicht die Verwendung eines SQL-API-Kontos. Wenn Sie dieses Beispiel für andere APIs verwenden möchten, kopieren Sie die zugehörigen Eigenschaften, und wenden Sie sie auf Ihr API-spezifisches Skript an.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/common/ps-account-update.ps1 "Update an Azure Cosmos DB account")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Nach Ausführung des Skriptbeispiels können mit dem folgenden Befehl die Ressourcengruppe und alle damit verbundenen Ressourcen entfernt werden.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
|**Azure Cosmos DB**| |
| [Get-AzCosmosDBAccount](/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | Listet Cosmos DB-Konten auf oder ruft ein bestimmtes Cosmos DB-Konto ab. |
| [Update-AzCosmosDBAccount](/powershell/module/az.cosmosdb/update-azcosmosdbaccountfailoverpriority) | Aktualisieren eines Cosmos DB-Kontos |
|**Azure-Ressourcengruppen**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Löscht eine Ressourcengruppe einschließlich aller geschachtelten Ressourcen. |
|||

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](/powershell/).