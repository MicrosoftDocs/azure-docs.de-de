---
title: Verwenden von MSAL.js mit Azure AD B2C
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library für JavaScript (MSAL.js) ermöglicht es Anwendungen, mit Azure AD B2C zusammenzuarbeiten und Token zum Aufrufen gesicherter Web-APIs abzurufen. Bei diesen Web-APIs kann es sich um Microsoft Graph, andere Microsoft-APIs, Web-APIs von Dritten oder um Ihre eigene Web-API handeln.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/05/2020
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev devx-track-js
ms.openlocfilehash: ef1c0003978251dd2637915e56dc396e85f4438f
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97107871"
---
# <a name="use-microsoft-authentication-library-for-javascript-to-work-with-azure-ad-b2c"></a>Verwenden von Microsoft Authentication Library für JavaScript für die Zusammenarbeit mit Azure AD B2C

Mit [Microsoft Authentication Library für JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) können JavaScript-Entwickler Benutzern die Authentifizierung mit Social-Media- und lokalen Identitäten über [Azure Active Directory B2C (Azure AD B2C)](../../active-directory-b2c/overview.md) ermöglichen.

Wenn Sie Azure AD B2C als Identitätsverwaltungsdienst verwenden, können Sie anpassen und steuern, wie Kunden sich registrieren, anmelden und ihre Profile verwalten, wenn sie Ihre Anwendungen verwenden. Mit Azure AD B2C können Sie auch die Benutzeroberfläche, die von Ihrer Anwendung während des Authentifizierungsvorgangs angezeigt wird, mit Ihrer Marke versehen und anpassen.

In den folgenden Abschnitten wird die Vorgehensweise veranschaulicht:

- Schützen einer Node.js-Web-API
- Unterstützen der Anmeldung in einer Single-Page-Anwendung (SPA) und Aufrufen *dieser* geschützten Web-API
- Unterstützen der Kennwortzurücksetzung

## <a name="prerequisites"></a>Voraussetzungen

Erstellen Sie einen eigenen [Azure AD B2C-Mandanten](../../active-directory-b2c/tutorial-create-tenant.md), falls dies noch nicht geschehen ist.

## <a name="nodejs-web-api"></a>Node.js-Web-API

In den folgenden Schritten wird gezeigt, wie eine **Web-API** Azure AD B2C verwenden kann, um sich selbst zu schützen und ausgewählte Bereiche für eine Clientanwendung verfügbar zu machen.

MSAL.js für Node befindet sich aktuell in der Entwicklungsphase. Weitere Informationen finden Sie unter [Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap) auf GitHub. Aktuell empfehlen wir, [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) zu verwenden, eine Authentifizierungsbibliothek für Node.js, die von Microsoft entwickelt und unterstützt wird.

### <a name="step-1-register-your-application"></a>Schritt 1: Anwendung registrieren

Sie müssen Ihre Web-API erst registrieren, um sie mit Azure AD B2C schützen zu können. Ausführliche Schritte hierzu finden Sie unter [Registrieren Ihrer Anwendung](../../active-directory-b2c/add-web-api-application.md).

### <a name="step-2-download-the-sample-application"></a>Schritt 2: Herunterladen der Beispielanwendung

Laden Sie die Beispielanwendung als ZIP-Datei herunter, oder klonen Sie sie aus GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>Schritt 3: Konfigurieren der Authentifizierung

1. Öffnen Sie im Beispiel die Datei *config.json*.

2. Konfigurieren Sie das Beispiel mit den Anwendungsanmeldeinformationen, die Sie zuvor bei der Registrierung Ihrer Anwendung erhalten haben. Ändern Sie die folgenden Codezeilen. Ersetzen Sie dabei die Werte durch den Namen Ihres Mandanten, die Client-ID und den Richtliniennamen.

    ```json
         "credentials": {
             "tenantName": "<your-tenant-name>",
             "clientID": "<your-webapi-application-ID>"
         },
         "policies": {
             "policyName": "B2C_1_signupsignin1"
         },
         "resource": {
             "scope": ["demo.read"] 
         },
    ```

Weitere Informationen finden Sie in diesem [Beispiel für eine Node.js-B2C-Web-API](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi).

## <a name="javascript-spa"></a>JavaScript SPA

Die folgenden Schritte veranschaulichen, wie eine **Single-Page-Webanwendung** Azure AD B2C zum Registrieren und Anmelden sowie zum Aufrufen einer geschützten Web-API verwenden kann.

### <a name="step-1-register-your-application"></a>Schritt 1: Anwendung registrieren

Sie müssen zuerst Ihre Anwendung registrieren, um die Authentifizierung zu implementieren. Ausführliche Schritte hierzu finden Sie unter [Registrieren Ihrer Anwendung](../../active-directory-b2c/tutorial-register-applications.md).

### <a name="step-2-download-the-sample-application"></a>Schritt 2: Herunterladen der Beispielanwendung

Laden Sie das [ZIP-Archiv](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp/archive/master.zip) des Codebeispiels herunter, oder klonen Sie das GitHub-Repository:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Schritt 3: Konfigurieren der Authentifizierung

Es gibt zwei Punkte, die beim Konfigurieren Ihrer Anwendung von Interesse sind:

- Konfigurieren des API-Endpunkts und von verfügbar gemachten Bereichen
- Konfigurieren von Authentifizierungsparametern und Tokenbereichen

1. Öffnen Sie im Beispiel die Datei *apiConfig.js*.

2. Konfigurieren Sie das Beispiel mit den Parametern, die Sie zuvor bei der Registrierung Ihrer Web-API erhalten haben. Ändern Sie die folgenden Codezeilen, indem Sie die entsprechenden Werte durch die Adresse Ihrer Web-API und die verfügbar gemachten Bereiche ersetzen.

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };
   ```

1. Öffnen Sie im Beispiel die Datei *authConfig.js*.

1. Konfigurieren Sie das Beispiel mit den Parametern, die Sie zuvor bei der Registrierung Ihrer Single-Page-Webanwendung erhalten haben. Ändern Sie die folgenden Codezeilen, indem Sie die entsprechenden Werte durch Ihre Client-ID, Autoritätsmetadaten und Bereiche für die Tokenanforderung ersetzen.

   ```javascript
    // Config object to be passed to Msal on creation.
    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/B2C_1_signupsignin1",
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage", // This configures where your cache will be stored
            storeAuthStateInCookie: false // Set this to "true" to save cache in cookies
        }
    };

    // Add here scopes for id token to be used at the MS Identity Platform endpoint
    const loginRequest = {
        scopes: ["openid", "profile"],
    };
   ```

Weitere Informationen finden Sie in diesem [Beispiel für eine JavaScript-B2C-Single-Page-Webanwendung](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp).

## <a name="support-password-reset"></a>Unterstützen der Kennwortzurücksetzung

In diesem Abschnitt erweitern Sie Ihre Single-Page-Anwendung und verwenden den Azure AD B2C-Benutzerflow für die Kennwortzurücksetzung. Auch wenn MSAL.js derzeit mehrere Benutzerflows oder benutzerdefinierte Richtlinien nativ nicht unterstützt, können Sie die Bibliothek verwenden, um gängige Anwendungsfälle wie die Kennwortzurücksetzung zu verarbeiten.

In den folgenden Schritten wird davon ausgegangen, dass Sie bereits die Schritte im vorherigen Abschnitt [JavaScript SPA](#javascript-spa) ausgeführt haben.

### <a name="step-1-define-the-authority-string-for-password-reset-user-flow"></a>Schritt 1: Definieren der „authority“-Zeichenfolge für den Benutzerflow für die Kennwortzurücksetzung

1. Erstellen Sie zunächst ein Objekt, in dem Sie die URIs der autoritativen Stelle speichern:

    ```javascript
        const b2cPolicies = {
            names: {
                signUpSignIn: "b2c_1_susi",
                forgotPassword: "b2c_1_reset"
            },
            authorities: {
                signUpSignIn: {
                    authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi",
                },
                forgotPassword: {
                    authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_reset",
                },
            },
        }
    ```

1. Initialisieren Sie als nächstes das MSAL-Objekt mit der `signInSignUp`-Richtlinie als Standard (siehe obigen Codeausschnitt). Wenn ein Benutzer versucht, sich anzumelden, wird der folgende Bildschirm angezeigt:

    :::image type="content" source="media/msal-b2c-overview/user-journey-01-signin.png" alt-text="Von Azure AD B2C angezeigter Anmeldebildschirm":::

### <a name="step-2-catch-and-handle-authentication-errors-in-your-login-method"></a>Schritt 2: Erfassen und Behandeln von Authentifizierungsfehlern in Ihrer Anmeldemethode

Wenn ein Benutzer die Option **Kennwort vergessen** auswählt, gibt Ihre Anmeldung einen Fehler aus, den Sie in Ihrem Code erfassen und anschließend durch Präsentieren des entsprechenden Benutzerflows behandeln müssen. In diesem Fall ist das der Kennwortzurücksetzungsflow `b2c_1_reset`.

1. Erweitern Sie Ihre Anmeldemethode wie folgt:

    ```javascript
    function signIn() {
      myMSALObj.loginPopup(loginRequest)
        .then(loginResponse => {
            console.log("id_token acquired at: " + new Date().toString());

            if (myMSALObj.getAccount()) {
              updateUI();
            }

        }).catch(function (error) {
          console.log(error);

          // error handling
          if (error.errorMessage) {
            // check for forgot password error
            if (error.errorMessage.indexOf("AADB2C90118") > -1) {

              //call login method again with the password reset user flow
              myMSALObj.loginPopup(b2cPolicies.authorities.forgotPassword)
                .then(loginResponse => {
                  console.log(loginResponse);
                  window.alert("Password has been reset successfully. \nPlease sign-in with your new password.");
                })
            }
          }
        });
    }
    ```

1. Der vorherige Codeausschnitt zeigt, wie nach dem Erfassen des Fehler mit dem Code `AADB2C90118` der Bildschirm für die Kennwortzurücksetzung angezeigt wird.

    Nachdem der Benutzer sein Kennwort zurückgesetzt hat, wird er zurück zur Anwendung geleitet, um sich erneut anzumelden.

    :::image type="content" source="media/msal-b2c-overview/user-journey-02-password-reset.png" alt-text="Von Azure AD B2C während des Kennwortzurücksetzungsflows angezeigte Bildschirme" border="false":::

    Weitere Informationen zu Fehlercodes und Ausnahmebehandlungen finden Sie unter [MSAL-Fehler- und Ausnahmecodes](msal-error-handling-js.md).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr zu den folgenden Azure AD B2C-Konzepten:

- [Benutzerabläufe](../../active-directory-b2c/tutorial-create-user-flows.md)
- [Benutzerdefinierte Richtlinien](../../active-directory-b2c/custom-policy-get-started.md)
- [Anpassung der Benutzererfahrung](../../active-directory-b2c/configure-user-input.md)
