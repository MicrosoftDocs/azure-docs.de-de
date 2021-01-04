---
title: Überwachen und Behandeln von Problemen bei der Trennung von Geräteverbindungen mit Azure IoT Hub
description: Hier erfahren Sie, wie Sie häufige Fehler bei der Gerätekonnektivität für Azure IoT Hub überwachen und behandeln.
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: jlian
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
- fasttrack-edit
- iot
ms.openlocfilehash: 81558526500f3c01e975d9a963b4a6a98ec6d753
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446408"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Überwachen, Diagnostizieren und Behandeln von Problemen bei der Trennung von Geräteverbindungen mit Azure IoT Hub

Verbindungsprobleme bei IoT-Geräten können schwierig zu behandeln sein, weil es viele mögliche Fehlerquellen gibt. Anwendungslogik, physische Netzwerke, Protokolle, Hardware, IoT Hub und andere Clouddienste können Probleme verursachen. Die Möglichkeit zum Erkennen und Ermitteln der Ursache eines Problems ist wichtig. Weil aber bei einer IoT-Lösung im großen Stil Tausende von Geräten betroffen sein könnten, ist es nicht praktikabel, einzelne Geräte manuell zu überprüfen. IoT Hub wird in zwei Azure-Dienste integriert, um Sie bei Folgendem zu unterstützen:

* **Azure Monitor** Nutzen Sie die Überwachungsfunktionen, die IoT Hub über Azure Monitor bereitstellt, als Hilfe beim Erkennen, Diagnostizieren und Beheben dieser Probleme im großen Stil. Dies umfasst das Einrichten von Warnungen zum Auslösen von Benachrichtigungen und Aktionen, wenn Geräteverbindungen getrennt werden, sowie das Konfigurieren von Protokollen, mit denen Sie die Bedingungen ermitteln können, die zu solchen Trennungen geführt haben.

* **Azure Event Grid** Bei einer kritischen Infrastruktur und Verbindungstrennungen pro Gerät verwenden Sie Azure Event Grid zum Abonnieren der von IoT Hub ausgegebenen Ereignisse der Geräteverbindung und -trennung.

Weil diese Funktionen in beiden Fällen auf das, was IoT Hub beobachten kann, begrenzt sind, empfehlen wir außerdem, dass Sie die bewährten Methoden für die Überwachung Ihrer Geräte und anderer Azure-Dienste befolgen.

## <a name="event-grid-vs-azure-monitor"></a>Event Grid im Vergleich zu Azure Monitor

Event Grid bietet eine Überwachungslösung mit geringer Wartezeit pro Gerät, die Sie zum Nachverfolgen von Geräteverbindungen bei kritischen Geräten und einer kritischen Infrastruktur verwenden können. Azure Monitor bietet eine Metrik, *Verbundene Geräte*, mit deren Hilfe Sie die Anzahl der mit Ihrem IoT-Hub verbundenen Geräte überwachen und eine Warnung auslösen können, wenn diese Anzahl einen statischen Schwellenwert unterschreitet.

Berücksichtigen Sie Folgendes bei Ihrer Entscheidung, ob Sie für ein bestimmtes Szenario Event Grid oder Azure Monitor verwenden möchten:

* Warnungswartezeit: IoT Hub-Verbindungsereignisse werden durch Event Grid wesentlich schneller übermittelt. Dadurch wird Event Grid eine bessere Wahl für Szenarien, in denen eine schnelle Benachrichtigung wünschenswert ist.

* Benachrichtigungen pro Gerät: Event Grid bietet die Möglichkeit zum Nachverfolgen von Verbindungsherstellungen und Verbindungstrennungen bei einzelnen Geräten. Dadurch wird Event Grid zu einer besseren Wahl für Szenarien, in denen Sie die Verbindungen bei kritischen Geräten überwachen müssen.

* Einfaches Setup: Azure Monitor-Metrikwarnungen ermöglichen ein einfaches Setup, bei dem keine Integration in andere Dienste erforderlich ist, um Benachrichtigungen über E-Mail, SMS, Voicemail und andere Benachrichtigungen zu übermitteln.  Event Grid müssen Sie in andere Azure-Dienste integrieren, um Benachrichtigungen zu übermitteln. Beide Dienste können in andere Dienste integriert werden, um komplexere Aktionen auszulösen.

Aufgrund seiner Funktionen mit geringer Wartezeit pro Gerät empfehlen wir für Produktionsumgebungen dringend, Event Grid zum Überwachen von Verbindungen zu verwenden. Diese Auswahl ist natürlich nicht exklusiv. Sie können sowohl Azure Monitor-Metrikwarnungen als auch Event Grid verwenden. Unabhängig von Ihrer Auswahl für die Nachverfolgung von Verbindungstrennungen werden Sie die Ursachen für unerwartete Gerätetrennungen wahrscheinlich mithilfe von Azure Monitor-Ressourcenprotokollen beheben. In den folgenden Abschnitten wird jede dieser Optionen ausführlicher erläutert.

## <a name="event-grid-monitor-device-connect-and-disconnect-events"></a>Event Grid: Überwachen von Ereignissen der Herstellung und Trennung von Geräteverbindungen

Zur Überwachung von Ereignissen der Herstellung und Trennung von Geräteverbindungen in der Produktion empfiehlt es sich, die [Ereignisse **DeviceConnected** und **DeviceDisconnected**](iot-hub-event-grid.md#event-types) in Event Grid zu abonnieren, um Warnungen auszulösen und den Geräteverbindungsstatus zu überwachen. Event Grid bietet eine viel geringere Ereignislatenz als Azure Monitor, und Sie können die Überwachung pro Gerät statt für die Gesamtanzahl verbundener Geräte durchführen. Durch diese Faktoren wird Event Grid zur bevorzugten Methode für die Überwachung kritischer Geräte und einer kritischen Infrastruktur.

Wenn Sie Warnungen zur Trennung von Geräteverbindungen mithilfe von Event Grid überwachen oder auslösen, müssen Sie die regelmäßigen Trennungen aufgrund von SAS-Tokenverlängerung auf Geräten herausfiltern, die die Azure IoT-SDKs verwenden. Weitere Informationen finden Sie unter [MQTT device disconnect behavior with Azure IoT SDKs](#mqtt-device-disconnect-behavior-with-azure-iot-sdks) (Verbindungstrennungsverhalten von MQTT-Geräten bei Azure IoT-SDKs).

Weitere Informationen zum Überwachen von Geräteverbindungsereignissen mit Event Grid finden Sie in den folgenden Themen:

* Eine Übersicht über die Verwendung von Event Grid bei IoT Hub finden Sie unter [Reagieren auf IoT Hub-Ereignisse mit Event Grid](iot-hub-event-grid.md). Beachten Sie insbesondere den Abschnitt [Beschränkungen bei den Ereignissen „Gerät verbunden“ und „Gerät getrennt“](iot-hub-event-grid.md#limitations-for-device-connected-and-device-disconnected-events).

* Ein Tutorial zum Sortieren von Geräteverbindungsereignissen finden Sie unter [Sortieren von Geräteverbindungsereignissen von Azure IoT Hub mithilfe von Azure Cosmos DB](iot-hub-how-to-order-connection-state-events.md).

* Ein Tutorial zum Senden von E-Mail-Benachrichtigungen finden Sie in der Event Grid-Dokumentation unter [Senden von E-Mail-Benachrichtigungen zu Azure IoT Hub-Ereignissen mit Event Grid und Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md).

## <a name="azure-monitor-route-connection-events-to-logs"></a>Azure Monitor: Weiterleiten von Verbindungsereignissen an Protokolle

IoT Hub gibt kontinuierlich Ressourcenprotokolle für mehrere Vorgangskategorien aus. Allerdings müssen Sie zum Erfassen dieser Protokolldaten eine Diagnoseeinstellung erstellen, um die Daten an ein Ziel weiterzuleiten, an dem sie analysiert oder archiviert werden können. Ein solches Ziel ist Azure Monitor-Protokolle über einen Log Analytics-Arbeitsbereich ([siehe Preise](https://azure.microsoft.com/pricing/details/log-analytics/)), in dem Sie die Daten mithilfe von Kusto-Abfragen analysieren können.

Die IoT Hub-Kategorie [Ressourcenprotokollverbindungen](monitor-iot-hub-reference.md#connections) gibt Vorgänge und Fehler im Zusammenhang mit Geräteverbindungen aus. Der folgende Screenshot zeigt eine Diagnoseeinstellung zum Weiterleiten dieser Protokolle an einen Log Analytics-Arbeitsbereich:

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/create-diagnostic-setting.png" alt-text="Empfohlene Einstellung zum Senden von Konnektivitätsprotokollen an Log Analytics-Arbeitsbereich.":::

Wir empfehlen, dass Sie eine Diagnoseeinstellung so früh wie möglich nach dem Erstellen Ihres IoT-Hubs erstellen. Der Grund: Obwohl IoT Hub immer Ressourcenprotokolle ausgibt, werden diese von Azure Monitor erst gesammelt, wenn Sie sie an ein Ziel weiterleiten.

Weitere Informationen zum Weiterleiten von Protokollen an ein Ziel finden Sie unter [Sammlung und Routing](monitor-iot-hub.md#collection-and-routing). Ausführliche Anleitungen zum Erstellen einer Diagnoseeinstellung finden Sie im [Tutorial „Verwenden von Metriken und Protokollen“](tutorial-use-metrics-and-diags.md).

## <a name="azure-monitor-set-up-metric-alerts-for-device-disconnect-at-scale"></a>Azure Monitor: Einrichten von Metrikwarnungen im großen Stil bei einer Trennung der Geräteverbindung

Sie können Warnungen basierend auf den Plattformmetriken einrichten, die von IoT Hub ausgegeben werden. Mit Metrikwarnungen können Sie Personen benachrichtigen, dass eine relevante Bedingung aufgetreten ist, und außerdem Aktionen auslösen, die auf diese Bedingung automatisch reagieren können.

Die Metrik [*Verbundene Geräte (Vorschau)*](monitor-iot-hub-reference.md#device-metrics) informiert Sie, wie viele Geräte mit Ihrem IoT-Hub verbunden sind. Sie können Warnungen erstellen, die ausgelöst werden sollen, wenn diese Metrik einen Schwellenwert unterschreitet:

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/configure-alert-logic.png" alt-text="Einstellungen für die Warnungslogik bei der Metrik „Verbundene Geräte“.":::

Sie können Anomalien bei der Trennung von Geräteverbindungen mithilfe von Metrikwarnungsregeln im großen Stil überwachen. Das heißt, wenn eine große Anzahl von Geräten unerwartet getrennt wird. Wird ein solches Vorkommen erkannt, können Sie Protokolle als Hilfe bei der Behebung dieses Problems anzeigen. Zum Überwachen von Trennungen pro Gerät und Trennungen bei kritischen Geräten müssen Sie jedoch Event Grid verwenden. Event Grid bietet auch eine bessere Echtzeitdarstellung als Azure-Metriken.

Weitere Informationen zu Warnungen bei IoT Hub finden Sie unter [Warnungen in Monitor IoT Hub](monitor-iot-hub.md#alerts). Eine exemplarische Vorgehensweise zum Erstellen von Warnungen in IoT Hub finden Sie im [Tutorial „Verwenden von Metriken und Protokollen“](tutorial-use-metrics-and-diags.md). Eine ausführlichere Übersicht über Warnungen finden Sie in der Azure Monitor-Dokumentation unter [Überblick über Warnungen in Microsoft Azure](../azure-monitor/platform/alerts-overview.md).

## <a name="azure-monitor-use-logs-to-resolve-connectivity-errors"></a>Azure Monitor: Verwenden von Protokollen zum Beheben von Verbindungsfehlern

Wenn Sie Trennungen von Geräteverbindungen erkennen, – unabhängig davon, ob dies mit Azure Monitor-Metrikwarnungen oder mit Event Grid geschieht – können Sie die Ursache anhand von Protokollen beheben. In diesem Abschnitt wird beschrieben, wie Sie in Azure Monitor-Protokollen nach häufigen Problemen suchen. In den folgenden Schritten wird davon ausgegangen, dass Sie bereits eine [Diagnoseeinstellung](#azure-monitor-route-connection-events-to-logs) erstellt haben, um IoT Hub-Verbindungsprotokolle an einen Log Analytics Arbeitsbereich zu senden.

Nachdem Sie eine Diagnoseeinstellung zum Weiterleiten von IoT Hub-Ressourcenprotokollen an Azure Monitor-Protokolle erstellt haben, führen Sie die folgenden Schritte aus, um die Protokolle im Azure-Portal anzuzeigen.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem IoT-Hub.

1. Wählen Sie im linken Bereich Ihres IoT-Hubs unter **Überwachung** die Option **Protokolle** aus.

1. Zum Isolieren von Verbindungsfehlerprotokollen für IoT Hub geben Sie im Abfrage-Editor die folgende Abfrage ein, und wählen Sie **Ausführen** aus:

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. Wenn Ergebnisse vorhanden sind, suchen Sie nach `OperationName`, `ResultType` (Fehlercode) und `ResultDescription` (Fehlermeldung), um weitere Details zu dem Fehler anzuzeigen.

   ![Beispiel für ein Fehlerprotokoll](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

Nachdem Sie den Fehler identifiziert haben, folgen Sie den Handbüchern zur Problemlösung, um Hilfe zu den häufigsten Fehlern zu erhalten:

* [400027 ConnectionForcefullyClosedOnNewConnection](iot-hub-troubleshoot-error-400027-connectionforcefullyclosedonnewconnection.md)

* [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)

* [401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)

* [409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)

* [500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

* [500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

## <a name="mqtt-device-disconnect-behavior-with-azure-iot-sdks"></a>Verbindungstrennungsverhalten von MQTT-Geräten bei Azure IoT-SDKs

Azure IoT-Geräte-SDKs trennen die Verbindung mit IoT Hub und stellen eine Verbindung erneut her, wenn sie SAS-Token über das MQTT-Protokoll (und MQTT über WebSockets) verlängern. In Protokollen wird dies als Informationsereignisse der Trennung und Herstellung von Geräteverbindungen angezeigt, die manchmal von Fehlerereignissen begleitet werden.

Die Tokenlebensdauer bei allen SDKs beträgt standardmäßig 60 Minuten; in einigen der SDKs kann sie jedoch von Entwicklern geändert werden. In der folgenden Tabelle werden die Tokenlebensdauer, die Tokenverlängerung und das Tokenverlängerungsverhalten für die einzelnen SDKs zusammengefasst:

| SDK | Tokenlebensdauer | Tokenverlängerung | Verlängerungsverhalten |
|-----|----------|---------------------|---------|
| .NET | 60 Minuten, konfigurierbar | 85 % der Lebensdauer, konfigurierbar | Das SDK stellt die Verbindung her und trennt sie während der Tokenlebensdauer, zuzüglich einer Karenzzeit von 10 Minuten. Informationsereignisse und Fehler, die in Protokollen generiert werden. |
| Java | 60 Minuten, konfigurierbar | 85 % der Lebensdauer, nicht konfigurierbar | Das SDK stellt die Verbindung her und trennt sie während der Tokenlebensdauer, zuzüglich einer Karenzzeit von 10 Minuten. Informationsereignisse und Fehler, die in Protokollen generiert werden. |
| Node.js | 60 Minuten, konfigurierbar | konfigurierbar | Das SDK stellt die Verbindung her und trennt sie bei der Tokenverlängerung. In Protokollen werden nur Informationsereignisse generiert. |
| Python | 60 Minuten, nicht konfigurierbar | -- | Das SDK stellt die Verbindung her und trennt sie während der Tokenlebensdauer. |

Die folgenden Screenshots zeigen das Tokenverlängerungsverhalten in Azure Monitor-Protokollen für verschiedene SDKs. Wie oben erwähnt wurden die Tokenlebensdauer und der Verlängerungsschwellenwert gegenüber ihren Standardeinstellungen geändert.

* Beim .NET-Geräte-SDK mit einer Tokenlebensdauer und Tokenverlängerung von 1.200 Sekunden (20 Minuten) wurde dies auf 90 % der Lebensdauer festgelegt. Die Verbindung wird alle 30 Minuten getrennt:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/net-mqtt.png" alt-text="Fehlerverhalten bei der Tokenverlängerung über MQTT in Azure Monitor-Protokollen mit dem .NET SDK.":::

* Java-SDK mit einer Tokenlebensdauer von 300 Sekunden (5 Minuten) und einem Standardwert von 85 % der Lebensdauerverlängerung. Die Verbindung wird alle 15 Minuten getrennt:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/java-mqtt.png" alt-text="Fehlerverhalten bei der Tokenverlängerung über MQTT in Azure Monitor-Protokollen mit dem Java SDK.":::

* Beim Node SDK mit einer Tokenlebensdauer und Tokenverlängerung von 300 Sekunden (5 Minuten) wurde dies auf 3 Minuten festgelegt. Die Verbindungstrennung erfolgt bei der Tokenverlängerung. Außerdem gibt es keine Fehler, sondern es werden nur Informationsereignisse der Verbindungsherstellung/Verbindungstrennung ausgegeben:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/node-mqtt.png" alt-text="Fehlerverhalten bei der Tokenverlängerung über MQTT in Azure Monitor-Protokollen mit dem Node SDK.":::

Mit der folgenden Abfrage wurden die Ergebnisse erfasst. Die Abfrage extrahiert den SDK-Namen und die Version aus der Eigenschaftensammlung. Weitere Informationen finden Sie unter [SDK-Version in IoT Hub-Protokollen](monitor-iot-hub.md#sdk-version-in-iot-hub-logs).

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s)
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId) , Protocol =  tostring(parsed_json.protocol)
| distinct TimeGenerated, OperationName, Level, ResultType, ResultDescription, DeviceId, Protocol, SDKVersion

```

Als Entwickler von IoT-Lösungen oder Operator müssen Sie dieses Verhalten beachten, um Ereignisse von Verbindungsherstellung/Verbindungstrennung und zugehörige Fehler in Protokollen zu interpretieren. Wenn Sie die Tokenlebensdauer oder das Verlängerungsverhalten für Geräte ändern möchten, überprüfen Sie, ob das Gerät eine Gerätezwillingseinstellung oder eine Gerätemethode implementiert, die dies ermöglicht.

Wenn Sie Geräteverbindungen mit Event Hub überwachen, müssen Sie die regelmäßigen Trennungen infolge der SAS-Tokenverlängerung herausfiltern, indem beispielsweise keine Aktionen auf der Grundlage von Trennungen ausgelöst werden, solange auf das Ereignis der Verbindungstrennung innerhalb einer bestimmten Zeitspanne ein Ereignis der Verbindungsherstellung folgt.

> [!NOTE]
> IoT Hub unterstützt nur eine aktive MQTT-Verbindung pro Gerät. Jede neue MQTT-Verbindung für die gleiche Geräte-ID bewirkt, dass IoT Hub die vorhandene Verbindung löscht.
>
> „400027 ConnectionForcefullyClosedOnNewConnection“ wird in IoT Hub-Protokollen protokolliert

## <a name="i-tried-the-steps-but-they-didnt-work"></a>Ich habe die Schritte ausprobiert, aber sie funktionieren nicht.

Wenn das Problem mit den vorstehenden Schritten nicht behoben werden konnte, versuchen Sie Folgendes:

* Wenn Sie Zugriff auf die problematischen Geräte haben, entweder physisch oder remote (wie SSH), folgen Sie der [Anleitung zur geräteseitigen Problembehandlung](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices), um die Fehlerbehebung fortzusetzen.

* Überprüfen Sie, ob Ihre Geräte im Azure-Portal > IoT Hub > IoT-Geräte **aktiviert** sind.

* Wenn Ihr Gerät das MQTT-Protokoll verwendet, vergewissern Sie sich, dass Port 8883 geöffnet ist. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

* Hilfe können Sie über [die Frageseite von Microsoft Q&A (Fragen und Antworten) zu Azure IoT Hub](/answers/topics/azure-iot-hub.html), [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub) oder [Azure-Support](https://azure.microsoft.com/support/options/) erhalten.

Um die Dokumentation allgemein zu verbessern, schreiben Sie im Feedbackabschnitt einen Kommentar, wenn Ihnen diese Anleitung bei der Problembehandlung nicht geholfen hat.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Beheben von vorübergehenden Problemen finden Sie unter [Behandlung vorübergehender Fehler](/azure/architecture/best-practices/transient-faults).

* Mehr über das Azure IoT-SDK und den Umgang mit Wiederholungen erfahren Sie unter [Verwalten von Konnektivität und zuverlässigem Messaging mithilfe von Azure IoT Hub-Geräte-SDKs](iot-hub-reliability-features-in-sdks.md#connection-and-retry).