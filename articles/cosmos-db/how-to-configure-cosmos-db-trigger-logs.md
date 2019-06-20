---
title: Protokolle für Azure Cosmos DB-Trigger
description: Hier erfahren Sie, wie Sie die Protokolle für Azure Cosmos DB-Trigger für Ihre Azure Functions-Protokollierungspipeline verfügbar machen.
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: maquaran
ms.openlocfilehash: bf5216dc3b296c98176387c6e2cfff7c31daedab
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241022"
---
# <a name="how-to-configure-and-read-the-azure-cosmos-db-trigger-logs"></a>Konfigurieren und Lesen der Protokolle für Azure Cosmos DB-Trigger

In diesem Artikel erfahren Sie, wie Sie Ihre Azure Functions-Umgebung so konfigurieren, dass die Protokolle für Azure Cosmos DB-Trigger an Ihre konfigurierte [Überwachungslösung](../azure-functions/functions-monitoring.md) gesendet werden.

## <a name="included-logs"></a>Enthaltene Protokolle

Der Azure Cosmos DB-Trigger nutzt intern die [Change Feed Processor-Bibliothek](./change-feed-processor.md), und die Bibliothek generiert eine Reihe von Integritätsprotokollen, die zur Überwachung interner Vorgänge für die [Problembehandlung](./troubleshoot-changefeed-functions.md) verwendet werden können.

Die Integritätsprotokolle geben Aufschluss über das Verhalten des Azure Cosmos DB-Triggers bei Vorgängen in Lastenausgleichsszenarien oder während der Initialisierung.

## <a name="enabling-logging"></a>Aktivieren der Protokollierung

Suchen Sie zum Aktivieren der Protokollierung für Azure Cosmos DB-Trigger in Ihrem Azure Functions-Projekt oder in Ihrer Azure Functions-App nach der Datei `host.json`, und [konfigurieren Sie den erforderlichen Protokolliergrad](../azure-functions/functions-monitoring.md#log-configuration-in-hostjson). Sie müssen die Ablaufverfolgungen für `Host.Triggers.CosmosDB` aktivieren, wie im folgenden Beispiel zu sehen:

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

Nachdem Sie die Azure-Funktion mit der aktualisierten Konfiguration bereitgestellt haben, werden die Protokolle für Azure Cosmos DB-Trigger im Rahmen Ihrer Ablaufverfolgungen angezeigt. Die Protokolle finden Sie in Ihrem konfigurierten Protokollierungsanbieter unter der *Kategorie* `Host.Triggers.CosmosDB`.

## <a name="query-the-logs"></a>Abfragen der Protokolle

Führen Sie die folgende Abfrage aus, um die vom Azure Cosmos DB-Trigger generierten Protokolle in der [Azure Application Insights-Analyse](../azure-monitor/app/analytics.md) abzufragen:

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="next-steps"></a>Nächste Schritte

* [Aktivieren Sie die Überwachung](../azure-functions/functions-monitoring.md) in Ihren Azure Functions-Anwendungen.
* Informieren Sie sich über die Vorgehensweise zum [Diagnostizieren und Behandeln allgemeiner Probleme](./troubleshoot-changefeed-functions.md) bei Verwendung des Azure Cosmos DB-Triggers in Azure Functions.