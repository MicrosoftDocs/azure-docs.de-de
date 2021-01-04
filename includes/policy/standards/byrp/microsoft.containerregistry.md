---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: e4ea2ec0a2f6dd2aba2070d971cac4fa5be0b033
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94988753"
---
## <a name="azure-security-benchmark"></a>Vergleichstest für die Azure-Sicherheit

Der [Azure-Sicherheitsvergleichstest](../../../../articles/security/benchmarks/overview.md) enthält Empfehlungen zum Schutz Ihrer Cloudlösungen in Azure. Zum Anzeigen der vollständigen Zuordnung dieses Diensts zum Azure-Sicherheitsvergleichstest sehen Sie sich die [Zuordnungsdateien zum Azure-Sicherheitsvergleichstest](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines) an.

Weitere Informationen dazu, wie die verfügbaren Azure Policy-Build-Ins für alle Azure-Dienste diesem Konformitätsstandard entsprechen, finden Sie unter [Einhaltung gesetzlicher Bestimmungen in Azure Policy: Azure Sicherheitsvergleichstest](../../../../articles/governance/policy/samples/azure-security-benchmark.md).

|Domain |Steuerungs-ID |Steuerungstitel |Richtlinie<br /><sub>(Azure-Portal)</sub> |Version der Richtlinie<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Netzwerksicherheit |1.1 |Schützen von Ressourcen mithilfe von Netzwerksicherheitsgruppen oder Azure Firewall in Virtual Network |[Container Registry sollte einen VNET-Dienstendpunkt verwenden.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9.2

Weitere Informationen dazu, wie die verfügbaren Azure Policy-Integrationen für alle Azure-Dienste diesem Konformitätsstandard entsprechen, finden Sie unter [Einhaltung gesetzlicher Bestimmungen in Azure Policy: HIPAA HITRUST 9.2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md).
Weitere Informationen zu diesem Compliancestandard finden Sie unter [HIPAA HITRUST 9.2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

|Domain |Steuerungs-ID |Steuerungstitel |Richtlinie<br /><sub>(Azure-Portal)</sub> |Version der Richtlinie<br /><sub>(GitHub)</sub>  |
|---|---|---|---|---|
|Trennung in Netzwerken |0805.01m1Organizational.12 - 01.m |Mit den Sicherheitsgateways der Organisation (z. B. Firewalls) werden Sicherheitsrichtlinien erzwungen, und sie sind für die Filterung von Datenverkehr zwischen Domänen und die Blockierung von unberechtigtem Zugriff konfiguriert. Außerdem werden sie genutzt, um die Trennung zwischen internen kabelgebundenen, internen drahtlosen sowie externen Netzwerksegmenten (z. B. dem Internet), z. B. DMZ, zu verwalten und die Zugriffssteuerungsrichtlinien für die einzelnen Domänen zu erzwingen. |[Container Registry sollte einen VNET-Dienstendpunkt verwenden.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|Trennung in Netzwerken |0806.01m2Organizational.12356 - 01.m |Das Netzwerk der Organisation ist logisch und physisch segmentiert mit einem definierten Sicherheitsperimeter und einem abgestuften Satz von Steuerungen, einschließlich Subnetzwerken für öffentlich zugängliche Systemkomponenten, die logisch vom internen Netzwerk getrennt sind, basierend auf organisatorischen Anforderungen; und der Datenverkehr wird auf der Grundlage der erforderlichen Funktionalität und der Klassifizierung der Daten/Systeme auf der Grundlage einer Risikobewertung und ihrer jeweiligen Sicherheitsanforderungen gesteuert. |[Container Registry sollte einen VNET-Dienstendpunkt verwenden.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|Trennung in Netzwerken |0894.01m2Organizational.7 - 01.m |Bei der Migration von physischen Servern, Anwendungen oder Daten zu virtualisierten Servern werden Netzwerke von Netzwerken auf Produktionsebene getrennt. |[Container Registry sollte einen VNET-Dienstendpunkt verwenden.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|Netzwerksteuerungen |0868.09m3Organizational.18 - 09.m |Die Organisation erstellt eine Firewallkonfiguration, um den ein- und ausgehenden Datenverkehr auf das für die abgedeckte Datenumgebung erforderliche Maß zu beschränken. |[Container Registry sollte einen VNET-Dienstendpunkt verwenden.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|Netzwerksteuerungen |0869.09m3Organizational.19 - 09.m |Die Konfigurationsdateien des Routers sind gesichert und werden synchronisiert. |[Container Registry sollte einen VNET-Dienstendpunkt verwenden.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|Netzwerksteuerungen |0870.09m3Organizational.20 - 09.m |Der Zugriff auf alle Proxys wird verweigert, mit Ausnahme der Hosts, Ports und Dienste, die ausdrücklich erforderlich sind. |[Container Registry sollte einen VNET-Dienstendpunkt verwenden.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|Netzwerksteuerungen |0871.09m3Organizational.22 - 09.m |Autorisierende DNS-Server werden in interne und externe Rollen aufgeteilt. |[Container Registry sollte einen VNET-Dienstendpunkt verwenden.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0-preview](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

