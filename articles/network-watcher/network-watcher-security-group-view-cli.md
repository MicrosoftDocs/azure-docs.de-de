---
title: Analysieren der Netzwerksicherheit mit der Azure Network Watcher-Sicherheitsgruppenansicht – Azure CLI | Microsoft-Dokumentation
description: In diesem Artikel wird die Verwendung der Azure CLI zum Analysieren der Sicherheit eines virtuellen Computers über die Sicherheitsgruppenansicht beschrieben.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: a986ff4f-7e0c-4994-95e1-4ac824986500
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 68222a90eb60ab4f84a34b5e46833128ea081ec1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64724439"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli"></a>Analysieren der Sicherheit Ihres virtuellen Computers über die Sicherheitsgruppenansicht mithilfe der Azure CLI

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure-Befehlszeilenschnittstelle](network-watcher-security-group-view-cli.md)
> - [REST-API](network-watcher-security-group-view-rest.md)

Die Sicherheitsgruppenansicht gibt konfigurierte und effektive Netzwerksicherheitsregeln zurück, die auf einen virtuellen Computer angewendet werden. Diese Funktion eignet sich zur Überwachung und Diagnose von Netzwerksicherheitsgruppen und Regeln, die auf einem virtuellen Computer konfiguriert sind, um sicherzustellen, dass Datenverkehr ordnungsgemäß zugelassen oder verweigert wird. In diesem Artikel erfahren Sie, wie Sie die konfigurierten und effektiven Sicherheitsregeln mithilfe der Azure CLI auf einen virtuellen Computer abrufen.

Um die Schritte in diesem Artikel ausführen zu können, müssen Sie [die Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows (CLI) installieren](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Voraussetzungen

Dieses Szenario setzt voraus, dass Sie die Schritte unter [Erstellen einer Network Watcher-Instanz](network-watcher-create.md) bereits ausgeführt haben.

## <a name="scenario"></a>Szenario

In dem in diesem Artikel beschriebenen Szenario werden die konfigurierten und effektiven Sicherheitsregeln für einen bestimmten virtuellen Computer abgerufen.

## <a name="get-a-vm"></a>Abrufen eines virtuellen Computers

Ein virtueller Computer ist zum Ausführen des Cmdlets `vm list` erforderlich. Der folgende Befehl listet die virtuellen Computer in einer Ressourcengruppe auf:

```azurecli
az vm list -resource-group resourceGroupName
```

Sobald Sie den virtuellen Computer kennen, können Sie die Ressourcen-ID mit dem Cmdlet `vm show` abrufen:

```azurecli
az vm show -resource-group resourceGroupName -name virtualMachineName
```

## <a name="retrieve-security-group-view"></a>Abrufen der Sicherheitsgruppenansicht

Im nächsten Schritt wird das Ergebnis der Sicherheitsgruppenansicht abgerufen.

```azurecli
az network watcher show-security-group-view --resource-group resourceGroupName --vm vmName
```

## <a name="viewing-the-results"></a>Anzeigen der Ergebnisse

Das folgende Beispiel ist eine verkürzte Antwort der zurückgegebenen Ergebnisse. In den Ergebnissen werden alle effektiven und angewendeten Sicherheitsregeln auf dem virtuellen Computer in folgende Gruppen unterteilt angezeigt: **NetworkInterfaceSecurityRules**, **DefaultSecurityRules** und **EffectiveSecurityRules**.

```json
{
  "networkInterfaces": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
      "resourceGroup": "{resourceGroupName}",
      "securityRuleAssociations": {
        "defaultSecurityRules": [
          {
            "access": "Allow",
            "description": "Allow inbound traffic from all VMs in VNET",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "*",
            "direction": "Inbound",
            "etag": null,
            "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups//providers/Microsoft.Network/networkSecurityGroups/{nsgName}/defaultSecurityRules/AllowVnetInBound",
            "name": "AllowVnetInBound",
            "priority": 65000,
            "protocol": "*",
            "provisioningState": "Succeeded",
            "resourceGroup": "",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "*"
          }...
        ],
        "effectiveSecurityRules": [
          {
            "access": "Deny",
            "destinationAddressPrefix": "*",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": null,
            "expandedSourceAddressPrefix": null,
            "name": "DefaultOutboundDenyAll",
            "priority": 65500,
            "protocol": "All",
            "sourceAddressPrefix": "*",
            "sourcePortRange": "0-65535"
          },
          {
            "access": "Allow",
            "destinationAddressPrefix": "VirtualNetwork",
            "destinationPortRange": "0-65535",
            "direction": "Outbound",
            "expandedDestinationAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "expandedSourceAddressPrefix": [
              "10.1.0.0/24",
              "168.63.129.16/32"
            ],
            "name": "DefaultRule_AllowVnetOutBound",
            "priority": 65000,
            "protocol": "All",
            "sourceAddressPrefix": "VirtualNetwork",
            "sourcePortRange": "0-65535"
          },...
        ],
        "networkInterfaceAssociation": {
          "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{nicName}",
          "resourceGroup": "{resourceGroupName}",
          "securityRules": [
            {
              "access": "Allow",
              "description": null,
              "destinationAddressPrefix": "*",
              "destinationPortRange": "3389",
              "direction": "Inbound",
              "etag": "W/\"efb606c1-2d54-475a-ab20-da3f80393577\"",
              "id": "/subscriptions/00000000-0000-0000-0000-0000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}/securityRules/default-allow-rdp",
              "name": "default-allow-rdp",
              "priority": 1000,
              "protocol": "TCP",
              "provisioningState": "Succeeded",
              "resourceGroup": "{resourceGroupName}",
              "sourceAddressPrefix": "*",
              "sourcePortRange": "*"
            }
          ]
        },
        "subnetAssociation": null
      }
    }
  ]
}
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Automatisierung der Überprüfung von Netzwerksicherheitsgruppen finden Sie unter [Überwachen von Netzwerksicherheitsgruppen (NSG) mit Network Watcher](network-watcher-nsg-auditing-powershell.md).

Weitere Informationen zu den Sicherheitsregeln, die auf Ihre Netzwerkressourcen angewendet werden, finden Sie unter [Übersicht über die Sicherheitsgruppenansicht](network-watcher-security-group-view-overview.md).
