---
title: 'Azure Resource Manager-Vorlagen: Struktur und Syntax | Microsoft-Dokumentation'
description: Beschreibt die Struktur und die Eigenschaften der Azure Resource Manager-Vorlagen mithilfe deklarativer JSON-Syntax.
author: tfitzmac
ms.assetid: 19694cb4-d9ed-499a-a2cc-bcfc4922d7f5
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tomfitz
ms.openlocfilehash: e3b8b6b969568fc15558002c268cdc4a16c2fadd
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431226"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Verstehen der Struktur und Syntax von Azure Resource Manager-Vorlagen

In diesem Artikel wird die Struktur einer Azure Resource Manager-Vorlage beschrieben. Er zeigt die verschiedenen Abschnitte einer Vorlage und die Eigenschaften, die in diesen Abschnitten verfügbar sind. Die Vorlage besteht aus JSON-Code und Ausdrücken, mit denen Sie Werte für Ihre Bereitstellung erstellen können.

Dieser Artikel richtet sich an Benutzer, die bereits Vorkenntnisse zu Resource Manager-Vorlagen haben. Er bietet detaillierte Informationen zur Struktur und Syntax der Vorlage. Eine Einführung in die Erstellung einer Vorlage finden Sie unter [Schnellstart: Erstellen und Bereitstellen von Azure Resource Manager-Vorlagen über das Azure-Portal](resource-manager-create-first-template.md).

## <a name="template-format"></a>Vorlagenformat

In der einfachsten Struktur weist eine Vorlage die folgenden Elemente auf:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
| $schema |Ja |Speicherort der JSON-Schemadatei, die die Version der Vorlagensprache beschreibt.<br><br> Verwenden Sie Folgendes für Bereitstellungen von Ressourcengruppen: `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`.<br><br>Verwenden Sie Folgendes für Bereitstellungen von Abonnements: `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`. |
| contentVersion |Ja |Version der Vorlage (z. B. 1.0.0.0). Sie können einen beliebigen Wert für dieses Element resources. Mit diesem Wert können Sie wichtige Änderungen in der Vorlage dokumentieren. Bei der Bereitstellung von Ressourcen mithilfe der Vorlage kann mit diesem Wert sichergestellt werden, dass die richtige Vorlage verwendet wird. |
| apiProfile |Nein | Eine API-Version, die als Sammlung von API-Versionen für Ressourcentypen dient. Verwenden Sie diesen Wert, um zu vermeiden, dass Sie API-Versionen für jede Ressource in der Vorlage angeben müssen. Wenn Sie eine API-Profilversion aber keine API-Version für den Ressourcentyp angeben, verwendet Resource Manager die API-Version für diesen Ressourcentyp, der im Profil bestimmt wurde.<br><br>Die API-Profileigenschaft ist besonders hilfreich, wenn Sie eine Vorlage in verschiedenen Umgebungen wie Azure Stack und der globalen Azure-Umgebung bereitstellen. Verwenden Sie die API-Profilversion, um sicherzustellen, dass Ihre Vorlage automatisch Versionen verwendet, die in beiden Umgebungen unterstützt werden. Eine Liste der im Profil definierten aktuellen API-Profilversionen und Ressourcen-API-Versionen finden Sie unter [API Profile (API-Profil)](https://github.com/Azure/azure-rest-api-specs/tree/master/profile).<br><br>Weitere Informationen finden Sie unter [Nachverfolgen von Versionen mithilfe von API-Profilen](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [parameters](#parameters) |Nein |Werte, die bei der Bereitstellung angegeben werden, um die Bereitstellung der Ressourcen anpassen. |
| [Variablen](#variables) |Nein |Werte, die als JSON-Fragmente in der Vorlage verwendet werden, um Vorlagensprachausdrücke zu vereinfachen. |
| [functions](#functions) |Nein |Benutzerdefinierte Funktionen, die in der Vorlage verfügbar sind. |
| [resources](#resources) |Ja |Ressourcentypen, die in einer Ressourcengruppe oder einem Abonnement bereitgestellt oder aktualisiert werden. |
| [outputs](#outputs) |Nein |Werte, die nach der Bereitstellung zurückgegeben werden. |

Jedes Element weist Eigenschaften auf, die Sie festlegen können. In diesem Artikel werden die Abschnitte der Vorlage ausführlicher beschrieben.

## <a name="syntax"></a>Syntax

Die grundlegende Syntax der Vorlage ist JSON. Allerdings können Sie Ausdrücke verwenden, um die JSON-Werte zu erweitern, die in der Vorlage zur Verfügung stehen.  Ausdrücke beginnen und enden mit Klammern, entsprechend `[` und `]`. Der Wert des Ausdrucks wird bei der Bereitstellung der Vorlage ausgewertet. Ein Ausdruck kann eine Zeichenfolge, eine ganze Zahl, einen booleschen Wert, ein Array oder ein Objekt zurückgeben. Das folgende Beispiel zeigt einen Ausdruck im Standardwert eines Parameters:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

Innerhalb des Ausdrucks ruft die Syntax `resourceGroup()` eine der Funktionen auf, die Resource Manager für die Verwendung innerhalb einer Vorlage bereitstellt. Genau wie in JavaScript haben Funktionsaufrufe das Format `functionName(arg1,arg2,arg3)`. Die Syntax `.location` ruft eine Eigenschaft des Objekts ab, das von dieser Funktion zurückgegeben wird.

Bei Vorlagenfunktionen und ihren Parametern wird Groß-und Kleinschreibung nicht unterschieden. Der Resource Manager löst beispielsweise **variables('var1')** und **VARIABLES('VAR1')** identisch auf. Bei der Auswertung wird die Groß-/Kleinschreibung beibehalten, sofern diese nicht ausdrücklich durch die Funktion geändert wird (z.B. mit „toUpper“ oder „toLower“). Für spezielle Ressourcentypen gelten möglicherweise Vorgaben zur Schreibweise ungeachtet der Auswertungsweise von Funktionen.

Wenn eine Literalzeichenfolge mit einer öffnenden eckigen Klammer `[` beginnen und einer schließenden eckigen Klammer `]` enden, aber nicht als Ausdruck interpretiert werden soll, fügen Sie am Anfang der Zeichenfolge eine zusätzliche eckige Klammer ein: `[[`. Beispielsweise wird folgende Variable:

```json
"demoVar1": "[[test value]"
```

in `[test value]` aufgelöst.

Wenn die Literalzeichenfolge jedoch nicht mit einer eckigen Klammer endet, dürfen Sie die erste eckige Klammer nicht mit einem Escapezeichen versehen. Beispielsweise wird folgende Variable:

```json
"demoVar2": "[test] value"
```

in `[test] value` aufgelöst.

Verwenden Sie einfache Anführungszeichen, um einen Zeichenfolgenwert als Parameter an eine Funktion zu übergeben.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

Verwenden Sie den umgekehrten Schrägstrich, um doppelte Anführungszeichen in einem Ausdruck mit einem Escapezeichen zu versehen, wie beim Hinzufügen eines JSON-Objekts.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

Ein Vorlagenausdruck darf aus maximal 24.576 Zeichen bestehen.

Unter [Funktionen von Azure-Ressourcen-Manager-Vorlagen](resource-group-template-functions.md)finden Sie eine vollständige Liste der Vorlagenfunktionen. 

## <a name="parameters"></a>Parameter

Im Abschnitt „Parameter“ der Vorlage geben Sie an, welche Werte Sie beim Bereitstellen der Ressourcen eingeben können. Mit diesen Parameterwerten können Sie die Bereitstellung anpassen, indem Sie Werte resources, die für eine bestimmte Umgebung (z. B. Entwicklung, Testing oder Produktion) maßgeschneidert sind. Sie müssen in der Vorlage nicht unbedingt Parameter angeben, aber ohne Parameter stellt Ihre Vorlage immer die gleichen Ressourcen mit den gleichen Namen, Speicherorten und Eigenschaften bereit.

Die Anzahl der Parameter in einer Vorlage ist auf 256 beschränkt. Sie können die Anzahl der Parameter verringern, indem Sie Objekte verwenden, die mehrere Eigenschaften enthalten, wie in diesem Artikel beschrieben wird.

### <a name="available-properties"></a>Verfügbare Eigenschaften

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
| parameterName |Ja |Der Name des Parameters. Es muss sich um einen gültigen JavaScript-Bezeichner handeln. |
| type |Ja |Der Typ des Parameterwerts. Die zulässigen Typen und Werte sind **string**, **securestring**, **int**, **bool**, **object**, **secureObject** und **array**. |
| defaultValue |Nein |Der Standardwert für den Parameter, wenn kein Wert für den Parameter angegeben wird. |
| allowedValues |Nein |Ein Array der zulässigen Werte für den Parameter, um sicherzustellen, dass der richtige Wert angegeben wird. |
| minValue |Nein |Der Mindestwert für Parameter vom Typ "int", einschließlich des angegebenen Werts. |
| maxValue |Nein |Der Höchstwert für Parameter vom Typ "int", einschließlich des angegebenen Werts. |
| minLength |Nein |Die Mindestlänge der Parameter „string“, „securestring“ und „array“, einschließlich des angegebenen Werts. |
| maxLength |Nein |Die Höchstlänge der Parameter „string“, „securestring“ und „array“, einschließlich des angegebenen Werts. |
| description |Nein |Beschreibung des Parameters, der Benutzern im Portal angezeigt wird. Weitere Informationen finden Sie unter [Kommentare in Vorlagen](#comments). |

### <a name="define-and-use-a-parameter"></a>Definieren und Verwenden eines Parameters

Das folgende Beispiel zeigt eine einfache Parameterdefinition. Dabei wird der Name des Parameters definiert, und es wird angegeben, dass er einen Zeichenfolgenwert annimmt. Der Parameter akzeptiert nur Werte, die für seine beabsichtigte Verwendung sinnvoll sind. Er gibt einen Standardwert an, wenn während der Bereitstellung kein Wert angegeben wird. Schließlich enthält den Parameter eine Beschreibung seiner Syntax.

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }   
}
```

In der Vorlage verweisen Sie mithilfe der folgenden Syntax auf den Wert für den Parameter:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

### <a name="template-functions-with-parameters"></a>Vorlagenfunktionen mit Parametern

Wenn Sie den Standardwert für einen Parameter angeben, können Sie die meisten Vorlagenfunktionen verwenden. Sie können einen anderen Parameterwert verwenden, um einen Standardwert zu erstellen. Die folgende Vorlage veranschaulicht die Verwendung von Funktionen im Standardwert:

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

Sie können die `reference`-Funktion nicht im Abschnitt „Parameter“ verwenden. Parameter werden vor der Bereitstellung ausgewertet, daher kann die `reference`-Funktion nicht den Laufzeitstatus einer Ressource abrufen. 

### <a name="objects-as-parameters"></a>Objekte als Parameter

Es kann einfacher sein, in Beziehung stehende Werte zu organisieren, indem sie als Objekt übergeben werden. Diese Vorgehensweise verringert auch die Anzahl der Parameter in der Vorlage.

Definieren Sie den Parameter in Ihrer Vorlage, und geben Sie ein JSON-Objekt an, anstatt einen einzelnen Wert während der Bereitstellung zu übergeben. 

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "location": "eastus",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

Verweisen Sie anschließend auf die Untereigenschaften des Parameters, indem Sie den Punktoperator verwenden.

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

### <a name="parameter-example-templates"></a>Parameterbeispielvorlagen

Diese Beispielvorlagen veranschaulichen einige Szenarien für die Verwendung von Parametern. Stellen Sie sie bereit, um zu testen, wie Parameter in verschiedenen Szenarien verarbeitet werden.

|Vorlage  |BESCHREIBUNG  |
|---------|---------|
|[Parameter mit Funktionen für Standardwerte](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Zeigt, wie Vorlagenfunktionen verwendet werden, wenn Sie Standardwerte für Parameter definieren. Die Vorlage stellt keine Ressourcen bereit. Sie erstellt Parameterwerte und gibt diese Werte zurück. |
|[Parameterobjekt](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Veranschaulicht die Verwendung eines Objekts für einen Parameter. Die Vorlage stellt keine Ressourcen bereit. Sie erstellt Parameterwerte und gibt diese Werte zurück. |

## <a name="variables"></a>Variables

Im Abschnitt „variables“ erstellen Sie Werte, die in der ganzen Vorlage verwendet werden können. Sie müssen nicht unbedingt Variablen definieren, aber häufig bewirken sie eine Vereinfachung Ihrer Vorlage, indem komplexe Ausdrücke reduziert werden.

### <a name="available-definitions"></a>Verfügbare Definitionen

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

Informationen zur Verwendung von `copy` zum Erstellen mehrerer Werte für eine Variable finden Sie unter [Variableniteration](resource-group-create-multiple.md#variable-iteration).

### <a name="define-and-use-a-variable"></a>Definieren und Verwenden einer Variablen

Im folgenden Beispiel wird eine Variablendefinition gezeigt. Dabei wird ein Zeichenfolgenwert für einen Speicherkontonamen erstellt. Es werden mehrere Vorlagenfunktionen verwendet, um einen Parameterwert abzurufen und zu einer eindeutigen Zeichenfolge zu verketten.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Sie verwenden die Variable, wenn Sie die Ressource definieren.

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

### <a name="configuration-variables"></a>Konfigurationsvariablen

Sie können komplexe JSON-Typen verwenden, um verwandte Werte für eine Umgebung zu definieren.

```json
"variables": {
  "environmentSettings": {
    "test": {
      "instanceSize": "Small",
      "instanceCount": 1
    },
    "prod": {
      "instanceSize": "Large",
      "instanceCount": 4
    }
  }
},
```

Unter „Parameters“ erstellen Sie einen Wert, der angibt, welche Konfigurationswerte verwendet werden sollen.

```json
"parameters": {
  "environmentName": {
    "type": "string",
    "allowedValues": [
      "test",
      "prod"
    ]
  }
},
```

Sie rufen die aktuellen Einstellungen folgendermaßen ab:

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

### <a name="variable-example-templates"></a>Variablenbeispielvorlagen

Diese Beispielvorlagen veranschaulichen einige Szenarien für die Verwendung von Variablen. Stellen Sie sie bereit, um zu testen, wie Variablen in verschiedenen Szenarien verarbeitet werden. 

|Vorlage  |BESCHREIBUNG  |
|---------|---------|
| [Variablendefinitionen](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Zeigt die verschiedenen Typen von Variablen. Die Vorlage stellt keine Ressourcen bereit. Sie erstellt Variablenwerte und gibt diese Werte zurück. |
| [Konfigurationsvariable](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Zeigt die Verwendung einer Variablen, die Konfigurationswerte definiert. Die Vorlage stellt keine Ressourcen bereit. Sie erstellt Variablenwerte und gibt diese Werte zurück. |
| [Netzwerksicherheitsregeln](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) und [Parameterdatei](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Generiert ein Array im richtigen Format zum Zuweisen von Sicherheitsregeln zu einer Netzwerksicherheitsgruppe. |


## <a name="functions"></a>Functions

In Ihrer Vorlage können Sie Ihre eigenen Funktionen erstellen. Diese Funktionen stehen dann zur Verwendung in der Vorlage zur Verfügung. Normalerweise definieren Sie komplexe Ausdrücke, die in der Vorlage nicht wiederholt werden sollen. Sie erstellen die benutzerdefinierten Funktionen aus Ausdrücken und [Funktionen](resource-group-template-functions.md), die in Vorlagen unterstützt werden.

Beim Definieren einer benutzerdefinierten Funktion gelten einige Einschränkungen:

* Die Funktion kann nicht auf Variablen zugreifen.
* Die Funktion kann nur Parameter verwenden, die in der Funktion definiert sind. Bei Verwendung der [parameters-Funktion](resource-group-template-functions-deployment.md#parameters) innerhalb einer benutzerdefinierten Funktion sind Sie auf die Parameter für diese Funktion beschränkt.
* Die Funktion kann keine anderen benutzerdefinierten Funktionen aufrufen.
* Die Funktion kann nicht die [Referenzfunktion](resource-group-template-functions-resource.md#reference) verwenden.
* Für die Parameter der Funktion können keine Standardwerte verwendet werden.

Für benutzerdefinierte Funktionen müssen Namespacewerte verwendet werden, um Namenskonflikte mit Vorlagenfunktionen zu vermeiden. Im folgenden Beispiel wird eine Funktion veranschaulicht, die einen Speicherkontonamen zurückgibt:

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

Sie rufen die Funktion wie folgt auf:

```json
"resources": [
  {
    "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "location": "South Central US",
    "tags": {},
    "properties": {}
  }
]
```

## <a name="resources"></a>Ressourcen
Im Ressourcenabschnitt definieren Sie die Ressourcen, die bereitgestellt oder aktualisiert werden.

### <a name="available-properties"></a>Verfügbare Eigenschaften

Sie definieren Ressourcen mit der folgenden Struktur:

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "apiVersion": "<api-version-of-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "name": "<name-of-the-resource>",
      "location": "<location-of-resource>",
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "comments": "<your-reference-notes>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
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
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Elementname | Erforderlich | BESCHREIBUNG |
|:--- |:--- |:--- |
| condition | Nein | Boolescher Wert, der angibt, ob die Ressource während dieser Bereitstellung bereitgestellt wird. Wenn der Wert `true` lautet, wird die Ressource während der Bereitstellung erstellt. Wenn der Wert `false` lautet, wird die Ressource für diese Bereitstellung ausgelassen. Weitere Informationen finden Sie unter [Bedingung](#condition). |
| apiVersion |Ja |Version der REST-API zum Erstellen der Ressource. Informationen zum Bestimmen verfügbarer Werte finden Sie in der [Vorlagenreferenz](/azure/templates/). |
| type |Ja |Der Typ der Ressource. Dieser Wert ist eine Kombination aus dem Namespace des Ressourcenanbieters und dem Ressourcentyp (z.B. **Microsoft.Storage/storageAccounts**). Informationen zum Bestimmen verfügbarer Werte finden Sie in der [Vorlagenreferenz](/azure/templates/). Für eine untergeordnete Ressource hängt das Format des Typs davon ab, ob sie innerhalb der übergeordneten Ressource geschachtelt oder außerhalb der übergeordneten Ressource definiert ist. Weitere Informationen finden Sie unter [Untergeordnete Ressourcen](#child-resources). |
| name |Ja |Der Name der Ressource. Der Name muss die Einschränkungen für URI-Komponenten laut Definition in RFC3986 erfüllen. Darüber hinaus überprüfen Azure-Dienste, die externen Parteien den Ressourcennamen verfügbar machen, den Namen, um sicherzustellen, dass es sich nicht um einen Versuch handelt, eine andere Identität vorzutäuschen. Für eine untergeordnete Ressource hängt das Format des Namens davon ab, ob sie innerhalb der übergeordneten Ressource geschachtelt oder außerhalb der übergeordneten Ressource definiert ist. Weitere Informationen finden Sie unter [Untergeordnete Ressourcen](#child-resources). |
| location |Variabel |Unterstützte Standorte der angegebenen Ressource Wählen Sie einen der verfügbaren Standorte. In der Regel ist es jedoch sinnvoll, einen in der Nähe der Benutzer zu wählen. Normalerweise ist es auch sinnvoll, Ressourcen, die miteinander interagieren, in der gleichen Region zu platzieren. Die meisten Ressourcentypen benötigen einen Speicherort, andere Typen (z.B. eine Rollenzuordnung) jedoch nicht. |
| tags |Nein |Markierungen, die der Ressource zugeordnet sind Verwenden Sie Tags zum logischen Organisieren der Ressourcen in Ihrem Abonnement. |
| Kommentare |Nein |Ihre Notizen zur Dokumentierung der Ressourcen in Ihrer Vorlage. Weitere Informationen finden Sie unter [Kommentare in Vorlagen](resource-group-authoring-templates.md#comments). |
| copy |Nein |Wenn mehr als eine Instanz erforderlich ist, die Anzahl der zu erstellenden Ressourcen. Der Standardmodus ist parallel. Geben Sie den seriellen Modus an, wenn nicht alle Ressourcen gleichzeitig bereitgestellt werden sollen. Weitere Informationen finden Sie unter [Erstellen mehrerer Instanzen von Ressourcen in Azure Resource Manager](resource-group-create-multiple.md). |
| dependsOn |Nein |Ressourcen, die bereitgestellt werden müssen, bevor diese Ressource bereitgestellt wird. Resource Manager wertet die Abhängigkeiten zwischen den Ressourcen aus und stellt sie in der richtigen Reihenfolge bereit. Wenn Ressourcen nicht voneinander abhängig sind, werden sie parallel bereitgestellt. Der Wert kann eine durch Trennzeichen getrennte Liste von Ressourcennamen oder eindeutigen Ressourcenbezeichnern sein. Es werden nur Ressourcen aufgelistet, die in dieser Vorlage bereitgestellt werden. Ressourcen, die nicht in dieser Vorlage definiert sind, müssen bereits vorhanden sein. Vermeiden Sie das Hinzufügen unnötiger Abhängigkeiten, da diese die Bereitstellung verlangsamen und Ringabhängigkeiten schaffen können. Tipps für das Festlegen von Abhängigkeiten finden Sie unter [Definieren von Abhängigkeiten in Azure Resource Manager-Vorlagen](resource-group-define-dependencies.md). |
| properties |Nein |Ressourcenspezifische Konfigurationseinstellungen. Die Werte für die Eigenschaften sind mit den Werten identisch, die Sie im Anforderungstext für den REST-API-Vorgang (PUT-Methode) angegeben haben, um die Ressource zu erstellen. Sie können auch ein Kopierarray angeben, um mehrere Instanzen einer Eigenschaft zu erstellen. Informationen zum Bestimmen verfügbarer Werte finden Sie in der [Vorlagenreferenz](/azure/templates/). |
| sku | Nein | Einige Ressourcen lassen Werte zu, die die bereitzustellende SKU definieren. Beispielsweise können Sie den Typ der Redundanz für ein Speicherkonto angeben. |
| kind | Nein | Einige Ressourcen lassen einen Wert zu, der den Typ der Ressource definiert, die Sie bereitstellen. Beispielsweise können Sie den Typ der zu erstellenden Cosmos DB angeben. |
| Tarif | Nein | Einige Ressourcen lassen Werte zu, die den bereitzustellenden Tarif definieren. Beispielsweise können Sie das Marketplace-Image für einen virtuellen Computer angeben. | 
| ressourcen |Nein |Untergeordnete Ressourcen, die von der definierten Ressource abhängig sind. Stellen Sie nur Ressourcentypen bereit, die laut Schema der übergeordneten Ressource zulässig sind. Eine Abhängigkeit von der übergeordneten Ressource ist nicht impliziert. Sie müssen diese Abhängigkeit explizit definieren. Weitere Informationen finden Sie unter [Untergeordnete Ressourcen](#child-resources). |

### <a name="condition"></a>Bedingung

Wenn Sie sich während der Bereitstellung entscheiden müssen, ob Sie eine Ressource erstellen möchten, verwenden Sie das `condition`-Element. Der Wert für dieses Element wird mit „true“ oder „false“ aufgelöst. Wenn der Wert TRUE ist, wird die Ressource erstellt. Wenn der Wert FALSE ist, wird die Ressource nicht erstellt. Der Wert kann nur auf die gesamte Ressource angewandt werden.

Dieser Wert wird üblicherweise verwendet, wenn Sie eine neue Ressource erstellen oder eine bereits vorhandene verwenden möchten. Verwenden Sie beispielsweise Folgendes, um anzugeben, ob ein neues Speicherkonto bereitgestellt wird oder ob ein vorhandenes Speicherkonto verwendet wird:

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

Eine vollständige Beispielvorlage, die das `condition`-Element verwendet, finden Sie unter [VM mit einem neuen oder vorhandenen virtuellen Netzwerk, Speicher und einer neuen oder vorhandenen öffentlichen IP-Adresse](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions).

Bei Verwendung einer Funktion vom Typ [reference](resource-group-template-functions-resource.md#reference) oder [list](resource-group-template-functions-resource.md#list) mit einer Ressource, die bedingt bereitgestellt wird, wird die Funktion auch dann ausgewertet, wenn die Ressource nicht bereitgestellt wird. Es wird eine Fehlermeldung angezeigt, wenn die Funktion auf eine nicht vorhandene Ressource verweist. Verwenden Sie die [if](resource-group-template-functions-logical.md#if)-Funktion, um sicherzustellen, dass die Funktion nur für Bedingungen ausgewertet wird, wenn die Ressource bereitgestellt wird. Eine Beispielvorlage, die „if“ und „reference“ mit einer bedingt bereitgestellten Ressource verwendet, finden Sie unter der Funktion [if](resource-group-template-functions-logical.md#if).

### <a name="resource-names"></a>Ressourcennamen

Im Allgemeinen arbeiten Sie in Resource Manager mit drei Arten von Ressourcennamen:

* Ressourcennamen, die eindeutig sein müssen.
* Ressourcennamen, die nicht eindeutig sein müssen. Sie können jedoch einen Namen angeben, der das Identifizieren der Ressource vereinfacht.
* Ressourcennamen, die allgemein sein können.

Geben Sie einen **eindeutigen Ressourcennamen** für alle Ressourcentypen an, die über einen Datenzugriffsendpunkt verfügen. Es folgen allgemeine Ressourcentypen, für die ein eindeutiger Name erforderlich ist:

* Azure Storage<sup>1</sup> 
* Web-Apps-Funktion von Azure App Service
* SQL Server
* Azure Key Vault
* Azure Cache for Redis
* Azure Batch
* Azure Traffic Manager
* Azure Search
* Azure HDInsight

<sup>1</sup>Für Namen von Speicherkonten gilt darüber hinaus Folgendes: Kleinbuchstaben, max. 24 Zeichen, keine Bindestriche.

Wenn Sie den Namen festlegen, können Sie entweder manuell einen eindeutigen Namen erstellen oder die [uniqueString()](resource-group-template-functions-string.md#uniquestring)-Funktion zum Generieren eines Namens verwenden. Sie können auch ein Präfix oder Suffix zum **uniqueString**-Ergebnis hinzufügen. Wenn Sie den eindeutigen Namen ändern, können Sie den Ressourcentyp einfacher aus dem Namen ableiten. Beispielsweise können Sie einen eindeutigen Namen für ein Speicherkonto mit der folgenden Variablen generieren:

```json
"variables": {
  "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

Für einige Ressourcentypen möchten Sie möglicherweise einen **Namen zur Identifizierung** angeben, der Name muss jedoch nicht eindeutig sein. Geben Sie für diese Ressourcentypen einen Namen an, der ihre Verwendung oder Charakteristiken beschreibt.

```json
"parameters": {
  "vmName": { 
    "type": "string",
    "defaultValue": "demoLinuxVM",
    "metadata": {
      "description": "The name of the VM to create."
    }
  }
}
```

Für Ressourcentypen, auf die hauptsächlich über eine andere Ressource zugegriffen wird, können Sie einen **allgemeinen Namen** wählen, der in der Vorlage hartcodiert ist. Beispielsweise können Sie einen allgemeinen Standardnamen für Firewallregeln auf einer SQL Server-Instanz festlegen:

```json
{
  "type": "firewallrules",
  "name": "AllowAllWindowsAzureIps",
  ...
}
```

### <a name="resource-location"></a>Speicherort von Ressourcen

Beim Bereitstellen einer Vorlage müssen Sie einen Speicherort für jede Ressource angeben. An verschiedenen Speicherorten werden unterschiedliche Ressourcentypen unterstützt. Informationen zum Abrufen der unterstützten Speicherorte für einen Ressourcentyp finden Sie unter [Azure-Ressourcenanbieter und -typen](resource-manager-supported-services.md).

Verwenden Sie einen Parameter zur Angabe des Standorts für Ressourcen, und legen den Standardwert auf `resourceGroup().location` fest.

Das folgende Beispiel zeigt ein Speicherkonto, das an einem als Parameter angegebenen Speicherort bereitgestellt wird:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('storage', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

### <a name="child-resources"></a>Untergeordnete Ressourcen

Innerhalb einiger Ressourcen können Sie auch ein Array untergeordneter Ressourcen definieren. Untergeordnete Ressourcen sind Ressourcen, die nur im Kontext einer anderen Ressource verfügbar sind. Beispielsweise kann eine SQL-Datenbank nicht ohne einen SQL-Server vorhanden sein – also ist die Datenbank ein untergeordnetes Element des Servers. Sie können die Datenbank in der Definition für den Server definieren.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Sql/servers",
  "name": "exampleserver",
  ...
  "resources": [
    {
      "apiVersion": "2017-10-01-preview",
      "type": "databases",
      "name": "exampledatabase",
      ...
    }
  ]
}
```

Sie müssen die Datenbank aber nicht auf dem Server definieren. Sie können die untergeordnete Ressource auf der obersten Ebene definieren. So können Sie vorgehen, wenn die übergeordnete Ressource nicht in derselben Vorlage bereitgestellt wird oder wenn Sie mithilfe von `copy` mehrere untergeordnete Ressourcen erstellen möchten. In diesem Fall müssen Sie den vollständigen Ressourcentyp angeben und den Namen der übergeordneten Ressource in den Namen der untergeordneten Ressource einfügen.

```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Sql/servers",
  "name": "exampleserver",
  "resources": [ 
  ],
  ...
},
{
  "apiVersion": "2017-10-01-preview",
  "type": "Microsoft.Sql/servers/databases",
  "name": "exampleserver/exampledatabase",
  ...
}
```

Welche Werte Sie für Typ und Namen angeben, richtet sich danach, ob die untergeordnete Ressource innerhalb oder außerhalb der übergeordneten Ressource definiert ist.

Wenn sie in der übergeordneten Ressource geschachtelt ist, verwenden Sie:

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

Wenn sie außerhalb der übergeordneten Ressource definiert ist, verwenden Sie:

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

Wenn sie geschachtelt ist, wird der Typ auf `databases` festgelegt, der vollständige Ressourcentyp lautet jedoch immer noch `Microsoft.Sql/servers/databases`. `Microsoft.Sql/servers/` wird nicht angegeben, da dieser Teil vom übergeordneten Ressourcentyp übernommen wird. Der Name der untergeordneten Ressource ist auf `exampledatabase` festgelegt, der vollständige Name enthält jedoch auch den Namen der übergeordneten Ressource. `exampleserver` wird nicht angegeben, da dieser Teil von der übergeordneten Ressource übernommen wird.

Wenn Sie einen vollqualifizierten Verweis auf eine Ressource erstellen, ist die Reihenfolge für die Kombination von Segmenten von Typ und Name nicht einfach eine Verkettung beider Werte. Verwenden Sie stattdessen nach dem Namespace eine Folge von *Typ-Name*-Paaren, beginnend mit dem am wenigsten spezifischen bis zum spezifischsten:

```json
{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]*
```

Beispiel:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` ist richtig `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` ist nicht richtig

## <a name="outputs"></a>Ausgaben

Im Ausgabenabschnitt legen Sie Werte fest, die von der Bereitstellung zurückgegeben werden. In der Regel geben Sie Werte aus bereitgestellten Ressourcen zurück.

### <a name="available-properties"></a>Verfügbare Eigenschaften

Das folgende Beispiel zeigt die Struktur einer Ausgabedefinition:

```json
"outputs": {
  "<outputName>" : {
    "condition": "<boolean-value-whether-to-output-value>",
    "type" : "<type-of-output-value>",
    "value": "<output-value-expression>"
  }
}
```

| Elementname | Erforderlich | BESCHREIBUNG |
|:--- |:--- |:--- |
| outputName |Ja |Name des Ausgabewerts. Es muss sich um einen gültigen JavaScript-Bezeichner handeln. |
| condition |Nein | Boolescher Wert, der angibt, ob dieser Ausgabewert zurückgegeben wird. Wenn `true`, wird der Wert in die Ausgabe für die Bereitstellung einbezogen. Wenn `false`, wird der Ausgabewert für diese Bereitstellung ausgelassen. Wenn keine Angabe erfolgt, lautet der Standardwert `true`. |
| type |Ja |Der Typ des Ausgabewerts. Ausgabewerte unterstützen dieselben Typen wie Vorlagen-Eingabeparameter. Bei Angabe von **securestring** für den Ausgabetyp wird der Wert nicht im Bereitstellungsverlauf angezeigt und kann nicht aus einer anderen Vorlage abgerufen werden. Um einen geheimen Wert in mehreren Vorlagen zu verwenden, speichern Sie das Geheimnis in einer Key Vault-Instanz, und verweisen Sie in der Parameterdatei auf das Geheimnis. Weitere Informationen finden Sie unter [Verwenden von Azure Key Vault zum Übergeben eines sicheren Parameterwerts während der Bereitstellung](resource-manager-keyvault-parameter.md). |
| value |Ja |Vorlagensprachausdruck, der ausgewertet und als Ausgabewert zurückgegeben wird. |

### <a name="define-and-use-output-values"></a>Definieren und Verwenden von Ausgabewerten

Das folgende Beispiel zeigt die Vorgehensweise zum Zurückgeben der Ressourcen-ID für eine öffentliche IP-Adresse:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Das nächste Beispiel zeigt, wie die Ressourcen-ID für eine öffentliche IP-Adresse abhängig davon zurückgegeben wird, ob eine neue Ressourcen-ID bereitgestellt wurde:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Ein einfaches Beispiel der bedingten Ausgabe finden Sie unter der [Vorlage für die bedingte Ausgabe](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

Nach der Bereitstellung können Sie den Wert per Skript abrufen. Verwenden Sie für PowerShell Folgendes:

```powershell
(Get-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

Verwenden Sie für die Azure-Befehlszeilenschnittstelle den folgenden Befehl:

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

Mit der Funktion [reference](resource-group-template-functions-resource.md#reference) können Sie den Ausgabewert aus einer verknüpften Vorlage abrufen. Um einen Ausgabewert aus einer verknüpften Vorlage abzurufen, rufen Sie den Eigenschaftswert mit einer der folgenden ähnlichen Syntax ab: `"[reference('deploymentName').outputs.propertyName.value]"`.

Wenn Sie eine Ausgabeeigenschaft von einer verknüpften Vorlage abrufen, kann der Name der Eigenschaft keinen Bindestrich enthalten.

Das folgende Beispiel zeigt, wie Sie die IP-Adresse auf einen Lastenausgleich festlegen, indem Sie einen Wert aus einer verknüpften Vorlage abrufen.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Sie können die Funktion `reference` nicht im Ausgabeabschnitt einer [geschachtelten Vorlage](resource-group-linked-templates.md#link-or-nest-a-template) verwenden. Um die Werte für eine bereitgestellte Ressource in einer geschachtelten Vorlage zurückzugeben, konvertieren Sie Ihre geschachtelte Vorlage in eine verknüpfte Vorlage.

### <a name="output-example-templates"></a>Ausgabebeispielvorlagen

|Vorlage  |BESCHREIBUNG  |
|---------|---------|
|[Variablen kopieren](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Erstellt komplexe Variablen und gibt die entsprechenden Werte aus. Stellt keine Ressourcen bereit. |
|[Öffentliche IP-Adresse](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Erstellt eine öffentliche IP-Adresse und gibt die Ressourcen-ID aus. |
|[Load Balancer](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Stellt eine Verknüpfung mit der vorherigen Vorlage her. Verwendet beim Erstellen des Lastenausgleichs die Ressourcen-ID in der Ausgabe. |


<a id="comments" />

## <a name="comments-and-metadata"></a>Kommentare und Metadaten

Es gibt mehrere Möglichkeiten, um Kommentare und Metadaten in Ihrer Vorlage hinzuzufügen.

Sie können ein `metadata`-Objekt fast überall in Ihrer Vorlage hinzufügen. Resource Manager ignoriert das Objekt, aber Sie werden von Ihrem JSON-Editor möglicherweise gewarnt, dass die Eigenschaft nicht gültig ist. Definieren Sie im Objekt die erforderlichen Eigenschaften.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

![Parametertipp anzeigen](./media/resource-group-authoring-templates/show-parameter-tip.png)

Fügen Sie für **Ressourcen** ein `comments`-Element oder ein Metadatenobjekt hinzu. Das folgende Beispiel zeigt sowohl ein Kommentarelement als auch ein Metadatenobjekt.

```json
"resources": [
  {
    "comments": "Storage account used to store VM disks",
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
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

Für Inlinekommentare können Sie `//` verwenden, aber diese Syntax funktioniert nicht mit allen Tools. Sie können nicht die Azure-Befehlszeilenschnittstelle verwenden, um die Vorlage mit Inlinekommentaren bereitzustellen. Und Sie können nicht den Portalvorlageneditor verwenden, um an Vorlagen mit Inlinekommentaren zu arbeiten. Wenn Sie diese Art von Kommentar hinzufügen, stellen Sie sicher, dass die von Ihnen verwendeten Tools JSON-Inlinekommentare unterstützen.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "apiVersion": "2018-10-01",
  "dependsOn": [ // storage account and network interface must be deployed first
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

Im VS-Code können Sie den Sprachmodus auf „JSON mit Kommentaren“ festlegen. Die Inlinekommentare werden nicht mehr als ungültig markiert. So ändern Sie den Modus

1. Öffnen Sie die Sprachmodusauswahl (STRG+K M).

1. Wählen Sie **JSON mit Kommentaren** aus.

   ![Auswählen des Sprachmodus](./media/resource-group-authoring-templates/select-json-comments.png)

[!INCLUDE [arm-tutorials-quickstarts](../../includes/resource-manager-tutorials-quickstarts.md)]

## <a name="next-steps"></a>Nächste Schritte
* Komplette Vorlagen für viele verschiedene Lösungstypen finden Sie unter [Azure-Schnellstartvorlagen](https://azure.microsoft.com/documentation/templates/).
* Ausführliche Informationen zu den Funktionen, die Sie innerhalb einer Vorlage nutzen können, finden Sie unter [Funktionen von Azure Resource Manager-Vorlagen](resource-group-template-functions.md).
* Informationen zum Zusammenführen mehrerer Vorlagen während der Bereitstellung finden Sie unter [Verwenden von verknüpften Vorlagen mit Azure Resource Manager](resource-group-linked-templates.md).
* Empfehlungen zum Erstellen von Vorlagen finden Sie unter [Azure Resource Manager template best practices (Bewährte Methoden für das Erstellen von Azure Resource Manager-Vorlagen)](template-best-practices.md).
* Weitere Empfehlungen zum Erstellen von Resource Manager-Vorlagen, die Sie in allen Azure-Umgebungen und in Azure Stack verwenden können, finden Sie unter [Entwickeln von Azure Resource Manager-Vorlagen für cloudübergreifende Konsistenz](templates-cloud-consistency.md).
