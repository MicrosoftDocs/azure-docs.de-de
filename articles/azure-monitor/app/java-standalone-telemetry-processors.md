---
title: 'Telemetrieprozessoren (Vorschau): Azure Monitor Application Insights Java'
description: Telemetrieprozessoren für Azure Monitor Application Insights Java
ms.topic: conceptual
ms.date: 10/29/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 316243e130345ce1b009e065cf9d8250c501ee84
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96601032"
---
# <a name="telemetry-processors-preview-for-azure-monitor-application-insights-java"></a>Telemetrieprozessoren (Vorschau) für Azure Monitor Application Insights Java

> [!NOTE]
> Dieses Feature befindet sich noch in der Vorschauphase.

Der Java 3.0-Agent für Application Insights verfügt jetzt über Funktionen, um Telemetriedaten zu verarbeiten, bevor die Daten exportiert werden.

### <a name="some-use-cases"></a>Einige Anwendungsfälle:
 * Maskieren vertraulicher Daten
 * Bedingtes Hinzufügen benutzerdefinierter Dimensionen
 * Aktualisieren des Telemetrienamens für Aggregation und Anzeige

### <a name="supported-processors"></a>Unterstützte Prozessoren:
 * Attributprozessor
 * Span-Prozessor

## <a name="to-get-started"></a>Einführung

Erstellen Sie eine Konfigurationsdatei mit dem Namen `applicationinsights.json`, und legen Sie sie im gleichen Verzeichnis wie `applicationinsights-agent-***.jar` mit folgender Vorlage ab.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        ...
      },
      {
        "type": "attribute",
        ...
      },
      {
        "type": "span",
        ...
      }
    ]
  }
}
```

## <a name="includeexclude-spans"></a>Einschließen/Ausschließen von Spans

Der Attributprozessor und der Span-Prozessor bieten die Option, einen Satz von Eigenschaften einer Span zum Vergleich bereitzustellen, um zu bestimmen, ob die Span in den Prozessor eingeschlossen oder davon ausgeschlossen werden sollte. Um diese Option zu konfigurieren, ist unter `include` und/oder `exclude` mindestens ein `matchType` und ein Exemplar von `spanNames` oder `attributes` erforderlich. Die Einschluss/Ausschluss-Konfiguration wird unterstützt, damit mehrere angegebene Bedingungen vorliegen. Alle angegebenen Bedingungen müssen als „true“ ausgewertet werden, damit eine Entsprechung auftritt. 

**Pflichtfeld**: 
* `matchType` steuert, wie Elemente in den `spanNames`- und `attributes`-Arrays interpretiert werden. Mögliche Werte sind `regexp` oder `strict`. 

**Optionale Felder**: 
* `spanNames` muss mindestens einem der Elemente entsprechen. 
* `attributes` gibt die Liste der Attribute für den Vergleich an. Voraussetzung einer Übereinstimmung ist, dass alle diese Attribute genau zutreffen.

> [!NOTE]
> Wenn sowohl `include` als auch `exclude` angegeben wird, werden die `include`-Eigenschaften vor den `exclude`-Eigenschaften geprüft.

#### <a name="sample-usage"></a>Beispielverwendung

Das folgende Beispiel zeigt, wie Sie den Satz von Span-Eigenschaften festlegen, um anzugeben, auf welche Spans dieser Prozessor angewendet werden soll. Mit `include` werden Eigenschaften eingeschlossen, und `exclude` filtert Eigenschaften heraus, die nicht verarbeitet werden sollen.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "spanNames": [
            "svcA",
            "svcB"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "attributes": [
            {
              "key": "redact_trace",
              "value": "false"
            }
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          },
          {
            "key": "duplicate_key",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

Mit der obigen Konfiguration stimmen die folgenden Spans mit den Eigenschaften überein und Prozessoraktionen werden angewandt:

* Span1 Name: 'svcb ' Attribute: {env: production, test_request: 123, credit_card: 1234, redact_trace: "false"}

* Span2 Name: 'svca' Attribute: {env: staging, test_request: false, redact_trace: true}

Die folgenden Spans stimmen nicht mit den Include-Eigenschaften und Prozessoraktionen überein:

* Span3 Name: 'svcB' Attribute: {env: production, test_request: true, credit_card: 1234, redact_trace: false}

* Span4 Name: 'svcC' Attribute: {env: dev, test_request: false}

## <a name="attribute-processor"></a>Attributprozessor 

Der Attributprozessor ändert Attribute einer Span. Er unterstützt optional die Möglichkeit zum Einschließen/Ausschließen von Spans.
Eine Liste von Aktionen ist erforderlich, deren Ausführungsreihenfolge in der Konfigurationsdatei angegeben wird. Die unterstützten Aktionen sind:

* `insert` : Fügt ein neues Attribut in Spans ein, wo der Schlüssel noch nicht vorhanden ist.
* `update` : Aktualisiert ein Attribut in Spans, wo der Schlüssel vorhanden ist.
* `delete` : Löscht ein Attribut aus einer Span.
* `hash` : Versieht einen vorhandenen Attributwert mit einem Hash (SHA1).

Für die Aktionen `insert` und `update`
* ist `key` erforderlich.
* ist entweder `value` oder `fromAttribute` erforderlich.
* `action` ist erforderlich.

Für die Aktion `delete`
* ist `key` erforderlich.
* ist `action`: `delete` erforderlich.

Für die Aktion `hash`
* ist `key` erforderlich.
* ist `action`: `hash` erforderlich.

Die Liste der Aktionen kann erstellt werden, um umfangreiche Szenarios zu erstellen, z. B. das Auffüllen von Attributen, Kopieren von Werten in einen neuen Schlüssel, Überarbeiten sensibler Informationen.

#### <a name="sample-usage"></a>Beispielverwendung

Im folgenden Beispiel wird das Einfügen von Schlüsseln/Werten in Spans veranschaulicht:

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "attribute1",
            "value": "value1",
            "action": "insert"
          },
          {
            "key": "key1",
            "fromAttribute": "anotherkey",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

Im folgenden Beispiel wird das Konfigurieren des Prozessors zum ausschließlichen Aktualisieren vorhandener Schlüssel in einem Attribut veranschaulicht:

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "piiattribute",
            "value": "redacted",
            "action": "update"
          },
          {
            "key": "credit_card",
            "action": "delete"
          },
          {
            "key": "user.email",
            "action": "hash"
          }
        ]
      }
    ]
  }
}
```

Im folgenden Beispiel wird veranschaulicht, wie Spans verarbeitet werden, deren Span-Name RegExp-Mustern entspricht.
Dieser Prozessor entfernt das Attribut „token“ und verschleiert das Attribut „password“ in Spans, deren Span-Name mit „auth.\*“ übereinstimmt und nicht mit „login.\*“.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "auth.*"
          ]
        },
        "exclude": {
          "matchType": "regexp",
          "spanNames": [
            "login.*"
          ]
        },
        "actions": [
          {
            "key": "password",
            "value": "obfuscated",
            "action": "update"
          },
          {
            "key": "token",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

## <a name="span-processors"></a>Span-Prozessoren

Der Span-Prozessor ändert entweder den Span-Namen oder die Attribute einer Span basierend auf dem Span-Namen. Er unterstützt optional die Möglichkeit zum Einschließen/Ausschließen von Spans.

### <a name="name-a-span"></a>Name einer Span

Die folgende Einstellung ist als Teil des Namensabschnitts erforderlich:

* `fromAttributes`: Der Attributwert für die Schlüssel wird verwendet, um einen neuen Namen in der in der Konfiguration angegebenen Reihenfolge zu erstellen. Alle Attributschlüssel müssen in der Span angegeben werden, damit der Prozessor sie umbenennen kann.

Die folgende Einstellung kann optional konfiguriert werden:

* `separator`: Eine angegebene Zeichenfolge, die verwendet wird, um Werte aufzuteilen.
> [!NOTE]
> Wenn das Umbenennen von Attributen abhängig ist, die durch den Attributprozessor geändert werden, stellen Sie sicher, dass der Span-Prozessor in der Pipelinespezifikation nach dem Attributprozessor angegeben wird.

#### <a name="sample-usage"></a>Beispielverwendung

Das folgende Beispiel zeigt, dass die Werte der Attribute „db.svc“, „operation“ und „id“ den neuen Namen der Span bilden, in dieser Reihenfolge, getrennt durch den Wert „::“.
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "fromAttributes": [
            "db.svc",
            "operation",
            "id"
          ],
          "separator": "::"
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-span-name"></a>Extrahieren von Attributen aus dem Span-Namen

Eine Liste von regulären Ausdrücken ist erforderlich, um Span-Namen abzugleichen und Attribute auf der Grundlage von Teilausdrücken daraus zu extrahieren. Sie muss im Abschnitt `toAttributes` angegeben werden.

Die folgenden Einstellungen sind erforderlich:

`rules` : Eine Liste von Regeln zum Extrahieren von Attributwerten aus dem Span-Namen. Die Werte im Span-Namen werden durch extrahierte Attributnamen ersetzt. Jede Regel in der Liste ist eine RegEx-Musterzeichenfolge. Der Span-Name wird anhand der RegEx-Musterzeichenfolge überprüft. Wenn die RegEx-Musterzeichenfolge übereinstimmt, werden alle benannten Teilausdrücke der RegEx-Musterzeichenfolge als Attribute extrahiert und der Span hinzugefügt. Jeder Teilausdruckname wird zu einem Attributnamen, und der übereinstimmende Anteil des Teilausdrucks wird zum Attributwert. Der übereinstimmende Anteil im Span-Namen wird durch den extrahierten Attributnamen ersetzt. Wenn die Attribute bereits in der Span vorhanden sind, werden sie überschrieben. Der Prozess wird für alle Regeln in der Reihenfolge wiederholt, in der sie angegeben sind. Jede nachfolgende Regel funktioniert bei dem Span-Namen, der nach der Verarbeitung der vorherigen Regel ausgegeben wird.

#### <a name="sample-usage"></a>Beispielverwendung

Nehmen wir an, der Eingabe-Span-Name sei „/api/v1/document/12345678/update“. Durch das Anwenden der folgenden Ergebnisse im Ausgabe-Span-Namen „/api/v1/document/{documentId}/update“ wird der Span ein neues Attribut „documentId“=„12345678“ hinzugefügt.
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "toAttributes": {
            "rules": [
              "^/api/v1/document/(?<documentId>.*)/update$"
            ]
          }
        }
      }
    ]
  }
}
```

Im Folgenden wird das Umbenennen des Span-Namens in „{operation_website}“ und Hinzufügen des Attributs „{Key: operation_website, Value: oldSpanName}", wenn die Span die folgenden Eigenschaften aufweist, veranschaulicht:
- Der Span-Name enthält „/“ an beliebiger Stelle in der Zeichenfolge.
- Der Span-Name ist nicht „donot/change“.
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "^(.*?)/(.*?)$"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "donot/change"
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [
              "(?<operation_website>.*?)$"
            ]
          }
        }
      }
    ]
  }
}
```