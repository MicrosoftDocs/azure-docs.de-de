---
title: Azure Active Directory Identity Protection Benachrichtigungen
description: Erfahren Sie, wie Benachrichtigungen Ihre Untersuchungsaktivitäten unterstützen.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 11/09/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9536cf41add73f494bfff451c201d36e951864e3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997663"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory Identity Protection Benachrichtigungen

Von Azure AD Identity Protection werden zwei Arten von automatisierten Benachrichtigungs-E-Mails gesendet, die der Verwaltung von Benutzerrisiko- und Risikoerkennungen dienen:

- E-Mail für erkannte gefährdete Benutzer
- Wöchentliche E-Mail mit Übersicht

Dieser Artikel gibt Ihnen eine Übersicht zu beiden Benachrichtigungs-E-Mails.

## <a name="users-at-risk-detected-email"></a>E-Mail für erkannte gefährdete Benutzer

Als Reaktion auf ein erkanntes gefährdetes Konto erstellt Azure Active Directory Identity Protection eine E-Mail-Warnung mit dem Betreff **Gefährdete Benutzer erkannt**. Die E-Mail enthält einen Link zum Bericht vom Typ **[Benutzer mit Risikokennzeichnung](./overview-identity-protection.md)** . Als bewährte Methode sollten Sie die gefährdeten Benutzer sofort untersuchen.

Durch Konfiguration dieser Warnung können Sie angeben, bei welcher Benutzerrisikostufe die Warnung generiert werden soll. Die E-Mail wird generiert, wenn die angegebene Risikostufe des Benutzers erreicht wird. Wenn Sie beispielsweise die Richtlinie für Warnungen auf ein mittleres Benutzerrisiko festlegen und die Risikobewertung Ihres Benutzers Johann aufgrund eines Echtzeit-Anmelderisikos die mittlere Risikostufe erreicht, erhalten Sie eine E-Mail des Typs „Gefährdete Benutzer erkannt“. Wenn für den Benutzer anschließend erneut Risiken ermittelt werden, aufgrund derer die Risikostufe des Benutzers die festgelegte Risikostufe bzw. eine höhere Risikostufe erreicht, erhalten Sie weitere E-Mails dieses Typs, sobald die Risikobewertung des Benutzers neu berechnet wurde. Wenn für einen Benutzer z. B. am 1. Januar die mittlere Risikostufe erreicht wird und Sie in Ihren Einstellungen festgelegt haben, dass bei mittlerem Risiko eine Warnung ausgegeben werden soll, erhalten Sie eine E-Mail-Benachrichtigung. Wenn für denselben Benutzer am 5. Januar ein weiteres mittleres Risiko ermittelt wird und die Risikobewertung des Benutzers nach der Neuberechnung weiterhin ein mittleres Risiko anzeigt, erhalten Sie eine weitere E-Mail-Benachrichtigung. 

Diese zusätzliche E-Mail-Benachrichtigung wird jedoch nur gesendet, wenn der Zeitpunkt der Risikoermittlung (aufgrund derer die Risikostufe des Benutzers geändert wurde) nach dem Zeitpunkt liegt, an dem die letzte E-Mail gesendet wurde. Beispiel: Ein Benutzer meldet sich am 1. Januar um 5:00 Uhr an, und es liegt kein Echtzeitrisiko vor (aufgrund dieser Anmeldung würde also keine E-Mail generiert). Zehn Minuten später, um 5:10 Uhr, meldet sich der Benutzer erneut an, dieses Mal jedoch mit einem hohen Echtzeitrisiko. Aufgrund dieser Anmeldung würde die Risikostufe des Benutzers in „Hoch“ geändert, und es würde eine E-Mail gesendet. Um 5:15 Uhr wird die Offlinerisikobewertung für die ursprüngliche Anmeldung um 5:00 Uhr durch die Offlinerisikoverarbeitung in ein hohes Risiko geändert. Da der Zeitpunkt der ersten Anmeldung vor dem Zeitpunkt der zweiten Anmeldung liegt, für die bereits eine E-Mail-Benachrichtigung gesendet wurde, wird keine zusätzliche E-Mail gesendet.

Zur Vermeidung einer übermäßig großen Anzahl von E-Mails erhalten Sie innerhalb von fünf Sekunden nur eine E-Mail. Dies bedeutet, dass wenn innerhalb der fünf Sekunden für mehrere Benutzer die angegebene Risikostufe erreicht wird, diese Änderung der Risikostufe für alle Benutzer in einer einzelnen E-Mail zusammengefasst wird.

Wenn Ihre Organisation wie im Artikel [Benutzeroberflächen in Azure AD Identity Protection](concept-identity-protection-user-experience.md) beschrieben die Eigenwartung aktiviert hat, können Benutzer ihr Risiko selbst beheben, bevor Sie die Möglichkeit zur Untersuchung haben. Sie können Risikobenutzer und Risikoanmeldungen, die korrigiert wurden, anzeigen, indem Sie im Bericht **Riskante Benutzer** oder **Riskante Anmeldungen** zum Filter **Risikozustand** „Bereinigt“ hinzufügen.

![E-Mail für erkannte gefährdete Benutzer](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>Konfigurieren von Warnungen zu erkannten gefährdeten Benutzern

Als Administrator können Sie Folgendes festlegen:

- **Die Benutzerrisikostufe, bei der die Erstellung dieser E-Mail ausgelöst wird**: Standardmäßig ist die Risikostufe auf „Hoch“ festgelegt.
- **Die Empfänger dieser E-Mail** – Benutzer in den Rollen „Globaler Administrator“, „Sicherheitsadministrator“ oder „Sicherheitsleseberechtigter“ werden dieser Liste automatisch hinzugefügt. Wir versuchen, E-Mails an die ersten 20 Mitglieder jeder Rolle zu senden. Falls ein Benutzer in PIM registriert ist, damit er bei Bedarf für eine dieser Rollen erhöhte Rechte erhält, **wird er nur dann E-Mails erhalten, wenn er zu dem Zeitpunkt, zu dem die E-Mail gesendet wird, über diese Rechte verfügt**.
   - Optional können Sie eine **benutzerdefinierte E-Mail-Adresse hier** einfügen. Definierte Benutzer müssen die entsprechenden Berechtigungen zum Anzeigen der verknüpften Berichte im Azure-Portal haben.

Konfigurieren Sie die E-Mail-Adresse der gefährdeten Benutzers im **Azure-Portal** unter **Azure Active Directory** > **Sicherheit** > **Identitätsschutz** > **Warnungen zu erkannten gefährdeten Benutzern**.

## <a name="weekly-digest-email"></a>Wöchentliche E-Mail mit Übersicht

Die wöchentliche Übersichts-E-Mail enthält eine Zusammenfassung der neuen Risikoerkennungen.  
Sie hat folgenden Inhalt:

- Neue Benutzer mit Risiko erkannt
- New risky sign-ins detected (in real time) (Neue Risikoanmeldungen erkannt (in Echtzeit))
- Links zu verwandten Berichten in Identity Protection

![Wöchentliche E-Mail mit Übersicht](./media/howto-identity-protection-configure-notifications/weekly-digest-email.png)

Benutzer in den Rollen „Globaler Administrator“, „Sicherheitsadministrator“ oder „Sicherheitsleseberechtigter“ werden dieser Liste automatisch hinzugefügt. Wir versuchen, E-Mails an die ersten 20 Mitglieder jeder Rolle zu senden. Falls ein Benutzer in PIM registriert ist, damit er bei Bedarf für eine dieser Rollen erhöhte Rechte erhält, **wird er nur dann E-Mails erhalten, wenn er zu dem Zeitpunkt, zu dem die E-Mail gesendet wird, über diese Rechte verfügt**.

### <a name="configure-weekly-digest-email"></a>Konfigurieren von wöchentlichen Zusammenfassung per E-Mail

Als Administrator können Sie das Senden einer wöchentlichen Zusammenfassung per E-Mail aktivieren oder deaktivieren und die Benutzer auswählen, denen die E-Mail gesendet werden soll.

Konfigurieren Sie die wöchentliche Zusammenfassung per E-Mail im **Azure-Portal** unter **Azure Active Directory** > **Sicherheit** > **Informationsschutz** > **Wöchentliche Übersicht**.

## <a name="see-also"></a>Weitere Informationen

- [Azure Active Directory Identity Protection](./overview-identity-protection.md)
