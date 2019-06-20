---
title: Konfigurieren von Einstellungen für Azure AD-Rollen in PIM – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Einstellungen für Azure AD-Rollen in Azure AD Privileged Identity Management (PIM) konfigurieren.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 05/31/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2bc7d3ffcb56251825bf5f6d760de647938f1ead
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66417879"
---
# <a name="configure-azure-ad-role-settings-in-pim"></a>Konfigurieren von Einstellungen für Azure AD-Rollen in PIM

Ein Administrator für privilegierte Rollen kann Azure Active Directory (Azure AD) Privileged Identity Management (PIM) in der Organisation anpassen und dabei auch die Art und Weise ändern, in der ein Benutzer eine berechtigte Rollenzuweisung aktiviert.

## <a name="open-role-settings"></a>Öffnen von Rolleneinstellungen

Führen Sie die folgenden Schritte aus, um die Einstellungen für eine Azure AD-Rolle zu öffnen.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Klicken Sie auf **Azure AD-Rollen**.

1. Klicken Sie auf **Einstellungen**.

    ![Azure AD-Rollen – Einstellungen](./media/pim-how-to-change-default-settings/pim-directory-roles-settings.png)

1. Klicken Sie auf **Rollen**.

1. Klicken Sie auf die Rolle, deren Einstellungen Sie konfigurieren möchten.

    ![Azure AD-Rollen – Einstellungen für Rollen](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-role.png)

    Auf der Einstellungsseite für jede Rolle können Sie verschiedene Einstellungen konfigurieren. Diese Einstellungen wirken sich nur auf Benutzer aus, die über **berechtigte** Zuweisungen verfügen, nicht über **permanente** Zuweisungen.

## <a name="activations"></a>Aktivierungen

Legen Sie mit dem Schieberegler **Aktivierungen** die maximale Zeit in Stunden fest, die eine Rolle aktiv bleibt, bevor sie abläuft. Dieser Wert kann zwischen 1 und 72 Stunden liegen.

## <a name="notifications"></a>Benachrichtigungen

Mit dem Schalter **Benachrichtigungen** können Sie angeben, ob Administratoren E-Mail-Benachrichtigungen empfangen, wenn Rollen aktiviert werden. Dies kann für die Erkennung von nicht autorisierten oder unzulässigem Aktivierungen nützlich sein.

Wenn diese Option auf **Aktivieren** festgelegt ist, werden Benachrichtigungen an folgende Administratoren gesendet:

- Administrator für privilegierte Rollen
- Sicherheitsadministrator
- Globaler Administrator

Weitere Informationen finden Sie unter[E-Mail-Benachrichtigungen in PIM](pim-email-notifications.md).

## <a name="incidentrequest-ticket"></a>Incident/Ticket anfordern

Mit dem Schalter **Incident/Ticket anfordern** können Sie angeben, ob berechtigte Administratoren eine Ticketnummer angeben müssen, wenn sie ihre Rolle aktivieren. Dies kann nützlich sein, wenn Sie Rollenzugriffe überprüfen.

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Mit dem Schalter **Multi-Factor Authentication** können Sie angeben, ob Benutzer ihre Identität mit MFA verifizieren müssen, bevor sie ihre Rollen aktivieren können. Sie müssen dies nur einmal pro Sitzung verifizieren, nicht jedes Mal, wenn sie eine Rolle aktivieren. Beachten Sie zwei Tipps, wenn Sie die MFA aktivieren:

* Benutzer, die Microsoft-Konten als E-Mail-Adressen verwenden (in der Regel @outlook.com, aber nicht immer), können sich nicht für Azure MFA registrieren. Wenn Sie Benutzern mit Microsoft-Konten Rollen zuweisen möchten, sollten Sie sie zu permanenten Administratoren machen oder MFA für diese Rolle deaktivieren.
* Sie können MFA für sehr privilegierte Rollen für Azure AD und Office 365 nicht deaktivieren. Dies ist ein Sicherheitsfeature, da diese Rollen sorgfältig geschützt werden sollten:  
  
  * Azure Information Protection-Administrator
  * Abrechnungsadministrator
  * Cloudanwendungsadministrator
  * Complianceadministrator
  * Administrator für den bedingten Zugriff
  * CRM-Dienstadministrator
  * Genehmigende Person für den LockBox-Kundenzugriff
  * Verzeichnis schreiben
  * Exchange-Administrator
  * Globaler Administrator
  * Intune-Dienstadministrator
  * Power BI-Dienstadministrator
  * Administrator für privilegierte Rollen
  * Sicherheitsadministrator
  * SharePoint-Dienstadministrator
  * Skype for Business-Administrator
  * Benutzeradministrator

Weitere Informationen finden Sie unter [Multi-Factor Authentication (MFA) und PIM](pim-how-to-require-mfa.md).

## <a name="require-approval"></a>Genehmigung anfordern

Wenn Sie für die Aktivierung einer Rolle eine Genehmigung anfordern möchten, gehen Sie wie folgt vor.

1. Legen Sie den Schalter **Genehmigung anfordern** auf **Aktiviert** fest. Der Bereich wird erweitert und zeigt Optionen zum Auswählen von genehmigenden Personen an.

    ![Azure AD-Rollen – Einstellungen: Genehmigung anfordern](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval.png)

    Wenn Sie **KEINE** genehmigenden Personen angeben, werden Administratoren mit privilegierten Rollen als standardmäßige genehmigende Personen festgelegt. Administratoren mit privilegierten Rollen sind erforderlich, um **ALLE** Aktivierungsanforderungen für diese Rolle zu genehmigen.

1. Um genehmigende Personen anzugeben, klicken Sie auf **Genehmigende Personen auswählen**.

    ![Azure AD-Rollen – Einstellungen: Genehmigung anfordern](./media/pim-how-to-change-default-settings/pim-directory-roles-settings-require-approval-select-approvers.png)

1. Wählen Sie eine oder mehrere genehmigende Personen aus, und klicken Sie anschließend auf **Auswählen**. Sie können Benutzer oder Gruppen auswählen. Es werden mindestens zwei genehmigende Personen empfohlen. Eine Genehmigung für die eigene Rolle ist nicht zulässig.

    Ihre Auswahl wird in der Liste der ausgewählten genehmigenden Personen angezeigt.

1. Wenn Sie alle gewünschten Rolleneinstellungen angegeben haben, klicken Sie auf **Speichern**, um Ihre Änderungen zu speichern.


<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

## <a name="next-steps"></a>Nächste Schritte

- [Zuweisen von Azure AD-Rollen in PIM](pim-how-to-add-role-to-user.md)
- [Konfigurieren von Sicherheitswarnungen für Azure AD-Rollen in PIM](pim-how-to-configure-security-alerts.md)
