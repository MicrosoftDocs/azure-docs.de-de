---
title: Grundlegendes zu Einstellungen für die automatische Skalierung in Azure Monitor
description: Enthält eine ausführliche Aufschlüsselung der Einstellungen für die automatische Skalierung und deren Funktionsweise. Gilt für Virtual Machines, Cloud Services und Web-Apps.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/18/2017
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: 02840b8a909f46c37130bdb7162674c694a0ff96
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60787494"
---
# <a name="understand-autoscale-settings"></a>Grundlegendes zu Einstellungen für die automatische Skalierung
Mithilfe der Einstellungen für die automatische Skalierung können Sie sicherstellen, dass Sie über die richtige Anzahl von ausgeführten Ressourcen verfügen, um die wechselnde Auslastung Ihrer Anwendung zu bewältigen. Sie können konfigurieren, dass Einstellungen für die automatische Skalierung basierend auf Metriken ausgelöst werden, mit denen die Auslastung oder die Leistung angezeigt werden, oder die Auslösung kann zu einem geplanten Datum bzw. einer geplanten Uhrzeit erfolgen. In diesem Artikel wird die Anatomie einer Einstellung für die automatische Skalierung ausführlich beschrieben. Der Artikel beginnt mit Erläuterungen zu dem Schema und den Eigenschaften einer Einstellung und führt Sie dann durch die verschiedenen Profiltypen, die konfiguriert werden können. Abschließend erfahren Sie in diesem Artikel, wie das Feature für die automatische Skalierung in Azure auswertet, welches Profil zu einem bestimmten Zeitpunkt ausgeführt wird.

## <a name="autoscale-setting-schema"></a>Schema der Einstellung für die automatische Skalierung
Zur Veranschaulichung des Schemas der Einstellung für die automatische Skalierung wird die folgende Einstellung verwendet. Es ist wichtig zu beachten, dass diese Einstellung für die automatische Skalierung über Folgendes verfügt:
- Ein Profil 
- Zwei Metrikregeln in diesem Profil: eine für das horizontale Hochskalieren und eine für das horizontale Herunterskalieren
  - Die Regel für das horizontale Hochskalieren wird ausgelöst, wenn der Durchschnittswert für die Metrik „CPU in Prozent“ für die VM-Skalierungsgruppe in den letzten zehn Minuten über 85 Prozent gelegen hat.
  - Die Regel für das horizontale Herunterskalieren wird ausgelöst, wenn der Durchschnittswert der VM-Skalierungsgruppe in der letzten Minute unter 60 Prozent gelegen hat.

> [!NOTE]
> Eine Einstellung kann mehrere Profile aufweisen. Weitere Informationen finden Sie im Abschnitt [Profile](#autoscale-profiles). Für ein Profil können auch mehrere Regeln für das horizontale Hochskalieren bzw. horizontale Herunterskalieren definiert sein. Informationen darüber, wie sie ausgewertet werden, erhalten Sie im Abschnitt [Auswertung](#autoscale-evaluation).

```JSON
{
  "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/setting1",
  "name": "setting1",
  "type": "Microsoft.Insights/autoscaleSettings",
  "location": "East US",
  "properties": {
    "enabled": true,
    "targetResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
    "profiles": [
      {
        "name": "mainProfile",
        "capacity": {
          "minimum": "1",
          "maximum": "4",
          "default": "1"
        },
        "rules": [
          {
            "metricTrigger": {
              "metricName": "Percentage CPU",
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
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
              "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachineScaleSets/vmss1",
              "timeGrain": "PT1M",
              "statistic": "Average",
              "timeWindow": "PT10M",
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
      }
    ]
  }
}
```

| `Section` | Elementname | BESCHREIBUNG |
| --- | --- | --- |
| Einstellung | ID | Die Ressourcen-ID der Einstellung für die automatische Skalierung. Einstellungen für die automatische Skalierung sind eine Azure Resource Manager-Ressource. |
| Einstellung | name | Der Name der Einstellung für die automatische Skalierung. |
| Einstellung | location | Der Speicherort der Einstellung für die automatische Skalierung. Dieser Speicherort kann sich vom Speicherort der zu skalierenden Ressource unterscheiden. |
| properties | targetResourceUri | Die Ressourcen-ID der zu skalierenden Ressource. Sie können pro Ressource nur eine Einstellung für die automatische Skalierung verwenden. |
| properties | profiles | Eine Einstellung für die automatische Skalierung besteht aus einem oder mehreren Profilen. Bei jeder Ausführung des Moduls für die automatische Skalierung wird ein Profil ausgeführt. |
| Profil | name | Der Name des Profils. Sie können einen beliebigen Namen wählen, der Ihnen die Identifizierung des Profils erleichtert. |
| Profil | Capacity.maximum | Die maximal zulässige Kapazität. Hiermit wird sichergestellt, dass bei der automatischen Skalierung während der Ausführung dieses Profils für Ihre Ressourcen keine Skalierung über diesen Wert hinaus durchgeführt wird. |
| Profil | Capacity.minimum | Die zulässige Mindestkapazität. Hiermit wird sichergestellt, dass bei der automatischen Skalierung während der Ausführung dieses Profils für Ihre Ressourcen keine Skalierung durchgeführt wird, die zu einer niedrigeren Kapazität führt. |
| Profil | Capacity.default | Falls ein Problem beim Lesen der Ressourcenmetriken vorliegt (hier die CPU von „vmss1“) und die aktuelle Kapazität unterhalb der Standardkapazität liegt, wird von der automatischen Skalierung das horizontale Hochskalieren auf den Standardwert durchgeführt. Dadurch wird die Verfügbarkeit der Ressource sichergestellt. Wenn die aktuelle Kapazität bereits über der Standardkapazität liegt, wird die automatische Skalierung nicht horizontal herunterskaliert. |
| Profil | Regeln | Die automatische Skalierung skaliert automatisch zwischen der maximalen und minimalen Kapazität, indem die Regeln im Profil verwendet werden. Sie können in einem Profil mehrere Regeln nutzen. Normalerweise gibt es zwei Regeln: eine zum Ermitteln des Zeitpunkts für das horizontale Hochskalieren und die andere zum Ermitteln des Zeitpunkts für das horizontale Herunterskalieren. |
| Regel | metricTrigger | Definiert die Metrikbedingung der Regel. |
| metricTrigger | metricName | Der Name der Metrik. |
| metricTrigger |  metricResourceUri | Die Ressourcen-ID der Ressource, von der die Metrik ausgegeben wird. Meistens ist dies auch die zu skalierende Ressource. In einigen Fällen kann es sich auch um eine andere Ressource handeln. Sie können beispielsweise eine VM-Skalierungsgruppe basierend auf der Anzahl von Nachrichten in einer Speicherwarteschlange skalieren. |
| metricTrigger | timeGrain | Die Dauer der Stichprobenentnahme aus den Metriken. Beispiel: Bei **TimeGrain = „PT1M“** werden die Metriken jede Minute über die im Element „statistic“ angegebene Aggregationsmethode aggregiert. |
| metricTrigger | statistic | Dies ist die Aggregationsmethode innerhalb des timeGrain-Zeitraums. Beispiel: **statistic = „Average“** und **timeGrain = „PT1M“** bedeutet, dass die Metriken jede Minute anhand des Durchschnitts aggregiert werden. Diese Eigenschaft gibt vor, wie die Stichprobennahme für die Metrik erfolgt. |
| metricTrigger | timeWindow | Gibt an, wie weit auf Metriken zurückgegriffen werden soll. Beispiel: **timeWindow = „PT10M“** bedeutet, dass bei jeder Ausführung der automatischen Skalierung die Metriken der letzten zehn Minuten abgefragt werden. Dieses Zeitfenster ermöglicht, dass Ihre Metriken normalisiert werden, und eine Reaktion auf vorübergehende Spitzen wird vermieden. |
| metricTrigger | timeAggregation | Die Aggregationsmethode, die zum Aggregieren der Stichprobenmetriken verwendet wird. Beispiel: Mit **TimeAggregation = „Average“** werden die Stichprobenmetriken aggregiert, indem der Durchschnittswert herangezogen wird. Im vorangegangenen Fall wird der Mittelwert für die zehn 1-Minuten-Stichproben ermittelt. |
| Regel | scaleAction | Die Aktion, die nach dem Auslösen des metricTrigger-Elements der Regel durchgeführt wird. |
| scaleAction | direction | „Increase“ (Erhöhen) für horizontales Hochskalieren oder „Decrease“ (Verringern) für horizontales Herunterskalieren.|
| scaleAction | value | Der Wert für die Erhöhung bzw. Verringerung der Kapazität einer Ressource. |
| scaleAction | cooldown | Gibt an, wie lange nach einem Skalierungsvorgang gewartet werden soll, bevor erneut skaliert wird. Beispiel: Bei **cooldown = „PT10M“** versucht die automatische Skalierung zehn Minuten lang nicht, einen neuen Vorgang zu starten. Der Zweck des cooldown-Vorgangs besteht darin, dass sich die Metriken nach dem Hinzufügen bzw. Entfernen von Instanzen stabilisieren können. |

## <a name="autoscale-profiles"></a>Profile für die automatische Skalierung

Es gibt drei Arten von Profilen für die automatische Skalierung:

- **Reguläres Profil:** Dies ist das am häufigsten verwendete Profil. Wenn Sie Ihre Ressourcen nicht an einem Wochentag oder einem bestimmten Tag skalieren müssen, können Sie ein reguläres Profil verwenden. Dieses Profil kann dann mit Metrikregeln konfiguriert werden, die vorgeben, wann horizontal hoch- oder herunterskaliert werden soll. Sie sollten nur ein reguläres Profil definieren.

    Das Beispielprofil weiter oben in diesem Artikel ist ein Beispiel für ein reguläres Profil. Beachten Sie, dass es auch möglich ist, ein Profil für die Skalierung auf eine statische Instanzanzahl für Ihre Ressource festzulegen.

- **Profil für ein festgelegtes Datum:** Dieses Profil eignet sich für Sonderfälle. Angenommen, es findet ein wichtiges Ereignis am 26. Dezember 2017 (PST) statt. Sie möchten, dass die minimalen und maximalen Kapazitäten Ihrer Ressource an diesem Tag abweichen, aber dennoch eine Skalierung unter Verwendung derselben Metriken ausgeführt wird. In diesem Fall sollten Sie der Profilliste Ihrer Einstellung ein Profil für ein festgelegtes Datum hinzufügen. Das Profil ist nur für die Ausführung am Tag des Ereignisses konfiguriert. An allen anderen Tagen verwendet die automatische Skalierung das reguläre Profil.

    ``` JSON
    "profiles": [{
    "name": " regularProfile",
    "capacity": {
    ...
    },
    "rules": [{
    ...
    },
    {
    ...
    }]
    },
    {
    "name": "eventProfile",
    "capacity": {
    ...
    },
    "rules": [{
    ...
    }, {
    ...
    }],
    "fixedDate": {
        "timeZone": "Pacific Standard Time",
               "start": "2017-12-26T00:00:00",
               "end": "2017-12-26T23:59:00"
    }}
    ]
    ```
    
- **Wiederholungsprofil:** Bei diesem Profiltyp können Sie sicherstellen, dass es immer an einem bestimmten Wochentag verwendet wird. Wiederholungsprofile müssen nur einen Startzeitpunkt aufweisen. Dann werden sie bis zum Startzeitpunkt des nächsten Wiederholungsprofils oder Profils für ein festgelegtes Datum ausgeführt. Bei einer Einstellung für die automatische Skalierung mit nur einem Wiederholungsprofil wird dieses Profil auch dann ausgeführt, wenn unter derselben Einstellung ein reguläres Profil definiert ist. Die beiden folgenden Beispiele veranschaulichen, wie dieses Profil verwendet wird:

    **Beispiel 1: Wochentage im Vergleich zu Wochenenden**
    
    Angenommen, Sie möchten die maximale Kapazität für Wochenenden auf „4“ festgelegen. An Wochentagen soll diese jedoch „10“ betragen, da Sie eine höhere Auslastung erwarten. In diesem Fall enthält Ihre Einstellung zwei Wiederholungsprofile: eins für die Ausführung an Wochenenden und das andere für die Wochentage.
    Die Einstellung sieht wie folgt aus:

    ``` JSON
    "profiles": [
    {
    "name": "weekdayProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }}
    },
    {
    "name": "weekendProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Saturday"
            ],
            "hours": [
                0
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```

    Die vorherige Einstellung zeigt, dass jedes Wiederholungsprofil über einen Zeitplan verfügt. Dieser Zeitplan bestimmt, wann die Ausführung des Profils beginnt. Die Ausführung des Profils wird beendet, wenn die Ausführung eines anderen Profils ansteht.

    Beispielsweise ist in der vorherigen Einstellung „weekdayProfile“ als Startzeitpunkt montags, 12:00 Uhr, festgelegt. Das bedeutet, dass dieses Profil am Montag um 12:00 Uhr ausgeführt wird. Die Ausführung endet erst am Samstag um 12:00 Uhr, da für diesen Zeitpunkt der Ausführungsstart von „weekendProfile“ geplant ist.

    **Beispiel 2: Geschäftszeiten**
    
    Angenommen, Sie möchten einen Metrikschwellenwert für die Geschäftszeiten (9:00 Uhr bis 17:00 Uhr) und einen anderen für alle anderen Zeiten festlegen. Die Einstellung würde wie folgt aussehen:
    
    ``` JSON
    "profiles": [
    {
    "name": "businessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }],
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                9
            ],
            "minutes": [
                0
            ]
        }
    }
    },
    {
    "name": "nonBusinessHoursProfile",
    "capacity": {
        ...
    },
    "rules": [{
        ...
    }]
    "recurrence": {
        "frequency": "Week",
        "schedule": {
            "timeZone": "Pacific Standard Time",
            "days": [
                "Monday", “Tuesday”, “Wednesday”, “Thursday”, “Friday”
            ],
            "hours": [
                17
            ],
            "minutes": [
                0
            ]
        }
    }
    }]
    ```
    
    Die vorherige Einstellung zeigt, dass die Ausführung von „businessHoursProfile“ am Montag von 9:00 Uhr bis 17:00 Uhr erfolgt. Anschließend wird „nonBusinessHoursProfile“ ausgeführt. Die Ausführung von „nonBusinessHoursProfile“ wird erst am Dienstag um 9:00 Uhr beendet, wenn erneut das Profil „businessHoursProfile“ übernimmt. Dieser Vorgang wiederholt sich bis Freitag um 17:00 Uhr. Ab diesem Zeitpunkt wird „nonBusinessHoursProfile“ durchgehend bis Montag um 9:00 Uhr ausgeführt.
    
> [!Note]
> In der Benutzeroberfläche für die automatische Skalierung im Azure-Portal werden Endzeiten für Wiederholungsprofile erzwungen, und die Ausführung des Standardprofils für die Einstellung für die automatische Skalierung beginnt in den Zeiträumen zwischen den Wiederholungsprofilen.
    
## <a name="autoscale-evaluation"></a>Auswertung für die automatische Skalierung
Da Einstellungen für die automatische Skalierung über mehrere Profile verfügen können und jedes Profil mehrere Metrikregeln aufweisen kann, ist es wichtig zu verstehen, wie eine Einstellung für die automatische Skalierung ausgewertet wird. Jede Ausführung des Auftrags für die automatische Skalierung beginnt mit der Auswahl des anzuwendenden Profils. Anschließend wertet die Funktion für die automatische Skalierung die minimalen und maximalen Werte und alle Metrikregeln im Profil aus, um zu entscheiden, ob eine Skalierung erforderlich ist.

### <a name="which-profile-will-autoscale-pick"></a>Welches Profil wird von der automatischen Skalierung ausgewählt?

Die automatische Skalierung verwendet die folgende Sequenz, um das Profil auszuwählen:
1. Zuerst wird nach einem Profil für ein festgelegtes Datum gesucht, das für die sofortige Ausführung konfiguriert ist. Wenn eines vorhanden ist, wird es von der automatischen Skalierung ausgeführt. Wenn mehrere Profile für ein festgelegtes Datum vorhanden sind, die ausgeführt werden sollen, wählt die automatische Skalierung das erste Profil aus.
2. Falls keine Profile für ein festgelegtes Datum vorhanden sind, sucht die automatische Skalierung nach Wiederholungsprofilen. Wenn die Suche erfolgreich ist, wird das entsprechende Profil ausgeführt.
3. Sind keine Profile für ein festgelegtes Datum bzw. keine Wiederholungsprofile vorhanden, führt die automatische Skalierung das reguläre Profil aus.

### <a name="how-does-autoscale-evaluate-multiple-rules"></a>Wie werden von der automatischen Skalierung mehrere Regeln ausgewertet?

Nachdem die automatische Skalierung ermittelt hat, welches Profil ausgeführt werden soll, werden zuerst alle Regeln für das horizontale Hochskalieren ausgewertet, die im Profil vorhanden sind (dies sind Regeln mit **direction = „Increase“** ).

Wenn mindestens eine Regel für das horizontale Hochskalieren ausgelöst wird, berechnet die automatische Skalierung die neue Kapazität die in diesen Regeln jeweils per **scaleAction** bestimmt wird. Anschließend wird das horizontale Hochskalieren auf den Höchstwert dieser Kapazitäten durchgeführt, um die Dienstverfügbarkeit sicherzustellen.

Angenommen, es ist eine VM-Skalierungsgruppe mit einer aktuellen Kapazität von „10“ vorhanden. Es gibt zwei Regeln für das horizontale Hochskalieren: eine, die die Kapazität um 10 Prozent erhöht, und eine, die die Kapazität um 3 Zähler erhöht. Die erste Regel ergibt eine neue Kapazität von 11, und die zweite Regel führt zu einem Kapazitätswert von 13. Damit die Dienstverfügbarkeit sichergestellt ist, wählt die automatische Skalierung die Aktion, die zur höchsten Kapazität führt. Also wird die zweite Regel gewählt.

Falls keine Regeln für das horizontale Hochskalieren ausgelöst werden, wertet die automatische Skalierung alle Regeln für das horizontale Herunterskalieren aus (Regeln mit **direction = „Decrease“** ). Bei der automatischen Skalierung wird nur eine Aktion für das horizontale Herunterskalieren verwendet, wenn alle Regeln für das horizontale Herunterskalieren ausgelöst werden.

Bei der automatischen Skalierung wird die neue Kapazität berechnet, die mit **scaleAction** für die einzelnen Regeln ermittelt wird. Anschließend wird die Skalierungsaktion gewählt, die zum maximalen Wert für diese Kapazitäten führt, um die Dienstverfügbarkeit sicherzustellen.

Angenommen, es ist eine VM-Skalierungsgruppe mit einer aktuellen Kapazität von „10“ vorhanden. Es gibt zwei Regeln für das horizontale Herunterskalieren: eine, die die Kapazität um 50 Prozent verringert, und eine, die die Kapazität um 3 Zähler verringert. Die erste Regel ergibt eine neue Kapazität von 5, und die zweite Regel führt zu einem Kapazitätswert von 7. Damit die Dienstverfügbarkeit sichergestellt ist, wählt die automatische Skalierung die Aktion, die zur höchsten Kapazität führt. Also wird die zweite Regel gewählt.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur automatischen Skalierung finden Sie in den folgenden Ressourcen:

* [Übersicht über die automatische Skalierung](../../azure-monitor/platform/autoscale-overview.md)
* [Allgemeine Metriken für die automatische Skalierung in Azure Monitor](../../azure-monitor/platform/autoscale-common-metrics.md)
* [Best Practices für die automatische Skalierung in Azure Monitor](../../azure-monitor/platform/autoscale-best-practices.md)
* [Verwenden von automatischen Skalierungsvorgängen zum Senden von E-Mail- und Webhook-Warnbenachrichtigungen](../../azure-monitor/platform/autoscale-webhook-email.md)
* [REST-API für die automatische Skalierung](https://msdn.microsoft.com/library/dn931953.aspx)

