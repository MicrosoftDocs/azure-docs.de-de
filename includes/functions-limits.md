---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/04/2020
ms.author: glenga
ms.openlocfilehash: 826fccad043b067ce86d5f56eaebc6ee48b532d1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025883"
---
| Resource |[Verbrauchstarif](../articles/azure-functions/functions-scale.md#consumption-plan)|[Premium-Plan](../articles/azure-functions/functions-scale.md#premium-plan)|[Dedizierter Plan](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/aks/quotas-skus-regions.md) |
| --- | --- | --- | --- | --- | --- |
|Standardmäßige [Timeoutdauer](../articles/azure-functions/functions-scale.md#timeout) (in Minuten) |5 | 30 |30<sup>1</sup> | 30 | 30 |
|Maximale [Timeoutdauer](../articles/azure-functions/functions-scale.md#timeout) (in Minuten) |10 | unbounded<sup>7</sup> | unbounded<sup>2</sup> | unbounded | unbounded |
| Maximale Anzahl ausgehender Verbindungen (pro Instanz) | 600 aktive (insgesamt 1.200) | unbounded | unbounded | unbounded | unbounded |
| Maximale Anforderungsgröße (MB)<sup>3</sup> | 100 | 100 | 100 | 100 | Abhängig von Cluster |
| Maximale Länge der Abfragezeichenfolge<sup>3</sup> | 4096 | 4096 | 4096 | 4096 | Abhängig von Cluster |
| Maximale Länge der Anforderungs-URL<sup>3</sup> | 8192 | 8192 | 8192 | 8192 | Abhängig von Cluster |
|[ACU](../articles/virtual-machines/acu.md) pro Instanz | 100 | 210–840 | 100–840 | 210–250<sup>8</sup> | [AKS – Preise](https://azure.microsoft.com/pricing/details/container-service/) |
| Maximaler Arbeitsspeicher (GB pro Instanz) | 1.5 | 3,5–14 | 1,75–14 | 3,5–14 | Jeder Knoten wird unterstützt. |
| Funktions-Apps pro Plan |100 |100 |unbounded<sup>4</sup> | unbounded | unbounded |
| [App Service-Pläne](../articles/app-service/overview-hosting-plans.md) | 100 pro [Region](https://azure.microsoft.com/global-infrastructure/regions/) |100 pro Ressourcengruppe |100 pro Ressourcengruppe | - | - |
| Speicher<sup>5</sup> |5 TB |250 GB |50–1.000 GB | 1 TB | – |
| Benutzerdefinierte Domänen pro App</a> |500<sup>6</sup> |500 |500 | 500 | – |
| benutzerdefinierte Domäne [SSL-Unterstützung](../articles/app-service/configure-ssl-bindings.md) |Unbegrenzte Anzahl von SNI SSL-Verbindungen inbegriffen | Unbegrenzte Anzahl von SNI SSL-Verbindungen und 1 IP-SSL-Verbindung inbegriffen |Unbegrenzte Anzahl von SNI SSL-Verbindungen und 1 IP-SSL-Verbindung inbegriffen | Unbegrenzte Anzahl von SNI SSL-Verbindungen und 1 IP-SSL-Verbindung inbegriffen | – |

<sup>1</sup> Das Timeout für die Laufzeit von Functions 1.x in einem App Service-Plan ist standardmäßig unbegrenzt.  
<sup>2</sup> Hierfür muss der App Service-Plan auf [Always On](../articles/azure-functions/functions-scale.md#always-on) festgelegt werden. Die Bezahlung erfolgt zu den üblichen [Raten](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>3</sup> Diese Grenzwerte werden [auf dem Host festgelegt](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>4</sup> Die tatsächliche Anzahl der Funktions-Apps, die Sie hosten können, hängt von der Aktivität der Apps, der Größe der Computerinstanzen und der entsprechenden Ressourcenauslastung ab.  
<sup>5</sup> Der Speichergrenzwert entspricht der gesamten Inhaltsgröße aller Apps im gleichen App Service-Plan im temporären Speicher. Der Verbrauchsplan verwendet Azure Files zur temporären Speicherung.  
<sup>6</sup> Wenn Ihre Funktions-App unter einem [Verbrauchsplan](../articles/azure-functions/functions-scale.md#consumption-plan) gehostet wird, wird nur die CNAME-Option unterstützt. Für Funktions-Apps unter einem [Premium-Plan](../articles/azure-functions/functions-scale.md#premium-plan) oder einem [App Service-Plan](../articles/azure-functions/functions-scale.md#app-service-plan) können Sie eine benutzerdefinierte Domäne entweder mit einem CNAME- oder einem A-Eintrag zuordnen.  
<sup>7</sup> Garantiert für bis zu 60 Minuten  
<sup>8</sup> Worker sind Rollen, die Kunden-Apps hosten. Worker sind in drei festen Größen verfügbar: Eine vCPU/3,5 GB RAM; zwei vCPUs/7 GB RAM; vier vCPUs/14 GB RAM.