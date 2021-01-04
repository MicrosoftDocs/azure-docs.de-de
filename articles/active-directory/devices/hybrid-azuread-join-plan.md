---
title: Planen einer Azure Active Directory-Hybrideinbindung – Azure Active Directory
description: Erfahren Sie, wie Sie in Azure Active Directory eingebundene Hybridgeräte konfigurieren.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: f13dfa4221f8f09c24cce3a451f3180d15ee3b99
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435756"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Anleitung: Planen der Implementierung einer Azure Active Directory-Hybrideinbindung

Ähnlich wie ein Benutzer ist ein Gerät eine weitere zentrale Identität, die Sie schützen sowie jederzeit und überall zum Schutz Ihrer Ressourcen verwenden können. Dafür können Sie die Geräteidentitäten mit einer der folgenden Methoden in Azure AD bereitstellen und verwalten:

- Azure AD-Einbindung
- Azure AD-Hybrideinbindung
- Azure AD-Registrierung

Durch das Bereitstellen Ihrer Geräte in Azure AD maximieren Sie die Produktivität Ihrer Benutzer durch einmaliges Anmelden (SSO) für Ihre gesamten Cloud- und lokalen Ressourcen. Gleichzeitig können Sie den Zugriff auf Ihre Cloud- und lokalen Ressourcen durch [bedingten Zugriff](../conditional-access/overview.md) schützen.

Wenn Sie in einer lokalen Active Directory (AD)-Umgebung Ihre in die AD-Domäne eingebundenen Compute in Azure AD einbinden möchten, kann dies durch Vornehmen einer Einbindung in Azure AD Hybrid erfolgen. Dieser Artikel enthält eine Anleitung zum Implementieren einer Azure AD-Hybrideinbindung in Ihre Umgebung. 

## <a name="prerequisites"></a>Voraussetzungen

Dieser Artikel setzt voraus, dass Sie die [Einführung in die Geräteidentitätsverwaltung in Azure Active Directory](./overview.md) gelesen haben.

> [!NOTE]
> Die mindestens erforderliche Version des Domänencontrollers für die Azure AD-Hybrideinbindung unter Windows 10 ist Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>Planen Ihrer Implementierung

Um Ihre Azure AD-Hybridimplementierung zu planen, sollten Sie sich mit folgenden Themen vertraut machen:

> [!div class="checklist"]
> - Überprüfung unterstützter Geräte
> - Überprüfung wichtiger Informationen
> - Überprüfung der kontrollierten Überprüfung der Azure AD-Hybrideinbindung
> - Auswählen Ihres Szenarios, basierend auf Ihrer Identitätsinfrastruktur
> - Überprüfung der lokalen AD UPN-Unterstützung (Benutzerprinzipalname) für Azure AD-Hybrideinbindung

## <a name="review-supported-devices"></a>Überprüfung unterstützter Geräte

Azure AD Hybrid Join unterstützt zahlreiche Windows-Geräte. Da die Konfiguration für Geräte mit älteren Versionen von Windows zusätzliche oder unterschiedliche Schritte erfordert, werden die unterstützten Geräte in zwei Kategorien eingeteilt:

### <a name="windows-current-devices"></a>Aktuelle Windows-Geräte

- Windows 10
- Windows Server 2016
  - **Hinweis**: Kunden der nationalen Azure-Cloud benötigen Version 1803.
- Windows Server 2019

Für Geräte, auf denen das Windows-Desktopbetriebssystem ausgeführt wird, sind die unterstützten Versionen in diesem Artikel aufgeführt: [Windows 10 Releaseinformationen](/windows/release-information/). Als bewährte Methode empfiehlt Microsoft, ein Upgrade auf die aktuelle Version von Windows 10 durchzuführen.

### <a name="windows-down-level-devices"></a>Kompatible Windows-Geräte

- Windows 8.1
- Die Windows 7-Unterstützung wurde am 14. Januar 2020 beendet. Weitere Informationen finden Sie unter [Der Support für Windows 7 endet am 14. Januar 2020](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020).
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2. Supportinformationen zu Windows Server 2008 und Windows Server 2008 R2 finden Sie unter [Der Support für Windows Server 2008 wird eingestellt](https://www.microsoft.com/cloud-platform/windows-server-2008).

Als ersten Planungsschritt sollten Sie Ihre Umgebung überprüfen und ermitteln, ob Sie Unterstützung für kompatible Windows-Geräte benötigen.

## <a name="review-things-you-should-know"></a>Überprüfung wichtiger Informationen

### <a name="unsupported-scenarios"></a>Nicht unterstützte Szenarien
- Azure AD-Hybrideinbindung wird zurzeit nicht unterstützt, wenn Ihre Umgebung aus einer einzelnen AD-Gesamtstruktur besteht, die Identitätsdaten mit mehr als einem Azure AD-Mandanten synchronisiert.

- Azure AD Hybrideinbindung wird für Windows Server, die die Domänencontrollerrolle ausführen, nicht unterstützt.

- Azure AD Hybrideinbindung wird für kompatible Windows-Geräte nicht unterstützt, wenn sie das Roaming von Anmeldeinformationen oder Benutzerprofilen bzw. obligatorischen Profilen verwenden.

- Das Server Core-Betriebssystem unterstützt jeden Geräteregistrierungstyp.

### <a name="os-imaging-considerations"></a>Überlegungen zur Erstellung von Betriebssystemimages
- Wenn Sie das Systemvorbereitungstool (Sysprep) verwenden und ein Image einer niedrigeren Version als **Windows 10 1809** für die Installation verwenden, stellen Sie sicher, dass dieses Image nicht von einem Gerät stammt, das bereits als Azure AD-Hybrideinbindung bei Azure AD registriert ist.

- Wenn Sie zusätzliche VMs mit einer Momentaufnahme des virtuellen Computers erstellen, stellen Sie sicher, dass diese Momentaufnahme nicht von einem Computer stammt, der bereits als Azure AD-Hybrideinbindung bei Azure AD registriert ist.

- Wenn Sie den [vereinheitlichten Schreibfilter](/windows-hardware/customize/enterprise/unified-write-filter) und ähnliche Technologien verwenden, die Änderungen am Datenträger beim Neustart löschen, müssen diese angewandt werden, nachdem das Gerät in Azure AD Hybrid eingebunden wurde. Das Aktivieren solcher Technologien vor dem Abschluss der Einbindung in Azure AD Hybrid führt dazu, dass das Gerät bei jedem Neustart getrennt wird.

### <a name="handling-devices-with-azure-ad-registered-state"></a>Behandeln von Geräten mit registriertem Azure AD-Status
Wenn Ihre in die Windows 10-Domäne eingebundenen Geräte für Ihren Mandanten [bei Azure AD registriert](overview.md#getting-devices-in-azure-ad) sind, kann dies zu einem Doppelstatus der Azure AD-Hybrideinbindung und der Registrierung bei Azure AD des Geräts führen. Es wird empfohlen, ein Upgrade auf Windows 10 1803 (mit angewandtem KB4489894) oder höher durchzuführen, um dieses Szenario automatisch zu beheben. In Releases vor 1803 müssen Sie die Registrierung bei Azure AD manuell entfernen, bevor Sie die Azure AD-Hybrideinbindung aktivieren. In Releases ab 1803 wurden die folgenden Änderungen vorgenommen, um diesen Doppelstatus zu vermeiden:

- Jeder vorhandene Azure AD-Registrierungsstatus für einen Benutzer wird nach der <i>Azure AD-Hybrideinbindung des Geräts und der Anmeldung desselben Benutzers</i> automatisch entfernt. Wenn Benutzer A beispielsweise einen Azure AD-Registrierungsstatus auf dem Gerät hat, wird der Doppelstatus für Benutzer A nur dann bereinigt, wenn sich Benutzer A beim Gerät anmeldet. Bei mehreren Benutzern auf demselben Gerät wird der Doppelstatus individuell bei der Anmeldung der jeweiligen Benutzer bereinigt. Windows 10 entfernt die Registrierung bei Azure AD und hebt darüber hinaus auch die Registrierung des Geräts bei Intune oder einer anderen mobilen Geräteverwaltung auf, wenn die Registrierung im Rahmen der Azure AD-Registrierung über die automatische Registrierung erfolgt ist.
- Sie können verhindern, dass Ihr in die Domäne eingebundenes Gerät bei Azure AD registriert wird, indem Sie den folgenden Registrierungsschlüssel in „HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin“ hinzufügen: "BlockAADWorkplaceJoin"=dword:00000001.
- Wenn Sie Windows Hello for Business unter Windows 10 1803 konfiguriert haben, muss der Benutzer Windows Hello for Business nach der Bereinigung des Doppelstatus erneut einrichten. Dieses Problem wird mit KB4512509 behoben.

> [!NOTE]
> Obwohl Windows 10 die Registrierung bei Azure AD lokal automatisch entfernt, wird das Geräteobjekt in Azure AD nicht sofort gelöscht, wenn es von Intune verwaltet wird. Sie können die Entfernung der Registrierung bei Azure AD überprüfen, indem Sie „dsregcmd /status“ ausführen und das Gerät auf dieser Grundlage als nicht bei Azure AD registriert betrachten.

### <a name="additional-considerations"></a>Weitere Überlegungen
- Wenn in Ihrer Umgebung Virtual Desktop Infrastructure (VDI) verwendet wird, finden Sie unter [Geräteidentität und Desktopvirtualisierung](./howto-device-identity-virtual-desktop-infrastructure.md) weitere Informationen.

- Azure AD Hybrid Join wird für FIPS-konformes TPM 2.0 und nicht für TPM 1.2 unterstützt. Wenn Ihre Geräte über FIPS-konformes TPM 1.2 verfügen, müssen Sie sie deaktivieren, bevor Sie mit Azure AD Hybrid Join fortfahren. Microsoft stellt keine Tools zum Deaktivieren des FIPS-Modus für TPMs bereit, da dieser vom TPM-Hersteller abhängig ist. Wenden Sie sich an Ihren Hardware-OEM, um Unterstützung zu erhalten. 

- Ab Windows 10 Release 1903 werden TPMs 1.2 nicht mehr für die Azure AD-Hybrideinbindung verwendet, und Geräte mit diesen TPMs werden so betrachtet, als würden sie nicht über ein TPM verfügen.

- UPN-Änderungen werden erst ab dem Windows 10-Update 2004 unterstützt. Auf Geräten vor dem Windows 10-Update 2004 haben Benutzer Probleme mit SSO und dem bedingten Zugriff. Zum Beheben des Problems müssen Sie das Gerät von Azure AD trennen (indem Sie „dsregcmd /leave“ mit erhöhten Rechten ausführen) und sich wieder anmelden (was automatisch geschieht). Allerdings tritt das Problem bei Benutzern, die sich mit Windows Hello for Business anmelden, nicht auf.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Überprüfung der kontrollierten Überprüfung der Azure AD-Hybrideinbindung

Wenn alle Voraussetzungen erfüllt sind, werden Windows-Geräte automatisch als Geräte bei Ihrem Azure AD-Mandanten registriert. Der Zustand dieser Geräteidentitäten in Azure AD wird als Azure AD Hybrid Join bezeichnet. Weitere Informationen zu den in diesem Artikel beschriebenen Konzepten finden Sie im Artikel [Einführung in die Geräteidentitätsverwaltung in Azure Active Directory](overview.md).

Organisationen sollten eine kontrollierte Überprüfung von Azure AD Hybrid Join durchführen, bevor sie den Dienst in der gesamten Organisation aktivieren. Das Verständnis, wie Sie dies erreichen können, erhalten Sie in dem Artikel [Kontrollierte Überprüfung der Azure AD-Hybrideinbindung](hybrid-azuread-join-control.md).

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Auswählen Ihres Szenarios, basierend auf Ihrer Identitätsinfrastruktur

Azure AD Hybrid-Join funktioniert sowohl in verwalteten als auch in Verbundumgebungen, je nachdem, ob der UPN routingfähig ist. Eine Tabelle mit unterstützten Szenarien finden Sie unten auf der Seite.  

### <a name="managed-environment"></a>Verwaltete Umgebung

Eine verwaltete Umgebung kann entweder durch [Kennworthashsynchronisierung](../hybrid/whatis-phs.md) (Password Hash Sync, PHS) oder [Passthrough-Authentifizierung](../hybrid/how-to-connect-pta.md) (Pass Through Authentication, PTA) mit [nahtlosem einmaligem Anmelden](../hybrid/how-to-connect-sso.md) (Seamless Single Sign-On, Seamless SSO) bereitgestellt werden.

In diesen Szenarien müssen Sie keinen Verbundserver für die Authentifizierung konfigurieren.

> [!NOTE]
> Die [Cloudauthentifizierung mit gestaffeltem Rollout](../hybrid/how-to-connect-staged-rollout.md) wird nur ab Windows 10, Update 1903, unterstützt

### <a name="federated-environment"></a>Verbundumgebung

Bei Verbundumgebungen sollte ein Identitätsanbieter verwendet werden, der die folgenden Anforderungen erfüllt. Wenn Sie eine Verbundumgebung besitzen, die Active Directory-Verbunddienste (AD FS) verwendet, werden die nachfolgend genannten Anforderungen bereits unterstützt.

- **WIAORMULTIAUTHN-Anspruch:** Dieser Anspruch ist erforderlich, um eine Azure AD-Hybrideinbindung für kompatible Windows-Geräte durchzuführen.
- **WS-Trust-Protokoll:** Dieses Protokoll ist erforderlich, um aktuelle Azure AD-hybrideingebundene Windows-Geräte mit Azure AD zu authentifizieren. Bei Verwendung von AD FS müssen Sie die folgenden WS-Trust-Endpunkte aktivieren: `/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> Die Endpunkte **adfs/services/trust/2005/windowstransport** und **adfs/services/trust/13/windowstransport** sollten nur als Endpunkte mit Intranetzugriff aktiviert werden und dürfen NICHT als Endpunkte mit Extranetzugriff über den Webanwendungsproxy verfügbar gemacht werden. Weitere Informationen zum Deaktivieren von WS-Trust-Windows-Endpunkten finden Sie unter [Deaktivieren von WS-Trust-Windows-Endpunkten auf dem Proxy](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Welche Endpunkte aktiviert sind, sehen Sie in der AD FS-Verwaltungskonsole unter **Dienst** > **Endpunkte**.

> [!NOTE]
> Azure AD unterstützt keine Smartcards oder Zertifikate in verwalteten Domänen.

Ab Version 1.1.819.0 bietet Azure AD Connect einen Assistenten für die Konfiguration der Azure AD-Hybrideinbindung. Mit dem Assistenten können Sie den Konfigurationsprozess erheblich vereinfachen. Wenn das Installieren der erforderlichen Version von Azure AD Connect keine Option für Sie ist, informieren Sie sich über die [manuelle Konfiguration der Geräteregistrierung](hybrid-azuread-join-manual.md). 

Lesen Sie auf Grundlage des Szenarios, das Ihrer Identitätsinfrastruktur entspricht:

- [Konfigurieren der Azure Active Directory-Hybrideinbindung für eine Verbundumgebung](hybrid-azuread-join-federated-domains.md)
- [Konfigurieren der Azure Active Directory-Hybrideinbindung für eine verwaltete Umgebung](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-users-upn-support-for-hybrid-azure-ad-join"></a>Überprüfen der Unterstützung lokaler AD-Benutzerprinzipalnamen (UPNs) in Azure AD Hybrid Join

In einigen Fällen können Ihre lokalen AD-Benutzerprinzipalnamen von den Azure AD-Benutzerprinzipalnamen abweichen. In diesen Fällen bietet Azure AD Hybrid Join unter Windows 10 auf Grundlage der [Authentifizierungsmethode](../hybrid/choose-ad-authn.md), dem Domänentyp und der Windows 10-Version eingeschränkte Unterstützung für lokale AD-UPNs. Es gibt zwei Arten lokaler AD-UPNs, die in Ihrer Umgebung vorhanden sein können:

- Routingfähige UPNs: Ein routingfähiger UPN verfügt über eine gültige überprüfte Domäne, die bei einer Domänenregistrierungsstelle registriert ist. Ist beispielsweise „contoso.com“ die primäre Domäne in Azure AD, ist „contoso.org“ die primäre Domäne im lokalen Active Directory, die Contoso gehört und [in Azure AD überprüft wird](../fundamentals/add-custom-domain.md).
- Nicht routingfähige UPNs: Ein nicht routingfähiger UPN verfügt nicht über eine überprüfte Domäne. Sie ist nur in einem privaten Netzwerk Ihrer Organisation gültig. Ist beispielsweise „contoso.com“ die primäre Domäne in Azure AD, ist „contoso.local“ die primäre Domäne im lokalen Active Directory, aber keine überprüfbare Domäne im Internet und wird nur innerhalb des Contoso-Netzwerks verwendet.

> [!NOTE]
> Die Informationen in diesem Abschnitt gelten nur für einen lokalen Benutzerprinzipalnamen. Sie gelten nicht für ein lokales Computerdomänensuffix (Beispiel: computer1.contoso.local).

Die folgende Tabelle enthält Details zur Unterstützung dieser lokalen AD UPNs in Azure AD Hybrid Join unter Windows 10

| Art der lokalen AD UPNs | Domänentyp | Windows 10-Version | BESCHREIBUNG |
| ----- | ----- | ----- | ----- |
| Routingfähig | Im Verbund | Version 1703 | Allgemein verfügbar |
| Nicht routingfähig | Im Verbund | Version 1803 | Allgemein verfügbar |
| Routingfähig | Verwaltet | Version 1803 | Allgemein verfügbar, Azure AD SSPR auf Windows-Sperrbildschirm wird nicht unterstützt |
| Nicht routingfähig | Verwaltet | Nicht unterstützt | |

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Konfigurieren der Azure Active Directory-Hybrideinbindung für eine Verbundumgebung](hybrid-azuread-join-federated-domains.md)
> [Konfigurieren der Azure Active Directory-Hybrideinbindung für eine verwaltete Umgebung](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
