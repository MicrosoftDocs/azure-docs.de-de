---
title: 'Erstellen und Abrufen der Attribute eines Schlüssels in Azure Key Vault: Azure CLI'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe der Azure CLI einen Schlüssel in Azure Key Vault festlegen und den Schlüssel daraus abrufen.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: 4141e60370b397e799664b7d42384bbeb096bd05
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071170"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-cli"></a>Schnellstart: Festlegen eines Schlüssels und Abrufen des Schlüssels aus Azure Key Vault mithilfe der Azure CLI

In dieser Schnellstartanleitung erstellen Sie einen Schlüsseltresor in Azure Key Vault mit der Azure CLI. Azure Key Vault ist ein Clouddienst, der als sicherer Geheimnisspeicher fungiert. Dadurch können Schlüssel, Kennwörter, Zertifikate und andere Geheimnisse sicher gespeichert werden. Weitere Informationen zu Key Vault finden Sie in der [Übersicht](../general/overview.md). Über die Azure-Befehlszeilenschnittstelle können Azure-Ressourcen mithilfe von Befehlen oder Skripts erstellt und verwaltet werden. Danach speichern Sie einen Schlüssel.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Für diesen Schnellstart ist mindestens Version 2.0.4 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Erstellen eines Schlüsseltresors

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="add-a-key-to-key-vault"></a>Hinzufügen eines Schlüssels zu Key Vault

Ein Schlüssel kann dem Tresor mit einigen wenigen Zusatzschritten hinzugefügt werden. Dieser Schlüssel kann dann von einer Anwendung verwendet werden. 

Geben Sie die folgenden Befehle ein, um einen Schlüssel mit dem Namen **ExampleKey** zu erstellen:

```azurecli
az keyvault key create --vault-name "<your-unique-keyvault-name>" -n ExampleKey --protection software
```

Nun können Sie mit dem dazugehörigen URI auf den Schlüssel verweisen, den Sie der Azure Key Vault-Instanz hinzugefügt haben. Verwenden Sie **'https://<Ihr-eindeutiger-Schlüsseltresor-Name>.vault.azure.net/keys/ExampleKey'** , um die aktuelle Version abzurufen. 

So zeigen Sie den zuvor gespeicherten Schlüssel an:

```azurecli

az keyvault key show --name "ExampleKey" --vault-name "<your-unique-keyvault-name>"
```

Sie haben nun eine Key Vault-Instanz erstellt sowie einen Schlüssel gespeichert und abgerufen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Key Vault-Instanz erstellt und einen Schlüssel darin gespeichert. Weitere Informationen zu Key Vault und zur Integration in Ihre Anwendungen finden Sie in den folgenden Artikeln:

- [Was ist der Azure-Schlüsseltresor?](../general/overview.md)
- Sehen Sie sich die Referenz zu den [Azure CLI-Befehlen vom Typ „az keyvault“](/cli/azure/keyvault) an.
- [Azure Key Vault-Sicherheitsübersicht](../general/security-overview.md)
