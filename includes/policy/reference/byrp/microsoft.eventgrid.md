---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: fc2bdd31f0dd2649c4f8a7a38e161f04f0e80260
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100097897"
---
|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Event Grid-Domänen sollten Private Link verwenden.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9830b652-8523-49cc-b1b3-e17dce1127ca) |Mit Azure Private Link können Sie Ihr virtuelles Netzwerk mit Azure-Diensten verbinden, ohne an der Quelle oder am Ziel eine öffentliche IP-Adresse zu verwenden. Auf der Private Link-Plattform wird die Konnektivität zwischen dem Consumer und den Diensten über das Azure-Backbone-Netzwerk verarbeitet. Indem Sie private Endpunkte nicht dem gesamten Dienst, sondern nur Ihren Event Grid-Domänen zuordnen, sind Sie auch vor Risiken aufgrund von Datenlecks geschützt. Weitere Informationen finden Sie unter [https://aka.ms/privateendpoints](https://aka.ms/privateendpoints). |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridDomains_EnablePrivateEndpoint_Audit.json) |
|[Azure Event Grid-Themen sollten Private Link verwenden.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b90e17e-8448-49db-875e-bd83fb6f804f) |Mit Azure Private Link können Sie Ihr virtuelles Netzwerk mit Azure-Diensten verbinden, ohne an der Quelle oder am Ziel eine öffentliche IP-Adresse zu verwenden. Auf der Private Link-Plattform wird die Konnektivität zwischen dem Consumer und den Diensten über das Azure-Backbone-Netzwerk verarbeitet. Indem Sie private Endpunkte nicht dem gesamten Dienst, sondern nur Ihren privaten Endpunkten zuordnen, sind Sie auch vor Risiken aufgrund von Datenlecks geschützt. Weitere Informationen finden Sie unter [https://aka.ms/privateendpoints](https://aka.ms/privateendpoints). |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridTopics_EnablePrivateEndpoint_Audit.json) |
