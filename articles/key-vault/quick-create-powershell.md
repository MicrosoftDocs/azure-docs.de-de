---
title: 'Azure-Schnellstart: Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Key Vault mithilfe von PowerShell | Microsoft-Dokumentation'
description: ''
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: 8d6260d462b4c244dfb41630e06710a1ce8baf6c
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66726778"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-powershell"></a>Schnellstart: Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Azure Key Vault mithilfe von PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure Key Vault ist ein Clouddienst, der als sicherer Geheimnisspeicher fungiert. Dadurch können Schlüssel, Kennwörter, Zertifikate und andere Geheimnisse sicher gespeichert werden. Weitere Informationen zu Key Vault finden Sie in der [Übersicht](key-vault-overview.md). In dieser Schnellstartanleitung erstellen Sie einen Schlüsseltresor mithilfe von PowerShell. Anschließend speichern Sie ein Geheimnis in dem neu erstellten Tresor.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial mindestens die Version 1.0.0 des Azure PowerShell-Moduls verwenden. Geben Sie `$PSVersionTable.PSVersion` ein, um die Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Login-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) eine Azure-Ressourcengruppe. Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>Erstellen eines Schlüsseltresors

Als Nächstes erstellen Sie eine Key Vault-Instanz. Für diesen Schritt benötigen Sie einige Informationen:

In dieser Schnellstartanleitung lautet der Name für die Key Vault-Instanz zwar „Contoso KeyVault2“, Sie müssen jedoch einen eindeutigen Namen verwenden.

- **Tresorname:** Contoso-Vault2
- **Ressourcengruppenname:** ContosoResourceGroup
- **Standort:** USA, Osten

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

Die Ausgabe dieses Cmdlets zeigt Eigenschaften des neu erstellten Schlüsseltresors. Beachten Sie die beiden folgenden Eigenschaften:

* **Tresorname**: In diesem Beispiel wird **Contoso-Vault2** verwendet. Sie verwenden diesen Namen für andere Schlüsseltresor-Cmdlets.
* **Tresor-URI**: In diesem Beispiel https://contosokeyvault.vault.azure.net/. Anwendungen, die Ihren Tresor über die zugehörige REST-API nutzen, müssen diesen URI verwenden.

Nach der Tresorerstellung ist Ihr Azure-Konto das einzige Konto, das zum Ausführen von Vorgängen für diesen neuen Tresor berechtigt ist.

![Ausgabe nach Abschluss des Befehls zur Erstellung des Schlüsseltresors](./media/quick-create-powershell/output-after-creating-keyvault.png)

## <a name="adding-a-secret-to-key-vault"></a>Hinzufügen eines Geheimnisses zu Key Vault

Ein Geheimnis kann dem Tresor mit wenigen Schritten hinzugefügt werden. In diesem Fall wird ein Kennwort hinzugefügt, das von einer Anwendung verwendet werden kann. Das Kennwort heißt **ExamplePassword** und enthält den Wert **hVFkk965BuUv**.

Geben Sie zunächst Folgendes ein, um den Wert **hVFkk965BuUv** in eine sichere Zeichenfolge zu konvertieren:

```azurepowershell-interactive
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
```

Geben Sie dann die folgenden PowerShell-Befehle ein, um in Key Vault ein Geheimnis namens **ExamplePassword** mit dem Wert **hVFkk965BuUv** zu erstellen:

```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'ExamplePassword' -SecretValue $secretvalue
```

Geben Sie Folgendes ein, um den Wert im Geheimnis als Nur-Text anzuzeigen:

```azurepowershell-interactive
(Get-AzKeyVaultSecret -vaultName "Contosokeyvault" -name "ExamplePassword").SecretValueText
```

Sie haben nun eine Key Vault-Instanz erstellt sowie ein Geheimnis gespeichert und abgerufen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

 Andere Schnellstartanleitungen und Tutorials in dieser Sammlung bauen auf dieser Schnellstartanleitung auf. Falls Sie mit weiteren Schnellstartanleitungen und Tutorials fortfahren möchten, sollten Sie die Ressourcen nicht bereinigen.

Wenn Sie die Ressourcen nicht mehr benötigen, führen Sie den Befehl [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) aus, um die Ressourcengruppe, die Key Vault-Instanz und alle dazugehörigen Ressourcen zu löschen.

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Key Vault-Instanz erstellt und einen Softwareschlüssel darin gespeichert. Das Tutorial zu Webanwendungen mit Key Vault enthält weitere Informationen zu Key Vault und zur Verwendung mit Ihren Anwendungen.

Wenn Sie erfahren möchten, wie Sie ein Geheimnis aus Key Vault aus einer Webanwendung mit verwalteten Identitäten für Azure-Ressourcen lesen, fahren Sie mit dem folgenden Tutorial fort:

> [!div class="nextstepaction"]
> [Tutorial: Konfigurieren einer Azure-Webanwendung zum Lesen eines Geheimnisses aus Key Vault](quick-create-net.md)
