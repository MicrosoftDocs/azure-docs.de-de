---
title: Grundlegendes zu Rollendefinitionen in RBAC für Azure-Ressourcen | Microsoft-Dokumentation
description: Dieser Artikel enthält Informationen zu Rollendefinitionen in der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) für die präzise Zugriffsverwaltung von Azure-Ressourcen.
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
ms.date: 02/09/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 5a08c2ae0b82841fd15aac4af06a8874cf64ba53
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65949991"
---
# <a name="understand-role-definitions-for-azure-resources"></a>Grundlegendes zu Rollendefinitionen für Azure-Ressourcen

Wenn Sie die Funktionsweise eine Rolle nachvollziehen oder eine eigene [benutzerdefinierte Rolle für Azure-Ressourcen](custom-roles.md) erstellen möchten, ist es hilfreich zu verstehen, wie Rollen definiert werden. Dieser Artikel geht ausführlich auf Rollendefinitionen ein und enthält einige Beispiele.

## <a name="role-definition-structure"></a>Struktur einer Rollendefinition

Eine *Rollendefinition* ist eine Sammlung von Berechtigungen. Gelegentlich wird sie auch einfach als *Rolle* bezeichnet. Eine Rollendefinition listet die ausführbaren Vorgänge wie etwa Lesen, Schreiben und Löschen auf. Sie kann auch die Vorgänge auflisten, die nicht ausgeführt werden können, oder Vorgänge, die im Zusammenhang mit den zugrunde liegenden Daten stehen. Eine Rollendefinition hat folgende Struktur:

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

Vorgänge werden mit Zeichenfolgen im folgenden Format angegeben:

- `{Company}.{ProviderName}/{resourceType}/{action}`

Der Teil `{action}` einer Vorgangszeichenfolge gibt die Art der Vorgänge an, die Sie für einen Ressourcentyp ausführen können. So weist `{action}` beispielsweise folgende Teilzeichenfolgen auf:

| Aktionsteilzeichenfolge    | BESCHREIBUNG         |
| ------------------- | ------------------- |
| `*` | Das Platzhalterzeichen gewährt Zugriff auf alle Vorgänge, die der Zeichenfolge entsprechen. |
| `read` | Ermöglicht Lesevorgänge (GET). |
| `write` | Ermöglicht Schreibvorgänge (PUT, POST und PATCH). |
| `delete` | Ermöglicht Löschvorgänge (DELETE). |

Hier sehen Sie die Rollendefinition [Mitwirkender](built-in-roles.md#contributor) im JSON-Format. Der Platzhaltervorgang (`*`) unter `Actions` gibt an, dass der Prinzipal, der dieser Rolle zugewiesen ist, alle Aktionen ausführen und somit alles verwalten kann. Dies schließt auch Aktionen ein, die später definiert werden, wenn Azure neue Ressourcentypen hinzufügt. Die Vorgänge unter `NotActions` werden von `Actions` subtrahiert. Im Falle der Rolle [Mitwirkender](built-in-roles.md#contributor) sorgt `NotActions` dafür, dass die Rolle weder den Zugriff auf Ressourcen verwalten noch Zugriff für Ressourcen zuweisen kann.

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
    "Microsoft.Authorization/elevateAccess/Action"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

## <a name="management-and-data-operations-preview"></a>Verwaltungs- und Datenvorgänge (Vorschauversion)

Die rollenbasierte Zugriffssteuerung für Verwaltungsvorgänge wird in den Eigenschaften `Actions` und `NotActions` einer Rollendefinition angegeben. Im Anschluss finden Sie einige Beispiele für Verwaltungsvorgänge in Azure:

- Verwalten des Zugriffs auf ein Speicherkonto
- Erstellen, Aktualisieren oder Löschen eines Blobcontainers
- Löschen einer Ressourcengruppe und aller dazugehörigen Ressourcen

Verwaltungszugriff geht nicht auf Ihre Daten über. Diese Trennung verhindert, dass Rollen mit Platzhaltern (`*`) uneingeschränkten Zugriff auf Ihre Daten erhalten. Wenn einem Benutzer also beispielsweise die Rolle [Leser](built-in-roles.md#reader) für ein Abonnement zugewiesen ist, kann er das Speicherkonto anzeigen, aber standardmäßig nicht die zugrunde liegenden Daten.

Vorher wurde die rollenbasierte Zugriffssteuerung nicht bei Datenvorgängen verwendet. Die Autorisierung bei Datenvorgängen variiert je nach Ressourcenanbieter. Das gleiche Modell der Autorisierung mit der rollenbasierten Zugriffssteuerung, das für Verwaltungsvorgänge verwendet wurde, wurde auf Datenvorgänge erweitert (zurzeit als Vorschauversion verfügbar).

Zur Unterstützung von Datenvorgängen wurden neue Dateneigenschaften zur Struktur der Rollendefinition hinzugefügt. Datenvorgänge werden in den Eigenschaften `DataActions` und `NotDataActions` angegeben. Durch Hinzufügen dieser Dateneigenschaften wird die Trennung zwischen Verwaltung und Daten beibehalten. Hierdurch wird verhindert, dass aktuelle Rollenzuweisungen mit Platzhaltern (`*`) wider Erwarten über Zugriff auf Daten verfügen. Im Folgenden werden einige Datenvorgänge aufgeführt, die in `DataActions` und `NotDataActions` angegeben werden können:

- Lesen einer Liste von Blobs in einem Container
- Schreiben eines Speicherblobs in einem Container
- Löschen einer Nachricht in einer Warteschlange

Hier folgt die Rollendefinition [Storage-Blobdatenleser](built-in-roles.md#storage-blob-data-reader), die sowohl in den `Actions`-Eigenschaften als auch in den `DataActions`-Eigenschaften Operationen einbezieht. In dieser Rolle können Sie den Blobcontainer sowie die zugrunde liegenden Blobdaten lesen.

```json
{
  "Name": "Storage Blob Data Reader",
  "Id": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "IsCustom": false,
  "Description": "Allows for read access to Azure Storage blob containers and data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/read"
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

Es können nur Datenvorgänge zu den Eigenschaften `DataActions` und `NotDataActions` hinzugefügt werden. Durch Festlegen der Eigenschaft `isDataAction` auf `true` identifizieren Ressourcenanbieter, bei welchen Vorgängen es sich um Datenvorgänge handelt. Wie Sie eine Liste der Vorgänge anzeigen, in denen `isDataAction` auf `true` festgelegt ist, erfahren Sie unter [Vorgänge für Azure Resource Manager-Ressourcenanbieter](resource-provider-operations.md). Bei Rollen, bei denen keine Datenvorgänge vorhanden sind, sind die Eigenschaften `DataActions` und `NotDataActions` in der Rollendefinition nicht erforderlich.

Die Autorisierung für alle API-Aufrufe für Verwaltungsvorgänge wird vom Azure Resource Manager verarbeitet. Die Autorisierung für API-Aufrufe für Datenvorgänge wird von einem Ressourcenanbieter oder vom Azure Resource Manager verarbeitet.

### <a name="data-operations-example"></a>Beispiel für Datenvorgänge

Werfen wir einen Blick auf ein bestimmtes Beispiel, um die Funktionsweise von Verwaltungs-und Datenvorgängen besser zu verstehen. Alice wurde die Rolle [Besitzer](built-in-roles.md#owner) im Abonnementbereich zugewiesen. Bob wurde die Rolle [Mitwirkender an Storage-Blobdaten](built-in-roles.md#storage-blob-data-contributor) in einem Speicherkontobereich zugewiesen. Die folgende Abbildung veranschaulicht dieses Beispiel.

![Erweiterte rollenbasierte Zugriffssteuerung zur Unterstützung von Verwaltungs- und Datenvorgängen](./media/role-definitions/rbac-management-data.png)

Die Rolle [Besitzer](built-in-roles.md#owner) für Alice und die Rolle [Mitwirkender an Storage-Blobdaten](built-in-roles.md#storage-blob-data-contributor) für Bob verfügen über die folgenden Aktionen:

Owner (Besitzer)

&nbsp;&nbsp;&nbsp;&nbsp;Actions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Mitwirkender an Storage-Blobdaten

&nbsp;&nbsp;&nbsp;&nbsp;Actions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Da Alice über eine Platzhalteraktion (`*`) in einem Abonnementbereich verfügt, werden ihre Berechtigungen nach unten vererbt, um ihr die Durchführung aller Verwaltungsaktionen zu ermöglichen. Alice kann Container lesen, schreiben und löschen. Alice kann jedoch keine Vorgänge auf Daten anwenden, ohne zusätzliche Schritte zu unternehmen. Beispielsweise kann Alice standardmäßig die Blobs innerhalb eines Containers nicht lesen. Um die Blobs lesen zu können, müsste Alice die Speicherzugriffsschlüssel abrufen und damit auf die Blobs zugreifen.

Die Berechtigungen von Bob sind ausschließlich auf `Actions` und `DataActions` beschränkt, die in der Rolle [Mitwirkenden an Storage-Blobdaten](built-in-roles.md#storage-blob-data-contributor) angegeben sind. Basierend auf der Rolle kann Bob Verwaltungs- und Datenvorgänge durchführen. Beispielsweise kann Bob Container im angegebenen Speicherkonto lesen, schreiben und löschen und zudem die Blobs lesen, schreiben und löschen.

Weitere Informationen zur Verwaltung und zur Sicherheit auf Datenebene für den Speicher finden Sie im [Azure Storage-Sicherheitsleitfaden](../storage/common/storage-security-guide.md).

### <a name="what-tools-support-using-rbac-for-data-operations"></a>Welche Tools unterstützt die RBAC für Datenvorgänge?

Um Datenvorgänge anzuzeigen und mit diesen zu arbeiten, müssen Sie über die richtigen Tool- oder SDK-Versionen verfügen:

| Tool  | Version  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 oder höher |
| [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli) | 2.0.30 oder höher |
| [Azure für .NET](/dotnet/azure/) | 2.8.0-preview oder höher |
| [Azure SDK für Go](/go/azure/azure-sdk-go-install) | 15.0.0 oder höher |
| [Azure für Java](/java/azure/) | 1.9.0 oder höher |
| [Azure für Python](/python/azure) | 0.40.0 oder höher |
| [Azure SDK für Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 oder höher |

Um die Datenvorgänge in der REST-API anzuzeigen und zu verwenden, müssen Sie den Parameter **api-version** auf die folgende Version oder höher festlegen:

- 2018-01-01-preview

Das Azure-Portal ermöglicht zudem das Durchsuchen und Verwalten der Inhalte von Warteschlangen- und Blobcontainern über die Azure AD-Vorschauoberfläche. Um den Inhalt eines Warteschlangen- oder Blobcontainers anzuzeigen und zu verwalten, klicken Sie in der Speicherkontoübersicht auf **Daten mithilfe der Azure AD-Vorschau erkunden**.

![Erkunden von Warteschlangen- und Blobcontainern mit der Azure AD-Vorschau](./media/role-definitions/rbac-dataactions-browsing.png)

## <a name="actions"></a>Aktionen

Die Berechtigung `Actions` gibt die Verwaltungsvorgänge an, deren Ausführung die Rolle zulässt. Es handelt sich um eine Sammlung von Vorgangszeichenfolgen, mit denen sicherungsfähige Vorgänge von Azure-Ressourcenanbietern identifiziert werden. Im Anschluss finden Sie einige Beispiele für Verwaltungsvorgänge, die in `Actions` verwendet werden können.

| Vorgangszeichenfolge    | BESCHREIBUNG         |
| ------------------- | ------------------- |
| `*/read` | Gewährt Zugriff auf Lesevorgänge für alle Ressourcentypen aller Azure-Ressourcenanbieter.|
| `Microsoft.Compute/*` | Gewährt Zugriff auf alle Vorgänge für alle Ressourcentypen im Microsoft.Compute-Ressourcenanbieter.|
| `Microsoft.Network/*/read` | Gewährt Zugriff auf Lesevorgänge für alle Ressourcentypen im Microsoft.Network-Ressourcenanbieter.|
| `Microsoft.Compute/virtualMachines/*` | Gewährt Zugriff auf alle Vorgänge virtueller Computer sowie auf die dazugehörigen untergeordneten Ressourcentypen.|
| `microsoft.web/sites/restart/Action` | Gewährt Zugriff zum Neustarten einer Web-App.|

## <a name="notactions"></a>NotActions

Die Berechtigung `NotActions` gibt die Verwaltungsvorgänge an, die von den zulässigen Aktionen (`Actions`) ausgeschlossen sind. Verwenden Sie die Berechtigung `NotActions`, wenn die Vorgänge, die Sie zulassen möchten, durch den Ausschluss unzulässiger Vorgänge leichter zu definieren sind. Zur Ermittlung des Zugriffs, der durch eine Rolle gewährt wird (effektive Berechtigungen), werden die `NotActions`-Vorgänge von den `Actions`-Vorgängen subtrahiert.

> [!NOTE]
> Wenn einem Benutzer eine Rolle zugewiesen wird, die einen Vorgang in `NotActions` ausschließt, und dem Benutzer dann durch Zuweisen einer zweiten Rolle der Zugriff auf diesen Vorgang gewährt wird, kann der Benutzer den Vorgang durchführen. `NotActions` ist keine Verweigerungsregel. Es ist lediglich eine bequeme Möglichkeit, eine Gruppe zulässiger Vorgänge zu erstellen, wenn bestimmte Vorgänge ausgeschlossen werden müssen.
>

## <a name="dataactions-preview"></a>DataActions (Vorschauversion)

Die Berechtigung `DataActions` gibt die Datenvorgänge an, deren Ausführung für Ihre Daten innerhalb des Objekts die Rolle zulässt. Wenn ein Benutzer z.B. über Lesezugriff auf Blobdaten auf ein Speicherkonto verfügt, kann er die Blobs in diesem Speicherkonto dann lesen. Im Anschluss finden Sie einige Beispiele für Datenvorgänge, die in `DataActions` verwendet werden können.

| Vorgangszeichenfolge    | BESCHREIBUNG         |
| ------------------- | ------------------- |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/read` | Gibt ein Blob oder eine Liste von Blobs zurück. |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/write` | Gibt das Ergebnis beim Schreiben eines Blobs zurück. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/read` | Gibt eine Nachricht zurück. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/*` | Gibt eine Nachricht oder das Ergebnis beim Schreiben oder Löschen einer Nachricht zurück. |

## <a name="notdataactions-preview"></a>NotDataActions (Vorschauversion)

Die Berechtigung `NotDataActions` gibt die Datenvorgänge an, die von den zulässigen Aktionen (`DataActions`) ausgeschlossen sind. Zur Ermittlung des Zugriffs, der durch eine Rolle gewährt wird (effektive Berechtigungen), werden die `NotDataActions`-Vorgänge von den `DataActions`-Vorgängen subtrahiert. Jedem Ressourcenanbieter steht eine entsprechende Gruppe von APIs zur Verfügung, um Datenvorgänge durchzuführen.

> [!NOTE]
> Wenn einem Benutzer eine Rolle zugewiesen wird, die einen Datenvorgang in `NotDataActions` ausschließt, und dem Benutzer dann durch Zuweisen einer zweiten Rolle der Zugriff auf diesen Datenvorgang gewährt wird, kann der Benutzer den Datenvorgang durchführen. `NotDataActions` ist keine Verweigerungsregel. Es ist lediglich eine bequeme Möglichkeit, eine Gruppe zulässiger Datenvorgänge zu erstellen, wenn bestimmte Datenvorgänge ausgeschlossen werden müssen.
>

## <a name="assignablescopes"></a>AssignableScopes

Die Eigenschaft `AssignableScopes` gibt die Bereiche (Abonnements, Ressourcengruppen oder Ressourcen) an, für die die Rolle zugewiesen werden kann. Sie können die Verfügbarkeit der Rolle für die Zuweisung auf die Abonnements oder Ressourcengruppen beschränken, für die dies erforderlich ist, um eine bessere Übersichtlichkeit der restlichen Abonnements oder Ressourcengruppen zu erzielen. Sie müssen mindestens ein Abonnement, eine Ressourcengruppe oder eine Ressourcen-ID verwenden.

Bei integrierten Rollen ist `AssignableScopes` auf den Stammbereich (`"/"`) festgelegt. Der Stammbereich gibt an, dass die Rolle für die Zuweisung in allen Bereichen verfügbar ist. Beispiele für gültige zuweisbare Bereiche:

| Szenario | Beispiel |
|----------|---------|
| Rolle ist für die Zuweisung in einem einzelnen Abonnement verfügbar | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"` |
| Rolle ist für die Zuweisung in zwei Abonnements verfügbar | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"` |
| Rolle ist nur für die Zuweisung in der Netzwerkressourcengruppe verfügbar | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network"` |
| Rolle ist für die Zuweisung in allen Bereichen verfügbar | `"/"` |

Informationen zu `AssignableScopes` für benutzerdefinierte Rollen finden Sie unter [Benutzerdefinierte Rollen für Azure-Ressourcen](custom-roles.md).

## <a name="next-steps"></a>Nächste Schritte

* [Integrierte Rollen für die rollenbasierte Zugriffssteuerung in Azure](built-in-roles.md)
* [Benutzerdefinierte Rollen für Azure-Ressourcen](custom-roles.md)
* [Vorgänge für Azure Resource Manager-Ressourcenanbieter](resource-provider-operations.md)
