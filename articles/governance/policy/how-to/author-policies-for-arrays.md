---
title: Erstellen von Richtlinien für Arrayeigenschaften für Ressourcen
description: Erfahren Sie, wie Sie mit Arrayparametern und Arrayausdrücken arbeiten, den [*]-Alias auswerten und Elemente mit Azure Policy-Definitionsregeln anfügen.
ms.date: 10/22/2020
ms.topic: how-to
ms.openlocfilehash: 60044d4a599c14088ea923a6a14cb46543646995
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920456"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Erstellen von Richtlinien für Arrayeigenschaften für Azure-Ressourcen

Azure Resource Manager-Eigenschaften werden häufig als Zeichenfolgen und boolesche Werte definiert. Im Fall einer 1: n-Beziehung werden komplexe Eigenschaften stattdessen als Arrays definiert. In Azure Policy werden Arrays auf verschiedene Weise verwendet:

- Als Typ eines [Definitionsparameters](../concepts/definition-structure.md#parameters), um mehrere Optionen bereitzustellen
- Als Teil einer [Richtlinienregel](../concepts/definition-structure.md#policy-rule) mit den Bedingungen **in** oder **notIn**
- Als Teil einer Richtlinienregel, die den [\[\*\]-Alias](../concepts/definition-structure.md#understanding-the--alias) auswertet für:
  - Szenarien wie **Keine**, **Beliebig** oder **Alle**
  - Komplexe Szenarien mit **count**
- In der [Auswirkung „append“](../concepts/effects.md#append), um ein vorhandenes Array zu ersetzen oder ihm Elemente hinzuzufügen

Dieser Artikel befasst sich mit jedem dieser Verwendungsfälle in Azure Policy und enthält mehrere Beispieldefinitionen.

## <a name="parameter-arrays"></a>Parameterarrays

### <a name="define-a-parameter-array"></a>Definieren eines Parameterarrays

Durch das Definieren eines Parameters als Array kann die Richtlinie flexibel gestaltet werden, wenn mehrere Werte erforderlich sind.
Diese Richtliniendefinition lässt einen beliebigen einzelnen Ort für den Parameter **AllowedLocations** zu (der Standardwert ist _eastus2_):

```json
"parameters": {
    "allowedLocations": {
        "type": "string",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2"
    }
}
```

Da **type** auf _string_ festgelegt ist, kann beim Zuweisen der Richtlinie nur ein Wert festgelegt werden. Wenn diese Richtlinie zugewiesen ist, sind Ressourcen innerhalb des Bereichs nur in einer einzigen Azure-Region zulässig. Die meisten Richtliniendefinitionen müssen eine Liste genehmigter Optionen zulassen, z. B. _eastus2_, _eastus_ und _westus2_.

Verwenden Sie zum Erstellen einer Richtliniendefinition, die mehrere Optionen zulässt, den **Typ** _array_. Diese Richtlinie kann wie folgt umgeschrieben werden:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2",
        "allowedValues": [
            "eastus2",
            "eastus",
            "westus2"
        ]

    }
}
```

> [!NOTE]
> Nachdem eine Richtliniendefinition gespeichert wurde, kann die **type**-Eigenschaft eines Parameters nicht mehr geändert werden.

Diese neue Parameterdefinition akzeptiert während der Richtlinienzuweisung mehrere Werte. Wenn die Arrayeigenschaft **allowedValues** definiert ist, werden die bei der Zuweisung verfügbaren Werte zusätzlich auf die vordefinierte Liste von Optionen beschränkt. Die Verwendung von **allowedValues** ist optional.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Übergeben von Werten an ein Parameterarray während der Zuweisung

Wenn Sie die Richtlinie über das Azure-Portal zuweisen, wird ein Parameter vom **Typ** _array_ als ein einzelnes Textfeld angezeigt. Der Hinweis lautet „Trennen Sie die Werte durch Semikola (;). (Beispiel: London;New York)“. Verwenden Sie die folgende Zeichenfolge, um die zulässigen Werte _eastus2_, _eastus_ und _westus2_ für den Ort an den Parameter zu übergeben:

`eastus2;eastus;westus2`

Das Format für den Parameterwert unterscheidet sich bei der Verwendung der Azure-Befehlszeilenschnittstelle (Azure CLI), von Azure PowerShell oder der REST-API. Die Werte werden in Form einer JSON-Zeichenfolge übergeben, die auch den Namen des Parameters enthält.

```json
{
    "allowedLocations": {
        "value": [
            "eastus2",
            "eastus",
            "westus2"
        ]
    }
}
```

Die Befehle zur Verwendung dieser Zeichenfolge mit den einzelnen SDKs lauten wie folgt:

- Azure CLI: Befehl [az policy assignment create](/cli/azure/policy/assignment#az_policy_assignment_create) mit dem Parameter **params**
- Azure PowerShell: Cmdlet [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) mit dem Parameter **PolicyParameter**
- REST-API: Im _PUT_-Vorgang [create](/rest/api/resources/policyassignments/create) als Teil des Anforderungstextes als Wert der **properties.parameters**-Eigenschaft

## <a name="array-conditions"></a>Arraybedingungen

Die [Bedingungen](../concepts/definition-structure.md#conditions) für Richtlinienregeln, mit denen ein Parameter vom 
**Typ**_array_ verwendet werden kann, sind auf `in` und `notIn` beschränkt. Die folgende Richtliniendefinition mit der Bedingung `equals` kann hierfür als Beispiel dienen:

```json
{
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "equals": "[parameters('allowedLocations')]"
      }
    },
    "then": {
      "effect": "audit"
    }
  },
  "parameters": {
    "allowedLocations": {
      "type": "Array",
      "metadata": {
        "description": "The list of allowed locations for resources.",
        "displayName": "Allowed locations",
        "strongType": "location"
      }
    }
  }
}
```

Wenn Sie versuchen, diese Richtliniendefinition über das Azure-Portal zu erstellen, führt dies zu einer Fehlermeldung wie der folgenden:

- „Die Richtlinie ‚{0}‘ konnte aufgrund von Überprüfungsfehlern nicht parametrisiert werden. Überprüfen Sie, ob die Richtlinienparameter richtig definiert sind. Innere Ausnahme: Das Auswertungsergebnis des Sprachausdrucks ‚[parameters('allowedLocations')]‘ ist vom Typ ‚Array‘. Erwartet wird Typ ‚String‘.“

Der erwartete **Typ** der Bedingung `equals` ist _string_. Da **allowedLocations** als **Typ** _array_ definiert ist, wertet die Richtlinien-Engine den Sprachausdruck aus und löst den Fehler aus. Bei den Bedingungen `in` und `notIn` erwartet die Richtlinien-Engine den **Typ** _array_ im Sprachausdruck. Um dieses Problem zu beheben, ändern Sie `equals` in `in` oder `notIn`.

## <a name="referencing-array-resource-properties"></a>Verweisen auf Arrayeigenschaften in Ressourcen

Bei vielen Anwendungsfällen werden die Arrayeigenschaften in der ausgewerteten Ressource benötigt. In einigen Szenarien muss auf ein gesamtes Array verwiesen werden (z. B. zur Überprüfung der Länge). In anderen Szenarien ist es erforderlich, dass eine Bedingung jeweils auf die einzelnen Arraymember angewandt wird (z. B. zum Sicherstellen, dass der Zugriff aus dem Internet durch alle Firewallregeln blockiert wird). Entscheidend zum Schreiben von Bedingungen für diese Szenarien ist die Kenntnis der verschiedenen Methoden, mit denen in Azure Policy auf Ressourceneigenschaften verwiesen werden kann, sowie des Verhaltens dieser Verweise beim Referenzieren von Arrayeigenschaften.

### <a name="referencing-resource-properties"></a>Verweisen auf Ressourceneigenschaften
Auf Ressourceneigenschaften kann in Azure Policy mithilfe von [Aliasen](../concepts/definition-structure.md#aliases) verwiesen werden. Zwei Möglichkeiten stehen zur Verfügung, um auf die Werte einer Ressourceneigenschaft in Azure Policy zu verweisen:

- Verwenden Sie die [field](../concepts/definition-structure.md#fields)-Bedingung, um zu überprüfen, ob **alle** ausgewählten Ressourceneigenschaften eine Bedingung erfüllen. Beispiel:

  ```json
  {
    "field" : "Microsoft.Test/resourceType/property",
    "equals": "value"
  }
  ```

- Verwenden Sie die `field()`-Funktion, um auf den Wert einer Eigenschaft zuzugreifen. Beispiel:

  ```json
  {
    "value": "[take(field('Microsoft.Test/resourceType/property'), 7)]",
    "equals": "prefix_"
  }
  ```

Die field-Bedingung weist ein implizites „alle von“-Verhalten auf. Wenn der Alias eine Sammlung von Werten darstellt, wird mit dieser Bedingung überprüft, ob alle einzelnen Werte die Bedingung erfüllen. Die `field()`-Funktion gibt die durch den Alias dargestellten Werte unverändert zurück, die dann über andere Vorlagenfunktionen bearbeitet werden können.

### <a name="referencing-array-fields"></a>Verweisen auf Arrayfelder

Arrayeigenschaften in Ressourcen werden normalerweise durch zwei unterschiedliche Typen von Aliasen dargestellt. Durch einen „normalen“ Alias und [Arrayaliase](../concepts/definition-structure.md#understanding-the--alias), an die `[*]` angefügt ist:

- `Microsoft.Test/resourceType/stringArray`
- `Microsoft.Test/resourceType/stringArray[*]`

#### <a name="referencing-the-array"></a>Verweisen auf das Array

Der erste Alias stellt einen einzelnen Wert dar: den Wert der `stringArray`-Eigenschaft aus dem Anforderungsinhalt. Da der Wert dieser Eigenschaft ein Array ist, ist er in Richtlinienbedingungen nicht sehr nützlich. Beispiel:

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "equals": "..."
}
```

Diese Bedingung vergleicht das gesamte `stringArray`-Array mit einem einzelnen Zeichenfolgenwert. Die meisten Bedingungen (einschließlich `equals`) unterstützen nur Zeichenfolgenwerte, sodass der Vergleich eines Arrays mit einer Zeichenfolge wenig sinnvoll ist. Das Hauptszenario, in dem der Verweis auf die Arrayeigenschaft sinnvoll ist, stellt die Überprüfung ihres Vorhandenseins dar:

```json
{
  "field": "Microsoft.Test/resourceType/stringArray",
  "exists": "true"
}
```

Bei der `field()`-Funktion ist der zurückgegebene Wert das Array aus dem Anforderungsinhalt, das dann mit allen [unterstützten Vorlagenfunktionen](../concepts/definition-structure.md#policy-functions) verwendet werden kann, die Arrayargumente unterstützen. Mit der folgenden Bedingung wird beispielsweise überprüft, ob die Länge von `stringArray` größer als 0 (null) ist:

```json
{
  "value": "[length(field('Microsoft.Test/resourceType/stringArray'))]",
  "greater": 0
}
```

#### <a name="referencing-the-array-members-collection"></a>Verweisen auf die Sammlung der Arraymember

Aliase, die die `[*]`-Syntax verwenden, stellen eine **Sammlung von aus einer Arrayeigenschaft ausgewählten Eigenschaftswerten** dar. Dies unterscheidet sich von der Auswahl der Arrayeigenschaft selbst. Im Fall von `Microsoft.Test/resourceType/stringArray[*]` wird eine Sammlung zurückgegeben, die alle Member von `stringArray` enthält. Wie zuvor erwähnt, überprüft eine `field`-Bedingung, ob alle ausgewählten Ressourceneigenschaften die Bedingung erfüllen. Daher ist die folgende Bedingung nur TRUE, wenn **alle** Member von `stringArray` gleich „value“ sind.

```json
{
  "field": "Microsoft.Test/resourceType/stringArray[*]",
  "equals": "value"
}
```

Wenn das Array Objekte enthält, kann ein `[*]`-Alias verwendet werden, um den Wert einer bestimmten Eigenschaft aus jedem Arraymember auszuwählen. Beispiel:

```json
{
  "field": "Microsoft.Test/resourceType/objectArray[*].property",
  "equals": "value"
}
```

Diese Bedingung ist TRUE, wenn die Werte aller `property`-Eigenschaften in `objectArray` gleich `"value"` sind.

Wenn Sie die `field()`-Funktion verwenden, um auf einen Arrayalias zu verweisen, ist der zurückgegebene Wert ein Array aller ausgewählten Werte. Durch dieses Verhalten wird der gängige Anwendungsfall der `field()`-Funktion, d. h. die Fähigkeit zum Anwenden von Vorlagenfunktionen auf Ressourceneigenschaftswerte, stark eingeschränkt. In diesem Fall können nur Vorlagenfunktionen verwendet werden, die Arrayargumente unterstützen. Beispielsweise ist es möglich, mit `[length(field('Microsoft.Test/resourceType/objectArray[*].property'))]` die Länge des Arrays abzurufen. Komplexere Szenarien wie das Anwenden einer Vorlagenfunktion auf die einzelnen Arraymember und das Vergleichen mit einem gewünschten Wert sind jedoch nur bei Verwendung des Ausdrucks `count` möglich. Weitere Informationen finden Sie unter [count-Ausdrücke](#count-expressions).

Zur Zusammenfassung folgt ein Beispiel für einen Ressourceninhalt und die von verschiedenen Aliasen zurückgegebenen ausgewählten Werte:

```json
{
  "tags": {
    "env": "prod"
  },
  "properties":
  {
    "stringArray": [ "a", "b", "c" ],
    "objectArray": [
      {
        "property": "value1",
        "nestedArray": [ 1, 2 ]
      },
      {
        "property": "value2",
        "nestedArray": [ 3, 4 ]
      }
    ]
  }
}
```

Bei Verwendung der field-Bedingung im Beispielressourceninhalt sehen die Ergebnisse wie folgt aus:

| Alias | Ausgewählte Werte |
|:--- |:---|
| `Microsoft.Test/resourceType/missingArray` | `null` |
| `Microsoft.Test/resourceType/missingArray[*]` | Leere Sammlung von Werten |
| `Microsoft.Test/resourceType/missingArray[*].property` | Leere Sammlung von Werten |
| `Microsoft.Test/resourceType/stringArray` | `["a", "b", "c"]` |
| `Microsoft.Test/resourceType/stringArray[*]` | `"a"`, `"b"`, `"c"` |
| `Microsoft.Test/resourceType/objectArray[*]` |  `{ "property": "value1", "nestedArray": [ 1, 2 ] }`,<br/>`{ "property": "value2", "nestedArray": [ 3, 4 ] }`|
| `Microsoft.Test/resourceType/objectArray[*].property` | `"value1"`, `"value2"` |
| `Microsoft.Test/resourceType/objectArray[*].nestedArray` | `[ 1, 2 ]`, `[ 3, 4 ]` |
| `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` | `1`, `2`, `3`, `4` |

Bei Verwendung der `field()`-Funktion im Beispielressourceninhalt sehen die Ergebnisse wie folgt aus:

| Ausdruck | Rückgabewert |
|:--- |:---|
| `[field('Microsoft.Test/resourceType/missingArray')]` | `""` |
| `[field('Microsoft.Test/resourceType/missingArray[*]')]` | `[]` |
| `[field('Microsoft.Test/resourceType/missingArray[*].property')]` | `[]` |
| `[field('Microsoft.Test/resourceType/stringArray')]` | `["a", "b", "c"]` |
| `[field('Microsoft.Test/resourceType/stringArray[*]')]` | `["a", "b", "c"]` |
| `[field('Microsoft.Test/resourceType/objectArray[*]')]` |  `[{ "property": "value1", "nestedArray": [ 1, 2 ] }, { "property": "value2", "nestedArray": [ 3, 4 ] }]`|
| `[field('Microsoft.Test/resourceType/objectArray[*].property')]` | `["value1", "value2"]` |
| `[field('Microsoft.Test/resourceType/objectArray[*].nestedArray')]` | `[[ 1, 2 ], [ 3, 4 ]]` |
| `[field('Microsoft.Test/resourceType/objectArray[*].nestedArray[*]')]` | `[1, 2, 3, 4]` |

## <a name="count-expressions"></a>count-Ausdrücke

[count](../concepts/definition-structure.md#count)-Ausdrücke zählen, wie viele Arraymember eine Bedingung erfüllen, und vergleichen die Anzahl mit einem Zielwert. `Count` ist im Vergleich zu `field` Bedingungen intuitiver und vielseitiger anwendbar für das Auswerten von Arrays. Die Syntax ist:

```json
{
  "count": {
    "field": <[*] alias>,
    "where": <optional policy condition expression>
  },
  "equals|greater|less|any other operator": <target value>
}
```

Bei Verwendung ohne eine „where“-Bedingung gibt `count` lediglich die Länge eines Arrays zurück. Mit dem Beispielressourceninhalt aus dem vorherigen Abschnitt wird der folgende `count`-Ausdruck als `true` ausgewertet, da `stringArray` drei Member hat:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]"
  },
  "equals": 3
}
```

Dieses Verhalten gilt auch bei geschachtelten Arrays. Beispielsweise wird der folgende `count`-Ausdruck als `true` ausgewertet, da in den `nestedArray`-Arrays vier Arraymember enthalten sind:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]"
  },
  "greaterOrEquals": 4
}
```

Die eigentliche Stärke von `count` zeigt sich in der `where`-Bedingung. Wenn diese angegeben ist, werden in Azure Policy die Arraymember gezählt und jeweils für die Bedingung ausgewertet. Dabei wird gezählt, wie viele Arraymember als `true` ausgewertet werden. Konkret wird in Azure Policy in jeder Iteration der Auswertung der `where`-Bedingung der einzelne Arraymember ***i** _ ausgewählt und der Ressourceninhalt für die `where`-Bedingung ausgewertet, _*als ob **_i_*_ der einzige Member des Arrays_* wäre. Die Tatsache, dass in jeder Iteration nur ein Arraymember zur Verfügung steht, bietet die Möglichkeit, komplexe Bedingungen auf jeden einzelnen Arraymember anzuwenden.

Beispiel:
```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "a"
    }
  },
  "equals": 1
}
```
Um den `count`-Ausdruck auszuwerten, wird die `where`-Bedingung in Azure Policy dreimal ausgewertet, einmal für jeden Member von `stringArray`. Dabei wird gezählt, wie viele Male sie als `true` ausgewertet wird. Wenn die `where`-Bedingung auf die Arraymember von `Microsoft.Test/resourceType/stringArray[*]` verweist, anstatt dass alle Member von `stringArray` ausgewählt werden, wird jeweils nur ein einzelner Arraymember ausgewählt:

| Iteration | Ausgewählte Werte von `Microsoft.Test/resourceType/stringArray[*]` | Ergebnis der Auswertung von `where` |
|:---|:---|:---|
| 1 | `"a"` | `true` |
| 2 | `"b"` | `false` |
| 3 | `"c"` | `false` |

Daher gibt `count` den Wert `1` zurück.

Hier ein komplexerer Ausdruck:
```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "allOf": [
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].property",
          "equals": "value2"
        },
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
          "greater": 2
        }
      ]
    }
  },
  "equals": 1
}
```

| Iteration | Ausgewählte Werte | Ergebnis der Auswertung von `where` |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | `false` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2"` </br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4`| `true` |

Daher gibt `count` den Wert `1` zurück.

Aufgrund der Tatsache, dass der `where`-Ausdruck für den **gesamten** Anforderungsinhalt ausgewertet wird (mit Änderungen nur am aktuell ausgewerteten Arraymember), kann die `where`-Bedingung auch auf Felder außerhalb des Arrays verweisen:
```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "field": "tags.env",
      "equals": "prod"
    }
  }
}
```

| Iteration | Ausgewählte Werte | Ergebnis der Auswertung von `where` |
|:---|:---|:---|
| 1 | `tags.env` => `"prod"` | `true` |
| 2 | `tags.env` => `"prod"` | `true` |

Auch geschachtelte count-Ausdrücke sind zulässig:
```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/objectArray[*]",
    "where": {
      "allOf": [
        {
          "field": "Microsoft.Test/resourceType/objectArray[*].property",
          "equals": "value2"
        },
        {
          "count": {
            "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
            "where": {
              "field": "Microsoft.Test/resourceType/objectArray[*].nestedArray[*]",
              "equals": 3
            },
            "greater": 0
          }
        }
      ]
    }
  }
}
```
 
| Äußere Schleifeniteration | Ausgewählte Werte | Innere Schleifeniteration | Ausgewählte Werte |
|:---|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1` |
| 1 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value1`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `1`, `2` | 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `2` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | 1 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3` |
| 2 | `Microsoft.Test/resourceType/objectArray[*].property` => `"value2`</br> `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `3`, `4` | 2 | `Microsoft.Test/resourceType/objectArray[*].nestedArray[*]` => `4` |

### <a name="the-field-function-inside-where-conditions"></a>Die `field()`-Funktion innerhalb von `where`-Bedingungen

Das Verhalten von `field()`-Funktionen innerhalb einer `where`-Bedingung basiert auf den folgenden Konzepten:
1. Arrayaliase werden in einer Sammlung von Werten aufgelöst, die aus allen Arraymembern ausgewählt werden.
1. `field()`-Funktionen, die auf Arrayaliase verweisen, geben ein Array mit den ausgewählten Werten zurück.
1. Durch Verweisen auf den gezählten Arrayalias innerhalb der `where`-Bedingung wird eine Sammlung mit einem einzelnen Wert zurückgegeben, der aus dem Arraymember ausgewählt wird, der in der aktuellen Iteration ausgewertet wird.

Durch dieses Verhalten wird beim Verweisen auf den gezählten Arraymember mit einer `field()`-Funktion innerhalb der `where`-Bedingung **ein Array mit einem einzelnen Member** zurückgegeben. Auch wenn dies vielleicht nicht intuitiv ist, entspricht es dem Grundgedanken, dass Arrayaliase immer eine Sammlung von ausgewählten Eigenschaften zurückgeben. Hier sehen Sie ein Beispiel:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "[field('Microsoft.Test/resourceType/stringArray[*]')]"
    }
  },
  "equals": 0
}
```

| Iteration | Ausdruckswerte | Ergebnis der Auswertung von `where` |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "a" ]` | `false` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "b" ]` | `false` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[field('Microsoft.Test/resourceType/stringArray[*]')]` => `[ "c" ]` | `false` |

Wenn also mit einer `field()`-Funktion auf den Wert des gezählten Arrayalias zugegriffen werden soll, ist der Wert mit einer `first()`-Vorlagenfunktion zu umschließen:

```json
{
  "count": {
    "field": "Microsoft.Test/resourceType/stringArray[*]",
    "where": {
      "field": "Microsoft.Test/resourceType/stringArray[*]",
      "equals": "[first(field('Microsoft.Test/resourceType/stringArray[*]'))]"
    }
  }
}
```

| Iteration | Ausdruckswerte | Ergebnis der Auswertung von `where` |
|:---|:---|:---|
| 1 | `Microsoft.Test/resourceType/stringArray[*]` => `"a"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"a"` | `true` |
| 2 | `Microsoft.Test/resourceType/stringArray[*]` => `"b"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"b"` | `true` |
| 3 | `Microsoft.Test/resourceType/stringArray[*]` => `"c"` </br>  `[first(field('Microsoft.Test/resourceType/stringArray[*]'))]` => `"c"` | `true` |

Nützliche Beispiele finden Sie unter [Beispiele für „count“](../concepts/definition-structure.md#count-examples).

## <a name="modifying-arrays"></a>Ändern von Arrays

[append](../concepts/effects.md#append) und [modify](../concepts/effects.md#modify) ändern Eigenschaften von Ressourcen bei deren Erstellung oder einem Update. Beim Arbeiten mit Arrayeigenschaften hängt das Verhalten bei diesen Auswirkungen davon ab, ob der Vorgang versucht, den **\[\*\]** -Alias zu ändern:

> [!NOTE]
> Die Verwendung des `modify`-Effekts mit Aliasen befindet sich derzeit in der **Vorschau**.

|Alias |Wirkung | Ergebnis |
|-|-|-|
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `append` | Azure Policy fügt das gesamte in den Auswirkungsdetails angegebene Array an, wenn nicht vorhanden. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `modify` mit `add`-Vorgang | Azure Policy fügt das gesamte in den Auswirkungsdetails angegebene Array an, wenn nicht vorhanden. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules` | `modify` mit `addOrReplace`-Vorgang | Azure Policy fügt das gesamte in den Auswirkungsdetails angegebene Array an, wenn nicht vorhanden, oder ersetzt das vorhandene Array. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `append` | Azure Policy fügt den in den Auswirkungsdetails angegebenen Arraymember an, wenn nicht vorhanden. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `modify` mit `add`-Vorgang | Azure Policy fügt den in den Auswirkungsdetails angegebenen Arraymember an, wenn nicht vorhanden. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | `modify` mit `addOrReplace`-Vorgang | Azure Policy entfernt alle vorhandenen Arraymember und fügt den in den Auswirkungsdetails angegebenen Arraymember an. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `append` | Azure Policy fügt einen Wert an die `action`-Eigenschaft der einzelnen Arraymember an. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `modify` mit `add`-Vorgang | Azure Policy fügt einen Wert an die `action`-Eigenschaft der einzelnen Arraymember an. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | `modify` mit `addOrReplace`-Vorgang | Azure Policy fügt bei den einzelnen Arraymembern die `action`-Eigenschaft an oder ersetzt sie, wenn vorhanden. |

Weitere Informationen finden Sie unter [Beispiele für „append“](../concepts/effects.md#append-examples).

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die Beispiele unter [Azure Policy-Beispiele](../samples/index.md) an.
- Lesen Sie die Informationen unter [Struktur von Azure Policy-Definitionen](../concepts/definition-structure.md).
- Lesen Sie [Grundlegendes zu Richtlinienauswirkungen](../concepts/effects.md).
- Informieren Sie sich über das [programmgesteuerte Erstellen von Richtlinien](programmatically-create.md).
- Erfahren Sie, wie Sie [nicht konforme Ressourcen korrigieren](remediate-resources.md) können.
- Weitere Informationen zu Verwaltungsgruppen finden Sie unter [Organisieren Ihrer Ressourcen mit Azure-Verwaltungsgruppen](../../management-groups/overview.md).
