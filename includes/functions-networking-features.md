---
ms.openlocfilehash: 82571d1a0e651f638dec29184f0ecdc88562b3ad
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020988"
---


| Funktion |[Verbrauchstarif](../articles/azure-functions/functions-scale.md#consumption-plan)|[Premium-Plan](../articles/azure-functions/functions-scale.md#premium-plan)|[Dedizierter Plan](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[IP-Einschränkungen für eingehenden Datenverkehr und Zugriff auf private Sites](../articles/azure-functions/functions-networking-options.md#inbound-access-restrictions)|✅Ja|✅Ja|✅Ja|✅Ja|✅Ja|
|[Integration in ein virtuelles Netzwerk](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌Nein|✅Ja (regional)|✅Ja (regional und Gateway)|✅Ja| ✅Ja|
|[Trigger für virtuelle Netzwerke (nicht HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌Nein| ✅Ja |✅Ja|✅Ja|✅Ja|
|[Hybridverbindungen](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (nur Windows)|❌Nein|✅Ja|✅Ja|✅Ja|✅Ja|
|[IP-Einschränkungen für ausgehenden Datenverkehr](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌Nein| ✅Ja|✅Ja|✅Ja|✅Ja|