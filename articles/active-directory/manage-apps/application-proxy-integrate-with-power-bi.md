---
title: Aktivieren des Remotezugriffs auf Power BI mit dem Azure AD-Anwendungsproxy
description: Hier werden die Grundlagen für die Integration einer lokalen Power BI-Instanz mit dem Azure AD-Anwendungsproxy erläutert.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/25/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c3188571e9188add7bc8f4f4d07ea5a562a79b3
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658110"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-ad-application-proxy"></a>Ermöglichen des Remotezugriffs auf Power BI Mobile mit dem Azure AD-Anwendungsproxy

In diesem Artikel erfahren Sie, wie Sie den Azure AD-Anwendungsproxy verwenden, um der Power BI Mobile-App das Herstellen einer Verbindung mit dem Power BI-Berichtsserver (Power BI Report Server, PBIRS) sowie mit SQL Server Reporting Services (SSRS) 2016 und höher zu ermöglichen. Durch diese Integration können Benutzer von außerhalb des Unternehmensnetzwerks über die Power BI Mobile-App auf ihre Power BI-Berichte zugreifen und dabei mittels Azure AD-Authentifizierung geschützt werden. Dieser Schutz umfasst [Sicherheitsvorteile](application-proxy-security.md#security-benefits) wie bedingten Zugriff und mehrstufige Authentifizierung.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie bereits Berichtsdienste bereitgestellt und den [Anwendungsproxy aktiviert](application-proxy-add-on-premises-application.md) haben.

- Zur Aktivierung des Anwendungsproxys müssen Sie einen Connector auf einem Windows-Server installieren und die [Voraussetzungen](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) erfüllen, damit der Connector mit Azure AD-Diensten kommunizieren kann.
- Bei der Veröffentlichung von Power BI empfiehlt es sich, die gleichen internen und externen Domänen zu verwenden. Weitere Informationen zu benutzerdefinierten Domänen finden Sie unter [Arbeiten mit benutzerdefinierten Domänen im Azure AD-Anwendungsproxy](./application-proxy-configure-custom-domain.md).
- Diese Integration ist für die **Power BI Mobile-Anwendung für iOS und Android** verfügbar.

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>Schritt 1: Konfigurieren der eingeschränkten Kerberos-Delegierung (Kerberos Constrained Delegation, KCD)

Für lokale Anwendungen, für die die Windows-Authentifizierung verwendet wird, können Sie einmaliges Anmelden (SSO) über das Kerberos-Authentifizierungsprotokoll und das Feature „Eingeschränkte Kerberos-Delegierung“ (Kerberos Constrained Delegation, KCD) erreichen. Nach der Konfiguration ermöglicht KCD dem Anwendungsproxyconnector das Abrufen eines Windows-Tokens für einen Benutzer, auch wenn sich dieser nicht direkt bei Windows angemeldet hat. Weitere Informationen zu KCD finden Sie in der [Übersicht über die eingeschränkte Kerberos-Delegierung](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj553400(v=ws.11)) sowie unter [Eingeschränkte Kerberos-Delegierung für die einmalige Anmeldung bei Ihren Apps mit Anwendungsproxy](application-proxy-configure-single-sign-on-with-kcd.md).

Aufseiten von Reporting Services gibt es nicht viel zu konfigurieren. Achten Sie lediglich darauf, dass Sie über einen gültigen Dienstprinzipalnamen (Service Principal Name, SPN) verfügen, um eine ordnungsgemäße Kerberos-Authentifizierung zu ermöglichen. Vergewissern Sie sich außerdem, dass der Reporting Services-Server für die Aushandlungsauthentifizierung aktiviert ist.

Gehen Sie wie folgt vor, um KCD für Reporting Services einzurichten:

### <a name="configure-the-service-principal-name-spn"></a>Konfigurieren des Dienstprinzipalnamens (Service Principal Name, SPN)

Der SPN ist ein eindeutiger Bezeichner für einen Dienst, der die Kerberos-Authentifizierung verwendet. Für Ihren Berichtsserver muss ein ordnungsgemäßer HTTP-SPN vorhanden sein. Informationen zum Konfigurieren des ordnungsgemäßen Dienstprinzipalnamens (Service Principal Name, SPN) für Ihren Berichtsserver finden Sie unter [Registrieren eines Dienstprinzipalnamens (SPN) für einen Berichtsserver](/sql/reporting-services/report-server/register-a-service-principal-name-spn-for-a-report-server).
Sie können überprüfen, ob der SPN hinzugefügt wurde, indem Sie den Befehl „setspn“ mit der Option „-L“ ausführen. Weitere Informationen zu diesem Befehl finden Sie unter [Setspn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx).

### <a name="enable-negotiate-authentication"></a>Aktivieren der Aushandlungsauthentifizierung

Um einem Berichtsserver die Verwendung der Kerberos-Authentifizierung zu ermöglichen, legen Sie den Authentifizierungstyp des Berichtsservers auf „RSWindowsNegotiate“ fest. Konfigurieren Sie diese Einstellung mithilfe der Datei „rsreportserver.config“.

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

Weitere Informationen finden Sie unter [Ändern einer Reporting Services-Konfigurationsdatei](/sql/reporting-services/report-server/modify-a-reporting-services-configuration-file-rsreportserver-config) sowie unter [Konfigurieren der Windows-Authentifizierung auf einem Berichtsserver](/sql/reporting-services/security/configure-windows-authentication-on-the-report-server).

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>Sicherstellen, dass dem Connector bei Delegierungen des dem Reporting Services-Anwendungspoolkonto hinzugefügten SPN vertraut wird
Konfigurieren Sie KCD so, dass der Azure AD-Anwendungsproxydienst Benutzeridentitäten an das Reporting Services-Anwendungspoolkonto delegieren kann. Sie konfigurieren KCD, indem Sie den Anwendungsproxyconnector so einrichten, dass er Kerberos-Tickets für Benutzer abruft, die in Azure AD authentifiziert wurden. Anschließend übergibt dieser Server den Kontext an die Zielanwendung bzw. in diesem Fall an Reporting Services.

Wiederholen Sie zum Konfigurieren von KCD die folgenden Schritte für jeden Connectorcomputer:

1. Melden Sie sich bei einem Domänencontroller als Domänenadministrator an, und öffnen Sie dann **Active Directory-Benutzer und -Computer**.
2. Suchen Sie nach dem Computer, auf dem der Connector ausgeführt wird.
3. Doppelklicken Sie auf den Computer, und wählen Sie anschließend die Registerkarte **Delegierung** aus.
4. Legen Sie die Delegierungseinstellungen auf **Computer bei Delegierungen angegebener Dienste vertrauen** fest. Wählen Sie dann die Option **Beliebiges Authentifizierungsprotokoll verwenden** aus.
5. Wählen Sie **Hinzufügen** und anschließend **Benutzer oder Computer** aus.
6. Geben Sie das Dienstkonto ein, das Sie für Reporting Services verwenden. Hierbei handelt es sich um das Konto, dem Sie in der Reporting Services-Konfiguration den SPN hinzugefügt haben.
7. Klicken Sie auf **OK**. Um die Änderungen zu speichern, klicken Sie erneut auf **OK**.

Weitere Informationen finden Sie unter [Eingeschränkte Delegierung von Kerberos für die einmalige Anmeldung zu Ihren Apps mit dem Anwendungsproxy](application-proxy-configure-single-sign-on-with-kcd.md).

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>Schritt 2: Veröffentlichen von Berichtsdiensten über den Azure AD-Anwendungsproxy

Nun können Sie den Azure AD-Anwendungsproxy konfigurieren.

1. Verwenden Sie die folgenden Einstellungen, um Berichtsdienste über den Anwendungsproxy zu veröffentlichen. Eine detaillierte Anleitung zum Veröffentlichen einer Anwendung über den Anwendungsproxy finden Sie unter [Veröffentlichen von Anwendungen mit Azure AD-Anwendungsproxy](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   - **Interne URL:** Geben Sie die URL des Berichtsservers ein, den der Connector im Unternehmensnetzwerk erreichen kann. Diese URL muss von dem Server aus erreichbar sein, auf dem der Connector installiert ist. Eine bewährte Methode ist die Verwendung einer Domäne der obersten Ebene (etwa `https://servername/`), um Probleme mit Unterpfaden zu vermeiden, die über den Anwendungsproxy veröffentlicht werden. Verwenden Sie beispielsweise `https://servername/`, aber nicht `https://servername/reports/` oder `https://servername/reportserver/`.
     > [!NOTE]
     > Wir empfehlen die Verwendung einer sicheren HTTPS-Verbindung mit dem Berichtsserver. Eine entsprechende Anleitung finden Sie unter [Konfigurieren von SSL-Verbindungen auf einem Berichtsserver im einheitlichen Modus](/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server?view=sql-server-2017).
   - **Externe URL:** Geben Sie die öffentliche URL ein, mit der die Power BI Mobile-App eine Verbindung herstellt. Bei Verwendung einer benutzerdefinierten Domäne kann dieser Wert beispielsweise wie folgt aussehen: `https://reports.contoso.com`. Laden Sie zur Verwendung einer benutzerdefinierten Domäne ein Zertifikat für die Domäne hoch, und erstellen Sie für einen DNS-Eintrag einen Verweis auf die Standarddomäne „msappproxy.net“ für Ihre Anwendung. Eine ausführliche Anleitung finden Sie unter [Arbeiten mit benutzerdefinierten Domänen im Azure AD-Anwendungsproxy](application-proxy-configure-custom-domain.md).

   - **Methode für die Vorauthentifizierung**: Azure Active Directory

2. Nachdem die App veröffentlicht wurde, konfigurieren Sie die Einstellungen für einmaliges Anmelden anhand der folgenden Schritte:

   a. Wählen Sie auf der Anwendungsseite im Portal **Einmaliges Anmelden** aus.

   b. Legen Sie den **Modus für einmaliges Anmelden** auf **Integrierte Windows-Authentifizierung** fest.

   c. Legen Sie **Interner Anwendungs-SPN** auf den zuvor festgelegten Wert fest.

   d. Wählen Sie die **delegierte Identität für die Anmeldung** für den zu verwendenden Connector im Auftrag Ihrer Benutzer. Weitere Informationen finden Sie unter [Bereitstellen von einmaligem Anmelden bei Ihren Apps mit dem Anwendungsproxy](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities).

   e. Klicken Sie zum Speichern der Änderungen auf **Speichern**.

Zum Abschluss der Anwendungseinrichtung wechseln Sie zum Abschnitt **Benutzer und Gruppen**. Weisen Sie dort Benutzer zu, die Zugriff auf diese Anwendung erhalten.

## <a name="step-3-modify-the-reply-uris-for-the-application"></a>Schritt 3: Ändern der Antwort-URIs für die Anwendung

Bevor die mobile Power BI-App eine Verbindung herstellen und auf Berichtsdienste zugreifen kann, müssen Sie die Anwendungsregistrierung konfigurieren, die in Schritt 2 automatisch für Sie erstellt wurde.

1. Wählen Sie in Azure Active Directory auf der Seite **Übersicht** die Option **App-Registrierungen** aus.
2. Suchen Sie auf der Registerkarte **Alle Anwendungen** nach der Anwendung, die Sie in Schritt 2 erstellt haben.
3. Wählen Sie die Anwendung aus, und wählen Sie dann **Authentifizierung** aus.
4. Fügen Sie die folgenden Umleitungs-URIs basierend auf der von Ihnen verwendeten Plattform hinzu.

   Fügen Sie beim Konfigurieren der App für Power BI Mobile **iOS** die folgenden Umleitungs-URIs vom Typ „Öffentlicher Client (Mobilgerät und Desktop)“ hinzu:
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`

   Fügen Sie beim Konfigurieren der App für Power BI Mobile **Android** die folgenden Umleitungs-URIs vom Typ „Öffentlicher Client (Mobilgerät und Desktop)“ hinzu:
   - `urn:ietf:wg:oauth:2.0:oob`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `msauth://com.microsoft.powerbim/g79ekQEgXBL5foHfTlO2TPawrbI%3D`
   - `msauth://com.microsoft.powerbim/izba1HXNWrSmQ7ZvMXgqeZPtNEU%3D`

   > [!IMPORTANT]
   > Die Umleitungs-URIs müssen hinzugefügt werden, damit die Anwendung ordnungsgemäß funktioniert. Wenn Sie die App für Power BI Mobile iOS und für Power BI Mobile Android konfigurieren, fügen Sie der Liste der für iOS konfigurierten Umleitungs-URIs den folgenden Umleitungs-URI vom Typ „Öffentlicher Client (Mobilgerät und Desktop)“ hinzu: `urn:ietf:wg:oauth:2.0:oob`

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>Schritt 4: Herstellen einer Verbindung über die Power BI Mobile-App

1. Stellen Sie in der Power BI Mobile-App eine Verbindung mit Ihrer Reporting Services-Instanz her. Geben Sie hierzu die **Externe URL** für die Anwendung ein, die Sie über den Anwendungsproxy veröffentlicht haben.

   ![Power BI Mobile-App mit externer URL](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. Wählen Sie **Verbinden**. Sie werden zur Azure Active Directory Anmeldeseite weitergeleitet.

3. Geben Sie gültige Anmeldeinformationen für Ihren Benutzer ein, und wählen Sie **Anmelden** aus. Die Elemente Ihres Reporting Services-Servers werden angezeigt.

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>Schritt 5: Konfigurieren einer Intune-Richtlinie für verwaltete Geräte (optional)

Sie können Microsoft Intune verwenden, um die von Ihrem Personal verwendeten Client-Apps zu verwalten. Intune bietet Funktionen wie Datenverschlüsselung und zusätzliche Zugriffsanforderungen. Weitere Informationen zur App-Verwaltung mit Intune finden Sie unter „Intune-App-Verwaltung“. Gehen Sie wie folgt vor, um der Power BI Mobile-Anwendung die Verwendung der Intune-Richtlinie zu ermöglichen:

1. Wechseln Sie zu **Azure Active Directory** und dann zu **App-Registrierungen**.
2. Wählen Sie die in Schritt 3 konfigurierte Anwendung aus, wenn Sie Ihre native Clientanwendung registrieren.
3. Wählen Sie auf der Anwendungsseite die Option **API-Berechtigungen** aus.
4. Klicken Sie auf **Berechtigung hinzufügen**.
5. Suchen Sie unter **Von meiner Organisation verwendete APIs** nach „Microsoft Mobile Application Management“, und wählen Sie die entsprechende Option aus.
6. Fügen Sie der Anwendung die Berechtigung **DeviceManagementManagedApps.ReadWrite** hinzu.
7. Klicken Sie auf **Administratoreinwilligung gewähren**, um der Berechtigung Zugriff auf die Anwendung zu gewähren.
8. Konfigurieren Sie die gewünschte Intune-Richtlinie. Eine entsprechende Anleitung finden Sie unter [Erstellen und Zuweisen von App-Schutzrichtlinien](/intune/app-protection-policies).

## <a name="troubleshooting"></a>Problembehandlung

Sollte die Anwendung eine Fehlerseite zurückgeben, wenn mehrere Minuten lang versucht wird, einen Bericht zu laden, müssen ggf. die Timeouteinstellungen geändert werden. Der Anwendungsproxy unterstützt standardmäßig Anwendungen, die bis zu 85 Sekunden benötigen, um auf eine Anforderung zu reagieren. Diese Einstellung kann auf 180 Sekunden erhöht werden. Legen Sie hierzu das Back-End-Timeout auf der Einstellungsseite des App-Proxys für die Anwendung auf **Lang** fest. Tipps zur Erstellung schneller und zuverlässiger Berichte finden Sie unter [Bewährte Methoden für die Power BI-Leistung](/power-bi/power-bi-reports-performance).

## <a name="next-steps"></a>Nächste Schritte

- [Aktivieren von nativen Clientanwendungen für die Interaktion mit Proxyanwendungen](application-proxy-configure-native-client-application.md)
- [Anzeigen lokaler Berichte und KPIs eines Berichtsservers in den mobilen Power BI-Apps](/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)