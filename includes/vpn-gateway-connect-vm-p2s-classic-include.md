---
title: Includedatei
description: Includedatei
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 6d0737a7300b2a6025f776c1ed65a05cacf2141a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66157662"
---
Stellen Sie eine Remotedesktopverbindung mit einer VM her, die in Ihrem VNet bereitgestellt wird. Die beste Möglichkeit, zu überprüfen, ob Sie eine Verbindung mit Ihrer VM herstellen können, ist das Herstellen der Verbindung mit der privaten IP-Adresse anstelle des Computernamens. Auf diese Weise können Sie testen, ob die Verbindungsherstellung möglich ist, anstatt zu überprüfen, ob die Namensauflösung richtig konfiguriert ist. 

1. Ermitteln Sie die private IP-Adresse für Ihren virtuellen Computer. Um die private IP-Adresse einer VM zu ermitteln, zeigen Sie entweder die Eigenschaften für die VM im Azure-Portal an, oder verwenden Sie PowerShell.
2. Vergewissern Sie sich, dass eine Point-to-Site-VPN-Verbindung mit Ihrem VNET besteht. 
3. Um eine Remotedesktopverbindung zu öffnen, geben Sie *RDP* oder *Remotedesktopverbindung* im Suchfeld auf der Taskleiste ein, und wählen Sie dann **Remotedesktopverbindung** aus. Sie können auch den Befehl **mstsc** in PowerShell verwenden, um eine Remotedesktopverbindung zu öffnen. 
3. Geben Sie in **Remotedesktopverbindung** die private IP-Adresse der VM ein. Wählen Sie bei Bedarf **Optionen anzeigen** aus, um weitere Einstellungen anzupassen und dann die Verbindung herzustellen.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>So führen Sie die Problembehandlung für die RDP-Verbindung mit einer VM durch

Falls Sie beim Herstellen einer Verbindung mit einem virtuellen Computer per VPN-Verbindung Probleme haben sollten, können Sie einige Dinge überprüfen. 

- Stellen Sie sicher, dass die Herstellung der VPN-Verbindung erfolgreich war.
- Stellen Sie sicher, dass Sie die Verbindung mit der privaten IP-Adresse für die VM herstellen.
- Überprüfen Sie mit **ipconfig** die IPv4-Adresse, die dem Ethernet-Adapter auf dem Computer zugewiesen ist, von dem aus Sie die Verbindung herstellen. Wenn sich die IP-Adresse im Adressbereich des VNETs befindet, mit dem Sie die Verbindung herstellen, oder im Adressbereich von VPNClientAddressPool liegt, wird dies als sich überschneidender Adressraum bezeichnet. Falls sich Ihr Adressraum auf diese Weise überschneidet, kommt der Netzwerkdatenverkehr nicht bei Azure an, sondern verbleibt im lokalen Netzwerk.
- Falls Sie mit der privaten IP-Adresse eine Verbindung mit der VM herstellen können, aber nicht mit dem Computernamen, sollten Sie sich vergewissern, dass das DNS richtig konfiguriert ist. Weitere Informationen zur Funktionsweise der Namensauflösung für virtuelle Computer finden Sie unter [Namensauflösung für virtuelle Computer](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
- Stellen Sie sicher, dass das VPN-Clientkonfigurationspaket generiert wurde, nachdem Sie die IP-Adressen des DNS-Servers für das VNET angegeben hatten. Wenn Sie die IP-Adressen des DNS-Servers aktualisieren, generieren Sie ein neues VPN-Clientkonfigurationspaket, und installieren Sie es.

Weitere Informationen zur Problembehandlung finden Sie unter [Behandeln von Problemen bei Remotedesktopverbindungen mit einem virtuellen Azure-Computer](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md).