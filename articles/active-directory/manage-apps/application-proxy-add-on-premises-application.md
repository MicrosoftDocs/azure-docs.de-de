---
title: 'Tutorial: Hinzufügen einer lokalen App – Anwendungsproxy in Azure Active Directory'
description: Mit einem Anwendungsproxydienst in Azure Active Directory (Azure AD) können Benutzer auf lokale Anwendungen zugreifen, indem sie sich mit ihrem Azure AD-Konto anmelden. In diesem Tutorial wird veranschaulicht, wie Sie Ihre Umgebung für die Nutzung mit einem Anwendungsproxy vorbereiten. Anschließend wird das Azure-Portal verwendet, um Ihrem Azure AD-Mandanten eine lokale Anwendung hinzuzufügen.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperf-fy21q3-portal
ms.openlocfilehash: 6bd44ea0217f11a156598a1a6f3703e528dd82d4
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100095170"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Tutorial: Hinzufügen einer lokalen Anwendung für den Remotezugriff über den Anwendungsproxy in Azure Active Directory

Mit einem Anwendungsproxydienst in Azure Active Directory (Azure AD) können Benutzer auf lokale Anwendungen zugreifen, indem sie sich mit ihrem Azure AD-Konto anmelden. Weitere Informationen zum Anwendungsproxy finden Sie unter [Veröffentlichen von lokalen Apps für Remotebenutzer mit dem Azure AD-Anwendungsproxy](what-is-application-proxy.md). In diesem Tutorial wird Ihre Umgebung auf die Verwendung des Anwendungsproxys vorbereitet. Wenn Ihre Umgebung bereit ist, fügen Sie im Azure-Portal Ihrem Azure AD-Mandanten eine lokale Anwendung hinzu. 

:::image type="content" source="./media/application-proxy-add-on-premises-application/app-proxy-diagram.png" alt-text="Übersichtsdiagramm für den Anwendungsproxy" lightbox="./media/application-proxy-add-on-premises-application/app-proxy-diagram.png":::

Vergewissern Sie sich zunächst, dass Sie mit den Konzepten der App-Verwaltung und des **einmaligen Anmeldens (Single Sign-On, SSO)** vertraut sind. Sehen Sie sich die Informationen unter den folgenden Links an:
- [Schnellstartserie zur App-Verwaltung in Azure AD](view-applications-portal.md)
- [Worum handelt es sich beim einmaligen Anmelden (Single Sign-On, SSO)?](what-is-single-sign-on.md)

Connectors sind ein wichtiger Bestandteil des Anwendungsproxys. Weitere Informationen zu Connectors finden Sie unter [Grundlegendes zu Azure AD-Anwendungsproxyconnectors](application-proxy-connectors.md).

In diesem Tutorial wird Folgendes durchgeführt:

> [!div class="checklist"]
> * Öffnen von Ports für ausgehenden Datenverkehr und Zulassen des Zugriffs auf bestimmte URLs
> * Installieren des Connectors auf Ihrem Windows-Server und Registrierung mit dem Anwendungsproxy
> * Überprüfen der ordnungsgemäßen Installation und Registrierung des Connectors
> * Hinzufügen einer lokalen Anwendung zum Azure AD-Mandanten
> * Überprüfen, ob ein Testbenutzer sich über das Azure AD-Konto an der Anwendung anmelden kann

## <a name="prerequisites"></a>Voraussetzungen

Zum Hinzufügen einer lokalen Anwendung zu Azure AD benötigen Sie Folgendes:

* Ein [Microsoft Azure AD-Abonnement vom Typ „Premium“](https://azure.microsoft.com/pricing/details/active-directory)
* Ein Konto als Anwendungsadministrator
* Benutzeridentitäten müssen aus einem lokalen Verzeichnis synchronisiert oder direkt in Ihren Azure AD-Mandanten erstellt werden. Mithilfe der Identitätssynchronisierung können Benutzer von Azure AD vorab authentifiziert werden, bevor ihnen Zugriff auf per Anwendungsproxy veröffentlichte Anwendungen gewährt wird. Außerdem können die benötigten Benutzer-ID-Informationen für einmaliges Anmelden (Single Sign-On, SSO) bereitgestellt werden.

### <a name="windows-server"></a>Windows-Server

Für die Verwendung des Anwendungsproxys benötigen Sie einen Windows-Server, auf dem Windows Server 2012 R2 oder höher ausgeführt wird. Sie müssen den Connector für den Anwendungsproxy auf dem Server installieren. Dieser Connectorserver muss eine Verbindung mit den Anwendungsproxydiensten in Azure sowie mit den lokalen Anwendungen herstellen können, die Sie veröffentlichen möchten.

Für hohe Verfügbarkeit in Ihrer Produktionsumgebung sollten Sie mehrere Windows-Server einsetzen. Für dieses Tutorial reicht ein Windows-Server aus.

> [!IMPORTANT]
> Wenn Sie den Connector unter Windows Server 2019 installieren, müssen Sie für eine ordnungsgemäße Funktion die HTTP2-Protokollunterstützung in der WinHttp-Komponente für die eingeschränkte Kerberos-Delegierung deaktivieren. Diese Einstellung ist in früheren Versionen unterstützter Betriebssysteme standardmäßig deaktiviert. Wenn Sie den folgenden Registrierungsschlüssel hinzufügen und den Server neu starten, wird die Einstellung unter Windows Server 2019 deaktiviert. Beachten Sie, dass es sich hierbei um einen computerweiten Registrierungsschlüssel handelt.
>
> ```
> Windows Registry Editor Version 5.00
> 
> [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp]
> "EnableDefaultHTTP2"=dword:00000000
> ```
>
> Der Schlüssel kann über PowerShell mit dem folgenden Befehl festgelegt werden:
> ```
> Set-ItemProperty 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp\' -Name EnableDefaultHTTP2 -Value 0
>

#### <a name="recommendations-for-the-connector-server"></a>Empfehlungen für den Connectorserver

1. Platzieren Sie den Connectorserver physisch in der Nähe des Anwendungsservers, um die Leistung zwischen Connector und Anwendung zu optimieren. Weitere Informationen finden Sie unter [Aspekte der Netzwerktopologie bei Verwendung des Azure Active Directory-Anwendungsproxys](application-proxy-network-topology.md).
1. Der Connectorserver und die Webanwendungsserver müssen derselben Active Directory-Domäne angehören bzw. vertrauenswürdige Domänen umfassen. Dass die Server zu derselben Domäne oder zu vertrauenswürdigen Domänen gehören, ist eine Voraussetzung für die Verwendung des einmaligen Anmeldens (Single Sign-On, SSO) mit integrierter Windows-Authentifizierung (IWA) und eingeschränkter Kerberos-Delegierung (Kerberos Constrained Delegation, KCD). Wenn der Connector- und Webanwendungsserver sich in unterschiedlichen Active Directory-Domänen befinden, müssen Sie die ressourcenbasierte Delegierung für einmaliges Anmelden verwenden. Weitere Informationen finden Sie unter [Eingeschränkte Kerberos-Delegierung für das einmalige Anmelden mit Anwendungsproxy](application-proxy-configure-single-sign-on-with-kcd.md).

> [!WARNING]
> Falls Sie den Proxy für den Azure AD-Kennwortschutz bereitgestellt haben, installieren Sie den Azure AD-Anwendungsproxy und den Proxy für den Azure AD-Kennwortschutz nicht auf dem gleichen Computer. Der Azure AD-Anwendungsproxy und der Proxy für den Azure AD-Kennwortschutz installieren verschiedene Versionen des Azure AD Connect Agent Updater-Diensts. Diese unterschiedlichen Versionen sind nicht kompatibel, wenn sie auf demselben Computer installiert werden.

#### <a name="tls-requirements"></a>TLS-Anforderungen

Auf dem Windows-Connectorserver muss TLS 1.2 aktiviert werden, bevor Sie den Anwendungsproxyconnector installieren.

So aktivieren Sie TLS 1.2

1. Legen Sie die folgenden Registrierungsschlüssel fest:

   ```
   Windows Registry Editor Version 5.00

   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
   "DisabledByDefault"=dword:00000000
   "Enabled"=dword:00000001
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server]
   "DisabledByDefault"=dword:00000000
   "Enabled"=dword:00000001
   [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
   "SchUseStrongCrypto"=dword:00000001
   ```

1. Starten Sie den Server neu.

> [!Note]
> Microsoft aktualisiert Azure-Dienste für die Verwendung von TLS-Zertifikaten aus einer anderen Gruppe von Stammzertifizierungsstellen (Certificate Authorities, CAs). Diese Änderung wird vorgenommen, da die aktuellen Zertifizierungsstellenzertifikate eine der grundlegenden Anforderungen des Zertifizierungsstellen-/Browserforums nicht erfüllen. Weitere Informationen finden Sie unter [TLS-Zertifikatänderungen für Azure](../../security/fundamentals/tls-certificate-changes.md).

## <a name="prepare-your-on-premises-environment"></a>Vorbereiten Ihrer lokalen Umgebung

Ermöglichen Sie zuerst die Kommunikation mit Azure-Rechenzentren, um Ihre Umgebung für den Azure AD-Anwendungsproxy vorzubereiten. Falls der Pfad eine Firewall enthält, sollten Sie sicherstellen, dass der Zugang geöffnet ist. Bei einer geöffneten Firewall kann der Connector HTTPS-Anforderungen (TCP) an den Anwendungsproxy senden.

> [!IMPORTANT]
> Wenn Sie den Connector für die Azure Government-Cloud installieren, beachten Sie die [Voraussetzungen](../hybrid/reference-connect-government-cloud.md#allow-access-to-urls) und [Installationsschritte](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud). Zum Ausführen der Installation sind der Zugriff auf einen anderen Satz von URLs und ein zusätzlicher Parameter erforderlich.

### <a name="open-ports"></a>Öffnen von Ports

Öffnen Sie die folgenden Ports für den **ausgehenden** Datenverkehr.

   | Portnummer | Wie diese verwendet wird |
   | --- | --- |
   | 80 | Herunterladen der Zertifikatsperrlisten (CRLs) bei der Überprüfung des TLS/SSL-Zertifikats |
   | 443 | Gesamte ausgehende Kommunikation mit dem Webanwendungsproxy-Dienst |

Wenn Ihre Firewall Datenverkehr gemäß Ursprungsbenutzern erzwingt, öffnen Sie auch die Ports 80 und 443 für den Datenverkehr aus Windows-Diensten, die als Netzwerkdienst ausgeführt werden.

### <a name="allow-access-to-urls"></a>Zulassen des Zugriffs auf URLs

Lassen Sie den Zugriff auf die folgenden URLs zu:

| URL | Port | Wie diese verwendet wird |
| --- | --- | --- |
| &ast;.msappproxy.net<br>&ast;.servicebus.windows.net | 443/HTTPS | Kommunikation zwischen dem Connector und dem Anwendungsproxy-Clouddienst |
| crl3.digicert.com<br>crl4.digicert.com<br>ocsp.digicert.com<br>crl.microsoft.com<br>oneocsp.microsoft.com<br>ocsp.msocsp.com<br> | 80/HTTP |Der Connector verwendet diese URLs, um Zertifikate zu überprüfen. |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>&ast;.microsoftonline.com<br>&ast;.microsoftonline-p.com<br>&ast;.msauth.net<br>&ast;.msauthimages.net<br>&ast;.msecnd.net<br>&ast;.msftauth.net<br>&ast;.msftauthimages.net<br>&ast;.phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctldl.windowsupdate.com<br>www.microsoft.com/pkiops | 443/HTTPS |Der Connector verwendet diese URLs während der Registrierung. |
| ctldl.windowsupdate.com | 80/HTTP |Der Connector verwendet diese URL während der Registrierung. |

Sie können Verbindungen mit „&ast;.msappproxy.net“, „&ast;.servicebus.windows.net“ und anderen URLs zulassen, wenn Ihre Firewall oder Ihr Proxy die Konfiguration von Zugriffsregeln auf der Grundlage von Domänensuffixen ermöglicht. Andernfalls müssen Sie den Zugriff auf die Datei [Azure IP Ranges and Service Tags – Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519) zulassen. Die IP-Adressbereiche werden wöchentlich aktualisiert.

### <a name="dns-name-resolution-for-azure-ad-application-proxy-endpoints"></a>DNS-Namensauflösung für Azure AD-Anwendungsproxy-Endpunkte

Öffentliche DNS-Einträge für Azure AD-Anwendungsproxy-Endpunkte sind verkettete CNAME-Einträge, die auf einen A-Eintrag verweisen. Das sorgt für Fehlertoleranz und Flexibilität. Es ist sichergestellt, dass der Azure AD-Anwendungsproxyconnector immer auf Hostnamen mit dem Domänensuffix _*.msappproxy.net_ oder _*.servicebus.windows.net_ zugreift. Bei der Namensauflösung können die CNAME-Einträge allerdings DNS-Einträge mit anderen Hostnamen und Suffixen enthalten.  Daher muss sichergestellt werden, dass das Gerät (abhängig von Ihrem Setup: Connectorserver, Firewall, Proxy für ausgehenden Datenverkehr) alle Einträge in der Kette auflösen kann und eine Verbindung mit den aufgelösten IP-Adressen zulässt. Da die DNS-Einträge in der Kette ggf. gelegentlich geändert werden, können wir keine Liste mit DNS-Einträgen bereitstellen.

## <a name="install-and-register-a-connector"></a>Installieren und Registrieren eines Connectors

Installieren Sie für die Nutzung des Anwendungsproxys einen Connector auf jedem Windows-Server, den Sie mit dem Anwendungsproxydienst verwenden. Der Connector ist ein Agent, der die aus dem lokalen Anwendungsserver ausgehende Verbindung mit dem Anwendungsproxy in Azure AD verwaltet. Sie können einen Connector auf Servern installieren, auf denen auch andere Authentifizierungs-Agents wie Azure AD Connect installiert sind.


So installieren Sie den Connector:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als Anwendungsadministrator des Verzeichnisses an, das den Anwendungsproxy verwendet. Wenn die Mandantendomäne also beispielsweise „contoso.com“ lautet, muss sich der Administrator als admin@contoso.com oder mit einem anderen Administratoraliasnamen in dieser Domäne anmelden.
1. Wählen Sie rechts oben Ihren Benutzernamen aus. Stellen Sie sicher, dass Sie an einem Verzeichnis angemeldet sind, für das der Anwendungsproxy verwendet wird. Wählen Sie **Verzeichnis wechseln**, und wählen Sie ein Verzeichnis aus, für das der Anwendungsproxy verwendet wird, falls Sie das Verzeichnis wechseln möchten.
1. Wählen Sie im Navigationsbereich auf der linken Seite die Option **Azure Active Directory**.
1. Wählen Sie unter **Verwalten** die Option **Anwendungsproxy**.
1. Wählen Sie **Connectordienst herunterladen**.

    ![Herunterladen des Connectordiensts zum Anzeigen der Nutzungsbedingungen](./media/application-proxy-add-on-premises-application/application-proxy-download-connector-service.png)

1. Lesen Sie die Vertragsbedingungen. Wählen Sie die Option **Bedingungen akzeptieren und herunterladen**, wenn Sie mit dem Lesen fertig sind.
1. Wählen Sie am unteren Rand des Fensters die Option **Ausführen**, um den Connector zu installieren. Ein Installations-Assistent wird geöffnet.
1. Befolgen Sie im Assistenten die Anleitung zum Installieren des Diensts. Wenn Sie aufgefordert werden, den Connector mit dem Anwendungsproxy für Ihren Azure AD-Mandanten zu registrieren, geben Sie Ihre Administratoranmeldeinformationen für die Anwendung ein.
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
1. Wählen Sie im Navigationsbereich auf der linken Seite die Option **Azure Active Directory** und dann im Abschnitt **Verwalten** die Option **Anwendungsproxy**. Alle Connectors und Connectorgruppen werden auf dieser Seite angezeigt.
1. Wählen Sie einen Connector aus, um die Details zu überprüfen. Die Connectors sollten standardmäßig erweitert sein. Falls der gewünschte zu überprüfende Connector nicht erweitert ist, können Sie ihn erweitern, um die Details anzuzeigen. Eine aktive grüne Beschriftung gibt an, dass Ihr Connector eine Verbindung mit dem Dienst herstellen kann. Jedoch auch wenn die Beschriftung grün ist, könnte ein Netzwerkproblem immer noch den Nachrichtenempfang des Connectors blockieren.

    ![Azure AD-Anwendungsproxyconnectors](./media/application-proxy-add-on-premises-application/app-proxy-connectors.png)

Weitere Hilfe zur Installation eines Connectors finden Sie unter [Problem beim Installieren des Anwendungsproxy-Agent-Connectors](application-proxy-connector-installation-problem.md).

### <a name="verify-the-installation-through-your-windows-server"></a>Überprüfen der Installation über Ihren Windows-Server

So überprüfen Sie, ob der Connector ordnungsgemäß installiert und registriert ist:

1. Öffnen Sie den Windows-Dienst-Manager, indem Sie auf die **Windows**-Taste klicken und *services.msc* eingeben.
1. Überprüfen Sie, ob die folgenden beiden Dienste den Status **Wird ausgeführt** haben.
   - Die Konnektivität wird per **Microsoft AAD-Anwendungsproxyconnector** ermöglicht.
   - Die **Aktualisierung des Microsoft AAD-Anwendungsproxyconnectors** ist ein automatischer Aktualisierungsdienst. Der Dienst sucht nach neuen Versionen des Connectors und aktualisiert den Connector bei Bedarf.

     ![Anwendungsproxy-Connectordienste – Screenshot](./media/application-proxy-add-on-premises-application/app_proxy_services.png)

1. Wenn die Dienste nicht den Status **Wird ausgeführt** haben, müssen Sie mit der rechten Maustaste auf jeden Dienst klicken und die Option **Starten** wählen.

## <a name="add-an-on-premises-app-to-azure-ad"></a>Hinzufügen einer lokalen App zu Azure AD

Nun haben Sie Ihre Umgebung vorbereitet, einen Connector installiert und sind bereit, Azure AD lokale Anwendungen hinzuzufügen.  

1. Melden Sie sich als Administrator beim [Azure-Portal](https://portal.azure.com/) an.
2. Wählen Sie im Navigationsbereich auf der linken Seite die Option **Azure Active Directory**.
3. Wählen Sie **Unternehmensanwendungen** und dann **Neue Anwendung**.
4. Wählen Sie die Schaltfläche **Lokale Anwendung hinzufügen** aus, die etwa in der Mitte der Seite im Abschnitt **Lokale Anwendungen** angezeigt wird. Alternativ können Sie oben auf der Seite die Option **Eigene Anwendung erstellen** und dann **Anwendungsproxy für sicheren Remotezugriff auf eine lokale Anwendung konfigurieren** auswählen.
5. Geben Sie im Abschnitt **Fügen Sie Ihre eigene lokale Anwendung hinzu** die folgenden Informationen zu Ihrer Anwendung an:

    | Feld | BESCHREIBUNG |
    | :---- | :---------- |
    | **Name** | Der Name der Anwendung, der in „Meine Apps“ und im Azure-Portal angezeigt wird |
    | **Interne URL** | Die URL zum Zugreifen auf die Anwendung innerhalb Ihres privaten Netzwerks. Sie können einen bestimmten Pfad auf dem Back-End-Server für die Veröffentlichung angeben, während der Rest des Servers nicht veröffentlicht wird. Auf diese Weise können Sie unterschiedliche Websites auf demselben Server als unterschiedliche Apps veröffentlichen und jeweils einen eigenen Namen und Zugriffsregeln vergeben.<br><br>Stellen Sie beim Veröffentlichen eines Pfads sicher, dass er alle erforderlichen Bilder, Skripts und Stylesheets für Ihre Anwendung enthält. Wenn sich die App beispielsweise unter „https:\//yourapp/app“ befindet und Bilder unter „https:\//yourapp/media“ genutzt werden, sollten Sie „https:\//yourapp/“ als Pfad veröffentlichen. Diese interne URL verfügt nicht über die Zielseite, die den Benutzern angezeigt wird. Weitere Informationen finden Sie unter [Festlegen einer benutzerdefinierten Startseite für veröffentlichte Apps](application-proxy-configure-custom-home-page.md). |
    | **Externe URL** | Die Adresse für den Benutzerzugriff auf die App von außerhalb Ihres Netzwerks. Wenn Sie die Anwendungsproxy-Standarddomäne nicht verwenden möchten, lesen Sie [Benutzerdefinierte Domänen im Azure AD-Anwendungsproxy](application-proxy-configure-custom-domain.md).|
    | **Vorauthentifizierung** | Gibt das Verfahren an, wie der Anwendungsproxy Benutzer überprüft, bevor diese Zugriff auf Ihre Anwendung erhalten.<br><br>**Azure Active Directory**: Der Anwendungsproxy leitet Benutzer an die Anmeldung mit Azure AD um. Hierbei werden deren Berechtigungen für das Verzeichnis und die Anwendung authentifiziert. Es wird empfohlen, diese Option als Standardwert aktiviert zu lassen, damit Sie Azure AD-Sicherheitsfunktionen wie bedingten Zugriff und Multi-Factor Authentication nutzen können. **Azure Active Directory** ist erforderlich, um die Anwendung mit Microsoft Cloud App Security zu überwachen.<br><br>**Passthrough**: Benutzer müssen sich nicht bei Azure AD authentifizieren, um Zugriff auf die Anwendung zu erhalten. Sie können weiterhin Authentifizierungsanforderungen auf dem Back-End einrichten. |
    | **Connectorgruppe** | Connectors verarbeiten den Remotezugriff auf Ihre Anwendung, und Connectorgruppen helfen Ihnen, Connectors und Apps nach Region, Netzwerk oder Zweck zu organisieren. Wenn Sie noch keine Connectorgruppen erstellt haben, wird Ihre App zu **Standard** zugewiesen.<br><br>Wenn Ihre Anwendung WebSockets verwendet, um eine Verbindung herzustellen, muss die Version aller Connectors in der Gruppe 1.5.612.0 oder höher sein.|

6. Konfigurieren Sie bei Bedarf die Einstellungen unter **Zusätzliche Einstellungen**. Für die meisten Anwendungen sollten Sie diese Einstellungen im Standardzustand belassen. 

    | Feld | BESCHREIBUNG |
    | :---- | :---------- |
    | **Timeout der Back-End-Anwendung** | Legen Sie diesen Wert nur auf **Lang** fest, wenn Ihre Anwendung bei der Authentifizierung und dem Verbinden langsam ist. Standardmäßig beträgt das Timeout der Back-End-Anwendung 85 Sekunden. Wird der Wert auf „Lang“ festgelegt, wird das Back-End-Timeout auf 180 Sekunden erhöht. |
    | **Nur-HTTP-Cookie verwenden** | Legen Sie diesen Wert auf **Ja** fest, damit Anwendungsproxycookies das Flag HTTPOnly im HTTP-Antwortheader enthalten. Wenn Sie Remotedesktopdienste verwenden, sollten Sie diese Option auf **Nein** festlegen.|
    | **Sicheres Cookie verwenden**| Legen Sie diesen Wert auf **Ja** fest, um Cookies über einen sicheren Kanal zu übertragen, z.B. eine verschlüsselte HTTPS-Anforderung.
    | **Beständiges Cookie verwenden**| Behalten Sie für diesen Wert die Einstellung **Nein** bei. Verwenden Sie diese Einstellung nur für Anwendungen, bei denen Cookies übergreifend für Prozesse genutzt werden können. Weitere Informationen zu Cookieeinstellungen finden Sie unter [Cookieeinstellungen für den Zugriff auf lokale Anwendungen in Azure Active Directory](./application-proxy-configure-cookie-settings.md).
    | **URLs in Headern übersetzen** | Behalten Sie für diesen Wert **Ja** bei, wenn Ihre Anwendung den ursprünglichen Hostheader nicht in der Authentifizierungsanforderung erfordert. |
    | **URLs im Hauptteil der Anwendung übersetzen** | Behalten Sie für diesen Wert **Nein** bei, wenn Sie nicht über hartcodierte HTML-Links zu anderen lokalen Anwendungen verfügen, und verwenden Sie keine benutzerdefinierten Domänen. Weitere Informationen finden Sie unter [Linkübersetzung mit dem Anwendungsproxy](application-proxy-configure-hard-coded-link-translation.md).<br><br>Legen Sie diesen Wert auf **Ja** fest, wenn Sie diese Anwendung mit Microsoft Cloud App Security (MCAS) überwachen möchten. Weitere Informationen finden Sie unter [Konfigurieren der Echtzeitüberwachung des Anwendungszugriffs mit Microsoft Cloud App Security und Azure Active Directory](application-proxy-integrate-with-microsoft-cloud-application-security.md). |

7. Wählen Sie **Hinzufügen**.

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

1. Wählen Sie in der zu testenden Anwendung **Anwendungsproxy** aus.
2. Wählen Sie oben auf der Seite **Anwendung testen** aus, um einen Test für die Anwendung auszuführen und Konfigurationsprobleme zu ermitteln.
3. Starten Sie unbedingt zuerst die Anwendung, um die Anmeldung zu testen, und laden Sie anschließend den Diagnosebericht herunter, um den Leitfaden zur Problemlösung für ermittelte Probleme anzuzeigen.

Informationen zur Problembehandlung finden Sie unter [Beheben von Problemen mit Anwendungsproxys und Fehlermeldungen](application-proxy-troubleshoot.md).

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die in diesem Tutorial erstellten Ressourcen, wenn Sie sie nicht mehr benötigen.

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
> [Konfigurieren von einmaligem Anmelden](sso-options.md#choosing-a-single-sign-on-method)
