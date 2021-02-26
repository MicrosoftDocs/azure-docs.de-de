---
title: Aktuelles Release von IoT Plug & Play | Microsoft-Dokumentation
description: Hier erfahren Sie, was im aktuellen IoT Plug & Play-Release enthalten ist.
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 7b6669ca55dc9b94dc5d702e54b42011120b8812
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831874"
---
# <a name="what-is-in-the-current-iot-plug-and-play-release"></a>Was enthält das aktuelle IoT Plug & Play-Release?

In diesem Artikel werden die Tools, SDKs und APIs zusammengefasst, die das aktuelle IoT Plug & Play-Release unterstützen. Die gezeigten Versionsnummern entsprechen der Versionsnummer zu dem Zeitpunkt, zu dem IoT Plug & Play allgemein verfügbar wurde. Versionsnummern können nach dem Release höher werden.

## <a name="modeling-language"></a>Modellierungssprache

[Digital Twins Definition Language (DTDL) v2](https://github.com/Azure/opendigitaltwins-dtdl).

Weitere Informationen zur Funktionsweise von IoT Plug & Play-Geräten mit DTDL finden Sie unter [IoT Plug & Play-Konventionen](concepts-convention.md).

## <a name="tools-and-utilities"></a>Tools und Hilfsprogramme

- Azure IoT-Explorer 0.12.0.

    Weitere Informationen finden Sie unter [Installieren und Verwenden des Azure IoT-Explorers](howto-use-iot-explorer.md).

- VS Code-Erweiterung 1.0.0.

    Weitere Informationen finden Sie unter [Installieren und Verwenden der DTDL-Erstellungstools](howto-use-dtdl-authoring-tools.md).

- Visual Studio 2019-Erweiterung 1.0.0.

    Weitere Informationen finden Sie unter [Installieren und Verwenden der DTDL-Erstellungstools](howto-use-dtdl-authoring-tools.md).

- Azure CLI IoT-Erweiterung 0.10.0.

    Die Azure IoT-Erweiterung enthält Befehle zur Unterstützung der Gerätezertifizierung. Siehe `az iot product -h`.

## <a name="libraries-and-sdks"></a>Bibliotheken und SDKs

Weitere Informationen zu den Bibliotheken und SDKs finden Sie unter [Microsoft-SDKs für IoT Plug & Play](libraries-sdks.md).

- C-Geräte-SDK [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md)
- Eingebettetes C-Geräte-SDK [GitHub](https://github.com/Azure/azure-sdk-for-c/)
- .NET-Geräte-SDK [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client)
- Java-Geräte-SDK [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client)
- Python-Geräte-SDK [pip 2.3.0](https://pypi.org/project/azure-iot-device/)
- Node.js-Geräte-SDK [npm 1.17.2](https://www.npmjs.com/package/azure-iot-device)
- .NET – IoT Hub-Dienst [NuGet 1.27.1](https://www.nuget.org/packages/Microsoft.Azure.Devices )
- Java – IoT Hub-Dienst [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0)
- Node.js – IoT Hub-Dienst [npm 1.13.0](https://www.npmjs.com/package/azure-iothub)
- Python – IoT Hub/Digital Twins-Dienst [pip 2.2.3](https://pypi.org/project/azure-iot-hub)
- DTDL-Modellparser [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser).

## <a name="rest-apis"></a>REST-APIs

REST-API [30.09.2020](/rest/api/iothub).

Weitere Informationen finden Sie im [Entwicklerhandbuch für IoT Plug & Play](concepts-developer-guide-service.md).

## <a name="iot-hub"></a>IoT Hub

IoT Plug & Play wird von IoT Hub in allen Regionen unterstützt. IoT Plug & Play wird nur von IoT-Hubs mit Standard- oder Free-Tarif unterstützt.

## <a name="announcements"></a>Ankündigungen

Aktuelle und vorherige Ankündigungen zu IoT Plug & Play finden Sie in den folgenden Blogbeiträgen:

- [Public preview refresh (Aktualisierung der öffentlichen Vorschau, veröffentlicht am 29. August 2020)](https://techcommunity.microsoft.com/t5/internet-of-things/add-quot-plug-and-play-quot-to-your-iot-solutions/ba-p/1548531)
- [Geräte für IoT Plug & Play vorbereiten und zertifizieren (Veröffentlicht am 26. August 2020)](https://azure.microsoft.com/blog/prepare-and-certify-your-devices-for-iot-plug-and-play/)
- [IoT Plug & Play jetzt in Vorschauversion verfügbar (Veröffentlicht am 22. August 2019)](https://azure.microsoft.com/blog/iot-plug-and-play-is-now-available-in-preview/)
- [Build with Azure IoT Central and IoT Plug and Play (Lösungen mit Azure IoT Central und IoT Plug & Play erstellen, veröffentlicht am 7. Mai 2019)](https://azure.microsoft.com/blog/build-with-azure-iot-central-and-iot-plug-and-play/)

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes können Sie sich mit [IoT Plug & Play](overview-iot-plug-and-play.md) vertraut machen.