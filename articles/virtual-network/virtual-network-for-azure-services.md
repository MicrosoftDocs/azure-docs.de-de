---
title: Dienste für virtuelle Netzwerke in Azure
titlesuffix: Azure Virtual Network
description: Erfahren Sie, wie dedizierte Azure-Dienste in einem virtuellen Netzwerk bereitgestellt werden und welche Funktionen durch diese Bereitstellungen ermöglicht werden.
services: virtual-network
documentationcenter: na
author: mohnader
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/06/2020
ms.author: kumud
ms.openlocfilehash: 2ae318f8372175b6e5988c8c07351e80cf3dcbce
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537863"
---
# <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>Bereitstellen von dedizierten Azure-Diensten in virtuellen Netzwerken

Wenn Sie dedizierte Azure-Dienste in einem [virtuellen Netzwerk](virtual-networks-overview.md) bereitstellen, können Sie über private IP-Adressen privat mit den Dienstressourcen kommunizieren.

![In einem virtuellen Netzwerk bereitgestellte Dienste](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Das Bereitstellen von Diensten in einem virtuellen Netzwerk bietet die folgenden Fähigkeiten:

- Ressourcen im virtuellen Netzwerk können über private IP-Adressen privat miteinander kommunizieren. Beispiel: Direktes Übertragen von Daten zwischen HDInsight und SQL Server auf einem virtuellen Computer im virtuellen Netzwerk.
- Lokale Ressourcen können mit privaten IP-Adressen über [Site-to-Site-VPN (VPN Gateway)](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) oder [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) auf Ressourcen in einem virtuellen Netzwerk zugreifen.
- Für virtuelle Netzwerke kann [Peering](virtual-network-peering-overview.md) eingerichtet werden, damit Ressourcen in den virtuellen Netzwerken mithilfe privater IP-Adressen miteinander kommunizieren können.
- Dienstinstanzen in einem virtuellen Netzwerk werden in der Regel vollständig vom Azure-Dienst verwaltet. Dies schließt Überwachung der Integrität der Ressourcen und Skalieren mit Last ein.
- Dienstinstanzen werden in einem Subnetz in einem virtuellen Netzwerk bereitgestellt. Eingehender und ausgehender Netzwerkzugriff auf das Subnetz muss gemäß der Anleitung durch den Dienst über [Netzwerksicherheitsgruppen](./network-security-groups-overview.md#network-security-groups) geöffnet sein.
- Bestimmte Dienste verhängen auch Einschränkungen über dem Subnetz, in dem sie bereitgestellt werden, wobei die Anwendung von Richtlinien, Routen oder die Kombination von virtuellen Computern und Dienstressourcen im selben Subnetz eingeschränkt werden. Überprüfen Sie für jeden Dienst die spezifischen Einschränkungen, da sie sich im Laufe der Zeit ändern können. Beispiele für solche Dienste sind Azure NetApp Files, Dedicated HSM, Azure Container Instances, App Service. 
- Optional ist für Dienste möglicherweise ein [delegiertes Subnetz](virtual-network-manage-subnet.md#add-a-subnet) als expliziter Bezeichner erforderlich, damit ein Subnetz einen bestimmten Diensts hosten kann. Durch Delegieren erhalten Dienste explizite Berechtigungen zum Erstellen dienstspezifischer Ressourcen im delegierten Subnetz.
- Betrachten Sie ein Beispiel für eine REST-API-Antwort in einem [virtuellen Netzwerk mit einem delegierten Subnetz](/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet). Eine umfassende Liste der Dienste, die das Modell des delegierten Subnetzes verwenden, erhalten Sie über die API für [verfügbare Delegierungen](/rest/api/virtualnetwork/availabledelegations/list).

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Dienste, die in einem virtuellen Netzwerk bereitgestellt werden können

|Category|Dienst| Dediziertes<sup>1</sup> Subnetz
|-|-|-|
| Compute | Virtuelle Computer: [Linux](/previous-versions/azure/virtual-machines/linux/infrastructure-example?toc=%2fazure%2fvirtual-network%2ftoc.json) oder [Windows](/previous-versions/azure/virtual-machines/windows/infrastructure-example?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Skalierungsgruppen für virtuelle Computer](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Clouddienst](/previous-versions/azure/reference/jj156091(v=azure.100)): Nur virtuelles Netzwerk (klassisch)<br/> [Azure Batch](../batch/nodes-and-pools.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| Nein <br/> Nein <br/> Nein <br/> Nein<sup>2</sup>
| Netzwerk | [Application Gateway – WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  <br/> [Azure Bastion](../bastion/bastion-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Virtuelle Netzwerkgeräte](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn):| Ja <br/> Ja <br/> Ja <br/> Ja <br/> Nein
|Daten|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Verwaltete Azure SQL-Datenbank-Instanz](../azure-sql/managed-instance/connectivity-architecture-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Ja <br/> Ja <br/> 
|Analytics | [Azure HDInsight](../hdinsight/hdinsight-plan-virtual-network-deployment.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks?toc=%2fazure%2fvirtual-network%2ftoc.json) |Nein<sup>2</sup> <br/> Nein<sup>2</sup> <br/> 
| Identity | [Azure Active Directory-Domänendienste](../active-directory-domain-services/tutorial-create-instance.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Nein <br/>
| Container | [Azure Kubernetes Service (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Container Instances (ACI)](https://www.aka.ms/acivnet)<br/>[Azure Container Service-Engine](https://github.com/Azure/acs-engine) mit Azure Virtual Network-CNI-[Plug-In](https://github.com/Azure/acs-engine/tree/master/examples/vnet)<br/>[Azure-Funktionen](../azure-functions/functions-networking-options.md#virtual-network-integration) |Nein<sup>2</sup><br/> Ja <br/><br/> Nein <br/> Ja
| Web | [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Web-Apps](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[App Service-Umgebung](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Ja <br/> Ja <br/> Ja <br/> Ja
| Gehostet | [Dediziertes HSM von Azure](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Ja <br/> Ja <br/>
| Azure Spring Cloud | [Bereitstellen in einem virtuellen Azure-Netzwerk (VNET-Einschleusung)](../spring-cloud/spring-cloud-tutorial-deploy-in-azure-virtual-network.md)<br/>| Ja <br/>
| | |

<sup>1</sup> „Dediziert“ beinhaltet, dass nur dienstspezifische Ressourcen in diesem Subnetz bereitgestellt und nicht mit VM/VMSSs von Kunden kombiniert werden können. <br/> 
<sup>2</sup> Es wird als bewährte Methode empfohlen, diese Dienste in einem dedizierten Subnetz auszuführen, dies ist jedoch keine zwingende Vorgabe des Diensts.