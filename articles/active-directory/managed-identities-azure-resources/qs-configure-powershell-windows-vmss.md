---
title: Konfigurieren von verwalteten Identitäten für Azure-Ressourcen in einer VM-Skalierungsgruppe mit PowerShell
description: Ausführliche Anweisungen zum Konfigurieren von system- und benutzerseitig zugewiesenen verwalteten Identitäten in einer VM-Skalierungsgruppe mit PowerShell
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4917720af2396b68ccd36cc0410c9acbbba2d9b2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60304600"
---
# <a name="configure-managed-identities-for-azure-resources-on-virtual-machine-scale-sets-using-powershell"></a>Konfigurieren von verwalteten Identitäten für Azure-Ressourcen in einer VM-Skalierungsgruppe mit PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Verwaltete Identitäten für Azure-Ressourcen stellen für Azure-Dienste eine automatisch verwaltete Identität in Azure Active Directory bereit. Sie können diese Identität für die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein. 

In diesem Artikel erfahren Sie, wie Sie mit PowerShell die folgenden Vorgänge für verwaltete Identitäten für Azure-Ressourcen in einer VM-Skalierungsgruppe ausführen können:
- Aktivieren und Deaktivieren der vom System zugewiesenen verwalteten Identität in einer VM-Skalierungsgruppe
- Hinzufügen und Entfernen einer vom Benutzer zugewiesenen verwalteten Identität in einer VM-Skalierungsgruppe

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie nicht mit verwalteten Identitäten für Azure-Ressourcen vertraut sind, helfen Ihnen die Informationen in der [Übersicht](overview.md) weiter. **Machen Sie sich den [Unterschied zwischen einer vom System und einer vom Benutzer zugewiesenen verwalteten Identität](overview.md#how-does-it-work)** bewusst.
- Wenn Sie noch kein Azure-Konto haben, sollten Sie sich [für ein kostenloses Konto registrieren](https://azure.microsoft.com/free/), bevor Sie fortfahren.
- Um die Verwaltungsvorgänge in diesem Artikel auszuführen, benötigt Ihr Konto die folgenden Zuweisungen der rollenbasierten Azure-Zugriffssteuerung:

    > [!NOTE]
    > Es sind keine weiteren Azure AD-Verzeichnisrollenzuweisungen erforderlich.

    - [Mitwirkender für virtuelle Computer](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor), um eine VM-Skalierungsgruppe zu erstellen und die vom System und/oder Benutzer zugewiesene verwaltete Identität auf einer VM-Skalierungsgruppe zu aktivieren bzw. daraus zu entfernen
    - [Mitwirkender für verwaltete Identität](/azure/role-based-access-control/built-in-roles#managed-identity-contributor), um eine vom Benutzer zugewiesene verwaltete Identität zu erstellen
    - [Operator für verwaltete Identität](/azure/role-based-access-control/built-in-roles#managed-identity-operator), um eine vom Benutzer zugewiesene verwaltete Identität einer VM-Skalierungsgruppe zuzuweisen bzw. daraus zu entfernen
- Installieren Sie [die aktuelle Version von Azure PowerShell](/powershell/azure/install-az-ps), sofern noch nicht geschehen. 

## <a name="system-assigned-managed-identity"></a>Systemseitig zugewiesene verwaltete Identität

In diesem Abschnitt erfahren Sie, wie Sie mithilfe von Azure PowerShell eine vom System zugewiesene verwaltete Identität aktivieren und entfernen.

### <a name="enable-system-assigned-managed-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Aktivieren der vom System zugewiesenen verwalteten Identität bei der Erstellung einer Azure-VM-Skalierungsgruppe

So erstellen Sie eine VM-Skalierungsgruppe samt aktivierter vom System zugewiesener verwalteter Identität

1. Informationen zum Erstellen einer VM-Skalierungsgruppe mit der vom System zugewiesenen verwalteten Identität finden Sie im *ersten Beispiel* des Referenzartikels für das Cmdlet [New-AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig).  Fügen Sie den Parameter `-IdentityType SystemAssigned` zum `New-AzVmssConfig`-Cmdlet hinzu:

    ```powershell
    $VMSS = New-AzVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```
> [!NOTE]
> Sie können optional die verwalteten Identitäten für die VM-Skalierungsgruppe von Azure-Ressourcen bereitstellen, die jedoch bald veraltet sein werden. Es wird empfohlen, den Azure Instance Metadata-Identitätsendpunkt für die Authentifizierung zu verwenden. Weitere Informationen finden Sie unter [Beenden der Verwendung der VM-Erweiterung und Starten der Verwendung des Azure IMDS-Endpunkts für die Authentifizierung](howto-migrate-vm-extension.md).


## <a name="enable-system-assigned-managed-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Aktivieren einer vom System zugewiesenen verwalteten Identität in einer vorhandenen Azure-VM-Skalierungsgruppe

Wenn Sie eine vom System zugewiesene verwaltete Identität in einer vorhandenen Azure-VM-Skalierungsgruppe aktivieren müssen, gehen Sie wie folgt vor:

1. Melden Sie sich mit `Connect-AzAccount` bei Azure an. Verwenden Sie ein Konto, das dem Azure-Abonnement zugeordnet ist, das die VM-Skalierungsgruppe enthält. Stellen Sie außerdem sicher, dass Ihr Konto zu einer Rolle gehört, die Ihnen Schreibberechtigungen auf der VM-Skalierungsgruppe erteilt, z.B. „Mitwirkender für virtuelle Computer“:

   ```powershell
   Connect-AzAccount
   ```

2. Rufen Sie zuerst mit dem [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss)-Cmdlet die Eigenschaften der VM-Skalierungsgruppe ab. Verwenden Sie dann den Schalter `-IdentityType` im Cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss), um die vom System zugewiesene verwaltete Identität zu aktivieren:

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```

> [!NOTE]
> Sie können optional die verwalteten Identitäten für die VM-Skalierungsgruppe von Azure-Ressourcen bereitstellen, die jedoch bald veraltet sein werden. Es wird empfohlen, den Azure Instance Metadata-Identitätsendpunkt für die Authentifizierung zu verwenden. Weitere Informationen finden Sie unter [Migrieren von der VM-Erweiterung zum Azure IMDS-Endpunkt für die Authentifizierung](howto-migrate-vm-extension.md).

### <a name="disable-the-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Deaktivieren der vom System zugewiesenen verwalteten Identität in einer Azure-VM-Skalierungsgruppe

Wenn Sie über eine VM-Skalierungsgruppe verfügen, die nicht mehr die vom System zugewiesene verwaltete Identität, jedoch weiterhin vom Benutzer zugewiesene verwaltete Identitäten benötigt, verwenden Sie das folgende Cmdlet:

1. Melden Sie sich mit `Connect-AzAccount` bei Azure an. Verwenden Sie ein Konto, das dem Azure-Abonnement zugeordnet ist, das den virtuellen Computer enthält. Stellen Sie außerdem sicher, dass Ihr Konto zu einer Rolle gehört, die Ihnen Schreibberechtigungen auf der VM-Skalierungsgruppe erteilt, z.B. „Mitwirkender für virtuelle Computer“:

2. Führen Sie das folgende Cmdlet aus:

   ```powershell
   Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

Bei einer VM-Skalierungsgruppe, die nicht mehr die vom System zugewiesene verwaltete Identität benötigt und über keine vom Benutzer zugewiesenen verwalteten Identitäten verfügt, verwenden Sie die folgenden Befehle:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-managed-identity"></a>Benutzerseitig zugewiesene verwaltete Identität

In diesem Abschnitt erfahren Sie, wie Sie mithilfe von Azure PowerShell eine vom Benutzer zugewiesene verwaltete Identität einer VM-Skalierungsgruppe hinzufügen und aus dieser entfernen.

### <a name="assign-a-user-assigned-managed-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Zuweisen einer vom Benutzer zugewiesenen verwalteten Identität bei der Erstellung einer Azure-VM-Skalierungsgruppe

Das Erstellen einer neuen VM-Skalierungsgruppe mit einer vom Benutzer zugewiesenen verwalteten Identität wird für PowerShell derzeit nicht unterstützt. Informationen dazu, wie Sie einer vorhandenen VM-Skalierungsgruppe eine vom Benutzer zugewiesene verwaltete Identität hinzufügen, finden Sie im nächsten Abschnitt. Überprüfen Sie zu einem späteren Zeitpunkt auf dieser Seite, ob neue Informationen vorliegen.

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Zuweisen einer vom Benutzer zugewiesenen verwalteten Identität zu einer vorhandenen Azure-VM-Skalierungsgruppe

So weisen Sie eine vom Benutzer zugewiesene verwaltete Identität einer vorhandenen Azure-VM-Skalierungsgruppe zu

1. Melden Sie sich mit `Connect-AzAccount` bei Azure an. Verwenden Sie ein Konto, das dem Azure-Abonnement zugeordnet ist, das die VM-Skalierungsgruppe enthält. Stellen Sie außerdem sicher, dass Ihr Konto zu einer Rolle gehört, die Ihnen Schreibberechtigungen auf der VM-Skalierungsgruppe erteilt, z.B. „Mitwirkender für virtuelle Computer“:

   ```powershell
   Connect-AzAccount
   ```

2. Rufen Sie zuerst mit dem `Get-AzVM`-Cmdlet die Eigenschaften der VM-Skalierungsgruppe ab. Weisen Sie der VM-Skalierungsgruppe dann eine vom Benutzer zugewiesene verwaltete Identität zu, indem Sie die Schalter `-IdentityType` und `-IdentityID` im Cmdlet [Update-AzVmss](/powershell/module/az.compute/update-azvmss) verwenden. Ersetzen Sie `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>` und `USER ASSIGNED ID2` durch Ihre eigenen Werte.

   [!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Entfernen einer vom Benutzer zugewiesenen verwalteten Identität aus einer Azure-VM-Skalierungsgruppe

Wenn Ihre VM-Skalierungsgruppe mehrere vom Benutzer zugewiesene verwaltete Identitäten hat, können Sie mit den folgenden Befehlen alle bis auf die letzte Identität entfernen. Ersetzen Sie die Parameterwerte `<RESOURCE GROUP>` und `<VIRTUAL MACHINE SCALE SET NAME>` durch Ihre eigenen Werte. `<USER ASSIGNED IDENTITY NAME>` ist die name-Eigenschaft der vom Benutzer zugewiesenen verwalteten Identität, die für die VM-Skalierungsgruppe erhalten bleiben soll. Diese Informationen finden Sie im Identitätsabschnitt der VM-Skalierungsgruppe mithilfe von `az vmss show`:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED IDENTITY NAME>"
```
Wenn Ihre VM-Skalierungsgruppe keine vom System zugewiesene verwaltete Identität hat und Sie alle vom Benutzer zugewiesenen verwalteten Identitäten entfernen möchten, verwenden Sie den folgenden Befehl:

```powershell
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Wenn die VM-Skalierungsgruppe sowohl vom System als auch vom Benutzer zugewiesene verwaltete Identitäten aufweist, können Sie alle vom Benutzer zugewiesenen verwalteten Identitäten entfernen, indem Sie in den Modus wechseln, in dem nur die vom System zugewiesene verwaltete Identität verwendet wird.

```powershell 
Update-AzVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Nächste Schritte

- [Verwaltete Identitäten für Azure-Ressourcen – Übersicht](overview.md)
- Die vollständigen Schnellstarts zum Erstellen virtueller Azure-Computer finden Sie unter:
  
  - [Erstellen eines virtuellen Windows-Computers mit PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Erstellen eines virtuellen Linux-Computers mit PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 

















