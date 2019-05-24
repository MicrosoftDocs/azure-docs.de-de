---
title: Verwalten von Token (Microsoft-Authentifizierungsbibliothek) | Azure
description: Erfahren Sie mehr über das Abrufen und Zwischenspeichern von Token mithilfe der Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ca011ec7185b084de6d1d346556c1c270c7aee3
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546086"
---
# <a name="acquiring-and-caching-tokens-using-msal"></a>Abrufen und Zwischenspeichern von Token mit MSAL
[Zugriffstoken](access-tokens.md) ermöglichen Clients das sichere Aufrufen von Web-APIs, die durch Azure geschützt sind. Es gibt viele Möglichkeiten zum Abrufen von Token mithilfe der Microsoft-Authentifizierungsbibliothek. In einigen Fällen sind Benutzerinteraktionen über einen Webbrowser erforderlich und in anderen Fällen nicht. Wie Sie ein Token anfordern, hängt in der Regel davon ab, ob es sich bei der Anwendung um eine öffentliche Clientanwendung (Desktopanwendung oder mobile App) oder um eine vertrauliche Clientanwendung (Web-App, Web-API oder Daemon-Anwendung wie einen Windows-Dienst) handelt.

Nachdem ein Token abgerufen wurde, wird es von MSAL zwischengespeichert.  Der Anwendungscode sollte zunächst versuchen, ein Token automatisch (aus dem Cache) abzurufen, bevor andere Methoden angewendet werden.

Sie können den Inhalt des Tokencaches auch löschen, indem Sie die Konten aus dem Cache entfernen. Das Sitzungscookie, das sich im Browser befindet, wird dabei jedoch nicht entfernt.

## <a name="scopes-when-acquiring-tokens"></a>Geltungsbereiche beim Abrufen von Token
[Geltungsbereiche](v2-permissions-and-consent.md) sind die Berechtigungen, die von einer Web-API verfügbar gemacht werden, damit Clientanwendungen Zugriff anfordern können. Clientanwendungen fordern die Benutzereinwilligung für diese Geltungsbereiche an, wenn sie Authentifizierungsanforderungen zum Tokenabruf senden, um auf die Web-APIs zuzugreifen. Über MSAL können Sie Token für den Zugriff auf APIs für Azure AD für Entwickler (v1.0) und Microsoft Identity Platform (v2.0) abrufen. Das v2.0-Protokoll verwendet in den Anforderungen Geltungsbereiche anstelle von Ressourcen. Weitere Informationen finden Sie unter [Vergleich von v1.0 und v2.0](active-directory-v2-compare.md). Abhängig von der Web-API-Konfiguration der akzeptierten Tokenversion gibt der v2.0-Endpunkt das Zugriffstoken an MSAL zurück.

Verschiedene MSAL-Methoden für den Tokenabruf erfordern einen *scopes*-Parameter. Bei diesem Parameter handelt es sich um eine einfache Liste von Zeichenfolgen, die die gewünschten Berechtigungen und Ressourcen deklarieren, die angefordert werden. Bekannte Geltungsbereiche sind [Microsoft Graph-Berechtigungen](/graph/permissions-reference).

In MSAL kann auch auf v1.0-Ressourcen zugegriffen werden. Weitere Informationen finden Sie unter [Geltungsbereiche für eine v1.0-Anwendung](msal-v1-app-scopes.md).

### <a name="request-specific-scopes-for-a-web-api"></a>Anfordern bestimmter Geltungsbereiche für eine Web-API
Wenn Ihre Anwendung Token mit bestimmten Berechtigungen für eine API-Ressource anfordern muss, müssen Sie die Geltungsbereiche, die den App-ID-URI der API enthalten, im folgenden Format übergeben: *&lt;App-ID-URI&gt;/&lt;Geltungsbereich&gt;*

Beispiel für Geltungsbereiche der Microsoft Graph-API: `https://graph.microsoft.com/User.Read`

Oder ein Beispiel für Geltungsbereiche einer benutzerdefinierten Web-API: `api://abscdefgh-1234-abcd-efgh-1234567890/api.read`

Ausschließlich bei der Microsoft Graph-API ist der Geltungsbereichswert `user.read` dem Format `https://graph.microsoft.com/User.Read` zugeordnet und somit austauschbar.

> [!NOTE]
> Für bestimmte Web-APIs wie die Azure Resource Manager API (https://management.core.windows.net/)) wird ein nachgestellter Schrägstrich (/) im aud-Anspruch (Audience) des Zugriffstokens erwartet. In diesem Fall ist es wichtig, den Geltungsbereich als https://management.core.windows.net//user_impersonation (beachten Sie den doppelten Schrägstrich) zu übergeben, damit das Token in der API gültig ist.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>Anfordern dynamischer Geltungsbereiche für inkrementelle Einwilligungen
Beim Erstellen von Anwendungen mit v1.0 mussten Sie den vollständigen Satz der von der Anwendung benötigten Berechtigungen (statische Geltungsbereiche) registrieren, damit bei der Anmeldung die Benutzereinwilligung eingeholt werden konnte. In v2.0 können Sie über den scope-Parameter zusätzliche Berechtigungen nach Bedarf anfordern. Diese werden als dynamische Geltungsbereiche bezeichnet und ermöglichen dem Benutzer, inkrementelle Einwilligungen für Geltungsbereiche zu erteilen.

Beispielsweise können Sie Benutzern nach der Anmeldung zunächst jeglichen Zugriff verweigern. Später können Sie ihnen dann die Möglichkeit einräumen, den Benutzerkalender zu lesen, indem der Kalendergeltungsbereich in den Tokenabrufmethoden angefordert und die Benutzereinwilligung eingeholt werden.

Beispiel: `https://graph.microsoft.com/User.Read` und `https://graph.microsoft.com/Calendar.Read`

## <a name="acquiring-tokens-silently-from-the-cache"></a>Automatisches Abrufen von Token (aus dem Cache)
MSAL verwaltet einen Tokencache (bzw. zwei Caches im Fall von vertraulichen Clientanwendungen). Nach dem Abruf werden Token in der Liste zwischengespeichert.  Beim automatischen Tokenabruf wird in vielen Fällen ein weiteres Token mit weiteren Geltungsbereichen abgerufen, die auf einem Token im Cache basieren. Auch die Aktualisierung von Token, die demnächst ablaufen, ist möglich (da der Tokencache auch ein Aktualisierungstoken enthält).

### <a name="recommended-call-pattern-for-public-client-applications"></a>Empfohlene Aufrufmuster für öffentliche Clientanwendungen
Der Anwendungscode sollte zunächst versuchen, ein Token automatisch (aus dem Cache) abzurufen.  Wenn der Methodenaufruf einen Fehler oder eine Ausnahme vom Typ „UI erforderlich“ zurückgibt, sollten Sie andere Methoden für den Tokenabruf probieren. 

Eine Ausnahme bilden zwei Flows, für die Token **nicht** automatisch abgerufen werden sollten:

- der [Flow für Clientanmeldeinformationen](msal-authentication-flows.md#client-credentials), für den statt des Benutzertokencaches ein Anwendungstokencache verwendet wird. Bei dieser Methode wird der Anwendungstokencache überprüft, bevor eine Anforderung an den STS gesendet wird.
- der [Flow für Autorisierungscode](msal-authentication-flows.md#authorization-code) in Web-Apps. In diesem Fall wird der von der Anwendung abgerufene Code eingelöst, indem der Benutzer angemeldet und seine Einwilligung für weitere Geltungsbereiche eingeholt wird. Da ein Code als Parameter und nicht als Konto übergeben wird, sind bei dieser Methode vor dem Einlösen des Codes keine Einblicke in den Cache möglich. Schon allein deshalb ist der Aufruf des Diensts erforderlich.

### <a name="recommended-call-pattern-in-web-apps-using-the-authorization-code-flow"></a>Empfohlene Aufrufmuster in Web-Apps bei Verwendung des Flows für Autorisierungscode 
Für Webanwendungen, die den [Autorisierungscodeflow „OpenID Connect“](v2-protocols-oidc.md) verwenden, wird das folgende Muster in Controllern empfohlen:

- Instanziieren einer vertraulichen Clientanwendung mit einem Tokencache unter Verwendung der benutzerdefinierten Serialisierung 
- Abrufen des Tokens mithilfe des Flows für Autorisierungscode

## <a name="acquiring-tokens"></a>Abrufen von Token
Welche Methode zum Tokenabruf verwendet wird, hängt normalerweise davon ab, ob es sich um eine öffentliche oder vertrauliche Clientanwendung handelt.

### <a name="public-client-applications"></a>Öffentliche Clientanwendungen
Die Vorgehensweise bei öffentlichen Clientanwendungen (Desktopanwendung oder mobile App):
- Häufig werden Token interaktiv abgerufen, indem sich der Benutzer über eine Benutzeroberfläche oder ein Popupfenster anmeldet.
- Über die integrierte Windows-Authentifizierung (IWA/Kerberos) können [Token automatisch für den angemeldeten Benutzer abgerufen werden](msal-authentication-flows.md#integrated-windows-authentication), wenn die Desktopanwendung auf einem Windows-Computer ausgeführt wird, der mit einer Domäne oder mit Azure verknüpft ist.
- In .NET Framework-Desktopclientanwendungen können [Token über einen Benutzernamen und ein Kennwort abgerufen werden](msal-authentication-flows.md#usernamepassword). Dies ist jedoch nicht empfehlenswert. Es wird davon abgeraten, Benutzernamen/Kennwörter in vertraulichen Clientanwendungen zu verwenden.
- In Anwendungen, die auf Geräten ohne Webbrowser ausgeführt werden, können Token über den [Gerätcodeflow](msal-authentication-flows.md#device-code) abgerufen werden. Der Benutzer erhält eine URL und einen Code, den er auf einem anderen Gerät in einen Webbrowser eingeben kann, um sich anzumelden.  Azure AD sendet daraufhin ein Token zurück an das browserlose Gerät.

### <a name="confidential-client-applications"></a>Vertrauliche Clientanwendungen 
Die Vorgehensweise bei vertraulichen Clientanwendungen (Web-App, Web-API oder Daemon-Anwendung wie ein Windows-Dienst):
- Token werden über den [Flow für Clientanmeldeinformationen](msal-authentication-flows.md#client-credentials) **für die Anwendung selbst** und nicht für einen Benutzer angefordert. Diese Methode kann für Synchronisierungstools oder für Tools verwendet werden, die Benutzervorgänge allgemein und nicht für einen bestimmten Benutzer verarbeiten. 
- Der [OBO-Flow (On Behalf Of)](msal-authentication-flows.md#on-behalf-of) wird für den Aufruf einer Web-API verwendet, die eine API im Namen des Benutzers aufruft. Die Anwendung wird anhand von Clientanmeldeinformationen identifiziert, um ein Token basierend auf einer Benutzerassertion (SAML oder ein JWT-Token) abzurufen. Dieser Flow wird von Anwendungen verwendet, die für Dienst-zu-Dienst-Aufrufe auf Ressourcen eines bestimmten Benutzers zugreifen müssen.
- Token werden in Web-Apps über den [Flow für Autorisierungscode](msal-authentication-flows.md#authorization-code) abgerufen, nachdem sich der Benutzer über die URL für Autorisierungsanforderungen angemeldet hat. OpenID Connect-Anwendungen verwenden in der Regel diesen Mechanismus, der die Benutzeranmeldung über Open ID Connect sowie den Zugriff auf Web-APIs im Namen des Benutzers zulässt.


## <a name="authentication-results"></a>Authentifizierungsergebnisse 
Wenn Ihr Client ein Zugriffstoken anfordert, gibt Azure AD auch ein Authentifizierungsergebnis zurück, das einige Metadaten zum Zugriffstoken enthält. Diese Informationen umfassen die Ablaufzeit eines Zugriffstokens und die Bereiche, für die es gilt. Die Daten ermöglichen Ihrer App das intelligente Zwischenspeichern von Zugriffstoken, ohne dass dabei das Zugriffstoken selbst analysiert werden muss.  Folgende Informationen werden im Authentifizierungsergebnis verfügbar gemacht:

- Das [Zugriffstoken](access-tokens.md), über das die Web-API auf Ressourcen zugreifen kann. Dies ist eine Zeichenfolge (meistens ein base64-codiertes JWT). Dem Client sollten jedoch niemals Einblicke in das Zugriffstoken gewährt werden. Das Format ist nicht zwingend stabil und kann für die Ressource verschlüsselt werden. Programmcode, der von Zugriffstokeninhalt auf dem Client abhängt, ist eine der häufigsten Ursachen für Fehler und Brüche in der Clientlogik.
- Das [ID-Token](id-tokens.md) für den Benutzer (ein JWT)
- Das Ablaufdatum des Tokens, das das Datum/die Uhrzeit für den Ablauf des Tokens enthält.
- Die Mandanten-ID enthält den Mandanten, in dem der Benutzer gefunden wurde. Bei Gastbenutzern (Azure AD-B2B-Szenarien) entspricht die Mandanten-ID dem Gastmandanten und nicht dem eindeutigen Mandanten. Wenn das Token im Namen eines Benutzers gesendet wird, enthält das Authentifizierungsergebnis auch Informationen über diesen Benutzer. Bei Flows für vertrauliche Clients, in denen Token ohne Benutzer (für die Anwendung) angefordert werden, wird für diese Benutzerinformationen NULL zurückgegeben.
- Die Geltungsbereiche, für die das Token ausgegeben wurde
- Die eindeutige ID für den Benutzer

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie mehr über [Behandeln von Fehlern und Ausnahmen](msal-handling-exceptions.md). 
