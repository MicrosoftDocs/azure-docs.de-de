---
title: Erste Schritte mit dem Azure CDN SDK für Node.js | Microsoft Docs
description: Erfahren Sie, wie Sie eine einfache Node.js-Konsolenanwendung erstellen, die zeigt, wie die Erstellung und Verwaltung von Azure CDN-Profilen und -Endpunkten automatisiert werden kann.
services: cdn
documentationcenter: nodejs
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c4bb6a61-de3d-4f0c-9dca-202554c43dfa
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/23/2017
ms.author: mazha
ms.custom: devx-track-js
ms.openlocfilehash: f5d5c7a6e1f6993b19f38db2ae846b213a1d553e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993389"
---
# <a name="get-started-with-azure-cdn-development"></a>Erste Schritte mit der Azure CDN-Entwicklung
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Sie können das [Azure CDN SDK für Node.js](https://www.npmjs.com/package/azure-arm-cdn) verwenden, um die Erstellung und Verwaltung von CDN-Profilen und -Endpunkten zu automatisieren.  In diesem Tutorial wird die Erstellung einer einfachen Node.js-Konsolenanwendung beschrieben, mit der einige verfügbare Vorgänge demonstriert werden.  Dieses Tutorial dient nicht dazu, alle Aspekte des Azure CDN SDK für „Node.js“ ausführlich zu beschreiben.

Zum Durcharbeiten dieses Tutorials sollten Sie bereits [Node.js](https://www.nodejs.org) **4.x.x** oder höher installiert und konfiguriert haben.  Sie können einen beliebigen Text-Editor verwenden, um die Node.js-Anwendung zu erstellen.  Zum Schreiben dieses Tutorials habe ich [Visual Studio Code](https://code.visualstudio.com)verwendet.  

> [!TIP]
> Das [vervollständigte Projekt aus diesem Tutorial](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74) steht auf MSDN zum Download bereit.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>Erstellen des Projekts und Hinzufügen von NPM-Abhängigkeiten
Nachdem Sie nun eine Ressourcengruppe für Ihre CDN-Profile erstellt und der Azure AD-Anwendung die Berechtigung zum Verwalten von CDN-Profilen und -Endpunkten innerhalb dieser Gruppe erteilt haben, können Sie damit beginnen, Ihre Anwendung zu erstellen.

Erstellen Sie einen Ordner zum Speichern der Anwendung.  Legen Sie in einer Konsole mit den Node.js-Tools im aktuellen Pfad den aktuellen Speicherort auf diesen neuen Ordner fest, und initialisieren Sie Ihr Projekt, indem Sie Folgendes ausführen:

```console
npm init
```

Ihnen werden dann verschiedene Fragen zur Initialisierung des Projekts gestellt.  Als **Einstiegspunkt** wird in diesem Tutorial *app.js* verwendet.  Meine anderen gewählten Optionen sehen Sie im folgenden Beispiel:

![NPM-Initialisierungsausgabe](./media/cdn-app-dev-node/cdn-npm-init.png)

Unser Projekt wird jetzt mit der Datei *packages.json* initialisiert.  Im Projekt werden einige Azure-Bibliotheken verwendet, die in NPM-Paketen enthalten sind.  Wir verwenden die Azure-Clientlaufzeit für Node.js (ms-rest-azure) und die Azure CDN-Clientbibliothek für Node.js (azure-arm-cd).  Diese Komponenten fügen wir dem Projekt nun als Abhängigkeiten hinzu.

```console
npm install --save ms-rest-azure
npm install --save azure-arm-cdn
```

Nachdem die Installation der Pakete abgeschlossen ist, sollte die Datei *package.json* in etwa wie folgendes Beispiel aussehen (Versionsnummern können abweichen):

``` json
{
  "name": "cdn_node",
  "version": "1.0.0",
  "description": "Azure CDN Node.js tutorial project",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Cam Soper",
  "license": "MIT",
  "dependencies": {
    "azure-arm-cdn": "^0.2.1",
    "ms-rest-azure": "^1.14.4"
  }
}
```

Erstellen Sie abschließend mit dem Text-Editor eine leere Textdatei, und speichern Sie sie im Stamm Ihres Projektordners als *app.js*.  Wir können nun mit dem Schreiben von Code beginnen.

## <a name="requires-constants-authentication-and-structure"></a>„Requires“, Konstanten, Authentifizierung und Struktur
Bei geöffneter Datei *app.js* im Editor schreiben wir die Grundstruktur unseres Programms.

1. Fügen Sie die „Requires“-Elemente für unsere NPM-Pakete oben wie folgt hinzu:
   
    ``` javascript
    var msRestAzure = require('ms-rest-azure');
    var cdnManagementClient = require('azure-arm-cdn');
    ```
2. Sie müssen einige Konstanten definieren, die von den Methoden verwendet werden.  Fügen Sie Folgendes hinzu.  Ersetzen Sie die Platzhalter einschließlich der **&lt;spitzen Klammern&gt;** nach Bedarf durch Ihre eigenen Werte.
   
    ``` javascript
    //Tenant app constants
    const clientId = "<YOUR CLIENT ID>";
    const clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    const tenantId = "<YOUR TENANT ID>";
   
    //Application constants
    const subscriptionId = "<YOUR SUBSCRIPTION ID>";
    const resourceGroupName = "CdnConsoleTutorial";
    const resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. Als Nächstes instanziieren wir den CDN-Verwaltungsclient und stellen unsere Anmeldeinformationen bereit.
   
    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Wenn Sie die individuelle Benutzerauthentifizierung verwenden, sehen diese beiden Zeilen etwas anders aus.
   
   > [!IMPORTANT]
   > Verwenden Sie dieses Codebeispiel nur, wenn Sie die individuelle Benutzerauthentifizierung anstelle eines Dienstprinzipals verwenden möchten.  Schützen Sie Ihre individuellen Anmeldeinformationen, und halten Sie sie geheim.
   > 
   > 
   
    ``` javascript
    var credentials = new msRestAzure.UserTokenCredentials(clientId, 
        tenantId, '<username>', '<password>', '<redirect URI>');
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    Stellen Sie sicher, dass Sie die Elemente in **&lt;spitzen Klammern&gt;** durch die richtigen Informationen ersetzen.  Verwenden Sie für `<redirect URI>`den Umleitungs-URI, den Sie bei der Registrierung der Anwendung in Azure AD eingegeben haben.
4. Für unsere Node.js-Konsolenanwendung benötigen wir einige Befehlszeilenparameter.  Wir vergewissern uns, dass mindestens ein Parameter übergeben wurde.
   
   ```javascript
   //Collect command-line parameters
   var parms = process.argv.slice(2);
   
   //Do we have parameters?
   if(parms == null || parms.length == 0)
   {
       console.log("Not enough parameters!");
       console.log("Valid commands are list, delete, create, and purge.");
       process.exit(1);
   }
   ```
5. Hiermit gelangen wir zum Hauptteil unseres Programms, in dem wir basierend auf den übergebenen Parametern zu anderen Funktionen verzweigen.
   
    ```javascript
    switch(parms[0].toLowerCase())
    {
        case "list":
            cdnList();
            break;
   
        case "create":
            cdnCreate();
            break;
   
        case "delete":
            cdnDelete();
            break;
   
        case "purge":
            cdnPurge();
            break;
   
        default:
            console.log("Valid commands are list, delete, create, and purge.");
            process.exit(1);
    }
    ```
6. Wir müssen an mehreren Stellen im Programm sicherstellen, dass die richtige Anzahl von Parametern übergeben wurde, und Hilfe anzeigen, wenn sie nicht richtig erscheinen.  Hierfür erstellen wir Funktionen.
   
   ```javascript
   function requireParms(parmCount) {
       if(parms.length < parmCount) {
           usageHelp(parms[0].toLowerCase());
           process.exit(1);
       }
   }
   
   function usageHelp(cmd) {
       console.log("Usage for " + cmd + ":");
       switch(cmd)
       {
           case "list":
               console.log("list profiles");
               console.log("list endpoints <profile name>");
               break;
   
           case "create":
               console.log("create profile <profile name>");
               console.log("create endpoint <profile name> <endpoint name> <origin hostname>");
               break;
   
           case "delete":
               console.log("delete profile <profile name>");
               console.log("delete endpoint <profile name> <endpoint name>");
               break;
   
           case "purge":
               console.log("purge <profile name> <endpoint name> <path>");
               break;
   
           default:
               console.log("Invalid command.");
       }
   }
   ```
7. Außerdem sind die Funktionen, die wir auf dem CDN-Verwaltungsclient verwenden, asynchron. Sie müssen also über eine Methode zum Durchführen eines Rückrufs nach Abschluss des Vorgangs verfügen.  Wir erstellen eine Methode, mit der die Ausgabe des CDN-Verwaltungsprogramms (falls vorhanden) angezeigt und das Programm richtig beendet werden kann.
   
    ```javascript
    function callback(err, result, request, response) {
        if (err) {
            console.log(err);
            process.exit(1);
        } else {
            console.log((result == null) ? "Done!" : result);
            process.exit(0);
        }
    }
    ```

Nachdem wir die Grundstruktur für unser Programm nun geschrieben haben, sollten wir die Funktionen erstellen, die basierend auf unseren Parametern aufgerufen werden.

## <a name="list-cdn-profiles-and-endpoints"></a>Auflisten von CDN-Profilen und -Endpunkten
Wir beginnen mit Code zum Auflisten der vorhandenen Profile und Endpunkte.  Meine Codekommentare enthalten die erwartete Syntax, damit wir wissen, welcher Parameter an welche Stelle gehört.

```javascript
// list profiles
// list endpoints <profile name>
function cdnList(){
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profiles":
            console.log("Listing profiles...");
            cdnClient.profiles.listByResourceGroup(resourceGroupName, callback);
            break;

        case "endpoints":
            requireParms(3);
            console.log("Listing endpoints...");
            cdnClient.endpoints.listByProfile(parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>Erstellen von CDN-Profilen und -Endpunkten
Als Nächstes schreiben wir die Funktionen zum Erstellen von Profilen und Endpunkten.

```javascript
function cdnCreate() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profile":
            cdnCreateProfile();
            break;

        case "endpoint":
            cdnCreateEndpoint();
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}

// create profile <profile name>
function cdnCreateProfile() {
    requireParms(3);
    console.log("Creating profile...");
    var standardCreateParameters = {
        location: resourceLocation,
        sku: {
            name: 'Standard_Verizon'
        }
    };

    cdnClient.profiles.create(parms[2], standardCreateParameters, resourceGroupName, callback);
}

// create endpoint <profile name> <endpoint name> <origin hostname>        
function cdnCreateEndpoint() {
    requireParms(5);
    console.log("Creating endpoint...");
    var endpointProperties = {
        location: resourceLocation,
        origins: [{
            name: parms[4],
            hostName: parms[4]
        }]
    };

    cdnClient.endpoints.create(parms[3], endpointProperties, parms[2], resourceGroupName, callback);
}
```

## <a name="purge-an-endpoint"></a>Bereinigen eines Endpunkts
Eine häufige Aufgabe, die Sie nach der Erstellung des Endpunkts auch in Ihr Programm aufnehmen sollten, ist das Löschen von Inhalten auf dem Endpunkt.

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(parms[2], parms[1], resourceGroupName, purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>Löschen von CDN-Profilen und -Endpunkten
Mit der letzten Funktion, die wir einfügen, werden Endpunkte und Profile gelöscht.

```javascript
function cdnDelete() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        // delete profile <profile name>
        case "profile":
            requireParms(3);
            console.log("Deleting profile...");
            cdnClient.profiles.deleteIfExists(parms[2], resourceGroupName, callback);
            break;

        // delete endpoint <profile name> <endpoint name>
        case "endpoint":
            requireParms(4);
            console.log("Deleting endpoint...");
            cdnClient.endpoints.deleteIfExists(parms[3], parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="running-the-program"></a>Ausführen des Programms
Wir können unser Node.js-Programm jetzt ausführen, indem wir unseren bevorzugten Debugger oder die Konsole verwenden.

> [!TIP]
> Wenn Sie Visual Studio Code als Debugger verwenden, müssen Sie Ihre Umgebung so einrichten, dass die Befehlszeilenparameter übergeben werden.  Visual Studio Code verwendet hierfür die Datei **launch.json**.  Suchen Sie nach einer Eigenschaft mit dem Namen **args**, und fügen Sie ein Array mit Zeichenfolgenwerten für Ihre Parameter hinzu, um in etwa Folgendes zu erhalten: `"args": ["list", "profiles"]`.
> 
> 

Als Erstes listen wir unsere Profile auf.

![Profile auflisten](./media/cdn-app-dev-node/cdn-list-profiles.png)

Wir erhalten ein leeres Array zurück.  Dies war zu erwarten, da unsere Ressourcengruppe keine Profile enthält.  Als Nächstes erstellen wir ein Profil.

![Profil erstellen](./media/cdn-app-dev-node/cdn-create-profile.png)

Nun fügen wir einen Endpunkt hinzu.

![Endpunkt erstellen](./media/cdn-app-dev-node/cdn-create-endpoint.png)

Abschließend löschen wir das Profil.

![Profil löschen](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>Nächste Schritte
[Laden Sie das Beispiel herunter](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74), um sich das abgeschlossene Projekt dieser exemplarischen Vorgehensweise anzusehen.

Lesen Sie die [Referenz](https://azure.github.io/azure-sdk-for-node/azure-arm-cdn/latest/)zum Azure CDN SDK für Node.js durch.

Weitere Teile der Dokumentation zum Azure SDK für Node.js finden Sie in der [vollständigen Referenz](https://azure.github.io/azure-sdk-for-node/).

Verwalten Sie Ihre CDN-Ressourcen mit [PowerShell](cdn-manage-powershell.md).

