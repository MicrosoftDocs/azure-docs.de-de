---
title: Aufrufen oder Auslösen von Logik-Apps mit Azure Functions und Azure Service Bus
description: Erstellen von Azure-Funktionen zum Aufrufen oder Auslösen von Logik-Apps mithilfe von Azure Service Bus
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: jehollan, klam, LADocs
ms.topic: article
ms.assetid: 19cbd921-7071-4221-ab86-b44d0fc0ecef
ms.date: 06/04/2019
ms.openlocfilehash: 3d4f642ae25a179ea2c3241240996da774cd8c23
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494968"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>Aufrufen oder Auslösen von Logik-Apps mithilfe von Azure Functions und Azure Service Bus

Sie können [Azure Functions](../azure-functions/functions-overview.md) verwenden, um eine Logik-App auszulösen, wenn Sie einen Listener oder eine Aufgabe mit langer Ausführungsdauer bereitstellen müssen. Beispielsweise können Sie eine Azure-Funktion erstellen, die an einer [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)-Warteschlange lauscht und sofort eine Logik-App als Pushtrigger auslöst.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

* Ein Azure Service Bus-Namespace. Wenn Sie keinen Namespace besitzen, [erstellen Sie zunächst Ihren Namespace](../service-bus-messaging/service-bus-create-namespace-portal.md).

* Eine Azure Functions-App, bei der es sich um einen Container für Azure-Funktionen handelt. Wenn Sie keine Funktions-App besitzen, [erstellen Sie zuerst Ihre Funktions-App](../azure-functions/functions-create-first-azure-function.md), und stellen Sie sicher, dass Sie .NET als Laufzeitstapel auswählen.

* Grundlegende Kenntnisse über die [Erstellung von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="create-logic-app"></a>Erstellen einer Logik-App

In diesem Szenario haben Sie eine Funktion, die jede Logik-App ausführt, die Sie auslösen möchten. Erstellen Sie zunächst eine Logik-App, die mit einem HTTP-Anforderungstrigger beginnt. Die Funktion ruft jedes Mal, wenn eine Warteschlangennachricht empfangen wird, einen Endpunkt auf.  

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und erstellen Sie leere Logik-App.

   Falls Sie noch nicht mit Logik-Apps gearbeitet haben, lesen Sie zunächst das Dokument [Schnellstart: Erstellen Ihres ersten automatisierten Workflows mit Azure Logic Apps – Azure-Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Geben Sie im Suchfeld den Begriff „HTTP-Anforderung“. Wählen Sie in der Triggerliste den folgenden Trigger aus: **Beim Empfang einer HTTP-Anforderung**

   ![Trigger auswählen](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   Mit dem Anforderungstrigger können Sie optional ein JSON-Schema für die Verwendung mit der Warteschlangenmeldung eingeben. JSON-Schemas können dem Logik-App-Designer die Struktur der Eingabedaten verdeutlichen und erleichtern Ihnen die Verwendung der Ausgaben in Ihrem Workflow.

1. Zum Angeben eines Schemas geben Sie dieses im Feld **JSON-Schema für Anforderungstext** ein, z.B.:

   ![JSON-Schema angeben](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   Wenn Sie kein Schema haben, jedoch über eine Beispielnutzlast im JSON-Format verfügen, können Sie aus dieser Nutzlast ein Schema generieren.

   1. Wählen Sie im Anforderungstrigger **Beispielnutzlast zum Generieren eines Schemas verwenden**.

   1. Geben Sie unter **Geben oder fügen Sie eine JSON-Beispielnutzlast ein** Ihre Beispielnutzlast ein, und wählen Sie dann **Fertig**.

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

## <a name="create-azure-function"></a>Azure-Funktion erstellen

Als erstellen Sie eine Funktion, die als Trigger fungiert und an der Warteschlange lauscht.

1. Öffnen und erweitern Sie im Azure-Portal Ihre Funktionen-App, falls sie noch nicht geöffnet ist. 

1. Erweitern Sie unter den Namen Ihrer Funktionen-App **Funktionen**. Klicken Sie im Bereich **Funktionen** auf **Neue Funktion**.

   ![„Funktionen“ erweitern und „Neue Funktion“ auswählen](./media/logic-apps-scenario-function-sb-trigger/create-new-function.png)

1. Wählen Sie diese Vorlage auf der Grundlage aus, ob Sie eine Funktions-App erstellt haben, bei der Sie .NET als Laufzeitstapel ausgewählt haben, oder ob Sie eine vorhandene Funktions-App verwenden.

   * Für neue Funktions-Apps wählen diese Vorlage aus: **Service Bus-Warteschlangentrigger**

     ![Vorlage für neue Funktions-App auswählen](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * Wählen Sie für eine vorhandene Funktions-Apps diese Vorlage aus: **ServiceBus-Warteschlangentrigger – C#**

     ![Vorlage für vorhandene Funktions-App auswählen](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. Geben Sie im Bereich **Azure Service Bus-Warteschlangentrigger** einen Namen für Ihren Trigger an, und richten Sie die **Service Bus-Verbindung** für die Warteschlange ein, die den Azure Service Bus SDK-Listener `OnMessageReceive()` verwendet, und wählen Sie **Erstellen** aus.

1. Schreiben Sie eine einfache Funktion zum Aufrufen des zuvor erstellten Logik-App-Endpunkts, bei der die Warteschlangenmeldung als Trigger verwendet wird. Dieses Beispiel verwendet den Inhaltstyp `application/json` für Nachrichten, Sie können diesen jedoch bei Bedarf ändern. Wenn möglich, verwenden Sie die Instanz von HTTP-Clients wieder. Weitere Informationen finden Sie unter [Verwalten von Verbindungen in Azure Functions](../azure-functions/manage-connections.md).

   ```CSharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   // Callback URL for previously created Request trigger
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/workflows/<remaining-callback-URL>";

   // Reuse the instance of HTTP clients if possible
   private static HttpClient httpClient = new HttpClient();

   public static void Run(string myQueueItem, ILogger log)
   {
       log.LogInformation($"C# ServiceBus queue trigger function processed message: {myQueueItem}");

       var response = httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")).Result;
   }
   ```

1. Zum Testen der Funktion fügen Sie mithilfe eines Tools wie dem [Service Bus-Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) eine Warteschlangenmeldung hinzu.

   Die Logik-App wird unmittelbar nach dem Empfang der Meldung durch die Funktion ausgelöst.

## <a name="next-steps"></a>Nächste Schritte

[Aufrufen, Auslösen oder Schachteln von Workflows mithilfe von HTTP-Endpunkten](../logic-apps/logic-apps-http-endpoint.md)