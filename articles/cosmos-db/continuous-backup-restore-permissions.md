---
title: Konfigurieren Sie die Berechtigungen zum Wiederherstellen eines Azure Cosmos DB-Kontos.
description: Erfahren Sie, wie Sie die Wiederherstellungsberechtigungen für ein Konto für die fortlaufende Sicherung auf eine bestimmte Rolle oder einen Prinzipal isolieren und einschränken. Hier wird beschrieben, wie Sie im Azure-Portal oder mithilfe der Befehlszeilenschnittstelle eine integrierte Rolle zuweisen oder eine benutzerdefinierte Rolle definieren.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 9d30f5325162b9ea447d54aadc092dbd9aa29132
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538692"
---
# <a name="manage-permissions-to-restore-an-azure-cosmos-db-account"></a>Verwalten von Berechtigungen zum Wiederherstellen eines Azure Cosmos DB-Kontos
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> Die Funktion der Zeitpunktwiederherstellung (fortlaufender Sicherungsmodus) für Azure Cosmos DB befindet sich derzeit in der öffentlichen Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mit Azure Cosmos DB können Sie die Wiederherstellungsberechtigungen für ein Konto für die fortlaufende Sicherung (Vorschauversion) auf eine bestimmte Rolle oder einen Prinzipal isolieren und einschränken. Der Besitzer des Kontos kann eine Wiederherstellung auslösen und anderen Prinzipalen eine Rolle zuweisen, damit diese den Wiederherstellungsvorgang ausführen können. Diese Berechtigungen können im Abonnementbereich oder präziser im Quellkontobereich angewendet werden, wie in der folgenden Abbildung dargestellt:

:::image type="content" source="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" alt-text="Liste der zum Ausführen des Wiederherstellungsvorgangs erforderlichen Rollen." lightbox="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" border="false":::

Bei einem Bereich handelt es sich um einen Satz von Ressourcen, die Zugriff haben. Weitere Informationen zu Bereichen finden Sie in der [Azure RBAC](../role-based-access-control/scope-overview.md)-Dokumentation. In Azure Cosmos DB stellen das Quellabonnement und das Datenbankkonto die anwendbaren Bereiche für die meisten Anwendungsfälle dar. Der Prinzipal, der die Wiederherstellungsaktionen ausführt, muss über Schreibberechtigungen für die Zielressourcengruppe verfügen.

## <a name="assign-roles-for-restore-using-the-azure-portal"></a>Zuweisen von Rollen für die Wiederherstellung im Azure-Portal

Um eine Wiederherstellung ausführen zu können, benötigt ein Benutzer oder ein Prinzipal die Berechtigung zum Wiederherstellen (d. h. die Berechtigung „restore/action“) und die Berechtigung zum Bereitstellen eines neuen Kontos (die Berechtigung „write“).  Um diese Berechtigungen zu erteilen, kann der Besitzer einem Prinzipal die integrierten Rollen „CosmosRestoreOperator“ und „Cosmos DB-Operator“ zuweisen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Navigieren Sie zu Ihrem Abonnement, wechseln Sie zur Registerkarte **Zugriffssteuerung (IAM)** , und wählen Sie **Hinzufügen** > **Rollenzuweisung hinzufügen** aus.

1. Wählen Sie im Bereich **Rollenzuweisung hinzufügen** im Feld **Rolle** die Rolle **CosmosRestoreOperator** aus. Wählen Sie im Feld **Zugriff zuweisen für** den Eintrag **Benutzer, Gruppe oder Dienstprinzipal** aus, und suchen Sie nach dem Namen oder der E-Mail-ID eines Benutzers, wie in der folgenden Abbildung dargestellt:

   :::image type="content" source="./media/continuous-backup-restore-permissions/assign-restore-operator-roles.png" alt-text="Weisen Sie die Rollen „CosmosRestoreOperator“ und „Cosmos DB-Operator“ zu." border="true":::

1. Wählen Sie **Speichern** aus, um die Berechtigung „restore/action“ zu erteilen.

1. Wiederholen Sie Schritt 3 zum Zuweisen der Rolle **Cosmos DB-Operator**, um die Schreibberechtigung („write“) zu erteilen. Wenn Sie diese Rolle im Azure-Portal zuweisen, wird die Berechtigung zum Wiederherstellen für das gesamte Abonnement erteilt.

## <a name="permission-scopes"></a>Berechtigungsbereiche

|`Scope`  |Beispiel  |
|---------|---------|
|Subscription | /subscriptions/00000000-0000-0000-0000-000000000000 |
|Resource group | /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-cosmosdb-rg |
|Wiederherstellbare CosmosDB-Kontoressource | /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995|

Die wiederherstellbare Kontoressource kann aus der Ausgabe des Befehls `az cosmosdb restorable-database-account list --name <accountname>` (in der Befehlszeilenschnittstelle) oder des Cmdlets `Get-AzCosmosDBRestorableDatabaseAccount -DatabaseAccountName <accountname>` (in PowerShell) extrahiert werden. Das name-Attribut in der Ausgabe stellt die Instanz-ID („instanceID“) des wiederherstellbaren Kontos dar. Weitere Informationen finden Sie im Artikel zu [PowerShell](continuous-backup-restore-powershell.md) oder zur [Befehlszeilenschnittstelle](continuous-backup-restore-command-line.md).

## <a name="permissions"></a>Berechtigungen

Die folgenden Berechtigungen sind zum Ausführen der verschiedenen Aktivitäten im Zusammenhang mit der Wiederherstellung für Konten im fortlaufenden Sicherungsmodus erforderlich:

|Berechtigung  |Auswirkung  |Mindestumfang  |Maximaler Bereich  |
|---------|---------|---------|---------|
|Microsoft.Resources/deployments/validate/action, Microsoft.Resources/deployments/write | Diese Berechtigungen sind für die ARM-Vorlagenbereitstellung zum Erstellen des wiederhergestellten Kontos erforderlich. Informationen zum Festlegen dieser Rolle finden Sie unten in der Beispielberechtigung [RestorableAction](). | Nicht zutreffend | Nicht zutreffend  |
|Microsoft.DocumentDB/databaseAccounts/write | Diese Berechtigung ist erforderlich, um ein Konto in einer Ressourcengruppe wiederherstellen zu können. | Die Ressourcengruppe, unter der das wiederhergestellte Konto erstellt wird. | Das Abonnement, unter dem das wiederhergestellte Konto erstellt wird. |
|Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action |Diese Berechtigung ist für den Bereich des wiederherstellbaren Datenbankquellkontos erforderlich, damit Wiederherstellungsaktionen dafür ausgeführt werden können.  | Die „RestorableDatabaseAccount“-Ressource, die zum wiederherzustellenden Quellkonto gehört. Dieser Wert wird auch durch die Eigenschaft „ID“ der wiederherstellbaren Datenbankkontoressource angegeben. Ein Beispiel für ein wiederherstellbares Konto ist `/subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/<guid-instanceid>`. | Das Abonnement, das das wiederherstellbare Datenbankkonto enthält. Die Ressourcengruppe kann nicht als Bereich ausgewählt werden.  |
|Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read |Diese Berechtigung ist für den Bereich des wiederherstellbaren Datenbankquellkontos erforderlich, um die wiederherstellbaren Datenbankkonten auflisten zu können.  | Die „RestorableDatabaseAccount“-Ressource, die zum wiederherzustellenden Quellkonto gehört. Dieser Wert wird auch durch die Eigenschaft „ID“ der wiederherstellbaren Datenbankkontoressource angegeben. Ein Beispiel für ein wiederherstellbares Konto ist `/subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/<guid-instanceid>`.| Das Abonnement, das das wiederherstellbare Datenbankkonto enthält. Die Ressourcengruppe kann nicht als Bereich ausgewählt werden.  |
|Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read | Diese Berechtigung ist für den Bereich des wiederherstellbaren Quellkontos erforderlich, um das Lesen von wiederherstellbaren Ressourcen wie der Liste der Datenbanken und Container für ein wiederherstellbares Konto zuzulassen.  | Die „RestorableDatabaseAccount“-Ressource, die zum wiederherzustellenden Quellkonto gehört. Dieser Wert wird auch durch die Eigenschaft „ID“ der wiederherstellbaren Datenbankkontoressource angegeben. Ein Beispiel für ein wiederherstellbares Konto ist `/subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/regionName/restorableDatabaseAccounts/<guid-instanceid>`.| Das Abonnement, das das wiederherstellbare Datenbankkonto enthält. Die Ressourcengruppe kann nicht als Bereich ausgewählt werden. |

## <a name="azure-cli-role-assignment-scenarios-to-restore-at-different-scopes"></a>Azure CLI-Rollenzuweisungsszenarien für die Wiederherstellung in unterschiedlichen Bereichen

Rollen mit Berechtigungen können verschiedenen Bereichen zugewiesen werden, um eine präzise Kontrolle darüber zu erhalten, wer den Wiederherstellungsvorgang innerhalb eines Abonnements oder eines bestimmten Kontos ausführen kann.

### <a name="assign-capability-to-restore-from-any-restorable-account-in-a-subscription"></a>Zuweisen der Fähigkeit zur Wiederherstellung von einem beliebigen wiederherstellbaren Konto in einem Abonnement

Zuweisen der integrierten Rolle `CosmosRestoreOperator` auf Abonnementebene

```azurecli-interactive
az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope /subscriptions/<subscriptionId>
```

### <a name="assign-capability-to-restore-from-a-specific-account"></a>Zuweisen der Fähigkeit zur Wiederherstellung von einem bestimmten Konto

* Weisen Sie einem Benutzer eine Schreibaktion für die entsprechende Ressourcengruppe zu. Diese Aktion ist erforderlich, um ein neues Konto in der Ressourcengruppe erstellen zu können.

* Weisen Sie dem entsprechenden wiederherstellbaren Datenbankkonto, das wiederhergestellt werden muss, die integrierte Rolle „CosmosRestoreOperator“ zu. Im folgenden Befehl wird der Bereich für das „RestorableDatabaseAccount“ aus der Eigenschaft „ID“ in der Ausgabe von `az cosmosdb restorable-database-account` (bei Verwendung der Befehlszeilenschnittstelle) oder `Get-AzCosmosDBRestorableDatabaseAccount` (bei Verwendung von PowerShell) abgerufen.

  ```azurecli-interactive
   az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope <RestorableDatabaseAccount>
  ```

### <a name="assign-capability-to-restore-from-any-source-account-in-a-resource-group"></a>Zuweisen der Fähigkeit zur Wiederherstellung von einem beliebigen Quellkonto in einer Ressourcengruppe
Dieser Vorgang wird derzeit nicht unterstützt.

## <a name="custom-role-creation-for-restore-action-with-cli"></a>Erstellen von benutzerdefinierten Rollen für Wiederherstellungsaktionen mit der CLI

Der Abonnementbesitzer kann die Berechtigung zum Wiederherstellen für jede andere Azure AD-Identität bereitstellen. Die Berechtigung zum Wiederherstellen basiert auf der Aktion „Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action“. Und sie sollte in die entsprechende „restore“-Berechtigung eingeschlossen werden. Es gibt eine integrierte Rolle namens „CosmosRestoreOperator“, die diese Rolle enthält. Sie können die Berechtigung entweder mithilfe dieser integrierten Rolle zuweisen oder eine benutzerdefinierte Rolle erstellen.

Die folgende Rolle „RestorableAction“ stellt eine benutzerdefinierte Rolle dar. Sie müssen diese Rolle explizit erstellen. Mit der folgenden JSON-Vorlage wird eine benutzerdefinierte Rolle „RestorableAction“ mit der Berechtigung zum Wiederherstellen erstellt:

```json
{
  "assignableScopes": [
    "/subscriptions/23587e98-b6ac-4328-a753-03bcd3c8e744"
  ],
  "description": "Can do a restore request for any Cosmos DB database account with continuous backup",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/deployments/validate/action",
        "Microsoft.DocumentDB/databaseAccounts/write",
        "Microsoft.Resources/deployments/write",  
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "Name": "RestorableAction",
    "roleType": "CustomRole"
}
```

Verwenden Sie als Nächstes den folgenden Vorlagenbereitstellungsbefehl, um mithilfe der ARM-Vorlage eine Rolle mit der Berechtigung zum Wiederherstellen zu erstellen:

```azurecli-interactive
az role definition create --role-definition <JSON_Role_Definition_Path>
```

## <a name="next-steps"></a>Nächste Schritte

* Konfigurieren und verwalten Sie die fortlaufende Sicherung im [Azure-Portal](continuous-backup-restore-portal.md), mithilfe von [PowerShell](continuous-backup-restore-powershell.md), [Azure CLI](continuous-backup-restore-command-line.md) oder [Azure Resource Manager](continuous-backup-restore-template.md).
* [Ressourcenmodell des fortlaufenden Sicherungsmodus](continuous-backup-restore-resource-model.md)
