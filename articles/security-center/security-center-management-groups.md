---
title: Erzielen der mandantenweiten Sichtbarkeit für Azure Security Center | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie Sie Ihren Sicherheitsstatus bedarfsorientiert verwalten, indem Sie Richtlinien auf alle Abonnements anwenden, die mit Ihrem Azure Active Directory-Mandanten verknüpft sind.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b85c0e93-9982-48ad-b23f-53b367f22b10
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2018
ms.author: memildin
ms.openlocfilehash: 7252a6ccd77212f75f5db54e5f3fcad7aa2df50a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013801"
---
# <a name="gain-tenant-wide-visibility-for-azure-security-center"></a>Erzielen der mandantenweiten Sichtbarkeit für Azure Security Center
In diesem Artikel wird erläutert, wie Sie den Sicherheitsstatus Ihrer Organisation bedarfsorientiert verwalten, indem Sie Sicherheitsrichtlinien auf alle Azure-Abonnements anwenden, die mit Ihrem Azure Active Directory-Mandanten verknüpft sind.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="management-groups"></a>Verwaltungsgruppen
Azure-Verwaltungsgruppen ermöglichen Ihnen das effiziente Verwalten der Bereiche Zugriff, Richtlinien und Berichterstellung für Gruppen von Abonnements sowie das effektive Verwalten der gesamten Azure-Umgebung, indem Aktionen für die Stammverwaltungsgruppe durchgeführt werden. Jeder Azure AD-Mandant erhält eine einzelne Verwaltungsgruppe der obersten Ebene, die als Stammverwaltungsgruppe bezeichnet wird. Die Stammverwaltungsgruppe ist in die Hierarchie integriert, sodass ihr alle Verwaltungsgruppen und Abonnements untergeordnet sind. Diese Gruppe ermöglicht das Anwenden von globalen Richtlinien und Azure-Rollenzuweisungen auf Verzeichnisebene. 

Die Stammverwaltungsgruppe wird automatisch erstellt, wenn Sie die folgenden Aktionen durchführen: 
1. Aktivieren Sie die Nutzung von Azure-Verwaltungsgruppen, indem Sie im [Azure-Portal](https://portal.azure.com) zu **Verwaltungsgruppen** navigieren.
2. Erstellen Sie eine Verwaltungsgruppe über einen API-Aufruf.
3. Erstellen Sie eine Verwaltungsgruppe mit PowerShell.

Eine ausführliche Übersicht über Verwaltungsgruppen finden Sie im Artikel [Organisieren Ihrer Ressourcen mit Azure-Verwaltungsgruppen](../governance/management-groups/overview.md).

## <a name="create-a-management-group-in-the-azure-portal"></a>Erstellen einer Verwaltungsgruppe im Azure-Portal
Sie können Abonnements in Verwaltungsgruppen organisieren und Ihre Governancerichtlinien auf die Verwaltungsgruppen anwenden. Alle Abonnements in einer Verwaltungsgruppe erben automatisch die auf die Verwaltungsgruppe angewendeten Richtlinien. Für das Security Center-Onboarding sind Verwaltungsgruppen zwar nicht erforderlich, aber es wird dringend empfohlen, mindestens eine Verwaltungsgruppe zu erstellen, damit die Stammverwaltungsgruppe erstellt wird. Nachdem die Gruppe erstellt wurde, werden alle Abonnements unter Ihrem Azure AD-Mandanten damit verknüpft. Eine Anleitung für PowerShell und weitere Informationen finden Sie unter [Erstellen von Verwaltungsgruppen zum Organisieren und Verwalten von Ressourcen](../governance/management-groups/create-management-group-portal.md).

 
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie auf **Alle Dienste** > **Verwaltungsgruppen**.
3. Klicken Sie auf der Hauptseite auf **New Management group** (Neue Verwaltungsgruppe). 

    ![Hauptgruppe](./media/security-center-management-groups/main.png) 
4.  Füllen Sie das Feld für die Verwaltungsgruppen-ID aus. 
    - Die **ID der Verwaltungsgruppe** ist der eindeutige Bezeichner des Verzeichnisses, der zum Übermitteln von Befehlen für diese Verwaltungsgruppe verwendet wird. Dieser Bezeichner kann nach der Erstellung nicht bearbeitet werden, da er im gesamten Azure-System zum Identifizieren dieser Gruppe verwendet wird. 
    - Im Feld für den Anzeigenamen wird der Name angegeben, der im Azure-Portal angezeigt wird. Beim Erstellen der Verwaltungsgruppe gibt es ein optionales Feld für einen separaten Anzeigenamen, der jederzeit geändert werden kann.  

      ![Erstellen](./media/security-center-management-groups/create_context_menu.png)  
5.  Wählen Sie **Speichern** aus.

### <a name="view-management-groups-in-the-azure-portal"></a>Anzeigen von Verwaltungsgruppen im Azure-Portal
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie zum Anzeigen von Verwaltungsgruppen im Hauptmenü von Azure die Option **Alle Dienste**.
3. Wählen Sie unter **Allgemein** die Option **Verwaltungsgruppen**.

    ![Erstellen einer Verwaltungsgruppe](./media/security-center-management-groups/all-services.png)

## <a name="grant-tenant-level-visibility-and-the-ability-to-assign-policies"></a>Gewähren der Sichtbarkeit auf Mandantenebene und der Möglichkeit zum Zuweisen von Richtlinien

Eine Azure-Rolle mit ausreichenden Leseberechtigungen muss für die Stammverwaltungsgruppe zugewiesen werden, um Sichtbarkeit für den Sicherheitsstatus aller Abonnements zu erzielen, die unter dem Azure AD-Mandanten registriert sind.

### <a name="elevate-access-for-a-global-administrator-in-azure-active-directory"></a>Erhöhen der Zugriffsrechte für einen globalen Administrator in Azure Active Directory
Ein Azure Active Directory-Mandantenadministrator hat keinen direkten Zugriff auf Azure-Abonnements. Als Verzeichnisadministrator können diese Personen aber ihre Rechte erhöhen und sich eine Rolle zuweisen, mit der der Zugriff möglich ist. Ein Azure AD-Mandantenadministrator muss für sich auf der Ebene der Stammverwaltungsgruppe das Höherstufen auf den Benutzerzugriffsadministrator durchführen, um Azure-Rollen zuweisen zu können. Eine PowerShell-Anleitung und weitere Informationen finden Sie unter [Erhöhen der Zugriffsrechte für einen globalen Administrator in Azure Active Directory](../role-based-access-control/elevate-access-global-admin.md). 


1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) oder [Azure Active Directory Admin Center](https://aad.portal.azure.com) an.

2. Klicken Sie in der Navigationsliste auf **Azure Active Directory** und dann auf **Eigenschaften**.

   ![Azure AD-Eigenschaften: Screenshot](./media/security-center-management-groups/aad-properties.png)

3. Legen Sie unter **Zugriffsverwaltung für Azure-Ressourcen** den Schalter auf **Ja** fest.

   ![Zugriffsverwaltung für Azure-Ressourcen – Screenshot](./media/security-center-management-groups/aad-properties-global-admin-setting.png)

   - Wenn Sie den Schalter auf „Ja“ festlegen, wird Ihnen in Azure RBAC im Stammbereich (/) die Rolle „Benutzerzugriffsadministrator“ zugewiesen. Dadurch erhalten Sie die Berechtigung, Rollen in allen Azure-Abonnements und Verwaltungsgruppen zuzuweisen, die diesem Azure AD-Verzeichnis zugeordnet sind. Dieser Schalter ist nur für Benutzer verfügbar, denen in Azure AD die Rolle des globalen Administrators zugewiesen wurde.

   - Wenn Sie den Schalter auf „Nein“ festlegen, wird die Rolle „Benutzerzugriffsadministrator“ in Azure RBAC aus Ihrem Benutzerkonto entfernt. Sie können dann keine Rollen mehr in allen Azure-Abonnements und Verwaltungsgruppen zuweisen, die diesem Azure AD-Verzeichnis zugeordnet sind. Sie können nur die Azure-Abonnements und Verwaltungsgruppen anzeigen und verwalten, für die Ihnen der Zugriff gewährt wurde.

4. Klicken Sie auf **Speichern**, um Ihre Einstellung zu speichern.

    - Diese Einstellung ist keine globale Eigenschaft und gilt nur für den derzeit angemeldeten Benutzer.

5. Führen Sie die Aufgaben aus, die Sie mit erhöhten Zugriffsrechten vornehmen müssen. Wenn Sie fertig sind, können Sie wieder **Nein** festlegen.


### <a name="assign-azure-roles-to-users"></a>Zuweisen von Azure-Rollen zu Benutzern
Um die Sichtbarkeit aller Abonnements zu erzielen, müssen Mandantenadministratoren jedem Benutzer, dem mandantenweite Sichtbarkeit gewährt werden soll, eine geeignete Azure-Rolle auf der Stammebene der Verwaltungsgruppe zuweisen (auch sich selbst). Die empfohlenen Rollen, die zugewiesen werden sollten, sind **Sicherheitsadministrator** oder **Sicherheitsleseberechtigter**. Im Allgemeinen ist die Rolle „Sicherheitsadministrator“ erforderlich, um Richtlinien auf der Stammebene anzuwenden, während „Sicherheitsleseberechtigter“ ausreicht, um Sichtbarkeit für die Mandantenebene bereitzustellen. Weitere Informationen zu den Berechtigungen, die durch diese Rollen gewährt werden, finden Sie in der [Beschreibung der integrierten Rolle „Sicherheitsadministrator“](../role-based-access-control/built-in-roles.md#security-admin) bzw. in der [Beschreibung der integrierten Rolle „Sicherheitsleseberechtigter“](../role-based-access-control/built-in-roles.md#security-reader).


#### <a name="assign-azure-roles-to-users-through-the-azure-portal"></a>Weisen Sie Benutzern Azure-Rollen über das Azure-Portal zu: 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 
1. Wählen Sie zum Anzeigen von Verwaltungsgruppen **Alle Dienste** im Azure-Hauptmenü und dann **Verwaltungsgruppen** aus.
1.  Wählen Sie eine Verwaltungsgruppe aus, und klicken Sie auf **Details**.

    ![Screenshot der Verwaltungsgruppendetails](./media/security-center-management-groups/management-group-details.PNG)
 
1. Klicken Sie auf **Zugriffssteuerung (IAM)** und dann auf **Rollenzuweisungen**.

1. Klicken Sie auf **Rollenzuweisung hinzufügen**.

1. Wählen Sie die zuzuweisende Rolle und den Benutzer aus, und klicken Sie dann auf **Speichern**.  
   
   ![Screenshot zum Hinzufügen der Rolle „Sicherheitsleseberechtigter“](./media/security-center-management-groups/asc-security-reader.png)


#### <a name="assign-azure-roles-to-users-with-powershell"></a>Weisen Sie Benutzern Azure-Rollen mit PowerShell zu: 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Installieren Sie [Azure PowerShell](/powershell/azure/install-az-ps).
2. Führen Sie die folgenden Befehle aus: 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. Melden Sie sich mit den Anmeldeinformationen des globalen Administrators an, wenn Sie dazu aufgefordert werden. 

    ![Screenshot: Aufforderung zur Anmeldung](./media/security-center-management-groups/azurerm-sign-in.PNG)

4. Gewähren Sie die Berechtigungen der Rolle „Leser“, indem Sie den folgenden Befehl ausführen:

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. Verwenden Sie zum Entfernen der Rolle den folgenden Befehl: 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

### <a name="open-or-refresh-security-center"></a>Öffnen oder Aktualisieren von Security Center
Wenn Sie erhöhte Zugriffsrechte besitzen, können Sie Azure Security Center öffnen bzw. aktualisieren, um sicherzustellen, dass die Sichtbarkeit für alle Abonnements Ihres Azure AD-Mandanten erzielt wurde. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 
2. Stellen Sie sicher, dass Sie alle Abonnements in der Abonnementauswahl auswählen, die in Security Center angezeigt werden sollen.

    ![Screenshot: Abonnementauswahl](./media/security-center-management-groups/subscription-selector.png)

1. Wählen Sie im Hauptmenü von Azure die Option **Alle Dienste** und dann **Security Center**.
2. Unter **Übersicht** wird ein Diagramm zur Abonnementabdeckung angezeigt.

    ![Screenshot: Diagramm zur Abonnementabdeckung](./media/security-center-management-groups/security-center-subscription-coverage.png)

3. Klicken Sie auf **Abdeckung**, um die Liste mit den abgedeckten Abonnements anzuzeigen. 

    ![Screenshot: Liste zur Abonnementabdeckung](./media/security-center-management-groups/security-center-coverage.png)

### <a name="remove-elevated-access"></a>Entfernen der erhöhten Zugriffsrechte 
Nachdem den Benutzern die Azure-Rollen zugewiesen wurden, sollte der Mandantenadministrator für sich selbst die Rolle „Benutzerzugriffsadministrator“ entfernen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) oder [Azure Active Directory Admin Center](https://aad.portal.azure.com) an.

2. Klicken Sie in der Navigationsliste auf **Azure Active Directory** und dann auf **Eigenschaften**.

3. Legen Sie unter **Zugriffsverwaltung für Azure-Ressourcen** den Schalter auf **Nein** fest.

4. Klicken Sie auf **Speichern**, um Ihre Einstellung zu speichern.



## <a name="adding-subscriptions-to-a-management-group"></a>Hinzufügen von Abonnements zu einer Verwaltungsgruppe
Sie können Abonnements der von Ihnen erstellten Verwaltungsgruppe hinzufügen. Diese Schritte sind zum Erzielen der mandantenweiten Sichtbarkeit und der globalen Verwaltung der Richtlinien und des Zugriffs nicht obligatorisch.

1. Wählen Sie unter **Verwaltungsgruppen** eine Verwaltungsgruppe aus, die Sie Ihrem Abonnement hinzufügen möchten.

    ![Auswählen einer Verwaltungsgruppe zum Hinzufügen zum Abonnement](./media/security-center-management-groups/management-group-subscriptions.png)

2. Wählen Sie **Vorhandene hinzufügen**.

    ![Vorhandene hinzufügen](./media/security-center-management-groups/add-existing.png)

3. Geben Sie das Abonnement unter **Vorhandene Ressource hinzufügen** ein, und klicken Sie auf **Speichern**.

4. Wiederholen Sie die Schritte 1 bis 3, bis Sie alle betreffenden Abonnements hinzugefügt haben.

   > [!NOTE]
   > Verwaltungsgruppen können sowohl Abonnements als auch untergeordnete Verwaltungsgruppen enthalten. Wenn Sie einem Benutzer eine Azure-Rolle für die übergeordnete Verwaltungsgruppe zuweisen, wird der Zugriff von den Abonnements der untergeordneten Verwaltungsgruppe geerbt. Richtlinien, die für die übergeordnete Verwaltungsgruppe festgelegt werden, werden ebenfalls von den untergeordneten Elementen geerbt. 

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel wurde beschrieben, wie Sie mandantenweite Sichtbarkeit für Azure Security Center erzielen. Weitere Informationen zu Security Center finden Sie in den folgenden Artikeln:

> [!div class="nextstepaction"]
> [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md)

> [!div class="nextstepaction"]
> [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md)