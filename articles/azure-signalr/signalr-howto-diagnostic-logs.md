---
title: Ressourcenprotokolle für Azure SignalR Service
description: Erfahren Sie, wie Sie Ressourcenprotokolle für Azure SignalR Service einrichten und diese für die eigenständige Problembehandlung nutzen.
author: wanlwanl
ms.service: signalr
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: wanl
ms.openlocfilehash: 5ad40ca051677ced0c6d8b5c35e8563272ff598f
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183973"
---
# <a name="resource-logs-for-azure-signalr-service"></a>Ressourcenprotokolle für Azure SignalR Service

In diesem Tutorial wird besprochen, was Ressourcenprotokolle für den Azure SignalR Service sind, wie sie eingerichtet und wie Probleme mit ihnen behoben werden. 

## <a name="prerequisites"></a>Voraussetzungen
Zum Aktivieren der Ressourcenprotokolle benötigen Sie einen Ort zum Speichern Ihrer Protokolldaten. In diesem Tutorial werden Azure Storage und Log Analytics verwendet.

* [Azure Storage](../azure-monitor/platform/resource-logs.md#send-to-azure-storage) speichert die Ressourcenprotokolle für die Richtlinienüberwachung, statische Analysen oder als Sicherung.
* [Log Analytics](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace) ist ein flexibles Tool für die Protokollsuche und -analyse, mit dem Sie die unformatierten Protokolle von einer Azure-Ressource analysieren können.

## <a name="set-up-resource-logs-for-an-azure-signalr-service"></a>Einrichten von Ressourcenprotokollen für einen Azure SignalR Service

Sie können Ressourcenprotokolle für Azure SignalR Service anzeigen. Diese Protokolle bieten einen umfassenderen Überblick über die Konnektivität mit Ihrer Azure SignalR Service-Instanz. Die Ressourcenprotokolle bieten detaillierte Informationen zu jeder Verbindung. Dies sind z. B. grundlegende Informationen (Benutzer-ID, Verbindungs-ID, Transporttyp usw.) und Ereignisinformationen (Verbinden-, Trennen-, Abbrechen-Ereignis usw.) zu der Verbindung. Ressourcenprotokolle können zur Identifizierung von Problemen sowie zur Verbindungsverfolgung und -analyse verwendet werden.

### <a name="enable-resource-logs"></a>Aktivieren von Ressourcenprotokollen

Ressourcenprotokolle sind standardmäßig deaktiviert. Führen Sie folgende Schritte aus, um die Ressourcenprotokolle zu aktivieren:

1. Klicken Sie im [Azure-Portal](https://portal.azure.com) unter **Überwachung** auf **Diagnoseeinstellungen**.

    ![Bereich „Navigation zu Diagnoseeinstellungen“](./media/signalr-tutorial-diagnostic-logs/diagnostic-settings-menu-item.png)

1. Klicken Sie dann auf **Diagnoseeinstellung hinzufügen**.

    ![Hinzufügen von Ressourcenprotokollen](./media/signalr-tutorial-diagnostic-logs/add-diagnostic-setting.png)

1. Legen Sie das gewünschte Archivierungsziel fest. Zurzeit unterstützen wir **In einem Speicherkonto archivieren** und **An Log Analytics senden**.

1. Wählen Sie die Protokolle aus, die Sie archivieren möchten.

    ![Bereich „Diagnoseeinstellungen“](./media/signalr-tutorial-diagnostic-logs/diagnostics-settings-pane.png)


1. Speichern Sie die neuen Diagnoseeinstellungen.

Neue Einstellungen werden in etwa zehn Minuten wirksam. Danach werden die Protokolle im gewünschten Archivierungsziel im Bereich **Diagnoseprotokolle** angezeigt.

Weitere Informationen zum Konfigurieren der Diagnose finden Sie in der [Übersicht über Azure-Ressourcenprotokolle](../azure-monitor/platform/platform-logs-overview.md).

### <a name="resource-logs-categories"></a>Ressourcenprotokollkategorien

Azure SignalR Service erfasst Ressourcenprotokolle in einer Kategorie:

* **Alle Protokolle**: Nachverfolgen von Verbindungen mit Azure SignalR Service. Die Protokolle enthalten Informationen zum Verbinden/Trennen, zur Authentifizierung und zur Drosselung. Weitere Informationen finden Sie im nächsten Abschnitt.

### <a name="archive-to-a-storage-account"></a>In einem Speicherkonto archivieren

Die Protokolle werden in dem Speicherkonto gespeichert, das im Bereich **Diagnoseprotokolle** konfiguriert ist. Ein Container mit dem Namen `insights-logs-alllogs` wird automatisch erstellt, um Ressourcenprotokolle zu speichern. In dem Container werden Protokolle in der Datei `resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX/y=YYYY/m=MM/d=DD/h=HH/m=00/PT1H.json` gespeichert. Im Grunde stellt der Pfad eine Kombination aus `resource ID` und `Date Time` dar. Die Protokolldateien werden anhand der `hour` aufgeteilt. Daher sind die Minuten immer `m=00`.

Alle Protokolle werden im JavaScript Object Notation (JSON)-Format gespeichert. Jeder Eintrag enthält Zeichenfolgenfelder im nachfolgend beschriebenen Format.

JSON-Zeichenfolgen im Archivprotokoll enthalten Elemente, die in den folgenden Tabellen aufgeführt sind:

**Format**

Name | BESCHREIBUNG
------- | -------
time | Protokollereigniszeit
level | Protokollereignisebene
resourceId | Ressourcen-ID Ihres Azure SignalR Service.
location | Standort Ihres Azure SignalR Service.
category | Kategorie des Protokollereignisses.
operationName | Vorgangsname des Ereignisses.
callerIpAddress | IP-Adresse Ihres Servers/Clients.
properties | Detaillierte Eigenschaften im Zusammenhang mit diesem Protokollereignis. Weitere Details finden Sie in der unten stehenden Tabelle.

**Eigenschaftentabelle**

Name | BESCHREIBUNG
------- | -------
type | Typ des Protokollereignisses. Aktuell bieten wir Informationen zur Konnektivität mit dem Azure SignalR Service. Nur der Typ `ConnectivityLogs` ist verfügbar.
collection | Sammlung des Protokollereignisses. Zulässige Werte sind `Connection`, `Authorization` und `Throttling`.
connectionId | Identität der Verbindung.
transportType | Transporttyp der Verbindung. Zulässige Werte sind: `Websockets` \| `ServerSentEvents` \| `LongPolling`.
connectionType | Typ der Verbindung. Zulässige Werte sind: `Server` \| `Client`. `Server`: serverseitige Verbindung; `Client`: clientseitige Verbindung
userId | Identität des Benutzers.
message | Ausführliche Meldung des Protokollereignisses.

Es folgt ein Codebeispiel für eine JSON-Zeichenfolge im Archivierungsprotokoll:

```json
{
    "properties": {
        "message": "Entered Serverless mode.",
        "type": "ConnectivityLogs",
        "collection": "Connection",
        "connectionId": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "userId": "User",
        "transportType": "WebSockets",
        "connectionType": "Client"
    },
    "operationName": "ServerlessModeEntered",
    "category": "AllLogs",
    "level": "Informational",
    "callerIpAddress": "xxx.xxx.xxx.xxx",
    "time": "2019-01-01T00:00:00Z",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX",
    "location": "xxxx"
}
```

### <a name="archive-logs-schema-for-log-analytics"></a>Archivprotokollschema für Log Analytics

Führen Sie folgende Schritte aus, um die Ressourcenprotokolle anzuzeigen:

1. Klicken Sie in Ihrem Ziel-Log Analytics auf `Logs`.

    ![Log Analytics-Menüelement](./media/signalr-tutorial-diagnostic-logs/log-analytics-menu-item.png)

2. Geben Sie `SignalRServiceDiagnosticLogs` ein, und wählen Sie den Zeitraum für die Abfrage der Ressourcenprotokolle aus. Informationen zu komplexeren Abfragen finden Sie unter [Erste Schritte mit Log Analytics in Azure Monitor](../azure-monitor/log-query/log-analytics-tutorial.md).

    ![Abfragen des Protokolls in Log Analytics](./media/signalr-tutorial-diagnostic-logs/query-log-in-log-analytics.png)

Archivprotokollspalten enthalten Elemente, die in der folgenden Tabelle aufgeführt sind:

Name | BESCHREIBUNG
------- | ------- 
TimeGenerated | Protokollereigniszeit
Collection | Sammlung des Protokollereignisses. Zulässige Werte sind `Connection`, `Authorization` und `Throttling`.
Vorgangsname | Vorgangsname des Ereignisses.
Standort | Standort Ihres Azure SignalR Service.
Ebene | Protokollereignisebene
CallerIpAddress | IP-Adresse Ihres Servers/Clients.
`Message` | Ausführliche Meldung des Protokollereignisses.
UserId | Identität des Benutzers.
ConnectionId | Identität der Verbindung.
ConnectionType | Typ der Verbindung. Zulässige Werte sind: `Server` \| `Client`. `Server`: serverseitige Verbindung; `Client`: clientseitige Verbindung
TransportType | Transporttyp der Verbindung. Zulässige Werte sind: `Websockets` \| `ServerSentEvents` \| `LongPolling`.

### <a name="troubleshooting-with-resource-logs"></a>Problembehandlung mit Ressourcenprotokollen

Für die Problembehandlung des Azure SignalR Service können Sie server-/clientseitige Protokolle aktivieren, um Fehler zu erfassen. Derzeit stellt der Azure SignalR Service Ressourcenprotokolle bereit, Sie können Protokolle aber auch dienstseitig aktivieren.

Wenn die Auslastung der Verbindung unerwartet zunimmt oder sie unterbrochen wird, können Sie die Ressourcenprotokolle für die Problembehandlung nutzen.

Typische Probleme sind häufig unerwartete Auslastungsveränderungen bei Verbindungen, das Erreichen von Verbindungslimits bei Verbindungen sowie Autorisierungsfehler. Informationen zur Vorgehensweise bei der Problembehandlung finden Sie in den nächsten Abschnitten.

#### <a name="unexpected-connection-number-changes"></a>Unerwartete Änderungen der Verbindungsanzahl

##### <a name="unexpected-connection-dropping"></a>Unerwartete Verbindungsunterbrechungen

Wenn bei Ihnen unerwartete Verbindungsunterbrechungen auftreten, aktivieren Sie zuerst die Protokolle auf Dienst-, Server- und Clientseite.

Wird eine Verbindung getrennt, zeichnet das Ressourcenprotokoll dieses Trennungsereignis auf, und Sie sehen `ConnectionAborted` oder `ConnectionEnded` in `operationName`.

Der Unterschied zwischen `ConnectionAborted` und `ConnectionEnded` besteht darin, dass `ConnectionEnded` eine erwartete Verbindungstrennung ist, die vom Client oder Server ausgelöst wird. Während es sich bei `ConnectionAborted` in der Regel um ein unerwartetes Verbindungsunterbrechungsereignis handelt, und der Grund für den Abbruch wird in `message` bereitgestellt.

Die Gründe für Abbrüche sind in der folgenden Tabelle aufgeführt:

`Reason` | BESCHREIBUNG
------- | ------- 
Anzahl der Verbindungen erreicht das Limit. | Die Anzahl der Verbindungen erreicht das Limit Ihres aktuellen Tarifs. Erwägen Sie, die Diensteinheit hochzuskalieren.
Der Anwendungsserver hat die Verbindung geschlossen. | Der App-Server löst den Abbruch aus. Dies kann als erwarteter Abbruch betrachtet werden.
Timeout des Verbindungspings. | Normalerweise wird dies durch Netzwerkprobleme verursacht. Erwägen Sie, die Verfügbarkeit Ihres App-Servers aus dem Internet zu überprüfen.
Erneutes Laden des Diensts, Wiederherstellen der Verbindung. | Azure SignalR Service wird neu geladen. Azure SignalR unterstützt automatisches erneutes Verbinden. Sie können bis zur erneuten Verbindungsherstellung warten oder die Verbindung mit Azure SignalR Service manuell erneut herstellen.
Vorübergehender interner Serverfehler. | Ein vorübergehender Fehler tritt in Azure SignalR Service auf; sollte automatisch wiederhergestellt werden.
Serververbindung unterbrochen. | Die Serververbindung wird mit unbekanntem Fehler unterbrochen. Erwägen Sie, die Problembehandlung zuerst selbst mithilfe der Dienst-/Server-/Clientprotokolle durchzuführen. Versuchen Sie, grundlegende Probleme auszuschließen (z. B. Netzwerkprobleme, Probleme auf App-Serverseite usw.). Wenn sich das Problem nicht beheben lässt, wenden Sie sich wegen weiterer Hilfe an uns. Weitere Informationen finden Sie im Abschnitt [Hilfe erhalten](#get-help). 

##### <a name="unexpected-connection-growing"></a>Unerwarteter Anstieg der Verbindungsauslastung.

Um unerwartete Anstiege der Verbindungsauslastung zu behandeln, müssen Sie zuerst die zusätzlichen Verbindungen ausfiltern. Sie können Ihrer Testclientverbindung eine eindeutige Testbenutzer-ID hinzufügen. Verifizieren Sie diese dann in den Ressourcenprotokollen. Wenn Sie dort mehr als eine Clientverbindung mit derselben Testbenutzer-ID oder IP-Adresse vorfinden, ist es wahrscheinlich, dass der Client mehr Verbindungen erstellt und unterhält als erwartet. Überprüfen Sie die Clientseite.

#### <a name="authorization-failure"></a>Autorisierungsfehler

Wenn Sie bei Clientanforderungen die Rückgabe „401 – Nicht autorisiert“ erhalten, überprüfen Sie Ihre Ressourcenprotokolle. Wenn `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>` auftritt, bedeutet dies, dass alle Zielgruppen in Ihrem Zugriffstoken ungültig sind. Versuchen Sie, die im Protokoll vorgeschlagenen gültigen Zielgruppen zu verwenden.


#### <a name="throttling"></a>Drosselung

Wenn Sie feststellen, dass Sie keine SignalR-Clientverbindungen mit dem Azure SignalR Service herstellen können, überprüfen Sie Ihre Ressourcenprotokolle. Wenn im Ressourcenprotokoll `Connection count reaches limit` auftritt, stellen Sie zu viele Verbindungen mit dem SignalR Service her, die das Limit für die Verbindungsanzahl erreichen. Erwägen Sie, Ihren SignalR Service zentral hochzuskalieren. Wenn im Ressourcenprotokoll `Message count reaches limit` auftritt, bedeutet dies, dass Sie den kostenlosen Tarif verwenden und das Kontingent der Nachrichten verbrauchen. Wenn Sie weitere Nachrichten senden möchten, sollten Sie einen Wechsel für den SignalR Service in den Standardtarif erwägen, um zusätzliche Nachrichten zu senden. Weitere Informationen finden Sie unter [Azure SignalR Service – Preise](https://azure.microsoft.com/pricing/details/signalr-service/).

### <a name="get-help"></a>Hier erhalten Sie Hilfe

Wir empfehlen Ihnen, zuerst eine eigenständige Problembehandlung durchzuführen. Die meisten Probleme werden vom App-Server oder durch Netzwerkprobleme verursacht. Befolgen Sie das [Handbuch zur Problembehandlung mit Ressourcenprotokollen](#troubleshooting-with-resource-logs) sowie das [Handbuch zur Behandlung grundlegender Probleme](https://github.com/Azure/azure-signalr/blob/dev/docs/tsg.md), um die Grundursachen zu ermitteln.
Wenn das Problem immer noch nicht gelöst werden kann, sollten Sie ein Problem in GitHub öffnen oder ein Ticket im Azure-Portal erstellen.
Stellen Sie Folgendes bereit:
1. Zeitraum von etwa 30 Minuten, wann das Problem auftritt
2. Ressourcen-ID Ihres Azure SignalR Service
3. Problemdetails, so spezifisch wie möglich: Beispielsweise: der App-Server sendet keine Nachrichten, Clientverbindungsunterbrechungen usw.
4. Auf Server-/Clientseite erfasste Protokolle sowie weitere Materialien, die nützlich sein könnten
5. [Optional] Reproduktionscode

> [!NOTE]
> Wenn Sie ein Problem in GitHub öffnen, halten Sie Ihre vertraulichen Informationen (z. B. Ressourcen-ID, Server-/Clientprotokolle) privat, und senden Sie diese nur privat an Mitglieder in der Microsoft-Organisation.