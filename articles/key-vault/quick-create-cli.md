---
title: 'Azure-Schnellstart: Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Key Vault mithilfe der Azure CLI | Microsoft-Dokumentation'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe der Azure CLI ein Geheimnis in Azure Key Vault festlegen und das Geheimnis daraus abrufen.
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/08/2019
ms.author: barclayn
ms.openlocfilehash: e9537c158afad2877bb2df2650500ab218de676f
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66726765"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-azure-cli"></a>Schnellstart: Festlegen eines Geheimnisses und Abrufen des Geheimnisses aus Azure Key Vault mithilfe der Azure CLI

Azure Key Vault ist ein Clouddienst, der als sicherer Geheimnisspeicher fungiert. Dadurch können Schlüssel, Kennwörter, Zertifikate und andere Geheimnisse sicher gespeichert werden. Weitere Informationen zu Key Vault finden Sie in der [Übersicht](key-vault-overview.md). Über die Azure-Befehlszeilenschnittstelle können Azure-Ressourcen mithilfe von Befehlen oder Skripts erstellt und verwaltet werden. In dieser Schnellstartanleitung erstellen Sie einen Schlüsseltresor. Danach speichern Sie ein Geheimnis.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Wenn Sie die Befehlszeilenschnittstelle lokal installieren und verwenden möchten, müssen Sie für diese Schnellstartanleitung mindestens die Azure CLI-Version 2.0.4 verwenden. Führen Sie `az --version` aus, um die Version zu finden. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI]( /cli/azure/install-azure-cli).

Geben Sie Folgendes ein, um sich über die Befehlszeilenschnittstelle bei Azure anzumelden:

```azurecli
az login
```

Weitere Informationen zu den Anmeldeoptionen für die Befehlszeilenschnittstelle finden Sie unter [Anmelden mit Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

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

Nun können Sie mit dem dazugehörigen URI auf das Kennwort verweisen, das Sie der Azure Key Vault-Instanz hinzugefügt haben. Verwenden Sie **https://ContosoVault.vault.azure.net/secrets/ExamplePassword** , um die aktuelle Version abzurufen. 

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

In dieser Schnellstartanleitung haben Sie eine Key Vault-Instanz erstellt und ein Geheimnis darin gespeichert. Das Tutorial zu Webanwendungen mit Key Vault enthält weitere Informationen zu Key Vault und zur Verwendung mit Ihren Anwendungen.

> [!div class="nextstepaction"]
> Wenn Sie erfahren möchten, wie Sie ein Geheimnis aus Key Vault aus einer Webanwendung mit verwalteten Identitäten für Azure-Ressourcen lesen, fahren Sie mit dem Tutorial [Konfigurieren einer Azure-Webanwendung für das Lesen eines Geheimnisses aus Key Vault](quick-create-net.md) fort.
