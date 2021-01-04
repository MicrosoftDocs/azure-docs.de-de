---
title: Anmeldeinformationen für das Microsoft Identity Platform-Zertifikat
titleSuffix: Microsoft identity platform
description: In diesem Artikel werden die Registrierung für die Anwendungsauthentifizierung und die Verwendung von Zertifikatanmeldeinformationen für diesen Zweck beschrieben.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 12/3/2020
ms.author: hirsin
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.openlocfilehash: f3222c790ccd0cee936b246253a16b5c434c61c8
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602205"
---
# <a name="microsoft-identity-platform-application-authentication-certificate-credentials"></a>Microsoft Identity Platform-Zertifikatanmeldeinformationen für die Anwendungsauthentifizierung

Microsoft Identity Platform ermöglicht es einer Anwendung, sich überall dort mit ihren eigenen Anmeldeinformationen zu authentifizieren, wo ein geheimer Clientschlüssel verwendet werden kann, also beispielsweise im OAuth 2.0-Flow zum [Gewähren von Clientanmeldeinformationen](v2-oauth2-client-creds-grant-flow.md) und im [On-Behalf-Of](v2-oauth2-on-behalf-of-flow.md)-Flow (OBO).

Eine Form von Anmeldeinformationen, die eine Anwendung zur Authentifizierung verwenden kann, ist eine [JSON Web Token (JWT)-Assertion](./security-tokens.md#json-web-tokens-jwts-and-claims), die mit einem zur Anwendung gehörigen Zertifikat signiert ist.

## <a name="assertion-format"></a>Assertionformat

Für die Berechnung der Assertion können Sie eine der zahlreichen JWT-Bibliotheken in der Sprache Ihrer Wahl nutzen. [MSAL unterstützt dies mithilfe von `.WithCertificate()`](msal-net-client-assertions.md). Die Informationen sind im [Header](#header), den [Ansprüchen](#claims-payload) und der [Signatur](#signature) des Tokens enthalten.

### <a name="header"></a>Header

| Parameter |  Anmerkung |
| --- | --- |
| `alg` | Muss **RS256** sein. |
| `typ` | Muss **JWT** sein. |
| `x5t` | Die als Base64url-Zeichenfolgenwert codierte Hexadezimaldarstellung des X.509-Zertifikathashs (auch als SHA-1-*Fingerabdruck* des Zertifikats bezeichnet). Beim X.509-Zertifikathash `84E05C1D98BCE3A5421D225B140B36E86A3D5534` (Hexadezimaldarstellung) würde der `x5t`-Anspruch beispielsweise `hOBcHZi846VCHSJbFAs26Go9VTQ=` (Base64url) lauten. |

### <a name="claims-payload"></a>Ansprüche (Nutzlast)

Anspruchstyp | Wert | BESCHREIBUNG
---------- | ---------- | ----------
aud | `https://login.microsoftonline.com/{tenantId}/v2.0` | Der Anspruch „aud“ (audience, Zielgruppe) identifiziert die Empfänger, für die das JWT vorgesehen ist (hier Azure AD). Weitere Informationen finden Sie unter [RFC 7519, Abschnitt 4.1.3](https://tools.ietf.org/html/rfc7519#section-4.1.3).  In diesem Fall ist dieser Empfänger der Anmeldeserver (login.microsoftonline.com).
exp | 1601519414 | Der Anspruch „exp“ (Ablaufzeit) gibt die Ablaufzeit an, ab oder nach der das JWT NICHT für die Bearbeitung akzeptiert werden darf. Weitere Informationen finden Sie unter [RFC 7519, Abschnitt 4.1.4](https://tools.ietf.org/html/rfc7519#section-4.1.4).  So kann die Assertion bis zu diesem Zeitpunkt verwendet werden. Daher sollte der Zeitraum kurz ausfallen, d. h. höchstens 5–10 Minuten nach `nbf` liegen.  In Azure AD sind derzeit keine Einschränkungen für den `exp`-Zeitpunkt vorgesehen. 
iss | {ClientID} | Der Anspruch „iss“ (issuer, Aussteller) identifiziert den Prinzipal, der das JWT ausgestellt hat, in diesem Fall Ihre Clientanwendung.  Verwenden Sie die GUID der Anwendungs-ID.
jti | (eine GUID) | Der Anspruch "jti" (JWT-ID) stellt einen eindeutigen Bezeichner für das JWT bereit. Es MUSS ein Bezeichnerwert zugewiesen werden, bei dem die Wahrscheinlichkeit vernachlässig ist, dass derselbe Wert versehentlich einem anderen Datenobjekt zugewiesen wird. Wenn die Anwendung mehrere Aussteller verwendet, MÜSSEN auch Konflikte zwischen von mehreren Ausstellern erstellten Werten verhindert werden. Der Wert von „JTI“ ist eine Zeichenfolge mit Beachtung der Groß-/Kleinschreibung. [RFC 7519, Abschnitt 4.1.7](https://tools.ietf.org/html/rfc7519#section-4.1.7)
nbf | 1601519114 | Der Anspruch „nbf“ (nicht vor) gibt die Zeit an, vor der das JWT NICHT für die Bearbeitung akzeptiert werden darf. Weitere Informationen finden Sie unter [RFC 7519, Abschnitt 4.1.5](https://tools.ietf.org/html/rfc7519#section-4.1.5).  Die Verwendung der aktuellen Uhrzeit ist angemessen. 
sub | {ClientID} | Der Anspruch „sub“ (subject, Antragsteller) identifiziert den Antragsteller des JWT, in diesem Fall ist das ebenfalls Ihre Anwendung. Verwenden Sie denselben Wert wie für `iss`. 

### <a name="signature"></a>Signatur

Zur Berechnung der Signatur wird das Zertifikat wie in der [Spezifikation RFC7519 für JSON-Webtoken](https://tools.ietf.org/html/rfc7519) beschrieben angewendet.

## <a name="example-of-a-decoded-jwt-assertion"></a>Beispiel einer decodierten JWT-Assertion

```JSON
{
  "alg": "RS256",
  "typ": "JWT",
  "x5t": "gx8tGysyjcRqKjFPnd7RFwvwZI0"
}
.
{
  "aud": "https: //login.microsoftonline.com/contoso.onmicrosoft.com/oauth2/token",
  "exp": 1484593341,
  "iss": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05",
  "jti": "22b3bb26-e046-42df-9c96-65dbd72c1c81",
  "nbf": 1484592741,
  "sub": "97e0a5b7-d745-40b6-94fe-5f77d35c6e05"
}
.
"Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="example-of-an-encoded-jwt-assertion"></a>Beispiel einer codierten JWT-Assertion

Die folgende Zeichenfolge ist ein Beispiel für eine codierte Assertion. Bei genauer Betrachtung sehen Sie drei Abschnitte, die jeweils durch einen Punkt getrennt sind (`.`):

* Im ersten Abschnitt ist der *Header* codiert.
* Im zweiten Abschnitt sind die *Ansprüche* (Nutzlast) codiert.
* Der letzte Abschnitt enthält die *Signatur*, die mit den Zertifikaten aus dem Inhalt der ersten beiden Abschnitte berechnet wurde.

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-microsoft-identity-platform"></a>Registrieren Ihres Zertifikats bei Microsoft Identity Platform

Über das Azure-Portal können Sie die Zertifikatanmeldeinformationen mit der Clientanwendung in Microsoft Identity Platform verknüpfen. Dazu haben Sie folgende Möglichkeiten:

### <a name="uploading-the-certificate-file"></a>Hochladen der Zertifikatdatei

In der Azure-App-Registrierung für die Clientanwendung:
1. Wählen Sie **Zertifikate & Geheimnisse** aus.
2. Klicken Sie auf **Zertifikat hochladen**, und wählen Sie die Zertifikatdatei zum Hochladen aus.
3. Klicken Sie auf **Hinzufügen**.
  Nachdem das Zertifikat hochgeladen wurde, werden der Fingerabdruck, das Startdatum und der Ablaufzeitpunkt angezeigt.

### <a name="updating-the-application-manifest"></a>Aktualisieren des Anwendungsmanifests

Wenn Sie über ein Zertifikat verfügen, berechnen Sie Folgendes:

- `$base64Thumbprint`: Base64-codierter Wert des Zertifikathashs
- `$base64Value`: Base64-codierter Wert der Zertifikatrohdaten

Geben Sie außerdem eine GUID an, um den Schlüssel im Anwendungsmanifest (`$keyId`) zu identifizieren.

In der Azure-App-Registrierung für die Clientanwendung:
1. Wählen Sie **Manifest** aus, um das Anwendungsmanifest zu öffnen.
2. Ersetzen Sie die Eigenschaft *keyCredentials* gemäß dem folgenden Schema durch Ihre neuen Zertifikatinformationen.

   ```JSON
   "keyCredentials": [
       {
           "customKeyIdentifier": "$base64Thumbprint",
           "keyId": "$keyid",
           "type": "AsymmetricX509Cert",
           "usage": "Verify",
           "value":  "$base64Value"
       }
   ]
   ```
3. Speichern Sie die Änderungen am Anwendungsmanifest, und laden Sie dann das Manifest in Microsoft Identity Platform hoch.

   Da die `keyCredentials`-Eigenschaft mehrere Werte besitzen kann, können Sie mehrere Zertifikate hochladen, um eine vielfältigere Schlüsselverwaltung zu erreichen.
   
## <a name="using-a-client-assertion"></a>Verwenden einer Clientassertion

Clientassertionen können überall dort verwendet werden, wo ein geheimer Clientschlüssel verwendet werden würde.  Beispielsweise können Sie im [Autorisierungscodeflow](v2-oauth2-auth-code-flow.md) den Parameter `client_secret` übergeben, um nachzuweisen, dass die Anforderung von Ihrer App stammt. Sie können dies durch `client_assertion`- und `client_assertion_type`-Parameter ersetzen. 

| Parameter | Wert | Beschreibung|
|-----------|-------|------------|
|`client_assertion_type`|`urn:ietf:params:oauth:client-assertion-type:jwt-bearer`| Dies ist ein fester Wert, der darauf hinweist, dass Sie Zertifikatanmeldeinformationen verwenden. |
|`client_assertion`| JWT |Dies ist das oben erstellte JWT. |

## <a name="next-steps"></a>Nächste Schritte

Die [Bibliothek „MSAL.NET“ löst dieses Szenario](msal-net-client-assertions.md) mit einer einzigen Codezeile.

Das Codebeispiel [.NET Core-Daemon-Konsolenanwendung mit Microsoft Identity Platform](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) auf GitHub zeigt, wie eine Anwendung ihre eigenen Anmeldeinformationen für die Authentifizierung verwendet. Zudem erfahren Sie darin, wie Sie mit dem PowerShell-Cmdlet `New-SelfSignedCertificate` ein [selbstsigniertes Zertifikat erstellen können](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/tree/master/1-Call-MSGraph#optional-use-the-automation-script). Sie können auch die [App-Erstellungsskripts](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/blob/master/1-Call-MSGraph/AppCreationScripts-withCert/AppCreationScripts.md) im Beispielrepository verwenden, um Zertifikate zu erstellen, den Fingerabdruck zu berechnen und so weiter.
