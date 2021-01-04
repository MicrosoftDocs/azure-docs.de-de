---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b413a5ad82fa464fa0fe698812dd36a7375cfead
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008019"
---
|Name<br /><sub>(Azure-Portal)</sub> |BESCHREIBUNG |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Containerregistrierungen müssen mit einem kundenseitig verwalteten Schlüssel (CMK) verschlüsselt werden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Hiermit überwachen oder verweigern Sie Containerregistrierungen, für die die Verschlüsselung mit kundenseitig verwalteten Schlüsseln (Customer-Managed Key, CMK) nicht aktiviert ist. Azure verschlüsselt ruhende Registrierungsinhalte automatisch mit dienstseitig verwalteten Schlüsseln. Sie können die Standardverschlüsselung durch eine zusätzliche Verschlüsselungsebene ergänzen, indem Sie einen Schlüssel verwenden, den Sie in Azure Key Vault erstellen und verwalten. Weitere Informationen zur CMK-Verschlüsselung finden Sie unter [https://aka.ms/acr/CMK](https://aka.ms/acr/CMK). |Audit, Deny, Disabled |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json) |
|[Containerregistrierungen dürfen keinen uneingeschränkten Netzwerkzugriff zulassen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Hiermit überwachen Sie Containerregistrierungen, für die keine Netzwerk- oder Firewallregeln (IP) konfiguriert wurden und die somit standardmäßig Zugriff auf das gesamte Netzwerk erlauben. Durch das Einschränken des Netzwerkzugriffs werden Containerregistrierungen vor potenziellen Bedrohungen geschützt. Containerregistrierungen mit mindestens einer IP-/Firewallregel oder einem konfigurierten virtuellen Netzwerk werden als konform eingestuft. Weitere Informationen zu Container Registry-Netzwerkregeln finden Sie unter [https://aka.ms/acr/portal/public-network](https://aka.ms/acr/portal/public-network) und [https://aka.ms/acr/vnet](https://aka.ms/acr/vnet). |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json) |
|[Containerregistrierungen müssen Private Link-Instanzen verwenden](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe8eef0a8-67cf-4eb4-9386-14b0e78733d4) |Überwachen Sie Containerregistrierungen, die nicht über mindestens eine Verbindung mit privatem Endpunkt verfügen. Clients in einem virtuellen Netzwerk können über Private Link-Instanzen sicher auf Ressourcen mit Verbindungen mit privaten Endpunkten zugreifen. Der öffentliche Zugriff kann anschließend deaktiviert werden, um sicherzustellen, dass nur Private Link-Instanzen zum Herstellen einer Verbindung mit der Registrierung verwendet werden können. Weitere Informationen finden Sie unter [https://aka.ms/acr/private-link](https://aka.ms/acr/private-link). |Audit, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_PrivateEndpointEnabled_Audit.json) |
