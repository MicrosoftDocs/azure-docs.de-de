---
title: Azure IoT Hub-Kommunikationsprotokolle und -Ports | Microsoft-Dokumentation
description: 'Entwicklerhandbuch: Beschreibung der unterstützten Kommunikationsprotokolle für D2C- und C2D-Kommunikationen sowie der Portnummern, die geöffnet sein müssen.'
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 7082ebc4ca3066f84ca9790797cfa04e437f78a3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60626178"
---
# <a name="reference---choose-a-communication-protocol"></a>Referenz – Auswählen eines Kommunikationsprotokolls

Mit IoT Hub können Geräte die folgenden Protokolle für die geräteseitige Kommunikation verwenden:

* [MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf)
* MQTT über WebSockets
* [AMQP](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
* AMQP über WebSockets
* HTTPS

Informationen dazu, wie diese Protokolle bestimmte IoT Hub-Features unterstützen, finden Sie im [Leitfaden zur D2C-Kommunikation](iot-hub-devguide-d2c-guidance.md) und im [Leitfaden zur C2D-Kommunikation](iot-hub-devguide-c2d-guidance.md).

Die folgende Tabelle enthält allgemeine Ratschläge für Ihre Protokollauswahl:

| Protocol | Wann Sie dieses Protokoll auswählen sollten |
| --- | --- |
| MQTT <br> MQTT über WebSockets |Verwenden Sie dieses Protokoll auf allen Geräten, die sich nicht über dieselbe TLS-Verbindung mit mehreren Geräten verbinden müssen (von denen jedes eigene gerätebezogene Anmeldeinformationen hat). |
| AMQP <br> AMQP über WebSockets |Verwenden Sie es für Feld- und Cloudgateways, um die Vorteile geräteübergreifender Multiplexingverbindungen zu nutzen. |
| HTTPS |Verwenden Sie es für Geräte, die keine anderen Protokolle unterstützen können. |

Beachten Sie bei der Auswahl des Protokolls für die geräteseitige Kommunikation folgende Punkte:

* **C2D-Muster**. HTTPS bietet keine effiziente Methode zum Implementieren von Serverpushvorgängen. Daher fragen Geräte bei Verwendung von HTTPS IoT Hub nach C2D-Nachrichten ab. Dieser Ansatz ist für das Gerät und auch für IoT Hub sehr ineffizient. Gemäß den aktuellen HTTPS-Richtlinien muss jedes Gerät mindestens alle 25 Minuten eine Abfrage nach Nachrichten durchführen. MQTT und AMQP unterstützen Serverpush beim Empfangen von C2D-Nachrichten. Sie ermöglichen sofortiges Nachrichtenpushen von IoT Hub zum Gerät. Wenn die Übermittlungslatenz eine wichtige Rolle spielt, sind MQTT oder AMQP die zu bevorzugenden Protokolle. Bei nur selten verbundenen Geräten funktioniert auch HTTPS.

* **Bereichsgateways**. Bei Verwendung von MQTT und HTTPS ist es nicht möglich, mehrere Geräte (jedes mit eigenen gerätebezogenen Anmeldeinformationen) mithilfe der gleichen TLS-Verbindung zu verbinden. Für [Bereichsgatewayszenarien](iot-hub-devguide-endpoints.md#field-gateways), die für jedes verbundene Gerät eine TLS-Verbindung zwischen dem Bereichsgateway und IoT Hub benötigen, sind diese Protokolle nicht optimal.

* **Geräte mit eingeschränkten Ressourcen**. Die MQTT- und HTTPS-Bibliotheken haben weniger Speicherbedarf als die AMQP-Bibliotheken. Wenn daher das Gerät über beschränkte Ressourcen verfügt (beispielsweise weniger als 1 MB RAM), stehen möglicherweise nur diese Protokolle als Protokollimplementierung zur Verfügung.

* **Netzwerkausnahme**. Das AMQP-Standardprotokoll verwendet Port 5671, und MQTT lauscht an Port 8883. Die Verwendung dieser Ports kann Probleme in Netzwerken verursachen, die für Nicht-HTTPS-Protokolle geschlossen sind. Verwenden Sie MQTT über WebSockets, AMQP über WebSockets oder HTTPS in diesem Szenario.

* **Größe der Nutzlast**. MQTT und AMQP sind binäre Protokolle und weisen daher kompaktere Nutzlasten als HTTPS auf.

> [!WARNING]
> Bei Verwendung von HTTPS muss jedes Gerät mindestens alle 25 Minuten eine Abfrage nach C2D-Nachrichten durchführen. In der Entwicklungsphase darf freilich häufiger als alle 25 Minuten eine Abfrage erfolgen.

## <a name="port-numbers"></a>Portnummern

Geräte können mit IoT Hub in Azure über verschiedene Protokolle kommunizieren. In der Regel richtet sich die Wahl des Protokolls nach den spezifischen Anforderungen der Lösung. Die folgende Tabelle enthält die ausgehenden Ports, die geöffnet sein müssen, damit ein Gerät ein bestimmtes Protokoll verwenden kann:

| Protocol | Port |
| --- | --- |
| MQTT |8883 |
| MQTT über WebSockets |443 |
| AMQP |5671 |
| AMQP über WebSockets |443 |
| HTTPS |443 |

Nachdem Sie einen IoT Hub in einer Azure-Region erstellt haben, behält er seine IP-Adresse für die gesamte Lebensdauer bei. Wenn der IoT Hub von Microsoft jedoch in eine andere Skalierungseinheit verschoben wird, um die Dienstqualität zu gewährleisten, wird ihm eine neue IP-Adresse zugewiesen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen dazu, wie IoT Hub das MQTT-Protokoll implementiert, finden Sie unter [Kommunikation mit Ihrem IoT Hub mithilfe des Protokolls MQTT](iot-hub-mqtt-support.md).
