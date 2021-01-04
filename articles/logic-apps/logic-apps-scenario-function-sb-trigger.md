---
title: Aufrufen von Logik-Apps mit Azure Functions
description: Aufrufen oder Auslösen von Logik-Apps mithilfe von Azure Functions und Azure Service Bus
services: logic-apps
ms.suite: integration
ms.reviewer: jehollan, klam, logicappspm
ms.topic: article
ms.date: 11/08/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 25f761d85ebfd0ac16f182941c5b5c29636066bf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000333"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>Aufrufen oder Auslösen von Logik-Apps mithilfe von Azure Functions und Azure Service Bus

Sie können [Azure Functions](../azure-functions/functions-overview.md) verwenden, um eine Logik-App auszulösen, wenn Sie einen Listener oder eine Aufgabe mit langer Ausführungsdauer bereitstellen müssen. Beispielsweise können Sie eine Funktion erstellen, die an einer [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)-Warteschlange lauscht und sofort eine Logik-App als Pushtrigger auslöst.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

* Ein Azure Service Bus-Namespace. Wenn Sie keinen Namespace besitzen, [erstellen Sie zunächst Ihren Namespace](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Eine Funktions-App, bei der es sich um einen Container für Ihre Funktionen handelt. Wenn Sie keine Funktions-App besitzen, [erstellen Sie zuerst Ihre Funktions-App](../azure-functions/functions-create-first-azure-function.md), und stellen Sie sicher, dass Sie .NET als Laufzeitstapel auswählen.

* Grundlegende Kenntnisse über die [Erstellung von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-logic-app"></a>Erstellen einer Logik-App

In diesem Szenario haben Sie eine Funktion, die jede Logik-App ausführt, die Sie auslösen möchten. Erstellen Sie zunächst eine Logik-App, die mit einem HTTP-Anforderungstrigger beginnt. Die Funktion ruft jedes Mal, wenn eine Warteschlangennachricht empfangen wird, einen Endpunkt auf.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und erstellen Sie leere Logik-App.

   Falls Sie noch nicht mit Logik-Apps gearbeitet haben, lesen Sie zunächst das Dokument [Schnellstart: Erstellen Ihres ersten automatisierten Workflows mit Azure Logic Apps – Azure-Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Geben Sie im Suchfeld `http request`ein. Wählen Sie aus der Triggerliste diesen Trigger aus: **Beim Empfang einer HTTP-Anforderung**.

   ![Trigger auswählen](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   Mit dem Anforderungstrigger können Sie optional ein JSON-Schema für die Verwendung mit der Warteschlangenmeldung eingeben. JSON-Schemas können dem Logik-App-Designer die Struktur der Eingabedaten verdeutlichen und erleichtern Ihnen die Verwendung der Ausgaben in Ihrem Workflow.

1. Zum Angeben eines Schemas geben Sie dieses im Feld **JSON-Schema für Anforderungstext** ein, z.B.:

   ![JSON-Schema angeben](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   Wenn Sie kein Schema haben, jedoch über eine Beispielnutzlast im JSON-Format verfügen, können Sie aus dieser Nutzlast ein Schema generieren.

   1. Wählen Sie im Anforderungstrigger **Beispielnutzlast zum Generieren eines Schemas verwenden** aus.

   1. Geben Sie unter **Geben oder fügen Sie eine JSON-Beispielnutzlast ein** Ihre Beispielnutzlast ein, und wählen Sie dann **Fertig** aus.

      ![Beispielnutzlast eingeben](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

   Diese Beispielnutzlast generiert dieses Schema, das im Trigger angezeigt wird:

   ```json
   {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "number": {
                  "type": "integer"
               },
               "street": {
                  "type": "string"
               },
               "city": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "integer"
               },
               "country": {
                  "type": "string"
               }
            }
         }
      }
   }
   ```

1. Fügen Sie alle weiteren Aktionen hinzu, die nach dem Empfang der Warteschlangenmeldung ausgeführt werden sollen.

   Beispielsweise können Sie eine E-Mail mit dem Office 365 Outlook-Connector senden.

1. Speichern Sie Ihre Logik-App, um die Rückruf-URL für den Trigger in dieser Logik-App zu generieren. Später verwenden Sie diese Rückruf-URL in dem Code für den Azure Service Bus-Warteschlangentrigger.

   Die Rückruf-URL kommt in der Eigenschaft **HTTP POST URL** vor.

   ![Generierte Rückruf-URL für Trigger](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-a-function"></a>Erstellen einer Funktion

Als erstellen Sie eine Funktion, die als Trigger fungiert und an der Warteschlange lauscht.

1. Öffnen und erweitern Sie im Azure-Portal Ihre Funktionen-App, falls sie noch nicht geöffnet ist. 

1. Erweitern Sie unter den Namen Ihrer Funktionen-App **Funktionen**. Wählen Sie im Bereich **Funktionen** die Option **Neue Funktion** aus.

   ![„Funktionen“ erweitern und „Neue Funktion“ auswählen](./media/logic-apps-scenario-function-sb-trigger/add-new-function-to-function-app.png)

1. Wählen Sie diese Vorlage auf der Grundlage aus, ob Sie eine Funktions-App erstellt haben, bei der Sie .NET als Laufzeitstapel ausgewählt haben, oder ob Sie eine vorhandene Funktions-App verwenden.

   * Für neue Funktions-Apps wählen diese Vorlage aus: **Service Bus-Warteschlangentrigger**

     ![Vorlage für neue Funktions-App auswählen](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * Wählen Sie für eine vorhandene Funktions-Apps diese Vorlage aus: **ServiceBus-Warteschlangentrigger – C#**

     ![Vorlage für vorhandene Funktions-App auswählen](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. Geben Sie im Bereich **Azure Service Bus-Warteschlangentrigger** einen Namen für Ihren Trigger an, und richten Sie die **Service Bus-Verbindung** für die Warteschlange ein, die den Azure Service Bus SDK-Listener `OnMessageReceive()` verwendet, und wählen Sie **Erstellen** aus.

1. Schreiben Sie eine einfache Funktion zum Aufrufen des zuvor erstellten Logik-App-Endpunkts, bei der die Warteschlangenmeldung als Trigger verwendet wird. Überprüfen Sie vor dem Schreiben Ihrer Funktion die folgenden Punkte:

   * Dieses Beispiel verwendet den Inhaltstyp `application/json` für Nachrichten, Sie können diesen jedoch bei Bedarf ändern.
   
   * Vermeiden Sie wegen möglicherweise gleichzeitig ausgeführter Funktionen, hoher Volumen oder starker Auslastungen das Instanziieren der [HttpClient-Klasse](/dotnet/api/system.net.http.httpclient) mit der `using`-Anweisung sowie das direkte Erstellen von HttpClient-Instanzen pro Anforderung. Weitere Informationen finden Sie unter [Verwenden von HttpClientFactory zum Implementieren robuster HTTP-Anforderungen](/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests#issues-with-the-original-httpclient-class-available-in-net-core).
   
   * Wenn möglich, verwenden Sie die Instanz von HTTP-Clients wieder. Weitere Informationen finden Sie unter [Verwalten von Verbindungen in Azure Functions](../azure-functions/manage-connections.md).

   In diesem Beispiel wird die [`Task.Run`-Methode](/dotnet/api/system.threading.tasks.task.run) im [asynchronen](/dotnet/csharp/language-reference/keywords/async) Modus verwendet. Weitere Informationen finden Sie unter [Asynchrone Programmierung mit async und await](/dotnet/csharp/programming-guide/concepts/async/).

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   // Can also fetch from App Settings or environment variable
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/workflows/<remaining-callback-URL>";

   // Reuse the instance of HTTP clients if possible: https://docs.microsoft.com/azure/azure-functions/manage-connections
   private static HttpClient httpClient = new HttpClient();

   public static async Task Run(string myQueueItem, TraceWriter log) 
   {
      log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
      var response = await httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")); 
   }
   ```

1. Zum Testen der Funktion fügen Sie mithilfe eines Tools wie dem [Service Bus-Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) eine Warteschlangenmeldung hinzu.

   Die Logik-App wird unmittelbar nach dem Empfang der Meldung durch die Funktion ausgelöst.

## <a name="next-steps"></a>Nächste Schritte

* [Aufrufen, Auslösen oder Schachteln von Workflows mithilfe von HTTP-Endpunkten](../logic-apps/logic-apps-http-endpoint.md)
