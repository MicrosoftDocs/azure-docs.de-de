---
title: Azure IoT Hub – Entwickeln für eingeschränkte Geräte mit dem IoT Hub-SDK für C
description: Entwicklerhandbuch – Anleitungen zum Entwickeln mit Azure IoT-SDKs für eingeschränkte Geräte.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 5a43eb2537ebc09ffcb524a4426d7a8c9bec560b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500001"
---
# <a name="develop-for-constrained-devices-using-azure-iot-c-sdk"></a>Entwickeln für eingeschränkte Geräte mit dem Azure IoT-SDK für C

Das Azure IoT Hub SDK für C ist in ANSI C (C99) geschrieben, wodurch es sich gut für eine Vielzahl von Plattformen mit geringem Datenträger- und Arbeitsspeicherbedarf eignet. Der empfohlene Arbeitsspeicher beträgt mindestens 64 KB. Der genaue Arbeitsspeicherbedarf hängt jedoch vom verwendeten Protokoll, der Anzahl der geöffneten Verbindungen sowie der Zielplattform ab.
> [!NOTE]
> * Durch das Azure IoT-SDK für C werden regelmäßig Informationen zum Ressourcenverbrauch veröffentlicht, um die Entwicklung zu unterstützen.  Besuchen Sie unser [GitHub-Repository](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md) und überprüfen Sie den neuesten Vergleichstest.
>

Das SDK für C ist in Paketform über apt-get NuGet und MBED verfügbar. Wenn Sie eingeschränkte Geräte zum Ziel haben, können Sie das SDK lokal für Ihre Zielplattform erstellen. Diese Dokumentation veranschaulicht, wie mit [cmake](https://cmake.org/) bestimmte Funktionen entfernt werden, um den Platzbedarf des SDK für C zu reduzieren. Darüber hinaus werden in dieser Dokumentation die bewährten Programmiermodelle für die Arbeit mit eingeschränkten Geräten erläutert.

## <a name="building-the-c-sdk-for-constrained-devices"></a>Erstellen des SDK für C für eingeschränkte Geräte

Erstellen Sie das SDK für C für eingeschränkte Geräte.

> [!NOTE]
> Das Embedded C SDK ist eine Alternative für eingeschränkte Geräte, bei der der Ansatz „Bring Your Own Network“ (BYON) unterstützt wird. IoT-Entwickler haben die Möglichkeit, den MQTT-Client, TLS und Sockets selbst bereitzustellen, um eine Gerätelösung zu erstellen. [Lesen Sie die weiteren Informationen zum Embedded C SDK](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot).

### <a name="prerequisites"></a>Voraussetzungen

Führen Sie die in diesem [Installationshandbuch für das SDK für C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md) (in englischer Sprache) beschriebenen Schritte aus, um Ihre Entwicklungsumgebung auf das Erstellen des SDK für C vorzubereiten. Vor dem Erstellen mit „cmake“ können Sie cmake-Flags zum Entfernen nicht verwendeter Funktionen aufrufen.

### <a name="remove-additional-protocol-libraries"></a>Entfernen zusätzlicher Protokollbibliotheken

Das SDK für C unterstützt derzeit fünf Protokolle: MQTT, MQTT über WebSocket, AMQPs, AMQP über WebSocket und HTTPS. Bei den meisten Szenarien müssen auf einem Client ein bis zwei Protokolle ausgeführt werden. Daher können Sie die Protokollbibliothek, die Sie nicht verwenden, aus dem SDK entfernen. Weitere Informationen zur Auswahl des geeigneten Kommunikationsprotokolls für Ihr Szenario finden Sie unter [Referenz – Auswählen eines Kommunikationsprotokolls](iot-hub-devguide-protocols.md). MQTT ist beispielsweise ein einfaches Protokoll, das für eingeschränkte Geräte oft besser geeignet ist.

Sie können die AMQP- und HTTP-Bibliotheken mit dem folgenden cmake-Befehl entfernen:

```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

### <a name="remove-sdk-logging-capability"></a>Entfernen der SDK-Protokollierungsfunktion

Das SDK für C bietet insgesamt eine umfangreiche Protokollierung, um das Debuggen zu erleichtern. Sie können die Protokollierungsfunktion für Produktionsgeräte mit dem folgenden cmake-Befehl entfernen:

```
cmake -Dno_logging=OFF <Path_to_cmake>
```

### <a name="remove-upload-to-blob-capability"></a>Entfernen der Funktion „In Blobspeicher hochladen“

Sie können umfangreiche Dateien mithilfe der integrierten Funktion im SDK in den Azure-Speicher hochladen. Azure IoT Hub fungiert als Verteiler für ein zugeordnetes Azure-Speicherkonto. Mit dieser Funktion können Sie Mediendateien, große Telemetriebatches und Protokolle senden. Weitere Informationen erhalten Sie unter [Hochladen von Dateien mit IoT Hub](iot-hub-devguide-file-upload.md). Wenn diese Funktionalität für die Anwendung nicht erforderlich ist, können Sie diese Funktion mit dem folgenden cmake-Befehl entfernen:

```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```

### <a name="running-strip-on-linux-environment"></a>Ausführen des strip-Befehls in Linux-Umgebung

Wenn die Binärdateien auf einem Linux-System ausgeführt werden, können Sie mit dem [strip-Befehl](https://en.wikipedia.org/wiki/Strip_(Unix)) die Größe der endgültigen Anwendung nach dem Kompilieren reduzieren.

```
strip -s <Path_to_executable>
```

## <a name="programming-models-for-constrained-devices"></a>Programmiermodelle für eingeschränkte Geräte

Sehen Sie sich als Nächstes die Programmiermodelle für eingeschränkte Geräte an.

### <a name="avoid-using-the-serializer"></a>Vermeiden der Verwendung des Serialisierungsprogramms

Das SDK für C verfügt über ein optionales [Serialisierungsprogramm](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer), mit dem Sie deklarative Zuordnungstabellen zum Definieren von Methoden sowie Eigenschaften von Gerätezwillingen verwenden können. Das Serialisierungsprogramm soll die Entwicklung vereinfachen, verursacht aber zusätzlichen Aufwand, was für eingeschränkte Geräte nicht optimal ist. Ziehen Sie in diesem Fall die Verwendung von APIs eines einfachen Clients und das Analysieren von JSON mithilfe eines grundlegenden Analysetools wie [parson](https://github.com/kgabis/parson) in Betracht.

### <a name="use-the-lower-layer-_ll_"></a>Verwenden der niedrigeren Ebene (_Lower Layer, LL_)

Das SDK für C unterstützt zwei Programmiermodelle. Eine Gruppe hat APIs mit dem Infix _LL_, das für die niedrigere Ebene steht. Dieser Satz von APIs ist weniger umfangreich und erstellt keine Arbeitsthreads, sodass der Benutzer die Planung manuell steuern muss. Die _LL_-APIs für den Geräteclient beispielsweise befinden sich in dieser [Headerdatei](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h). 

Eine andere Gruppe von APIs ohne den _LL_-Index wird als „Convenience Layer“ (Komfortebene) bezeichnet, auf der ein Arbeitsthread automatisch erstellt wird. Die Convenience Layer-APIs für den Geräteclient beispielsweise befinden sich in dieser [Headerdatei für IoT-Geräteclients](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h). Bei eingeschränkten Geräten, bei denen jeder zusätzliche Thread einen erheblichen Prozentsatz der Systemressourcen belegen kann, sollten Sie die Verwendung von _LL_-APIs in Betracht ziehen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Azure IoT C SDK-Architektur:
-    [Azure IoT C SDK-Quellcode](https://github.com/Azure/azure-iot-sdk-c/)
-    [Einführung in das Azure IoT-Geräte-SDK für C](iot-hub-device-sdk-c-intro.md)
