---
title: Behandeln von Fehlern und Ausnahmen in Workflows
description: Erfahren Sie, wie Sie Fehler und Ausnahmen behandeln können, die in automatisierten Tasks und Workflows auftreten, die mithilfe von Azure Logic Apps erstellt wurden.
services: logic-apps
ms.suite: integration
author: dereklee
ms.author: deli
ms.reviewer: klam, estfan, logicappspm
ms.date: 01/11/2020
ms.topic: article
ms.openlocfilehash: d4bff4ee7980002d911426ed46ffef6fc28c43e9
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96920745"
---
# <a name="handle-errors-and-exceptions-in-azure-logic-apps"></a>Behandeln von Fehlern und Ausnahmen in Azure Logic Apps

Die Art und Weise, wie eine Integrationsarchitektur Ausfallzeiten oder Probleme aufgrund von abhängigen Systemen behandelt, kann eine Herausforderung darstellen. Damit Sie stabile und robuste Integrationen erstellen können, bei denen Probleme und Fehler korrekt behandelt werden, verfügt Logic Apps über eine professionelle Benutzeroberfläche für den Umgang mit Fehlern und Ausnahmen.

<a name="retry-policies"></a>

## <a name="retry-policies"></a>Wiederholungsrichtlinien

Für die grundlegende Behandlung von Ausnahmen und Fehlern können Sie eine *Wiederholungsrichtlinie* in allen Aktionen oder Triggern verwenden, sofern dies unterstützt wird, z. B. in der [HTTP-Aktion](../logic-apps/logic-apps-workflow-actions-triggers.md#http-trigger). In einer Wiederholungsrichtlinie wird angegeben, ob und wie die Aktion bzw. der Trigger versucht, eine Anforderung zu wiederholen, wenn es für die ursprüngliche Anforderung zu einem Timeout oder einem Ausfall kommt. Dies sind alle Anforderungen, die zu Antworten vom Typ 408, 429 oder 5xx führen. Wenn keine andere Wiederholungsrichtlinie verwendet wird, wird die Standardrichtlinie genutzt.

Hier sind die Arten von Wiederholungsrichtlinien angegeben:

| type | BESCHREIBUNG |
|------|-------------|
| **Standard** | Bei dieser Richtlinie werden bis zu vier Wiederholungen in Intervallen durchgeführt, die sich *exponentiell erhöhen*. Sie werden um 7,5 Sekunden skaliert, aber der obere Grenzwert liegt zwischen 5 und 45 Sekunden. |
| **Exponentielles Intervall**  | Bei dieser Richtlinie wird für den Zeitraum eines zufälligen Intervalls gewartet, das aus einem exponentiell zunehmenden Bereich ausgewählt wird, bevor die nächste Anforderung gesendet wird. |
| **Festes Intervall**  | Bei dieser Richtlinie wird für den Zeitraum des angegebenen Intervalls gewartet, bevor die nächste Anforderung gesendet wird. |
| **None**  | Die Anforderung wird nicht erneut gesendet. |
|||

Informationen zu den Grenzwerten von Wiederholungsrichtlinien finden Sie unter [Logic Apps-Grenzwerte und -Konfiguration](../logic-apps/logic-apps-limits-and-config.md#http-limits).

### <a name="change-retry-policy"></a>Ändern der Wiederholungsrichtlinie

Führen Sie diese Schritte aus, um eine andere Wiederholungsrichtlinie auszuwählen:

1. Öffnen Sie Ihre Logik-App im Logik-App-Designer.

1. Öffnen Sie die **Einstellungen** für eine Aktion oder einen Trigger.

1. Wenn die Aktion bzw. der Trigger Wiederholungsrichtlinien unterstützt, können Sie unter **Wiederholungsrichtlinie** den gewünschten Typ auswählen.

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
         "maximumInterval": "<maximum-interval>"
      },
      "<other-action-specific-inputs>"
   },
   "runAfter": {}
}
```

*Erforderlich*

| Wert | type | BESCHREIBUNG |
|-------|------|-------------|
| <*retry-policy-type*> | String | Der Wiederholungsrichtlinientyp, den Sie verwenden möchten: `default`, `none`, `fixed` oder `exponential` |
| <*retry-interval*> | String | Das Wiederholungsintervall, bei dem für den Wert das [ISO 8601-Format](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) verwendet werden muss. Der niedrigste Wert für das Intervall ist `PT5S`, und der höchste Wert ist `PT1D`. Wenn Sie die Richtlinie mit dem exponentiellen Intervall verwenden, können Sie einen anderen Mindest- und Maximalwert angeben. |
| <*retry-attempts*> | Integer | Die Anzahl der Wiederholungsversuche, die zwischen 1 und 90 liegen muss |
||||

*Optional*

| Wert | type | BESCHREIBUNG |
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

<a name="control-run-after-behavior"></a>

## <a name="catch-and-handle-failures-by-changing-run-after-behavior"></a>Erfassen und Behandeln von Fehlern durch Ändern des Verhaltens „Ausführen nach“

Wenn Sie im Logik-App-Designer Aktionen hinzufügen, deklarieren Sie implizit die Reihenfolge, in der diese Aktionen ausgeführt werden sollen. Sobald die Ausführung einer Aktion abgeschlossen ist, erhält die Aktion einen Status wie `Succeeded`, `Failed`, `Skipped` oder `TimedOut`. In jeder Aktionsdefinition wird über die `runAfter`-Eigenschaft festgelegt, welche vorherige Aktion abgeschlossen sein muss, und wie ihr Status lauten muss, bevor die anschließende Aktion ausgeführt werden darf. Standardmäßig wird eine Aktion, die Sie im Designer hinzufügen, nur ausgeführt, wenn die vorherige Aktion mit dem Status `Succeeded` abgeschlossen ist.

Wenn eine Aktion einen nicht behandelten Fehler oder eine nicht behandelte Ausnahme auslöst, wird die Aktion als `Failed` markiert, und alle darauffolgenden Aktionen mit `Skipped`. Wenn dieses Verhalten bei einer Aktion mit parallelen Branches auftritt, folgt die Logic Apps-Engine den anderen Branches, um deren Abschlussstatus zu ermitteln. Wenn ein Branch beispielsweise mit einer `Skipped`-Aktion endet, hängt der Abschlussstatus dieses Branch vom Status der Aktion ab, die vor dieser übersprungenen Aktion ausgeführt wurde. Nachdem die Ausführung der Logik-App abgeschlossen ist, ermittelt die Engine den Status der gesamten Ausführung, indem die Status aller Branches ausgewertet werden. Wenn irgendein Branch zu einem Fehler führt, wird die gesamte Ausführung der Logik-App als `Failed` markiert.

![Beispiele, die zeigen, wie die Status von Ausführungen ausgewertet werden](./media/logic-apps-exception-handling/status-evaluation-for-parallel-branches.png)

[Passen Sie das Verhalten „Ausführen nach“ einer Aktion an](#customize-run-after), um mit den Status über Misserfolge zuvor ausgeführter Aktionen umgehen zu können, und so sicherzustellen, dass eine Aktion unabhängig vom Status ihres Vorgängers ausgeführt werden kann.

<a name="customize-run-after"></a>

### <a name="customize-run-after-behavior"></a>Anpassen des Verhaltens „Ausführen nach“

Sie können das Verhalten „Ausführen nach“ einer Aktion anpassen, sodass die Aktion ausgeführt wird, wenn einer der folgenden Status vorliegt: `Succeeded`, `Failed`, `Skipped` oder `TimedOut`. Wenn beispielsweise eine E-Mail gesendet werden soll, nachdem die Vorgängeraktion `Add_a_row_into_a_table` in Excel Online nicht als `Succeeded` sondern als `Failed` markiert wurde, ändern Sie das Verhalten „Ausführen nach“, indem Sie die folgenden Schritte ausführen:

* Wählen Sie in der Designansicht die Schaltfläche mit Auslassungspunkten ( **…** ) aus, und wählen Sie dann die Option **"Ausführen nach" konfigurieren** aus.

  ![Konfigurieren des Verhaltens „Ausführen nach“ für eine Aktion](./media/logic-apps-exception-handling/configure-run-after-property-setting.png)

  Im folgenden Ablaufplan für die Aktion sehen Sie den Standardstatus, der für die vorherige Aktion erforderlich ist. In diesem Beispiel ist das **Zeile zu Tabelle hinzufügen**:

  ![Standardverhalten von „Ausführen nach“ für eine Aktion](./media/logic-apps-exception-handling/change-run-after-property-status.png)

  Ändern Sie das Verhalten für „Ausführen nach“ in einen beliebigen Status. In diesem Beispiel wurde es in **ist fehlerhaft** geändert:

  ![Ändern des Verhaltens von „Ausführen nach“ in „ist fehlerhaft“](./media/logic-apps-exception-handling/run-after-property-status-set-to-failed.png)

  Wählen Sie einen der anderen Status aus, wenn Sie angeben möchten, dass die Aktion ausgeführt wird, wenn die Vorgängeraktion als `Failed`, `Skipped` oder `TimedOut` markiert ist.

  ![Ändern des Verhaltens von „Ausführen nach“, sodass einer der anderen Status vorliegen kann](./media/logic-apps-exception-handling/run-after-property-multiple-statuses.png)

* Bearbeiten Sie in der Codeansicht in der JSON-Definition der Aktion die Eigenschaft `runAfter`, die folgende Syntax aufweist:

  ```json
  "<action-name>": {
     "inputs": {
        "<action-specific-inputs>"
     },
     "runAfter": {
        "<preceding-action>": [
           "Succeeded"
        ]
     },
     "type": "<action-type>"
  }
  ```

  Ändern Sie für dieses Beispiel die `runAfter`-Eigenschaft von `Succeeded` in `Failed`:

  ```json
  "Send_an_email_(V2)": {
     "inputs": {
        "body": {
           "Body": "<p>Failed to&nbsp;add row to &nbsp;@{body('Add_a_row_into_a_table')?['Terms']}</p>",,
           "Subject": "Add row to table failed: @{body('Add_a_row_into_a_table')?['Terms']}",
           "To": "Sophia.Owen@fabrikam.com"
        },
        "host": {
           "connection": {
              "name": "@parameters('$connections')['office365']['connectionId']"
           }
        },
        "method": "post",
        "path": "/v2/Mail"
     },
     "runAfter": {
        "Add_a_row_into_a_table": [
           "Failed"
        ]
     },
     "type": "ApiConnection"
  }
  ```

  Wenn Sie angeben möchten, dass die Aktion ausgeführt wird, wenn die Vorgängeraktion als `Failed`, `Skipped` oder `TimedOut` markiert ist, fügen Sie die anderen Status hinzu:

  ```json
  "runAfter": {
     "Add_a_row_into_a_table": [
        "Failed", "Skipped", "TimedOut"
     ]
  },
  ```

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Auswerten von Aktionen mit Bereichen und ihren Ergebnissen

Ähnlich wie beim Ausführen von Schritten nach einzelnen Aktionen mit der `runAfter`-Eigenschaft können Sie Aktionen innerhalb eines [Bereichs](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md) gruppieren. Sie können Bereiche verwenden, wenn Sie Aktionen logisch gruppieren, den Aggregatstatus des Bereichs feststellen und basierend auf diesem Status Aktionen ausführen möchten. Nachdem die Ausführung aller Aktionen in einem Bereich beendet ist, erhält der Bereich selbst seinen eigenen Status.

Zum Überprüfen des Status eines Bereichs können Sie die gleichen Kriterien wie zum Überprüfen des Ausführungsstatus einer Logik-App verwenden, z. B. `Succeeded` oder `Failed`.

Bei erfolgreicher Ausführung aller Aktionen des Bereichs wird der Status des Bereichs als `Succeeded` gekennzeichnet. Wenn die letzte Aktion in einem Bereich zu dem Ergebnis `Failed` oder `Aborted` führt, wird der Status des Bereichs als `Failed` gekennzeichnet.

Zum Abfangen von Ausnahmen in einem als `Failed` gekennzeichneten Bereich und zum Ausführen von Aktionen, die diese Fehler behandeln, können Sie die `runAfter`-Eigenschaft für diesen `Failed`-Bereich verwenden. Auf diese Weise können Sie, wenn bei einer *beliebigen* Aktion im Bereich ein Fehler auftritt und Sie die `runAfter`-Eigenschaft für diesen Bereich verwenden, eine einzelne Aktion zum Abfangen von Fehlern erstellen.

Grenzwerte für Bereiche finden Sie unter [Grenzwerte und Konfiguration](../logic-apps/logic-apps-limits-and-config.md).

<a name="get-results-from-failures"></a>

### <a name="get-context-and-results-for-failures"></a>Abrufen von Kontext und Ergebnissen auf Fehler

Das Abfangen der Fehler eines Bereichs ist nützlich. Aber häufig ist auch der Kontext hilfreich, um genau zu verstehen, für welche Aktionen Fehler aufgetreten sind und welche Fehler oder Statuscodes zurückgegeben wurden.

Die [`result()`](../logic-apps/workflow-definition-language-functions-reference.md#result)-Funktion stellt Kontext zu den Ergebnissen aller Aktionen in einem Bereich zur Verfügung. Die `result()`-Funktion erwartet als einzigen Parameter den Namen des Bereichs und gibt ein Array zurück, das alle Aktionsergebnisse aus diesem Bereich enthält. Diese Aktionsobjekte enthalten dieselben Attribute wie das `actions()`-Objekt, z.B. Start- und Endzeit der Aktion, Status, Eingaben, Korrelations-IDs und Ausgaben. Sie können einen `@result()`-Ausdruck einfach mit der `runAfter`-Eigenschaft koppeln, um Kontext zu allen Aktionen zu senden, für die in einem Bereich ein Fehler aufgetreten ist.

Wenn Sie eine Aktion für jede in einem Bereich befindliche Aktion ausführen, die das Ergebnis `Failed` hat, und wenn Sie das Array der Ergebnisse bis hinab zu den fehlgeschlagenen Aktionen filtern möchten, können Sie einen `@result()`-Ausdruck mit einer [**Array filtern**](logic-apps-perform-data-operations.md#filter-array-action)-Aktion und einer [**For each**](../logic-apps/logic-apps-control-flow-loops.md)-Schleife koppeln. Sie können für das Array mit den gefilterten Ergebnissen eine Aktion für jeden Fehler durchführen, indem Sie die `For_each`-Schleife verwenden.

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

1. Die Bedingung für **Array filtern** ist ein beliebiges `@result()`-Element, dessen Status `Failed` lautet. Mit dieser Bedingung wird das Array mit allen Aktionsergebnissen aus „My_Scope“ bis hinab zu einem Array gefiltert, dass nur die fehlerhaften Aktionen als Ergebnisse enthält.

1. Führen Sie eine `For_each`-Schleifenaktion für die Ausgaben *gefilterter Arrays* aus. In diesem Schritt wird eine Aktion für jedes Ergebnis einer fehlgeschlagenen Aktion durchgeführt, das zuvor gefiltert wurde.

   Wenn eine einzelne Aktion im Bereich fehlgeschlagen ist, werden die Aktionen in der `For_each`-Schleife nur einmal ausgeführt. Mehrere Aktionen mit Fehler lösen eine Aktion pro Fehler aus.

1. Senden Sie HTTP POST für den `For_each`-Elementantworttext. Dies ist der Ausdruck `@item()['outputs']['body']`.

   Die `@result()`-Elementform ist mit der `@actions()`-Form identisch und kann auch genauso analysiert werden.

1. Binden Sie zwei benutzerdefinierte Header mit dem Namen der fehlerhaften Aktion (`@item()['name']`) und der Clientnachverfolgungs-ID der fehlerhaften Ausführung (`@item()['clientTrackingId']`) ein.

Zu Referenzzwecken ist hier ein Beispiel für ein einzelnes `@result()`-Element angegeben, das die Eigenschaften `name`, `body` und `clientTrackingId` enthält, die im vorherigen Beispiel analysiert werden. Außerhalb einer `For_each`-Aktion, gibt `@result()` ein Array dieser Objekte zurück.

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

Sie können die oben in diesem Artikel beschriebenen Ausdrücke verwenden, um unterschiedliche Muster für die Ausnahmebehandlung zu nutzen. Außerdem können Sie die Entscheidung treffen, eine einzelne Aktion für die Ausnahmebehandlung außerhalb des Bereichs durchzuführen, bei der das gesamte gefilterte Array mit Fehlern akzeptiert wird, und die `For_each`-Aktion zu entfernen. Darüber hinaus können Sie noch andere nützliche Eigenschaften aus der `\@result()`-Antwort einfügen, wie zuvor beschrieben.

## <a name="set-up-azure-monitor-logs"></a>Einrichten von Azure Monitor-Protokollen

Die oben beschriebenen Muster eignen sich gut zum Behandeln von Fehlern und Ausnahmen in einer Ausführung, aber Sie können auch unabhängig von der eigentlichen Ausführung Fehler identifizieren und darauf reagieren. [Azure Monitor](../azure-monitor/overview.md) bietet eine einfache Möglichkeit zum Senden aller Workflowereignisse (einschließlich aller Ausführungs- und Aktionsstatus) an einen [Log Analytics-Arbeitsbereich](../azure-monitor/platform/data-platform-logs.md), ein [Azure-Speicherkonto](../storage/blobs/storage-blobs-overview.md) oder an [Azure Event Hubs](../event-hubs/event-hubs-about.md).

Sie können die Protokolle und Metriken überwachen oder mit einem von Ihnen bevorzugten Überwachungstool veröffentlichen, um jeweils den Ausführungsstatus auszuwerten. Eine mögliche Option ist das Streamen aller Ereignisse durch Event Hubs nach [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). In Stream Analytics können Sie Liveabfragen auf der Grundlage von Anomalien, Mittelwerten oder Fehlern aus den Diagnoseprotokollen schreiben. Sie können Stream Analytics zum Senden von Informationen an andere Datenquellen verwenden, etwa an Warteschlangen, Themen, SQL, Azure Cosmos DB oder Power BI.

## <a name="next-steps"></a>Nächste Schritte

* [Szenario: Ausnahmebehandlung und Fehlerprotokollierung für Logik-Apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Beispiele und häufige Szenarios für Logik-Apps](../logic-apps/logic-apps-examples-and-scenarios.md)
