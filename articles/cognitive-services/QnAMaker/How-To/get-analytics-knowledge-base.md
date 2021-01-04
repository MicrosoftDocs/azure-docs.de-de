---
title: Analysen für die Wissensdatenbank – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker speichert alle Chatprotokolle und andere Telemetriedaten, wenn Sie App Insights bei der Erstellung Ihres QnA Maker-Diensts aktiviert haben. Führen Sie die Beispielabfragen aus, um Ihre Chatprotokolle aus App Insights abzurufen.
services: cognitive-services
manager: nitinme
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 5f149dd6db82b66b45a4c995e2004936481af786
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352421"
---
# <a name="get-analytics-on-your-knowledge-base"></a>Abrufen von Analysen zu Ihrer Wissensdatenbank

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

QnA Maker speichert alle Chatprotokolle und anderen Telemetriedaten, wenn Sie Application Insights bei der [Erstellung Ihres QnA Maker-Diensts](./set-up-qnamaker-service-azure.md) aktiviert haben. Führen Sie die Beispielabfragen aus, um Ihre Chatprotokolle aus Application Insights abzurufen.

1. Wechseln Sie zu Ihrer Application Insights-Ressource.

    ![Auswählen Ihrer Application Insights-Ressource](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. Wählen Sie **Protokoll (Analytics)** aus. Daraufhin wird ein neues Fenster geöffnet, in dem Sie QnA Maker-Telemetriedaten abfragen können.

3. Fügen Sie die folgende Abfrage ein, und führen Sie sie aus.

    ```kusto
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, url, resultCode, duration, performanceBucket
    | parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId
    ) on id
    | extend question = tostring(customDimensions['Question'])
    | extend answer = tostring(customDimensions['Answer'])
    | extend score = tostring(customDimensions['Score'])
    | project timestamp, resultCode, duration, id, question, answer, score, performanceBucket,KbId
    ```

    Klicken Sie auf **Ausführen**, um die Abfrage auszuführen.

    [![Ausführen einer Abfrage zum Ermitteln von Fragen, Antworten und Bewertungen von Benutzern](../media/qnamaker-how-to-analytics-kb/run-query.png)](../media/qnamaker-how-to-analytics-kb/run-query.png#lightbox)

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/v2)

QnA Maker verwaltet (Vorschau) verwendet Azure-Diagnoseprotokollierung, um die Telemetriedaten und Chatprotokolle zu speichern. Führen Sie die folgenden Schritte aus, um Beispielabfragen auszuführen, um Analysen zur Verwendung ihrer QnA Maker-Wissensdatenbank zu erhalten.

1. [Aktivieren Sie die Diagnoseprotokollierung](../../diagnostic-logging.md) für Ihren QnA Maker verwaltet (Vorschau)-Dienst.

2. Wählen Sie im vorherigen Schritt zusätzlich zu **Audit, RequestResponse und AllMetrics** noch **Trace** für die Protokollierung aus.

    ![Aktivieren der Ablaufverfolgungsprotokollierung in QnA Maker verwaltet (Vorschau)](../media/qnamaker-how-to-analytics-kb/qnamaker-v2-enable-trace-logging.png)

---

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>Ausführen von Abfragen für andere Analysen zu Ihrer QnA Maker-Wissensdatenbank

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker, allgemeine Verfügbarkeit (stabile Version)](#tab/v1)

### <a name="total-90-day-traffic"></a>Gesamter Datenverkehr von 90 Tagen

```kusto
//Total Traffic
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| summarize ChatCount=count() by bin(timestamp, 1d), KbId
```

### <a name="total-question-traffic-in-a-given-time-period"></a>Gesamter Fragendatenverkehr in einem bestimmten Zeitraum

```kusto
//Total Question Traffic in a given time period
let startDate = todatetime('2019-01-01');
let endDate = todatetime('2020-12-31');
requests
| where timestamp <= endDate and timestamp >=startDate
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>Benutzerdatenverkehr

```kusto
//User Traffic
requests
| where url endswith "generateAnswer"
| project timestamp, id, url, resultCode, duration
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| join kind= inner (
traces | extend id = operation_ParentId
) on id
| extend UserId = tostring(customDimensions['UserId'])
| summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### <a name="latency-distribution-of-questions"></a>Latenzverteilung von Fragen

```kusto
//Latency distribution of questions
requests
| where url endswith "generateAnswer" and name startswith "POST"
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| project timestamp, id, name, resultCode, performanceBucket, KbId
| summarize count() by performanceBucket, KbId
```

### <a name="unanswered-questions"></a>Unbeantwortete Fragen

```kusto
// Unanswered questions
requests
| where url endswith "generateAnswer"
| project timestamp, id, url
| parse kind = regex url with *"(?i)knowledgebases/"KbId"/generateAnswer"
| join kind= inner (
traces | extend id = operation_ParentId
) on id
| extend question = tostring(customDimensions['Question'])
| extend answer = tostring(customDimensions['Answer'])
| extend score = tostring(customDimensions['Score'])
| where  score  == "0" and message == "QnAMaker GenerateAnswer"
| project timestamp, KbId, question, answer, score
| order  by timestamp  desc
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker verwaltet (Vorschauversion)](#tab/v2)

### <a name="all-qna-chat-log"></a>Alle QnA-Chatprotokolle

```kusto
// All QnA Traffic
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="QnAMaker GenerateAnswer"
| extend answer_ = tostring(parse_json(properties_s).answer)
| extend question_ = tostring(parse_json(properties_s).question)
| extend score_ = tostring(parse_json(properties_s).score)
| extend kbId_ = tostring(parse_json(properties_s).kbId)
| project question_, answer_, score_, kbId_
```

### <a name="traffic-count-per-knowledge-base-and-user-in-a-time-period"></a>Datenverkehrszähler pro Wissensdatenbank und Benutzer in einem bestimmten Zeitraum

```kusto
// Traffic count per KB and user in a time period
let startDate = todatetime('2019-01-01');
let endDate = todatetime('2020-12-31');
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="QnAMaker GenerateAnswer"
| where TimeGenerated <= endDate and TimeGenerated >=startDate
| extend kbId_ = tostring(parse_json(properties_s).kbId)
| extend userId_ = tostring(parse_json(properties_s).userId)
| summarize ChatCount=count() by bin(TimeGenerated, 1d), kbId_, userId_
```

### <a name="latency-of-generateanswer-api"></a>Wartezeit der GenerateAnswer-API

```kusto
// Latency of GenerateAnswer
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="Generate Answer"
| project TimeGenerated, DurationMs
| render timechart
```

### <a name="average-latency-of-all-operations"></a>Durchschnittliche Wartezeit aller Vorgänge

```kusto
// Average Latency of all operations
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| project DurationMs, OperationName
| summarize count(), avg(DurationMs) by OperationName
| render barchart
```

### <a name="unanswered-questions"></a>Unbeantwortete Fragen

```kusto
// All unanswered questions
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| where OperationName=="QnAMaker GenerateAnswer"
| extend answer_ = tostring(parse_json(properties_s).answer)
| extend question_ = tostring(parse_json(properties_s).question)
| extend score_ = tostring(parse_json(properties_s).score)
| extend kbId_ = tostring(parse_json(properties_s).kbId)
| where score_ == 0
| project question_, answer_, score_, kbId_
```

---

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Auswählen der Kapazität](./improve-knowledge-base.md)