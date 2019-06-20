---
title: Erweiterte automatische Skalierung mit Azure Virtual Machines
description: Hier werden Resource Manager und VM Scale Sets mit mehreren Regeln und Profilen verwendet, die mit Skalierungsaktionen E-Mails senden und Webhook-URLs aufrufen.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/22/2016
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: 6da653bc94c8b549282ab9124dba23b08771c5f1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60787783"
---
# <a name="advanced-autoscale-configuration-using-resource-manager-templates-for-vm-scale-sets"></a>Konfiguration der erweiterten automatischen Skalierung mithilfe von Resource Manager-Vorlagen für VM Scale Sets
Sie können VM-Skalierungsgruppen basierend auf Leistungsmetrik-Schwellenwerten horizontal herunter- und hochskalieren – entweder nach einem sich wiederholenden Zeitplan oder zu einem bestimmten Datum. Außerdem können Sie E-Mail- und Webhookbenachrichtigungen für Skalierungsaktionen konfigurieren. Diese exemplarische Vorgehensweise zeigt ein Beispiel für die Konfiguration der oben genannten Objekte mithilfe einer Resource Manager-Vorlage für eine VM-Skalierungsgruppe.

> [!NOTE]
> Während diese exemplarische Vorgehensweise die Schritte für VM-Skalierungsgruppen erläutert, gelten dieselben Informationen auch für die automatische Skalierung von [Clouddiensten](https://azure.microsoft.com/services/cloud-services/), [App Service – Web Apps](https://azure.microsoft.com/services/app-service/web/) und [API Management-Diensten](https://docs.microsoft.com/azure/api-management/api-management-key-concepts). Informationen zu einer einfachen Einstellung zum horizontalen Herunterskalieren bzw. Hochskalieren für eine VM-Skalierungsgruppe, die auf einer einfachen Leistungsmetrik wie „CPU“ basiert, finden Sie in den [Linux](../../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-cli.md)- und [Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md)-Dokumenten.
>
>

## <a name="walkthrough"></a>Exemplarische Vorgehensweise
In dieser exemplarischen Vorgehensweise verwenden wir den [Azure-Ressourcen-Explorer](https://resources.azure.com/) zum Konfigurieren und Aktualisieren der Einstellung für das automatische Skalieren einer Skalierungsgruppe. Der Azure-Ressourcen-Explorer bietet eine einfache Möglichkeit zum Verwalten von Azure-Ressourcen über Resource Manager-Vorlagen. Wenn Sie den Azure-Ressourcen-Explorer noch nicht kennen, lesen Sie zunächst [diese Einführung](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/).

1. Stellen Sie eine neue Skalierungsgruppe mit grundlegenden Einstellungen für die automatischen Skalierung bereit. In diesem Artikel wird die Windows-Skalierungsgruppe aus dem Azure-Schnellstartkatalog mit einer Standardvorlage für die automatische Skalierung verwendet. Die Linux-Skalierung erfolgt in gleicher Weise.
2. Navigieren Sie nach dem Erstellen der Skalierungsgruppe mithilfe des Azure-Ressourcen-Explorers zur Ressource der Skalierungsgruppe. Unter dem Microsoft.Insights-Knoten wird Folgendes angezeigt:

    ![Azure Explorer](media/autoscale-virtual-machine-scale-sets/azure_explorer_navigate.png)

    Durch die Ausführung der Vorlage wurde eine Standardeinstellung für die automatische Skalierung mit dem Namen **autoscalewad** erstellt. Auf der rechten Seite wird die vollständige Definition dieser Einstellung für die automatische Skalierung angezeigt. In diesem Fall verfügt die Standardeinstellung für die automatische Skalierung über eine CPU%-basierte Regel für horizontales Hoch- und Herunterskalieren.  

3. Sie können nun basierend auf dem Zeitplan oder bestimmten Anforderungen weitere Profile und Regeln hinzufügen. Wir erstellen eine Einstellung für die automatische Skalierung mit drei Profilen. Weitere Informationen zu Profilen und Regeln bei der automatischen Skalierung finden Sie unter [Empfohlene Methoden für die automatische Skalierung](autoscale-best-practices.md).  

    | Profile und Regeln | BESCHREIBUNG |
    |--- | --- |
    | **Profil** |**Basierend auf Leistung/Metrik** |
    | Regel |Anzahl von Service Bus-Warteschlangennachrichten > x |
    | Regel |Anzahl von Service Bus-Warteschlangennachrichten < y |
    | Regel |CPU % > n |
    | Regel |CPU% < p |
    | **Profil** |**Wochentage morgens (keine Regeln)** |
    | **Profil** |**Tag der Produkteinführung (keine Regeln)** |

4. Dies ist ein hypothetisches Skalierungsszenario, das wir für diese exemplarische Vorgehensweise verwenden.

   * **Lastbasiert:** Ich möchte je nach Last meiner Anwendung, die in meiner Skalierungsgruppe gehostet wird, horizontal hoch- oder herunterskalieren.*
   * **Länge der Nachrichtenwarteschlange:** Ich verwende eine Service Bus-Warteschlange für die eingehenden Nachrichten, die an meine Anwendung gerichtet sind. Ich verwende die Anzahl von Warteschlangennachrichten und CPU%. Außerdem konfiguriere ich ein Standardprofil zum Auslösen einer Skalierungsaktion, wenn entweder die Nachrichtenanzahl oder die CPU-Nutzung in Prozent den Schwellenwert erreicht.\*
   * **Zeitpunkt innerhalb einer Woche und Tageszeit:** Ich möchte ein sich wöchentlich wiederholendes, auf der Tageszeit basierendes Profil namens „Wochentage, morgens“ verwenden. Den Verlaufsdaten habe ich entnommen, dass es besser ist, über eine bestimmte Anzahl von VM-Instanzen zu verfügen, damit meine Anwendungslast während dieser Zeit bewältigt werden kann.\*
   * **Sondertermine:** Ich habe ein Profil vom Typ „Tag der Produkteinführung“ hinzugefügt. Ich plane für bestimmte Tage im Voraus, damit meine Anwendung die Last verarbeiten kann, die durch Marketingankündigungen und die Einführung neuer Produkte verursacht wird.\*
   * *Die letzten zwei Profile können auch andere auf Leistungsmetriken basierende Regeln umfassen. In diesem Fall werden jedoch stattdessen die auf Leistungsmetriken basierenden Standardregeln verwendet. Regeln sind für sich wiederholende und datumsbasierte Profile optional.*

     Die Profil- und Regelpriorisierung der Engine für die automatische Skalierung wird auch im Artikel [Empfohlene Methoden für die automatische Skalierung](autoscale-best-practices.md) erläutert.
     Eine Liste mit gängigen Metriken für die automatische Skalierung finden Sie unter [Allgemeine Metriken für die automatische Skalierung](autoscale-common-metrics.md).

5. Stellen Sie sicher, dass Sie sich im Ressourcen-Explorer im Modus **Lesen/Schreiben** befinden.

    ![Autoscalewad, Standardeinstellung für die automatische Skalierung](media/autoscale-virtual-machine-scale-sets/autoscalewad.png)

6. Klicken Sie auf "Bearbeiten". **Ersetzen** Sie das profiles-Element in der Einstellung für die automatische Skalierung durch folgende Konfiguration:

    ![profiles](media/autoscale-virtual-machine-scale-sets/profiles.png)

    ```
    {
            "name": "Perf_Based_Scale",
            "capacity": {
              "minimum": "2",
              "maximum": "12",
              "default": "2"
            },
            "rules": [
              {
                "metricTrigger": {
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 10
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "MessageCount",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT5M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 3
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "Percentage CPU",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
                  "timeAggregation": "Average",
                  "operator": "GreaterThan",
                  "threshold": 85
                },
                "scaleAction": {
                  "direction": "Increase",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              },
              {
                "metricTrigger": {
                  "metricName": "Percentage CPU",
                  "metricNamespace": "",
                  "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/<this_vmss_name>",
                  "timeGrain": "PT5M",
                  "statistic": "Average",
                  "timeWindow": "PT30M",
                  "timeAggregation": "Average",
                  "operator": "LessThan",
                  "threshold": 60
                },
                "scaleAction": {
                  "direction": "Decrease",
                  "type": "ChangeCount",
                  "value": "1",
                  "cooldown": "PT5M"
                }
              }
            ]
          },
          {
            "name": "Weekday_Morning_Hours_Scale",
            "capacity": {
              "minimum": "4",
              "maximum": "12",
              "default": "4"
            },
            "rules": [],
            "recurrence": {
              "frequency": "Week",
              "schedule": {
                "timeZone": "Pacific Standard Time",
                "days": [
                  "Monday",
                  "Tuesday",
                  "Wednesday",
                  "Thursday",
                  "Friday"
                ],
                "hours": [
                  6
                ],
                "minutes": [
                  0
                ]
              }
            }
          },
          {
            "name": "Product_Launch_Day",
            "capacity": {
              "minimum": "6",
              "maximum": "20",
              "default": "6"
            },
            "rules": [],
            "fixedDate": {
              "timeZone": "Pacific Standard Time",
              "start": "2016-06-20T00:06:00Z",
              "end": "2016-06-21T23:59:00Z"
            }
          }
    ```
    Die unterstützten Felder und ihre Werte finden Sie in der [Dokumentation zur REST-API für die automatische Skalierung](https://msdn.microsoft.com/library/azure/dn931928.aspx). Ihre Einstellung für die automatische Skalierung enthält jetzt die drei zuvor erläuterten Profile.

7. Betrachten Sie abschließend den Abschnitt **notification** der automatischen Skalierung. Mit Benachrichtigungen für die automatische Skalierung können Sie drei Aufgaben ausführen, wenn eine Aktion zum horizontalen Hoch- oder Herunterskalieren erfolgreich ausgelöst wurde.
   - Sie können den Administrator und Co-Administratoren Ihres Abonnements benachrichtigen.
   - Sie können eine E-Mail an eine Benutzergruppe senden.
   - Sie können einen Webhookaufruf auslösen. Wenn dieser Webhook ausgelöst wird, sendet er Metadaten zur Bedingung für die automatische Skalierung sowie zur Ressource der Skalierungsgruppe. Weitere Informationen über die Nutzlast des Webhooks für die automatische Skalierung finden Sie unter [Konfigurieren von Webhook- und E-Mail-Benachrichtigungen für die automatische Skalierung](autoscale-webhook-email.md).

   Ergänzen Sie die Einstellung für die automatische Skalierung wie folgt, und ersetzen Sie dabei Ihr **notification**-Element, dessen Wert NULL ist.

   ```
   "notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": true,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]

   ```

   Klicken Sie im Ressourcen-Explorer auf die Schaltfläche **Put**, um die Einstellung für die automatische Skalierung zu aktualisieren.

Sie haben eine Einstellung für die automatische Skalierung für eine VM-Skalierungsgruppe aktualisiert, um mehrere Skalierungsprofile und Skalierungsbenachrichtigungen aufzunehmen.

## <a name="next-steps"></a>Nächste Schritte
Über die folgenden Links können Sie auf weitere Informationen zur automatischen Skalierung zugreifen.

[Beheben von Problemen bei der automatischen Skalierung von VM-Skalierungsgruppen](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)

[Allgemeine Metriken für die automatische Skalierung](autoscale-common-metrics.md)

[Empfohlene Methoden für die automatische Skalierung](autoscale-best-practices.md)

[Verwalten der automatischen Skalierung mit PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md#create-and-manage-autoscale-settings)

[Verwalten der automatischen Skalierung über die CLI](cli-samples.md#autoscale)

[Konfigurieren von Webhook- und E-Mail-Benachrichtigungen für die automatische Skalierung](autoscale-webhook-email.md)

[Microsoft.Insights/autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings)-Vorlagenreferenz
