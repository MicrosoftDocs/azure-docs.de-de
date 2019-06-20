---
title: Übersicht über Azure Service Bus-Messagingwarteschlangen, -themen und -abonnements | Microsoft-Dokumentation
description: Überblick über Service Bus-Nachrichtenentitäten
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/18/2018
ms.author: aschhab
ms.openlocfilehash: 7cacabf4f171189810e943043b5513e20113d962
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62125813"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Service Bus-Warteschlangen, -Themen und -Abonnements

Microsoft Azure Service Bus unterstützt einen Satz cloudbasierter, nachrichtenorientierter Middlewaretechnologien, darunter zuverlässiges Message Queuing und dauerhaftes Veröffentlichungs-/Abonnementmessaging. Diese Brokermessagingfunktionen kann man sich als asynchrone, entkoppelte Messagingfeatures vorstellen, die unter Verwendung der Service Bus-Messagingworkload verschiedene Szenarien wie Veröffentlichung/Abonnements, vorübergehende Entkopplung und Lastenausgleich unterstützen. Entkoppelte Kommunikation hat viele Vorteile, beispielsweise können Clients und Server Verbindungen nach Bedarf herstellen und Vorgänge asynchron ausführen.

Den Kern der Messagingfunktionen in Service Bus bilden die folgenden Messagingentitäten: Warteschlangen, Themen und Abonnements sowie Regeln/Aktionen.

## <a name="queues"></a>Warteschlangen

Warteschlangen liefern die Nachrichten im *First In, First Out*-Verfahren (FIFO) an einen oder mehrere Consumer. Das bedeutet, Nachrichten werden in der Reihenfolge an die Empfänger versendet und verarbeitet, in der sie der Warteschlange hinzugefügt wurden. Außerdem empfängt und verarbeitet jede Nachricht ein einziger Consumer. Als Hauptvorteil ergibt sich bei der Verwendung von Warteschlangen eine "vorübergehende Entkopplung" von Anwendungskomponenten. Anders ausgedrückt, die Producer (Absender) und Consumer (Empfänger) müssen Nachrichten nicht gleichzeitig senden und empfangen, da Nachrichten dauerhaft in der Warteschlange gespeichert werden. Außerdem muss der Producer nicht auf eine Antwort vom Consumer warten, um weiterhin Nachrichten zu verarbeiten und zu senden.

Ein weiterer Vorteil ist der Belastungsausgleich, durch den Producer und Consumer die Möglichkeit haben, Nachrichten mit unterschiedlichen Raten zu senden und zu empfangen. In vielen Anwendungen schwankt die Systemlast mit der Zeit, jedoch ist die erforderliche Verarbeitungsdauer pro Arbeitseinheit üblicherweise konstant. Durch die Zwischenschaltung einer Warteschlange zwischen Nachrichtenproducer und -consumer reicht es aus, wenn die konsumierende Anwendung für die Durchschnittslast anstatt für die Spitzenlast ausgelegt ist. Die Tiefe der Warteschlange erhöht und verringert sich mit der eingehenden Last. Dadurch sparen Sie direkt Geld, weil Sie weniger Infrastruktur für Ihre Anwendungslast benötigen. Mit zunehmender Last können weitere Arbeitsprozesse hinzugefügt werden, die Nachrichten aus der Warteschlange abrufen. Jede Nachricht wird nur von einem der Arbeitsprozesse verarbeitet. Außerdem ermöglicht dieser Pull-basierte Lastenausgleich eine optimale Nutzung der Workercomputer, selbst wenn diese über unterschiedliche Leistung verfügen, da jeder von ihnen die Nachrichten in der eigenen Maximalgeschwindigkeit abruft. Dieses Schema wird auch als „Konkurrierende Consumer“ bezeichnet.

Die Verwendung von Warteschlangen als Zwischenglied zwischen Nachrichtenproducern und -consumern sorgt für eine inhärente lockere Kopplung zwischen den Komponenten. Da Producer und Consumer voneinander unabhängig sind, kann ein Upgrade für einen Consumer ohne Auswirkungen auf den Producer durchgeführt werden.

### <a name="create-queues"></a>Erstellen von Warteschlangen

Sie können Warteschlangen über das [Azure-Portal](service-bus-quickstart-portal.md), [PowerShell](service-bus-quickstart-powershell.md), [CLI](service-bus-quickstart-cli.md) oder eine [Resource Manager-Vorlage](service-bus-resource-manager-namespace-queue.md) erstellen. Daraufhin werden Ihre Nachrichten mit einem [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient)-Objekt versendet und empfangen.

Die [Schnellstarts](service-bus-quickstart-portal.md) bieten einen umfassenden Überblick über die diversen Möglichkeiten, eine Warteschlange zu erstellen, und dann Nachrichten an die Warteschlange zu senden und von dieser zu empfangen. Ein ausführlicheres Tutorial zum Verwenden von Warteschlangen finden Sie unter [Erste Schritte mit Service Bus-Warteschlangen](service-bus-dotnet-get-started-with-queues.md).

Ein Anwendungsbeispiel finden Sie auf GitHub unter [BasicSendReceiveUsingQueueClient](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient).

### <a name="receive-modes"></a>Empfangsmodi

Sie können für den Nachrichtenempfang von Service Bus zwischen zwei Modi wählen: *ReceiveAndDelete* und *PeekLock*. Im [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode)-Modus ist der Nachrichtenempfang ein einstufiger Vorgang. Das heißt, wenn Service Bus die Anforderung erhält, wird die Nachricht als verarbeitet gekennzeichnet und an die Anwendung zurückgesendet. Der **ReceiveAndDelete**-Modus ist das einfachere Modell. Er eignet sich am besten für Szenarien, in denen eine Anwendung es toleriert, wenn eine Nachricht beim Auftreten eines Fehlers nicht verarbeitet wird. Beispiel: Ein Consumer stellt eine Empfangsanforderung aus und stürzt dann ab, bevor diese verarbeitet wird. Wenn die Anwendung neu gestartet wird und das Konsumieren von Nachrichten fortsetzt, entgeht ihr die vor dem Absturz konsumierte Nachricht, da Service Bus die Nachricht als konsumiert markiert hat.

Im [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode)-Modus ist der Empfangsvorgang zweistufig. Dadurch können Anwendungen unterstützt werden, die das Fehlen von Nachrichten nicht tolerieren können. Wenn Service Bus die Anforderung erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann an die Anwendung zurück. Nachdem die Anwendung die Nachricht verarbeitet oder zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat, führt sie die zweite Phase des Empfangsprozesses per Aufruf von [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) für die empfangene Nachricht durch. Wenn Service Bus den **CompleteAsync**-Aufruf erkennt, wird die Nachricht als verarbeitet markiert.

Wenn die Anwendung die Nachricht aus einem bestimmten Grund nicht verarbeiten kann, kann sie für die empfangene Nachricht anstelle der [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync)-Methode die [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync)-Methode aufrufen. So kann Service Bus die Nachricht entsperren und verfügbar machen, damit sie erneut empfangen werden kann – entweder von demselben oder von einem anderen konkurrierenden Consumer. Der Sperre ist außerdem ein Timeout zugeordnet. Wenn die Anwendung die Nachricht nicht vor Ablauf des Timeouts verarbeiten kann, z.B. weil sie abstürzt, entsperrt Service Bus die Nachricht und macht sie verfügbar, sodass sie wieder empfangen werden kann. Im Grunde erfolgt standardmäßig der Vorgang [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync).

Falls die Anwendung nach der Verarbeitung der Nachricht, jedoch vor Ausgabe der **CompleteAsync**-Anforderung abstürzt, wird die Nachricht der Anwendung beim Neustart wieder zugestellt. Dieser Vorgang wird auch als *At Least Once*-Verarbeitung bezeichnet. Das bedeutet, jede Nachricht wird mindestens einmal verarbeitet. In bestimmten Situationen wird dieselbe Nachricht jedoch möglicherweise erneut zugestellt. Wenn das Szenario keine doppelte Verarbeitung toleriert, muss in der Anwendung zusätzliche Logik zum Erkennen von Duplikaten vorhanden sein. Hierzu kann die Eigenschaft [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) der Nachricht verwendet werden, die über alle Zustellversuche hinweg konstant bleibt. Das ist die *Exactly Once*-Verarbeitung. Dabei wird eine Nachricht genau einmal verarbeitet.

## <a name="topics-and-subscriptions"></a>Themen und Abonnements

Im Gegensatz zu Warteschlangen, bei denen jede Nachricht von einem einzigen Consumer verarbeitet wird, bieten *Themen* und *Abonnements* eine 1:n-Kommunikationsform, die nach dem Muster *Veröffentlichen/Abonnieren* abläuft. Jede veröffentlichte Nachricht wird für jedes beim Thema registrierte Abonnement verfügbar gemacht, was für das Skalieren auf viele Empfänger hilfreich ist. Nachrichten werden an ein Thema gesendet und an ein oder mehrere zugeordnete Abonnements übermittelt, abhängig von Filterregeln, die für jedes Abonnement einzeln festlegt werden können. In Abonnements können die zu empfangenden Nachrichten mithilfe zusätzlicher Filter eingeschränkt werden. Nachrichten werden auf die gleiche Weise an ein Thema gesendet wie an eine Warteschlange, sie werden jedoch nicht direkt vom Thema empfangen. Stattdessen werden sie von Abonnements empfangen. Ein Themenabonnement ist mit einer virtuellen Warteschlange vergleichbar, die Kopien der an das Thema gesendeten Nachrichten empfängt. Nachrichten werden von einem Abonnement auf die gleiche Weise empfangen wie von einer Warteschlange.

Zum Vergleich: Die Sendefunktionalität einer Warteschlange ist direkt einem Thema zuzuordnen, und die Empfangsfunktionalität ist einem Abonnement zuzuordnen. Das bedeutet unter anderem, dass Abonnements das weiter oben beschriebene Muster hinsichtlich Warteschlangen unterstützen: konkurrierender Consumer, vorübergehende Entkopplung, Belastungsausgleich und Lastenausgleich.

### <a name="create-topics-and-subscriptions"></a>Erstellen von Themen und Abonnements

Das Erstellen eines Themas ähnelt dem Erstellen einer Warteschlange (siehe vorheriger Abschnitt). Nachdem das geschehen ist, senden Sie Nachrichten mithilfe der [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient)-Klasse. Erstellen Sie mindestens ein Abonnement für das Thema, um Nachrichten zu empfangen. Ähnlich wie bei Warteschlangen werden Nachrichten von einem Abonnement nicht mit einem [QueueClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient)-Objekt, sondern mit einem [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.queueclient)-Objekt empfangen. Erstellen Sie den Abonnementclient, und übergeben Sie dabei den Namen des Themas, den Namen des Abonnements und (optional) den Empfangsmodus als Parameter.

Ein Anwendungsbeispiel finden Sie auf GitHub unter [BasicSendReceiveUsingTopicSubscriptionClient](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingTopicSubscriptionClient).

### <a name="rules-and-actions"></a>Regeln und Aktionen

In vielen Fällen müssen Nachrichten mit bestimmten Merkmalen auf unterschiedliche Weise verarbeitet werden. Um dies zu ermöglichen, können Sie Abonnements so konfigurieren, dass sie nach Nachrichten mit bestimmten Eigenschaften suchen und dann bestimmte Änderungen an diesen Eigenschaften vornehmen. Auch wenn Service Bus-Abonnements alle Nachrichten angezeigt werden, die an das Thema gesendet wurden, können Sie nur eine Teilmenge dieser Nachrichten in die virtuelle Abonnementwarteschlange kopieren. Diese Filterung wird mithilfe von Abonnementfiltern erreicht. Solche Änderungen werden als *Filteraktionen* bezeichnet. Beim Erstellen eines Abonnements können Sie einen Filterausdruck angeben, der auf die Eigenschaften der Nachricht angewendet wird, und zwar sowohl auf die Systemeigenschaften (z.B. **Label**) als auch auf benutzerdefinierte Anwendungseigenschaften (z.B. **StoreName**.) Der SQL-Filterausdruck ist in diesem Fall optional. Ohne SQL-Filterausdruck wird jede für ein Abonnement definierte Filteraktion für alle Nachrichten in diesem Abonnement ausgeführt.

Ein Anwendungsbeispiel finden Sie auf GitHub unter [TopicSubscriptionWithRuleOperationsSample](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample).

Weitere Informationen über mögliche Filterwerte finden Sie in der Dokumentation zu den Klassen [SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) und [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen und Beispiele für die Verwendung von Service Bus-Messaging finden Sie in den folgenden erweiterten Themen:

* [Übersicht über Service Bus-Messaging](service-bus-messaging-overview.md)
* [Schnellstart: Senden und Empfangen von Nachrichten mit dem Azure-Portal und .NET](service-bus-quickstart-portal.md)
* [Tutorial: Aktualisieren des Bestands über das Azure-Portal und mithilfe von Themen/Abonnements](service-bus-tutorial-topics-subscriptions-portal.md)


