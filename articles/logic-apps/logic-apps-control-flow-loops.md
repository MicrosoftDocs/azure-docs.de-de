---
title: Hinzufügen von Schleifen zum Wiederholen von Aktionen
description: Erstellen von Schleifen in Azure Logic Apps, die Workflowaktionen wiederholen oder Arrays verarbeiten
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/05/2019
ms.openlocfilehash: aa4be5852b4f8af00346a3ea9a86b13a85f99824
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93358455"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>Erstellen von Schleifen in Azure Logic Apps, die Workflowaktionen wiederholen oder Arrays verarbeiten

Um ein Array in Ihrer Logik-App zu verarbeiten, können Sie eine [„ForEach“-Schleife](#foreach-loop) erstellen. Diese Schleife wiederholt eine oder mehrere Aktionen für jedes Element im Array. Informationen zum Grenzwert für die Anzahl von Arrayelementen, die von einer ForEach-Schleife verarbeitet werden kann, finden Sie unter [Grenzwerte für Parallelität, Schleifen und Auflösen von Batches](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

Um Aktionen zu wiederholen, bis eine Bedingung erfüllt ist oder sich ein Zustand ändert, können Sie eine [„Until“-Schleife](#until-loop) erstellen. Ihre Logik-App führt zuerst alle Aktionen innerhalb der Schleife aus und überprüft anschließend die Bedingung oder den Status. Wenn die Bedingung erfüllt ist, wird die Schleife beendet. Andernfalls wird die Schleife wiederholt. Informationen zu den Standard- und Höchstwerten für die Anzahl von Until-Schleifen in einer Logik-App-Ausführung finden Sie unter [Grenzwerte für Parallelität, Schleifen und Auflösen von Batches](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

> [!TIP]
> Wenn Sie einen Auslöser verwenden, der ein Array empfängt, und für jedes Arrayelement einen Workflow ausführen möchten, können Sie dieses Array mit der [**Auslösereigenschaft** SplitOn](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch)*aus dem Batch lösen*.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/). 

* Grundlegende Kenntnisse über die [Erstellung von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>„ForEach“-Schleife

Eine ForEach-Schleife wiederholt eine oder mehrere Aktionen für jedes Arrayelement und funktioniert nur für Arrays. Hier sind einige Aspekte angegeben, die beim Verwenden von „ForEach“-Schleifen berücksichtigt werden sollten:

* Die ForEach-Schleife kann eine begrenzte Anzahl von Arrayelementen verarbeiten. Informationen zu diesem Grenzwert finden Sie unter [Grenzwerte für Parallelität, Schleifen und Auflösen von Batches](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

* Standardmäßig werden Iterationen in einer „Foreach“-Schleife gleichzeitig bzw. parallel ausgeführt. Dieses Verhalten unterscheidet sich von der [**Apply to each**-Schleife von Power Automate](/power-automate/apply-to-each), bei der Iterationen einzeln bzw. nacheinander ausgeführt werden. Sie können jedoch [sequenzielle Iterationen für „Foreach“-Schleifen](#sequential-foreach-loop) einrichten. Wenn Sie beispielsweise die nächste Iteration in einer „Foreach“-Schleife mithilfe der Verzögerungsaktion ([Delay](../connectors/connectors-native-delay.md)) anhalten möchten, müssen Sie die Schleife so einrichten, dass sie sequenziell ausgeführt wird.

  Die Ausnahme beim Standardverhalten sind geschachtelte Schleifen, deren Iterationen immer nacheinander, nicht parallel ausgeführt werden. Um Vorgänge für Elemente in einer geschachtelten Schleife parallel auszuführen, erstellen Sie [eine untergeordnete Logik-App und rufen Sie sie auf](../logic-apps/logic-apps-http-endpoint.md).

* Um vorhersehbare Ergebnisse von Um vorhersagbare Ergebnisse von Vorgängen mit Variablen während jeder Schleifeniteration zu erhalten, führen Sie diese Schleifen nacheinander aus. Wenn beispielsweise eine gleichzeitig laufende Schleife endet, liefern die Inkrementierung, Dekrementierung und Anhänge an variable Vorgänge vorhersehbare Ergebnisse. Bei jeder Iteration in der gleichzeitig laufenden Schleife können diese Vorgänge jedoch zu unvorhersehbaren Ergebnissen führen. 

* Aktionen in einer „ForEach“-Schleife verwenden den Ausdruck [`@item()`](../logic-apps/workflow-definition-language-functions-reference.md#item) 
zum Verweisen auf und zum Verarbeiten der einzelnen Elemente im Array. Wenn Sie Daten angeben, die sich nicht in einem Array befinden, tritt im Logik-App-Workflow ein Fehler auf. 

Diese Beispiel-Logik-App sendet eine tägliche Zusammenfassung für einen RSS-Feed einer Website. Die App verwendet eine „ForEach“-Schleife, die für jedes neue Element eine E-Mail sendet.

1. [Erstellen Sie diese Beispiel-Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md) mit einem Outlook.com- bzw. einem Geschäfts-, Schul- oder Unikonto.

2. Fügen Sie zwischen dem RSS-Trigger und der E-Mail-Sendeaktion eine „ForEach“-Schleife hinzu. 

   1. Wenn Sie zwischen Schritten eine Schleife einfügen möchten, bewegen Sie den Mauszeiger über den Pfeil zwischen den Schritten. 
   Wählen Sie das daraufhin angezeigte **Pluszeichen** ( **+** ) und dann **Aktion hinzufügen** aus.

      ![Auswählen von „Aktion hinzufügen“](media/logic-apps-control-flow-loops/add-for-each-loop.png)

   1. Wählen Sie unter dem Suchfeld **Alle** aus. Geben Sie im Suchfeld die Zeichenfolge „for each“ als Filter ein. Wählen Sie in der Liste mit den Aktionen diese Aktion aus: **ForEach – Steuerung**

      ![Hinzufügen einer „ForEach“-Schleife](media/logic-apps-control-flow-loops/select-for-each.png)

3. Erstellen Sie jetzt die Schleife. Wählen Sie unter **Ausgabe von vorherigen Schritten auswählen**, nachdem die Liste **Dynamischen Inhalt hinzufügen** angezeigt wird, das **Feed links**-Array aus, das vom RSS-Trigger ausgegeben wird. 

   ![Aus Liste mit dynamischen Inhalten auswählen](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > Sie können *nur* Arrayausgaben aus dem vorherigen Schritt auswählen.

   Das ausgewählte Array wird nun hier angezeigt:

   ![Array auswählen](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Um für jedes Arrayelement eine Aktion auszuführen, ziehen Sie die **E-Mail senden**-Aktion in die „Foreach“-Schleife. 

   Ihre Logik-App sieht in etwa wie im folgenden Beispiel aus:

   ![Schritte für „ForEach“-Schleife hinzufügen](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. Speichern Sie Ihre Logik-App. Wählen Sie in der Symbolleiste des Designers die Option **Ausführen**, um Ihre Logik-App manuell zu testen.

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>Definition der „ForEach“-Schleife (JSON)

Wenn Sie in der Codeansicht für Ihre Logik-App arbeiten, können Sie die `Foreach`-Schleife stattdessen in der JSON-Definition der Logik-App definieren, z.B.:

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": {
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "@{item()}",
                  "Subject": "New CNN post @{triggerBody()?['publishDate']}",
                  "To": "me@contoso.com"
               },
               "host": {
                  "api": {
                     "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/office365"
                  },
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {}
   }
}
```

<a name="sequential-foreach-loop"></a>

## <a name="foreach-loop-sequential"></a>„ForEach“-Schleife: Sequenziell

Standardmäßig werden Zyklen in einer „ForEach“-Schleife parallel ausgeführt. Um jeden Zyklus sequenziell auszuführen, legen Sie die Option **Sequenziell** in Ihrer Schleife fest. „ForEach“-Schleifen müssen sequentiell ausgeführt werden, wenn Sie verschachtelte Schleifen oder Variablen innerhalb von Schleifen haben, in denen Sie vorhersehbare Ergebnisse erwarten. 

1. Wählen Sie in der oberen rechten Ecke der Schleife **Auslassungspunkte** ( **...** ) > **Einstellungen**.

   ![In „ForEach“-Schleife „...“ > „Einstellungen“ auswählen](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

1. Legen Sie unter **Gleichzeitigkeitssteuerung** die Einstellung **Gleichzeitigkeitssteuerung** auf **Ein** fest. Ziehen Sie den Schieberegler **Parallelitätsgrad** auf **1**, und wählen Sie **Fertig** aus.

   ![Einschalten der Gleichzeitigkeitssteuerung](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

Wenn Sie mit der JSON-Definition Ihrer Logik-App arbeiten, können Sie die Option `Sequential` verwenden, indem Sie beispielsweise den Parameter `operationOptions` hinzufügen, z.B.:

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": { }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {},
      "operationOptions": "Sequential"
   }
}
```

<a name="until-loop"></a>

## <a name="until-loop"></a>„Until“-Schleife
  
Wenn Sie Aktionen ausführen und wiederholen möchten, bis eine Bedingung erfüllt ist oder sich ein Status ändert, können Sie diese Aktionen in eine „Until“-Schleife verlegen. Ihre Logik-App führt zuerst alle Aktionen innerhalb der Schleife aus und überprüft anschließend die Bedingung oder den Status. Wenn die Bedingung erfüllt ist, wird die Schleife beendet. Andernfalls wird die Schleife wiederholt. Informationen zu den Standard- und Höchstwerten für die Anzahl von Until-Schleifen in einer Logik-App-Ausführung finden Sie unter [Grenzwerte für Parallelität, Schleifen und Auflösen von Batches](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

Hier sind einige gängige Szenarios für eine „Until“-Schleife:

* Rufen Sie einen Endpunkt auf, bis Sie die gewünschte Antwort erhalten.

* Erstellen Sie einen Datensatz in der Datenbank. Warten Sie, bis ein bestimmtes Feld in diesem Datensatz genehmigt wird. Setzen Sie die Verarbeitung fort. 

Diese beispielhafte Logik-App inkrementiert täglich um 8:00 Uhr eine Variable, bis diese den Wert 10 hat. Dann sendet die Logik-App eine E-Mail, die den aktuellen Wert bestätigt. 

> [!NOTE]
> Diese Schritte verwenden Office 365 Outlook, aber Sie können jeden E-Mail-Anbieter verwenden, der Logik-Apps unterstützt. 
> [Eine Liste der Connectors finden Sie hier](/connectors/). Bei Verwendung eines anderen E-Mail-Kontos bleiben die allgemeinen Schritte zwar gleich, die Benutzeroberfläche sieht aber unter Umständen etwas anders aus. 

1. Erstellen einer leeren Logik-App Wählen Sie im Logik-App-Designer im Suchfeld **Alle** aus. Suchen Sie nach „Wiederholung“. 
   Wählen Sie in der Triggerliste den folgenden Trigger aus: **Wiederholung – Zeitplan**

   ![Hinzufügen des Triggers „Wiederholung – Zeitplan“](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

1. Geben Sie an, wann der Trigger ausgelöst wird, indem Sie das Intervall, die Häufigkeit und die Stunde des Tages festlegen. Um die Stunde festzulegen, wählen Sie **Erweiterte Optionen anzeigen** aus.

   ![Einrichten des Wiederholungszeitplans](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Eigenschaft | Wert |
   | -------- | ----- |
   | **Intervall** | 1 | 
   | **Frequency** | Day (Tag) |
   | **Zu diesen Stunden** | 8 |
   ||| 

1. Wählen Sie unter dem Trigger die Option **Neuer Schritt** aus. 
   Suchen Sie nach „Variablen“, und wählen Sie diese Aktion aus: **Variable initialisieren – Variablen**

   ![Hinzufügen der Aktion „Variable initialisieren – Variablen“](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

1. Richten Sie die Variable mit den folgenden Werten ein:

   ![Variableneigenschaften festlegen](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Eigenschaft | Wert | Beschreibung |
   | -------- | ----- | ----------- |
   | **Name** | Begrenzung | Name Ihrer Variablen | 
   | **Typ** | Integer | Datentyp Ihrer Variablen | 
   | **Wert** | 0 | Startwert Ihrer Variablen | 
   |||| 

1. Wählen Sie unter der Aktion **Variable initialisieren** die Option **Neuer Schritt** aus. 

1. Wählen Sie unter dem Suchfeld **Alle** aus. Suchen Sie nach „until“, und wählen Sie diese Aktion aus: **Until – Steuerung**

   ![Hinzufügen der „Until“-Schleife](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

1. Erstellen Sie die Beendigungsbedingung der Schleife durch Auswahl der **Limit**-Variablen und des **ist gleich**-Operators. 
   Geben Sie **10** als Vergleichswert ein.

   ![Beendigungsbedingung zum Anhalten der Schleife erstellen](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

1. Wählen Sie in der Schleife **Aktion hinzufügen**. 

1. Wählen Sie unter dem Suchfeld **Alle** aus. Suchen Sie nach „Variablen“, und wählen Sie diese Aktion aus: **Variable erhöhen – Variablen**

   ![Aktion zum Inkrementieren der Variablen hinzufügen](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

1. Wählen Sie für **Name** die **Limit**-Variable. Geben Sie für **Wert** „1“ ein. 

     ![„Limit“ um 1 inkrementieren](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

1. Wählen Sie außerhalb und unter der Schleife **Neuer Schritt** aus. 

1. Wählen Sie unter dem Suchfeld **Alle** aus. 
     Suchen Sie eine Aktion zum Senden von E-Mails, und fügen Sie sie hinzu, zum Beispiel: 

     ![Aktion hinzufügen, die E-Mail sendet](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

1. Melden Sie sich nach Aufforderung bei Ihrem E-Mail-Konto an.

1. Legen Sie die Eigenschaften der E-Mail-Aktion fest. Fügen Sie die **Limit**-Variable dem Betreff hinzu. Auf diese Weise können Sie sicherstellen, dass der aktuelle Wert der Variablen die angegebene Bedingung erfüllt, beispielsweise:

      ![E-Mail-Eigenschaften einrichten](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

      | Eigenschaft | Wert | BESCHREIBUNG |
      | -------- | ----- | ----------- | 
      | **An** | *\<email-address\@domain>* | Die E-Mail-Adresse des Empfängers. Geben Sie zum Testen Ihre eigene E-Mail-Adresse an. | 
      | **Subject** | Aktueller Wert für „Limit“ ist **Limit** | Legen Sie den E-Mail-Betreff fest. Stellen Sie in diesem Beispiel sicher, dass Sie die **Limit**-Variable einbeziehen. | 
      | **Text** | <*E-Mail-Inhalt*> | Geben Sie den Inhalt der E-Mail-Nachricht an, die Sie senden möchten. In diesem Beispiel können Sie beliebigen Text eingeben. | 
      |||| 

1. Speichern Sie Ihre Logik-App. Wählen Sie in der Symbolleiste des Designers die Option **Ausführen**, um Ihre Logik-App manuell zu testen.

      Sobald Ihre Logik ausgeführt wird, erhalten Sie eine E-Mail mit dem Inhalt, den Sie angegeben:

      ![Empfangene E-Mail](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

<a name="prevent-endless-loops"></a>

## <a name="prevent-endless-loops"></a>Verhindern von Endlosschleifen

Die Ausführung der Until-Schleife wird basierend auf diesen Eigenschaften beendet. Gehen Sie daher beim Festlegen dieser Werte entsprechend sorgfältig vor:

* **Count**: Dieser Wert ist die höchste Anzahl von Schleifen, die ausgeführt werden, bevor die Schleife beendet wird. Informationen zu den Standard- und Höchstwerten für die Anzahl von Until-Schleifen in einer Logik-App-Ausführung finden Sie unter [Grenzwerte für Parallelität, Schleifen und Auflösen von Batches](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

* **Timeout**: Dieser Wert ist die maximale Zeitspanne, die die Schleife ausgeführt wird, bevor sie beendet wird. Die Angabe erfolgt im [ISO 8601-Format](https://en.wikipedia.org/wiki/ISO_8601). Informationen zu den Standard- und Höchstwerten für den **Timeout**-Wert finden Sie unter [Grenzwerte für Parallelität, Schleifen und Auflösen von Batches](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

  Der Timeoutwert wird für jeden Schleifendurchlauf ausgewertet. Wenn eine Aktion in der Schleife länger als die Zeitüberschreitung dauert, wird der aktuelle Zyklus nicht beendet. Der nächste Zyklus beginnt jedoch nicht, weil die Grenzwertbedingung nicht erfüllt ist.

Um diese Grenzwerte zu ändern, wählen Sie in der Schleifenaktion die Option **Grenzwerte ändern** aus.

<a name="until-json"></a>

## <a name="until-definition-json"></a>„Until“-Definition (JSON)

Wenn Sie in der Codeansicht für Ihre Logik-App arbeiten, können Sie eine `Until`-Schleife stattdessen in der JSON-Definition der Logik-App definieren, z.B.:

``` json
"actions": {
   "Initialize_variable": {
      // Definition for initialize variable action
   },
   "Send_an_email": {
      // Definition for send email action
   },
   "Until": {
      "type": "Until",
      "actions": {
         "Increment_variable": {
            "type": "IncrementVariable",
            "inputs": {
               "name": "Limit",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "expression": "@equals(variables('Limit'), 10)",
      // To prevent endless loops, an "Until" loop 
      // includes these default limits that stop the loop. 
      "limit": { 
         "count": 60,
         "timeout": "PT1H"
      },
      "runAfter": {
         "Initialize_variable": [
            "Succeeded"
         ]
      }
   }
}
```

Diese beispielhafte „Until“-Schleife ruft einen HTTP-Endpunkt auf, der eine Ressource erzeugt. Die Schleife wird angehalten, wenn der HTTP-Antworttext mit dem Status `Completed` zurückgegeben wird. Um Endlosschleifen zu verhindern, wird die Schleife auch beendet, wenn eine dieser Bedingungen vorliegt:

* Die Schleife wurde gemäß `count`-Attribut 10-mal ausgeführt. Der Standardwert ist 60-mal. 

* Die Schleife wurde gemäß `timeout`-Attribut im ISO 8601-Format zwei Stunden ausgeführt. Der Standardwert ist eine Stunde.
  
``` json
"actions": {
   "myUntilLoopName": {
      "type": "Until",
      "actions": {
         "Create_new_resource": {
            "type": "Http",
            "inputs": {
               "body": {
                  "resourceId": "@triggerBody()"
               },
               "url": "https://domain.com/provisionResource/create-resource",
               "body": {
                  "resourceId": "@triggerBody()"
               }
            },
            "runAfter": {},
            "type": "ApiConnection"
         }
      },
      "expression": "@equals(triggerBody(), 'Completed')",
      "limit": {
         "count": 10,
         "timeout": "PT2H"
      },
      "runAfter": {}
   }
}
```

## <a name="get-support"></a>Support

* Weitere Informationen finden Sie auf der [Frageseite von Microsoft Q&A für Azure Logic Apps](/answers/topics/azure-logic-apps.html).
* Wenn Sie Featurevorschläge übermitteln oder darüber abstimmen möchten, besuchen Sie die [Website für Azure Logic Apps-Benutzerfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nächste Schritte

* [Ausführen von Schritten basierend auf einer Bedingung (Bedingungsanweisungen)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Ausführen von Schritten basierend auf verschiedenen Werten (switch-Anweisungen)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Ausführen oder Zusammenführen paralleler Schritte (Verzweigungen)](../logic-apps/logic-apps-control-flow-branches.md)
* [Ausführen von Schritten basierend auf gruppierten Aktionsstatus (Bereiche)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
