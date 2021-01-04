---
title: MQTT 5-Unterstützung in Azure IoT Hub (Vorschau)
description: Erfahren Sie mehr über die MQTT 5-Unterstützung von IoT Hub.
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: jlian
ms.openlocfilehash: fb2cc0b81083936a67bcd465e0408b9f4b53996b
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96603050"
---
# <a name="iot-hub-mqtt-5-support-overview-preview"></a>Übersicht über die MQTT 5-Unterstützung in IoT Hub (Vorschau)

**Version:** 2.0 **API-Version:** 2020-10-01-preview

In diesem Dokument wird die API auf IoT Hub-Datenebene über das MQTT-Protokoll Version 5.0 definiert. Ausführliche Definitionen in dieser API finden Sie in der [API-Referenz](iot-hub-mqtt-5-reference.md).

## <a name="prerequisites"></a>Voraussetzungen

- [Aktivieren Sie den Vorschaumodus](iot-hub-preview-mode.md) in einem neuen IoT-Hub, um MQTT 5 auszuprobieren.
- Es sind Kenntnisse über die [MQTT 5-Spezifikation](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html) erforderlich.

## <a name="level-of-support-and-limitations"></a>Grad der Unterstützung und Einschränkungen

Die Unterstützung für MQTT 5 in IoT Hub befindet sich in der Vorschauphase und ist folgendermaßen eingeschränkt (wird an Clients über die `CONNACK`-Eigenschaften übermittelt, sofern nicht anders angegeben):

- Es gibt noch keinen offiziellen Support für das [Azure IoT Hub-Geräte-SDK](iot-hub-devguide-sdks.md).
- Abonnementbezeichner werden nicht unterstützt.
- Gemeinsame Abonnements werden nicht unterstützt.
- `RETAIN` wird nicht unterstützt.
- `Maximum QoS` ist `1`.
- `Maximum Packet Size` ist `256 KiB` (unterliegt pro Vorgang weiteren Einschränkungen).
- Zugewiesene Client-IDs werden nicht unterstützt.
- `Keep Alive` ist auf `19 min` beschränkt (maximale Verzögerung bei der Aktivitätsüberprüfung: `28.5 min`).
- `Topic Alias Maximum` ist `10`.
- `Response Information` wird nicht unterstützt. `CONNACK` gibt keine `Response Information`-Eigenschaft zurück, selbst wenn `CONNECT` eine `Request Response Information`-Eigenschaft enthält.
- `Receive Maximum` (maximale Anzahl zulässiger ausstehender, nicht bestätigter `PUBLISH`-Pakete (in Client-Server-Richtung) mit `QoS: 1`) ist `16`.
- Ein einzelner Client darf nicht mehr als `50` Abonnements umfassen.
  Wenn dieser Grenzwert erreicht ist, gibt `SUBACK` den Ursachencode `0x97` (Kontingent überschritten) für Abonnements zurück.

## <a name="connection-lifecycle"></a>Lebenszyklus von Verbindungen

### <a name="connection"></a>Verbindung

Wenn Sie mithilfe dieser API eine Verbindung mit IoT Hub herstellen möchten, richten Sie diese gemäß der MQTT 5-Spezifikation ein.
Der Client muss innerhalb von 30 Sekunden nach dem erfolgreichen TLS-Handshake ein `CONNECT`-Paket senden, andernfalls trennt der Server die Verbindung.
Hier ist ein Beispiel für ein `CONNECT`-Paket:

```yaml
-> CONNECT
    Protocol_Version: 5
    Clean_Start: 0
    Client_Id: D1
    Authentication_Method: SAS
    Authentication_Data: {SAS bytes}
    api-version: 2020-10-10
    host: abc.azure-devices.net
    sas-at: 1600987795320
    sas-expiry: 1600987195320
    client-agent: artisan;Linux
```

- Die `Authentication Method`-Eigenschaft ist erforderlich. Sie gibt an, welche Authentifizierungsmethode verwendet wird. Weitere Informationen zu Authentifizierungsmethoden finden Sie unter [Authentifizierung](#authentication).
- Die Verarbeitung der `Authentication Data`-Eigenschaft hängt von der `Authentication Method` ab. Wenn `Authentication Method` auf `SAS` festgelegt wurde, ist `Authentication Data` erforderlich und muss eine gültige Signatur enthalten. Weitere Informationen zu Authentifizierungsdaten finden Sie unter [Authentifizierung](#authentication).
- Die `api-version`-Eigenschaft ist erforderlich und muss auf die im Header dieser Spezifikation angegebene API-Version festgelegt werden, damit diese Spezifikation angewandt wird.
- Die `host`-Eigenschaft definiert den Hostnamen des Mandanten. Dies ist nicht erforderlich, wenn die SNI-Erweiterung beim TLS-Handshake im Hello-Eintrag vom Client enthalten war.
- `sas-at` definiert den Zeitpunkt der Verbindung.
- `sas-expiry` definiert die Ablaufzeit für die bereitgestellte SAS.
- `client-agent` gibt optional Informationen zum Client an, der die Verbindung herstellt.

> [!NOTE]
> `Authentication Method` und andere Eigenschaften in der Spezifikation mit Namen in Großbuchstaben sind erstrangige Eigenschaften in MQTT 5. Sie werden in der MQTT 5-Spezifikation ausführlich beschrieben. `api-version` und andere Eigenschaften mit Bindestrichen sind Benutzereigenschaften, die für die IoT Hub-API spezifisch sind.

IoT Hub antwortet nach Abschluss der Authentifizierung und dem Sammeln von Daten für die Verbindung mit einem `CONNACK`-Paket. Wenn die Verbindung erfolgreich hergestellt wurde, sieht `CONNACK` wie folgt aus:

```yaml
<- CONNACK
    Session_Present: 1
    Reason_Code: 0x00
    Session_Expiry_Interval: 0xFFFFFFFF # included only if CONNECT specified value less than 0xFFFFFFFF and more than 0x00
    Receive_Maximum: 16
    Maximum_QoS: 1
    Retain_Available: 0
    Maximum_Packet_Size: 262144
    Topic_Alias_Maximum: 10
    Subscription_Identifiers_Available: 0
    Shared_Subscriptions_Available: 0
    Server_Keep_Alive: 1140 # included only if client did not specify Keep Alive or if it specified a bigger value
```

Diese Eigenschaften des `CONNACK`-Pakets entsprechen der [MQTT 5-Spezifikation](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901080). Sie spiegeln die IoT Hub-Funktionen wider.

### <a name="authentication"></a>Authentifizierung

Die `Authentication Method`-Eigenschaft auf dem `CONNECT`-Client definiert, welche Art von Authentifizierung für diese Verbindung verwendet wird:

- `SAS`: Die SAS (Shared Access Signature) wird in der `Authentication Data`-Eigenschaft von `CONNECT` bereitgestellt.
- `X509`: Der Client nutzt die Clientzertifikatauthentifizierung.

 Die Authentifizierung führt zu einem Fehler, wenn die Authentifizierungsmethode nicht mit der Methode übereinstimmt, die für den Client in IoT Hub konfiguriert wurde.

> [!NOTE]
> Für diese API muss die `Authentication Method`-Eigenschaft im `CONNECT`-Paket festgelegt werden. Wenn die `Authentication Method`-Eigenschaft nicht angegeben wird, führt die Verbindungsherstellung zum Fehler `Bad Request`.

Die in früheren API-Versionen verwendete Authentifizierung mit Benutzername und Kennwort wird nicht unterstützt.

#### <a name="sas"></a>SAS

Bei der SAS-basierten Authentifizierung muss der Client die Signatur für den Verbindungskontext bereitstellen. Damit wird die Authentizität der MQTT-Verbindung belegt. Die Signatur muss auf einem von zwei Authentifizierungsschlüsseln in der Konfiguration des Clients in IoT Hub oder einem von zwei gemeinsam genutzten Zugriffsschlüsseln einer [SAS-Richtlinie](iot-hub-devguide-security.md) basieren.

Die Zeichenfolge für das Signieren muss wie folgt gebildet werden:

```text
{host name}\n
{Client Id}\n
{sas-policy}\n
{sas-at}\n
{sas-expiry}\n
```

- `host name` wird entweder von der SNI-Erweiterung (die vom Client während des TLS-Handshakes im Hello-Eintrag übergeben wird) oder der `host`-Benutzereigenschaft im `CONNECT`-Paket abgeleitet.
- `Client Id` ist der Clientbezeichner im `CONNECT`-Paket.
- `sas-policy` (falls vorhanden) definiert die IoT Hub-Zugriffsrichtlinie für die Authentifizierung. Sie wird als Benutzereigenschaft im `CONNECT`-Paket codiert. Optional: Wenn keine Angabe erfolgt, werden stattdessen die Authentifizierungseinstellungen in der Geräteregistrierung verwendet.
- `sas-at` (falls vorhanden) gibt den Zeitpunkt der Verbindung an (aktuelle Zeit). Dies wird als Benutzereigenschaft vom Typ `time` im `CONNECT`-Paket codiert.
- `sas-expiry` definiert die Ablaufzeit für die Authentifizierung. Es handelt sich um eine Benutzereigenschaft vom Typ `time` im `CONNECT`-Paket. Diese Eigenschaft ist erforderlich.

Wenn dies bei optionalen Parametern ausgelassen wird, MUSS stattdessen eine leere Zeichenfolge in der Signaturzeichenfolge verwendet werden.

Für das Konvertieren der Zeichenfolge in einen Hash wird HMAC-SHA256 basierend auf einem der symmetrischen Schlüssel des Geräts verwendet. Der Hashwert wird dann als Wert der `Authentication Data`-Eigenschaft festgelegt.

#### <a name="x509"></a>X509

Wenn die `Authentication Method`-Eigenschaft auf `X509` festgelegt ist, authentifiziert IoT Hub die Verbindung basierend auf dem bereitgestellten Clientzertifikat.

#### <a name="reauthentication"></a>Erneute Authentifizierung

Bei Verwendung der SAS-basierten Authentifizierung empfiehlt es sich, Authentifizierungstoken mit kurzer Lebensdauer zu verwenden. Um die Authentifizierung der Verbindung aufrecht zu erhalten und die Trennung aufgrund des Ablaufs zu verhindern, muss sich der Client erneut authentifizieren, indem er ein `AUTH`-Paket mit `Reason Code: 0x19` (erneute Authentifizierung) sendet:

```yaml
-> AUTH
    Reason_Code: 0x19
    Authentication_Method: SAS
    Authentication_Data: {SAS bytes}
    sas-at: {current time}
    sas-expiry: {SAS expiry time}
```

Regeln:

- Die `Authentication Method` muss mit der identisch sein, die für die anfängliche Authentifizierung verwendet wurde.
- Wenn die Verbindung ursprünglich mithilfe einer SAS basierend auf der SAS-Richtlinie authentifiziert wurde, muss die bei der erneuten Authentifizierung verwendete Signatur auf derselben Richtlinie basieren.

Wenn die erneute Authentifizierung erfolgreich ist, sendet IoT Hub ein `AUTH`-Paket mit `Reason Code: 0x00` (Erfolg). Andernfalls sendet IoT Hub ein `DISCONNECT`-Paket mit `Reason Code: 0x87` (nicht autorisiert) und trennt die Verbindung.

### <a name="disconnection"></a>Verbindungstrennung

Der Server kann die Verbindung mit dem Client aus verschiedenen Gründen trennen:

- Der Client verhält sich auf eine Weise falsch, die es unmöglich macht, direkt mit einer negativen Bestätigung (oder Antwort) zu antworten.
- Der Server kann den Status der Verbindung nicht auf dem neuesten Stand halten.
- Ein Client mit derselben Identität hat eine Verbindung hergestellt.

Der Server kann die Verbindung mit jedem Ursachencode trennen, der in der Spezifikation zu MQTT 5.0 definiert ist. Wichtige Hinweise:

- `135` (nicht autorisiert): wenn die erneute Authentifizierung zu einem Fehler führt, das aktuelle SAS-Token abläuft oder die Anmeldeinformationen des Geräts geändert werden
- `142` (Sitzung übernommen): wenn eine neue Verbindung mit derselben Clientidentität hergestellt wurde
- `159` (Verbindungsrate überschritten): wenn die Verbindungsrate für den IoT-Hub überschritten wurde  
- `131` (implementierungsspezifischer Fehler): für benutzerdefinierte Fehler, die in dieser API definiert sind. Mithilfe der Eigenschaften `status` und `reason` werden weitere Details zur Ursache der Verbindungstrennung übermittelt. (Weitere Informationen finden Sie unter [Antwort](#response).)

## <a name="operations"></a>Operations

Sämtliche Funktionen in dieser API werden als Vorgänge ausgedrückt. Hier ist ein Beispiel für den Vorgang zum Senden von Telemetriedaten:

```yaml
-> PUBLISH
    QoS: 1
    Packet_Id: 3
    Topic: $iothub/telemetry
    Payload: Hello

<- PUBACK
    Packet_Id: 3
    Reason_Code: 0
```

Eine umfassende Spezifikation der Vorgänge in dieser API finden Sie in der [API-Referenz](iot-hub-mqtt-5-reference.md).

> [!NOTE]
> Alle Beispiele in dieser Spezifikation werden aus der Perspektive des Clients beschrieben. Das Zeichen `->` bedeutet, dass der Client das Paket sendet, und `<-`, dass er es empfängt.

### <a name="message-topics-and-subscriptions"></a>Nachrichtenthemen und -abonnements

Die in den Nachrichten zu Vorgängen in dieser API verwendeten Themen beginnen mit `$iothub/`.
Die Semantik für MQTT-Broker gilt für diese Vorgänge nicht. (Weitere Informationen finden Sie unter [Topics beginning with \$](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901246) (Themen, die mit $ beginnen).)
Themen, die mit `$iothub/` beginnen und nicht in dieser API definiert sind, werden nicht unterstützt:

- Das Senden von Nachrichten an ein nicht definiertes Thema führt zur Antwort `Not Found`. (Weitere Details finden Sie weiter unten unter [Antwort](#response).)
- Das Abonnieren eines nicht definierten Themas führt zu `SUBACK` mit `Reason Code: 0x8F` (Themenfilter ungültig).

Bei Themen- und Eigenschaftennamen muss die Groß-/Kleinschreibung beachtet werden. `$iothub/telemetry/` wird beispielsweise nicht unterstützt, `$iothub/telemetry` hingegen schon.

> [!NOTE]
> Platzhalter in Abonnements unter `$iothub/..` werden nicht unterstützt. Ein Client kann also nicht `$iothub/+` oder `$iothub/#` abonnieren. Entsprechende Versuche führen zu `SUBACK` mit `Reason Code: 0xA2` (Platzhalterabonnements nicht unterstützt). Anstelle von Pfadparametern im Themennamen werden nur Platzhalter für Einzelsegmente (`+`) bei Vorgängen mit diesen unterstützt.

### <a name="interaction-types"></a>Interaktionstypen

Alle Vorgänge in dieser API basieren auf einem von zwei Interaktionstypen:

- Nachricht mit optionaler Bestätigung (MessageAck)
- Anforderung/Antwort (ReqRep)

Vorgänge unterscheiden sich auch nach der Richtung (Richtung der ersten Nachricht in der Kommunikation):

- Client-zu-Server (C2S)
- Server-zu-Client (S2C)

Das Senden von Telemetriedaten ist z. B. ein Client-zu-Server-Vorgang des Typs „Nachricht mit Bestätigung“, während die Verarbeitung direkter Methoden ein Server-zu-Client-Vorgang des Typs „Anforderung/Antwort“ ist.

#### <a name="message-acknowledgement-interactions"></a>Interaktionen bei der Nachrichtenbestätigung

Die Interaktion bei einer Nachricht mit optionaler Bestätigung (MessageAck) wird in MQTT als Austausch von `PUBLISH`- und `PUBACK`-Paketen ausgedrückt. Die Bestätigung ist optional, und der Absender kann sie auch nicht anfordern, indem er ein `PUBLISH`-Paket mit `QoS: 0` sendet.

> [!NOTE]
> Wenn Eigenschaften im `PUBACK`-Paket abgeschnitten werden, da der Client `Maximum Packet Size` deklariert hat, behält IoT Hub so viele Benutzereigenschaften bei, wie mit dem angegebenen Grenzwert möglich sind. Die zuerst aufgeführten Benutzereigenschaften haben eine höhere Wahrscheinlichkeit, gesendet zu werden, als die später aufgeführten. Die `Reason String`-Eigenschaft hat die niedrigste Priorität.

##### <a name="example-of-simple-messageack-interaction"></a>Beispiel einer einfachen MessageAck-Interaktion

Meldung:

```yaml
PUBLISH
    QoS: 1
    Packet_Id: 34
    Topic: $iothub/{request.path}
    Payload: <any>
```

Bestätigung (Erfolg):

```yaml
PUBACK
    Packet_Id: 34
    Reason_Code: 0
```

#### <a name="request-response-interactions"></a>Interaktionen mit Anforderung/Antwort

Bei Interaktionen vom Typ „Anforderung/Antwort“ (ReqRep) werden sowohl die Anforderung als auch die Antwort in `PUBLISH`-Pakete mit `QoS: 0` übersetzt.

Die `Correlation Data`-Eigenschaft muss in beiden festgelegt werden. Sie dient dazu, das Antwortpaket mit dem Anforderungspaket abzugleichen.

Diese API verwendet das Antwortthema `$iothub/responses` für alle ReqRep-Vorgänge. Das Abonnieren (und Aufheben des Abonnements) dieses Themas für Client-zu-Server-Vorgängen ist nicht erforderlich. Der Server sieht alle Clients als abonniert an.

##### <a name="example-of-simple-reqrep-interaction"></a>Beispiel einer einfachen ReqRep-Interaktion

Anforderung:

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/{request.path}
    Correlation_Data: 0x01 0xFA
    Payload: ...
```

Antwort (Erfolg):

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    Payload: ...
```

ReqRep-Interaktionen unterstützen keine `PUBLISH`-Pakete mit `QoS: 1` als Anforderungs- oder Antwortnachrichten. Das Senden der Anforderung `PUBLISH` führt zur Antwort `Bad Request`.

Die `Correlation Data`-Eigenschaft unterstützt eine maximale Länge von 16 Byte. Wenn die `Correlation Data`-Eigenschaft im `PUBLISH`-Paket auf einen Wert von mehr als 16 Byte festgelegt ist, sendet IoT Hub `DISCONNECT` mit dem Ergebnis `Bad Request` und trennt die Verbindung. Dieses Verhalten gilt nur für Pakete, die innerhalb dieser API ausgetauscht werden.

> [!NOTE]
> Korrelationsdaten sind eine beliebige Bytesequenz, sodass z. B. nicht garantiert ist, dass es eine UTF-8-Zeichenfolge ist.
>
> ReqRep verwendet für die Antwort vordefinierte Themen. Die Eigenschaft für das Antwortthema im `PUBLISH`-Paket der Anforderung (sofern vom Absender festgelegt) wird ignoriert.

IoT Hub abonniert automatisch die Clients für Antwortthemen für sämtliche Client-zu-Server-ReqRep-Vorgänge. Selbst wenn der Client das Abonnement für das Antwortthema explizit kündigt, richtet IoT Hub das Abonnement automatisch wieder ein. Bei Server-zu-Client-ReqRep-Interaktionen muss das Gerät trotzdem ein Abonnement einrichten.

### <a name="message-properties"></a>Nachrichteneigenschaften

Vorgangseigenschaften werden unabhängig davon, ob sie vom System oder vom Benutzer definiert wurden, in MQTT 5 als Paketeigenschaften ausgedrückt.

Bei Namen von Benutzereigenschaften wird die Groß-/Kleinschreibung beachtet, und die Schreibung muss genau der Definition entsprechen. `Trace-ID` wird beispielsweise nicht unterstützt, `trace-id` hingegen schon.

Anforderungen mit Benutzereigenschaften, die nicht der Spezifikation entsprechen und nicht das Präfix `@` aufweisen, führen zu einem Fehler.

Systemeigenschaften werden entweder als erstrangige Eigenschaften (z. B. `Content Type`) oder als Benutzereigenschaften codiert. Die Spezifikation enthält eine vollständige Liste der unterstützten Systemeigenschaften.
Es werden alle erstrangigen Eigenschaften ignoriert, für die in der Spezifikation nicht explizit die Unterstützung angegeben ist.

Wenn benutzerdefinierte Eigenschaften zulässig sind, müssen ihre Namen das Format `@{property name}` aufweisen. Benutzerdefinierte Eigenschaften unterstützen nur gültige UTF-8-Zeichenfolgenwerte. So muss beispielsweise die `MyProperty1`-Eigenschaft mit dem Wert `15` als Benutzereigenschaft mit dem Namen `@MyProperty` und dem Wert `15` codiert werden.

Wenn IoT Hub eine Benutzereigenschaft nicht erkennt, wird sie als Fehler betrachtet, und IoT Hub antwortet mit `PUBACK` mit `Reason Code: 0x83` (implementierungsspezifischer Fehler) und `status: 0100` (ungültige Anforderung). Wenn die Bestätigung nicht angefordert wurde (QoS: 0), wird ein `DISCONNECT`-Paket mit demselben Fehler zurückgesendet, und die Verbindung wird getrennt.

Diese API definiert neben `string` die folgenden Datentypen:

- `time`: Anzahl der Millisekunden seit `1970-01-01T00:00:00.000Z`, z. B. `1600987195320` für `2020-09-24T22:39:55.320Z`.
- `u32`: ganze Zahl ohne Vorzeichen (32 Bit)
- `u64`: ganze Zahl ohne Vorzeichen (64 Bit)
- `i32`: ganze Zahl mit Vorzeichen (32 Bit)

### <a name="response"></a>Antwort

Interaktionen können zu unterschiedlichen Ergebnissen führen: `Success`, `Bad Request`, `Not Found` und andere.
Die Ergebnisse werden mithilfe der `status`-Benutzereigenschaft voneinander unterschieden. `Reason Code` in `PUBACK`-Paketen (für MessageAck-Interaktionen) entspricht soweit möglich dem `status`.

> [!NOTE]
> Wenn der Client `Request Problem Information: 0` im CONNECT-Paket angibt, werden in `PUBACK`-Paketen keine Benutzereigenschaften gesendet, um die MQTT 5-Spezifikation einzuhalten. Dies schließt auch die `status`-Eigenschaft ein. In diesem Fall kann der Client trotzdem mit `Reason Code` ermitteln, ob die Bestätigung positiv oder negativ ist. 

Jede Interaktion hat einen Standardwert (oder Erfolg). Dies ist ein `Reason Code` von `0` und die `status`-Eigenschaft mit dem Wert „not set“ (nicht festgelegt). Andernfalls:

- Bei MessageAck-Interaktionen hat `PUBACK` einen anderen `Reason Code` als „0x0“ (Erfolg). Die `status`-Eigenschaft kann angegeben werden, um das Ergebnis zu verdeutlichen.
- Bei ReqRep-Interaktionen wird bei der Antwort `PUBLISH` die `status`-Eigenschaft festgelegt.
- Da es keine Möglichkeit gibt, auf MessageAck-Interaktionen direkt mit `QoS: 0` zu antworten, wird das `DISCONNECT`-Paket stattdessen mit Antwortinformationen gefolgt von einer Trennungsanforderung gesendet.

Beispiele:

Ungültige Anforderung (MessageAck):

```yaml
PUBACK
    Reason_Code: 131
    status: 0100
    reason: Unknown property `test`
```

Nicht autorisiert (MessageAck):

```yaml
PUBACK
    Reason_Code: 135
    status: 0101
```

Nicht autorisiert (ReqRep):

```yaml
PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: ...
    status: 0101
```

Bei Bedarf legt IoT Hub die folgenden Benutzereigenschaften fest:

- `status`: der erweiterte Code von IoT Hub für den Vorgangsstatus. Mithilfe dieses Codes können die Ergebnisse unterschieden werden.
- `trace-id`: die Ablaufverfolgungs-ID für den Vorgang. IoT Hub kann zusätzliche Diagnoseinformationen zu dem Vorgang speichern, die für interne Untersuchungen verwendet werden können.
- `reason`: für Personen lesbare Nachricht mit zusätzlichen Informationen dazu, warum der Vorgang mit dem von der `status`-Eigenschaft angegebenen Zustand beendet wurde.

> [!NOTE]
> Wenn der Client die `Maximum Packet Size`-Eigenschaft im CONNECT-Paket auf einen sehr kleinen Wert festlegt, können möglicherweise nicht alle Benutzereigenschaften erfasst und im Paket angezeigt werden.
> 
> `reason` ist nur für Personen gedacht und sollte nicht in der Clientlogik verwendet werden. In dieser API können Nachrichten jederzeit ohne eine Warnung oder Versionsänderung geändert werden.
>
> Wenn der Client `RequestProblemInformation: 0` im CONNECT-Paket sendet, werden gemäß der [MQTT 5-Spezifikation](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901053) keine Benutzereigenschaften in Bestätigungen eingeschlossen.

#### <a name="status-code"></a>Statuscode

Die `status`-Eigenschaft enthält den Statuscode für den Vorgang. Sie wurde für das effiziente maschinelle Lesen optimiert
und besteht aus einer ganzen Zahl ohne Vorzeichen mit zwei Byte, die als Hexadezimalwert in einer Zeichenfolge wie `0501` codiert wurde.
Codestruktur (Bitmap):

```text
7 6 5 4 3 2 1 0 | 7 6 5 4 3 2 1 0
0 0 0 0 0 R T T | C C C C C C C C
```

Das erste Byte wird für Flags verwendet:

- Die Bits 0 und 1 geben den Ergebnistyp an:
  - `00`: Erfolg
  - `01`: Clientfehler
  - `10`: Serverfehler
- Im zweiten Bit gibt `1` einen Fehlertyp an, der eine Wiederholung zulässt.
- Die Bits 3 bis 7 sind reserviert und müssen auf `0` festgelegt werden.

Das zweite Byte enthält den eigentlichen eindeutigen Antwortcode. Fehlercodes mit unterschiedlichen Flags können im zweiten Byte denselben Wert aufweisen. Beispielsweise können die Fehlercodes `0001`, `0101`, `0201` und `0301` unterschiedliche Bedeutungen haben.

`Too Many Requests` ist z. B. ein wiederholbarer Fehler mit dem eigenen Code `1`. Sein Wert lautet `0000 0101 0000 0001` oder `0x0501`.

Clients können Typbits verwenden, um zu ermitteln, ob der Vorgang erfolgreich abgeschlossen wurde. Außerdem können Clients anhand des Bits zur Wiederholbarkeit entscheiden, ob es sinnvoll ist, den Vorgang zu wiederholen.

## <a name="recommendations"></a>Empfehlungen

### <a name="session-management"></a>Sitzungsverwaltung

Das `CONNACK`-Paket enthält die `Session Present`-Eigenschaft, die angibt, ob der Server eine zuvor erstellte Sitzung wiederhergestellt hat. Mithilfe dieser Eigenschaft können Sie herausfinden, ob Sie bestimmte Themen abonnieren müssen oder ob das Abonnement bereits zuvor abgeschlossen wurde.

Damit sich Clients auf `Session Present` verlassen können, müssen sie die abgeschlossenen Abonnements nachverfolgen (also die gesendeten `SUBSCRIBE`-Pakete und die `SUBACK`-Pakete mit einem Erfolgs-Ursachencode) oder sicherstellen, dass alle Themen in einem `SUBSCRIBE`/`SUBACK`-Austausch abonniert wurden. Sendet der Client andernfalls zwei `SUBSCRIBE`-Pakete, von denen nur eines erfolgreich vom Server verarbeitet wird, übergibt der Server `Session Present: 1` in `CONNACK`, obwohl er nur einen Teil der Clientabonnements akzeptiert hat.

Um den Fall zu vermeiden, in dem eine ältere Version des Clients nicht alle Themen abonniert hat, sollten Sie bedingungslos alles abonnieren, wenn sich das Clientverhalten ändert (z. B. im Rahmen eines Firmwareupdates). Außerdem sollten Sie sicherstellen, dass Abonnements, die nicht mehr verwendet werden, explizit aus den Abonnements entfernt (und von der maximal zulässigen Anzahl von Abonnements abgezogen) werden, indem Sie nicht mehr verwendete Abonnements explizit aufheben.

### <a name="batching"></a>Batchverarbeitung

Es gibt kein spezielles Format, um einen Batch von Nachrichten zu senden. Um den Aufwand bei ressourcenintensiven Vorgängen in TLS und im Netzwerk zu reduzieren, erstellen Sie Pakete (`PUBLISH`, `PUBACK`, `SUBSCRIBE` usw.), bevor Sie sie an den zugrunde liegenden TLS/TCP-Stapel übergeben. Außerdem kann der Client einfacher einen Themenalias innerhalb des „Batches“ festlegen:

- Fügen Sie den vollständigen Themennamen in das erste `PUBLISH`-Paket für die Verbindung ein, und ordnen Sie ihm den Themenalias zu.
- Fügen Sie nachfolgende Pakete für dasselbe Thema mit einem leeren Themennamen und leerer Themenaliaseigenschaft ein.

## <a name="migration"></a>Migration

In diesem Abschnitt werden die Änderungen in der-API im Vergleich zur [früheren MQTT-API](iot-hub-mqtt-support.md) aufgelistet.

- Das Transportprotokoll ist MQTT 5. Bisher war dies MQTT 3.1.1.
- Kontextinformationen für die SAS-Authentifizierung sind direkt im `CONNECT`-Paket enthalten und werden nicht zusammen mit der Signatur codiert.
- Die verwendete Authentifizierungsmethode wird mithilfe der Authentifizierungsmethode angegeben.
- Die SAS (Shared Access Signature) wird in die Eigenschaft mit den Authentifizierungsdaten eingefügt. Bisher wurde dafür das Feld für das Kennwort verwendet.
- Die Themen für Vorgänge haben sich geändert:
  - Telemetrie: `$iothub/telemetry` statt `devices/{Client Id}/messages/events`
  - Befehle: `$iothub/commands` statt `devices/{Client Id}/messages/devicebound`
  - Gemeldete Patchzwillinge: `$iothub/twin/patch/reported` statt `$iothub/twin/PATCH/properties/reported`
  - Benachrichtigen des Zwillings bei Änderung des gewünschten Zustands: `$iothub/twin/patch/desired` statt `$iothub/twin/PATCH/properties/desired`
- Für das Antwortthema für Client-zu-Server-Vorgänge vom Typ „Anforderung/Antwort“ ist kein Abonnement erforderlich.
- Im Segment für den Themennamen werden anstelle von codierten Eigenschaften Benutzereigenschaften verwendet.
- Bei Eigenschaftennamen wird eine Schreibung mit Bindestrichen anstelle von Abkürzungen mit einem speziellen Präfix verwendet. Jetzt erfordern stattdessen benutzerdefinierte Eigenschaften ein Präfix. Beispielsweise ist `$.mid` jetzt `message-id`, während `myProperty1` zu `@myProperty1` wird.
- Anforderungs- und Antwortnachrichten für Vorgänge vom Typ „Anforderung/Antwort“ werden nicht mehr mit der im Thema codierten `$rid`-Eigenschaft, sondern mithilfe der Eigenschaften mit Korrelationsdaten korreliert.
- Die `iothub-connection-auth-method`-Eigenschaft wird nicht mehr als Stempel für Telemetrieereignisse verwendet.
- C2D-Befehle werden nicht gelöscht, wenn das Abonnement vom Gerät fehlt. Sie bleiben so lange in der Warteschlange, bis das Gerät ein Abonnement einrichtet oder bis sie ablaufen.

## <a name="examples"></a>Beispiele

### <a name="send-telemetry"></a>Senden von Telemetriedaten

Meldung:

```yaml
-> PUBLISH
    QoS: 1
    Packet_Id: 31
    Topic: $iothub/telemetry
    @myProperty1: My String Value # optional
    creation-time: 1600987195320 # optional
    @ No_Rules-ForUser-PROPERTIES: Any UTF-8 string value # optional
    Payload: <data>
```

Bestätigung:

```yaml
<- PUBACK
    Packet_Id: 31
    Reason_Code: 0
```

Alternative Bestätigung (gedrosselt):

```yaml
<- PUBACK
    Packet_Id: 31
    Reason_Code: 151
    status: 0501
```

---

### <a name="send-get-twins-state"></a>Senden einer Anforderung zum Abrufen des Zwillingsstatus

Anforderung:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/get
    Correlation_Data: 0x01 0xFA
    Payload: <empty>
```

Antwort (Erfolg):

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    Payload: <twin/desired state>
```

Antwort (nicht zulässig):

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x01 0xFA
    status: 0102
    reason: Operation not allowed for `B2` SKU
    Payload: <empty>
```

---

### <a name="handle-direct-method-call"></a>Verarbeiten von Aufrufen direkter Methoden

Anforderung:

```yaml
<- PUBLISH
    QoS: 0
    Topic: $iothub/methods/abc
    Correlation_Data: 0x0A 0x10
    Payload: <data>
```

Antwort (Erfolg):

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x0A 0x10
    response-code: 200 # user defined response code
    Payload: <data>
```

> [!NOTE]
> `status` wurde nicht festgelegt, sondern es ist eine Antwort für einen Erfolg.

Antwort bei einem nicht verfügbaren Gerät:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/responses
    Correlation_Data: 0x0A 0x10
    status: 0603
```

---

### <a name="error-while-using-qos-0-part-1"></a>Fehler bei der Verwendung von QoS 0, Teil 1

Anforderung:

```yaml
-> PUBLISH
    QoS: 0
    Topic: $iothub/twin/gett # misspelled topic name - server won't recognize it as Request-Response interaction
    Correlation_Data: 0x0A 0x10
    Payload: <data>
```

Antwort:

```yaml
<- DISCONNECT
    Reason_Code: 144
    reason: "Unsupported topic: `$iothub/twin/gett`"
```

---

### <a name="error-while-using-qos-0-part-2"></a>Fehler bei der Verwendung von QoS 0, Teil 2

Anforderung:

```yaml
-> PUBLISH # missing Correlation Data
    QoS: 0
    Topic: $iothub/twin/get
    Payload: <data>
```

Antwort:

```yaml
<- DISCONNECT
    Reason_Code: 131
    status: 0100
    reason: "`Correlation Data` property is missing"
```
## <a name="next-steps"></a>Nächste Schritte

- Die API-Referenz für die Vorschauversion von MQTT 5 finden Sie in der [MQTT 5-API-Referenz zur IoT Hub-Datenebene](iot-hub-mqtt-5-reference.md).
- Ein C#-Beispiel finden Sie im [GitHub-Beispielrepository](https://github.com/Azure-Samples/iot-hub-mqtt-5-preview-samples-csharp).