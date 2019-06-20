---
title: Arbeiten mit dem Node.js Back-End Server SDK für Mobile Apps | Microsoft-Dokumentation
description: Informationen zum Arbeiten mit dem Node.js Back-End Server SDK für mobile Azure App Service-Apps.
services: app-service\mobile
documentationcenter: ''
author: elamalani
manager: elamalani
editor: ''
ms.assetid: e7d97d3b-356e-4fb3-ba88-38ecbda5ea50
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 831f6b4bdc99e63859b390f8a9bb88d74301284e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62128099"
---
# <a name="how-to-use-the-mobile-apps-nodejs-sdk"></a>Verwenden des Node.js SDK für Mobile Apps

[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Dieser Artikel enthält ausführliche Informationen und Beispiele, die veranschaulichen, wie Sie im Mobile Apps-Feature von Azure App Service ein Node.js-Back-End verwenden.

## <a name="Introduction"></a>Einführung

Mobile Apps bietet eine Funktion zum Hinzufügen einer für Mobilgeräte optimierten Datenzugriff-Web-API zu einer Webanwendung. Das Mobile Apps SDK steht für ASP.NET- und Node.js-Webanwendungen zur Verfügung. Das SDK ermöglicht die folgenden Vorgänge:

* Tabellenvorgänge (Lesen, Einfügen, Aktualisieren, Löschen) für den Datenzugriff
* Vorgänge der benutzerdefinierten API

Beide Vorgänge sorgen für Authentifizierung über alle Identitätsanbieter hinweg, die in Azure App Service zulässig sind. Hierzu gehören Identitätsanbieter von sozialen Netzwerken wie Facebook, Twitter, Google und Microsoft sowie Azure Active Directory für die Identität in Unternehmen.

Beispiele für die einzelnen Anwendungsfälle finden Sie im [Verzeichnis mit den Beispielen auf GitHub].

## <a name="supported-platforms"></a>Unterstützte Plattformen

Das Mobile Apps Node.js SDK unterstützt das aktuelle LTS-Release von Node und höher. Derzeit ist Node v4.5.0 die neueste LTS-Version. Andere Node-Versionen funktionieren möglicherweise, werden aber nicht unterstützt.

Das Mobile Apps Node.js SDK unterstützt zwei Datenbanktreiber: 

* Der node-mssql-Treiber unterstützt Azure SQL-Datenbank und lokale SQL Server-Instanzen.  
* Der sqlite3-Treiber unterstützt SQLite-Datenbanken nur auf einer einzigen Instanz.

### <a name="howto-cmdline-basicapp"></a>Erstellen eines einfachen Node.js-Back-Ends über die Befehlszeile

Jedes Mobile Apps-Node.js-Back-End verhält sich am Anfang wie eine ExpressJS-Anwendung. ExpressJS ist das beliebteste Webdienst-Framework für Node.js. Sie können eine [Express] -Basisanwendung wie folgt erstellen:

1. Erstellen Sie in einem Befehlsfenster oder PowerShell-Fenster ein Verzeichnis für Ihr Projekt:

        mkdir basicapp

1. Führen Sie `npm init` aus, um die Paketstruktur zu initialisieren:

        cd basicapp
        npm init

   Im Rahmen des Befehls `npm init` werden einige Fragen gestellt, um das Projekt initialisieren zu können. Hier finden Sie eine Beispielausgabe:

   ![Ausgabe von npm init][0]

1. Installieren Sie die Bibliotheken `express` und `azure-mobile-apps` aus dem npm-Repository:

        npm install --save express azure-mobile-apps

1. Erstellen Sie die Datei „app.js“, um den grundlegenden mobilen Server zu implementieren:

    ```javascript
    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Define a TodoItem table.
    mobile.tables.add('TodoItem');

    // Add the Mobile API so it is accessible as a Web API.
    app.use(mobile);

    // Start listening on HTTP.
    app.listen(process.env.PORT || 3000);
    ```

Diese Anwendung erstellt eine für Mobilgeräte optimierte Web-API mit einem einzelnen Endpunkt (`/tables/TodoItem`), der mithilfe eines dynamischen Schemas nicht authentifizierten Zugriff auf einen zugrunde liegenden SQL-Datenspeicher bereitstellt. Mit dieser Anwendung können Sie die Schnellstarts für die Clientbibliothek nachvollziehen:

* [Android-Client-Schnellstart]
* [Apache Cordova-Client-Schnellstart]
* [iOS-Client-Schnellstart]
* [Windows Store-Client-Schnellstart]
* [Xamarin.iOS-Client-Schnellstart]
* [Xamarin.Android-Client-Schnellstart]
* [Xamarin.Forms-Client-Schnellstart]

Den Code für diese einfache Anwendung finden Sie im [basicapp-Beispiel auf GitHub].

### <a name="howto-vs2015-basicapp"></a>Erstellen eines Node.js-Back-Ends mithilfe von Visual Studio 2015

Für Visual Studio 2015 ist eine Erweiterung zum Entwickeln der Node.js-Anwendung in der IDE erforderlich. Um zu beginnen, installieren Sie die [Node.js-Tools 1.1 für Visual Studio]. Wenn Sie die Installation abschließen, erstellen Sie eine Express 4.x-Anwendung:

1. Öffnen Sie das Dialogfeld **Neues Projekt** (über **Datei** > **Neu** > **Projekt**).
1. Erweitern Sie **Vorlagen** > **JavaScript** > **Node.js**.
1. Wählen Sie die **Azure Node.js Express 4-Basisanwendung** aus.
1. Geben Sie den Projektnamen ein. Klicken Sie auf **OK**.

   ![Visual Studio 2015 – neues Projekt][1]
1. Klicken Sie mit der rechten Maustaste auf den Knoten **npm**, und wählen Sie **Neue npm-Pakete installieren** aus.
1. Nach der Erstellung der ersten Node.js-Anwendung müssen Sie den npm-Katalog möglicherweise aktualisieren. Klicken Sie bei Bedarf auf **Aktualisieren**.
1. Geben Sie in das Suchfeld **azure-mobile-apps** ein. Wählen Sie das Paket **azure-mobile-apps 2.0.0** aus, und klicken Sie dann auf **Paket installieren**.

   ![Neue npm-Pakete installieren][2]
1. Klicken Sie auf **Schließen**.
1. Öffnen Sie die Datei „app.js“, um Unterstützung für das Mobile Apps SDK hinzuzufügen. Fügen Sie unten in den `require`-Bibliotheksanweisungen in Zeile 6 den folgenden Code hinzu:

    ```javascript
    var bodyParser = require('body-parser');
    var azureMobileApps = require('azure-mobile-apps');
    ```

    Fügen Sie ungefähr bei Zeile 27 nach den anderen `app.use`-Anweisungen den folgenden Code hinzu:

    ```javascript
    app.use('/users', users);

    // Mobile Apps initialization
    var mobile = azureMobileApps();
    mobile.tables.add('TodoItem');
    app.use(mobile);
    ```

    Speichern Sie die Datei .

1. Führen Sie die Anwendung entweder lokal aus (die API wird unter `http://localhost:3000` bereitgestellt), oder veröffentlichen Sie sie in Azure.

### <a name="create-node-backend-portal"></a>Erstellen eines Node.js-Back-Ends mithilfe des Azure-Portals

Sie können ein Mobile Apps-Back-End direkt im [Azure-Portal] erstellen. Sie können entweder die folgenden Schritte ausführen oder anhand der Anweisungen im Tutorial [Erstellen einer mobilen App](app-service-mobile-ios-get-started.md) einen Client und einen Server erstellen. Das Tutorial enthält eine vereinfachte Version dieser Anweisungen und eignet sich am besten für Proof of Concept-Projekte.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Wählen Sie im Bereich **Erste Schritte** unter **Erstellen einer Tabellen-API** die Option **Node.js** als Back-End-Sprache aus.
Aktivieren Sie das Kontrollkästchen **Ich bestätige, dass durch diese Aktion alle Websiteinhalte überschrieben werden**, und klicken Sie auf **TodoItem-Tabelle erstellen**.

### <a name="download-quickstart"></a>Herunterladen des Schnellstart-Codeprojekts für das Node.js-Back-End mit Git

Wenn Sie im Portalbereich **Schnellstart** ein Node.js-Mobile Apps-Back-End erstellen, wird ein Node.js-Projekt für Sie erstellt und auf Ihrer Website bereitgestellt. Im Portal können Sie Tabellen und APIs hinzufügen und Codedateien für das Node.js-Back-End bearbeiten. Sie können auch verschiedene Bereitstellungstools zum Herunterladen des Back-End-Projekts verwenden, um Tabellen und APIs hinzuzufügen oder zu ändern und das Projekt dann erneut zu veröffentlichen. Weitere Informationen finden Sie im [Azure App Service-Bereitstellungshandbuch].

Beim folgenden Verfahren wird ein Git-Repository verwendet, um den Schnellstart-Projektcode herunterzuladen:

1. Installieren Sie Git, falls Sie dies noch nicht getan haben. Die erforderlichen Schritte zum Installieren von Git variieren je nach Betriebssystem. Informationen zu betriebssystemspezifischen Distributionen und zur Installation finden Sie unter [Installieren von Git](https://git-scm.com/book/en/Getting-Started-Installing-Git).
2. Lesen Sie die Informationen unter [Lokale Git-Bereitstellung in Azure App Service](../app-service/deploy-local-git.md#prepare-your-repository), um das Git-Repository für Ihre Back-End-Website zu aktivieren. Notieren Sie sich den Benutzernamen und das Kennwort der Bereitstellung.
3. Notieren Sie sich die Einstellung für die **Git-Klon-URL** im Bereich für Ihr Mobile App-Back-End.
4. Führen Sie den Befehl `git clone` mit der Git-Klon-URL aus. Geben Sie bei entsprechender Aufforderung Ihr Kennwort ein, wie im folgenden Beispiel gezeigt:

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

5. Navigieren Sie zum lokalen Verzeichnis (im obigen Beispiel ist dies `/todolist`). Sie sehen, dass Projektdateien heruntergeladen wurden. Suchen Sie die todoitem.json-Datei im `/tables`-Verzeichnis. Diese Datei definiert Berechtigungen für die Tabelle. Suchen Sie im gleichen Verzeichnis auch die todoitem.js-Datei. Sie definiert die CRUD-Vorgangsskripts für die Tabelle.
6. Wenn Sie Projektdateien geändert haben, führen Sie die folgenden Befehle aus, um die Änderungen hinzuzufügen, zu committen und sie dann auf die Website hochzuladen:

        $ git commit -m "updated the table script"
        $ git push origin master

   Wenn Sie dem Projekt neue Dateien hinzufügen, müssen Sie zuerst den Befehl `git add .` ausführen.

Die Website wird jeweils neu veröffentlicht, wenn ein neuer Satz mit Commits per Pushvorgang auf die Website übertragen wird.

### <a name="howto-publish-to-azure"></a>Veröffentlichen des Node.js-Back-Ends in Azure

Microsoft Azure bietet viele Verfahren zum Veröffentlichen Ihres Mobile Apps-Node.js-Back-Ends im Azure-Dienst. Hierzu gehören die Nutzung von Bereitstellungstools, die in Visual Studio integriert sind, Befehlszeilentools und Optionen für die fortlaufende Bereitstellung, die auf der Quellcodeverwaltung basieren. Weitere Informationen finden Sie im [Azure App Service-Bereitstellungshandbuch].

Azure App Service bietet spezielle Hinweise für Node.js-Anwendungen, die Sie vor dem Veröffentlichen des Back-Ends lesen sollten:

* [Angeben der Node.js-Version]
* Vorgehensweise: [Verwenden von Node.js-Modulen]

### <a name="howto-enable-homepage"></a>Aktivieren einer Startseite für Ihre Anwendung

Viele Anwendungen sind eine Kombination aus Web-Apps und mobilen Apps. Sie können das ExpressJS-Framework verwenden, um diese beiden Aspekte zu kombinieren. In manchen Fällen möchten Sie jedoch vielleicht nur eine Mobilschnittstelle implementieren. Es ist hilfreich, eine Startseite einzurichten, um sicherzustellen, dass der App-Dienst betriebsbereit ist und ausgeführt wird. Sie können entweder Ihre eigene Startseite bereitstellen oder eine temporäre Startseite aktivieren. Zum Aktivieren einer temporären Startseite verwenden Sie folgenden Code, um Mobile Apps zu instanziieren:

```javascript
var mobile = azureMobileApps({ homePage: true });
```

Wenn diese Option nur bei der lokalen Entwicklung zur Verfügung stehen soll, können Sie diese Einstellung zu Ihrer azureMobile.js-Datei hinzufügen.

## <a name="TableOperations"></a>Tabellenvorgänge

Das Node.js Server SDK für „azure-mobile-apps“ bietet Mechanismen, mit denen Sie in Azure SQL-Datenbank gespeicherte Datentabellen als Web-API verfügbar machen können. Fünf Vorgänge werden bereitgestellt:

| Vorgang | BESCHREIBUNG |
| --- | --- |
| GET /tables/*Tabellenname* |Ruft alle Datensätze der Tabelle ab. |
| GET /tables/*Tabellenname*/:id |Ruft einen bestimmten Datensatz der Tabelle ab. |
| POST /tables/*Tabellenname* |Erstellt einen Datensatz in der Tabelle. |
| PATCH /tables/*Tabellenname*/:id |Aktualisiert einen Datensatz in der Tabelle. |
| DELETE /tables/*Tabellenname*/:id |Löscht einen Datensatz in der Tabelle. |

Diese Web-API unterstützt [OData] und erweitert das Tabellenschema, um die [Synchronisierung von Offlinedaten] zu unterstützen.

### <a name="howto-dynamicschema"></a>Definieren von Tabellen mit einem dynamischen Schema

Bevor Sie eine Tabelle verwenden können, müssen Sie sie definieren. Sie können Tabellen mit einem statischen Schema (Sie definieren die Spalten im Schema) oder dynamisch (das SDK steuert das Schema basierend auf eingehenden Anforderungen) definieren. Außerdem können Sie bestimmte Aspekte der Web-API steuern, indem Sie der Definition JavaScript-Code hinzufügen.

Eine bewährte Methode hierbei lautet: Definieren Sie jede Tabelle in einer JavaScript-Datei im Verzeichnis `tables`, und verwenden Sie dann die `tables.import()`-Methode, um die Tabellen zu importieren. Zum Erweitern des grundlegenden App-Beispiels passen Sie die Datei „app.js“ an:

```javascript
var express = require('express'),
    azureMobileApps = require('azure-mobile-apps');

var app = express(),
    mobile = azureMobileApps();

// Define the database schema that is exposed.
mobile.tables.import('./tables');

// Provide initialization of any tables that are statically defined.
mobile.tables.initialize().then(function () {
    // Add the Mobile API so it is accessible as a Web API.
    app.use(mobile);

    // Start listening on HTTP.
    app.listen(process.env.PORT || 3000);
});
```

Definieren Sie die Tabelle in „./tables/TodoItem.js“:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Additional configuration for the table goes here.

module.exports = table;
```

Tabellen verwenden standardmäßig ein dynamisches Schema. Zum globalen Deaktivieren des dynamischen Schemas legen Sie die App-Einstellung `MS_DynamicSchema` im Azure-Portal auf „false“ fest.

Ein vollständiges Beispiel finden Sie im [todo-Beispiel auf GitHub].

### <a name="howto-staticschema"></a>Definieren von Tabellen mit einem statischen Schema

Sie können die Spalten explizit definieren, die über die Web-API verfügbar gemacht werden sollen. Das Node.js SDK für „azure-mobile-apps“ fügt der von Ihnen bereitgestellten Liste automatisch alle zusätzlichen Spalten hinzu, die für die Synchronisierung von Offlinedaten benötigt werden. Für die Schnellstart-Clientanwendungen ist beispielsweise eine Tabelle mit zwei Spalten erforderlich: `text` (eine Zeichenfolge) und `complete` (ein boolescher Wert).  
Die Tabelle kann wie folgt in der JavaScript-Datei mit der Tabellendefinition (im Verzeichnis `tables`) festgelegt werden:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};

// Turn off the dynamic schema.
table.dynamicSchema = false;

module.exports = table;
```

Wenn Sie Tabellen statisch definieren, müssen Sie auch die `tables.initialize()`-Methode aufrufen, um das Datenbankschema beim Starten zu erstellen. Die Methode `tables.initialize()` gibt ein [Promise]-Objekt zurück, um sicherzustellen, dass der Webdienst erst dann Anforderungen verarbeitet, nachdem die Datenbank initialisiert wurde.

### <a name="howto-sqlexpress-setup"></a>Verwenden von SQL Server Express als Entwicklungsdatenspeicher auf Ihrem lokalen Computer

Das Mobile Apps Node.js SDK bietet drei Optionen zum Bereitstellen von Daten ohne weitere Konfiguration:

* Verwenden Sie den **memory**-Treiber zum Bereitstellen eines nicht beständigen Beispielspeichers.
* Verwenden Sie den **mssql**-Treiber zum Bereitstellen eines SQL Server Express-Datenspeichers für die Entwicklung.
* Verwenden Sie den **mssql**-Treiber zum Bereitstellen eines Azure SQL-Datenbank-Datenspeichers für die Produktion.

Das Mobile Apps Node.js SDK verwendet das [mssql-Node.js-Paket], um eine Verbindung mit SQL Server Express und SQL-Datenbank einzurichten und zu nutzen. Für dieses Paket müssen Sie TCP-Verbindungen auf Ihrer SQL Server Express-Instanz aktivieren.

> [!TIP]
> Beim memory-Treiber wird keine vollständige Gruppe von Elementen für Testzwecke bereitgestellt. Wenn Sie Ihr Back-End lokal testen möchten, empfehlen wir die Verwendung eines SQL Server Express-Datenspeichers und des mssql-Treibers.

1. Laden Sie [Microsoft SQL Server 2014 Express]herunter, und installieren Sie die Anwendung. Stellen Sie sicher, dass Sie SQL Server 2014 Express-Edition mit Tools installieren. Falls Sie nicht unbedingt 64-Bit-Unterstützung benötigen, können Sie die 32-Bit-Version verwenden, die bei der Ausführung weniger Arbeitsspeicher verbraucht.
1. Führen Sie den SQL Server 2014-Konfigurations-Manager aus:

   a. Erweitern Sie im Strukturmenü den Knoten **SQL Server-Netzwerkkonfiguration**.

   b. Wählen Sie **Protokolle für SQLEXPRESS** aus.

   c. Klicken Sie mit der rechten Maustaste auf **TCP/IP**, und wählen Sie **Aktivieren**. Klicken Sie im Popupdialogfenster auf **OK**.

   d. Klicken Sie mit der rechten Maustaste auf **TCP/IP**, und wählen Sie **Eigenschaften**.

   e. Wählen Sie die Registerkarte **IP-Adressen** aus.

   f. Suchen Sie nach dem Knoten **IPAll** . Geben Sie im Feld **TCP-Port** den Wert **1433** ein.

      ![Konfigurieren von SQL Server Express für TCP/IP][3]

   g. Klicken Sie auf **OK**. Klicken Sie im Popupdialogfenster auf **OK**.

   h. Wählen Sie im Strukturmenü **SQL Server-Dienste** aus.

   i. Klicken Sie mit der rechten Maustaste auf **SQL Server (SQLEXPRESS)** , und wählen Sie **Neu starten** aus.

   j. Schließen Sie den SQL Server 2014-Konfigurations-Manager.

1. Ausführen von SQL Server 2014 Management Studio und Herstellen einer Verbindung mit Ihrer lokalen SQL Server Express-Instanz:

   1. Klicken Sie im Objekt-Explorer mit der rechten Maustaste auf Ihre Instanz, und wählen Sie **Eigenschaften** aus.
   1. Wählen Sie die Seite **Sicherheit** aus.
   1. Stellen Sie sicher, dass der **SQL Server- und Windows-Authentifizierungsmodus** ausgewählt ist.
   1. Klicken Sie auf **OK**.

      ![SQL Server Express-Authentifizierung konfigurieren][4]
   1. Erweitern Sie **Sicherheit** > **Anmeldungen** im Objekt-Explorer.
   1. Klicken Sie mit der rechten Maustaste auf **Anmeldungen**, und wählen Sie **Neue Anmeldung** aus.
   1. Geben Sie einen Anmeldenamen ein. Wählen Sie **SQL Server-Authentifizierung**. Geben Sie ein Kennwort ein, und geben Sie das gleiche Kennwort dann noch einmal unter **Kennwort bestätigen** ein. Das Kennwort muss die unter Windows erforderliche Kennwortkomplexität aufweisen.
   1. Klicken Sie auf **OK**.

      ![Neuen Benutzer zu SQL Server Express hinzufügen][5]
   1. Klicken Sie mit der rechten Maustaste auf die neue Anmeldung, und wählen Sie **Eigenschaften** aus.
   1. Wählen Sie die Seite **Serverrollen** aus.
   1. Aktivieren Sie das Kontrollkästchen für die Serverrolle **dbcreator**.
   1. Klicken Sie auf **OK**.
   1. Schließen Sie SQL Server 2015 Management Studio.

Notieren Sie sich unbedingt den gewählten Benutzernamen und das gewählte Kennwort. Möglicherweise müssen Sie je nach Datenbankanforderungen weitere Serverrollen oder Berechtigungen zuweisen.

Die Node.js-Anwendung liest die Umgebungsvariable `SQLCONNSTR_MS_TableConnectionString`, um die Verbindungszeichenfolge für diese Datenbank abzurufen. Sie können diese Variable in Ihrer Umgebung festlegen. Beispielsweise können Sie PowerShell verwenden, um diese Umgebungsvariable festzulegen:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Greifen Sie über eine TCP/IP-Verbindung auf die Datenbank zu. Geben Sie einen Benutzernamen und ein Kennwort für die Verbindung an.

### <a name="howto-config-localdev"></a>Konfigurieren des Projekts für die lokale Entwicklung

Mobile Apps liest eine JavaScript-Datei mit dem Namen *azureMobile.js* aus dem lokalen Dateisystem. Verwenden Sie diese Datei nicht, um das Mobile Apps SDK in einer Produktionsumgebung zu konfigurieren. Verwenden Sie stattdessen **App-Einstellungen** im [Azure-Portal].

Die Datei „azureMobile.js“ muss ein Konfigurationsobjekt exportieren. Die am häufigsten verwendeten Einstellungen lauten:

* Datenbankeinstellungen
* Diagnoseprotokollierungseinstellungen
* Alternative CORS-Einstellungen

Diese **azureMobile.js**-Beispieldatei implementiert die oben beschriebenen Datenbankeinstellungen:

```javascript
module.exports = {
    cors: {
        origins: [ 'localhost' ]
    },
    data: {
        provider: 'mssql',
        server: '127.0.0.1',
        database: 'mytestdatabase',
        user: 'azuremobile',
        password: 'T3stPa55word'
    },
    logging: {
        level: 'verbose'
    }
};
```

Es ist ratsam, dass Sie **azureMobile.js** Ihrer **.gitignore**-Datei hinzufügen (oder eine andere Datei zum Ignorieren der Quellcodeverwaltung), um zu verhindern, dass Kennwörter in der Cloud gespeichert werden. Konfigurieren Sie die Produktionseinstellungen immer in den **App-Einstellungen** im [Azure-Portal].

### <a name="howto-appsettings"></a>Konfigurieren von App-Einstellungen für Ihre mobile App

Für die meisten Einstellungen in der Datei „azureMobile.js“ gibt es im [Azure-Portal] eine entsprechende App-Einstellung. Verwenden Sie die folgende Liste, um Ihre App unter **App-Einstellungen** zu konfigurieren:

| App-Einstellung | azureMobile.js-Einstellung | BESCHREIBUNG | Gültige Werte |
|:--- |:--- |:--- |:--- |
| **MS_MobileAppName** |name |Name der App |Zeichenfolge |
| **MS_MobileLoggingLevel** |logging.level |Mindestprotokolliergrad für die zu protokollierenden Meldungen. |error, warning, info, verbose, debug, silly |
| **MS_DebugMode** |debug |Aktiviert oder deaktiviert den Debugmodus. |true, false |
| **MS_TableSchema** |data.schema |Name des Standardschemas für SQL-Tabellen |string (default: dbo) |
| **MS_DynamicSchema** |data.dynamicSchema |Aktiviert oder deaktiviert den Debugmodus. |true, false |
| **MS_DisableVersionHeader** |version (set to undefined) |Deaktiviert den Header „X-ZUMO-Server-Version“. |true, false |
| **MS_SkipVersionCheck** |skipversioncheck |Deaktiviert die Überprüfung der Client-API-Version. |true, false |

So legen Sie eine App-Einstellung fest:

1. Melden Sie sich beim [Azure-Portal] an.
1. Wählen Sie **Alle Ressourcen** oder **App Services** aus, und klicken Sie dann auf den Namen Ihrer mobilen App.
1. Der Bereich **Einstellungen** wird standardmäßig geöffnet. Wenn dies nicht der Fall ist, klicken Sie auf **Einstellungen**.
1. Wählen Sie im Menü **Allgemein** die Option **Anwendungseinstellungen** aus.
1. Scrollen Sie zum Abschnitt **App-Einstellungen**.
1. Wenn Ihre App-Einstellung bereits vorhanden ist, wählen Sie den Wert der App-Einstellung aus, um ihn zu bearbeiten.
   Wenn die App-Einstellung nicht vorhanden ist, geben Sie im Feld **Schlüssel** die App-Einstellung und im Feld **Wert** den Wert ein.
1. Wählen Sie **Speichern** aus.

Nach dem Ändern der meisten App-Einstellungen ist ein Neustart des Diensts erforderlich.

### <a name="howto-use-sqlazure"></a>Verwenden von SQL-Datenbank als Datenspeicher für die Produktion

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

Das Verwenden von Azure SQL-Datenbank als Datenspeicher ist über alle Azure App Service-Anwendungstypen hinweg identisch. Führen Sie diese Schritte zum Erstellen eines Mobile Apps-Back-Ends aus, falls Sie dies noch nicht getan haben:

1. Melden Sie sich beim [Azure-Portal] an.
1. Klicken Sie oben links im Fenster auf **+NEU** > **Web + Mobil** > **Mobile App**, und geben Sie dann einen Namen für Ihr Mobile Apps-Back-End an.
1. Geben Sie im Feld **Ressourcengruppe** den gleichen Namen wie für Ihre App ein.
1. Der App Service-Plan „Standard“ wird ausgewählt. Wenn Sie den App Service-Plan ändern möchten, gehen Sie folgendermaßen von:

   a. Wählen Sie **App Service-Plan** >  **+Neu erstellen** aus.

   b. Geben Sie einen Namen für den neuen App Service-Tarif ein, und wählen Sie einen geeigneten Speicherort.

   c. Wählen Sie einen geeigneten Tarif für den Dienst aus. Wählen Sie **Alle anzeigen** aus, um mehr Tarifoptionen anzuzeigen, z. B. **Free** und **Shared**.

   d. Klicken Sie auf die Schaltfläche **Auswählen**.

   e. Kehren Sie zum Bereich **App Service-Plan** zurück, und klicken Sie auf **OK**.
1. Klicken Sie auf **Erstellen**.

Das Bereitstellen eines Mobile Apps-Back-Ends kann einige Minuten in Anspruch nehmen. Nachdem das Mobile Apps-Back-End bereitgestellt wurde, wird im Portal der Bereich **Einstellungen** für das Mobile Apps-Back-End geöffnet.

Sie können entweder für eine vorhandene SQL-Datenbank eine Verbindung mit Ihrem Mobile Apps-Back-End herstellen oder eine neue SQL-Datenbank erstellen. In diesem Abschnitt erstellen wir eine SQL-Datenbank.

> [!NOTE]
> Wenn Sie am Standort des Mobile Apps-Back-Ends bereits über eine Datenbank verfügen, können Sie auf stattdessen **Vorhandene Datenbank verwenden** klicken und dann diese Datenbank auswählen. Die Verwendung einer Datenbank an einem anderen Standort wird aufgrund der höheren Latenz nicht empfohlen.

1. Wählen Sie im neuen Mobile Apps-Back-End **Einstellungen** > **Mobile App** > **Daten** >  **+Hinzufügen** aus.
1. Klicken Sie im Bereich **Datenverbindung hinzufügen** auf **SQL-Datenbank – erforderliche Einstellungen konfigurieren** > **Neue Datenbank erstellen**. Geben Sie den Namen der neuen Datenbank in das Feld **Name** ein.
1. Wählen Sie **Server** aus. Geben Sie im Bereich **Neuer Server** einen eindeutigen Namen in das Feld **Servername** ein, und geben Sie einen geeigneten Administratoranmeldenamen und ein Kennwort für den Server an. Achten Sie darauf, dass **Azure-Diensten Zugriff auf den Server erlauben** aktiviert ist. Klicken Sie auf **OK**.

   ![Erstellen einer Azure SQL-Datenbank][6]
1. Klicken Sie im Bereich **Neue Datenbank** auf **OK**.
1. Kehren Sie zum Bereich **Datenverbindung hinzufügen** zurück, wählen Sie **Verbindungszeichenfolge** aus, und geben Sie den Anmeldenamen und das Kennwort ein, den bzw. das Sie beim Erstellen der Datenbank angegeben haben. Wenn Sie eine vorhandene Datenbank verwenden, geben Sie die Anmeldeinformationen für diese Datenbank an. Klicken Sie auf **OK**.
1. Kehren Sie erneut zum Bereich **Datenverbindung hinzufügen** zurück, und klicken Sie auf **OK**, um die Datenbank zu erstellen.

<!--- END OF ALTERNATE INCLUDE -->

Das Erstellen der Datenbank kann einige Minuten dauern. Im Bereich **Benachrichtigungen** können Sie den Fortschritt der Bereitstellung überwachen. Fahren Sie erst fort, wenn die Datenbank erfolgreich bereitgestellt wurde. Nach der Bereitstellung der Datenbank wird in den Einstellungen Ihres Mobile Apps-Back-Ends eine Verbindungszeichenfolge für die SQL-Datenbankinstanz erstellt. Sie können diese App-Einstellung unter **Einstellungen** > **Anwendungseinstellungen** > **Verbindungszeichenfolgen** anzeigen.

### <a name="howto-tables-auth"></a>Erzwingen der Authentifizierung für den Zugriff auf Tabellen

Wenn Sie die App Service-Authentifizierung mit dem `tables`-Endpunkt verwenden möchten, müssen Sie die App Service-Authentifizierung zuerst im [Azure-Portal] konfigurieren. Weitere Informationen finden Sie im Konfigurationshandbuch für den Identitätsanbieter, den Sie verwenden möchten:

* [Konfigurieren der Azure Active Directory-Authentifizierung]
* [Konfigurieren der Facebook-Authentifizierung]
* [Konfigurieren der Google-Authentifizierung]
* [Konfigurieren der Microsoft-Authentifizierung]
* [Konfigurieren der Twitter-Authentifizierung]

Jede Tabelle verfügt über eine access-Eigenschaft, die zum Steuern des Zugriffs auf die Tabelle verwendet werden kann. Das folgende Beispiel zeigt eine statisch definierte Tabelle mit erforderlicher Authentifizierung.

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};

// Turn off the dynamic schema.
table.dynamicSchema = false;

// Require authentication to access the table.
table.access = 'authenticated';

module.exports = table;
```

Die access-Eigenschaft kann einen von drei Werten annehmen:

* *anonymous* gibt an, dass die Clientanwendung Daten ohne Authentifizierung lesen darf.
* *authenticated* gibt an, dass die Clientanwendung zusammen mit der Anforderung ein gültiges Authentifizierungstoken senden muss.
* *disabled* gibt an, dass die Tabelle derzeit deaktiviert ist.

Wenn die access-Eigenschaft nicht definiert ist, ist der Zugriff ohne Authentifizierung zulässig.

### <a name="howto-tables-getidentity"></a>Verwenden von Authentifizierungsansprüchen für Tabellen
Sie können verschiedene Ansprüche einrichten, die beim Einrichten der Authentifizierung angefordert werden. Diese Ansprüche stehen normalerweise nicht über das `context.user` -Objekt zur Verfügung. Sie können jedoch mithilfe der `context.user.getIdentity()`-Methode abgerufen werden. Die `getIdentity()`-Methode gibt eine Zusage (promise) zurück, die in ein Objekt aufgelöst wird. Das Objekt wird durch die Authentifizierungsmethode mit einem Schlüssel versehen (`facebook`, `google`, `twitter`, `microsoftaccount` oder `aad`).

Wenn Sie beispielsweise die Microsoft-Kontoauthentifizierung einrichten und den Anspruch „E-Mail-Adressen“ anfordern, können Sie die E-Mail-Adresse mit dem folgenden Tabellencontroller zum Datensatz hinzufügen:

```javascript
var azureMobileApps = require('azure-mobile-apps');

// Create a new table definition.
var table = azureMobileApps.table();

table.columns = {
    "emailAddress": "string",
    "text": "string",
    "complete": "boolean"
};
table.dynamicSchema = false;
table.access = 'authenticated';

/**
* Limit the context query to those records with the authenticated user email address
* @param {Context} context the operation context
* @returns {Promise} context execution Promise
*/
function queryContextForEmail(context) {
    return context.user.getIdentity().then((data) => {
        context.query.where({ emailAddress: data.microsoftaccount.claims.emailaddress });
        return context.execute();
    });
}

/**
* Adds the email address from the claims to the context item - used for
* insert operations
* @param {Context} context the operation context
* @returns {Promise} context execution Promise
*/
function addEmailToContext(context) {
    return context.user.getIdentity().then((data) => {
        context.item.emailAddress = data.microsoftaccount.claims.emailaddress;
        return context.execute();
    });
}

// Configure specific code when the client does a request.
// READ: only return records that belong to the authenticated user.
table.read(queryContextForEmail);

// CREATE: add or overwrite the userId based on the authenticated user.
table.insert(addEmailToContext);

// UPDATE: only allow updating of records that belong to the authenticated user.
table.update(queryContextForEmail);

// DELETE: only allow deletion of records that belong to the authenticated user.
table.delete(queryContextForEmail);

module.exports = table;
```

Um zu sehen, welche Ansprüche verfügbar sind, verwenden Sie einen Webbrowser zum Anzeigen des `/.auth/me` -Endpunkts der Website.

### <a name="howto-tables-disabled"></a>Deaktivieren des Zugriffs auf bestimmte Tabellenvorgänge

Die access-Eigenschaft kann nicht nur in der Tabelle angezeigt werden, sondern sie kann auch verwendet werden, um einzelne Vorgänge zu steuern. Es gibt vier Vorgänge:

* `read` ist der auf die Tabelle angewendete RESTful GET-Vorgang.
* `insert` ist der auf die Tabelle angewendete RESTful POST-Vorgang.
* `update` ist der auf die Tabelle angewendete RESTful PATCH-Vorgang.
* `delete` ist der auf die Tabelle angewendete RESTful DELETE-Vorgang.

Es kann beispielsweise sein, dass Sie eine schreibgeschützte, nicht authentifizierte Tabelle bereitstellen möchten:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Read-only table. Only allow READ operations.
table.read.access = 'anonymous';
table.insert.access = 'disabled';
table.update.access = 'disabled';
table.delete.access = 'disabled';

module.exports = table;
```

### <a name="howto-tables-query"></a>Anpassen der Abfrage für Tabellenvorgänge

Eine häufige Anforderung an Tabellenvorgänge ist das Bereitstellen einer eingeschränkten Anzeige von Daten. Beispielsweise können Sie eine Tabelle bereitstellen, die mit der authentifizierten Benutzer-ID gekennzeichnet ist, damit Sie nur Ihre eigenen Datensätze lesen oder aktualisieren können. Mit der folgenden Tabellendefinition werden diese Funktionen bereitgestellt:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define a static schema for the table.
table.columns = {
    "userId": "string",
    "text": "string",
    "complete": "boolean"
};
table.dynamicSchema = false;

// Require authentication for this table.
table.access = 'authenticated';

// Ensure that only records for the authenticated user are retrieved.
table.read(function (context) {
    context.query.where({ userId: context.user.id });
    return context.execute();
});

// When adding records, add or overwrite the userId with the authenticated user.
table.insert(function (context) {
    context.item.userId = context.user.id;
    return context.execute();
});

module.exports = table;
```

Vorgänge, bei denen normalerweise eine Abfrage ausgeführt wird, verfügen über eine Abfrageeigenschaft, die Sie mit einer `where`-Klausel anpassen können. Die Abfrageeigenschaft ist ein [QueryJS]-Objekt zum Konvertieren einer OData-Abfrage in ein Element, das vom Daten-Back-End verarbeitet werden kann. Für einfache Gleichheit (wie im vorherigen Fall) können Sie eine Zuordnung verwenden. Sie können auch bestimmte SQL-Klauseln hinzufügen:

```javascript
context.query.where('myfield eq ?', 'value');
```

### <a name="howto-tables-softdelete"></a>Konfigurieren des vorläufigen Löschens in einer Tabelle

Beim vorläufigen Löschen werden Datensätze nicht tatsächlich gelöscht. Stattdessen werden sie in der Datenbank als gelöscht markiert, indem die Spalte „deleted“ auf „true“ festgelegt wird. Das Mobile Apps SDK entfernt vorläufig gelöschte Datensätze automatisch aus Ergebnissen, es sei denn, im Mobile Client SDK wird `IncludeDeleted()` verwendet. Legen Sie in der Tabellendefinitionsdatei die `softDelete`-Eigenschaft fest, um eine Tabelle für das vorläufige Löschen zu konfigurieren:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};

// Turn off the dynamic schema.
table.dynamicSchema = false;

// Turn on soft delete.
table.softDelete = true;

// Require authentication to access the table.
table.access = 'authenticated';

module.exports = table;
```

Sie sollten ein Verfahren zum Löschen von Datensätzen einrichten: eine Clientanwendung, einen WebJob, eine Azure-Funktion oder eine benutzerdefinierte API.

### <a name="howto-tables-seeding"></a>Durchführen des Seedings für Ihre Datenbank mit Daten

Wenn Sie eine neue Anwendung erstellen, kann es sein, dass Sie für eine Tabelle ein Seeding mit Daten durchführen möchten. Dies kann innerhalb der JavaScript-Datei mit der Tabellendefinition erfolgen:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};
table.seed = [
    { text: 'Example 1', complete: false },
    { text: 'Example 2', complete: true }
];

// Turn off the dynamic schema.
table.dynamicSchema = false;

// Require authentication to access the table.
table.access = 'authenticated';

module.exports = table;
```

Ein Seeding der Daten findet nur statt, wenn Sie zum Erstellen der Tabelle das Mobile Apps SDK verwendet haben. Wenn die Tabelle in der Datenbank bereits vorhanden ist, werden keine Daten in die Tabelle eingefügt. Wenn das dynamische Schema aktiviert ist, wird das Schema aus den Seedingdaten abgeleitet.

Wir empfehlen, die `tables.initialize()` -Methode explizit aufzurufen, um die Tabelle zu erstellen, wenn die Ausführung des Diensts beginnt.

### <a name="Swagger"></a>Aktivieren der Swagger-Unterstützung
[Swagger]-Unterstützung ist in Mobile Apps bereits integriert. Um die Swagger-Unterstützung zu aktivieren, installieren Sie zuerst die Swagger-Benutzeroberfläche als Abhängigkeit:

    npm install --save swagger-ui

Danach können Sie die Swagger-Unterstützung im Mobile Apps-Konstruktor aktivieren:

```javascript
var mobile = azureMobileApps({ swagger: true });
```

Die Swagger-Unterstützung soll wahrscheinlich nur in Entwicklungseditionen aktiviert werden. Hierzu können Sie die App-Einstellung `NODE_ENV` verwenden:

```javascript
var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });
```

Der `swagger`-Endpunkt befindet sich unter http://*Ihre_Website*.azurewebsites.net/swagger. Sie können über den `/swagger/ui` -Endpunkt auf die Swagger-Benutzeroberfläche zugreifen. Wenn Sie die Authentifizierung in Ihrer gesamten Anwendung als erforderlich festlegen, gibt Swagger einen Fehler aus. Um optimale Ergebnisse zu erzielen, lassen Sie in den Einstellungen für die Azure App Service-Authentifizierung/-Autorisierung nicht authentifizierte Anforderungen zu, und steuern Sie dann die Authentifizierung mithilfe der `table.access`-Eigenschaft.

Sie können die Swagger-Option auch der Datei „azureMobile.js“ hinzufügen, wenn die Swagger-Unterstützung nur für die lokale Entwicklung verfügbar sein soll.

## <a name="a-namepushpush-notifications"></a><a name="push"/>Pushbenachrichtigungen

Mobile Apps ist in Azure Notification Hubs integriert, damit Sie gezielte Pushbenachrichtigungen an Millionen von Geräten auf allen gängigen Plattformen senden können. Mit Notification Hubs können Sie Pushbenachrichtigungen an iOS-, Android- und Windows-Geräte senden. Weitere Informationen zu den verfügbaren Möglichkeiten mit Notification Hubs finden Sie unter [Übersicht über Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md).

### <a name="send-push"></a>Senden von Pushbenachrichtigungen

Der folgende Code zeigt, wie Sie das `push`-Objekt zum Senden einer Broadcast-Pushbenachrichtigung an registrierte iOS-Geräte verwenden:

```javascript
// Create an APNS payload.
var payload = '{"aps": {"alert": "This is an APNS payload."}}';

// Only do the push if configured.
if (context.push) {
    // Send a push notification by using APNS.
    context.push.apns.send(null, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

Indem Sie auf dem Client eine Pushregistrierungsvorlage erstellen, können Sie stattdessen eine Pushnachrichtenvorlage an Geräte auf allen unterstützten Plattformen senden. Der folgende Code zeigt, wie eine Benachrichtigungsvorlage gesendet wird:

```javascript
// Define the template payload.
var payload = '{"messageParam": "This is a template payload."}';

// Only do the push if configured.
if (context.push) {
    // Send a template notification.
    context.push.send(null, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

### <a name="push-user"></a>Senden von Pushbenachrichtigungen an einen authentifizierten Benutzer mithilfe von Tags
Wenn ein authentifizierter Benutzer für Pushbenachrichtigungen registriert wird, wird der Registrierung automatisch ein Tag mit der Benutzer-ID hinzugefügt. Mithilfe dieses Tags können Sie Pushbenachrichtigungen an alle Geräte senden, die von einem bestimmten Benutzer registriert wurden. Der folgende Code ruft die SID des Benutzers ab, der die Anforderung stellt, und sendet eine Pushbenachrichtigungsvorlage an jede Geräteregistrierung für diesen Benutzer:

```javascript
// Only do the push if configured.
if (context.push) {
    // Send a notification to the current user.
    context.push.send(context.user.id, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

Bevor Sie sich für Pushbenachrichtigungen von einem authentifizierten Client registrieren, stellen Sie sicher, dass die Authentifizierung abgeschlossen ist.

## <a name="CustomAPI"></a> Benutzerdefinierte APIs

### <a name="howto-customapi-basic"></a>Definieren einer benutzerdefinierten API

Zusätzlich zur Datenzugriff-API über den `/tables`-Endpunkt unterstützt Mobile Apps auch benutzerdefinierte APIs. Benutzerdefinierte APIs werden auf ähnliche Weise wie Tabellendefinitionen definiert und können auf die gleichen Funktionen zugreifen, einschließlich der Authentifizierung.

Wenn Sie die App Service-Authentifizierung mit einer benutzerdefinierten API verwenden möchten, müssen Sie die App Service-Authentifizierung zuerst im [Azure-Portal] konfigurieren. Weitere Informationen finden Sie im Konfigurationshandbuch für den Identitätsanbieter, den Sie verwenden möchten:

* [Konfigurieren der Azure Active Directory-Authentifizierung]
* [Konfigurieren der Facebook-Authentifizierung]
* [Konfigurieren der Google-Authentifizierung]
* [Konfigurieren der Microsoft-Authentifizierung]
* [Konfigurieren der Twitter-Authentifizierung]

Benutzerdefinierte APIs werden auf ähnliche Weise wie die Tabellen-API definiert:

1. Erstellen Sie ein Verzeichnis namens `api`.
1. Erstellen Sie im `api`-Verzeichnis eine JavaScript-Datei mit einer API-Definition.
1. Verwenden Sie die import-Methode, um das `api`-Verzeichnis zu importieren.

Hier finden Sie die API-Prototypdefinition, die auf dem weiter oben in diesem Artikel verwendeten grundlegenden App-Beispiel basiert:

```javascript
var express = require('express'),
    azureMobileApps = require('azure-mobile-apps');

var app = express(),
    mobile = azureMobileApps();

// Import the custom API.
mobile.api.import('./api');

// Add the Mobile API so it is accessible as a Web API.
app.use(mobile);

// Start listening on HTTP
app.listen(process.env.PORT || 3000);
```

Wir verwenden eine Beispiel-API, die mithilfe der `Date.now()`-Methode das Serverdatum zurückgibt. Dies ist die Datei „api/date.js“:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};

module.exports = api;
```

Jeder Parameter entspricht einem der standardmäßigen RESTful-Verben: GET, POST, PATCH oder DELETE. Bei der Methode handelt es sich um eine [ExpressJS-Middleware]-Standardfunktion, mit der die erforderliche Ausgabe gesendet wird.

### <a name="howto-customapi-auth"></a>Erzwingen der Authentifizierung für den Zugriff auf eine benutzerdefinierte API

Das Mobile Apps SDK implementiert die Authentifizierung sowohl für den `tables`-Endpunkt als auch für benutzerdefinierte APIs auf die gleiche Weise. Fügen Sie eine `access`-Eigenschaft hinzu, um die Authentifizierung für die im vorherigen Abschnitt entwickelte API hinzuzufügen:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};
// All methods must be authenticated.
api.access = 'authenticated';

module.exports = api;
```

Sie können die Authentifizierung auch für bestimmte Vorgänge angeben:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    }
};
// The GET methods must be authenticated.
api.get.access = 'authenticated';

module.exports = api;
```

Dasselbe Token, das für den `tables`-Endpunkt verwendet wird, muss auch für benutzerdefinierte APIs verwendet werden, die eine Authentifizierung erfordern.

### <a name="howto-customapi-auth"></a>Ausführen großer Dateiuploads

Das Mobile Apps SDK verwendet die [body-parser-Middleware](https://github.com/expressjs/body-parser), um Textinhalt in Ihrer Übermittlung zu akzeptieren und zu decodieren. Sie können „body-parser“ im Voraus so konfigurieren, dass größere Dateiuploads akzeptiert werden:

```javascript
var express = require('express'),
    bodyParser = require('body-parser'),
    azureMobileApps = require('azure-mobile-apps');

var app = express(),
    mobile = azureMobileApps();

// Set up large body content handling.
app.use(bodyParser.json({ limit: '50mb' }));
app.use(bodyParser.urlencoded({ limit: '50mb', extended: true }));

// Import the custom API.
mobile.api.import('./api');

// Add the Mobile API so it is accessible as a Web API.
app.use(mobile);

// Start listening on HTTP.
app.listen(process.env.PORT || 3000);
```

Diese Datei wird vor der Übertragung Base64-codiert. Durch diese Codierung erhöht sich die Größe des tatsächlichen Uploads (und damit auch die Größe, die Sie einberechnen müssen).

### <a name="howto-customapi-sql"></a>Ausführen von benutzerdefinierten SQL-Anweisungen

Das Mobile Apps SDK ermöglicht über das request-Objekt Zugriff auf den gesamten Kontext. Sie können ganz einfach parameterbasierte SQL-Anweisungen für den definierten Datenanbieter ausführen:

```javascript
var api = {
    get: function (request, response, next) {
        // Check for parameters. If not there, pass on to a later API call.
        if (typeof request.params.completed === 'undefined')
            return next();

        // Define the query. Anything that the mssql
        // driver can handle is allowed.
        var query = {
            sql: 'UPDATE TodoItem SET complete=@completed',
            parameters: [{
                completed: request.params.completed
            }]
        };

        // Execute the query. The context for Mobile Apps is available through
        // request.azureMobile. The data object contains the configured data provider.
        request.azureMobile.data.execute(query)
        .then(function (results) {
            response.json(results);
        });
    }
};

api.get.access = 'authenticated';
module.exports = api;
```

## <a name="Debugging"></a>Debuggen, Easy Tables und Easy APIs

### <a name="howto-diagnostic-logs"></a>Debuggen, Diagnose und Problembehandlung bei Mobile Apps

Azure App Service stellt mehrere Debugging- und Problembehandlungsverfahren für Node.js-Anwendungen bereit.
Folgende Artikel helfen Ihnen beim Einstieg in die Problembehandlung Ihres Node.js Mobile Apps-Back-Ends:

* [Überwachen von Azure App Service]
* [Aktivieren der Diagnoseprotokollierung in Azure App Service]
* [Problembehandlung für Azure App Service in Visual Studio]

Node.js-Anwendungen haben Zugriff auf viele Tools für die Diagnoseprotokollierung. Intern nutzt das Mobile Apps Node.js SDK [Winston] für die Diagnoseprotokollierung. Die Protokollierung wird automatisch aktiviert, wenn Sie den Debugmodus aktivieren oder die App-Einstellung `MS_DebugMode` im [Azure-Portal] auf „true“ festlegen. Generierte Protokolle werden im [Azure-Portal] in den Diagnoseprotokollen angezeigt.

### <a name="in-portal-editing"></a><a name="work-easy-tables"></a>Verwenden von einfachen Tabellen im Azure-Portal

Mit einfachen Tabellen können Sie Tabellen direkt im Portal erstellen und damit arbeiten. Sie können Datasets im CSV-Format in Easy Tables hochladen. Beachten Sie, dass Sie keine Namen von Eigenschaften (in Ihrem CSV-Dataset) verwenden können, die mit den Namen von Systemeigenschaften des Mobile Apps-Back-Ends in Konflikt stehen. Es handelt sich dabei um folgende Namen von Systemeigenschaften:
* createdAt
* updatedAt
* deleted
* version

Sie können mit dem App Service-Editor sogar Tabellenvorgänge bearbeiten. Wenn Sie in den Einstellungen für die Back-End-Website **Einfache Tabellen** auswählen, können Sie eine Tabelle hinzufügen, ändern oder löschen. Sie sehen auch, dass Daten in der Tabelle enthalten sind.

![Verwenden von Easy Tables](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-tables.png)

Die folgenden Befehle stehen in der Befehlsleiste einer Tabelle zur Verfügung:

* **Berechtigungen ändern**: Dient zum Ändern der Berechtigung für Vorgänge zum Lesen, Einfügen, Aktualisieren und Löschen in der Tabelle.
 Sie können den anonymen Zugriff zulassen, eine Authentifizierung erzwingen oder den gesamten Zugriff auf den Vorgang deaktivieren.
* **Skript bearbeiten**: Die Skriptdatei für die Tabelle wird im App Service-Editor geöffnet.
* **Schema verwalten**: Dient zum Hinzufügen oder Löschen von Spalten oder zum Ändern des Tabellenindex.
* **Tabelle löschen**: Dient zum Kürzen bzw. Abschneiden einer vorhandenen Tabelle, indem alle Datenzeilen gelöscht werden, während das Schema unverändert bleibt.
* **Zeilen löschen**: Dient zum Löschen einzelner Zeilen mit Daten.
* **Streamingprotokolle anzeigen**: Dient zum Herstellen einer Verbindung mit dem Streamingprotokolldienst für Ihre Website.

### <a name="work-easy-apis"></a>Verwenden von einfachen APIs im Azure-Portal

Sie können einfache APIs verwenden, um benutzerdefinierte APIs direkt im Portal zu erstellen und damit zu arbeiten. Sie können API-Skripts mit dem App Service-Editor bearbeiten.

Wenn Sie in den Einstellungen für die Back-End-Website auf **Einfache APIs** klicken, können Sie einen benutzerdefinierten API-Endpunkt hinzufügen, ändern oder löschen.

![Verwenden von Easy APIs](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-easy-apis.png)

Im Portal können Sie die Zugriffsberechtigungen für eine HTTP-Aktion ändern, die API-Skriptdatei im App Service-Editor bearbeiten oder die Streamingprotokolle anzeigen.

### <a name="online-editor"></a>Bearbeiten von Code im App Service-Editor

Durch Verwendung des Azure-Portals können Sie Ihre Node.js-Back-End-Skriptdateien im App Service-Editor bearbeiten, ohne das Projekt auf den lokalen Computer herunterladen zu müssen. So bearbeiten Sie Skriptdateien im Online-Editor

1. Wählen Sie im Bereich für Ihr Mobile Apps-Back-End **Alle Einstellungen** und dann entweder **Einfache Tabellen** oder **Einfache APIs** aus. Wählen Sie eine Tabelle oder API aus, und klicken Sie dann auf **Skript bearbeiten**. Die Skriptdatei wird im App Service-Editor geöffnet.

   ![App Service-Editor](./media/app-service-mobile-node-backend-how-to-use-server-sdk/mobile-apps-visual-studio-editor.png)
1. Nehmen Sie im Online-Editor die gewünschten Änderungen an der Codedatei vor. Die Änderungen werden beim Eingeben automatisch gespeichert.

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Android-Client-Schnellstart]: app-service-mobile-android-get-started.md
[Apache Cordova-Client-Schnellstart]: app-service-mobile-cordova-get-started.md
[iOS-Client-Schnellstart]: app-service-mobile-ios-get-started.md
[Xamarin.iOS-Client-Schnellstart]: app-service-mobile-xamarin-ios-get-started.md
[Xamarin.Android-Client-Schnellstart]: app-service-mobile-xamarin-android-get-started.md
[Xamarin.Forms-Client-Schnellstart]: app-service-mobile-xamarin-forms-get-started.md
[Windows Store-Client-Schnellstart]: app-service-mobile-windows-store-dotnet-get-started.md
[Synchronisierung von Offlinedaten]: app-service-mobile-offline-data-sync.md
[Konfigurieren der Azure Active Directory-Authentifizierung]: ../app-service/configure-authentication-provider-aad.md
[Konfigurieren der Facebook-Authentifizierung]: ../app-service/configure-authentication-provider-facebook.md
[Konfigurieren der Google-Authentifizierung]: ../app-service/configure-authentication-provider-google.md
[Konfigurieren der Microsoft-Authentifizierung]: ../app-service/configure-authentication-provider-microsoft.md
[Konfigurieren der Twitter-Authentifizierung]: ../app-service/configure-authentication-provider-twitter.md
[Azure App Service-Bereitstellungshandbuch]: ../app-service/deploy-local-git.md
[Überwachen von Azure App Service]: ../app-service/web-sites-monitor.md
[Aktivieren der Diagnoseprotokollierung in Azure App Service]: ../app-service/troubleshoot-diagnostic-logs.md
[Problembehandlung für Azure App Service in Visual Studio]: ../app-service/troubleshoot-dotnet-visual-studio.md
[Angeben der Node.js-Version]: ../nodejs-specify-node-version-azure-apps.md
[Verwenden von Node.js-Modulen]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: https://expressjs.com/
[Swagger]: https://swagger.io/

[Azure-Portal]: https://portal.azure.com/
[OData]: https://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp-Beispiel auf GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo-Beispiel auf GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[Verzeichnis mit den Beispielen auf GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js-Tools 1.1 für Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql-Node.js-Paket]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: https://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS-Middleware]: https://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
