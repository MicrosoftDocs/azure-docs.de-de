---
title: E-Mail-Benachrichtigungen in PIM – Azure Active Directory | Microsoft-Dokumentation
description: Dieser Artikel enthält eine Beschreibung der E-Mail-Benachrichtigungen in Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/05/2019
ms.author: rolyon
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9aa9da83a7a6e97f5b721dad550831fe2645ffd3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60289194"
---
# <a name="email-notifications-in-pim"></a>E-Mail-Benachrichtigungen in PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) benachrichtigt Sie über wichtige Ereignisse, z. B. wenn eine Rolle zugewiesen oder aktiviert wird. Mit PIM werden E-Mail-Benachrichtigungen an Sie und andere Teilnehmer gesendet. Diese E-Mail-Benachrichtigungen können auch Links zu relevanten Aufgaben, z.B. Aktivieren oder Erneuern einer Rolle, enthalten. In diesem Artikel wird beschrieben, wie diese E-Mails aussehen, wann sie gesendet werden und wer sie erhält.

## <a name="sender-email-address-and-subject-line"></a>Absender-E-Mail-Adresse und Betreffzeile

Die von PIM für Azure AD- und Azure-Ressourcenrollen gesendeten E-Mails haben folgende Absender-E-Mail-Adresse:

- E-Mail-Adresse: **azure-noreply\@microsoft.com**
- Anzeigename: Microsoft Azure

Diese E-Mails enthalten in der Betreffzeile das Präfix **PIM**. Hier sehen Sie ein Beispiel:

- PIM: Alain Charon wurde permanent die Rolle des Benutzers mit Leseberechtigung für Sicherungsfunktionen zugewiesen.

## <a name="pim-emails-for-azure-ad-roles"></a>PIM-E-Mails für Azure AD-Rollen

PIM sendet E-Mails bei folgenden Ereignissen für Azure AD-Rollen:

- Wenn die Genehmigung für die Aktivierung einer privilegierten Rolle aussteht
- Wenn die Aktivierungsanforderung einer privilegierten Rolle abgeschlossen wird
- Wenn Azure AD PIM aktiviert wird

Wer diese E-Mails für Azure AD-Rollen erhält, hängt von der Rolle, dem Ereignis und der Einstellung für Benachrichtigungen ab:

| Benutzer | Für die Rollenaktivierung steht Genehmigung aus | Anforderung zur Rollenaktivierung ist abgeschlossen | PIM ist aktiviert |
| --- | --- | --- | --- |
| Administrator für privilegierte Rollen</br>(Aktiviert/Berechtigt) | Ja</br>(nur, wenn keine expliziten genehmigenden Personen angegeben sind) | Ja* | Ja |
| Sicherheitsadministrator</br>(Aktiviert/Berechtigt) | Nein | Ja* | Ja |
| Globaler Administrator</br>(Aktiviert/Berechtigt) | Nein | Ja* | Ja |

\* Wenn die [Einstellung **Benachrichtigungen**](pim-how-to-change-default-settings.md#notifications) auf **Aktivieren** festgelegt ist.

Nachstehend finden Sie eine Beispiel-E-Mail, die gesendet wird, wenn ein Benutzer eine Azure AD-Rolle für die fiktive Organisation Contoso aktiviert.

![Neue PIM-E-Mail für Azure AD-Rollen](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-pim-digest-email-for-azure-ad-roles"></a>Wöchentliche PIM-Zusammenfassung für Azure AD-Rollen per E-Mail

Eine wöchentliche PIM-Zusammenfassung für Azure AD-Rollen wird per E-Mail an Administratoren für privilegierte Rollen, Sicherheitsadministratoren und globale Administratoren gesendet, die PIM aktiviert haben. Diese wöchentliche E-Mail enthält eine Momentaufnahme der PIM-Aktivitäten der Woche sowie der Zuweisungen privilegierter Rollen. Sie ist nur für Mandanten in der öffentlichen Cloud verfügbar. Hier sehen Sie eine Beispiel-E-Mail:

![Wöchentliche PIM-Zusammenfassung für Azure AD-Rollen per E-Mail](./media/pim-email-notifications/email-directory-weekly.png)

Die E-Mail enthält vier Kacheln:

| Kachel | BESCHREIBUNG |
| --- | --- |
| **Users activated** (Benutzer aktiviert) | Zeigt an, wie oft Benutzer ihre berechtigte Rolle im Mandanten aktiviert haben. |
| **Users made permanent** (Benutzer als permanent festgelegt) | Zeigt an, wie oft Benutzer mit einer berechtigten Zuweisung als permanent festgelegt werden. |
| **Role assignments in PIM** (Rollenzuweisungen in PIM) | Zeigt an, wie oft Benutzern eine berechtigte Rolle in PIM zugewiesen wird. |
| **Role assignments outside of PIM** (Rollenzuweisungen außerhalb von PIM) | Zeigt an, wie oft Benutzern eine permanente Rolle außerhalb von PIM (innerhalb von Azure AD) zugewiesen wird. |

Im Abschnitt **Overview of your top roles** (Übersicht über Ihre wichtigsten Rollen) sind die fünf wichtigsten Rollen in Ihrem Mandanten basierend auf der Gesamtzahl der permanenten und berechtigten Administratoren für jede Rolle aufgeführt. Über den Link **Aktion ausführen** wird der [PIM-Assistent](pim-security-wizard.md) geöffnet, in dem Sie permanente Administratoren in Batches in berechtigte Administratoren umwandeln können.

## <a name="pim-emails-for-azure-resource-roles"></a>PIM-E-Mails für Azure-Ressourcenrollen

PIM sendet bei folgenden Ereignissen für Azure-Ressourcenrollen E-Mails an Besitzer und Benutzerzugriffsadministratoren:

- Wenn die Genehmigung für eine Rollenzuweisung aussteht
- Wenn eine Rolle zugewiesen wird
- Wenn eine Rolle bald abläuft
- Wenn eine Rolle erweitert werden kann
- Wenn eine Rolle von einem Endbenutzer erneuert wird
- Wenn die Anforderung zur Rollenaktivierung abgeschlossen wird

PIM sendet bei folgenden Ereignissen für Azure-Ressourcenrollen E-Mails an Endbenutzer:

- Wenn dem Benutzer eine Rolle zugewiesen wird
- Wenn die Rolle eines Benutzers abgelaufen ist
- Wenn die Rolle eines Benutzers erweitert wird
- Wenn die Anforderung zur Rollenaktivierung eines Benutzers abgeschlossen wird

Nachstehend finden Sie eine Beispiel-E-Mail, die gesendet wird, wenn einem Benutzer eine Azure-Ressourcenrolle für die fiktive Organisation Contoso zugewiesen wird.

![Neue PIM-E-Mails für Azure-Ressourcenrollen](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren von Einstellungen für Azure AD-Rollen in PIM](pim-how-to-change-default-settings.md)
- [Genehmigen oder Ablehnen von Anforderungen für Azure AD-Rollen in PIM](azure-ad-pim-approval-workflow.md)
