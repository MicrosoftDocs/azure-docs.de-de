---
title: Anpassen von benutzerdefinierten Routen (UDR) in Azure Kubernetes Service (AKS)
description: Erfahren Sie, wie Sie benutzerdefinierte ausgehende Routen in Azure Kubernetes Service (AKS) definieren.
services: container-service
ms.topic: article
ms.date: 06/29/2020
ms.openlocfilehash: 103d7dc76dee56a336f08f2cc0c7c8489c0bc565
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348133"
---
# <a name="customize-cluster-egress-with-a-user-defined-route"></a>Anpassen des ausgehenden Clusterdatenverkehrs mit einer benutzerdefinierten Route

Der von einem AKS-Cluster ausgehende Datenverkehr kann an verschiedene Szenarien angepasst werden. Standardmäßig stellt AKS eine standardmäßige SKU-Lastenausgleichsressource bereit, die für ausgehenden Datenverkehr eingerichtet und verwendet wird. Das Standardsetup erfüllt aber möglicherweise nicht alle Anforderungen in allen Szenarien, wenn öffentliche IP-Adressen nicht zulässig oder zusätzliche Hops für den ausgehenden Datenverkehr erforderlich sind.

In diesem Artikel wird beschrieben, wie Sie die ausgehende Route eines Clusters an verschiedene Netzwerkszenarien anpassen, wenn beispielsweise öffentliche IP-Adressen nicht zulässig sind und der Cluster hinter einem virtuellen Netzwerkgerät platziert werden muss.

## <a name="prerequisites"></a>Voraussetzungen
* Azure CLI, Version 2.0.81 oder höher
* API-Version `2020-01-01` oder höher


## <a name="limitations"></a>Einschränkungen
* „OutboundType“ kann nur zum Zeitpunkt der Clustererstellung definiert werden, und eine spätere Aktualisierung ist nicht möglich.
* Für `outboundType` sind AKS-Cluster erforderlich, bei denen `vm-set-type` als `VirtualMachineScaleSets` und `load-balancer-sku` als `Standard` festgelegt ist.
* Wenn Sie `outboundType` auf den Wert `UDR` festlegen, wird eine benutzerdefinierte Route mit gültiger ausgehender Konnektivität für den Cluster benötigt.
* Durch Festlegung von `outboundType` auf den Wert `UDR` wird impliziert, dass die IP-Quelladresse des eingehenden Datenverkehrs zum Routing an den Lastenausgleich **nicht** der IP-Zieladresse des vom Cluster ausgehenden Datenverkehrs entspricht.

## <a name="overview-of-outbound-types-in-aks"></a>Übersicht über ausgehende Datenverkehrstypen in AKS

Ein AKS-Cluster kann mit einem eindeutigen `outboundType` vom Typ `loadBalancer` oder `userDefinedRouting` angepasst werden.

> [!IMPORTANT]
> Der ausgehende Typ betrifft nur den ausgehenden Datenverkehr Ihres Clusters. Weitere Informationen finden Sie unter [Einrichten von Eingangscontrollern](ingress-basic.md).

> [!NOTE]
> Sie können Ihre eigene [Routingtabelle][byo-route-table] mit UDR und Kubenet-Netzwerken verwenden. Stellen Sie sicher, dass Ihre Clusteridentität (Dienstprinzipal oder verwaltete Identität) über Berechtigungen für „Mitwirkender“ für die benutzerdefinierte Routingtabelle verfügt.

### <a name="outbound-type-of-loadbalancer"></a>Ausgehender Typ von loadBalancer

Wenn `loadBalancer` festgelegt ist, führt AKS die folgende Konfiguration automatisch durch. Ein Lastenausgleich wird für ausgehenden Datenverkehr über eine von AKS zugewiesene öffentliche IP-Adresse verwendet. Ein ausgehender Typ von `loadBalancer` unterstützt Kubernetes-Dienste vom Typ `loadBalancer`, die ausgehenden Datenverkehr von der Lastenausgleichsressource erwarten, der vom AKS-Ressourcenanbieter erstellt wurde.

Die folgende Konfiguration wird von AKS durchgeführt.
   * Für ausgehenden Datenverkehr vom Cluster wird eine öffentliche IP-Adresse bereitgestellt.
   * Die öffentliche IP-Adresse ist der Lastenausgleichsressource zugewiesen.
   * Für Agent-Knoten im Cluster werden Back-End-Pools für den Lastenausgleich eingerichtet.

Hier sehen Sie eine Netzwerktopologie, die standardmäßig in AKS-Clustern bereitgestellt wird und `loadBalancer` als `outboundType` verwendet.

![Diagramm der Eingangs-IP-Adresse und der Ausgangs-IP-Adresse. Der eingehende Datenverkehr wird an einen Lastenausgleich weitergeleitet, der Datenverkehr zu und von einem internen Cluster sowie anderen Datenverkehr an die Ausgangs-IP-Adresse weiterleitet. Von dort wird der Datenverkehr an das Internet, MCR, die von Azure benötigten Dienste und die AKS-Steuerungsebene weitergeleitet.](media/egress-outboundtype/outboundtype-lb.png)

### <a name="outbound-type-of-userdefinedrouting"></a>Ausgehender Typ von userDefinedRouting

> [!NOTE]
> Die Verwendung von ausgehenden Typen ist ein erweitertes Netzwerkszenario und erfordert eine ordnungsgemäße Netzwerkkonfiguration.

Wenn `userDefinedRouting` festgelegt ist, konfiguriert AKS ausgehende Datenpfade nicht automatisch. Das ausgehende Setup muss von Ihnen durchgeführt werden.

Der AKS-Cluster muss in einem bestehenden virtuellen Netzwerk mit einem zuvor konfigurierten Subnetz bereitgestellt werden, da ein expliziter Ausgang eingerichtet werden muss, wenn die Architektur für den standardmäßigen Lastenausgleich (Standard Load Balancer, SLB) nicht verwendet wird. Als solche erfordert diese Architektur das explizite Senden von ausgehendem Datenverkehr an eine Appliance wie eine Firewall, ein Gateway oder einen Proxy oder das Zulassen der Netzwerkadressenübersetzung (NAT) über eine öffentliche IP-Adresse, die dem Load Balancer Standard oder der Appliance zugewiesen ist.

#### <a name="load-balancer-creation-with-userdefinedrouting"></a>Load Balancer-Erstellung mit „UserDefinedRouting“

AKS-Cluster mit dem Ausgangstyp „UDR“ erhalten nur dann einen Standard-Load Balancer (SLB), wenn der erste Kubernetes-Dienst vom Typ „loadBalancer“ bereitgestellt wird. Der Load Balancer wird mit einer öffentlichen IP-Adresse für *eingehende* Anforderungen und einem Back-End-Pool für *eingehende* Anforderungen konfiguriert. Eingangsregeln werden vom Azure-Cloudanbieter konfiguriert. Aufgrund des Ausgangstyps „UDR“ werden aber **weder eine öffentliche ausgehende IP-Adresse noch Ausgangsregeln** konfiguriert. Ihre benutzerdefinierte Route (UDR) ist weiterhin die einzige Quelle für ausgehenden Datenverkehr.

Für Azure Load Balancer [fallen erst Gebühren an, wenn eine Regel festgelegt wird](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="deploy-a-cluster-with-outbound-type-of-udr-and-azure-firewall"></a>Bereitstellen eines Clusters mit dem ausgehenden Typ „UDR“ und Azure Firewall

Um die Anwendung eines Clusters mit ausgehendem Typ und einer benutzerdefinierten Route zu veranschaulichen, kann der Cluster in einem virtuellen Netzwerk mit einer Azure Firewall in einem eigenen Subnetz konfiguriert werden. Informationen zum [Beispiel zum Einschränken des ausgehenden Datenverkehrs mit Azure Firewall](limit-egress-traffic.md#restrict-egress-traffic-using-azure-firewall) finden Sie im folgenden Beispiel.

> [!IMPORTANT]
> Für den ausgehenden Typ „UDR“ muss in der Routingtabelle eine Route für das Ziel 0.0.0.0/0 und den nächsten Hop des virtuellen Netzwerkgeräts (Network Virtual Appliance, NVA) vorhanden sein.
> Die Routingtabelle weist bereits den Standardwert 0.0.0.0/0 zum Internet auf. Ohne eine öffentliche IP-Adresse zum SNAT wird Ihnen allein das Hinzufügen dieser Route keinen Ausgang ermöglichen. AKS wird überprüfen, dass Sie keine 0.0.0.0/0-Route erstellen, die auf das Internet verweist, sondern stattdessen auf NVA oder ein Gateway usw. Bei Verwendung des Ausgangstyps „UDR“ wird eine öffentliche Load Balancer-IP-Adresse für **eingehende Anforderungen** nur dann erstellt, wenn ein Dienst vom Typ *loadbalancer* konfiguriert wird. Eine öffentliche IP-Adresse für **ausgehende Anforderungen** wird von Azure Kubernetes Service nie erstellt, wenn „UDR“ als Ausgangstyp festgelegt ist.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Übersicht über benutzerdefinierte Routen in Azure-Netzwerken](../virtual-network/virtual-networks-udr-overview.md).

Weitere Informationen finden Sie auch unter [Erstellen, Ändern oder Löschen einer Routingtabelle](../virtual-network/manage-route-table.md).

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[byo-route-table]: configure-kubenet.md#bring-your-own-subnet-and-route-table-with-kubenet
