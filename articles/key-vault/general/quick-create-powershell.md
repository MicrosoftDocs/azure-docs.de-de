---
title: 'Schnellstart: Erstellen einer Azure Key Vault-Instanz mit Azure PowerShell'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie eine Azure Key Vault-Instanz mithilfe von Azure PowerShell erstellen.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: e77493bc73bc2d6f590d9bdcf891171fbd71f74e
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070226"
---
# <a name="quickstart-create-a-key-vault-using-powershell"></a>Schnellstart: Erstellen eines Schlüsseltresors mithilfe von PowerShell

Azure Key Vault ist ein Clouddienst, der einen sicheren Speicher für [Schlüssel](../keys/index.yml), [Geheimnisse](../secrets/index.yml) und [Zertifikate](../certificates/index.yml) bereitstellt. Weitere Informationen zu Key Vault finden Sie unter [Informationen zu Azure Key Vault](overview.md). Weitere Informationen zu den Elementen, die in einem Schlüsseltresor gespeichert werden können, finden Sie unter [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](about-keys-secrets-certificates.md).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

In dieser Schnellstartanleitung erstellen Sie einen Schlüsseltresor mit [Azure PowerShell](/powershell/azure/). Wenn Sie PowerShell lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial mindestens die Version 1.0.0 des Azure PowerShell-Moduls verwenden. Geben Sie `$PSVersionTable.PSVersion` ein, um die Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Login-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>Erstellen eines Schlüsseltresors

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="clean-up-resources"></a>Bereinigen der Ressourcen

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Key Vault-Instanz mithilfe von Azure PowerShell erstellt. Weitere Informationen zu Key Vault und zur Integration in Ihre Anwendungen finden Sie in den folgenden Artikeln:

- [Was ist der Azure-Schlüsseltresor?](overview.md)
- Sehen Sie sich die Referenz zu den [Azure PowerShell-Cmdlets für Key Vault](/powershell/module/az.keyvault/) an.
- [Azure Key Vault-Sicherheitsübersicht](security-overview.md)

