---
title: FAQ zu Konfigurations- und Verwaltungsproblemen
titleSuffix: Azure Cloud Services
description: Dieser Artikel behandelt häufig gestellte Fragen zur Konfiguration und Verwaltung von Microsoft Azure Cloud Services.
services: cloud-services
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/23/2018
ms.author: genli
ms.openlocfilehash: c4497805e64ef303c9d7340c48a49027b3a26bef
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011019"
---
# <a name="configuration-and-management-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Probleme mit der Konfiguration und Verwaltung von Microsoft Azure Cloud Services: Häufig gestellte Fragen (FAQs)

Dieser Artikel enthält häufig gestellte Fragen zur Konfiguration und Verwaltung von [Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services). Informationen zur Größe finden sie auch unter [Größen für Clouddienste](cloud-services-sizes-specs.md) .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

**Zertifikate**

- [Warum ist die Zertifikatkette meines Clouddienst-TSL/SSL-Zertifikats unvollständig?](#why-is-the-certificate-chain-of-my-cloud-service-tlsssl-certificate-incomplete)
- [Welchen Zweck hat das „Windows Azure Tools Encryption Certificate for Extensions“ (Windows Azure Tools-Verschlüsselungszertifikat für Erweiterungen)?](#what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions)
- [Wie kann ich eine Zertifikatsignieranforderung (Certificate Signing Request, CSR) generieren, ohne eine RDP-Verbindung mit der Instanz herzustellen?](#how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance)
- [Mein Clouddienst-Verwaltungszertifikat läuft ab. Wie kann ich es verlängern?](#my-cloud-service-management-certificate-is-expiring-how-to-renew-it)
- [Wie wird die Installation des TLS/SSL-Hauptzertifikats (PFX) und des Zwischenzertifikats (P7B) automatisiert?](#how-to-automate-the-installation-of-main-tlsssl-certificatepfx-and-intermediate-certificatep7b)
- [Wozu dient das Microsoft Azure-Dienstverwaltungszertifikat für MachineKey?](#what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate)

**Überwachung und Protokollierung**

- [Welche Clouddienstfunktionen für die Anwendungsverwaltung und -überwachung sind im Azure-Portal geplant?](#what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications)
- [Warum schreibt IIS keine Einträge mehr in das Protokollverzeichnis?](#why-does-iis-stop-writing-to-the-log-directory)
- [Wie aktiviere ich WAD-Protokollierung für Clouddienste?](#how-do-i-enable-wad-logging-for-cloud-services)

**Network Configuration**

- [Wie richte ich das Leerlauftimeout für Azure Load Balancer ein?](#how-do-i-set-the-idle-timeout-for-azure-load-balancer)
- [Wie verknüpfe ich eine statische IP-Adresse mit meinem Clouddienst?](#how-do-i-associate-a-static-ip-address-to-my-cloud-service)
- [Welche Features und Funktionen bieten grundlegende Azure-IPS/IDS und -DDoS?](#what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides)
- [Wie wird HTTP/2 für eine Cloud Services-VM aktiviert?](#how-to-enable-http2-on-cloud-services-vm)

**Berechtigungen**

- [Können interne Microsoft-Techniker ohne Berechtigung eine Remotedesktopverbindung mit Clouddienstinstanzen herstellen?](#can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission)
- [Ich kann über die RDP-Datei keine Remotedesktopverbindung mit einer Clouddienst-VM herstellen. Ich erhalte folgenden Fehler: Authentifizierungsfehler (Code: 0x80004005)](#i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005)

**Skalieren**

- [Ich kann nicht über X Instanzen hinaus skalieren.](#i-cannot-scale-beyond-x-instances)
- [Wie kann ich die automatische Skalierung basierend auf Speichermetriken konfigurieren?](#how-can-i-configure-auto-scale-based-on-memory-metrics)

**Allgemein**

- [Wie füge ich meiner Website „nosniff“ hinzu?](#how-do-i-add-nosniff-to-my-website)
- [Wie passe ich IIS für eine Webrolle an?](#how-do-i-customize-iis-for-a-web-role)
- [Wie hoch ist die Kontingentgrenze für meinen Clouddienst?](#what-is-the-quota-limit-for-my-cloud-service)
- [Warum wird auf dem Laufwerk meiner Clouddienst-VM nur sehr wenig freier Speicherplatz angezeigt?](#why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space)
- [Wie kann ich meinen Cloud Services automatisch eine Antischadsoftware-Erweiterung hinzufügen?](#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way)
- [Wie wird die Servernamensanzeige (Server Name Indication, SNI) für Cloud Services aktiviert?](#how-to-enable-server-name-indication-sni-for-cloud-services)
- [Wie kann ich meinem Azure-Clouddienst Tags hinzufügen?](#how-can-i-add-tags-to-my-azure-cloud-service)
- [Die SDK-Version meines Clouddiensts wird im Azure-Portal nicht angezeigt. Wie erhalte ich die Version?](#the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that)
- [Ich möchte den Clouddienst für mehrere Monate einstellen. Wie kann ich die Abrechnungskosten für den Clouddienst verringern, ohne die IP-Adresse zu verlieren?](#i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address)


## <a name="certificates"></a>Zertifikate

### <a name="why-is-the-certificate-chain-of-my-cloud-service-tlsssl-certificate-incomplete"></a>Warum ist die Zertifikatkette meines Clouddienst-TSL/SSL-Zertifikats unvollständig?
    
Wir empfehlen, dass Kunden die vollständige Zertifikatskette (untergeordnetes Zertifikat, Zwischenzertifikate und Stammzertifikate) installieren und nicht nur das untergeordnete Zertifikat. Wenn Sie nur das untergeordnete Zertifikat installieren, sind Sie davon abhängig, dass Windows die Zertifikatskette durch durchlaufen der CTL erstellt. Wenn zeitweilige Netzwerk- oder DNS-Probleme in Azure oder Windows Update auftreten, während Windows das Zertifikat überprüft, wird das Zertifikat möglicherweise als ungültig behandelt. Durch Installation der vollständigen Zertifikatskette kann dieses Problem vermieden werden. Der Blogbeitrag [How to install a chained SSL certificate (Installieren eines SSL-Kettenzertifikats)](/archive/blogs/azuredevsupport/how-to-install-a-chained-ssl-certificate) veranschaulicht diese Vorgehensweise.

### <a name="what-is-the-purpose-of-the-windows-azure-tools-encryption-certificate-for-extensions"></a>Welchen Zweck hat das „Windows Azure Tools Encryption Certificate for Extensions“ (Windows Azure Tools-Verschlüsselungszertifikat für Erweiterungen)?

Diese Zertifikate werden automatisch erstellt, wenn dem Clouddienst eine Erweiterung hinzugefügt wird. In den meisten Fällen handelt es sich hierbei um die WAD- oder RDP-Erweiterung. Es kann sich aber auch um die Antimalware- oder Log Collector-Erweiterung handeln. Diese Zertifikate werden nur zum Verschlüsseln und Entschlüsseln der privaten Konfiguration der Erweiterung verwendet. Das Ablaufdatum wird nicht überprüft, sodass es keine Rolle spielt, wenn das Zertifikat abgelaufen ist. 

Sie können diese Zertifikate ignorieren. Wenn Sie die Zertifikate bereinigen möchten, können Sie versuchen, sie alle zu löschen. Azure löst einen Fehler aus, wenn Sie versuchen ein Zertifikat zu löschen, das verwendet wird.

### <a name="how-can-i-generate-a-certificate-signing-request-csr-without-rdp-ing-in-to-the-instance"></a>Wie kann ich eine Zertifikatsignieranforderung (Certficate Signing Request, CSR) generieren, ohne eine RDP-Verbindung mit der Instanz herzustellen?

Weitere Informationen finden Sie im folgenden Richtliniendokument:

[Obtaining a certificate for use with Windows Azure Web Sites (WAWS) (Abrufen eines Zertifikats für die Verwendung in Windows Azure-Websites (WAWS))](https://azure.microsoft.com/blog/obtaining-a-certificate-for-use-with-windows-azure-web-sites-waws/)

Die CSR-Datei ist nur eine Textdatei. Sie muss nicht von dem Computer aus erstellt werden, auf dem das Zertifikat letztlich verwendet wird. Obwohl dieses Dokument für App Service geschrieben ist, ist das Erstellen einer CSR generisch und gilt auch für Cloud Services.

### <a name="my-cloud-service-management-certificate-is-expiring-how-to-renew-it"></a>Mein Clouddienst-Verwaltungszertifikat läuft ab. Wie kann ich es verlängern?

Sie können Ihre Verwaltungszertifikate mit folgenden PowerShell-Befehlen verlängern:

```powershell
Add-AzureAccount
Select-AzureSubscription -Current -SubscriptionName <your subscription name>
Get-AzurePublishSettingsFile
```

Mit **Get-AzurePublishSettingsFile** wird im Azure-Portal unter **Abonnement** > **Verwaltungszertifikate** ein neues Verwaltungszertifikat erstellt. Der Name des neuen Zertifikats sieht wie folgt aus: YourSubscriptionNam]-[CurrentDate]-Anmeldeinformationen

### <a name="how-to-automate-the-installation-of-main-tlsssl-certificatepfx-and-intermediate-certificatep7b"></a>Wie wird die Installation des TLS/SSL-Hauptzertifikats (PFX) und des Zwischenzertifikats (P7B) automatisiert?

Sie können diese Aufgabe mithilfe eines Startskripts („batch/cmd/PowerShell“) automatisieren und das Startskript in der Dienstdefinitionsdatei registrieren. Fügen Sie sowohl das Startskript als auch das Zertifikat (P7B-Datei) dem Projektordner im selben Verzeichnis wie das Startskript hinzu.

### <a name="what-is-the-purpose-of-the-microsoft-azure-service-management-for-machinekey-certificate"></a>Wozu dient das Microsoft Azure-Dienstverwaltungszertifikat für MachineKey?

Dieses Zertifikat dient zum Verschlüsseln von Computerschlüsseln für Azure-Webrollen. Weitere Informationen finden Sie in [dieser Empfehlung](/security-updates/securityadvisories/2018/4092731).

Weitere Informationen finden Sie in den folgenden Artikeln:
- [Konfigurieren und Ausführen von Startaufgaben für einen Clouddienst](./cloud-services-startup-tasks.md)
- [Allgemeine Starttasks für Clouddienste](./cloud-services-startup-tasks-common.md)

## <a name="monitoring-and-logging"></a>Überwachung und Protokollierung

### <a name="what-are-the-upcoming-cloud-service-capabilities-in-the-azure-portal-which-can-help-manage-and-monitor-applications"></a>Welche Clouddienstfunktionen für die Anwendungsverwaltung und -überwachung sind im Azure-Portal geplant?

Die Option zum Generieren eines neuen Zertifikats für Remotedesktopprotokoll (RDP) ist bald verfügbar. Alternativ können Sie das folgende Skript ausführen:

```powershell
$cert = New-SelfSignedCertificate -DnsName yourdomain.cloudapp.net -CertStoreLocation "cert:\LocalMachine\My" -KeyLength 20 48 -KeySpec "KeyExchange"
$password = ConvertTo-SecureString -String "your-password" -Force -AsPlainText
Export-PfxCertificate -Cert $cert -FilePath ".\my-cert-file.pfx" -Password $password
```
Die Option zum Auswählen eines Blobs oder eines lokalen Speicherorts als CSDEF- und CSCFG-Uploadspeicherort ist in Kürze verfügbar. Mit [New-AzureDeployment](/powershell/module/servicemanagement/azure.service/new-azuredeployment?view=azuresmps-4.0.0) können Sie jeden Speicherortwert festlegen.

Option zum Überwachen von Metriken auf Instanzebene. Zusätzliche Überwachungsfunktionen stehen unter [Überwachung von Clouddiensten](cloud-services-how-to-monitor.md) zur Verfügung.

### <a name="why-does-iis-stop-writing-to-the-log-directory"></a>Warum beendet IIS das Schreiben in das Protokollverzeichnis?
Sie haben das lokale Speicherkontingent für das Schreiben in das Protokollverzeichnis ausgeschöpft. Es gibt drei Möglichkeiten, dies zu korrigieren:
* Aktivieren Sie die Diagnose für IIS und lassen Sie die Diagnose regelmäßig in einen Blob-Speicher verschieben.
* Entfernen Sie Protokolldateien manuell aus dem Protokollierungsverzeichnis.
* Erhöhen Sie die Kontingentgrenze für lokale Ressourcen.

Weitere Informationen finden Sie in den folgenden Dokumenten:
* [Speichern und Anzeigen von Diagnosedaten in Azure Storage](../storage/common/storage-introduction.md)
* [Im Clouddienst werden keine IIS-Protokolle mehr geschrieben.](/archive/blogs/cie/iis-logs-stops-writing-in-cloud-service)

### <a name="how-do-i-enable-wad-logging-for-cloud-services"></a>Wie aktiviere ich WAD-Protokollierung für Clouddienste?
Sie können die Protokollierung durch die Windows Azure-Diagnose (WAD) über folgende Optionen aktivieren:
1. [Aktivieren über Visual Studio](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them)
2. [Aktivieren über .NET-Code](./cloud-services-dotnet-diagnostics.md)
3. [Aktivieren über PowerShell](./cloud-services-diagnostics-powershell.md)

Um die aktuellen WAD-Einstellungen Ihres Clouddiensts abzurufen, können Sie das PS-Cmd [Get-AzureServiceDiagnosticsExtensions](./cloud-services-diagnostics-powershell.md#get-current-diagnostics-extension-configuration) verwenden oder sie über das Portal auf dem Blatt „Clouddienste--> Erweiterungen“ einsehen.


## <a name="network-configuration"></a>Netzwerkkonfiguration

### <a name="how-do-i-set-the-idle-timeout-for-azure-load-balancer"></a>Wie richte ich das Leerlauftimeout für Azure Load Balancer ein?
Sie können das Timeout in der Dienstdefinitionsdatei (CSDEF) wie folgt angeben:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="mgVS2015Worker" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRole1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10100"   idleTimeoutInMinutes="30" />
    </Endpoints>
  </WorkerRole>
```
Weitere Informationen finden Sie unter [New: Configurable Idle Timeout for Azure Load Balancer (Neu: Konfigurierbares Leerlauftimeout für Azure Load Balancer)](https://azure.microsoft.com/blog/new-configurable-idle-timeout-for-azure-load-balancer/).

### <a name="how-do-i-associate-a-static-ip-address-to-my-cloud-service"></a>Wie verknüpfe ich eine statische IP-Adresse mit meinem Clouddienst?
Sie müssen für das Erstellen einer reservierten IP-Adresse eine statische IP-Adresse festlegen. Diese reservierte IP-Adresse kann einem neuen Clouddienst oder einer vorhandenen Bereitstellung zugeordnet werden. Weitere Informationen finden Sie in den folgenden Dokumenten:
* [Verwalten von reservierten IPs](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#manage-reserved-vips)
* [Reservieren der IP-Adresse eines vorhandenen Clouddiensts](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#reserve-the-ip-address-of-an-existing-cloud-service)
* [Zuordnen einer reservierten IP zu einem neuen Clouddienst](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-new-cloud-service)
* [Zuordnen einer reservierten IP zu einer aktiven Bereitstellung](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-running-deployment)
* [Zuordnen einer reservierten IP zu einem Clouddienst mit einer Dienstkonfigurationsdatei](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

### <a name="what-are-the-features-and-capabilities-that-azure-basic-ipsids-and-ddos-provides"></a>Welche Features und Funktionen bieten Azure Basic-IPS/IDS und DDoS?
Azure verfügt über IPS/IDS in physischen Servern des Datencenters, um Bedrohungen abzuwehren. Darüber hinaus können Kunden Sicherheitslösungen von Drittanbietern bereitstellen, z.B. Web Application Firewalls, Netzwerkfirewalls, Antischadsoftware, Angriffserkennungs- und Eindringschutzsysteme (IDS/IPS) und weitere. Weitere Informationen finden Sie unter [Protect your data and assets and comply with global security standards (Schutz Ihrer Daten und Objekte in Übereinstimmung mit globalen Sicherheitsstandards)](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity).

Microsoft überwacht durchgehend Server, Netzwerke und Anwendungen, um Bedrohungen zu erkennen. Azure's mehrgleisiger Ansatz zur Verwaltung von Bedrohungen verwendet Angriffserkennung, Verhinderung von verteilten Denial-of-Service-Angriffen (DDoS), Penetrationstests, Verhaltensanalyse, Anomalieerkennung und Machine Learning, um den Schutz stetig zu verbessern und Risiken zu reduzieren. Microsoft Antimalware für Azure schützt Azure Cloud Services und virtuelle Computer. Darüber hinaus können Sie zusätzlich Sicherheitslösungen von Drittanbietern bereitstellen, z.B. Web Application Firewalls, Netzwerkfirewalls, Antischadsoftware, Angriffserkennungs- und Eindringschutzsysteme (IDS/IPS) und weitere.

### <a name="how-to-enable-http2-on-cloud-services-vm"></a>Wie wird HTTP/2 auf der Cloud Services-VM aktiviert?

Windows 10 und Windows Server 2016 unterstützen HTTP/2 auf Client- und Serverseite. Wenn Ihr Client (Browser) über TLS eine Verbindung mit dem IIS-Server herstellt, wobei HTTP/2 über TLS-Erweiterungen ausgehandelt wird, müssen Sie keine Änderungen auf der Serverseite vornehmen. Dies liegt daran, weil über TLS standardmäßig der h2-14-Header gesendet wird, der die Verwendung von HTTP/2 festlegt. Wenn andererseits der Client einen Upgradeheader zum Upgrade auf HTTP/2 sendet, müssen Sie die folgende Änderung auf der Serverseite vornehmen, um sicherzustellen, dass das Upgrade funktioniert, und Sie eine HTTP/2-Verbindung erhalten. 

1. Führen Sie „regedit.exe“ aus.
2. Navigieren Sie zu folgendem Registrierungsschlüssel: HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HTTP\Parameters.
3. Erstellen Sie einen neuen DWORD-Wert namens **DuoEnabled**.
4. Legen Sie hierfür den Wert 1 fest.
5. Starten Sie den Server neu.
6. Wechseln Sie zu Ihrer **Standardwebsite**, und erstellen Sie unter **Bindungen** eine neue TLS-Bindung mit dem selbstsignierten Zertifikat, das Sie gerade erstellt haben. 

Weitere Informationen finden Sie unter

- [HTTP/2 on IIS](https://blogs.iis.net/davidso/http2) (HTTP/2 auf IIS)
- [Video: HTTP/2 in Windows 10: Browser, Apps and Web Server](https://channel9.msdn.com/Events/Build/2015/3-88) (HTTP/2 in Windows 10: Browser, Apps und Webserver)
         

Diese Schritte könnten über einen Starttask automatisiert werden, sodass immer dann, wenn eine neue PaaS-Instanz erstellt wird, die obigen Änderungen in der Systemregistrierung vorgenommen werden könnten. Weitere Informationen finden Sie unter [Konfigurieren und Ausführen von Startaufgaben für einen Clouddienst](cloud-services-startup-tasks.md).

 
Sobald dies geschehen ist, können Sie mit einer der folgenden Methoden überprüfen, ob HTTP/2 aktiviert wurde:

- Aktivieren Sie die Protokollversion in IIS-Protokollen, und schauen Sie in die IIS-Protokolle. HTTP/2 wird in den Protokollen angezeigt. 
- Aktivieren Sie „F12 Developer Tool“ in Internet Explorer oder Microsoft Edge, und wechseln Sie zur Registerkarte „Netzwerk“, um das Protokoll zu überprüfen. 

Weitere Informationen finden Sie unter [HTTP/2 on IIS](https://blogs.iis.net/davidso/http2) (HTTP/2 auf IIS).

## <a name="permissions"></a>Berechtigungen

### <a name="how-can-i-implement-role-based-access-for-cloud-services"></a>Wie implementiere ich rollenbasierten Zugriff für Cloud Services?
Das Azure RBAC-Modell (Azure Role-Based Access Control, rollenbasierter Zugriff von Azure) wird von Cloud Services nicht unterstützt, da es sich nicht um einen Azure Resource Manager-basierten Dienst handelt.

Weitere Informationen finden Sie unter [Grundlegendes zu den verschiedenen Rollen in Azure](../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="remote-desktop"></a>Remotedesktop

### <a name="can-microsoft-internal-engineers-remote-desktop-to-cloud-service-instances-without-permission"></a>Können interne Microsoft-Techniker ohne Berechtigung eine Remotedesktopverbindung mit Clouddienstinstanzen herstellen?
Bei Microsoft gilt ein strenger Verhaltenskodex, der es internen Technikern nicht erlaubt, eine Remotedesktopverbindung mit einem Clouddienst herzustellen, ohne dass eine schriftliche Genehmigung (in Form einer E-Mail oder schriftlichen Mitteilung) des Besitzers oder Bevollmächtigten vorliegt.

### <a name="i-cannot-remote-desktop-to-cloud-service-vm--by-using-the-rdp-file-i-get-following-error-an-authentication-error-has-occurred-code-0x80004005"></a>Ich kann über die RDP-Datei keine Remotedesktopverbindung mit einer Clouddienst-VM herstellen. Ich erhalte folgenden Fehler: Authentifizierungsfehler (Code: 0x80004005)

Dieser Fehler kann auftreten, wenn Sie die RDP-Datei eines Computers verwenden, der Mitglied von Azure Active Directory ist. Gehen Sie folgendermaßen vor, um das Problem zu beheben:

1. Klicken Sie mit der rechten Maustaste auf die heruntergeladene RDP-Datei, und wählen Sie **Bearbeiten** aus.
2. Fügen Sie vor dem Benutzernamen das Präfix „&#92;“ hinzu. Verwenden Sie z. B. **.\username** anstelle von **username**.

## <a name="scaling"></a>Skalierung

### <a name="i-cannot-scale-beyond-x-instances"></a>Ich kann nicht über X Instanzen hinaus skalieren
Für Ihr Azure-Abonnement gilt ein Limit hinsichtlich der Anzahl von Kernen, die Sie verwenden können. Wenn Sie alle verfügbaren Kerne verwendet haben, können Sie nicht höher skalieren. Beispiel: Wenn Ihr Limit bei 100 Kernen liegt, können Sie für Ihren Clouddienst 100 virtuelle Computerinstanzen der Größe A1 oder 50 virtuelle Computerinstanzen der Größe A2 einrichten.

### <a name="how-can-i-configure-auto-scale-based-on-memory-metrics"></a>Wie kann ich die automatische Skalierung basierend auf Speichermetriken konfigurieren?

Die automatische Skalierung auf Grundlage von Speichermetriken für Cloud Services wird derzeit nicht unterstützt. 

Um dieses Problem zu umgehen, können Sie Application Insights verwenden. Die automatische Skalierung unterstützt Application Insights als Quelle für Metriken und kann die Anzahl der Rolleninstanzen anhand einer Gastmetrik wie „Speicher“ skalieren.  Sie müssen Application Insights in der Paketdatei Ihres Clouddienstprojekts (*.cspkg) konfigurieren und die Azure-Diagnoseerweiterung für den Dienst aktivieren, um dieses Feature zu implementieren.

Weitere Informationen dazu, wie Sie über Application Insights eine benutzerdefinierte Metrik verwenden, um die automatische Skalierung für Cloud Services zu konfigurieren, finden Sie unter [Erste Schritte mit der automatischen Skalierung durch eine benutzerdefinierte Metrik in Azure](../azure-monitor/platform/autoscale-custom-metric.md).

Weitere Informationen zur Integration der Azure-Diagnose in Application Insights für Cloud Services finden Sie unter [Senden von Cloud Services-, Virtual Machines- oder Service Fabric-Diagnosedaten an Application Insights](../azure-monitor/platform/diagnostics-extension-to-application-insights.md).

Weitere Informationen dazu, wie Sie Application Insights für Cloud Services aktivieren, finden Sie unter [Application Insights für Azure Cloud Services](../azure-monitor/app/cloudservices.md).

Weitere Informationen dazu, wie Sie die Azure-Diagnoseprotokollierung für Cloud Services aktivieren, finden Sie unter [Einrichten der Diagnose für Azure Cloud Services und virtuelle Azure-Computer](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them).

## <a name="generic"></a>Allgemein

### <a name="how-do-i-add-nosniff-to-my-website"></a>Wie füge ich meiner Website „nosniff“ hinzu?
Um zu verhindern, dass Clients die MIME-Typen abfangen, fügen Sie eine Einstellung in Ihre Datei *web.config* ein.

```xml
<configuration>
   <system.webServer>
      <httpProtocol>
         <customHeaders>
            <add name="X-Content-Type-Options" value="nosniff" />
         </customHeaders>
      </httpProtocol>
   </system.webServer>
</configuration>
```

Sie können dies auch als Einstellung in IIS hinzufügen. Verwenden Sie den folgenden Befehl und den Artikel [Gängige Starttasks](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe).

```cmd
%windir%\system32\inetsrv\appcmd set config /section:httpProtocol /+customHeaders.[name='X-Content-Type-Options',value='nosniff']
```

### <a name="how-do-i-customize-iis-for-a-web-role"></a>Wie passe ich IIS für eine Webrolle an?
Verwenden Sie das IIS-Startskript aus dem Artikel [Gängige Starttasks](cloud-services-startup-tasks-common.md#configure-iis-startup-with-appcmdexe).

### <a name="what-is-the-quota-limit-for-my-cloud-service"></a>Wie hoch ist die Kontingentgrenze für meinen Clouddienst?
Weitere Informationen finden Sie unter [Dienstspezifische Grenzwerte](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits).

### <a name="why-does-the-drive-on-my-cloud-service-vm-show-very-little-free-disk-space"></a>Warum wird auf dem Laufwerk meiner Clouddienst-VM nur sehr wenig freier Speicherplatz angezeigt?
Hierbei handelt es sich um erwartetes Verhalten, das kein Problem bei Ihrer Anwendung verursachen sollte. Für das Laufwerk %approot% auf virtuellen Azure PaaS-Computern ist das Journaling aktiviert, was praktisch doppelt so viel Speicherplatz verbraucht, wie Dateien normalerweise belegen. Es gibt jedoch einige Punkte zu beachten, durch die dieses Verhalten nicht zu einem Problem wird.

Die Größe des %approot%-Laufwerks wird als \<size of .cspkg + max journal size + a margin of free space> oder 1,5 GB berechnet, je nachdem, welcher Wert größer ist. Die Größe Ihres virtuellen Computers hat keinen Einfluss auf diese Berechnung. (Die Größe des virtuellen Computers betrifft nur die Größe des temporären Laufwerks „C:“) 

Schreibvorgänge in das Laufwerk %aproot% werden nicht unterstützt. Wenn Sie einen Schreibvorgang in den virtuellen Azure-Computer vornehmen, müssen Sie diesen in einer temporären LocalStorage-Ressource (oder einer anderen Option, z.B. Blob-Speicher, Azure Storage usw.) ausführen. Der freie Speicherplatz im Ordner %approot% ist also nicht von Bedeutung. Wenn Sie sich unsicher sind, ob Ihre Anwendung in das Laufwerk %aproot% schreibt, können Sie Ihren Dienst immer ein paar Tage lang ausführen lassen und anschließend die „Vorher/Nachher“-Größe vergleichen. 

Azure wird nichts in das Laufwerk %approot% schreiben. Sobald die VHD-Datei aus CSPKG erstellt und in den virtuellen Azure-Computer eingebunden wurde, kann nur noch die Anwendung in dieses Laufwerk schreiben. 

Die Einstellungen für Journaling sind nicht konfigurierbar, also lässt es sich nicht deaktiveren.

### <a name="how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way"></a>Wie kann ich eine Antischadsoftware-Erweiterung für meine Cloud Services automatisiert hinzufügen?

Sie können mithilfe des PowerShell-Skripts im Starttask die Antischadsoftware-Erweiterung aktivieren. Führen Sie die Schritte in den folgenden Artikeln aus, um sie zu implementieren: 
 
- [Erstellen eines PowerShell-Starttasks](cloud-services-startup-tasks-common.md#create-a-powershell-startup-task)
- [Set-AzureServiceAntimalwareExtension](/powershell/module/servicemanagement/azure.service/Set-AzureServiceAntimalwareExtension?view=azuresmps-4.0.0 )

Weitere Informationen zu den Antimalware-Bereitstellungsszenarien, und wie sie vom Portal aus aktiviert werden, finden Sie unter [Antimalware-Bereitstellungsszenarien](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios).

### <a name="how-to-enable-server-name-indication-sni-for-cloud-services"></a>Wie wird die Servernamensanzeige (Server Name Indication, SNI) für Cloud Services aktiviert?

Sie können SNI in Cloud Services mit einer der folgenden Methoden aktivieren:

**Methode 1: Verwenden von PowerShell**

Die SNI-Bindung kann mithilfe des PowerShell-Cmdlets **New-WebBinding** in einem Starttask für eine Rolleninstanz des Clouddiensts wie unten beschrieben konfiguriert werden:

```powershell
New-WebBinding -Name $WebsiteName -Protocol "https" -Port 443 -IPAddress $IPAddress -HostHeader $HostHeader -SslFlags $sslFlags
```

Wie [hier](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee790567(v=technet.10)) beschrieben, können die $sslFlags einen der folgenden Werte haben:

|Wert|Bedeutung|
------|------
|0|Keine SNI|
|1|SNI aktiviert|
|2|Keine SNI-Bindung, die den zentralen Zertifikatspeicher verwendet|
|3|SNI-Bindung, die den zentralen Zertifikatspeicher verwendet|
 
**Methode 2: Verwenden von Code**

Die SNI-Bindung könnte auch über den Code im Rollenstart konfiguriert werden, wie in diesem [Blogbeitrag](/archive/blogs/jianwu/expose-ssl-service-to-multi-domains-from-the-same-cloud-service) beschrieben:

```csharp
//<code snip> 
                var serverManager = new ServerManager(); 
                var site = serverManager.Sites[0]; 
                var binding = site.Bindings.Add(":443:www.test1.com", newCert.GetCertHash(), "My"); 
                binding.SetAttributeValue("sslFlags", 1); //enables the SNI 
                serverManager.CommitChanges(); 
    //</code snip>
```
    
Bei jedem der obigen Ansätze müssen die entsprechenden Zertifikate (*.pfx) für den spezifischen Hostnamen zuerst mit einem Starttask oder über den Code in der Rolleninstanz installiert werden, damit die SNI-Bindung wirksam wird.

### <a name="how-can-i-add-tags-to-my-azure-cloud-service"></a>Wie kann ich meinem Azure-Clouddienst Tags hinzufügen? 

Der Clouddienst ist eine klassische Ressource. Nur Ressourcen, die mit dem Azure Resource Manager erstellt wurden, unterstützen Tags. Sie können keine Tags auf klassische Ressourcen wie den Clouddienst anwenden. 

### <a name="the-azure-portal-doesnt-display-the-sdk-version-of-my-cloud-service-how-can-i-get-that"></a>Die SDK-Version meines Clouddiensts wird im Azure-Portal nicht angezeigt. Wie erhalte ich die Version?

Wir arbeiten an der Implementierung dieses Features im Azure-Portal. In der Zwischenzeit können Sie die SDK-Version mithilfe folgender PowerShell-Befehle abrufen:

```powershell
Get-AzureService -ServiceName "<Cloud Service name>" | Get-AzureDeployment | Where-Object -Property SdkVersion -NE -Value "" | select ServiceName,SdkVersion,OSVersion,Slot
```

### <a name="i-want-to-shut-down-the-cloud-service-for-several-months-how-to-reduce-the-billing-cost-of-cloud-service-without-losing-the-ip-address"></a>Ich möchte den Clouddienst für mehrere Monate einstellen. Wie kann ich die Abrechnungskosten für den Clouddienst verringern, ohne die IP-Adresse zu verlieren?

Bei einem bereits bereitgestellten Clouddienst wird die Compute- und Speichernutzung in Rechnung gestellt. Daher wird die Speichernutzung selbst dann abgerechnet, wenn Sie die Azure-VM deaktivieren. 

Hier erfahren Sie, wie Sie Ihre Kosten reduzieren können, ohne die IP-Adresse für Ihren Dienst zu verlieren:

1. [Reservieren Sie die IP-Adresse](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip), bevor Sie die Bereitstellungen löschen.  Es fallen nur Gebühren für diese IP-Adresse an. Weitere Informationen zur Abrechnung von IP-Adressen finden Sie unter [IP-Adressen – Preise](https://azure.microsoft.com/pricing/details/ip-addresses/).
2. Löschen Sie die Bereitstellungen. „xxx.cloudapp.net“ darf nicht gelöscht werden, damit Sie sie künftig weiter verwenden können.
3. Wenn Sie den Clouddienst mit derselben IP-Adresse erneut bereitstellen möchten, die Sie in Ihrem Abonnement reserviert haben, informieren Sie sich im Thema zu [Reservierten IP-Adressen für Cloud Services und Virtual Machines](https://azure.microsoft.com/blog/reserved-ip-addresses/).