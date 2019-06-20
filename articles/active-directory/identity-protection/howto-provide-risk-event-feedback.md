---
title: Senden von Feedback zu Risikoereignissen in Azure AD Identity Protection – Azure Active Directory
description: Wie und warum Sie Feedback zu Identity Protection-Risikoereignissen senden sollten.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66d53590e89afb1a903b22ff60e32871a1502ada
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65828078"
---
# <a name="how-to-give-risk-feedback-in-azure-ad-identity-protection"></a>Anleitung: Senden von Feedback zu Risikoereignissen in Azure AD Identity Protection

Sie können Feedback zur Risikobewertung durch Azure AD Identity Protection geben. Im folgenden Dokument sind die Szenarios aufgelistet, in denen Sie Feedback zur Azure AD Identity Protection-Risikobewertung geben können, und Sie erfahren, wie wir es integrieren.

## <a name="what-is-a-detection"></a>Was ist eine Erkennung?

Eine Erkennung von Identity Protection ist ein Indikator für verdächtige Aktivität bezüglich des Identitätsrisikos. Diese verdächtigen Aktivitäten werden als Risikoereignisse bezeichnet. Diese identitätsbasierenden Erkennungen können auf Heuristiken und Machine Learning basieren oder von Partnerprodukten stammen. Mit diesen Erkennungen werden Anmelde- und Benutzerrisiko bestimmt.

* Ein Benutzerrisiko entspricht der Wahrscheinlichkeit, dass eine bestimmte Identität kompromittiert wurde.
* Das Anmelderisiko entspricht der Wahrscheinlichkeit, dass eine Anmeldung gefährdet ist (die Anmeldung ist z.B. nicht durch den Besitzer der Identität autorisiert).

## <a name="why-should-i-give-risk-feedback-to-azure-ads-risk-assessments"></a>Warum sollte ich Feedback zu den Risikobewertungen von Azure AD senden? 

Es gibt verschiedene Gründe, warum Sie Feedback zu den Risikobewertungen von Azure AD senden sollten:

1. **Sie haben festgestellt, dass die Benutzer- oder Anmelderisikobewertung von Azure AD falsch ist**. Beispielsweise könnte eine im Bericht „Riskante Anmeldungen“ aufgeführte Anmeldung gutartig und alle darauf bezogenen Erkennungen falsch positiv gewesen sein.
1. **Sie haben festgestellt, dass die Benutzer- oder Anmelderisikobewertung von Azure AD richtig ist**. Beispielsweise könnte eine im Bericht „Riskante Anmeldungen“ aufgeführte Anmeldung tatsächlich bösartig gewesen sein, und Sie möchten Azure AD bestätigen, dass alle Erkennungen bezüglich dieser Anmeldung richtig positiv sind.
1. **Sie haben das Risiko für diesen Benutzer außerhalb von Azure AD Identity Protection bereinigt** und möchten, dass die Risikostufe des Benutzers aktualisiert wird.

## <a name="how-does-azure-ad-use-my-risk-feedback"></a>Wie verwendet Azure AD mein Risikofeedback?

Azure AD verwendet Ihr Feedback, um das Risiko des zugrunde liegenden Benutzers bzw. der Anmeldung zu aktualisieren. Dieses Feedback hilft dabei, den Endbenutzer zu schützen. Sobald Sie beispielsweise bestätigen, dass eine Anmeldung kompromittiert wurde, setzt Azure AD direkt das Risiko des Benutzers und das aggregierte Risiko der Anmeldungen (nicht das Echtzeitrisiko) auf „Hoch“. Wenn Sie diesen Benutzer in Ihrer Benutzerrisikorichtlinie den Benutzern mit hohem Risiko zuordnen, bei denen erzwungen wird, dass sie ihre Kennwörter sicher zurücksetzen, sorgt der Benutzer bei seiner nächsten Anmeldung automatisch für Abhilfe.

## <a name="how-should-i-give-risk-feedback-and-what-happens-under-the-hood"></a>Wie soll ich Risikofeedback senden, und was geschieht im Hintergrund?

Hier finden Sie Szenarios und Mechanismen zum Senden von Risikofeedback an Azure AD.

| Szenario | Wie können Sie Feedback senden? | Was geschieht im Hintergrund? | Notizen |
| --- | --- | --- | --- |
| **Anmeldung nicht kompromittiert (falsch positiv)** <br> Der Bericht „Riskante Anmeldungen“ zeigt eine riskante Anmeldung [Risikostatus = Gefährdet] an, aber diese Anmeldung war nicht kompromittiert. | Wählen Sie die Anmeldung aus, und klicken Sie auf „Anmeldung als sicher bestätigen“. | Azure AD hebt das aggregierte Risiko der Anmeldung auf [Risikostatus = Als sicher bestätigt; Risikostufe (aggregiert) = -] und macht die Auswirkung auf das Benutzerrisiko rückgängig. | Die Option „Anmeldung als sicher bestätigen“ ist derzeit nur im Bericht „Riskante Anmeldungen“ verfügbar. |
| **Anmeldung kompromittiert (richtig positiv)** <br> Der Bericht „Riskante Anmeldungen“ zeigt eine gefährdete Anmeldung [Risikostatus = Gefährdet] mit geringem Risiko [Risikostufe (aggregiert) = Niedrig] an, und diese Anmeldung wurde tatsächlich kompromittiert. | Wählen Sie die Anmeldung aus, und klicken Sie auf „Anmeldung als gefährdet bestätigen“. | Azure AD setzt das aggregierte Risiko der Anmeldung auf „Hoch“ [Risikostatus = Gefährdung bestätigen; Risikostufe = Hoch]. | Die Option „Anmeldung als gefährdet bestätigen“ ist derzeit nur im Bericht „Riskante Anmeldungen“ verfügbar. |
| **Benutzer kompromittiert (richtig positiv)** <br> Der Bericht „Riskante Benutzer“ zeigt einen gefährdeten Benutzer [Risikostatus = Gefährdet] mit geringem Risiko [Risikostufe = Niedrig] an, und dieser Benutzer wurde tatsächlich kompromittiert. | Wählen Sie den Benutzer aus, und klicken Sie auf „Benutzergefährdung bestätigen“. | Azure AD setzt das Benutzerrisiko auf „Hoch“ [Risikostatus = Gefährdung bestätigen; Risikostufe = Hoch] und fügt eine neue Erkennung „Benutzergefährdung durch Administrator bestätigt“ hinzu. | Die Option „Benutzergefährdung bestätigen“ ist derzeit nur im Bericht „Riskante Anmeldungen“ verfügbar. <br> Die Erkennung „Benutzergefährdung durch Administrator bestätigt“ wird im Bericht „Riskante Benutzer“ auf der Registerkarte „Risikoereignisse ohne Bezug zu einer Anmeldung“ angezeigt. |
| **Benutzer außerhalb von Azure AD Identity Protection bereinigt (Richtig positiv + Bereinigt)** <br> Der Bericht „Riskante Benutzer“ zeigt einen gefährdeten Benutzer an, und Sie haben den Benutzer anschließend außerhalb von Azure AD Identity Protection bereinigt. | 1. Wählen Sie den Benutzer aus, und klicken Sie auf „Benutzergefährdung bestätigen“. (Dieser Prozess bestätigt Azure AD, dass der Benutzer tatsächlich kompromittiert wurde.) <br> 2. Warten Sie, bis die Risikostufe des Benutzers auf „Hoch“ gesetzt wird. (Mit dem Warten räumen Sie Azure AD die erforderliche Zeit ein, um das obige Feedback in das Risikomodul aufzunehmen.) <br> 3. Wählen Sie den Benutzer aus, und klicken Sie auf „Benutzerrisiko verwerfen“. (Dieser Prozess bestätigt Azure AD, dass der Benutzer nicht mehr kompromittiert ist.) |  Azure AD hebt das Benutzerrisiko auf [Risikostatus = Verworfen; Risikostufe = -] und schließt das Risiko für alle vorhandenen Anmeldungen mit aktivem Risiko. | Beim Klicken auf „Benutzerrisiko verwerfen“ werden alle Risiken für den Benutzer und für in der Vergangenheit liegende Anmeldungen geschlossen. Diese Aktion kann nicht rückgängig gemacht werden. |
| **Benutzer nicht kompromittiert (falsch positiv)** <br> Der Bericht „Riskante Benutzer“ zeigt einen gefährdeten Benutzer an, aber der Benutzer ist nicht kompromittiert. | Wählen Sie den Benutzer aus, und klicken Sie auf „Benutzerrisiko verwerfen“. (Dieser Prozess bestätigt Azure AD, dass der Benutzer nicht kompromittiert ist.) | Azure AD hebt das Benutzerrisiko auf [Risikostatus = Verworfen; Risikostufe = -]. | Beim Klicken auf „Benutzerrisiko verwerfen“ werden alle Risiken für den Benutzer und für in der Vergangenheit liegende Anmeldungen geschlossen. Diese Aktion kann nicht rückgängig gemacht werden. |
| Ich möchte das Benutzerrisiko schließen, aber ich bin mir nicht sicher, ob der Benutzer kompromittiert/sicher ist. | Wählen Sie den Benutzer aus, und klicken Sie auf „Benutzerrisiko verwerfen“. (Dieser Prozess bestätigt Azure AD, dass der Benutzer nicht mehr kompromittiert ist.) | Azure AD hebt das Benutzerrisiko auf [Risikostatus = Verworfen; Risikostufe = -]. | Beim Klicken auf „Benutzerrisiko verwerfen“ werden alle Risiken für den Benutzer und für in der Vergangenheit liegende Anmeldungen geschlossen. Diese Aktion kann nicht rückgängig gemacht werden. Sie sollten den Benutzer durch Klicken auf „Kennwort zurücksetzen“ bereinigen oder ihn auffordern, auf sichere Weise seine Anmeldeinformationen zurückzusetzen / zu ändern. |

Feedback zu Benutzerrisikoereignissen in Identity Protection wird offline verarbeitet, und die Aktualisierung kann einige Zeit dauern. Die Spalte zum Status der Risikoverarbeitung gibt den aktuellen Status der Feedbackverarbeitung an.

![Status der Risikoverarbeitung für Bericht „Riskante Benutzer“](./media/howto-provide-risk-event-feedback/risky-users-provide-feedback.png)

## <a name="next-steps"></a>Nächste Schritte

[Referenz für Risikoereignisse in Azure Active Directory Identity Protection](risk-events-reference.md)