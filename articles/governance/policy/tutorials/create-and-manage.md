---
title: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung
description: Verwenden Sie Azure Policy, um die Einhaltung von Standards, gesetzlichen Vorschriften und Auditanforderungen durchzusetzen, Kosten zu steuern, für konsistente Sicherheit und Leistung zu sorgen und unternehmensweite Entwurfsprinzipien vorzugeben.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/04/2019
ms.topic: tutorial
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: c12345791e62aa99bd07dde7fc44dd52d0989941
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65979183"
---
# <a name="tutorial-create-and-manage-policies-to-enforce-compliance"></a>Tutorial: Erstellen und Verwalten von Richtlinien zur Konformitätserzwingung

Zur Einhaltung Ihrer Unternehmensstandards und Vereinbarungen zum Servicelevel müssen Sie mit der Erstellung und Verwaltung von Richtlinien in Azure vertraut sein. In diesem Tutorial erfahren Sie, wie Sie mithilfe von Azure Policy einige allgemeinere Aufgaben im Zusammenhang mit der organisationsweiten Erstellung, Zuweisung und Verwaltung von Richtlinien durchführen:

> [!div class="checklist"]
> - Zuweisen einer Richtlinie zur Erzwingung einer Bedingung für die spätere Erstellung von Ressourcen
> - Erstellen und Zuweisen einer Initiativdefinition zur Nachverfolgung der Konformität für mehrere Ressourcen
> - Beheben von Problemen mit einer nicht konformen oder abgelehnten Ressource
> - Implementieren einer neuen Richtlinie in der gesamten Organisation

Lesen Sie die Schnellstartartikel, wenn Sie eine Richtlinie zum Ermitteln des aktuellen Konformitätszustands Ihrer vorhandenen Ressourcen zuweisen möchten. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="assign-a-policy"></a>Zuweisen einer Richtlinie

Für die Konformitätserzwingung mit Azure Policy muss zunächst eine Richtliniendefinition zugewiesen werden. Eine Richtliniendefinition definiert, unter welcher Bedingung eine Richtlinie erzwungen wird und welche Auswirkung sie haben soll. In diesem Beispiel weisen Sie eine integrierte Richtliniendefinition namens *SQL Server-Version 12.0 erfordern* zu, um die Bedingung durchzusetzen, dass alle SQL Server-Datenbanken die Version 12.0 aufweisen müssen, um die Konformitätsanforderungen zu erfüllen.

1. Starten Sie den Azure Policy-Dienst über das Azure-Portal, indem Sie auf **Alle Dienste** klicken und dann nach **Richtlinie** suchen und die entsprechende Option auswählen.

   ![Suchen nach „Policy“ unter „Alle Dienste“](../media/create-and-manage/search-policy.png)

1. Wählen Sie links auf der Seite „Azure Policy“ die Option **Zuweisungen**. Eine Zuweisung ist eine zugewiesene Richtlinie, die innerhalb eines bestimmten Bereichs angewendet werden soll.

   ![Auswählen von „Zuweisungen“ auf der Übersichtsseite von Policy](../media/create-and-manage/select-assignments.png)

1. Wählen Sie im oberen Bereich der Seite **Richtlinien – Zuweisungen** die Option **Richtlinie zuweisen**.

   ![Zuweisen einer Richtliniendefinition auf der Seite „Zuweisungen“](../media/create-and-manage/select-assign-policy.png)

1. Wählen Sie auf der Seite **Richtlinie zuweisen** den **Bereich** aus, indem Sie auf die Auslassungspunkte klicken und entweder eine Verwaltungsgruppe oder ein Abonnement auswählen. Wählen Sie optional eine Ressourcengruppe aus. Ein Bereich bestimmt, für welche Ressourcen oder Ressourcengruppe die Richtlinienzuweisung erzwungen wird. Klicken Sie dann unten im Abschnitt der Seite **Bereich** auf **Auswählen**.

   In diesem Beispiel wird das Abonnement **Contoso** verwendet. Ihr Abonnement wird sich davon unterscheiden.

1. Ressourcen können basierend auf dem **Bereich** ausgeschlossen werden. **Ausschlüsse** beginnen auf einer Ebene unterhalb der Ebene des **Bereichs**. **Ausschlüsse** sind optional, lassen Sie sie daher vorerst leer.

1. Wählen Sie die **Richtliniendefinition** mit den Auslassungspunkten, um die Liste der verfügbaren Definitionen zu öffnen. Sie können den **Typ** der Richtliniendefinition nach *Integriert* filtern, um alle Definitionen anzuzeigen und ihre Beschreibungen zu lesen.

1. Wählen Sie **SQL Server-Version 12.0 erfordern** aus. Falls Sie die Option nicht direkt finden, geben Sie **SQL Server erfordern** in das Suchfeld ein, und drücken Sie dann die EINGABETASTE, oder klicken Sie außerhalb des Suchfelds. Klicken Sie unten auf der Seite **Verfügbare Definitionen** auf **Auswählen**, nachdem Sie die Richtliniendefinition gefunden und ausgewählt haben.

   ![Suchen nach einer Richtlinie mithilfe eines Suchfilters](../media/create-and-manage/select-available-definition.png)

1. Der **Zuweisungsname** wird automatisch mit dem ausgewählten Richtliniennamen gefüllt, kann aber geändert werden. In diesem Beispiel verwenden Sie *SQL Server-Version 12.0 erfordern*. Geben Sie ggf. auch eine **Beschreibung** ein. Die Beschreibung enthält Details zu dieser Richtlinienzuweisung.
   Das Feld **Zugewiesen von** wird abhängig vom angemeldeten Benutzer automatisch ausgefüllt. Dieses Feld ist optional. Daher können auch benutzerdefinierte Werte eingegeben werden.

1. Lassen Sie **Verwaltete Identität erstellen** deaktiviert. Diese Option _muss_ aktiviert werden, wenn die zugewiesene Richtlinie oder Initiative eine Richtlinie mit dem Effekt [deployIfNotExists](../concepts/effects.md#deployifnotexists) enthält. Da dies bei der für dieses Tutorial verwendeten Richtlinie nicht der Fall ist, lassen Sie sie deaktiviert. Weitere Informationen finden Sie unter [Verwaltete Identitäten](../../../active-directory/managed-identities-azure-resources/overview.md) und [Funktionsweise der Wiederherstellungssicherheit](../how-to/remediate-resources.md#how-remediation-security-works).

1. Klicken Sie auf **Zuweisen**.

## <a name="implement-a-new-custom-policy"></a>Implementieren einer neuen benutzerdefinierten Richtlinie

Sie haben eine integrierte Richtliniendefinition zugewiesen und können nun weitere Aktionen mit Azure Policy ausführen. Erstellen Sie als Nächstes eine neue benutzerdefinierte Richtlinie zur Kosteneinsparung, die durch eine Überprüfung sicherstellt, dass in Ihrer Umgebung keine VMs der G-Serie erstellt werden können. Wenn also ein Benutzer in Ihrer Organisation versucht, einen virtuellen Computer aus der G-Serie zu erstellen, wird die Anforderung abgelehnt.

1. Wählen Sie links auf der Seite „Azure Policy“ unter **Erstellung** die Option **Definitionen**.

   ![Definitionsseite unter der Gruppe „Erstellung“](../media/create-and-manage/definition-under-authoring.png)

1. Wählen Sie oben auf der Seite **+ Richtliniendefinition** aus. Über diese Schaltfläche gelangen Sie zur Seite **Richtliniendefinition**.

1. Geben Sie Folgendes ein:

   - Die Verwaltungsgruppe oder das Abonnement, in der bzw. in dem die Richtliniendefinition gespeichert ist. Wählen Sie über die Auslassungspunkte bei **Speicherort der Definition** aus.

     > [!NOTE]
     > Wenn Sie diese Richtliniendefinition auf mehrere Abonnements anwenden möchten, muss der Speicherort eine Verwaltungsgruppe sein, die die Abonnements enthält, denen Sie die Richtlinie zuweisen. Dasselbe gilt für eine Initiativdefinition.

   - Den Namen der Richtliniendefinition: *Require VM SKUs smaller than the G series* (VM-SKUs müssen kleiner als die G-Serie sein)
   - Den Zweck der Richtliniendefinition: *This policy definition enforces that all VMs created in this scope have SKUs smaller than the G series to reduce cost.* (Diese Richtliniendefinition dient zur Kostensenkung und bewirkt, dass die SKU von virtuellen Computern, die in diesem Bereich erstellt werden, kleiner als die G-Serie ist.)
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

   Die *field*-Eigenschaft in der Richtlinienregel muss einen der folgenden Werte aufweisen: „Name“, „Type“, „Location“, „Tags“ oder einen Alias. Ein Beispiel für einen Alias wäre `"Microsoft.Compute/VirtualMachines/Size"`.

   Weitere Beispiele für Azure-Richtlinien finden Sie unter [Azure Policy-Beispiele](../samples/index.md).

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

Sie können eine Richtliniendefinition mit dem entsprechenden Befehl über Azure CLI erstellen. Verwenden Sie zum Erstellen einer Richtliniendefinition mit einer Inline-Regel das folgende Beispiel:

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

   ![Auswählen einer Definition auf der Seite „Definitionen“](../media/create-and-manage/definition-under-authoring.png)

1. Klicken Sie im oberen Bereich der Seite auf **+ Initiativdefinition**, um zur Seite **Initiativdefinition** zu gelangen.

   ![Überprüfen der Seite „Initiativendefinition“](../media/create-and-manage/initiative-definition.png)

1. Wählen Sie über die Auslassungspunkte unter **Speicherort der Definition** eine Verwaltungsgruppe oder ein Abonnement zum Speichern der Definition aus. Wenn die vorherige Seite auf eine einzelne Verwaltungsgruppe oder ein einzelnes Abonnement begrenzt war, wird **Speicherort der Definition** automatisch aufgefüllt.

1. Geben Sie **Name** und **Beschreibung** der Initiative ein.

   Mit diesem Beispiel wird überprüft, ob Ressourcen mit Richtliniendefinitionen zur Verbesserung der Sicherheit konform sind. Geben Sie für die Initiative den Namen **Get Secure** (Sicherheit verbessern) ein, und legen Sie die Beschreibung wie folgt fest: **This initiative has been created to handle all policy definitions associated with securing resources.** (Diese Initiative gilt für alle Richtliniendefinitionen, die mit dem Schutz von Ressourcen in Zusammenhang stehen.)

1. Wählen Sie für **Kategorie** eine der vorhandenen Optionen, oder erstellen Sie eine neue Kategorie.

1. Durchsuchen Sie die Liste mit den verfügbaren **Definitionen** (rechte Hälfte der Seite **Initiativdefinition**), und wählen Sie die Richtliniendefinitionen aus, die Sie dieser Initiative hinzufügen möchten. Fügen Sie für die Initiative **Get secure** die folgenden integrierten Richtliniendefinitionen hinzu, indem Sie auf **+** neben den Informationen zur Richtliniendefinition oder auf eine Zeile mit den Richtliniendefinitionen und dann auf der Detailseite auf die Option **+ Hinzufügen** klicken:

   - Require SQL Server version 12.0 (SQL Server-Version 12.0 fordern)
   - [Preview]: Monitor unprotected web applications in Security Center.
   - [Preview]: Monitor permissive network across in Security Center.
   - [Preview]: Monitor possible app Whitelisting in Security Center.
   - [Preview]: Monitor unencrypted VM Disks in Security Center.

   Nachdem Sie die Richtliniendefinition in der Liste ausgewählt haben, wird sie unter **Richtlinien und Parameter** hinzugefügt.

   ![Überprüfen der Parameter für die Initiativendefinition](../media/create-and-manage/initiative-definition-2.png)

1. Wenn eine zur Initiative hinzugefügte Richtliniendefinition Parameter enthält, werden sie unterhalb des Richtliniennamens im Bereich **Richtlinien und Parameter** angezeigt. Der _Wert_ kann entweder auf „Wert festlegen“ (hartcodiert für alle Zuweisungen dieser Initiative) oder auf „Initiativenparameter verwenden“ (Festlegung während der einzelnen Initiativenzuweisungen) festgelegt werden. Wird „Wert festlegen“ ausgewählt, können die Werte in der Dropdownliste rechts neben _Werte_ eingegeben oder ausgewählt werden. Wird „Initiativenparameter verwenden“ ausgewählt, wird ein neuer Abschnitt **Initiativenparameter** angezeigt, in dem Sie den Parameter definieren können, der während der Initiativenzuweisung festgelegt wird. Mit den zulässigen Werten für diesen Initiativenparameter können Sie weiter einschränken, welche Werte während der Initiativenzuweisung festgelegt werden können.

   ![Ändern der Parameter für die Initiativdefinition auf der Grundlage zulässiger Werte](../media/create-and-manage/initiative-definition-3.png)

   > [!NOTE]
   > Werden einige `strongType`-Parameter verwendet, kann die Liste der Werte nicht automatisch ermittelt werden. In diesem Fall wird rechts neben der Parameterzeile eine Ellipse angezeigt. Wenn Sie darauf klicken, wird die Seite „Parameterbereich (&lt;Parametername&gt;)“ geöffnet. Wählen Sie auf dieser Seite das Abonnement aus, mit dem die Wertoptionen bereitgestellt werden sollen. Dieser Parameterbereich wird nur während der Erstellung der Initiativendefinition verwendet und wirkt sich bei der Zuweisung nicht auf die Richtlinienauswertung oder den Bereich der Initiative aus.

1. Klicken Sie auf **Speichern**.

### <a name="assign-an-initiative-definition"></a>Zuweisen einer Initiativdefinition

1. Wählen Sie links auf der Seite „Azure Policy“ unter **Erstellung** die Option **Definitionen**.

1. Suchen Sie die Initiativendefinition **Get Secure**, die Sie zuvor erstellt haben, und klicken Sie darauf. Wählen Sie oben auf der Seite **Zuweisen** aus, um die Seite **Get Secure: Initiative zuweisen** zu öffnen.

   ![Zuweisen einer Definition auf der Seite „Initiativdefinition“](../media/create-and-manage/assign-definition.png)

   Sie können auch mit der rechten Maustaste auf die ausgewählte Zeile oder mit der linken Maustaste auf die Auslassungspunkte am Ende der Zeile für ein Kontextmenü klicken. Wählen Sie dann **Zuweisen** aus.

   ![Alternative Optionen für eine initiative](../media/create-and-manage/select-right-click.png)

1. Füllen Sie die Seite **Get Secure: Initiative zuweisen** aus, indem Sie die folgenden Beispielinformationen eingeben. Sie können Ihre eigenen Daten verwenden.

   - Bereich: Die Verwaltungsgruppe oder das Abonnement, in der bzw. dem Sie die Initiative gespeichert haben, wird zur Standardeinstellung.
     Sie können den Bereich ändern, um die Initiative einem Abonnement oder einer Ressourcengruppe innerhalb des Speicherorts zuzuweisen.
   - Ausschlüsse: Konfigurieren Sie Ressourcen innerhalb des Bereichs, auf die die Zuweisung der Initiative nicht angewendet werden soll.
   - Initiativendefinition und Zuweisungsname: Get Secure (vorab ausgefüllt als Name der zuzuweisenden Initiative).
   - Beschreibung: Diese Initiativenzuweisung dient zur Erzwingung dieser Gruppe von Richtliniendefinitionen.
   - Zugewiesen von: Dieses Feld wird abhängig vom angemeldeten Benutzer automatisch ausgefüllt. Dieses Feld ist optional. Daher können auch benutzerdefinierte Werte eingegeben werden.

1. Lassen Sie **Verwaltete Identität erstellen** deaktiviert. Diese Option _muss_ aktiviert werden, wenn die zugewiesene Richtlinie oder Initiative eine Richtlinie mit dem Effekt [deployIfNotExists](../concepts/effects.md#deployifnotexists) enthält. Da dies bei der für dieses Tutorial verwendeten Richtlinie nicht der Fall ist, lassen Sie sie deaktiviert. Weitere Informationen finden Sie unter [Verwaltete Identitäten](../../../active-directory/managed-identities-azure-resources/overview.md) und [Funktionsweise der Wiederherstellungssicherheit](../how-to/remediate-resources.md#how-remediation-security-works).

1. Klicken Sie auf **Zuweisen**.

## <a name="check-initial-compliance"></a>Überprüfen der anfänglichen Konformität

1. Wählen Sie links auf der Seite „Azure Policy“ die Option **Konformität**.

1. Suchen Sie nach der Initiative **Get Secure**. Wahrscheinlich lautet der _Konformitätszustand_ immer noch **Nicht gestartet**.
   Klicken Sie auf die Initiative, um ausführliche Informationen zum Status der Zuweisung zu erhalten.

   ![Seite „Initiativenkonformität“: Auswertung noch nicht gestartet](../media/create-and-manage/compliance-status-not-started.png)

1. Wenn die Initiativenzuweisung abgeschlossen wurde, wird die Konformitätsseite aktualisiert und für _Konformitätszustand_ der Zustand **Konform** angezeigt.

   ![Seite „Initiativenkonformität“: Ressourcen konform](../media/create-and-manage/compliance-status-compliant.png)

1. Wenn Sie auf der Seite mit der Initiativenkonformität auf eine Richtlinie klicken, wird die Seite mit den Konformitätsdetails für die Richtlinie angezeigt. Diese Seite enthält Konformitätsdetails auf Ressourcenebene.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Ausnehmen einer nicht konformen oder abgelehnten Ressource mithilfe eines Ausschlusses

Das obige Beispiel bewirkt, dass nach dem Zuweisen der Richtliniendefinition zur Forderung der SQL Server-Version 12.0 die Erstellung einer SQL Server-Instanz mit einer anderen Version als 12.0 abgelehnt wird. In diesem Abschnitt wird erläutert, wie Sie das Problem einer verweigerten Anforderung zum Erstellen einer SQL Server-Instanz durch Erstellen eines Ausschlusses für eine einzelne Ressourcengruppe lösen. Der Ausschluss verhindert die Durchsetzung der Richtlinie (oder Initiative) für diese Ressource.
Im folgenden Beispiel ist jede SQL Server-Version in einer einzigen Ressourcengruppe erlaubt. Ein Ausschluss kann für ein Abonnement oder eine Ressourcengruppe gelten oder auf einzelne Ressourcen beschränkt werden.

Eine von einer zugewiesenen Richtlinie oder Initiative verhinderte Bereitstellung kann an zwei Stellen eingesehen werden:

- In der Ressourcengruppe, für die die Bereitstellung gilt: Wählen Sie links auf der Seite die Option **Bereitstellungen**aus, und klicken Sie dann auf den **Bereitstellungsnamen** der fehlerhaften Bereitstellung. Die abgelehnte Ressource wird mit dem Status _Unzulässig_ angezeigt. Um die Richtlinie oder Initiative und die Zuweisung zu bestimmen, die die Ressource abgelehnt hat, klicken Sie auf der Übersichtsseite zur Bereitstellung auf **Fehler. Klicken Sie hier, um Details anzuzeigen. ->** . Rechts auf der Seite wird ein Fenster mit den Fehlerinformationen geöffnet. Unter **Fehlerdetails** werden die GUIDs der zugehörigen Richtlinienobjekte angezeigt.

  ![Bereitstellung durch Richtlinienzuweisung abgelehnt](../media/create-and-manage/rg-deployment-denied.png)

- Auf der Seite „Azure Policy“: Wählen Sie links auf der Seite die Option **Konformität** aus, und klicken Sie auf die Richtlinie **SQL Server-Version 12.0 erfordern**. Auf der Seite, die geöffnet wird, sehen Sie dann eine Erhöhung der Anzahl für **Ablehnen**. Auf der Registerkarte **Ereignisse** sehen Sie auch, welcher Benutzer versucht hat, die von der Richtlinie abgelehnte Bereitstellung durchzuführen.

  ![Konformitätsübersicht zu einer zugewiesenen Richtlinie](../media/create-and-manage/compliance-overview.png)

In diesem Beispiel hat Trent Baker, einer der leitenden Virtualisierungsspezialisten von Contoso, erforderliche Arbeiten durchgeführt. Sie müssen eine Ausnahme für Trent gewähren, möchten aber in keiner der Ressourcengruppen SQL Server-Instanzen mit einer anderen Version als 12.0 haben. Zu diesem Zweck dient eine neue Ressourcengruppe **SQLServers_Excluded**, der nun eine Ausnahme für diese Richtlinienzuweisung gewährt wird.

### <a name="update-assignment-with-exclusion"></a>Aktualisieren einer Zuweisung mit Ausschluss

1. Wählen Sie links auf der Seite „Azure Policy“ unter **Erstellung** die Option **Zuweisungen**.

1. Durchsuchen Sie alle Richtlinienzuweisungen, und öffnen Sie die Zuweisung *Require SQL Server version 12.0* (SQL Server-Version 12.0 fordern).

1. Legen Sie den **Ausschluss** fest, indem Sie auf die Auslassungspunkte klicken und die auszuschließende Ressourcengruppe auswählen (in diesem Beispiel *SQLServers_Excluded*).

   ![Hinzufügen einer ausgeschlossenen Ressourcengruppe zur Richtlinienzuweisung](../media/create-and-manage/request-exclusion.png)

   > [!NOTE]
   > Je nach Richtlinie und Auswirkung kann der Ausschluss auch für bestimmte Ressourcen innerhalb einer Ressourcengruppe im Bereich der Zuweisung gewährt werden. Da in diesem Tutorial die Auswirkung **Ablehnen** verwendet wurde, wäre es nicht sinnvoll, den Ausschluss auf eine bestimmte Ressource festzulegen, die bereits vorhanden ist.

1. Klicken Sie auf **Auswählen**, und klicken Sie dann auf **Speichern**.

In diesem Abschnitt haben Sie das Problem der verweigerten Anforderung behoben, indem Sie einen Ausschluss für eine einzelne Ressourcengruppe erstellt haben.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die Ressourcen dieses Tutorials nicht mehr benötigen, führen Sie die folgenden Schritte aus, um die erstellten Zuweisungen oder Definitionen zu löschen:

1. Klicken Sie links auf der Seite „Azure Policy“ unter **Erstellung** auf **Definitionen** (oder auf **Zuweisungen**, wenn Sie eine Zuweisung löschen möchten).

1. Suchen Sie nach der neuen Initiativ- oder Richtliniendefinition (bzw. der Zuweisung), die Sie entfernen möchten.

1. Klicken Sie mit der rechten Maustaste auf die Zeile, oder wählen Sie die Auslassungspunkte am Ende der Definition (oder Zuweisung), und wählen Sie anschließend **Definition löschen** (bzw. **Zuweisung löschen**) aus.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie folgende Aufgaben durchgeführt:

> [!div class="checklist"]
> - Sie haben eine Richtlinie zur Erzwingung einer Bedingung für die spätere Erstellung von Ressourcen zugewiesen.
> - Sie haben eine Initiativdefinition zur Nachverfolgung der Konformität für mehrere Ressourcen erstellt und zugewiesen.
> - Sie haben ein Problem mit einer nicht konformen oder abgelehnten Ressource behoben.
> - Sie haben eine neue Richtlinie in der gesamten Organisation implementiert.

Weitere Informationen zu den Strukturen von Richtliniendefinitionen finden Sie im folgenden Artikel:

> [!div class="nextstepaction"]
> [Struktur von Azure Policy-Definitionen](../concepts/definition-structure.md)