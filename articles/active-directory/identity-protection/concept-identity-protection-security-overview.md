---
title: Sicherheitsübersicht über Azure Active Directory Identity Protection
description: Erfahren Sie, wie Sie mit der Sicherheitsübersicht einen Einblick in den Sicherheitsstatus Ihrer Organisation erhalten.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95c589289d77597be2550673944c8fa21902e0fb
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098466"
---
# <a name="azure-active-directory-identity-protection---security-overview"></a>Azure Active Directory Identity Protection – Sicherheitsübersicht

Mit der [Sicherheitsübersicht](https://aka.ms/IdentityProtectionRefresh) im Azure-Portal erhalten Sie einen Einblick in den Sicherheitsstatus Ihrer Organisation. Damit können Sie mögliche Angriffe erkennen und die Wirksamkeit Ihrer Richtlinien einschätzen.

Die Sicherheitsübersicht ist grob in zwei Abschnitte unterteilt:

- Durch Trends wird im linken Bereich eine Zeitachse der Risiken in Ihrer Organisation dargestellt.
- Im rechten Bereich werden in Kacheln wichtige aktuelle Probleme in Ihrer Organisation hervorgehoben und Vorschläge zu schnellen Maßnahmen angezeigt.

:::image type="content" source="./media/concept-identity-protection-security-overview/01.png" alt-text="Screenshot der Sicherheitsübersicht im Azure-Portal. Zeitachse der Risiken in den Balkendiagrammen. Übersichtsinformationen zu Benutzern und Anmeldungen in den Kacheln." border="false":::
  
## <a name="trends"></a>Trends

### <a name="new-risky-users-detected"></a>Neue Benutzer mit Risiko erkannt

In diesem Diagramm wird die Anzahl neuer riskanter Benutzer angezeigt, die im ausgewählten Zeitraum erkannt wurden. Sie können die Ansicht des Diagramms nach Benutzerrisikostufe (Niedrig, Mittel, Hoch) filtern. Zeigen Sie auf die UTC-Datumswerte, um die Anzahl ermittelter riskanter Benutzer für den jeweiligen Tag anzuzeigen. Durch Klicken auf dieses Diagramm gelangen Sie zum Bericht „Riskante Benutzer“. Um riskante Benutzer zu bereinigen, können Sie deren Kennwort ändern.

### <a name="new-risky-sign-ins-detected"></a>Neue riskante Anmeldungen erkannt

In diesem Diagramm wird die Anzahl riskanter Anmeldungen angezeigt, die im ausgewählten Zeitraum erkannt wurden. Sie können die Ansicht des Diagramms nach Anmelderisikotyp (Echtzeit oder Aggregat) und Risikostufe der Anmeldung (Niedrig, Mittel, Hoch) filtern. Nicht geschützte Anmeldungen sind erfolgreiche Risikoanmeldungen in Echtzeit, bei denen keine Aufforderung zur mehrstufigen Authentifizierung erfolgte. (Hinweis: Offlinerisikoanmeldungen können nicht in Echtzeit durch Richtlinien zum Anmelderisiko geschützt werden.) Zeigen Sie auf die UTC-Datumswerte, um die Anzahl ermittelter riskanter Anmeldevorgänge für den jeweiligen Tag anzuzeigen. Durch Klicken auf dieses Diagramm gelangen Sie zum Bericht „Riskante Anmeldungen“.

## <a name="tiles"></a>Kacheln
 
### <a name="high-risk-users"></a>Benutzer mit hohem Risiko

Auf der Kachel „Benutzer mit hohem Risiko“ wird die aktuelle Anzahl der Benutzer mit hoher Wahrscheinlichkeit eines Identitätsmissbrauchs angezeigt. Diese sollten mit höchster Priorität untersucht werden. Durch Klicken auf die Kachel „Benutzer mit hohem Risiko“ erfolgt eine Weiterleitung zu einer gefilterten Ansicht des Berichts „Riskante Benutzer“, in der nur Benutzer mit der Risikostufe „Hoch“ angezeigt werden. Mithilfe dieses Berichts erhalten Sie weitere Informationen und können diese Benutzer durch eine Kennwortzurücksetzung bereinigen.

:::image type="content" source="./media/concept-identity-protection-security-overview/02.png" alt-text="Screenshot der Sicherheitsübersicht im Azure-Portal mit angezeigten Kacheln für Benutzer mit hohem Risiko und mit mittlerem Risiko sowie für andere Risikofaktoren" border="false":::

### <a name="medium-risk-users"></a>Benutzer mit mittlerem Risiko
Auf der Kachel „Benutzer mit mittlerem Risiko“ wird die aktuelle Anzahl der Benutzer mit mittlerer Wahrscheinlichkeit eines Identitätsmissbrauchs angezeigt. Durch Klicken auf die Kachel „Benutzer mit mittlerem Risiko“ erfolgt eine Weiterleitung zu einer gefilterten Ansicht des Berichts „Riskante Benutzer“, in der nur Benutzer mit der Risikostufe „Mittel“ angezeigt werden. Mithilfe dieses Berichts können Sie weitere Untersuchungen durchführen und diese Benutzer bereinigen.

### <a name="unprotected-risky-sign-ins"></a>Nicht geschützte riskante Anmeldungen

Auf der Kachel „Nicht geschützte riskante Anmeldungen“ wird die Anzahl der erfolgreichen Risikoanmeldungen in Echtzeit in der letzten Woche angezeigt, die nicht durch eine Richtlinie für bedingten Zugriff, eine Identity Protection-Risikorichtlinie oder eine benutzerbasierte mehrstufige Authentifizierung blockiert wurden und bei denen keine Aufforderung zur mehrstufigen Authentifizierung erfolgte. Hierbei handelt es sich um potenziell kompromittierte Anmeldungen, die erfolgreich waren und nicht durch mehrstufige Authentifizierung überprüft wurden. Um diese Anmeldungen in Zukunft zu schützen, müssen Sie eine Richtlinie zum Anmelderisiko anwenden. Durch Klicken auf die Kachel „Nicht geschützte riskante Anmeldungen“ werden Sie zum Blatt für die Konfiguration von Richtlinien zum Anmelderisiko weitergeleitet, auf dem Sie die Richtlinie zum Anmelderisiko so konfigurieren können, dass bei einer Anmeldung mit einer angegebenen Risikostufe eine mehrstufige Authentifizierung erforderlich ist.

### <a name="legacy-authentication"></a>Legacyauthentifizierung

Auf der Kachel „Legacyauthentifizierung“ wird die Anzahl der risikobehafteten Legacyauthentifizierungen in Ihrer Organisation in der letzten Woche angezeigt. Ältere Authentifizierungsprotokolle unterstützen keine modernen Sicherheitsmethoden wie z.B. eine mehrstufige Authentifizierung. Um Legacyauthentifizierungen zu verhindern, können Sie eine Richtlinie für bedingten Zugriff anwenden. Durch Klicken auf die Kachel „Legacyauthentifizierung“ gelangen Sie zu „Identity Secure Score“.

### <a name="identity-secure-score"></a>Identity Secure Score

Identity Secure Score misst und vergleicht Ihren Sicherheitsstatus mit Branchenmustern. Wenn Sie auf die Kachel „Identity Secure Score (Vorschau)“ klicken, werden Sie zum Blatt „Identity Secure Score“ weitergeleitet, auf dem Sie weitere Informationen zur Verbesserung des Sicherheitsstatus erhalten.

## <a name="next-steps"></a>Nächste Schritte

- [Was bedeutet Risiko?](concept-identity-protection-risks.md)

- [Verfügbare Richtlinien zum Mindern von Risiken](concept-identity-protection-policies.md)
