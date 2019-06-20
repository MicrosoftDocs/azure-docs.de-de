---
title: Grundlegendes zu Azure IoT Hub-Messaging | Microsoft-Dokumentation
description: 'Entwicklerhandbuch: Messaging zwischen Geräten und Cloud mit IoT Hub. Enthält Informationen zu Nachrichtenformaten und unterstützten Kommunikationsprotokollen.'
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: f56332fa7f53c729ffaa28ea375f043d1b4a3678
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60626246"
---
# <a name="send-device-to-cloud-and-cloud-to-device-messages-with-iot-hub"></a>Senden von Nachrichten zwischen Geräten und der Cloud per IoT Hub

IoT Hub ermöglicht die bidirektionale Kommunikation mit Ihren Geräten. Nutzen Sie IoT Hub-Messaging, um mit Ihren Geräten zu kommunizieren, indem Nachrichten von Ihren Geräten an Ihr Lösungs-Back-End und Befehle vom IoT-Lösungs-Back-End an Ihre Geräte gesendet werden. Erfahren Sie mehr zum [IoT Hub-Nachrichtenformat](iot-hub-devguide-messages-construct.md).

## <a name="sending-device-to-cloud-messages-to-iot-hub"></a>Senden von D2C-Nachrichten an IoT Hub

IoT Hub verfügt über einen integrierten Dienstendpunkt, der von Back-End-Diensten verwendet werden kann, um Telemetrienachrichten von Ihren Geräten zu lesen. Dieser Endpunkt ist kompatibel mit [Event Hubs](https://docs.microsoft.com/azure/event-hubs/), und Sie können IoT Hub-Standard-SDKs nutzen, um [Lesevorgänge auf diesem integrierten Endpunkt durchzuführen](iot-hub-devguide-messages-read-builtin.md).

IoT Hub unterstützt auch [benutzerdefinierte Endpunkte](iot-hub-devguide-endpoints.md#custom-endpoints), die von Benutzern definiert werden können, um Gerätetelemetriedaten und Ereignisse an Azure-Dienste zu senden, indem die [Nachrichtenweiterleitung](iot-hub-devguide-messages-d2c.md) verwendet wird.

## <a name="sending-cloud-to-device-messages-from-iot-hub"></a>Senden von C2D-Nachrichten von IoT Hub

Sie können [C2D](iot-hub-devguide-messages-c2d.md)-Nachrichten vom Lösungs-Back-End an Ihre Geräte senden.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Die wichtigsten Eigenschaften beim IoT Hub-Messaging sind eine zuverlässige und stabile Übermittlung von Nachrichten. Diese Eigenschaften ermöglichen Ausfallsicherheit bei zeitweiligen Verbindungsproblemen auf Geräteseite und Lastspitzen bei der Ereignisverarbeitung auf Cloudseite. IoT Hub implementiert *mindestens einmal* Übermittlungsgarantien für das D2C- und C2D-Messaging.

## <a name="choosing-the-right-type-of-iot-hub-messaging"></a>Auswählen des richtigen Typs von IoT Hub-Messaging

Sie verwenden D2C-Nachrichten zum Senden von Zeitreihen-Telemetriedaten und Warnungen von Ihrer Geräte-App und von C2D-Nachrichten für unidirektionale Nachrichten an Ihre Geräte-App.

* Wenn Sie eine Entscheidungshilfe zur Wahl zwischen D2C-Nachrichten, gemeldeten Eigenschaften und Dateiuploads benötigen, helfen Ihnen die Informationen im [Leitfaden zur D2C-Kommunikation](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-d2c-guidance) weiter.

* Wenn Sie eine Entscheidungshilfe zur Wahl zwischen C2D-Nachrichten, gewünschten Eigenschaften und direkten Methoden benötigen, helfen Ihnen die Informationen im [Leitfaden zur C2D-Kommunikation](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-c2d-guidance) weiter.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr zur [Nachrichtenweiterleitung](iot-hub-devguide-messages-d2c.md) von IoT Hub.

* Erfahren Sie mehr über das IoT Hub-[C2D-Messaging](iot-hub-devguide-messages-c2d.md).