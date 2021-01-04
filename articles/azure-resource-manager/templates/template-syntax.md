---
title: Vorlagenstruktur und -syntax
description: Beschreibt die Struktur und die Eigenschaften der Azure Resource Manager-Vorlagen (ARM-Vorlagen) mithilfe deklarativer JSON-Syntax.
ms.topic: conceptual
ms.date: 12/01/2020
ms.openlocfilehash: ce36d725b3844fcd4c8d43a9f044423611d44fbd
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96497876"
---
# <a name="understand-the-structure-and-syntax-of-arm-templates"></a>Verstehen der Struktur und Syntax von ARM-Vorlagen

In diesem Artikel wird die Struktur einer Azure Resource Manager-Vorlage (ARM-Vorlage) beschrieben. Er zeigt die verschiedenen Abschnitte einer Vorlage und die Eigenschaften, die in diesen Abschnitten verfügbar sind.

Dieser Artikel richtet sich an Benutzer, die bereits Vorkenntnisse zu ARM-Vorlagen haben. Er bietet detaillierte Informationen zur Struktur der Vorlage. Ein Schritt-für-Schritt-Tutorial mit Anleitungen zum Erstellen einer Vorlage finden Sie unter [Tutorial: Erstellen und Bereitstellen Ihrer ersten ARM-Vorlage](template-tutorial-create-first-template.md).

## <a name="template-format"></a>Vorlagenformat

In der einfachsten Struktur weist eine Vorlage die folgenden Elemente auf:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "",
  "apiProfile": "",
  "parameters": {  },
  "variables": {  },
  "functions": [  ],
  "resources": [  ],
  "outputs": {  }
}
```

| Elementname | Erforderlich | BESCHREIBUNG |
|:--- |:--- |:--- |
| $schema |Ja |Speicherort der JSON-Schemadatei, die die Version der Vorlagensprache beschreibt. Die von Ihnen verwendete Versionsnummer hängt vom Umfang der Bereitstellung und vom JSON-Editor ab.<br><br>Wenn Sie [VS Code mit der Erweiterung für Azure Resource Manager-Tools](quickstart-create-templates-use-visual-studio-code.md) nutzen, verwenden Sie die aktuelle Version für Bereitstellungen von Ressourcengruppen:<br>`https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`<br><br>Andere Editoren (einschließlich Visual Studio) können dieses Schema unter Umständen nicht verarbeiten. Verwenden Sie für diese Editoren Folgendes:<br>`https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`<br><br>Verwenden Sie Folgendes für Bereitstellungen von Abonnements:<br>`https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`<br><br>Verwenden Sie für Bereitstellungen von Verwaltungsgruppen Folgendes:<br>`https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#`<br><br>Verwenden Sie für Bereitstellungen von Mandanten Folgendes:<br>`https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#` |
| contentVersion |Ja |Version der Vorlage (z. B. 1.0.0.0). Sie können einen beliebigen Wert für dieses Element resources. Mit diesem Wert können Sie wichtige Änderungen in der Vorlage dokumentieren. Bei der Bereitstellung von Ressourcen mithilfe der Vorlage kann mit diesem Wert sichergestellt werden, dass die richtige Vorlage verwendet wird. |
| apiProfile |Nein | Eine API-Version, die als Sammlung von API-Versionen für Ressourcentypen dient. Verwenden Sie diesen Wert, um zu vermeiden, dass Sie API-Versionen für jede Ressource in der Vorlage angeben müssen. Wenn Sie eine API-Profilversion aber keine API-Version für den Ressourcentyp angeben, verwendet Resource Manager die API-Version für diesen Ressourcentyp, der im Profil bestimmt wurde.<br><br>Die API-Profileigenschaft ist besonders hilfreich, wenn Sie eine Vorlage in verschiedenen Umgebungen wie Azure Stack und der globalen Azure-Umgebung bereitstellen. Verwenden Sie die API-Profilversion, um sicherzustellen, dass Ihre Vorlage automatisch Versionen verwendet, die in beiden Umgebungen unterstützt werden. Eine Liste der im Profil definierten aktuellen API-Profilversionen und Ressourcen-API-Versionen finden Sie unter [API Profile (API-Profil)](https://github.com/Azure/azure-rest-api-specs/tree/master/profile).<br><br>Weitere Informationen finden Sie unter [Nachverfolgen von Versionen mithilfe von API-Profilen](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [parameters](#parameters) |Nein |Werte, die bei der Bereitstellung angegeben werden, um die Bereitstellung der Ressourcen anpassen. |
| [variables](#variables) |Nein |Werte, die als JSON-Fragmente in der Vorlage verwendet werden, um Vorlagensprachausdrücke zu vereinfachen. |
| [functions](#functions) |Nein |Benutzerdefinierte Funktionen, die in der Vorlage verfügbar sind. |
| [resources](#resources) |Ja |Ressourcentypen, die in einer Ressourcengruppe oder einem Abonnement bereitgestellt oder aktualisiert werden. |
| [outputs](#outputs) |Nein |Werte, die nach der Bereitstellung zurückgegeben werden. |

Jedes Element weist Eigenschaften auf, die Sie festlegen können. In diesem Artikel werden die Abschnitte der Vorlage ausführlicher beschrieben.

## <a name="data-types"></a>Datentypen

In einer ARM-Vorlage können Sie die folgenden Datentypen verwenden:

* Zeichenfolge
* securestring
* INT
* bool
* object
* secureObject
* array

Die folgende Vorlage zeigt das Format für die Datentypen. Jeder Typ verfügt über einen Standardwert im richtigen Format.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "stringParameter": {
      "type": "string",
      "defaultValue": "option 1"
    },
    "intParameter": {
      "type": "int",
      "defaultValue": 1
    },
    "boolParameter": {
        "type": "bool",
        "defaultValue": true
    },
    "objectParameter": {
      "type": "object",
      "defaultValue": {
        "one": "a",
        "two": "b"
      }
    },
    "arrayParameter": {
      "type": "array",
      "defaultValue": [ 1, 2, 3 ]
    }
  },
  "resources": [],
  "outputs": {}
}
```

Die sichere Zeichenfolge verwendet das gleiche Format wie die Zeichenfolge, und das sichere Objekt verwendet das gleiche Format wie das Objekt. Wenn Sie einen Parameter auf eine sichere Zeichenfolge oder ein sicheres Objekt festlegen, wird der Wert des-Parameters weder im Bereitstellungsverlauf gespeichert noch protokolliert. Wenn Sie diesen sicheren Wert jedoch auf eine Eigenschaft festlegen, die keinen sicheren Wert erwartet, wird der Wert nicht geschützt. Wenn Sie z. B. eine sichere Zeichenfolge auf ein Tag festlegen, wird dieser Wert als reiner Text gespeichert. Verwenden Sie sichere Zeichenfolgen für Kennwörter und Geheimnisse.

Für Integer, die als Inlineparameter übergeben werden, ist der Wertebereich möglicherweise durch das SDK oder Befehlszeilentool, das Sie zur Bereitstellung verwenden, eingeschränkt. Wenn Sie beispielsweise PowerShell zum Bereitstellen einer Vorlage verwenden, können Integertypen im Bereich von -2147483648 bis 2147483647 liegen. Um diese Einschränkung zu vermeiden, geben Sie große Werte in einer [Parameterdatei](parameter-files.md) an. Ressourcentypen wenden ihre eigenen Grenzwerte für Integereigenschaften an.

Schließen Sie Boolesche und Integerwerte in Ihrer Vorlage nicht in Anführungszeichen ein. Beginnen und beenden Sie Zeichenfolgenwerte mit doppelten Anführungszeichen.

Objekte beginnen mit einer linken geschweiften Klammer und enden mit einer rechten geschweiften Klammer. Arrays beginnen mit einer linken eckigen Klammer und enden mit einer rechten eckigen Klammer.

## <a name="parameters"></a>Parameter

Im Abschnitt „Parameter“ der Vorlage geben Sie an, welche Werte Sie beim Bereitstellen der Ressourcen eingeben können. Die Anzahl der Parameter in einer Vorlage ist auf 256 beschränkt. Sie können die Anzahl der Parameter verringern, indem Sie Objekte verwenden, die mehrere Eigenschaften enthalten.

Folgende Eigenschaften sind für einen Parameter verfügbar:

```json
"parameters": {
  "<parameter-name>" : {
    "type" : "<type-of-parameter-value>",
    "defaultValue": "<default-value-of-parameter>",
    "allowedValues": [ "<array-of-allowed-values>" ],
    "minValue": <minimum-value-for-int>,
    "maxValue": <maximum-value-for-int>,
    "minLength": <minimum-length-for-string-or-array>,
    "maxLength": <maximum-length-for-string-or-array-parameters>,
    "metadata": {
      "description": "<description-of-the parameter>"
    }
  }
}
```

| Elementname | Erforderlich | BESCHREIBUNG |
|:--- |:--- |:--- |
| parameter-name |Ja |Der Name des Parameters. Es muss sich um einen gültigen JavaScript-Bezeichner handeln. |
| type |Ja |Der Typ des Parameterwerts. Die zulässigen Typen und Werte sind **string**, **securestring**, **int**, **bool**, **object**, **secureObject** und **array**. Siehe [Datentypen](#data-types). |
| defaultValue |Nein |Der Standardwert für den Parameter, wenn kein Wert für den Parameter angegeben wird. |
| allowedValues |Nein |Ein Array der zulässigen Werte für den Parameter, um sicherzustellen, dass der richtige Wert angegeben wird. |
| minValue |Nein |Der Mindestwert für Parameter vom Typ "int", einschließlich des angegebenen Werts. |
| maxValue |Nein |Der Höchstwert für Parameter vom Typ "int", einschließlich des angegebenen Werts. |
| minLength |Nein |Die Mindestlänge der Parameter „string“, „securestring“ und „array“, einschließlich des angegebenen Werts. |
| maxLength |Nein |Die Höchstlänge der Parameter „string“, „securestring“ und „array“, einschließlich des angegebenen Werts. |
| description |Nein |Beschreibung des Parameters, der Benutzern im Portal angezeigt wird. Weitere Informationen finden Sie unter [Kommentare in Vorlagen](#comments). |

Beispiele für die Verwendung von Parametern finden Sie unter [Parameter in ARM-Vorlagen](template-parameters.md).

## <a name="variables"></a>Variables

Im Abschnitt „variables“ erstellen Sie Werte, die in der ganzen Vorlage verwendet werden können. Sie müssen nicht unbedingt Variablen definieren, aber häufig bewirken sie eine Vereinfachung Ihrer Vorlage, indem komplexe Ausdrücke reduziert werden. Das Format der einzelnen Variablen entspricht einem der [Datentypen](#data-types).

Im folgenden Beispiel werden die verfügbaren Optionen zum Definieren einer Variable angezeigt:

```json
"variables": {
  "<variable-name>": "<variable-value>",
  "<variable-name>": {
    <variable-complex-type-value>
  },
  "<variable-object-name>": {
    "copy": [
      {
        "name": "<name-of-array-property>",
        "count": <number-of-iterations>,
        "input": <object-or-value-to-repeat>
      }
    ]
  },
  "copy": [
    {
      "name": "<variable-array-name>",
      "count": <number-of-iterations>,
      "input": <object-or-value-to-repeat>
    }
  ]
}
```

Informationen zur Verwendung von `copy` zum Erstellen mehrerer Werte für eine Variable finden Sie unter [Variableniteration](copy-variables.md).

Beispiele für die Verwendung von Variablen finden Sie unter [Variablen in einer ARM-Vorlage](template-variables.md).

## <a name="functions"></a>Functions

In Ihrer Vorlage können Sie Ihre eigenen Funktionen erstellen. Diese Funktionen stehen dann zur Verwendung in der Vorlage zur Verfügung. Normalerweise definieren Sie komplexe Ausdrücke, die in der Vorlage nicht wiederholt werden sollen. Sie erstellen die benutzerdefinierten Funktionen aus Ausdrücken und [Funktionen](template-functions.md), die in Vorlagen unterstützt werden.

Beim Definieren einer benutzerdefinierten Funktion gelten einige Einschränkungen:

* Die Funktion kann nicht auf Variablen zugreifen.
* Die Funktion kann nur Parameter verwenden, die in der Funktion definiert sind. Bei Verwendung der [parameters-Funktion](template-functions-deployment.md#parameters) innerhalb einer benutzerdefinierten Funktion sind Sie auf die Parameter für diese Funktion beschränkt.
* Die Funktion kann keine anderen benutzerdefinierten Funktionen aufrufen.
* Die Funktion kann nicht die [Referenzfunktion](template-functions-resource.md#reference) verwenden.
* Für die Parameter der Funktion können keine Standardwerte verwendet werden.

```json
"functions": [
  {
    "namespace": "<namespace-for-functions>",
    "members": {
      "<function-name>": {
        "parameters": [
          {
            "name": "<parameter-name>",
            "type": "<type-of-parameter-value>"
          }
        ],
        "output": {
          "type": "<type-of-output-value>",
          "value": "<function-return-value>"
        }
      }
    }
  }
],
```

| Elementname | Erforderlich | BESCHREIBUNG |
|:--- |:--- |:--- |
| Namespace |Ja |Namespace für die benutzerdefinierten Funktionen. Damit können Sie Namenskonflikte mit Vorlagenfunktionen vermeiden. |
| function-name |Ja |Name der benutzerdefinierten Funktion. Wenn Sie die Funktion aufrufen, kombinieren Sie den Funktionsnamen mit dem Namespace. Verwenden Sie z. B. `"[contoso.uniqueName()]"`, um die Funktion „uniqueName“ im Namespace „contoso“ aufzurufen. |
| parameter-name |Nein |Name des Parameters, der in der benutzerdefinierten Funktion verwendet werden soll |
| parameter-value |Nein |Der Typ des Parameterwerts. Die zulässigen Typen und Werte sind **string**, **securestring**, **int**, **bool**, **object**, **secureObject** und **array**. |
| output-type |Ja |Der Typ des Ausgabewerts. Ausgabewerte unterstützen dieselben Typen wie die Eingabeparameter der Funktion. |
| output-value |Ja |Vorlagensprachausdruck, der ausgewertet und von der Funktion zurückgegeben wird |

Beispiele für die Verwendung von benutzerdefinierten Funktionen finden Sie unter [Benutzerdefinierte Funktionen in einer ARM-Vorlage](template-user-defined-functions.md).

## <a name="resources"></a>Ressourcen

Im Ressourcenabschnitt definieren Sie die Ressourcen, die bereitgestellt oder aktualisiert werden.

Sie definieren Ressourcen mit der folgenden Struktur:

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "apiVersion": "<api-version-of-resource>",
      "name": "<name-of-the-resource>",
      "comments": "<your-reference-notes>",
      "location": "<location-of-resource>",
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Elementname | Erforderlich | BESCHREIBUNG |
|:--- |:--- |:--- |
| condition | Nein | Boolescher Wert, der angibt, ob die Ressource während dieser Bereitstellung bereitgestellt wird. Wenn der Wert `true` lautet, wird die Ressource während der Bereitstellung erstellt. Wenn der Wert `false` lautet, wird die Ressource für diese Bereitstellung ausgelassen. Weitere Informationen finden Sie unter [Bedingung](conditional-resource-deployment.md). |
| type |Ja |Der Typ der Ressource. Dieser Wert ist eine Kombination aus dem Namespace des Ressourcenanbieters und dem Ressourcentyp (z.B. **Microsoft.Storage/storageAccounts**). Informationen zum Bestimmen verfügbarer Werte finden Sie in der [Vorlagenreferenz](/azure/templates/). Für eine untergeordnete Ressource hängt das Format des Typs davon ab, ob sie innerhalb der übergeordneten Ressource geschachtelt oder außerhalb der übergeordneten Ressource definiert ist. Weitere Informationen finden Sie unter [Festlegen von Name und Typ für untergeordnete Ressourcen](child-resource-name-type.md). |
| apiVersion |Ja |Version der REST-API zum Erstellen der Ressource. Wenn Sie eine neue Vorlage erstellen, legen Sie diesen Wert auf die neueste Version der Ressource fest, die Sie bereitstellen. Solange die Vorlage wie erforderlich funktioniert, verwenden Sie weiterhin dieselbe API-Version. Indem Sie weiterhin dieselbe API-Version verwenden, minimieren Sie das Risiko, dass eine neue API-Version die Funktionsweise Ihrer Vorlage verändert. Ziehen Sie eine Aktualisierung der API-Version nur dann in Betracht, wenn Sie ein neues Feature verwenden möchten, das in einer späteren Version eingeführt wird. Informationen zum Bestimmen verfügbarer Werte finden Sie in der [Vorlagenreferenz](/azure/templates/). |
| name |Ja |Der Name der Ressource. Der Name muss die Einschränkungen für URI-Komponenten laut Definition in RFC3986 erfüllen. Azure-Dienste, die externen Parteien den Ressourcennamen verfügbar machen, überprüfen den Namen, um sicherzustellen, dass es sich nicht um einen Versuch handelt, eine andere Identität vorzutäuschen. Für eine untergeordnete Ressource hängt das Format des Namens davon ab, ob sie innerhalb der übergeordneten Ressource geschachtelt oder außerhalb der übergeordneten Ressource definiert ist. Weitere Informationen finden Sie unter [Festlegen von Name und Typ für untergeordnete Ressourcen](child-resource-name-type.md). |
| comments |Nein |Ihre Notizen zur Dokumentierung der Ressourcen in Ihrer Vorlage. Weitere Informationen finden Sie unter [Kommentare in Vorlagen](template-syntax.md#comments). |
| location |Varies |Unterstützte Standorte der angegebenen Ressource Wählen Sie einen der verfügbaren Standorte. In der Regel ist es jedoch sinnvoll, einen in der Nähe der Benutzer zu wählen. Normalerweise ist es auch sinnvoll, Ressourcen, die miteinander interagieren, in der gleichen Region zu platzieren. Die meisten Ressourcentypen benötigen einen Speicherort, andere Typen (z.B. eine Rollenzuordnung) jedoch nicht. Weitere Informationen finden Sie unter [Festlegen des Ressourcenspeicherorts](resource-location.md). |
| dependsOn |Nein |Ressourcen, die bereitgestellt werden müssen, bevor diese Ressource bereitgestellt wird. Resource Manager wertet die Abhängigkeiten zwischen den Ressourcen aus und stellt sie in der richtigen Reihenfolge bereit. Wenn Ressourcen nicht voneinander abhängig sind, werden sie parallel bereitgestellt. Der Wert kann eine durch Trennzeichen getrennte Liste von Ressourcennamen oder eindeutigen Ressourcenbezeichnern sein. Es werden nur Ressourcen aufgelistet, die in dieser Vorlage bereitgestellt werden. Ressourcen, die nicht in dieser Vorlage definiert sind, müssen bereits vorhanden sein. Vermeiden Sie das Hinzufügen unnötiger Abhängigkeiten, da diese die Bereitstellung verlangsamen und Ringabhängigkeiten schaffen können. Anleitungen zum Festlegen von Abhängigkeiten finden Sie unter [Definieren der Reihenfolge für die Bereitstellung von Ressourcen in ARM-Vorlagen](define-resource-dependency.md). |
| tags |Nein |Markierungen, die der Ressource zugeordnet sind Verwenden Sie Tags zum logischen Organisieren der Ressourcen in Ihrem Abonnement. |
| sku | Nein | Einige Ressourcen lassen Werte zu, die die bereitzustellende SKU definieren. Beispielsweise können Sie den Typ der Redundanz für ein Speicherkonto angeben. |
| kind | Nein | Einige Ressourcen lassen einen Wert zu, der den Typ der Ressource definiert, die Sie bereitstellen. Beispielsweise können Sie den Typ der zu erstellenden Cosmos DB angeben. |
| copy |Nein |Wenn mehr als eine Instanz erforderlich ist, die Anzahl der zu erstellenden Ressourcen. Der Standardmodus ist parallel. Geben Sie den seriellen Modus an, wenn nicht alle Ressourcen gleichzeitig bereitgestellt werden sollen. Weitere Informationen finden Sie unter [Erstellen mehrerer Instanzen von Ressourcen in Azure Resource Manager](copy-resources.md). |
| Tarif | Nein | Einige Ressourcen lassen Werte zu, die den bereitzustellenden Tarif definieren. Beispielsweise können Sie das Marketplace-Image für einen virtuellen Computer angeben. |
| properties |Nein |Ressourcenspezifische Konfigurationseinstellungen. Die Werte für die Eigenschaften sind mit den Werten identisch, die Sie im Anforderungstext für den REST-API-Vorgang (PUT-Methode) angegeben haben, um die Ressource zu erstellen. Sie können auch ein Kopierarray angeben, um mehrere Instanzen einer Eigenschaft zu erstellen. Informationen zum Bestimmen verfügbarer Werte finden Sie in der [Vorlagenreferenz](/azure/templates/). |
| ressourcen |Nein |Untergeordnete Ressourcen, die von der definierten Ressource abhängig sind. Stellen Sie nur Ressourcentypen bereit, die laut Schema der übergeordneten Ressource zulässig sind. Eine Abhängigkeit von der übergeordneten Ressource ist nicht impliziert. Sie müssen diese Abhängigkeit explizit definieren. Weitere Informationen finden Sie unter [Festlegen von Name und Typ für untergeordnete Ressourcen](child-resource-name-type.md). |

## <a name="outputs"></a>Ausgaben

Im Ausgabenabschnitt legen Sie Werte fest, die von der Bereitstellung zurückgegeben werden. In der Regel geben Sie Werte aus bereitgestellten Ressourcen zurück.

Das folgende Beispiel zeigt die Struktur einer Ausgabedefinition:

```json
"outputs": {
  "<output-name>": {
    "condition": "<boolean-value-whether-to-output-value>",
    "type": "<type-of-output-value>",
    "value": "<output-value-expression>",
    "copy": {
      "count": <number-of-iterations>,
      "input": <values-for-the-variable>
    }
  }
}
```

| Elementname | Erforderlich | BESCHREIBUNG |
|:--- |:--- |:--- |
| output-name |Ja |Name des Ausgabewerts. Es muss sich um einen gültigen JavaScript-Bezeichner handeln. |
| condition |Nein | Boolescher Wert, der angibt, ob dieser Ausgabewert zurückgegeben wird. Wenn `true`, wird der Wert in die Ausgabe für die Bereitstellung einbezogen. Wenn `false`, wird der Ausgabewert für diese Bereitstellung ausgelassen. Wenn keine Angabe erfolgt, lautet der Standardwert `true`. |
| type |Ja |Der Typ des Ausgabewerts. Ausgabewerte unterstützen dieselben Typen wie Vorlagen-Eingabeparameter. Bei Angabe von **securestring** für den Ausgabetyp wird der Wert nicht im Bereitstellungsverlauf angezeigt und kann nicht aus einer anderen Vorlage abgerufen werden. Um einen geheimen Wert in mehreren Vorlagen zu verwenden, speichern Sie das Geheimnis in einer Key Vault-Instanz, und verweisen Sie in der Parameterdatei auf das Geheimnis. Weitere Informationen finden Sie unter [Verwenden von Azure Key Vault zum Übergeben eines sicheren Parameterwerts während der Bereitstellung](key-vault-parameter.md). |
| value |Nein |Vorlagensprachausdruck, der ausgewertet und als Ausgabewert zurückgegeben wird. Geben Sie **value** oder **copy** an. |
| copy |Nein | Wird verwendet, um mehr als einen Wert für eine Ausgabe zurückzugeben. Geben Sie **value** oder **copy** an. Weitere Informationen finden Sie unter [Ausgabeiteration in ARM-Vorlagen](copy-outputs.md). |

Beispiele für die Verwendung von Ausgaben finden Sie unter [Ausgaben in ARM-Vorlagen](template-outputs.md).

<a id="comments"></a>

## <a name="comments-and-metadata"></a>Kommentare und Metadaten

Es gibt mehrere Möglichkeiten, um Kommentare und Metadaten in Ihrer Vorlage hinzuzufügen.

### <a name="comments"></a>Kommentare

Für Inlinekommentare können Sie entweder `//` oder `/* ... */` verwenden, aber diese Syntax funktioniert nicht mit allen Tools. Wenn Sie diese Art von Kommentar hinzufügen, stellen Sie sicher, dass die von Ihnen verwendeten Tools JSON-Inlinekommentare unterstützen.

> [!NOTE]
> Wenn Sie Vorlagen mit Kommentaren mithilfe der Azure CLI, Version 2.3.0 oder niedriger, bereitstellen möchten, müssen Sie den Switch `--handle-extended-json-format` verwenden.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "dependsOn": [ /* storage account and network interface must be deployed first */
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

In Visual Studio Code kann die [Azure Resource Manager-Tools-Erweiterung](quickstart-create-templates-use-visual-studio-code.md) automatisch eine ARM-Vorlage erkennen und den Sprachmodus ändern. Wenn in der rechten unteren Ecke von VS Code **Azure Resource Manager-Vorlage** angezeigt wird, können Sie die Inline-Kommentare verwenden. Die Inlinekommentare werden nicht mehr als ungültig markiert.

![Azure Resource Manager-Vorlagenmodus in Visual Studio Code](./media/template-syntax/resource-manager-template-editor-mode.png)

### <a name="metadata"></a>Metadaten

Sie können ein `metadata`-Objekt fast überall in Ihrer Vorlage hinzufügen. Resource Manager ignoriert das Objekt, aber Sie werden von Ihrem JSON-Editor möglicherweise gewarnt, dass die Eigenschaft nicht gültig ist. Definieren Sie im Objekt die erforderlichen Eigenschaften.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

Fügen Sie für **Parameter** ein `metadata`-Objekt mit einer `description`-Eigenschaft hinzu.

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "User name for the Virtual Machine."
    }
  },
```

Wenn Sie die Vorlage über das Portal bereitstellen, wird der Text, den Sie in der Beschreibung angeben, automatisch als Tipp für diesen Parameter verwendet.

![Parametertipp anzeigen](./media/template-syntax/show-parameter-tip.png)

Fügen Sie für **Ressourcen** ein `comments`-Element oder ein Metadatenobjekt hinzu. Das folgende Beispiel zeigt sowohl ein Kommentarelement als auch ein Metadatenobjekt.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "comments": "Storage account used to store VM disks",
    "location": "[parameters('location')]",
    "metadata": {
      "comments": "These tags are needed for policy compliance."
    },
    "tags": {
      "Dept": "[parameters('deptName')]",
      "Environment": "[parameters('environment')]"
    },
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

Fügen Sie für **Ausgaben** dem Ausgabewert ein Metadatenobjekt hinzu.

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]",
    "metadata": {
      "comments": "Return the fully qualified domain name"
    }
  },
```

Sie können ein Metadatenobjekt nicht zu benutzerdefinierten Funktionen hinzufügen.

## <a name="multi-line-strings"></a>Mehrzeilige Zeichenfolgen

Sie können eine Zeichenfolge in mehrere Zeilen unterteilen. Sehen Sie sich beispielsweise die Standorteigenschaft und einen der Kommentare im folgenden JSON-Beispiel an.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

Wenn Sie Vorlagen mit mehrzeiligen Zeichenfolgen mithilfe der Azure CLI, Version 2.3.0oder niedriger, bereitstellen möchten, müssen Sie den Switch `--handle-extended-json-format` verwenden.

## <a name="next-steps"></a>Nächste Schritte

* Komplette Vorlagen für viele verschiedene Lösungstypen finden Sie unter [Azure-Schnellstartvorlagen](https://azure.microsoft.com/documentation/templates/).
* Ausführliche Informationen zu den Funktionen, die Sie innerhalb einer Vorlage nutzen können, finden Sie unter [Funktionen von ARM-Vorlagen](template-functions.md).
* Informationen zum Zusammenführen mehrerer Vorlagen während der Bereitstellung finden Sie unter [Verwenden von verknüpften und geschachtelten Vorlagen bei der Bereitstellung von Azure-Ressourcen](linked-templates.md).
* Empfehlungen zum Erstellen von Vorlagen finden Sie unter [Bewährte Methoden für ARM-Vorlagen](template-best-practices.md).
* Antworten auf gängige Fragen finden Sie unter [Häufig gestellte Fragen zu ARM-Vorlagen](frequently-asked-questions.md).
