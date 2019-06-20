---
title: Node.js-Anwendung mit Socket.io – Azure
description: Erfahren Sie, wie Sie socket.io in einer node.js-Anwendung verwenden können, die in Azure gehostet wird.
services: cloud-services
documentationcenter: nodejs
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 7f9435e0-7732-4aa1-a4df-ea0e894b847f
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: jeconnoc
ms.openlocfilehash: cd0bceae770182e778410d8065d34dfeed055acc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61433130"
---
# <a name="build-a-nodejs-chat-application-with-socketio-on-an-azure-cloud-service"></a>Erstellen einer Node.js-Chatanwendung mit Socket.IO in einem Azure Cloud-Dienst

Socket.IO ermöglicht die Echtzeitkommunikation zwischen Ihrem Node.js-Server und den Clients. In diesem Tutorial wird das Hosten einer Socket.IO-basierten Chatanwendung in Azure erläutert. Weitere Informationen zu Socket.IO finden Sie unter [socket.io](https://socket.io).

Nachfolgend sehen Sie einen Screenshot der fertigen Anwendung:

![Browserfenster, in dem der in Azure gehostete Dienst angezeigt wird][completed-app]  

## <a name="prerequisites"></a>Voraussetzungen
Stellen Sie sicher, dass die folgenden Produkte und Versionen installiert sind, um das Beispiel in diesem Artikel erfolgreich abzuschließen:

* Installieren von [Visual Studio](https://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx)
* Installieren von [Node.js](https://nodejs.org/download/)
* Installieren von [Python, Version 2.7.10](https://www.python.org/)

## <a name="create-a-cloud-service-project"></a>Erstellen eines Cloud-Dienst-Projekts
Mit den folgenden Schritten wird das Cloud-Dienst-Projekt erstellt, in dem die Socket.IO-Anwendung gehostet wird.

1. Suchen Sie im **Startmenü** oder auf dem **Startbildschirm** nach **Windows PowerShell**. Klicken Sie dann mit der rechten Maustaste auf **Windows PowerShell**, und wählen Sie **Als Administrator ausführen** aus.
   
    ![Azure PowerShell-Symbol][powershell-menu]
2. Erstellen Sie ein Verzeichnis namens **c:\\node**. 
   
        PS C:\> md node
3. Wechseln Sie in das Verzeichnis **c:\\node**.
   
        PS C:\> cd node
4. Geben Sie die folgenden Befehle ein, um eine neue Projektmappe mit dem Namen **chatapp** und eine Workerrolle mit dem Namen **WorkerRole1** zu erstellen:
   
        PS C:\node> New-AzureServiceProject chatapp
        PS C:\Node> Add-AzureNodeWorkerRole
   
    Sie werden folgende Antwort erhalten:
   
    ![Ausgabe von new-azureservice und add-azurenodeworkerrolecmdlets](./media/cloud-services-nodejs-chat-app-socketio/socketio-1.png)

## <a name="download-the-chat-example"></a>Herunterladen des Chat-Beispiels
Für dieses Projekt verwenden wir das Chat-Beispiel aus dem [Socket.IO GitHub-Repository]. Führen Sie die folgenden Schritte aus, um das Beispiel herunterzuladen und es dem zuvor erstellten Projekt hinzuzufügen.

1. Erstellen Sie eine lokale Kopie des Repositorys mit der Schaltfläche **Klonen** . Sie können auch die Schaltfläche **ZIP** verwenden, um das Projekt herunterzuladen.
   
   ![Ein Browserfenster mit https://github.com/LearnBoost/socket.io/tree/master/examples/chat, in dem das ZIP-Downloadsymbol hervorgehoben ist](./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png)
2. Navigieren Sie in der Verzeichnisstruktur des lokalen Repositorys, bis sie zum Verzeichnis **examples\\chat** gelangen. Kopieren Sie den Inhalt dieses Verzeichnisses in das zuvor erstellte Verzeichnis **C:\\node\\chatapp\\WorkerRole1**.
   
   ![Explorer zeigt den Inhalt des aus dem Archiv extrahierten Verzeichnisses „examples\\chat“ an.][chat-contents]
   
   Die hervorgehobenen Elemente im Screenshot oben sind die aus dem Verzeichnis **examples\\chat** kopierten Dateien.
3. Löschen Sie im Verzeichnis **C:\\node\\chatapp\\WorkerRole1** die Datei **server.js**, und benennen Sie anschließend die Datei **app.js** in **server.js** um. Dadurch wird die Standarddatei **server.js** entfernt, die zuvor vom Cmdlet **Add-AzureNodeWorkerRole** erstellt wurde, und durch die Anwendungsdatei aus dem Chat-Beispiel ersetzt.

### <a name="modify-serverjs-and-install-modules"></a>Ändern von Server.js und Installieren von Modulen
Bevor die Anwendung im Azure-Emulator gestestet wird, müssen einige kleinere Änderungen vorgenommen werden. Führen Sie an der Datei server.js die folgenden Schritte aus:

1. Öffnen Sie die Datei **server.js** in Visual Studio oder einem beliebigen Text-Editor.
2. Suchen Sie den Abschnitt **Module dependencies** am Anfang der Datei „server.js“, und ändern Sie die Zeile **sio = require('..//..//lib//socket.io')** in **sio = require('socket.io')** , wie unten dargestellt:
   
       var express = require('express')
         , stylus = require('stylus')
         , nib = require('nib')
       //, sio = require('..//..//lib//socket.io'); //Original
         , sio = require('socket.io');                //Updated
         var port = process.env.PORT || 3000;         //Updated
3. Um sicherzustellen, dass die Anwendung am richtigen Port lauscht, öffnen Sie „server.js“ in Editor oder Ihrem bevorzugten Texteditor, und ändern Sie die folgende Zeile, indem Sie **3000** durch **process.env.port** ersetzen, wie unten dargestellt:
   
       //app.listen(3000, function () {            //Original
       app.listen(process.env.port, function () {  //Updated
         var addr = app.address();
         console.log('   app listening on http://' + addr.address + ':' + addr.port);
       });

Nachdem Sie die Änderungen in **server.js** gespeichert haben, gehen Sie folgendermaßen vor, um erforderliche Module zu installieren. Testen Sie die Anwendung anschließend im Azure-Emulator:

1. Ändern Sie mit **Azure PowerShell** das Verzeichnis auf **C:\\node\\chatapp\\WorkerRole1**, und verwenden Sie den folgenden Befehl, um die von dieser Anwendung benötigten Module zu installieren:
   
       PS C:\node\chatapp\WorkerRole1> npm install
   
   Damit werden die Module installiert, die in der Datei package.json aufgeführt sind. Nach Abschluss des Befehls sollten Sie eine Ausgabe ähnlich der folgenden erhalten:
   
   ![Ausgabe des Befehls npm install][The-output-of-the-npm-install-command]
2. Da dieses Beispiel ursprünglich ein Teil des Socket.IO GitHub-Repositorys war und über einen relativen Pfad auf die Socket.IO-Bibliothek direkt verwiesen wurde, wurde in der Datei package.json nicht auf Socket.IO verwiesen. Daher muss es mit dem folgenden Befehl installiert werden:
   
       PS C:\node\chatapp\WorkerRole1> npm install socket.io --save

### <a name="test-and-deploy"></a>Testen und Bereitstellen
1. Starten Sie den Emulator mit dem folgenden Befehl:
   
       PS C:\node\chatapp\WorkerRole1> Start-AzureEmulator -Launch
   
   > [!NOTE]
   > Wenn Probleme beim Starten des Emulators auftreten, z. B.: Start-AzureEmulator: Unerwarteter Fehler.  Details: Ein unerwarteter Fehler ist aufgetreten. Das Kommunikationsobjekt „System.ServiceModel.Channels.ServiceChannel“ kann nicht für die Kommunikation verwendet werden, da es den Status „Faulted“ aufweist.
   > 
   > Installieren Sie AzureAuthoringTools 2.7.1 und AzureComputeEmulator 2.7 neu. Stellen Sie sicher, dass die Versionen übereinstimmen.

2. Öffnen Sie einen Browser, und navigieren Sie zu **http://127.0.0.1** .
3. Wenn das Browserfenster geöffnet wird, geben Sie einen Spitznamen ein, und drücken Sie die Eingabetaste.
   So können Sie Nachrichten unter einem bestimmten Spitznamen posten. Um die Mehrbenutzerfunktion zu testen, öffnen Sie weitere Browserfenster mit derselben URL, und geben Sie andere Spitznamen ein.
   
   ![Zwei Browserfenster, in denen Chat-Nachrichten von Benutzer1 und Benutzer2 angezeigt werden](./media/cloud-services-nodejs-chat-app-socketio/socketio-8.png)
4. Stoppen Sie den Emulator nach dem Testen der Anwendung mit dem folgenden Befehl:
   
       PS C:\node\chatapp\WorkerRole1> Stop-AzureEmulator
5. Verwenden Sie für die Bereitstellung der Anwendung in Azure das Cmdlet **Publish-AzureServiceProject** . Beispiel:
   
       PS C:\node\chatapp\WorkerRole1> Publish-AzureServiceProject -ServiceName mychatapp -Location "East US" -Launch
   
   > [!IMPORTANT]
   > Verwenden Sie einen eindeutigen Namen, da der Veröffentlichungsprozess ansonsten fehlschlägt. Sobald die Bereitstellung erfolgt ist, wird der Browser geöffnet und navigiert zum bereitgestellten Dienst.
   > 
   > Wenn Sie eine Fehlermeldung erhalten, die besagt, dass der angegebene Abonnementname im importierten Veröffentlichungsprofil nicht vorhanden ist, müssen Sie das Veröffentlichungsprofil für Ihr Abonnement vor der Bereitstellung in Azure herunterladen und importieren. Weitere Informationen hierzu finden Sie im Abschnitt **Deploying the Application to Azure** (Bereitstellen der Anwendung in Azure) auf der Seite [Build and deploy a Node.js application to an Azure Cloud Service](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 
   
   ![Browserfenster, in dem der in Azure gehostete Dienst angezeigt wird][completed-app]
   
   > [!NOTE]
   > Wenn Sie eine Fehlermeldung erhalten, die besagt, dass der angegebene Abonnementname im importierten Veröffentlichungsprofil nicht vorhanden ist, müssen Sie das Veröffentlichungsprofil für Ihr Abonnement vor der Bereitstellung in Azure herunterladen und importieren. Weitere Informationen hierzu finden Sie im Abschnitt **Deploying the Application to Azure** (Bereitstellen der Anwendung in Azure) auf der Seite [Build and deploy a Node.js application to an Azure Cloud Service](https://azure.microsoft.com/develop/nodejs/tutorials/getting-started/)
   > 
   > 

Ihre Anwendung wird jetzt in Azure ausgeführt und kann Chat-Nachrichten zwischen verschiedenen Clients mithilfe von Socket.IO vermitteln.

> [!NOTE]
> Zur Vereinfachung beschränkt sich dieses Beispiel auf das Chatten zwischen Benutzern, die mit derselben Instanz verbunden sind. Wenn der Cloud-Dienst also zwei Workerrolleninstanzen erstellt, können die Benutzer nur mit Benutzern chatten, die mit derselben Workerrolleninstanz verbunden sind. Wenn Sie die Anwendung für mehrere Rolleninstanzen skalieren möchten, können Sie eine Technologie wie Service Bus verwenden, um den Socket.IO-Speicherzustand für mehrere Instanzen freizugeben. Beispiele hierzu finden Sie in den Syntaxbeispielen für Service Bus Queues und Service Bus-Themen im [Azure SDK für Node.js GitHub-Repository](https://github.com/WindowsAzure/azure-sdk-for-node).
> 
> 

## <a name="next-steps"></a>Nächste Schritte
In diesem Lernprogramm haben Sie erfahren, wie eine grundlegende Chat-Anwendung erstellt wird, die in einem Azure-Cloud-Dienst gehostet wird. Informationen zum Hosten dieser Anwendung in einer Azure-Website finden Sie unter [Erstellen einer Node.js-Chat-Anwendung mit Socket.IO in einer Azure-Website][chatwebsite].

Weitere Informationen finden Sie außerdem im [Node.js Developer Center](https://docs.microsoft.com/javascript/azure/?view=azure-node-latest).

[chatwebsite]: https://docs.microsoft.com/azure/cloud-services/cloud-services-nodejs-develop-deploy-app

[Azure SLA]: https://www.windowsazure.com/support/sla/
[Azure SDK for Node.js GitHub repository]: https://github.com/WindowsAzure/azure-sdk-for-node
[completed-app]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-10.png
[Azure SDK for Node.js]: https://www.windowsazure.com/develop/nodejs/
[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Socket.IO GitHub-Repository]: https://github.com/LearnBoost/socket.io/tree/0.9.14
[Azure Considerations]: #windowsazureconsiderations
[Hosting the Chat Example in a Worker Role]: #hostingthechatexampleinawebrole
[Summary and Next Steps]: #summary
[powershell-menu]: ./media/cloud-services-nodejs-chat-app-socketio/azure-powershell-start.png

[chat example]: https://github.com/LearnBoost/socket.io/tree/master/examples/chat
[chat-example-view]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-22.png


[chat-contents]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-5.png
[The-output-of-the-npm-install-command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-7.png
[The output of the Publish-AzureService command]: ./media/cloud-services-nodejs-chat-app-socketio/socketio-9.png


