---
title: Single-Page-Webanwendungen in Azure Active Directory
description: Beschreibt, worum es sich bei Web-API-Anwendungen handelt, und stellt die Grundlagen des Protokollflusses, der Registrierung und des Tokenablaufs für diesen App-Typ vor.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1f6f66779bec9ed4e38e5a662c2d3728ba2034b6
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545301"
---
# <a name="single-page-applications"></a>Single-Page-Webanwendungen

Single-Page-Webanwendungen (SPAs) sind typischerweise als JavaScript-Präsentationsschicht (Front-End) strukturiert, die im Browser ausgeführt wird, und als Web-API-Back-End, das auf einem Server ausgeführt wird und die Geschäftslogik der Anwendung implementiert. Um mehr über die implizite Autorisierung zu erfahren und Sie bei der Entscheidung zu unterstützen, ob sie für Ihr Anwendungsszenario geeignet ist, lesen Sie [Informationen zum Verständnis des impliziten OAuth2-Genehmigungsflusses in Azure Active Directory](v1-oauth2-implicit-grant-flow.md).

In diesem Szenario verwendet das JavaScript-Front-End bei der Benutzeranmeldung die [Active Directory Authentication Library für JavaScript (ADAL.JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js) und die implizite Gewährung der Autorisierung, um ein ID-Token (id_token) von Azure AD zu erhalten. Das Token wird zwischengespeichert und der Anforderung als Bearertoken angefügt, wenn der Client Aufrufe an das durch die OWIN-Middleware gesicherte Web-API-Back-End sendet.

## <a name="diagram"></a>Diagramm

![Abbildung: Single-Page-Webanwendung](./media/authentication-scenarios/single_page_app.png)

## <a name="protocol-flow"></a>Protokollfluss

1. Der Benutzer navigiert zu der Webanwendung.
1. Die Anwendung gibt das JavaScript-Front-End (Darstellungsschicht) an den Browser zurück.
1. Der Benutzer initiiert die Anmeldung (beispielsweise durch Klicken auf einen Anmeldelink). Der Browser sendet einen GET-Befehl an den Azure AD-Autorisierungsendpunkt, um ein ID-Token anzufordern. Diese Anforderung enthält die Anwendungs-ID und die Antwort-URL in den Abfrageparametern.
1. Azure AD überprüft die Antwort-URL anhand der registrierten Antwort-URL, die im Azure-Portal konfiguriert wurde.
1. Der Benutzer meldet sich auf der Anmeldeseite an.
1. Bei erfolgreicher Authentifizierung erstellt Azure AD ein ID-Token und gibt es als URL-Fragment (#) an die Antwort-URL der Anwendung zurück. Bei Produktionsanwendungen empfiehlt sich die Verwendung einer Antwort-URL mit HTTPS. Das zurückgegebene Token enthält Ansprüche für den Benutzer und Azure AD, die die Anwendung zum Überprüfen des Tokens benötigt.
1. Der im Browser ausgeführte JavaScript-Clientcode extrahiert das Token aus der Antwort und verwendet es zur Absicherung von Aufrufen an das Web-API-Back-End der Anwendung.
1. Der Browser ruft das Web-API-Back-End der Anwendung auf und übermittelt dabei das ID-Token im Autorisierungsheader. Der Azure AD-Authentifizierungsdienst stellt ein ID-Token aus, das als Bearertoken verwendet werden kann, wenn die Ressource die gleiche ist wie die Client-ID (was in diesem Fall stimmt, da die Web-API das eigene Back-End der App ist).

## <a name="code-samples"></a>Codebeispiele

Sehen Sie sich die [Codebeispiele für Szenarien vom Typ Single-Page-Webanwendung](sample-v1-code.md#single-page-applications) an. Schauen Sie außerdem regelmäßig vorbei: Es kommen immer wieder neue Beispiele hinzu.

## <a name="app-registration"></a>App-Registrierung

* Einzelinstanzenfähige Anwendung: Wenn Sie eine Anwendung nur für Ihre Organisation erstellen, muss diese über das Azure-Portal im Verzeichnis Ihres Unternehmens registriert werden.
* Mehrinstanzenfähige Anwendung: Wenn Sie eine Anwendung erstellen, die von Benutzern außerhalb Ihrer Organisation verwendet werden kann, muss diese ebenfalls im Verzeichnis Ihres Unternehmens registriert werden. Darüber hinaus muss sie aber auch im Verzeichnis jeder anderen Organisation registriert werden, von der die Anwendung verwendet wird. Um Ihre Anwendung in deren Verzeichnis verfügbar zu machen, können Sie einen Registrierungsprozess für Ihre Kunden einschließen, über den sie Ihrer Anwendung zustimmen können. Bei der Registrierung für Ihre Anwendung erscheint ein Dialogfeld mit den erforderlichen Berechtigungen für die Anwendung sowie mit einer Zustimmungsoption. Je nachdem, welche Berechtigungen erforderlich sind, wird unter Umständen die Zustimmung eines Administrators aus der anderen Organisation benötigt. Wenn der Benutzer oder Administrator seine Zustimmung gibt, wird die Anwendung in dessen Verzeichnis registriert.

Nachdem die Anwendung registriert wurde, muss sie für die Verwendung des impliziten OAuth 2.0-Genehmigungsprotokolls konfiguriert werden. Dieses Protokoll ist für Anwendungen standardmäßig deaktiviert. Um das implizite OAuth 2.0-Genehmigungsprotokoll für die Anwendung zu aktivieren, müssen Sie das Anwendungsmanifest aus dem Azure-Portal bearbeiten und den Wert „oauth2AllowImplicitFlow“ auf TRUE festlegen. Weitere Informationen finden Sie unter [Anwendungsmanifest](reference-app-manifest.md).

## <a name="token-expiration"></a>Tokenablauf

Die Verwendung von ADAL.js ist hilfreich für Folgendes:

* Aktualisieren eines abgelaufenen Tokens
* Anfordern eines Zugriffstokens zum Aufrufen einer Web-API-Ressource

Nach erfolgreicher Authentifizierung schreibt Azure AD ein Cookie in den Browser des Benutzers, um eine Sitzung einzurichten. Hinweis: Die Sitzung besteht zwischen dem Benutzer und Azure AD (nicht zwischen dem Benutzer und der Webanwendung). Wenn ein Token abläuft, ruft ADAL.js unter Verwendung dieser Sitzung automatisch ein weiteres Token ab. ADAL.js verwendet ein verborgenes iFrame, um die Anforderung unter Verwendung des impliziten OAuth-Genehmigungsprotokolls zu senden und zu empfangen. Mit dem gleichen Mechanismus kann ADAL.js automatisch Zugriffstoken für andere Web-API-Ressourcen abrufen, die von der Anwendung aufgerufen werden. Dafür müssen die Ressourcen CORS (Cross-Origin Resource Sharing) unterstützen, im Verzeichnis des Benutzers registriert sein und bei der Anmeldung die erforderlichen Zustimmungen des Benutzers erhalten haben.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu anderen [Anwendungstypen und -szenarien](app-types.md)
* Weitere Informationen zu den [Authentifizierungsgrundlagen](authentication-scenarios.md) von Azure AD
