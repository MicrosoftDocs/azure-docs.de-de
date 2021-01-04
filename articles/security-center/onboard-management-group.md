---
title: Durchführen des Onboardings für eine Verwaltungsgruppe in Azure Security Center
description: Es wird beschrieben, wie Sie eine bereitgestellte Azure Policy-Definition verwenden, um Azure Security Center für alle Abonnements einer Verwaltungsgruppe zu aktivieren.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2020
ms.author: memildin
ms.openlocfilehash: 4ecd436b548c29c520a7538970d4d703cc8488d2
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/29/2020
ms.locfileid: "93027566"
---
# <a name="enable-security-center-on-all-subscriptions-in-a-management-group"></a>Aktivieren von Security Center in allen Abonnements einer Verwaltungsgruppe

Sie können Azure Policy verwenden, um Azure Security Center in allen Azure-Abonnements innerhalb derselben Verwaltungsgruppe zu aktivieren. Dies ist einfacher als der individuelle Zugriff darauf über das Portal und funktioniert sogar, wenn die Abonnements unterschiedliche Besitzer haben. 

Führen Sie das Onboarding einer Verwaltungsgruppe und aller zugehörigen Abonnements wie folgt durch:

1. Öffnen Sie Azure Policy als Benutzer mit Berechtigungen vom Typ **Sicherheitsadministrator** , und suchen Sie nach der Definition **Azure Security Center für Ihr Abonnement aktivieren**.

    :::image type="content" source="./media/security-center-get-started/enable-security-center-policy.png" alt-text="Azure Policy-Definition „Azure Security Center für Ihr Abonnement aktivieren“":::

1. Wählen Sie die Option **Zuweisen** aus, und stellen Sie sicher, dass Sie den Bereich auf die Ebene der Verwaltungsgruppe festlegen.

    :::image type="content" source="./media/security-center-get-started/assign-policy.png" alt-text="Zuweisen der Definition „Azure Security Center für Ihr Abonnement aktivieren“":::

    > [!TIP]
    > Mit Ausnahme des Bereichs sind keine weiteren Parameter erforderlich.

1. Wählen Sie **Korrekturtask erstellen** aus, um sicherzustellen, dass alle vorhandenen Abonnements, für die Security Center nicht aktiviert ist, das Onboarding durchgeführt wird.

    :::image type="content" source="./media/security-center-get-started/remediation-task.png" alt-text="Erstellen eines Korrekturtasks für die Azure Policy-Definition „Azure Security Center für Ihr Abonnement aktivieren“":::

1. Wenn die Definition zugewiesen wird, bewirkt sie Folgendes:

    1. Erkennen aller Abonnements in der Verwaltungsgruppe, die noch nicht bei Security Center registriert sind
    1. Kennzeichnen dieser Abonnements als „Nicht konform“
    1. Kennzeichnen von allen registrierten Abonnements als „konform“ (unabhängig davon, ob Azure Defender dafür aktiviert ist)

    Mit dem Korrekturtask wird Security Center dann kostenlos für die nicht konformen Abonnements aktiviert.

> [!IMPORTANT]
> Mit der Richtliniendefinition wird Security Center nur in **vorhandenen** Abonnements aktiviert. Öffnen Sie zum Registrieren von neu erstellten Abonnements die Registerkarte „Konformität“, wählen Sie die relevanten nicht konformen Abonnements aus, und erstellen Sie einen Korrekturtask. Wiederholen Sie diesen Schritt, wenn Sie mindestens ein neues Abonnement mit Security Center überwachen möchten.

## <a name="optional-modifications"></a>Optionale Änderungen

Es gibt verschiedene Möglichkeiten, wie Sie die Azure Policy-Definition ändern können: 

- **Abweichendes Definieren von Konformität** : Mit der bereitgestellten Richtlinie werden alle Abonnements der Verwaltungsgruppe, die noch nicht bei Security Center registriert sind, als „nicht konform“ klassifiziert. Sie können auch angeben, dass dies für alle Abonnements ohne Azure Defender gelten soll.

    Mit der bereitgestellten Definition werden *beide* unten angegebenen Preiseinstellungen als konform festgelegt. Dies bedeutet, dass ein Abonnement, das auf „Standard“ oder „Free“ festgelegt ist, konform ist.

    > [!TIP]
    > Wenn ein Azure Defender-Plan aktiviert wird, wird er so beschrieben, dass die Einstellung „Standard“ gilt. Wenn er deaktiviert ist, gilt „Free“. [Erfahren Sie mehr zu Azure Defender-Plänen](security-center-pricing.md).

    ```
    "existenceCondition": {
        "anyof": [
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "standard"
            },
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "free"
            }
        ]
    },
    ```

    Wenn Sie eine Änderung in den folgenden Wert vornehmen, werden nur Abonnements, die auf „Standard“ festgelegt sind, als konform klassifiziert:

    ```
    "existenceCondition": {
          {
            "field": "microsoft.security/pricings/pricingTier",
            "equals": "standard"
          },
    },
    ```

- **Definieren von einigen Azure Defender-Plänen zum Anwenden beim Aktivieren von Security Center** : Mit der bereitgestellten Richtlinie wird Security Center ohne die optionalen Azure Defender-Pläne aktiviert. Sie können einen oder mehrere davon aktivieren.

    Im Abschnitt `deployment` der bereitgestellten Definition wird der Parameter `pricingTier` verwendet. Standardmäßig ist er auf `free` festgelegt, aber Sie können dies ändern. 


## <a name="next-steps"></a>Nächste Schritte:

Nachdem Sie nun das Onboarding für eine komplette Verwaltungsgruppe durchgeführt haben, aktivieren Sie als Nächstes den erweiterten Schutz von Azure Defender. 

> [!div class="nextstepaction"]
> [Aktivieren von Azure Defender](security-center-pricing.md)
