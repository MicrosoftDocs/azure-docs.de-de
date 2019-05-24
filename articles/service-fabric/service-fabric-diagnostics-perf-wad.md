---
title: 'Azure Service Fabric: Leistungsüberwachung mit der Microsoft Azure-Diagnoseerweiterung | Microsoft-Dokumentation'
description: Verwenden Sie die Microsoft Azure-Diagnose, um Leistungsindikatoren für Ihre Azure Service Fabric-Cluster zu erfassen.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: 20fa8945f01a3431d2fd78d545c43d6215c83f56
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66110298"
---
# <a name="performance-monitoring-with-the-windows-azure-diagnostics-extension"></a>Leistungsüberwachung mit der Microsoft Azure-Diagnoseerweiterung

In diesem Artikel erfahren Sie, wie Sie die Erfassung von Leistungsindikatoren über die Microsoft Azure-Diagnoseerweiterung für Windows-Cluster einrichten. Für Linux-Cluster muss der [Log Analytics-Agent](service-fabric-diagnostics-oms-agent.md) eingerichtet werden, um Leistungsindikatoren für die Knoten zu erfassen. 

 > [!NOTE]
> Die Microsoft Azure-Diagnoseerweiterung muss in Ihrem Cluster bereitgestellt sein, damit diese Schritte funktionieren. Falls sie nicht eingerichtet ist, helfen Ihnen die Informationen unter [Ereignisaggregation und -sammlung mit der Microsoft Azure-Diagnose](service-fabric-diagnostics-event-aggregation-wad.md) weiter.  


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="collect-performance-counters-via-the-wadcfg"></a>Erfassen von Leistungsindikatoren über WadCfg

Um Leistungsindikatoren über die Microsoft Azure-Diagnoseerweiterung erfassen zu können, müssen Sie die Konfiguration in der Resource Manager-Vorlage Ihres Clusters entsprechend ändern. Führen Sie die folgenden Schritte aus, um Ihrer Vorlage einen zu erfassenden Leistungsindikator hinzuzufügen und ein Upgrade der Resource Manager-Ressource durchzuführen.

1. Suchen Sie in der Vorlage Ihres Clusters die Konfiguration der Microsoft Azure-Diagnoseerweiterung (`WadCfg`). Hier werden zu erfassende Leistungsindikatoren unter `DiagnosticMonitorConfiguration` hinzugefügt.

2. Fügen Sie `DiagnosticMonitorConfiguration` den folgenden Abschnitt hinzu, um in Ihrer Konfiguration die Erfassung von Leistungsindikatoren einzurichten: 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    `scheduledTransferPeriod` definiert, wie häufig die Werte der zu erfassenden Leistungsindikatoren an Ihre Azure-Speichertabelle sowie an ggf. konfigurierte Senken übertragen werden sollen. 

3. Fügen Sie `PerformanceCounterConfiguration` (wurde im vorherigen Schritt deklariert) die Leistungsindikatoren hinzu, die erfasst werden sollen. Jeder zu erfassende Leistungsindikator wird mit `counterSpecifier`, `sampleRate`, `unit`, `annotation` sowie mit allen relevanten `sinks` definiert.

Hier ist ein Beispiel für eine Konfiguration mit dem Leistungsindikator für *Total Processor Time* (Prozessorzeit gesamt; Dauer der Nutzung der CPU für Verarbeitungsvorgänge) und *Service Fabric Actor Method Invocations per Second* (Service Fabric Actor-Methode – Aufrufe pro Sekunde; einer der benutzerdefinierten Service Fabric-Leistungsindikatoren) angegeben. Eine vollständige Liste mit benutzerdefinierten Service Fabric-Leistungsindikatoren finden Sie in den Abschnitten zu [Reliable Actors-Leistungsindikatoren](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters) und [Reliable Services-Leistungsindikatoren](service-fabric-reliable-serviceremoting-diagnostics.md#list-of-performance-counters).

 ```json
 "WadCfg": {
         "DiagnosticMonitorConfiguration": {
           "overallQuotaInMB": "50000",
           "EtwProviders": {
             "EtwEventSourceProviderConfiguration": [
               {
                 "provider": "Microsoft-ServiceFabric-Actors",
                 "scheduledTransferKeywordFilter": "1",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableActorEventTable"
                 }
               },
               {
                 "provider": "Microsoft-ServiceFabric-Services",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricReliableServiceEventTable"
                 }
               }
             ],
             "EtwManifestProviderConfiguration": [
               {
                 "provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
                 "scheduledTransferLogLevelFilter": "Information",
                 "scheduledTransferKeywordFilter": "4611686018427387904",
                 "scheduledTransferPeriod": "PT5M",
                 "DefaultEvents": {
                   "eventDestination": "ServiceFabricSystemEventTable"
                 }
               }
             ]
           },
           "PerformanceCounters": {
                 "scheduledTransferPeriod": "PT1M",
                 "PerformanceCounterConfiguration": [
                     {
                         "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                         "sampleRate": "PT1M",
                         "unit": "Percent",
                         "annotation": [
                         ],
                         "sinks": ""
                     },
                     {
                         "counterSpecifier": "\\Service Fabric Actor Method(*)\\Invocations/Sec",
                         "sampleRate": "PT1M",
                     }
                 ]
             }
         }
       },
  ```

 Die Samplingrate für den Leistungsindikator kann gemäß Ihren Anforderungen angepasst werden. Das Format lautet `PT<time><unit>`. Wenn der Leistungsindikator also beispielsweise sekündlich erfasst werden soll, legen Sie Folgendes fest: `"sampleRate": "PT15S"`.

 Sie können in der ARM-Vorlage auch Variablen verwenden, um ein Array von-Leistungsindikatoren zu erfassen. Dies kann hilfreich sein, wenn Sie Leistungsindikatoren pro Prozess erfassen. Im folgenden Beispiel werden Prozessorzeit und Garbage Collector-Zeit pro Prozess sowie zwei Leistungsindikatoren für die eigentlichen Knoten erfasst. Dafür werden jeweils Variablen verwendet. 

 ```json
"variables": {
  "copy": [
      {
        "name": "processorTimeCounters",
        "count": "[length(parameters('monitoredProcesses'))]",
        "input": {
          "counterSpecifier": "\\Process([parameters('monitoredProcesses')[copyIndex('processorTimeCounters')]])\\% Processor Time",
          "sampleRate": "PT1M",
          "unit": "Percent",
          "sinks": "applicationInsights",
          "annotation": [
            {
              "displayName": "[concat(parameters('monitoredProcesses')[copyIndex('processorTimeCounters')],' Processor Time')]",
              "locale": "en-us"
            }
          ]
        }
      },
      {
        "name": "gcTimeCounters",
        "count": "[length(parameters('monitoredProcesses'))]",
        "input": {
          "counterSpecifier": "\\.NET CLR Memory([parameters('monitoredProcesses')[copyIndex('gcTimeCounters')]])\\% Time in GC",
          "sampleRate": "PT1M",
          "unit": "Percent",
          "sinks": "applicationInsights",
          "annotation": [
            {
              "displayName": "[concat(parameters('monitoredProcesses')[copyIndex('gcTimeCounters')],' Time in GC')]",
              "locale": "en-us"
            }
          ]
        }
      }
    ],
    "machineCounters": [
      {
        "counterSpecifier": "\\Memory\\Available Bytes",
        "sampleRate": "PT1M",
        "unit": "KB",
        "sinks": "applicationInsights",
        "annotation": [
          {
            "displayName": "Memory Available Kb",
            "locale": "en-us"
          }
        ]
      },
      {
        "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
        "sampleRate": "PT15S",
        "unit": "percent",
        "annotation": [
          {
            "displayName": "Memory usage",
            "locale": "en-us"
          }
        ]
      }
    ]
  }
....
"WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": "50000",
      "Metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', variables('vmNodeTypeApp2Name'))]"
      },
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": "[concat(variables ('processorTimeCounters'), variables('gcTimeCounters'),  variables('machineCounters'))]"
      },
....
```

 >[!NOTE]
 >Mithilfe von `*` können Sie zwar Gruppen von Leistungsindikatoren mit ähnlichem Namen angeben, um Leistungsindikatoren jedoch über eine Senke (an Application Insights) senden zu können, müssen sie einzeln deklariert werden. 

1. Nach dem Hinzufügen der zu erfassenden Leistungsindikatoren müssen Sie Ihre Clusterressource upgraden, damit die Änderungen in Ihrem ausgeführten Cluster übernommen werden. Speichern Sie Ihre Änderungen an `template.json`, und öffnen Sie PowerShell. Das Upgrade des Clusters kann mithilfe von `New-AzResourceGroupDeployment` durchgeführt werden. Für den Aufruf müssen Sie den Namen der Ressourcengruppe, die aktualisierte Vorlagendatei und die Parameterdatei angeben. Durch den Aufruf wird Resource Manager angewiesen, entsprechende Änderungen an den von Ihnen aktualisierten Ressourcen vorzunehmen. Vergewissern Sie sich, dass Sie bei Ihrem Konto angemeldet sind und sich im richtigen Abonnement befinden, und führen Sie dann das Upgrade mithilfe des folgenden Befehls durch:

    ```sh
    New-AzResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

1. Nachdem der Rollout des Upgrades abgeschlossen ist (was zwischen 15 und 45 Minuten dauert – je nachdem, ob es sich um die erste Bereitstellung handelt und wie groß Ihre Ressourcengruppe ist), sollten die Leistungsindikatoren per Microsoft Azure-Diagnose erfasst und an die Tabelle „WADPerformanceCountersTable“ in dem Speicherkonto gesendet werden, das dem Cluster zugeordnet ist. Sie können die Leistungsindikatoren in Application Insights anzeigen, indem Sie [die AI-Senke der Resource Manager-Vorlage hinzufügen](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template).

## <a name="next-steps"></a>Nächste Schritte
* Erfassen Sie weitere Leistungsindikatoren für Ihren Cluster. Eine Liste mit Leistungsindikatoren, die Sie erfassen sollten, finden Sie unter [Leistungsmetriken](service-fabric-diagnostics-event-generation-perf.md).
* Unter [Verwenden von Überwachung und Diagnose bei einer Windows-VM und Azure Resource Manager-Vorlagen](../virtual-machines/windows/extensions-diagnostics-template.md) erfahren Sie, wie Sie `WadCfg` noch weiter anpassen können und beispielsweise zusätzliche Speicherkonten für die Übermittlung von Diagnosedaten konfigurieren.
* Besuchen Sie den [WadCfg-Generator](https://azure.github.io/azure-diagnostics-tools/config-builder/), um eine Vorlage von Grund auf neu zu erstellen und sicherzustellen, dass Ihre Syntax korrekt ist. (https://azure.github.io/azure-diagnostics-tools/config-builder/), um eine Vorlage von Grund auf neu zu erstellen und sicherzustellen, dass Ihre Syntax korrekt ist.)