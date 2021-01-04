---
title: 'Schnellstart: Erstellen einer Traffic Manager-Instanz mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage)'
description: In diesem Schnellstartartikel wird beschrieben, wie Sie mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage) ein Azure Traffic Manager-Profil erstellen.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: duau
ms.date: 09/01/2020
ms.openlocfilehash: ec569781a6318062810358c2c5e17ba71efc4f71
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92676002"
---
# <a name="quickstart-create-a-traffic-manager-profile-using-an-arm-template"></a>Schnellstart: Erstellen eines Traffic Manager-Profils mithilfe einer ARM-Vorlage

In dieser Schnellstartanleitung wird beschrieben, wie Sie eine Azure Resource Manager-Vorlage (ARM-Vorlage) verwenden, um mithilfe der leistungsorientierten Routingmethode ein Traffic Manager-Profil mit externen Endpunkten zu erstellen.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-traffic-manager-external-endpoint%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-traffic-manager-external-endpoint).

:::code language="json" source="~/quickstart-templates/101-traffic-manager-external-endpoint/azuredeploy.json":::

In der Vorlage ist eine einzelne Azure-Ressource definiert:

* [**Microsoft.Network/trafficManagerProfiles**](/azure/templates/microsoft.network/trafficmanagerprofiles)

Weitere Vorlagen zu Azure Traffic Manager finden Sie unter [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

1. Wählen Sie **Try it** (Ausprobieren) im folgenden Codeblock aus, um Azure Cloud Shell zu öffnen. Folgen Sie dann den Anweisungen, um sich bei Azure anzumelden.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-traffic-manager-external-endpoint/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Warten Sie, bis die Aufforderung in der Konsole angezeigt wird.

1. Wählen Sie **Copy** (Kopieren) im vorherigen Codeblock aus, um das PowerShell-Skript zu kopieren.

1. Klicken Sie mit der rechten Maustaste auf den Shellkonsolenbereich, und wählen Sie **Einfügen** aus.

1. Gehen Sie die Werte ein.

    Bei der Vorlagenbereitstellung wird ein Profil mit zwei externen Endpunkten erstellt. **Endpoint1** verwendet den Zielendpunkt `www.microsoft.com` mit dem Standort in **Europa, Norden**. **Endpoint2** verwendet den Zielendpunkt `docs.microsoft.com` mit dem Standort in **USA, Süden-Mitte**.

    Der Ressourcengruppenname ist der Projektname mit dem Zusatz **rg**.

    > [!NOTE]
    > **uniqueDNSname** muss ein global eindeutiger Name sein, damit die Vorlage erfolgreich bereitgestellt werden kann. Tritt bei der Bereitstellung ein Fehler auf, beginnen Sie erneut mit Schritt 1.

    Die Bereitstellung der Vorlage dauert einige Minuten. Nach Abschluss des Vorgangs sieht die Ausgabe in etwa wie folgt aus:

    :::image type="content" source="./media/quickstart-create-traffic-manager-profile/traffic-manager-arm-powershell-output.png" alt-text="Azure Traffic Manager-Resource Manager-Vorlage: PowerShell-Bereitstellungsausgabe":::

Azure PowerShell wird verwendet, um die Vorlage bereitzustellen. Neben Azure PowerShell können Sie auch das Azure-Portal, die Azure-CLI und die REST-API verwenden. Informationen zu anderen Bereitstellungsmethoden finden Sie unter [Bereitstellen von Vorlagen](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Überprüfen der Bereitstellung

1. Ermitteln Sie den DNS-Namen des Traffic Manager-Profils mit [Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile).

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name ExternalEndpointExample -ResourceGroupName $resourceGroupName | Select RelativeDnsName
    ```

    Kopieren Sie den Wert von **RelativeDnsName**. Der DNS-Name Ihres Traffic Manager-Profils lautet `<relativednsname>.trafficmanager.net`.

1. Führen Sie in einer lokalen PowerShell-Instanz den folgenden Befehl aus. Ersetzen Sie dabei die Variable **{relativeDNSname}** durch `<relativednsname>.trafficmanager.net`.

    ```powershell
    Resolve-DnsName -Name {relativeDNSname} | Select-Object NameHost | Select -First 1
    ```

    Abhängig davon, welche Region näher zu Ihnen liegt, sollte für „NameHost“ entweder `www.microsoft.com` oder `docs.microsoft.com` zurückgegeben werden.

1. Um zu überprüfen, ob die Auflösung in den anderen Endpunkt möglich ist, deaktivieren Sie den Endpunkt für das im letzten Schritt abgerufene Ziel. Ersetzen Sie **{endpointName}** entweder durch **endpoint1** oder durch **endpoint2** , um das Ziel für `www.microsoft.com` oder `docs.microsoft.com` zu deaktivieren.

    ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name {endpointName} -Type ExternalEndpoints -ProfileName ExternalEndpointExample -ResourceGroupName $resourceGroupName -Force
    ```

1. Führen Sie den Befehl aus Schritt 2 erneut in einer lokalen PowerShell-Instanz aus. Dieses Mal sollten Sie den anderen NameHost-Wert für den anderen Endpunkt erhalten.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie das Traffic Manager-Profil nicht mehr benötigen, löschen Sie die Ressourcengruppe. Dadurch werden das Traffic Manager-Profil und alle zugehörigen Ressourcen entfernt.

Rufen Sie zum Löschen der Ressourcengruppe das Cmdlet `Remove-AzResourceGroup` auf:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie ein Traffic Manager-Profil erstellt.

Weitere Informationen zum Weiterleiten des Datenverkehrs finden Sie in den Tutorials zu Traffic Manager.

> [!div class="nextstepaction"]
> [Traffic Manager-Tutorials](tutorial-traffic-manager-improve-website-response.md)
