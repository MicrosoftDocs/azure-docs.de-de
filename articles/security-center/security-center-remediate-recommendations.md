---
title: Umsetzen von Empfehlungen in Azure Security Center | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie Sie Empfehlungen in Azure Security Center umsetzen, um Ihre Ressourcen zu schützen und Sicherheitsrichtlinien einzuhalten.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/08/2020
ms.author: memildin
ms.openlocfilehash: 3d4b968d0d5e091d72cf718d58b11ade68ddf8c6
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637086"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Umsetzen von Empfehlungen in Azure Security Center

Empfehlungen enthalten Vorschläge dazu, wie Sie Ihre Ressourcen besser schützen können. Sie implementieren eine Empfehlung, indem Sie die in der Empfehlung beschriebenen Schritte zur Bereinigung ausführen.

## <a name="remediation-steps"></a>Schritte zur Bereinigung<a name="remediation-steps"></a>

Nach der Überprüfung aller Empfehlungen müssen Sie entscheiden, welche zuerst umgesetzt werden soll. Zur Priorisierung Ihrer Aktionen empfiehlt sich die Verwendung der [Auswirkung auf die Sicherheitsbewertung](security-center-recommendations.md#monitor-recommendations).

1. Klicken Sie in der Liste auf die gewünschte Empfehlung.

1. Befolgen Sie die Anweisungen im Abschnitt **Schritte zur Bereinigung**. Jede Empfehlung beinhaltet spezifische Anweisungen. Im folgenden Screenshot werden die Schritte angezeigt, die ausgeführt werden müssen, um Anwendungen so zu konfigurieren, dass der Datenverkehr nur über HTTPS zulässig ist.

    ![Empfehlungsdetails](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. Nach Abschluss des Vorgangs wird eine Benachrichtigung angezeigt, in der Sie darüber informiert werden, ob die Umstellung erfolgreich durchgeführt wurde.

## <a name="quick-fix-remediation"></a>Schnelle Problembehebung<a name="one-click"></a>

Die schnelle Problembehebung vereinfacht das Umsetzen von Empfehlungen, ermöglicht Ihnen eine schnelle Verbesserung Ihrer Sicherheitsbewertung und erhöht die Sicherheit in Ihrer Umgebung.

Mithilfe der schnellen Problembehebung können Sie eine Empfehlung schnell für mehrere Ressourcen umsetzen. 

> [!TIP]
> Die schnelle Problembehebung ist nur für bestimmte Empfehlungen verfügbar. Um die Empfehlungen zu finden, die die Option zur schnellen Problembehebung aufweisen, verwenden Sie den dedizierten Filter am Anfang der Liste der Empfehlungen:
> 
> :::image type="content" source="media/security-center-remediate-recommendations/quick-fix-filter.png" alt-text="Verwenden Sie die Filter über der Empfehlungsliste, um Empfehlungen mit der Option zur schnellen Problembehebung zu finden":::

So implementieren Sie eine Empfehlung zur schnellen Problembehebung:

1. Klicken Sie in der Liste der Empfehlungen, die mit **Schnelle Problembehebung** gekennzeichnet sind, auf die entsprechende Empfehlung.

    [![Wählen Sie „Schnelle Problembehebung“ aus.](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png)](media/security-center-remediate-recommendations/security-center-one-click-fix-select.png#lightbox)

1. Wählen Sie auf der Registerkarte **Fehlerhafte Ressourcen** die Ressourcen aus, für die Sie die Empfehlung implementieren möchten, und klicken Sie auf **Wartung ausführen**.

    > [!NOTE]
    > Einige der aufgelisteten Ressourcen sind möglicherweise deaktiviert, da Sie nicht über die entsprechenden Berechtigungen zum Ändern verfügen.

1. Lesen Sie sich im Bestätigungsdialogfeld die Details und Auswirkungen der Korrektur durch.

    ![Schnelle Problembehebung](./media/security-center-remediate-recommendations/security-center-one-click-fix-view.png)

    > [!NOTE]
    > Die Auswirkungen sind in dem grauen Feld im Fenster **Ressourcen warten** aufgelistet, das nach dem Klicken auf **Wartung ausführen** geöffnet wird. Hier ist angegeben, welche Änderungen auftreten, wenn Sie mit der schnellen Problembehebung fortfahren.

1. Fügen Sie ggf. die relevanten Parameter ein, und genehmigen Sie die Korrektur.

    > [!NOTE]
    > Es kann einige Minuten dauern, bis nach der Korrektur die Ressourcen auf der Registerkarte **Fehlerfreie Ressourcen** angezeigt werden. Zum Anzeigen der Korrekturaktionen sehen Sie sich das [Aktivitätsprotokoll](#activity-log) an.

1. Nach Abschluss des Vorgangs wird eine Benachrichtigung angezeigt, in der Sie darüber informiert werden, ob die Umstellung erfolgreich durchgeführt wurde.

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>Protokollierung der schnellen Problembehebung im Aktivitätsprotokoll <a name="activity-log"></a>

Beim Korrekturvorgang wird ein Vorlagenbereitstellungs- oder REST PATCH-API-Aufruf verwendet, um die Konfiguration auf die Ressource anzuwenden. Diese Vorgänge werden im [Azure-Aktivitätsprotokoll](../azure-resource-manager/management/view-activity-logs.md) protokolliert.


## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument wurde beschrieben, wie Sie Empfehlungen in Security Center umsetzen. Weitere Informationen zu Security Center finden Sie in den folgenden Themen:

* [Festlegen von Sicherheitsrichtlinien in Azure Security Center:](tutorial-security-policy.md) Erfahren Sie, wie Sie Sicherheitsrichtlinien für Ihre Azure-Abonnements und -Ressourcengruppen konfigurieren.
* [Überwachen der Sicherheitsintegrität in Azure Security Center:](security-center-monitoring.md) Erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
