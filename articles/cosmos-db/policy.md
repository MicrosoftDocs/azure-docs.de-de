---
title: Verwenden von Azure Policy zum Implementieren von Governance und Kontrollen für Azure Cosmos DB-Ressourcen
description: Erfahren Sie, wie Sie Azure Policy zum Implementieren von Governance und Kontrollen für Azure Cosmos DB-Ressourcen verwenden.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 1390f5db6e0f0370788bef60d5a2cafee1e8a96d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93080650"
---
# <a name="use-azure-policy-to-implement-governance-and-controls-for-azure-cosmos-db-resources"></a>Verwenden von Azure Policy zum Implementieren von Governance und Kontrollen für Azure Cosmos DB-Ressourcen
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

[Azure Policy](../governance/policy/overview.md) unterstützt das Erzwingen von Governancestandards für eine Organisation, mit denen die Ressourcencompliance bewertet und eine automatische Korrektur implementiert werden kann. Zu den gängigen Anwendungsfällen gehören Sicherheit, Kostenverwaltung und Konfigurationskonsistenz.

Azure Policy bietet integrierte Richtliniendefinitionen. Sie können benutzerdefinierte Richtliniendefinitionen für Szenarien erstellen, die nicht durch die integrierten Richtliniendefinitionen abgedeckt werden. Weitere Informationen finden Sie in der [Dokumentation zu Azure Policy](../governance/policy/overview.md).

> [!IMPORTANT]
> Azure Policy wird auf Ressourcenanbieterebene für Azure-Dienste erzwungen. Über Cosmos DB-SDKs können die meisten Verwaltungsvorgänge für Datenbank-, Container- und Durchsatzressourcen ausgeführt werden. Diese umgehen den Cosmos DB-Ressourcenanbieter, sodass alle Richtlinien ignoriert werden, die mit Azure Policy erstellt wurden. Informationen zur Erzwingung von Richtlinien finden Sie unter [Verhindern von Änderungen aus den Azure Cosmos DB-SDKs](role-based-access-control.md#prevent-sdk-changes).

## <a name="assign-a-built-in-policy-definition"></a>Zuweisen einer integrierten Richtliniendefinition

Richtliniendefinitionen beschreiben Bedingungen für die Ressourcencompliance und die Maßnahmen, die ergriffen werden, wenn eine Bedingung erfüllt ist. _Richtlinienzuweisungen_ werden aus _Richtliniendefinitionen_ erstellt. Sie können integrierte oder benutzerdefinierte Richtliniendefinitionen für Ihre Azure Cosmos DB-Ressourcen verwenden. Richtlinienzuweisungen gelten für eine Azure-Verwaltungsgruppe, ein Azure-Abonnement oder eine Ressourcengruppe und werden innerhalb des ausgewählten Bereichs auf die Ressourcen angewandt. Optional können Sie bestimmte Ressourcen aus dem Bereich ausschließen.

Sie können Richtlinienzuweisungen mit dem [Azure-Portal](../governance/policy/assign-policy-portal.md), [Azure PowerShell](../governance/policy/assign-policy-powershell.md), der [Azure-Befehlszeilenschnittstelle](../governance/policy/assign-policy-azurecli.md) oder [ARM-Vorlagen](../governance/policy/assign-policy-template.md) erstellen.

Um eine Richtlinienzuweisung aus einer integrierten Richtliniendefinition für Azure Cosmos DB zu erstellen, führen Sie die Schritte im Artikel [Erstellen einer Richtlinienzuweisung mit dem Azure-Portal](../governance/policy/assign-policy-portal.md) aus.

Geben Sie im Schritt zur Auswahl der Richtliniendefinition im Suchfeld `Cosmos DB` ein, um die Liste der verfügbaren integrierten Richtliniendefinitionen zu filtern. Wählen Sie eine der verfügbaren integrierten Richtliniendefinitionen und dann **Auswählen** aus, um die Erstellung der Richtlinienzuweisung fortzusetzen.

> [!TIP]
> Sie können auch die integrierten Richtliniendefinitionsnamen, die im Bereich **Verfügbare Definitionen** angezeigt werden, mit Azure PowerShell, der Azure-Befehlszeilenschnittstelle oder ARM-Vorlagen verwenden, um Richtlinienzuweisungen zu erstellen.

:::image type="content" source="./media/policy/available-definitions.png" alt-text="Suchen integrierter Richtliniendefinitionen für Azure Cosmos DB":::

## <a name="create-a-custom-policy-definition"></a>Erstellen einer benutzerdefinierten Richtliniendefinition

Für bestimmte Szenarien, die nicht durch die integrierten Richtliniendefinitionen abgedeckt werden, können Sie eine [benutzerdefinierte Richtliniendefinition](../governance/policy/tutorials/create-custom-policy-definition.md) erstellen. Später erstellen Sie aus Ihrer benutzerdefinierten _Richtliniendefinition_ eine _Richtlinienzuweisung_.

### <a name="property-types-and-property-aliases-in-policy-rules"></a>Eigenschaftstypen und Eigenschaftenaliase in Richtlinienregeln

Verwenden Sie die [Schritte für benutzerdefinierte Richtliniendefinitionen](../governance/policy/tutorials/create-custom-policy-definition.md), um die Ressourceneigenschaften und Eigenschaftenaliase zu ermitteln, die zum Erstellen von Richtlinienregeln erforderlich sind.

Um spezifische Eigenschaftenaliase für Azure Cosmos DB zu identifizieren, verwenden Sie den Namespace `Microsoft.DocumentDB` mit einer der Methoden, die im Anleitungsartikel zu benutzerdefinierten Richtliniendefinitionen angezeigt werden.

#### <a name="use-the-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle:
```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for namespace Microsoft.DocumentDB
az provider show --namespace Microsoft.DocumentDB --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

#### <a name="use-azure-powershell"></a>Verwenden von Azure PowerShell:
```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.DocumentDB namespace
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.DocumentDB').Aliases
```

Mit diesen Befehlen geben Sie die Liste der Eigenschaftenaliasnamen für eine Azure Cosmos DB-Eigenschaft aus. Im Folgenden finden Sie einen Auszug aus der Ausgabe:

```json
[
  "Microsoft.DocumentDB/databaseAccounts/sku.name",
  "Microsoft.DocumentDB/databaseAccounts/virtualNetworkRules[*]",
  "Microsoft.DocumentDB/databaseAccounts/virtualNetworkRules[*].id",
  "Microsoft.DocumentDB/databaseAccounts/isVirtualNetworkFilterEnabled",
  "Microsoft.DocumentDB/databaseAccounts/consistencyPolicy.defaultConsistencyLevel",
  "Microsoft.DocumentDB/databaseAccounts/enableAutomaticFailover",
  "Microsoft.DocumentDB/databaseAccounts/Locations",
  "Microsoft.DocumentDB/databaseAccounts/Locations[*]",
  "Microsoft.DocumentDB/databaseAccounts/Locations[*].locationName",
  "..."
]
```

Sie können jeden dieser Eigenschaftenaliasnamen in den [benutzerdefinierten Richtliniendefinitionsregeln](../governance/policy/tutorials/create-custom-policy-definition.md#policy-rule) verwenden.

Im Folgenden finden Sie eine Beispielrichtliniendefinition, die prüft, ob ein Azure Cosmos DB-Konto für mehrere Schreibstandorte konfiguriert ist. Die benutzerdefinierte Richtliniendefinition umfasst zwei Regeln: Eine zur Prüfung auf den bestimmten Typ des Eigenschaftsalias und die zweite zur Prüfung auf die bestimmte Eigenschaft des Typs. In diesem Fall das Feld, in dem die Einstellung für mehrere Schreibstandorte gespeichert wird. Beide Regeln verwenden die Aliasnamen.

```json
"policyRule": {
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.DocumentDB/databaseAccounts"
      },
      {
        "field": "Microsoft.DocumentDB/databaseAccounts/enableMultipleWriteLocations",
        "notEquals": true
      }
    ]
  },
  "then": {
    "effect": "Audit"
  }
}
```

Mithilfe benutzerdefinierter Richtliniendefinitionen können Sie Richtlinienzuweisungen erstellen. Die Verwendung entspricht der für integrierte Richtliniendefinitionen.

## <a name="policy-compliance"></a>Richtlinienkonformität

Nach dem Erstellen der Richtlinienzuweisungen wertet Azure Policy die Ressourcen im Bereich der Zuweisung aus. Dabei wird die _Compliance_ der einzelnen Ressourcen mit dieser Richtlinie bewertet. Die in der Richtlinie angegebenen _Maßnahmen_ werden dann auf nicht konforme Ressourcen angewandt.

Sie können die Complianceergebnisse und Details zu den Korrekturmaßnahmen im [Azure-Portal](../governance/policy/how-to/get-compliance-data.md#portal), über die [Azure-Befehlszeilenschnittstelle](../governance/policy/how-to/get-compliance-data.md#command-line) oder in [Azure Monitor-Protokollen](../governance/policy/how-to/get-compliance-data.md#azure-monitor-logs) überprüfen.

Der folgende Screenshot zeigt zwei Beispiele für Richtlinienzuweisungen.

Eine Zuweisung basiert auf einer integrierten Richtliniendefinition, die überprüft, ob die Azure Cosmos DB-Ressourcen nur in den zulässigen Azure-Regionen bereitgestellt werden. Ressourcenkonformität zeigt das Ergebnis der Richtlinienauswertung (konform oder nicht konform) für Ressourcen innerhalb des Geltungsbereichs.

Die andere Zuweisung basiert auf einer benutzerdefinierten Richtliniendefinition. Bei dieser Zuordnung wird geprüft, ob Cosmos DB-Konten für mehrere Schreibstandorte konfiguriert sind.

Nach der Bereitstellung der Richtlinienzuweisungen werden die Auswertungsergebnisse im Compliance-Dashboard angezeigt. Beachten Sie, dass dies nach der Bereitstellung einer Richtlinienzuweisung bis zu 30 Minuten dauern kann. Darüber hinaus können [Prüfungen zur Regelauswertung bei Bedarf gestartet werden](../governance/policy/how-to/get-compliance-data.md#on-demand-evaluation-scan), unmittelbar nach der Erstellung von Richtlinienzuweisungen.

Der Screenshot zeigt die folgenden Ergebnisse der Konformitätsauswertung für Azure Cosmos DB-Konten im Gültigkeitsbereich:

- Null von zwei Konten entsprechen einer Richtlinie, dass die Virtual Network-Filterung (VNet) konfiguriert werden muss.
- Null von zwei Konten entsprechen einer Richtlinie, die erfordert, dass das Konto für mehrere Schreibstandorte konfiguriert sein muss.
- Null von zwei Konten entsprechen einer Richtlinie, wonach Ressourcen in zulässigen Azure-Regionen bereitgestellt wurden.

:::image type="content" source="./media/policy/compliance.png" alt-text="Aufgeführte Konformitätsergebnisse für Azure Policy-Zuweisungen":::

Informationen zum Korrigieren nicht konformer Ressourcen finden Sie im Artikel zum [Korrigieren von Ressourcen mit Azure Policy](../governance/policy/how-to/remediate-resources.md).

## <a name="next-steps"></a>Nächste Schritte

- [Überprüfen Sie benutzerdefinierte Richtliniendefinitionen für Azure Cosmos DB](https://github.com/Azure/azure-policy/tree/master/samples/CosmosDB), einschließlich der oben gezeigten Richtlinien für mehrere Schreibstandorte und VNet-Filterung.
- [Erstellen einer Richtlinienzuweisung im Azure-Portal](../governance/policy/assign-policy-portal.md)
- [Überprüfen der integrierten Azure Policy-Richtliniendefinitionen für Azure Cosmos DB](./policy-reference.md)