---
title: 'Schnellstart: Erstellen einer Azure Front Door Service-Instanz mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage)'
description: In diesem Schnellstart wird beschrieben, wie Sie eine Azure Front Door Service-Instanz mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage) erstellen.
services: front-door
documentationcenter: ''
author: duongau
editor: ''
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2020
ms.author: duau
ms.custom: subject-armqs
ms.openlocfilehash: 223006193219afe4179f3161d5e60e6439207b22
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896054"
---
# <a name="quickstart-create-a-front-door-using-an-arm-template"></a>Schnellstart: Erstellen einer Front Door-Instanz mithilfe einer ARM-Vorlage

In diesem Schnellstart wird beschrieben, wie Sie eine Azure Resource Manager-Vorlage (ARM-Vorlage) zum Erstellen einer Front Door-Instanz verwenden, um Hochverfügbarkeit für einen Webendpunkt einzurichten.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen** . Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-front-door-create-basic%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.
* Die IP-Adresse oder der vollqualifizierter Domänenname (FQDN) einer Website oder Webanwendung

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-front-door-create-basic).

In diesem Schnellstart erstellen Sie eine Front Door-Konfiguration mit einem einzelnen Back-End und einem einzelnen Standardpfad, der mit `/*` übereinstimmt.

:::code language="json" source="~/quickstart-templates/101-front-door-create-basic/azuredeploy.json":::

In der Vorlage ist eine einzelne Azure-Ressource definiert:

* [**Microsoft.Network/frontDoors**](/azure/templates/microsoft.network/frontDoors)

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

1. Wählen Sie **Try it** (Ausprobieren) im folgenden Codeblock aus, um Azure Cloud Shell zu öffnen. Folgen Sie dann den Anweisungen, um sich bei Azure anzumelden.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-front-door-create-basic/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Warten Sie, bis die Aufforderung in der Konsole angezeigt wird.

1. Wählen Sie **Copy** (Kopieren) im vorherigen Codeblock aus, um das PowerShell-Skript zu kopieren.

1. Klicken Sie mit der rechten Maustaste auf den Shellkonsolenbereich, und wählen Sie **Einfügen** aus.

1. Gehen Sie die Werte ein.

    Bei der Vorlagenbereitstellung wird eine Front Door-Instanz mit einem einzelnen Back-End erstellt. In diesem Beispiel wird `microsoft.com` für **backendAddress** verwendet.

    Der Ressourcengruppenname ist der Projektname mit dem Zusatz **rg** .

    > [!NOTE]
    > **frontDoorName** muss ein global eindeutiger Name sein, damit die Vorlage erfolgreich bereitgestellt werden kann. Tritt bei der Bereitstellung ein Fehler auf, beginnen Sie erneut mit Schritt 1.

    Die Bereitstellung der Vorlage dauert einige Minuten. Nach Abschluss des Vorgangs sieht die Ausgabe in etwa wie folgt aus:

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-template-deployment-powershell-output.png" alt-text="PowerShell-Bereitstellungsausgabe für die Front Door-Instanz mit Resource Manager-Vorlage":::

Azure PowerShell wird verwendet, um die Vorlage bereitzustellen. Neben Azure PowerShell können Sie auch das Azure-Portal, die Azure-CLI und die REST-API verwenden. Informationen zu anderen Bereitstellungsmethoden finden Sie unter [Bereitstellen von Vorlagen](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Überprüfen der Bereitstellung

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie im linken Bereich **Ressourcengruppen** aus.

1. Wählen Sie die Ressourcengruppe aus, die Sie im vorherigen Abschnitt erstellt haben. Der Ressourcengruppenname entspricht standardmäßig dem Projektnamen mit dem Zusatz **rg** .

1. Wählen Sie die zuvor erstellte Front Door-Instanz aus, und klicken Sie auf den Link **Front-End-Host** . Dadurch wird ein Webbrowser geöffnet, der Sie an den Back-End-FQDN weiterleitet, den Sie bei der Erstellung definiert haben.

    :::image type="content" source="./media/quickstart-create-front-door-template/front-door-overview.png" alt-text="PowerShell-Bereitstellungsausgabe für die Front Door-Instanz mit Resource Manager-Vorlage":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie den Front Door-Dienst nicht mehr benötigen, löschen Sie die Ressourcengruppe. Dadurch werden die Front Door-Instanz und alle zugehörigen Ressourcen gelöscht.

Rufen Sie zum Löschen der Ressourcengruppe das Cmdlet `Remove-AzResourceGroup` auf:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie eine Front Door-Instanz erstellt.

Fahren Sie mit den Front Door-Tutorials fort, um zu erfahren, wie Sie Ihrer Front Door-Instanz eine benutzerdefinierte Domäne hinzufügen.

> [!div class="nextstepaction"]
> [Front Door-Tutorials](front-door-custom-domain.md)
