---
title: Abrufen und Zwischenspeichern von Token mithilfe der Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL) | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie etwas über das Abrufen und Zwischenspeichern von Token mit MSAL.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/04/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: da79a74121318993f807ec3bde101b652a8b49da
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/14/2020
ms.locfileid: "94628134"
---
# <a name="acquire-and-cache-tokens-using-the-microsoft-authentication-library-msal"></a>Abrufen und Zwischenspeichern von Token mithilfe der Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL)

[Zugriffstoken](access-tokens.md) ermöglichen Clients das sichere Aufrufen von Web-APIs, die durch Azure geschützt sind. Es gibt verschiedene Möglichkeiten zum Abrufen von Token mithilfe der Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL). Einige erfordern eine Benutzerinteraktion über einen Webbrowser, während bei anderen keine Benutzerinteraktionen erforderlich sind. Die Methode zum Anfordern eines Tokens ist davon abhängig, ob es sich bei der Anwendung um eine öffentliche Clientanwendung (Desktopanwendung oder mobile App) oder um eine vertrauliche Clientanwendung (Web-App, Web-API oder Daemonanwendung) handelt.

Nachdem ein Token abgerufen wurde, wird es von MSAL zwischengespeichert. Ihr Anwendungscode sollte zunächst versuchen, ein Token ohne Eingriff aus dem Cache abzurufen, bevor andere Methoden angewandt werden.

Sie können den Inhalt des Tokencaches auch löschen, indem Sie die Konten aus dem Cache entfernen. Das Sitzungscookie im Browser wird dabei jedoch nicht entfernt.

## <a name="scopes-when-acquiring-tokens"></a>Geltungsbereiche beim Abrufen von Token

[Geltungsbereiche](v2-permissions-and-consent.md) sind die Berechtigungen, die von einer Web-API verfügbar gemacht werden und auf die Clientanwendungen Zugriff anfordern können. Clientanwendungen fordern die Benutzereinwilligung für diese Geltungsbereiche an, wenn sie Authentifizierungsanforderungen zum Tokenabruf senden, um auf die Web-APIs zuzugreifen. Über MSAL können Sie Token für den Zugriff auf APIs für Azure AD für Entwickler (v1.0) und Microsoft Identity Platform (v2.0) abrufen. Das v2.0-Protokoll verwendet in den Anforderungen Geltungsbereiche anstelle von Ressourcen. Weitere Informationen finden Sie unter [Vergleich von v1.0 und v2.0](../azuread-dev/azure-ad-endpoint-comparison.md). Abhängig von der Web-API-Konfiguration der akzeptierten Tokenversion gibt der v2.0-Endpunkt das Zugriffstoken an MSAL zurück.

Einige der Tokenabrufmethoden von MSAL erfordern den Parameter `scopes`. Bei dem Parameter `scopes` handelt es sich um eine Liste von Zeichenfolgen, die die gewünschten Berechtigungen und die angeforderten Ressourcen deklarieren. Bekannte Geltungsbereiche sind die [Microsoft Graph-Berechtigungen](/graph/permissions-reference).

In MSAL kann auch auf v1.0-Ressourcen zugegriffen werden. Weitere Informationen finden Sie unter [Geltungsbereiche für eine v1.0-Anwendung](msal-v1-app-scopes.md).

### <a name="request-scopes-for-a-web-api"></a>Anfordern von Bereichen für eine Web-API

Wenn Ihre Anwendung Token mit bestimmten Berechtigungen für eine Ressourcen-API anfordern muss, übergeben Sie die Geltungsbereiche mit dem App-ID-URI der API im Format `<app ID URI>/<scope>`.

Einige Beispielwerte für Geltungsbereiche für verschiedene Ressourcen:

- Microsoft Graph-API: `https://graph.microsoft.com/User.Read`
- Benutzerdefinierte Web-API: `api://11111111-1111-1111-1111-111111111111/api.read`

Das Format des Bereichswerts variiert abhängig von der Ressource (API), die das Zugriffstoken empfängt, und den `aud`-Anspruchswerten, die sie akzeptiert.

Nur für Microsoft Graph wird der Bereich `user.read` `https://graph.microsoft.com/User.Read` zugeordnet, und beide Bereichsformate können austauschbar verwendet werden.

Für bestimmte Web-APIs wie die Azure Resource Manager API (https://management.core.windows.net/) ) wird ein nachgestellter Schrägstrich („/“) im Zielgruppenanspruch (`aud` ) des Zugriffstokens erwartet. Übergeben Sie in diesem Fall den Bereich als `https://management.core.windows.net//user_impersonation`, einschließlich des doppelten Schrägstrichs („//“).

Bei anderen APIs darf im Bereichswert möglicherweise *kein Schema oder Host* enthalten sind, und es werden nur die App-ID (GUID) und der Bereichsname erwartet, z. B.:

`11111111-1111-1111-1111-111111111111/api.read`

> [!TIP]
> Wenn sich die Downstreamressource nicht in Ihrer Kontrolle befindet, müssen Sie möglicherweise verschiedene Bereichswertformate ausprobieren (z. B. mit/ohne Schema und Host), wenn Sie beim Übergeben des Zugriffstokens an die Ressource `401` oder andere Fehler erhalten.

### <a name="request-dynamic-scopes-for-incremental-consent"></a>Anfordern dynamischer Geltungsbereiche für inkrementelle Einwilligungen

Wenn die von Ihrer Anwendung bereitgestellten Features oder ihre Anforderungen geändert werden, können Sie bei Bedarf mithilfe des Bereichsparameters zusätzliche Berechtigungen anfordern. Diese *dynamischen Bereiche* ermöglichen Ihren Benutzern, ihre inkrementelle Einwilligung für Bereiche anzugeben.

Beispielsweise können Sie den Benutzer anmelden, ihm anfänglich jedoch den Zugriff auf alle Ressourcen verweigern. Später können Sie ihm dann die Möglichkeit einräumen, seinen Kalender anzuzeigen, indem der Kalenderbereich in der Tokenabrufmethode angefordert und die Benutzereinwilligung eingeholt werden. Dies kann z. B. durch Anfordern der Bereiche `https://graph.microsoft.com/User.Read` und `https://graph.microsoft.com/Calendar.Read` erfolgen.

## <a name="acquiring-tokens-silently-from-the-cache"></a>Automatisches Abrufen von Token (aus dem Cache)

MSAL verwaltet einen Tokencache (bzw. zwei Caches im Fall von vertraulichen Clientanwendungen) und speichert Token nach dem Abruf zwischen. Beim automatischen Tokenabruf wird in vielen Fällen ein weiteres Token mit weiteren Geltungsbereichen abgerufen, die auf einem Token im Cache basieren. Auch die Aktualisierung von Token, die demnächst ablaufen, ist möglich (da der Tokencache auch ein Aktualisierungstoken enthält).

### <a name="recommended-call-pattern-for-public-client-applications"></a>Empfohlene Aufrufmuster für öffentliche Clientanwendungen

Der Anwendungscode sollte zunächst versuchen, ein Token automatisch aus dem Cache abzurufen. Wenn der Methodenaufruf einen Fehler oder eine Ausnahme vom Typ „UI erforderlich“ zurückgibt, sollten Sie andere Methoden für den Tokenabruf probieren.

Bei zwei Flows sollten Sie Token jedoch **nicht** automatisch abrufen:

- [Flow für Clientanmeldeinformationen](msal-authentication-flows.md#client-credentials), für den anstelle des Benutzertokencaches ein Anwendungstokencache verwendet wird. Bei dieser Methode wird der Anwendungstokencache überprüft, bevor eine Anforderung an den Sicherheitstokendienst (STS) gesendet wird.
- [Flow für Autorisierungscode](msal-authentication-flows.md#authorization-code) in Web-Apps. In diesem Fall wird der von der Anwendung abgerufene Code eingelöst, indem der Benutzer angemeldet und seine Einwilligung für weitere Geltungsbereiche eingeholt wird. Da ein Code und nicht ein Konto als Parameter übergeben wird, sind bei dieser Methode keine Einblicke in den Cache vor dem Einlösen des Codes möglich, da hierbei der Dienst aufgerufen wird.

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
- In .NET Framework-Desktopclientanwendungen können [Token über einen Benutzernamen und ein Kennwort abgerufen werden](msal-authentication-flows.md#usernamepassword). Dies wird jedoch nicht empfohlen. Es wird davon abgeraten, Benutzernamen/Kennwörter in vertraulichen Clientanwendungen zu verwenden.
- In Anwendungen, die auf Geräten ohne Webbrowser ausgeführt werden, können Token über den [Gerätecodeflow](msal-authentication-flows.md#device-code) abgerufen werden. Der Benutzer erhält eine URL und einen Code, den er auf einem anderen Gerät in einen Webbrowser eingeben kann, um sich anzumelden. Azure AD sendet daraufhin ein Token zurück an das browserlose Gerät.

### <a name="confidential-client-applications"></a>Vertrauliche Clientanwendungen

Vorgehensweise bei vertraulichen Clientanwendungen (Web-App, Web-API oder Daemonanwendung wie einem Windows-Dienst):

- Token werden über den [Flow für Clientanmeldeinformationen](msal-authentication-flows.md#client-credentials)**für die Anwendung selbst** und nicht für einen Benutzer angefordert. Diese Technik kann für Synchronisierungstools verwendet werden oder für Tools, die Benutzer allgemein und nicht als bestimmte Benutzer verarbeiten.
- Der [On-Behalf-Of-Flow](msal-authentication-flows.md#on-behalf-of) wird für den Aufruf einer Web-API verwendet, die eine API im Namen des Benutzers aufruft. Die Anwendung wird anhand von Clientanmeldeinformationen identifiziert, um ein Token basierend auf einer Benutzerassertion (z. B. SAML oder ein JWT-Token) abzurufen. Dieser Flow wird von Anwendungen verwendet, die für Dienst-zu-Dienst-Aufrufe auf Ressourcen eines bestimmten Benutzers zugreifen müssen.
- Token werden in Web-Apps über den [Flow für Autorisierungscode](msal-authentication-flows.md#authorization-code) abgerufen, nachdem sich der Benutzer über die URL für Autorisierungsanforderungen angemeldet hat. OpenID Connect-Anwendungen verwenden in der Regel diesen Mechanismus, der die Benutzeranmeldung über Open ID Connect sowie den Zugriff auf Web-APIs im Namen des Benutzers zulässt.

## <a name="authentication-results"></a>Authentifizierungsergebnisse

Wenn Ihr Client ein Zugriffstoken anfordert, gibt Azure AD auch ein Authentifizierungsergebnis zurück, das Metadaten zum Zugriffstoken enthält. Diese Informationen umfassen die Ablaufzeit eines Zugriffstokens und die Bereiche, für die es gilt. Die Daten ermöglichen Ihrer App das intelligente Zwischenspeichern von Zugriffstoken, ohne dass dabei das Zugriffstoken selbst analysiert werden muss. Folgende Informationen werden im Authentifizierungsergebnis verfügbar gemacht:

- Das [Zugriffstoken](access-tokens.md), über das die Web-API auf Ressourcen zugreifen kann. Diese Zeichenfolge ist meist ein Base64-codiertes JWT, dem Client sollten jedoch niemals Einblicke in das Zugriffstoken gewährt werden. Die Stabilität des Formats ist nicht garantiert, und das Format kann für die Ressource verschlüsselt werden. Programmcode, der vom Inhalt von Zugriffstoken auf dem Client abhängig ist, stellt eine der häufigsten Ursachen für Fehler und Brüche in der Clientlogik dar.
- Das [ID-Token](id-tokens.md) für den Benutzer (ein JWT).
- Das Ablaufdatum des Tokens, das das Datum/die Uhrzeit für den Ablauf des Tokens enthält.
- Die Mandanten-ID enthält den Mandanten, in dem der Benutzer gefunden wurde. Bei Gastbenutzern (Azure AD-B2B-Szenarien) entspricht die Mandanten-ID dem Gastmandanten und nicht dem eindeutigen Mandanten. Wenn das Token im Namen eines Benutzers gesendet wird, enthält das Authentifizierungsergebnis auch Informationen über diesen Benutzer. Bei Flows für vertrauliche Clients, in denen Token ohne Benutzer (für die Anwendung) angefordert werden, wird für diese Benutzerinformationen NULL zurückgegeben.
- Die Geltungsbereiche, für die das Token ausgegeben wurde
- Die eindeutige ID für den Benutzer

## <a name="advanced-accessing-the-users-cached-tokens-in-background-apps-and-services"></a>(Erweitert) Zugreifen auf die zwischengespeicherten Token des Benutzers in Hintergrund-Apps und Diensten

[!INCLUDE [advanced-token-caching](../../../includes/advanced-token-cache.md)]

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie MSAL für Java verwenden, informieren Sie sich über die [benutzerdefinierte Tokencacheserialisierung in MSAL für Java](msal-java-token-cache-serialization.md).

Erfahren Sie mehr über [Behandeln von Fehlern und Ausnahmen](msal-handling-exceptions.md).