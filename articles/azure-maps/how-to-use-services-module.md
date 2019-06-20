---
title: Verwenden des Moduls „Dienste“ – Azure Maps | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie das Modul „Dienste“ von Azure Maps verwenden.
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.openlocfilehash: b56191bc93a91f944bb313b4ab9ad602da17dcf0
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357649"
---
# <a name="use-the-azure-maps-services-module"></a>Verwenden des Moduls „Dienste“ von Azure Maps

Das Web SDK für Azure Maps stellt das *Modul „Dienste“* bereit. Dieses Modul ist eine Hilfsbibliothek, die die Verwendung der REST-Dienste von Azure Maps in Web- oder Node.js-Anwendungen vereinfacht, indem JavaScript oder TypeScript verwendet wird.

## <a name="use-the-services-module-in-a-webpage"></a>Verwenden des Moduls „Dienste“ auf einer Webseite

1. Erstellen Sie eine neue HTML-Datei.
1. Laden Sie das Modul „Dienste“ von Azure Maps. Hierzu stehen zwei Möglichkeiten zur Verfügung:
    - Verwenden Sie die global gehostete Azure Content Delivery Network-Version des Azure Maps-Moduls „Dienste“. Fügen Sie der Datei einen Skriptverweis zum `<head>`-Element hinzu:

        ```html
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas-service.min.js"></script>
        ```

    - Laden Sie alternativ den Quellcode des Web SDK von Azure Maps lokal mithilfe des npm-Pakets [azure-maps-rest](https://www.npmjs.com/package/azure-maps-rest), und hosten Sie es anschließend zusammen mit Ihrer App. Dieses Paket enthält außerdem TypeScript-Definitionen. Verwenden Sie diesen Befehl:
    
        > **npm install azure-maps-rest**
    
        Fügen Sie dann der Datei einen Skriptverweis zum `<head>`-Element hinzu:

         ```html
        <script src="node_modules/azure-maps-rest/dist/js/atlas-service.min.js"></script>
         ```

1. Erstellen Sie eine Authentifizierungspipeline. Sie müssen die Pipeline erstellen, bevor Sie einen URL-Clientendpunkt für den Dienst initialisieren können. Verwenden Sie Ihren eigenen Azure Maps-Kontoschlüssel oder Ihre Azure Active Directory-Anmeldeinformationen (Azure AD), um den Suchdienstclient von Azure Maps zu authentifizieren. In diesem Beispiel wird der URL-Client des Suchdiensts erstellt. 

    Falls Sie einen Abonnementschlüssel für die Authentifizierung verwenden:

    ```javascript
    // Get an Azure Maps key at https://azure.com/maps.
    var subscriptionKey = '<Your Azure Maps Key>';

    // Use SubscriptionKeyCredential with a subscription key.
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(subscriptionKey);

    // Use subscriptionKeyCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential, {
      retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);
    ```

    Falls Sie Azure AD für die Authentifizierung verwenden:

    ```javascript
    // Enter your Azure AD client ID.
    var clientId = "<Your Azure Active Directory Client Id>";

    // Use TokenCredential with OAuth token (Azure AD or Anonymous).
    var aadToken = await getAadToken();
    var tokenCredential = new atlas.service.TokenCredential(clientId, aadToken);

    // Create a repeating time-out that will renew the Azure AD token.
    // This time-out must be cleared when the TokenCredential object is no longer needed.
    // If the time-out is not cleared, the memory used by the TokenCredential will never be reclaimed.
    var renewToken = async () => {
    try {
      console.log("Renewing token");
      var token = await getAadToken();
      tokenCredential.token = token;
      tokenRenewalTimer = setTimeout(renewToken, getExpiration(token));
    } catch (error) {
      console.log("Caught error when renewing token");
      clearTimeout(tokenRenewalTimer);
      throw error;
    }
    }
    tokenRenewalTimer = setTimeout(renewToken, getExpiration(aadToken));

    // Use tokenCredential to create a pipeline.
    var pipeline = atlas.service.MapsURL.newPipeline(tokenCredential, {
    retryOptions: { maxTries: 4 } // Retry options
    });

    // Create an instance of the SearchURL client.
    var searchURL = new atlas.service.SearchURL(pipeline);

    function getAadToken() {
        // Use the signed-in auth context to get a token.
        return new Promise((resolve, reject) => {
            // The resource should always be https://atlas.microsoft.com/.
            const resource = "https://atlas.microsoft.com/";
            authContext.acquireToken(resource, (error, token) => {
                if (error) {
                    reject(error);
                } else {
                    resolve(token);
                }
            });
        })
    }

    function getExpiration(jwtToken) {
        // Decode the JSON Web Token (JWT) to get the expiration time stamp.
        const json = atob(jwtToken.split(".")[1]);
        const decode = JSON.parse(json);

        // Return the milliseconds remaining until the token must be renewed.
        // Reduce the time until renewal by 5 minutes to avoid using an expired token.
        // The exp property is the time stamp of the expiration, in seconds.
        const renewSkew = 300000;
        return (1000 * decode.exp) - Date.now() - renewSkew;
    }
    ```

    Weitere Informationen finden Sie unter [Authentifizierung mit Azure Maps](azure-maps-authentication.md).

1. Der folgende Code verwendet den neu erstellten URL-Client des Azure Search-Diensts, um eine Adresse mit einem Geocode zu versehen: "1 Microsoft Way, Redmond, WA". Der Code verwendet die `searchAddress`-Funktion und zeigt die Ergebnisse als Tabelle im Textkörper der Seite an.

    ```javascript
    // Search for "1 microsoft way, redmond, wa".
    searchURL.searchAddress(atlas.service.Aborter.timeout(10000), '1 microsoft way, redmond, wa').then(response => {
      var html = [];

      // Display the total results.
      html.push('Total results: ', response.summary.numResults, '<br/><br/>');

      // Create a table of the results.
      html.push('<table><tr><td></td><td>Result</td><td>Latitude</td><td>Longitude</td></tr>');

      for(var i=0;i<response.results.length;i++){
        html.push('<tr><td>', (i+1), '.</td><td>', 
          response.results[i].address.freeformAddress, 
          '</td><td>', 
          response.results[i].position.lat,
          '</td><td>', 
          response.results[i].position.lon,
          '</td></tr>');
      }

      html.push('</table>');

      // Add the resulting HTML to the body of the page.
      document.body.innerHTML = html.join('');
    });
    ```

    Es folgt das voll funktionsfähige Codebeispiel:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Verwenden des Moduls „Dienste“" src="//codepen.io/azuremaps/embed/zbXGMR/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Weitere Informationen finden Sie unter <a href='https://codepen.io/azuremaps/pen/zbXGMR/'>Verwenden des Moduls „Dienste“</a> von Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) auf <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den in diesem Artikel verwendeten Klassen und Methoden:

> [!div class="nextstepaction"]
> [MapsURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.mapsurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [SearchURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [RouteURL](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [SubscriptionKeyCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.subscriptionkeycredential?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [TokenCredential](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.tokencredential?view=azure-maps-typescript-latest)

In den folgenden Artikeln finden Sie weitere Codebeispiele, die das Modul „Dienste“ verwenden:

> [!div class="nextstepaction"]
> [Anzeigen von Suchergebnissen auf der Karte](./map-search-location.md)

> [!div class="nextstepaction"]
> [Abrufen von Informationen von einer Koordinate](./map-get-information-from-coordinate.md)

> [!div class="nextstepaction"]
> [Anzeigen einer Wegbeschreibung von A nach B](./map-route.md)