---
title: 'Schnellstart: Erstellen eines Azure Purview-Kontos unter Verwendung von Azure PowerShell/Azure CLI (Vorschauversion)'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie unter Verwendung von Azure PowerShell/Azure CLI ein Azure Purview-Konto erstellen.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: quickstart
ms.date: 11/23/2020
ms.openlocfilehash: 7db546ec9038403bd9e34f637e9d9de368277d52
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400301"
---
# <a name="quickstart-create-an-azure-purview-account-using-azure-powershellazure-cli"></a>Schnellstart: Erstellen eines Azure Purview-Kontos unter Verwendung von Azure PowerShell/Azure CLI

> [!IMPORTANT]
> Azure Purview ist derzeit als VORSCHAUVERSION verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In dieser Schnellstartanleitung wird ein Azure Purview-Konto unter Verwendung von Azure PowerShell/Azure CLI erstellt.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Das Benutzerkonto, mit dem Sie sich bei Azure anmelden, muss ein Mitglied der Rolle „Mitwirkender“ oder „Besitzer“ oder ein Administrator des Azure-Abonnements sein.

* Ihr eigener [Azure Active Directory-Mandant](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).

* Installieren Sie zum Bereitstellen der Vorlage entweder Azure PowerShell oder die Azure CLI auf Ihrem Clientcomputer: [Befehlszeilenbereitstellung](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-cli#command-line-deployment)

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich mit Ihrem Azure-Konto beim [Azure-Portal](https://portal.azure.com) an.

## <a name="configure-your-subscription"></a>Konfigurieren Ihres Abonnements

Führen Sie bei Bedarf die folgenden Schritte aus, um Ihr Abonnement so zu konfigurieren, dass Azure Purview darunter ausgeführt werden kann:

   1. Suchen Sie im Azure-Portal nach dem Eintrag **Abonnements**, und wählen Sie ihn aus.

   1. Wählen Sie in der Abonnementliste das gewünschte Abonnement aus. Für das Abonnement wird Administratorzugriff benötigt.

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="Screenshot: Auswählen eines Abonnements im Azure-Portal":::

   1. Wählen Sie für Ihr Abonnement die Option **Ressourcenanbieter** aus. Suchen Sie im Bereich **Ressourcenanbieter** nach den drei folgenden Ressourcenanbietern, und registrieren Sie sie: 
       1. **Microsoft.Purview**
       1. **Microsoft.Storage**
       1. **Microsoft.EventHub** 
      
      Sollten sie nicht registriert sein, wählen Sie **Registrieren** aus, um sie zu registrieren.

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="Screenshot: Registrieren des Ressourcenanbieters „Microsoft.Purview“ im Azure-Portal":::

## <a name="create-an-azure-purview-account-instance"></a>Erstellen einer Azure Purview-Kontoinstanz

1. Melden Sie sich mit Ihren Azure-Anmeldeinformationen an.

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Connect-AzAccount
    ```
    
    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
    
    ```azurecli
    az login
    ```
    
    ---

1. Wenn Sie über mehrere Azure-Abonnements verfügen, wählen Sie dasjenige aus, das Sie verwenden möchten:

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Set-AzContext [SubscriptionID/SubscriptionName]
    ```
    
    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
    
    ```azurecli
    az account set --subscription [SubscriptionID/SubscriptionName]
    ```
    
    ---

1. Erstellen Sie eine Ressourcengruppe für Ihr Purview-Konto. Falls Sie bereits über eins verfügen, können Sie diesen Schritt überspringen:

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroup `
      -Name myResourceGroup `
      -Location "East US"
    ```
    
    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
    
    ```azurecli
    az group create \
      --name myResourceGroup \
      --location "East US"
    ```
    
    ---

1. Erstellen Sie eine Purview-Vorlagendatei (beispielsweise `purviewtemplate.json`). Sie können `name`, `location` und `capacity` (`4` oder `16`) aktualisieren:

    ```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [
        {
          "name": "<yourPurviewAccountName>",
          "type": "Microsoft.Purview/accounts",
          "apiVersion": "2020-12-01-preview",
          "location": "EastUs",
          "identity": {
            "type": "SystemAssigned"
          },
          "properties": {
            "networkAcls": {
              "defaultAction": "Allow"
            }
          },
          "dependsOn": [],
          "sku": {
            "name": "Standard",
            "capacity": "4"
          },
          "tags": {}
        }
      ],
      "outputs": {}
    }
    ```

1. Bereitstellen der Purview-Vorlage

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName "<myResourceGroup>" -TemplateFile "<PATH TO purviewtemplate.json>"
    ```
    
    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
    
    Für die Ausführung dieses Bereitstellungsbefehls müssen Sie über die [aktuelle Version](/cli/azure/install-azure-cli) der Azure CLI verfügen.
    
    ```azurecli
    az deployment group create --resource-group "<myResourceGroup>" --template-file "<PATH TO purviewtemplate.json>"
    ```
    
    ---

1. Der Bereitstellungsbefehl gibt Ergebnisse zurück. Suchen Sie nach `ProvisioningState`, um zu sehen, ob die Bereitstellung erfolgreich war.
    
## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie gelernt, wie Sie ein Azure Purview-Konto erstellen.

Im nächsten Artikel erfahren Sie, wie Sie Benutzern Zugriff auf Ihr Azure Purview-Konto gewähren. 

> [!div class="nextstepaction"]
> [Rollenbasierte Zugriffssteuerung auf der Datenebene von Azure Purview](catalog-permissions.md)