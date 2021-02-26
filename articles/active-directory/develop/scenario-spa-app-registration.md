---
title: Registrieren von Single-Page-Webanwendungen (Single-Page Applications, SPAs) | Azure
titleSuffix: Microsoft identity platform
description: Informieren Sie sich, wie Sie eine Single-Page-Webanwendung (App-Registrierung) erstellen.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/19/2020
ms.author: hahamil
ms.custom: aaddev
ms.openlocfilehash: 9366bb5b2bb5820245ec1b699bbf2ddda0dd9f9d
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103174"
---
# <a name="single-page-application-app-registration"></a>Single-Page-Webanwendung: App-Registrierung

Führen Sie die folgenden Schritte aus, um eine Single-Page-Webanwendung (Single-Page Application, SPA) auf der Microsoft Identity Platform zu registrieren. Es gelten unterschiedliche Registrierungsschritte für MSAL.js 1.0, die den Flow zur impliziten Genehmigung unterstützt, und MSAL.js 2.0, die den Autorisierungscodeflow mit PKCE unterstützt.

## <a name="create-the-app-registration"></a>Erstellen der App-Registrierung

Führen Sie für MSAL.js 1.0- und für MSAL.js 2.0-basierte Anwendungen zunächst die folgenden Schritte aus, um die App-Erstregistrierung zu erstellen.

1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an.
1. Wenn Sie Zugriff auf mehrere Mandanten haben, verwenden Sie im Menü am oberen Rand den Filter **Verzeichnis + Abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::, um den Mandanten auszuwählen, für den Sie eine Anwendung registrieren möchten.
1. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
1. Wählen Sie unter **Verwalten** Folgendes aus: **App-Registrierungen** > **Neue Registrierung**.
1. Geben Sie einen **Namen** für Ihre Anwendung ein. Benutzern Ihrer App wird wahrscheinlich dieser Namen angezeigt. Sie können ihn später ändern.
1. Wählen Sie **Unterstützte Kontotypen** für die Anwendung aus. Geben Sie **KEINEN** **Umleitungs-URI** ein. Eine Beschreibung der verschiedenen Kontotypen finden Sie unter [Registrieren einer Anwendung](quickstart-register-app.md).
1. Wählen Sie **Registrieren** aus, um die App-Registrierung zu erstellen.

Konfigurieren Sie als Nächstes die App-Registrierung mit einem **Umleitungs-URI**, um anzugeben, wohin die Microsoft Identity Platform den Client zusammen mit allen Sicherheitstoken umleiten soll. Führen Sie die Schritte aus, die für die in Ihrer Anwendung verwendete Version von MSAL.js gelten:

- [MSAL.js 2.0 mit dem Authentifizierungscodeflow](#redirect-uri-msaljs-20-with-auth-code-flow) (empfohlen)
- [MSAL.js 1.0 mit dem impliziten Flow](#redirect-uri-msaljs-10-with-implicit-flow)

## <a name="redirect-uri-msaljs-20-with-auth-code-flow"></a>Umleitungs-URI: [MSAL.js 2.0 mit dem Authentifizierungscodeflow](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)

Führen Sie die folgenden Schritte aus, um einen Umleitungs-URI für eine App hinzuzufügen, die MSAL.js 2.0 oder höher verwendet. MSAL.js 2.0 (und höher) unterstützt den Autorisierungscodeflow mit PKCE und CORS als Reaktion auf [Einschränkungen bei Browsern, die Cookies von Drittanbietern blockieren](reference-third-party-cookies-spas.md). Der Flow zur impliziten Genehmigung wird von MSAL.js 2.0 und höheren Versionen nicht unterstützt.

1. Wählen Sie im Azure-Portal die App-Registrierung aus, die Sie zuvor im Abschnitt [Erstellen der App-Registrierung](#create-the-app-registration) erstellt haben.
1. Wählen Sie unter **Verwalten** die Optionen **Authentifizierung** > **Plattform hinzufügen** aus.
1. Wählen Sie unter **Webanwendungen** die Kachel **Single-Page-Webanwendung** aus.
1. Geben Sie unter **Umleitungs-URIs** einen [Umleitungs-URI](reply-url.md) ein. Aktivieren Sie **KEINES** der beiden Kontrollkästchen unter **Implizite Genehmigung und hybride Flows**.
1. Wählen Sie **Konfigurieren** aus, um den Vorgang zum Hinzufügen des Umleitungs-URIs abzuschließen.

Sie haben jetzt die Registrierung Ihrer Single-Page-Webanwendung (Single-Page Application, SPA) abgeschlossen und einen Umleitungs-URI konfiguriert, an den der Client umgeleitet wird und alle Sicherheitstoken gesendet werden. Wenn Sie Ihren Umleitungs-URI über die Kachel **Single-Page-Webanwendung** im Bereich **Plattform hinzufügen** konfigurieren, wird Ihre Anwendungsregistrierung für die Unterstützung des Autorisierungscodeflows mit PKCE und CORS konfiguriert.

Befolgen Sie das [Tutorial](tutorial-v2-javascript-auth-code.md), um weitere Anleitungen zu erhalten.

## <a name="redirect-uri-msaljs-10-with-implicit-flow"></a>Umleitungs-URI: [MSAL.js 1.0 mit dem impliziten Flow](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)

Führen Sie die folgenden Schritte aus, um einen Umleitungs-URI für eine Single-Page-App hinzuzufügen, die MSAL.js 1.3 (oder eine frühere Version) und den Flow zur impliziten Genehmigung verwendet. Der Autorisierungscodeflow wird von Anwendungen, die MSAL.js 1.3 oder eine frühere Version verwenden, nicht unterstützt.

1. Wählen Sie im Azure-Portal die App-Registrierung aus, die Sie zuvor im Abschnitt [Erstellen der App-Registrierung](#create-the-app-registration) erstellt haben.
1. Wählen Sie unter **Verwalten** die Optionen **Authentifizierung** > **Plattform hinzufügen** aus.
1. Wählen Sie unter **Webanwendungen** die Kachel **Single-Page-Webanwendung** aus.
1. Geben Sie unter **Umleitungs-URIs** einen [Umleitungs-URI](reply-url.md) ein.
1. Aktivieren Sie die Option **Implizite Genehmigung und hybride Flows**:
    - Wenn Ihre Anwendung Benutzer anmeldet, wählen Sie **ID-Token** aus.
    - Wenn Ihre Anwendung auch eine geschützte Web-API aufrufen muss, wählen Sie **Zugriffstoken** aus. Weitere Informationen zu diesen Tokentypen finden Sie unter [ID-Token](id-tokens.md) und [Zugriffstoken](access-tokens.md).
1. Wählen Sie **Konfigurieren** aus, um den Vorgang zum Hinzufügen des Umleitungs-URIs abzuschließen.

Sie haben jetzt die Registrierung Ihrer Single-Page-Webanwendung (Single-Page Application, SPA) abgeschlossen und einen Umleitungs-URI konfiguriert, an den der Client umgeleitet wird und alle Sicherheitstoken gesendet werden. Wenn Sie einen oder beide Tokentypen (**ID-Token** und **Zugriffstoken**) auswählen, haben Sie den Flow zur impliziten Genehmigung aktiviert.

Befolgen Sie das [Tutorial](tutorial-v2-javascript-spa.md), um weitere Anleitungen zu erhalten.

## <a name="note-about-authorization-flows"></a>Hinweis zu Autorisierungsflows

Bei einer mithilfe der SPA-Plattformkonfiguration erstellten App-Registrierung wird der Autorisierungscodeflow standardmäßig aktiviert. Um diesen Flow nutzen zu können, muss Ihre Anwendung MSAL.js 2.0 oder höher verwenden.

Wie bereits erwähnt, sind Single-Page-Webanwendungen, die MSAL.js 1.3 verwenden, auf den Flow zur impliziten Genehmigung beschränkt. In den aktuellen [bewährten Methoden für OAuth 2.0](v2-oauth2-auth-code-flow.md) wird für SPAs die Verwendung des Autorisierungscodeflows anstelle des impliziten Flows empfohlen. Außerdem tragen Aktualisierungstoken mit begrenzter Lebensdauer zur Anpassung Ihrer Anwendung an die [in Bezug auf die Einschränkungen von Cookies geltenden Datenschutzbestimmungen moderner Browser](reference-third-party-cookies-spas.md) (z. B. an Safari ITP) bei.

Wenn alle von einer App-Registrierung dargestellten Single-Page-Webanwendungen in Ihrer Produktionsumgebung MSAL.js 2.0 und den Autorisierungscodeflow verwenden, deaktivieren Sie im Azure-Portal im Bereich **Authentifizierung** der App-Registrierung die Einstellungen für die implizite Genehmigung. Anwendungen, die MSAL.js 1.x und den impliziten Flow verwenden, funktionieren weiterhin, aber dazu muss der implizite Flow aktiviert bleiben.

## <a name="next-steps"></a>Nächste Schritte

Konfigurieren Sie den Code Ihrer App, um die in den vorherigen Schritten erstellte App-Registrierung zu verwenden: [Codekonfiguration der App](scenario-spa-app-configuration.md).
