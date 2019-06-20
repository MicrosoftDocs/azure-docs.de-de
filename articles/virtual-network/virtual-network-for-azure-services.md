---
title: Dienste für virtuelle Netzwerke in Azure
titlesuffix: Azure Virtual Network
description: Informieren Sie sich über die Vorteile der Bereitstellung von Ressourcen in einem virtuellen Netzwerk. Ressourcen in virtuellen Netzwerken können miteinander und mit lokalen Ressourcen kommunizieren, ohne das Aufkommen von Datenverkehr im Internet.
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: malop;kumud
ms.openlocfilehash: e5481b0e262021e28a398b72b5ad022673947609
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65409503"
---
# <a name="virtual-network-integration-for-azure-services"></a>Integration virtueller Netzwerke für Azure-Dienste

Wenn Sie Azure-Dienste in ein virtuelles Azure-Netzwerk integrieren, ermöglichen Sie damit den privaten Zugriff auf den Dienst über virtuelle Computer oder Computeressourcen im virtuellen Netzwerk.
Sie können Azure-Dienste mit den folgenden Optionen in Ihr virtuelles Netzwerk integrieren:
- Stellen Sie dedizierte Instanzen des Diensts in einem virtuellen Netzwerk bereit. Auf die Dienste kann dann innerhalb des virtuellen Netzwerks und von lokalen Netzwerken aus privat zugegriffen werden.
- Erweitern Sie ein virtuelles Netzwerk über Dienstendpunkte auf den Dienst. Durch Dienstendpunkte können einzelne Dienstressourcen im virtuellen Netzwerk gesichert werden.

Um mehrere Azure-Dienste in Ihr virtuelles Netzwerk zu integrieren, können Sie eines der oben genannten Muster verwenden oder mehrere Muster kombinieren. Sie können z.B. HDInsight im virtuellen Netzwerk bereitstellen und ein Speicherkonto über Dienstendpunkte im HDInsight-Subnetz sichern.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Bereitstellen von Azure-Diensten in virtuellen Netzwerken

Wenn Sie dedizierte Azure-Dienste in einem [virtuellen Netzwerk](virtual-networks-overview.md) bereitstellen, können Sie über private IP-Adressen privat mit den Dienstressourcen kommunizieren.

![In einem virtuellen Netzwerk bereitgestellte Dienste](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Das Bereitstellen von Diensten in einem virtuellen Netzwerk bietet die folgenden Fähigkeiten:

- Ressourcen im virtuellen Netzwerk können über private IP-Adressen privat miteinander kommunizieren. Beispiel: Direktes Übertragen von Daten zwischen HDInsight und SQL Server auf einem virtuellen Computer im virtuellen Netzwerk.
- Lokale Ressourcen können mit privaten IP-Adressen über [Site-to-Site-VPN (VPN Gateway)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) oder [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) auf Ressourcen in einem virtuellen Netzwerk zugreifen.
- Für virtuelle Netzwerke kann [Peering](virtual-network-peering-overview.md) eingerichtet werden, damit Ressourcen in den virtuellen Netzwerken mithilfe privater IP-Adressen miteinander kommunizieren können.
- Dienstinstanzen in einem virtuellen Netzwerk werden in der Regel vollständig vom Azure-Dienst verwaltet. Dies schließt Überwachung der Integrität der Ressourcen und Skalieren mit Last ein.
- Dienstinstanzen werden in einem Subnetz in einem virtuellen Netzwerk bereitgestellt. Eingehender und ausgehender Netzwerkzugriff auf das Subnetz muss gemäß der Anleitung durch den Dienst über [Netzwerksicherheitsgruppen](security-overview.md#network-security-groups) geöffnet sein.
- Bestimmte Dienste verhängen auch Einschränkungen über dem Subnetz, in dem sie bereitgestellt werden, wobei die Anwendung von Richtlinien, Routen oder die Kombination von virtuellen Computern und Dienstressourcen im selben Subnetz eingeschränkt werden. Überprüfen Sie für jeden Dienst die spezifischen Einschränkungen, da sie sich im Laufe der Zeit ändern können. Beispiele für solche Dienste sind Azure NetApp Files, Dedicated HSM, Azure Container Instances, App Service. 
- Optional ist für Dienste möglicherweise ein [delegiertes Subnetz](virtual-network-manage-subnet.md#add-a-subnet) als expliziter Bezeichner erforderlich, damit ein Subnetz einen bestimmten Diensts hosten kann. Durch Delegieren erhalten Dienste explizite Berechtigungen zum Erstellen dienstspezifischer Ressourcen im delegierten Subnetz.
- Betrachten Sie ein Beispiel für eine REST-API-Antwort in einem [virtuellen Netzwerk mit einem delegierten Subnetz](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get_virtual_network_with_a_delegated_subnet). Eine umfassende Liste der Dienste, die das Modell des delegierten Subnetzes verwenden, erhalten Sie über die API für [verfügbare Delegierungen](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list).

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Dienste, die in einem virtuellen Netzwerk bereitgestellt werden können

|Category (Kategorie)|Dienst| Dediziertes¹ Subnetz
|-|-|-|
| Compute | Virtuelle Computer: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) oder [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Skalierungsgruppen für virtuelle Computer](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Clouddienst](https://msdn.microsoft.com/library/azure/jj156091): Nur virtuelles Netzwerk (klassisch)<br/> [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| Nein <br/> Nein <br/> Nein <br/> Nein²
| Netzwerk | [Application Gateway – WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Virtuelle Netzwerkgeräte](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn): | Ja <br/> Ja <br/> Ja <br/> Nein
|Daten|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Verwaltete Azure SQL-Datenbank-Instanz](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Ja <br/> Ja <br/> 
|Analytics | [Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Nein² <br/> Nein² <br/> 
| Identity | [Azure Active Directory-Domänendienste](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Nein <br/>
| Container | [Azure Kubernetes Service (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Container Instances (ACI)](https://www.aka.ms/acivnet)<br/>[Azure Container Service-Engine](https://github.com/Azure/acs-engine) mit Azure Virtual Network-CNI-[Plug-In](https://github.com/Azure/acs-engine/tree/master/examples/vnet)|Nein²<br/> Ja <br/><br/> Nein
| Web | [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[App Service-Umgebung](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Ja <br/> Ja <br/> Ja
| Gehostet | [Dediziertes HSM von Azure](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Ja <br/> Ja <br/>
| | |

¹ „Dediziert“ beinhaltet, dass nur dienstspezifische Ressourcen in diesem Subnetz bereitgestellt und nicht mit VM/VMSSs von Kunden kombiniert werden können. <br/> ² Empfohlen, aber nicht zwingend erforderlich vom Dienst vorgeschrieben.


## <a name="service-endpoints-for-azure-services"></a>Dienstendpunkte für Azure-Dienste

Einige Azure-Dienste können nicht in virtuellen Netzwerken bereitgestellt werden. Sie können ggf. den Zugriff auf einige der Dienstressourcen auf bestimmte Subnetze des virtuellen Netzwerks beschränken, indem Sie einen VNET-Dienstendpunkt aktivieren.  Erfahren Sie mehr über [VNET-Dienstendpunkte](virtual-network-service-endpoints-overview.md) und die Dienste, für die Endpunkte aktiviert werden können.
