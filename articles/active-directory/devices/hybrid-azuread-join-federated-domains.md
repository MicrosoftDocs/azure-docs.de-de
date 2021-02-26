---
title: Konfigurieren der Azure Active Directory-Hybrideinbindung für Verbunddomänen | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie die Azure Active Directory-Hybrideinbindung für Verbunddomänen konfigurieren.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 636f209eeb74ada1b4c7c4232b6aba8738eee10a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100365853"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Tutorial: Konfigurieren der Azure Active Directory-Hybrideinbindung für Verbunddomänen

Ähnlich wie ein Benutzer in Ihrer Organisation ist auch ein Gerät eine zentrale Identität, die Sie schützen möchten. Über die Identität eines Geräts können Sie Ihre Ressourcen jederzeit und von überall aus schützen. Dafür können Sie Geräteidentitäten mit einer der folgenden Methoden in Azure Active Directory (Azure AD) bereitstellen und verwalten:

- Azure AD-Einbindung
- Azure AD-Hybrideinbindung
- Azure AD-Registrierung

Durch das Bereitstellen Ihrer Geräte in Azure AD wird die Benutzerproduktivität über einmaliges Anmelden (SSO) für Ihre gesamten Cloud- und lokalen Ressourcen maximiert. Gleichzeitig können Sie den Zugriff auf Ihre Cloud- und lokalen Ressourcen durch den [bedingten Zugriff](../conditional-access/howto-conditional-access-policy-compliant-device.md) sichern.

Bei Verbundumgebungen sollte ein Identitätsanbieter verwendet werden, der die folgenden Anforderungen erfüllt. Wenn Sie eine Verbundumgebung besitzen, die Active Directory-Verbunddienste (AD FS) verwendet, werden die nachfolgend genannten Anforderungen bereits unterstützt.

- **WIAORMULTIAUTHN-Anspruch:** Dieser Anspruch ist erforderlich, um eine Azure AD-Hybrideinbindung für kompatible Windows-Geräte durchzuführen.
- **WS-Trust-Protokoll:** Dieses Protokoll ist erforderlich, um aktuelle Azure AD-hybrideingebundene Windows-Geräte mit Azure AD zu authentifizieren.
  Bei Verwendung von AD FS müssen Sie die folgenden WS-Trust-Endpunkte aktivieren: `/adfs/services/trust/2005/windowstransport`
   `/adfs/services/trust/13/windowstransport`
   `/adfs/services/trust/2005/usernamemixed`
   `/adfs/services/trust/13/usernamemixed`
   `/adfs/services/trust/2005/certificatemixed`
   `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> Die Endpunkte **adfs/services/trust/2005/windowstransport** und **adfs/services/trust/13/windowstransport** dürfen nur als Endpunkte mit Intranetzugriff aktiviert und NICHT als Endpunkte mit Extranetzugriff über den Webanwendungsproxy verfügbar gemacht werden. Weitere Informationen zum Deaktivieren von WS-Trust-Windows-Endpunkten finden Sie unter [Deaktivieren von WS-Trust-Windows-Endpunkten auf dem Proxy](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Welche Endpunkte aktiviert sind, sehen Sie in der AD FS-Verwaltungskonsole unter **Dienst** > **Endpunkte**.

In diesem Tutorial erfahren Sie, wie die Azure AD-Hybrideinbindung für in die Active Directory-Domäne eingebundene Computer in einer Verbundumgebung mit AD FS konfiguriert wird.

Folgendes wird vermittelt:

> [!div class="checklist"]
> * Konfigurieren der Hybrid-Azure AD-Einbindung
> * Aktivieren von kompatiblen Windows-Geräten
> * Überprüfen der Registrierung
> * Problembehandlung

## <a name="prerequisites"></a>Voraussetzungen

In diesem Tutorial wird vorausgesetzt, dass Sie mit folgenden Artikeln vertraut sind:

- [Was ist eine Geräteidentität?](overview.md)
- [Planen der Implementierung einer Azure AD-Hybrideinbindung](hybrid-azuread-join-plan.md)
- [Kontrollierte Überprüfung der Azure AD-Hybrideinbindung](hybrid-azuread-join-control.md)

Für die Konfiguration des Szenarios in diesem Tutorials benötigen Sie Folgendes:

- Windows Server 2012 R2 mit AD FS
- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) Version 1.1.819.0 oder höher

Ab Version 1.1.819.0 enthält Azure AD Connect einen Assistenten, den Sie für die Konfiguration der Azure AD-Hybrideinbindung verwenden können. Mit dem Assistenten wird der Konfigurationsprozess erheblich vereinfacht. Der entsprechende Assistent:

- Konfiguriert die Dienstverbindungspunkte (SCPs) für die Geräteregistrierung
- Sichert Ihre vorhandene Azure AD-Vertrauensstellung der vertrauenden Seite
- Aktualisiert die Anspruchsregeln in Ihrer Azure AD-Vertrauensstellung

Die Konfigurationsschritte in diesem Artikel basieren auf der Verwendung des Azure AD Connect-Assistenten. Wenn Sie eine frühere Version von Azure AD Connect installiert haben, müssen Sie sie mindestens auf Version 1.1.819 aktualisieren, um den Assistenten verwenden zu können. Wenn die Installation der aktuellen Version von Azure AD Connect keine Option für Sie ist, informieren Sie sich über die [manuelle Konfiguration der Azure AD-Hybrideinbindung](hybrid-azuread-join-manual.md).

Für die Azure AD-Hybrideinbindung müssen die Geräte innerhalb des Netzwerks Ihrer Organisation Zugriff auf die folgenden Microsoft-Ressourcen haben:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- Sicherheitstokendienst (STS) Ihrer Organisation (für Verbunddomänen)
- `https://autologon.microsoftazuread-sso.com` (Wenn Sie nahtloses einmaliges Anmelden verwenden oder verwenden möchten.)

> [!WARNING]
> Wenn Ihre Organisation Proxyserver verwendet, die SSL-Datenverkehr für Szenarien wie die Verhinderung von Datenverlust oder Azure AD-Mandanteneinschränkungen abfangen, stellen Sie sicher, dass der Datenverkehr zu „https://device.login.microsoftonline.com“ von TLSI (TLS break and inspect) ausgeschlossen ist. Wird „https://device.login.microsoftonline.com“ nicht ausgeschlossen, kann dies zu Beeinträchtigungen bei der Clientzertifikatauthentifizierung führen und Probleme bei der Geräteregistrierung und beim gerätebasierten bedingten Zugriff verursachen.

Ab Windows 10 1803 gilt Folgendes: Wenn bei der sofortigen Azure AD-Hybrideinbindung für Verbundumgebungen unter Verwendung von AD FS ein Fehler auftritt, nutzen wir Azure AD Connect, um das Computerobjekt in Azure AD zu synchronisieren. Dieses Objekt wird anschließend verwendet, um die Geräteregistrierung für die Azure AD-Hybrideinbindung durchzuführen. Vergewissern Sie sich, dass Azure AD Connect die Computerobjekte der Geräte für die Azure AD-Hybrideinbindung mit Azure AD synchronisiert. Wenn die Computerobjekte zu bestimmten Organisationseinheiten (OEs) gehören, müssen Sie diese Organisationseinheiten ebenfalls so konfigurieren, dass sie in Azure AD Connect synchronisiert werden. Weitere Informationen zum Synchronisieren von Computerobjekten mit Azure AD Connect finden Sie unter [Azure AD Connect-Synchronisierung: Konfigurieren der Filterung](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

Wenn für Ihre Organisation Zugriff auf das Internet über einen ausgehenden Proxy erforderlich ist, empfiehlt Microsoft die [Implementierung von Web Proxy Auto-Discovery (WPAD)](/previous-versions/tn-archive/cc995261(v%3dtechnet.10)), damit Windows 10-Computer Geräte bei Azure AD registrieren können. Wenn bei der Konfiguration und Verwaltung von WPAD Probleme auftreten, finden Sie entsprechende Informationen unter [Problembehandlung bei der automatischen Erkennung](/previous-versions/tn-archive/cc302643(v=technet.10)). 

Wenn Sie WPAD nicht verwenden und Proxyeinstellungen auf Ihrem Computer konfigurieren möchten, ist dies ab Windows 10 1709 möglich. Weitere Informationen finden Sie unter [Configure WinHTTP settings by using a group policy object (GPO)](/archive/blogs/netgeeks/winhttp-proxy-settings-deployed-by-gpo) (Konfigurieren von WinHTTP-Einstellungen über ein Gruppenrichtlinienobjekt (GPO)).

> [!NOTE]
> Wenn Sie Proxyeinstellungen auf Ihrem Computer mithilfe von WinHTTP-Einstellungen konfigurieren, können alle Computer, die keine Verbindung mit dem konfigurierten Proxy herstellen können, auch keine Internetverbindung herstellen.

Wenn Ihre Organisation Internetzugriff über einen authentifizierten ausgehenden Proxy erfordert, müssen Sie sicherstellen, dass Ihre Windows 10-Computer erfolgreich beim ausgehenden Proxy authentifiziert werden können. Da Windows 10-Computer die Geräteregistrierung mithilfe von Computerkontext ausführen, müssen Sie die Authentifizierung bei ausgehenden Proxys mit dem Computerkontext konfigurieren. Erkundigen Sie sich beim Anbieter Ihres ausgehenden Proxys nach den Konfigurationsanforderungen.

Sie können das Skript zum [Testen der Geräteregistrierungskonnektivität](https://docs.microsoft.com/samples/azure-samples/testdeviceregconnectivity/testdeviceregconnectivity/) verwenden, um zu überprüfen, ob das Gerät unter dem Systemkonto auf die oben genannten Microsoft-Ressourcen zugreifen kann.

## <a name="configure-hybrid-azure-ad-join"></a>Konfigurieren der Hybrid-Azure AD-Einbindung

Zum Konfigurieren einer Azure AD-Hybrideinbindung mithilfe von Azure AD Connect benötigen Sie Folgendes:

- Die Anmeldeinformationen eines globalen Administrators für Ihren Azure AD-Mandanten  
- Die Anmeldeinformationen eines Unternehmensadministrators für jede Gesamtstruktur
- Die Anmeldeinformationen Ihres AD FS-Administrators

**So konfigurieren Sie eine Azure AD-Hybrideinbindung mithilfe von Azure AD Connect**

1. Starten Sie Azure AD Connect, und wählen Sie dann **Konfigurieren** aus.

   ![Willkommen](./media/hybrid-azuread-join-federated-domains/11.png)

1. Wählen Sie auf der Seite **Zusätzliche Aufgaben** die Option **Geräteoptionen konfigurieren** und dann **Weiter** aus.

   ![Zusätzliche Aufgaben](./media/hybrid-azuread-join-federated-domains/12.png)

1. Wählen Sie auf der Seite **Übersicht** die Option **Weiter** aus.

   ![Übersicht](./media/hybrid-azuread-join-federated-domains/13.png)

1. Geben Sie auf der Seite **Mit Azure AD verbinden** die Anmeldeinformationen eines globalen Administrators für Ihren Azure AD-Mandanten ein, und wählen Sie **Weiter** aus.

   ![Stellen Sie eine Verbindung mit Azure AD her.](./media/hybrid-azuread-join-federated-domains/14.png)

1. Wählen Sie auf der Seite **Geräteoptionen** die Option **Hybrid-Azure AD-Einbindung konfigurieren** und dann **Weiter** aus.

   ![Geräteoptionen](./media/hybrid-azuread-join-federated-domains/15.png)

1. Führen Sie auf der Seite **SCP** die folgenden Schritte aus, und wählen Sie dann **Weiter** aus:

   ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

   1. Wählen Sie die Gesamtstruktur aus.
   1. Wählen Sie den Authentifizierungsdienst aus. Sie müssen **AD FS-Server** auswählen – es sei denn, Ihre Organisation verfügt ausschließlich über Windows 10-Clients und Sie haben die Computer-/Gerätesynchronisierung konfiguriert, oder Ihre Organisation verwendet nahtlose einmaliges Anmelden.
   1. Wählen Sie **Hinzufügen** aus, um die Anmeldeinformationen eines Unternehmensadministrators einzugeben.

1. Wählen Sie auf der Seite **Gerätebetriebssysteme** die Betriebssysteme der Geräte in Ihrer Active Directory-Umgebung und dann **Weiter** aus.

   ![Gerätebetriebssystem](./media/hybrid-azuread-join-federated-domains/17.png)

1. Geben Sie auf der Seite **Verbundkonfiguration** die Anmeldeinformationen Ihres AD FS-Administrators ein, und wählen Sie dann **Weiter** aus.

   ![Verbundkonfiguration](./media/hybrid-azuread-join-federated-domains/18.png)

1. Wählen Sie auf der Seite **Bereit zur Konfiguration** die Option **Konfigurieren** aus.

   ![Bereit zur Konfiguration](./media/hybrid-azuread-join-federated-domains/19.png)

1. Wählen Sie auf der Seite **Konfiguration abgeschlossen** die Option **Beenden** aus.

   ![Konfiguration abgeschlossen](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Aktivieren von kompatiblen Windows-Geräten

Wenn es sich bei einigen Ihrer in die Domäne eingebundenen Geräte um kompatible Windows-Geräte handelt, gehen Sie wie folgt vor:

- Konfigurieren der lokalen Intraneteinstellungen für die Geräteregistrierung
- Installieren von Microsoft Workplace Join für kompatible Windows-Computer

> [!NOTE]
> Die Unterstützung für Windows 7 wurde am 14. Januar 2020 eingestellt. Weitere Informationen finden Sie unter [Der Support für Windows 7 endet am 14. Januar 2020](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020).

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurieren der lokalen Intraneteinstellungen für die Geräteregistrierung

Um die Einbindung in Hybrid-Azure AD für Ihre kompatiblen Windows-Geräte erfolgreich abzuschließen und Zertifikataufforderungen bei der Authentifizierung von Geräten bei Azure AD zu vermeiden, können Sie eine Richtlinie auf Ihre in die Domäne eingebundenen Geräte übertragen, mit der die folgende URL in Internet Explorer der Zone „Lokales Intranet“ hinzugefügt wird:

- `https://device.login.microsoftonline.com`
- STS Ihrer Organisation (für Verbunddomänen)
- `https://autologon.microsoftazuread-sso.com` (für nahtloses einmaliges Anmelden)

Außerdem müssen Sie in der lokalen Intranetzone des Benutzers die Option **Aktualisierungen der Statusleiste per Skript zulassen** aktivieren.

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Installieren von Microsoft Workplace Join für kompatible Windows-Computer

Zur Registrierung von kompatiblen Windows-Geräten müssen Organisationen [Microsoft Workplace Join für Computer installieren, auf denen nicht Windows 10 ausgeführt wird](https://www.microsoft.com/download/details.aspx?id=53554). Microsoft Workplace Join für Computer, auf denen nicht Windows 10 ausgeführt wird, steht im Microsoft Download Center zur Verfügung.

Sie können das Paket mithilfe eines Softwareverteilungssystems wie  [Microsoft Endpoint Configuration Manager](/configmgr/) bereitstellen. Das Paket unterstützt die Standardoptionen für die Installation im Hintergrund unter Verwendung des `quiet`-Parameters. Configuration Manager Current Branch bietet zusätzliche Vorteile gegenüber früheren Versionen, z.B. die Möglichkeit zur Nachverfolgung abgeschlossener Registrierungen.

Das Installationsprogramm erstellt einen geplanten Task für das System, der im Kontext des Benutzers ausgeführt wird. Der Task wird ausgelöst, wenn sich der Benutzer bei Windows anmeldet. Nach der Authentifizierung durch Azure AD bindet der Task das Gerät unter Verwendung der Anmeldeinformationen des Benutzers im Hintergrund in Azure AD ein.

## <a name="verify-the-registration"></a>Überprüfen der Registrierung

Im Folgenden finden Sie drei Möglichkeiten, den Gerätezustand zu finden und zu überprüfen:

### <a name="locally-on-the-device"></a>Lokal auf dem Gerät

1. Öffnen Sie Windows PowerShell.
2. Geben Sie `dsregcmd /status` ein.
3. Überprüfen Sie, ob sowohl **AzureAdJoined-** als auch **DomainJoined** auf **YES** festgelegt sind.
4. Sie können die **DeviceId** verwenden und den Status des Diensts entweder ium Azure-Portal oder mithilfe der PowerShell vergleichen.

### <a name="using-the-azure-portal"></a>Verwenden des Azure-Portals

1. Wechseln Sie zur Geräteseite über einen [direkten Link](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices).
2. Informationen, wie Sie ein Gerät lokalisieren können, finden Sie unter [Verwalten von Geräteidentitäten mit dem Azure-Portal](./device-management-azure-portal.md).
3. Wenn in der Spalte **Registriert** der Wert **Ausstehend** angezeigt wird, wurde Azure AD Hybrid Join nicht abgeschlossen. In Verbundumgebungen kann es hierzu nur kommen, wenn die Registrierung fehlgeschlagen ist und AAD Connect für die Synchronisierung der Geräte konfiguriert ist.
4. Wenn die Spalte **Registriert** einen **Datum/Uhrzeit**-Wert enthält, wurde Azure AD Hybrid Join abgeschlossen.

### <a name="using-powershell"></a>PowerShell

Überprüfen Sie den Geräteregistrierungsstatus in Ihrem Azure-Mandanten mithilfe von **[Get-MsolDevice](/powershell/module/msonline/get-msoldevice)** . Dieses Cmdlet befindet sich im [Azure Active Directory PowerShell-Modul](/powershell/azure/active-directory/install-msonlinev1).

Bei Verwendung des Cmdlets **Get-MSolDevice** zur Überprüfung der Dienstdetails:

- Ein Objekt mit der **Geräte-ID**, die der ID auf dem Windows-Client entspricht, muss vorhanden sein.
- Der Wert für **DeviceTrustType** ist **Domänenbeitritt**. Diese Einstellung entspricht dem Status **Hybrid in Azure AD eingebunden** auf der Seite **Geräte** im Azure AD-Portal.
- Für Geräte, die für den bedingten Zugriff verwendet werden, hat **Aktiviert** den Wert **True** und **DeviceTrustLevel** den Wert **Verwaltet**.

1. Öffnen Sie Windows PowerShell als Administrator.
2. Geben Sie `Connect-MsolService` ein, um die Verbindung mit Ihrem Azure-Mandanten herzustellen.

#### <a name="count-all-hybrid-azure-ad-joined-devices-excluding-pending-state"></a>Zählen Sie alle in Azure AD Hybrid eingebundenen Geräte (ausgenommen der Zustand **Ausstehend**).

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="count-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Zählen Sie alle in Azure AD Hybrid eingebundenen Geräte mit dem Zustand **Ausstehend**.

```azurepowershell
(Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}).count
```

#### <a name="list-all-hybrid-azure-ad-joined-devices"></a>Listen Sie alle in Hybrid Azure AD eingebundenen Geräte auf.

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-all-hybrid-azure-ad-joined-devices-with-pending-state"></a>Listen Sie alle in Azure AD Hybrid eingebundenen Geräte mit dem Zustand **Ausstehend** auf.

```azurepowershell
Get-MsolDevice -All -IncludeSystemManagedDevices | where {($_.DeviceTrustType -eq 'Domain Joined') -and (-not([string]($_.AlternativeSecurityIds)).StartsWith("X509:"))}
```

#### <a name="list-details-of-a-single-device"></a>Details einen einzelnen Geräts auflisten:

1. Geben Sie `get-msoldevice -deviceId <deviceId>` ein (Dies ist die lokal auf dem Gerät abgerufene **DeviceId**).
2. Vergewissern Sie sich, dass **Aktiviert** auf **True** festgelegt ist.

## <a name="troubleshoot-your-implementation"></a>Problembehandlung bei der Implementierung

Sollten bei der Azure AD-Hybrideinbindung für in Domänen eingebundene Windows-Geräte Probleme auftreten, finden Sie weitere Informationen unter:

- [Problembehandlung von Geräten mit dem Befehl „dsregcmd“](./troubleshoot-device-dsregcmd.md)
- [Problembehandlung für in Azure AD eingebundene aktuelle Windows-Hybridgeräte](troubleshoot-hybrid-join-windows-current.md)
- [Problembehandlung für in Azure AD eingebundene kompatible Windows-Hybridgeräte](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [Geräteidentitäten im Azure-Portal verwalten](device-management-azure-portal.md).

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
