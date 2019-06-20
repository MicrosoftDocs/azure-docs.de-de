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
ms.openlocfilehash: 35814de74fa03f9969cdd48882a5f672cfe306a1
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67177952"
---
Sie können eine Verbindung mit einer VM herstellen, die in Ihrem VNet bereitgestellt wird, indem Sie eine Remotedesktopverbindung mit Ihrer VM herstellen. Die beste Möglichkeit zur anfänglichen Sicherstellung, dass eine Verbindung mit Ihrer VM hergestellt werden kann, ist die Verwendung der privaten IP-Adresse anstelle des Computernamens. Auf diese Weise können Sie testen, ob die Verbindungsherstellung möglich ist, anstatt zu überprüfen, ob die Namensauflösung richtig konfiguriert ist.

1. Ermitteln Sie die private IP-Adresse. Sie können die private IP-Adresse einer VM ermitteln, indem Sie sich entweder die Eigenschaften für die VM im Azure-Portal ansehen oder PowerShell verwenden.

   - Azure-Portal: Suchen Sie im Azure-Portal nach Ihrem virtuellen Computer. Zeigen Sie die Eigenschaften für die VM an. Die private IP-Adresse ist aufgeführt.

   - PowerShell: Verwenden Sie das Beispiel, um eine Liste mit VMs und privaten IP-Adressen aus Ihren Ressourcengruppen anzuzeigen. Es ist nicht erforderlich, dieses Beispiel vor der Verwendung zu ändern.

     ```powershell
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

2. Vergewissern Sie sich, dass eine Point-to-Site-VPN-Verbindung mit Ihrem VNet besteht.
3. Öffnen Sie eine **Remotedesktopverbindung**, indem Sie auf der Taskleiste im Suchfeld „RDP“ oder „Remotedesktopverbindung“ eingeben und dann „Remotedesktopverbindung“ wählen. Sie können auch den Befehl „mstsc“ in PowerShell verwenden, um eine Remotedesktopverbindung zu öffnen. 
4. Geben Sie in der Remotedesktopverbindung die private IP-Adresse der VM ein. Sie können auf „Optionen anzeigen“ klicken, um weitere Einstellungen anzupassen, und dann die Verbindung herstellen.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>So führen Sie die Problembehandlung für die RDP-Verbindung mit einer VM durch

Falls Sie beim Herstellen einer Verbindung mit einem virtuellen Computer per VPN-Verbindung Probleme haben sollten, überprüfen Sie Folgendes:

- Stellen Sie sicher, dass die Herstellung der VPN-Verbindung erfolgreich war.
- Stellen Sie sicher, dass Sie die Verbindung mit der privaten IP-Adresse für die VM herstellen.
- Überprüfen Sie mit „ipconfig“ die IPv4-Adresse, die dem Ethernet-Adapter auf dem Computer zugewiesen ist, von dem aus Sie die Verbindung herstellen. Wenn sich die IP-Adresse im Adressbereich des VNETs befindet, mit dem Sie die Verbindung herstellen, oder im Adressbereich von „VPNClientAddressPool“ liegt, wird dies als sich überschneidender Adressraum bezeichnet. Falls sich Ihr Adressraum auf diese Weise überschneidet, kommt der Netzwerkdatenverkehr nicht bei Azure an, sondern verbleibt im lokalen Netzwerk.
- Falls Sie mit der privaten IP-Adresse eine Verbindung mit der VM herstellen können, aber nicht mit dem Computernamen, sollten Sie sich vergewissern, dass das DNS richtig konfiguriert ist. Weitere Informationen zur Funktionsweise der Namensauflösung für virtuelle Computer finden Sie unter [Namensauflösung für virtuelle Computer](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
- Stellen Sie sicher, dass das VPN-Clientkonfigurationspaket generiert wurde, nachdem die IP-Adressen des DNS-Server für das VNET angegeben wurden. Wenn Sie die IP-Adressen des DNS-Servers aktualisiert haben, generieren Sie ein neues VPN-Clientkonfigurationspaket und installieren es.
- Weitere Informationen zu RDP-Verbindungen finden Sie unter [Behandeln von Problemen bei Remotedesktopverbindungen mit einem virtuellen Azure-Computer](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md).