---
title: In Azure AD B2C unterstützte Anwendungstypen
titleSuffix: Azure AD B2C
description: Hier erfahren Sie, welche Anwendungstypen mit Azure Active Directory B2C verwendet werden können.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 07897823a3ba3b83e240e8e8dc005ea13b036fce
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94952045"
---
# <a name="application-types-that-can-be-used-in-active-directory-b2c"></a>In Active Directory B2C verwendbare Anwendungstypen
 
Azure Active Directory B2C (Azure AD B2C) unterstützt die Authentifizierung für eine Vielzahl moderner Anwendungsarchitekturen. Diese basieren alle auf den branchenüblichen Protokollen [OAuth 2.0](protocols-overview.md) oder [OpenID Connect](protocols-overview.md). In diesem Artikel werden die Anwendungstypen beschrieben, die Sie unabhängig von der bevorzugten Sprache oder Plattform erstellen können. Außerdem verdeutlicht es die allgemeinen Szenarios, bevor Sie mit dem Erstellen von Anwendungen beginnen.

Jede Anwendung, die Azure AD B2C verwendet, muss über das [Azure-Portal](https://portal.azure.com/) in Ihrem [Azure AD B2C-Mandanten](tutorial-create-tenant.md) registriert werden. Während des Anwendungsregistrierungsprozesses werden Werte erfasst und zugewiesen, wie z.B.:

* Eine **Anwendungs-ID**, die Ihre Anwendung eindeutig ausweist.
* Ein **Antwort-URI**, der zum Umleiten von Antworten zurück an die Anwendung verwendet werden kann.

Jede an Azure AD B2C gesendete Anforderung gibt einen **Benutzerflow** (eine integrierte Richtlinie) oder eine **benutzerdefinierte Richtlinie** an, die das Verhalten von Azure AD B2C steuert. Beide Richtlinientypen ermöglichen die Erstellung hochgradig anpassbarer Benutzerumgebungen.

Die Interaktion der einzelnen Anwendungen folgt einem ähnlichen allgemeinen Muster:

1. Die Anwendung leitet den Endbenutzer an den v2.0-Endpunkt weiter, um eine [Richtlinie](user-flow-overview.md) auszuführen.
2. Der Benutzer schließt die Richtlinie gemäß Richtliniendefinition ab.
3. Die Anwendung empfängt ein Sicherheitstoken vom v2.0-Endpunkt.
4. Die Anwendung verwendet das Sicherheitstoken für den Zugriff auf geschützte Informationen oder eine geschützte Ressource.
5. Der Ressourcenserver überprüft das Sicherheitstoken, um sicherzustellen, dass der Zugriff gewährt werden kann.
6. Die Anwendung aktualisiert das Sicherheitstoken in regelmäßigen Abständen.

Diese Schritte können sich je nach erstelltem Anwendungstyp geringfügig unterscheiden.

## <a name="web-applications"></a>Webanwendungen

Für Webanwendungen (einschließlich .NET, PHP, Java, Ruby, Python und Node.js), die auf einem Server gehostet werden und über einen Browser zugänglich sind, unterstützt Azure AD B2C [OpenID Connect](protocols-overview.md) für alle Benutzeroberflächen. In der Azure AD B2C-Implementierung von OpenID Connect initiiert Ihre Webanwendung Benutzeroberflächen, indem Authentifizierungsanforderungen für Azure AD ausgegeben werden. Das Ergebnis der Anforderung ist ein `id_token`. Dieses Sicherheitstoken stellt die Identität des Benutzers dar. Darüber hinaus werden Informationen über den Benutzer in Form von Ansprüchen bereitgestellt:

```json
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Informationen zu allen Arten von Token und zu den für eine Anwendung verfügbaren Ansprüchen finden Sie in der [Azure AD B2C-Tokenreferenz](tokens-overview.md).

In einer Webanwendung sind für jede Ausführung einer [Richtlinie](user-flow-overview.md) im Allgemeinen folgende Schritte erforderlich:

1. Der Benutzer durchsucht die Webanwendung.
2. Die Webanwendung leitet den Benutzer an Azure AD B2C um und gibt die auszuführende Richtlinie an.
3. Der Benutzer schließt die Richtlinie ab.
4. Azure AD B2C gibt ein `id_token` an den Browser zurück.
5. Das `id_token` wird für den Umleitungs-URI bereitgestellt.
6. Das `id_token` wird überprüft, und ein Sitzungscookie wird festgelegt.
7. Für den Benutzer wird eine sichere Seite zurückgegeben.

Die Überprüfung von `id_token` mithilfe eines von Azure AD erhaltenen öffentlichen Signaturschlüssels ist ausreichend, um die Identität des Benutzers zu bestätigen. Bei diesem Prozess wird auch ein Sitzungscookie festgelegt, das zur Identifizierung des Benutzers in nachfolgenden Seitenanforderungen verwendet werden kann.

Um dieses Szenario in Aktion zu sehen, können Sie eines der Webanwendungs-Codebeispiele für die Anmeldung im Abschnitt [Erste Schritte](overview.md) testen.

Neben der Bereitstellung der einfachen Anmeldung benötigt eine Webserveranwendung unter Umständen auch Zugriff auf einen Back-End-Webdienst. In diesem Fall kann die Webanwendung einen etwas anderen [OpenID Connect-Ablauf](openid-connect.md) durchführen und Token anhand von Autorisierungscodes und Aktualisierungstoken beschaffen. Dieses Szenario ist im folgenden [Abschnitt zu Web-APIs](#web-apis)dargestellt.

## <a name="single-page-applications"></a>Single-Page-Webanwendungen
Viele moderne Webanwendungen werden als clientseitige Single-Page-Webanwendungen („SPAs“) erstellt. Entwickler schreiben diese mithilfe von JavaScript oder eines SPA-Frameworks wie Angular, Vue oder React. Diese Anwendungen werden in einem Webbrowser ausgeführt und weisen andere Authentifizierungsmerkmale als herkömmliche serverseitige Webanwendungen auf.

Azure AD B2C bietet **zwei** Optionen, um Single-Page-Webanwendungen das Anmelden von Benutzern und Abrufen von Token für den Zugriff auf Back-End-Dienste oder -Web-APIs zu ermöglichen:

### <a name="authorization-code-flow-with-pkce"></a>Autorisierungscodefluss (mit PKCE)
- [OAuth 2.0-Autorisierungscodefluss (mit PKCE)](./authorization-code-flow.md). Der Autorisierungscodefluss ermöglicht es der Anwendung, einen Autorisierungscode für **ID-Token** auszutauschen, um den authentifizierten Benutzer darzustellen, sowie **Zugriffstoken** auszutauschen, die zum Aufrufen geschützter APIs erforderlich sind. Darüber hinaus werden **Aktualisierungstoken** zurückgegeben, die langfristigen Zugriff auf Ressourcen im Auftrag von Benutzern bereitstellen, ohne dass eine Interaktion mit diesen Benutzern erforderlich ist. 

Dies ist die **empfohlene** Vorgehensweise. Aktualisierungstoken mit begrenzter Lebensdauer tragen zur Anpassung Ihrer Anwendung an die [in Bezug auf die Einschränkungen von Cookies geltenden Datenschutzbestimmungen moderner Browser](../active-directory/develop/reference-third-party-cookies-spas.md) (z. B. an Safari ITP) bei.

Um diesen Fluss nutzen zu können, kann Ihre Anwendung eine Authentifizierungsbibliothek verwenden, von der sie unterstützt wird, z. B. [MSAL.js 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser).

<!-- ![Single-page applications-auth](./media/tutorial-single-page-app/spa-app-auth.svg) -->
![Autorisierungsfluss für Single-Page-Webanwendungen](./media/tutorial-single-page-app/active-directory-oauth-code-spa.png)

### <a name="implicit-grant-flow"></a>Impliziter Gewährungsflow
- [Impliziter OAuth 2.0-Fluss](implicit-flow-single-page-application.md). Einige Frameworks, z. B. [MSAL.js 1.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core), unterstützen nur den Fluss für die implizite Genehmigung. Der Fluss für die implizite Genehmigung ermöglicht der Anwendung das Abrufen von **ID-** und **Zugriffstoken**. Im Gegensatz zum Autorisierungscodefluss gibt der Fluss für die implizite Genehmigung kein **Aktualisierungstoken** zurück. 

Dieser Authentifizierungsfluss umfasst keine Anwendungsszenarios, die plattformübergreifende JavaScript-Frameworks verwenden, z. B. Electron und React-Native. Diese Szenarien erfordern weitere Funktionen für die Interaktion mit den nativen Plattformen.

## <a name="web-apis"></a>Web-APIs

Sie können Azure AD B2C zum Schützen von Webdiensten, wie z.B. der RESTful-Web-API Ihrer Anwendung, verwenden. Web-APIs können OAuth 2.0 verwenden, um ihre Daten zu schützen, indem eingehende HTTP-Anforderungen per Token authentifiziert werden. Der Aufrufer einer Web-API fügt ein Token im Autorisierungsheader einer HTTP-Anforderung an:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
``` 

Die Web-API kann dann mit dem Token die Identität des API-Aufrufers überprüfen und Informationen über den Aufrufer aus Ansprüchen extrahieren, die im Token codiert sind. Informationen zu allen Arten von Token und zu den für eine App verfügbaren Ansprüchen finden Sie in der [Azure AD B2C-Tokenreferenz](tokens-overview.md).

Eine Web-API kann Token von vielen Clienttypen empfangen, einschließlich Webanwendungen, Desktop- und mobilen Anwendungen, Single-Page-Anwendungen, serverseitigen Daemons und anderen Web-APIs. Hier sehen Sie ein Beispiel für den vollständigen Ablauf für eine Webanwendung, die eine Web-API aufruft:

1. Die Webanwendung führt eine Richtlinie aus, und der Benutzer schließt die Benutzeroberfläche ab.
2. Azure AD B2C gibt `id_token` (OpenID Connect) und einen Autorisierungscode an den Browser zurück.
3. Der Browser stellt das `id_token` und den Autorisierungscode für den Umleitungs-URI bereit.
4. Der Webserver überprüft das `id_token` und legt ein Sitzungscookie fest.
5. Der Webserver fragt Azure AD B2C nach `access_token`. Dabei gibt er den Autorisierungscode, die Anwendungsclient-ID sowie Clientanmeldeinformationen an.
6. Das `access_token` und das `refresh_token` werden an den Webserver zurückgegeben.
7. Die Web-API wird mit dem `access_token` in einem Autorisierungsheader aufgerufen.
8. Die Web-API überprüft das Token.
9. Sichere Daten werden an die Webanwendung zurückgegeben.

Weitere Informationen zu Autorisierungscodes, Aktualisierungstoken und den Schritten zum Abrufen von Token finden Sie im [OAuth 2.0-Protokoll](authorization-code-flow.md).

Weitere Informationen zum Schützen einer Web-API mit Azure AD B2C finden Sie in den Tutorials zur Web-API im [Abschnitt mit den ersten Schritten](overview.md).

## <a name="mobile-and-native-applications"></a>Mobile und native Anwendungen

Auf Geräten installierte Anwendungen, z.B. mobile Anwendungen und Desktopanwendungen, benötigen häufig Zugriff auf Back-End-Dienste oder Web-APIs im Auftrag von Benutzern. Sie können Ihren nativen Anwendungen angepasste Oberflächen für die Identitätsverwaltung hinzufügen und Back-End-Dienste sicher aufrufen, indem Sie Azure AD B2C und den [Autorisierungscodeablauf von OAuth 2.0](authorization-code-flow.md) verwenden.

Bei diesem Ablauf führt die Anwendung [Richtlinien](user-flow-overview.md) aus und empfängt einen `authorization_code` von Azure AD, nachdem der Benutzer die Richtlinie abgeschlossen hat. Der `authorization_code` stellt die Berechtigung der Anwendung zum Aufrufen von Back-End-Diensten im Namen des derzeit angemeldeten Benutzers dar. Die Anwendung kann dann den `authorization_code` im Hintergrund gegen ein `access_token` und ein `refresh_token` austauschen.  Mit dem `access_token` kann die Anwendung sich in HTTP-Anforderungen bei einer Back-End-Web-API authentifizieren. Sie kann auch das `refresh_token` zum Abrufen eines neuen `access_token` verwenden, wenn ein älteres abläuft.

## <a name="current-limitations"></a>Aktuelle Einschränkungen

### <a name="unsupported-application-types"></a>Nicht unterstützte Anwendungstypen

#### <a name="daemonsserver-side-applications"></a>Daemons/serverseitige Anwendungen

Anwendungen, die lang andauernde Prozesse enthalten oder ohne Benutzereingriff arbeiten, benötigen auch die Möglichkeit, auf sichere Ressourcen wie Web-APIs zuzugreifen. Diese Anwendungen können mithilfe der Identität der Anwendung (anstelle der delegierten Benutzeridentität) und mithilfe des Clientanmeldeinformations-Flows von OAuth 2.0 die Authentifizierung durchführen und Token abrufen. Ein Clientanmeldeinformations-Flow ist nicht gleich einem „Im Auftrag von“-Ablauf, und in der „Im Auftrag von“-Ablauf sollte nicht für die Server-zu-Server-Authentifizierung verwendet werden.

Der Flow für die Gewährung von OAuth 2.0-Clientanmeldeinformationen wird derzeit nicht direkt vom Azure AD B2C-Authentifizierungsdienst unterstützt. Sie können den Clientanmeldeinformations-Flow jedoch mithilfe von Azure AD und Microsoft Identity Platform/Tokenendpunkt für eine Anwendung in Ihrem Azure AD B2C-Mandanten einrichten. Ein Azure AD B2C-Mandant teilt sich einige Funktionen mit Azure AD-Unternehmensmandanten.

Informationen über das Einrichten des Clientanmeldeinformations-Flows finden Sie unter [Azure Active Directory v2.0 und der OAuth 2.0-Clientanmeldeinformations-Flow](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). Bei einer erfolgreichen Authentifizierung wird ein formatiertes Token empfangen, sodass es von Azure AD wie in [Azure AD-Tokenreferenz](../active-directory/develop/id-tokens.md) beschrieben verwendet werden kann.

Anweisungen zum Registrieren einer Verwaltungsanwendung finden Sie unter [Verwalten von Azure AD B2C mit Microsoft Graph](microsoft-graph-get-started.md).

#### <a name="web-api-chains-on-behalf-of-flow"></a>Web-API-Ketten („Im Auftrag von“-Ablauf)

Viele Architekturen umfassen eine Web-API, von der eine andere Downstream-Web-API aufgerufen werden muss, wobei beide durch Azure AD B2C gesichert sind. Dieses Szenario kommt häufig bei nativen Clients mit Web-API-Back-End vor. Dabei wird ein Microsoft-Onlinedienst (z. B. die Microsoft Graph-API) aufgerufen.

Dieses Szenario der verketteten Web-API kann mithilfe der Berechtigung für Anmeldeinformationen über den OAuth 2.0-JWT-Bearer unterstützt werden, auch bekannt als „Im Auftrag von“-Ablauf.  Der „Im Auftrag von“-Ablauf ist in Azure AD B2C derzeit noch nicht implementiert.

### <a name="faulted-apps"></a>Fehlerhafte Apps

Azure AD B2C-Anwendungen dürfen nicht auf folgende Weise bearbeitet werden:

- In anderen Portalen für die Anwendungsverwaltung, z.B. im [Anwendungsregistrierungsportal](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)
- Verwenden der Graph-API oder PowerShell

Wenn Sie die Azure AD B2C-Anwendung außerhalb des Azure-Portals bearbeiten, entsteht eine fehlerhafte Anwendung, die nicht mehr mit Azure AD B2C verwendet werden kann. Löschen Sie die Anwendung, und erstellen Sie die Anwendung neu.

Um die Anwendung zu löschen, rufen Sie das [App-Registrierungsportal](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) auf und löschen dort die Anwendung. Damit die Anwendung angezeigt wird, müssen Sie der Besitzer der Anwendung sein (und nicht nur Administrator des Mandanten).

## <a name="next-steps"></a>Nächste Schritte

Beschäftigen Sie sich ausführlicher mit den integrierten Richtlinien, die von [Benutzerflows in Azure Active Directory B2C](user-flow-overview.md) bereitgestellt werden.