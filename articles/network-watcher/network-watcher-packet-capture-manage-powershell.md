---
title: Verwalten von Paketerfassungen – Azure PowerShell
titleSuffix: Azure Network Watcher
description: Auf dieser Seite wird erläutert, wie das Network Watcher-Feature zur Paketerfassung mit PowerShell verwaltet wird.
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
ms.openlocfilehash: c9a7af7575fcdd19d83e5a4f38ef03ef8948b8c0
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963469"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-powershell"></a>Verwalten von Paketerfassungen mit Azure Network Watcher mithilfe von PowerShell

> [!div class="op_single_selector"]
> - [Azure portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure-Befehlszeilenschnittstelle](network-watcher-packet-capture-manage-cli.md)
> - [Azure-REST-API](network-watcher-packet-capture-manage-rest.md)

Mithilfe der Paketerfassung von Network Watcher können Sie Sitzungen erfassen, um den eingehenden und ausgehenden Datenverkehr eines virtuellen Computers nachzuverfolgen. Für die Erfassungssitzung werden Filter bereitgestellt, um sicherzustellen, dass nur der gewünschte Datenverkehr erfasst wird. Mithilfe der Paketerfassung können Sie Netzwerkanomalien sowohl reaktiv als auch proaktiv diagnostizieren. Weitere Verwendungszwecke sind das Erfassen von Netzwerkstatistiken, das Gewinnen von Informationen zu Netzwerkangriffen, das Debuggen der Kommunikation zwischen Client und Server und vieles mehr. Durch die Möglichkeit zur Remoteauslösung von Paketerfassungen erleichtert diese Funktion die manuelle Ausführung einer Paketerfassung auf dem gewünschten Computer. So sparen Sie wertvolle Zeit.

Dieser Artikel führt Sie durch die verschiedenen Verwaltungsaufgaben, die derzeit für die Paketerfassung verfügbar sind.

- [**Starten einer Paketerfassung**](#start-a-packet-capture)
- [**Beenden einer Paketerfassung**](#stop-a-packet-capture)
- [**Löschen einer Paketerfassung**](#delete-a-packet-capture)
- [**Herunterladen einer Paketerfassung**](#download-a-packet-capture)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie über die folgenden Ressourcen verfügen:

* Eine Instanz von Network Watcher in der Region, in der Sie eine Paketerfassung erstellen möchten

* Einen virtuellen Computer mit aktivierter Paketerfassungserweiterung

> [!IMPORTANT]
> Für die Paketerfassung ist die VM-Erweiterung `AzureNetworkWatcherExtension` erforderlich. Informationen zur Installation der Erweiterung finden Sie für einen virtuellen Windows-Computer unter [VM-Erweiterung für den Network Watcher-Agent für Windows](../virtual-machines/extensions/network-watcher-windows.md) und für einen virtuellen Linux-Computer unter [VM-Erweiterung für den Network Watcher-Agent für Linux](../virtual-machines/extensions/network-watcher-linux.md).

## <a name="install-vm-extension"></a>Installieren der VM-Erweiterung

### <a name="step-1"></a>Schritt 1

```powershell
$VM = Get-AzVM -ResourceGroupName testrg -Name VM1
```

### <a name="step-2"></a>Schritt 2

Das folgende Beispiel ruft die benötigten Erweiterungsinformationen zum Ausführen des Cmdlets `Set-AzVMExtension` ab. Mit diesem Cmdlet wird der Paketerfassungs-Agent auf dem virtuellen Gastcomputer installiert.

> [!NOTE]
> Die Ausführung des Cmdlets `Set-AzVMExtension` kann mehrere Minuten in Anspruch nehmen.

Für virtuelle Windows-Computer:

```powershell
$AzureNetworkWatcherExtension = Get-AzVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentWindows -Version 1.4.585.2
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

Für virtuelle Linux-Computer:

```powershell
$AzureNetworkWatcherExtension = Get-AzVMExtensionImage -Location WestCentralUS -PublisherName Microsoft.Azure.NetworkWatcher -Type NetworkWatcherAgentLinux -Version 1.4.13.0
$ExtensionName = "AzureNetworkWatcherExtension"
Set-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -Location $VM.Location -VMName $VM.Name -Name $ExtensionName -Publisher $AzureNetworkWatcherExtension.PublisherName -ExtensionType $AzureNetworkWatcherExtension.Type -TypeHandlerVersion $AzureNetworkWatcherExtension.Version.Substring(0,3)
```

Das folgende Beispiel zeigt eine erfolgreiche Antwort nach Ausführung des Cmdlets `Set-AzVMExtension`.

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   
```

### <a name="step-3"></a>Schritt 3

Um sicherzustellen, dass der Agent installiert ist, führen Sie das Cmdlet `Get-AzVMExtension` aus, und übergeben Sie den Namen des virtuellen Computers und den Erweiterungsnamen.

```powershell
Get-AzVMExtension -ResourceGroupName $VM.ResourceGroupName  -VMName $VM.Name -Name $ExtensionName
```

Das folgende Beispiel zeigt die Antwort nach der Ausführung von `Get-AzVMExtension`.

```
ResourceGroupName       : testrg
VMName                  : testvm1
Name                    : AzureNetworkWatcherExtension
Location                : westcentralus
Etag                    : null
Publisher               : Microsoft.Azure.NetworkWatcher
ExtensionType           : NetworkWatcherAgentWindows
TypeHandlerVersion      : 1.4
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1/
                          extensions/AzureNetworkWatcherExtension
PublicSettings          : 
ProtectedSettings       : 
ProvisioningState       : Succeeded
Statuses                : 
SubStatuses             : 
AutoUpgradeMinorVersion : True
ForceUpdateTag          : 
```

## <a name="start-a-packet-capture"></a>Starten einer Paketerfassung

Nachdem Sie die vorherigen Schritte abgeschlossen haben, wird der Paketerfassungs-Agent auf dem virtuellen Computer installiert.

### <a name="step-1"></a>Schritt 1

Der nächste Schritt besteht im Abrufen der Network Watcher-Instanz. Diese Variable wird in Schritt 4 an das Cmdlet `New-AzNetworkWatcherPacketCapture` übergeben.

```powershell
$networkWatcher = Get-AzResource -ResourceType "Microsoft.Network/networkWatchers" | Where {$_.Location -eq "WestCentralUS" }
```

### <a name="step-2"></a>Schritt 2

Erstellen Sie ein Speicherkonto. Dieses Speicherkonto wird verwendet, um die Paketerfassungsdatei zu speichern.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName testrg -Name testrgsa123
```

### <a name="step-3"></a>Schritt 3

Mithilfe von Filtern können die von der Paketerfassung gespeicherten Daten eingeschränkt werden. Im folgende Beispiel werden zwei Filter eingerichtet.  Ein Filter erfasst nur ausgehenden TCP-Datenverkehr von der lokalen IP-Adresse 10.0.0.3 an die Zielports 20, 80 und 443.  Mit dem zweiten Filter wird nur UDP-Datenverkehr erfasst.

```powershell
$filter1 = New-AzPacketCaptureFilterConfig -Protocol TCP -RemoteIPAddress "1.1.1.1-255.255.255.255" -LocalIPAddress "10.0.0.3" -LocalPort "1-65535" -RemotePort "20;80;443"
$filter2 = New-AzPacketCaptureFilterConfig -Protocol UDP
```

> [!NOTE]
> Für eine Paketerfassung können mehrere Filter definiert werden.

### <a name="step-4"></a>Schritt 4

Führen Sie das Cmdlet `New-AzNetworkWatcherPacketCapture` zum Starten der Paketerfassung aus, und übergeben Sie dabei die erforderlichen Werte, die Sie in den vorherigen Schritten abgerufen haben.
```powershell

New-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $vm.Id -PacketCaptureName "PacketCaptureTest" -StorageAccountId $storageAccount.id -TimeLimitInSeconds 60 -Filter $filter1, $filter2
```

Das folgende Beispiel zeigt die erwartete Ausgabe nach Ausführen des Cmdlets `New-AzNetworkWatcherPacketCapture`.

```
Name                    : PacketCaptureTest
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatcher
                          s/NetworkWatcher_westcentralus/packetCaptures/PacketCaptureTest
Etag                    : W/"3bf27278-8251-4651-9546-c7f369855e4e"
ProvisioningState       : Succeeded
Target                  : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1
BytesToCapturePerPacket : 0
TotalBytesPerSession    : 1073741824
TimeLimitInSeconds      : 60
StorageLocation         : {
                            "StorageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Storage/storageA
                          ccounts/examplestorage",
                            "StoragePath": "https://examplestorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-00000
                          0000000/resourcegroups/testrg/providers/microsoft.compute/virtualmachines/testvm1/2017/02/01/packetcapture_22_42_48_238.cap"
                          }
Filters                 : [
                            {
                              "Protocol": "TCP",
                              "RemoteIPAddress": "1.1.1.1-255.255.255",
                              "LocalIPAddress": "10.0.0.3",
                              "LocalPort": "1-65535",
                              "RemotePort": "20;80;443"
                            },
                            {
                              "Protocol": "UDP",
                              "RemoteIPAddress": "",
                              "LocalIPAddress": "",
                              "LocalPort": "",
                              "RemotePort": ""
                            }
                          ]


```

## <a name="get-a-packet-capture"></a>Abrufen einer Paketerfassung

Durch Ausführen des Cmdlets `Get-AzNetworkWatcherPacketCapture` wird der Status einer zurzeit durchgeführten oder abgeschlossenen Paketerfassung abgerufen.

```powershell
Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

Das folgende Beispiel zeigt die Ausgabe des Cmdlets `Get-AzNetworkWatcherPacketCapture`. Im folgenden Beispiel ist die Erfassung abgeschlossen. Der PacketCaptureStatus-Wert lautet „Stopped“ mit dem StopReason „TimeExceeded“. Dieser Wert zeigt, dass die Paketerfassung erfolgreich war und über den dafür festgelegten Zeitraum ausgeführt wurde.
```
Name                    : PacketCaptureTest
Id                      : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatcher
                          s/NetworkWatcher_westcentralus/packetCaptures/PacketCaptureTest
Etag                    : W/"4b9a81ed-dc63-472e-869e-96d7166ccb9b"
ProvisioningState       : Succeeded
Target                  : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm1
BytesToCapturePerPacket : 0
TotalBytesPerSession    : 1073741824
TimeLimitInSeconds      : 60
StorageLocation         : {
                            "StorageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Storage/storageA
                          ccounts/examplestorage",
                            "StoragePath": "https://examplestorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-00000
                          0000000/resourcegroups/testrg/providers/microsoft.compute/virtualmachines/testvm1/2017/02/01/packetcapture_22_42_48_238.cap"
                          }
Filters                 : [
                            {
                              "Protocol": "TCP",
                              "RemoteIPAddress": "1.1.1.1-255.255.255",
                              "LocalIPAddress": "10.0.0.3",
                              "LocalPort": "1-65535",
                              "RemotePort": "20;80;443"
                            },
                            {
                              "Protocol": "UDP",
                              "RemoteIPAddress": "",
                              "LocalIPAddress": "",
                              "LocalPort": "",
                              "RemotePort": ""
                            }
                          ]
CaptureStartTime        : 2/1/2017 10:43:01 PM
PacketCaptureStatus     : Stopped
StopReason              : TimeExceeded
PacketCaptureError      : []
```

## <a name="stop-a-packet-capture"></a>Beenden einer Paketerfassung

Durch Ausführen des Cmdlets `Stop-AzNetworkWatcherPacketCapture` wird eine Sitzung, die ggf. gerade ausgeführt wird, beendet.

```powershell
Stop-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

> [!NOTE]
> Das Cmdlet gibt keine Antwort zurück, wenn es für eine aktuell ausgeführte Erfassungssitzung oder eine vorhandene Sitzung ausgeführt wird, die bereits beendet wurde.

## <a name="delete-a-packet-capture"></a>Löschen einer Paketerfassung

```powershell
Remove-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName "PacketCaptureTest"
```

> [!NOTE]
> Durch das Löschen einer Paketerfassung wird die Datei im Speicherkonto nicht gelöscht.

## <a name="download-a-packet-capture"></a>Herunterladen einer Paketerfassung

Nachdem die Paketerfassungssitzung abgeschlossen wurde, kann die Erfassungsdatei in den Blobspeicher oder in eine lokale Datei auf dem virtuellen Computer hochgeladen werden. Der Speicherort der Paketerfassung wird beim Erstellen der Sitzung definiert. Ein nützliches Tool für den Zugriff auf diese in einem Speicherkonto gespeicherten Erfassungsdateien ist der Microsoft Azure Storage-Explorer, der hier heruntergeladen werden kann: https://storageexplorer.com/

Wenn ein Speicherkonto angegeben wird, werden Paketerfassungsdateien in einem Speicherkonto am folgenden Speicherort gespeichert:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Automatisierung von Paketerfassungen mit VM-Warnungen finden Sie unter [Erstellen einer durch Warnungen ausgelösten Paketerfassung](network-watcher-alert-triggered-packet-capture.md).

Lesen Sie den Artikel zur [IP-Datenflussüberprüfung](diagnose-vm-network-traffic-filtering-problem.md), um herauszufinden, ob bestimmter eingehender oder ausgehender Datenverkehr für Ihren virtuellen Computer zulässig ist.

<!-- Image references -->