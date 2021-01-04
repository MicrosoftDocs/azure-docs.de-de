---
title: Hinzufügen, Entfernen und Auflisten von Gruppen in einer Verwaltungseinheit – Azure Active Directory | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie Gruppen und deren Rollenberechtigungen in einer Verwaltungseinheit in Azure Active Directory verwalten.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 092604429cf5a527d7ee62b412e879ad9991eace
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394746"
---
# <a name="add-and-manage-groups-in-an-administrative-unit-in-azure-active-directory"></a>Hinzufügen und Verwalten von Gruppen in einer Verwaltungseinheit in Azure Active Directory

In Azure Active Directory (Azure AD) können Sie einer Verwaltungseinheit Gruppen hinzufügen, um eine präzisere Steuerung der Verwaltungsbereiche zu erzielen.

Informationen zur Vorbereitung auf den Einsatz von PowerShell und Microsoft Graph für die Verwaltung von Verwaltungseinheiten finden Sie unter [Erste Schritte](admin-units-manage.md#get-started).

## <a name="add-groups-to-an-administrative-unit"></a>Zuweisen von Gruppen zu einer Verwaltungseinheit

Sie können Gruppen einer Verwaltungseinheit hinzufügen, indem Sie das Azure-Portal, PowerShell oder Microsoft Graph verwenden.

### <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

Sie können Gruppen einer Verwaltungseinheit nur einzeln zuweisen. Eine Option zum Zuweisen von Gruppen per Massenvorgang ist nicht vorhanden. Es gibt zwei Möglichkeiten, wie Sie im Azure-Portal eine Gruppe einer Verwaltungseinheit zuweisen können:

* Im Bereich **Gruppen** :

  1. Navigieren Sie im Azure-Portal zu **Azure AD**.
  1. Wählen Sie die Option **Gruppen** und dann die Gruppe aus, die Sie der Verwaltungseinheit zuweisen möchten. 
  1. Wählen Sie im linken Bereich die Option **Verwaltungseinheiten** aus, um eine Liste mit den Verwaltungseinheiten anzuzeigen, denen die Gruppe zugewiesen ist. 

     ![Screenshot: Link „Der Verwaltungseinheit zuweisen“ im Bereich „Verwaltungseinheiten“](./media/admin-units-add-manage-groups/assign-to-group-1.png)

  1. Wählen Sie die Option **Der Verwaltungseinheit zuweisen** aus.
  1. Wählen Sie im rechten Bereich die Verwaltungseinheit aus.

* Im Bereich **Verwaltungseinheiten** > **Alle Gruppen** :

  1. Navigieren Sie im Azure-Portal zu **Azure AD**.
  
  1. Wählen Sie im linken Bereich die Option **Verwaltungseinheiten** und dann **Alle Gruppen** aus. 
     Alle Gruppen, die der Verwaltungseinheit bereits zugewiesen sind, werden im rechten Bereich angezeigt. 

  1. Wählen Sie im Bereich **Gruppen** die Option **Hinzufügen** aus.
    Im rechten Bereich sind alle verfügbaren Gruppen Ihrer Azure AD-Organisation aufgeführt. 

     ![Screenshot: Schaltfläche „Hinzufügen“ zum Hinzufügen einer Gruppe zu einer Verwaltungseinheit](./media/admin-units-add-manage-groups/assign-to-admin-unit.png)

  1. Wählen Sie mindestens eine Gruppe aus, die der Verwaltungseinheit zugewiesen werden soll, und wählen Sie dann die Schaltfläche **Auswählen** aus.

### <a name="use-powershell"></a>Verwenden von PowerShell

Verwenden Sie im folgenden Beispiel das Cmdlet `Add-AzureADMSAdministrativeUnitMember`, um die Gruppe der Verwaltungseinheit hinzuzufügen. Die Objekt-ID der Verwaltungseinheit und die Objekt-ID der hinzuzufügenden Gruppe werden als Argumente verwendet. Ändern Sie den hervorgehobenen Abschnitt so, wie dies für Ihre jeweilige Umgebung erforderlich ist.


```powershell
$administrative unitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
Add-AzureADMSAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId -RefObjectId $GroupObj.ObjectId
```

### <a name="use-microsoft-graph"></a>Verwenden von Microsoft Graph

Führen Sie die folgenden Befehle aus:

```http
Http request
POST /administrativeUnits/{Admin Unit id}/members/$ref

Request body
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/{id}"
}
```

Beispiel:

```http
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/ 871d21ab-6b4e-4d56-b257-ba27827628f3"
}
```

## <a name="view-a-list-of-groups-in-an-administrative-unit"></a>Anzeigen einer Liste mit Gruppen in einer Verwaltungseinheit

### <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

1. Navigieren Sie im Azure-Portal zu **Azure AD**.

1. Wählen Sie im linken Bereich die Option **Verwaltungseinheiten** und dann die Verwaltungseinheit aus, deren Gruppen Sie anzeigen möchten. Standardmäßig ist im linken Bereich die Option **Alle Benutzer** ausgewählt. 

1. Wählen Sie im linken Bereich die Option **Gruppen** aus. Im rechten Bereich wird eine Liste mit den Gruppen angezeigt, die Mitglieder der ausgewählten Verwaltungseinheit sind.

   ![Screenshot: Bereich „Gruppen“ mit einer Liste der Gruppen in einer Verwaltungseinheit](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>Verwenden von PowerShell

Führen Sie den folgenden Befehl aus, um eine Liste mit allen Mitgliedern der Verwaltungseinheit anzuzeigen: 

```powershell
$administrative unitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
Get-AzureADMSAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId
```

Verwenden Sie den folgenden Codeausschnitt, um alle Gruppen anzuzeigen, die Mitglieder der Verwaltungseinheit sind:

```http
foreach ($member in (Get-AzureADMSAdministrativeUnitMember -ObjectId $administrative unitObj.ObjectId)) 
{
if($member.ObjectType -eq "Group")
{
Get-AzureADGroup -ObjectId $member.ObjectId
}
}
```

### <a name="use-microsoft-graph"></a>Verwenden von Microsoft Graph

Führen Sie den folgenden Befehl aus:

```http
HTTP request
GET /directory/administrativeUnits/{Admin id}/members/$/microsoft.graph.group
Request body
{}
```

## <a name="view-a-list-of-administrative-units-for-a-group"></a>Anzeigen einer Liste mit Verwaltungseinheiten für eine Gruppe

### <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

1. Navigieren Sie im Azure-Portal zu **Azure AD**.

1. Wählen Sie im linken Bereich die Option **Gruppen** aus, um eine Liste mit Gruppen anzuzeigen.

1. Wählen Sie eine Gruppe aus, um das Gruppenprofil zu öffnen. 

1. Wählen Sie im linken Bereich die Option **Verwaltungseinheiten** aus, um alle Verwaltungseinheiten aufzulisten, deren Mitglied die Gruppe ist.

   ![Screenshot: Bereich „Verwaltungseinheiten“ mit einer Liste mit Verwaltungseinheiten, denen eine Gruppe zugewiesen ist](./media/admin-units-add-manage-groups/list-group-au.png)

### <a name="use-powershell"></a>Verwenden von PowerShell

Führen Sie den folgenden Befehl aus:

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }
```

### <a name="use-microsoft-graph"></a>Verwenden von Microsoft Graph

Führen Sie den folgenden Befehl aus:

```http
https://graph.microsoft.com/v1.0/groups/<group-id>/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-group-from-an-administrative-unit"></a>Entfernen einer Gruppe aus einer Verwaltungseinheit

### <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

Im Azure-Portal gibt es zwei Möglichkeiten, wie Sie eine Gruppe aus einer Verwaltungseinheit entfernen können:

- Entfernen aus einer Gruppenübersicht:

  1. Navigieren Sie im Azure-Portal zu **Azure AD**.
  1. Wählen Sie im linken Bereich die Option **Gruppen** aus, und öffnen Sie dann das Profil für die Gruppe, die Sie aus einer Verwaltungseinheit entfernen möchten.
  1. Wählen Sie im linken Bereich die Option **Verwaltungseinheiten** aus, um alle Verwaltungseinheiten aufzulisten, denen die Gruppe zugewiesen ist. 
  1. Wählen Sie die Verwaltungseinheit aus, aus der Sie die Gruppe entfernen möchten, und wählen Sie dann **Aus Verwaltungseinheit entfernen** aus.

     ![Screenshot: Bereich „Verwaltungseinheiten“ mit einer Liste mit Gruppen, die der ausgewählten Verwaltungseinheit zugewiesen sind](./media/admin-units-add-manage-groups/group-au-remove.png)

- Entfernen aus einer Verwaltungseinheit:

  1. Navigieren Sie im Azure-Portal zu **Azure AD**.
  1. Wählen Sie im linken Bereich die Option **Verwaltungseinheiten** und dann die Verwaltungseinheit aus, der die Gruppe zugewiesen ist.
  1. Wählen Sie im linken Bereich die Option **Gruppen** aus, um alle Gruppen aufzulisten, die der Verwaltungseinheit zugewiesen sind.
  1. Wählen Sie die zu entfernende Gruppe und dann die Option **Gruppen entfernen** aus.

    ![Screenshot: Bereich „Gruppen“ mit einer Liste der Gruppen in einer Verwaltungseinheit](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="use-powershell"></a>Verwenden von PowerShell

Führen Sie den folgenden Befehl aus:

```powershell
Remove-AzureADMSAdministrativeUnitMember -ObjectId $auId -MemberId $memberGroupObjId
```

### <a name="use-microsoft-graph"></a>Verwenden von Microsoft Graph

Führen Sie den folgenden Befehl aus:

```http
https://graph.microsoft.com/v1.0/directory/AdministrativeUnits/<adminunit-id>/members/<group-id>/$ref
```

## <a name="next-steps"></a>Nächste Schritte

- [Zuweisen einer Rolle zu einer Verwaltungseinheit](admin-units-assign-roles.md)
- [Verwalten von Benutzern in einer Verwaltungseinheit](admin-units-add-manage-users.md)
