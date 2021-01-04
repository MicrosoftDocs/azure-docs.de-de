---
title: Hinzufügen von Korrelations-IDs zu IoT-Nachrichten mit der verteilten Ablaufverfolgung (Vorschau)
description: Hier erfahren Sie, wie Sie IoT-Nachrichten mithilfe der verteilten Ablaufverfolgung in allen von Ihrer Lösung verwendeten Azure-Diensten überwachen.
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: jlian
ms.custom:
- amqp
- mqtt
- fasttrack-edit
- iot
ms.openlocfilehash: f8d37cf8f23de1d0535c7a9ff4a95ac217eddf74
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452398"
---
# <a name="trace-azure-iot-device-to-cloud-messages-with-distributed-tracing-preview"></a>Überwachen von Gerät-zu-Cloud-Nachrichten in Azure IoT mit der verteilten Ablaufverfolgung (Vorschau)

Microsoft Azure IoT Hub unterstützt derzeit die verteilte Ablaufverfolgung als [Previewfunktion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

IoT Hub ist einer der ersten Azure-Dienste, der die verteilte Ablaufverfolgung unterstützt. Wenn die verteilte Ablaufverfolgung nach und nach in weiteren Azure-Diensten unterstützt wird, können Sie IoT-Nachrichten in allen in Ihrer Lösung enthaltenen Azure-Diensten überwachen. Hintergrundinformationen zur verteilten Ablaufverfolgung finden Sie unter [Verteilte Ablaufverfolgung](../azure-monitor/app/distributed-tracing.md).

Durch die Aktivierung der verteilten Ablaufverfolgung für IoT Hub können Sie die folgenden Aktionen ausführen:

- Überwachen Sie ganz präzise den Fluss jeder Nachricht in IoT Hub mit dem [Überwachungskontext](https://github.com/w3c/trace-context). Dieser Überwachungskontext umfasst Korrelations-IDs, mit denen Sie Ereignisse von einer Komponente mit Ereignissen einer anderen Komponente korrelieren können. Anwendbar ist dies mithilfe eines [Gerätezwillings](iot-hub-devguide-device-twins.md) auf einige oder alle IoT-Gerätenachrichten.
- Lassen Sie den Überwachungskontext in [Azure Monitor-Protokollen](monitor-iot-hub.md) automatisch protokollieren.
- Messen und analysieren Sie den Nachrichtenfluss sowie die Latenzen von Geräten zu IoT Hub und Routingendpunkten.
- Überlegen Sie, wie Sie die verteilte Ablaufverfolgung für Nicht-Azure-Dienste in Ihrer IoT-Lösung implementieren möchten.

In diesem Artikel verwenden Sie das [Azure IoT-Geräte-SDK für C](iot-hub-device-sdk-c-intro.md) mit der verteilten Ablaufverfolgung. An der Unterstützung der verteilten Ablaufverfolgung für die anderen SDKs wird noch gearbeitet.

## <a name="prerequisites"></a>Voraussetzungen

- Die Vorschau der verteilten Ablaufverfolgung wird derzeit nur für IoT Hubs unterstützt, die in folgenden Regionen erstellt werden:

  - **Europa, Norden**
  - **Asien, Südosten**
  - **USA, Westen 2**

- In diesem Artikel wird davon ausgegangen, dass Sie mit dem Senden von Telemetrienachrichten an Ihre IoT Hub-Instanz vertraut sind. Absolvieren Sie zunächst den Schnellstart zum [Senden von Telemetriedaten mit C](quickstart-send-telemetry-c.md).

- Registrieren Sie ein Gerät bei Ihrer IoT Hub-Instanz (entsprechende Schritte werden in jedem Schnellstart beschrieben), und notieren Sie sich die Verbindungszeichenfolge.

- Installieren Sie die aktuelle Version von [Git](https://git-scm.com/download/).

## <a name="configure-iot-hub"></a>Konfigurieren von IoT Hub

In diesem Abschnitt konfigurieren Sie eine IoT Hub-Instanz so, dass die Attribute der verteilten Ablaufverfolgung (Korrelations-IDs und Zeitstempel) protokolliert werden.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrer IoT Hub-Instanz.

1. Scrollen Sie im linken Bereich für den IoT Hub nach unten zum Abschnitt **Überwachung**, und klicken Sie auf **Diagnoseeinstellungen**.

1. Klicken Sie auf **Diagnoseeinstellung hinzufügen**.

1. Geben Sie im Feld **Name** einen Namen für eine neue Diagnoseeinstellung ein, z.B. **DistributedTracingSettings**.

1. Wählen Sie eine oder mehrere der folgenden Optionen aus, mit denen festgelegt wird, wohin die Protokollierung gesendet wird:

    - **In einem Speicherkonto archivieren:** Konfigurieren Sie ein Speicherkonto für die Protokollierungsinformationen.
    - **An einen Event Hub streamen:** Konfigurieren Sie einen Event Hub für die Protokollierungsinformationen.
    - **An Log Analytics senden:** Konfigurieren Sie einen Log Analytics-Arbeitsbereich für die Protokollierungsinformationen.

1. Wählen Sie im Abschnitt **Protokoll** die Vorgänge aus, für die Protokollierungsinformationen erstellt werden sollen.

    Stellen Sie sicher, dass Sie **DistributedTracing**, auswählen, und legen Sie unter **Aufbewahrung (Tage)** fest, für wie viele Tage die Protokollierung beibehalten werden soll. Die Dauer der Protokollbeibehaltung wirkt sich auf die Speicherkosten aus.

    ![Screenshot: Kategorie „DistributedTracing“ in den IoT-Diagnoseeinstellungen](./media/iot-hub-distributed-tracing/diag-logs.png)

1. Klicken Sie für die neue Einstellung auf **Speichern**.

1. (Optional:) Damit die Nachrichten in verschiedene Speicherorte übertragen werden, richten Sie [Routingregeln an mindestens zwei unterschiedlichen Endpunkten](iot-hub-devguide-messages-d2c.md) ein.

Nachdem die Protokollierung aktiviert wurde, wird in IoT Hub bei Nachrichten mit gültigen Überwachungseigenschaften in folgenden Fällen ein Protokoll erstellt:

- Die Nachricht wird im IoT Hub-Gateway empfangen.
- Die Nachricht wird vom IoT Hub verarbeitet.
- Die Nachricht wird an benutzerdefinierte Endpunkte weitergeleitet. Das Routing muss aktiviert sein.

Weitere Informationen zu diesen Protokollen und deren Schemas finden Sie unter [Überwachen von IoT Hub](monitor-iot-hub.md) und [Verteilte Ablaufverfolgung in IoT Hub-Ressourcenprotokollen](monitor-iot-hub-reference.md#distributed-tracing-preview).

## <a name="set-up-device"></a>Einrichten des Geräts

In diesem Abschnitt bereiten Sie eine Entwicklungsumgebung zur Verwendung mit dem [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) vor. Anschließend ändern Sie eines der Beispiele, um die verteilte Ablaufverfolgung für die Telemetriedaten des Geräts zu aktivieren.

Diese Anweisungen betreffen die Erstellung des Beispiels unter Windows. Informationen zu anderen Umgebungen finden Sie unter [Compile the C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compile) (Kompilieren des C SDK) oder [Prepackaged C SDK for Platform Specific Development](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#prepackaged-c-sdk-for-platform-specific-development) (Vorkonfiguriertes C SDK für plattformspezifische Entwicklung).

### <a name="clone-the-source-code-and-initialize"></a>Klonen des Quellcodes und Initialisieren

1. Installieren Sie die Workload [Desktop development with C++](/cpp/build/vscpp-step-0-installation?view=vs-2019) für Visual Studio 2019. Visual Studio 2017 und 2015 werden ebenfalls unterstützt.

1. Installieren Sie [CMake](https://cmake.org/). Stellen Sie sicher, dass das Tool sich in Ihrem `PATH` befindet, indem Sie an einer Eingabeaufforderung `cmake -version` eingeben.

1. Öffnen Sie eine Eingabeaufforderung oder die Git Bash-Shell. Führen Sie die folgenden Befehle zum Klonen des aktuellen Releases des GitHub-Repositorys [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) aus:

    ```cmd
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Sie sollten damit rechnen, dass die Ausführung dieses Vorgangs mehrere Minuten in Anspruch nimmt.

1. Erstellen Sie ein `cmake`-Unterverzeichnis im Stammverzeichnis des Git-Repositorys, und navigieren Sie zu diesem Ordner. Führen Sie die folgenden Befehle im Verzeichnis `azure-iot-sdk-c` aus:

    ```cmd
    mkdir cmake
    cd cmake
    cmake ..
    ```

    Wenn `cmake` Ihren C++-Compiler nicht finden kann, treten beim Ausführen des obigen Befehls unter Umständen Buildfehler auf. Führen Sie den Befehl in diesem Fall an der [Visual Studio-Eingabeaufforderung](/dotnet/framework/tools/developer-command-prompt-for-vs) aus. 

    Nach erfolgreicher Erstellung ähneln die letzten Ausgabezeilen der folgenden Ausgabe:

    ```cmd
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

### <a name="edit-the-send-telemetry-sample-to-enable-distributed-tracing"></a>Bearbeiten des Beispiels zum Senden von Telemetriedaten zur Aktivierung der verteilten Ablaufverfolgung

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/azure-iot-distributed-tracing-sample/blob/master/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c" target="_blank">Beispiel von GitHub abrufen</a>

1. Öffnen Sie die Quelldatei `azure-iot-sdk-c/iothub_client/samples/iothub_ll_telemetry_sample/iothub_ll_telemetry_sample.c` in einem Editor.

1. Suchen Sie die Deklaration der Konstanten `connectionString`:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=2)]

    Ersetzen Sie den Wert der Konstante `connectionString` durch die Verbindungszeichenfolge des Geräts, die Sie im Abschnitt [Registrieren eines Geräts](./quickstart-send-telemetry-c.md#register-a-device) des Schnellstarts zum [Senden von Telemetriedaten mit C](./quickstart-send-telemetry-c.md) notiert haben.

1. Ändern Sie `MESSAGE_COUNT` in `5000`:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=3)]

1. Suchen Sie die Codezeile, über die `IoTHubDeviceClient_LL_SetConnectionStatusCallback` aufgerufen wird, um eine Rückruffunktion des Verbindungsstatus vor der Schleife zum Senden von Nachrichten zu registrieren. Fügen Sie wie unten gezeigt Code unter dieser Zeile ein, um `IoTHubDeviceClient_LL_EnablePolicyConfiguration` aufzurufen, sodass die verteilte Ablaufverfolgung für das Gerät aktiviert wird:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_tracing&highlight=5)]

    Die Funktion `IoTHubDeviceClient_LL_EnablePolicyConfiguration` aktiviert Richtlinien für spezifische IoT Hub-Funktionen, die über [Gerätezwillinge](./iot-hub-devguide-device-twins.md) konfiguriert werden. Nachdem `POLICY_CONFIGURATION_DISTRIBUTED_TRACING` durch die obige Codezeile aktiviert wurde, werden bei der Überwachung des Geräts die am Gerätezwilling vorgenommenen Änderungen der verteilten Ablaufverfolgung angegeben.

1. Damit die Beispiel-App ausgeführt wird, ohne dass Ihr gesamtes Kontingent aufgebraucht wird, fügen Sie am Ende der Schleife zum Senden von Nachrichten eine Verzögerung von einer Sekunde hinzu:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_sleep&highlight=8)]

### <a name="compile-and-run"></a>Kompilieren und Ausführen

1. Navigieren Sie im zuvor erstellten CMake-Verzeichnis (`azure-iot-sdk-c/cmake`) zum Projektverzeichnis *iothub_ll_telemetry_sample*, und kompilieren Sie das Beispiel:

    ```cmd
    cd iothub_client/samples/iothub_ll_telemetry_sample
    cmake --build . --target iothub_ll_telemetry_sample --config Debug
    ```

1. Führen Sie die Anwendung aus. Das Gerät sendet Telemetriedaten, die die verteilte Ablaufverfolgung unterstützen.

    ```cmd
    Debug/iothub_ll_telemetry_sample.exe
    ```

1. Führen Sie die App weiter aus. Überprüfen Sie optional die an IoT Hub gesendete Nachricht im Konsolenfenster.

<!-- For a client app that can receive sampling decisions from the cloud, check out [this sample](https://aka.ms/iottracingCsample).  -->

### <a name="workaround-for-third-party-clients"></a>Problemumgehung für Clients von Drittanbietern

Es ist **nicht einfach**, eine Vorschau des Features zur verteilten Ablaufverfolgung anzuzeigen, ohne das C SDK zu verwenden. Daher wird dieser Ansatz nicht empfohlen.

Zunächst müssen Sie alle IoT-Hub-Protokollgrundtypen in Ihren Nachrichten implementieren, indem Sie der Anleitung für Entwickler [Erstellen und Lesen von IoT Hub-Nachrichten](iot-hub-devguide-messages-construct.md) folgen. Bearbeiten Sie dann die Protokolleigenschaften in den MQTT/AMQP-Nachrichten, um `tracestate` als **Systemeigenschaft** hinzuzufügen. Sie haben folgende Möglichkeiten:

* Fügen Sie für MQTT `%24.tracestate=timestamp%3d1539243209` zum Nachrichtenthema hinzu, wobei `1539243209` durch den Erstellungszeitpunkt der Nachricht im Unix-Zeitstempelformat ersetzt werden sollte. Ein Beispiel hierfür ist die Implementierung [im C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/iothubtransport_mqtt_common.c#L761).
* Für AMQP fügen Sie `key("tracestate")` und `value("timestamp=1539243209")` als Nachrichtenanmerkung hinzu. Eine Referenzimplementierung finden Sie [hier](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/uamqp_messaging.c#L527).

Um den Prozentsatz der Nachrichten, die diese Eigenschaft enthalten, zu steuern, implementieren Sie Logik zum Lauschen auf in der Cloud initiierte Ereignisse, z.B. Gerätezwillingsaktualisierungen.

## <a name="update-sampling-options"></a>Aktualisieren von Samplingoptionen 

Um den Prozentsatz der in der Cloud zu überwachenden Nachrichten zu ändern, müssen Sie den Gerätezwilling aktualisieren. Dazu stehen mehrere Möglichkeiten zur Verfügung, z.B. über den JSON-Editor im Portal oder mit dem IoT Hub Service SDK. Beispiele dafür finden Sie in den folgenden Abschnitten.

### <a name="update-using-the-portal"></a>Aktualisieren über das Portal

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrer IoT Hub-Instanz, und klicken Sie auf **IoT-Geräte**.

1. Klicken Sie auf Ihr Gerät.

1. Suchen Sie nach **Enable distributed tracing (preview)** (Verteilte Ablaufverfolgung aktivieren (Vorschau)), und wählen Sie dann **Aktivieren** aus.

    ![Aktivieren der verteilten Ablaufverfolgung im Azure-Portal](./media/iot-hub-distributed-tracing/azure-portal.png)

1. Wählen Sie eine **Samplingrate** zwischen 0 %und 100 % aus.

1. Klicken Sie auf **Speichern**.

1. Warten Sie einige Sekunden, und klicken Sie dann auf **Aktualisieren**. Wenn die Aktualisierung des Geräts erfolgreich durchgeführt wurde, wird ein Synchronisierungssymbol mit einem Häkchen angezeigt.

1. Wechseln Sie zum Konsolenfenster mit der Anwendung für die Telemetrienachrichten. Sie können sehen, dass Nachrichten mit `tracestate` in den Anwendungseigenschaften gesendet werden.

    ![Status der Ablaufverfolgung](./media/iot-hub-distributed-tracing/MicrosoftTeams-image.png)

1. (Optional:) Ändern Sie die Samplingrate in einen anderen Wert, und überprüfen Sie die Änderung an der Häufigkeit, mit der Nachrichten `tracestate` in den Anwendungseigenschaften enthalten.

### <a name="update-using-azure-iot-hub-for-vs-code"></a>Aktualisieren mit Azure IoT Hub für Visual Studio Code

1. Installieren Sie Visual Studio Code und dann die neueste Version von Azure IoT Hub für Visual Studio Code, die Sie [hier](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) herunterladen können.

1. Öffnen Sie Visual Studio Code, und [richten Sie die IoT Hub-Verbindungszeichenfolge ein](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit#user-content-prerequisites).

1. Erweitern Sie das Gerät, und suchen Sie nach **Distributed Tracing Setting (Preview)** (Einstellung für verteilte Ablaufverfolgung (Vorschau)). Klicken Sie darunter auf den Unterknoten **Update Distributed Tracing Setting (Preview)** (Einstellung für verteilte Ablaufverfolgung aktualisieren (Vorschau)).

    ![Aktivieren der verteilten Ablaufverfolgung in der Azure IoT Hub-Erweiterung](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-1.png)

1. Wählen Sie im Popupfenster die Option **Aktivieren** aus. Drücken Sie dann die EINGABETASTE, um 100 als Samplingrate zu bestätigen.

    ![Aktualisieren des Samplingmodus](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-2.png)

    ![Aktualisieren der Samplingrate](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-3.png)

### <a name="bulk-update-for-multiple-devices"></a>Massenaktualisierung für mehrere Geräte

Verwenden Sie zum Aktualisieren der Samplingkonfiguration der verteilten Ablaufverfolgung für mehrere Geräte die [automatische Gerätekonfiguration](./iot-hub-automatic-device-management.md). Befolgen Sie dazu das folgende Schema für Gerätezwillinge:

```json
{
    "properties": {
        "desired": {
            "azureiot*com^dtracing^1": {
                "sampling_mode": 1,
                "sampling_rate": 100
            }
        }
    }
}
```

| Elementname | Erforderlich | type | Beschreibung |
|-----------------|----------|---------|-----------------------------------------------------|
| `sampling_mode` | Ja | Integer | Derzeit werden zum Aktivieren und Deaktivieren des Samplings zwei Moduswerte unterstützt. `1` steht für „Aktiviert“ und `2` für „Deaktiviert“. |
| `sampling_rate` | Ja | Integer | Dieser Wert ist ein Prozentsatz. Es sind nur Werte von `0` bis (einschließlich) `100` zulässig.  |

## <a name="query-and-visualize"></a>Abfragen und Visualisieren

Um alle von einer IoT Hub-Instanz protokollierten Ablaufverfolgungen anzuzeigen, fragen Sie den Protokollspeicher ab, den Sie in den Diagnoseeinstellungen ausgewählt haben. In diesem Abschnitt werden einige verschiedene Möglichkeiten beschrieben.

### <a name="query-using-log-analytics"></a>Abfragen mithilfe von Log Analytics

Wenn Sie [Log Analytics mit Ressourcenprotokollen](../azure-monitor/platform/resource-logs.md#send-to-azure-storage) eingerichtet haben, führen Sie die Abfragen durch Suchen nach Protokollen in der Kategorie `DistributedTracing` durch. In der folgenden Abfrage sind beispielsweise alle protokollierten Ablaufverfolgungen aufgeführt:

```Kusto
// All distributed traces 
AzureDiagnostics 
| where Category == "DistributedTracing" 
| project TimeGenerated, Category, OperationName, Level, CorrelationId, DurationMs, properties_s 
| order by TimeGenerated asc  
```

Beispiel für in Log Analytics angezeigte Protokolle:

| TimeGenerated | Vorgangsname | Category | Ebene | CorrelationId | DurationMs | Eigenschaften |
|--------------------------|---------------|--------------------|---------------|---------------------------------------------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-02-22T03:28:28.633Z | DiagnosticIoTHubD2C | DistributedTracing | Informational | 00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01 |  | {"deviceId":"AZ3166","messageSize":"96","callerLocalTimeUtc":"2018-02-22T03:27:28.633Z","calleeLocalTimeUtc":"2018-02-22T03:27:28.687Z"} |
| 2018-02-22T03:28:38.633Z | DiagnosticIoTHubIngress | DistributedTracing | Informational | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 20 | {"isRoutingEnabled":"false","parentSpanId":"0144d2590aacd909"} |
| 2018-02-22T03:28:48.633Z | DiagnosticIoTHubEgress | DistributedTracing | Informational | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 23 | {"endpointType":"EventHub","endpointName":"myEventHub", "parentSpanId":"0144d2590aacd909"} |

Eine Beschreibung der unterschiedlichen Protokolltypen finden Sie unter [Azure IoT Hub-Protokolle für verteilte Ablaufverfolgung](monitor-iot-hub-reference.md#distributed-tracing-preview).

### <a name="application-map"></a>Anwendungszuordnung

Um den Fluss von IoT-Nachrichten zu visualisieren, richten Sie die Beispiel-App für die Anwendungsübersicht ein. Die Beispiel-App sendet die Protokolle der verteilten Ablaufverfolgung mithilfe einer Azure-Funktion und einem Event Hub an die [Anwendungsübersicht](../azure-monitor/app/app-map.md).

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/e2e-diagnostic-provision-cli" target="_blank">Beispiel von GitHub abrufen</a>

In dieser Abbildung ist die verteilte Ablaufverfolgung mit drei Routingendpunkten in der Anwendungsübersicht dargestellt:

![IoT: Verteilte Ablaufverfolgung in der Anwendungsübersicht](./media/iot-hub-distributed-tracing/app-map.png)

## <a name="understand-azure-iot-distributed-tracing"></a>Grundlegendes zur verteilten Ablaufverfolgung in Azure IoT

### <a name="context"></a>Kontext

Viele IoT-Lösungen, einschließlich unserer eigenen [Referenzarchitektur](/azure/architecture/reference-architectures/iot) (nur Englisch) folgen in der Regel einer Variante der [Microservicearchitektur](/azure/architecture/microservices/). Mit zunehmender Komplexität einer IoT-Lösung nutzen Sie schließlich ein Dutzend oder mehr Microservices. Diese Microservices können von Azure oder anderen Anbietern stammen. Die genaue Ermittlung, an welcher Stelle IoT-Nachrichten gelöscht oder langsamer übertragen werden, kann sich schwierig gestalten. Angenommen, Ihre IoT-Lösung nutzt 5 unterschiedliche Azure-Dienste und umfasst 1.500 aktive Geräte. Jedes Gerät sendet 10 Gerät-zu-Cloud-Nachrichten pro Sekunde (also insgesamt 15.000 Nachrichten/Sekunde), aber bei Ihrer Web-App kommen pro Sekunde nur 10.000 Nachrichten an. Wo liegt das Problem? Wie finden Sie den Verursacher?

### <a name="distributed-tracing-pattern-in-microservice-architecture"></a>Muster der verteilten Ablaufverfolgung in der Microservicearchitektur

Damit der Weg einer IoT-Nachricht in den verschiedenen Diensten nachvollzogen werden kann, muss in jedem Dienst eine *Korrelations-ID* vergeben werden, mit der die Nachricht eindeutig identifiziert wird. Nach der Erfassung in einem zentralen System können Sie anhand der Korrelations-IDs den Fluss der Nachricht anzeigen. Diese Methode wird als [Muster der verteilten Ablaufverfolgung](/azure/architecture/microservices/logging-monitoring#distributed-tracing) bezeichnet.

Zur Unterstützung einer größeren Akzeptanz der verteilten Ablaufverfolgung beteiligt sich Microsoft am [Vorschlag für den W3C-Standard für die verteilte Ablaufverfolgung](https://w3c.github.io/trace-context/).

### <a name="iot-hub-support"></a>IoT Hub-Unterstützung

Nach der Aktivierung erfolgt die Unterstützung der verteilten Ablaufverfolgung für IoT Hub nach diesem Muster:

1. Auf dem IoT-Gerät wird eine Nachricht generiert.
1. Das IoT-Gerät legt fest (mit Unterstützung der Cloud), dass dieser Nachricht ein Ablaufverfolgungskontext zugewiesen werden soll.
1. Das SDK fügt der Nachrichteneigenschaft ein `tracestate`-Element hinzu, das den Zeitstempel der Nachrichtenerstellung enthält.
1. Das IoT-Gerät sendet die Nachricht an IoT Hub.
1. Die Nachricht wird am IoT Hub-Gateway empfangen.
1. IoT Hub sucht in den Nachrichteneigenschaften nach `tracestate` und überprüft, ob das richtige Format vorliegt.
1. In diesem Fall generiert IoT Hub eine global eindeutige `trace-id` für die Nachricht sowie eine `span-id` für den „Hop“ und protokolliert beide in [IoT Hub-Protokollen für verteilte Ablaufverfolgung](monitor-iot-hub-reference.md#distributed-tracing-preview) unter dem Vorgang `DiagnosticIoTHubD2C`.
1. Nach Abschluss der Nachrichtenverarbeitung generiert IoT Hub eine andere `span-id` und protokolliert sie zusammen mit der vorhandenen `trace-id` unter dem Vorgang `DiagnosticIoTHubIngress`.
1. Wenn für die Nachricht das Routing aktiviert ist, schreibt IoT Hub die Nachricht in den benutzerdefinierten Endpunkt und protokolliert ein weiteres `span-id`-Element mit dem gleichen `trace-id`-Element unter der Kategorie `DiagnosticIoTHubEgress`.
1. Diese Schritte werden für jede generierte Nachricht wiederholt.

## <a name="public-preview-limits-and-considerations"></a>Einschränkungen und Anmerkungen zur Public Preview

- Der Vorschlag für den W3C-Standard zur Ablaufverfolgungskontext ist aktuell ein Arbeitsentwurf.
- Derzeit wird im Client-SDK nur die Entwicklungssprache C unterstützt.
- Die Funktion für Cloud-zu-Gerät-Zwillinge ist für den [Basic-Tarif von IoT Hub](iot-hub-scaling.md#basic-and-standard-tiers) nicht verfügbar. Dennoch führt IoT Hub die Protokollierung in Azure Monitor durch, wenn ordnungsgemäß erstellte Kontextheader für die Ablaufverfolgung erkannt werden.
- Um einen effizienten Betrieb sicherzustellen, wird in IoT Hub eine Drosselung für die Protokollierungsrate festgelegt, die als Teil der verteilten Ablaufverfolgung erfolgen kann.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum allgemeinen Muster der verteilten Ablaufverfolgung in Microservices finden Sie unter [Microservice architecture pattern: distributed tracing](https://microservices.io/patterns/observability/distributed-tracing.html) (Microservicearchitekturmuster: Verteilte Ablaufverfolgung).
- Informationen zum Einrichten der Konfiguration zur Anwendung von Einstellungen der verteilten Ablaufverfolgung auf eine große Anzahl von Geräten finden Sie unter [Bedarfsgerechtes Konfigurieren und Überwachen von IoT-Geräten](./iot-hub-automatic-device-management.md).
- Weitere Informationen zu Azure Monitor finden Sie unter [Was ist Azure Monitor?](../azure-monitor/overview.md).
- Weitere Informationen zur Verwendung von Azure Monitor bei IoT Hub finden Sie unter [Überwachen von IoT Hub](monitor-iot-hub.md).