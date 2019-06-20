---
title: Verwenden von öffentlichen IP-Adressen nach einem Failover mit Azure Site Recovery | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie öffentliche IP-Adressen mit Azure Site Recovery und Azure Traffic Manager für die Notfallwiederherstellung und Migration einrichten.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: 1f20818f0b899eede9fff05d71e98c8bffb94b0a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62101949"
---
# <a name="set-up-public-ip-addresses-after-failover"></a>Einrichten von öffentlichen IP-Adressen nach einem Failover

Öffentliche IP-Adressen ermöglichen Internetressourcen die eingehende Kommunikation mit Azure-Ressourcen. Öffentliche IP-Adressen ermöglichen Azure-Ressourcen auch die ausgehende Kommunikation mit dem Internet und öffentlichen Azure-Diensten mit einer der Ressource zugewiesenen IP-Adresse.
- Eingehende Kommunikation aus dem Internet an die Ressource, z.B. Azure Virtual Machines (VM), Azure Application Gateways, Azure Load Balancers, Azure VPN Gateways und weitere. Sie können mit einigen Ressourcen wie z.B. virtuellen Computern aus dem Internet auch dann kommunizieren, wenn einem virtuellem Computer keine öffentliche IP-Adresse zugewiesen ist, sofern der virtuelle Computer Teil eines Lastenausgleich-Back-End-Pools und dem Lastenausgleich eine öffentliche IP-Adresse zugewiesen ist.
- Ausgehende Konnektivität mit dem Internet über eine vorhersagbare IP-Adresse. Beispielsweise kann ein virtueller Computer, obwohl ihm keine öffentliche IP-Adresse zugewiesen ist, ausgehend mit dem Internet kommunizieren, weil seine Netzwerkadresse von Azure standardmäßig in eine nicht vorhersagbare öffentliche Adresse übersetzt wird. Indem Sie Ressourcen eine öffentliche IP-Adresse zuweisen, wissen Sie, welche IP-Adresse für die ausgehende Verbindung verwendet wird. Eine solche Adresse ist zwar vorhersehbar, kann sich aber ändern – je nach ausgewählter Zuweisungsmethode. Weitere Informationen finden Sie unter [Erstellen einer öffentlichen IP-Adresse](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address). Weitere Informationen zu ausgehenden Verbindungen von Azure-Ressourcen finden Sie unter [Grundlegendes zu ausgehenden Verbindungen in Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

In Azure Resource Manager ist eine öffentliche IP-Adresse eine Ressource, die über eigene Eigenschaften verfügt. Sie können eine öffentliche IP-Adressressource beispielsweise einer der folgenden Ressourcen zuordnen:

* Netzwerkschnittstellen für virtuelle Computer
* Load Balancer mit Internetzugriff
* VPN-Gateways
* Anwendungsgateways

In diesem Artikel wird beschrieben, wie Sie öffentliche IP-Adressen mit Site Recovery verwenden können.

## <a name="public-ip-address-assignment-using-recovery-plan"></a>Zuweisung öffentlicher IP-Adressen mithilfe eines Wiederherstellungsplans

Öffentliche IP-Adressen einer Produktionsanwendung **können bei einem Failover nicht beibehalten werden**. Arbeitsauslastungen, die als Teil eines Failoverprozesses entstehen, müssen einer in der Zielregion verfügbaren öffentlichen Azure-IP-Ressource zugewiesen werden. Dieser Schritt kann entweder manuell erfolgen oder wird mithilfe von Wiederherstellungsplänen automatisiert. Ein Wiederherstellungsplan fasst Computer in Wiederherstellungsgruppen zusammen. Dies unterstützt Sie beim definieren eines systematischen Wiederherstellungsprozesses. Sie können mit einem Wiederherstellungsplan die Reihenfolge vorgeben und mithilfe von Azure Automation-Runbooks für Failover in Azure oder Skripts die in jedem Schritt erforderlichen Aktionen automatisieren.

Die Einrichtung erfolgt wie folgt:
- Erstellen Sie einen [Wiederherstellungsplan](../site-recovery/site-recovery-create-recovery-plans.md#create-a-recovery-plan), und gruppieren Sie Ihre Arbeitsauslastungen nach Bedarf im Plan.
- Passen Sie den Plan an, indem Sie einen Schritt zum Anfügen einer öffentlichen IP-Adresse mit [Azure Automation-Runbooks](../site-recovery/site-recovery-runbook-automation.md#customize-the-recovery-plan)-Skripten für die VM mit dem Failover hinzufügen.

 
## <a name="public-endpoint-switching-with-dns-level-routing"></a>Wechseln des öffentlichen Endpunkts mit Routing auf DNS-Ebene

Azure Traffic Manager ermöglicht das Routing auf DNS-Ebene zwischen Endpunkten und kann Sie beim [Senken Ihrer RTOs](../site-recovery/concepts-traffic-manager-with-site-recovery.md#recovery-time-objective-rto-considerations) für ein DR-Szenario unterstützen. 

Weitere Informationen zu Failoverszenarios mit Traffic Manager:
1. [Failover von einem lokalen Standort nach Azure](../site-recovery/concepts-traffic-manager-with-site-recovery.md#on-premises-to-azure-failover) mit Traffic Manager 
2. [Failover von Azure nach Azure](../site-recovery/concepts-traffic-manager-with-site-recovery.md#azure-to-azure-failover) mit Traffic Manager 

Die Einrichtung erfolgt wie folgt:
- Erstellen Sie ein [Traffic Manager-Profil](../traffic-manager/traffic-manager-create-profile.md).
- Erstellen Sie mithilfe der **prioritätsbasierten** Routingmethode zwei Endpunkte. Den **primären** Endpunkt für die Quelle und den **Failover**-Endpunkt für Azure. **Primary** erhält die Priorität 1 und **Failover** die Priorität 2.
- Der **primäre** Endpunkt kann [Azure](../traffic-manager/traffic-manager-endpoint-types.md#azure-endpoints) oder eine [externe Quelle](../traffic-manager/traffic-manager-endpoint-types.md#external-endpoints) sein, je nachdem, ob Ihre Quellumgebung sich innerhalb oder außerhalb von Azure befindet.
- Der **Failover**-Endpunkt wird als **Azure**-Endpunkt erstellt. Verwenden Sie eine **statisch öffentliche IP-Adresse**, da diese bei einem Notfallereignis einen nach außen gerichteten Endpunkt für Traffic Manager darstellt.

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zu [Traffic Manager mit Azure Site Recovery](../site-recovery/concepts-traffic-manager-with-site-recovery.md).
- Informieren Sie sich ausführlicher über [Traffic Manager-Routingmethoden](../traffic-manager/traffic-manager-routing-methods.md).
- Informieren Sie sich ausführlicher über [Wiederherstellungspläne](site-recovery-create-recovery-plans.md) zum Automatisieren des Anwendungsfailovers.
