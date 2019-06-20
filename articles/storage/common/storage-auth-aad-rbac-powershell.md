---
title: Verwenden von Azure PowerShell zum Verwalten von Azure AD-Zugriffsrechten für Blob- und Warteschlangendaten mit RBAC – Azure Storage
description: Verwenden Sie Azure PowerShell, um den Zugriff auf Container und Warteschlangen mit der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) zuzuweisen. Azure Storage unterstützt für die Authentifizierung über Azure AD integrierte und benutzerdefinierte RBAC-Rollen.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: e850b915cd01b6bacd70d6df7752eeb83f7101d0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65153856"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-using-powershell"></a>Gewähren von Zugriff auf Azure-Blob- und -Warteschlangendaten mit RBAC über PowerShell

Azure Active Directory (Azure AD) autorisiert Rechte für den Zugriff auf abgesicherte Ressourcen über die [rollenbasierte Zugriffssteuerung (RBAC)](../../role-based-access-control/overview.md). Azure Storage bietet eine Reihe integrierter RBAC-Rollen mit üblichen Berechtigungssätzen für den Zugriff auf Container und Warteschlangen. 

Wenn einem Azure AD-Sicherheitsprinzipal eine RBAC-Rolle zugewiesen wird, gewährt Azure diesem Sicherheitsprinzipal Zugriff auf diese Ressourcen. Der Zugriff kann auf die Ebene des Abonnements, der Ressourcengruppe, des Speicherkontos oder eines einzelnen Containers oder einer Warteschlange begrenzt werden. Ein Azure AD-Sicherheitsprinzipal kann ein Benutzer, eine Gruppe, ein Anwendungsdienstprinzipal oder eine [verwaltete Identität für Azure-Ressourcen](../../active-directory/managed-identities-azure-resources/overview.md) sein.

Dieser Artikel beschreibt, wie Sie mithilfe von Azure PowerShell integrierte RBAC-Rollen auflisten und diese Benutzern zuweisen. Weitere Informationen zur Verwendung von Azure PowerShell finden Sie unter [Übersicht über Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="rbac-roles-for-blobs-and-queues"></a>RBAC-Rollen für Blobs und Warteschlangen

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Bestimmen des Ressourcenumfangs 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-rbac-roles"></a>Auflisten verfügbarer RBAC-Rollen

Verwenden Sie zum Auflisten verfügbarer integrierter RBAC-Rollen mit Azure PowerShell den Befehl [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition):

```powershell
Get-AzRoleDefinition | FT Name, Description
```

Die integrierten Azure Storage-Datenrollen werden zusammen mit anderen integrierten Rollen für Azure aufgelistet:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-rbac-role-to-a-user"></a>Zuweisen einer RBAC-Rolle zu einem Benutzer

Verwenden Sie den Befehl [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment), um einem Benutzer eine RBAC-Rolle zuzuweisen. Das Format des Befehls kann je nach Bereich der Zuweisung unterschiedlich sein. In den folgenden Beispielen wird veranschaulicht, wie Sie einem Benutzer eine Rolle in verschiedenen Bereichen zuweisen können.

### <a name="container-scope"></a>Containerbereich

Geben Sie zum Zuweisen einer auf einen Container begrenzten Rolle für den Parameter `--scope` eine Zeichenfolge an, die den Containerbereich enthält. Der Bereich für einen Container weist folgendes Format auf:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

Im folgenden Beispiel wird die Rolle **Mitwirkender an Storage-Blobdaten** einem Benutzer zugewiesen und auf einen Container mit dem Namen *sample-container* begrenzt. Ersetzen Sie die Beispielwerte und die Platzhalterwerte in Klammern durch Ihre eigenen Werte: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>Warteschlangenbereich

Geben Sie zum Zuweisen einer auf eine Warteschlange begrenzten Rolle für den Parameter `--scope` eine Zeichenfolge an, die den Warteschlangenbereich enthält. Der Bereich für eine Warteschlange weist folgendes Format auf:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

Im folgenden Beispiel wird die Rolle **Mitwirkender an Storage-Warteschlangendaten** einem Benutzer zugewiesen und auf eine Warteschlange mit dem Namen *sample-queue* begrenzt. Ersetzen Sie die Beispielwerte und die Platzhalterwerte in Klammern durch Ihre eigenen Werte: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>Speicherkontobereich

Geben Sie zum Zuweisen einer auf eine Warteschlange begrenzten Rolle für den Parameter `--scope` den Bereich der Speicherkontoressource an. Der Bereich für ein Speicherkonto weist die folgende Form auf:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Das folgende Beispiel zeigt, wie die Rolle **Storage-Blobdatenleser** für einen Benutzer auf die Ebene des Speicherkontos begrenzt wird. Stellen Sie sicher, dass Sie die Beispielwerte durch Ihre eigenen Werte ersetzen: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Ressourcengruppenbereich

Geben Sie zum Zuweisen einer auf eine Ressourcengruppe begrenzten Rolle für den Parameter `--resource-group` den Namen oder die ID der Ressourcengruppe an. Im folgenden Beispiel wird die Rolle **Storage-Warteschlangendatenleser** einem Benutzer auf Ressourcengruppenebene zugewiesen. Ersetzen Sie die Beispielwerte und Platzhalterwerte in Klammern durch Ihre eigenen Werte: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Reader" `
    -ResourceGroupName "sample-resource-group"
```

### <a name="subscription-scope"></a>Abonnementbereich

Geben Sie zum Zuweisen einer auf das Abonnement begrenzten Rolle für den Parameter `--scope` den Bereich für das Abonnement an. Der Bereich für ein Abonnement weist folgendes Format auf:

```
/subscriptions/<subscription>
```

Im folgenden Beispiel wird veranschaulicht, wie die Rolle **Storage-Blobdatenleser** einem Benutzer auf Speicherkontoebene zugewiesen wird. Ersetzen Sie die Beispielwerte durch Ihre eigenen Werte: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten des Zugriffs auf Azure-Ressourcen mit RBAC und Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [Gewähren von Zugriff auf Azure-Blob- und -Warteschlangendaten mit RBAC über die Azure CLI](storage-auth-aad-rbac-cli.md)
- [Gewähren von Zugriff auf Azure-Blob- und -Warteschlangendaten mit RBAC über das Azure-Portal](storage-auth-aad-rbac-portal.md)
