---
title: 'Schnellstart: Erstellen einer Azure Firewall-Instanz mit Verfügbarkeitszonen – Resource Manager-Vorlage'
description: 'In dieser Schnellstartanleitung stellen Sie Azure Firewall mithilfe einer Vorlage bereit. Das virtuelle Netzwerk verfügt über ein VNET mit drei Subnetzen. Zwei virtuelle Windows Server-Computer werden bereitgestellt: eine Jumpbox und ein Server.'
services: firewall
author: vhorne
ms.service: firewall
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 08/28/2020
ms.author: victorh
ms.openlocfilehash: 478f3454a728871040cdbbf9f817394cffe6b82f
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660252"
---
# <a name="quickstart-deploy-azure-firewall-with-availability-zones---arm-template"></a>Schnellstart: Bereitstellen von Azure Firewall mit Verfügbarkeitszonen – ARM-Vorlage

In dieser Schnellstartanleitung verwenden Sie eine Azure Resource Manager-Vorlage (ARM-Vorlage), um eine Azure Firewall-Instanz in drei Verfügbarkeitszonen bereitzustellen.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Die Vorlage erstellt eine Testnetzwerkumgebung mit einer Firewall. Das Netzwerk verfügt über ein virtuelles Netzwerk (VNET) mit drei Subnetzen: *AzureFirewallSubnet*, *ServersSubnet* und *JumpboxSubnet*. Die Subnetze *ServersSubnet* und *JumpboxSubnet* verfügen jeweils über einen virtuellen Windows Server-Computer mit zwei Kernen.

Die Firewall befindet sich im Subnetz *AzureFirewallSubnet*, und der Zugriff auf `www.microsoft.com` wird durch eine Anwendungsregelsammlung mit einer einzelnen Regel ermöglicht.

Es wird eine benutzerdefinierte Route erstellt, die den Netzwerkverkehr vom Subnetz *ServersSubnet* durch die Firewall leitet, wo die Firewallregeln angewendet werden.

Weitere Informationen zu Azure Firewall finden Sie unter [Bereitstellen und Konfigurieren von Azure Firewall über das Azure-Portal](tutorial-firewall-deploy-portal.md).

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-with-zones-sandbox%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="review-the-template"></a>Überprüfen der Vorlage

Mit dieser Vorlage werden eine Azure Firewall-Instanz mit Verfügbarkeitszonen und die erforderlichen unterstützenden Ressourcen für die Azure Firewall-Instanz erstellt.

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-azurefirewall-with-zones-sandbox).

:::code language="json" source="~/quickstart-templates/101-azurefirewall-with-zones-sandbox/azuredeploy.json":::

In der Vorlage sind mehrere Azure-Ressourcen definiert:

- [**Microsoft.Storage/storageAccounts**](/azure/templates/microsoft.storage/storageAccounts)
- [**Microsoft.Network/routeTables**](/azure/templates/microsoft.network/routeTables)
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft.Network/azureFirewalls**](/azure/templates/microsoft.network/azureFirewalls)

## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

Stellen Sie die ARM-Vorlage in Azure bereit:

1. Wählen Sie **Bereitstellung in Azure** aus, um sich bei Azure anzumelden und die Vorlage zu öffnen. Durch die Vorlage werden eine Azure Firewall-Instanz, die Netzwerkinfrastruktur und zwei virtuelle Computer erstellt.

   [![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurefirewall-with-zones-sandbox%2Fazuredeploy.json)

2. Geben Sie im Portal auf der Seite **Create a sandbox setup of Azure Firewall with Zones** (Erstellen eines Sandboxsetups von Azure Firewall mit Zonen) die folgenden Werte ein, oder wählen Sie diese aus:
   - **Ressourcengruppe**: Wählen Sie **Neu erstellen** aus, und geben Sie einen Namen für die Ressourcengruppe an. Klicken Sie auf **OK**. 
   - **Name des virtuellen Netzwerks**: Geben Sie den Namen für das neue VNET ein.
   - **Administratorbenutzername**: Geben Sie einen Benutzernamen für das Administratorbenutzerkonto ein.
   - **Administratorkennwort**: Geben Sie ein Administratorkennwort ein.

3. Lesen Sie die Geschäftsbedingungen, und wählen Sie anschließend die Option **Ich stimme den oben genannten Geschäftsbedingungen zu** und dann **Kaufen** aus. Die Bereitstellung kann zehn Minuten oder länger dauern.

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

Untersuchen Sie zuerst die Ressourcen, die mit der Firewall erstellt wurden.

Weitere Informationen zur JSON-Syntax und den Eigenschaften für eine Firewall in einer Vorlage finden Sie unter [Microsoft.Network/azureFirewalls](/azure/templates/microsoft.network/azurefirewalls).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie sie nicht mehr benötigen, können Sie die Ressourcengruppe, die Firewall und alle zugehörigen Ressourcen entfernen, indem Sie den PowerShell-Befehl `Remove-AzResourceGroup`ausführen. Zum Entfernen einer Ressourcengruppe mit dem Namen *MyResourceGroup* führen Sie diesen Befehl aus:

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

Entfernen Sie die Ressourcengruppe und die Firewall nicht, wenn Sie mit dem Tutorial zur Firewallüberwachung fortfahren möchten. 

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes können Sie die Azure Firewall-Protokolle überwachen.

> [!div class="nextstepaction"]
> [Tutorial: Überwachen von Azure Firewall-Protokollen](./firewall-diagnostics.md)