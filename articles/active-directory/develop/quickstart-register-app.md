---
title: 'Registrieren einer App bei Microsoft Identity Platform: Microsoft Identity Platform'
description: Hier erfahren Sie, wie Sie eine Anwendung hinzufügen und bei der Microsoft Identity Platform registrieren.
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
ms.date: 05/09/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 156b26454aad6d6fd3230e19b47a938841331aec
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545711"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Schnellstart: Registrieren einer Anwendung bei Microsoft Identity Platform

Unternehmensentwickler und SaaS-Anbieter (Software-as-a-Service) können kommerzielle Clouddienste oder Branchenanwendungen entwickeln, die in die Microsoft Identity Platform integriert werden können, um eine sichere Anmeldung und Autorisierung für ihre Dienste bereitzustellen.

In dieser Schnellstartanleitung erfahren Sie, wie Sie eine Anwendung im Azure-Portal unter Verwendung von **App-Registrierungen** hinzufügen und registrieren, um die Anwendung in Microsoft Identity Platform integrieren zu können. Weitere Informationen zu den neuen Features und den Verbesserungen in der neuen App-Registrierungsumgebung finden Sie in [diesem Blogbeitrag](https://developer.microsoft.com/graph/blogs/new-app-registration/).

## <a name="register-a-new-application-using-the-azure-portal"></a>Registrieren einer neuen Anwendung mit dem Azure-Portal

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wenn Sie mit Ihrem Konto auf mehrere Mandanten zugreifen können, klicken Sie rechts oben auf Ihr Konto, und legen Sie Ihre Portalsitzung auf den gewünschten Azure AD-Mandanten fest.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** und anschließend **App-Registrierungen > Neue Registrierung** aus.
1. Geben Sie auf der daraufhin angezeigten Seite **Anwendung registrieren** die Registrierungsinformationen für Ihre Anwendung ein:

   - **Name**: Geben Sie einen aussagekräftigen Anwendungsnamen ein, der den Benutzern der App angezeigt wird.
   - **Unterstützte Kontotypen**: Wählen Sie aus, welche Konten von Ihrer Anwendung unterstützt werden sollen.

       | Unterstützte Kontotypen | BESCHREIBUNG |
       |-------------------------|-------------|
       | **Nur Konten in diesem Organisationsverzeichnis** | Wählen Sie diese Option aus, wenn Sie eine Branchenanwendung erstellen. Falls Sie die Anwendung nicht in einem Verzeichnis registrieren, ist diese Option nicht verfügbar.<br><br>Diese Option ist für reine Azure AD-Apps mit einem einzelnen Mandanten vorgesehen.<br><br>Sofern Sie die App nicht außerhalb eines Verzeichnisses registrieren, ist dies die Standardoption. Wird die App außerhalb eines Verzeichnisses registriert, werden standardmäßig mehrinstanzenfähige Azure AD-Konten und persönliche Microsoft-Konten verwendet. |
       | **Konten in einem beliebigen Organisationsverzeichnis** | Wählen Sie diese Option, wenn Sie alle Kunden aus dem Unternehmens- und Bildungsbereich ansprechen möchten.<br><br>Diese Option ist für reine Azure AD-Apps mit mehreren Mandanten vorgesehen.<br><br>Wenn Sie die App als reine Azure AD-App mit einem einzelnen Mandanten registriert haben, können Sie sie über das Blatt **Authentifizierung** in eine Azure AD-App mit mehreren Mandanten (und wieder zurück in eine App mit einem einzelnen Mandanten) verwandeln. |
       | **Konten in allen Organisationsverzeichnissen und persönliche Microsoft-Konten** | Verwenden Sie diese Option, um die breiteste Kundengruppe anzusprechen.<br><br>Diese Option ist für Azure AD-Apps mit mehreren Mandanten und für persönliche Microsoft-Konten vorgesehen.<br><br>Wenn Sie die App als Azure AD-App mit mehreren Mandanten und für persönliche Microsoft-Konten registriert haben, können Sie dies über die Benutzeroberfläche nicht ändern. In diesem Fall müssen die unterstützten Kontotypen mithilfe des Anwendungsmanifest-Editors geändert werden. |

   - **Umleitungs-URI (optional)**: Wählen Sie die Art der App aus, die Sie erstellen (**Web** oder **Öffentlicher Client (Mobilgerät und Desktop)**), und geben Sie dann den Umleitungs-URI (oder die Antwort-URL) für Ihre Anwendung ein.
       - Geben Sie für Webanwendungen die Basis-URL Ihrer App an. `http://localhost:31544` kann beispielsweise die URL für eine Web-App sein, die auf einem lokalen Computer ausgeführt wird. Benutzer können diese URL nutzen, um sich an einer Webclientanwendung anzumelden.
       - Geben Sie für öffentliche Clientanwendungen den URI an, der von Azure AD zum Zurückgeben von Tokenantworten verwendet wird. Geben Sie einen für Ihre Anwendung spezifischen Wert ein (beispielsweise `myapp://auth`).

     Spezifische Beispiele für Webanwendungen oder native Anwendungen finden Sie in unseren [Schnellstartanleitungen](https://docs.microsoft.com/azure/active-directory/develop/#quickstarts).

1. Wenn Sie so weit sind, klicken Sie auf **Registrieren**.

    [![Registrieren einer neuen Anwendung im Azure-Portal](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png#lightbox)

Azure AD weist Ihrer App eine eindeutige Anwendungs-ID (Client) zu und leitet Sie zur Seite **Übersicht** Ihrer Anwendung weiter. Wenn Sie Ihrer Anwendung weitere Funktionen hinzufügen möchten, können Sie weitere Konfigurationsoptionen wie Branding, Zertifikate und Geheimnisse, API-Berechtigungen und Ähnliches auswählen.

[![Übersichtsseite der neu registrierten App](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über [Berechtigungen und Zustimmung](v2-permissions-and-consent.md).
- Informationen zum Aktivieren zusätzlicher Konfigurationsfunktionen in Ihrer Anwendungsregistrierung (beispielsweise Anmeldeinformationen und Berechtigungen) sowie zum Ermöglichen der Anmeldung für Benutzer aus anderen Mandanten finden Sie in den folgenden Schnellstartanleitungen:
    - [Konfigurieren einer Clientanwendung für den Zugriff auf Web-APIs](quickstart-configure-app-access-web-apis.md)
    - [Konfigurieren einer Anwendung für das Verfügbarmachen von Web-APIs](quickstart-configure-app-expose-web-apis.md)
    - [Ändern der von einer Anwendung unterstützten Konten](quickstart-modify-supported-accounts.md)
- Nutzen Sie eine [Schnellstartanleitung](https://docs.microsoft.com/azure/active-directory/develop/#quickstarts), um in kurzer Zeit eine App zu erstellen und Funktionen hinzuzufügen – etwa zum Abrufen von Token, Aktualisieren von Token, Anmelden eines Benutzers oder Anzeigen von Benutzerinformationen.
- Um mehr über die beiden Azure AD-Objekte, die eine registrierte Anwendung darstellen, und die Beziehung zwischen ihnen zu erfahren, lesen Sie [Anwendungsobjekte und Dienstprinzipalobjekte](app-objects-and-service-principals.md).
- Weitere Informationen zu den Brandingrichtlinien, die Sie bei der App-Entwicklung verwenden sollten, finden Sie unter [Brandingrichtlinien für Anwendungen](howto-add-branding-in-azure-ad-apps.md).
