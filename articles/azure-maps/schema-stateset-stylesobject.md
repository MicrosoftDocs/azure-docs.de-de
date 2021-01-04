---
title: StylesObject-Schemareferenzhandbuch für dynamische Azure Maps-Karten
description: Referenzhandbuch zum StylesObject-Schema und zur Syntax für dynamische Azure Maps-Karten.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: reference
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 08379e66c97d34eea53410190475e90e156a58e2
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96903342"
---
# <a name="stylesobject-schema-reference-guide-for-dynamic-maps"></a>StylesObject-Schemareferenzhandbuch für dynamische Karten

> [!IMPORTANT]
> Azure Maps Creator-Dienste befinden sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 Das `StylesObject` ist ein `StyleObject`-Array, das Zustandsset-Formatvorlagen darstellt. Verwenden Sie den [Featurezustandsdienst](/rest/api/maps/featurestate) von Azure Maps Creator (Vorschau), um Ihre Zustandsset-Formatvorlagen auf die Features von Gebäudeplandaten anzuwenden. Nachdem Sie Ihre Zustandsset-Formatvorlagen erstellt und den Features von Gebäudeplänen zugeordnet haben, können Sie sie zum Erstellen dynamischer Gebäudepläne verwenden. Weitere Informationen zum Erstellen dynamischer Gebäudepläne finden Sie unter [Implementieren dynamischer Formatvorlagen für Gebäudepläne von Creator](indoor-map-dynamic-styling.md).

## <a name="styleobject"></a>StyleObject

Ein `StyleObject` ist eine der folgenden Formatvorlagenregeln:

 * [`BooleanTypeStyleRule`](#booleantypestylerule)
 * [`NumericTypeStyleRule`](#numerictypestylerule)
 * [`StringTypeStyleRule`](#stringtypestylerule)

Der folgende JSON-Code zeigt ein Beispiel für die Verwendung aller drei Formatvorlagentypen.  Mit der `BooleanTypeStyleRule` wird die dynamische Formatvorlage für Features festgelegt, deren `occupied`-Eigenschaft den Wert TRUE bzw. FALSE aufweist.  Mit der `NumericTypeStyleRule` wird die Formatvorlage für Features festgelegt, deren `temperature`-Eigenschaft in einem bestimmten Bereich liegt. Mit der `StringTypeStyleRule` werden schließlich die festgelegten Formatvorlagen mit dem `meetingType` abgeglichen.



```json
 "styles": [
     {
        "keyname": "occupied",
        "type": "boolean",
        "rules": [
            {
                "true": "#FF0000",
                "false": "#00FF00"
            }
        ]
    },
    {
        "keyname": "temperature",
        "type": "number",
        "rules": [
             {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "maximum": 70,
                "exclusiveMinimum": 30
              },
              "color": "#eba834"
            }
        ]
    },
    {
      "keyname": "meetingType",
      "type": "string",
      "rules": [
        {
          "private": "#FF0000",
          "confidential": "#FF00AA",
          "allHands": "#00FF00",
          "brownBag": "#964B00"
        }
      ]
    }
]
```

## <a name="numerictypestylerule"></a>NumericTypeStyleRule

 Eine `NumericTypeStyleRule` ist ein [`StyleObject`](#styleobject) und besteht aus folgenden Eigenschaften:

| Eigenschaft | type | BESCHREIBUNG | Erforderlich |
|-----------|----------|-------------|-------------|
| `keyName` | Zeichenfolge | Der *Zustand* oder der dynamische Eigenschaftsname. Ein `keyName` sollte im `StyleObject`-Array eindeutig sein.| Ja |
| `type` | Zeichenfolge | Der Wert ist „numerisch“. | Ja |
| `rules` | [`NumberRuleObject`](#numberruleobject)[]| Ein Array aus numerischen Formatvorlagenbereichen mit zugeordneten Farben. Jeder Bereich definiert eine Farbe, die verwendet werden soll, wenn der Wert von *state* den Bereich erfüllt.| Ja |

### <a name="numberruleobject"></a>NumberRuleObject

Ein `NumberRuleObject` besteht aus einem [`RangeObject`](#rangeobject) und einer `color`-Eigenschaft. Wenn der Wert von *Zustand* in den Bereich fällt, wird seine Farbe für die Anzeige die in der `color`-Eigenschaft angegebene Farbe.

Wenn Sie mehrere überlappende Bereiche definieren, ist die ausgewählte Farbe die Farbe, die im ersten Bereich definiert ist, der erfüllt wird.

Im folgenden JSON-Beispiel sind beide Bereiche „true“, wenn der Wert von *Zustand* zwischen 50 und 60 liegt. Die verwendete Farbe ist jedoch `#343deb`, da dies der erste Bereich in der Liste ist, der erfüllt wurde.

```json

    {
        "rules":[
            {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "minimum": 50,
                "maximum": 60
                },
                "color": "#eba834"
            }
        ]
    }
]
```

| Eigenschaft | type | BESCHREIBUNG | Erforderlich |
|-----------|----------|-------------|-------------|
| `range` | [RangeObject](#rangeobject) | Das [RangeObject](#rangeobject) definiert einen Satz logischer Bereichsbedingungen, die, wenn sie `true` sind, die Anzeigefarbe von *Zustand* in die in der Eigenschaft `color` angegebene Farbe ändern. Wenn `range` nicht angegeben ist, wird immer die in der Eigenschaft `color` definierte Farbe verwendet.   | Nein |
| `color` | Zeichenfolge | Die zu verwendende Farbe, wenn der Wert von „Zustand“ in den Bereich fällt. Die `color`-Eigenschaft ist eine JSON-Zeichenfolge in einem der folgenden Formate: <ul><li> Hexadezimalwerte im HTML-Format </li><li> RGB ("#ff0", "#ffff00", "rgb(255, 255, 0)")</li><li> RGBA ("rgba(255, 255, 0, 1)")</li><li> HSL("hsl(100, 50%, 50%)")</li><li> HSLA("hsla(100, 50%, 50%, 1)")</li><li> Vordefinierte HTML-Farbnamen wie Gelb oder Blau.</li></ul> | Ja |

### <a name="rangeobject"></a>RangeObject

Das `RangeObject` definiert einen numerischen Bereichswert eines [`NumberRuleObject`](#numberruleobject). Damit der Wert von *Zustand* in den Bereich fällt, müssen alle definierten Bedingungen „true“ sein.

| Eigenschaft | type | BESCHREIBUNG | Erforderlich |
|-----------|----------|-------------|-------------|
| `minimum` | double | Alle Zahlen x, die x ≥ `minimum`.| Nein |
| `maximum` | double | Alle Zahlen x, die x ≤  `maximum`. | Nein |
| `exclusiveMinimum` | double | Alle Zahlen x, die x > `exclusiveMinimum`.| Nein |
| `exclusiveMaximum` | double | Alle Zahlen x, die x < `exclusiveMaximum`.| Nein |

### <a name="example-of-numerictypestylerule"></a>Beispiel für NumericTypeStyleRule

Der folgende JSON-Code veranschaulicht einen `NumericTypeStyleRule` *Zustand* namens `temperature`. In diesem Beispiel enthält das [`NumberRuleObject`](#numberruleobject) zwei definierte Temperaturbereiche und die diesen zugeordneten Farbformatvorlagen. Wenn der Temperaturbereich 50–69 ist, sollte die Anzeige die Farbe `#343deb` verwenden.  Ist der Temperaturbereich 31–70, sollte die Anzeige die Farbe `#eba834` verwenden.

```json
{
    "keyname": "temperature",
    "type": "number",
    "rules":[
        {
            "range": {
            "minimum": 50,
            "exclusiveMaximum": 70
            },
            "color": "#343deb"
        },
        {
            "range": {
            "maximum": 70,
            "exclusiveMinimum": 30
            },
            "color": "#eba834"
        }
    ]
}
```

## <a name="stringtypestylerule"></a>StringTypeStyleRule

Eine `StringTypeStyleRule` ist ein [`StyleObject`](#styleobject) und besteht aus folgenden Eigenschaften:

| Eigenschaft | type | BESCHREIBUNG | Erforderlich |
|-----------|----------|-------------|-------------|
| `keyName` | Zeichenfolge |  Der *Zustand* oder der dynamische Eigenschaftsname.  Ein `keyName` sollte im `StyleObject`-Array eindeutig sein.| Ja |
| `type` | Zeichenfolge |Der Wert lautet „string“. | Ja |
| `rules` | [`StringRuleObject`](#stringruleobject)[]| Ein Array von N *state*-Werten.| Ja |

### <a name="stringruleobject"></a>StringRuleObject

Ein `StringRuleObject` besteht aus bis zu N Zustandswerten („state“), die die möglichen Zeichenfolgenwerte für die Eigenschaft eines Features sind. Wenn der Eigenschaftswert des Features mit keinem der definierten Zustandswerte übereinstimmt, hat dieses Feature keinen dynamischen Stil. Wenn Zustandswerte mehrfach angegeben werden, hat der erste Vorrang.

Beim Vergleich von Zeichenfolgenwerten wird die Groß-/Kleinschreibung beachtet.

| Eigenschaft | type | BESCHREIBUNG | Erforderlich |
|-----------|----------|-------------|-------------|
| `stateValue1` | Zeichenfolge | Die Farbe, wenn die Zeichenfolge im Wert „stateValue1“ lautet. | Nein |
| `stateValue2` | Zeichenfolge | Die Farbe, wenn die Zeichenfolge im Wert „stateValue2“ lautet. | Nein |
| `stateValueN` | Zeichenfolge | Die Farbe, wenn die Zeichenfolge im Wert „stateValueN“ lautet. | Nein |

### <a name="example-of-stringtypestylerule"></a>Beispiel für StringTypeStyleRule

Der folgende JSON-Code veranschaulicht eine `StringTypeStyleRule`, die die bestimmten Besprechungstypen zugeordneten Formatvorlagen definiert.

```json
    {
      "keyname": "meetingType",
      "type": "string",
      "rules": [
        {
          "private": "#FF0000",
          "confidential": "#FF00AA",
          "allHands": "#00FF00",
          "brownBag": "#964B00"
        }
      ]
    }

```

## <a name="booleantypestylerule"></a>BooleanTypeStyleRule

Eine `BooleanTypeStyleRule` ist ein [`StyleObject`](#styleobject) und besteht aus folgenden Eigenschaften:

| Eigenschaft | type | BESCHREIBUNG | Erforderlich |
|-----------|----------|-------------|-------------|
| `keyName` | Zeichenfolge |  Der *Zustand* oder der dynamische Eigenschaftsname.  Ein `keyName` sollte im `StyleObject`-Array eindeutig sein.| Ja |
| `type` | Zeichenfolge |Der Wert ist „boolesch“. | Ja |
| `rules` | [`BooleanRuleObject`](#booleanruleobject)[1]| Ein boolesches Paar mit Farben für `true`- und `false`-Werte von *Zustand*.| Ja |

### <a name="booleanruleobject"></a>BooleanRuleObject

Ein `BooleanRuleObject` definiert Farben für `true`- und `false`-Werte.

| Eigenschaft | type | BESCHREIBUNG | Erforderlich |
|-----------|----------|-------------|-------------|
| `true` | Zeichenfolge | Die zu verwendende Farbe, wenn der Wert von *Zustand* `true` ist. Die `color`-Eigenschaft ist eine JSON-Zeichenfolge in einem der folgenden Formate: <ul><li> Hexadezimalwerte im HTML-Format </li><li> RGB ("#ff0", "#ffff00", "rgb(255, 255, 0)")</li><li> RGBA ("rgba(255, 255, 0, 1)")</li><li> HSL("hsl(100, 50%, 50%)")</li><li> HSLA("hsla(100, 50%, 50%, 1)")</li><li> Vordefinierte HTML-Farbnamen wie Gelb oder Blau.</li></ul>| Ja |
| `false` | Zeichenfolge | Die zu verwendende Farbe, wenn der Wert von *Zustand* `false` ist. | Ja |

### <a name="example-of-booleantypestylerule"></a>Beispiel für BooleanTypeStyleRule

Der folgende JSON-Code veranschaulicht einen `BooleanTypeStyleRule` *Zustand* namens `occupied`. Das [`BooleanRuleObject`](#booleanruleobject) definiert Farben für `true`- und `false`-Werte.

```json
{
    "keyname": "occupied",
    "type": "boolean",
    "rules": [
    {
        "true": "#FF0000",
        "false": "#00FF00"
    }
    ]
}
```