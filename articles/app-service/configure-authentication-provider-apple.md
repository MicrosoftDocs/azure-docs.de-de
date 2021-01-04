---
title: Konfigurieren von „Mit Apple anmelden“ (Vorschau)
description: Erfahren Sie, wie Sie „Mit Apple anmelden“ als Identitätsanbieter für Ihre App Service- oder Azure Functions-App konfigurieren.
ms.topic: article
ms.date: 11/19/2020
ms.reviewer: mikarmar
ms.openlocfilehash: b77e0613f502d003b5e4651e34be4cadbd4209a9
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602970"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-sign-in-using-a-sign-in-with-apple-provider-preview"></a>Konfigurieren Ihrer App Service- oder Azure Functions-App für die Anmeldung über einen „Mit Apple anmelden“-Anbieter (Vorschau)

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

In diesem Artikel wird beschrieben, wie Sie Azure App Service oder Azure Functions zur Verwendung von „Mit Apple anmelden“ als Authentifizierungsanbieter konfigurieren. 

Um die in diesem Artikel beschriebenen Schritte ausführen zu können, müssen Sie für das Apple Developer Program registriert sein. Wechseln Sie zu [developer.apple.com/programs/enroll](https://developer.apple.com/programs/enroll/), um sich für das Apple Developer Program zu registrieren.

> [!CAUTION]
> Durch das Aktivieren von „Mit Apple anmelden“ wird die Verwaltung des App Service-Features zur Authentifizierung/Autorisierung für Ihre Anwendung durch einige Clients wie das Azure-Portal, die Azure-Befehlszeilenschnittstelle und Azure PowerShell deaktiviert. Das Feature basiert auf einer neuen API-Oberfläche, die während der Vorschau noch nicht in allen Verwaltungsumgebungen implementiert ist.

[comment]: <Remove this caution block when V2 becomes available for use.> 

## <a name="create-an-application-in-the-apple-developer-portal"></a><a name="createApplication"> </a>Erstellen einer Anwendung im Apple Developer-Portal
Sie müssen eine App-ID und eine Dienst-ID im Apple Developer-Portal erstellen.

1. Navigieren Sie im Apple Developer-Portal zu **Certificates, Identifiers, & Profiles** (Zertifikate, IDs & Profile).
2. Wählen Sie auf der Registerkarte **Identifiers** (Bezeichner) die Schaltfläche **(+)** aus.
3. Wählen Sie auf der Seite **Register a New Identifier** (Neue ID registrieren) die Option **App IDs** (App-IDs) und dann **Continue** (Weiter) aus. (App-IDs enthalten mindestens eine Dienst-ID.) ![Registrieren einer neuen App-ID im Apple Developer-Portal](media/configure-authentication-provider-apple/apple-register-app.jpg)
4. Geben Sie auf der Seite **Register an App ID** (App-ID registrieren) eine Beschreibung und eine Bündel-ID an, und wählen Sie **Sign in with Apple** (Mit Apple anmelden) in der Liste der Funktionen aus. Klicken Sie anschließend auf **Weiter**. Notieren Sie sich das **App-ID-Präfix (Team-ID)** aus diesem Schritt. Sie benötigen es später.
![Konfigurieren einer neuen App-ID im Apple Developer-Portal](media/configure-authentication-provider-apple/apple-configure-app.jpg)
5. Überprüfen Sie die App-Registrierungsinformationen, und wählen Sie **Register** (Registrieren) aus.
6. Wählen Sie auf der Registerkarte **Identifiers** (Bezeichner) wieder die Schaltfläche **(+)** aus.
![Erstellen einer neuen Dienst-ID im Apple Developer-Portal](media/configure-authentication-provider-apple/apple-new-app.jpg) 
7. Wählen Sie auf der Seite **Register a New Identifier** (Neue ID registrieren) die Option **Services IDs** (Dienst-IDs) und dann **Continue** (Weiter) aus.
![Registrieren einer neuen Dienst-ID im Apple Developer-Portal](media/configure-authentication-provider-apple/apple-register-service.jpg)
8. Geben Sie auf der Seite **Register a Services ID** (Dienst-ID registrieren) eine Beschreibung und einen Bezeichner an. Die Beschreibung wird auf dem Genehmigungsbildschirm für den Benutzer angezeigt. Der Bezeichner ist die Client-ID, die beim Konfigurieren des Apple-Anbieters mit Ihrer App Service-Instanz verwendet wird. Klicken Sie anschließend auf **Konfigurieren**.
![Angeben einer Beschreibung und eines Bezeichners](media/configure-authentication-provider-apple/apple-configure-service-1.jpg)
9. Legen Sie im Popupfenster die primäre App-ID (Primary App ID) auf die App-ID fest, die Sie zuvor erstellt haben. Geben Sie im Bereich „Domains“ (Domänen) die Domäne Ihrer Anwendung an. Verwenden Sie als Rückgabe-URL die URL `<app-url>/.auth/login/apple/callback`. Beispiel: `https://contoso.azurewebsites.net/.auth/login/apple/callback`. Wählen Sie dann **Add** (Hinzufügen) und **Save** (Speichern) aus.
![Angeben der Domäne und der Rückgabe-URL für die Registrierung](media/configure-authentication-provider-apple/apple-configure-service-2.jpg)
10. Überprüfen Sie die Dienstregistrierungsinformationen, und wählen Sie **Save** (Speichern) aus.

## <a name="generate-the-client-secret"></a><a name="generateClientSecret"> </a>Generieren des geheimen Clientschlüssels
In Apple müssen App-Entwickler ein JWT-Token als geheimen Clientschlüssel erstellen und signieren. Zum Generieren dieses geheimen Clientschlüssels generieren und laden Sie zunächst einen privaten Elliptic Curve-Schlüssel über das Apple Developer-Portal herunter. Verwenden Sie diesen Schlüssel dann zum [Signieren eines JWT-Tokens](#sign-the-client-secret-jwt) mit [spezifischen Nutzdaten](#structure-the-client-secret-jwt).

### <a name="create-and-download-the-private-key"></a>Erstellen und Herunterladen des privaten Schlüssels
1. Wählen Sie im Apple Developer-Portal auf der Registerkarte **Keys** (Schlüssel) die Option **Create a key** (Schlüssel erstellen) oder die Schaltfläche **(+)** aus.
2. Geben Sie auf der Seite **Register a New Key** (Neuen Schlüssel registrieren) einen Namen für den Schlüssel an, aktivieren Sie das Kontrollkästchen neben **Sign in with Apple** (Mit Apple anmelden), und wählen Sie **Configure** (Konfigurieren) aus.
3. Verknüpfen Sie auf der Seite **Configure Key** (Schlüssel konfigurieren) den Schlüssel mit der zuvor erstellten primären App-ID, und wählen Sie **Save** (Speichern) aus.
4. Schließen Sie die Erstellung des Schlüssels ab, indem Sie die Informationen bestätigen und **Continue** (Weiter) auswählen. Überprüfen Sie dann die Informationen, und wählen Sie **Register** (Registrieren) aus.
5. Laden Sie auf der Seite **Download Your Key** (Ihren Schlüssel herunterladen) den Schlüssel herunter. Er wird als `.p8`-Datei (PKCS#8) heruntergeladen. Sie verwenden den Dateiinhalt, um das JWT-Token des geheimen Clientschlüssels zu signieren.

### <a name="structure-the-client-secret-jwt"></a>Strukturieren des JWT-Tokens des geheimen Clientschlüssels
In Apple muss der geheime Clientschlüssel die Base64-Codierung eines JWT-Tokens sein. Das decodierte JWT-Token muss Nutzdaten enthalten, die wie in diesem Beispiel strukturiert sind:
```json
{
  "alg": "ES256",
  "kid": "URKEYID001",
}.{
  "sub": "com.yourcompany.app1",
  "nbf": 1560203207,
  "exp": 1560289607,
  "iss": "ABC123DEFG",
  "aud": "https://appleid.apple.com"
}.[Signature]
```
- **sub:** die Apple-Client-ID (auch die Dienst-ID)
- **iss:** Ihre Apple Developer-Team-ID
- **aud:** Apple empfängt das Token, daher ist Apple die Zielgruppe.
- **exp**: nicht mehr als sechs Monate nach **nbf**

Die Base64-codierte Version der Nutzdaten oben sieht wie folgt aus: ```eyJhbGciOiJFUzI1NiIsImtpZCI6IlVSS0VZSUQwMDEifQ.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDIwMzIwNywiZXhwIjoxNTYwMjg5NjA3LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.ABSXELWuTbgqfrIUz7bLi6nXvkXAz5O8vt0jB2dSHTQTib1x1DSP4__4UrlKI-pdzNg1sgeocolPNTmDKazO8-BHAZCsdeeTNlgFEzBytIpMKFfVEQbEtGRkam5IeclUK7S9oOva4EK4jV4VmgDrr-LGWWO3TaAxAvy3_ZoKohvFFkVG```

_Hinweis: Apple akzeptiert keine JWT-Token für geheime Clientschlüssel mit einem Ablaufdatum von mehr als sechs Monaten nach dem Erstellungsdatum (oder nbf). Das bedeutet, dass Sie den geheimen Clientschlüssel mindestens alle sechs Monate rotieren müssen._

Weitere Informationen zum Generieren und Überprüfen von Token finden Sie in der [Entwicklerdokumentation von Apple](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens). 

### <a name="sign-the-client-secret-jwt"></a>Signieren des JWT-Tokens des geheimen Clientschlüssels
Verwenden Sie die `.p8`-Datei, die Sie zuvor heruntergeladen haben, um das JWT-Token des geheimen Clientschlüssels zu signieren. Bei dieser Datei handelt es sich um eine [PCKS#8-Datei](https://en.wikipedia.org/wiki/PKCS_8) die den privaten Signaturschlüssel im PEM-Format enthält. Das JWT-Token kann in vielen verfügbaren Bibliotheken erstellt und signiert werden. 

Es gibt verschiedene Arten von Open-Source-Bibliotheken, die online zum Erstellen und Signieren von JWT-Token zur Verfügung stehen. Weitere Informationen zum Generieren von JWT-Token finden Sie unter jwt.io. Eine Möglichkeit, den geheimen Clientschlüssel zu generieren, besteht beispielsweise darin, das NuGet-Paket [Microsoft.IdentityModel.Tokens](https://www.nuget.org/packages/Microsoft.IdentityModel.Tokens/) zu importieren und einen kleinen Teil des unten gezeigten C#-Codes auszuführen.

```csharp
using Microsoft.IdentityModel.Tokens;

public static string GetAppleClientSecret(string teamId, string clientId, string keyId, string p8key)
{
    string audience = "https://appleid.apple.com";

    string issuer = teamId;
    string subject = clientId;
    string kid = keyId;

    IList<Claim> claims = new List<Claim> {
        new Claim ("sub", subject)
    };

    CngKey cngKey = CngKey.Import(Convert.FromBase64String(p8key), CngKeyBlobFormat.Pkcs8PrivateBlob);

    SigningCredentials signingCred = new SigningCredentials(
        new ECDsaSecurityKey(new ECDsaCng(cngKey)),
        SecurityAlgorithms.EcdsaSha256
    );

    JwtSecurityToken token = new JwtSecurityToken(
        issuer,
        audience,
        claims,
        DateTime.Now,
        DateTime.Now.AddDays(180),
        signingCred
    );
    token.Header.Add("kid", kid);
    token.Header.Remove("typ");

    JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler();

    return tokenHandler.WriteToken(token);
}
```
- **teamId:** Ihre Apple Developer-Team-ID
- **clientId:** die Apple-Client-ID (auch die Dienst-ID)
- **p8key:** der Schlüssel im PEM-Format. Sie können den Schlüssel abrufen, indem Sie die `.p8`-Datei in einem Text-Editor öffnen und alles zwischen `-----BEGIN PRIVATE KEY-----` und `-----END PRIVATE KEY-----` ohne Zeilenumbrüche kopieren.
- **keyId:** die ID des heruntergeladenen Schlüssels

Dieses zurückgegebene Token verwenden Sie als geheimen Clientschlüssel zum Konfigurieren des Apple-Anbieters.

> [!IMPORTANT]
> Der geheime Clientschlüssel ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schlüssel mit niemandem, und geben Sie ihn nicht über Ihre Anwendung weiter.
>

Fügen Sie das Clientgeheimnis als [Anwendungseinstellung](./configure-common.md#configure-app-settings) für die App hinzu, und verwenden Sie dabei einen Einstellungsnamen Ihrer Wahl. Notieren Sie sich diesen Namen zur späteren Verwendung.

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>Hinzufügen von Anbieterinformationen zu Ihrer Anwendung

> [!NOTE]
> Die erforderliche Konfiguration ist in einem neuen API-Format verfügbar, das derzeit nur durch eine [dateibasierte Konfiguration (Vorschau)](.\app-service-authentication-how-to.md#config-file)unterstützt wird. Führen Sie die unten aufgeführten Schritte mithilfe einer solchen Datei aus.

Dieser Abschnitt führt Sie durch die Aktualisierung der Konfiguration für Ihren neuen IdP. Im Folgenden ist eine Beispielkonfiguration aufgeführt.

1. Fügen Sie innerhalb des `identityProviders`-Objekts ein `apple`-Objekt ein, sofern noch keines vorhanden ist.
2. Weisen Sie dem Schlüssel ein Objekt mit einem darin enthaltenen Objekt `registration` und optional ein Objekt `login` zu:
    
    ```json
    "apple" : {
       "registration" : {
            "clientId": "<client id>",
            "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_CLIENT_SECRET" 
        },
       "login": {
             "scopes": []
       }
    }
    ```
    a. Legen Sie im `registration`-Objekt `clientId` auf die abgerufene Client-ID fest.
    
    b. Legen Sie im `registration`-Objekt `clientSecretSettingName` auf den Namen der Anwendungseinstellung fest, in der Sie den geheimen Clientschlüssel gespeichert haben.
    
    c. Innerhalb des `login`-Objekts können Sie das `scopes`-Array so festlegen, dass es eine Liste der Bereiche enthält, die bei der Authentifizierung mit Apple verwendet werden, z. B. „name“ und „email“. Wenn Bereiche konfiguriert sind, werden sie auf dem Genehmigungsbildschirm explizit angefordert, wenn Benutzer sich zum ersten Mal anmelden.

Nachdem diese Konfiguration festgelegt wurde, können Sie den Apple-Anbieter für die Authentifizierung in Ihrer App verwenden.

Eine abgeschlossene Konfiguration sollte dem folgende Beispiel ähneln (die Einstellung APPLE_GENERATED_CLIENT_SECRET verweist auf eine Anwendungseinstellung mit einem generierten JWT-Token):

```json
{
    "platform": {
        "enabled": true
    },
    "globalValidation": {
        "redirectToProvider": "apple",
        "unauthenticatedClientAction": "RedirectToLoginPage"
    },
    "identityProviders": {
        "apple": {
            "registration": {
                "clientId": "com.contoso.example.client",
                "clientSecretSettingName": "APPLE_GENERATED_CLIENT_SECRET"
            },
            "login": {
                "scopes": []
            }
        }
    },
    "login": {
        "tokenStore": {
            "enabled": true
        }
    }     
}
```

## <a name="next-steps"></a><a name="related-content"> </a>Nächste Schritte

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
