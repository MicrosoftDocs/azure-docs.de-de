---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: a68c3ff276c4bd1a9e33084f9745beac48f4b83d
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94992595"
---
|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Georedundante Sicherung muss für Azure Database for MariaDB aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Mit Azure Database for MariaDB können Sie die Redundanzoption für Ihren Datenbankserver auswählen. Sie kann auf einen georedundanten Sicherungsspeicher festgelegt werden, in dem die Daten nicht nur in der Region gespeichert werden, in der Ihr Server gehostet wird, sondern auch in eine gekoppelte Region repliziert werden, um die Wiederherstellungsoption bei einem Regionsausfall bereitzustellen. Das Konfigurieren von georedundantem Speicher für die Sicherung ist nur während der Erstellung des Servers zulässig. |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[MariaDB-Server muss einen VNET-Dienstendpunkt verwenden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdfbd9a64-6114-48de-a47d-90574dc2e489) |Auf virtuellen Netzwerken basierende Firewallregeln werden verwendet, um Datenverkehr von einem bestimmten Subnetz zu Azure Database for MariaDB zu ermöglichen und gleichzeitig sicherzustellen, dass der Datenverkehr Azure nicht verlässt. Diese Richtlinie bietet eine Möglichkeit, zu überprüfen, ob Azure Database for MariaDB einen VNET-Dienstendpunkt verwendet. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_VirtualNetworkServiceEndpoint_Audit.json) |
|[Privater Endpunkt muss für MariaDB-Server aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |Private Endpunktverbindungen erzwingen eine sichere Kommunikation durch das Aktivieren privater Konnektivität mit Azure Database for MariaDB. Konfigurieren Sie eine private Endpunktverbindung, um nur Zugriff auf Datenverkehr zu ermöglichen, der aus bekannten Netzwerken stammt, und Zugriff von allen anderen IP-Adressen, auch solchen in Azure, zu verhindern. |AuditIfNotExists, Disabled |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[Öffentlicher Netzwerkzugriff muss für MariaDB-Server deaktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |Das Deaktivieren der Eigenschaft für öffentlichen Netzwerkzugriff verbessert die Sicherheit, indem sichergestellt wird, dass auf Ihre Azure Database for MariaDB-Instanz nur von einem privaten Endpunkt aus zugegriffen werden kann. Mit dieser Konfiguration wird der Zugriff aus einem öffentlichen Adressraum außerhalb des Azure-IP-Adressbereichs strikt deaktiviert, und alle Anmeldungen, auf die auf IP-Adressen oder virtuellen Netzwerken basierende Firewallregeln reagieren, werden verweigert. |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
