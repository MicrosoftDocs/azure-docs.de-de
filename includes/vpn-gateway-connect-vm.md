---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/23/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6986651d2b48d82382aa26a0a3947104c7e09981
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026912"
---
Sie können eine Verbindung mit einer VM herstellen, die in Ihrem VNet bereitgestellt wird, indem Sie eine Remotedesktopverbindung mit Ihrer VM herstellen. Die beste Möglichkeit zur anfänglichen Sicherstellung, dass eine Verbindung mit Ihrer VM hergestellt werden kann, ist die Verwendung der privaten IP-Adresse anstelle des Computernamens. Auf diese Weise können Sie testen, ob die Verbindungsherstellung möglich ist, anstatt zu überprüfen, ob die Namensauflösung richtig konfiguriert ist.

1. Ermitteln Sie die private IP-Adresse. Sie können die private IP-Adresse einer VM ermitteln, indem Sie sich entweder die Eigenschaften für die VM im Azure-Portal ansehen oder PowerShell verwenden.

   * Azure-Portal: Suchen Sie im Azure-Portal nach Ihrem virtuellen Computer. Zeigen Sie die Eigenschaften für die VM an. Die private IP-Adresse ist aufgeführt.

   * PowerShell: Verwenden Sie das Beispiel, um eine Liste mit VMs und privaten IP-Adressen aus Ihren Ressourcengruppen anzuzeigen. Es ist nicht erforderlich, dieses Beispiel vor der Verwendung zu ändern.

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

1. Vergewissern Sie sich, dass eine Point-to-Site-VPN-Verbindung mit Ihrem VNet besteht.
1. Öffnen Sie eine **Remotedesktopverbindung**, indem Sie auf der Taskleiste im Suchfeld „RDP“ oder „Remotedesktopverbindung“ eingeben und dann „Remotedesktopverbindung“ wählen. Sie können auch den Befehl „mstsc“ in PowerShell verwenden, um eine Remotedesktopverbindung zu öffnen. 
1. Geben Sie in der Remotedesktopverbindung die private IP-Adresse der VM ein. Sie können auf „Optionen anzeigen“ klicken, um weitere Einstellungen anzupassen, und dann die Verbindung herstellen.

**Problembehandlung für Verbindungen**

Falls Sie beim Herstellen einer Verbindung mit einem virtuellen Computer per VPN-Verbindung Probleme haben sollten, überprüfen Sie Folgendes:

* Stellen Sie sicher, dass die Herstellung der VPN-Verbindung erfolgreich war.

* Stellen Sie sicher, dass Sie die Verbindung mit der privaten IP-Adresse für die VM herstellen.

* Falls Sie mit der privaten IP-Adresse eine Verbindung mit der VM herstellen können, aber nicht mit dem Computernamen, sollten Sie sich vergewissern, dass das DNS richtig konfiguriert ist. Weitere Informationen zur Funktionsweise der Namensauflösung für virtuelle Computer finden Sie unter [Namensauflösung für virtuelle Computer](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

* Weitere Informationen zu RDP-Verbindungen finden Sie unter [Behandeln von Problemen bei Remotedesktopverbindungen mit einem virtuellen Azure-Computer](../articles/virtual-machines/troubleshooting/troubleshoot-rdp-connection.md).