---
title: 'Schnellstart: Festlegen und Abrufen eines Geheimnisses aus Key Vault mit PowerShell'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe von Azure PowerShell Geheimnisse in einer Azure Key Vault-Instanz erstellen, daraus abrufen und löschen.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, devx-track-azurepowershell
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: f3b770a5790d5e9554c7bf5d7d24f1eeccff7662
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99072218"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-powershell"></a>Schnellstart: Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Azure Key Vault mithilfe von PowerShell

Azure Key Vault ist ein Clouddienst, der als sicherer Geheimnisspeicher fungiert. Dadurch können Schlüssel, Kennwörter, Zertifikate und andere Geheimnisse sicher gespeichert werden. Weitere Informationen zu Key Vault finden Sie in der [Übersicht](../general/overview.md). In dieser Schnellstartanleitung erstellen Sie einen Schlüsseltresor mithilfe von PowerShell. Anschließend speichern Sie ein Geheimnis in dem neu erstellten Tresor.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial mindestens die Version 5.0.0 des Azure PowerShell-Moduls verwenden. Geben Sie `$PSVersionTable.PSVersion` ein, um die Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>Erstellen eines Schlüsseltresors

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="give-your-user-account-permissions-to-manage-secrets-in-key-vault"></a>Gewähren von Berechtigungen zum Verwalten von Geheimnissen in Key Vault für Ihr Benutzerkonto

Verwenden Sie das Azure PowerShell-Cmdlet „Set-AzKeyVaultAccessPolicy“, um die Key Vault-Zugriffsrichtlinie zu aktualisieren und Ihrem Benutzerkonto Berechtigungen für Geheimnisse zu gewähren.

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -UserPrincipalName "user@domain.com" -PermissionsToSecrets get,set,delete
```

## <a name="adding-a-secret-to-key-vault"></a>Hinzufügen eines Geheimnisses zu Key Vault

Ein Geheimnis kann dem Tresor mit wenigen Schritten hinzugefügt werden. In diesem Fall wird ein Kennwort hinzugefügt, das von einer Anwendung verwendet werden kann. Das Kennwort heißt **ExamplePassword** und enthält den Wert **hVFkk965BuUv**.

Geben Sie zunächst Folgendes ein, um den Wert **hVFkk965BuUv** in eine sichere Zeichenfolge zu konvertieren:

```azurepowershell-interactive
$secretvalue = ConvertTo-SecureString "hVFkk965BuUv" -AsPlainText -Force
```

Geben Sie dann die folgenden PowerShell-Befehle ein, um in Key Vault ein Geheimnis namens **ExamplePassword** mit dem Wert **hVFkk965BuUv** zu erstellen:


```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName "<your-unique-keyvault-name>" -Name "ExamplePassword" -SecretValue $secretvalue
```

## <a name="retrieve-a-secret-from-key-vault"></a>Abrufen eines Geheimnisses aus Key Vault

Geben Sie Folgendes ein, um den Wert im Geheimnis als Nur-Text anzuzeigen:

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName "<your-unique-keyvault-name>" -Name "ExamplePassword"
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)
try {
   $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)
} finally {
   [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)
}
Write-Output $secretValueText
```

Sie haben nun eine Key Vault-Instanz erstellt sowie ein Geheimnis gespeichert und abgerufen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

 Andere Schnellstartanleitungen und Tutorials in dieser Sammlung bauen auf dieser Schnellstartanleitung auf. Falls Sie mit weiteren Schnellstartanleitungen und Tutorials fortfahren möchten, sollten Sie die Ressourcen nicht bereinigen.

Wenn Sie die Ressourcen nicht mehr benötigen, führen Sie den Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) aus, um die Ressourcengruppe, die Key Vault-Instanz und alle dazugehörigen Ressourcen zu löschen.

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Key Vault-Instanz erstellt und ein Geheimnis darin gespeichert. Weitere Informationen zu Key Vault und zur Integration in Ihre Anwendungen finden Sie in den folgenden Artikeln:

- [Was ist der Azure-Schlüsseltresor?](../general/overview.md)
- Sehen Sie sich die Referenz zu den [Azure PowerShell-Cmdlets für Key Vault](/powershell/module/az.keyvault/#key_vault) an.
- [Azure Key Vault-Sicherheitsübersicht](../general/security-overview.md)
