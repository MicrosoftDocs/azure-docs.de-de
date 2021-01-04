---
title: 'Schnellstart: Erstellen einer Azure DNS-Zone und eines -Eintrags: Azure Resource Manager-Vorlage (ARM-Vorlage)'
titleSuffix: Azure DNS
description: Erfahren Sie, wie Sie eine DNS-Zone und einen DNS-Eintrag in Azure DNS erstellen. Dies ist ein schrittweiser Schnellstart zum Erstellen und Verwalten Ihrer ersten DNS-Zone und Ihres ersten DNS-Eintrags mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage).
services: dns
author: duongau
ms.service: dns
ms.topic: quickstart
ms.date: 09/8/2020
ms.author: duau
ms.custom: subject-armqs
ms.openlocfilehash: 24460167e2279e7d3001d0bc16d050beb5b55289
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791000"
---
# <a name="quickstart-create-an-azure-dns-zone-and-record-using-an-arm-template"></a>Schnellstart: Erstellen einer Azure DNS-Zone und eines DNS-Eintrags mithilfe einer ARM-Vorlage

In dieser Schnellstartanleitung wird beschrieben, wie Sie mithilfe einer Azure Resource Manager-Vorlage (ARM-Vorlage) eine DNS-Zone und den zugehörigen `A`-Eintrag erstellen.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-dns-new-zone%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-azure-dns-new-zone).

In dieser Schnellstartanleitung erstellen Sie eine eindeutige DNS-Zone mit dem Suffix `azurequickstart.org`. Ein `A`-Eintrag, der auf zwei IP-Adressen verweist, wird ebenfalls in der Zone platziert.

:::code language="json" source="~/quickstart-templates/101-azure-dns-new-zone/azuredeploy.json":::

In der Vorlage wurden zwei Azure-Ressourcen definiert:

- [**Microsoft.Network/dnsZones**](/azure/templates/microsoft.network/dnsZones)
- [**Microsoft.Network/dnsZones/A**](/azure/templates/microsoft.network/dnsZones/A): Wird zum Erstellen eines `A`-Datensatzes in der Zone verwendet.

Weitere Vorlagen zu Azure Traffic Manager finden Sie unter [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

1. Wählen Sie **Try it** (Ausprobieren) im folgenden Codeblock aus, um Azure Cloud Shell zu öffnen. Folgen Sie dann den Anweisungen, um sich bei Azure anzumelden.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-dns-new-zone/azuredeploy.json"

    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri

    Read-Host -Prompt "Press [ENTER] to continue ..."
    ```

    Warten Sie, bis die Aufforderung in der Konsole angezeigt wird.

1. Wählen Sie **Copy** (Kopieren) im vorherigen Codeblock aus, um das PowerShell-Skript zu kopieren.

1. Klicken Sie mit der rechten Maustaste auf den Shellkonsolenbereich, und wählen Sie **Einfügen** aus.

1. Gehen Sie die Werte ein.

    Die Vorlagenbereitstellung erstellt eine Zone mit einem `A`-Eintrag, der auf zwei IP-Adressen verweist. Der Ressourcengruppenname ist der Projektname mit dem Zusatz **rg**.

    Die Bereitstellung der Vorlage dauert einige Sekunden. Nach Abschluss des Vorgangs sieht die Ausgabe in etwa wie folgt aus:

    :::image type="content" source="./media/dns-getstarted-template/create-dns-zone-powershell-output.png" alt-text="Resource Manager-Vorlage für Azure DNS-Zone: PowerShell-Bereitstellungsausgabe":::

Azure PowerShell wird verwendet, um die Vorlage bereitzustellen. Neben Azure PowerShell können Sie auch das Azure-Portal, die Azure-CLI und die REST-API verwenden. Informationen zu anderen Bereitstellungsmethoden finden Sie unter [Bereitstellen von Vorlagen](../azure-resource-manager/templates/deploy-portal.md).

## <a name="validate-the-deployment"></a>Überprüfen der Bereitstellung

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie im linken Bereich **Ressourcengruppen** aus.

1. Wählen Sie die Ressourcengruppe aus, die Sie im vorherigen Abschnitt erstellt haben. Der Ressourcengruppenname entspricht standardmäßig dem Projektnamen mit dem Zusatz **rg**.

1. Die Ressourcengruppe sollte die folgenden Ressourcen enthalten:

    :::image type="content" source="./media/dns-getstarted-template/resource-group-dns-zone.png" alt-text="Ressourcengruppe der DNS-Zonenbereitstellung":::

1. Wählen Sie die DNS-Zone mit dem Suffix `azurequickstart.org` aus, um zu überprüfen, ob die Zone ordnungsgemäß mit einem `A`-Eintrag erstellt wurde, der auf die Werte `1.2.3.4` und `1.2.3.5` verweist.

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-overview.png" alt-text="DNS-Zonenbereitstellung":::

1. Kopieren Sie einen der Namen der Namenserver aus dem vorherigen Schritt.

1. Öffnen Sie eine Eingabeaufforderung, und führen Sie den folgenden Befehl aus:

   ```cmd
   nslookup www.<dns zone name> <name server name>
   ```

   Beispiel:

   ```cmd
   nslookup www.2lwynbseszpam.azurequickstart.org ns1-09.azure-dns.com.
   ```

   Es sollte ein Screenshot angezeigt werden, der in etwa wie folgt aussieht:

    :::image type="content" source="./media/dns-getstarted-template/dns-zone-validation.png" alt-text="DNS zone nslookup":::

Der Hostname `www.2lwynbseszpam.azurequickstart.org` wird gemäß Ihrer Konfiguration in `1.2.3.4` und `1.2.3.5` aufgelöst. Mit diesem Ergebnis wird bestätigt, dass die Namensauflösung richtig funktioniert.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die mit der DNS-Zone erstellten Ressourcen nicht mehr benötigen, löschen Sie die Ressourcengruppe. Dadurch werden die DNS-Zone und alle zugehörigen Ressourcen entfernt.

Rufen Sie zum Löschen der Ressourcengruppe das Cmdlet `Remove-AzResourceGroup` auf:

```azurepowershell-interactive
Remove-AzResourceGroup -Name <your resource group name>
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie Folgendes erstellt:

- DNS-Zone
- `A`-Eintrag

Nachdem Sie nun Ihre erste DNS-Zone und Ihren ersten Eintrag mit einer ARM-Vorlage erstellt haben, können Sie Einträge für eine Web-App in einer benutzerdefinierten Domäne erstellen.

> [!div class="nextstepaction"]
> [Erstellen von DNS-Einträgen für eine Web-App in einer benutzerdefinierten Domäne](./dns-web-sites-custom-domain.md)
