---
title: Festlegen einer Bereitstellungsreihenfolge für Azure-Ressourcen | Microsoft-Dokumentation
description: Beschreibt, wie während der Bereitstellung festlegt wird, dass eine Ressource von einer anderen Ressource abhängt, um sicherzustellen, dass die Ressourcen in der richtigen Reihenfolge bereitgestellt werden.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: ''
ms.assetid: 34ebaf1e-480c-4b4d-9bf6-251bd3f8f2cf
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: tomfitz
ms.openlocfilehash: 91325b7884eae4c6f4c85c142b1e81cf2121c039
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62103794"
---
# <a name="define-the-order-for-deploying-resources-in-azure-resource-manager-templates"></a>Definieren der Reihenfolge für die Bereitstellung von Ressourcen in Azure Resource Manager-Vorlagen
Vor dem Bereitstellen einer bestimmten Ressource kann es erforderlich sein, dass bereits andere Ressourcen vorhanden sind. Zum Beispiel muss SQL Server vorhanden sein, bevor versucht wird, eine SQL-Datenbank bereitzustellen. Diese Beziehung definieren Sie, indem Sie eine Ressource als von einer anderen Ressource abhängig kennzeichnen. Sie definieren eine Abhängigkeit mit dem **dependsOn**-Element oder mit der **reference**-Funktion. 

Resource Manager wertet die Abhängigkeiten zwischen den Ressourcen aus und stellt sie in der Reihenfolge ihrer Abhängigkeiten bereit. Wenn Ressourcen nicht voneinander abhängig sind, stellt Resource Manager sie parallel bereit. Sie müssen nur Abhängigkeiten für Ressourcen definieren, die in der gleichen Vorlage bereitgestellt werden. 

Ein Tutorial finden Sie unter [Tutorial: Erstellen von Azure Resource Manager-Vorlagen mit abhängigen Ressourcen](./resource-manager-tutorial-create-templates-with-dependent-resources.md).

## <a name="dependson"></a>dependsOn
Innerhalb Ihrer Vorlage bietet das „dependsOn“-Element die Möglichkeit, eine Ressource als von einer oder mehreren Ressourcen abhängig zu definieren. Der Wert kann eine durch Trennzeichen getrennte Liste von Ressourcennamen sein. 

Das folgende Beispiel zeigt eine VM-Skalierungsgruppe, die abhängig von einem Load Balancer, einem virtuellen Netzwerk und einer Schleife ist, die mehrere Speicherkonten erstellt. Diese anderen Ressourcen werden im folgenden Beispiel nicht gezeigt, müssen jedoch anderswo in der Vorlage vorhanden sein.

```json
{
  "type": "Microsoft.Compute/virtualMachineScaleSets",
  "name": "[variables('namingInfix')]",
  "location": "[variables('location')]",
  "apiVersion": "2016-03-30",
  "tags": {
    "displayName": "VMScaleSet"
  },
  "dependsOn": [
    "[variables('loadBalancerName')]",
    "[variables('virtualNetworkName')]",
    "storageLoop",
  ],
  ...
}
```

Im vorherigen Beispiel ist eine Abhängigkeit für die Ressourcen enthalten, die durch eine Kopierschleife mit dem Namen **storageLoop** erstellt werden. Ein Beispiel finden Sie unter [Erstellen mehrerer Instanzen von Ressourcen im Azure-Ressourcen-Manager](resource-group-create-multiple.md).

Wenn Sie Abhängigkeiten definieren, können Sie Ressourcenanbieter-Namespace und Ressourcentyp einbeziehen, um Mehrdeutigkeiten zu vermeiden. Um z.B. einen Lastenausgleich und ein virtuelles Netzwerk eindeutig darzustellen, die möglicherweise die gleichen Namen wie andere Ressourcen haben, verwenden Sie folgendes Format:

```json
"dependsOn": [
  "[resourceId('Microsoft.Network/loadBalancers', variables('loadBalancerName'))]",
  "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]"
]
``` 

Sie können Beziehungen zwischen Ihren Ressourcen mithilfe von „dependsOn“ zuordnen, dabei sollte Ihnen jedoch bewusst sein, welchen Zweck diese Zuordnung erfüllt. Um beispielsweise zu dokumentieren, wie Ressourcen miteinander verbunden sind, ist „dependsOn“ nicht der richtige Ansatz. Nach der Bereitstellung können Sie nicht mehr abfragen, welche Ressourcen im dependsOn-Element definiert waren. Durch Verwendung von „dependsOn“ beeinträchtigen Sie möglicherweise die Bereitstellungszeit, da Resource Manager zwei Ressourcen, die eine Abhängigkeit aufweisen, nicht parallel bereitstellt. 

## <a name="child-resources"></a>Untergeordnete Ressourcen
Mit der resources-Eigenschaft können Sie untergeordnete Ressourcen angeben, die mit der definierten Ressource verknüpft sind. Untergeordnete Ressourcen können nur mit fünf Ebenen definiert werden. Es ist wichtig zu beachten, dass keine implizite Bereitstellungsabhängigkeit zwischen einer untergeordneten Ressource und der übergeordneten Ressource erstellt wird. Wenn die untergeordnete Ressource nach der übergeordneten Ressource bereitgestellt werden muss, müssen Sie diese Abhängigkeit explizit mit der dependsOn-Eigenschaft angeben. 

Jede übergeordnete Ressource akzeptiert nur bestimmte Ressourcentypen als untergeordnete Ressourcen. Die akzeptierten Ressourcentypen werden im [Vorlagenschema](https://github.com/Azure/azure-resource-manager-schemas) der übergeordneten Ressource angegeben. Der Name des untergeordneten Ressourcentyps enthält den Namen des übergeordneten Ressourcentyps. So sind z.B. **Microsoft.Web/sites/config** und **Microsoft.Web/sites/extensions** untergeordnete Ressourcen von **Microsoft.Web/sites**.

Das folgende Beispiel zeigt einen SQL Server und eine SQL-Datenbank. Beachten Sie, dass eine explizite Abhängigkeit zwischen der SQL-Datenbank und SQL Server definiert wird, obwohl die Datenbank ein untergeordnetes Element des Servers ist.

```json
"resources": [
  {
    "name": "[variables('sqlserverName')]",
    "type": "Microsoft.Sql/servers",
    "location": "[resourceGroup().location]",
    "tags": {
      "displayName": "SqlServer"
    },
    "apiVersion": "2014-04-01-preview",
    "properties": {
      "administratorLogin": "[parameters('administratorLogin')]",
      "administratorLoginPassword": "[parameters('administratorLoginPassword')]"
    },
    "resources": [
      {
        "name": "[parameters('databaseName')]",
        "type": "databases",
        "location": "[resourceGroup().location]",
        "tags": {
          "displayName": "Database"
        },
        "apiVersion": "2014-04-01-preview",
        "dependsOn": [
          "[variables('sqlserverName')]"
        ],
        "properties": {
          "edition": "[parameters('edition')]",
          "collation": "[parameters('collation')]",
          "maxSizeBytes": "[parameters('maxSizeBytes')]",
          "requestedServiceObjectiveName": "[parameters('requestedServiceObjectiveName')]"
        }
      }
    ]
  }
]
```

## <a name="reference-and-list-functions"></a>Funktionen „reference“ und „list“
Mit der [reference](resource-group-template-functions-resource.md#reference) -Funktion kann ein Ausdruck seinen Wert von anderen Paaren aus JSON-Name und -Wert oder Laufzeitressourcen ableiten. Die [list*](resource-group-template-functions-resource.md#list)-Funktionen geben Werte für eine Ressource aus einem list-Vorgang zurück.  reference- und list-Ausdrücke deklarieren implizit, dass eine Ressource von der anderen abhängig ist, wenn die referenzierte Ressource in der gleichen Vorlage bereitgestellt wird und anhand ihres Namens (nicht ihrer Ressourcen-ID) auf sie verwiesen wird. Wenn Sie die Ressourcen-ID an die reference- oder list-Funktionen übergeben, wird kein impliziter Verweis erstellt.

Das allgemeine Format der reference-Funktion sieht folgendermaßen aus:

```json
reference('resourceName').propertyPath
```

Das allgemeine Format der listKeys-Funktion sieht folgendermaßen aus:

```json
listKeys('resourceName', 'yyyy-mm-dd')
```

Im folgenden Beispiel hängt ein CDN-Endpunkt explizit vom CDN-Profil und implizit von einer Web-App ab.

```json
{
    "name": "[variables('endpointName')]",
    "type": "endpoints",
    "location": "[resourceGroup().location]",
    "apiVersion": "2016-04-02",
    "dependsOn": [
            "[variables('profileName')]"
    ],
    "properties": {
        "originHostHeader": "[reference(variables('webAppName')).hostNames[0]]",
        ...
    }
```

Sie können dieses Element oder das dependsOn-Element verwenden, um Abhängigkeiten anzugeben, aber Sie müssen nicht beide für dieselbe abhängige Ressource verwenden. Verwenden Sie nach Möglichkeit einen impliziten Verweis, um das Hinzufügen einer unnötigen Abhängigkeit zu vermeiden.

Weitere Informationen finden Sie unter [reference-Funktion](resource-group-template-functions-resource.md#reference).

## <a name="circular-dependencies"></a>Ringabhängigkeiten

Resource Manager kennzeichnet Ringabhängigkeiten während der Überprüfung der Vorlage. Falls Sie einen Fehler erhalten, der besagt, dass eine Ringabhängigkeit vorhanden ist, überprüfen Sie Ihre Vorlage, um festzustellen, ob bestimmte Abhängigkeiten nicht erforderlich sind und entfernt werden können. Wenn das Entfernen der Abhängigkeiten nicht funktioniert, können Sie Ringabhängigkeiten vermeiden, indem Sie einige Bereitstellungsvorgänge in untergeordnete Ressourcen verschieben, die nach den Ressourcen mit der Ringabhängigkeit bereitgestellt werden. Nehmen wir beispielsweise an, Sie stellen zwei virtuelle Computer bereit, müssen aber Eigenschaften festlegen, die auf den jeweils anderen verweisen. Sie können diese in der folgenden Reihenfolge bereitstellen:

1. VM1
2. VM2
3. Die Erweiterung auf VM1 hängt von VM1 und VM2 ab. Die Erweiterung legt Werte auf VM1 fest, die sie von VM2 abruft.
4. Die Erweiterung auf VM2 hängt von VM1 und VM2 ab. Die Erweiterung legt Werte auf VM2 fest, die sie von VM1 abruft.

Informationen über das Bewerten der Bereitstellungsreihenfolge und das Beheben von Abhängigkeitsfehlern finden Sie unter [Beheben gängiger Azure-Bereitstellungsfehler mit Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="next-steps"></a>Nächste Schritte

* Ein Tutorial, das Sie durcharbeiten können, finden Sie unter [Tutorial: Erstellen von Azure Resource Manager-Vorlagen mit abhängigen Ressourcen](./resource-manager-tutorial-create-templates-with-dependent-resources.md).
* Empfehlungen zum Festlegen von Abhängigkeiten finden Sie unter [Bewährte Methoden für Azure Resource Manager-Vorlagen](template-best-practices.md).
* Informationen zur Behebung von Abhängigkeiten während der Bereitstellung finden Sie unter [Beheben gängiger Azure-Bereitstellungsfehler mit Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Weitere Informationen zum Erstellen von Azure-Ressourcen-Manager-Vorlagen finden Sie unter [Erstellen von Vorlagen](resource-group-authoring-templates.md). 
* Eine Liste der verfügbaren Funktionen in einer Vorlage finden Sie unter [Funktionen von Azure Resource Manager-Vorlagen](resource-group-template-functions.md).

