---
title: Erstellen und Bereitstellen einer Node.js-Express-App in Azure Cloud Services
description: Erstellen und Bereitstellen einer Express.js-Anwendung in Node.js für Azure Cloud Services
services: cloud-services
documentationcenter: nodejs
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 24f8e7ef-e90d-4554-9b1e-a9b31d5824e5
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: jeconnoc
ms.openlocfilehash: b212eaffb977846d40270a5f2abc76192aee4c0d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60527982"
---
# <a name="build-and-deploy-a-nodejs-web-application-using-express-on-an-azure-cloud-services"></a>Erstellen und Bereitstellen einer Node.js-Webanwendung mit Express in Azure Cloud Services

Die Core Runtime von Node.js umfasst eine Reihe elementarer Funktionen.
Entwickler verwenden bei der Entwicklung einer Node.js-Anwendung häufig Module von Drittanbietern, die über zusätzliche Funktionen verfügen. In diesem Lernprogramm erstellen Sie eine neue Anwendung mit dem Modul [Express](https://github.com/expressjs/express) , das ein MVC-Framework zum Erstellen von Node.js-Webanwendungen bereitstellt.

Nachfolgend sehen Sie einen Screenshot der fertigen Anwendung:

![Webbrowser, in dem 'Welcome to Express in Azure' angezeigt wird](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="create-a-cloud-service-project"></a>Erstellen eines Cloud-Dienst-Projekts
[!INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

Führen Sie die folgenden Schritte aus, um ein neues Cloud-Dienst-Projekt namens 'expressapp' zu erstellen:

1. Suchen Sie im **Startmenü** oder auf dem **Startbildschirm** nach **Windows PowerShell**. Klicken Sie dann mit der rechten Maustaste auf **Windows PowerShell**, und wählen Sie **Als Administrator ausführen** aus.
   
    ![Azure PowerShell-Symbol](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
2. Ändern Sie das Verzeichnis in **c:\\node**, und geben Sie die folgenden Befehle ein, um eine neue Projektmappe mit dem Namen **expressapp** und eine Webrolle mit dem Namen **WebRole1** zu erstellen:
   
        PS C:\node> New-AzureServiceProject expressapp
        PS C:\Node\expressapp> Add-AzureNodeWebRole
        PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21
   
    > [!NOTE]
    > Standardmäßig verwendet **Add-AzureNodeWebRole** eine ältere Version von "Node.js". Die oben stehende Anweisung **Set-AzureServiceProjectRole** weist Azure an, Node 0.10.21 zu verwenden.  Beachten Sie, dass bei Parametern die Groß-/Kleinschreibung relevant ist.  Sie können überprüfen, ob die richtige Version von „Node.js“ ausgewählt wurde, indem Sie die **engines**-Eigenschaft in **WebRole1\package.json** prüfen.
    > 
    > 

## <a name="install-express"></a>Installieren von Express
1. Installieren Sie den Express-Generator mit dem folgenden Befehl:
   
        PS C:\node\expressapp> npm install express-generator -g
   
    Die Ausgabe des Befehls npm sollte ungefähr wie folgt aussehen. 
   
    ![Windows PowerShell zeigt Ausgabe des Befehls npm install express an.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)
2. Ändern Sie das Verzeichnis in **WebRole1** , und erzeugen Sie mit dem Befehl express eine neue Anwendung:
   
        PS C:\node\expressapp\WebRole1> express
   
    Sie werden aufgefordert, die zuvor erstellte Anwendung zu überschreiben. Geben Sie **y** oder **yes** ein, um fortzufahren. Express erzeugt die Datei app.js und eine Ordnerstruktur zum Erstellen der Anwendung.
   
    ![Ausgabe des Befehls express](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)
3. Geben Sie den folgenden Befehl ein, um weitere in der Datei package.json definierte Abhängigkeiten zu installieren.
   
       PS C:\node\expressapp\WebRole1> npm install
   
   ![Ausgabe des Befehls npm install](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)
4. Kopieren Sie mit folgendem Befehl die Datei **bin/www** nach **server.js**. Damit kann der Cloud-Dienst den Einstiegspunkt für diese Anwendung finden.
   
       PS C:\node\expressapp\WebRole1> copy bin/www server.js
   
   Nach Ausführung dieses Befehls müsste die Datei **server.js** im Verzeichnis "WebRole1" vorliegen.
5. Entfernen Sie in **server.js** eines der '.'-Zeichen aus der folgenden Zeile.
   
       var app = require('../app');
   
   Nach dieser Änderung müsste die Zeile wie folgt aussehen.
   
       var app = require('./app');
   
   Diese Änderung ist erforderlich, da die Datei (ehemals **bin/www**,) in dasselbe Verzeichnis wie die benötigte App-Datei verschoben wurde. Speichern Sie die Datei **server.js** anschließend.
6. Verwenden Sie den folgenden Befehl, um die Anwendung im Azure-Emulator auszuführen:
   
       PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
   
    ![Webseite mit 'Welcome to Express'](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>Ändern der Ansicht
Ändern Sie nun die Ansicht, um die Meldung 'Welcome to Express in Azure' anzuzeigen.

1. Geben Sie den folgenden Befehl ein, um die Datei index.jade zu öffnen:
   
       PS C:\node\expressapp\WebRole1> notepad views/index.jade
   
   ![Inhalt der Datei index.jade](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)
   
   Jade ist die Standard-Anzeige-Engine, die von Express-Anwendungen verwendet wird. Weitere Informationen zur Anzeige-Engine Jade finden Sie unter [http://jade-lang.com][http://jade-lang.com].
2. Ändern Sie die letzte Textzeile, indem Sie **in Azure**anhängen.
   
   ![Die letzte Zeile in der Datei „index.jade“ lautet „p Welcome to \#{title} in Azure“.](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)
3. Speichern Sie die Datei, und beenden Sie den Editor.
4. Wenn Sie den Browser aktualisieren, werden die Änderungen angezeigt.
   
   ![Ein Browserfenster, die Seite enthält 'Welcome to Express in Azure'](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

Stoppen Sie den Emulator nach dem Testen der Anwendung mit dem Cmdlet **Stop-AzureEmulator** .

## <a name="publishing-the-application-to-azure"></a>Veröffentlichen der Anwendung in Azure
Verwenden Sie im Fenster von Azure PowerShell das Cmdlet **Publish-AzureServiceProject** , um die Anwendung in einem Cloud-Dienst bereitzustellen.

    PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch

Sobald die Bereitstellung abgeschlossen ist, wird der Browser geöffnet und die Webseite angezeigt.

![Webbrowser mit der Express-Seite. Die URL zeigt an, dass die Seite jetzt auf Azure gehostet wird.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie im [Node.js Developer Center](https://docs.microsoft.com/javascript/azure/?view=azure-node-latest).

[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Express]: https://expressjs.com/
[http://jade-lang.com]: http://jade-lang.com


