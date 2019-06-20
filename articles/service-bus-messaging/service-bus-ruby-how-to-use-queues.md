---
title: Verwenden von Azure Service Bus-Warteschlangen mit Ruby | Microsoft Docs
description: Erfahren Sie mehr über die Verwendung von Service Bus-Warteschlangen in Azure. Die Codebeispiele wurden in Ruby geschrieben.
services: service-bus-messaging
documentationcenter: ruby
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 0a11eab2-823f-4cc7-842b-fbbe0f953751
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 48f60b7c07cc16b4d9994d5644069fdcb4881e0a
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991876"
---
# <a name="how-to-use-service-bus-queues-with-ruby"></a>Verwenden von Service Bus-Warteschlangen mit Ruby

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

In diesem Tutorial erfahren Sie, wie Sie Ruby-Anwendungen erstellen, um Nachrichten an eine Service Bus-Warteschlange zu senden und Antworten zu empfangen. Die Beispiele sind in Ruby geschrieben und verwenden das Azure-Gem.

## <a name="prerequisites"></a>Voraussetzungen
1. Ein Azure-Abonnement. Um dieses Tutorial abzuschließen, benötigen Sie ein Azure-Konto. Sie können Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF) registrieren.
2. Führen Sie die Schritte im Artikel [Schnellstart: Erstellen einer Service Bus-Warteschlange mithilfe des Azure-Portals](service-bus-quickstart-portal.md) aus.
    1. Lesen Sie die kurze **Übersicht** über Service Bus-**Warteschlangen**. 
    2. Erstellen Sie einen Service Bus-**Namespace**. 
    3. Rufen Sie die **Verbindungszeichenfolge** ab. 

        > [!NOTE]
        > In diesem Tutorial erstellen Sie eine **Warteschlange** im Service Bus-Namespace mithilfe von Ruby. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="how-to-create-a-queue"></a>Erstellen von Warteschlangen
Das **Azure::ServiceBusService**-Objekt ermöglicht Ihnen, mit Warteschlangen zu arbeiten. Verwenden Sie die Methode `create_queue()`, um eine Warteschlange zu erstellen. Im folgenden Beispiel werden eine Warteschlange erstellt oder gegebenenfalls Fehler ausgegeben.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

Sie können außerdem ein **Azure::ServiceBus::Queue**-Objekt mit weiteren Optionen übergeben, mit denen Sie die Standardeinstellungen wie z.B. Nachrichtenlebensdauer oder maximale Warteschlangengröße überschreiben können. Das folgende Beispiel zeigt, wie Sie die maximale Warteschlangengröße auf 5 GB und die Gültigkeitsdauer auf eine Minute festlegen:

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Senden von Nachrichten an eine Warteschlange
Um eine Nachricht an eine Service Bus-Warteschlange zu senden, ruft Ihre Anwendung die Methode `send_queue_message()` für das Objekt **Azure::ServiceBusService** auf. Nachrichten, die an Service Bus-Warteschlangen gesendet (und von diesen empfangen) werden, sind Objekte vom Typ **Azure::ServiceBus::BrokeredMessage** und verfügen über einen Satz von Standardeigenschaften (wie etwa `label` und `time_to_live`), über ein Wörterbuch zum Speichern benutzerdefinierter anwendungsspezifischer Eigenschaften sowie über beliebige Anwendungsdaten. Eine Anwendung kann den Nachrichtentext festlegen, indem sie einen Zeichenfolgenwert als Nachricht übergibt. Erforderliche Standardeigenschaften werden mit den Standardwerten gefüllt.

Das folgende Beispiel zeigt, wie mithilfe von `send_queue_message()` eine Testnachricht an die Warteschlange `test-queue` gesendet wird:

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

Service Bus-Warteschlangen unterstützen eine maximale Nachrichtengröße von 256 KB für den [Standard-Tarif](service-bus-premium-messaging.md) und 1 MB für den [Premium-Tarif](service-bus-premium-messaging.md). Der Header, der die standardmäßigen und benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 KB haben. Bei der Anzahl der Nachrichten, die in einer Warteschlange aufgenommen werden können, besteht keine Beschränkung. Allerdings gilt eine Deckelung bei der Gesamtgröße der in einer Warteschlange aufzunehmenden Nachrichten. Die Warteschlangengröße wird bei der Erstellung definiert. Die Obergrenze beträgt 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Empfangen von Nachrichten aus einer Warteschlange
Zum Empfangen von Nachrichten aus einer Warteschlange wird die Methode `receive_queue_message()` für das Objekt **Azure::ServiceBusService** verwendet. Standardmäßig werden Nachrichten gelesen und gesperrt, ohne aus der Warteschlange gelöscht zu werden. Gelesene Nachrichten können jedoch aus der Warteschlange gelöscht werden, indem Sie die Option `:peek_lock` auf **false** festlegen.

Im Standardverhalten sind Empfang und Löschung von Nachrichten zweistufig. Dadurch können Anwendungen unterstützt werden, die das Auslassen bzw. Fehlen von Nachrichten nicht zulassen können. Wenn Service Bus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann zurück an die Anwendung. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt sie die zweite Phase des Empfangsprozesses durch: In dieser Phase wird die Methode `delete_queue_message()` aufgerufen und die zu löschende Nachricht als Parameter angegeben. Die Methode `delete_queue_message()` markiert die Nachricht als verarbeitet und entfernt sie aus der Warteschlange.

Wenn der Parameter `:peek_lock` auf **false** festgelegt ist, wird das Lesen und Löschen der Nachricht zum einfachsten Modell. Es eignet sich am besten für Szenarien, in denen es kein Problem ist, wenn Nachrichten bei Auftreten eines Fehlers nicht verarbeitet werden. Um dieses Verfahren zu verstehen, stellen Sie sich ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wird. Da die Nachricht von Service Bus als verarbeitet gekennzeichnet wurde, wird die vor dem Absturz verarbeitete Nachricht nicht berücksichtigt, wenn die Anwendung neu gestartet und die Verarbeitung von Nachrichten wieder aufgenommen wird.

Das folgende Beispiel zeigt, wie Nachrichten mit `receive_queue_message()` empfangen und verarbeitet werden können. In diesem Beispiel wird zunächst eine Nachricht empfangen und gelöscht, wobei `:peek_lock` auf **false** festgelegt ist. Anschließend wird eine weitere Nachricht empfangen und mit `delete_queue_message()` gelöscht:

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Behandeln von Anwendungsabstürzen und nicht lesbaren Nachrichten
Service Bus stellt Funktionen zur Verfügung, die Sie bei der ordnungsgemäßen Behandlung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht unterstützen. Wenn eine Empfängeranwendung die Nachricht aus einem bestimmten Grund nicht verarbeiten kann, kann sie die Methode `unlock_queue_message()` für das Objekt **Azure::ServiceBusService** aufrufen. Dieser Aufruf führt dazu, dass Service Bus die Nachricht innerhalb der Warteschlange entsperrt und verfügbar macht, damit sie erneut empfangen werden kann (entweder durch die gleiche verarbeitende Anwendung oder durch eine andere verarbeitende Anwendung).

Mit einer innerhalb der Warteschlange gesperrten Nachricht ist außerdem eine Zeitlimitüberschreitung verknüpft. Wenn die Anwendung die Nachricht nicht verarbeiten kann, bevor die Zeitlimitüberschreitung der Sperre abläuft (z. B. falls die Anwendung abstürzt), so entsperrt Service Bus die Nachricht automatisch und macht sie verfügbar, sodass sie wieder empfangen werden kann.

Falls die Anwendung nach der Verarbeitung der Nachricht, aber vor dem Aufruf der Methode `delete_queue_message()` abstürzt, wird die Nachricht wieder an die Anwendung zugestellt, wenn diese neu gestartet wird. Dieser Prozess wird häufig als *At Least Once Processing* (Mindestens einmalige Verarbeitung) bezeichnet und bedeutet, dass jede Nachricht mindestens einmal verarbeitet wird, wobei eine Nachricht in bestimmten Situationen unter Umständen erneut übermittelt wird. Wenn eine doppelte Verarbeitung im betreffenden Szenario nicht geeignet ist, sollten Anwendungsentwickler ihrer Anwendung zusätzliche Logik für den Umgang mit der Übermittlung doppelter Nachrichten hinzufügen. Dies wird häufig durch die Verwendung der Nachrichteneigenschaft `message_id` erreicht, die über mehrere Übermittlungsversuche hinweg konstant bleibt.

> [!NOTE]
> Sie können Service Bus-Ressourcen mit dem [Service Bus-Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/) verwalten. Mit dem Service Bus-Explorer können Benutzer eine Verbindung mit einem Service Bus-Namespace herstellen und Messagingentitäten auf einfache Weise verwalten. Das Tool stellt erweiterte Features wie Import-/Exportfunktionen oder Testmöglichkeiten für Themen, Warteschlangen, Abonnements, Relaydienste, Notification Hubs und Event Hubs zur Verfügung. 

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie nun mit den Grundlagen der Servicebus-Warteschlangen vertraut sind, finden Sie unter den folgenden Links weitere Informationen.

* Übersicht über [Warteschlangen, Themen und Abonnements](service-bus-queues-topics-subscriptions.md).
* Besuchen Sie das [Azure SDK for Ruby](https://github.com/Azure/azure-sdk-for-ruby)-Repository auf GitHub.

Einen Vergleich zwischen den in diesem Artikel besprochenen Azure Service Bus-Warteschlangen und den unter [Verwenden des Warteschlangenspeichers mit Ruby](../storage/queues/storage-ruby-how-to-use-queue-storage.md) erörterten Azure-Warteschlangen finden Sie unter [Azure-Warteschlangen und Azure Service Bus-Warteschlangen – Vergleich und Gegenüberstellung](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

