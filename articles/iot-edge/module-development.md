---
title: Entwickeln von Modulen für Azure IoT Edge | Microsoft-Dokumentation
description: Entwickeln von benutzerdefinierten Modellen für Azure IoT Edge zur Kommunikation mit Runtime und IoT Hub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8907af07fff7b315eec263d38b686c17218ed9d2
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445471"
---
# <a name="develop-your-own-iot-edge-modules"></a>Entwickeln eigener IoT Edge-Module

Azure IoT Edge-Module können mit anderen Azure-Diensten eine Verbindung herstellen und zu Ihrer größeren Clouddatenpipeline beitragen. In diesem Artikel wird beschrieben, wie Sie Module für die Kommunikation mit der IoT Edge-Runtime und IoT Hub und damit mit dem Rest der Azure-Cloud entwickeln können.

## <a name="iot-edge-runtime-environment"></a>IoT Edge-Laufzeitumgebung

Die IoT Edge-Laufzeit bietet die Infrastruktur, um die Funktionalität mehrerer IoT Edge-Module zu integrieren und auf IoT Edge-Geräten bereitstellen. Jedes beliebige Programm kann als IoT Edge-Modul verpackt werden. Wenn Sie die Kommunikations- und Verwaltungsfunktionalitäten von IoT Edge umfassend nutzen möchten, kann ein in einem Modul ausgeführtes Programm über das Azure IoT-Geräte-SDK eine Verbindung mit dem lokalen IoT Edge-Hub herstellen.
::: moniker range=">=iotedge-2020-11"
Module können auch einen beliebigen MQTT-Client verwenden, um eine Verbindung mit dem lokalen IoT Edge-Hub-MQTT-Broker herzustellen.
::: moniker-end

### <a name="packaging-your-program-as-an-iot-edge-module"></a>Verpacken des Programms als IoT Edge-Modul

Zum Bereitstellen des Programms auf einem IoT Edge-Gerät muss es zunächst in einen Container integriert und mit einem Docker-kompatiblen Modul ausgeführt werden. IoT Edge verwendet [Moby](https://github.com/moby/moby), das Open Source-Projekt hinter Docker, als Docker-kompatibles Modul. Die gleichen Parameter, die Sie für Docker verwenden, können auch an Ihre IoT Edge-Module übermittelt werden. Weitere Informationen finden Sie unter [Konfigurieren von Erstellungsoptionen für Container für IoT Edge-Module](how-to-use-create-options.md).

## <a name="using-the-iot-edge-hub"></a>Verwenden des IoT Edge-Hubs

Der IoT Edge-Hub bietet zwei Hauptfunktionalitäten: Proxy für IoT Hub und lokale Kommunikation.

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Herstellen einer Verbindung von einem Modul mit dem IoT Edge Hub

Das Herstellen einer Verbindung mit dem lokalen IoT Edge-Hub von einem Modul umfasst die gleichen Verbindungsschritte wie bei allen anderen Clients. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit dem IoT Edge-Hub](iot-edge-runtime.md#connecting-to-the-iot-edge-hub).

Wenn Sie IoT Edge-Routing über AMQP oder MQTT verwenden möchten, können Sie den ModuleClient aus dem Azure IoT SDK verwenden. Erstellen Sie eine ModuleClient-Instanz, um Ihr Modul mit dem IoT Edge-Hub zu verbinden, der auf dem Gerät ausgeführt wird. Dies erfolgt auf ähnliche Weise wie DeviceClient-Instanzen eine Verbindung zwischen IoT-Geräten und IoT Hub herstellen. Weitere Informationen zur ModuleClient-Klasse und ihre Kommunikationsmethoden finden Sie in der API-Referenz für Ihre bevorzugte SDK-Sprache: [C#](/dotnet/api/microsoft.azure.devices.client.moduleclient), [C](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient), [Java](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient) oder [Node.js](/javascript/api/azure-iot-device/moduleclient).

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Um den IoT Edge-MQTT-Broker verwenden zu können, müssen Sie Ihren eigenen MQTT-Client verwenden und die Verbindung selbst mit den Informationen initiieren, die Sie aus der IoT Edge-Daemon-Workload-API abrufen. <!--Need to add details here-->

Weitere Informationen zur Entscheidung zwischen Routing oder Veröffentlichen/Abonnieren mit dem MQTT-Broker finden Sie unter [Lokale Kommunikation](iot-edge-runtime.md#local-communication).

### <a name="mqtt-broker-primitives"></a>Primitive des MQTT-Brokers

#### <a name="send-a-message-on-a-user-defined-topic"></a>Senden einer Nachricht zu einem benutzerdefinierten Thema

Mit dem IoT Edge-MQTT-Broker können Sie Nachrichten für beliebige benutzerdefinierte Themen veröffentlichen. Dazu können Sie das Modul autorisieren, Nachrichten zu bestimmten Themen zu veröffentlichen, und dann ein Token aus der Workload-API abrufen, das beim Herstellen einer Verbindung mit dem MQTT-Broker als Kennwort verwendet werden soll. Veröffentlichen Sie schließlich Nachrichten zu den autorisierten Themen mit einem MQTT-Client Ihrer Wahl.

#### <a name="receive-messages-on-a-user-defined-topic"></a>Empfangen von Nachrichten zu einem benutzerdefinierten Thema

Das Empfangen von Nachrichten mit dem IoT Edge-MQTT-Broker verläuft ganz ähnlich. Stellen Sie zunächst sicher, dass das Modul autorisiert ist, bestimmte Themen zu abonnieren, und rufen Sie dann ein Token aus der Workload-API, das beim Herstellen einer Verbindung mit dem MQTT-Broker als Kennwort verwendet werden soll. Abonnieren Sie schließlich Nachrichten zu den autorisierten Themen mit einem MQTT-Client Ihrer Wahl.

::: moniker-end

### <a name="iot-hub-primitives"></a>IoT Hub-Primitive

IoT Hub behandelt eine Modulinstanz analog zu einem Gerät. Dies bedeutet Folgendes:

* Sie kann [Gerät-zu-Cloud-Nachrichten](../iot-hub/iot-hub-devguide-messaging.md) senden.
* Sie kann [direkte Methoden](../iot-hub/iot-hub-devguide-direct-methods.md) empfangen, die speziell an ihre Identität gerichtet sind.
* Die Modulinstanz hat einen Modulzwilling, der vom [Gerätezwilling](../iot-hub/iot-hub-devguide-device-twins.md) und den anderen Modulzwillingen des Geräts getrennt und isoliert ist.

Derzeit können Module keine Cloud-zu-Gerät-Nachrichten empfangen oder Dateien hochladen.

Wenn Sie ein Modul schreiben, können Sie eine Verbindung mit dem IoT Edge-Hub herstellen und IoT Hub-Primitive wie bei der Verwendung von IoT Hub mit einer Geräteanwendung verwenden. Der einzige Unterschied zwischen IoT Edge-Modulen und IoT-Geräteanwendungen besteht darin, dass Sie auf die Modulidentität (statt auf die Geräteidentität) verweisen müssen.

#### <a name="device-to-cloud-messages"></a>D2C-Nachrichten

Ein IoT Edge-Modul kann Nachrichten über den IoT Edge-Hub, der als lokaler Broker fungiert und Nachrichten an die Cloud weitergibt, an die Cloud senden. Um eine komplexe Verarbeitung von Gerät-zu-Cloud-Nachrichten zu ermöglichen, kann ein IoT Edge-Modul auch Nachrichten abfangen und verarbeiten, die von anderen Modulen oder Geräten an den lokalen IoT Edge-Hub gesendet werden, sowie neue Nachrichten mit verarbeiteten Daten senden. So können Ketten von IoT Edge-Modulen gebildet werden, um lokale Verarbeitungspipelines zu erstellen.

Verwenden Sie zum Senden von Gerät-zu-Cloud-Telemetrienachrichten mithilfe von Routing den ModuleClient aus dem Azure IoT SDK. Durch das Azure IoT SDK können alle Module das Konzept der *Eingabe-* und *Ausgabe* endpunkte für Module nutzen, die speziellen MQTT-Themen zugeordnet sind. Verwenden Sie die `ModuleClient.sendMessageAsync`-Methode, die dann Nachrichten auf dem Ausgabeendpunkt Ihres Moduls sendet. Konfigurieren Sie dann eine Route in edgeHub, um diesen Ausgabeendpunkt an IoT Hub zu senden.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Das Senden von Gerät-zu-Cloud-Telemetrienachrichten mit dem MQTT-Broker ist mit dem Veröffentlichen von Nachrichten in benutzerdefinierten Themen vergleichbar, verwendet jedoch das folgende spezielle IoT Hub-Thema für Ihr Modul: `devices/<device_name>/<module_name>/messages/events`. Autorisierungen müssen entsprechend eingerichtet werden. Die MQTT-Bridge muss ebenfalls so konfiguriert werden, dass sie die Nachrichten zu diesem Thema an die Cloud weiterleiten.

::: moniker-end

Zum Verarbeiten von Nachrichten mithilfe des Routings richten Sie zuerst eine Route ein, die Nachrichten von einem anderen Endpunkt (Modul oder Gerät) an den Eingabeendpunkt des Moduls sendet, und lauschen Sie dann auf dem Eingabeendpunkt des Moduls auf die Nachrichten. Jedes Mal, wenn eine neue Nachricht zurückgesendet wird, wird eine Rückruffunktion durch das Azure IoT SDK ausgelöst. Verarbeiten Sie Ihre Nachricht mit dieser Rückruffunktion, und senden Sie optional neue Nachrichten an die Warteschlange des Modulendpunkts.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Das Verarbeiten von Nachrichten mit dem MQTT-Broker ähnelt dem Abonnieren von Nachrichten zu benutzerdefinierten Themen, es werden jedoch die speziellen IoT Edge-Themen der Ausgabewarteschlange Ihres Moduls verwendet: `devices/<device_name>/<module_name>/messages/events`. Autorisierungen müssen entsprechend eingerichtet werden. Optional können Sie neue Nachrichten zu den Themen Ihrer Wahl senden.

::: moniker-end

#### <a name="twins"></a>Zwillinge

Zwillinge sind eine der Primitiven, die von IoT Hub bereitgestellt werden. Diese sind JSON-Dokumente, in denen Statusinformationen gespeichert werden, z. B. Metadaten, Konfigurationen und Bedingungen. Jedes Modul oder Gerät verfügt über einen eigenen Zwilling.

Um einen Modulzwilling mit dem Azure IoT SDK abzurufen, rufen Sie die `ModuleClient.getTwin`-Methode auf.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Um einen Modulzwilling mit einem beliebigen MQTT-Client abzurufen, ist ein etwas höherer Aufwand erforderlich, da das Abrufen eines Zwillings kein typisches MQTT-Muster ist. Zuerst muss das Modul das spezielle IoT Hub-Thema `$iothub/twin/res/#` abonnieren. Dieser Themenname wird von IoT Hub geerbt, und alle Geräte/Module müssen dasselbe Thema abonnieren. Dies bedeutet nicht, dass Geräte den Zwilling voneinander empfangen. IoT Hub und edgeHub wissen, welcher Zwilling wohin übermittelt werden sollte, auch wenn alle Geräte auf denselben Themennamen lauschen. Nachdem das Abonnement erstellt wurde, muss das Modul den Zwilling anfordern, indem es eine Nachricht für das spezielle IoT Hub-Thema mit der Anforderungs-ID `$iothub/twin/GET/?$rid=1234` veröffentlicht. Diese Anforderungs-ID ist eine beliebige ID (d. h. eine GUID), die von IoT Hub zusammen mit den angeforderten Daten zurückgesendet wird. Auf diese Weise kann ein Client seine Anforderungen mit den Antworten koppeln. Der Ergebniscode ist ein HTTP-ähnlicher Statuscode, bei dem eine erfolgreiche Ausführung als „200“ codiert ist.

::: moniker-end

Um einen Modulzwillingspatch mit dem Azure IoT SDK zu empfangen, implementieren Sie eine Rückruffunktion, und registrieren Sie sie mit der `ModuleClient.moduleTwinCallback`-Methode aus dem Azure IoT SDK, damit Ihre Rückruffunktion bei jedem Eingang eines Zwillingspatches ausgelöst wird.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Um einen Modulzwillingspatch mit einem beliebigen MQTT-Client zu empfangen, wird ein mit dem Empfang von vollständigen Zwillingen vergleichbarer Prozess verwendet: Ein Client muss das spezielle IoT Hub-Thema `$iothub/twin/PATCH/properties/desired/#` abonnieren. Anschließend wird das Abonnement vom Client empfangen, wenn IoT Hub eine Änderung des gewünschten Abschnitts des Zwillings sendet.

::: moniker-end

#### <a name="receive-direct-methods"></a>Empfangen von direkten Methoden

Wenn Sie eine direkte Methode mit dem Azure IoT SDK empfangen möchten, implementieren Sie eine Rückruffunktion, und registrieren Sie sie mit der `ModuleClient.methodCallback`-Methode aus dem Azure IoT SDK, damit Ihre Rückruffunktion bei jedem Eingang einer direkten Methode ausgelöst wird.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Um eine direkte Methode mit einem beliebigen MQTT-Client zu empfangen, wird ein dem Empfangen von Zwillingspatches ähnlicher Prozess verwendet. Der Client muss wiederum bestätigen, dass er den Aufruf empfangen hat, und er kann gleichzeitig Informationen zurücksenden. Das spezielle IoT Hub-Thema, das abonniert werden muss, ist `$iothub/methods/POST/#`.

::: moniker-end

## <a name="language-and-architecture-support"></a>Unterstützung für Sprache und Architektur

IOT Edge unterstützt mehrere Betriebssysteme, Gerätearchitekturen und Entwicklungssprachen, sodass Sie das Szenario erstellen können, das Ihren Anforderungen entspricht. Erfahren Sie in diesem Abschnitt, welche Optionen Ihnen zum Entwickeln von benutzerdefinierten IOT Edge-Modulen zur Verfügung stehen. Weitere Informationen zur Toolunterstützung und Anforderungen für die einzelnen Sprachen finden Sie unter [Vorbereiten Ihrer Entwicklungs- und Testumgebung für IOT Edge](development-environment.md).

### <a name="linux"></a>Linux

Für alle Sprachen in der folgenden Tabelle unterstützt IOT Edge die Entwicklung für AMD64- und ARM32-Linux-Geräte.

| Programmiersprache | Entwicklungstools |
| -------------------- | ----------------- |
| C | Visual Studio Code<br>Visual Studio 2017/2019 |
| C# | Visual Studio Code<br>Visual Studio 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>Die Unterstützung für das Entwickeln und Debuggen von ARM64-Linux-Geräten befindet sich in der [öffentlichen Vorschau](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Weitere Informationen finden Sie unter [Develop and debug ARM64 IoT Edge modules in Visual Studio Code (preview) (Entwickeln und Debuggen von ARM64 IoT Edge-Modulen in Visual Studio Code (Vorschauversion))](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

### <a name="windows"></a>Windows

Für alle Sprachen in der folgenden Tabelle unterstützt IOT Edge die Entwicklung für AMD64-Windows-Geräte.

| Programmiersprache | Entwicklungstools |
| -------------------- | ----------------- |
| C | Visual Studio 2017/2019 |
| C# | Visual Studio Code (keine Debuggingfunktionen)<br>Visual Studio 2017/2019 |

## <a name="next-steps"></a>Nächste Schritte

[Vorbereiten Ihrer Entwicklungs- und Testumgebung für IoT Edge](development-environment.md)

[Verwenden von Visual Studio zum Entwickeln von C#-Modulen für IoT Edge](how-to-visual-studio-develop-module.md)

[Verwenden von Visual Studio Code zum Entwickeln von Modulen für IoT Edge](how-to-vs-code-develop-module.md)

[Grundlegendes zu Azure IoT Hub-SDKs und deren Verwendung](../iot-hub/iot-hub-devguide-sdks.md)
