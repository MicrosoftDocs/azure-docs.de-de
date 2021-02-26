---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f26c7a4e9a571c317aa9cdfa1267778b607430be
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100100240"
---
|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Der Bot Service-Endpunkt muss ein gültiger HTTPS-URI sein.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F6164527b-e1ee-4882-8673-572f425f5e0a) |Daten können während der Übertragung manipuliert werden. Es gibt Protokolle, die eine Verschlüsselung ermöglichen, um Missbrauch und Manipulationen vorzubeugen. Um sicherzustellen, dass Ihre Bots ausschließlich über verschlüsselte Kanäle kommunizieren, legen Sie den Endpunkt auf einen gültigen HTTPS-URI fest. Dadurch wird sichergestellt, dass das HTTPS-Protokoll zum Verschlüsseln Ihrer Daten während der Übertragung verwendet wird. Darüber hinaus ist es häufig Voraussetzung zur Einhaltung gesetzlicher oder branchenüblicher Standards. Informationen finden Sie unter [https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines](https://docs.microsoft.com/azure/bot-service/bot-builder-security-guidelines). |Audit, Deny, Disabled |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_ValidEndpoint_Audit.json) |
|[Bot Service muss mit einem kundenseitig verwalteten Schlüssel (CMK) verschlüsselt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F51522a96-0869-4791-82f3-981000c2c67f) |Azure Bot Service verschlüsselt Ihre Ressource automatisch, um Ihre Daten zu schützen sowie die Sicherheits- und Complianceanforderungen der Organisation zu erfüllen. Standardmäßig werden von Microsoft verwaltete Verschlüsselungsschlüssel verwendet. Wählen Sie kundenseitig verwaltete Schlüssel (auch als Bring Your Own Key (BYOK) bezeichnet) aus, um mehr Flexibilität bei der Verwaltung von Schlüsseln oder der Steuerung des Zugriffs auf Ihr Abonnement zu haben. Weitere Informationen zur Azure Bot Service-Verschlüsselung finden Sie hier: [https://docs.microsoft.com/azure/bot-service/bot-service-encryption](https://docs.microsoft.com/azure/bot-service/bot-service-encryption). |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Bot%20Service/BotService_CMKEnabled_Audit.json) |
