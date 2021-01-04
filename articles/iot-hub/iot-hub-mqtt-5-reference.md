---
title: 'Azure IoT Hub: MQTT 5-API-Referenz (Vorschau)'
description: Erfahren Sie mehr über die MQTT 5-API-Referenz für IoT Hub.
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: reference
ms.date: 11/19/2020
ms.author: jlian
ms.openlocfilehash: 5f0af7d6bf16a05fad1ca9df5db1729abd088010
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96603053"
---
# <a name="iot-hub-data-plane-mqtt-5-api-reference"></a>MQTT 5-API-Referenz auf IoT Hub-Datenebene

In diesem Dokument werden die verfügbaren Vorgänge in Version 2.0 (API-Version: `2020-10-01-preview`) der API auf IoT Hub-Datenebene definiert.

## <a name="operations"></a>Operations

### <a name="get-twin"></a>Get Twin

Abrufen des Zwillingsstatus

#### <a name="request"></a>Anforderung

**Themenname:** `$iothub/twin/get`

**Eigenschaften:** keine

**Payload:** leer

#### <a name="success-response"></a>Antwort bei Erfolg

**Eigenschaften:** keine

**Nutzlast**: Zwilling

#### <a name="alternative-responses"></a>Alternative Antworten

| Status | Name | BESCHREIBUNG |
| :----- | :--- | :---------- |
| 0100 |  Ungültige Anforderung | Die Vorgangsnachricht ist falsch formatiert und kann nicht verarbeitet werden. |
| 0101 |  Nicht autorisiert | Der Client ist nicht zum Ausführen des Vorgangs autorisiert. |
| 0102 |  Nicht zulässig | Der Vorgang ist nicht zulässig. |
| 0501 |  Gedrosselt | Die Anforderungsrate ist für die SKU zu hoch. |
| 0502 |  Quote überschritten | Das tägliche Kontingent der aktuellen SKU wurde überschritten. |
| 0601 |  Serverfehler | Interner Serverfehler |
| 0602 |  Timeout | Beim Vorgang ist vor dem Abschluss ein Timeout aufgetreten. |
| 0603 |  Server ausgelastet | Der Server ist ausgelastet. |

#### <a name="pseudo-code-sample"></a>Pseudobeispielcode

```

-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/get
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="patch-twin-reported"></a>Gemeldete Patchzwillinge

Status der gemeldeten Patchzwillinge

#### <a name="request"></a>Anforderung

**Themenname:** `$iothub/twin/patch/reported`

**Eigenschaften**:

| Name | type | Erforderlich | BESCHREIBUNG |
| :--- | :--- | :------- | :---------- |
| if-version | u64 | nein |  |

**Nutzlast**: TwinState

#### <a name="success-response"></a>Antwort bei Erfolg

**Eigenschaften**:

| Name | type | Erforderlich | BESCHREIBUNG |
| :--- | :--- | :------- | :---------- |
| version | u64 | ja | Version des gemeldeten Status nach dem Anwenden des Patches |

**Payload:** leer

#### <a name="alternative-responses"></a>Alternative Antworten

| Status | Name | BESCHREIBUNG |
| :----- | :--- | :---------- |
| 0104 |  Vorbedingung nicht erfüllt | Die Anforderung wurde abgebrochen, da die Vorbedingung nicht erfüllt war. |
| 0100 |  Ungültige Anforderung | Die Vorgangsnachricht ist falsch formatiert und kann nicht verarbeitet werden. |
| 0101 |  Nicht autorisiert | Der Client ist nicht zum Ausführen des Vorgangs autorisiert. |
| 0102 |  Nicht zulässig | Der Vorgang ist nicht zulässig. |
| 0501 |  Gedrosselt | Die Anforderungsrate ist für die SKU zu hoch. |
| 0502 |  Quote überschritten | Das tägliche Kontingent der aktuellen SKU wurde überschritten. |
| 0601 |  Serverfehler | Interner Serverfehler |
| 0602 |  Timeout | Beim Vorgang ist vor dem Abschluss ein Timeout aufgetreten. |
| 0603 |  Server ausgelastet | Der Server ist ausgelastet. |

#### <a name="pseudo-code-sample"></a>Pseudobeispielcode

```

-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/patch/reported
    [if-version: <u64>]
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="receive-commands"></a>Empfangen von Befehlen

Empfangen und Verarbeiten von Befehlen

#### <a name="message"></a>Meldung

**Themenname:** `$iothub/commands`

**Eigenschaften**:

| Name | type | Erforderlich | BESCHREIBUNG |
| :--- | :--- | :------- | :---------- |
| sequence-no | u64 | ja | Sequenznummer der Nachricht |
| enqueued-time | time | ja | Zeitstempel des Zeitpunkts, zu dem die Nachricht in das System gelangt ist |
| delivery-count | u32 | ja | Häufigkeit, mit der die Nachrichtenübermittlung versucht wurde |
| creation-time | time | nein | Zeitstempel des Zeitpunkts, zu dem die Nachricht erstellt wurde (vom Absender bereitgestellt) |
| message-id | Zeichenfolge | nein | Nachrichtenidentität (vom Absender bereitgestellt) |
| user-id | Zeichenfolge | nein | Benutzeridentität (vom Absender bereitgestellt) |
| correlation-id | Zeichenfolge | nein | Korrelationsidentität (vom Absender bereitgestellt) |
| Inhaltstyp | Zeichenfolge | nein | Gibt den Inhaltstyp der Payload an |
| content-encoding | Zeichenfolge | nein | Gibt die Inhaltscodierung der Payload an |

**Payload:** beliebige Bytesequenz

#### <a name="success-acknowledgment"></a>Bestätigung bei Erfolg

Gibt an, dass der Befehl für die Verarbeitung durch den Client akzeptiert wurde

**Eigenschaften:** keine

**Payload:** leer

#### <a name="alternative-acknowledgments"></a>Alternative Bestätigungen

| Ursachencode | Status | Name | BESCHREIBUNG |
| :---------- | :----- | :--- | :---------- |
| 131 | 0603 | Abandon | Gibt an, dass der Befehl zu diesem Zeitpunkt nicht verarbeitet wird und in der Zukunft erneut übermittelt werden sollte. |
| 131 | 0100 | Reject | Gibt an, dass der Befehl vom Client abgelehnt wurde und nicht erneut versucht werden sollte. |

#### <a name="pseudo-code-sample"></a>Pseudobeispielcode

```

-> SUBSCRIBE
    - Topic: $iothub/commands
      QoS: 1
<- PUBLISH
    QoS: 1
    Topic: $iothub/commands
    sequence-no: <u64>enqueued-time: <time>delivery-count: <u32>[creation-time: <time>][message-id: <string>][user-id: <string>][correlation-id: <string>][Content Type: <string>][content-encoding: <string>]
    Payload: ...

-> PUBACK

```

### <a name="receive-direct-methods"></a>Empfangen von direkten Methoden

Empfangen und Verarbeiten von Aufrufen direkter Methoden

#### <a name="request"></a>Anforderung

**Themenname:** `$iothub/methods/{name}`

**Eigenschaften:** keine

**Payload:** beliebige Bytesequenz

#### <a name="success-response"></a>Antwort bei Erfolg

**Eigenschaften**:

| Name | type | Erforderlich | BESCHREIBUNG |
| :--- | :--- | :------- | :---------- |
| response-code | u32 | ja |  |

**Payload:** beliebige Bytesequenz

#### <a name="alternative-responses"></a>Alternative Antworten

| Status | Name | BESCHREIBUNG |
| :----- | :--- | :---------- |
| 06A0 |  Nicht verfügbar | Gibt an, dass der Client über diese Verbindung nicht erreichbar ist. |

#### <a name="pseudo-code-sample"></a>Pseudobeispielcode

```

-> SUBSCRIBE
    - Topic: methods/{name}
      QoS: 0
<- SUBACK
<- PUBLISH
    QoS: 0
    Topic: $iothub/methods/{name}
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses

```

### <a name="receive-twin-desired-state-changes"></a>Empfangen von Änderungen am gewünschten Status von Zwillingen

Empfangen von Updates am gewünschten Zustand des Zwillings

#### <a name="message"></a>Meldung

**Themenname:** `$iothub/twin/patch/desired`

**Eigenschaften**:

| Name | type | Erforderlich | BESCHREIBUNG |
| :--- | :--- | :------- | :---------- |
| version | u64 | ja | Version des gewünschten Status zu diesem Update |

**Nutzlast**: TwinState

#### <a name="pseudo-code-sample"></a>Pseudobeispielcode

```

-> SUBSCRIBE
    - Topic: $iothub/twin/patch/desired
      QoS: 0
<- PUBLISH
    QoS: 0
    Topic: $iothub/twin/patch/desired
    version: <u64>
    Payload: ...

```

### <a name="send-telemetry"></a>Senden von Telemetriedaten

Senden Sie eine Nachricht an den Telemetriekanal. Standardmäßig ist dies Event Hubs, Sie können aber über die Routingkonfiguration einen anderen Endpunkt festlegen.

#### <a name="message"></a>Meldung

**Themenname:** `$iothub/telemetry`

**Eigenschaften**:

| Name | type | Erforderlich | BESCHREIBUNG |
| :--- | :--- | :------- | :---------- |
| Inhaltstyp | Zeichenfolge | nein | Wird in der übermittelten Nachricht in eine `content-type`-Systemeigenschaft übersetzt |
| content-encoding | Zeichenfolge | nein | Wird in der übermittelten Nachricht in eine `content-encoding`-Systemeigenschaft übersetzt |
| message-id | Zeichenfolge | nein | Wird in der übermittelten Nachricht in eine `message-id`-Systemeigenschaft übersetzt |
| user-id | Zeichenfolge | nein | Wird in der übermittelten Nachricht in eine `user-id`-Systemeigenschaft übersetzt |
| correlation-id | Zeichenfolge | nein | Wird in der übermittelten Nachricht in eine `correlation-id`-Systemeigenschaft übersetzt |
| creation-time | time | nein | Wird in der übermittelten Nachricht in eine `iothub-creation-time-utc`-Eigenschaft übersetzt |

**Payload:** beliebige Bytesequenz

#### <a name="success-acknowledgment"></a>Bestätigung bei Erfolg

Die Nachricht wurde an den Telemetriekanal übermittelt.

**Eigenschaften:** keine

**Payload:** leer

#### <a name="alternative-acknowledgments"></a>Alternative Bestätigungen

| Ursachencode | Status | Name | BESCHREIBUNG |
| :---------- | :----- | :--- | :---------- |
| 131 | 0100 | Ungültige Anforderung | Die Vorgangsnachricht ist falsch formatiert und kann nicht verarbeitet werden. |
| 135 | 0101 | Nicht autorisiert | Der Client ist nicht zum Ausführen des Vorgangs autorisiert. |
| 131 | 0102 | Nicht zulässig | Der Vorgang ist nicht zulässig. |
| 131 | 0601 | Serverfehler | Interner Serverfehler |
| 151 | 0501 | Gedrosselt | Die Anforderungsrate ist für die SKU zu hoch. |
| 151 | 0502 | Quote überschritten | Das tägliche Kontingent der aktuellen SKU wurde überschritten. |
| 131 | 0602 | Timeout | Beim Vorgang ist vor dem Abschluss ein Timeout aufgetreten. |
| 131 | 0603 | Server ausgelastet | Der Server ist ausgelastet. |

#### <a name="pseudo-code-sample"></a>Pseudobeispielcode

```
-> PUBLISH
    QoS: 1
    Topic: $iothub/telemetry
    [Content Type: <string>]
    [content-encoding: <string>]
    [message-id: <string>]
    [user-id: <string>]
    [correlation-id: <string>]
    [creation-time: <time>]

<- PUBACK

```

## <a name="responses"></a>Antworten

### <a name="bad-request"></a>Ungültige Anforderung

Die Vorgangsnachricht ist falsch formatiert und kann nicht verarbeitet werden.

**Ursachencode:** `131`

**Status:** `0100`

**Eigenschaften**:

| Name | type | Erforderlich | BESCHREIBUNG |
| :--- | :--- | :------- | :---------- |
| reason | Zeichenfolge | nein | enthält Informationen darüber, was speziell an dieser Nachricht nicht gültig ist |

**Payload:** leer

### <a name="conflict"></a>Konflikt:

Der Vorgang steht im Konflikt mit einem anderen laufenden Vorgang.

**Ursachencode:** `131`

**Status:** `0103`

**Eigenschaften**:

| Name | type | Erforderlich | BESCHREIBUNG |
| :--- | :--- | :------- | :---------- |
| trace-id | Zeichenfolge | nein | Ablaufverfolgungs-ID für die Korrelation mit zusätzlichen Diagnoseinformationen zum Fehler |
| reason | Zeichenfolge | nein | enthält Informationen darüber, was speziell an dieser Nachricht nicht gültig ist |

**Payload:** leer

### <a name="not-allowed"></a>Nicht zulässig

Der Vorgang ist nicht zulässig.

**Ursachencode:** `131`

**Status:** `0102`

**Eigenschaften**:

| Name | type | Erforderlich | BESCHREIBUNG |
| :--- | :--- | :------- | :---------- |
| reason | Zeichenfolge | nein | enthält Informationen darüber, was speziell an dieser Nachricht nicht gültig ist |

**Payload:** leer

### <a name="not-authorized"></a>Nicht autorisiert

Der Client ist nicht zum Ausführen des Vorgangs autorisiert.

**Ursachencode:** `135`

**Status:** `0101`

**Eigenschaften**:

| Name | type | Erforderlich | BESCHREIBUNG |
| :--- | :--- | :------- | :---------- |
| trace-id | Zeichenfolge | nein | Ablaufverfolgungs-ID für die Korrelation mit zusätzlichen Diagnoseinformationen zum Fehler |

**Payload:** leer

### <a name="not-found"></a>Nicht gefunden

Die angeforderte Ressource ist nicht vorhanden.

**Ursachencode:** `131`

**Status:** `0504`

**Eigenschaften**:

| Name | type | Erforderlich | BESCHREIBUNG |
| :--- | :--- | :------- | :---------- |
| reason | Zeichenfolge | nein | enthält Informationen darüber, was speziell an dieser Nachricht nicht gültig ist |

**Payload:** leer

### <a name="not-modified"></a>Nicht geändert

Die Ressource wurde hinsichtlich der angegebenen Vorbedingung nicht geändert.

**Ursachencode:** `0`

**Status:** `0001`

**Eigenschaften:** keine

**Payload:** leer

### <a name="precondition-failed"></a>Vorbedingung nicht erfüllt

Die Anforderung wurde abgebrochen, da die Vorbedingung nicht erfüllt war.

**Ursachencode:** `131`

**Status:** `0104`

**Eigenschaften:** keine

**Payload:** leer

### <a name="quota-exceeded"></a>Quote überschritten

Das tägliche Kontingent der aktuellen SKU wurde überschritten.

**Ursachencode:** `151`

**Status:** `0502`

**Eigenschaften:** keine

**Payload:** leer

### <a name="resource-exhausted"></a>Ressource erschöpft

Die Ressource verfügt nicht über die Kapazitäten zum Abschließen des Vorgangs.

**Ursachencode:** `131`

**Status:** `0503`

**Eigenschaften**:

| Name | type | Erforderlich | BESCHREIBUNG |
| :--- | :--- | :------- | :---------- |
| reason | Zeichenfolge | nein | enthält Informationen darüber, was speziell an dieser Nachricht nicht gültig ist |

**Payload:** leer

### <a name="server-busy"></a>Server ausgelastet

Der Server ist ausgelastet.

**Ursachencode:** `131`

**Status:** `0603`

**Eigenschaften**:

| Name | type | Erforderlich | BESCHREIBUNG |
| :--- | :--- | :------- | :---------- |
| trace-id | Zeichenfolge | nein | Ablaufverfolgungs-ID für die Korrelation mit zusätzlichen Diagnoseinformationen zum Fehler |

**Payload:** leer

### <a name="server-error"></a>Serverfehler

Interner Serverfehler

**Ursachencode:** `131`

**Status:** `0601`

**Eigenschaften**:

| Name | type | Erforderlich | BESCHREIBUNG |
| :--- | :--- | :------- | :---------- |
| trace-id | Zeichenfolge | nein | Ablaufverfolgungs-ID für die Korrelation mit zusätzlichen Diagnoseinformationen zum Fehler |

**Payload:** leer

### <a name="target-failed"></a>Zielfehler

Das Ziel hat geantwortet, aber die Antwort war ungültig oder falsch formatiert.

**Ursachencode:** `131`

**Status:** `06A2`

**Eigenschaften**:

| Name | type | Erforderlich | BESCHREIBUNG |
| :--- | :--- | :------- | :---------- |
| reason | Zeichenfolge | nein | enthält Informationen darüber, was speziell an dieser Nachricht nicht gültig ist |

**Payload:** leer

### <a name="target-timeout"></a>Timeout beim Ziel

Beim Warten auf den Abschluss der Anforderung durch das Ziel ist ein Timeout aufgetreten.

**Ursachencode:** `131`

**Status:** `06A1`

**Eigenschaften**:

| Name | type | Erforderlich | BESCHREIBUNG |
| :--- | :--- | :------- | :---------- |
| trace-id | Zeichenfolge | nein | Ablaufverfolgungs-ID für die Korrelation mit zusätzlichen Diagnoseinformationen zum Fehler |
| reason | Zeichenfolge | nein | enthält Informationen darüber, was speziell an dieser Nachricht nicht gültig ist |

**Payload:** leer

### <a name="target-unavailable"></a>Ziel nicht verfügbar

Das Ziel ist nicht erreichbar und kann die Anforderung nicht abschließen.

**Ursachencode:** `131`

**Status:** `06A0`

**Eigenschaften:** keine

**Payload:** leer

### <a name="throttled"></a>Gedrosselt

Die Anforderungsrate ist für die SKU zu hoch.

**Ursachencode:** `151`

**Status:** `0501`

**Eigenschaften:** keine

**Payload:** leer

### <a name="timeout"></a>Timeout

Beim Vorgang ist vor dem Abschluss ein Timeout aufgetreten.

**Ursachencode:** `131`

**Status:** `0602`

**Eigenschaften**:

| Name | type | Erforderlich | BESCHREIBUNG |
| :--- | :--- | :------- | :---------- |
| trace-id | Zeichenfolge | nein | Ablaufverfolgungs-ID für die Korrelation mit zusätzlichen Diagnoseinformationen zum Fehler |

**Payload:** leer

