---
title: Automatisieren der NSG-Überwachung mit der Azure Network Watcher-Sicherheitsgruppenansicht | Microsoft-Dokumentation
description: Diese Seite enthält Anweisungen zum Konfigurieren der Überwachung einer Netzwerksicherheitsgruppe.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 78a01bcf-74fe-402a-9812-285f3501f877
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 016d68de90088314250fef1fcfdb57d7f155ef79
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64707168"
---
# <a name="automate-nsg-auditing-with-azure-network-watcher-security-group-view"></a>Automatisieren der NSG-Überwachung mit der Azure Network Watcher-Sicherheitsgruppenansicht

Kunden stehen häufig vor der Herausforderung, den Sicherheitsstatus ihrer Infrastruktur abzufragen. Diese Abfrage muss auch für die virtuellen Computer in Azure durchgeführt werden. Es ist wichtig, ein ähnliches Sicherheitsprofil basierend auf den Regeln von Netzwerksicherheitsgruppen (NSGs) anzuwenden. In der Sicherheitsgruppenansicht können Sie jetzt die Liste der Regeln abrufen, die innerhalb einer NSG auf einen virtuellen Computer angewendet sind. Sie können ein Gold-Sicherheitsprofil für die NSG definieren, die Sicherheitsgruppenansicht im Wochenrhythmus initiieren, die Ausgabe mit dem Gold-Profil vergleichen und einen Bericht erstellen. Auf diese Weise können Sie problemlos alle virtuellen Computer identifizieren, die nicht dem vorgeschriebenen Sicherheitsprofil entsprechen.

Wenn Sie mit Netzwerksicherheitsgruppen nicht vertraut sind, informieren Sie sich in der [Übersicht über die Netzwerksicherheit](../virtual-network/security-overview.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Voraussetzungen

In diesem Szenario vergleichen Sie eine bekannte gute Grundlage mit den für einen virtuellen Computer zurückgegebenen Ergebnissen der Sicherheitsgruppenansicht.

Dieses Szenario setzt voraus, dass Sie die Schritte unter [Erstellen einer Network Watcher-Instanz](network-watcher-create.md) bereits ausgeführt haben, um eine Network Watcher-Instanz zu erstellen. Ferner wird davon ausgegangen, dass eine Ressourcengruppe mit einem gültigen virtuellen Computer vorhanden ist und verwendet werden kann.

## <a name="scenario"></a>Szenario

Mit dem in diesem Artikel beschriebenen Szenario wird die Sicherheitsgruppenansicht für einen virtuellen Computer abgerufen.

In diesem Szenario führen Sie Folgendes durch:

- Abrufen eines bekannten guten Regelsatzes
- Abrufen eines virtuellen Computers mit der Rest-API
- Abrufen der Sicherheitsgruppenansicht für den virtuellen Computer
- Auswerten der Antwort

## <a name="retrieve-rule-set"></a>Abrufen des Regelsatzes

Der erste Schritt in diesem Beispiel ist das Verwenden einer vorhandenen Baseline. Das folgende Beispiel sind einige JSON-Daten, die mithilfe des Cmdlets `Get-AzNetworkSecurityGroup` aus einer vorhandenen Netzwerksicherheitsgruppe extrahiert wurden und als Baseline für dieses Beispiel verwendet werden.

```json
[
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "3389",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1000,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "default-allow-rdp",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/default-allow-rdp"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "111",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1010,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "MyRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/MyRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "112",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1020,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "My2ndRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/My2ndRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "5672",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Deny",
        "Priority":  1030,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "ThisRuleNeedsToStay",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/ThisRuleNeedsToStay"
    }
]
```

## <a name="convert-rule-set-to-powershell-objects"></a>Konvertieren des Regelsatzes in PowerShell-Objekte

In diesem Schritt lesen wir eine JSON-Datei, die zuvor mit den Regeln erstellt wurde, die für dieses Beispiel in der Netzwerksicherheitsgruppe erwartet werden.

```powershell
$nsgbaserules = Get-Content -Path C:\temp\testvm1-nsg.json | ConvertFrom-Json
```

## <a name="retrieve-network-watcher"></a>Abrufen von Network Watcher

Der nächste Schritt besteht im Abrufen der Network Watcher-Instanz. Die Variable `$networkWatcher` wird an das Cmdlet `AzNetworkWatcherSecurityGroupView` übergeben.

```powershell
$nw = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-vm"></a>Abrufen eines virtuellen Computers

Zum Ausführen des Cmdlets `Get-AzNetworkWatcherSecurityGroupView` ist ein virtueller Computer erforderlich. Im folgenden Beispiel wird ein VM-Objekt abgerufen.

```powershell
$VM = Get-AzVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="retrieve-security-group-view"></a>Abrufen der Sicherheitsgruppenansicht

Im nächsten Schritt wird das Ergebnis der Sicherheitsgruppenansicht abgerufen. Dieses Ergebnis wird mit der „Baseline“-JSON-Datei verglichen, die weiter oben gezeigt wurde.

```powershell
$secgroup = Get-AzNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="analyzing-the-results"></a>Analysieren der Ergebnisse

Die Antwort ist nach Netzwerkschnittstellen gruppiert. Die verschiedenen zurückgegebenen Regeltypen sind geltende Standardsicherheitsregeln. Das Ergebnis wird je nach seiner Anwendung entweder auf ein Subnetz oder eine virtuelle NIC weiter aufgeschlüsselt.

Das folgende PowerShell-Skript vergleicht die Ergebnisse der Sicherheitsgruppenansicht mit einer vorhandenen Ausgabe einer NSG. Das folgende ist ein einfaches Beispiel dafür, wie die Ergebnisse mithilfe des Cmdlets `Compare-Object` verglichen werden können.

```powershell
Compare-Object -ReferenceObject $nsgbaserules `
-DifferenceObject $secgroup.NetworkInterfaces[0].NetworkInterfaceSecurityRules `
-Property Name,Description,Protocol,SourcePortRange,DestinationPortRange,SourceAddressPrefix,DestinationAddressPrefix,Access,Priority,Direction
```

Das folgende Beispiel ist das Ergebnis. Sie können sehen, dass zwei der Regeln aus dem ersten Regelsatz nicht im Vergleich vorhanden waren.

```
Name                     : My2ndRuleDoNotDelete
Description              : 
Protocol                 : *
SourcePortRange          : *
DestinationPortRange     : 112
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Allow
Priority                 : 1020
Direction                : Inbound
SideIndicator            : <=

Name                     : ThisRuleNeedsToStay
Description              : 
Protocol                 : TCP
SourcePortRange          : *
DestinationPortRange     : 5672
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Deny
Priority                 : 1030
Direction                : Inbound
SideIndicator            : <=
```

## <a name="next-steps"></a>Nächste Schritte

Wenn Einstellungen geändert wurden, finden Sie unter [Verwalten von Netzwerksicherheitsgruppen](../virtual-network/manage-network-security-group.md) Informationen zum Ermitteln der fraglichen Netzwerksicherheitsgruppe und Sicherheitsregeln.













