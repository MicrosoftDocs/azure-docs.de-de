---
title: Migrieren von VPN Gateways aus dem klassischen Modell zu Resource Manager | Microsoft Docs
description: Diese Seite bietet einen Überblick über das Migrieren von VPN Gateways aus dem klassischen Modell zu Resource Manager.
documentationcenter: na
services: vpn-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: caa8eb19-825a-4031-8b49-18fbf3ebc04e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/06/2020
ms.author: amsriva
ms.openlocfilehash: c9d7fb8be1894ffa5f8c35e16e1ed3aa0949b3ff
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488203"
---
# <a name="vpn-gateway-classic-to-resource-manager-migration"></a>Migrieren von VPN Gateways aus dem klassischen Modell zu Resource Manager
VPN Gateways können jetzt aus dem klassischen Modell zum Resource Manager-Bereitstellungsmodell migriert werden. Informieren Sie sich weiter über [Features und Vorteile von Azure Resource Manager](../azure-resource-manager/management/overview.md). In diesem Artikel wird erklärt, wie man von klassischen Bereitstellungen zu neueren Resource Manager-basierten Modellen migriert. 

VPN Gateways werden als Teil der VNet-Migration vom klassischen Modell zu Resource Manager migriert. Diese Migration erfolgt nacheinander für einzelne VNets. Es gibt keine zusätzlichen Anforderungen in Bezug auf Tools oder Voraussetzungen für die Migration. Die Schritte bei der Migration sind mit der Migration von VNets identisch, und sind auf der Seite [Migrieren von IaaS-Ressourcen](../virtual-machines/migration-classic-resource-manager-ps.md) aufgeführt. Es gibt keine Downtime von Datenpfaden während der Migration. Aus diesem Grund können bestehende Workloads während der Migration ohne Verlust der lokalen Konnektivität weiterhin ausgeführt werden. Die öffentliche IP-Adresse, die dem VPN Gateway zugeordnet ist, verändert sich während des Migrationsvorgangs nicht. Das bedeutet, dass Sie Ihren lokalen Router nicht neu konfigurieren müssen, wenn die Migration abgeschlossen ist.  

Das Modell in Resource Manager unterscheidet sich vom klassischen Modell und besteht aus Gateways der virtuellen Netzwerke, Gateways des lokalen Netzwerks und Verbindungsressourcen. Diese stellen das VPN Gateway selbst dar, den lokalen Standort, der einen lokalen Adressraum und Konnektivität jeweils zwischen den beiden darstellt. Sobald die Migration abgeschlossen ist, wären Ihre Gateways im klassischen Modell nicht verfügbar, und alle Verwaltungsvorgänge auf Gateways der virtuellen Netzwerke und Verbindungsobjekte müssten im Resource Manager-Modell ausgeführt werden.

## <a name="supported-scenarios"></a>Unterstützte Szenarios
Die meisten gängigen VPN-Konnektivitätsszenarios werden von der Migration vom klassischen zum Resource Manager-Modell unterstützt. Zu diesen Szenarios gehören –

* Punkt-zu-Standort-Konnektivität
* Site-to-Site-Konnektivität mit VPN Gateway mit Verbindung zum lokalen Standort
* VNet-zu-VNet-Konnektivität zwischen zwei VNets mithilfe von VPN Gateways
* Mehrere VNets mit Verbindung zum gleichen lokalen Standort
* Konnektivität für mehrere Standorte
* Tunnelerzwingung für aktivierte VNets

Zu nicht unterstützten Szenarios gehören –  

* Ein VNet mit sowohl einem ExpressRoute-Gateway als auch einem VPN-Gateway wird derzeit nicht unterstützt.
* Übertragungsszenarios in denen VM-Erweiterungen mit lokalen Servern verbunden sind. Einschränkungen zur Übertragung mit VPN-Konnektivität sind unten aufgeführt.

> [!NOTE]
> Die Überprüfung von CIDR im Resource Manager-Modell ist strenger als im klassischen Modell. Stellen Sie vor dem Migrieren sicher, dass die entsprechenden klassischen Adressbereiche dem gültigen CIDR-Format entsprechen. CIDR kann mithilfe von allen gängigen CIDR-Validierern überprüft werden. VNets oder lokale Standorte mit ungültigen CIDR-Bereichen beim Migrieren würden einen Fehlerstatus ergeben.
> 
> 

## <a name="vnet-to-vnet-connectivity-migration"></a>Migration der VNet-zu-VNet-Konnektivität
VNet-zu-VNet-Konnektivität im klassischen Modell wurde durch das Erstellen eines lokalen Standorts als Darstellung des verbundenen VNets erreicht. Kunden mussten zwei lokale Standorte erstellen, die die beiden VNets darstellten, die miteinander verbunden werden mussten. Diese wurden dann mit den entsprechenden VNets mithilfe von IPsec-Tunneln verbunden, um eine Verbindung zwischen den beiden VNets herzustellen. Bei diesem Modell bestehen Herausforderungen mit der Verwaltbarkeit, da alle Änderungen im Adressbereich eines VNets auch in der zugehörigen Darstellung der lokalen Standorte durchgeführt werden müssen. Im Resource Manager-Modell ist diese Problemumgehung nicht länger notwendig. Die Verbindung zwischen den beiden VNets kann mithilfe des Verbindungstyps „Vnet2Vnet“ direkt in der Verbindungsressource hergestellt werden. 

![Screenshot der VNet-zu-VNet-Migration.](./media/vpn-gateway-migration/migration1.png)

Während der VNet-Migration stellen wir fest, dass die verbundene Entität zum aktuellen VPN Gateways des VNets ein anderes VNet ist, und stellen sicher, dass die beiden lokalen Standorte, die das andere VNet darstellen, nicht mehr angezeigt werden, sobald die Migration beider VNets abgeschlossen ist. Das klassische Modell mit zwei VPN Gateways, zwei lokalen Standorten und zwei Verbindungen zwischen diesen wird zum Resource Manager-Modell mit zwei VPN Gateways und zwei Verbindungen des Typs „Vnet2Vnet“ transformiert.

## <a name="transit-vpn-connectivity"></a>Übertragung mit VPN-Konnektivität
Sie können VPN Gateways in einer Topologie konfigurieren, damit die lokale Konnektivität für ein VNet durch Verbindung zu einem anderen VNet hergestellt wird, das direkt lokal verbunden ist. Dies ist die Übertragung mit VPN-Konnektivität, bei der Instanzen im ersten VNet mit den lokalen Ressourcen durch eine Verbindung zum VPN Gateway im verbundenen VNet verbunden werden, das direkt lokal verbunden ist. Um diese Konfiguration im klassischen Modell zu erreichen, müssten Sie einen lokalen Standort erstellen, der über aggregierte Präfixe verfügt, die sowohl das verbundene VNet als auch den lokalen Adressbereich darstellen. Dieser lokale Standort als Darstellung wird dann mit dem VNet verbunden, um Transitkonnektivität herzustellen. Bei diesem klassischen Modell bestehen ähnliche Herausforderungen mit der Verwaltbarkeit, da alle Änderungen im lokalen Adressbereich auch am lokalen Standort durchgeführt werden müssen, der das Aggregieren von VNet und lokaler Verbindung darstellt. Die Einführung von BGB-Unterstützung in von Resource Manager unterstützen Gateways vereinfacht die Verwaltbarkeit, nachdem die verbundenen Gateways Routen ohne die manuelle Modifizierung von Präfixen erlernen können.

![Screenshot des Transitroutingszenarios.](./media/vpn-gateway-migration/migration2.png)

Nachdem wir die VNet-zu-VNet-Konnektivität ohne lokale Standorte transformieren, verliert das Übertragungsszenario die lokale Konnektivität für das VNet, das indirekt lokal verbunden ist. Der Verlust der Konnektivität kann durch die folgenden zwei Wege nach Abschluss der Migration ausgeglichen werden – 

* Durch Aktivieren von BGP auf VPN Gateways, die miteinander und lokal verbunden sind. Durch Aktivieren von BGP wird die Konnektivität ohne eine andere Änderung der Konfiguration wiederhergestellt, nachdem zwischen den VNet Gateways Routen gelernt und beworben werden. Beachten Sie, dass die BGP-Option nur unter Standard- oder höheren SKUs verfügbar ist.
* Stellen Sie eine Verbindung vom betroffenen VNet zum lokalen Netzwerkgateway her, das den lokalen Standort darstellt. Das würde auch Änderungen der Einstellungen am lokalen Router erforderlich machen, um den IPsec-Tunnel zu erstellen und zu konfigurieren.

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie Informationen zum VPN-Gateway Migrationssupport erhalten haben, wechseln Sie zu [Migrieren von IaaS-Ressourcen aus dem klassischen Bereitstellungsmodell zu Azure Resource Manager mithilfe von Azure PowerShell](../virtual-machines/migration-classic-resource-manager-ps.md), um anzufangen.