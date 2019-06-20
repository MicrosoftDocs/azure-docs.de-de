---
title: Sichere Methoden zur regionsübergreifenden Bereitstellung – Azure-Bereitstellungs-Manager
description: Beschreibt die zahlreiche Regionen umfassende Bereitstellung eines Diensts mit dem Azure-Bereitstellungs-Manager. Zeigt sichere Bereitstellungsmethoden, mit denen Sie die Stabilität Ihrer Bereitstellung vor dem Rollout in alle Regionen überprüfen können.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: 52b132b45bd90d7d21bb072e9a94d8588d5cf301
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431173"
---
# <a name="enable-safe-deployment-practices-with-azure-deployment-manager-public-preview"></a>Sichere Bereitstellungsmethoden mit dem Azure-Bereitstellungs-Manager (öffentliche Vorschau)

Um Ihren Dienst in vielen Regionen bereitzustellen und sicherzustellen, dass er in jeder Region wie erwartet ausgeführt wird, koordinieren Sie mit dem Bereitstellungs-Manager von Azure einen gestaffelten Rollout des Diensts. Gehen Sie wie bei jeder anderen Azure-Bereitstellung vor. Definieren Sie die Ressourcen für Ihren Dienst in [Resource Manager-Vorlagen](resource-group-authoring-templates.md). Wenn Sie die Vorlagen erstellt haben, beschreiben Sie mit dem Bereitstellungs-Manager die Topologie Ihres Diensts und den konkreten Rollout.

Der Bereitstellungs-Manager ist eine Funktion von Resource Manager. Er erweitert die verfügbaren Funktionen beim Bereitstellen. Verwenden Sie den Bereitstellungs-Manager für komplexe Dienste, die Sie in mehreren Regionen bereitstellen möchten. Mit der phasenweisen Einführung des Diensts können Sie mögliche Probleme finden, bevor er in allen Regionen bereitgestellt ist. Wenn Sie auf die zusätzlichen Vorsichtsmaßnahmen eines gestaffelten Rollouts verzichten möchten, verwenden Sie die standardmäßigen [Bereitstellungsoptionen](resource-group-template-deploy-portal.md) für Resource Manager. Der Bereitstellungs-Manager lässt sich nahtlos in alle vorhandenen Tools von Drittanbietern integrieren, die Resource Manager-Bereitstellungen unterstützen, z.B. CI- und CD-Angebote (Continuous Integration und Continuous Delivery). 

Der Azure-Bereitstellungs-Manager befindet sich in der Vorschau. Helfen Sie uns dabei, das Feature zu verbessern, indem Sie uns [Feedback senden](https://aka.ms/admfeedback).

Um den Bereitstellungs-Manager zu verwenden, müssen Sie vier Dateien erstellen:

* Topologievorlage
* Rolloutvorlage
* Parameterdatei für die Topologie
* Parameterdatei für den Rollout

Die Topologievorlage wird vor der Rolloutvorlage bereitgestellt.

Die REST-API-Referenz für den Azure-Bereitstellungs-Manager finden Sie [hier](https://docs.microsoft.com/rest/api/deploymentmanager/).

## <a name="identity-and-access"></a>Identität und Zugriff

Im Falle des Bereitstellungs-Managers führt eine [benutzerseitig zugewiesene verwaltete Identität](../active-directory/managed-identities-azure-resources/overview.md) die Bereitstellungsaktionen durch. Sie erstellen diese Identität vor Beginn der Bereitstellung. Sie muss Zugriff auf das Abonnement haben, für das Sie den Dienst bereitstellen, und über die nötigen Berechtigungen zum Abschließen der Bereitstellung verfügen. Weitere Informationen zu den Aktionen, die durch Rollen gewährt werden, finden Sie unter [Integrierte Rollen für Azure-Ressourcen](../role-based-access-control/built-in-roles.md).

Die Identität muss sich am gleichen Speicherort wie der Rollout befinden.

## <a name="topology-template"></a>Topologievorlage

Die Topologievorlage beschreibt, welche Azure-Ressourcen Ihr Dienst umfasst und wo sie bereitgestellt werden sollen. Hier sehen Sie eine beispielhafte Darstellung einer Diensttopologie:

![Hierarchie von der Diensttopologie über die Dienste bis hin zu Diensteinheiten](./media/deployment-manager-overview/service-topology.png)

Die Topologievorlage enthält die folgenden Ressourcen:

* Artefaktquelle: Hier sind Ihre Resource Manager-Vorlage und -Parameter gespeichert.
* Diensttopologie: Sie verweist auf die Artefaktquelle.
  * Dienste: Geben den Standort und die Azure-Abonnement-ID an.
    * Diensteinheiten. Legen die Ressourcengruppe, den Bereitstellungsmodus und den Pfad für die Vorlage und Parameterdatei fest.

Um die Vorgänge auf jeder Ebene zu verstehen, ist es hilfreich zu sehen, welche Werte Sie angeben.

![Werte für die jeweilige Ebene](./media/deployment-manager-overview/topology-values.png)

### <a name="artifact-source-for-templates"></a>Artefaktquelle für Vorlagen

In Ihrer Topologievorlage erstellen Sie eine Artefaktquelle, die die Vorlagen und Parameterdateien enthält. Die Artefaktquelle bietet eine Möglichkeit, die Dateien mithilfe von Pull für die Bereitstellung zu übertragen. Später in diesem Artikel sehen Sie eine weitere Artefaktquelle für Binärdateien.

Das folgende Beispiel zeigt das allgemeine Format der Artefaktquelle.

```json
{
    "type": "Microsoft.DeploymentManager/artifactSources",
    "name": "<artifact-source-name>",
    "location": "<artifact-source-location>",
    "apiVersion": "2018-09-01-preview",
    "properties": {
        "sourceType": "AzureStorage",
        "artifactRoot": "<root-folder-for-templates>",
        "authentication": {
            "type": "SAS",
            "properties": {
                "sasUri": "<SAS-URI-for-storage-container>"
            }
        }
    }
}
```

Weitere Informationen finden Sie in der [artifactSources-Vorlagenreferenz](/azure/templates/Microsoft.DeploymentManager/artifactSources).

### <a name="service-topology"></a>Diensttopologie

Das folgende Beispiel zeigt das allgemeine Format der Diensttopologieressource. Sie geben die Ressourcen-ID der Artefaktquelle an, die die Vorlagen und Parameterdateien enthält. Die Diensttopologie umfasst alle Dienstressourcen. Um sicherzustellen, dass die Artefaktquelle verfügbar ist, hängt die Diensttopologie davon ab.

```json
{
    "type": "Microsoft.DeploymentManager/serviceTopologies",
    "name": "<topology-name>",
    "location": "<topology-location>",
    "apiVersion": "2018-09-01-preview",
    "properties": {
        "artifactSourceId": "<resource-ID-artifact-source>"
    },
    "dependsOn": [
        "<artifact-source>"
    ],
    "resources": [
        {
            "type": "services",
            ...
        }
    ]
}
```

Weitere Informationen finden Sie in der [serviceTopologies-Vorlagenreferenz](/azure/templates/Microsoft.DeploymentManager/serviceTopologies).

### <a name="services"></a>Dienste

Das folgende Beispiel zeigt das allgemeine Format der Dienstressource. Geben Sie in jedem Dienst den Standort und die Azure-Abonnement-ID für die Bereitstellung Ihres Diensts an. Wenn Sie die Bereitstellung in mehreren Regionen ausführen möchten, definieren Sie einen Dienst für jede Region. Der Dienst hängt von der Diensttopologie ab.

```json
{
    "type": "services",
    "name": "<service-name>",
    "location": "<service-location>",
    "apiVersion": "2018-09-01-preview",
    "dependsOn": [
        "<service-topology>"
    ],
    "properties": {
        "targetSubscriptionId": "<subscription-ID>",
        "targetLocation": "<location-of-deployed-service>"
    },
    "resources": [
        {
            "type": "serviceUnits",
            ...
        }
    ]
}
```

Weitere Informationen finden Sie in der [services-Vorlagenreferenz](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services).

### <a name="service-units"></a>Diensteinheiten

Das folgende Beispiel zeigt das allgemeine Format der Diensteinheitenressource. Geben Sie in jeder Diensteinheit die Ressourcengruppe, den für die Bereitstellung zu verwendenden [Bereitstellungsmodus](deployment-modes.md) und den Pfad zur Vorlage und zur Parameterdatei an. Wenn Sie einen relativen Pfad für die Vorlage und die Parameter angeben, wird der vollständige Pfad aus dem Stammordner in der Artefaktquelle erstellt. Sie können einen absoluten Pfad für die Vorlage und die Parameter angeben, doch dann können Sie Ihre Releases nicht mehr einfach versionieren. Diensteinheiten hängen vom Dienst ab.

```json
{
    "type": "serviceUnits",
    "name": "<service-unit-name>",
    "location": "<service-unit-location>",
    "apiVersion": "2018-09-01-preview",
    "dependsOn": [
        "<service>"
    ],
    "tags": {
        "serviceType": "Service West US Web App"
    },
    "properties": {
        "targetResourceGroup": "<resource-group-name>",
        "deploymentMode": "Incremental",
        "artifacts": {
            "templateArtifactSourceRelativePath": "<relative-path-to-template>",
            "parametersArtifactSourceRelativePath": "<relative-path-to-parameter-file>"
        }
    }
}
```

Jede Vorlage sollte die zugehörigen Ressourcen enthalten, die Sie in einem Schritt bereitstellen möchten. Eine Diensteinheit kann z.B. über eine Vorlage verfügen, die alle Ressourcen für das Front-End Ihres Diensts bereitstellt.

Weitere Informationen finden Sie in der [serviceUnits-Vorlagenreferenz](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits).

## <a name="rollout-template"></a>Rolloutvorlage

Die Rolloutvorlage beschreibt die auszuführenden Schritte beim Bereitstellen Ihres Diensts. Sie geben die zu verwendende Diensttopologie an und definieren die Reihenfolge für die Bereitstellung der Diensteinheiten. Die Vorlage enthält eine Artefaktquelle zum Speichern von Binärdateien für die Bereitstellung. In Ihrer Rolloutvorlage definieren Sie die folgende Hierarchie:

* Artefaktquelle
* Schritt
* Rollout
  * Schrittgruppen
    * Bereitstellungsvorgänge

Die folgende Abbildung zeigt die Hierarchie der Rolloutvorlage:

![Hierarchie von Rollout bis zu den Schritten](./media/deployment-manager-overview/Rollout.png)

Jeder Rollout kann mehrere Schrittgruppen umfassen. Jede Schrittgruppe verfügt über einen Bereitstellungsvorgang, der auf eine Diensteinheit in der Diensttopologie verweist.

### <a name="artifact-source-for-binaries"></a>Artefaktquelle für Binärdateien

In der Rolloutvorlage erstellen Sie eine Artefaktquelle für die Binärdateien, die Sie für den Dienst bereitstellen. Diese Artefaktquelle ähnelt der [Artefaktquelle für Vorlagen](#artifact-source-for-templates), mit der Ausnahme, dass sie Skripts, Webseiten, kompilierten Code oder andere Dateien enthält, die Ihr Dienst benötigt.

### <a name="steps"></a>Schritte

Sie können einen Schritt definieren, der entweder vor oder nach der Bereitstellung ausgeführt werden soll. Derzeit stehen nur die Schritte `wait` und „healthCheck“ zur Verfügung. 

Der Warteschritt hält die Bereitstellung zwischendurch an. So können Sie überprüfen, ob Ihr Dienst wie erwartet ausgeführt wird, bevor Sie die nächste Diensteinheit bereitstellen. Das folgende Beispiel zeigt das allgemeine Format eines Warteschritts.

```json
{
    "apiVersion": "2018-09-01-preview",
    "type": "Microsoft.DeploymentManager/steps",
    "name": "waitStep",
        "location": "<step-location>",
    "properties": {
        "stepType": "wait",
        "attributes": {
          "duration": "PT1M"
        }
    }
},
```

Die Eigenschaft „duration“ verwendet den [ISO 8601-Standard](https://en.wikipedia.org/wiki/ISO_8601#Durations). Das vorherige Beispiel gibt eine Wartezeit von einer Minute an.

Weitere Informationen zum Integritätsüberprüfungsschritt finden Sie unter [Rollout der Integritätsintegration in Azure-Bereitstellungs-Manager](./deployment-manager-health-check.md) und im [Tutorial: Verwenden der Integritätsprüfung im Azure-Bereitstellungs-Manager (Public Preview)](./deployment-manager-tutorial-health-check.md).

Weitere Informationen finden Sie in der [steps-Vorlagenreferenz](/azure/templates/Microsoft.DeploymentManager/steps).

### <a name="rollouts"></a>Rollouts

Um sicherzustellen, dass die Artefaktquelle verfügbar ist, hängt der Rollout davon ab. Der Rollout definiert Schrittgruppen für jede bereitgestellte Diensteinheit. Sie können Aktionen definieren, die vor oder nach der Bereitstellung ausgeführt werden sollen. Sie können beispielsweise festlegen, dass die Bereitstellung nach dem Bereitstellen der Diensteinheit pausiert. Die Reihenfolge der Schrittgruppen lässt sich definieren.

Das Identitätsobjekt gibt die [benutzerseitig zugewiesene verwaltete Identität](#identity-and-access) an, die die Bereitstellungsaktionen ausführt.

Das folgende Beispiel zeigt das allgemeine Format des Rollouts.

```json
{
    "type": "Microsoft.DeploymentManager/rollouts",
    "name": "<rollout-name>",
    "location": "<rollout-location>",
    "apiVersion": "2018-09-01-preview",
    "Identity": {
        "type": "userAssigned",
        "identityIds": [
            "<managed-identity-ID>"
        ]
    },
    "dependsOn": [
        "<artifact-source>"
    ],
    "properties": {
        "buildVersion": "1.0.0.0",
        "artifactSourceId": "<artifact-source-ID>",
        "targetServiceTopologyId": "<service-topology-ID>",
        "stepGroups": [
            {
                "name": "stepGroup1",
                "dependsOnStepGroups": ["<step-group-name>"],
                "preDeploymentSteps": ["<step-ID>"],
                "deploymentTargetId":
                    "<service-unit-ID>",
                "postDeploymentSteps": ["<step-ID>"]
            },
            ...
        ]
    }
}
```

Weitere Informationen finden Sie in der [rollouts-Vorlagenreferenz](/azure/templates/Microsoft.DeploymentManager/rollouts).

## <a name="parameter-file"></a>Parameterdatei

Erstellen Sie zwei Parameterdateien. Eine Parameterdatei wird bei der Bereitstellung der Diensttopologie und die andere bei der Rolloutbereitstellung verwendet. Bei einigen Werte müssen Sie darauf achten, dass sie in beiden Parameterdateien identisch sind.  

## <a name="containerroot-variable"></a>containerRoot-Variable

Bei versionierten Bereitstellungen ändert sich der Pfad zu Ihren Artefakten mit jeder neuen Version. Wenn Sie eine Bereitstellung zum ersten Mal ausführen, lautet der Pfad beispielsweise `https://<base-uri-blob-container>/binaries/1.0.0.0`. Beim zweiten Mal möglicherweise `https://<base-uri-blob-container>/binaries/1.0.0.1`. Der Bereitstellungs-Manager vereinfacht das Abrufen des korrekten Stammpfads für die aktuelle Bereitstellung mithilfe der Variablen `$containerRoot`. Dieser Wert ändert sich mit jeder Version und ist vor der Bereitstellung nicht bekannt.

Verwenden Sie die Variable `$containerRoot` in der Parameterdatei für die Vorlage zum Bereitstellen der Azure-Ressourcen. Bei der Bereitstellung wird diese Variable durch die Istwerte aus dem Rollout ersetzt. 

Beim Rollout erstellen Sie beispielsweise eine Artefaktquelle für die binären Artefakte.

```json
{
    "type": "Microsoft.DeploymentManager/artifactSources",
    "name": "[variables('rolloutArtifactSource').name]",
    "location": "[parameters('azureResourceLocation')]",
    "apiVersion": "2018-09-01-preview",
    "properties": {
        "sourceType": "AzureStorage",
        "artifactRoot": "[parameters('binaryArtifactRoot')]",
        "authentication" :
        {
            "type": "SAS",
            "properties": {
                "sasUri": "[parameters('artifactSourceSASLocation')]"
            }
        }
    }
},
```

Beachten Sie die Eigenschaften `artifactRoot` und `sasUri`. Der Artefaktstamm kann auf einen Wert wie `binaries/1.0.0.0` festgelegt werden. Der SAS-URI ist der URI des Speichercontainers mit einem SAS-Token für den Zugriff. Der Bereitstellungs-Manager erstellt automatisch den Wert der Variablen `$containerRoot`. Er kombiniert diese Werte im Format `<container>/<artifactRoot>`.

Ihre Vorlage und Parameterdatei benötigen den korrekten Pfad, um die versionierten Binärdateien abzurufen. Um beispielsweise Dateien für eine Web-App bereitzustellen, erstellen Sie die folgende Parameterdatei mit der Variablen „$containerRoot“. Sie müssen zwei umgekehrte Schrägstriche (`\\`) für den Pfad verwenden, weil der erste ein Escapezeichen ist.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "deployPackageUri": {
            "value": "$containerRoot\\helloWorldWebAppWUS.zip"
        }
    }
}
```

Verwenden Sie dann diesen Parameter in Ihrer Vorlage:

```json
{
    "name": "MSDeploy",
    "type": "extensions",
    "location": "[parameters('location')]",
    "apiVersion": "2015-08-01",
    "dependsOn": [
        "[concat('Microsoft.Web/sites/', parameters('WebAppName'))]"
    ],
    "tags": {
        "displayName": "WebAppMSDeploy"
    },
    "properties": {
        "packageUri": "[parameters('deployPackageURI')]"
    }
}
```

Verwalten Sie versionierte Bereitstellungen, indem Sie neue Ordner erstellen und diese beim Rollout übergeben. Der Pfad führt zu der Vorlage, die die Ressourcen bereitstellt.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie Grundlegendes über den Bereitstellungs-Manager gelernt. Im nächsten Artikel erfahren Sie, wie Sie mit dem Bereitstellungs-Manager Bereitstellungen ausführen.

> [!div class="nextstepaction"]
> [Tutorial: Verwenden des Azure-Bereitstellungs-Managers mit Resource Manager-Vorlagen](./deployment-manager-tutorial.md)