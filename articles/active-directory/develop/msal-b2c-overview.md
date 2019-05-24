---
title: Informationen zur Integration in Azure AD B2C mithilfe von Microsoft Authentication Library (MSAL)
description: Microsoft Authentication Library (MSAL) ermöglicht Anwendungsentwicklern die Integration in Azure AD B2C und das Abrufen von Token, um gesicherte Web-APIs aufzurufen. Bei diesen Web-APIs kann es sich um Microsoft Graph, andere Microsoft APIs, Web-APIs von Drittanbietern oder Ihre eigene Web-API handeln.
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6af78a593fb8fadb836d2dc4c02115d95a7f2712
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546035"
---
# <a name="integrate-microsoft-authentication-library-msal-with-azure-active-directory-b2c"></a>Integrieren von Microsoft Authentication Library (MSAL) in Azure Active Directory B2C

Microsoft Authentication Library (MSAL) ermöglicht es Anwendungsentwicklern, Benutzer mit Social Media-Identitäten und lokalen Identitäten mithilfe von [Azure Active Directory (Azure AD) B2C](https://docs.microsoft.com/azure/active-directory-b2c/) zu authentifizieren. Azure Active Directory (Azure AD) B2C ist ein Identitätsverwaltungsdienst, mit dem Sie die Kundenregistrierung und -anmeldung anpassen und steuern sowie Profile bei der Verwendung Ihrer Anwendungen verwalten können.

Azure Active Directory (Azure AD) B2C ermöglicht es Ihnen auch, die Benutzeroberfläche (UI) Ihrer Anwendungen mit einer Marke zu kennzeichnen und anzupassen, um Ihren Kunden eine nahtlose Erfahrung zu bieten.

Dieses Tutorial veranschaulicht die Verwendung von Microsoft Authentication Library (MSAL) zur Integration in Azure Active Directory (Azure AD) B2C.


## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie Ihren eigenen [Azure AD B2C-Mandanten](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) noch nicht erstellt haben, erstellen Sie jetzt einen. Sie können einen vorhandenen Azure AD B2C-Mandanten verwenden. 

## <a name="javascript"></a>JavaScript

Die folgenden Schritte veranschaulichen, wie eine Single-Page-Webanwendung Azure AD B2C für die Benutzerregistrierung und -anmeldung sowie zum Aufrufen einer geschützten Web-API verwenden kann.

### <a name="step-1-register-your-application"></a>Schritt 1: Anwendung registrieren

Sie müssen zuerst Ihre Anwendung registrieren, um die Authentifizierung zu implementieren. Ausführliche Schritte zum Registrieren Ihrer App finden Sie unter [Registrieren Ihrer Anwendung](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c).

### <a name="steps-2-download-applications"></a>Schritt 2: Herunterladen von Anwendungen

Laden Sie eine ZIP-Datei herunter, oder klonen Sie das Beispiel aus GitHub.
>Git-Klon https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git

### <a name="steps-3-authentication"></a>Schritt 3: Authentication

1. Öffnen Sie die Datei „index.html“ in Ihrem Browser.

2. Konfigurieren Sie das Beispiel mit der Anwendungs-ID und dem Schlüssel, die Sie zuvor bei der Registrierung Ihrer Anwendung notiert haben. Ändern Sie die folgenden Codezeilen. Ersetzen Sie dabei die Werte durch die Namen Ihres Verzeichnisses und Ihre APIs:

```javascript
// The current application coordinates were pre-registered in a B2C directory.

const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/tfp/<your-tenant-name>.onmicrosoft.com/<your-sign-in-sign-up-policy>",
        b2cScopes: ["https://<your-tenant-name>.onmicrosoft.com/hello/demo.read"],
        webApi: 'http://localhost:5000/hello',
  };

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

Der Name des in diesem Tutorial verwendeten [Benutzerflows](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) lautet „B2C_1_signupsignin1“. Wenn Sie einen anderen Benutzerflownamen verwenden, geben Sie diesen als Autoritätswert an.


### <a name="configure-application-to-use-b2clogincom"></a>Konfigurieren der Anwendung für die Verwendung von `b2clogin.com`

Sie können `b2clogin.com` anstelle von `login.microsoftonline.com` verwenden, wenn Sie einen Identitätsanbieter für die Registrierung und Anmeldung in Ihrer Azure Active Directory B2C-Anwendung (Azure AD) einrichten.

**`b2clogin.com`**, d. h. 
`https://your-tenant-name.b2clogin.com/your-tenant-guid` wird für Folgendes verwendet:

- Der im Cookieheader von Microsoft-Diensten verbrauchte Speicherplatz wird reduziert.
- Ihre URLs enthalten keinen Verweis mehr auf Microsoft. Beispielsweise verweist Ihre Azure AD B2C-Anwendung wahrscheinlich auf „login.microsoftonline.com“.


 Um „b2clogin.com“ nutzen zu können, müssen Sie die Konfiguration Ihrer Anwendung aktualisieren.  

1. „ValidateAuthority“ aktualisieren: Legen Sie die **ValidateAuthority**-Eigenschaft auf `false` fest. Wenn **validateAuthority** auf „false“ festgelegt ist, sind Umleitungen an „b2clogin.com“ zulässig.

Das folgende Beispiel zeigt eine Möglichkeit, wie Sie die Eigenschaft festlegen können:
```javascript
// The current application coordinates were pre-registered in a B2C directory.

const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://contoso.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://contosohello.azurewebsites.net/hello',
        validateAuthority: false;

};
// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

> [!NOTE]
> Die Azure AD B2C-Anwendung verweist wahrscheinlich an mehreren Stellen auf „login.microsoftonline.com“, z. B. in Ihren Benutzerflowverweisen und Tokenendpunkten. Vergewissern Sie sich, dass Ihr Autorisierungsendpunkt, Ihr Tokenendpunkt und Ihr Aussteller aktualisiert wurden und „Ihr-Mandantenname.b2clogin.com“ verwenden.

Folgen Sie diesem [MSAL JS-Beispiel](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c) für die Verwendung von Microsoft Authentication Library (Vorschauversion) für JavaScript (msal.js), um ein Zugriffstoken zu erhalten und eine durch Azure AD B2C gesicherte API aufzurufen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen:

- [Benutzerdefinierte Richtlinien](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Anpassung der Benutzeroberfläche](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)