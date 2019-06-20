---
title: Übersicht über den Agent für virtuelle Azure-Computer | Microsoft-Dokumentation
description: Übersicht über den Agent für virtuelle Azure-Computer
services: virtual-machines-windows
documentationcenter: virtual-machines
author: roiyz-msft
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 0a1f212e-053e-4a39-9910-8d622959f594
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: roiyz
ms.openlocfilehash: d17d7c9d7b57e6ca040e4f81c9665789c8bc26e2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60799775"
---
# <a name="azure-virtual-machine-agent-overview"></a>Übersicht über den Agent für virtuelle Azure-Computer
Der Agent für virtuelle Microsoft Azure-Computer (VM-Agent) ist ein sicherer, einfacher Prozess zur Verwaltung der VM-Interaktion mit dem Azure Fabric Controller. Der VM-Agent spielt eine primäre Rolle bei der Aktivierung und Ausführung von Azure-VM-Erweiterungen. VM-Erweiterungen ermöglichen es, VMs nach der Bereitstellung zu konfigurieren (beispielsweise, um Software zu installieren und zu konfigurieren). Außerdem ermöglichen VM-Erweiterungen den Einsatz von Wiederherstellungsfeatures wie das Zurücksetzen des Administratorkennworts einer VM. Ohne den Azure-VM-Agent können keine VM-Erweiterungen ausgeführt werden.

In diesem Artikel werden die Installation, Erkennung und Entfernung des Azure VM-Agents erläutert.

## <a name="install-the-vm-agent"></a>Installieren des VM-Agents

### <a name="azure-marketplace-image"></a>Azure Marketplace-Image

Der Azure-VM-Agent wird standardmäßig auf jeder Windows-VM installiert, die auf der Grundlage eines Azure Marketplace-Image bereitgestellt wird. Wenn Sie ein Azure Marketplace-Image über das Portal, mithilfe von PowerShell, über die Befehlszeilenschnittstelle oder unter Verwendung einer Azure Resource Manager-Vorlage bereitstellen, wird der Azure-VM-Agent ebenfalls installiert.

Das Windows-Gast-Agent-Paket setzt sich aus zwei Teilen zusammen:

- Bereitstellungs-Agent (PA)
- Windows-Gast-Agent (WinGA)

Zum Starten einer VM muss nur der PA auf der VM installiert sein, nicht jedoch der WinGA. Bei der VM-Bereitstellung können Sie festlegen, dass der WinGA nicht installiert werden soll. Im folgenden Beispiel wird gezeigt, wie mit einer Azure Resource Manager-Vorlage die *provisionVmAgent*-Option ausgewählt wird:

```json
"resources": [{
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2016-04-30-preview",
"location": "[parameters('location')]",
"dependsOn": ["[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"],
"properties": {
    "osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "adminPassword": "[parameters('adminPassword')]",
    "windowsConfiguration": {
        "provisionVmAgent": "false"
}
```

Wenn die Agents nicht installiert sind, können Sie einige Azure-Dienste wie Azure Backup oder das Azure Security Center nicht verwenden. Für diese Dienste muss eine Erweiterung installiert werden. Wenn Sie eine VM ohne den WinGA bereitgestellt haben, können Sie später die neueste Agent-Version installieren.

### <a name="manual-installation"></a>Manuelle Installation
Der Windows-VM-Agent kann manuell mithilfe eines Windows-Installationspakets installiert werden. Wenn Sie ein benutzerdefiniertes VM-Image erstellen, das in Azure bereitgestellt wird, ist möglicherweise eine manuelle Installation erforderlich. Laden Sie für die manuelle Installation des Windows-VM-Agents [das Installationsprogramms für den VM-Agent herunter](https://go.microsoft.com/fwlink/?LinkID=394789).

Der VM-Agent kann mittels Doppelklick auf die Windows-Installationsdatei installiert werden. Wenn Sie eine automatisierte oder unbeaufsichtigte Installation des VM-Agents durchführen möchten, verwenden Sie den folgenden Befehl:

```cmd
msiexec.exe /i WindowsAzureVmAgent.2.7.1198.778.rd_art_stable.160617-1120.fre /quiet
```

## <a name="detect-the-vm-agent"></a>Erkennen des VM-Agents

### <a name="powershell"></a>PowerShell

Mithilfe des Azure Resource Manager-PowerShell-Moduls können Sie Informationen zu Azure-VMs abrufen. Informationen zu einer VM wie etwa dem Bereitstellungsstatus für den Azure-VM-Agent finden Sie unter [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm):

```powershell
Get-AzVM
```

In der folgenden verkürzten Beispielausgabe ist die in *OSProfile* geschachtelte Eigenschaft *ProvisionVMAgent* dargestellt. Diese Eigenschaft gibt Aufschluss darüber, ob der VM-Agent für den virtuellen Computer bereitgestellt wurde:

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

Mit dem folgenden Skript können Sie eine kompakte Liste mit den VM-Namen und dem Status des VM-Agents zurückgeben:

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>Manuelle Erkennung

Wenn Sie bei einem virtuellen Windows-Computer angemeldet sind, können Sie mithilfe des Task-Managers die ausgeführten Prozesse untersuchen. Öffnen Sie zum Überprüfen des Azure-VM-Agents den Task-Manager, klicken Sie auf die Registerkarte *Details*, und suchen sie nach einen Prozess namens **WindowsAzureGuestAgent.exe**. Ist dieser Prozess vorhanden, ist der VM-Agent installiert.


## <a name="upgrade-the-vm-agent"></a>Upgraden des VM-Agents
Der Azure VM-Agent für Windows wird automatisch aktualisiert. Wenn neue VMs für Azure bereitgestellt werden, erhalten diese bei der VM-Bereitstellung jeweils den neuesten VM-Agent. Benutzerdefinierte VM-Images sollten bei der Erstellung von Images manuell mit dem neuen VM-Agent aktualisiert werden.


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu VM-Erweiterungen finden Sie unter [Azure virtual machine extensions and features overview (Übersicht über Azure-VM-Erweiterungen und Features)](overview.md).
