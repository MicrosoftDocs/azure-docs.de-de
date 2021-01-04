---
title: Erstellen einer Azure Cognitive Services-Ressource mithilfe von ARM-Vorlagen | Microsoft-Dokumentation
description: Erstellen Sie eine Azure Cognitive Service-Ressource mithilfe einer ARM-Vorlage.
keywords: Cognitive Services, kognitive Intelligenz, kognitive Lösungen, kognitive künstliche Intelligenz
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: aahi
ms.custom: subject-armqs, devx-track-azurecli
ms.openlocfilehash: 4afaad77fdff7a1ba80a385e9f2c3d8ddef8ab96
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842106"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-an-arm-template"></a>Schnellstart: Erstellen einer Cognitive Services-Ressource mithilfe einer ARM-Vorlage

In dieser Schnellstartanleitung wird beschrieben, wie Sie mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage) Cognitive Services erstellen.

Azure Cognitive Services sind cloudbasierte Dienste mit REST-APIs und Clientbibliothek-SDKs, die Entwickler bei der Erstellung intelligenter Anwendungen unterstützen, ohne dass direkte KI- oder Data Science-Fähigkeiten oder -Kenntnisse erforderlich sind. Mit Cognitive Services können Entwickler ganz einfach kognitive Features in ihre Anwendungen integrieren – mit kognitiven Lösungen, die sehen, hören, sprechen, verstehen und sogar schlussfolgern können.

Erstellen einer Ressource mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage). Diese Ressource für mehrere Dienste gibt Ihnen folgende Möglichkeiten:

* Greifen Sie mit einem einzigen Schlüssel und Endpunkt auf mehrere Azure Cognitive Services zu.
* Konsolidieren Sie die Abrechnung der von Ihnen verwendeten Dienste.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![Bereitstellen Ihres Cognitive Service auf Azure](../media/template-deployments/deploy-to-azure.svg "Bereitstellen Ihres Cognitive Service auf Azure")](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cognitive-services-universalkey%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto erstellen](https://azure.microsoft.com/free/cognitive-services).

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-cognitive-services-universalkey/).

:::code language="json" source="~/quickstart-templates/101-cognitive-services-universalkey/azuredeploy.json":::

In der Vorlage ist eine einzelne Azure-Ressource definiert:
* [Microsoft.CognitiveServices/accounts](/azure/templates/microsoft.cognitiveservices/accounts): erstellt eine Cognitive Services-Ressource.

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. Klicken Sie auf die Schaltfläche **Bereitstellen in Azure**.

    [![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cognitive-services-universalkey%2Fazuredeploy.json)

2. Geben Sie die folgenden Werte ein.

    |Wert  |Beschreibung  |
    |---------|---------|
    | **Abonnement** | Wählen Sie ein Azure-Abonnement aus. |
    | **Ressourcengruppe** | Wählen Sie die Option **Neu erstellen** aus, geben Sie einen eindeutigen Namen für die Ressourcengruppe ein, und klicken Sie dann auf **OK**. |
    | **Region** | Wählen Sie eine Region aus.  Beispiel: **USA, Osten**. |
    | **Cognitive Service-Name** | Ersetzen Sie den Wert durch einen eindeutigen Namen für Ihre Ressource. Sie benötigen den Namen im nächsten Abschnitt beim Überprüfen der Bereitstellung. |
    | **Location** | Ersetzen Sie dies durch die oben angegebene Region. |
    | **sku** | Die [Preisstufe](https://azure.microsoft.com/pricing/details/cognitive-services/) für Ihre Ressource. |

    :::image type="content" source="media/arm-template/universal-key-portal-template.png" alt-text="Bildschirm für die Ressourcenerstellung":::

3. Wählen Sie **Überprüfen + erstellen** und danach **Erstellen** aus. Nachdem die Bereitstellung der Ressource erfolgreich abgeschlossen wurde, ist die Schaltfläche **Zu Ressource wechseln** hervorgehoben.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/CLI)

> [!NOTE]
> Zum Erstellen von `az deployment group` ist Azure CLI, Version 2.6 oder höher, erforderlich. Verwenden Sie den Befehl `az --version`, um den Versionstyp anzuzeigen. Weitere Informationen finden Sie in der [Dokumentation](/cli/azure/deployment/group).

Führen Sie das folgende Skript an der Azure-Befehlszeilenschnittstelle (CLI) [auf dem lokalen Computer](/cli/azure/install-azure-cli?view=azure-cli-latest) oder in einem Browser über die Schaltfläche **Ausprobieren** aus. Geben Sie einen Namen und einen Speicherort (beispielsweise `centralus`) für eine neue Ressourcengruppe ein, dann wird die ARM-Vorlage verwendet, um darin eine Cognitive Services-Ressource bereitzustellen. Merken Sie sich den verwendeten Namen. Sie benötigen ihn später, um die Bereitstellung zu überprüfen.


```azurecli-interactive
read -p "Enter a name for your new resource group:" resourceGroupName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cognitive-services-universalkey/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri  $templateUri &&
echo "Press [ENTER] to continue ..." &&
read
```

---

[!INCLUDE [Register Azure resource for subscription](./includes/register-resource-subscription.md)]


## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

# <a name="portal"></a>[Portal](#tab/portal)

Wenn die Bereitstellung abgeschlossen ist, können Sie auf die Schaltfläche **Zu Ressource wechseln** klicken, um die neue Ressource anzuzeigen. Die Ressourcengruppe können Sie auch auf folgende Weise ermitteln:

1. Wählen Sie im linken Navigationsmenü **Ressourcengruppen** aus.
2. Wählen Sie den Namen der Ressourcengruppe aus.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/CLI)

Führen Sie an der Azure CLI das folgende Skript aus, und geben Sie den Namen der zuvor erstellten Ressourcengruppe ein.

```azurecli-interactive
echo "Enter the resource group where the Cognitive Services resource exists:" &&
read resourceGroupName &&
az cognitiveservices account list -g $resourceGroupName
```

---


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Cognitive Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen in der Gruppe enthaltenen Ressourcen gelöscht.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. Erweitern Sie im Azure-Portal das Menü auf der linken Seite, um das Menü mit den Diensten zu öffnen, und klicken Sie auf **Ressourcengruppen**, um die Liste mit Ihren Ressourcengruppen anzuzeigen.
2. Suchen Sie die Ressourcengruppe, die die zu löschende Ressource enthält.
3. Klicken Sie mit der rechten Maustaste auf die Ressourcengruppenauflistung. Klicken Sie auf **Ressourcengruppe löschen**, und bestätigen Sie den Vorgang.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/CLI)

Führen Sie an der Azure CLI das folgende Skript aus, und geben Sie den Namen der zuvor erstellten Ressourcengruppe ein.

```azurecli-interactive
echo "Enter the resource group name, for deletion:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

---

## <a name="next-steps"></a>Nächste Schritte

* [Authentifizieren von Anforderungen an Azure Cognitive Services](authentication.md)
* [Was ist Azure Cognitive Services?](./what-are-cognitive-services.md)
* [Unterstützung für natürliche Sprachen in Azure Cognitive Services](language-support.md)
* [Containerunterstützung in Azure Cognitive Services](cognitive-services-container-support.md)
