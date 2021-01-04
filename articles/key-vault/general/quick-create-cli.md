---
title: Schnellstart – Erstellen eines Azure Key Vault über die Azure CLI
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie eine Azure Key Vault-Instanz über die Azure CLI erstellen.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: d84f500560031322cff32005b537037dc897919e
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94516763"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-cli"></a>Schnellstart: Erstellen eines Schlüsseltresors über die Azure CLI

Azure Key Vault ist ein Clouddienst, der einen sicheren Speicher für [Schlüssel](../keys/index.yml), [Geheimnisse](../secrets/index.yml) und [Zertifikate](../certificates/index.yml) bereitstellt. Weitere Informationen zu Key Vault finden Sie unter [Informationen zu Azure Key Vault](overview.md). Weitere Informationen zu den Elementen, die in einem Schlüsseltresor gespeichert werden können, finden Sie unter [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten](about-keys-secrets-certificates.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Für diesen Schnellstart ist mindestens Version 2.0.4 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *ContosoResourceGroup* am Standort *eastus*.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="create-a-key-vault"></a>Erstellen eines Schlüsseltresors

Erstellen Sie einen Schlüsseltresors in der Ressourcengruppe aus dem vorherigen Schritt. Dazu müssen einige Informationen angegeben werden:

- Name des Schlüsseltresors: Eine Zeichenfolge mit 3 bis 24 Zeichen, die nur Zahlen (0–9), Buchstaben (a–z, A–Z) und Bindestriche (-) enthalten darf.

  > [!Important]
  > Jeder Schlüsseltresor muss einen eindeutigen Namen haben. Ersetzen Sie in den folgenden Beispielen „<your-unique-keyvault-name>“ durch den Namen Ihres Schlüsseltresors.

- Ressourcengruppennamen: **myResourceGroup**
- Standort: **EastUS**

```azurecli
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "EastUS"
```

Die Ausgabe dieses Cmdlets zeigt Eigenschaften des neu erstellten Schlüsseltresors. Beachten Sie die beiden folgenden Eigenschaften:

- **Tresorname**: Der Name, den Sie oben für den Parameter „--name“ angegeben haben.
- **Tresor-URI**: In dem Beispiel ist dies „https://&lt;Ihr-eindeutiger-Schlüsseltresor-Name&gt;.vault.azure.net/“. Anwendungen, die Ihren Tresor über die zugehörige REST-API nutzen, müssen diesen URI verwenden.

An diesem Punkt ist nur Ihr Azure-Konto zum Ausführen von Vorgängen für den neuen Tresor autorisiert.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Andere Schnellstartanleitungen und Tutorials in dieser Sammlung bauen auf dieser Schnellstartanleitung auf. Falls Sie mit weiteren Schnellstartanleitungen und Tutorials fortfahren möchten, sollten Sie die Ressourcen nicht bereinigen.

Wenn Sie die Ressourcen nicht mehr benötigen, führen Sie den Azure CLI-Befehl [az group delete](/cli/azure/group) aus, um die Ressourcengruppe und alle dazugehörigen Ressourcen zu löschen:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen Schlüsseltresor erstellt und diesen gelöscht. Weitere Informationen zu Key Vault und zur Integration in Ihre Anwendungen finden Sie in den folgenden Artikeln:

- [Was ist der Azure-Schlüsseltresor?](overview.md)
- Sehen Sie sich die Referenz zu den [Azure CLI-Befehlen vom Typ „az keyvault“](/cli/azure/keyvault?view=azure-cli-latest) an.
- [Bewährte Methoden zum Verwenden von Key Vault](best-practices.md)
