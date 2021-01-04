---
title: 'Azure Functions-Trigger für Cosmos DB: Erweiterte Konfiguration'
description: Hier erfahren Sie, wie Sie Protokollierungs- und Verbindungsrichtlinien konfigurieren, die vom Azure Functions-Trigger für Cosmos DB verwendet werden.
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/19/2020
ms.author: maquaran
ms.openlocfilehash: c47d18726d9581b1b03aa2e676a71d6ca1bc1b7d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93086464"
---
# <a name="how-to-configure-logging-and-connectivity-with-the-azure-functions-trigger-for-cosmos-db"></a>Konfigurieren der Protokollierung und Konnektivität mit dem Azure Functions-Trigger für Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In diesem Artikel werden erweiterte Konfigurationsoptionen beschrieben, die Sie beim Verwenden des Azure Functions-Triggers für Cosmos DB festlegen können.

## <a name="enabling-trigger-specific-logs"></a>Aktivieren von triggerspezifischen Protokollen

Der Azure Functions-Trigger für Cosmos DB nutzt intern die [Änderungsfeed-Prozessorbibliothek](./change-feed-processor.md), und die Bibliothek generiert eine Reihe von Integritätsprotokollen, die zur Überwachung interner Vorgänge für die [Problembehandlung](./troubleshoot-changefeed-functions.md) verwendet werden können.

Die Integritätsprotokolle geben Aufschluss über das Verhalten des Azure Functions-Triggers für Cosmos DB bei Vorgängen in Lastenausgleichsszenarien oder während der Initialisierung.

### <a name="enabling-logging"></a>Aktivieren der Protokollierung

Suchen Sie bei Verwendung des Azure Functions-Triggers für Cosmos DB zum Aktivieren der Protokollierung in Ihrem Azure Functions-Projekt oder in Ihrer Azure Functions-App nach der Datei `host.json`, und [konfigurieren Sie den erforderlichen Protokolliergrad](../azure-functions/functions-monitoring.md#log-levels-and-categories). Aktivieren Sie wie im folgenden Beispiel gezeigt die Ablaufverfolgungen für `Host.Triggers.CosmosDB`:

```js
{
  "version": "2.0",
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "Host.Triggers.CosmosDB": "Trace"
    }
  }
}
```

Nachdem Sie die Azure-Funktion mit der aktualisierten Konfiguration bereitgestellt haben, werden die Protokolle für Azure Functions-Trigger für Cosmos DB im Rahmen Ihrer Ablaufverfolgungen angezeigt. Die Protokolle finden Sie in Ihrem konfigurierten Protokollierungsanbieter in der *Kategorie* `Host.Triggers.CosmosDB`.

### <a name="query-the-logs"></a>Abfragen der Protokolle

Führen Sie die folgende Abfrage aus, um die vom Azure Functions-Trigger für Cosmos DB generierten Protokolle in der [Azure Application Insights-Analyse](../azure-monitor/log-query/log-query-overview.md) abzufragen:

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="configuring-the-connection-policy"></a>Konfigurieren der Verbindungsrichtlinie

Es gibt zwei Verbindungsmodi: den direkten Modus und den Gatewaymodus. Weitere Informationen zu diesen Verbindungsmodi finden Sie im Artikel [Azure Cosmos DB SQL SDK-Konnektivitätsmodi](sql-sdk-connection-modes.md). Standardmäßig wird ein **Gateway** zum Herstellen aller Verbindungen für den Azure Functions-Trigger für Cosmos DB verwendet. Dies ist jedoch unter Umständen nicht die optimale Option für Szenarien mit hohen Leistungsanforderungen.

### <a name="changing-the-connection-mode-and-protocol"></a>Ändern des Verbindungsmodus und des Protokolls

Für die Konfiguration der Clientverbindungsrichtlinie stehen zwei wichtige Konfigurationseinstellungen zur Verfügung: der **Verbindungsmodus** und das **Verbindungsprotokoll**. Sie können die Standardeinstellung für den Verbindungsmodus und das Verbindungsprotokoll ändern, die vom Azure Functions-Trigger für Cosmos DB und allen [Azure Cosmos DB-Bindungen](../azure-functions/functions-bindings-cosmosdb-v2-output.md) verwendet werden. Wenn Sie die Standardeinstellungen ändern möchten, müssen Sie in Ihrem Azure Functions-Projekt zur Datei `host.json` navigieren und die folgende [zusätzliche Einstellung](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings) hinzufügen:

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

Dabei muss für `connectionMode` der gewünschte Verbindungsmodus (direkte Verbindung oder Gatewayverbindung) und für `protocol` das gewünschte Verbindungsprotokoll (TCP oder HTTPS) angegeben werden. 

Bei Azure Functions-Projekten mit Version 1 der Azure Functions-Runtime hat die Konfiguration einen geringfügig anderen Namen. Verwenden Sie `documentDB` anstelle von `cosmosDB`:

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> Wenn Sie als Azure Functions-Hostingplan den Verbrauchsplan verwenden, gilt für jede Instanz ein Grenzwert bei der Menge der Socket-Verbindungen, die aufrechterhalten werden können. Wenn Sie den direkten Modus oder den TCP-Modus verwenden, werden standardmäßig mehr Verbindungen erstellt, und unter Umständen wird der [Grenzwert des Verbindungsplans](../azure-functions/manage-connections.md#connection-limit) erreicht. In diesem Fall können Sie entweder den Gatewaymodus verwenden oder Azure Functions im [App Service-Modus](../azure-functions/functions-scale.md#app-service-plan) ausführen.

## <a name="next-steps"></a>Nächste Schritte

* [Verbindungsgrenzwerte in Azure Functions](../azure-functions/manage-connections.md#connection-limit)
* [Aktivieren Sie die Überwachung](../azure-functions/functions-monitoring.md) in Ihren Azure Functions-Anwendungen.
* Informieren Sie sich über die Vorgehensweise zum [Diagnostizieren und Behandeln allgemeiner Probleme](./troubleshoot-changefeed-functions.md) bei Verwendung des Azure Functions-Triggers für Cosmos DB.