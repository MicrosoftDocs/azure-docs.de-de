---
title: Anwendungstypen für Microsoft Identity Platform | Azure
description: Die Typen von Apps und Szenarien, die vom Microsoft Identity Platform-Endpunkt unterstützt werden.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev, fasttrack-edit, contperf-fy21q2
ms.openlocfilehash: fd1fc59fd1ade6036c57f15415afccfc693f7bff
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97029752"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Anwendungstypen für Microsoft Identity Platform

Der Microsoft Identity Platform-Endpunkt unterstützt die Authentifizierung für eine Vielzahl moderner App-Architekturen, die alle auf den branchenüblichen Standardprotokollen [OAuth 2.0 oder OpenID Connect](active-directory-v2-protocols.md) basieren. Dieser Artikel beschreibt die App-Typen, die Sie unabhängig von der bevorzugten Sprache oder Plattform mithilfe von Microsoft Identity Platform erstellen können. Die Informationen dienen Ihrem Verständnis allgemeiner Szenarien, bevor Sie beginnen, in den [Anwendungsszenarios](authentication-flows-app-scenarios.md#application-scenarios) mit Code zu arbeiten.

## <a name="the-basics"></a>Grundlagen

Sie müssen jede App, die den Microsoft Identity Platform-Endpunkt verwendet, im Azure-Portal in [App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908) registrieren. Beim App-Registrierungsvorgang werden die folgenden Werte für die App erfasst und zugewiesen:

* Eine **Anwendungs- (bzw. Client-) ID**, die Ihre App eindeutig identifiziert
* Ein **Umleitungs-URI**, der zum Umleiten von Antworten zurück an die App verwendet werden kann
* Einige andere szenariospezifische Werte, wie z.B. unterstützte Kontotypen

Nähere Informationen finden Sie im Artikel zum [Registrieren einer App](quickstart-register-app.md).

Nach der Registrierung der App erfolgt die Kommunikation zwischen der App und Microsoft Identity Plattform durch Senden von Anforderungen an den Endpunkt. Wir stellen Open-Source-Frameworks und -Bibliotheken bereit, über die die Details dieser Anforderungen verarbeitet werden. Zudem können Sie auch selbst die Authentifizierungslogik implementieren, indem Sie Anforderungen an diese Endpunkte erstellen:

```HTTP
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>Single-Page-Apps (JavaScript)

Viele moderne Apps verfügen über ein Single-Page-Webanwendungs-Front-End, das in erster Linie in JavaScript geschrieben ist, häufig mit einem Framework wie Angular, React oder Vue. Der Microsoft Identity Platform-Endpunkt unterstützt diese Apps durch Verwendung des [OpenID Connect](v2-protocols-oidc.md)-Protokolls zur Authentifizierung und entweder des [OAuth 2.0-Flows zur impliziten Genehmigung](v2-oauth2-implicit-grant-flow.md) oder des neueren [OAuth 2.0-Autorisierungscodes + PKCE-Flows](v2-oauth2-auth-code-flow.md) zur Autorisierung (siehe unten).

Das folgende Flussdiagramm veranschaulicht die OAuth 2.0-Autorisierungscodegenehmigung (ohne Details zu PKCE), bei der die App einen Code vom Endpunkt `authorize` der Microsoft Identity Platform erhält und diesen gegen Token und Aktualisierungstoken unter Verwendung von standortübergreifenden Webanforderungen einlöst. Das Aktualisierungstoken läuft nach jeweils 24 Stunden ab, und die App muss einen weiteren Code anfordern. Zusätzlich zum Zugriffstoken wird normalerweise über denselben Flow und/oder eine separate OpenID Connect-Anforderung (hier nicht dargestellt) auch ein `id_token` angefordert, das den bei der Clientanwendung angemeldeten Benutzer repräsentiert.

:::image type="content" source="media/v2-oauth-auth-code-spa/active-directory-oauth-code-spa.svg" alt-text="Diagramm des OAuth 2.0-Autorisierungscodeflows zwischen einer Single-Page-Webanwendung und dem Endpunkt des Sicherheitstokendiensts." border="false":::

Ein Beispiel für dieses Szenario finden Sie unter [Tutorial: Anmelden von Benutzern und Aufrufen der Microsoft Graph-API aus einer JavaScript-Single-Page-Webanwendung (SPA) mithilfe des Autorisierungscodeflusses](tutorial-v2-javascript-auth-code.md).

### <a name="authorization-code-flow-vs-implicit-flow"></a>Autorisierungscodeflow im Vergleich zum impliziten Fluss

Während des größten Teils des Entwicklungsverlaufs von OAuth 2.0 war der [implizite Fluss](v2-oauth2-implicit-grant-flow.md) die empfohlene Methode zum Erstellen von Single-Page-Apps. Durch den Wegfall von [Cookies von Drittanbietern](reference-third-party-cookies-spas.md) und die [größere Aufmerksamkeit](https://tools.ietf.org/html/draft-ietf-oauth-security-topics-14), die Sicherheitsbedenken im Zusammenhang mit dem impliziten Fluss geschenkt wurde, haben wir auf den Autorisierungscodeflow für Single-Page-Apps umgestellt.

Um die Kompatibilität Ihrer App in Safari und anderen datenschutzbewussten Browsern sicherzustellen, empfehlen wir nicht mehr die Verwendung des impliziten Flusses, sondern stattdessen die Verwendung des Autorisierungscodeflows.

## <a name="web-apps"></a>Web-Apps

Bei Web-Apps (.NET, PHP, Java, Ruby, Python, Node), auf die der Benutzer über einen Browser zugreift, können Sie für die Benutzeranmeldung [OpenID Connect](active-directory-v2-protocols.md) verwenden. In OpenID Connect empfängt die Web-App ein ID-Token. Ein ID-Token ist ein Sicherheitstoken, das die Identität des Benutzers überprüft und Informationen über den Benutzer in Form von Ansprüchen enthält:

```JSON
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Weitere Informationen zu verschiedenen Tokentypen, die im Microsoft Identity Plattform-Endpunkt verwendet werden, finden Sie in der Referenz zu [Zugriffstoken](access-tokens.md) und zu [id_token](id-tokens.md).

In Webserver-Apps werden beim Authentifizierungsablauf für die Anmeldung folgende allgemeine Schritte ausgeführt:

![Zeigt den Authentifizierungsflow für Web-Apps](./media/v2-app-types/convergence-scenarios-webapp.svg)

Sie können die Identität des Benutzers validieren, indem Sie das ID-Token mit einem öffentlichen Signaturschlüssel überprüfen, der vom Microsoft Identity Plattform-Endpunkt empfangen wird. Ein Sitzungscookie wird festgelegt, das zur Identifizierung des Benutzers in nachfolgenden Seitenanforderungen verwendet werden kann.

Um dieses Szenario in Aktion zu sehen, testen Sie die Codebeispiele in [Szenario: Web-App, die Benutzer anmeldet](scenario-web-app-sign-user-overview.md).

Neben der einfachen Anmeldung benötigt eine Webserver-App möglicherweise Zugriff auf einen anderen Webdienst, z.B. auf eine REST-API. In diesem Fall wird die Webserver-App in einem kombinierten OpenID Connect- und OAuth 2.0-Vorgang ausgeführt, indem der [OAuth 2.0-Autorisierungscodefluss](v2-oauth2-auth-code-flow.md) verwendet wird. Weitere Informationen zu diesem Szenario finden Sie in den [ersten Schritten mit Web-Apps und Web-APIs](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIDConnect-DotNet).

## <a name="web-apis"></a>Web-APIs

Mit dem Microsoft Identity Plattform-Endpunkt können Sie Webdienste schützen, z. B. die RESTful-Web-API Ihrer App. Web-APIs können auf zahlreichen Plattformen und in vielen Sprachen implementiert werden. Sie können auch mithilfe von HTTP-Triggern in Azure Functions implementiert werden. Anstelle von ID-Token und Sitzungscookies verwendet eine Web-API ein OAuth 2.0-Zugriffstoken zum Schutz der zugehörigen Daten und zum Authentifizieren eingehender Anforderungen. Der Aufrufer einer Web-API fügt ein Zugriffstoken wie folgt im Autorisierungsheader einer HTTP-Anforderung an:

```HTTP
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Die Web-API verwendet das Zugriffstoken zum Überprüfen der Identität des API-Aufrufers und zum Extrahieren von Informationen über den Aufrufer aus Ansprüchen, die im Zugriffstoken codiert sind. Weitere Informationen zu verschiedenen Tokentypen, die im Microsoft Identity Plattform-Endpunkt verwendet werden, finden Sie in der Referenz zu [Zugriffstoken](access-tokens.md) und zu [id_token](id-tokens.md).

Eine Web-API kann Benutzern die Möglichkeit geben, sich für oder gegen bestimmte Funktionen oder Daten zu entscheiden, indem sie Berechtigungen erhalten, die auch [Bereiche](v2-permissions-and-consent.md) genannt werden. Damit eine aufrufende App Berechtigungen für einen Bereich erhält, muss der Benutzer während eines Ablaufs seine Zustimmung für den Bereich erteilen. Der Microsoft Identity Plattform-Endpunkt fragt die Zustimmung des Benutzers ab und zeichnet dann die Berechtigungen in allen Zugriffstoken auf, die die Web-API empfängt. Die Web-API überprüft das Zugriffstoken, das bei jedem Aufruf empfangen wird, und führt Autorisierungsprüfungen durch.

Eine Web-API kann Zugriffstoken von allen App-Typen empfangen, z. B. von Webserver-Apps, Desktop-Apps, mobilen Apps, Single Page-Apps, serverseitigen Daemons und selbst von anderen Web-APIs. Der allgemeine Ablauf für eine Web-API sieht folgendermaßen aus:

![Zeigt den Authentifizierungsflow für Web-APIs](./media/v2-app-types/convergence-scenarios-webapi.svg)

Informationen zum Schützen einer Web-API mithilfe von OAuth2-Zugriffstoken finden Sie in den Web-API-Codebeispielen in [Szenario: Geschützte Web-API](scenario-protected-web-api-overview.md).

Oftmals müssen Web-APIs auch ausgehende Anforderungen an nachgelagerte Web-APIs stellen, die von Microsoft Identity Plattform geschützt werden. Zu diesem Zweck können Web-APIs den **On-Behalf-Of**-Fluss nutzen, der der Web-API ermöglicht, ein eingehendes Zugriffstoken gegen ein anderes Zugriffstoken zu tauschen, das in ausgehenden Anforderungen verwendet werden soll. Weitere Informationen finden Sie unter [Microsoft Identity Plattform und On-Behalf-Of-Fluss in OAuth 2.0](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>Mobile und native Apps

Auf Geräten installierte Apps, z. B. mobile Apps und Desktop-Apps, benötigen häufig Zugriff auf Back-End-Dienste oder Web-APIs, die im Auftrag eines Benutzers Daten speichern und Funktionen ausführen. Diese Apps können sich mithilfe des [OAuth 2.0-Autorisierungscodeflusses](v2-oauth2-auth-code-flow.md) bei Back-End-Diensten anmelden und die Autorisierung hinzufügen.

Bei diesem Fluss empfängt die App bei der Anmeldung des Benutzers vom Microsoft Identity Platform-Endpunkt einen Autorisierungscode. Der Autorisierungscode stellt die Berechtigung der App zum Aufrufen von Back-End-Diensten im Namen des angemeldeten Benutzers dar. Die App kann den Autorisierungscode im Hintergrund gegen ein OAuth 2.0-Zugriffstoken und ein Aktualisierungstoken austauschen. Die App kann mithilfe des Zugriffstokens Web-APIs in HTTP-Anforderungen authentifizieren und mithilfe des Aktualisierungstokens neue Zugriffstoken abrufen, wenn die älteren Zugriffstoken abgelaufen sind.

![Zeigt den Authentifizierungsflow für native Apps](./media/v2-app-types/convergence-scenarios-native.svg)

> [!NOTE]
> Wenn die Anwendung die standardmäßige Systemwebansicht verwendet, überprüfen Sie die Informationen zur Funktion „Meine Anmeldung bestätigen“ und den Fehlercode AADSTS50199 in [Fehlercodes für die Azure AD-Authentifizierung und Autorisierung](reference-aadsts-error-codes.md).

## <a name="daemons-and-server-side-apps"></a>Daemons und serverseitige Apps

Apps, die Prozesse mit langer Ausführungsdauer umfassen oder ohne Benutzereingriff ausgeführt werden, benötigen auch die Möglichkeit, auf sichere Ressourcen wie Web-APIs zuzugreifen. Diese Apps können mithilfe der App-Identität (anstelle der delegierten Benutzeridentität) über den OAuth 2.0-Ablauf für Clientanmeldeinformationen die Authentifizierung durchführen und Token abrufen. Sie können die Identität der App mit einem geheimen Clientschlüssel oder einem Zertifikat nachweisen. Weitere Informationen finden Sie unter [.NET Core-Daemon-Konsolenanwendung mit Microsoft Identity Platform](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2).

In diesem Flow interagiert die App direkt mit dem `/token`-Endpunkt, um Zugriff zu erhalten:

![Zeigt den Authentifizierungsflow für Daemon-Apps](./media/v2-app-types/convergence-scenarios-daemon.svg)

Informationen zum Erstellen einer Daemon-App finden Sie in der [Dokumentation zu Clientanmeldeinformationen](v2-oauth2-client-creds-grant-flow.md). Sie können aber auch eine [.NET-Beispiel-App](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2) testen.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun mit den von der Microsoft Identity Platform unterstützten Anwendungstypen vertraut sind, informieren Sie sich über [OAuth 2.0 und OpenID Connect](active-directory-v2-protocols.md), um ein besseres Verständnis der in den verschiedenen Szenarien verwendeten Protokollkomponenten zu entwickeln.
