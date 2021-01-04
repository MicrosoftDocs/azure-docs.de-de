---
title: 'Tutorial: Erstellen von Richtlinien zur Konformitätserzwingung'
description: In diesem Tutorial verwenden Sie Richtlinien zum Erzwingen von Standards, Steuern der Kosten, Gewährleisten der Sicherheit und Erzwingen von unternehmensweiten Entwurfsprinzipien.
ms.date: 10/05/2020
ms.topic: tutorial
ms.openlocfilehash: bf3da82abcdcada1fc38df29efc988a1805c3020
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005434"
---
# <a name="tutorial-create-and-manage-policies-to-enforce-compliance"></a>Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung

Zur Einhaltung Ihrer Unternehmensstandards und Vereinbarungen zum Servicelevel müssen Sie mit der Erstellung und Verwaltung von Richtlinien in Azure vertraut sein. In diesem Tutorial erfahren Sie, wie Sie mithilfe von Azure Policy einige allgemeinere Aufgaben im Zusammenhang mit der organisationsweiten Erstellung, Zuweisung und Verwaltung von Richtlinien durchführen:

> [!div class="checklist"]
> - Zuweisen einer Richtlinie zur Erzwingung einer Bedingung für die spätere Erstellung von Ressourcen
> - Erstellen und Zuweisen einer Initiativdefinition zur Nachverfolgung der Konformität für mehrere Ressourcen
> - Beheben von Problemen mit einer nicht konformen oder abgelehnten Ressource
> - Implementieren einer neuen Richtlinie in der gesamten Organisation

Lesen Sie die Schnellstartartikel, wenn Sie eine Richtlinie zum Ermitteln des aktuellen Konformitätszustands Ihrer vorhandenen Ressourcen zuweisen möchten.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="assign-a-policy"></a>Zuweisen einer Richtlinie

Für die Konformitätserzwingung mit Azure Policy muss zunächst eine Richtliniendefinition zugewiesen werden. Eine Richtliniendefinition definiert, unter welcher Bedingung eine Richtlinie erzwungen wird und welche Auswirkung sie haben soll. In diesem Beispiel weisen Sie die integrierte Richtliniendefinition _Tag von der Ressourcengruppe erben, falls nicht vorhanden_ zu, um das angegebene Tag mit seinem Wert von der übergeordneten Ressourcengruppe den neuen oder aktualisierten Ressourcen hinzuzufügen, denen das Tag fehlt.

1. Navigieren Sie zum Azure-Portal, um Richtlinien zuzuweisen. Suchen Sie nach **Policy**, und wählen Sie diese Option aus.

   :::image type="content" source="../media/create-and-manage/search-policy.png" alt-text="Screenshot: Suchen nach „Policy“ über die Suchleiste" border="false":::

1. Wählen Sie links auf der Seite „Azure Policy“ die Option **Zuweisungen**. Eine Zuweisung ist eine zugewiesene Richtlinie, die innerhalb eines bestimmten Bereichs angewendet werden soll.

   :::image type="content" source="../media/create-and-manage/select-assignments.png" alt-text="Screenshot: Auswählen des Knotens „Zuweisungen“ auf der Übersichtsseite für Richtlinien" border="false":::

1. Wählen Sie im oberen Bereich der Seite **Richtlinien – Zuweisungen** die Option **Richtlinie zuweisen**.

   :::image type="content" source="../media/create-and-manage/select-assign-policy.png" alt-text="Screenshot: Auswählen der Schaltfläche „Richtlinie zuweisen“ auf der Seite „Zuweisungen“" border="false":::

1. Wählen Sie auf der Seite **Richtlinie zuweisen** auf der Registerkarte **Grundlagen** unter **Bereich** den Bereich aus, indem Sie die Auslassungspunkte und entweder eine Verwaltungsgruppe oder ein Abonnement auswählen. Wählen Sie optional eine Ressourcengruppe aus. Ein Bereich bestimmt, für welche Ressourcen oder Ressourcengruppe die Richtlinienzuweisung erzwungen wird.
   Wählen Sie anschließend unten auf der Seite **Bereich** die Option **Auswählen** aus.

   In diesem Beispiel wird das Abonnement **Contoso** verwendet. Ihr Abonnement wird sich davon unterscheiden.

1. Ressourcen können basierend auf dem **Bereich** ausgeschlossen werden. **Ausschlüsse** beginnen auf einer Ebene unterhalb der Ebene des **Bereichs**. **Ausschlüsse** sind optional, lassen Sie sie daher vorerst leer.

1. Wählen Sie die **Richtliniendefinition** mit den Auslassungspunkten, um die Liste der verfügbaren Definitionen zu öffnen. Sie können den **Typ** der Richtliniendefinition nach _Integriert_ filtern, um alle Definitionen anzuzeigen und ihre Beschreibungen zu lesen.

1. Wählen Sie **Tag von der Ressourcengruppe erben, falls nicht vorhanden** aus. Sollten Sie die Option nicht direkt finden, geben Sie **Tag erben** in das Suchfeld ein, und drücken Sie dann die EINGABETASTE, oder wählen Sie die entsprechende Option im Suchfeld aus.
   Wählen Sie unten auf der Seite **Verfügbare Definitionen** die Option **Auswählen** aus, nachdem Sie die Richtliniendefinition gefunden und ausgewählt haben.

   :::image type="content" source="../media/create-and-manage/select-available-definition.png" alt-text="Screenshot: Suchfilter beim Auswählen einer Richtliniendefinition":::

1. Der **Zuweisungsname** wird automatisch mit dem ausgewählten Richtliniennamen gefüllt, kann aber geändert werden. Übernehmen Sie in diesem Fall _Tag von der Ressourcengruppe erben, falls nicht vorhanden_. Geben Sie ggf. auch eine **Beschreibung** ein. Die Beschreibung enthält Details zu dieser Richtlinienzuweisung.

1. Behalten Sie für **Richtlinienerzwingung** die Einstellung _Aktiviert_ bei. Wenn Sie diese Einstellung auf _Deaktiviert_ festlegen, kann das Ergebnis der Richtlinie getestet werden, ohne die Auswirkung auszulösen. Weitere Informationen finden Sie unter [Erzwingungsmodus](../concepts/assignment-structure.md#enforcement-mode).

1. Das Feld **Zugewiesen von** wird abhängig vom angemeldeten Benutzer automatisch ausgefüllt. Dieses Feld ist optional. Daher können auch benutzerdefinierte Werte eingegeben werden.

1. Wählen Sie im oberen Bereich des Assistenten die Registerkarte **Parameter** aus.

1. Geben Sie unter **Tagname** den Namen _Umgebung_ ein.

1. Wählen Sie im oberen Bereich des Assistenten die Registerkarte **Bereinigung** aus.

1. Lassen Sie das Kontrollkästchen **Korrekturtask erstellen** deaktiviert. Dieses Kontrollkästchen ermöglicht die Erstellung eines Tasks zur Änderung bereits vorhandener Ressourcen (zusätzlich zu neuen oder aktualisierten Ressourcen). Weitere Informationen finden Sie unter [Korrigieren nicht konformer Ressourcen mit Azure Policy](../how-to/remediate-resources.md).

1. **Verwaltete Identität erstellen** ist automatisch aktiviert, da diese Richtliniendefinition die Auswirkung [Modify](../concepts/effects.md#modify) verwendet. **Berechtigungen** ist basierend auf der Richtliniendefinition automatisch auf _Mitwirkende_ festgelegt. Weitere Informationen finden Sie unter [Verwaltete Identitäten](../../../active-directory/managed-identities-azure-resources/overview.md) und [Funktionsweise der Wiederherstellungssicherheit](../how-to/remediate-resources.md#how-remediation-security-works).

1. Wählen Sie im oberen Bereich des Assistenten die Registerkarte **Überprüfen + erstellen** aus.

1. Überprüfen Sie die ausgewählten Optionen, und wählen Sie anschließend am unteren Rand der Seite die Option **Erstellen** aus.

## <a name="implement-a-new-custom-policy"></a>Implementieren einer neuen benutzerdefinierten Richtlinie

Sie haben eine integrierte Richtliniendefinition zugewiesen und können nun weitere Aktionen mit Azure Policy ausführen. Erstellen Sie als Nächstes eine neue benutzerdefinierte Richtlinie zur Kosteneinsparung, die durch eine Überprüfung sicherstellt, dass in Ihrer Umgebung keine virtuellen Computer der G-Serie erstellt werden können. Wenn also ein Benutzer in Ihrer Organisation versucht, einen virtuellen Computer der G-Serie zu erstellen, wird die Anforderung abgelehnt.

1. Wählen Sie links auf der Seite „Azure Policy“ unter **Erstellung** die Option **Definitionen**.

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="Screenshot: Seite „Definitionen“ unter der Gruppe „Erstellung“" border="false":::

1. Wählen Sie oben auf der Seite **+ Richtliniendefinition** aus. Über diese Schaltfläche gelangen Sie zur Seite **Richtliniendefinition**.

1. Geben Sie Folgendes ein:

   - Die Verwaltungsgruppe oder das Abonnement, in der bzw. in dem die Richtliniendefinition gespeichert ist. Wählen Sie über die Auslassungspunkte bei **Speicherort der Definition** aus.

     > [!NOTE]
     > Wenn Sie diese Richtliniendefinition auf mehrere Abonnements anwenden möchten, muss der Speicherort eine Verwaltungsgruppe sein, die die Abonnements enthält, denen Sie die Richtlinie zuweisen. Dasselbe gilt für eine Initiativdefinition.

   - Den Namen der Richtliniendefinition: _Require VM SKUs not in the G series_ (VM-SKUs dürfen nicht der G-Serie angehören)
   - Den Zweck der Richtliniendefinition: _This policy definition enforces that all virtual machines created in this scope have SKUs other than the G series to reduce cost._ (Diese Richtliniendefinition dient zur Kostensenkung und bewirkt, dass alle virtuellen Computer, die in diesem Bereich erstellt werden, andere SKUs als die der G-Serie aufweisen.)
   - Wählen Sie eine der vorhandenen Optionen (etwa _Compute_) aus, oder erstellen Sie für diese Richtliniendefinition eine neue Kategorie.
   - Kopieren Sie den folgenden JSON-Code, und aktualisieren Sie ihn dann Ihren Anforderungen entsprechend mit folgenden Angaben:
      - Richtlinienparameter
      - Richtlinienregeln/-bedingungen (in unserem Fall: VM-SKU-Größe gleich G-Serie)
      - Wirkung der Richtlinie (in unserem Fall: **deny**, also „Ablehnen“)

   Der JSON-Code sollte wie folgt aussehen. Fügen Sie den überarbeiteten Code in das Azure-Portal ein.

   ```json
   {
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Compute/virtualMachines"
                   },
                   {
                       "field": "Microsoft.Compute/virtualMachines/sku.name",
                       "like": "Standard_G*"
                   }
               ]
           },
           "then": {
               "effect": "deny"
           }
       }
   }
   ```

   Die Eigenschaft _field_ in der Richtlinienregel muss auf einen unterstützten Wert festgelegt sein. Eine vollständige Liste der Werte finden Sie im Artikel „Struktur von Azure Policy-Definitionen“ unter [Felder](../concepts/definition-structure.md#fields). Ein Beispiel für einen Alias wäre `"Microsoft.Compute/VirtualMachines/Size"`.

   Weitere Azure Policy-Beispiele finden Sie unter [Azure Policy-Beispiele](../samples/index.md).

1. Wählen Sie **Speichern** aus.

## <a name="create-a-policy-definition-with-rest-api"></a>Erstellen einer Richtliniendefinition mit der REST-API

Sie können eine Richtlinie mit der REST-API für Azure Policy-Definitionen erstellen. Die REST-API ermöglicht es Ihnen, Richtliniendefinitionen zu erstellen und zu löschen sowie Informationen zu vorhandenen Definitionen abzurufen. Um eine Richtliniendefinition zu erstellen, verwenden Sie das folgende Beispiel:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

Nehmen Sie einen Anforderungstext auf, der dem im folgenden Beispiel dargestellten ähnelt:

```json
{
    "properties": {
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                }
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

## <a name="create-a-policy-definition-with-powershell"></a>Erstellen einer Richtliniendefinition mit PowerShell

Vergewissern Sie sich, dass Sie die neueste Version des Az-Moduls von Azure PowerShell installiert haben, bevor Sie mit dem PowerShell-Beispiel fortfahren.

Sie können eine Richtliniendefinition über das Cmdlet `New-AzPolicyDefinition` erstellen.

Um eine Richtliniendefinition auf der Grundlage einer Datei zu erstellen, übergeben Sie den Pfad zur Datei. Verwenden Sie das folgende Beispiel für eine externe Datei:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -DisplayName 'Deny cool access tiering for storage' `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

Verwenden Sie das folgende Beispiel für eine lokale Datei:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -Description 'Deny cool access tiering for storage' `
    -Policy 'c:\policies\coolAccessTier.json'
```

Verwenden Sie zum Erstellen einer Richtliniendefinition mit einer Inline-Regel das folgende Beispiel:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name 'denyCoolTiering' -Description 'Deny cool access tiering for storage' -Policy '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

Die Ausgabe wird in einem `$definition`-Objekt gespeichert, das während der Richtlinienzuweisung verwendet wird. Im folgenden Beispiel wird eine Richtliniendefinition mit Parametern erstellt:

```azurepowershell-interactive
$policy = '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of locations that can be specified when deploying storage accounts.",
            "strongType": "location",
            "displayName": "Allowed locations"
        }
    }
}'

$definition = New-AzPolicyDefinition -Name 'storageLocations' -Description 'Policy to specify locations for storage accounts.' -Policy $policy -Parameter $parameters
```

### <a name="view-policy-definitions-with-powershell"></a>Anzeigen von Richtliniendefinitionen mit PowerShell

Verwenden Sie den folgenden Befehl, um alle Richtliniendefinitionen in Ihrem Abonnement anzuzeigen:

```azurepowershell-interactive
Get-AzPolicyDefinition
```

Es gibt alle verfügbaren Richtliniendefinitionen, einschließlich integrierten Richtlinien, zurück. Jede Richtlinie wird im folgenden Format zurückgeben:

```output
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

## <a name="create-a-policy-definition-with-azure-cli"></a>Erstellen einer Richtliniendefinition mit Azure CLI

Sie können eine Richtliniendefinition mit dem Befehl `az policy definition` über die Azure-Befehlszeilenschnittstelle erstellen. Verwenden Sie zum Erstellen einer Richtliniendefinition mit einer Inline-Regel das folgende Beispiel:

```azurecli-interactive
az policy definition create --name 'denyCoolTiering' --description 'Deny cool access tiering for storage' --rules '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

### <a name="view-policy-definitions-with-azure-cli"></a>Anzeigen von Richtliniendefinitionen mit Azure CLI

Verwenden Sie den folgenden Befehl, um alle Richtliniendefinitionen in Ihrem Abonnement anzuzeigen:

```azurecli-interactive
az policy definition list
```

Es gibt alle verfügbaren Richtliniendefinitionen, einschließlich integrierten Richtlinien, zurück. Jede Richtlinie wird im folgenden Format zurückgeben:

```json
{
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",
    "displayName": "Allowed locations",
    "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "policyRule": {
        "if": {
            "not": {
                "field": "location",
                "in": "[parameters('listOfAllowedLocations')]"
            }
        },
        "then": {
            "effect": "Deny"
        }
    },
    "policyType": "BuiltIn"
}
```

## <a name="create-and-assign-an-initiative-definition"></a>Erstellen und Zuweisen einer Initiativdefinition

Mit einer Initiativdefinition können Sie mehrere Richtliniendefinitionen zu einer Gruppe zusammenfassen, um ein übergeordnetes Ziel zu erreichen. Eine Initiative wertet Ressourcen im Bereich der Zuweisung bezüglich der Einhaltung der enthaltenen Richtlinien aus. Weitere Informationen zu Initiativdefinitionen finden Sie in der [Übersicht über Azure Policy](../overview.md).

### <a name="create-an-initiative-definition"></a>Erstellen einer Initiativdefinition

1. Wählen Sie links auf der Seite „Azure Policy“ unter **Erstellung** die Option **Definitionen**.

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="Screenshot: Seite „Definitionen“ unter der Gruppe „Erstellung“":::

1. Klicken Sie im oberen Bereich der Seite auf **+ Initiativendefinition**, um zum Assistenten **Initiativendefinition** zu gelangen.

   :::image type="content" source="../media/create-and-manage/initiative-definition.png" alt-text="Screenshot: Seite „Initiativendefinition“ und festzulegende Eigenschaften":::

1. Verwenden Sie die Auslassungspunkte für **Standort der Initiative**, um eine Verwaltungsgruppe oder ein Abonnement zum Speichern der Definition auszuwählen. Wenn die vorherige Seite auf eine einzelne Verwaltungsgruppe oder ein einzelnes Abonnement ausgerichtet war, wird **Standort der Initiative** automatisch aufgefüllt.

1. Geben Sie **Name** und **Beschreibung** der Initiative ein.

   Mit diesem Beispiel wird überprüft, ob Ressourcen mit Richtliniendefinitionen zur Verbesserung der Sicherheit konform sind. Geben Sie für die Initiative den Namen **Get Secure** (Sicherheit verbessern) ein, und legen Sie die Beschreibung wie folgt fest: **This initiative has been created to handle all policy definitions associated with securing resources.** (Diese Initiative gilt für alle Richtliniendefinitionen, die mit dem Schutz von Ressourcen in Zusammenhang stehen.)

1. Wählen Sie für **Kategorie** eine der vorhandenen Optionen, oder erstellen Sie eine neue Kategorie.

1. Legen Sie unter **Version** eine Version für die Initiative fest (beispielsweise _1.0_).

   > [!NOTE]
   > Bei dem Versionswert handelt es sich lediglich um eine Metadatenangabe. Er wird vom Azure Policy-Dienst nicht für Updates oder für Prozesse verwendet.

1. Wählen Sie unten auf der Seite die Option **Weiter** oder im oberen Bereich des Assistenten die Registerkarte **Richtlinien** aus.

1. Wählen Sie die Schaltfläche **Richtliniendefinition(en) hinzufügen** aus, und blättern Sie durch die Liste. Wählen Sie die Richtliniendefinitionen aus, die Sie dieser Initiative hinzufügen möchten. Fügen Sie für die Initiative **Get Secure** (Sicherheit verbessern) die folgenden integrierten Richtliniendefinitionen hinzu, indem Sie jeweils das Kontrollkästchen neben der Richtliniendefinition aktivieren:

   - Allowed locations (Zulässige Speicherorte)
   - Fehlenden Endpoint Protection-Schutz in Azure Security Center überwachen
   - Virtuelle Computer ohne Internetzugang sollten über Netzwerksicherheitsgruppen geschützt werden.
   - Azure Backup muss für Virtual Machines aktiviert sein.
   - Die Datenträgerverschlüsselung sollte auf virtuelle Computer angewendet werden.
   - „Tag für Ressourcen hinzufügen oder ersetzen“ (Fügen Sie diese Richtliniendefinition zweimal hinzu.)

   Nachdem Sie die einzelnen Richtliniendefinitionen in der Liste ausgewählt haben, können Sie am unteren Rand der Liste **Hinzufügen** auswählen.
   Da die Richtliniendefinition _Tag für Ressourcen hinzufügen oder ersetzen_ zweimal hinzugefügt wird, erhalten die einzelnen Instanzen jeweils eine andere _Referenz-ID_.

   :::image type="content" source="../media/create-and-manage/initiative-definition-2.png" alt-text="Screenshot: Ausgewählte Richtliniendefinitionen mit zugehöriger Referenz-ID und Gruppe auf der Seite „Initiativendefinition":::

   > [!NOTE]
   > Die ausgewählten Richtliniendefinitionen können Gruppen hinzugefügt werden. Wählen Sie hierzu mindestens eine hinzugefügte Definition und anschließend **Ausgewählte Richtlinien einer Gruppe hinzufügen** aus. Die Gruppe muss bereits vorhanden sein und kann über die Registerkarte **Gruppen** des Assistenten erstellt werden.

1. Wählen Sie unten auf der Seite die Option **Weiter** oder im oberen Bereich des Assistenten die Registerkarte **Gruppen** aus. Über diese Registerkarte können neue Gruppen hinzugefügt werden. In diesem Tutorial werden keine Gruppen hinzugefügt.

1. Wählen Sie unten auf der Seite die Option **Weiter** oder im oberen Bereich des Assistenten die Registerkarte **Initiativenparameter** aus. Wenn in der Initiative ein Parameter vorhanden sein soll, der an einzelne oder mehrere Richtliniendefinitionen übergeben werden kann, wird der Parameter hier definiert und anschließend auf der Registerkarte **Richtlinienparameter** verwendet. In diesem Tutorial werden keine Initiativenparameter hinzugefügt.

   > [!NOTE]
   > Nach dem Speichern in einer Initiativendefinition können Initiativenparameter nicht mehr aus der Initiative gelöscht werden. Sollte ein Initiativenparameter nicht mehr benötigt werden, entfernen Sie ihn, sodass er von keinem Richtliniendefinitionsparameter mehr verwendet wird.

1. Wählen Sie unten auf der Seite die Option **Weiter** oder im oberen Bereich des Assistenten die Registerkarte **Richtlinienparameter** aus.

1. Die der Initiative hinzugefügten Richtliniendefinitionen mit Parametern werden in einem Raster angezeigt. Mögliche Optionen für _Werttyp_ sind „Standardwert“, „Wert festlegen“ und „Initiativenparameter verwenden“. Bei Verwendung von „Wert festlegen“ wird der zugehörige Wert unter _Wert(e)_ eingegeben. Wenn der Parameter für die Richtliniendefinition über eine Liste zulässiger Werte verfügt, ist das Eingabefeld eine Dropdownauswahl. Bei Verwendung von „Initiativenparameter verwenden“ wird eine Dropdownauswahl mit den Namen der Initiativenparameter bereitgestellt, die auf der Registerkarte **Initiativenparameter** erstellt wurden.

   :::image type="content" source="../media/create-and-manage/initiative-definition-3.png" alt-text="Screenshot: Optionen für zulässige Werte für den Definitionsparameter „Zulässige Standorte“ auf der Registerkarte „Richtlinienparameter“ der Seite „Initiativendefinition“":::

   > [!NOTE]
   > Werden einige `strongType`-Parameter verwendet, kann die Liste der Werte nicht automatisch ermittelt werden. In diesem Fall wird rechts neben der Parameterzeile eine Ellipse angezeigt. Wenn Sie sie auswählen, wird die Seite „Parameterbereich (&lt;Parametername&gt;)“ geöffnet. Wählen Sie auf dieser Seite das Abonnement aus, mit dem die Wertoptionen bereitgestellt werden sollen. Dieser Parameterbereich wird nur während der Erstellung der Initiativendefinition verwendet und wirkt sich bei der Zuweisung nicht auf die Richtlinienauswertung oder den Bereich der Initiative aus.

   Legen Sie den _Werttyp_ für „Zulässige Standorte“ auf „Wert festlegen“ fest, und wählen Sie in der Dropdownliste den Wert „USA, Osten 2“ aus. Legen Sie für die beiden Instanzen der Richtliniendefinition _Tag für Ressourcen hinzufügen oder ersetzen_ die Parameter **Tagname** auf „Env“ bzw. „CostCenter“ und die Parameter **Tagwert** auf „Test“ bzw. „Lab“ fest, wie im Anschluss gezeigt. Behalten Sie bei den anderen die Option „Standardwert“ bei. Da in der Initiative die gleiche Definition zweimal verwendet wird, aber jeweils unterschiedliche Parameter angegeben wurden, werden ein Tag vom Typ „Env“ mit dem Wert „Test“ und ein Tag vom Typ „CostCenter“ mit dem Wert „Lab“ für Ressourcen hinzugefügt oder ersetzt, die sich im Gültigkeitsbereich der Zuweisung befinden.

   :::image type="content" source="../media/create-and-manage/initiative-definition-4.png" alt-text="Screenshot: Eingegebene Optionen für zulässige Werte für den Definitionsparameter „Zulässige Standorte“ und Werte für beide Tagparametersätze auf der Registerkarte „Richtlinienparameter“ der Seite „Initiativendefinition“":::

1. Wählen Sie unten auf der Seite oder im oberen Bereich des Assistenten die Option **Überprüfen und erstellen** aus.

1. Überprüfen Sie die Einstellungen, und wählen Sie **Erstellen** aus.

#### <a name="create-a-policy-initiative-definition-with-azure-cli"></a>Erstellen einer Richtlinien-Initiativendefinition mit der Azure-Befehlszeilenschnittstelle

Sie können eine Richtlinien-Initiativendefinition mit dem Befehl `az policy set-definition` über die Azure-Befehlszeilenschnittstelle erstellen. Verwenden Sie das folgende Beispiel, um eine Richtlinien-Initiativendefinition mit einer vorhandenen Richtliniendefinition zu erstellen:

```azurecli-interactive
az policy set-definition create -n readOnlyStorage --definitions '[
        {
            "policyDefinitionId": "/subscriptions/mySubId/providers/Microsoft.Authorization/policyDefinitions/storagePolicy",
            "parameters": { "storageSku": { "value": "[parameters(\"requiredSku\")]" } }
        }
    ]' \
    --params '{ "requiredSku": { "type": "String" } }'
```

#### <a name="create-a-policy-initiative-definition-with-azure-powershell"></a>Erstellen einer Richtlinien-Initiativendefinition mit Azure PowerShell

Sie können eine Richtlinien-Initiativendefinition mithilfe von Azure PowerShell mit dem Cmdlet `New-AzPolicySetDefinition` erstellen. Verwenden Sie die folgende Richtlinien-Initiativendefinitionsdatei als `VMPolicySet.json`, um eine Richtlinien-Initiativendefinition mit einer vorhandenen Richtliniendefinition zu erstellen:

```json
[
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "parameters": {
            "tagName": {
                "value": "Business Unit"
            },
            "tagValue": {
                "value": "Finance"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/464dbb85-3d5f-4a1d-bb09-95a9b5dd19cf"
    }
]
```

```azurepowershell-interactive
New-AzPolicySetDefinition -Name 'VMPolicySetDefinition' -Metadata '{"category":"Virtual Machine"}' -PolicyDefinition C:\VMPolicySet.json
```

### <a name="assign-an-initiative-definition"></a>Zuweisen einer Initiativdefinition

1. Wählen Sie links auf der Seite „Azure Policy“ unter **Erstellung** die Option **Definitionen**.

1. Suchen Sie die Initiativdefinition **Get Secure**, die Sie zuvor erstellt haben, und wählen Sie sie aus. Wählen Sie oben auf der Seite **Zuweisen** aus, um die Seite **Get Secure: Initiative zuweisen** zu öffnen.

   :::image type="content" source="../media/create-and-manage/assign-definition.png" alt-text="Screenshot: Schaltfläche „Zuweisen“ auf der Seite „Initiativendefinition“" border="false":::

   Sie können auch mit der rechten Maustaste auf die ausgewählte Zeile klicken oder die Auslassungspunkte am Ende der Zeile für ein Kontextmenü auswählen. Wählen Sie dann **Zuweisen** aus.

   :::image type="content" source="../media/create-and-manage/select-right-click.png" alt-text="Screenshot: Kontextmenü für eine Initiative zum Auswählen der Funktion „Zuweisen“" border="false":::

1. Füllen Sie die Seite **Get Secure: Initiative zuweisen** aus, indem Sie die folgenden Beispielinformationen eingeben. Sie können Ihre eigenen Daten verwenden.

   - Umfang: Die Verwaltungsgruppe oder das Abonnement, in der bzw. dem Sie die Initiative gespeichert haben, wird zur Standardeinstellung.
     Sie können den Bereich ändern, um die Initiative einem Abonnement oder einer Ressourcengruppe innerhalb des Speicherorts zuzuweisen.
   - Ausschlüsse: Konfigurieren Sie Ressourcen innerhalb des Bereichs, auf die die Zuweisung der Initiative nicht angewendet werden soll.
   - Initiativendefinition und Zuweisungsname: Get Secure (vorab ausgefüllt als Name der zuzuweisenden Initiative).
   - Beschreibung: Diese Initiativenzuweisung dient zur Erzwingung dieser Gruppe von Richtliniendefinitionen.
   - Richtlinienerzwingung: Behalten Sie die Standardeinstellung _Aktiviert_ bei.
   - Zugewiesen von: Dieses Feld wird abhängig vom angemeldeten Benutzer automatisch ausgefüllt. Dieses Feld ist optional. Daher können auch benutzerdefinierte Werte eingegeben werden.

1. Wählen Sie im oberen Bereich des Assistenten die Registerkarte **Parameter** aus. Falls Sie in den vorherigen Schritten einen Initiativenparameter konfiguriert haben, legen Sie hier einen Wert fest.

1. Wählen Sie im oberen Bereich des Assistenten die Registerkarte **Bereinigung** aus. Lassen Sie **Verwaltete Identität erstellen** deaktiviert. Dieses Kontrollkästchen _muss_ aktiviert werden, wenn die zugewiesene Richtlinie oder Initiative eine Richtlinie mit der Auswirkung [deployIfNotExists](../concepts/effects.md#deployifnotexists) oder [modify](../concepts/effects.md#modify) enthält. Da dies bei der für dieses Tutorial verwendeten Richtlinie nicht der Fall ist, lassen Sie sie deaktiviert. Weitere Informationen finden Sie unter [Verwaltete Identitäten](../../../active-directory/managed-identities-azure-resources/overview.md) und [Funktionsweise der Wiederherstellungssicherheit](../how-to/remediate-resources.md#how-remediation-security-works).

1. Wählen Sie im oberen Bereich des Assistenten die Registerkarte **Überprüfen + erstellen** aus.

1. Überprüfen Sie die ausgewählten Optionen, und wählen Sie anschließend am unteren Rand der Seite die Option **Erstellen** aus.

## <a name="check-initial-compliance"></a>Überprüfen der anfänglichen Konformität

1. Wählen Sie links auf der Seite „Azure Policy“ die Option **Konformität**.

1. Suchen Sie nach der Initiative **Get Secure**. Wahrscheinlich lautet der _Konformitätszustand_ immer noch **Nicht gestartet**.
   Wählen Sie die Initiative aus, um ausführliche Informationen zur Zuweisung zu erhalten.

   :::image type="content" source="../media/create-and-manage/compliance-status-not-started.png" alt-text="Screenshot: Seite „Initiativenkonformität“ mit Zuweisungsbewertungen im Status „Nicht gestartet“" border="false":::

1. Wenn die Initiativenzuweisung abgeschlossen wurde, wird die Konformitätsseite aktualisiert und für _Konformitätszustand_ der Zustand **Konform** angezeigt.

   :::image type="content" source="../media/create-and-manage/compliance-status-compliant.png" alt-text="Screenshot: Seite „Initiativenkonformität“ mit abgeschlossenen Zuweisungsbewertungen im Status „Konform“" border="false":::

1. Wenn Sie auf der Seite mit der Initiativenkonformität eine Richtlinie auswählen, wird die Seite mit den Konformitätsdetails für die entsprechende Richtlinie angezeigt. Diese Seite enthält Konformitätsdetails auf Ressourcenebene.

## <a name="remove-a-non-compliant-or-denied-resource-from-the-scope-with-an-exclusion"></a>Entfernen einer nicht konformen oder abgelehnten Ressource aus dem Bereich mithilfe eines Ausschlusses

Nachdem eine Richtlinieninitiative zugewiesen wurde, um einen bestimmten Standort vorzuschreiben, werden an einem anderen Standort erstellte Ressourcen abgelehnt. In diesem Abschnitt erfahren Sie, wie Sie das Problem einer verweigerten Anforderung zum Erstellen einer Ressource durch Erstellen eines Ausschlusses für eine einzelne Ressourcengruppe lösen. Der Ausschluss verhindert die Erzwingung der Richtlinie (oder Initiative) für diese Ressourcengruppe. Im folgenden Beispiel ist in der ausgeschlossenen Ressourcengruppe jeder Standort erlaubt. Ein Ausschluss kann für ein Abonnement, für eine Ressourcengruppe oder für einzelne Ressourcen gelten.

> [!NOTE]
> Mithilfe einer [Richtlinienausnahme](../concepts/exemption-structure.md) kann auch die Auswertung einer Ressource übersprungen werden. Weitere Informationen finden Sie unter [Erläuterungen zum Bereich in Azure Policy](../concepts/scope.md).

Durch eine zugewiesene Richtlinie oder Initiative verhinderte Bereitstellungen können in der Ressourcengruppe angezeigt werden, die als Ziel der Bereitstellung fungiert: Wählen Sie links auf der Seite die Option **Bereitstellungen** und dann den **Bereitstellungsnamen** der fehlerhaften Bereitstellung aus. Die abgelehnte Ressource wird mit dem Status _Unzulässig_ angezeigt. Um die Richtlinie oder Initiative und die Zuweisung zu bestimmen, die die Ressource abgelehnt hat, wählen Sie auf der Übersichtsseite zur Bereitstellung **Fehler. Klicken Sie hier, um Details anzuzeigen. ->** . Rechts auf der Seite wird ein Fenster mit den Fehlerinformationen geöffnet. Unter **Fehlerdetails** werden die GUIDs der zugehörigen Richtlinienobjekte angezeigt.

:::image type="content" source="../media/create-and-manage/rg-deployment-denied.png" alt-text="Screenshot: Nicht erfolgreiche Bereitstellung, die von einer Richtlinienzuweisung abgelehnt wurde" border="false":::

Auf der Seite „Azure Policy“: Wählen Sie links auf der Seite die Option **Konformität** und anschließend die Richtlinieninitiative **Get Secure** aus. Auf dieser Seite sehen Sie eine Zunahme des Werts **Ablehnen** für blockierte Ressourcen. Auf der Registerkarte **Ereignisse** erfahren Sie, wer versucht hat, die durch die Richtliniendefinition abgelehnte Ressource zu erstellen oder bereitzustellen.

:::image type="content" source="../media/create-and-manage/compliance-overview.png" alt-text="Screenshot: Registerkarte „Ereignisse“ und Richtlinienereignisdetails auf der Seite „Initiativenkonformität“" border="false":::

In diesem Beispiel hat Trent Baker, einer der leitenden Virtualisierungsspezialisten von Contoso, erforderliche Arbeiten durchgeführt. Wir müssen Trent einen Ort für eine Ausnahme zur Verfügung stellen. Hierzu erstellen wir eine neue Ressourcengruppe namens **LocationsExcluded** und gewähren ihr eine Ausnahme für diese Richtlinienzuweisung.

### <a name="update-assignment-with-exclusion"></a>Aktualisieren einer Zuweisung mit Ausschluss

1. Wählen Sie links auf der Seite „Azure Policy“ unter **Erstellung** die Option **Zuweisungen**.

1. Durchsuchen Sie alle Richtlinienzuweisungen, und öffnen Sie die Richtlinienzuweisung _Get Secure_.

1. Legen Sie den **Ausschluss** fest, indem Sie die Auslassungspunkte und die auszuschließende Ressourcengruppe auswählen (in diesem Beispiel: _LocationsExcluded_). Wählen Sie **Zu ausgewähltem Bereich hinzufügen** und anschließend **Speichern** aus.

   :::image type="content" source="../media/create-and-manage/request-exclusion.png" alt-text="Screenshot: Option „Ausschlüsse“ auf der Seite „Initiativenzuweisung“ zum Hinzufügen einer ausgeschlossenen Ressourcengruppe zur Richtlinienzuweisung" border="false":::

   > [!NOTE]
   > Je nach Richtliniendefinition und deren Auswirkung kann der Ausschluss auch für bestimmte Ressourcen innerhalb einer Ressourcengruppe im Bereich der Zuweisung gewährt werden. Da in diesem Tutorial die Auswirkung **Deny** verwendet wurde, wäre es nicht sinnvoll, den Ausschluss auf eine bestimmte Ressource festzulegen, die bereits vorhanden ist.

1. Wählen Sie **Überprüfen + speichern** und anschließend **Speichern** aus.

In diesem Abschnitt haben Sie das Problem der verweigerten Anforderung behoben, indem Sie einen Ausschluss für eine einzelne Ressourcengruppe erstellt haben.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcen dieses Tutorials nicht mehr benötigen, führen Sie die folgenden Schritte aus, um die erstellten Richtlinienzuweisungen oder Definitionen zu löschen:

1. Klicken Sie links auf der Seite „Azure Policy“ unter **Erstellung** auf **Definitionen** (oder auf **Zuweisungen**, wenn Sie eine Zuweisung löschen möchten).

1. Suchen Sie nach der neuen Initiativ- oder Richtliniendefinition (bzw. der Zuweisung), die Sie entfernen möchten.

1. Klicken Sie mit der rechten Maustaste auf die Zeile, oder wählen Sie die Auslassungspunkte am Ende der Definition (oder Zuweisung), und wählen Sie anschließend **Definition löschen** (bzw. **Zuweisung löschen**) aus.

## <a name="review"></a>Überprüfung

In diesem Tutorial haben Sie folgende Aufgaben durchgeführt:

> [!div class="checklist"]
> - Sie haben eine Richtlinie zur Erzwingung einer Bedingung für die spätere Erstellung von Ressourcen zugewiesen.
> - Sie haben eine Initiativdefinition zur Nachverfolgung der Konformität für mehrere Ressourcen erstellt und zugewiesen.
> - Sie haben ein Problem mit einer nicht konformen oder abgelehnten Ressource behoben.
> - Sie haben eine neue Richtlinie in der gesamten Organisation implementiert.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Strukturen von Richtliniendefinitionen finden Sie im folgenden Artikel:

> [!div class="nextstepaction"]
> [Struktur von Azure Policy-Definitionen](../concepts/definition-structure.md)