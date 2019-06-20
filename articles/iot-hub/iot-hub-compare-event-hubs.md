---
title: Vergleich zwischen Azure IoT Hub und Azure Event Hubs | Microsoft Docs
description: Hier finden Sie einen Vergleich der Azure-Dienste IoT Hub und Event Hubs, wobei besonders auf Unterschiede bei den Funktionen und Anwendungsbeispiele eingegangen wird. Der Vergleich umfasst die unterstützten Protokolle, Geräteverwaltung, Überwachung, und Dateiuploads.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 7a589ba80b61ea5ef9ea1c941e9a0218a1653c99
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60735514"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>Verbinden von IoT-Geräten mit Azure: IoT Hub und Event Hubs

Azure bietet Dienste, die speziell für verschiedene Arten von Konnektivität und Kommunikation entwickelt wurden, um Ihnen zu helfen, Ihre Daten mit der Leistungsstärke der Cloud zu verbinden. Azure IoT Hub und Azure Event Hubs sind Clouddienste, die große Datenmengen aufnehmen und diese Daten für Einblicke in Geschäftsabläufe verarbeiten oder speichern können. Die beiden Dienste sind insofern ähnlich, als sie beide die Erfassung von Daten mit geringer Wartezeit und hoher Zuverlässigkeit unterstützen, jedoch für unterschiedliche Zwecke konzipiert sind. IoT Hub wurde speziell entwickelt, um die speziellen Anforderungen bei der Verbindung von IoT-Geräten mit der Azure Cloud zu erfüllen, während Event Hubs für das Streaming von Big Data entwickelt wurde. Microsoft empfiehlt den Einsatz von Azure IoT Hub, um IoT-Geräte mit Azure zu verbinden.

Azure IoT Hub ist das Cloudgateway, das IoT-Geräte verbindet, um Daten zu sammeln und so geschäftliche Erkenntnisse und Automatisierung zu fördern. Darüber hinaus bietet IoT Hub Funktionen, die die Beziehung zwischen Ihren Geräten und Ihren Back-End-Systemen optimieren. Bidirektionale Kommunikationsfunktionen bedeuten: Während Sie Daten von Geräten empfangen, können Sie auch Befehle und Richtlinien an Geräte zurücksenden. Verwenden Sie z. B. Cloud-zu-Gerät-Messaging, um Eigenschaften zu aktualisieren oder Aktionen zur Geräteverwaltung aufzurufen. Die Cloud-zu-Gerät-Kommunikation ermöglicht es Ihnen auch, Cloud Intelligence mit Azure IoT Edge an Ihre Edge-Geräte zu senden. Die besondere von IoT Hub bereitgestellte Identität auf Geräteebene hilft Ihnen, Ihre IoT-Lösung besser vor möglichen Angriffen zu schützen. 

[Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) ist der Big Data-Streamingdienst von Azure. Die Lösung ist auf Szenarien für das Streaming von Daten mit hohem Durchsatz ausgelegt, bei denen Kunden täglich Milliarden von Anforderungen senden können. Event Hubs nutzt ein partitioniertes Consumermodell zur horizontalen Skalierung Ihres Streams und ist in die Big Data- und Analysedienste von Azure integriert, darunter Databricks, Stream Analytics, ADLS und HDInsight. Mit Funktionen wie Event Hubs Capture und „Automatische Vergrößerung“ unterstützt dieser Dienst Ihre Big Data-Apps und -Lösungen. Zusätzlich wird Event Hubs von IoT Hub für seinen Telemetrie-Datenflusspfad genutzt, sodass auch Ihre IoT-Lösung von der enormen Leistungsfähigkeit von Event Hubs profitiert.

Zusammenfassend lässt sich sagen, dass beide Lösungen für die Datenerfassung in großem Maßstab ausgelegt sind. Nur IoT Hub bietet die umfangreichen IoT-spezifischen Funktionen, die für Sie entwickelt wurden, um den betriebswirtschaftlichen Nutzen der Verbindung Ihrer IoT-Geräte mit der Azure-Cloud zu maximieren.  Wenn Sie gerade erst am Anfang Ihrer IoT-Projekte stehen, können Sie mit IoT Hub zur Unterstützung Ihrer Datenerfassungsszenarien beginnen, um sicherzustellen, dass Sie sofortigen Zugriff auf die vollständigen IoT-Funktionen haben, sobald Ihre geschäftlichen und technischen Anforderungen dies erfordern.

Die folgende Tabelle enthält Details zum Vergleich der beiden Tarife von IoT Hub mit Event Hubs, wenn Sie IoT Hub und Event Hubs gemäß ihrer IoT-Funktionen bewerten möchten. Weitere Informationen zu den IoT Hub-Tarifen „Basic“ und „Standard“ finden Sie unter [Auswählen des passenden IoT Hub-Tarifs für Ihre Lösung](iot-hub-scaling.md).

| IoT-Funktion | IoT Hub-Standard-Tarif | IoT Hub-Basic-Tarif | Event Hubs |
| --- | --- | --- | --- |
| Nachrichten, die von Geräten an die Cloud gesendet werden | ![Prüfen][checkmark] | ![Prüfen][checkmark] | ![Prüfen][checkmark] |
| Protocols: HTTPS, AMQP, AMQP über webSockets | ![Prüfen][checkmark] | ![Prüfen][checkmark] | ![Prüfen][checkmark] |
| Protocols: MQTT, MQTT über webSockets | ![Prüfen][checkmark] | ![Prüfen][checkmark] |  |
| Gerätebasierte Identität | ![Prüfen][checkmark] | ![Prüfen][checkmark] |  |
| Dateiupload von Geräten | ![Prüfen][checkmark] | ![Prüfen][checkmark] |  |
| Device Provisioning-Dienst | ![Prüfen][checkmark] | ![Prüfen][checkmark] |  |
| Senden von Nachrichten aus der Cloud an Geräte | ![Prüfen][checkmark] |  |  |
| Gerätezwillinge und Geräteverwaltung | ![Prüfen][checkmark] |  |  |
| Gerätestreams (Vorschau) | ![Prüfen][checkmark] |  |  |
| IoT Edge | ![Prüfen][checkmark] |  |  |

Auch wenn die D2C-Clouddatenerfassung der einzige Anwendungsfall ist, sollten Sie unbedingt IoT Hub verwenden, da IoT Hub einen Dienst bietet, der für die Verbindung von IoT-Geräten ausgelegt ist. 

### <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Funktionen von IoT Hub finden Sie unter [Entwicklungsleitfaden für IoT Hub](iot-hub-devguide.md).

<!-- This one reference link is used over and over. --robinsh -->
[checkmark]: ./media/iot-hub-compare-event-hubs/ic195031.png
