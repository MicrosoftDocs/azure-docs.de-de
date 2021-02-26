---
title: Ändern von Ressourcenrollen für ein Zugriffspaket in der Azure AD-Berechtigungsverwaltung – Azure Active Directory
description: Hier erfahren Sie, wie Sie die Ressourcenrollen für ein vorhandenes Zugriffspaket in der Azure Active Directory-Berechtigungsverwaltung ändern.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/14/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82247ae9817e5ff2e26d0862230654021876406b
ms.sourcegitcommit: 8c8c71a38b6ab2e8622698d4df60cb8a77aa9685
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/01/2021
ms.locfileid: "99223273"
---
# <a name="change-resource-roles-for-an-access-package-in-azure-ad-entitlement-management"></a>Ändern von Ressourcenrollen für ein Zugriffspaket in der Azure AD-Berechtigungsverwaltung

Als Zugriffspaket-Manager können Sie die Ressourcen innerhalb eines Zugriffspakets jederzeit ändern, ohne dass Sie Benutzern den Zugriff auf die neuen Ressourcen bereitstellen oder den Zugriff auf die bisherigen Ressourcen entfernen müssen. In diesem Artikel erfahren Sie, wie Sie die Ressourcenrollen für ein vorhandenes Zugriffspaket ändern.

Das folgende Video enthält eine Übersicht über das Ändern eines Zugriffspakets:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3LD4Z]

## <a name="check-catalog-for-resources"></a>Überprüfen des Katalogs auf Ressourcen

Wenn Sie einem Zugriffspaket Ressourcen hinzufügen möchten, müssen Sie überprüfen, ob die benötigten Ressourcen im Katalog verfügbar sind. Zugriffspaket-Manager können einem Katalog keine Ressourcen hinzufügen, auch wenn Sie deren Besitzer sind. Sie können nur die im Katalog verfügbaren Ressourcen verwenden.

**Erforderliche Rolle:** Globaler Administrator, Benutzeradministrator, Katalogbesitzer oder Zugriffspaket-Manager

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im linken Menü auf **Katalog**, und öffnen Sie den Katalog.

1. Klicken Sie im linken Menü auf **Ressourcen**, um die Liste mit den Ressourcen in diesem Katalog anzuzeigen.

    ![Ressourcenliste eines Katalogs](./media/entitlement-management-access-package-resources/catalog-resources.png)

1. Falls Sie Zugriffspaket-Manager sind und dem Katalog Ressourcen hinzufügen möchten, können Sie den Katalogbesitzer bitten, die erforderlichen Ressourcen hinzuzufügen.

## <a name="add-resource-roles"></a>Hinzufügen von Ressourcenrollen

Eine Ressourcenrolle ist eine Sammlung von Berechtigungen, die einer Ressource zugeordnet sind. Damit Benutzer Ressourcen anfordern können, müssen Sie dem Zugriffspaket Ressourcenrollen hinzufügen. Ressourcenrollen können für Gruppen, Teams, Anwendungen und SharePoint-Websites hinzugefügt werden.

**Erforderliche Rolle:** Globaler Administrator, Benutzeradministrator, Katalogbesitzer oder Zugriffspaket-Manager

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im Menü auf der linken Seite auf **Zugriffspakete**, und öffnen Sie das Zugriffspaket.

1. Klicken Sie im Menü auf der linken Seite auf **Ressourcenrollen**.

1. Klicken Sie auf **Ressourcenrollen hinzufügen**, um die Seite „Hinzufügen von Ressourcenrollen zu einem Zugriffspaket“ zu öffnen.

    ![Zugriffspaket: Ressourcenrollen hinzufügen](./media/entitlement-management-access-package-resources/resource-roles-add.png)

1. Führen Sie abhängig davon, ob Sie eine Gruppe, ein Team, eine Anwendung oder eine SharePoint-Website hinzufügen möchten, die entsprechenden Schritte in einem der folgenden Ressourcenrollenabschnitte aus.

## <a name="add-a-group-or-team-resource-role"></a>Hinzufügen einer Ressourcenrolle für eine Gruppe oder ein Team

Von der Berechtigungsverwaltung können Benutzer automatisch einer Gruppe oder einem Team in Microsoft Teams hinzugefügt werden, wenn sie einem Zugriffspaket zugewiesen werden. 

- Ist eine Gruppe oder ein Team Teil eines Zugriffspakets und wird ein Benutzer diesem Zugriffspaket zugewiesen, wird der Benutzer automatisch dieser Gruppe oder diesem Team hinzugefügt (sofern noch nicht vorhanden).
- Läuft die Zuweisung des Benutzers zu einem Zugriffspaket ab, wird er aus der Gruppe oder dem Team entfernt, sofern er keinem anderen Zugriffspaket zugewiesen ist, dem diese Gruppe oder dieses Team angehört.

Sie können eine beliebige [Azure AD-Sicherheitsgruppe oder Microsoft 365-Gruppe](../fundamentals/active-directory-groups-create-azure-portal.md) auswählen. Administratoren können einem Katalog jede beliebige Gruppe hinzufügen. Das gleiche gilt für Katalogbesitzer, wenn sie Besitzer der Gruppe sind. Beachten Sie beim Auswählen einer Gruppe die folgenden Azure AD-Einschränkungen:

- Wird ein Benutzer als Gruppen- oder Teammitglied hinzugefügt, sind alle anderen Mitglieder dieser Gruppe oder dieses Teams für ihn sichtbar. (Dies gilt auch für Gastbenutzer.)
- In Azure AD kann die Mitgliedschaft einer Gruppe, die über Windows Server Active Directory mit Azure AD Connect synchronisiert oder in Exchange Online als Verteilergruppe erstellt wurde, nicht geändert werden.  
- Die Mitgliedschaft von dynamischen Gruppen kann nicht durch Hinzufügen oder Entfernen eines Mitglieds aktualisiert werden. Daher eignen sich dynamische Gruppenmitgliedschaften nicht für die Azure AD-Berechtigungsverwaltung.

Weitere Informationen finden Sie unter [Vergleichen von Gruppen](/office365/admin/create-groups/compare-groups) und [Microsoft 365-Gruppen und Microsoft-Teams](/microsoftteams/office-365-groups).

1. Klicken Sie auf der Seite **Hinzufügen von Ressourcenrollen zu einem Zugriffspaket** auf **Gruppen und Teams**, um den Bereich „Gruppen auswählen“ zu öffnen.

1. Wählen Sie die Gruppen und Teams aus, die Sie in das Zugriffspaket einschließen möchten.

    ![Zugriffspaket: Ressourcenrollen hinzufügen – Gruppen auswählen](./media/entitlement-management-access-package-resources/group-select.png)

1. Klicken Sie auf **Auswählen**.

    Nachdem Sie die Gruppe oder das Team ausgewählt haben, wird in der Spalte **Untertyp** einer der folgenden Untertypen aufgelistet:

    | Untertyp | BESCHREIBUNG |
    | --- | --- |
    | Sicherheit | Gewähren des Zugriffs auf Ressourcen |
    | Distribution | Senden von Benachrichtigungen an eine Gruppe von Personen |
    | Microsoft 365 | Nicht Teams-fähige Microsoft 365-Gruppe. Wird für die Zusammenarbeit zwischen Benutzern sowohl innerhalb als auch außerhalb des Unternehmens verwendet. |
    | Team | Teams-fähige Microsoft 365-Gruppe. Wird für die Zusammenarbeit zwischen Benutzern sowohl innerhalb als auch außerhalb des Unternehmens verwendet. |

1. Wählen Sie in der Liste **Rolle** die Option **Besitzer** oder **Mitglied** aus.

    In der Regel wird die Rolle „Mitglied“ ausgewählt. Mit der Rolle „Besitzer“ ist es Benutzern erlaubt, andere Mitglieder oder Besitzer hinzuzufügen oder zu entfernen.

    ![Zugriffspaket: Hinzufügen einer Ressourcenrolle für eine Gruppe oder ein Team](./media/entitlement-management-access-package-resources/group-role.png)

1. Klicken Sie auf **Hinzufügen**.

    Alle Benutzer mit einer bestehenden Zuweisung zu dem Zugriffspaket werden durch das Hinzufügen der Gruppe oder des Teams automatisch Mitglied dieser Gruppe.

## <a name="add-an-application-resource-role"></a>Hinzufügen einer Ressourcenrolle für eine Anwendung

Wird ein Benutzer einem Zugriffspaket zugewiesen, kann ihm mit Azure AD automatisch der Zugriff auf eine Azure AD-Unternehmensanwendung zugewiesen werden, einschließlich SaaS-Anwendungen und Verbundanwendungen Ihrer Organisation mit Azure AD. Für Anwendungen, die in Azure AD über die einmalige Verbundanmeldung integriert sind, stellt Azure AD für Benutzer, die diesen Anwendungen zugewiesen sind, Verbundtoken aus.

Anwendungen können über mehrere Rollen verfügen. Wenn Sie einem Zugriffspaket eine Anwendung hinzufügen, die mehr als eine Rolle besitzt, müssen Sie für diese Benutzer die entsprechende Rolle angeben. Wenn Sie Anwendungen entwickeln, finden Sie weitere Informationen zum Hinzufügen dieser Rollen zu Ihren Anwendungen unter [Gewusst wie: Konfigurieren von im SAML-Token ausgestellten Rollenansprüchen für Unternehmensanwendungen](../develop/active-directory-enterprise-app-role-management.md).

Sobald eine Anwendungsrolle Teil eines Zugriffspakets ist, gilt Folgendes:

- Wird ein Benutzer dem Zugriffspaket zugewiesen, wird er der Anwendungsrolle hinzugefügt (sofern noch nicht vorhanden).
- Läuft die Zuweisung des Benutzers zu einem Zugriffspaket ab, wird dessen Zugriff aus der Anwendung entfernt, sofern er keinem anderen Zugriffspaket zugewiesen ist, dem diese Anwendungsrolle angehört.

Beachten Sie Folgendes, wenn Sie eine Anwendung auswählen:

- Anwendungen können auch Gruppen besitzen, die ihren Rollen zugewiesen sind.  Sie können wählen, ob Sie einem Zugriffspaket anstelle einer Anwendungsrolle eine Gruppe hinzufügen möchten. In diesem Fall wird die Anwendung dem Benutzer jedoch nicht als Teil des Zugriffspakets im Portal „Mein Zugriff“ angezeigt.

1. Klicken Sie auf der Seite **Hinzufügen von Ressourcenrollen zu einem Zugriffspaket** auf **Anwendungen**, um den Bereich „Anwendungen auswählen“ zu öffnen.

1. Wählen Sie die Anwendungen aus, die Sie in das Zugriffspaket einschließen möchten.

    ![Zugriffspaket: Ressourcenrollen hinzufügen – Anwendungen auswählen](./media/entitlement-management-access-package-resources/application-select.png)

1. Klicken Sie auf **Auswählen**.

1. Wählen Sie in der Liste **Rolle** eine Anwendungsrolle aus.

    ![Zugriffspaket: Ressourcenrolle für eine Anwendung hinzufügen](./media/entitlement-management-access-package-resources/application-role.png)

1. Klicken Sie auf **Hinzufügen**.

    Alle Benutzer mit einer bestehenden Zuweisung zu dem Zugriffspaket erhalten durch das Hinzufügen der Anwendung automatisch Zugriff auf diese Anwendung.

## <a name="add-a-sharepoint-site-resource-role"></a>Hinzufügen einer Ressourcenrolle für eine SharePoint-Website

Mit Azure AD kann Benutzern, die einem Zugriffspaket zugewiesen sind, automatisch Zugriff auf eine Website oder Websitesammlung von SharePoint Online zugewiesen werden.

1. Klicken Sie auf der Seite **Hinzufügen von Ressourcenrollen zu einem Zugriffspaket** auf **SharePoint-Websites**, um den Bereich „SharePoint Online-Websites auswählen“ zu öffnen.

    :::image type="content" source="media/entitlement-management-access-package-resources/sharepoint-multigeo-portal.png" alt-text="Zugriffspaket: Hinzufügen von Ressourcenrollen: Auswählen von SharePoint-Websites: Portalansicht":::

1. Wenn für SharePoint [Multi-Geo](https://docs.microsoft.com/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365?view=o365-worldwide) aktiviert ist, wählen Sie die Umgebung aus, aus der Sie Websites auswählen möchten.
    
    :::image type="content" source="media/entitlement-management-access-package-resources/sharepoint-multigeo-select.png" alt-text="Zugriffspaket: Hinzufügen von Ressourcenrollen: Auswählen von SharePoint-Websites: Multi-Geo-Websites":::

1. Ist „Multi-Geo“ nicht aktiviert, muss keine Umgebung ausgewählt werden. Wählen Sie die SharePoint Online-Websites aus, die Sie in das Zugriffspaket einschließen möchten.

    ![Zugriffspaket: Ressourcenrollen hinzufügen – SharePoint Online-Websites auswählen](./media/entitlement-management-access-package-resources/sharepoint-site-select.png)

1. Klicken Sie auf **Auswählen**.

1. Wählen Sie in der Liste **Rolle** eine Rolle für die SharePoint Online-Website aus.

    ![Zugriffspaket: Ressourcenrolle für eine SharePoint Online-Website hinzufügen](./media/entitlement-management-access-package-resources/sharepoint-site-role.png)

1. Klicken Sie auf **Hinzufügen**.

    Alle Benutzer mit einer bestehenden Zuweisung zu dem Zugriffspaket erhalten durch das Hinzufügen der SharePoint Online-Website automatisch Zugriff auf diese Website.

## <a name="remove-resource-roles"></a>Entfernen von Ressourcenrollen

**Erforderliche Rolle:** Globaler Administrator, Benutzeradministrator, Katalogbesitzer oder Zugriffspaket-Manager

1. Klicken Sie im Azure-Portal auf **Azure Active Directory** und dann auf **Identity Governance**.

1. Klicken Sie im Menü auf der linken Seite auf **Zugriffspakete**, und öffnen Sie das Zugriffspaket.

1. Klicken Sie im Menü auf der linken Seite auf **Ressourcenrollen**.

1. Suchen Sie in der Liste der Ressourcenrollen nach der Ressourcenrolle, die Sie entfernen möchten.

1. Klicken Sie auf die Auslassungspunkte ( **...** ) und dann auf **Ressourcenrolle entfernen**.

    Allen Benutzern mit einer bestehenden Zuweisung zu dem Zugriffspaket wird durch das Entfernen der Ressourcenrolle automatisch der Zugriff darauf widerrufen.

## <a name="when-changes-are-applied"></a>Wann werden Änderungen angewendet?

In der Azure AD-Berechtigungsverwaltung werden Massenänderungen an Zuweisungen und Ressourcen in Zugriffspaketen mehrmals täglich verarbeitet. Wenn Sie also eine Zuweisung vornehmen oder die Ressourcenrollen Ihres Zugriffspakets ändern, kann es bis zu 24 Stunden dauern, bis diese Änderungen in Azure AD übernommen werden. Hinzu kommt die Zeit für die Weitergabe der Änderungen an andere Microsoft Online Services oder verbundene SaaS-Anwendungen. Betrifft Ihre Änderung nur einige wenige Objekte, wird sie wahrscheinlich innerhalb von ein paar Minuten in Azure AD übernommen. Anschließend wird diese Änderung von anderen Azure AD-Komponenten erkannt, und die SaaS-Anwendungen werden aktualisiert. Betrifft die Änderung hingegen Tausende von Objekten, dauert die Übernahme länger. Enthält Ihr Zugriffspaket beispielsweise zwei Anwendungen und 100 Benutzerzuweisungen, und Sie möchten dem Zugriffspaket eine Rolle für eine SharePoint-Website hinzufügen, kann es möglicherweise eine Weile dauern, bis alle Benutzer Teil dieser SharePoint-Rolle sind. Sie können den Fortschritt über das Azure AD-Überwachungsprotokoll, das Azure AD-Bereitstellungsprotokoll und die Überwachungsprotokolle der SharePoint-Website überwachen.

Wenn Sie ein Teammitglied entfernen, wird es auch aus der Microsoft 365-Gruppe entfernt. Das Entfernen aus der Chatfunktion des Teams kann etwas länger dauern. Weitere Informationen finden Sie unter [Gruppenmitgliedschaft](/microsoftteams/office-365-groups#group-membership).

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen einer Basisgruppe und Hinzufügen von Mitgliedern mit Azure Active Directory](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Vorgehensweise: Konfigurieren von im SAML-Token ausgestellten Rollenansprüchen für Unternehmensanwendungen](../develop/active-directory-enterprise-app-role-management.md)
- [Einführung in SharePoint Online](/sharepoint/introduction)
