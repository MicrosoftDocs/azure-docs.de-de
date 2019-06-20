---
title: Beispiele für die Integration von Azure Service Bus in Event Grid | Microsoft-Dokumentation
description: Dieser Artikel enthält Beispiele für die Integration von Service Bus-Messaging und Event Grid.
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: spelluru
ms.openlocfilehash: b7dbc7dbc0b670de81a3f4603b0d52bce7559af8
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66428323"
---
# <a name="respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions-and-azure-logic-apps"></a>Reagieren auf Azure Service Bus-Ereignisse, die über Azure Event Grid empfangen wurden, mithilfe von Azure Functions und Azure Logic Apps
In diesem Tutorial erfahren Sie, wie Sie mithilfe von Azure Functions und Azure Logic Apps auf Azure Service Bus-Ereignisse reagieren, die über Azure Event Grid empfangen wurden. Hierbei gehen Sie wie folgt vor:
 
- Erstellen Sie eine Azure-Testfunktion zum Debuggen und Anzeigen des ursprünglichen Ereignisflusses aus Event Grid.
- Erstellen Sie eine Azure-Funktion zum Empfangen und Verarbeiten von Azure Service Bus-Nachrichten basierend auf Event Grid-Ereignissen.
- Erstellen Sie eine Logik-App zum Reagieren auf Event Grid-Ereignisse.

Nachdem Sie die Service Bus-, Event Grid-, Azure Functions- und Logic Apps-Artefakte erstellt haben, führen Sie die folgenden Aktionen aus: 

1. Senden von Nachricht an ein Service Bus-Thema. 
2. Überprüfen, ob die Abonnements des Themas diese Nachrichten empfangen haben.
3. Überprüfen, ob die Funktion oder Logik-App, die das Ereignis abonniert hat, das Ereignis erhalten hat. 

## <a name="create-a-service-bus-namespace"></a>Erstellen eines Service Bus-Namespace
Befolgen Sie die Anleitungen in diesem Tutorial: [Schnellstart: Erstellen eines Service Bus-Themas und eines oder mehrerer Abonnements dieses Themas im Azure-Portal](service-bus-quickstart-topics-subscriptions-portal.md), um folgende Aufgaben durchzuführen:

- Erstellen eines Service Bus **Premium**-Namespace. 
- Abrufen der Verbindungszeichenfolge. 
- Erstellen eines Service Bus-Themas.
- Erstellen von zwei Abonnements für das Thema. 

## <a name="prepare-a-sample-application-to-send-messages"></a>Vorbereiten einer Beispielanwendung zum Senden von Nachrichten.
Sie können beliebige Verfahren zum Senden einer Nachricht an Ihr Service Bus-Thema verwenden. Im Beispielcode am Ende dieses Verfahrens wird vorausgesetzt, dass Sie Visual Studio 2017 verwenden.

1. Klonen Sie das [GitHub-Repository „azure-service-bus“](https://github.com/Azure/azure-service-bus/).
2. Navigieren Sie in Visual Studio zum Ordner *\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration*, und öffnen Sie die Datei *SBEventGridIntegration.sln*.
3. Navigieren Sie zum Projekt **MessageSender**, und wählen Sie **Program.cs**.
4. Tragen Sie den Namen Ihres Service Bus-Themas ein sowie die Verbindungszeichenfolge, die Sie aus dem vorherigen Schritt haben:

    ```CSharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```
5. Erstellen Sie das Programm, und führen Sie es aus, um Testnachrichten an das Service Bus-Thema zu senden. 

## <a name="set-up-a-test-function-on-azure"></a>Einrichten einer Testfunktion in Azure 
Richten Sie vor dem Durcharbeiten des gesamten Szenarios mindestens eine kleine Testfunktion ein, mit der Sie die fließenden Ereignisse debuggen und beobachten können. Befolgen Sie die Anleitungen in dem Artikel [Erstellen Ihrer ersten Funktion im Azure-Portal](../azure-functions/functions-create-first-azure-function.md), um die folgenden Aufgaben auszuführen: 

1. Erstellen einer Funktions-App.
2. Erstellen einer durch HTTP ausgelösten Funktion. 

Führen Sie dann die folgenden Schritte aus: 


# <a name="azure-functions-v2tabv2"></a>[Azure Functions V2](#tab/v2)

1. Erweitern Sie **Funktionen** in der Strukturansicht, und wählen Sie Ihre Funktion aus. Ersetzen Sie den Code für die Funktion durch den folgenden Code: 

    ```CSharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");
        var content = req.Body;
        string jsonContent = await new StreamReader(content).ReadToEndAsync();
        log.LogInformation($"Received Event with payload: {jsonContent}");
    
        IEnumerable<string> headerValues;
        headerValues = req.Headers.GetCommaSeparatedValues("Aeg-Event-Type");
    
        if (headerValues.Count() != 0)
        {
            var validationHeaderValue = headerValues.FirstOrDefault();
            if(validationHeaderValue == "SubscriptionValidation")
            {
                var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
                var code = events[0].Data["validationCode"];
                log.LogInformation("Validation code: {code}");
                return (ActionResult) new OkObjectResult(new { validationResponse = code });
            }
        }
    
        return jsonContent == null
            ? new BadRequestObjectResult("Please pass a name on the query string or in the request body")
            : (ActionResult)new OkObjectResult($"Hello, {jsonContent}");
    }
    
    public class GridEvent
    {
        public string Id { get; set; }
        public string EventType { get; set; }
        public string Subject { get; set; }
        public DateTime EventTime { get; set; }
        public Dictionary<string, string> Data { get; set; }
        public string Topic { get; set; }
    }
    
    ```
2. Klicken Sie auf **Speichern und ausführen**.

    ![Ausgabe der Funktions-App](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
3. Wählen Sie **Funktions-URL abrufen** aus, und notieren Sie sich die URL. 

    ![Abrufen der Funktions-URL](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

# <a name="azure-functions-v1tabv1"></a>[Azure Functions V1](#tab/v1)

1. Konfigurieren Sie die Funktion für die Verwendung der Version **V1**: 
    1. Wählen Sie Ihre Funktions-App in der Strukturansicht aus, und wählen Sie **Funktionen-App-Einstellungen** aus. 

        ![Einstellungen für Funktions-Apps]()./media/service-bus-to-event-grid-integration-example/function-app-settings.png)
    2. Wählen Sie als **Laufzeitversion** **~1** aus. 
2. Erweitern Sie **Funktionen** in der Strukturansicht, und wählen Sie Ihre Funktion aus. Ersetzen Sie den Code für die Funktion durch den folgenden Code: 

    ```CSharp
    #r "Newtonsoft.Json"
    using System.Net;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info("C# HTTP trigger function processed a request.");
        // parse query parameter
        var content = req.Content;
    
        string jsonContent = await content.ReadAsStringAsync(); 
        log.Info($"Received Event with payload: {jsonContent}");
    
        IEnumerable<string> headerValues;
        if (req.Headers.TryGetValues("Aeg-Event-Type", out headerValues))
        {
            var validationHeaderValue = headerValues.FirstOrDefault();
            if(validationHeaderValue == "SubscriptionValidation")
            {
            var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
                 var code = events[0].Data["validationCode"];
                 return req.CreateResponse(HttpStatusCode.OK,
                 new { validationResponse = code });
            }
        }
    
        return jsonContent == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + jsonContent);
    }
    
    public class GridEvent
    {
        public string Id { get; set; }
        public string EventType { get; set; }
        public string Subject { get; set; }
        public DateTime EventTime { get; set; }
        public Dictionary<string, string> Data { get; set; }
        public string Topic { get; set; }
    }
    ```
4. Klicken Sie auf **Speichern und ausführen**.

    ![Ausgabe der Funktions-App](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
4. Wählen Sie **Funktions-URL abrufen** aus, und notieren Sie sich die URL. 

    ![Abrufen der Funktions-URL](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

---

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Verbinden der Funktion und des Namespace per Event Grid
In diesem Abschnitt verknüpfen Sie die Funktion und den Service Bus-Namespace unter Verwendung des Azure-Portals. 

Gehen Sie wie folgt vor, um ein Azure Event Grid-Abonnement zu erstellen:

1. Wechseln Sie im Azure-Portal zu Ihrem Namespace, und wählen Sie im Bereich auf der linken Seite dann **Ereignisse** aus. Ihr Namespacefenster wird geöffnet, und im rechten Bereich werden zwei Event Grid-Abonnements angezeigt. 
    
    ![Service Bus: Seite „Ereignisse“](./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png)
2. Wählen Sie in der Symbolleiste **+ Ereignisabonnement** aus. 
3. Führen Sie auf der Seite **Ereignisabonnement erstellen** die folgenden Schritte aus:
    1. Geben Sie einen **Namen** für das Abonnement ein. 
    2. Wählen Sie **Webhook** als **Endpunkttyp** aus. 

        ![Service Bus: Event Grid-Abonnement](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. Wählen Sie **Endpunkt auswählen** aus, fügen Sie die Funktions-URL ein, und wählen Sie dann **Auswahl bestätigen** aus. 

        ![Funktion: Endpunkt auswählen](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Wechseln Sie zur Registerkarte **Filter**, geben Sie den Namen des **erste Abonnements** des Service Bus-Themas ein, das Sie zuvor erstellt haben, und wählen Sie dann die Schaltfläche **Erstellen** aus. 

        ![Ereignisabonnementfilter](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Vergewissern Sie sich, dass das Ereignisabonnement in der Liste angezeigt wird.

    ![Ereignisabonnement in der Liste](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="send-messages-to-the-service-bus-topic"></a>Senden von Nachricht an das Service Bus-Thema
1. Führen Sie die .NET C#-Anwendung aus, die Nachrichten an das Service Bus-Thema sendet. 

    ![Ausgabe der Konsolen-App](./media/service-bus-to-event-grid-integration-example/console-app-output.png)
1. Erweitern Sie auf der Seite für Ihre Azure Funktions-App **Funktionen**, erweitern Sie Ihre **Funktion**, und wählen Sie **Überwachen** aus. 

    ![Überwachen-Funktion](./media/service-bus-to-event-grid-integration-example/function-monitor.png)

## <a name="receive-messages-by-using-azure-functions"></a>Empfangen von Nachrichten per Azure Functions
Im vorherigen Abschnitt haben Sie ein einfaches Test- und Debuggingszenario verwendet und sich vergewissert, dass Ereignisse fließen. 

In diesem Abschnitt erfahren Sie, wie Sie nach dem Erhalt eines Ereignisses Nachrichten empfangen und verarbeiten.

### <a name="publish-a-function-from-visual-studio"></a>Veröffentlichen einer Funktion aus Visual Studio
1. Wählen Sie in derselben Visual Studio-Projektmappe (**SBEventGridIntegration**), die Sie geöffnet haben, **ReceiveMessagesOnEvent.cs** im Projekt **SBEventGridIntegration** aus. 
2. Geben Sie Ihre Service Bus-Verbindungszeichenfolge in den folgenden Code ein:

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```
3. Laden Sie das **Veröffentlichungsprofil** für die Funktion herunter:
    1. Wählen Sie Ihre Funktions-App aus. 
    2. Wählen Sie die Registerkarte **Übersicht** aus, wenn diese nicht bereits ausgewählt ist. 
    3. Wählen Sie in der Symbolleiste **Veröffentlichungsprofil abrufen** aus. 

        ![Veröffentlichungsprofil für die Funktion abrufen](./media/service-bus-to-event-grid-integration-example/function-download-publish-profile.png)
    4. Speichern Sie die Datei im Ordner Ihres Projekts. 
4. Klicken Sie in Visual Studio mit der rechten Maustaste auf **SBEventGridIntegration**, und wählen Sie anschließend die Option **Veröffentlichen**. 
5. Wählen Sie auf der Seite **Veröffentlichen** die Option *Start** aus. 
6. Führen Sie auf der Seite **Veröffentlichungsziel auswählen** die folgenden Schritte aus, und wählen Sie **Profil importieren** aus. 

    ![Visual Studio: Schaltfläche „Profil importieren“](./media/service-bus-to-event-grid-integration-example/visual-studio-import-profile-button.png)
7. Wählen Sie die zuvor heruntergeladene **Veröffentlichungsprofildatei** aus. 
8. Wählen Sie auf der Seite **Veröffentlichen** den Befehl **Veröffentlichen** aus. 

    ![Visual Studio: Veröffentlichen](./media/service-bus-to-event-grid-integration-example/select-publish.png)
9. Vergewissern Sie sich, dass die neue Azure-Funktion **ReceiveMessagesOnEvent** angezeigt wird. Aktualisieren Sie die Seite bei Bedarf. 

    ![Vergewissern, dass die neue Funktion erstellt wurde](./media/service-bus-to-event-grid-integration-example/function-receive-messages.png)
10. Rufen Sie die URL zu der neuen Funktion ab, und notieren Sie sie. 

### <a name="event-grid-subscription"></a>Event Grid-Abonnement

1. Löschen Sie das vorhandene Event Grid-Abonnement:
    1. Wählen Sie auf der Seite **Service Bus-Namespace** im linken Menü die Option **Ereignisse** aus. 
    2. Wählen Sie das vorhandene Ereignisabonnement aus. 
    3. Wählen Sie auf der Seite **Ereignisabonnement** die Option **Löschen** aus.
2. Befolgen Sie die Anleitungen im Abschnitt [Verbinden von Funktion und Namespace per Event Grid](#connect-the-function-and-namespace-via-event-grid), um mithilfe der neuen Funktions-URL ein Event Grid-Abonnement zu erstellen.
3. Führen Sie die Anweisungen im Abschnitt [Senden von Nachrichten an das Service Bus-Thema](#send-messages-to-the-service-bus-topic) aus, um Nachrichten an das Thema zu senden und die Funktion zu überwachen. 

## <a name="receive-messages-by-using-logic-apps"></a>Empfangen von Nachrichten mithilfe von Logik-Apps
Gehen Sie wie folgt vor, um für eine Logik-App eine Verbindung mit Azure Service Bus und Azure Event Grid herzustellen:

1. Erstellen Sie im Azure-Portal eine Logik-App.
    1. Wählen Sie **+Ressource erstellen**, dann **Integration** und schließlich **Logik-App** aus. 
    2. Geben Sie auf der Seite **Logik-App – Erstellen** einen **Namen** für die Logik-App ein.
    3. Wählen Sie Ihr Azure- **Abonnement**aus. 
    4. Wählen Sie für die **Ressourcengruppe** **Vorhandene verwenden** aus, und wählen Sie die Ressourcengruppe aus, die Sie für andere Ressourcen (wie Azure-Funktion, Service Bus-Namespace) verwendet haben, die Sie zuvor erstellt haben. 
    5. Wählen Sie den **Speicherort** für die Logik-App aus. 
    6. Wählen Sie **Erstellen** aus, um die Logik-App zu erstellen. 
2. Wählen Sie auf der Seite **Designer für Logik-Apps** unter **Vorlagen** den Eintrag **Leere Logik-App** aus. 
3. Führen Sie im Designer folgende Schritte aus:
    1. Suchen Sie nach **Event Grid**. 
    2. Wählen Sie **Bei Auftreten eines Ressourcenereignisses (Vorschau) – Azure Event Grid** aus. 

        ![Designer für Logik-Apps: Event Grid-Trigger auswählen](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. Wählen Sie **Anmelden** aus, geben Sie Ihre Azure-Anmeldeinformationen ein, und wählen Sie **Zugriff zulassen** aus. 
5. Führen Sie auf der Seite **Bei Auftreten eines Ressourcenereignisses** die folgenden Schritte aus:
    1. Wählen Sie Ihr Azure-Abonnement. 
    2. Wählen Sie für **Ressourcentyp** den Wert **Microsoft.ServiceBus.Namespaces** aus. 
    3. Wählen Sie für **Ressourcenname** Ihren Service Bus-Namespace aus. 
    4. Wählen Sie **Neuen Parameter hinzufügen** und dann **Suffixfilter** aus. 
    5. Geben Sie für **Suffixfilter** den Namen Ihres zweiten Service Bus-Themenabonnements ein. 
        ![Designer für Logik-Apps: Ereignis konfigurieren](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. Wählen Sie im Designer **+ Neuer Schritt** aus, und führen Sie die folgenden Schritte aus:
    1. Suchen Sie nach **Service Bus**.
    2. Wählen Sie in der Liste **Service Bus** aus. 
    3. Wählen Sie in der Liste **Aktionen** den Eintrag **Nachrichten abrufen** aus. 
    4. Wählen Sie **Ruft Nachrichten aus einem Themenabonnement ab (Peek-Lock)** aus. 

        ![Designer für Logik-Apps: Aktion „Nachrichten abrufen“](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. Geben Sie einen **Namen für die Verbindung** ein. Beispiel:  **Rufen Sie Nachrichten aus einem Themenabonnement ab**, und wählen Sie den Service Bus-Namespace aus. 

        ![Designer für Logik-Apps: Service Bus-Namespace auswählen](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. Wählen Sie **RootManageSharedAccessKey**.

        ![Designer für Logik-Apps: Freigegebenen Zugriffsschlüssel auswählen](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    7. Klicken Sie auf **Erstellen**. 
    8. Wählen Sie Ihr Thema und Abonnement aus. 
    
        ![Designer für Logik-Apps: Service Bus-Thema und -Abonnement auswählen](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. Wählen Sie **+ Neuer Schritt** aus, und führen Sie die folgenden Schritte aus: 
    1. Wählen Sie **Service Bus** aus.
    2. Wählen Sie in der Liste der Aktionen **Nachricht in einem Themenabonnement abschließen** aus. 
    3. Wählen Sie Ihr Service Bus-**Thema** aus.
    4. Wählen Sie das zweite **Abonnement** für das Thema aus.
    5. Wählen Sie für **Sperrtoken der Nachricht** die Option **Sperrtoken** aus dem **Dynamischen Inhalt** aus. 

        ![Designer für Logik-Apps: Service Bus-Thema und -Abonnement auswählen](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. Wählen Sie in der Symbolleiste des Designers für Logik-Apps **Speichern** aus, um die Logik-App zu speichern. 
9. Befolgen Sie die Anleitungen im Abschnitt [Senden von Nachrichten an das Service Bus-Thema](#send-messages-to-the-service-bus-topic) aus, um Nachrichten an das Thema zu senden. 
10. Wechseln Sie zur Seite **Übersicht** Ihrer Logik-App. Sie sehen, dass die Logik-App ausgeführt wird, im **Ausführungsverlauf** für die gesendeten Nachrichten.

    ![Designer für Logik-Apps – Logik-App wird ausgeführt](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich ausführlicher über [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/).
* Erfahren Sie mehr über [Azure Functions](https://docs.microsoft.com/azure/azure-functions/).
* Weitere Informationen zum [Logic Apps-Feature von Azure App Service](https://docs.microsoft.com/azure/logic-apps/)
* Weitere Informationen zu [Azure Service Bus](https://docs.microsoft.com/azure/service-bus/)


[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
