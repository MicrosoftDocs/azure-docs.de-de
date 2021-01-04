---
title: Details der Struktur von Richtliniendefinitionen
description: Beschreibt, wie Richtliniendefinitionen verwendet werden, um Konventionen für Azure-Ressourcen in Ihrer Organisation einzurichten.
ms.date: 10/22/2020
ms.topic: conceptual
ms.openlocfilehash: 5f9a110247d4ec93c8f3fb95fc9ed61eb6806787
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305157"
---
# <a name="azure-policy-definition-structure"></a>Struktur von Azure Policy-Definitionen

Azure Policy richtet Konventionen für Ressourcen ein. Richtliniendefinitionen beschreiben [Bedingungen](#conditions) für die Ressourcenkonformität und die Maßnahmen, die ergriffen werden, wenn eine Bedingung erfüllt ist. Eine Bedingung vergleicht ein [Feld](#fields) oder einen [Wert](#value) einer Ressourceneigenschaft mit einem erforderlichen Wert. Der Zugriff auf Ressourceneigenschaftsfelder erfolgt über [Aliase](#aliases). Wenn ein Ressourceneigenschaftsfeld ein Array ist, kann ein spezieller [Arrayalias](#understanding-the--alias) verwendet werden, um Werte aus allen Arraymembern auszuwählen und auf jedes eine Bedingung anzuwenden.
Erfahren Sie mehr über [Bedingungen](#conditions).

Durch Definieren von Konventionen können Sie Kosten beeinflussen und Ihre Ressourcen einfacher verwalten. Sie können beispielsweise angeben, dass nur bestimmte Typen virtueller Computer zulässig sind. Oder Sie können festlegen, dass Ressourcen ein bestimmtes Tag aufweisen. Richtlinienzuweisungen werden von untergeordneten Ressourcen geerbt. Wenn eine Richtlinienzuweisung auf eine Ressourcengruppe angewandt wird, gilt sie für alle Ressourcen in dieser Ressourcengruppe.

Das Richtliniendefinitionsschema _policyRule_ finden Sie hier: [https://schema.management.azure.com/schemas/2019-09-01/policyDefinition.json](https://schema.management.azure.com/schemas/2019-09-01/policyDefinition.json)

Eine Richtliniendefinition wird mithilfe von JSON erstellt. Die Richtliniendefinition enthält Elemente für Folgendes:

- Anzeigename
- description
- Modus
- metadata
- parameters
- Richtlinienregel
  - Logische Auswertung
  - Wirkung

Die folgende JSON-Datei zeigt beispielsweise eine Richtlinie, die einschränkt, wo Ressourcen bereitgestellt werden:

```json
{
    "properties": {
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "mode": "Indexed",
        "metadata": {
            "version": "1.0.0",
            "category": "Locations"
        },
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

Integrierte Richtlinien und Muster von Azure Policy finden Sie unter [Azure Policy-Beispiele](../samples/index.md).

## <a name="display-name-and-description"></a>Anzeigename und Beschreibung

Sie verwenden **displayName** und **description** , um die Richtliniendefinition zu bestimmen und den Kontext für ihre Verwendung anzugeben. **displayName** hat eine maximale Länge von _128_ Zeichen, und **description** hat eine maximale Länge von _512_ Zeichen.

> [!NOTE]
> Während der Erstellung oder Aktualisierung einer Richtliniendefinition werden **ID** , **Typ** und **Name** durch JSON-externe Eigenschaften definiert und sind in der JSON-Datei nicht erforderlich. Wenn Sie die Richtliniendefinition über das SDK abrufen, werden die Eigenschaften **ID** , **Typ** und **Name** als Teil der JSON-Datei zurückgegeben, sie sind jedoch schreibgeschützte Informationen, die nur die Richtliniendefinition betreffen.

## <a name="type"></a>type

Die **type-** Eigenschaft kann zwar nicht festgelegt werden, aber es gibt drei Werte, die vom SDK zurückgegeben und im Portal angezeigt werden:

- `Builtin`: Diese Richtliniendefinitionen werden von Microsoft bereitgestellt und verwaltet.
- `Custom`: Alle von Kunden erstellten Richtliniendefinitionen haben diesen Wert.
- `Static`: Gibt eine Richtliniendefinition zur [Einhaltung gesetzlicher Bestimmungen](./regulatory-compliance.md) mit Microsoft **Ownership** an. Die Complianceergebnisse für diese Richtliniendefinitionen sind die Ergebnisse von Drittanbieterüberwachungen in der Microsoft-Infrastruktur. Im Azure-Portal wird dieser Wert manchmal als **Von Microsoft verwaltet** angezeigt. Weitere Informationen dazu finden Sie unter [Gemeinsame Verantwortung in der Cloud](../../../security/fundamentals/shared-responsibility.md).

## <a name="mode"></a>Mode

Der Modus ( **mode** ) wird abhängig davon konfiguriert, ob die Richtlinie für eine Azure Resource Manager- oder eine Ressourcenanbieter-Eigenschaft gilt.

### <a name="resource-manager-modes"></a>Ressourcen-Manager-Modi

Der Modus ( **mode** ) bestimmt, welche Ressourcentypen für eine Richtliniendefinition ausgewertet werden. Unterstützte Modi:

- `all`: Ressourcengruppen, Abonnements und alle Ressourcentypen werden ausgewertet.
- `indexed`: Nur Ressourcentypen, die Tags und Speicherort unterstützen, werden ausgewertet.

Beispielsweise unterstützt die Ressource `Microsoft.Network/routeTables` Tags und Standort und wird in beiden Modi ausgewertet. Es ist jedoch nicht möglich, die Ressource `Microsoft.Network/routeTables/routes` mit einem Tag zu versehen, und sie wird nicht im Modus `Indexed` ausgewertet.

Es wird empfohlen, **mode** in den meisten Fällen auf `all` zu setzen. Alle über das Portal erstellten Richtliniendefinitionen verwenden für „mode“ die Option `all`. Wenn Sie PowerShell oder die Azure CLI verwenden, können Sie den **mode** -Parameter manuell angeben. Wenn die Richtliniendefinition keinen Wert für **mode** enthält, wird dieser in Azure PowerShell standardmäßig auf `all` und in der Azure CLI auf `null` festgelegt. Der Modus `null` entspricht dem Verwenden von `indexed`, um Abwärtskompatibilität zu unterstützen.

`indexed` sollte beim Erstellen von Richtlinien verwendet werden, die Tags oder Speicherorte erzwingen. Dies ist nicht erforderlich, verhindert aber, dass Ressourcen, die keine Tags und Speicherorte unterstützen, bei der Konformitätsprüfung als nicht konform angezeigt werden. Ausgenommen hiervon sind **Ressourcengruppen** und **Abonnements**. Richtliniendefinitionen zum Erzwingen von Speicherort oder Tags für eine Ressourcengruppe oder ein Abonnement sollten **mode** auf `all` festlegen und speziell auf den Typ `Microsoft.Resources/subscriptions/resourceGroups` oder `Microsoft.Resources/subscriptions` abzielen. Ein Beispiel finden Sie unter [Azure Policy-Muster: Tags – Beispiel 1](../samples/pattern-tags.md). Eine Liste der Ressourcen, die Tags unterstützen, finden Sie unter [Tagunterstützung für Azure-Ressourcen](../../../azure-resource-manager/management/tag-support.md).

### <a name="resource-provider-modes"></a>Ressourcenanbietermodi

Der folgende Ressourcenanbietermodus wird vollständig unterstützt:

- `Microsoft.Kubernetes.Data` zum Verwalten Ihrer Kubernetes-Cluster in oder außerhalb von Azure. Definitionen, die diesen Ressourcenanbietermodus nutzen, verwenden die Auswirkungen _audit_ , _deny_ und _disabled_. Die Verwendung der Auswirkung [EnforceOPAConstraint](./effects.md#enforceopaconstraint) ist _veraltet_.

Die folgenden Ressourcenanbietermodi werden derzeit als **Vorschau** unterstützt:

- `Microsoft.ContainerService.Data` zur Verwaltung der Zugangscontrollerregeln für [Azure Kubernetes Service](../../../aks/intro-kubernetes.md). Definitionen, die diesen Ressourcenanbietermodus verwenden, **müssen** die Auswirkung [EnforceRegoPolicy](./effects.md#enforceregopolicy) verwenden. Dieser Modus ist _veraltet_.
- `Microsoft.KeyVault.Data` zur Verwaltung von Tresoren und Zertifikaten in [Azure Key Vault](../../../key-vault/general/overview.md). Weitere Informationen zu diesen Richtliniendefinitionen finden Sie unter [Integrieren von Azure Key Vault in Azure Policy](../../../key-vault/general/azure-policy.md).

> [!NOTE]
> Ressourcenanbietermodi unterstützen nur integrierte Richtliniendefinitionen und keine [Ausnahmen](./exemption-structure.md).

## <a name="metadata"></a>Metadaten

In der optionalen `metadata`-Eigenschaft werden Informationen zur Richtliniendefinition gespeichert. Kunden können alle für ihre Organisation nützlichen Eigenschaften und Werte in `metadata` definieren. Es gibt jedoch einige _allgemeine_ Eigenschaften, die von Azure Policy und integrierten Richtlinien verwendet werden.

### <a name="common-metadata-properties"></a>Allgemeine Metadateneigenschaften

- `version` (Zeichenfolge): Verfolgt Details zur Version des Inhalts einer Richtliniendefinition nach.
- `category` (Zeichenfolge): Legt fest, unter welcher Kategorie im Azure-Portal die Richtliniendefinition angezeigt wird.
- `preview` (boolescher Wert): Flag mit einem der Werte TRUE oder FALSE für den Fall, dass sich die Richtliniendefinition in der _Vorschauphase_ befindet.
- `deprecated` (Boolescher Wert): Flag mit einem der Werte TRUE oder FALSE für den Fall, dass die Richtliniendefinition als _veraltet_ markiert wurde.

> [!NOTE]
> Der Azure Policy-Dienst verwendet die Eigenschaften `version`, `preview` und `deprecated`, um den Grad der Änderung einer integrierten Richtliniendefinition oder Initiative und einen Status zu übermitteln. Das Format von `version` ist `{Major}.{Minor}.{Patch}`. Bestimmte Zustände, z. B. _veraltet_ oder _Vorschau_ , werden der `version`-Eigenschaft angehängt oder in eine andere Eigenschaft als **boolescher** Wert eingefügt. Weitere Informationen zu der Methodik, mit der Azure Policy die Versionen integrierter Richtlinien verwaltet, finden Sie unter [Built-in versioning](https://github.com/Azure/azure-policy/blob/master/built-in-policies/README.md) (Versionsverwaltung für integrierte Richtlinien).

## <a name="parameters"></a>Parameter

Parameter vereinfachen Ihre Richtlinienverwaltung, indem sie die Anzahl von Richtliniendefinitionen reduzieren. Es handelt sich dabei z.B. um Parameter wie die folgenden Felder auf einem Formular: `name`, `address`, `city`, `state`. Diese Parameter bleiben immer gleich, allerdings ändern sich ihre Werte auf Grundlage der Einträge des Einzelnen.
Parameter funktionieren beim Erstellen von Richtlinien genauso. Sie können die Richtlinie für verschiedene Szenarios wiederverwenden, indem Sie Parameter in eine Richtliniendefinition einbeziehen und verschiedene Werte verwenden.

> [!NOTE]
> Parameter können einer vorhandenen und zugewiesenen Definition hinzugefügt werden. Der neue Parameter muss die **defaultValue** -Eigenschaft enthalten. Dadurch wird verhindert, dass vorhandene Zuweisungen der Richtlinie oder Initiative indirekt als ungültig erklärt werden.

### <a name="parameter-properties"></a>Parametereigenschaften

Ein Parameter hat die folgenden Eigenschaften, die in der Richtliniendefinition verwendet werden:

- `name`: Der Name des Parameters. Wird in der Richtlinienregel von der Bereitstellungsfunktion `parameters` verwendet. Weitere Informationen finden Sie unter [Verwenden eines Parameterwerts](#using-a-parameter-value).
- `type`: Bestimmt, ob der Parameter eine **Zeichenfolge** , ein **Array** , ein **Objekt** , ein **boolescher Wert** , eine **ganze Zahl** oder vom Typ **float** oder **datetime** ist.
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

In der Richtlinienregel wird die folgende Syntax der Funktion `parameters` verwendet, um auf Parameter zu verweisen:

```json
{
    "field": "location",
    "in": "[parameters('allowedLocations')]"
}
```

In diesem Beispiel wird auf den Parameter **allowedLocations** verwiesen, der unter [Parametereigenschaften](#parameter-properties) vorgestellt wurde.

### <a name="strongtype"></a>strongType

Innerhalb der `metadata`-Eigenschaft können Sie mit **strongType** im Azure-Portal eine Liste der Optionen mit Mehrfachauswahl angeben. **strongType** kann ein unterstützter _Ressourcentyp_ oder ein zulässiger Wert sein. Verwenden Sie [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider), um festzustellen, ob ein _Ressourcentyp_ für **strongType** zulässig ist. Das Format für den **strongType** eines _Ressourcentyps_ lautet `<Resource Provider>/<Resource Type>`. Beispiel: `Microsoft.Network/virtualNetworks/subnets`.

Einige _Ressourcentypen_ , die von **Get-AzResourceProvider** nicht zurückgegeben werden, werden unterstützt. Diese Typen lauten wie folgt:

- `Microsoft.RecoveryServices/vaults/backupPolicies`

Die nicht für den _Ressourcentyp_ zulässigen Werte für **strongType** sind:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="definition-location"></a>Definitionsspeicherort

Beim Erstellen einer Initiative oder Richtlinie muss der Speicherort der Definition angegeben werden. Dieser Speicherort muss eine Verwaltungsgruppe oder ein Abonnement sein. Er bestimmt den Bereich, dem die Initiative oder Richtlinie zugewiesen werden kann. Ressourcen müssen direkte Mitglieder oder untergeordnete Elemente innerhalb der Hierarchie des Definitionsspeicherorts für die Zuweisung sein.

Für den Definitionsspeicherort gilt Folgendes:

- **Abonnement** : Der Richtliniendefinition können nur Ressourcen innerhalb dieses Abonnements zugewiesen werden.
- **Verwaltungsgruppe** : Der Richtliniendefinition können nur Ressourcen in untergeordneten Verwaltungsgruppen und untergeordneten Abonnements zugewiesen werden. Wenn Sie die Richtliniendefinition auf mehrere Abonnements anwenden möchten, muss der Speicherort eine Verwaltungsgruppe sein, die das jeweilige Abonnement enthält.

Weitere Informationen finden Sie in der [Übersicht zu Bereichen in Azure Policy](./scope.md#definition-location).

## <a name="policy-rule"></a>Richtlinienregel

Die Richtlinienregel besteht aus **If** - und **Then** -Blöcken. Im **If** -Block definieren Sie mindestens eine Bedingung, die angibt, wann die Richtlinie erzwungen wird. Auf diese Bedingungen können logische Operatoren angewendet werden, um das Szenario für eine Richtlinie präzise zu definieren.

Im **Then** -Block definieren Sie die Wirkung, die eintritt, wenn die **If** -Bedingungen erfüllt sind.

```json
{
    "if": {
        <condition> | <logical operator>
    },
    "then": {
        "effect": "deny | audit | modify | append | auditIfNotExists | deployIfNotExists | disabled"
    }
}
```

### <a name="logical-operators"></a>Logische Operatoren

Folgende logische Operatoren werden unterstützt:

- `"not": {condition  or operator}`
- `"allOf": [{condition or operator},{condition or operator}]`
- `"anyOf": [{condition or operator},{condition or operator}]`

Die **not** -Syntax kehrt das Ergebnis der Bedingung um. Die **allOf** -Syntax gleicht der logischen **And** -Operation und erfordert, dass alle Bedingungen erfüllt sind. Die **anyOf** -Syntax gleicht der logischen **Or** -Operation und erfordert, dass mindestens eine Bedingung erfüllt ist.

Logische Operatoren können geschachtelt werden. Das folgende Beispiel zeigt eine **not** -Operation, die in einer **allOf** -Operation geschachtelt ist.

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

- `"equals": "stringValue"`
- `"notEquals": "stringValue"`
- `"like": "stringValue"`
- `"notLike": "stringValue"`
- `"match": "stringValue"`
- `"matchInsensitively": "stringValue"`
- `"notMatch": "stringValue"`
- `"notMatchInsensitively": "stringValue"`
- `"contains": "stringValue"`
- `"notContains": "stringValue"`
- `"in": ["stringValue1","stringValue2"]`
- `"notIn": ["stringValue1","stringValue2"]`
- `"containsKey": "keyName"`
- `"notContainsKey": "keyName"`
- `"less": "dateValue"` | `"less": "stringValue"` | `"less": intValue`
- `"lessOrEquals": "dateValue"` | `"lessOrEquals": "stringValue"` | `"lessOrEquals": intValue`
- `"greater": "dateValue"` | `"greater": "stringValue"` | `"greater": intValue`
- `"greaterOrEquals": "dateValue"` | `"greaterOrEquals": "stringValue"` |
  `"greaterOrEquals": intValue`
- `"exists": "bool"`

Bei **less** , **lessOrEquals** , **greater** und **greaterOrEquals** wird, wenn der Eigenschaftentyp nicht mit dem Bedingungstyp übereinstimmt, ein Fehler ausgelöst. Zeichenfolgenvergleiche werden mit `InvariantCultureIgnoreCase` durchgeführt.

Bei Verwendung der Bedingungen **like** und **notLike** können Sie im Wert den Platzhalter `*` angeben.
Der Wert darf maximal einen Platzhalter des Typs `*` enthalten.

Geben Sie bei Verwendung der Bedingungen **match** und **notMatch** für eine Ziffer `#`, für einen Buchstaben `?`, für irgendein Zeichen `.` und für ein Zeichen das gewünschte Zeichen ein. Während bei **match** und **notMatch** die Groß-/Kleinschreibung beachtet wird, erfolgt bei allen anderen Bedingungen, die einen _stringValue_ auswerten, keine Berücksichtigung der Groß-/Kleinschreibung. Als Alternativen, bei denen die Groß-/Kleinschreibung nicht beachtet werden muss, stehen **matchInsensitively** und **notMatchInsensitively** zur Verfügung.

Bei den Feldwerten eines Arrays mit einem **\[\*\]-Alias** wird jedes Element im Array einzeln ausgewertet, wobei die Elemente durch ein logisches **UND** verknüpft werden. Weitere Informationen finden Sie unter [Verweisen auf Arrayeigenschaften in Ressourcen](../how-to/author-policies-for-arrays.md#referencing-array-resource-properties).

### <a name="fields"></a>Felder

Bedingungen werden mithilfe von Feldern gebildet. Ein Feld stimmt mit Eigenschaften in der Anforderungsnutzlast einer Ressource überein und beschreibt den Zustand der Ressource.

Folgende Felder werden unterstützt:

- `name`
- `fullName`
  - Gibt den vollständigen Namen der Ressource zurück. Der vollständige Name einer Ressource setzt sich zusammen aus dem Namen der Ressource und den vorangestellten Namen übergeordneter Ressourcen, sofern vorhanden (Beispiel: meinServer/meineDatenbank).
- `kind`
- `type`
- `location`
  - Verwenden Sie **global** für Ressourcen, die speicherortagnostisch sind.
- `id`
  - Gibt die Ressourcen-ID für die auszuwertende Ressource zurück.
  - Beispiel: `/subscriptions/06be863d-0996-4d56-be22-384767287aa2/resourceGroups/myRG/providers/Microsoft.KeyVault/vaults/myVault`
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
  - Beispiel: `tags['''My.Apostrophe.Tag''']`, wobei **'My.Apostrophe.Tag'** der Name des Tags ist.
- Eigenschaftenaliase – Eine Liste finden Sie unter [Aliase](#aliases).

> [!NOTE]
> `tags.<tagName>`, `tags[tagName]` und `tags[tag.with.dots]` werden weiterhin als Möglichkeiten zum Deklarieren eines Felds für Tags akzeptiert. Die oben aufgeführten Ausdrücke werden jedoch bevorzugt.

#### <a name="use-tags-with-parameters"></a>Verwenden von Tags mit Parametern

Ein Parameterwert kann an ein Tagfeld übergeben werden. Das Übergeben eines Parameters an ein Tagfeld erhöht die Flexibilität der Richtliniendefinition während der Richtlinienzuweisung.

Im folgenden Beispiel wird mit `concat` eine Tagfeldsuche nach dem Tag erstellt, das als Namen den Wert des **TagName** -Parameters aufweist. Wenn dieses Tag nicht vorhanden ist, wird die Auswirkung **modify** verwendet, um mithilfe der Nachschlagefunktion `resourcegroup()` das Tag mit dem Wert dieses benannten Tags hinzuzufügen, das für die übergeordnete Ressourcengruppe der überwachten Ressourcen festgelegt ist.

```json
{
    "if": {
        "field": "[concat('tags[', parameters('tagName'), ']')]",
        "exists": "false"
    },
    "then": {
        "effect": "modify",
        "details": {
            "operations": [{
                "operation": "add",
                "field": "[concat('tags[', parameters('tagName'), ']')]",
                "value": "[resourcegroup().tags[parameters('tagName')]]"
            }],
            "roleDefinitionIds": [
                "/providers/microsoft.authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c"
            ]
        }
    }
}
```

### <a name="value"></a>Wert

Bedingungen können auch mithilfe von **Wert** gebildet werden. **Wert** gleicht die Bedingungen mit [Parametern](#parameters), [unterstützten Vorlagenfunktionen](#policy-functions) oder Literalen ab. **Wert** wird mit beliebigen unterstützten [Bedingungen](#conditions) verknüpft.

> [!WARNING]
> Wenn eine _Vorlagenfunktion_ einen Fehler ergibt, schlägt die Richtlinienauswertung fehl. Eine fehlerhafte Auswertung ist ein implizites **Deny** (Verweigern). Weitere Informationen finden Sie unter [Vermeiden von Vorlagenfehlern](#avoiding-template-failures). Verwenden Sie als [enforcementMode](./assignment-structure.md#enforcement-mode) den Modus **DoNotEnforce** , um Auswirkungen einer fehlerhaften Auswertung auf neue oder aktualisierte Ressourcen beim Testen und Überprüfen einer neuen Richtliniendefinition zu verhindern.

#### <a name="value-examples"></a>Beispiele von Werten

Dieses Beispiel einer Richtlinienregel verwendet **Wert** , um das Ergebnis der `resourceGroup()`-Funktion und der zurückgegebenen **name** -Eigenschaft mit der **like** -Bedingung `*netrg` zu vergleichen. Die Regel verweigert Ressourcen, die nicht den **type** `Microsoft.Network/*` haben, in Ressourcengruppen, deren Name mit `*netrg` endet.

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

Diese Beispiel einer Regelrichtlinie verwendet **value** , um zu überprüfen, ob das Ergebnis mehrerer geschachtelter Funktionen **gleich** `true` ist. Die Regel verweigert alle Ressourcen, die nicht mindestens drei Tags haben.

```json
{
    "mode": "indexed",
    "policyRule": {
        "if": {
            "value": "[less(length(field('tags')), 3)]",
            "equals": "true"
        },
        "then": {
            "effect": "deny"
        }
    }
}
```

#### <a name="avoiding-template-failures"></a>Vermeiden von Vorlagenfehlern

Wenn Sie _Vorlagenfunktionen_ als **Wert** verwenden, sind viele komplexe und verschachtelte Funktionen möglich. Wenn eine _Vorlagenfunktion_ einen Fehler ergibt, schlägt die Richtlinienauswertung fehl. Eine fehlerhafte Auswertung ist ein implizites **Deny** (Verweigern). Beispiel für einen **Wert** , bei dem in bestimmten Szenarios ein Fehler auftritt:

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

Bei der oben als Beispiel verwendeten Richtlinienregel wird [substring()](../../../azure-resource-manager/templates/template-functions-string.md#substring) zum Vergleichen der ersten drei Zeichen des **Namens** mit **abc** verwendet. Wenn der **Name** kürzer als drei Zeichen ist, ergibt die `substring()`-Funktion einen Fehler. Dieser Fehler löst bei der Richtlinie den **Deny** -Effekt aus.

Verwenden Sie stattdessen die [if()](../../../azure-resource-manager/templates/template-functions-logical.md#if)-Funktion, um zu überprüfen, ob die ersten drei Zeichen des **Namens** gleich **abc** sind, ohne dass ein **Name** , der kürzer als drei Zeichen ist, einen Fehler verursachen kann:

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

Mit der überarbeiteten Richtlinienregel überprüft `if()` die Länge des **Namens** , bevor es versucht, einen `substring()` für einen Wert mit weniger als drei Zeichen abzurufen. Wenn der **Name** zu kurz ist, wird der Wert, der „nicht mit abc beginnt“, zurückgegeben und mit **abc** verglichen. Eine Ressource mit einem Kurznamen, der nicht mit **abc** beginnt, erzeugt zwar immer noch einen Fehler bei der Richtlinienregel, verursacht aber bei der Auswertung keinen Fehler mehr.

### <a name="count"></a>Anzahl

Mithilfe eines **count** -Ausdrucks können Bedingungen erstellt werden, mit denen gezählt wird, wie viele Member eines Arrays in der Ressourcennutzlast einem Bedingungsausdruck entsprechen. Häufige Szenarien sind die Überprüfung, ob mindestens einer (at least one of), genau einer (exactly one of), alle (all of) oder keiner (none of) der Arraymember die Bedingung erfüllt. **count** wertet jeden [\[\*\]-Alias](#understanding-the--alias)-Arraymember für einen Bedingungsausdruck aus und fasst die Ergebnisse mit dem Wert _true_ zusammen, die dann mit dem Ausdrucksoperator verglichen werden. **Count** -Ausdrücke können einer einzelnen **policyRule-** -Definition bis zu drei Mal hinzugefügt werden.

Die Struktur des **count** -Ausdrucks sieht wie folgt aus:

```json
{
    "count": {
        "field": "<[*] alias>",
        "where": {
            /* condition expression */
        }
    },
    "<condition>": "<compare the count of true condition expression array members to this value>"
}
```

Die folgenden Eigenschaften werden bei **count** verwendet:

- **count.field** (erforderlich): Enthält den Pfad zum Array und muss ein Arrayalias sein. Wenn das Array fehlt, wird der Ausdruck ohne Berücksichtigung des Bedingungsausdrucks als _false_ ausgewertet.
- **count.where** (optional): Der Bedingungsausdruck zum individuellen Auswerten der einzelnen Arraymember des Typs [\[\*\]-Alias](#understanding-the--alias) von **count.field**. Wenn diese Eigenschaft nicht angegeben wird, werden alle Arraymember mit dem Pfad „field“ als _true_ ausgewertet. Innerhalb dieser Eigenschaft kann eine beliebige [Bedingung](../concepts/definition-structure.md#conditions) verwendet werden.
  Es können [logische Operatoren](#logical-operators) innerhalb dieser Eigenschaft verwendet werden, um komplexe Auswertungsanforderungen zu erstellen.
- **\<condition\>** (erforderlich): Der Wert wird mit der Anzahl der Elemente verglichen, die dem **count.where** -Bedingungsausdruck entsprachen. Es sollte eine numerische [Bedingung](../concepts/definition-structure.md#conditions) verwendet werden.

Weitere Informationen zur Arbeit mit Arrayeigenschaften in Azure Policy, einschließlich einer detaillierten Erklärung, wie der Count-Ausdruck ausgewertet wird, finden Sie unter [Verweisen auf Arrayeigenschaften in Ressourcen](../how-to/author-policies-for-arrays.md#referencing-array-resource-properties).

#### <a name="count-examples"></a>Beispiele für „count“

Beispiel 1: Überprüfen, ob ein Array leer ist

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]"
    },
    "equals": 0
}
```

Beispiel 2: Überprüfen, ob nur ein Arraymember dem Bedingungsausdruck entspricht

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My unique description"
        }
    },
    "equals": 1
}
```

Beispiel 3: Überprüfen, ob mindestens ein Arraymember dem Bedingungsausdruck entspricht

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "My common description"
        }
    },
    "greaterOrEquals": 1
}
```

Beispiel 4: Überprüfen, ob alle Objektarraymember dem Bedingungsausdruck entsprechen

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].description",
            "equals": "description"
        }
    },
    "equals": "[length(field('Microsoft.Network/networkSecurityGroups/securityRules[*]'))]"
}
```

Beispiel 5: Überprüfen, ob mindestens ein Arraymember mehreren Eigenschaften im Bedingungsausdruck entspricht

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
            "allOf": [
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].direction",
                    "equals": "Inbound"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].access",
                    "equals": "Allow"
                },
                {
                    "field": "Microsoft.Network/networkSecurityGroups/securityRules[*].destinationPortRange",
                    "equals": "3389"
                }
            ]
        }
    },
    "greater": 0
}
```

Beispiel 6: Verwenden Sie die `field()`-Funktion innerhalb der `where`-Bedingungen, um auf den Literalwert des aktuell ausgewerteten Arraymembers zuzugreifen. Diese Bedingung prüft, dass es keine Sicherheitsregeln mit einem geradzahligen _Prioritätswert_ gibt.

```json
{
    "count": {
        "field": "Microsoft.Network/networkSecurityGroups/securityRules[*]",
        "where": {
          "value": "[mod(first(field('Microsoft.Network/networkSecurityGroups/securityRules[*].priority')), 2)]",
          "equals": 0
        }
    },
    "greater": 0
}
```

### <a name="effect"></a>Wirkung

Azure Policy unterstützt die folgenden Auswirkungstypen:

- **Append** fügt der Anforderung verschiedene definierte Felder hinzu.
- **Audit** generiert eine Warnung im Überwachungsprotokoll, führt jedoch nicht zu einem Fehler bei der Anforderung.
- **AuditIfNotExists** generiert eine Warnung im Aktivitätsprotokoll, wenn keine verwandte Ressource vorhanden ist.
- **Deny** generiert ein Ereignis im Aktivitätsprotokoll und führt zu einem Fehler bei der Anforderung.
- **DeployIfNotExists** stellt eine verwandte Ressource bereit, falls noch keine vorhanden ist.
- **Deaktiviert** wertet Ressourcen nicht auf Konformität mit der Richtlinienregel aus.
- **Modify** fügt die definierten Tags zu einer Ressource hinzu, aktualisiert sie oder entfernt sie aus einer Ressource.
- **EnforceOPAConstraint** (veraltet) konfiguriert den Open Policy Agent-Zugangscontroller mit Gatekeeper v3 für selbstverwaltete Kubernetes-Cluster in Azure.
- **EnforceRegoPolicy** (veraltet) konfiguriert den Open Policy Agent-Zugangscontroller mit Gatekeeper v2 in Azure Kubernetes Service.

Ausführliche Informationen zu den einzelnen Auswirkungen, der Reihenfolge der Auswertung, den Eigenschaften und Beispielen finden Sie unter [Grundlegendes zu Azure Policy-Auswirkungen](effects.md).

### <a name="policy-functions"></a>Richtlinienfunktionen

Alle [Resource Manager-Vorlagenfunktionen](../../../azure-resource-manager/templates/template-functions.md) stehen innerhalb einer Richtlinienregel zur Verfügung, mit Ausnahme der folgenden Funktionen und benutzerdefinierten Funktionen:

- copyIndex()
- deployment()
- list*
- newGuid()
- pickZones()
- providers()
- reference()
- resourceId()
- variables()

> [!NOTE]
> Diese Funktionen sind weiterhin innerhalb des Teils `details.deployment.properties.template` der Vorlagenbereitstellung in der Definition der Richtlinie **deployIfNotExists** verfügbar.

Die folgende Funktion steht zur Verwendung in einer Richtlinienregel zur Verfügung, unterscheidet sich jedoch von der Verwendung in einer Azure Resource Manager-Vorlage (ARM-Vorlage):

- `utcNow()`: Im Gegensatz zu einer ARM-Vorlage kann diese Eigenschaft auch außerhalb von _defaultValue_ verwendet werden.
  - Gibt eine Zeichenfolge zurück, die auf das aktuelle Datum und die aktuelle Uhrzeit im Universal ISO 8601-DateTime-Format (`yyyy-MM-ddTHH:mm:ss.fffffffZ`) festgelegt ist.

Die folgenden Funktionen sind nur in Richtlinienregeln verfügbar:

- `addDays(dateTime, numberOfDaysToAdd)`
  - **dateTime** : [Erforderlich] string – Zeichenfolge im Universal ISO 8601 DateTime-Format 'yyyy-MM-ddTHH:mm:ss.FFFFFFFZ'
  - **numberOfDaysToAdd** : [Erforderlich] integer – Anzahl der hinzuzufügenden Tage
- `field(fieldName)`
  - **fieldName** [erforderlich]: Zeichenfolge – Name des abzurufenden [Felds](#fields)
  - Gibt den Wert dieses Felds aus der Ressource zurück, die von der If-Bedingung ausgewertet wird
  - `field` ist in erster Linie für die Verwendung mit **AuditIfNotExists** und **DeployIfNotExists** zum Verweisen auf Felder in der Ressource bestimmt, die ausgewertet werden. Ein Beispiel hierfür finden Sie im [Beispiel für DeployIfNotExists](effects.md#deployifnotexists-example).
- `requestContext().apiVersion`
  - Gibt die API-Version der Anforderung zurück, die die Richtlinienauswertung ausgelöst hat (z. B. `2019-09-01`).
    Dieser Wert ist die API-Version, die in der PUT/PATCH-Anforderung für Auswertungen bei der Erstellung/Aktualisierung von Ressourcen verwendet wurde. Bei der Kompatibilitätsauswertung vorhandener Ressourcen wird immer die neueste API-Version verwendet.
- `policy()`
  - Gibt die folgenden Informationen zur derzeit ausgewerteten Richtlinie zurück. Auf Eigenschaften kann über das zurückgegebene Objekt zugegriffen werden (Beispiel: `[policy().assignmentId]`).
  
  ```json
  {
    "assignmentId": "/subscriptions/ad404ddd-36a5-4ea8-b3e3-681e77487a63/providers/Microsoft.Authorization/policyAssignments/myAssignment",
    "definitionId": "/providers/Microsoft.Authorization/policyDefinitions/34c877ad-507e-4c82-993e-3452a6e0ad3c",
    "setDefinitionId": "/providers/Microsoft.Authorization/policySetDefinitions/42a694ed-f65e-42b2-aa9e-8052e9740a92",
    "definitionReferenceId": "StorageAccountNetworkACLs"
  }
  ```


- `ipRangeContains(range, targetRange)`
    - **range** : [Erforderlich] Zeichenfolge: Zeichenfolge, die einen Bereich von IP-Adressen angibt
    - **targetRange** : [Erforderlich] Zeichenfolge: Zeichenfolge, die einen Bereich von IP-Adressen angibt

    Gibt zurück, ob der angegebene IP-Adressbereich den Ziel-IP-Adressbereich enthält. Leere Bereiche oder Kombinationen verschiedener IP-Familien sind nicht zulässig und führen zu einem Bewertungsfehler.

    Unterstützte Formate:
    - Einzelne IP-Adresse (Beispiele: `10.0.0.0`, `2001:0DB8::3:FFFE`)
    - CIDR-Bereich (Beispiele: `10.0.0.0/24`, `2001:0DB8::/110`)
    - Durch Start- und End-IP-Adressen definierter Bereich (Beispiele: `192.168.0.1-192.168.0.9`, `2001:0DB8::-2001:0DB8::3:FFFF`)


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

- Azure Policy-Erweiterung für Visual Studio Code (empfohlen)

  Verwenden Sie die [Azure Policy-Erweiterung für Visual Studio Code](../how-to/extension-for-vscode.md) zum Anzeigen und Ermitteln von Aliasen für Ressourceneigenschaften.

  :::image type="content" source="../media/extension-for-vscode/extension-hover-shows-property-alias.png" alt-text="Screenshot der Azure Policy-Erweiterung für Visual Studio Code mit dem Mauszeiger über einer Eigenschaft, um die Aliasnamen anzuzeigen" border="false":::

- Azure Resource Graph

  Verwenden Sie den Operator `project`, um den **Alias** einer Ressource anzuzeigen.

  ```kusto
  Resources
  | where type=~'microsoft.storage/storageaccounts'
  | limit 1
  | project aliases
  ```
  
  ```azurecli-interactive
  az graph query -q "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
  ```
  
  ```azurepowershell-interactive
  Search-AzGraph -Query "Resources | where type=~'microsoft.storage/storageaccounts' | limit 1 | project aliases"
  ```

- Azure PowerShell

  ```azurepowershell-interactive
  # Login first with Connect-AzAccount if not using Cloud Shell

  # Use Get-AzPolicyAlias to list available providers
  Get-AzPolicyAlias -ListAvailable

  # Use Get-AzPolicyAlias to list aliases for a Namespace (such as Azure Compute -- Microsoft.Compute)
  (Get-AzPolicyAlias -NamespaceMatch 'compute').Aliases
  ```

  > [!NOTE]
  > Zum Suchen von Aliasen, die mit dem [Modify](./effects.md#modify)-Effekt verwendet werden können, verwenden Sie in Azure PowerShell  **4.6.0** oder höher den folgenden Befehl:
  >
  > ```azurepowershell-interactive
  > Get-AzPolicyAlias | Select-Object -ExpandProperty 'Aliases' | Where-Object { $_.DefaultMetadata.Attributes -eq 'Modifiable' }
  > ```

- Azure CLI

  ```azurecli-interactive
  # Login first with az login if not using Cloud Shell

  # List namespaces
  az provider list --query [*].namespace

  # Get Azure Policy aliases for a specific Namespace (such as Azure Compute -- Microsoft.Compute)
  az provider show --namespace Microsoft.Compute --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
  ```

- REST-API/ARM-Client

  ```http
  GET https://management.azure.com/providers/?api-version=2019-10-01&$expand=resourceTypes/aliases
  ```

### <a name="understanding-the--alias"></a>Grundlegendes zum [*]-Alias

Einige der verfügbaren Aliase weisen eine Version auf, die als „normaler“ Name angezeigt wird, an andere wird **\[\*\]** angefügt. Beispiel:

- `Microsoft.Storage/storageAccounts/networkAcls.ipRules`
- `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]`

Bei einem „normalen“ Alias wird das Feld als einzelner Wert dargestellt. Dieses Feld ist für genaue Vergleichs-/Übereinstimmungsszenarios bestimmt, wenn der gesamte Wertesatz exakt der Definition entsprechen muss (nicht mehr und nicht weniger).

Der Alias **\[\*\]** repräsentiert eine Sammlung von Werten, die aus den Elementen einer Arrayressourceneigenschaft ausgewählt wurden. Beispiel:

| Alias | Ausgewählte Werte |
|:---|:---|
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*]` | Die Elemente des `ipRules`-Arrays. |
| `Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].action` | Die Werte der `action`-Eigenschaft der einzelnen Elemente des `ipRules`-Arrays. |

Bei Verwendung in einer [Feld](#fields)-Bedingung ermöglichen die Arrayaliase den Vergleich der einzelnen Arrayelemente mit einem Zielwert. Bei Verwendung mit dem [count](#count)-Ausdruck ist Folgendes möglich:

- Überprüfen der Größe eines Arrays
- Überprüfen, ob alle\einige\keine Arrayelemente eine komplexe Bedingung erfüllen
- Überprüfen, ob genau ***n*** Arrayelemente eine komplexe Bedingung erfüllen

Weitere Informationen und Beispiele finden Sie unter [Verweisen auf Arrayeigenschaften in Ressourcen](../how-to/author-policies-for-arrays.md#referencing-array-resource-properties).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie unter [Struktur der Initiativendefinition](./initiative-definition-structure.md).
- Sehen Sie sich die Beispiele unter [Azure Policy-Beispiele](../samples/index.md) an.
- Lesen Sie [Grundlegendes zu Richtlinienauswirkungen](effects.md).
- Informieren Sie sich über das [programmgesteuerte Erstellen von Richtlinien](../how-to/programmatically-create.md).
- Informieren Sie sich über das [Abrufen von Konformitätsdaten](../how-to/get-compliance-data.md).
- Erfahren Sie, wie Sie [nicht konforme Ressourcen korrigieren](../how-to/remediate-resources.md) können.
- Weitere Informationen zu Verwaltungsgruppen finden Sie unter [Organisieren Ihrer Ressourcen mit Azure-Verwaltungsgruppen](../../management-groups/overview.md).
