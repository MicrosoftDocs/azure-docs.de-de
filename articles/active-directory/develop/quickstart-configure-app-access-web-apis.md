---
title: 'Konfigurieren einer Anwendung für den Zugriff auf Web-APIs: Microsoft Identity Platform'
description: Es wird beschrieben, wie Sie bei der Microsoft Identity Platform registrierte Anwendung so konfigurieren, dass sie Umleitungs-URIs, Anmeldeinformationen oder Berechtigungen für den Zugriff auf Web-APIs enthält.
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
ms.openlocfilehash: 3b434e5b0c4524af642eb1255597cd3e7674571c
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545817"
---
# <a name="quickstart-configure-a-client-application-to-access-web-apis"></a>Schnellstart: Konfigurieren einer Clientanwendung für den Zugriff auf Web-APIs

Damit eine Webanwendung oder vertrauliche Clientanwendung an einem Flow zur Autorisierungsgenehmigung teilnehmen kann, bei dem eine Authentifizierung (und das Abrufen eines Zugriffstokens) erforderlich ist, benötigt sie sichere Anmeldeinformationen. Die Standardauthentifizierungsmethode im Azure-Portal ist „Client-ID + geheimer Schlüssel“.

Bevor ein Client Zugriff auf eine Web-API erhält, die durch eine Ressourcenanwendung (z.B. Microsoft Graph-API) bereitgestellt wird, stellt das Zustimmungs-Framework außerdem sicher, dass dem Client die erforderlichen Berechtigungen basierend auf den angeforderten Berechtigungen erteilt werden. Standardmäßig können alle Anwendungen Berechtigungen aus der Microsoft Graph-API wählen. Die [Graph-API-Berechtigung „Anmelden und Benutzerprofil lesen“](https://developer.microsoft.com/graph/docs/concepts/permissions_reference#user-permissions) ist standardmäßig ausgewählt. Sie können für jede gewünschte Web-API zwischen [zwei Arten von Berechtigungen](developer-glossary.md#permissions) wählen:

* **Anwendungsberechtigungen**: Ihre Clientanwendung benötigt direkten Zugriff auf die Web-API als sie selbst (kein Benutzerkontext). Für diese Art von Berechtigung ist die Zustimmung des Administrators erforderlich, und sie steht nicht für öffentliche Clientanwendungen (Desktop und mobil) zur Verfügung.
* **Delegierte Berechtigungen**: Ihre Clientanwendung benötigt als angemeldeter Benutzer Zugriff auf die Web-API. Der Zugriff ist aber durch die ausgewählte Berechtigung eingeschränkt. Diese Art von Berechtigung kann von einem Benutzer erteilt werden, sofern für die Berechtigung nicht die Zustimmung durch einen Administrator erforderlich ist.

  > [!NOTE]
  > Durch Hinzufügen einer delegierten Berechtigung zu einer Anwendung erteilen Sie den Benutzern im Mandanten nicht automatisch Ihre Zustimmung. Benutzer müssen den zusätzlichen delegierten Berechtigungen weiterhin zur Laufzeit manuell zustimmen, sofern der Administrator nicht im Namen aller Benutzer die Zustimmung erteilt hat.

In dieser Schnellstartanleitung wird veranschaulicht, wie Sie Ihre App für folgende Zwecke konfigurieren:

* [Hinzufügen von Umleitungs-URIs zur Anwendung](#add-redirect-uris-to-your-application)
* [Hinzufügen von Anmeldeinformationen zu Ihrer Webanwendung](#add-credentials-to-your-web-application)
* [Hinzufügen von Zugriffsberechtigungen für Web-APIs](#add-permissions-to-access-web-apis)

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
1. Wählen Sie die Anwendung aus, die Sie konfigurieren möchten. Nachdem Sie die App ausgewählt haben, wird die Seite **Übersicht** oder die Hauptseite für die Registrierung angezeigt.
1. Führen Sie die Schritte zum Konfigurieren Ihrer Anwendung für den Zugriff auf Web-APIs aus: 
    * [Hinzufügen von Umleitungs-URIs zur Anwendung](#add-redirect-uris-to-your-application)
    * [Hinzufügen von Anmeldeinformationen zu Ihrer Webanwendung](#add-credentials-to-your-web-application)
    * [Hinzufügen von Zugriffsberechtigungen für Web-APIs](#add-permissions-to-access-web-apis)

## <a name="add-redirect-uris-to-your-application"></a>Hinzufügen von Umleitungs-URIs zur Anwendung

[![Hinzufügen von benutzerdefinierten Umleitungs-URIs für Web-Apps und öffentliche Client-Apps](./media/quickstart-update-azure-ad-app-preview/authentication-redirect-uris-expanded.png)](./media/quickstart-update-azure-ad-app-preview/authentication-redirect-uris-expanded.png#lightbox)

Fügen Sie Ihrer Anwendung wie folgt einen Umleitungs-URI hinzu:

1. Wählen Sie auf der Seite **Übersicht** der App den Abschnitt **Authentifizierung**.

1. Führen Sie diese Schritte aus, um einen benutzerdefinierten Umleitungs-URI für Web-Apps und öffentliche Clientanwendungen hinzuzufügen:

   1. Suchen Sie nach dem Abschnitt **Umleitungs-URI**.
   1. Wählen Sie den Typ der zu erstellenden Anwendung aus: **Web** oder **Öffentlicher Client (Mobilgerät und Desktop)**.
   1. Geben Sie den Umleitungs-URI für Ihre Anwendung ein.
      * Geben Sie für Webanwendungen die Basis-URL Ihrer Anwendung an. `http://localhost:31544` kann beispielsweise die URL für eine Webanwendung sein, die auf einem lokalen Computer ausgeführt wird. Benutzer können diese URL nutzen, um sich an einer Webclientanwendung anzumelden.
      * Geben Sie für öffentliche Anwendungen den URI an, der von Azure AD zum Zurückgeben von Tokenantworten verwendet wird. Geben Sie einen für Ihre Anwendung spezifischen Wert ein, z.B. https://MyFirstApp.

1. Führen Sie diese Schritte aus, um eine Auswahl aus den vorgeschlagenen Umleitungs-URIs für öffentliche Clients (Mobilgerät, Desktop) zu treffen:

    1. Suchen Sie nach dem Abschnitt mit den Vorschlägen zu **Umleitungs-URIs für öffentliche Clients (Mobilgerät, Desktop)**.
    1. Wählen Sie die passenden Umleitungs-URIs für Ihre Anwendung aus, indem Sie die Kontrollkästchen verwenden.

## <a name="add-credentials-to-your-web-application"></a>Hinzufügen von Anmeldeinformationen zu Ihrer Webanwendung

[![Hinzufügen von Zertifikaten und geheimen Clientschlüsseln](./media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png)](./media/quickstart-update-azure-ad-app-preview/credentials-certificates-secrets-expanded.png#lightbox)

Fügen Sie Ihrer Webanwendung wie folgt Anmeldeinformationen hinzu:

1. Wählen Sie auf der Seite **Übersicht** der App den Abschnitt **Certificates & secrets** (Zertifikate und Geheimnisse).

1. Führen Sie diese Schritte aus, um ein Zertifikat hinzuzufügen:

    1. Wählen Sie **Zertifikat hochladen**.
    1. Wählen Sie die Datei aus, die Sie hochladen möchten. Die Datei muss einen der folgenden Dateitypen aufweisen: CER, PEM, CRT.
    1. Wählen Sie **Hinzufügen**.

1. Führen Sie diese Schritte aus, um einen geheimen Clientschlüssel hinzuzufügen:

    1. Wählen Sie **Neuer geheimer Clientschlüssel**.
    1. Fügen Sie eine Beschreibung für Ihren geheimen Clientschlüssel hinzu.
    1. Wählen Sie eine Dauer aus.
    1. Wählen Sie **Hinzufügen**.

> [!NOTE]
> Nach dem Speichern der Konfigurationsänderungen enthält die Spalte ganz rechts den Wert für den geheimen Clientschlüssel. **Kopieren Sie den Wert** zur Verwendung im Code Ihrer Clientanwendung, da er nach dem Verlassen dieser Seite nicht mehr zugänglich ist.

## <a name="add-permissions-to-access-web-apis"></a>Hinzufügen von Zugriffsberechtigungen für Web-APIs

[![Hinzufügen von API-Berechtigungen](./media/quickstart-update-azure-ad-app-preview/api-permissions-expanded.png)](./media/quickstart-update-azure-ad-app-preview/api-permissions-expanded.png#lightbox)

Fügen Sie Berechtigungen für den Zugriff auf Ressourcen-APIs von Ihrem Client wie folgt hinzu:

1. Wählen Sie auf der Seite **Übersicht** der App die Option **API-Berechtigungen**.
1. Wählen Sie die Schaltfläche **Berechtigung hinzufügen**.
1. Standardmäßig ermöglicht Ihnen die Ansicht die Auswahl von **Microsoft-APIs**. Wählen Sie den Abschnitt mit den APIs aus, die für Sie interessant sind:
    * **Microsoft-APIs**: Sie können Berechtigungen für Microsoft-APIs auswählen, z.B. Microsoft Graph.
    * **Von meiner Organisation verwendete APIs**: Sie können Berechtigungen für APIs auswählen, die von Ihrer Organisation verfügbar gemacht wurden, oder APIs, für die Ihre Organisation die Integration durchgeführt hat.
    * **Meine APIs**: Sie können Berechtigungen für APIs auswählen, die Sie verfügbar gemacht haben.
1. Nachdem Sie die APIs ausgewählt haben, wird die Seite **API-Berechtigungen anfordern** angezeigt. Wenn die API sowohl delegierte Berechtigungen als auch Anwendungsberechtigungen verfügbar macht, können Sie auswählen, welche Art von Berechtigung für Ihre Anwendung erforderlich ist.
1. Wählen Sie abschließend die Option **Berechtigungen hinzufügen**. Sie gelangen zurück auf die Seite **API-Berechtigungen**, auf der die Berechtigungen gespeichert und der Tabelle hinzugefügt wurden.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die anderen Schnellstartanleitungen zur App-Verwaltung an:

* [Registrieren einer Anwendung bei der Microsoft Identity Platform](quickstart-register-app.md)
* [Konfigurieren einer Anwendung für das Verfügbarmachen von Web-APIs](quickstart-configure-app-expose-web-apis.md)
* [Ändern der von einer Anwendung unterstützten Konten](quickstart-modify-supported-accounts.md)
* [Entfernen einer bei der Microsoft Identity Platform registrierten Anwendung](quickstart-remove-app.md)

Um mehr über die beiden Azure AD-Objekte, die eine registrierte Anwendung darstellen, und die Beziehung zwischen ihnen zu erfahren, lesen Sie [Anwendungsobjekte und Dienstprinzipalobjekte](app-objects-and-service-principals.md).

Weitere Informationen zu den Brandingrichtlinien, die Sie bei der Entwicklung von Anwendungen mit Azure Active Directory verwenden sollten, finden Sie unter [Brandingrichtlinien für Anwendungen](howto-add-branding-in-azure-ad-apps.md).
