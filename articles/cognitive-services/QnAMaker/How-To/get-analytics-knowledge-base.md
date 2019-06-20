---
title: Analysen für die Wissensdatenbank
titleSuffix: Azure Cognitive Services
description: QnA Maker speichert alle Chatprotokolle und andere Telemetriedaten, wenn Sie App Insights bei der Erstellung Ihres QnA Maker-Diensts aktiviert haben. Führen Sie die Beispielabfragen aus, um Ihre Chatprotokolle aus App Insights abzurufen.
services: cognitive-services
author: tulasim88
manager: nitinme
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/04/2019
ms.author: tulasim
ms.openlocfilehash: 09369e760c654e7e56067e6da02bb772bffa7400
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61372595"
---
# <a name="get-analytics-on-your-knowledge-base"></a>Abrufen von Analysen zu Ihrer Wissensdatenbank

QnA Maker speichert alle Chatprotokolle und andere Telemetriedaten, wenn Sie App Insights bei der [Erstellung Ihres QnA Maker-Diensts](./set-up-qnamaker-service-azure.md) aktiviert haben. Führen Sie die Beispielabfragen aus, um Ihre Chatprotokolle aus App Insights abzurufen.

1. Wechseln Sie zu Ihrer App Insights-Ressource.

    ![Auswählen Ihrer Application Insights-Ressource](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. Wählen Sie **Analytics** aus. Daraufhin wird ein neues Fenster geöffnet, in dem Sie QnA Maker-Telemetriedaten abfragen können.

    ![Auswählen von Analytics](../media/qnamaker-how-to-analytics-kb/analytics.png)

3. Fügen Sie die folgende Abfrage ein, und führen Sie sie aus.

    ```query
        requests
        | where url endswith "generateAnswer"
        | project timestamp, id, name, resultCode, duration
        | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer"
        | join kind= inner (
        traces | extend id = operation_ParentId
        ) on id
        | extend question = tostring(customDimensions['Question'])
        | extend answer = tostring(customDimensions['Answer'])
        | project KbId, timestamp, resultCode, duration, question, answer
    ```

    Wählen Sie **Ausführen** aus, um die Abfrage auszuführen.

    ![Abfrage ausführen](../media/qnamaker-how-to-analytics-kb/run-query.png)

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>Ausführen von Abfragen für andere Analysen zu Ihrer QnA Maker-Wissensdatenbank

### <a name="total-90-day-traffic"></a>Gesamter Datenverkehr von 90 Tagen

```query
    //Total Traffic
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by bin(timestamp, 1d), KbId
```

### <a name="total-question-traffic-in-a-given-time-period"></a>Gesamter Fragendatenverkehr in einem bestimmten Zeitraum

```query
    //Total Question Traffic in a given time period
    let startDate = todatetime('2018-02-18');
    let endDate = todatetime('2018-03-12');
    requests
    | where timestamp <= endDate and timestamp >=startDate
    | where url endswith "generateAnswer" and name startswith "POST" 
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>Benutzerdatenverkehr

```query
    //User Traffic
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, name, resultCode, duration
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId 
    ) on id
    | extend UserId = tostring(customDimensions['UserId'])
    | summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### <a name="latency-distribution-of-questions"></a>Latenzverteilung von Fragen

```query
    //Latency distribution of questions
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | project timestamp, id, name, resultCode, performanceBucket, KbId
    | summarize count() by performanceBucket, KbId
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Verwalten von Schlüsseln](./key-management.md)
