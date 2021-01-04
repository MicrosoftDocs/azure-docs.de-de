---
title: Upgrade des Application Insights Java 2.x SDK für Azure Monitor
description: Upgrade des Application Insights Java 2.x SDK für Azure Monitor
ms.topic: conceptual
ms.date: 11/25/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: d815c919c2b2d63b093c4290a661cbf508c56012
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96601066"
---
# <a name="upgrading-from-application-insights-java-2x-sdk"></a>Upgrade des Application Insights Java 2.x SDK

Wenn Sie bereits das Application Insights Java 2.x SDK in Ihrer Anwendung nutzen, müssen Sie es nicht entfernen.
Der Java 3.0-Agent erkennt es und erfasst sowie korreliert alle benutzerdefinierten Telemetriedaten, die über das 2.x SDK gesendet werden, während gleichzeitig alle vom 2.x SDK durchgeführten automatischen Sammlungen unterdrückt werden, um doppelte Telemetriedaten zu verhindern.

Wenn Sie den Application Insights 2.x-Agent verwendet haben, müssen Sie das JVM-Argument `-javaagent:` entfernen, das auf den 2.x-Agent verweist.

Im restlichen Dokument werden die Einschränkungen und Änderungen beschrieben, die beim Upgrade von 2.x auf 3.0 auftreten können, sowie einige Problemumgehungen, die für Sie nützlich sein können.

## <a name="telemetryinitializers-and-telemetryprocessors"></a>TelemetryInitializers und TelemetryProcessors

TelemetryInitializers und TelemetryProcessors des 2.x SDK werden bei Verwendung des 3.0-Agents nicht ausgeführt.
Viele der Anwendungsfälle, in denen diese Eigenschaften zuvor erforderlich waren, können in 3.0 gelöst werden, indem [benutzerdefinierte Dimensionen](./java-standalone-config.md#custom-dimensions) oder [Telemetrieprozessoren](./java-standalone-telemetry-processors.md) konfiguriert werden.

## <a name="multiple-applications-in-a-single-jvm"></a>Mehrere Anwendungen in einer einzelnen JVM

Derzeit unterstützt Version 3.0 pro laufendem Prozess nur eine einzige [ Verbindungszeichenfolge und einen einzigen Rollennamen](./java-standalone-config.md#connection-string-and-role-name). Insbesondere sind in derselben Tomcat-Bereitstellung noch nicht mehrere Tomcat-Web-Apps mit unterschiedlichen Verbindungszeichenfolgen oder Rollennamen möglich.

## <a name="operation-names"></a>Vorgangsnamen

Vorgangsnamen in Version 3.0 haben sich geändert, um allgemein eine bessere aggregierte Ansicht auf der Benutzeroberfläche des Application Insights-Portals zu bieten.

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-3-0.png" alt-text="Vorgangsnamen in Version 3.0":::

Bei einigen Anwendungen bevorzugen Sie jedoch möglicherweise immer noch die aggregierte Ansicht auf der Benutzeroberfläche, die durch die vorherigen Vorgangsnamen bereitgestellt wurde. In diesem Fall können Sie die Funktion [Telemetrieprozessoren](./java-standalone-telemetry-processors.md) (Vorschau) in Version 3.0 verwenden, um das vorherige Verhalten zu replizieren.

### <a name="prefix-the-operation-name-with-the-http-method-get-post-etc"></a>Voranstellen der HTTP-Methode (`GET`, `POST` usw.) als Präfix für den Vorgangsnamen

Im 2.x SDK wurde den Vorgangsnamen die HTTP-Methode (`GET`, `POST` usw.) als Präfix vorangestellt.

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-prefixed-by-http-method.png" alt-text="Vorgangsnamen mit HTTP-Methode als Präfix":::

Mit dem folgenden Codeausschnitt werden drei Telemetrieprozessoren konfiguriert, die zum Replizieren des vorherigen Verhaltens kombiniert werden.
Die Telemetrieprozessoren führen die folgenden Aktionen aus (in der angegebenen Reihenfolge):

1. Der erste Telemetrieprozessor ist ein Span-Prozessor (Typ `span`). Das bedeutet, dass er für `requests` und `dependencies` zutrifft.

   Er sucht nach Spans, die über ein Attribut mit dem Namen `http.method` verfügen und einen Span-Namen aufweisen, der mit `/` beginnt.

   Anschließend wird dieser Span-Name in ein Attribut mit dem Namen `tempName` extrahiert.

2. Der zweite Telemetrieprozessor ist ebenfalls ein Span-Prozessor.

   Er sucht nach Spans, die über ein Attribut mit dem Namen `tempName` verfügen.

   Anschließend wird der Span-Name durch Verkettung der beiden Attribute `http.method` und `tempName` (getrennt durch ein Leerzeichen) aktualisiert.

3. Der letzte Telemetrieprozessor ist ein Attributprozessor (Typ `attribute`). Das bedeutet, dass er für alle Telemetrien zutrifft, die über Attribute verfügen (derzeit`requests`, `dependencies` und `traces`).

   Er sucht nach Telemetrien, die über ein Attribut mit dem Namen `tempName` verfügen.

   Anschließend wird das Attribut mit dem Namen `tempName`gelöscht, damit es nicht als benutzerdefinierte Dimension gemeldet wird.

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "attributes": [
            { "key": "http.method", "value": "" }
          ],
          "spanNames": [ "^/" ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "^(?<tempName>.*)$" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.method", "tempName" ],
          "separator": " "
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "actions": [
          { "key": "tempName", "action": "delete" }
        ]
      }
    ]
  }
}
```

### <a name="set-the-operation-name-to-the-full-path"></a>Festlegen des Vorgangsnamens auf den vollständigen Pfad

Im 2.x SDK enthielten die Vorgangsnamen außerdem in einigen Fällen den vollständigen Pfad.

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-with-full-path.png" alt-text="Vorgangsnamen mit vollständigem Pfad":::

Mit dem folgenden Codeausschnitt werden vier Telemetrieprozessoren konfiguriert, die zum Replizieren des vorherigen Verhaltens kombiniert werden.
Die Telemetrieprozessoren führen die folgenden Aktionen aus (in der angegebenen Reihenfolge):

1. Der erste Telemetrieprozessor ist ein Span-Prozessor (Typ `span`). Das bedeutet, dass er für `requests` und `dependencies` zutrifft.

   Er sucht nach Spans, die über ein Attribut mit dem Namen `http.url` verfügen.

   Anschließend wird der Span-Name mit dem Wert des `http.url`-Attributs aktualisiert.

   Damit wäre der Vorgang abgeschlossen, außer dass `http.url` so ähnlich aussieht wie `http://host:port/path`, und Sie wahrscheinlich nur den `/path`-Teil wünschen.

2. Der zweite Telemetrieprozessor ist ebenfalls ein Span-Prozessor.

   Er sucht nach Spans, die über ein Attribut mit dem Namen `http.url` verfügen (d. h. alle Spans, die vom ersten Prozessor gefunden wurden).

   Anschließend wird der Pfadteil des Span-Namens in ein Attribut mit dem Namen `tempName` extrahiert.

3. Der dritte Telemetrieprozessor ist ebenfalls ein Span-Prozessor.

   Er sucht nach Spans, die über ein Attribut mit dem Namen `tempPath` verfügen.

   Anschließend wird der Span-Name aus dem Attribut `tempPath` aktualisiert.

4. Der letzte Telemetrieprozessor ist ein Attributprozessor (Typ `attribute`). Das bedeutet, dass er für alle Telemetrien zutrifft, die über Attribute verfügen (derzeit`requests`, `dependencies` und `traces`).

   Er sucht nach Telemetrien, die über ein Attribut mit dem Namen `tempPath` verfügen.

   Anschließend wird das Attribut mit dem Namen `tempPath`gelöscht, damit es nicht als benutzerdefinierte Dimension gemeldet wird.

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.url" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.url" ]
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.url" }
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "https?://[^/]+(?<tempPath>/[^?]*)" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "name": {
          "fromAttributes": [ "tempPath" ]
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "actions": [
          { "key": "tempPath", "action": "delete" }
        ]
      }
    ]
  }
}
```

## <a name="dependency-names"></a>Abhängigkeitsnamen

Die Abhängigkeitsnamen in Version 3.0 haben sich ebenfalls geändert, um allgemein eine bessere aggregierte Ansicht auf der Benutzeroberfläche des Application Insights-Portals zu bieten.

Auch hier bevorzugen Sie bei einigen Anwendungen möglicherweise immer noch die aggregierte Ansicht auf der Benutzeroberfläche, die durch die vorherigen Abhängigkeitsnamen bereitgestellt wurde. In diesem Fall können Sie ähnliche Vorgehensweisen wie oben verwenden, um das vorherige Verhalten zu replizieren.

## <a name="operation-name-on-dependencies"></a>Vorgangsname für Abhängigkeiten

Zuvor wurde im 2.x SDK der Vorgangsname aus der Anforderungstelemetrie auch für die Abhängigkeitstelemetrie festgelegt.
In Application Insights Java 3.0 wird der Vorgangsname für die Abhängigkeitstelemetrie nicht mehr aufgefüllt.
Wenn Sie den Vorgangsnamen für die Anforderung anzeigen möchten, die der Abhängigkeitstelemetrie übergeordnet ist, können Sie eine Protokollabfrage (Kusto) zur Verknüpfung der Abhängigkeitstabelle mit der Anforderungstabelle schreiben.
