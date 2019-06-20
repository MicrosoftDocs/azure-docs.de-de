---
title: Verwenden von Service Bus-Themen mit PHP | Microsoft Docs
description: Erfahren Sie mehr über die Verwendung von Service Bus-Themen mit PHP in Azure.
services: service-bus-messaging
documentationcenter: php
author: axisc
manager: timlt
editor: spelluru
ms.assetid: faaa4bbd-f6ef-42ff-aca7-fc4353976449
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: f0ae734ac8814cc605ce03756fde5545c77c837d
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65992067"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-php"></a>Verwenden von Service Bus-Themen und -Abonnements mit PHP

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

In diesem Artikel erfahren Sie, wie Sie Service Bus-Themen und -Abonnements verwenden. Die Beispiele wurden in PHP geschrieben und verwenden das [Azure-SDK für PHP](../php-download-sdk.md). Folgende Szenarien werden behandelt:

- Erstellen von Themen und Abonnements 
- Erstellen von Abonnementfiltern 
- Senden von Nachrichten an ein Thema 
- Empfangen von Nachrichten aus einem Abonnement
- Löschen von Themen und Abonnements

## <a name="prerequisites"></a>Voraussetzungen
1. Ein Azure-Abonnement. Um dieses Tutorial abzuschließen, benötigen Sie ein Azure-Konto. Sie können [Ihre Visual Studio-oder MSDN-Abonnentenvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) oder [sich für ein kostenloses Konto anmelden](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Befolgen Sie die Schritte im [Schnellstart: Verwenden Sie das Azure-Portal, um ein Service Bus-Thema und -Abonnements für das Thema](service-bus-quickstart-topics-subscriptions-portal.md) zu erstellen, um einen Service Bus-**Namespace** zu erstellen und die **Verbindungszeichenfolge** abzurufen.

    > [!NOTE]
    > Sie erstellen ein **Thema** und ein **Abonnement** für das Thema, indem Sie **PHP** in dieser Schnellstartanleitung verwenden. 

## <a name="create-a-php-application"></a>Erstellen einer PHP-Anwendung
Die einzige Voraussetzung für das Erstellen einer PHP-Anwendung, die auf den Azure-Blob-Dienst zugreift, ist das Verweisen auf Klassen im [Azure SDK für PHP](../php-download-sdk.md) aus dem Code heraus. Sie können die Anwendung mit beliebigen Entwicklungstools oder mit Editor erstellen.

> [!NOTE]
> In Ihrer PHP-Installation muss außerdem die [OpenSSL-Erweiterung](https://php.net/openssl) installiert und aktiviert sein.
> 
> 

In diesem Artikel wird beschrieben, wie Sie die Dienstfunktionen verwenden, die sowohl lokal in einer PHP-Anwendung aufgerufen werden können als auch als Code in einer Azure-Webrolle, -Workerrolle oder als Website

## <a name="get-the-azure-client-libraries"></a>Abrufen der Azure-Clientbibliotheken
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus
So verwenden Sie die Service Bus-APIs

1. Verweisen Sie mithilfe der [require_once][require-once]-Anweisung auf die Autoloaderdatei.
2. Verweisen Sie auf alle Klassen, die Sie möglicherweise verwenden.

Das folgende Beispiel zeigt, wie die Autoloaderdatei integriert und auf die **ServiceBusService**-Klasse verwiesen wird.

> [!NOTE]
> In diesem Beispiel (und in anderen Beispielen in diesem Artikel) wird angenommen, dass Sie die PHP-Clientbibliotheken für Azure über Composer installiert haben. Wenn Sie die Bibliotheken manuell oder als PEAR-Paket installiert haben, müssen Sie auf die Autoloaderdatei **WindowsAzure.php** verweisen.
> 
> 

```php
require_once 'vendor\autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

In den folgenden Beispielen wird die `require_once`-Anweisung immer angezeigt. Jedoch wird nur auf die für die Ausführung des Beispiels erforderlichen Klassen verwiesen.

## <a name="set-up-a-service-bus-connection"></a>Einrichten einer Service Bus-Verbindung
Um einen Service Bus-Client zu instanziieren, benötigen Sie zunächst eine gültige Verbindungszeichenfolge mit diesem Format:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Hierbei hat der `Endpoint` normalerweise das Format `https://[yourNamespace].servicebus.windows.net`.

Zum Erstellen eines Azure-Dienstclients muss die `ServicesBuilder`-Klasse verwendet werden. Ihre Möglichkeiten:

* die Verbindungszeichenfolge direkt an die Klasse weitergeben.
* den **CloudConfigurationManager (CCM)** verwenden, um mehrere externe Quellen für die Verbindungszeichenfolge zu überprüfen:
  * Standardmäßig wird eine externe Quelle unterstützt – Umgebungsvariablen.
  * Sie können neue Quellen durch Erweitern der `ConnectionStringSource`-Klasse hinzufügen.

Für die hier erläuterten Beispiele wird die Verbindungszeichenfolge direkt weitergegeben.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-topic"></a>Erstellen eines Themas
Verwaltungsvorgänge für Service Bus-Themen können über die `ServiceBusRestProxy`-Klasse durchgeführt werden. Ein `ServiceBusRestProxy`-Objekt wird über die `ServicesBuilder::createServiceBusService`-Factorymethode mit einer entsprechenden Verbindungszeichenfolge erstellt, die die Tokenberechtigungen für deren Verwaltung kapselt.

Das folgende Beispiel zeigt, wie Sie `ServiceBusRestProxy` instanziieren und `ServiceBusRestProxy->createTopic` aufrufen, um in einem `MySBNamespace`-Namespace ein Thema namens `mytopic` zu erstellen:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\TopicInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create topic.
    $topicInfo = new TopicInfo("mytopic");
    $serviceBusRestProxy->createTopic($topicInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> Sie können die `listTopics`-Methode bei `ServiceBusRestProxy`-Objekten verwenden, um zu überprüfen, ob ein Thema mit einem bestimmten Namen bereits innerhalb eines Dienstnamespace vorhanden ist.
> 
> 

## <a name="create-a-subscription"></a>Erstellen eines Abonnements
Themenabonnements werden ebenfalls mit der `ServiceBusRestProxy->createSubscription`-Methode erstellt. Abonnements werden benannt und können einen optionalen Filter aufweisen, der die Nachrichten einschränkt, die an die virtuelle Warteschlange des Abonnements übergeben werden.

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Erstellen eines Abonnements mit dem Standardfilter (MatchAll)
Wenn beim Erstellen eines neuen Abonnements kein Filter angegeben wird, wird der Filter **MatchAll** (Standard) verwendet. Wenn der Filter **MatchAll** verwendet wird, werden alle für das Thema veröffentlichten Nachrichten in die virtuelle Warteschlange des Abonnements gestellt. Mit dem folgenden Beispiel wird ein Abonnement namens `mysubscription` erstellt, für das der Standardfilter **MatchAll** verwendet wird.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\SubscriptionInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create subscription.
    $subscriptionInfo = new SubscriptionInfo("mysubscription");
    $serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

### <a name="create-subscriptions-with-filters"></a>Erstellen von Abonnements mit Filtern
Sie können auch Filter einrichten, durch die Sie angeben können, welche an ein Thema gesendeten Nachrichten in einem bestimmten Themenabonnement angezeigt werden sollen. Der von Abonnements unterstützte flexibelste Filtertyp ist [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter), der eine Teilmenge von SQL92 implementiert. SQL-Filter werden auf die Eigenschaften der Nachrichten angewendet, die für das Thema veröffentlicht werden. Weitere Informationen zu SQL-Filtern finden Sie unter der [SqlFilter.SqlExpression-Eigenschaft][sqlfilter].

> [!NOTE]
> Jede Regel in einem Abonnement verarbeitet eingehende Nachrichten unabhängig und fügt ihre Ergebnisnachrichten dem Abonnement hinzu. Zusätzlich ist für jedes neue Abonnement ein Standard-**Rule**-Objekt mit einem Filter vorhanden, der dem Abonnement alle Nachrichten aus einem Thema hinzufügt. Wenn Sie nur Nachrichten erhalten wollen, die auf Ihren Filter passen, müssen Sie die Standardregel entfernen. Sie können die Standardregel entfernen, indem Sie die `ServiceBusRestProxy->deleteRule`-Methode verwenden.
> 
> 

Mit dem folgenden Beispiel wird ein Abonnement namens `HighMessages` mit einem SQL-Filter (**SqlFilter**) erstellt, der nur Nachrichten auswählt, deren benutzerdefinierte `MessageNumber`-Eigenschaft größer ist als drei. Informationen zum Hinzufügen von benutzerdefinierten Eigenschaften zu Nachrichten finden Sie unter [Senden von Nachrichten an ein Thema](#send-messages-to-a-topic).

```php
$subscriptionInfo = new SubscriptionInfo("HighMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "HighMessages", '$Default');

$ruleInfo = new RuleInfo("HighMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber > 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "HighMessages", $ruleInfo);
```

Für diesen Code ist ein zusätzlicher Namespace erforderlich: `WindowsAzure\ServiceBus\Models\SubscriptionInfo`.

Analog dazu erstellt das folgende Beispiel ein Abonnement namens `LowMessages` mit einem `SqlFilter`-Element, das nur Nachrichten auswählt, deren benutzerdefinierte `MessageNumber`-Eigenschaft kleiner oder gleich drei ist.

```php
$subscriptionInfo = new SubscriptionInfo("LowMessages");
$serviceBusRestProxy->createSubscription("mytopic", $subscriptionInfo);

$serviceBusRestProxy->deleteRule("mytopic", "LowMessages", '$Default');

$ruleInfo = new RuleInfo("LowMessagesRule");
$ruleInfo->withSqlFilter("MessageNumber <= 3");
$ruleResult = $serviceBusRestProxy->createRule("mytopic", "LowMessages", $ruleInfo);
```

Wenn jetzt eine Nachricht an das `mytopic`-Thema gesendet wird, wird diese nun stets an die Empfänger des `mysubscription`-Abonnements zugestellt. Sie wird selektiv an die Empfänger der Abonnements `HighMessages` und `LowMessages` zugestellt (je nach Inhalt der Nachricht).

## <a name="send-messages-to-a-topic"></a>Senden von Nachrichten an ein Thema
Um eine Nachricht an ein Service Bus-Thema zu senden, ruft Ihre Anwendung die `ServiceBusRestProxy->sendTopicMessage`-Methode auf. Der folgende Code zeigt, wie Sie eine Nachricht an das zuvor erstellte `mytopic`-Thema im `MySBNamespace`-Dienstnamespace senden können.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");

    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

An Service Bus-Themen gesendete Nachrichten sind Instanzen der [BrokeredMessage][BrokeredMessage]-Klasse. [BrokeredMessage][BrokeredMessage]-Objekte verfügen über eine Reihe von Standardeigenschaften und -methoden sowie über Eigenschaften zum Speichern benutzerdefinierter anwendungsspezifischer Eigenschaften. Das folgende Beispiel zeigt das Senden von fünf Testnachrichten an das zuvor erstellte `mytopic`-Thema. Mit der `setProperty`-Methode wird jeder Nachricht eine benutzerdefinierte Eigenschaft (`MessageNumber`) hinzugefügt. Der Wert der `MessageNumber`-Eigenschaft variiert in jeder Nachricht (auf diese Weise können Sie bestimmen, welche Abonnements sie erhalten, wie im Abschnitt [Erstellen eines Abonnements](#create-a-subscription) erläutert):

```php
for($i = 0; $i < 5; $i++){
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message ".$i);

    // Set custom property.
    $message->setProperty("MessageNumber", $i);

    // Send message.
    $serviceBusRestProxy->sendTopicMessage("mytopic", $message);
}
```

Service Bus-Themen unterstützen eine maximale Nachrichtengröße von 256 KB im [Standard-Tarif](service-bus-premium-messaging.md) und 1 MB im [Premium-Tarif](service-bus-premium-messaging.md). Der Header, der die standardmäßigen und benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 KB haben. Es gibt keine Beschränkung für die Anzahl der Nachrichten, die ein Thema enthält. Es gibt jedoch eine Obergrenze für die Gesamtgröße der Nachrichten eines Themas. Die Obergrenze für die Themengröße beträgt 5 GB. Weitere Informationen zu Kontingenten finden Sie unter [Service Bus-Kontingente][Service Bus quotas].

## <a name="receive-messages-from-a-subscription"></a>Empfangen von Nachrichten aus einem Abonnement
Die beste Vorgehensweise zum Empfangen von Nachrichten aus einem Abonnement ist die Verwendung einer `ServiceBusRestProxy->receiveSubscriptionMessage`-Methode. Nachrichten können in zwei unterschiedlichen Modi empfangen werden: [*ReceiveAndDelete* und *PeekLock*](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode). **PeekLock** ist die Standardeinstellung.

Bei Verwendung des [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode)-Modus ist der Nachrichtenempfang ein Single-Shot-Vorgang. Dies bedeutet: Wenn Service Bus eine Leseanforderung für eine Nachricht in einem Abonnement erhält, wird die Nachricht als verarbeitet gekennzeichnet und an die Anwendung zurückgesendet. Der [ReceiveAndDelete](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode)*-Modus ist das einfachste Modell. Er wird am besten für Szenarios eingesetzt, bei denen eine Anwendung tolerieren kann, wenn eine Nachricht bei Auftreten eines Fehlers nicht verarbeitet wird. Um dieses Verfahren zu verstehen, stellen Sie sich ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wird. Da die Nachricht von Service Bus als verarbeitet gekennzeichnet wurde, wird die vor dem Absturz verarbeitete Nachricht nicht berücksichtigt, wenn die Anwendung neu gestartet und die Verarbeitung von Nachrichten wieder aufgenommen wird.

Im standardmäßigen [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode)-Modus ist der Nachrichtenempfang zweistufig. Dadurch können Anwendungen ohne Toleranz für fehlende Nachrichten unterstützt werden. Wenn Service Bus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann zurück an die Anwendung. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen (oder sie zur späteren Verarbeitung zuverlässig gespeichert) hat, führt Sie die zweite Phase des Empfangsprozesses aus, indem sie die empfangene Nachricht an `ServiceBusRestProxy->deleteMessage` übergibt. Wenn Service Bus den `deleteMessage`-Aufruf registriert, wird die Nachricht als verarbeitet markiert und aus der Warteschlange entfernt.

Das folgende Beispiel zeigt, wie eine Nachricht mit dem (standardmäßigen) [PeekLock](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.receivemode)-Modus empfangen und verarbeitet wird. 

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Set receive mode to PeekLock (default is ReceiveAndDelete)
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Get message.
    $message = $serviceBusRestProxy->receiveSubscriptionMessage("mytopic", "mysubscription", $options);

    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Deleting message...<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Behandeln von Anwendungsabstürzen und nicht lesbaren Nachrichten
Service Bus stellt Funktionen zur Verfügung, die Sie bei der ordnungsgemäßen Behandlung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht unterstützen. Falls eine Empfängeranwendung die Nachricht aus einem bestimmten Grund nicht verarbeiten kann, kann sie anstelle der `deleteMessage`-Methode die `unlockMessage`-Methode für die empfangene Nachricht aufrufen. Dies führt dazu, dass Service Bus die Nachricht innerhalb der Warteschlange entsperrt und verfügbar macht, damit sie erneut empfangen werden kann, und zwar entweder durch dieselbe verarbeitende Anwendung oder durch eine andere verarbeitende Anwendung.

Mit einer innerhalb der Warteschlange gesperrten Nachricht ist außerdem eine Zeitlimitüberschreitung verknüpft. Wenn die Anwendung die Nachricht nicht verarbeiten kann, bevor die Zeitlimitüberschreitung der Sperre abläuft (z. B. falls die Anwendung abstürzt), so entsperrt Service Bus die Nachricht automatisch und macht sie verfügbar, sodass sie wieder empfangen werden kann.

Falls die Anwendung nach der Verarbeitung der Nachricht, aber vor der Ausgabe der `deleteMessage`-Anforderung abstürzt, wird die Nachricht erneut an die Anwendung übermittelt, wenn diese neu gestartet wird. Dieser Prozesstyp wird häufig als *At Least Once Processing* (Mindestens einmalige Verarbeitung) bezeichnet und bedeutet, dass jede Nachricht mindestens einmal verarbeitet wird, wobei eine Nachricht in bestimmten Situationen unter Umständen erneut übermittelt wird. Wenn eine doppelte Verarbeitung im betreffenden Szenario nicht geeignet ist, sollten Anwendungsentwickler Anwendungen zusätzliche Logik für den Umgang mit der Übermittlung doppelter Nachrichten hinzufügen. Dies wird häufig durch die Verwendung der `getMessageId`-Methode der Nachricht erreicht, die auch nach mehreren Übermittlungsversuchen konstant bleibt.

## <a name="delete-topics-and-subscriptions"></a>Löschen von Themen und Abonnements
Verwenden Sie zum Löschen eines Themas oder Abonnements die `ServiceBusRestProxy->deleteTopic`- bzw. die `ServiceBusRestProxy->deleteSubscripton`-Methode. Durch das Löschen eines Themas werden auch alle Abonnements gelöscht, die mit dem Thema registriert sind.

Im folgenden Beispiel wird gezeigt, wie ein Thema (`mytopic`) und die darin registrierten Abonnements gelöscht werden.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\ServiceBus\ServiceBusService;
use WindowsAzure\ServiceBus\ServiceBusSettings;
use WindowsAzure\Common\ServiceException;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try {
    // Delete topic.
    $serviceBusRestProxy->deleteTopic("mytopic");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

Mithilfe der `deleteSubscription`-Methode können Sie ein Abonnement unabhängig löschen:

```php
$serviceBusRestProxy->deleteSubscription("mytopic", "mysubscription");
```

> [!NOTE]
> Sie können Service Bus-Ressourcen mit dem [Service Bus-Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/) verwalten. Mit dem Service Bus-Explorer können Benutzer eine Verbindung mit einem Service Bus-Namespace herstellen und Messagingentitäten auf einfache Weise verwalten. Das Tool stellt erweiterte Features wie Import-/Exportfunktionen oder Testmöglichkeiten für Themen, Warteschlangen, Abonnements, Relaydienste, Notification Hubs und Event Hubs zur Verfügung. 

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie unter [Service Bus-Warteschlangen, -Themen und -Abonnements][Queues, topics, and subscriptions].

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[sqlfilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[require-once]: https://php.net/require_once
[Service Bus quotas]: service-bus-quotas.md
