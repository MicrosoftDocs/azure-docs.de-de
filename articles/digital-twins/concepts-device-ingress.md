---
title: Gerätekonnektivität und eingehende Telemetriedaten mit Azure Digital Twins | Microsoft-Dokumentation
description: Übersicht über das Onboarding eines Geräts mit Azure Digital Twins
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: alinast
ms.openlocfilehash: 35d12d0114f9677905c85a9df94ecd074e5f8f75
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60926075"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>Gerätekonnektivität und eingehende Telemetriedaten

Die von Geräten und Sensoren gesendeten Telemetriedaten bilden das Rückgrat jeder IoT-Lösung. Die Entscheidung, wie diese unterschiedlichen Ressourcen dargestellt und im Kontext eines Standorts verwaltet werden, ist ein wichtiger Aspekt der IoT-App-Entwicklung. Azure Digital Twins vereinfacht die Entwicklung von IoT-Lösungen, indem Geräte und Sensoren mit einem Raumintelligenzgraphen kombiniert werden.

Zunächst einmal muss eine Azure IoT Hub-Ressource am Stamm des Raumgraphen erstellt werden. Die IoT Hub-Ressource ermöglicht es allen Geräten unter dem Stammraum, Nachrichten zu senden. Nach Erstellung der IoT Hub-Instanz müssen Geräte mit Sensoren innerhalb der Digital Twins-Instanz registriert werden. Die Geräte können Daten über das [Azure IoT-Geräte-SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) an einen Digital Twins-Dienst senden.

Eine schrittweise Anleitung für das Onboarding von Geräten finden Sie im [Tutorial zum Bereitstellen und Konfigurieren von Digital Twins](tutorial-facilities-setup.md). In Kurzform sind dies folgende Schritte:

- Bereitstellen einer Digital Twins-Instanz über das [Azure-Portal](https://portal.azure.com)
- Erstellen von Räumen in Ihrem Graphen
- Erstellen einer IoT Hub-Ressource und Zuweisen dieser Ressource zu einem Bereich in Ihrem Graphen
- Erstellen von Geräten und Sensoren in Ihrem Graphen und Zuweisen dieser Geräte und Sensoren zu den in den vorherigen Schritten erstellten Räumen
- Erstellen eines Matchers (Abgleicher), um Telemetrienachrichten anhand von Bedingungen zu filtern
- Erstellen einer [benutzerdefinierten Funktion](concepts-user-defined-functions.md) und Zuweisen dieser Funktion zu einem Raum im Graphen, sodass eine angepasste Verarbeitung der Telemetrienachrichten erfolgt
- Zuweisen einer Rolle, damit die benutzerdefinierte Funktion auf die Graphdaten zugreifen kann
- Abrufen der IoT Hub-Verbindungszeichenfolge des Geräts über die Digital Twins-Verwaltungs-APIs
- Konfigurieren der Geräteverbindungszeichenfolge auf dem Gerät mit dem Azure IoT-Geräte-SDK

In den folgenden Abschnitten erfahren Sie, wie Sie die IoT Hub-Geräteverbindungszeichenfolge über die Digital Twins-Verwaltungs-API abrufen. Außerdem erfahren Sie, wie Sie sensorbasierte Telemetriedaten unter Verwendung des IoT Hub-Telemetrienachrichtenformats senden. Alle von Digital Twins empfangenen Telemetriedaten müssen einem Sensor im Raumgraphen zugeordnet sein. Dadurch wird sichergestellt, dass die Daten im richtigen räumlichen Kontext verarbeitet und weitergeleitet werden.

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>Abrufen der IoT Hub-Verbindungszeichenfolge eines Geräts aus der Verwaltungs-API

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Führen Sie einen GET-Aufruf für die Geräte-API mit einem `includes=ConnectionString`-Parameter aus, um die IoT Hub-Verbindungszeichenfolge des Geräts abzurufen. Filtern Sie nach der Geräte-GUID oder nach der Hardware-ID, um das Gerät zu finden.

```plaintext
YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_GUID?includes=ConnectionString
```

| Parameter | Ersetzen durch |
| --- | --- |
| *YOUR_DEVICE_GUID* | Der Geräte-ID |

```plaintext
YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=ConnectionString
```

| Parameterwert | Ersetzen durch |
| --- | --- |
| *YOUR_DEVICE_HARDWARE_ID* | Der Gerätehardware-ID |

Kopieren Sie in der Antwortnutzlast die Eigenschaft **connectionString** des Geräts. Sie wird benötigt, wenn Sie das Azure IoT-Geräte-SDK aufrufen, um Daten an Digital Twins zu senden.

## <a name="device-to-cloud-message"></a>Gerät-zu-Cloud-Nachricht

Sie können das Nachrichtenformat und die Nutzlast Ihres Geräts entsprechend den Anforderungen Ihrer Lösung anpassen. Verwenden Sie einen beliebigen Datenvertrag, der in ein Bytearray oder in einen Datenstrom serialisiert werden kann und von der [Klasse für Azure IoT-Geräteclientnachrichten (Message(byte[] byteArray))](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___) unterstützt wird. Die Nachricht kann in einem beliebigen benutzerdefinierten Binärformat vorliegen, solange Sie den Datenvertrag in einer entsprechenden benutzerdefinierten Funktion decodieren. Für eine Gerät-zu-Cloud-Nachricht muss nur eine einzige Anforderung erfüllt werden: Sie müssen mithilfe eines Satzes von Eigenschaften sicherstellen, dass die Nachricht ordnungsgemäß an die Verarbeitungs-Engine weitergeleitet wird.

### <a name="telemetry-properties"></a>Telemetrieeigenschaften

 Beim Nutzlastinhalt einer **Nachricht** kann es sich um beliebige Daten mit einer Größe von bis zu 256 KB handeln. Für Eigenschaften vom Typ [`Message.Properties`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet) gelten einige wenige Einschränkungen. Die folgende Tabelle enthält die erforderlichen und optionalen Eigenschaften, die vom System unterstützt werden:

| Eigenschaftenname | Wert | Erforderlich | BESCHREIBUNG |
|---|---|---|---|
| **DigitalTwins-Telemetry** | 1.0 | Ja | Ein konstanter Wert, der eine Nachricht an das System identifiziert. |
| **DigitalTwins-SensorHardwareId** | `string(72)` | Ja | Ein eindeutiger Bezeichner des Sensors, der die **Nachricht** sendet. Dieser Wert muss mit der **HardwareId**-Eigenschaft eines Objekts übereinstimmen, damit es vom System verarbeitet wird. Beispiel: `00FF0643BE88-CO2`. |
| **CreationTimeUtc** | `string` | Nein | Eine gemäß [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) formatierte Datumszeichenfolge, die den Zeitpunkt der Stichprobenentnahme für die Nutzlast angibt. Beispiel: `2018-09-20T07:35:00.8587882-07:00`. |
| **CorrelationId** | `string` | Nein | Eine UUID zur Nachverfolgung von Ereignissen im gesamten System. Beispiel: `cec16751-ab27-405d-8fe6-c68e1412ce1f`.

### <a name="send-your-message-to-digital-twins"></a>Senden einer Nachricht an Digital Twins

Verwenden Sie den DeviceClient-Aufruf [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) oder [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet), um eine Nachricht an Digital Twins zu senden.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur Datenverarbeitung und zu den Möglichkeiten von benutzerdefinierten Funktionen in Azure Digital Twins finden Sie unter [Datenverarbeitung und benutzerdefinierte Funktionen](concepts-user-defined-functions.md).
