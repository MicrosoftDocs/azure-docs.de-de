---
title: Gründe für eine Aktualisierung auf die Microsoft Identity Platform (v2.0) | Azure
description: Lernen Sie die Unterschiede zwischen Endpunkten der Microsoft Identity Platform (v2.0) und von Azure Active Directory (Azure AD) (v1.0) und die Vorteile einer Aktualisierung auf v2.0 kennen.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: ryanwi
ms.reviewer: saeeda, hirsin, jmprieur, sureshja, jesakowi, lenalepa, kkrishna, negoe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ccac719c78ce2844a8dd37a80445e11baa4a488
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962882"
---
# <a name="why-update-to-microsoft-identity-platform-v20"></a>Gründe für eine Aktualisierung auf die Microsoft Identity Platform (v2.0)

Beim Entwickeln einer neuen Anwendung ist es wichtig, dass Sie die Unterschiede zwischen Endpunkten der Microsoft Identity Platform (v2.0) und von Azure Active Directory (v1.0) kennen. In diesem Artikel werden die wichtigsten Unterschiede zwischen den Endpunkten und einige bestehende Einschränkungen für die Microsoft Identity Platform beschrieben.

> [!NOTE]
> Der Microsoft Identity Platform-Endpunkt unterstützt nicht alle Szenarien und Features von Azure AD. Informieren Sie sich über die [Einschränkungen der Microsoft Identity Platform](#limitations), um zu ermitteln, ob Sie den Microsoft Identity Platform-Endpunkt verwenden sollten.

## <a name="who-can-sign-in"></a>Wer kann sich anmelden?

![Wer kann sich mit v1.0- und v2.0-Endpunkten anmelden?](media/azure-ad-endpoint-comparison/who-can-sign-in.svg)

* Beim v1.0-Endpunkt ist die Anmeldung an Ihrer Anwendung (Azure AD) nur mit Geschäfts-, Schul- und Unikonten möglich.
* Beim Microsoft Identity Platform-Endpunkt ist die Anmeldung mit Geschäfts-, Schul- und Unikonten über Azure AD und mit persönlichen Microsoft-Konten (MSA) (z. B. hotmail.com, outlook.com und msn.com) möglich.
* Für beide Endpunkte werden außerdem Anmeldungen von *[Gastbenutzern](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b)* eines Azure AD-Verzeichnisses für Anwendungen akzeptiert, die für *[einen Mandanten](single-and-multi-tenant-apps.md)* konfiguriert sind, oder für Anwendungen mit *mehreren Mandanten*, für die ein Verweis auf den mandantenspezifischen Endpunkt (`https://login.microsoftonline.com/{TenantId_or_Name}`) konfiguriert ist.

Mit dem Microsoft Identity Platform-Endpunkt können Sie Anwendungen schreiben, die Anmeldungen von persönlichen Microsoft-Konten sowie Geschäfts-, Schul- und Unikonten akzeptieren. Dies gibt Ihnen die Möglichkeit, Ihre Anwendung vollständig kontounabhängig zu schreiben. Wenn Ihre Anwendung beispielsweise [Microsoft Graph](https://graph.microsoft.io) aufruft, sind einige zusätzliche Funktionen und Daten für Geschäftskonten verfügbar, z.B. SharePoint-Websites oder Verzeichnisdaten. Aber für viele Aktionen, z.B. das [Lesen der E-Mail eines Benutzers](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_messages), kann mit demselben Code auf E-Mails für persönliche Konten und auch für Geschäfts-, Schul- und Unikonten zugegriffen werden.

Für den Microsoft Identity Platform-Endpunkt können Sie die Microsoft Authentication Library (MSAL) nutzen, um Zugriff auf die Bereiche für Konsumenten, Weiterbildung und Unternehmen zu erhalten. Der Azure AD v1.0-Endpunkt akzeptiert nur Anmeldungen von Geschäfts-, Schul- und Unikonten.

## <a name="incremental-and-dynamic-consent"></a>Inkrementelle und dynamische Zustimmung

Für Apps, für die der Azure AD v1.0-Endpunkt verwendet wird, müssen vorher die erforderlichen OAuth 2.0-Berechtigungen angegeben werden, z.B.:

![Benutzeroberfläche für die Registrierung von Berechtigungen](./media/azure-ad-endpoint-comparison/app_reg_permissions.png)

Die Berechtigungen, die direkt in der Anwendungsregistrierung festgelegt werden, sind **statisch**. Statische Berechtigungen der Anwendung werden im Azure-Portal definiert und sorgen dafür, dass der Code klar und einfach ist, aber für Entwickler kann dies ggf. zu Problemen führen:

* Die Anwendung muss alle Berechtigungen anfordern, die nach der ersten Anmeldung des Benutzers jemals benötigt werden. Dies kann zu einer langen Liste von Berechtigungen führen, was Endbenutzer davon abhielt, bei der ersten Anmeldung den Zugriff der Anwendung zu genehmigen.

* Die Anwendung muss frühzeitig alle Ressourcen kennen, auf die sie jemals zugreifen muss. Es war schwierig, Apps zu erstellen, die auf eine beliebige Anzahl von Ressourcen zugreifen konnten.

Mit dem Microsoft Identity Platform-Endpunkt können Sie die in den App-Registrierungsinformationen im Azure-Portal definierten statischen Berechtigungen ignorieren und stattdessen Berechtigungen schrittweise anfordern. Dies bedeutet, dass Sie zunächst nach einem absoluten Minimum an Berechtigungen fragen und im Laufe der Zeit mehr anfallen, da der Kunde zusätzliche Anwendungsfunktionen verwendet. Hierzu können Sie die Bereiche angeben, die für Ihre Anwendung zu beliebigen Zeitpunkten benötigt werden, indem Sie die neuen Bereiche in den Parameter `scope` einfügen, wenn Sie ein Zugriffstoken anfordern. Es ist nicht erforderlich, sie in den Informationen der Anwendungsregistrierung vorab zu definieren. Wenn der Benutzer noch nicht seine Zustimmung zu den neuen Bereichen gegeben hat, die der Anforderung hinzugefügt wurden, wird er aufgefordert, seine Zustimmung nur für die neuen Berechtigungen zu erteilen. Weitere Informationen dazu finden Sie unter [Berechtigungen, Zustimmung und Bereiche](v2-permissions-and-consent.md).

Wenn es für eine App zugelassen wird, Berechtigungen dynamisch über den `scope`-Parameter anzufordern, erhalten Entwickler die vollständige Kontrolle über die Erfahrung des Benutzers. Sie können die Zustimmungserfahrung auch vorziehen und alle Berechtigungen in einer anfänglichen Autorisierungsanforderung erfragen. Wenn Ihre Anwendung eine große Anzahl von Berechtigungen erfordert, können Sie die Berechtigungen des Benutzers inkrementell erfassen, während er über die Zeit bestimmte Features der Anwendung verwendet.

Die Administratorzustimmung im Namen einer Organisation erfordert weiterhin die für die Anwendung registrierten statischen Berechtigungen verwendet werden. Sie sollten diese Berechtigungen für die Anwendungen im Anwendungsregistrierungsportal festzulegen, falls ein Administrator die Zustimmung im Namen der gesamten Organisation erteilen muss. Hierdurch werden die Zyklen reduziert, die der Administrator der Organisation zum Einrichten der Anwendung benötigt.

## <a name="scopes-not-resources"></a>Bereiche, keine Ressourcen

Für Apps mit dem v1.0-Endpunkt kann sich eine App wie eine **Ressource** oder ein Empfänger von Token verhalten. Eine Ressource kann eine Anzahl von **Bereichen** oder **OAuth 2.0-Berechtigungen** definieren, die sie versteht, sodass Client-Apps Token für diese Ressource für einen bestimmten Satz von Bereichen anfordern können. Betrachten Sie als Beispiel für eine Ressource die Azure AD Graph-API:

* Ressourcenbezeichner oder `AppID URI`: `https://graph.windows.net/`
* Bereiche oder `oAuth2Permissions`: `Directory.Read`, `Directory.Write` usw.

Dies gilt für den Microsoft Identity Platform-Endpunkt. Eine Anwendung kann sich immer noch als Ressource verhalten, Bereiche definieren und durch einen URI identifiziert werden. Clientanwendungen können immer noch den Zugriff auf diese Bereiche anfordern. Allerdings hat sich die Art und Weise geändert, wie ein Client solche Berechtigungen anfordert.

Für den v1.0-Endpunkt kann eine an Azure AD gesendete OAuth 2.0-Autorisierungsanforderung beispielsweise wie folgt ausgesehen haben:

```text
GET https://login.microsoftonline.com/common/oauth2/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&resource=https://graph.windows.net/
...
```

Hier gibt der **resource**-Parameter an, für welche Ressource die Clientanwendung eine Autorisierung anfordert. Azure AD hat die von der App benötigten Berechtigungen berechnet, und zwar basierend auf einer statischen Konfiguration im Azure-Portal. Die Token wurden entsprechend ausgestellt.

Für Anwendungen, für die der Microsoft Identity Platform-Endpunkt verwendet wird, sieht die entsprechende OAuth 2.0-Autorisierungsanforderung wie folgt aus:

```text
GET https://login.microsoftonline.com/common/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e
&scope=https://graph.windows.net/directory.read%20https://graph.windows.net/directory.write
...
```

Hierbei wird mit dem Parameter **scope** angegeben, für welche Ressourcen und Berechtigungen die Anwendung eine Autorisierung anfordert. Die gewünschte Ressource ist noch in der Anforderung vorhanden – sie ist von den einzelnen Werten des Bereichsparameters umgeben. Wenn der Bereichsparameter auf diese Weise verwendet wird, entspricht der Microsoft Identity Platform-Endpunkt eher der OAuth 2.0-Spezifikation und richtet sich mehr nach den gängigen Methoden der Branche. Damit können Anwendungen auch eine [inkrementelle Zustimmung](#incremental-and-dynamic-consent) ausführen – d. h. nur dann, wenn die Anwendung sie benötigt, und nicht im Voraus.

## <a name="well-known-scopes"></a>Bekannte Bereiche

### <a name="offline-access"></a>Offlinezugriff

Für Apps, die den Microsoft Identity Platform-Endpunkt verwenden, muss unter Umständen eine neue bekannte Berechtigung für Apps verwendet werden: der Bereich `offline_access`. Alle Apps müssen diese Berechtigung anfordern, wenn sie im Auftrag eines Benutzers für einen längeren Zeitraum auf Ressourcen zugreifen wollen, selbst, wenn der Benutzer die App nicht aktiv verwendet. Der `offline_access`-Bereich wird dem Benutzer in den Zustimmungsdialogfeldern als **Jederzeit auf Ihre Daten zugreifen** angezeigt, wofür der Benutzer seine Zustimmung gewähren muss. Durch das Anfordern der `offline_access`-Berechtigung kann Ihre Web-App OAuth 2.0-Aktualisierungstoken vom Microsoft Identity Platform-Endpunkt erhalten. Aktualisierungstoken sind langlebig und können durch neue OAuth 2.0-Zugriffstoken für längere Zugriffszeiten ausgetauscht werden.

Wenn die App den `offline_access`-Bereich nicht anfordert, werden auch keine Aktualisierungstoken empfangen. Dies bedeutet, dass Sie beim Einlösen eines Autorisierungscodes im OAuth 2.0-Autorisierungscodefluss nur ein Zugriffstoken vom `/token`-Endpunkt erhalten. Dieses Zugriffstoken bleibt für einen kurzen Zeitraum (in der Regel eine Stunde) gültig, läuft aber anschließend ab. Zu diesem Zeitpunkt muss Ihre App den Benutzer zurück an den `/authorize`-Endpunkt leiten, um einen neuen Autorisierungscode abzurufen. Während dieser Umleitung muss der Benutzer möglicherweise seine Anmeldeinformationen erneut eingeben oder den Berechtigungen erneut zustimmen, je nach Anwendungstyp.

Weitere Informationen zu OAuth 2.0, `refresh_tokens` und `access_tokens` finden Sie in der [Microsoft Identity Platform-Protokollreferenz](active-directory-v2-protocols.md).

### <a name="openid-profile-and-email"></a>OpenID, Profil und E-Mail

Bislang wurden im grundlegenden OpenID Connect-Anmeldefluss mit Microsoft Identity Platform zahlreiche Benutzerinformationen im resultierenden *id_token* bereitgestellt. Die Ansprüche in einem ID-Token können u. a. Name, bevorzugten Benutzernamen, E-Mail-Adresse, Objekt-ID eines Benutzers enthalten.

Die Informationen, auf die der Bereich `openid` Ihrer App Zugriff gewährt, sind nun beschränkt. Der Bereich `openid` erlaubt Ihrer App nur das Anmelden des Benutzers und das Empfangen eines App-spezifischen Bezeichners für den Benutzer. Falls Sie personenbezogene Daten über den Benutzer in Ihrer Anwendung erhalten möchten, müssen Sie über Ihre App zusätzliche Berechtigungen vom Benutzer einholen. Über zwei neue Bereiche `email` und `profile` können Sie weitere Berechtigungen anfordern.

* Der Bereich `email` gewährt Ihrer Anwendung mithilfe des Anspruchs `email` im „id_token“ Zugriff auf die primäre E-Mail-Adresse des Benutzers, vorausgesetzt, der Benutzer hat eine adressierbare E-Mail-Adresse.
* Der Bereich `profile` ermöglicht Ihrer App den Zugriff auf alle anderen grundlegenden Informationen zum Benutzer: Name, bevorzugter Benutzername, Objekt-ID usw. auf dem „id_token“.

Diese Bereiche können Sie für Ihre Anwendung den Weg der minimalen Offenlegung einschlagen. Sie können Benutzer nur um die Daten bitten, die für die Funktionsweise der Anwendung erforderlich sind. Weitere Informationen zu diesen Bereichen finden Sie in der [Referenz zu den Bereichen der Microsoft Identity Platform](v2-permissions-and-consent.md).

## <a name="token-claims"></a>Tokenansprüche

Der Microsoft Identity Platform-Endpunkt gibt standardmäßig einen kleineren Satz mit Ansprüchen in seinen Token aus, um die Nutzlasten klein zu halten. Wenn Sie Anwendungen und Dienste haben, die von einem bestimmten Anspruch in einem v1.0-Token abhängig sind, der über ein Microsoft Identity Platform-Token nicht mehr standardmäßig bereitgestellt wird, sollten Sie die Funktion [optionale Ansprüche](active-directory-optional-claims.md) verwenden, um diesen Anspruch einzuschließen.

## <a name="limitations"></a>Einschränkungen

Es gelten einige Einschränkungen, mit denen Sie bei Verwendung der Microsoft Identity Platform vertraut sein sollten.

Beim Erstellen von Anwendungen, die in die Microsoft Identity Platform integriert werden, müssen Sie entscheiden, ob der Microsoft Identity Platform-Endpunkt und die Authentifizierungsprotokolle Ihren Anforderungen entsprechen. Der v1.0-Endpunkt und die zugehörige Plattform werden weiterhin vollständig unterstützt und umfassen in gewisser Hinsicht mehr Funktionen als die Microsoft Identity Platform. Mit der Microsoft Identity Platform ergeben sich aber [erhebliche Vorteile](azure-ad-endpoint-comparison.md) für Entwickler.

Hier ist nun eine vereinfachte Empfehlung für Entwickler:

* Verwenden Sie die Microsoft Identity Platform, wenn Sie persönliche Microsoft-Konten in Ihrer Anwendung unterstützen möchten bzw. müssen oder wenn Sie eine neue Anwendung schreiben. Zuvor sollten Sie sich jedoch mit den in diesem Artikel behandelten Einschränkungen vertraut machen.
* Falls Sie eine Anwendung migrieren oder aktualisieren, die auf SAML basiert, können Sie die Microsoft Identity Platform nicht verwenden. Verwenden Sie stattdessen den [Leitfaden zu Azure AD v1.0](v1-overview.md).

Der Microsoft Identity Platform-Endpunkt wird weiterentwickelt, um die hier aufgeführten Einschränkungen zu beseitigen. Das Ziel besteht darin, dass nur noch der Microsoft Identity Platform-Endpunkt verwendet werden muss. Nutzen Sie in der Zwischenzeit diesen Artikel, um zu ermitteln, ob der Microsoft Identity Platform-Endpunkt für Ihre Zwecke geeignet ist. Wir werden diesen Artikel weiter aktualisieren, um den aktuellen Stand des Microsoft Identity Platform-Endpunkts anzugeben. Prüfen Sie den Artikel regelmäßig, um zu ermitteln, ob die Funktionen der Microsoft Identity Platform Ihre Anforderungen noch erfüllen.

### <a name="restrictions-on-app-registrations"></a>Einschränkungen für App-Registrierungen

Für jede App, die Sie in den Microsoft Identity Platform-Endpunkt integrieren möchten, können Sie im Azure-Portal auf der neuen [Benutzeroberfläche für **App-Registrierungen**](https://aka.ms/appregistrations) eine App-Registrierung erstellen. Vorhandene Microsoft-Konto-Apps sind nicht mit dem Portal kompatibel, aber alle Azure AD-Apps. Dies gilt unabhängig davon, wo und wann diese registriert wurden.

App-Registrierungen, die Geschäfts-, Schul- und Unikonten und persönliche Konten unterstützen, haben die folgenden Einschränkungen:

* Pro Anwendungs-ID sind nur zwei Anwendungsgeheimnisse zulässig.
* Eine Anwendung, die nicht in einem Mandanten registriert war, kann nur von dem Konto verwaltet werden, das sie registriert hat. Sie kann nicht für andere Entwicklern freigegeben werden. Dies ist bei den meisten Anwendungen der Fall, die mit einem persönlichen Microsoft-Konto im App-Registrierungsportal registriert wurden. Registrieren Sie die Anwendung unter Verwendung des neuen Abschnitts **App-Registrierungen** im Azure-Portal in einem Mandanten, wenn Sie Ihre App-Registrierung für mehrere Entwickler freigeben möchten.
* Es gibt mehrere Einschränkungen, was das zulässige Format der Umleitungs-URL angeht. Weitere Informationen zur Umleitungs-URL finden Sie im nächsten Abschnitt.

### <a name="restrictions-on-redirect-urls"></a>Einschränkungen für Umleitungs-URLs

Apps, die für die Microsoft Identity Platform registriert sind, sind auf einen begrenzten Satz von URL-Umleitungswerten beschränkt. Die Umleitungs-URL für Web-Apps und -Dienste muss mit dem Schema `https` beginnen, und alle Umleitungs-URL-Werte müssen dieselbe DNS-Domäne verwenden.  Das Registrierungssystem vergleicht den gesamten DNS-Namen der vorhandenen Umleitungs-URL mit dem DNS-Namen der von Ihnen hinzugefügten Umleitungs-URL. `http://localhost` wird auch als Umleitungs-URL unterstützt.  

Die Anforderung zum Hinzufügen des DNS-Namens schlägt fehl, wenn eine der folgenden Bedingungen erfüllt ist:  

* Der gesamte DNS-Name der neuen Umleitungs-URL entspricht nicht dem DNS-Namen der vorhandenen Umleitungs-URL.
* Der gesamte DNS-Name der neuen Umleitungs-URL ist keine Unterdomäne der vorhandenen Umleitungs-URL.

#### <a name="example-1"></a>Beispiel 1

Wenn die Anwendung eine Umleitungs-URL von `https://login.contoso.com` hat, können Sie eine Umleitungs-URL hinzufügen, bei der der DNS-Name genau übereinstimmt, wie im folgenden Beispiel gezeigt:

`https://login.contoso.com/new`

Oder Sie können sich auf eine DNS-Unterdomäne von login.contoso.com beziehen, wie im folgenden Beispiel gezeigt:

`https://new.login.contoso.com`

#### <a name="example-2"></a>Beispiel 2

Wenn Sie eine Anwendung verwenden möchten, für die `login-east.contoso.com` und `login-west.contoso.com` als Umleitungs-URLs angegeben sind, müssen Sie diese URLs in der folgenden Reihenfolge hinzufügen:

`https://contoso.com`  
`https://login-east.contoso.com`  
`https://login-west.contoso.com`  

Die beiden letztgenannten URLs können hinzugefügt werden, da es sich hierbei um Unterdomänen der ersten Umleitungs-URL „contoso.com“ handelt.

Sie können nur 20 Antwort-URLs für eine bestimmte Anwendung haben – diese Begrenzung gilt für alle Anwendungstypen, die die Registrierung unterstützt (SPA, nativer Client, Web-App und Dienst).  

Informationen dazu, wie Sie eine App für die Nutzung mit der Microsoft Identity Platform registrieren, finden Sie unter [Schnellstart: Registrieren einer Anwendung bei Microsoft Identity Platform](quickstart-register-app.md).

### <a name="restrictions-on-libraries-and-sdks"></a>Einschränkungen für Bibliotheken und SDKs

Derzeit ist die Bibliotheksunterstützung für den Microsoft Identity Platform-Endpunkt eingeschränkt. Wenn Sie den Microsoft Identity Platform-Endpunkt in einer Produktionsanwendung verwenden möchten, haben Sie folgende Möglichkeiten:

* Wenn Sie eine Webanwendung erstellen, können Sie die allgemein verfügbare serverseitige Middleware bedenkenlos für die Anmeldung und die Tokenüberprüfung einsetzen. Dazu gehören die OWIN Open ID Connect-Middleware für ASP.NET und das NodeJS Passport-Plug-In. Codebeispiele, für die Microsoft-Middleware verwendet wird, finden Sie im Abschnitt zu den [ersten Schritten mit der Microsoft Identity Platform](v2-overview.md#getting-started).
* Wenn Sie eine Anwendung für Desktop- oder Mobilgeräte entwickeln, können Sie eine der Microsoft Authentication Libraries (MSAL) verwenden. Diese Bibliotheken sind allgemein verfügbar, oder es gibt sie in einer für die Produktion unterstützten Vorschauversion, die Sie gefahrlos in Produktionsanwendungen einsetzen können. In der [Referenz zu Authentifizierungsbibliotheken](reference-v2-libraries.md) erfahren Sie mehr zu den Nutzungsbedingungen der Vorschauversion und verfügbaren Bibliotheken.
* Für Plattformen, die nicht von den Microsoft-Bibliotheken abgedeckt sind, können Sie die Integration mit dem Microsoft Identity Platform-Endpunkt auch erreichen, indem Sie Protokollnachrichten direkt in Ihrem Anwendungscode senden und empfangen. Die Protokolle OpenID Connect und OAuth [wurden explizit dokumentiert](active-directory-v2-protocols.md), um Sie bei einer Integration dieser Art zu unterstützen.
* Zudem können Sie die Open ID Connect- und OAuth-Open Source-Bibliotheken für die Integration in den Microsoft Identity Platform-Endpunkt verwenden. Der Microsoft Identity Platform-Endpunkt sollte ohne Änderungen mit vielen Open Source-Protokollbibliotheken kompatibel sein. Die Verfügbarkeit dieser Bibliotheksarten variiert je nach Sprache und Plattform. Die [Open ID Connect](https://openid.net/connect/)- und die [OAuth 2.0](https://oauth.net/2/)-Websites enthalten eine Liste gängiger Implementierungen. Weitere Informationen finden Sie im Artikel zur [Microsoft Identity Platform und den Authentifizierungsbibliotheken](reference-v2-libraries.md). Er enthält auch die Liste mit den Open Source-Clientbibliotheken und den Beispielen, die für den Microsoft Identity Platform-Endpunkt getestet wurden.
* Hilfreiche Information: Der `.well-known`-Endpunkt für den allgemeinen Microsoft Identity Platform-Endpunkt ist `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`. Ersetzen Sie `common` durch Ihre Mandanten-ID, um mandantenspezifische Daten abzurufen.  

### <a name="protocol-changes"></a>Protokolländerungen

Der Microsoft Identity Platform-Endpunkt unterstützt keinen SAML- oder WS-Verbund, sondern nur Open ID Connect und OAuth 2.0.  Die wesentlichen Änderungen an den OAuth 2.0-Protokollen gegenüber dem v1.0-Endpunkt sind: 

* Der Anspruch `email` wird zurückgegeben, wenn ein optionaler Anspruch konfiguriert **oder** „scope=email“ in der Anforderung angegeben wurde. 
* Der `scope`-Parameter wird jetzt anstelle des `resource`-Parameters unterstützt.  
* Viele Antworten wurden geändert, um die Kompatibilität mit der OAuth 2.0-Spezifikation zu erhöhen, z.B. die korrekte Rückgabe von `expires_in` als int-Wert anstelle einer Zeichenfolge.  

Informationen zum Umfang der vom Microsoft Identity Platform-Endpunkt unterstützten Protokollfunktionen finden Sie in der Referenz [OpenID Connect- und OAuth 2.0-Protokolle](active-directory-v2-protocols.md).

#### <a name="saml-restrictions"></a>SAML-Einschränkungen

Wenn Sie die Active Directory-Authentifizierungsbibliothek (ADAL) in Windows-Anwendungen verwendet haben, haben Sie möglicherweise die integrierte Windows-Authentifizierung genutzt, die mit der SAML-Assertionsgewährung (Security Assertion Markup Language) arbeitet. Diese Gewährung erlaubt Benutzern von Azure AD-Partnermandanten, sich im Hintergrund bei ihrer lokalen Active Directory-Instanz zu authentifizieren, ohne ihre Anmeldeinformationen eingeben zu müssen. Die SAML-Assertionsgewährung wird auf dem Microsoft Identity Platform-Endpunkt nicht unterstützt.
