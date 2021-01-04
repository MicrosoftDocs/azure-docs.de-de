---
title: 'Konzepte: API Management'
description: Hier erfahren Sie, wie auf virtuellen AVS-Computern (Azure VMware Solution) ausgeführte APIs durch API Management geschützt werden.
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 958cc52c48d1121a69dca2fc901289ad1ed671cb
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541962"
---
# <a name="api-management-to-publish-and-protect-apis-running-on-azure-vmware-solution-based-vms"></a>API Management zum Veröffentlichen und Schützen von APIs, die auf virtuellen Azure VMware Solution-basierten Computern ausgeführt werden

Microsoft Azure [API Management](https://azure.microsoft.com/services/api-management/) ermöglicht Ihnen die sichere Veröffentlichung für interne oder externe Consumer.  Die Azure Virtual Network-Integration für die Veröffentlichung von APIs, die in Azure VMware Solution-Workloads ausgeführt werden, steht nur in der Developer- und in der Premium-SKU zur Verfügung.  Diese beiden SKUs ermöglichen eine sichere Konnektivität zwischen dem API Management-Dienst und dem Back-End. 

>[!NOTE]
>Die Developer-SKU ist für Entwicklungs- und Testzwecke vorgesehen, die Premium-SKU für Produktionsbereitstellungen.

Die API Management-Konfiguration ist für Back-End-Dienste identisch, die auf Azure VMware Solution-VMs (virtuelle Computer) und lokal ausgeführt werden. Von API Management wird bei beiden Bereitstellungen die virtuelle IP-Adresse (VIP) des Lastenausgleichs als Back-End-Endpunkt konfiguriert, wenn der Back-End-Server hinter einem NSX-Lastenausgleich in Azure VMware Solution platziert wird. 


## <a name="external-deployment"></a>Externe Bereitstellung

Bei einer externen Bereitstellung werden APIs veröffentlicht, die von externen Benutzern über einen öffentlichen Endpunkt verwendet werden. Entwickler und DevOps-Engineers können APIs über das Azure-Portal oder mithilfe von PowerShell sowie über das API Management-Entwicklerportal verwalten.

Das Diagramm für die externe Bereitstellung zeigt den gesamten Prozess und die beteiligten Akteure (ganz oben). Zu den Akteuren zählen:

- **Administrator(en):** Das Administrator- oder DevOps-Team, das Azure VMware Solution über das Azure-Portal sowie über Automatisierungsmechanismen wie PowerShell oder Azure DevOps verwaltet.

- **Benutzer:**  Die Consumer der verfügbar gemachten APIs (sowohl Benutzer als auch Dienste).

Die Daten durchlaufen die API Management-Instanz, von der die in das virtuelle Hub-Netzwerk eingebundenen Back-End-Dienste abstrahiert werden. Der Datenverkehr wird vom ExpressRoute-Gateway an den Kanal „ExpressRoute Global Reach“ weitergeleitet und erreicht einen NSX-Lastenausgleich, durch den der eingehende Datenverkehr auf die verschiedenen Back-End-Dienstinstanzen verteilt wird.

API Management verfügt über eine öffentliche Azure-API, und es empfiehlt sich, den Dienst „Azure DDoS Protection“ zu aktivieren. 

:::image type="content" source="media/api-management/external-deployment.png" alt-text="Externe Bereitstellung: API Management für Azure VMware Solution":::


## <a name="internal-deployment"></a>Interne Bereitstellung

Bei einer internen Bereitstellung werden APIs veröffentlicht, die von internen Benutzern oder Systemen genutzt werden. DevOps-Team und API-Entwickler verwenden die gleichen Verwaltungstools und das gleiche Entwicklerportal wie bei der externen Bereitstellung.

Interne Bereitstellungen können [mit Azure Application Gateway](../api-management/api-management-howto-integrate-internal-vnet-appgateway.md) durchgeführt werden, um einen öffentlichen und sicheren Endpunkt für die API zu erstellen.  Die Funktionen des Gateways werden zum Erstellen einer Hybridbereitstellung verwendet, die verschiedene Szenarios ermöglicht.  

* Verwenden der gleichen API Management-Ressource für die Nutzung durch interne und externe Consumer

* Verwenden einer einzelnen API Management-Ressource mit einer Teilmenge von APIs, die für externe Consumer definiert und verfügbar ist

* Bereitstellen einer einfachen Möglichkeit zum Aktivieren/Deaktivieren des Zugriffs auf API Management über das öffentliche Internet

Das Bereitstellungsdiagramm weiter unten zeigt Consumer, die intern oder extern sein können, wobei jeder Typ auf die gleichen oder auf unterschiedliche APIs zugreift.

Bei einer internen Bereitstellung werden APIs für die gleiche API Management-Instanz verfügbar gemacht. Application Gateway wird vor API Management mit aktivierter WAF-Funktion (Web Application Firewall) bereitgestellt. Außerdem wird eine Reihe von HTTP-Listenern und -Regeln zum Filtern des Datenverkehrs bereitgestellt, wodurch nur eine Teilmenge der in Azure VMware Solution ausgeführten Back-End-Dienste zur Verfügung gestellt werden.


* Der interne Datenverkehr verläuft über ein ExpressRoute-Gateway an Azure Firewall und dann direkt oder über Datenverkehrsregeln an API Management.   

* Externer Datenverkehr wird über Application Gateway unter Verwendung der externen Schutzebene für API Management in Azure eingebracht.


:::image type="content" source="media/api-management/internal-deployment.png" alt-text="Interne Bereitstellung: API Management für Azure VMware Solution" lightbox="media/api-management/internal-deployment.png":::