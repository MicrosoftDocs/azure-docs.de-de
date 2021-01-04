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
ms.openlocfilehash: 3fe622d2ff4f6f8aff546452db0f475cfd44eb1b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015372"
---
## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

*Wenn Sie bereits über eine Ressourcengruppe verfügen, können Sie mit dem [Erstellen eines Schlüsseltresors](#create-a-key-vault) fortfahren.*

Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. 

Erstellen Sie eine Ressourcengruppe mit dem Azure CLI-Befehl [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create), dem Azure PowerShell-Befehl [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) oder dem [Azure-Portal](https://portal.azure.com).

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Erstellen eines Schlüsseltresors

*Wenn Sie bereits über einen Schlüsseltresor verfügen, können Sie mit dem [Festlegen von erweiterten Zugriffsrichtlinien für Schlüsseltresore](#set-key-vault-advanced-access-policies) fortfahren.*

Erstellen Sie einen Schlüsseltresor mit dem Azure CLI-Befehl [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create), dem Azure PowerShell-Befehl [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault), dem [Azure-Portal](https://portal.azure.com) oder einer [Resource Manager-Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

>[!WARNING]
> Der Schlüsseltresor und die VMs müssen sich im selben Abonnement befinden. Um sicherzustellen, dass die Verschlüsselungsgeheimnisse die Regionsgrenzen nicht verlassen, müssen sich der Schlüsseltresor und die VMs außerdem für Azure Disk Encryption in derselben Region befinden. Erstellen und verwenden Sie einen Schlüsseltresor, der sich im selben Abonnement und derselben Region wie die zu verschlüsselnden VMs befindet. 

Jeder Schlüsseltresor muss einen eindeutigen Namen haben. Ersetzen Sie in den folgenden Beispielen „<your-unique-keyvault-name>“ durch den Namen Ihres Schlüsseltresors.

### <a name="azure-cli"></a>Azure CLI

Fügen Sie beim Erstellen eines Schlüsseltresors mithilfe der Azure-Befehlszeilenschnittstelle das Flag „--enabled-for-disk-encryption“ hinzu.

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

Fügen Sie beim Erstellen eines Schlüsseltresors mithilfe von Azure-PowerShell das Flag „-EnabledForDiskEncryption“ hinzu.

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Resource Manager-Vorlage

Sie können auch mit der [Resource Manager-Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create) einen Schlüsseltresor erstellen.

1. Klicken Sie in der Azure-Schnellstartvorlage auf **Deploy to Azure** (In Azure bereitstellen).
2. Wählen Sie Abonnement, Ressourcengruppe, Ressourcengruppenstandort, Schlüsseltresorname, Objekt-ID, rechtliche Bedingungen und Vereinbarung aus, und klicken Sie auf **Kaufen**. 


##  <a name="set-key-vault-advanced-access-policies"></a>Festlegen von erweiterten Zugriffsrichtlinien für Schlüsseltresore

Die Azure-Plattform benötigt Zugriff auf die Verschlüsselungsschlüssel oder geheimen Schlüssel in Ihrem Schlüsseltresor, um sie für den virtuellen Computer zur Verfügung zu stellen, damit die Volumes gestartet und entschlüsselt werden können. 

Wenn Sie Ihren Schlüsseltresor nicht zum Zeitpunkt der Erstellung für Datenträgerverschlüsselung, Bereitstellung oder Vorlagenbereitstellung aktiviert haben (wie im vorherigen Schritt gezeigt), müssen Sie die erweiterten Zugriffsrichtlinien aktualisieren.  

### <a name="azure-cli"></a>Azure CLI

Verwenden Sie [az keyvault update](/cli/azure/keyvault#az-keyvault-update), um die Datenträgerverschlüsselung für den Schlüsseltresor zu aktivieren. 

 - **Aktivieren von Key Vault für die Datenträgerverschlüsselung:** „Enabled-for-disk-encryption“ ist erforderlich. 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Aktivieren von Key Vault für die Bereitstellung (falls erforderlich):** Der Ressourcenanbieter „Microsoft.Compute“ wird aktiviert, um Geheimnisse aus diesem Schlüsseltresor abzurufen, wenn dieser Schlüsseltresor bei der Ressourcenerstellung (z. B. beim Erstellen einer VM) referenziert wird.

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Aktivieren von Key Vault für die Vorlagenbereitstellung (falls erforderlich):** Der Resource Manager kann Geheimnisse aus dem Tresor abrufen.
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 Verwenden Sie das PowerShell-Cmdlet für Schlüsseltresore [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy), um die Datenträgerverschlüsselung für den Schlüsseltresor zu aktivieren.

  - **Aktivieren von Key Vault für die Datenträgerverschlüsselung:** „EnabledForDiskEncryption“ ist für die Verwendung von Azure Disk Encryption erforderlich.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **Aktivieren von Key Vault für die Bereitstellung (falls erforderlich):** Der Ressourcenanbieter „Microsoft.Compute“ wird aktiviert, um Geheimnisse aus diesem Schlüsseltresor abzurufen, wenn dieser Schlüsseltresor bei der Ressourcenerstellung (z. B. beim Erstellen einer VM) referenziert wird.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **Aktivieren von Key Vault für die Vorlagenbereitstellung (falls erforderlich):** Azure Resource Manager kann aus diesem Schlüsseltresor Geheimnisse abrufen, wenn dieser Schlüsseltresor in einer Vorlagenbereitstellung referenziert wird.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Azure-Portal

1. Wählen Sie Ihren Schlüsseltresor aus, navigieren Sie zu **Zugriffsrichtlinien**, und **klicken Sie, um erweiterte Zugriffsrichtlinien anzuzeigen**.
2. Wählen Sie das Feld **Zugriff auf Azure Disk Encryption für Volumeverschlüsselung aktivieren** aus.
3. Wählen Sie ggf. **Zugriff auf Azure Virtual Machines für Bereitstellung aktivieren** und/oder **Zugriff auf Azure Resource Manager für Vorlagenbereitstellung aktivieren** aus. 
4. Klicken Sie auf **Speichern**.

    ![Erweiterte Zugriffsrichtlinien für Schlüsseltresore in Azure](../articles/virtual-machines/media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>Einrichten eines Schlüssels zur Schlüsselverschlüsselung (Key Encryption Key, KEK)

Wenn Sie Verschlüsselungsschlüssel mit einem Schlüssel für die Schlüsselverschlüsselung zusätzlich schützen möchten, fügen Sie Ihrem Schlüsseltresor einen Schlüsselverschlüsselungsschlüssel hinzu. Wenn ein Schlüsselverschlüsselungsschlüssel angegeben wird, verwendet Azure Disk Encryption diesen, um Verschlüsselungsgeheimnisse vor dem Schreiben in Key Vault zu umschließen.

Sie können einen neuen KEK mit dem Azure CLI-Befehl [az keyvault key create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create), dem Azure PowerShell-Cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) oder dem [Azure-Portal](https://portal.azure.com/) generieren. Sie müssen einen Schlüssel vom Typ RSA generieren, da Azure Disk Encryption noch nicht die Verwendung von Elliptic Curve-Schlüsseln unterstützt.

Sie können den KEK auch aus Ihrem lokalen Hardwaresicherheitsmodul (HSM) für die Schlüsselverwaltung importieren. Weitere Informationen finden Sie in der [Key Vault](../articles/key-vault/keys/hsm-protected-keys.md)-Dokumentation.

Ihre URLs für den KEK müssen mit einer Versionsangabe versehen sein. Azure erzwingt diese Einschränkung der Versionsverwaltung. Gültige URLs für Geheimnisse und KEKs finden Sie in den folgenden Beispielen:

* Beispiel für eine gültige Geheimnis-URL: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* Beispiel für eine gültige KEK-URL: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Die Angabe von Portnummern als Teil von URLs für Schlüsseltresorgeheimnisse und KEK-URLs wird von Azure Disk Encryption nicht unterstützt. Beispiele für nicht unterstützte und unterstützte Schlüsseltresor-URLs finden Sie hier:

  * Zulässige Schlüsseltresor-URL: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Unzulässige Schlüsseltresor-URL: *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Azure CLI

Generieren Sie mit dem Azure CLI-Befehl [az keyvault key create](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-create) einen neuen KEK, und speichern Sie ihn in Ihrem Schlüsseltresor.

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA
```

Sie können stattdessen auch einen privaten Schlüssel mithilfe des Befehls [az keyvault key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) in der Azure-Befehlszeilenschnittstelle importieren:

In beiden Fällen geben Sie den Namen des KEK im Parameter „--key-encryption-key“ für den Azure CLI-Befehl [az vm encryption enable](/cli/azure/vm/encryption?view=azure-cli-latest#az-vm-encryption-enable) an. 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Generieren Sie mit dem Azure PowerShell-Cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey?view=azps-2.5.0) einen neuen KEK, und speichern Sie ihn in Ihrem Schlüsseltresor.

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

Sie können stattdessen auch einen privaten Schlüssel mithilfe des Azure PowerShell-Befehls [az keyvault key import](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-import) importieren.

In beiden Fällen geben Sie die ID Ihres KEK-Schlüsseltresors und die URL Ihres KEK in den Parametern „-KeyEncryptionKeyVaultId“ und „-KeyEncryptionKeyUrl“ des Azure PowerShell-Befehls [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension?view=azps-2.5.0) an. Beachten Sie, dass in diesem Beispiel davon ausgegangen wird, dass Sie für den Datenträger-Verschlüsselungsschlüssel und den KEK denselben Schlüsseltresor verwenden.

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```