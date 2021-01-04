---
title: Hinzufügen, Entfernen und Auflisten von Benutzern in einer Verwaltungseinheit – Azure Active Directory | Microsoft-Dokumentation
description: Verwalten von Benutzern und deren Rollenberechtigungen in einer Verwaltungseinheit in Azure Active Directory
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
ms.openlocfilehash: b490e03ea7ac0a3bc780fa731629217126b6828a
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395528"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Hinzufügen und Verwalten von Benutzern in einer Verwaltungseinheit in Azure Active Directory

In Azure Active Directory (Azure AD) können Sie Benutzer einer Verwaltungseinheit hinzufügen, um eine präzisere Steuerung der Verwaltungsbereiche zu erzielen.

Informationen zur Vorbereitung auf den Einsatz von PowerShell und Microsoft Graph für die Verwaltung von Verwaltungseinheiten finden Sie unter [Erste Schritte](admin-units-manage.md#get-started).

## <a name="add-users-to-an-administrative-unit"></a>Hinzufügen von Benutzern zu einer Verwaltungseinheit

### <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

Sie können Benutzer Verwaltungseinheiten einzeln oder per Massenvorgang zuweisen.

- Zuweisen einzelner Benutzer aus einem Benutzerprofil:

   1. Melden Sie sich beim [Azure AD Admin Center](https://portal.azure.com) mit Berechtigungen „Administrator für privilegierte Rollen“ an.

   1. Wählen Sie **Benutzer** und dann zum Öffnen des Benutzerprofils den Benutzer aus, der einer Verwaltungseinheit zugewiesen werden soll.
   
   1. Wählen Sie **Verwaltungseinheiten** aus. 
   
   1. Gehen Sie wie folgt vor, um den Benutzer mindestens einer Verwaltungseinheit zuzuweisen: Wählen Sie die Option **Der Verwaltungseinheit zuweisen** und dann im rechten Bereich die Verwaltungseinheiten aus, denen Sie den Benutzer zuweisen möchten.

       ![Screenshot: Bereich „Verwaltungseinheiten“ zum Zuweisen eines Benutzers zu einer Verwaltungseinheit](./media/admin-units-add-manage-users/assign-users-individually.png)

- Zuweisen einzelner Benutzer aus einer Verwaltungseinheit:

   1. Melden Sie sich beim [Azure AD Admin Center](https://portal.azure.com) mit Berechtigungen „Administrator für privilegierte Rollen“ an.
   1. Wählen Sie **Verwaltungseinheiten** und dann die Verwaltungseinheit aus, für die der Benutzer zugewiesen werden sollen.
   1. Wählen Sie **Alle Benutzer** , **Mitglied hinzufügen** und dann im Bereich **Mitglied hinzufügen** einen oder mehrere Benutzer aus, die Sie der Verwaltungseinheit zuweisen möchten.

        ![Screenshot: Bereich „Benutzer“ der Verwaltungseinheit zum Zuweisen eines Benutzers zu einer Verwaltungseinheit](./media/admin-units-add-manage-users/assign-to-admin-unit.png)

- Zuweisen von Benutzern per Massenvorgang:

   1. Melden Sie sich beim [Azure AD Admin Center](https://portal.azure.com) mit Berechtigungen „Administrator für privilegierte Rollen“ an.

   1. Wählen Sie **Verwaltungseinheiten** aus.

   1. Wählen Sie die Verwaltungseinheit aus, der Sie Benutzer hinzufügen möchten.

   1. Wählen Sie **Users** > **Bulk activities** > **Bulk add members** (Benutzer > Massenaktivitäten > Massenhinzufügen von Mitgliedern) aus. Anschließend können Sie die CSV-Vorlage herunterladen und die Datei bearbeiten. Das Format ist einfach. In jeder Zeile muss jeweils ein Benutzerprinzipalname hinzugefügt werden. Wenn die Datei bereit ist, speichern Sie sie an einem geeigneten Speicherort und laden sie dann im Rahmen dieses Schritts hoch.

      ![Screenshot: Bereich „Benutzer“ zum Zuweisen von Benutzern zu einer Verwaltungseinheit per Massenvorgang](./media/admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="use-powershell"></a>Verwenden von PowerShell

Verwenden Sie in PowerShell das Cmdlet `Add-AzureADAdministrativeUnitMember` aus dem folgenden Beispiel, um den Benutzer der Verwaltungseinheit hinzuzufügen. Die Objekt-ID der Verwaltungseinheit, der Sie den Benutzer hinzufügen möchten, und die Objekt-ID des hinzuzufügenden Benutzers werden als Argumente verwendet. Ändern Sie den hervorgehobenen Abschnitt so, wie dies für Ihre jeweilige Umgebung erforderlich ist.

```powershell
$administrativeunitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
Add-AzureADMSAdministrativeUnitMember -Id $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId
```


### <a name="use-microsoft-graph"></a>Verwenden von Microsoft Graph

Ersetzen Sie den Platzhalter durch Testinformationen, und führen Sie den folgenden Befehl aus:

```http
Http request
POST /administrativeUnits/{Admin Unit id}/members/$ref
Request body
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/{id}"
}
```

Beispiel:

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/johndoe@fabidentity.com"
}
```

## <a name="view-a-list-of-administrative-units-for-a-user"></a>Anzeigen einer Liste mit Verwaltungseinheiten für einen Benutzer

### <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

Im Azure-Portal können Sie das Profil eines Benutzers öffnen, indem Sie wie folgt vorgehen:

1. Navigieren Sie zu **Azure AD** , und wählen Sie **Benutzer** aus.

1. Wählen Sie den Benutzer aus, dessen Profil Sie anzeigen möchten.

1. Wählen Sie die Option **Verwaltungseinheiten** aus, um die Liste mit den Verwaltungseinheiten anzuzeigen, denen der Benutzer zugewiesen wurde.

   ![Screenshot: Verwaltungseinheiten, denen ein Benutzer zugewiesen wurde](./media/admin-units-add-manage-users/list-user-admin-units.png)

### <a name="use-powershell"></a>Verwenden von PowerShell

Führen Sie den folgenden Befehl aus:

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -Id $_.ObjectId | where {$_.RefObjectId -eq $userObjId} }
```
> [!NOTE]
> Standardmäßig werden mit `Get-AzureADAdministrativeUnitMember` nur 100 Mitglieder einer Verwaltungseinheit zurückgegeben. Sie können `"-All $true"` hinzufügen, wenn Sie weitere Mitglieder abrufen möchten.

### <a name="use-microsoft-graph"></a>Verwenden von Microsoft Graph

Ersetzen Sie den Platzhalter durch Testinformationen, und führen Sie den folgenden Befehl aus:

```http
https://graph.microsoft.com/v1.0/users/{id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-administrative-unit"></a>Entfernen eines einzelnen Benutzers aus einer Verwaltungseinheit

### <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

Es gibt zwei Möglichkeiten, einen Benutzer aus einer Verwaltungseinheit zu entfernen: 

* Navigieren Sie im Azure-Portal zu **Azure AD** , und wählen Sie die Option **Benutzer** aus. 
  1. Wählen Sie den Benutzer aus, um dessen Profil zu öffnen. 
  1. Wählen Sie die Verwaltungseinheit aus, aus der Sie den Benutzer entfernen möchten, und wählen Sie dann die Option **Aus Verwaltungseinheit entfernen** aus.

     ![Screenshot: Entfernen eines Benutzers aus einer Verwaltungseinheit über den Profilbereich des Benutzers](./media/admin-units-add-manage-users/user-remove-admin-units.png)

* Navigieren Sie im Azure-Portal zu **Azure AD** , und wählen Sie **Verwaltungseinheiten** aus.
  1. Wählen Sie die Verwaltungseinheit aus, aus der Sie den Benutzer entfernen möchten. 
  1. Wählen Sie den Benutzer und dann die Option **Mitglied entfernen** aus.
  
     ![Screenshot: Entfernen eines Benutzers auf der Ebene der Verwaltungseinheit](./media/admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="use-powershell"></a>Verwenden von PowerShell

Führen Sie den folgenden Befehl aus:

```powershell
Remove-AzureADMSAdministrativeUnitMember -Id $auId -MemberId $memberUserObjId
```

### <a name="use-microsoft-graph"></a>Verwenden von Microsoft Graph

Ersetzen Sie die Platzhalter durch Testinformationen, und führen Sie den folgenden Befehl aus:

`https://graph.microsoft.com/v1.0/directory/administrativeUnits/{adminunit-id}/members/{user-id}/$ref`

## <a name="remove-multiple-users-as-a-bulk-operation"></a>Entfernen mehrerer Benutzer per Massenvorgang

Gehen Sie wie folgt vor, um mehrere Benutzer aus einer Verwaltungseinheit zu entfernen:

1. Navigieren Sie im Azure-Portal zu **Azure AD**.

1. Wählen Sie die Option **Verwaltungseinheiten** und dann die Verwaltungseinheit aus, aus der Sie Benutzer entfernen möchten. 

1. Wählen Sie die Option **Bulk remove members** (Massenentfernung von Mitgliedern) aus, und laden Sie anschließend die CSV-Vorlage herunter, die Sie zum Auflisten der zu entfernenden Benutzer verwenden möchten.

   ![Screenshot: Link „Bulk remove members“ (Massenentfernung von Mitgliedern) im Bereich „Benutzer“](./media/admin-units-add-manage-users/bulk-user-remove.png)

1. Bearbeiten Sie die heruntergeladene CSV-Vorlage mit den relevanten Benutzereinträgen. Entfernen Sie auf keinen Fall die beiden ersten Zeilen der Vorlage. Fügen Sie in jeder Zeile einen Benutzerprinzipalnamen (UPN) hinzu.

   ![Screenshot: Bearbeitete CSV-Datei für die Entfernung von Benutzern aus einer Verwaltungseinheit per Massenvorgang](./media/admin-units-add-manage-users/bulk-user-entries.png)

1. Speichern Sie Ihre Änderungen, laden Sie die Datei hoch, und wählen Sie dann **Übermitteln** aus.

## <a name="next-steps"></a>Nächste Schritte

- [Zuweisen einer Rolle zu einer Verwaltungseinheit](admin-units-assign-roles.md)
- [Zuweisen von Gruppen zu einer Verwaltungseinheit](admin-units-add-manage-groups.md)
