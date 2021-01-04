---
title: PowerShell-Skript zum Erstellen einer Ressourcensperre für eine Datenbank und Sammlung für die MongoDB-API in Azure Cosmos
description: Erstellen einer Ressourcensperre für eine Datenbank und Sammlung für die MongoDB-API in Azure Cosmos
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 06/12/2020
ms.openlocfilehash: 26210b183c48835eeaedc353bab0fd2cde4a2dbb
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93089626"
---
# <a name="create-a-resource-lock-for-azure-cosmos-mongodb-api-database-and-collection-using-azure-powershell"></a>Erstellen einer Ressourcensperre für eine Datenbank und Sammlung für die MongoDB-API für Azure Cosmos mithilfe der Azure PowerShell
[!INCLUDE[appliesto-mongodb-api](../../../includes/appliesto-mongodb-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

> [!IMPORTANT]
> Ressourcensperren funktionieren nicht bei Änderungen, die von Benutzern vorgenommen werden, die eine Verbindung über ein MongoDB SDK, die Mongo-Shell, Tools oder das Azure-Portal herstellen, es sei denn, das Cosmos DB-Konto wird zuvor bei aktivierter Eigenschaft `disableKeyBasedMetadataWriteAccess` gesperrt. Weitere Informationen zum Aktivieren dieser Eigenschaft finden Sie unter [Verhindern von Änderungen im Cosmos SDK](../../../role-based-access-control.md#prevent-sdk-changes).

## <a name="sample-script"></a>Beispielskript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-lock.ps1 "Create, list, and remove resource locks")]

## <a name="clean-up-deployment"></a>Bereinigen der Bereitstellung

Nach Ausführung des Skriptbeispiels können mit dem folgenden Befehl die Ressourcengruppe und alle damit verbundenen Ressourcen entfernt werden.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Erläuterung des Skripts

Das Skript verwendet die folgenden Befehle. Jeder Befehl in der Tabelle ist mit der zugehörigen Dokumentation verknüpft.

| Get-Help | Notizen |
|---|---|
|**Azure-Ressource**| |
| [New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) | Erstellt eine Ressourcensperre. |
| [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock) | Ruft eine Ressourcensperre ab oder listet Ressourcensperren auf. |
| [Remove-AzResourceLock](/powershell/module/az.resources/remove-azresourcelock) | Entfernt eine Ressourcensperre. |
|||

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](/powershell/).