---
title: Architektur von IoT Plug & Play | Microsoft-Dokumentation
description: Hier finden Sie als Lösungsentwickler Informationen zu den Hauptelementen der Architektur von IoT Plug & Play.
author: ridomin
ms.author: rmpablos
ms.date: 09/15/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 2cccb1fdfe775250f80da6cc2ecdcc4ddaa3d88e
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95484082"
---
# <a name="iot-plug-and-play-architecture"></a>IoT Plug & Play-Architektur

IoT Plug & Play ermöglicht es Lösungsentwicklern, intelligente Geräte ohne manuelle Konfiguration in ihre Lösungen zu integrieren. Das Herzstück von IoT Plug & Play ist ein _Gerätemodell_, das die Funktionen eines Geräts für eine IoT Plug & Play-fähige Anwendung beschreibt. Dieses Modell ist als ein Satz von Schnittstellen strukturiert, die Folgendes definieren:

- _Eigenschaften_, die den schreibgeschützten oder schreibbaren Status eines Geräts oder einer anderen Entität darstellen. Beispielsweise kann eine Geräteseriennummer eine schreibgeschützte Eigenschaft sein und die Zieltemperatur eines Thermostats eine schreibbare Eigenschaft.
- _Telemetriedaten_, d. h. die von einem Gerät ausgegebenen Daten – unabhängig davon, ob es sich bei den Daten um einen regulären Datenstrom von Sensormesswerten, einen gelegentlichen Fehler oder eine Informationsmeldung handelt.
- _Befehle_, die eine Funktion oder einen Vorgang beschreiben, die bzw. der auf einem Gerät ausgeführt werden kann. Durch einen Befehl kann beispielsweise ein Gateway neu gestartet oder ein Foto mit einer Remotekamera aufgenommen werden.

Jedes Modell und jede Schnittstelle hat eine eindeutige ID.

Im folgenden Diagramm sind die wichtigsten Elemente einer IoT Plug & Play-Lösung dargestellt:

:::image type="content" source="media/concepts-architecture/pnp-architecture.png" alt-text="IoT Plug & Play-Architektur":::

## <a name="model-repository"></a>Modellrepository

Das [Modellrepository](./concepts-model-repository.md) ist ein Speicher für Modell- und Schnittstellendefinitionen. Sie definieren Modelle und Schnittstellen mithilfe der [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl).

Mithilfe der Webbenutzeroberfläche können Sie die Modelle und Schnittstellen verwalten.

Das Modellrepository hat integrierte rollenbasierte Zugriffssteuerungen, mit denen Sie den Zugriff auf Schnittstellendefinitionen einschränken können.

## <a name="devices"></a>Geräte

Ein Geräteentwickler implementiert den Code, der auf einem intelligenten IoT-Gerät ausgeführt werden soll, mithilfe eines der [Azure IoT-Geräte-SDKs](./libraries-sdks.md). Mithilfe der Geräte-SDKs können Geräteentwickler folgende Aktionen ausführen:

- Eine sichere Verbindung mit einem IoT-Hub herstellen.
- Registrieren des Geräts beim IoT-Hub und Ankündigen der Modell-ID, die die Sammlung der vom Gerät implementierten DTDL-Schnittstellen angibt
- Synchronisieren der Eigenschaften, die in den DTDL-Schnittstellen zwischen dem Gerät und Ihrem IoT-Hub definiert sind
- Hinzufügen von Befehlshandlern für die Befehle, die in den DTDL-Schnittstellen definiert sind
- Senden von Telemetriedaten an den IoT-Hub.

## <a name="iot-edge-gateway"></a>IoT Edge-Gateway

Ein IoT Edge-Gateway fungiert als Vermittler zum Verbinden von IoT Plug & Play-Geräten, die keine direkte Verbindung mit einem IoT-Hub herstellen können. Weitere Informationen finden Sie unter [Verwendung eines IoT Edge-Geräts als Gateway](../iot-edge/iot-edge-as-gateway.md).

## <a name="iot-edge-modules"></a>IoT Edge-Module

Ein _IoT Edge-Modul_ ermöglicht Ihnen die Bereitstellung und Verwaltung von Geschäftslogik im Edge. Azure IoT Edge-Module sind die kleinste von IoT Edge verwaltete Recheneinheit und kann Azure-Dienste (wie Azure Stream Analytics) oder Ihren eigenen lösungsspezifischen Code enthalten.

Der _IoT Edge-Hub_ ist eines der Module, aus denen die Azure IoT Edge-Runtime besteht. Er fungiert als lokaler Proxy für IoT Hub, indem er die gleichen Protokollendpunkte wie IoT Hub verfügbar macht. Diese Konsistenz bedeutet, dass Clients (ob Geräte oder Module) auf die gleiche Weise eine Verbindung mit der IoT Edge-Laufzeit wie mit IoT Hub herstellen können.

Mithilfe der Geräte-SDKs können Modulentwickler folgende Aktionen ausführen:

- Verwenden des IoT Edge-Hubs, um eine sichere Verbindung mit Ihrem IoT-Hub herzustellen
- Registrieren des Geräts beim IoT-Hub und Ankündigen der Modell-ID, die die Sammlung der vom Gerät implementierten DTDL-Schnittstellen angibt
- Synchronisieren der Eigenschaften, die in den DTDL-Schnittstellen zwischen dem Gerät und Ihrem IoT-Hub definiert sind
- Hinzufügen von Befehlshandlern für die Befehle, die in den DTDL-Schnittstellen definiert sind
- Senden von Telemetriedaten an den IoT-Hub.

## <a name="iot-hub"></a>IoT Hub

[IoT Hub](../iot-hub/about-iot-hub.md) ist ein in der Cloud gehosteter Dienst, der als zentraler Nachrichtenhub für die bidirektionale Kommunikation zwischen Ihrer IoT-Lösung und den Geräten dient, die von dieser verwaltet werden.

Ein IoT-Hub erfüllt folgende Aufgaben:

- Macht die von einem Gerät implementierte Modell-ID für eine Back-End-Lösung verfügbar.
- Verwaltet den digitalen Zwilling, der jedem Plug & Play-Gerät zugeordnet ist, das mit dem Hub verbunden ist.
- Leitet Telemetriedatenströme zur Verarbeitung oder Speicherung an andere Dienste weiter.
- Leitet Änderungsereignisse bei digitalen Zwillingen an andere Dienste weiter, um die Geräteüberwachung zu ermöglichen.

## <a name="backend-solution"></a>Back-End-Lösung

Eine Back-End-Lösung überwacht und steuert verbundene Geräte durch Interaktion mit digitalen Zwillingen im IoT-Hub. Verwenden Sie eines der Dienst-SDKs, um Ihre Back-End-Lösung zu implementieren. Um die Funktionen eines verbundenen Geräts zu verstehen, für das Lösungs-Back-End folgende Aktionen aus:

1. Ruft die Modell-ID ab, die das Gerät beim IoT-Hub registriert hat.
1. Verwendet die Modell-ID zum Abrufen der Schnittstellendefinitionen aus einem beliebigen Modellrepository.
1. Verwendet den Modellparser zum Extrahieren von Informationen aus den Schnittstellendefinitionen.

Die Back-End-Lösung kann anhand der Informationen aus den Schnittstellendefinitionen folgende Aktionen ausführen:

- Lesen der von Geräten gemeldeten Eigenschaftswerten.
- Aktualisieren schreibbarer Eigenschaften auf einem Gerät.
- Aufrufen von Befehlen, die von einem Gerät implementiert werden.
- Verstehen des Formats der von einem Gerät gesendeten Telemetriedaten.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun einen Überblick über die Architektur einer IoT Plug & Play-Lösung erhalten haben, können Sie in den nächsten Schritten mehr über die folgenden Themen erfahren:

- [Modellrepository](./concepts-model-repository.md)
- [Integration von digitale Zwillingsmodellen](./concepts-model-discovery.md)
- [Entwicklung für IoT Plug & Play](./concepts-developer-guide-device.md)
