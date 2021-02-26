---
title: Unterschiede zwischen MSAL.js und ADAL.js | Azure
titleSuffix: Microsoft identity platform
description: Erfahren Sie mehr über die Unterschiede zwischen der Microsoft Authentication Library für JavaScript (MSAL.js) und der Azure AD-Authentifizierungsbibliothek für JavaScript (ADAL.js) und über die Auswahl des jeweils zu verwendenden Tools.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/10/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1a56685c830fc7aa717add3e826c68c04449e378
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99580845"
---
# <a name="differences-between-msaljs-and-adaljs"></a>Unterschiede zwischen MSAL.js und ADAL.js

Sowohl die Microsoft Authentication Library für JavaScript (MSAL.js) als auch die Azure AD-Authentifizierungsbibliothek für JavaScript (ADAL.js) werden zum Authentifizieren von Azure AD-Entitäten und zum Anfordern von Token von Azure AD verwendet. Bisher haben die meisten Entwickler mit Azure AD für Entwickler (v1.0) gearbeitet, um Azure AD-Identitäten (Geschäfts-, Schul- und Unikonten) zu authentifizieren, indem sie Token mithilfe von ADAL anfordern. Jetzt können Sie mithilfe von MSAL.js eine größere Gruppe von Microsoft-Identitäten (Azure AD-Identitäten und Microsoft-Konten sowie Social Media- und lokale Konten über Azure AD B2C) über Microsoft Identity Platform authentifizieren.

Dieser Artikel bietet Hilfestellung bei der Auswahl zwischen der Microsoft Authentication Library für JavaScript (MSAL.js) und der Azure AD-Authentifizierungsbibliothek für JavaScript (ADAL.js) sowie eine Gegenüberstellung der beiden Bibliotheken.

## <a name="choosing-between-adaljs-and-msaljs"></a>Auswählen zwischen ADAL.js und MSAL.js

In den meisten Fällen möchten Sie wahrscheinlich die Microsoft Identity Platform und MSAL.js verwenden, da es sich hierbei um die neueste Generation von Microsoft-Authentifizierungsbibliotheken handelt. Mithilfe von MSAL.js rufen Sie Token für Benutzer ab, die sich mit Azure AD (Geschäfts-, Schul- und Unikonten), (persönlichen) Microsoft-Konten (MSA) oder Azure AD B2C bei Ihrer Anwendung anmelden.

Wenn Sie bereits mit dem v1.0-Endpunkt (und ADAL.js) vertraut sind, könnte Sie der Artikel [Neuerungen des v2.0-Endpunkts](../azuread-dev/azure-ad-endpoint-comparison.md) interessieren.

Wenn Benutzer mit früheren Versionen der [Active Directory-Verbunddienste (AD FS)](/windows-server/identity/active-directory-federation-services) bei Ihrer Anwendung angemeldet werden müssen, müssen Sie jedoch weiterhin ADAL.js verwenden.

## <a name="key-differences-in-authentication-with-msaljs"></a>Wichtige Unterschiede bei der Authentifizierung mit MSAL.js

### <a name="core-api"></a>Core-API

* ADAL.js verwendet [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-js/wiki/Config-authentication-context#authenticationcontext) als Darstellung einer Instanz der Verbindung Ihrer Anwendung mit dem Autorisierungsserver oder Identitätsanbieter über eine Autoritäts-URL. Im Gegensatz dazu ist die MSAL.js-API um die Benutzer-Agent-Clientanwendung herum konzipiert (eine Form der öffentlichen Clientanwendung, bei der der Clientcode in einem Benutzer-Agent wie beispielsweise einem Webbrowser ausgeführt wird). Sie stellt die `UserAgentApplication`-Klasse zum Darstellen einer Instanz des Authentifizierungskontexts der Anwendung mit dem Autorisierungsserver bereit. Weitere Informationen finden Sie unter [Initialisieren von Clientanwendungen mithilfe von MSAL.js](msal-js-initializing-client-applications.md).

* In ADAL.js sind die Methoden zum Abrufen von Token mit einem einzigen Autoritätssatz im `AuthenticationContext` verknüpft. In MSAL.js können die Anforderungen zum Abrufen von Token andere als die in der `UserAgentApplication`-Klasse festgelegten Autoritätswerte aufweisen. Dadurch kann MSAL.js Token separat für mehrere Mandanten und Benutzerkonten in derselben Anwendung abrufen und zwischenspeichern.

* In ADAL.js heißt die Methode zum automatischen Abrufen und Erneuern von Token ohne Benutzereingabeaufforderung `acquireToken`. In MSAL.js trägt diese Methode den Namen `acquireTokenSilent`, mit dem diese Funktion besser beschrieben wird.

### <a name="authority-value-common"></a>Autoritätswert `common`

Wenn in v1.0 die Autorität `https://login.microsoftonline.com/common` verwendet wird, können sich Benutzer mit jedem Azure AD-Konto (für jede Organisation) anmelden.

Wenn in v2.0 die Autorität `https://login.microsoftonline.com/common` verwendet wird, können sich Benutzer mit jedem Azure AD-Organisationskonto oder einem persönlichen Microsoft-Konto (MSA) anmelden. Verwenden Sie `https://login.microsoftonline.com/organizations`, um die Anmeldung nur auf Azure AD-Konten (gleiches Verhalten wie bei ADAL.js) zu beschränken. Weitere Informationen finden Sie im Artikel [Initialisieren von Clientanwendungen mithilfe von MSAL.js](msal-js-initializing-client-applications.md) unter der Konfigurationsoption `authority`.

### <a name="scopes-for-acquiring-tokens"></a>Geltungsbereiche für das Abrufen von Token
* Verwendung von Geltungsbereichen anstelle von Ressourcenparametern in Authentifizierungsanforderungen zum Abrufen von Token

    Das v2.0-Protokoll verwendet in den Anforderungen Geltungsbereiche anstelle von Ressourcen. Das heißt, wenn Ihre Anwendung Token mit Berechtigungen für eine Ressource wie Microsoft Graph anfordern muss, unterscheiden sich die an die Bibliotheksmethoden übergebenen Werte wie folgt:

    v1.0: Ressource = https\://graph.microsoft.com

    v2.0: Bereich = https\://graph.microsoft.com/User.Read

    Sie können Geltungsbereiche für jede beliebige Ressourcen-API mit dem URI der API im Format „App-ID-URI/Geltungsbereich anfordern. Beispiel: „https:\//mytenant.onmicrosoft.com/myapi/api.read“

    Nur bei der Microsoft Graph-API ist der Geltungsbereichswert `user.read` dem Format „https:\//graph.microsoft.com/User.Read“ zugeordnet und somit austauschbar.

    ```javascript
    var request = {
        scopes = ["User.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Dynamische Geltungsbereiche für inkrementelle Einwilligungen

    Beim Erstellen von Anwendungen mit v1.0 mussten Sie den vollständigen Satz der von der Anwendung benötigten Berechtigungen (statische Geltungsbereiche) registrieren, damit bei der Anmeldung die Benutzereinwilligung eingeholt werden konnte. In v2.0 können Sie den Parameter „scope“ verwenden, um die Berechtigungen zum gewünschten Zeitpunkt anzufordern. Diese werden als dynamische Geltungsbereiche bezeichnet. Dadurch kann der Benutzer Geltungsbereichen inkrementelle Einwilligungen erteilen. Wenn Sie also am Anfang nur möchten, dass sich der Benutzer bei Ihrer Anwendung anmeldet und Sie keinen Zugriff benötigen, können Sie dies tun. Wenn Sie später in der Lage sein müssen, den Kalender des Benutzers zu lesen, können Sie dann den Kalendergeltungsbereich in den Tokenabrufmethoden („acquireToken“) anfordern und die Einwilligung des Benutzers einholen. Beispiel:

    ```javascript
    var request = {
        scopes = ["https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Calendar.Read"];
    };

    acquireTokenPopup(request);   
    ```

* Geltungsbereiche für V1. 0-APIs

    Beim Abrufen von Token für V1. 0-APIs mit MSAL.js können Sie alle für die API registrierten statischen Geltungsbereiche anfordern, indem Sie `.default` als Geltungsbereich an den App-ID-URI der API anfügen. Beispiel:

    ```javascript
    var request = {
        scopes = [ appidURI + "/.default"];
    };

    acquireTokenPopup(request);
    ```

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie unter [Vergleich von v1.0 und v2.0](../azuread-dev/azure-ad-endpoint-comparison.md).
