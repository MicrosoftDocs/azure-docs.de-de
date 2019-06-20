---
title: Verwenden von Azure Service Bus-Warteschlangen in Node.js | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Verwendung von Service Bus-Warteschlangen in Azure aus einer Node.js-App.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: a87a00f9-9aba-4c49-a0df-f900a8b67b3f
ms.service: service-bus-messaging
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: 1426b3d31159280ad9aac2dd240a5f083c40752d
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65988310"
---
# <a name="how-to-use-service-bus-queues-with-nodejs-and-the-azure-sb-package"></a>Verwenden von Service Bus-Warteschlangen mit Node.js und dem Paket „azure-sb“
> [!div class="op_multi_selector" title1="Programming language" title2="Node.js pacakge"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-queues.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-queues-new-package.md)

In diesem Tutorial erfahren Sie, wie Sie Node.js-Anwendungen erstellen, um Nachrichten mithilfe des Pakets [azure-sb](https://www.npmjs.com/package/azure-sb) an eine Service Bus-Warteschlange zu senden und Antworten zu empfangen. Die Beispiele sind in JavaScript geschrieben und verwenden das [Azure-Modul](https://www.npmjs.com/package/azure) Node.js, das intern das `azure-sb`-Paket verwendet.

Das [azure-sb](https://www.npmjs.com/package/azure-sb)-Paket verwendet [Service Bus-REST-Laufzeit-APIs](/rest/api/servicebus/service-bus-runtime-rest). Sie können die Funktionalität beschleunigen, wenn Sie das neue [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus)-Paket verwenden, das das schnellere Protokoll [AMQP 1.0](service-bus-amqp-overview.md) nutzt. Weitere Informationen zu diesem neuen Paket finden Sie unter [How to use Service Bus queues with Node.js and @azure/service-bus package (Verwenden von Service Bus-Warteschlangen mit Node.js und dem Paket „@azure/service-bus“)](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-nodejs-how-to-use-queues-new-package), oder Sie lesen hier weiter, um zu erfahren, wie das [azure](https://www.npmjs.com/package/azure)-Paket verwendet wird.

## <a name="prerequisites"></a>Voraussetzungen
- Ein Azure-Abonnement. Um dieses Tutorial abzuschließen, benötigen Sie ein Azure-Konto. Sie können Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF) registrieren.
- Wenn Sie über keine Warteschlange verfügen, führen Sie die Schritte im Artikel [Schnellstart: Erstellen einer Service Bus-Warteschlange mithilfe des Azure-Portals](service-bus-quickstart-portal.md) aus, um eine Warteschlange zu erstellen.
    1. Lesen Sie die kurze **Übersicht** über Service Bus-**Warteschlangen**. 
    2. Erstellen Sie einen Service Bus-**Namespace**. 
    3. Rufen Sie die **Verbindungszeichenfolge** ab. 

        > [!NOTE]
        > In diesem Tutorial erstellen Sie eine **Warteschlange** im Service Bus-Namespace mithilfe von Node.js. 
 

## <a name="create-a-nodejs-application"></a>Erstellen einer Node.js-Anwendung
Erstellen Sie eine leere Node.js-Anwendung. Anweisungen zum Erstellen von Node.js-Anwendungen finden Sie unter [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website][Create and deploy a Node.js application to an Azure Website] und [Node.js-Clouddienst][Node.js Cloud Service] (mithilfe von Windows PowerShell).

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus
Damit Azure Service Bus verwendet werden kann, laden Sie das Node.js-Azure-Paket herunter und verwenden es. Dieses Paket enthält eine Reihe von Bibliotheken, die mit den Service Bus-REST-Diensten kommunizieren.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Verwenden von Node Package Manager (NPM) zum Beziehen des Pakets
1. Verwenden Sie das Befehlsfenster von **Windows PowerShell für Node.js**, um zum Ordner **c:\\node\\sbqueues\\WebRole1** zu navigieren, in dem Sie Ihre Beispielanwendung erstellt haben.
2. Geben Sie **npm install azure** im Befehlsfenster ein. Die Ausgabe sollte in etwa wie folgt aussehen:

    ```
    azure@0.7.5 node_modules\azure
        ├── dateformat@1.0.2-1.2.3
        ├── xmlbuilder@0.4.2
        ├── node-uuid@1.2.0
        ├── mime@1.2.9
        ├── underscore@1.4.4
        ├── validator@1.1.1
        ├── tunnel@0.0.2
        ├── wns@0.5.3
        ├── xml2js@0.2.7 (sax@0.5.2)
        └── request@2.21.0 (json-stringify-safe@4.0.0, forever-agent@0.5.0, aws-sign@0.3.0, tunnel-agent@0.3.0, oauth-sign@0.3.0, qs@0.6.5, cookie-jar@0.3.0, node-uuid@1.4.0, http-signature@0.9.11, form-data@0.0.8, hawk@0.13.1)
    ```
3. Sie können den Befehl **ls** manuell ausführen, um zu überprüfen, ob der Ordner **node_modules** erstellt wurde. In diesem Ordner finden Sie das **azure**-Paket, das die für den Zugriff auf Service Bus-Warteschlangen benötigten Bibliotheken enthält.

### <a name="import-the-module"></a>Importieren des Moduls
Verwenden Sie Editor oder einen anderen Texteditor, um die folgende Zeile am Anfang der Datei **server.js** der Anwendung einzufügen:

```javascript
var azure = require('azure');
```

### <a name="set-up-an-azure-service-bus-connection"></a>Einrichten einer Azure Service Bus-Verbindung
Das Azure-Modul liest die Umgebungsvariable `AZURE_SERVICEBUS_CONNECTION_STRING`, um Informationen zu erhalten, die für eine Verbindung mit Service Bus benötigt werden. Wenn diese Umgebungsvariable nicht festgelegt wurde, müssen Sie beim Aufrufen von `createServiceBusService` die Kontoinformationen angeben.

Ein Beispiel zum Festlegen der Umgebungsvariablen im [Azure-Portal][Azure portal] für eine Azure-Website finden Sie unter [Node.js-Webanwendung mit Storage][Node.js Web Application with Storage].

## <a name="create-a-queue"></a>Erstellen einer Warteschlange
Das **ServiceBusService**-Objekt ermöglicht Ihnen, mit Service Bus-Warteschlangen zu arbeiten. Der folgende Code erstellt ein **ServiceBusService**-Objekt. Fügen Sie ihn am Anfang der Datei **server.js** hinzu, nach der Anweisung zum Importieren des Azure-Moduls:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Durch Aufrufen von `createQueueIfNotExists` für das Objekt **ServiceBusService** wird die angegebene Warteschlange zurückgegeben (sofern vorhanden), oder es wird eine neue Warteschlange mit dem angegebenen Namen erstellt. Der folgende Code verwendet `createQueueIfNotExists`, um die Warteschlange `myqueue` zu erstellen oder eine Verbindung mit dieser Warteschlange herzustellen:

```javascript
serviceBusService.createQueueIfNotExists('myqueue', function(error){
    if(!error){
        // Queue exists
    }
});
```

Die Methode `createServiceBusService` unterstützt zudem weitere Optionen, mit denen Sie Standardeinstellungen für die Warteschlange überschreiben können. Hierzu zählen beispielsweise die Gültigkeitsdauer von Nachrichten und die maximale Warteschlangengröße. Das folgende Beispiel legt die maximale Warteschlangengröße auf 5 GB bei einer Gültigkeitsdauer (Time-to-live, TTL) von 1 Minute fest:

```javascript
var queueOptions = {
      MaxSizeInMegabytes: '5120',
      DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createQueueIfNotExists('myqueue', queueOptions, function(error){
    if(!error){
        // Queue exists
    }
});
```

### <a name="filters"></a>Filter
Mit **ServiceBusService** können Sie optionale Filteroperationen auf Operationen ausführen. Filtervorgänge können Protokollierung, automatische Wiederholung usw. umfassen. Filter sind Objekte, die eine Methode mit der folgenden Signatur implementieren:

```javascript
function handle (requestOptions, next)
```

Nachdem die Vorverarbeitung der Anforderungsoptionen abgeschlossen ist, muss die Methode `next` aufrufen und hierbei eine Rückruffunktion mit der folgenden Signatur übergeben:

```javascript
function (returnObject, finalCallback, next)
```

Nach der Verarbeitung von `returnObject` (die Antwort auf die an den Server gesendete Anforderung) muss der Rückruf entweder `next` aufrufen (sofern vorhanden), um weitere Filter zu verarbeiten, oder er muss `finalCallback` aufrufen, um den Dienstaufruf zu beenden.

Das Azure SDK für Node.js enthält zwei Filter zur Implementierung von Wiederholungslogik: `ExponentialRetryPolicyFilter` und `LinearRetryPolicyFilter`. Der folgende Code erstellt ein Objekt vom Typ `ServiceBusService`, das `ExponentialRetryPolicyFilter` verwendet:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="send-messages-to-a-queue"></a>Senden von Nachrichten an eine Warteschlange
Um eine Nachricht an eine Service Bus-Warteschlange zu senden, ruft Ihre Anwendung die Methode `sendQueueMessage` für das Objekt **ServiceBusService** auf. Nachrichten, die an die Service Bus-Warteschlangen gesendet werden (und von diesen empfangen werden), sind **BrokeredMessage**-Objekte und verfügen über einen Satz von Standardeigenschaften (z.B. **Label** und **TimeToLive**), ein Wörterbuch, in dem benutzerdefinierte anwendungsspezifische Eigenschaften enthalten sind, sowie einen Bestand beliebiger Anwendungsdaten. Eine Anwendung kann den Text der Nachricht festlegen, indem eine Zeichenfolge als Nachricht übergeben wird. Alle erforderlichen Standardeigenschaften werden mit Standardwerten aufgefüllt.

Das folgende Beispiel zeigt, wie mithilfe von `sendQueueMessage` eine Testnachricht an die Warteschlange `myqueue` gesendet wird:

```javascript
var message = {
    body: 'Test message',
    customProperties: {
        testproperty: 'TestValue'
    }};
serviceBusService.sendQueueMessage('myqueue', message, function(error){
    if(!error){
        // message sent
    }
});
```

Service Bus-Warteschlangen unterstützen eine maximale Nachrichtengröße von 256 KB für den [Standard-Tarif](service-bus-premium-messaging.md) und 1 MB für den [Premium-Tarif](service-bus-premium-messaging.md). Der Header, der die standardmäßigen und benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 KB haben. Bei der Anzahl der Nachrichten, die in einer Warteschlange enthalten sein können, besteht keine Beschränkung. Allerdings gilt eine Obergrenze bei der Gesamtgröße der in einer Warteschlange enthaltenen Nachrichten. Die Warteschlangengröße wird bei der Erstellung definiert. Die Obergrenze beträgt 5 GB. Weitere Informationen zu Kontingenten finden Sie unter [Service Bus-Kontingente][Service Bus quotas].

## <a name="receive-messages-from-a-queue"></a>Empfangen von Nachrichten aus einer Warteschlange
Zum Empfangen von Nachrichten aus einer Warteschlange wird die Methode `receiveQueueMessage` für das Objekt **ServiceBusService** verwendet. Nachrichten werden standardmäßig aus der Warteschlange gelöscht, wenn sie gelesen wurden. Sie können jedoch Nachrichten lesen (sprich: einen kurzen Blick darauf werfen) und sperren, ohne sie aus der Warteschlange zu löschen, indem Sie den optionalen Parameter `isPeekLock` auf **true** festlegen.

Das Standardverhalten für das Lesen und Löschen der Nachricht im Rahmen des Empfangsvorgangs ist das einfachste Modell. Es wird am besten für Szenarios eingesetzt, bei denen eine Anwendung tolerieren kann, wenn eine Nachricht bei einem Fehler nicht verarbeitet wird. Um dieses Verfahren zu verstehen, stellen Sie sich ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wird. Da Service Bus die Nachricht als verarbeitet markiert hat, wird er die Nachricht auslassen, die vor dem Absturz verarbeitet wurde, wenn die Anwendung neu startet und erneut mit der Verarbeitung von Nachrichten beginnt.

Wenn der Parameter `isPeekLock` auf **true** festgelegt ist, wird der Empfangsvorgang zu einem zweistufigen Vorgang. Dadurch können Anwendungen ohne Toleranz für die Auslassung von Nachrichten unterstützt werden. Wenn Service Bus eine Anfrage erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie dann zurück an die Anwendung. Nachdem die Anwendung die Verarbeitung der Nachricht abgeschlossen hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt sie die zweite Phase des Empfangsprozesses durch: In dieser Phase wird die Methode `deleteMessage` aufgerufen und die zu löschende Nachricht als Parameter angegeben. Die Methode `deleteMessage` markiert die Nachricht als verarbeitet und entfernt sie aus der Warteschlange.

Das folgende Beispiel zeigt, wie Nachrichten mit `receiveQueueMessage` empfangen und verarbeitet werden können. In dem Beispiel wird zunächst eine Nachricht empfangen und gelöscht. Anschließend wird eine Nachricht empfangen, während `isPeekLock` auf **true** festgelegt ist, und die Nachricht anschließend mithilfe von `deleteMessage` gelöscht:

```javascript
serviceBusService.receiveQueueMessage('myqueue', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
    }
});
serviceBusService.receiveQueueMessage('myqueue', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
            }
        });
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Behandeln von Anwendungsabstürzen und nicht lesbaren Nachrichten
Service Bus stellt Funktionen zur Verfügung, die Sie bei der ordnungsgemäßen Behandlung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht unterstützen. Wenn eine Empfängeranwendung die Nachricht aus einem bestimmten Grund nicht verarbeiten kann, kann sie die Methode `unlockMessage` für das Objekt **ServiceBusService** aufrufen. Dies führt dazu, dass Service Bus die Nachricht innerhalb der Warteschlange entsperrt und verfügbar macht, damit sie erneut empfangen werden kann, und zwar entweder durch dieselbe verarbeitende Anwendung oder durch eine andere verarbeitende Anwendung.

Zudem wird einer in der Warteschlange gesperrten Anwendung ein Zeitlimit zugeordnet. Wenn die Anwendung die Nachricht vor Ablauf des Sperrzeitlimits nicht verarbeiten kann (z. B. wenn die Anwendung abstürzt), entsperrt Service Bus die Nachricht automatisch und macht sie verfügbar, um erneut empfangen zu werden.

Falls die Anwendung nach der Verarbeitung der Nachricht, aber vor dem Aufruf der Methode `deleteMessage` abstürzt, wird die Nachricht wieder an die Anwendung zugestellt, wenn diese neu gestartet wird. Dieser Ansatz wird häufig als *At Least Once Processing* bezeichnet und bedeutet, dass jede Nachricht mindestens einmal verarbeitet wird, wobei dieselbe Nachricht in bestimmten Situationen möglicherweise erneut zugestellt wird. Wenn eine doppelte Verarbeitung im betreffenden Szenario nicht geeignet ist, sollten Anwendungsentwickler ihrer Anwendung zusätzliche Logik für den Umgang mit der Übermittlung doppelter Nachrichten hinzufügen. Dies wird häufig durch die Verwendung der **MessageId**-Eigenschaft der Nachricht erzielt, die über mehrere Zustellungsversuche hinweg konstant bleibt.

> [!NOTE]
> Sie können Service Bus-Ressourcen mit dem [Service Bus-Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/) verwalten. Mit dem Service Bus-Explorer können Benutzer eine Verbindung mit einem Service Bus-Namespace herstellen und Messagingentitäten auf einfache Weise verwalten. Das Tool stellt erweiterte Features wie Import-/Exportfunktionen oder Testmöglichkeiten für Themen, Warteschlangen, Abonnements, Relaydienste, Notification Hubs und Event Hubs zur Verfügung. 

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Warteschlangen finden Sie in den folgenden Ressourcen:

* [Warteschlangen, Themen und Abonnements][Queues, topics, and subscriptions]
* [Azure SDK für Node][Azure SDK for Node]-Repository auf GitHub
* [Node.js Developer Center (in englischer Sprache)](https://azure.microsoft.com/develop/nodejs/)

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com

[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Create and deploy a Node.js application to an Azure Website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Web Application with Storage]:../cosmos-db/table-storage-how-to-use-nodejs.md
[Service Bus quotas]: service-bus-quotas.md
