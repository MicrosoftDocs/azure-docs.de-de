---
title: Häufig gestellte Fragen (FAQ) zu Azure Private Link
description: Erfahren Sie mehr über Azure Private Link.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: allensu
ms.openlocfilehash: b56c57a0b803a41c095f6f25f69a18a815d182f1
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582008"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Häufig gestellte Fragen (FAQ) zu Azure Private Link

## <a name="private-link"></a>Private Link

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>Was sind der private Endpunkt in Azure und der Azure Private Link-Dienst?

- **[Privater Endpunkt in Azure](private-endpoint-overview.md)** : Ein privater Endpunkt in Azure ist eine Netzwerkschnittstelle, die Sie privat und sicher mit einem von Azure Private Link betriebenen Dienst verbindet. Sie können private Endpunkte verwenden, um eine Verbindung mit einem Private Link unterstützenden Azure PaaS-Dienst oder mit Ihrem eigenen Private Link-Dienst herzustellen.
- **[Azure Private Link-Dienst](private-link-service-overview.md)** : Der Azure Private Link-Dienst ist ein von einem Dienstanbieter erstellter Dienst. Derzeit kann ein Private Link-Dienst an die Front-End-IP-Konfiguration eines Load Balancer Standard angefügt werden. 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>Wie wird der Datenverkehr bei der Verwendung von Private Link gesendet?
Der Datenverkehr wird privat über den Microsoft-Backbone übertragen. Er gelangt nicht ins Internet. Azure Private Link speichert keine Kundendaten.
 
### <a name="what-is-the-difference-between-service-endpoints-and-private-endpoints"></a>Worin besteht der Unterschied zwischen Dienstendpunkten und privaten Endpunkten?
- Private Endpunkte gewähren Netzwerkzugriff auf bestimmte Ressourcen hinter einem bestimmten Dienst, der eine differenzierte Segmentierung bietet. Der Datenverkehr kann die Dienstressource von einem lokalen Standort ohne öffentliche Endpunkte erreichen.
- Ein Dienstendpunkt ist weiterhin ein IP-Adresse, die öffentlich geroutet werden kann.  Ein privater Endpunkt ist eine private IP-Adresse im Adressraum des virtuellen Netzwerks, in dem der private Endpunkt konfiguriert wurde.

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Welche Beziehung besteht zwischen dem Private Link-Dienst und dem privaten Endpunkt?
Mehrere Ressourcentypen für private Links unterstützen den Zugriff über einen privaten Endpunkt. Zu den Ressourcen gehören Azure PaaS-Dienste und Ihr eigener Private Link-Dienst. Es handelt sich um eine 1:n-Beziehung. 

Ein Private Link-Dienst empfängt Verbindungen von mehreren privaten Endpunkten. Ein privater Endpunkt stellt eine Verbindung mit einem privaten Private Link-Dienst her.    

### <a name="do-i-need-to-disable-network-policies-for-private-link"></a>Muss ich Netzwerkrichtlinien für den Private Link-Dienst deaktivieren?
Ja. Sowohl für den privaten Endpunkt als auch für den Private Link-Dienst müssen Netzwerkrichtlinien deaktiviert werden, damit sie ordnungsgemäß funktionieren. Sie weisen beide voneinander unabhängige Eigenschaften auf.

## <a name="private-endpoint"></a>Privater Endpunkt 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>Kann ich mehrere private Endpunkte in demselben VNet erstellen? Können sie Verbindungen zu anderen Diensten herstellen? 
Ja. Sie können mehrere private Endpunkte in demselben VNet oder Subnetz verwenden. Sie können Verbindungen zu anderen Diensten herstellen.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>Benötige ich ein dediziertes Subnetz für private Endpunkte? 
Nein. Sie benötigen kein dediziertes Subnetz für private Endpunkte. Sie können eine private Endpunkt-IP aus einem beliebigen Subnetz des VNet auswählen, in dem Ihr Dienst bereitgestellt wird.  
 
### <a name="can-a-private-endpoint-connect-to-private-link-services-across-azure-active-directory-tenants"></a>Kann ein privater Endpunkt eine Verbindung mit dem Private Link-Dienst über verschiedene Azure Active Directory-Mandanten hinweg herstellen? 
Ja. Private Endpunkte können über verschiedene Azure Active Directory-Mandanten hinweg eine Verbindung mit Private Link-Diensten oder einer Azure PaaS-Lösung herstellen. Private Endpunkte, die mandantenübergreifende Verbindungen herstellen können, erfordern eine manuelle Genehmigung von Anforderungen. 
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>Können private Endpunkte eine Verbindung mit Azure PaaS-Ressourcen zwischen Azure-Regionen herstellen?
Ja. Private Endpunkte können eine Verbindung mit Azure PaaS-Ressourcen zwischen Azure-Regionen herstellen.

### <a name="can-i-modify-my-private-endpoint-network-interface-nic-"></a>Kann ich die Netzwerkschnittstelle (NIC) meines privaten Endpunkts ändern?
Wenn ein privater Endpunkt erstellt wird, wird eine schreibgeschützte NIC zugewiesen. Dies kann nicht geändert werden und bleibt für den Lebenszyklus des privaten Endpunkts beibehalten.

## <a name="private-link-service"></a>Private Link-Dienst
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>Welche Voraussetzungen müssen gegeben sein, um einen Private Link-Dienst zu erstellen? 
Ihre Dienst-Back-Ends sollten sich in einem virtuellen Netzwerk und hinter einem Load Balancer Standard befinden.
 
### <a name="how-can-i-scale-my-private-link-service"></a>Wie kann ich meinen Private Link-Dienst skalieren? 
Sie können Ihren Private Link-Dienst auf verschiedene Arten skalieren: 
- Fügen Sie dem Pool hinter Ihrem Load Balancer Standard Back-End-VMs hinzu. 
- Fügen Sie dem Private Link-Dienst eine IP-Adresse hinzu. Es sind bis zu acht IP-Adressen pro Private Link-Dienst gestattet.  
- Fügen Sie dem Load Balancer Standard einen neuen Private Link-Dienst hinzu. Es sind bis zu acht Private Link-Dienste pro Load Balancer Standard gestattet.   

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>Kann ich meinen Dienst mit mehreren privaten Endpunkten verbinden?
Ja. Ein Private Link-Dienst kann Verbindungen von mehreren privaten Endpunkten empfangen. Ein privater Endpunkt kann jedoch nur eine Verbindung mit einem Private Link-Dienst herstellen.  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>Wie sollte ich die Offenlegung meines Private Link-Dienstes kontrollieren?
Sie können die Offenlegung über die Sichtbarkeitskonfiguration des Private Link-Dienstes kontrollieren. Die Sichtbarkeit unterstützt drei Einstellungen:

- **Keine:** Nur Abonnements mit Azure RBAC-Zugriff können den Dienst finden. 
- **Restriktiv:** Nur genehmigte aufgeführte Abonnements, die über Azure RBAC-Zugriff verfügen, können den Dienst finden. 
- **Alle**: Jeder kann den Dienst finden. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>Kann ich mit einem Load Balancer im Tarif „Basic“ einen Private Link-Dienst erstellen? 
Nein. Der Private Link-Dienst über einen Load Balancer im Tarif „Basic“ wird nicht unterstützt.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>Ist ein dediziertes Subnetz für den Private Link-Dienst erforderlich? 
Nein. Der Private Link-Dienst erfordert kein dediziertes Subnetz. Sie können ein beliebiges Subnetz in Ihrem VNet auswählen, in dem Ihr Dienst bereitgestellt wird.   

### <a name="im-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Ich bin ein Dienstanbieter, der Azure Private Link verwendet. Muss ich sicherstellen, dass alle meine Kunden über einen eindeutigen IP-Bereich verfügen und sich nicht mit meinem IP-Bereich überschneiden? 
Nein. Azure Private Link stellt Ihnen diese Funktionen zur Verfügung. Es ist nicht erforderlich, dass Sie über einen Adressraum verfügen, der nicht mit dem Adressraum Ihrer Kunden überlappt. 

##  <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über [Azure Private Link](private-link-overview.md).
