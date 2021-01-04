---
title: E-Mail-Benachrichtigungen in PIM – Azure Active Directory | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Beschreibung der E-Mail-Benachrichtigungen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/28/2020
ms.author: curtand
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe57a44a5a6fa9a631604d92419fd8f5ebcce50a
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394729"
---
# <a name="email-notifications-in-pim"></a>E-Mail-Benachrichtigungen in PIM

Privileged Identity Management (PIM) benachrichtigt Sie, wenn wichtige Ereignisse in Ihrer Azure Active Directory-Organisation (Azure AD-Organisation) erfolgen, beispielsweise wenn eine Rolle zugewiesen oder aktiviert wird. Sie werden entsprechend informiert, indem Privileged Identity Management E-Mail-Benachrichtigungen an Sie und andere Teilnehmer sendet. Diese E-Mail-Benachrichtigungen können auch Links zu relevanten Aufgaben, z.B. Aktivieren oder Erneuern einer Rolle, enthalten. In diesem Artikel wird beschrieben, wie diese E-Mails aussehen, wann sie gesendet werden und wer sie erhält.

## <a name="sender-email-address-and-subject-line"></a>Absender-E-Mail-Adresse und Betreffzeile

Die von Privileged Identity Management für Azure AD-Rollen und Azure-Ressourcenrollen gesendeten E-Mails weisen die folgende Absender-E-Mail-Adresse auf:

- E-Mail-Adresse: **azure-noreply\@microsoft.com**
- Anzeigename: Microsoft Azure

Diese E-Mails enthalten in der Betreffzeile das Präfix **PIM**. Hier sehen Sie ein Beispiel:

- PIM: Alain Charon wurde permanent die Rolle des Benutzers mit Leseberechtigung für Sicherungsfunktionen zugewiesen.

## <a name="notifications-for-azure-ad-roles"></a>Benachrichtigungen für Azure AD-Rollen

Privileged Identity Management sendet E-Mails bei folgenden Ereignissen für Azure AD-Rollen:

- Wenn die Genehmigung für die Aktivierung einer privilegierten Rolle aussteht
- Wenn die Aktivierungsanforderung einer privilegierten Rolle abgeschlossen wird
- Wenn Azure AD Privileged Identity Management aktiviert wird

Wer diese E-Mails für Azure AD-Rollen erhält, hängt von der Rolle, dem Ereignis und der Einstellung für Benachrichtigungen ab:

| Benutzer | Für die Rollenaktivierung steht Genehmigung aus | Anforderung zur Rollenaktivierung ist abgeschlossen | PIM ist aktiviert |
| --- | --- | --- | --- |
| Administrator für privilegierte Rollen</br>(Aktiviert/Berechtigt) | Ja</br>(nur, wenn keine expliziten genehmigenden Personen angegeben sind) | Ja* | Ja |
| Sicherheitsadministrator</br>(Aktiviert/Berechtigt) | Nein  | Ja* | Ja |
| Globaler Administrator</br>(Aktiviert/Berechtigt) | Nein  | Ja* | Ja |

\* Wenn die [Einstellung **Benachrichtigungen**](pim-how-to-change-default-settings.md#notifications) auf **Aktivieren** festgelegt ist.

Nachstehend finden Sie eine Beispiel-E-Mail, die gesendet wird, wenn ein Benutzer eine Azure AD-Rolle für die fiktive Organisation Contoso aktiviert.

![Neue Privileged Identity Management-E-Mail für Azure AD-Rollen](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-privileged-identity-management-digest-email-for-azure-ad-roles"></a>Wöchentliche Privileged Identity Management-Zusammenfassungs-E-Mail für Azure AD-Rollen

Eine wöchentliche Privileged Identity Management-Zusammenfassung für Azure AD-Rollen wird per E-Mail an Administratoren für privilegierte Rollen, Sicherheitsadministratoren und globale Administratoren gesendet, die Privileged Identity Management aktiviert haben. Diese wöchentliche E-Mail enthält eine Momentaufnahme der Privileged Identity Management-Aktivitäten der Woche sowie der Zuweisungen privilegierter Rollen. Sie ist nur für Azure AD-Organisationen in der öffentlichen Cloud verfügbar. Hier sehen Sie eine Beispiel-E-Mail:

![Wöchentliche Privileged Identity Management-Zusammenfassungs-E-Mail für Azure AD-Rollen](./media/pim-email-notifications/email-directory-weekly.png)

Die E-Mail enthält vier Kacheln:

| Kachel | BESCHREIBUNG |
| --- | --- |
| **Users activated** (Benutzer aktiviert) | Zeigt an, wie oft Benutzer ihre berechtigte Rolle in der Organisation aktiviert haben. |
| **Users made permanent** (Benutzer als permanent festgelegt) | Zeigt an, wie oft Benutzer mit einer berechtigten Zuweisung als permanent festgelegt werden. |
| **Rollenzuweisungen in Privileged Identity Management** | Zeigt an, wie oft Benutzern eine berechtigte Rolle in Privileged Identity Management zugewiesen wird. |
| **Role assignments outside of PIM** (Rollenzuweisungen außerhalb von PIM) | Zeigt an, wie oft Benutzern eine permanente Rolle außerhalb von Privileged Identity Management (in Azure AD) zugewiesen wird. |

Im Abschnitt **Overview of your top roles** (Übersicht über Ihre wichtigsten Rollen) sind die fünf wichtigsten Rollen in Ihrer Organisation basierend auf der Gesamtzahl der permanenten und berechtigten Administratoren für jede Rolle aufgeführt. Über den Link **Aktion ausführen** wird der [PIM-Assistent](pim-security-wizard.md) geöffnet, in dem Sie permanente Administratoren in Batches in berechtigte Administratoren umwandeln können.

## <a name="email-timing-for-activation-approvals"></a>E-Mail-Timing für Aktivierungsgenehmigungen

Wenn Benutzer ihre Rolle aktivieren und die Rolleneinstellung genehmigt werden muss, erhalten genehmigende Personen für jede Genehmigung zwei E-Mails:

- Anforderung zum Genehmigen oder Ablehnen der Aktivierungsanforderung des Benutzers (vom Genehmigungsanforderungsmodul gesendet)
- Die Anforderung des Benutzers wird genehmigt (vom Genehmigungsanforderungsmodul gesendet)

Außerdem erhalten globale Administratoren und Administratoren für privilegierte Rollen eine E-Mail für jede Genehmigung:

- Die Rolle des Benutzers wird aktiviert (von Privileged Identity Management gesendet)

Bei den ersten beiden vom Genehmigungsanforderungsmodul gesendeten E-Mails können Verzögerungen auftreten. Bei 90 % der E-Mails dauert das Versenden derzeit drei bis zehn Minuten, aber bei 1 % der Kunden kann es viel länger dauern, bis zu fünfzehn Minuten.

Wird im Azure-Portal eine Genehmigungsanforderung vor dem Senden der ersten E-Mail genehmigt, wird die erste E-Mail nicht mehr ausgelöst, und andere genehmigende Personen werden nicht per E-Mail über die Genehmigungsanforderung benachrichtigt. Es mag den Anschein haben, als hätten sie keine E-Mail erhalten, aber dies ist das erwartete Verhalten.

## <a name="pim-emails-for-azure-resource-roles"></a>PIM-E-Mails für Azure-Ressourcenrollen

Privileged Identity Management sendet bei folgenden Ereignissen für Azure-Ressourcenrollen E-Mails an Besitzer und Benutzerzugriffsadministratoren:

- Wenn die Genehmigung für eine Rollenzuweisung aussteht
- Wenn eine Rolle zugewiesen wird
- Wenn eine Rolle bald abläuft
- Wenn eine Rolle erweitert werden kann
- Wenn eine Rolle von einem Endbenutzer erneuert wird
- Wenn die Anforderung zur Rollenaktivierung abgeschlossen wird

Privileged Identity Management sendet bei folgenden Ereignissen für Azure-Ressourcenrollen E-Mails an Endbenutzer:

- Wenn dem Benutzer eine Rolle zugewiesen wird
- Wenn die Rolle eines Benutzers abgelaufen ist
- Wenn die Rolle eines Benutzers erweitert wird
- Wenn die Anforderung zur Rollenaktivierung eines Benutzers abgeschlossen wird

Nachstehend finden Sie eine Beispiel-E-Mail, die gesendet wird, wenn einem Benutzer eine Azure-Ressourcenrolle für die fiktive Organisation Contoso zugewiesen wird.

![Neue Privileged Identity Management-E-Mail für Azure-Ressourcenrollen](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren von Einstellungen für Azure AD-Rollen in PIM](pim-how-to-change-default-settings.md)
- [Genehmigen oder Ablehnen von Anforderungen für Azure AD-Rollen in PIM](azure-ad-pim-approval-workflow.md)
