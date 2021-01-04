---
title: Häufig gestellte Fragen zum Azure Active Directory-Anwendungsproxy
description: Hier finden Sie Antworten auf häufig gestellte Fragen (Frequently Asked Questions, FAQ) zur Verwendung des Azure AD-Anwendungsproxys zum Veröffentlichen interner, lokaler Anwendungen für Remotebenutzer.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 07/23/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperf-fy21q2
ms.openlocfilehash: c40a0cb362f8c43541194cd1ca0c57abbf8fa974
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033101"
---
# <a name="active-directory-azure-ad-application-proxy-frequently-asked-questions"></a>Häufig gestellte Fragen zum Azure Active Directory-Anwendungsproxy (Azure AD-Anwendungsproxy)

Auf dieser Seite werden häufig gestellte Fragen zum Anwendungsproxy von Azure Active Directory (Azure AD) beantwortet.

## <a name="enabling-azure-ad-application-proxy"></a>Aktivieren des Azure AD-Anwendungsproxys

### <a name="what-license-is-required-to-use-azure-ad-application-proxy"></a>Welche Lizenz ist für die Verwendung des Azure AD-Anwendungsproxys erforderlich?

Um den Azure AD-Anwendungsproxy verwenden zu können, müssen Sie über eine Azure AD Premium P1- oder P2-Lizenz verfügen. Weitere Informationen zur Lizenzierung finden Sie unter [Azure Active Directory – Preise](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="what-happens-to-azure-ad-application-proxy-in-my-tenant-if-my-license-expires"></a>Was geschieht mit dem Azure AD-Anwendungsproxy in meinem Mandanten, wenn meine Lizenz abläuft?
Wenn Ihre Lizenz abgelaufen ist, wird der Anwendungsproxy automatisch deaktiviert. Ihre Anwendungsinformationen werden bis zu einem Jahr gespeichert.

### <a name="why-is-the-enable-application-proxy-button-grayed-out"></a>Warum ist die Schaltfläche „Anwendungsproxy aktivieren“ abgeblendet?

Stellen Sie sicher, dass Sie über mindestens eine Azure AD Premium P1- oder P2-Lizenz verfügen und ein Azure AD-Anwendungsproxyconnector installiert ist. Nachdem Sie den ersten Connector erfolgreich installiert haben, wird der Azure AD-Anwendungsproxydienst automatisch aktiviert.

## <a name="connector-configuration"></a>Connector-Konfiguration

### <a name="can-application-proxy-connector-services-run-in-a-different-user-context-than-the-default"></a>Können Anwendungsproxyconnector-Dienste in einem anderen als dem Standardbenutzerkontext ausgeführt werden?

Nein, dieses Szenario wird nicht unterstützt. Die Standardeinstellungen sind folgende:

- Microsoft AAD-Anwendungsproxyconnector – WAPCSvc – Netzwerkdienst
- Microsoft AAD Application Proxy Connector Updater – WAPCUpdaterSvc – NT-Autorität\System

### <a name="can-a-guest-user-with-the-global-administrator-or-the-application-administrator-role-register-the-connector-for-the-guest-tenant"></a>Kann ein Gastbenutzer mit der Rolle „Globaler Administrator“ oder „Anwendungsadministrator“ den Connector für den (Gast-)Mandanten registrieren?

Nein, dies ist derzeit nicht möglich. Der Registrierungsversuch erfolgt immer beim Basismandanten des Benutzers.

### <a name="my-back-end-application-is-hosted-on-multiple-web-servers-and-requires-user-session-persistence-stickiness-how-can-i-achieve-session-persistence"></a>Meine Back-End-Anwendung wird auf mehreren Webservern gehostet und erfordert Benutzersitzungspersistenz (Stickiness). Wie kann ich Sitzungspersistenz erreichen? 

Empfehlungen finden Sie unter [Hochverfügbarkeit und Lastenausgleich von Anwendungsproxy-Connectors und -Anwendungen](application-proxy-high-availability-load-balancing.md).

### <a name="is-tls-termination-tlshttps-inspection-or-acceleration-on-traffic-from-the-connector-servers-to-azure-supported"></a>Wird die TLS-Terminierung (TLS/HTTPS-Prüfung oder -Beschleunigung) für Datenverkehr von den Connectorservern zu Azure unterstützt?

Der Anwendungsproxyconnector führt eine zertifikatbasierte Authentifizierung bei Azure durch. Die TLS-Terminierung (TLS/HTTPS-Prüfung oder -Beschleunigung) unterbricht diese Authentifizierungsmethode und wird nicht unterstützt. Der Datenverkehr vom Connector zu Azure muss alle Geräte umgehen, die eine TLS-Terminierung ausführen.  

### <a name="is-tls-12-required-for-all-connections"></a>Ist TLS 1.2 für alle Verbindungen erforderlich?
Ja. Zur Bereitstellung erstklassiger Verschlüsselung für unsere Kunden beschränkt der Anwendungsproxydienst den Zugriff ausschließlich auf TLS 1.2-Protokolle. Diese Änderungen wurden nach und nach bereitgestellt und sind seit dem 31. August 2019 wirksam. Vergewissern Sie sich, dass alle Kombinationen aus Client/Server und Browser/Server zur Verwendung von TLS 1.2 aktualisiert wurden, um die Verbindung mit dem Anwendungsproxydienst sicherzustellen. Dazu gehören Clients, mit denen Ihre Benutzer auf Anwendungen zugreifen, die über den Anwendungsproxy veröffentlicht werden. Nützliche Referenzen und Ressourcen finden Sie unter [Vorbereiten der Verwendung von TLS 1.2 in Office 365](/microsoft-365/compliance/prepare-tls-1.2-in-office-365).

### <a name="can-i-place-a-forward-proxy-device-between-the-connector-servers-and-the-back-end-application-server"></a>Kann ich ein Weiterleitungsproxygerät zwischen den Connectorservern und dem Back-End-Anwendungsserver platzieren?
Ja, dieses Szenario wird ab Connectorversion 1.5.1526.0 unterstützt. Weitere Informationen finden Sie unter [Verwenden von vorhandenen lokalen Proxyservern](application-proxy-configure-connectors-with-proxy-servers.md).

### <a name="should-i-create-a-dedicated-account-to-register-the-connector-with-azure-ad-application-proxy"></a>Sollte ich ein dediziertes Konto erstellen, um den Connector beim Azure AD-Anwendungsproxy zu registrieren?

Dafür gibt es keinen Grund. Dafür kann ein globales Administrator- oder Anwendungsadministratorkonto verwendet werden. Die bei der Installation eingegebenen Anmeldeinformationen werden nach dem Registrierungsprozess nicht verwendet. Stattdessen wird ein Zertifikat für den Connector ausgestellt, das ab diesem Zeitpunkt für die Authentifizierung verwendet wird.

### <a name="how-can-i-monitor-the-performance-of-the-azure-ad-application-proxy-connector"></a>Wie kann ich die Leistung des Azure AD-Anwendungsproxyconnectors überwachen?

Es sind Leistungsindikatoren der Leistungsüberwachung verfügbar, die zusammen mit dem Connector installiert werden. Gehen Sie wie folgt vor, um diese anzuzeigen:  

1. Wählen Sie **Start** aus, geben Sie „Perfmon“ ein, und drücken Sie die EINGABETASTE.
2. Wählen Sie **Leistungsüberwachung** aus, und klicken Sie dann auf das grüne Symbol **+** .
3. Fügen Sie die Leistungsindikatoren von **Microsoft AAD-Anwendungsproxyconnector** hinzu, die Sie überwachen möchten.

### <a name="does-the-azure-ad-application-proxy-connector-have-to-be-on-the-same-subnet-as-the-resource"></a>Muss sich der Azure AD-Anwendungsproxyconnector in demselben Subnetz befinden wie die Ressource?

Der Connector muss sich nicht in demselben Subnetz befinden. Er benötigt jedoch eine Namensauflösung (DNS, Hostdatei) für die Ressource und die erforderliche Netzwerkkonnektivität (Routing an die Ressource, für die Ressource geöffnete Ports usw.). Empfehlungen finden Sie unter [Aspekte der Netzwerktopologie bei Verwendung des Azure Active Directory-Anwendungsproxys](application-proxy-network-topology.md).

### <a name="what-versions-of-windows-server-can-i-install-a-connector-on"></a>Auf welchen Windows Server-Versionen kann ich einen Connector installieren?
Für diesen Anwendungsproxy ist Windows Server 2012 R2 oder höher erforderlich. Derzeit gibt es eine Einschränkung bei HTTP2 für Windows Server 2019. Damit der Connector unter Windows Server 2019 erfolgreich verwendet werden kann, müssen Sie den folgenden Registrierungsschlüssel hinzufügen und den Server neu starten:
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp\EnableDefaultHttp2 (DWORD) Value: 0 
    ```

## <a name="application-configuration"></a>Anwendungskonfiguration

### <a name="i-am-receiving-an-error-about-an-invalid-certificate-or-possible-wrong-password"></a>Ich erhalte einen Fehler für ein ungültiges Zertifikat oder möglicherweise falsches Kennwort

Nachdem Sie das SSL-Zertifikat hochgeladen haben, erhalten Sie im Portal die Meldung „Invalid certificate, possible wrong password“ (Ungültiges Zertifikat, möglicherweise falsches Kennwort).

So können Sie diesen Fehler z. B. beheben:
- Überprüfen Sie, ob Probleme mit dem Zertifikat vorliegen. Installieren Sie es auf Ihrem lokalen Computer. Wenn keine weiteren Probleme auftreten, ist das Zertifikat in Ordnung.
- Stellen Sie sicher, dass das Kennwort keine Sonderzeichen enthält. Für Tests sollte das Kennwort nur die Zeichen 0–9, A–Z und a–z enthalten.
- Wenn das Zertifikat mit Microsoft Software Key Storage Provider erstellt wurde, muss der RSA-Algorithmus verwendet werden.

### <a name="what-is-the-length-of-the-default-and-long-back-end-timeout-can-the-timeout-be-extended"></a>Wie lauten die Werte für das Standard- und das „lange“ Back-End-Timeout? Kann das Timeoutlimit verlängert werden?

Die Standarddauer beträgt 85 Sekunden. Die „lange“ Einstellung beträgt 180 Sekunden. Das Timeoutlimit kann nicht verlängert werden.

### <a name="how-do-i-change-the-landing-page-my-application-loads"></a>Wie ändere ich die Landing Page, die meine Anwendung lädt?

Auf der Seite „Anwendungsregistrierungen“ können Sie die Homepage-URL in die gewünschte externe URL der Landing Page ändern. Die angegebene Seite wird geladen, wenn die Anwendung über „Meine Apps“ oder das Office 365-Portal gestartet wird. Die Konfigurationsschritte finden Sie unter [Festlegen einer benutzerdefinierten Startseite für veröffentlichte Apps mithilfe eines Azure AD-Anwendungsproxys](./application-proxy-configure-custom-home-page.md).

### <a name="can-only-iis-based-applications-be-published-what-about-web-applications-running-on-non-windows-web-servers-does-the-connector-have-to-be-installed-on-a-server-with-iis-installed"></a>Können nur IIS-basierte Anwendungen veröffentlicht werden? Wie sieht es mit Webanwendungen aus, die auf Nicht-Windows-Webservern ausgeführt werden? Muss der Connector auf einem Server installiert sein, auf dem IIS installiert ist?

Nein, es gibt keine IIS-Anforderung für Anwendungen, die veröffentlicht werden. Sie können Webanwendungen veröffentlichen, die auf Servern ausgeführt werden, die nicht auf Windows Server basieren. Abhängig davon, ob der Webserver die Aushandlung (Kerberos-Authentifizierung) unterstützt, können Sie jedoch möglicherweise keine Vorauthentifizierung bei einem Nicht-Windows-Server verwenden. IIS ist nicht auf dem Server erforderlich, auf dem der Connector installiert ist.

### <a name="can-i-configure-application-proxy-to-add-the-hsts-header"></a>Kann ich den Anwendungsproxy so konfigurieren, dass der HSTS-Header hinzugefügt wird?
Der Anwendungsproxy fügt HTTPS-Antworten nicht automatisch den HTTP Strict-Transport-Security-Header hinzu, behält jedoch den Header bei, wenn er sich in der ursprünglichen Antwort befindet, die von der veröffentlichten Anwendung gesendet wurde. Die Suche nach einer Einstellung zur Aktivierung dieser Funktion ist in Planung. Wenn Sie an einer Vorschau interessiert sind, die das Hinzufügen des HSTS-Headers zu Antworten ermöglicht, wenden Sie sich an aadapfeedback@microsoft.com, um weitere Informationen zu erhalten.

## <a name="integrated-windows-authentication"></a>Integrierte Windows-Authentifizierung

### <a name="when-should-i-use-the-principalsallowedtodelegatetoaccount-method-when-setting-up-kerberos-constrained-delegation-kcd"></a>In welchen Fällen sollte ich die „PrincipalsAllowedToDelegateToAccount“-Methode beim Einrichten der eingeschränkten Kerberos-Delegierung (Kerberos Constrained Delegation, KCD) verwenden?

Die „PrincipalsAllowedToDelegateToAccount“-Methode wird verwendet, wenn sich Connectorserver in einer anderen Domäne befinden als das Webanwendungsdienstkonto. Dafür muss die ressourcenbasierte eingeschränkte Delegierung verwendet werden.
Wenn sich die Connectorserver und das Webanwendungsdienstkonto in derselben Domäne befinden, können Sie „Active Directory-Benutzer und -Computer“ verwenden, um die Delegierungseinstellungen für die einzelnen Connectorcomputerkonten zu konfigurieren, sodass sie an den Ziel-SPN delegiert werden können.

Wenn sich die Connectorserver und das Webanwendungsdienstkonto in unterschiedlichen Domänen befinden, wird die ressourcenbasierte Delegierung verwendet. Die Delegierungsberechtigungen werden für den Zielwebserver und das Webanwendungsdienstkonto konfiguriert. Diese Methode der eingeschränkten Delegierung ist relativ neu. Die Methode wurde in Windows Server 2012 eingeführt. Dieses Betriebssystem unterstützt die domänenübergreifende Delegierung, indem der Besitzer der Ressource (des Webdiensts) steuern kann, welche Computer- und Dienstkonten die Delegierung ausführen können. Es gibt keine Benutzeroberfläche, die Sie bei dieser Konfiguration unterstützt, daher müssen Sie PowerShell verwenden.
Weitere Informationen finden Sie im Whitepaper [Grundlegendes zur eingeschränkten Kerberos-Delegierung mit dem Anwendungsproxy](https://aka.ms/kcdpaper).

### <a name="does-ntlm-authentication-work-with-azure-ad-application-proxy"></a>Funktioniert die NTLM-Authentifizierung mit dem Azure AD-Anwendungsproxy?

Die NTLM-Authentifizierung kann nicht als Vorauthentifizierung oder SSO-Methode verwendet werden. Die NTLM-Authentifizierung kann nur verwendet werden, wenn sie direkt zwischen dem Client und der veröffentlichten Webanwendung ausgehandelt werden kann. Bei Verwendung der NTLM-Authentifizierung wird in der Regel eine Anmeldeaufforderung im Browser angezeigt.

### <a name="can-i-use-the-logon-identity-on-premises-user-principal-name-or-on-premises-sam-account-name-in-a-b2b-iwa-single-sign-on-scenario"></a>Kann die Anmeldeidentität „Lokaler Benutzerprinzipalname“ oder „Name des lokalen SAM-Kontos“ in einem B2B-IWA-Szenario mit einmaligem Anmelden verwendet werden?

Nein, dies funktioniert nicht, da ein Gastbenutzer in Azure AD nicht über das Attribut verfügt, das von den oben erwähnten Anmeldeidentitäten benötigt wird.

In diesem Fall wird ein Fallback auf „Benutzerprinzipalname“ durchgeführt. Weitere Details zum B2B-Szenario finden Sie unter [Gewähren des Zugriffs auf lokale Anwendungen für B2B-Benutzer in Azure AD](../external-identities/hybrid-cloud-to-on-premises.md).

## <a name="pass-through-authentication"></a>Passthrough-Authentifizierung

### <a name="can-i-use-conditional-access-policies-for-applications-published-with-pass-through-authentication"></a>Kann ich Richtlinien für bedingten Zugriff für Anwendungen verwenden, die mit Passthrough-Authentifizierung veröffentlicht werden?

Richtlinien für bedingten Zugriff werden nur für erfolgreich vorauthentifizierte Benutzer in Azure AD erzwungen. Die Passthrough-Authentifizierung löst keine Azure AD-Authentifizierung aus. Daher können keine Richtlinien für bedingten Zugriff erzwungen werden. Bei der Passthrough-Authentifizierung müssen MFA-Richtlinien auf dem lokalen Server implementiert werden (sofern möglich) oder durch Aktivieren der Vorauthentifizierung mit dem Azure AD-Anwendungsproxy.

### <a name="can-i-publish-a-web-application-with-client-certificate-authentication-requirement"></a>Kann ich eine Webanwendung mit Clientzertifikat-Authentifizierungsanforderung veröffentlichen?

Nein, dieses Szenario wird nicht unterstützt, weil der Anwendungsproxy den TLS-Datenverkehr beendet.  

## <a name="remote-desktop-gateway-publishing"></a>Remotedesktopgateway-Veröffentlichung

### <a name="how-can-i-publish-remote-desktop-gateway-over-azure-ad-application-proxy"></a>Wie kann ich Remotedesktopgateway über den Azure AD-Anwendungsproxy veröffentlichen?

Entsprechende Informationen finden Sie unter [Veröffentlichen des Remotedesktops per Azure AD-Anwendungsproxy](application-proxy-integrate-with-remote-desktop-services.md).

### <a name="can-i-use-kerberos-constrained-delegation-single-sign-on---windows-integrated-authentication-in-the-remote-desktop-gateway-publishing-scenario"></a>Kann ich die eingeschränkte Kerberos-Delegierung (Einmaliges Anmelden – Windows Integrated Authentication) im Szenario der Remotedesktopgateway-Veröffentlichung verwenden?

Nein, dieses Szenario wird nicht unterstützt.  

### <a name="my-users-dont-use-internet-explorer-11-and-the-pre-authentication-scenario-doesnt-work-for-them-is-this-expected"></a>Meine Benutzer verwenden Internet Explorer 11 nicht, und das Szenario der Vorauthentifizierung funktioniert bei ihnen nicht. Entspricht dies dem erwarteten Verhalten?

Ja, das entspricht dem erwarteten Verhalten. Für das Szenario der Vorauthentifizierung ist ein ActiveX-Steuerelement erforderlich, das in Browsern von Drittanbietern nicht unterstützt wird.

### <a name="is-the-remote-desktop-web-client-html5-supported"></a>Wird der Remotedesktop-Webclient (HTML5) unterstützt?

Ja, dieses Szenario befindet sich derzeit in der öffentlichen Vorschau. Entsprechende Informationen finden Sie unter [Veröffentlichen des Remotedesktops per Azure AD-Anwendungsproxy](application-proxy-integrate-with-remote-desktop-services.md).

### <a name="after-i-configured-the-pre-authentication-scenario-i-realized-that-the-user-has-to-authenticate-twice-first-on-the-azure-ad-sign-in-form-and-then-on-the-rdweb-sign-in-form-is-this-expected-how-can-i-reduce-this-to-one-sign-in"></a>Nach der Konfiguration des Szenarios der Vorauthentifizierung musste ich feststellen, dass sich der Benutzer zweimal authentifizieren muss: zuerst auf dem Azure AD-Anmeldeformular und dann auf dem RDWeb-Anmeldeformular. Entspricht dies dem erwarteten Verhalten? Wie kann ich das auf eine Anmeldung reduzieren?

Ja, das entspricht dem erwarteten Verhalten. Wenn der Computer des Benutzers in Azure AD eingebunden ist, meldet sich der Benutzer automatisch bei Azure AD an. Der Benutzer muss seine Anmeldeinformationen nur auf dem RDWeb-Anmeldeformular angeben.

## <a name="sharepoint-publishing"></a>SharePoint-Veröffentlichung

### <a name="how-can-i-publish-sharepoint-over-azure-ad-application-proxy"></a>Wie kann ich SharePoint über den Azure AD-Anwendungsproxy veröffentlichen?

Entsprechende Informationen finden Sie unter [Aktivieren des Remotezugriffs auf SharePoint per Azure AD-Anwendungsproxy](application-proxy-integrate-with-sharepoint-server.md).

### <a name="can-i-use-the-sharepoint-mobile-app-ios-android-to-access-a-published-sharepoint-server"></a>Kann ich die mobile SharePoint-App (iOS/Android) verwenden, um auf einen veröffentlichten SharePoint-Server zuzugreifen?

Die [mobile SharePoint-App](/sharepoint/administration/supporting-the-sharepoint-mobile-apps-online-and-on-premises) unterstützt derzeit keine Azure Active Directory-Vorauthentifizierung.

## <a name="active-directory-federation-services-ad-fs-publishing"></a>Veröffentlichung von Active Directory-Verbunddienste (AD FS) 

### <a name="can-i-use-azure-ad-application-proxy-as-ad-fs-proxy-like-web-application-proxy"></a>Kann ich den Azure AD-Anwendungsproxy als AD FS-Proxy (wie den Webanwendungsproxy) verwenden?

Nein. Der Azure AD-Anwendungsproxy ist für die Arbeit mit Azure AD konzipiert und erfüllt nicht die Anforderungen, um als AD FS-Proxy fungieren zu können.

## <a name="websocket"></a>WebSocket

### <a name="does-websocket-support-work-for-applications-other-than-qliksense"></a>Funktioniert die WebSocket-Unterstützung für andere Anwendungen als QlikSense?

Derzeit befindet sich die WebSocket-Protokollunterstützung noch in der öffentlichen Vorschauphase und funktioniert möglicherweise nicht für andere Anwendungen. Einige Kunden verzeichneten einen gemischten Erfolg beim Verwenden des WebSocket-Protokolls mit anderen Anwendungen. Wenn Sie solche Szenarien testen, würden wir uns über Feedback zu Ihren Ergebnissen freuen. Senden Sie uns Ihr Feedback an aadapfeedback@microsoft.com.

Features (Ereignisprotokolle, PowerShell und Remotedesktopdienste) im Windows Admin Center (WAC) oder Remotedesktop-Webclient (HTML5) funktionieren derzeit nicht über den Azure AD-Anwendungsproxy.

## <a name="link-translation"></a>Linkübersetzung

### <a name="does-using-link-translation-affect-performance"></a>Wirkt sich die Verwendung der Linkübersetzung auf die Leistung aus?

Ja. Die Verwendung der Linkübersetzung wirkt sich auf die Leistung aus. Der Anwendungsproxydienst überprüft die Anwendung auf hartcodierte Links und ersetzt diese durch die entsprechenden veröffentlichten externen URLs, bevor sie dem Benutzer angezeigt werden. 

Um eine optimale Leistung zu erzielen, empfiehlt sich die Verwendung von identischen internen und externen URLs durch die Konfiguration [benutzerdefinierter Domänen](./application-proxy-configure-custom-domain.md). Wenn die Verwendung benutzerdefinierter Domänen nicht möglich ist, können Sie die Leistung der Linkübersetzung durch Verwenden der Erweiterung zur sicheren Anmeldung bei „Meine Apps“ oder des Microsoft Edge-Browsers auf mobilen Geräten verbessern. Weitere Informationen finden Sie unter [Umleiten von hartcodierten Links für Apps, die mit Azure AD-Anwendungsproxy veröffentlicht wurden](application-proxy-configure-hard-coded-link-translation.md).

## <a name="wildcards"></a>Platzhalter

### <a name="how-do-i-use-wildcards-to-publish-two-applications-with-the-same-custom-domain-name-but-with-different-protocols-one-for-http-and-one-for-https"></a>Wie kann ich mithilfe von Platzhaltern zwei Anwendungen mit demselben benutzerdefinierten Domänennamen, aber mit unterschiedlichen Protokollen – für HTTP und für HTTPS – veröffentlichen?

Dieses Szenario wird nicht direkt unterstützt. Sie haben bei diesem Szenario die folgenden Möglichkeiten:

1. Veröffentlichen Sie die HTTP-URL und die HTTPS-URL als separate Anwendungen mit einem Platzhalter, aber weisen Sie diesen jeweils eine andere benutzerdefinierte Domäne zu. Diese Konfiguration funktioniert, weil unterschiedliche externe URLs verwendet werden.

2. Veröffentlichen Sie die HTTPS-URL über eine Platzhalteranwendung. Veröffentlichen Sie die HTTP-Anwendungen separat mithilfe der folgenden PowerShell-Cmdlets für den Anwendungsproxy:
   - [Verwaltung von Anwendungsproxyanwendungen](/powershell/module/azuread/#application_proxy_application_management&preserve-view=true)
   - [Verwaltung von Anwendungsproxyconnectors](/powershell/module/azuread/#application_proxy_connector_management&preserve-view=true)
