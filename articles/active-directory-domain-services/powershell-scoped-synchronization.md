---
title: Bereichsbezogene Synchronisierung für Azure AD Domain Services mithilfe von PowerShell | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie mithilfe von PowerShell in Azure AD die bereichsbezogene Synchronisierung von Azure AD für eine verwaltete Azure Active Directory Domain Services-Domäne konfigurieren.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/24/2020
ms.author: justinha
ms.openlocfilehash: c078117baf84d7dbfaaaa2b569abb8a5f5c67e6d
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619010"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services-using-azure-ad-powershell"></a>Konfigurieren der bereichsbezogenen Synchronisierung von Azure AD für Azure Active Directory Domain Services mithilfe von PowerShell in Azure AD

Zum Bereitstellen von Authentifizierungsdiensten synchronisiert Azure Active Directory Domain Services (Azure AD DS) Benutzer und Gruppen von Azure AD. In einer Hybridumgebung können Benutzer und Gruppen aus einer lokalen AD DS-Umgebung (Active Directory Domain Services) zuerst mithilfe von Azure AD Connect mit Azure AD und anschließend mit Azure AD DS synchronisiert werden.

Standardmäßig werden alle Benutzer und Gruppen aus einem Azure AD-Verzeichnis mit einer verwalteten Azure AD DS-Domäne synchronisiert. Bei speziellen Anforderungen können Sie stattdessen festlegen, dass nur eine definierte Gruppe von Benutzern synchronisiert werden soll.

In diesem Artikel erfahren Sie, wie Sie mithilfe von PowerShell in Azure AD eine verwaltete Domäne mit einer bereichsbezogenen Synchronisierung erstellen und anschließend die Gruppe der Benutzer für diesen Bereich ändern oder deaktivieren. Sie können [diese Schritte auch über das Azure-Portal ausführen][scoped-sync].

## <a name="before-you-begin"></a>Voraussetzungen

Für diesen Artikel benötigen Sie die folgenden Ressourcen und Berechtigungen:

* Ein aktives Azure-Abonnement.
    * Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Einen mit Ihrem Abonnement verknüpften Azure Active Directory-Mandanten, der entweder mit einem lokalen Verzeichnis synchronisiert oder ein reines Cloudverzeichnis ist.
    * [Erstellen Sie einen Azure Active Directory-Mandanten][create-azure-ad-tenant], oder [verknüpfen Sie ein Azure-Abonnement mit Ihrem Konto][associate-azure-ad-tenant], sofern erforderlich.
* Eine verwaltete Azure Active Directory Domain Services-Domäne, die in Ihrem Azure AD-Mandanten aktiviert und konfiguriert ist.
    * Führen Sie bei Bedarf das Tutorial zum [Erstellen und Konfigurieren einer verwalteten Azure Active Directory Domain Services-Domäne][tutorial-create-instance] aus.
* Sie benötigen die Berechtigungen eines *globalen Administrators* in Ihrem Azure AD-Mandanten, um den AD DS-Synchronisierungsbereich ändern zu können.

## <a name="scoped-synchronization-overview"></a>Übersicht über die bereichsbezogene Synchronisierung

Standardmäßig werden alle Benutzer und Gruppen aus einem Azure AD-Verzeichnis in einer verwalteten Domäne synchronisiert. Wenn nur wenige Benutzer auf die verwaltete Domäne zugreifen müssen, können Sie nur diese Benutzerkonten synchronisieren. Diese bereichsbezogene Synchronisierung ist gruppenbasiert. Wenn Sie eine gruppenbasierte bereichsbezogene Synchronisierung konfigurieren, werden nur die Benutzerkonten, die zu den angegebenen Gruppen gehören, mit der verwalteten Domäne synchronisiert. Geschachtelte Gruppen werden nicht synchronisiert, nur die von Ihnen ausgewählten.

Sie können den Synchronisierungsbereich ändern, wenn Sie die verwaltete Domäne erstellen, oder nach erfolgter Bereitstellung. Sie können nun auch den Synchronisierungsbereich für eine vorhandene verwaltete Domäne ändern, ohne dass Sie sie dafür neu erstellen müssen.

Weitere Informationen zum Synchronisierungsvorgang finden Sie unter [Grundlegendes zur Synchronisierung in Azure AD Domain Services.][concepts-sync]

> [!WARNING]
> Wenn Sie den Bereich der Synchronisierung ändern, werden alle Daten in der verwalteten Domäne neu synchronisiert. Es gelten die folgenden Bedingungen:
>
>  * Wenn Sie den Synchronisierungsbereich für eine verwaltete Domäne ändern, erfolgt eine vollständige Neusynchronisierung.
>  * Objekte, die in der verwalteten Domäne nicht mehr erforderlich sind, werden gelöscht. Neue Objekte werden in der verwalteten Domäne erstellt.

## <a name="powershell-script-for-scoped-synchronization"></a>PowerShell-Skript für die bereichsbezogene Synchronisierung

Zum Konfigurieren der bereichsbezogenen Synchronisierung mithilfe von PowerShell müssen Sie zuerst das folgende Skript in einer Datei namens `Select-GroupsToSync.ps1` speichern.

Mit diesem Skript wird Azure AD DS für die Synchronisierung ausgewählter Gruppen von Azure AD konfiguriert. Alle Benutzerkonten, die zu den angegebenen Gruppen gehören, werden mit der verwalteten Domäne synchronisiert.

Dieses Skript wird in den weiteren Schritten in diesem Artikel verwendet.

```powershell
param (
    [Parameter(Position = 0)]
    [String[]]$groupsToAdd
)

Connect-AzureAD
$sp = Get-AzureADServicePrincipal -Filter "AppId eq '2565bd9d-da50-47d4-8b85-4c97f669dc36'"
$role = $sp.AppRoles | where-object -FilterScript {$_.DisplayName -eq "User"}

Write-Output "`n****************************************************************************"

Write-Output "Total group-assignments need to be added: $($groupsToAdd.Count)"
$newGroupIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($groupName in $groupsToAdd)
{
    try
    {
        $group = Get-AzureADGroup -Filter "DisplayName eq '$groupName'"
        $newGroupIds.Add($group.ObjectId)

        Write-Output "Group-Name: $groupName, Id: $($group.ObjectId)"
    }
    catch
    {
        Write-Error "Failed to find group: $groupName. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

$currentAssignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId
Write-Output "Total current group-assignments: $($currentAssignments.Count), SP-ObjectId: $($sp.ObjectId)"

$currAssignedObjectIds = New-Object 'System.Collections.Generic.HashSet[string]'
foreach ($assignment in $currentAssignments)
{
    Write-Output "Assignment-ObjectId: $($assignment.PrincipalId)"

    if ($newGroupIds.Contains($assignment.PrincipalId) -eq $false)
    {
        Write-Output "This assignment is not needed anymore. Removing it! Assignment-ObjectId: $($assignment.PrincipalId)"
        Remove-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -AppRoleAssignmentId $assignment.ObjectId
    }
    else
    {
        $currAssignedObjectIds.Add($assignment.PrincipalId)
    }
}

Write-Output "****************************************************************************`n"
Write-Output "`n****************************************************************************"

foreach ($id in $newGroupIds)
{
    try
    {
        if ($currAssignedObjectIds.Contains($id) -eq $false)
        {
            Write-Output "Adding new group-assignment. Role-Id: $($role.Id), Group-Object-Id: $id, ResourceId: $($sp.ObjectId)"
            New-AzureADGroupAppRoleAssignment -Id $role.Id -ObjectId $id -PrincipalId $id -ResourceId $sp.ObjectId
        }
        else
        {
            Write-Output "Group-ObjectId: $id is already assigned."
        }
    }
    catch
    {
        Write-Error "Exception occurred assigning Object-ID: $id. Exception: $($_.Exception)."
    }
}

Write-Output "****************************************************************************`n"
```

## <a name="enable-scoped-synchronization"></a>Aktivieren der bereichsbezogenen Synchronisierung

Führen Sie die folgenden Schritte aus, um die gruppenbasierte bereichsbezogene Synchronisierung für eine verwaltete Domäne zu aktivieren:

1. Legen Sie zuerst *"filteredSync" = "Enabled"* für die AD DS-Ressource fest, und aktualisieren Sie dann die verwaltete Domäne.

    Geben Sie bei Aufforderung die Anmeldeinformationen für einen *globalen Administrator* an, um sich bei Ihrem Azure AD-Mandanten mithilfe des Cmdlet [Connect-AzureAD][Connect-AzureAD] anzumelden:

    ```powershell
    // Connect to your Azure AD tenant
    Connect-AzureAD

    // Retrieve the Azure AD DS resource.
    $DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

    // Enable group-based scoped synchronization.
    $enableScopedSync = @{"filteredSync" = "Enabled"}

    // Update the Azure AD DS resource
    Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $enableScopedSync
    ```

1. Geben Sie nun die Liste der Gruppen an, deren Benutzer mit der verwalteten Domäne synchronisiert werden sollen.

    Führen Sie das `Select-GroupsToSync.ps1`-Skript aus, und geben Sie die Liste der zu synchronisierenden Gruppen an. Im folgenden Beispiel sind die zu synchronisierenden Gruppen *GroupName1* und *GroupName2*.

    > [!WARNING]
    > Sie müssen die Gruppe *AAD DC-Administratoren* in die Liste der Gruppen für die bereichsbezogene Synchronisierung aufnehmen. Wenn Sie diese Gruppe nicht einbeziehen, kann die verwaltete Domäne nicht verwendet werden.

    ```powershell
    .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
    ```

Wenn Sie den Bereich der Synchronisierung ändern, werden alle Daten in der verwalteten Domäne neu synchronisiert. Objekte, die in der verwalteten Domäne nicht mehr erforderlich sind, werden gelöscht, und die erneute Synchronisierung kann lange Zeit in Anspruch nehmen.

## <a name="modify-scoped-synchronization"></a>Ändern der bereichsbezogenen Synchronisierung

Führen Sie das `Select-GroupsToSync.ps1`-Skript aus, und geben Sie die neue Liste von Gruppen für die Synchronisierung an, um die Liste der Gruppen zu ändern, deren Benutzer mit der verwalteten Domäne synchronisiert werden sollen.

Im folgenden Beispiel enthält die Liste der zu synchronisierenden Gruppen nicht mehr *GroupName2*, dafür aber *GroupName3*.

> [!WARNING]
> Sie müssen die Gruppe *AAD DC-Administratoren* in die Liste der Gruppen für die bereichsbezogene Synchronisierung aufnehmen. Wenn Sie diese Gruppe nicht einbeziehen, kann die verwaltete Domäne nicht verwendet werden.

Geben Sie bei Aufforderung die Anmeldeinformationen für einen *globalen Administrator* an, um sich bei Ihrem Azure AD-Mandanten mithilfe des Cmdlet [Connect-AzureAD][Connect-AzureAD] anzumelden:

```powershell
.\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName3")
```

Wenn Sie den Bereich der Synchronisierung ändern, werden alle Daten in der verwalteten Domäne neu synchronisiert. Objekte, die in der verwalteten Domäne nicht mehr erforderlich sind, werden gelöscht, und die erneute Synchronisierung kann lange Zeit in Anspruch nehmen.

## <a name="disable-scoped-synchronization"></a>Deaktivieren der bereichsbezogenen Synchronisierung

Legen Sie zum Deaktivieren der gruppenbasierten bereichsbezogenen Synchronisierung für eine verwaltete Domäne *"filteredSync" = "Disabled"* für die Azure AD DS-Ressource fest, und aktualisieren Sie dann die verwaltete Domäne. Nach Abschluss des Vorgangs ist für alle Benutzer und Gruppen die Synchronisierung von Azure AD festgelegt.

Geben Sie bei Aufforderung die Anmeldeinformationen für einen *globalen Administrator* an, um sich bei Ihrem Azure AD-Mandanten mithilfe des Cmdlet [Connect-AzureAD][Connect-AzureAD] anzumelden:

```powershell
// Connect to your Azure AD tenant
Connect-AzureAD

// Retrieve the Azure AD DS resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

// Update the Azure AD DS resource
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

Wenn Sie den Bereich der Synchronisierung ändern, werden alle Daten in der verwalteten Domäne neu synchronisiert. Objekte, die in der verwalteten Domäne nicht mehr erforderlich sind, werden gelöscht, und die erneute Synchronisierung kann lange Zeit in Anspruch nehmen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Synchronisierungsvorgang finden Sie unter [Grundlegendes zur Synchronisierung in Azure AD Domain Services.](synchronization.md)

<!-- INTERNAL LINKS -->
[scoped-sync]: scoped-synchronization.md
[concepts-sync]: synchronization.md
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md

<!-- EXTERNAL LINKS -->
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
