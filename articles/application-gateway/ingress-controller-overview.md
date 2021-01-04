---
title: Was ist der Azure Application Gateway-Eingangscontroller?
description: Dieser Artikel bietet eine Einführung in die Funktionsweise des Application Gateway-Eingangscontrollers.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 06/10/2020
ms.author: caya
ms.openlocfilehash: 26f53a8f93d4d51ec8f8fd91051496a46670f432
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397347"
---
# <a name="what-is-application-gateway-ingress-controller"></a>Was ist der Application Gateway-Eingangscontroller?
Der Application Gateway-Eingangscontroller (Application Gateway Ingress Controller, AGIC) ist eine Kubernetes-Anwendung, die es Kunden von [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) ermöglicht, den nativen L7-Lastenausgleich von [Application Gateway](https://azure.microsoft.com/services/application-gateway/) zu nutzen, um Cloudsoftware im Internet bereitzustellen. AGIC überwacht den Kubernetes-Cluster, auf dem er gehostet wird, und aktualisiert fortlaufend eine Application Gateway-Instanz, sodass ausgewählte Dienste im Internet bereitgestellt werden.

Der Eingangscontroller wird in einem eigenen Pod im AKS des Kunden ausgeführt. AGIC überwacht eine Teilmenge der Kubernetes-Ressourcen auf Änderungen. Der Status des AKS-Clusters wird in Application Gateway-spezifische Konfiguration übersetzt und auf [Azure Resource Manager (ARM)](../azure-resource-manager/management/overview.md) angewendet.

## <a name="benefits-of-application-gateway-ingress-controller"></a>Vorteile des Application Gateway-Eingangscontrollers
AGIC hilft, die Notwendigkeit zu vermeiden, einen weiteren Lastenausgleich bzw. eine öffentliche IP-Adresse vor dem AKS-Cluster einzusetzen, und vermeidet mehrere Hops in Ihrem Datenpfad, bevor Anforderungen den AKS-Cluster erreichen. Application Gateway kommuniziert direkt mit Pods über ihre private IP-Adresse und benötigt keine NodePort- oder KubeProxy-Dienste. Dies führt auch zu einer besseren Leistung für Ihre Bereitstellungen.

Eingangscontroller werden exklusiv von den Standard_v2- und WAF_v2-SKUs unterstützt, wodurch auch Vorteile der automatischen Skalierung erzielt werden. Application Gateway kann als Reaktion auf eine Erhöhung oder Verringerung der Datenverkehrslast reagieren und eine entsprechende Skalierung ausführen, ohne Ressourcen aus Ihrem AKS-Cluster zu verbrauchen.

Die Verwendung von Application Gateway zusätzlich zu AGIC schützt auch Ihren AKS-Cluster durch die Bereitstellung von TLS-Richtlinien und WAF-Funktionen (Web Application Firewall).

![Azure Application Gateway und AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

AGIC wird über die Kubernetes-[Eingangsressource](https://kubernetes.io/docs/user-guide/ingress/) sowie über Dienste und Bereitstellungen/Pods konfiguriert. Er bietet eine Reihe von Features und nutzt dabei den nativen L7-Lastenausgleich von Application Gateway in Azure. Beispiele für solche Features:
  - URL-Routing
  - Cookiebasierte Affinität
  - TLS-Terminierung
  - End-to-End-TLS
  - Unterstützung für öffentliche, private und hybride Websites
  - Integrierte Web Application Firewall

## <a name="difference-between-helm-deployment-and-aks-add-on"></a>Unterschied zwischen Helm-Bereitstellung und AKS-Add-On
Es gibt zwei Möglichkeiten, AGIC für Ihren AKS-Cluster bereitzustellen. Die erste Möglichkeit ist die Verwendung von Helm, bei der zweiten wird AKS als Add-On verwendet. Der Hauptvorteil der Bereitstellung von AGIC als AKS-Add-On ist, dass dies wesentlich einfacher ist, als die Bereitstellung mittels Helm. Bei einer neuen Einrichtung können Sie mit nur einer Zeile in Azure CLI ein neues Application Gateway und einen neuen AKS-Cluster mit als Add-On aktiviertem AGIC bereitstellen. Das Add-On ist außerdem ein vollständig verwalteter Dienst, der zusätzliche Vorteile bietet wie automatische Updates und verbesserten Support. Ein mittels Helm bereitgestellter AGIC wird von AKS nicht unterstützt, doch der als AKS-Add-On bereitgestellte AGIC wird von AKS unterstützt. 

Das AGIC-Add-On wird weiterhin als Pod im AKS-Cluster des Kunden bereitgestellt, doch es gibt ein paar Unterschiede zwischen der Helm-Bereitstellungsversion und der Add-On-Version von AGIC. Im Folgenden finden Sie eine Liste der Unterschiede zwischen den beiden Versionen: 
  - Helm-Bereitstellungswerte können im AKS-Add-On nicht geändert werden:
    - `verbosityLevel` wird standardmäßig auf 5 festgelegt.
    - `usePrivateIp` wird standardmäßig auf „false“ festgelegt. Dies kann mit der [„use-private-ip“-Anmerkung](ingress-controller-annotations.md#use-private-ip) außer Kraft gesetzt werden.
    - `shared` wird im Add-On nicht unterstützt. 
    - `reconcilePeriodSeconds` wird im Add-On nicht unterstützt.
    - `armAuth.type` wird im Add-On nicht unterstützt.
  - Der mittels Helm bereitgestellte AGIC unterstützt ProhibitedTargets, was bedeutet, dass AGIC das Application Gateway speziell für AKS-Cluster konfigurieren kann, ohne dass sich dies auf andere vorhandene Back-Ends auswirkt. Diese Funktion wird vom AGIC-Add-On zurzeit nicht unterstützt. 
  - Da das AGIC-Add-On ein verwalteter Dienst ist, werden Kunden automatisch auf die neueste Version des AGIC-Add-Ons aktualisiert, anders als beim mittels Helm bereitgestellten AGIC, bei dem der Kunde AGIC manuell aktualisieren muss. 

> [!NOTE]
> Die AGIC-Bereitstellungsmethode mittels AKS-Add-On befindet sich zurzeit in der Vorschauversion. Es wird davon abgeraten, Produktionsworkloads mit Funktionen auszuführen, die sich noch in der Vorschauversion befinden. Wenn Sie also neugierig sind und sie ausprobieren möchten, empfehlen wir, einen neuen Cluster zu Testzwecken einzurichten. 

In den folgenden Tabellen wird sortiert, welche Szenarien derzeit mit der Helm-Bereitstellungsversion und welche mit der AKS-Add-On-Version von AGIC unterstützt werden. 

### <a name="aks-add-on-agic-single-aks-cluster"></a>AKS-Add-On AGIC (einzelner AKS-Cluster)
|                  |1 Application Gateway |2+ Application Gateways |
|------------------|---------|--------|
|**1 AGIC**|Ja, diese Möglichkeit wird unterstützt. |Nein, diese Möglichkeit befindet sich in unserem Backlog. |
|**2+ AGICs**|Nein, nur 1 AGIC unterstützt/Cluster. |Nein, nur 1 AGIC unterstützt/Cluster. |

### <a name="helm-deployed-agic-single-aks-cluster"></a>Mittels Helm bereitgestellter AGIC (einzelner AKS-Cluster)
|                  |1 Application Gateway |2+ Application Gateways |
|------------------|---------|--------|
|**1 AGIC**|Ja, diese Möglichkeit wird unterstützt. |Nein, diese Möglichkeit befindet sich in unserem Backlog. |
|**2+ AGICs**|Muss die freigegebene ProhibitedTarget-Funktion verwenden und gesonderte Namespaces beachten. |Ja, diese Möglichkeit wird unterstützt. |

### <a name="helm-deployed-agic-2-aks-clusters"></a>Mittels Helm bereitgestellter AGIC (2+ AKS-Cluster)
|                  |1 Application Gateway |2+ Application Gateways |
|------------------|---------|--------|
|**1 AGIC**|– |– |
|**2+ AGICs**|Muss die freigegebene ProhibitedTarget-Funktion verwenden. |– |

## <a name="next-steps"></a>Nächste Schritte
- [**Greenfield-Bereitstellung mit AKS-Add-On**](tutorial-ingress-controller-add-on-new.md): Anleitungen zum Installieren des AGIC-Add-Ons, von AKS und Application Gateway in einer leeren Infrastruktur.
- [**Brownfield-Bereitstellung mit AKS-Add-On**](tutorial-ingress-controller-add-on-existing.md): Installieren des AGIC-Add-Ons auf einem AKS-Cluster mit einem vorhandenen Application Gateway.
- [**Greenfield-Bereitstellung mit Helm**](ingress-controller-install-new.md): Installieren von AGIC mittels Helm, eines neuen AKS-Clusters und eines neuen Application Gateways in einer leeren Infrastruktur.
- [**Brownfield-Bereitstellung von Helm**](ingress-controller-install-existing.md): Bereitstellen von AGIC mittels Helm auf einem vorhandenen AKS-Cluster und Application Gateway.