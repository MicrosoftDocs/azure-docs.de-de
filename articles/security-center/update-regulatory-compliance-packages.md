---
title: Verwenden des Dashboards für die Einhaltung gesetzlicher Bestimmungen in Azure Security Center
description: Hier erfahren Sie, wie Sie gesetzliche Standards auf dem Dashboard für die Einhaltung gesetzlicher Bestimmungen in Security Center hinzufügen und entfernen.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: 3fca96c6cf11e0b35cd830fe4c0d8ccac8e66bd2
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99548863"
---
# <a name="customize-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>Anpassen der Standards in Ihrem Dashboard für die Einhaltung gesetzlicher Bestimmungen

Azure Security Center vergleicht die Konfiguration Ihrer Ressourcen kontinuierlich mit den Anforderungen von Branchenstandards, Vorschriften und Benchmarks. Das **Dashboard für die Einhaltung gesetzlicher Bestimmungen** bietet Erkenntnisse zu Ihrem Compliancestatus basierend auf der Erfüllung bestimmter Complianceanforderungen.


## <a name="how-are-regulatory-compliance-standards-represented-in-security-center"></a>Wie werden die Standards für die Einhaltung gesetzlicher Bestimmungen in Azure Security Center dargestellt?

Branchenstandards, gesetzliche Standards und Benchmarks werden im Security Center im Dashboard für die Einhaltung gesetzlicher Bestimmungen dargestellt. Jeder Standard kann als eine in Azure Policy definierte Initiative verstanden werden.

Fügen Sie der Verwaltungsgruppe oder dem Abonnement auf der Seite **Sicherheitsrichtlinie** ein Compliancestandard hinzu, um Compliancedaten als zugeordnete Bewertungen in Ihrem Dashboard anzuzeigen. Weitere Informationen zu Azure Policy und Initiativen finden Sie unter [Arbeiten mit Sicherheitsrichtlinien](tutorial-security-policy.md).

Nachdem Sie dem ausgewählten Bereich einen Standard oder Vergleichstest zugewiesen haben, wird der Standard in Ihrem Dashboard für die Einhaltung gesetzlicher Bestimmungen mit allen zugeordneten Compliancedaten in Form von Bewertungen angezeigt. Für alle Standards, die Sie zugewiesen haben, können Sie einen zusammenfassenden Bericht herunterladen.

Microsoft überwacht die gesetzlichen Standards selbst und passt die Pakete im Laufe der Zeit entsprechend an. Wenn Microsoft neue Inhalte für die Initiative veröffentlicht,werden diese in Ihrem Dashboard automatisch als neue Richtlinien, die Regeln im Standard entsprechen, hinzugefügt.


## <a name="what-regulatory-compliance-standards-are-available-in-security-center"></a>Welche Standards für die Einhaltung gesetzlicher Bestimmungen sind im Azure Security Center verfügbar?

Standardmäßig ist jedem Abonnement der **Azure Security-Vergleichstest** zugewiesen. Hierbei handelt es sich um von Microsoft erstellte, Azure-spezifische Richtlinien zu bewährten Methoden für Sicherheit und Compliance, die auf allgemeinen Complianceframeworks basieren. [Weitere Informationen zum Azure-Sicherheitsvergleichstest](../security/benchmarks/introduction.md)

Sie können auch beispielsweise folgende Standards hinzufügen:

- NIST SP 800-53 R4
- SWIFT CSP CSCF-v2020
- UK Official und UK NHS
- Canada Federal PBMM
- Azure CIS 1.1.0

Standards werden dem Dashboard hinzugefügt, sobald Sie verfügbar werden.


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>Hinzufügen eines gesetzlichen Standards in Ihrem Dashboard

In den folgenden Schritten wird erklärt, wie Sie ein Paket hinzufügen, um Ihre Compliance mit einem der unterstützten gesetzlichen Standards zu überwachen.

> [!NOTE]
> Um dem Dashboard Standards hinzufügen zu können, muss Azure Defender für das Abonnement aktiviert sein. Zudem haben nur Benutzer, die Besitzer oder Mitwirkende an Richtlinien sind, die erforderlichen Berechtigungen zum Hinzufügen von Compliancestandards. 

1. Wählen Sie auf der Seitenleiste in Security Center **Einhaltung gesetzlicher Bestimmungen** aus, um das Dashboard für die Einhaltung gesetzlicher Bestimmungen zu öffnen. Hier sehen Sie die Compliancestandards, die den aktuell ausgewählten Abonnements zugeordnet sind.   

1. Wählen Sie oben auf der Seite **Konformitätsrichtlinien verwalten** aus. Dadurch wird die Seite für die Richtlinienverwaltung geöffnet.

1. Wählen Sie das Abonnement oder die Verwaltungsgruppe aus, für das bzw. die Sie den Status der Einhaltung gesetzlicher Bestimmungen verwalten möchten. 

    > [!TIP]
    > Es wird empfohlen, den höchsten Bereich auszuwählen, für den der Standard gilt, damit Compliancedaten für alle geschachtelten Ressourcen aggregiert und nachverfolgt werden. 

1. Wenn Sie die für Ihre Organisation relevanten Standards hinzufügen möchten, klicken Sie auf **Weitere Standards hinzufügen**. 

1. Auf der Seite **Standards zur Einhaltung gesetzlicher Bestimmungen hinzufügen** können Sie nach verfügbaren Standards suchen, z. B.:

    - **NIST SP 800-53 R4**
    - **NIST SP 800 171 R2**
    - **SWIFT CSP CSCF v2020**
    - **UKO und UK NHS**
    - **Canada Federal PBMM**
    - **HIPAA HITRUST**
    - **Azure CIS 1.1.0**
    
    ![Hinzufügen von Compliancestandards zum Dashboard für die Einhaltung gesetzlicher Bestimmungen in Azure Security Center](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. Klicken Sie auf **Hinzufügen**, und geben Sie alle notwendigen Details für die jeweilige Initiative ein, z. B. Bereich, Parameter und Wartung.

1. Wählen Sie auf der Seitenleiste in Security Center erneut **Einhaltung gesetzlicher Bestimmungen** aus, um zum Dashboard für die Einhaltung gesetzlicher Bestimmungen zurückzukehren.

    Ihr neuer Standard wird jetzt in der Liste der Branchen- und gesetzlichen Standards aufgeführt. 

    > [!NOTE]
    > Es kann einige Stunden dauern, bis ein neu hinzugefügter Standard im Compliance-Dashboard angezeigt wird.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Dashboard für die Einhaltung gesetzlicher Bestimmungen" lightbox="./media/security-center-compliance-dashboard/compliance-dashboard.png":::

## <a name="removing-a-standard-from-your-dashboard"></a>Entfernen eines Standards aus Ihrem Dashboard

Falls einer der angegebenen gesetzlichen Standards für Ihre Organisation nicht relevant ist, ist es problemlos möglich, ihn aus der Benutzeroberfläche zu entfernen. Auf diese Weise können Sie das Dashboard für die Einhaltung gesetzlicher Bestimmungen weiter anpassen und nur die Standards nutzen, die für Sie zutreffen.

Entfernen Sie einen Standard wie folgt:

1. Wählen Sie im Menü von Security Center **Sicherheitsrichtlinie** aus.

1. Wählen Sie das relevante Abonnement aus, aus dem Sie einen Standard entfernen möchten.

    > [!NOTE]
    > Sie können einen Standard aus einem Abonnement entfernen, aber nicht aus einer Verwaltungsgruppe. 

    Die Seite mit der Sicherheitsrichtlinie wird geöffnet. Für das ausgewählte Abonnement werden die Standardrichtlinie, die Branchen- und gesetzlichen Standards sowie alle von Ihnen erstellten benutzerdefinierten Initiativen angezeigt.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="Entfernen eines gesetzlichen Standards aus Ihrem Dashboard für die Einhaltung gesetzlicher Bestimmungen in Azure Security Center":::

1. Wählen Sie für den Standard, den Sie entfernen möchten, die Option **Deaktivieren** aus. Ein Bestätigungsfenster wird angezeigt.

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="Vergewissern, dass der ausgewählte gesetzliche Standard wirklich entfernt werden soll":::

1. Wählen Sie **Ja** aus. Der Standard wird entfernt. 


## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie Informationen zum **Hinzufügen von Compliancestandards** zum Überwachen Ihrer Compliance mit gesetzlichen und Branchenstandards erhalten.

Verwandte Informationen finden Sie auf den folgenden Seiten:

- [Einführung zum Azure Security-Vergleichstest](../security/benchmarks/introduction.md)
- [Security Center-Dashboard für die Einhaltung gesetzlicher Bestimmungen](security-center-compliance-dashboard.md) – Erfahren Sie, wie Sie Ihre Daten über die Einhaltung gesetzlicher Vorschriften mit Security Center und externen Tools verfolgen und exportieren.
- [Arbeiten mit Sicherheitsrichtlinien](tutorial-security-policy.md)