---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 1f8f8d314a8bb37a08b3696f597b395a8a4beb8e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66170638"
---
## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich mit dem Befehl `Connect-AzAccount` bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm.

```powershell
Connect-AzAccount
```

Wenn Sie sich nicht sicher sind, welche Region Sie verwenden sollen, können Sie die verfügbaren Regionen auflisten. Zeigen Sie mithilfe des folgenden Codebeispiels die Liste mit den Standorten an, und suchen Sie nach dem gewünschten Standort. In diesem Beispiel wird **eastus** verwendet. Speichern Sie den Standort in einer Variablen, und verwenden Sie die Variable, um den Standort an einem zentralen Ort ändern zu können.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Azure-Ressourcengruppe. Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

```powershell
$resourceGroup = "myResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>Speicherkonto erstellen

Erstellen Sie mithilfe von [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) ein universelles Standardspeicherkonto mit LRS-Replikation. Rufen Sie als Nächstes den Kontext des Speicherkontos ab, der das zu verwendende Speicherkonto definiert. Wenn Sie Aktionen für ein Speicherkonto ausführen, verweisen Sie auf den Kontext, anstatt immer wieder die Anmeldeinformationen zu übergeben. Verwenden Sie das folgende Beispiel, um ein Speicherkonto namens *mystorageaccount* mit lokal redundantem Speicher (Locally Redundant Storage, LRS) und Blobverschlüsselung (standardmäßig aktiviert) zu erstellen.

```powershell
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name "mystorageaccount" `
  -SkuName Standard_LRS `
  -Location $location `

$ctx = $storageAccount.Context
```
