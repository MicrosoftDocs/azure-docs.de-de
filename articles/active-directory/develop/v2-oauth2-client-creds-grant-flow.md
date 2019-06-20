---
title: Zugreifen auf sichere Ressourcen ohne Benutzerinteraktion mithilfe von Microsoft Identity Platform | Azure
description: Erstellen von Webanwendungen mit der Microsoft Identity Platform-Implementierung des OAuth 2.0-Authentifizierungsprotokolls.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 9b7cfbd7-f89f-4e33-aff2-414edd584b07
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 485f79f79c52067e89fa0a606e76a533c312fb84
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65545092"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-client-credentials-flow"></a>Microsoft Identity Platform und der Fluss von OAuth 2.0-Clientanmeldeinformationen

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Die [Gewährung von OAuth 2.0-Clientanmeldeinformationen](https://tools.ietf.org/html/rfc6749#section-4.4), die in RFC 6749 angegeben ist und gelegentlich als *zweibeinige OAuth-Autorisierung* bezeichnet wird, kann für den Zugriff auf webgehostete Ressourcen über die Identität einer Anwendung verwendet werden. Diese Art der Gewährung wird häufig für Interaktionen zwischen Servern verwendet, die ohne Benutzereingriff im Hintergrund ausgeführt werden müssen. Diese Anwendungstypen werden oft als *Daemons* oder *Dienstkonten* bezeichnet.

Beim Fluss zur Gewährung von OAuth 2.0-Clientanmeldeinformationen kann ein Webdienst (ein vertraulicher Client) seine eigenen Anmeldeinformationen zum Authentifizieren verwenden, wenn ein anderer Webdienst aufgerufen wird, anstatt die Identität eines Benutzers anzunehmen. In diesem Szenario ist der Client normalerweise ein Webdienst der mittleren Ebene, ein Daemondienst oder eine Website. Für ein höheres Maß an Sicherheit bietet die Microsoft Identity Platform auch die Möglichkeit, dass der aufrufende Dienst ein Zertifikat (statt eines gemeinsamen Geheimnisses) als Anmeldeinformationen verwendet.

> [!NOTE]
> Der Microsoft Identity Platform-Endpunkt unterstützt nicht alle Szenarien und Features von Azure AD. Informieren Sie sich über die [Einschränkungen von Microsoft Identity Platform](active-directory-v2-limitations.md), um zu bestimmen, ob Sie den Microsoft Identity Platform-Endpunkt verwenden sollten.

In der üblicheren *dreibeinigen OAuth-Autorisierung* wird einer Clientanwendung die Berechtigung zum Zugriff auf eine Ressource im Auftrag eines bestimmten Benutzers gewährt. Die Berechtigung wird in der Regel während des [Zustimmungsprozesses](v2-permissions-and-consent.md) vom Benutzer an die Anwendung delegiert. Allerdings werden Berechtigungen im Clientanmeldeinformations-Flow (*zweibeinige OAuth-Authentifizierung*) direkt an die Anwendung selbst erteilt. Wenn die App einer Ressource ein Token anbietet, erzwingt die Ressource, dass die App selbst (und nicht der Benutzer) für die Ausführung einer Aktion autorisiert ist.

## <a name="protocol-diagram"></a>Protokolldiagramm

Der gesamte Clientanmeldeinformations-Flow ist im folgenden Diagramm dargestellt. Wir beschreiben die einzelnen Schritte weiter unten in diesem Artikel.

![Clientanmeldeinformations-Flow](./media/v2-oauth2-client-creds-grant-flow/convergence-scenarios-client-creds.svg)

## <a name="get-direct-authorization"></a>Erhalten der direkten Autorisierung

Eine App empfängt die direkte Autorisierung für den Zugriff auf eine Ressource in der Regel auf zwei Arten:

* [Über eine Zugriffssteuerungsliste (Access Control List, ACL) der Ressource](#access-control-lists)
* [Durch Zuweisung einer Anwendungsberechtigung in Azure AD](#application-permissions)

Diese beiden Methoden sind in Azure AD am gebräuchlichsten und werden von uns für Clients und Ressourcen empfohlen, die den Clientanmeldeinformations-Flow ausführen. Eine Ressource kann auch wählen, dass die Clients auf andere Weise autorisiert werden sollen. Jeder Ressourcenserver kann die Methode auswählen, die am sinnvollsten für seine Anwendung ist.

### <a name="access-control-lists"></a>Zugriffssteuerungslisten

Ein Ressourcenanbieter erzwingt möglicherweise eine Autorisierungsprüfung anhand einer Liste von Anwendungs-IDs (Client-IDs), die ihm bekannt sind, und erteilt eine bestimmte Zugriffsebene. Empfängt die Ressource ein Token vom Microsoft Identity Platform-Endpunkt, kann sie das Token decodieren und die Anwendungs-ID des Clients aus den Ansprüchen `appid` und `iss` extrahieren. Anschließend vergleicht sie die Anwendung mit der von ihr verwalteten Zugriffssteuerungsliste. Granularität der Zugriffssteuerungsliste und Methode können sich zwischen Ressourcen erheblich unterscheiden.

Ein allgemeiner Anwendungsfall ist die Verwendung einer Zugriffssteuerungsliste zum Ausführen von Tests für eine Webanwendung oder eine Web-API. Die Web-API gewährt einem bestimmten Client möglicherweise nur eine Teilmenge der vollständigen Berechtigungen. Zum Ausführen von End-to-End-Tests auf der API wird jedoch ein Testclient erstellt, der Token vom Microsoft Identity Platform-Endpunkt erhält und diese anschließend an die API sendet. Die API überprüft die Anwendungs-ID des Testclients anschließend anhand der Zugriffssteuerungsliste, um Vollzugriff auf die gesamte Funktionalität der API zu gewähren. Wenn Sie eine solche Zugriffssteuerungsliste verwenden, müssen Sie nicht nur den `appid`-Wert des Aufrufers überprüfen, sondern auch sicherstellen, dass der `iss`-Wert des Tokens vertrauenswürdig ist.

Diese Art der Autorisierung wird häufig für Daemons und Dienstkonten eingesetzt, die auf Daten zugreifen müssen, die sich im Besitz von Privatnutzern mit persönlichen Microsoft-Konten befinden. Für Daten, die Organisationen gehören, empfiehlt es sich, die erforderliche Autorisierung über Anwendungsberechtigungen zu erhalten.

### <a name="application-permissions"></a>Anwendungsberechtigungen

Anstelle von Zugriffssteuerungslisten können Sie APIs verwenden, um einen Satz von Anwendungsberechtigungen verfügbar zu machen. Eine Anwendungsberechtigung wird einer Anwendung von einem Administrator einer Organisation erteilt und kann nur für den Zugriff auf Daten verwendet werden, die sich im Besitz der jeweiligen Organisation und deren Mitarbeiter befinden. Microsoft Graph macht beispielsweise verschiedene Anwendungsberechtigungen für Folgendes verfügbar:

* Lesen von E-Mails in allen Postfächern
* Lesen und Schreiben von E-Mails in allen Postfächern
* Senden von E-Mails als beliebiger Benutzer
* Verzeichnisdaten lesen

Weitere Informationen zu Anwendungsberechtigungen finden Sie unter [Microsoft Graph](https://developer.microsoft.com/graph).

Wenn Sie Anwendungsberechtigungen in Ihrer App verwenden möchten, führen Sie die in den nächsten Abschnitten erläuterten Schritte aus.

#### <a name="request-the-permissions-in-the-app-registration-portal"></a>Anfordern der Berechtigungen im App-Registrierungsportal

1. Registrieren und erstellen Sie eine App über die neue [Oberfläche „App-Registrierungen“ (Vorschauversion)](quickstart-register-app.md).
2. Wechseln Sie auf der Benutzeroberfläche von „App-Registrierungen“ (Vorschauversion) zu Ihrer Anwendung. Navigieren Sie zum Abschnitt **Zertifikate und Geheimnisse**, und fügen Sie einen **neuen geheimen Clientschlüssel** hinzu, weil Sie mindestens einen geheimen Clientschlüssel benötigen, um ein Token anzufordern.
3. Suchen Sie den Abschnitt **API-Berechtigungen**, und fügen Sie die **Anwendungsberechtigungen** hinzu, die von Ihrer App benötigt werden.
4. **Speichern** Sie die App-Registrierung.

#### <a name="recommended-sign-the-user-into-your-app"></a>Empfohlen: Anmelden des Benutzers bei Ihrer App

Wenn Sie eine Anwendung erstellen, die Anwendungsberechtigungen verwendet, erfordert die App in der Regel eine Seite oder eine Sicht, auf der der Administrator Berechtigungen für die App genehmigt. Diese Seite kann Teil des Anmelde-Flows der App, Teil der App-Einstellungen oder ein dedizierter Flow „Verbinden“ sein. In vielen Fällen ist es sinnvoll, wenn die App diese Ansicht „Verbinden“ erst anzeigt, wenn ein Benutzer sich mit einem Geschäfts-, Schul- oder Unikonto von Microsoft angemeldet hat.

Durch das Anmelden des Benutzers bei Ihrer App können Sie die Organisation identifizieren, der der Benutzer angehört, bevor Sie ihn zur Genehmigung der Anwendungsberechtigungen auffordern. Auch wenn es nicht unbedingt erforderlich ist, können Sie für Ihre Benutzer auf diese Weise eine intuitivere Benutzeroberfläche erstellen. Befolgen Sie unsere Tutorials zum [Microsoft Identity Platform-Protokoll](active-directory-v2-protocols.md), um den Benutzer anzumelden.

#### <a name="request-the-permissions-from-a-directory-admin"></a>Anfordern der Berechtigungen von einem Verzeichnisadministrator

Wenn Sie dazu bereit sind, vom Administrator der Organisation Berechtigungen anzufordern, können Sie den Benutzer zum *Endpunkt für die Administratorzustimmung* von Microsoft Identity Platform umleiten.

> [!TIP]
> Führen Sie diese Anforderung in Postman aus. (Verwenden Sie die ID Ihrer eigenen Anwendung, um optimale Ergebnisse zu erzielen – die Tutorialanwendung fordert keine nützlichen Berechtigungen an.) [![Ausführen in Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

GET https://login.microsoftonline.com/{tenant}/adminconsent?
client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&state=12345
&redirect_uri=http://localhost/myapp/permissions
```

```
// Pro tip: Try pasting the following request in a browser.
```

```
https://login.microsoftonline.com/common/adminconsent?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&state=12345&redirect_uri=http://localhost/myapp/permissions
```

| Parameter | Bedingung | BESCHREIBUNG |
| --- | --- | --- |
| `tenant` | Erforderlich | Der Verzeichnismandant, von dem Sie die Berechtigung anfordern möchten. Kann als GUID oder als Anzeigename bereitgestellt werden. Wenn Sie nicht wissen, zu welchem Mandanten der Benutzer gehört, und wenn der Benutzer sich bei jedem Mandanten anmelden können soll, verwenden Sie `common`. |
| `client_id` | Erforderlich | Die **Anwendungs-ID (Client-ID)** , die Ihrer App im [Azure-Portal auf der Seite „App-Registrierungen“](https://go.microsoft.com/fwlink/?linkid=2083908) zugewiesen wurde. |
| `redirect_uri` | Erforderlich | Der Umleitungs-URI, an den die Antwort zur Verarbeitung durch die App gesendet werden soll. Er muss genau einem der Umleitungs-URIs entsprechen, die Sie im Portal registriert haben, mit dem Unterschied, dass er URL-codiert sein muss und zusätzliche Pfadsegmente aufweisen kann. |
| `state` | Empfohlen | Ein in der Anforderung enthaltener Wert, der ebenfalls in der Tokenantwort zurückgegeben wird. Es kann sich um eine Zeichenfolge mit jedem beliebigen Inhalt handeln. Der Status wird verwendet, um Informationen über den Status des Benutzers in der App zu codieren, bevor die Authentifizierungsanforderung aufgetreten ist, z.B. Informationen zu der Seite oder Ansicht, die der Benutzer besucht hat. |

An diesem Punkt erzwingt Azure AD, dass sich nur ein Mandantenadministrator anmelden kann, um die Anforderung abzuschließen. Der Administrator wird aufgefordert, alle direkten Anwendungsberechtigungen zu genehmigen, die Sie für Ihre App im App-Registrierungsportal angefordert haben.

##### <a name="successful-response"></a>Erfolgreiche Antwort

Wenn der Administrator die Berechtigungen für Ihre Anwendung genehmigt, lautet die erfolgreiche Antwort wie folgt:

```
GET http://localhost/myapp/permissions?tenant=a8990e1f-ff32-408a-9f8e-78d3b9139b95&state=state=12345&admin_consent=True
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| `tenant` | Der Verzeichnismandant, der Ihrer Anwendung die angeforderten Berechtigungen erteilt hat, im GUID-Format |
| `state` | Ein in der Anforderung enthaltener Wert, der auch in der Tokenantwort zurückgegeben wird. Es kann sich um eine Zeichenfolge mit jedem beliebigen Inhalt handeln. Der Status wird verwendet, um Informationen über den Status des Benutzers in der App zu codieren, bevor die Authentifizierungsanforderung aufgetreten ist, z.B. Informationen zu der Seite oder Ansicht, die der Benutzer besucht hat. |
| `admin_consent` | Legen Sie den Wert auf **True** fest. |

##### <a name="error-response"></a>Fehlerantwort

Wenn der Administrator die Berechtigungen für Ihre Anwendung nicht genehmigt, lautet die Fehlerantwort wie folgt:

```
GET http://localhost/myapp/permissions?error=permission_denied&error_description=The+admin+canceled+the+request
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| `error` | Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| `error_description` | Eine spezifische Fehlermeldung, mit der Sie die Grundursache eines Fehlers identifizieren können. |

Nachdem Sie eine erfolgreiche Antwort vom App-Bereitstellungsendpunkt erhalten haben, erhält Ihre App die direkten Anwendungsberechtigungen, die sie angefordert hat. Als Nächstes können Sie ein Token für eine gewünschte Ressource anfordern.

## <a name="get-a-token"></a>Abrufen von Token

Sobald Sie die notwendige Autorisierung für Ihre Anwendung erhalten haben, können Sie mit dem Abrufen von Zugriffstoken für APIs fortfahren. Um ein Token über die Gewährung von Clientanmeldeinformationen zu erhalten, senden Sie eine POST-Anforderung an den `/token`-Microsoft Identity Platform-Endpunkt:

> [!TIP]
> Führen Sie diese Anforderung in Postman aus. (Verwenden Sie die ID Ihrer eigenen Anwendung, um optimale Ergebnisse zu erzielen – die Tutorialanwendung fordert keine nützlichen Berechtigungen an.) [![Ausführen in Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

### <a name="first-case-access-token-request-with-a-shared-secret"></a>Erster Fall: Zugriffstokenanforderung mit einem gemeinsamen Geheimnis

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

```
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=535fb089-9ff3-47b6-9bfb-4f1264799865&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default&client_secret=qWgdYAmab0YSkuL1qKv5bPX&grant_type=client_credentials' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| Parameter | Bedingung | BESCHREIBUNG |
| --- | --- | --- |
| `tenant` | Erforderlich | Der Verzeichnismandant, der von der Anwendung für den Betrieb verwendet werden soll, im GUID- oder Domänennamensformat. |
| `client_id` | Erforderlich | Die Anwendungs-ID, die Ihrer App zugewiesen ist. Diese Informationen finden Sie in dem Portal, in dem Sie Ihre App registriert haben. |
| `scope` | Erforderlich | Bei dem Wert, der in dieser Anforderung für den `scope`-Parameter übergeben wird, muss es sich um den Ressourcenbezeichner (Anwendungs-ID-URI) der gewünschten Ressource mit dem Suffix `.default` handeln. Für das angegebene Microsoft Graph-Beispiel ist der Wert `https://graph.microsoft.com/.default`. <br/>Dieser Wert weist den Microsoft Identity Platform-Endpunkt an, von allen direkten Anwendungsberechtigungen, die Sie für Ihre App konfiguriert haben, ein Token für diejenigen auszustellen, die zur gewünschten Ressource gehören. Weitere Informationen zum `/.default`-Bereich finden Sie in der [Dokumentation zu Zustimmungen](v2-permissions-and-consent.md#the-default-scope). |
| `client_secret` | Erforderlich | Der geheime Clientschlüssel, den Sie im App-Registrierungsportal für Ihre App generiert haben. Der geheime Clientschlüssel muss vor dem Senden URL-codiert werden. |
| `grant_type` | Erforderlich | Muss auf `client_credentials` festgelegt sein. |

### <a name="second-case-access-token-request-with-a-certificate"></a>Zweiter Fall: Zugriffstokenanforderung mit einem Zertifikat

```
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

| Parameter | Bedingung | BESCHREIBUNG |
| --- | --- | --- |
| `tenant` | Erforderlich | Der Verzeichnismandant, der von der Anwendung für den Betrieb verwendet werden soll, im GUID- oder Domänennamensformat. |
| `client_id` | Erforderlich |Die Anwendungs-ID (Client-ID), die Ihrer App zugewiesen ist. |
| `scope` | Erforderlich | Bei dem Wert, der in dieser Anforderung für den `scope`-Parameter übergeben wird, muss es sich um den Ressourcenbezeichner (Anwendungs-ID-URI) der gewünschten Ressource mit dem Suffix `.default` handeln. Für das angegebene Microsoft Graph-Beispiel ist der Wert `https://graph.microsoft.com/.default`. <br/>Dieser Wert teilt dem Microsoft Identity Platform-Endpunkt mit, dass er von allen direkten Anwendungsberechtigungen, die Sie für Ihre App konfiguriert haben, ein Token für diejenigen ausstellen soll, die zur gewünschten Ressource gehören. Weitere Informationen zum `/.default`-Bereich finden Sie in der [Dokumentation zu Zustimmungen](v2-permissions-and-consent.md#the-default-scope). |
| `client_assertion_type` | Erforderlich | Der Wert muss auf `urn:ietf:params:oauth:client-assertion-type:jwt-bearer` festgelegt werden. |
| `client_assertion` | Erforderlich | Eine Assertion (ein JSON-Webtoken), die Sie benötigen, um das Zertifikat, das Sie als Anmeldeinformationen für Ihre Anwendung registriert haben, zu erstellen und sich damit anzumelden. Informationen zum Registrieren Ihres Zertifikats sowie zum Format der Assertion finden Sie im Abschnitt [Zertifikatanmeldeinformationen](active-directory-certificate-credentials.md).|
| `grant_type` | Erforderlich | Muss auf `client_credentials` festgelegt sein. |

Beachten Sie, dass die Parameter nahezu identisch mit den Parametern der Anforderung mit dem gemeinsamen geheimen Schlüssel sind. Einziger Unterschied: Anstelle des Parameters „client_secret“ werden die beiden Parameter „client_assertion_type“ und „client_assertion“ verwendet.

### <a name="successful-response"></a>Erfolgreiche Antwort

Eine erfolgreiche Antwort sieht wie folgt aus:

```
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik1uQ19WWmNBVGZNNXBP..."
}
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| `access_token` | Das angeforderte Zugriffstoken. Die App kann dieses Token zur Authentifizierung bei geschützten Ressourcen wie einer Web-API verwenden. |
| `token_type` | Gibt den Wert des Tokentyps an. Die Microsoft Identity Platform unterstützt nur den Typ `bearer`. |
| `expires_in` | Die Gültigkeitsdauer eines Zugriffstokens (in Sekunden). |

### <a name="error-response"></a>Fehlerantwort

Eine Fehlerantwort sieht wie folgt aus:

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/.default is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| Parameter | BESCHREIBUNG |
| --- | --- |
| `error` | Eine Fehlercodezeichenfolge, die verwendet werden kann, um unterschiedliche Arten auftretender Fehler zu klassifizieren und um auf Fehler zu reagieren. |
| `error_description` | Eine spezifische Fehlermeldung, mit der Sie die Hauptursache eines Authentifizierungsfehlers identifizieren können |
| `error_codes` | Eine Liste der STS-spezifischen Fehlercodes, die bei der Diagnose helfen können |
| `timestamp` | Die Uhrzeit, zu der der Fehler aufgetreten ist |
| `trace_id` | Ein eindeutiger Bezeichner für die Anforderung, der bei der Diagnose hilfreich sein kann |
| `correlation_id` | Ein eindeutiger Bezeichner für die Anforderung, der bei der komponentenübergreifenden Diagnose hilfreich sein kann |

> [!NOTE]
> Damit Ihre Anwendung das v2-Token empfangen kann, können Sie die Manifestdatei der Anwendung im Azure-Portal aktualisieren. Sie können das Attribut `accessTokenAcceptedVersion` hinzufügen und den Wert auf 2 festlegen, wie in `"accessTokenAcceptedVersion": 2` zu sehen. Sie sollten den Artikel [Anwendungsmanifest](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest#manifest-reference) lesen, um mehr darüber zu erfahren. Standardmäßig empfängt die Anwendung derzeit ein v1-Token. Wenn dies nicht im Anwendungs-/Web-API-Manifest definiert ist, wird für dieses Attribut im Manifest standardmäßig der Wert 1 festgelegt, sodass die Anwendung folglich v1-Token empfängt.  


## <a name="use-a-token"></a>Verwenden eines Tokens

Da Sie jetzt ein Token abgerufen haben, können Sie dieses Token verwenden, um Anforderungen an die Ressource zu stellen. Wenn das Token abläuft, wiederholen Sie die Anforderung an den `/token`-Endpunkt, um ein neues Zugriffstoken abzurufen.

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

```
// Pro tip: Try the following command! (Replace the token with your own.)
```

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## <a name="code-samples-and-other-documentation"></a>Codebeispiele und anderes Dokumentationsmaterial

Lesen Sie die [Übersichtsdokumentation zu Clientanmeldeinformationen](https://aka.ms/msal-net-client-credentials) aus der Microsoft-Authentifizierungsbibliothek.

| Beispiel | Plattform |BESCHREIBUNG |
|--------|----------|------------|
|[active-directory-dotnetcore-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2) | .NET Core 2.1-Konsole | Eine einfache .NET Core-Anwendung, die die Benutzer eines Mandanten anzeigt, der Microsoft Graph unter Verwendung der Anwendungsidentität (statt im Auftrag eines Benutzers) abfragt. Das Beispiel veranschaulicht außerdem die Variante, bei der Zertifikate für die Authentifizierung verwendet werden. |
|[active-directory-dotnet-daemon-v2](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2)|ASP.NET MVC | Eine Webanwendung, die Daten aus Microsoft Graph unter Verwendung der Anwendungsidentität (statt im Auftrag eines Benutzers) synchronisiert. |
