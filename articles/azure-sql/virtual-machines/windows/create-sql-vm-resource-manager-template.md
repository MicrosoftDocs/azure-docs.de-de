---
title: Erstellen einer SQL Server-VM mithilfe einer ARM-Vorlage
description: Es wird beschrieben, wie Sie eine SQL Server-Instanz auf einem virtuellen Azure-Computer (VM) erstellen, indem Sie eine Azure Resource Manager-Vorlage (ARM-Vorlage) verwenden.
author: MashaMSFT
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mathoma
ms.date: 06/29/2020
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.openlocfilehash: a20cb27ac91a0b01ed9cc3a5ac4c5c57f90ceda1
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359641"
---
# <a name="quickstart-create-sql-server-vm-using-an-arm-template"></a>Schnellstart: Erstellen einer SQL Server-VM mithilfe einer ARM-Vorlage

Verwenden Sie diese Azure Resource Manager-Vorlage (ARM-Vorlage), um auf einem virtuellen Azure-Computer (VM) eine SQL Server-Instanz bereitzustellen. 

[!INCLUDE [About Azure Resource Manager](../../../../includes/resource-manager-quickstart-introduction.md)]

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, klicken Sie auf die Schaltfläche **In Azure bereitstellen**. Die Vorlage wird im Azure-Portal geöffnet.

[![In Azure bereitstellen](../../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-sql-vm-new-storage%2fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

Für die ARM-Vorlage für die SQL Server-VM benötigen Sie Folgendes:

- Die aktuelle Version der [Azure CLI](/cli/azure/install-azure-cli) bzw. von [PowerShell](/powershell/scripting/install/installing-powershell). 
- Eine vorkonfigurierte [Ressourcengruppe](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) mit einem vorbereiteten [virtuellen Netzwerk](../../../virtual-network/quick-create-portal.md) und einem [Subnetz](../../../virtual-network/virtual-network-manage-subnet.md#add-a-subnet).
- Ein Azure-Abonnement. Sollten Sie kein Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.


## <a name="review-the-template"></a>Überprüfen der Vorlage

Die in dieser Schnellstartanleitung verwendete Vorlage stammt von der Seite mit den [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/101-sql-vm-new-storage/).

:::code language="json" source="~/quickstart-templates/101-sql-vm-new-storage/azuredeploy.json":::

In der Vorlage sind fünf Azure-Ressourcen definiert: 

- [Microsoft.Network/publicIpAddresses](/azure/templates/microsoft.network/publicipaddresses): Erstellt eine öffentliche IP-Adresse. 
- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups): Erstellt eine Netzwerksicherheitsgruppe. 
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces): Konfiguriert die Netzwerkschnittstelle. 
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines): Erstellt einen virtuellen Computer in Azure. 
- [Microsoft.SqlVirtualMachine/SqlVirtualMachines](/azure/templates/microsoft.sqlvirtualmachine/sqlvirtualmachines): Registriert den virtuellen Computer bei der Erweiterung für den SQL-IaaS-Agent. 

Weitere Vorlagen für SQL Server-Instanzen auf virtuellen Azure-Computern finden Sie im Katalog unter [Azure-Schnellstartvorlagen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Sqlvirtualmachine&pageNumber=1&sort=Popular).


## <a name="deploy-the-template"></a>Bereitstellen der Vorlage

1. Klicken Sie auf das folgende Bild, um sich bei Azure anzumelden und eine Vorlage zu öffnen. Mit der Vorlage wird ein virtueller Computer erstellt, auf dem die gewünschte SQL Server-Version installiert ist, und die Registrierung bei der Erweiterung für den SQL-IaaS-Agent durchgeführt. 

   [![In Azure bereitstellen](../../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-sql-vm-new-storage%2fazuredeploy.json)

2. Wählen Sie die folgenden Werte aus, bzw. geben Sie sie ein.

    * **Abonnement**: Wählen Sie ein Azure-Abonnement aus.
    * **Ressourcengruppe**: Die vorbereitete Ressourcengruppe für Ihre SQL Server-VM. 
    * **Region**: Wählen Sie eine Region aus.  Beispiel: **USA, Mitte**.
    * **Name des virtuellen Computers**: Geben Sie einen Namen für die SQL Server-VM ein. 
    * **Größe des virtuellen Computers**: Wählen Sie in der Dropdownliste die passende Größe für Ihren virtuellen Computer aus.
    * **Name des vorhandenen virtuellen Netzwerks**: Geben Sie den Namen des vorbereiteten virtuellen Netzwerks für Ihre SQL Server-VM ein. 
    * **Vorhandene VNET-Ressourcengruppe**: Geben Sie die Ressourcengruppe ein, in der Ihr virtuelles Netzwerk vorbereitet wurde. 
    * **Name des vorhandenen Subnetzes**: Der Name Ihres vorbereiteten Subnetzes. 
    * **Imageangebot**: Wählen Sie das SQL Server- und Windows Server-Image aus, das Ihre geschäftlichen Anforderungen am besten erfüllt. 
    * **SQL-SKU**: Wählen Sie die Edition der SQL Server-SKU aus, die Ihre geschäftlichen Anforderungen am besten erfüllt. 
    * **Administratorbenutzername**: Der Benutzername für den Administrator des virtuellen Computers. 
    * **Administratorkennwort**: Das Kennwort, das vom VM-Administratorkonto verwendet wird. 
    * **Workload-Speichertyp**:  Der Typ von Speicher für die Workload, der Ihre geschäftlichen Anforderungen am besten erfüllt. 
    * **Anzahl von SQL-Datenträgern**:  Die Anzahl von Datenträgern, die von SQL Server für Datendateien verwendet werden.  
    * **Datenpfad**:  Der Pfad für die SQL Server-Datendateien. 
    * **Anzahl von SQL-Protokolldatenträgern**:  Die Anzahl von Datenträgern, die von SQL Server für Protokolldateien verwendet werden. 
    * **Protokollpfad**:  Der Pfad für die SQL Server-Protokolldateien. 
    * **Standort**:  Der Speicherort der gesamten Ressourcen. Für diesen Wert sollte die Standardeinstellung `[resourceGroup().location]` beibehalten werden. 

3. Klicken Sie auf **Überprüfen + erstellen**. Nach der erfolgreichen Bereitstellung der SQL Server-VM erhalten Sie eine Benachrichtigung.

Zum Bereitstellen der Vorlage wird das Azure-Portal verwendet. Neben dem Azure-Portal können Sie auch Azure PowerShell, die Azure-Befehlszeilenschnittstelle (Azure CLI) und die REST-API verwenden. Informationen zu anderen Bereitstellungsmethoden finden Sie unter [Bereitstellen von Vorlagen](../../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Überprüfen der bereitgestellten Ressourcen

Sie können die Azure CLI verwenden, um bereitgestellte Ressourcen zu überprüfen. 


```azurecli-interactive
echo "Enter the resource group where your SQL Server VM exists:" &&
read resourcegroupName &&
az resource list --resource-group $resourcegroupName 
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn sie nicht mehr benötigt wird, kann die Ressourcengruppe über die Azure-Befehlszeilenschnittstelle oder mithilfe von Azure PowerShell gelöscht werden:

# <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Nächste Schritte

Ein Schritt-für-Schritt-Tutorial mit Anleitungen zum Erstellen einer Vorlage finden Sie unter folgendem Link:

> [!div class="nextstepaction"]
> [Tutorial: Erstellen und Bereitstellen Ihrer ersten ARM-Vorlage](../../../azure-resource-manager/templates/template-tutorial-create-first-template.md)

Weitere Möglichkeiten zum Bereitstellen einer SQL Server-VM finden Sie unter: 
- [Azure portal](create-sql-vm-portal.md)
- [PowerShell](create-sql-vm-powershell.md)

Weitere Informationen finden Sie in der [Übersicht über SQL Server auf virtuellen Azure-Computern](sql-server-on-azure-vm-iaas-what-is-overview.md).