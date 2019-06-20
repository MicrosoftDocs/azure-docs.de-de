---
title: Hinzufügen von Bereichen für die Ausführung von Aktionen auf der Grundlage des Gruppenstatus – Azure Logic Apps | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie in Azure Logic Apps Bereiche erstellen, die Workflowaktionen auf der Grundlage des Gruppenstatus ausführen.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.reviewer: klam, LADocs
ms.date: 10/03/2018
ms.topic: article
ms.openlocfilehash: 48fb2d14cd4cf99510fff88b25b9ae45814a92a8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60685406"
---
# <a name="run-actions-based-on-group-status-with-scopes-in-azure-logic-apps"></a>Ausführen von Aktionen basierend auf dem Gruppenstatus mit Bereichen in Azure Logic Apps

Wenn Aktionen nur ausgeführt werden sollen, nachdem eine andere Gruppe von Aktionen erfolgreich oder nicht erfolgreich war, gruppieren Sie diese Aktionen in einem *Bereich*. Diese Struktur ist hilfreich, wenn Sie Aktionen als logische Gruppe organisieren, den Status dieser Gruppe auswerten und Aktionen durchführen möchten, die auf dem Status des Bereichs basieren. Nachdem die Ausführung aller Aktionen in einem Bereich beendet ist, erhält der Bereich auch einen eigenen Status. Beispielsweise können Sie Bereiche verwenden, wenn Sie die [Ausnahme- und Fehlerbehandlung](../logic-apps/logic-apps-exception-handling.md#scopes) implementieren möchten. 

Zum Überprüfen des Status eines Bereichs können Sie die gleichen Kriterien wie zum Bestimmen des Ausführungsstatus einer Logik-App verwenden, z.B. „Erfolgreich“, „Fehlerhaft“, „Abgebrochen“ usw. Bei erfolgreicher Ausführung aller Aktionen des Bereichs wird der Status des Bereichs als „Erfolgreich“ festgelegt. Wenn jedoch eine Aktion im Bereich fehlschlägt oder abgebrochen wird, erhält der Bereich den Status „Fehlerhaft“. Grenzwerte für Bereiche finden Sie unter [Grenzwerte und Konfiguration](../logic-apps/logic-apps-limits-and-config.md). 

Hier sehen Sie beispielsweise eine allgemeine Logik-App, die einen Bereich zum Ausführen bestimmter Aktionen und eine Bedingung zum Überprüfen des Bereichsstatus verwendet. Wenn Aktionen im Bereich fehlschlagen oder unerwartet beendet werden, wird der Bereich mit „Fehlerhaft“ bzw. „Abgebrochen“ gekennzeichnet, und die Logik-App sendet eine Nachricht, dass der Bereich fehlerhaft ist. Wenn alle Aktionen im Bereich erfolgreich sind, sendet die Logik-App ebenfalls eine entsprechende Nachricht.

![Einrichten des Auslösers „Zeitplan: Wiederholung“](./media/logic-apps-control-flow-run-steps-group-scopes/scope-high-level.png)

## <a name="prerequisites"></a>Voraussetzungen

Um dem Beispiel in diesem Artikel zu folgen, benötigen Sie diese Elemente:

* Ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/). 

* Ein E-Mail-Konto bei einem von Logic Apps unterstützten E-Mail-Anbieter. In diesem Beispiel wird Outlook.com verwendet. Wenn Sie einen anderen Anbieter verwenden, bleibt der allgemeine Ablauf gleich, das Erscheinungsbild der Benutzeroberfläche weicht jedoch ab.

* Ein Bing Karten-Schlüssel. Informationen zum Abrufen eines Bing Karten-Schlüssels finden Sie <a href="https://msdn.microsoft.com/library/ff428642.aspx" target="_blank">hier</a>.

* Grundlegende Kenntnisse über die [Erstellung von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-sample-logic-app"></a>Erstellen einer Beispiel-Logik-App

Erstellen Sie zunächst diese Beispiel-Logik-App, damit Sie später einen Bereich hinzufügen können:

![Erstellen einer Beispiel-Logik-App](./media/logic-apps-control-flow-run-steps-group-scopes/finished-sample-app.png)

* Ein Auslöser vom Typ **„Zeitplan: Wiederholung“** der den Bing Karten-Dienst in einem von Ihnen festgelegten Intervall überprüft
* Die Aktion **Bing Maps - Get-Route** (Bing Karten – Route ermitteln), die die Reisezeit zwischen zwei Standorten überprüft
* Eine Bedingungsanweisung, die überprüft, ob die Reisezeit Ihre angegebene Reisezeit überschreitet
* Eine Aktion, die Ihnen eine E-Mail sendet, wenn die aktuelle Reisezeit Ihre angegebene Zeit überschreitet

Sie können Ihre Logik-App jederzeit speichern, machen Sie also häufig Gebrauch davon.

1. Melden Sie sich beim <a href="https://portal.azure.com" target="_blank">Azure-Portal</a> an, falls Sie dies noch nicht getan haben. Erstellen einer leeren Logik-App

1. Fügen Sie den Auslöser **Zeitplan – Wiederholung** mit folgenden Einstellungen hinzu: **Intervall** = "1" und **Häufigkeit** = "Minute"

   ![Einrichten des Auslösers „Zeitplan: Wiederholung“](./media/logic-apps-control-flow-run-steps-group-scopes/recurrence.png)

   > [!TIP]
   > Zum visuellen Vereinfachen der Ansicht und zum Ausblenden von Details der einzelnen Aktionen im Designer reduzieren Sie die Form jeder Aktion, während Sie diese Schritte nacheinander ausführen.

1. Fügen Sie die Aktion **Bing Maps - Get route** (Bing Karten– Route ermitteln) hinzu. 

   1. Falls noch keine Verbindung mit Bing Karten besteht, werden Sie aufgefordert, eine Verbindung herzustellen.

      | Einstellung | Wert | BESCHREIBUNG |
      | ------- | ----- | ----------- |
      | **Verbindungsname** | BingMapsConnection | Geben Sie einen Namen für die Verbindung an. | 
      | **API-Schlüssel** | <*Ihr Bing Maps-Schlüssel*> | Geben Sie den Bing Maps-Schlüssel ein, den Sie zuvor abgerufen haben. | 
      ||||  

   1. Richten Sie die Aktion **Route ermitteln** wie in der Tabelle unter dieser Grafik gezeigt ein:

      ![Einrichten der Aktion „Bing Karten – Route ermitteln“](./media/logic-apps-control-flow-run-steps-group-scopes/get-route.png) 

      Weitere Informationen zu diesen Parametern finden Sie unter [Calculate a route](https://msdn.microsoft.com/library/ff701717.aspx) (Berechnen einer Route).

      | Einstellung | Wert | BESCHREIBUNG |
      | ------- | ----- | ----------- |
      | **Wegpunkt 1** | <*Start*> | Geben Sie den Ausgangspunkt Ihrer Route ein. | 
      | **Wegpunkt 2** | <*Ende*> | Geben Sie das Ziel Ihrer Route ein. | 
      | **Vermeiden** | Keine | Geben Sie Elemente ein, die auf der Route vermieden werden sollen, etwa Autobahnen, Mautgebühren usw. Mögliche Werte finden Sie im Artikel zum [Berechnen einer Route](https://msdn.microsoft.com/library/ff701717.aspx). | 
      | **Optimieren** | timeWithTraffic | Wählen Sie einen Parameter zur Optimierung der Route aus, z.B. Entfernung, Reisezeit basierend auf der aktuellen Verkehrslage, usw. In diesem Beispiel wird der „timeWithTraffic“ verwendet. | 
      | **Distance unit** (Einheit für Entfernung) | <*Ihre Präferenz*> | Geben Sie die Einheit der Entfernung ein, um die Route zu berechnen. In diesem Beispiel wird folgender Wert verwendet: "Mile" | 
      | **Travel mode** (Reisemodus) | Driving (Auto) | Geben Sie das Fortbewegungsmittel für die Route ein. In diesem Beispiel wird „Driving“ (Auto) verwendet. | 
      | **Transit Date-Time** (Datum und Uhrzeit für Transit) | Keine | Gilt für nur für den Transitmodus. | 
      | **Transit Date-Time Type** (Typ für Datum und Uhrzeit für Transit) | Keine | Gilt für nur für den Transitmodus. | 
      ||||  

1. [Fügen Sie eine Bedingung hinzu](../logic-apps/logic-apps-control-flow-conditional-statement.md), die überprüft, ob die aktuelle Reisezeit mit Verkehr eine angegebene Zeit überschreitet. 
   Führen Sie für dieses Beispiel die folgenden Schritte aus:

   1. Benennen Sie die Bedingung mit dieser Beschreibung um: **Wenn die Verkehrszeit die angegebene Zeit überschreitet**

   1. Klicken Sie in der linken Spalte in das Feld **Wert auswählen**, damit die dynamische Inhaltsliste angezeigt wird. Wählen Sie in der Liste das Feld **Travel Duration Traffic** (Reisedauer (Verkehr)) aus, das in Sekunden angegeben wird. 

      ![Erstellen der Bedingung](./media/logic-apps-control-flow-run-steps-group-scopes/build-condition.png)

   1. Wählen Sie im mittleren Feld den folgenden Operator aus: **ist größer als**.

   1. Geben Sie in der rechten Spalte diesen Vergleichswert ein, der in Sekunden angegeben ist und 10 Minuten entspricht: **600**

      Danach sieht Ihre Bedingung wie im folgenden Beispiel aus:

      ![Abgeschlossene Bedingung](./media/logic-apps-control-flow-run-steps-group-scopes/finished-condition.png)

1. Im Branch **If true** (Bei „true“) fügen Sie eine Aktion zum Senden einer E-Mail für Ihren E-Mail-Anbieter hinzu. 
   Richten Sie diese Aktion ein, indem Sie den Schritten unter dieser Abbildung folgen:

   ![Hinzufügen der Aktion „Senden einer E-Mail“ zur Verzweigung „If true“ (Bei „true“)](./media/logic-apps-control-flow-run-steps-group-scopes/send-email.png)

   1. Geben Sie im Feld **An** Ihre E-Mail-Adresse für Testzwecke an.

   1. Geben Sie im Feld **Betreff** diesen Text ein:

      ```Time to leave: Traffic more than 10 minutes```

   1. Geben Sie im Feld **Text** diesen Text mit einem nachgestellten Leerzeichen ein: 

      ```Travel time:```

      Während der Cursor im Feld **Text** angezeigt wird, bleibt die Liste mit den dynamischen Inhalten geöffnet, damit Sie alle Parameter auswählen können, die an diesem Punkt verfügbar sind.

   1. Wenn die Liste mit den dynamischen Inhalten erscheint, wählen Sie **Ausdruck** aus.

   1. Suchen Sie die Funktion **div()** , und wählen Sie sie aus. 
      Setzen Sie den Cursor zwischen die Klammern der Funktion.

   1. Während sich der Cursor innerhalb der Klammern der befindet, wählen Sie **Dynamischer Inhalt** aus, damit die dynamische Inhaltsliste angezeigt wird. 
   
   1. Wählen Sie im Abschnitt **Route abrufen** das Feld **Traffic Duration Traffic** (Reisedauer (Verkehr)) aus.

      ![Auswählen von „Travel Duration Traffic“ (Reisedauer (Verkehr))](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-2.png)

   1. Nachdem das Feld in das JSON-Format aufgelöst wurde, fügen Sie ein **Komma** (```,```) gefolgt von der Zahl ```60``` hinzu, damit Sie den Wert **Traffic Duration Traffic** (Reisedauer (Verkehr)) von Sekunden in Minuten umwandeln können. 
   
      ```
      div(body('Get_route')?['travelDurationTraffic'],60)
      ```

      Nun sieht Ihr Ausdruck wie in diesem Beispiel aus:

      ![Fertigstellen des Ausdrucks](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-3.png)  

   1. Wählen Sie **OK** aus, wenn Sie fertig sind.

   <!-- markdownlint-disable MD038 -->
   1. Nachdem der Ausdruck aufgelöst wurde, fügen Sie diesen Text mit einem vorangestellten Leerzeichen hinzu: ``` minutes```
  
       Nun sieht Ihr **Textfeld** wie in diesem Beispiel aus:

       ![Fertiggestelltes Feld „Text“](./media/logic-apps-control-flow-run-steps-group-scopes/send-email-4.png)
   <!-- markdownlint-enable MD038 -->

1. Speichern Sie Ihre Logik-App.

Fügen Sie als nächstes einen Bereich hinzu, damit Sie bestimmte Aktionen gruppieren und ihren Status auswerten können.

## <a name="add-a-scope"></a>Hinzufügen eines Bereichs

1. Öffnen Sie Ihre Logik-App im Azure-Portal im Logik-App-Designer, sofern Sie dies noch nicht getan haben. 

1. Fügen im gewünschten Workflow-Speicherort einen Bereich hinzu. Um z. B. einen Bereich zwischen bestehenden Schritten im Workflow der Logik-App hinzuzufügen, führen Sie diese Schritte aus: 

   1. Bewegen Sie den Mauszeiger über den Pfeil, wo Sie den Bereich hinzufügen möchten. 
   Wählen Sie das **Pluszeichen** ( **+** ) > **Aktion hinzufügen** aus.

      ![Hinzufügen eines Bereichs](./media/logic-apps-control-flow-run-steps-group-scopes/add-scope.png)

   1. Geben Sie im Suchfeld den Begriff „Bereich“ als Filter ein. 
   Wählen Sie die Aktion **Bereich** aus.

## <a name="add-steps-to-scope"></a>Hinzufügen von Schritten zum Bereich

1. Fügen Sie jetzt die Schritte hinzu, oder ziehen Sie vorhandene Schritte, die Sie ausführen möchten, in den Bereich. Ziehen Sie für dieses Beispiel diese Aktionen in den Bereich:
      
   * **Get route** (Route ermitteln)
   * **If traffic time is more than specified time** (Wenn Verkehrszeit die angegebene Zeit überschreitet); enthält die beiden Branches **true** und **false**

   Nun sieht Ihre Logik-App wie dieses Beispiel aus:

   ![Bereich hinzugefügt](./media/logic-apps-control-flow-run-steps-group-scopes/scope-added.png)

1. Fügen Sie unter dem Bereich eine Bedingung hinzu, die den Status des Bereichs prüft. Benennen Sie die Bedingung mit dieser Beschreibung um: **Wenn im Bereich ein Fehler auftritt**

   ![Hinzufügen einer Bedingung zum Überprüfen des Bereichsstatus](./media/logic-apps-control-flow-run-steps-group-scopes/add-condition-check-scope-status.png)
  
1. Fügen Sie in der Bedingung diese Ausdrücke hinzu, die prüfen, ob der Status des Bereichs gleich „Fehler“ oder „Abgebrochen“ ist. 

   1. Um eine weitere Zeile hinzuzufügen, wählen Sie **Hinzufügen** aus. 

   1. Klicken Sie in den einzelnen Zeilen in das linke Feld, damit die Liste der dynamischen Inhalte angezeigt wird. 
   Wählen Sie in der Liste der dynamischen Inhalte die Option **Ausdruck** aus. Geben Sie im Bearbeitungsfeld diesen Ausdruck ein, und wählen Sie dann **OK** aus: 
   
      `result('Scope')[0]['status']`

      ![Hinzufügen eines Ausdrucks, der den Bereichsstatus prüft](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status.png)

   1. Wählen Sie für beide Zeilen **ist gleich** als Operator aus. 
   
   1. Geben Sie für die Vergleichswerte in der ersten Zeile `Failed` ein. 
   Geben Sie in der zweiten Zeile `Aborted` ein. 

      Danach sieht Ihre Bedingung wie im folgenden Beispiel aus:

      ![Hinzufügen eines Ausdrucks, der den Bereichsstatus prüft](./media/logic-apps-control-flow-run-steps-group-scopes/check-scope-status-finished.png)

      Legen Sie jetzt die Eigenschaft `runAfter` der Bedingung so fest, dass die Bedingung den Bereichsstatus überprüft und die passende Aktion ausführt, die Sie in späteren Schritten definieren.

   1. Wählen Sie für die Bedingung **If scope failed** (Bei fehlerhaftem Bereich) die Schaltfläche mit den **Auslassungspunkten** (...) und dann **Ausführung konfigurieren nach** aus.

      ![Konfigurieren der Eigenschaft „runAfter“](./media/logic-apps-control-flow-run-steps-group-scopes/configure-run-after.png)

   1. Wählen Sie alle genannten Bereichsstatus aus: **is successful** (ist erfolgreich), **has failed** (ist fehlerhaft), **is skipped** (wurde übersprungen) und **has timed out** (hat ein Timeout verursacht).

      ![Auswählen des Bereichsstatus](./media/logic-apps-control-flow-run-steps-group-scopes/select-run-after-statuses.png)

   1. Klicken Sie auf **Fertig**, wenn Sie fertig sind. 
   Die Bedingung zeigt jetzt ein „Informationssymbol“ an.

1. Fügen Sie in den Branches **If true** (Bei „true“) und **If false** (Bei „false“) die Aktionen hinzu, die Sie auf Basis der einzelnen Bereichsstatus ausführen möchten, z. B. das Senden einer E-Mail-Adresse oder einer Nachricht.

   ![Hinzufügen der auf Basis des Bereichsstatus zu ergreifenden Aktionen](./media/logic-apps-control-flow-run-steps-group-scopes/handle-true-false-branches.png)

1. Speichern Sie Ihre Logik-App.

Nun sieht Ihre fertige Logik-App wie dieses Beispiel aus:

![Fertiggestellte Logik-App mit Bereich](./media/logic-apps-control-flow-run-steps-group-scopes/scopes-overview.png)

## <a name="test-your-work"></a>Testen Ihrer Arbeit

Wählen Sie auf der Symbolleiste des Designers die Option **Ausführen**. Wenn alle Aktionen im Bereich erfolgreich sind, erhalten Sie eine entsprechende Nachricht. Wenn Aktionen im Bereich nicht erfolgreich sind, erhalten Sie ebenfalls entsprechende Nachricht. 

<a name="scopes-json"></a>

## <a name="json-definition"></a>JSON-Definition

Wenn Sie in der Codeansicht arbeiten, können Sie stattdessen in der JSON-Definition Ihrer Logik-App eine Bereichsstruktur definieren. Hier sehen Sie z.B. die JSON-Definition für Auslöser und Aktionen in der vorherigen Logik-App:

``` json
"triggers": {
  "Recurrence": {
    "type": "Recurrence",
    "recurrence": {
       "frequency": "Minute",
       "interval": 1
    }
  }
}
```

```json
"actions": {
  "If_scope_failed": {
    "type": "If",
    "actions": {
      "Scope_failed": {
        "type": "ApiConnection",
        "inputs": {
          "body": {
            "Body": "Scope failed. Scope status: @{result('Scope')[0]['status']}",
            "Subject": "Scope failed",
            "To": "<your-email@domain.com>"
          },
          "host": {
            "connection": {
              "name": "@parameters('$connections')['outlook']['connectionId']"
            }
          },
          "method": "post",
          "path": "/Mail"
        },
        "runAfter": {}
      }
    },
    "else": {
      "actions": {
        "Scope_succeded": {
          "type": "ApiConnection",
          "inputs": {
            "body": {
              "Body": "Scope succeeded. Scope status: @{result('Scope')[0]['status']}",
              "Subject": "Scope succeeded",
              "To": "<your-email@domain.com>"
            },
            "host": {
              "connection": {
               "name": "@parameters('$connections')['outlook']['connectionId']"
              }
            },
            "method": "post",
            "path": "/Mail"
          },
          "runAfter": {}
        }
      }
    },
    "expression": {
      "or": [ 
         {
            "equals": [ 
              "@result('Scope')[0]['status']", 
              "Failed"
            ]
         },
         {
            "equals": [
               "@result('Scope')[0]['status']", 
               "Aborted"
            ]
         } 
      ]
    },
    "runAfter": {
      "Scope": [
        "Failed",
        "Skipped",
        "Succeeded",
        "TimedOut"
      ]
    }
  },
  "Scope": {
    "type": "Scope",
    "actions": {
      "Get_route": {
        "type": "ApiConnection",
        "inputs": {
          "host": {
            "connection": {
              "name": "@parameters('$connections')['bingmaps']['connectionId']"
            }
          },
          "method": "get",
          "path": "/REST/V1/Routes/Driving",
          "queries": {
            "distanceUnit": "Mile",
            "optimize": "timeWithTraffic",
            "travelMode": "Driving",
            "wp.0": "<start>",
            "wp.1": "<end>"
          }
        },
        "runAfter": {}
      },
      "If_traffic_time_is_more_than_specified_time": {
        "type": "If",
        "actions": {
          "Send_mail_when_traffic_exceeds_10_minutes": {
            "type": "ApiConnection",
            "inputs": {
              "body": {
                 "Body": "Travel time:@{div(body('Get_route')?['travelDurationTraffic'],60)} minutes",
                 "Subject": "Time to leave: Traffic more than 10 minutes",
                 "To": "<your-email@domain.com>"
              },
              "host": {
                "connection": {
                   "name": "@parameters('$connections')['outlook']['connectionId']"
                }
              },
              "method": "post",
              "path": "/Mail"
            },
            "runAfter": {}
          }
        },
        "expression": {
          "and" : [
            {
               "greater": [ 
                  "@body('Get_route')?['travelDurationTraffic']", 
                  600
               ]
            }
          ]
        },
        "runAfter": {
          "Get_route": [
            "Succeeded"
          ]
        }
      }
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Support

* Sollten Sie Fragen haben, besuchen Sie das [Azure Logic Apps-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Wenn Sie Featurevorschläge übermitteln oder darüber abstimmen möchten, besuchen Sie die [Website für Azure Logic Apps-Benutzerfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nächste Schritte

* [Ausführen von Schritten basierend auf einer Bedingung (Bedingungsanweisungen)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Ausführen von Schritten basierend auf verschiedenen Werten (switch-Anweisungen)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Ausführen und Wiederholen von Schritten (Schleifen)](../logic-apps/logic-apps-control-flow-loops.md)
* [Ausführen oder Zusammenführen paralleler Schritte (Verzweigungen)](../logic-apps/logic-apps-control-flow-branches.md)
