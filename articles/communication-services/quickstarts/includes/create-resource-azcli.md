---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 1/28/2021
ms.author: mikben
ms.openlocfilehash: d70d53da48f41f0a6a37da6154d29696d3824325
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820001"
---
## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/dotnet/).

## <a name="create-azure-communication-resource"></a>Erstellen von Azure Communication-Ressourcen

Wenn Sie eine Azure Communication Services-Ressource erstellen möchten, [melden Sie sich bei der Azure CLI an](https://docs.microsoft.com/cli/azure/authenticate-azure-cli), und führen Sie den folgenden Befehl aus:

```azurecli
az communication create --name "<communicationName>" --location "Global" --data-location "United States" --resource-group "<resourceGroup>"
```

Sie können Ihre Communication Services-Ressource mit den folgenden Optionen konfigurieren:

* Die Ressourcengruppe
* Den Namen der Communication Services-Ressource
* Die Geografie, der die Ressource zugeordnet wird

Im nächsten Schritt können Sie der Ressource Tags zuweisen. Tags können verwendet werden, um Azure-Ressourcen zu organisieren. Weitere Informationen zu Tags finden Sie in der [Dokumentation zum Anwenden von Tags auf Ressourcen](../../../azure-resource-manager/management/tag-resources.md).

## <a name="manage-your-communication-services-resource"></a>Verwalten Ihrer Communication Services-Ressource

Führen Sie die folgenden Befehle aus, um Ihrer Communication Services-Ressource Tags hinzuzufügen:

```azurecli
az communication update --name "<communicationName>" --tags newTag="newVal" --resource-group "<resourceGroup>"

az communication show --name "<communicationName>" --resource-group "<resourceGroup>"
```

Informationen zu weiteren Befehlen finden Sie unter [az communication](https://docs.microsoft.com/cli/azure/ext/communication/communication).