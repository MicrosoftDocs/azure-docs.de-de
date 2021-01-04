---
title: Integration virtueller Netzwerke von Azure-Diensten für die Netzwerkisolation
titlesuffix: Azure Virtual Network
description: In diesem Artikel werden verschiedene Methoden zur Integration eines Azure-Diensts in ein virtuelles Netzwerk beschrieben, die Ihnen den sicheren Zugriff auf den Azure-Dienst ermöglichen.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/01/2020
ms.author: kumud
ms.openlocfilehash: 814158ad68e63760c1f2a1bce3eeff812de3c13e
ms.sourcegitcommit: dea56e0dd919ad4250dde03c11d5406530c21c28
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96938731"
---
# <a name="integrate-azure-services-with-virtual-networks-for-network-isolation"></a>Integrieren von Azure-Diensten mit virtuellen Netzwerken zur Netzwerkisolation

Die VNet-Integration (virtuelles Netzwerk) für einen Azure-Dienst ermöglicht es Ihnen, den Zugriff auf den Dienst nur auf Ihre virtuelle Netzwerkinfrastruktur zu beschränken. Die VNet-Infrastruktur umfasst auch virtuelle Netzwerke mit Peering und lokale Netzwerke.

Die VNet-Integration bietet Azure-Diensten die Vorteile der Netzwerkisolation und kann durch eine oder mehrere der folgenden Methoden erreicht werden:
- [Bereitstellen dedizierter Instanzen des Diensts in einem virtuellen Netzwerk](virtual-network-service-endpoints-overview.md). Auf die Dienste kann dann innerhalb des virtuellen Netzwerks und von lokalen Netzwerken aus privat zugegriffen werden.
- Verwenden von [Privaten Endpunkten](../private-link/private-endpoint-overview.md), die eine private und sichere Verbindung zwischen Ihnen und dem Dienst herstellen, der von [Azure Private Link](../private-link/private-link-overview.md) unterstützt wird. Ein privater Endpunkt verwendet eine private IP-Adresse in Ihrem VNet und bindet den Dienst so effektiv in das virtuelle Netzwerk ein.
- Zugreifen auf den Dienst unter Verwendung öffentlicher Endpunkte, indem ein virtuelles Netzwerk mithilfe von [Dienstendpunkten](virtual-network-service-endpoints-overview.md) auf den Dienst erweitert wird. Durch Dienstendpunkte können Dienstressourcen an das virtuelle Netzwerk gebunden werden.
- Verwenden von [Diensttags](service-tags-overview.md), um Datenverkehr zu Ihren Azure-Ressourcen von und zu öffentlichen IP-Endpunkten zuzulassen oder zu verweigern.

## <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>Bereitstellen von dedizierten Azure-Diensten in virtuellen Netzwerken

Wenn Sie dedizierte Azure-Dienste in einem virtuellen Netzwerk bereitstellen, können Sie über private IP-Adressen privat mit den Dienstressourcen kommunizieren.

![Bereitstellen von dedizierten Azure-Diensten in virtuellen Netzwerken](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Das Bereitstellen eines dedizierten Azure-Diensts in Ihrem virtuellen Netzwerk bietet die folgenden Möglichkeiten:
- Ressourcen im virtuellen Netzwerk können über private IP-Adressen privat miteinander kommunizieren. Beispiel: Direktes Übertragen von Daten zwischen HDInsight und SQL Server auf einem virtuellen Computer im virtuellen Netzwerk.
- Lokale Ressourcen können mit privaten IP-Adressen über Site-to-Site-VPN (VPN Gateway) oder ExpressRoute auf Ressourcen in einem virtuellen Netzwerk zugreifen.
- Für virtuelle Netzwerke kann Peering eingerichtet werden, damit Ressourcen in den virtuellen Netzwerken mithilfe privater IP-Adressen miteinander kommunizieren können.
- Dienstinstanzen in einem virtuellen Netzwerk werden in der Regel vollständig vom Azure-Dienst verwaltet. Dies schließt Überwachung der Integrität der Ressourcen und Skalieren mit Last ein.
- Dienstinstanzen werden in einem Subnetz in einem virtuellen Netzwerk bereitgestellt. Eingehender und ausgehender Netzwerkzugriff auf das Subnetz muss gemäß der Anleitung durch den Dienst über Netzwerksicherheitsgruppen geöffnet sein.
- Bestimmte Dienste verhängen auch Einschränkungen über dem Subnetz, in dem sie bereitgestellt werden, wobei die Anwendung von Richtlinien, Routen oder die Kombination von virtuellen Computern und Dienstressourcen im selben Subnetz eingeschränkt werden. Überprüfen Sie für jeden Dienst die spezifischen Einschränkungen, da sie sich im Laufe der Zeit ändern können. Beispiele für solche Dienste sind Azure NetApp Files, Dedicated HSM, Azure Container Instances, App Service.
- Optional ist für Dienste möglicherweise ein delegiertes Subnetz als expliziter Bezeichner erforderlich, damit ein Subnetz einen bestimmten Diensts hosten kann. Durch Delegieren erhalten Dienste explizite Berechtigungen zum Erstellen dienstspezifischer Ressourcen im delegierten Subnetz.
- Betrachten Sie ein Beispiel für eine REST-API-Antwort in einem virtuellen Netzwerk mit einem delegierten Subnetz. Eine umfassende Liste der Dienste, die das Modell des delegierten Subnetzes verwenden, erhalten Sie über die API für verfügbare Delegierungen.

Eine Liste der Dienste, die in einem virtuellen Netzwerk bereitgestellt werden können, finden Sie unter [Bereitstellen von dedizierten Azure-Diensten in virtuellen Netzwerken](virtual-network-for-azure-services.md).

## <a name="private-link-and-private-endpoints"></a>Private Link und private Endpunkte

Sie können private Endpunkte verwenden, um den Eingang von Ereignissen direkt aus Ihrem virtuellen Netzwerk zur Azure-Ressource sicher über eine private Verbindung zu ermöglichen, ohne das öffentliche Internet zu durchlaufen. Ein privater Endpunkt ist eine spezielle Netzwerkschnittstelle für einen Azure-Dienst in Ihrem virtuellen Netzwerk. Wenn Sie einen privaten Endpunkt für Ihre Azure-Ressource erstellen, wird eine sichere Verbindung zwischen Clients in Ihrem virtuellen Netzwerk und Ihrer Azure-Ressource bereitgestellt. Dem privaten Endpunkt wird eine IP-Adresse aus dem IP-Adressbereich Ihres virtuellen Netzwerks zugewiesen. Für die Verbindung zwischen dem privaten Endpunkt und dem Azure-Dienst wird eine sichere private Verbindung verwendet.

Das folgende Beispiel zeigt den privaten Zugriff auf einen privaten Endpunkt einer Event Grid-Ressource, der eine sichere Verbindung zwischen Clients in einem virtuellen Netzwerk und der Event Grid-Ressource ermöglicht.

![Privater Zugriff auf die SQL-Datenbank-Ressource mithilfe des privaten Endpunkts](./media/network-isolation/architecture-diagram.png)

Weitere Informationen zu Private Link und eine Liste der unterstützten Azure-Dienste finden Sie unter [Was ist Private Link?](../private-link/private-link-overview.md)

## <a name="service-endpoints"></a>Dienstendpunkte
Der VNet-Dienstendpunkt bietet eine sichere und direkte Verbindung mit Azure-Diensten über eine optimierte Route über das Azure-Backbonenetzwerk. Endpunkte ermöglichen es Ihnen, Ihre kritischen Ressourcen von Azure-Diensten auf Ihre virtuellen Netzwerke zu beschränken und so zu schützen. Dienstendpunkte ermöglichen es privaten IP-Adressen im VNet, den Endpunkt eines Azure-Diensts zu erreichen, ohne dass eine öffentliche IP-Adresse im VNet benötigt wird.

![Schützen von Azure-Diensten in virtuellen Netzwerken](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

Weitere Informationen finden Sie unter [Dienstendpunkte im virtuellen Netzwerk](virtual-network-service-endpoints-overview.md).

## <a name="service-tags"></a>Diensttags

Ein Diensttag steht für eine Gruppe von IP-Adresspräfixen eines bestimmten Azure-Diensts. Mithilfe von Diensttags können Sie Netzwerkzugriffssteuerungen in [Netzwerksicherheitsgruppen](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) oder in der [Azure Firewall](https://docs.microsoft.com/azure/firewall/service-tags) definieren. Wenn Sie den Diensttagnamen (beispielsweise „AzureEventGrid“) im entsprechenden Quell- oder Zielfeld einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern.

![Zulassen oder Verweigern von Datenverkehr mithilfe von Diensttags](./media/network-isolation/service-tags.png)

Mithilfe von Diensttags können Sie Netzwerkisolation erreichen und Ihre Azure-Ressourcen vor dem allgemeinen Internet schützen, während Sie auf Azure-Dienste mit öffentlichen Endpunkten zugreifen. Erstellen Sie Regeln für eingehende/ausgehende Netzwerksicherheitsgruppen, um Datenverkehr in das **Internet** und aus diesem zu verweigern und Datenverkehr in die **Azure-Cloud** oder andere [verfügbare Diensttags](service-tags-overview.md#available-service-tags) von bestimmten Azure-Diensten und aus diesen zuzulassen.

Weitere Informationen zu Diensttags und Azure-Diensten, die diese unterstützen, finden Sie unter [Übersicht über Diensttags](service-tags-overview.md).

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie Ihre [App in ein Azure-Netzwerk integrieren können](../app-service/web-sites-integrate-with-vnet.md).
- Erfahren Sie, wie Sie den [Zugriff auf Ressourcen mithilfe von Diensttags einschränken](tutorial-restrict-network-access-to-resources.md) können.
- Erfahren Sie, wie Sie eine [private Verbindung mit einem Azure Cosmos-Konto mithilfe von Azure Private Link herstellen](../private-link/create-private-endpoint-cosmosdb-portal.md).