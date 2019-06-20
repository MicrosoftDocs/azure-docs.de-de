---
title: Verwalten von Konnektivität und zuverlässigem Messaging mithilfe von Azure IoT Hub-Geräte-SDKs
description: Erfahren Sie, wie Sie Gerätekonnektivität und Messaging mithilfe der Azure IoT Hub-Geräte-SDKs verbessern
services: iot-hub
author: yzhong94
ms.author: yizhon
ms.date: 07/07/2018
ms.topic: article
ms.service: iot-hub
ms.openlocfilehash: 9180c27e64f26c05e6e16007b74f9aa8a98bcfe5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61440300"
---
# <a name="manage-connectivity-and-reliable-messaging-by-using-azure-iot-hub-device-sdks"></a>Verwalten von Konnektivität und zuverlässigem Messaging mithilfe von Azure IoT Hub-Geräte-SDKs

Dieser Artikel enthält eine allgemeine Anleitung für die Entwicklung stabilerer Geräteanwendungen. Es wird erläutert, wie Sie die Features für Konnektivität und zuverlässiges Messaging in Azure IoT-Geräte-SDKs nutzen können. Ziel dieser Anleitung ist die Unterstützung bei folgenden Szenarien:

* Beheben einer getrennten Netzwerkverbindung

* Wechseln zwischen verschiedenen Netzwerkverbindungen

* Erneutes Verbinden aufgrund vorübergehender Verbindungsfehler im Dienst

Die Implementierungsdetails können je nach Sprache variieren. Weitere Informationen finden Sie in der API-Dokumentation sowie im jeweiligen SDK:

* [C/Python/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)

* [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

## <a name="designing-for-resiliency"></a>Entwerfen mit Blick auf Resilienz

IoT-Geräte nutzen häufig durch Unterbrechungen gekennzeichnete bzw. instabile Netzwerkverbindungen (z.B. über GSM oder Satellit). Bei der Interaktion der Geräte mit cloudbasierten Diensten können aufgrund unregelmäßiger Dienstverfügbarkeit und Störungen auf Infrastrukturebene oder kurzzeitiger Störungen Fehler auftreten. Eine auf einem Gerät ausgeführte Anwendung muss die Verbindungs- und Wiederverbindungsmechanismen sowie die Wiederholungslogik für das Senden und Empfangen von Nachrichten handhaben. Darüber hinaus hängen die Anforderungen für die Wiederholungsstrategie besonders vom IoT-Szenario, dem Kontext und den Funktionen des Geräts ab.

Die Azure IoT Hub-Geräte-SDKs zielen darauf ab, die Verbindung und Kommunikation von Cloud-zu-Gerät und Gerät-zu-Cloud zu vereinfachen. Diese SDKs stellen eine zuverlässige Möglichkeit für die Verbindung mit Azure IoT Hub dar und umfassen umfangreiche Optionen zum Senden und Empfangen von Nachrichten. Entwickler können zudem vorhandene Implementierungen ändern, um die optimale Wiederholungsstrategie für ein bestimmtes Szenario anzupassen.

Die relevanten SDK-Funktionen, die Konnektivität und zuverlässiges Messaging unterstützen, werden in den folgenden Abschnitten behandelt.

## <a name="connection-and-retry"></a>Verbindung und Wiederholung

Dieser Abschnitt bietet eine Übersicht über die verfügbaren Wiederverbindungs- und Wiederholungsmuster bei der Verwaltung von Verbindungen. Es werden Implementierungsschritte für unterschiedliche Wiederholungsrichtlinien in Ihrer Geräteanwendung und relevante APIs der Geräte-SDKs erläutert.

### <a name="error-patterns"></a>Fehlermuster

Verbindungsfehler können auf vielen Ebenen auftreten:

* Netzwerkfehler: getrennter Socket und Namensauflösungsfehler

* Fehler auf Protokollebene beim HTTP-, AMQP- und MQTT-Datentransport: getrennte Verbindungen oder abgelaufene Sitzungen

* Fehler auf Anwendungsebene, die auf lokale Fehler zurückzuführen sind: ungültige Anmeldeinformationen oder Dienstverhalten (z.B. Kontingentüberschreitung oder Drosselung)

Die Geräte-SDKs erkennen Fehler auf allen drei Ebenen. Fehler im Zusammenhang mit dem Betriebssystem und Hardwarefehler werden von den Geräte-SDKs weder erkannt noch behandelt. Der SDK-Entwurf basiert auf der Anleitung [Behandeln vorübergehender Fehler](/azure/architecture/best-practices/transient-faults#general-guidelines) im Azure Architecture Center.

### <a name="retry-patterns"></a>Wiederholungsmuster

Die folgenden Schritte beschreiben den Wiederholungsvorgang bei Erkennen von Verbindungsfehlern:

1. Das SDK erkennt den Fehler und den damit verbundenen Fehler im Netzwerk, Protokoll oder in der Anwendung.

2. Das SDK verwendet den Fehlerfilter, um den Fehlertyp zu ermitteln und zu ermitteln, ob eine Wiederholung erforderlich ist.

3. Wenn das SDK einen **nicht behebbaren Fehler** erkennt, werden Vorgänge wie Verbinden, Senden und Empfangen abgebrochen. Das SDK benachrichtigt den Benutzer. Beispiele für nicht behebbare Fehler sind Authentifizierungsfehler oder Fehler aufgrund eines falschen Endpunkts.

4. Wenn das SDK einen **behebbaren Fehler** erkennt, wird die Wiederholung entsprechend der angegebenen Wiederholungsrichtlinie durchgeführt, bis das definierte Zeitlimit abläuft.  Beachten Sie, dass das SDK standardmäßig eine Wiederholungsrichtlinie per **exponentiellem Backoff mit Jitter** verwendet.
5. Wenn das definierte Zeitlimit abläuft, versucht das SDK nicht mehr, eine Verbindung herzustellen oder eine Nachricht zu senden, und benachrichtigt den Benutzer.

6. Das SDK ermöglicht dem Benutzer, einen Rückruf anzufügen, um Änderungen am Verbindungsstatus zu empfangen.

Die SDKs umfassen drei Wiederholungsrichtlinien:

* **Exponentielles Backoff mit Jitter**: Diese standardmäßige Wiederholungsrichtlinie ist am Anfang eher aggressiv, verlangsamt sich und erreicht dann eine maximale Verzögerung. Der Entwurf basiert auf der [Wiederholungsanleitung](https://docs.microsoft.com/azure/architecture/best-practices/retry-service-specific) im Azure Architecture Center. 

* **Benutzerdefinierte Wiederholung**: Für einige SDK-Sprachen können Sie eine benutzerdefinierte Wiederholungsrichtlinie entwerfen, die für Ihr Szenario besser geeignet ist, und diese dann in die Wiederholungsrichtlinie (RetryPolicy) einfügen. Für das C SDK ist die benutzerdefinierte Wiederholung nicht verfügbar.

* **Keine Wiederholung**: Sie können die Wiederholungsrichtlinie auf „no retry“ festlegen, wodurch die Wiederholungslogik deaktiviert wird. Das SDK versucht, einmal eine Verbindung herzustellen und einmal eine Nachricht zu senden, vorausgesetzt, die Verbindung ist hergestellt. Diese Richtlinie wird typischerweise in Szenarien verwendet, in denen Bandbreiten- oder Kostenaspekte eine Rolle spielen. Bei dieser Option gehen Nachrichten, die nicht gesendet werden können, verloren und können nicht wiederhergestellt werden.

### <a name="retry-policy-apis"></a>APIs für Wiederholungsrichtlinien

   | SDK | SetRetryPolicy-Methode | Richtlinienimplementierungen | Implementierungsleitfaden |
   |-----|----------------------|--|--|
   |  C/Python/iOS  | [IOTHUB_CLIENT_RESULT IoTHubClient_SetRetryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/2018-05-04/iothub_client/inc/iothub_client.h#L188)        | **Standard:** [IOTHUB_CLIENT_RETRY_EXPONENTIAL_BACKOFF](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)<BR>**Benutzerdefiniert:** Verfügbare [retryPolicy](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies) verwenden<BR>**Keine Wiederholung:** [IOTHUB_CLIENT_RETRY_NONE](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-retry-policies)  | [C-/Python-/iOS-Implementierung](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#)  |
   | Java| [SetRetryPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.deviceclientconfig.setretrypolicy?view=azure-java-stable)        | **Standard:** [ExponentialBackoffWithJitter-Klasse](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)<BR>**Benutzerdefiniert:** [RetryPolicy-Schnittstelle](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/RetryPolicy.java) implementieren<BR>**Keine Wiederholung:** [NoRetry-Klasse](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/src/main/java/com/microsoft/azure/sdk/iot/device/transport/NoRetry.java)  | [Java-Implementierung](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md) |
   | .NET| [DeviceClient.SetRetryPolicy](/dotnet/api/microsoft.azure.devices.client.deviceclient.setretrypolicy?view=azure-dotnet) | **Standard:** [ExponentialBackoff-Klasse](/dotnet/api/microsoft.azure.devices.client.exponentialbackoff?view=azure-dotnet)<BR>**Benutzerdefiniert:** [IRetryPolicy-Schnittstelle](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.iretrypolicy?view=azure-dotnet) implementieren<BR>**Keine Wiederholung:** [NoRetry-Klasse](/dotnet/api/microsoft.azure.devices.client.noretry?view=azure-dotnet) | [C#-Implementierung](https://github.com/Azure/azure-iot-sdk-csharp) | |
   | Knoten| [setRetryPolicy](/javascript/api/azure-iot-device/client?view=azure-iot-typescript-latest) | **Standard:** [ExponentialBackoffWithJitter-Klasse](/javascript/api/azure-iot-common/exponentialbackoffwithjitter?view=azure-iot-typescript-latest)<BR>**Benutzerdefiniert:** [RetryPolicy-Schnittstelle](/javascript/api/azure-iot-common/retrypolicy?view=azure-iot-typescript-latest) implementieren<BR>**Keine Wiederholung:** [NoRetry-Klasse](/javascript/api/azure-iot-common/noretry?view=azure-iot-typescript-latest) | [Node-Implementierung](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them) |

Die folgenden Codebeispiele veranschaulichen diesen Ablauf:

#### <a name="net-implementation-guidance"></a>Leitfaden zur .NET-Implementierung

Das folgende Codebeispiel veranschaulicht die Definition und Festlegung der Standardwiederholungsrichtlinie:

   ```csharp
   # define/set default retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(int.MaxValue, TimeSpan.FromMilliseconds(100), TimeSpan.FromSeconds(10), TimeSpan.FromMilliseconds(100));
   SetRetryPolicy(retryPolicy);
   ```

Um eine hohe CPU-Auslastung zu vermeiden, werden die Wiederholungsversuche gedrosselt, wenn im Code unmittelbar Fehler auftreten, beispielsweise wenn kein Netzwerk oder keine Route zum Ziel vorhanden ist. Die Mindestzeit für den nächsten Wiederholungsversuch beträgt 1 Sekunde.

Wenn der Dienst mit einem Drosselungsfehler reagiert, ist die Wiederholungsrichtlinie anders und kann nicht über die öffentliche API geändert werden:

   ```csharp
   # throttled retry policy
   RetryPolicy retryPolicy = new ExponentialBackoff(RetryCount, TimeSpan.FromSeconds(10), 
     TimeSpan.FromSeconds(60), TimeSpan.FromSeconds(5)); SetRetryPolicy(retryPolicy);
   ```

Der Wiederholungsmechanismus wird nach der Einstellung `DefaultOperationTimeoutInMilliseconds`, die derzeit auf 4 Minuten festgelegt ist, abgebrochen.

#### <a name="other-languages-implementation-guidance"></a>Leitfaden zur Implementierung anderer Sprachen

Codebeispiele in anderen Sprachen finden Sie in den folgenden Dokumenten zur Implementierung. Das Repository enthält Beispiele zur Verwendung von APIs für Wiederholungsrichtlinien.

* [C/Python/iOS SDK](https://github.com/azure/azure-iot-sdk-c)

* [.NET SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/devdoc/requirements/retrypolicy.md)

* [Java SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-client/devdoc/requirement_docs/com/microsoft/azure/iothub/retryPolicy.md)

* [Node SDK](https://github.com/Azure/azure-iot-sdk-node/wiki/Connectivity-and-Retries#types-of-errors-and-how-to-detect-them)

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden von Geräte- und Dienst-SDKs](./iot-hub-devguide-sdks.md)

* [Verwenden des Azure IoT-Geräte-SDKs für C](./iot-hub-device-sdk-c-intro.md)

* [Entwickeln für eingeschränkte Geräte](./iot-hub-devguide-develop-for-constrained-devices.md)

* [Entwickeln für mobile Geräte](./iot-hub-how-to-develop-for-mobile-devices.md)

* [Troubleshoot device disconnects](iot-hub-troubleshoot-connectivity.md) (Problembehandlung bei getrennten Geräteverbindungen)