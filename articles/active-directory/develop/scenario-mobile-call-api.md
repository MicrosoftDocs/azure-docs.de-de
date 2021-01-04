---
title: Aufrufen einer Web-API aus einer mobilen App | Azure
titleSuffix: Microsoft identity platform
description: Hier erfahren Sie, wie Sie eine mobile App erstellen, die Web-APIs aufruft. (Aufrufen einer Web-API)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: d7cad0592c5c4c0487f582ce5405c275b94b7bd0
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94444026"
---
# <a name="call-a-web-api-from-a-mobile-app"></a>Aufrufen einer Web-API aus einer mobilen App

Nachdem Ihre App einen Benutzer angemeldet und Token erhalten hat, macht die Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL) mehrere Informationen über den Benutzer, die Umgebung des Benutzers und die ausgestellten Token verfügbar. Ihre App kann diese Werte verwenden, um eine Web-API aufzurufen oder dem Benutzer eine Willkommensnachricht anzuzeigen.

In diesem Artikel betrachten wir zuerst das MSAL-Ergebnis. Dann befassen wir uns damit, wie ein Zugriffstoken von `AuthenticationResult` oder `result` zum Aufrufen einer geschützten Web-API verwendet wird.

## <a name="msal-result"></a>MSAL-Ergebnis
MSAL stellt die folgenden Werte bereit: 

- `AccessToken` ruft geschützte Web-APIs in einer HTTP-Beareranforderung auf.
- `IdToken` enthält nützliche Informationen über den angemeldeten Benutzer. Diese Informationen beinhalten den Namen des Benutzers, den Basismandanten und einen eindeutigen Bezeichner für die Speicherung.
- `ExpiresOn` ist die Ablaufzeit des Tokens. MSAL übernimmt die automatische Aktualisierung einer App.
- `TenantId` ist der Bezeichner des Mandanten, bei dem der Benutzer angemeldet ist. Für Gastbenutzer in Azure Active Directory (Azure AD) B2B identifiziert dieser Wert den Mandanten, bei dem sich der Benutzer angemeldet hat. Der Wert identifiziert nicht den Basismandanten des Benutzers.  
- `Scopes` gibt die Bereiche an, die mit dem Token erteilt wurden. Bei den gewährten Bereichen kann es sich um eine Teilmenge der von Ihnen angeforderten Bereiche handeln.

MSAL stellt auch eine Abstraktion für einen `Account`-Wert bereit. Ein `Account`-Wert stellt das Konto dar, mit dem der aktuelle Benutzer angemeldet ist:

- `HomeAccountIdentifier` bezeichnet den Basismandanten des Benutzers.
- `UserName` ist der bevorzugte Benutzername des Benutzers. Bei Azure AD B2C-Benutzern kann dieser Wert leer sein.
- `AccountIdentifier` bezeichnet den angemeldeten Benutzer. In den meisten Fällen stimmt dieser Wert mit dem Wert `HomeAccountIdentifier` überein, es sei denn, der Benutzer ist Gast in einem anderen Mandanten.

## <a name="call-an-api"></a>Aufrufen einer API

Wenn Sie über das Zugriffstoken verfügen, können Sie eine Web-API aufrufen. Ihre App verwendet das Token, um eine HTTP-Anforderung zu generieren und die Anforderung dann auszuführen.

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

### <a name="msal-for-ios-and-macos"></a>MSAL für iOS und macOS

Diese Methoden zum Abrufen von Token geben ein `MSALResult`-Objekt zurück. `MSALResult` macht eine `accessToken`-Eigenschaft verfügbar. Sie können `accessToken` verwenden, um eine Web-API aufzurufen. Fügen Sie dem HTTP-Autorisierungsheader diese Eigenschaft hinzu, bevor der Aufruf zum Zugriff auf die geschützte Web-API erfolgt.

```objc
NSMutableURLRequest *urlRequest = [NSMutableURLRequest new];
urlRequest.URL = [NSURL URLWithString:"https://contoso.api.com"];
urlRequest.HTTPMethod = @"GET";
urlRequest.allHTTPHeaderFields = @{ @"Authorization" : [NSString stringWithFormat:@"Bearer %@", accessToken] };
        
NSURLSessionDataTask *task =
[[NSURLSession sharedSession] dataTaskWithRequest:urlRequest
     completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {}];
[task resume];
```

```swift
let urlRequest = NSMutableURLRequest()
urlRequest.url = URL(string: "https://contoso.api.com")!
urlRequest.httpMethod = "GET"
urlRequest.allHTTPHeaderFields = [ "Authorization" : "Bearer \(accessToken)" ]
     
let task = URLSession.shared.dataTask(with: urlRequest as URLRequest) { (data: Data?, response: URLResponse?, error: Error?) in }
task.resume()
```

### <a name="xamarin"></a>Xamarin

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

## <a name="make-several-api-requests"></a>Erstellen mehrerer API-Anforderungen

Wenn Sie die gleiche API mehrmals oder mehrere APIs aufrufen müssen, berücksichtigen Sie beim Erstellen Ihrer App Folgendes:

- **Inkrementelle Zustimmung**: Microsoft Identity Platform ermöglicht es Apps, Benutzereinwilligungen bei Bedarf einzuholen, wenn Berechtigungen erforderlich sind, statt alle direkt am Anfang einzuholen. Jedes Mal, wenn Ihre App für den Aufruf einer API bereit ist, sollte sie nur die Bereiche anfordern, die sie benötigt.

- **Bedingter Zugriff**: Wenn Sie mehrere API-Anforderungen ausgeben, müssen Sie in bestimmten Szenarien zusätzlichen Anforderungen für bedingten Zugriff genügen. Auf diese Weise können sich die Anforderungen erhöhen, wenn für die erste Anforderung keine bedingten Zugriffsrichtlinien gelten und Ihre App im Hintergrund versucht, auf eine neue API-zuzugreifen, die bedingten Zugriff erfordert. Stellen Sie zur Lösung dieses Problems sicher, dass Sie Fehler bei automatischen Anforderungen erkennen, und bereiten Sie sich auf eine interaktive Anforderung vor.  Weitere Informationen finden Sie unter [Anleitung für bedingten Zugriff](../azuread-dev/conditional-access-dev-guide.md).

## <a name="call-several-apis-by-using-incremental-consent-and-conditional-access"></a>Aufrufen mehrerer APIs mithilfe von inkrementeller Zustimmung und bedingtem Zugriff

Wenn Sie nach dem Abrufen eines Tokens für einen Benutzer für denselben Benutzer mehrere APIs abrufen müssen, können Sie es vermeiden, den Benutzer wiederholt nach den Anmeldeinformationen zu fragen, indem Sie bei den Folgeaufrufen `AcquireTokenSilent` zum Abrufen eines Tokens aufrufen.

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
```

Ein Interaktion ist in folgenden Fällen erforderlich:

- Der Benutzer hat der ersten API zugestimmt, muss nun aber weiteren Bereichen zustimmen. In diesem Fall verwenden Sie inkrementelle Zustimmung.
- Für die erste API ist keine [mehrstufige Authentifizierung](../authentication/concept-mfa-howitworks.md) erforderlich, für die nächste API wird diese jedoch benötigt.

```csharp
var result = await app.AcquireTokenXX("scopeApi1")
                      .ExecuteAsync();

try
{
 result = await app.AcquireTokenSilent("scopeApi2")
                  .ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
 result = await app.AcquireTokenInteractive("scopeApi2")
                  .WithClaims(ex.Claims)
                  .ExecuteAsync();
}
```

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [Überführen in die Produktion](scenario-mobile-production.md).
