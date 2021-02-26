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
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: 13cbc2e9451221fef951eb6fac4c6b2772275122
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99556426"
---
# <a name="organize-management-groups-subscriptions-and-tenant-wide-visibility"></a>Organisieren von Verwaltungsgruppen, Abonnements und mandantenweiter Sichtbarkeit

In diesem Artikel wird erläutert, wie Sie den Sicherheitsstatus Ihrer Organisation bedarfsorientiert verwalten, indem Sie Sicherheitsrichtlinien auf alle Azure-Abonnements anwenden, die mit Ihrem Azure Active Directory-Mandanten verknüpft sind.

Eine Azure-Rolle mit ausreichenden Leseberechtigungen muss für die Stammverwaltungsgruppe zugewiesen werden, um Sichtbarkeit für den Sicherheitsstatus aller Abonnements zu erzielen, die unter dem Azure AD-Mandanten registriert sind.


## <a name="organize-your-subscriptions-into-management-groups"></a>Organisieren von Abonnements in Verwaltungsgruppen

### <a name="introduction-to-management-groups"></a>Einführung in Verwaltungsgruppen

Azure-Verwaltungsgruppen ermöglichen Ihnen das effiziente Verwalten der Bereiche Zugriff, Richtlinien und Berichterstellung für Gruppen von Abonnements sowie das effektive Verwalten der gesamten Azure-Umgebung, indem Aktionen für die Stammverwaltungsgruppe durchgeführt werden. Sie können Abonnements in Verwaltungsgruppen organisieren und Ihre Governancerichtlinien auf die Verwaltungsgruppen anwenden. Alle Abonnements in einer Verwaltungsgruppe erben automatisch die auf die Verwaltungsgruppe angewendeten Richtlinien. 

Jeder Azure AD-Mandant wird einer einzelnen Verwaltungsgruppe der obersten Ebene zugewiesen, die als **Stammverwaltungsgruppe** bezeichnet wird. Die Stammverwaltungsgruppe ist in die Hierarchie integriert, sodass ihr alle Verwaltungsgruppen und Abonnements untergeordnet sind. Diese Gruppe ermöglicht das Anwenden von globalen Richtlinien und Azure-Rollenzuweisungen auf Verzeichnisebene. 

Die Stammverwaltungsgruppe wird automatisch erstellt, wenn Sie die folgenden Aktionen durchführen: 
- Öffnen Sie **Verwaltungsgruppen** im [Azure-Portal](https://portal.azure.com).
- Erstellen Sie per API-Aufruf eine Verwaltungsgruppe.
- Erstellen Sie eine Verwaltungsgruppe mit PowerShell. Eine Anleitung für PowerShell finden Sie unter [Erstellen von Verwaltungsgruppen zum Organisieren und Verwalten von Ressourcen](../governance/management-groups/create-management-group-portal.md).

Für das Security Center-Onboarding sind Verwaltungsgruppen zwar nicht erforderlich, aber es wird empfohlen, mindestens eine solche Gruppe zu erstellen, damit die Stammverwaltungsgruppe erstellt wird. Nachdem die Gruppe erstellt wurde, werden alle Abonnements unter Ihrem Azure AD-Mandanten damit verknüpft. 


Eine ausführliche Übersicht über Verwaltungsgruppen finden Sie im Artikel [Organisieren Ihrer Ressourcen mit Azure-Verwaltungsgruppen](../governance/management-groups/overview.md).

### <a name="view-and-create-management-groups-in-the-azure-portal"></a>Anzeigen und Erstellen von Verwaltungsgruppen im Azure-Portal

1. Verwenden Sie im [Azure-Portal](https://portal.azure.com) das Suchfeld in der oberen Leiste, um **Verwaltungsgruppen** zu suchen und zu öffnen.

    :::image type="content" source="./media/security-center-management-groups/open-management-groups-service.png" alt-text="Zugreifen auf Verwaltungsgruppen":::

    Die Liste Ihrer Verwaltungsgruppen wird angezeigt.

1. Zum Erstellen einer Verwaltungsgruppe klicken Sie auf **Verwaltungsgruppe hinzufügen**, geben die relevanten Details ein und klicken auf **Speichern**.

    :::image type="content" source="media/security-center-management-groups/add-management-group.png" alt-text="Hinzufügen einer Verwaltungsgruppe zu Azure":::

    - Die **ID der Verwaltungsgruppe** ist der eindeutige Bezeichner des Verzeichnisses, der zum Übermitteln von Befehlen für diese Verwaltungsgruppe verwendet wird. Dieser Bezeichner kann nach der Erstellung nicht bearbeitet werden, da er im gesamten Azure-System zum Identifizieren dieser Gruppe verwendet wird. 
    - Im Feld für den Anzeigenamen wird der Name angegeben, der im Azure-Portal angezeigt wird. Beim Erstellen der Verwaltungsgruppe gibt es ein optionales Feld für einen separaten Anzeigenamen, der jederzeit geändert werden kann.  


### <a name="add-subscriptions-to-a-management-group"></a>Hinzufügen von Abonnements zu einer Verwaltungsgruppe
Sie können Abonnements der von Ihnen erstellten Verwaltungsgruppe hinzufügen.

1. Wählen Sie unter **Verwaltungsgruppen** die Verwaltungsgruppe für Ihr Abonnement aus.

    :::image type="content" source="./media/security-center-management-groups/management-group-subscriptions.png" alt-text="Auswählen einer Verwaltungsgruppe für das Abonnement":::

1. Wenn die Seite der Gruppe geöffnet wird, klicken Sie auf **Details**.

    :::image type="content" source="./media/security-center-management-groups/management-group-details-page.png" alt-text="Öffnen der Detailseite einer Verwaltungsgruppe":::

1. Klicken Sie auf der Detailseite der Gruppe auf **Abonnement hinzufügen**, wählen Sie Ihre Abonnements aus, und klicken Sie auf **Speichern**. Wiederholen Sie diese Schritte, bis Sie alle betreffenden Abonnements hinzugefügt haben.

    :::image type="content" source="./media/security-center-management-groups/management-group-add-subscriptions.png" alt-text="Hinzufügen eines Abonnements zu einer Verwaltungsgruppe":::
   > [!IMPORTANT]
   > Verwaltungsgruppen können sowohl Abonnements als auch untergeordnete Verwaltungsgruppen enthalten. Wenn Sie einem Benutzer eine Azure-Rolle für die übergeordnete Verwaltungsgruppe zuweisen, wird der Zugriff von den Abonnements der untergeordneten Verwaltungsgruppe geerbt. Richtlinien, die für die übergeordnete Verwaltungsgruppe festgelegt werden, werden ebenfalls von den untergeordneten Elementen geerbt. 


## <a name="grant-tenant-wide-permissions-to-yourself"></a>Erteilen von mandantenweiten Berechtigungen an sich selbst

Ein Benutzer mit der Azure Active Directory-Rolle **Globaler Administrator** hat möglicherweise mandantenweite Aufgaben, aber keine Azure-Berechtigungen zum Anzeigen der organisationsweiten Informationen in Azure Security Center. Eine Berechtigungserhöhung ist erforderlich, da Azure AD-Rollenzuweisungen keinen Zugriff auf Azure-Ressourcen gewähren. 

> [!TIP]
> Weitere Informationen zur Berechtigungserhöhung für die Rolle „Globaler Administrator“ finden Sie unter [Erhöhen des Zugriffs zur Verwaltung aller Azure-Abonnements und Verwaltungsgruppen](../role-based-access-control/elevate-access-global-admin.md).

So weisen Sie sich selbst Berechtigungen auf Mandantenebene zu:

1. Wenn Ihre Organisation den Ressourcenzugriff über [Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-configure.md) oder ein anderes PIM-Tool verwaltet, muss die Rolle des globalen Administrators für den Benutzer, der nachfolgende Prozedur ausführt, aktiv sein.

1. Öffnen Sie als globaler Administrator ohne Zuweisung in der Stammverwaltungsgruppe des Mandanten die Security Center-Seite **Übersicht**, und klicken Sie im Banner auf den Link **Mandantenweite Sichtbarkeit**. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-banner.png" alt-text="Aktivieren von mandantenweiten Berechtigungen in Azure Security Center":::

1. Wählen Sie die neue Azure-Rolle aus, die Sie zuweisen möchten. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-form.png" alt-text="Formular zum Definieren der Berechtigungen auf Mandantenebene, die dem Benutzer zugewiesen werden sollen":::

    > [!TIP]
    > Im Allgemeinen ist die Rolle „Sicherheitsadministrator“ erforderlich, um Richtlinien auf der Stammebene anzuwenden, während „Sicherheitsleseberechtigter“ ausreicht, um Sichtbarkeit für die Mandantenebene bereitzustellen. Weitere Informationen zu den Berechtigungen, die durch diese Rollen gewährt werden, finden Sie in der [Beschreibung der integrierten Rolle „Sicherheitsadministrator“](../role-based-access-control/built-in-roles.md#security-admin) bzw. in der [Beschreibung der integrierten Rolle „Sicherheitsleseberechtigter“](../role-based-access-control/built-in-roles.md#security-reader).
    >
    > Informationen zu den Unterschieden zwischen diesen Rollen in Bezug auf Security Center finden Sie in der Tabelle unter [Rollen und zulässige Aktionen](security-center-permissions.md#roles-and-allowed-actions).

    Eine organisationsweite Ansicht lässt sich erzielen, indem Sie Rollen auf Ebene der Stammverwaltungsgruppe des Mandanten zuweisen.  

1. Melden Sie sich vom Azure-Portal ab und dann wieder an.

1. Wenn Sie erhöhte Zugriffsrechte besitzen, können Sie Azure Security Center öffnen bzw. aktualisieren, um sicherzustellen, dass die Sichtbarkeit für alle Abonnements Ihres Azure AD-Mandanten erzielt wurde. 


## <a name="request-tenant-wide-permissions-when-yours-are-insufficient"></a>Anfordern mandantenweiter Berechtigungen, wenn die eigenen Berechtigungen nicht ausreichen

Wenn Sie sich bei Security Center anmelden und ein Banner sehen, das Sie darüber informiert, dass Ihre Ansicht eingeschränkt ist, können Sie durchklicken, um eine Anforderung an den globalen Administrator Ihrer Organisation zu senden. In die Anforderung können Sie die Rolle einschließen, deren Zuweisung Sie wünschen, und der globale Administrator entscheidet, welche Rolle gewährt wird. 

Der globale Administrator entscheidet, ob er diese Anforderungen annimmt oder ablehnt. 

> [!IMPORTANT]
> Sie können nur alle sieben Tage eine Anforderung übermitteln.

So fordern Sie von ihrem globalen Administrator erhöhte Berechtigungen an:

1. Öffnen Sie Azure Security Center über das Azure-Portal.

1. Wenn das Banner „Sie sehen eingeschränkte Informationen.“ angezeigt wird, wählen Sie es aus.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="Banner mit der Information, dass der Benutzer mandantenweite Berechtigungen anfordern kann":::

1. Wählen Sie im ausführlichen Anforderungsformular die gewünschte Rolle und die Begründung aus, warum Sie diese Berechtigungen benötigen.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-details.png" alt-text="Detailseite zum Anfordern mandantenweiter Berechtigungen von ihrem globalen Azure-Administrator":::

1. Wählen Sie **Zugriff anfordern** aus.

    Eine E-Mail wird an den globalen Administrator gesendet. Die E-Mail enthält einen Link zu Security Center, wo die Anforderung genehmigt oder abgelehnt werden kann.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-email.png" alt-text="Bezüglich neuer Berechtigungen an den globalen Administrator gesendete E-Mail":::

    Sobald der globale Administrator **Anforderung überprüfen** auswählt und den Prozess abschließt, wird die Entscheidung per E-Mail an den anfordernden Benutzer gesendet. 

## <a name="assign-azure-roles-to-other-users"></a>Zuweisen von Azure-Rollen zu anderen Benutzern

### <a name="assign-azure-roles-to-users-through-the-azure-portal"></a>Weisen Sie Benutzern Azure-Rollen über das Azure-Portal zu: 
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 
1. Wählen Sie zum Anzeigen von Verwaltungsgruppen **Alle Dienste** im Azure-Hauptmenü und dann **Verwaltungsgruppen** aus.
1.  Wählen Sie eine Verwaltungsgruppe aus, und klicken Sie auf **Details**.

    :::image type="content" source="./media/security-center-management-groups/management-group-details.PNG" alt-text="Screenshot der Verwaltungsgruppendetails":::

1. Klicken Sie auf **Zugriffssteuerung (IAM)** und dann auf **Rollenzuweisungen**.
1. Wählen Sie **Rollenzuweisung hinzufügen** aus.
1. Wählen Sie die zuzuweisende Rolle und den Benutzer aus, und klicken Sie dann auf **Speichern**.  
   
   ![Screenshot zum Hinzufügen der Rolle „Sicherheitsleseberechtigter“](./media/security-center-management-groups/asc-security-reader.png)

### <a name="assign-azure-roles-to-users-with-powershell"></a>Weisen Sie Benutzern Azure-Rollen mit PowerShell zu: 
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

## <a name="remove-elevated-access"></a>Entfernen der erhöhten Zugriffsrechte 

Nachdem den Benutzern die Azure-Rollen zugewiesen wurden, sollte der Mandantenadministrator für sich selbst die Rolle „Benutzerzugriffsadministrator“ entfernen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) oder [Azure Active Directory Admin Center](https://aad.portal.azure.com) an.

2. Wählen Sie in der Navigationsliste **Azure Active Directory** aus, und klicken Sie dann auf **Eigenschaften**.

3. Legen Sie unter **Zugriffsverwaltung für Azure-Ressourcen** den Schalter auf **Nein** fest.

4. Wählen Sie **Speichern** aus, um Ihre Einstellungen zu speichern.



## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel wurde beschrieben, wie Sie mandantenweite Sichtbarkeit für Azure Security Center erzielen. Verwandte Informationen

- [Berechtigungen in Azure Security Center](security-center-permissions.md)