---
title: 'Verwalten des Zugriffs auf Azure-Ressourcen mit RBAC und der REST-API: Azure | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie den Zugriff auf Azure-Ressourcen für Benutzer, Gruppen und Anwendungen mit der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) und der REST-API verwalten. Dazu gehören das Auflisten, Erteilen und Entfernen des Zugriffs.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 3602e4ca83e828270ebef56c688670b896ca58a4
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66472730"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-rest-api"></a>Verwalten des Zugriffs auf Azure-Ressourcen mit RBAC und der REST-API

Der Zugriff auf Azure-Ressourcen wird mithilfe der [rollenbasierten Zugriffssteuerung (RBAC)](overview.md) verwaltet. In diesem Artikel wird beschrieben, wie Sie den Zugriff für Benutzer, Gruppen und Anwendungen mit der RBAC und der REST-API verwalten.

## <a name="list-access"></a>Auflisten des Zugriffs

Zum Auflisten des Zugriffs in RBAC führen Sie die Rollenzuweisungen auf. Um Rollenzuweisungen aufzulisten, verwenden Sie eine der REST-APIs unter [Rollenzuweisungen – Auflisten](/rest/api/authorization/roleassignments/list). Um die Ergebnisse einzugrenzen, geben Sie einen Bereich und einen optionalen Filter an.

1. Beginnen Sie mit der folgenden Anforderung:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. Ersetzen Sie innerhalb des URIs *{scope}* durch den Bereich, für den die Rollen aufgelistet werden sollen.

    | `Scope` | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Abonnement |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Ressourcengruppe |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |
    
       
     > [!NOTE]
     > Im Beispiel oben wird „Microsoft.web“ als Ressourcenanbieter verwendet. Er verweist auf die App-Dienstinstanz. Analog dazu können Sie einen beliebigen anderen Ressourcenanbieter verwenden und den Bereichs-URI bilden. Weitere Informationen finden Sie unter [Azure-Ressourcenanbieter und -typen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services) und [Vorgänge für Azure Resource Manager-Ressourcenanbieter](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations).  
     
1. Ersetzen Sie *{filter}* durch die Bedingung, die zum Filtern der Liste mit den Rollenzuweisungen angewendet werden soll.

    | Filter | BESCHREIBUNG |
    | --- | --- |
    | `$filter=atScope()` | Listet nur die Rollenzuweisungen für den angegebenen Bereich auf, ohne die Rollenzuweisungen der Unterbereiche. |
    | `$filter=principalId%20eq%20'{objectId}'` | Listet die Rollenzuweisungen für bestimmte Benutzer, Gruppen oder Dienstprinzipale auf. |
    | `$filter=assignedTo('{objectId}')` | Listet die Rollenzuweisungen für bestimmte Benutzer oder Dienstprinzipale auf. Wenn der Benutzer Mitglied einer Gruppe ist, die über eine Rollenzuweisung verfügt, wird diese Rollenzuweisung ebenfalls aufgeführt. Dieser Filter ist für Gruppen transitiv, das heißt: Wenn der Benutzer Mitglied einer Gruppe und diese Gruppe wiederum Mitglied einer anderen Gruppe ist, die über eine Rollenzuweisung verfügt, wird diese Rollenzuweisung ebenfalls aufgeführt. Dieser Filter akzeptiert nur eine Objekt-ID für einen Benutzer oder einen Dienstprinzipal. Für eine Gruppe kann keine Objekt-ID übergeben werden. |

## <a name="grant-access"></a>Gewähren von Zugriff

In RBAC erstellen Sie zum Gewähren des Zugriffs eine Rollenzuweisung. Um eine Rollenzuweisung zu erstellen, verwenden Sie die REST-API unter [Rollenzuweisungen – Erstellen](/rest/api/authorization/roleassignments/create), und geben Sie das Sicherheitsprinzipal, die Rollendefinition und den Bereich an. Zum Aufrufen dieser API benötigen Sie Zugriff auf den Vorgang `Microsoft.Authorization/roleAssignments/write`. Von den integrierten Rollen verfügen nur [Besitzer](built-in-roles.md#owner) und [Benutzerzugriffsadministrator](built-in-roles.md#user-access-administrator) über Zugriff auf diesen Vorgang.

1. Verwenden Sie die REST API unter [Rollendefinitionen – Auflisten](/rest/api/authorization/roledefinitions/list) oder lesen Sie [Integrierte Rollen in Azure](built-in-roles.md), um den Bezeichner für die zuzuweisende Rollendefinition zu ermitteln.

1. Verwenden Sie ein GUID-Tool, um einen eindeutigen Bezeichner zu generieren, der für den Bezeichner der Rollenzuweisung verwendet wird. Das Format des Bezeichners ist: `00000000-0000-0000-0000-000000000000`

1. Beginnen Sie mit folgender Anforderung und folgendem Text:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```
    
1. Ersetzen Sie innerhalb des URIs *{scope}* durch den Bereich für die Rollenzuweisung.

    | `Scope` | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Abonnement |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Ressourcengruppe |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Ersetzen Sie *{roleAssignmentName}* durch den GUID-Bezeichner der Rollenzuweisung.

1. Ersetzen Sie innerhalb des Anforderungstexts *{subscriptionId}* durch Ihre Abonnement-ID.

1. Ersetzen Sie *{roleDefinitionId}* durch den Bezeichner der Rollendefinition.

1. Ersetzen Sie *{principalId}* durch den Objektbezeichner des Benutzers, der Gruppe oder des Dienstprinzipals, der bzw. die der Rolle zugewiesen wird.

## <a name="remove-access"></a>Zugriff entfernen

In RBAC entfernen Sie eine Rollenzuweisung, um den Zugriff zu entfernen. Zum Entfernen einer Rollenzuweisung verwenden Sie die REST-API [Rollenzuweisungen – Löschen](/rest/api/authorization/roleassignments/delete). Zum Aufrufen dieser API benötigen Sie Zugriff auf den Vorgang `Microsoft.Authorization/roleAssignments/delete`. Von den integrierten Rollen verfügen nur [Besitzer](built-in-roles.md#owner) und [Benutzerzugriffsadministrator](built-in-roles.md#user-access-administrator) über Zugriff auf diesen Vorgang.

1. Rufen Sie den Bezeichner der Rollenzuweisung (GUID) ab. Dieser Bezeichner wird beim ersten Erstellen der Rollenzuweisung zurückgegeben oder kann durch Auflisten der Rollenzuweisungen ermittelt werden.

1. Beginnen Sie mit der folgenden Anforderung:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. Ersetzen Sie innerhalb des URIs *{scope}* durch den Bereich, um die Rollenzuweisung zu entfernen.

    | `Scope` | Type |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Abonnement |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Ressourcengruppe |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Ersetzen Sie *{roleAssignmentName}* durch den GUID-Bezeichner der Rollenzuweisung.

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen von Ressourcen mit Resource Manager-Vorlagen und Resource Manager-REST-API](../azure-resource-manager/resource-group-template-deploy-rest.md)
- [Azure REST-API-Referenz](/rest/api/azure/)
- [Erstellen von benutzerdefinierten Rollen für Azure-Ressourcen mithilfe der REST-API](custom-roles-rest.md)
