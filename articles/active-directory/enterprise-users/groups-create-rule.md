---
title: Erstellen oder Bearbeiten einer dynamischen Gruppe und Abrufen des Status – Azure AD | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie im Azure-Portal eine Regel für die Gruppenmitgliedschaft erstellen oder aktualisieren und den Verarbeitungsstatus überprüfen.
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
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a25caa3b59d242b8dcc8a52e1a5d684815cf7bf
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547779"
---
# <a name="create-or-update-a-dynamic-group-in-azure-active-directory"></a>Erstellen oder Aktualisieren einer dynamischen Gruppe in Azure Active Directory

In Azure Active Directory (Azure AD) können Sie mithilfe von Regeln die Gruppenmitgliedschaft auf der Grundlage von Benutzer- oder Geräteeigenschaften ermitteln. In diesem Artikel erfahren Sie, wie Sie im Azure-Portal eine Regel für eine dynamische Gruppe einrichten.
Die dynamische Mitgliedschaft wird für Sicherheitsgruppen und Microsoft 365-Gruppen unterstützt. Wenn eine Regel für die Gruppenmitgliedschaft angewendet wird, werden Benutzer- und Geräteattribute auf Übereinstimmungen mit der Mitgliedschaftsregel geprüft. Wenn sich ein Attribut für einen Benutzer oder ein Gerät ändert, werden alle dynamischen Gruppenregeln in der Organisation verarbeitet, um Mitgliedschaftsänderungen zu berücksichtigen. Benutzer und Geräte werden hinzugefügt oder entfernt, wenn sie die Bedingungen für eine Gruppe erfüllen. Sicherheitsgruppen können für Geräte oder Benutzer verwendet werden, Microsoft 365-Gruppen dagegen können nur Benutzergruppen sein. Für die Verwendung dynamischer Gruppen ist eine Azure AD Premium P1-Lizenz erforderlich. Weitere Details finden Sie unter [Regeln für eine dynamische Mitgliedschaft für Gruppen in Azure Active Directory](./groups-dynamic-membership.md). 

## <a name="rule-builder-in-the-azure-portal"></a>Regel-Generator im Azure-Portal

Azure AD stellt einen Regel-Generator bereit, mit dem Sie wichtige Regeln schneller erstellen und aktualisieren können. Der Regel-Generator unterstützt die Erstellung von bis zu fünf Ausdrücken. Die Erstellung einer Regel mit einigen einfachen Ausdrücken wird durch den Regel-Generator vereinfacht, aber er kann nicht verwendet werden, um jede Regel zu reproduzieren. Falls der Regel-Generator die zu erstellende Regel nicht unterstützt, können Sie das Textfeld verwenden.

Im Anschluss folgen einige Beispiele für erweiterte Regeln oder für Syntax, für die die Erstellung über das Textfeld empfohlen wird:

- Regel mit mehr als fünf Ausdrücken
- Mitarbeiterregel
- Festlegen der [Rangfolge der Operatoren](groups-dynamic-membership.md#operator-precedence)
- [Regeln mit komplexen Ausdrücken](groups-dynamic-membership.md#rules-with-complex-expressions), z. B. `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> Der Regel-Generator kann ggf. einige Regeln, die über das Textfeld erstellt wurden, nicht anzeigen. Unter Umständen wird eine Meldung angezeigt, falls die Regel vom Regel-Generator nicht angezeigt werden kann. Der Regel-Generator nimmt keinerlei Änderungen an der unterstützten Syntax, Überprüfung oder Verarbeitung von Regeln für dynamische Gruppen vor.

![Screenshot der Seite „Dynamische Mitgliedschaftsregeln“ mit der ausgewählten Aktion „Ausdruck hinzufügen“ auf der Registerkarte „Regeln konfigurieren“](./media/groups-create-rule/update-dynamic-group-rule.png)

Beispiele für Syntax, unterstützte Eigenschaften, Operatoren und Werte für eine Mitgliedschaftsregel finden Sie unter [Regeln für eine dynamische Mitgliedschaft für Gruppen in Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>So erstellen Sie eine Regel für die Gruppenmitgliedschaft

1. Melden Sie sich beim [Azure AD Admin Center](https://aad.portal.azure.com) mit einem Konto an, das der Rolle „Globaler Administrator“, „Intune-Administrator“ oder „Benutzeradministrator“ in der Azure AD-Organisation angehört.
1. Suchen Sie nach **Gruppen**, und wählen Sie diese Option aus.
1. Wählen Sie **Alle Gruppen** und **Neue Gruppe** aus.

   ![Auswählen des Befehls zum Hinzufügen einer neuen Gruppe](./media/groups-create-rule/create-new-group-azure-active-directory.png)

1. Geben Sie auf der Seite **Gruppe** einen Namen und eine Beschreibung für die neue Gruppe ein. Wählen Sie einen **Mitgliedschaftstyp** für Benutzer oder Geräte und anschließend die Option **Dynamische Abfrage hinzufügen** aus. Der Regel-Generator unterstützt bis zu fünf Ausdrücke. Falls Sie mehr als fünf Ausdrücke hinzufügen möchten, müssen Sie das Textfeld verwenden.

   ![Screenshot der Seite „Alle Gruppen“ mit der ausgewählten Aktion „Neue Gruppe“](./media/groups-create-rule/add-dynamic-group-rule.png)

1. Zeigen Sie die benutzerdefinierten Erweiterungseigenschaften für Ihre Mitgliedschaftsabfrage wie folgt an:
   1. Wählen Sie **Benutzerdefinierte Erweiterungseigenschaften abrufen** aus.
   1. Geben Sie die Anwendungs-ID ein, und wählen Sie anschließend **Eigenschaften aktualisieren** aus.
1. Klicken Sie nach dem Erstellen der Regel auf **Speichern**.
1. Wählen Sie auf der Seite **Neue Gruppe** die Option **Erstellen** aus, um die Gruppe zu erstellen.

Sollte die eingegebene Regel ungültig sein, wird über eine Azure-Benachrichtigung im Portal angezeigt, warum die Regel nicht verarbeitet werden konnte. Lesen Sie sich die Erklärung aufmerksam durch, um die Regel korrigieren zu können.

## <a name="to-update-an-existing-rule"></a>So aktualisieren Sie eine vorhandene Rolle

1. Melden Sie sich beim [Azure AD Admin Center](https://aad.portal.azure.com) mit einem Konto an, das der Rolle „Globaler Administrator“, „Gruppenadministrator“, „Intune-Administrator“ oder „Benutzeradministrator“ in der Azure AD-Organisation angehört.
1. Wählen Sie **Gruppen** > **Alle Gruppen** aus.
1. Wählen Sie eine Gruppe aus, um ihr Profil zu öffnen.
1. Wählen Sie auf der Profilseite für die Gruppe **Dynamische Mitgliedschaftsregeln** aus. Der Regel-Generator unterstützt bis zu fünf Ausdrücke. Falls Sie mehr als fünf Ausdrücke hinzufügen möchten, müssen Sie das Textfeld verwenden.

   ![Hinzufügen einer Mitgliedschaftsregel für eine dynamische Gruppe](./media/groups-create-rule/update-dynamic-group-rule.png)

1. So zeigen Sie die benutzerdefinierten Erweiterungseigenschaften für Ihre Mitgliedschaftsregel an
   1. Wählen Sie **Benutzerdefinierte Erweiterungseigenschaften abrufen** aus.
   1. Geben Sie die Anwendungs-ID ein, und wählen Sie anschließend **Eigenschaften aktualisieren** aus.
1. Klicken Sie nach dem Aktualisieren der Regel auf **Speichern**.

## <a name="turn-on-or-off-welcome-email"></a>Aktivieren oder Deaktivieren der Begrüßungs-E-Mail

Wenn eine neue Microsoft 365-Gruppe erstellt wird, erhalten die Benutzer, die der Gruppe hinzugefügt werden, eine Begrüßungs-E-Mail. Wenn sich später Attribute eines Benutzers oder Geräts ändern, werden alle dynamischen Gruppenregeln in der Organisation verarbeitet, um Mitgliedschaftsänderungen zu berücksichtigen. Hinzugefügte Benutzer erhalten dann ebenfalls eine Begrüßungsnachricht. Sie können dieses Verhalten in [Exchange PowerShell](/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps&preserve-view=true) deaktivieren.

## <a name="check-processing-status-for-a-rule"></a>Überprüfen des Verarbeitungsstatus für eine Regel

Auf der Seite **Übersicht** für die Gruppe sehen Sie den Verarbeitungsstatus der Mitgliedschaft und das zuletzt geänderte Datum.
  
  ![Anzeigen des Status der dynamischen Gruppe](./media/groups-create-rule/group-status.png)

Für **Verarbeitungsstatus der Mitgliedschaft** können die folgenden Statusmeldungen angezeigt werden:

- **Auswertung wird durchgeführt:**  Die Gruppenänderung wurde empfangen, und die Aktualisierungen werden ausgewertet.
- **Processing**: Die Updates werden verarbeitet.
- **Aktualisierung abgeschlossen:** Die Verarbeitung wurde abgeschlossen, alle anwendbaren Aktualisierungen wurden vorgenommen.
- **Verarbeitungsfehler:**  Die Verarbeitung konnte aufgrund eines Fehlers beim Auswerten der Mitgliedschaftsregel nicht abgeschlossen werden.
- **Aktualisierung angehalten:** Aktualisierungen der Regeln für die dynamische Mitgliedschaft wurden vom Administrator angehalten. „MembershipRuleProcessingState“ ist auf „Paused“ festgelegt.

Für **Letzte Aktualisierung der Mitgliedschaft** können die folgenden Statusmeldungen angezeigt werden:

- &lt;**Datum und Uhrzeit:** &gt; Der Zeitpunkt der letzten Aktualisierung der Mitgliedschaft.
- **In Bearbeitung**: Aktualisierungen sind derzeit in Bearbeitung.
- **Unbekannt:** Der Zeitpunkt der letzten Aktualisierung kann nicht abgerufen werden. Die Gruppe ist möglicherweise neu.

Wenn bei der Verarbeitung der Mitgliedschaftsregel für eine bestimmte Gruppe ein Fehler auftritt, wird oben auf der Seite **Übersicht** der Gruppe eine Warnung angezeigt. Wenn für alle Gruppen innerhalb der Organisation für mehr als 24 Stunden keine ausstehenden Aktualisierungen der dynamischen Mitgliedschaft verarbeitet werden können, wird oben in **Alle Gruppen** eine Warnung angezeigt.

![Warnungen aufgrund von Verarbeitungsfehlern](./media/groups-create-rule/processing-error.png)

Diese Artikel enthalten zusätzliche Informationen zu Gruppen in Azure Active Directory.

- [Anzeigen vorhandener Gruppen](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Erstellen einer neuen Gruppe und Hinzufügen von Mitgliedern](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Verwalten der Einstellungen einer Gruppe](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Verwalten der Mitgliedschaften einer Gruppe](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Verwalten dynamischer Regeln für Benutzer in einer Gruppe](groups-dynamic-membership.md)