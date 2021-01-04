---
title: Optionen für einmaliges Anmelden in Azure AD
description: Hier erfahren Sie mehr über die verfügbaren Optionen für einmaliges Anmelden (Single Sign-On, SSO) in Azure Active Directory.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: kenwith
ms.reviewer: arvindh, japere
ms.openlocfilehash: f7ec8a913634322be5a1eb854972cfa2a0217381
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94651922"
---
# <a name="single-sign-on-options-in-azure-ad"></a>Optionen für einmaliges Anmelden in Azure AD

Einmaliges Anmelden bietet viele Vorteile gegenüber herkömmlichen Anmeldemethoden.

- **Mit einmaligem Anmelden** melden sich Benutzer einmal mit einem Konto an, um auf in die Domäne eingebundene Geräte, Unternehmensressourcen, SaaS-Anwendungen (Software-as-a-Service) und Webanwendungen zuzugreifen. Nach der Anmeldung kann der Benutzer Anwendungen aus dem Office 365-Portal oder über „Meine Apps“ starten. Administratoren können die Verwaltung von Benutzerkonten zentralisieren und den Benutzerzugriff auf Anwendungen basierend auf der Gruppenmitgliedschaft automatisch hinzufügen oder entfernen.

- **Ohne einmaliges Anmelden** müssen sich die Benutzer anwendungsspezifische Kennwörter merken und sich für jede Anwendung anmelden. IT-Mitarbeiter müssen Benutzerkonten für jede Anwendung (z. B. Microsoft 365, Box und Salesforce) erstellen und aktualisieren. Benutzer müssen sich ihre Kennwörter merken und außerdem Zeit für die Anmeldung bei jeder Anwendung verbringen.

Weitere Informationen zum einmaligen Anmelden finden Sie unter [Worum handelt es sich beim einmaligen Anmelden?](what-is-single-sign-on.md).

## <a name="choosing-a-single-sign-on-method"></a>Auswählen einer Methode für einmaliges Anmelden

Es gibt mehrere Möglichkeiten, eine Anwendung für einmaliges Anmelden zu konfigurieren. Die Auswahl einer Methode für einmaliges Anmelden hängt davon ab, wie die Anwendung für die Authentifizierung konfiguriert ist.

- Cloudanwendungen können für einmaliges Anmelden die Methoden „OpenID Connect“, „OAuth“, „SAML“, „Kennwort“, „Verknüpft“ oder „Deaktiviert“ verwenden. 
- Lokale Anwendungen können für einmaliges Anmelden die Methoden „Kennwort“, „Integrierte Windows-Authentifizierung“, „Header“, „Verknüpft“ oder „Deaktiviert“ verwenden. Die lokalen Optionen funktionieren, wenn die Anwendungen für den Anwendungsproxy konfiguriert wurden.

Dieses Flussdiagramm erleichtert Ihnen die Entscheidung, welche Methode des einmaligen Anmeldens für Ihre Situation am besten geeignet ist.

![Flussdiagramm zum Treffen einer Entscheidung für eine Methode zum einmaligen Anmelden](./media/what-is-single-sign-on/choose-single-sign-on-method-040419.png)

In der folgenden Tabelle werden die Methoden für einmaliges Anmelden zusammengefasst und Links zu weiteren Details angegeben.

| Methode für einmaliges Anmelden | Anwendungstypen | Verwendung |
| :------ | :------- | :----- |
| [OpenID Connect und OAuth](#openid-connect-and-oauth) | Cloud und lokal | Verwenden Sie OpenID Connect und OAuth, wenn Sie eine neue Anwendung entwickeln. Dieses Protokoll vereinfacht die Anwendungskonfiguration, verfügt über leicht zu verwendende SDKs und ermöglicht für Ihre Anwendung die Nutzung von MS Graph.
| [SAML](#saml-sso) | Cloud und lokal | Wählen Sie SAML nach Möglichkeit immer für vorhandene Anwendungen, für die nicht OpenID Connect oder OAuth genutzt wird. SAML funktioniert für Anwendungen, bei denen die Authentifizierung mit einem der SAML-Protokolle durchgeführt wird.|
| [Kennwortbasiert](#password-based-sso) | Cloud und lokal | Wählen Sie die kennwortbasierte Methode, wenn die Anwendung die Authentifizierung mit Benutzername und Kennwort vornimmt. Das kennwortbasierte einmalige Anmelden ermöglicht die sichere Speicherung des Anwendungskennworts und dessen Wiedergabe mit einer Webbrowsererweiterung oder einer mobilen App. Mit dieser Methode wird der von der Anwendung bereitgestellte vorhandene Anmeldevorgang genutzt, die Kennwortverwaltung kann jedoch der Administrator übernehmen. |
| [Verknüpft](#linked-sign-on) | Cloud und lokal | Wählen Sie das verknüpfte Anmelden aus, wenn die Anwendung für einmaliges Anmelden bei einem anderen Identitätsanbieterdienst konfiguriert ist. Mit dieser Option wird der Anwendung kein einmaliges Anmelden hinzugefügt. Die Anwendung kann das einmalige Anmelden jedoch möglicherweise bereits über einen anderen Dienst implementiert haben, z.B. Active Directory-Verbunddienste.|
| [Disabled](#disabled-sso) | Cloud und lokal | Wählen Sie das deaktivierte einmalige Anmelden, wenn die App nicht für einmaliges Anmelden konfiguriert werden kann. Dieser Modus ist beim Erstellen der App standardmäßig eingestellt.|
| [Integrierte Windows-Authentifizierung (IWA)](#integrated-windows-authentication-iwa-sso) | Nur lokal | Wählen Sie einmaliges Anmelden vom Typ IWA für Anwendungen, die die [integrierte Windows-Authentifizierung (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication) verwenden oder Ansprüche unterstützen. Bei IWA verwenden die Anwendungsproxyconnectors die eingeschränkte Kerberos-Delegierung (Kerberos Constrained Delegation, KCD), um Benutzer für die Anwendung zu authentifizieren. |
| [Headerbasiert](#header-based-sso) | Nur lokal | Verwenden Sie das headerbasierte einmalige Anmelden, wenn die Anwendung für die Authentifizierung Header verwendet. Der Anwendungsproxy verwendet Azure AD, um den Benutzer zu authentifizieren, und leitet Datenverkehr dann über den Connectordienst weiter.  |

## <a name="openid-connect-and-oauth"></a>OpenID Connect und OAuth 2.0

Verwenden Sie beim Entwickeln neuer Anwendungen moderne Protokolle wie OpenID Connect und OAuth, um den Benutzern Ihrer App durch das einmalige Anmelden die beste Anmeldeerfahrung über mehrere Plattformen hinweg zu bieten. OAuth gibt Benutzern oder Administratoren die Möglichkeit, für geschützte Ressourcen wie [Microsoft Graph](/graph/overview) ihre [Einwilligung zu erteilen](configure-user-consent.md). Wir stellen einfach zu übernehmende [SDKs](../develop/reference-v2-libraries.md) für Ihre App bereit. Außerdem ist Ihre App dann einsatzbereit für die Verwendung von [Microsoft Graph](/graph/overview).

Weitere Informationen finden Sie unter

- [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md)
- [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)
- [Microsoft Identity Platform (vormals Azure Active Directory für Entwickler)](../develop/index.yml)

## <a name="saml-sso"></a>SAML SSO

Mit **SAML SSO** nimmt Azure AD die Authentifizierung bei der Anwendung mithilfe des Azure AD-Kontos des Benutzers vor. Azure AD gibt die Informationen für das einmalige Anmelden über ein Verbindungsprotokoll an die Anwendung weiter. Mit SAML-basiertem einmaligem Anmelden können Sie Benutzer basierend auf Regeln, die Sie in Ihren SAML-Ansprüchen definieren, bestimmten Anwendungsrollen zuordnen.

Wählen Sie SAML-basiertes einmaliges Anmelden, wenn die Anwendung dies unterstützt.

SAML-basiertes einmaliges Anmelden wird für Anwendungen unterstützt, die eines dieser Protokolle verwenden:

- SAML 2.0
- WS-Federation-

Informationen zum Konfigurieren einer SaaS-Anwendung für SAML-basiertes einmaliges Anmelden finden Sie unter [Konfigurieren des SAML-basierten einmaligen Anmeldens](configure-saml-single-sign-on.md). Viele SaaS-Anwendungen (Software-as-a-Service) verfügen auch über ein [anwendungsspezifisches Tutorial](../saas-apps/tutorial-list.md), in dem die Konfiguration des SAML-basierten einmaligen Anmeldens Schritt für Schritt beschrieben wird.

Um eine Anwendung für den WS-Verbund zu konfigurieren, führen Sie die gleichen Anweisungen wie zum Konfigurieren der Anwendung für SAML-basiertes einmaliges Anmelden aus. Im Schritt zum Konfigurieren der Anwendung für die Verwendung von Azure AD müssen Sie die Azure AD-Anmelde-URL für den WS-Verbund-Endpunkt `https://login.microsoftonline.com/<tenant-ID>/wsfed` ersetzen.

Informationen zum Konfigurieren einer lokalen Anwendung für SAML-basiertes einmaliges Anmelden finden Sie unter [SAML-SSO (einmaliges Anmelden) für lokale Anwendungen mit dem Anwendungsproxy](application-proxy-configure-single-sign-on-on-premises-apps.md).

Weitere Informationen zum SAML-Protokoll finden Sie unter [SAML-Protokoll für einmaliges Anmelden](../develop/single-sign-on-saml-protocol.md).

## <a name="password-based-sso"></a>Kennwortbasiertes einmaliges Anmelden

Bei der kennwortbasierten Anmeldung melden sich Benutzer mit einem Benutzernamen und einem Kennwort bei der Anwendung an, wenn sie zum ersten Mal darauf zugreifen. Nach der ersten Anmeldung werden der Benutzername und das Kennwort von Azure AD für die Anwendung bereitgestellt.

Beim kennwortbasierten einmaligen Anmelden wird der vorhandene Authentifizierungsvorgang der Anwendung verwendet. Wenn Sie einmaliges Anmelden per Kennwort für eine Anwendung aktivieren, sammelt Azure AD Benutzernamen und Kennwörter für die Anwendung und speichert diese sicher. Anmeldeinformationen des Benutzers werden in einem verschlüsselten Zustand im Verzeichnis gespeichert.

Wählen Sie das kennwortbasierte einmalige Anmelden in folgenden Fällen:

- Eine Anwendung unterstützt das SAML-SSO-Protokoll nicht.
- Eine Anwendung authentifiziert sich mit Benutzername und Kennwort anstelle von Zugriffstoken und Headern.

>[!NOTE]
>Sie können keine Richtlinien für bedingten Zugriff oder mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) auf das kennwortbasierte einmalige Anmelden anwenden.

Das kennwortbasierte einmalige Anmelden wird für jede cloudbasierte Anwendung unterstützt, die über eine HTML-basierte Anmeldeseite verfügt. Der Benutzer kann jeden der folgenden Browser verwenden:

- Internet Explorer 11 unter Windows 7 oder höher
   > [!NOTE]
   > Internet Explorer wird nur eingeschränkt unterstützt, und es werden keine neuen Softwareupdates dafür bereitgestellt. Microsoft Edge ist der empfohlene Browser.

- Microsoft Edge in Windows 10 Anniversary Edition oder höher
- Microsoft Edge für iOS und Android
- Intune Managed Browser
- Chrome: Unter Windows 7 oder höher und macOS X oder höher
- Firefox 26.0 oder höher: Unter Windows XP SP2 oder höher und macOS X 10.6 oder höher

Informationen zum Konfigurieren einer Cloudanwendung für kennwortbasiertes einmaliges Anmelden finden Sie unter [Konfigurieren des einmaligen Anmeldens per Kennwort](configure-password-single-sign-on-non-gallery-applications.md).

Informationen zum Konfigurieren einer lokalen Anwendung für einmaliges Anmelden über den Anwendungsproxy finden Sie unter [Kennworttresore (Password Vaulting) für einmaliges Anmelden mit Anwendungsproxy](application-proxy-configure-single-sign-on-password-vaulting.md).

### <a name="how-authentication-works-for-password-based-sso"></a>Funktionsweise der Authentifizierung für kennwortbasiertes einmaliges Anmelden

Um einen Benutzer bei einer Anwendung zu authentifizieren, ruft Azure AD die Anmeldeinformationen des Benutzers aus dem Verzeichnis ab und gibt sie auf der Anmeldeseite der Anwendung ein.  Azure AD übermittelt die Anmeldeinformationen des Benutzers sicher über eine Webbrowsererweiterung oder eine mobile App. Durch diesen Vorgang können Administratoren Anmeldeinformationen von Benutzern verwalten, ohne dass Benutzer sich ihr Kennwort merken müssen.

> [!IMPORTANT]
> Die Anmeldeinformationen werden während des automatisierten Anmeldevorgangs vor dem Benutzer verborgen. Die Anmeldeinformationen können jedoch mithilfe von Webdebugtools ermittelt werden. Benutzer und Administratoren müssen die gleichen Sicherheitsrichtlinien befolgen, als wären die Anmeldeinformationen direkt vom Benutzer eingegeben worden.

### <a name="managing-credentials-for-password-based-sso"></a>Verwalten von Anmeldeinformationen für kennwortbasiertes einmaliges Anmelden

Die Kennwörter für die einzelnen Anwendungen können vom Azure AD-Administrator oder von den Benutzern verwaltet werden.

Wenn der Azure AD-Administrator die Anmeldeinformationen verwaltet, gilt Folgendes:  

- Der Benutzer muss seinen Benutzernamen und sein Kennwort nicht zurücksetzen oder sie sich merken. Der Benutzer kann durch Klicken auf die Anwendung unter „Meine Apps“ oder über einen bereitgestellten Link auf diese zugreifen.
- Der Administrator kann Verwaltungsaufgaben für die Anmeldeinformationen ausführen. Beispielsweise kann der Administrator den Anwendungszugriff entsprechend Gruppenmitgliedschaften und dem Mitarbeiterstatus des Benutzers aktualisieren.
- Der Administrator kann mithilfe von administrativen Anmeldeinformationen Zugriff auf Anwendungen bereitstellen, die von vielen Benutzern gemeinsam verwendet werden. Beispielsweise kann der Administrator jeder Person mit Zugriff auf eine Anwendung den Zugriff auf eine Anwendung für sozialen Medien oder zum Freigeben von Dokumenten gewähren.

Wenn der Endbenutzer die Anmeldeinformationen verwaltet, gilt Folgendes:

- Benutzer können ihre Kennwörter verwalten, indem Sie sie nach Bedarf aktualisieren oder löschen.
- Administratoren können weiterhin neue Anmeldeinformationen für die Anwendung festlegen.

## <a name="linked-sign-on"></a>Verknüpfte Anmeldung
Über das verknüpfte einmalige Anmelden kann Azure AD einmaliges Anmelden bei einer Anwendung, die bereits in einem anderen Dienst für einmaliges Anmelden konfiguriert ist, bereitstellen. Die verknüpfte Anwendung kann Endbenutzern im Office 365-Portal oder im Azure AD-MyApps-Portal angezeigt werden. Beispielsweise kann ein Benutzer eine Anwendung, die für einmaliges Anmelden in Active Directory-Verbunddienste 2.0 (AD FS) konfiguriert ist, über das Office 365-Portal starten. Für verknüpfte Anwendungen, die über das Office 365-Portal oder das Azure AD-MyApps-Portal gestartet werden, steht außerdem eine zusätzliche Berichterstellung zur Verfügung. Informationen zum Konfigurieren einer Anwendung für die Anmeldung über Link finden Sie unter [Konfigurieren der Anmeldung über Link](configure-linked-sign-on.md).

### <a name="linked-sign-on-for-application-migration"></a>Verknüpftes Anmelden für die Migration von Anwendungen

Das verknüpfte Anmelden sorgt während der Migration von Anwendungen über einen längeren Zeitraum für eine einheitliche Benutzererfahrung. Wenn Sie Anwendungen in Azure Active Directory migrieren, können Sie mithilfe des verknüpften Anmeldens schnell Links für alle Anwendungen veröffentlichen, die Sie migrieren möchten.  Benutzer finden alle Links im [MyApps-Portal](../user-help/my-apps-portal-end-user-access.md) und im [Microsoft 365-Anwendungsstarter](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a). Benutzer erkennen nicht, dass sie auf eine verknüpfte Anwendung bzw. eine migrierte Anwendung zugreifen.  

Nachdem ein Benutzer sich mit einer verknüpften Anwendung authentifiziert hat, muss ein Kontodatensatz erstellt werden, bevor der Endbenutzer Zugriff durch das einmalige Anmelden erhält. Die Bereitstellung dieses Kontodatensatzes kann automatisch oder manuell durch einen Administrator erfolgen.

>[!NOTE]
>Sie können keine Richtlinien für bedingten Zugriff oder mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) auf eine verknüpfte Anwendung anwenden. Der Grund dafür ist, dass eine verknüpfte Anwendung keine Funktionen für einmaliges Anmelden über Azure AD bereitstellt. Wenn Sie eine verknüpfte Anwendung konfigurieren, fügen Sie einfach einen Link hinzu, der im App-Startfeld oder im Portal „Meine Apps“ angezeigt wird. 

## <a name="disabled-sso"></a>Deaktiviertes einmaliges Anmelden

Der deaktivierte Modus bedeutet, dass einmaliges Anmelden nicht für die Anwendung verwendet wird. Wenn einmaliges Anmelden deaktiviert ist, müssen sich Benutzer möglicherweise zweimal authentifizieren. Beim ersten Mal authentifizieren die Benutzer sich in Azure AD, danach melden sie sich bei der Anwendung an.

Verwenden Sie den deaktivierten Modus für das einmalige Anmelden in folgenden Fällen:

- Wenn Sie noch nicht bereit sind, diese Anwendung in das einmalige Anmelden mit Azure AD-SSO zu integrieren
- Wenn Sie andere Aspekte der Anwendung testen
- Als Sicherheitsebene für eine lokale Anwendung, die keine Authentifizierung von Benutzern erfordert. Im deaktivierten Modus muss der Benutzer sich authentifizieren.

Hinweis: Wenn Sie die Anwendung für das SP-initiierte und SAML-basierte einmalige Anmelden konfiguriert haben und den SSO-Modus in „Deaktiviert“ ändern, wird nicht verhindert, dass sich Benutzer außerhalb des Portals „Meine Apps“ bei der Anwendung anmelden. Dazu müssen Sie die [Anmeldemöglichkeit der Benutzer deaktivieren](disable-user-sign-in-portal.md).

## <a name="integrated-windows-authentication-iwa-sso"></a>Einmaliges Anmelden mit der integrierten Windows-Authentifizierung (IWA)

Der [Anwendungsproxy](application-proxy.md) ermöglicht einmaliges Anmelden (SSO) für Anwendungen, die die [integrierte Windows-Authentifizierung (IWA)](/aspnet/web-api/overview/security/integrated-windows-authentication) verwenden oder Ansprüche unterstützen. Wenn Ihre Anwendung die integrierte Windows-Authentifizierung verwendet, authentifiziert sich der Anwendungsproxy mithilfe der eingeschränkten Kerberos-Delegierung (Kerberos Constrained Delegation, KCD) bei der Anwendung. Bei einer Anwendung, die Ansprüche unterstützt und Azure Active Directory vertraut, funktioniert das einmalige Anmelden, da der Benutzer bereits über Azure AD authentifiziert wurde.

Wählen Sie den Modus für einmaliges Anmelden für die integrierte Windows-Authentifizierung (IWA) aus, um einmaliges Anmelden für eine lokale App mit IWA zur Verfügung zu stellen.

Informationen zum Konfigurieren einer lokalen App für IWA finden Sie unter [Eingeschränkte Kerberos-Delegierung für einmaliges Anmelden bei Ihren Anwendungen mit dem Anwendungsproxy](application-proxy-configure-single-sign-on-with-kcd.md).

### <a name="how-single-sign-on-with-kcd-works"></a>So funktioniert das einmalige Anmelden mit KCD
Dieses Diagramm erläutert die Vorgänge, die bei einem Zugriff eines Benutzers auf eine lokale Anwendung, die IWA verwendet, ablaufen.

![Flussdiagramm für die Microsoft Azure AD-Authentifizierung](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. Der Benutzer gibt die URL ein, um über den Anwendungsproxy auf die lokale Anwendung zuzugreifen.
1. Der Anwendungsproxy leitet die Anforderung zur Vorauthentifizierung an Azure AD-Authentifizierungsdienste weiter. Zu diesem Zeitpunkt wendet Azure AD alle gültigen Authentifizierungs- und Autorisierungsrichtlinien an, wie z. B. mehrstufige Authentifizierung. Nachdem der Benutzer überprüft wurde, erstellt Azure AD ein Token und sendet es an den Benutzer.
1. Der Benutzer übergibt das Token an den Anwendungsproxy.
1. Der Anwendungsproxy überprüft das Token und ruft den Benutzerprinzipalnamen aus dem Token ab. Dann sendet er die Anforderung, den UPN und den Dienstprinzipalnamen über einen zweifach authentifizierten sicheren Kanal an den Connector.
1. Der Connector führt eine KCD-Aushandlung (Kerberos Constrained Delegation) mit dem lokalen AD aus und nimmt dabei die Identität des Benutzers an, um ein Kerberos-Token für die Anwendung zu erhalten.
1. Active Directory sendet das Kerberos-Token für die Anwendung an den Connector.
1. Der Connector sendet die ursprüngliche Anforderung an den Anwendungsserver und verwendet dabei das von AD empfangene Kerberos-Token.
1. Die Anwendung sendet die Antwort an den Connector, die dann an den Anwendungsproxydienst und schließlich an den Benutzer zurückgegeben wird.

## <a name="header-based-sso"></a>Headerbasiertes einmaliges Anmelden

Das headerbasierte einmalige Anmelden funktioniert für Anwendungen, die für die Authentifizierung HTTP-Header verwenden.

Wählen Sie headerbasiertes einmaliges Anmelden aus, wenn ein Anwendungsproxy für die lokale Anwendung konfiguriert ist.

Weitere Informationen zur headerbasierten Authentifizierung finden Sie unter [Headerbasiertes einmaliges Anmelden für lokale Apps mit Azure AD-App-Proxy (Vorschau)](application-proxy-configure-single-sign-on-with-headers.md).


## <a name="next-steps"></a>Nächste Schritte
* [Schnellstartserie zur Anwendungsverwaltung](view-applications-portal.md)
* [Planen einer Bereitstellung für einmaliges Anmelden](plan-sso-deployment.md)
* [Einmaliges Anmelden mit lokalen Apps](application-proxy-config-sso-how-to.md)