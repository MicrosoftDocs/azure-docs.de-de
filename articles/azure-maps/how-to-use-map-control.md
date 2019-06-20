---
title: Erste Schritte mit dem Web-Kartensteuerelement in Azure Maps | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie die clientseitige JavaScript-Bibliothek des Azure Maps-Kartensteuerelements verwenden.
author: walsehgal
ms.author: v-musehg
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: aa923fa7e2d5e673e6a2db2b349e54d433d1817b
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65957269"
---
# <a name="use-the-azure-maps-map-control"></a>Verwenden des Azure Maps-Kartensteuerelements

Mit der clientseitigen JavaScript-Bibliothek des Kartensteuerelements können Sie Karten und eingebettete Azure Maps-Funktionen in Ihrer Web- oder Mobilanwendung rendern.

## <a name="create-a-new-map-in-a-web-page"></a>Erstellen einer neuen Karte auf einer Webseite

Sie können eine Karte in eine Webseite einbetten, indem Sie die clientseitige JavaScript-Bibliothek des Kartensteuerelements verwenden.

1. Erstellen Sie eine neue HTML-Datei.

2. Laden Sie das Azure Maps Web SDK. Dies kann auf einem von zwei Wegen erfolgen:

    a. Verwenden Sie die global gehostete CDN-Version des Azure Maps Web SDK, indem Sie dem Stylesheet die URL-Endpunkte und Skriptverweise im `<head>`-Element der Datei hinzufügen:

    ```HTML
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    ```

    b. Laden Sie alternativ den Quellcode des Azure Maps Web SDK lokal mithilfe des NPM-Pakets [azure-maps-control](https://www.npmjs.com/package/azure-maps-control), und hosten Sie es zusammen mit Ihrer App. Dieses Paket enthält außerdem TypeScript-Definitionen.

    > npm install azure-maps-control

    Fügen Sie dann dem `<head>`-Element der Datei Verweise auf das Azure Maps-Stylesheet und die Skriptquelle hinzu:

    ```HTML
    <link rel="stylesheet" href="node_modules/azure-maps-control/dist/css/atlas.min.css" type="text/css">
    <script src="node_modules/azure-maps-control/dist/js/atlas.min.js"></script>
    ```

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

4. Fügen Sie im Seitenbereich ein `<div>`-Element hinzu, und geben Sie ihm die `id` **myMap**.

    ```HTML
    <body>
        <div id="myMap"></div>
    </body>
    ```

5. Definieren Sie zum Initialisieren des Kartensteuerelements im HTML-Text einen neuen Abschnitt, und erstellen Sie ein Skript. Übergeben Sie beim Erstellen einer Instanz der `Map`-Klasse die `id` der Karte `<div>` oder ein `HTMLElement` (z.B. `document.getElementById('myMap')`) als ersten Parameter. Verwenden Sie Ihren eigenen Azure Maps-Kontoschlüssel oder Ihre Azure Active Directory-Anmeldeinformationen (AAD), um die Karte mithilfe der [Authentifizierungsoptionen](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.authenticationoptions) zu authentifizieren. Wenn Sie ein Konto erstellen oder Ihren Schlüssel ermitteln müssen, lesen Sie die Informationen unter [Verwalten Ihres Azure Maps-Kontos und der dazugehörigen Schlüssel](how-to-manage-account-keys.md). Die Option**language** gibt die Sprache an, die für Kartenbeschriftungen und Steuerelemente verwendet werden soll. Weitere Informationen zu unterstützten Sprachen finden Sie unter [Unterstützte Sprachen](supported-languages.md). Falls ein Abonnementschlüssel für die Authentifizierung verwendet wird:

    ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'subscriptionKey',
                subscriptionKey: '<Your Azure Maps Key>'
            }
        });
    </script>
    ```

    Falls Azure Active Directory (AAD) für die Authentifizierung verwendet wird:

    ```HTML
    <script type="text/javascript">
        var map = new atlas.Map('myMap', {
            center: [-122.33, 47.6],
            zoom: 12,
            language: 'en-US',
            authOptions: {
                authType: 'aad',
                clientId: '<Your AAD Client Id>',
                aadAppId: '<Your AAD App Id>',
                aadTenant: 'msft.ccsctp.net'
            }
        });
    </script>
    ```

    Weitere Informationen finden Sie unter [Authentifizierung mit Azure Maps](azure-maps-authentication.md).

6. Es kann sinnvoll sein, dem Kopf der Seite die folgenden optionalen Metatagelemente hinzuzufügen:

    ```HTML
    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    ```

7. Im Ergebnis sollte Ihre HTML-Datei ungefähr wie der folgende Code aussehen:

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
    <body>
        <div id="myMap"></div>

        <script type="text/javascript">
            //Create an instance of the map control and set some options.
            var map = new atlas.Map('myMap', {
                center: [-122.33, 47.6],
                zoom: 12,
                language: 'en-US',
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });
        </script>
    </body>
    </html>
    ```

8. Öffnen Sie die Datei in Ihrem Webbrowser, und zeigen Sie die gerenderte Karte an. Es sollte in etwa wie der folgende Code aussehen:

    <iframe height="700" style="width: 100%;" scrolling="no" title="Verwenden des Kartensteuerelements" src="//codepen.io/azuremaps/embed/yZpEYL/?height=557&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">Weitere Informationen finden Sie unter dem Pen <a href='https://codepen.io/azuremaps/pen/yZpEYL/'>Verwenden des Kartensteuerelements</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) bei <a href='https://codepen.io'>CodePen</a>.
    </iframe>

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie Karten erstellen und mit ihnen interagieren:

> [!div class="nextstepaction"]
> [Erstellen einer Karte](map-create.md)

Erfahren Sie, wie Sie den Stil einer Karte auswählen:

> [!div class="nextstepaction"]
> [Auswählen eines Kartenstils](choose-map-style.md)
