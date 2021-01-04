---
title: Verwalten öffentlicher IP-Adressen | Microsoft-Dokumentation
titleSuffix: Azure Virtual Network
description: Verwalten öffentlicher IP-Adressen  Erfahren Sie auch, wie eine öffentliche IP-Adresse eine Ressource mit eigenen konfigurierbaren Einstellungen ist.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: kumud
ms.openlocfilehash: a8847768cf4cf1ea85df8646a8848aac02cbd621
ms.sourcegitcommit: 3e8058f0c075f8ce34a6da8db92ae006cc64151a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92629254"
---
# <a name="manage-public-ip-addresses"></a>Verwalten öffentlicher IP-Adressen

Sie erhalten Informationen über öffentliche IP-Adressen und darüber, wie Sie diese erstellen, ändern und löschen können. Eine öffentliche IP-Adresse ist eine Ressource mit eigenen konfigurierbaren Einstellungen. Das Zuweisen einer öffentlichen IP-Adresse an eine Azure-Ressource, die öffentliche IP-Adressen unterstützt, ermöglicht Folgendes:
- Eingehende Kommunikation aus dem Internet an die Ressource, z.B. Azure Virtual Machines (VM), Azure Application Gateways, Azure Load Balancers, Azure VPN Gateways und weitere. Sie können mit einigen Ressourcen wie z.B. virtuellen Computern aus dem Internet auch dann kommunizieren, wenn einem virtuellem Computer keine öffentliche IP-Adresse zugewiesen ist, sofern der virtuelle Computer Teil eines Lastenausgleich-Back-End-Pools und dem Lastenausgleich eine öffentliche IP-Adresse zugewiesen ist. Ob einer Ressource für einen bestimmten Azure-Dienst eine öffentliche IP-Adresse zugewiesen werden kann, oder ob über die öffentliche IP-Adresse einer anderen Azure-Ressource damit kommuniziert werden kann, erfahren Sie in der Dokumentation für den jeweiligen Dienst.
- Ausgehende Konnektivität mit dem Internet über eine vorhersagbare IP-Adresse. Beispielsweise kann ein virtueller Computer, obwohl ihm keine öffentliche IP-Adresse zugewiesen ist, ausgehend mit dem Internet kommunizieren, weil seine Netzwerkadresse von Azure standardmäßig in eine nicht vorhersagbare öffentliche Adresse übersetzt wird. Indem Sie Ressourcen eine öffentliche IP-Adresse zuweisen, wissen Sie, welche IP-Adresse für die ausgehende Verbindung verwendet wird. Eine solche Adresse ist zwar vorhersehbar, kann sich aber ändern – je nach ausgewählter Zuweisungsmethode. Weitere Informationen finden Sie unter [Erstellen einer öffentlichen IP-Adresse](#create-a-public-ip-address). Weitere Informationen zu ausgehenden Verbindungen von Azure-Ressourcen finden Sie unter [Grundlegendes zu ausgehenden Verbindungen in Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="before-you-begin"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Führen Sie zuerst die folgenden Aufgaben aus, ehe Sie die Schritte in den Abschnitten dieses Artikels durchführen:

- Falls Sie noch nicht über ein Azure-Konto verfügen, können Sie sich für ein [kostenloses Testkonto](https://azure.microsoft.com/free) registrieren.
- Öffnen Sie bei Verwendung des Portals https://portal.azure.com, und melden Sie sich mit Ihrem Azure-Konto an.
- Wenn Sie PowerShell-Befehle zum Durchführen von Aufgaben in diesem Artikel verwenden, führen Sie die Befehle entweder in [Azure Cloud Shell](https://shell.azure.com/powershell) oder durch Ausführen von PowerShell auf Ihrem Computer aus. Azure Cloud Shell ist eine kostenlose interaktive Shell, mit der Sie die Schritte in diesem Artikel ausführen können. Sie verfügt über allgemeine vorinstallierte Tools und ist für die Verwendung mit Ihrem Konto konfiguriert. Für dieses Tutorial ist das Azure PowerShell-Modul Version 1.0.0 oder höher erforderlich. Führen Sie `Get-Module -ListAvailable Az` aus, um die installierte Version zu ermitteln. Wenn Sie ein Upgrade ausführen müssen, finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps) Informationen dazu. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzAccount` ausführen, um eine Verbindung mit Azure herzustellen.
- Wenn Sie Befehle der Azure-Befehlszeilenschnittstelle (CLI) zum Durchführen von Aufgaben in diesem Artikel verwenden, führen Sie die Befehle entweder in [Azure Cloud Shell](https://shell.azure.com/bash) oder durch Ausführen der CLI auf Ihrem Computer aus. Für dieses Tutorial ist die Azure CLI-Version 2.0.31 oder höher erforderlich. Führen Sie `az --version` aus, um die installierte Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli). Wenn Sie die Azure CLI lokal ausführen, müssen Sie auch `az login` ausführen, um eine Verbindung mit Azure herzustellen.

Das Konto, bei dem Sie sich anmelden oder das Sie zum Herstellen einer Verbindung mit Azure verwenden, muss der Rolle [Netzwerkmitwirkender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) oder einer [benutzerdefinierten Rolle](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) zugewiesen sein, der die entsprechenden, unter [Berechtigungen](#permissions) aufgeführten Aktionen zugewiesen wurden.

Für öffentliche IP-Adressen fällt eine Schutzgebühr an. Informationen zu den Preisen finden Sie auf der Seite [Preise für IP-Adressen](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="create-a-public-ip-address"></a>Erstellen einer öffentlichen IP-Adresse

Anleitungen zum Erstellen von öffentlichen IP-Adressen mit dem Portal, mit PowerShell oder der CLI finden Sie auf den folgenden Seiten:

 * [Erstellen öffentlicher IP-Adressen: Portal](https://docs.microsoft.com/azure/virtual-network/create-public-ip-portal?tabs=option-create-public-ip-standard-zones)
 * [Erstellen öffentlicher IP-Adressen: PowerShell](https://docs.microsoft.com/azure/virtual-network/create-public-ip-powershell?tabs=option-create-public-ip-standard-zones)
 * [Erstellen öffentlicher IP-Adressen: Azure CLI](https://docs.microsoft.com/azure/virtual-network/create-public-ip-cli?tabs=option-create-public-ip-standard-zones)

>[!NOTE]
>Obwohl das Portal die Option bietet, zwei öffentliche IP-Adressen zu erstellen (eine IPv4- und eine IPv6-Adresse), erstellen die folgenden PowerShell- und CLI-Befehle eine Ressource mit einer Adresse für jeweils nur eine der IP-Versionen. Wenn Sie zwei öffentliche IP-Adressressourcen (eine für jede IP-Version) benötigen, müssen Sie den folgenden Befehl zweimal ausführen und bei jeder Ausführung unterschiedliche Namen und IP-Versionen für die öffentlichen IP-Adressressourcen angeben.

Weitere Details zu den spezifischen Attributen einer öffentlichen IP-Adresse während der Erstellung finden Sie in der folgenden Tabelle.

   |Einstellung|Erforderlich?|Details|
   |---|---|---|
   |IP-Version|Ja| Wählen Sie „IPv4“, „IPv6“ oder „Beide“ aus. Wenn Sie „Beide“ auswählen, werden zwei öffentliche IP-Adressen erstellt: eine IPv4-Adresse und eine IPv6-Adresse. Weitere Informationen finden Sie unter [Was ist IPv6 für Azure Virtual Network? (Vorschau)](../virtual-network/ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
   |SKU|Ja|Alle öffentlichen IP-Adressen, die vor der Einführung von SKUs erstellt wurden, sind öffentliche IP-Adressen für eine **Basic** -SKU. Sie können die SKU nicht ändern, nachdem die öffentliche IP-Adresse erstellt wurde. Ein eigenständiger virtueller Computer, virtuelle Computer innerhalb einer Verfügbarkeitsgruppe oder VM-Skalierungsgruppen können Basic- oder Standard-SKUs verwenden. Das Mischen von SKUs zwischen virtuellen Computern in Verfügbarkeitsgruppen oder Skalierungsgruppen oder eigenständigen VMs ist nicht zulässig. **Basic** -SKU: Wenn Sie eine öffentliche IP-Adresse in einer Region erstellen, die Verfügbarkeitszonen unterstützt, wird die Einstellung **Verfügbarkeitszone** standardmäßig auf *Keine* festgelegt. Öffentliche IP-Adressen des Typs „Basic“ unterstützen keine Verfügbarkeitszonen. **Standard** -SKU: Eine öffentliche IP-Adresse für eine Standard-SKU kann dem Front-End eines virtuellen Computers oder Load Balancers zugeordnet werden. Wenn Sie eine öffentliche IP-Adresse in einer Region erstellen, die Verfügbarkeitszonen unterstützt, wird die Einstellung **Verfügbarkeitszone** standardmäßig auf *Zonenredundant* festgelegt. Weitere Informationen zu Verfügbarkeitszonen finden Sie im Abschnitt **Verfügbarkeitszone**. Die Standard-SKU ist erforderlich, wenn Sie die Adresse einem Standard-Load Balancer zuordnen. Weitere Informationen zum Standard-Load Balancer finden Sie unter [Azure Load Balancer Standard overview](../load-balancer/load-balancer-standard-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Übersicht über den Standard-Azure Load Balancer, in englischer Sprache). Wenn Sie der Netzwerkschnittstelle eines virtuellen Computers eine öffentliche IP-Adresse für eine Standard-SKU zuweisen, müssen Sie den geplanten Datenverkehr explizit mit einer [Netzwerksicherheitsgruppe](security-overview.md#network-security-groups) zulassen. Die Kommunikation mit der Ressource schlägt fehl, bis Sie eine Netzwerksicherheitsgruppe erstellen und zuordnen und den gewünschten Datenverkehr explizit zulassen.|
   |Name|Ja|Der Name muss innerhalb der ausgewählten Ressourcengruppe eindeutig sein.|
   |IP-Adresszuweisung|Ja|**Dynamisch:** Dynamische Adressen werden nur zugewiesen, nachdem einer Azure-Ressource eine öffentliche IP-Adresse zugeordnet und die Ressource erstmalig gestartet wurde. Dynamische Adressen können sich ändern, wenn sie einer Ressource zugewiesen sind, z. B. einem virtuellen Computer, und der virtuelle Computer wird beendet (seine Zuordnung aufgehoben) und dann neu gestartet. Die Adresse bleibt unverändert, wenn ein virtueller Computer neu gestartet oder (ohne Aufheben der Zuordnung) beendet wird. Dynamische Adressen werden freigegeben, wenn eine öffentliche IP-Adressressource von einer Ressource getrennt wird, der sie zugeordnet ist. **Statisch:** Statische Adressen werden zugewiesen, wenn eine öffentliche IP-Adresse erstellt wird. Statische Adressen werden erst freigegeben, wenn eine öffentliche IP-Adressressource gelöscht wird. Wenn die Adresse keiner Ressource zugeordnet ist, können Sie die Zuweisungsmethode ändern, nachdem die Adresse erstellt wurde. Wenn die Adresse einer Ressource zugeordnet ist, können Sie die Zuweisungsmethode eventuell nicht ändern. Wenn Sie *IPv6* als **IP-Version** auswählen, muss für die SKU „Basic“ als Zuweisungsmethode *Dynamisch* ausgewählt werden.  Adressen der SKU „Standard“ sind sowohl für IPv4 als auch für IPv6 *statisch*. |
   |Leerlaufzeitüberschreitung (Minuten)|Nein|Gibt an, wie viele Minuten eine TCP- oder HTTP-Verbindung geöffnet bleiben soll, ohne dass Clients Keep-Alive-Meldungen senden müssen. Wenn Sie IPv6 als **IP-Version** auswählen, kann dieser Wert nicht geändert werden. |
   |DNS-Namensbezeichnung|Nein|Muss in dem Azure-Standort, in dem Sie den Namen erstellen, eindeutig sein (über alle Abonnements und Kunden hinweg). Azure registriert den Namen und die IP-Adresse automatisch im DNS, sodass Sie über den Namen eine Verbindung mit der Ressource herstellen können. Azure fügt ein Standardsubnetz wie etwa *location.cloudapp.azure.com* (wobei „location“ der Standort ist, den Sie auswählen) an den von Ihnen bereitgestellten Namen an, um den vollqualifizierten DNS-Namen zu erstellen. Wenn Sie beide Adressversionen für die Erstellung ausgewählt haben, wird sowohl der IPv4- als auch der IPv6-Adresse der gleiche DNS-Name zugewiesen. Das Standard-DNS von Azure enthält IPv4-A- und IPv6-AAAA-Namenseinträge und antwortet beim Nachschlagen des DNS-Namens mit beiden Einträgen. Der Client wählt die Adresse (IPv4 oder IPv6) für die Kommunikation aus. Sie können anstelle der oder zusätzlich zur DNS-Namensbezeichnung mit dem Standardsuffix den Azure DNS-Dienst verwenden, um einen DNS-Namen mit einem benutzerdefinierten Suffix zu konfigurieren, das in die öffentliche IP-Adresse aufgelöst wird. Weitere Informationen finden Sie unter [Bereitstellen von benutzerdefinierten Domäneneinstellungen für einen Azure-Dienst mit Azure DNS](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address).|
   |Name (nur sichtbar, wenn Sie als IP-Version **Beide** auswählen)|Ja, wenn Sie als IP-Version **Beide** auswählen|Der Name muss sich von dem Namen unterscheiden, den Sie als ersten **Namen** in diese Liste eingeben. Wenn Sie sowohl eine IPv4- als auch eine IPv6-Adresse erstellen, erstellt das Portal zwei separate öffentliche IP-Adressressourcen, wobei jeder dieser Ressourcen eine IP-Adressversion zugewiesen ist.|
   |IP-Adresszuweisung (nur sichtbar, wenn Sie als IP-Version **Beide** auswählen)|Ja, wenn Sie als IP-Version **Beide** auswählen|Dieselben Einschränkungen wie bei der IP-Adresszuweisung oben|
   |Subscription|Ja|Muss im selben [Abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) wie die Ressource vorhanden sein, der Sie die öffentlichen IP-Adressen zuordnen.|
   |Resource group|Ja|Kann in derselben oder in einer anderen [Ressourcengruppe](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) wie bzw. als die Ressource vorhanden sein, der Sie die öffentlichen IP-Adressen zuordnen.|
   |Position|Ja|Muss am selben [Standort](https://azure.microsoft.com/regions) (auch als Region bezeichnet) wie die Ressource vorhanden sein, der Sie die öffentlichen IP-Adressen zuordnen.|
   |Verfügbarkeitszone| Nein | Diese Einstellung wird nur angezeigt, wenn Sie einen unterstützten Standort auswählen. Eine Liste der unterstützten Standorte finden Sie unter [Übersicht über Verfügbarkeitszonen](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Wenn Sie die **Basic** -SKU ausgewählt haben, wird automatisch *Keine* ausgewählt. Wenn Sie es vorziehen, eine bestimmte Zone zu gewährleisten, können Sie eine bestimmte Zone auswählen. Keine der beiden Optionen ist zonenredundant. Bei Auswahl der **Standard** -SKU: Es wird automatisch die zonenredundante Option ausgewählt, und der Datenpfad wird gegen Zonenausfall stabilisiert. Wenn Sie es vorziehen, eine bestimmte Zone zu gewährleisten, die nicht gegen Zonenausfall stabilisiert ist, können Sie eine bestimmte Zone auswählen.

## <a name="view-modify-settings-for-or-delete-a-public-ip-address"></a>Anzeigen, Ändern von Einstellungen oder Löschen einer öffentlichen IP-Adresse

   - **Anzeigen/Auflisten** : Zum Überprüfen der Einstellungen für eine öffentliche IP-Adresse, einschließlich SKU, Adresse, entsprechender Zuordnung (z. B. Virtual Machine-NIC, Load Balancer-Front-End).
   - **Ändern** : Zum Ändern der Einstellungen mithilfe der Informationen in Schritt 4 von [Erstellen einer öffentlichen IP-Adresse](#create-a-public-ip-address), z. B. des Leerlauftimeouts, der DNS-Namenbezeichnung oder der Zuweisungsmethode.  (Den gesamten Upgradeprozess einer SKU mit öffentlicher IP-Adresse von „Basic“ zu „Standard“ finden Sie unter [Upgrade von öffentlichen Azure-IP-Adressen](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address-upgrade).)
   >[!WARNING]
   >Wenn Sie die Zuweisung für eine öffentliche IP-Adresse von „statisch“ in „dynamisch“ ändern möchten, müssen Sie die Adresse zunächst von den jeweiligen IP-Konfigurationen trennen (siehe Abschnitt **Löschen** ).  Beachten Sie außerdem Folgendes: Wenn Sie die Zuweisungsmethode von statisch in dynamisch ändern, geht die IP-Adresse verloren, die der öffentlichen IP-Adresse zugewiesen war. Während die öffentlichen DNS-Server von Azure eine Zuordnung zwischen statischen oder dynamischen Adressen und jeder DNS-Namensbezeichnung (sofern eine solche definiert ist) verwalten, kann sich eine dynamische IP-Adresse ändern, wenn der virtuelle Computer gestartet wird, nachdem er sich im Status „Beendet (Zuordnung aufgehoben)“ befunden hat. Möchten Sie verhindern, dass sich die Adresse ändert, weisen Sie eine statische IP-Adresse zu.
   
|Vorgang|Azure-Portal|Azure PowerShell|Azure CLI|
|---|---|---|---|
|Sicht | Im Abschnitt **Übersicht** einer öffentlichen IP-Adresse |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) zum Abrufen eines öffentlichen IP-Adressobjekts und zum Anzeigen seiner Einstellungen| [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) zum Anzeigen von Einstellungen|
|List | Unter der Kategorie **Öffentliche IP-Adressen** |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) zum Abrufen von mindestens einem öffentlichen IP-Adressobjekt und zum Anzeigen seiner Einstellungen|[az network public-ip list](/cli/azure/network/public-ip#az-network-public-ip-list), um öffentliche IP-Adressen aufzulisten|
|Ändern | Wählen Sie für eine IP-Adresse, die getrennt ist, **Konfiguration** aus, um das Leerlauftimeout, die DNS-Namenbezeichnung oder die Zuweisung der IP-Basisadresse von statisch in dynamisch zu ändern.  |[Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) zum Aktualisieren von Einstellungen |[az network public-ip update](/cli/azure/network/public-ip#az-network-public-ip-update) zum Aktualisieren |

   - **Löschen:** Das Löschen öffentlicher IP-Adressen erfordert, dass das öffentliche IP-Objekt keiner IP-Konfiguration bzw. keinem Virtual Machine-NIC zugeordnet ist. Weitere Details finden Sie in der Tabelle unten.

|Resource|Azure-Portal|Azure PowerShell|Azure CLI|
|---|---|---|---|
|[Virtueller Computer](https://docs.microsoft.com/azure/virtual-network/remove-public-ip-address-vm)|Wählen Sie **Trennen** aus, um die IP-Adresse von der NIC-Konfiguration zu trennen, und wählen Sie dann **Löschen** aus.|[Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress), um die IP-Adresse von der NIC-Konfiguration zu trennen, [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) zum Löschen.|[az network public-ip update --remove](/cli/azure/network/public-ip#az-network-public-ip-update), um die IP-Adresse von der NIC-Konfiguration zu trennen, [az network public-ip delete](/cli/azure/network/public-ip#az-network-public-ip-delete) zum Löschen. |
|Load Balancer-Front-End | Navigieren Sie zu einer nicht verwendeten öffentlichen IP-Adresse, wählen Sie **Zuordnen** aus, und wählen Sie den Load Balancer mit der relevanten Front-End-IP-Konfiguration aus, um ihn zu ersetzen (die alte IP-Adresse kann dann mit derselben Methode wie für die VM gelöscht werden).  | [Set-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/set-azloadbalancerfrontendipconfig), um eine neue Front-End-IP-Konfiguration mit dem öffentlichen Load Balancer zu verknüpfen, [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) zum Löschen. Es kann auch [Remove-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/remove-azloadbalancerfrontendipconfig) verwendet werden, um eine Front-End-IP-Konfiguration zu entfernen, wenn es mehr als eine Konfiguration gibt. |[az network lb frontend-ip update](/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az_network_lb_frontend_ip_update), um eine neue Front-End-IP-Konfiguration dem öffentlichen Load Balancer zuzuordnen, [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) zum Löschen. Es kann auch [AZ network lb frontend-ip delete](/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az_network_lb_frontend_ip_delete) verwendet werden, um die Front-End-IP-Konfiguration zu entfernen, wenn mehrere Konfigurationen vorhanden sind.|
|Firewall|–| [Deallocate()](https://docs.microsoft.com/azure/firewall/firewall-faq#how-can-i-stop-and-start-azure-firewall), um die Zuordnung der Firewall aufzuheben und alle IP-Konfigurationen zu entfernen | [az network firewall ip-config delete](/cli/azure/ext/azure-firewall/network/firewall/ip-config#ext_azure_firewall_az_network_firewall_ip_config_delete), um IP-Adressen zu entfernen (zuvor muss aber PowerShell zum Aufheben der Zuordnung verwendet werden)|

## <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

Bei Verwendung einer VM-Skalierungsgruppe mit öffentlichen IP-Adressen sind den einzelnen VM-Instanzen keine separaten öffentlichen IP-Objekte zugeordnet. Allerdings kann ein öffentliches IP-Präfixobjekt verwendet werden, um die [Instanz-IPs zu generieren](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/).

Um die öffentlichen IP-Adressen für eine VM-Skalierungsgruppe aufzulisten, können Sie PowerShell ([Get-AzPublicIpAddress -VirtualMachineScaleSetName](/powershell/module/az.network/get-azpublicipaddress)) oder die CLI ([az vmss list-instance-public-ips](/cli/azure/vmss?view=azure-cli-latest#az_vmss_list_instance_public_ips)) verwenden.

Weitere Informationen finden Sie unter [Netzwerk für Azure-VM-Skalierungsgruppen](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-networking#public-ipv4-per-virtual-machine).

## <a name="assign-a-public-ip-address"></a>Zuweisen einer öffentlichen IP-Adresse

Erfahren Sie, wie Sie eine öffentliche IP-Adresse den folgenden Ressourcen zuweisen:

- Einer [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)- oder [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-VM (beim Erstellen) oder einem [vorhandenen virtuellen Computer](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [Öffentlicher Lastenausgleich](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Application Gateway](../application-gateway/application-gateway-create-gateway-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Site-to-Site-Verbindung über ein Azure VPN Gateway](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [VM-Skalierungsgruppe](../virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Berechtigungen

Zum Durchführen von Aufgaben für öffentliche IP-Adressen muss Ihr Konto der Rolle [Netzwerkmitwirkender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) oder einer [benutzerdefinierten](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Rolle zugewiesen sein, der die entsprechenden, in der folgenden Tabelle aufgeführten Aktionen zugewiesen wurden:

| Aktion                                                             | Name                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | Lesen einer öffentlichen IP-Adresse                                          |
| Microsoft.Network/publicIPAddresses/write                          | Schreiben oder Aktualisieren einer öffentlichen IP-Adresse                           |
| Microsoft.Network/publicIPAddresses/delete                         | Löschen einer öffentlichen IP-Adresse                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | Zuordnen einer öffentlichen IP-Adresse zu einer Ressource                    |

## <a name="next-steps"></a>Nächste Schritte

- Erstellen einer öffentlichen IP-Adresse mithilfe von [PowerShell](powershell-samples.md)- oder [Azure CLI](cli-samples.md)-Beispielskripts oder mithilfe von Azure [Resource Manager-Vorlagen](template-samples.md)
- Erstellen und Zuweisen von [Azure Policy-Definitionen](policy-samples.md) für öffentliche IP-Adressen
