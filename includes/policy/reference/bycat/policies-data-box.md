---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 4d95cac0532c17bc369beccc376603567b0defe0
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100090501"
---
|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Data Box-Aufträge müssen die Mehrfachverschlüsselung für ruhende Daten auf dem Gerät aktivieren](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc349d81b-9985-44ae-a8da-ff98d108ede8) |Aktivieren Sie eine zweite Ebene der softwarebasierten Verschlüsselung für ruhende Daten auf dem Gerät. Das Gerät ist bereits über eine AES-256-Verschlüsselung (Advanced Encryption Standard) für ruhende Daten geschützt. Mit dieser Option wird eine zweite Ebene der Datenverschlüsselung hinzugefügt. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_DoubleEncryption_Audit.json) |
|[Azure Data Box-Aufträge müssen einen kundenseitig verwalteten Schlüssel zum Verschlüsseln des Kennworts für die Geräteentsperrung verwenden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F86efb160-8de7-451d-bc08-5d475b0aadae) |Verwenden Sie einen kundenseitig verwalteten Schlüssel, um die Verschlüsselung des Kennworts für die Geräteentsperrung für Azure Data Box zu steuern. Kundenseitig verwaltete Schlüssel bieten zudem Unterstützung beim Verwalten des Zugriffs auf das Kennwort zur Geräteentsperrung durch den Data Box Dienst, um das Gerät vorzubereiten und Daten automatisiert zu kopieren. Die Daten auf dem Gerät selbst sind bereits im Ruhezustand mit einer AES-256-Verschlüsselung (Advanced Encryption Standard) verschlüsselt, und das Kennwort zur Geräteentsperrung wird standardmäßig mit einem von Microsoft verwalteten Schlüssel verschlüsselt. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Data%20Box/DataBox_CMK_Audit.json) |
