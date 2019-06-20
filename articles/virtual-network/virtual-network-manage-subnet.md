---
title: Hinzufügen, Ändern oder Löschen eines virtuellen Azure-Subnetzes
titlesuffix: Azure Virtual Network
description: Erfahren Sie, wie Sie Subnetze virtueller Netzwerke in Azure hinzufügen, ändern oder löschen.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: kumud
ms.openlocfilehash: 8a8feb0f12fbf5eadcddf239ff2e13c058a566fe
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64693298"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Hinzufügen, Ändern oder Löschen von Subnetzen virtueller Netzwerke

Erfahren Sie, wie Sie Subnetze virtueller Netzwerke hinzufügen, ändern oder löschen. Alle in einem virtuellen Netzwerk bereitgestellten Azure-Ressourcen werden in einem Subnetz innerhalb eines virtuellen Netzwerks bereitgestellt. Wenn Sie mit virtuellen Netzwerken noch nicht vertraut sind, lesen Sie mehr darüber in der [Übersicht über virtuelle Netzwerke](virtual-networks-overview.md), oder arbeiten Sie ein [Tutorial](quick-create-portal.md) durch. Informationen darüber, wie Sie ein virtuelles Netzwerk erstellen, ändern oder löschen, finden Sie unter [Erstellen, Ändern oder Löschen eines virtuellen Netzwerks](manage-virtual-network.md).

## <a name="before-you-begin"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Führen Sie zuerst die folgenden Aufgaben aus, ehe Sie die Schritte in den Abschnitten dieses Artikels durchführen:

- Falls Sie noch nicht über ein Azure-Konto verfügen, können Sie sich für ein [kostenloses Testkonto](https://azure.microsoft.com/free) registrieren.
- Öffnen Sie bei Verwendung des Portals https://portal.azure.com, und melden Sie sich mit Ihrem Azure-Konto an.
- Wenn Sie PowerShell-Befehle zum Durchführen von Aufgaben in diesem Artikel verwenden, führen Sie die Befehle entweder in [Azure Cloud Shell](https://shell.azure.com/powershell) oder durch Ausführen von PowerShell auf Ihrem Computer aus. Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. Für dieses Tutorial ist das Azure PowerShell-Modul Version 1.0.0 oder höher erforderlich. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.
- Wenn Sie Befehle der Azure-Befehlszeilenschnittstelle (CLI) zum Durchführen von Aufgaben in diesem Artikel verwenden, führen Sie die Befehle entweder in [Azure Cloud Shell](https://shell.azure.com/bash) oder durch Ausführen der CLI auf Ihrem Computer aus. Für dieses Tutorial ist die Azure CLI-Version 2.0.31 oder höher erforderlich. Führen Sie `az --version` aus, um die installierte Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli). Wenn Sie die Azure CLI lokal ausführen, müssen Sie auch `az login` ausführen, um eine Verbindung mit Azure herzustellen.

Das Konto, bei dem Sie sich anmelden oder das Sie zum Herstellen einer Verbindung mit Azure verwenden, muss der Rolle [Netzwerkmitwirkender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) oder einer [benutzerdefinierten Rolle](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) zugewiesen sein, der die entsprechenden, unter [Berechtigungen](#permissions) aufgeführten Aktionen zugewiesen wurden.

## <a name="add-a-subnet"></a>Hinzufügen eines Subnetzes

1. Geben Sie oben im Portal *Virtuelle Netzwerke* in das Suchfeld ein. Wenn **Virtuelle Netzwerke** in den Suchergebnissen angezeigt wird, können Sie den Begriff auswählen.
2. Wählen Sie in der Liste der virtuellen Netzwerke das virtuelle Netzwerk aus, dem Sie ein Subnetz hinzufügen möchten.
3. Wählen Sie unter **EINSTELLUNGEN** die Option **Subnetze** aus.
4. Wählen Sie **+ Subnetz** aus.
5. Geben Sie Werte für die folgenden Parameter ein:
   - **Name**: Der Name muss innerhalb des virtuellen Netzwerks eindeutig sein. Für die maximale Kompatibilität mit anderen Azure-Diensten wird empfohlen, einen Buchstaben als erstes Zeichen des Namens zu verwenden. Beispielsweise wird Azure Application Gateway nicht in einem Subnetz bereitgestellt, dessen Name mit einer Zahl beginnt.
   - **Adressbereich**: Der Bereich muss innerhalb des Adressraums für das virtuelle Netzwerk eindeutig sein. Der Bereich darf keine Überschneidungen mit anderen Subnetzadressbereichen innerhalb des virtuellen Netzwerks aufweisen. Der Adressraum muss mithilfe der CIDR-Notation (Classless Inter-Domain Routing, klassenloses domänenübergreifendes Routing) angegeben werden. In einem virtuellen Netzwerk mit dem Adressraum 10.0.0.0/16 können Sie beispielsweise für ein Subnetz den Adressraum 10.0.0.0/24 definieren. Der kleinste Bereich, den Sie angeben können, ist /29, der acht IP-Adressen für das Subnetz bereitstellt. Azure reserviert die erste und letzte Adresse in jedem Subnetz aus Gründen der Protokollkonformität. Drei weitere Adressen sind für die Nutzung durch Azure-Dienste reserviert. Demzufolge führt das Einrichten eines Subnetzes mit dem Adressbereich /29 zu drei nutzbaren IP-Adressen im Subnetz. Wenn Sie ein virtuelles Netzwerk mit einem VPN-Gateway verbinden möchten, müssen Sie ein Gatewaysubnetz erstellen. Weitere Informationen finden Sie unter [Informationen zu VPN Gateway-Einstellungen](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) im Abschnitt „Gatewaysubnetz“. Unter bestimmten Bedingungen können Sie den Adressbereich ändern, nachdem das Subnetz hinzugefügt wurde. Informationen zum Ändern des Adressbereichs eines Subnetzes finden Sie im Abschnitt [Ändern der Subnetzeinstellungen](#change-subnet-settings).
   - **Netzwerksicherheitsgruppe**: Sie können einem Subnetz keine oder eine vorhandene Netzwerksicherheitsgruppe zuordnen, um ein- und ausgehenden Netzwerkdatenverkehr für das Subnetz zu filtern. Die Netzwerksicherheitsgruppe muss demselben Abonnement und Standort wie das virtuelle Netzwerk angehören. Erfahren Sie mehr über [Netzwerksicherheitsgruppen](security-overview.md) und das [Erstellen einer Netzwerksicherheitsgruppe](tutorial-filter-network-traffic.md).
   - **Routingtabelle**: Sie können einem Subnetz keine oder eine vorhandene Routingtabelle zuordnen, um die Weiterleitung von Netzwerkdatenverkehr an andere Netzwerke zu steuern. Die Routingtabelle muss demselben Abonnement und Standort wie das virtuelle Netzwerk angehören. Erfahren Sie mehr über [Azure-Routing](virtual-networks-udr-overview.md) und das [Erstellen einer Routingtabelle](tutorial-create-route-table-portal.md).
   - **Dienstendpunkte**: Für ein Subnetz können keine oder mehrere Dienstendpunkte aktiviert sein. Zum Aktivieren eines Dienstendpunkts für einen Dienst wählen Sie Dienste, für die Sie Dienstendpunkte aktivieren möchten, in der Liste **Dienste** aus. Der Standort wird für einen Endpunkt automatisch konfiguriert. Standardmäßig werden Dienstendpunkte für die Region des virtuellen Netzwerks konfiguriert. Damit Azure Storage Szenarien für ein regionales Failover unterstützt, werden Endpunkte automatisch für [Azure-Regionspaare](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions) konfiguriert.
   - **Subnetzdelegierung:** Für ein Subnetz können keine oder mehrere Delegierungen aktiviert sein. Bei der Subnetzdelegierung erhält der Dienst explizite Berechtigungen, um dienstspezifische Ressourcen im Subnetz zu erstellen, indem beim Bereitstellen des Diensts ein eindeutiger Bezeichner verwendet wird. Wählen Sie in der Liste **Dienste** den Dienst aus, der als Ziel für die Delegierung fungieren soll.

       Zum Entfernen eines Dienstendpunkts heben Sie die Auswahl des Diensts auf, für den der Dienstendpunkt entfernt werden soll. Weitere Informationen zu Dienstendpunkten und den Diensten, für die sie aktiviert werden können, finden Sie in der Übersicht über [VNET-Dienstendpunkte](virtual-network-service-endpoints-overview.md). Nach dem Aktivieren eines Dienstendpunkts für einen Dienst müssen Sie auch Netzwerkzugriff für das Subnetz für eine mit dem Dienst erstellte Ressource aktivieren. Beispiel: Wenn Sie den Dienstendpunkt für *Microsoft.Storage* aktivieren, müssen Sie auch Netzwerkzugriff auf alle Azure Storage-Konten aktivieren, auf die Netzwerkzugriff gewährt werden soll. Ausführliche Informationen zum Aktivieren des Netzwerkzugriffs auf Subnetze, für die ein Dienstendpunkt aktiviert ist, finden Sie in der Dokumentation zum jeweiligen Dienst, für den Sie den Dienstendpunkt aktiviert haben.

     Um zu überprüfen, ob ein Dienstendpunkt für ein Subnetz aktiviert wurde, zeigen Sie die [effektiven Routen](diagnose-network-routing-problem.md) für eine Netzwerkschnittstelle im Subnetz an. Wenn der Endpunkt konfiguriert ist, wird eine *Standardroute* mit den Adresspräfixen des Diensts angezeigt, und für „nextHopType“ ist **VirtualNetworkServiceEndpoint** angegeben. Weitere Informationen zum Routing finden Sie unter [Routing von Datenverkehr für virtuelle Netzwerke](virtual-networks-udr-overview.md).
6. Um das Subnetz dem ausgewählten virtuellen Netzwerk hinzuzufügen, wählen Sie **OK**.

**Befehle**

- Azure CLI: [az network vnet subnet create](/cli/azure/network/vnet/subnet)
- PowerShell: [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig)

## <a name="change-subnet-settings"></a>Ändern von Subnetzeinstellungen

1. Geben Sie oben im Portal *Virtuelle Netzwerke* in das Suchfeld ein. Wenn **Virtuelle Netzwerke** in den Suchergebnissen angezeigt wird, können Sie den Begriff auswählen.
2. Wählen Sie in der Liste der virtuellen Netzwerke das virtuelle Netzwerk mit dem Subnetz aus, für das Sie Einstellungen ändern möchten.
3. Wählen Sie unter **EINSTELLUNGEN** die Option **Subnetze** aus.
4. Wählen Sie in der Liste der Subnetze das Subnetz aus, für das Sie Einstellungen ändern möchten. Sie können die folgenden Einstellungen ändern:

    - **Adressbereich**: Wenn innerhalb des Subnetzes keine Ressourcen bereitgestellt sind, können Sie den Adressbereich ändern. Wenn Ressourcen im Subnetz vorhanden sind, müssen Sie die Ressourcen zuerst entweder in ein anderes Subnetz verschieben oder aus dem Subnetz löschen. Die Schritte zum Verschieben oder Löschen einer Ressource variieren abhängig von der Ressource. Informationen zum Verschieben oder Löschen von Ressourcen in Subnetzen finden Sie in der Dokumentation zum jeweiligen Ressourcentyp, den Sie verschieben oder löschen möchten. Die Einschränkungen für den **Adressbereich** finden Sie in Schritt 5 von [Hinzufügen eines Subnetzes](#add-a-subnet).
    - **Benutzer**: Sie können den Zugriff auf das Subnetz über integrierte Rollen oder eigene benutzerdefinierte Rollen steuern. Weitere Informationen zum Zuweisen von Rollen und Benutzern für den Zugriff auf das Subnetz finden Sie unter [Verwenden von Rollenzuweisungen zum Verwalten des Zugriffs auf Ihre Azure-Ressourcen](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment).
    - **Netzwerksicherheitsgruppe** und **Routingtabelle**: Siehe Schritt 5 unter [Hinzufügen eines Subnetzes](#add-a-subnet).
    - **Dienstendpunkte**: Siehe Schritt 5 im Abschnitt „Dienstendpunkte“ unter [Hinzufügen eines Subnetzes](#add-a-subnet). Wenn Sie einen Dienstendpunkt für ein vorhandenes Subnetz aktivieren, stellen Sie sicher, dass auf keiner Ressource im Subnetz kritische Tasks ausgeführt werden. Dienstendpunkte ändern die Routen in jeder Netzwerkschnittstelle im Subnetz von der Standardroute mit dem Adresspräfix *0.0.0.0/0* und *Internet* als Typ des nächsten Hops zu einer neuen Route mit dem Adresspräfix des Diensts und *VirtualNetworkServiceEndpoint* als Typ des nächsten Hops. Während dieses Wechsels werden alle offenen TCP-Verbindungen geschlossen. Der Dienstendpunkt wird erst aktiviert, wenn der gesamte Datenverkehr an den Dienst in allen Netzwerkschnittstellen mit der neuen Route aktualisiert wurde. Weitere Informationen zum Routing finden Sie unter [Routing von Datenverkehr für virtuelle Netzwerke](virtual-networks-udr-overview.md).
    - **Subnetzdelegierung:** Siehe Schritt 5 im Abschnitt „Dienstendpunkte“ unter [Hinzufügen eines Subnetzes](#add-a-subnet). Die Subnetzdelegierung kann so geändert werden, dass dafür null oder mehr Delegierungen aktiviert sind. Wenn im Subnetz bereits eine Ressource für einen Dienst bereitgestellt wurde, kann die Subnetzdelegierung erst entfernt werden, nachdem alle Ressourcen für den Dienst entfernt wurden. Wählen Sie in der Liste **Dienste** den Dienst aus, der als neues Ziel für die Delegierung fungieren soll.
5. Wählen Sie **Speichern** aus.

**Befehle**

- Azure CLI: [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)
- PowerShell: [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="delete-a-subnet"></a>Löschen eines Subnetzes

Sie können ein Subnetz erst löschen, wenn es keine Ressourcen mehr enthält. Falls das Subnetz noch Ressourcen enthält, müssen Sie diese Ressourcen zunächst löschen, bevor das Subnetz gelöscht werden kann. Die Schritte zum Löschen einer Ressource variieren abhängig von der Ressource. Informationen zum Löschen von Ressourcen in Subnetzen finden Sie in der Dokumentation zum jeweiligen Ressourcentyp, den Sie löschen möchten.

1. Geben Sie oben im Portal *Virtuelle Netzwerke* in das Suchfeld ein. Wenn **Virtuelle Netzwerke** in den Suchergebnissen angezeigt wird, können Sie den Begriff auswählen.
2. Wählen Sie in der Liste der virtuellen Netzwerke das virtuelle Netzwerk mit dem Subnetz aus, das Sie löschen möchten.
3. Wählen Sie unter **EINSTELLUNGEN** die Option **Subnetze** aus.
4. Wählen Sie in der Liste der Subnetze auf der rechten Seite **...** für das Subnetz, das Sie löschen möchten.
5. Wählen Sie **Löschen** und dann **Ja**.

**Befehle**

- Azure CLI: [az network vnet delete](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete)
- PowerShell: [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Berechtigungen

Zum Durchführen von Aufgaben für Subnetze muss Ihr Konto der Rolle [Netzwerkmitwirkender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) oder einer [benutzerdefinierten](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Rolle zugewiesen sein, der die entsprechenden, in der folgenden Tabelle aufgeführten Aktionen zugewiesen wurden:

|Aktion                                                                   |   NAME                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Lesen des Subnetzes eines virtuellen Netzwerks              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   Erstellen oder Aktualisieren des Subnetzes eines virtuellen Netzwerks  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   Löschen des Subnetzes eines virtuellen Netzwerks            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Verknüpfen eines virtuellen Netzwerks                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Aktivieren eines Dienstendpunkts für ein Subnetz     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   Abrufen der virtuellen Computer in einem Subnetz       |

## <a name="next-steps"></a>Nächste Schritte

- Erstellen eines virtuelles Netzwerks und der zugehörigen Subnetze mithilfe von [PowerShell](powershell-samples.md)- oder [Azure CLI](cli-samples.md)-Beispielskripts oder mithilfe von Azure [Resource Manager-Vorlagen](template-samples.md)
- Erstellen und Anwenden einer [Azure-Richtlinie](policy-samples.md) für virtuelle Netzwerke
