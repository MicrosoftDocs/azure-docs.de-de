---
title: Hinzufügen einer lokalen App – Anwendungsproxy in Azure Active Directory | Microsoft-Dokumentation
description: Mit einem Anwendungsproxydienst in Azure Active Directory (Azure AD) können Benutzer auf lokale Anwendungen zugreifen, indem sie sich mit ihrem Azure AD-Konto anmelden. In diesem Tutorial wird veranschaulicht, wie Sie Ihre Umgebung für die Nutzung mit einem Anwendungsproxy vorbereiten. Anschließend wird das Azure-Portal verwendet, um Ihrem Azure AD-Mandanten eine lokale Anwendung hinzuzufügen.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82c7b698f655b82ba95f66127f27a921def02cde
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66472994"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Tutorial: Hinzufügen einer lokalen Anwendung für den Remotezugriff über den Anwendungsproxy in Azure Active Directory

Mit einem Anwendungsproxydienst in Azure Active Directory (Azure AD) können Benutzer auf lokale Anwendungen zugreifen, indem sie sich mit ihrem Azure AD-Konto anmelden. In diesem Tutorial wird Ihre Umgebung auf die Verwendung des Anwendungsproxys vorbereitet. Wenn Ihre Umgebung bereit ist, fügen Sie im Azure-Portal Ihrem Azure AD-Mandanten eine lokale Anwendung hinzu.

In diesem Tutorial wird Folgendes durchgeführt:

> [!div class="checklist"]
> * Öffnen von Ports für ausgehenden Datenverkehr und Zulassen des Zugriffs auf bestimmte URLs
> * Installieren des Connectors auf Ihrem Windows-Server und Registrierung mit dem Anwendungsproxy
> * Überprüfen der ordnungsgemäßen Installation und Registrierung des Connectors
> * Hinzufügen einer lokalen Anwendung zum Azure AD-Mandanten
> * Überprüfen, ob ein Testbenutzer sich über das Azure AD-Konto an der Anwendung anmelden kann

## <a name="before-you-begin"></a>Voraussetzungen

Zum Hinzufügen einer lokalen Anwendung zu Azure AD benötigen Sie Folgendes:

* Ein [Microsoft Azure AD-Abonnement vom Typ „Basic“ oder „Premium“](https://azure.microsoft.com/pricing/details/active-directory)
* Ein Konto als Anwendungsadministrator
* Benutzeridentitäten müssen aus einem lokalen Verzeichnis synchronisiert oder direkt in Ihren Azure AD-Mandanten erstellt werden. Mithilfe der Identitätssynchronisierung können Benutzer von Azure AD vorab authentifiziert werden, bevor ihnen Zugriff auf per Anwendungsproxy veröffentlichte Anwendungen gewährt wird. Außerdem können die benötigten Benutzer-ID-Informationen für einmaliges Anmelden (Single Sign-On, SSO) bereitgestellt werden.

### <a name="windows-server"></a>Windows-Server

Für die Verwendung des Anwendungsproxys benötigen Sie einen Windows-Server, auf dem Windows Server 2012 R2 oder höher ausgeführt wird. Sie müssen den Connector für den Anwendungsproxy auf dem Server installieren. Dieser Connectorserver muss eine Verbindung mit den Anwendungsproxydiensten in Azure sowie mit den lokalen Anwendungen herstellen können, die Sie veröffentlichen möchten.

Für hohe Verfügbarkeit in Ihrer Produktionsumgebung sollten Sie mehrere Windows-Server einsetzen. Für dieses Tutorial reicht ein Windows-Server aus.

#### <a name="recommendations-for-the-connector-server"></a>Empfehlungen für den Connectorserver

1. Platzieren Sie den Connectorserver physisch in der Nähe des Anwendungsservers, um die Leistung zwischen Connector und Anwendung zu optimieren. Weitere Informationen finden Sie unter [Aspekte der Netzwerktopologie bei Verwendung des Azure Active Directory-Anwendungsproxys](application-proxy-network-topology.md).

2. Der Connectorserver und die Webanwendungsserver müssen derselben Active Directory-Domäne angehören bzw. vertrauenswürdige Domänen umfassen. Dass die Server zu derselben Domäne oder zu vertrauenswürdigen Domänen gehören, ist eine Voraussetzung für die Verwendung des einmaligen Anmeldens (Single Sign-On, SSO) mit integrierter Windows-Authentifizierung (IWA) und eingeschränkter Kerberos-Delegierung (Kerberos Constrained Delegation, KCD). Wenn der Connector- und Webanwendungsserver sich in unterschiedlichen Active Directory-Domänen befinden, müssen Sie die ressourcenbasierte Delegierung für einmaliges Anmelden verwenden. Weitere Informationen finden Sie unter [Eingeschränkte Kerberos-Delegierung für das einmalige Anmelden mit Anwendungsproxy](application-proxy-configure-single-sign-on-with-kcd.md).

#### <a name="tls-requirements"></a>TLS-Anforderungen

Auf dem Windows-Connectorserver muss TLS 1.2 aktiviert werden, bevor Sie den Anwendungsproxyconnector installieren.

So aktivieren Sie TLS 1.2

1. Legen Sie die folgenden Registrierungsschlüssel fest:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

2. Starten Sie den Server neu.

>[!Important] 
> Zur Bereitstellung erstklassiger Verschlüsselung für unsere Kunden wird der Anwendungsproxydienst aktualisiert, um den Zugriff ausschließlich auf TLS 1.2-Protokolle zu beschränken. Basierend auf der Kundenbereitschaft werden die Änderungen nach und nach für Kunden bereitgestellt, die ausschließlich TLS 1.2-Protokolle verwenden. Für die Kunden ist keinerlei Beeinträchtigung spürbar. TLS 1.0 und 1.1 werden am 31. August 2019 eingestellt, und Kunden erhalten eine Vorankündigung, um sich auf diese Änderung vorbereiten zu können. Vergewissern Sie sich bei der Vorbereitung auf diese Änderung, dass alle Kombinationen aus Client/Server und Browser/Server zur Verwendung von TLS 1.2 aktualisiert wurden, um die Verbindung mit dem Anwendungsproxydienst sicherzustellen. Dazu gehören Clients, mit denen Ihre Benutzer auf Anwendungen zugreifen, die über den Anwendungsproxy veröffentlicht werden. Nützliche Referenzen und Ressourcen finden Sie unter [Vorbereiten der Verwendung von TLS 1.2 in Office 365](https://support.microsoft.com/help/4057306/preparing-for-tls-1-2-in-office-365).

## <a name="prepare-your-on-premises-environment"></a>Vorbereiten Ihrer lokalen Umgebung

Ermöglichen Sie zuerst die Kommunikation mit Azure-Rechenzentren, um Ihre Umgebung für den Azure AD-Anwendungsproxy vorzubereiten. Falls der Pfad eine Firewall enthält, sollten Sie sicherstellen, dass der Zugang geöffnet ist. Bei einer geöffneten Firewall kann der Connector HTTPS-Anforderungen (TCP) an den Anwendungsproxy senden.

### <a name="open-ports"></a>Öffnen von Ports

Öffnen Sie die folgenden Ports für den **ausgehenden** Datenverkehr. 

   | Portnummer | Wie diese verwendet wird |
   | --- | --- |
   | 80 | Herunterladen der Zertifikatssperrlisten (CRL) bei der Überprüfung des SSL-Zertifikats |
   | 443 | Gesamte ausgehende Kommunikation mit dem Webanwendungsproxy-Dienst |

Wenn Ihre Firewall Datenverkehr gemäß Ursprungsbenutzern erzwingt, öffnen Sie auch die Ports 80 und 443 für den Datenverkehr aus Windows-Diensten, die als Netzwerkdienst ausgeführt werden.

Wenn Sie den Anwendungsproxy bereits verwenden, haben Sie unter Umständen eine ältere Version des Connectors installiert. Installieren Sie mithilfe dieses Tutorials die neueste Version des Connectors. Ältere Versionen als 1.5.132.0 erfordern auch die folgenden offenen Ports: 5671, 8080, 9090-9091, 9350, 9352, 10100–10120. 

### <a name="allow-access-to-urls"></a>Zulassen des Zugriffs auf URLs

Lassen Sie den Zugriff auf die folgenden URLs zu:

| URL | Wie diese verwendet wird |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Kommunikation zwischen dem Connector und dem Anwendungsproxy-Clouddienst |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Azure verwendet diese URLs, um Zertifikate zu überprüfen. |
| login.windows.net<br>login.microsoftonline.com<br>secure.aadcdn.microsoftonline-p.com  | Der Connector verwendet diese URLs während der Registrierung. |

Sie können Verbindungen mit „\*.msappproxy.net“ und „\*.servicebus.windows.net“ zulassen, wenn für Ihre Firewall oder Ihren Proxy die Konfiguration von DNS-Zulassungslisten möglich ist. Andernfalls müssen Sie den Zugriff auf die [IP-Adressbereiche für das Azure-Rechenzentrum](https://www.microsoft.com/download/details.aspx?id=41653) zulassen. Die IP-Adressbereiche werden wöchentlich aktualisiert.

## <a name="install-and-register-a-connector"></a>Installieren und Registrieren eines Connectors

Installieren Sie für die Nutzung des Anwendungsproxys einen Connector auf jedem Windows-Server, den Sie mit dem Anwendungsproxydienst verwenden. Der Connector ist ein Agent, der die aus dem lokalen Anwendungsserver ausgehende Verbindung mit dem Anwendungsproxy in Azure AD verwaltet. Sie können einen Connector auf Servern installieren, auf denen auch andere Authentifizierungs-Agents wie Azure AD Connect installiert sind.

So installieren Sie den Connector:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als Anwendungsadministrator des Verzeichnisses an, das den Anwendungsproxy verwendet. Wenn die Mandantendomäne also beispielsweise „contoso.com“ lautet, muss sich der Administrator als admin@contoso.com oder mit einem anderen Administratoraliasnamen in dieser Domäne anmelden.
2. Wählen Sie rechts oben Ihren Benutzernamen aus. Stellen Sie sicher, dass Sie an einem Verzeichnis angemeldet sind, für das der Anwendungsproxy verwendet wird. Wählen Sie **Verzeichnis wechseln**, und wählen Sie ein Verzeichnis aus, für das der Anwendungsproxy verwendet wird, falls Sie das Verzeichnis wechseln möchten.
3. Wählen Sie im Navigationsbereich auf der linken Seite die Option **Azure Active Directory**. 
4. Wählen Sie unter **Verwalten** die Option **Anwendungsproxy**.
5. Wählen Sie **Connectordienst herunterladen**.
    
    ![Herunterladen eines Connectordiensts](./media/application-proxy-add-on-premises-application/application-proxy-download-connector-service.png)

6. Lesen Sie die Vertragsbedingungen.  Wählen Sie die Option **Bedingungen akzeptieren und herunterladen**, wenn Sie mit dem Lesen fertig sind.
7. Wählen Sie am unteren Rand des Fensters die Option **Ausführen**, um den Connector zu installieren. Ein Installations-Assistent wird geöffnet. 
8. Befolgen Sie im Assistenten die Anleitung zum Installieren des Diensts. Wenn Sie aufgefordert werden, den Connector mit dem Anwendungsproxy für Ihren Azure AD-Mandanten zu registrieren, geben Sie Ihre Administratoranmeldeinformationen für die Anwendung ein.
    - Falls beim Internet Explorer (IE) **Verstärkte Sicherheitskonfiguration für IE** auf **Ein** festgelegt ist, wird der Registrierungsbildschirm unter Umständen nicht anzeigt. Befolgen Sie die Anweisungen in der Fehlermeldung, um Zugriff zu erhalten. Stellen Sie sicher, dass **Verstärkte Sicherheitskonfiguration für Internet Explorer** auf **Aus** festgelegt ist.

### <a name="general-remarks"></a>Allgemeine Hinweise

Wenn Sie bereits einen Connector installiert haben, installieren Sie ihn neu, um die neueste Version zu erhalten. Informationen zu zuvor veröffentlichten Versionen und den damit verbundenen Änderungen finden Sie unter [Azure AD-Anwendungsproxy: Verlauf der Versionsveröffentlichungen](application-proxy-release-version-history.md).

Wenn Sie mehrere Windows-Server für Ihre lokalen Anwendungen einsetzen möchten, müssen Sie den Connector auf jedem Server installieren und registrieren. Sie können die Connectors in Connectorgruppen organisieren. Weitere Informationen finden Sie unter [Veröffentlichen von Anwendungen in getrennten Netzwerken und an getrennten Standorten mithilfe von Connectorgruppen](application-proxy-connector-groups.md). 

Wenn Ihre Organisation für Verbindungen mit dem Internet Proxyserver verwendet, müssen Sie diese für den Anwendungsproxy konfigurieren.  Weitere Informationen finden Sie unter [Verwenden von vorhandenen lokalen Proxyservern](application-proxy-configure-connectors-with-proxy-servers.md). 

Informationen zu Connectors, deren Aktualisierung und Kapazitätsplanung finden Sie unter [Grundlegendes zu Azure AD-Anwendungsproxyconnectors](application-proxy-connectors.md). 


## <a name="verify-the-connector-installed-and-registered-correctly"></a>Überprüfen der ordnungsgemäßen Installation und Registrierung des Connectors

Sie können mithilfe des Azure-Portals oder Ihres Windows-Servers sicherstellen, dass ein neuer Connector ordnungsgemäß installiert ist.

### <a name="verify-the-installation-through-azure-portal"></a>Überprüfen der Installation im Azure-Portal

So überprüfen Sie, ob der Connector ordnungsgemäß installiert und registriert ist:

1. Melden Sie sich bei Ihrem Mandantenverzeichnis im [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im Navigationsbereich auf der linken Seite die Option **Azure Active Directory** und dann im Abschnitt **Verwalten** die Option **Anwendungsproxy**. Alle Connectors und Connectorgruppen werden auf dieser Seite angezeigt. 
3. Wählen Sie einen Connector aus, um die Details zu überprüfen. Die Connectors sollten standardmäßig erweitert sein. Falls der gewünschte zu überprüfende Connector nicht erweitert ist, können Sie ihn erweitern, um die Details anzuzeigen. Eine aktive grüne Beschriftung gibt an, dass Ihr Connector eine Verbindung mit dem Dienst herstellen kann. Jedoch auch wenn die Beschriftung grün ist, könnte ein Netzwerkproblem immer noch den Nachrichtenempfang des Connectors blockieren. 

    ![Azure AD-Anwendungsproxyconnectors](./media/application-proxy-connectors/app-proxy-connectors.png)

Weitere Hilfe zur Installation eines Connectors finden Sie unter [Problem beim Installieren des Anwendungsproxy-Agent-Connectors](application-proxy-connector-installation-problem.md).

### <a name="verify-the-installation-through-your-windows-server"></a>Überprüfen der Installation über Ihren Windows-Server

So überprüfen Sie, ob der Connector ordnungsgemäß installiert und registriert ist:

1. Öffnen Sie den Windows-Dienst-Manager, indem Sie auf die **Windows**-Taste klicken und *services.msc* eingeben.
2. Überprüfen Sie, ob die folgenden beiden Dienste den Status **Wird ausgeführt** haben.
   - Die Konnektivität wird per **Microsoft AAD-Anwendungsproxyconnector** ermöglicht.
   - Die **Aktualisierung des Microsoft AAD-Anwendungsproxyconnectors** ist ein automatischer Aktualisierungsdienst. Der Dienst sucht nach neuen Versionen des Connectors und aktualisiert den Connector bei Bedarf.

     ![Anwendungsproxy-Connectordienste – Screenshot](./media/application-proxy-enable/app_proxy_services.png)

3. Wenn die Dienste nicht den Status **Wird ausgeführt** haben, müssen Sie mit der rechten Maustaste auf jeden Dienst klicken und die Option **Starten** wählen. 

## <a name="add-an-on-premises-app-to-azure-ad"></a>Hinzufügen einer lokalen App zu Azure AD

Nun haben Sie Ihre Umgebung vorbereitet, einen Connector installiert und sind bereit, Azure AD lokale Anwendungen hinzuzufügen.  

1. Melden Sie sich als Administrator beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie im Navigationsbereich auf der linken Seite die Option **Azure Active Directory**.
3. Wählen Sie **Unternehmensanwendungen** und dann **Neue Anwendung**.
4. Wählen Sie **Lokale Anwendung** aus.  
5. Geben Sie im Abschnitt **Fügen Sie Ihre eigene lokale Anwendung hinzu** die folgenden Informationen zu Ihrer Anwendung an:

    | Feld | BESCHREIBUNG |
    | :---- | :---------- |
    | **Name** | Der Name der Anwendung, der im Zugriffsbereich und im Azure-Portal angezeigt wird. |
    | **Interne URL** | Die URL zum Zugreifen auf die Anwendung innerhalb Ihres privaten Netzwerks. Sie können einen bestimmten Pfad auf dem Back-End-Server für die Veröffentlichung angeben, während der Rest des Servers nicht veröffentlicht wird. Auf diese Weise können Sie unterschiedliche Websites auf demselben Server als unterschiedliche Apps veröffentlichen und jeweils einen eigenen Namen und Zugriffsregeln vergeben.<br><br>Stellen Sie beim Veröffentlichen eines Pfads sicher, dass er alle erforderlichen Bilder, Skripts und Stylesheets für Ihre Anwendung enthält. Wenn sich die App beispielsweise unter „https:\//yourapp/app“ befindet und Bilder unter „https:\//yourapp/media“ genutzt werden, sollten Sie „https:\//yourapp/“ als Pfad veröffentlichen. Diese interne URL verfügt nicht über die Zielseite, die den Benutzern angezeigt wird. Weitere Informationen finden Sie unter [Festlegen einer benutzerdefinierten Startseite für veröffentlichte Apps](application-proxy-configure-custom-home-page.md). |
    | **Externe URL** | Die Adresse für den Benutzerzugriff auf die App von außerhalb Ihres Netzwerks. Wenn Sie die Anwendungsproxy-Standarddomäne nicht verwenden möchten, lesen Sie [Benutzerdefinierte Domänen im Azure AD-Anwendungsproxy](application-proxy-configure-custom-domain.md).|
    | **Vorauthentifizierung** | Gibt das Verfahren an, wie der Anwendungsproxy Benutzer überprüft, bevor diese Zugriff auf Ihre Anwendung erhalten.<br><br>**Azure Active Directory**: Der Anwendungsproxy leitet Benutzer an die Anmeldung mit Azure AD um. Hierbei werden deren Berechtigungen für das Verzeichnis und die Anwendung authentifiziert. Es wird empfohlen, diese Option als Standardwert aktiviert zu lassen, damit Sie Azure AD-Sicherheitsfunktionen wie bedingten Zugriff und Multi-Factor Authentication nutzen können. **Azure Active Directory** ist erforderlich, um die Anwendung mit Microsoft Cloud App Security zu überwachen.<br><br>**Passthrough**: Benutzer müssen sich nicht bei Azure AD authentifizieren, um Zugriff auf die Anwendung zu erhalten. Sie können weiterhin Authentifizierungsanforderungen auf dem Back-End einrichten. |
    | **Connectorgruppe** | Connectors verarbeiten den Remotezugriff auf Ihre Anwendung, und Connectorgruppen helfen Ihnen, Connectors und Apps nach Region, Netzwerk oder Zweck zu organisieren. Wenn Sie noch keine Connectorgruppen erstellt haben, wird Ihre App zu **Standard** zugewiesen.<br><br>Wenn Ihre Anwendung WebSockets verwendet, um eine Verbindung herzustellen, muss die Version aller Connectors in der Gruppe 1.5.612.0 oder höher sein.|

5. Konfigurieren Sie bei Bedarf die Einstellungen unter **Zusätzliche Einstellungen**. Für die meisten Anwendungen sollten Sie diese Einstellungen im Standardzustand belassen. 

    | Feld | BESCHREIBUNG |
    | :---- | :---------- |
    | **Timeout der Back-End-Anwendung** | Legen Sie diesen Wert nur auf **Lang** fest, wenn Ihre Anwendung bei der Authentifizierung und dem Verbinden langsam ist. |
    | **Nur-HTTP-Cookie verwenden** | Legen Sie diesen Wert auf **Ja** fest, damit Anwendungsproxycookies das Flag HTTPOnly im HTTP-Antwortheader enthalten. Wenn Sie Remotedesktopdienste verwenden, sollten Sie diese Option auf **Nein** festlegen.|
    | **Sicheres Cookie verwenden**| Legen Sie diesen Wert auf **Ja** fest, um Cookies über einen sicheren Kanal zu übertragen, z.B. eine verschlüsselte HTTPS-Anforderung.
    | **Beständiges Cookie verwenden**| Behalten Sie für diesen Wert die Einstellung **Nein** bei. Verwenden Sie diese Einstellung nur für Anwendungen, bei denen Cookies übergreifend für Prozesse genutzt werden können. Weitere Informationen zu Cookieeinstellungen finden Sie unter [Cookieeinstellungen für den Zugriff auf lokale Anwendungen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).
    | **URLs in Headern übersetzen** | Behalten Sie für diesen Wert **Ja** bei, wenn Ihre Anwendung den ursprünglichen Hostheader nicht in der Authentifizierungsanforderung erfordert. |
    | **URLs im Hauptteil der Anwendung übersetzen** | Behalten Sie für diesen Wert **Nein** bei, wenn Sie nicht über hartcodierte HTML-Links zu anderen lokalen Anwendungen verfügen, und verwenden Sie keine benutzerdefinierten Domänen. Weitere Informationen finden Sie unter [Linkübersetzung mit dem Anwendungsproxy](application-proxy-configure-hard-coded-link-translation.md).<br><br>Legen Sie diesen Wert auf **Ja** fest, wenn Sie diese Anwendung mit Microsoft Cloud App Security (MCAS) überwachen möchten. Weitere Informationen finden Sie unter [Konfigurieren der Echtzeitüberwachung des Anwendungszugriffs mit Microsoft Cloud App Security und Azure Active Directory](application-proxy-integrate-with-microsoft-cloud-application-security.md). |
   
6. Wählen Sie **Hinzufügen**.

## <a name="test-the-application"></a>Testen der Anwendung

Sie können nun testen, ob die Anwendung richtig hinzugefügt wurde. Mit den folgenden Schritten fügen Sie der Anwendung ein Benutzerkonto hinzu und probieren aus, ob Sie sich anmelden können.

### <a name="add-a-user-for-testing"></a>Hinzufügen eines Benutzers zu Testzwecken

Bevor Sie der Anwendung einen Benutzer hinzufügen, stellen Sie sicher, dass das Benutzerkonto bereits über Berechtigungen zum Zugriff auf die Anwendung aus dem Unternehmensnetzwerk heraus verfügt.

So fügen Sie einen Testbenutzer hinzu:

1. Wählen Sie die Option **Unternehmensanwendungen**, und wählen Sie dann die zu testende Anwendung aus.
2. Wählen Sie **Erste Schritte** und dann **Zuweisen eines Benutzers zu Testzwecken**.
3. Wählen Sie unter **Benutzer und Gruppen** die Option **Benutzer hinzufügen**.
4. Wählen Sie unter **Zuweisung hinzufügen** die Option **Benutzer und Gruppen**. Der Abschnitt **Benutzer und Gruppen** wird angezeigt. 
5. Wählen Sie das Konto aus, das Sie hinzufügen möchten. 
6. Wählen Sie die Option **Auswählen** und dann **Zuweisen**.

### <a name="test-the-sign-on"></a>Testen des Anmeldens

Testen Sie wie folgt das Anmelden an der Anwendung:

1. Navigieren Sie in Ihrem Browser zur externen URL, die Sie beim Veröffentlichen konfiguriert haben. Der Startbildschirm sollte angezeigt werden.
2. Melden Sie sich als der Benutzer an, den Sie im vorherigen Abschnitt erstellt haben.

Informationen zur Problembehandlung finden Sie unter [Beheben von Problemen mit Anwendungsproxys und Fehlermeldungen](application-proxy-troubleshoot.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Ihre lokale Umgebung auf den Einsatz des Anwendungsproxys vorbereitet und dann den Anwendungsproxyconnector installiert und registriert. Als Nächstes fügen Sie Ihrem Azure AD-Mandanten eine Anwendung hinzu. Sie haben überprüft, ob ein Testbenutzer sich über das Azure AD-Konto bei der Anwendung anmelden kann.

Sie haben folgende Schritte ausgeführt:
> [!div class="checklist"]
> * Öffnen von Ports für ausgehenden Datenverkehr und Zulassen des Zugriffs auf bestimmte URLs
> * Installieren des Connectors auf Ihrem Windows-Server und Registrierung mit dem Anwendungsproxy
> * Überprüfen der ordnungsgemäßen Installation und Registrierung des Connectors
> * Hinzufügen einer lokalen Anwendung zum Azure AD-Mandanten
> * Überprüfen, ob ein Testbenutzer sich über das Azure AD-Konto bei der Anwendung anmelden kann

Sie können die Anwendung jetzt für einmaliges Anmelden konfigurieren. Verwenden Sie den folgenden Link, um eine Anmeldemethode auszuwählen und auf Tutorials zum einmaligen Anmelden zuzugreifen. 

> [!div class="nextstepaction"]
>[Konfigurieren von einmaligem Anmelden](what-is-single-sign-on.md#choosing-a-single-sign-on-method)
