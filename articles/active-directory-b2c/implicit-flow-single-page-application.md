---
title: Single-Page-Anmeldung mit implizitem Flow
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie Single-Page-Anmeldung mithilfe des impliziten OAuth 2.0-Flow mit Azure Active Directory B2C hinzufügen.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: fe31e1bf095d15cfdd7945288486cb866ace8246
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/18/2020
ms.locfileid: "94840609"
---
# <a name="single-page-sign-in-using-the-oauth-20-implicit-flow-in-azure-active-directory-b2c"></a>Single-Page-Anmeldung mithilfe des impliziten OAuth 2.0-Flusses in Azure Active Directory B2C

Viele moderne Anwendungen verfügen über ein Single-Page-App-Front-End, das hauptsächlich in JavaScript geschrieben ist. Häufig wird zum Schreiben der App ein Framework wie Angular, React oder Vue.js verwendet. Bei einseitigen Apps und anderen JavaScript-Apps, die hauptsächlich in einem Browser ausgeführt werden, gibt es in Bezug auf die Authentifizierung einige zusätzliche Herausforderungen:

- Die Sicherheitsmerkmale dieser Apps unterscheiden sich von herkömmlichen serverbasierten Webanwendungen.
- Zahlreiche Autorisierungsserver und Identitätsanbieter unterstützen keine CORS-Anforderungen (CORS = Cross-Origin Resource Sharing).
- Umleitungen auf ganzseitige Browserseiten können die Benutzererfahrung stören.

Die empfohlene Methode zur Unterstützung von Single-Page-Webanwendungen ist [OAuth 2.0-Autorisierungscodefluss (mit PKCE)](./authorization-code-flow.md).

Einige Frameworks, z. B. [MSAL.js 1.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core), unterstützen nur den Fluss für implizite Genehmigung. In diesen Fällen unterstützt Azure Active Directory B2C (Azure AD B2C) den Fluss für implizite Genehmigung für OAuth 2.0-Autorisierung. Dieser Fluss wird in [Abschnitt 4.2 der OAuth 2.0-Spezifikation](https://tools.ietf.org/html/rfc6749) beschrieben. Beim impliziten Ablauf empfängt die App Token direkt vom Azure AD-Autorisierungsendpunkt (Azure Active Directory), ohne dass eine Kommunikation zwischen Servern stattfindet. Die gesamte Authentifizierungslogik und Sitzungsverarbeitung wird vollständig im JavaScript-Client abgewickelt – entweder mit einer Seitenumleitung oder mit einem Popupfeld.

Azure AD B2C erweitert den impliziten OAuth 2.0-Standardfluss, sodass mehr als nur eine einfache Authentifizierung und Autorisierung erfolgt. Azure AD B2C führt den [Richtlinienparameter](user-flow-overview.md) ein. Mit dem Richtlinienparameter können Sie OAuth 2.0 zum Hinzufügen von Richtlinien zu Ihrer App verwenden, z. B. für Benutzerflows für die Registrierung, Anmeldung und Profilverwaltung. In den HTTP-Beispielanforderungen in diesem Artikel wird **{tenant}.onmicrosoft.com** als Beispiel verwendet. Ersetzen Sie `{tenant}` durch den Namen Ihres Mandanten, wenn ein solcher vorhanden ist und Sie einen Benutzerflow erstellt haben.

Die implizite Anmeldevorgang sieht in etwa wie die folgende Abbildung aus. Die einzelnen Schritte werden später im Artikel detailliert beschrieben.

![Swimlane-Diagramm mit dem impliziten OpenID Connect-Flow](./media/implicit-flow-single-page-application/convergence_scenarios_implicit.png)

## <a name="send-authentication-requests"></a>Übermitteln von Authentifizierungsanforderungen

Wenn Ihre Webanwendung den Benutzer authentifizieren und einen Benutzerflow ausführen muss, kann sie den Benutzer an den `/authorize`-Endpunkt weiterleiten. Der Benutzer führt Aktionen in Abhängigkeit vom Benutzerflow durch.

In dieser Anforderung gibt der Client die Berechtigungen, die er vom Benutzer benötigt, im Parameter `scope` an. Er gibt auch den auszuführenden Benutzerflow an. Um sich anzusehen, wie diese Anforderung funktioniert, fügen Sie sie in einem Browser ein und führen sie aus. Ersetzen Sie `{tenant}` durch den Namen des Azure AD B2C-Mandanten. Ersetzen Sie `90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6` durch die App-ID der Anwendung, die Sie zuvor in Ihrem Mandanten registriert haben. Ersetzen Sie `{policy}` durch den Namen einer Richtlinie, die Sie in Ihrem Mandanten erstellt haben, z. B. `b2c_1_sign_in`.

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=id_token+token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&response_mode=fragment
&scope=openid%20offline_access
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
```

| Parameter | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
|{tenant}| Ja | Name des Azure AD B2C-Mandanten.|
|{policy}| Ja| Der auszuführende Benutzerflow. Geben Sie den Namen eines Benutzerflows an, den Sie in Ihrem Azure AD B2C-Mandanten erstellt haben. Beispiel: `b2c_1_sign_in`, `b2c_1_sign_up` oder `b2c_1_edit_profile`. |
| client_id | Ja | Die Anwendungs-ID, die das [Azure-Portal](https://portal.azure.com/) Ihrer Anwendung zugewiesen hat. |
| response_type | Ja | Muss das `id_token` für die OpenID Connect-Anmeldung enthalten. Kann auch den Antworttyp `token` enthalten. Mithilfe von `token` kann Ihre App ein Zugriffstoken direkt vom Autorisierungsendpunkt abrufen, ohne dass eine zweite Anforderung an den Autorisierungsendpunkt erforderlich ist.  Wenn Sie den Antworttyp `token` verwenden, muss der Parameter `scope` einen Bereich enthalten, in dem angegeben wird, für welche Ressource das Token ausgegeben wird. |
| redirect_uri | Nein  | Der Umleitungs-URI der App, in dem Authentifizierungsantworten gesendet und von der App empfangen werden können. Er muss genau mit einem der Umleitungs-URIs übereinstimmen, die Sie im Portal registriert haben, mit dem Unterschied, dass er URL-codiert sein muss. |
| response_mode | Nein | Gibt die Methode an, die zum Zurücksenden des resultierenden Tokens an Ihre App verwendet wird.  Verwenden Sie `fragment` für implizite Flüsse. |
| scope | Ja | Eine durch Leerzeichen getrennte Liste von Bereichen. Ein einzelner Bereichswert gibt Azure AD an, dass beide Berechtigungen angefordert werden. Der `openid`-Bereich gibt eine Berechtigung für das Anmelden des Benutzers und das Abrufen von Daten über den Benutzer in Form von ID-Token an. Der `offline_access` -Bereich ist für Web-Apps optional. Er gibt an, dass Ihre App ein Aktualisierungstoken für den dauerhaften Zugriff auf Ressourcen benötigt. |
| state | Nein  | Ein in der Anforderung enthaltener Wert, der ebenfalls in der Tokenantwort zurückgegeben wird. Es kann sich um eine Zeichenfolge mit jedem beliebigen zu verwendenden Inhalt handeln. Normalerweise wird ein zufällig generierter eindeutiger Wert verwendet, um eine websiteübergreifende Anforderungsfälschung zu verhindern. Der Status wird auch zum Codieren von Informationen über den Status des Benutzers in der App vor der Authentifizierungsanforderung verwendet, z.B. für Informationen zu der Seite, die der Benutzer besucht hat. |
| nonce | Ja | Ein (von der App generierter) Wert in der Anforderung, der im resultierenden ID-Token als Anspruch enthalten ist. Die App kann diesen Wert dann verifizieren, um Tokenwiederholungsangriffe abzuwehren. Der Wert ist eine zufällige, eindeutige Zeichenfolge, die verwendet werden kann, um den Ursprung der Anforderung zu identifizieren. |
| prompt | Nein  | Der Typ der erforderlichen Benutzerinteraktion. Derzeit ist `login` der einzige gültige Wert. Durch diesen Parameter wird der Benutzer dazu gezwungen, die Anmeldeinformationen bei dieser Anforderung einzugeben. Einmaliges Anmelden wird nicht berücksichtigt. |

An diesem Punkt wird der Benutzer aufgefordert, den Workflow der Richtlinie abzuschließen. Der Benutzer muss möglicherweise seinen Benutzernamen und sein Kennwort eingeben, sich mit einer Social-Media-Identität anmelden, sich für das Verzeichnis registrieren oder andere Schritte ausführen. Die Benutzeraktionen hängen davon ab, wie der Benutzerflow definiert ist.

Nachdem der Benutzer den Benutzerflow abgeschlossen hat, gibt Azure AD über den für `redirect_uri` verwendeten Wert eine Antwort an Ihre App zurück. Hierzu wird die im Parameter `response_mode` angegebene Methode verwendet. Die Antwort ist in den Benutzeraktionsszenarien immer gleich, unabhängig vom ausgeführten Benutzerflow.

### <a name="successful-response"></a>Erfolgreiche Antwort
Eine erfolgreiche Antwort mithilfe von `response_mode=fragment` und `response_type=id_token+token` sieht wie folgt aus, wobei die Zeilenumbrüche der Lesbarkeit dienen:

```http
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&token_type=Bearer
&expires_in=3599
&scope="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6 offline_access",
&id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
```

| Parameter | BESCHREIBUNG |
| --------- | ----------- |
| access_token | Das von der App angeforderte Zugriffstoken. |
| token_type | Der Wert des Tokentyps. Bearertoken ist der einzige Typ, den Azure AD unterstützt. |
| expires_in | Gibt an, wie lange das Zugriffstoken gültig ist (in Sekunden). |
| scope | Die Bereiche, für die das Token gültig ist. Sie können in den Bereichen auch Token für die spätere Verwendung zwischenspeichern. |
| id_token | Das ID-Token, das die App angefordert hat. Sie können mit dem ID-Token die Identität des Benutzers überprüfen und eine Sitzung mit dem Benutzer beginnen. Weitere Informationen zu ID-Token und deren Inhalt finden Sie im [Azure AD B2C-Tokenverweis](tokens-overview.md). |
| state | Wenn ein Parameter `state` in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die App sollte überprüfen, ob die `state`-Werte in der Anforderung und in der Antwort identisch sind. |

### <a name="error-response"></a>Fehlerantwort
Fehlerantworten können auch an den Umleitungs-URI gesendet werden, damit die App diese entsprechend behandeln kann:

```http
GET https://aadb2cplayground.azurewebsites.net/#
error=access_denied
&error_description=the+user+canceled+the+authentication
&state=arbitrary_data_you_can_receive_in_the_response
```

| Parameter | BESCHREIBUNG |
| --------- | ----------- |
| error | Ein Code, mit dem Typen der auftretenden Fehler klassifiziert werden. |
| error_description | Eine spezifische Fehlermeldung, mit der Sie die Hauptursache eines Authentifizierungsfehlers identifizieren können. |
| state | Wenn ein Parameter `state` in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die App sollte überprüfen, ob die `state`-Werte in der Anforderung und in der Antwort identisch sind.|

## <a name="validate-the-id-token"></a>Überprüfen des ID-Tokens

Das Empfangen eines ID-Tokens reicht nicht aus, um den Benutzer zu authentifizieren. Validieren Sie die Signatur des ID-Tokens, und überprüfen Sie die Ansprüche im Token entsprechend den Anforderungen Ihrer App. Azure AD B2C verwendet [JSON-Webtoken (JWT)](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html) und die Verschlüsselung mit öffentlichem Schlüssel, um Token zu signieren und deren Gültigkeit zu überprüfen.

Für die Überprüfung von JWTs sind je nach gewünschter Sprache viele Open Source-Bibliotheken verfügbar. Erwägen Sie die Verwendung verfügbarer Open-Source-Bibliotheken, anstatt eine eigene Überprüfungslogik zu implementieren. Die Informationen in diesem Handbuch sind hilfreich für die ordnungsgemäße Verwendung dieser Bibliotheken.

Azure AD B2C verfügt über einen OpenID Connect-Metadatenendpunkt. Eine App kann mit diesem Endpunkt Informationen über Azure AD B2C zur Laufzeit abrufen. Diese Informationen umfassen Endpunkte, Tokeninhalte und Token-Signaturschlüssel. Es gibt ein JSON-Metadatendokument für jeden Benutzerflow in Ihrem Azure AD B2C-Mandanten. Das Metadatendokument für den Benutzerflow „b2c_1_sign_in“ im Mandanten „fabrikamb2c.onmicrosoft.com“ befindet sich beispielsweise unter:

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/v2.0/.well-known/openid-configuration
```

`jwks_uri` ist eine der Eigenschaften dieses Konfigurationsdokuments. Der Wert für den gleichen Benutzerflow würde wie folgt lauten:

```http
https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_sign_in/discovery/v2.0/keys
```

Es gibt zwei Möglichkeiten zum Ermitteln, welcher Benutzerflow zum Signieren eines ID-Tokens verwendet wurde (und wo die Metadaten abgerufen werden können). Zunächst einmal ist der Benutzerflowname im `acr`-Anspruch in `id_token` enthalten. Informationen zum Analysieren von Ansprüchen nach einem ID-Token finden Sie im [Azure AD B2C-Tokenverweis](tokens-overview.md). Die andere Möglichkeit besteht darin, den Benutzerflow beim Übermitteln der Anforderung im Wert des Parameters `state` zu verschlüsseln. Anschließend kann der Parameter `state` entschlüsselt werden, um zu bestimmen, welcher Benutzerflow verwendet wurde. Beide Methoden sind gültig.

Nachdem Sie das Metadatendokument aus dem OpenID Connect-Metadatenendpunkt erhalten haben, können Sie die öffentlichen RSA-256-Schlüssel (die sich an diesem Endpunkt befinden) zum Überprüfen der Signatur des ID-Tokens verwenden. Es gibt möglicherweise zu einem bestimmten Zeitpunkt mehrere Schlüssel an diesem Endpunkt. Sie werden jeweils durch eine `kid` identifiziert. Der Header der `id_token` enthält auch einen `kid`-Anspruch. Er gibt an, mit welchem dieser Schlüssel das ID-Token signiert wurde. Weitere Informationen finden Sie im [Azure AD B2C-Tokenverweis](tokens-overview.md). Dort finden Sie auch Einzelheiten zum [Überprüfen von Token](tokens-overview.md).
<!--TODO: Improve the information on this-->

Nach der Überprüfung der Signatur des ID-Tokens müssen auch einige Ansprüche überprüft werden. Zum Beispiel:

* Überprüfen Sie den `nonce`-Anspruch, um Tokenwiederholungsangriffe zu verhindern. Dessen Wert sollte dem in der Anmeldeanforderung angegebenen Wert entsprechen.
* Überprüfen Sie den `aud`-Anspruch, um sicherzustellen, dass das ID-Token für Ihre App ausgegeben wurde. Der Wert sollte der Anwendungs-ID der App entsprechen.
* Überprüfen Sie die Ansprüche `iat` und `exp`, um sicherzustellen, dass das ID-Token nicht abgelaufen ist.

Es gibt auch noch einige andere Überprüfungen, die Sie durchführen sollten. Diese sind in der [OpenID Connect Core-Spezifikation](https://openid.net/specs/openid-connect-core-1_0.html) ausführlich beschrieben. Sie können je nach Szenario auch zusätzliche Ansprüche überprüfen. Einige allgemeinen Überprüfungen umfassen:

* Sicherstellen, dass sich der Benutzer/die Organisation für die App registriert hat.
* Sicherstellen, dass der Benutzer über eine ordnungsgemäße Autorisierung und die richtigen Berechtigungen verfügt.
* Sicherstellen, dass eine bestimmte Authentifizierungsmethode verwendet wird, z. B. Azure AD Multi-Factor Authentication.

Weitere Informationen zu den Ansprüchen in einem ID-Token finden Sie im [Azure AD B2C-Tokenverweis](tokens-overview.md).

Nachdem Sie das ID-Token vollständig überprüft haben, können Sie eine Sitzung mit dem Benutzer beginnen. Verwenden Sie in Ihrer App die Ansprüche im ID-Token, um Informationen über den Benutzer abzurufen. Diese Informationen können für die Anzeige, für Aufzeichnungen, für die Autorisierung usw. verwendet werden.

## <a name="get-access-tokens"></a>Abrufen von Zugriffstoken
Wenn Ihre Web-Apps lediglich Benutzerflows ausführen müssen, können Sie die nächsten Abschnitte überspringen. Die Informationen in diesen Abschnitten gelten nur für Web-Apps, die authentifizierte Aufrufe an eine Web-API durchführen müssen und die auch von Azure AD B2C geschützt werden.

Nachdem Sie den Benutzer bei der Single-Page-App angemeldet haben, können Sie Zugriffstoken zum Aufrufen der durch Azure AD gesicherten Web-APIs abrufen. Auch wenn Sie mithilfe des Antworttyps `token` bereits ein Token erhalten haben, können Sie diese Methode zum Abrufen von Token für zusätzliche Ressourcen verwenden, ohne den Benutzer zur erneuten Anmeldung umzuleiten.

In einem typischen Web-App-Fluss senden Sie eine Anforderung an den `/token`-Endpunkt. Der Endpunkt unterstützt jedoch keine CORS-Anforderungen, daher kommen AJAX-Aufrufe zum Abrufen eines Aktualisierungstokens nicht infrage. Stattdessen können Sie den impliziten Fluss in einem ausgeblendeten HTML-IFrame-Element verwenden, um neue Token für andere Web-APIs zu erhalten. Hier sehen Sie ein Beispiel, mit Zeilenumbrüchen für bessere Lesbarkeit:

```http
https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/authorize?
client_id=90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6
&response_type=token
&redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
&response_mode=fragment
&state=arbitrary_data_you_can_receive_in_the_response
&nonce=12345
&prompt=none
```

| Parameter | Erforderlich? | BESCHREIBUNG |
| --- | --- | --- |
|{tenant}| Erforderlich | Name des Azure AD B2C-Mandanten.|
{policy}| Erforderlich| Der auszuführende Benutzerflow. Geben Sie den Namen eines Benutzerflows an, den Sie in Ihrem Azure AD B2C-Mandanten erstellt haben. Beispiel: `b2c_1_sign_in`, `b2c_1_sign_up` oder `b2c_1_edit_profile`. |
| client_id |Erforderlich |Die Anwendungs-ID, die Ihrer App im [Azure-Portal](https://portal.azure.com) zugewiesen wird. |
| response_type |Erforderlich |Muss das `id_token` für die OpenID Connect-Anmeldung enthalten.  Kann auch den Antworttyp `token` enthalten. Mithilfe von `token` kann Ihre App ein Zugriffstoken direkt vom Autorisierungsendpunkt abrufen, ohne dass eine zweite Anforderung an den Autorisierungsendpunkt erforderlich ist. Wenn Sie den Antworttyp `token` verwenden, muss der Parameter `scope` einen Bereich enthalten, in dem angegeben wird, für welche Ressource das Token ausgegeben wird. |
| redirect_uri |Empfohlen |Der Umleitungs-URI der App, in dem Authentifizierungsantworten gesendet und von der App empfangen werden können. Er muss genau mit einem der Umleitungs-URIs übereinstimmen, den Sie im Portal registriert haben – mit dem Unterschied, dass er URL-codiert sein muss. |
| scope |Erforderlich |Eine durch Leerzeichen getrennte Liste von Bereichen.  Beziehen Sie zum Abrufen von Token alle Bereiche ein, die für die gewünschte Ressource erforderlich sind. |
| response_mode |Empfohlen |Gibt die Methode an, die zum Zurücksenden des resultierenden Tokens an Ihre App verwendet wird. Verwenden Sie `fragment` für den impliziten Flow. Es können zwei weitere Modi (`query` und `form_post`) angegeben werden, diese funktionieren aber nicht im impliziten Flow. |
| state |Empfohlen |Ein in der Anforderung enthaltener Wert, der in der Tokenantwort zurückgegeben wird.  Es kann sich um eine Zeichenfolge mit jedem beliebigen zu verwendenden Inhalt handeln.  Normalerweise wird ein zufällig generierter eindeutiger Wert verwendet, um eine websiteübergreifende Anforderungsfälschung zu verhindern.  Der Status wird außerdem verwendet, um Informationen über den Status des Benutzers in der App zu codieren, bevor die Authentifizierungsanforderung aufgetreten ist. Beispiel: Informationen zu der Seite oder Ansicht, die der Benutzer besucht hat. |
| nonce |Erforderlich |Ein Wert in der Anforderung, der von der App generiert wird und im resultierenden ID-Token als Anspruch enthalten ist.  Die App kann diesen Wert dann verifizieren, um Tokenwiederholungsangriffe abzuwehren. Der Wert ist in der Regel eine zufällige, eindeutige Zeichenfolge, die den Ursprung der Anforderung identifiziert. |
| prompt |Erforderlich |Verwenden Sie `prompt=none` zum Aktualisieren und Abrufen von Token in einem ausgeblendeten IFrame, um sicherzustellen, dass das IFrame auf der Anmeldeseite nicht hängenbleibt, sondern direkt zurückgegeben wird. |
| login_hint |Erforderlich |Beziehen Sie zum Aktualisieren und Abrufen von Token in einem ausgeblendete IFrame den Benutzernamen des Benutzers in diesem Hinweis mit ein, damit zwischen verschiedenen Sitzungen unterschieden werden kann, die der Benutzer möglicherweise ausführt. Sie können den Benutzernamen mithilfe des Anspruchs `preferred_username` aus einer früheren Anmeldung extrahieren. (Der Bereich `profile` ist erforderlich, um den Anspruch `preferred_username` zu erhalten.) |
| domain_hint |Erforderlich |Kann `consumers` oder `organizations` sein.  Fügen Sie zum Aktualisieren und Abrufen von Token in einem ausgeblendeten IFrame den Wert `domain_hint` in die Anforderung ein.  Extrahieren Sie den Anspruch `tid` aus dem ID-Token einer früheren Anmeldung, um zu bestimmen, welcher Wert verwendet werden soll. (Der Bereich `profile` ist erforderlich, um den Anspruch `tid` zu erhalten.) Verwenden Sie `domain_hint=consumers`, wenn der Anspruch `tid` den Wert `9188040d-6c67-4c5b-b112-36a304b66dad` hat.  Verwenden Sie andernfalls `domain_hint=organizations`. |

Durch Festlegen des Parameters `prompt=none` ist diese Anforderung entweder erfolgreich, oder sie führt direkt zu einem Fehler und kehrt zu Ihrer Anwendung zurück.  Eine erfolgreiche Antwort wird an Ihre App an den angegebenen Umleitungs-URI gesendet. Dabei wird die im Parameter `response_mode` angegebene Methode verwendet.

### <a name="successful-response"></a>Erfolgreiche Antwort
Eine erfolgreiche Antwort mit `response_mode=fragment` sieht wie in diesem Beispiel aus:

```http
GET https://aadb2cplayground.azurewebsites.net/#
access_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
&state=arbitrary_data_you_sent_earlier
&token_type=Bearer
&expires_in=3599
&scope=https%3A%2F%2Fapi.contoso.com%2Ftasks.read
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| access_token |Das von der App angeforderte Token |
| token_type |Der Tokentyp wird immer Träger sein. |
| state |Wenn ein Parameter `state` in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die App sollte überprüfen, ob die `state`-Werte in der Anforderung und in der Antwort identisch sind. |
| expires_in |Die Gültigkeitsdauer des Zugriffstokens (in Sekunden). |
| scope |Die Bereiche, für die das Zugriffstoken gültig ist. |

### <a name="error-response"></a>Fehlerantwort
Fehlerantworten können auch an den Umleitungs-URI gesendet werden, damit die App diese angemessen behandeln kann.  Bei `prompt=none` sieht ein erwarteter Fehler wie in diesem Beispiel aus:

```http
GET https://aadb2cplayground.azurewebsites.net/#
error=user_authentication_required
&error_description=the+request+could+not+be+completed+silently
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| error |Eine Zeichenfolge mit einem Fehlercode, die zum Klassifizieren der auftretenden Fehlertypen verwendet werden kann. Sie können mit der Zeichenfolge auch auf Fehler reagieren. |
| error_description |Eine spezifische Fehlermeldung, mit der Sie die Hauptursache eines Authentifizierungsfehlers identifizieren können. |

Wenn Sie diesen Fehler in der IFrame-Anforderung erhalten, muss sich der Benutzer erneut anmelden, um ein neues Token abzurufen.

## <a name="refresh-tokens"></a>Aktualisierungstoken
ID-Token und Zugriffstoken laufen nach einem kurzen Zeitraum ab. Ihre App muss darauf vorbereitet sein, diese Token in regelmäßigen Abständen zu aktualisieren.  Führen Sie zum Aktualisieren beider Tokentypen die oben erwähnte verborgene IFrame-Anforderung unter Verwendung des Parameters `prompt=none` aus, um die Schritte von Azure AD zu steuern.  Verwenden Sie `response_type=id_token` und `scope=openid` sowie einen `nonce`-Parameter, um einen neuen `id_token`-Wert zu erhalten.

## <a name="send-a-sign-out-request"></a>Senden einer Abmeldeanforderung
Wenn Sie einen Benutzer von der App abmelden möchten, leiten Sie den Benutzer zum Abmelden an Azure AD weiter. Wenn der Benutzer nicht umgeleitet wird, kann er sich möglicherweise erneut für Ihre Anwendung authentifizieren, ohne die Anmeldeinformationen erneut einzugeben, da er nach wie vor über eine gültige SSO-Sitzung bei Azure AD verfügt.

Sie können den Benutzer einfach an den `end_session_endpoint` umleiten, der im gleichen OpenID Connect-Metadatendokument aufgeführt wird, das weiter oben im Abschnitt [Überprüfen des ID-Tokens](#validate-the-id-token) beschrieben wird. Beispiel:

```http
GET https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{policy}/oauth2/v2.0/logout?post_logout_redirect_uri=https%3A%2F%2Faadb2cplayground.azurewebsites.net%2F
```

| Parameter | Erforderlich | BESCHREIBUNG |
| --------- | -------- | ----------- |
| {tenant} | Ja | Name des Azure AD B2C-Mandanten. |
| {policy} | Ja | Der Benutzerflow, den Sie zum Abmelden des Benutzers von der Anwendung verwenden möchten. |
| post_logout_redirect_uri | Nein  | Die URL, an die der Benutzer nach erfolgreicher Abmeldung umgeleitet werden soll. Wenn sie nicht angegeben ist, gibt Azure AD B2C eine generische Nachricht an den Benutzer aus. |
| state | Nein  | Wenn ein Parameter `state` in der Anforderung enthalten ist, sollte der gleiche Wert in der Antwort angezeigt werden. Die Anwendung sollte überprüfen, ob die `state`-Werte in der Anforderung und in der Antwort identisch sind. |


> [!NOTE]
> Durch die Weiterleitung des Benutzers zu `end_session_endpoint` wird der SSO-Status des Benutzers in Azure AD B2C teilweise aufgehoben. Allerdings wird der Benutzer nicht von der Sitzung des sozialen Netzwerks als Identitätsanbieter abgemeldet. Wenn der Benutzer bei einer nachfolgenden Anmeldung den gleichen Identitätsanbieter auswählt, wird der Benutzer ohne erneute Eingabe seiner Anmeldeinformationen erneut authentifiziert. Wenn ein Benutzer sich von der Azure AD B2C-Anwendung abmelden möchte, bedeutet dies beispielsweise nicht unbedingt, dass er sich auch vollständig von seinem Facebook-Konto abmelden möchte. Bei lokalen Konten wird die Sitzung des Benutzers jedoch ordnungsgemäß beendet.
>

## <a name="next-steps"></a>Nächste Schritte

### <a name="code-sample-azure-ad-b2c-with-microsoft-authentication-library-for-javascript"></a>Codebeispiel: Azure AD B2C mit Microsoft Authentication Library für JavaScript

[Mit „msal.js“ erstellte Single-Page-Anwendung für Azure AD B2C][github-msal-js-example] (GitHub)

Dieses Beispiel auf GitHub soll Ihnen bei Ihren ersten Schritten mit Azure AD B2C in einer einfachen, mit [msal.js][github-msal-js] erstellten Webanwendung mit Popupauthentifizierung helfen.

<!-- Links - EXTERNAL -->
[github-msal-js-example]: https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp
[github-msal-js]: https://github.com/AzureAD/microsoft-authentication-library-for-js
