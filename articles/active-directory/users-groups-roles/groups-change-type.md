---
title: Ändern des Typs der statischen Gruppenmitgliedschaft in „Dynamisch“ – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Mitgliedschaftsregeln erstellt werden, um Gruppen automatisch aufzufüllen. Sie finden hier außerdem eine Regelreferenz.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: dd753ca4994975302a0bc6fede61964f80196d7c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60472070"
---
# <a name="change-static-group-membership-to-dynamic-in-azure-active-directory"></a>Ändern der statischen Gruppenmitgliedschaft in „Dynamisch“ in Azure Active Directory

Sie können die Mitgliedschaft einer Gruppe in Azure Active Directory (Azure AD) von „Statisch“ in „Dynamisch“ ändern (oder umgekehrt). Azure AD behält den gleichen Gruppennamen und die ID im System bei, sodass alle vorhandenen Verweise auf die Gruppe weiterhin gültig sind. Wenn Sie stattdessen eine neue Gruppe erstellen, müssen Sie diese Verweise aktualisieren. Bei der dynamischen Gruppenmitgliedschaft entfällt der Verwaltungsaufwand für das Hinzufügen und Entfernen von Benutzern. In diesem Artikel wird beschrieben, wie Sie für vorhandene Gruppen die Mitgliedschaft von „Statisch“ in „Dynamisch“ ändern, indem Sie entweder Azure AD Admin Center oder PowerShell-Cmdlets verwenden.

> [!WARNING]
> Wenn Sie eine vorhandene statische Gruppe in eine dynamische Gruppe ändern, werden alle vorhandenen Mitglieder aus der Gruppe entfernt, und anschließend wird die Mitgliedschaftsregel verarbeitet, um neue Mitglieder hinzuzufügen. Wenn die Gruppe verwendet wird, um den Zugriff auf Apps oder Ressourcen zu steuern, sollten Sie Folgendes beachten: Die ursprünglichen Mitglieder haben unter Umständen erst wieder Zugriff, wenn die Mitgliedschaftsregel vollständig verarbeitet wurde.
>
> Sie sollten die neue Mitgliedsschaftsregel vorher testen, um sicherzustellen, dass sich die neue Mitgliedschaft in der Gruppe wie erwartet verhält.

## <a name="change-the-membership-type-for-a-group"></a>Ändern des Typs der Mitgliedschaft für eine Gruppe

1. Melden Sie sich beim [Azure AD Admin Center](https://aad.portal.azure.com) über ein Konto an, das als globaler Administrator oder Benutzeradministrator in Ihrem Mandanten konfiguriert ist.
2. Wählen Sie **Gruppen** aus.
3. Öffnen Sie in der Liste **Alle Gruppen** die Gruppe, die Sie ändern möchten.
4. Wählen Sie **Eigenschaften** aus.
5. Wählen Sie auf der Seite **Eigenschaften** für die Gruppe einen **Mitgliedschaftstyp** aus, je nach gewünschtem Mitgliedschaftstyp „Zugewiesen (statisch)“, „Dynamischer Benutzer“ oder „Dynamisches Gerät“. Für die dynamische Mitgliedschaft können Sie im Regel-Generator Optionen für eine einfache Regel auswählen oder selbst eine Mitgliedschaftsregel schreiben. 

Die folgenden Schritte sind ein Beispiel für die Änderung der Mitgliedschaft einer Gruppe von Benutzern von statisch zu dynamisch.

1. Wählen Sie auf der Seite **Eigenschaften** für Ihre ausgewählte Gruppe den **Mitgliedschaftstyp** **Dynamischer Benutzer** aus, und wählen Sie dann in dem Dialogfeld, in dem die Änderungen der Gruppenmitgliedschaft erläutert werden, zum Fortfahren die Option „Ja“ aus. 
  
   ![Mitgliedschaftstyp „Dynamischer Benutzer“ auswählen](./media/groups-change-type/select-group-to-convert.png)
  
2. Wählen Sie **Dynamische Abfrage hinzufügen** aus, und geben Sie dann die Regel an.
  
   ![Eingeben der Regel für die dynamische Gruppe](./media/groups-change-type/enter-rule.png)
  
3. Wählen Sie nach dem Erstellen der Regel unten auf der Seite **Abfrage hinzufügen** aus.
4. Wählen Sie für die Gruppe **Speichern** auf der Seite **Eigenschaften** aus, um die Änderungen zu speichern. Der **Mitgliedschaftstyp** der Gruppe wird sofort in der Gruppenliste aktualisiert.

> [!TIP]
> Wenn die eingegebene Mitgliedschaftsregel falsch war, kann dies zu Fehlern bei der Gruppenkonvertierung führen. Oben rechts im Portal wird eine Benachrichtigung angezeigt, die erläutert, warum die Regel nicht vom System angenommen werden konnte. Lesen Sie sie sorgfältig, um zu erfahren, wie die Regel angepasst werden kann, damit sie gültig ist. Beispiele für die Regelsyntax und eine vollständige Liste mit den unterstützten Eigenschaften, Operatoren und Werten für eine Mitgliedschaftsregel finden Sie unter [Regeln für eine dynamische Mitgliedschaft für Gruppen in Azure Active Directory](groups-dynamic-membership.md).

## <a name="change-membership-type-for-a-group-powershell"></a>Ändern des Typs der Mitgliedschaft für eine Gruppe (PowerShell)

> [!NOTE]
> Zum Ändern der Eigenschaften dynamischer Gruppen müssen Sie Cmdlets aus **der Vorschauversion von** [Azure AD PowerShell Version 2](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0) verwenden. Sie können die Vorschau aus dem [PowerShell-Katalog](https://www.powershellgallery.com/packages/AzureADPreview) installieren.

Hier folgt ein Beispiel für Funktionen, die die Verwaltung der Mitgliedschaft für eine vorhandene Gruppe wechseln. In diesem Beispiel wird darauf geachtet, dass die GroupTypes-Eigenschaft ordnungsgemäß geändert wird und alle Werte erhalten bleiben, die dort vorhanden sind und keine Beziehung zur dynamischen Mitgliedschaft aufweisen.

```powershell
#The moniker for dynamic groups as used in the GroupTypes property of a group object
$dynamicGroupTypeString = "DynamicMembership"

function ConvertDynamicGroupToStatic
{
    Param([string]$groupId)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -eq $null -or !$groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a static group. Aborting conversion.";
    }


    #remove the type for dynamic groups, but keep the other type values
    $groupTypes.Remove($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to remove the dynamic type, ii) pause execution of the current rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "Paused"
}

function ConvertStaticGroupToDynamic
{
    Param([string]$groupId, [string]$dynamicMembershipRule)

    #existing group types
    [System.Collections.ArrayList]$groupTypes = (Get-AzureAdMsGroup -Id $groupId).GroupTypes

    if($groupTypes -ne $null -and $groupTypes.Contains($dynamicGroupTypeString))
    {
        throw "This group is already a dynamic group. Aborting conversion.";
    }
    #add the dynamic group type to existing types
    $groupTypes.Add($dynamicGroupTypeString)

    #modify the group properties to make it a static group: i) change GroupTypes to add the dynamic type, ii) start execution of the rule, iii) set the rule
    Set-AzureAdMsGroup -Id $groupId -GroupTypes $groupTypes.ToArray() -MembershipRuleProcessingState "On" -MembershipRule $dynamicMembershipRule
}
```
So erstellen Sie eine statische Gruppe

```powershell
ConvertDynamicGroupToStatic "a58913b2-eee4-44f9-beb2-e381c375058f"
```

So erstellen Sie eine dynamische Gruppe

```powershell
ConvertStaticGroupToDynamic "a58913b2-eee4-44f9-beb2-e381c375058f" "user.displayName -startsWith ""Peter"""
```

## <a name="next-steps"></a>Nächste Schritte

Diese Artikel enthalten zusätzliche Informationen zu Gruppen in Azure Active Directory.

* [Anzeigen vorhandener Gruppen](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Erstellen einer neuen Gruppe und Hinzufügen von Mitgliedern](../fundamentals/active-directory-groups-create-azure-portal.md)
* [Verwalten der Einstellungen einer Gruppe](../fundamentals/active-directory-groups-settings-azure-portal.md)
* [Verwalten der Mitgliedschaften einer Gruppe](../fundamentals/active-directory-groups-membership-azure-portal.md)
* [Verwalten dynamischer Regeln für Benutzer in einer Gruppe](groups-dynamic-membership.md)
