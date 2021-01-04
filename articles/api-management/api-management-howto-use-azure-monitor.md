---
title: 'Tutorial: Überwachen von veröffentlichten APIs in Azure API Management | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie Schritt für Schritt, wie Sie Metriken, Warnungen, Aktivitätsprotokolle und Ressourcenprotokolle zur Überwachung Ihrer APIs in Azure API Management verwenden.
services: api-management
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: apimpm
ms.openlocfilehash: 5e5c59d611cb7f4b5333b9919488e6fc083611cd
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96779242"
---
# <a name="tutorial-monitor-published-apis"></a>Tutorial: Überwachen von veröffentlichten APIs

Mit Azure Monitor können Sie Metriken oder Protokolle aus Ihrem Azure API Management-Dienst visualisieren, abfragen, weiterleiten und archivieren sowie ggf. notwendige Maßnahmen ergreifen.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Anzeigen von Metriken Ihrer API 
> * Einrichten einer Warnungsregel 
> * Anzeigen von Aktivitätsprotokollen
> * Aktivieren und Anzeigen von Ressourcenprotokollen

Sie können auch die integrierte [Analyse](howto-use-analytics.md) von API Management verwenden, um die Nutzung und Leistung Ihrer APIs zu überwachen.

## <a name="prerequisites"></a>Voraussetzungen

+ Machen Sie sich mit der [Azure API Management-Terminologie](api-management-terminology.md) vertraut.
+ Bearbeiten Sie den folgenden Schnellstart: [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md)
+ Absolvieren Sie außerdem das folgende Tutorial: [Importieren und Veröffentlichen Ihrer ersten API](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="view-metrics-of-your-apis"></a>Anzeigen von Metriken Ihrer API

Von API Management werden jede Minute [Metriken](../azure-monitor/platform/data-platform-metrics.md) ausgegeben, sodass Sie nahezu in Echtzeit einen Überblick über den Zustand und die Integrität Ihrer APIs erhalten. Im Anschluss finden Sie die beiden am häufigsten verwendeten Metriken. Eine Liste aller verfügbaren Metriken finden Sie unter [unterstützte Metriken](../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice).

* **Kapazität:** Unterstützt Sie beim Treffen von Entscheidungen in Bezug auf Upgrades/Downgrades Ihrer APIM-Dienste. Die Metrik wird minütlich ausgegeben und spiegelt die Gatewaykapazität zum Zeitpunkt der Meldung wider. Der Wert der Metrik kann zwischen 0 und 100 liegen und wird basierend auf Gatewayressourcen wie CPU und Speicherauslastung berechnet.
* **Anforderungen:** Unterstützt Sie bei der Analyse von API-Datenverkehr, der Ihre API Management-Dienste durchläuft. Die Metrik wird pro Minute ausgegeben und meldet die Anzahl von Gatewayanforderungen mit Dimensionen, einschließlich Antwortcodes, Standort, Hostname und Fehlern. 

> [!IMPORTANT]
> Die folgenden Metriken sind ab Mai 2019 veraltet und werden im August 2023 eingestellt: Total Gateway Requests (Gatewayanforderungen gesamt), Successful Gateway Requests (Erfolgreiche Gatewayanforderungen), Unauthorized Gateway Requests (Nicht autorisierte Gatewayanforderungen), Failed Gateway Requests (Fehlgeschlagene Gatewayanforderungen), Other Gateway Requests (Sonstige Gatewayanforderungen). Migrieren Sie zur Metrik „Anforderungen“, die eine entsprechende Funktionalität bietet.

:::image type="content" source="media/api-management-howto-use-azure-monitor/apim-monitor-metrics.png" alt-text="Screenshot: Übersicht über Metriken in API Management":::

So greifen Sie auf Metriken zu:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer API Management-Instanz. Überprüfen Sie auf der Seite **Übersicht** die Schlüsselmetriken für Ihre APIs.
1. Wählen Sie im Menü am unteren Seitenrand die Option **Metriken** aus, um Metriken im Detail zu untersuchen.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-metrics-blade.png" alt-text="Screenshot: Element „Metriken“ im Menü „Überwachung“":::

1. Wählen Sie im Dropdownmenü die gewünschten Metriken aus. Beispiel: **Anforderungen**. 
1. Im Diagramm ist die Gesamtanzahl von API-Aufrufen gezeigt.
1. Das Diagramm kann anhand der Dimensionen der Metrik **Anforderungen** gefiltert werden. Wählen Sie beispielsweise **Filter hinzufügen** und anschließend die Kategorie für den Back-End-Antwortcode aus, und geben Sie **500** als Wert ein. Das Diagramm zeigt nun die Anzahl von Anforderungen an, für die im API-Back-End ein Fehler auftrat.   

## <a name="set-up-an-alert-rule"></a>Einrichten einer Warnungsregel 

Sie können [Warnungen](../azure-monitor/platform/alerts-metric-overview.md) auf der Grundlage von Metriken und Aktivitätsprotokollen empfangen. Mit Azure Monitor können Sie [eine Warnung konfigurieren](../azure-monitor/platform/alerts-metric.md), die folgende Schritte auslöst:

* Senden einer E-Mail-Benachrichtigung
* Aufrufen eines Webhooks
* Aufrufen einer Azure-Logik-App

So konfigurieren Sie eine exemplarische Warnungsregel auf der Grundlage einer Anforderungsmetrik:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer API Management-Instanz.
1. Klicken Sie im unteren Seitenbereich auf der Menüleiste auf **Warnungen**.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/alert-menu-item.png" alt-text="Screenshot: Option „Warnungen“ im Menü „Überwachung“":::

1. Wählen Sie **+ Neue Warnungsregel** aus.
1. Wählen Sie im Fenster **Warnungsregel erstellen** die Option **Bedingung auswählen** aus.
1. Gehen Sie im Fenster **Signallogik konfigurieren** wie folgt vor:
    1. Wählen Sie unter **Signaltyp** die Option **Metriken** aus.
    1. Wählen Sie unter **Signalname** die Option **Anforderungen** aus.
    1. Wählen Sie im Abschnitt **Split by dimensions** (Nach Dimensionen aufteilen) unter **Dimensionsname** die **Kategorie für den Gatewayantwortcode aus**.
    1. Wählen Sie unter **Dimensionswerte** die Option **4xx** (für Clientfehler wie nicht autorisierte oder ungültige Anforderungen) aus.
    1. Geben Sie unter **Warnungslogik** einen Schwellenwert an, nach dem die Warnung ausgelöst werden soll, und wählen Sie anschließend **Fertig** aus.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/threshold.png" alt-text="Screenshot: Fenster zum Konfigurieren der Signallogik":::

1. Wählen Sie eine vorhandene Aktionsgruppe aus, oder erstellen Sie eine neue Aktionsgruppe. Im folgenden Beispiel wird eine neue Aktionsgruppe erstellt. Eine Bestätigungs-E-Mail wird an admin@contoso.com gesendet. 

    :::image type="content" source="media/api-management-howto-use-azure-monitor/action-details.png" alt-text="Screenshot: Benachrichtigungen für neue Aktionsgruppe":::

1. Geben Sie einen Namen und eine Beschreibung der Warnungsregel ein, und wählen Sie den Schweregrad aus. 
1. Wählen Sie **Warnungsregel erstellen** aus.
1. Testen Sie nun die Warnungsregel, indem Sie die Konferenz-API ohne API-Schlüssel aufrufen. Zum Beispiel:

    ```bash
    curl GET https://apim-hello-world.azure-api.net/conference/speakers HTTP/1.1 
    ```

    Eine auf dem Evaluierungszeitraum basierende Warnung wird ausgelöst, und eine E-Mail wird an admin@contoso.com gesendet. 

    Warnungen werden auch auf der Seite **Warnungen** für die API Management-Instanz angezeigt.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/portal-alerts.png" alt-text="Screenshot: Warnungen im Portal":::

## <a name="activity-logs"></a>Aktivitätsprotokolle

Aktivitätsprotokolle geben Einblick in die Vorgänge, die für Ihre API Management-Dienste ausgeführt wurden. Mit dem Aktivitätsprotokoll können Sie die Antworten auf die Fragen „Was“, „Wer“ und „Wann“ für alle Schreibvorgänge (PUT, POST, DELETE) ermitteln, die für Ihre API Management-Dienste durchgeführt wurden.

> [!NOTE]
> Aktivitätsprotokolle enthalten keine Lesevorgänge (GET) oder Vorgänge, die im Azure-Portal oder mithilfe der ursprünglichen Management-APIs durchgeführt wurden.

Sie können in Ihrem API Management-Dienst auf Aktivitätsprotokolle oder in Azure Monitor auf Protokolle all Ihrer Azure-Ressourcen zugreifen. 

:::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-activity-logs.png" alt-text="Screenshot: Aktivitätsprotokoll im Portal":::

So zeigen Sie das Aktivitätsprotokoll an:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer API Management-Instanz.

1. Wählen Sie **Aktivitätsprotokoll** aus.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-activity-logs-blade.png" alt-text="Screenshot: Element „Aktivitätsprotokoll“ im Menü „Überwachung“":::
1. Wählen Sie den gewünschten Filterbereich und anschließend **Anwenden** aus.

## <a name="resource-logs"></a>Ressourcenprotokolle

Ressourcenprotokolle bieten umfassende Informationen zu Vorgängen und Fehlern, die für die Überwachung und Problembehandlung relevant sind. Ressourcenprotokolle unterscheiden sich von Aktivitätsprotokollen. Das Aktivitätsprotokoll gibt Einblick in die Vorgänge, die für Ihre Azure-Ressourcen ausgeführt wurden. Ressourcenprotokolle bieten Einblicke in Vorgänge, die Ihre Ressource ausgeführt hat.

So konfigurieren Sie Ressourcenprotokolle:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer API Management-Instanz.
2. Wählen Sie **Diagnoseeinstellungen** aus.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-diagnostic-logs-blade.png" alt-text="Screenshot: Element „Diagnoseeinstellungen“ im Menü „Überwachung“":::

1. Wählen Sie **+Diagnoseeinstellung hinzufügen**  aus.
1. Wählen Sie die zu sammelnden Protokolle oder Metriken aus.

   Sie können Ressourcenprotokolle zusammen mit Metriken in einem Speicherkonto archivieren, an Event Hub streamen oder an einen Log Analytics-Arbeitsbereich senden. 

Weitere Informationen finden Sie unter [Erstellen von Diagnoseeinstellungen zum Senden von Plattformprotokollen und Metriken an verschiedene Ziele](../azure-monitor/platform/diagnostic-settings.md).

## <a name="view-diagnostic-data-in-azure-monitor"></a>Anzeigen von Diagnosedaten in Azure Monitor

Wenn Sie die Sammlung von Gatewayprotokollen (GatewayLogs) oder Metriken in einem Log Analytics-Arbeitsbereich aktiviert haben, kann es ein paar Minuten dauern, bis die Daten in Azure Monitor angezeigt werden. So zeigen Sie die Daten an:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer API Management-Instanz.
1. Wählen Sie im Menü am unteren Seitenrand die Option **Protokolle** aus.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/logs-menu-item.png" alt-text="Screenshot: Element „Protokolle“ im Menü „Überwachung“":::

Führen Sie Abfragen aus, um die Daten anzuzeigen. Es stehen mehrere [Beispielabfragen](../azure-monitor/log-query/example-queries.md) zur Verfügung. Sie können aber auch eine eigene Abfrage verwenden. Mit der folgenden Abfrage werden beispielsweise die Daten der letzten 24 Stunden aus der Tabelle „GatewayLogs“ abgerufen:

```kusto
ApiManagementGatewayLogs
| where TimeGenerated > ago(1d) 
```

Weitere Informationen zur Verwendung von Ressourcenprotokollen für API Management finden Sie hier:

* [Erste Schritte mit Azure Monitor Log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md) oder in der [Demoumgebung von Log Analytics](https://portal.loganalytics.io/demo).

* [Übersicht über Protokollabfragen in Azure Monitor](../azure-monitor/log-query/log-query-overview.md)

Der folgende JSON-Code zeigt einen Beispieleintrag in „GatewayLogs“ für eine erfolgreiche API-Anforderung. Ausführliche Informationen finden Sie in der [Schemareferenz](gateway-log-schema-reference.md). 

```json
{
    "Level": 4,
    "isRequestSuccess": true,
    "time": "2020-10-14T17:xx:xx.xx",
    "operationName": "Microsoft.ApiManagement/GatewayLogs",
    "category": "GatewayLogs",
    "durationMs": 152,
    "callerIpAddress": "xx.xx.xxx.xx",
    "correlationId": "3f06647e-xxxx-xxxx-xxxx-530eb9f15261",
    "location": "East US",
    "properties": {
        "method": "GET",
        "url": "https://apim-hello-world.azure-api.net/conference/speakers",
        "backendResponseCode": 200,
        "responseCode": 200,
        "responseSize": 41583,
        "cache": "none",
        "backendTime": 87,
        "requestSize": 526,
        "apiId": "demo-conference-api",
        "operationId": "GetSpeakers",
        "apimSubscriptionId": "master",
        "clientTime": 65,
        "clientProtocol": "HTTP/1.1",
        "backendProtocol": "HTTP/1.1",
        "apiRevision": "1",
        "clientTlsVersion": "1.2",
        "backendMethod": "GET",
        "backendUrl": "https://conferenceapi.azurewebsites.net/speakers"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group>/PROVIDERS/MICROSOFT.APIMANAGEMENT/SERVICE/APIM-HELLO-WORLD"
}
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Anzeigen von Metriken Ihrer API
> * Einrichten einer Warnungsregel 
> * Anzeigen von Aktivitätsprotokollen
> * Aktivieren und Anzeigen von Ressourcenprotokollen


Fahren Sie mit dem nächsten Tutorial fort:

> [!div class="nextstepaction"]
> [Verwenden des API-Inspektors zur Verfolgung von Aufrufen in Azure API Management](api-management-howto-api-inspector.md)