---
title: 'Azure Active Directory Domain Services: Bereichsbezogene Synchronisierung | Microsoft-Dokumentation'
description: Konfigurieren der bereichsbezogenen Synchronisierung von Azure AD mit Ihren verwalteten Domänen
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: 332c0bc43a269734e0dc4db37228006a78e460e2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245419"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-your-managed-domain"></a>Konfigurieren der bereichsbezogenen Synchronisierung von Azure AD mit Ihrer verwalteten Domäne
In diesem Artikel erfahren Sie, wie Sie konfigurieren können, dass nur bestimmte Benutzerkonten aus Ihrem Azure AD-Verzeichnis mit der von Azure AD Domain Services verwalteten Domäne synchronisiert werden.


## <a name="group-based-scoped-synchronization"></a>Gruppenbasierte bereichsbezogene Synchronisierung
Standardmäßig werden alle Benutzer und Gruppen in Ihrem Azure AD-Verzeichnis mit Ihrer verwalteten Domäne synchronisiert. Wenn nur wenige Benutzer die verwaltete Domäne verwenden, könnten Sie nur diese Benutzerkonten synchronisieren. Dies erreichen Sie mit der gruppenbasierten bereichsbezogenen Synchronisierung. Wenn sie konfiguriert ist, werden nur Benutzerkonten, die zu den von Ihnen angegebenen Gruppen gehören, mit der verwalteten Domäne synchronisiert.

Mit der folgende Tabelle können Sie bestimmen, wie die bereichsbezogene Synchronisierung verwendet wird:

| **Aktueller Zustand** | **Gewünschter Zustand** | **Erforderliche Konfiguration** |
| --- | --- | --- |
| Ihre vorhandene verwaltete Domäne ist zum Synchronisieren aller Benutzerkonten und Gruppen konfiguriert. | Sie möchten nur Benutzerkonten, die zu bestimmten Gruppen gehören, mit Ihrer verwalteten Domäne synchronisieren. | [Löschen Sie die vorhandene verwaltete Domäne](delete-aadds.md). Befolgen Sie dann die Anweisungen in diesem Artikel, um sie mit konfigurierter bereichsbezogener Synchronisierung neu zu erstellen. |
| Sie verfügen nicht über eine vorhandene verwaltete Domäne. | Sie möchten eine neue verwaltete Domäne erstellen und nur Benutzerkonten synchronisieren, die zu bestimmten Gruppen gehören. | Befolgen Sie die Anweisungen in diesem Artikel, um eine neue verwaltete Domäne mit konfigurierter bereichsbezogener Synchronisierung zu erstellen. |
| Ihre vorhandene verwaltete Domäne ist zum Synchronisieren nur der Konten konfiguriert, die zu bestimmten Gruppen gehören. | Sie möchten die Liste der Gruppen ändern, deren Benutzer mit der verwalteten Domäne synchronisiert werden sollen. | Befolgen Sie die Anweisungen in diesem Artikel, um die bereichsbezogene Synchronisierung zu ändern. |

> [!WARNING]
> **Durch das Ändern des Bereichs der Synchronisierung durchläuft Ihre verwaltete Domäne eine Neusynchronisierung.**
> 
>  * Wenn Sie den Synchronisierungsbereich für eine verwaltete Domäne ändern, erfolgt eine vollständige Neusynchronisierung.
>  * Objekte, die für die verwaltete Domäne nicht mehr erforderlich sind, werden gelöscht. Neue Objekte werden in der verwalteten Domäne erstellt.
>  * Die Neusynchronisierung kann sehr lange dauern. Dies ist abhängig von der Anzahl von Objekten (Benutzer, Gruppen und Gruppenmitgliedschaften) in Ihrer verwalteten Domäne und im Azure AD-Verzeichnis. Bei großen Verzeichnissen mit Hunderten oder Tausenden von Objekten kann die Neusynchronisierung einige Tage dauern.


## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-azure-portal"></a>Erstellen einer neuen verwalteten Domäne und Aktivieren der gruppenbasierten bereichsbezogenen Synchronisierung über das Azure-Portal

1. Gehen Sie wie im [Leitfaden zu den ersten Schritten](create-instance.md) beschrieben vor, um eine verwaltete Domäne zu erstellen.
2. Wählen Sie im Erstellungs-Assistenten von Azure AD Domain Services den **bereichsbezogenen** Synchronisierungstyp aus.

## <a name="create-a-new-managed-domain-and-enable-group-based-scoped-synchronization-using-powershell"></a>Erstellen einer neuen verwalteten Domäne und Aktivieren der gruppenbasierten bereichsbezogenen Synchronisierung mithilfe von PowerShell
Verwenden Sie PowerShell, um diese Schritte auszuführen. Lesen Sie die Anweisungen zum [Aktivieren von Azure Active Directory Domain Services mithilfe von PowerShell](powershell-create-instance.md). Einige Schritte in diesem Artikel wurden leicht geändert, um die bereichsbezogene Synchronisierung zu konfigurieren.

Führen Sie die folgenden Schritte aus, um die gruppenbasierte bereichsbezogene Synchronisierung mit der verwalteten Domäne zu konfigurieren:

1. Führen Sie die folgenden Aufgaben durch:
   * [Aufgabe 1: Installieren der erforderlichen PowerShell-Module](powershell-create-instance.md#task-1-install-the-required-powershell-modules)
   * [Aufgabe 2: Erstellen des erforderlichen Dienstprinzipals in Ihrem Azure AD-Verzeichnis](powershell-create-instance.md#task-2-create-the-required-service-principal-in-your-azure-ad-directory)
   * [Aufgabe 3: Erstellen und Konfigurieren der Gruppe „AAD DC Administrators"]powershell-create-instance.md#task-3-create-and-configure-the-aad-dc-administrators-group).
   * [Aufgabe 4: Registrieren des Ressourcenanbieters für Azure AD Domain Services](powershell-create-instance.md#task-4-register-the-azure-ad-domain-services-resource-provider)
   * [Aufgabe 5: Erstellen einer Ressourcengruppe](powershell-create-instance.md#task-5-create-a-resource-group)
   * [Aufgabe 6: Erstellen und Konfigurieren des virtuellen Netzwerks](powershell-create-instance.md#task-6-create-and-configure-the-virtual-network)

2. Wählen Sie die Gruppen aus, die Sie synchronisieren möchten, und geben Sie den Anzeigenamen der Gruppen an, die Sie mit Ihrer verwalteten Domäne synchronisieren möchten.

3. Speichern Sie das [Skript im folgenden Abschnitt](scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1) in einer Datei namens ```Select-GroupsToSync.ps1```. Führen Sie das folgende Skript aus:

   ```powershell
   .\Select-GroupsToSync.ps1 -groupsToAdd @("AAD DC Administrators", "GroupName1", "GroupName2")
   ```

   > [!WARNING]
   > **Vergessen Sie nicht, die Gruppe „AAD DC-Administratoren“ einzubeziehen.**
   >
   > Sie müssen die Gruppe „AAD DC-Administratoren“ in die Liste der Gruppen aufnehmen, die für die bereichsbezogene Synchronisierung konfiguriert werden. Wenn Sie diese Gruppe nicht einbeziehen, kann die verwaltete Domäne nicht verwendet werden.
   >

4. Erstellen Sie jetzt die verwaltete Domäne, und aktivieren Sie die gruppenbasierte bereichsbezogene Synchronisierung für die verwaltete Domäne. Schließen Sie die Eigenschaft ```"filteredSync" = "Enabled"``` in den Parameter ```Properties``` ein. Sehen Sie sich beispielsweise das folgende Skriptfragment an, das aus [Aufgabe 7: Bereitstellen der durch Azure AD Domain Services verwalteten Domäne](powershell-create-instance.md#task-7-provision-the-azure-ad-domain-services-managed-domain) kopiert wurde.

   ```powershell
   $AzureSubscriptionId = "YOUR_AZURE_SUBSCRIPTION_ID"
   $ManagedDomainName = "contoso100.com"
   $ResourceGroupName = "ContosoAaddsRg"
   $VnetName = "DomainServicesVNet_WUS"
   $AzureLocation = "westus"

   # Enable Azure AD Domain Services for the directory.
   New-AzResource -ResourceId "/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.AAD/DomainServices/$ManagedDomainName" `
   -Location $AzureLocation `
   -Properties @{"DomainName"=$ManagedDomainName; "filteredSync" = "Enabled"; `
    "SubnetId"="/subscriptions/$AzureSubscriptionId/resourceGroups/$ResourceGroupName/providers/Microsoft.Network/virtualNetworks/$VnetName/subnets/DomainServices"} `
   -ApiVersion 2017-06-01 -Force -Verbose
   ```

   > [!TIP]
   > Vergessen Sie nicht, ```"filteredSync" = "Enabled"``` in den Parameter ```-Properties``` einzubinden, damit die bereichsbezogene Synchronisierung für die verwaltete Domäne aktiviert wird.


## <a name="script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1"></a>Skript zum Auswählen von Gruppen für die Synchronisierung mit der verwalteten Domäne (Select-GroupsToSync.ps1)
Speichern Sie das folgende Skript in einer Datei (```Select-GroupsToSync.ps1```). Dieses Skript konfiguriert Azure AD Domain Services so, dass die ausgewählten Gruppen mit der verwalteten Domäne synchronisiert werden. Alle Benutzerkonten, die zu den angegebenen Gruppen gehören, werden mit der verwalteten Domäne synchronisiert.

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


## <a name="modify-group-based-scoped-synchronization"></a>Ändern der gruppenbasierten bereichsbezogenen Synchronisierung
Um die Liste der Gruppen zu ändern, deren Benutzer mit Ihrer verwalteten Domäne synchronisiert werden sollen, führen Sie erneut das [PowerShell-Skript](scoped-synchronization.md#script-to-select-groups-to-synchronize-to-the-managed-domain-select-groupstosyncps1) aus, und geben Sie die neue Liste von Gruppen an. Denken Sie daran, immer die Gruppe „AAD DC-Administratoren“ in dieser Liste anzugeben.

> [!WARNING]
> **Vergessen Sie nicht, die Gruppe „AAD DC-Administratoren“ einzubeziehen.**
>
> Sie müssen die Gruppe „AAD DC-Administratoren“ in die Liste der Gruppen aufnehmen, die für die bereichsbezogene Synchronisierung konfiguriert werden. Wenn Sie diese Gruppe nicht einbeziehen, kann die verwaltete Domäne nicht verwendet werden.
>


## <a name="disable-group-based-scoped-synchronization"></a>Deaktivieren der gruppenbasierten bereichsbezogenen Synchronisierung
Verwenden Sie das folgende PowerShell-Skript, um die gruppenbasierte bereichsbezogene Synchronisierung für Ihre verwaltete Domäne zu deaktivieren:

```powershell
// Login to your Azure AD tenant
Login-AzAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// Disable group-based scoped synchronization.
$disableScopedSync = @{"filteredSync" = "Disabled"}

Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $disableScopedSync
```

## <a name="next-steps"></a>Nächste Schritte
* [Grundlegendes zur Synchronisierung in Azure AD Domain Services](synchronization.md)
* [Aktivieren von Azure Active Directory Domain Services mithilfe von PowerShell](powershell-create-instance.md)
