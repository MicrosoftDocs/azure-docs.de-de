---
title: Zertifikatanmeldeinformationen in Azure AD | Microsoft-Dokumentation
description: In diesem Artikel werden die Registrierung für die Anwendungsauthentifizierung und die Verwendung von Zertifikatanmeldeinformationen für diesen Zweck beschrieben.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 88f0c64a-25f7-4974-aca2-2acadc9acbd8
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2019
ms.author: ryanwi
ms.reviewer: nacanuma, jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed4e7559ff6c3b76bbdf49b538ffebf3ad09cc58
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66001232"
---
# <a name="certificate-credentials-for-application-authentication"></a>Zertifikatanmeldeinformationen für die Anwendungsauthentifizierung

Azure Active Directory (Azure AD) ermöglicht einer Anwendung, ihre eigenen Anmeldeinformationen für die Authentifizierung z.B. im Flow zum Erteilen der OAuth 2.0-Clientanmeldeinformationen ([v1.0](v1-oauth2-client-creds-grant-flow.md), [v2.0](v2-oauth2-client-creds-grant-flow.md)) und den Im-Auftrag-von-Flow ([v1.0](v1-oauth2-on-behalf-of-flow.md), [v2.0](v2-oauth2-on-behalf-of-flow.md)) zu verwenden.

Eine Form von Anmeldeinformationen, die eine Anwendung zur Authentifizierung verwenden kann, ist eine JWT-Assertion (JSON Web Token), die mit einem der Anwendung zugehörigen Zertifikat signiert ist.

## <a name="assertion-format"></a>Assertionformat
Für die Berechnung der Assertion können Sie eine der zahlreichen [JSON Web Token](https://jwt.ms/)-Bibliotheken in der Sprache Ihrer Wahl nutzen. Das Token enthält folgende Informationen:

### <a name="header"></a>Header

| Parameter |  Anmerkung |
| --- | --- |
| `alg` | Muss **RS256** sein. |
| `typ` | Muss **JWT** sein. |
| `x5t` | Muss der SHA-1-Fingerabdruck des X.509-Zertifikats sein. |

### <a name="claims-payload"></a>Ansprüche (Nutzlast)

| Parameter |  Anmerkungen |
| --- | --- |
| `aud` | Audience: Sollte **https://login.microsoftonline.com/*Mandanten-ID*/oauth2/token** lauten |
| `exp` | Ablaufdatum: Datum, an dem das Token abläuft. Die Zeit wird als Anzahl der Sekunden ab dem 1. Januar 1970 (1970-01-01T0:0:0Z) UTC bis zum Zeitpunkt dargestellt, an dem die Gültigkeit des Tokens abläuft.|
| `iss` | Aussteller: Muss die Client-ID (Anwendungs-ID des Clientdiensts) sein. |
| `jti` | GUID: Die JWT-ID. |
| `nbf` | Nicht vor: Datum, vor dem das Token nicht verwendet werden kann. Die Zeit wird als Anzahl der Sekunden ab dem 1. Januar 1970 (1970-01-01T0:0:0Z) (UTC) bis zur Zeit der Ausstellung des Tokens dargestellt. |
| `sub` | Antragsteller: Muss für `iss` die Client-ID (Anwendungs-ID des Clientdiensts) sein |

### <a name="signature"></a>Signatur

Zur Berechnung der Signatur wird das Zertifikat wie in der [Spezifikation für JSON-Webtoken vom Typ „RFC7519“](https://tools.ietf.org/html/rfc7519) beschrieben angewendet.

## <a name="example-of-a-decoded-jwt-assertion"></a>Beispiel einer decodierten JWT-Assertion

```
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

Die folgende Zeichenfolge ist ein Beispiel für eine codierte Assertion. Bei genauer Betrachtung sehen Sie drei Abschnitte, die jeweils durch einen Punkt getrennt sind:
* Im ersten Abschnitt wird der Header codiert.
* Im zweiten Abschnitt wird die Nutzlast codiert.
* Der letzte Abschnitt enthält die Signatur, die mit den Zertifikaten aus dem Inhalt der ersten beiden Abschnitte berechnet wurde.

```
"eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJhdWQiOiJodHRwczpcL1wvbG9naW4ubWljcm9zb2Z0b25saW5lLmNvbVwvam1wcmlldXJob3RtYWlsLm9ubWljcm9zb2Z0LmNvbVwvb2F1dGgyXC90b2tlbiIsImV4cCI6MTQ4NDU5MzM0MSwiaXNzIjoiOTdlMGE1YjctZDc0NS00MGI2LTk0ZmUtNWY3N2QzNWM2ZTA1IiwianRpIjoiMjJiM2JiMjYtZTA0Ni00MmRmLTljOTYtNjVkYmQ3MmMxYzgxIiwibmJmIjoxNDg0NTkyNzQxLCJzdWIiOiI5N2UwYTViNy1kNzQ1LTQwYjYtOTRmZS01Zjc3ZDM1YzZlMDUifQ.
Gh95kHCOEGq5E_ArMBbDXhwKR577scxYaoJ1P{a lot of characters here}KKJDEg"
```

## <a name="register-your-certificate-with-azure-ad"></a>Registrieren Ihres Zertifikats bei Azure AD

Über das Azure-Portal können Sie die Zertifikatanmeldeinformationen in Azure AD mit der Clientanwendung verknüpfen. Dazu haben Sie folgende Möglichkeiten:

### <a name="uploading-the-certificate-file"></a>Hochladen der Zertifikatdatei

In der Azure-App-Registrierung für die Clientanwendung:
1. Wählen Sie **Zertifikate & Geheimnisse** aus. 
2. Klicken Sie auf **Zertifikat hochladen**, und wählen Sie die Zertifikatdatei zum Hochladen aus.
3. Klicken Sie auf **Hinzufügen**.
  Nachdem das Zertifikat hochgeladen wurde, werden der Fingerabdruck, das Startdatum und der Ablaufzeitpunkt angezeigt. 

### <a name="updating-the-application-manifest"></a>Aktualisieren des Anwendungsmanifests

Wenn Sie über ein Zertifikat verfügen, berechnen Sie Folgendes:

- `$base64Thumbprint`: Die base64-Codierung des Zertifikathashs.
- `$base64Value`: Die base64-Codierung der Zertifikatrohdaten.

Geben Sie außerdem eine GUID an, um den Schlüssel im Anwendungsmanifest (`$keyId`) zu identifizieren.

In der Azure-App-Registrierung für die Clientanwendung:
1. Wählen Sie **Manifest** aus, um das Anwendungsmanifest zu öffnen.
2. Ersetzen Sie die Eigenschaft *keyCredentials* gemäß dem folgenden Schema durch Ihre neuen Zertifikatinformationen.

   ```
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
3. Speichern Sie die Änderungen am Anwendungsmanifest, und laden Sie dann das Manifest in Azure AD hoch. 

   Da die `keyCredentials`-Eigenschaft mehrere Werte besitzen kann, können Sie mehrere Zertifikate hochladen, um eine vielfältigere Schlüsselverwaltung zu erreichen.
   
## <a name="code-sample"></a>Codebeispiel

Das Codebeispiel unter [Authenticating to Azure AD in daemon apps with certificates](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential) (Authentifizierung bei Azure AD in Daemonanwendungen mit Zertifikaten) zeigt, wie eine Anwendung ihre eigenen Anmeldeinformationen für die Authentifizierung verwendet. Zudem erfahren Sie darin, wie Sie mit dem `New-SelfSignedCertificate`-PowerShell-Befehl ein [selbstsigniertes Zertifikat erstellen](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential#create-a-self-signed-certificate) können. Sie können auch die [App-Erstellungsskripte](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential/blob/master/AppCreationScripts/AppCreationScripts.md) verwenden, um die Zertifikate zu erstellen, den Fingerabdruck zu berechnen und so weiter.
