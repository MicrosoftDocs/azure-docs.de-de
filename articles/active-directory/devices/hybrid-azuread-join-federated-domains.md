---
title: Konfigurieren der Azure Active Directory-Hybrideinbindung für Verbunddomänen | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie die Azure Active Directory-Hybrideinbindung für Verbunddomänen konfigurieren.
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
ms.openlocfilehash: 600d6b9f1eb8d8073e1658dd5b8196a3d8137e42
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66733716"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Tutorial: Konfigurieren der Azure Active Directory-Hybrideinbindung für Verbunddomänen

Ähnlich wie ein Benutzer ist ein Gerät eine weitere zentrale Identität, die Sie schützen sowie jederzeit und überall zum Schutz Ihrer Ressourcen verwenden können. Dafür können Sie die Geräteidentitäten mit einer der folgenden Methoden in Azure AD bereitstellen und verwalten:

- Azure AD-Einbindung
- Azure AD-Hybrideinbindung
- Azure AD-Registrierung

Durch das Bereitstellen Ihrer Geräte in Azure AD maximieren Sie die Produktivität Ihrer Benutzer durch einmaliges Anmelden (SSO) für Ihre gesamten Cloud- und lokalen Ressourcen. Gleichzeitig können Sie den Zugriff auf Ihre Cloud- und lokalen Ressourcen durch den [bedingten Zugriff](../active-directory-conditional-access-azure-portal.md) sichern.

In diesem Tutorial erfahren Sie, wie die Azure AD-Hybrideinbindung für in die AD-Domäne eingebundene Computer in einer Verbundumgebung mit AD FS konfiguriert wird.

> [!NOTE]
> Wenn Ihre Verbundumgebung einen anderen Identitätsanbieter als AD FS verwendet, müssen Sie sicherstellen, dass dieser Identitätsanbieter das WS-Trust-Protokoll unterstützt. WS-Trust ist erforderlich, um Ihre aktuellen über Azure AD Hybrid Join eingebundenen Windows-Geräte mit Azure AD zu authentifizieren. Falls Sie kompatible Windows-Geräte besitzen, die Sie über Azure AD Hybrid Join einbinden müssen, muss Ihr Identitätsanbieter darüber hinaus den Anspruch WIAORMULTIAUTHN unterstützen. 


> [!div class="checklist"]
> * Konfigurieren der Hybrid-Azure AD-Einbindung
> * Aktivieren von kompatiblen Windows-Geräten
> * Überprüfen der Registrierung
> * Problembehandlung

## <a name="prerequisites"></a>Voraussetzungen

In diesem Tutorial wird vorausgesetzt, dass Sie mit Folgendem vertraut sind:

- [Einführung in die Geräteidentitätsverwaltung in Azure Active Directory](../device-management-introduction.md)
- [Planen der Implementierung einer Azure Active Directory-Hybrideinbindung](hybrid-azuread-join-plan.md)
- [Kontrollierte Überprüfung der Azure AD-Hybrideinbindung](hybrid-azuread-join-control.md)

Für die Konfiguration des Szenarios in diesem Tutorials benötigen Sie Folgendes:

- Windows Server 2012 R2 mit AD FS
- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) ab Version 1.1.819.0

Ab Version 1.1.819.0 bietet Azure AD Connect einen Assistenten für die Konfiguration der Azure AD-Hybrideinbindung. Mit dem Assistenten können Sie den Konfigurationsprozess erheblich vereinfachen. Der entsprechende Assistent:

- Konfiguriert die Dienstverbindungspunkte (SCP) für die Geräteregistrierung
- Sichert Ihre vorhandene Azure AD-Vertrauensstellung der vertrauenden Seite
- Aktualisiert die Anspruchsregeln in Ihrer Azure AD-Vertrauensstellung

Die Konfigurationsschritte in diesem Artikel basieren auf diesen Assistenten. Wenn Sie eine ältere Version von Azure AD Connect installiert haben, müssen Sie sie mindestens auf 1.1.819 aktualisieren. Wenn das Installieren der aktuellen Version von Azure AD Connect keine Option für Sie ist, informieren Sie sich über die [manuelle Konfiguration der Azure AD-Hybrideinbindung](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-manual).

für die Azure AD-Hybrideinbindung ist erforderlich, dass die Geräte innerhalb des Netzwerks Ihrer Organisation auf die folgenden Microsoft-Ressourcen zugreifen können:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- STS Ihrer Organisation (Verbunddomänen)
- [https://autologon.microsoftazuread-sso.com](`https://autologon.microsoftazuread-sso.com`) (Wenn Sie das nahtlose einmalige Anmelden verwenden oder verwenden möchten)

Ab Windows 10 1803 gilt Folgendes: Wenn für die sofortige Azure AD-Hybrideinbindung für Verbundumgebungen wie AD FS ein Fehler auftritt, nutzen wir Azure AD Connect, um das Computerobjekt in Azure AD zu synchronisieren. Dieses Objekt wird anschließend verwendet, um die Geräteregistrierung für die Azure AD-Hybrideinbindung durchzuführen. Vergewissern Sie sich, dass Azure AD die Computerobjekte der Geräte für die Azure AD-Hybrideinbindung mit Azure AD synchronisiert. Wenn die Computerobjekte zu bestimmten Organisationseinheiten (OEs) gehören, müssen diese OEs auch für die Synchronisierung in Azure AD Connect konfiguriert werden. Weitere Informationen zum Synchronisieren von Computerobjekten mit Azure AD Connect finden Sie im Artikel [Azure AD Connect-Synchronisierung: Konfigurieren der Filterung](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering#organizational-unitbased-filtering).

Wenn für Ihre Organisation Zugriff auf das Internet über einen ausgehenden Proxy erforderlich ist, empfiehlt Microsoft die [Implementierung von WPAD (Web Proxy AutoDiscovery, Webproxy-AutoErmittlung)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)), damit Windows 10-Computer Geräte bei Azure AD registrieren können. Wenn beim Konfigurieren und Verwalten von WPAD Probleme auftreten, lesen Sie die Informationen unter [Troubleshooting Automatic Detection](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10)) (Problembehandlung bei der automatischen Ermittlung). 

Wenn Sie nicht die Webproxy-AutoErmittlung verwenden, aber Proxyeinstellungen auf Ihrem Computer konfigurieren müssen, können Sie dazu ab Windows 10 1709 [WinHTTP-Einstellungen mithilfe eines Gruppenrichtlinienobjekts (Group Policy Object, GPO) konfigurieren](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Wenn Sie Proxyeinstellungen auf Ihrem Computer mithilfe von WinHTTP-Einstellungen konfigurieren, können alle Computer, die keine Verbindung mit dem konfigurierten Proxy herstellen können, auch keine Internetverbindung herstellen.

Wenn Ihre Organisation Internetzugriff über einen authentifizierten ausgehenden Proxy erfordert, müssen Sie sicherstellen, dass Ihre Windows 10-Computer erfolgreich beim ausgehenden Proxy authentifiziert werden können. Da Windows 10-Computer die Geräteregistrierung mithilfe von Computerkontext ausführen, muss die Authentifizierung bei ausgehenden Proxys mithilfe von Computerkontext konfiguriert werden. Erkundigen Sie sich beim Anbieter Ihres ausgehenden Proxys nach den Konfigurationsanforderungen.

## <a name="configure-hybrid-azure-ad-join"></a>Konfigurieren der Hybrid-Azure AD-Einbindung

Zum Konfigurieren einer Azure AD-Hybrideinbindung mithilfe von Azure AD Connect benötigen Sie Folgendes:

- Die Anmeldeinformationen eines globalen Administrators für Ihren Azure AD-Mandanten.  
- Die Anmeldeinformationen eines Unternehmensadministrators für jede Gesamtstruktur.
- Die Anmeldeinformationen Ihres AD FS-Administrators.

**Konfigurieren einer Azure AD-Hybrideinbindung mithilfe von Azure AD Connect:**

1. Starten Sie Azure AD Connect, und klicken Sie dann auf **Konfigurieren**.

   ![Willkommen](./media/hybrid-azuread-join-federated-domains/11.png)

1. Wählen Sie auf der Seite **Zusätzliche Aufgaben** die Option **Geräteoptionen konfigurieren**, und klicken Sie dann auf **Weiter**.

   ![Zusätzliche Aufgaben](./media/hybrid-azuread-join-federated-domains/12.png)

1. Klicken Sie auf der Seite **Übersicht** auf **Weiter**.

   ![Übersicht](./media/hybrid-azuread-join-federated-domains/13.png)

1. Geben Sie auf der Seite **Mit Azure AD verbinden** die Anmeldeinformationen eines globalen Administrators für Ihren Azure AD-Mandanten ein und klicken Sie auf **Weiter**.

   ![Stellen Sie eine Verbindung mit Azure AD her.](./media/hybrid-azuread-join-federated-domains/14.png)

1. Wählen Sie auf der Seite **Geräteoptionen** die Option **Konfigurieren einer Azure AD-Hybrideinbindung** aus, und klicken Sie dann auf **Weiter**.

   ![Geräteoptionen](./media/hybrid-azuread-join-federated-domains/15.png)

1. Führen Sie auf der Seite **SCP** die folgenden Schritte aus, und klicken Sie dann auf **Weiter**:

   ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

   1. Wählen Sie die Gesamtstruktur aus.
   1. Wählen Sie den Authentifizierungsdienst aus. Sie müssen AD FS-Server auswählen – es sei denn, Ihre Organisation verfügt ausschließlich über Windows 10-Clients und Sie haben die Computer-/Gerätesynchronisierung konfiguriert, oder Ihre Organisation verwendet SeamlessSSO.
   1. Klicken Sie auf **Hinzufügen**, um die Anmeldeinformationen eines Unternehmensadministrators einzugeben.

1. Wählen Sie auf der Seite **Gerätebetriebssysteme** die von den Geräten in Ihrer Active Directory-Umgebung verwendeten Betriebssysteme aus, und klicken Sie dann auf **Weiter**.

   ![Gerätebetriebssystem](./media/hybrid-azuread-join-federated-domains/17.png)

1. Geben Sie auf der Seite **Verbundkonfiguration** die Anmeldeinformationen Ihres AD FS-Administrators ein, und klicken Sie dann auf **Weiter**.

   ![Verbundkonfiguration](./media/hybrid-azuread-join-federated-domains/18.png)

1. Klicken Sie auf der Seite **Bereit zur Konfiguration** auf **Konfigurieren**.

   ![Bereit zur Konfiguration](./media/hybrid-azuread-join-federated-domains/19.png)

1. Klicken Sie auf der Seite **Konfiguration abgeschlossen** auf **Beenden**.

   ![Konfiguration abgeschlossen](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-down-level-devices"></a>Aktivieren von kompatiblen Windows-Geräten

Wenn es sich bei einigen Ihrer in die Domäne eingebundenen Geräte um kompatible Windows-Geräte handelt, gehen Sie wie folgt vor:

- Konfigurieren der lokalen Intraneteinstellungen für die Geräteregistrierung
- Installieren von Microsoft Workplace Join für kompatible Windows-Computer

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurieren der lokalen Intraneteinstellungen für die Geräteregistrierung

Um die Einbindung in Hybrid-Azure AD für Ihre kompatiblen Windows-Geräte erfolgreich abzuschließen und Zertifikataufforderungen bei der Authentifizierung von Geräten gegenüber Azure AD zu vermeiden, können Sie eine Richtlinie auf Ihre in die Domäne eingebundenen Geräte übertragen, mit der die folgende URL in Internet Explorer der Zone „Lokales Intranet“ hinzugefügt wird:

- `https://device.login.microsoftonline.com`
- Der Sicherheitstokendienst Ihrer Organisation (STS – Verbunddomänen)
- `https://autologon.microsoftazuread-sso.com` (für nahtloses einmaliges Anmelden).

Außerdem müssen Sie in der lokalen Intranetzone des Benutzers die Option **Aktualisierungen der Statusleiste per Skript zulassen** aktivieren.

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

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
