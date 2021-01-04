---
title: 'Schnellstart: Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Azure Key Vault'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe der Azure CLI ein Geheimnis in Azure Key Vault festlegen und das Geheimnis daraus abrufen.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 36688586cc0b9c94a07873bacfa6210f31695d36
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94517256"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-azure-cli"></a>Schnellstart: Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Azure Key Vault mithilfe der Azure CLI

In dieser Schnellstartanleitung erstellen Sie einen Schlüsseltresor in Azure Key Vault mit der Azure CLI. Azure Key Vault ist ein Clouddienst, der als sicherer Geheimnisspeicher fungiert. Dadurch können Schlüssel, Kennwörter, Zertifikate und andere Geheimnisse sicher gespeichert werden. Weitere Informationen zu Key Vault finden Sie in der [Übersicht](../general/overview.md). Über die Azure-Befehlszeilenschnittstelle können Azure-Ressourcen mithilfe von Befehlen oder Skripts erstellt und verwaltet werden. Danach speichern Sie ein Geheimnis.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Für diesen Schnellstart ist mindestens Version 2.0.4 der Azure CLI erforderlich. Bei Verwendung von Azure Cloud Shell ist die aktuelle Version bereits installiert.

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *ContosoResourceGroup* am Standort *eastus*.

```azurecli
az group create --name "ContosoResourceGroup" --location eastus
```

## <a name="create-a-key-vault"></a>Erstellen eines Schlüsseltresors

Als Nächstes erstellen Sie eine Key Vault-Instanz in der Ressourcengruppe aus dem vorherigen Schritt. Dazu müssen einige Informationen angegeben werden:

- Im Rahmen dieser Schnellstartanleitung verwenden wir **Contoso-vault2**. Geben Sie bei Ihren Tests einen eindeutigen Namen an.
- Der Ressourcengruppenname lautet **ContosoResourceGroup**.
- Als Standort wird **USA, Osten** verwendet.

```azurecli
az keyvault create --name "Contoso-Vault2" --resource-group "ContosoResourceGroup" --location eastus
```

Die Ausgabe dieses Cmdlets zeigt Eigenschaften der neu erstellten Key Vault-Instanz. Beachten Sie die beiden folgenden Eigenschaften:

- **Tresorname**: In diesem Beispiel wird **Contoso-Vault2** verwendet. Sie verwenden diesen Namen für andere Schlüsseltresor-Befehle.
- **Tresor-URI**: In diesem Beispiel https://contoso-vault2.vault.azure.net/. Anwendungen, die Ihren Tresor über die zugehörige REST-API nutzen, müssen diesen URI verwenden.

An diesem Punkt ist nur Ihr Azure-Konto zum Ausführen von Vorgängen für den neuen Tresor autorisiert.

## <a name="add-a-secret-to-key-vault"></a>Hinzufügen eines Geheimnisses zu Key Vault

Ein Geheimnis kann dem Tresor mit einigen wenigen Zusatzschritten hinzugefügt werden. Dieses Kennwort kann dann von einer Anwendung verwendet werden. Das Kennwort soll **ExamplePassword** heißen und den Wert **hVFkk965BuUv** enthalten.

Geben Sie die folgenden Befehle ein, um in Key Vault ein Geheimnis namens **ExamplePassword** mit dem Wert **hVFkk965BuUv** zu erstellen:

```azurecli
az keyvault secret set --vault-name "Contoso-Vault2" --name "ExamplePassword" --value "hVFkk965BuUv"
```

Nun können Sie mit dem dazugehörigen URI auf das Kennwort verweisen, das Sie der Azure Key Vault-Instanz hinzugefügt haben. Verwenden Sie **„https://Contoso-Vault2.vault.azure.net/secrets/ExamplePassword“** , um die aktuelle Version abzurufen. 

Geben Sie Folgendes ein, um den Wert im Geheimnis als Nur-Text anzuzeigen:

```azurecli
az keyvault secret show --name "ExamplePassword" --vault-name "Contoso-Vault2"
```

Sie haben nun eine Key Vault-Instanz erstellt sowie ein Geheimnis gespeichert und abgerufen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Andere Schnellstartanleitungen und Tutorials in dieser Sammlung bauen auf dieser Schnellstartanleitung auf. Falls Sie mit weiteren Schnellstartanleitungen und Tutorials fortfahren möchten, sollten Sie die Ressourcen nicht bereinigen.
Wenn Sie die Ressourcen nicht mehr benötigen, führen Sie den Befehl [az group delete](/cli/azure/group) aus, um die Ressourcengruppe und alle dazugehörigen Ressourcen zu löschen. Die Ressourcen können wie folgt gelöscht werden:

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie eine Key Vault-Instanz erstellt und ein Geheimnis darin gespeichert. Weitere Informationen zu Key Vault und zur Integration in Ihre Anwendungen finden Sie in den folgenden Artikeln:

- [Was ist der Azure-Schlüsseltresor?](../general/overview.md)
- Sehen Sie sich die Referenz zu den [Azure CLI-Befehlen vom Typ „az keyvault“](/cli/azure/keyvault?view=azure-cli-latest) an.
- [Bewährte Methoden zum Verwenden von Key Vault](../general/best-practices.md)
