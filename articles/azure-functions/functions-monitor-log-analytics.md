---
title: Überwachen von Azure Functions mit Azure Monitor-Protokollen
description: Erfahren Sie, wie Sie Azure Monitor-Protokolle mit Azure Functions zum Überwachen der Funktionsausführung verwenden.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python
ms.openlocfilehash: 582dafba40012e9ff9c59bc09adb1a0831e999f5
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491221"
---
# <a name="monitoring-azure-functions-with-azure-monitor-logs"></a>Überwachen von Azure Functions mit Azure Monitor-Protokollen

Azure Functions bietet eine Integration in [Azure Monitor-Protokolle](../azure-monitor/platform/data-platform-logs.md) zum Überwachen von Funktionen. In diesem Artikel wird veranschaulicht, wie Sie Azure Functions zum Senden von systemseitig generierten sowie von benutzergenerierten Protokollen an Azure Monitor-Protokolle konfigurieren.

Azure Monitor-Protokolle bieten Ihnen die Möglichkeit, Protokolle aus unterschiedlichen Ressourcen im selben Arbeitsbereich zu konsolidieren, wo Sie mit [Abfragen](../azure-monitor/log-query/log-query-overview.md) analysiert werden können, um gesammelte Daten schnell abzurufen, zu konsolidieren und zu analysieren.  Sie können Abfragen mit [Log Analytics](../azure-monitor/log-query/log-query-overview.md) im Azure-Portal erstellen und testen und die Daten dann entweder mit diesen Tools direkt analysieren oder Abfragen zur Verwendung mit [Visualisierungen](../azure-monitor/visualizations.md) oder [Warnungsregeln](../azure-monitor/platform/alerts-overview.md) speichern.

Azure Monitor verwendet eine Version der von Azure Data Explorer verwendeten [Abfragesprache Kusto](/azure/kusto/query/), die für einfache Protokollabfragen geeignet ist, aber auch erweiterte Funktionen enthält, z.B. Aggregationen, Verknüpfungen und intelligente Analysen. Sie können die Abfragesprache schnell erlernen, indem Sie [mehrere Lektionen](../azure-monitor/log-query/get-started-queries.md) nutzen.

> [!NOTE]
> Die Integration mit Azure Monitor-Protokollen befindet sich derzeit in der öffentlichen Vorschauphase für Funktions-Apps der Version 2 und Version 3, die unter den Windows-Hostingplänen „Nutzung“, „Premium“ und „Dedicated“ ausgeführt werden.

## <a name="setting-up"></a>Einrichten

1. Wählen Sie im Abschnitt **Überwachung** der Funktions-App im [Azure-Portal](https://portal.azure.com) die Option **Diagnoseeinstellungen** aus, und wählen Sie dann **Diagnoseeinstellung hinzufügen** aus.

   :::image type="content" source="media/functions-monitor-log-analytics/diagnostic-settings-add.png" alt-text="Auswählen der Diagnoseeinstellungen":::

1. Wählen Sie auf der Seite **Diagnoseeinstellungen** unter **Kategoriedetails** und **Protokoll** die Option **FunctionAppLogs** aus.

   Die Tabelle **FunctionAppLogs** enthält die gewünschten Protokolle.

1. Wählen Sie unter **Zieldetails** die Option **An Log Analytics senden** und anschließend Ihren **Log Analytics-Arbeitsbereich** aus. 

1. Geben Sie einen **Namen für die Diagnoseeinstellungen** ein, und wählen Sie dann **Speichern** aus.

   :::image type="content" source="media/functions-monitor-log-analytics/choose-table.png" alt-text="Hinzufügen einer Diagnoseeinstellung":::

## <a name="user-generated-logs"></a>Benutzergenerierte Protokolle

Um benutzerdefinierte Protokolle zu generieren, verwenden Sie die für Ihre Sprache spezifische Protokollierungsanweisung. Im Folgenden finden Sie Beispielcodeausschnitte:


# <a name="c"></a>[C#](#tab/csharp)

```csharp
log.LogInformation("My app logs here.");
```

# <a name="java"></a>[Java](#tab/java)

```java
context.getLogger().info("My app logs here.");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
context.log('My app logs here.');
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Write-Host "My app logs here."
```

# <a name="python"></a>[Python](#tab/python)

```python
logging.info('My app logs here.')
```

---

## <a name="querying-the-logs"></a>Abfragen der Protokolle

So fragen Sie die generierten Protokolle ab:
 
1. Wählen Sie in ihrer Funktions-App **Diagnoseeinstellungen** aus. 

1. Wählen Sie in der Liste **Diagnoseeinstellungen** den Log Analytics-Arbeitsbereich aus, an den die Funktionsprotokolle gesendet werden sollen. 

1. Wählen Sie auf der Seite **Log Analytics-Arbeitsbereich** die Option **Protokolle** aus.

   Azure Functions schreibt alle Protokolle in die Tabelle **FunctionAppLogs** unter **LogManagement**. 

   :::image type="content" source="media/functions-monitor-log-analytics/querying.png" alt-text="Abfragefenster im Log Analytics-Arbeitsbereich":::

Im Folgenden finden Sie einige Beispielabfragen:

### <a name="all-logs"></a>Alle Protokolle

```

FunctionAppLogs
| order by TimeGenerated desc

```

### <a name="specific-function-logs"></a>Spezifische Funktionsprotokolle

```

FunctionAppLogs
| where FunctionName == "<Function name>" 

```

### <a name="exceptions"></a>Ausnahmen

```

FunctionAppLogs
| where ExceptionDetails != ""  
| order by TimeGenerated asc

```

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie die [Übersicht zu Azure Functions](functions-overview.md).
- Weitere Informationen zu [Azure Monitor-Protokollen](../azure-monitor/platform/data-platform-logs.md).
- Erfahren Sie mehr über die [Abfragesprache](../azure-monitor/log-query/get-started-queries.md).
