---
title: Erstellen Ihres Azure Maps Kontos mithilfe einer ARM-Vorlage | Microsoft Azure Maps
description: Erfahren Sie, wie Sie mit einer Azure Resource Manager-Vorlage (ARM) ein Azure Maps-Konto erstellen.
author: philmea
ms.author: philmea
ms.date: 10/20/2020
ms.topic: how-to
ms.service: azure-maps
ms.openlocfilehash: c715c0639e962f76f669515c1d2c826c8cf6cc9e
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92524989"
---
# <a name="create-your-azure-maps-account-using-an-arm-template"></a>Erstellen eines Azure Maps-Kontos über eine ARM-Vorlage

Sie können mithilfe einer Azure Resource Manager-Vorlage (ARM) ein Azure Maps-Konto erstellen. Sobald Sie über ein Konto verfügen, können Sie die APIs auf Ihrer Website oder in Ihrer mobilen Anwendung implementieren.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-maps-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie für diesen Artikel die folgenden Schritte aus:

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-maps-create/).

:::code language="json" source="~/quickstart-templates/101-maps-create/azuredeploy.json":::

Die Azure Maps-Kontoressource ist in dieser Vorlage definiert:

* [**Microsoft.Maps/accounts:** ](/azure/templates/microsoft.maps/accounts) Erstellen Sie ein Azure Maps Konto.

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

1. Klicken Sie auf das folgende Bild, um sich bei Azure anzumelden und eine Vorlage zu öffnen. Die Vorlage erstellt ein Azure Maps-Konto.

    [![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-maps-create%2Fazuredeploy.json)

2. Wählen Sie die folgenden Werte aus, bzw. geben Sie sie ein.

    ![ARM-Vorlage, Bereitstellung im Portal](./media/how-to-create-template/create-account-using-template-portal.png)

    Verwenden Sie den Standardwert, um das Azure Maps-Konto zu erstellen, sofern kein anderer Wert angegeben ist.

    * **Abonnement** : Wählen Sie ein Azure-Abonnement aus.
    * **Ressourcengruppe** : Wählen Sie die Option **Neu erstellen** aus, geben Sie einen eindeutigen Namen für die Ressourcengruppe ein, und klicken Sie dann auf **OK**.
    * **Standort** : Wählen Sie einen Standort aus. Beispiel: **USA, Westen 2**.
    * **Kontoname:** Geben Sie einen Namen für Ihr Azure Maps-Konto ein, der global eindeutig sein muss.
    * **Tarif:** Wählen Sie einen geeigneten Tarif aus, der Standardwert für die Vorlage ist „S0“.

3. Klicken Sie auf **Überprüfen und erstellen**. 
4. Bestätigen Sie Ihre Einstellungen auf der Seite „Überprüfen“, und klicken Sie auf **Erstellen**. Nach der erfolgreichen Bereitstellung Ihres Azure Maps-Kontos erhalten Sie eine Benachrichtigung:

    ![ARM-Vorlage, Benachrichtigung zur Bereitstellung im Portal](./media/how-to-create-template/resource-manager-template-portal-deployment-notification.png)

Zum Bereitstellen Ihrer Vorlage wird das Azure-Portal verwendet. Sie können auch Azure PowerShell, die Azure CLI und die REST-API verwenden. Informationen zu anderen Bereitstellungsmethoden finden Sie unter [Bereitstellen von Vorlagen](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

Sie können das Azure-Portal verwenden, um Ihr Azure Maps-Konto zu überprüfen und Ihre Schlüssel anzuzeigen. Sie können auch das folgende Azure CLI-Skript verwenden, um Ihre Kontoschlüssel aufzulisten.

```azurecli-interactive
az maps account keys list --name MyMapsAccount --resource-group MyResourceGroup
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcengruppe nicht mehr benötigen, löschen Sie sie. Dadurch wird auch das Azure Maps-Konto gelöscht. So löschen Sie die Ressourcengruppe mithilfe der Azure-Befehlszeilenschnittstelle

```azurecli-interactive
az group delete --name MyResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Maps und zum Azure Resource Manager finden Sie in den folgenden Artikeln:

- [Demoanwendung](quick-demo-map-app.md) zum Erstellen eines Azure Maps-Kontos
- Weitere Informationen zu [ARM-Vorlagen](../azure-resource-manager/templates/overview.md)