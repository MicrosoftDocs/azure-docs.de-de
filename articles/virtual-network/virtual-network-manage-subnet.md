---
title: Hinzufügen, Ändern oder Löschen eines virtuellen Azure-Subnetzes
titlesuffix: Azure Virtual Network
description: Erfahren Sie, wo Sie Informationen zu virtuellen Netzwerken finden und wie Sie in Azure ein Subnetz eines virtuellen Netzwerks hinzufügen, ändern oder löschen.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2020
ms.author: kumud
ms.openlocfilehash: 15fe5d6d16948875253d65e70d9d440214a4a2e8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995657"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Hinzufügen, Ändern oder Löschen von Subnetzen virtueller Netzwerke

Erfahren Sie, wie Sie Subnetze virtueller Netzwerke hinzufügen, ändern oder löschen. Alle in einem virtuellen Netzwerk bereitgestellten Azure-Ressourcen werden in einem Subnetz innerhalb eines virtuellen Netzwerks bereitgestellt. Wenn Sie mit virtuellen Netzwerken noch nicht vertraut sind, lesen Sie mehr darüber in der [Übersicht über virtuelle Netzwerke](virtual-networks-overview.md), oder arbeiten Sie eine [Schnellstart](quick-create-portal.md)-Anleitung durch. Weitere Informationen zur Verwaltung eines virtuellen Netzwerks finden Sie unter [Erstellen, Ändern oder Löschen eines virtuellen Netzwerks](manage-virtual-network.md).

## <a name="before-you-begin"></a>Voraussetzungen

Falls Sie noch nicht über ein Azure-Konto verfügen, richten Sie ein Azure-Konto mit einem aktiven Abonnement ein. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Führen Sie anschließend eine der folgenden Aufgaben aus, bevor Sie mit den Schritten der anderen Abschnitte dieses Artikels beginnen: 

- **Portalbenutzer:** Melden Sie sich mit Ihrem Azure-Konto beim [Azure-Portal](https://portal.azure.com) an.

- **PowerShell-Benutzer:** Führen Sie die Befehle entweder in [Azure Cloud Shell](https://shell.azure.com/powershell) oder in PowerShell auf Ihrem Computer aus. Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. Suchen Sie auf der Browserregisterkarte „Azure Cloud Shell“ die Dropdownliste **Umgebung auswählen**, und wählen Sie dann **PowerShell** aus, sofern diese Option nicht bereits ausgewählt ist.

    Wenn Sie PowerShell lokal ausführen, verwenden Sie die Azure PowerShell-Modulversion 1.0.0 oder höher. Führen Sie `Get-Module -ListAvailable Az.Network` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Führen Sie außerdem `Connect-AzAccount` aus, um eine Verbindung mit Azure herzustellen.

- **Benutzer der Azure-Befehlszeilenschnittstelle (Command-Line Interface, CLI):** Führen Sie die Befehle entweder in [Azure Cloud Shell](https://shell.azure.com/bash) oder über die CLI auf Ihrem Computer aus. Verwenden Sie bei lokaler Ausführung der Azure CLI die Version 2.0.31 oder höher. Führen Sie `az --version` aus, um die installierte Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli). Führen Sie außerdem `az login` aus, um eine Verbindung mit Azure herzustellen.

Das Konto, bei dem Sie sich anmelden oder das Sie zum Herstellen einer Verbindung mit Azure verwenden, muss der Rolle [Netzwerkmitwirkender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) oder einer [benutzerdefinierten Rolle](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) zugewiesen sein, der die entsprechenden, unter [Berechtigungen](#permissions) aufgeführten Aktionen zugewiesen wurden.

## <a name="add-a-subnet"></a>Hinzufügen eines Subnetzes

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com), um Ihr virtuelles Netzwerk anzuzeigen. Suchen Sie nach **Virtuelle Netzwerke**, und wählen Sie diese Option aus.

2. Wählen den Namen des virtuellen Netzwerks aus, dem Sie ein Subnetz hinzufügen möchten.

3. Wählen Sie unter **Einstellungen** die Option **Subnetze** > **Subnetz** aus.

4. Geben Sie im Dialogfeld **Subnetz hinzufügen** Werte für die folgenden Einstellungen ein:

    | Einstellung | BESCHREIBUNG |
    | --- | --- |
    | **Name** | Der Name muss innerhalb des virtuellen Netzwerks eindeutig sein. Für die maximale Kompatibilität mit anderen Azure-Diensten wird empfohlen, einen Buchstaben als erstes Zeichen des Namens zu verwenden. Beispielsweise wird Azure Application Gateway nicht in einem Subnetz bereitgestellt, dessen Name mit einer Zahl beginnt. |
    | **Adressbereich** | <p>Der Bereich muss innerhalb des Adressraums für das virtuelle Netzwerk eindeutig sein. Der Bereich darf keine Überschneidungen mit anderen Subnetzadressbereichen innerhalb des virtuellen Netzwerks aufweisen. Der Adressraum muss mithilfe der CIDR-Notation (Classless Inter-Domain Routing, klassenloses domänenübergreifendes Routing) angegeben werden.</p><p>In einem virtuellen Netzwerk mit dem Adressraum *10.0.0.0/16* können Sie beispielsweise für ein Subnetz den Adressraum *10.0.0.0/22* definieren. Der kleinste Bereich, den Sie angeben können, ist */29*, der acht IP-Adressen für das Subnetz bereitstellt. Azure reserviert die erste und letzte Adresse in jedem Subnetz aus Gründen der Protokollkonformität. Drei weitere Adressen sind für die Nutzung durch Azure-Dienste reserviert. Demzufolge führt das Einrichten eines Subnetzes mit dem Adressbereich */29* zu drei nutzbaren IP-Adressen im Subnetz.</p><p>Wenn Sie ein virtuelles Netzwerk mit einem VPN-Gateway verbinden möchten, müssen Sie ein Gatewaysubnetz erstellen. Weitere Informationen finden Sie unter [Informationen zu VPN Gateway-Einstellungen](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) im Abschnitt „Gatewaysubnetz“. Unter bestimmten Bedingungen können Sie den Adressbereich ändern, nachdem das Subnetz hinzugefügt wurde. Informationen zum Ändern des Adressbereichs eines Subnetzes finden Sie im Abschnitt [Ändern der Subnetzeinstellungen](#change-subnet-settings).</p> |
    | **Netzwerksicherheitsgruppe** | Sie können einem Subnetz keine oder eine vorhandene Netzwerksicherheitsgruppe zuordnen, um ein- und ausgehenden Netzwerkdatenverkehr für das Subnetz zu filtern. Die Netzwerksicherheitsgruppe muss demselben Abonnement und Standort wie das virtuelle Netzwerk angehören. Erfahren Sie mehr über [Netzwerksicherheitsgruppen](security-overview.md) und das [Erstellen einer Netzwerksicherheitsgruppe](tutorial-filter-network-traffic.md). |
    | **Routingtabelle** | Sie können dem Subnetz optional eine vorhandene Routingtabelle zur Steuerung der Weiterleitung von Netzwerkdatenverkehr zu anderen Netzwerken zuordnen. Die Routingtabelle muss demselben Abonnement und Standort wie das virtuelle Netzwerk angehören. Erfahren Sie mehr über [Azure-Routing](virtual-networks-udr-overview.md) und das [Erstellen einer Routingtabelle](tutorial-create-route-table-portal.md). |
    | **Dienstendpunkte** | <p>Für ein Subnetz können ggf. ein oder mehrere Dienstendpunkte aktiviert sein. Zum Aktivieren eines Dienstendpunkts für einen Dienst wählen Sie Dienste, für die Sie Dienstendpunkte aktivieren möchten, in der Liste **Dienste** aus. Azure konfiguriert den Standort für einen Endpunkt automatisch. Standardmäßig konfiguriert Azure die Dienstendpunkte für die Region des virtuellen Netzwerks. Damit Azure Storage Szenarien für ein regionales Failover unterstützt werden, konfiguriert Azure Endpunkte automatisch für [Azure-Regionspaare](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).</p><p>Zum Entfernen eines Dienstendpunkts heben Sie die Auswahl des Diensts auf, für den der Dienstendpunkt entfernt werden soll. Weitere Informationen zu Dienstendpunkten und den Diensten, für die sie aktiviert werden können, finden Sie unter [VNET-Dienstendpunkte](virtual-network-service-endpoints-overview.md). Nach dem Aktivieren eines Dienstendpunkts für einen Dienst müssen Sie auch Netzwerkzugriff für das Subnetz für eine mit dem Dienst erstellte Ressource aktivieren. Beispiel: Wenn Sie den Dienstendpunkt für **Microsoft.Storage** aktivieren, müssen Sie auch Netzwerkzugriff auf alle Azure Storage-Konten aktivieren, auf die Netzwerkzugriff gewährt werden soll. Informationen zum Aktivieren des Netzwerkzugriffs auf Subnetze, für die ein Dienstendpunkt aktiviert ist, finden Sie in der Dokumentation zum jeweiligen Dienst, für den Sie den Dienstendpunkt aktiviert haben.</p><p>Um zu überprüfen, ob ein Dienstendpunkt für ein Subnetz aktiviert wurde, zeigen Sie die [effektiven Routen](diagnose-network-routing-problem.md) für eine Netzwerkschnittstelle im Subnetz an. Wenn Sie einen Endpunkt konfigurieren, werden eine *Standardroute* mit den Adresspräfixen des Diensts und **VirtualNetworkServiceEndpoint** als Typ des nächsten Hops angezeigt. Weitere Informationen zum Routing finden Sie unter [Routing von Datenverkehr für virtuelle Netzwerke](virtual-networks-udr-overview.md).</p> |
    | **Subnetzdelegierung** | Für ein Subnetz können ggf. ein oder mehrere Delegierungen aktiviert sein. Bei der Subnetzdelegierung erhält der Dienst explizite Berechtigungen, um dienstspezifische Ressourcen im Subnetz zu erstellen, indem bei der Bereitstellung des Diensts ein eindeutiger Bezeichner verwendet wird. Wählen Sie in der Liste **Dienste** den Dienst aus, der als Ziel für die Delegierung fungieren soll. |

5. Um das Subnetz dem ausgewählten virtuellen Netzwerk hinzuzufügen, wählen Sie **OK**.

### <a name="commands"></a>Befehle

| Tool | Get-Help |
| ---- | ------- |
| Azure CLI | [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) |
| PowerShell | [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) |

## <a name="change-subnet-settings"></a>Ändern von Subnetzeinstellungen

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com), um Ihr virtuelles Netzwerk anzuzeigen. Suchen Sie nach **Virtuelle Netzwerke**, und wählen Sie diese Option aus.

2. Wählen den Namen des virtuellen Netzwerks mit dem Subnetz aus, das Sie ändern möchten.

3. Wählen Sie unter **Einstellungen** die Option **Subnetze** aus.

4. Wählen Sie in der Liste der Subnetze das Subnetz aus, für das Sie Einstellungen ändern möchten.

5. Ändern Sie auf der Subnetzseite die folgenden Einstellungen je nach Bedarf:

    | Einstellung | BESCHREIBUNG |
    | --- | --- |
    | **Adressbereich** | Wenn innerhalb des Subnetzes keine Ressourcen bereitgestellt sind, können Sie den Adressbereich ändern. Wenn Ressourcen im Subnetz vorhanden sind, müssen Sie die Ressourcen zuerst entweder in ein anderes Subnetz verschieben oder aus dem Subnetz löschen. Die Schritte zum Verschieben oder Löschen einer Ressource variieren abhängig von der Ressource. Informationen zum Verschieben oder Löschen von Ressourcen in Subnetzen finden Sie in der Dokumentation zum jeweiligen Ressourcentyp. Die Einschränkungen für den **Adressbereich** finden Sie in Schritt 4 von [Hinzufügen eines Subnetzes](#add-a-subnet). |
    | **Benutzer** | Sie können den Zugriff auf das Subnetz über integrierte Rollen oder eigene benutzerdefinierte Rollen steuern. Weitere Informationen zum Zuweisen von Rollen und Benutzern für den Zugriff auf das Subnetz finden Sie unter [Hinzufügen einer Rollenzuweisung](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment). |
    | **Netzwerksicherheitsgruppe** und **Routingtabelle** | Siehe Schritt 4 unter [Hinzufügen eines Subnetzes](#add-a-subnet). |
    | **Dienstendpunkte** | <p>Siehe „Dienstendpunkte“ Schritt 4 von [Hinzufügen eines Subnetzes](#add-a-subnet). Wenn Sie einen Dienstendpunkt für ein vorhandenes Subnetz aktivieren, stellen Sie sicher, dass auf keiner Ressource im Subnetz kritische Tasks ausgeführt werden. Dienstendpunkte ändern die Routen jeder Netzwerkschnittstelle im Subnetz. Dienstendpunkte wechseln von der Standardroute mit dem Adresspräfix *0.0.0.0/0* und *Internet* als Typ des nächsten Hops zu einer neuen Route mit dem Adresspräfix des Diensts und *VirtualNetworkServiceEndpoint* als Typ des nächsten Hops.</p><p>Während dieses Wechsels werden alle offenen TCP-Verbindungen geschlossen. Der Dienstendpunkt wird erst aktiviert, wenn der gesamte Datenverkehr an den Dienst in allen Netzwerkschnittstellen mit der neuen Route aktualisiert wurde. Weitere Informationen zum Routing finden Sie unter [Routing von Datenverkehr für virtuelle Netzwerke](virtual-networks-udr-overview.md).</p> |
    | **Subnetzdelegierung** | Siehe „Dienstendpunkte“ Schritt 4 von [Hinzufügen eines Subnetzes](#add-a-subnet). Die Subnetzdelegierung kann so geändert werden, dass dafür null oder mehr Delegierungen aktiviert sind. Wenn im Subnetz bereits eine Ressource für einen Dienst bereitgestellt ist, kann Subnetzdelegierung erst hinzugefügt oder entfernt werden, nachdem alle Ressourcen für den Dienst entfernt wurden. Wählen Sie in der Liste **Dienste** den Dienst aus, der als neues Ziel für die Delegierung fungieren soll. |

6. Wählen Sie **Speichern** aus.

### <a name="commands"></a>Befehle

| Tool | Get-Help |
| ---- | ------- |
| Azure CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-subnet"></a>Löschen eines Subnetzes

Sie können ein Subnetz erst löschen, wenn es keine Ressourcen mehr enthält. Falls sich noch Ressourcen im Subnetz befinden, müssen Sie diese Ressourcen zunächst löschen, damit das Subnetz gelöscht werden kann. Die Schritte zum Löschen einer Ressource variieren abhängig von der Ressource. Informationen zum Löschen von Ressourcen in Subnetzen finden Sie in der Dokumentation zum jeweiligen Ressourcentyp.

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com), um Ihr virtuelles Netzwerk anzuzeigen. Suchen Sie nach **Virtuelle Netzwerke**, und wählen Sie diese Option aus.

2. Wählen den Namen des virtuellen Netzwerks mit dem Subnetz aus, das Sie löschen möchten.

3. Wählen Sie unter **Einstellungen** die Option **Subnetze** aus.

4. Wählen Sie in der Liste der Subnetze das Subnetz aus, das Sie löschen möchten.

5. Klicken Sie auf **Löschen** und dann im Bestätigungsdialogfeld auf **Ja**.

### <a name="commands"></a>Befehle

| Tool | Get-Help |
| ---- | ------- |
| Azure CLI | [az network vnet subnet delete](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete) |
| PowerShell | [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json) |

## <a name="permissions"></a>Berechtigungen

Zum Durchführen von Aufgaben für Subnetze muss Ihr Konto der Rolle [Netzwerkmitwirkender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) oder einer [benutzerdefinierten Rolle](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) zugewiesen sein, der die entsprechenden Aktionen in der folgenden Tabelle zugewiesen wurden:

|Aktion                                                                   |   Name                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Lesen des Subnetzes eines virtuellen Netzwerks              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   Erstellen oder Aktualisieren des Subnetzes eines virtuellen Netzwerks  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   Löschen des Subnetzes eines virtuellen Netzwerks            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Verknüpfen eines virtuellen Netzwerks                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Aktivieren eines Dienstendpunkts für ein Subnetz     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   Abrufen der virtuellen Computer in einem Subnetz       |

## <a name="next-steps"></a>Nächste Schritte

- Erstellen eines virtuelles Netzwerks und der zugehörigen Subnetze mithilfe von [PowerShell](powershell-samples.md)- oder [Azure CLI](cli-samples.md)-Beispielskripts oder mithilfe von Azure [Resource Manager-Vorlagen](template-samples.md)
- Erstellen und Zuweisen von [Azure Policy-Definitionen](policy-samples.md) für virtuelle Netzwerke
