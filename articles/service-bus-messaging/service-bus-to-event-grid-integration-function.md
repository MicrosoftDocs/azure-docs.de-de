---
title: Verarbeiten von Service Bus-Ereignissen über Event Grid mit Azure Functions
description: Dieser Artikel enthält Schritte zum Verarbeiten von Service Bus-Ereignissen über Event Grid mit Azure Functions.
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: afc0a5bf9b83363d1f4baab955b55148fe3a8498
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "95818480"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions"></a>Tutorial: Reagieren auf über Azure Event Grid empfangene Azure Service Bus-Ereignisse mit Azure Functions
In diesem Tutorial erfahren Sie, wie Sie mithilfe von Azure Functions und Azure Logic Apps auf Azure Service Bus-Ereignisse reagieren, die über Azure Event Grid empfangen wurden. 

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Erstellen eines Service Bus-Namespace
> * Vorbereiten einer Beispielanwendung zum Senden von Nachrichten.
> * Senden von Nachricht an das Service Bus-Thema
> * Empfangen von Nachrichten mithilfe von Logik-Apps
> * Einrichten einer Testfunktion in Azure
> * Verbinden der Funktion und des Namespace per Event Grid
> * Empfangen von Nachrichten per Azure Functions

[!INCLUDE [service-bus-event-grid-prerequisites](../../includes/service-bus-event-grid-prerequisites.md)]

## <a name="additional-prerequisites"></a>Zusätzliche Voraussetzungen
Installieren von [Visual Studio 2019](https://www.visualstudio.com/vs) mit der Workload **Azure-Entwicklung**. Diese Workload umfasst **Azure Function-Tools**, die Sie zum Erstellen, Kompilieren und Bereitstellen von Azure Functions-Projekten in Visual Studio benötigen. 

## <a name="deploy-the-function-app"></a>Bereitstellen der Funktionen-App 

>[!NOTE]
> Weitere Informationen zum Erstellen und Bereitstellen einer Azure Functions-App finden Sie unter [Entwickeln von Azure Functions mithilfe von Visual Studio](../azure-functions/functions-develop-vs.md).

1. Öffnen Sie die Datei **ReceiveMessagesOnEvent.cs** aus dem Projekt **FunctionApp1** der Projektmappe **SBEventGridIntegration.sln**. 
1. Ersetzen Sie `<SERCICE BUS NAMESPACE - CONNECTION STRING>` durch die Verbindungszeichenfolge für Ihren Service Bus-Namespace. Sie sollte mit der Verbindungszeichenfolge identisch sein, die Sie in der Datei **Program.cs** des Projekts **MessageSender** in der gleichen Projektmappe verwendet haben. 
1. Klicken Sie mit der rechten Maustaste auf **FunctionApp1**, und wählen Sie **Veröffentlichen** aus. 
1. Wählen Sie auf der Seite **Veröffentlichen** die Option **Starten** aus. Diese Schritte unterscheiden sich möglicherweise von dem, was bei Ihnen angezeigt wird, aber der Veröffentlichungsprozess sollte ähnlich sein. 
1. Wählen Sie im **Veröffentlichungs-Assistenten** auf der Seite **Ziel** die Option **Azure** als **Ziel** aus. 
1. Wählen Sie auf der Seite **Spezifisches Ziel** die Option **Azure-Funktions-App (Windows)** aus. 
1. Wählen Sie auf der Seite **Functions-Instanz** die Option **Neue Azure-Funktion erstellen** aus. 
1. Führen Sie auf der Seite **Funktions-App (Windows)** die folgenden Schritte aus:
    1. Geben Sie einen **Namen** für die Funktions-App aus.
    1. Wählen Sie ein Azure-**Abonnement** aus.
    1. Wählen Sie eine vorhandene **Ressourcengruppe** aus, oder erstellen Sie eine neue Ressourcengruppe. Wählen Sie für dieses Tutorial die Ressourcengruppe aus, die über den Service Bus-Namespace verfügt. 
    1. Wählen Sie einen **Plantyp** für App Service aus.
    1. Wählen Sie einen **Standort** aus. Wählen Sie den gleichen Speicherort wie für den Service Bus-Namespace aus. 
    1. Wählen Sie ein vorhandenes **Azure Storage-Konto** aus, oder wählen Sie **Neu** aus, um ein neues Storage-Konto zu erstellen, das von der Functions-App verwendet werden soll. 
    1. Wählen Sie **Erstellen** aus, um die Functions-App zu erstellen. 
1. Wählen Sie auf der Seite **Functions-Instanz** des **Veröffentlichungs-Assistenten** die Option **Fertig stellen** aus. 
1. Wählen Sie auf der Seite **Veröffentlichen** in Visual Studio **Veröffentlichen** aus, um die Functions-App in Azure zu veröffentlichen. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/publish-function-app.png" alt-text="Veröffentlichen einer Functions-App":::    
1. Überprüfen Sie im Fenster **Ausgabe** die Meldungen des Kompilierungs- und Veröffentlichungsvorgangs, und vergewissern Sie sich, dass beide erfolgreich waren. 
1. Wählen Sie nun auf der Seite **Veröffentlichen** die Option **Im Azure-Portal verwalten** aus. 
1. Wählen Sie im Azure-Portal auf der Seite **Funktions-App** im linken Menü **Funktionen** aus, und vergewissern Sie sich, dass zwei Funktionen angezeigt werden: 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/functions.png" alt-text="Zwei Funktionen für einen Handler für einen Event Grid-Trigger und einen HTTP-Trigger":::

    > [!NOTE]
    > Die `EventGridTriggerFunction` verwendet einen [Event Grid-Trigger](../azure-functions/functions-bindings-event-grid-trigger.md), und die `HTTPTriggerFunction` verwendet einen [HTTP-Trigger](../azure-functions/functions-bindings-http-webhook-trigger.md).
1. Wählen Sie **EventGridTriggerFunction** aus der Liste aus. Wir empfehlen die Verwendung des Event Grid-Triggers mit Azure Functions, da seine Verwendung im Vergleich zum HTTP-Trigger einige Vorteile bietet. Weitere Informationen finden Sie unter [Azure-Funktion als Ereignishandler für Event Grid-Ereignisse](../event-grid/handler-functions.md).
1. Wählen Sie auf der Seite **Funktion** für die **EventGridTriggerFunction** im linken Menü **Überwachen** aus. 
1. Wählen Sie **Konfigurieren** zum Konfigurieren von Application Insights zum Erfassen des Aufrufprotokolls aus. Sie verwenden diese Seite, um die Funktionsausführungen beim Empfang von Service-Bus-Ereignissen von Event Grid zu überwachen. 
1. Geben Sie auf der Seite **Application Insights** einen Namen für die Ressource ein, wählen Sie einen **Speicherort** für die Ressource aus, und wählen Sie dann **OK** aus. 
1. Wählen Sie im oberen Bereich (Breadcrumb-Menü) die Funktion **EventGridTriggerFunction** aus, um zurück zur Seite **Funktion** zu navigieren. 
1. Vergewissern Sie sich, dass Sie sich auf der Seite **Überwachen** befinden. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/monitor-before.png" alt-text="Seite „Überwachen“ für die Funktion vor Funktionsaufrufen":::
    
    Lassen Sie diese Seite auf einer Registerkarte in Ihrem Webbrowser geöffnet. Sie aktualisieren diese Seite später, um Aufrufe für diese Funktion anzuzeigen.

## <a name="connect-the-function-and-namespace-via-event-grid"></a>Verbinden der Funktion und des Namespace per Event Grid
In diesem Abschnitt verknüpfen Sie die Funktion und den Service Bus-Namespace unter Verwendung des Azure-Portals. 

Gehen Sie wie folgt vor, um ein Azure Event Grid-Abonnement zu erstellen:

1. Wechseln Sie im Azure-Portal zu Ihrem Namespace, und wählen Sie im Bereich auf der linken Seite dann **Ereignisse** aus. Ihr Namespacefenster wird geöffnet, und im rechten Bereich werden zwei Event Grid-Abonnements angezeigt. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png" alt-text="Service Bus: Seite „Ereignisse“":::
2. Wählen Sie in der Symbolleiste **+ Ereignisabonnement** aus. 
3. Führen Sie auf der Seite **Ereignisabonnement erstellen** die folgenden Schritte aus:
    1. Geben Sie einen **Namen** für das Abonnement ein. 
    2. Geben Sie unter **Name** einen Namen für das **Systemthema** ein. Systemthemen werden für Azure-Ressourcen wie Azure Storage-Konten und Azure Service Bus erstellt. Weitere Informationen zu Systemthemen finden Sie unter [Systemthemen in Azure Event Grid](../event-grid/system-topics.md).
    2. Wählen Sie **Azure-Funktion** als **Endpunkttyp** aus, und klicken Sie auf **Endpunkt auswählen**. 

        ![Service Bus: Event Grid-Abonnement](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. Wählen Sie auf der Seite **Azure-Funktion auswählen** das Abonnement, die Ressourcengruppe, die Funktions-App, den Slot und die Funktion aus, und wählen Sie dann **Auswahl bestätigen** aus. 

        ![Funktion: Endpunkt auswählen](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. Wechseln Sie auf der Seite **Ereignisabonnement erstellen** zur Registerkarte **Filter**, und führen Sie die folgenden Aufgaben aus:
        1. Wählen Sie **Betrefffilter aktivieren** aus.
        2. Geben Sie den Namen des Abonnements für das zuvor erstellte Service Bus-Thema (**S1**) ein.
        3. Wählen Sie die Schaltfläche **Erstellen**. 

            ![Ereignisabonnementfilter](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. Wechseln Sie auf der Registerkarte **Ereignisabonnements** zur Seite **Ereignisse**, und vergewissern Sie sich, dass das Ereignisabonnement in der Liste angezeigt wird.

    ![Ereignisabonnement in der Liste](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="monitor-the-functions-app"></a>Überwachen der Funktions-App
Die Nachrichten, die Sie zuvor an das Service Bus-Thema gesendet haben, werden an das Abonnement (S1) weitergeleitet. Event Grid leitet die Nachrichten an das Abonnement für die Azure-Funktion weiter. In diesem Schritt des Tutorials bestätigen Sie, dass die Funktion aufgerufen wurde, und zeigen die protokollierten Informationsmeldungen an. 

1. Wechseln Sie auf der Seite für Ihre Azure-Funktions-App zur Registerkarte **Überwachen**, wenn diese nicht bereits aktiv ist. Für jede im Service Bus-Thema gepostete Nachricht sollte ein Eintrag angezeigt werden. Ist dies nicht der Fall, aktualisieren Sie die Seite nach einigen Minuten. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/function-monitor.png" alt-text="Seite „Überwachen“ für die Funktion nach Aufrufen":::
2. Wählen Sie den Aufruf in der Liste aus, um die Details anzuzeigen. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/invocation-details.png" alt-text="Details des Funktionsaufrufs" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::

    Sie können die Protokollierungsinformationen auch über die Registerkarte **Protokolle** auf der Seite **Monitor** anzeigen, wenn die Nachrichten gesendet werden. Es kann eine Verzögerung auftreten. Daher kann es einige Minuten dauern, bis die protokollierten Nachrichten angezeigt werden. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/function-logs.png" alt-text="Funktionsprotokolle" lightbox="./media/service-bus-to-event-grid-integration-example/function-logs.png":::

## <a name="troubleshoot"></a>Problembehandlung
Führen Sie die folgenden Schritte aus, falls keine Funktionsaufrufe angezeigt werden, nachdem Sie etwas abgewartet und eine Aktualisierung durchgeführt haben: 

1. Vergewissern Sie sich, dass die Nachrichten das Service Bus-Thema erreicht haben. Weitere Informationen finden Sie auf der Seite **Service Bus-Thema** unter dem Zähler **Eingehende Nachrichten**. In diesem Fall habe ich die Anwendung **MessageSender** zwei Mal ausgeführt, sodass zehn Nachrichten angezeigt werden (fünf Nachrichten pro Ausführung).

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/topic-incoming-messages.png" alt-text="Seite „Service Bus-Thema“: Eingehende Nachrichten":::    
1. Vergewissern Sie sich, dass für das Service Bus-Abonnement **keine aktiven Nachrichten** vorhanden sind. 
    Falls auf dieser Seite keine Ereignisse angezeigt werden, sollten Sie sich vergewissern, dass auf der Seite **Service Bus-Abonnement** kein Wert für **Anzahl aktiver Nachrichten** angezeigt wird. Wenn die Anzahl für diesen Zähler größer als Null ist, werden die Nachrichten des Abonnements aus irgendeinem Grund nicht an die Handlerfunktion (Handler für Ereignisabonnement) weitergeleitet. Vergewissern Sie sich, dass Sie das Ereignisabonnement richtig eingerichtet haben. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/subscription-active-message-count.png" alt-text="Anzahl aktiver Nachrichten für Service Bus-Abonnement":::    
1. Darüber hinaus werden **übermittelte Ereignisse** auf der Seite **Ereignisse** des Service Bus-Namespace angezeigt. 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-page.png" alt-text="Seite „Ereignisse“: Übermittelte Ereignisse" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::
1. Sie können auch auf der Seite **Ereignisabonnement** verfolgen, dass die Ereignisse übermittelt wurden. Sie können auf diese Seite zugreifen, indem Sie auf der Seite **Ereignisse** das Ereignisabonnement auswählen. 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-delivered-events.png" alt-text="Seite „Ereignisabonnement“: Übermittelte Ereignisse":::
    


## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich ausführlicher über [Azure Event Grid](../event-grid/index.yml).
* Erfahren Sie mehr über [Azure Functions](../azure-functions/index.yml).
* Weitere Informationen zum [Logic Apps-Feature von Azure App Service](../logic-apps/index.yml)
* Weitere Informationen zu [Azure Service Bus](/azure/service-bus/)


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