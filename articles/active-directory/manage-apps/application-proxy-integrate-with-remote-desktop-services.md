---
title: Veröffentlichen von Remotedesktops mit dem Azure AD-App-Proxy | Microsoft-Dokumentation
description: Hier finden Sie grundlegende Informationen zu Azure AD-Anwendungsproxy-Connectors.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: mimart
ms.custom: it-pro
ms.reviewer: harshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ecd55cbb3a8eefc150db731901458561d90f033
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65783402"
---
# <a name="publish-remote-desktop-with-azure-ad-application-proxy"></a>Veröffentlichen des Remotedesktops per Azure AD-Anwendungsproxy

Der Remotedesktopdienst und der Azure AD-Anwendungsproxy verbessern zusammen die Produktivität von Mitarbeitern, die außerhalb des Unternehmensnetzwerks arbeiten. 

Dieser Artikel richtet sich an folgende Zielgruppe:
- Bestehende Kunden des Anwendungsproxys, die durch Veröffentlichung von lokalen Anwendungen über Remotedesktopdienste mehrere Anwendungen für ihre Endbenutzer anbieten möchten
- Bestehende Kunden der Remotedesktopdienste, die die Angriffsfläche ihrer Bereitstellung mit dem Azure AD-Anwendungsproxy reduzieren möchten. Dieses Szenario bietet eine begrenzte Anzahl von Steuerelementen zur zweistufigen Überprüfung und für den bedingten Zugriff auf RDS.

## <a name="how-application-proxy-fits-in-the-standard-rds-deployment"></a>Informationen zur Rolle des Anwendungsproxys bei standardmäßigen RDS-Bereitstellungen

Eine standardmäßige RDS-Bereitstellung umfasst verschiedene Remotedesktop-Rollendienste, die auf Windows Server ausgeführt werden. Die [Architektur der Remotedesktopdienste](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) bietet mehrere Bereitstellungsoptionen. Im Gegensatz zu den RDS-Bereitstellungsoptionen weist die [RDS-Bereitstellung mit Azure AD-Anwendungsproxy](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/desktop-hosting-logical-architecture) (in der folgenden Abbildung dargestellt) über eine permanente ausgehende Verbindung von dem Server auf, auf dem der Connectordienst ausgeführt wird. Bei anderen Bereitstellungen werden eingehende Verbindungen durch einen Lastenausgleich offen gelassen.

![Der Anwendungsproxy befindet sich zwischen der RDS-VM und dem öffentlichen Internet.](./media/application-proxy-integrate-with-remote-desktop-services/rds-with-app-proxy.png)

Bei einer RDS-Bereitstellung werden die Rollen „RD-Web“ und „RD-Gateway“ auf Computern mit Internetzugriff ausgeführt. Diese Endpunkte werden aus folgenden Gründen zur Verfügung gestellt:
- Durch RD-Web wird ein öffentlicher Endpunkt für den Benutzer bereitgestellt, um sich bei den verschiedenen lokalen Anwendungen und Desktops, auf die sie zugreifen können, anzumelden und diese anzuzeigen. Nach Auswahl einer Ressource wird eine RDP-Verbindung mithilfe der systemeigenen App im Betriebssystem erstellt.
- RD-Gateway kommt ins Spiel, sobald ein Benutzer die RDP-Verbindung startet. Das RD-Gateway verarbeitet den verschlüsselten RDP-Datenverkehr, der über das Internet eingeht, und übersetzt diesen auf dem lokalen Server, mit dem der Benutzer eine Verbindung herstellt. In diesem Szenario stammt der im RD-Gateway eingehende Datenverkehr vom Azure AD-Anwendungsproxy.

>[!TIP]
>Wenn Sie RDS zum ersten Mal bereitstellen oder weitere Informationen wünschen, bevor Sie beginnen, lesen Sie den Abschnitt [Nahtlose Bereitstellung von RDS mit Azure Resource Manager und dem Azure Marketplace](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure).

## <a name="requirements"></a>Requirements (Anforderungen)

- Verwenden Sie einen anderen Client als den Remotedesktop-Webclient, da der Webclient Anwendungsproxy nicht unterstützt.

- Die RD-Web- und RD-Gateway-Endpunkte müssen sich auf demselben Computer befinden und ein gemeinsames Stamm verwenden. RD-Web und RD-Gateway werden auf dem Anwendungsproxy als einzelne Anwendung veröffentlicht, damit Sie über die Möglichkeit für einmaliges Anmelden zwischen den beiden Anwendungen verfügen.

- Sie sollten bereits [RDS bereitgestellt](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-in-azure) und das [Anwendungsproxy aktiviert](application-proxy-add-on-premises-application.md) haben.

- In diesem Szenario wird davon ausgegangen, dass Ihre Endbenutzer mit dem Internet Explorer auf Windows 7- oder Windows 10-Desktops verwenden, die über die Seite von RD-Web eine Verbindung herstellen. Weitere Informationen zur Unterstützung anderer Betriebssysteme finden Sie unter [Unterstützung für andere Clientkonfigurationen](#support-for-other-client-configurations).

- Bei der Veröffentlichung von RD-Web empfiehlt es sich, den gleichen internen und externen FQDN zu verwenden. Wenn sich die internen und externen FQDNs unterscheiden, deaktivieren Sie die Anforderungsgheaderübersetzung, um zu vermeiden, dass der Client ungültige Links empfängt. 

- Aktivieren Sie im Internet Explorer das RDS-ActiveX-Add-On.

## <a name="deploy-the-joint-rds-and-application-proxy-scenario"></a>Bereitstellen des gemeinsamen RDS- und Anwendungsproxy-Szenarios

Führen Sie nach der Einrichtung von RDS und des Azure AD-Anwendungsproxys für Ihre Umgebung die Schritte durch, um beide Lösungen zu kombinieren. Diese Schritte führen Sie durch die Veröffentlichung der zwei RDS-Endpunkte mit Webzugriff (RD-Web und RD-Gateway) als Anwendungen und anschließenden Weiterleitung des Datenverkehrs in RDS an das Anwendungsproxy.

### <a name="publish-the-rd-host-endpoint"></a>Veröffentlichen des RD-Hostendpunkts

1. [Veröffentlichen Sie anhand der folgenden Werten eine neue Anwendung mit Anwendungsproxy](application-proxy-add-on-premises-application.md):
   - Interne URL: `https://\<rdhost\>.com/`, wobei `\<rdhost\>` das gemeinsame Stammverzeichnis ist, das von RD-Web und RD-Gateway gemeinsam genutzt wird.
   - Externe URL: Dieses Feld wird automatisch basierend auf dem Namen der Anwendung aufgefüllt. Sie können das Feld jedoch ändern. Ihre Benutzer werden beim Zugriff auf RDS an diese URL weitergeleitet.
   - Präauthentifizierungsmethode: Azure Active Directory
   - URL-Header übersetzen: Nein 
2. Weisen Sie Benutzern die veröffentlichte RD-Anwendung zu. Stellen Sie sicher, dass alle Benutzer auch Zugriff auf RDS haben.
3. Legen Sie für die Methode zur einmaligen Anmeldung für die Anwendung **Azure AD-SSO deaktiviert** fest. Ihre Benutzer werden aufgefordert, sich einmal bei Azure AD und einmal bei RD-Web zu authentifizieren, verfügen jedoch über die Möglichkeit zur einmaligen Anmeldung beim RD-Gateway.
4. Navigieren Sie zu **Azure Active Directory** > **App-Registrierungen** > *Ihre Anwendung* > **Einstellungen**.
5. Wählen Sie **Eigenschaften**, und aktualisieren Sie das Feld **URL der Startseite**, sodass auf Ihren RD-Web-Endpunkt verwiesen wird (z.B. `https://\<rdhost\>.com/RDWeb`).

### <a name="direct-rds-traffic-to-application-proxy"></a>Weiterleiten des RDS-Datenverkehrs an den Anwendungsproxy

Stellen Sie eine Verbindung mit der RDS-Bereitstellung als Administrator her und ändern Sie den Remotedesktop-Gatewayservernamen für die Bereitstellung. Durch diese Konfiguration wird sichergestellt, dass Verbindungen über den Azure AD-Anwendungsproxydienst hergestellt werden.

1. Stellen Sie eine Verbindung zum RDS-Server her, auf dem die Rolle „Remotedesktop-Verbindungsbroker“ ausgeführt wird.
2. Starten Sie den **Server-Manager**.
3. Wählen Sie im Bereich auf der linken Seite **Remotedesktopdienste**.
4. Wählen Sie **Übersicht**.
5. Klicken Sie im Abschnitt „Bereitstellungsübersicht“ auf das Dropdownmenü und wählen Sie **Bereitstellungseigenschaften bearbeiten**.
6. Ändern Sie auf der Registerkarte „RD-Gateway“ das Feld **Servername** in die externe URL, die Sie für den RD-Hostendpunkt im Anwendungsproxy festgelegt haben.
7. Ändern Sie das Feld **Anmeldemethode** in **Kennwortauthentifizierung**.

   ![Bildschirm „Bereitstellungseigenschaften“ in RDS](./media/application-proxy-integrate-with-remote-desktop-services/rds-deployment-properties.png)

8. Führen Sie diesen Befehl für jede Sammlung aus. Ersetzen Sie *\<yourcollectionname\>* und *\<proxyfrontendurl\>* durch Ihre eigenen Information. Dieser Befehl ermöglicht eine einmalige Anmeldung zwischen RD-Web und RD-Gateway und optimiert die Leistung:

   ```
   Set-RDSessionCollectionConfiguration -CollectionName "<yourcollectionname>" -CustomRdpProperty "pre-authentication server address:s:<proxyfrontendurl>`nrequire pre-authentication:i:1"
   ```

   **Beispiel:**
   ```
   Set-RDSessionCollectionConfiguration -CollectionName "QuickSessionCollection" -CustomRdpProperty "pre-authentication server address:s:https://remotedesktoptest-aadapdemo.msappproxy.net/`nrequire pre-authentication:i:1"
   ```
   >[!NOTE]
   >Im obigen Befehl wird in „`nrequire“ ein Graviszeichen verwendet.

9. Führen den folgenden Befehl aus, um die Änderung der benutzerdefinierten RDP-Eigenschaften zu überprüfen und um den Inhalt der RDP-Datei anzuzeigen, der für diese Sammlung aus RDWeb heruntergeladen wird:
    ```
    (get-wmiobject -Namespace root\cimv2\terminalservices -Class Win32_RDCentralPublishedRemoteDesktop).RDPFileContents
    ```

Nachdem Sie nun den Remotedesktop konfiguriert haben, fungiert der Azure AD-Anwendungsproxy als RDS-Komponente mit Internetzugriff. Sie können die anderen Endpunkte mit öffentlichem Internetzugriff auf Ihren RD-Web- und RD-Gateway-Computern entfernen.

## <a name="test-the-scenario"></a>Prüfen des Szenarios

Prüfen Sie das Szenario mit Internet Explorer auf einem Windows 7- oder 10-Computer.

1. Navigieren Sie zur externen URL, die Sie eingerichtet haben, oder suchen Sie im Bereich [Meine Apps](https://myapps.microsoft.com) nach Ihrer Anwendung.
2. Sie werden aufgefordert, sich bei Azure Active Directory zu authentifizieren. Melden Sie sich mit einem Konto an, das Sie der Anwendung zugewiesen haben.
3. Sie werden aufgefordert, sich bei RD-Web zu authentifizieren.
4. Nach Ihrer erfolgreichen RDS-Authentifizierung können Sie den gewünschten Desktop oder die gewünschte Anwendung auswählen und loslegen.

## <a name="support-for-other-client-configurations"></a>Unterstützung für andere Clientkonfigurationen

Die in diesem Artikel beschriebene Konfiguration ist für Benutzer unter Windows 7 oder Windows 10 mit dem Internet Explorer sowie dem RDS-ActiveX-Add-On gedacht. Wenn es erforderlich ist, können jedoch auch andere Betriebssysteme oder Browser unterstützt werden. Der Unterschied besteht in der verwendeten Authentifizierungsmethode.

| Authentifizierungsmethode | Unterstützte Clientkonfiguration |
| --------------------- | ------------------------------ |
| Vorauthentifizierung    | Windows 7/10 mit dem Internet Explorer + RDS-ActiveX-Add-On |
| Passthrough | Alle anderen Betriebssysteme, die die Microsoft-Remotedesktopanwendung unterstützen |

Der Vorauthentifizierungsflow bietet weitere Vorteile im Hinblick auf Sicherheit als die der Passthroughflow. Mit der Vorauthentifizierung können Sie Authentifizierungsfeatures von Azure AD nutzen, z.B. das einmalige Anmelden, den bedingten Zugriff und die zweistufige Überprüfung für Ihre lokalen Ressourcen. Sie stellen Sie sicher, dass nur authentifizierter Datenverkehr Ihr Netzwerk erreicht.

Um die Passthrough-Authentifizierung zu verwenden, müssen nur zwei Änderungen an den Schritten in diesem Artikel ausgeführt werden:
1. Legen Sie in Schritt 1: [Veröffentlichen des RD-Hostendpunkts](#publish-the-rd-host-endpoint) die Vorauthentifizierungsmethode auf **Passthrough** fest.
2. Überspringen Sie unter [Weiterleiten des RDS-Datenverkehrs an den Anwendungsproxy](#direct-rds-traffic-to-application-proxy) Schritt 8 vollständig.

## <a name="next-steps"></a>Nächste Schritte

[Aktivieren des Remotezugriffs auf SharePoint per Azure AD-Anwendungsproxy](application-proxy-integrate-with-sharepoint-server.md)  
[Sicherheitsaspekte beim Remotezugriff auf Apps mit dem Azure AD-Anwendungsproxy](application-proxy-security.md)
