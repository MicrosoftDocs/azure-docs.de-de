---
title: 'Azure AD Connect: Voraussetzungen und Hardware | Microsoft-Dokumentation'
description: In diesem Artikel werden die Voraussetzungen und die Hardwareanforderungen für Azure AD Connect beschrieben.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/05/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: edace0298514d1fc3cfd3afcff73fa0d29e18f0c
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96858772"
---
# <a name="prerequisites-for-azure-ad-connect"></a>Voraussetzungen für Azure AD Connect
In diesem Artikel werden die Voraussetzungen und die Hardwareanforderungen für Azure Active Directory (Azure AD) Connect beschrieben.

## <a name="before-you-install-azure-ad-connect"></a>Vor der Installation von Azure AD Connect
Vor der Installation von Azure AD Connect gibt es einige Dinge, die Sie benötigen.

### <a name="azure-ad"></a>Azure AD
* Sie benötigen einen Azure AD-Mandanten. Sie können diesen mit einer [kostenlosen Azure-Testversion erhalten](https://azure.microsoft.com/pricing/free-trial/). Sie können eines der folgenden Portale verwenden, um Azure AD Connect zu verwalten:
  * [Azure-Portal](https://portal.azure.com)
  * [Office-Portal](https://portal.office.com)
* [Fügen Sie die Domäne hinzu](../fundamentals/add-custom-domain.md) , die Sie in Azure AD verwenden möchten, und überprüfen Sie sie. Wenn Sie beispielsweise planen, „contoso.com“ für Ihre Benutzer zu verwenden, müssen Sie sicherstellen, dass diese Domäne überprüft wurde und nicht nur die Standarddomäne „contoso.onmicrosoft.com“ verwendet wird.
* In einem Azure AD-Mandanten sind standardmäßig 50.000 Objekte zulässig. Wenn Sie Ihre Domäne überprüfen, wird der Grenzwert auf 300.000 Objekte erhöht. Wenn Sie noch mehr Objekte in Azure AD benötigen, stellen Sie eine Supportanfrage, um den Grenzwert noch weiter erhöhen zu lassen. Wenn Sie mehr als 500.000 Objekte verwalten müssen, benötigen Sie eine Lizenz, z. B. für Microsoft 365, Azure AD Premium oder Enterprise Mobility + Security.

### <a name="prepare-your-on-premises-data"></a>Vorbereiten Ihrer lokalen Daten
* Verwenden Sie [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) zum Ermitteln von Fehlern wie Duplikaten und Formatierungsproblemen in Ihrem Verzeichnis, bevor Sie eine Synchronisierung mit Azure AD und Microsoft 365 durchführen.
* Lesen Sie den Artikel über die [optionalen Synchronisierungsfeatures, die Sie in Azure AD aktivieren können](how-to-connect-syncservice-features.md), und bestimmen Sie, welche Features Sie aktivieren sollten.

### <a name="on-premises-active-directory"></a>Lokales Active Directory
* Die Active Directory-Schemaversion und die Funktionsebene der Gesamtstruktur müssen Windows Server 2003 oder höher entsprechen. Die Domänencontroller können unter einer beliebigen Version ausgeführt werden, sofern die Anforderungen an die Schemaversion und an die Gesamtstrukturebene erfüllt sind.
* Wenn Sie das Feature *Kennwortrückschreiben* verwenden möchten, müssen die Domänencontroller unter Windows Server 2012 oder höher ausgeführt werden.
* Der von Azure AD verwendete Domänencontroller darf nicht schreibgeschützt sein. Die Verwendung eines schreibgeschützten Domänencontrollers (Read-Only Domain Controller, RODC) wird *nicht unterstützt*, und Azure AD Connect folgt keinen Umleitungen für Schreibvorgänge.
* Die Verwendung von lokalen Gesamtstrukturen oder Domänen mit NetBIOS-Namen, die einen Punkt (.) enthalten, wird *nicht unterstützt*.
* Wir empfehlen das [Aktivieren des Active Directory-Papierkorbs](how-to-connect-sync-recycle-bin.md).

### <a name="powershell-execution-policy"></a>PowerShell-Ausführungsrichtlinie
Azure Active Directory Connect führt signierte PowerShell-Skripts im Rahmen der Installation aus. Stellen Sie sicher, dass die PowerShell-Ausführungsrichtlinie das Ausführen von Skripts zulässt.

Die empfohlene Ausführungsrichtlinie während der Installation ist „RemoteSigned“.

Weitere Informationen zum Festlegen der PowerShell-Ausführungsrichtlinie finden Sie unter [Set-ExecutionPolicy](/powershell/module/microsoft.powershell.security/set-executionpolicy).


### <a name="azure-ad-connect-server"></a>Azure AD Connect-Server
Der Azure AD Connect-Server enthält kritische Identitätsdaten. Es ist wichtig, den administrativen Zugriff auf diesen Server ordnungsgemäß zu schützen. Berücksichtigen Sie die Richtlinien unter [Schützen des privilegierten Zugriffs](/windows-server/identity/securing-privileged-access/securing-privileged-access). 

Der Azure AD Connect-Server muss wie im [Active Directory-Verwaltungsebenenmodell](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) beschrieben als Komponente der Ebene 0 behandelt werden. 

Weitere Informationen zum Schützen Ihrer Active Directory-Umgebung finden Sie unter [Bewährte Methoden für den Schutz von Active Directory](/windows-server/identity/ad-ds/plan/security-best-practices/best-practices-for-securing-active-directory).

#### <a name="installation-prerequisites"></a>Voraussetzungen für die Installation

- Azure AD Connect muss auf einem in die Domäne eingebundenen Computer unter Windows Server 2012 oder höher installiert werden. 
- Azure AD Connect kann nicht unter Small Business Server oder Windows Server Essentials vor 2019 (Windows Server Essentials 2019 wird unterstützt) installiert werden. Der Server muss Windows Server Standard oder höher verwenden. 
- Auf dem Azure AD Connect Server muss eine vollständige GUI installiert sein. Die Installation von Azure AD Connect unter Windows Server Core wird nicht unterstützt. 
- Auf dem Azure AD Connect-Server darf die Gruppenrichtlinie für die PowerShell-Aufzeichnung nicht aktiviert sein, wenn Sie die Konfiguration von Active Directory-Verbunddienste (Active Directory Federation Services, AD FS) mit dem Azure AD Connect-Assistenten verwalten. Sie können die PowerShell-Aufzeichnung aktivieren, wenn Sie den Azure AD Connect-Assistenten zum Verwalten der Synchronisierungskonfiguration verwenden. 
- Wenn AD FS bereitgestellt wird, gelten die folgenden Voraussetzungen: 
    - Die Server, auf denen AD FS oder der Webanwendungsproxy installiert wird, müssen unter Windows Server 2012 R2 oder höher ausgeführt werden. Zur Remoteinstallation muss auf diesen Servern die Windows-Remoteverwaltung aktiviert sein. 
    - Sie müssen TLS-/SSL-Zertifikate konfigurieren. Weitere Informationen finden Sie unter [Verwalten von SSL-/TLS-Protokollen und Verschlüsselungssammlungen für AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) und [Verwalten von SSL-Zertifikaten in AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-certificates-ad-fs-wap).
    - Sie müssen die Namensauflösung konfigurieren. 
- Wenn Ihre globalen Administratoren MFA aktiviert haben, *muss* die URL https://secure.aadcdn.microsoftonline-p.com in der Liste vertrauenswürdiger Websites enthalten sein. Sie werden aufgefordert, diese Website zur Liste vertrauenswürdiger Websites hinzuzufügen, wenn Sie zu einer MFA-Abfrage aufgefordert werden und diese zuvor noch nicht hinzugefügt wurde. Sie können dafür den Internet Explorer verwenden.

#### <a name="harden-your-azure-ad-connect-server"></a>Schützen des Azure AD Connect-Servers 
Es wird empfohlen, den Azure AD Connect-Server zuverlässig zu schützen, um die Angriffsfläche für Sicherheitsangriffe für diese wichtige Komponente Ihrer IT-Umgebung zu verringern. Wenn Sie die folgenden Empfehlungen befolgen, können Sie einige Sicherheitsrisiken für Ihre Organisation minimieren.

- Behandeln Sie Azure AD Connect genauso wie einen Domänencontroller und andere Ressourcen der Ebene 0. Weitere Informationen finden Sie unter [Active Directory-Verwaltungsebenenmodell](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material).
- Beschränken Sie den administrativen Zugriff auf den Azure AD Connect-Server ausschließlich auf Domänenadministratoren oder andere streng kontrollierte Sicherheitsgruppen.
- Erstellen Sie ein [dediziertes Konto für alle Mitarbeiter mit privilegiertem Zugriff](/windows-server/identity/securing-privileged-access/securing-privileged-access). Administratoren sollten in Konten mit hohen Berechtigungen nicht im Internet surfen, ihre E-Mails abfragen oder alltägliche Produktivitätsaufgaben ausführen.
- Folgen Sie den Anweisungen unter [Schützen des privilegierten Zugriffs](/windows-server/identity/securing-privileged-access/securing-privileged-access). 
- Lehnen Sie die Verwendung der NTLM-Authentifizierung beim AADConnect-Server ab. Hierfür gibt es verschiedene Möglichkeiten: [Einschränken von NTLM auf dem AADConnect-Server](/windows/security/threat-protection/security-policy-settings/network-security-restrict-ntlm-outgoing-ntlm-traffic-to-remote-servers) und [Einschränken von NTLM in einer Domäne](/windows/security/threat-protection/security-policy-settings/network-security-restrict-ntlm-ntlm-authentication-in-this-domain)
- Stellen Sie sicher, dass für jeden Computer ein eindeutiges lokales Administratorkennwort vorhanden ist. Weitere Informationen finden Sie unter [Microsoft-Sicherheitsempfehlung: Local Administrator Password Solution (LAPS)](https://support.microsoft.com/help/3062591/microsoft-security-advisory-local-administrator-password-solution-laps). Mit dieser Lösung können eindeutige zufällige Kennwörter auf jeder Arbeitsstation konfiguriert werden, und für den Server werden die Kennwörter in Active Directory gespeichert und durch eine ACL geschützt. Nur berechtigte autorisierte Benutzer können diese lokalen Kennwörter für das Administratorkonto lesen oder eine Rücksetzung anfordern. Sie können die LAPS für die Verwendung auf Arbeitsstationen und Servern aus dem [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=46899) abrufen. Weitere Informationen zum Betreiben einer Umgebung mit LAPS und Arbeitsstationen mit privilegiertem Zugriff (Privileged Access Workstations, PAWs) finden Sie unter [Prinzip der vertrauenswürdigen Quelle für betriebliche Standards](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material#operational-standards-based-on-clean-source-principle). 
- Implementieren Sie dedizierte [Arbeitsstationen mit privilegiertem Zugriff](/windows-server/identity/securing-privileged-access/privileged-access-workstations) für alle Mitarbeiter mit privilegiertem Zugriff auf die Informationssysteme Ihrer Organisation. 
- Berücksichtigen Sie diese [zusätzlichen Richtlinien](/windows-server/identity/ad-ds/plan/security-best-practices/reducing-the-active-directory-attack-surface), um die Angriffsfläche Ihrer Active Directory-Umgebung zu verringern.


### <a name="sql-server-used-by-azure-ad-connect"></a>Von Azure AD Connect verwendete SQL Server-Datenbank
* Azure AD Connect erfordert eine SQL Server-Datenbank zum Speichern von Identitätsdaten. Standardmäßig wird SQL Server 2012 Express LocalDB (eine einfache Version von SQL Server Express) installiert. Für SQL Server Express gilt eine Größenbeschränkung von 10 GB. Dies ermöglicht Ihnen das Verwalten von ca. 100.000 Objekten. Wenn Sie eine größere Anzahl von Verzeichnisobjekten verwalten möchten, müssen Sie im Installations-Assistenten auf eine andere Installation von SQL Server verweisen. Der SQL Server-Installationstyp kann sich auf die [Leistung von Azure AD Connect](./plan-connect-performance-factors.md#sql-database-factors) auswirken.
* Wenn Sie eine andere Installation von SQL Server verwenden, gelten die folgenden Anforderungen:
  * Azure AD Connect unterstützt alle SQL Server-Versionen von 2012 (mit dem neuesten Service Pack) bis SQL Server 2019. Azure SQL-Datenbank wird als Datenbank *nicht unterstützt*.
  * Sie müssen eine SQL-Sortierung ohne Berücksichtigung der Groß- und Kleinschreibung verwenden. Diese Sortierungen werden durch „\_CI_“ in ihrem Namen bestimmt. Eine Sortierung mit Berücksichtigung der Groß- und Kleinschreibung und „\_CS_“ im Namen wird *nicht unterstützt*.
  * Sie können jeweils nur ein Synchronisierungsmodul pro SQL-Instanz verwenden. Das Freigeben einer SQL-Instanz mit FIM/MIM Sync, DirSync oder Azure AD Sync wird *nicht unterstützt*.

### <a name="accounts"></a>Konten
* Sie müssen über ein globales Azure AD-Administratorkonto für den Azure AD-Mandanten verfügen, den Sie integrieren möchten. Bei diesem Konto muss es sich um ein *Geschäfts-, Schul- oder Unikonto* handeln, und es darf kein *Microsoft-Konto* sein.
* Wenn Sie [Expresseinstellungen](reference-connect-accounts-permissions.md#express-settings-installation) verwenden oder ein Upgrade von DirSync durchführen, müssen Sie über ein Unternehmensadministratorkonto für Ihre lokale Active Directory-Instanz verfügen.
* Wenn Sie den Installationspfad für benutzerdefinierte Einstellungen verwenden, stehen Ihnen mehr Optionen zur Verfügung. Weitere Informationen finden Sie unter [Einstellungen für die benutzerdefinierte Installation](reference-connect-accounts-permissions.md#custom-installation-settings).

### <a name="connectivity"></a>Konnektivität
* Der Azure AD Connect-Server benötigt die DNS-Auflösung sowohl für das Intranet als auch für das Internet. Der DNS-Server muss Namen sowohl zu Ihrem lokalen Active Directory als auch zu den Azure AD-Endpunkten auflösen können.
* Wenn Sie in Ihrem Intranet Firewalls verwenden und die Ports zwischen den Azure AD Connect-Servern und Ihren Domänencontrollern öffnen müssen, lesen Sie die Informationen unter [Azure AD Connect-Ports](reference-connect-ports.md).
* Wenn Ihr Proxy oder Ihre Firewall den Zugriff auf bestimmte URLs beschränkt, müssen die unter [Office 365-URLs und -IP-Adressbereiche](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) dokumentierten URLs geöffnet werden. Weitere Informationen finden Sie auch unter [Hinzufügen der Azure-Portal-URLs zu einer Liste sicherer Adressen für Ihre Firewall oder Ihren Proxyserver](../../azure-portal/azure-portal-safelist-urls.md?tabs=public-cloud)
  * Wenn Sie die Microsoft Cloud in Deutschland oder die Microsoft Azure Government-Cloud verwenden, finden Sie die entsprechenden URLs unter [Azure AD Connect: Besondere Überlegungen zu Instanzen](reference-connect-instances.md).
* Azure AD Connect (Version 1.1.614.0 und höher) verwendet standardmäßig TLS 1.2 für die Verschlüsselung der Kommunikation zwischen dem Synchronisierungsmodul und Azure AD. Wenn TLS 1.2 auf dem zugrunde liegenden Betriebssystem nicht verfügbar ist, greift Azure AD Connect schrittweise auf älter Protokolle zurück (TLS 1.1 und TLS 1.0).
* Vor der Version 1.1.614.0 verwendet Azure AD Connect standardmäßig TLS 1.0 für die Verschlüsselung der Kommunikation zwischen dem Synchronisierungsmodul und Azure AD. Folgen Sie zur Änderung in TLS 1.2 den Schritten in [Aktivieren von TLS 1.2 für Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Wenn Sie einen ausgehenden Proxy für die Verbindung mit dem Internet verwenden, muss die folgende Einstellung in der Datei **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** hinzugefügt werden, um dem Installations-Assistenten und der Azure AD Connect-Synchronisierung das Herstellen einer Verbindung mit dem Internet und Azure AD zu ermöglichen. Dieser Text muss am Ende der Datei eingegeben werden. Im folgenden Code steht *&lt;PROXYADDRESS&gt;* für die tatsächliche Proxy-IP-Adresse oder den tatsächlichen Hostnamen.

    ```
        <system.net>
            <defaultProxy>
                <proxy
                usesystemdefault="true"
                proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
                bypassonlocal="true"
                />
            </defaultProxy>
        </system.net>
    ```

* Wenn für den Proxyserver eine Authentifizierung erforderlich ist, muss sich das [Dienstkonto](reference-connect-accounts-permissions.md#adsync-service-account) in der Domäne befinden. Verwenden Sie den Installationspfad für benutzerdefinierte Einstellungen, um ein [benutzerdefiniertes Dienstkonto](how-to-connect-install-custom.md#install-required-components) anzugeben. Außerdem müssen Sie in „machine.config“ eine weitere Änderung vornehmen. Durch diese Änderung in „machine.config“ antworten der Installations-Assistent und das Synchronisierungsmodul auf Authentifizierungsanfragen des Proxyservers. Auf allen Seiten des Installations-Assistenten mit Ausnahme der Seite **Konfigurieren** werden die Anmeldeinformationen des angemeldeten Benutzers verwendet. Am Ende des Installations-Assistenten wird auf der Seite **Konfigurieren** der Kontext in das von Ihnen erstellte [Dienstkonto](reference-connect-accounts-permissions.md#adsync-service-account) geändert. Der Abschnitt in der Datei „machine.config“ sollte wie folgt aussehen:

    ```
        <system.net>
            <defaultProxy enabled="true" useDefaultCredentials="true">
                <proxy
                usesystemdefault="true"
                proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
                bypassonlocal="true"
                />
            </defaultProxy>
        </system.net>
    ```

* Wenn die Proxykonfiguration in einem vorhandenen Setup erfolgt, muss der **Microsoft Azure AD Sync-Dienst** einmal neu gestartet werden, damit Azure AD Connect die Proxykonfiguration lesen und das Verhalten aktualisieren kann. 
* Wenn Azure AD Connect im Rahmen der Verzeichnissynchronisierung eine Webanforderung an Azure AD sendet, kann es bis zu 5 Minuten dauern, bis Azure AD antwortet. In der Regel ist für Proxyserver die Leerlaufzeitüberschreitung für Verbindungen konfiguriert. Stellen Sie sicher, dass die Konfiguration auf mindestens 6 Minuten festgelegt ist.

Weitere Informationen finden Sie im MSDN-Artikel über das [defaultProxy-Element](/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings).
Informationen zu Problemen mit der Konnektivität finden Sie unter [Beheben von Konnektivitätsproblemen](tshoot-connect-connectivity.md).

### <a name="other"></a>Andere
Optional: Verwenden Sie ein Testbenutzerkonto zum Überprüfen der Synchronisierung.

## <a name="component-prerequisites"></a>Voraussetzungen an Komponenten
### <a name="powershell-and-net-framework"></a>PowerShell und .NET Framework
Azure AD Connect ist abhängig von Microsoft PowerShell und .NET Framework 4.5.1. Auf dem Server muss diese Version oder eine neuere Version installiert sein. Führen Sie abhängig von Ihrer Windows Server-Version die folgenden Aktionen aus:

* Windows Server 2012 R2
  * Microsoft PowerShell ist standardmäßig installiert. Keine Aktion erforderlich.
  * .NET Framework 4.5.1 und neuere Versionen werden über Windows Update angeboten. Stellen Sie in der Systemsteuerung sicher, dass die neuesten Updates von Windows Server installiert sind.
* Windows Server 2012
  * Die aktuelle Version von Microsoft PowerShell steht im [Microsoft Download Center](https://www.microsoft.com/downloads) in Windows Management Framework 4.0 zur Verfügung.
  * .NET Framework 4.5.1 und neuere Versionen sind im [Microsoft Download Center](https://www.microsoft.com/downloads) verfügbar.


### <a name="enable-tls-12-for-azure-ad-connect"></a>Aktivieren von TLS 1.2 für Azure AD Connect
Vor der Version 1.1.614.0 verwendet Azure AD Connect standardmäßig TLS 1.0 für die Verschlüsselung der Kommunikation zwischen dem Server mit dem Synchronisierungsmodul und Azure AD. Sie können .NET-Anwendungen für die standardmäßige Verwendung von TLS 1.2 auf dem Server konfigurieren. Weitere Informationen zu TLS 1.2 finden Sie in der [Microsoft-Sicherheitsempfehlung 2960358](/security-updates/SecurityAdvisories/2015/2960358).

1. Stellen Sie sicher, dass der .NET 4.5.1-Hotfix für Ihr Betriebssystem installiert ist. Weitere Informationen finden Sie in der [Microsoft-Sicherheitsempfehlung 2960358](/security-updates/SecurityAdvisories/2015/2960358). Möglicherweise ist dieser Hotfix oder eine neuere Version bereits auf dem Server installiert.

1. Legen Sie für alle Betriebssysteme diesen Registrierungsschlüssel fest, und starten Sie den Server neu.
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
    "SchUseStrongCrypto"=dword:00000001
    ```
1. Wenn Sie TLS 1.2 auch zwischen dem Server mit dem Synchronisierungsmodul und einer SQL Server-Remoteinstanz aktivieren möchten, müssen Sie sicherstellen, dass die erforderlichen Versionen für die [TLS 1.2-Unterstützung für Microsoft SQL Server](https://support.microsoft.com/kb/3135244) installiert sind.

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Voraussetzungen für die Verbundinstallation und -konfiguration
### <a name="windows-remote-management"></a>Windows-Remoteverwaltung
Wenn Sie Azure AD Connect zum Bereitstellen von AD FS oder des Webanwendungsproxys (WAP) verwenden, überprüfen Sie die folgenden Anforderungen:

* Wenn der Zielserver in die Domäne eingebunden ist, stellen Sie sicher, dass die Windows-Remoteverwaltung aktiviert ist.
  * Verwenden Sie in einem PowerShell-Befehlsfenster mit erhöhten Rechten den Befehl `Enable-PSRemoting –force`.
* Wenn der Zielserver kein in die Domäne eingebundener WAP-Computer ist, gibt es einige zusätzliche Anforderungen:
  * Auf dem Zielcomputer (WAP-Computer):
    * Stellen Sie sicher, dass die Windows-Remoteverwaltung/der WS-Verwaltungsdienst (WinRM) über das Dienste-Snap-In ausgeführt wird.
    * Verwenden Sie in einem PowerShell-Befehlsfenster mit erhöhten Rechten den Befehl `Enable-PSRemoting –force`.
  * Auf dem Computer, auf dem der Assistent ausgeführt wird (wenn der Zielcomputer nicht in die Domäne eingebunden ist oder sich in einer nicht vertrauenswürdigen Domäne befindet):
    * Verwenden Sie in einem PowerShell-Befehlsfenster mit erhöhten Rechten den Befehl `Set-Item.WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`.
    * Im Server-Manager:
      * Fügen Sie einem Computerpool einen DMZ-WAP-Host hinzu. Wählen Sie im Server-Manager die Optionen **Verwalten** > **Server hinzufügen** aus, und verwenden Sie dann die Registerkarte **DNS**.
      * Klicken Sie auf der Registerkarte **Server-Manager – Alle Server** mit der rechten Maustaste auf den WAP-Server, und wählen Sie **Verwalten als** aus. Geben Sie die lokalen (keine domänenspezifischen) Anmeldeinformationen für den WAP-Computer ein.
      * Um die PowerShell-Remotekonnektivität zu überprüfen, klicken Sie auf der Registerkarte **Server-Manager – Alle Server** mit der rechten Maustaste auf den WAP-Server, und wählen Sie **Windows PowerShell** aus. Daraufhin sollte eine PowerShell-Remotesitzung geöffnet werden, um sicherzustellen, dass PowerShell-Remotesitzungen hergestellt werden können.

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL-Zertifikatanforderungen
* Es wird empfohlen, dasselbe TLS-/SSL-Zertifikat für alle Knoten Ihrer AD FS-Farm und für alle Webanwendungsproxy-Server zu verwenden.
* Das Zertifikat muss ein X.509-Zertifikat sein.
* Sie können ein selbstsigniertes Zertifikat für Verbundserver in einer Testumgebung verwenden. Für Produktionsumgebungen empfiehlt es sich, das Zertifikat von einer öffentlichen Zertifizierungsstelle zu erwerben.
  * Stellen Sie bei Verwendung eines nicht öffentlich vertrauenswürdigen Zertifikats sicher, dass dem auf jedem Webanwendungsproxy-Server installierten Zertifikat sowohl auf dem lokalen Server als auch auf allen Verbundservern vertraut wird.
* Die Identität des Zertifikats muss mit dem Namen des Verbunddiensts (z.B. sts.contoso.com) übereinstimmen.
  * Die Identität ist die Erweiterung eines alternativen Antragstellernamens (Subject Alternative Name, SAN) des Typs „dNSName“, oder der Name des Antragstellers wird als allgemeiner Name angegeben, wenn keine SAN-Einträge vorhanden sind.
  * Im Zertifikat können mehrere SAN-Einträge vorhanden sein, sofern einer der Einträge mit dem Namen des Verbunddiensts übereinstimmt.
  * Wenn Sie Workplace Join verwenden möchten, ist ein zusätzlicher alternativer Antragstellername mit dem Wert **enterpriseregistration.** gefolgt vom UPN-Suffix (User Principal Name, Benutzerprinzipalname) Ihrer Organisation (z. B. „enterpriseregistration.contoso.com“) erforderlich.
* Auf CNG-Schlüsseln (Cryptography API: Next Generation) und Schlüsselspeicheranbietern (Key Storage Providers, KSPs) basierende Zertifikate werden nicht unterstützt. Dies bedeutet, dass Sie ein auf einem Kryptografiedienstanbieter (Cryptographic Service Provider, CSP) basierendes Zertifikat verwenden müssen und kein Zertifikat von einem KSP.
* Platzhalterzertifikate werden unterstützt.

### <a name="name-resolution-for-federation-servers"></a>Namensauflösung für Verbundserver
* Richten Sie DNS-Einträge für den AD FS-Namen (z. B. „sts.contoso.com“) für das Intranet (Ihren internen DNS-Server) sowie für das Extranet (den öffentlichen DNS-Server über Ihre Domänenregistrierungsstelle) ein. Stellen Sie sicher, dass Sie für den Intranet-DNS-Eintrag A-Einträge und keine CNAME-Einträge verwenden. Die Verwendung von A-Einträgen ist erforderlich, damit die Windows-Authentifizierung auf Ihrem in die Domäne eingebundenen Computer ordnungsgemäß funktioniert.
* Wenn Sie mehr als einen AD FS-Server oder Webanwendungsproxy-Server bereitstellen, müssen Sie unbedingt den Lastenausgleich konfigurieren und sicherstellen, dass die DNS-Einträge für den AD FS-Namen (z. B. „sts.contoso.com“) auf den Lastenausgleich verweisen.
* Damit die integrierte Windows-Authentifizierung für Browseranwendungen bei Verwendung von Internet Explorer in Ihrem Intranet funktioniert, müssen Sie sicherstellen, dass der AD FS-Name (z. B. „sts.contoso.com“) der Intranetzone in Internet Explorer hinzugefügt wird. Diese Anforderung kann über Gruppenrichtlinien gesteuert und für alle Ihre in die Domäne eingebundenen Computer bereitgestellt werden.

## <a name="azure-ad-connect-supporting-components"></a>Azure AD Connect unterstützende Komponenten
Azure AD Connect installiert die folgenden Komponenten auf dem Server, auf dem Azure AD Connect installiert ist. Diese Liste ist für eine einfache Expressinstallation. Wenn Sie auf der Seite **Synchronisierungsdienste installieren** eine andere SQL Server-Version auswählen, wird SQL Express LocalDB nicht lokal installiert.

* Azure AD Connect Health
* Microsoft SQL Server 2012 – Befehlszeilenprogramme
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Microsoft Visual C++ 2013 Redistributionspaket

## <a name="hardware-requirements-for-azure-ad-connect"></a>Hardwareanforderungen für Azure AD Connect
In der folgenden Tabelle sind die Mindestanforderungen für den Azure AD Connect-Synchronisierungscomputer aufgeführt.

| Anzahl der Objekte in Active Directory | CPU | Arbeitsspeicher | Festplattengröße |
| --- | --- | --- | --- |
| Weniger als 10.000 |1,6 GHz |4 GB |70 GB |
| 10.000 bis 50.000 |1,6 GHz |4 GB |70 GB |
| 50.000 bis 100.000 |1,6 GHz |16 GB |100 GB |
| Bei 100.000 oder mehr Objekten ist die Vollversion von SQL Server erforderlich. | | | |
| 100.000 bis 300.000 |1,6 GHz |32 GB |300 GB |
| 300.000 bis 600.000 |1,6 GHz |32 GB |450 GB |
| Mehr als 600.000 |1,6 GHz |32 GB |500 GB |

Die Mindestanforderungen für Computer mit AD FS oder Webanwendungs-Proxyservern lauten wie folgt:

* CPU: Doppelkern mit 1,6 GHz oder mehr
* Memory: Mindestens 2 GB
* Azure-VM: A2-Konfiguration oder höher

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md).
