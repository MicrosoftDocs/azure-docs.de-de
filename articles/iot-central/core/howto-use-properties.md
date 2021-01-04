---
title: Verwenden von Eigenschaften in einer Azure IoT Central-Lösung
description: Hier erfahren Sie, wie Sie schreibgeschützte und schreibbare Eigenschaften in einer Azure IoT Central-Lösung verwenden können.
author: dominicbetts
ms.author: dobett
ms.date: 11/06/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: a517f7a796b6543c8d60f0d1ebdba16afa0bc4b7
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96751426"
---
# <a name="use-properties-in-an-azure-iot-central-solution"></a>Verwenden von Eigenschaften in einer Azure IoT Central-Lösung

In diesem Artikel wird gezeigt, wie Sie Geräteeigenschaften verwenden können, die in Ihrer Azure IoT Central-Anwendung in einer Gerätevorlage definiert werden.

Eigenschaften stellen Zeitpunktwerte dar. Ein Gerät kann beispielsweise eine Eigenschaft verwenden, um die Zieltemperatur zu melden, die es zu erreichen versucht. Standardmäßig sind Geräteeigenschaften in IoT Central schreibgeschützt. Mithilfe von schreibbaren Eigenschaften können Sie den Zustand zwischen Ihrem Gerät und der Azure IoT Central-Anwendung synchronisieren.

Außerdem können Sie Cloudeigenschaften in einer Azure IoT Central-Anwendung definieren. Cloudeigenschaftswerte werden niemals mit einem Gerät ausgetauscht und deshalb in diesem Artikel nicht behandelt.

## <a name="define-your-properties"></a>Definieren Ihrer Eigenschaften

Eigenschaften sind Datenfelder, die den Zustand des Geräts darstellen. Verwenden Sie Eigenschaften zur Darstellung von dessen dauerhaftem Zustand, z. B. seinem „Ein/Aus“-Status. Eigenschaften können auch grundlegende Geräteeigenschaften darstellen, z. B. die Softwareversion des Geräts. Sie können Eigenschaften als schreibgeschützt oder schreibbar deklarieren.

Der folgende Screenshot zeigt eine Eigenschaftsdefinition in einer Azure IoT Central-Anwendung.

:::image type="content" source="media/howto-use-properties/property-definition.png" alt-text="Der Screenshot zeigt eine Eigenschaftsdefinition in einer Azure IoT Central-Anwendung.":::

In der folgenden Tabelle sind die Konfigurationseinstellungen für eine Eigenschaftsfunktion angegeben.

| Feld           | BESCHREIBUNG                                                                                                                                                                                                                        |
|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Anzeigename    | Der Anzeigename für den Eigenschaftswert, der in Dashboards und Formularen verwendet wird.                                                                                                                                                              |
| Name            | Der Name der Eigenschaft. Azure IoT Central generiert einen Wert für dieses Feld aus dem Anzeigenamen. Bei Bedarf können Sie aber einen eigenen Wert auswählen. Dieses Feld muss alphanumerisch sein.                                                 |
| Funktionstyp | Eigenschaft.                                                                                                                                                                                                                          |
| Semantischer Typ   | Der semantische Typ der Eigenschaft, z. B. Temperatur, Zustand oder Ereignis. Die Auswahl des semantischen Typs bestimmt, welches der folgenden Felder verfügbar ist.                                                                       |
| Schema          | Der Eigenschaftsdatentyp, z.B. „double“, „string“ oder „vector“. Die verfügbaren Optionen werden durch den semantischen Typ bestimmt. Schema ist für die semantischen Typen „Ereignis“ und „Zustand“ nicht verfügbar.                                               |
| Schreibbar       | Wenn die Eigenschaft nicht schreibbar ist, kann das Gerät Eigenschaftswerte an Azure IoT Central melden. Wenn die Eigenschaft schreibbar ist, kann das Gerät Eigenschaftswerte an Azure IoT Central melden. Anschließend kann Azure IoT Central Eigenschaftsaktualisierungen an das Gerät senden. |
| severity        | Nur für den semantischen Typ „Ereignis“ verfügbar. Die Schweregrade lauten **Fehler**, **Information** und **Warnung**.                                                                                                                         |
| Zustandswerte    | Nur für den semantischen Typ „Zustand“ verfügbar. Definieren Sie die möglichen Zustandswerte, die jeweils einen Anzeigenamen, Namen, Enumerationstyp und Wert umfassen.                                                                                   |
| Einheit            | Eine Einheit für den Eigenschaftswert, z. B. **km/h**, **%** oder **&deg;C**.                                                                                                                                                              |
| Anzeigeeinheit    | Eine Anzeigeeinheit zur Verwendung in Dashboards und Formularen.                                                                                                                                                                                    |
| Comment         | Beliebige Kommentare zur Eigenschaftsfunktion.                                                                                                                                                                                        |
| BESCHREIBUNG     | Eine Beschreibung der Eigenschaftsfunktion.                                                                                                                                                                                          |

Die Eigenschaften können auch in einer Schnittstelle in einer Gerätevorlage definiert werden, wie hier gezeigt wird:

``` json
{
  "@type": [
    "Property",
    "Temperature"
  ],
  "name": "targetTemperature",
  "schema": "double",
  "displayName": "Target Temperature",
  "description": "Allows to remotely specify the desired target temperature.",
  "unit" : "degreeCelsius",
  "writable": true
},
{
  "@type": [
    "Property",
    "Temperature"
  ],
  "name": "maxTempSinceLastReboot",
  "schema": "double",
  "unit" : "degreeCelsius",
  "displayName": "Max temperature since last reboot.",
  "description": "Returns the max temperature since last device reboot."
}
```

In diesem Beispiel werden zwei Eigenschaften gezeigt. Diese Eigenschaften beziehen sich auf die Eigenschaftsdefinition auf der Benutzeroberfläche:

* `@type` gibt den Typ der Funktion an: `Property`. Im vorherigen Beispiel wird auch der semantische Typ `Temperature` für beide Eigenschaften angezeigt.
* `name` der Eigenschaft.
* `schema` gibt den Datentyp für die Eigenschaft an. Dieser Wert kann ein einfacher Typ sein, z. B. „double“, „integer“, „Boolean“ oder „string“. Komplexe Objekttypen und Zuordnungen werden ebenfalls unterstützt.
* `writable` Eigenschaften sind standardmäßig schreibgeschützt. Mit diesem Feld können Sie eine Eigenschaft als „schreibbar“ kennzeichnen.

Über optionale Felder, z. B. Anzeigename und Beschreibung, können Sie der Schnittstelle und den Funktionen weitere Details hinzufügen.

Beim Erstellen einer Eigenschaft können Sie komplexe Schematypen wie **Objekt** und **Enumeration** angeben.

![Der Screenshot zeigt, wie eine Funktion hinzugefügt wird.](./media/howto-use-properties/property.png)

Wenn Sie das komplexe **Schema**, z. B. **Objekt** auswählen, müssen Sie auch das Objekt definieren.

:::image type="content" source="media/howto-use-properties/object.png" alt-text="Der Screenshot zeigt, wie ein Objekt definiert wird.":::

Der folgende Code zeigt die Definition eines Eigenschaftstyps „Objekt“. Dieses Objekt hat zwei Felder mit den Typen „string“ und „integer“.

``` json
{
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "schema": {
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "displayName": {
          "en": "Field2"
        },
        "name": "Field2",
        "schema": "string"
      }
    ]
  }
}
```

## <a name="implement-read-only-properties"></a>Implementieren von schreibgeschützten Eigenschaften

Standardmäßig sind Eigenschaften schreibgeschützt. Mit schreibgeschützten Eigenschaften kann das Gerät Aktualisierungen der Eigenschaftswerte an die Azure IoT Central-Anwendung melden. Ihre Azure IoT Central-Anwendung kann den Wert einer schreibgeschützten Eigenschaft nicht festlegen.

Azure IoT Central verwendet Gerätezwillinge zum Synchronisieren von Eigenschaftswerten zwischen dem Gerät und der Azure IoT Central-Anwendung. Die gemeldeten Eigenschaften von Gerätezwillingen werden für die Eigenschaftswerte eines Geräts verwendet. Weitere Informationen finden Sie unter [Gerätezwillinge](../../iot-hub/tutorial-device-twins.md).

Der folgende Codeausschnitt aus einem Gerätemodell zeigt die Definition eines schreibgeschützten Eigenschaftstyps:

``` json
{
  "name": "model",
  "displayName": "Device model",
  "schema": "string",
  "comment": "Device model name or ID. Ex. Surface Book 2."
}
```

Eigenschaftsaktualisierungen werden von einem Gerät als JSON-Nutzdaten gesendet. Weitere Informationen finden Sie unter [Payloads](./concepts-telemetry-properties-commands.md) (Nutzlasten).

Sie können mithilfe des Azure IoT-Geräte-SDKs eine Eigenschaftsaktualisierung an Ihre Azure IoT Central-Anwendung senden.

Gerätezwillingseigenschaften können mithilfe der folgenden Funktion an Ihre Azure IoT Central-Anwendung gesendet werden:

``` javascript
hubClient.getTwin((err, twin) => {
    const properties = {
        model: 'environmentalSensor1.0'
    };
    twin.properties.reported.update(properties, (err) => {
        console.log(err ? `error: ${err.toString()}` : `status: success` )
    });
});
```

In diesem Artikel wird der Einfachheit halber „Node.js“ verwendet. Vollständige Informationen und Anwendungsbeispiele für Geräte finden Sie im Tutorial [Erstellen einer Clientanwendung und Verbinden dieser Anwendung mit Ihrer Azure IoT Central-Anwendung](tutorial-connect-device.md).

Die folgende Ansicht in der Azure IoT Central-Anwendung zeigt die Eigenschaften, die Sie sehen können. In der Ansicht wird aus der Eigenschaft **Gerätemodell** automatisch eine _schreibgeschützte Geräteeigenschaft_.

:::image type="content" source="media/howto-use-properties/read-only.png" alt-text="Screenshot der Ansicht einer schreibgeschützten Eigenschaft":::

## <a name="implement-writable-properties"></a>Implementieren von schreibbaren Eigenschaften

Schreibbare Eigenschaften werden von einem Operator in der Azure IoT Central-Anwendung in einem Formular festgelegt. Azure IoT Central sendet die-Eigenschaft an das Gerät. Azure IoT Central erwartet eine Bestätigung vom Gerät.

Der folgende Codeausschnitt aus einem Gerätemodell zeigt die Definition eines schreibbaren Eigenschaftstyps:

``` json
{
  "@type": "Property",
  "displayName": "Brightness Level",
  "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
  "name": "brightness",
  "writable": true,
  "schema": "long"
}
```

Zum Definieren und Verarbeiten der schreibbaren Eigenschaften, auf die Ihr Gerät reagiert, können Sie den folgenden Code verwenden:

``` javascript
hubClient.getTwin((err, twin) => {
    twin.on('properties.desired.brightness', function(desiredBrightness) {
        console.log( `Received setting: ${desiredBrightness.value}` );
        var patch = {
            brightness: {
                value: desiredBrightness.value,
                ad: 'success',
                ac: 200,
                av: desiredBrightness.$version
            }
        }
        twin.properties.reported.update(patch, (err) => {
            console.log(err ? `error: ${err.toString()}` : `status: success` )
        });
    });
});
```

Die Antwortnachricht sollte die Felder `ac` und `av` enthalten. Das Feld `ad` ist optional. Beispiele hierzu finden Sie in den folgenden Codeausschnitten:

* `ac` ist ein numerisches Feld, in dem die Werte in der folgenden Tabelle verwendet werden.
* `av` ist die an das Gerät gesendete Versionsnummer.
* `ad` ist eine Beschreibung der Optionszeichenfolge.

| Wert | Bezeichnung | BESCHREIBUNG |
| ----- | ----- | ----------- |
| `'ac': 200` | Abgeschlossen | Der Vorgang einer Eigenschaftsänderung wurde erfolgreich abgeschlossen. |
| `'ac': 202` oder `'ac': 201` | Ausstehend | Der Vorgang einer Eigenschaftsänderung ist ausstehend oder in Bearbeitung. |
| `'ac': 4xx` | Fehler | Die angeforderte Eigenschaftsänderung war ungültig oder fehlerhaft. |
| `'ac': 5xx` | Fehler | Beim Verarbeiten der angeforderten Änderung ist auf dem Gerät ein unerwarteter Fehler aufgetreten. |

Weitere Informationen zu Gerätezwillingen finden Sie unter [Konfigurieren Ihrer Geräte über einen Back-End-Dienst](../../iot-hub/tutorial-device-twins.md).

Wenn der Operator in der Azure IoT Central-Anwendung eine schreibbare Eigenschaft festlegt, verwendet die Anwendung eine gewünschte Eigenschaft des Gerätezwillings, um den Wert an das Gerät zu senden. Das Gerät antwortet dann mithilfe einer gemeldeten Eigenschaft des Gerätezwillings. Wenn Azure IoT Central den gemeldeten Eigenschaftswert empfängt, wird die Eigenschaftsansicht mit dem Status **Akzeptiert** aktualisiert.

In der folgenden Ansicht werden die schreibbaren Eigenschaften gezeigt. Wenn Sie den Wert eingeben und **Speichern** auswählen, lautet der Anfangsstatus **Ausstehend**. Wenn das Gerät die Änderung akzeptiert, wird der Status in **Akzeptiert** geändert.

![Der Screenshot zeigt den Status „Ausstehend“.](./media/howto-use-properties/status-pending.png)

![Der Screenshot zeigt die Eigenschaft „Akzeptiert“.](./media/howto-use-properties/accepted.png)

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie erfahren haben, wie Sie Eigenschaften in Ihrer Azure IoT Central-Anwendung verwenden können, lesen Sie jetzt:

* [Payloads](concepts-telemetry-properties-commands.md)
* [Tutorial: Erstellen einer Node.js-Clientanwendung und Verbinden der Anwendung mit Ihrer Azure IoT Central-Anwendung (Node.js)](tutorial-connect-device.md)