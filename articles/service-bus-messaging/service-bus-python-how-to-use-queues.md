---
title: Verwenden von Azure Service Bus-Warteschlangen mit Python | Microsoft Docs
description: Erfahren Sie, wie Azure Service Bus-Warteschlangen von Python aus verwendet werden.
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 6d95e4a0a7aeedef2fc7e635d2e49ea68c3ba0ca
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65992045"
---
# <a name="how-to-use-service-bus-queues-with-python"></a>Verwenden von Service Bus-Warteschlangen mit Python

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

In diesem Tutorial erfahren Sie, wie Sie Python-Anwendungen erstellen, um Nachrichten an eine Service Bus-Warteschlange zu senden und Antworten zu empfangen. 

## <a name="prerequisites"></a>Voraussetzungen
1. Ein Azure-Abonnement. Um dieses Tutorial abzuschließen, benötigen Sie ein Azure-Konto. Sie können Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF) registrieren.
2. Führen Sie die Schritte im Artikel [Schnellstart: Erstellen einer Service Bus-Warteschlange mithilfe des Azure-Portals](service-bus-quickstart-portal.md) aus.
    1. Lesen Sie die kurze **Übersicht** über Service Bus-**Warteschlangen**. 
    2. Erstellen Sie einen Service Bus-**Namespace**. 
    3. Rufen Sie die **Verbindungszeichenfolge** ab. 

        > [!NOTE]
        > In diesem Tutorial erstellen Sie eine **Warteschlange** im Service Bus-Namespace mithilfe von Python. 
1. Installieren Sie Python oder das [Azure Service Bus-Paket für Python][Python Azure Service Bus package]. Informationen hierzu finden Sie im [Leitfaden zur Installation von Python](../python-how-to-install.md). Die vollständige Dokumentation zum Service Bus-SDK für Python finden Sie [hier](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="create-a-queue"></a>Erstellen einer Warteschlange
Das **ServiceBusClient**-Objekt ermöglicht Ihnen, mit Warteschlangen zu arbeiten. Fügen Sie am Anfang jeder Python-Datei, in der Sie programmgesteuert auf Service Bus zugreifen möchten, den folgenden Code hinzu:

```python
from azure.servicebus import ServiceBusClient
```

Der folgende Code erstellt ein **ServiceBusClient**-Objekt. Ersetzen Sie `mynamespace`, `sharedaccesskeyname` und `sharedaccesskey` durch Ihren Namespace, SAS (Shared Access Signature)-Schlüsselnamen und -wert.

```python
sb_client = ServiceBusClient.from_connection_string('<CONNECTION STRING>')
```

Den Namen und Wert des SAS-Schlüssels finden Sie in den Verbindungsinformationen des [Azure-Portals][Azure portal] oder im Bereich **Eigenschaften** von Visual Studio, wenn Sie den Service Bus-Namespace im Server-Explorer auswählen (wie im vorherigen Abschnitt gezeigt).

```python
sb_client.create_queue("taskqueue")
```

Die Methode `create_queue` unterstützt zudem weitere Optionen, mit denen Sie Standardeinstellungen für die Warteschlange überschreiben können. Hierzu zählen beispielsweise die Gültigkeitsdauer (Time To Live, TTL) von Nachrichten und die maximale Warteschlangengröße. Das folgende Beispiel legt die maximale Warteschlangengröße auf 5 GB bei einem TTL-Wert von 1 Minute fest:

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120, default_message_time_to_live=datetime.timedelta(minutes=1))
```

Weitere Informationen finden Sie unter [Service Bus-Bibliotheken für Python](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="send-messages-to-a-queue"></a>Senden von Nachrichten an eine Warteschlange
Um eine Nachricht an eine Service Bus-Warteschlange zu senden, ruft Ihre Anwendung die `send`-Methode des `ServiceBusClient`-Objekts auf.

Das folgende Beispiel zeigt, wie mithilfe von `send_queue_message` eine Testnachricht an die Warteschlange `taskqueue` gesendet wird:

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient 
queue_client = QueueClient.from_connection_string("<CONNECTION STRING>", "<QUEUE NAME>")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

Service Bus-Warteschlangen unterstützen eine maximale Nachrichtengröße von 256 KB für den [Standard-Tarif](service-bus-premium-messaging.md) und 1 MB für den [Premium-Tarif](service-bus-premium-messaging.md). Der Header, der die standardmäßigen und benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 KB haben. Bei der Anzahl der Nachrichten, die in einer Warteschlange aufgenommen werden können, besteht keine Beschränkung. Allerdings gilt eine Deckelung bei der Gesamtgröße der in einer Warteschlange aufzunehmenden Nachrichten. Die Warteschlangengröße wird bei der Erstellung definiert. Die Obergrenze beträgt 5 GB. Weitere Informationen zu Kontingenten finden Sie unter [Service Bus-Kontingente][Service Bus quotas].

Weitere Informationen finden Sie unter [Service Bus-Bibliotheken für Python](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="receive-messages-from-a-queue"></a>Empfangen von Nachrichten aus einer Warteschlange
Zum Empfangen von Nachrichten aus einer Warteschlange wird die `get_receiver`-Methode des `ServiceBusService`-Objekts verwendet:

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient 
queue_client = QueueClient.from_connection_string("<CONNECTION STRING>", "<QUEUE NAME>")

## Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

Weitere Informationen finden Sie unter [Service Bus-Bibliotheken für Python](/python/api/overview/azure/servicebus?view=azure-python).


Wenn der Parameter `peek_lock` auf **False** festgelegt ist, werden Nachrichten nach dem Lesen aus der Warteschlange gelöscht. Sie können die Nachricht lesen (einen kurzen Blick darauf werfen) und sperren, ohne sie aus der Warteschlange zu löschen, indem Sie den Parameter `peek_lock` auf **true** festlegen.

Das Verhalten für das Lesen und Löschen der Nachricht als Teil des Empfangsvorgangs ist das einfachste Modell. Es wird am besten für Szenarien eingesetzt, bei denen es eine Anwendung tolerieren kann, wenn eine Nachricht bei Auftreten eines Fehlers nicht verarbeitet wird. Um dieses Verfahren zu verstehen, stellen Sie sich ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wird. Da Service Bus die Nachricht als verwendet markiert hat, wird er jene Nachricht auslassen, die vor dem Absturz verwendet wurde, wenn die Anwendung neu startet und erneut mit der Verwendung von Nachrichten beginnt.

Wenn der Parameter `peek_lock` auf **true** festgelegt ist, wird der Empfangsvorgang zu einem zweistufigen Vorgang. Dadurch können Anwendungen ohne Toleranz für fehlende Nachrichten unterstützt werden. Wenn Service Bus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann zurück an die Anwendung. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt Sie die zweite Phase des Empfangsprozesses durch Aufrufen der Methode **delete** für das **Message**-Objekt aus. Die Methode **delete** markiert die Nachricht als verarbeitet und entfernt sie aus der Warteschlange.

```python
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Behandeln von Anwendungsabstürzen und nicht lesbaren Nachrichten
Service Bus stellt Funktionen zur Verfügung, die Sie bei der ordnungsgemäßen Behandlung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht unterstützen. Wenn eine Empfängeranwendung die Nachricht aus einem bestimmten Grund nicht verarbeiten kann, so kann sie die Methode **unlock** für das **Message**-Objekt aufrufen. Dies führt dazu, dass Service Bus die Nachricht innerhalb der Warteschlange entsperrt und verfügbar macht, damit sie erneut empfangen werden kann, und zwar entweder durch dieselbe verarbeitende Anwendung oder durch eine andere verarbeitende Anwendung.

Zudem wird einer in der Warteschlange gesperrten Anwendung ein Zeitlimit zugeordnet. Wenn die Anwendung die Nachricht vor Ablauf des Sperrzeitlimits nicht verarbeiten kann (zum Beispiel wenn die Anwendung abstürzt), entsperrt Service Bus die Nachricht automatisch und macht sie verfügbar, um erneut empfangen zu werden.

Falls die Anwendung nach der Verarbeitung der Nachricht, aber vor Abrufen der Methode **delete** abstürzt, wird die Nachricht wieder an die Anwendung zugestellt, wenn diese neu gestartet wird. Dies wird häufig als **At Least Once Processing** bezeichnet und bedeutet, dass jede Nachricht mindestens einmal verarbeitet wird, wobei dieselbe Nachricht in bestimmten Situationen möglicherweise erneut zugestellt wird. Wenn eine doppelte Verarbeitung im betreffenden Szenario nicht geeignet ist, sollten Anwendungsentwickler ihrer Anwendung zusätzliche Logik für den Umgang mit der Übermittlung doppelter Nachrichten hinzufügen. Dies wird häufig durch die Verwendung der **MessageId**-Eigenschaft der Nachricht erzielt, die über mehrere Zustellungsversuche hinweg konstant bleibt.

> [!NOTE]
> Sie können Service Bus-Ressourcen mit dem [Service Bus-Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/) verwalten. Mit dem Service Bus-Explorer können Benutzer eine Verbindung mit einem Service Bus-Namespace herstellen und Messagingentitäten auf einfache Weise verwalten. Das Tool stellt erweiterte Features wie Import-/Exportfunktionen oder Testmöglichkeiten für Themen, Warteschlangen, Abonnements, Relaydienste, Notification Hubs und Event Hubs zur Verfügung. 

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie nun mit den Grundlagen von Service Bus-Warteschlangen vertraut sind, finden Sie in den folgenden Artikeln weitere Informationen.

* [Warteschlangen, Themen und Abonnements][Queues, topics, and subscriptions]

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md

