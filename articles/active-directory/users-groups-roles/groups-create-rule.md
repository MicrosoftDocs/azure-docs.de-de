---
title: Erstellen einer dynamischen Gruppe und Überprüfen des Status – Azure Active Directory | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie im Azure-Portal eine Regel für die Gruppenmitgliedschaft erstellen und den Status überprüfen.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f828ff83e6b9c60eb08edef7f47e88185fb5aef8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60472141"
---
# <a name="create-a-dynamic-group-and-check-status"></a>Erstellen einer dynamischen Gruppe und Überprüfen des Status

In Azure Active Directory (Azure AD) können Sie mithilfe von Regeln die Gruppenmitgliedschaft auf der Grundlage von Benutzer- oder Geräteeigenschaften ermitteln. In diesem Artikel erfahren Sie, wie Sie im Azure-Portal eine Regel für eine dynamische Gruppe einrichten.
Die dynamische Mitgliedschaft wird für Sicherheitsgruppen und Office 365-Gruppen unterstützt. Wenn eine Regel für die Gruppenmitgliedschaft angewendet wird, werden Benutzer- und Geräteattribute auf Übereinstimmungen mit der Mitgliedschaftsregel geprüft. Wenn sich ein Attribut für einen Benutzer oder ein Gerät ändert, werden alle dynamischen Gruppenregeln in der Organisation verarbeitet, um Mitgliedschaftsänderungen zu berücksichtigen. Benutzer und Geräte werden hinzugefügt oder entfernt, wenn sie die Bedingungen für eine Gruppe erfüllen.

Beispiele für Syntax, unterstützte Eigenschaften, Operatoren und Werte für eine Mitgliedschaftsregel finden Sie unter [Regeln für eine dynamische Mitgliedschaft für Gruppen in Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>So erstellen Sie eine Regel für die Gruppenmitgliedschaft

1. Melden Sie sich beim [Azure AD Admin Center](https://aad.portal.azure.com) mit einem Konto an, das der Rolle des globalen Administrators, Intune-Administrators oder Benutzeradministrators in dem Mandanten angehört.
2. Wählen Sie **Gruppen** aus.
3. Wählen Sie **Alle Gruppen** und **Neue Gruppe** aus.

   ![Auswählen des Befehls zum Hinzufügen einer neuen Gruppe](./media/groups-create-rule/new-group-creation.png)

4. Geben Sie auf der Seite **Gruppe** einen Namen und eine Beschreibung für die neue Gruppe ein. Wählen Sie einen **Mitgliedschaftstyp** für Benutzer oder Geräte und anschließend die Option **Dynamische Abfrage hinzufügen** aus. Sie können den Regel-Generator verwenden, um eine einfache Regel zu erstellen, oder [selbst eine Mitgliedschaftsregel schreiben](groups-dynamic-membership.md).

   ![Hinzufügen einer Mitgliedschaftsregel für eine dynamische Gruppe](./media/groups-create-rule/add-dynamic-group-rule.png)

5. So zeigen Sie die benutzerdefinierten Erweiterungseigenschaften für Ihre Mitgliedschaftsabfrage an
   1. Wählen Sie **Benutzerdefinierte Erweiterungseigenschaften abrufen** aus.
   2. Geben Sie die Anwendungs-ID ein, und wählen Sie anschließend **Eigenschaften aktualisieren** aus. 
6. Wählen Sie nach der Erstellung der Regel unten auf dem Blatt **Abfrage hinzufügen**.
7. Wählen Sie **Erstellen** on the **Erstellen** aus, um die Gruppe zu erstellen.

Sollte die eingegebene Regel ungültig sein, wird rechts oben im Portal angezeigt, warum die Regel nicht verarbeitet werden konnte. Lesen Sie sich die Erklärung aufmerksam durch, um die Regel korrigieren zu können.

## <a name="turn-on-or-off-welcome-email"></a>Aktivieren oder Deaktivieren der Begrüßungs-E-Mail

Wenn eine neue Office 365-Gruppe erstellt wird, erhalten die Benutzer, die der Gruppe hinzugefügt werden, eine Begrüßungsnachricht. Wenn sich später Attribute eines Benutzers oder Geräts ändern, werden alle dynamischen Gruppenregeln in der Organisation verarbeitet, um Mitgliedschaftsänderungen zu berücksichtigen. Hinzugefügte Benutzer erhalten dann ebenfalls eine Begrüßungsnachricht. Sie können dieses Verhalten in [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps) deaktivieren. 

## <a name="check-processing-status-for-a-rule"></a>Überprüfen des Verarbeitungsstatus für eine Regel

Auf der Seite **Übersicht** für die Gruppe sehen Sie den Verarbeitungsstatus der Mitgliedschaft und das zuletzt geänderte Datum.
  
  ![Anzeigen des Status der dynamischen Gruppe](./media/groups-create-rule/group-status.png)

Für **Verarbeitungsstatus der Mitgliedschaft** können die folgenden Statusmeldungen angezeigt werden:

* **Auswertung wird durchgeführt:**  Die Gruppenänderung wurde empfangen, und die Aktualisierungen werden ausgewertet.
* **Verarbeitung:** Die Updates werden verarbeitet.
* **Aktualisierung abgeschlossen:** Die Verarbeitung wurde abgeschlossen, alle anwendbaren Aktualisierungen wurden vorgenommen.
* **Verarbeitungsfehler:**  Die Verarbeitung konnte aufgrund eines Fehlers beim Auswerten der Mitgliedschaftsregel nicht abgeschlossen werden.
* **Aktualisierung angehalten:** Aktualisierungen der Regeln für die dynamische Mitgliedschaft wurden vom Administrator angehalten. „MembershipRuleProcessingState“ ist auf „Paused“ festgelegt.

Für **Letzte Aktualisierung der Mitgliedschaft** können die folgenden Statusmeldungen angezeigt werden:

* &lt;**Datum und Uhrzeit:** &gt; Der Zeitpunkt der letzten Aktualisierung der Mitgliedschaft.
* **In Bearbeitung**: Aktualisierungen sind derzeit in Bearbeitung.
* **Unbekannt:** Der Zeitpunkt der letzten Aktualisierung kann nicht abgerufen werden. Die Gruppe ist möglicherweise neu.

Wenn bei der Verarbeitung der Mitgliedschaftsregel für eine bestimmte Gruppe ein Fehler auftritt, wird oben auf der Seite **Übersicht** der Gruppe eine Warnung angezeigt. Wenn für alle Gruppen innerhalb des Mandanten für mehr als 24 Stunden keine ausstehenden Aktualisierungen der dynamischen Mitgliedschaft verarbeitet werden können, wird oben in **Alle Gruppen** eine Warnung angezeigt.

![Warnungen aufgrund von Verarbeitungsfehlern](./media/groups-create-rule/processing-error.png)

Diese Artikel enthalten zusätzliche Informationen zu Gruppen in Azure Active Directory.

* [Anzeigen vorhandener Gruppen](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Erstellen einer neuen Gruppe und Hinzufügen von Mitgliedern](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Verwalten der Einstellungen einer Gruppe](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Verwalten der Mitgliedschaften einer Gruppe](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Verwalten dynamischer Regeln für Benutzer in einer Gruppe](groups-dynamic-membership.md)
