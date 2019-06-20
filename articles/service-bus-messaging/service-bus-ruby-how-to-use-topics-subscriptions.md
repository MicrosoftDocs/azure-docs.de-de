---
title: Verwenden von Service Bus-Themen (Ruby) | Microsoft Docs
description: Erfahren Sie mehr zur Verwendung von Service Bus-Themen und -Abonnements in Azure. Die Codebeispiele wurden für Ruby-Anwendungen geschrieben.
services: service-bus-messaging
documentationcenter: ruby
author: axisc
manager: timlt
editor: ''
ms.assetid: 3ef2295e-7c5f-4c54-a13b-a69c8045d4b6
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: fa3e50374c47f863923252a47b4b54fc1e18f87d
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991862"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-ruby"></a>Verwenden von Service Bus-Themen und -Abonnements mit Ruby
 
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

In diesem Artikel erfahren Sie, wie Sie Service Bus-Themen und -Abonnements aus Ruby-Anwendungen verwenden. Folgende Szenarien werden behandelt:

- Erstellen von Themen und Abonnements 
- Erstellen von Abonnementfiltern 
- Senden von Nachrichten an ein Thema 
- Empfangen von Nachrichten aus einem Abonnement
- Löschen von Themen und Abonnements


## <a name="prerequisites"></a>Voraussetzungen
1. Ein Azure-Abonnement. Um dieses Tutorial abzuschließen, benötigen Sie ein Azure-Konto. Sie können [Ihre Visual Studio-oder MSDN-Abonnentenvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) oder [sich für ein kostenloses Konto anmelden](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Befolgen Sie die Schritte im [Schnellstart: Verwenden Sie das Azure-Portal, um ein Service Bus-Thema und -Abonnements für das Thema](service-bus-quickstart-topics-subscriptions-portal.md) zu erstellen, um einen Service Bus-**Namespace** zu erstellen und die **Verbindungszeichenfolge** abzurufen. 

    > [!NOTE]
    > Sie erstellen ein **Thema** und ein **Abonnement** für das Thema, indem Sie **Ruby** in dieser Schnellstartanleitung verwenden. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="create-a-topic"></a>Erstellen eines Themas
Das **Azure::ServiceBusService**-Objekt ermöglicht es Ihnen, mit Themen zu arbeiten. Der folgende Code erstellt ein **Azure::ServiceBusService**-Objekt. Verwenden Sie die Methode `create_topic()`, um ein Thema zu erstellen. Im folgenden Beispiel wird ein Thema erstellt oder es werden gegebenenfalls Fehler ausgegeben.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  topic = azure_service_bus_service.create_topic("test-topic")
rescue
  puts $!
end
```

Sie können außerdem ein Objekt vom Typ **Azure::ServiceBus::Topic** mit weiteren Optionen übergeben, mit denen Sie die Standardthemeneinstellungen (etwa die Nachrichtenlebensdauer oder die maximale Warteschlangengröße) überschreiben können. Das folgende Beispiel zeigt, wie Sie die maximale Warteschlangengröße auf 5 GB und die Gültigkeitsdauer auf eine Minute festlegen:

```ruby
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Erstellen von Abonnements
Themenabonnements werden ebenfalls mit dem **Azure::ServiceBusService**-Objekt erstellt. Abonnements werden benannt und können einen optionalen Filter aufweisen, der die Nachrichten einschränkt, die an die virtuelle Warteschlange des Abonnements übermittelt werden.

Abonnements sind persistent. Sie existieren, bis sie selbst oder die ihnen zugeordneten Themen gelöscht werden. Wenn Ihre Anwendung Logik beinhaltet, sollte sie bei der Erstellung eines Abonnements zuerst mithilfe der getSubscription-Methode überprüfen, ob das Abonnement bereits vorhanden ist.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Erstellen eines Abonnements mit dem Standardfilter (MatchAll)
Wenn beim Erstellen eines neuen Abonnements kein Filter angegeben wird, wird der Filter **MatchAll** (Standard) verwendet. Wenn der Filter **MatchAll** verwendet wird, werden alle für das Thema veröffentlichten Nachrichten in die virtuelle Warteschlange des Abonnements gestellt. Mit dem folgenden Beispiel wird ein Abonnement namens „all-messages“ erstellt, für das der Standardfilter **MatchAll** verwendet wird.

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Erstellen von Abonnements mit Filtern
Sie können auch Filter definieren, durch die Sie angeben können, welche an ein Thema gesendeten Nachrichten in einem bestimmten Abonnement angezeigt werden sollen.

Der von Abonnements unterstützte flexibelste Filtertyp ist **Azure::ServiceBus::SqlFilter**, der eine Teilmenge von SQL92 implementiert. SQL-Filter werden auf die Eigenschaften der Nachrichten angewendet, die für das Thema veröffentlicht werden. Weitere Informationen zu den Ausdrücken, die mit einem SQL-Filter verwendet werden können, finden Sie in der Syntax von [SqlFilter](service-bus-messaging-sql-filter.md).

Sie können einem Abonnement mithilfe der Methode `create_rule()` des Objekts **Azure::ServiceBusService** Filter hinzufügen. Durch diese Methode können Sie neue Filter einem vorhandenen Abonnement hinzufügen.

Da der Standardfilter automatisch auf alle neuen Abonnements angewendet wird, müssen Sie zuerst den Standardfilter entfernen. Ansonsten überschreibt **MatchAll** alle Filter, die Sie angeben. Sie können die Standardregel mithilfe der Methode `delete_rule()` des Objekts **Azure::ServiceBusService** entfernen.

Mit dem folgenden Beispiel wird ein Abonnement namens „high-messages“ mit einem Filter vom Typ **Azure::ServiceBus::SqlFilter** erstellt, der nur Nachrichten auswählt, deren benutzerdefinierte Eigenschaft `message_number` größer „3“ ist:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "high-messages")
azure_service_bus_service.delete_rule("test-topic", "high-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("high-messages-rule")
rule.topic = "test-topic"
rule.subscription = "high-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number > 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Analog dazu erstellt das folgende Beispiel ein Abonnement namens `low-messages` mit einem Filter vom Typ **Azure::ServiceBus::SqlFilter**, der nur Nachrichten auswählt, deren benutzerdefinierte Eigenschaft `message_number` kleiner oder gleich „3“ ist:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "low-messages")
azure_service_bus_service.delete_rule("test-topic", "low-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("low-messages-rule")
rule.topic = "test-topic"
rule.subscription = "low-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number <= 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Wenn nun eine Nachricht an `test-topic` gesendet wird, wird diese stets an das `all-messages`-Themenabonnement und selektiv an die Empfänger der Abonnements `high-messages` und `low-messages` zugestellt (je nach Inhalt der Nachricht).

## <a name="send-messages-to-a-topic"></a>Senden von Nachrichten an ein Thema
Um eine Nachricht an ein Service Bus-Thema zu senden, muss die Anwendung die Methode `send_topic_message()` des Objekts **Azure::ServiceBusService** verwenden. Nachrichten, die an Service Bus-Themen gesendet werden, sind **Azure::ServiceBus::BrokeredMessage**-Objekte. Objekte vom Typ **Azure::ServiceBus::BrokeredMessage** besitzen eine Reihe von Standardeigenschaften (wie etwa `label` und `time_to_live`), ein Wörterbuch zur Speicherung benutzerdefinierter, anwendungsspezifischer Eigenschaften und einen aus Zeichenfolgendaten bestehenden Nachrichtentext. Eine Anwendung kann den Nachrichtentext festlegen, indem sie einen Zeichenfolgenwert an die `send_topic_message()`-Methode übergibt. Erforderliche Standardeigenschaften werden mit Standardwerten gefüllt.

Das folgende Beispiel zeigt, wie Sie fünf Testnachrichten an `test-topic` senden. Der benutzerdefinierte Eigenschaftswert `message_number` jeder Nachricht variiert gemäß der Iteration der Schleife. (Dadurch wird bestimmt, welches Abonnement die Nachricht erhält.)

```ruby
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Service Bus-Themen unterstützen eine maximale Nachrichtengröße von 256 KB im [Standard-Tarif](service-bus-premium-messaging.md) und 1 MB im [Premium-Tarif](service-bus-premium-messaging.md). Der Header, der die standardmäßigen und benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 KB haben. Es gibt keine Beschränkung für die Anzahl der Nachrichten, die ein Thema enthält. Es gibt jedoch eine Obergrenze für die Gesamtgröße der Nachrichten eines Themas. Die Themengröße wird bei der Erstellung definiert. Die Obergrenze beträgt 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Empfangen von Nachrichten aus einem Abonnement
Nachrichten werden von einem Abonnement über die Methode `receive_subscription_message()` des Objekts **Azure::ServiceBusService** empfangen. Standardmäßig werden Nachrichten gelesen(peak) und gesperrt, ohne aus dem Abonnement gelöscht zu werden. Sie können die Nachricht aus dem Abonnement lesen und löschen, indem Sie die Option `peek_lock` auf **false** festlegen.

Im Standardverhalten sind Empfang und Löschung von Nachrichten zweistufig. Dadurch können Anwendungen unterstützt werden, die das Auslassen bzw. Fehlen von Nachrichten nicht zulassen können. Wenn Service Bus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann zurück an die Anwendung. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt sie die zweite Phase des Empfangsprozesses durch: In dieser Phase wird die Methode `delete_subscription_message()` aufgerufen und die zu löschende Nachricht als Parameter angegeben. Die Methode `delete_subscription_message()` markiert die Nachricht als verarbeitet und entfernt sie aus dem Abonnement.

Wenn der Parameter `:peek_lock` auf **FALSE** festgelegt ist, wird das Lesen und Löschen der Nachricht zum einfachsten Modell. Es eignet sich am besten für Szenarios, in denen es kein Problem ist, wenn Nachrichten beim Auftreten eines Fehlers nicht verarbeitet werden. Stellen Sie sich ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wird. Da die Nachricht von Service Bus als verarbeitet gekennzeichnet wurde, wird die vor dem Absturz verarbeitete Nachricht nicht berücksichtigt, wenn die Anwendung neu gestartet und die Verarbeitung von Nachrichten wieder aufgenommen wird.

Das folgende Beispiel zeigt, wie Nachrichten mit `receive_subscription_message()` empfangen und verarbeitet werden können. In diesem Beispiel wird zunächst eine Nachricht aus dem Abonnement `low-messages` empfangen und gelöscht, wobei `:peek_lock` auf **false** festgelegt ist. Anschließend wird eine Nachricht von `high-messages` empfangen und mit `delete_subscription_message()` gelöscht:

```ruby
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Behandeln von Anwendungsabstürzen und nicht lesbaren Nachrichten
Service Bus stellt Funktionen zur Verfügung, die Sie bei der ordnungsgemäßen Behandlung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht unterstützen. Wenn eine Empfängeranwendung die Nachricht aus einem bestimmten Grund nicht verarbeiten kann, kann sie die Methode `unlock_subscription_message()` für das Objekt **Azure::ServiceBusService** aufrufen. Dies führt dazu, dass Service Bus die Nachricht innerhalb des Abonnements entsperrt und verfügbar macht, damit sie erneut empfangen werden kann, und zwar entweder durch dieselbe verarbeitende Anwendung oder durch eine andere verarbeitende Anwendung.

Zudem wird einer im Abonnement gesperrten Anwendung ein Zeitlimit zugeordnet. Wenn die Anwendung die Nachricht vor Ablauf des Sperrzeitlimits nicht verarbeiten kann (zum Beispiel wenn die Anwendung abstürzt), entsperrt Service Bus die Nachricht automatisch und macht sie verfügbar, um erneut empfangen zu werden.

Falls die Anwendung nach der Verarbeitung der Nachricht, aber vor dem Aufruf der Methode `delete_subscription_message()` abstürzt, wird die Nachricht wieder an die Anwendung zugestellt, wenn diese neu gestartet wird. Dies wird häufig als *at least once processing* bezeichnet und bedeutet, dass jede Nachricht mindestens einmal verarbeitet wird, wobei eine Nachricht in bestimmten Situationen unter Umständen erneut übermittelt wird. Wenn eine doppelte Verarbeitung im betreffenden Szenario nicht geeignet ist, sollten Anwendungsentwickler ihrer Anwendung zusätzliche Logik für den Umgang mit der Übermittlung doppelter Nachrichten hinzufügen. Diese Logik wird häufig durch Verwendung der Nachrichteneigenschaft `message_id` erreicht, die über mehrere Übermittlungsversuche hinweg konstant bleibt.

## <a name="delete-topics-and-subscriptions"></a>Löschen von Themen und Abonnements
Themen und Abonnements sind persistent und müssen über das [Azure-Portal][Azure portal] oder programmgesteuert explizit gelöscht werden. Das folgende Beispiel zeigt das Löschen des Themas `test-topic`.

```ruby
azure_service_bus_service.delete_topic("test-topic")
```

Durch das Löschen eines Themas werden auch alle Abonnements gelöscht, die mit dem Thema registriert sind. Abonnements können auch unabhängig gelöscht werden. Der folgende Code zeigt, wie das Abonnement `high-messages` aus dem Thema `test-topic` gelöscht wird:

```ruby
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

> [!NOTE]
> Sie können Service Bus-Ressourcen mit dem [Service Bus-Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/) verwalten. Mit dem Service Bus-Explorer können Benutzer eine Verbindung mit einem Service Bus-Namespace herstellen und Messagingentitäten auf einfache Weise verwalten. Das Tool stellt erweiterte Features wie Import-/Exportfunktionen oder Testmöglichkeiten für Themen, Warteschlangen, Abonnements, Relaydienste, Notification Hubs und Event Hubs zur Verfügung. 

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie nun mit den Grundlagen der Servicebus-Themen vertraut sind, finden Sie unter den folgenden Links weitere Informationen.

* Siehe [Service Bus-Warteschlangen, -Themen und -Abonnements](service-bus-queues-topics-subscriptions.md).
* API-Referenz für [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter).
* Besuchen Sie das [Azure SDK for Ruby](https://github.com/Azure/azure-sdk-for-ruby)-Repository auf GitHub.

[Azure portal]: https://portal.azure.com
