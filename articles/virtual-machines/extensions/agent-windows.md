---
title: Übersicht über den Agent für virtuelle Azure-Computer
description: Übersicht über den Agent für virtuelle Azure-Computer
services: virtual-machines-windows
ms.subservice: extensions
author: mimckitt
ms.service: virtual-machines-windows
ms.topic: article
ms.date: 07/20/2019
ms.author: mimckitt
ms.openlocfilehash: 3724b8a2afb89594c73f7dae782658ec8978963a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016470"
---
# <a name="azure-virtual-machine-agent-overview"></a>Übersicht über den Agent für virtuelle Azure-Computer
Der Agent für virtuelle Microsoft Azure-Computer (VM-Agent) ist ein sicherer, einfacher Prozess zur Verwaltung der VM-Interaktion mit dem Azure Fabric Controller. Der VM-Agent spielt eine primäre Rolle bei der Aktivierung und Ausführung von Azure-VM-Erweiterungen. VM-Erweiterungen ermöglichen es, VMs nach der Bereitstellung zu konfigurieren (beispielsweise, um Software zu installieren und zu konfigurieren). Außerdem ermöglichen VM-Erweiterungen den Einsatz von Wiederherstellungsfeatures wie das Zurücksetzen des Administratorkennworts einer VM. Ohne den Azure-VM-Agent können keine VM-Erweiterungen ausgeführt werden.

In diesem Artikel werden die Installation und Erkennung des Agents für virtuelle Azure-Computer erläutert.

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
Der Windows-VM-Agent kann manuell mithilfe eines Windows-Installationspakets installiert werden. Wenn Sie ein benutzerdefiniertes VM-Image erstellen, das in Azure bereitgestellt wird, ist möglicherweise eine manuelle Installation erforderlich. Laden Sie für die manuelle Installation des Windows-VM-Agents [das Installationsprogramms für den VM-Agent herunter](https://go.microsoft.com/fwlink/?LinkID=394789). Der VM-Agent wird unter Windows Server 2008 R2 (64 Bit) und höher unterstützt.

> [!NOTE]
> Nachdem der VM-Agent manuell auf einem virtuellen Computer installiert wurde, der ohne Aktivierung von ProvisionVMAgent auf der Grundlage eines Images bereitgestellt wurde, muss die Option AllowExtensionOperations aktualisiert werden.

```powershell
$vm.OSProfile.AllowExtensionOperations = $true
$vm | Update-AzVM
```

### <a name="prerequisites"></a>Voraussetzungen

- Für die Ausführung des Windows-VM-Agent sind mindestens Windows Server 2008 SP2 (64 Bit) und .NET Framework 4.0 erforderlich. Siehe [Minimum version support for virtual machine agents in Azure](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) (Unterstützte Mindestversion für VM-Agents in Azure).

- Stellen Sie sicher, dass Ihr virtueller Computer Zugriff auf die IP-Adresse 168.63.129.16 hat. Unter [Was ist die IP-Adresse 168.63.129.16?](../../virtual-network/what-is-ip-address-168-63-129-16.md) finden Sie weitere Informationen dazu.

- Stellen Sie sicher, dass DHCP auf der Gast-VM aktiviert ist. Dies ist erforderlich, damit die Host- oder Fabricadresse des Dynamic Host Configuration-Protokolls (DHCP) für den IaaS-VM-Agent und die Erweiterungen funktioniert. Wenn Sie eine statische private IP-Adresse benötigen, sollten Sie diese im Azure-Portal oder mithilfe von PowerShell konfigurieren und sicherstellen, dass die DHCP-Option auf dem virtuellen Computer aktiviert ist. [Hier erfahren Sie mehr](../../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) über das Einrichten einer statischen IP-Adresse mit PowerShell.


## <a name="detect-the-vm-agent"></a>Erkennen des VM-Agents

### <a name="powershell"></a>PowerShell

Mithilfe des Azure Resource Manager-PowerShell-Moduls können Sie Informationen zu Azure-VMs abrufen. Informationen zu einer VM wie etwa dem Bereitstellungsstatus für den Azure-VM-Agent finden Sie unter [Get-AzVM](/powershell/module/az.compute/get-azvm):

```powershell
Get-AzVM
```

In der folgenden verkürzten Beispielausgabe ist die in `OSProfile` geschachtelte Eigenschaft *ProvisionVMAgent* dargestellt. Diese Eigenschaft gibt Aufschluss darüber, ob der VM-Agent für den virtuellen Computer bereitgestellt wurde:

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
Der Azure VM-Agent für Windows wird automatisch in Images aktualisiert, die aus dem Azure Marketplace bereitgestellt werden. Wenn neue VMs für Azure bereitgestellt werden, erhalten diese bei der VM-Bereitstellung jeweils den neuesten VM-Agent. Wenn Sie den Agent manuell installiert haben oder benutzerdefinierte VM-Images bereitstellen, müssen Sie diese manuell so aktualisieren, dass der neue VM-Agent zum Zeitpunkt der Imageerstellung eingeschlossen wird.

## <a name="windows-guest-agent-automatic-logs-collection"></a>Automatische Protokollerfassung für Windows-Gast-Agent
Der Windows-Gast-Agent verfügt über eine Funktion zum automatischen Erfassen einiger Protokolle. Diese Funktion wird durch den Prozess CollectGuestLogs.exe gesteuert. Sie ist sowohl für PaaS Cloud Services als auch für IaaS-VMs vorhanden und darauf ausgelegt, schnell und automatisch einige Diagnoseprotokolle von einer VM zu erfassen, sodass sie für die Offlineanalyse verwendet werden können. Erfasst werden Ereignisprotokolle, Betriebssystemprotokolle, Azure-Protokolle sowie einige Registrierungsschlüssel. Es wird eine ZIP-Datei erstellt, die an den Host der VM übertragen wird. Diese ZIP-Datei kann dann von Technikerteams und Supportmitarbeitern verwendet werden, um Probleme auf Anforderung des Besitzers der VM zu untersuchen.

## <a name="guest-agent-and-osprofile-certificates"></a>Gast-Agent- und OSProfile-Zertifikate
Der Azure-VM-Agent ist für die Installation der Zertifikate zuständig, auf die im `OSProfile` eines virtuellen Computers oder einer VM-Skalierungsgruppe verwiesen wird. Wenn Sie diese Zertifikate manuell in der MMC-Konsole für Zertifikate auf der Gast-VM entfernen, wird erwartet, dass sie vom Gast-Agent wieder hinzugefügt werden.
Um ein Zertifikat dauerhaft zu entfernen, müssen Sie es aus `OSProfile` entfernen und dann im Gastbetriebssystems entfernen.

Verwenden Sie für einen virtuellen Computer [Remove-AzVMSecret](), um Zertifikate aus `OSProfile` zu entfernen.

Weitere Informationen zu VMSS-Zertifikaten finden Sie unter [Virtual Machine Scale Sets – How do I remove deprecated certificates?](../../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-remove-deprecated-certificates) (Skalierungsgruppen für virtuelle Computer – Wie entferne ich veraltete Zertifikate?).


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu VM-Erweiterungen finden Sie unter [Azure virtual machine extensions and features overview (Übersicht über Azure-VM-Erweiterungen und Features)](overview.md).