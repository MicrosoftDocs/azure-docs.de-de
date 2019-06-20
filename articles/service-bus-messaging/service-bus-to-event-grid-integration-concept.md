---
title: Übersicht über die Integration von Azure Service Bus in Event Grid | Microsoft-Dokumentation
description: Beschreibung der Integration von Service Bus-Messaging in Event Grid
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: aschhab
ms.openlocfilehash: 9df321980db3a2481f0d8cc007546822fea46f9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66111176"
---
# <a name="azure-service-bus-to-event-grid-integration-overview"></a>Übersicht über die Integration von Azure Service Bus in Event Grid

Für Azure Service Bus wurde eine neue Integration in Azure Event Grid eingeführt. Die zentrale Bedeutung dieses Features besteht in einem Szenario, in dem Service Bus-Warteschlangen oder Abonnements mit einer geringen Anzahl von Nachrichten keinen Empfänger benötigen, der kontinuierlich Nachrichten abfragt. 

Service Bus kann nun Ereignisse an Event Grid ausgeben, wenn Nachrichten in einer Warteschlange oder in einem Abonnement vorliegen und keine Empfänger vorhanden sind. Sie können Event Grid-Abonnements für Ihre Service Bus-Namespaces erstellen, auf diese Ereignisse lauschen und als Reaktion auf die Ereignisse einen Empfänger starten. Mit diesem Feature können Sie Service Bus in reaktiven Programmiermodellen verwenden.

Um das Feature aktivieren zu können, benötigen Sie Folgendes:

* Einen Service Bus Premium-Namespace mit mindestens einer Service Bus-Warteschlange oder ein Service Bus-Thema mit mindestens einem Abonnement
* Zugriff für Mitwirkende auf den Service Bus-Namespace
* Außerdem benötigen Sie ein Event Grid-Abonnement für den Service Bus-Namespace. Dieses Abonnement erhält eine Benachrichtigung von Event Grid, wenn abzurufende Nachrichten verfügbar sind. Zu den typischen Abonnenten zählen beispielsweise das Logic Apps-Feature von Azure App Service, Azure Functions oder ein Webhook, der eine Web-App kontaktiert. Die Nachrichten werden dann vom Abonnenten verarbeitet. 

![19][]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="verify-that-you-have-contributor-access"></a>Überprüfen, ob Sie über Zugriff für Mitwirkende verfügen
Navigieren Sie zu Ihrem Service Bus-Namespace, und wählen Sie **Zugriffssteuerung (IAM)** und dann die Registerkarte **Rollenzuweisungen** aus: Vergewissern Sie sich, dass Sie über Zugriff für Mitwirkende auf den Namespace verfügen. 

### <a name="events-and-event-schemas"></a>Ereignisse und Ereignisschemas

Service Bus sendet derzeit Ereignisse für zwei Szenarien:

* [ActiveMessagesWithNoListenersAvailable](#active-messages-available-event)
* DeadletterMessagesAvailable

Darüber hinaus verwendet Service Bus die Standardsicherheit und die [Authentifizierungsmechanismen](https://docs.microsoft.com/azure/event-grid/security-authentication) von Event Grid.

Weitere Informationen finden Sie unter [Azure Event Grid-Ereignisschema](https://docs.microsoft.com/azure/event-grid/event-schema).

#### <a name="active-messages-available-event"></a>Ereignis: Aktive Nachrichten verfügbar

Dieses Ereignis wird generiert, wenn aktive Nachrichten in einer Warteschlange oder in einem Abonnement vorliegen und keine lauschenden Empfänger vorhanden sind.

Das Schema für dieses Ereignis sieht wie folgt aus:

```JSON
{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}
```

#### <a name="dead-letter-messages-available-event"></a>Ereignis: Unzustellbare Nachrichten verfügbar

Sie erhalten mindestens ein Ereignis pro Warteschlange für unzustellbare Nachrichten, die Nachrichten enthält und über keine aktiven Empfänger verfügt.

Das Schema für dieses Ereignis sieht wie folgt aus:

```JSON
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="how-many-events-are-emitted-and-how-often"></a>Wie viele Ereignisse werden ausgegeben, und wie oft?

Wenn der Namespace mehrere Warteschlangen und Themen oder Abonnements enthält, erhalten Sie mindestens ein Ereignis pro Warteschlange und ein Ereignis pro Abonnement. Die Ereignisse werden sofort ausgegeben, wenn in der Service Bus-Entität keine Nachrichten vorhanden sind und eine neue Nachricht eingeht. Andernfalls werden Ereignisse alle zwei Minuten ausgegeben – es sei denn, Service Bus erkennt einen aktiven Empfänger. Durch das Einsehen von Nachrichten werden die Ereignisse nicht unterbrochen.

Standardmäßig gibt Service Bus Ereignisse für alle Entitäten im Namespace aus. Wenn Sie nur Ereignisse für bestimmte Entitäten abrufen möchten, lesen Sie den folgenden Abschnitt.

### <a name="use-filters-to-limit-where-you-get-events-from"></a>Verwenden von Filtern, um einzuschränken, von wo Ereignisse abgerufen werden

Wenn Sie beispielsweise nur Ereignisse aus einer bestimmten Warteschlange oder aus einem bestimmten Abonnement innerhalb Ihres Namespace abrufen möchten, können Sie den Event Grid-Filter *Beginnt mit* oder *Endet mit* verwenden. Bei einigen Schnittstellen werden die Filter als *Präfix* und *Suffix* bezeichnet. Wenn Sie Ereignisse für mehrere (aber nicht für alle) Warteschlangen und Abonnements abrufen möchten, können Sie mehrere Event Grid-Abonnements erstellen und diese jeweils mit einem Filter versehen.

## <a name="create-event-grid-subscriptions-for-service-bus-namespaces"></a>Erstellen von Event Grid-Abonnements für Service Bus-Namespaces

Event Grid-Abonnements für Service Bus-Namespaces können auf drei Arten erstellt werden:

* Im Azure-Portal
* Über die [Azure-Befehlszeilenschnittstelle](#azure-cli-instructions)
* In [PowerShell](#powershell-instructions)

## <a name="azure-portal-instructions"></a>Anleitung für das Azure-Portal

Gehen Sie wie folgt vor, um ein neues Event Grid-Abonnement zu erstellen:
1. Navigieren Sie im Azure-Portal zu Ihrem Namespace.
2. Klicken Sie im linken Bereich auf **Event Grid**. 
3. Klicken Sie auf **Ereignisabonnement**.  

   Die folgende Abbildung zeigt einen Namespace mit einem Event Grid-Abonnement:

   ![Event Grid-Abonnements](./media/service-bus-to-event-grid-integration-concept/sbtoeventgridportal.png)

   Die folgende Abbildung zeigt, wie Sie eine Funktion oder einen Webhook ohne spezielle Filterung abonnieren:

   ![21][]

## <a name="azure-cli-instructions"></a>Anleitung für die Azure-Befehlszeilenschnittstelle

Vergewissern Sie sich zunächst, dass mindestens die Version 2.0 der Azure-Befehlszeilenschnittstelle installiert ist. [Laden Sie das Installationsprogramm herunter.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Drücken Sie **WINDOWS-TASTE+X**, und öffnen Sie eine neue PowerShell-Konsole mit Administratorberechtigungen. Alternativ können Sie auch eine Befehlsshell im Azure-Portal verwenden.

Führen Sie den folgenden Code aus:

 ```azurecli-interactive
az login

az account set -s “THE SUBSCRIPTION YOU WANT TO USE”

$namespaceid=(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name “<yourNamespace>“--resource-group “<Your Resource Group Name>” --query id --output tsv)

az eventgrid event-subscription create --resource-id $namespaceid --name “<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>” --endpoint “<your_function_url>” --subject-ends-with “<YOUR SERVICE BUS SUBSCRIPTION NAME>”
```

## <a name="powershell-instructions"></a>Anleitung für PowerShell

Vergewissern Sie sich, dass Azure PowerShell installiert ist. [Laden Sie das Installationsprogramm herunter.](https://docs.microsoft.com/powershell/azure/install-Az-ps) Drücken Sie **WINDOWS-TASTE+X**, und öffnen Sie eine neue PowerShell-Konsole mit Administratorberechtigungen. Alternativ können Sie auch eine Befehlsshell im Azure-Portal verwenden.

```powershell-interactive
Connect-AzAccount

Select-AzSubscription -SubscriptionName "<YOUR SUBSCRIPTION NAME>"

# This might be installed already
Install-Module Az.ServiceBus

$NSID = (Get-AzServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -Na
mespaceName "<YOUR NAMESPACE NAME>").Id

New-AzEVentGridSubscription -EventSubscriptionName “<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>” -ResourceId $NSID -Endpoint "<YOUR FUNCTION URL>” -SubjectEndsWith “<YOUR SERVICE BUS SUBSCRIPTION NAME>”
```

Als Nächstes können Sie sich die anderen Setupoptionen ansehen oder sich vergewissern, dass Ereignisse fließen.

## <a name="next-steps"></a>Nächste Schritte

* Rufen Sie [Beispiele](service-bus-to-event-grid-integration-example.md) für Service Bus und Event Grid ab.
* Informieren Sie sich ausführlicher über [Event Grid](https://docs.microsoft.com/azure/event-grid/).
* Erfahren Sie mehr über [Azure Functions](https://docs.microsoft.com/azure/azure-functions/).
* Informieren Sie sich ausführlicher über [Logic Apps](https://docs.microsoft.com/azure/logic-apps/).
* Erfahren Sie mehr über [Service Bus](https://docs.microsoft.com/azure/service-bus/).

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[19]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
