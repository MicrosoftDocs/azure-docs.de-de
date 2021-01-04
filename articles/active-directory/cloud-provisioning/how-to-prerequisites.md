---
title: Voraussetzungen für die Azure AD Connect-Cloudbereitstellung in Azure AD
description: In diesem Artikel werden die Voraussetzungen und Hardwareanforderungen beschrieben, die für die Cloudbereitstellung erforderlich sind.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8eb8de2424012d12f216f154eb077028a8f82d76
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173701"
---
# <a name="prerequisites-for-azure-ad-connect-cloud-provisioning"></a>Voraussetzungen für die Azure AD Connect-Cloudbereitstellung
Dieser Artikel enthält Anleitungen zur Auswahl und Verwendung der Azure Active Directory (Azure AD) Connect-Cloudbereitstellung als Identitätslösung.

## <a name="cloud-provisioning-agent-requirements"></a>Anforderungen des Agents für die Cloudbereitstellung
Für die Verwendung der Azure AD Connect-Cloudbereitstellung benötigen Sie Folgendes:

- Anmeldeinformationen eines Domänenadministrators oder Unternehmensadministrators zum Erstellen des gMSA (group Managed Service Account, gruppenverwaltetes Dienstkonto) für die Azure AD Connect-Cloudsynchronisierung zum Ausführen des-Agent-Diensts 
- Ein Hybrididentität-Administratorkonto für Ihren Azure AD-Mandanten, das kein Gastbenutzer ist.
- einen lokalen Server für den Bereitstellungs-Agent mit Windows 2012 R2 oder höher  Bei diesem Server sollte es sich um einen Server der Ebene 0 im [Active Directory-Verwaltungsebenenmodell](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) handeln.
- lokale Firewallkonfigurationen

## <a name="group-managed-service-accounts"></a>Gruppenverwaltete Dienstkonten
Ein gruppenverwaltetes Dienstkonto ist ein verwaltetes Domänenkonto, das eine automatische Kennwortverwaltung, eine vereinfachte Verwaltung von Dienstprinzipalnamen (Service Principal Name, SPN) und die Möglichkeit bietet, die Verwaltung an andere Administratoren zu delegieren, wobei diese Funktionalität auch auf mehrere Server erweitert wird.  Die Azure AD Connect-Cloudsynchronisierung unterstützt und verwendet ein gMSA zum Ausführen des Agents.  Sie werden während des Setups zur Eingabe administrativer Anmeldeinformationen aufgefordert, um das Konto zu erstellen.  Das Konto wird als (domain\provAgentgMSA$) angezeigt.  Weitere Informationen zu einem gruppenverwalteten Dienstkonto (Group Managed Service Account, GMSA) finden Sie unter [Gruppenverwaltete Dienstkonten](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview). 

### <a name="prerequisites-for-gmsa"></a>Voraussetzungen für das gMSA:
1.  Das Active Directory-Schema in der Gesamtstruktur der gMSA-Domäne muss auf Windows Server 2012 aktualisiert werden.
2.  [PowerShell-RSAT-Module](/windows-server/remote/remote-server-administration-tools) auf einem Domänencontroller
3.  Auf mindestens einem Domänencontroller in der Domäne muss Windows Server 2012 ausgeführt werden.
4.  Ein in eine Domäne eingebundener Server, auf dem der Agent installiert ist, muss Windows Server 2012 oder höher aufweisen.

Die Schritte zum Aktualisieren eines vorhandenen Agents für die Verwendung eines gMSA-Kontos finden Sie unter [Gruppenverwaltete Dienstkonten](how-to-install.md#group-managed-service-accounts).

### <a name="in-the-azure-active-directory-admin-center"></a>Im Azure Active Directory Admin Center

1. Erstellen Sie in Ihrem Azure AD-Mandanten ein auf die Cloud beschränktes Hybrididentität-Administratorkonto. Auf diese Weise können Sie die Konfiguration Ihres Mandanten verwalten, wenn Ihre lokalen Dienste ausfallen oder nicht verfügbar sind. Erfahren Sie, wie Sie ein [auf die Cloud beschränktes Hybrididentität-Administratorkonto hinzufügen](../fundamentals/add-users-azure-active-directory.md). Die Ausführung dieses Schritts ist äußerst wichtig, damit sichergestellt ist, dass Sie für Ihren Mandanten nicht gesperrt werden.
1. Fügen Sie Ihrem Azure AD-Mandanten mindestens einen [benutzerdefinierten Domänennamen](../fundamentals/add-custom-domain.md) hinzu. Ihre Benutzer können sich mit einem dieser Domänennamen anmelden.

### <a name="in-your-directory-in-active-directory"></a>In Ihrem Verzeichnis in Active Directory

Führen Sie das [IdFix-Tool](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) aus, um die Verzeichnisattribute für die Synchronisierung vorzubereiten.

### <a name="in-your-on-premises-environment"></a>In Ihrer lokalen Umgebung

1. Geben Sie einen in die Domäne eingebundenen Hostserver unter Windows Server 2012 R2 oder höher mit mindestens 4 GB RAM und .NET 4.7.1 + Runtime an.

1. Die PowerShell-Ausführungsrichtlinie auf dem lokalen Server muss auf „Nicht definiert“ oder „RemoteSigned“ festgelegt werden.

1. Wenn zwischen Ihren Servern und Azure AD eine Firewall eingerichtet wurde, konfigurieren Sie die folgenden Elemente:
   - Stellen Sie sicher, dass Agents über die folgenden Ports *ausgehende* Anforderungen an Azure AD senden können:

        | Portnummer | Wie diese verwendet wird |
        | --- | --- |
        | **80** | Herunterladen der Zertifikatsperrlisten (CRLs) bei der Überprüfung des TLS/SSL-Zertifikats.  |
        | **443** | Verarbeiten der gesamten ausgehenden Kommunikation mit dem Dienst |
        |**8082**|Erforderlich für die Installation und wenn Sie die HIS-Verwaltungs-API konfigurieren möchten.  Dieser Port kann entfernt werden, nachdem der Agent installiert ist und Sie nicht mehr beabsichtigen, die API zu verwenden.   |
        | **8080** (optional) | Agents melden ihren Status alle zehn Minuten über den Port 8080, wenn der Port 443 nicht verfügbar ist. Dieser Status wird im Azure AD-Portal angezeigt. |
   
     
   - Wenn Ihre Firewall Regeln gemäß Ursprungsbenutzern erzwingt, öffnen Sie diese Ports für den Datenverkehr aus Windows-Diensten, die als Netzwerkdienst ausgeführt werden.
   - Wenn Ihre Firewall oder Ihr Proxy das Angeben sicherer Suffixe zulässt, fügen Sie Verbindungen zu \*.msappproxy.net\* und „.servicebus.windows.net“ hinzu. Aktivieren Sie andernfalls den Zugriff auf die [IP-Adressbereiche für das Azure-Rechenzentrum](https://www.microsoft.com/download/details.aspx?id=41653), die wöchentlich aktualisiert werden.
   - Ihre Agents benötigen für die Erstregistrierung Zugriff auf „login.windows.net“ und „login.microsoftonline.com“. Öffnen Sie Ihre Firewall auch für diese URLs.
   - Geben Sie für die Zertifikatüberprüfung folgende URLs frei: „mscrl.microsoft.com:80“, „crl.microsoft.com:80“, „ocsp.msocsp.com:80“ und „www\.microsoft.com:80“. Da diese URLs für die Zertifikatüberprüfung in Verbindung mit anderen Microsoft-Produkten verwendet werden, haben Sie diese möglicherweise bereits freigegeben.

>[!NOTE]
> Die Installation des Agents für die Cloudbereitstellung unter Windows Server Core wird nicht unterstützt.




### <a name="additional-requirements"></a>Zusätzliche Anforderungen
- [Microsoft .NET Framework 4.7.1](https://www.microsoft.com/download/details.aspx?id=56116) 

#### <a name="tls-requirements"></a>TLS-Anforderungen

>[!NOTE]
>Transport Layer Security (TLS) ist ein Protokoll, das für eine sichere Kommunikation sorgt. Das Ändern der TLS-Einstellungen wirkt sich auf die Gesamtstruktur aus. Weitere Informationen finden Sie unter [Update zur Aktivierung von TLS 1.1 und TLS 1.2 als sichere Standardprotokolle in WinHTTP unter Windows](https://support.microsoft.com/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi).

Auf dem Windows-Server, auf dem der Agent für die Azure AD Connect-Cloudbereitstellung gehostet wird, muss TLS 1.2 aktiviert sein, bevor Sie den Agent installieren.

Führen Sie diese Schritte aus, um TLS 1.2 zu aktivieren.

1. Legen Sie die folgenden Registrierungsschlüssel fest:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Starten Sie den Server neu.




## <a name="next-steps"></a>Nächste Schritte 

- [Was ist die Identitätsbereitstellung?](what-is-provisioning.md)
- [Was ist die Azure AD Connect-Cloudbereitstellung?](what-is-cloud-provisioning.md)