---
title: Grundlegendes zur Azure IoT Hub-Abfragesprache | Microsoft Docs
description: 'Entwicklerhandbuch: Beschreibung der SQL-ähnlichen IoT Hub-Abfragesprache, die zum Abrufen von Informationen zu Geräte-/Modulzwillingen und Aufträgen von IoT Hub verwendet wird.'
author: rezasherafat
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: rezas
ms.openlocfilehash: e5387f1e44a55b0a30f8620b49d237ac1e1ec2b6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61442085"
---
# <a name="iot-hub-query-language-for-device-and-module-twins-jobs-and-message-routing"></a>IoT Hub-Abfragesprache für Geräte- und Modulzwillinge, Aufträge und Nachrichtenrouting

IoT Hub bietet eine leistungsstarke, SQL-ähnliche Sprache zum Abrufen von Informationen zu [Gerätezwillingen](iot-hub-devguide-device-twins.md), [Aufträgen](iot-hub-devguide-jobs.md) und [Nachrichtenrouting](iot-hub-devguide-messages-d2c.md). Dieser Artikel enthält Folgendes:

* Eine Einführung in die wichtigsten Features der IoT Hub-Abfragesprache
* Eine ausführliche Beschreibung der Sprache Weitere Informationen zur Abfragesprache für das Nachrichtenrouting finden Sie unter [Abfragen im Nachrichtenrouting](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="device-and-module-twin-queries"></a>Abfragen von Geräte- und Modulzwillingen

[Gerätezwillinge](iot-hub-devguide-device-twins.md) und Modulzwillinge können beliebige JSON-Objekte als Tags und Eigenschaften enthalten. In IoT Hub können Sie Geräte- und Modulzwillinge als einzelnes JSON-Dokument abfragen, das alle Informationen zum Zwilling enthält.

Angenommen, Ihre IoT Hub-Gerätezwillinge weisen die folgende Struktur auf (Modulzwillinge sehen ähnlich aus, enthalten aber zusätzlich eine Modul-ID):

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "Disconnected",
    "lastActivityTime": "0001-01-01T00:00:00",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "tags": {
        "location": {
            "region": "US",
            "plant": "Redmond43"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300
            },
            "$metadata": {
            ...
            },
            "$version": 4
        },
        "reported": {
            "connectivity": {
                "type": "cellular"
            },
            "telemetryConfig": {
                "configId": "db00ebf5-eeeb-42be-86a1-458cccb69e57",
                "sendFrequencyInSecs": 300,
                "status": "Success"
            },
            "$metadata": {
            ...
            },
            "$version": 7
        }
    }
}
```

### <a name="device-twin-queries"></a>Gerätezwillingabfragen

IoT Hub macht die Gerätezwillinge als eine Dokumentensammlung namens **Geräte** verfügbar. Beispielsweise ruft die folgende Abfrage die gesamte Gruppe von Gerätezwillingen ab:

```sql
SELECT * FROM devices
```

> [!NOTE]
> [Azure IoT SDKs](iot-hub-devguide-sdks.md) unterstützen die seitenweise Ausgabe von umfangreichen Ergebnissen.

IoT Hub ermöglicht beim Abrufen von Gerätezwillingen das Filtern mit beliebigen Bedingungen. Verwenden Sie z. B. die folgende Abfrage, um Gerätezwillinge zu erhalten, bei denen das Tag **location.region** auf **US** festgelegt ist:

```sql
SELECT * FROM devices
WHERE tags.location.region = 'US'
```

Boolesche Operatoren und arithmetische Vergleiche werden ebenfalls unterstützt. Verwenden Sie z. B. die folgende Abfrage, um Gerätezwillinge in den USA abzurufen, die so konfiguriert sind, dass sie Telemetriedaten häufiger als jede Minute senden:

```sql
SELECT * FROM devices
  WHERE tags.location.region = 'US'
    AND properties.reported.telemetryConfig.sendFrequencyInSecs >= 60
```

Zur Vereinfachung können auch Arraykonstanten mit den Operatoren **IN** und **NIN** (nicht IN) verwendet werden. Verwenden Sie z. B. die folgende Abfrage, um Gerätezwillinge abzurufen, die WLAN- oder Kabelverbindungen melden:

```sql
SELECT * FROM devices
  WHERE properties.reported.connectivity IN ['wired', 'wifi']
```

Es ist häufig erforderlich, alle Gerätezwillinge zu ermitteln, die eine bestimmte Eigenschaft enthalten. IoT Hub unterstützt zu diesem Zweck die Funktion `is_defined()`. Verwenden Sie z. B. die folgende Abfrage, um Gerätezwillinge abzurufen, die die `connectivity`-Eigenschaft definieren:

```SQL
SELECT * FROM devices
  WHERE is_defined(properties.reported.connectivity)
```

Die vollständige Referenz zu den Filtermöglichkeiten finden Sie im Abschnitt zur [WHERE-Klausel](iot-hub-devguide-query-language.md#where-clause).

Gruppierungen und Aggregationen werden ebenfalls unterstützt. Verwenden Sie z. B. die folgende Abfrage, um die Anzahl von Geräten mit dem jeweiligen Status für die Telemetriekonfiguration zu ermitteln:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
  FROM devices
  GROUP BY properties.reported.telemetryConfig.status
```

Diese Gruppierungsabfrage würde ein ähnliches Ergebnis wie im folgenden Beispiel zurückgegeben:

```json
[
    {
        "numberOfDevices": 3,
        "status": "Success"
    },
    {
        "numberOfDevices": 2,
        "status": "Pending"
    },
    {
        "numberOfDevices": 1,
        "status": "Error"
    }
]
```

In diesem Beispiel meldeten drei Geräte eine erfolgreiche Konfiguration, zwei wenden die Konfiguration noch an und ein Gerät hat einen Fehler gemeldet.

Mithilfe von Projektionsabfragen können Entwickler nur die interessierenden Eigenschaften zurückgeben. Um beispielsweise den Zeitpunkt der letzten Aktivität aller getrennten Geräte abzurufen, verwenden Sie die folgende Abfrage:

```sql
SELECT LastActivityTime FROM devices WHERE status = 'enabled'
```

### <a name="module-twin-queries"></a>Modulzwillingsabfragen

Das Abfragen von Modulzwillingen ähnelt dem Abfragen von Gerätezwillingen. Allerdings wird dabei eine andere Sammlung/ein anderer Namespace verwendet. Anstelle von „from devices“ können Sie „device.modules“ abfragen:

```sql
SELECT * FROM devices.modules
```

Eine Verknüpfung zwischen den Sammlungen „devices“ und „devices.modules“ ist nicht zulässig. Wenn Sie Modulzwillinge geräteübergreifend abfragen möchten, müssen dazu Tags verwendet werden. Die folgende Abfrage gibt alle Modulzwillinge auf allen Geräten mit dem Status „scanning“ zurück:

```sql
SELECT * FROM devices.modules WHERE properties.reported.status = 'scanning'
```

Die folgende Abfrage gibt alle Modulzwillinge mit dem Status „scanning“ nur für die angegebene Teilmenge der Geräte zurück:

```sql
SELECT * FROM devices.modules
  WHERE properties.reported.status = 'scanning'
  AND deviceId IN ['device1', 'device2']
```

### <a name="c-example"></a>C#-Beispiel

Die Abfragefunktion wird durch das [C#-Dienst-SDK](iot-hub-devguide-sdks.md) in der **RegistryManager**-Klasse verfügbar gemacht.

Es folgt ein Beispiel für eine einfache Abfrage:

```csharp
var query = registryManager.CreateQuery("SELECT * FROM devices", 100);
while (query.HasMoreResults)
{
    var page = await query.GetNextAsTwinAsync();
    foreach (var twin in page)
    {
        // do work on twin object
    }
}
```

Das Objekt **query** wird mit einer Seitengröße (bis zu 100) instanziiert. Dann erfolgt der Abruf mehrerer Seiten, indem mehrmals die **GetNextAsTwinAsync**-Methoden aufgerufen werden.

Das Abfrageobjekt macht mehrere **Next**-Werte verfügbar, abhängig von der Deserialisierungsoption, die von der Abfrage benötigt werden. Beispielsweise Gerätezwillings- bzw. Auftragsobjekte oder einfachen JSON-Text bei der Verwendung von Projektionen.

### <a name="nodejs-example"></a>Node.js-Beispiel

Die Abfragefunktion wird durch das [Azure IoT-Dienst-SDK für Node.js](iot-hub-devguide-sdks.md) im **Registry**-Objekt verfügbar gemacht.

Es folgt ein Beispiel für eine einfache Abfrage:

```javascript
var query = registry.createQuery('SELECT * FROM devices', 100);
var onResults = function(err, results) {
    if (err) {
        console.error('Failed to fetch the results: ' + err.message);
    } else {
        // Do something with the results
        results.forEach(function(twin) {
            console.log(twin.deviceId);
        });

        if (query.hasMoreResults) {
            query.nextAsTwin(onResults);
        }
    }
};
query.nextAsTwin(onResults);
```

Das Objekt **query** wird mit einer Seitengröße (bis zu 100) instanziiert. Dann erfolgt der Abruf mehrerer Seiten, indem mehrmals die **nextAsTwin**-Methode aufgerufen wird.

Das Abfrageobjekt macht mehrere **Next**-Werte verfügbar, abhängig von der Deserialisierungsoption, die von der Abfrage benötigt werden. Beispielsweise Gerätezwillings- bzw. Auftragsobjekte oder einfachen JSON-Text bei der Verwendung von Projektionen.

### <a name="limitations"></a>Einschränkungen

> [!IMPORTANT]
> Abfrageergebnisse können mit einigen Minuten Verzögerung im Vergleich zu den aktuellen Werten in Gerätezwillingen ausgegeben werden. Wenn Sie einzelne Gerätezwillinge per ID abfragen, verwenden Sie die API zum Abrufen von Gerätezwillingen. Diese API enthält immer die aktuellen Werte und verfügt über höhere Einschränkungsgrenzwerte.

Derzeit werden Vergleiche nur zwischen primitiven Typen (keine Objekte) unterstützt. `... WHERE properties.desired.config = properties.reported.config` wird beispielsweise nur unterstützt, wenn diese Eigenschaften über primitive Werte verfügen.

## <a name="get-started-with-jobs-queries"></a>Erste Schritte mit Auftragsabfragen

[Aufträge](iot-hub-devguide-jobs.md) bieten eine Möglichkeit zum Ausführen von Vorgängen für Gerätegruppen. Jeder Gerätezwilling enthält die Informationen der auf ihn bezogenen Aufträge in einer Sammlung namens **jobs**.

```json
{
    "deviceId": "myDeviceId",
    "etag": "AAAAAAAAAAc=",
    "tags": {
        ...
    },
    "properties": {
        ...
    },
    "jobs": [
        {
            "deviceId": "myDeviceId",
            "jobId": "myJobId",
            "jobType": "scheduleTwinUpdate",
            "status": "completed",
            "startTimeUtc": "2016-09-29T18:18:52.7418462",
            "endTimeUtc": "2016-09-29T18:20:52.7418462",
            "createdDateTimeUtc": "2016-09-29T18:18:56.7787107Z",
            "lastUpdatedDateTimeUtc": "2016-09-29T18:18:56.8894408Z",
            "outcome": {
                "deviceMethodResponse": null
            }
        },
        ...
    ]
}
```

Diese Sammlung kann derzeit in der IoT Hub-Abfragesprache über **devices.jobs** abgefragt werden.

> [!IMPORTANT]
> Die „jobs“-Eigenschaft wird derzeit nie zurückgegeben, wenn Gerätezwillinge abgefragt werden. Hierbei handelt es sich um Abfragen, die „FROM devices“ enthalten. Auf die „jobs“-Eigenschaft kann nur direkt mit Abfragen unter Verwendung von `FROM devices.jobs` zugegriffen werden.
>
>

Um alle Aufträge (vergangene und geplante) abzurufen, die ein einzelnes Gerät betreffen, können Sie z.B. die folgende Abfrage verwenden:

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
```

Beachten Sie, wie diese Abfrage den gerätespezifischen Status (und möglicherweise die direkte Antwortmethode) jedes zurückgegebenen Auftrags bereitstellt.

Es ist auch möglich, mit beliebigen booleschen Bedingungen alle Objekteigenschaften in der Sammlung **devices.jobs** zu filtern.

Verwenden Sie z. B. die folgende Abfrage, um alle abgeschlossenen Aktualisierungsaufträge von Gerätezwillingen abzurufen, die nach September 2016 für ein bestimmtes Gerät erstellt wurden:

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.deviceId = 'myDeviceId'
    AND devices.jobs.jobType = 'scheduleTwinUpdate'
    AND devices.jobs.status = 'completed'
    AND devices.jobs.createdTimeUtc > '2016-09-01'
```

Sie können auch die Ergebnisse pro Gerät eines einzelnen Auftrags abrufen.

```sql
SELECT * FROM devices.jobs
  WHERE devices.jobs.jobId = 'myJobId'
```

### <a name="limitations"></a>Einschränkungen

Derzeit wird für Abfragen von **devices.jobs** Folgendes nicht unterstützt:

* Projektionen, sodass nur `SELECT *` möglich ist.
* Bedingungen, die zusätzlich zu Auftragseigenschaften auf einen Gerätezwilling verweisen (siehe vorausgehender Abschnitt).
* Durchführung von Aggregationen, z.B. Zählen, Durchschnittsbildung, Gruppieren.

## <a name="basics-of-an-iot-hub-query"></a>Grundlagen von IoT Hub-Abfragen

Jede IoT Hub-Abfrage besteht aus einer SELECT- und einer FROM-Klausel mit optionalen WHERE- und GROUP BY-Klauseln. Jede Abfrage wird für eine Sammlung von JSON-Dokumenten ausgeführt, z.B. Gerätezwillinge. Die FROM-Klausel zeigt die Dokumentsammlung an, die durchlaufen werden soll (**devices** oder **devices.jobs**). Anschließend wird der Filter in der WHERE-Klausel angewendet. Mit Aggregationen werden die Ergebnisse dieses Schritts gruppiert, wie in der GROUP BY-Klausel angegeben. Für jede Gruppe wird eine Zeile generiert, wie in der SELECT-Klausel angegeben.

```sql
SELECT <select_list>
  FROM <from_specification>
  [WHERE <filter_condition>]
  [GROUP BY <group_specification>]
```

## <a name="from-clause"></a>Die FROM-Klausel

Die Klausel **FROM <from_specification>** kann nur zwei Werte annehmen: **FROM-Geräte** zum Abfragen von Gerätezwillingen oder **FROM devices.jobs** beim Abfragen von Auftragsdetails pro Gerät.


## <a name="where-clause"></a>WHERE-Klausel
Die **WHERE <Filterbedingung>** -Klausel ist optional. Sie gibt eine oder mehrere Bedingungen an, die von den in der FROM-Sammlung enthaltenen JSON-Dokumenten erfüllt werden müssen, um als Teil des Ergebnisses zurückgegeben zu werden. Jedes JSON-Dokument muss die angegebenen Bedingungen erfüllen, um in das Ergebnis einbezogen zu werden.

Die zulässigen Bedingungen werden im Abschnitt [Ausdrücke und Bedingungen](iot-hub-devguide-query-language.md#expressions-and-conditions) beschrieben.

## <a name="select-clause"></a>Die SELECT-Klausel

Die **SELECT <Liste>** -Klausel ist obligatorisch und gibt an, welche Werte von der Abfrage abgerufen werden. Sie gibt die JSON-Werte an, mit denen die neuen JSON-Objekte erstellt werden sollen.
Für jedes Element der gefilterten (und optional gruppierten) Teilmenge der FROM-Sammlung wird in der Projektionsphase ein neues JSON-Objekt generiert. Dieses Objekt wird mit den in der SELECT-Klausel angegebenen Werten erstellt.

Dies ist die Grammatik der SELECT-Klausel:

```
SELECT [TOP <max number>] <projection list>

<projection_list> ::=
    '*'
    | <projection_element> AS alias [, <projection_element> AS alias]+

<projection_element> :==
    attribute_name
    | <projection_element> '.' attribute_name
    | <aggregate>

<aggregate> :==
    count()
    | avg(<projection_element>)
    | sum(<projection_element>)
    | min(<projection_element>)
    | max(<projection_element>)
```

**Attribute_name** bezieht sich auf jede Eigenschaft des JSON-Dokuments in der FROM-Sammlung. Im Abschnitt „Erste Schritte mit Gerätezwillingsabfragen“ finden Sie einige Beispiele für SELECT-Klauseln.

Derzeit werden andere Auswahlklauseln als **SELECT*** nur in Aggregatabfragen für Gerätezwillinge unterstützt.

## <a name="group-by-clause"></a>GROUP BY-Klausel
Die **GROUP BY <Gruppierungsspezifikation>** -Klausel ist ein optionaler Schritt, der nach dem in der WHERE-Klausel angegebenen Filter und vor der in der SELECT-Klausel angegebenen Projektion ausgeführt wird. Sie gruppiert Dokumente anhand des Werts eines Attributs. Diese Gruppen werden verwendet, um aggregierte Werte gemäß der SELECT-Klausel zu generieren.

Ein Beispiel für eine Abfrage mit GROUP BY:

```sql
SELECT properties.reported.telemetryConfig.status AS status,
    COUNT() AS numberOfDevices
FROM devices
GROUP BY properties.reported.telemetryConfig.status
```

Die formale Syntax für GROUP BY lautet:

```
GROUP BY <group_by_element>
<group_by_element> :==
    attribute_name
    | < group_by_element > '.' attribute_name
```

**Attribute_name** bezieht sich auf jede Eigenschaft des JSON-Dokuments in der FROM-Sammlung.

Die GROUP BY-Klausel wird derzeit nur für Abfragen von Gerätezwillingen unterstützt.

> [!IMPORTANT]
> Der Begriff `group` wird derzeit in Abfragen als spezielles Schlüsselwort behandelt. Wenn Sie `group` als Eigenschaftenname verwenden, sollten Sie ihn zur Vermeidung von Fehlern in doppelte Klammern einschließen, z.B. `SELECT * FROM devices WHERE tags.[[group]].name = 'some_value'`.
>
>

## <a name="expressions-and-conditions"></a>Ausdrücke und Bedingungen
Auf hoher Ebene wird ein *Ausdruck*:

* in eine Instanz eines JSON-Typs (z. B. boolescher Wert, Zahl, Zeichenfolge, Array oder Objekt) ausgewertet.
* durch das Ändern von Daten aus dem JSON-Dokument des Geräts und Konstanten mit integrierten Operatoren und Funktionen definiert.

*Bedingungen* sind Ausdrücke, die als boolescher Wert ausgewertet werden. Jede Konstante, die sich vom booleschen Ausdruck **true** unterscheidet, wird als **false** betrachtet. Diese Regel umfasst **null**, **undefined**, jede Objekt- oder Arrayinstanz, jede Zeichenfolge und den booleschen Ausdruck **false**.

Die Syntax für Ausdrücke lautet:

```
<expression> ::=
    <constant> |
    attribute_name |
    <function_call> |
    <expression> binary_operator <expression> |
    <create_array_expression> |
    '(' <expression> ')'

<function_call> ::=
    <function_name> '(' expression ')'

<constant> ::=
    <undefined_constant>
    | <null_constant>
    | <number_constant>
    | <string_constant>
    | <array_constant>

<undefined_constant> ::= undefined
<null_constant> ::= null
<number_constant> ::= decimal_literal | hexadecimal_literal
<string_constant> ::= string_literal
<array_constant> ::= '[' <constant> [, <constant>]+ ']'
```

Informationen dazu, wofür die einzelnen Symbole in der Ausdruckssyntax stehen, finden Sie in der folgenden Tabelle:

| Symbol | Definition |
| --- | --- |
| attribute_name | Eine beliebige Eigenschaft des JSON-Dokuments in der **FROM**-Sammlung. |
| binary_operator | Ein beliebiger binärer, im Abschnitt [Operatoren](#operators) aufgelisteter Operator. |
| function_name| Eine beliebige im Abschnitt [Funktionen](#functions) aufgelistete Funktion. |
| decimal_literal |Ein Gleitkommawert, ausgedrückt in Dezimalschreibweise. |
| hexadecimal_literal |Eine Zahl, ausgedrückt durch die Zeichenfolge „0x“ gefolgt von einer Zeichenfolge von Hexadezimalzahlen. |
| string_literal |Zeichenfolgenliterale sind Unicode-Zeichenfolgen, die durch eine Sequenz aus null oder mehr Unicode-Zeichen oder Escapesequenzen dargestellt werden. Zeichenfolgenliterale werden in einfache Anführungszeichen oder doppelte Anführungszeichen eingeschlossen. Zulässige Escapezeichen: `\'`, `\"`, `\\`, `\uXXXX` für Unicode-Zeichen, die durch 4 Hexadezimalstellen definiert werden. |

### <a name="operators"></a>Operatoren
Die folgenden Operatoren werden unterstützt:

| Familie | Operatoren |
| --- | --- |
| Arithmetik |+, -, *, /, % |
| Logisch |AND, OR, NOT |
| Vergleich |=, !=, <, >, <=, >=, <> |

### <a name="functions"></a>Functions
Bei Abfragen von Zwillingen und Aufträgen wird nur folgende Funktion unterstützt:

| Funktion | BESCHREIBUNG |
| -------- | ----------- |
| IS_DEFINED(Eigenschaft) | Gibt einen booleschen Wert zurück, um anzugeben, ob der Eigenschaft ein Wert zugewiesen wurde (inklusive `null`). |

In Routenbedingungen werden die folgenden mathematischen Funktionen unterstützt:

| Funktion | BESCHREIBUNG |
| -------- | ----------- |
| ABS(x) | Gibt den absoluten (positiven) Wert des angegebenen numerischen Ausdrucks zurück. |
| EXP(x) | Gibt den Exponentialwert des angegebenen numerischen Ausdrucks (e^x) zurück. |
| POWER(x,y) | Gibt den Wert des angegebenen Ausdrucks gemäß der angegebenen Potenz (x^y) zurück.|
| SQUARE(x) | Gibt das Quadrat des angegebenen numerischen Werts zurück. |
| CEILING(x) | Gibt den kleinsten ganzzahligen Wert zurück, der größer oder gleich dem angegebenen numerischen Ausdruck ist. |
| FLOOR(x) | Gibt den größten ganzzahligen Wert zurück, der kleiner oder gleich dem angegebenen numerischen Ausdruck ist. |
| SIGN(x) | Gibt das positive Vorzeichen (+1), null (0) oder das negative Vorzeichen (-1) des angegebenen numerischen Ausdrucks zurück.|
| SQRT(x) | Gibt die Quadratwurzel des angegebenen numerischen Werts zurück. |

In Routenbedingungen werden die folgenden Typüberprüfungs- und Umwandlungsfunktionen unterstützt:

| Funktion | BESCHREIBUNG |
| -------- | ----------- |
| AS_NUMBER | Konvertiert die Eingabezeichenfolge in eine Zahl. `noop`, wenn die Eingabe eine Zahl ist; `Undefined`, wenn die Zeichenfolge keine Zahl darstellt.|
| IS_ARRAY | Gibt einen booleschen Wert zurück, der angibt, ob der angegebene Ausdruck vom Typ „Array“ ist. |
| IS_BOOL | Gibt einen booleschen Wert zurück, der angibt, ob der angegebene Ausdruck vom Typ „boolesch“ ist. |
| IS_DEFINED | Gibt einen booleschen Wert zurück, um anzugeben, ob der Eigenschaft ein Wert zugewiesen wurde. |
| IS_NULL | Gibt einen booleschen Wert zurück, der angibt, ob der angegebene Ausdruck vom Typ „NULL“ ist. |
| IS_NUMBER | Gibt einen booleschen Wert zurück, der angibt, ob der angegebene Ausdruck vom Typ „Zahl“ ist. |
| IS_OBJECT | Gibt einen booleschen Wert zurück, der angibt, ob der angegebene Ausdruck vom Typ „JSON-Objekt“ ist. |
| IS_PRIMITIVE | Gibt einen booleschen Wert zurück, der angibt, ob der angegebene Ausdruck ein Grundtyp ist (Zeichenfolge, boolesch, numerisch oder `null`). |
| IS_STRING | Gibt einen booleschen Wert zurück, der angibt, ob der angegebene Ausdruck vom Typ „Zeichenfolge“ ist. |

In Routenbedingungen werden die folgenden Zeichenfolgenfunktionen unterstützt:

| Funktion | BESCHREIBUNG |
| -------- | ----------- |
| CONCAT(x, y, …) | Gibt eine Zeichenfolge zurück, die das Ergebnis der Verkettung von zwei oder mehr Zeichenfolgenwerten darstellt. |
| LENGTH(x) | Gibt die Anzahl der Zeichen im angegebenen Zeichenfolgenausdruck zurück.|
| LOWER(x) | Gibt eine Zeichenfolge zurück, nachdem Großbuchstaben in Kleinbuchstaben konvertiert wurden. |
| UPPER(x) | Gibt eine Zeichenfolge zurück, nachdem Kleinbuchstaben in Großbuchstaben konvertiert wurden. |
| SUBSTRING(Zeichenfolge, Start [, Länge]) | Gibt einen Teil eines Zeichenfolgenausdrucks zurück. Das angegebene Zeichen ist der Nullpunkt, von dem ab die Teilzeichenfolge in angegebener Länge bzw. bis zum Ende der Zeichenfolge zurückgegeben wird. |
| INDEX_OF(Zeichenfolge, Fragment) | Gibt die Anfangsposition des ersten Vorkommens des zweiten Zeichenfolgenausdrucks innerhalb des ersten angegebenen Zeichenfolgenausdrucks zurück, oder -1, wenn die Zeichenfolge nicht gefunden wird.|
| STARTS_WITH(x, y) | Gibt einen booleschen Wert zurück, um anzugeben, ob der erste Zeichenfolgenausdruck mit dem zweiten beginnt. |
| ENDS_WITH(x, y) | Gibt einen booleschen Wert zurück, um anzugeben, ob der erste Zeichenfolgenausdruck mit dem zweiten endet. |
| CONTAINS(x,y) | Gibt einen booleschen Wert zurück, um anzugeben, ob der erste Zeichenfolgenausdruck den zweiten enthält. |

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich darüber, wie Sie Abfragen in Ihren Apps mit [Azure IoT SDKs](iot-hub-devguide-sdks.md) ausführen.
