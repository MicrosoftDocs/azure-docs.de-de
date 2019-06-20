---
title: Unterstützte Kubernetes-Versionen in Azure Kubernetes Service
description: Grundlegendes zur Richtlinie zur Unterstützung der Kubernetes-Version und zum Lebenszyklus von Clustern in Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: article
ms.date: 05/20/2019
ms.author: saudas
ms.openlocfilehash: a7b3176e39ccaa0f9ddb1ef45c33ec6902e62f1c
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956321"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Unterstützte Kubernetes-Versionen in Azure Kubernetes Service (AKS)

Die Kubernetes-Community veröffentlicht etwa alle drei Monate Nebenversionen. Diese Releases enthalten neue Features und Verbesserungen. Patchreleases kommen häufiger vor (manchmal wöchentlich) und sind nur für wichtige Fehlerbehebungen in einer Nebenversion gedacht. Diese Patchreleases beinhalten Behebungen von Sicherheitsrisiken oder größerer Fehler, die viele Kunden und Produkte betreffen, die in einer auf Kubernetes basierenden Produktionsumgebung ausgeführt werden.

Eine neue Nebenversion von Kubernetes wird am ersten Tag in der [aks-engine][aks-engine] zur Verfügung gestellt. Das AKS-Servicelevelziel (Service Level Objective, SLO) ist darauf ausgerichtet, die Nebenversion für AKS-Cluster innerhalb von 30 Tagen zu veröffentlichen, vorbehaltlich der Stabilität des Releases.

## <a name="kubernetes-version-support-policy"></a>Richtlinie zur Unterstützung der Kubernetes-Version

AKS unterstützt vier Nebenversionen von Kubernetes:

- Die aktuelle Nebenversion, die upstream veröffentlicht wird (n).
- Die drei vorherigen Nebenversionen. Jede unterstützte Nebenversion unterstützt ebenfalls zwei stabile Patches.

Wenn AKS zum Beispiel heute *1.13.x* einführt, werden auch *1.12.a* + *1.12.b*, *1.11.c* + *1.11d* und *1.10.e* + *1.10f* unterstützt (wobei die Buchstaben der Patchreleases für die beiden letzten stabilen Builds stehen).

Wenn eine neue Nebenversion eingeführt wird, werden die älteste Nebenversion und die unterstützten Patchreleases deaktiviert. 30 Tage vor der Veröffentlichung der neuen Nebenversion und der bevorstehenden Außerbetriebnahme der Version erfolgt eine Ankündigung über die [Azure-Updatekanäle][azure-update-channel]. Im Beispiel oben wird *1.13.x* veröffentlicht, und die Versionen *1.9.g* + *1.9.h* werden deaktiviert.

Wenn Sie einen AKS-Cluster im Portal oder mit der Azure CLI bereitstellen, wird der Cluster immer auf die Nebenversion n-1 und den neuesten Patch festgelegt. Wenn AKS zum Beispiel *1.13.x*, *1.12.a* + *1.12.b*, *1.11.c* + *1.11d* und *1.10.e* + *1.10f* unterstützt, ist die Standardversion des neuen Clusters *1.11.b*.

## <a name="list-currently-supported-versions"></a>Auflisten der derzeit unterstützten Versionen

Um herauszufinden, welche Versionen für Ihr Abonnement und Ihre Region derzeit verfügbar sind, verwenden Sie den Befehl [az aks get-versions][az-aks-get-versions]. Im folgenden Beispiel sind die verfügbaren Kubernetes-Versionen für die Region *EastUS* aufgelistet:

```azurecli-interactive
az aks get-versions --location eastus --output table
```

Die Ausgabe sieht ungefähr wie im folgenden Beispiel aus, in dem die Kubernetes-Version *1.13.5* als aktuelle verfügbare Version angezeigt wird:

```
KubernetesVersion    Upgrades
-------------------  ------------------------
1.13.5               None available
1.12.7               1.13.5
1.12.6               1.12.7, 1.13.5
1.11.9               1.12.6, 1.12.7
1.11.8               1.11.9, 1.12.6, 1.12.7
1.10.13              1.11.8, 1.11.9
1.10.12              1.10.13, 1.11.8, 1.11.9
```

## <a name="faq"></a>Häufig gestellte Fragen

**Was geschieht, wenn ein Kunde ein Upgrade eines Kubernetes-Clusters mit einer Nebenversion durchführt, die nicht unterstützt wird?**

Wenn Sie die Version *n-4* verwenden, befinden Sie sich außerhalb des SLO. Wenn Ihr Upgrade von Version n-4 auf n-3 erfolgreich ist, befinden Sie sich wieder innerhalb des SLO. Beispiel:

- Wenn die AKS-Versionen *1.13.x*, *1.12.a* + *1.12.b*, *1.11.c* + *1.11d* und *1.10.e* + *1.10f* unterstützt werden und Sie *1.9.g* oder *1.9.h* verwenden, befinden Sie sich außerhalb des Servicelevelziels (SLO).
- Wenn das Upgrade von *1.9.g* oder *1.9.h* auf *1.10.e* oder *1.10.f* erfolgreich ist, befinden Sie sich wieder innerhalb des Servicelevelziels (SLO).

Upgrades auf ältere Versionen als *n-4* werden nicht unterstützt. In solchen Fällen wird empfohlen, dass die Kunden neue AKS-Cluster erstellen und ihre Workloads erneut bereitstellen.

**Was geschieht, wenn ein Kunde einen Kubernetes-Cluster mit einer Nebenversion skaliert, die nicht unterstützt wird?**

Bei Nebenversionen, die von AKS nicht unterstützt werden, funktioniert das Herunter- und Hochskalieren weiterhin einwandfrei.

**Kann ein Kunde für immer eine Kubernetes-Version verwenden?**

Ja. Wenn der Cluster jedoch keine der von AKS unterstützten Versionen verwendet, befindet sich der Cluster außerhalb des AKS SLO. Azure führt kein automatisches Upgrade Ihres Clusters durch und löscht ihn nicht.

**Welche Version wird vom Master unterstützt, wenn der Agent-Cluster keine der unterstützten AKS-Versionen verwendet?**

Der Master wird automatisch auf die neueste unterstützte Version aktualisiert.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Upgrade Ihres Clusters finden Sie unter [Durchführen eines Upgrades für einen Azure Kubernetes Service-Cluster (AKS)][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
