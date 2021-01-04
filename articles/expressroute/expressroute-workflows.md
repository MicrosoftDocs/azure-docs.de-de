---
title: 'Azure ExpressRoute: Workflow für die Leitungskonfiguration'
description: Diese Seite zeigt den Workflow für die Konfiguration von ExpressRoute-Leitungen und -Peerings.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: duau
ms.custom: contperf-fy21q1
ms.openlocfilehash: 2742b03bcacd73e7e602666b898417f295905f19
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97034070"
---
# <a name="expressroute-workflows-for-circuit-provisioning-and-circuit-states"></a>ExpressRoute-Workflows für die Verbindungsbereitstellung und Verbindungszustände

In diesem Artikel erhalten Sie einen allgemeinen Überblick über die Workflows zur Dienstbereitstellung und Routingkonfiguration. In den folgenden Abschnitten werden sämtliche Aufgaben zur Bereitstellung einer ExpressRoute-Leitung erläutert.

## <a name="workflow-steps"></a>Workflowschritte

### <a name="1-prerequisites"></a>1. Voraussetzungen

Stellen Sie sicher, dass die Voraussetzungen erfüllt sind. Eine vollständige Liste finden Sie unter [Voraussetzungen und Checkliste für ExpressRoute](expressroute-prerequisites.md).

* Ein Azure-Abonnement wurde erstellt.
* Die physische Konnektivität mit dem ExpressRoute-Partner wurde eingerichtet oder über ExpressRoute Direct konfiguriert. Überprüfen Sie den Standort. Informationen zu ExpressRoute-Partnern und ExpressRoute Direct-Konnektivität zwischen Peeringstandorten finden Sie unter [ExpressRoute-Partner und Peeringstandorte](expressroute-locations-providers.md#partners).

### <a name="2-order-connectivity-or-configure-expressroute-direct"></a>2. Bestellen einer Verbindung oder Konfigurieren von ExpressRoute Direct

Bestellen Sie beim Dienstanbieter eine Verbindung, oder konfigurieren Sie ExpressRoute Direct.

#### <a name="expressroute-partner-model"></a>ExpressRoute-Partnermodell

Fordern Sie Konnektivität vom Service Provider an. Dieser Prozess variiert. Wenden Sie sich an Ihren Konnektivitätsanbieter, um weitere Details zum Anfordern der Konnektivität zu erhalten.

* Wählen Sie einen ExpressRoute-Partner aus.
* Wählen Sie einen Peeringstandort aus.
* Wählen Sie die gewünschte Bandbreite aus.
* Wählen Sie ein Abrechnungsmodell aus.
* Wählen Sie ein Standard- oder Premium-Add-On aus.

#### <a name="expressroute-direct-model"></a>ExpressRoute Direct-Modell

* Zeigen Sie die verfügbare ExpressRoute Direct-Kapazität für alle Peeringstandorte an.
* Reservieren Sie Ports, indem Sie eine ExpressRoute Direct-Ressource in Ihrem Azure-Abonnement erstellen.
* Fordern Sie ein Genehmigungsschreiben an, und bestellen Sie die physischen Verbindungen beim Anbieter der Peeringstandorte.
* Verwenden Sie [Azure Monitor](expressroute-monitoring-metrics-alerts.md#expressroute-direct-metrics), um den Administratorstatus zu aktivieren und die Lichtpegel und physischen Verbindungen anzuzeigen.

### <a name="3-create-an-expressroute-circuit"></a>3. Erstellen Sie eine ExpressRoute-Verbindung.

#### <a name="expressroute-partner-model"></a>ExpressRoute-Partnermodell

Überprüfen Sie, ob der ExpressRoute-Partner in der Lage ist, die gewünschte Konnektivität bereitzustellen. Ihre ExpressRoute-Verbindung wird von dem Moment an berechnet, in dem ein Dienstschlüssel ausgegeben wird. Befolgen Sie die Anweisungen unter [Erstellen einer ExpressRoute-Verbindung](expressroute-howto-circuit-portal-resource-manager.md), um die Leitung zu erstellen.

#### <a name="expressroute-direct-model"></a>ExpressRoute Direct-Modell

Stellen Sie sicher, dass die physische Verbindung und der Administratorstatus für beide Verbindungen aktiviert sind. Anleitungen dazu finden Sie unter [Konfigurieren von ExpressRoute Direct](how-to-expressroute-direct-portal.md). Ihre ExpressRoute-Verbindung wird von dem Moment an berechnet, in dem ein Dienstschlüssel ausgegeben wird. Befolgen Sie die Anweisungen unter [Erstellen einer ExpressRoute-Verbindung](expressroute-howto-circuit-portal-resource-manager.md), um die Leitung zu erstellen.

### <a name="4-service-provider-provisions-connectivity"></a>4. Bereitstellung der Konnektivität durch den Dienstanbieter

Dieser Abschnitt gilt nur für das ExpressRoute-Partnermodell:

* Stellen Sie dem Konnektivitätsanbieter den Dienstschlüssel bereit.
* Stellen Sie dem Konnektivitätsanbieter alle erforderlichen Informationen bereit (z. B. die VPN-ID).
* Wenn der Anbieter sich um die Routingkonfiguration kümmert, stellen Sie die erforderlichen Informationen bereit.

Sie können sicherstellen, dass die Leitung erfolgreich eingerichtet wurde, indem Sie den Bereitstellungszustand der ExpressRoute-Leitung über PowerShell, das Azure-Portal oder die Azure CLI überprüfen.

### <a name="5-configure-routing-domains"></a>5. Konfigurieren von Routingdomänen

Konfigurieren Sie Routingdomänen. Wenn Ihr Konnektivitätsanbieter die Layer 3-Konfiguration verwaltet, wird das Routing für Ihre Verbindung konfiguriert. Wenn Ihr Konnektivitätsanbieter nur Layer 2-Dienste anbietet oder Sie ExpressRoute Direct verwenden, müssen Sie das Routing gemäß den Richtlinien in den Artikeln [Routinganforderungen](expressroute-routing.md) und [Routingkonfiguration](expressroute-howto-routing-classic.md) konfigurieren.

#### <a name="for-azure-private-peering"></a>Für privates Azure-Peering

Aktivieren Sie das private Peering, um eine Verbindung mit VMs und Clouddiensten herzustellen, die im virtuellen Azure-Netzwerk bereitgestellt sind.

* Peeringsubnetz für Pfad 1 (/30)
* Peeringsubnetz für Pfad 2 (/30)
* VLAN-ID für das Peering
* ASN für das Peering
* ExpressRoute-ASN = 12076
* MD5-Hash (optional)

#### <a name="for-microsoft-peering"></a>Für Microsoft-Peering

Aktivieren Sie diese Option, um auf Microsoft-Onlinedienste wie Microsoft 365 zuzugreifen. Darüber hinaus kann über Microsoft-Peering auf alle Azure-PaaS-Dienste zugegriffen werden. Verwenden Sie für die Verbindung mit Microsoft unbedingt einen anderen Proxy/Edge als für die Internetverbindung. Wenn Sie denselben Edge für ExpressRoute und das Internet verwenden, führt das zu asymmetrischem Routing und Konnektivitätsausfällen für Ihr Netzwerk.

* Peeringsubnetz für Pfad 1 (/30) – muss eine öffentliche IP-Adresse sein
* Peeringsubnetz für Pfad 2 (/30) – muss eine öffentliche IP-Adresse sein
* VLAN-ID für das Peering
* ASN für das Peering
* Angekündigte Präfixe – müssen Präfixe öffentlicher IP-Adressen sein
* Kunden-ASN (optional, wenn abweichend von Peering-ASN)
* RIR/IRR für IP- und ASN-Validierung
* ExpressRoute-ASN = 12076
* MD5-Hash (optional)

### <a name="6-start-using-the-expressroute-circuit"></a>6. Nutzen der ExpressRoute-Leitung

* Sie können virtuelle Azure-Netzwerke mit Ihrer ExpressRoute-Leitung verbinden, um die Konnektivität zwischen dem lokalen Netzwerk und dem virtuellen Azure-Netzwerk einzurichten. Informationen dazu finden Sie unter [Verbinden eines virtuellen Netzwerks mit einer ExpressRoute-Verbindung](expressroute-howto-linkvnet-arm.md). Diese VNets können sich im gleichen Azure-Abonnement wie die ExpressRoute-Verbindung oder aber in einem anderen Abonnement befinden.
* Stellen Sie über ein Microsoft-Peering eine Verbindung mit Azure-Diensten und Microsoft Cloud Services her.

##  <a name="expressroute-partner-circuit-provisioning-states"></a><a name="expressroute-circuit-provisioning-states"></a>Bereitstellungszustände der Leitungen von ExpressRoute-Partnern

Im folgenden Abschnitt werden die verschiedenen ExpressRoute-Leitungszustände für das ExpressRoute-Partnermodell beschrieben.
Jede Leitung eines ExpressRoute-Partners kann zwei Zustände aufweisen:

* **ServiceProviderProvisioningState** gibt den Zustand seitens des Konnektivitätsanbieters an. Mögliche Zustände: *NichtBereitgestellt*, *Bereitstellung* oder *Bereitgestellt*. Die ExpressRoute-Leitung muss sich im Zustand „Provisioned“ befinden, damit das Peering konfiguriert werden kann. **Dieser Zustand gilt nur für ExpressRoute-Partnerleitungen und wird in den Eigenschaften einer ExpressRoute Direct-Leitung nicht angezeigt**.

* Der **Status** gibt den Bereitstellungszustand bei Microsoft an. Wenn Sie eine ExpressRoute-Leitung erstellen, ist diese Eigenschaft auf „Aktiviert“ festgelegt.

### <a name="possible-states-of-an-expressroute-circuit"></a>Mögliche Zustände einer ExpressRoute-Verbindung

In diesem folgenden Abschnitt werden die möglichen Zustände einer ExpressRoute-Leitung beschrieben, die im ExpressRoute-Partnermodell erstellt wurde.

**Zum Zeitpunkt der Erstellung:**

Von der ExpressRoute-Verbindung werden während der Ressourcenerstellung folgende Zustände gemeldet.

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

**Der Konnektivitätsanbieter stellt die Verbindung gerade bereit**

Von der ExpressRoute-Verbindung werden folgende Zustände gemeldet, während der Konnektivitätsanbieter an der Bereitstellung der Verbindung arbeitet.

```output
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

**Der Konnektivitätsanbieter hat die Bereitstellung abgeschlossen**

Von der ExpressRoute-Verbindung werden folgende Zustände gemeldet, sobald der Konnektivitätsanbieter die Verbindung erfolgreich bereitgestellt hat.

```output
ServiceProviderProvisioningState : Provisioned
Status                           : Enabled
```

**Der Konnektivitätsanbieter hebt die Bereitstellung der Leitung auf**

Wenn die Bereitstellung der ExpressRoute-Verbindung aufgehoben werden muss, werden von der Verbindung die folgenden Zustände gemeldet, sobald der Dienstanbieter die Bereitstellung vollständig aufgehoben hat.

```output
ServiceProviderProvisioningState : NotProvisioned
Status                           : Enabled
```

Sie können sie bei Bedarf erneut aktivieren oder PowerShell-Cmdlets ausführen, um die Verbindung zu löschen.  

> [!IMPORTANT]
> Eine Verbindung kann nicht gelöscht werden, wenn „ServiceProviderProvisioningState“ den Zustand „Provisioning“ oder „Provisioned“ aufweist. Der Konnektivitätsanbieter muss die Bereitstellung der Verbindung aufheben, bevor sie gelöscht werden kann. Microsoft stellt die Verbindung weiterhin in Rechnung, bis die ExpressRoute-Verbindungsressource in Azure gelöscht wird.
> 

## <a name="routing-session-configuration-state"></a>Konfigurationszustand der Routingsitzung

Durch den BGP-Bereitstellungszustand wird gemeldet, ob die BGP-Sitzung in Microsoft Edge aktiviert wurde. Der Zustand muss aktiviert sein, um privates oder Microsoft-Peering zu verwenden.

Insbesondere beim Microsoft-Peering sollte der BGP-Sitzungszustand überprüft werden. Zusätzlich zum BGP-Bereitstellungszustand gibt es einen weiteren Zustand: *advertised public prefixes state*. Der Zustand der angekündigten öffentlichen Präfixe muss *configured* lauten, damit die BGP-Sitzung aktiv ist und das Routing durchgängig funktioniert. 

Wenn der Zustand der angekündigten öffentlichen Präfixe *validation needed* lautet, ist die BGP-Sitzung nicht aktiviert, da die angekündigten Präfixe keiner AS-Nummer in einer der Routingregistrierungen entsprachen.

> [!IMPORTANT]
> Wenn der Zustand der angekündigten öffentlichen Präfixe *manual validation* lautet, müssen Sie ein Supportticket beim [Microsoft-Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) öffnen, nachweisen, dass Sie der Besitzer der angekündigten IP-Adressen sind, und die zugeordnete autonome Systemnummer angeben.
> 
> 

## <a name="next-steps"></a>Nächste Schritte

* Konfigurieren Sie Ihre ExpressRoute-Verbindung.
  
  * [Erstellen Sie eine ExpressRoute-Verbindung.](expressroute-howto-circuit-arm.md)
  * [Konfigurieren des Routings](expressroute-howto-routing-arm.md)
  * [Verknüpfen eines VNet mit einer ExpressRoute-Verbindung](expressroute-howto-linkvnet-arm.md)