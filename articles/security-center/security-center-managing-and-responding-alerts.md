---
title: Verwalten von Sicherheitswarnungen in Azure Security Center | Microsoft-Dokumentation
description: In diesem Dokument erfahren Sie, wie Sie Azure Security Center-Funktionen verwenden, um Sicherheitswarnungen zu verwalten und auf diese zu reagieren.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: how-to
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: 75ca0438336825bf8d4bbdc6e08eca109f430fde
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92785917"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center

In diesem Thema wird gezeigt, wie Sie die empfangenen Warnungen anzeigen und verarbeiten, um Ihre Ressourcen zu schützen. 

* Weitere Informationen zu den unterschiedlichen Arten von Warnungen finden Sie unter [Arten von Sicherheitswarnungen](alerts-reference.md).
* Eine Übersicht darüber, wie Security Center Warnungen generiert, finden Sie unter [Erkennen von und Reagieren auf Bedrohungen mit Azure Security Center](security-center-alerts-overview.md).

> [!NOTE]
> Aktivieren Sie Azure Defender, um erweiterte Erkennungen zu aktivieren. Eine kostenlose Testversion ist verfügbar. Wenn Sie ein Upgrade durchführen möchten, wählen Sie in der [Sicherheitsrichtlinie](tutorial-security-policy.md)die Tarifoption aus. Weitere Informationen finden Sie unter [Azure Security Center Preise](security-center-pricing.md).

## <a name="what-are-security-alerts"></a>Was sind Sicherheitswarnungen?
Security Center erfasst, analysiert und vereinigt automatisch Protokolldaten von Ihren Azure-Ressourcen, vom Netzwerk und von verbundenen Partnerlösungen, z.B. Lösungen zum Schutz von Firewalls und Endpunkten, um echte Bedrohungen zu erkennen und falsch positive Ergebnisse zu reduzieren. Eine Liste mit priorisierten Sicherheitswarnungen wird im Security Center zusammen mit den Informationen angezeigt, die Sie zum schnellen Untersuchen des Problems benötigen. Außerdem sind Empfehlungen zum Reagieren auf einen Angriff vorhanden.

> [!NOTE]
> Weitere Informationen darüber, wie die Erkennungsfunktionen von Security Center funktionieren, finden Sie unter [Erkennen von und Reagieren auf Bedrohungen mit Azure Security Center](security-center-alerts-overview.md#detect-threats).

## <a name="manage-your-security-alerts"></a>Verwalten von Sicherheitswarnungen

1. Auf dem Security Center-Dashboard finden Sie auf der Kachel **Bedrohungsschutz** eine Übersicht über die Warnungen.

    ![Kachel „Sicherheitswarnungen“ in Security Center](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

1. Klicken Sie auf die Kachel, um weitere Details zu den Warnungen anzuzeigen.

   ![Die Sicherheitswarnungen in Security Center](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Zum Filtern der angezeigten Warnungen klicken Sie auf **Filter** und wählen auf dem daraufhin geöffneten Blatt **Filter** die Filteroptionen aus, die Sie anwenden möchten. Die Liste wird entsprechend dem ausgewählten Filter aktualisiert. Diese Filterung kann sehr nützlich sein. Es könnte beispielsweise sein, dass Sie während der Untersuchung einer möglichen Sicherheitsverletzung im System die Sicherheitswarnungen überprüfen möchten, die in den letzten 24 Stunden aufgetreten sind.

    ![Filtern von Warnungen in Security Center](./media/security-center-managing-and-responding-alerts/security-center-filter-alerts.png)

## <a name="respond-to-security-alerts"></a>Reagieren auf Sicherheitswarnungen

1. Klicken Sie in der Liste **Sicherheitswarnungen** auf eine Sicherheitswarnung. Die betroffenen Ressourcen und die Schritte, die Sie zum Abwehren eines Angriffs ausführen müssen, werden angezeigt.

    ![Reagieren auf Sicherheitswarnungen](./media/security-center-managing-and-responding-alerts/security-center-alert.png)

1. Klicken Sie nach dem Überprüfen der Informationen auf eine Ressource, die angegriffen wurde.

    Im linken Bereich der Seite mit der Sicherheitswarnung werden allgemeine Informationen zur Sicherheitswarnung angezeigt: Titel, Schweregrad, Status, Zeitpunkt der Aktivität, Beschreibung der verdächtigen Aktivität und die betroffene Ressource. Neben der betroffenen Ressource finden Sie die Azure-Tags für die Ressource. Verwenden Sie diese Tags, um den Organisationskontext der Ressource abzuleiten, wenn Sie die Warnung untersuchen.

    Auf der rechten Seite befindet sich die Registerkarte **Warnungsdetails** , die detaillierte Informationen zur Warnung anzeigt, die Ihnen beim Untersuchen des Problems helfen: IP-Adressen, Dateien, Prozesse und vieles mehr.
     
    ![Vorschläge für Maßnahmen bei Sicherheitswarnungen](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    Im rechten Bereich finden Sie auch die Registerkarte **Aktion ausführen**. Über diese Registerkarte können Sie weitere Aktionen in Bezug auf die Sicherheitswarnung ausführen. Folgende Aktionen sind möglich:
    - *Auswirkungen der Bedrohung minimieren* : Stellt manuelle Schritte zur Behebung dieser Sicherheitswarnung bereit.
    - *Künftige Angriffe verhindern* : Gibt Sicherheitsempfehlungen, um die Angriffsfläche zu verringern, den Sicherheitsstatus zu erhöhen und so künftige Angriffe zu verhindern.
    - *Automatische Reaktion auslösen* : Bietet die Möglichkeit, eine Logik-App als Reaktion auf diese Sicherheitswarnung auszulösen.
    - *Ähnliche Warnungen unterdrücken* : Bietet die Möglichkeit, zukünftige Warnungen mit ähnlichen Merkmalen zu unterdrücken, wenn die Warnung für Ihre Organisation nicht relevant ist.

    ![Registerkarte „Aktion ausführen“](./media/security-center-managing-and-responding-alerts/alert-take-action.png)




## <a name="see-also"></a>Weitere Informationen

In diesem Dokument haben Sie erfahren, wie Sie Sicherheitswarnungen anzeigen. Verwandte Informationen finden Sie auf den folgenden Seiten:

- [Konfigurieren von Warnungsunterdrückungsregeln](alerts-suppression-rules.md)
- [Automatisieren der Reaktionen auf Security Center-Trigger](workflow-automation.md)
