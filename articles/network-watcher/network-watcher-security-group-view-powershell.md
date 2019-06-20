---
title: Analysieren der Netzwerksicherheit mit der Azure Network Watcher-Sicherheitsgruppenansicht – PowerShell | Microsoft-Dokumentation
description: In diesem Artikel wird die Verwendung von PowerShell zum Analysieren der Sicherheit eines virtuellen Computers über die Sicherheitsgruppenansicht beschrieben.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 04e76b49-6a1b-4d0f-9a9b-51cf2f4df5a2
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 389eab13193b313d8609bfa54d5e0dc42329f5ad
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64720780"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-powershell"></a>Analysieren der Sicherheit Ihres virtuellen Computers über die Sicherheitsgruppenansicht mit PowerShell

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-security-group-view-powershell.md)
> - [Azure-Befehlszeilenschnittstelle](network-watcher-security-group-view-cli.md)
> - [REST-API](network-watcher-security-group-view-rest.md)

Die Sicherheitsgruppenansicht gibt konfigurierte und effektive Netzwerksicherheitsregeln zurück, die auf einen virtuellen Computer angewendet werden. Diese Funktion eignet sich zur Überwachung und Diagnose von Netzwerksicherheitsgruppen und Regeln, die auf einem virtuellen Computer konfiguriert sind, um sicherzustellen, dass Datenverkehr ordnungsgemäß zugelassen oder verweigert wird. In diesem Artikel erfahren Sie, wie Sie die konfigurierten und effektiven Sicherheitsregeln mit PowerShell auf einem virtuellen Computer abrufen.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Voraussetzungen

In diesem Szenario verwenden Sie das Cmdlet `Get-AzNetworkWatcherSecurityGroupView` zum Abrufen der Sicherheitsregelinformationen.

Dieses Szenario setzt voraus, dass Sie die Schritte unter [Erstellen einer Network Watcher-Instanz](network-watcher-create.md) bereits ausgeführt haben.

## <a name="scenario"></a>Szenario

In dem in diesem Artikel beschriebenen Szenario werden die konfigurierten und effektiven Sicherheitsregeln für einen bestimmten virtuellen Computer abgerufen.

## <a name="retrieve-network-watcher"></a>Abrufen von Network Watcher

Der erste Schritt besteht im Abrufen der Network Watcher-Instanz. Diese Variable wird an das Cmdlet `Get-AzNetworkWatcherSecurityGroupView` übergeben.

```powershell
$nw = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" }
$networkWatcher = Get-AzNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
```

## <a name="get-a-vm"></a>Abrufen eines virtuellen Computers

Zum Ausführen des Cmdlets `Get-AzNetworkWatcherSecurityGroupView` ist ein virtueller Computer erforderlich. Im folgenden Beispiel wird ein VM-Objekt abgerufen.

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name testvm1
```

## <a name="retrieve-security-group-view"></a>Abrufen der Sicherheitsgruppenansicht

Im nächsten Schritt wird das Ergebnis der Sicherheitsgruppenansicht abgerufen.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="viewing-the-results"></a>Anzeigen der Ergebnisse

Das folgende Beispiel ist eine verkürzte Antwort der zurückgegebenen Ergebnisse. In den Ergebnissen werden alle effektiven und angewendeten Sicherheitsregeln auf dem virtuellen Computer in folgende Gruppen unterteilt angezeigt: **NetworkInterfaceSecurityRules**, **DefaultSecurityRules** und **EffectiveSecurityRules**.

```
NetworkInterfaces : [
                      {
                        "NetworkInterfaceSecurityRules": [
                          {
                            "Name": "default-allow-rdp",
                            "Etag": "W/\"d4c411d4-0d62-49dc-8092-3d4b57825740\"",
                            "Id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/securityRules/default-allow-rdp",
                            "Protocol": "TCP",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "3389",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "Access": "Allow",
                            "Priority": 1000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "DefaultSecurityRules": [
                          {
                            "Name": "AllowVnetInBound",
                            "Id": "/subscriptions00000000-0000-0000-0000-000000000000/resourceGroups/testrg2/providers/Microsoft.Network/networkSecurityGroups/testvm2-nsg/defaultSecurityRules/",
                            "Description": "Allow inbound traffic from all VMs in VNET",
                            "Protocol": "*",
                            "SourcePortRange": "*",
                            "DestinationPortRange": "*",
                            "SourceAddressPrefix": "VirtualNetwork",
                            "DestinationAddressPrefix": "VirtualNetwork",
                            "Access": "Allow",
                            "Priority": 65000,
                            "Direction": "Inbound",
                            "ProvisioningState": "Succeeded"
                          }
                          ...
                        ],
                        "EffectiveSecurityRules": [
                          {
                            "Name": "DefaultOutboundDenyAll",
                            "Protocol": "All",
                            "SourcePortRange": "0-65535",
                            "DestinationPortRange": "0-65535",
                            "SourceAddressPrefix": "*",
                            "DestinationAddressPrefix": "*",
                            "ExpandedSourceAddressPrefix": [],
                            "ExpandedDestinationAddressPrefix": [],
                            "Access": "Deny",
                            "Priority": 65500,
                            "Direction": "Outbound"
                          },
                          ...
                        ]
                      }
                    ]
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Automatisierung der Überprüfung von Netzwerksicherheitsgruppen finden Sie unter [Automate NSG auditing with Azure Network Watcher Security group view](network-watcher-nsg-auditing-powershell.md) (Automatisieren der NSG-Überwachung mit Azure Network Watcher-Sicherheitsgruppenansicht).


