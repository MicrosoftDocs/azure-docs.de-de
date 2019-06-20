---
title: Aufrufen einer Azure-Funktion von Microsoft Flow | Microsoft-Dokumentation
description: Erstellen Sie einen benutzerdefinierten Connector, und rufen Sie anschließend mithilfe dieses Connectors eine Funktion auf.
services: functions
keywords: Cloud-Apps, Clouddienste, Microsoft Flow, Geschäftsprozesse, Geschäftsanwendung
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: glenga
ms.reviewer: sunayv
ms.custom: ''
ms.openlocfilehash: d3e777b5611dec382dc4eaaac5ec1594abcdab31
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65787675"
---
# <a name="call-a-function-from-microsoft-flow"></a>Aufrufen einer Funktion von Microsoft Flow

[Microsoft Flow](https://flow.microsoft.com/) ermöglicht die problemlose Automatisierung von Workflows und Geschäftsprozessen zwischen Ihren bevorzugten Apps und Diensten. Professionelle Entwickler können die Funktionen von Microsoft Flow mithilfe von Azure-Funktionen erweitern und gleichzeitig sicherstellen, dass sich Flow-Ersteller nicht mit technischen Details beschäftigen müssen.

In diesem Thema erstellen Sie einen Workflow auf der Grundlage eines Wartungsszenarios für Windturbinen. Sie erfahren, wie Sie die Funktion aufrufen, die Sie in [Erstellen einer OpenAPI-Definition für eine Funktion](functions-openapi-definition.md) definiert haben. Die Funktion bestimmt, ob die Notfallreparatur einer Windturbine kosteneffizient ist. Ist sie kosteneffizient, sendet der Workflow eine E-Mail, um die Reparatur zu empfehlen.

Informationen zum Aufrufen derselben Funktion aus PowerApps finden Sie unter [Aufrufen einer Funktion aus PowerApps](functions-powerapps-scenario.md).

In diesem Thema lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer Liste in SharePoint.
> * Exportieren einer API-Definition
> * Hinzufügen einer Verbindung mit der API
> * Erstellen eines Workflows, um E-Mails zu senden, wenn eine Reparatur kostengünstig ist.
> * Ausführen des Workflows.

[!INCLUDE [functions-openapi-note](../../includes/functions-openapi-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

+ Ein aktives [Microsoft Flow-Konto](https://flow.microsoft.com/documentation/sign-up-sign-in/) mit den gleichen Anmeldeinformationen wie Ihr Azure-Konto. 
+ SharePoint, das Sie als Datenquelle für diesen Workflow verwenden. Melden Sie sich für [eine Testversion von Office 365](https://signup.microsoft.com/Signup?OfferId=467eab54-127b-42d3-b046-3844b860bebf&dl=O365_BUSINESS_PREMIUM&ali=1) an, wenn Sie SharePoint noch nicht haben.
+ Absolvierung des Tutorials [Erstellen einer OpenAPI-Definition für eine Funktion](functions-openapi-definition.md)

## <a name="create-a-sharepoint-list"></a>Erstellen einer SharePoint-Liste
Erstellen Sie zunächst eine Liste, die Sie als Datenquelle für den Workflow verwenden. Die Liste verfügt über die folgende Spalten.

| Listenspalte     | Datentyp           | Notizen                                    |
|-----------------|---------------------|------------------------------------------|
| **Titel**           | Einzelne Textzeile | Name der Turbine                      |
| **LastServiceDate** | Date                |                                          |
| **MaxOutput**       | Number              | Ausgabe der Turbine in KwH            |
| **ServiceRequired** | Ja/Nein              |                                          |
| **EstimatedEffort** | Number              | Geschätzte Zeit für die Reparatur in Stunden |

1. Klicken oder tippen Sie auf der SharePoint-Website auf **Neu**, und klicken Sie dann auf **Liste**.

    ![Erstellen Sie eine neue SharePoint-Liste](./media/functions-flow-scenario/new-list.png)

2. Geben Sie den Namen `Turbines` ein, und klicken oder tippen Sie auf **Erstellen**.

    ![Geben Sie den Namen für die neue Liste an](./media/functions-flow-scenario/create-list.png)

    Die Liste **Turbinen** mit dem Standardfeld **Titel** wird erstellt.

    ![Turbinenliste](./media/functions-flow-scenario/initial-list.png)

3. Klicken oder tippen Sie auf das ![Symbol „Neues Element“](./media/functions-flow-scenario/icon-new.png) und dann auf **Datum**.

    ![Fügen Sie eine einzelne Textzeile hinzu](./media/functions-flow-scenario/add-column.png)

4. Geben Sie den Namen `LastServiceDate` ein, und klicken oder tippen Sie auf **Erstellen**.

    ![Erstellen Sie die LastServiceDate-Spalte](./media/functions-flow-scenario/date-column.png)

5. Wiederholen Sie die letzten beiden Schritte für die anderen drei Spalten in der Liste:

    1. **Anzahl** > „MaxOutput“

    2. **Ja/Nein** > „ServiceRequired“

    3. **Anzahl** > „EstimatedEffort“

Das ist alles – Sie sollten über eine leere Liste verfügen, die wie folgt aussieht. Fügen Sie Daten zur Liste hinzu, nachdem Sie den Datenfluss erstellt haben.

![Leere Liste](media/functions-flow-scenario/empty-list.png)

[!INCLUDE [Export an API definition](../../includes/functions-export-api-definition.md)]

## <a name="add-a-connection-to-the-api"></a>Hinzufügen einer Verbindung mit der API
Die benutzerdefinierte API (auch benutzerdefinierter Connector genannt) ist zwar in Microsoft Flow verfügbar, Sie müssen jedoch eine Verbindung mit der API herstellen, um sie in einem Datenfluss verwenden zu können.

1. Klicken Sie in [flow.microsoft.com](https://flow.microsoft.com) auf das Zahnradsymbol (in der oberen rechten Ecke), und klicken Sie auf **Verbindungen**.

    ![Datenflussverbindungen](media/functions-flow-scenario/flow-connections.png)

1. Klicken Sie auf **Verbindung erstellen**, scrollen Sie nach unten zum Connector **Turbinenreparatur**, und klicken Sie darauf.

    ![Erstellen der Verbindung](media/functions-flow-scenario/create-connection.png)

1. Geben Sie den API-Schlüssel ein, und klicken Sie auf **Verbindung erstellen**.

    ![Geben Sie die API-Schlüssel, und klicken Sie auf Erstellen](media/functions-flow-scenario/api-key.png)

> [!NOTE]
> Bei einem gemeinsam genutzten Datenfluss muss jeder Benutzer den API-Schlüssel eingeben, um eine Verbindung mit der API herzustellen. Dieses Verhalten wird unter Umständen noch geändert und das Thema ggf. entsprechend angepasst.


## <a name="create-a-flow"></a>Erstellen eines Datenflusses

Nun können Sie einen Datenfluss erstellen, der den benutzerdefinierten Connector und die von Ihnen erstellte SharePoint-Liste verwendet.

### <a name="add-a-trigger-and-specify-a-condition"></a>Hinzufügen eines Triggers und Angeben einer Bedingung

Erstellen Sie zunächst einen Datenfluss ohne Vorlage, und fügen Sie einen *Trigger* hinzu, der ausgelöst wird, wenn ein Element in der SharePoint-Liste erstellt wird. Fügen Sie dann eine *Bedingung* hinzu, um zu bestimmen, was daraufhin geschieht.

1. Klicken Sie in [flow.microsoft.com](https://flow.microsoft.com) auf **Meine Datenflüsse**, und klicken Sie dann **Ohne Vorlage erstellen**.

    ![Ohne Vorlage erstellen](media/functions-flow-scenario/create-from-blank.png)

2. Klicken Sie auf den SharePoint-Trigger **Beim Erstellen eines Elements**.

    ![Trigger auswählen](media/functions-flow-scenario/choose-trigger.png)

    Wenn Sie nicht bereits in SharePoint angemeldet sind, werden Sie aufgefordert, dies zu tun.

3. Geben Sie für die **Standortadresse** den Namen Ihrer SharePoint-Website ein, und für den **Listennamen** die Liste mit den Turbinendaten.

    ![Trigger auswählen](media/functions-flow-scenario/site-list.png)

4. Klicken Sie auf **Neuer Schritt** und dann auf **Eine Bedingung hinzufügen**.

    ![Bedingung hinzufügen](media/functions-flow-scenario/add-condition.png)

    Microsoft Flow fügt zwei Verzweigungen zum Datenfluss hinzu: **Wenn ja** und **Wenn nein**. Nachdem Sie die Bedingung definiert haben, die übereinstimmen sollen, fügen Sie Schritte zu einer oder beiden Verzweigungen hinzu.

    ![Bedingungsverzweigungen](media/functions-flow-scenario/condition-branches.png)

5. Klicken Sie auf der Karte **Bedingung** auf das erste Feld, und wählen Sie dann **ServiceRequired** aus dem Dialogfeld **Dynamische Inhalte** aus.

    ![Auswahlfeld für die Bedingung](media/functions-flow-scenario/condition1-field.png)

6. Geben Sie einen Wert von `True` für die Bedingung ein.

    ![Geben Sie „TRUE“ für die Bedingung ein](media/functions-flow-scenario/condition1-yes.png)

    Der Wert wird als `Yes` oder `No` in der SharePoint Liste angezeigt, aber als *boolescher* Wert gespeichert , entweder `True` oder `False`. 

7. Klicken Sie am oberen Rand der Seite auf **Datenfluss erstellen**. Klicken Sie in regelmäßigen Abständen auf **Datenfluss aktualisieren**.

Für alle erstellten Elemente in der Liste überprüft der Datenfluss, ob das Feld **ServiceRequired** auf `Yes`, festgelegt ist, wechselt dann zu der Verzweigung **Falls Ja** oder der Verzweigung **Falls Nein**, je nach Bedarf. Geben Sie aus Zeitgründen in diesem Thema nur Aktionen für die Verzweigung **Falls Ja** an.

### <a name="add-the-custom-connector"></a>Einen benutzerdefinierten Connector hinzufügen

Sie fügen nun den benutzerdefinierten Connector hinzu, der die Funktionsaufrufe in Azure aufruft. Fügen Sie den benutzerdefinierten Connector genau wie ein Standard-Connector zum Datenfluss hinzu. 

1. Klicken Sie in der Verzweigung **Falls Ja** auf **Eine Aktion hinzufügen**.

    ![Hinzufügen einer Aktion](media/functions-flow-scenario/condition1-yes-add-action.png)

2. Suchen Sie im Dialogfeld **Eine Aktion auswählen** nach `Turbine Repair`, wählen Sie dann die Aktion **Turbinenreparatur – Berechnet Kosten** aus.

    ![Auswählen einer Aktion](media/functions-flow-scenario/choose-turbine-repair.png)

    Die folgende Abbildung zeigt die Karte, die zum Workflow hinzugefügt wird. Die Felder und Beschreibungen stammen aus der OpenAPI-Definition für den Connector.

    ![Berechnet die Kosten für Standardwerte](media/functions-flow-scenario/calculates-costs-default.png)

3. Verwenden Sie auf der Karte **Berechnet Kosten** das Dialogfeld **Dynamische Inhalte**, um Eingaben für die Funktion auszuwählen. Microsoft Flow zeigt numerische Felder, jedoch nicht das Datumsfeld, da die OpenAPI-Definition angibt, dass **Stunden** und **Kapazität** numerisch sind.

    Wählen Sie für **Stunden** die Option **EstimatedEffort** aus, und für **Kapazität** die Option **MaxOutput**.

    ![Auswählen einer Aktion](media/functions-flow-scenario/calculates-costs-fields.png)

     Nun fügen Sie eine andere Bedingung auf der Grundlage der Funktionsausgabe hinzu.

4. Klicken Sie am unteren Rand der Verzweigung **Falls Ja** auf **Mehr**, und klicken Sie dann **Eine Bedingung hinzufügen**.

    ![Bedingung hinzufügen](media/functions-flow-scenario/condition2-add.png)

5. Klicken Sie auf der Karte **Bedingung 2** auf das erste Feld, und wählen Sie dann **Nachricht** aus dem Dialogfeld **Dynamische Inhalte** aus.

    ![Auswahlfeld für die Bedingung](media/functions-flow-scenario/condition2-field.png)

6. Geben Sie einen Wert von `Yes` ein. Der Workflow wechselt zur nächsten **Falls Ja**- oder **Falls Nein**-Verzweigung in Abhängigkeit davon, ob die von der Funktion zurückgegebene Nachricht „Ja“ (führen Sie die Reparatur durch) oder „Nein“ (führen Sie die Reparatur nicht durch) lautet. 

    ![Geben Sie „Ja“ für die Bedingung ein](media/functions-flow-scenario/condition2-yes.png)

Der Workflow sollte jetzt wie die folgende Abbildung aussehen.

![Geben Sie „Ja“ für die Bedingung ein](media/functions-flow-scenario/flow-checkpoint1.png)

### <a name="send-email-based-on-function-results"></a>Senden von E-Mails basierend auf Funktionsergebnissen

An diesem Punkt im Workflow hat die Funktion den Wert **Nachricht** von `Yes` oder `No` aus der Funktion zurückgegeben sowie andere Informationen zu Kosten und potenziellem Umsatz. In der Verzweigung **Falls Ja** der zweiten Bedingung senden Sie eine E-Mail, aber Sie könnten eine beliebige Anzahl von Dingen erledigen, wie z.B. an die SharePoint-Liste zurückzuschreiben oder einen [Genehmigungsprozess](https://flow.microsoft.com/documentation/modern-approvals/) zu starten.

1. Klicken Sie in der **Falls Ja**-Verzweigung der zweiten Bedingung auf **Eine Aktion hinzufügen**.

    ![Hinzufügen einer Aktion](media/functions-flow-scenario/condition2-yes-add-action.png)

2. Suchen Sie im Dialogfeld **Eine Aktion auswählen** nach `email`, wählen Sie dann auf der Grundlage des verwendeten E-Mail-Systems (in diesem Fall Outlook) eine E-Mail-Sendeaktion aus.

    ![Outlook sendet eine E-Mail](media/functions-flow-scenario/outlook-send-email.png)

3. Verfassen Sie auf der Karte **Eine E-Mail senden** eine E-Mail. Geben Sie einen gültigen Namen aus Ihrem Unternehmen für das **An**-Feld ein. In der folgenden Abbildung sehen Sie, dass alle anderen Felder eine Kombination aus Text und Tokens aus dem Dialogfeld **Dynamische Inhalte** sind. 

    ![E-Mail-Felder](media/functions-flow-scenario/email-fields.png)

    Das **Titel**-Token ergibt sich aus der SharePoint-Liste und **CostToFix** und **RevenueOpportunity** werden von der Funktion zurückgegeben.

    Der abgeschlossene Workflow sollte wie im folgenden Bild aussehen (wir haben die erste **Falls Nein**-Verzweigung außer acht gelassen, um Platz zu sparen).

    ![Vollständiger Workflow](media/functions-flow-scenario/complete-flow.png)

4. Klicken Sie am oberen Rand der Seite auf **Workflow aktualisieren**, und klicken Sie dann auf **Fertig**.

## <a name="run-the-flow"></a>Ausführen des Workflows

Nun, da der Workflow abgeschlossen ist, fügen Sie eine Zeile zur SharePoint-Liste hinzu, und beobachten, wie der Workflow reagiert.

1. Wechseln Sie zurück zur SharePoint-Liste, und klicken Sie auf **Schnelle Bearbeitung**.

    ![Schnelle Bearbeitung](media/functions-flow-scenario/quick-edit.png)

2. Geben Sie auf im Bearbeitungsfeld die folgenden Werte ein.

    | Listenspalte     | Wert           |
    |-----------------|---------------------|
    | **Titel**           | Turbine 60 |
    | **LastServiceDate** | 08/04/2017 |
    | **MaxOutput**       | 2500 |
    | **ServiceRequired** | Ja |
    | **EstimatedEffort** | 10 |

3. Klicken Sie auf **Fertig**.

    ![Schnelle Bearbeitung abgeschlossen](media/functions-flow-scenario/quick-edit-done.png)

    Wenn Sie das Element hinzufügen, löst es den Workflow aus, auf den Sie nun einen Blick werfen werden.

4. Klicken Sie unter [flow.microsoft.com](https://flow.microsoft.com) auf **Meine Workflows**, und klicken Sie dann auf die Vorlage, die Sie erstellt haben.

    ![Meine Workflows](media/functions-flow-scenario/my-flows.png)

5. Klicken Sie unter **Ausführungsverlauf** auf die Ausführung des Workflows.

    ![Ausführungsverlauf](media/functions-flow-scenario/run-history.png)

    Wenn die Ausführung erfolgreich war, können Sie die Workflow-Vorgänge auf der nächsten Seite überprüfen. Wenn die Ausführung aus irgendeinem Grund fehlgeschlagen ist, enthält die nächste Seite Informationen zur Problembehandlung.

6. Erweitern Sie die Karten, um festzustellen, was während des Workflows aufgetreten ist. Erweitern Sie beispielsweise die Karte **Berechnete Kosten**, um die Eingaben und Ausgaben der Funktion anzuzeigen. 

    ![Berechnet Eingabe- und Ausgabekosten](media/functions-flow-scenario/calculates-costs-outputs.png)

7. Überprüfen Sie das E-Mail-Konto für die Person, die Sie im Feld **An** der Karte **E-Mail senden** angegeben haben. Die aus dem Workflow versendete E-Mail sollte wie die folgende Abbildung aussehen.

    ![Workflow-E-Mail](media/functions-flow-scenario/flow-email.png)

    Sie können sehen, wie das Token durch die richtigen Werte aus der SharePoint-Liste und der Funktion ersetzt wurde.

## <a name="next-steps"></a>Nächste Schritte
In diesem Thema wurde Folgendes vermittelt:

> [!div class="checklist"]
> * Erstellen einer Liste in SharePoint.
> * Exportieren einer API-Definition
> * Hinzufügen einer Verbindung mit der API
> * Erstellen eines Workflows, um E-Mails zu senden, wenn eine Reparatur kostengünstig ist.
> * Ausführen des Workflows.

Weitere Informationen zu Microsoft Flow finden Sie unter [Erste Schritte mit Microsoft Flow](https://flow.microsoft.com/documentation/getting-started/).

Ein weiteres interessantes Szenario mit Azure-Funktionen finden Sie unter [Aufrufen einer Funktion aus PowerApps](functions-powerapps-scenario.md) und [Erstellen einer Funktion, die in Azure Logic Apps integriert ist](functions-twitter-email.md).
