---
title: Azure Peering Service – häufig gestellte Fragen (FAQ)
description: Erfahren Sie etwas über häufig gestellte Fragen zu Microsoft Azure Peering Service.
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 95ce90dbbf47ffe527fe6f25704d9cd28b834ea9
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026694"
---
# <a name="peering-service-faq"></a>Peering Service – häufig gestellte Fragen (FAQ)

In diesem Artikel werden die am häufigsten gestellten Fragen zu Azure Peering Service-Verbindungen erläutert.


**F: Wer sind die Zielkunden?**

A. Zielkunden sind Unternehmen, die über das Internet als Transportstrecke eine Verbindung mit der Microsoft-Cloud herstellen.

**F: Können sich Kunden bei mehreren Anbietern für Peering Service registrieren?** 

A. Ja, Kunden können sich bei mehreren Anbietern in derselben Region oder in verschiedenen Regionen für Peering Service registrieren, jedoch nicht für dasselbe Präfix.

**F: Können Kunden pro geografischer Region einen anderen ISP für ihre Standorte auswählen?**

A. Ja, dies ist Kunden möglich. Wählen Sie den Partner-ISP pro Region aus, der Ihren geschäftlichen und betrieblichen Anforderungen am besten entspricht.

**F: Was ist ein Microsoft Edge-PoP?**

A. Es handelt sich dabei um einen physischen Standort, an dem Microsoft mit anderen Netzwerken Verbindungen herstellt. Am Microsoft Edge-PoP-Speicherort werden Dienste wie Azure Front Door und Azure CDN gehostet. Weitere Informationen finden Sie unter [Azure CDN](../cdn/cdn-features.md).

## <a name="peering-service-unique-characteristics"></a>Peering Service: Alleinstellungsmerkmale

**F: Wie unterscheidet sich Peering Service vom normalen Internetzugang?**

A. Partner, die sich bei Microsoft Peering Service registriert haben, arbeiten mit Microsoft zusammen, um optimiertes Routing und zuverlässige Konnektivität mit Microsoft-Diensten zu bieten.  

**F: Wie unterscheidet sich Peering Service von ExpressRoute?**

A. Azure ExpressRoute ist eine private, dedizierte Verbindung von einem oder mehreren Kundenstandorten. Peering Service bietet optimierte öffentliche Konnektivität und unterstützt keine privaten Verbindungen, bietet jedoch auch optimierte Konnektivität für lokale Internetabzweige.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu Peering Service finden Sie unter [Übersicht über Peering Service](about.md).
- Einen Dienstanbieter können Sie unter [Peering Service: Partner und Standorte](location-partners.md) finden.
- Informationen zum Onboarding einer Peering Service-Verbindung finden Sie unter [Onboarding von Peering Service](onboarding-model.md).
- Informationen zum Registrieren einer Peering -Verbindung finden Sie unter [Registrieren einer Peering Service-Verbindung – Azure-Portal](azure-portal.md).
- Informationen zum Messen der Telemetrie finden Sie unter [Messen der Verbindungstelemetrie](measure-connection-telemetry.md).