---
title: Was ist Azure Application Gateway?
description: Es wird beschrieben, wie Sie eine Azure Application Gateway-Instanz verwenden können, um den Webdatenverkehr für Ihre Anwendung zu verwalten.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 08/26/2020
ms.author: victorh
ms.openlocfilehash: 52bc2a20be0d7113583b7f23cc569dbb9503d364
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397245"
---
# <a name="what-is-azure-application-gateway"></a>Was ist Azure Application Gateway?

Azure Application Gateway ist ein Lastenausgleich für Webdatenverkehr, mit dem Sie eingehenden Datenverkehr für Ihre Webanwendungen verwalten können. Herkömmliche Lastenausgleichsmodule sind auf der Transportschicht (OSI-Schicht 4 – TCP und UDP) aktiv und leiten Datenverkehr basierend auf der IP-Quelladresse und dem dazugehörigen Port an eine IP-Zieladresse und an den entsprechenden Port weiter.

Application Gateway kann Routingentscheidungen auf der Grundlage zusätzlicher Attribute einer HTTP-Anforderung treffen. Beispiele für solche Attribute wären etwa der URI-Pfad oder Hostheader. So können Sie beispielsweise Datenverkehr basierend auf der eingehenden URL weiterleiten. Falls die eingehende URL also `/images` enthält, können Sie Datenverkehr an eine bestimmte Gruppe von Servern (einen so genannten Pool) weiterleiten, die für Bilder konfiguriert sind. Falls die URL `/video` enthält, wird dieser Datenverkehr an einen anderen Pool weitergeleitet, der für Videos optimiert ist.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Diese Art des Routings wird als Lastenausgleich auf Anwendungsebene (OSI-Schicht 7) bezeichnet. Per Azure Application Gateway kann das URL-basierte Routing und noch mehr durchgeführt werden.

>[!NOTE]
> Azure stellt eine Sammlung aus vollständig verwalteten Lastenausgleichslösungen für Ihre Szenarios bereit. Falls Sie einen Layer 4-Lastenausgleich mit hoher Leistung und geringer Latenz benötigen, helfen Ihnen die Informationen unter [Was versteht man unter Azure Load Balancer?](../load-balancer/load-balancer-overview.md) weiter. Wenn Sie nach Informationen zum globalen DNS-Lastenausgleich suchen, hilft Ihnen der Artikel [Was ist Traffic Manager?](../traffic-manager/traffic-manager-overview.md) weiter. Für Ihre End-to-End-Szenarien kann es vorteilhaft sein, diese Lösungen zu kombinieren.
>
> Einen Vergleich der Azure-Lastenausgleichsoptionen finden Sie unter [Übersicht über Lastenausgleichsoptionen in Azure](/azure/architecture/guide/technology-choices/load-balancing-overview).

## <a name="features"></a>Features

Informationen zu Application Gateway-Features finden Sie unter [Azure Application Gateway-Funktionen](features.md).

## <a name="pricing-and-sla"></a>Preise und SLA

Informationen zu den Preisen von Application Gateway finden Sie unter [Application Gateway – Preise](https://azure.microsoft.com/pricing/details/application-gateway/).

SLA-Informationen zu Application Gateway finden Sie unter [SLA für Application Gateway](https://azure.microsoft.com/support/legal/sla/application-gateway/v1_2/).

## <a name="whats-new"></a>Neuigkeiten

Informationen zu den Neuerungen in Azure Application Gateway finden Sie unter [Azure-Updates](https://azure.microsoft.com/updates/?category=networking&query=Application%20Gateway).

## <a name="next-steps"></a>Nächste Schritte

Je nach Ihren Anforderungen und der vorhandenen Umgebung können Sie eine Application Gateway-Testinstanz über das Azure-Portal, mithilfe von Azure PowerShell oder unter Verwendung der Azure-Befehlszeilenschnittstelle erstellen.

- [Schnellstart: Weiterleiten von Webdatenverkehr per Azure Application Gateway – Azure-Portal](quick-create-portal.md)
- [Quickstart: Direct web traffic with Azure Application Gateway – Azure PowerShell](quick-create-powershell.md) (Schnellstart: Weiterleiten von Webdatenverkehr per Azure Application Gateway – Azure PowerShell)
- [Schnellstart: Weiterleiten von Webdatenverkehr per Azure Application Gateway: Azure CLI](quick-create-cli.md)