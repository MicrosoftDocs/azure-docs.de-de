---
title: Umstellen eines öffentlichen Peerings auf das Microsoft-Peering – Azure ExpressRoute | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie Ihr öffentliches Peering über ExpressRoute auf Microsoft-Peering umstellen.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/12/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 6b2bce6b488698db0a72c9a17f67c2555c6afa5b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60883133"
---
# <a name="move-a-public-peering-to-microsoft-peering"></a>Umstellen von öffentlichem Peering auf Microsoft-Peering

Dieser Artikel erklärt, wie Sie ohne Ausfallzeiten von einer öffentlichen Peeringkonfiguration auf Microsoft-Peering umsteigen. ExpressRoute unterstützt die Verwendung von Microsoft-Peering und Routenfiltern für Azure-PaaS-Dienste wie Azure Storage und Azure SQL-Datenbank. Für den Zugriff auf PaaS- und SaaS-Dienste von Microsoft ist nun nur noch eine einzelne Routingdomäne erforderlich. Mit Routenfiltern können Sie selektiv die PaaS-Dienstpräfixe für die zu verwendenden Azure-Regionen ankündigen. Weitere Informationen zu Routingdomänen und Peerings finden Sie unter [ExpressRoute-Verbindungen und Routingdomänen](expressroute-circuit-peerings.md).

## <a name="before"></a>Voraussetzungen

* Um eine Verbindung mit Microsoft-Peering herstellen zu können, müssen Sie die NAT einrichten und verwalten. Die NAT wird unter Umständen von Ihrem Konnektivitätsanbieter eingerichtet und verwaltet. Wenn Sie im Rahmen von Microsoft-Peering auf die Azure-PaaS- und Azure-SaaS-Dienste zugreifen möchten, muss der NAT-IP-Adresspool die richtige Größe haben. Weitere Informationen zur NAT für ExpressRoute finden Sie unter [NAT-Anforderungen für Microsoft-Peering](expressroute-nat.md#nat-requirements-for-microsoft-peering).

* Wenn Sie öffentliches Peering verwenden und derzeit über IP-Netzwerkregeln für öffentliche IP-Adressen verfügen, mit denen auf [Azure Storage](../storage/common/storage-network-security.md) oder [Azure SQL-Datenbank](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md) zugegriffen wird, müssen Sie sicherstellen, dass der mit Microsoft-Peering konfigurierte NAT-IP-Adressenpool in der Liste der öffentlichen IP-Adressen für das Azure Storage- oder Azure SQL-Konto enthalten ist.

* Führen Sie die Schritte in diesem Artikel in der angegebenen Reihenfolge aus, damit es bei der Umstellung auf Microsoft-Peering zu keinen Ausfällen kommt.

## <a name="create"></a>1. Erstellen von Microsoft-Peering

Falls Microsoft-Peering noch nicht erstellt wurde, gehen Sie gemäß einem der folgenden Artikel vor, um es zu erstellen. Wenn Ihr Konnektivitätsanbieter verwaltete Layer 3-Dienste anbietet, können Sie ihn mit der Aktivierung des Microsoft-Peerings für Ihre Verbindung beauftragen.

  * [Erstellen von Microsoft-Peering über das Azure-Portal](expressroute-howto-routing-portal-resource-manager.md#msft)
  * [Erstellen von Microsoft-Peering mithilfe von Azure-PowerShell](expressroute-howto-routing-arm.md#msft)
  * [Erstellen von Microsoft-Peering mithilfe der Azure-Befehlszeilenschnittstelle](howto-routing-cli.md#msft)

## <a name="validate"></a>2. Überprüfen des Aktivierungsstatus von Microsoft-Peering

Vergewissern Sie sich, dass Microsoft-Peering aktiviert ist und die angekündigten öffentlichen Präfixe konfiguriert sind.

  * [Azure-Portal](expressroute-howto-routing-portal-resource-manager.md#getmsft)
  * [Azure PowerShell](expressroute-howto-routing-arm.md#getmsft)
  * [Azure-Befehlszeilenschnittstelle](howto-routing-cli.md#getmsft)

## <a name="routefilter"></a>3. Konfigurieren eines Routenfilters und Anfügen des Routenfilters an die Verbindung

Standardmäßig kündigen neue Microsoft-Peerings keine Präfixe an, solange kein Routenfilter an die Verbindung angefügt wurde. Beim Erstellen einer Routenfilterregel können Sie die Liste der Dienstcommunitys für Azure-Regionen angeben, die Sie für Azure-PaaS-Dienste nutzen möchten, wie im folgenden Screenshot zu sehen:

![Zusammenführen des öffentlichen Peerings](./media/how-to-move-peering/public.png)

Gehen Sie zum Konfigurieren von Routenfiltern gemäß einem der folgenden Artikel vor:

  * [Konfigurieren von Routenfiltern für Microsoft-Peering: Azure-Portal](how-to-routefilter-portal.md)
  * [Konfigurieren von Routenfiltern für Microsoft-Peering: PowerShell](how-to-routefilter-powershell.md)
  * [Konfigurieren von Routenfiltern für Microsoft-Peering: Azure CLI](how-to-routefilter-cli.md)

## <a name="delete"></a>4. Löschen des öffentlichen Peerings

Nachdem Sie sich vergewissert haben, dass das Microsoft-Peering konfiguriert ist und die Präfixe, die Sie nutzen möchten, ordnungsgemäß für Microsoft-Peering angekündigt werden, können Sie das öffentliche Peering löschen. Gehen Sie zum Löschen des öffentlichen Peerings gemäß einem der folgenden Artikel vor:

  * [Löschen des öffentlichen Azure-Peerings über das Azure-Portal](expressroute-howto-routing-portal-resource-manager.md#deletepublic)
  * [Löschen des öffentlichen Azure-Peerings mithilfe von Azure PowerShell](expressroute-howto-routing-arm.md#deletepublic)
  * [Löschen des öffentlichen Azure-Peerings mithilfe der Befehlszeilenschnittstelle](howto-routing-cli.md#deletepublic)
  
## <a name="view"></a>5. Anzeigen der Peerings
  
Im Azure-Portal können Sie eine Liste mit allen ExpressRoute-Leitungen und -Peerings anzeigen. Weitere Informationen finden Sie unter [So zeigen Sie die Details zum Microsoft-Peering an](expressroute-howto-routing-portal-resource-manager.md#getmsft).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen über ExpressRoute finden Sie unter [ExpressRoute – FAQ](expressroute-faqs.md).
