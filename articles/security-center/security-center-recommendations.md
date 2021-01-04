---
title: Sicherheitsempfehlungen in Azure Security Center
description: In diesem Dokument erfahren Sie, wie Sicherheitsempfehlungen in Azure Security Center Ihnen helfen, Ihre Azure-Ressourcen zu schützen und Ihre Sicherheitsrichtlinien einzuhalten.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: c0952e0f267f9c2511a2e80fa7106958d8dd0ee5
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791884"
---
# <a name="security-recommendations-in-azure-security-center"></a>Sicherheitsempfehlungen in Azure Security Center 
In diesem Thema wird erläutert, wie Sie die Empfehlungen in Azure Security Center anzeigen und nutzen können, mit denen Sie Ihre Azure-Ressourcen schützen.


## <a name="what-are-security-recommendations"></a>Was sind Sicherheitsempfehlungen?

Empfehlungen sind Maßnahmen, die Sie ergreifen sollten, um Ihre Ressourcen zu schützen.

Azure Security Center analysiert in regelmäßigen Abständen den Sicherheitsstatus der Azure-Ressourcen, um mögliche Sicherheitsrisiken zu erkennen. Anschließend erhalten Sie Empfehlungen dazu, wie Sie diese Sicherheitsrisiken beheben können.

Jede Empfehlung beinhaltet Folgendes:

- Eine kurze Problembeschreibung
- Die Schritte zur Bereinigung, die zum Implementieren der Empfehlung ausgeführt werden müssen
- Die betroffenen Ressourcen

## <a name="monitor-recommendations"></a>Überwachen von Empfehlungen<a name="monitor-recommendations"></a>

Security Center analysiert den Sicherheitsstatus Ihrer Ressourcen, um mögliche Sicherheitsrisiken zu identifizieren. 

1. Öffnen Sie im Menü von Security Center die Seite **Empfehlungen** , um die für Ihre Umgebung geltenden Empfehlungen anzuzeigen. Die Empfehlungen sind nach Sicherheitssteuerungen gruppiert.

      ![Nach Sicherheitssteuerung gruppierte Empfehlungen](./media/security-center-recommendations/view-recommendations.png)

1. Erweitern Sie eine Steuerung, und wählen Sie eine bestimmte Empfehlung aus, um die Seite mit den Empfehlungsdetails anzuzeigen.

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="Seite mit den Empfehlungsdetails" lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    Inhalt der Seite:

    1. Die Schaltflächen **Erzwingen** und **Ablehnen** für unterstützte Empfehlungen (siehe [Verhindern von Fehlkonfigurationen mit den Optionen zum Erzwingen/Ablehnen für Empfehlungen](prevent-misconfigurations.md))
    1. **Angabe des Schweregrads**
    1. **Aktualisierungsintervall** (sofern relevant) 
    1. **Beschreibung** : Eine kurze Beschreibung des Problems
    1. **Schritte zur Bereinigung** : Eine Beschreibung der manuellen Schritte, die erforderlich sind, um das Sicherheitsproblem für die betroffenen Ressourcen zu beheben. Für Empfehlungen unter „Schnellkorrektur“ können Sie **Korrekturlogik anzeigen** auswählen, bevor Sie die vorgeschlagene Lösung auf die Ressourcen anwenden. 
    1. **Betroffene Ressourcen** : Die Ressourcen sind in Registerkarten unterteilt:
        - **Fehlerfreie Ressourcen** : Relevante Ressourcen, die entweder nicht beeinträchtigt sind oder bei denen das Problem bereits behoben wurde.
        - **Fehlerhafte Ressourcen** : Ressourcen, die weiterhin vom identifizierten Problem betroffen sind.
        - **Nicht anwendbare Ressourcen** : Ressourcen, für die die Empfehlung keine definitive Antwort geben kann. Auf dieser Registerkarte werden auch Gründe für die jeweilige Ressource angegeben. 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="Nicht anwendbare Ressourcen mit Gründen":::
    1. Aktionsschaltflächen zum Beheben über die Empfehlung oder Auslösen einer Logik-App

## <a name="preview-recommendations"></a>Vorschau der Empfehlungen

Empfehlungen, die als **Vorschau** gekennzeichnet sind, werden nicht in die Berechnungen Ihrer Sicherheitsbewertung einbezogen.

Sie sollten nach Möglichkeit weiterhin korrigiert werden, damit sie nach Ablauf des Vorschauzeitraums zu Ihrer Bewertung beitragen.

Beispiel für eine Vorschauempfehlung:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Empfehlung mit dem Flag „Vorschau“":::
 
## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument wurden Ihnen die Sicherheitsempfehlungen in Security Center vorgestellt. In den folgenden Artikeln finden Sie verwandte Informationen:

- [Umsetzen von Empfehlungen](security-center-remediate-recommendations.md) – In diesem Artikel erfahren Sie, wie Sie Sicherheitsrichtlinien für Ihre Azure-Abonnements und -Ressourcengruppen konfigurieren.
- [Verhindern von Fehlkonfigurationen mit den Optionen zum Erzwingen/Ablehnen für Empfehlungen](prevent-misconfigurations.md).
- [Automatisieren von Reaktionen auf Security Center-Trigger](workflow-automation.md) – In diesem Artikel erfahren Sie, wie Sie Reaktionen auf Empfehlungen automatisieren.
- [Ausschließen einer Ressource aus einer Empfehlung](exempt-resource.md)
- [Sicherheitsempfehlungen: Referenzhandbuch](recommendations-reference.md)