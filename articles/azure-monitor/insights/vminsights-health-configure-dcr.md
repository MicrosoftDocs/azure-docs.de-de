---
title: Konfigurieren der Überwachung für das Feature „Gastintegrität“ von Azure Monitor für VMs mit Datensammlungsregeln (Vorschau)
description: Es wird beschrieben, wie Sie für das Feature „Gastintegrität“ von Azure Monitor für VMs die Standardüberwachung im großen Stil ändern, indem Sie Resource Manager-Vorlagen verwenden.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2020
ms.openlocfilehash: 55e5a587a0ad02fa1f8993027b46162a14a58832
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96448239"
---
# <a name="configure-monitoring-in-azure-monitor-for-vms-guest-health-using-data-collection-rules-preview"></a>Konfigurieren der Überwachung für das Feature „Gastintegrität“ von Azure Monitor für VMs mit Datensammlungsregeln (Vorschau)
Mit dem [Feature „Gastintegrität“ von Azure Monitor für VMs](vminsights-health-overview.md) können Sie Informationen zur Integrität eines virtuellen Computers anzeigen. Dies wird anhand von verschiedenen Leistungsmessungen definiert, für die in regelmäßigen Abständen Stichproben genommen werden. In diesem Artikel wird beschrieben, wie Sie die Standardüberwachung übergreifend für mehrere virtuelle Computer mit Datensammlungsregeln ändern können.


## <a name="monitors"></a>Monitore
Der Integritätsstatus eines virtuellen Computers wird anhand des [Integritätsrollups](vminsights-health-overview.md#health-rollup-policy) seiner einzelnen Monitore bestimmt. Die beiden Monitorarten für das Feature „Gastintegrität“ von Azure Monitor für VMs sind unten in der Tabelle angegeben.

| Überwachen | BESCHREIBUNG |
|:---|:---|
| Einheitenmonitor | Misst einen bestimmten Aspekt einer Ressource oder Anwendung. Dabei kann es sich um die Überprüfung eines Leistungszählers zum Ermitteln der Leistung der Ressource oder ihrer Verfügbarkeit handeln. |
| Aggregatmonitor | Eine Gruppierung mehrerer Monitore, um einen zusammengefassten Integritätsstatus angeben zu können. Ein Aggregatmonitor kann einen oder mehrere Einheitenmonitore sowie weitere Aggregatmonitore enthalten. |

Es ist nicht möglich, die Monitore, die vom Feature „Gastintegrität“ von Azure Monitor für VMs genutzt werden, sowie die zugehörige Konfiguration direkt zu ändern. Sie können aber [Außerkraftsetzungen](#overrides) erstellen, mit denen das Verhalten der Standardkonfiguration geändert wird. Außerkraftsetzungen werden in Datensammlungsregeln definiert. Sie können mehrere Datensammlungsregeln erstellen, die jeweils mehrere Außerkraftsetzungen enthalten, um Ihre erforderliche Überwachungskonfiguration zu erzielen.

## <a name="monitor-properties"></a>Monitoreigenschaften
In der folgenden Tabelle sind die Eigenschaften beschrieben, die für jeden Monitor konfiguriert werden können.

| Eigenschaft | Monitore | Beschreibung |
|:---|:---|:---|
| Aktiviert | Aggregat<br>Einheit | Bei „true“ wird der Status des Monitors berechnet, und das Ergebnis geht in den Integritätswert des virtuellen Computers ein. Eine Warnung kann ausgelöst werden, sofern diese Funktion aktiviert ist. |
| Warnungen | Aggregat<br>Einheit | Bei „true“ wird eine Warnung für den Monitor ausgelöst, wenn der Status nicht mehr „Fehlerfrei“ lautet. Bei „false“ geht der Status des Monitors trotzdem in den Integritätswert des virtuellen Computers ein und kann somit zur Auslösung einer Warnung beitragen. |
| Warnung | Einheit | Kriterien für den Status „Warnung“. Bei „none“ kann der Monitor den Status „Warnung“ niemals erreichen. |
| Kritisch | Einheit | Kriterien für den Status „Kritisch“. Bei „none“ kann der Monitor den Status „Kritisch“ niemals erreichen. |
| Auswertungshäufigkeit | Einheit | Die Häufigkeit, mit der der Integritätsstatus ausgewertet wird. |
| Rückblick | Einheit | Größe des Rückblickfensters in Sekunden. Eine ausführliche Beschreibung finden Sie unter [Element „monitorConfiguration“](#monitorconfiguration-element). |
| Auswertungstyp | Einheit | Definiert, welcher Wert des Stichprobensatzes verwendet werden soll. Eine ausführliche Beschreibung finden Sie unter [Element „monitorConfiguration“](#monitorconfiguration-element). |
| Mindestwert für Stichprobe | Einheit | Mindestanzahl von Werten, die zum Berechnen des Werts verwendet werden sollen. |
| Höchstwert für Stichprobe | Einheit | Maximale Anzahl von Werten, die zum Berechnen des Werts verwendet werden sollen. |


## <a name="default-configuration"></a>Standardkonfiguration
In der folgenden Tabelle ist die Standardkonfiguration für jeden Monitor angegeben. Diese Standardkonfiguration kann nicht direkt geändert werden, aber Sie können [Außerkraftsetzungen](#overrides) definieren, mit denen die Monitorkonfiguration für bestimmte virtuelle Computer geändert wird.


| Überwachen | Aktiviert | Warnungen | Warnung | Kritisch | Auswertungshäufigkeit | Rückblick | Auswertungstyp | Mindestwert für Stichprobe | Höchstwert für Stichprobe |
|:---|:---|:---|:---|:---|:---|:---|:---|:---|:---|
| CPU-Auslastung  | True | False | Keine | \> 90 %    | 60 Sekunden | 240 Sekunden | Min | 2 | 3 |
| Verfügbarer Arbeitsspeicher | True | False | Keine | \< 100 MB | 60 Sekunden | 240 Sekunden | Max | 2 | 3 |
| Dateisystem      | True | False | Keine | \< 100 MB | 60 Sekunden | 120 Sekunden | Max | 1 | 1 |


## <a name="overrides"></a>Überschreibt
Mit einer *Außerkraftsetzung* wird mindestens eine Eigenschaft eines Monitors geändert. Beispielsweise kann mit einer Außerkraftsetzung ein Monitor deaktiviert werden, der standardmäßig aktiviert ist. Weitere Möglichkeiten sind das Definieren von Warnungskriterien oder das Ändern des Schwellenwerts für den Status „Kritisch“ für den Monitor. 

Außerkraftsetzungen werden in einer [Datensammlungsregel](../platform/data-collection-rule-overview.md) definiert. Sie können mehrere Datensammlungsregeln mit verschiedenen Gruppen von Außerkraftsetzungen erstellen und diese auf mehrere virtuelle Computer anwenden. Sie wenden eine Datensammlungsregel auf einen virtuellen Computer an, indem Sie eine Zuordnung erstellen. Dies ist unter [Konfigurieren der Datensammlung für den Azure Monitor-Agent (Vorschau)](../platform/data-collection-rule-azure-monitor-agent.md#data-collection-rule-associations) beschrieben.


## <a name="multiple-overrides"></a>Mehrere Außerkraftsetzungen

Ein Monitor kann über mehrere Außerkraftsetzungen verfügen. Wenn für die Außerkraftsetzungen unterschiedliche Eigenschaften definiert werden, ist die sich ergebende Konfiguration eine Kombination all dieser Außerkraftsetzungen.

Für den Monitor `memory|available` wird beispielsweise kein Warnungsschwellenwert angegeben, und Warnungen werden nicht standardmäßig aktiviert. Angenommen, für diesen Monitor werden die folgenden Außerkraftsetzungen angewendet:

- Mit Außerkraftsetzung 1 wird der Eigenschaftswert `alertConfiguration.isEnabled` auf `true` festgelegt.
- Mit Außerkraftsetzung 2 wird `monitorConfiguration.warningCondition` mit einer Schwellenwertbedingung von `< 250` definiert.

Die sich ergebende Konfiguration führt zu einem Monitor, für den der Integritätsstatus „Warnung“ aktiviert und eine Warnung mit Schweregrad 2 erstellt wird, wenn weniger als 250 MB Arbeitsspeicher verfügbar sind. Darüber hinaus wird der Integritätsstatus „Kritisch“ aktiviert und eine Warnung mit Schweregrad 1 erstellt (bzw. die Warnung von Schweregrad 2 in 1 geändert, falls sie bereits vorhanden ist), wenn weniger als 100 MB Arbeitsspeicher verfügbar sind.

Falls zwei Außerkraftsetzungen die gleiche Eigenschaft unter demselben Monitor definieren, hat einer der Werte Vorrang. Außerkraftsetzungen werden anhand ihres [Bereichs (Englisch: scope)](#scopes-element) (von allgemein bis spezifisch) angewendet. Dies bedeutet, dass für die spezifischsten Außerkraftsetzungen die größte Wahrscheinlichkeit besteht, dass sie angewendet werden. Die jeweilige Reihenfolge lautet wie folgt:

1. Global 
2. Subscription
3. Resource group
4. Virtuellen Computer 

Falls in mehreren Außerkraftsetzungen auf der gleichen Bereichsebene die gleiche Eigenschaft für den gleichen Monitor definiert wird, werden sie in der Reihenfolge angewendet, in der sie in der Datensammlungsregel vorkommen. Befinden sich die Außerkraftsetzungen in unterschiedlichen Datensammlungsregeln, werden sie in der alphabetischen Reihenfolge der Ressourcen-IDs der Datensammlungsregel angewendet.


## <a name="data-collection-rule-configuration"></a>Konfiguration für Datensammlungsregeln
Die JSON-Elemente in der Datensammlungsregel, mit denen die Außerkraftsetzungen definiert werden, sind in den folgenden Abschnitten beschrieben. Ein umfassendes Beispiel finden Sie unter [Beispielregel für die Datensammlung](#sample-data-collection-rule).

## <a name="extensions-structure"></a>Erweiterungsstruktur
Das Feature „Gastintegrität“ wird als Erweiterung des Azure Monitor-Agents implementiert, sodass Außerkraftsetzungen im Element `extensions` der Datensammlungsregel definiert sind. 

```json
"extensions": [
    {
        "name": "Microsoft-VMInsights-Health",
        "streams": [
            "Microsoft-HealthStateChange"
        ],
        "extensionName": "HealthExtension",
        "extensionSettings": {   }
    }
]
```

| Element | Erforderlich | Beschreibung |
|:---|:---|:---|
| `name` | Ja | Benutzerdefinierte Zeichenfolge für die Erweiterung. |
| `streams` | Ja | Liste mit Streams, an die Daten zur Gastintegrität gesendet werden. In dieser Liste muss **Microsoft-HealthStateChange** enthalten sein.  |
| `extensionName` | Ja | Name der Erweiterung. Er muss **HealthExtension** lauten. |
| `extensionSettings` | Ja | Array mit `healthRuleOverride`-Elementen, die auf die Standardkonfiguration angewendet werden sollen. |


## <a name="extensionsettings-element"></a>Element „extensionSettings“
Enthält Einstellungen für die Erweiterung.

```json
"extensionSettings": {
    "schemaVersion": "1.0",
    "contentVersion": "",
    "healthRuleOverrides": [ ]
}
```

| Element | Erforderlich | Beschreibung |
|:---|:---|:---|
| `schemaVersion` | Ja | Von Microsoft definierte Zeichenfolge, mit der das erwartete Schema des Elements dargestellt wird. Muss derzeit auf „1.0“ festgelegt sein. |
| `contentVersion` | Nein | Die vom Benutzer definierte Zeichenfolge, mit der bei Bedarf unterschiedliche Versionen der Integritätskonfiguration nachverfolgt werden können. |
| `healthRuleOverrides` | Ja | Array mit `healthRuleOverride`-Elementen, die auf die Standardkonfiguration angewendet werden sollen. |

## <a name="healthrulesoverrides-element"></a>Element „healthRulesOverrides“
Enthält ein oder mehrere Elemente vom Typ `healthRuleOverride`, mit denen jeweils eine Außerkraftsetzung definiert wird.

```json
"healthRuleOverrides": [
    {
        "scopes": [ ],
        "monitors": [ ],
        "monitorConfiguration": { },
        "alertConfiguration": {  },
        "isEnabled": true|false
    }
]
```

| Element | Erforderlich | Beschreibung |
|:---|:---|:---|
| `scopes` | Ja | Liste mit einem oder mehreren Bereichen, mit denen die virtuellen Computer angegeben werden, für die diese Außerkraftsetzung gilt. Auch wenn die Datensammlungsregel einem virtuellen Computer zugeordnet ist, muss sich der virtuelle Computer in einem passenden Bereich befinden, damit die Außerkraftsetzung angewendet werden kann. |
| `monitors` | Ja | Eine Liste mit einer oder mehreren Zeichenfolgen, mit denen definiert wird, für welche Monitore diese Außerkraftsetzung gilt.  |
| `monitorConfiguration` | Nein | Konfiguration für den Monitor, einschließlich der Integritätsstatus und ihrer Berechnung. |
| `alertConfiguration` | Nein | Die Warnungskonfiguration für den Monitor. |
| `isEnabled` | Nein | Steuert, ob der Monitor aktiviert ist. Der deaktivierte Monitor wird in den speziellen Integritätsstatus *Deaktiviert* versetzt. „Deaktiviert“ wird so lange angezeigt, bis er wieder aktiviert wird. Falls hier nichts angegeben wird, erbt der Monitor seinen Status vom übergeordneten Monitor der Hierarchie. |


## <a name="scopes-element"></a>Element „scopes“
Jede Außerkraftsetzung verfügt über mindestens einen Bereich (scope), mit dem definiert wird, auf welche virtuellen Computer die Außerkraftsetzung angewendet werden soll. Bei dem Bereich kann es sich um ein Abonnement, eine Ressourcengruppe oder einen einzelnen virtuellen Computer handeln. Auch wenn sich die Außerkraftsetzung in einer Datensammlungsregel befindet, die einem bestimmten virtuellen Computer zugeordnet ist, wird sie nur auf diesen virtuellen Computer angewendet, wenn dieser in einem der Bereiche der Außerkraftsetzung enthalten ist. Dies ermöglicht es Ihnen, eine geringere Zahl von Datensammlungsregeln grob auf eine Gruppe mit VMs anzuwenden und die Zuweisung für jede Außerkraftsetzung dann direkt in der Datensammlungsregel präzise zu steuern. Es kann ratsam sein, kleine Gruppen mit Datensammlungsregeln zu erstellen und diese per Richtlinie einer Gruppe mit virtuellen Computern zuzuordnen, während Außerkraftsetzungen für Integritätsmonitore für die einzelnen Teilmengen dieser virtuellen Computer mit dem Element „scopes“ angegeben werden.

Die folgende Tabelle enthält Beispiele für verschiedene Bereiche.

| `Scope` | Beispiel |
|:---|:---|
| Einzelner virtueller Computer | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-name/providers/Microsoft.Compute/virutalMachines/my-vm` |
| Alle virtuellen Computer einer Ressourcengruppe | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rg-name` |
| Alle virtuellen Computer in einem Abonnement | `/subscriptions/00000000-0000-0000-0000-000000000000/` |
| Alle virtuellen Computer, denen die Datensammlungsregel zugeordnet ist | `*` |


## <a name="monitors-element"></a>Element „monitors“
Eine Liste mit einer oder mehreren Zeichenfolgen, mit denen definiert wird, für welche Monitore der Integritätshierarchie die Außerkraftsetzung gilt. Jedes Element kann ein Monitor- oder Typname sein, der für einen oder mehrere Monitore steht, für die diese Außerkraftsetzung gelten soll. 

```json
"monitors": [
    "<monitor name>"
 ],
```



In der folgenden Tabelle sind die Monitornamen aufgeführt, die derzeit verfügbar sind.

| Typname | Name | Beschreibung |
|:---|:---|:---|
| root | root | Monitor der obersten Ebene für die Integrität des virtuellen Computers | |
| cpu-utilization | cpu-utilization | Monitor für CPU-Auslastung | |
| logical-disks | logical-disks | Aggregatmonitor für den Integritätsstatus aller überwachten Datenträger auf dem virtuellen Windows-Computer | |
| logical-disks\|* | logical-disks\|C:<br>logical-disks\|D: | Aggregatmonitor zur Nachverfolgung der Integrität eines bestimmten Datenträgers auf einem virtuellen Windows-Computer | 
| logical-disks\|*\|free-space | logical-disks\|C:\|free-space<br>logical-disks\|D:\|free-space | Monitor für freien Speicherplatz auf dem Datenträger des virtuellen Windows-Computers |
| filesystems | filesystems | Aggregatmonitor für die Integrität aller Dateisysteme auf einem virtuellen Linux-Computer |
| filesystems\|* | filesystems\|/<br>filesystems\|/mnt | Aggregatmonitor zur Nachverfolgung der Integrität des Dateisystems eines virtuellen Linux-Computers | filesystems|/var/log |
| filesystems\|*\|free-space | filesystems\|/\|free-space<br>filesystems\|/mnt\|free-space | Monitor für freien Speicherplatz im Dateisystem eines virtuellen Linux-Computers | 
| Arbeitsspeicher | Arbeitsspeicher | Aggregatmonitor für die Integrität des Arbeitsspeichers eines virtuellen Computers | |
| memory\|available| memory\|available | Monitor zur Nachverfolgung des verfügbaren Arbeitsspeichers auf dem virtuellen Computer | |


## <a name="alertconfiguration-element"></a>Element „alertConfiguration“
Gibt an, ob vom Monitor eine Warnung erstellt werden soll.

```json
"alertConfiguration": {
    "isEnabled": true|false
}
```

| Element | Obligatorisch. | BESCHREIBUNG | 
|:---|:---|:---|
| `isEnabled` | Nein | Bei „true“ generiert der Monitor eine Warnung, wenn in den Status „Kritisch“ oder „Warnung“ gewechselt wird, und die Warnung wird als behoben gekennzeichnet, nachdem wieder der Status „Fehlerfrei“ angezeigt wird. Bei „false“ oder keiner Angabe wird keine Warnung generiert.  |


## <a name="monitorconfiguration-element"></a>Element „monitorConfiguration“
Gilt nur für Einheitenmonitore. Definiert die Konfiguration für den Monitor, einschließlich der Integritätsstatus und ihrer Berechnung.

Mit Parametern wird der Algorithmus zum Berechnen des Metrikwerts definiert, der mit den Schwellenwerten verglichen wird. Anstatt basierend auf nur einer Datenstichprobe der zugrunde liegenden Metrik zu handeln, wertet der Monitor mehrere Metrikstichproben aus, die innerhalb des Fensters vom Auswertungszeitpunkt bis zum Zeitpunkt `lookbackSec` empfangen wurden. Alle Stichproben, die innerhalb dieses Zeitraums empfangen werden, werden berücksichtigt. Wenn die Anzahl von Stichproben größer als `maxSamples` ist, werden Stichproben ignoriert, die älter als `maxSamples` sind. 

Falls das Rückblickintervall weniger Beispiele als `minSamples` enthält, wechselt der Monitor in den Status *Unbekannt*. Hiermit wird angegeben, dass nicht genügend Daten vorhanden sind, um eine fundierte Entscheidung zur Integrität der zugrunde liegenden Metriken treffen zu können. Falls eine größere Anzahl von Stichproben als `minSamples` verfügbar ist, wird für den Satz eine Aggregationsfunktion ausgeführt. Diese wird mit dem Parameter „evaluationType“ angegeben, um einen einzelnen Wert zu berechnen.


```json
"monitorConfiguration": {
        "evaluationType" : "<type-of-evaluation>",
        "lookbackSecs": <lookback-number-of-seconds>,
        "evaluationFrequencySecs": <evaluation-frequency-number-of-seconds>,
        "minSamples": <minimum-samples>,
        "maxSamples": <maximum-samples>,
        "warningCondition": {  },
        "criticalCondition": {  }
    }
}
```

| Element | Obligatorisch. | BESCHREIBUNG | 
|:---|:---|:---|
| `evaluationFrequencySecs` | Nein | Definiert die Häufigkeit für die Auswertung des Integritätsstatus. Jeder Monitor wird ausgewertet, wenn der Agent gestartet wird. Anschließend wird die Auswertung nach einem regelmäßigen Intervall durchgeführt, das mit diesem Parameter definiert wird. |
| `lookbackSecs`   | Nein | Größe des Rückblickfensters in Sekunden. |
| `evaluationType` | Nein | `min`: Verwendung des Mindestwerts des gesamten Stichprobensatzes<br>`max`: Verwendung des Höchstwerts des gesamten Stichprobensatzes<br>`avg`: Verwendung des Mittelwerts der Stichprobensatzwerte<br>`all`: Vergleich jedes einzelnen Werts des Satzes mit den Schwellenwerten. Der Status des Monitors wird nur dann gewechselt, wenn alle Stichproben des Satzes die Schwellenwertbedingung erfüllen. |
| `minSamples`     | Nein | Mindestanzahl von Werten, die zum Berechnen des Werts verwendet werden sollen. |
| `maxSamples`     | Nein | Maximale Anzahl von Werten, die zum Berechnen des Werts verwendet werden sollen. |
| `warningCondition`  | Nein | Schwellenwert und Vergleichslogik für die Bedingung „Warnung“. |
| `criticalCondition` | Nein | Schwellenwert und Vergleichslogik für die Bedingung „Kritisch“. |


## <a name="warningcondition-element"></a>Element „warningCondition“
Definiert den Schwellenwert und die Vergleichslogik für die Bedingung „Warnung“. Wenn dieses Element nicht enthalten ist, wird für den Monitor niemals in den Integritätsstatus „Warnung“ gewechselt.

```json
"warningCondition": {
    "isEnabled": true|false,
    "operator": "<comparison-operator>",
    "threshold": <threshold-value>
},
```

| Eigenschaft | Obligatorisch. | BESCHREIBUNG | 
|:---|:---|:---|
| `isEnabled` | Nein | Gibt an, ob die Bedingung aktiviert ist. Bei **false** ist die Bedingung deaktiviert, obwohl ggf. Eigenschaften für den Schwellenwert und den Operator festgelegt sind. |
| `threshold` | Nein | Definiert den Schwellenwert zum Vergleichen des ausgewerteten Werts. |
| `operator`  | Nein | Definiert den Vergleichsoperator für die Verwendung im Schwellenwertausdruck. Mögliche Werte: >, <, >=, <=, ==. |


## <a name="criticalcondition-element"></a>Element „criticalCondition“
Definiert den Schwellenwert und die Vergleichslogik für die Bedingung „Kritisch“. Wenn dieses Element nicht enthalten ist, wird für den Monitor niemals in den Integritätsstatus „Kritisch“ gewechselt.

```json
"criticalCondition": {
    "isEnabled": true|false,
    "operator": "<comparison-operator>",
    "threshold": <threshold-value>
},
```

| Eigenschaft | Obligatorisch. | BESCHREIBUNG | 
|:---|:---|:---|
| `isEnabled` | Nein | Gibt an, ob die Bedingung aktiviert ist. Bei **false** ist die Bedingung deaktiviert, obwohl ggf. Eigenschaften für den Schwellenwert und den Operator festgelegt sind. |
| `threshold` | Nein | Definiert den Schwellenwert zum Vergleichen des ausgewerteten Werts. |
| `operator`  | Nein | Definiert den Vergleichsoperator für die Verwendung im Schwellenwertausdruck. Mögliche Werte: >, <, >=, <=, ==. |

## <a name="sample-data-collection-rule"></a>Beispielregel für die Datensammlung
Eine Beispielregel für die Datensammlung, mit der die Gastüberwachung aktiviert wird, finden Sie unter [Aktivieren eines virtuellen Computers per Resource Manager-Vorlage](vminsights-health-enable.md#enable-a-virtual-machine-using-resource-manager-template).


## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über [Datensammlungsregeln](../platform/data-collection-rule-overview.md).