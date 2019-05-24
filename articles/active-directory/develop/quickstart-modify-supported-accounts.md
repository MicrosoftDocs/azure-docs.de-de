---
title: Ändern der Konten, die von einer bei der Microsoft Identity Platform registrierten Anwendung unterstützt werden | Azure
description: Konfigurieren Sie eine bei der Microsoft Identity Platform registrierte Anwendung, um zu ändern, welche Benutzer oder Konten auf die Anwendung zugreifen können.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00d99ae48abfcb3e4d4abc0d63565994df45f746
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545725"
---
# <a name="quickstart-modify-the-accounts-supported-by-an-application"></a>Schnellstart: Ändern der von einer Anwendung unterstützten Konten

Wenn Sie eine Anwendung bei der Microsoft Identity Platform registrieren, können Sie beispielsweise festlegen, dass nur Benutzer aus Ihrer Organisation auf Ihre Anwendung zugreifen dürfen. Alternativ können Sie auch festlegen, dass Ihre App für Benutzer aus externen Organisationen (oder für Benutzer aus externen Organisationen und für Benutzer mit persönlichen Konten, die möglicherweise keiner Organisation angehören) zugänglich sein soll.

In dieser Schnellstartanleitung erfahren Sie, wie Sie die Konfiguration Ihrer Anwendung bearbeiten, um zu ändern, welche Benutzer oder Konten auf die Anwendung zugreifen können.

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie zunächst sicher, dass die folgenden Voraussetzungen erfüllt sind:

* Sie sind über die Unterstützung von [Berechtigungen und Zustimmung](v2-permissions-and-consent.md) informiert. Hiermit sollten Sie vertraut sein, wenn Sie Anwendungen erstellen, die von anderen Benutzern oder Anwendungen verwendet werden müssen.
* Sie verfügen über einen Mandanten, unter dem Anwendungen registriert wurden.
  * Wenn Sie keine Apps registriert haben, sollten Sie sich darüber informieren, [wie Sie Anwendungen bei der Microsoft Identity Platform registrieren](quickstart-register-app.md).

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Anmelden beim Azure-Portal und Auswählen der App

Sie müssen die folgenden Schritte ausführen, bevor Sie die App konfigurieren können:

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wenn Sie mit Ihrem Konto auf mehrere Mandanten zugreifen können, klicken Sie rechts oben auf Ihr Konto, und legen Sie Ihre Portalsitzung auf den gewünschten Azure AD-Mandanten fest.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** und anschließend **App-Registrierungen** aus.
1. Wählen Sie die Anwendung aus, die Sie konfigurieren möchten. Nachdem Sie die App ausgewählt haben, wird die anwendungsspezifische Seite **Übersicht** oder die Hauptseite für die Registrierung angezeigt.
1. Führen Sie die Schritte unter [Unterstützen anderer Konten durch Ändern der Anwendungsregistrierung](#change-the-application-registration-to-support-different-accounts) aus.
1. Bei Verwendung einer Single-Page-Webanwendung müssen Sie [die implizite OAuth 2.0-Genehmigung aktivieren](#enable-oauth-20-implicit-grant-for-single-page-applications).

## <a name="change-the-application-registration-to-support-different-accounts"></a>Unterstützen anderer Konten durch Ändern der Anwendungsregistrierung

Wenn Sie eine Anwendung schreiben, die Sie Ihren Kunden oder Partnern außerhalb Ihrer Organisation zur Verfügung stellen möchten, müssen Sie die Anwendungsdefinition im Azure-Portal aktualisieren.

> [!IMPORTANT]
> Für Azure AD muss der App-ID-URI von mehrinstanzenfähigen Anwendungen global eindeutig sein. Der App-ID-URI ist eine der Methoden, mit der eine Anwendung in Protokollmeldungen identifiziert wird. Bei einer Anwendung mit einem einzelnen Mandanten muss der App-ID-URI nur in diesem Mandanten eindeutig sein. Bei einer mehrinstanzenfähigen Anwendung muss er global eindeutig sein, damit Azure AD die Anwendung in allen Mandanten finden kann. Globale Eindeutigkeit wird durch die Anforderung erzwungen, dass der App-ID-URI einen Hostnamen aufweisen muss, der mit einer überprüften Domäne des Azure AD-Mandanten übereinstimmt. Wenn der Name Ihres Mandanten also beispielsweise „contoso.onmicrosoft.com“ lautet, wäre https://contoso.onmicrosoft.com/myapp ein gültiger App-ID-URI. Wenn Ihr Mandant eine verifizierte Domäne von „contoso.com“ hat, wäre auch https://contoso.com/myapp ein gültiger App-ID-URI. Wenn der App-ID-URI nicht diesem Muster folgt, schlägt das Festlegen einer Anwendung als mehrinstanzenfähig fehl.

### <a name="to-change-who-can-access-your-application"></a>So ändern Sie, wer auf Ihre Anwendung zugreifen kann

1. Ändern Sie auf der Seite **Übersicht** der App im Abschnitt **Authentifizierung** den ausgewählten Wert unter **Unterstützte Kontotypen**.
    * Wählen Sie **Nur Konten in diesem Verzeichnis** aus, wenn Sie eine Branchenanwendung erstellen. Wenn die Anwendung nicht in einem Verzeichnis registriert ist, ist diese Option nicht verfügbar.
    * Wählen Sie **Konten in einem beliebigen Organisationsverzeichnis** aus, wenn Sie alle Kunden aus dem Unternehmens- und Bildungsbereich ansprechen möchten.
    * Wählen Sie **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten** aus, um die breiteste Kundengruppe anzusprechen.
1. Wählen Sie **Speichern** aus.

## <a name="enable-oauth-20-implicit-grant-for-single-page-applications"></a>Aktivieren der impliziten OAuth 2.0-Genehmigung für Single-Page-Webanwendungen

Single-Page-Webanwendungen (SPAs) basieren in der Regel auf einem JavaScript-intensiven Front-End, das im Browser ausgeführt wird. Hierüber wird das Web-API-Back-End der Anwendung zum Ausführen der Geschäftslogik aufgerufen. Für SPAs, die in Azure AD gehostet werden, verwenden Sie die implizite OAuth 2.0-Gewährung zum Authentifizieren des Benutzers für Azure AD und rufen ein Token ab, mit dem Sie Aufrufe vom JavaScript-Client der Anwendung an die dazugehörige Back-End-Web-API schützen können.

Nachdem der Benutzer seine Zustimmung erteilt hat, können mit dem gleichen Authentifizierungsprotokoll Token zum Sichern von Aufrufen zwischen dem Client und anderen für die Anwendung konfigurierten Web-API-Ressourcen abgerufen werden. Informieren Sie sich über den Ablauf der impliziten OAuth 2.0-Genehmigung in Azure AD [v1.0](v1-oauth2-implicit-grant-flow.md) und [v2.0](v2-oauth2-implicit-grant-flow.md), um weitere Informationen zur impliziten Autorisierungsgenehmigung zu erhalten und entscheiden zu können, ob sie für Ihr Anwendungsszenario geeignet ist.

Die implizite OAuth 2.0-Genehmigung ist für Anwendungen standardmäßig deaktiviert. Sie können die implizite OAuth 2.0-Genehmigung für Ihre Anwendung mithilfe der unten aufgeführten Schritte aktivieren.

### <a name="to-enable-oauth-20-implicit-grant"></a>So aktivieren Sie die implizite OAuth 2.0-Gewährung

1. Navigieren Sie auf der Seite **Übersicht** der App zum Abschnitt **Authentifizierung**.
1. Suchen Sie unter **Erweiterte Einstellungen** nach dem Abschnitt **Implizite Genehmigung**.
1. Wählen Sie **ID-Token**, **Zugriffstoken** oder beides aus.
1. Wählen Sie **Speichern** aus.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die anderen Schnellstartanleitungen zur App-Verwaltung an:

* [Registrieren einer Anwendung bei der Microsoft Identity Platform](quickstart-register-app.md)
* [Konfigurieren einer Clientanwendung für den Zugriff auf Web-APIs](quickstart-configure-app-access-web-apis.md)
* [Konfigurieren einer Anwendung für das Verfügbarmachen von Web-APIs](quickstart-configure-app-expose-web-apis.md)
* [Entfernen einer bei der Microsoft Identity Platform registrierten Anwendung](quickstart-remove-app.md)

Um mehr über die beiden Azure AD-Objekte, die eine registrierte Anwendung darstellen, und die Beziehung zwischen ihnen zu erfahren, lesen Sie [Anwendungsobjekte und Dienstprinzipalobjekte](app-objects-and-service-principals.md).

Weitere Informationen zu den Brandingrichtlinien, die Sie bei der Entwicklung von Anwendungen mit Azure Active Directory verwenden sollten, finden Sie unter [Brandingrichtlinien für Anwendungen](howto-add-branding-in-azure-ad-apps.md).
