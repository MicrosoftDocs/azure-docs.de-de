---
title: Details der Struktur von Richtliniendefinitionen
description: Beschreibt, wie die von Azure Policy verwendete Definition von Ressourcenrichtlinien es Ihnen ermöglicht, Konventionen für Ressourcen in Ihrer Organisation einzurichten, indem Sie beschreiben, wann die Richtlinie erzwungen werden soll und welche Auswirkungen erfolgen sollen.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/13/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 91dd1ebc457bfeed5c9e8d0d62ecc23740ca5d8d
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65979549"
---
# <a name="azure-policy-definition-structure"></a>Struktur von Azure Policy-Definitionen

Definitionen von Ressourcenrichtlinien werden von Azure Policy verwendet, um Konventionen für Ressourcen festzulegen. Jede Definition beschreibt Ressourcenkonformität und welche Maßnahme ergriffen werden soll, wenn eine Ressource nicht konform ist.
Durch Definieren von Konventionen können Sie Kosten beeinflussen und Ihre Ressourcen einfacher verwalten. Sie können beispielsweise angeben, dass nur bestimmte Typen virtueller Computer zulässig sind. Oder Sie können festlegen, dass alle Ressourcen ein bestimmtes Tag aufweisen. Richtlinien werden von allen untergeordneten Ressourcen geerbt. Wenn eine Richtlinie auf eine Ressourcengruppe angewendet wird, gilt sie für alle Ressourcen in dieser Ressourcengruppe.

Das von Azure Policy verwendete Schema finden Sie hier: [https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json](https://schema.management.azure.com/schemas/2018-05-01/policyDefinition.json)

Eine Richtliniendefinition wird mithilfe von JSON erstellt. Die Richtliniendefinition enthält Elemente für Folgendes:

- Modus
- parameters
- Anzeigename
- description
- Richtlinienregel
  - Logische Auswertung
  - Wirkung

Die folgende JSON-Datei zeigt beispielsweise eine Richtlinie, die einschränkt, wo Ressourcen bereitgestellt werden:

```json
{
    "properties": {
        "mode": "all",
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                },
                "defaultValue": [ "westus2" ]
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

Alle Azure Policy-Beispiele sind unter [Azure Policy-Beispiele](../samples/index.md) verfügbar.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="mode"></a>Mode

Der Modus (**mode**) bestimmt, welche Ressourcentypen für eine Richtlinie ausgewertet werden. Unterstützte Modi:

- `all`: Ressourcengruppen und alle Ressourcentypen werden ausgewertet.
- `indexed`: Nur Ressourcentypen, die Tags und Speicherort unterstützen, werden ausgewertet.

Es wird empfohlen, **mode** in den meisten Fällen auf `all` zu setzen. Alle über das Portal erstellten Richtliniendefinitionen verwenden für „mode“ die Option `all`. Wenn Sie PowerShell oder die Azure CLI verwenden, können Sie den **mode**-Parameter manuell angeben. Wenn die Richtliniendefinition keinen Wert für **mode** enthält, wird dieser in Azure PowerShell standardmäßig auf `all` und in der Azure CLI auf `null` festgelegt. Der Modus `null` entspricht dem Verwenden von `indexed`, um Abwärtskompatibilität zu unterstützen.

`indexed` sollte beim Erstellen von Richtlinien verwendet werden, die Tags oder Speicherorte erzwingen. Dies ist nicht erforderlich, verhindert aber, dass Ressourcen, die keine Tags und Speicherorte unterstützen, bei der Konformitätsprüfung als nicht konform angezeigt werden. Die Ausnahme sind **Ressourcengruppen**. Richtlinien zum Erzwingen von Speicherort oder Tags für eine Ressourcengruppe sollten **mode** auf `all` festlegen und speziell auf den Typ `Microsoft.Resources/subscriptions/resourceGroups` abzielen. Ein Beispiel finden Sie unter [Ressourcengruppen-Tags erzwingen](../samples/enforce-tag-rg.md). Eine Liste der Ressourcen, die Tags unterstützen, finden Sie unter [Tagunterstützung für Azure-Ressourcen](../../../azure-resource-manager/tag-support.md).

## <a name="parameters"></a>Parameter

Parameter vereinfachen Ihre Richtlinienverwaltung, indem sie die Anzahl von Richtliniendefinitionen reduzieren. Es handelt sich dabei z.B. um Parameter wie die folgenden Felder auf einem Formular: `name`, `address`, `city`, `state`. Diese Parameter bleiben immer gleich, allerdings ändern sich ihre Werte auf Grundlage der Einträge des Einzelnen.
Parameter funktionieren beim Erstellen von Richtlinien genauso. Sie können die Richtlinie für verschiedene Szenarios wiederverwenden, indem Sie Parameter in eine Richtliniendefinition einbeziehen und verschiedene Werte verwenden.

> [!NOTE]
> Parameter können einer vorhandenen und zugewiesenen Definition hinzugefügt werden. Der neue Parameter muss die **defaultValue**-Eigenschaft enthalten. Dadurch wird verhindert, dass vorhandene Zuweisungen der Richtlinie oder Initiative indirekt als ungültig erklärt werden.

### <a name="parameter-properties"></a>Parametereigenschaften

Ein Parameter hat die folgenden Eigenschaften, die in der Richtliniendefinition verwendet werden:

- **name:** Der Name des Parameters. Wird in der Richtlinienregel von der Bereitstellungsfunktion `parameters` verwendet. Weitere Informationen finden Sie unter [Verwenden eines Parameterwerts](#using-a-parameter-value).
- `type`: Bestimmt, ob der Parameter eine **Zeichenfolge** oder ein **Array** ist.
- `metadata`: Definiert untergeordnete Eigenschaften, die hauptsächlich vom Azure-Portal verwendet werden, um benutzerfreundliche Informationen anzuzeigen:
  - `description`: Die Erläuterung des Zwecks des Parameters. Kann verwendet werden, um Beispiele zulässiger Werte bereitzustellen.
  - `displayName`: Der Anzeigename des Parameters im Portal.
  - `strongType`: (Optional) Wird verwendet, wenn die Richtliniendefinition über das Portal zugewiesen wird. Bietet eine kontextbezogene Liste. Weitere Informationen finden Sie unter [strongType](#strongtype).
  - `assignPermissions`: (Optional) Legen Sie diesen Wert auf _true_ fest, damit das Azure-Portal während der Richtlinienzuweisung Rollenzuweisungen erstellt. Diese Eigenschaft ist hilfreich, wenn Sie Berechtigungen außerhalb des Zuweisungsbereichs zuweisen möchten. Es gibt eine Rollenzuordnung pro Rollendefinition in der Richtlinie (oder pro Rollendefinition in allen Richtlinien der Initiative). Der Parameterwert muss eine gültige Ressource oder ein gültiger Bereich sein.
- `defaultValue`: (Optional) Legt den Wert des Parameters in einer Zuweisung fest, wenn kein Wert angegeben ist.
  Erforderlich, wenn eine vorhandene zugewiesene Richtliniendefinition aktualisiert wird.
- `allowedValues`: (Optional) Stellt ein Array von Werten bereit, die der Parameter bei der Zuweisung akzeptiert.

Beispielsweise können Sie eine Richtliniendefinition verwenden, um die Speicherorte einzuschränken, an denen Ressourcen bereitgestellt werden können. Ein Parameter für diese Richtliniendefinition kann **allowedLocations** heißen. Dieser Parameter kann bei jeder Zuweisung der Richtliniendefinition verwendet werden, um die akzeptierten Werte zu begrenzen. Die Verwendung von **strongType** bietet erweiterte Möglichkeiten, wenn die Zuweisung über das Portal erfolgt:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": [ "westus2" ],
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="using-a-parameter-value"></a>Verwenden eines Parameterwerts

In der Richtlinienregel wird die folgende Syntax für die `parameters`Bereitstellungswertefunktion verwendet, um auf Parameter zu verweisen:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

In diesem Beispiel wird auf den Parameter **allowedLocations** verwiesen, der unter [Parametereigenschaften](#parameter-properties) vorgestellt wurde.

### <a name="strongtype"></a>strongType

Innerhalb der `metadata`-Eigenschaft können Sie mit **strongType** im Azure-Portal eine Liste der Optionen mit Mehrfachauswahl angeben. Derzeit zulässige Werte für **strongType** sind:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`
- `omsWorkspace`
- `Microsoft.EventHub/Namespaces/EventHubs`
- `Microsoft.EventHub/Namespaces/EventHubs/AuthorizationRules`
- `Microsoft.EventHub/Namespaces/AuthorizationRules`
- `Microsoft.RecoveryServices/vaults`
- `Microsoft.RecoveryServices/vaults/backupPolicies`

## <a name="definition-location"></a>Definitionsspeicherort

Beim Erstellen einer Initiative oder Richtlinie muss der Speicherort der Definition angegeben werden. Dieser Speicherort muss eine Verwaltungsgruppe oder ein Abonnement sein. Er bestimmt den Bereich, dem die Initiative oder Richtlinie zugewiesen werden kann. Ressourcen müssen direkte Mitglieder oder untergeordnete Elemente innerhalb der Hierarchie des Definitionsspeicherorts für die Zuweisung sein.

Für den Definitionsspeicherort gilt Folgendes:

- **Abonnement:** Die Richtlinie kann nur Ressourcen innerhalb dieses Abonnements zugewiesen werden.
- **Verwaltungsgruppe:** Die Richtlinie kann nur Ressourcen innerhalb untergeordneter Verwaltungsgruppen und untergeordneter Abonnements zugewiesen werden. Wenn Sie diese Richtliniendefinition mehreren Abonnements zuordnen möchten, muss der Speicherort eine Verwaltungsgruppe sein, die diese Abonnements enthält.

## <a name="display-name-and-description"></a>Anzeigename und Beschreibung

Sie verwenden **displayName** und **description**, um die Richtliniendefinition zu bestimmen und den Kontext für ihre Verwendung anzugeben. **displayName** hat eine maximale Länge von _128_ Zeichen, und **description** hat eine maximale Länge von _512_ Zeichen.

## <a name="policy-rule"></a>Richtlinienregel

Die Richtlinienregel besteht aus **If**- und **Then**-Blöcken. Im **If**-Block definieren Sie mindestens eine Bedingung, die angibt, wann die Richtlinie erzwungen wird. Auf diese Bedingungen können logische Operatoren angewendet werden, um das Szenario für eine Richtlinie präzise zu definieren.

Im **Then**-Block definieren Sie die Wirkung, die eintritt, wenn die **If**-Bedingungen erfüllt sind.

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | append | auditIfNotExists | deployIfNotExists | disabled"
    }
}
```

### <a name="logical-operators"></a>Logische Operatoren

Folgende logische Operatoren werden unterstützt:

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

Die **not**-Syntax kehrt das Ergebnis der Bedingung um. Die **allOf**-Syntax gleicht der logischen **And**-Operation und erfordert, dass alle Bedingungen erfüllt sind. Die **anyOf**-Syntax gleicht der logischen **Or**-Operation und erfordert, dass mindestens eine Bedingung erfüllt ist.

Logische Operatoren können geschachtelt werden. Das folgende Beispiel zeigt eine **not**-Operation, die in einer **allOf**-Operation geschachtelt ist.

```json
"if": {
    "allOf": [{
            "not": {
                "field": "tags",
                "containsKey": "application"
            }
        },
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        }
    ]
},
```

### <a name="conditions"></a>Bedingungen

Eine Bedingung prüft, ob ein **Feld** oder der Accessor **Wert** bestimmte Kriterien erfüllt. Folgende Bedingungen werden unterstützt:

- `"equals": "value"`
- `"notEquals": "value"`
- `"like": "value"`
- `"notLike": "value"`
- `"match": "value"`
- `"matchInsensitively": "value"`
- `"notMatch": "value"`
- `"notMatchInsensitively": "value"`
- `"contains": "value"`
- `"notContains": "value"`
- `"in": ["value1","value2"]`
- `"notIn": ["value1","value2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"less": "value"`
- `"lessOrEquals": "value"`
- `"greater": "value"`
- `"greaterOrEquals": "value"`
- `"exists": "bool"`

Bei Verwendung der Bedingungen **like** und **notLike** können Sie im Wert den Platzhalter `*` angeben.
Der Wert darf maximal einen Platzhalter des Typs `*` enthalten.

Geben Sie bei Verwendung der Bedingungen **match** und **notMatch** für eine Ziffer `#`, für einen Buchstaben `?`, für irgendein Zeichen `.` und für ein Zeichen das gewünschte Zeichen ein.
Bei **match** und **notMatch** muss die Groß-/Kleinschreibung beachtet werden. Als Alternativen, bei denen die Groß-/Kleinschreibung nicht beachtet werden muss, stehen **matchInsensitively** und **notMatchInsensitively** zur Verfügung. Beispiele finden Sie unter [Zulassen mehrerer Namensmuster](../samples/allow-multiple-name-patterns.md).

### <a name="fields"></a>Felder

Bedingungen werden mithilfe von Feldern gebildet. Ein Feld stimmt mit Eigenschaften in der Anforderungsnutzlast einer Ressource überein und beschreibt den Zustand der Ressource.

Folgende Felder werden unterstützt:

- `name`
- `fullName`
  - Gibt den vollständigen Namen der Ressource zurück. Der vollständige Name einer Ressource setzt sich zusammen aus dem Namen der Ressource und den vorangestellten Namen übergeordneter Ressourcen, sofern vorhanden (Beispiel: meinServer/meineDatenbank).
- `kind`
- `type`
- `location`
  - Verwenden Sie **global** für Ressourcen, die speicherortagnostisch sind. Ein Beispiel finden Sie unter [Beispiele: Zulässige Speicherorte](../samples/allowed-locations.md).
- `identity.type`
  - Gibt den Typ [Verwaltete Identität](../../../active-directory/managed-identities-azure-resources/overview.md) zurück, der für die Ressource aktiviert ist.
- `tags`
- `tags['<tagName>']`
  - Diese Klammersyntax unterstützt Tagnamen, die Satzzeichen wie Bindestriche, Punkte oder Leerzeichen enthalten.
  - Wobei **\<tagName\>** der Name des Tags ist, auf das die Bedingung geprüft wird.
  - Beispiele: `tags['Acct.CostCenter']`, wobei **Acct.CostCenter** der Name des Tags ist.
- `tags['''<tagName>''']`
  - Diese Klammersyntax unterstützt Tagnamen, die Apostrophe enthalten, und verwendet doppelte Apostrophe als Escapezeichen.
  - Wobei **'\<tagName\>'** der Name des Tags ist, auf das die Bedingung geprüft wird.
  - Beispiel: `tags['''My.Apostrophe.Tag''']`, wobei **'\<tagName\>'** der Name des Tags ist.
- Eigenschaftenaliase – Eine Liste finden Sie unter [Aliase](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]` und `tags[tag.with.dots]` werden weiterhin als Möglichkeiten zum Deklarieren eines Felds für Tags akzeptiert. Die oben aufgeführten Ausdrücke werden jedoch bevorzugt.

#### <a name="use-tags-with-parameters"></a>Verwenden von Tags mit Parametern

Ein Parameterwert kann an ein Tagfeld übergeben werden. Das Übergeben eines Parameters an ein Tagfeld erhöht die Flexibilität der Richtliniendefinition während der Richtlinienzuweisung.

Im folgenden Beispiel wird mit `concat` eine Tagfeldsuche nach dem Tag erstellt, das als Namen den Wert des **TagName**-Parameters aufweist. Wenn dieses Tag nicht vorhanden ist, wird die Auswirkung **append** verwendet, um mithilfe der Nachschlagefunktion `resourcegroup()` das Tag mit dem Wert dieses benannten Tags hinzuzufügen, das für die übergeordnete Ressourcengruppe der überwachten Ressourcen festgelegt ist.

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "append",
        "details": [{
            "field": "[concat('tags[', parameters('tagName'), ']')]",
            "value": "[resourcegroup().tags[parameters('tagName')]]"
        }]
    }
}
```

### <a name="value"></a>Wert

Bedingungen können auch mithilfe von **Wert** gebildet werden. **Wert** gleicht die Bedingungen mit [Parametern](#parameters), [unterstützten Vorlagenfunktionen](#policy-functions) oder Literalen ab.
**Wert** wird mit beliebigen unterstützten [Bedingungen](#conditions) verknüpft.

> [!WARNING]
> Wenn eine _Vorlagenfunktion_ einen Fehler ergibt, schlägt die Richtlinienauswertung fehl. Eine fehlerhafte Auswertung ist ein implizites **Deny** (Verweigern). Weitere Informationen finden Sie unter [Vermeiden von Vorlagenfehlern](#avoiding-template-failures).

#### <a name="value-examples"></a>Beispiele von Werten

Dieses Beispiel einer Richtlinienregel verwendet **Wert**, um das Ergebnis der `resourceGroup()`-Funktion und der zurückgegebenen **name**-Eigenschaft mit der **like**-Bedingung `*netrg` zu vergleichen. Die Regel verweigert Ressourcen, die nicht den **Typ** `Microsoft.Network/*` haben, in Ressourcengruppen, deren Name mit `*netrg` endet.

```json
{
    "if": {
        "allOf": [{
                "value": "[resourceGroup().name]",
                "like": "*netrg"
            },
            {
                "field": "type",
                "notLike": "Microsoft.Network/*"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

Diese Beispiel einer Regelrichtlinie verwendet **Wert**, um zu überprüfen, ob das Ergebnis mehrerer geschachtelter Funktionen **gleich** `true` ist. Die Regel verweigert alle Ressourcen, die nicht mindestens drei Tags haben.

```json
{
    "mode": "indexed",
    "policyRule": {
        "if": {
            "value": "[less(length(field('tags')), 3)]",
            "equals": true
        },
        "then": {
            "effect": "deny"
        }
    }
}
```

#### <a name="avoiding-template-failures"></a>Vermeiden von Vorlagenfehlern

Wenn Sie _Vorlagenfunktionen_ als **Wert** verwenden, sind viele komplexe und verschachtelte Funktionen möglich. Wenn eine _Vorlagenfunktion_ einen Fehler ergibt, schlägt die Richtlinienauswertung fehl. Eine fehlerhafte Auswertung ist ein implizites **Deny** (Verweigern). Beispiel für einen **Wert**, bei dem in bestimmten Szenarios ein Fehler auftritt:

```json
{
    "policyRule": {
        "if": {
            "value": "[substring(field('name'), 0, 3)]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

Bei der oben als Beispiel verwendeten Richtlinienregel wird [substring()](../../../azure-resource-manager/resource-group-template-functions-string.md#substring) zum Vergleichen der ersten drei Zeichen des **Namens** mit **abc** verwendet. Wenn der **Name** kürzer als drei Zeichen ist, ergibt die `substring()`-Funktion einen Fehler. Dieser Fehler löst bei der Richtlinie den **Deny**-Effekt aus.

Verwenden Sie stattdessen die [if()](../../../azure-resource-manager/resource-group-template-functions-logical.md#if)-Funktion, um zu überprüfen, ob die ersten drei Zeichen des **Namens** gleich **abc** sind, ohne dass ein **Name**, der kürzer als drei Zeichen ist, einen Fehler verursachen kann:

```json
{
    "policyRule": {
        "if": {
            "value": "[if(greaterOrEquals(length(field('name')), 3), substring(field('name'), 0, 3), 'not starting with abc')]",
            "equals": "abc"
        },
        "then": {
            "effect": "audit"
        }
    }
}
```

Mit der überarbeiteten Richtlinienregel überprüft `if()` die Länge des **Namens**, bevor es versucht, einen `substring()` für einen Wert mit weniger als drei Zeichen abzurufen. Wenn der **Name** zu kurz ist, wird der Wert, der „nicht mit abc beginnt“, zurückgegeben und mit **abc** verglichen. Eine Ressource mit einem Kurznamen, der nicht mit **abc** beginnt, erzeugt zwar immer noch einen Fehler bei der Richtlinienregel, verursacht aber bei der Auswertung keinen Fehler mehr.

### <a name="effect"></a>Wirkung

Azure Policy unterstützt die folgenden Auswirkungstypen:

- **Deny** generiert ein Ereignis im Aktivitätsprotokoll und führt zu einem Fehler bei der Anforderung.
- **Audit** generiert eine Warnung im Überwachungsprotokoll, führt jedoch nicht zu einem Fehler bei der Anforderung.
- **Append** fügt der Anforderung verschiedene definierte Felder hinzu.
- **AuditIfNotExists** aktiviert das Überwachen, wenn eine Ressource nicht vorhanden ist.
- **DeployIfNotExists** stellt eine Ressource bereit, falls noch keine vorhanden ist.
- **Deaktiviert** wertet Ressourcen nicht auf Konformität mit der Richtlinienregel aus.

Für **append**müssen Sie die folgenden Details angeben:

```json
"effect": "append",
"details": [{
    "field": "field name",
    "value": "value of the field"
}]
```

Der Wert kann entweder eine Zeichenfolge oder ein Objekt im JSON-Format sein.

Mit **AuditIfNotExists** und **DeployIfNotExists** können Sie das Vorhandensein einer zugehörigen Ressource auswerten und eine Regel anwenden. Wenn die Ressource nicht mit die Regel übereinstimmt, wird die Auswirkung implementiert. Sie können z.B. erforderlich machen, dass ein Network Watcher für alle virtuellen Netzwerke bereitgestellt wird. Weitere Informationen finden Sie im Beispiel [Überwachung, wenn keine Erweiterung vorhanden ist](../samples/audit-ext-not-exist.md).

Der Effekt **DeployIfNotExists** erfordert die **roleDefinitionId**-Eigenschaft im Bereich **details** der Richtlinienregel. Weitere Informationen finden Sie unter [Korrigieren nicht konformer Ressourcen](../how-to/remediate-resources.md#configure-policy-definition).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscription/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

Ausführliche Informationen zu den einzelnen Auswirkungen, der Reihenfolge der Auswertung, den Eigenschaften und Beispielen finden Sie unter [Grundlegendes zu Azure Policy-Auswirkungen](effects.md).

### <a name="policy-functions"></a>Richtlinienfunktionen

Alle [Resource Manager-Vorlagenfunktionen](../../../azure-resource-manager/resource-group-template-functions.md) stehen innerhalb einer Richtlinienregel zur Verfügung, mit Ausnahme der folgenden Funktionen und benutzerdefinierten Funktionen:

- copyIndex()
- deployment()
- list*
- newGuid()
- pickZones()
- providers()
- reference()
- resourceId()
- variables()

Die folgenden Funktionen stehen zur Verwendung in einer Richtlinienregel zur Verfügung, unterscheiden sich jedoch von der Verwendung in einer Azure Resource Manager-Vorlage:

- addDays(dateTime, numberOfDaysToAdd)
  - **dateTime**: [Erforderlich] string – Zeichenfolge im Universal ISO 8601 DateTime-Format 'jjjj-MM-ttTHH:mm:ss.fffffffZ'
  - **numberOfDaysToAdd**: [Erforderlich] integer – Anzahl der hinzuzufügenden Tage
- utcNow() – Im Gegensatz zu einer Resource Manager-Vorlage kann dies auch außerhalb von „defaultValue“ verwendet werden.
  - Gibt eine Zeichenfolge zurück, die auf das aktuelle Datum und die aktuelle Uhrzeit im Universal ISO 8601 DateTime-Format 'jjjj-MM-ttTHH:mm:ss.fffffffZ' festgelegt ist.

Darüber hinaus ist die `field` Funktion für Richtlinienregeln verfügbar. `field` ist in erster Linie für die Verwendung mit **AuditIfNotExists** und **DeployIfNotExists** zum Verweisen auf Felder in der Ressource bestimmt, die ausgewertet werden. Ein Beispiel hierfür finden Sie im [Beispiel für DeployIfNotExists](effects.md#deployifnotexists-example).

#### <a name="policy-function-example"></a>Beispiel für Richtlinienfunktion

Dieses Richtlinienregelbeispiel verwendet die Ressourcenfunktion `resourceGroup`, um die Eigenschaft **name** zu erhalten, kombiniert mit dem Array `concat` und der Objektfunktion, um eine `like`-Bedingung zu erstellen, die für den Ressourcennamen erzwingt, mit dem Ressourcengruppennamen zu beginnen.

```json
{
    "if": {
        "not": {
            "field": "name",
            "like": "[concat(resourceGroup().name,'*')]"
        }
    },
    "then": {
        "effect": "deny"
    }
}
```

## <a name="aliases"></a>Aliase

Eigenschaftenaliase dienen zum Zugreifen auf bestimmte Eigenschaften für einen Ressourcentyp. Mithilfe von Aliasen können Sie beschränken, welche Werte oder Bedingungen für eine Eigenschaft einer Ressourcen zulässig sind. Jeder Alias wird Pfaden in verschiedenen API-Versionen für einen bestimmten Ressourcentyp zugeordnet. Bei der Richtlinienauswertung ruft das Richtlinienmodul den Eigenschaftenpfad für diese API-Version ab.

Die Liste der Aliase wächst ständig. Um zu ermitteln, welche Aliase derzeit von Azure Policy unterstützt werden, verwenden Sie eine der folgenden Methoden:

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzAccount if not using Cloud Shell

  # Use Get-AzPolicyAlias to list available providers
  Get-AzPolicyAlias -ListAvailable

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Automation -- Microsoft.Automation)
  Get-AzPolicyAlias -NamespaceMatch 'automation'
  ```

- Azure-Befehlszeilenschnittstelle

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Automation -- Microsoft.Automation)
  az provider show --namespace Microsoft.Automation --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- REST-API/ARM-Client

  ```http
  GET https://management.azure.com/providers/?api-version=2017-08-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Grundlegendes zum [*]-Alias

Einige der verfügbaren Aliase weisen eine Version auf, die als „normaler“ Name angezeigt wird, an andere wird **[\*]** angefügt. Beispiel:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

Bei einem „normalen“ Alias wird das Feld als einzelner Wert dargestellt. Dieses Feld ist für genaue Vergleichs-/Übereinstimmungsszenarios bestimmt, wenn der gesamte Wertesatz exakt der Definition entsprechen muss (nicht mehr und nicht weniger).

Beim einem Alias mit Stern **[\*]** ist ein Vergleich mit dem Wert jedes einzelnen Elements im Array und mit bestimmten Eigenschaften der einzelnen Elemente möglich. Dieser Ansatz ermöglicht das Vergleichen von Elementeigenschaften bei Szenarios wie „if none of“, „if any of“ oder „if all of“. Mit **IpRules [\*]** würden Sie beispielsweise überprüfen, ob jede _action_ auf _Deny_ eingestellt ist, aber nicht darüber nachdenken, wie viele Regeln vorhanden sind oder welchen _Wert_ die IP-Adresse hat. Diese Beispielregel überprüft alle Übereinstimmungen von **IpRules[\*].Wert** mit **10.0.4.1** und wendet den **Effektttyp** nur dann an, wenn nicht mindestens eine Übereinstimmung gefunden wird:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value",
                "notEquals": "10.0.4.1"
            }
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

Weitere Informationen finden Sie unter [Auswerten eines Alias mit Stern [\*]](../how-to/author-policies-for-arrays.md#evaluating-the--alias).

## <a name="initiatives"></a>Initiativen

Mithilfe von Initiativen können Sie mehrere verwandte Richtliniendefinitionen gruppieren, um Zuweisungen und das Verwalten zu vereinfachen, indem Sie mit einer Gruppe als einzelnes Element arbeiten. Beispielsweise können Sie zusammengehörige Richtliniendefinitionen zum Markieren in einer einzelnen Initiative gruppieren. Anstatt jede Richtlinie einzeln zuzuweisen, wenden Sie die Initiative an.

Im folgenden Beispiel wird veranschaulicht, wie eine Initiative zur Behandlung der Tags `costCenter` und `productName` erstellt werden kann. Es werden zwei integrierte Richtlinien verwendet, um den Standardtagwert anzuwenden.

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                }
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                }
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    },
    "id": "/subscriptions/<subscription-id>/providers/Microsoft.Authorization/policySetDefinitions/billingTagsPolicy",
    "type": "Microsoft.Authorization/policySetDefinitions",
    "name": "billingTagsPolicy"
}
```

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die Beispiele unter [Azure Policy-Beispiele](../samples/index.md) an.
- Lesen Sie [Grundlegendes zu Richtlinienauswirkungen](effects.md).
- Informieren Sie sich über das [programmgesteuerte Erstellen von Richtlinien](../how-to/programmatically-create.md).
- Informieren Sie sich über das [Abrufen von Konformitätsdaten](../how-to/getting-compliance-data.md).
- Erfahren Sie, wie Sie [nicht konforme Ressourcen korrigieren](../how-to/remediate-resources.md) können.
- Weitere Informationen zu Verwaltungsgruppen finden Sie unter [Organisieren Ihrer Ressourcen mit Azure-Verwaltungsgruppen](../../management-groups/overview.md).