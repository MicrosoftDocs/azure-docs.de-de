---
title: Behandlung von Fehlern und Ausnahmen – Azure Logic Apps | Microsoft-Dokumentation
description: Enthält eine Beschreibung der Muster für die Behandlung von Fehlern und Ausnahmen in Azure Logic Apps.
services: logic-apps
ms.service: logic-apps
author: dereklee
ms.author: deli
manager: jeconnoc
ms.date: 01/31/2018
ms.topic: article
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 3f812c1142b5cd40169f7340163295b0f7ea6a4d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60996590"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Behandeln von Fehlern und Ausnahmen in Azure Logic Apps

Die Art und Weise, wie eine Integrationsarchitektur Ausfallzeiten oder Probleme aufgrund von abhängigen Systemen behandelt, kann eine Herausforderung darstellen. Damit Sie stabile und robuste Integrationen erstellen können, bei denen Probleme und Fehler korrekt behandelt werden, verfügt Logic Apps über eine professionelle Benutzeroberfläche für den Umgang mit Fehlern und Ausnahmen. 

<a name="retry-policies"></a>

## <a name="retry-policies"></a>Wiederholungsrichtlinien

Für die grundlegende Behandlung von Ausnahmen und Fehlern können Sie eine *Wiederholungsrichtlinie* in allen Aktionen oder Triggern verwenden, sofern dies unterstützt wird. In einer Wiederholungsrichtlinie wird angegeben, ob und wie die Aktion bzw. der Trigger versucht, eine Anforderung zu wiederholen, wenn es für die ursprüngliche Anforderung zu einem Timeout oder einem Ausfall kommt. Dies sind alle Anforderungen, die zu Antworten vom Typ 408, 429 oder 5xx führen. Wenn keine andere Wiederholungsrichtlinie verwendet wird, wird die Standardrichtlinie genutzt. 

Hier sind die Arten von Wiederholungsrichtlinien angegeben: 

| Type | BESCHREIBUNG | 
|------|-------------| 
| **Standard** | Bei dieser Richtlinie werden bis zu vier Wiederholungen in Intervallen durchgeführt, die sich *exponentiell erhöhen*. Sie werden um 7,5 Sekunden skaliert, aber der obere Grenzwert liegt zwischen 5 und 45 Sekunden. | 
| **Exponentielles Intervall**  | Bei dieser Richtlinie wird für den Zeitraum eines zufälligen Intervalls gewartet, das aus einem exponentiell zunehmenden Bereich ausgewählt wird, bevor die nächste Anforderung gesendet wird. | 
| **Festes Intervall**  | Bei dieser Richtlinie wird für den Zeitraum des angegebenen Intervalls gewartet, bevor die nächste Anforderung gesendet wird. | 
| **Keine**  | Die Anforderung wird nicht erneut gesendet. | 
||| 

Informationen zu den Grenzwerten von Wiederholungsrichtlinien finden Sie unter [Logic Apps-Grenzwerte und -Konfiguration](../logic-apps/logic-apps-limits-and-config.md#request-limits). 

### <a name="change-retry-policy"></a>Ändern der Wiederholungsrichtlinie

Führen Sie diese Schritte aus, um eine andere Wiederholungsrichtlinie auszuwählen: 

1. Öffnen Sie Ihre Logik-App im Logik-App-Designer. 

2. Öffnen Sie die **Einstellungen** für eine Aktion oder einen Trigger.

3. Wenn die Aktion bzw. der Trigger Wiederholungsrichtlinien unterstützt, können Sie unter **Wiederholungsrichtlinie** den gewünschten Typ auswählen. 

Oder Sie können die Wiederholungsrichtlinie manuell im Abschnitt `inputs` für eine Aktion oder einen Trigger angeben, die bzw. der Wiederholungsrichtlinien unterstützt. Wenn Sie keine Wiederholungsrichtlinie angeben, wird für die Aktion die Standardrichtlinie verwendet.

```json
"<action-name>": {
   "type": "<action-type>", 
   "inputs": {
      "<action-specific-inputs>",
      "retryPolicy": {
         "type": "<retry-policy-type>",
         "interval": "<retry-interval>",
         "count": <retry-attempts>,
         "minimumInterval": "<minimum-interval>",
         "maximumInterval": "<maximun-interval>"
      },
      "<other-action-specific-inputs>"
   },
   "runAfter": {}
}
```

*Erforderlich*

| Wert | Type | BESCHREIBUNG |
|-------|------|-------------|
| <*retry-policy-type*> | String | Der Wiederholungsrichtlinientyp, den Sie verwenden möchten: `default`, `none`, `fixed` oder `exponential` | 
| <*retry-interval*> | String | Das Wiederholungsintervall, bei dem für den Wert das [ISO 8601-Format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) verwendet werden muss. Der niedrigste Wert für das Intervall ist `PT5S`, und der höchste Wert ist `PT1D`. Wenn Sie die Richtlinie mit dem exponentiellen Intervall verwenden, können Sie einen anderen Mindest- und Maximalwert angeben. | 
| <*retry-attempts*> | Integer | Die Anzahl der Wiederholungsversuche, die zwischen 1 und 90 liegen muss | 
||||

*Optional*

| Wert | Type | BESCHREIBUNG |
|-------|------|-------------|
| <*minimum-interval*> | String | Bei der Richtlinie mit dem exponentiellen Intervall ist dies der niedrigste Intervallwert für das zufällig ausgewählte Intervall im [ISO 8601-Format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). | 
| <*maximum-interval*> | String | Bei der Richtlinie mit dem exponentiellen Intervall ist dies der höchste Intervallwert für das zufällig ausgewählte Intervall im [ISO 8601-Format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). | 
|||| 

Hier sind weitere Informationen zu den unterschiedlichen Richtlinientypen angegeben.

<a name="default-retry"></a>

### <a name="default"></a>Standard

Wenn Sie keine Wiederholungsrichtlinie angeben, wird für die Aktion die Standardrichtlinie verwendet. Dies ist eine [Richtlinie für ein exponentielles Intervall](#exponential-interval), bei der bis zu vier Wiederholungsversuche in exponentiell zunehmenden Intervallen gesendet werden und eine Skalierung von 7,5 Sekunden verwendet wird. Das Intervall ist auf den Bereich zwischen 5 und 45 Sekunden beschränkt. 

Dies ist zwar nicht explizit in Ihrer Aktion oder Ihrem Trigger definiert, aber hier ist angegeben, wie sich die Standardrichtlinie in einer HTTP-Beispielaktion verhält:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "http://myAPIendpoint/api/action",
      "retryPolicy" : {
         "type": "exponential",
         "interval": "PT7S",
         "count": 4,
         "minimumInterval": "PT5S",
         "maximumInterval": "PT1H"
      }
   },
   "runAfter": {}
}
```

### <a name="none"></a>Keine

Um anzugeben, dass die Aktion bzw. der Trigger für fehlgeschlagene Anforderungen keine Wiederholungsversuche durchführen soll, legen Sie <*retry-policy-type*> auf `none` fest.

### <a name="fixed-interval"></a>Festes Intervall

Um anzugeben, dass die Aktion bzw. der Trigger für den Zeitraum des angegebenen Intervalls wartet, bevor die nächste Anforderung gesendet wird, legen Sie <*retry-policy-type*> auf `fixed` fest.

*Beispiel*

Bei dieser Wiederholungsrichtlinie wird zwei weitere Male versucht, die aktuellen Neuigkeiten abzurufen, nachdem die erste Anforderung fehlgeschlagen ist, und zwischen den Versuchen wird jeweils eine Verzögerung von 30 Sekunden verwendet:

```json
"Get_latest_news": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "https://mynews.example.com/latest",
      "retryPolicy": {
         "type": "fixed",
         "interval": "PT30S",
         "count": 2
      }
   }
}
```

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>Exponentielles Intervall

Um anzugeben, dass die Aktion bzw. der Trigger für den Zeitraum eines zufälligen Intervalls wartet, bevor die nächste Anforderung gesendet wird, legen Sie <*retry-policy-type*> auf `exponential` fest. Das zufällige Intervall wird aus einem exponentiell zunehmenden Bereich ausgewählt. Optional können Sie das niedrigste und höchste Standardintervall auch außer Kraft setzen, indem Sie Ihr eigenes niedrigstes und höchstes Intervall angeben.

**Bereiche der Zufallsvariablen**

Diese Tabelle veranschaulicht, wie Logic Apps eine einheitliche zufällige Variable im angegebenen Bereich für jeden Wiederholungsversuch generiert, und zwar bis zur festgelegten höchsten Wiederholungsanzahl (einschließlich):

| Wiederholungsanzahl | Minimales Intervall | Maximales Intervall |
|--------------|------------------|------------------|
| 1 | max(0, <*minimum-interval*>) | min(interval, <*maximum-interval*>) |
| 2 | max(interval, <*minimum-interval*>) | min(2 * interval, <*maximum-interval*>) |
| 3 | max(2 * interval, <*minimum-interval*>) | min(4 * interval, <*maximum-interval*>) |
| 4 | max(4 * interval, <*minimum-interval*>) | min(8 * interval, <*maximum-interval*>) |
| ... | ... | ... | 
|||| 

## <a name="catch-and-handle-failures-with-the-runafter-property"></a>Abfangen und Behandeln von Fehlern mit der RunAfter-Eigenschaft

Jede Logik-App-Aktion deklariert die Aktionen, die beendet werden müssen, bevor diese Aktion startet, ähnlich wie Sie die Reihenfolge der Schritte in Ihrem Workflow angeben. In einer Aktionsdefinition definiert die **runAfter**-Eigenschaft diese Reihenfolge und ist ein Objekt, mit dem beschrieben wird, von welchen Aktionen und Aktionsstatus die Aktion ausgeführt wird.

Standardmäßig ist für alle Aktionen, die im Logik-App-Designer hinzugefügt haben, die Ausführung nach dem vorhergehenden Schritt festgelegt, wenn das Ergebnis des vorhergehenden Schritts **Erfolgreich** ist. Sie können jedoch den **runAfter**-Wert so anpassen, dass Aktionen ausgelöst werden, wenn die vorherigen Aktionen zu dem Ergebnis **Fehlerhaft**, **Übersprungen** oder einer Kombination dieser Werte führen. Um z.B. ein Element einem bestimmten Service Bus-Thema hinzuzufügen, nachdem bei einer bestimmten **Insert_Row**-Aktion ein Fehler aufgetreten ist, könnten Sie diese **runAfter**-Beispieldefinition verwenden:

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ \"content-type\" : \"application/json\" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

Die Ausführung der **runAfter**-Eigenschaft wird für den Fall festgelegt, dass die **Insert_Row**-Aktion den Status **Fehlerhaft** hat. Um die Aktion auszuführen, wenn der Status der Aktion **Erfolgreich**, **Fehler** oder **Übersprungen** ist, verwenden Sie diese Syntax:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> Aktionen, die nach einem Fehler bei einer vorhergehenden Aktion ausgeführt und erfolgreich abgeschlossen werden, werden als **Erfolgreich** gekennzeichnet. Dies bedeutet Folgendes: Wenn das Abfangen aller Fehler eines Workflows erfolgreich ist, wird die gesamte Ausführung selbst als **Erfolgreich** gekennzeichnet.

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Auswerten von Aktionen mit Bereichen und ihren Ergebnissen

Ähnlich wie beim Ausführen von Schritten nach einzelnen Aktionen mit der **runAfter**-Eigenschaft können Sie Aktionen innerhalb eines [Bereichs](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) gruppieren. Sie können Bereiche verwenden, wenn Sie Aktionen logisch gruppieren, den Aggregatstatus des Bereichs feststellen und basierend auf diesem Status Aktionen ausführen möchten. Nachdem die Ausführung aller Aktionen in einem Bereich beendet ist, erhält der Bereich selbst seinen eigenen Status. 

Zum Überprüfen des Status eines Bereichs können Sie die gleichen Kriterien wie zum Überprüfen des Ausführungsstatus einer Logik-App verwenden, z.B. **Erfolgreich**, **Fehlerhaft** usw. 

Bei erfolgreicher Ausführung aller Aktionen des Bereichs wird der Status des Bereichs als **Erfolgreich** gekennzeichnet. Wenn die letzte Aktion in einem Bereich zu dem Ergebnis **Fehlerhaft** oder **Abgebrochen** führt, wird der Status des Bereichs als **Fehlerhaft** gekennzeichnet. 

Zum Abfangen von Ausnahmen in einem als **Fehlerhaft** gekennzeichneten Bereich und Ausführen von Aktionen, die diese Fehler behandeln, können Sie die **runAfter**-Eigenschaft für diesen **Fehlerhaften** Bereich verwenden. Auf diese Weise können Sie, wenn bei einer *beliebigen* Aktion im Bereich ein Fehler auftritt und Sie die **runAfter**-Eigenschaft für diesen Bereich verwenden, eine einzelne Aktion zum Abfangen von Fehlern erstellen.

Grenzwerte für Bereiche finden Sie unter [Grenzwerte und Konfiguration](../logic-apps/logic-apps-limits-and-config.md).

### <a name="get-context-and-results-for-failures"></a>Abrufen von Kontext und Ergebnissen auf Fehler

Das Abfangen der Fehler eines Bereichs ist nützlich. Aber häufig ist auch der Kontext hilfreich, um genau zu verstehen, für welche Aktionen Fehler aufgetreten sind und welche Fehler oder Statuscodes zurückgegeben wurden. Mit dem Ausdruck `@result()` wird Kontext zum Ergebnis aller Aktionen in einem Bereich bereitgestellt.

Der Ausdruck `@result()` nimmt einen einzelnen Parameter (Bereichsname) an und gibt ein Array mit den Ergebnissen aller Aktionen innerhalb des betreffenden Bereichs zurück. Diese Aktionsobjekte enthalten dieselben Attribute wie das  **\@actions()** -Objekt, z.B. Start- und Endzeit der Aktion, Status, Eingaben, Korrelations-IDs und Ausgaben. Sie können eine **\@result()** -Funktion problemlos mit einer **runAfter**-Eigenschaft koppeln, um Kontext zu allen Aktionen zu senden, für die im Bereich ein Fehler aufgetreten ist.

Zum Ausführen einer Aktion für jede Aktion eines Bereichs mit dem Ergebnis **Failed** und zum Filtern des Arrays der Ergebnisse bis hinab zu den fehlerhaften Aktionen können Sie **\@result()** mit der Aktion **[Filter Array](../connectors/connectors-native-query.md)** und einer [**For each**](../logic-apps/logic-apps-control-flow-loops.md)-Schleife koppeln. Sie können für das Array mit den gefilterten Ergebnissen eine Aktion für jeden Fehler durchführen, indem Sie die **For each**-Schleife verwenden. 

Hier ist ein Beispiel gefolgt von einer ausführlichen Erklärung angegeben, bei dem eine HTTP POST-Anforderung mit dem Antworttext für alle fehlerhaften Aktionen im Bereich „My_Scope“ gesendet wird:

```json
"Filter_array": {
   "type": "Query",
   "inputs": {
      "from": "@result('My_Scope')",
      "where": "@equals(item()['status'], 'Failed')"
   },
   "runAfter": {
      "My_Scope": [
         "Failed"
      ]
    }
},
"For_each": {
   "type": "foreach",
   "actions": {
      "Log_exception": {
         "type": "Http",
         "inputs": {
            "method": "POST",
            "body": "@item()['outputs']['body']",
            "headers": {
               "x-failed-action-name": "@item()['name']",
               "x-failed-tracking-id": "@item()['clientTrackingId']"
            },
            "uri": "http://requestb.in/"
         },
         "runAfter": {}
      }
   },
   "foreach": "@body('Filter_array')",
   "runAfter": {
      "Filter_array": [
         "Succeeded"
      ]
   }
}
```

In dieser ausführlichen exemplarischen Vorgehensweise wird beschrieben, was in diesem Beispiel geschieht:

1. Um das Ergebnis aller Aktionen in „My_Scope“ abzurufen, wird für die Aktion **Filter Array** dieser Filterausdruck verwendet: `@result('My_Scope')`

2. Die Bedingung für **Filter Array** ist ein beliebiges `@result()`-Element, das den Status **Failed** aufweist. Mit dieser Bedingung wird das Array mit allen Aktionsergebnissen aus „My_Scope“ bis hinab zu einem Array gefiltert, dass nur die fehlerhaften Aktionen als Ergebnisse enthält.

3. Führen Sie eine **For each**-Schleifenaktion für die Ausgaben *gefilterter Arrays* aus. In diesem Schritt wird eine Aktion für jedes Ergebnis einer fehlgeschlagenen Aktion durchgeführt, das zuvor gefiltert wurde.

   Wenn für eine einzelne Aktion im Bereich ein Fehler aufgetreten ist, werden die Aktionen in der **For each**-Schleife nur einmal ausgeführt. 
   Mehrere Aktionen mit Fehler lösen eine Aktion pro Fehler aus.

4. Senden Sie HTTP POST für den **For each**-Elementantworttext. Dies ist der Ausdruck `@item()['outputs']['body']`. 

   Die `@result()`-Elementform ist mit der `@actions()`-Form identisch und kann auch genauso analysiert werden.

5. Binden Sie zwei benutzerdefinierte Header mit dem Namen der fehlerhaften Aktion (`@item()['name']`) und der Clientnachverfolgungs-ID der fehlerhaften Ausführung (`@item()['clientTrackingId']`) ein.

Zu Referenzzwecken ist hier ein Beispiel für ein einzelnes `@result()`-Element angegeben, das die Eigenschaften **name**, **body** und **clientTrackingId** enthält, die im vorherigen Beispiel analysiert werden. Außerhalb einer **For each**-Aktion gibt `@result()` ein Array mit diesen Objekten zurück.

```json
{
   "name": "Example_Action_That_Failed",
   "inputs": {
      "uri": "https://myfailedaction.azurewebsites.net",
      "method": "POST"
   },
   "outputs": {
      "statusCode": 404,
      "headers": {
         "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
         "Server": "Microsoft-IIS/8.0",
         "X-Powered-By": "ASP.NET",
         "Content-Length": "68",
         "Content-Type": "application/json"
      },
      "body": {
         "code": "ResourceNotFound",
         "message": "/docs/folder-name/resource-name does not exist"
      }
   },
   "startTime": "2016-08-11T03:18:19.7755341Z",
   "endTime": "2016-08-11T03:18:20.2598835Z",
   "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
   "clientTrackingId": "08587307213861835591296330354",
   "code": "NotFound",
   "status": "Failed"
}
```

Sie können die oben in diesem Artikel beschriebenen Ausdrücke verwenden, um unterschiedliche Muster für die Ausnahmebehandlung zu nutzen. Außerdem können Sie die Entscheidung treffen, eine einzelne Aktion für die Ausnahmebehandlung außerhalb des Bereichs durchzuführen, bei der das gesamte gefilterte Array mit Fehlern akzeptiert wird, und die **For each**-Aktion zu entfernen. Darüber hinaus können Sie noch andere nützliche Eigenschaften aus der **\@result()** -Antwort einfügen, wie zuvor beschrieben.

## <a name="azure-diagnostics-and-metrics"></a>Azure-Diagnose und Metriken

Die oben beschriebenen Muster eignen sich gut zum Behandeln von Fehlern und Ausnahmen in einer Ausführung, aber Sie können auch unabhängig von der eigentlichen Ausführung Fehler identifizieren und darauf reagieren. 
[Azure-Diagnose](../logic-apps/logic-apps-monitor-your-logic-apps.md) ist eine einfache Möglichkeit zum Senden aller Workflowereignisse einschließlich aller Ausführungs- und Aktionsstatus an ein Azure Storage-Konto oder an einen mit Azure Event Hubs erstellten Event Hub. 

Sie können die Protokolle und Metriken überwachen oder mit einem von Ihnen bevorzugten Überwachungstool veröffentlichen, um jeweils den Ausführungsstatus auszuwerten. Eine mögliche Option ist das Streamen aller Ereignisse durch Event Hubs nach [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). In Stream Analytics können Sie Liveabfragen auf der Grundlage von Anomalien, Mittelwerten oder Fehlern aus den Diagnoseprotokollen schreiben. Sie können Stream Analytics zum Senden von Informationen an andere Datenquellen verwenden, etwa an Warteschlangen, Themen, SQL, Azure Cosmos DB oder Power BI.

## <a name="next-steps"></a>Nächste Schritte

* [Szenario: Ausnahmebehandlung und Fehlerprotokollierung für Logik-Apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Beispiele und häufige Szenarios für Logik-Apps](../logic-apps/logic-apps-examples-and-scenarios.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
