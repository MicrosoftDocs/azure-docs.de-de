---
title: Verwalten von Paketerfassungen mit Azure Network Watcher – Azure CLI | Microsoft-Dokumentation
description: Auf dieser Seite wird erläutert, wie das Network Watcher-Feature zur Paketerfassung mithilfe der Azure-Befehlszeilenschnittstelle verwaltet wird.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 6e5f2a519564716d426c50bb9cc8dd245774321e
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966495"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-azure-cli"></a>Verwalten von Paketerfassungen mit Azure Network Watcher mithilfe der Azure-Befehlszeilenschnittstelle

> [!div class="op_single_selector"]
> - [Azure portal](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure-Befehlszeilenschnittstelle](network-watcher-packet-capture-manage-cli.md)
> - [Azure-REST-API](network-watcher-packet-capture-manage-rest.md)

Mithilfe der Paketerfassung von Network Watcher können Sie Sitzungen erfassen, um den eingehenden und ausgehenden Datenverkehr eines virtuellen Computers nachzuverfolgen. Für die Erfassungssitzung werden Filter bereitgestellt, um sicherzustellen, dass nur der gewünschte Datenverkehr erfasst wird. Mithilfe der Paketerfassung können Sie Netzwerkanomalien sowohl reaktiv als auch proaktiv diagnostizieren. Weitere Verwendungszwecke sind das Erfassen von Netzwerkstatistiken, das Gewinnen von Informationen zu Netzwerkangriffen, das Debuggen der Kommunikation zwischen Client und Server und vieles mehr. Durch die Möglichkeit zur Remoteauslösung von Paketerfassungen erleichtert diese Funktion die manuelle Ausführung einer Paketerfassung auf dem gewünschten Computer. So sparen Sie wertvolle Zeit.

Um die Schritte in diesem Artikel ausführen zu können, müssen Sie [die Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows (Azure CLI) installieren](/cli/azure/install-azure-cli).

Dieser Artikel führt Sie durch die verschiedenen Verwaltungsaufgaben, die derzeit für die Paketerfassung verfügbar sind.

- [**Starten einer Paketerfassung**](#start-a-packet-capture)
- [**Beenden einer Paketerfassung**](#stop-a-packet-capture)
- [**Löschen einer Paketerfassung**](#delete-a-packet-capture)
- [**Herunterladen einer Paketerfassung**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie über die folgenden Ressourcen verfügen:

- Eine Instanz von Network Watcher in der Region, in der Sie eine Paketerfassung erstellen möchten
- Einen virtuellen Computer mit aktivierter Paketerfassungserweiterung

> [!IMPORTANT]
> Für die Paketerfassung muss ein Agent auf dem virtuellen Computer ausgeführt werden. Der Agent wird als Erweiterung installiert. Anweisungen zu VM-Erweiterungen finden Sie unter [Erweiterungen und Features für virtuelle Computer](../virtual-machines/extensions/features-windows.md).

## <a name="install-vm-extension"></a>Installieren der VM-Erweiterung

### <a name="step-1"></a>Schritt 1

Führen Sie den Befehl `az vm extension set` aus, um den Paketerfassungs-Agent auf dem virtuellen Gastcomputer zu installieren.

Für virtuelle Windows-Computer:

```azurecli-interactive
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentWindows --version 1.4
```

Für virtuelle Linux-Computer:

```azurecli-interactive
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentLinux --version 1.4
```

### <a name="step-2"></a>Schritt 2

Um sicherzustellen, dass der Agent installiert ist, führen Sie den Befehl `vm extension show` aus, und geben Sie die Ressourcengruppe und den Namen des virtuellen Computers an. Überprüfen Sie die Ergebnisliste, um sicherzustellen, dass der Agent installiert ist.

Für virtuelle Windows-Computer:

```azurecli-interactive
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name NetworkWatcherAgentWindows
```

Für virtuelle Linux-Computer:

```azurecli-interactive
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name AzureNetworkWatcherExtension
```

Das folgende Beispiel zeigt die Antwort nach der Ausführung von `az vm extension show`.

```json
{
  "autoUpgradeMinorVersion": true,
  "forceUpdateTag": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/extensions/NetworkWatcherAgentWindows",
  "instanceView": null,
  "location": "westcentralus",
  "name": "NetworkWatcherAgentWindows",
  "protectedSettings": null,
  "provisioningState": "Succeeded",
  "publisher": "Microsoft.Azure.NetworkWatcher",
  "resourceGroup": "{resourceGroupName}",
  "settings": null,
  "tags": null,
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "typeHandlerVersion": "1.4",
  "virtualMachineExtensionType": "NetworkWatcherAgentWindows"
}
```

## <a name="start-a-packet-capture"></a>Starten einer Paketerfassung

Nachdem Sie die vorherigen Schritte abgeschlossen haben, wird der Paketerfassungs-Agent auf dem virtuellen Computer installiert.

### <a name="step-1"></a>Schritt 1

Erstellen Sie ein Speicherkonto. Dieses Speicherkonto wird verwendet, um die Paketerfassungsdatei zu speichern.

```azurecli-interactive
az storage account list
```

### <a name="step-2"></a>Schritt 2

An diesem Punkt können Sie eine Paketerfassung erstellen.  Lassen Sie uns zunächst die Parameter untersuchen, die Sie möglicherweise konfigurieren möchten. Filter gehören zu den Parametern, die zur Einschränkung der Daten, die bei der Paketerfassung gespeichert werden, verwendet werden können. Im folgenden Beispiel wird eine Paketerfassung mit mehreren Filtern eingerichtet.  Die ersten drei Filter erfassen nur ausgehenden TCP-Datenverkehr von der lokalen IP-Adresse 10.0.0.3 an die Zielports 20, 80 und 443.  Mit dem letzten Filter wird nur UDP-Datenverkehr erfasst.

```azurecli-interactive
az network watcher packet-capture create --resource-group {resourceGroupName} --vm {vmName} --name packetCaptureName --storage-account {storageAccountName} --filters "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

Das folgende Beispiel zeigt die erwartete Ausgabe nach Ausführen des Befehls `az network watcher packet-capture create`.

```json
{
  "bytesToCapturePerPacket": 0,
  "etag": "W/\"b8cf3528-2e14-45cb-a7f3-5712ffb687ac\"",
  "filters": [
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "20"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "80"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "443"
    },
    {
      "localIpAddress": "",
      "localPort": "",
      "protocol": "UDP",
      "remoteIpAddress": "",
      "remotePort": ""
    }
  ],
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/pa
cketCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "provisioningState": "Succeeded",
  "resourceGroup": "NetworkWatcherRG",
  "storageLocation": {
    "filePath": null,
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/gwteststorage123abc",
    "storagePath": "https://gwteststorage123abc.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/{resourceGroupName}/p
roviders/microsoft.compute/virtualmachines/{vmName}/2017/05/25/packetcapture_16_22_34_630.cap"
  },
  "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}",
  "timeLimitInSeconds": 18000,
  "totalBytesPerSession": 1073741824
}
```

## <a name="get-a-packet-capture"></a>Abrufen einer Paketerfassung

Durch Ausführen des Befehls `az network watcher packet-capture show-status` wird der Status einer zurzeit durchgeführten oder abgeschlossenen Paketerfassung abgerufen.

```azurecli-interactive
az network watcher packet-capture show-status --name packetCaptureName --location {networkWatcherLocation}
```

Das folgende Beispiel zeigt die Ausgabe des Befehls `az network watcher packet-capture show-status`. Das folgende Beispiel zeigt die angehaltene Erfassung, wobei „TimeExceeded“ für „StopReason“ angegeben ist.

```
{
  "additionalProperties": {
    "status": "Succeeded"
  },
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/pa
cketCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "packetCaptureError": [],
  "packetCaptureStatus": "Stopped",
  "stopReason": "TimeExceeded"
}
```

## <a name="stop-a-packet-capture"></a>Beenden einer Paketerfassung

Durch Ausführen des Befehls `az network watcher packet-capture stop` wird eine Sitzung, die ggf. gerade ausgeführt wird, beendet.

```azurecli-interactive
az network watcher packet-capture stop --name packetCaptureName --location westcentralus
```

> [!NOTE]
> Der Befehl gibt keine Antwort zurück, wenn es für eine aktuell ausgeführte Erfassungssitzung oder eine vorhandene Sitzung ausgeführt wird, die bereits beendet wurde.

## <a name="delete-a-packet-capture"></a>Löschen einer Paketerfassung

```azurecli-interactive
az network watcher packet-capture delete --name packetCaptureName --location westcentralus
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