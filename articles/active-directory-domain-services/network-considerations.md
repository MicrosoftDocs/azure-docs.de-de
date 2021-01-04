---
title: Netzwerkplanung und Verbindungen für Azure AD Domain Services | Microsoft-Dokumentation
description: Hier erhalten Sie Informationen zu einigen der Überlegungen zum Entwurf virtueller Netzwerke und zu den für die Konnektivität verwendeten Ressourcen, wenn Sie Azure Active Directory Domain Services ausführen.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 246da3a35396430bbda86e5a5e927a456618ac05
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619282"
---
# <a name="virtual-network-design-considerations-and-configuration-options-for-azure-active-directory-domain-services"></a>Überlegungen zum Entwurf virtueller Netzwerke und Konfigurationsoptionen für Azure Active Directory Domain Services

Azure Active Directory Domain Services (AD DS) stellt Authentifizierungs- und Verwaltungsdienste für andere Anwendungen und Workloads bereit. Die Netzwerkkonnektivität ist dabei eine Schlüsselkomponente. Ohne ordnungsgemäß konfigurierte virtuelle Netzwerkressourcen können Anwendungen und Workloads nicht mit den von Azure AD DS bereitgestellten Features kommunizieren und diese auch nicht verwenden. Planen Sie die Anforderungen für Ihr virtuelles Netzwerk, um sicherzustellen, dass Azure AD DS Ihre Anwendungen und Workloads nach Bedarf bedienen kann.

In diesem Artikel werden Überlegungen und Anforderungen an den Entwurf eines virtuellen Azure-Netzwerks zur Unterstützung von Azure AD DS erörtert.

## <a name="azure-virtual-network-design"></a>Entwurf von Azure Virtual Network

Um die Netzwerkkonnektivität bereitzustellen und Anwendungen und Dienste zur Authentifizierung bei einer von Azure AD DS verwalteten Domäne zu ermöglichen, verwenden Sie ein virtuelles Azure-Netzwerk und ein Subnetz. Im Idealfall sollte die verwaltete Domäne in einem eigenen virtuellen Netzwerk bereitgestellt werden.

Sie können ein separates Anwendungssubnetz in dasselbe virtuelle Netzwerk einbeziehen, um Ihre Verwaltungs-VM oder leichte Anwendungsworkloads zu hosten. Ein separates virtuelles Netzwerk für größere oder komplexe Anwendungsworkloads, das auf das virtuelle Azure AD DS-Netzwerk ausgerichtet ist, stellt in der Regel den am besten geeignete Entwurf dar.

Andere Entwurfsmöglichkeiten sind zulässig, sofern Sie die in den folgenden Abschnitten für das virtuelle Netzwerk und das Subnetz beschriebenen Anforderungen erfüllen.

Beim Entwerfen des virtuellen Netzwerks für Azure AD DS gelten die folgenden Überlegungen:

* Azure AD DS muss in derselben Azure-Region wie Ihr virtuelles Netzwerk bereitgestellt werden.
    * Derzeit können Sie nur eine verwaltete Domäne pro Azure AD-Mandanten bereitstellen. Die verwaltete Domäne wird in einer einzelnen Region bereitgestellt. Stellen Sie sicher, dass Sie ein virtuelles Netzwerk in einer [Region erstellen oder auswählen, die Azure AD DS](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all) unterstützt.
* Berücksichtigen Sie die Nähe zu anderen Azure-Regionen und zu den virtuellen Netzwerken, die Ihre Anwendungsworkloads hosten.
    * Um die Latenzzeit zu minimieren, belassen Sie Ihre Kernanwendungen in der Nähe oder in derselben Region wie das Subnetz des virtuellen Netzwerks für Ihre verwaltete Domäne. Sie können das Peering virtueller Netzwerke oder VPN-Verbindungen (Virtual Private Network) zwischen virtuellen Azure-Netzwerken verwenden. Diese Verbindungsoptionen werden in einem nachfolgenden Abschnitt erläutert.
* Das virtuelle Netzwerk darf sich nur die von der verwalteten Domäne bereitgestellten DNS-Dienste verlassen.
    * Azure AD DS bietet einen eigenen DNS-Dienst. Das virtuelle Netzwerk muss so konfiguriert sein, dass es diese DNS-Dienstadressen verwendet. Die Namensauflösung für weitere Namespaces kann mithilfe von bedingten Weiterleitungen erfolgen.
    * Sie können keine benutzerdefinierten DNS-Servereinstellungen verwenden, um Abfragen von anderen DNS-Servern weiterzuleiten, einschließlich VMs. Ressourcen im virtuellen Netzwerk müssen den von der verwalteten Domäne bereitgestellten DNS-Dienst verwenden.

> [!IMPORTANT]
> Sie können Azure AD DS nicht in ein anderes virtuelles Netzwerk verschieben, nachdem Sie den Dienst aktiviert haben.

Eine verwaltete Domäne stellt eine Verbindung mit einem Subnetz in einem virtuellen Azure-Netzwerk her. Entwerfen Sie dieses Subnetz für Azure AD DS mit den folgenden Überlegungen:

* Eine verwaltete Domäne muss in einem eigenen Subnetz bereitgestellt werden. Verwenden Sie kein bestehendes Subnetz oder Gatewaysubnetz.
* Während der Bereitstellung einer verwalteten Domäne wird eine Netzwerksicherheitsgruppe erstellt. Diese Netzwerksicherheitsgruppe enthält die erforderlichen Regeln für eine korrekte Dienstkommunikation.
    * Erstellen oder verwenden Sie keine bestehende Netzwerksicherheitsgruppe mit Ihren eigenen benutzerdefinierten Regeln.
* Für eine verwaltete Domäne sind 3–5 IP-Adressen erforderlich. Stellen Sie sicher, dass der IP-Adressbereich Ihres Subnetzes diese Anzahl von Adressen bereitstellen kann.
    * Die Einschränkung der verfügbaren IP-Adressen kann verhindern, dass die verwaltete Domäne zwei Domänencontroller verwaltet.

Das folgende Beispieldiagramm zeigt einen gültigen Entwurf, bei dem die verwaltete Domäne über ein eigenes Subnetz verfügt, ein Gatewaysubnetz für externe Verbindungen vorhanden ist und Anwendungsworkloads in einem verbundenen Subnetz innerhalb des virtuellen Netzwerks liegen:

![Empfohlener Subnetzentwurf](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

## <a name="connections-to-the-azure-ad-ds-virtual-network"></a>Verbindungen mit dem virtuellen Azure AD DS-Netzwerk

Wie im vorherigen Abschnitt erwähnt, können Sie nur eine verwaltete Domäne in einem einzelnen virtuellen Netzwerk in Azure erstellen, und pro Azure AD-Mandant kann nur eine verwaltete Domäne erstellt werden. Auf der Grundlage dieser Architektur müssen Sie möglicherweise ein oder mehrere virtuelle Netzwerke, die Ihre Anwendungsworkloads hosten, mit dem virtuellen Netzwerk der verwalteten Domäne verbinden.

Sie können Anwendungsworkloads, die in anderen virtuellen Azure-Netzwerken gehostet werden, mit einer der folgenden Methoden verbinden:

* Peering in virtuellen Netzwerken
* Virtuelles privates Netzwerk (VPN)

### <a name="virtual-network-peering"></a>Peering in virtuellen Netzwerken

Das Peering virtueller Netzwerke ist ein Mechanismus, der zwei virtuelle Netzwerke in der gleichen Region über das Azure-Backbonenetzwerk miteinander verbindet. Das globale Peering virtueller Netzwerke kann virtuelle Netzwerke über Azure-Regionen hinweg verbinden. Nach dem Peering lassen die beiden virtuellen Netzwerke Ressourcen wie VMs direkt über private IP-Adressen miteinander kommunizieren. Durch Verwendung des Peerings für virtuelle Netzwerke können Sie eine verwaltete Domäne mit Ihren Anwendungsworkloads in anderen virtuellen Netzwerken bereitstellen.

![Verbindung von virtuellen Netzwerken per Peering](./media/active-directory-domain-services-design-guide/vnet-peering.png)

Weitere Informationen finden Sie unter [Übersicht über das Peering virtueller Azure-Netzwerke](../virtual-network/virtual-network-peering-overview.md).

### <a name="virtual-private-networking-vpn"></a>Virtuelle private Netzwerke (Virtual Private Networks, VPNs)

Sie können ein virtuelles Netzwerk mit einem anderen virtuellen Netzwerk (VNet-to-VNet) auf dieselbe Weise verbinden, wie Sie ein virtuelles Netzwerk an einem lokalen Standort konfigurieren können. Beide Verbindungen verwenden ein VPN-Gateway, um mit IPsec/IKE einen sicheren Tunnel zu erstellen. Mit diesem Verbindungsmodell können Sie die verwaltete Domäne in einem virtuellen Azure-Netzwerk einsetzen und dann lokale Standorte oder andere Clouds verbinden.

![Verbindung von virtuellen Netzwerken per VPN Gateway](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

Weitere Informationen zur Verwendung von virtuellen privaten Netzwerken finden Sie unter [Konfigurieren einer VNET-zu-VNET-VPN-Gatewayverbindung über das Azure-Portal](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

## <a name="name-resolution-when-connecting-virtual-networks"></a>Namensauflösung beim Verbinden virtueller Netzwerke

Virtuelle Netzwerke, die mit dem virtuellen Netzwerk der verwalteten Domäne verbunden sind, verfügen in der Regel über eigene DNS-Einstellungen. Wenn Sie virtuelle Netzwerke verbinden, wird die Namensauflösung für das zu verbindende virtuelle Netzwerk nicht automatisch konfiguriert, um Dienste aufzulösen, die von der verwalteten Domäne bereitgestellt werden. Die Namensauflösung in den zu verbindenden virtuellen Netzwerken muss so konfiguriert sein, dass Anwendungsworkloads die verwaltete Domäne finden können.

Sie können die Namensauflösung mit bedingten DNS-Weiterleitungen auf dem DNS-Server aktivieren, der die zu verbindenden virtuellen Netzwerke unterstützt, oder indem Sie dieselben DNS-IP-Adressen aus dem virtuellen Netzwerk der verwalteten Domäne verwenden.

## <a name="network-resources-used-by-azure-ad-ds"></a>Von Azure AD DS verwendete Netzwerkressourcen

Eine verwaltete Domäne erstellt während der Bereitstellung einige Netzwerkressourcen. Diese Ressourcen werden für den erfolgreichen Betrieb und die Verwaltung der verwalteten Domäne benötigt und sollten nicht manuell konfiguriert werden.

| Azure-Ressource                          | BESCHREIBUNG |
|:----------------------------------------|:---|
| Netzwerkschnittstellenkarte                  | Azure AD DS hostet die verwaltete Domäne auf zwei Domänencontrollern (DCs), die auf Windows Server als virtuelle Azure-Computer ausgeführt werden. Jeder virtuelle Computer verfügt über eine virtuelle Netzwerkschnittstelle, die sich mit Ihrem virtuellen Subnetz verbindet. |
| Dynamische öffentliche Standard-IP-Adresse      | Azure AD DS kommuniziert mit dem Synchronisierungs- und Verwaltungsdienst über eine standardmäßige öffentliche SKU-IP-Adresse. Weitere Informationen zu öffentlichen IP-Adressen finden Sie unter [IP-Adresstypen und Zuordnungsmethoden in Azure](../virtual-network/public-ip-addresses.md). |
| Azure Load Balancer Standard            | Azure AD DS verwendet einen SKU-Load Balancer vom Typ „Standard“ für die Netzwerkadressübersetzung (NAT) und den Lastenausgleich (bei Verwendung mit sicherem LDAP). Weitere Informationen zu Azure Load Balancer finden Sie unter [Was versteht man unter Azure Load Balancer?](../load-balancer/load-balancer-overview.md). |
| Regeln für die Netzwerkadressübersetzung (NAT) | Azure AD DS erstellt und verwendet drei NAT-Regeln auf dem Load Balancer – eine Regel für sicheren HTTP-Datenverkehr und zwei Regeln für sicheres PowerShell-Remoting. |
| Lastenausgleichsregeln                     | Wenn eine verwaltete Domäne für sicheres LDAP am TCP-Port 636 konfiguriert ist, werden drei Regeln erstellt und für einen Lastenausgleich verwendet, um den Datenverkehr zu verteilen. |

> [!WARNING]
> Löschen oder ändern Sie keine Netzwerkressourcen, die von Azure AD DS erstellt wurden (z. B. das manuelle Konfigurieren des Lastenausgleichs oder der Regeln). Wenn Sie eine der Netzwerkressourcen löschen oder ändern, kann es zu einem Ausfall von Azure AD DS kommen.

## <a name="network-security-groups-and-required-ports"></a>Netzwerksicherheitsgruppen und erforderliche Ports

Eine [Netzwerksicherheitsgruppe (NSG)](../virtual-network/network-security-groups-overview.md) enthält eine Liste von Regeln, die den Netzwerkdatenverkehr für den Datenverkehr in einem virtuellen Azure-Netzwerk gestatten oder ablehnen. Eine Netzwerksicherheitsgruppe wird erstellt, wenn Sie eine verwaltete Domäne bereitstellen, die einen Satz von Regeln enthält, mit denen der Dienst Authentifizierungs- und Verwaltungsfunktionen bereitstellen kann. Diese standardmäßige Netzwerksicherheitsgruppe ist dem virtuellen Subnetz zugeordnet, in dem Ihre verwaltete Domäne bereitgestellt wird.

Die folgenden Regeln für die Netzwerksicherheitsgruppe sind erforderlich, damit die verwaltete Domäne Authentifizierungs- und Verwaltungsdienste bereitstellen kann. Bearbeiten oder löschen Sie diese Regeln für die Netzwerksicherheitsgruppe nicht für das virtuelle Subnetz, in dem Ihre verwaltete Domäne bereitgestellt wird.

| Portnummer | Protocol | `Source`                             | Destination | Aktion | Erforderlich | Zweck |
|:-----------:|:--------:|:----------------------------------:|:-----------:|:------:|:--------:|:--------|
| 443         | TCP      | AzureActiveDirectoryDomainServices | Any         | Allow  | Ja      | Synchronisierung mit Ihrem Azure AD-Mandanten |
| 3389        | TCP      | CorpNetSaw                         | Any         | Allow  | Ja      | Verwaltung Ihrer Domäne |
| 5986        | TCP      | AzureActiveDirectoryDomainServices | Any         | Allow  | Ja      | Verwaltung Ihrer Domäne |

Ein Azure-Standard-Load Balancer wird erstellt, der diese Regeln erfordert. Diese Netzwerksicherheitsgruppe sichert Azure AD DS. Sie ist erforderlich, damit die verwaltete Domäne ordnungsgemäß funktioniert. Löschen Sie diese Netzwerksicherheitsgruppe nicht. Der Lastenausgleich funktioniert ohne sie nicht ordnungsgemäß.

Falls erforderlich, können Sie [die erforderliche Netzwerksicherheitsgruppe und Regeln mithilfe Azure PowerShell erstellen](powershell-create-instance.md#create-a-network-security-group).

> [!WARNING]
> Bearbeiten Sie diese Netzwerkressourcen und Konfigurationen nicht manuell. Wenn Sie eine falsch konfigurierte Netzwerksicherheitsgruppe oder eine benutzerdefinierte Routingtabelle mit dem Subnetz verknüpfen, in dem die verwaltete Domäne bereitgestellt wird, werden die Möglichkeiten von Microsoft zur Wartung und Verwaltung der Domäne möglicherweise beeinträchtigt. Die Synchronisierung zwischen Ihrem Azure AD-Mandanten und Ihrer verwalteten Domäne wird ebenfalls beeinträchtigt.
>
> Wenn Sie Secure LDAP verwenden, können Sie bei Bedarf eine Regel für den erforderlichen TCP-Port 636 hinzufügen, um externen Datenverkehr zuzulassen. Das Hinzufügen dieser Regel hat nicht zur Folge, dass Ihre NSG-Regeln in einen nicht unterstützten Zustand versetzt werden. Weitere Informationen finden Sie unter [Beschränken des Secure LDAP-Zugriffs über das Internet](tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet).
>
> Standardregeln für *AllowVnetInBound*, *AllowAzureLoadBalancerInBound*, *DenyAllInBound*, *AllowVnetOutBound*, *AllowInternetOutBound* und *DenyAllOutBound* sind auch für die Netzwerksicherheitsgruppe vorhanden. Diese Standardregeln sollten nicht bearbeitet oder gelöscht werden.
>
> Die Azure-SLA gilt nicht für Bereitstellungen, bei denen eine falsch konfigurierte Netzwerksicherheitsgruppe und/oder benutzerdefinierte Routingtabellen angewendet wurden, die verhindern, dass Azure AD DS Ihre Domäne aktualisiert und verwaltet.

### <a name="port-443---synchronization-with-azure-ad"></a>Port 443 (Synchronisierung mit Azure AD)

* Wird zum Synchronisieren Ihres Azure AD-Mandanten mit Ihrer verwalteten Domäne verwendet.
* Ohne Zugriff auf diesen Port kann Ihre verwaltete Domäne nicht mit Ihrem Azure AD-Mandanten synchronisiert werden. Benutzer können sich unter Umständen nicht anmelden, da Kennwortänderungen nicht mit Ihrer verwalteten Domäne synchronisiert werden.
* Der eingehende Zugriff über diesen Port auf IP-Adressen ist standardmäßig durch das Diensttag **AzureActiveDirectoryDomainServices** eingeschränkt.
* Schränken Sie den ausgehenden Zugriff von diesem Port nicht ein.

### <a name="port-3389---management-using-remote-desktop"></a>Port 3389 (Verwaltung über Remotedesktop)

* Wird für Remotedesktopverbindungen mit Domänencontrollern in Ihrer verwalteten Domäne verwendet.
* Die Standardregel für die Netzwerksicherheitsgruppe verwendet das Diensttag *CorpNetSaw*, um den Datenverkehr weiter einzuschränken.
    * Dieses Diensttag gestattet nur sicheren Zugriff auf Arbeitsstationen im Microsoft-Unternehmensnetzwerk, um den Remotedesktop für die verwaltete Domäne zu verwenden.
    * Der Zugriff ist nur mit geschäftlicher Begründung gestattet, z. B. für Verwaltungs- oder Problembehandlungsszenarien.
* Diese Regel kann auf *Ablehnen* festgelegt werden, um dann nur bei Bedarf auf *Zulassen* festgelegt zu werden. Die meisten Verwaltungs- und Überwachungsaufgaben werden mit PowerShell-Remoting durchgeführt. RDP wird nur in dem seltenen Fall verwendet, dass Microsoft zur erweiterten Problembehandlung eine Remoteverbindung mit Ihrer verwalteten Domäne herstellen muss.

> [!NOTE]
> Sie können das Diensttag *CorpNetSaw* nicht manuell über das Portal auswählen, wenn Sie versuchen, diese Regel für Netzwerksicherheitsgruppen zu bearbeiten. Sie müssen Azure PowerShell oder die Azure CLI verwenden, um eine Regel manuell zu konfigurieren, die das Diensttag *CorpNetSaw* verwendet.
>
> Beispielsweise können Sie das folgende Skript verwenden, um eine Regel zu erstellen, die RDP zulässt: 
>
> `Get-AzureRmNetworkSecurityGroup -Name "nsg-name" -ResourceGroupName "resource-group-name" | Add-AzureRmNetworkSecurityRuleConfig -Name "new-rule-name" -Access "Allow" -Protocol "TCP" -Direction "Inbound" -Priority "priority-number" -SourceAddressPrefix "CorpNetSaw" -SourcePortRange "" -DestinationPortRange "3389" -DestinationAddressPrefix "" | Set-AzureRmNetworkSecurityGroup`

### <a name="port-5986---management-using-powershell-remoting"></a>Port 5986 (Verwaltung mit PowerShell-Remoting)

* Wird zum Ausführen von Verwaltungsaufgaben mithilfe von PowerShell-Remoting in Ihrer verwalteten Domäne verwendet.
* Ohne Zugriff auf diesen Port kann Ihre verwaltete Domäne nicht aktualisiert, konfiguriert, gesichert oder überwacht werden.
* Für verwaltete Domänen, die ein Resource Manager-basiertes virtuelles Netzwerk verwenden, können Sie den eingehenden Zugriff auf diesen Port auf das Diensttag *AzureActiveDirectoryDomainServices* beschränken.
    * Für ältere verwaltete Domänen, die ein klassisches virtuelles Netzwerk verwenden, können Sie den eingehenden Zugriff auf diesen Port auf die folgenden Quell-IP-Adressen beschränken: *52.180.183.8*, *23.101.0.70*, *52.225.184.198*, *52.179.126.223*, *13.74.249.156*, *52.187.117.83*, *52.161.13.95*, *104.40.156.18* und *104.40.87.209*.

    > [!NOTE]
    > Seit 2017 können Azure AD Domain Services in einem Azure Resource Manager-Netzwerk gehostet werden. Seitdem konnten wir mithilfe der modernen Möglichkeiten von Azure Resource Manager einen sichereren Dienst schaffen. Da Azure Resource Manager-Bereitstellungen klassische Bereitstellungen vollständig ersetzen, werden die klassischen Bereitstellungen virtueller Netzwerke mit Azure AD DS am 1. März 2023 eingestellt.
    >
    > Weitere Informationen finden Sie im [offiziellen Hinweis zur Einstellung](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/).

## <a name="user-defined-routes"></a>Benutzerdefinierte Routen

Benutzerdefinierte Routen werden standardmäßig nicht erstellt und sind nicht erforderlich, damit Azure AD DS ordnungsgemäß funktioniert. Wenn Sie Routingtabellen verwenden müssen, vermeiden Sie Änderungen an der Route *0.0.0.0*. Änderungen an dieser Route führen zu Unterbrechungen von Azure AD DS und versetzen die verwaltete Domäne in einen nicht unterstützten Status.

Sie müssen auch eingehenden Datenverkehr von den IP-Adressen, die in den jeweiligen Azure-Diensttags enthalten sind, an das Subnetz der verwalteten Domäne weiterleiten. Weitere Informationen zu Diensttags und der zugehörigen IP-Adresse finden Sie unter [IP-Bereiche und Diensttags von Azure – Public Cloud](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

> [!CAUTION]
> Diese IP-Bereiche des Azure-Rechenzentrums können sich ohne vorherige Ankündigung ändern. Stellen Sie sicher, dass Sie über Prozesse zur Überprüfung verfügen, dass Sie über die neuesten IP-Adressen verfügen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu einigen der von Azure AD DS verwendeten Netzwerkressourcen und Verbindungsoptionen finden Sie in den folgenden Artikeln:

* [Peering in virtuellen Azure-Netzwerken](../virtual-network/virtual-network-peering-overview.md)
* [Azure-VPN-Gateways](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)
* [Azure-Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md)