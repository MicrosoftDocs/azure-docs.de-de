---
title: Überprüfen der Mitgliedschaftsregeln für dynamische Gruppen (Vorschau) – Azure AD | Microsoft-Dokumentation
description: Vorgehensweise beim Testen von Mitgliedern anhand einer Mitgliedschaftregel für eine dynamische Gruppe in Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: yukarppa
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1048284e8e7a492bf0810a16e29409546ed414b
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547558"
---
# <a name="validate-a-dynamic-group-membership-rule-preview-in-azure-active-directory"></a>Überprüfen der Mitgliedschaftregel für eine dynamische Gruppe (Vorschau) in Azure Active Directory

Azure Active Directory (Azure AD) bietet nun die Möglichkeit, Regeln für dynamische Gruppen (in der öffentlichen Vorschau) zu überprüfen. Auf der Registerkarte **Regeln überprüfen** können Sie Ihre dynamische Regel anhand von Beispielgruppenmitgliedern überprüfen, um zu bestätigen, dass die Regel erwartungsgemäß funktioniert. Beim Erstellen oder Aktualisieren von Regeln für dynamische Gruppen möchten Administratoren wissen, ob ein Benutzer oder ein Gerät Mitglied der Gruppe sein wird. So können sie auswerten, ob ein Benutzer oder Gerät die Regelkriterien erfüllt und für den Fall, dass die Mitgliedschaft nicht erwartet wird, eine entsprechende Problembehandlung durchführen.

## <a name="step-by-step-walk-through"></a>Exemplarische Vorgehensweise in einzelnen Schritten

Wechseln Sie zu Beginn zu **Azure Active Directory** > **Gruppen**. Wählen Sie eine vorhandene dynamische Gruppe aus, oder erstellen Sie eine neue dynamische Gruppe, und klicken Sie auf „Dynamische Mitgliedschaftsregeln“. Dann wird die Registerkarte **Regeln überprüfen** angezeigt.

![Suchen der Registerkarte „Regeln überprüfen“ und Beginn mit einer vorhandenen Regel](./media/groups-dynamic-rule-validation/validate-tab.png)

Auf der Registerkarte **Regeln überprüfen** können Sie Benutzer auswählen, um deren Mitgliedschaft zu überprüfen. 20 Benutzer oder Geräte können gleichzeitig ausgewählt werden.

![Hinzufügen von Benutzern, um die vorhandene Regel zu überprüfen](./media/groups-dynamic-rule-validation/validate-tab-add-users.png)

Nachdem Sie die Benutzer oder Geräte und **Auswählen** ausgewählt haben, wird die Überprüfung automatisch gestartet und die Überprüfungsergebnisse werden angezeigt.

![Anzeigen der Ergebnisse der Regelüberprüfung](./media/groups-dynamic-rule-validation/validate-tab-results.png)

Die Ergebnisse geben an, ob ein Benutzer Mitglied der Gruppe ist oder nicht. Wenn die Regel ungültig ist oder ein Netzwerkproblem vorliegt, wird als Ergebnis **Unbekannt** angezeigt. Bei **Unbekannt** werden in der ausführlichen Fehlermeldung das Problem und die erforderlichen Aktionen beschrieben.

![Anzeigen der Details der Ergebnisse der Regelüberprüfung](./media/groups-dynamic-rule-validation/validate-tab-view-details.png)

Sie können die Regel ändern, und die Überprüfung von Mitgliedschaften wird ausgelöst. Um zu sehen, warum der Benutzer kein Mitglied der Gruppe ist, klicken Sie auf „Details anzeigen“, und in den Überprüfungsdetails wird das Ergebnis der einzelnen Ausdrücke angezeigt, die die Regel bilden. Klicken Sie zum Beenden auf **OK**.

## <a name="next-steps"></a>Nächste Schritte

- [Regeln für eine dynamische Mitgliedschaft für Gruppen in Azure Active Directory](groups-dynamic-membership.md)
