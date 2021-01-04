---
title: 'Azure ExpressRoute: Zurücksetzen des Verbindungspeerings mithilfe des Azure-Portals'
description: Erfahren Sie, wie Peerings einer Azure ExpressRoute-Verbindung mithilfe des Azure-Portals deaktiviert und aktiviert werden.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.author: duau
ms.openlocfilehash: d4d6b0b0cce4f5304f7c5790ef2bda05633be52f
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581086"
---
# <a name="reset-expressroute-circuit-peerings-use-the-azure-portal"></a>Zurücksetzen von ExpressRoute-Verbindungspeerings im Azure-Portal

In diesem Artikel wird beschrieben, wie Peerings einer ExpressRoute-Verbindung im Azure-Portal deaktiviert und aktiviert werden. Wenn Sie ein Peering deaktivieren, wird die BGP-Sitzung der primären und sekundären Verbindung Ihrer ExpressRoute-Verbindung heruntergefahren. Durch das Peering zu Microsoft geht Konnektivität verloren. Wenn Sie das Peering aktivieren, wird die BGP-Sitzung jeweils auf der primären und sekundären Verbindung Ihrer ExpressRoute-Verbindung gestartet. Sie erhalten über dieses Peering zu Microsoft die Konnektivität zurück. Sie können das Microsoft-Peering und das private Azure-Peering unabhängig voneinander auf einer ExpressRoute-Verbindung aktivieren und deaktivieren. Wenn Sie die Peerings zum ersten Mal auf Ihrer ExpressRoute-Verbindung konfigurieren, werden diese standardmäßig aktiviert.

Es gibt einige Szenarios, in denen das Zurücksetzen Ihrer ExpressRoute-Peerings hilfreich sein kann.
* Testen Sie Ihren Entwurf und die Implementierung der Notfallwiederherstellung. Wenn Sie z.B. über zwei ExpressRoute-Verbindungen verfügen, können Sie die Peerings von einer Verbindung deaktivieren und erzwingen, dass Ihr Netzwerkdatenverkehr ein Failover auf die andere Verbindung ausführt.
* Aktivieren Sie die bidirektionale Weiterleitungserkennung (Bidirectional Forwarding Detection, BFD) für das private Azure-Peering oder Microsoft-Peering Ihrer ExpressRoute-Verbindung. BFD ist standardmäßig im privaten Azure-Peering aktiviert, wenn Ihre ExpressRoute-Verbindung nach dem 1. August 2018 erstellt wurde, und im Microsoft-Peering, wenn Ihre ExpressRoute-Verbindung nach dem 10. Januar 2020 erstellt wurde. Wenn Ihre Verbindung vor diesem Datum erstellt wurde, wurde BFD nicht aktiviert. Sie können BFD aktivieren, indem Sie das Peering deaktivieren und anschließend erneut aktivieren. 

### <a name="sign-in-to-the-azure-portal"></a>Melden Sie sich auf dem Azure-Portal an.

Navigieren Sie in einem Browser zum [Azure-Portal](https://portal.azure.com) , und melden Sie sich mit Ihrem Azure-Konto an.

## <a name="reset-a-peering"></a>Zurücksetzen eines Peerings

1. Wählen Sie die Verbindung aus, deren Peeringkonfiguration Sie ändern möchten.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit-list.png" alt-text="ExpressRoute-Verbindungsliste":::

1. Wählen Sie die Peeringkonfiguration aus, die Sie aktivieren oder deaktivieren möchten.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit.png" alt-text="Übersicht zur ExpressRoute-Verbindung":::

1. Deaktivieren Sie **Peering aktivieren**, und wählen Sie **Speichern** aus, um die Peeringkonfiguration zu deaktivieren.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/disable-peering.png" alt-text="Deaktivieren des privaten Peerings":::

1. Sie können das Peering erneut aktivieren, indem Sie **Peering aktivieren** aktivieren und **Speichern** auswählen.

## <a name="next-steps"></a>Nächste Schritte
Wenn Sie zur Behandlung eines ExpressRoute-Problems Hilfe benötigen, sehen Sie sich folgende Artikel an:
* [Überprüfen der ExpressRoute-Konnektivität](expressroute-troubleshooting-expressroute-overview.md)
* [Beheben von Problemen bei der Netzwerkleistung](expressroute-troubleshooting-network-performance.md)
