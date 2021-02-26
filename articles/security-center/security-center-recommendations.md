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
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 3b2f111f83dbd731b69671e58d4bf9dc648a596f
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526502"
---
# <a name="security-recommendations-in-azure-security-center"></a>Sicherheitsempfehlungen in Azure Security Center 

In diesem Thema wird erläutert, wie Sie die Empfehlungen in Azure Security Center anzeigen und nutzen können, mit denen Sie Ihre Azure-Ressourcen schützen.


## <a name="what-are-security-recommendations"></a>Was sind Sicherheitsempfehlungen?

Azure Security Center analysiert in regelmäßigen Abständen den Sicherheitsstatus der Azure-Ressourcen, um mögliche Sicherheitsrisiken zu erkennen. Anschließend erhalten Sie Empfehlungen dazu, wie Sie diese Sicherheitsrisiken beheben können.

Empfehlungen sind Maßnahmen, die Sie ergreifen sollten, um Ihre Ressourcen zu härten und zu schützen. 

Jede Empfehlung beinhaltet Folgendes:

- Eine kurze Problembeschreibung
- Die Schritte zur Bereinigung, die zum Implementieren der Empfehlung ausgeführt werden müssen
- Die betroffenen Ressourcen

## <a name="how-does-microsoft-decide-what-needs-securing-and-hardening"></a>Wie entscheidet Microsoft, was gesichert und gehärtet werden muss?

Security Center-Empfehlungen beruhen auf dem Azure Security-Vergleichstest. Fast jede Empfehlung weist eine zugrunde liegende Richtlinie auf, die von einer Anforderung im Vergleichstest abgeleitet ist.

Beim Azure-Sicherheitsvergleichstest handelt es sich um einen von Microsoft erstellten Satz Azure-spezifischer Richtlinien zu bewährten Methoden für Sicherheit und Compliance, die auf allgemeinen Complianceframeworks basieren. Diese weit verbreitete Benchmark basiert auf den Kontrollen des [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) und des [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) und konzentriert sich auf cloudzentrierte Sicherheit. Informieren Sie sich über [Azure Security-Vergleichstests](../security/benchmarks/introduction.md).

Wenn Sie die Details einer Empfehlung überprüfen, ist es häufig hilfreich, die zugrunde liegende Richtlinie zu kennen. Verwenden Sie für jede Empfehlung, die von einer Richtlinie unterstützt wird, den Link **Richtliniendefinition anzeigen** auf der Seite mit den Empfehlungsdetails, um direkt zum Azure Policy-Eintrag für die entsprechende Richtlinie zu gelangen:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Link zur Azure Policy-Seite für eine bestimmte Richtlinie, die eine Empfehlung unterstützt":::

Verwenden Sie diesen Link, um die Richtliniendefinition anzuzeigen und die Bewertungslogik zu überprüfen. 

Bei Betrachtung der Liste der Empfehlungen in unserem [Referenzhandbuch für Sicherheitsempfehlungen](recommendations-reference.md) werden Sie auch Links zu den Seiten mit Richtliniendefinitionen finden:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Zugriff auf die Azure Policy-Seite für eine bestimmte Richtlinie direkt über die Seite mit der Azure Security Center-Empfehlungsreferenz":::

## <a name="monitor-recommendations"></a>Überwachen von Empfehlungen<a name="monitor-recommendations"></a>

Security Center analysiert den Sicherheitsstatus Ihrer Ressourcen, um mögliche Sicherheitsrisiken zu identifizieren. 

1. Öffnen Sie im Menü von Security Center die Seite **Empfehlungen**, um die für Ihre Umgebung geltenden Empfehlungen anzuzeigen. Die Empfehlungen sind nach Sicherheitssteuerungen gruppiert.

    :::image type="content" source="./media/security-center-recommendations/view-recommendations.png" alt-text="Nach Sicherheitssteuerung gruppierte Empfehlungen" lightbox="./media/security-center-recommendations/view-recommendations.png":::

1. Verwenden Sie die optionalen Filter oberhalb der Liste der Empfehlungen, um Empfehlungen zu Ressourcentyp, Schweregrad, Umgebung oder anderen Kriterien zu finden, die für Sie wichtig sind.

    :::image type="content" source="media/security-center-recommendations/recommendation-list-filters.png" alt-text="Filter zum Verfeinern der Liste der Azure Security Center-Empfehlungen":::

1. Erweitern Sie eine Steuerung, und wählen Sie eine bestimmte Empfehlung aus, um die Seite mit den Empfehlungsdetails anzuzeigen.

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="Seite mit den Empfehlungsdetails" lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    Inhalt der Seite:

    1. Für unterstützte Empfehlungen zeigt die obere Symbolleiste eine oder alle der folgenden Schaltflächen an:
        - **Erzwingen** und **Ablehnen** (Weitere Informationen finden Sie unter [Verhindern von Fehlkonfigurationen mit den Optionen zum Erzwingen/Ablehnen für Empfehlungen](prevent-misconfigurations.md).)
        - **Richtliniendefinitionen anzeigen**, um direkt zum Azure Policy-Eintrag der zugrunde liegenden Richtlinie zu gelangen
    1. **Angabe des Schweregrads**
    1. **Aktualisierungsintervall** (sofern relevant)
    1. **Anzahl ausgenommener Ressourcen**: Wenn für diese Empfehlung Ausnahmen vorhanden sind, wird die Anzahl der ausgenommenen Ressourcen angezeigt.
    1. **Beschreibung**: Eine kurze Beschreibung des Problems
    1. **Schritte zur Bereinigung**: Eine Beschreibung der manuellen Schritte, die erforderlich sind, um das Sicherheitsproblem für die betroffenen Ressourcen zu beheben. Für Empfehlungen unter „Schnellkorrektur“ können Sie **Korrekturlogik anzeigen** auswählen, bevor Sie die vorgeschlagene Lösung auf die Ressourcen anwenden. 
    1. **Betroffene Ressourcen**: Die Ressourcen sind in Registerkarten unterteilt:
        - **Fehlerfreie Ressourcen**: Relevante Ressourcen, die entweder nicht beeinträchtigt sind oder bei denen das Problem bereits behoben wurde.
        - **Fehlerhafte Ressourcen**: Ressourcen, die weiterhin vom identifizierten Problem betroffen sind.
        - **Nicht anwendbare Ressourcen**: Ressourcen, für die die Empfehlung keine definitive Antwort geben kann. Auf dieser Registerkarte werden auch Gründe für die jeweilige Ressource angegeben. 

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