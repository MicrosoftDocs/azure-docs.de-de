---
title: Konfigurieren der Azure Active Directory-Hybrideinbindung für verwaltete Domänen | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie die Azure Active Directory-Hybrideinbindung für verwaltete Domänen konfigurieren.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 01/26/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4fb45f222fcf5abd762d97d702925655db6cba60
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100365768"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Tutorial: Konfigurieren der Azure Active Directory-Hybrideinbindung für verwaltete Domänen

In diesem Tutorial erfahren Sie, wie die Azure Active Directory-Hybrideinbindung (Azure AD) für in die Active Directory-Domäne eingebundene Geräte konfiguriert wird. Diese Methode unterstützt eine verwaltete Umgebung, die sowohl Active Directory lokal als auch Azure AD umfasst.

Ähnlich wie ein Benutzer in Ihrer Organisation ist auch ein Gerät eine zentrale Identität, die Sie schützen möchten. Über die Identität eines Geräts können Sie Ihre Ressourcen jederzeit und von überall aus schützen. Sie erreichen dieses Ziel durch die Verwaltung von Geräteidentitäten in Azure AD. Verwenden Sie eine der folgenden Methoden:

- Azure AD-Einbindung
- Azure AD-Hybrideinbindung
- Azure AD-Registrierung

In diesem Artikel geht es um die Azure AD-Hybrideinbindung.

Durch das Bereitstellen Ihrer Geräte in Azure AD wird die Benutzerproduktivität über einmaliges Anmelden (SSO) für Ihre gesamten Cloud- und lokalen Ressourcen maximiert. Gleichzeitig können Sie den Zugriff auf Ihre Cloud- und lokalen Ressourcen durch den [bedingten Zugriff](../conditional-access/howto-conditional-access-policy-compliant-device.md) sichern.

Eine verwaltete Umgebung kann entweder durch [Kennworthashsynchronisierung](../hybrid/whatis-phs.md) (Password Hash Sync, PHS) oder [Pass-Through-Authentifizierung](../hybrid/how-to-connect-pta.md) (Pass Through Authentication, PTA) mit [nahtlosem einmaligem Anmelden](../hybrid/how-to-connect-sso.md) (Seamless Single Sign-On, Seamless SSO) bereitgestellt werden. In diesen Szenarien müssen Sie keinen Verbundserver für die Authentifizierung konfigurieren.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Konfigurieren der Hybrid-Azure AD-Einbindung
> * Aktivieren von kompatiblen Windows-Geräten
> * Überprüfen der eingebundenen Geräte
> * Problembehandlung

## <a name="prerequisites"></a>Voraussetzungen

- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 oder höher)
- Die Anmeldeinformationen eines globalen Administrators für Ihren Azure AD-Mandanten
- Die Anmeldeinformationen eines Unternehmensadministrators für jede Gesamtstruktur

Machen Sie sich mit diesen Artikeln vertraut:

- [Was ist eine Geräteidentität?](overview.md)
- [Vorgehensweise: Planen der Implementierung einer Azure Active Directory-Hybrideinbindung](hybrid-azuread-join-plan.md)
- [Kontrollierte Überprüfung der Azure AD-Hybrideinbindung](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD unterstützt keine Smartcards oder Zertifikate in verwalteten Domänen.

Vergewissern Sie sich, dass Azure AD Connect die Computerobjekte der Geräte für die Azure AD-Hybrideinbindung mit Azure AD synchronisiert. Wenn die Computerobjekte zu bestimmten Organisationseinheiten (OEs) gehören, konfigurieren Sie diese Organisationseinheiten ebenfalls so, dass sie in Azure AD Connect synchronisiert werden. Weitere Informationen zum Synchronisieren von Computerobjekten mit Azure AD Connect finden Sie unter [Filterung basierend auf Organisationseinheiten](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

Ab Version 1.1.819.0 enthält Azure AD Connect einen Assistenten für die Konfiguration der Azure AD-Hybrideinbindung. Mit dem Assistenten wird der Konfigurationsprozess erheblich vereinfacht. Der Assistent konfiguriert die Dienstverbindungspunkte (SCP) für die Geräteregistrierung.

Die Konfigurationsschritte in diesem Artikel basieren auf der Verwendung des Assistenten in Azure AD Connect.

Für die Azure AD-Hybrideinbindung müssen die Geräte innerhalb des Netzwerks Ihrer Organisation Zugriff auf die folgenden Microsoft-Ressourcen haben:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (Wenn Sie nahtloses einmaliges Anmelden verwenden oder verwenden möchten.)

> [!WARNING]
> Wenn Ihre Organisation Proxyserver verwendet, die SSL-Datenverkehr für Szenarien wie die Verhinderung von Datenverlust oder Azure AD-Mandanteneinschränkungen abfangen, stellen Sie sicher, dass der Datenverkehr zu „https://device.login.microsoftonline.com“ von TLSI (TLS break and inspect) ausgeschlossen ist. Wird „https://device.login.microsoftonline.com“ nicht ausgeschlossen, kann dies zu Beeinträchtigungen bei der Clientzertifikatauthentifizierung führen und Probleme bei der Geräteregistrierung und beim gerätebasierten bedingten Zugriff verursachen.

Wenn für Ihre Organisation Zugriff auf das Internet über einen ausgehenden Proxy erforderlich ist, können Sie die Vorgehensweise unter [Implementierung von Web Proxy Auto-Discovery (WPAD)](/previous-versions/tn-archive/cc995261(v=technet.10)) verwenden, damit Windows 10-Computer Geräte bei Azure AD registrieren können. Wenn bei der Konfiguration und Verwaltung von WPAD Probleme auftreten, finden Sie entsprechende Informationen unter [Problembehandlung bei der automatischen Erkennung](/previous-versions/tn-archive/cc302643(v=technet.10)). Auf Windows 10-Geräten vor dem Update 1709 ist „WPAD“ die einzige verfügbare Option zum Konfigurieren eines Proxys für Azure AD Hybrid Join. 

Wenn Sie WPAD nicht verwenden, können Sie ab Windows 10 1709 WinHTTP-Proxyeinstellungen auf Ihrem Computer konfigurieren. Weitere Informationen finden Sie unter [Vom GPO bereitgestellte WinHTTP-Proxy-Einstellungen](/archive/blogs/netgeeks/winhttp-proxy-settings-deployed-by-gpo).

> [!NOTE]
> Wenn Sie Proxyeinstellungen auf Ihrem Computer mithilfe von WinHTTP-Einstellungen konfigurieren, können alle Computer, die keine Verbindung mit dem konfigurierten Proxy herstellen können, auch keine Internetverbindung herstellen.

Wenn Ihre Organisation Internetzugriff über einen authentifizierten ausgehenden Proxy erfordert, stellen Sie sicher, dass Ihre Windows 10-Computer erfolgreich beim ausgehenden Proxy authentifiziert werden können. Da Windows 10-Computer die Geräteregistrierung mithilfe von Computerkontext ausführen, konfigurieren Sie die Authentifizierung bei ausgehenden Proxys mit dem Computerkontext. Erkundigen Sie sich beim Anbieter Ihres ausgehenden Proxys nach den Konfigurationsanforderungen.

Verwenden Sie das Skript zum [Testen der Geräteregistrierungskonnektivität](https://docs.microsoft.com/samples/azure-samples/testdeviceregconnectivity/testdeviceregconnectivity/), um zu überprüfen, ob das Gerät unter dem Systemkonto auf die oben genannten Microsoft-Ressourcen zugreifen kann.

## <a name="configure-hybrid-azure-ad-join"></a>Konfigurieren der Hybrid-Azure AD-Einbindung

So konfigurieren Sie eine Azure AD-Hybrideinbindung mithilfe von Azure AD Connect:

1. Starten Sie Azure AD Connect, und wählen Sie dann **Konfigurieren** aus.

1. Wählen Sie unter **Zusätzliche Aufgaben** die Option **Geräteoptionen konfigurieren** und dann **Weiter** aus.

   ![Zusätzliche Aufgaben](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-additional-tasks.png)

1. Wählen Sie unter **Übersicht** die Option **Weiter** aus.

1. Geben Sie unter **Mit Azure AD verbinden** die Anmeldeinformationen eines globalen Administrators für Ihren Azure AD-Mandanten ein.  

1. Wählen Sie unter **Geräteoptionen** die Option **Hybrid-Azure AD-Einbindung konfigurieren** und dann **Weiter** aus.

   ![Geräteoptionen](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-options.png)

1. Wählen Sie unter **Gerätebetriebssysteme** die Betriebssysteme der Geräte in Ihrer Active Directory-Umgebung und dann **Weiter** aus.

   ![Gerätebetriebssystem](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-operating-systems.png)

1. Führen Sie unter **SCP-Konfiguration** für jede Gesamtstruktur, in der Azure AD Connect das SCP konfigurieren soll, die folgenden Schritte aus, und wählen Sie dann **Weiter** aus.

   1. Wählen Sie die **Gesamtstruktur** aus.
   1. Wählen Sie einen **Authentifizierungsdienst** aus.
   1. Wählen Sie **Hinzufügen** aus, um die Anmeldeinformationen eines Unternehmensadministrators einzugeben.

   ![SCP](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-scp-configuration.png)

1. Wählen Sie unter **Bereit zur Konfiguration** die Option **Konfigurieren** aus.

1. Wählen Sie unter **Konfiguration abgeschlossen** die Option **Beenden** aus.

## <a name="enable-windows-down-level-devices"></a>Aktivieren von kompatiblen Windows-Geräten

Wenn es sich bei einigen Ihrer in die Domäne eingebundenen Geräte um kompatible Windows-Geräte handelt, gehen Sie wie folgt vor:

- Konfigurieren der lokalen Intraneteinstellungen für die Geräteregistrierung
- Konfigurieren des nahtlosen einmaligen Anmeldens
- Installieren von Microsoft Workplace Join für kompatible Windows-Computer

> [!NOTE]
> Die Unterstützung für Windows 7 wurde am 14. Januar 2020 eingestellt. Weitere Informationen finden Sie unter [Der Support für Windows 7 endet am 14. Januar 2020](https://support.microsoft.com/help/4057281/windows-7-support-ended-on-january-14-2020).

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurieren der lokalen Intraneteinstellungen für die Geräteregistrierung

Um die Einbindung in Hybrid-Azure AD für Ihre kompatiblen Windows-Geräte abzuschließen und Zertifikataufforderungen bei der Authentifizierung von Geräten gegenüber Azure AD zu vermeiden, können Sie eine Richtlinie auf Ihre in die Domäne eingebundenen Geräte übertragen, mit der die folgende URL in Internet Explorer der Zone „Lokales Intranet“ hinzugefügt wird:

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

Außerdem müssen Sie in der lokalen Intranetzone des Benutzers die Option **Aktualisierungen der Statusleiste per Skript zulassen** aktivieren.

### <a name="configure-seamless-sso"></a>Konfigurieren des nahtlosen einmaligen Anmeldens

Sie müssen außerdem [nahtloses SSO konfigurieren](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature), um die Azure AD-Hybrideinbindung Ihrer kompatiblen Windows-Geräte in einer verwalteten Domäne abzuschließen, die als Authentifizierungsmethode für die Azure AD-Cloud [Kennworthashsynchronisierung](../hybrid/whatis-phs.md) oder [Passthrough-Authentifizierung](../hybrid/how-to-connect-pta.md) verwendet.

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>Installieren von Microsoft Workplace Join für kompatible Windows-Computer

Zur Registrierung von kompatiblen Windows-Geräten müssen Organisationen [Microsoft Workplace Join für Computer installieren, auf denen nicht Windows 10 ausgeführt wird](https://www.microsoft.com/download/details.aspx?id=53554). Microsoft Workplace Join für Computer, auf denen nicht Windows 10 ausgeführt wird, steht im Microsoft Download Center zur Verfügung.

Sie können das Paket mithilfe eines Softwareverteilungssystems wie  [Microsoft Endpoint Configuration Manager](/configmgr/) bereitstellen. Das Paket unterstützt die Standardoptionen für die Installation im Hintergrund unter Verwendung des `quiet`-Parameters. Die aktuelle Version von Configuration Manager bietet zusätzliche Vorteile gegenüber früheren Versionen, z. B. die Möglichkeit zur Nachverfolgung abgeschlossener Registrierungen.

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
3. Wenn in der Spalte **Registriert** der Wert **Ausstehend** angezeigt wird, wurde Azure AD Hybrid Join nicht abgeschlossen.
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
- [Beheben von Problemen mit Geräten mit Hybrid-Azure Active Directory-Einbindung](troubleshoot-hybrid-join-windows-current.md)
- [Beheben von Problemen mit Geräten mit Hybrideinbindung in Azure Active Directory](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie im nächsten Artikel, wie Sie Geräteidentitäten im Azure-Portal verwalten.
> [!div class="nextstepaction"]
> [Verwalten von Geräteidentitäten](device-management-azure-portal.md)
