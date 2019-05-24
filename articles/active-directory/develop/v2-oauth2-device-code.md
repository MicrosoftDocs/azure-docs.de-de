---
title: Anmelden von Benutzern bei Geräten ohne Browser über Microsoft Identity Platform | Azure
description: Erstellen von eingebetteten und browserlosen Authentifizierungsflows unter Verwendung der Gerätecodegewährung.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/20/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86e875108e0349c0ab08a7217074e2afe23bcacc
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544921"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-code-flow"></a>Microsoft Identity Platform und der OAuth 2.0-Gerätecodeflow

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Microsoft Identity Platform unterstützt die [Gerätecodegewährung](https://tools.ietf.org/html/draft-ietf-oauth-device-flow-12), die es Benutzern ermöglicht, sich bei eingabeeingeschränkten Geräten wie einem Smart-TV, IoT-Geräten oder einem Drucker anzumelden.  Um diesen Flow zu ermöglichen, veranlasst das Gerät den Benutzer zum Besuch einer Webseite im Browser auf einem anderen Gerät, um sich anzumelden.  Sobald sich der Benutzer anmeldet, kann das Gerät nach Bedarf Zugriffstoken und Aktualisierungstoken abrufen.  

> [!IMPORTANT]
> Derzeit wird am Microsoft Identity Platform-Endpunkt nur der Geräteflow für Azure AD-Mandanten, jedoch nicht für persönliche Konten unterstützt.  Dies bedeutet, dass Sie einen Endpunkt als Mandanten einrichten oder den Endpunkt `organizations` verwenden müssen.  
>
> Persönliche Konten, die zu einem Azure AD-Mandanten eingeladen werden, können die Geräteflowgewährung verwenden, aber nur im Kontext des Mandanten.

> [!NOTE]
> Der Microsoft Identity Platform-Endpunkt unterstützt nicht alle Szenarien und Features von Azure Active Directory. Informieren Sie sich über die [Einschränkungen von Microsoft Identity Platform](active-directory-v2-limitations.md), um zu bestimmen, ob Sie den Microsoft Identity Platform-Endpunkt verwenden sollten.

## <a name="protocol-diagram"></a>Protokolldiagramm

Der gesamte Gerätecodeflow ähnelt dem nächsten Diagramm. Wir beschreiben die einzelnen Schritte weiter unten in diesem Artikel.

![Gerätecodefluss](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Geräteautorisierungsanforderung

Der Client muss zuerst den Authentifizierungsserver auf einen Geräte- und Benutzercode überprüfen, die zum Initiieren der Authentifizierung verwendet werden. Der Client sammelt diese Anforderung vom `/devicecode`-Endpunkt. In diese Anforderung sollte der Client außerdem die Berechtigungen aufnehmen, die er vom Benutzer erhalten muss. Ab dem Zeitpunkt des Absendens dieser Anforderung hat der Benutzer nur 15 Minuten, um sich anzumelden (der übliche Wert für `expires_in`), weshalb Sie diese Anforderung nur stellen sollten, wenn der Benutzer angezeigt hat, dass er zur Anmeldung bereit ist.

> [!TIP]
> Führen Sie diese Anforderung in Postman aus.
> [![Ausführen in Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parameter | Bedingung | BESCHREIBUNG |
| --- | --- | --- |
| `tenant` | Erforderlich |Der Verzeichnismandant, von dem Sie die Berechtigung anfordern möchten. Kann als GUID oder als Anzeigename bereitgestellt werden.  |
| `client_id` | Erforderlich | Die **Anwendungs-ID (Client-ID)**, die Ihrer App im [Azure-Portal auf der Seite „App-Registrierungen“](https://go.microsoft.com/fwlink/?linkid=2083908) zugewiesen wurde. |
| `scope` | Empfohlen | Eine durch Leerzeichen getrennte Liste mit [Bereichen](v2-permissions-and-consent.md) , denen der Benutzer zustimmen soll.  |

### <a name="device-authorization-response"></a>Geräteautorisierungsantwort

Eine erfolgreicher Antwort besteht aus einem JSON-Objekt, das die erforderlichen Informationen enthält, mit denen sich der Benutzer anmelden kann.  

| Parameter | Format | BESCHREIBUNG |
| ---              | --- | --- |
|`device_code`     | Zeichenfolge | Eine lange Zeichenfolge, die zum Verifizieren der Sitzung zwischen dem Client und dem Autorisierungsserver verwendet wird. Der Client fordert über diesen Parameter das Zugriffstoken vom Autorisierungsserver an. |
|`user_code`       | Zeichenfolge | Eine kurze, für den Benutzer angezeigte Zeichenfolge, mit der die Sitzung auf einem sekundären Gerät identifiziert wird.|
|`verification_uri`| URI | Der URI, den der Benutzer mit dem `user_code` besuchen sollte, um sich anzumelden. |
|`verification_uri_complete`| URI | Ein URI, der `user_code` und `verification_uri` kombiniert und für Nicht-Text-Übertragungen an den Benutzer verwendet wird (z. B. über Bluetooth an ein Gerät oder über einen QR-Code).  |
|`expires_in`      | int | Die Anzahl der Sekunden, bevor `device_code` und `user_code` ablaufen. |
|`interval`        | int | Die Anzahl der Sekunden, die der Client zwischen Abrufanforderungen warten soll. |
| `message`        | Zeichenfolge | Eine lesbare Zeichenfolge mit Anweisungen für den Benutzer. Diese kann lokalisiert werden, indem ein **Abfrageparameter** in die Anforderung des Formulars `?mkt=xx-XX` aufgenommen und der entsprechende Sprachkulturcode eingetragen wird. |

## <a name="authenticating-the-user"></a>Authentifizieren des Benutzers

Nach dem Empfang von `user_code` und `verification_uri` zeigt der Client diese für den Benutzer an und weist ihn an, sich unter Verwendung seines Mobiltelefons oder PC-Browsers anzumelden.  Darüber hinaus kann der Client einen QR-Code oder einen ähnlichen Mechanismus verwenden, um `verfication_uri_complete` anzuzeigen, der den Schritt des Eingebens des `user_code` für den Benutzer übernimmt.

Während sich der Benutzer bei dem `verification_uri` authentifiziert, sollte der Client beim `/token`-Endpunkt das angeforderte Token mithilfe des `device_code` abrufen.

``` 
POST https://login.microsoftonline.com/tenant/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8
```

| Parameter | Erforderlich | BESCHREIBUNG|
| -------- | -------- | ---------- |
| `grant_type` | Erforderlich | Muss gleich `urn:ietf:params:oauth:grant-type:device_code` sein.|
| `client_id`  | Erforderlich | Muss mit der in der anfänglichen Anforderung verwendeten `client_id` übereinstimmen. |
| `device_code`| Erforderlich | Der in der Geräteautorisierungsanforderung zurückgegebene `device_code`.  |

### <a name="expected-errors"></a>Erwartete Fehler

Da der Gerätecodeflow ein Abrufprotokoll ist, muss der Client davon ausgehen, Fehler zu erhalten, bevor der Benutzer die Authentifizierung abgeschlossen hat.  

| Error | BESCHREIBUNG | Clientaktion |
| ------ | ----------- | -------------|
| `authorization_pending` | Der Benutzer hat die Authentifizierung nicht abgeschlossen, den Flow aber nicht abgebrochen. | Wiederholen Sie die Anforderung nach mindestens `interval` Sekunden. |
| `authorization_declined` | Der Endbenutzer hat die Autorisierungsanforderung verweigert.| Beenden Sie das Abrufen, und kehren Sie in einen nicht authentifizierten Zustand zurück.  |
| `bad_verification_code`| Der an den `/token`-Endpunkt gesendete `device_code` wurde nicht erkannt. | Stellen Sie sicher, dass der Client den richtigen `device_code` in der Anforderung sendet. |
| `expired_token` | Mindestens `expires_in` Sekunden sind verstrichen, und die Authentifizierung ist mit diesem `device_code` nicht mehr möglich. | Beenden des Abrufens und Wiederherstellen eines nicht authentifizierten Zustands. |

### <a name="successful-authentication-response"></a>Erfolgreiche Authentifizierungsantwort

Eine erfolgreiche Tokenantwort sieht wie folgt aus:

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| Parameter | Format | BESCHREIBUNG |
| --------- | ------ | ----------- |
| `token_type` | Zeichenfolge| Immer „Bearer. |
| `scope` | Durch Leerzeichen getrennte Zeichenfolgen | Wenn ein Zugriffstoken zurückgegeben wurde, werden hierdurch die Bereiche aufgeführt, für die das Zugriffstoken gültig ist. |
| `expires_in`| int | Anzahl der Sekunden, bevor das enthaltene Zugriffstoken gültig ist. |
| `access_token`| Nicht transparente Zeichenfolge | Ausgestellt für die [Bereiche](v2-permissions-and-consent.md), die angefordert wurden.  |
| `id_token`   | JWT | Ausgestellt, wenn der ursprüngliche `scope`-Parameter den `openid`-Bereich enthalten hat.  |
| `refresh_token` | Nicht transparente Zeichenfolge | Ausgestellt, wenn der ursprüngliche `scope`-Parameter `offline_access` enthalten hat.  |

Sie können mit dem Aktualisierungstoken neue Zugriffstoken und Aktualisierungstoken abrufen. Verwenden Sie dazu den in der [Dokumentation für den OAuth-Codeflow](v2-oauth2-auth-code-flow.md#refresh-the-access-token) beschriebenen Flow.  
