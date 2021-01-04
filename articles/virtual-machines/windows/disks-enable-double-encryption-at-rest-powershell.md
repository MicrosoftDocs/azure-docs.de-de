---
title: 'Azure PowerShell: Aktivieren der doppelten Verschlüsselung für ruhende Daten – verwaltete Datenträger'
description: Hier erfahren Sie, wie Sie die doppelte Verschlüsselung für ruhende Daten auf Ihren verwalteten Datenträgern mithilfe von Azure PowerShell aktivieren können.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-windows
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: c231aab47f154b6150368b504872d87317c790cc
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93359951"
---
# <a name="use-the-azure-powershell-module-to-enable-double-encryption-at-rest-for-managed-disks"></a>Verwenden des Azure PowerShell-Moduls zum Aktivieren der doppelten Verschlüsselung von ruhenden Daten auf verwalteten Datenträgern

Azure Disk Storage unterstützt die doppelte Verschlüsselung von ruhenden Daten auf verwalteten Datenträgern. Informationen zum Konzept der doppelten Verschlüsselung ruhender Daten sowie weitere Typen der Verschlüsselung von Daten auf verwalteten Datenträgern finden Sie im Abschnitt [Doppelte Verschlüsselung ruhender Daten](../disk-encryption.md#double-encryption-at-rest) des Artikels zur Datenträgerverschlüsselung.

## <a name="prerequisites"></a>Voraussetzungen

Installieren Sie die aktuelle [Azure PowerShell-Version](/powershell/azure/install-az-ps), und melden Sie sich in einem Azure-Konto mithilfe von [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-4.3.0) an.

## <a name="getting-started"></a>Erste Schritte

1. Erstellen Sie eine Azure Key Vault-Instanz und den Verschlüsselungsschlüssel.

    Beim Erstellen der Key Vault-Instanz müssen Sie vorläufiges Löschen und den Schutz vor endgültigem Löschen aktivieren. Durch vorläufiges Löschen wird sichergestellt, dass der Schlüsseltresor einen gelöschten Schlüssel für einen bestimmten Aufbewahrungszeitraum (standardmäßig 90 Tage) speichert. Der Schutz vor endgültigem Löschen stellt sicher, dass ein gelöschter Schlüssel erst nach Ablauf der Aufbewahrungsdauer dauerhaft gelöscht werden kann. Diese Einstellungen schützen Sie vor dem Verlust von Daten durch versehentliches Löschen. Diese Einstellungen sind obligatorisch, wenn ein Schlüsseltresor für die Verschlüsselung verwalteter Datenträger verwendet wird.
    
    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westus2"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName -ResourceGroupName $ResourceGroupName -Location $LocationName -EnableSoftDelete -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName -Destination $keyDestination  
    ```

1.  Erstellen Sie eine DiskEncryptionSet-Klasse. Legen Sie encryptionType dabei auf EncryptionAtRestWithPlatformAndCustomerKeys fest. Verwenden Sie die API-Version **2020-05-01** in der Azure Resource Manager-Vorlage (ARM). 
    
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/DoubleEncryption/CreateDiskEncryptionSetForDoubleEncryption.json" `
    -diskEncryptionSetName $diskEncryptionSetName `
    -keyVaultId $keyVault.ResourceId `
    -keyVaultKeyUrl $key.Key.Kid `
    -encryptionType "EncryptionAtRestWithPlatformAndCustomerKeys" `
    -region $LocationName
    ```

1. Gewähren Sie der DiskEncryptionSet-Ressource Zugriff auf den Schlüsseltresor.

    > [!NOTE]
    > Es kann einige Minuten dauern, bis Azure die Identität des Datenträgerverschlüsselungssatzes in Azure Active Directory erstellt hat. Wenn Sie bei der Ausführung des folgenden Befehls einen ähnlichen Fehler wie „Active Directory-Objekt kann nicht gefunden werden“ erhalten, warten Sie einige Minuten, und versuchen Sie es erneut.

    ```powershell  
    $des=Get-AzDiskEncryptionSet -name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
    ```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie diese Ressourcen erstellt und konfiguriert haben, können Sie diese zum Sichern Ihrer verwalteten Datenträger verwenden. Der folgende Link enthält Beispielskripts mit jeweils einem entsprechenden Szenario, die Sie zum Sichern Ihrer verwalteten Datenträger verwenden können.

- [Azure PowerShell: Aktivieren kundenseitig verwalteter Schlüssel mit serverseitiger Verschlüsselung – verwaltete Datenträger](disks-enable-customer-managed-keys-powershell.md)
- [Beispiele für Azure Resource Manager-Vorlagen](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)