---
title: Erstellen, Ändern oder Löschen eines Peerings virtueller Azure-Netzwerke | Microsoft-Dokumentation
description: Erstellen, ändern oder löschen Sie das Peering virtueller Netzwerke. Beim Peering virtueller Netzwerke verbinden Sie virtuelle Netzwerke in derselben Region und regionsübergreifend.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: altambaw
ms.openlocfilehash: b5099f839da4547ab9551ea131fa65100d25fe65
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763042"
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>Erstellen, Ändern oder Löschen eines Peerings virtueller Netzwerke

In diesem Artikel erfahren Sie, wie Sie ein Peering virtueller Netzwerke erstellen, ändern oder löschen. VNET-Peering ist ein Mechanismus, mit dem virtuelle Netzwerke (VNETs) in der gleichen Region und regionsübergreifend (auch globales VNet-Peering genannt) über das Azure-Backbonenetzwerk miteinander verbunden werden können. Nach dem Peering werden die virtuellen Netzwerke weiterhin als separate Ressourcen verwaltet. Wenn Sie mit dem Peering virtueller Netzwerke noch nicht vertraut sind, lesen Sie mehr darüber in der [Übersicht über das Peering virtueller Netzwerke](virtual-network-peering-overview.md), oder arbeiten Sie ein [Tutorial](tutorial-connect-virtual-networks-portal.md) durch.

## <a name="before-you-begin"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Führen Sie zuerst die folgenden Aufgaben aus, ehe Sie die Schritte in den Abschnitten dieses Artikels durchführen:

- Falls Sie noch nicht über ein Azure-Konto verfügen, können Sie sich für ein [kostenloses Testkonto](https://azure.microsoft.com/free) registrieren.
- Wenn Sie das Portal verwenden, öffnen Sie https://portal.azure.com, und melden Sie sich mit einem Konto an, das über die [erforderlichen Berechtigungen](#permissions) für die Verwendung von Peerings verfügt.
- Wenn Sie PowerShell-Befehle zum Durchführen von Aufgaben in diesem Artikel verwenden, führen Sie die Befehle entweder in [Azure Cloud Shell](https://shell.azure.com/powershell) oder durch Ausführen von PowerShell auf Ihrem Computer aus. Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. Für dieses Tutorial ist das Azure PowerShell-Modul Version 1.0.0 oder höher erforderlich. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie zum Herstellen einer Verbindung mit Azure auch `Connect-AzAccount` mit einem Konto ausführen, das über die [erforderlichen Berechtigungen](#permissions) für die Verwendung von Peerings verfügt.
- Wenn Sie Befehle der Azure-Befehlszeilenschnittstelle (CLI) zum Durchführen von Aufgaben in diesem Artikel verwenden, führen Sie die Befehle entweder in [Azure Cloud Shell](https://shell.azure.com/bash) oder durch Ausführen der CLI auf Ihrem Computer aus. Für dieses Tutorial ist die Azure CLI-Version 2.0.31 oder höher erforderlich. Führen Sie `az --version` aus, um die installierte Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli). Wenn Sie die Azure CLI lokal ausführen, müssen Sie zum Herstellen einer Verbindung mit Azure auch `az login` mit einem Konto ausführen, das über die [erforderlichen Berechtigungen](#permissions) für die Verwendung von Peerings verfügt.

Das Konto, bei dem Sie sich anmelden oder das Sie zum Herstellen einer Verbindung mit Azure verwenden, muss der Rolle [Netzwerkmitwirkender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) oder einer [benutzerdefinierten Rolle](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) zugewiesen sein, der die entsprechenden, unter [Berechtigungen](#permissions) aufgeführten Aktionen zugewiesen wurden.

## <a name="create-a-peering"></a>Erstellen eines Peerings

Informieren Sie sich vor der Erstellung eines Peerings über die Anforderungen und Einschränkungen sowie die [erforderlichen Berechtigungen](#permissions).

1. Geben Sie oben im Azure-Portal *Virtuelle Netzwerke* in das Suchfeld ein. Wenn **Virtuelle Netzwerke** in den Suchergebnissen angezeigt wird, können Sie den Begriff auswählen. Aktivieren Sie nicht **Virtuelle Netzwerke (klassisch)** , wenn diese Option in der Liste angezeigt wird, da über das klassische Bereitstellungsmodell kein Peering von einem bereitgestellten virtuellen Netzwerk aus erstellt werden kann.
2. Wählen Sie das virtuelle Netzwerk in der Liste aus, für das Sie ein Peering erstellen möchten.
3. Wählen Sie unter **EINSTELLUNGEN** die Option **Peerings** aus.
4. Wählen Sie **+ Hinzufügen**. 
5. <a name="add-peering"></a>Geben Sie Werte für folgende Einstellungen ein, oder wählen Sie sie aus:
    - **Name**: Der Name für das Peering muss innerhalb des virtuellen Netzwerks eindeutig sein.
    - **Bereitstellungsmodell für das virtuelle Netzwerk**: Wählen Sie aus, über welches Bereitstellungsmodell das mittels Peering verknüpfte virtuelle Netzwerk bereitgestellt wurde.
    - **Ich kenne meine Ressourcen-ID**: Wenn Sie Lesezugriff auf das virtuelle Netzwerk haben, das mittels Peering verknüpft werden sollen, lassen Sie dieses Kontrollkästchen deaktiviert. Wenn Sie keinen Lesezugriff auf das virtuelle Netzwerk oder das Abonnement besitzen, das mittels Peering verknüpft werden soll, aktivieren Sie dieses Kontrollkästchen. Geben Sie in das Feld **Ressourcen-ID**, das bei Aktivierung des Kontrollkästchens angezeigt wird, die vollständige Ressourcen-ID des mittels Peering verknüpften virtuellen Netzwerks ein. Die von Ihnen eingegebene Ressourcen-ID muss für ein virtuelles Netzwerk bestimmt sein, das sich in der gleichen Azure-[Region](https://azure.microsoft.com/regions) wie dieses virtuelle Netzwerk oder in einer [anderen unterstützten Region](#requirements-and-constraints) befindet. Die vollständige Ressourcen-ID sieht ähnlich aus wie `/subscriptions/<Id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>`. Sie können die Ressourcen-ID für ein virtuelles Netzwerk abrufen, indem Sie die Eigenschaften eines virtuellen Netzwerks anzeigen. Informationen zum Anzeigen der Eigenschaften eines virtuellen Netzwerks finden Sie im Artikel [Verwalten virtueller Netzwerke](manage-virtual-network.md#view-virtual-networks-and-settings). Wenn das Abonnement einem anderen Azure Active Directory-Mandanten als das Abonnement mit dem virtuellen Netzwerk, über das Sie das Peering erstellen, zugeordnet ist, müssen Sie zuerst einen Benutzer aus jedem Mandanten als [Gastbenutzer](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) im entsprechenden anderen Mandanten hinzufügen.
    - **Abonnement**: Wählen Sie das [Abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) des virtuellen Netzwerks aus, das mittels Peering verknüpft werden soll. Je nachdem, auf wie viele Abonnements Ihr Konto Lesezugriff verfügt, werden ein oder mehrere Abonnements aufgeführt. Wenn Sie das Kontrollkästchen **Ressourcen-ID** aktivieren, ist diese Einstellung ist verfügbar.
    - **Virtuelles Netzwerk**: Wählen Sie das virtuelle Netzwerk aus, das mittels Peering verknüpft werden soll. Sie können ein virtuelles Netzwerk auswählen, das mit einem beliebigen Azure-Bereitstellungsmodell erstellt wurde. Sie können nur ein virtuelles Netzwerk in einer anderen Region auswählen, das [unterstützt](#cross-region) wird. Sie benötigen Lesezugriff auf das virtuelle Netzwerk, damit es in der Liste angezeigt wird. Wenn ein virtuelles Netzwerk aufgeführt wird, jedoch ausgegraut ist, überschneidet sich der Adressraum des virtuellen Netzwerks möglicherweise mit dem Adressraum dieses virtuellen Netzwerks. Wenn sich die Adressräume des virtuellen Netzwerks überschneidet, können diese nicht mittels Peering verknüpft werden. Wenn Sie das Kontrollkästchen **Ressourcen-ID** aktivieren, ist diese Einstellung ist verfügbar.
    - **Zugriff auf virtuelles Netzwerk zulassen:** Wählen Sie **Aktiviert** (Standard) aus, wenn Kommunikation zwischen beiden virtuellen Netzwerken über den Standardflow `VirtualNetwork` aktiviert werden soll. Durch die Aktivierung der Kommunikation zwischen virtuellen Netzwerken können Ressourcen, die mit einem der beiden virtuellen Netzwerke verbunden sind, mit derselben Bandbreite und Latenz kommunizieren, als ob sie mit demselben virtuellen Netzwerk verbunden wären. Die gesamte Kommunikation zwischen Ressourcen in den beiden virtuellen Netzwerken erfolgt über das private Azure-Netzwerk. Das Diensttag **VirtualNetwork** für Netzwerksicherheitsgruppen umfasst das virtuelle Netzwerk und das mittels Peering verknüpfte virtuelle Netzwerk, sofern diese Einstellung **Aktiviert** lautet. (Weitere Informationen zu Diensttags von Netzwerksicherheitsgruppen finden Sie unter [Netzwerksicherheitsgruppen – Übersicht](security-overview.md#service-tags).) Wählen Sie **Deaktiviert** aus, wenn der Datenverkehr nicht standardmäßig über das mittels Peering verknüpfte virtuelle Netzwerk weitergeleitet werden soll. Sie können **Deaktiviert** auswählen, wenn Sie ein virtuelles Netzwerk mittels Peering mit einem anderen virtuellen Netzwerk verknüpft haben, jedoch die Möglichkeit haben möchten, den Standarddatenverkehr zwischen den beiden virtuellen Netzwerken gelegentlich zu deaktivieren. Es kann u.U. praktischer sein, die Option zu aktivieren bzw. zu deaktivieren statt Peerings zu löschen und neu zu erstellen. Wenn diese Einstellung deaktiviert ist, wird standardmäßig kein Datenverkehr zwischen den mittels Peering verknüpften virtuellen Netzwerken übertragen. Datenverkehr ist jedoch weiterhin möglich, wenn dies mit einer Regel für die [Netzwerksicherheitsgruppe](security-overview.md) explizit zugelassen wird, die die entsprechenden IP-Adressen oder Anwendungssicherheitsgruppen enthält.
      > [!WARNING]
      > Wenn Sie die Einstellung **Zugriff auf virtuelles Netzwerk zulassen** deaktivieren, wird nur die Definition des Diensttags **VirtualNetwork** geändert. Dabei wird *nicht* der komplette Datenverkehrsfluss über die Peerverbindung verhindert, wie in der Einstellungsbeschreibung erläutert.    
    - **Weitergeleiteten Datenverkehr zulassen**: Aktivieren Sie dieses Kontrollkästchen, um zuzulassen, dass der von einem virtuellen Netzwerkgerät *weitergeleitete* Datenverkehr (der nicht von dem virtuellen Netzwerk generiert wird) mittels Peering über dieses virtuelle Netzwerk erfolgt. Sehen Sie sich beispielsweise die drei virtuellen Netzwerke mit dem Namen „Spoke1“, „Spoke2“ und „Hub“ an. Die einzelnen Spokes und der Hub des virtuellen Netzwerks sind mittels Peering miteinander verknüpft, jedoch nicht die Spokes des virtuellen Netzwerks untereinander. Im Hub des virtuellen Netzwerks wird eine virtuelle Netzwerkappliance bereitgestellt, während bei den einzelnen Spokes des virtuellen Netzwerks benutzerdefinierte Routen angewendet werden, die Datenverkehr zwischen den Subnetzen über die virtuelle Netzwerkappliance weiterleiten. Wenn dieses Kontrollkästchen für das Peering zwischen den einzelnen Spokes und dem Hub des virtuellen Netzwerks nicht aktiviert ist, fließt kein Datenverkehr zwischen den Spokes des virtuellen Netzwerks, da der Hub den Datenverkehr zwischen den virtuellen Netzwerken nicht weiterleitet. Durch die Aktivierung dieser Funktion wird zwar die Weiterleitung des Datenverkehrs mittels Peering ermöglicht, es werden jedoch keine benutzerdefinierten Routen oder virtuellen Netzwerkgeräte erstellt. Benutzerdefinierte Routen und virtuelle Netzwerkgeräte werden separat erstellt. Weitere Informationen hierzu finden Sie unter [Benutzerdefinierte Routen](virtual-networks-udr-overview.md#user-defined). Sie müssen diese Einstellung nicht überprüfen, wenn der Datenverkehr zwischen virtuellen Netzwerken über ein Azure VPN Gateway weitergeleitet wird.
    - **Gatewaytransit zulassen**: Aktivieren Sie dieses Kontrollkästchen, wenn ein Gateway für virtuelle Netzwerke mit diesem virtuellen Netzwerk verbunden ist und Sie zulassen möchten, dass Datenverkehr von mittels Peering verknüpften virtuellen Netzwerken über das Gateway geleitet werden soll. Dieses virtuelle Netzwerk kann z.B. über ein Gateway für virtuelle Netzwerke mit einem lokalen Netzwerk verbunden werden. Bei dem Gateway kann es sich um ein ExpressRoute- oder VPN-Gateway handeln. Wird dieses Kontrollkästchen aktiviert, kann Datenverkehr aus dem mittels Peering verknüpften virtuellen Netzwerk über das mit diesem virtuellen Netzwerk verbundene Gateway an das lokale Netzwerk weitergeleitet werden. Wenn Sie dieses Kontrollkästchen aktivieren, darf für das mittels Peering verknüpfte virtuelle Netzwerk kein Gateway konfiguriert sein. Wenn Sie das Peering vom anderen virtuellen Netzwerk mit diesem virtuellen Netzwerk einrichten, muss für das mittels Peering verknüpfte virtuelle Netzwerk das Kontrollkästchen **Remotegateways verwenden** aktiviert sein. Wenn Sie dieses Kontrollkästchen deaktiviert (Standard) lassen, wird der Datenverkehr trotzdem von dem mittels Peering verknüpften Netzwerk an dieses virtuelle Netzwerk weitergeleitet, kann jedoch nicht über ein mit diesem virtuellen Netzwerk verbundenes Gateway für virtuelle Netzwerke erfolgen. Wenn das Peering zwischen einem virtuellen Netzwerk (Resource Manager) und einem virtuellen Netzwerk (klassisch) hergestellt wird, muss sich das Gateway im virtuellen Netzwerk (Resource Manager) befinden.

       Zusätzlich zum Weiterleiten von Datenverkehr an ein lokales Netzwerk kann ein VPN-Gateway Netzwerkdatenverkehr zwischen virtuellen Netzwerken weiterleiten, die mittels Peering mit dem virtuellen Netzwerk, in dem das Gateway enthalten ist, verknüpft sind. Die virtuellen Netzwerke müssen dabei nicht untereinander mittels Peering verknüpft werden. Die Weiterleitung von Datenverkehr über ein VPN-Gateway ist nützlich, wenn Sie ein VPN-Gateway im Hub eines virtuellen Netzwerks verwenden möchten, um Datenverkehr zwischen den Spokes des virtuellen Netzwerks, die nicht untereinander verknüpft sind, weiterzuleiten. (Weitere Informationen finden Sie in dem beschriebenen Beispiel zur Option **Weitergeleiteten Datenverkehr zulassen** bezüglich der Hub-Spoke-Topologie.) Weitere Informationen zum Zulassen der Verwendung eines Gateways für den Transit finden Sie unter [Konfigurieren eines VPN-Gateways für den Transit in einem Peering virtueller Netzwerke](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json). In diesem Szenario müssen benutzerdefinierte Routen implementiert werden, die das Gateway für virtuelle Netzwerke als Typ des nächsten Hops angeben. Weitere Informationen hierzu finden Sie unter [Benutzerdefinierte Routen](virtual-networks-udr-overview.md#user-defined). Als Typ des nächsten Hops in einer benutzerdefinierten Route können Sie nur ein VPN-Gateway angeben, jedoch kein ExpressRoute-Gateway.

    - **Remotegateways verwenden**: Aktivieren Sie dieses Kontrollkästchen, wenn Sie zulassen möchten, dass Datenverkehr von diesen virtuellen Netzwerken über ein Gateway für virtuelle Netzwerke geleitet werden soll, das mit dem mittels Peering verknüpften virtuellen Netzwerk verbunden ist. Beispielsweise ist an das virtuelle Netzwerk, mit dem Sie mittels Peering eine Verbindung herstellen, ein VPN-Gateway verbunden, dass Kommunikation mit einem lokalen Netzwerk ermöglicht.  Wird dieses Kontrollkästchen aktiviert, kann Datenverkehr aus diesem virtuellen Netzwerk über das verbundene VPN-Gateway an dieses mittels Peering verknüpfte virtuelle Netzwerk weitergeleitet werden. Wenn Sie dieses Kontrollkästchen aktivieren, muss ein Gateway für virtuelle Netzwerke mit dem mittels Peering verknüpften virtuellen Netzwerk verbunden sein. Zudem muss das Kontrollkästchen **Gatewaytransit zulassen** aktiviert sein. Wenn Sie dieses Kontrollkästchen deaktiviert (Standard) lassen, kann der Datenverkehr trotzdem von dem mittels Peering verknüpften Netzwerk an dieses virtuelle Netzwerk weitergeleitet werden, kann jedoch nicht über ein mit diesem virtuellen Netzwerk verbundenes Gateway für virtuelle Netzwerke erfolgen.
    
      Diese Einstellung kann nur bei einem Peering für dieses virtuelle Netzwerk aktiviert sein.

      Wenn bereits ein Gateway in Ihrem virtuellen Netzwerk konfiguriert ist, können keine Remotegateways verwendet werden. Weitere Informationen zur Verwendung eines Gateways für den Transit finden Sie unter [Konfigurieren eines VPN-Gateways für den Transit in einem Peering virtueller Netzwerke](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
        
    > [!NOTE]
    > Wenn Sie ein Virtual Network-Gateway verwenden, um den lokalen Datenverkehr transitiv an ein mittels Peering verknüpftes VNet zu senden, muss der IP-Adressbereich des mittels Peering verknüpften VNets für das lokale VPN-Gerät auf „interessanter“ Datenverkehr festgelegt werden. Andernfalls können Ihre lokalen Ressourcen nicht mit Ressourcen im mittels Peering verknüpften VNet kommunizieren.

6. Klicken Sie auf **OK**, um das Peering dem ausgewählten virtuellen Netzwerk hinzuzufügen.

Ausführliche Anweisungen zum Implementieren von Peerings zwischen virtuellen Netzwerken in verschiedenen Abonnements und Bereitstellungsmodellen finden Sie unter [Nächste Schritte](#next-steps).

### <a name="commands"></a>Befehle

- **Azure CLI**: [az network vnet peering create](/cli/azure/network/vnet/peering)
- **PowerShell**: [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering)

## <a name="view-or-change-peering-settings"></a>Anzeigen oder Ändern von Peeringeinstellungen

Informieren Sie sich vor der Änderung eines Peerings über die Anforderungen und Einschränkungen sowie die [erforderlichen Berechtigungen](#permissions).

1. Geben Sie oben im Portal *Virtuelle Netzwerke* in das Suchfeld ein. Wenn **Virtuelle Netzwerke** in den Suchergebnissen angezeigt wird, können Sie den Begriff auswählen. Aktivieren Sie nicht **Virtuelle Netzwerke (klassisch)** , wenn diese Option in der Liste angezeigt wird, da über das klassische Bereitstellungsmodell kein Peering von einem bereitgestellten virtuellen Netzwerk aus erstellt werden kann.
2. Wählen Sie das virtuelle Netzwerk in der Liste aus, für das Sie Peering-Einstellungen ändern möchten.
3. Wählen Sie unter **EINSTELLUNGEN** die Option **Peerings** aus.
4. Wählen Sie das Peering aus, dessen Einstellungen angezeigt oder geändert werden sollen.
5. Ändern Sie die entsprechende Einstellung. Lesen Sie die Informationen zu den Optionen für jede Einstellung in [Schritt 5](#add-peering) von „Erstellen eines Peerings“.
6. Wählen Sie **Speichern** aus.

**Befehle**

- **Azure CLI**: [az network vnet peering list](/cli/azure/network/vnet/peering) zum Auflisten von Peerings für ein virtuelles Netzwerk, [az network vnet peering show](/cli/azure/network/vnet/peering) zum Anzeigen von Einstellungen für ein bestimmtes Peering und [az network vnet peering update](/cli/azure/network/vnet/peering) zum Ändern von Peeringeinstellungen|
- **PowerShell**: [Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering) zum Abrufen der Ansichtspeeringeinstellungen und [Set-AzVirtualNetworkPeering](/powershell/module/az.network/set-azvirtualnetworkpeering) zum Ändern der Einstellungen.

## <a name="delete-a-peering"></a>Löschen eines Peerings

Stellen Sie vor dem Löschen eines Peerings sicher, dass Ihr Konto über die [erforderlichen Berechtigungen](#permissions) verfügt.

Wenn ein Peering gelöscht wird, wird der Datenverkehr von einem virtuellen Netzwerk nicht mehr an das mittels Peering verknüpfte virtuelle Netzwerk weitergeleitet. Wenn bereitgestellte virtuelle Netzwerke mittels Peering über Resource Manager verbunden werden, weist jedes virtuelle Netzwerk ein Peering mit dem anderen virtuellen Netzwerk auf. Obwohl durch das Löschen des Peerings von einem virtuellen Netzwerk die Kommunikation zwischen den virtuellen Netzwerken deaktiviert wird, wird das Peering vom anderen virtuellen Netzwerk nicht gelöscht. Der Peeringstatus für das Peering, das im anderen virtuellen Netzwerk vorhanden ist, lautet **Getrennt**. Sie können das Peering erst neu erstellen, wenn Sie das Peering im ersten virtuellen Netzwerk neu erstellen, und der Peeringstatus bei beiden virtuellen Netzwerken zu *Verbunden* wechselt.

Wenn Sie möchten, dass virtuelle Netzwerke gelegentlich, jedoch nicht immer, kommunizieren sollen, können Sie die Einstellung **Zugriff auf virtuelles Netzwerk zulassen** auf **Deaktiviert** festlegen, statt das Peering zu löschen. Informationen hierzu finden Sie in Schritt 6 des Abschnitts „Erstellen eines Peerings“ dieses Artikels. Es kann ggf. einfacher sein, den Netzwerkzugriff zu deaktivieren und zu aktivieren, statt die Peerings zu löschen und neu zu erstellen.

1. Geben Sie oben im Portal *Virtuelle Netzwerke* in das Suchfeld ein. Wenn **Virtuelle Netzwerke** in den Suchergebnissen angezeigt wird, können Sie den Begriff auswählen. Aktivieren Sie nicht **Virtuelle Netzwerke (klassisch)** , wenn diese Option in der Liste angezeigt wird, da über das klassische Bereitstellungsmodell kein Peering von einem bereitgestellten virtuellen Netzwerk aus erstellt werden kann.
2. Wählen Sie das virtuelle Netzwerk in der Liste aus, für das Sie ein Peering löschen möchten.
3. Wählen Sie unter **EINSTELLUNGEN** die Option **Peerings** aus.
4. Wählen Sie auf der rechten Seite des zu löschenden Peerings die Optionen **...** , **Löschen** und **Ja**, um das Peering aus dem ersten virtuellen Netzwerk löschen.
5. Führen Sie die vorherigen Schritte durch, um das Peering aus dem anderen virtuellen Netzwerk im Peering zu löschen.

**Befehle**

- **Azure CLI**: [az network vnet peering delete](/cli/azure/network/vnet/peering)
- **PowerShell**: [Remove-AzVirtualNetworkPeering](/powershell/module/az.network/remove-azvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>Anforderungen und Einschränkungen

- <a name="cross-region"></a>Sie können virtuelle Netzwerke in derselben Region oder in verschiedenen Regionen per Peering verknüpfen. Das Peering von virtuellen Netzwerken in unterschiedlichen Regionen wird auch als *globales VNet-Peering* bezeichnet. 
- Wenn ein globales Peering erstellt wird, können sich die virtuellen Netzwerke in einer beliebigen Region einer öffentlichen Azure-Cloud, in China-Cloudregionen oder in Government Cloud-Regionen befinden. Peering ist nicht über die Grenzen von Clouds hinweg möglich. Beispielsweise kann für ein VNet in der öffentlichen Azure-Cloud kein Peering mit einem VNet in der Azure China-Cloud durchgeführt werden.
- Ressourcen in einem virtuellen Netzwerk können nicht mit der Front-End-IP-Adresse eines Load Balancers Standard in einem per globalem Peering verbundenen virtuellen Netzwerk kommunizieren. Unterstützung für Load Balancer Standard besteht nur innerhalb der gleichen Region. Unterstützung für Load Balancer Standard besteht für beide – VNet-Peering und globales VNet-Peering. Dienste, die einen Load Balancer im Tarif „Basic“ verwenden, die nicht über das globale VNET-Peering funktionieren, sind [hier](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) dokumentiert.
- Sie können Remotegateways verwenden oder Gatewaytransit in virtuellen Netzwerken mit globalem Peering und in virtuellen Netzwerken mit lokalem Peering zulassen.
- Die virtuellen Netzwerke können sich im gleichen Abonnement oder in verschiedenen Abonnements befinden. Wenn Sie eine Peerverbindung zwischen virtuellen Netzwerken in verschiedenen Abonnements herstellen, können beide Abonnements demselben oder einem anderen Azure Active Directory-Mandanten zugeordnet sein. Wenn Sie noch keinen AD-Mandanten besitzen, [erstellen Sie einen](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json-a-new-azure-ad-tenant).
- Die mittels Peering verknüpften virtuellen Netzwerke dürfen keine sich überschneidenden IP-Adressräume aufweisen.
- Sie können Adressbereiche zum Adressraum eines virtuellen Netzwerks hinzufügen oder aus diesem löschen, nachdem ein virtuelles Netzwerk per Peering mit einem anderen virtuellen Netzwerk verknüpft wurde. Um Adressbereiche hinzuzufügen oder zu entfernen, das Peering zu löschen oder Adressräume hinzuzufügen oder zu entfernen, erstellen Sie das Peering erneut. Informationen zum Hinzufügen oder Entfernen von Adressbereichen in virtuellen Netzwerken finden Sie unter [Verwalten virtueller Netzwerke](manage-virtual-network.md).
- Sie können zwei über Resource Manager bereitgestellte virtuelle Netzwerke oder ein über Resource Manager bereitgestelltes virtuelles Netzwerk per Peering mit einem virtuellen Netzwerk verknüpfen, das über das klassische Bereitstellungsmodell bereitgestellt wurde. Sie können nicht zwei virtuelle Netzwerke, die mit dem klassischen Bereitstellungsmodell erstellt wurden, mittels Peering miteinander verknüpfen. Wenn Sie mit den Azure-Bereitstellungsmodellen nicht vertraut sind, lesen Sie den Artikel [Grundlegendes zu Azure-Bereitstellungsmodellen](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Sie können ein [VPN Gateway](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) verwenden, um zwei virtuelle Netzwerke zu verbinden, die mit dem klassischen Bereitstellungsmodell erstellt wurden.
- Wenn zwei virtuelle Netzwerke, die mit dem Resource Manager erstellt wurden, mittels Peering miteinander verknüpft werden, muss für jedes virtuelle Netzwerk im Peering ein Peering konfiguriert werden. Für den Peeringstatus wird einer der folgenden Typen angezeigt: 
  - *Initiiert:* Wenn Sie das Peering vom ersten virtuellen Netzwerk mit dem zweiten virtuellen Netzwerk erstellen, lautet der Peeringstatus *Initiiert*. 
  - *:* Wenn Sie das Peering vom zweiten virtuellen Netzwerk mit dem ersten virtuellen Netzwerk erstellen, lautet der Peeringstatus *Verbunden*. Wenn Sie sich den Peeringstatus für das erste virtuelle Netzwerk ansehen, erkennen Sie, dass der Status von *Initiiert* zu *Verbunden* gewechselt ist. Das Peering wurde erst erfolgreich erstellt, wenn der Peeringstatus für beide Peerings virtueller Netzwerk *Verbunden* lautet.
- Wenn ein über Resource Manager erstelltes virtuelles Netzwerk mittels Peering mit einem über das klassische Bereitstellungsmodell erstellten virtuellen Netzwerk verbunden wird, konfigurieren Sie nur ein Peering für das virtuelle Netzwerk, das über Resource Manager bereitgestellt wurde. Es ist jedoch nicht möglich, ein Peering für ein virtuelles Netzwerk (klassisch) oder zwischen zwei virtuellen Netzwerken zu konfigurieren, die über das klassische Bereitstellungsmodell bereitgestellt wurden. Wenn Sie das virtuelle Netzwerk (Resource Manager) mittels Peering mit dem virtuellen Netzwerk (klassisch) verbinden, lautet der Peeringstatus *Wird aktualisiert* und wechselt anschließend gleich zu *Verbunden*.
- Ein Peering wird zwischen zwei virtuellen Netzwerken eingerichtet. Peerings an sich sind nicht transitiv. Wenn Sie Peerings zwischen Folgendem erstellen:
  - VirtualNetwork1 & VirtualNetwork2     - VirtualNetwork1 & VirtualNetwork2
  - VirtualNetwork2 & VirtualNetwork3     - VirtualNetwork2 & VirtualNetwork3


  Es erfolgt kein Peering zwischen VirtualNetwork1 und VirtualNetwork3 über VirtualNetwork2. Wenn Sie ein Peering zwischen virtuellen Netzwerken für VirtualNetwork1 und VirtualNetwork3 erstellen möchten, müssen Sie ein Peering zwischen VirtualNetwork1 und VirtualNetwork3 erstellen. Es erfolgt kein Peering zwischen VirtualNetwork1 und VirtualNetwork3 über VirtualNetwork2. Wenn Sie möchten, dass VirtualNetwork1 und VirtualNetwork3 direkt miteinander kommunizieren, müssen Sie ein explizites Peering zwischen VirtualNetwork1 und VirtualNetwork3 erstellen oder eine NVA im Hub-Netzwerk durchlaufen.  
- Über die standardmäßige Azure-Namensauflösung können keine Namen in virtuellen Netzwerken aufgelöst werden, die mittels Peering verbunden sind. Um Namen in anderen virtuellen Netzwerken aufzulösen, müssen Sie [Azure DNS für private Domänen](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) oder einen benutzerdefinierten DNS-Server verwenden. Weitere Informationen zum Einrichten Ihres DNS-Servers finden Sie unter [Namensauflösung mithilfe eines eigenen DNS-Servers](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
- Ressourcen in per Peering verbundenen virtuellen Netzwerken in derselben Region können mit derselben Bandbreite und Latenz miteinander kommunizieren, als ob sie sich im selben virtuellen Netzwerk befinden würden. Für die Größe jedes virtuellen Computers liegt jedoch eine eigene maximale Netzwerkbandbreite. Weitere Informationen zur Bandbreitenobergrenze für verschiedene VM-Größen finden Sie in den Artikeln zu den Größen virtueller Computer unter [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) oder [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Ein virtuelles Netzwerk kann mittels Peering mit einem anderen virtuellen Netzwerk verbunden werden; dies gilt auch für ein anderes virtuelles Netzwerk mit einem virtuellen Gateway für virtuelle Azure-Netzwerke. Wenn die virtuellen Netzwerke sowohl mittels Peering als auch mithilfe eines Gateways verbunden sind, wird der Datenverkehr zwischen den virtuellen Netzwerken über die Peeringkonfiguration statt über das Gateway geleitet.
- Um sicherzustellen, dass die neuen Routen auf den Client heruntergeladen werden, müssen die Point-to-Site-VPN-Clients nach der erfolgreichen Konfiguration des Peerings in virtuellen Netzwerken erneut heruntergeladen werden.
- Für ein- und ausgehenden Datenverkehr, der VNET-Peering verwendet, fällt eine Gebühr an. Weitere Informationen hierzu finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="permissions"></a>Berechtigungen

Die Konten, mit denen Sie beim Peering virtueller Netzwerke arbeiten, müssen den folgenden Rollen zugewiesen werden:

- [Netzwerkmitwirkender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor): Für ein über den Resource Manager bereitgestelltes virtuelles Netzwerk.
- [Klassischer Netzwerkmitwirkender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor): Für ein über das klassische Bereitstellungsmodell bereitgestelltes virtuelles Netzwerk.

Wenn Ihr Konto nicht einer der zuvor aufgeführten Rollen zugewiesen ist, müssen Sie es einer [benutzerdefinierten Rolle](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) zuweisen, die den erforderlichen Aktionen aus der folgenden Tabelle zugewiesen ist:

| Aktion                                                          | Name |
|---                                                              |---   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write  | Erforderlich, um ein Peering zwischen dem virtuellen Netzwerk A und dem virtuellen Netzwerk B herzustellen. Das virtuelle Netzwerk A muss ein virtuelles Netzwerk sein (Resource Manager).          |
| Microsoft.Network/virtualNetworks/peer/action                   | Erforderlich, um ein Peering zwischen dem virtuellen Netzwerk B (Resource Manager) und dem virtuellen Netzwerk A herzustellen.                                                       |
| Microsoft.ClassicNetwork/virtualNetworks/peer/action                   | Erforderlich, um ein Peering zwischen dem virtuellen Netzwerk B (klassisch) und dem virtuellen Netzwerk A herzustellen.                                                                |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read   | Lesen eines Peerings in virtuellen Netzwerken   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Löschen eines Peerings in virtuellen Netzwerken |

## <a name="next-steps"></a>Nächste Schritte

- Ein Peering für virtuelle Netzwerke wird zwischen virtuellen Netzwerken erstellt, die mit dem gleichen oder unterschiedlichen Bereitstellungsmodellen unter demselben oder unterschiedlichen Abonnements erstellt wurden. Arbeiten Sie ein Tutorial für eines der folgenden Szenarien durch:

  |Azure-Bereitstellungsmodell             | Subscription  |
  |---------                          |---------|
  |Beide mit Resource Manager              |[Gleich](tutorial-connect-virtual-networks-portal.md)|
  |                                   |[Unterschiedlich](create-peering-different-subscriptions.md)|
  |Einmal Resource Manager, einmal klassisch  |[Gleich](create-peering-different-deployment-models.md)|
  |                                   |[Unterschiedlich](create-peering-different-deployment-models-subscriptions.md)|

- Informieren Sie sich über das Erstellen einer [Hub-and-Spoke-Netzwerktopologie](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Erstellen eines Peering virtueller Netzwerke mithilfe von [PowerShell](powershell-samples.md)- oder [Azure CLI](cli-samples.md)-Beispielskripts oder mithilfe von Azure [Resource Manager-Vorlagen](template-samples.md)
- Erstellen und Zuweisen von [Azure Policy-Definitionen](policy-samples.md) für virtuelle Netzwerke
