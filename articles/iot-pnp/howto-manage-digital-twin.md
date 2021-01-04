---
title: Verwalten von digitalen IoT Plug & Play-Zwillingen
description: Verwalten eines IoT Plug & Play-Geräts mithilfe von APIs für digitale Zwillinge
author: prashmo
ms.author: prashmo
ms.date: 07/20/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 51bd073b6f84f3378b91091e04f2f278ea2f49b6
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95495268"
---
# <a name="manage-iot-plug-and-play-digital-twins"></a>Verwalten von digitalen IoT Plug & Play-Zwillingen

IoT Plug & Play unterstützt die Vorgänge **Get digital twin** (Abrufen des digitalen Zwillings) und **Update digital twin** (Aktualisieren des digitalen Zwillings) zum Verwalten von digitalen Zwillingen. Sie können dafür entweder die [REST-APIs](/rest/api/iothub/service/digitaltwin) oder eines der [Dienst-SDKs](libraries-sdks.md) verwenden.

Zum Zeitpunkt der Artikelerstellung ist die API-Version des digitalen Zwillings `2020-09-30`.

## <a name="update-a-digital-twin"></a>Aktualisieren eines digitalen Zwillings

Ein IoT-Plug & Play-Gerät implementiert ein Modell, das durch die [Digital Twins Definition Language v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) beschrieben wird. Lösungsentwickler können mithilfe der **Update Digital Twin API** (API zum Aktualisieren des digitalen Zwillings) den Status der Komponente und die Eigenschaften des digitalen Zwillings aktualisieren.

Das in diesem Artikel als Beispiel verwendete IoT Plug & Play-Gerät implementiert das [Modell „Temperatur-Controller“](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) mit [Thermostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)-Komponenten.

Der folgende Codeausschnitt zeigt die Antwort auf eine Anforderung **Get Digital Twin** (Abrufen des digitalen Zwillings), die als JSON-Objekt formatiert ist. Weitere Informationen zum Format eines digitalen Zwillings finden Sie unter [Grundlegendes zu digitalen IoT Plug & Play-Zwillingen](./concepts-digital-twin.md#digital-twin-json-format):

```json
{
    "$dtId": "sample-device",
    "serialNumber": "alwinexlepaho8329",
    "thermostat1": {
        "maxTempSinceLastReboot": 25.3,
        "targetTemperature": 20.4,
        "$metadata": {
            "targetTemperature": {
                "desiredValue": 20.4,
                "desiredVersion": 4,
                "ackVersion": 4,
                "ackCode": 200,
                "ackDescription": "Successfully executed patch",
                "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
            },
            "maxTempSinceLastReboot": {
                "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
            }
        }
    },
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

Digitale Zwillinge ermöglichen es Ihnen, eine ganze Komponente oder Eigenschaft mithilfe eines [JSON-Patches](http://jsonpatch.com/) zu aktualisieren.

Beispielsweise können Sie die Eigenschaft `targetTemperature`so aktualisieren:

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    }
]
```

Mit dem vorherigen Update wird der gewünschte Wert einer Eigenschaft in den entsprechenden `$metadata` auf Komponentenebene festgelegt, wie im folgenden Codeausschnitt gezeigt wird. IoT Hub aktualisiert die gewünschte Version der Eigenschaft:

```json
"thermostat1": {
    "targetTemperature": 20.4,
    "$metadata": {
        "targetTemperature": {
            "desiredValue": 21.4,
            "desiredVersion": 5,
            "ackVersion": 4,
            "ackCode": 200,
            "ackDescription": "Successfully executed patch",
            "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
        }
    }
}
```

### <a name="add-replace-or-remove-a-component"></a>Hinzufügen, Ersetzen oder Entfernen einer Komponente

Vorgänge auf Komponentenebene erfordern einen leeren `$metadata`-Objektmarker innerhalb des Werts.

Ein Vorgang zum Hinzufügen oder Ersetzen von Komponenten legt die gewünschten Werte für alle bereitgestellten Eigenschaften fest. Außerdem werden die gewünschten Werte für alle schreibbaren Eigenschaften gelöscht, die mit dem Update nicht bereitgestellt wurden.

Beim Entfernen einer Komponente werden die gewünschten Werte aller vorhandenen schreibbaren Eigenschaften gelöscht. Ein Gerät synchronisiert schließlich diesen Entfernungsvorgang und beendet die Berichterstellung für die einzelnen Eigenschaften. Danach wird die Komponente aus dem digitalen Zwilling entfernt.

Das folgende JSON-Patchbeispiel zeigt, wie eine Komponente hinzugefügt, ersetzt oder entfernt wird:

```json
[
    {
        "op": "add",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "anotherWritableProperty": 42,
            "$metadata": {}
        }
    },
    {
        "op": "replace",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "$metadata": {}
        }
    },
    {
        "op": "remove",
        "path": "/thermostat2"
    }
]
```

### <a name="add-replace-or-remove-a-property"></a>Hinzufügen, Ersetzen oder Entfernen einer Eigenschaft

Durch einen Vorgang zum Hinzufügen oder Ersetzen wird der gewünschte Wert einer Eigenschaft festgelegt. Das Gerät kann den Status synchronisieren und eine Aktualisierung des Werts zusammen mit einem `ack`-Code, einer Version und einer Beschreibung melden.

Beim Entfernen einer Eigenschaft wird deren gewünschter Wert gelöscht, falls er festgelegt wurde. Das Gerät kann dann die Berichterstellung für diese Eigenschaft beenden, und sie wird aus der Komponente entfernt. Wenn diese Eigenschaft die letzte in der Komponente ist, wird die Komponente ebenfalls entfernt.

Das folgende JSON-Patchbeispiel zeigt, wie eine Eigenschaft in einer Komponente hinzugefügt, ersetzt oder entfernt wird:

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    },
    {
        "op": "replace",
        "path": "/thermostat1/anotherWritableProperty",
        "value": 42
    },
    {
        "op": "remove",
        "path": "/thermostat2/targetTemperature",
    }
]
```

### <a name="rules-for-setting-the-desired-value-of-a-digital-twin-property"></a>Regeln zum Festlegen des gewünschten Werts für eine Eigenschaft des digitalen Zwillings

**Name**

Der Name einer Komponente oder Eigenschaft muss ein gültiger DTDL v2-Name sein.

Zulässige Zeichen sind a–z, A–Z, 0–9 (nicht als erstes Zeichen) und Unterstrich (nicht als erstes oder letztes Zeichen).

Ein Name kann 1 bis 64 Zeichen lang sein.

**Eigenschaftswert**

Der Wert muss eine gültige [DTDL v2-Eigenschaft](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property) sein.

Alle einfachen Typen werden unterstützt. Innerhalb komplexer Typen werden Enumerationen, Zuordnungen und Objekte unterstützt. Weitere Informationen finden Sie unter [DTDL v2-Schemas](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#schemas).

Eigenschaften unterstützen kein Array oder kein komplexes Schema mit einem Array.

Bei einem komplexen Objekt wird eine maximale Tiefe von fünf Ebenen unterstützt.

Alle Feldnamen innerhalb eines komplexen Objekts sollten gültige DTDL v2-Namen sein.

Alle Zuordnungsschlüssel sollten gültige DTDL v2-Namen sein.

## <a name="troubleshoot-update-digital-twin-api-errors"></a>Beheben von API-Fehlern beim Aktualisieren von digitalen Zwillingen

Die Digital Twin-API löst die folgende generische Fehlermeldung aus:

`ErrorCode:ArgumentInvalid;'{propertyName}' exists within the device twin and is not digital twin conformant property. Please refer to aka.ms/dtpatch to update this to be conformant.`

Wenn Sie diesen Fehler sehen, vergewissern Sie sich, dass der Updatepatch die [Regeln zum Festlegen des gewünschten Werts für eine Eigenschaft des digitalen Zwillings](#rules-for-setting-the-desired-value-of-a-digital-twin-property) einhält.

Stellen Sie beim Aktualisieren einer Komponente sicher, dass der [leere Objektmarker „$metadata“](#add-replace-or-remove-a-component) festgelegt wird.

Updates können fehlschlagen, wenn die gemeldeten Werte eines Geräts den [IoT Plug & Play-Konventionen](./concepts-convention.md#writable-properties) nicht entsprechen.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit digitalen Zwillingen vertraut gemacht haben, finden Sie weitere Informationen in folgenden Artikeln:

- [Interagieren mit einem Gerät über Ihre Lösung](quickstart-service.md)
- [IoT-REST-API für digitale Zwillinge](/rest/api/iothub/service/digitaltwin)
- [Azure IoT-Explorer](howto-use-iot-explorer.md)