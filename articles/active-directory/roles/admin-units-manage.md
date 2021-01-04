---
title: Hinzufügen und Entfernen von Verwaltungseinheiten – Azure Active Directory | Microsoft-Dokumentation
description: Verwenden Sie Verwaltungseinheiten, um den Geltungsbereich der Rollenberechtigungen in Azure Active Directory einzuschränken.
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
ms.openlocfilehash: bbe76d21d948525421790f574830da7b3a163216
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395392"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Verwalten von Verwaltungseinheiten in Azure Active Directory

Um eine präzisere administrative Steuerung in Azure Active Directory (Azure AD) zu ermöglichen, können Sie Benutzer einer Azure AD-Rolle mit einem auf eine oder mehrere Verwaltungseinheiten beschränkten Bereich zuweisen.

## <a name="get-started"></a>Erste Schritte

1. Um Abfragen aus den folgenden Anweisungen über den [Graph-Tester](https://aka.ms/ge) auszuführen, gehen Sie folgendermaßen vor:

    a. Wechseln Sie im Azure-Portal zu Azure AD. 
    
    b. Wählen Sie in der Anwendungsliste **Graph-Tester** aus.
    
    c. Wählen Sie im Bereich **Berechtigungen** die Option **Grant admin consent for Graph explorer** (Administratoreinwilligung für Graph-Tester erteilen) aus.

    ![Screenshot: Link „Grant admin consent for Graph explorer“ (Administratoreinwilligung für Graph-Tester erteilen)](./media/admin-units-manage/select-graph-explorer.png)


1. Verwenden Sie die Vorschauversion von Azure AD PowerShell.

## <a name="add-an-administrative-unit"></a>Hinzufügen einer Verwaltungseinheit

Sie können eine Verwaltungseinheit entweder über das Azure-Portal oder mithilfe von PowerShell hinzufügen.

### <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

1. Wechseln Sie im Azure-Portal zu Azure AD. Anschließend können Sie im linken Bereich **Verwaltungseinheiten** auswählen.

    ![Screenshot: Link „Verwaltungseinheiten“ in Azure AD](./media/admin-units-manage/nav-to-admin-units.png)

1. Wählen Sie oben im Bereich die Schaltfläche **Hinzufügen** aus, und geben Sie dann im Feld **Name** den Namen der Verwaltungseinheit ein. Fügen Sie optional eine Beschreibung der Verwaltungseinheit hinzu.

    ![Screenshot: Schaltfläche „Hinzufügen“ und Feld „Name“ zum Eingeben des Namens der Verwaltungseinheit](./media/admin-units-manage/add-new-admin-unit.png)

1. Wählen Sie die blaue Schaltfläche **Hinzufügen** aus, um die Verwaltungseinheit abzuschließen.

### <a name="use-powershell"></a>Verwenden von PowerShell

Installieren Sie Azure AD PowerShell (Vorschau), bevor Sie versuchen, die folgenden Befehle auszuführen:

```powershell
Connect-AzureAD
New-AzureADMSAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"
```

Sie können die Werte in den Anführungszeichen nach Bedarf ändern.

### <a name="use-microsoft-graph"></a>Verwenden von Microsoft Graph

```http
Http Request
POST /administrativeUnits
Request body
{
  "displayName": "North America Operations",
  "description": "North America Operations administration"
}
```

## <a name="remove-an-administrative-unit"></a>Entfernen einer Verwaltungseinheit

In Azure AD können Sie eine Verwaltungseinheit entfernen, die nicht mehr als Bereichseinheit für Administratorrollen benötigt wird.

### <a name="use-the-azure-portal"></a>Verwenden des Azure-Portals

1. Navigieren Sie im Azure-Portal zu **Azure AD** , und wählen Sie **Verwaltungseinheiten** aus. 
1. Wählen Sie die zu löschende Verwaltungseinheit und dann **Löschen** aus. 
1. Klicken Sie auf **Ja** , um zu bestätigen, dass Sie die Verwaltungseinheit löschen möchten. Die Verwaltungseinheit wird gelöscht.

![Screenshot: Schaltfläche „Löschen“ und Bestätigungsfenster für eine Verwaltungseinheit](./media/admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>Verwenden von PowerShell

```powershell
$delau = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
Remove-AzureADMSAdministrativeUnit -ObjectId $delau.ObjectId
```

Sie können die Werte in den Anführungszeichen so ändern, wie sie für die jeweilige Umgebung benötigt werden.

### <a name="use-the-graph-api"></a>Verwenden der Graph-API

```http
HTTP request
DELETE /administrativeUnits/{Admin id}
Request body
{}
```

## <a name="next-steps"></a>Nächste Schritte

* [Verwalten von Benutzern in einer Verwaltungseinheit](admin-units-add-manage-users.md)
* [Verwalten von Gruppen in einer Verwaltungseinheit](admin-units-add-manage-groups.md)
