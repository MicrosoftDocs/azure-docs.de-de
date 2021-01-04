---
title: IoT Plug & Play-Bibliotheken und SDKs
description: Informationen zu den Geräte- und Dienstbibliotheken, die für die Entwicklung von IoT Plug & Play-fähigen Lösungen zur Verfügung stehen.
author: rido-min
ms.author: rmpablos
ms.date: 07/22/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 3ff01455c56dd259ea64878f912eb3f2431547e7
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95495251"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>Microsoft-SDKs für IoT Plug & Play

Die IoT Plug & Play-Bibliotheken und SDKs ermöglichen es Entwicklern, IoT-Lösungen mithilfe einer Vielzahl von Programmiersprachen auf mehreren Plattformen zu erstellen. Die nachstehende Tabelle enthält Links zu Beispielen und Schnellstarts, die Ihnen den Einstieg erleichtern sollen:

## <a name="device-sdks"></a>Geräte-SDKs

| Sprache | Paket | Coderepository | Beispiele | Schnellstart | Verweis |
|---|---|---|---|---|---|
| C (Gerät) | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [Beispiele](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp) | [Herstellen einer Verbindung mit IoT Hub](quickstart-connect-device.md) | [Referenz](/azure/iot-hub/iot-c-sdk-ref/) |
| .NET (Gerät) | [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [Beispiele](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/device/PnpDeviceSamples) | [Herstellen einer Verbindung mit IoT Hub](quickstart-connect-device.md) | [Referenz](/dotnet/api/microsoft.azure.devices.client?preserve-view=true&view=azure-dotnet) |
| Java (Gerät) | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [Beispiele](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [Herstellen einer Verbindung mit IoT Hub](quickstart-connect-device.md) | [Referenz](/java/api/com.microsoft.azure.sdk.iot.device?preserve-view=true&view=azure-java-stable) |
| Python (Gerät) | [pip 2.3.0](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [Beispiele](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Herstellen einer Verbindung mit IoT Hub](quickstart-connect-device.md) | [Referenz](/python/api/azure-iot-device/azure.iot.device?preserve-view=true&view=azure-python) |
| Node (Gerät) | [npm 1.17.2](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [Beispiele](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [Herstellen einer Verbindung mit IoT Hub](quickstart-connect-device.md) | [Referenz](/javascript/api/azure-iot-device/?preserve-view=true&view=azure-node-latest) |
| Embedded C: Gerät | – | [GitHub](https://github.com/Azure/azure-sdk-for-c/)| [Beispiele](howto-use-embedded-c.md#samples) | [Verwenden von Embedded C](howto-use-embedded-c.md) | NICHT ZUTREFFEND

## <a name="service-sdks"></a>Dienst-SDKs

| Plattform  | Paket | Coderepository | Beispiele | Schnellstart | Verweis |
|---|---|---|---|---|---|
| .NET – IoT Hub-Dienst | [NuGet 1.27.1](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Beispiele](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/iot-hub/Samples/service/PnpServiceSamples) | N/V | [Verweis](/dotnet/api/microsoft.azure.devices?preserve-view=true&view=azure-dotnet) |
| Java – IoT Hub-Dienst | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Beispiele](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples/pnp-service-sample) | N/V | [Verweis](/java/api/com.microsoft.azure.sdk.iot.service?preserve-view=true&view=azure-java-stable) |
| Node – IoT Hub-Dienst | [npm 1.13.0](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Beispiele](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | N/V | [Verweis](/javascript/api/azure-iothub/?preserve-view=true&view=azure-node-latest) |
| Python – Digital Twins-Dienst | [pip 2.2.3](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [Beispiele](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [Interaktion mit IoT Hub Digital Twins-API](quickstart-service.md) | – |
| Node – Digital Twins-Dienst | [npm 1.13.0](https://www.npmjs.com/package/azure-iot-digitaltwins-service) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Beispiele](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples/javascript) | [Interaktion mit IoT Hub Digital Twins-API](quickstart-service.md) | – |

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Testen der SDKs und Bibliotheken finden Sie im [Entwicklerhandbuch](concepts-developer-guide-device.md) und in den [Schnellstartanleitungen für Geräte](quickstart-connect-device.md) und [Schnellstartanleitungen für Dienste](quickstart-service.md).