---
title: Erstellen einer verwalteten Instanz (ARM-Vorlage & PowerShell)
titleSuffix: Azure SQL Managed Instance
description: Verwenden Sie dieses Azure PowerShell-Beispielskript zum Erstellen einer verwalteten Instanz.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-dt-2019, devx-track-azurepowershell
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: 7c364fe7fa0ac4dd70a01a75478289ea861dee7f
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790830"
---
# <a name="use-powershell-with-an-azure-resource-manager-template-to-create-a-managed-instance"></a>Verwenden von PowerShell mit einer Azure Resource Manager-Vorlage zum Erstellen einer verwalteten Instanz

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Sie können eine verwaltete Instanz mit einer Azure PowerShell-Bibliothek und Azure Resource Manager-Vorlagen erstellen.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Wenn Sie PowerShell lokal installieren und nutzen möchten, müssen Sie für dieses Tutorial mindestens die Version 1.4.0 von Azure PowerShell verwenden. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, führen Sie `Connect-AzAccount` aus, um eine Verbindung zu Azure herzustellen.

Mit Azure PowerShell-Befehlen kann die Bereitstellung gestartet werden, indem eine vordefinierte Azure Resource Manager-Vorlage verwendet wird. Die folgenden Eigenschaften können in der Vorlage angegeben werden:

- Name der verwalteten Instanz
- Benutzername und Kennwort des SQL-Administrators
- Größe der Instanz (Anzahl von Kernen und maximale Speichergröße)
- VNET und Subnetz, in dem die Instanz angeordnet wird
- Sortierung der Instanz auf Serverebene (Vorschau).

Der Instanzname, der Benutzername des SQL-Administrators, das VNET/Subnetz und die Sortierung können später nicht mehr geändert werden. Andere Eigenschaften der Instanz können geändert werden.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Beispiel wird vorausgesetzt, dass Sie für Ihre verwaltete Instanz [eine gültige Netzwerkumgebung erstellt](../virtual-network-subnet-create-arm-template.md) oder [ein vorhandenes VNet geändert](../vnet-existing-add-subnet.md) haben. Falls erforderlich, können Sie die Netzwerkumgebung mit einer separaten [Azure Resource Manager-Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) vorbereiten. 


Da im Beispiel die Cmdlets [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) und [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) verwendet werden, vergewissern Sie sich, dass Sie die folgenden PowerShell-Module installiert haben:

```powershell
Install-Module Az.Network
Install-Module Az.Resources
```

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-Vorlage


Speichern Sie das folgende Skript in eine JSON-Datei, und notieren Sie sich den Speicherort der Datei: 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "instance": {
            "type": "string"
        },
        "user": {
            "type": "string"
        },
        "pwd": {
            "type": "securestring"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('instance')]",
            "location": "West Central US",
            "tags": {
                "Description":"GP Instance with custom instance collation - Serbian_Cyrillic_100_CS_AS"
            },
            "sku": {
                "name": "GP_Gen5",
                "tier": "GeneralPurpose"
            },
            "properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
                "collation": "Serbian_Cyrillic_100_CS_AS"
            },
            "type": "Microsoft.Sql/managedInstances",
            "identity": {
                "type": "SystemAssigned"
            },
            "apiVersion": "2015-05-01-preview"
        }
    ]
}
```

Aktualisieren Sie das folgende PowerShell-Skript mit dem korrekten Dateipfad für die zuvor gespeicherte JSON-Datei, und ändern Sie die Namen der Objekte im Skript:

```powershell
$subscriptionId = "ed827499-xxxx-xxxx-xxxx-xxxxxxxxxx"
Select-AzSubscription -SubscriptionId $subscriptionId

# Managed instance properties
$resourceGroup = "rg_mi"
$location = "West Central US"
$name = "managed-instance-name"
$user = "miSqlAdmin"
$secpasswd = ConvertTo-SecureString "<Put some strong password here>" -AsPlainText -Force

# Network configuration
$vNetName = "my_vnet"
$vNetResourceGroup = "rg_mi_vnet"
$subnetName = "ManagedInstances"
$vNet = Get-AzVirtualNetwork -Name $vNetName -ResourceGroupName $vNetResourceGroup
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vNet
$subnetId = $subnet.Id

# Deploy instance using Azure Resource Manager template:
New-AzResourceGroupDeployment  -Name MyDeployment -ResourceGroupName $resourceGroup  `
                                    -TemplateFile 'C:\...\create-managed-instance.json' `
                                    -instance $name -user $user -pwd $secpasswd -subnetId $subnetId
```

Sobald das Skript abgeschlossen ist, kann auf die verwaltete Instanz von allen Azure-Diensten und der konfigurierten IP-Adresse aus zugegriffen werden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure PowerShell finden Sie in der [Azure PowerShell-Dokumentation](/powershell/azure/).

Weitere PowerShell-Skriptbeispiele für die verwaltete Azure SQL-Instanz finden Sie unter [PowerShell-Skripts für die verwaltete Azure SQL-Instanz](../../database/powershell-script-content-guide.md).