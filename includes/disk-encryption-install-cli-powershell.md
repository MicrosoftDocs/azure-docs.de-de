---
title: include file
description: include file
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 1c1d438f0322942a1e68c0af74de8d5e2d77c77a
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95558059"
---
Azure Disk Encryption kann über die [Azure CLI](/cli/azure) und [Azure PowerShell](/powershell/azure/new-azureps-module-az) aktiviert und verwaltet werden. Zu diesem Zweck müssen Sie die Tools lokal installieren und eine Verbindung mit Ihrem Azure-Abonnement herstellen.

### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

Die [Azure CLI 2.0](/cli/azure) ist ein Befehlszeilentool zum Verwalten von Azure-Ressourcen. Sie wurde entwickelt, um Daten flexible abzufragen, Vorgänge mit langer Ausführungsdauer als nicht blockierende Prozesse zu unterstützen und das Erstellen von Skripts zu vereinfachen. Sie können sie lokal installieren, indem Sie die Schritte unter [Installieren der Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) ausführen.

Um [sich bei Ihrem Azure-Konto mit der Azure CLI anzumelden](/cli/azure/authenticate-azure-cli), verwenden Sie den Befehl [az login](/cli/azure/reference-index?view=azure-cli-latest#az-login).

```azurecli
az login
```

Verwenden Sie Folgendes, falls Sie für die Anmeldung einen Mandanten auswählen möchten:
    
```azurecli
az login --tenant <tenant>
```

Wenn Sie über mehrere Abonnements verfügen und ein bestimmtes Abonnement angeben möchten, können Sie Ihre Abonnementliste mit [az account list](/cli/azure/account#az-account-list) abrufen und den Befehl [az account set](/cli/azure/account#az-account-set) zum Angeben verwenden.
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Weitere Informationen finden Sie unter [Erste Schritte mit Azure CLI 2.0](/cli/azure/get-started-with-azure-cli). 

### <a name="azure-powershell"></a>Azure PowerShell
Das [az-Modul von Azure PowerShell](/powershell/azure/new-azureps-module-az) bietet eine Reihe von Cmdlets, die das [Azure Resource Manager](../articles/azure-resource-manager/management/overview.md)-Modell für die Verwaltung von Azure-Ressourcen verwenden. Azure PowerShell kann mit [Azure Cloud Shell](../articles/cloud-shell/overview.md) im Browser verwendet oder auf dem lokalen Computer mithilfe der Anleitungen unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) installiert werden. 

Falls Sie die lokale Installation bereits durchgeführt haben, verwenden Sie die neueste Version des Azure PowerShell SDKs, um Azure Disk Encryption zu konfigurieren. Laden Sie die neueste Version von [Azure PowerShell](https://github.com/Azure/azure-powershell/releases) herunter.

Um [sich mit Azure PowerShell bei Ihrem Azure-Konto anzumelden](/powershell/azure/authenticate-azureps?view=azps-2.5.0), verwenden Sie das Cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0).

```powershell
Connect-AzAccount
```

Wenn Sie über mehrere Abonnements verfügen und eines davon angeben möchten, verwenden Sie das Cmdlet [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription), um die Abonnements aufzulisten, gefolgt vom Cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0):

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Wenn Sie das Cmdlet [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) ausführen, wird überprüft, ob das richtige Abonnement ausgewählt wurde.

Um zu bestätigen, dass die Azure Disk Encryption-Cmdlets installiert sind, verwenden Sie das Cmdlet [Get-Command](/powershell/module/microsoft.powershell.core/get-command?view=powershell-6):
     
```powershell
Get-command *diskencryption*
```
Weitere Informationen finden Sie unter [Erste Schritte mit Azure PowerShell](/powershell/azure/get-started-azureps).