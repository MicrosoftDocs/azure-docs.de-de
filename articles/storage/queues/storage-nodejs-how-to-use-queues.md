---
title: 'Verwenden von Queue Storage mit Node.js: Azure Storage'
description: Erfahren Sie, wie Sie den Azure-Warteschlangendienst zum Erstellen und Löschen von Warteschlangen sowie zum Einfügen, Abrufen und Löschen von Nachrichten verwenden. Die Beispiele sind in Node.js geschrieben.
services: storage
author: mhopkins-msft
ms.service: storage
ms.devlang: nodejs
ms.topic: article
ms.date: 12/08/2016
ms.author: mhopkins
ms.reviewer: cbrooks
ms.subservice: queues
ms.openlocfilehash: 01afe1ab7b9028f3f77d52f7d6f8ced27f6a79c7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65142710"
---
# <a name="how-to-use-queue-storage-from-nodejs"></a>Verwenden des Warteschlangenspeichers mit Node.js
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>Übersicht
In diesem Leitfaden wird die Durchführung häufiger Szenarios mit dem Microsoft Azure-Warteschlangendienst erläutert. Die Beispiele sind in C# geschrieben und verwenden die Node.js-API. Zu den Szenarien gehören das **Einfügen**, **Einsehen**, **Abrufen** und **Löschen** von Warteschlangennachrichten sowie das **Erstellen und Löschen von Warteschlangen**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Erstellen einer Node.js-Anwendung
Erstellen Sie eine leere Node.js-Anwendung. Anweisungen zum Erstellen von Node.js-Anwendungen finden Sie unter [Erstellen einer Node.js-Web-App in Azure App Service](../../app-service/app-service-web-get-started-nodejs.md), [Erstellen und Bereitstellen einer Node.js-Anwendung in einem Azure-Clouddienst](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) mithilfe von Windows PowerShell oder [Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial).

## <a name="configure-your-application-to-access-storage"></a>Konfigurieren der Anwendung für den Speicherzugriff
Um Azure-Speicher verwenden zu können, müssen Sie das Azure Storage-SDK für Node.js herunterladen. Es enthält eine Reihe von Bibliotheken, die mit den REST-Speicherdiensten kommunizieren.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Verwenden von Node Package Manager (NPM) zum Beziehen des Pakets
1. Verwenden Sie eine Befehlszeilenschnittstelle, z.B.**PowerShell** (Windows,) **Terminal** (Mac) oder **Bash** (Unix), und navigieren Sie zu dem Ordner, in dem Sie die Beispielanwendung erstellt haben.
2. Geben Sie **npm install azure-storage** in das Befehlsfenster ein. Die Ausgabe des Befehls ähnelt dem folgenden Beispiel.
 
    ```bash
    azure-storage@0.5.0 node_modules\azure-storage
    +-- extend@1.2.1
    +-- xmlbuilder@0.4.3
    +-- mime@1.2.11
    +-- node-uuid@1.4.3
    +-- validator@3.22.2
    +-- underscore@1.4.4
    +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
    +-- xml2js@0.2.7 (sax@0.5.2)
    +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
    ```

3. Sie können den Befehl **ls** manuell ausführen, um zu überprüfen, ob der Ordner **node\_modules** erstellt wurde. In diesem Ordner finden Sie das Paket **azure-storage** mit den Bibliotheken, die Sie benötigen, um auf den Speicher zuzugreifen.

### <a name="import-the-package"></a>Importieren des Pakets
Verwenden Sie Editor oder einen anderen Texteditor, um die folgende Zeile am Anfang der Datei **server.js** der Anwendung einzufügen, in der Sie den Speicher nutzen möchten:

```javascript
var azure = require('azure-storage');
```

## <a name="setup-an-azure-storage-connection"></a>Einrichten einer Azure-Speicherverbindung
Das Azure-Modul liest die Umgebungsvariablen AZURE\_STORAGE\_ACCOUNT und AZURE\_STORAGE\_ACCESS\_KEY oder AZURE\_STORAGE\_CONNECTION\_STRING nach Informationen aus, die erforderlich sind, um eine Verbindung mit dem Azure-Speicherkonto herzustellen. Falls diese Umgebungsvariablen nicht gesetzt sind, müssen Sie die Kontoinformationen beim Aufruf von **createQueueService**angeben.

## <a name="how-to-create-a-queue"></a>Anleitung: Erstellen einer Warteschlange
Durch folgenden Code wird ein **QueueService** -Objekt erstellt, das Ihnen das Arbeiten mit Warteschlangen ermöglicht.

```javascript
var queueSvc = azure.createQueueService();
```

Verwenden Sie die **createQueueIfNotExists** -Methode, die die angegebene Warteschlange zurückgibt, wenn sie bereits vorhanden ist, oder eine neue Warteschlange mit dem angegebenen Namen erstellt, sollte sie noch nicht vorhanden sein.

```javascript
queueSvc.createQueueIfNotExists('myqueue', function(error, results, response){
  if(!error){
    // Queue created or exists
  }
});
```

Wenn die Warteschlange erstellt wird, ist `result.created` "true". Wenn die Warteschlange bereits vorhanden ist, ist `result.created` "false".

### <a name="filters"></a>Filter
Mit **QueueService** können Sie optionale Filteroperationen auf Operationen anwenden. Filtervorgänge können Protokollierung, automatische Wiederholung usw. umfassen. Filter sind Objekte, die eine Methode mit der Signatur implementieren:

```javascript
function handle (requestOptions, next)
```

Nachdem die Vorverarbeitung der Anforderungsoptionen angeschlossen ist, muss die Methode "next" aufrufen und hierbei eine Rückruffunktion mit der folgenden Signatur übergeben:

```javascript
function (returnObject, finalCallback, next)
```

Nachdem das returnObject-Objekt (die Antwort auf die an den Server gesendete Anforderung) verarbeitet wurde, muss in dieser Rückruffunktion entweder "next" aufgerufen werden, wenn die Tabelle vorhanden ist, um weitere Filter zu verarbeiten, oder andernfalls einfach "finallCallback" aufrufen, um den Dienstaufruf zu beenden.

Zwei Filter, die eine Wiederholungslogik implementieren, sind im Azure SDK für Node.js enthalten: **ExponentialRetryPolicyFilter** und **LinearRetryPolicyFilter**. Mit folgendem Code wird ein **QueueService**-Objekt erstellt, das **ExponentialRetryPolicyFilter** verwendet:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var queueSvc = azure.createQueueService().withFilter(retryOperations);
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Anleitung: Einfügen einer Nachricht in eine Warteschlange
Zum Einfügen einer Nachricht in eine Warteschlange verwenden Sie die **createMessage**-Methode, um eine neue Nachricht zu erstellen. Fügen Sie sie dann der Warteschlange hinzu.

```javascript
queueSvc.createMessage('myqueue', "Hello world!", function(error, results, response){
  if(!error){
    // Message inserted
  }
});
```

## <a name="how-to-peek-at-the-next-message"></a>Anleitung: Einsehen der nächsten Nachricht
Sie können einen Blick auf die Nachricht am Anfang einer Warteschlange werfen, ohne sie aus der Warteschlange zu entfernen, indem Sie die Methode **peekMessages** aufrufen. Standardmäßig wird von **peekMessages** jeweils nur eine Nachricht angeschaut.

```javascript
queueSvc.peekMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
  }
});
```

Das `result` enthält die Nachricht.

> [!NOTE]
> Wenn Sie **peekMessages** verwenden und keine Nachrichten in der Warteschlange vorhanden sind, werden weder Fehler noch Nachrichten zurückgegeben.
> 
> 

## <a name="how-to-dequeue-the-next-message"></a>Anleitung: Entfernen der nächsten Nachricht aus der Warteschlange
Das Verarbeiten einer Nachricht besteht aus zwei Stufen:

1. Entfernen der Nachricht aus der Warteschlange.
2. Löschen der Nachricht.

Verwenden Sie **getMessages**, um eine Nachricht aus der Warteschlange zu entfernen. Durch diesen Vorgang werden die Nachrichten in der Warteschlange unsichtbar, sodass sie nicht durch andere Clients verarbeitet werden können. Sobald Ihre Anwendung eine Nachricht verarbeitet hat, rufen Sie **deleteMessage** auf, um sie aus der Warteschlange zu löschen. Im folgenden Beispiel wird eine Nachricht abgerufen und anschließend gelöscht:

```javascript
queueSvc.getMessages('myqueue', function(error, results, response){
  if(!error){
    // Message text is in results[0].messageText
    var message = results[0];
    queueSvc.deleteMessage('myqueue', message.messageId, message.popReceipt, function(error, response){
      if(!error){
        //message deleted
      }
    });
  }
});
```

> [!NOTE]
> Standardmäßig wird eine Nachricht nur für 30 Sekunden ausgeblendet, danach ist sie für andere Client sichtbar. Sie können einen anderen Wert angeben, indem Sie `options.visibilityTimeout` mit **getMessages**verwenden.
> 
> [!NOTE]
> Wenn Sie **getMessages** verwenden und keine Nachrichten in der Warteschlange vorhanden sind, werden weder Fehler noch Nachrichten zurückgegeben.
> 
> 

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Anleitung: Ändern des Inhalts von Nachrichten in der Warteschlange
Sie können die Inhalte einer Nachricht direkt in der Warteschlange mithilfe von **updateMessage**ändern. Im folgenden Beispiel wird der Text einer Nachricht aktualisiert:

```javascript
queueSvc.getMessages('myqueue', function(error, getResults, getResponse){
  if(!error){
    // Got the message
    var message = getResults[0];
    queueSvc.updateMessage('myqueue', message.messageId, message.popReceipt, 10, {messageText: 'new text'}, function(error, updateResults, updateResponse){
      if(!error){
        // Message updated successfully
      }
    });
  }
});
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Anleitung: Zusätzliche Optionen für das Entfernen von Nachrichten aus der Warteschlange
Es gibt zwei Möglichkeiten, wie Sie das Abrufen von Nachrichten aus der Warteschlange anpassen können:

* `options.numOfMessages` : Abrufen eines Stapels an Nachrichten (bis zu 32).
* `options.visibilityTimeout` : Festlegen eines längeren oder kürzeren Unsichtbarkeits-Zeitlimits.

Im folgenden Beispiel wird die Methode **getMessages** verwendet, um mit einem Aufruf 15 Nachrichten abzurufen. Anschließend wird jede Nachricht mithilfe einer for Schleife verarbeitet. Zudem wird das Unsichtbarkeits-Zeitlimit auf fünf Minuten für alle Nachrichten festgelegt, die durch diese Methode zurückgegeben werden.

```javascript
queueSvc.getMessages('myqueue', {numOfMessages: 15, visibilityTimeout: 5 * 60}, function(error, results, getResponse){
  if(!error){
    // Messages retrieved
    for(var index in result){
      // text is available in result[index].messageText
      var message = results[index];
      queueSvc.deleteMessage(queueName, message.messageId, message.popReceipt, function(error, deleteResponse){
        if(!error){
          // Message deleted
        }
      });
    }
  }
});
```

## <a name="how-to-get-the-queue-length"></a>Anleitung: Abrufen der Warteschlangenlänge
**getQueueMetadata** gibt Metadaten über die Warteschlange zurück, einschließlich der geschätzten Anzahl der in der Warteschlange wartenden Nachrichten.

```javascript
queueSvc.getQueueMetadata('myqueue', function(error, results, response){
  if(!error){
    // Queue length is available in results.approximateMessageCount
  }
});
```

## <a name="how-to-list-queues"></a>Anleitung: Auflisten von Warteschlangen
Verwenden Sie zum Aufrufen einer Liste an Warteschlangen **listQueuesSegmented**. Verwenden Sie **listQueuesSegmentedWithPrefix**, um eine Liste abzurufen, die nach einem bestimmten Präfix gefiltert ist.

```javascript
queueSvc.listQueuesSegmented(null, function(error, results, response){
  if(!error){
    // results.entries contains the list of queues
  }
});
```

Wenn nicht alle Warteschlangen zurückgegeben werden können, kann `result.continuationToken` als erster Parameter von **listQueuesSegmented** oder als zweiter Parameter von **listQueuesSegmentedWithPrefix** verwendet werden, um weitere Ergebnisse abzurufen.

## <a name="how-to-delete-a-queue"></a>Anleitung: Löschen einer Warteschlange
Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie die Methode **deleteQueue** für das Warteschlangenobjekt auf.

```javascript
queueSvc.deleteQueue(queueName, function(error, response){
  if(!error){
    // Queue has been deleted
  }
});
```

Verwenden Sie **clearMessages**, um alle Nachrichten aus einer Warteschlange zu entfernen, ohne sie zu löschen.

## <a name="how-to-work-with-shared-access-signatures"></a>Gewusst wie: Arbeiten mit Shared Access Signatures
Shared Access Signature (SAS) ist eine sichere Möglichkeit zum Bereitstellen einen granularen Zugriffs auf Warteschlangen, ohne dass dabei Ihr Speicherkontoname oder -schlüssel angegeben werden müssen. SAS wird oftmals zum Bereitstellen des beschränkten Zugriffs auf Ihre Warteschlangen verwendet, dazu zählt beispielsweise auch, einer mobilen App das Senden von Nachrichten zu erlauben.

Eine vertrauenswürdige Anwendung wie ein cloudbasierter Dienst generiert mittels **generateSharedAccessSignature** von **QueueService** ein SAS und stellt sie einer nicht vertrauenswürdigen oder teilweise vertrauenswürdigen Anwendung bereit. Zum Beispiel für eine mobile App. Die SAS wird mithilfe einer Richtlinie generiert, die das Anfangs- und das Enddatum der Gültigkeit der SAS sowie die Zugriffsstufe definiert, die dem Inhaber der SAS gewährt wird.

Im folgenden Beispiel wird eine neue Richtlinie für den geteilten Zugriff generiert, wodurch der SAS-Inhaber der Warteschlange Nachrichten hinzufügen kann, die 100 Minuten nach ihrer Erstellung abläuft.

```javascript
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};

var queueSAS = queueSvc.generateSharedAccessSignature('myqueue', sharedAccessPolicy);
var host = queueSvc.host;
```

Beachten Sie, dass auch die Hostinformationen angegeben werden müssen, da sie erforderlich sind, wenn der SAS-Inhaber versucht, auf die Warteschlange zuzugreifen.

Die Clientanwendung verwendet dann die SAS mit **QueueServiceWithSAS** , um Vorgänge für die Warteschlange auszuführen. Im folgenden Beispiel wird eine Verbindung zur Warteschlange hergestellt, und es wird eine Nachricht erstellt.

```javascript
var sharedQueueService = azure.createQueueServiceWithSas(host, queueSAS);
sharedQueueService.createMessage('myqueue', 'Hello world from SAS!', function(error, result, response){
  if(!error){
    //message added
  }
});
```

Da die SAS mit "add access" generiert wurde, würde beim Versuch, Nachrichten zu lesen, zu aktualisieren oder zu löschen, ein Fehler angezeigt werden.

### <a name="access-control-lists"></a>Zugriffssteuerungslisten
Sie können auch eine Zugriffssteuerungsliste (Access Control List, ACL) verwenden, um die Zugriffsrichtlinie für eine SAS festzulegen. Dies ist nützlich, wenn mehrere Clients auf die Warteschlange zugreifen sollen und für jeden Client unterschiedliche Zugriffsrichtlinien bereitgestellt werden.

Eine ACL wird in einem Array von Zugriffsrichtlinien implementiert, wobei jeder Richtlinie eine ID zugeordnet wird. Im folgenden Beispiel werden zwei Richtlinien definiert, eine für „user1“ und eine für „user2“:

```javascript
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.PROCESS,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.QueueUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

Im folgenden Beispiel wird die aktuelle Zugriffssteuerungsliste für **myqueue** abgerufen. Anschließend werden die neuen Richtlinien mithilfe von **setQueueAcl** hinzugefügt. Dieser Ansatz ermöglicht Folgendes:

```javascript
var extend = require('extend');
queueSvc.getQueueAcl('myqueue', function(error, result, response) {
  if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    queueSvc.setQueueAcl('myqueue', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

Nachdem die ACL festgelegt wurde, können Sie basierend auf der ID für eine Richtlinie eine SAS erstellen. Im folgenden Beispiel wird eine neue SAS für 'user2' erstellt:

```javascript
queueSAS = queueSvc.generateSharedAccessSignature('myqueue', { Id: 'user2' });
```

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie sich nun mit den Grundlagen des Warteschlangenspeichers vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

* Besuchen Sie den [Azure Storage-Teamblog][Azure Storage Team Blog].
* Besuchen Sie das [Azure Storage-SDK für Node][Azure Storage SDK for Node]-Repository auf GitHub.



[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node

[using the REST API]: https://msdn.microsoft.com/library/azure/hh264518.aspx

[Azure Portal]: https://portal.azure.com

[Erstellen einer Node.js-Web-App in Azure App Service](../../app-service/app-service-web-get-started-nodejs.md)

[Erstellen und Bereitstellen einer Node.js-Anwendung in einem Azure-Clouddienst](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/
