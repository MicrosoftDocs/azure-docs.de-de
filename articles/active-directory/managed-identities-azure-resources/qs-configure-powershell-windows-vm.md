---
title: Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer mithilfe von PowerShell
description: Schrittweise Anweisungen zum Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer mithilfe von PowerShell.
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
ms.openlocfilehash: 6f76fef3d5e6515e9d546c709ace0a4a533c0a45
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66112695"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer mithilfe von PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Verwaltete Identitäten für Azure-Ressourcen stellen für Azure-Dienste eine automatisch verwaltete Identität in Azure Active Directory bereit. Sie können diese Identität für die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein. 

In diesem Artikel erfahren Sie, wie Sie mithilfe von PowerShell die folgenden Vorgänge für verwaltete Identitäten für Azure-Ressourcen auf einer Azure-VM ausführen.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie nicht mit verwalteten Identitäten für Azure-Ressourcen vertraut sind, helfen Ihnen die Informationen in der [Übersicht](overview.md) weiter. **Machen Sie sich den [Unterschied zwischen einer vom System und einer vom Benutzer zugewiesenen verwalteten Identität](overview.md#how-does-it-work)** bewusst.
- Wenn Sie noch kein Azure-Konto haben, sollten Sie sich [für ein kostenloses Konto registrieren](https://azure.microsoft.com/free/), bevor Sie fortfahren.
- Installieren Sie [die aktuelle Version von Azure PowerShell](/powershell/azure/install-az-ps), sofern noch nicht geschehen.

## <a name="system-assigned-managed-identity"></a>Systemseitig zugewiesene verwaltete Identität

In diesem Abschnitt erfahren Sie, wie Sie die vom System zugewiesene verwaltete Identität über Azure PowerShell aktivieren und deaktivieren.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Aktivieren einer vom System zugewiesenen verwalteten Identität beim Erstellen eines virtuellen Azure-Computers

Zum Erstellen eines virtuellen Azure-Computers, auf dem die systemseitig zugewiesene verwaltete Identität aktiviert ist, benötigt Ihr Konto die Rollenzuweisung [Mitwirkender für virtuelle Computer](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Es sind keine weiteren Azure AD-Verzeichnisrollenzuweisungen erforderlich.

1. Verwenden Sie einen der folgenden Schnellstarts für virtuelle Azure-Computer, und setzen Sie nur die erforderlichen Abschnitte um („Anmelden bei Azure“, „Erstellen einer Ressourcengruppe“, „Erstellen einer Netzwerkgruppe“, „Erstellen des virtuellen Computers“).
    
    Im Abschnitt „Erstellen der VM“ nehmen Sie eine kleine Änderung an der Syntax des Cmdlets [New-AzVMConfig](/powershell/module/az.compute/new-azvm) vor. Fügen Sie einen `-AssignIdentity:$SystemAssigned`-Parameter hinzu, um den virtuellen Computer mit aktivierter systemzugewiesener Identität bereitzustellen. Beispiel:
      
    ```powershell
    $vmConfig = New-AzVMConfig -VMName myVM -AssignIdentity:$SystemAssigned ...
    ```

   - [Erstellen eines virtuellen Windows-Computers mithilfe von PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Erstellen eines virtuellen Linux-Computers mithilfe von PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

> [!NOTE]
> Sie können optional die verwalteten Identitäten für die VM-Erweiterung von Azure-Ressourcen bereitstellen, die jedoch bald veraltet sein werden. Es wird empfohlen, den Azure Instance Metadata-Identitätsendpunkt für die Authentifizierung zu verwenden. Weitere Informationen finden Sie unter [Migrieren von der VM-Erweiterung zum Azure IMDS-Endpunkt für die Authentifizierung](howto-migrate-vm-extension.md).

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Aktivieren einer vom System zugewiesenen verwalteten Identität auf einem vorhandenen virtuellen Azure-Computer

Zum Aktivieren der systemseitig zugewiesenen verwalteten Identität auf einem virtuellen Computer, der ursprünglich ohne diese bereitgestellt wurde, benötigt Ihr Konto die Rollenzuweisung [Mitwirkender für virtuelle Computer](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Es sind keine weiteren Azure AD-Verzeichnisrollenzuweisungen erforderlich.

1. Melden Sie sich mit `Connect-AzAccount` bei Azure an. Verwenden Sie ein Konto, das dem Azure-Abonnement zugeordnet ist, das den virtuellen Computer enthält.

   ```powershell
   Connect-AzAccount
   ```

2. Rufen Sie zunächst die VM-Einstellungen mithilfe des Cmdlets `Get-AzVM` ab. Verwenden Sie dann zum Aktivieren einer vom System zugewiesenen verwalteten Identität den Schalter `-AssignIdentity` im Cmdlet [Update-AzVM](/powershell/module/az.compute/update-azvm):

   ```powershell
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity:$SystemAssigned
   ```

> [!NOTE]
> Sie können optional die verwalteten Identitäten für die VM-Erweiterung von Azure-Ressourcen bereitstellen, die jedoch bald veraltet sein werden. Es wird empfohlen, den Azure Instance Metadata-Identitätsendpunkt für die Authentifizierung zu verwenden. Weitere Informationen finden Sie unter [Migrieren von der VM-Erweiterung zum Azure IMDS-Endpunkt für die Authentifizierung](howto-migrate-vm-extension.md).

### <a name="add-vm-system-assigned-identity-to-a-group"></a>Hinzufügen einer systemseitig zugewiesenen Identität einer VM zu einer Gruppe

Nachdem Sie auf einer VM eine systemseitig zugewiesene Identität aktiviert haben, können Sie diese einer Gruppe hinzufügen.  Mit dem folgenden Verfahren wird die systemseitig zugewiesene Identität einer VM einer Gruppe hinzugefügt.

1. Melden Sie sich mit `Connect-AzAccount` bei Azure an. Verwenden Sie ein Konto, das dem Azure-Abonnement zugeordnet ist, das den virtuellen Computer enthält.

   ```powershell
   Connect-AzAccount
   ```

2. Rufen Sie die `ObjectID` des VM-Dienstprinzipals (gemäß den Angaben im Feld `Id` der zurückgegebenen Werte) ab, und notieren Sie sie:

   ```powerhshell
   Get-AzADServicePrincipal -displayname "myVM"
   ```

3. Rufen Sie die `ObjectID` der Gruppe (gemäß den Angaben im Feld `Id` der zurückgegebenen Werte) ab, und notieren Sie sie:

   ```powershell
   Get-AzADGroup -searchstring "myGroup"
   ```

4. Fügen Sie den Dienstprinzipal der VM der Gruppe hinzu:

   ```powershell
   Add-AzureADGroupMember -ObjectId "<objectID of group>" -RefObjectId "<object id of VM service principal>"
   ```

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Deaktivieren einer vom System zugewiesenen verwalteten Identität auf einem virtuellen Azure-Computer

Zum Deaktivieren der systemseitig zugewiesenen verwalteten Identität auf einem virtuellen Computer benötigt Ihr Konto die Rollenzuweisung [Mitwirkender für virtuelle Computer](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Es sind keine weiteren Azure AD-Verzeichnisrollenzuweisungen erforderlich.

Wenn Sie über einen virtuellen Computer verfügen, der nicht mehr die systemzugewiesene verwaltete Identität, jedoch weiterhin benutzerzugewiesene verwaltete Identitäten benötigt, verwenden Sie das folgende Cmdlet:

1. Melden Sie sich mit `Connect-AzAccount` bei Azure an. Verwenden Sie ein Konto, das dem Azure-Abonnement zugeordnet ist, das den virtuellen Computer enthält.

   ```powershell
   Connect-AzAccount
   ```

2. Rufen Sie die VM-Eigenschaften mithilfe des Cmdlets `Get-AzVM` ab, und legen Sie für den `-IdentityType`-Parameter `UserAssigned` fest:

   ```powershell   
   $vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM 
   Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

Bei einem virtuellen Computer, der die systemzugewiesene verwaltete Identität nicht mehr benötigt und über keine benutzerzugewiesenen verwalteten Identitäten verfügt, verwenden Sie die folgenden Befehle:

```powershell
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```

> [!NOTE]
> Wenn Sie die verwaltete Identität für die VM-Erweiterung für Azure-Ressourcen (bald veraltet) bereitgestellt haben, müssen Sie diese mithilfe von [Remove-AzVMExtension](/powershell/module/az.compute/remove-azvmextension) entfernen. Weitere Informationen finden Sie unter [Migrieren von der VM-Erweiterung zu Azure IMDS für die Authentifizierung](howto-migrate-vm-extension.md).

## <a name="user-assigned-managed-identity"></a>Benutzerseitig zugewiesene verwaltete Identität

In diesem Abschnitt erfahren Sie, wie Sie mithilfe von Azure PowerShell eine benutzerzugewiesene verwaltete Identität zu einem virtuellen Computer hinzufügen und von diesem entfernen.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>Zuweisen einer vom Benutzer zugewiesenen verwalteten Identität zu einem virtuellen Computer bei der Erstellung

Für die Zuweisung einer benutzerseitig zugewiesenen Identität zu einem virtuellen Computer benötigt Ihr Konto die Rollenzuweisungen [Mitwirkender für virtuelle Computer](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) und [Operator für verwaltete Identität](/azure/role-based-access-control/built-in-roles#managed-identity-operator). Es sind keine weiteren Azure AD-Verzeichnisrollenzuweisungen erforderlich.

1. Verwenden Sie einen der folgenden Schnellstarts für virtuelle Azure-Computer, und setzen Sie nur die erforderlichen Abschnitte um („Anmelden bei Azure“, „Erstellen einer Ressourcengruppe“, „Erstellen einer Netzwerkgruppe“, „Erstellen des virtuellen Computers“). 
  
    Wenn Sie zum Abschnitt zum Erstellen der VM gelangen, nehmen Sie eine kleine Änderung an der Syntax des Cmdlets [`New-AzVMConfig`](/powershell/module/az.compute/new-azvm) vor. Fügen Sie die Parameter `-IdentityType UserAssigned` und `-IdentityID` für die Bereitstellung des virtuellen Computers mit einer benutzerzugewiesenen Identität hinzu.  Ersetzen Sie `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>` und `<USER ASSIGNED IDENTITY NAME>` durch Ihre eigenen Werte.  Beispiel:
    
    ```powershell 
    $vmConfig = New-AzVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```
    
    - [Erstellen eines virtuellen Windows-Computers mithilfe von PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Erstellen eines virtuellen Linux-Computers mithilfe von PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

> [!NOTE]
> Sie können optional die verwalteten Identitäten für die VM-Erweiterung von Azure-Ressourcen bereitstellen, die jedoch bald veraltet sein werden. Es wird empfohlen, den Azure Instance Metadata-Identitätsendpunkt für die Authentifizierung zu verwenden. Weitere Informationen finden Sie unter [Migrieren von der VM-Erweiterung zum Azure IMDS-Endpunkt für die Authentifizierung](howto-migrate-vm-extension.md).

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Zuweisen einer vom Benutzer zugewiesenen verwalteten Identität zu einem vorhandenen virtuellen Azure-Computer

Für die Zuweisung einer benutzerseitig zugewiesenen Identität zu einem virtuellen Computer benötigt Ihr Konto die Rollenzuweisungen [Mitwirkender für virtuelle Computer](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) und [Operator für verwaltete Identität](/azure/role-based-access-control/built-in-roles#managed-identity-operator). Es sind keine weiteren Azure AD-Verzeichnisrollenzuweisungen erforderlich.

1. Melden Sie sich mit `Connect-AzAccount` bei Azure an. Verwenden Sie ein Konto, das dem Azure-Abonnement zugeordnet ist, das den virtuellen Computer enthält.

   ```powershell
   Connect-AzAccount
   ```

2. Erstellen Sie mit dem Cmdlet [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/new-azuserassignedidentity) eine vom Benutzer zugewiesene verwaltete Identität.  Beachten Sie die `Id` in der Ausgabe, da Sie diese im nächsten Schritt benötigen.

   > [!IMPORTANT]
   > Für die Erstellung von durch Benutzer zugewiesenen verwalteten Identitäten werden nur alphanumerische Zeichen, Unter- und Bindestriche („0-9“, „a-Z“ bzw. „A-Z“, \_ oder „-“) unterstützt. Darüber hinaus sollten Namen zwischen 3 und 128 Zeichen enthalten, damit die Zuordnung zur VM/VMSS ordnungsgemäß funktioniert. Weitere Informationen finden Sie unter [FAQs und bekannte Probleme mit der verwalteten Dienstidentität (Managed Service Identity, MSI) für Azure Active Directory](known-issues.md).

   ```powershell
   New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
3. Rufen Sie zunächst die VM-Eigenschaften mithilfe des Cmdlets `Get-AzVM` ab. Weisen Sie der Azure-VM anschließend mit den Schaltern `-IdentityType` und `-IdentityID` im Cmdlet [Update-AzVM](/powershell/module/az.compute/update-azvm) eine vom Benutzer zugewiesene verwaltete Identität zu.  Der Wert für den Parameter `-IdentityId` ist die `Id`, die Sie im vorherigen Schritt notiert haben.  Ersetzen Sie `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>` und `<USER ASSIGNED IDENTITY NAME>` durch Ihre eigenen Werte.

   > [!WARNING]
   > Um alle zuvor vom Benutzer zugewiesenen verwalteten Identitäten zu erhalten, die dem virtuellen Computer zugewiesen sind, fragen Sie die Eigenschaft `Identity` des VM-Objekts ab (z. B. `$vm.Identity`).  Wenn vom Benutzer zugewiesene verwaltete Identitäten zurückgegeben werden, nehmen Sie diese in den folgenden Befehl auf, zusammen mit der neuen vom Benutzer zugewiesenen verwalteten Identität, die Sie der VM zuweisen möchten.

   ```powershell
   $vm = Get-AzVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

> [!NOTE]
> Sie können optional die verwalteten Identitäten für die VM-Erweiterung von Azure-Ressourcen bereitstellen, die jedoch bald veraltet sein werden. Es wird empfohlen, den Azure Instance Metadata-Identitätsendpunkt für die Authentifizierung zu verwenden. Weitere Informationen finden Sie unter [Migrieren von der VM-Erweiterung zum Azure IMDS-Endpunkt für die Authentifizierung](howto-migrate-vm-extension.md).

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Entfernen einer vom Benutzer zugewiesenen verwalteten Identität von einer Azure-VM

Für das Entfernen einer benutzerseitig zugewiesenen Identität von einem virtuellen Computer benötigt Ihr Konto die Rollenzuweisung [Mitwirkender für virtuelle Computer](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).

Wenn Ihr virtueller Computer mehrere benutzerzugewiesene Identitäten umfasst, können Sie mit den folgenden Befehlen alle bis auf die letzte Identität entfernen. Ersetzen Sie die Parameterwerte `<RESOURCE GROUP>` und `<VM NAME>` durch Ihre eigenen Werte. `<USER ASSIGNED IDENTITY NAME>` ist die Namenseigenschaft der benutzerzugewiesenen verwalteten Identität, die weiterhin auf dem virtuellen Computer festgelegt sein sollte. Diese Informationen finden Sie durch Abfragen der Eigenschaft `Identity` des VM-Objekts.  Beispiel: `$vm.Identity`:

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
Wenn Ihr virtueller Computer keine systemzugewiesene verwaltete Identität hat, und Sie alle benutzerzugewiesenen verwalteten Identitäten entfernen möchten, verwenden Sie den folgenden Befehl:

```powershell
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
Wenn der virtuelle Computer sowohl system- als auch benutzerzugewiesene verwaltete Identitäten aufweist, können Sie alle benutzerzugewiesenen verwalteten Identitäten entfernen, indem Sie in den Modus wechseln, in dem nur systemzugewiesene verwaltete Identitäten verwendet werden.

```powershell 
$vm = Get-AzVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Nächste Schritte

- [Verwaltete Identitäten für Azure-Ressourcen – Übersicht](overview.md)
- Die vollständigen Schnellstarts zum Erstellen virtueller Azure-Computer finden Sie unter:
  
  - [Erstellen eines virtuellen Windows-Computers mit PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Erstellen eines virtuellen Linux-Computers mit PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 
