---
title: Entwicklerhandbuch für Azure IoT Hub | Microsoft Docs
description: Das Azure IoT Hub-Entwicklerhandbuch umfasst Erläuterungen zu Endpunkten, Sicherheit, Identitätsregistrierung, Geräteverwaltung, direkten Methoden, Gerätezwillingen, Dateiuploads, Aufträgen, die Abfragesprache von IoT Hub und Messaging.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom: mqtt
ms.openlocfilehash: 03e05eb2bc56a07c2bda3d3fb224012abb3c9e4a
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92735924"
---
# <a name="azure-iot-hub-developer-guide"></a>Entwicklungsleitfaden für Azure IoT Hub

Azure IoT Hub ist ein vollständig verwalteter Dienst, der eine zuverlässige und sichere bidirektionale Kommunikation zwischen Millionen von Geräten und einem Lösungs-Back-End ermöglicht.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Azure IoT Hub bietet Ihnen Folgendes:

* Eine sichere Kommunikation unter Verwendung von Zugriffssteuerung und Sicherheitsanmeldeinformationen auf Gerätebasis.

* Zahlreiche Optionen für hyperskalierbare Kommunikation zwischen Geräten und Cloud (Device-to-Cloud, D2C) sowie zwischen Cloud und Geräten (Cloud-to-Device, C2D)

* Abfragbarer Speicher mit Zustandsinformationen und Metdadaten auf Gerätebasis

* Eine problemlose Geräteanbindung mithilfe von Gerätebibliotheken für die gängigsten Sprachen und Plattformen

Dieses IoT Hub-Entwicklerhandbuch umfasst die folgenden Artikel:

* Die Informationen im [Leitfaden zur D2C-Kommunikation](iot-hub-devguide-d2c-guidance.md) erleichtern die Wahl zwischen D2C-Nachrichten, den gemeldeten Eigenschaften des Gerätezwillings und dem Dateiupload.

* Die Informationen im [Leitfaden zur C2D-Kommunikation](iot-hub-devguide-c2d-guidance.md) erleichtern die Wahl zwischen direkten Methoden, den gewünschten Eigenschaften des Gerätezwillings und C2D-Nachrichten.

* Unter [Messaging zwischen Geräten und Cloud mit IoT Hub](iot-hub-devguide-messaging.md) werden die Messagingfeatures (D2C und C2D) beschrieben, die mit IoT Hub zur Verfügung stehen.

  * [Senden von D2C-Nachrichten an IoT Hub](iot-hub-devguide-messages-d2c.md)

  * [Lesen von D2C-Nachrichten vom integrierten Endpunkt](iot-hub-devguide-messages-read-builtin.md)

  * [Verwenden von benutzerdefinierten Endpunkten und Routingregeln für D2C-Nachrichten](iot-hub-devguide-messages-read-custom.md)

  * [Senden von C2D-Nachrichten von IoT Hub](iot-hub-devguide-messages-c2d.md)

  * [Erstellen und Lesen von IoT Hub-Nachrichten](iot-hub-devguide-messages-construct.md)

* Unter [Hochladen von Dateien von einem Gerät](iot-hub-devguide-file-upload.md) wird beschrieben, wie Sie Dateien von einem Gerät hochladen können. Dieser Artikel bietet auch Informationen zu Themen wie Benachrichtigungen, die beim Hochladevorgang gesendet werden können.

* Unter [Grundlegendes zur Identitätsregistrierung in Ihrer IoT Hub-Instanz](iot-hub-devguide-identity-registry.md) wird beschrieben, welche Informationen in jeder IoT Hub-Identitätsregistrierung gespeichert werden. Zudem wird im Artikel beschrieben, wie Sie darauf zugreifen und diese bearbeiten können.

* Unter [Verwalten des Zugriffs auf IoT Hub](iot-hub-devguide-security.md) wird das Sicherheitsmodell beschrieben, mit dem sowohl für Geräte als auch für Cloudkomponenten Zugriff auf IoT Hub-Funktionalität gewährt wird. Der Artikel enthält Informationen zum Verwenden von Token und X.509-Zertifikaten sowie Details zu den Berechtigungen, die Sie erteilen können.

* [Verwenden von Gerätezwillingen zum Synchronisieren von Status und Konfigurationen](iot-hub-devguide-device-twins.md) beschreibt das Konzept der *Gerätezwillinge* . Zudem finden Sie in diesem Artikel auch Informationen zur Funktionalität von Gerätezwillingen, z. B. zur Synchronisierung eines Geräts mit dessen Gerätezwilling. Der Artikel enthält Informationen zu den Daten, die in einem Gerätezwilling gespeichert sind.

* [Aufrufen einer direkten Methode auf einem Gerät](iot-hub-devguide-direct-methods.md) beschreibt den Lebenszyklus einer direkten Methode. Der Artikel beschreibt, wie Sie Methoden für ein Gerät aus Ihrer Back-End-App aufrufen und die direkte Methode für Ihr Gerät anwenden.

* Unter [Planen von Aufträgen auf mehreren Geräten](iot-hub-devguide-jobs.md) wird beschrieben, wie Sie Aufträge auf mehreren Geräten planen können. Der Artikel erläutert das Übermitteln von Aufträgen zum Erledigen von Aufgaben wie dem Ausführen einer direkten Methode und Aktualisieren eines Geräts mithilfe eines Gerätezwillings. Außerdem wird erklärt, wie der Status eines Auftrags abgefragt wird.

* In [Referenz – Auswählen eines Kommunikationsprotokolls](iot-hub-devguide-protocols.md) werden die Kommunikationsprotokolle beschrieben, die IoT Hub für die Gerätekommunikation unterstützt. Außerdem werden die Ports aufgeführt, die geöffnet werden müssen.

* Unter [Referenz – IoT Hub-Endpunkte](iot-hub-devguide-endpoints.md) werden die verschiedenen Endpunkte beschrieben, die jeder IoT-Hub für Laufzeit- und Verwaltungsvorgänge bereitstellt. Außerdem wird beschrieben, wie Sie zusätzliche Endpunkte in Ihrem IoT-Hub erstellen können, und wie Sie mit einem Feldgateway die Konnektivität mit Ihren IoT Hub-Endpunkten in nicht standardmäßigen Szenarien aktivieren.

* Unter [Referenz – IoT Hub-Abfragesprache für Gerätezwillinge, Aufträge und Nachrichtenrouting](iot-hub-devguide-query-language.md) wird die IoT Hub-Abfragesprache beschrieben, mit der Sie von Ihrem Hub Informationen über Gerätezwillinge und Aufträge abrufen können.

* Unter [Referenz: IoT Hub-Kontingente und -Drosselung](iot-hub-devguide-quotas-throttling.md) sind die im IoT Hub-Dienst festgelegten Kontingente und die Drosselung zusammengefasst, die beim Überschreiten eines Kontingents auftritt.

* [Referenz – Preise](iot-hub-devguide-pricing.md) enthält allgemeine Informationen zu verschiedenen SKUs und Preise für IoT Hub sowie Details zur Erfassung der verschiedenen IoT Hub-Funktionen als Nachrichten durch IoT Hub.

* Unter [Referenz – Geräte- und Dienst-SDKs](iot-hub-devguide-sdks.md) sind die Azure IoT SDKs aufgelistet, die Sie bei der Entwicklung von Geräte- und Dienst-Apps für die Interaktion mit Ihrer IoT Hub-Instanz verwenden können. Der Artikel enthält Links zur online verfügbaren API-Dokumentation.

* [IoT Hub-MQTT-Unterstützung](iot-hub-mqtt-support.md) enthält detaillierte Informationen zur Unterstützung des MQTT-Protokolls durch IoT Hub. Der Artikel beschreibt die Unterstützung des in die Azure IoT SDKs integrierten MQTT-Protokolls und bietet Informationen zur direkten Verwendung des MQTT-Protokolls.

* [Glossar](iot-hub-devguide-glossary.md) enthält eine Liste der allgemeinen auf IoT Hub bezogenen Begriffe.
