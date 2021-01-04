---
title: Informationen zu Azure IoT SDKs | Microsoft Docs
description: 'Entwicklerhandbuch: Informationen und Links zu verschiedenen Geräte- und Dienst-SDKs für Azure IoT, mit denen Sie Geräte- und Back-End-Apps erstellen können.'
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/14/2020
ms.custom:
- mqtt
- 'Role: IoT Device'
- 'Role: Cloud Development'
ms.openlocfilehash: 9921ed500f831f2a002335fe83eae8f1bb47ecd4
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97095337"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>Verstehen und Verwenden von Azure IoT Hub SDKs

Es gibt zwei Kategorien von Software Development Kits (SDKs) für die Arbeit mit IoT Hub:

* **IoT Hub-Geräte-SDKs** ermöglichen das Erstellen von Apps, die auf Ihren IoT-Geräten ausgeführt werden, mit dem Geräteclient oder dem Modulclient. Mit diesen Apps werden Telemetriedaten an die IoT Hub-Instanz gesendet und optional Nachrichten oder Updates für Aufträge, Methoden oder Gerätezwillingen von der IoT Hub-Instanz empfangen.  Sie können mit dem Modulclient auch [Module](../iot-edge/iot-edge-modules.md) für [Azure IoT Edge-Runtime](../iot-edge/about-iot-edge.md) erstellen.

* Mit **IoT Hub-Dienst-SDKs** können Sie Back-End-Anwendungen zum Verwalten Ihrer IoT-Hub-Instanz erstellen und optional Nachrichten senden, Aufträge planen, direkte Methoden aufrufen oder gewünschte Eigenschaftsupdates an Ihre IoT-Geräte oder -Module senden.

Darüber hinaus bieten wir auch ein Spektrum an SDKs zum Arbeiten mit dem [Device Provisioning Service](../iot-dps/about-iot-dps.md).

* Mit **Gerätebereitstellungs-SDKs** können Sie auf Ihren IoT-Geräten ausgeführte Apps für die Kommunikation mit dem Device Provisioning Service erstellen.

* Mit **Dienstbereitstellungs-SDKs** können Sie Back-End-Anwendungen zum Verwalten Ihrer Registrierungen im Device Provisioning Service erstellen.

Erfahren Sie mehr über die [Vorteile beim Entwickeln mit Azure IoT SDKs](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="os-platform-and-hardware-compatibility"></a>Betriebssystemplattformen und Hardwarekompatibilität

Unterstützte Plattformen für die SDKs sind in [Azure IoT-SDKs – Plattformunterstützung](iot-hub-device-sdk-platform-support.md) aufgeführt.

Weitere Informationen zur Kompatibilität von SDKs mit bestimmten Hardwaregeräten finden Sie im [Azure Certified for IoT-Gerätekatalog](https://catalog.azureiotsolutions.com/) oder im jeweiligen Repository.

## <a name="azure-iot-hub-device-sdks"></a>Azure IoT Hub-Geräte-SDKs

Die Microsoft Azure IoT-Geräte-SDKs enthalten Code, der das Erstellen von Anwendungen erleichtert, die eine Verbindung mit Azure IoT Hub-Diensten herstellen und von ihnen verwaltet werden.

Azure IoT Hub Device SDK für .NET: 

* Download über [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/).  Der Namespace ist Microsoft.Azure.Devices.Clients, worin IoT Hub-Geräteclients (DeviceClient, ModuleClient) enthalten sind.
* [Quellcode](https://github.com/Azure/azure-iot-sdk-csharp)
* [API-Referenz](/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [Modulreferenz](/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)


Azure IoT Hub-Geräte-SDK für Embedded C (ANSI C – C99):
* [Erstellen des Embedded C SDK](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#build)
* [Quellcode](https://github.com/Azure/azure-sdk-for-c)
* [Größendiagramm](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#size-chart) für eingeschränkte Geräte.
* [API-Referenz](https://azuresdkdocs.blob.core.windows.net/$web/dotnet/Azure.Identity/1.0.0/api/index.html)


Azure IoT Hub-Geräte-SDK für C (ANSI C – C99):

* Installation über [apt-get, MBED, Arduino-IDE oder iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Quellcode](https://github.com/Azure/azure-iot-sdk-c)
* [Kompilieren des C-Geräte-SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [API-Referenz](/azure/iot-hub/iot-c-sdk-ref/)
* [Modulreferenz](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [Portieren des C-SDK für andere Plattformen](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Entwicklerdokumentation](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) für Informationen zu Cross-Kompilierung, erste Schritte auf verschiedenen Plattformen etc.
* [Informationen zum Ressourcenverbrauch beim Azure IoT Hub C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

Azure IoT Hub Device SDK für Java:

* Hinzufügen zum [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)-Projekt
* [Quellcode](https://github.com/Azure/azure-iot-sdk-java)
* [API-Referenz](/java/api/com.microsoft.azure.sdk.iot.device)
* [Modulreferenz](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)

Azure IoT Hub Device SDK für Node.js:

* Installation über [npm](https://www.npmjs.com/package/azure-iot-device)
* [Quellcode](https://github.com/Azure/azure-iot-sdk-node)
* [API-Referenz](/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest)
* [Modulreferenz](/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

Azure IoT Hub Device SDK für Python:

* Installation über [pip](https://pypi.org/project/azure-iot-device/)
* [Quellcode](https://github.com/Azure/azure-iot-sdk-python)
* [API-Referenz](/python/api/azure-iot-device)

Azure IoT Hub Device SDK für iOS:

* Installation über [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient)
* [Beispiele](https://github.com/Azure-Samples/azure-iot-samples-ios)
* API-Referenz: siehe [C-API-Referenz](/azure/iot-hub/iot-c-sdk-ref/)

## <a name="azure-iot-hub-service-sdks"></a>Azure IoT Hub-Dienst-SDKs

Die Azure IoT-Dienst-SDKs enthalten Code zum Erstellen von Anwendungen, die direkt mit IoT Hub interagieren, um Geräte und Sicherheit zu verwalten.

Azure IoT Hub Service SDK für .NET:

* Download über [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices/).  Der Namespace ist Microsoft.Azure.Devices, worin IoT Hub-Dienstclients (RegistryManager, ServiceClients) enthalten sind.
* [Quellcode](https://github.com/Azure/azure-iot-sdk-csharp)
* [API-Referenz](/dotnet/api/microsoft.azure.devices)

Azure IoT Hub Service SDK für Java:

* Hinzufügen zum [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)-Projekt
* [Quellcode](https://github.com/Azure/azure-iot-sdk-java)
* [API-Referenz](/java/api/com.microsoft.azure.sdk.iot.service)

Azure IoT Hub Service SDK für Node.js:

* Download über [npm](https://www.npmjs.com/package/azure-iothub)
* [Quellcode](https://github.com/Azure/azure-iot-sdk-node)
* [API-Referenz](/javascript/api/azure-iothub/?view=azure-iot-typescript-latest)

Azure IoT Hub Service SDK für Python:

* Download über [pip](https://pypi.python.org/pypi/azure-iot-hub/)
* [Quellcode](https://github.com/Azure/azure-iot-sdk-python/tree/master)
* [API-Referenz](/python/api/azure-iot-hub)

Azure IoT Hub Service SDK für C:

Das Azure IoT-Dienst-SDK für C befindet sich nicht mehr in der aktiven Entwicklung.
Wir werden weiterhin wichtige Fehler wie z. B. Abstürze, Datenbeschädigungen und Sicherheitsrisiken beheben. Es werden jedoch KEINE neuen Features hinzugefügt oder Fehler behoben, die nicht von Bedeutung sind.

Die Unterstützung des Azure IoT-Dienst-SDK ist in den Sprachen der höheren Ebene ([C#](https://github.com/Azure/azure-iot-sdk-csharp), [Java](https://github.com/Azure/azure-iot-sdk-java), [Node](https://github.com/Azure/azure-iot-sdk-node) und [Python](https://github.com/Azure/azure-iot-sdk-python)) verfügbar.

* Download über [apt-get, MBED, Arduino-IDE oder NuGet](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md)
* [Quellcode](https://github.com/Azure/azure-iot-sdk-c)

Azure IoT Hub Service SDK für iOS:

* Installation über [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient)
* [Beispiele](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> In den „Readme“-Dateien in den GitHub-Repositorys finden Sie Informationen zum Verwenden sprach- und plattformspezifischer Paket-Manager zum Installieren von Binärdateien und Abhängigkeiten auf Ihrem Entwicklungscomputer.

## <a name="microsoft-azure-provisioning-sdks"></a>Microsoft Azure-Bereitstellungs-SDKs

Mit **Microsoft Azure-Bereitstellungs-SDKs** können Sie Geräte in IoT Hub mit dem [Device Provisioning Service](../iot-dps/about-iot-dps.md) bereitstellen.

Geräte- und Dienst-SDKs für die Azure Bereitstellung für C#:

* Laden Sie [Geräte-SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Client/) und [Dienst-SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) von NuGet herunter.
* [Quellcode](https://github.com/Azure/azure-iot-sdk-csharp/)
* [API-Referenz](/dotnet/api/microsoft.azure.devices.provisioning.client?view=azure-dotnet)

Geräte- und Dienst-SDKs für die Azure Bereitstellung für C:

* Installation über [apt-get, MBED, Arduino-IDE oder iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Quellcode](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [API-Referenz](/azure/iot-hub/iot-c-sdk-ref/)

Geräte- und Dienst-SDKs für die Azure Bereitstellung für Java:

* Hinzufügen zum [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)-Projekt
* [Quellcode](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning)
* [API-Referenz](/java/api/com.microsoft.azure.sdk.iot.provisioning.device?view=azure-java-stable)

Geräte- und Dienst-SDKs für die Azure Bereitstellung für Node.js:

* [Quellcode](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning)
* [API-Referenz](/javascript/api/overview/azure/iothubdeviceprovisioning?view=azure-node-latest)
* Laden Sie [Geräte-SDK](https://badge.fury.io/js/azure-iot-provisioning-device) und [Dienst-SDK](https://badge.fury.io/js/azure-iot-provisioning-service) von npm herunter.

Geräte- und Dienst-SDKs für die Azure Bereitstellung für Python:

* [Quellcode](https://github.com/Azure/azure-iot-sdk-python)
* Laden Sie [Geräte-SDK](https://pypi.org/project/azure-iot-device/) und [Dienst-SDK](https://pypi.org/project/azure-iothub-provisioningserviceclient/) von pip herunter.

## <a name="next-steps"></a>Nächste Schritte

Azure IoT SDKs stellen ebenso Tools für die Entwicklung bereit:

* [iothub-diagnostics:](https://github.com/Azure/iothub-diagnostics) ein plattformübergeifendes Befehlszeilentool zum Diagnostizieren von Problemen, die im Zusammenhang mit der Verbindung mit IoT Hub stehen.
* [azure-iot-explorer](https://github.com/Azure/azure-iot-explorer): eine plattformübergreifende Desktopanwendung zum Herstellen einer Verbindung mit Ihrem IoT Hub sowie zum Hinzufügen und Verwalten von und Kommunizieren mit IoT-Geräten.

Relevante Dokumentation, die im Zusammenhang mit der Entwicklung mithilfe der Azure-IoT-SDKs steht:

* Informieren Sie sich über das [Verwalten von Konnektivität und zuverlässigem Messaging](iot-hub-reliability-features-in-sdks.md) mithilfe von IoT Hub-SDKs.
* Informieren Sie sich über das [Entwickeln für mobile Plattformen](iot-hub-how-to-develop-for-mobile-devices.md) wie etwa iOS und Android.
* [Azure IoT SDK – Plattformunterstützung](iot-hub-device-sdk-platform-support.md)

Weitere Referenzthemen in diesem IoT Hub-Entwicklungsleitfaden:

* [IoT Hub-Endpunkte](iot-hub-devguide-endpoints.md)
* [IoT Hub-Abfragesprache für Gerätezwillinge, Aufträge und Nachrichtenrouting](iot-hub-devguide-query-language.md)
* [Kontingente und Drosselung](iot-hub-devguide-quotas-throttling.md)
* [IoT Hub-MQTT-Unterstützung](iot-hub-mqtt-support.md)
* [REST-API-Referenz zu IoT Hub](/rest/api/iothub/)
