---
title: 'Verwenden von Queue Storage mit PHP: Azure Storage'
description: Erfahren Sie, wie Sie den Azure-Warteschlangenspeicherdienst zum Erstellen und Löschen von Warteschlangen sowie zum Einfügen, Abrufen und Löschen von Nachrichten verwenden. Die Beispiele sind in PHP geschrieben.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2018
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.openlocfilehash: 0e5b7ed75f22659a9a38ac761cc61c841102a067
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93345838"
---
# <a name="how-to-use-queue-storage-from-php"></a>Verwenden des Warteschlangenspeichers mit PHP

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

In diesem Leitfaden wird die Durchführung häufiger Szenarios mit Azure Queue Storage demonstriert. Die Beispiele sind mit Klassen aus der [Azure Storage-Clientbibliothek für PHP][download] geschrieben. Zu den beschriebenen Szenarien gehören das Einfügen, Einsehen, Abrufen und Löschen von Warteschlangennachrichten sowie das Erstellen und Löschen von Warteschlangen.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-php-application"></a>Erstellen einer PHP-Anwendung

Die einzige Voraussetzung für das Erstellen einer PHP-Anwendung, die auf Azure Queue Storage zugreift, ist ein Verweis auf Klassen in der [Azure Storage-Clientbibliothek für PHP][download] im Code. Sie können die Anwendung mit beliebigen Entwicklungstools erstellen, unter anderem auch mit Notepad.

In diesem Leitfaden verwenden Sie Features von Azure Queue Storage, die lokal innerhalb einer PHP-Anwendung oder im Code aufgerufen werden können, der in einer Webanwendung in Azure ausgeführt wird.

## <a name="get-the-azure-client-libraries"></a>Abrufen der Azure-Clientbibliotheken

### <a name="install-via-composer"></a>Installation mithilfe von Composer

1. Erstellen Sie im Stammverzeichnis Ihres Projekts eine Datei namens **composer.json** , und fügen Sie zu dieser den folgenden Code hinzu:

    ```json
    {
      "require": {
        "microsoft/azure-storage-queue": "*"
      }
    }
    ```

2. Laden Sie **[composer.phar][composer-phar]** in das Stammverzeichnis Ihres Projekts herunter.
3. Öffnen Sie eine Eingabeaufforderung und führen Sie in Ihrem Projektverzeichnis folgenden Befehl aus

    ```
    php composer.phar install
    ```

Navigieren Sie alternativ zur [Azure Storage PHP-Clientbibliothek][download] auf GitHub, um den Quellcode zu klonen.

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurieren Ihrer Anwendung für den Zugriff auf den Warteschlangenspeicher

Um die APIs für den Azure-Warteschlangenspeicher verwenden zu können, müssen Sie:

1. mithilfe der [require_once]-Anweisung auf die Autoloaderdatei verweisen.
2. auf alle Klassen verweisen, die Sie möglicherweise verwenden.

Das folgende Beispiel zeigt, wie die Autoloaderdatei eingeschlossen und die **QueueRestProxy** -Klasse referenziert wird.

```php
require_once 'vendor/autoload.php';
use MicrosoftAzure\Storage\Queue\QueueRestProxy;
```

In den folgenden Beispielen wird die `require_once`-Anweisung immer angezeigt. Jedoch wird nur auf die für die Ausführung des Beispiels erforderlichen Klassen verwiesen.

## <a name="set-up-an-azure-storage-connection"></a>Einrichten einer Azure-Speicherverbindung

Um einen Client für den Azure-Warteschlangenspeicher zu instanziieren, benötigen Sie zunächst eine gültige Verbindungszeichenfolge. Das Format der Warteschlangendienst-Verbindungszeichenfolge lautet wie folgt.

Für den Zugriff auf einen Livedienst:

```php
DefaultEndpointsProtocol=[http|https];AccountName=[yourAccount];AccountKey=[yourKey]
```

Für den Zugriff auf den Emulatorspeicher:

```php
UseDevelopmentStorage=true
```

Um einen Azure Queue-Dienstclient erstellen zu können, müssen Sie die **QueueRestProxy** -Klasse verwenden. Dazu können Sie eine der folgenden Methoden verwenden:

- die Verbindungszeichenfolge direkt an die Klasse weitergeben.
- Verwenden Sie Umgebungsvariablen in Ihrer Web-App, um die Verbindungszeichenfolge zu speichern. Weitere Informationen zum Konfigurieren von Verbindungszeichenfolgen finden Sie im Dokument [Azure web app configuration settings (Konfigurationseinstellungen für die Azure-Web-App)](../../app-service/configure-common.md).

Für die hier erläuterten Beispiele wird die Verbindungszeichenfolge direkt weitergegeben.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";
$queueClient = QueueRestProxy::createQueueService($connectionString);
```

## <a name="create-a-queue"></a>Erstellen einer Warteschlange

Über ein **QueueRestProxy** -Objekt können Sie eine Warteschlange mithilfe der **createQueue** -Methode erstellen. Bei der Erstellung von Warteschlangen können Sie verschiedene Optionen festlegen, was allerdings nicht erforderlich ist. (Das folgende Beispiel zeigt, wie Sie die Metadaten für eine Warteschlange festlegen.)

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateQueueOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set queue metadata.
$createQueueOptions = new CreateQueueOptions();
$createQueueOptions->addMetaData("key1", "value1");
$createQueueOptions->addMetaData("key2", "value2");

try    {
    // Create queue.
    $queueClient->createQueue("myqueue", $createQueueOptions);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> Metadaten-Schlüssel unterscheiden nicht immer zwischen Groß- und Kleinschreibung. Alle Schlüssel werden vom Dienst in Kleinbuchstaben gelesen.

## <a name="add-a-message-to-a-queue"></a>Hinzufügen von Nachrichten zu einer Warteschlange

Verwenden Sie **QueueRestProxy->createMessage** , um Nachrichten zu einer Warteschlange hinzuzufügen. Übergeben Sie der Methode den Warteschlangennamen, Nachrichtentext und (optionale) Nachrichtenoptionen.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\CreateMessageOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Create message.
    $queueClient->createMessage("myqueue", "Hello World!");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="peek-at-the-next-message"></a>Einsehen der nächsten Nachricht

Sie können einen Blick auf die Nachricht(en) am Anfang einer Warteschlange werfen, ohne diese aus der Warteschlange zu entfernen, indem Sie **QueueRestProxy->peekMessages** aufrufen. Die Methode **peekMessage** gibt standardmäßig eine einzelne Nachricht zurück. Sie können diesen Wert jedoch mithilfe der Methode **PeekMessagesOptions->setNumberOfMessages** ändern.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\PeekMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// OPTIONAL: Set peek message options.
$message_options = new PeekMessagesOptions();
$message_options->setNumberOfMessages(1); // Default value is 1.

try    {
    $peekMessagesResult = $queueClient->peekMessages("myqueue", $message_options);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

$messages = $peekMessagesResult->getQueueMessages();

// View messages.
$messageCount = count($messages);
if($messageCount <= 0){
    echo "There are no messages.<br />";
}
else{
    foreach($messages as $message)    {
        echo "Peeked message:<br />";
        echo "Message Id: ".$message->getMessageId()."<br />";
        echo "Date: ".date_format($message->getInsertionDate(), 'Y-m-d')."<br />";
        echo "Message text: ".$message->getMessageText()."<br /><br />";
    }
}
```

## <a name="de-queue-the-next-message"></a>Entfernen der nächsten Nachricht aus der Warteschlange

Dieser Code entfernt eine Nachricht in zwei Schritten aus der Warteschlange. Zunächst rufen Sie **QueueRestProxy->listMessages** auf, um die Nachricht unsichtbar für anderen Code zu machen, der aus der Warteschlange liest. Standardmäßig bleibt die Nachricht 30 Sekunden lang unsichtbar. (Falls sie in diesem Zeitraum nicht gelöscht wird, kann sie wieder aus der Warteschlange gelesen werden.) Um die Nachricht endgültig aus der Warteschlange zu entfernen, müssen Sie **QueueRestProxy->deleteMessage** aufrufen. Dieser zweistufige Prozess zum Entfernen von Nachrichten stellt sicher, dass eine andere Codeinstanz dieselbe Nachricht erneut abrufen kann, falls die Verarbeitung aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. Der Code ruft **deleteMessage** direkt nach der Verarbeitung der Nachricht auf.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

/* ---------------------
    Process message.
   --------------------- */

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Delete message.
    $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="change-the-contents-of-a-queued-message"></a>Ändern des Inhalts von Nachrichten in der Warteschlange

Sie können den Inhalt einer Nachricht vor Ort in der Warteschlange ändern, indem Sie **QueueRestProxy->updateMessage** aufrufen. Wenn die Nachricht eine Arbeitsaufgabe darstellt, können Sie diese Funktion verwenden, um den Status der Aufgabe zu aktualisieren. Mit dem folgenden Code wird die Warteschlangennachricht mit neuem Inhalt aktualisiert und das Sichtbarkeits-Zeitlimit um weitere 60 Sekunden verlängert. Dadurch wird der mit der Nachricht verknüpfte Arbeitsstatus gespeichert, und der Client erhält eine weitere Minute zur Bearbeitung der Nachricht. Sie können diese Technik verwenden, um Workflows mit mehreren Schritten in Warteschlangennachrichten zu verfolgen, ohne von vorn beginnen zu müssen, wenn ein Verarbeitungsschritt aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. In der Regel behalten Sie auch die Anzahl der Wiederholungen bei, und wenn die Nachricht mehr als *n* Mal wiederholt wurde, wird sie gelöscht. Dies verhindert, dass eine Nachricht bei jeder Verarbeitung einen Anwendungsfehler auslöst.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Get message.
$listMessagesResult = $queueClient->listMessages("myqueue");
$messages = $listMessagesResult->getQueueMessages();
$message = $messages[0];

// Define new message properties.
$new_message_text = "New message text.";
$new_visibility_timeout = 5; // Measured in seconds.

// Get message ID and pop receipt.
$messageId = $message->getMessageId();
$popReceipt = $message->getPopReceipt();

try    {
    // Update message.
    $queueClient->updateMessage("myqueue",
                                $messageId,
                                $popReceipt,
                                $new_message_text,
                                $new_visibility_timeout);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="additional-options-for-de-queuing-messages"></a>Zusätzliche Optionen für Nachrichten aus der Warteschlange

Es gibt zwei Möglichkeiten, wie Sie das Abrufen von Nachrichten aus der Warteschlange anpassen können. Erstens können Sie einen Nachrichtenstapel abrufen (bis zu 32). Zweitens können Sie das Sichtbarkeits-Zeitlimit verkürzen oder verlängern, sodass der Code mehr oder weniger Zeit zur vollständigen Verarbeitung jeder Nachricht benötigt. Im folgenden Codebeispiel wird **getMessages** verwendet, um 16 Nachrichten mit einem Aufruf abzurufen. Anschließend wird jede Nachricht mithilfe einer **for** -Schleife verarbeitet. Außerdem wird das Unsichtbarkeits-Zeitlimit auf fünf Minuten pro Nachricht festgelegt.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;
use MicrosoftAzure\Storage\Queue\Models\ListMessagesOptions;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

// Set list message options.
$message_options = new ListMessagesOptions();
$message_options->setVisibilityTimeoutInSeconds(300);
$message_options->setNumberOfMessages(16);

// Get messages.
try{
    $listMessagesResult = $queueClient->listMessages("myqueue",
                                                     $message_options);
    $messages = $listMessagesResult->getQueueMessages();

    foreach($messages as $message){

        /* ---------------------
            Process message.
        --------------------- */

        // Get message Id and pop receipt.
        $messageId = $message->getMessageId();
        $popReceipt = $message->getPopReceipt();

        // Delete message.
        $queueClient->deleteMessage("myqueue", $messageId, $popReceipt);
    }
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="get-queue-length"></a>Abrufen der Warteschlangenlänge

Sie können die Anzahl der Nachrichten in einer Warteschlange schätzen lassen. Die Methode **QueueRestProxy->getQueueMetadata** ruft Metadaten zur Warteschlange vom Warteschlangendienst ab. Rufen Sie die Methode **getApproximateMessageCount** auf das zurückgegebene Objekt auf, um die Anzahl der Nachrichten in der Warteschlange abzurufen. Die Anzahl ist nur ein ungefährer Wert, da seit der Antwort des Warteschlangendienstes möglicherweise bereits Nachrichten hinzugefügt oder gelöscht wurden.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Get queue metadata.
    $queue_metadata = $queueClient->getQueueMetadata("myqueue");
    $approx_msg_count = $queue_metadata->getApproximateMessageCount();
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}

echo $approx_msg_count;
```

## <a name="delete-a-queue"></a>Löschen einer Warteschlange

Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie die Methode **QueueRestProxy->deleteQueue** auf.

```php
require_once 'vendor/autoload.php';

use MicrosoftAzure\Storage\Queue\QueueRestProxy;
use MicrosoftAzure\Storage\Common\Exceptions\ServiceException;

$connectionString = "DefaultEndpointsProtocol=http;AccountName=<accountNameHere>;AccountKey=<accountKeyHere>";

// Create queue REST proxy.
$queueClient = QueueRestProxy::createQueueService($connectionString);

try    {
    // Delete queue.
    $queueClient->deleteQueue("myqueue");
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://msdn.microsoft.com/library/azure/dd179446.aspx
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen des Azure-Warteschlangenspeichers vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden:

- Machen Sie sich mit der [API-Referenz für die Azure Storage PHP-Clientbibliothek](https://azure.github.io/azure-storage-php/) vertraut.
- Sehen Sie sich das [Beispiel für erweiterte Warteschlangen](https://github.com/Azure/azure-storage-php/blob/master/samples/QueueSamples.php) an.

Weitere Informationen finden Sie außerdem im [PHP Developer Center](https://azure.microsoft.com/develop/php/).

[download]: https://github.com/Azure/azure-storage-php
[require_once]: https://www.php.net/manual/en/function.require-once.php
[Azure Portal]: https://portal.azure.com
[composer-phar]: https://getcomposer.org/composer.phar
