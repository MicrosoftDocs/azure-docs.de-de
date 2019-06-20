---
title: Konfigurieren von Kunden verwalteter Schlüssel für die Azure Storage-Verschlüsselung mithilfe von PowerShell
description: Erfahren Sie, wie Sie PowerShell verwenden, um von Kunden verwaltete Schlüssel für die Azure Storage-Verschlüsselung zu konfigurieren. Mit von Kunden verwalteten Schlüsseln können Sie Zugriffssteuerungen erstellen, rotieren, deaktivieren und widerrufen.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: be876b370cd476bee2af7d90a9f0433fd80de3b4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233679"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-powershell"></a>Konfigurieren von Kunden verwalteter Schlüssel für die Azure Storage-Verschlüsselung mithilfe von PowerShell

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

In diesem Artikel erfahren Sie, wie Sie einen Schlüsseltresor mit von Kunden verwalteten Schlüsseln mithilfe von PowerShell konfigurieren.

## <a name="assign-an-identity-to-the-storage-account"></a>Zuweisen einer Identität zum Speicherkonto

Sie müssen zunächst eine systemseitig zugewiesene verwaltete Identität zum Speicherkonto hinzufügen, um von Kunden verwaltete Schlüssel für Ihr Speicherkonto zu aktivieren. Sie verwenden diese Identität, um dem Speicherkonto die Zugriffsberechtigungen für den Schlüsseltresor zu gewähren.

Rufen Sie für das Zuweisen einer verwalteten Identität mithilfe von PowerShell [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) auf. Denken Sie daran, die Platzhalterwerte in den spitzen Klammern durch Ihre eigenen Werte zu ersetzen.

```powershell
$storageAccount = Set-AzStorageAccount -ResourceGroupName <resource_group> `
    -Name <storage-account> `
    -AssignIdentity
```

Weitere Informationen zur Konfiguration systemseitig zugewiesener verwalteter Identitäten mit PowerShell finden Sie unter [Konfigurieren von verwalteten Identitäten für Azure-Ressourcen auf einem virtuellen Azure-Computer mithilfe von PowerShell](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md).

## <a name="create-a-new-key-vault"></a>Erstellen eines neuen Schlüsseltresors

Rufen Sie zum Erstellen eines neuen Schlüsseltresors mit PowerShell [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) auf. Für den Schlüsseltresor, den Sie zum Speichern von Kunden verwalteter Schlüssel für die Azure Storage-Verschlüsselung verwenden, müssen zwei wichtige Schutzeinstellungen aktiviert sein: **Vorläufiges Löschen** und **Do Not Purge** (Nicht bereinigen). 

Denken Sie daran, die Platzhalterwerte in den spitzen Klammern durch Ihre eigenen Werte zu ersetzen. 

```powershell
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>Konfigurieren der Zugriffsrichtlinie für den Schlüsseltresor

Konfigurieren Sie als Nächstes die Zugriffsrichtlinie für den Schlüsseltresor, sodass das Speicherkonto über die Berechtigungen für den Zugriff auf diesen verfügt. In diesem Schritt verwenden Sie die verwaltete Identität, die Sie zuvor dem Speicherkonto zugewiesen haben.

Rufen Sie zum Festlegen der Zugriffsrichtlinie für den Schlüsseltresor [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) auf. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen und die in den vorherigen Beispielen definierten Variablen zu verwenden.

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>Erstellen eines neuen Schlüssels

Als Nächstes erstellen Sie einen Schlüssel im Schlüsseltresor. Rufen Sie zum Erstellen eines neuen Schlüssels [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) auf. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen und die in den vorherigen Beispielen definierten Variablen zu verwenden.

```powershell
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```

## <a name="configure-encryption-with-customer-managed-keys"></a>Konfigurieren der Verschlüsselung mit von Kunden verwalteten Schlüsseln

Die Azure Storage-Verschlüsselung verwendet standardmäßig von Microsoft verwaltete Schlüssel. In diesem Schritt konfigurieren Sie Ihr Azure Storage-Konto für von Kunden verwaltete Schlüssel und legen den Schlüssel fest, der dem Speicherkonto zugeordnet werden soll.

Rufen Sie [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) auf, um die Verschlüsselungseinstellungen des Speicherkontos zu aktualisieren. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen und die in den vorherigen Beispielen definierten Variablen zu verwenden.

```powershell
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyvaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```

## <a name="update-the-key-version"></a>Aktualisieren der Schlüsselversion

Wenn Sie eine neue Version eines Schlüssels erstellen, müssen Sie das Speicherkonto aktualisieren, damit dieses die neue Version nutzt. Rufen Sie zunächst [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey) auf, um die neueste Version des Schlüssels abzurufen. Rufen Sie dann [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) auf, um die Verschlüsselungseinstellungen des Speicherkontos zu aktualisieren, damit wie im vorherigen Abschnitt gezeigt die neue Version des Schlüssels verwendet wird.

## <a name="next-steps"></a>Nächste Schritte

- [Azure Storage encryption for data at rest (Azure Storage-Verschlüsselung für ruhende Daten)](storage-service-encryption.md) 
- [What is Azure Key Vault? (Was ist Azure Key Vault?)](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)
