---
title: Datensammlungsregeln in Azure Monitor (Vorschau)
description: Enthält eine Übersicht über die Datensammlungsregeln in Azure Monitor, z. B. zu Inhalt und Struktur und zur Erstellung und Nutzung.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/19/2020
ms.openlocfilehash: 048068a74151bb986392b5cb27787385fc0f5363
ms.sourcegitcommit: 5ae2f32951474ae9e46c0d46f104eda95f7c5a06
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95315531"
---
# <a name="data-collection-rules-in-azure-monitor-preview"></a>Datensammlungsregeln in Azure Monitor (Vorschau)
Mit Datensammlungsregeln werden die in Azure Monitor eingehenden Daten definiert, und es wird angegeben, wohin die Daten gesendet bzw. wo sie gespeichert werden sollen. Dieser Artikel enthält eine Übersicht über die Datensammlungsregeln, z. B. zu Inhalt und Struktur und zur Erstellung und Nutzung.

## <a name="input-sources"></a>Eingabequellen
Datensammlungsregeln unterstützen derzeit die folgenden Eingabequellen:

- Virtueller Azure-Computer mit dem Azure Monitor-Agent. Weitere Informationen finden Sie unter [Konfigurieren der Datensammlung für den Azure Monitor-Agent (Vorschau)](data-collection-rule-azure-monitor-agent.md).



## <a name="components-of-a-data-collection-rule"></a>Komponenten einer Datensammlungsregel
Eine Datensammlungsregel enthält die unten angegebenen Komponenten.

| Komponente | BESCHREIBUNG |
|:---|:---|
| Datenquellen | Eindeutige Quelle für Überwachungsdaten mit einem eigenen Format und einer eigenen Methode zum Verfügbarmachen der Daten. Beispiele für eine Datenquelle sind Windows-Ereignisprotokoll, Leistungsindikatoren und Syslog. Jede Datenquelle hat einen bestimmten Datenquellentyp (wie unten beschrieben). |
| Datenströme | Eindeutiges Handle zur Beschreibung einer Gruppe von Datenquellen, die unter Verwendung eines gemeinsamen Typs transformiert und schematisiert werden. Für jede Datenquelle wird mindestens ein Datenstrom benötigt, und ein Datenstrom kann von mehreren Datenquellen verwendet werden. Für alle Datenquellen eines Datenstroms wird ein gemeinsames Schema genutzt. Verwenden Sie beispielsweise mehrere Datenströme, wenn Sie eine bestimmte Datenquelle an mehrere Tabellen in demselben Log Analytics-Arbeitsbereich senden möchten. |
| Destinations | Eine Gruppe von Zielen, an die die Daten gesendet werden sollen. Beispiele hierfür sind Log Analytics-Arbeitsbereich, Azure Monitor-Metriken und Azure Event Hubs. | 
| Datenflüsse | Definition, welche Datenströme an welche Ziele gesendet werden sollen. | 

Im folgenden Diagramm sind die Komponenten einer Regel für die Datensammlung sowie deren Beziehungen dargestellt.

[![Diagramm: Datensammlungsregel](media/data-collection-rule/data-collection-rule-components.png)](media/data-collection-rule/data-collection-rule-components.png#lightbox)

### <a name="data-source-types"></a>Datenquellentypen
Jede Datenquelle hat einen Datenquellentyp. Mit jedem Typ wird eine eindeutige Gruppe von Eigenschaften definiert, die für jede Datenquelle angegeben werden müssen. Die derzeit verfügbaren Datenquellentypen sind in der folgenden Tabelle angegeben.

| Datenquellentyp | BESCHREIBUNG | 
|:---|:---|
| Erweiterung | Auf VM-Erweiterung basierende Datenquelle |
| performanceCounters | Leistungsindikatoren für Windows und Linux |
| syslog | Syslog-Ereignisse unter Linux |
| windowsEventLogs | Windows-Ereignisprotokoll |


## <a name="limits"></a>Grenzwerte
Informationen zu Grenzwerten, die für die einzelnen Datensammlungsregeln gelten, finden Sie unter [Azure Monitor-Diensteinschränkungen](../service-limits.md#data-collection-rules).


## <a name="create-a-dcr"></a>Erstellen einer Datensammlungsregel
Es gibt derzeit zwei verfügbare Methoden zum Erstellen einer Datensammlungsregel:

- [Verwenden Sie das Azure-Portal](data-collection-rule-azure-monitor-agent.md), um eine Datensammlungsregel zu erstellen und einem oder mehreren virtuellen Computern zuzuordnen.
- Bearbeiten Sie die Datensammlungsregel direkt in JSON, und [übermitteln Sie sie mit der REST-API](/rest/api/monitor/datacollectionrules).

## <a name="sample-data-collection-rule"></a>Beispielregel für die Datensammlung
Die unten angegebene Beispielregel für die Datensammlung gilt für virtuelle Computer mit Azure-Verwaltungs-Agent und verfügt über die folgenden Details:

- Leistungsdaten
  - Sammelt alle 15 Sekunden spezifische Indikatordaten für Prozessor, Arbeitsspeicher, logischen Datenträger und physischen Datenträger und führt einmal pro Minute ein Upload durch.
  - Sammelt alle 30 Sekunden spezifische Prozessindikatoren und führt alle fünf Minuten ein Upload durch.
- Windows-Ereignisse
  - Sammelt Windows-Sicherheitsereignisse und führt einmal pro Minute ein Upload durch.
  - Sammelt Windows-Anwendungs- und -Systemereignisse und führt alle fünf Minuten ein Upload durch.
- syslog
  - Sammelt Ereignisse vom Typ „Debuggen“, „Kritisch“ und „Notfall“ für die Cron-Komponente.
  - Sammelt Ereignisse vom Typ „Warnung“, „Kritisch“ und „Notfall“ für die Syslog-Komponente.
- Destinations
  - Sendet alle Daten an einen Log Analytics-Arbeitsbereich mit dem Namen „centralWorkspace“.

```json
{
    "location": "eastus",
    "properties": {
      "dataSources": {
        "performanceCounters": [
          {
            "name": "cloudTeamCoreCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT1M",
            "samplingFrequencyInSeconds": 15,
            "counterSpecifiers": [
              "\\Processor(_Total)\\% Processor Time",
              "\\Memory\\Committed Bytes",
              "\\LogicalDisk(_Total)\\Free Megabytes",
              "\\PhysicalDisk(_Total)\\Avg. Disk Queue Length"
            ]
          },
          {
            "name": "appTeamExtraCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT5M",
            "samplingFrequencyInSeconds": 30,
            "counterSpecifiers": [
              "\\Process(_Total)\\Thread Count"
            ]
          }
        ],
        "windowsEventLogs": [
          {
            "name": "cloudSecurityTeamEvents",
            "streams": [
              "Microsoft-WindowsEvent"
            ],
            "scheduledTransferPeriod": "PT1M",
            "xPathQueries": [
              "Security!*"
            ]
          },
          {
            "name": "appTeam1AppEvents",
            "streams": [
              "Microsoft-WindowsEvent"
            ],
            "scheduledTransferPeriod": "PT5M",
            "xPathQueries": [
              "System!*[System[(Level = 1 or Level = 2 or Level = 3)]]",
              "Application!*[System[(Level = 1 or Level = 2 or Level = 3)]]"
            ]
          }
        ],
        "syslog": [
          {
            "name": "cronSyslog",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "cron"
            ],
            "logLevels": [
              "Debug",
              "Critical",
              "Emergency"
            ]
          },
          {
            "name": "syslogBase",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "syslog"
            ],
            "logLevels": [
              "Alert",
              "Critical",
              "Emergency"
            ]
          }
        ]
      },
      "destinations": {
        "logAnalytics": [
          {
            "workspaceResourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.OperationalInsights/workspaces/my-workspace",
            "name": "centralWorkspace"
          }
        ]
      },
      "dataFlows": [
        {
          "streams": [
            "Microsoft-Perf",
            "Microsoft-Syslog",
            "Microsoft-WindowsEvent"
          ],
          "destinations": [
            "centralWorkspace"
          ]
        }
      ]
    }
  }
```


## <a name="next-steps"></a>Nächste Schritte

- [Erstellen Sie eine Datensammlungsregel](data-collection-rule-azure-monitor-agent.md) und eine entsprechende Zuordnung von einem virtuellen Computer aus, indem Sie den Azure Monitor-Agent verwenden.