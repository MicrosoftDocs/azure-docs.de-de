---
title: Verwenden von AppAuth in einer iOS-Anwendung
titleSuffix: Azure AD B2C
description: Informationen zum Erstellen einer iOS-App, die AppAuth mit Azure Active Directory B2C zum Verwalten von Benutzeridentitäten und Authentifizieren von Benutzern verwendet.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6064bd2c62922abea44508b8bf6cdfa3e7ecbc92
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953303"
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Azure AD B2C: Anmelden mit einer iOS-Anwendung

Die Microsoft Identity-Plattform nutzt offene Standards, z.B. OAuth2 und OpenID Connect. Mit einem offenen Protokoll profitieren Entwickler von einer größeren Auswahl an Bibliotheken für die Integration unserer Dienste. Wir haben diese und weitere exemplarische Vorgehensweisen bereitgestellt, um Entwickler beim Schreiben von Anwendungen zu unterstützen, die mit der Microsoft-Identitätsplattform verbunden sind. Die meisten Bibliotheken, die die [RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749)-Spezifikation implementieren, können eine Verbindung mit der Microsoft Identity-Plattform herstellen.

> [!WARNING]
> Microsoft stellt keine Fehlerbehebungen für diese Drittanbieterbibliotheken bereit und hat diese Bibliotheken keinerlei Prüfungen unterzogen. In diesem Beispiel wird eine Drittanbieterbibliothek namens AppAuth verwendet, deren Kompatibilität in grundlegenden Szenarien mit Azure AD B2C getestet wurde. Probleme und Funktionsanforderungen müssen direkt an das Open Source-Projekt der Bibliothek weitergeleitet werden. [hier finden Sie weitere Informationen](../active-directory/develop/reference-v2-libraries.md)
>
>

Wenn OAuth2 oder OpenID Connect neu für Sie ist, ergibt diese Beispielkonfiguration für Sie vielleicht noch nicht viel Sinn. Wir empfehlen Ihnen, sich eine kurze [Übersicht über das Protokoll](protocols-overview.md)anzusehen.

## <a name="get-an-azure-ad-b2c-directory"></a>Erstellen eines Azure AD B2C-Verzeichnisses
Bevor Sie Azure AD B2C verwenden können, müssen Sie ein Verzeichnis oder einen Mandanten erstellen. Ein Verzeichnis ist ein Container für alle Benutzer, Apps, Gruppen usw. [Erstellen Sie zunächst ein B2C-Verzeichnis](tutorial-create-tenant.md) , sofern noch keines vorhanden ist.

## <a name="create-an-application"></a>Erstellen einer Anwendung

Registrieren Sie als Nächstes eine Anwendung in Ihrem Azure AD B2C-Mandanten. Dadurch werden Azure AD die Informationen bereitgestellt, die für die sichere Kommunikation mit Ihrer App erforderlich sind.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Notieren Sie sich die **Anwendungs-ID (Client)** zur Verwendung in einem späteren Schritt.

Notieren Sie außerdem Ihren benutzerdefinierten Umleitungs-URI für die Verwendung in einem späteren Schritt. Beispiel: `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Erstellen Ihrer Benutzerflows
In Azure AD B2C wird jede Benutzeroberfläche durch einen [Benutzerflow](user-flow-overview.md) definiert. Diese Anwendung enthält eine kombinierte Identitätsoberfläche für die Anmeldung und Registrierung. Achten Sie beim Erstellen Ihres Benutzerflows auf Folgendes:

* Wählen Sie unter **Registrierungsattribute** das Attribut **Anzeigename** aus.  Sie können auch andere Attribute auswählen.
* Wählen Sie unter **Anwendungsansprüche** die Ansprüche **Anzeigename** und **Objekt-ID des Benutzers** aus. Sie können auch andere Ansprüche auswählen.
* Notieren Sie sich nach der Erstellung den **Namen** jedes Benutzerflows. Dem Namen Ihres Benutzerflows wird beim Speichern das Präfix `b2c_1_` vorangestellt.  Der Name des Benutzerflows wird später noch benötigt.

Nachdem Sie Ihre Benutzerflows erstellt haben, können Sie Ihre App erstellen.

## <a name="download-the-sample-code"></a>Herunterladen des Beispielcodes
Wir haben ein funktionsfähiges Beispiel bereitgestellt, das AppAuth mit Azure AD B2C [auf GitHub](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c) verwendet. Sie können den Code herunterladen und ausführen. Folgen Sie den Anweisungen in der Datei [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md), um Ihren eigenen Azure AD B2C-Mandanten zu verwenden.

Dieses Beispiel wurde anhand der README-Anweisungen vom [iOS AppAuth Projekt auf GitHub](https://github.com/openid/AppAuth-iOS) erstellt. Weitere Informationen zur Funktionsweise des Beispiels und der Bibliothek finden Sie in der AppAuth-README auf GitHub.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Ändern Ihrer Anwendung zur Verwendung von Azure AD B2C mit AppAuth

> [!NOTE]
> AppAuth unterstützt iOS 7 und höher.  Um Anmeldeinformationen für soziale Netzwerke auf Google zu unterstützen, benötigt SFSafariViewController jedoch iOS 9 oder höher.
>

### <a name="configuration"></a>Konfiguration

Sie können die Kommunikation mit Azure AD B2C konfigurieren, indem Sie die Autorisierungsendpunkt- und die Token-Endpunkt-URIs angeben.  Zum Generieren dieser URIs benötigen Sie die folgenden Informationen:
* Mandanten-ID (z.B. contoso.onmicrosoft.com)
* Name des Benutzerflows (z.B. B2C\_1\_SignUpIn)

Sie können die Token-Endpunkt-URI generieren, indem Sie Tenant\_ID (Mandanten-ID) und Policy\_Name (Richtlinienname) in der folgenden URL ersetzen:

```objc
static NSString *const tokenEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

Sie können die Autorisierungsendpunkt-URI generieren, indem Sie Tenant\_ID (Mandanten-ID) und Policy\_Name (Richtlinienname) in der folgenden URL ersetzen:

```objc
static NSString *const authorizationEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

Führen Sie den folgenden Code aus, um Ihr AuthorizationServiceConfiguration-Objekt zu erstellen:

```objc
OIDServiceConfiguration *configuration =
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>Autorisieren

Nach dem Konfigurieren oder Abrufen einer Autorisierungsdienstkonfiguration kann eine Autorisierungsanforderung erstellt werden. Zum Erstellen dieser Anforderung benötigen Sie die folgenden Informationen:

* Die Client-ID (ANWENDUNGS-ID), die Sie sich zuvor notiert haben. Beispiel: `00000000-0000-0000-0000-000000000000`.
* Den benutzerdefinierten Umleitungs-URI, den Sie sich zuvor notiert haben. Beispiel: `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

Sie sollten beide Elemente beim [Registrieren Ihrer App](#create-an-application) gespeichert haben.

```objc
OIDAuthorizationRequest *request =
    [[OIDAuthorizationRequest alloc] initWithConfiguration:configuration
                                                  clientId:kClientId
                                                    scopes:@[OIDScopeOpenID, OIDScopeProfile]
                                               redirectURL:[NSURL URLWithString:kRedirectUri]
                                              responseType:OIDResponseTypeCode
                                      additionalParameters:nil];

AppDelegate *appDelegate = (AppDelegate *)[UIApplication sharedApplication].delegate;
appDelegate.currentAuthorizationFlow =
    [OIDAuthState authStateByPresentingAuthorizationRequest:request
                                   presentingViewController:self
                                                   callback:^(OIDAuthState *_Nullable authState, NSError *_Nullable error) {
        if (authState) {
            NSLog(@"Got authorization tokens. Access token: %@", authState.lastTokenResponse.accessToken);
            [self setAuthState:authState];
        } else {
            NSLog(@"Authorization error: %@", [error localizedDescription]);
            [self setAuthState:nil];
        }
    }];
```

Um Ihre Anwendung zum Verarbeiten der Umleitung an den URI mit dem benutzerdefinierten Schema einzurichten, müssen Sie die Liste der URL-Schemas in der Datei „Info.plist“ aktualisieren:
* Öffnen Sie die Datei „Info.plist“.
* Zeigen Sie mit dem Mauszeiger auf eine Zeile wie „Bundle OS Type Code“, und klicken Sie auf das Symbol \+.
* Nennen Sie die neue Zeile „URL types“.
* Klicken Sie auf den Pfeil links neben „URL types“, um die Struktur zu öffnen.
* Klicken Sie auf den Pfeil links neben „Item 0“, um die Struktur zu öffnen.
* Benennen Sie das erste Element unterhalb von „Item 0“ in „URL Schemes“ um.
* Klicken Sie auf den Pfeil links neben „URL Schemes“, um die Struktur zu öffnen.
* In der Spalte „Value“ befindet sich links neben „Item 0“ unterhalb von „URL Schemes“ ein leeres Feld.  Legen Sie den Wert auf das eindeutige Schema Ihrer Anwendung fest.  Der Wert muss mit dem in der Umleitungs-URL beim Erstellen des OIDAuthorizationRequest-Objekts verwendeten Schema übereinstimmen.  Im Beispiel wird das Schema „com.onmicrosoft.fabrikamb2c.exampleapp“ verwendet.

Informationen zum restlichen Prozess finden Sie im [AppAuth Handbuch](https://openid.github.io/AppAuth-iOS/). Sehen Sie sich [das Beispiel](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c) für die ersten Schritte mit einer funktionsfähigen App an. Folgen Sie den Anweisungen in der Datei [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) aus, um Ihre eigene Azure AD B2C-Konfiguration einzugeben.

Wir sind stets offen für Feedback und Vorschläge. Wenn Sie Probleme mit diesem Artikel oder Vorschläge zur Verbesserung dieses Inhalts haben, würden wir uns über Ihr Feedback unten auf der Seite freuen. Anforderungen neuer Features geben Sie bitte unter [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c) ein.