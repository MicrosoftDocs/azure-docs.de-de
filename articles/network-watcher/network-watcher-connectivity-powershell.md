---
title: Problembehandlung bei Verbindungen – Azure PowerShell
titleSuffix: Azure Network Watcher
description: Hier erfahren Sie, wie Sie mithilfe von PowerShell die Funktion zur Problembehandlung für Verbindungen von Azure Network Watcher nutzen.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: damendo
ms.openlocfilehash: 15ce453b607855072ca6e7c7ee32f63000e1d754
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492487"
---
# <a name="troubleshoot-connections-with-azure-network-watcher-using-powershell"></a>Problembehandlung für Verbindungen mit Azure Network Watcher und PowerShell

> [!div class="op_single_selector"]
> - [Portal](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [Azure-Befehlszeilenschnittstelle](network-watcher-connectivity-cli.md)
> - [Azure-REST-API](network-watcher-connectivity-rest.md)

Hier erfahren Sie, wie Sie mit der Problembehandlung für Verbindungen überprüfen können, ob von einem virtuellen Computer eine direkte TCP-Verbindung mit einem bestimmten Endpunkt hergestellt werden kann.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Voraussetzungen

* Eine Instanz von Network Watcher in der Region, in der Sie die Problembehandlung für eine Verbindung ausführen möchten
* Virtuelle Computer zum Ausführen der Problembehandlung für Verbindungen

> [!IMPORTANT]
> Für die Problembehandlung für Verbindungen muss auf dem virtuellen Computer, auf dem Sie die Problembehandlung ausführen, die VM-Erweiterung `AzureNetworkWatcherExtension` installiert sein. Informationen zur Installation der Erweiterung finden Sie für einen virtuellen Windows-Computer unter [VM-Erweiterung für den Network Watcher-Agent für Windows](../virtual-machines/extensions/network-watcher-windows.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) und für einen virtuellen Linux-Computer unter [VM-Erweiterung für den Network Watcher-Agent für Linux](../virtual-machines/extensions/network-watcher-linux.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). Die Erweiterung ist nicht auf dem Zielendpunkt erforderlich.

## <a name="check-connectivity-to-a-virtual-machine"></a>Überprüfen der Konnektivität zu einem virtuellen Computer

In diesem Beispiel wird die Verbindung mit einem virtuellen Zielcomputer über Port 80 überprüft. Für dieses Beispiel muss Network Watcher in der Region mit der Quell-VM aktiviert sein.  

### <a name="example"></a>Beispiel

```powershell
$rgName = "ContosoRG"
$sourceVMName = "MultiTierApp0"
$destVMName = "Database0"

$RG = Get-AzResourceGroup -Name $rgName

$VM1 = Get-AzVM -ResourceGroupName $rgName | Where-Object -Property Name -EQ $sourceVMName
$VM2 = Get-AzVM -ResourceGroupName $rgName | Where-Object -Property Name -EQ $destVMName

$networkWatcher = Get-AzNetworkWatcher | Where-Object -Property Location -EQ -Value $VM1.Location 

Test-AzNetworkWatcherConnectivity -NetworkWatcher $networkWatcher -SourceId $VM1.Id -DestinationId $VM2.Id -DestinationPort 80
```

### <a name="response"></a>Antwort

Die folgende Antwort stammt aus dem vorherigen Beispiel.  In dieser Antwort ist der `ConnectionStatus`**Nicht erreichbar**. Wie Sie sehen, sind alle gesendeten Tests fehlgeschlagen. Bei der Konnektivität ist am virtuellen Gerät ein Fehler aufgetreten. Der Grund dafür ist eine benutzerdefinierte `NetworkSecurityRule` mit der Bezeichnung **UserRule_Port80**, deren Konfiguration eingehenden Datenverkehr über Port 80 blockiert. Diese Informationen können bei der Untersuchung von Konnektivitätsproblemen verwendet werden.

```
ConnectionStatus : Unreachable
AvgLatencyInMs   : 
MinLatencyInMs   : 
MaxLatencyInMs   : 
ProbesSent       : 100
ProbesFailed     : 100
Hops             : [
                     {
                       "Type": "Source",
                       "Id": "c5222ea0-3213-4f85-a642-cee63217c2f3",
                       "Address": "10.1.1.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGrou
                   ps/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurat
                   ions/ipconfig1",
                       "NextHopIds": [
                         "9283a9f0-cc5e-4239-8f5e-ae0f3c19fbaa"
                       ],
                       "Issues": []
                     },
                     {
                       "Type": "VirtualAppliance",
                       "Id": "9283a9f0-cc5e-4239-8f5e-ae0f3c19fbaa",
                       "Address": "10.1.2.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGrou
                   ps/ContosoRG/providers/Microsoft.Network/networkInterfaces/fwNic/ipConfiguratio
                   ns/ipconfig1",
                       "NextHopIds": [
                         "0f1500cd-c512-4d43-b431-7267e4e67017"
                       ],
                       "Issues": []
                     },
                     {
                       "Type": "VirtualAppliance",
                       "Id": "0f1500cd-c512-4d43-b431-7267e4e67017",
                       "Address": "10.1.3.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGrou
                   ps/ContosoRG/providers/Microsoft.Network/networkInterfaces/auNic/ipConfiguratio
                   ns/ipconfig1",
                       "NextHopIds": [
                         "a88940f8-5fbe-40da-8d99-1dee89240f64"
                       ],
                       "Issues": [
                         {
                           "Origin": "Outbound",
                           "Severity": "Error",
                           "Type": "NetworkSecurityRule",
                           "Context": [
                             {
                               "key": "RuleName",
                               "value": "UserRule_Port80"
                             }
                           ]
                         }
                       ]
                     },
                     {
                       "Type": "VnetLocal",
                       "Id": "a88940f8-5fbe-40da-8d99-1dee89240f64",
                       "Address": "10.1.4.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGrou
                   ps/ContosoRG/providers/Microsoft.Network/networkInterfaces/dbNic0/ipConfigurati
                   ons/ipconfig1",
                       "NextHopIds": [],
                       "Issues": []
                     }
                   ]
```

## <a name="validate-routing-issues"></a>Überprüfen von Routingproblemen

In diesem Beispiel wird die Konnektivität zwischen einem virtuellen Computer und einem Remoteendpunkt überprüft. Für dieses Beispiel muss Network Watcher in der Region mit der Quell-VM aktiviert sein.  

### <a name="example"></a>Beispiel

```powershell
$rgName = "ContosoRG"
$sourceVMName = "MultiTierApp0"

$RG = Get-AzResourceGroup -Name $rgName
$VM1 = Get-AzVM -ResourceGroupName $rgName | Where-Object -Property Name -EQ $sourceVMName

$networkWatcher = Get-AzNetworkWatcher | Where-Object -Property Location -EQ -Value $VM1.Location 

Test-AzNetworkWatcherConnectivity -NetworkWatcher $networkWatcher -SourceId $VM1.Id -DestinationAddress 13.107.21.200 -DestinationPort 80
```

### <a name="response"></a>Antwort

Im folgenden Beispiel wird der `ConnectionStatus` als **Nicht erreichbar** angezeigt. In den `Hops`-Details können Sie unter `Issues` sehen, dass der Datenverkehr aufgrund einer `UserDefinedRoute` blockiert war. 

```
ConnectionStatus : Unreachable
AvgLatencyInMs   : 
MinLatencyInMs   : 
MaxLatencyInMs   : 
ProbesSent       : 100
ProbesFailed     : 100
Hops             : [
                     {
                       "Type": "Source",
                       "Id": "b4f7bceb-07a3-44ca-8bae-adec6628225f",
                       "Address": "10.1.1.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurations/ipconfig1",
                       "NextHopIds": [
                         "3fee8adf-692f-4523-b742-f6fdf6da6584"
                       ],
                       "Issues": [
                         {
                           "Origin": "Outbound",
                           "Severity": "Error",
                           "Type": "UserDefinedRoute",
                           "Context": [
                             {
                               "key": "RouteType",
                               "value": "User"
                             }
                           ]
                         }
                       ]
                     },
                     {
                       "Type": "Destination",
                       "Id": "3fee8adf-692f-4523-b742-f6fdf6da6584",
                       "Address": "13.107.21.200",
                       "ResourceId": "Unknown",
                       "NextHopIds": [],
                       "Issues": []
                     }
                   ]
```

## <a name="check-website-latency"></a>Überprüfen der Websitelatenz

Im folgenden Beispiel wird die Konnektivität mit einer Website überprüft. Für dieses Beispiel muss Network Watcher in der Region mit der Quell-VM aktiviert sein.  

### <a name="example"></a>Beispiel

```powershell
$rgName = "ContosoRG"
$sourceVMName = "MultiTierApp0"

$RG = Get-AzResourceGroup -Name $rgName
$VM1 = Get-AzVM -ResourceGroupName $rgName | Where-Object -Property Name -EQ $sourceVMName

$networkWatcher = Get-AzNetworkWatcher | Where-Object -Property Location -EQ -Value $VM1.Location 


Test-AzNetworkWatcherConnectivity -NetworkWatcher $networkWatcher -SourceId $VM1.Id -DestinationAddress https://bing.com/
```

### <a name="response"></a>Antwort

In der folgenden Antwort wird der `ConnectionStatus` als **Erreichbar** angezeigt. Wenn eine Verbindung erfolgreich ist, werden Latenzwerte bereitgestellt.

```
ConnectionStatus : Reachable
AvgLatencyInMs   : 1
MinLatencyInMs   : 0
MaxLatencyInMs   : 7
ProbesSent       : 100
ProbesFailed     : 0
Hops             : [
                     {
                       "Type": "Source",
                       "Id": "1f0e3415-27b0-4bf7-a59d-3e19fb854e3e",
                       "Address": "10.1.1.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurations/ipconfig1",
                       "NextHopIds": [
                         "f99f2bd1-42e8-4bbf-85b6-5d21d00c84e0"
                       ],
                       "Issues": []
                     },
                     {
                       "Type": "Internet",
                       "Id": "f99f2bd1-42e8-4bbf-85b6-5d21d00c84e0",
                       "Address": "204.79.197.200",
                       "ResourceId": "Internet",
                       "NextHopIds": [],
                       "Issues": []
                     }
                   ]
```

## <a name="check-connectivity-to-a-storage-endpoint"></a>Überprüfen der Konnektivität zu einem Speicherendpunkt

Im folgenden Beispiel wird die Konnektivität von einem virtuellen Computer mit einem Blobspeicherkonto überprüft. Für dieses Beispiel muss Network Watcher in der Region mit der Quell-VM aktiviert sein.  

### <a name="example"></a>Beispiel

```powershell
$rgName = "ContosoRG"
$sourceVMName = "MultiTierApp0"

$RG = Get-AzResourceGroup -Name $rgName

$VM1 = Get-AzVM -ResourceGroupName $rgName | Where-Object -Property Name -EQ $sourceVMName

$networkWatcher = Get-AzNetworkWatcher | Where-Object -Property Location -EQ -Value $VM1.Location

Test-AzNetworkWatcherConnectivity -NetworkWatcher $networkWatcher -SourceId $VM1.Id -DestinationAddress https://contosostorageexample.blob.core.windows.net/ 
```

### <a name="response"></a>Antwort

Der folgende JSON-Code ist die Beispielantwort auf die Ausführung des vorherigen Cmdlets. Da das Ziel erreichbar ist, wird die `ConnectionStatus`-Eigenschaft als **Erreichbar** angezeigt.  Die Einzelheiten in Bezug auf die Anzahl der Hops, die zum Erreichen des Speicherblobs und der Latenz benötigt werden, werden bereitgestellt.

```json
ConnectionStatus : Reachable
AvgLatencyInMs   : 1
MinLatencyInMs   : 0
MaxLatencyInMs   : 8
ProbesSent       : 100
ProbesFailed     : 0
Hops             : [
                     {
                       "Type": "Source",
                       "Id": "9e7f61d9-fb45-41db-83e2-c815a919b8ed",
                       "Address": "10.1.1.4",
                       "ResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/networkInterfaces/appNic0/ipConfigurations/ipconfig1",
                       "NextHopIds": [
                         "1e6d4b3c-7964-4afd-b959-aaa746ee0f15"
                       ],
                       "Issues": []
                     },
                     {
                       "Type": "Internet",
                       "Id": "1e6d4b3c-7964-4afd-b959-aaa746ee0f15",
                       "Address": "13.71.200.248",
                       "ResourceId": "Internet",
                       "NextHopIds": [],
                       "Issues": []
                     }
                   ]
```

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie den Artikel zur [IP-Datenflussüberprüfung](diagnose-vm-network-traffic-filtering-problem.md), um zu ermitteln, ob bestimmter eingehender oder ausgehender Datenverkehr für Ihren virtuellen Computer zulässig ist.

Wenn Datenverkehr fälschlicherweise blockiert wird, finden Sie unter [Verwalten von Netzwerksicherheitsgruppen](../virtual-network/manage-network-security-group.md) Informationen zum Ermitteln der Netzwerksicherheitsgruppe und der definierten Sicherheitsregeln.