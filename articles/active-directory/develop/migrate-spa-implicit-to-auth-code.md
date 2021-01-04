---
title: Migrieren von JavaScript-Single-Page-Webanwendungen vom impliziten Genehmigungsflow zum Autorisierungscodeflow | Azure
titleSuffix: Microsoft identity platform
description: Hier erfahren Sie, wie Sie eine JavaScript-Single Page App (SPA), die MSAL.js 1.x und den impliziten Genehmigungsflow verwendet, zu MSAL.js 2.x und dem Autorisierungscodeflow mit PKCE- und CORS-Unterstützung migrieren.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/17/2020
ms.author: hahamil
ms.custom: aaddev, devx-track-js
ms.openlocfilehash: 05258e201c65138e53e861f0631eb33e08c9c199
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92673598"
---
# <a name="migrate-a-javascript-single-page-app-from-implicit-grant-to-auth-code-flow"></a>Migrieren von JavaScript-Single-Page-Webanwendungen vom impliziten Genehmigungsflow zum Autorisierungscodeflow

Mit Version 2.0 von Microsoft Authentication Library for JavaScript (MSAL.js) wird die Unterstützung des Autorisierungscodeflows mit PKCE und CORS für Single-Page-Webanwendungen auf der Microsoft Identity-Plattform eingeführt. Führen Sie die Schritte in den folgenden Abschnitten aus, um Ihre MSAL.js 1.x-Anwendung mit impliziter Genehmigung zu MSAL.js 2.0 und höher (im folgenden *2.x* genannt) und dem Autorisierungscodeflow zu migrieren.

MSAL.js 2.x bringt Verbesserungen gegenüber MSAL.js 1.x mit sich und unterstützt jetzt anstelle des impliziten Genehmigungsflows den Autorisierungscodeflow. MSAL.js 2.x unterstützt den impliziten Genehmigungsflow **NICHT** .

## <a name="migration-steps"></a>Schritte bei der Migration

Das Aktualisieren der Anwendung auf MSAL.js 2.x und den Autorisierungscodeflow gliedert sich in drei Hauptschritte:

1. Ändern Sie bei Ihre(n) Umleitungs-URI(s) der [App-Registrierung](#switch-redirect-uris-to-spa-platform) die Plattform **Web** in die Plattform **Single-Page-Webanwendung** .
1. Aktualisieren Sie Ihren [Code](#switch-redirect-uris-to-spa-platform) von MSAL.js 1.x auf **2.x** .
1. Deaktivieren Sie die [implizite Genehmigung](#disable-implicit-grant-settings) in Ihrer App-Registrierung, wenn alle Anwendungen, die die Registrierung gemeinsam nutzen, auf MSAL.js 2.x und den Autorisierungscodeflow aktualisiert wurden.

In den folgenden Abschnitten wird jeder Schritt ausführlich beschrieben.

## <a name="switch-redirect-uris-to-spa-platform"></a>Ändern der Umleitungs-URIs für die SPA-Plattform

Wenn Sie die vorhandene App-Registrierung für Ihre Anwendungen weiter verwenden möchten, aktualisieren Sie im Azure-Portal die Umleitungs-URIs der Registrierung auf die SPA-Plattform. Dadurch wird der Autorisierungscodeflow mit PKCE- und CORS-Unterstützung für die Apps aktiviert, die diese Registrierung verwenden (den Code Ihrer Anwendung müssen Sie noch auf MSAL.js v2.x aktualisieren).

Führen Sie für App-Registrierungen, die derzeit mit Umleitungs URIs für die Plattform **Web** konfiguriert sind, die folgenden Schritte aus:

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com) an, und wählen Sie **Azure Active Directory** aus.
1. Wählen Sie unter **App-Registrierungen** Ihre Anwendung aus, und wählen Sie dann **Authentifizierung** aus.
1. Wählen Sie auf der Kachel für die Plattform **Web** unter **Umleitungs-URIs** das Warnbanner aus, das angibt, dass Sie Ihre URIs migrieren sollten.

    :::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-01-implicit-warning-banner.png" alt-text="Warnbanner für den impliziten Genehmigungsflow auf der Web-App-Kachel im Azure-Portal":::
1. Wählen Sie *nur* die Umleitungs-URIs für die Anwendungen aus, die MSAL.js 2.x verwenden sollen, und wählen Sie dann **Konfigurieren** aus.

    :::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-02-select-redirect-uri.png" alt-text="Warnbanner für den impliziten Genehmigungsflow auf der Web-App-Kachel im Azure-Portal":::

Diese Umleitungs-URIs sollten jetzt auf der Kachel für die Plattform **Single-Page-Webanwendung** angezeigt werden. Ebenfalls wird angezeigt, dass CORS-Unterstützung mit dem Autorisierungscodeflow und PKCE für diese URIs aktiviert ist.

:::image type="content" source="media/migrate-spa-implicit-to-auth-code/portal-03-spa-redirect-uri-tile.png" alt-text="Warnbanner für den impliziten Genehmigungsflow auf der Web-App-Kachel im Azure-Portal":::

Sie können auch [eine neue App-Registrierung erstellen](scenario-spa-app-registration.md), anstatt die Umleitungs-URIs in der vorhandenen Registrierung zu aktualisieren.

## <a name="update-your-code-to-msaljs-2x"></a>Aktualisieren des Codes auf MSAL.js 2.x

In MSAL 1.x haben Sie eine Anwendungsinstanz erstellt, indem Sie wie folgt eine [UserAgentApplication][msal-js-useragentapplication] initialisiert haben:

```javascript
// MSAL 1.x
import * as msal from "msal";

const msalInstance = new msal.UserAgentApplication(config);
```

In MSAL 2.x initialisieren Sie stattdessen eine [PublicClientApplication][msal-js-publicclientapplication]:

```javascript
// MSAL 2.x
import * as msal from "@azure/msal-browser";

const msalInstance = new msal.PublicClientApplication(config);
```

Eine vollständige exemplarische Vorgehensweise zum Hinzufügen von MSAL 2.x zu Ihrer Anwendung finden Sie im [Tutorial: Anmelden von Benutzern und Aufrufen der Microsoft Graph-API aus einer JavaScript-Single-Page-Webanwendung (SPA) mithilfe des Autorisierungscodeflows](tutorial-v2-javascript-auth-code.md).

Weitere Änderungen, die Sie möglicherweise an Ihrem Code vornehmen müssen, finden Sie im [Handbuch zur Migration](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/v1-migration.md) auf GitHub.

## <a name="disable-implicit-grant-settings"></a>Deaktivieren der Einstellungen für die implizite Genehmigung

Wenn Sie alle Produktionsanwendungen, die diese App-Registrierung verwenden, und die zugehörige Client-ID auf MSAL 2.x und den Autorisierungscodeflow aktualisiert haben, sollten Sie die Einstellungen für die implizite Genehmigung in der App-Registrierung deaktivieren.

Wenn Sie die Einstellungen für die implizite Genehmigung in der App-Registrierung deaktivieren, wird der implizite Genehmigungsflow für alle Anwendungen, die die Registrierung verwenden, und die zugehörige Client-ID deaktiviert.

Deaktivieren Sie den impliziten Genehmigungsflow **nicht** , bevor Sie alle Anwendungen auf MSAL.js 2.x und [PublicClientApplication][msal-js-publicclientapplication] aktualisiert haben.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Authentifizierungscodefluss, einschließlich der Unterschiede zwischen den impliziten und Autorisierungscodeflüssen finden Sie unter [Microsoft Identity Platform und der OAuth 2.0-Autorisierungscodefluss](v2-oauth2-auth-code-flow.md).

Wenn Sie sich eingehender mit der Entwicklung von Single-Page-Webanwendungen auf der Microsoft Identity Platform beschäftigen möchten, kann Ihnen die mehrteilige Artikelreihe [Szenario: Single-Page-Webanwendung](scenario-spa-overview.md) den Einstieg erleichtern.

<!-- LINKS - external -->
[msal-js-useragentapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/modules/_useragentapplication_.html
[msal-js-publicclientapplication]: https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-browser/classes/_src_app_publicclientapplication_.publicclientapplication.html
