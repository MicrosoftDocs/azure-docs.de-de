---
title: Verwenden der Azure-Autoskalierung mit Gastmetriken in einer Linux-Skalierungsgruppenvorlage | Microsoft-Dokumentationen
description: Erfahren Sie, wie Sie die Autoskalierung mit Gastmetriken in einer Linux-Vorlage für virtuelle Computer in einer VM-Skalierungsgruppe verwenden können
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: drewm
editor: ''
tags: azure-resource-manager
ms.assetid: na
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: 8cd665ffd82547c4f554eb4a515a8da7dc5b3f5f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64868991"
---
# <a name="autoscale-using-guest-metrics-in-a-linux-scale-set-template"></a>Autoskalierung mit Gastmetriken in einer Linux-Skalierungsgruppenvorlage

Es gibt zwei allgemeine Arten von Metriken in Azure, die aus VMs und Skalierungsgruppen erfasst werden: Hostmetriken und Gastmetriken. Grundsätzlich gilt: Wenn Sie standardmäßige CPU-, Datenträger- und Netzwerkmetriken verwenden möchten, sind Hostmetriken eine gute Wahl. Wenn Sie jedoch eine größere Auswahl von Metriken benötigen, sind Gastmetriken wahrscheinlich besser geeignet.

Hostmetriken setzen keine zusätzlichen Einstellungen voraus, weil sie vom virtuellen Hostcomputer gesammelt werden, während für Gastmetriken die [Windows Azure Diagnostics-Erweiterung](../virtual-machines/windows/extensions-diagnostics-template.md) oder die [Linux Azure Diagnostics-Erweiterung](../virtual-machines/linux/diagnostic-extension.md) auf dem virtuellen Gastcomputer installiert werden muss. Ein häufiger Grund für die Verwendung von Gastmetriken anstatt von Hostmetriken ist, dass Gastmetriken eine größere Auswahl an Metriken bieten als Hostmetriken. Ein Beispiel dafür sind Speicherverbrauchsmetriken, die nur über Gastmetriken zur Verfügung stehen. Die unterstützten Hostmetriken finden Sie [hier](../azure-monitor/platform/metrics-supported.md), und häufig verwendete Gastmetriken finden Sie [hier](../azure-monitor/platform/autoscale-common-metrics.md). In diesem Artikel wird gezeigt, wie Sie die [Vorlage für die grundlegende Skalierungsgruppe](virtual-machine-scale-sets-mvss-start.md) verändern müssen, um bei Linux-Skalierungsgruppen auf Gastmetriken basierende Autoskalierungsregeln verwenden zu können.

## <a name="change-the-template-definition"></a>Ändern der Vorlagendefinition

In einem [vorherigen Artikel](virtual-machine-scale-sets-mvss-start.md) haben wir eine grundlegende Vorlage für eine Skalierungsgruppe erstellt. Wir verwenden nun diese frühere Vorlage und ändern sie, um eine Vorlage zu erstellen, die eine Linux-Skalierungsgruppe mit auf Gastmetriken basierender Autoskalierung bereitstellt.

Fügen Sie zunächst Parameter für `storageAccountName` und `storageAccountSasToken` hinzu. Der Diagnose-Agent speichert Metrikdaten in einer [Tabelle](../cosmos-db/table-storage-how-to-use-dotnet.md) in diesem Speicherkonto. Ab Version 3.0 des Linux-Diagnose-Agents wird der Speicherzugriffsschlüssel nicht mehr unterstützt. Verwenden Sie daher stattdessen ein [SAS-Token](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "storageAccountName": {
+      "type": "string"
+    },
+    "storageAccountSasToken": {
+      "type": "securestring"
     }
   },
```

Fügen Sie im nächsten Schritt der Skalierungsgruppe `extensionProfile` die Diagnoseerweiterung hinzu. Geben Sie bei dieser Konfiguration die Ressourcen-ID der Skalierungsgruppe an, von der Metriken gesammelt werden sollen. Geben Sie weiterhin das Speicherkonto und das SAS-Token an, die zum Speichern der Metriken verwendet werden sollen. Geben Sie ferner an, wie häufig die Metriken aggregiert werden (hier minütlich) und welche Metriken verfolgt werden sollen (hier Prozent des belegten Speichers). Weitere Informationen über diese Konfiguration und andere Metriken als den Prozentsatz des verbrauchten Speichers finden Sie in [dieser Dokumentation](../virtual-machines/linux/diagnostic-extension.md).

```diff
                 }
               }
             ]
+          },
+          "extensionProfile": {
+            "extensions": [
+              {
+                "name": "LinuxDiagnosticExtension",
+                "properties": {
+                  "publisher": "Microsoft.Azure.Diagnostics",
+                  "type": "LinuxDiagnostic",
+                  "typeHandlerVersion": "3.0",
+                  "settings": {
+                    "StorageAccount": "[parameters('storageAccountName')]",
+                    "ladCfg": {
+                      "diagnosticMonitorConfiguration": {
+                        "performanceCounters": {
+                          "sinks": "WADMetricJsonBlob",
+                          "performanceCounterConfiguration": [
+                            {
+                              "unit": "percent",
+                              "type": "builtin",
+                              "class": "memory",
+                              "counter": "percentUsedMemory",
+                              "counterSpecifier": "/builtin/memory/percentUsedMemory",
+                              "condition": "IsAggregate=TRUE"
+                            }
+                          ]
+                        },
+                        "metrics": {
+                          "metricAggregation": [
+                            {
+                              "scheduledTransferPeriod": "PT1M"
+                            }
+                          ],
+                          "resourceId": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]"
+                        }
+                      }
+                    }
+                  },
+                  "protectedSettings": {
+                    "storageAccountName": "[parameters('storageAccountName')]",
+                    "storageAccountSasToken": "[parameters('storageAccountSasToken')]",
+                    "sinksConfig": {
+                      "sink": [
+                        {
+                          "name": "WADMetricJsonBlob",
+                          "type": "JsonBlob"
+                        }
+                      ]
+                    }
+                  }
+                }
+              }
+            ]
           }
         }
       }
```

Fügen Sie schließlich eine `autoscaleSettings`-Ressource hinzu, um basierend auf diesen Metriken die automatische Skalierung konfigurieren zu können. Diese Ressource verfügt über eine `dependsOn`-Klausel, die auf die Skalierungsgruppe verweist. So wird vor dem Autoskalierungsversuch sichergestellt, dass die Skalierungsgruppe existiert. Wenn Sie eine andere Metrik für die automatische Skalierung auswählen, würden Sie den `counterSpecifier` aus der Konfiguration der Diagnoseerweiterung als `metricName` in der Konfiguration der automatischen Skalierung verwenden. Weitere Informationen über das Konfigurieren der Autoskalierung finden Sie in [Bewährte Methoden für die automatische Skalierung](../azure-monitor/platform/autoscale-best-practices.md) und der Referenzdokumentation der [Azure Monitor REST-API](/rest/api/monitor/autoscalesettings).

```diff
+    },
+    {
+      "type": "Microsoft.Insights/autoscaleSettings",
+      "apiVersion": "2015-04-01",
+      "name": "guestMetricsAutoscale",
+      "location": "[resourceGroup().location]",
+      "dependsOn": [
+        "Microsoft.Compute/virtualMachineScaleSets/myScaleSet"
+      ],
+      "properties": {
+        "name": "guestMetricsAutoscale",
+        "targetResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+        "enabled": true,
+        "profiles": [
+          {
+            "name": "Profile1",
+            "capacity": {
+              "minimum": "1",
+              "maximum": "10",
+              "default": "3"
+            },
+            "rules": [
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "GreaterThan",
+                  "threshold": 60
+                },
+                "scaleAction": {
+                  "direction": "Increase",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              },
+              {
+                "metricTrigger": {
+                  "metricName": "/builtin/memory/percentUsedMemory",
+                  "metricNamespace": "",
+                  "metricResourceUri": "[resourceId('Microsoft.Compute/virtualMachineScaleSets', 'myScaleSet')]",
+                  "timeGrain": "PT1M",
+                  "statistic": "Average",
+                  "timeWindow": "PT5M",
+                  "timeAggregation": "Average",
+                  "operator": "LessThan",
+                  "threshold": 30
+                },
+                "scaleAction": {
+                  "direction": "Decrease",
+                  "type": "ChangeCount",
+                  "value": "1",
+                  "cooldown": "PT1M"
+                }
+              }
+            ]
+          }
+        ]
+      }
     }
   ]
 }
```





## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
