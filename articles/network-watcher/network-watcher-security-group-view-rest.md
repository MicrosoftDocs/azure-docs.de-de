---
title: Analysieren der Netzwerksicherheit – Sicherheitsgruppenansicht – Azure-REST-API
titleSuffix: Azure Network Watcher
description: In diesem Artikel wird das Analysieren der Sicherheit eines virtuellen Computers mithilfe der Azure-REST-API und Sicherheitsgruppenansicht beschrieben.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 2efd3e9c9ca97ea3d94b03bd5e440cd24d5da5da
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94960580"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-rest-api"></a>Analysieren der Sicherheit Ihres virtuellen Computers über die Sicherheitsgruppenansicht mithilfe der REST API

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure-Befehlszeilenschnittstelle](network-watcher-security-group-view-cli.md)
> - [REST-API](network-watcher-security-group-view-rest.md)

> [!NOTE]
> Die Sicherheitsgruppenansichts-API wird nicht mehr gepflegt und in Kürze außer Betrieb gesetzt. Verwenden Sie die [Funktion „Effektive Sicherheitsregeln“](./network-watcher-security-group-view-overview.md), die dieselbe Funktionalität bietet. 

Die Sicherheitsgruppenansicht gibt konfigurierte und effektive Netzwerksicherheitsregeln zurück, die auf einen virtuellen Computer angewendet werden. Diese Funktion eignet sich zur Überwachung und Diagnose von Netzwerksicherheitsgruppen und Regeln, die auf einem virtuellen Computer konfiguriert sind, um sicherzustellen, dass Datenverkehr ordnungsgemäß zugelassen oder verweigert wird. In diesem Artikel erfahren Sie, wie Sie die effektiven und angewandten Sicherheitsregeln mithilfe der REST-API auf einen virtuellen Computer abrufen.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Voraussetzungen

In diesem Szenario rufen Sie die Network Watcher-Rest-API auf, um die Sicherheitsgruppenansicht für einen virtuellen Computer zu erhalten. ARMclient dient zum Aufrufen der REST-API mithilfe von PowerShell. Sie finden [ARMClient auf Chocolatey](https://chocolatey.org/packages/ARMClient).

Dieses Szenario setzt voraus, dass Sie die Schritte unter [Erstellen einer Network Watcher-Instanz](network-watcher-create.md) bereits ausgeführt haben. Ferner wird davon ausgegangen, dass eine Ressourcengruppe mit einem gültigen virtuellen Computer vorhanden ist und verwendet werden kann.

## <a name="scenario"></a>Szenario

In dem in diesem Artikel beschriebenen Szenario werden die effektiven und angewandten Sicherheitsregeln für einen bestimmten virtuellen Computer abgerufen.

## <a name="log-in-with-armclient"></a>Anmelden mit ARMClient

```powershell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Abrufen eines virtuellen Computers

Führen Sie das folgende Skript aus, um einen virtuellen Computer zurückzugeben. Der folgende Code benötigt Variablen:

- **subscriptionId:** Die Abonnement-ID kann auch mit dem Cmdlet **Get-AzSubscription** abgerufen werden.
- **resourceGroupName:** der Name einer Ressourcengruppe, die virtuelle Computer enthält.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = '<resource group name>'

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

Die benötigte Information ist die **id** unter dem Typ `Microsoft.Compute/virtualMachines` in der Antwort, wie im folgenden Beispiel dargestellt:

```json
...,
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft
.Network/networkInterfaces/{nicName}"
            }
          ]
        },
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Com
pute/virtualMachines/{vmName}/extensions/CustomScriptExtension"
        }
      ],
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute
/virtualMachines/{vmName}",
      "name": "{vmName}"
    }
  ]
}
```

## <a name="get-security-group-view-for-virtual-machine"></a>Abrufen der Sicherheitsgruppenansicht für den virtuellen Computer

Das folgende Beispiel ruft die Sicherheitsgruppenansicht eines bestimmten virtuellen Computers ab. Die Ergebnisse aus diesem Beispiel können für einen Vergleich mit ursprünglichen Regel- und Sicherheitsdefinitionen verwendet werden, um festzustellen, ob eine Konfigurationsabweichung vorliegt.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"
$networkWatcherName = "<network watcher name>"
$targetUri = "<uri of target resource>" # Example: /subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.compute/virtualMachine/$vmName

$requestBody = @"
{
    'targetResourceId': '${targetUri}'

}
"@
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/securityGroupView?api-version=2016-12-01" $requestBody -verbose
```

## <a name="view-the-response"></a>Anzeigen der Antwort

Das folgende Beispiel ist die vom vorhergehenden Befehl zurückgegebene Antwort. In den Ergebnissen werden alle effektiven und angewendeten Sicherheitsregeln auf dem virtuellen Computer in folgende Gruppen unterteilt angezeigt: **NetworkInterfaceSecurityRules**, **DefaultSecurityRules** und **EffectiveSecurityRules**.

```json

{
  "networkInterfaces": [
    {
      "securityRuleAssociations": {
        "networkInterfaceAssociation": {
          "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "securityRules": [
            {
              "name": "default-allow-rdp",
              "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
              "properties": {
                "provisioningState": "Succeeded",
                "protocol": "TCP",
                "sourcePortRange": "*",
                "destinationPortRange": "3389",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 1000,
                "direction": "Inbound"
              }
            }
          ]
        },
        "defaultSecurityRules": [
          {
            "name": "AllowVnetInBound",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/",
            "properties": {
              "provisioningState": "Succeeded",
              "description": "Allow inbound traffic from all VMs in VNET",
              "protocol": "*",
              "sourcePortRange": "*",
              "destinationPortRange": "*",
              "sourceAddressPrefix": "VirtualNetwork",
              "destinationAddressPrefix": "VirtualNetwork",
              "access": "Allow",
              "priority": 65000,
              "direction": "Inbound"
            }
          },
          ...
        ],
        "effectiveSecurityRules": [
          {
            "name": "DefaultOutboundDenyAll",
            "protocol": "All",
            "sourcePortRange": "0-65535",
            "destinationPortRange": "0-65535",
            "sourceAddressPrefix": "*",
            "destinationAddressPrefix": "*",
            "access": "Deny",
            "priority": 65500,
            "direction": "Outbound"
          },
          ...
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Automatisierung der Überprüfung von Netzwerksicherheitsgruppen finden Sie unter [Überwachen von Netzwerksicherheitsgruppen (NSG) mit Network Watcher](network-watcher-security-group-view-powershell.md).