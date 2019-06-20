---
title: 'Umstellen von Verbindungen vom klassischen Modell auf das Resource Manager-Modell – ExpressRoute: Azure | Microsoft-Dokumentation'
description: Übersicht über das Bridging zwischen dem klassischen Bereitstellungsmodell und dem Resource Manager-Bereitstellungsmodell.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: dfa2bbc735a79555da0421f64ca644adbd7a1701
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60363826"
---
# <a name="moving-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>Umstellen von ExpressRoute-Verbindungen vom klassischen Bereitstellungsmodell auf das Resource Manager-Bereitstellungsmodell
Dieser Artikel enthält eine Übersicht über die Umstellung einer Azure ExpressRoute-Verbindung vom klassischen Bereitstellungsmodell auf das Azure Resource Manager-Bereitstellungsmodell.

Über eine einzelne ExpressRoute-Verbindung können Sie eine Verbindung mit virtuellen Netzwerken herstellen, die mit dem klassischen Bereitstellungsmodell oder mit dem Resource Manager-Bereitstellungsmodell bereitgestellt wurden. Eine ExpressRoute-Verbindung kann jetzt unabhängig von der Erstellungsmethode mit virtuellen Netzwerken beider Bereitstellungsmodelle verknüpft werden.

![Eine ExpressRoute-Verbindung, die mit virtuellen Netzwerken beider Bereitstellungsmodelle verknüpft ist.](./media/expressroute-move/expressroute-move-1.png)

## <a name="expressroute-circuits-that-are-created-in-the-classic-deployment-model"></a>Mit dem klassischen Bereitstellungsmodell erstellte ExpressRoute-Verbindungen
ExpressRoute-Verbindungen, die im klassischen Bereitstellungsmodell erstellt wurden, müssen zuerst auf das Resource Manager-Bereitstellungsmodell umgestellt werden, um die Konnektivität für das klassische Bereitstellungsmodell und das Resource Manager-Bereitstellungsmodell zu ermöglichen. Die Verbindung wird bei der Umstellung nicht getrennt oder unterbrochen. Alle Verknüpfungen zwischen Verbindung und virtuellem Netzwerk im klassischen Bereitstellungsmodell (innerhalb eines Abonnements und abonnementübergreifend) bleiben erhalten.

Nach erfolgreicher Umstellung entspricht die ExpressRoute-Verbindung in sämtlichen Belangen einer ExpressRoute-Verbindung, die mit dem Resource Manager-Bereitstellungsmodell erstellt wurde. Nun können Sie Verbindungen mit virtuellen Netzwerken im Resource Manager-Bereitstellungsmodell erstellen.

Nach der Umstellung einer ExpressRoute-Verbindung auf das Resource Manager-Bereitstellungsmodell können Sie den Lebenszyklus der ExpressRoute-Verbindung nur noch mit dem Resource Manager-Bereitstellungsmodell verwalten. Das bedeutet, dass das Hinzufügen, Aktualisieren und Löschen von Peerings, das Aktualisieren von Verbindungseigenschaften wie Bandbreite, SKU und Abrechnungstyp und das Löschen von Verbindungen nur im Rahmen des Resource Manager-Bereitstellungsmodells möglich ist. Weitere Informationen zum Verwalten des Zugriffs auf beide Bereitstellungsmodelle finden Sie weiter unten im Abschnitt zu Verbindungen, die mit dem Resource Manager-Bereitstellungsmodell erstellt wurden.

Der Konnektivitätsanbieter muss nicht in die Umstellung einbezogen werden.

## <a name="expressroute-circuits-that-are-created-in-the-resource-manager-deployment-model"></a>Mit dem Resource Manager-Bereitstellungsmodell erstellte ExpressRoute-Verbindungen
Mit dem Resource Manager-Bereitstellungsmodell erstellte ExpressRoute-Verbindungen können über beide Bereitstellungsmodelle zugänglich gemacht werden. Der Zugang über beide Bereitstellungsmodelle kann für alle ExpressRoute-Verbindungen Ihres Abonnements ermöglicht werden.

* ExpressRoute-Verbindungen, die mit dem Resource Manager-Bereitstellungsmodell erstellt wurden, haben standardmäßig keinen Zugriff auf das klassische Bereitstellungsmodell.
* ExpressRoute-Verbindungen, die vom klassischen Bereitstellungsmodell auf das Resource Manager-Bereitstellungsmodell umgestellt wurden, sind standardmäßig über beide Bereitstellungsmodelle zugänglich.
* Eine ExpressRoute-Verbindung hat immer Zugriff auf das Resource Manager-Bereitstellungsmodell, und zwar unabhängig davon, ob sie mit dem Resource Manager-Bereitstellungsmodell oder mit dem klassischen Bereitstellungsmodell erstellt wurde. Das bedeutet, dass Sie anhand der Anleitung unter [Verknüpfen von virtuellen Netzwerken](expressroute-howto-linkvnet-arm.md)Verbindungen mit virtuellen Netzwerken herstellen können, die mit dem Resource Manager-Bereitstellungsmodell erstellt wurden.
* Der Zugriff auf das klassische Bereitstellungsmodell wird in der ExpressRoute-Verbindung durch den Parameter **allowClassicOperations** gesteuert.

> [!IMPORTANT]
> Es gelten alle Kontingente, die auf der Seite [Diensteinschränkungen](../azure-subscription-service-limits.md) dokumentiert sind. Beispiel: Eine Standardverbindung kann insgesamt maximal zehn Verknüpfungen/Verbindungen mit virtuellen Netzwerken für das klassische Bereitstellungsmodell und das Resource Manager-Bereitstellungsmodell umfassen.
> 
> 

## <a name="controlling-access-to-the-classic-deployment-model"></a>Steuern des Zugriffs auf das klassische Bereitstellungsmodell
Sie können für eine einzelne ExpressRoute-Verbindung die Verknüpfung mit virtuellen Netzwerken unter beiden Bereitstellungsmodellen ermöglichen, indem Sie den Parameter **allowClassicOperations** der ExpressRoute-Verbindung festlegen.

Wenn Sie **allowClassicOperations** auf „TRUE“ festlegen, können Sie Verknüpfungen mit virtuellen Netzwerken aus beiden Bereitstellungsmodellen mit der ExpressRoute-Verbindung einrichten. Anhand der Anleitung unter [Verknüpfen virtueller Netzwerke im klassischen Bereitstellungsmodell](expressroute-howto-linkvnet-classic.md)können Sie eine Verknüpfung mit virtuellen Netzwerken unter dem klassischen Bereitstellungsmodell einrichten. Anhand der Anleitung unter [Verknüpfen virtueller Netzwerke im Resource Manager-Bereitstellungsmodell](expressroute-howto-linkvnet-arm.md)können Sie eine Verknüpfung mit virtuellen Netzwerken unter dem Resource Manager-Bereitstellungsmodell einrichten.

Wenn Sie **allowClassicOperations** auf „FALSE“ festlegen, wird der Zugriff auf die Verbindung über das klassische Bereitstellungsmodell blockiert. Die Verknüpfungen mit virtuellen Netzwerken im klassischen Bereitstellungsmodell werden jedoch beibehalten. In diesem Fall wird die ExpressRoute-Verbindung im klassischen Bereitstellungsmodell nicht angezeigt.

## <a name="supported-operations-in-the-classic-deployment-model"></a>Unterstützte Vorgänge im klassischen Bereitstellungsmodell
Wenn **allowClassicOperations** auf „TRUE“ festgelegt ist, werden folgende klassische Vorgänge für eine ExpressRoute-Verbindung unterstützt:

* Abrufen von Informationen zur ExpressRoute-Verbindung
* Erstellen/Aktualisieren/Abrufen/Löschen von VNET-Verknüpfungen mit klassischen virtuellen Netzwerken
* Erstellen/Aktualisieren/Abrufen/Löschen von VNET-Verknüpfungsautorisierungen für abonnementübergreifende Konnektivität

Wenn **allowClassicOperations** auf „TRUE“ festgelegt ist, sind folgende klassische Vorgänge nicht möglich:

* Erstellen/Aktualisieren/Abrufen/Löschen von BGP-Peerings für private Azure-Peerings, öffentliche Azure-Peerings und Microsoft-Peerings
* Löschen von ExpressRoute-Verbindungen

## <a name="communication-between-the-classic-and-the-resource-manager-deployment-models"></a>Kommunikation zwischen dem klassischen Bereitstellungsmodell und dem Resource Manager-Bereitstellungsmodell
Die ExpressRoute-Verbindung fungiert als Brücke zwischen dem klassischen Bereitstellungsmodell und dem Resource Manager-Bereitstellungsmodell. Der Datenverkehr zwischen virtuellen Computern in virtuellen Netzwerken unter dem klassischen Bereitstellungsmodell und virtuellen Computern in virtuellen Netzwerken unter dem Resource Manager-Bereitstellungsmodell wird über die ExpressRoute abgewickelt, wenn beide virtuellen Netzwerke mit der gleichen ExpressRoute-Verbindung verknüpft sind.

Der aggregierte Durchsatz wird durch die Durchsatzkapazität des Gateways des virtuellen Netzwerks beschränkt. In einem solchen Fall gelangt der Datenverkehr nicht in die Netzwerke des Konnektivitätsanbieters oder in Ihre Netzwerke. Der Datenverkehrsfluss zwischen den virtuellen Netzwerken wird vollständig innerhalb des Microsoft-Netzwerks abgewickelt.

## <a name="access-to-azure-public-and-microsoft-peering-resources"></a>Zugreifen auf öffentliche Azure- und Microsoft-Peeringressourcen
Sie können weiterhin unterbrechungsfrei auf Ressourcen zugreifen, die normalerweise per öffentlichem Azure-Peering und Microsoft-Peering verfügbar sind.  

## <a name="whats-supported"></a>Unterstützte Funktionen
In diesem Abschnitt werden die unterstützten Funktionen für ExpressRoute-Verbindungen beschrieben:

* Über eine einzelne ExpressRoute-Verbindung können Sie auf virtuelle Netzwerke zugreifen, die mit dem klassischen Bereitstellungsmodell oder mit dem Resource Manager-Bereitstellungsmodell bereitgestellt wurden.
* Sie können eine ExpressRoute-Verbindung vom klassischen Bereitstellungsmodell auf das Resource Manager-Bereitstellungsmodell umstellen. Nach der Umstellung ist die ExpressRoute-Verbindung in sämtlichen Belangen mit anderen ExpressRoute-Verbindungen identisch, die mit dem Resource Manager-Bereitstellungsmodell erstellt wurden.
* Nur die ExpressRoute-Verbindung kann umgestellt werden. Eine Umstellung von Verbindungsverknüpfungen, virtuellen Netzwerken und VPN-Gateways ist mit diesem Vorgang nicht möglich.
* Nach der Umstellung einer ExpressRoute-Verbindung auf das Resource Manager-Bereitstellungsmodell können Sie den Lebenszyklus der ExpressRoute-Verbindung nur noch mit dem Resource Manager-Bereitstellungsmodell verwalten. Das bedeutet, dass das Hinzufügen, Aktualisieren und Löschen von Peerings, das Aktualisieren von Verbindungseigenschaften wie Bandbreite, SKU und Abrechnungstyp und das Löschen von Verbindungen nur im Rahmen des Resource Manager-Bereitstellungsmodells möglich ist.
* Die ExpressRoute-Verbindung fungiert als Brücke zwischen dem klassischen Bereitstellungsmodell und dem Resource Manager-Bereitstellungsmodell. Der Datenverkehr zwischen virtuellen Computern in virtuellen Netzwerken unter dem klassischen Bereitstellungsmodell und virtuellen Computern in virtuellen Netzwerken unter dem Resource Manager-Bereitstellungsmodell wird über die ExpressRoute abgewickelt, wenn beide virtuellen Netzwerke mit der gleichen ExpressRoute-Verbindung verknüpft sind.
* Abonnementübergreifende Konnektivität wird sowohl im klassischen Bereitstellungsmodell als auch im Resource Manager-Bereitstellungsmodell unterstützt.
* Nachdem Sie eine ExpressRoute-Verbindung vom klassischen Modell auf das Azure Resource Manager-Modell umgestellt haben, können Sie [die mit der ExpressRoute-Verbindung verknüpften virtuellen Netzwerke migrieren](expressroute-migration-classic-resource-manager.md).

## <a name="whats-not-supported"></a>Nicht unterstützte Funktionen
In diesem Abschnitt werden die nicht unterstützten Funktionen für ExpressRoute-Verbindungen beschrieben:

* Verwalten des Lebenszyklus einer ExpressRoute-Verbindung über das klassische Bereitstellungsmodell.
* Unterstützung der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) für das klassische Bereitstellungsmodell. Für eine Verbindung unter dem klassischen Bereitstellungsmodell können keine RBAC-Vorgänge ausgeführt werden. Jeder Administrator/Co-Administrator des Abonnements kann virtuelle Netzwerke mit der Verbindung verknüpfen oder die Verknüpfung aufheben.

## <a name="configuration"></a>Konfiguration
Folgen Sie den Anweisungen unter [Verschieben von ExpressRoute-Verbindungen vom klassischen zum Resource Manager-Bereitstellungsmodell](expressroute-howto-move-arm.md).

## <a name="next-steps"></a>Nächste Schritte
* [Migrieren der mit der ExpressRoute-Verbindung verknüpften virtuellen Netzwerke vom klassischen Modell zum Azure Resource Manager-Modell](expressroute-migration-classic-resource-manager.md)
* Workflowinformationen finden Sie unter [Bereitstellungsworkflows für ExpressRoute-Verbindungen und Verbindungszustände](expressroute-workflows.md).
* Gehen Sie zum Konfigurieren Ihrer ExpressRoute-Verbindung wie folgt vor:
  
  * [Erstellen Sie eine ExpressRoute-Verbindung.](expressroute-howto-circuit-arm.md)
  * [Konfigurieren des Routings](expressroute-howto-routing-arm.md)
  * [Verknüpfen eines virtuellen Netzwerks mit einer ExpressRoute-Verbindung](expressroute-howto-linkvnet-arm.md)

