---
title: Übersicht über Token – Azure Active Directory B2C
description: Hier finden Sie Informationen zu den in Azure Active Directory B2C verwendeten Token.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a0ad14481673f0061fb0170e60869109c87a6829
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2020
ms.locfileid: "94379785"
---
# <a name="overview-of-tokens-in-azure-active-directory-b2c"></a>Übersicht über Token in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) stellt bei der Verarbeitung der einzelnen [Authentifizierungsflüsse](application-types.md) verschiedene Arten von Sicherheitstoken aus. In diesem Dokument sind das Format, die Sicherheitsmerkmale und der Inhalt aller Tokentypen beschrieben.

## <a name="token-types"></a>Tokentypen

Azure AD B2C unterstützt [das OAuth 2.0-Protokoll und das OpenID Connect-Protokoll](protocols-overview.md), die Token für die Authentifizierung und den sicheren Zugriff auf Ressourcen nutzen. Alle in Azure AD B2C verwendeten Token sind [JSON Web Tokens (JWTs)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) und enthalten Assertionen von Informationen zum Bearer sowie zum Antragsteller des Tokens.

Bei der Kommunikation mit Azure AD B2C werden folgende Token verwendet:

- *ID-Token:* Ein JWT mit Ansprüchen, die Sie zur Identifizierung von Benutzern in Ihrer Anwendung verwenden können. Dieses Token wird sicher in HTTP-Anforderungen für die Kommunikation zwischen zwei Komponenten der gleichen Anwendung oder des gleichen Diensts gesendet. Sie können die Ansprüche nach Bedarf in einem ID-Token verwenden. Im Allgemeinen werden sie in einer Anwendung zum Anzeigen von Kontoinformationen oder zum Treffen von Entscheidungen hinsichtlich der Zugriffssteuerung genutzt. ID-Token sind zwar signiert, aber nicht verschlüsselt. Wenn Ihre Anwendung oder API ein ID-Token empfängt, muss sie die Signatur überprüfen, um die Authentizität des Tokens nachzuweisen. Ihre Anwendung oder API muss außerdem einige Ansprüche im Token überprüfen, um seine Gültigkeit zu belegen. Je nach Szenarioanforderungen können die von einer Anwendung überprüften Ansprüche variieren. Einige allgemeine Anspruchsüberprüfungen muss Ihre Anwendung aber in jedem Szenario durchführen.
- *Zugriffstoken:* Ein JWT mit Ansprüchen, die Sie zur Identifizierung der erteilten Berechtigungen für Ihre APIs verwenden können. Zugriffstoken sind zwar signiert, aber nicht verschlüsselt. Zugriffstoken dienen zum Gewähren von Zugriff auf APIs und Ressourcenserver.  Wenn Ihre API ein Zugriffstoken empfängt, muss sie die Signatur überprüfen, um die Authentizität des Tokens nachzuweisen. Ihre API muss außerdem einige Ansprüche im Token überprüfen, um seine Gültigkeit zu belegen. Je nach Szenarioanforderungen können die von einer Anwendung überprüften Ansprüche variieren. Einige allgemeine Anspruchsüberprüfungen muss Ihre Anwendung aber in jedem Szenario durchführen.
- *Aktualisierungstoken:* Aktualisierungstoken dienen zum Abrufen neuer ID-Token und Zugriffstoken in einem OAuth 2.0-Ablauf. Sie ermöglichen Ihrer Anwendung langfristig Zugriff auf Ressourcen im Auftrag von Benutzern, ohne dass eine Interaktion mit den Benutzern erforderlich ist. Der Inhalt von Aktualisierungstoken ist für Ihre Anwendung nicht zugänglich. Sie werden von Azure AD B2C ausgestellt und können nur von Azure AD B2C untersucht und interpretiert werden. Obwohl sie lange gültig sind, darf beim Schreiben Ihrer Anwendung nicht von der bestimmten Gültigkeitsdauer eines Aktualisierungstokens ausgegangen werden. Aktualisierungstoken können jederzeit aus unterschiedlichen Gründen ungültig werden. Um Gültigkeit eines Aktualisierungstokens zu überprüfen, muss Ihre Anwendung versuchen, das Token einzulösen, indem sie eine Tokenanforderung an Azure AD B2C sendet. Wenn Sie ein Aktualisierungstoken für ein neues Token einlösen, erhalten Sie in der Tokenantwort ein neues Aktualisierungstoken. Speichern Sie das neue Aktualisierungstoken. Es ersetzt das Aktualisierungstoken, das zuvor in der Anforderung verwendet wurde. Dadurch wird sichergestellt, dass Ihre Aktualisierungstoken möglichst lange gültig bleiben. Beachten Sie, dass bei Single-Page-Webanwendungen, die den Autorisierungscodeflow mit PKCE verwenden, das Aktualisierungstoken immer eine Lebensdauer von 24 Stunden hat. [Weitere Informationen zu Sicherheitsaspekten von Aktualisierungstoken im Browser](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser)

## <a name="endpoints"></a>Endpunkte

Eine [registrierte Anwendung](tutorial-register-applications.md) empfängt Token und kommuniziert mit Azure AD B2C, indem sie Anforderungen an folgende Endpunkte sendet:

- `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/authorize`
- `https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/oauth2/v2.0/token`

Sicherheitstoken, die Ihre Anwendung von Azure AD B2C empfängt, können von den Endpunkten `/authorize` oder `/token` stammen. Wenn ID-Token vom Endpunkt `/authorize` bezogen werden, kommt der [implizite Fluss](implicit-flow-single-page-application.md) zur Anwendung. Dieser wird häufig für Benutzer verwendet, die sich bei JavaScript-basierten Webanwendungen anmelden. Wenn ID-Token vom Endpunkt `/token` bezogen werden, kommt der [Autorisierungscodefluss](openid-connect.md#get-a-token) zur Anwendung, bei dem das Token dem Browser verborgen bleibt.

## <a name="claims"></a>Ansprüche

Bei der Verwendung von Azure AD B2C verfügen Sie über eine präzise Kontrolle über den Inhalt Ihrer Token. Sie können [Benutzerflows](user-flow-overview.md) und [benutzerdefinierte Richtlinien](custom-policy-overview.md) verwenden, um bestimmte Gruppen von Benutzerdaten in Ansprüchen zu senden, die für Ihre Anwendung erforderlich sind. Die Ansprüche können Standardeigenschaften wie **displayName** und **emailAddress** enthalten. Diese können von Ihren Anwendungen genutzt werden, um Benutzer und Anforderungen sicher zu authentifizieren.

Die Ansprüche in ID-Token werden in keiner bestimmten Reihenfolge zurückgegeben. Es können jederzeit neue Ansprüche in ID-Token eingeführt werden. Ihre Anwendung darf durch die Einführung neuer Ansprüche nicht beschädigt werden. Sie können auch [benutzerdefinierte Attribute](user-flow-custom-attributes.md) in Ihre Ansprüche einschließen.

Die folgende Tabelle enthält die Ansprüche, die in von Azure AD B2C ausgestellten ID-Token und Zugriffstoken zu erwarten sind:

| Name | Anspruch | Beispielwert | BESCHREIBUNG |
| ---- | ----- | ------------- | ----------- |
| Zielgruppe | `aud` | `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` | Identifiziert den vorgesehenen Empfänger des Tokens. Für Azure AD B2C ist die Zielgruppe die Anwendungs-ID. Ihre Anwendung muss diesen Wert überprüfen und das Token ablehnen, wenn er nicht übereinstimmt. Die Zielgruppe ist synonym mit der Ressource. |
| Issuer (Aussteller) | `iss` |`https://<tenant-name>.b2clogin.com/775527ff-9a37-4307-8b3d-cc311f58d925/v2.0/` | Identifiziert den Sicherheitstokendienst (Security Token Service, STS), der das Token erstellt und zurückgibt. Er identifiziert auch das Verzeichnis, in dem der Benutzer authentifiziert wurde. Ihre Anwendung muss den Ausstelleranspruch überprüfen, um sicherzustellen, dass das Token vom passenden Endpunkt stammt. |
| Ausgestellt um | `iat` | `1438535543` | Die Zeit, zu der das Token ausgestellt wurde (dargestellt als Epochenzeit) |
| Ablaufzeit | `exp` | `1438539443` | Die Zeit, zu der das Token ungültig wird (dargestellt als Epochenzeit). Ihre Anwendung muss anhand dieses Anspruchs die Gültigkeit der Tokenlebensdauer überprüfen. |
| Nicht vor | `nbf` | `1438535543` | Die Zeit, zu der das Token gültig wird (dargestellt als Epochenzeit). Diese Zeit entspricht in der Regel dem Ausstellungszeitpunkt des Tokens. Ihre Anwendung muss anhand dieses Anspruchs die Gültigkeit der Tokenlebensdauer überprüfen. |
| Version | `ver` | `1.0` | Die Version des ID-Tokens, die in Azure AD B2C definiert wurde. |
| Codehash | `c_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Ein Codehash ist nur in einem ID-Token enthalten, wenn das Token zusammen mit einem OAuth 2.0-Autorisierungscode ausgestellt wird. Mithilfe eines Codehashs kann die Authentizität eines Autorisierungscodes überprüft werden. Weitere Informationen zum Ausführen dieser Validierung finden Sie in der [OpenID Connect-Spezifikation](https://openid.net/specs/openid-connect-core-1_0.html).  |
| Zugriffstokenhash | `at_hash` | `SGCPtt01wxwfgnYZy2VJtQ` | Ein Zugriffstokenhash ist nur in einem ID-Token enthalten, wenn das Token zusammen mit einem OAuth 2.0-Zugriffstoken ausgestellt wird. Mithilfe des Zugriffstokenhashs kann die Authentizität eines Zugriffstokens überprüft werden. Weitere Informationen zum Ausführen dieser Validierung finden Sie in der [OpenID Connect-Spezifikation](https://openid.net/specs/openid-connect-core-1_0.html).  |
| Nonce | `nonce` | `12345` | Eine Nonce ist eine Strategie zum Abwehren von Tokenwiedergabeangriffe. Ihre Anwendung kann eine Nonce in einer Autorisierungsanforderung mithilfe des Abfrageparameters `nonce` angeben. Der in der Anforderung angegebene Wert wird nur im Anspruch `nonce` eines ID-Tokens unverändert ausgegeben. Mithilfe dieses Anspruchs kann Ihre Anwendung den Wert anhand des in der Anforderung angegebenen Werts überprüfen. Ihre Anwendung muss diese Überprüfung im Rahmen der ID-Tokenüberprüfung durchführen. |
| Subject | `sub` | `884408e1-2918-4cz0-b12d-3aa027d7563b` | Der Prinzipal, für den das Token Informationen bestätigt (beispielsweise der Benutzer einer Anwendung). Dieser Wert ist unveränderlich und kann nicht erneut zugewiesen oder wiederverwendet werden. Er kann für die sichere Durchführung von Autorisierungsüberprüfungen verwendet werden, z.B. wenn das Token verwendet wird, um auf eine Ressource zuzugreifen. Der Anspruch „Antragsteller“ wird standardmäßig mit der Objekt-ID des Benutzers im Verzeichnis aufgefüllt. |
| Klassenreferenz des Anwendungskontexts | `acr` | Nicht verfügbar | Wird nur mit älteren Richtlinien verwendet. |
| Framework-Vertrauensrichtlinie | `tfp` | `b2c_1_signupsignin1` | Der Name der Richtlinie, die zum Abrufen des ID-Tokens verwendet wurde. |
| Authentifizierungszeit | `auth_time` | `1438535543` | Die Zeit, zu der ein Benutzer seine Anmeldeinformationen zuletzt eingegeben hat (dargestellt als Epochenzeit). Dabei spielt es keine Rolle, ob es sich bei dieser Authentifizierung um eine neue Anmeldung, um eine SSO-Sitzung (Single Sign-On, einmaliges Anmelden) oder um einen anderen Anmeldetyp handelt. `auth_time` ist der letzte Zeitpunkt, zu dem die Anwendung (oder der Benutzer) einen Authentifizierungsversuch für Azure AD B2C initiiert hat. Die verwendete Authentifizierungsmethode wird nicht berücksichtigt. |
| `Scope` | `scp` | `Read`| Die Berechtigungen, die der Ressource für ein Zugriffstoken gewährt werden. Mehrere gewährte Berechtigungen werden jeweils durch ein Leerzeichen voneinander getrennt. |
| Autorisierte Partei | `azp` | `975251ed-e4f5-4efd-abcb-5f1a8f566ab7` | Die **Anwendungs-ID** der Clientanwendung, die die Anforderung initiiert hat. |

## <a name="configuration"></a>Konfiguration

Die folgenden Eigenschaften werden zum [Verwalten der Gültigkeitsdauer von Sicherheitstoken](configure-tokens.md) verwendet, die von Azure AD B2C ausgegeben werden:

- **Lebensdauer von Zugriffs- und ID-Token (Minuten)** : Die Gültigkeitsdauer des OAuth 2.0-Bearertokens, das für den Zugriff auf eine geschützte Ressource verwendet wird Der Standardwert ist 60 Sekunden. Der Mindestwert ist fünf Minuten (einschließlich). Der Höchstwert ist 1.440 Minuten (einschließlich).

- **Lebensdauer des Aktualisierungstokens (Tage):** Die maximale Dauer, während der ein Aktualisierungstoken zum Anfordern eines neuen Zugriffs- oder ID-Tokens verwendet werden kann. Der Zeitraum deckt auch das Abrufen eines neuen Aktualisierungstokens ab, falls Ihrer Anwendung der Bereich `offline_access` gewährt wurde. Der Standardwert ist 14 Tage. Der Mindestwert ist ein Tag (einschließlich). Der Höchstwert ist 90 Tage (einschließlich).

- **Lebensdauer für gleitendes Fenster des Aktualisierungstokens (Tage):** Nach Ablauf dieses Zeitraums muss der Benutzer sich erneut authentifizieren (unabhängig von der Gültigkeitsdauer des zuletzt von der Anwendung abgerufenen Aktualisierungstokens). Es kann nur bereitgestellt werden, wenn die Option auf **Begrenzt** festgelegt ist. Sein Wert muss mindestens dem Wert **Lebensdauer des Aktualisierungstokens (Tage)** entsprechen. Wenn die Option auf **Unbegrenzt** festgelegt ist, können Sie keinen bestimmten Wert angeben. Der Standardwert ist 90 Tage. Der Mindestwert ist ein Tag (einschließlich). Der Höchstwert ist 365 Tage (einschließlich).

Mit diesen Eigenschaften werden die folgenden Anwendungsfälle ermöglicht:

- Ermöglichen Sie einem Benutzer, unbegrenzt bei einer mobilen Anwendung angemeldet zu bleiben, solange er ständig in der Anwendung aktiv ist. Sie können die Option **Lebensdauer für gleitendes Fenster des Aktualisierungstokens (Tage)** in Ihrem Benutzerflow für die Anmeldung auf **Unbegrenzt** festlegen.
- Erfüllen Sie die Sicherheits- und Complianceanforderungen Ihrer Branche durch Festlegung der entsprechenden Zugriffstoken-Gültigkeitsdauer.

Diese Einstellungen sind für Benutzerflows zur Kennwortzurücksetzung nicht verfügbar.

## <a name="compatibility"></a>Kompatibilität

Die folgenden Eigenschaften werden zum [Verwalten der Tokenkompatibilität](configure-tokens.md) verwendet:

- **Ausstelleranspruch (iss)** : Diese Eigenschaft gibt den Azure AD B2C-Mandanten an, der das Token ausgestellt hat. Standardwert: `https://<domain>/{B2C tenant GUID}/v2.0/`. Der Wert `https://<domain>/tfp/{B2C tenant GUID}/{Policy ID}/v2.0/` enthält IDs für den Azure AD B2C-Mandanten und den in der Tokenanforderung verwendeten Benutzerflow. Verwenden Sie diesen Wert, wenn Ihre Anwendung oder Bibliothek Azure AD B2C benötigt, um die [OpenID Connect Discovery 1.0-Spezifikationen](https://openid.net/specs/openid-connect-discovery-1_0.html) zu erfüllen.

- **Antragstelleranspruch (sub)** : Diese Eigenschaft gibt die Entität an, für die das Token Informationen bestätigt. Der Standardwert ist **ObjectID**. Dieser Wert füllt den Anspruch `sub` im Token mit der Objekt-ID des Benutzers auf. Der Wert **Nicht unterstützt** wird nur zur Abwärtskompatibilität angegeben. Es empfiehlt sich, baldmöglichst zu **ObjectID** zu wechseln.

- **Anspruch zur Darstellung der Richtlinien-ID:** Mit dieser Eigenschaft wird der Anspruchstyp angegeben, in den der in der Tokenanforderung verwendete Richtlinienname eingefügt wird. Standardwert: `tfp`. Der Wert `acr` wird nur zur Abwärtskompatibilität angegeben.

## <a name="pass-through"></a>Pass-Through

Zu Beginn einer User Journey erhält Azure AD B2C ein Zugriffstoken von einem Identitätsanbieter. Azure AD B2C verwendet dieses Token, um Informationen zum Benutzer abzurufen. Sie [aktivieren einen Anspruch in Ihrem Benutzerflow](idp-pass-through-user-flow.md) oder [definieren einen Anspruch in Ihrer benutzerdefinierten Richtlinie](idp-pass-through-custom.md), um das Token an die Anwendungen zu übergeben, die Sie in Azure AD B2C registrieren. Ihre Anwendung muss einen [empfohlenen Benutzerflow](user-flow-versions.md) verwenden, um von der Übergabe des Tokens als Anspruch profitieren zu können.

Azure AD B2C unterstützt derzeit nur die Übergabe des Zugriffstokens für OAuth 2.0-Identitätsanbieter. Hierzu zählen unter anderem Facebook und Google. Für alle weiteren Identitätsanbieter wird ein leerer Anspruch zurückgegeben.

## <a name="validation"></a>Überprüfen

Bei der Überprüfung eines Tokens muss Ihre Anwendung sowohl die Signatur als auch die Ansprüche des Tokens überprüfen. Für die Überprüfung von JWTs sind je nach gewünschter Sprache zahlreiche Open-Source-Bibliotheken verfügbar. Es empfiehlt sich, diese Optionen zu erkunden, anstatt eine eigene Validierungslogik zu implementieren.

### <a name="validate-signature"></a>Überprüfen der Signatur

Ein JWT enthält drei Segmente: einen *Header*, einen *Textteil* und eine *Signatur*. Mit dem Signatursegment kann die Authentizität des Tokens überprüft werden, sodass es für Ihre Anwendung als vertrauenswürdig eingestuft werden kann. Azure AD B2C-Token werden mit branchenüblichen asymmetrischen Verschlüsselungsalgorithmen signiert, z.B. RSA 256.

Der Header des Tokens enthält Informationen zum Schlüssel und zur Verschlüsselungsmethode, die zum Signieren des Tokens verwendet wird:

```
{
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
}
```

Der Wert des Anspruchs **alg** ist der Algorithmus, mit dem das Token signiert wurde. Der Wert des Anspruchs **kid** ist der öffentliche Schlüssel, mit dem das Token signiert wurde. Azure AD B2C kann ein Token jederzeit mithilfe eines beliebigen Paars aus öffentlichen und privaten Schlüsseln aus einer Gruppe signieren. Die möglichen Schlüsselgruppen werden von Azure AD B2C regelmäßig rotiert. Ihre Anwendung muss daher über eine automatische Verarbeitung dieser Schlüsseländerungen verfügen. Die von Azure AD B2C verwendeten öffentlichen Schlüssel müssen alle 24 Stunden auf Änderungen überprüft werden. Um unerwartete Schlüsseländerungen zu behandeln, sollte Ihre Anwendung so geschrieben sein, dass die öffentlichen Schlüssel erneut abgerufen werden, wenn ein unerwarteter **kid**-Wert empfangen wird.

Azure AD B2C verfügt über einen OpenID Connect-Metadatenendpunkt. Über diesen Endpunkt können Anwendungen zur Laufzeit Informationen zu Azure AD B2C anfordern. Diese Informationen umfassen Endpunkte, Tokeninhalte und Token-Signaturschlüssel. Ihr Azure AD B2C-Mandant enthält ein JSON-Metadatendokument für jede Richtlinie. Beim Metadatendokument handelt es sich um ein JSON-Objekt, das zahlreiche nützliche Informationen enthält, Die Metadaten enthalten **jwks_uri**, um den Ort anzugeben, an dem sich die Gruppe von öffentlichen Schlüsseln zum Signieren von Token befinden. Dieser Ort ist hier angegeben. Es wird jedoch empfohlen, ihn dynamisch mithilfe des Metadatendokuments abzurufen und dabei **jwks_uri** zu analysieren:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/b2c_1_signupsignin1/discovery/v2.0/keys
```
Das JSON-Dokument unter dieser URL enthält alle Informationen zu den zu einem bestimmten Zeitpunkt verwendeten öffentlichen Schlüsseln. Ihre App kann mit dem Anspruch `kid` im JWT-Header auswählen, welcher öffentliche Schlüssel im JSON-Dokument zum Signieren eines bestimmten Tokens verwendet wird. Sie kann anschließend die Signaturüberprüfung mithilfe des korrekten öffentlichen Schlüssels und des angegebenen Algorithmus ausführen.

Das Metadatendokument für die Richtlinie `B2C_1_signupsignin1` im Mandaten `contoso.onmicrosoft.com` befindet sich hier:

```
https://contoso.b2clogin.com/contoso.onmicrosoft.com/b2c_1_signupsignin1/v2.0/.well-known/openid-configuration
```

Sie haben zwei Optionen, um zu ermitteln, welche Richtlinie zum Signieren eines Tokens verwendet wurde (und wo die Metadaten angefordert werden können). Zunächst einmal ist der Richtlinienname im Token im `tfp`-Anspruch (Standard) oder im `acr`-Anspruch (wie konfiguriert) enthalten. Sie können Ansprüche aus dem Hauptteil des JWT analysieren, indem Sie eine Base64-Decodierung auf den Hauptteil anwenden und die sich ergebende JSON-Zeichenfolge deserialisieren. Der Anspruch `tfp` bzw. `acr` ist der Name der Richtlinie, die zum Ausstellen des Tokens verwendet wurde. Die andere Option besteht darin, die Richtlinie beim Übermitteln der Anforderung im Wert des Parameters `state` zu codieren und später zu decodieren, um die verwendete Richtlinie zu bestimmen. Beide Methoden sind gültig.

Die Beschreibung der Signaturüberprüfung geht über den Rahmen dieses Dokuments hinaus. Hilfreiche Informationen zur Tokenvalidierung stehen jedoch in zahlreichen Open-Source-Bibliotheken zur Verfügung.

### <a name="validate-claims"></a>Überprüfen von Ansprüchen

Wenn Ihre Anwendung oder API ein ID-Token empfängt, muss sie auch einige Überprüfungen anhand der Ansprüche im ID-Token durchführen. Folgende Ansprüche müssen überprüft werden:

- **audience**: Hiermit wird überprüft, ob das ID-Token für Ihre Anwendung bestimmt ist.
- **not before** und **expiration time**: Hiermit wird überprüft, ob das ID-Token abgelaufen ist.
- **issuer**: Hiermit wird überprüft, ob das Token von Azure AD B2C für Ihre Anwendung ausgestellt wurde.
- **nonce**: Eine Strategie zur Abwehr von Tokenwiedergabeangriffen.

Eine vollständige Liste mit Validierungen, die von Ihrer Anwendung ausgeführt werden müssen, finden Sie in der [OpenID Connect-Spezifikation](https://openid.net).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum [Verwenden von Zugriffstoken](access-tokens.md).

