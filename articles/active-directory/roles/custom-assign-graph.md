---
title: Zuweisen von Azure AD-Administratorrollen mit Microsoft Graph-API | Microsoft-Dokumentation
description: Zuweisen und Entfernen von Azure AD-Administratorrollen mit der Graph-API in Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/05/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a3f6fab4d3e1a4c2b73c2fe4ab0df3447b75480
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93378342"
---
# <a name="assign-custom-admin-roles-using-the-microsoft-graph-api-in-azure-active-directory"></a>Zuweisen von benutzerdefinierten Administratorrollen mithilfe von Microsoft Graph-API in Azure Active Directory 

Mithilfe von Microsoft Graph-API können Sie automatisieren, wie Benutzerkonten Rollen zugewiesen werden sollen. In diesem Artikel werden POST-, GET- und DELETE-Vorgänge für Rollenzuweisungen (roleAssignments) behandelt.

## <a name="required-permissions"></a>Erforderliche Berechtigungen

Stellen Sie über ein Konto eines globalen Administrators oder Administrators für privilegierte Rollen eine Verbindung mit Ihrer Azure AD-Organisation her, um Rollen zuzuweisen oder zu entfernen.

## <a name="post-operations-on-roleassignment"></a>POST-Vorgänge für RoleAssignment

### <a name="example-1-create-a-role-assignment-between-a-user-and-a-role-definition"></a>Beispiel 1: Erstellen einer Rollenzuweisung zwischen einem Benutzer und einer Rollendefinition

POST

``` HTTP
POST https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
Content-type: application/json
```

Body

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "directoryScopeId":"/"  // Don't use "resourceScope" attribute in Azure AD role assignments. It will be deprecated soon.
}
```

Antwort

``` HTTP
HTTP/1.1 201 Created
```

### <a name="example-2-create-a-role-assignment-where-the-principal-or-role-definition-does-not-exist"></a>Beispiel 2: Erstellen einer Rollenzuweisung, bei der der Prinzipal oder die Rollendefinition nicht vorhanden ist

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Body

``` HTTP
{
    "principalId":" 2142743c-a5b3-4983-8486-4532ccba12869",
    "roleDefinitionId":"194ae4cb-b126-40b2-bd5b-6091b380977d",
    "directoryScopeId":"/"  //Don't use "resourceScope" attribute in Azure AD role assignments. It will be deprecated soon.
}
```

Antwort

``` HTTP
HTTP/1.1 404 Not Found
```
### <a name="example-3-create-a-role-assignment-on-a-single-resource-scope"></a>Beispiel 3: Erstellen einer Rollenzuweisung für einen einzelnen Ressourcenbereich

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Body

``` HTTP
{
    "principalId":" 2142743c-a5b3-4983-8486-4532ccba12869",
    "roleDefinitionId":"e9b2b976-1dea-4229-a078-b08abd6c4f84",    //role template ID of a custom role
    "directoryScopeId":"/13ff0c50-18e7-4071-8b52-a6f08e17c8cc"  //object ID of an application
}
```

Antwort

``` HTTP
HTTP/1.1 201 Created
```

### <a name="example-4-create-an-administrative-unit-scoped-role-assignment-on-a-built-in-role-definition-which-is-not-supported"></a>Beispiel 4: Erstellen einer auf den Bereich der Verwaltungseinheit bezogenen Rollenzuweisung für eine integrierte Rollendefinition, die nicht unterstützt wird

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

Body

``` HTTP
{
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"29232cdf-9323-42fd-ade2-1d097af3e4de",    //role template ID of Exchange Administrator
    "directoryScopeId":"/administrativeUnits/13ff0c50-18e7-4071-8b52-a6f08e17c8cc"    //object ID of an administrative unit
}
```

Antwort

``` HTTP
HTTP/1.1 400 Bad Request
{
    "odata.error":
    {
        "code":"Request_BadRequest",
        "message":
        {
            "message":"The given built-in role is not supported to be assigned to a single resource scope."
        }
    }
}
```

Nur eine Teilmenge der integrierten Rollen ist für die Bereichsdefinition der Verwaltungseinheit aktiviert. Eine Liste der integrierten Rollen, die für eine Verwaltungseinheit unterstützt werden, finden Sie in [dieser Dokumentation](admin-units-assign-roles.md).

## <a name="get-operations-on-roleassignment"></a>GET-Vorgänge für RoleAssignment

### <a name="example-5-get-role-assignments-for-a-given-principal"></a>Beispiel 5: Abrufen von Rollenzuweisungen für einen bestimmten Prinzipal

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=principalId eq ‘<object-id-of-principal>’
```

Antwort

``` HTTP
HTTP/1.1 200 OK
{
"value":[
            { 
                "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1"
                "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
                "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
                "directoryScopeId":"/"  
            } ,
            {
                "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
                "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
                "roleDefinitionId":"fe930be7-5e62-47db-91af-98c3a49a38b1",
                "directoryScopeId":"/"
            }
        ]
}
```

### <a name="example-6-get-role-assignments-for-a-given-role-definition"></a>Beispiel 6: Abrufen von Rollenzuweisungen für eine bestimmte Rollendefinition

GET

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments&$filter=roleDefinitionId eq ‘<object-id-or-template-id-of-role-definition>’
```

Antwort

``` HTTP
HTTP/1.1 200 OK
{
"value":[
            {
                "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
                "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
                "roleDefinitionId":"fe930be7-5e62-47db-91af-98c3a49a38b1",
                "directoryScopeId":"/"
            }
     ]
}
```

### <a name="example-7-get-a-role-assignment-by-id"></a>Beispiel 7: Abrufen einer Rollenzuweisung nach ID

GET

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Antwort

``` HTTP
HTTP/1.1 200 OK
{ 
    "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1",
    "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
    "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "directoryScopeId":"/"
}
```

### <a name="example-8-get-role-assignments-for-a-given-scope"></a>Beispiel 8: Abrufen von Rollenzuweisungen für einen bestimmten Bereich


GET

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments?$filter=directoryScopeId eq '/d23998b1-8853-4c87-b95f-be97d6c6b610'
```

Antwort

``` HTTP
HTTP/1.1 200 OK
{
"value":[
            { 
                "id":"mhxJMipY4UanIzy2yE-r7JIiSDKQoTVJrLE9etXyrY0-1"
                "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
                "roleDefinitionId":"10dae51f-b6af-4016-8d66-8c2a99b929b3",
                "directoryScopeId":"/d23998b1-8853-4c87-b95f-be97d6c6b610"
            } ,
            {
                "id":"CtRxNqwabEKgwaOCHr2CGJIiSDKQoTVJrLE9etXyrY0-1"
                "principalId":"ab2e1023-bddc-4038-9ac1-ad4843e7e539",
                "roleDefinitionId":"3671d40a-1aac-426c-a0c1-a3821ebd8218",
                "directoryScopeId":"/d23998b1-8853-4c87-b95f-be97d6c6b610"
            }
        ]
}
```

## <a name="delete-operations-on-roleassignment"></a>DELETE-Vorgänge für RoleAssignment

### <a name="example-9-delete-a-role-assignment-between-a-user-and-a-role-definition"></a>Beispiel 9: Löschen einer Rollenzuweisung zwischen einem Benutzer und einer Rollendefinition

Delete

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Antwort
``` HTTP
HTTP/1.1 204 No Content
```

### <a name="example-10-delete-a-role-assignment-that-no-longer-exists"></a>Beispiel 10: Löschen einer Rollenzuweisung, die nicht mehr vorhanden ist

Delete

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Antwort

``` HTTP
HTTP/1.1 404 Not Found
```

### <a name="example-11-delete-a-role-assignment-between-self-and-global-administrator-role-definition"></a>Beispiel 11: Löschen einer Rollenzuweisung zwischen der eigenen und der Rollendefinition als globaler Administrator

Delete

``` HTTP
GET https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments/lAPpYvVpN0KRkAEhdxReEJC2sEqbR_9Hr48lds9SGHI-1
```

Antwort

``` HTTP
HTTP/1.1 400 Bad Request
{
    "odata.error":
    {
        "code":"Request_BadRequest",
        "message":
        {
            "lang":"en",
            "value":"Removing self from Global Administrator built-in role is not allowed"},
            "values":null
        }
    }
}
```

Es wird verhindert, dass Benutzer die eigene Rolle als globaler Administrator löschen, um ein Szenario zu vermeiden, in dem ein Mandant keine globalen Administratoren aufweist. Das Entfernen anderer zugewiesenen Rollen ist zulässig.

## <a name="next-steps"></a>Nächste Schritte

* Im [Forum für Azure AD-Administratorrollen](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) können Sie sich gerne mit uns in Verbindung setzen.
* Weitere Informationen zu Rollen und zur Zuweisung von Administratorrollen finden Sie unter [Zuweisen von Administratorrollen](permissions-reference.md).
* Informationen zu Standardbenutzerberechtigungen finden Sie unter [Vergleich von Standardbenutzerberechtigungen für Gäste und Mitglieder](../fundamentals/users-default-permissions.md).