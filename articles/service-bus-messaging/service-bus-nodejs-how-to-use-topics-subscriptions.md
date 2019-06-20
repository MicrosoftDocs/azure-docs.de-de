---
title: Verwenden von Azure Service Bus-Themen und -Abonnements mit Node.js | Microsoft Docs
description: Erfahren Sie mehr zur Verwendung von Service Bus-Themen und -Abonnements in Azure aus einer Node.js-App.
services: service-bus-messaging
documentationcenter: nodejs
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b9f5db85-7b6c-4cc7-bd2c-bd3087c99875
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 04/15/2019
ms.author: aschhab
ms.openlocfilehash: 3b805a80330dd44ac4a65db88950393d3d4d60b7
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65992094"
---
# <a name="how-to-use-service-bus-topics-and-subscriptions-with-nodejs-and-the-azure-sb-package"></a>Verwenden von Service Bus-Themen und -Abonnements mit Node.js und dem azure-sb-Paket
> [!div class="op_multi_selector" title1="Programming language" title2="Node.js pacakge"]
> - [(Node.js | azure-sb)](service-bus-nodejs-how-to-use-topics-subscriptions.md)
> - [(Node.js | @azure/service-bus)](service-bus-nodejs-how-to-use-topics-subscriptions-new-package.md)

In diesem Tutorial erfahren Sie, wie Sie Node.js-Anwendungen erstellen, um Nachrichten mithilfe des Pakets [azure-sb](https://www.npmjs.com/package/azure-sb) an ein Service Bus-Thema zu senden sowie Nachrichten von einem Service Bus-Abonnement zu empfangen. Die Beispiele sind in JavaScript geschrieben und verwenden das [Azure-Modul](https://www.npmjs.com/package/azure) von Node.js, das intern das `azure-sb`-Paket verwendet.

Das [azure-sb](https://www.npmjs.com/package/azure-sb)-Paket verwendet [Service Bus-REST-Laufzeit-APIs](/rest/api/servicebus/service-bus-runtime-rest). Sie können die Funktionalität beschleunigen, wenn Sie das neue [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus)-Paket verwenden, das das schnellere Protokoll [AMQP 1.0](service-bus-amqp-overview.md) nutzt. Weitere Informationen zu diesem neuen Paket finden Sie unter [Verwenden von Service Bus-Themen und -Abonnements mit Node.js und dem @azure/service-bus-Paket](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-nodejs-how-to-use-topics-subscriptions-new-package), oder lesen Sie hier weiter, um zu erfahren, wie das [azure](https://www.npmjs.com/package/azure)-Paket verwendet wird.

Folgende Szenarien werden hier behandelt:

- Erstellen von Themen und Abonnements 
- Erstellen von Abonnementfiltern 
- Senden von Nachrichten an ein Thema 
- Empfangen von Nachrichten aus einem Abonnement
- Löschen von Themen und Abonnements 

Weitere Informationen zu Themen und Abonnements finden Sie im Abschnitt [Nächste Schritte](#next-steps).

## <a name="prerequisites"></a>Voraussetzungen
- Ein Azure-Abonnement. Um dieses Tutorial abzuschließen, benötigen Sie ein Azure-Konto. Sie können [Ihre Visual Studio-oder MSDN-Abonnentenvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) oder [sich für ein kostenloses Konto anmelden](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Befolgen Sie die Schritte im [Schnellstart: Verwenden Sie das Azure-Portal, um ein Service Bus-Thema und -Abonnements für das Thema](service-bus-quickstart-topics-subscriptions-portal.md) zu erstellen, um einen Service Bus-**Namespace** zu erstellen und die **Verbindungszeichenfolge** abzurufen.

    > [!NOTE]
    > Sie erstellen ein **Thema** und ein **Abonnement** für das Thema, indem Sie **Node.js** in dieser Schnellstartanleitung verwenden. 

## <a name="create-a-nodejs-application"></a>Erstellen einer Node.js-Anwendung
Erstellen Sie eine leere Node.js-Anwendung. Anweisungen zum Erstellen von Node.js-Anwendungen finden Sie unter [Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website] zur Verwendung des [Node.js-Clouddiensts][Node.js Cloud Service] mit Windows PowerShell oder einer Website mit WebMatrix.

## <a name="configure-your-application-to-use-service-bus"></a>Konfigurieren Ihrer Anwendung für die Verwendung von Service Bus
Um Service Bus zu verwenden, laden Sie das Node.js-Azure-Paket herunter. Dieses Paket enthält eine Reihe von Bibliotheken, die mit den Service Bus-REST-Diensten kommunizieren.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Verwenden von Node Package Manager (NPM) zum Beziehen des Pakets
1. Öffnen Sie eine Befehlszeilenschnittstelle wie **PowerShell** (Windows), **Terminal** (Mac) oder **Bash** (Unix).
2. Navigieren Sie zu dem Ordner, in dem Sie Ihre Beispielanwendung erstellt haben.
3. Geben Sie **npm install azure** im Befehlsfenster ein. Die Ausgabe dieses Befehls sollte wie folgt aussehen:

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
3. Sie können den Befehl **ls** manuell ausführen, um zu überprüfen, ob der Ordner **node\_modules** erstellt wurde. In diesem Ordner finden Sie das Paket **azure**, das die für den Zugriff auf Service Bus-Themen benötigten Bibliotheken enthält.

### <a name="import-the-module"></a>Importieren des Moduls
Verwenden Sie Editor oder einen anderen Text-Editor, um die folgende Zeile am Anfang der Datei **server.js** der Anwendung einzufügen:

```javascript
var azure = require('azure');
```

### <a name="set-up-a-service-bus-connection"></a>Einrichten einer Service Bus-Verbindung
Das Azure-Modul liest die Umgebungsvariable `AZURE_SERVICEBUS_CONNECTION_STRING` für die Verbindungszeichenfolge, die Sie weiter oben im Schritt „Abrufen der Anmeldeinformationen“ abgerufen haben. Wenn diese Umgebungsvariable nicht festgelegt wurde, müssen Sie beim Aufrufen von `createServiceBusService` die Kontoinformationen angeben.

Ein Beispiel zum Festlegen der Umgebungsvariablen für einen Azure Cloud Service finden Sie unter [Festlegen von Umgebungsvariablen](../container-instances/container-instances-environment-variables.md#azure-cli-example).



## <a name="create-a-topic"></a>Erstellen eines Themas
Das **ServiceBusService**-Objekt ermöglicht es Ihnen, mit Themen zu arbeiten. Der folgende Code erstellt ein **ServiceBusService**-Objekt. Fügen Sie ihn am Anfang der Datei **server.js** hinzu, nach der Anweisung zum Importieren des Azure-Moduls:

```javascript
var serviceBusService = azure.createServiceBusService();
```

Wenn Sie `createTopicIfNotExists` für das Objekt **ServiceBusService** aufrufen, wird das angegebene Thema zurückgegeben (sofern vorhanden), oder es wird ein neues Thema mit dem angegebenen Namen erstellt. Der folgende Code verwendet `createTopicIfNotExists`, um das Thema `MyTopic` zu erstellen oder eine Verbindung mit diesem Thema herzustellen:

```javascript
serviceBusService.createTopicIfNotExists('MyTopic',function(error){
    if(!error){
        // Topic was created or exists
        console.log('topic created or exists.');
    }
});
```

Die Methode `createTopicIfNotExists` unterstützt zudem weitere Optionen, mit denen Sie Standardeinstellungen für Themen überschreiben können. Hierzu zählen beispielsweise die Gültigkeitsdauer von Nachrichten und die maximale Themengröße. 

Das folgende Beispiel legt die maximale Themengröße auf 5 GB bei einer Gültigkeitsdauer (TTL) von 1 Minute fest:

```javascript
var topicOptions = {
        MaxSizeInMegabytes: '5120',
        DefaultMessageTimeToLive: 'PT1M'
    };

serviceBusService.createTopicIfNotExists('MyTopic', topicOptions, function(error){
    if(!error){
        // topic was created or exists
    }
});
```

### <a name="filters"></a>Filter
Mit **ServiceBusService** können Sie optionale Filteroperationen auf Operationen ausführen. Filtervorgänge können Protokollierung, automatische Wiederholung usw. umfassen. Filter sind Objekte, die eine Methode mit der Signatur implementieren:

```javascript
function handle (requestOptions, next)
```

Nachdem die Vorverarbeitung der Anforderungsoptionen abgeschlossen ist, ruft die Methode `next` auf und übergibt eine Rückruffunktion mit der folgenden Signatur:

```javascript
function (returnObject, finalCallback, next)
```

Nach der Verarbeitung von `returnObject` (die Antwort auf die an den Server gesendete Anforderung) muss der Rückruf „next“ aufrufen (sofern vorhanden), um weitere Filter zu verarbeiten. Andernfalls muss er `finalCallback` aufrufen, um den Dienstaufruf zu beenden.

Zwei Filter, die eine Wiederholungslogik implementieren, sind im Azure SDK für Node.js enthalten: **ExponentialRetryPolicyFilter** und **LinearRetryPolicyFilter**. Der folgende Code erstellt ein **ServiceBusService**-Objekt, das den **ExponentialRetryPolicyFilter** verwendet:

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var serviceBusService = azure.createServiceBusService().withFilter(retryOperations);
```

## <a name="create-subscriptions"></a>Erstellen von Abonnements
Themenabonnements werden ebenfalls mit dem **ServiceBusService**-Objekt erstellt. Abonnements werden benannt und können einen optionalen Filter aufweisen, der die Nachrichten einschränkt, die an die virtuelle Warteschlange des Abonnements übermittelt werden.

> [!NOTE]
> Abonnements sind persistent, bis sie selbst oder die ihnen zugeordneten Themen gelöscht werden. Wenn Ihre Anwendung Logik zum Erstellen eines Abonnements beinhaltet, muss sie zuerst mithilfe der Methode `getSubscription` überprüfen, ob das Abonnement vorhanden ist.
>
>

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Erstellen eines Abonnements mit dem Standardfilter (MatchAll)
Der Filter **MatchAll** ist der Standardfilter, der bei der Erstellung eines Abonnements verwendet wird. Wenn Sie den Filter **MatchAll** verwenden, werden alle für das Thema veröffentlichten Nachrichten in die virtuelle Warteschlange des Abonnements eingereiht. Mit dem folgenden Beispiel wird ein Abonnement namens „AllMessages“ erstellt, für das der Standardfilter **MatchAll** verwendet wird.

```javascript
serviceBusService.createSubscription('MyTopic','AllMessages',function(error){
    if(!error){
        // subscription created
    }
});
```

### <a name="create-subscriptions-with-filters"></a>Erstellen von Abonnements mit Filtern
Sie können auch Filter erstellen, durch die Sie angeben können, welche an ein Thema gesendeten Nachrichten in einem bestimmten Themenabonnement angezeigt werden sollen.

Der von Abonnements unterstützte flexibelste Filtertyp ist **SqlFilter**, der eine Teilmenge von SQL92 implementiert. SQL-Filter werden auf die Eigenschaften der Nachrichten angewendet, die für das Thema veröffentlicht werden. Weitere Informationen zu den Ausdrücken, die mit einem SQL-Filter verwendet werden können, finden Sie in der Syntax von [SqlFilter.SqlExpression][SqlFilter.SqlExpression].

Filter können einem Abonnement mithilfe der Methode `createRule` des Objekts **ServiceBusService** hinzugefügt werden. Durch diese Methode können Sie neue Filter zu einem vorhandenen Abonnement hinzufügen.

> [!NOTE]
> Da der Standardfilter automatisch auf alle neuen Abonnements angewendet wird, müssen Sie zuerst den Standardfilter entfernen. Andernfalls überschreibt **MatchAll** alle Filter, die Sie angeben. Die Standardregel kann mithilfe der Methode `deleteRule` des Objekts **ServiceBusService** entfernt werden.
>
>

Im folgenden Beispiel wird ein Abonnement namens `HighMessages` mit einem Filter vom Typ **SqlFilter** erstellt, der nur Nachrichten auswählt, deren benutzerdefinierte Eigenschaft `messagenumber` größer „3“ ist:

```javascript
serviceBusService.createSubscription('MyTopic', 'HighMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'HighMessages',
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME,
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber > 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic',
            'HighMessages',
            'HighMessageFilter',
            ruleOptions,
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Analog dazu erstellt das folgende Beispiel ein Abonnement namens `LowMessages` mit einem Filter vom Typ **SqlFilter**, der nur Nachrichten auswählt, deren benutzerdefinierte Eigenschaft `messagenumber` kleiner oder gleich „3“ ist:

```javascript
serviceBusService.createSubscription('MyTopic', 'LowMessages', function (error){
    if(!error){
        // subscription created
        rule.create();
    }
});
var rule={
    deleteDefault: function(){
        serviceBusService.deleteRule('MyTopic',
            'LowMessages',
            azure.Constants.ServiceBusConstants.DEFAULT_RULE_NAME,
            rule.handleError);
    },
    create: function(){
        var ruleOptions = {
            sqlExpressionFilter: 'messagenumber <= 3'
        };
        rule.deleteDefault();
        serviceBusService.createRule('MyTopic',
            'LowMessages',
            'LowMessageFilter',
            ruleOptions,
            rule.handleError);
    },
    handleError: function(error){
        if(error){
            console.log(error)
        }
    }
}
```

Wenn jetzt eine Nachricht an `MyTopic` gesendet wird, erfolgt die Zustellung an Empfänger, die das `AllMessages`-Themenabonnement abonniert haben. Sie wird selektiv an die Empfänger der Themenabonnements `HighMessages` und `LowMessages` zugestellt (je nach Inhalt der Nachricht).

## <a name="how-to-send-messages-to-a-topic"></a>Senden von Nachrichten an ein Thema
Um eine Nachricht an ein Service Bus-Thema zu senden, muss die Anwendung die Methode `sendTopicMessage` des Objekts **ServiceBusService** verwenden.
Nachrichten, die an Service Bus-Themen gesendet werden, sind **BrokeredMessage**-Objekte.
Objekte vom Typ **BrokeredMessage** besitzen eine Reihe von Standardeigenschaften (wie etwa `Label` und `TimeToLive`), ein Wörterbuch zur Speicherung benutzerdefinierter, anwendungsspezifischer Eigenschaften und einen aus Zeichenfolgendaten bestehenden Nachrichtentext. Eine Anwendung kann den Nachrichtentext festlegen, indem sie einen Zeichenfolgenwert an `sendTopicMessage` übergibt. Erforderliche Standardeigenschaften werden mit Standardwerten gefüllt.

Das folgende Beispiel zeigt, wie Sie fünf Testnachrichten an `MyTopic` senden. Der Eigenschaftswert `messagenumber` jeder Nachricht variiert gemäß der Iteration der Schleife. (Diese Eigenschaft bestimmt, welche Abonnements die Nachricht erhalten.)

```javascript
var message = {
    body: '',
    customProperties: {
        messagenumber: 0
    }
}

for (i = 0;i < 5;i++) {
    message.customProperties.messagenumber=i;
    message.body='This is Message #'+i;
    serviceBusService.sendTopicMessage(topic, message, function(error) {
      if (error) {
        console.log(error);
      }
    });
}
```

Service Bus-Themen unterstützen eine maximale Nachrichtengröße von 256 KB im [Standard-Tarif](service-bus-premium-messaging.md) und 1 MB im [Premium-Tarif](service-bus-premium-messaging.md). Der Header, der die standardmäßigen und benutzerdefinierten Anwendungseigenschaften enthält, kann eine maximale Größe von 64 KB haben. Es gibt keine Beschränkung für die Anzahl der Nachrichten, die ein Thema enthält. Es gibt jedoch eine Grenze für die Gesamtgröße der Nachrichten eines Themas. Die Themengröße wird bei der Erstellung definiert. Die Obergrenze beträgt 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Empfangen von Nachrichten aus einem Abonnement
Nachrichten werden von einem Abonnement über die Methode `receiveSubscriptionMessage` des Objekts **ServiceBusService** empfangen. Nachrichten werden nach dem Lesen standardmäßig aus dem Abonnement gelöscht. Sie können jedoch den optionalen Parameter `isPeekLock` auf **true** festlegen, um die Nachricht zu lesen (einen kurzen Blick darauf zu werfen) und zu sperren, ohne sie aus dem Abonnement zu löschen.

Das Standardverhalten für das Lesen und Löschen der Nachricht im Rahmen des Empfangsvorgangs ist das einfachste Modell. Es wird am besten für Szenarios eingesetzt, bei denen eine Anwendung tolerieren kann, wenn eine Nachricht bei einem Fehler nicht verarbeitet wird. Um dieses Verfahren zu verstehen, stellen Sie sich ein Szenario vor, in dem der Consumer die Empfangsanforderung ausstellt und dann abstürzt, bevor diese verarbeitet wird. Da die Nachricht von Service Bus als verarbeitet gekennzeichnet wurde, wird die vor dem Absturz verarbeitete Nachricht nicht berücksichtigt, wenn die Anwendung neu gestartet und die Verarbeitung von Nachrichten wieder aufgenommen wird.

Wenn der Parameter `isPeekLock` auf **true** festgelegt ist, wird der Empfangsvorgang zu einem zweistufigen Vorgang. Dadurch können Anwendungen ohne Toleranz für fehlende Nachrichten unterstützt werden. Wenn Service Bus eine Anforderung erhält, ermittelt der Dienst die nächste zu verarbeitende Nachricht, sperrt diese, um zu verhindern, dass andere Consumer sie erhalten, und sendet sie an die Anwendung zurück.
Nachdem die Anwendung die Nachricht verarbeitet hat (oder sie zwecks zukünftiger Verarbeitung zuverlässig gespeichert hat), führt sie die zweite Phase des Empfangsprozesses durch Aufruf der Methode **deleteMessage** durch, und stellt die Nachricht bereit, die als Parameter gelöscht werden soll. Die Methode **deleteMessage** markiert die Nachricht als verarbeitet und entfernt sie aus dem Abonnement.

Das folgende Beispiel zeigt, wie Nachrichten mit `receiveSubscriptionMessage` empfangen und verarbeitet werden können. In diesem Beispiel wird zuerst eine Nachricht des Abonnements „LowMessages“ empfangen und gelöscht und anschließend eine Nachricht des Abonnements „HighMessages“ empfangen, wobei `isPeekLock` auf „true“ festgelegt ist. Anschließend wird die Nachricht mithilfe von `deleteMessage` gelöscht:

```javascript
serviceBusService.receiveSubscriptionMessage('MyTopic', 'LowMessages', function(error, receivedMessage){
    if(!error){
        // Message received and deleted
        console.log(receivedMessage);
    }
});
serviceBusService.receiveSubscriptionMessage('MyTopic', 'HighMessages', { isPeekLock: true }, function(error, lockedMessage){
    if(!error){
        // Message received and locked
        console.log(lockedMessage);
        serviceBusService.deleteMessage(lockedMessage, function (deleteError){
            if(!deleteError){
                // Message deleted
                console.log('message has been deleted.');
            }
        })
    }
});
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Behandeln von Anwendungsabstürzen und nicht lesbaren Nachrichten
Service Bus stellt Funktionen zur Verfügung, die Sie bei der ordnungsgemäßen Behandlung von Fehlern in der Anwendung oder bei Problemen beim Verarbeiten einer Nachricht unterstützen. Wenn eine Empfängeranwendung die Nachricht aus einem bestimmten Grund nicht verarbeiten kann, kann sie die Methode `unlockMessage` für das Objekt **ServiceBusService** aufrufen. Diese Methode führt dazu, dass Service Bus die Nachricht innerhalb des Abonnements entsperrt und verfügbar macht, damit sie erneut empfangen werden kann, in diesem Fall entweder durch dieselbe verarbeitende Anwendung oder durch eine andere verarbeitende Anwendung.

Einer innerhalb des Abonnements gesperrten Nachricht ist auch ein Zeitlimit zugeordnet. Wenn die Anwendung die Nachricht nicht verarbeiten kann, bevor das Zeitlimit der Sperre abläuft (z.B. wenn die Anwendung abstürzt), entsperrt Service Bus die Nachricht automatisch und macht sie verfügbar, sodass sie wieder empfangen werden kann.

Falls die Anwendung nach der Verarbeitung der Nachricht, aber vor dem Aufruf der Methode `deleteMessage` abstürzt, wird die Nachricht wieder an die Anwendung zugestellt, wenn diese neu gestartet wird. Dies wird häufig als *At-Least-Once Processing* (Mindestens einmalige Verarbeitung) bezeichnet und bedeutet, dass jede Nachricht mindestens einmal verarbeitet wird, wobei eine Nachricht in bestimmten Situationen unter Umständen erneut übermittelt wird. Wenn eine doppelte Verarbeitung im betreffenden Szenario nicht geeignet ist, sollten Sie Ihrer Anwendung zusätzliche Logik für den Umgang mit der Übermittlung doppelter Nachrichten hinzufügen. Dazu können Sie die Eigenschaft **MessageId** der Nachricht verwenden, die über mehrere Zustellungsversuche hinweg konstant bleibt.

## <a name="delete-topics-and-subscriptions"></a>Löschen von Themen und Abonnements
Themen und Abonnements sind persistent und müssen über das [Azure-Portal][Azure portal] oder programmgesteuert explizit gelöscht werden.
Das folgende Beispiel zeigt das Löschen des Themas `MyTopic`:

```javascript
serviceBusService.deleteTopic('MyTopic', function (error) {
    if (error) {
        console.log(error);
    }
});
```

Durch das Löschen eines Themas werden auch alle Abonnements gelöscht, die mit dem Thema registriert sind. Abonnements können auch unabhängig gelöscht werden. Das folgende Beispiel zeigt, wie ein Abonnement namens `HighMessages` aus dem Thema `MyTopic` gelöscht wird:

```javascript
serviceBusService.deleteSubscription('MyTopic', 'HighMessages', function (error) {
    if(error) {
        console.log(error);
    }
});
```

> [!NOTE]
> Sie können Service Bus-Ressourcen mit dem [Service Bus-Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/) verwalten. Mit dem Service Bus-Explorer können Benutzer eine Verbindung mit einem Service Bus-Namespace herstellen und Messagingentitäten auf einfache Weise verwalten. Das Tool stellt erweiterte Features wie Import-/Exportfunktionen oder Testmöglichkeiten für Themen, Warteschlangen, Abonnements, Relaydienste, Notification Hubs und Event Hubs zur Verfügung. 

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie nun mit den Grundlagen der Servicebus-Themen vertraut sind, finden Sie unter den folgenden Links weitere Informationen.

* Siehe [Warteschlangen, Themen und Abonnements][Queues, topics, and subscriptions].
* API-Referenz für [SqlFilter][SqlFilter]
* Besuchen Sie das [Azure SDK for Node][Azure SDK for Node]-Repository auf GitHub.

[Azure SDK for Node]: https://github.com/Azure/azure-sdk-for-node
[Azure portal]: https://portal.azure.com
[SqlFilter.SqlExpression]: service-bus-messaging-sql-filter.md
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.servicebus.messaging.sqlfilter
[Node.js Cloud Service]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md
[Erstellen und Bereitstellen einer Node.js-Anwendung auf einer Azure-Website]: ../app-service/app-service-web-get-started-nodejs.md
[Node.js Cloud Service with Storage]: ../cloud-services/cloud-services-nodejs-develop-deploy-app.md

