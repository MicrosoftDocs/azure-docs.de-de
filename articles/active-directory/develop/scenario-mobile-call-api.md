---
title: Mobile App, die Web-APIs aufruft (Aufrufen einer Web-API) | Microsoft Identity Platform
description: Hier finden Sie Informationen zum Erstellen einer mobilen App, die Web-APIs aufruft (Aufrufen einer Web-API).
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44b6c203583a082228c2ba1f4c5f6fdb04d059be
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962390"
---
# <a name="mobile-app-that-calls-web-apis---call-a-web-api"></a>Mobile App, die Web-APIs aufruft – Aufrufen einer Web-API

Nachdem Ihre App einen Benutzer angemeldet und Token erhalten hat, macht MSAL mehrere Informationen über den Benutzer, die Umgebung des Benutzers und die ausgestellten Token verfügbar. Ihre App kann diese Werte verwenden, um eine Web-API aufzurufen oder dem Benutzer eine Willkommensnachricht anzuzeigen.

Zuerst sehen wir uns das MSAL-Ergebnis an. Dann befassen wir uns damit, wie ein Zugriffstoken von `AuthenticationResult` oder `result` zum Aufrufen einer geschützten Web-API verwendet wird.

## <a name="msal-result"></a>MSAL-Ergebnis
MSAL stellt die folgenden Werte bereit: 

- `AccessToken`: Wird zum Aufrufen geschützter Web-APIs in einer HTTP-Beareranforderung verwendet.
- `IdToken`: Enthält nützliche Informationen über den angemeldeten Benutzer, wie den Namen des Benutzers, den Basismandanten und einen eindeutigen Bezeichner für den Speicher.
- `ExpiresOn`: Die Ablaufzeit des Tokens. MSAL übernimmt die automatische Aktualisierung für Apps.
- `TenantId`: Der Bezeichner des Mandanten, mit dem sich der Benutzer angemeldet hat. Bei Gastbenutzern (Azure Active Directory B2B) identifiziert dieser Wert den Mandanten, mit dem sich der Benutzer angemeldet hat, und nicht den Basismandanten des Benutzers.  
- `Scopes`: Die Bereiche, die mit Ihrem Token erteilt wurden. Bei den gewährten Bereichen kann es sich um eine Teilmenge der von Ihnen angeforderten Bereiche handeln.

MSAL stellt auch eine Abstraktion für ein `Account` bereit. Ein `Account` stellt das Konto dar, mit dem der aktuelle Benutzer angemeldet ist.

- `HomeAccountIdentifier`: Der Bezeichner des Basismandanten des Benutzers.
- `UserName`: Der bevorzugte Benutzername des Benutzers. Bei Azure Active Directory B2C-Benutzern kann dieser Wert leer sein.
- `AccountIdentifier`: Der Bezeichner des angemeldeten Benutzers. In den meisten Fällen stimmt dieser Wert mit dem Wert von `HomeAccountIdentifier` überein, sofern der Benutzer kein Gast in einem anderen Mandanten ist.

## <a name="call-an-api"></a>Aufrufen einer API

Wenn Sie über das Zugriffstoken verfügen, ist es einfach, eine Web-API aufzurufen. Ihre App verwendet das Token, um eine HTTP-Anforderung zu erstellen und die Anforderung dann auszuführen.

### <a name="android"></a>Android

```Java
        RequestQueue queue = Volley.newRequestQueue(this);
        JSONObject parameters = new JSONObject();

        try {
            parameters.put("key", "value");
        } catch (Exception e) {
            // Error when constructing.
        }
        JsonObjectRequest request = new JsonObjectRequest(Request.Method.GET, MSGRAPH_URL,
                parameters,new Response.Listener<JSONObject>() {
            @Override
            public void onResponse(JSONObject response) {
                // Successfully called Graph. Process data and send to UI.
            }
        }, new Response.ErrorListener() {
            @Override
            public void onErrorResponse(VolleyError error) {
                // Error.
            }
        }) {
            @Override
            public Map<String, String> getHeaders() throws AuthFailureError {
                Map<String, String> headers = new HashMap<>();
                
                // Put access token in HTTP request.
                headers.put("Authorization", "Bearer " + authResult.getAccessToken());
                return headers;
            }
        };

        request.setRetryPolicy(new DefaultRetryPolicy(
                3000,
                DefaultRetryPolicy.DEFAULT_MAX_RETRIES,
                DefaultRetryPolicy.DEFAULT_BACKOFF_MULT));
        queue.add(request);
```

### <a name="ios"></a>iOS

```swift
        let url = URL(string: kGraphURI)
        var request = URLRequest(url: url!)

        // Put access token in HTTP request.
        request.setValue("Bearer \(self.accessToken)", forHTTPHeaderField: "Authorization")

        URLSession.shared.dataTask(with: request) { data, response, error in
            if let error = error {
                self.updateLogging(text: "Couldn't get graph result: \(error)")
                return
            }
            guard let result = try? JSONSerialization.jsonObject(with: data!, options: []) else {
                self.updateLogging(text: "Couldn't deserialize result JSON")
                return
            }

            // Successfully got data from Graph.
            self.updateLogging(text: "Result from Graph: \(result))")
        }.resume()
```

### <a name="xamarin"></a>Xamarin

```CSharp
httpClient = new HttpClient();

// Put access token in HTTP request.
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call Graph.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```

## <a name="making-several-api-requests"></a>Erstellen mehrerer API-Anforderungen

Wenn Sie die gleiche API mehrmals oder mehrere APIs aufrufen müssen, berücksichtigen Sie beim Erstellen Ihrer App Folgendes:

- **Inkrementelle Zustimmung**: Microsoft Identity Platform ermöglicht Apps, Benutzereinwilligungen bei Bedarf einzuholen, wenn Berechtigungen erforderlich sind, statt alle direkt am Anfang einzuholen. Jedes Mal, wenn Ihre App für den Aufruf einer API bereit ist, sollte sie nur die Bereiche anfordern, die verwendet werden müssen.
- **Bedingter Zugriff**: In bestimmten Szenarien erhalten Sie möglicherweise weitere Anforderungen für bedingten Zugriff, wenn Sie mehrere API-Anforderungen ausgeben. Dies kann vorkommen, wenn für die erste Anforderung keine Richtlinien für bedingten Zugriff gelten und Ihre App versucht, automatisch auf eine neue API zuzugreifen, die bedingten Zugriff erfordert. Stellen Sie für den Umgang mit diesem Szenario sicher, dass Sie Fehler bei automatischen Anforderungen erkennen, und bereiten Sie sich auf eine interaktive Anforderung vor.  Weitere Informationen finden Sie unter [Anleitung für Entwickler zum bedingten Zugriff mit Azure Active Directory](conditional-access-dev-guide.md).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Übergang in die Produktion](scenario-mobile-production.md)
