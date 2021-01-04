---
title: 'Schnellstart: Erstellen einer Azure Dedicated HSM-Instanz mit Azure PowerShell'
description: Erstellen einer Azure Dedicated HSM-Instanz mit Azure PowerShell
services: dedicated-hsm
author: msmbaldwin
ms.author: mbaldwin
ms.topic: quickstart
ms.service: key-vault
ms.devlang: azurepowershell
ms.date: 11/13/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: eebfb257d0324cf2771bd3af979ddbebb8429fb7
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94905546"
---
# <a name="quickstart-create-an-azure-dedicated-hsm-with-azure-powershell"></a>Schnellstart: Erstellen einer Azure Dedicated HSM-Instanz mit Azure PowerShell

In diesem Artikel erfahren Sie, wie Sie mithilfe des PowerShell-Moduls [Az.DedicatedHsm](/powershell/module/az.dedicatedhsm) eine Azure Dedicated HSM-Instanz erstellen.

## <a name="requirements"></a>Requirements (Anforderungen)

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Solange sich das PowerShell-Modul **Az.DedicatedHsm** in der Vorschauphase befindet, muss es mithilfe des Cmdlets `Install-Module` separat installiert werden. Sobald dieses PowerShell-Modul allgemein verfügbar ist, wird es in die zukünftigen Releases des Az PowerShell-Moduls integriert und in Azure Cloud Shell standardmäßig zur Verfügung gestellt.

  ```azurepowershell-interactive
  Install-Module -Name Az.DedicatedHsm
  ```

* Wenn Sie über mehrere Azure-Abonnements verfügen, müssen Sie das entsprechende Abonnement auswählen, in dem die Ressourcen fakturiert werden sollen. Wählen Sie mit dem Cmdlet [Set-AzContext](/powershell/module/az.accounts/set-azcontext) ein bestimmtes Abonnement aus.

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit dem Cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine [Azure-Ressourcengruppe](../azure-resource-manager/management/overview.md). Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen als Gruppe bereitgestellt und verwaltet werden.

Im folgenden Beispiel wird eine Ressourcengruppe mit dem angegebenen Namen am angegebenen Standort erstellt.

```azurepowershell-interactive
New-AzResourceGroup -Name myRG -Location westus
```

## <a name="create-a-dedicated-hsm"></a>Erstellen eines dedizierten HSM

Verwenden Sie zum Erstellen eines dedizierten HSM das Cmdlet [New-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/new-azdedicatedhsm). Im folgenden Beispiel wird ein dediziertes HSM im angegebenen Abonnement erstellt:

```azurepowershell-interactive
$Params = @{
  Name  = 'MyHSM'
  ResourceGroupName = 'myRG'
  Location = 'westus'
  Sku = 'SafeNet Luna Network HSM A790'
  StampId = 'stamp1'
  SubnetId = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myhsm-vnet/subnets/hsmsubnet'
  NetworkInterface = @{PrivateIPAddress = '10.2.1.120'}
}
New-AzDedicatedHsm @Params
```

```Output
Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="get-a-dedicated-hsm"></a>Abrufen eines dedizierten HSM

Wenn Sie Informationen zu einem bereits vorhandenen dedizierten HSM abrufen möchten, verwenden Sie das Cmdlet [Get-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/get-azdedicatedhsm). Im folgenden Beispiel wird das angegebene dedizierte HSM abgerufen:

```azurepowershell-interactive
Get-AzDedicatedHsm -Name MyHSM -ResourceGroupName myRG
```

```Output
Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="update-a-dedicated-hsm"></a>Aktualisieren eines dedizierten HSM

Verwenden Sie zum Aktualisieren eines dedizierten HSM das Cmdlet [Update-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/update-azdedicatedhsm). Im folgenden Beispiel wird ein dediziertes HSM im angegebenen Abonnement aktualisiert:

```azurepowershell-interactive
Update-AzDedicatedHsm -Name MyHSM -ResourceGroupName myRG -Tag @{'key1' = '1'; 'key2' = 2; 'key3' = 3}
```

```Output
PS C:\>Update-AzDedicatedHsm -Name  hsm-n7wfxi -ResourceGroupName dedicatedhsm-rg-n359cz -Tag @{'key1' = '1';
'key2' = 2; 'key3' = 3}

Name       Provisioning State SKU                           Location
----       ------------------ ---                           --------
myhsm      Succeeded          SafeNet Luna Network HSM A790 westus
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die in diesem Artikel erstellten Ressourcen nicht benötigt werden, können Sie sie löschen, indem Sie die folgenden Beispiele ausführen.

### <a name="remove-a-dedicated-hsm"></a>Entfernen eines dedizierten HSM

Verwenden Sie zum Entfernen eines dedizierten HSM das Cmdlet [Remove-AzDedicatedHsm](/powershell/module/az.dedicatedhsm/remove-azdedicatedhsm). Im folgenden Beispiel wird das angegebene dedizierte HSM gelöscht:

```azurepowershell-interactive
Remove-AzDedicatedHsm -Name hsm-7t2xaf -ResourceGroupName lucas-manual-test
```

### <a name="delete-the-resource-group"></a>Löschen der Ressourcengruppe

> [!CAUTION]
> Im folgenden Beispiel werden die angegebene Ressourcengruppe und alle darin enthaltenen Ressourcen gelöscht.
> Falls in der angegebenen Ressourcengruppe Ressourcen enthalten sind, die nicht zum Umfang dieses Artikels gehören, werden sie ebenfalls gelöscht.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myRG
```

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich ausführlicher über [Azure Dedicated HSM](overview.md).
