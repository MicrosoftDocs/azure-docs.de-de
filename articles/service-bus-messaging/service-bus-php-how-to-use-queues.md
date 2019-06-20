---
title: Verwenden von Service Bus-Warteschlangen mit PHP | Microsoft Docs
description: Erfahren Sie mehr über die Verwendung von Service Bus-Warteschlangen in Azure. Die Codebeispiele wurden in PHP geschrieben.
services: service-bus-messaging
documentationcenter: php
author: axisc
manager: timlt
editor: spelluru
ms.assetid: e29c829b-44c5-4350-8f2e-39e0c380a9f2
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 92ea3c71dda011c5f7b19682d9bdea6c226ae5d2
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65992081"
---
# <a name="how-to-use-service-bus-queues-with-php"></a>Verwenden von Service Bus-Warteschlangen mit PHP
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

In diesem Tutorial erfahren Sie, wie Sie PHP-Anwendungen erstellen, um Nachrichten an eine Service Bus-Warteschlange zu senden und Antworten zu empfangen. 

## <a name="prerequisites"></a>Voraussetzungen
1. Ein Azure-Abonnement. Um dieses Tutorial abzuschließen, benötigen Sie ein Azure-Konto. Sie können Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF) registrieren.
2. Wenn Sie über keine Warteschlange verfügen, führen Sie die Schritte im Artikel [Schnellstart: Erstellen einer Service Bus-Warteschlange mithilfe des Azure-Portals](service-bus-quickstart-portal.md) aus, um eine Warteschlange zu erstellen.
    1. Lesen Sie die kurze **Übersicht** über Service Bus-**Warteschlangen**. 
    2. Erstellen Sie einen Service Bus-**Namespace**. 
    3. Rufen Sie die **Verbindungszeichenfolge** ab. 

        > [!NOTE]
        > In diesem Tutorial erstellen Sie eine **Warteschlange** im Service Bus-Namespace mithilfe von PHP. 
3. [Azure SDK für PHP](../php-download-sdk.md)

## <a name="create-a-php-application"></a>Erstellen einer PHP-Anwendung
Die einzige Voraussetzung für das Erstellen einer PHP-Anwendung, die auf den Azure-Blob-Dienst zugreift, ist das Verweisen auf Klassen im [Azure-SDK für PHP](../php-download-sdk.md) aus dem Code heraus. Sie können die Anwendung mit beliebigen Entwicklungstools oder mit Editor erstellen.

> [!NOTE]
> In Ihrer PHP-Installation muss außerdem die [OpenSSL-Erweiterung](https://php.net/openssl) installiert und aktiviert sein.

In diesem Leitfaden verwenden Sie Dienstfunktionen, die lokal aus einer PHP-Anwendung heraus oder im Code, der in einer Azure-Webrolle, -Workerrolle oder -Website ausgeführt wird, aufgerufen werden.

## <a name="get-the-azure-client-libraries"></a>Abrufen der Azure-Clientbibliotheken
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus
Um die APIs für Service Bus-Warteschlangen zu verwenden, gehen Sie folgendermaßen vor:

1. Verweisen Sie mithilfe der [require_once][require_once]-Anweisung auf die Autoloaderdatei.
2. Verweisen Sie auf alle Klassen, die Sie möglicherweise verwenden.

Das folgende Beispiel zeigt, wie die Autoloaderdatei eingeschlossen und die `ServicesBuilder`-Klasse referenziert wird.

> [!NOTE]
> In diesem Beispiel (und in anderen Beispielen in diesem Artikel) wird angenommen, dass Sie die PHP-Clientbibliotheken für Azure über Composer installiert haben. Wenn Sie die Bibliotheken manuell oder als PEAR-Paket installiert haben, müssen Sie auf die Autoloaderdatei **WindowsAzure.php** verweisen.
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

In den Beispielen weiter unten wird die `require_once`-Anweisung immer angezeigt. Jedoch wird nur auf die für die Ausführung des Beispiels erforderlichen Klassen verwiesen.

## <a name="set-up-a-service-bus-connection"></a>Einrichten einer Service Bus-Verbindung
Um einen Service Bus-Client zu instanziieren, benötigen Sie zunächst eine gültige Verbindungszeichenfolge mit diesem Format:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Hierbei hat der `Endpoint` normalerweise das Format `[yourNamespace].servicebus.windows.net`.

Zum Erstellen eines Azure-Dienstclients muss die `ServicesBuilder`-Klasse verwendet werden. Ihre Möglichkeiten:

* die Verbindungszeichenfolge direkt an die Klasse weitergeben.
* den **CloudConfigurationManager (CCM)** verwenden, um mehrere externe Quellen für die Verbindungszeichenfolge zu überprüfen:
  * Standardmäßig wird eine externe Quelle unterstützt – Umgebungsvariablen
  * Sie können neue Quellen durch Erweitern der `ConnectionStringSource`-Klasse hinzufügen.

Für die hier erläuterten Beispiele wird die Verbindungszeichenfolge direkt weitergegeben.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-queue"></a>Erstellen einer Warteschlange
Verwaltungsvorgänge für Service Bus-Warteschlangen können über die `ServiceBusRestProxy`-Klasse durchgeführt werden. Ein `ServiceBusRestProxy`-Objekt wird über die `ServicesBuilder::createServiceBusService`-Factorymethode mit einer entsprechenden Verbindungszeichenfolge erstellt, die die Tokenberechtigungen für deren Verwaltung kapselt.

Das folgende Beispiel zeigt, wie Sie `ServiceBusRestProxy` instanziieren und `ServiceBusRestProxy->createQueue` aufrufen, um in einem `MySBNamespace`-Dienstnamespace eine Warteschlange namens `myqueue` zu erstellen:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\QueueInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    $queueInfo = new QueueInfo("myqueue");

    // Create queue.
    $serviceBusRestProxy->createQueue($queueInfo);
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
> Sie können die `listQueues`-Methode bei `ServiceBusRestProxy`-Objekten verwenden, um zu überprüfen, ob eine Warteschlange mit einem angegebenen Namen bereits innerhalb eines Namespace vorhanden ist.
> 
> 

## <a name="send-messages-to-a-queue"></a>Senden von Nachrichten an eine Warteschlange
Um eine Nachricht an eine Service Bus-Warteschlange zu senden, ruft Ihre Anwendung die `ServiceBusRestProxy->sendQueueMessage`-Methode auf. Der folgende Code zeigt, wie Sie eine Nachricht an die zuvor erstellte `myqueue`-Warteschlange im `MySBNamespace`-Dienstnamespace senden können.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");

    // Send message.
    $serviceBusRestProxy->sendQueueMessage("myqueue", $message);
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

Die Nachrichten, die an die Service Bus-Warteschlangen gesendet werden (und von diesen eingehen), sind Instanzen der [BrokeredMessage][BrokeredMessage]-Klasse. [BrokeredMessage][BrokeredMessage]-Objekte verfügen über einen Satz von Standardmethoden und -eigenschaften zum Speichern benutzerdefinierter anwendungsspezifischer Eigenschaften sowie über Text mit beliebigen Anwendungsdaten.

Service Bus-Warteschlangen unterstützen eine maximale Nachrichtengröße von 256 KB für den [Standard-Tarif](service-bus-premium-messaging.md) und 1 MB für den [Premium-Tarif](service-bus-premium-messaging.md). Der Header, der die standardmäßigen und benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 KB haben. Bei der Anzahl der Nachrichten, die in einer Warteschlange aufgenommen werden können, besteht keine Beschränkung. Allerdings gilt eine Deckelung bei der Gesamtgröße der in einer Warteschlange aufzunehmenden Nachrichten. Die Obergrenze für die Warteschlangengröße beträgt 5 GB.

## <a name="receive-messages-from-a-queue"></a>Empfangen von Nachrichten aus einer Warteschlange

Die beste Vorgehensweise zum Empfangen von Nachrichten aus einer Warteschlange ist die Verwendung einer `ServiceBusRestProxy->receiveQueueMessage`-Methode. Nachrichten können in zwei unterschiedlichen Modi empfangen werden: [*ReceiveAndDelete*](/dotnet/api/microsoft.servicebus.messaging.receivemode) und [*PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock). **PeekLock** ist die Standardeinstellung.

Bei Verwendung des [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode)-Modus ist der Nachrichtenempfang ein Single-Shot-Vorgang. Das bedeutet, wenn Service Bus eine Leseanforderung für eine Nachricht in einer Warteschlange erhält, wird die Nachricht als verarbeitet gekennzeichnet und an die Anwendung zurückgegeben. Der [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode)-Modus ist das einfachste Modell. Er wird am besten für Szenarios eingesetzt, bei denen es eine Anwendung tolerieren kann, wenn eine Nachricht bei Auftreten eines Fehlers nicht verarbeitet wird. Um dieses Verfahren zu verstehen, stellen Sie sich ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wird. Da Service Bus die Nachricht als verwendet markiert hat, wird er jene Nachricht auslassen, die vor dem Absturz verwendet wurde, wenn die Anwendung neu startet und erneut mit der Verwendung von Nachrichten beginnt.

Im standardmäßigen [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock)-Modus ist der Nachrichtenempfang zweistufig. Dadurch können Anwendungen ohne Toleranz für fehlende Nachrichten unterstützt werden. Wenn Service Bus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann an die Anwendung zurück. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen (oder sie zur späteren Verarbeitung zuverlässig gespeichert) hat, führt Sie die zweite Phase des Empfangsprozesses aus, indem sie die empfangene Nachricht an `ServiceBusRestProxy->deleteMessage` übergibt. Wenn Service Bus den `deleteMessage`-Aufruf registriert, wird die Nachricht als verarbeitet markiert und aus der Warteschlange entfernt.

Das folgende Beispiel zeigt, wie eine Nachricht mit dem (standardmäßigen) [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock)-Modus empfangen und verarbeitet wird.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Set the receive mode to PeekLock (default is ReceiveAndDelete).
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Receive message.
    $message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Message deleted.<br />";
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

Zudem wird einer in der Warteschlange gesperrten Anwendung ein Zeitlimit zugeordnet. Wenn die Anwendung die Nachricht vor Ablauf des Sperrzeitlimits nicht verarbeiten kann (zum Beispiel wenn die Anwendung abstürzt), entsperrt Service Bus die Nachricht automatisch und macht sie verfügbar, um erneut empfangen zu werden.

Falls die Anwendung nach der Verarbeitung der Nachricht, aber vor der Ausgabe der `deleteMessage`-Anforderung abstürzt, wird die Nachricht erneut an die Anwendung übermittelt, wenn diese neu gestartet wird. Dies wird häufig als *At Least Once Processing* (Mindestens einmalige Verarbeitung) bezeichnet und bedeutet, dass jede Nachricht mindestens einmal verarbeitet wird, wobei eine Nachricht in bestimmten Situationen unter Umständen erneut übermittelt wird. Wenn eine doppelte Verarbeitung in dem Szenario nicht zulässig ist, sollten Anwendungen zusätzliche Logik für den Umgang mit der Zustellung doppelter Nachrichten enthalten. Dies wird häufig durch die Verwendung der `getMessageId`-Methode der Nachricht erreicht, die über mehrere Übermittlungsversuche hinweg konstant bleibt.

> [!NOTE]
> Sie können Service Bus-Ressourcen mit dem [Service Bus-Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/) verwalten. Mit dem Service Bus-Explorer können Benutzer eine Verbindung mit einem Service Bus-Namespace herstellen und Messagingentitäten auf einfache Weise verwalten. Das Tool stellt erweiterte Features wie Import-/Exportfunktionen oder Testmöglichkeiten für Themen, Warteschlangen, Abonnements, Relaydienste, Notification Hubs und Event Hubs zur Verfügung. 

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie nun mit den Grundlagen von Service Bus-Warteschlangen vertraut sind, finden Sie weitere Informationen unter [Warteschlangen, Themen und Abonnements][Queues, topics, and subscriptions].

Weitere Informationen finden Sie auch im [PHP Developer Center](https://azure.microsoft.com/develop/php/).

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: https://php.net/require_once


