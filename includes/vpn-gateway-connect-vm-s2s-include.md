---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 820a6a4da9f5c466e694f247d09393474d8464ee
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177940"
---
Sie können eine Verbindung mit einer VM herstellen, die in Ihrem VNet bereitgestellt wird, indem Sie eine Remotedesktopverbindung mit Ihrer VM herstellen. Die beste Möglichkeit zur anfänglichen Sicherstellung, dass eine Verbindung mit Ihrer VM hergestellt werden kann, ist die Verwendung der privaten IP-Adresse anstelle des Computernamens. Auf diese Weise können Sie testen, ob die Verbindungsherstellung möglich ist, anstatt zu überprüfen, ob die Namensauflösung richtig konfiguriert ist.

1. Ermitteln Sie die private IP-Adresse. Sie können die private IP-Adresse eines virtuellen Computers auf verschiedene Weise ermitteln. Unten werden die Schritte für das Azure-Portal und PowerShell gezeigt.

   - Azure-Portal: Suchen Sie im Azure-Portal nach Ihrem virtuellen Computer. Zeigen Sie die Eigenschaften für die VM an. Die private IP-Adresse ist aufgeführt.

   - PowerShell: Verwenden Sie das Beispiel, um eine Liste mit VMs und privaten IP-Adressen aus Ihren Ressourcengruppen anzuzeigen. Es ist nicht erforderlich, dieses Beispiel vor der Verwendung zu ändern.

     ```azurepowershell-interactive
     $VMs = Get-AzVM
     $Nics = Get-AzNetworkInterface | Where VirtualMachine -ne $null

     foreach($Nic in $Nics)
     {
      $VM = $VMs | Where-Object -Property Id -eq $Nic.VirtualMachine.Id
      $Prv = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAddress
      $Alloc = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAllocationMethod
      Write-Output "$($VM.Name): $Prv,$Alloc"
     }
     ```

2. Vergewissern Sie sich, dass eine VPN-Verbindung mit Ihrem VNet besteht.
3. Öffnen Sie eine **Remotedesktopverbindung**, indem Sie auf der Taskleiste im Suchfeld „RDP“ oder „Remotedesktopverbindung“ eingeben und dann „Remotedesktopverbindung“ wählen. Sie können auch den Befehl „mstsc“ in PowerShell verwenden, um eine Remotedesktopverbindung zu öffnen. 
4. Geben Sie in der Remotedesktopverbindung die private IP-Adresse der VM ein. Sie können auf „Optionen anzeigen“ klicken, um weitere Einstellungen anzupassen, und dann die Verbindung herstellen.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>So führen Sie die Problembehandlung für die RDP-Verbindung mit einer VM durch

Falls Sie beim Herstellen einer Verbindung mit einem virtuellen Computer per VPN-Verbindung Probleme haben sollten, überprüfen Sie Folgendes:

- Stellen Sie sicher, dass die Herstellung der VPN-Verbindung erfolgreich war.
- Stellen Sie sicher, dass Sie die Verbindung mit der privaten IP-Adresse für die VM herstellen.
- Falls Sie mit der privaten IP-Adresse eine Verbindung mit der VM herstellen können, aber nicht mit dem Computernamen, sollten Sie sich vergewissern, dass das DNS richtig konfiguriert ist. Weitere Informationen zur Funktionsweise der Namensauflösung für virtuelle Computer finden Sie unter [Namensauflösung für virtuelle Computer](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
- Weitere Informationen zu RDP-Verbindungen finden Sie unter [Behandeln von Problemen bei Remotedesktopverbindungen mit einem virtuellen Azure-Computer](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md).