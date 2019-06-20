---
title: Erstellen, Ändern oder Löschen eines virtuellen Azure-Netzwerks
titlesuffix: Azure Virtual Network
description: In diesem Artikel erfahren Sie, wie Sie ein virtuelles Netzwerk in Azure erstellen, ändern oder löschen.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: kumud
ms.openlocfilehash: 235a82c6bba4165790c370c2641ee6cd41f10840
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64700484"
---
# <a name="create-change-or-delete-a-virtual-network"></a>Erstellen, Ändern oder Löschen eines virtuellen Netzwerks

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In diesem Artikel erfahren Sie, wie Sie ein virtuelles Netzwerk erstellen und löschen und Einstellungen für vorhandene virtuelle Netzwerke wie DNS-Server und IP-Adressräume ändern. Wenn Sie mit virtuellen Netzwerken noch nicht vertraut sind, lesen Sie mehr darüber in der [Übersicht über virtuelle Netzwerke](virtual-networks-overview.md), oder arbeiten Sie ein [Tutorial](quick-create-portal.md) durch. Ein virtuelles Netzwerk enthält Subnetze. Informationen zum Erstellen, Ändern und Löschen von Subnetzen finden Sie unter [Hinzufügen, Ändern oder Löschen von Subnetzen virtueller Netzwerke](virtual-network-manage-subnet.md).

## <a name="before-you-begin"></a>Voraussetzungen

Führen Sie zuerst die folgenden Aufgaben aus, ehe Sie die Schritte in den Abschnitten dieses Artikels durchführen:

- Falls Sie noch nicht über ein Azure-Konto verfügen, können Sie sich für ein [kostenloses Testkonto](https://azure.microsoft.com/free) registrieren.
- Öffnen Sie bei Verwendung des Portals https://portal.azure.com, und melden Sie sich mit Ihrem Azure-Konto an.
- Wenn Sie PowerShell-Befehle zum Durchführen von Aufgaben in diesem Artikel verwenden, führen Sie die Befehle entweder in [Azure Cloud Shell](https://shell.azure.com/powershell) oder durch Ausführen von PowerShell auf Ihrem Computer aus. Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. Für dieses Tutorial ist das Azure PowerShell-Modul Version 1.0.0 oder höher erforderlich. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.
- Wenn Sie Befehle der Azure-Befehlszeilenschnittstelle (CLI) zum Durchführen von Aufgaben in diesem Artikel verwenden, führen Sie die Befehle entweder in [Azure Cloud Shell](https://shell.azure.com/bash) oder durch Ausführen der CLI auf Ihrem Computer aus. Für dieses Tutorial ist die Azure CLI-Version 2.0.31 oder höher erforderlich. Führen Sie `az --version` aus, um die installierte Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli). Wenn Sie die Azure CLI lokal ausführen, müssen Sie auch `az login` ausführen, um eine Verbindung mit Azure herzustellen.
- Das Konto, bei dem Sie sich anmelden oder das Sie zum Herstellen einer Verbindung mit Azure verwenden, muss der Rolle [Netzwerkmitwirkender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) oder einer [benutzerdefinierten Rolle](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) zugewiesen sein, der die entsprechenden, unter [Berechtigungen](#permissions) aufgeführten Aktionen zugewiesen wurden.

## <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

1. Wählen Sie **+ Ressource erstellen** > **Netzwerk** > **Virtuelles Netzwerk**.
2. Geben Sie Werte für folgende Einstellungen ein, oder wählen Sie sie aus, und wählen Sie dann **Erstellen**:
   - **Name**: Der Name muss in der ausgewählten [Ressourcengruppe](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), in der Sie das virtuelle Netzwerk erstellen möchten, eindeutig sein. Sie können den Namen nicht ändern, nachdem das virtuelle Netzwerk erstellt wurde. Sie können im Laufe der Zeit mehrere virtuelle Netzwerke erstellen. Benennungsvorschläge finden Sie unter [Benennungskonventionen](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions). Durch die Verwendung einer Benennungskonvention können Sie einfacher mehrere virtuelle Netzwerke verwalten.
   - **Adressraum:** Der Adressraum für ein virtuelles Netzwerk besteht aus einem oder mehreren Adressbereichen, die sich nicht überschneiden und in der CIDR-Notation angegeben sind. Die von Ihnen definierten Adressbereiche können öffentlich oder privat (RFC 1918) sein. Ganz gleich, ob Sie den Adressbereich als öffentlich oder privat definieren – der Adressbereich ist nur innerhalb des virtuellen Netzwerks, über miteinander verbundene virtuelle Netzwerke und über beliebige lokale Netzwerke erreichbar, die Sie mit dem virtuellen Netzwerk verbunden haben. Sie können die folgenden Adressbereiche nicht hinzufügen:
     - 224.0.0.0/4 (Multicast)
     - 255.255.255.255/32 (Übertragung)
     - 127.0.0.0/8 (Loopback)
     - 169.254.0.0/16 (verbindungslokal)
     - 168.63.129.16/32 ([Integritätstest](../load-balancer/load-balancer-custom-probe-overview.md#probesource) für internes DNS, DHCP und Azure Load Balancer)

     Sie können beim Erstellen des virtuellen Netzwerks zwar nur einen Adressbereich definieren, nach der Erstellung des virtuellen Netzwerks dem Adressraum jedoch weitere Adressbereiche hinzufügen. Informationen zum Hinzufügen eines Adressbereichs zu einem vorhandenen virtuellen Netzwerk finden Sie unter [Hinzufügen oder Entfernen eines Adressbereichs](#add-or-remove-an-address-range).

     >[!WARNING]
     >Wenn ein virtuelles Netzwerk Adressbereiche aufweist, die sich mit denen eines anderen virtuellen Netzwerks oder eines lokalen Netzwerks überlappen, können die beiden Netzwerke nicht verbunden werden. Bevor Sie einen Adressbereich definieren, sollten Sie das virtuelle Netzwerk zukünftig eventuell mit anderen virtuellen Netzwerken oder lokalen Netzwerken verbinden.
     >
     >

     - **Subnetzname**: Der Subnetzname muss innerhalb des virtuellen Netzwerks eindeutig sein. Sie können den Subnetznamen nicht ändern, nachdem das Subnetz erstellt wurde. Das Portal erfordert, dass Sie bei der Erstellung eines virtuellen Netzwerks ein Subnetz definieren, obwohl ein virtuelles Netzwerk nicht unbedingt Subnetze umfassen muss. Im Portal können Sie nur ein Subnetz definieren, wenn Sie ein virtuelles Netzwerk erstellen. Sie können später weitere Subnetze zum virtuellen Netzwerk hinzufügen, nachdem das virtuelle Netzwerk erstellt wurde. Informationen zum Hinzufügen eines Subnetzes zu einem virtuellen Netzwerk finden Sie unter [Verwalten von Subnetzen](virtual-network-manage-subnet.md). Sie können über die Azure CLI oder über PowerShell ein virtuelles Netzwerk mit mehreren Subnetzen erstellen.

       >[!TIP]
       >Gelegentlich erstellen Administratoren unterschiedliche Subnetze, um das Datenverkehrsrouting zwischen ihnen zu filtern oder zu steuern. Erwägen Sie vor dem Definieren von Subnetzen, wie Sie Datenverkehr zwischen Ihren Subnetzen filtern und weiterleiten möchten. Weitere Informationen zum Filtern von Datenverkehr zwischen Subnetzen finden Sie unter [Netzwerksicherheitsgruppen](security-overview.md). Azure leitet Datenverkehr zwischen Subnetzen automatisch weiter, doch Sie können diese Standardrouten von Azure außer Kraft setzen. Weitere Informationen zum standardmäßigen Datenverkehrsrouting bei Azure-Subnetzen finden Sie in der [Übersicht über das Routing](virtual-networks-udr-overview.md).
       >

     - **Subnetzadressbereich:** Der Bereich muss sich im Adressraum befinden, den Sie für das virtuelle Netzwerk eingegeben haben. Der kleinste Bereich, den Sie angeben können, ist /29, der acht IP-Adressen für das Subnetz bereitstellt. Azure reserviert die erste und letzte Adresse in jedem Subnetz aus Gründen der Protokollkonformität. Drei weitere Adressen sind für die Nutzung durch Azure-Dienste reserviert. Folglich weist ein virtuelles Netzwerk mit dem Subnetzadressbereich „/29“ drei verwendbare IP-Adressen auf. Wenn Sie ein virtuelles Netzwerk mit einem VPN-Gateway verbinden möchten, müssen Sie ein Gatewaysubnetz erstellen. Weitere Informationen finden Sie unter [Informationen zu VPN Gateway-Einstellungen](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) im Abschnitt „Gatewaysubnetz“. Unter bestimmten Bedingungen können Sie den Adressbereich ändern, nachdem das Subnetz erstellt wurde. Informationen zum Ändern des Adressbereichs eines Subnetzes finden Sie unter [Verwalten von Subnetzen](virtual-network-manage-subnet.md).
     - **Abonnement**: Wählen Sie ein [Abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) aus. Sie können nicht dasselbe virtuelle Netzwerk in mehreren Azure-Abonnements verwenden. Allerdings können Sie ein virtuelles Netzwerk in einem Abonnement durch [Peering in virtuellen Netzwerken](virtual-network-peering-overview.md) mit virtuellen Netzwerken in anderen Abonnements verbinden. Alle Azure-Ressource, die Sie mit dem virtuellen Netzwerk verbinden, müssen sich im selben Abonnement wie das des virtuellen Netzwerks befinden.
     - **Ressourcengruppe**: Wählen Sie eine vorhandene [Ressourcengruppe](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) aus, oder erstellen Sie eine neue. Azure-Ressourcen, die Sie mit dem virtuellen Netzwerk verbinden, können sich in derselben Ressourcengruppe wie die des virtuellen Netzwerks oder in einer anderen Ressourcengruppe befinden.
     - **Standort**: Wählen Sie einen Azure-[Standort](https://azure.microsoft.com/regions/) (auch als „Region“ bezeichnet) aus. Ein virtuelles Netzwerk kann sich nur an einem Azure-Standort befinden. Allerdings können Sie ein virtuelles Netzwerk an einem Standort mithilfe eines VPN-Gateways mit einem virtuellen Netzwerk an einem anderen Standort verbinden. Alle Azure-Ressource, die Sie mit dem virtuellen Netzwerk verbinden, müssen sich am selben Standort wie dem des virtuellen Netzwerks befinden.

**Befehle**

- Azure CLI: [az network vnet create](/cli/azure/network/vnet)
- PowerShell: [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)

## <a name="view-virtual-networks-and-settings"></a>Anzeigen von virtuellen Netzwerken und Einstellungen

1. Geben Sie oben im Portal *Virtuelle Netzwerke* in das Suchfeld ein. Wenn **Virtuelle Netzwerke** in den Suchergebnissen angezeigt wird, können Sie den Begriff auswählen.
2. Wählen Sie in der Liste der virtuellen Netzwerke das virtuelle Netzwerk aus, für das Sie Einstellungen anzeigen möchten.
3. Für das ausgewählte virtuelle Netzwerk sind folgende Einstellungen aufgeführt:
   - **Übersicht**: Enthält Informationen zum virtuellen Netzwerk, z.B. zum Adressraum und den DNS-Servern. Der folgende Screenshot zeigt die Übersicht über die Einstellungen eines virtuellen Netzwerks mit dem Namen **MyVNet** an:

     ![Übersicht über die Netzwerkschnittstelle](./media/manage-virtual-network/vnet-overview.png)

     Sie können ein virtuelles Netzwerk in ein anderes Abonnement oder eine andere Ressourcengruppe verschieben, indem Sie neben dem Namen der **Ressourcengruppe** oder des **Abonnements** auf **Ändern** klicken. Informationen zum Verschieben eines virtuellen Netzwerks finden Sie unter [Verschieben von Ressourcen in eine andere Ressourcengruppe oder ein anderes Abonnement](../azure-resource-manager/resource-group-move-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json). In diesem Artikel erfahren Sie, welche Voraussetzungen erfüllt sein müssen und wie Sie Ressourcen über das Azure-Portal, mithilfe von PowerShell oder unter Verwendung der Azure CLI verschieben. Alle Ressourcen, die mit dem virtuellen Netzwerk verbunden sind, müssen mit dem virtuellen Netzwerk verschoben werden.
   - **Adressraum:** Die dem virtuellen Netzwerk zugewiesenen Adressräume werden aufgelistet. Die erforderlichen Schritte zum Hinzufügen und Entfernen eines Adressbereichs für den Adressraum werden im Abschnitt [Hinzufügen oder Entfernen eines Adressbereichs](#add-or-remove-an-address-range) erläutert.
   - **Verbundene Geräte**: Alle Ressourcen, die mit dem virtuellen Netzwerk verbunden sind, werden aufgeführt. Im vorhergehenden Screenshot werden drei Netzwerkschnittstellen und ein Load Balancer gezeigt, die mit dem virtuellen Netzwerk verbunden sind. Neue Ressourcen, die Sie erstellen und mit dem virtuellen Netzwerk verbinden, werden aufgeführt. Wenn Sie eine Ressource löschen, die mit dem virtuellen Netzwerk verbunden war, wird sie nicht mehr in der Liste angezeigt.
   - **Subnetze**: Eine Liste von Subnetzen im virtuellen Netzwerk wird angezeigt. Informationen zum Hinzufügen und Entfernen eines Subnetzes finden Sie unter [Verwalten von Subnetzen](virtual-network-manage-subnet.md).
   - **DNS-Server**: Sie können angeben, ob der interne DNS-Server von Azure oder ein benutzerdefinierter DNS-Server die Namensauflösung für Geräte bereitstellt, die mit dem virtuellen Netzwerk verbunden sind. Wenn Sie ein virtuelles Netzwerk über das Azure-Portal erstellen, werden standardmäßig die DNS-Server von Azure für die Namensauflösung in einem virtuellen Netzwerk verwendet. Führen Sie zum Ändern der DNS-Server die Schritte im Abschnitt [Ändern von DNS-Server](#change-dns-servers) in diesem Artikel durch.
   - **Peerings**: Wenn Peerings im Abonnement vorhanden sind, werden sie hier aufgeführt. Sie können die Einstellungen für vorhandene Peerings anzeigen oder Peerings erstellen, ändern oder löschen. Weitere Informationen zu Peerings finden Sie im Artikel [Peering virtueller Netzwerke](virtual-network-peering-overview.md).
   - **Properties:** Zeigt Einstellungen des virtuellen Netzwerks an, wie z.B. seine Ressourcen-ID und das zugehörige Azure-Abonnement.
   - **Diagramm**: Das Diagramm bietet eine visuelle Darstellung aller Geräte, die mit dem virtuellen Netzwerk verbunden sind. Das Diagramm enthält einige wichtige Informationen zu den Geräten. Zum Verwalten eines Geräts in dieser Ansicht wählen Sie es im Diagramm aus.
   - **Allgemeine Azure-Einstellungen**: Weitere Informationen zu allgemeinen Azure-Einstellungen finden Sie in den folgenden Artikeln:
     - [Aktivitätsprotokoll](../azure-monitor/platform/activity-logs-overview.md)
     - [Zugriffssteuerung (IAM)](../role-based-access-control/overview.md)
     - [Tags](../azure-resource-manager/resource-group-using-tags.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Sperren](../azure-resource-manager/resource-group-lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Automatisierungsskript](../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates)

**Befehle**

- Azure CLI: [az network vnet show](/cli/azure/network/vnet)
- PowerShell: [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)

## <a name="add-or-remove-an-address-range"></a>Hinzufügen oder Entfernen eines Adressbereichs

Sie können Adressbereiche eines virtuellen Netzwerks hinzufügen und entfernen. Ein Adressbereich muss in der CIDR-Notation angegeben werden und darf sich innerhalb desselben virtuellen Netzwerks nicht mit anderen Adressbereichen überlappen. Die von Ihnen definierten Adressbereiche können öffentlich oder privat (RFC 1918) sein. Ganz gleich, ob Sie den Adressbereich als öffentlich oder privat definieren – der Adressbereich ist nur innerhalb des virtuellen Netzwerks, über miteinander verbundene virtuelle Netzwerke und über beliebige lokale Netzwerke erreichbar, die Sie mit dem virtuellen Netzwerk verbunden haben. 

Sie können den Adressbereich für ein virtuelles Netzwerk verringern, wenn Sie ihm keine Subnetze zugeordnet haben. Andernfalls können Sie nur den Adressbereich erweitern, z.B. „/16“ in „/8“ ändern. Sie könnten mit einem kleinen Adressbereich beginnen und ihn dann später erweitern oder weitere Adressbereiche hinzufügen.

<!-- the last two sentences above are added per GitHub issue https://github.com/MicrosoftDocs/azure-docs/issues/20572 -->

Sie können die folgenden Adressbereiche nicht hinzufügen:

- 224.0.0.0/4 (Multicast)
- 255.255.255.255/32 (Übertragung)
- 127.0.0.0/8 (Loopback)
- 169.254.0.0/16 (verbindungslokal)
- 168.63.129.16/32 ([Integritätstest](../load-balancer/load-balancer-custom-probe-overview.md#probesource) für internes DNS, DHCP und Azure Load Balancer)

Hinzufügen oder Entfernen eines Adressbereichs:

1. Geben Sie oben im Portal *Virtuelle Netzwerke* in das Suchfeld ein. Wenn **Virtuelle Netzwerke** in den Suchergebnissen angezeigt wird, können Sie den Begriff auswählen.
2. Wählen Sie in der Liste der virtuellen Netzwerke das virtuelle Netzwerk aus, für das Sie einen Adressbereich hinzufügen oder entfernen möchten.
3. Wählen Sie unter **EINSTELLUNGEN** die Option **Adressraum** aus.
4. Führen Sie eine der folgenden Optionen aus:
    - **IP-Adressbereich hinzufügen**: Geben Sie den neuen Adressbereich ein. Der Adressbereich darf sich nicht mit einem vorhandenen Adressbereich überlappen, der für das virtuelle Netzwerk definiert ist.
    - **Adressbereich entfernen**: Wählen Sie rechts neben dem zu entfernenden Adressbereich **...** und dann **Entfernen** aus. Wenn ein Subnetz im Adressbereich vorhanden ist, können Sie den Adressbereich nicht entfernen. Zum Entfernen eines Adressbereichs müssen Sie zunächst alle Subnetze (sowie alle Ressourcen in diesen Subnetzen) löschen, die in diesem Adressbereich vorhanden sind.
5. Wählen Sie **Speichern** aus.

**Befehle**

- Azure CLI: [az network vnet update](/cli/azure/network/vnet)
- PowerShell: [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="change-dns-servers"></a>Ändern von DNS-Servern

Alle mit dem virtuellen Netzwerk verbundenen VMs werden bei den DNS-Servern registriert, die Sie für das virtuelle Netzwerk angeben. Zudem wird der angegebene DNS-Server für die Namensauflösung verwendet. Jede Netzwerkschnittstellenkarte (NIC) einer VM hat eigene DNS-Servereinstellungen. Wenn eine Netzwerkkarte mit eigenen DNS-Servereinstellungen versehen ist, überschreiben diese die DNS-Servereinstellungen für das virtuelle Netzwerk. Weitere Informationen zu DNS-Einstellungen von Netzwerkkarten finden Sie unter [Aufgaben und Einstellungen für Netzwerkschnittstellen](virtual-network-network-interface.md#change-dns-servers). Weitere Informationen zur Namensauflösung für VMs und zu Rolleninstanzen in Azure Cloud Services finden Sie unter [Namensauflösung für VMs und Rolleninstanzen](virtual-networks-name-resolution-for-vms-and-role-instances.md). So fügen Sie einen DNS-Server hinzu oder ändern bzw. entfernen diesen:

1. Geben Sie oben im Portal *Virtuelle Netzwerke* in das Suchfeld ein. Wenn **Virtuelle Netzwerke** in den Suchergebnissen angezeigt wird, können Sie den Begriff auswählen.
2. Wählen Sie in der Liste der virtuellen Netzwerke das virtuelle Netzwerk aus, für das Sie DNS-Server ändern möchten.
3. Wählen Sie unter **EINSTELLUNGEN** die Option **DNS-Server** aus.
4. Wählen Sie eine der folgenden Optionen:
   - **Standard (von Azure bereitgestellt)** : Alle Ressourcennamen und privaten IP-Adressen werden automatisch bei den Azure DNS-Servern registriert. Sie können Namen zwischen allen Ressourcen auflösen, die im selben virtuellen Netzwerk miteinander verbunden sind. Diese Option eignet sich nicht zum Auflösen von Namen über virtuelle Netzwerke hinweg. Hierfür müssen Sie einen benutzerdefinierten DNS-Server verwenden.
   - **Benutzerdefiniert**: Sie können einen oder mehrere Server bis zum Azure-Grenzwert für ein virtuelles Netzwerk hinzufügen. Weitere Informationen zu Einschränkungen für DNS-Server finden Sie unter [Azure-Grenzwerte](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). Folgende Optionen stehen zur Auswahl:
   - **Adresse hinzufügen**: Hierüber können Sie den Server der DNS-Serverliste Ihres virtuellen Netzwerks hinzufügen. Durch diese Option wird der DNS-Server auch bei Azure registriert. Wenn Sie bereits einen DNS-Server bei Azure registriert haben, können Sie ihn in der Liste auswählen.
   - **Adresse entfernen**: Wählen Sie neben dem Server, den Sie entfernen möchten, **...** und dann **Entfernen** aus. Durch das Löschen des Servers wird dieser nur aus der Liste dieses virtuellen Netzwerks entfernt. Der DNS-Server bleibt in Azure registriert und kann von Ihren anderen virtuellen Netzwerken verwendet werden.
   - **Neuanordnen von DNS-Serveradressen**: Sie müssen darauf achten, dass Sie Ihre DNS-Server in der richtigen Reihenfolge für Ihre Umgebung auflisten. DNS-Serverlisten werden in der Reihenfolge verwendet, in der sie angegeben sind. Sie können nicht als Roundrobinsetup verwendet. Wenn der erste DNS-Server in der Liste erreicht werden kann, verwendet der Client diesen DNS-Server unabhängig davon, ob der DNS-Server ordnungsgemäß funktioniert. Entfernen Sie alle aufgeführten DNS-Server, und fügen Sie sie in der gewünschten Reihenfolge wieder hinzu.
   - **Adresse ändern**: Markieren Sie den DNS-Server in der Liste, und geben Sie die neue Adresse ein.
5. Wählen Sie **Speichern** aus.
6. Starten Sie die mit dem virtuellen Netzwerk verbundenen VMs, damit ihnen die neuen DNS-Servereinstellungen zugewiesen werden. VMs nutzen ihre aktuellen DNS-Einstellungen weiter, bis sie neu gestartet werden.

**Befehle**

- Azure CLI: [az network vnet update](/cli/azure/network/vnet)
- PowerShell: [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="delete-a-virtual-network"></a>Löschen eines virtuellen Netzwerks

Sie können ein virtuelles Netzwerk erst löschen, wenn keine Ressourcen mehr damit verbunden sind. Falls Ressourcen mit den Subnetzen im virtuellen Netzwerk verbunden sind, müssen Sie zuerst die Ressourcen, die mit allen Subnetzen im virtuellen Netzwerk verbunden sind, löschen. Die Schritte zum Löschen einer Ressource variieren abhängig von der Ressource. Informationen zum Löschen von mit Subnetzen verbundenen Ressourcen finden Sie in der Dokumentation zum jeweiligen Ressourcentyp, den Sie löschen möchten. So löschen Sie ein virtuelles Netzwerk:

1. Geben Sie oben im Portal *Virtuelle Netzwerke* in das Suchfeld ein. Wenn **Virtuelle Netzwerke** in den Suchergebnissen angezeigt wird, können Sie den Begriff auswählen.
2. Wählen Sie in der Liste der virtuellen Netzwerke das virtuelle Netzwerk aus, das Sie löschen möchten.
3. Wählen Sie unter **EINSTELLUNGEN** die Option **Verbundene Geräte** aus, um sicherzustellen, dass keine Geräte mit dem virtuellen Netzwerk verbunden sind. Wenn verbundene Geräte vorhanden sind, müssen Sie diese löschen, ehe Sie das virtuelle Netzwerk löschen können. Wenn keine verbundenen Geräte vorhanden sind, wählen Sie **Übersicht**.
4. Klicken Sie auf **Löschen**.
5. Wählen Sie **Ja**, um das Löschen des virtuellen Netzwerks zu bestätigen.

**Befehle**

- Azure CLI: [azure network vnet delete](/cli/azure/network/vnet)
- PowerShell: [Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork)

## <a name="permissions"></a>Berechtigungen

Zur Durchführung von Aufgaben für virtuelle Netzwerke muss Ihr Konto der Rolle [Netzwerkmitwirkender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) oder einer [benutzerdefinierten](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Rolle zugewiesen sein, der die passenden Aktionen aus der folgenden Tabelle zugewiesen sind:

| Aktion                                  |   NAME                                |
|---------------------------------------- |   --------------------------------    |
|Microsoft.Network/virtualNetworks/read   |   Lesen eines virtuellen Netzwerks              |
|Microsoft.Network/virtualNetworks/write  |   Erstellen oder Aktualisieren eines virtuellen Netzwerks  |
|Microsoft.Network/virtualNetworks/delete |   Löschen eines virtuellen Netzwerks            |

## <a name="next-steps"></a>Nächste Schritte

- Erstellen eines virtuellen Netzwerks mithilfe von Beispielskripts für [PowerShell](powershell-samples.md) oder für die [Azure CLI](cli-samples.md) oder mithilfe von [Azure Resource Manager-Vorlagen](template-samples.md)
- Erstellen und Anwenden einer [Azure-Richtlinie](policy-samples.md) für virtuelle Netzwerke
