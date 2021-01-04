---
title: Erste Schritte mit dem Web-Kartensteuerelement in Microsoft Azure Maps
description: Lernen Sie, wie Sie Web- und mobilen Anwendungen mithilfe der clientseitigen Kartensteuerelement-JavaScript-Bibliothek in Azure Maps Karten hinzufügen. Erfahren Sie, wie Sie Karten lokalisieren.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/20/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: db7006c01f59e8a11872d99b3a16f24f65ef7588
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94413376"
---
# <a name="use-the-azure-maps-map-control"></a>Verwenden des Azure Maps-Kartensteuerelements

Mit der clientseitigen JavaScript-Bibliothek des Kartensteuerelements können Sie Karten und eingebettete Azure Maps-Funktionen in Ihrer Web- oder Mobilanwendung rendern.

## <a name="prerequisites"></a>Voraussetzungen

Zur Verwendung des Kartensteuerelements auf einer Webseite müssen Sie eine der folgenden Voraussetzungen erfüllen:

* [Erstellen eines Azure Maps-Kontos](quick-demo-map-app.md#create-an-azure-maps-account) und [Abrufen eines Primärschlüssels](quick-demo-map-app.md#get-the-primary-key-for-your-account) (auch primärer Schlüssel oder Abonnementschlüssel genannt)

* Abrufen Ihrer Azure Active Directory-Anmeldeinformationen (AAD) mit [Authentifizierungsoptionen](/javascript/api/azure-maps-control/atlas.authenticationoptions)

## <a name="create-a-new-map-in-a-web-page"></a>Erstellen einer neuen Karte auf einer Webseite

Sie können eine Karte in eine Webseite einbetten, indem Sie die clientseitige JavaScript-Bibliothek des Kartensteuerelements verwenden.

1. Erstellen Sie eine neue HTML-Datei.

2. Laden Sie das Azure Maps Web SDK. Sie können eine dieser beiden Optionen wählen:

    * Verwenden Sie die global gehostete CDN-Version des Azure Maps Web SDK, indem Sie Verweise auf JavaScript und Stylesheet dem Element `<head>` der HTML-Datei hinzufügen:

      ```html
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
      <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
      ```

    * Laden Sie den Quellcode des Azure Maps Web SDK lokal mithilfe des NPM-Pakets [azure-maps-control](https://www.npmjs.com/package/azure-maps-control), und hosten Sie es mit Ihrer App. Dieses Paket enthält außerdem TypeScript-Definitionen.

      > **npm install azure-maps-control**

    Fügen Sie dem `<head>`-Element der Datei Verweise auf das Azure Maps-Stylesheet hinzu:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
     ```

    > [!NOTE]
    > Sie können TypeScript-Definitionen in Ihre Anwendung importieren, indem Sie den folgenden Code hinzufügen:
    >
    > ```javascript
    > import * as atlas from 'azure-maps-control';
    > ```

3. Um die Karte so zu rendern, dass sie den gesamten Bereich der Seite ausfüllt, fügen Sie dem `<head>`-Element das folgende `<style>`-Element hinzu.

   ```HTML
    <style>
        html, body {
            margin: 0;
        }

        #myMap {
            height: 100vh;
            width: 100vw;
        }
    </style>
   ```

4. Fügen Sie im Seitenbereich ein `<div>`-Element hinzu, und geben Sie ihm die `id`**myMap**.

   ```HTML
    <body onload="InitMap()">
        <div id="myMap"></div>
    </body>
   ```

5. Dann wird das Kartensteuerelement initialisiert. Zur Authentifizierung des Steuerelements müssen Sie entweder Besitzer eines Azure Maps-Abonnementschlüssels sein oder Azure Active Directory-Anmeldeinformationen (AAD) mit [Authentifizierungsoptionen](/javascript/api/azure-maps-control/atlas.authenticationoptions) nutzen.

    Wenn Sie einen Abonnementschlüssel für die Authentifizierung nutzen, kopieren Sie das folgende Skriptelement und fügen Sie es innerhalb des `<head>`-Elements und unterhalb des ersten `<script>`-Elements ein. Ersetzen Sie `<Your Azure Maps Key>` durch Ihren primären Abonnementschlüssel für Azure Maps.

     ```HTML
    <script type="text/javascript">
        function InitMap()
        {
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });
       }
    </script>
    ```

    Wenn Sie Azure Active Directory (AAD) für die Authentifizierung verwenden, kopieren Sie das folgende Skriptelement, und fügen Sie es innerhalb des `<head>`-Elements und unterhalb des ersten `<script>`-Elements ein.

      ```HTML
    <script type="text/javascript">
        function InitMap()
        {
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'aad',
                    clientId: '<Your AAD Client Id>',
                    aadAppId: '<Your AAD App Id>',
                    aadTenant: '<Your AAD Tenant Id>'
                }
            });
        }
    </script>
   ```

    Weitere Informationen zur Authentifizierung mit Azure Maps finden Sie im Dokument [Authentifizierung mit Azure Maps](azure-maps-authentication.md). Eine Liste mit Beispielen zur Integration von Azure Active Directory (AAD) mit Azure Maps finden Sie außerdem [hier](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples).

    >[!TIP]
    >In diesem Beispiel wurde der Wert `id` der Karte `<div>` übergeben. Eine andere Möglichkeit, dies zu tun, ist es, das `HTMLElement`-Objekt zu übergeben, indem `document.getElementById('myMap')` als erster Parameter übergeben wird.

6. Optional können Sie dem `head`-Element auf dieser Seite auch die folgenden `meta`-Elemente hinzufügen:

   ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   ```

7. Im Ergebnis sollte Ihre HTML-Datei ungefähr wie der folgende Markup-Code aussehen:

   ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>


        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            function InitMap()
            {
                var map = new atlas.Map('myMap', {
                    center: [-122.33, 47.6],
                    zoom: 12,
                    language: 'en-US',
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });
            }
        </script>

        <style>
            html, body {
                margin: 0;
            }

            #myMap {
                height: 100vh;
                width: 100vw;
            }
        </style>
    </head>
    <body onload="InitMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

8. Öffnen Sie die Datei in Ihrem Webbrowser, und zeigen Sie die gerenderte Karte an. Sie sollte wie die folgende Abbildung aussehen:

   ![Abbildung der Karte mit gerendertem Ergebnis](./media/how-to-use-map-control/map-of-seattle.png)

## <a name="localizing-the-map"></a>Lokalisieren der Karte

Azure Maps bietet zwei verschiedene Möglichkeiten zum Festlegen der Sprache und der regionalen Ansicht für die gerenderte Karte. Die erste Option besteht darin, diese Informationen dem globalen Namespace `atlas` hinzuzufügen. Dies führt dazu, dass alle Kartensteuerelementinstanzen in Ihrer App diese Einstellungen als Standard verwenden. Im Folgenden wird die Sprache auf Französisch („fr-FR“) und die regionale Ansicht auf „Auto“ festgelegt:

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('Auto');
```

Die zweite Option besteht darin, diese Informationen beim Laden der Karte folgendermaßen an die Kartenoptionen zu übergeben:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'Auto',

    authOptions: {
        authType: 'aad',
        clientId: '<Your AAD Client Id>',
        aadAppId: '<Your AAD App Id>',
        aadTenant: '<Your AAD Tenant Id>'
    }
});
```

> [!NOTE]
> Es ist möglich, auf der gleichen Seite mehrere Karteninstanzen mit unterschiedlichen Sprach- und Regionseinstellungen zu laden. Darüber hinaus können diese Einstellungen mithilfe der Kartenfunktion `setStyle` aktualisiert werden, nachdem die Karte geladen wurde.

Hier ist ein Beispiel für Azure Maps, bei dem die Sprache auf „fr-FR“ und die regionale Ansicht auf „Auto“ festgelegt wurde.

![Kartenbild mit Beschriftungen in Französisch](./media/how-to-use-map-control/websdk-localization.png)

Eine vollständige Liste der unterstützten Sprachen und regionalen Ansichten ist [hier](supported-languages.md) dokumentiert.

## <a name="azure-government-cloud-support"></a>Unterstützung für die Azure Government-Cloud

Das Azure Maps Web SDK unterstützt die Azure Government-Cloud. Alle JavaScript- und CSS-URLs, die für den Zugriff auf das Azure Maps Web SDK verwendet werden, bleiben unverändert. Die folgenden Aufgaben müssen ausgeführt werden, um eine Verbindung mit der Azure Government-Cloudversion der Azure Maps-Plattform herzustellen.

Wenn Sie das interaktive Kartensteuerelement verwenden, fügen Sie die folgende Codezeile hinzu, bevor Sie eine Instanz der `Map`-Klasse erstellen.

```javascript
atlas.setDomain('atlas.azure.us');
```

Verwenden Sie beim Authentifizieren der Karte und Dienste unbedingt die Azure Maps-Authentifizierungsinformationen der Azure Government-Cloudplattform.

Wenn Sie das Services-Modul verwenden, muss die Domäne für die Dienste beim Erstellen einer Instanz eines API-URL-Endpunkts festgelegt werden. Mit dem folgenden Code wird z. B. eine Instanz der `SearchURL`-Klasse erstellt und mit der Domäne auf die Azure Government-Cloud verwiesen.

```javascript
var searchURL = new atlas.service.SearchURL(pipeline, 'atlas.azure.us');
```

Wenn Sie direkt auf die Azure Maps-REST-Dienste zugreifen, ändern Sie die URL-Domäne in `atlas.azure.us`. Wenn Sie z. B. den Such-API-Dienst verwenden, ändern Sie die URL-Domäne von `https://atlas.microsoft.com/search/` in `https://atlas.azure.us/search/`.

## <a name="javascript-frameworks"></a>JavaScript-Frameworks

Bei Entwicklungen mit einem JavaScript-Framework kann eins der folgenden Open-Source-Projekte nützlich sein:

- [ng-azure-maps](https://github.com/arnaudleclerc/ng-azure-maps): Angular 10-Wrapper zur Umschließung von Azure-Karten.
- [AzureMapsControl.Components](https://github.com/arnaudleclerc/AzureMapsControl.Components): Eine Blazor-Komponente für Azure Maps.
- [Azure Maps React Component](https://github.com/WiredSolutions/react-azure-maps): Ein React-Wrapper für das Azure Maps-Steuerelement.
- [Vue Azure Maps](https://github.com/rickyruiz/vue-azure-maps): Eine Azure Maps-Komponente für die Vue-Anwendung.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie Karten erstellen und mit ihnen interagieren:

> [!div class="nextstepaction"]
> [Erstellen einer Karte](map-create.md)

Erfahren Sie, wie Sie den Stil einer Karte auswählen:

> [!div class="nextstepaction"]
> [Auswählen eines Kartenstils](choose-map-style.md)

So fügen Sie Ihrer Karte weitere Daten hinzu:

> [!div class="nextstepaction"]
> [Erstellen einer Karte](map-create.md)

> [!div class="nextstepaction"]
> [Codebeispiele](/samples/browse/?products=azure-maps)

Eine Liste mit Beispielen zur Integration von Azure Active Directory (AAD) in Azure Maps finden Sie hier:

> [!div class="nextstepaction"]
> [Beispiele für die Azure AD-Authentifizierung](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)