---
title: Ungefähre Standortbestimmung in Objective-C
description: Hier erfahren Sie ausführlich, wie Sie mithilfe der ungefähren Standortbestimmung in Objective-C Anker erstellen und nach ihnen suchen.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 9c50b324bddd2c57f65225c02b0745ed3ad1244a
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "96018017"
---
# <a name="how-to-create-and-locate-anchors-using-coarse-relocalization-in-objective-c"></a>Erstellen von und Suchen nach Ankern mithilfe der ungefähren Standortbestimmung in Objective-C

> [!div  class="op_single_selector"]
> * [Unity](set-up-coarse-reloc-unity.md)
> * [Objective-C](set-up-coarse-reloc-objc.md)
> * [Swift](set-up-coarse-reloc-swift.md)
> * [Android Java](set-up-coarse-reloc-java.md)
> * [C++/NDK](set-up-coarse-reloc-cpp-ndk.md)
> * [C++/WinRT](set-up-coarse-reloc-cpp-winrt.md)

Azure Spatial Anchors kann Daten eines geräteeigenen Positionierungssensors mit den von Ihnen erstellten Ankern verknüpfen. Diese Daten können auch verwendet werden, um schnell festzustellen, ob in der Nähe Ihres Geräts irgendwelche Anker vorhanden sind. Weitere Informationen finden Sie unter [Ungefähre Standortbestimmung](../concepts/coarse-reloc.md).

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie dieses Anleitungen ausführen können, stellen Sie sicher, dass die folgenden Bedingungen erfüllt sind:

- Grundlegende Kenntnisse zu Objective-C.
- Sie haben die Informationen unter [Azure Spatial Anchors-Übersicht](../overview.md) gelesen.
- Sie haben eine der [fünfminütigen Schnellstartanleitungen](../index.yml) absolviert.
- Sie haben die Informationen unter [Erstellen von und Suchen nach Ankern mit Azure Spatial Anchors](../create-locate-anchors-overview.md) gelesen.

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-provider.md)]

```objc
// Create the sensor fingerprint provider
ASAPlatformLocationProvider *sensorProvider;
sensorProvider = [[ASAPlatformLocationProvider alloc] init];

// Allow GPS
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.geoLocationEnabled = true;

// Allow WiFi scanning
sensors.wifiEnabled = true;

// Populate the set of known BLE beacons' UUIDs
NSArray *uuids = @[@"22e38f1a-c1b3-452b-b5ce-fdb0f39535c1", @"a63819b9-8b7b-436d-88ec-ea5d8db2acb0"];

// Allow a set of known BLE beacons
sensors.bluetoothEnabled = true;
sensors.knownBeaconProximityUuids = uuids;
```

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-configure-session.md)]

```objc
// Set the session's sensor fingerprint provider
cloudSpatialAnchorSession.locationProvider = sensorProvider;

// Configure the near-device criteria
ASANearDeviceCriteria *nearDeviceCriteria = [[ASANearDeviceCriteria alloc] init];
nearDeviceCriteria.distanceInMeters = 5.0f;
nearDeviceCriteria.maxResultCount = 25;

// Set the session's locate criteria
ASAAnchorLocateCriteria *anchorLocateCriteria = [[ASAAnchorLocateCriteria alloc] init];
anchorLocateCriteria.nearDevice = nearDeviceCriteria;
[cloudSpatialAnchorSession createWatcher:anchorLocateCriteria];
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

[!INCLUDE [Configure Provider](../../../includes/spatial-anchors-set-up-coarse-reloc-next-steps.md)]