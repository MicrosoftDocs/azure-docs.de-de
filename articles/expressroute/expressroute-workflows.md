---
title: 'Workflows zum Konfigurieren von Verbindungen – ExpressRoute: Azure | Microsoft-Dokumentation'
description: Diese Seite zeigt die Workflows zum Konfigurieren von ExpressRoute-Verbindungen und -Peerings.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 3ffcc5ac2193e607573ceb93717258f5349d1f15
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60883182"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>ExpressRoute-Workflows für die Verbindungsbereitstellung und Verbindungszustände
Auf dieser Seite erhalten Sie einen Überblick über die Workflows zur Dienstbereitstellung und Routingkonfiguration.

![Verbindungsworkflow](./media/expressroute-workflows/expressroute-circuit-workflow.png)

Die folgende Abbildung und die dazugehörigen Schritte veranschaulichen die Aufgaben, die zum vollständigen Bereitstellen einer ExpressRoute-Verbindung ausgeführt werden müssen. 

1. Verwenden Sie PowerShell, um eine ExpressRoute-Verbindung zu konfigurieren. Ausführlichere Anweisungen finden Sie im Artikel [Erstellen von ExpressRoute-Verbindungen](expressroute-howto-circuit-classic.md) .
2. Fordern Sie Konnektivität vom Service Provider an. Dieser Prozess variiert. Wenden Sie sich an Ihren Konnektivitätsanbieter, um weitere Details zum Anfordern der Konnektivität zu erhalten.
3. Stellen Sie sicher, dass die Verbindung erfolgreich eingerichtet wurde, indem Sie den Bereitstellungszustand der ExpressRoute-Verbindung mithilfe von PowerShell überprüfen. 
4. Konfigurieren Sie Routingdomänen. Wenn Ihr Konnektivitätsanbieter Layer 3 für Sie verwaltet, konfiguriert er das Routing für Ihre Verbindung. Wenn Ihr Konnektivitätsanbieter nur Layer 2-Dienste anbietet, müssen Sie das Routing gemäß den Richtlinien auf den Seiten [Routinganforderungen](expressroute-routing.md) und [Routingkonfiguration](expressroute-howto-routing-classic.md) konfigurieren.
   
   * Privates Azure-Peering aktivieren – Aktivieren Sie dieses Peering, um eine Verbindung mit virtuellen Computern/Clouddiensten herzustellen, die in virtuellen Netzwerken bereitgestellt werden.

   * Microsoft-Peering aktivieren – Aktivieren Sie dieses Peering, um auf Office 365 und Dynamics 365 zugreifen zu können. Darüber hinaus kann über Microsoft-Peering auf alle Azure-PaaS-Dienste zugegriffen werden.
     
     > [!IMPORTANT]
     > Verwenden Sie für die Verbindung mit Microsoft unbedingt einen anderen Proxy/Edge als für das Internet. Wenn Sie denselben Edge für ExpressRoute und das Internet verwenden, führt das zu asymmetrischem Routing und Konnektivitätsausfällen für Ihr Netzwerk.
     > 
     > 
     
     ![Routing von Workflows](./media/expressroute-workflows/routing-workflow.png)
5. Verknüpfen von virtuellen Netzwerken mit ExpressRoute-Verbindungen – Sie können virtuelle Netzwerke mit Ihrer ExpressRoute-Verbindung verknüpfen. Führen Sie die Schritte zum [Verknüpfen von VNets](expressroute-howto-linkvnet-arm.md) mit Ihrer Verbindung aus. Diese VNets können sich im gleichen Azure-Abonnement wie die ExpressRoute-Verbindung oder aber in einem anderen Abonnement befinden.

## <a name="expressroute-circuit-provisioning-states"></a>Bereitstellungszustände von ExpressRoute-Verbindungen
Jede ExpressRoute-Verbindung hat zwei Zustände:

* Bereitstellungszustand des Service Providers
* Status

Der Status gibt den Bereitstellungszustand von Microsoft an. Diese Eigenschaft wird auf „Aktiviert“ festgelegt, wenn Sie eine ExpressRoute-Verbindung erstellen.

Der Bereitstellungszustand des Konnektivitätsanbieters gibt den Zustand aufseiten des Konnektivitätsanbieters an. Mögliche Zustände: *NichtBereitgestellt*, *Bereitstellung* oder *Bereitgestellt*. Die ExpressRoute-Verbindung muss den Zustand „Provisioned“ aufweisen, damit Sie sie verwenden können.

### <a name="possible-states-of-an-expressroute-circuit"></a>Mögliche Zustände einer ExpressRoute-Verbindung
In diesem Abschnitt sind die möglichen Zustände für eine ExpressRoute-Verbindung aufgeführt.

**Zum Zeitpunkt der Erstellung**:

Die ExpressRoute-Verbindung weist den folgenden Zustand auf, wenn Sie das PowerShell-Cmdlet zum Erstellen der ExpressRoute-Verbindung ausführen.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


**Der Konnektivitätsanbieter stellt die Verbindung gerade bereit**:

Die ExpressRoute-Verbindung weist den folgenden Zustand auf, wenn Sie den Dienstschlüssel an den Konnektivitätsanbieter übergeben und der Anbieter den Bereitstellungsprozess gestartet hat.

    ServiceProviderProvisioningState : Provisioning
    Status                           : Enabled


**Der Konnektivitätsanbieter hat die Bereitstellung abgeschlossen**:

Die ExpressRoute-Verbindung weist den folgenden Zustand auf, sobald der Konnektivitätsanbieter die Bereitstellung abgeschlossen hat.

    ServiceProviderProvisioningState : Provisioned
    Status                           : Enabled

Die Verbindung kann nur im Zustand „Provisioned“ und „Enabled“ verwendet werden. Wenn Sie einen Layer 2-Anbieter nutzen, können Sie das Routing für Ihre Verbindung nur konfigurieren, wenn sie sich in diesem Zustand befindet.

**Der Konnektivitätsanbieter hebt die Verbindungsbereitstellung auf**:

Wenn Sie den Dienstanbieter aufgefordert haben, die Bereitstellung der ExpressRoute-Verbindung aufzuheben, weist die Verbindung den folgenden Zustand auf, wenn der Dienstanbieter den Prozess der Bereitstellungsaufhebung abgeschlossen hat.

    ServiceProviderProvisioningState : NotProvisioned
    Status                           : Enabled


Sie können sie bei Bedarf erneut aktivieren oder PowerShell-Cmdlets ausführen, um die Verbindung zu löschen.  

> [!IMPORTANT]
> Wenn Sie ein PowerShell-Cmdlet zum Löschen der Verbindung ausführen, während sich ServiceProviderProvisioningState im Status „Bereitstellung“ oder „Bereitgestellt“ befindet, tritt ein Fehler auf. Arbeiten Sie mit Ihrem Konnektivitätsanbieter zusammen, um zuerst die Bereitstellung der ExpressRoute-Verbindung aufzuheben, und löschen Sie dann die Verbindung. Microsoft erhebt weiterhin Gebühren für die Verbindung, bis Sie die Verbindung mithilfe des PowerShell-Cmdlets löschen.
> 
> 

## <a name="routing-session-configuration-state"></a>Konfigurationszustand der Routingsitzung
Der BGP-Bereitstellungszustand gibt an, ob die BGP-Sitzung auf dem Microsoft-Edge aktiviert wurde. Der Zustand muss aktiviert sein, damit Sie das Peering verwenden können.

Insbesondere beim Microsoft-Peering sollte der BGP-Sitzungszustand überprüft werden. Zusätzlich zum BGP-Bereitstellungszustand gibt es einen weiteren Zustand: *advertised public prefixes state*. Der Zustand der angekündigten öffentlichen Präfixe muss *configured* lauten, damit die BGP-Sitzung aktiv ist und das Routing durchgängig funktioniert. 

Wenn der Zustand der angekündigten öffentlichen Präfixe *validation needed* lautet, ist die BGP-Sitzung nicht aktiviert, da die angekündigten Präfixe keiner AS-Nummer in einer der Routingregistrierungen entsprachen. 

> [!IMPORTANT]
> Wenn der Zustand der angekündigten öffentlichen Präfixe *manual validation* lautet, müssen Sie ein Supportticket beim [Microsoft-Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) öffnen, nachweisen, dass Sie der Besitzer der angekündigten IP-Adressen sind, und die zugeordnete autonome Systemnummer angeben.
> 
> 

## <a name="next-steps"></a>Nächste Schritte
* Konfigurieren Sie Ihre ExpressRoute-Verbindung.
  
  * [Erstellen einer ExpressRoute-Verbindung](expressroute-howto-circuit-arm.md)
  * [Konfigurieren des Routings](expressroute-howto-routing-arm.md)
  * [Verknüpfen eines VNet mit einer ExpressRoute-Verbindung](expressroute-howto-linkvnet-arm.md)

