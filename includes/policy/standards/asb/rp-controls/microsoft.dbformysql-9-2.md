---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/11/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7b16eae7e1583cfed226f26208764ae14187e7ea
ms.sourcegitcommit: c05e595b9f2dbe78e657fed2eb75c8fe511610e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/11/2021
ms.locfileid: "112040623"
---
|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Georedundante Sicherung muss für Azure Database for MySQL aktiviert sein](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Mit Azure Database for MySQL können Sie die Redundanzoption für Ihren Datenbankserver auswählen. Sie kann auf einen georedundanten Sicherungsspeicher festgelegt werden, in dem die Daten nicht nur in der Region gespeichert werden, in der Ihr Server gehostet wird, sondern auch in eine gekoppelte Region repliziert werden, um die Wiederherstellungsoption bei einem Regionsausfall bereitzustellen. Das Konfigurieren von georedundantem Speicher für die Sicherung ist nur während der Erstellung des Servers zulässig. |Audit, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
