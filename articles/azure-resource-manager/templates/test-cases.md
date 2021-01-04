---
title: Testfälle für das Testtoolkit
description: Beschreibt die Tests, die vom Resource Manager-Vorlagen-Testtoolkit ausgeführt werden.
ms.topic: conceptual
ms.date: 12/03/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: ff9ad659e15a88725e4c3905ab6c623fda7610fd
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96600903"
---
# <a name="default-test-cases-for-arm-template-test-toolkit"></a>Standardtestfälle für das Resource Manager-Vorlagen-Testtoolkit

In diesem Artikel werden die Standardtests beschrieben, die mit dem [Vorlagen-Testtoolkit](test-toolkit.md) ausgeführt werden. Er enthält Beispiele, bei denen der Test erfolgreich ist oder fehlschlägt, sowie den Namen der einzelnen Tests.

## <a name="use-correct-schema"></a>Verwendung des richtigen Schemas

Testname: **DeploymentTemplate Schema Is Correct**

Sie müssen in Ihrer Vorlage einen gültigen Schemawert angeben.

Im folgenden Beispiel ist der Test **erfolgreich**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": []
}
```

Die schema-Eigenschaft in der Vorlage muss auf eines der folgenden Schemas festgelegt sein:

* `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#`
* `https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json`

## <a name="parameters-must-exist"></a>Vorhandensein des parameters-Elements

Testname: **Parameters Property Must Exist**

Die Vorlage muss ein parameters-Element enthalten. Parameter sind erforderlich, um die Wiederverwendung Ihrer Vorlagen in verschiedenen Umgebungen zu ermöglichen. Fügen Sie der Vorlage Parameter für Werte hinzu, die sich bei der Bereitstellung in unterschiedlichen Umgebungen ändern.

Im folgenden Beispiel ist der Test **erfolgreich**:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "vmName": {
          "type": "string",
          "defaultValue": "linux-vm",
          "metadata": {
            "description": "Name for the Virtual Machine."
          }
      }
  },
  ...
```

## <a name="declared-parameters-must-be-used"></a>Verwendung deklarierter Parameter

Testname: **Parameters Must Be Referenced**

Löschen Sie alle Parameter, die definiert sind, aber nicht verwendet werden, um Ihre Vorlage übersichtlicher zu machen. Dieser Test ermittelt alle Parameter, die nicht in der Vorlage verwendet werden. Das Löschen nicht verwendeter Parameter vereinfacht zudem die Bereitstellung Ihrer Vorlage, da Sie keine unnötigen Werte angeben müssen.

## <a name="secure-parameters-cant-have-hardcoded-default"></a>Keine hartcodierten Standardwerte für sichere Parameter

Testname: **Secure String Parameters Cannot Have Default**

Stellen Sie keinen hartcodierten Standardwert für einen sicheren Parameter in Ihrer Vorlage bereit. Eine leere Zeichenfolge ist als Standardwert ausreichend.

Für Parameter, die sensible Werte enthalten (z. B. Kennwörter), verwenden Sie die Typen **SecureString** oder **SecureObject**. Der Wert von Parametern mit einem sicheren Typ wird nicht protokolliert oder im Bereitstellungsverlauf gespeichert. Dadurch wird verhindert, dass der sensible Wert von einem böswilligen Benutzer entdeckt wird.

Wenn Sie jedoch einen Standardwert angeben, ist dieser Wert für jeden Benutzer sichtbar, der Zugriff auf die Vorlage oder den Bereitstellungsverlauf hat.

Im folgenden Beispiel ist der Test **nicht erfolgreich**:

```json
"parameters": {
    "adminPassword": {
        "defaultValue": "HardcodedPassword",
        "type": "SecureString"
    }
}
```

Im nächsten Beispiel ist der Test **erfolgreich**:

```json
"parameters": {
    "adminPassword": {
        "type": "SecureString"
    }
}
```

## <a name="environment-urls-cant-be-hardcoded"></a>Umgebungs-URLs können nicht hartcodiert werden.

Testname: **DeploymentTemplate Must Not Contain Hardcoded Uri**

Die Umgebungs-URLs in der Vorlage dürfen nicht hartcodiert werden. Verwenden Sie stattdessen die [Umgebungsfunktion](template-functions-deployment.md#environment), um diese URLs während der Bereitstellung dynamisch abzurufen. Eine Liste der blockierten URL-Hosts finden Sie im Artikel zum [Testfall](https://github.com/Azure/arm-ttk/blob/master/arm-ttk/testcases/deploymentTemplate/DeploymentTemplate-Must-Not-Contain-Hardcoded-Uri.test.ps1).

Im folgenden Beispiel ist der Test **nicht erfolgreich**, weil die URL hartcodiert ist.

```json
"variables":{
    "AzureURL":"https://management.azure.com"
}
```

Der Test ist auch bei Verwendung von [concat](template-functions-string.md#concat) oder [uri](template-functions-string.md#uri) **nicht erfolgreich**.

```json
"variables":{
    "AzureSchemaURL1": "[concat('https://','gallery.azure.com')]",
    "AzureSchemaURL2": "[uri('gallery.azure.com','test')]"
}
```

Im folgenden Beispiel ist der Test **erfolgreich**.

```json
"variables": {
    "AzureSchemaURL": "[environment().gallery]"
},
```

## <a name="location-uses-parameter"></a>Verwendung eines Parameters für den Speicherort

Testname: **Location Should Not Be Hardcoded**

Ihre Vorlagen sollten einen Parameter mit dem Namen „location“ aufweisen. Verwenden Sie diesen Parameter, um den Standort der Ressourcen in der Vorlage festzulegen. In der Hauptvorlage (mit dem Namen „azuredeploy.json“ oder „mainTemplate.json“) kann dieser Parameter standardmäßig auf den Standort der Ressourcengruppe festgelegt werden. In verknüpften oder geschachtelten Vorlagen sollte der location-Parameter keinen Standardstandort aufweisen.

Benutzern Ihrer Vorlage stehen möglicherweise begrenzte Regionen zur Verfügung. Wenn Sie den Ressourcenstandort hartcodieren, können Benutzer möglicherweise keine Ressource in dieser Region erstellen. Benutzer können auch dann blockiert werden, wenn Sie den Ressourcenstandort auf `"[resourceGroup().location]"` festlegen. Es besteht die Möglichkeit, dass die Ressourcengruppe in einer Region erstellt wurde, auf die andere Benutzer nicht zugreifen können. Diese Benutzer können die Vorlage nicht verwenden.

Wenn Sie einen location-Parameter angeben, der als Standardwert den Standort der Ressourcengruppe aufweist, können die Benutzer den Standardwert verwenden oder einen anderen Standort angeben.

Im folgenden Beispiel ist der Test **nicht erfolgreich**, weil der Speicherort der Ressource auf `resourceGroup().location` festgelegt ist.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[resourceGroup().location]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ]
}
```

Im nächsten Beispiel wird ein location-Parameter verwendet, der Test ist jedoch **nicht erfolgreich**, da der Standardwert des location-Parameters ein hartcodierter Speicherort ist.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "westus"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

Erstellen Sie stattdessen einen Parameter, dessen Standardwert der Ressourcengruppenstandort ist, Benutzern jedoch die Angabe eines anderen Werts ermöglicht. Im folgenden Beispiel wird dieser Test **bestanden**, wenn die Vorlage als Hauptvorlage verwendet wird.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the resources."
            }
        }
     },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

Wenn das vorherige Beispiel jedoch als verknüpfte Vorlage verwendet wird, gilt der Test als **nicht bestanden**. Wenn Sie die Verwendung als verknüpfte Vorlage planen, entfernen Sie den Standardwert.

## <a name="resources-should-have-location"></a>Verwendung eines Speicherorts für Ressourcen

Testname: **Resources Should Have Location**

Der Speicherort für eine Ressource sollte auf einen [Vorlagenausdruck](template-expressions.md) oder `global` festgelegt werden. Der Vorlagenausdruck verwendet in der Regel den im vorherigen Test beschriebenen location-Parameter.

Im folgenden Beispiel ist der Test **nicht erfolgreich**, weil der Speicherort nicht auf einen Ausdruck oder `global` festgelegt ist.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "westus",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

Im folgenden Beispiel ist der Test **erfolgreich**.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Maps/accounts",
            "apiVersion": "2020-02-01-preview",
            "name": "demoMap",
            "location": "global",
            "sku": {
                "name": "S0"
            }
        }
    ],
    "outputs": {
    }
}
```

Im nächsten Beispiel ist der Test ebenfalls **erfolgreich**.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for the resources."
            }
        }
     },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "storageaccount1",
            "location": "[parameters('location')]",
            "kind": "StorageV2",
            "sku": {
                "name": "Premium_LRS",
                "tier": "Premium"
            }
        }
    ],
    "outputs": {}
}
```

## <a name="vm-size-uses-parameter"></a>Verwendung eines Parameters für die VM-Größe

Testname: **VM Size Should Be A Parameter**

Die Größe der VM sollte nicht hartcodiert werden. Stellen Sie einen Parameter bereit, damit Benutzer Ihrer Vorlage die Größe der bereitgestellten VM ändern können.

Im folgenden Beispiel ist der Test **nicht erfolgreich**.

```json
"hardwareProfile": {
    "vmSize": "Standard_D2_v3"
}
```

Stellen Sie stattdessen einen Parameter bereit.

```json
"vmSize": {
    "type": "string",
    "defaultValue": "Standard_A2_v2",
    "metadata": {
        "description": "Size for the Virtual Machine."
    }
},
```

Legen Sie dann die VM-Größe auf diesen Parameter fest.

```json
"hardwareProfile": {
    "vmSize": "[parameters('vmSize')]"
},
```

## <a name="min-and-max-values-are-numbers"></a>Angabe von Mindest- und Maximalwerten als Zahlen

Testname: **Min And Max Value Are Numbers**

Geben Sie Mindest- und Maximalwerte als Zahlen an, wenn Sie diese für einen Parameter festlegen.

Im folgenden Beispiel ist der Test **nicht erfolgreich**:

```json
"exampleParameter": {
    "type": "int",
    "minValue": "0",
    "maxValue": "10"
},
```

Geben Sie die Werte stattdessen als Zahlen an. Im folgenden Beispiel ist der Test **erfolgreich**:

```json
"exampleParameter": {
    "type": "int",
    "minValue": 0,
    "maxValue": 10
},
```

Diese Warnung wird auch ausgegeben, wenn Sie nur einen der Werte angeben, d. h. einen Mindestwert ohne zugehörigen Maximalwert oder umgekehrt.

## <a name="artifacts-parameter-defined-correctly"></a>Korrekte Definition von Artefaktparametern

Testname: **artifacts parameter**

Wenn Sie Parameter für `_artifactsLocation` und `_artifactsLocationSasToken` hinzufügen, müssen Sie die richtigen Standardwerte und Typen verwenden. Die folgenden Bedingungen müssen erfüllt sein, damit dieser Test erfolgreich ist:

* Wenn Sie einen Parameter bereitstellen, müssen Sie auch den anderen Parameter angeben.
* `_artifactsLocation` muss eine Zeichenfolge (**string**) sein.
* `_artifactsLocation` muss in der Hauptvorlage über einen Standardwert verfügen.
* `_artifactsLocation` darf in der geschachtelten Vorlage keinen Standardwert haben. 
* `_artifactsLocation` muss entweder `"[deployment().properties.templateLink.uri]"` oder die Basis-URL des Repositorys als Standardwert aufweisen.
* `_artifactsLocationSasToken` muss eine sichere Zeichenfolge (**secureString**) sein.
* `_artifactsLocationSasToken` darf nur eine leere Zeichenfolge als Standardwert aufweisen.
* `_artifactsLocationSasToken` darf in einer geschachtelten Vorlage keinen Standardwert haben. 

## <a name="declared-variables-must-be-used"></a>Verwendung deklarierter Variablen

Testname: **Variables Must Be Referenced**

Löschen Sie alle Variablen, die definiert sind, aber nicht verwendet werden, um Ihre Vorlage übersichtlicher zu machen. Dieser Test ermittelt alle Variablen, die nicht in der Vorlage verwendet werden.

## <a name="dynamic-variable-should-not-use-concat"></a>Keine Verwendung der concat-Funktion für dynamische Variablen

Testname: **Dynamic Variable References Should Not Use Concat**

Manchmal müssen Sie eine Variable dynamisch auf Grundlage des Werts einer anderen Variablen oder eines anderen Parameters erstellen. Verwenden Sie beim Festlegen des Werts nicht die [concat](template-functions-string.md#concat)-Funktion. Verwenden Sie stattdessen ein Objekt, das die verfügbaren Optionen enthält, und rufen Sie während der Bereitstellung dynamisch eine der Eigenschaften aus dem Objekt ab.

Im folgenden Beispiel ist der Test **erfolgreich**. Die Variable **currentImage** wird während der Bereitstellung dynamisch festgelegt.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "osType": {
          "type": "string",
          "allowedValues": [
              "Windows",
              "Linux"
          ]
      }
  },
  "variables": {
    "imageOS": {
        "Windows": {
            "image": "Windows Image"
        },
        "Linux": {
            "image": "Linux Image"
        }
    },
    "currentImage": "[variables('imageOS')[parameters('osType')].image]"
  },
  "resources": [],
  "outputs": {
      "result": {
          "type": "string",
          "value": "[variables('currentImage')]"
      }
  }
}
```

## <a name="use-recent-api-version"></a>Verwendung einer aktuellen API-Version

Testname: **apiVersions Should Be Recent**

Für jede Ressource muss eine aktuelle API-Version verwendet werden. Der Test vergleicht die verwendete Version mit den Versionen, die für den jeweiligen Ressourcentyp verfügbar sind.

## <a name="use-hardcoded-api-version"></a>Verwendung einer hartcodierten API-Version

Testname: **Providers apiVersions Is Not Permitted**

Die API-Version für einen Ressourcentyp bestimmt, welche Eigenschaften verfügbar sind. Stellen Sie in Ihrer Vorlage eine hartcodierte API-Version bereit. Rufen Sie keine API-Version ab, die während der Bereitstellung ermittelt wird. Andernfalls wissen Sie nicht, welche Eigenschaften verfügbar sind.

Im folgenden Beispiel ist der Test **nicht erfolgreich**.

```json
"resources": [
    {
        "type": "Microsoft.Compute/virtualMachines",
        "apiVersion": "[providers('Microsoft.Compute', 'virtualMachines').apiVersions[0]]",
        ...
    }
]
```

Im folgenden Beispiel ist der Test **erfolgreich**.

```json
"resources": [
    {
       "type": "Microsoft.Compute/virtualMachines",
       "apiVersion": "2019-12-01",
       ...
    }
]
```

## <a name="properties-cant-be-empty"></a>Keine leeren Eigenschaften

Testname: **Template Should Not Contain Blanks**

Eigenschaften dürfen nicht auf einen leeren Wert hartcodiert sein. Leere Werte sind NULL sowie leere Zeichenfolgen, Objekte oder Arrays. Wenn Sie eine Eigenschaft auf einen leeren Wert festgelegt haben, entfernen Sie diese Eigenschaft aus der Vorlage. Sie können eine Eigenschaft jedoch während der Bereitstellung auf einen leeren Wert festlegen, z. B. über einen Parameter.

## <a name="use-resource-id-functions"></a>Verwendung von Ressourcen-ID-Funktionen

Testname: **IDs Should Be Derived From ResourceIDs**

Verwenden Sie eine der Ressourcen-ID-Funktionen, wenn Sie eine Ressourcen-ID angeben. Folgende Funktionen sind zulässig:

* [Ressourcen-ID](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)

Verwenden Sie nicht die concat-Funktion, um eine Ressourcen-ID zu erstellen. Im folgenden Beispiel ist der Test **nicht erfolgreich**.

```json
"networkSecurityGroup": {
    "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('networkSecurityGroupName'))]"
}
```

Im nächsten Beispiel ist der Test **erfolgreich**.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

## <a name="resourceid-function-has-correct-parameters"></a>Korrekte Parameter für die resourceId-Funktion

Testname: **ResourceIds should not contain**

Verwenden Sie keine unnötigen Funktionen für optionale Parameter, wenn Sie Ressourcen-IDs generieren. Die [resourceId](template-functions-resource.md#resourceid)-Funktion verwendet standardmäßig das aktuelle Abonnement und die aktuelle Ressourcengruppe. Sie müssen diese Werte nicht angeben.  

Im folgenden Beispiel ist der Test **nicht erfolgreich**, weil Sie die aktuelle Abonnement-ID und den aktuellen Ressourcengruppennamen nicht angeben müssen.

```json
"networkSecurityGroup": {
    "id": "[resourceId(subscription().subscriptionId, resourceGroup().name, 'Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

Im nächsten Beispiel ist der Test **erfolgreich**.

```json
"networkSecurityGroup": {
    "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
}
```

Dieser Test gilt für Folgendes:

* [Ressourcen-ID](template-functions-resource.md#resourceid)
* [subscriptionResourceId](template-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](template-functions-resource.md#tenantresourceid)
* [extensionResourceId](template-functions-resource.md#extensionresourceid)
* [Referenz](template-functions-resource.md#reference)
* [list*](template-functions-resource.md#list)

Bei `reference` und `list*` ist der Test **nicht erfolgreich**, wenn Sie `concat` zum Erstellen der Ressourcen-ID verwenden.

## <a name="dependson-best-practices"></a>dependsOn best practices

Testname: **DependsOn Best Practices**

Verwenden Sie beim Festlegen der Bereitstellungsabhängigkeiten nicht die [if](template-functions-logical.md#if)-Funktion, um eine Bedingung zu testen. Wenn eine Ressource von einer Ressource abhängig ist, die [bedingt bereitgestellt](conditional-resource-deployment.md) wird, legen Sie die Abhängigkeit wie bei jeder beliebigen Ressource fest. Wenn eine bedingte Ressource nicht bereitgestellt wurde, entfernt Azure Resource Manager sie automatisch aus den erforderlichen Abhängigkeiten.

Im folgenden Beispiel ist der Test **nicht erfolgreich**.

```json
"dependsOn": [
    "[if(equals(parameters('newOrExisting'),'new'), variables('storageAccountName'), '')]"
]
```

Im nächsten Beispiel ist der Test **erfolgreich**.

```json
"dependsOn": [
    "[variables('storageAccountName')]"
]
```

## <a name="nested-or-linked-deployments-cant-use-debug"></a>Keine Verwendung von „debug“ für geschachtelte oder verknüpfte Bereitstellungen

Testname: **Deployment Resources Must Not Be Debug**

Wenn Sie eine [geschachtelte oder verknüpfte Vorlage](linked-templates.md) mit dem Ressourcentyp **Microsoft.Resources/deployments** definieren, können Sie das Debuggen für diese Vorlage aktivieren. Das Debuggen kann zum Testen der Vorlage verwendet werden, sollte aber deaktiviert werden, wenn die Vorlage für die Verwendung in der Produktionsumgebung bereit ist.

## <a name="admin-user-names-cant-be-literal-value"></a>Keine Literalwerte für Administratorbenutzernamen

Testname: **adminUsername Should Not Be A Literal**

Verwenden Sie beim Festlegen von Administratorbenutzernamen keine Literalwerte.

Im folgenden Beispiel ist der Test **nicht erfolgreich**:

```json
"osProfile":  {
    "adminUserName":  "myAdmin"
},
```

Verwenden Sie stattdessen einen Parameter. Im folgenden Beispiel ist der Test **erfolgreich**:

```json
"osProfile": {
    "adminUsername": "[parameters('adminUsername')]"
}
```

## <a name="use-latest-vm-image"></a>Verwendung des aktuellen VM-Images

Testname: **VM Images Should Use Latest Version**

Wenn Ihre Vorlage eine VM mit einem Image enthält, müssen Sie sicherstellen, dass es sich um die aktuelle Imageversion handelt.

## <a name="use-stable-vm-images"></a>Verwendung stabiler VM-Images

Testname: **Virtual Machines Should Not Be Preview**

Für VMs sollten keine Vorschauimages verwendet werden.

Im folgenden Beispiel ist der Test **nicht erfolgreich**.

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest-preview"
}
```

Im folgenden Beispiel ist der Test **erfolgreich**.

```json
"imageReference": {
    "publisher": "Canonical",
    "offer": "UbuntuServer",
    "sku": "16.04-LTS",
    "version": "latest"
},
```

## <a name="dont-use-managedidentity-extension"></a>Keine Verwendung der ManagedIdentity-Erweiterung

Testname: **ManagedIdentityExtension must not be used**

Wenden Sie die ManagedIdentity-Erweiterung nicht auf eine VM an. Weitere Informationen finden Sie unter [Umsteigen von der VM-Erweiterung für verwaltete Identitäten auf Azure Instance Metadata Service](../../active-directory/managed-identities-azure-resources/howto-migrate-vm-extension.md).

## <a name="outputs-cant-include-secrets"></a>Keine Geheimnisse in Ausgaben

Testname: **Outputs Must Not Contain Secrets**

Fügen Sie dem Abschnitt „outputs“ keine Werte hinzu, durch die möglicherweise Geheimnisse offengelegt werden. Die Ausgabe einer Vorlage wird im Bereitstellungsverlauf gespeichert. Die Informationen können daher von einem böswilligen Benutzer gefunden werden.

Im folgenden Beispiel ist der Test **nicht erfolgreich**, weil ein Ausgabewert einen sicheren Parameter enthält.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "secureParam": {
            "type": "securestring"
        }
    },
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "badResult": {
            "type": "string",
            "value": "[concat('this is the value ', parameters('secureParam'))]"
        }
    }
}
```

Im folgenden Beispiel ist der Test **nicht erfolgreich**, weil eine [list*](template-functions-resource.md#list)-Funktion in den Ausgaben verwendet wird.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string"
        }
    },
    "functions": [],
    "variables": {},
    "resources": [],
    "outputs": {
        "badResult": {
            "type": "object",
            "value": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageName')), '2019-06-01')]"
        }
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Ausführen des Testtoolkits finden Sie unter [Verwenden des Resource Manager-Vorlagen-Testtoolkits](test-toolkit.md).