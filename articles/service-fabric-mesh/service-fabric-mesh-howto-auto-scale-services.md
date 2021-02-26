---
title: Automatisches Skalieren einer in Azure Service Fabric Mesh ausgeführten App
description: Erfahren Sie, wie Sie Richtlinien für die automatische Skalierung für die Dienste einer Service Fabric Mesh-Anwendung konfigurieren.
author: georgewallace
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: a707e3601bb24b2d5c2aa9402edff4a2e8803033
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625903"
---
# <a name="create-autoscale-policies-for-a-service-fabric-mesh-application"></a>Erstellen von Richtlinien für automatische Skalierung für eine Service Fabric Mesh-Anwendung

> [!IMPORTANT]
> Die Vorschauversion von Azure Service Fabric Mesh wurde eingestellt. Neue Bereitstellungen werden nicht mehr über die Service Fabric Mesh-API gestattet. Unterstützung für vorhandene Bereitstellungen wird bis zum 28. April 2021 fortgesetzt.
> 
> Einzelheiten finden Sie unter [Einstellung der Vorschauversion von Azure Service Fabric Mesh](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Einer der Hauptvorteile bei der Bereitstellung von Anwendungen in Service Fabric Mesh ist die Möglichkeit, Ihre Dienste ganz einfach horizontal hoch- und herunterzuskalieren. Auf diese Weise können Sie unterschiedlich starke Dienstauslastungen auffangen oder die Verfügbarkeit verbessern. Sie können Ihre Dienste manuell herunter- oder hochskalieren oder Richtlinien für die automatische Skalierung einrichten.

[Automatische Skalierung](service-fabric-mesh-scalability.md#autoscaling-service-instances) ermöglicht es Ihnen, die Anzahl Ihrer Dienstinstanzen dynamisch zu skalieren (horizontale Skalierung). Die automatische Skalierung bietet eine hohe Flexibilität und ermöglicht die Bereitstellung oder Entfernung von Dienstinstanzen basierend auf der CPU- oder Speicherauslastung.

## <a name="options-for-creating-an-auto-scaling-policy-trigger-and-mechanism"></a>Optionen zum Erstellen einer Richtlinie für die automatische Skalierung, eines Triggers und eines Mechanismus für automatische Skalierung
Für jeden Dienst, den Sie skalieren möchten, wird eine Richtlinie für die automatische Skalierung definiert. Die Richtlinie wird entweder in der YAML-Dienstressourcendatei oder in der JSON-Bereitstellungsvorlage definiert. Jede Skalierungsrichtlinie besteht aus zwei Komponenten: einem Trigger und einem Skalierungsmechanismus.

Der Trigger definiert, wann eine Richtlinie für die automatische Skalierung aufgerufen wird.  Geben Sie die Art des Triggers (durchschnittliche Auslastung) und die zu überwachende Metrik (CPU oder Arbeitsspeicher) an.  Obere und untere Auslastungsschwellenwerte werden in Prozentwerten angegeben. Das Skalierungsintervall definiert, wie oft (in Sekunden) die angegebene Auslastung (z.B. die durchschnittliche CPU-Auslastung) über alle aktuell installierten Dienstinstanzen hinweg überprüft werden soll.  Der Mechanismus wird ausgelöst, wenn die überwachte Metrik unter den unteren Schwellenwert fällt oder über den oberen Schwellenwert ansteigt.  

Der Skalierungsmechanismus definiert, wie der Skalierungsvorgang ausgeführt werden soll, wenn die Richtlinie ausgelöst wird.  Geben Sie die Art des Mechanismus (Replikat hinzufügen/entfernen) und die minimale und maximale Anzahl der Replikate (als ganze Zahlen) an.  Die Anzahl der Dienstreplikate wird niemals unter die Mindestanzahl oder über die Höchstanzahl skaliert.  Geben Sie auch das Skalierungsinkrement als ganze Zahl an, d.h. die Anzahl der Replikate, die bei einem Skalierungsvorgang hinzugefügt oder entfernt werden.  

## <a name="define-an-auto-scaling-policy-in-a-json-template"></a>Definieren einer Richtlinie für die automatische Skalierung in einer JSON-Vorlage

Das folgende Beispiel zeigt eine Richtlinie für die automatische Skalierung in einer JSON-Bereitstellungsvorlage.  Die Richtlinie für die automatische Skalierung wird in einer Eigenschaft des zu skalierenden Diensts deklariert.  In diesem Beispiel wird ein Trigger für die durchschnittliche CPU-Last definiert.  Der Mechanismus wird ausgelöst, wenn die durchschnittliche CPU-Auslastung aller bereitgestellten Instanzen unter 0,2 (20 %) fällt oder über 0,8 (80 %) steigt.  Die CPU-Auslastung wird alle 60 Sekunden überprüft.  Der Skalierungsmechanismus wird definiert, um Instanzen hinzuzufügen oder zu entfernen, wenn die Richtlinie ausgelöst wird.  Dienstinstanzen werden in Inkrementen von jeweils 1 hinzugefügt oder entfernt.  Eine Mindestinstanzanzahl von eins und eine maximale Instanzanzahl von 40 werden ebenfalls definiert.

```json
{
"apiVersion": "2018-09-01-preview",
"name": "WorkerApp",
"type": "Microsoft.ServiceFabricMesh/applications",
"location": "[parameters('location')]",
"dependsOn": [
"Microsoft.ServiceFabricMesh/networks/worker-app-network"
],
"properties": {
"services": [   
    { ... },       
    {
    "name": "WorkerService",
    "properties": {
        "description": "Worker Service",
        "osType": "linux",
        "codePackages": [
        {  ...              }
        ],
        "replicaCount": 1,
        "autoScalingPolicies": [
        {
            "name": "AutoScaleWorkerRule",
            "trigger": {
                "kind": "AverageLoad",
                "metric": {
                    "kind": "Resource",
                    "name": "cpu"
                },
                "lowerLoadThreshold": "0.2",
                "upperLoadThreshold": "0.8",
                "scaleIntervalInSeconds": "60"
            },
            "mechanism": {
                "kind": "AddRemoveReplica",
                "minCount": "1",
                "maxCount": "40",
                "scaleIncrement": "1"
            }
        }
        ],        
        ...
    }
    }
]
}
}
```

## <a name="define-an-autoscale-policy-in-a-serviceyaml-resource-file"></a>Definieren einer Richtlinie für die automatische Skalierung in einer Ressourcendatei „service.yaml“
Das folgende Beispiel zeigt eine Richtlinie für die automatische Skalierung in einer Dienstressourcendatei (YAML-Datei).  Die Richtlinie für die automatische Skalierung wird als eine Eigenschaft des zu skalierenden Diensts deklariert.  In diesem Beispiel wird ein Trigger für die durchschnittliche CPU-Last definiert.  Der Mechanismus wird ausgelöst, wenn die durchschnittliche CPU-Auslastung aller bereitgestellten Instanzen unter 0,2 (20 %) fällt oder über 0,8 (80 %) steigt.  Die CPU-Auslastung wird alle 60 Sekunden überprüft.  Der Skalierungsmechanismus wird definiert, um Instanzen hinzuzufügen oder zu entfernen, wenn die Richtlinie ausgelöst wird.  Dienstinstanzen werden in Inkrementen von jeweils 1 hinzugefügt oder entfernt.  Eine Mindestinstanzanzahl von eins und eine maximale Instanzanzahl von 40 werden ebenfalls definiert.

```yaml
## Service definition ##
application:
  schemaVersion: 1.0.0-preview2
  name: WorkerApp
  properties:
    services:
      - name: WorkerService
        properties:
          description: WorkerService description.
          osType: Linux
          codePackages:
            ...
          replicaCount: 1
          autoScalingPolicies:
            - name: AutoScaleWorkerRule
              trigger:
                kind: AverageLoad
                metric:
                  kind: Resource
                  name: cpu
                lowerLoadThreshold: 0.2
                upperLoadThreshold: 0.8
                scaleIntervalInSeconds: 60
              mechanism:
                kind: AddRemoveReplica
                minCount: 1
                maxCount: 40
                scaleIncrement: 1
          ...
```

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr über das [manuelle Skalieren eines Diensts](service-fabric-mesh-tutorial-template-scale-services.md)
