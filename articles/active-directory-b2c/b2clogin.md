---
title: Migrieren von Anwendungen und APIs zu b2clogin.com
titleSuffix: Azure AD B2C
description: Erfahren Sie mehr über das Verwenden von b2clogin.com in Umleitungs-URLs für Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/27/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 20df5fc3a4d7c392be62df2b7778854d1e2e1cba
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109061"
---
# <a name="set-redirect-urls-to-b2clogincom-for-azure-active-directory-b2c"></a>Festlegen von Umleitungs-URLs zu b2clogin.com für Azure Active Directory B2C

Wenn Sie einen Identitätsanbieter für die Registrierung und Anmeldung in Ihrer Azure Active Directory B2C-Anwendung (Azure AD B2C) einrichten, müssen Sie eine Umleitungs-URL angeben. Für die Authentifizierung von Benutzern in Azure AD B2C sollte in Ihren Anwendungen und APIs nicht mehr auf *login.microsoftonline.com* verwiesen werden. Verwenden Sie stattdessen für alle neuen Anwendungen *b2clogin.com*, und migrieren Sie vorhandene Anwendungen von *login.microsoftonline.com* nach *b2clogin.com*.

## <a name="deprecation-of-loginmicrosoftonlinecom"></a>Einstellung von login.microsoftonline.com

**Update vom Oktober 2020:** Für Mandanten, die das ursprünglich angekündigte Veraltungsdatum am 4. Dezember 2020 nicht einhalten können, wird die Toleranzperiode verlängert. Die Außerbetriebnahme von login.microsoftonline.com findet nun erst am **14. Januar 2021**.

**Begründung:** Am 4. Dezember 2019 wurde ursprünglich [bekannt gegeben](https://azure.microsoft.com/updates/b2c-deprecate-msol/), dass login.microsoftonline.com in Azure AD B2C ab dem 4. Dezember 2020 nicht mehr unterstützt wird. Somit verfügten vorhandene Mandanten über genau ein Jahr Zeit, um zu b2clogin.com zu migrieren. Neue Mandanten, die nach dem 4. Dezember 2019 erstellt werden, akzeptieren keine Anforderungen von login.microsoftonline.com. Alle Funktionen stehen auf dem Endpunkt b2clogin.com weiterhin unverändert zur Verfügung.

Die Einstellung von login.microsoftonline.com wirkt sich nicht auf Azure Active Directory-Mandanten aus. Von dieser Änderung sind ausschließlich Azure Active Directory B2C-Mandanten betroffen.

## <a name="what-endpoints-does-this-apply-to"></a>Betroffene Endpunkte
Der Übergang zu „b2clogin.com“ gilt nur für Authentifizierungsendpunkte, die Azure AD B2C-Richtlinien (Benutzerflows oder benutzerdefinierte Richtlinien) verwenden, um Benutzer zu authentifizieren. Diese Endpunkte verfügen über einen `<policy-name>`-Parameter, der die von Azure AD B2C zu verwendende Richtlinie angibt. [Erfahren Sie mehr über Azure AD B2C-Richtlinien](technical-overview.md#identity-experiences-user-flows-or-custom-policies). 

Diese Endpunkte können wie folgt aussehen:
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/<b>\<policy-name\></b>/oauth2/v2.0/authorize</code>

- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/<b>\<policy-name\></b>/oauth2/v2.0/token</code>

Alternativ kann `<policy-name>` als Abfrageparameter übergeben werden:
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/oauth2/v2.0/authorize?<b>p=\<policy-name\></b></code>
- <code>https://login.microsoft.com/\<tenant-name\>.onmicrosoft.com/oauth2/v2.0/token?<b>p=\<policy-name\></b></code>

> [!IMPORTANT]
> Endpunkte, die den „policy“-Parameter verwenden, müssen auch als [Umleitungs-URLs für Identitätsanbieter](#change-identity-provider-redirect-urls) aktualisiert werden.

Einige Azure AD B2C-Kunden verwenden die freigegebenen Funktionen von Azure AD-Unternehmensmandanten, beispielsweise den Gewährungsflow für OAuth 2.0-Clientanmeldeinformationen. Der Zugriff auf diese Features erfolgt über die „login.microsoftonline.com“-Endpunkte von Azure AD, *die keinen Richtlinienparameter enthalten*. __Diese Endpunkte sind nicht betroffen__.

## <a name="benefits-of-b2clogincom"></a>Vorteile von „b2clogin.com“

Bei Verwendung von *b2clogin.com* als Umleitungs-URL:

* Der im Cookieheader von Microsoft-Diensten verbrauchte Speicherplatz wird reduziert.
* Ihre Umleitungs-URLs müssen keinen Verweis auf Microsoft mehr enthalten.
* Clientseitiger JavaScript-Code wird in angepassten Seiten unterstützt (derzeit als [Vorschaufunktion](javascript-and-page-layout.md)). Aufgrund von Sicherheitseinschränkungen werden JavaScript-Code und HTML-Formularelemente von benutzerdefinierten Seiten entfernt, wenn Sie *login.microsoftonline.com* verwenden.

## <a name="overview-of-required-changes"></a>Übersicht über erforderliche Änderungen

Sie müssen möglicherweise eine Reihe von Änderungen vornehmen, um Ihre Anwendungen zu *b2clogin.com* zu migrieren:

* Ändern Sie die Umleitungs-URL in den Anwendungen Ihres Identitätsanbieters so, dass auf *b2clogin.com* verwiesen wird.
* Aktualisieren Sie Ihre Azure AD B2C-Anwendungen so, dass *b2clogin.com* in den Verweisen auf Benutzerflows und Tokenendpunkte verwendet wird. Dieser Schritt kann das Aktualisieren der Verwendung einer Authentifizierungsbibliothek wie Microsoft Authentication Library (MSAL) umfassen.
* Aktualisieren Sie alle **zulässigen Ursprünge**, die Sie in den CORS-Einstellungen zur [Anpassung der Benutzeroberfläche](customize-ui-with-html.md) definiert haben.

Ein alter Endpunkt kann wie folgt aussehen:
- <b><code>https://login.microsoft.com/</b>\<tenant-name\>.onmicrosoft.com/\<policy-name\>/oauth2/v2.0/authorize</code>

Ein entsprechender aktualisierter Endpunkt würde wie folgt aussehen:
- <code><b>https://\<tenant-name\>.b2clogin.com/</b>\<tenant-name\>.onmicrosoft.com/\<policy-name\>/oauth2/v2.0/authorize</code>


## <a name="change-identity-provider-redirect-urls"></a>Ändern der Umleitungs-URL für Identitätsanbieter

Ändern Sie auf jeder Website des Identitätsanbieters, auf der Sie eine Anwendung erstellt haben, alle vertrauenswürdigen URLs so, dass eine Umleitung zu `your-tenant-name.b2clogin.com` und nicht zu *login.microsoftonline.com* erfolgt.

Es gibt zwei Formate, die für die Umleitungs-URLs zu „b2clogin.com“ verwendet werden können. Das erste bietet den Vorteil, dass an keiner Stelle der URL „Microsoft“ auftaucht, da die Mandanten-ID (eine GUID) anstelle des Domänennamens Ihres Mandanten verwendet wird:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-id}/oauth2/authresp
```

Bei der zweiten Option wird der Domänenname Ihres Mandanten im Format `your-tenant-name.onmicrosoft.com` verwendet. Beispiel:

```
https://{your-tenant-name}.b2clogin.com/{your-tenant-name}.onmicrosoft.com/oauth2/authresp
```

Für beide Formate:

* Ersetzen Sie `{your-tenant-name}` durch den Namen des Azure AD B2C-Mandanten.
* Entfernen Sie `/te`, sofern in der URL vorhanden.

## <a name="update-your-applications-and-apis"></a>Aktualisieren Ihrer Anwendungen und APIs

Der Code in Ihren Azure AD B2C-fähigen Anwendungen und APIs kann an verschiedenen Stellen auf `login.microsoftonline.com` verweisen. Ihr Code kann z. B. Verweise auf Benutzerflows und Tokenendpunkte enthalten. Aktualisieren Sie Folgendes, sodass auf `your-tenant-name.b2clogin.com` verwiesen wird:

* Authorization endpoint (Autorisierungsendpunkt)
* Token endpoint (Tokenendpunkt)
* Tokenaussteller

Der Autorisierungsendpunkt für die Registrierungs-/Anmelderichtlinie von Contoso würde z. B. nun wie folgt lauten:

```
https://contosob2c.b2clogin.com/00000000-0000-0000-0000-000000000000/B2C_1_signupsignin1
```

Informationen zur Migration von OWIN-basierten Webanwendungen zu b2clogin.com finden Sie unter [Migrieren einer OWIN-basierten Web-API zu b2clogin.com](multiple-token-endpoints.md).

Informationen zur Migration von Azure API Management-APIs, die durch Azure AD B2C geschützt sind, finden Sie unter [Sichern einer Azure API Management-API mit Azure AD B2C](secure-api-management.md) im Abschnitt [Migrieren zu b2clogin.com](secure-api-management.md#migrate-to-b2clogincom).

## <a name="microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL)

### <a name="msalnet-validateauthority-property"></a>MSAL.NET: Eigenschaft „ValidateAuthority“

Wenn Sie [MSAL.NET][msal-dotnet] v2 oder früher verwenden, legen Sie die **ValidateAuthority**-Eigenschaft bei der Clientinstanziierung auf `false` fest, um Umleitungen zu *b2clogin.com* zuzulassen. Für MSAL.NET v3 und höher ist es nicht erforderlich, diesen Wert auf `false` festzulegen.

```csharp
ConfidentialClientApplication client = new ConfidentialClientApplication(...); // Can also be PublicClientApplication
client.ValidateAuthority = false; // MSAL.NET v2 and earlier **ONLY**
```

### <a name="msal-for-javascript-validateauthority-property"></a>MSAL für JavaScript: Eigenschaft „validateAuthority“

Wenn Sie [MSAL für JavaScript][msal-js] v1.2.2 oder eine frühere Version verwenden, legen Sie die Eigenschaft **validateAuthority** auf `false` fest.

```JavaScript
// MSAL.js v1.2.2 and earlier
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: false // Required in MSAL.js v1.2.2 and earlier **ONLY**
  }
);
```

Wenn Sie `validateAuthority: true` in MSAL.js 1.3.0+ (Standardeinstellung) festlegen, müssen Sie außerdem mit `knownAuthorities` einen gültigen Tokenaussteller angeben:

```JavaScript
// MSAL.js v1.3.0+
this.clientApplication = new UserAgentApplication(
  env.auth.clientId,
  env.auth.loginAuthority,
  this.authCallback.bind(this),
  {
    validateAuthority: true, // Supported in MSAL.js v1.3.0+
    knownAuthorities: ['tenant-name.b2clogin.com'] // Required if validateAuthority: true
  }
);
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zur Migration von OWIN-basierten Webanwendungen zu b2clogin.com finden Sie unter [Migrieren einer OWIN-basierten Web-API zu b2clogin.com](multiple-token-endpoints.md).

Informationen zur Migration von Azure API Management-APIs, die durch Azure AD B2C geschützt sind, finden Sie unter [Sichern einer Azure API Management-API mit Azure AD B2C](secure-api-management.md) im Abschnitt [Migrieren zu b2clogin.com](secure-api-management.md#migrate-to-b2clogincom).

<!-- LINKS - External -->
[msal-dotnet]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[msal-dotnet-b2c]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics
[msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
[msal-js-b2c]: ../active-directory/develop/msal-b2c-overview.md
