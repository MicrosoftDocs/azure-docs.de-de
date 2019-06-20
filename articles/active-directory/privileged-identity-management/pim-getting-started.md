---
title: Einstieg in die Verwendung von PIM – Azure Active Directory | Microsoft-Dokumentation
description: Hier erhalten Sie Informationen zum Aktivieren von und zu den ersten Schritten mit Azure AD Privileged Identity Management (PIM) im Azure-Portal.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 651dc4efa653e7a4eb77f153280579ffd87fe8f7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60290000"
---
# <a name="start-using-pim"></a>Einstieg in die Verwendung von PIM

Mit Azure Active Directory Privileged Identity Management (PIM) können Sie den Zugriff innerhalb Ihrer Organisation verwalten, steuern und überwachen. Dazu zählt der Zugriff auf Azure-Ressourcen, Azure AD und andere Microsoft-Onlinedienste wie Office 365 oder Microsoft Intune.

In diesem Artikel werden das Aktivieren von und die ersten Schritte mit PIM beschrieben.

## <a name="prerequisites"></a>Voraussetzungen

Um PIM verwenden zu können, müssen Sie über eine der folgenden Lizenzen verfügen:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Weitere Informationen finden Sie unter [Lizenzanforderungen für die Verwendung von PIM](subscription-requirements.md).

## <a name="first-person-to-use-pim"></a>Erster Benutzer in PIM

Wenn Sie die erste Person sind, die PIM in Ihrem Verzeichnis verwendet, werden Ihnen automatisch die Rollen [Sicherheitsadministrator](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) und [Administrator für privilegierte Rollen](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) im Verzeichnis zugewiesen. Nur Administratoren für privilegierte Rollen können Azure AD-Rollenzuweisungen von Benutzern verwalten. Darüber hinaus können Sie den [Sicherheits-Assistenten](pim-security-wizard.md) ausführen, der Sie durch die Erstermittlung und -zuweisung führt.

## <a name="enable-pim"></a>Aktivieren von PIM

Um PIM in Ihrem Verzeichnis verwenden zu können, müssen Sie PIM zuerst aktivieren.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als globaler Administrator Ihres Verzeichnisses an.

    Sie müssen ein globaler Administrator mit einem Organisationskonto (wie @yourdomain.com) und keinem Microsoft-Konto (wie @outlook.com) sein, um PIM für ein Verzeichnis aktivieren zu können.

1. Klicken Sie auf **Alle Dienste**, und suchen Sie nach dem Dienst **Azure AD Privileged Identity Management**.

    ![Azure AD Privileged Identity Management in „Alle Dienste“](./media/pim-getting-started/pim-all-services-find.png)

1. Klicken Sie, um den PIM-Schnellstart zu öffnen.

1. Klicken Sie in der Liste auf **PIM zustimmen**.

    ![PIM zustimmen](./media/pim-getting-started/consent-pim.png)

1. Klicken Sie auf **Meine Identität überprüfen**, um Ihre Identität mit Azure MFA zu überprüfen. Sie werden aufgefordert, ein Konto auszuwählen.

    ![Konto auswählen](./media/pim-getting-started/pick-account.png)

1. Wenn weitere Informationen zur Überprüfung erforderlich sind, werden Sie durch den Prozess geführt. Weitere Informationen finden Sie unter [Hilfe bei der zweistufigen Überprüfung](https://go.microsoft.com/fwlink/p/?LinkId=708614).

    ![Weitere Informationen erforderlich](./media/pim-getting-started/more-information-required.png)

    Sie könnten beispielsweise zum Angeben der Telefonüberprüfung aufgefordert werden.

    ![Zusätzliche Sicherheitsüberprüfung](./media/pim-getting-started/additional-security-verification.png)

1. Klicken Sie nach Abschluss des Überprüfungsprozesses auf die Schaltfläche **Zustimmen**.

1. Klicken Sie in der daraufhin angezeigten Meldung auf **Ja**, um der Verwendung des PIM-Diensts zuzustimmen.

    ![Meldung: PIM zustimmen](./media/pim-getting-started/consent-pim-message.png)

## <a name="sign-up-pim-for-azure-ad-roles"></a>Registrieren von PIM für Azure AD-Rollen

Nachdem Sie PIM für Ihr Verzeichnis aktiviert haben, müssen Sie PIM für die Verwaltung von Azure AD-Rollen registrieren.

1. Öffnen Sie **Azure AD Privileged Identity Management**.

1. Klicken Sie auf **Azure AD-Rollen**.

    ![Registrieren von PIM für Azure AD-Rollen](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. Klicken Sie auf **Registrieren**.

1. Klicken Sie in der daraufhin angezeigten Meldung auf **Ja**, um PIM für die Verwaltung von Azure AD-Rollen zu registrieren.

    ![Meldung: PIM für Azure AD-Rollen registrieren](./media/pim-getting-started/sign-up-pim-message.png)

    Nach Abschluss der Registrierung sind die Azure AD-Optionen aktiviert. Möglicherweise müssen Sie das Portal aktualisieren.

    Informationen zum Ermitteln und Auswählen der mit PIM zu schützenden Azure-Ressourcen finden Sie unter [Ermitteln von Azure-Ressourcen zur Verwaltung in PIM](pim-resource-roles-discover-resources.md).

## <a name="navigate-to-your-tasks"></a>Navigieren zu Ihren Aufgaben

Sobald PIM eingerichtet ist, können Sie Ihre Identitätsverwaltungsaufgaben ausführen.

![Aufgaben der obersten Ebene für PIM – Screenshot](./media/pim-getting-started/pim-quickstart-tasks.png)

| „Aufgaben“ und „Verwalten“ | BESCHREIBUNG |
| --- | --- |
| **Meine Rollen**  | Zeigt eine Liste mit berechtigten und aktiven Rollen an, die Ihnen zugewiesen sind. Hier können Sie zugewiesene berechtigte Rollen aktivieren. |
| **Meine Anforderungen** | Zeigt Ihre ausstehenden Anforderungen zum Aktivieren berechtigter Rollenzuweisungen an. |
| **Genehmigen von Anforderungen** | Zeigt eine Liste mit Anforderungen zum Aktivieren berechtigter Rollen nach Benutzern in Ihrem Verzeichnis an, für deren Genehmigung Sie zuständig sind. |
| **Zugriff überprüfen** | Zeigt eine Liste mit aktiven Zugriffsüberprüfungen an, die Ihnen zugewiesen sind. Dabei spielt es keine Rolle, ob Sie den Zugriff für sich selbst oder für eine andere Person überprüfen. |
| **Azure AD-Rollen** | Zeigt für Administratoren privilegierter Rollen ein Dashboard und Einstellungen zum Verwalten von Azure AD-Rollenzuweisungen an. Für Benutzer, die kein Administrator für privilegierte Rollen sind, ist dieses Dashboard deaktiviert. Diese Benutzer haben Zugriff auf ein spezielles Dashboard namens „Meine Ansicht“. Auf dem Dashboard „Meine Ansicht“ werden nur Informationen zu dem Benutzer angezeigt, der auf das Dashboard zugreift (also keine Informationen für den gesamten Mandanten). |
| **Azure-Ressourcen** | Zeigt für Administratoren privilegierter Rollen ein Dashboard und Einstellungen zum Verwalten von Azure-Ressourcenrollenzuweisungen an. Für Benutzer, die kein Administrator für privilegierte Rollen sind, ist dieses Dashboard deaktiviert. Diese Benutzer haben Zugriff auf ein spezielles Dashboard namens „Meine Ansicht“. Auf dem Dashboard „Meine Ansicht“ werden nur Informationen zu dem Benutzer angezeigt, der auf das Dashboard zugreift (also keine Informationen für den gesamten Mandanten). |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Hinzufügen einer PIM-Kachel zum Dashboard

Um das Öffnen von PIM zu vereinfachen, sollten Sie Ihrem Azure-Portal-Dashboard eine PIM-Kachel hinzufügen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Klicken Sie auf **Alle Dienste**, und suchen Sie nach dem Dienst **Azure AD Privileged Identity Management**.

    ![Azure AD Privileged Identity Management in „Alle Dienste“](./media/pim-getting-started/pim-all-services-find.png)

1. Klicken Sie, um den PIM-Schnellstart zu öffnen.

1. Aktivieren Sie die Option **Blatt an Dashboard anheften**, um das Blatt „PIM-Schnellstart“ an das Dashboard anzuheften.

    ![Blatt an Dashboard anheften](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    Im Azure-Dashboard wird eine Kachel wie diese angezeigt:

    ![Kachel „PIM-Schnellstart“](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Nächste Schritte

- [Zuweisen von Azure AD-Rollen in PIM](pim-how-to-add-role-to-user.md)
- [Ermitteln von Azure-Ressourcen zur Verwaltung in PIM](pim-resource-roles-discover-resources.md)
