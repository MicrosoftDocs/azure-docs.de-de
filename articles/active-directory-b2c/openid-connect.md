---
title: Webanmeldungen mit OpenID Connect – Azure Active Directory B2C
description: Erstellen von Webanwendungen mit dem OpenID Connect-Authentifizierungsprotokoll in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: dbfeefc14059785ba82cbf245a60e5e72759db76
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94840405"
---
# <a name="web-sign-in-with-openid-connect-in-azure-active-directory-b2c"></a>Webanmeldungen mit OpenID Connect in Azure Active Directory B2C

OpenID Connect ist ein Authentifizierungsprotokoll auf Grundlage von OAuth 2.0, mit dem Benutzer sicher bei Webanwendungen angemeldet werden können. Mithilfe der Azure Active Directory B2C (Azure AD B2C)-Implementierung von OpenID Connect können Sie die Registrierung, die Anmeldung und die sonstige Identitätsverwaltung Ihrer Webanwendungen nach Azure Active Directory (Azure AD) auslagern. In diesem Leitfaden wird dies sprachunabhängig erläutert. Er beschreibt das Senden und Empfangen von HTTP-Nachrichten ohne Verwendung unserer Open Source-Bibliotheken.

[OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) erweitert das OAuth 2.0-Protokoll für die *Autorisierung*, damit es als Protokoll für die *Authentifizierung* verwendet werden kann. Authentifizierungsprotokoll ermöglicht Ihnen das Ausführen von SSO (Einmaliges Anmelden). Dabei wird das Konzept eines *ID-Tokens* eingeführt, mit dem der Client die Identität des Benutzers überprüfen und grundlegende Profilinformationen über den Benutzer erhalten kann.

Da OAuth 2.0 erweitert wird, können Anwendungen zudem *Zugriffstoken* sicher abrufen. Mit Zugriffstoken können Sie auf die Ressourcen zugreifen, die von einem [Autorisierungsserver](protocols-overview.md) gesichert werden. OpenID Connect wird für Webanwendungen empfohlen, die auf einem Server gehostet werden und auf die über einen Browser zugegriffen wird. Weitere Informationen zu Token finden Sie in der [Übersicht über Token in Azure Active Directory B2C](tokens-overview.md).

Azure AD B2C erweitert das OpenID Connect-Standardprotokoll, sodass mehr als nur eine einfache Authentifizierung und Autorisierung erfolgt. Der [Benutzerflowparameter](user-flow-overview.md) wird eingeführt, mit dem Sie OpenID Connect zum Hinzufügen von Benutzeroberflächen (z.B. für die Registrierung, Anmeldung und Profilverwaltung) zu Ihrer Anwendung verwenden können.

## <a name="send-authentication-requests"></a>Übermitteln von Authentifizierungsanforderungen

Wenn Ihre Webanwendung den Benutzer authentifizieren und einen Benutzerflow ausführen muss, kann sie den Benutzer an den `/authorize`-Endpunkt weiterleiten. Der Benutzer führt Aktionen in Abhängigkeit vom Benutzerflow durch.

In dieser Anforderung gibt der Client die Berechtigungen, die er vom Benutzer benötigt, im Parameter `scope` an, und er gibt den auszuführenden Benutzerflow an. Um sich anzusehen, wie diese Anforderung funktioniert, fügen Sie sie in einem Browser ein und führen sie aus. Ersetzen Sie `{tenant}` durch den Namen Ihres Mandanten. Ersetzen Sie `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` durch die App-ID der Anwendung, die Sie zuvor in Ihrem Mandanten registriert haben. Ändern Sie auch den Richtliniennamen (`{policy}`) in den Richtliniennamen in Ihrem Mandanten, z. B. `b2c_1_sign_in`.

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code+id_token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=form_post
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| Parameter | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| {tenant} | Ja | Name des Azure AD B2C-Mandanten. |
| {policy} | Ja | Der auszuführende Benutzerflow. Geben Sie den Namen eines Benutzerflows an, den Sie in Ihrem Azure AD B2C-Mandanten erstellt haben. Beispiel: `b2c_1_sign_in`, `b2c_1_sign_up` oder `b2c_1_edit_profile`. |
| client_id | Ja | Die Anwendungs-ID, die das [Azure-Portal](https://portal.azure.com/) Ihrer Anwendung zugewiesen hat. |
| nonce | Ja | Ein Wert in der Anforderung, der von der Anwendung generiert wird und im resultierenden ID-Token als Anspruch enthalten ist. Die Anwendung kann diesen Wert dann überprüfen, um die Gefahr von Token-Replay-Angriffen zu vermindern. Der Wert ist in der Regel eine zufällige, eindeutige Zeichenfolge, die verwendet werden kann, um den Ursprung der Anforderung zu identifizieren. |
| response_type | Ja | Muss ein ID-Token für OpenID Connect enthalten. Wenn Ihre Webanwendung auch Token für den Aufruf einer Web-API benötigt, können Sie `code+id_token` verwenden. |
| scope | Ja | Eine durch Leerzeichen getrennte Liste von Bereichen. Der `openid`-Bereich gibt eine Berechtigung für das Anmelden des Benutzers und das Abrufen von Daten über den Benutzer in Form von ID-Token an. Der `offline_access`-Bereich ist für Webanwendungen optional. Er gibt an, dass Ihre Anwendung ein *Aktualisierungstoken* für den dauerhaften Zugriff auf Ressourcen benötigt. |
| prompt | Nein  | Der Typ der erforderlichen Benutzerinteraktion. Zu diesem Zeitpunkt ist der einzige gültige Wert `login`, der den Benutzer zur Eingabe von Anmeldeinformationen für diese Anforderung zwingt. |
| redirect_uri | Nein  | Der `redirect_uri`-Parameter der Anwendung, in dem Authentifizierungsantworten gesendet und von der Anwendung empfangen werden können. Er muss genau mit einem der `redirect_uri`-Parameter übereinstimmen, die Sie im Azure-Portal registriert haben, mit dem Unterschied, dass er URL-codiert sein muss. |
| response_mode | Nein  | Die Methode, die zum Senden des resultierenden Autorisierungscodes zurück an Ihre Anwendung verwendet wird. Es kann sich um `query`, `form_post`, oder `fragment` handeln.  Der `form_post`-Antwortmodus wird empfohlen, für die optimale Sicherheit. |
| state | Nein  | Ein in der Anforderung enthaltener Wert, der ebenfalls in der Tokenantwort zurückgegeben wird. Es kann sich um eine Zeichenfolge mit jedem beliebigen Inhalt handeln. Ein zufällig generierter eindeutiger Wert wird normalerweise verwendet, um websiteübergreifende Anforderungsfälschungsangriffe zu verhindern. Der Status wird auch zum Codieren von Informationen über den Status des Benutzers in der Anwendung vor der Authentifizierungsanforderung verwendet, z.B. für Informationen zu der Seite, die der Benutzer besucht hat. |

An diesem Punkt wird der Benutzer aufgefordert, den Workflow abzuschließen. Der Benutzer muss möglicherweise seinen Benutzernamen und sein Kennwort eingeben, sich mit einer Social-Media-Identität anmelden oder sich für das Verzeichnis registrieren. Je nach Definition des Benutzerflows ist eine andere Zahl von Schritten auszuführen.

Nachdem der Benutzer den Benutzerflow abgeschlossen hat, wird im angegebenen `redirect_uri`-Parameter eine Antwort an die Anwendung zurückgegeben; hierbei wird die im `response_mode`-Parameter angegebene Methode verwendet. Die Antwort ist in jedem der vorangegangenen Fälle immer gleich, unabhängig vom jeweiligen Benutzerflow.

Eine erfolgreiche Antwort mit `response_mode=fragment` sieht wie folgt aus:

```http
GET https://aadb2cplayground.azurewebsites.net/#
id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | BESCHREIBUNG |
| --------- | ----------- |
| id_token | Das ID-Token, das die Anwendung angefordert hat. Sie können mit dem ID-Token die Identität des Benutzers überprüfen und eine Sitzung mit dem Benutzer beginnen. |
| code | Der Autorisierungscode, den die Anwendung angefordert hat, wenn Sie `response_type=code+id_token` verwendet haben. Die Anwendung kann mit dem Autorisierungscode ein Zugriffstoken für eine Zielressource anfordern. Autorisierungscodes laufen in der Regel nach etwa zehn Minuten ab. |
| state | Wenn ein Parameter `state` in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die Anwendung sollte überprüfen, ob die `state`-Werte in der Anforderung und in der Antwort identisch sind. |

Fehlerantworten können auch an den `redirect_uri`-Parameter gesendet werden, damit die Anwendung diese angemessen behandeln kann:

```http
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | BESCHREIBUNG |
| --------- | ----------- |
| error | Ein Code, mit dem die Typen der auftretenden Fehler klassifiziert werden können. |
| error_description | Eine spezifische Fehlermeldung, mit der Sie die Hauptursache eines Authentifizierungsfehlers identifizieren können. |
| state | Wenn ein Parameter `state` in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die Anwendung sollte überprüfen, ob die `state`-Werte in der Anforderung und in der Antwort identisch sind. |

## <a name="validate-the-id-token"></a>Überprüfen des ID-Tokens

Das Empfangen eines ID-Tokens reicht nicht aus, um den Benutzer zu authentifizieren. Validieren Sie die Signatur des ID-Tokens, und überprüfen Sie die Ansprüche im Token gemäß den Anforderungen Ihrer Anwendung. Azure AD B2C verwendet [JSON-Webtoken (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) und die Verschlüsselung mit öffentlichem Schlüssel, um Token zu signieren und deren Gültigkeit zu überprüfen. Viele Open Source-Bibliotheken sind für die Überprüfung von JWTs für unterschiedliche Sprachen verfügbar. Wir empfehlen die Verwendung einer dieser Optionen, anstatt eine eigene Validierungslogik zu implementieren.

Azure AD B2C verfügt über einen OpenID Connect-Metadatenendpunkt, mit dem die Anwendung zur Laufzeit Informationen über Azure AD B2C abrufen kann. Diese Informationen umfassen Endpunkte, Tokeninhalte und Token-Signaturschlüssel. Es gibt ein JSON-Metadatendokument für jeden Benutzerflow in Ihrem B2C-Mandanten. Das Metadatendokument für den Benutzerflow `b2c_1_sign_in` in `fabrikamb2c.onmicrosoft.com` befindet sich beispielsweise unter:

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

Eine der Eigenschaften dieses Konfigurationsdokuments ist `jwks_uri`, deren Wert für den gleichen Benutzerflow wie folgt lautet:

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

Es gibt zwei Möglichkeiten zum Ermitteln, welcher Benutzerflow zum Signieren eines ID-Tokens verwendet wurde (und wo die Metadaten abgerufen werden können). Zunächst einmal ist der Benutzerflowname im `acr`-Anspruch im ID-Token enthalten. Die andere Möglichkeit besteht darin, den Benutzerflow beim Übermitteln der Anforderung im Wert des Parameters `state` zu verschlüsseln und später zu entschlüsseln, um zu bestimmen, welcher Benutzerflow verwendet wurde. Beide Methoden sind gültig.

Nachdem Sie das Metadatendokument aus dem OpenID Connect-Metadatenendpunkt erhalten haben, können Sie die öffentlichen RSA-256-Schlüssel zum Überprüfen der Signatur des ID-Tokens verwenden. Es gibt möglicherweise mehrere Schlüssel an diesem Endpunkt. Sie werden jeweils durch einen `kid`-Anspruch identifiziert. Der Header des ID-Tokens enthält außerdem einen `kid`-Anspruch, der anzeigt, welcher Schlüssel zum Signieren des ID-Tokens verwendet wurde.

Sie müssen den öffentlichen Schlüssel mithilfe von Exponent (e) und Modulus (n) generieren, um die Token von Azure AD B2C zu überprüfen. Sie müssen festlegen, wie dies in der jeweiligen Programmiersprache entsprechend durchzuführen ist. Die offizielle Dokumentation zur Generierung öffentlicher Schlüssel mit dem RSA-Protokoll finden Sie hier: https://tools.ietf.org/html/rfc3447#section-3.1

Nachdem Sie die Signatur des ID-Tokens validiert haben, müssen Sie einige Ansprüche überprüfen, z.B.: Beispiel:

- Überprüfen Sie den `nonce`-Anspruch, um Tokenwiederholungsangriffe zu verhindern. Dessen Wert sollte dem in der Anmeldeanforderung angegebenen Wert entsprechen.
- Überprüfen Sie den `aud`-Anspruch, um sicherzustellen, dass das ID-Token für Ihre Anwendung ausgegeben wurde. Der Wert sollte der Anwendungs-ID der Anwendung entsprechen.
- Überprüfen Sie die Ansprüche `iat` und `exp`, um sicherzustellen, dass das ID-Token nicht abgelaufen ist.

Es gibt auch einige weitere Überprüfungen, die Sie durchführen sollten. Diese Überprüfungen werden ausführlich in der [OpenID Connect Core Spec (Core-Spezifikation des OpenID Connect) ](https://openid.net/specs/openid-connect-core-1_0.html) beschrieben. Sie können je nach Szenario auch zusätzliche Ansprüche überprüfen. Einige allgemeinen Überprüfungen umfassen:

- Sicherstellen, dass sich der Benutzer/die Organisation für die Anwendung registriert hat.
- Sicherstellen, dass der Benutzer über eine ordnungsgemäße Autorisierung und die richtigen Berechtigungen verfügt.
- Sicherstellen, dass eine bestimmte Authentifizierungsmethode verwendet wird, z. B. Azure AD Multi-Factor Authentication.

Nachdem Sie das ID-Token vollständig überprüft haben, können Sie eine Sitzung mit dem Benutzer beginnen. Verwenden Sie die Ansprüche im ID-Token, um Informationen über den Benutzer in Ihrer Anwendung zu erhalten. Die Verwendung dieser Information umfasst die Anzeige, Datensätze und Autorisierung.

## <a name="get-a-token"></a>Abrufen von Token

Wenn Ihre Webanwendung nur Benutzerflows ausführen soll, können Sie die nächsten Abschnitte überspringen. Diese Abschnitte gelten nur für Webanwendungen, die authentifizierte Aufrufe an eine Web-API durchführen müssen und die auch von Azure AD B2C geschützt werden.

Sie können den erhaltenen Autorisierungscode (mit `response_type=code+id_token`) für ein Token für die gewünschte Ressource einlösen, indem Sie eine `POST`-Anforderung an den `/token`-Endpunkt senden. In Azure AD B2C können Sie [Zugriffstoken für andere APIs](access-tokens.md#request-a-token) wie gewohnt anfordern, indem Sie ihre Bereiche in der Anforderung angeben.

Sie haben auch die Möglichkeit, ein Zugriffstoken für die Web-API Ihres App-Back-Ends anzufordern, indem Sie die Client-ID der App als angeforderten Bereich verwenden. Dies führt dazu, dass ein Zugriffstoken mit dieser Client-ID als Zielgruppe erstellt wird:

```http
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parameter | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| {tenant} | Ja | Name des Azure AD B2C-Mandanten. |
| {policy} | Ja | Der Benutzerflow, der zum Abrufen des Autorisierungscodes verwendet wurde. Sie können in dieser Anforderung keinen anderen Benutzerflow verwenden. Fügen Sie diesen Parameter in der Abfragezeichenfolge hinzu, nicht im POST-Text. |
| client_id | Ja | Die Anwendungs-ID, die das [Azure-Portal](https://portal.azure.com/) Ihrer Anwendung zugewiesen hat. |
| client_secret | Ja, in Web-Apps | Der geheime Schlüssel der Anwendung, der im [Azure-Portal](https://portal.azure.com/) generiert wurde. Geheime Client-Schlüssel werden in diesem Flow für Web-App-Szenarien verwendet, in denen der Client einen geheimen Client-Schlüssel sicher speichern kann. Bei Szenarios mit nativen Apps (öffentlicher Client) können geheime Clientschlüssel nicht sicher gespeichert werden, daher werden sie nicht für diesen Flow verwendet. Wenn Sie einen geheimen Client-Schlüssel verwenden, ändern Sie ihn regelmäßig. |
| code | Ja | Der Autorisierungscode, den Sie am Anfang des Benutzerflows erhalten haben. |
| grant_type | Ja | Der Berechtigungstyp, der für den Autorisierungscodefluss `authorization_code` lauten muss. |
| redirect_uri | Ja | Der `redirect_uri`-Parameter der Anwendung, bei der Sie den Autorisierungscode erhalten haben. |
| scope | Nein  | Eine durch Leerzeichen getrennte Liste von Bereichen. Der `openid`-Bereich gibt eine Berechtigung für das Anmelden des Benutzers und das Abrufen von Daten über den Benutzer in Form von id_token-Parametern an. Damit können Sie Token an die Back-End-Web-API ihrer Anwendung übermitteln, die durch dieselbe Anwendungs-ID wie der Client dargestellt wird. Der `offline_access`-Bereich gibt an, dass Ihre Anwendung ein Aktualisierungstoken für den dauerhaften Zugriff auf Ressourcen benötigt. |

Eine erfolgreiche Token-Antwort sieht wie folgt aus:

```json
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| Parameter | BESCHREIBUNG |
| --------- | ----------- |
| not_before | Der Zeitpunkt in Epochenzeit, ab dem das Token gültig ist. |
| token_type | Der Wert des Tokentyps. `Bearer` ist der einzige Typ, der unterstützt wird. |
| access_token | Das signierte JWT-Token, das Sie angefordert haben. |
| scope | Die Bereiche, für die das Token gültig ist. |
| expires_in | Gibt an, wie lange das Zugriffstoken gültig ist (in Sekunden). |
| refresh_token | Ein Aktualisierungstoken von OAuth 2.0. Die Anwendung kann dieses Token verwenden, um nach Ablauf des aktuellen Tokens zusätzliche Token zu erhalten. Mit Aktualisierungstoken kann der Zugriff auf Ressourcen für längere Zeit beibehalten werden. Der `offline_access`-Bereich muss sowohl für die Autorisierung als auch in den Tokenanforderungen verwendet werden, um ein Aktualisierungstoken zu erhalten. |

Fehlerantworten sehen aus wie folgt:

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | BESCHREIBUNG |
| --------- | ----------- |
| error | Ein Code, mit dem Typen der auftretenden Fehler klassifiziert werden können. |
| error_description | Eine Meldung, anhand derer Sie die Hauptursache eines Authentifizierungsfehlers identifizieren können. |

## <a name="use-the-token"></a>Verwenden des Tokens

Nachdem Sie ein Zugriffstoken erhalten haben, können Sie das Token für Anforderungen an die Back-End-Web-APIs verwenden, indem Sie es in den -`Authorization`Header einfügen:

```http
GET /tasks
Host: mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="refresh-the-token"></a>Aktualisieren des Tokens

ID-Token laufen nach kurzer Zeit ab. Aktualisieren Sie die Token nach deren Ablauf, um weiterhin auf Ressourcen zugreifen zu können. Zum Aktualisieren eines Tokens übermitteln Sie eine weitere `POST`-Anforderung an den `/token`-Endpunkt. Geben Sie diesmal den `refresh_token`-Parameter anstelle von `code` an:

```http
POST {tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/token HTTP/1.1
Host: {tenant}.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=openid offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parameter | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| {tenant} | Ja | Name des Azure AD B2C-Mandanten. |
| {policy} | Ja | Der Benutzerflow, der zum Abrufen des ursprünglichen Aktualisierungstokens verwendet wurde. Sie können in dieser Anforderung keinen anderen Benutzerflow verwenden. Fügen Sie diesen Parameter in der Abfragezeichenfolge hinzu, nicht im POST-Text. |
| client_id | Ja | Die Anwendungs-ID, die das [Azure-Portal](https://portal.azure.com/) Ihrer Anwendung zugewiesen hat. |
| client_secret | Ja, in Web-Apps | Der geheime Schlüssel der Anwendung, der im [Azure-Portal](https://portal.azure.com/) generiert wurde. Geheime Client-Schlüssel werden in diesem Flow für Web-App-Szenarien verwendet, in denen der Client einen geheimen Client-Schlüssel sicher speichern kann. Bei Szenarios mit nativen Apps (öffentlicher Client) können geheime Clientschlüssel nicht sicher gespeichert werden, daher werden sie nicht für diesen Aufruf verwendet. Wenn Sie einen geheimen Client-Schlüssel verwenden, ändern Sie ihn regelmäßig. |
| grant_type | Ja | Der Berechtigungstyp, der für diesen Teil des Autorisierungscodeflusses `refresh_token` lauten muss. |
| refresh_token | Ja | Das ursprüngliche Aktualisierungstoken, das im zweiten Teil des Flows erhalten wurde. Der `offline_access`-Bereich muss sowohl für die Autorisierung als auch in den Tokenanforderungen verwendet werden, um ein Aktualisierungstoken zu erhalten. |
| redirect_uri | Nein  | Der `redirect_uri`-Parameter der Anwendung, bei der Sie den Autorisierungscode erhalten haben. |
| scope | Nein  | Eine durch Leerzeichen getrennte Liste von Bereichen. Der `openid`-Bereich gibt eine Berechtigung für das Anmelden des Benutzers und das Abrufen von Daten über den Benutzer in Form von ID-Token an. Damit können Sie Token an die Back-End-Web-API Ihrer Anwendung senden, die durch dieselbe Anwendungs-ID wie der Client dargestellt wird. Der `offline_access`-Bereich gibt an, dass Ihre Anwendung ein Aktualisierungstoken für den dauerhaften Zugriff auf Ressourcen benötigt. |

Eine erfolgreiche Token-Antwort sieht wie folgt aus:

```json
{
    "not_before": "1442340812",
    "token_type": "Bearer",
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "scope": "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
    "expires_in": "3600",
    "refresh_token": "AAQfQmvuDy8WtUv-sd0TBwWVQs1rC-Lfxa_NDkLqpg50Cxp5Dxj0VPF1mx2Z...",
}
```

| Parameter | BESCHREIBUNG |
| --------- | ----------- |
| not_before | Der Zeitpunkt in Epochenzeit, ab dem das Token gültig ist. |
| token_type | Der Wert des Tokentyps. `Bearer` ist der einzige Typ, der unterstützt wird. |
| access_token | Das signierte JWT-Token, das angefordert wurde. |
| scope | Der Bereich, für den das Token gültig ist. |
| expires_in | Gibt an, wie lange das Zugriffstoken gültig ist (in Sekunden). |
| refresh_token | Ein Aktualisierungstoken von OAuth 2.0. Die Anwendung kann dieses Token verwenden, um nach Ablauf des aktuellen Tokens zusätzliche Token zu erhalten. Mit Aktualisierungstoken kann der Zugriff auf Ressourcen für längere Zeit beibehalten werden. |

Fehlerantworten sehen aus wie folgt:

```json
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | BESCHREIBUNG |
| --------- | ----------- |
| error | Ein Code, mit dem Typen der auftretenden Fehler klassifiziert werden können. |
| error_description | Eine Meldung, anhand derer Sie die Hauptursache eines Authentifizierungsfehlers identifizieren können. |

## <a name="send-a-sign-out-request"></a>Senden einer Abmeldeanforderung

Wenn Sie den Benutzer bei der Anwendung abmelden möchten, reicht es nicht aus, die Cookies der Anwendung zu löschen oder die Sitzung mit dem Benutzer auf andere Weise zu beenden. Leiten Sie den Benutzer für die Abmeldung zu Azure AD B2C um. Wenn Sie dies versäumen, kann sich der Benutzer möglicherweise erneut bei Ihrer Anwendung authentifizieren, ohne die Anmeldeinformationen erneut eingeben zu müssen. Weitere Informationen finden Sie unter [Azure AD B2C-Sitzung](session-overview.md).

Um den Benutzer abzumelden, leiten Sie ihn an den `end_session`-Endpunkt um, der im oben beschriebenen OpenID Connect-Metadatendokument aufgeführt wird:

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Fjwt.ms%2F
```

| Parameter | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| {tenant} | Ja | Name des Azure AD B2C-Mandanten. |
| {policy} | Ja | Der Benutzerflow, den Sie zum Abmelden des Benutzers von der Anwendung verwenden möchten. |
| id_token_hint| Nein  | Ein zuvor ausgestelltes ID-Token, das an den Abmelde-Endpunkt als Hinweis bezüglich der aktuellen authentifizierten Sitzung des Endbenutzers mit dem Client übergeben werden soll. Der `id_token_hint` stellt sicher, dass der `post_logout_redirect_uri` eine registrierte Antwort-URL in Ihren Azure AD B2C-Anwendungseinstellungen darstellt. Weitere Informationen finden Sie unter [Sichern der Umleitung beim Abmelden](#secure-your-logout-redirect). |
| client_id | Nein* | Die Anwendungs-ID, die das [Azure-Portal](https://portal.azure.com/) Ihrer Anwendung zugewiesen hat.<br><br>\**Dies ist erforderlich, wenn die `Application`-Isolation-SSO-Konfiguration verwendet wird und _ID-Token erforderlich_ in der Abmeldeanforderung auf `No` festgelegt ist.* |
| post_logout_redirect_uri | Nein  | Die URL, an die der Benutzer nach erfolgreicher Abmeldung umgeleitet werden soll. Wenn sie nicht angegeben ist, gibt Azure AD B2C eine generische Nachricht an den Benutzer aus. Wenn Sie keinen `id_token_hint` angeben, sollten Sie diese URL nicht als Antwort-URL in Ihren Azure AD B2C-Anwendungseinstellungen registrieren. |
| state | Nein  | Wenn ein Parameter `state` in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die Anwendung sollte überprüfen, ob die `state`-Werte in der Anforderung und in der Antwort identisch sind. |

### <a name="secure-your-logout-redirect"></a>Sichern der Umleitung beim Abmelden

Nach der Abmeldung wird der Benutzer an den im `post_logout_redirect_uri`-Parameter angegebenen URI umgeleitet, ungeachtet der Antwort-URLs, die für die Anwendung angegeben wurden. Wenn jedoch ein gültiger `id_token_hint`-Wert übergeben wird und die Option **ID-Token in Abmeldeanforderungen erforderlich** aktiviert ist, überprüft Azure AD B2C, ob der Wert von `post_logout_redirect_uri` einem der für die Anwendung konfigurierten Umleitungs-URIs entspricht, bevor die Umleitung ausgeführt wird. Wenn keine entsprechende Antwort-URL für die Anwendung konfiguriert ist, wird eine Fehlermeldung angezeigt, und der Benutzer wird nicht umgeleitet.

Informationen dazu, wie Sie festlegen, dass ein ID-Token in Abmeldeanforderungen erforderlich ist, finden Sie unter [Konfigurieren des Sitzungsverhaltens in Azure Active Directory B2C](session-behavior-custom-policy.md#secure-your-logout-redirect) und [Konfigurieren des Sitzungsverhaltens mit benutzerdefinierten Richtlinien in Azure Active Directory B2C](session-behavior-custom-policy.md#secure-your-logout-redirect).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur [Azure AD B2C-Sitzung](session-overview.md).
