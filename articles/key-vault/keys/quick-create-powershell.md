---
title: 'Erstellen und Abrufen der Attribute eines Schlüssels in Azure Key Vault: Azure PowerShell'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe von Azure PowerShell einen Schlüssel in Azure Key Vault festlegen und den Schlüssel daraus abrufen.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: adbf3080367e54147c981c8ccf0bb6236111b8c7
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071204"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-powershell"></a>Schnellstart: Festlegen eines Schlüssels und Abrufen des Schlüssels aus Azure Key Vault mit Azure PowerShell

In dieser Schnellstartanleitung erstellen Sie mit Azure PowerShell einen Schlüsseltresor in Azure Key Vault. Azure Key Vault ist ein Clouddienst, der als sicherer Geheimnisspeicher fungiert. Dadurch können Schlüssel, Kennwörter, Zertifikate und andere Geheimnisse sicher gespeichert werden. Weitere Informationen zu Key Vault finden Sie in der [Übersicht](../general/overview.md). Mit Azure PowerShell können Azure-Ressourcen mithilfe von Befehlen oder Skripts erstellt und verwaltet werden. Danach speichern Sie einen Schlüssel.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial mindestens die Version 1.0.0 des Azure PowerShell-Moduls verwenden. Geben Sie `$PSVersionTable.PSVersion` ein, um die Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Login-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>Erstellen eines Schlüsseltresors

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="add-a-key-to-key-vault"></a>Hinzufügen eines Schlüssels zu Key Vault

Ein Schlüssel kann dem Tresor mit einigen wenigen Zusatzschritten hinzugefügt werden. Dieser Schlüssel kann dann von einer Anwendung verwendet werden. 

Geben Sie die folgenden Befehle ein, um einen Schlüssel mit dem Namen **ExampleKey** zu erstellen:

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "ExampleKey" -Destination "Software"
```

Nun können Sie mit dem dazugehörigen URI auf den Schlüssel verweisen, den Sie der Azure Key Vault-Instanz hinzugefügt haben. Verwenden Sie **„https://<Ihr-eindeutiger-Schlüsseltresor-Name>.vault.azure.net/keys/ExampleKey“** , um die aktuelle Version abzurufen. 

So zeigen Sie den zuvor gespeicherten Schlüssel an:

```azurepowershell-interactive
Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -KeyName "ExampleKey"
```

Sie haben nun eine Key Vault-Instanz erstellt sowie einen Schlüssel gespeichert und abgerufen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Key Vault-Instanz erstellt und ein Zertifikat darin gespeichert. Weitere Informationen zu Key Vault und zur Integration in Ihre Anwendungen finden Sie in den folgenden Artikeln:

- [Was ist der Azure-Schlüsseltresor?](../general/overview.md)
- Sehen Sie sich die Referenz zu den [Azure PowerShell-Cmdlets für Key Vault](/powershell/module/az.keyvault/) an.
- [Azure Key Vault-Sicherheitsübersicht](../general/security-overview.md)
