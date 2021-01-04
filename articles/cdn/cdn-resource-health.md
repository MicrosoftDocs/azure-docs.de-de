---
title: Überwachen der Integrität von Azure CDN-Ressourcen | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie die Integrität Ihrer Azure CDN-Ressourcen mit Azure Resource Health überwachen.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: bf23bd89-35b2-4aca-ac7f-68ee02953f31
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 81c92b6ef5bdf89391364bc238f55200eb3679f6
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92778167"
---
# <a name="monitor-the-health-of-azure-cdn-resources"></a>Überwachen der Integrität von Azure CDN-Ressourcen
  
Die Integrität von Azure CDN-Ressourcen ist Teil von [Azure Resource Health](../service-health/resource-health-overview.md).  Mit Azure Resource Health können Sie die Integrität von CDN-Ressourcen überwachen und erhalten praktische Anweisungen zur Problembehandlung.

>[!IMPORTANT] 
>Die Integrität von Azure CDN-Ressourcen berücksichtigt derzeit nur die Integrität von globalen CDN-Übermittlungs- und -API-Funktionen.  Die Azure CDN-Ressourcenintegrität überprüft keine einzelnen CDN-Endpunkte.
>
>Die Signale, die die Azure CDN-Ressourcenintegrität empfängt, können bis zu 15 Minuten verzögert sein.

## <a name="how-to-find-azure-cdn-resource-health"></a>Suchen der Azure CDN-Ressourcenintegrität

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com)zu Ihrem CDN-Profil.

2. Klicken Sie auf die Schaltfläche **Settings** .

    ![Schaltfläche „Einstellungen“](./media/cdn-resource-health/cdn-profile-settings.png)

3. Klicken Sie unter *Support und Problembehandlung* auf **Ressourcenintegrität** .

    ![Ressourcenintegrität für CDN](./media/cdn-resource-health/cdn-resource-health3.png)

>[!TIP] 
>Die CDN-Ressourcen werden auch auf der Kachel *Ressourcenintegrität* auf dem Blatt *Hilfe und Support* aufgeführt.  Sie können *Hilfe und Support* schnell aufrufen, indem Sie auf das markierte **?** in der rechten oberen Ecke des Portals klicken.
>
> ![Hilfe und Support](./media/cdn-resource-health/cdn-help-support.png)

## <a name="azure-cdn-specific-messages"></a>Azure CDN-spezifische Nachrichten

Die Status für die Azure CDN-Ressourcenintegrität werden unten aufgeführt.

|`Message` | Empfohlene Maßnahme |
|---|---|
|Einer Ihrer CDN-Endpunkte wurde möglicherweise beendet, entfernt oder falsch konfiguriert | Einer Ihrer CDN-Endpunkte wurde möglicherweise beendet, entfernt oder falsch konfiguriert.|
|Leider ist der CDN-Verwaltungsdienst momentan nicht verfügbar. | Überprüfen Sie diese Seite auf Statusaktualisierungen. Wenn das Problem nach der erwarteten Behebungsdauer weiterhin auftritt, wenden Sie sich an den Support.|
|Leider kann es aufgrund von aktuellen Problemen mit einigen unserer CDN-Anbieter zu einer Beeinträchtigung Ihrer CDN-Punkte kommen | Überprüfen Sie diese Seite auf Statusaktualisierungen. Im Problembehandlungstool erhalten Sie Informationen zum Testen der Quelle und des CDN-Endpunkts. Wenn das Problem nach der erwarteten Behebungsdauer weiterhin auftritt, wenden Sie sich an den Support. |
|Leider kommt es momentan zu Verzögerungen bei der Weitergabe von Konfigurationsänderungen für CDN-Endpunkte | Überprüfen Sie diese Seite auf Statusaktualisierungen. Wenn die Konfigurationsänderungen nach der erwarteten Behebungsdauer nicht vollständig weitergegeben werden, wenden Sie sich an den Support.|
|Leider gibt es momentan Probleme beim Laden des zusätzlichen Portals | Überprüfen Sie diese Seite auf Statusaktualisierungen. Wenn das Problem nach der erwarteten Behebungsdauer weiterhin auftritt, wenden Sie sich an den Support.|
Leider kommt es momentan zu Problemen mit einigen unserer CDN-Anbieter | Überprüfen Sie diese Seite auf Statusaktualisierungen. Wenn das Problem nach der erwarteten Behebungsdauer weiterhin auftritt, wenden Sie sich an den Support. |

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über Azure Resource Health](../service-health/resource-health-overview.md)
- [Problembehandlung bei der CDN-Komprimierung](./cdn-troubleshoot-compression.md)
- [Problembehandlung bei 404-Fehlern](./cdn-troubleshoot-endpoint.md)