---
title: Verwenden von Azure Service Bus-Themen mit Python | Microsoft Docs
description: Erfahren Sie mehr über die Verwendung von Azure Service Bus-Themen und -Abonnements über Python.
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: c4f1d76c-7567-4b33-9193-3788f82934e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: 47cd0621a601e3f1ef53572bc7bb8bc1c7ea76ab
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65992000"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-python"></a>Verwenden von Service Bus-Themen und -Abonnements mit Python

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

In diesem Artikel erfahren Sie, wie Sie Service Bus-Themen und -Abonnements verwenden. Die Beispiele sind in Python geschrieben und verwenden das [Azure Python SDK-Paket][Azure Python package]. Folgende Szenarien werden behandelt:

- Erstellen von Themen und Abonnements 
- Erstellen von Abonnementfiltern 
- Senden von Nachrichten an ein Thema 
- Empfangen von Nachrichten aus einem Abonnement
- Löschen von Themen und Abonnements

## <a name="prerequisites"></a>Voraussetzungen
1. Ein Azure-Abonnement. Um dieses Tutorial abzuschließen, benötigen Sie ein Azure-Konto. Sie können [Ihre Visual Studio-oder MSDN-Abonnentenvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) oder [sich für ein kostenloses Konto anmelden](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Befolgen Sie die Schritte im [Schnellstart: Verwenden Sie das Azure-Portal, um ein Service Bus-Thema und -Abonnements für das Thema](service-bus-quickstart-topics-subscriptions-portal.md) zu erstellen, um einen Service Bus-**Namespace** zu erstellen und die **Verbindungszeichenfolge** abzurufen.

    > [!NOTE]
    > Sie erstellen ein **Thema** und ein **Abonnement** für das Thema, indem Sie **Python** in dieser Schnellstartanleitung verwenden. 
3. Installieren Sie das [Azure Python-Paket][Azure Python package]. Weitere Informationen finden Sie im [Python-Installationshandbuch](../python-how-to-install.md).

## <a name="create-a-topic"></a>Erstellen eines Themas

Das **ServiceBusService**-Objekt ermöglicht es Ihnen, mit Themen zu arbeiten. Fügen Sie am Anfang jeder Python-Datei, in der Sie programmgesteuert auf Service Bus zugreifen möchten, den folgenden Code hinzu:

```python
from azure.servicebus.control_client import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

Der folgende Code erstellt ein **ServiceBusService**-Objekt. Ersetzen Sie `mynamespace`, `sharedaccesskeyname` und `sharedaccesskey` durch Ihren tatsächlichen Namespace, Shared Access Signature (SAS)-Schlüsselnamen und Schlüsselwert.

```python
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

Sie können die Werte für den Namen und Wert des SAS-Schlüssels im [Azure-Portal][Azure portal] abrufen.

```python
bus_service.create_topic('mytopic')
```

Die Methode `create_topic` unterstützt zudem weitere Optionen, mit denen Sie Standardeinstellungen für Themen überschreiben können. Hierzu zählen beispielsweise die Gültigkeitsdauer von Nachrichten und die maximale Themengröße. Das folgende Beispiel legt die maximale Themengröße auf 5 GB bei einer Gültigkeitsdauer (Time-To-Live, TTL) von einer Minute fest:

```python
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## <a name="create-subscriptions"></a>Erstellen von Abonnements

Abonnements von Themen werden ebenfalls mit dem **ServiceBusService**-Objekt erstellt. Abonnements werden benannt und können einen optionalen Filter aufweisen, der die Nachrichten einschränkt, die an die virtuelle Warteschlange des Abonnements übermittelt werden.

> [!NOTE]
> Abonnements sind dauerhaft und existieren solange, bis sie oder das Thema, für das sie abonniert sind, gelöscht werden.
> 
> 

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Erstellen eines Abonnements mit dem Standardfilter (MatchAll)

Wenn beim Erstellen eines neuen Abonnements kein Filter angegeben wird, wird der Filter **MatchAll** (Standard) verwendet. Wenn der Filter **MatchAll** verwendet wird, werden alle für das Thema veröffentlichten Nachrichten in die virtuelle Warteschlange des Abonnements gestellt. Mit dem folgenden Beispiel wird ein Abonnement namens `AllMessages` erstellt, für das der Standardfilter **MatchAll** verwendet wird.

```python
bus_service.create_subscription('mytopic', 'AllMessages')
```

### <a name="create-subscriptions-with-filters"></a>Erstellen von Abonnements mit Filtern

Sie können auch Filter definieren, durch die Sie angeben können, welche an ein Thema gesendeten Nachrichten in einem bestimmten Themenabonnement angezeigt werden sollen.

Der flexibelste, von Abonnements unterstützte Filtertyp ist **SqlFilter**, der eine Teilmenge von SQL92 implementiert. SQL-Filter werden auf die Eigenschaften der Nachrichten angewendet, die für das Thema veröffentlicht werden. Weitere Informationen zu den Ausdrücken, die mit einem SQL-Filter verwendet werden können, finden Sie in der Syntax [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Sie können einem Abonnement mithilfe der Methode **create\_rule** des **ServiceBusService**-Objekts Filter hinzufügen. Durch diese Methode können Sie neue Filter zu einem vorhandenen Abonnement hinzufügen.

> [!NOTE]
> Da der Standardfilter automatisch auf alle neuen Abonnements angewendet wird, müssen Sie zuerst den Standardfilter entfernen. Andernfalls überschreibt **MatchAll** alle Filter, die Sie angeben. Die Standardregel kann mithilfe der Methode `delete_rule` des Objekts **ServiceBusService** entfernt werden.
> 
> 

Im folgenden Beispiel wird ein Abonnement namens `HighMessages` mit einem Filter vom Typ **SqlFilter** erstellt, der nur Nachrichten auswählt, deren benutzerdefinierte Eigenschaft `messagenumber` größer „3“ ist:

```python
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

Analog dazu erstellt das folgende Beispiel ein Abonnement namens `LowMessages` mit einem Filter vom Typ **SqlFilter**, der nur Nachrichten auswählt, deren benutzerdefinierte Eigenschaft `messagenumber` kleiner oder gleich „3“ ist:

```python
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Wenn eine Nachricht an `mytopic` gesendet wird, wird diese nun stets an die Empfänger des Themenabonnements **AllMessages** zugestellt. Sie wird selektiv an die Empfänger der Themenabonnements **HighMessages** und **LowMessages** zugestellt (je nach Inhalt der Nachricht).

## <a name="send-messages-to-a-topic"></a>Senden von Nachrichten an ein Thema

Um eine Nachricht an ein Service Bus-Thema zu senden, muss die Anwendung die Methode `send_topic_message` des Objekts **ServiceBusService** verwenden.

Das folgende Beispiel zeigt, wie Sie fünf Testnachrichten an `mytopic` senden. Der Eigenschaftswert `messagenumber` jeder Nachricht variiert gemäß der Iteration der Schleife. (Dadurch wird bestimmt, welche Abonnements die Nachricht erhalten.)

```python
for i in range(5):
    msg = Message('Msg {0}'.format(i).encode('utf-8'), custom_properties={'messagenumber':i})
    bus_service.send_topic_message('mytopic', msg)
```

Service Bus-Themen unterstützen eine maximale Nachrichtengröße von 256 KB im [Standard-Tarif](service-bus-premium-messaging.md) und 1 MB im [Premium-Tarif](service-bus-premium-messaging.md). Der Header, der die standardmäßigen und benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 KB haben. Es gibt keine Beschränkung für die Anzahl der Nachrichten, die ein Thema enthält. Es gibt jedoch eine Obergrenze für die Gesamtgröße der Nachrichten eines Themas. Die Themengröße wird bei der Erstellung definiert. Die Obergrenze beträgt 5 GB. Weitere Informationen zu Kontingenten finden Sie unter [Service Bus-Kontingente][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Empfangen von Nachrichten aus einem Abonnement

Nachrichten werden von einem Abonnement über die Methode `receive_subscription_message` des Objekts **ServiceBusService** empfangen:

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=False)
print(msg.body)
```

Wenn der Parameter `peek_lock` auf **False** festgelegt ist, werden Nachrichten nach dem Lesen aus dem Abonnement gelöscht. Sie können die Nachricht lesen (einen kurzen Blick darauf werfen) und sperren, ohne sie aus der Warteschlange zu löschen, indem Sie den Parameter `peek_lock` auf **true** festlegen.

Das Verhalten für das Lesen und Löschen der Nachricht im Rahmen des Empfangsvorgangs ist das einfachste Modell. Es wird am besten für Szenarios eingesetzt, bei denen eine Anwendung tolerieren kann, wenn eine Nachricht bei einem Fehler nicht verarbeitet wird. Um dieses Verfahren zu verstehen, stellen Sie sich ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wird. Da die Nachricht von Service Bus als verarbeitet gekennzeichnet wurde, wird die vor dem Absturz verarbeitete Nachricht nicht berücksichtigt, wenn die Anwendung neu gestartet und die Verarbeitung von Nachrichten wieder aufgenommen wird.

Wenn der Parameter `peek_lock` auf **true** festgelegt ist, wird der Empfangsvorgang zu einem zweistufigen Vorgang. Dadurch können Anwendungen ohne Toleranz für fehlende Nachrichten unterstützt werden. Wenn Service Bus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann zurück an die Anwendung. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt sie die zweite Phase des Empfangsprozesses durch, indem sie die Methode `delete` für das Objekt **Message** aufruft. Die Methode `delete` markiert die Nachricht als verarbeitet und entfernt sie aus dem Abonnement.

```python
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
if msg.body is not None:
print(msg.body)
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Behandeln von Anwendungsabstürzen und nicht lesbaren Nachrichten

Service Bus stellt Funktionen zur Verfügung, die Sie bei der ordnungsgemäßen Behandlung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht unterstützen. Wenn eine Empfängeranwendung die Nachricht aus einem bestimmten Grund nicht verarbeiten kann, kann sie die Methode `unlock` für das Objekt **Message** aufrufen. Diese Methode führt dazu, dass Service Bus die Nachricht innerhalb des Abonnements entsperrt und verfügbar macht, damit sie erneut empfangen werden kann, und zwar entweder durch dieselbe verarbeitende Anwendung oder durch eine andere verarbeitende Anwendung.

Zudem wird einer im Abonnement gesperrten Anwendung ein Zeitlimit zugeordnet. Wenn die Anwendung die Nachricht vor Ablauf des Sperrzeitlimits nicht verarbeiten kann (zum Beispiel wenn die Anwendung abstürzt) entsperrt Service Bus die Nachricht automatisch und macht sie verfügbar, um erneut empfangen zu werden.

Falls die Anwendung nach der Verarbeitung der Nachricht, aber vor dem Aufruf der Methode `delete` abstürzt, wird die Nachricht wieder an die Anwendung zugestellt, wenn diese neu gestartet wird. Dieses Verhalten wird häufig „At Least Once Processing“\* (Mindestens einmalige Verarbeitung) bezeichnet und bedeutet, dass jede Nachricht mindestens einmal verarbeitet wird, wobei eine Nachricht in bestimmten Situationen unter Umständen erneut übermittelt wird. Wenn eine doppelte Verarbeitung im betreffenden Szenario nicht geeignet ist, sollten Anwendungsentwickler ihrer Anwendung zusätzliche Logik für den Umgang mit der Übermittlung doppelter Nachrichten hinzufügen. Dazu können Sie die Eigenschaft **MessageId** der Nachricht verwenden, die über mehrere Zustellungsversuche hinweg konstant bleibt.

## <a name="delete-topics-and-subscriptions"></a>Löschen von Themen und Abonnements

Themen und Abonnements sind persistent und müssen über das [Azure-Portal][Azure portal] oder programmgesteuert explizit gelöscht werden. Im folgenden Beispiel wird das Thema `mytopic` gelöscht:

```python
bus_service.delete_topic('mytopic')
```

Durch das Löschen eines Themas werden auch alle Abonnements gelöscht, die mit dem Thema registriert sind. Abonnements können auch unabhängig gelöscht werden. Der folgende Code zeigt, wie das Abonnement `HighMessages` aus dem Thema `mytopic` gelöscht wird:

```python
bus_service.delete_subscription('mytopic', 'HighMessages')
```

> [!NOTE]
> Sie können Service Bus-Ressourcen mit dem [Service Bus-Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/) verwalten. Mit dem Service Bus-Explorer können Benutzer eine Verbindung mit einem Service Bus-Namespace herstellen und Messagingentitäten auf einfache Weise verwalten. Das Tool stellt erweiterte Features wie Import-/Exportfunktionen oder Testmöglichkeiten für Themen, Warteschlangen, Abonnements, Relaydienste, Notification Hubs und Event Hubs zur Verfügung. 

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun mit den Grundlagen der Servicebus-Themen vertraut sind, finden Sie unter den folgenden Links weitere Informationen.

* Siehe [Warteschlangen, Themen und Abonnements][Queues, topics, and subscriptions].
* Referenz für [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

[Azure portal]: https://portal.azure.com
[Azure Python package]: https://pypi.python.org/pypi/azure  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Service Bus quotas]: service-bus-quotas.md 
