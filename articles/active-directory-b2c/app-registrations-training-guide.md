---
title: Neue Benutzeroberfläche für App-Registrierungen in Azure AD B2C
description: Dieser Artikel enthält eine Einführung in die neue Benutzeroberfläche für App-Registrierungen in Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/25/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7c9e3344ed819f607feeab88ac43a9dc286a61e0
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109095"
---
# <a name="the-new-app-registrations-experience-for-azure-active-directory-b2c"></a>Neue Benutzeroberfläche für App-Registrierungen in Azure Active Directory B2C

Die neue Benutzeroberfläche für **[App-Registrierungen](https://aka.ms/b2cappregistrations)** in Azure Active Directory B2C (Azure AD B2C) ist jetzt allgemein verfügbar. Wenn Sie bisher die Benutzeroberfläche des Anwendungsportals zum Registrieren von **Anwendungen** bei Azure AD B2C (im Folgenden als „Legacyoberfläche“ bezeichnet) verwendet haben, erleichtert Ihnen diese Anleitung den Einstieg in die Nutzung der neuen Benutzeroberfläche.

## <a name="overview"></a>Übersicht
Bisher mussten Sie Ihre kundenseitigen Azure AD B2C-Anwendungen mithilfe der Legacyoberfläche getrennt von Ihren restlichen Apps verwalten. Dies bedeutete die Nutzung unterschiedlicher Oberflächen zur App-Erstellung an unterschiedlichen Stellen in Azure.

Mit der neuen Benutzeroberfläche können alle App-Registrierungen bei Azure AD B2C und Azure AD von einem Ort aus konsistent verwaltet werden. Ob Sie nun eine kundenseitige App erstellen oder eine App mit Microsoft Graph-Berechtigungen für die Verwaltung von Ressourcen verwalten möchten – Sie müssen sich nur mit dieser einen Benutzeroberfläche vertraut machen.

Navigieren Sie im Azure-Portal von einem Azure AD B2C-Mandanten des **Azure AD B2C**- oder **Azure Active Directory**-Diensts aus zu **App-Registrierungen**, um zur neuen Benutzeroberfläche zu gelangen.

Die Azure AD B2C-Benutzeroberfläche für App-Registrierungen basiert auf der allgemeinen [Oberfläche für App-Registrierungen](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) bei einem beliebigen Azure AD-Mandanten, ist jedoch an die Anforderungen von Azure AD B2C-Mandanten angepasst.

## <a name="whats-not-changing"></a>Was ändert sich nicht?
- Ihre Anwendungen und die zugehörigen Konfigurationen finden Sie unverändert in der neuen Umgebung. Sie müssen die Anwendungen nicht erneut registrieren, und die Benutzer Ihrer Anwendungen müssen sich nicht erneut anmelden.

> [!NOTE]
> Wenn Sie alle bisher erstellten Anwendungen anzeigen möchten, navigieren Sie zum Blatt **App-Registrierungen**, und wählen Sie die Registerkarte **Alle Anwendungen** aus. Hier werden alle Apps angezeigt, die Sie über die Legacyoberfläche, die neue Oberfläche sowie im Azure AD-Dienst erstellt haben.

## <a name="key-new-features"></a>Wichtige neue Funktionen

-   In einer **einheitlichen App-Liste** werden alle Anwendungen, die sich bei Azure AD B2C und Azure AD authentifizieren, an einem zentralen Ort angezeigt. Außerdem können Sie Funktionen nutzen, die bereits für Azure AD-Anwendungen verfügbar sind, wie etwa die Datumsangabe **Erstellt am**, der Status **Zertifikate und Geheimnisse**, die Suchleiste und vieles mehr.

-   Über die **kombinierte App-Registrierung** können Sie auf schnelle Weise eine App registrieren – unabhängig davon, ob es sich um eine kundenseitige App oder eine App für den Zugriff auf Microsoft Graph handelt.

- Im Bereich **Endpunkte** können Sie schnell die relevanten Endpunkte für Ihr Szenario ermitteln, einschließlich der Endpunkte für die OpenID Connect-Konfiguration, SAML-Metadaten, Microsoft Graph-API sowie des [OAuth 2.0-Benutzerflows](tokens-overview.md#endpoints).

- Über die Funktionen **API-Berechtigungen** und **Eine API verfügbar machen** verfügen Sie über erweiterte Möglichkeiten zur Verwaltung von Bereichen, Berechtigungen und Einwilligungen. Sie können einer App nun auch MS Graph- und Azure AD Graph-Berechtigungen zuweisen.

-   Die Funktionen **Besitzer** und **Manifest** sind jetzt für Apps verfügbar, die sich bei Azure AD B2C authentifizieren. Sie können Ihren Registrierungen Besitzer hinzufügen und mithilfe des [Manifest-Editors](../active-directory/develop/reference-app-manifest.md) die Anwendungseigenschaften direkt bearbeiten.


## <a name="new-supported-account-types"></a>Neue unterstützte Kontotypen

In der neuen Benutzeroberfläche können Sie aus den folgenden Optionen einen unterstützten Kontotyp auswählen:
- Nur Konten in diesem Organisationsverzeichnis
- Konten in einem beliebigen Organisationsverzeichnis (beliebiges Azure AD-Verzeichnis – mehrinstanzenfähig)
- Konten in einem beliebigen Identitätsanbieter oder Organisationsverzeichnis (zum Authentifizieren von Benutzern mit Benutzerabläufen)

Weitere Informationen zu den unterschiedlichen Kontotypen finden Sie in der Benutzeroberfläche für die Erstellung unter **Hilfe bei der Auswahl**.

In der Legacyoberfläche wurden alle Apps als kundenseitige Anwendungen erstellt. Bei diesen Apps wird der Kontotyp auf **-Konten in einem beliebigen Identitätsanbieter oder Organisationsverzeichnis festgelegt (zum Authentifizieren von Benutzern mit Benutzerabläufen)** .
> [!NOTE]
> Diese Option ist erforderlich, um Azure AD B2C-Benutzerflows für die Authentifizierung von Benutzern bei dieser Anwendung ausführen zu können. Informationen zur Registrierung einer Anwendung für die Verwendung mit Benutzerflows finden Sie [hier](tutorial-register-applications.md).

Über diese Option können Sie auch Azure AD B2C als SAML-Dienstanbieter verwenden. [Weitere Informationen](identity-provider-adfs.md)

## <a name="applications-for-devops-scenarios"></a>Anwendungen für DevOps-Szenarios
Mit den anderen Kontotypen können Sie eine App erstellen, mit der Sie DevOps-Szenarios, wie z. B. das Hochladen von Identity Experience Framework-Richtlinien oder das Bereitstellen von Benutzern mithilfe von Microsoft Graph, verwalten können. Informationen zum Registrieren einer Microsoft Graph-Anwendung zur Verwaltung von Azure AD B2C-Ressourcen finden Sie [hier](microsoft-graph-get-started.md).

Möglicherweise werden nicht alle Microsoft Graph-Berechtigungen angezeigt, da viele dieser Berechtigungen für Azure B2C-Consumerbenutzer nicht gelten. Informationen zur Verwaltung von Benutzern mit Microsoft Graph finden Sie [hier](manage-user-accounts-graph-api.md).

## <a name="admin-consent-and-offline_accessopenid-scopes"></a>Administratoreinwilligung und die Bereiche „offline_access“ und „openid“
<!-- Azure AD B2C doesn't support user consent. That is, when a user signs into an application, the user doesn't see a screen requesting consent for the application permissions. All permissions have to be granted through admin consent.  -->

Der Bereich **openid** ist für die Anmeldung von Benutzern bei einer App durch Azure AD B2C erforderlich. Der Bereich **offline_access** wird für das Ausstellen von Aktualisierungstoken für einen Benutzer benötigt. Diese Bereiche wurden neu hinzugefügt und verfügen standardmäßig über Administratoreinwilligung. Nun können Sie während des Erstellungsprozesses problemlos Berechtigungen für diese Bereiche hinzufügen, indem Sie sicherstellen, dass die Option **Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen** aktiviert ist. Alternativ dazu können Sie Microsoft Graph-Berechtigungen mit Administratoreinwilligung auch in den Einstellungen **API-Berechtigungen** für eine vorhandene App hinzufügen.

Informationen zu Berechtigungen und Einwilligungen finden Sie [hier](../active-directory/develop/v2-permissions-and-consent.md).

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>Plattformen/Authentifizierung Antwort-URLs/Umleitungs-URIs
In der Legacyoberfläche konnten Sie unter **Eigenschaften** die verschiedenen Plattformtypen als Antwort-URLs für Web-Apps/-APIs und Umleitungs-URIs für native Clients verwalten. „Native Clients“, auch „öffentliche Clients“ genannt, beinhalten Apps für iOS, macOS, Android und andere Typen von Mobil- und Desktopanwendungen.

In der neuen Benutzeroberfläche werden sowohl Antwort-URLs als auch Umleitungs-URIs „Umleitungs-URIs“ genannt. Sie befinden sich im Abschnitt **Authentifizierung** einer App. App-Registrierungen sind nicht auf eine Web-App oder eine native Anwendung beschränkt. Sie können die gleiche App-Registrierung für all diese Plattformtypen verwenden, indem Sie die entsprechenden Umleitungs-URIs registrieren.

Umleitungs-URIs müssen einem App-Typ zugeordnet werden: entweder „Web“ oder „Öffentlich“ (Mobilgerät und Desktop). Weitere Informationen zu Umleitungs-URIs finden Sie [hier](../active-directory/develop/quickstart-register-app.md#add-a-redirect-uri).

<!-- Whether an application should be treated as a public client is inferred at run-time from the Redirect URI platform type, if possible. The **Treat application as a public client** setting should be set to **Yes** for flows that might not use a redirect URI, such as ROPC flows. -->

Die Plattformen für **iOS/macOS** und **Android** sind vom Typ „Öffentlicher Client“. Sie stellen eine einfache Möglichkeit zur Konfiguration von iOS/macOS- oder Android-Apps mit den entsprechenden Umleitungs-URIs für die Nutzung mit MSAL dar. Weitere Informationen zu den Optionen bei der Anwendungskonfiguration finden Sie [hier](../active-directory/develop/msal-client-applications.md).


## <a name="application-certificates--secrets"></a>Anwendungszertifikate und -geheimnisse

In der neuen Benutzerfläche werden Zertifikate und Geheimnisse nicht mehr über das Blatt **Schlüssel**, sondern über **Zertifikate und Geheimnisse** verwaltet. Anwendungen können über Zertifikate und Geheimnisse vom Authentifizierungsdienst identifiziert werden, wenn sie Token (über ein HTTPS-Schema) an einem über das Web aufrufbaren Speicherort erhalten. Es wird empfohlen, für die Authentifizierung bei Azure AD in Szenarios mit Clientanmeldeinformationen anstelle eines Clientgeheimnisses ein Zertifikat zu verwenden. Für die Authentifizierung bei Azure AD B2C können keine Zertifikate verwendet werden.


## <a name="features-not-applicable-in-azure-ad-b2c-tenants"></a>Nicht für Azure AD B2C-Mandanten geltende Features
Die folgenden Azure AD-Features für die App-Registrierung gelten nicht für Azure AD B2C-Mandanten oder sind für diese Mandanten nicht verfügbar:
- **Rollen und Administratoren** – Für Azure AD B2C zurzeit nicht verfügbar.
- **Branding**: Die Anpassung der Benutzeroberfläche wird über die Benutzeroberfläche **Unternehmensbranding** oder als Teil eines Benutzerflows konfiguriert. Informationen zum Anpassen der Benutzeroberfläche in Azure Active Directory B2C finden Sie [hier](customize-ui-with-html.md).
- **Überprüfung der Herausgeberdomäne**: Ihre App ist in der Domäne *.onmicrosoft.com* registriert, die keine überprüfte Domäne darstellt. Außerdem wird die Herausgeberdomäne hauptsächlich zum Erteilen von Benutzereinwilligungen verwendet, die für die Benutzerauthentifizierung bei Azure AD B2C-Apps nicht gelten. Weitere Informationen zur Herausgeberdomäne finden Sie [hier](../active-directory/develop/howto-configure-publisher-domain.md).
- **Tokenkonfiguration**: Token werden als Teil eines Benutzerflows und nicht für Apps konfiguriert.
- Die Funktion **Schnellstarts** ist derzeit für Azure AD B2C-Mandanten nicht verfügbar.
<!-- - The **Integration assistant** blade is currently not available for Azure AD B2C tenants. -->

## <a name="limitations"></a>Einschränkungen
Für die neue Umgebung gelten die folgenden Einschränkungen:
- Derzeit wird in Azure AD B2C nicht unterschieden zwischen der Möglichkeit, für implizite Flows Zugriffstoken auszustellen oder ID-Token. Ist im Blatt **Authentifizierung** die Option **ID-Token** aktiviert, sind für Flows zur impliziten Genehmigung beide Tokentypen verfügbar.
<!-- - Azure AD B2C doesn't currently support the single-page application "SPA" app type.  -->
- Das Ändern des Werts für unterstützte Konten wird in der neuen Benutzeroberfläche nicht unterstützt. Dies ist nur über das App-Manifest möglich, sofern Sie nicht zwischen Azure AD mit einem Mandanten und mehreren Mandanten wechseln.

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den ersten Schritten mit der neuen Benutzeroberfläche für App-Registrierungen finden Sie unter:
* [Tutorial: Registrieren einer Webanwendung in Azure Active Directory B2C](tutorial-register-applications.md)
* [Hinzufügen einer Web-API-Anwendung zu Ihrem Azure Active Directory B2C-Mandanten](add-web-api-application.md)
* [Hinzufügen einer nativen Clientanwendung zu Ihrem Active Directory B2C-Mandanten](add-native-application.md)
* [Registrieren einer Microsoft Graph-Anwendung zur Verwaltung von Azure AD B2C-Ressourcen](microsoft-graph-get-started.md)
* [Verwenden von Azure AD B2C als SAML-Dienstanbieter](identity-provider-adfs.md)
* [In Active Directory B2C verwendbare Anwendungstypen](application-types.md)