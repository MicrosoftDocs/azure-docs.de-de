---
title: Konfigurieren eines Mikrofonarrays – Speech Service
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie ein Mikrofonarray so konfigurieren, dass es vom Speech Devices SDK verwendet werden kann.
services: cognitive-services
author: mswellsi
manager: yanbo
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: wellsi
ms.openlocfilehash: cf0580c96f5bf78f0444b2bb39088f2a417fd658
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025060"
---
# <a name="how-to-configure-a-microphone-array"></a>Konfigurieren eines Mikrofonarrays

In diesem Artikel erfahren Sie, wie Sie ein [Mikrofonarray](./speech-devices-sdk-microphone.md) konfigurieren. Er umfasst die Einstellung des Arbeitswinkels und die Auswahl, welches Mikrofon für das Speech Devices SDK verwendet wird.

Das Speech Devices SDK funktioniert am besten mit einem Mikrofonarray, das nach [unseren Richtlinien](./speech-devices-sdk-microphone.md) entwickelt wurde. Die Konfiguration des Mikrofonarrays kann über das Betriebssystem oder durch eine der folgenden Methoden bereitgestellt werden.

Das Speech Devices SDK unterstützte Mikrofonarrays anfänglich durch Auswählen aus einem festen Satz von Konfigurationen.

```java
private static String DeviceGeometry = "Circular6+1"; // "Circular6+1", "Linear4",
private static String SelectedGeometry = "Circular6+1"; // "Circular6+1", "Circular3+1", "Linear4", "Linear2"
```

Unter Windows wird die Konfiguration des Mikrofonarrays vom Audiotreiber bereitgestellt.

Ab v1.11.0 unterstützt das Speech Devices SDK auch die Konfiguration über eine [JSON-Datei](https://aka.ms/sdsdk-micarray-json).


## <a name="windows"></a>Windows
Unter Windows werden die Geometrieinformationen des Mikrofonarrays automatisch vom Audiotreiber bezogen. Die Eigenschaften `DeviceGeometry`, `SelectedGeometry` und `MicArrayGeometryConfigFile` sind also optional. Wir verwenden die [JSON-Datei](https://aka.ms/sdsdk-micarray-json), die mit `MicArrayGeometryConfigFile` bereitgestellt wird, um nur den Bearmformingbereich zu erhalten.

Wenn ein Mikrofonarray mit `AudioConfig::FromMicrophoneInput` angegeben wird, dann verwenden wir das angegebene Mikrofon. Wenn kein Mikrofon angegeben ist oder `AudioConfig::FromDefaultMicrophoneInput` aufgerufen wird, verwenden wir das Standardmikrofon, das in den Soundeinstellungen unter Windows angegeben ist.
Der Microsoft Audio Stack im Speech Devices SDK unterstützt das Komprimieren von Daten nur für Abtastraten, die ein ganzzahliges Vielfaches von 16 KHz sind.

## <a name="linux"></a>Linux
Unter Linux müssen die Geometrieinformationen des Mikrofons angegeben werden. Die Verwendung von `DeviceGeometry` und `SelectedGeometry` wird weiterhin unterstützt. Sie können auch über die JSON-Datei mit der Eigenschaft `MicArrayGeometryConfigFile` bereitgestellt werden. Ähnlich wie bei Windows kann der Beamformingbereich über die JSON-Datei bereitgestellt werden.

Wenn ein Mikrofonarray mit `AudioConfig::FromMicrophoneInput` angegeben wird, dann verwenden wir das angegebene Mikrofon. Wenn kein Mikrofon angegeben ist oder `AudioConfig::FromDefaultMicrophoneInput` aufgerufen wird, dann erfolgt die Aufzeichnung über das ALSA-Gerät namens *Standard*. Standardmäßig zeigt *Standard* immer auf Karte 0 Gerät 0, aber Benutzer können dies in der `asound.conf`-Datei ändern. 

Der Microsoft Audio Stack im Speech Devices SDK unterstützt das Komprimieren von Daten nur für Abtastraten, die ein ganzzahliges Vielfaches von 16 KHz sind. Zusätzlich werden die folgenden Formate unterstützt: 32-Bit IEEE Little-Endian vom Typ „float“, 32-Bit Little-Endian vom Typ „signed int“, 24-Bit Little-Endian vom Typ „signed int“, 16-Bit Little-Endian vom Typ „signed int“ und 8-Bit vom Typ „signed int“.

## <a name="android"></a>Android
Derzeit wird nur [Roobo v1](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android) vom Speech Devices SDK unterstützt. Das Verhalten ist dasselbe wie in früheren Versionen, außer dass jetzt die `MicArrayGeometryConfigFile`-Eigenschaft verwendet werden kann, um eine JSON-Datei mit einem Beamformingbereich anzugeben.

## <a name="microphone-array-configuration-json"></a>JSON-Konfiguration des Mikrofonarrays

Die JSON-Datei für die Konfiguration der Mikrofonarraygeometrie folgt dem [JSON-Schema](https://aka.ms/sdsdk-micarray-json). Nachfolgend finden Sie einige Beispiele, die dem Schema folgen.


```json
{
    "micArrayType": "Linear",
    "geometry": "Linear4"
}
```


oder


```json
{
    "micArrayType": "Planar",
    "horizontalAngleBegin": 0,
    "horizontalAngleEnd": 360,
    "numberOfMicrophones": 4,
    "micCoord": [
        {
            "xCoord": 0,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": 40,
            "yCoord": 0,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": -35,
            "zCoord": 0
        },
        {
            "xCoord": -20,
            "yCoord": 35,
            "zCoord": 0
        }
    ]
}
```

oder

```json
{
    "micArrayType": "Linear",
    "horizontalAngleBegin": 70,
    "horizontalAngleEnd": 110
}
```


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Auswählen Ihres Speech-Geräts](get-speech-devices-sdk.md)