---
title: Einführung in IoT Plug & Play | Microsoft-Dokumentation
description: Hier finden Sie Informationen zu IoT Plug & Play. IoT Plug & Play basiert auf einer offenen Modelliersprache, mit der intelligente IoT-Geräte ihre Funktionen deklarieren können. IoT-Geräte geben diese als „Gerätemodell“ bezeichnete Deklaration beim Herstellen einer Verbindung mit Cloudlösungen an. Die Cloudlösung kann das Gerät dann automatisch verstehen und mit der Interaktion beginnen – ohne dass Sie Code schreiben müssen.
author: rido-min
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
manager: eliotgra
ms.custom: references_regions
ms.openlocfilehash: d6cba4bcb76094ed156ef35b1ea76cea7001c372
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95486295"
---
# <a name="what-is-iot-plug-and-play"></a>Was ist IoT Plug & Play?

IoT Plug & Play ermöglicht es Lösungsentwicklern, intelligente Geräte ohne manuelle Konfiguration in ihre Lösungen zu integrieren. Das Herzstück von IoT Plug & Play ist ein _Gerätemodell_, mit dessen Hilfe ein Gerät einer IoT Plug & Play-fähigen Anwendung seine Funktionen verfügbar macht. Dieses Modell ist als ein Satz von Elementen strukturiert, die Folgendes definieren:

- _Eigenschaften_, die den schreibgeschützten oder schreibbaren Status eines Geräts oder einer anderen Entität darstellen. Beispielsweise kann eine Geräteseriennummer eine schreibgeschützte Eigenschaft sein und die Zieltemperatur eines Thermostats eine schreibbare Eigenschaft.
- _Telemetriedaten_, d. h. die von einem Gerät ausgegebenen Daten – unabhängig davon, ob es sich bei den Daten um einen regulären Datenstrom von Sensormesswerten, einen gelegentlichen Fehler oder eine Informationsmeldung handelt.
- _Befehle_, die eine Funktion oder einen Vorgang beschreiben, die bzw. der auf einem Gerät ausgeführt werden kann. Durch einen Befehl kann beispielsweise ein Gateway neu gestartet oder ein Foto mit einer Remotekamera aufgenommen werden.

Sie können diese Elemente in Schnittstellen zur modellübergreifenden Wiederverwendung gruppieren, um die Zusammenarbeit zu vereinfachen und die Entwicklung zu beschleunigen.

Damit IoT Plug & Play mit [Azure Digital Twins](../digital-twins/overview.md) arbeiten kann, definieren Sie Modelle und Schnittstellen mithilfe der [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl). IoT Plug & Play und die DTDL sind für die Community freigegeben, und Microsoft begrüßt die Zusammenarbeit mit Kunden, Partnern und der Branche. Beide Technologien basieren auf offenen W3C-Standards (World Wide Web Consortium) wie JSON-LD und RDF, die eine einfachere Nutzung in verschiedenen Diensten und Tools ermöglichen.

Für die Nutzung von IoT Plug & Play und der DTDL fallen keine zusätzlichen Kosten an. Für [Azure IoT Hub](../iot-hub/about-iot-hub.md) und andere Azure-Dienste gelten weiterhin die Standardtarife.

In diesem Artikel wird Folgendes beschrieben:

- Die typischen Rollen bei einem Projekt mit IoT Plug & Play
- Verwenden von IoT Plug & Play-Geräten in Ihrer Anwendung
- Entwickeln einer IoT-Geräteanwendung, die IoT Plug & Play unterstützt

## <a name="user-roles"></a>Benutzerrollen

IoT Plug & Play ist für zwei Arten von Entwicklern nützlich:

- Ein _Lösungsentwickler_ ist für das Entwickeln einer IoT-Lösung mithilfe von Azure IoT Hub und anderen Azure-Ressourcen sowie für das Ermitteln der zu integrierenden IoT-Geräte zuständig.
- Ein _Geräteentwickler_ erstellt den Code, der auf einem mit Ihrer Lösung verbundenen Gerät ausgeführt wird.

## <a name="use-iot-plug-and-play-devices"></a>Verwenden von IoT Plug & Play-Geräten

Als Lösungsentwickler können Sie eine in der Cloud gehostete IoT-Lösung entwickeln, die IoT Plug & Play-Geräte verwendet. Verwenden Sie [IoT Hub](../iot-hub/about-iot-hub.md), einen verwalteten Clouddienst, der als Nachrichtenhub für die sichere, bidirektionale Kommunikation zwischen Ihrer IoT-Anwendung und Ihren Geräten fungiert.

Wenn Sie ein IoT Plug & Play-Gerät mit einem IoT-Hub verbinden, können Sie mithilfe des Tools [Azure IoT-Explorer](./howto-use-iot-explorer.md) die Telemetrie, Eigenschaften und Befehle anzeigen, die in den Schnittstellen definiert sind, aus denen das Modell besteht.

Wenn Sie vorhandene Sensoren an ein Windows- oder Linux-Gateway angeschlossen haben, können Sie diese Sensoren mithilfe der [IoT Plug & Play-Brücke](./concepts-iot-pnp-bridge.md) verbinden und IoT Plug & Play-Geräte erstellen, ohne Gerätesoftware/-firmware (für [unterstützte Protokolle](./concepts-iot-pnp-bridge.md#supported-protocols-and-sensors)) schreiben zu müssen.

## <a name="develop-an-iot-device-application"></a>Entwickeln einer IoT-Geräteanwendung

Als Geräteentwickler können Sie ein IoT-Hardwareprodukt entwickeln, das IoT Plug & Play unterstützt. Der Prozess besteht aus drei Hauptschritten:

1. Definieren Sie das Gerätemodell. Dazu erstellen Sie mehrere JSON-Dateien zum Definieren der Funktionen Ihres Geräts mithilfe der [DTDL](https://github.com/Azure/opendigitaltwins-dtdl). Ein Modell beschreibt eine komplette Entität (z. B. ein physisches Produkt) und definiert die von dieser Entität implementierten Schnittstellen. Schnittstellen sind freigegebene Verträge, die die von einem Gerät unterstützten Telemetriedaten, Eigenschaften und Befehle eindeutig identifizieren. Schnittstellen können in verschiedenen Modellen wiederverwendet werden.

1. Erstellen Sie Gerätesoftware oder -firmware so, dass deren Telemetriedaten, Eigenschaften und Befehle den IoT Plug & Play-Konventionen entsprechen. Wenn Sie vorhandene Sensoren verbinden, die an ein Windows- oder Linux-Gateway angeschlossen sind, kann die [IoT Plug & Play-Brücke](./concepts-iot-pnp-bridge.md) diesen Schritt vereinfachen.

1. Das Gerät kündigt die Modell-ID als Teil der MQTT-Verbindung an. Das Azure IoT SDK enthält neue Konstrukte zum Bereitstellen der Modell-ID zur Verbindungszeit.

> [!Important]
> IoT Plug & Play-Geräte müssen MQTT oder MQTT über WebSockets verwenden. Andere Protokolle, z. B. AMQP oder HTTP, sind für die Implementierung von IoT Plug & Play-Geräten nicht gültig.

## <a name="device-certification"></a>Gerätezertifizierung

Mit dem [IoT Plug & Play-Gerätezertifizierungsprogramm](howto-certify-device.md) wird überprüft, ob ein Gerät die IoT Plug & Play-Zertifizierungsanforderungen erfüllt. Sie können ein zertifiziertes Gerät zum öffentlichen [Katalog mit Certified for Azure IoT-Geräten](https://aka.ms/devicecatalog) hinzufügen.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich nun einen Überblick über IoT Plug & Play verschafft haben, empfehlen wir Ihnen als Nächstes einen der folgenden Schnellstarts:

- [Verbinden eines Geräts mit IoT Hub](./quickstart-connect-device.md)
- [Interagieren mit einem Gerät über Ihre Lösung](./quickstart-service.md)
