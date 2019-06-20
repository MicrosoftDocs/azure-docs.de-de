---
title: Azure Blueprints-Funktionen
description: Beschreibt die für die Verwendung mit Azure Blueprints-Definitionen und -Zuweisungen gedachten Funktionen.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/15/2019
ms.topic: reference
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: dc72113a8f5ed978d64d35c43e94dc9e19e4cdb1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65209402"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Funktionen für die Verwendung mit Azure Blueprints

Azure Blueprints bietet Funktionen, durch die eine Blaupausendefinition dynamischer wird. Diese Funktionen dienen zur Verwendung mit Blaupausendefinitionen und Blaupausenartefakten. Ein Resource Manager-Vorlagenartefakt unterstützt die vollständige Verwendung von Resource Manager-Funktionen zusätzlich zum Abrufen eines dynamischen Werts über einen Blaupausenparameter.

Folgende Funktionen werden unterstützt:

- [artifacts](#artifacts)
- [concat](#concat)
- [parameters](#parameters)
- [Ressourcengruppe](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [Abonnement](#subscription)

## <a name="artifacts"></a>artifacts

`artifacts(artifactName)`

Gibt ein Objekt von Eigenschaften zurück, die mit den Ausgaben dieses Blaupausenartefakts aufgefüllt sind.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| artifactName |Ja |Zeichenfolge |Der Name eines Blaupausenartefakts. |

### <a name="return-value"></a>Rückgabewert

Ein Objekt von Ausgabeeigenschaften. Die **Ausgaben**eigenschaften sind abhängig vom Typ des Blaupausenartefakts, auf das verwiesen wird. Alle Typen halten folgendes Format ein:

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>Richtlinienzuweisungsartefakt

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="resource-manager-template-artifact"></a>Resource Manager-Vorlagenartefakt

Die **Ausgaben**eigenschaften des zurückgegebenen Objekts sind in der Resource Manager-Vorlage definiert und werden von der Bereitstellung zurückgegeben.

#### <a name="role-assignment-artifact"></a>Rollenzuweisungsartefakt

```json
{
    "outputs": {
        "roleAssignmentId": "{resourceId-of-role-assignment}",
        "roleDefinitionId": "{resourceId-of-role-definition}",
        "principalId": "{principalId-role-is-being-assigned-to}",
    }
}
```

### <a name="example"></a>Beispiel

Ein Resource Manager-Vorlagenartefakt mit der ID _myTemplateArtifact_, das die folgende Beispielausgabeeigenschaft enthält:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    ...
    "outputs": {
        "myArray": {
            "type": "array",
            "value": ["first", "second"]
        },
        "myString": {
            "type": "string",
            "value": "my string value"
        },
        "myObject": {
            "type": "object",
            "value": {
                "myProperty": "my value",
                "anotherProperty": true
            }
        }
    }
}
```

Einige Beispiele für das Abrufen von Daten aus dem _myTemplateArtifact_-Beispiel sind:

| Ausdruck | Type | Wert |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Array | \["first", "second"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | string | "first" |
|`[artifacts("myTemplateArtifact").outputs.myString]` | string | "my string value" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Object | { "myproperty": "my value", "anotherProperty": true } |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | string | "my value" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Bool | True |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

Kombiniert mehrere Zeichenfolgenwerte und gibt die verkettete Zeichenfolge zurück.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| string1 |Ja |Zeichenfolge |Der erste Wert für die Verkettung. |
| zusätzliche Argumente |Nein |Zeichenfolge |Weitere Werte in sequenzieller Reihenfolge für die Verkettung. |

### <a name="return-value"></a>Rückgabewert

Eine Zeichenfolge aus verketteten Werten.

### <a name="remarks"></a>Anmerkungen

Die Azure Blueprint-Funktion unterscheidet sich von der Azure Resource Manager-Vorlagenfunktion darin, dass sie nur mit Zeichenfolgen arbeitet.

### <a name="example"></a>Beispiel

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parameters

`parameters(parameterName)`

Gibt einen Blaupausenparameterwert zurück. Der angegebene Parametername muss in der Blaupausendefinition oder den Blaupausenartefakten definiert sein.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| parameterName |Ja |Zeichenfolge |Der Name des zurückzugebenden Parameter. |

### <a name="return-value"></a>Rückgabewert

Der Wert des angegebenen Blaupausen- oder Blaupausenartefaktparameters.

### <a name="remarks"></a>Anmerkungen

Die Azure Blueprint-Funktion unterscheidet sich von der Azure Resource Manager-Vorlagenfunktion darin, dass sie nur mit Blaupausenparametern arbeitet.

### <a name="example"></a>Beispiel

Definieren des Parameters _principalIds_ in der Blaupausendefinition:

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "parameters": {
            "principalIds": {
                "type": "array",
                "metadata": {
                    "displayName": "Principal IDs",
                    "description": "This is a blueprint parameter that any artifact can reference. We'll display these descriptions for you in the info bubble. Supply principal IDs for the users,groups, or service principals for the RBAC assignment.",
                    "strongType": "PrincipalId"
                }
            }
        },
        ...
    }
}
```

Anschließende Verwendung von _principalIds_ als Argument für `parameters()` in einem Blaupausenartefakt:

```json
{
    "type": "Microsoft.Blueprint/blueprints/artifacts",
    "kind": "roleAssignment",
    ...
    "properties": {
        "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
        "principalIds": "[parameters('principalIds')]",
        ...
    }
}
```

## <a name="resourcegroup"></a>Ressourcengruppe

`resourceGroup()`

Gibt ein Objekt zurück, das die aktuelle Ressourcengruppe darstellt.

### <a name="return-value"></a>Rückgabewert

Das zurückgegebene Objekt hat das folgende Format:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="remarks"></a>Anmerkungen

Die Azure Blueprint-Funktion unterscheidet sich von der Azure Resource Manager-Vorlagenfunktion. Die `resourceGroup()`-Funktion kann nicht in einem Artefakt der Abonnementebene oder der Blaupausendefinition verwendet werden. Sie kann nur in Blaupausenartefakten verwendet werden, die Teil eines Ressourcengruppenartefakts sind.

Die `resourceGroup()`-Funktion wird häufig verwendet, um Ressourcen am gleichen Speicherort wie das Ressourcengruppenartefakt zu erstellen.

### <a name="example"></a>Beispiel

Um den Speicherort der Ressourcengruppe zu verwenden, der entweder in der Blaupausendefinition oder während der Zuweisung festgelegt wurde, deklarieren Sie als Speicherort für ein anderes Artefakt ein Platzhalterobjekt für eine Ressourcengruppe in Ihrer Blaupausendefinition. In diesem Beispiel ist _NetworkingPlaceholder_ der Name des Ressourcengruppenplatzhalters.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Verwenden Sie dann die `resourceGroup()`-Funktion im Kontext eines Blaupausenartefakts, das auf ein Platzhalterobjekt für eine Ressourcengruppe abzielt. In diesem Beispiel wird das Vorlagenartefakt in der _NetworkingPlaceholder_-Ressourcengruppe bereitgestellt und stellt den Parameter _resourceLocation_ bereit, der mit dem Speicherort der _NetworkingPlaceholder_-Ressourcengruppe in der Vorlage dynamisch aufgefüllt wird. Der Speicherort der _NetworkingPlaceholder_-Ressourcengruppe hätte auch statisch in der Blaupausendefinition oder dynamisch während der Zuweisung definiert werden können. In beiden Fällen wird dem Vorlagenartefakt diese Information als Parameter bereitgestellt, den es verwendet, um die Ressourcen am richtigen Speicherort bereitzustellen.

```json
{
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "kind": "template",
  "properties": {
      "template": {
        ...
      },
      "resourceGroup": "NetworkingPlaceholder",
      ...
      "parameters": {
        "resourceLocation": {
          "value": "[resourceGroup().location]"
        }
      }
  }
}
```

## <a name="resourcegroups"></a>resourceGroups

`resourceGroups(placeholderName)`

Gibt ein Objekt zurück, das das angegebene Ressourcengruppenartefakt darstellt. Im Gegensatz zu `resourceGroup()`, wo der Kontext des Artefakts erforderlich ist, wird diese Funktion verwendet, um die Eigenschaften eines bestimmten Ressourcengruppenplatzhalters abzurufen, wenn sich die Funktion nicht im Kontext dieser Ressourcengruppe befindet.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| placeholderName |Ja |Zeichenfolge |Der Platzhaltername des Ressourcengruppenartefakts, der zurückgegeben werden soll. |

### <a name="return-value"></a>Rückgabewert

Das zurückgegebene Objekt hat das folgende Format:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>Beispiel

Um den Speicherort der Ressourcengruppe zu verwenden, der entweder in der Blaupausendefinition oder während der Zuweisung festgelegt wurde, deklarieren Sie als Speicherort für ein anderes Artefakt ein Platzhalterobjekt für eine Ressourcengruppe in Ihrer Blaupausendefinition. In diesem Beispiel ist _NetworkingPlaceholder_ der Name des Ressourcengruppenplatzhalters.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Verwenden Sie dann die `resourceGroups()`-Funktion aus dem Kontext eines beliebigen Blaupausenartefakts, um einen Verweis auf das Platzhalterobjekt für die Ressourcengruppe abzurufen. In diesem Beispiel wird das Vorlagenartefakt außerhalb der _NetworkingPlaceholder_-Ressourcengruppe bereitgestellt und stellt den Parameter _artifactLocation_ bereit, der mit dem Speicherort der _NetworkingPlaceholder_-Ressourcengruppe in der Vorlage dynamisch aufgefüllt wird. Der Speicherort der _NetworkingPlaceholder_-Ressourcengruppe hätte auch statisch in der Blaupausendefinition oder dynamisch während der Zuweisung definiert werden können. In beiden Fällen wird dem Vorlagenartefakt diese Information als Parameter bereitgestellt, den es verwendet, um die Ressourcen am richtigen Speicherort bereitzustellen.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "artifactLocation": {
          "value": "[resourceGroups('NetworkingPlaceholder').location]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="subscription"></a>Abonnement

`subscription()`

Gibt Details zum Abonnement für die aktuelle Blaupausenzuweisung zurück.

### <a name="return-value"></a>Rückgabewert

Das zurückgegebene Objekt hat das folgende Format:

```json
{
    "id": "/subscriptions/{subscriptionId}",
    "subscriptionId": "{subscriptionId}",
    "tenantId": "{tenantId}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>Beispiel

Verwendung des Anzeigenamens des Abonnements und der `concat()`-Funktion zum Erstellen einer Benennungskonvention, die als Parameter _resourceName_ an das Vorlagenartefakt übergeben wird.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "resourceName": {
          "value": "[concat(subscription().displayName, '-vm')]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](../concepts/lifecycle.md).
- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](../concepts/parameters.md) vertraut.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](../concepts/sequencing-order.md) anpassen können.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](../concepts/resource-locking.md) können.
- Lernen Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../how-to/update-existing-assignments.md).
- Beheben Sie Probleme bei der Blaupausenzuweisung mithilfe des [allgemeinen Leitfadens zur Problembehandlung](../troubleshoot/general.md).