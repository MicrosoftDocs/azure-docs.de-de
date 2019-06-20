---
title: Diagnose des Routingproblems einer Azure-VM| Microsoft-Dokumentation
description: Erfahren Sie, wie Sie ein Routingproblem einer VM durch Anzeigen der effektiven Routen für eine VM diagnostizieren.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2018
ms.author: kumud
ms.openlocfilehash: 465d44ea823c99afbb4f25541d64770c114ba7e2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64730507"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>Diagnose des Routingproblems einer VM

In diesem Artikel erfahren Sie, wie Sie ein Routingproblem durch Anzeigen der effektiven Routen für eine Netzwerkschnittstelle in einem virtuellen Computer (VM) diagnostizieren. In Azure werden mehrere Standardrouten für jedes Subnetz eines virtuellen Netzwerks erstellt. Sie können Standardrouten von Azure durch Definieren von Routen in einer Routingtabelle überschreiben und dann die Routingtabelle einem Subnetz zuordnen. Aus der Kombination von Routen, die Sie erstellen, Standardrouten von Azure und allen Routen, die von Ihrem lokalen Netzwerk durch ein Azure-VPN-Gateway (sofern das virtuelle Netzwerk mit Ihrem lokalen Netzwerk verbunden ist) über das Border Gateway Protocol (BGP) verteilt werden, resultieren die effektiven Routen für alle Netzwerkschnittstellen in einem Subnetz. Wenn Sie nicht mit virtuellem Netzwerk, Netzwerkschnittstelle oder Routingkonzepten vertraut sind, lesen Sie [Was ist Azure Virtual Network?](virtual-networks-overview.md), [Erstellen, Ändern oder Löschen von Netzwerkschnittstellen](virtual-network-network-interface.md) und [Routing von Datenverkehr für virtuelle Netzwerke](virtual-networks-udr-overview.md).

## <a name="scenario"></a>Szenario

Sie versuchen, eine Verbindung mit einer VM herzustellen, aber ohne Erfolg. Um zu bestimmen, warum Sie keine Verbindung mit dem virtuellen Computer herstellen können, können Sie die effektiven Routen für eine Netzwerkschnittstelle mit [Azure-Portal](#diagnose-using-azure-portal), [PowerShell](#diagnose-using-powershell) oder [Azure CLI](#diagnose-using-azure-cli) anzeigen.

Die folgenden Schritte setzen voraus, dass Sie über einen virtuellen Computer verfügen, dessen effektive Routen Sie anzeigen können. Wenn Sie nicht über einen virtuellen Computer verfügen, stellen Sie zuerst eine [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)- oder [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-VM zum Ausführen der Aufgaben in diesem Artikel bereit. Die Beispiele in diesem Artikel beziehen sich auf eine VM mit dem Namen *myVM* mit einer Netzwerkschnittstelle namens *myVMVMNic*. VM und Netzwerkschnittstelle gehören zu einer Ressourcengruppe mit dem Namen *myResourceGroup* in der Region *USA, Osten*. Ändern Sie die Werte in den Schritten nach Bedarf für die VM, deren Problem Sie diagnostizieren.

## <a name="diagnose-using-azure-portal"></a>Diagnostizieren über das Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) mit einem Azure-Konto an, dem die [erforderlichen Berechtigungen](virtual-network-network-interface.md#permissions) zugewiesen sind.
2. Geben Sie oben im Azure-Portal in das Suchfeld den Namen eines virtuellen Computers ein, der sich im Ausführungsstatus befindet. Wenn der Name der VM in den Suchergebnissen angezeigt wird, wählen Sie ihn aus.
3. Wählen Sie **Diagnose und Problembehandlung** aus und dann unter **Empfohlene Schritte** die Option **Effektive Routen** in Punkt 7, wie in der folgenden Abbildung gezeigt:

    ![Anzeigen effektiver Routen](./media/diagnose-network-routing-problem/view-effective-routes.png)

4. Die effektiven Routen für eine Netzwerkschnittstelle namens **myVMVMNic** werden in der folgenden Abbildung dargestellt:

     ![Anzeigen effektiver Routen](./media/diagnose-network-routing-problem/effective-routes.png)

    Wenn dem virtuellen Computer mehrere Netzwerkschnittstellen angefügt sind, können Sie die effektiven Routen für eine Netzwerkschnittstelle anzeigen, indem Sie sie auswählen. Da sich jede Netzwerkschnittstelle in einem anderen Subnetz befinden kann, kann jede Netzwerkschnittstelle über unterschiedliche effektive Routen verfügen.

    In dem in der vorherigen Abbildung gezeigten Beispiel sind die aufgelisteten Routen Standardrouten, die Azure für jedes Subnetz erstellt. Ihre Liste verfügt mindestens über diese Routen, aber möglicherweise über zusätzliche Routen, je nach den Funktionen, die Sie für Ihr virtuelles Netzwerk aktiviert haben, z.B. seine Verbindung im Peering mit einem anderen virtuellen Netzwerk oder seine Verbindung mit dem lokalen Netzwerk über ein Azure-VPN-Gateway. Weitere Informationen zu den einzelnen Routen und anderen Routen, die möglicherweise für die Netzwerkschnittstelle angezeigt werden, finden Sie unter [Routing von Datenverkehr für virtuelle Netzwerke](virtual-networks-udr-overview.md). Wenn die Liste eine große Anzahl von Routen aufweist, ist es einfacher, wenn Sie **Herunterladen** auswählen, um eine CSV-Datei mit der Liste der Routen herunterzuladen.

Effektive Routen können nicht nur, wie in den vorherigen Schritten, über die VM angezeigt werden, sondern auch folgendermaßen:
- **Individuelle Netzwerkschnittstelle:** Erfahren Sie, wie Sie [eine Netzwerkschnittstelle anzeigen](virtual-network-network-interface.md#view-network-interface-settings).
- **Individuelle Routingtabelle:** Erfahren Sie, wie Sie [eine Routingtabelle anzeigen](manage-route-table.md#view-details-of-a-route-table).

## <a name="diagnose-using-powershell"></a>Diagnose mit PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Sie können die nachfolgenden Befehle in [Azure Cloud Shell](https://shell.azure.com/powershell) oder über PowerShell auf Ihrem Computer ausführen. Azure Cloud Shell ist eine kostenlose interaktive Shell. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. Wenn Sie PowerShell auf Ihrem Computer ausführen, müssen Sie das Azure PowerShell-Modul Version 1.0.0 oder höher ausführen. Führen Sie `Get-Module -ListAvailable Az` auf Ihrem Computer aus, um nach der installierten Version zu suchen. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um sich bei Azure mit einem Konto anzumelden, das über die [erforderlichen Berechtigungen](virtual-network-network-interface.md#permissions) verfügt.

Rufen Sie die effektiven Routen für eine Netzwerkschnittstelle mit [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) auf. Im folgenden Beispiel werden die effektiven Routen für eine Netzwerkschnittstelle mit dem Namen *myVMVMNic* abgerufen, die sich in einer Ressourcengruppe mit dem Namen *myResourceGroup* befindet:

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVMVMNic `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

Um die in der Ausgabe zurückgegebenen Informationen zu verstehen, lesen Sie [Routing von Datenverkehr für virtuelle Netzwerke](virtual-networks-udr-overview.md). Die Ausgabe wird nur zurückgegeben, wenn der virtuelle Computer ausgeführt wird. Wenn dem virtuellen Computer mehrere Netzwerkschnittstellen angefügt sind, können Sie die effektiven Routen für jede Netzwerkschnittstelle anzeigen. Da sich jede Netzwerkschnittstelle in einem anderen Subnetz befinden kann, kann jede Netzwerkschnittstelle über unterschiedliche effektive Routen verfügen. Wenn das Kommunikationsproblem weiterhin besteht, lesen Sie die Abschnitte [Zusätzliche Diagnose](#additional-diagnosis) und [Überlegungen](#considerations).

Wenn Sie den Namen einer Netzwerkschnittstelle nicht kennen, aber den Namen der VM, der die Netzwerkschnittstelle angefügt ist, geben die folgenden Befehle die IDs aller Netzwerkschnittstellen zurück, die einer VM angefügt sind:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM `
  -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

Eine Ausgabe ähnlich wie im folgenden Beispiel wird angezeigt:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic
```

In der vorherigen Ausgabe lautete der Netzwerkschnittstellenname *myVMVMNic*.

## <a name="diagnose-using-azure-cli"></a>Diagnose über die Azure CLI

Sie können die nachfolgenden Befehle in [Azure Cloud Shell](https://shell.azure.com/bash) oder über die CLI auf Ihrem Computer ausführen. Für diesen Artikel ist die Azure CLI-Version 2.0.32 oder höher erforderlich. Führen Sie `az --version` aus, um die installierte Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli). Wenn Sie die Azure CLI lokal ausführen, müssen Sie auch `az login` ausführen, um sich bei Azure mit einem Konto anzumelden, das über die [erforderlichen Berechtigungen](virtual-network-network-interface.md#permissions) verfügt.

Rufen Sie mit [az network nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) die effektiven Routen für eine Netzwerkschnittstelle ab. Im folgenden Beispiel werden die effektiven Routen für eine Netzwerkschnittstelle mit dem Namen *myVMVMNic* abgerufen, die sich in einer Ressourcengruppe mit dem Namen *myResourceGroup* befindet:

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMVMNic \
  --resource-group myResourceGroup
```

Um die in der Ausgabe zurückgegebenen Informationen zu verstehen, lesen Sie [Routing von Datenverkehr für virtuelle Netzwerke](virtual-networks-udr-overview.md). Die Ausgabe wird nur zurückgegeben, wenn der virtuelle Computer ausgeführt wird. Wenn dem virtuellen Computer mehrere Netzwerkschnittstellen angefügt sind, können Sie die effektiven Routen für jede Netzwerkschnittstelle anzeigen. Da sich jede Netzwerkschnittstelle in einem anderen Subnetz befinden kann, kann jede Netzwerkschnittstelle über unterschiedliche effektive Routen verfügen. Wenn das Kommunikationsproblem weiterhin besteht, lesen Sie die Abschnitte [Zusätzliche Diagnose](#additional-diagnosis) und [Überlegungen](#considerations).

Wenn Sie den Namen einer Netzwerkschnittstelle nicht kennen, aber den Namen der VM, der die Netzwerkschnittstelle angefügt ist, geben die folgenden Befehle die IDs aller Netzwerkschnittstellen zurück, die einer VM angefügt sind:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>Beheben eines Problems

Die Lösung von Routingproblemen erfolgt in der Regel folgendermaßen:

- Fügen Sie eine benutzerdefinierte Route hinzu, um die Standardrouten von Azure außer Kraft zu setzen. Erfahren Sie, wie Sie [eine benutzerdefinierte Route hinzufügen](manage-route-table.md#create-a-route).
- Ändern oder entfernen Sie eine benutzerdefinierte Route, die eine Weiterleitung an einen unerwünschten Ort zur Folge haben könnte. Erfahren Sie, wie Sie eine benutzerdefinierte Route [ändern](manage-route-table.md#change-a-route) oder [löschen](manage-route-table.md#delete-a-route).
- Stellen Sie sicher, dass die Routingtabelle, die benutzerdefinierte Routen enthält, die Sie definiert haben, dem Subnetz zugeordnet ist, in dem sich die Netzwerkschnittstelle befindet. Erfahren Sie, wie Sie [eine Routingtabelle einem Subnetz zuordnen](manage-route-table.md#associate-a-route-table-to-a-subnet).
- Stellen Sie sicher, dass Geräte wie das Azure-VPN-Gateway oder virtuelle Netzwerkappliances, die Sie bereitgestellt haben, funktionsfähig sind. Verwenden Sie die [VPN-Diagnose](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) von Network Watcher, um Probleme mit einem Azure-VPN-Gateway zu bestimmen.

Falls weiterhin Probleme mit der Kommunikation bestehen, lesen Sie die Abschnitte [Überlegungen](#considerations) und „Zusätzliche Diagnose“.

## <a name="considerations"></a>Überlegungen

Beachten Sie bei der Behandlung von Kommunikationsproblemen die folgenden Punkte:

- Routing basiert auf der längsten Präfixübereinstimmung (Longest Prefix Match, LPM) bei Routen, die Sie definiert haben, Border Gateway Protocol (BGP) und Systemrouten. Wenn mehrere Routen mit identischer längster Präfixübereinstimmung vorhanden sind, wird die Route gemäß ihrem Ursprung in der unter [Auswahl einer Route durch Azure](virtual-networks-udr-overview.md#how-azure-selects-a-route) aufgelisteten Reihenfolge ausgewählt. Mit effektiven Routen können Sie nur die effektiven Routen mit der basierend auf allen verfügbaren Routen jeweils längsten Präfixübereinstimmung anzeigen. Zu sehen, wie die Routen für eine Netzwerkschnittstelle ausgewertet werden, vereinfacht die Problembehandlung bestimmter Routen erheblich, die die Verbindung mit dem virtuellen Computer beeinträchtigen können.
- Wenn Sie benutzerdefinierte Routen zu einer virtuellen Netzwerkappliance (Network Virtual Appliance, NVA) mit *Virtuelles Gerät* als nächstem Hoptyp definiert haben, müssen Sie sicherstellen, dass die IP-Weiterleitung auf der NVA aktiviert ist, die den Datenverkehr empfängt – andernfalls werden Pakete verworfen. Erfahren Sie mehr über das [Aktivieren der IP-Weiterleitung für eine Netzwerkschnittstelle](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). Darüber hinaus muss das Betriebssystem oder die Anwendung innerhalb der NVA auch Netzwerkdatenverkehr weiterleiten können und dafür konfiguriert sein.
- Wenn Sie eine Route zu 0.0.0.0/0 erstellt haben, wird der gesamte ausgehende Internetdatenverkehr an den nächsten Hop weitergeleitet, den Sie angegeben haben, z.B. zu einer NVA oder einem VPN-Gateway. Das Erstellen einer solchen Route wird häufig als Tunnelerzwingung bezeichnet. Remoteverbindungen zwischen dem Internet und Ihrem virtuellen Computer mithilfe der Protokolle RDP oder SSH funktionieren mit dieser Route möglicherweise nicht, je nachdem, wie der nächste Hop den Datenverkehr verarbeitet. Die Tunnelerzwingung kann in folgenden Fällen aktiviert werden:
    - Bei Verwendung eines Site-to-Site-VPN beim Erstellen einer Route mit dem nächsten Hoptyp *VPN Gateway*. Weitere Informationen zum [Konfigurieren der Tunnelerzwingung](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - Wenn 0.0.0.0/0 (Standardroute) über BGP über ein virtuelles Netzwerkgateway angekündigt wird, bei Verwendung eines Site-to-Site-VPN oder einer ExpressRoute-Leitung. Weitere Informationen zur Verwendung von BGP mit [Site-to-Site-VPN](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) oder [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering) finden Sie hier.
- Damit der Peering-Datenverkehr des virtuellen Netzwerks ordnungsgemäß weitergeleitet wird, muss eine Systemroute mit einem nächsten Hoptyp *VNET-Peering* für den Präfixbereich des per Peering verknüpften virtuellen Netzwerks vorhanden sein. Wenn eine solche Route nicht vorhanden und der Peeringlink des virtuellen Netzwerks **Verbunden** ist:
    - Warten Sie einige Sekunden, und versuchen Sie es erneut. Wenn es sich um einen neu eingerichteten Peeringlink handelt, dauert das Weitergeben von Routen an alle Netzwerkschnittstellen in einem Subnetz gelegentlich etwas länger. Weitere Informationen zum Peering in virtuellen Netzwerken finden Sie unter [Peering in virtuellen Netzwerken](virtual-network-peering-overview.md) und [Erstellen, Ändern oder Löschen eines Peerings virtueller Netzwerke](virtual-network-manage-peering.md).
    - Die Regeln von Netzwerksicherheitsgruppen (NSGs) können die Kommunikation beeinträchtigen. Weitere Informationen finden Sie unter [Diagnostizieren von Problemen mit dem Filter für Netzwerkdatenverkehr eines virtuellen Computers](diagnose-network-traffic-filter-problem.md).
- Wenn dem virtuellen Computer mehrere Netzwerkschnittstellen angefügt sind, wird nur der primären Netzwerkschnittstelle im Betriebssystem des virtuellen Computers eine Standardroute (0.0.0.0/0) oder ein Gateway zugewiesen, obwohl Azure jeder Azure-Netzwerkschnittstelle Standardrouten zuweist. Erfahren Sie, wie Sie eine Standardroute für sekundäre Netzwerkschnittstellen erstellen, die einer [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics)- oder [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics)-VM angefügt sind. Erfahren Sie mehr über [primäre und sekundäre Netzwerkschnittstellen](virtual-network-network-interface-vm.md#constraints).

## <a name="additional-diagnosis"></a>Zusätzliche Diagnose

* Um einen Schnelltest zur Bestimmung des nächsten Hoptyps für den an einen bestimmten Standort gerichteten Datenverkehr durchzuführen, nutzen Sie die Funktion [Nächster Hop](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) von Azure Network Watcher. „Nächster Hop“ gibt Ihnen Aufschluss über den nächsten Hoptyp für Datenverkehr, der an einen bestimmten Standort gerichtet ist.
* Wenn keine Routen vorliegen, die bei der Netzwerkkommunikation eines virtuellen Computers einen Fehler auslösen, wird das Problem möglicherweise durch die Firewallsoftware verursacht, die im Betriebssystem des virtuellen Computers ausgeführt wird.
* Wenn Sie eine [Tunnelerzwingung](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) für den Datenverkehr zu einem lokalen Gerät über ein VPN-Gateway oder eine NVA durchführen, können Sie je nachdem, wie Sie das Routing für die Geräte konfiguriert haben, möglicherweise keine Verbindung mit einer VM aus dem Internet herstellen. Vergewissern Sie sich, dass das Routing, das Sie für das Gerät konfiguriert haben, den Datenverkehr entweder an eine öffentliche oder private IP-Adresse für den virtuellen Computer weiterleitet.
* Verwenden Sie die Funktion [Problembehandlung für Verbindung](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) von Network Watcher, um Probleme mit der ausgehenden Kommunikation zu ermitteln, deren Ursache in Routing oder Filtern liegt bzw. betriebssystemintern ist.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über alle Aufgaben, Eigenschaften und Einstellungen für eine [Routingtabelle und Routen](manage-route-table.md).
- Erfahren Sie mehr über alle [nächsten Hoptypen, Systemrouten und die Art und Weise, wie Azure eine Route auswählt](virtual-networks-udr-overview.md).
