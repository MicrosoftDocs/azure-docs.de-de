---
title: Integrieren in den AD-Anwendungsproxy auf einem NDES-Server
titleSuffix: Azure Active Directory
description: Dies ist ein Leitfaden für die Bereitstellung eines Azure Active Directory-Anwendungsproxys zum Schützen Ihres NDES-Servers.
services: active-directory
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/17/2020
ms.author: kenwith
ms.reviewer: mimart
ms.openlocfilehash: 0682115727068c928418d97fbf92ed32897c39d4
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656750"
---
# <a name="integrate-with-azure-ad-application-proxy-on-a-network-device-enrollment-service-ndes-server"></a>Integrieren in den Azure AD-Anwendungsproxy auf einem NDES-Server (Network Device Enrollment Service, Registrierungsdienst für Netzwerke)

Azure AD-Anwendungsproxys (Azure Active Directory) ermöglichen das Veröffentlichen von Anwendungen in Ihrem Netzwerk. Zu diesen Anwendungen gehören beispielsweise SharePoint-Websites, die Microsoft Outlook-Web-App und andere Webanwendungen. Außerdem wird mit diesem Anwendungsproxy über Azure sicherer Zugriff auf Benutzer außerhalb Ihres Netzwerks ermöglicht.

Wenn Sie noch nicht mit dem Azure AD-Anwendungsproxy vertraut sind und mehr darüber erfahren möchten, finden Sie weitere Informationen im Artikel [Remotezugriff auf lokale Anwendungen über den Azure AD-Anwendungsproxy](application-proxy.md).

Der Azure Active Directory-Anwendungsproxy basiert auf Azure. Dadurch erhalten Sie eine enorme Menge an Netzwerkbandbreite und Serverinfrastruktur für einen besseren Schutz vor verteilten DDoS-Angriffen (Denial-of-Service) sowie eine hervorragende Verfügbarkeit. Darüber hinaus ist es nicht erforderlich, externe Firewallports für Ihr lokales Netzwerk zu öffnen, und es ist kein DMZ-Server erforderlich. Es handelt sich ausschließlich um eingehenden Datenverkehr. Eine umfassende Liste der ausgehenden Ports finden Sie unter [Tutorial: Hinzufügen einer lokalen Anwendung für den Remotezugriff über den Anwendungsproxy in Azure Active Directory](./application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

> Der Azure AD-Anwendungsproxy ist ein Feature, das nur verfügbar ist, wenn Sie die Premium- oder Basic-Edition von Azure Active Directory verwenden. Weitere Informationen finden Sie unter [Azure Active Directory – Preise](https://azure.microsoft.com/pricing/details/active-directory/). 
> Wenn Sie über EMS-Lizenzen (Enterprise Mobility Suite) verfügen, können Sie diese Lösung verwenden.
> Der Azure AD-Anwendungsproxy-Collector wird nur unter Windows Server 2012 R2 oder höher installiert. Dies ist auch eine Anforderung des NDES-Servers.

## <a name="install-and-register-the-connector-on-the-ndes-server"></a>Installieren und Registrieren des Connectors auf dem NDES-Server

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) als Anwendungsadministrator des Verzeichnisses an, das den Anwendungsproxy verwendet. Wenn die Mandantendomäne also beispielsweise „contoso.com“ lautet, muss sich der Administrator als admin@contoso.com oder mit einem anderen Administratoraliasnamen in dieser Domäne anmelden.
1. Wählen Sie rechts oben Ihren Benutzernamen aus. Stellen Sie sicher, dass Sie an einem Verzeichnis angemeldet sind, für das der Anwendungsproxy verwendet wird. Wählen Sie **Verzeichnis wechseln**, und wählen Sie ein Verzeichnis aus, für das der Anwendungsproxy verwendet wird, falls Sie das Verzeichnis wechseln möchten.
1. Wählen Sie im Navigationsbereich auf der linken Seite die Option **Azure Active Directory**.
1. Wählen Sie unter **Verwalten** die Option **Anwendungsproxy**.
1. Wählen Sie **Connectordienst herunterladen**.

    ![Herunterladen des Connectordiensts zum Anzeigen der Nutzungsbedingungen](./media/active-directory-app-proxy-protect-ndes/application-proxy-download-connector-service.png)

1. Lesen Sie die Vertragsbedingungen. Wählen Sie die Option **Bedingungen akzeptieren und herunterladen**, wenn Sie mit dem Lesen fertig sind.
1. Kopieren Sie die Setupdatei für den Azure AD-Anwendungsproxy-Connector auf Ihrem NDES-Server. 
   > Sie können den Connector auf jedem beliebigen Server innerhalb Ihres Unternehmensnetzwerks mit Zugriff auf NDES installieren. Sie müssen ihn nicht auf dem NDES-Server installieren.
1. Führen Sie die Setupdatei – z. B. *AADApplicationProxyConnectorInstaller.exe* – aus. Akzeptieren Sie die Softwarelizenzbedingungen.
1. Während der Installation werden Sie aufgefordert, den Connector mit dem Anwendungsproxy in Ihrer Azure AD-Instanz zu registrieren.
   * Geben Sie die Anmeldeinformationen eines globalen oder Anwendungsadministrators für Ihr Azure AD-Verzeichnis an. Die Anmeldeinformationen für den globalen Azure AD-Administrator oder Anwendungsadministrator können sich von den Azure-Anmeldeinformationen im Portal unterscheiden.

        > [!NOTE]
        > Das zum Registrieren des Connectors verwendete globale Administratorkonto oder Anwendungsadministratorkonto muss demselben Verzeichnis angehören, in dem Sie den Anwendungsproxydienst aktivieren.
        >
        > Wenn die Azure AD-Domäne beispielsweise *contoso.com* lautet, muss sich der globale Administrator bzw. Anwendungsadministrator als `admin@contoso.com` oder mit einem anderen gültigen Aliasnamen in dieser Domäne anmelden.

   * Falls auf dem Server, auf dem Sie den Connector installieren, die Option „Verstärkte Sicherheitskonfiguration für Internet Explorer“ aktiviert ist, ist der Registrierungsbildschirm möglicherweise blockiert. Zum Zulassen des Zugriffs befolgen Sie die Anweisungen in der Fehlermeldung, um „Verstärkte Sicherheitskonfiguration für Internet Explorer“ während des Installationsvorgangs zu deaktivieren.
   * Falls bei der Connectorregistrierung ein Fehler auftritt, helfen Ihnen die Informationen unter [Problembehandlung von Anwendungsproxys](application-proxy-troubleshoot.md) weiter.
1. Am Ende des Setupprozesses wird ein Hinweis zu Umgebungen mit einem ausgehenden Proxy angezeigt. Um den Azure AD-Anwendungsproxyconnector so zu konfigurieren, dass er mit dem ausgehenden Proxy funktioniert, führen Sie das bereitgestellte Skript aus, z. B. `C:\Program Files\Microsoft AAD App Proxy connector\ConfigureOutBoundProxy.ps1`.
1. Auf der Azure-Portalseite mit Anwendungsproxys wird der neue Connector mit dem Status *Aktiv* aufgeführt, wie im folgenden Beispiel gezeigt:

    ![Der neue Azure AD-Anwendungsproxyconnector mit dem Status „Aktiv“ im Azure-Portal](./media/active-directory-app-proxy-protect-ndes/connected-app-proxy.png)

    > [!NOTE]
    > Um Hochverfügbarkeit für Anwendungen bereitzustellen, die sich über den Azure AD-Anwendungsproxy authentifizieren, können Sie Connectors auf mehreren VMs installieren. Wiederholen Sie die im vorherigen Abschnitt aufgeführten Schritte, um den Connector auf weiteren Servern zu installieren, die in die über Azure AD DS verwaltete Domäne eingebunden sind.

1. Wechseln Sie nach der erfolgreichen Installation zurück zum Azure-Portal.

1. Wählen Sie **Unternehmensanwendungen**.

   ![Hinzuziehen der richtigen Beteiligten](./media/active-directory-app-proxy-protect-ndes/azure-active-directory-enterprise-applications.png)

1. Klicken Sie zunächst auf **Neue Anwendung** und dann auf **Lokale Anwendung**. 

1. Konfigurieren Sie unter **Fügen Sie Ihre eigene lokale Anwendung hinzu** die folgenden Felder:

   * **Name**: Geben Sie einen Namen für die Anwendung ein.
   * **Interne URL:** Geben Sie die interne URL bzw. den vollqualifizierten Domänennamen Ihres NDES-Servers ein, auf dem Sie den Connector installiert haben.
   * **Vor der Authentifizierung:** Klicken Sie auf **Passthrough**. Es ist nicht möglich, eine beliebige Form der Vorauthentifizierung zu verwenden. Das Protokoll, das für Zertifikatanforderungen (SCEP) verwendet wird, bietet keine solche Option.
   * Kopieren Sie die angegebene **externe URL** in die Zwischenablage.

1. Klicken Sie auf **Hinzufügen**, um die Anwendung zu speichern.

1. Überprüfen Sie, ob Sie über den Azure AD-Anwendungsproxy auf Ihren NDES-Server zugreifen können, indem Sie den in Schritt 10 kopierten Link in einen Browser einfügen. Es sollte eine Standardwillkommensseite von IIS angezeigt werden.

1. Fügen Sie der vorhandenen URL, die Sie im vorherigen Schritt eingefügt haben, als letzten Test den Pfad *mscep.dll* hinzu:

   https://scep-test93635307549127448334.msappproxy.net/certsrv/mscep/mscep.dll

1. Es sollte die Antwort **HTTP Error 403 – Forbidden** (HTTP-Fehler 403: Unzulässig) angezeigt werden.

1. Ändern Sie die (über Microsoft Intune) für die Geräte angegebene NDES-URL. Sie können diese Änderung im Microsoft Endpoint Configuration Manager oder im Admin Center von Microsoft Endpoint Manager vornehmen.

   * Wenn Sie den Configuration Manager verwenden, wechseln Sie zum Zertifikatregistrierungspunkt, und passen Sie die URL an. Diese URL wird von den Geräten abgerufen, woraufhin das Problem benannt wird.
   * Wenn Sie eigenständiges Intune verwenden, erstellen oder bearbeiten Sie eine SCEP-Richtlinie, und fügen Sie die neue URL hinzu.

## <a name="next-steps"></a>Nächste Schritte

Wenn der Azure AD-Anwendungsproxy in NDES integriert ist, können Sie die Anwendungen für den Zugriff durch Benutzer freigeben. Weitere Informationen finden Sie unter [Veröffentlichen von Anwendungen mit dem Azure AD-Anwendungsproxy](./application-proxy-add-on-premises-application.md).