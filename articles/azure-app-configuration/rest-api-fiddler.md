---
title: Azure Active Directory-REST-API – Testen mit Fiddler
description: Verwenden von Fiddler zum Testen der Azure App Configuration-REST-API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 1142aa25212d87c5484963cda4e172df3d1fbafc
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932607"
---
# <a name="test-the-azure-app-configuration-rest-api-using-fiddler"></a>Testen der Azure App Configuration-REST-API mit Fiddler

Zum Testen der REST-API mit [Fiddler](https://www.telerik.com/fiddler) müssen Sie die zur [Authentifizierung](./rest-api-authentication-hmac.md) erforderlichen HTTP-Header in Ihre Anforderungen einfügen. So konfigurieren Sie Fiddler zum Testen der REST-API und automatischen Generieren der Authentifizierungsheader:

1. Stellen Sie sicher, dass TLS 1.2 ein zulässiges Protokoll ist:
    1. Wechseln Sie zu **Tools** > **Options** > **HTTPS** (Tools > Optionen > HTTPS).
    1. Stellen Sie sicher, dass **Decrypt HTTPS traffic** (HTTPS-Datenverkehr entschlüsseln) aktiviert ist.
    1. Fügen Sie der Liste der Protokolle **tls1.2** hinzu, falls nicht vorhanden.
1. Öffnen Sie **Fiddler Script Editor** (Fiddler-Skript-Editor), oder drücken Sie **STRG+R** in Fiddler.
1. Fügen Sie innerhalb der `Handlers`-Klasse vor der `OnBeforeRequest`-Funktion den folgenden Code hinzu.

    ```js
    static function SignRequest(oSession: Session, credential: String, secret: String) {
        var utcNow = DateTimeOffset.UtcNow.ToString("r", System.Globalization.DateTimeFormatInfo.InvariantInfo);
        var contentHash = ComputeSHA256Hash(oSession.RequestBody);
        var stringToSign = oSession.RequestMethod.ToUpperInvariant() + "\n" + oSession.PathAndQuery + "\n" + utcNow +";" + oSession.hostname + ";" + contentHash;
        var signature = ComputeHMACHash(secret, stringToSign);

        oSession.oRequest.headers["x-ms-date"] = utcNow;
        oSession.oRequest.headers["x-ms-content-sha256"] = contentHash;
        oSession.oRequest.headers["Authorization"] = "HMAC-SHA256 Credential=" + credential + "&SignedHeaders=x-ms-date;host;x-ms-content-sha256&Signature=" + signature;
    }

    static function ComputeSHA256Hash(content: Byte[]) {
        var sha256 = System.Security.Cryptography.SHA256.Create();
        try {
            return Convert.ToBase64String(sha256.ComputeHash(content));
        }
        finally {
            sha256.Dispose();
        }
    }

    static function ComputeHMACHash(secret: String, content: String) {
        var hmac = new System.Security.Cryptography.HMACSHA256(Convert.FromBase64String(secret));
        try {
            return Convert.ToBase64String(hmac.ComputeHash(System.Text.Encoding.ASCII.GetBytes(content)));
        }
        finally {
            hmac.Dispose();
        }
    }
    ```

1. Fügen Sie am Ende der `OnBeforeRequest`-Funktion den folgenden Code hinzu. Aktualisieren Sie den Zugriffsschlüssel, wie im TODO-Kommentar angegeben.

    ```js
    if (oSession.isFlagSet(SessionFlags.RequestGeneratedByFiddler) &&
        oSession.hostname.EndsWith(".azconfig.io", StringComparison.OrdinalIgnoreCase)) {

        // TODO: Replace the following placeholders with your access key
        var credential = "<Credential>"; // Id
        var secret = "<Secret>"; // Value

        SignRequest(oSession, credential, secret);
    }
    ```
1. Verwenden Sie den [Composer von Fiddler](https://docs.telerik.com/fiddler/Generate-Traffic/Tasks/CreateNewRequest), um eine Anforderung zu generieren und zu senden.
