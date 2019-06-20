---
title: Konfigurieren der Azure Active Directory-Hybrideinbindung für verwaltete Domänen | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie die Azure Active Directory-Hybrideinbindung für verwaltete Domänen konfigurieren.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f9daeb5e0de9c53f16efff46e02015acfa7c521
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734608"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Tutorial: Konfigurieren der Azure Active Directory-Hybrideinbindung für verwaltete Domänen

Ähnlich wie ein Benutzer ist ein Gerät eine weitere zentrale Identität, die Sie schützen sowie jederzeit und überall zum Schutz Ihrer Ressourcen verwenden können. Dafür können Sie die Geräteidentitäten mit einer der folgenden Methoden in Azure AD bereitstellen und verwalten:

- Azure AD-Einbindung
- Azure AD-Hybrideinbindung
- Azure AD-Registrierung

Durch das Bereitstellen Ihrer Geräte in Azure AD maximieren Sie die Produktivität Ihrer Benutzer durch einmaliges Anmelden (SSO) für Ihre gesamten Cloud- und lokalen Ressourcen. Gleichzeitig können Sie den Zugriff auf Ihre Cloud- und lokalen Ressourcen durch den [bedingten Zugriff](../active-directory-conditional-access-azure-portal.md) sichern.

In diesem Tutorial erfahren Sie, wie die Azure AD-Hybrideinbindung für in die AD-Domäne eingebundene Computer in einer verwalteten Umgebung konfiguriert wird. 

Eine verwaltete Umgebung kann entweder durch [Kennworthashsynchronisierung](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) (Password Hash Sync, PHS) oder [Passthrough-Authentifizierung](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) (Pass Through Authentication, PTA) mit [nahtlosem einmaligem Anmelden](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) (Seamless Single Sign-On, Seamless SSO) bereitgestellt werden.
In diesen Szenarien müssen Sie keinen Verbundserver für die Authentifizierung konfigurieren.

> [!div class="checklist"]
> * Konfigurieren der Hybrid-Azure AD-Einbindung
> * Aktivieren von kompatiblen Windows-Geräten
> * Überprüfen der eingebundenen Geräte
> * Problembehandlung

## <a name="prerequisites"></a>Voraussetzungen

In diesem Tutorial wird vorausgesetzt, dass Sie mit Folgendem vertraut sind:

- [Einführung in die Geräteidentitätsverwaltung in Azure Active Directory](../device-management-introduction.md)
- [Planen der Implementierung einer Azure Active Directory-Hybrideinbindung](hybrid-azuread-join-plan.md)
- [Kontrollierte Überprüfung der Azure AD-Hybrideinbindung](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD unterstützt keine Smartcards oder Zertifikate in verwalteten Domänen.

Zum Konfigurieren des Szenarios in diesem Artikel muss die [aktuelle Version von Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 oder höher) installiert sein.

Vergewissern Sie sich, dass Azure AD die Computerobjekte der Geräte für die Azure AD-Hybrideinbindung mit Azure AD synchronisiert. Wenn die Computerobjekte zu bestimmten Organisationseinheiten (OEs) gehören, müssen diese OEs auch für die Synchronisierung in Azure AD Connect konfiguriert werden. Weitere Informationen zum Synchronisieren von Computerobjekten mit Azure AD Connect finden Sie im Artikel [Azure AD Connect-Synchronisierung: Konfigurieren der Filterung](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering#organizational-unitbased-filtering).

Ab Version 1.1.819.0 bietet Azure AD Connect einen Assistenten für die Konfiguration der Azure AD-Hybrideinbindung. Mit dem Assistenten können Sie den Konfigurationsprozess erheblich vereinfachen. Der entsprechende Assistent konfiguriert die Dienstverbindungspunkte (SCP) für die Geräteregistrierung.

Die Konfigurationsschritte in diesem Artikel basieren auf diesen Assistenten.

für die Azure AD-Hybrideinbindung ist erforderlich, dass die Geräte innerhalb des Netzwerks Ihrer Organisation auf die folgenden Microsoft-Ressourcen zugreifen können:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- [https://autologon.microsoftazuread-sso.com](`https://autologon.microsoftazuread-sso.com`) (Wenn Sie das nahtlose einmalige Anmelden verwenden oder verwenden möchten)

Wenn für Ihre Organisation Zugriff auf das Internet über einen ausgehenden Proxy erforderlich ist, empfiehlt Microsoft die [Implementierung von WPAD (Web Proxy AutoDiscovery, Webproxy-AutoErmittlung)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)), damit Windows 10-Computer Geräte bei Azure AD registrieren können. Wenn beim Konfigurieren und Verwalten von WPAD Probleme auftreten, lesen Sie die Informationen unter [Troubleshooting Automatic Detection](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10)) (Problembehandlung bei der automatischen Ermittlung). 

Wenn Sie nicht die Webproxy-AutoErmittlung verwenden, aber Proxyeinstellungen auf Ihrem Computer konfigurieren müssen, können Sie dazu ab Windows 10 1709 [WinHTTP-Einstellungen mithilfe eines Gruppenrichtlinienobjekts (Group Policy Object, GPO) konfigurieren](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Wenn Sie Proxyeinstellungen auf Ihrem Computer mithilfe von WinHTTP-Einstellungen konfigurieren, können alle Computer, die keine Verbindung mit dem konfigurierten Proxy herstellen können, auch keine Internetverbindung herstellen.

Wenn Ihre Organisation Internetzugriff über einen authentifizierten ausgehenden Proxy erfordert, müssen Sie sicherstellen, dass Ihre Windows 10-Computer erfolgreich beim ausgehenden Proxy authentifiziert werden können. Da Windows 10-Computer die Geräteregistrierung mithilfe von Computerkontext ausführen, muss die Authentifizierung bei ausgehenden Proxys mithilfe von Computerkontext konfiguriert werden. Erkundigen Sie sich beim Anbieter Ihres ausgehenden Proxys nach den Konfigurationsanforderungen.

## <a name="configure-hybrid-azure-ad-join"></a>Konfigurieren der Hybrid-Azure AD-Einbindung

Zum Konfigurieren einer Azure AD-Hybrideinbindung mithilfe von Azure AD Connect benötigen Sie Folgendes:

- Die Anmeldeinformationen eines globalen Administrators für Ihren Azure AD-Mandanten.  
- Die Anmeldeinformationen eines Unternehmensadministrators für jede Gesamtstruktur.

**Konfigurieren einer Azure AD-Hybrideinbindung mithilfe von Azure AD Connect:**

1. Starten Sie Azure AD Connect, und klicken Sie dann auf **Konfigurieren**.

   ![Willkommen](./media/hybrid-azuread-join-managed-domains/11.png)

1. Wählen Sie auf der Seite **Zusätzliche Aufgaben** die Option **Geräteoptionen konfigurieren**, und klicken Sie dann auf **Weiter**.

   ![Zusätzliche Aufgaben](./media/hybrid-azuread-join-managed-domains/12.png)

1. Klicken Sie auf der Seite **Übersicht** auf **Weiter**.

   ![Übersicht](./media/hybrid-azuread-join-managed-domains/13.png)

1. Geben Sie auf der Seite **Mit Azure AD verbinden** die Anmeldeinformationen eines globalen Administrators für Ihren Azure AD-Mandanten ein.  

   ![Stellen Sie eine Verbindung mit Azure AD her.](./media/hybrid-azuread-join-managed-domains/14.png)

1. Wählen Sie auf der Seite **Geräteoptionen** die Option **Konfigurieren einer Azure AD-Hybrideinbindung** aus, und klicken Sie dann auf **Weiter**.

   ![Geräteoptionen](./media/hybrid-azuread-join-managed-domains/15.png)

1. Führen Sie auf der Seite **SCP** für jede Gesamtstruktur, in der Azure AD Connect das SCP konfigurieren soll, die folgenden Schritte aus, und klicken Sie dann auf **Weiter**:

   ![SCP](./media/hybrid-azuread-join-managed-domains/16.png)

   1. Wählen Sie die Gesamtstruktur aus.
   1. Wählen Sie den Authentifizierungsdienst aus.
   1. Klicken Sie auf **Hinzufügen**, um die Anmeldeinformationen eines Unternehmensadministrators einzugeben.

1. Wählen Sie auf der Seite **Gerätebetriebssysteme** die von den Geräten in Ihrer Active Directory-Umgebung verwendeten Betriebssysteme aus, und klicken Sie dann auf **Weiter**.

   ![Gerätebetriebssystem](./media/hybrid-azuread-join-managed-domains/17.png)

1. Klicken Sie auf der Seite **Bereit zur Konfiguration** auf **Konfigurieren**.

   ![Bereit zur Konfiguration](./media/hybrid-azuread-join-managed-domains/19.png)

1. Klicken Sie auf der Seite **Konfiguration abgeschlossen** auf **Beenden**.

   ![Konfiguration abgeschlossen](./media/hybrid-azuread-join-managed-domains/20.png)

## <a name="enable-windows-down-level-devices"></a>Aktivieren von kompatiblen Windows-Geräten

Wenn es sich bei einigen Ihrer in die Domäne eingebundenen Geräte um kompatible Windows-Geräte handelt, gehen Sie wie folgt vor:

- Konfigurieren der lokalen Intraneteinstellungen für die Geräteregistrierung
- Konfigurieren des nahtlosen einmaligen Anmeldens (Single Sign-On, SSO)
- Installieren von Microsoft Workplace Join für kompatible Windows-Computer

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurieren der lokalen Intraneteinstellungen für die Geräteregistrierung

Um die Einbindung in Hybrid-Azure AD für Ihre kompatiblen Windows-Geräte erfolgreich abzuschließen und Zertifikataufforderungen bei der Authentifizierung von Geräten gegenüber Azure AD zu vermeiden, können Sie eine Richtlinie auf Ihre in die Domäne eingebundenen Geräte übertragen, mit der die folgende URL in Internet Explorer der Zone „Lokales Intranet“ hinzugefügt wird:

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

Außerdem müssen Sie in der lokalen Intranetzone des Benutzers die Option **Aktualisierungen der Statusleiste per Skript zulassen** aktivieren.

### <a name="configure-seamless-sso"></a>Konfigurieren des nahtlosen einmaligen Anmeldens

Sie müssen außerdem [nahtloses SSO konfigurieren](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso-quick-start#step-2-enable-the-feature), um die Azure AD-Hybrideinbindung Ihrer kompatiblen Windows-Geräte in einer verwalteten Domäne erfolgreich abzuschließen, die als Authentifizierungsmethode für die Azure AD-Cloud [Kennworthashsynchronisierung (PHS)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) oder [Passthrough-Authentifizierung (PTA)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) verwendet.

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>Installieren von Microsoft Workplace Join für kompatible Windows-Computer

Zur Registrierung von kompatiblen Windows-Geräten müssen Organisationen [Microsoft Workplace Join für Computer installieren, auf denen nicht Windows 10 ausgeführt wird](https://www.microsoft.com/download/details.aspx?id=53554) (verfügbar im Microsoft Download Center).

Sie können das Paket mithilfe eines Softwareverteilungssystems wie  [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager) bereitstellen. Das Paket unterstützt die Standardoptionen für die Installation im Hintergrund unter Verwendung des quiet-Parameters. Configuration Manager Current Branch bietet zusätzliche Vorteile gegenüber früheren Versionen, z.B. die Möglichkeit zur Nachverfolgung abgeschlossener Registrierungen.

Das Installationsprogramm erstellt einen geplanten Task auf dem System, der im Kontext des Benutzers ausgeführt wird. Der Task wird ausgelöst, wenn sich der Benutzer bei Windows anmeldet. Nach der Authentifizierung durch Azure AD verknüpft die Aufgabe das Gerät unter Verwendung der Benutzeranmeldeinformationen mit Azure AD.

## <a name="verify-the-registration"></a>Überprüfen der Registrierung

Zum Überprüfen des Geräteregistrierungsstatus in Ihrem Azure-Mandanten können Sie das Cmdlet **[Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice)** im **[Azure Active Directory PowerShell-Modul](/powershell/azure/install-msonlinev1?view=azureadps-2.0)** verwenden.

Bei Verwendung des Cmdlets **Get-MSolDevice** zur Überprüfung der Dienstdetails:

- Ein Objekt mit der **Geräte-ID**, die der ID auf dem Windows-Client entspricht, muss vorhanden sein.
- Der Wert für **DeviceTrustType** muss **Domänenbeitritt** sein. Dies entspricht dem Status **Hybrid in Azure AD eingebunden** auf der Seite „Geräte“ im Azure AD-Portal.
- Für Geräte, die für den bedingten Zugriff verwendet werden, muss **Aktiviert** den Wert **True** und **DeviceTrustLevel** den Wert **Verwaltet** haben.

**So überprüfen Sie die Dienstdetails:**

1. Öffnen Sie **Windows PowerShell** als Administrator.
1. Geben Sie `Connect-MsolService` ein, um die Verbindung mit Ihrem Azure-Mandanten herzustellen.  
1. Geben Sie `get-msoldevice -deviceId <deviceId>`ein.
1. Vergewissern Sie sich, dass **Aktiviert** auf **True** festgelegt ist.

## <a name="troubleshoot-your-implementation"></a>Problembehandlung bei der Implementierung

Wenn bei der Azure AD-Hybrideinbindung für in Domänen eingebundene Windows-Geräte Probleme auftreten, finden Sie weitere Informationen unter:

- [Problembehandlung für in Azure AD eingebundene aktuelle Windows-Hybridgeräte](troubleshoot-hybrid-join-windows-current.md)
- [Problembehandlung für in Azure AD eingebundene kompatible Windows-Hybridgeräte](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zur Verwaltung von Geräten im Azure AD-Portal finden Sie unter [Verwalten von Geräten mithilfe des Azure-Portals](device-management-azure-portal.md).
