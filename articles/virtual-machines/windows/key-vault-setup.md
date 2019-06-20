---
title: Einrichten von Key Vault für virtuelle Windows-Computer in Azure Resource Manager | Microsoft-Dokumentation
description: Einrichten eines Schlüsseltresors, der für einen mit Azure Resource Manager erstellten virtuellen Computer verwendet werden soll.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 33a483e2-cfbc-4c62-a588-5d9fd52491e2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: kasing
ms.openlocfilehash: e9d2b518af50aaec43bc7075e51829e80cdeddf5
ms.sourcegitcommit: db3fe303b251c92e94072b160e546cec15361c2c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66016146"
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Einrichten des Schlüsseltresors für virtuelle Computer in Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

Im Azure Resource Manager-Stapel sind geheime Schlüssel/Zertifikate als Ressourcen modelliert, die durch den Ressourcenanbieter des Schlüsseltresors bereitgestellt werden. Weitere Informationen über Schlüsseltresore finden Sie unter [Was ist der Azure-Schlüsseltresor?](../../key-vault/key-vault-whatis.md)

> [!NOTE]
> 1. Damit ein Schlüsseltresor mit virtuellen Computern verwendet werden kann, die mit Azure Resource Manager bereitgestellt wurden, müssen Sie die **EnabledForDeployment** -Eigenschaft für den Schlüsseltresor auf „true“ festlegen. Dazu können Sie verschiedene Clients verwenden.
> 2. Der Schlüsseltresor muss im gleichen Abonnement und Speicherort wie der virtuelle Computer erstellt werden.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Verwenden von PowerShell zum Einrichten des Schlüsseltresors
Informationen zum Erstellen eines Schlüsseltresors mithilfe von PowerShell finden Sie unter [Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Azure Key Vault mithilfe von PowerShell](../../key-vault/quick-create-powershell.md).

Für neue Schlüsseltresore können Sie dieses PowerShell-Cmdlet verwenden:

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Für vorhandene Schlüsseltresore können Sie dieses PowerShell-Cmdlet verwenden:

    Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="use-cli-to-set-up-key-vault"></a>Verwenden der Befehlszeilenschnittstelle zum Einrichten des Schlüsseltresors
Informationen zum Erstellen eines Schlüsseltresors über die Befehlszeilenschnittstelle finden Sie unter [Verwalten des Schlüsseltresors über die Befehlszeilenschnittstelle](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault).

Wenn Sie die Befehlszeilenschnittstelle verwenden, müssen Sie den Schlüsseltresor erstellen, bevor Sie die Bereitstellungsrichtlinie zuweisen. Hierfür können Sie den folgenden Befehl verwenden:

    az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "EastAsia"
    
Führen Sie den folgenden Befehl aus, um Key Vault für die Verwendung mit der Vorlagenbereitstellung zu aktivieren:

    az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"

## <a name="use-templates-to-set-up-key-vault"></a>Verwenden von Vorlagen zum Einrichten des Schlüsseltresors
Wenn Sie eine Vorlage verwenden, müssen Sie die `enabledForDeployment`-Eigenschaft für die Schlüsseltresorressource auf `true` festlegen.

    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "ContosoKeyVault",
      "apiVersion": "2015-06-01",
      "location": "<location-of-key-vault>",
      "properties": {
        "enabledForDeployment": "true",
        ....
        ....
      }
    }

Weitere Optionen, die Sie beim Erstellen eines Schlüsseltresors mithilfe von Vorlagen konfigurieren können, finden Sie unter [Create a Key Vault](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)(Erstellen eines Schlüsseltresors).
