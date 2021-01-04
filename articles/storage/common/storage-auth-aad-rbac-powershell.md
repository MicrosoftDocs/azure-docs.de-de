---
title: Zuweisen einer Azure-Rolle für den Datenzugriff mithilfe von PowerShell
titleSuffix: Azure Storage
description: Hier erfahren Sie, wie Sie mit dem Azure PowerShell-Modul Berechtigungen für einen Azure Active Directory-Sicherheitsprinzipal mit rollenbasierter Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC) zuweisen. Azure Storage unterstützt für die Authentifizierung über Azure AD integrierte und benutzerdefinierte Azure-Rollen.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/07/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 852375cc7948fc7f6bd106380b3194f2dc84b8ca
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96778919"
---
# <a name="use-powershell-to-assign-an-azure-role-for-access-to-blob-and-queue-data"></a>Zuweisen einer Azure-Rolle für den Zugriff auf Blob- und Warteschlangendaten mithilfe von PowerShell

Azure Active Directory (Azure AD) autorisiert Rechte für den Zugriff auf abgesicherte Ressourcen über die [rollenbasierte Zugriffssteuerung (Azure Role-Based Access Control, Azure RBAC)](../../role-based-access-control/overview.md). Azure Storage bietet eine Reihe integrierter Rollen mit normalen Berechtigungen für den Zugriff auf Container und Warteschlangen.

Wenn einem Azure AD-Sicherheitsprinzipal eine Azure-Rolle zugewiesen wird, gewährt ihm Azure Zugriff auf diese Ressourcen. Der Zugriff kann auf die Ebene des Abonnements, der Ressourcengruppe, des Speicherkontos oder eines einzelnen Containers oder einer Warteschlange begrenzt werden. Eine Azure AD-Sicherheitsprinzipal kann ein Benutzer, eine Gruppe, ein Anwendungsdienstprinzipal oder eine [verwaltete Identität für Azure-Ressourcen](../../active-directory/managed-identities-azure-resources/overview.md) sein.

Dieser Artikel beschreibt, wie Sie mithilfe von Azure PowerShell integrierte Rollen auflisten und diese Benutzern zuweisen. Weitere Informationen zur Verwendung von Azure PowerShell finden Sie unter [Übersicht über Azure PowerShell](/powershell/azure/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="azure-roles-for-blobs-and-queues"></a>Azure-Rollen für Blobs und Warteschlangen

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Bestimmen des Ressourcenumfangs

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-azure-roles"></a>Auflisten verfügbarer Azure-Rollen

Verwenden Sie zum Auflisten verfügbarer integrierter Rollen mit Azure PowerShell den Befehl [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition):

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

## <a name="assign-an-azure-role-to-a-security-principal"></a>Zuweisen einer Azure-Rolle zu einem Sicherheitsprinzipal

Verwenden Sie den Befehl [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment), um einem Sicherheitsprinzipal eine Azure-Rolle zuzuweisen. Das Format des Befehls kann je nach Bereich der Zuweisung unterschiedlich sein. Für das Ausführen des Befehls benötigen Sie eine der Rollen „Besitzer“ oder „Mitwirkender“ für den entsprechenden Bereich. Die folgenden Beispiele zeigen, wie Sie einem Benutzer eine Rolle in verschiedenen Bereichen zuweisen können. Sie können jedoch den gleichen Befehl verwenden, um eine Rolle einem beliebigen Sicherheitsprinzipal zuzuordnen.

> [!NOTE]
> Wenn Sie ein Azure Storage-Konto erstellen, erhalten Sie nicht automatisch Berechtigungen für den Zugriff auf Daten über Azure AD. Sie müssen sich selbst explizit eine Azure-Rolle für Azure Storage zuweisen. Sie können sie auf Ebene Ihres Abonnements, einer Ressourcengruppe, eines Speicherkontos oder eines Containers oder einer Warteschlange zuordnen.

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

Geben Sie zum Zuweisen einer auf eine Warteschlange begrenzten Rolle für den Parameter `--scope` den Bereich der Speicherkontoressource an. Der Bereich für ein Speicherkonto weist folgendes Format auf:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Das folgende Beispiel zeigt, wie die Rolle **Storage-Blobdatenleser** für einen Benutzer auf die Ebene des Speicherkontos begrenzt wird. Ersetzen Sie die Beispielwerte durch Ihre eigenen Werte: 

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

- [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe des Azure PowerShell-Moduls](../../role-based-access-control/role-assignments-powershell.md)
- [Verwenden der Azure-Befehlszeilenschnittstelle zum Zuweisen einer Azure-Rolle für den Zugriff auf Blob- und Warteschlangendaten](storage-auth-aad-rbac-cli.md)
- [Zuweisen einer Azure-Rolle für den Zugriff auf Blob- und Warteschlangendaten über das Azure-Portal](storage-auth-aad-rbac-portal.md)
