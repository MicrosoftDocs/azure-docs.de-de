---
title: Bereitstellen mehrerer Instanzen von Azure-Ressourcen | Microsoft-Dokumentation
description: Verwenden Sie den copy-Vorgang und Arrays in einer Azure-Ressourcen-Manager-Vorlage, um sie beim Bereitstellen von Ressourcen mehrere Male zu durchlaufen.
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: tomfitz
ms.openlocfilehash: 99fd4215de4dd118558acc008fcfa6490ea0093d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66807369"
---
# <a name="resource-property-or-variable-iteration-in-azure-resource-manager-templates"></a>Iteration von Ressourcen, Eigenschaften oder Variablen in Azure Resource Manager-Vorlagen

In diesem Artikel erfahren Sie, wie Sie in Ihrer Azure Resource Manager-Vorlage mehrere Instanzen einer Ressource, Variablen oder Eigenschaft erstellen. Um mehrere Instanzen zu erstellen, fügen Sie das `copy`-Objekt zur Vorlage hinzu.

Bei Verwendung mit einer Ressource weist das copy-Objekt das folgende Format auf:

```json
"copy": {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "mode": "serial" <or> "parallel",
    "batchSize": <number-to-deploy-serially>
}
```

Bei Verwendung mit einer Variablen oder Eigenschaft weist das copy-Objekt das folgende Format auf:

```json
"copy": [
  {
      "name": "<name-of-loop>",
      "count": <number-of-iterations>,
      "input": <values-for-the-property-or-variable>
  }
]
```

Beide Verwendungen werden in diesem Artikel ausführlicher beschrieben. Ein Tutorial finden Sie unter [Tutorial: Erstellen mehrerer Ressourceninstanzen mit Resource Manager-Vorlagen](./resource-manager-tutorial-create-multiple-instances.md).

Wenn Sie angeben müssen, ob eine Ressource überhaupt bereitgestellt wird, finden Sie die erforderlichen Informationen unter [Element „condition“](resource-group-authoring-templates.md#condition).

## <a name="copy-limits"></a>Einschränkungen für „copy“

Um die Anzahl der Iterationen festzulegen, geben Sie einen Wert für die count-Eigenschaft an. Der Wert von „count“ darf 800 nicht überschreiten.

Der Wert von „count“ darf nicht negativ sein. Wenn Sie eine Vorlage mit der REST-API-Version **2019-05-10** oder höher bereitstellen, können Sie „count“ auf „0“ (null) festlegen. Frühere Versionen der REST-API unterstützen den Wert „0“ (null) für „count“ nicht. Die Azure-Befehlszeilenschnittstelle oder PowerShell unterstützen den Wert „0“ (null) für „count“ derzeit nicht, aber die Unterstützung wird in einer zukünftigen Version hinzugefügt.

Die Einschränkungen für „count“ sind für Ressourcen, Variablen und Eigenschaften gleich.

## <a name="resource-iteration"></a>Ressourceniteration

Wenn Sie sich während der Bereitstellung entscheiden müssen, eine oder mehrere Instanzen einer Ressource zu erstellen, fügen Sie dem Ressourcentyp ein `copy`-Element hinzu. Geben Sie im copy-Element die Anzahl von Iterationen und einen Namen für diese Schleife an.

Die Ressource zum mehrfachen Erstellen übernimmt das folgende Format:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
      }
    }
  ],
  "outputs": {}
}
```

Beachten Sie, dass der Name der einzelnen Ressourcen die `copyIndex()`-Funktion enthält, die die aktuelle Iteration in der Schleife zurückgibt. `copyIndex()` ist nullbasiert. Im folgenden Beispiel werden die unten aufgeführten Namen erstellt:

```json
"name": "[concat('storage', copyIndex())]",
```

Namen:

* storage0
* storage1
* storage2

Zum Versetzen des Indexwerts können Sie einen Wert in der copyIndex()-Funktion übergeben. Die Anzahl von Iterationen wird weiterhin im copy-Element angegeben, aber der Wert von „copyIndex“ wird um den angegebenen Wert versetzt. Im folgenden Beispiel werden die unten aufgeführten Namen erstellt:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Namen:

* storage1
* storage2
* storage3

Der „copy“-Vorgang ist besonders bei Verwendung von Arrays hilfreich, weil Sie jedes Element im Array durchlaufen können. Verwenden Sie die Funktion `length` für das Array, um die Anzahl von Iterationen anzugeben, und `copyIndex`, um den aktuellen Index im Array abzurufen. Im folgenden Beispiel werden die unten aufgeführten Namen erstellt:

```json
"parameters": { 
  "org": { 
    "type": "array", 
    "defaultValue": [ 
      "contoso", 
      "fabrikam", 
      "coho" 
    ] 
  }
}, 
"resources": [ 
  { 
    "name": "[concat('storage', parameters('org')[copyIndex()])]", 
    "copy": { 
      "name": "storagecopy", 
      "count": "[length(parameters('org'))]" 
    }, 
    ...
  } 
]
```

Namen:

* storagecontoso
* storagefabrikam
* storagecoho

Resource Manager erstellt die Ressourcen standardmäßig gleichzeitig. Die Reihenfolge, in der sie erstellt werden, ist nicht garantiert. Es ist aber möglicherweise sinnvoll, anzugeben, dass die Ressource sequenziell bereitgestellt werden. Wenn Sie z.B. eine Produktionsumgebung aktualisieren, möchten Sie die Updates möglicherweise staffeln, sodass nur eine bestimmte Anzahl von Ressourcen gleichzeitig aktualisiert wird.

Legen Sie zum seriellen Bereitstellen mehrerer Instanzen einer Ressource `mode` auf **serial** und `batchSize` auf die Anzahl der Instanzen fest, die zu einem Zeitpunkt bereitgestellt werden sollen. Im seriellen Modus erstellt Resource Manager eine Abhängigkeit von früheren Instanzen in der Schleife, sodass ein Batch erst dann gestartet wird, wenn der vorherige Batch abgeschlossen wurde.

Zum seriellen Bereitstellen von zwei Speicherkonten gleichzeitig verwenden Sie beispielsweise:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      }
    }
  ],
  "outputs": {}
}
```

Die mode-Eigenschaft akzeptiert auch **parallel**, wobei es sich um den Standardwert handelt.

Informationen zur Verwendung des „copy“-Elementes mit geschachtelten Vorlagen finden Sie unter [Verwenden des „copy“-Elements](resource-group-linked-templates.md#using-copy).

## <a name="property-iteration"></a>Iteration von Eigenschaften

Wenn Sie mehrere Werte für eine Eigenschaft einer Ressource erstellen möchten, fügen Sie im properties-Element ein `copy`-Array hinzu. Dieses Array enthält Objekte, und jedes Objekt weist die folgenden Eigenschaften auf:

* „name“ – der Name der Eigenschaft, für die mehrere Werte erstellt werden sollen
* „count“ – die Anzahl der zu erstellenden Werte.
* „input“ – ein Objekt, das die Werte enthält, die der Eigenschaft zugewiesen werden sollen  

Im folgenden Beispiel wird veranschaulicht, wie `copy` auf die dataDisks-Eigenschaft auf einem virtuellen Computer angewendet wird:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "copy": [{
        "name": "dataDisks",
        "count": 3,
        "input": {
          "lun": "[copyIndex('dataDisks')]",
          "createOption": "Empty",
          "diskSizeGB": "1023"
        }
      }],
      ...
```

Beachten Sie Folgendes: Bei Verwendung von `copyIndex` in einer Eigenschaften-Iteration müssen Sie den Namen der Iteration angeben. Bei Verwendung mit einer Ressourcen-Iteration muss der Name nicht angegeben werden.

Ressourcen-Manager erweitert das `copy`-Array während der Bereitstellung. Der Name des Arrays wird zum Namen der Eigenschaft. Die Eingabewerte werden zu den Eigenschaften des Objekts. Die bereitgestellte Vorlage sieht wie folgt aus:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": "1023"
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": "1023"
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": "1023"
        }
      ],
      ...
```

Das Kopierelement ist ein Array, sodass Sie mehrere Eigenschaften für die Ressource angeben können. Fügen Sie für jede zu erstellende Eigenschaft ein Objekt hinzu.

```json
{
  "name": "string",
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "properties": {
    "copy": [
      {
        "name": "loadBalancingRules",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      },
      {
        "name": "probes",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      }
    ]
  }
}
```

Sie können die Ressourcen- und die Eigenschaften-Iteration zusammen verwenden. Verweisen Sie anhand des Namens auf die Eigenschaften-Iteration.

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "apiVersion": "2018-04-01",
  "copy":{
    "count": 2,
    "name": "vnetloop"
  },
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[parameters('addressPrefix')]"
      ]
    },
    "copy": [
      {
        "name": "subnets",
        "count": 2,
        "input": {
          "name": "[concat('subnet-', copyIndex('subnets'))]",
          "properties": {
            "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
          }
        }
      }
    ]
  }
}
```

## <a name="variable-iteration"></a>Variableniteration

Um mehrere Instanzen einer Variable zu erstellen, verwenden Sie die `copy`-Eigenschaft im Abschnitt „variables“. Erstellen Sie ein Array von Elementen, das aus dem Wert in der `input`-Eigenschaft erstellt wird. Sie können die `copy`-Eigenschaft innerhalb einer Variablen oder auf der obersten Ebene des Abschnitts „variables“ verwenden. Bei Verwendung von `copyIndex` in einer Variableniteration müssen Sie den Namen der Iteration angeben.

Ein einfaches Beispiel für das Erstellen eines Arrays von Zeichenfolgenwerten finden Sie in diesem Artikel zum [Kopieren der Arrayvorlage](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/copy-array/azuredeploy.json).

Das folgende Beispiel zeigt mehrere verschiedene Möglichkeiten zum Erstellen von Arrayvariablen mit dynamisch erstellten Elementen. Es veranschaulicht die Verwendung des copy-Elements in einer Variablen, um Arrays von Objekten und Zeichenfolgen zu erstellen. Es veranschaulicht außerdem, wie das copy-Element auf der obersten Ebene zum Erstellen von Arrays von Objekten, Zeichenfolgen und ganzen Zahlen verwendet wird.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "disk-array-on-object": {
      "copy": [
        {
          "name": "disks",
          "count": 5,
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        },
        {
          "name": "diskNames",
          "count": 5,
          "input": "[concat('myDataDisk', copyIndex('diskNames', 1))]"
        }
      ]
    },
    "copy": [
      {
        "name": "top-level-object-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('top-level-object-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('top-level-object-array')]"
        }
      },
      {
        "name": "top-level-string-array",
        "count": 5,
        "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
      },
      {
        "name": "top-level-integer-array",
        "count": 5,
        "input": "[copyIndex('top-level-integer-array')]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "exampleObject": {
      "value": "[variables('disk-array-on-object')]",
      "type": "object"
    },
    "exampleArrayOnObject": {
      "value": "[variables('disk-array-on-object').disks]",
      "type" : "array"
    },
    "exampleObjectArray": {
      "value": "[variables('top-level-object-array')]",
      "type" : "array"
    },
    "exampleStringArray": {
      "value": "[variables('top-level-string-array')]",
      "type" : "array"
    },
    "exampleIntegerArray": {
      "value": "[variables('top-level-integer-array')]",
      "type" : "array"
    }
  }
}
```

Der Typ der Variablen, die erstellt wird, hängt vom Eingabeobjekt ab. Die Variable mit dem Namen **top-level-object-array** im vorherigen Beispiel gibt z.B. Folgendes zurück:

```json
[
  {
    "name": "myDataDisk1",
    "diskSizeGB": "1",
    "diskIndex": 0
  },
  {
    "name": "myDataDisk2",
    "diskSizeGB": "1",
    "diskIndex": 1
  },
  {
    "name": "myDataDisk3",
    "diskSizeGB": "1",
    "diskIndex": 2
  },
  {
    "name": "myDataDisk4",
    "diskSizeGB": "1",
    "diskIndex": 3
  },
  {
    "name": "myDataDisk5",
    "diskSizeGB": "1",
    "diskIndex": 4
  }
]
```

Und die Variable mit dem Namen **top-level-string-array** gibt Folgendes zurück:

```json
[
  "myDataDisk1",
  "myDataDisk2",
  "myDataDisk3",
  "myDataDisk4",
  "myDataDisk5"
]
```

## <a name="depend-on-resources-in-a-loop"></a>Abhängigkeit von Ressourcen in einer Schleife

Sie geben an, dass eine Ressource nach einer anderen Ressource bereitgestellt wird, indem Sie das `dependsOn`-Element verwenden. Um eine Ressource bereitzustellen, die von der Sammlung von Ressourcen in einer Schleife abhängt, geben Sie den Namen der Kopierschleife im dependsOn-Element an. Das folgende Beispiel zeigt, wie drei Speicherkonten vor dem Bereitstellen des virtuellen Computers bereitgestellt werden. Die vollständige Definition des virtuellen Computers ist dabei nicht angegeben. Beachten Sie, dass „name“ für das copy-Element auf `storagecopy` und auch das dependsOn-Element für die virtuellen Computer auf `storagecopy` festgelegt ist.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
      }
    },
    {
      "apiVersion": "2015-06-15", 
      "type": "Microsoft.Compute/virtualMachines", 
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

<a id="looping-on-a-nested-resource" />

## <a name="iteration-for-a-child-resource"></a>Iteration für eine untergeordnete Ressource
Für eine untergeordnete Ressource kann keine Kopierschleife verwendet werden. Um mehrere Instanzen einer Ressource zu erstellen, die Sie in der Regel als innerhalb einer anderen Ressource geschachtelt definieren, müssen Sie diese Ressource stattdessen als Ressource oberster Ebene erstellen. Sie definieren die Beziehung zur übergeordneten Ressource mithilfe der Eigenschaften „type“ und „name“.

Angenommen, Sie definieren ein Dataset als untergeordnete Ressource innerhalb einer Data Factory.

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
  "name": "exampleDataFactory",
  ...
  "resources": [
    {
      "type": "datasets",
      "name": "exampleDataSet",
      "dependsOn": [
        "exampleDataFactory"
      ],
      ...
    }
  ]
```

Um mehrere Datasets zu erstellen, verschieben Sie sie außerhalb der Data Factory. Das Dataset muss sich auf der gleichen Ebene wie die Data Factory befinden, ist aber immer noch eine untergeordnete Ressource der Data Factory. Sie erhalten die Beziehung zwischen Dataset und Data Factory mithilfe der Eigenschaften „type“ und „name“ bei. Da „type“ nicht mehr von seiner Position in der Vorlage abgeleitet werden kann, müssen Sie den vollqualifizierten Typ im folgenden Format angeben: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Um eine Über-/Unterordnungsbeziehung mit einer Instanz der Data Factory herzustellen, geben Sie einen Namen für das Dataset an, das den Namen der übergeordneten Ressource enthält. Verwenden Sie das folgende Format: `{parent-resource-name}/{child-resource-name}`.  

Das folgende Beispiel zeigt die Implementierung:

```json
"resources": [
{
  "type": "Microsoft.DataFactory/datafactories",
  "name": "exampleDataFactory",
  ...
},
{
  "type": "Microsoft.DataFactory/datafactories/datasets",
  "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
  "dependsOn": [
    "exampleDataFactory"
  ],
  "copy": {
    "name": "datasetcopy",
    "count": "3"
  },
  ...
}]
```

## <a name="example-templates"></a>Beispielvorlagen

Die folgenden Beispiele zeigen allgemeine Szenarien für das Erstellen mehrerer Ressourcen oder Eigenschaften.

|Vorlage  |BESCHREIBUNG  |
|---------|---------|
|[Speicher kopieren](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Stellt mehrere Speicherkonten mit einer Indexnummer im Namen bereit. |
|[Speicher seriell kopieren](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Stellt mehrere Speicherkonten nacheinander bereit. Der Name enthält die Indexnummer. |
|[Speicher mit Array kopieren](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Stellt mehrere Speicherkonten bereit. Der Name enthält einen Wert aus einem Array. |
|[VM-Bereitstellung mit einer variablen Anzahl von Datenträgern](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Stellt mehrere Datenträger mit einem virtuellen Computer bereit. |
|[Variablen kopieren](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Veranschaulicht die verschiedenen Methoden zum Durchlaufen von Variablen |
|[Mehrere Sicherheitsregeln](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Stellt mehrere Sicherheitsregeln in einer Netzwerksicherheitsgruppe bereit. Die Sicherheitsregeln werden aus einem Parameter generiert. Informationen zum Parameter finden Sie im Artikel zur [Datei mit mehreren NSG-Parametern](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Nächste Schritte

* Ein Tutorial, das Sie durcharbeiten können, finden Sie unter [Tutorial: Erstellen mehrerer Ressourceninstanzen mit Resource Manager-Vorlagen](./resource-manager-tutorial-create-multiple-instances.md).

* Informationen zu den Abschnitten einer Vorlage finden Sie unter [Erstellen von Azure Resource Manager-Vorlagen](resource-group-authoring-templates.md).
* Informationen zum Bereitstellen Ihrer Vorlage finden Sie unter [Bereitstellen einer Anwendung mit einer Azure-Ressourcen-Manager-Vorlage](resource-group-template-deploy.md).

