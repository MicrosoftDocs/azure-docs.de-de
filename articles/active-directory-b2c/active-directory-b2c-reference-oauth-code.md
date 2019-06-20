---
title: 'Autorisierungscodeflow: Azure Active Directory B2C | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Web-Apps mithilfe von Azure AD B2C und dem Authentifizierungsprotokoll OpenID Connect erstellen.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7157682d7952529f9dfa98e8bc8707df9cfe944f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66509239"
---
# <a name="oauth-20-authorization-code-flow-in-azure-active-directory-b2c"></a>OAuth 2.0-Autorisierungscodefluss in Azure Active Directory B2C

Durch Gewähren des OAuth 2.0-Autorisierungcodes in Apps, die auf einem Gerät installiert sind, können Sie auf geschützte Ressourcen wie Web-APIs zugreifen. Mithilfe der Azure Active Directory B2C(Azure AD B2C)-Implementierung von OAuth 2.0 können Sie Ihre mobilen und Desktop-Apps Aufgaben zur Registrierung, Anmeldung und Identitätsverwaltung hinzufügen. Dieser Artikel ist sprachunabhängig. In dem Artikel wird beschrieben, wie HTTP-Nachrichten ohne Verwendung von Open Source-Bibliotheken gesendet und empfangen werden.

Der OAuth 2.0-Autorisierungscodefluss wird in [Abschnitt 4.1 der OAuth 2.0-Spezifikation](https://tools.ietf.org/html/rfc6749)beschrieben. Sie können ihn zum Ausführen der Authentifizierung und Autorisierung bei den meisten [Anwendungstypen](active-directory-b2c-apps.md) verwenden, einschließlich Webanwendungen und nativ installierten Anwendungen. Über den OAuth 2.0-Autorisierungscodeflow können Sie Zugriffstoken und Aktualisierungstoken für Ihre Anwendungen auf sichere Weise abrufen, die für den Zugriff auf mit einem [Autorisierungsserver](active-directory-b2c-reference-protocols.md) geschützte Ressourcen verwendet werden können.  Mit dem Aktualisierungstoken kann der Client neue Zugriffstoken (und Aktualisierungstoken) beschaffen, nachdem das Zugriffstoken abgelaufen ist. Dies ist meist nach einer Stunde der Fall.

Dieser Artikel behandelt den OAuth 2.0-Autorisierungscodeflow von **öffentlichen Clients**. Ein öffentlicher Client ist jede Clientanwendung, der nicht bei der sicheren Verwaltung der Integrität von geheimen Kennwörtern vertraut werden kann. Dazu gehören mobile Anwendungen, Desktopanwendungen und im Wesentlichen jede Anwendung, die auf einem Gerät ausgeführt wird und Zugriffstokens abrufen muss. 

> [!NOTE]
> Wenn Sie in einer Web-App mit Azure AD B2C eine Identitätsverwaltung hinzufügen möchten, verwenden Sie [OpenID Connect](active-directory-b2c-reference-oidc.md) anstelle von OAuth 2.0.

Azure AD B2C erweitert den OAuth 2.0-Standardfluss, sodass mehr als nur eine einfache Authentifizierung und Autorisierung erfolgt. Dabei wird der [Benutzerflowparameter](active-directory-b2c-reference-policies.md) eingeführt. Mit Benutzerflows können Sie übe OAuth 2.0 Benutzeroberflächenfunktionen zu Ihrer Anwendung hinzufügen, z.B. für die Registrierung Anmeldung und Profilverwaltung. Zu den Identitätsanbietern, die das OAuth 2.0-Protokoll verwenden, zählen [Amazon](active-directory-b2c-setup-amzn-app.md), [Azure Active Directory](active-directory-b2c-setup-oidc-azure-active-directory.md), [Facebook](active-directory-b2c-setup-fb-app.md), [GitHub](active-directory-b2c-setup-github-app.md), [Google](active-directory-b2c-setup-goog-app.md) und [LinkedIn](active-directory-b2c-setup-li-app.md).

In den HTTP-Beispielanforderungen in diesem Artikel wird das Azure AD B2C-Beispielverzeichnis **fabrikamb2c.onmicrosoft.com** verwendet. Darüber hinaus kommen unsere Beispielanwendung und beispielhafte Benutzerflows zum Einsatz. Sie können selbst Anforderungen mit diesen Werten testen oder eigene Werte verwenden.
Erfahren Sie, wie Sie [ein eigenes Azure AD B2C-Verzeichnis, Anwendungen und Benutzerflows abrufen](#use-your-own-azure-ad-b2c-directory).

## <a name="1-get-an-authorization-code"></a>1. Abrufen von Autorisierungscodes
Der Autorisierungscodefluss beginnt damit, dass der Client den Benutzer auf den `/authorize` -Endpunkt leitet. Dies ist der interaktive Teil des Flows, bei dem der Benutzer tatsächlich Aktionen durchführt. In dieser Anforderung gibt der Client im Parameter `scope` die Berechtigungen an, die er vom Benutzer abrufen muss. Im Parameter `p` wird der auszuführende Benutzerflow angegeben. In den folgenden drei Beispielen (mit Zeilenumbrüchen für bessere Lesbarkeit) wird jeweils ein anderer Benutzerflow verwendet.

### <a name="use-a-sign-in-user-flow"></a>Verwenden eines Benutzerflows für die Anmeldung
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_in
```

### <a name="use-a-sign-up-user-flow"></a>Verwenden eines Benutzerflows für die Registrierung
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_sign_up
```

### <a name="use-an-edit-profile-user-flow"></a>Verwenden eines Benutzerflows für die Profilbearbeitung
```
GET https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=code
&redirect_uri=urn%3Aietf%3Awg%3Aoauth%3A2.0%3Aoob
&response_mode=query
&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&p=b2c_1_edit_profile
```

| Parameter | Erforderlich | BESCHREIBUNG |
| --- | --- | --- |
| client_id |Erforderlich |Die Anwendungs-ID, die Ihrer App im [Azure-Portal](https://portal.azure.com) zugewiesen wird. |
| response_type |Erforderlich |Der Antworttyp, der `code` für den Autorisierungscodefluss enthalten muss. |
| redirect_uri |Erforderlich |Der Umleitungs-URI der App, in dem Authentifizierungsantworten gesendet und von der App empfangen werden. Er muss genau mit einem der Umleitungs-URIs übereinstimmen, die Sie im Portal registriert haben, mit dem Unterschied, dass er URL-codiert sein muss. |
| scope |Erforderlich |Eine durch Leerzeichen getrennte Liste von Bereichen. Ein einzelner Bereichswert gibt Azure Active Directory (Azure AD) an, dass beide Berechtigungen angefordert werden. Mit der Verwendung der Client-ID als Bereich wird angegeben, dass für die App ein Zugriffstoken erforderlich ist, das für Ihren eigenen Dienst oder die Web-API mit derselben Client-ID verwendet werden kann.  Der Bereich `offline_access` gibt an, dass Ihre App ein Aktualisierungstoken für den langfristigen Zugriff auf Ressourcen benötigt. Außerdem können Sie den Bereich `openid` verwenden, um ein ID-Token von Azure AD B2C anzufordern. |
| response_mode |Empfohlen |Die Methode, die zum Senden des resultierenden Autorisierungscodes zurück an Ihre App verwendet wird. Es kann sich um `query`, `form_post` oder `fragment` handeln. |
| state |Empfohlen |Ein Wert in der Anforderung, der eine Zeichenfolge mit beliebigem Inhalt Ihrer Wahl sein kann. Normalerweise wird ein zufällig generierter eindeutiger Wert verwendet, um eine websiteübergreifende Anforderungsfälschung zu verhindern. Der Status wird außerdem verwendet, um Informationen über den Status des Benutzers in der App zu codieren, bevor die Authentifizierungsanforderung aufgetreten ist. Dies kann z.B. die vom Benutzer besuchte Seite oder der ausgeführte Benutzerflow sein. |
| p |Erforderlich |Der Benutzerflow, der ausgeführt wird. Dies ist der Name eines Benutzerflows, der in Ihrem Azure AD B2C-Verzeichnis erstellt wird. Der Wert für den Benutzerflownamen muss mit **b2c\_1\_** beginnen. Weitere Informationen zu Benutzerflows finden Sie unter [Azure AD B2C-Benutzerflows](active-directory-b2c-reference-policies.md). |
| prompt |Optional |Der Typ der erforderlichen Benutzerinteraktion. Gegenwärtig ist der einzige gültige Wert `login`, der den Benutzer zur Eingabe seiner Anmeldeinformationen für diese Anforderung zwingt. Einmaliges Anmelden wird nicht verwendet. |

An diesem Punkt wird der Benutzer aufgefordert, den Workflow des Benutzerflows abzuschließen. Dafür muss der Benutzer z.B. den Benutzernamen und das Kennwort angeben, sich mit der Identität eines sozialen Netzwerks anmelden, sich für das Verzeichnis registrieren oder andere Schritte ausführen. Die Benutzeraktionen hängen davon ab, wie der Benutzerflow definiert ist.

Nachdem der Benutzer den Benutzerflow abgeschlossen hat, gibt Azure AD über den für `redirect_uri` verwendeten Wert eine Antwort an Ihre App zurück. Hierzu wird die im Parameter `response_mode` angegebene Methode verwendet. Die Antwort ist in den oben aufgeführten Benutzeraktionsszenarien immer gleich, unabhängig vom ausgeführten Benutzerflow.

Eine erfolgreiche Antwort mit `response_mode=query` sieht wie folgt aus:

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...        // the authorization_code, truncated
&state=arbitrary_data_you_can_receive_in_the_response                // the value provided in the request
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| code |Der Autorisierungscode, den die App angefordert hat. Die App kann den Autorisierungscode zum Anfordern eines Zugriffstokens für eine Zielressource verwenden. Autorisierungscodes sind von sehr kurzer Lebensdauer. In der Regel laufen sie nach etwa 10 Minuten ab. |
| state |Die vollständige Beschreibung finden Sie im vorangehenden Abschnitt. Wenn ein Parameter `state` in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die App sollte überprüfen, ob die `state`-Werte in der Anforderung und in der Antwort identisch sind. |

Fehlerantworten können auch an den Umleitungs-URI gesendet werden, damit die App diese entsprechend behandeln kann:

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=The+user+has+cancelled+entering+self-asserted+information
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| error |Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren. Sie können mit der Zeichenfolge auch auf Fehler reagieren. |
| error_description |Eine spezifische Fehlermeldung, mit der Sie die Hauptursache eines Authentifizierungsfehlers identifizieren können. |
| state |Die vollständige Beschreibung finden Sie in der obigen Tabelle. Wenn ein Parameter `state` in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die App sollte überprüfen, ob die `state`-Werte in der Anforderung und in der Antwort identisch sind. |

## <a name="2-get-a-token"></a>2. Abrufen von Token
Nachdem Sie einen Autorisierungscode abgerufen haben, können Sie den `code` für ein Token für die gewünschte Ressource einlösen, indem Sie eine POST-Anforderung an den `/token`-Endpunkt senden. In Azure AD B2C ist die einzige Ressource, für die Sie ein Token anfordern können, die Back-End-Web-API Ihrer App. Für das Anfordern eines Tokens für sich selbst wird die Client-ID Ihrer App als Bereich verwendet:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob

```

| Parameter | Erforderlich | BESCHREIBUNG |
| --- | --- | --- |
| p |Erforderlich |Der Benutzerflow, der zum Abrufen des Autorisierungscodes verwendet wurde. Sie können in dieser Anforderung keinen anderen Benutzerflow verwenden. Sie müssen diesen Parameter in der *Abfragezeichenfolge*hinzufügen, nicht im POST-Text. |
| client_id |Erforderlich |Die Anwendungs-ID, die Ihrer App im [Azure-Portal](https://portal.azure.com) zugewiesen wird. |
| grant_type |Erforderlich |Der Gewährungstyp. Beim Autorisierungscodefluss muss der Gewährungstyp `authorization_code` sein. |
| scope |Empfohlen |Eine durch Leerzeichen getrennte Liste von Bereichen. Ein einzelner Bereichswert gibt Azure AD an, dass beide Berechtigungen angefordert werden. Mit der Verwendung der Client-ID als Bereich wird angegeben, dass für die App ein Zugriffstoken erforderlich ist, das für Ihren eigenen Dienst oder die Web-API mit derselben Client-ID verwendet werden kann.  Der Bereich `offline_access` gibt an, dass Ihre App ein Aktualisierungstoken für den langfristigen Zugriff auf Ressourcen benötigt.  Außerdem können Sie den Bereich `openid` verwenden, um ein ID-Token von Azure AD B2C anzufordern. |
| code |Erforderlich |Der Autorisierungscode, den Sie im ersten Abschnitt des Vorgangs erhalten haben. |
| redirect_uri |Erforderlich |Der Umleitungs-URI der Anwendung, bei der Sie den Autorisierungscode erhalten haben. |

Eine erfolgreiche Tokenantwort sieht wie folgt aus:

```
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
| --- | --- |
| not_before |Der Zeitpunkt in Epochenzeit, ab dem das Token gültig ist. |
| token_type |Der Wert des Tokentyps. Bearertoken ist der einzige Typ, den Azure AD unterstützt. |
| access_token |Das signierte JWT-Token (JSON Web Token), das Sie angefordert haben. |
| scope |Die Bereiche, für die das Token gültig ist. Sie können in den Bereichen auch Token für die spätere Verwendung zwischenspeichern. |
| expires_in |Gibt an, wie lange das Token gültig ist (in Sekunden). |
| refresh_token |Ein Aktualisierungstoken von OAuth 2.0. Die App kann dieses Token verwenden, um nach Ablauf der aktuellen Token zusätzliche Token zu erhalten. Aktualisierungstoken sind langlebig. Sie können diese verwenden, um den Zugriff auf Ressourcen für längere Zeit beizubehalten. Weitere Informationen finden Sie unter [Azure AD B2C-Tokenverweise](active-directory-b2c-reference-tokens.md). |

Fehlerantworten sehen wie folgt aus:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| error |Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren. Sie können mit der Zeichenfolge auch auf Fehler reagieren. |
| error_description |Eine spezifische Fehlermeldung, mit der Sie die Hauptursache eines Authentifizierungsfehlers identifizieren können. |

## <a name="3-use-the-token"></a>3. Verwenden des Tokens
Nachdem Sie ein Zugriffstoken abgerufen haben, können Sie das Token für Anforderungen an die Back-End-Web-APIs verwenden, indem Sie es in den `Authorization`-Header einfügen:

```
GET /tasks
Host: https://mytaskwebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

## <a name="4-refresh-the-token"></a>4. Aktualisieren des Tokens
Zugriffs- und ID-Tokens sind kurzlebig. Nach ihrem Ablauf müssen Sie sie aktualisieren, um weiterhin auf Ressourcen zugreifen zu können. Übermitteln Sie zu diesem Zweck eine andere POST-Anforderung an den `/token`-Endpunkt. Geben Sie dieses Mal `refresh_token` anstelle von `code` an:

```
POST fabrikamb2c.onmicrosoft.com/oauth2/v2.0/token?p=b2c_1_sign_in HTTP/1.1
Host: https://fabrikamb2c.b2clogin.com
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6&client_secret=JqQX2PNo9bpM0uEihUPzyrh&scope=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access&refresh_token=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...&redirect_uri=urn:ietf:wg:oauth:2.0:oob
```

| Parameter | Erforderlich | BESCHREIBUNG |
| --- | --- | --- |
| p |Erforderlich |Der Benutzerflow, der zum Abrufen des ursprünglichen Aktualisierungstokens verwendet wurde. Sie können in dieser Anforderung keinen anderen Benutzerflow verwenden. Sie müssen diesen Parameter in der *Abfragezeichenfolge*hinzufügen, nicht im POST-Text. |
| client_id |Erforderlich |Die Anwendungs-ID, die Ihrer App im [Azure-Portal](https://portal.azure.com) zugewiesen wird. |
| client_secret |Erforderlich |Das Clientgeheimnis (client_secret), das der Client-ID (client_id) im [Azure-Portal](https://portal.azure.com) zugewiesen wird |
| grant_type |Erforderlich |Der Gewährungstyp. Bei diesem Abschnitt des Autorisierungscodeflows muss der Gewährungstyp `refresh_token` sein. |
| scope |Empfohlen |Eine durch Leerzeichen getrennte Liste von Bereichen. Ein einzelner Bereichswert gibt Azure AD an, dass beide Berechtigungen angefordert werden. Mit der Verwendung der Client-ID als Bereich wird angegeben, dass für die App ein Zugriffstoken erforderlich ist, das für Ihren eigenen Dienst oder die Web-API mit derselben Client-ID verwendet werden kann.  Der Bereich `offline_access` gibt an, dass Ihre App ein Aktualisierungstoken für den langfristigen Zugriff auf Ressourcen benötigt.  Außerdem können Sie den Bereich `openid` verwenden, um ein ID-Token von Azure AD B2C anzufordern. |
| redirect_uri |Optional |Der Umleitungs-URI der Anwendung, bei der Sie den Autorisierungscode erhalten haben. |
| refresh_token |Erforderlich |Das ursprüngliche Aktualisierungstoken, das Sie im zweiten Abschnitt des Flows abgerufen haben. |

Eine erfolgreiche Tokenantwort sieht wie folgt aus:

```
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
| --- | --- |
| not_before |Der Zeitpunkt in Epochenzeit, ab dem das Token gültig ist. |
| token_type |Der Wert des Tokentyps. Bearertoken ist der einzige Typ, den Azure AD unterstützt. |
| access_token |Das signierte JWT-Token, das Sie angefordert haben. |
| scope |Die Bereiche, für die das Token gültig ist. Sie können in den Bereichen auch Tokens für die spätere Verwendung zwischenspeichern. |
| expires_in |Gibt an, wie lange das Token gültig ist (in Sekunden). |
| refresh_token |Ein Aktualisierungstoken von OAuth 2.0. Die App kann dieses Token verwenden, um nach Ablauf der aktuellen Token zusätzliche Token zu erhalten. Aktualisierungstoken sind langlebig und können verwendet werden, um den Zugriff auf Ressourcen für längere Zeit beizubehalten. Weitere Informationen finden Sie unter [Azure AD B2C-Tokenverweise](active-directory-b2c-reference-tokens.md). |

Fehlerantworten sehen wie folgt aus:

```
{
    "error": "access_denied",
    "error_description": "The user revoked access to the app.",
}
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| error |Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren. Sie können mit der Zeichenfolge auch auf Fehler reagieren. |
| error_description |Eine spezifische Fehlermeldung, mit der Sie die Hauptursache eines Authentifizierungsfehlers identifizieren können. |

## <a name="use-your-own-azure-ad-b2c-directory"></a>Verwenden Sie Ihr eigenes Azure AD B2C-Verzeichnis.
Führen Sie die folgenden Schritte durch, um diese Anforderungen selbst zu testen. Ersetzen Sie die in diesem Artikel verwendeten Beispielwerte durch Ihre eigenen Werte.

1. [Erstellen Sie ein Azure AD B2C-Verzeichnis](active-directory-b2c-get-started.md). Verwenden Sie den Namen Ihres Verzeichnisses in den Anforderungen.
2. [Erstellen Sie eine Anwendung](active-directory-b2c-app-registration.md) zum Abrufen einer Anwendungs-ID und eines Umleitungs-URI. Sie können Ihrer App einen nativen Client hinzufügen.
3. [Erstellen Sie Ihre Benutzerflows](active-directory-b2c-reference-policies.md), um Ihre Benutzerflownamen abzurufen.

