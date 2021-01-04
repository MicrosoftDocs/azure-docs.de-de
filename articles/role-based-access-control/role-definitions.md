---
title: 'Grundlegendes zu Azure-Rollendefinitionen: Azure RBAC'
description: Dieser Artikel enthält Informationen zu Azure-Rollendefinitionen in der rollenbasierten Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC) für die präzise Zugriffsverwaltung von Azure-Ressourcen.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/08/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: bc3640fecbe1138e46fd0d36975691740bc669dd
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369258"
---
# <a name="understand-azure-role-definitions"></a>Grundlegendes zu Azure-Rollendefinitionen

Wenn Sie die Funktionsweise eine Azure-Rolle nachvollziehen oder eine eigene [benutzerdefinierte Azure-Rolle](custom-roles.md) erstellen möchten, ist es hilfreich zu verstehen, wie Rollen definiert werden. Dieser Artikel geht ausführlich auf Rollendefinitionen ein und enthält einige Beispiele.

## <a name="role-definition"></a>Rollendefinition

Eine *Rollendefinition* ist eine Sammlung von Berechtigungen. Gelegentlich wird sie auch einfach als *Rolle* bezeichnet. Eine Rollendefinition listet die ausführbaren Vorgänge wie etwa Lesen, Schreiben und Löschen auf. Unter Umständen werden außerdem die Vorgänge aufgeführt, die nicht zugelassen sind oder im Zusammenhang mit den zugrunde liegenden Daten stehen.

Nachstehend finden Sie ein Beispiel für die in einer Rollendefinition enthaltenen Eigenschaften, wenn diese mit Azure PowerShell angezeigt wird:

```
Name
Id
IsCustom
Description
Actions []
NotActions []
DataActions []
NotDataActions []
AssignableScopes []
```

Nachstehend finden Sie ein Beispiel für die in einer Rollendefinition enthaltenen Eigenschaften, wenn diese im Azure-Portal, in der Azure CLI oder in der REST-API angezeigt wird:

```
roleName
name
type
description
actions []
notActions []
dataActions []
notDataActions []
assignableScopes []
```

In der folgenden Tabelle wird erläutert, was die einzelnen Eigenschaften bedeuten.

| Eigenschaft | BESCHREIBUNG |
| --- | --- |
| `Name`</br>`roleName` | Der Anzeigename der Rolle. |
| `Id`</br>`name` | Die eindeutige ID der Rolle. |
| `IsCustom`</br>`roleType` | Gibt an, ob dies eine benutzerdefinierte Rolle ist. Legen Sie die Eigenschaft für benutzerdefinierte Rollen auf `true` oder `CustomRole` fest. Legen Sie die Eigenschaft für integrierte Rollen auf `false` oder `BuiltInRole` fest. |
| `Description`</br>`description` | Die Beschreibung der Rolle. |
| `Actions`</br>`actions` | Ein Array von Zeichenfolgen, das die Verwaltungsvorgänge angibt, deren Ausführung die Rolle zulässt. |
| `NotActions`</br>`notActions` | Ein Array von Zeichenfolgen, das die Verwaltungsvorgänge angibt, die von den zulässigen `Actions` ausgeschlossen sind. |
| `DataActions`</br>`dataActions` | Ein Array von Zeichenfolgen, das die Datenvorgänge angibt, deren Ausführung für Ihre Daten innerhalb des Objekts die Rolle zulässt. |
| `NotDataActions`</br>`notDataActions` | Ein Array von Zeichenfolgen, das die Datenvorgänge angibt, die von den zulässigen `DataActions` ausgeschlossen sind. |
| `AssignableScopes`</br>`assignableScopes` | Ein Array aus Zeichenfolgen zum Angeben der Bereiche, in denen die Rolle zur Zuweisung verfügbar ist. |

### <a name="operations-format"></a>Vorgangsformat

Vorgänge werden mit Zeichenfolgen im folgenden Format angegeben:

- `{Company}.{ProviderName}/{resourceType}/{action}`

Der Teil `{action}` einer Vorgangszeichenfolge gibt die Art der Vorgänge an, die Sie für einen Ressourcentyp ausführen können. So weist `{action}` beispielsweise folgende Teilzeichenfolgen auf:

| Aktionsteilzeichenfolge    | BESCHREIBUNG         |
| ------------------- | ------------------- |
| `*` | Das Platzhalterzeichen gewährt Zugriff auf alle Vorgänge, die der Zeichenfolge entsprechen. |
| `read` | Ermöglicht Lesevorgänge (GET). |
| `write` | Ermöglicht Schreibvorgänge (PUT oder PATCH). |
| `action` | Ermöglicht benutzerdefinierte Vorgänge wie das Neustarten von virtuellen Computern (POST). |
| `delete` | Ermöglicht Löschvorgänge (DELETE). |

### <a name="role-definition-example"></a>Beispiel für eine Rollendefinition

Im folgenden Codeausschnitt finden Sie die Definition für die Rolle [Mitwirkender](built-in-roles.md#contributor), so wie sie in Azure PowerShell und in der Azure CLI angezeigt wird. Der Platzhaltervorgang (`*`) unter `Actions` gibt an, dass der Prinzipal, der dieser Rolle zugewiesen ist, alle Aktionen ausführen und somit alles verwalten kann. Dies schließt auch Aktionen ein, die später definiert werden, wenn Azure neue Ressourcentypen hinzufügt. Die Vorgänge unter `NotActions` werden von `Actions` subtrahiert. Im Fall der Rolle [Mitwirkender](built-in-roles.md#contributor) sorgt `NotActions` dafür, dass die Rolle weder den Zugriff auf Ressourcen noch die Zuweisungen für Azure Blueprint verwalten kann.

Rolle „Mitwirkender“, so wie sie in Azure PowerShell angezeigt wird:

```json
{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Rolle „Mitwirkender“, so wie sie in der Azure CLI angezeigt wird:

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything except access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management-and-data-operations"></a>Verwaltungs- und Datenvorgänge

Die rollenbasierte Zugriffssteuerung für Verwaltungsvorgänge wird in den Eigenschaften `Actions` und `NotActions` einer Rollendefinition angegeben. Im Anschluss finden Sie einige Beispiele für Verwaltungsvorgänge in Azure:

- Verwalten des Zugriffs auf ein Speicherkonto
- Erstellen, Aktualisieren oder Löschen eines Blobcontainers
- Löschen einer Ressourcengruppe und aller dazugehörigen Ressourcen

Der Verwaltungszugriff wird nicht von Ihren Daten geerbt, vorausgesetzt, die Methode für die Containerauthentifizierung ist auf „Azure AD-Benutzerkonto“ und nicht auf „Zugriffsschlüssel“ festgelegt. Diese Trennung verhindert, dass Rollen mit Platzhaltern (`*`) uneingeschränkten Zugriff auf Ihre Daten erhalten. Wenn einem Benutzer also beispielsweise die Rolle [Leser](built-in-roles.md#reader) für ein Abonnement zugewiesen ist, kann er das Speicherkonto anzeigen, aber standardmäßig nicht die zugrunde liegenden Daten.

Vorher wurde die rollenbasierte Zugriffssteuerung nicht bei Datenvorgängen verwendet. Die Autorisierung bei Datenvorgängen variiert je nach Ressourcenanbieter. Das gleiche Modell der Autorisierung mit der rollenbasierten Zugriffssteuerung, das für Verwaltungsvorgänge verwendet wurde, wurde auf Datenvorgänge erweitert.

Zur Unterstützung von Datenvorgängen wurden der Rollendefinition neue Dateneigenschaften hinzugefügt. Datenvorgänge werden in den Eigenschaften `DataActions` und `NotDataActions` angegeben. Durch Hinzufügen dieser Dateneigenschaften wird die Trennung zwischen Verwaltung und Daten beibehalten. Hierdurch wird verhindert, dass aktuelle Rollenzuweisungen mit Platzhaltern (`*`) wider Erwarten über Zugriff auf Daten verfügen. Im Folgenden werden einige Datenvorgänge aufgeführt, die in `DataActions` und `NotDataActions` angegeben werden können:

- Lesen einer Liste von Blobs in einem Container
- Schreiben eines Speicherblobs in einem Container
- Löschen einer Nachricht in einer Warteschlange

Hier folgt die Rollendefinition [Storage-Blobdatenleser](built-in-roles.md#storage-blob-data-reader), die sowohl in den `Actions`-Eigenschaften als auch in den `DataActions`-Eigenschaften Operationen einbezieht. In dieser Rolle können Sie den Blobcontainer sowie die zugrunde liegenden Blobdaten lesen.

Rolle „Storage-Blobdatenleser“, so wie sie in Azure PowerShell angezeigt wird:

```json
{
  "Name": "Storage Blob Data Reader",
  "Id": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "IsCustom": false,
  "Description": "Allows for read access to Azure Storage blob containers and data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/read",
    "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
  ],
  "NotActions": [],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
  ],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Rolle „Storage-Blobdatenleser“, so wie sie in der Azure CLI angezeigt wird:

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

Es können nur Datenvorgänge zu den Eigenschaften `DataActions` und `NotDataActions` hinzugefügt werden. Durch Festlegen der Eigenschaft `isDataAction` auf `true` identifizieren Ressourcenanbieter, bei welchen Vorgängen es sich um Datenvorgänge handelt. Wie Sie eine Liste der Vorgänge anzeigen, in denen `isDataAction` auf `true` festgelegt ist, erfahren Sie unter [Vorgänge für Azure Resource Manager-Ressourcenanbieter](resource-provider-operations.md). Bei Rollen, bei denen keine Datenvorgänge vorhanden sind, sind die Eigenschaften `DataActions` und `NotDataActions` in der Rollendefinition nicht erforderlich.

Die Autorisierung für alle API-Aufrufe für Verwaltungsvorgänge wird vom Azure Resource Manager verarbeitet. Die Autorisierung für API-Aufrufe für Datenvorgänge wird von einem Ressourcenanbieter oder vom Azure Resource Manager verarbeitet.

### <a name="data-operations-example"></a>Beispiel für Datenvorgänge

Werfen wir einen Blick auf ein bestimmtes Beispiel, um die Funktionsweise von Verwaltungs-und Datenvorgängen besser zu verstehen. Alice wurde die Rolle [Besitzer](built-in-roles.md#owner) im Abonnementbereich zugewiesen. Bob wurde die Rolle [Mitwirkender an Storage-Blobdaten](built-in-roles.md#storage-blob-data-contributor) in einem Speicherkontobereich zugewiesen. Die folgende Abbildung veranschaulicht dieses Beispiel.

![Erweiterte rollenbasierte Zugriffssteuerung zur Unterstützung von Verwaltungs- und Datenvorgängen](./media/role-definitions/rbac-management-data.png)

Die Rolle [Besitzer](built-in-roles.md#owner) für Alice und die Rolle [Mitwirkender an Storage-Blobdaten](built-in-roles.md#storage-blob-data-contributor) für Bob verfügen über die folgenden Aktionen:

Besitzer

&nbsp;&nbsp;&nbsp;&nbsp;Actions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Mitwirkender an Storage-Blobdaten

&nbsp;&nbsp;&nbsp;&nbsp;Actions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Da Alice über eine Platzhalteraktion (`*`) in einem Abonnementbereich verfügt, werden ihre Berechtigungen nach unten vererbt, um ihr die Durchführung aller Verwaltungsaktionen zu ermöglichen. Alice kann Container lesen, schreiben und löschen. Alice kann jedoch keine Vorgänge auf Daten anwenden, ohne zusätzliche Schritte zu unternehmen. Beispielsweise kann Alice standardmäßig die Blobs innerhalb eines Containers nicht lesen. Um die Blobs lesen zu können, müsste Alice die Speicherzugriffsschlüssel abrufen und damit auf die Blobs zugreifen.

Die Berechtigungen von Bob sind ausschließlich auf `Actions` und `DataActions` beschränkt, die in der Rolle [Mitwirkenden an Storage-Blobdaten](built-in-roles.md#storage-blob-data-contributor) angegeben sind. Basierend auf der Rolle kann Bob Verwaltungs- und Datenvorgänge durchführen. Beispielsweise kann Bob Container im angegebenen Speicherkonto lesen, schreiben und löschen und zudem die Blobs lesen, schreiben und löschen.

Weitere Informationen zur Verwaltung und zur Sicherheit auf Datenebene für den Speicher finden Sie im [Azure Storage-Sicherheitsleitfaden](../storage/blobs/security-recommendations.md).

### <a name="what-tools-support-using-azure-roles-for-data-operations"></a>Welche Tools unterstützen die Verwendung von Azure-Rollen für Datenvorgänge?

Um Datenvorgänge anzuzeigen und mit diesen zu arbeiten, müssen Sie über die richtigen Tool- oder SDK-Versionen verfügen:

| Tool  | Version  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 oder höher |
| [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli) | 2.0.30 oder höher |
| [Azure für .NET](/dotnet/azure/) | 2.8.0-preview oder höher |
| [Azure SDK für Go](/azure/go/azure-sdk-go-install) | 15.0.0 oder höher |
| [Azure für Java](/java/azure/) | 1.9.0 oder höher |
| [Azure für Python](/azure/python/) | 0.40.0 oder höher |
| [Azure SDK für Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 oder höher |

Um die Datenvorgänge in der REST-API anzuzeigen und zu verwenden, müssen Sie den Parameter **api-version** auf die folgende Version oder höher festlegen:

- 2018-07-01

## <a name="actions"></a>Aktionen

Die Berechtigung `Actions` gibt die Verwaltungsvorgänge an, deren Ausführung die Rolle zulässt. Es handelt sich um eine Sammlung von Vorgangszeichenfolgen, mit denen sicherungsfähige Vorgänge von Azure-Ressourcenanbietern identifiziert werden. Im Anschluss finden Sie einige Beispiele für Verwaltungsvorgänge, die in `Actions` verwendet werden können.

> [!div class="mx-tableFixed"]
> | Vorgangszeichenfolge    | BESCHREIBUNG         |
> | ------------------- | ------------------- |
> | `*/read` | Gewährt Zugriff auf Lesevorgänge für alle Ressourcentypen aller Azure-Ressourcenanbieter.|
> | `Microsoft.Compute/*` | Gewährt Zugriff auf alle Vorgänge für alle Ressourcentypen im Microsoft.Compute-Ressourcenanbieter.|
> | `Microsoft.Network/*/read` | Gewährt Zugriff auf Lesevorgänge für alle Ressourcentypen im Microsoft.Network-Ressourcenanbieter.|
> | `Microsoft.Compute/virtualMachines/*` | Gewährt Zugriff auf alle Vorgänge virtueller Computer sowie auf die dazugehörigen untergeordneten Ressourcentypen.|
> | `microsoft.web/sites/restart/Action` | Gewährt Zugriff zum Neustarten einer Web-App.|

## <a name="notactions"></a>NotActions

Die Berechtigung `NotActions` gibt die Verwaltungsvorgänge an, die von den zulässigen Aktionen (`Actions`) ausgeschlossen sind. Verwenden Sie die Berechtigung `NotActions`, wenn die Vorgänge, die Sie zulassen möchten, durch den Ausschluss unzulässiger Vorgänge leichter zu definieren sind. Zur Ermittlung des Zugriffs, der durch eine Rolle gewährt wird (effektive Berechtigungen), werden die `NotActions`-Vorgänge von den `Actions`-Vorgängen subtrahiert.

> [!NOTE]
> Wenn einem Benutzer eine Rolle zugewiesen wird, die einen Vorgang in `NotActions` ausschließt, und dem Benutzer dann durch Zuweisen einer zweiten Rolle der Zugriff auf diesen Vorgang gewährt wird, kann der Benutzer den Vorgang durchführen. `NotActions` ist keine Verweigerungsregel. Es ist lediglich eine bequeme Möglichkeit, eine Gruppe zulässiger Vorgänge zu erstellen, wenn bestimmte Vorgänge ausgeschlossen werden müssen.
>

## <a name="dataactions"></a>DataActions

Die Berechtigung `DataActions` gibt die Datenvorgänge an, deren Ausführung für Ihre Daten innerhalb des Objekts die Rolle zulässt. Wenn ein Benutzer z.B. über Lesezugriff auf Blobdaten auf ein Speicherkonto verfügt, kann er die Blobs in diesem Speicherkonto dann lesen. Im Anschluss finden Sie einige Beispiele für Datenvorgänge, die in `DataActions` verwendet werden können.

> [!div class="mx-tableFixed"]
> | Vorgangszeichenfolge    | BESCHREIBUNG         |
> | ------------------- | ------------------- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | Gibt ein Blob oder eine Liste von Blobs zurück. |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` | Gibt das Ergebnis beim Schreiben eines Blobs zurück. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/read` | Gibt eine Nachricht zurück. |
> | `Microsoft.Storage/storageAccounts/queueServices/queues/messages/*` | Gibt eine Nachricht oder das Ergebnis beim Schreiben oder Löschen einer Nachricht zurück. |

## <a name="notdataactions"></a>NotDataActions

Die Berechtigung `NotDataActions` gibt die Datenvorgänge an, die von den zulässigen Aktionen (`DataActions`) ausgeschlossen sind. Zur Ermittlung des Zugriffs, der durch eine Rolle gewährt wird (effektive Berechtigungen), werden die `NotDataActions`-Vorgänge von den `DataActions`-Vorgängen subtrahiert. Jedem Ressourcenanbieter steht eine entsprechende Gruppe von APIs zur Verfügung, um Datenvorgänge durchzuführen.

> [!NOTE]
> Wenn einem Benutzer eine Rolle zugewiesen wird, die einen Datenvorgang in `NotDataActions` ausschließt, und dem Benutzer dann durch Zuweisen einer zweiten Rolle der Zugriff auf diesen Datenvorgang gewährt wird, kann der Benutzer den Datenvorgang durchführen. `NotDataActions` ist keine Verweigerungsregel. Es ist lediglich eine bequeme Möglichkeit, eine Gruppe zulässiger Datenvorgänge zu erstellen, wenn bestimmte Datenvorgänge ausgeschlossen werden müssen.
>

## <a name="assignablescopes"></a>AssignableScopes

Die Eigenschaft `AssignableScopes` gibt die Bereiche (Verwaltungsgruppen, Abonnements, oder Ressourcengruppen) an, für die diese Rollendefinition verfügbar ist. Sie können die Rolle nur in den für Sie erforderlichen Verwaltungsgruppen, Abonnements oder Ressourcengruppen für die Zuweisung zur Verfügung stellen. Sie müssen mindestens eine Verwaltungsgruppe, ein Abonnement oder eine Ressourcengruppe verwenden.

Bei integrierten Rollen ist `AssignableScopes` auf den Stammbereich (`"/"`) festgelegt. Der Stammbereich gibt an, dass die Rolle für die Zuweisung in allen Bereichen verfügbar ist. Beispiele für gültige zuweisbare Bereiche:

> [!div class="mx-tableFixed"]
> | Rolle ist für die Zuweisung verfügbar | Beispiel |
> |----------|---------|
> | Ein Abonnement | `"/subscriptions/{subscriptionId1}"` |
> | Zwei Abonnements | `"/subscriptions/{subscriptionId1}", "/subscriptions/{subscriptionId2}"` |
> | Netzwerkressourcengruppe | `"/subscriptions/{subscriptionId1}/resourceGroups/Network"` |
> | Eine Verwaltungsgruppe | `"/providers/Microsoft.Management/managementGroups/{groupId1}"` |
> | Verwaltungsgruppe und ein Abonnement | `"/providers/Microsoft.Management/managementGroups/{groupId1}", /subscriptions/{subscriptionId1}",` |
> | Alle Bereiche (gilt nur für integrierte Rollen) | `"/"` |

Informationen zu `AssignableScopes` für benutzerdefinierte Rollen finden Sie unter [Benutzerdefinierte Azure-Rollen](custom-roles.md).

## <a name="next-steps"></a>Nächste Schritte

* [Integrierte Azure-Rollen](built-in-roles.md)
* [Benutzerdefinierte Azure-Rollen](custom-roles.md)
* [Vorgänge für Azure-Ressourcenanbieter](resource-provider-operations.md)
