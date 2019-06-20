---
title: include file
description: include file
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11/01/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: cd3b7d6cc75afc5d83ff02a15b920d9f8b05f608
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66391339"
---
Beim Erstellen eines virtuellen Azure-Computers (VM) müssen Sie ein [virtuelles Netzwerk](../articles/virtual-network/virtual-networks-overview.md) (VNet) erstellen oder ein vorhandenes VNet verwenden. Außerdem müssen Sie entscheiden, wie auf Ihre VMs im VNet zugegriffen werden soll. Es ist wichtig, [vor dem Erstellen von Ressourcen einen Plan aufzustellen](../articles/virtual-network/virtual-network-vnet-plan-design-arm.md) und sicherzustellen, dass Sie die [Einschränkungen von Netzwerkressourcen](../articles/azure-subscription-service-limits.md#networking-limits) verstehen.

In der folgenden Abbildung werden VMs als Webserver und Datenbankserver dargestellt. Alle Gruppen mit VMs werden im VNet separaten Subnetzen zugewiesen.

![Azure Virtual Network](./media/virtual-machines-common-network-overview/vnetoverview.png)

Sie können ein VNet erstellen, bevor Sie eine VM erstellen, oder Sie können dies während einer VM-Erstellung durchführen. Sie erstellen diese Ressourcen, um die Kommunikation mit einer VM zu unterstützen:

- Netzwerkschnittstellen
- IP-Adressen
- Virtuelles Netzwerk und Subnetze

Zusätzlich zu diesen grundlegenden Ressourcen sollten Sie auch die folgenden optionalen Ressourcen berücksichtigen:

- Netzwerksicherheitsgruppen
- Load Balancer 

[!INCLUDE [updated-for-az](./updated-for-az.md)]

## <a name="network-interfaces"></a>Netzwerkschnittstellen

Eine [Netzwerkschnittstelle (NIC)](../articles/virtual-network/virtual-network-network-interface.md) ist die Verbindung zwischen einer VM und einem virtuellen Netzwerk (VNet). Eine VM muss mindestens eine NIC haben. Es können je nach der Größe Ihrer erstellten VM aber auch mehr sein. Informieren Sie sich darüber, wie viele Netzwerkkarten jede VM-Größe für [Windows](../articles/virtual-machines/windows/sizes.md) oder [Linux](../articles/virtual-machines/linux/sizes.md) unterstützt.

Sie können eine VM mit mehreren Netzwerkkarten erstellen und über den Lebenszyklus einer VM hinweg Netzwerkkarten hinzufügen oder entfernen. Bei mehreren Netzwerkkarten kann eine VM eine Verbindung mit verschiedenen Subnetzen herstellen und Datenverkehr über die am besten geeignete Schnittstelle senden oder empfangen. In einer Verfügbarkeitsgruppe können VMs mit bis zu der von der VM-Größe unterstützten Anzahl von Netzwerkschnittstellen vertreten sein. 

Jede NIC, die an eine VM angefügt ist, muss sich an demselben Standort und unter demselben Abonnement wie die VM befinden. Jede NIC muss mit einem VNET verbunden werden, das in derselben Azure-Region und unter demselben Abonnement wie die NIC vorhanden ist. Sie können das Subnetz, mit dem eine VM verbunden ist, nach der Erstellung ändern, aber eine Änderung des VNet ist nicht möglich. Jeder NIC, die an eine VM angefügt ist, wird eine MAC-Adresse zugewiesen, die sich nicht ändert, bis die VM gelöscht wird.

In dieser Tabelle sind die Methoden aufgeführt, die Sie zum Erstellen einer Netzwerkschnittstelle verwenden können.

| Methode | BESCHREIBUNG |
| ------ | ----------- |
| Azure-Portal | Beim Erstellen einer VM im Azure-Portal wird automatisch eine Netzwerkschnittstelle für Sie erstellt (die Verwendung einer separat erstellten NIC ist nicht möglich). Das Portal erstellt eine VM mit nur einer NIC. Falls Sie eine VM mit mehr als einer NIC erstellen möchten, müssen Sie eine andere Methode verwenden. |
| [Azure PowerShell](../articles/virtual-machines/windows/multiple-nics.md) | Verwenden Sie [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) mit dem Parameter **-PublicIpAddressId**, um den Bezeichner der zuvor erstellten öffentlichen IP-Adresse anzugeben. |
| [Azure-Befehlszeilenschnittstelle](../articles/virtual-machines/linux/multiple-nics.md) | Verwenden Sie zum Angeben des Bezeichners der zuvor erstellten öffentlichen IP-Adresse [az network nic create](https://docs.microsoft.com/cli/azure/network/nic) mit dem Parameter **--public-ip-address**. |
| [Vorlage](../articles/virtual-network/template-samples.md) | Verwenden Sie [Network Interface in a Virtual Network with Public IP Address](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet) (Netzwerkschnittstelle in einem virtuellen Netzwerk mit öffentlicher IP-Adresse) als Anleitung zum Bereitstellen einer Netzwerkschnittstelle mit einer Vorlage. |

## <a name="ip-addresses"></a>IP-Adressen 

Sie können diese Arten von [IP-Adressen](../articles/virtual-network/virtual-network-ip-addresses-overview-arm.md) einer NIC in Azure zuweisen:

- **Öffentliche IP-Adressen**: Werden für die eingehende und ausgehende Kommunikation (ohne Netzwerkadressübersetzung (NAT)) mit dem Internet und anderen Azure-Ressourcen verwendet, die nicht mit einem VNet verbunden sind. Das Zuweisen einer öffentlichen IP-Adresse zu einer NIC ist optional. Für öffentliche IP-Adressen wird eine geringe Gebühr berechnet, und es kann nur eine bestimmte Anzahl dieser IP-Adressen pro Abonnement verwendet werden.
- **Private IP-Adressen**: Werden für die Kommunikation in einem VNet, in Ihrem lokalen Netzwerk und mit dem Internet (mit NAT) verwendet. Sie müssen einer VM mindestens eine private IP-Adresse zuweisen. Weitere Informationen zu NAT in Azure finden Sie unter [Grundlegendes zu ausgehenden Verbindungen in Azure](../articles/load-balancer/load-balancer-outbound-connections.md).

Sie können öffentliche IP-Adressen VMs oder Lastenausgleichsmodulen mit Internetzugriff zuweisen. Sie können private IP-Adressen VMs und internen Lastenausgleichsmodulen zuweisen. Die Zuweisung von IP-Adressen zu einer VM erfolgt über eine Netzwerkschnittstelle.

Es gibt zwei Methoden zum Zuordnen einer IP-Adresse zu einer Ressource: dynamisch oder statisch. Die Standardmethode für die Zuordnung ist dynamisch. Bei diesem Verfahren wird eine IP-Adresse nicht zum Zeitpunkt ihrer Erstellung zugewiesen. Stattdessen wird die IP-Adresse zugeordnet, wenn Sie eine VM erstellen oder eine beendete VM starten. Die IP-Adresse wird freigegeben, wenn Sie die VM beenden oder löschen. 

Damit die IP-Adresse für die VM unverändert bleibt, können Sie die Zuordnungsmethode explizit auf „Statisch“ festlegen. In diesem Fall wird sofort eine IP-Adresse zugewiesen. Sie wird nur freigegeben, wenn Sie die VM löschen oder deren Zuordnungsmethode in „Dynamisch“ ändern.
    
In dieser Tabelle sind die Methoden aufgeführt, die Sie zum Erstellen einer IP-Adresse verwenden können.

| Methode | BESCHREIBUNG |
| ------ | ----------- |
| [Azure-Portal](../articles/virtual-network/virtual-network-deploy-static-pip-arm-portal.md) | Öffentliche IP-Adressen sind standardmäßig dynamisch. Die ihnen zugewiesene Adresse kann sich daher ändern, wenn die VM beendet oder gelöscht wird. Erstellen Sie eine statische öffentliche IP-Adresse, um sicherzustellen, dass für die VM immer die gleiche öffentliche IP-Adresse verwendet wird. Standardmäßig wird einer NIC beim Erstellen einer VM vom Portal eine dynamische private IP-Adresse zugewiesen. Sie können diese IP-Adresse in „Statisch“ ändern, nachdem die VM erstellt wurde.|
| [Azure PowerShell](../articles/virtual-network/virtual-network-deploy-static-pip-arm-ps.md) | Sie verwenden [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) mit dem Parameter **-AllocationMethod** mit dem Wert „Dynamic“ oder „Static“. |
| [Azure-Befehlszeilenschnittstelle](../articles/virtual-network/virtual-network-deploy-static-pip-arm-cli.md) | Sie verwenden [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) mit dem Parameter **--allocation-method** und dem Wert „Dynamic“ oder „Static“. |
| [Vorlage](../articles/virtual-network/template-samples.md) | Verwenden Sie [Network Interface in a Virtual Network with Public IP Address](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet) (Netzwerkschnittstelle in einem virtuellen Netzwerk mit öffentlicher IP-Adresse) als Anleitung zum Bereitstellen einer öffentlichen IP-Adresse mit einer Vorlage. |

Nach dem Erstellen einer öffentlichen IP-Adresse können Sie diese einer VM zuordnen, indem Sie sie einer NIC zuweisen.

## <a name="virtual-network-and-subnets"></a>Virtuelles Netzwerk und Subnetze

Ein Subnetz ist ein Bereich von IP-Adressen im VNet. Sie können ein VNet aus Organisations- und Sicherheitsgründen in mehrere Subnetze unterteilen. Jede NIC einer VM ist mit einem Subnetz im VNet verbunden. NICs, die mit (gleichen oder anderen) Subnetzen in einem VNet verbunden sind, können ohne zusätzliche Konfiguration miteinander kommunizieren.

Beim Einrichten eines VNet geben Sie die Topologie an, einschließlich der verfügbaren Adressräume und Subnetze. Wenn das VNet mit anderen VNets oder lokalen Netzwerken verbunden werden soll, müssen Sie Adressbereiche auswählen, die sich nicht überlappen. Die IP-Adressen sind privat und nicht über das Internet zugänglich. Dies galt bisher nur für die nicht routingfähigen IP-Adressen, z.B. 10.0.0.0/8, 172.16.0.0/12 oder 192.168.0.0/16. Jetzt werden alle Adressbereiche von Azure als Teil des privaten VNet-IP-Adressraums behandelt, der nur innerhalb des VNets, innerhalb von miteinander verbundenen VNets und von Ihrem lokalen Standort aus erreichbar ist. 

Wenn Sie in einer Organisation arbeiten, in der eine andere Person als Sie für die internen Netzwerke zuständig ist, sollten Sie sich mit dieser Person in Verbindung setzen, bevor Sie den Adressraum auswählen. Stellen Sie sicher, dass es nicht zu Überlappungen kommt, und informieren Sie die Person über den gewünschten Adressraum, damit dieser Bereich von IP-Adressen nur einmal verwendet wird. 

Standardmäßig besteht keine Sicherheitsgrenze zwischen Subnetzen, sodass VMs in diesen Subnetzen miteinander kommunizieren können. Sie können aber Netzwerksicherheitsgruppen (NSGs) einrichten, mit denen Sie den Datenverkehrsfluss für Subnetze und VMs in eingehender und ausgehender Richtung steuern können. 

In dieser Tabelle sind die Methoden aufgeführt, die Sie zum Erstellen eines VNet und von Subnetzen verwenden können. 

| Methode | BESCHREIBUNG |
| ------ | ----------- |
| [Azure-Portal](../articles/virtual-network/quick-create-portal.md) | Wenn Sie bei der Erstellung einer VM zulassen, dass von Azure ein VNet erstellt wird, ist der Name eine Kombination aus dem Namen der Ressourcengruppe, die das VNet enthält, und dem Zusatz **-vnet**. Der Adressraum ist 10.0.0.0/24, der erforderliche Subnetzname lautet **default**, und der Adressbereich des Subnetzes ist 10.0.0.0/24. |
| [Azure PowerShell](../articles/virtual-network/quick-create-powershell.md) | Sie verwenden [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworkSubnetConfig) und [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork), um ein Subnetz und ein VNET zu erstellen. Sie können auch [Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/Az.Network/Add-AzVirtualNetworkSubnetConfig) verwenden, um ein Subnetz in einem vorhandenen VNET hinzuzufügen. |
| [Azure-Befehlszeilenschnittstelle](../articles/virtual-network/quick-create-cli.md) | Das Subnetz und das VNet werden gleichzeitig erstellt. Geben Sie für [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) den Parameter **--subnet-name** mit dem Subnetznamen an. |
| Vorlage | Die einfachste Möglichkeit zum Erstellen eines VNet und von Subnetzen ist das Herunterladen einer vorhandenen Vorlage, z.B. [Virtual Network with two Subnets](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets) (Virtuelles Netzwerk mit zwei Subnetzen), und die anschließende Anpassung gemäß Ihren Anforderungen. |

## <a name="network-security-groups"></a>Netzwerksicherheitsgruppen

Eine [Netzwerksicherheitsgruppe (NSG)](../articles/virtual-network/virtual-network-vnet-plan-design-arm.md) enthält eine Liste mit ACL-Regeln (Access Control List, Zugriffssteuerungsliste) zum Zulassen oder Verweigern von Netzwerkdatenverkehr an Subnetze, NICs oder beides. NSGs können entweder Subnetzen oder einzelnen NICs zugeordnet werden, die mit einem Subnetz verbunden sind. Wenn eine NSG einem Subnetz zugeordnet ist, gelten die ACL-Regeln für alle VMs in diesem Subnetz. Darüber hinaus kann Datenverkehr zu einer einzelnen NIC beschränkt werden, indem eine NSG direkt einer NIC zugewiesen wird.

NGSs enthalten zwei Regelsätze: eingehende und ausgehende Regeln. Die Priorität für eine Regel muss innerhalb jedes Satzes eindeutig sein. Jede Regel verfügt über Eigenschaften für Protokoll, Quell- und Zielportbereiche, Adresspräfixe, Richtung des Datenverkehrs, Priorität und Zugriffstyp. 

Alle NSGs enthalten eine Gruppe von Standardregeln. Die Standardregeln können zwar nicht gelöscht werden, haben aber niedrigste Priorität und können somit durch selbst erstellte Regeln außer Kraft gesetzt werden. 

Wenn Sie eine NSG zu einer Netzwerkkarte zuordnen, werden die Netzwerkzugriffsregeln in der NSG nur auf diese Netzwerkkarte angewendet. Wenn eine NSG auf eine einzelne NIC auf einer VM mit mehreren NICs angewendet wird, hat dies keine Auswirkung auf den Datenverkehr zu den anderen NICs. Sie können verschiedene NSGs einer NIC (oder einer VM, je nach Bereitstellungsmodell) und dem Subnetz zuordnen, an das eine Netzwerkkarte oder ein virtueller Computer gebunden ist. Die Priorität richtet sich nach der Richtung des Datenverkehrs.

[Planen](../articles/virtual-network/virtual-network-vnet-plan-design-arm.md) Sie beim Planen von VMs und des VNet auch die NSGs.

In dieser Tabelle sind die Methoden aufgeführt, die Sie zum Erstellen einer Netzwerksicherheitsgruppe verwenden können.

| Methode | BESCHREIBUNG |
| ------ | ----------- |
| [Azure-Portal](../articles/virtual-network/tutorial-filter-network-traffic.md) | Wenn Sie im Azure-Portal eine VM erstellen, wird automatisch eine NSG erstellt und der vom Portal erstellten NIC zugeordnet. Der Name der NSG ist eine Kombination aus dem Namen der VM und dem Zusatz **-nsg**. Diese NSG enthält eine Regel für eingehenden Datenverkehr mit einer Priorität von 1000, RDP als Dienst, TCP als Protokoll, 3389 als Port und der Einstellung „Allow“ als Aktion. Wenn Sie anderen eingehenden Datenverkehr für die VM zulassen möchten, müssen Sie der NSG zusätzliche Regeln hinzufügen. |
| [Azure PowerShell](../articles/virtual-network/tutorial-filter-network-traffic.md) | Verwenden Sie [New-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig), und geben Sie die erforderlichen Informationen für die Regel an. Verwenden Sie [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup), um die NSG zu erstellen. Verwenden Sie [Set-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworksubnetconfig), um die NSG für das Subnetz zu konfigurieren. Verwenden Sie [Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork), um die NSG im VNET hinzuzufügen. |
| [Azure-Befehlszeilenschnittstelle](../articles/virtual-network/tutorial-filter-network-traffic-cli.md) | Verwenden Sie [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg) für die erste Erstellung der NSG. Verwenden Sie [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule), um der NSG Regeln hinzuzufügen. Verwenden Sie [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet), um die NSG dem Subnetz hinzuzufügen. |
| [Vorlage](../articles/virtual-network/template-samples.md) | Verwenden Sie [Create a Network Security Group](https://github.com/Azure/azure-quickstart-templates/tree/master/101-security-group-create) (Erstellen einer Netzwerksicherheitsgruppe) als Anleitung zum Bereitstellen einer Netzwerksicherheitsgruppe mit einer Vorlage. |

## <a name="load-balancers"></a>Load Balancer

Der [Azure Load Balancer](../articles/load-balancer/load-balancer-overview.md) bietet Hochverfügbarkeit und Netzwerkleistung für Ihre Anwendungen. Ein Load Balancer (Lastenausgleich) kann konfiguriert werden, um [eingehenden Internet-Datenverkehr für VMs auszugleichen](../articles/load-balancer/load-balancer-internet-overview.md) oder [Datenverkehr zwischen VMs in einem VNet auszugleichen](../articles/load-balancer/load-balancer-internal-overview.md). Ein Lastenausgleich kann außerdem den Datenverkehr zwischen lokalen Computern und VMs in einem standortübergreifenden Netzwerk ausgleichen oder externen Datenverkehr an eine bestimmte VM weiterleiten.

Der Lastenausgleich ordnet eingehenden und ausgehenden Datenverkehr zwischen der öffentlichen IP-Adresse und dem Port auf dem Lastenausgleichsmodul und der privaten IP-Adresse und dem Port der VM zu.

Beim Erstellen eines Lastenausgleichs müssen Sie auch die folgenden Konfigurationselemente berücksichtigen:

- **Front-End-IP-Konfiguration** – Ein Lastenausgleichsmodul kann eine oder mehrere Front-End-IP-Adressen umfassen, die auch als virtuelle IPs (VIPs) bezeichnet werden. Diese IP-Adressen dienen als Eingang für den Datenverkehr.
- **Back-End-Adresspool**: IP-Adressen, die der NIC zugeordnet sind, auf die die Last verteilt wird.
- **NAT-Regeln**: Definieren, wie eingehender Datenverkehr über die Front-End-IP verläuft und an die Back-End-IP verteilt wird.
- **Lastenausgleichsregeln**: Dienen zum Zuordnen einer bestimmten Front-End-IP-/Port-Kombination zu einer Back-End-IP-Adressen-/Port-Kombination. Ein einzelnes Lastenausgleichsmodul kann mehrere Lastenausgleichsregeln umfassen. Jede Regel ist eine Kombination aus der Front-End-IP und dem Front-End-Port sowie der Back-End-IP und dem Back-End-Port von virtuellen Computern.
- **[Tests](../articles/load-balancer/load-balancer-custom-probe-overview.md)** : Dienen zum Überwachen der Integrität von VMs. Wenn ein Test nicht reagiert, beendet der Lastenausgleich das Senden neuer Verbindungen an die fehlerhafte VM. Die vorhandenen Verbindungen sind nicht betroffen, und neue Verbindungen werden an fehlerfreie VMs gesendet.

In dieser Tabelle sind die Methoden aufgeführt, die Sie zum Erstellen eines Lastenausgleichs für den Internetzugriff verwenden können.

| Methode | BESCHREIBUNG |
| ------ | ----------- |
| Azure-Portal |  Sie können [einen Lastausgleich für den Internetdatenverkehr an virtuelle Computer mit dem Azure-Portal vornehmen](../articles/load-balancer/tutorial-load-balancer-standard-manage-portal.md). |
| [Azure PowerShell](../articles/load-balancer/load-balancer-get-started-internet-arm-ps.md) | Verwenden Sie zum Angeben des Bezeichners der zuvor erstellten öffentlichen IP-Adresse [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) mit dem Parameter **-PublicIpAddress**. Verwenden Sie [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig), um die Konfiguration des Back-End-Adresspools zu erstellen. Verwenden Sie [New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig), um NAT-Regeln für den eingehenden Datenverkehr zu erstellen, die der von Ihnen erstellten Front-End-IP-Konfiguration zugeordnet sind. Verwenden Sie [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig), um die erforderlichen Tests zu erstellen. Verwenden Sie [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig), um die Konfiguration für den Lastenausgleich zu erstellen. Verwenden Sie [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer), um den Lastenausgleich zu erstellen.|
| [Azure-Befehlszeilenschnittstelle](../articles/load-balancer/load-balancer-get-started-internet-arm-cli.md) | Verwenden Sie [az network lb create](https://docs.microsoft.com/cli/azure/network/lb), um die erste Konfiguration für den Lastenausgleich zu erstellen. Verwenden Sie [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip), um die zuvor erstellte öffentliche IP-Adresse hinzuzufügen. Verwenden Sie [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool), um die Konfiguration des Back-End-Adresspools hinzuzufügen. Verwenden Sie [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule), um NAT-Regeln hinzuzufügen. Verwenden Sie [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule), um die Lastenausgleichsregeln hinzuzufügen. Verwenden Sie [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe), um die Tests hinzuzufügen. |
| [Vorlage](../articles/load-balancer/load-balancer-get-started-internet-arm-template.md) | Verwenden Sie [2 VMs in a Load Balancer and configure NAT rules on the LB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-natrules) (Zwei VMs in einem Load Balancer und Konfiguration von NAT-Regeln für den LB) als Anleitung zum Bereitstellen eines Lastenausgleichs mit einer Vorlage. |
    
In dieser Tabelle sind die Methoden aufgeführt, die Sie zum Erstellen eines internen Lastenausgleichs verwenden können.

| Methode | BESCHREIBUNG |
| ------ | ----------- |
| Azure-Portal | Sie können die [interne Datenverkehrslast mithilfe eines Lastenausgleichs im Tarif „Basic“ über das Azure-Portal ausgleichen](../articles/load-balancer/tutorial-load-balancer-basic-internal-portal.md). |
| [Azure PowerShell](../articles/load-balancer/load-balancer-get-started-ilb-arm-ps.md) | Verwenden Sie zum Angeben einer privaten IP-Adresse im Netzwerksubnetz [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) mit dem Parameter **-PrivateIpAddress**. Verwenden Sie [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig), um die Konfiguration des Back-End-Adresspools zu erstellen. Verwenden Sie [New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig), um NAT-Regeln für den eingehenden Datenverkehr zu erstellen, die der von Ihnen erstellten Front-End-IP-Konfiguration zugeordnet sind. Verwenden Sie [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig), um die erforderlichen Tests zu erstellen. Verwenden Sie [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig), um die Konfiguration für den Lastenausgleich zu erstellen. Verwenden Sie [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer), um den Lastenausgleich zu erstellen.|
| [Azure-Befehlszeilenschnittstelle](../articles/load-balancer/load-balancer-get-started-ilb-arm-cli.md) | Verwenden Sie den Befehl [az network lb create](https://docs.microsoft.com/cli/azure/network/lb), um die erste Konfiguration für den Lastenausgleich zu erstellen. Verwenden Sie zum Definieren der privaten IP-Adresse [az network lb frontend-ip create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip) mit dem Parameter **--private-ip-address**. Verwenden Sie [az network lb address-pool create](https://docs.microsoft.com/cli/azure/network/lb/address-pool), um die Konfiguration des Back-End-Adresspools hinzuzufügen. Verwenden Sie [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule), um NAT-Regeln hinzuzufügen. Verwenden Sie [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule), um die Lastenausgleichsregeln hinzuzufügen. Verwenden Sie [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe), um die Tests hinzuzufügen.|
| [Vorlage](../articles/load-balancer/load-balancer-get-started-ilb-arm-template.md) | Verwenden Sie [2 VMs in a Load Balancer and configure NAT rules on the LB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer) (Zwei VMs in einem Load Balancer und Konfiguration von NAT-Regeln für den LB) als Anleitung zum Bereitstellen eines Lastenausgleichs mit einer Vorlage. |

## <a name="vms"></a>VMs

VMs können in demselben VNet erstellt werden, und die Verbindung dazwischen kann über private IP-Adressen hergestellt werden. Das Herstellen der Verbindung ist auch dann möglich, wenn sie sich in unterschiedlichen Subnetzen befinden, ohne dass Gateways konfiguriert oder öffentliche IP-Adressen verwendet werden müssen. Zum Einfügen von VMs in ein VNet erstellen Sie das VNet und weisen es dann beim Erstellen der einzelnen VMs dem VNet und dem Subnetz zu. VMs beschaffen sich ihre Netzwerkeinstellungen während der Bereitstellung oder des Startvorgangs.  

VMs wird eine IP-Adresse zugewiesen, wenn sie bereitgestellt werden. Falls Sie mehrere VMs in einem VNet oder Subnetz bereitstellen, werden dafür beim Starten IP-Adressen zugewiesen. Sie können auch eine statische IP einer VM zuordnen. Erwägen Sie beim Zuordnen einer statischen IP die Verwendung eines spezifischen Subnetzes, um zu verhindern, dass eine statische IP versehentlich für eine andere VM wiederverwendet wird.  

Wenn Sie eine VM erstellen und diese später in ein VNet migrieren möchten, ist dies keine einfache Änderung der Konfiguration. Sie müssen die VM im VNet neu bereitstellen. Die einfachste Möglichkeit zur erneuten Bereitstellung ist das Löschen der VM, ohne dass aber die daran angefügten Datenträger gelöscht werden. Anschließend wird die VM mit den Originaldatenträgern im VNet neu erstellt. 

In dieser Tabelle sind die Methoden aufgeführt, die Sie zum Erstellen einer VM in einem VNet verwenden können.

| Methode | BESCHREIBUNG |
| ------ | ----------- |
| [Azure-Portal](../articles/virtual-machines/windows/quick-create-portal.md) | Es werden die bereits erwähnten Standard-Netzwerkeinstellungen verwendet, um eine VM mit einer einzelnen NIC zu erstellen. Zum Erstellen einer VM mit mehreren NICs müssen Sie eine andere Methode verwenden. |
| [Azure PowerShell](../articles/virtual-machines/windows/tutorial-manage-vm.md) | Umfasst die Verwendung von [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) zum Hinzufügen der zuvor erstellten NIC zur VM-Konfiguration. |
| [Azure-Befehlszeilenschnittstelle](../articles/virtual-machines/linux/create-cli-complete.md) | Erstellen Sie eine VM, und stellen Sie dafür eine Verbindung mit einem VNet, einem Subnetz und einer Netzwerkkarte her, indem Sie die einzelnen erforderlichen Schritte ausführen. |
| [Vorlage](../articles/virtual-machines/windows/ps-template.md) | Verwenden Sie [Very simple deployment of a Windows VM](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) (Sehr einfache Bereitstellung einer Windows-VM) als Anleitung zum Bereitstellen einer VM mit einer Vorlage. |

## <a name="next-steps"></a>Nächste Schritte
Informationen zu den VM-spezifischen Schritten zum Verwalten von virtuellen Azure-Netzwerken für VMs finden Sie in den Tutorials für [Windows](../articles/virtual-machines/windows/tutorial-virtual-network.md) oder [Linux](../articles/virtual-machines/linux/tutorial-virtual-network.md).

Es gibt auch Tutorials dazu, wie Sie den Lastenausgleich für VMs durchführen und hoch verfügbare Anwendungen für [Windows](../articles/virtual-machines/windows/tutorial-load-balancer.md) oder [Linux](../articles/virtual-machines/linux/tutorial-load-balancer.md) erstellen.

- Informieren Sie sich, wie Sie [benutzerdefinierte Routen und die IP-Weiterleitung](../articles/virtual-network/virtual-networks-udr-overview.md) konfigurieren. 
- Informieren Sie sich, wie Sie [VNet-zu-VNet-Verbindungen](../articles/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) konfigurieren.
- Lesen Sie die Informationen zur [Problembehandlung bei Routen](../articles/virtual-network/diagnose-network-routing-problem.md).
