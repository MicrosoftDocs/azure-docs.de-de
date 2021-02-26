---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 33e9ef75bcb7403a9faf1638f23498e18d5e226c
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100100358"
---
|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[VM Image Builder-Vorlagen müssen eine private Verbindung verwenden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2154edb9-244f-4741-9970-660785bccdaa) |Hiermit werden VM Image Builder-Vorlagen überwacht, für die kein virtuelles Netzwerk konfiguriert ist. Wenn kein virtuelles Netzwerk konfiguriert ist, wird stattdessen eine öffentliche IP-Adresse erstellt und verwendet. Dies kann dazu führen, dass Ressourcen direkt im Internet verfügbar gemacht werden, sodass sich die potenzielle Angriffsfläche vergrößert. |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/VM%20Image%20Builder/PrivateLinkEnabled_Audit.json) |
