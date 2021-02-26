---
title: Azure Migrate-Appliance
description: Zusammenfassung der Unterstützung der Azure Migrate-Appliance.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 17ac10fd4e48043b79db5448fad2f29c5eb3f2a3
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98986818"
---
# <a name="azure-migrate-appliance"></a>Azure Migrate-Appliance

In diesem Artikel werden die Voraussetzungen und Supportanforderungen für die Azure Migrate-Appliance zusammengefasst. 

## <a name="deployment-scenarios"></a>Bereitstellungsszenarien

Die Azure Migrate-Appliance wird in den folgenden Szenarien verwendet.

**Szenario** | **Tool** | **Verwendung** 
--- | --- | ---
**Bewertung von VMware-VMs** | Azure Migrate-Serverbewertung | Ermitteln von virtuellen VMware-Computern<br/><br/> Ermitteln der Apps und Abhängigkeiten der Computer<br/><br/> Erfassen von Computer- und Leistungsmetadaten für Bewertungen
**Migration von virtuellen VMware-Computern ohne Agent** | Azure Migrate-Servermigration | Ermitteln von virtuellen VMware-Computern <br/><br/> Replizieren von virtuellen VMware-Computern per Migration ohne Agent
**Bewertung von Hyper-V-VMs** | Azure Migrate-Serverbewertung | Ermitteln von virtuellen Hyper-V-Computern<br/><br/> Erfassen von Computer- und Leistungsmetadaten für Bewertungen
**Bewertung physischer Computer** |  Azure Migrate-Serverbewertung |  Ermitteln physischer Server (oder virtuelle Computer, die als physische Server behandelt werden)<br/><br/> Erfassen von Computer- und Leistungsmetadaten für Bewertungen

## <a name="deployment-methods"></a>Bereitstellungsmethoden

Die Appliance kann mithilfe verschiedener Methoden bereitgestellt werden:

- Die Appliance kann mithilfe einer Vorlage für VMware-VMs und Hyper-V-VMs (OVA-Vorlage für VMware oder VHD für Hyper-V) bereitgestellt werden.
- Wenn Sie keine Vorlage verwenden möchten, können Sie die Appliance für VMware oder Hyper-V mithilfe eines PowerShell-Skripts bereitstellen.
- In Azure Government sollten Sie die Appliance mithilfe eines Skripts bereitstellen.
- Bei physischen Servern stellen Sie die Appliance immer mithilfe eines Skripts bereit.
- Die entsprechenden Downloadlinks finden Sie in den nachstehenden Tabellen.


## <a name="appliance---vmware"></a>Appliance – VMware 

In der folgenden Tabelle sind die Anforderungen der Azure Migrate-Appliance für VMware zusammengefasst.

**Anforderung** | **VMware** 
--- | ---
**Berechtigungen** | Für den lokalen oder Remotezugriff auf die Appliance-Web-App benötigen Sie ein lokales Benutzerkonto oder ein Domänenbenutzerkonto mit Administratorrechten auf dem Applianceserver.
**Appliancekomponenten** | Die Appliance verfügt über die folgenden Komponenten:<br/><br/> - **Verwaltungs-App**: Dies ist eine Web-App für Benutzereingaben während der Appliance-Bereitstellung. Wird beim Bewerten von Computern für die Migration zu Azure verwendet.<br/> - **Ermittlungs-Agent**: Der Agent erfasst Computerkonfigurationsdaten. Wird beim Bewerten von Computern für die Migration zu Azure verwendet.<br/>- **Collector-Agent**: Der Agent sammelt Leistungsdaten. Wird beim Bewerten von Computern für die Migration zu Azure verwendet.<br/>- **Dienst für automatische Aktualisierungen**: Aktualisiert Appliancekomponenten (wird alle 24 Stunden ausgeführt).<br/>- **DRA-Agent**: Orchestriert die VM-Replikation und koordiniert die Kommunikation zwischen replizierten Computern und Azure. Wird nur bei der Replikation von VMware-VMs in Azure mithilfe der Migration ohne Agent verwendet.<br/>- **Gateway**: Sendet replizierte Daten an Azure. Wird nur bei der Replikation von VMware-VMs in Azure mithilfe der Migration ohne Agent verwendet.
**Unterstützte Bereitstellung** | Bereitstellen als VMware VM mit OVA-Vorlage.<br/><br/> Bereitstellen als VMware VM oder physischer Computer mithilfe des PowerShell-Installationsskripts.
**Projektunterstützung** |  Eine Appliance kann einem einzelnen Projekt zugeordnet werden. <br/> Einem einzelnen Projekt kann eine beliebige Anzahl von Appliances zugeordnet werden.<br/> 
**Ermittlungsgrenzwerte** | Eine Appliance kann bis zu 10.000 VMware-VMs in einer vCenter Server-Instanz ermitteln.<br/> Eine Appliance kann eine Verbindung mit einer einzelnen vCenter Server-Instanz herstellen.
**OVA-Vorlage** | Laden Sie sie über das Portal oder [hier](https://go.microsoft.com/fwlink/?linkid=2140333) herunter.<br/><br/> Die Downloadgröße beträgt 11,9 GB.<br/><br/> Die heruntergeladene Appliancevorlage ist mit einer Windows Server 2016-Evaluierungslizenz ausgestattet, die 180 Tage lang gültig ist. Wenn der Evaluierungszeitraum fast abgelaufen ist, empfiehlt es sich, eine neue Appliance herunterzuladen und bereitzustellen oder die Betriebssystemlizenz der Appliance-VM zu aktivieren.
**PowerShell-Skript** | Weitere Informationen finden Sie in diesem [Artikel](./deploy-appliance-script.md#set-up-the-appliance-for-vmware).<br/><br/> 
**Software/Hardware** |  Die Appliance sollte auf einem Computer mit Windows Server 2016, 32 GB RAM, 8 vCPUs, etwa 80 GB Speicherplatz und einem externen virtuellen Switch ausgeführt werden.<br/> Die Appliance erfordert Internetzugriff, entweder direkt oder über einen Proxy.<br/><br/> Wenn Sie die Appliance auf einem virtuellen VMware-Computer ausführen, benötigen Sie ausreichend Ressourcen auf dem vCenter Server, um einen virtuellen Computer zuzuordnen, der den Anforderungen entspricht.<br/><br/> Wenn Sie die Appliance auf einem physischen Computer ausführen, stellen Sie sicher, dass sie unter Windows Server 2016 läuft und die Hardwareanforderungen erfüllt.
**VMware-Anforderungen** | Wenn Sie die Appliance als virtuellen VMware-Computer bereitstellen, muss er auf einem ESXi-Host mit der Version 5.5 oder höher bereitgestellt werden.<br/><br/> vCenter Server mit Version 5.5, 6.0, 6.5 oder 6.7
**VDDK (Migration ohne Agent)** | Wenn Sie die Appliance als virtuellen VMware- Computer bereitstellen und eine Migration ohne Agent durchführen, muss das VMware vSphere VDDK auf dem virtuellen Computer der Appliance installiert sein.
**Hashwert-OVA** | [Überprüfen](tutorial-discover-vmware.md#verify-security) Sie die OVA-Vorlagenhashwerte.
**Hashwert-PowerShell-Skript** | [Überprüfen](deploy-appliance-script.md#verify-file-security) Sie die PowerShell-Skripthashwerte.




## <a name="appliance---hyper-v"></a>Appliance – Hyper-V

**Anforderung** | **Hyper-V** 
--- | ---
**Berechtigungen** | Für den lokalen oder Remotezugriff auf die Appliance-Web-App benötigen Sie ein lokales Benutzerkonto oder ein Domänenbenutzerkonto mit Administratorrechten auf dem Applianceserver.
**Appliancekomponenten** | Die Appliance verfügt über die folgenden Komponenten:<br/><br/>- **Verwaltungs-App**: Dies ist eine Web-App für Benutzereingaben während der Appliance-Bereitstellung. Wird beim Bewerten von Computern für die Migration zu Azure verwendet.<br/> - **Ermittlungs-Agent**: Der Agent erfasst Computerkonfigurationsdaten. Wird beim Bewerten von Computern für die Migration zu Azure verwendet.<br/>- **Collector-Agent**: Der Agent sammelt Leistungsdaten. Wird beim Bewerten von Computern für die Migration zu Azure verwendet.<br/>- **Dienst für automatische Aktualisierungen**: Aktualisiert Appliancekomponenten (wird alle 24 Stunden ausgeführt).
**Unterstützte Bereitstellung** | Bereitstellen als Hyper-V VM mithilfe einer VHD-Vorlage.<br/><br/> Bereitstellen als virtueller Hyper-V-Computer oder physischer Computer mithilfe des PowerShell-Installationsskripts.
**Projektunterstützung** |  Eine Appliance kann einem einzelnen Projekt zugeordnet werden. <br/> Einem einzelnen Projekt kann eine beliebige Anzahl von Appliances zugeordnet werden.<br/> 
**Ermittlungsgrenzwerte** | Eine Appliance kann bis zu 5000 virtuelle Hyper-V-Computer ermitteln.<br/> Eine einzelne Appliance kann Verbindungen mit bis zu 300 Hyper-V-Hosts herstellen.
**VHD-Vorlage** | Gezippte Ordner, einschließlich VHD. Laden Sie sie über das Portal oder [hier](https://go.microsoft.com/fwlink/?linkid=2140422) herunter.<br/><br/> Die Downloadgröße beträgt 8,91 GB.<br/><br/> Die heruntergeladene Appliancevorlage ist mit einer Windows Server 2016-Evaluierungslizenz ausgestattet, die 180 Tage lang gültig ist. Wenn der Evaluierungszeitraum fast abgelaufen ist, empfiehlt es sich, eine neue Appliance herunterzuladen und bereitzustellen oder die Betriebssystemlizenz der Appliance-VM zu aktivieren.
**PowerShell-Skript** | Weitere Informationen finden Sie in diesem [Artikel](./deploy-appliance-script.md#set-up-the-appliance-for-hyper-v).<br/><br/> 
**Software/Hardware** _   |  Die Appliance sollte auf einem Computer mit Windows Server 2016, 16 GB RAM, 8 vCPUs, etwa 80 GB Speicherplatz und einem externen virtuellen Switch ausgeführt werden.<br/> Die Appliance erfordert eine statische oder dynamische IP-Adresse sowie Zugriff auf das Internet, entweder direkt oder über einen Proxy.<br/><br/> Wenn Sie die Appliance als virtuellen Hyper-V-Computer ausführen, benötigen Sie genügend Ressourcen auf dem Hyper-V-Host zum Zuordnen der Hardwareanforderungen.<br/><br/> Wenn Sie die Appliance auf einem physischen Computer ausführen, stellen Sie sicher, dass sie unter Windows Server 2016 läuft und die Hardwareanforderungen erfüllt. 
_ *Hyper-V-Anforderungen** | Wenn Sie die Appliance mit der VHD-Vorlage bereitstellen, entspricht die von Azure Migrate bereitgestellte Appliance der Hyper-V VM Version 5.0.<br/><br/> Auf dem Hyper-V-Host muss mindestens Windows Server 2012 R2 ausgeführt werden. 
**Hashwert-VHD** | [Überprüfen](tutorial-discover-hyper-v.md#verify-security) Sie die Hashwerte der VHD-Vorlage.
**Hashwert-PowerShell-Skript** | [Überprüfen](deploy-appliance-script.md#verify-file-security) Sie die PowerShell-Skripthashwerte.


## <a name="appliance---physical"></a>Appliance – physisch

**Anforderung** | **Physisch** 
--- | ---
**Berechtigungen** | Für den lokalen oder Remotezugriff auf die Appliance-Web-App benötigen Sie ein lokales Benutzerkonto oder ein Domänenbenutzerkonto mit Administratorrechten auf dem Applianceserver.
**Appliancekomponenten** | Die Appliance verfügt über die folgenden Komponenten: <br/><br/> - **Verwaltungs-App**: Dies ist eine Web-App für Benutzereingaben während der Appliance-Bereitstellung. Wird beim Bewerten von Computern für die Migration zu Azure verwendet.<br/> - **Ermittlungs-Agent**: Der Agent erfasst Computerkonfigurationsdaten. Wird beim Bewerten von Computern für die Migration zu Azure verwendet.<br/>- **Collector-Agent**: Der Agent sammelt Leistungsdaten. Wird beim Bewerten von Computern für die Migration zu Azure verwendet.<br/>- **Dienst für automatische Aktualisierungen**: Aktualisiert Appliancekomponenten (wird alle 24 Stunden ausgeführt).
**Unterstützte Bereitstellung** | Bereitstellen als dedizierter physischer Computer oder als virtueller Computer mithilfe eines PowerShell-Installationsskripts. Das Skript steht im Portal als Download zur Verfügung.
**Projektunterstützung** |  Eine Appliance kann einem einzelnen Projekt zugeordnet werden. <br/> Einem einzelnen Projekt kann eine beliebige Anzahl von Appliances zugeordnet werden.<br/> 
**Ermittlungsgrenzwerte** | Eine Appliance kann bis zu 1.000 physische Server ermitteln.
**PowerShell-Skript** | Laden Sie das Skript (AzureMigrateInstaller.ps1) in einem gezippten Ordner aus dem Portal oder [hier](https://go.microsoft.com/fwlink/?linkid=2140334) herunter. [Weitere Informationen](tutorial-discover-physical.md)<br/><br/> Die Downloadgröße beträgt 85.8 MB.
**Software/Hardware** |  Die Appliance sollte auf einem Computer mit Windows Server 2016, 16 GB RAM, 8 vCPUs und etwa 80 GB Speicherplatz ausgeführt werden.<br/> Die Appliance erfordert eine statische oder dynamische IP-Adresse sowie Zugriff auf das Internet, entweder direkt oder über einen Proxy.<br/><br/> Wenn Sie die Appliance auf einem physischen Computer ausführen, stellen Sie sicher, dass sie unter Windows Server 2016 läuft und die Hardwareanforderungen erfüllt.<br/>_(Derzeit wird die Bereitstellung einer Appliance nur unter Windows Server 2016 unterstützt.)_
**Hashwert** | [Überprüfen](tutorial-discover-physical.md#verify-security) Sie die PowerShell-Skripthashwerte.

## <a name="url-access"></a>URL-Zugriff

Das Azure Migrate-Gerät muss mit dem Internet verbunden sein.

- Wenn Sie die Appliance bereitstellen, führt Azure Migrate eine Konnektivitätsprüfung für die erforderlichen URLs durch.
- Sie müssen den Zugriff auf alle URLs in der Liste zulassen. Wenn Sie nur eine Bewertung durchführen, können Sie die URLs überspringen, die für die VMware-Migration ohne Agent als erforderlich gekennzeichnet sind.
-  Wenn Sie einen URL-basierten Proxy für die Internetverbindung verwenden, stellen Sie sicher, dass der Proxy alle CNAME-Einträge auflöst, die beim Abrufen der URLs empfangen werden.

### <a name="public-cloud-urls"></a>URLs für die öffentliche Cloud

**URL** | **Details**  
--- | --- |
*.portal.azure.com  | Navigieren Sie zum Azure-Portal.
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com <br/> *.office.com | Melden Sie sich bei Ihrem Azure-Abonnement an.
*.microsoftonline.com <br/> *.microsoftonline-p.com | Erstellen von Azure Active Directory-Apps (AD) für die Kommunikation zwischen der Appliance und Azure Migrate.
management.azure.com | Erstellen von Azure AD-Apps, damit die Appliance mit dem Azure Migrate-Service kommunizieren kann.
*.services.visualstudio.com | Laden Sie App-Protokolle hoch, die für die interne Überwachung verwendet werden.
*.vault.azure.net | Verwalten von Geheimnissen in Azure Key Vault Hinweis: Stellen Sie sicher, dass die replizierten Computer darauf zugreifen können.
aka.ms/* | Zulassen des Zugriffs auf aka-Links Wird für Updates der Azure Migrate-Appliance verwendet.
download.microsoft.com/download | Zulassen von Downloads von Microsoft Download Center
*.servicebus.windows.net | Kommunikation zwischen der Appliance und dem Azure Migrate-Dienst
*.discoverysrv.windowsazure.com <br/> *.migration.windowsazure.com | Herstellen einer Verbindung mit Azure Migrate-Dienst-URLs
*.hypervrecoverymanager.windowsazure.com | **Wird für die VMware-Migration ohne Agent verwendet.**<br/><br/> Herstellen einer Verbindung mit Azure Migrate-Dienst-URLs
*.blob.core.windows.net |  **Wird für die VMware-Migration ohne Agent verwendet.**<br/><br/>Hochladen von Daten in Storage für die Migration

### <a name="government-cloud-urls"></a>URLs für Azure Government-Clouds

**URL** | **Details**  
--- | --- |
*.portal.azure.us  | Navigieren Sie zum Azure-Portal.
graph.windows.net | Melden Sie sich bei Ihrem Azure-Abonnement an.
login.microsoftonline.us  | Erstellen von Azure Active Directory-Apps (AD) für die Kommunikation zwischen der Appliance und Azure Migrate.
management.usgovcloudapi.net | Erstellen von Azure AD-Apps, damit die Appliance mit dem Azure Migrate-Service kommunizieren kann.
*.services.visualstudio.com | Laden Sie App-Protokolle hoch, die für die interne Überwachung verwendet werden.
*.vault.usgovcloudapi.net | Verwalten von Geheimnissen in Azure Key Vault
aka.ms/* | Zulassen des Zugriffs auf aka-Links Wird für Updates der Azure Migrate-Appliance verwendet.
download.microsoft.com/download | Zulassen von Downloads von Microsoft Download Center
*.servicebus.usgovcloudapi.net  | Kommunikation zwischen der Appliance und dem Azure Migrate-Dienst
*.discoverysrv.windowsazure.us <br/> *.migration.windowsazure.us | Herstellen einer Verbindung mit Azure Migrate-Dienst-URLs
*.hypervrecoverymanager.windowsazure.us | **Wird für die VMware-Migration ohne Agent verwendet.**<br/><br/> Herstellen einer Verbindung mit Azure Migrate-Dienst-URLs
*.blob.core.usgovcloudapi.net  |  **Wird für die VMware-Migration ohne Agent verwendet.**<br/><br/>Hochladen von Daten in Storage für die Migration
*.applicationinsights.us | Laden Sie App-Protokolle hoch, die für die interne Überwachung verwendet werden.





## <a name="collected-data---vmware"></a>Gesammelte Daten – VMware

Die Appliance sammelt Metadaten, Leistungsdaten und Abhängigkeitsanalysedaten (wenn die [Abhängigkeitsanalyse](concepts-dependency-visualization.md) ohne Agent verwendet wird).

### <a name="metadata"></a>Metadaten

Die von der Azure Migrate-Appliance ermittelten Metadaten helfen Ihnen bei der Ermittlung der Bereitschaft von Computern und Apps für die Migration zu Azure, der richtigen Größe für Computer und Apps, der Kosten der Planung und bei der Analyse von Anwendungsabhängigkeiten. Microsoft verwendet diese Daten nicht zur Überprüfung der Compliance von Lizenzen.

Nachstehend finden Sie die vollständige Liste der VMware-VM-Metadaten, die die Appliance sammelt und an Azure sendet.

**DATEN** | **ZÄHLER**
--- | --- 
**Computerdetails** | 
VM-ID | vm.Config.InstanceUuid 
Name des virtuellen Computers | vm.Config.Name
vCenter Server-ID | VMwareClient.Instance.Uuid
VM-Beschreibung | vm.Summary.Config.Annotation
Lizenzproduktname | vm.Client.ServiceContent.About.LicenseProductName
Betriebssystemtyp | vm.SummaryConfig.GuestFullName
Starttyp | vm.Config.Firmware
Anzahl von Kernen | vm.Config.Hardware.NumCPU
Arbeitsspeicher (MB) | vm.Config.Hardware.MemoryMB
Anzahl der Datenträger | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk).count
Liste der Datenträgergrößen | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualDisk)
Liste der Netzwerkadapter | vm.Config.Hardware.Device.ToList().FindAll(x => is VirtualEthernet).count
CPU-Auslastung | cpu.usage.average
Arbeitsspeichernutzung |mem.usage.average
**Details pro Datenträger** | 
Datenträgerschlüsselwert | disk.Key
Diskunit-Nummer | disk.UnitNumber
Datenträgercontroller-Schlüsselwert | disk.ControllerKey.Value
Bereitgestellte Gigabytes | virtualDisk.DeviceInfo.Summary
Name des Datenträgers | Mithilfe von disk.UnitNumber, disk.Key und disk.ControllerKey.Value generierter Wert
Lesevorgänge pro Sekunde | virtualDisk.numberReadAveraged.average
Schreibvorgänge pro Sekunde | virtualDisk.numberWriteAveraged.average
Lesedurchsatz (MB pro Sekunde) | virtualDisk.read.average
Schreibdurchsatz (MB pro Sekunde) | virtualDisk.write.average
**Details pro NIC** | 
Netzwerkadaptername | nic.Key
MAC-Adresse | ((VirtualEthernetCard)nic).MacAddress
IPv4-Adressen | vm.Guest.Net
IPv6-Adressen | vm.Guest.Net
Lesedurchsatz (MB pro Sekunde) | net.received.average
Schreibdurchsatz (MB pro Sekunde) | net.transmitted.average
**Inventurpfaddetails** | 
Name | container.GetType().Name
Typ des untergeordneten Objekts | container.ChildType
Referenzdetails | container.MoRef
Details des übergeordneten Objekts | Container.Parent
Ordnerdetails pro VM | ((Folder)container).ChildEntity.Type
Datencenterdetails pro VM | ((Datacenter)container).VmFolder
Details zum Rechenzentrum pro Hostordner | ((Datacenter)container).HostFolder
Clusterdetails pro Host | ((ClusterComputeResource)container).Host
Hostdetails pro VM | ((HostSystem)container).VM

### <a name="performance-data"></a>Leistungsdaten


Nachstehend finden Sie die VMware-VM-Leistungsdaten, die die Appliance sammelt und an Azure sendet.

**Daten** | **Leistungsindikator** | **Auswirkungen auf die Bewertung**
--- | --- | ---
CPU-Auslastung | cpu.usage.average | Empfohlene VM-Größe/Kosten
Arbeitsspeichernutzung | mem.usage.average | Empfohlene VM-Größe/Kosten
Datenträgerlesedurchsatz (MB pro Sekunde) | virtualDisk.read.average | Berechnung von Datenträgergröße, Speicherkosten und VM-Größe
Datenträgerschreibdurchsatz (MB pro Sekunde) | virtualDisk.write.average | Berechnung von Datenträgergröße, Speicherkosten und VM-Größe
Datenträgerlesevorgänge pro Sekunde | virtualDisk.numberReadAveraged.average | Berechnung von Datenträgergröße, Speicherkosten und VM-Größe
Datenträgerschreibvorgänge pro Sekunde | virtualDisk.numberWriteAveraged.average  | Berechnung von Datenträgergröße, Speicherkosten und VM-Größe
NIC-Lesedurchsatz (MB pro Sekunde) | net.received.average | Berechnung der VM-Größe
NIC-Schreibdurchsatz (MB pro Sekunde) | net.transmitted.average  |Berechnung der VM-Größe


### <a name="installed-apps-metadata"></a>Metadaten installierter Apps

Bei der Anwendungsermittlung werden installierte Anwendungen und Betriebssystemdaten erfasst.

#### <a name="windows-vm-apps-data"></a>Windows-VM-App-Daten

Dies sind die Daten der installierten Anwendung, die die Appliance von jedem virtuellen Computer sammelt, der für die Anwendungsermittlung aktiviert ist. Diese Daten werden an Azure gesendet.

**Daten** | **Registrierungsstandort** | **Schlüssel**
--- | --- | ---
Anwendungsname  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayName
Version  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | DisplayVersion 
Anbieter  | HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\*  <br/> HKLM:\Software\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\*  | Herausgeber

#### <a name="windows-vm-features-data"></a>Daten zu Windows-VM-Features

Dies sind die Daten der Features, die die Appliance von jedem virtuellen Computer sammelt, der für die Anwendungsermittlung aktiviert ist. Diese Daten werden an Azure gesendet.

**Daten**  | **PowerShell-Cmdlet** | **Eigenschaft**
--- | --- | ---
Name  | Get-WindowsFeature  | Name
Featuretyp | Get-WindowsFeature  | FeatureType
Parent  | Get-WindowsFeature  | Parent

#### <a name="windows-vm-sql-server-metadata"></a>Windows-VM-SQL Server-Metadaten

Dies sind die SQL Server-Metadaten, die von der Appliance von virtuellen Computern gesammelt werden, auf denen Microsoft SQL Server für die Anwendungsermittlung aktiviert ist. Diese Daten werden an Azure gesendet.

**Daten**  | **Registrierungsstandort**  | **Schlüssel**
--- | --- | ---
Name  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\Instance Names\SQL  | installedInstance
Edition  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\\\<InstanceName>\Setup  | Edition 
Service Pack  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\\\<InstanceName>\Setup  | SP
Version  | HKLM:\SOFTWARE\Microsoft\Microsoft SQL Server\\\<InstanceName>\Setup  | Version 

#### <a name="windows-vm-operating-system-data"></a>Windows-VM-Betriebssystemdaten

Dies sind die Betriebssystemdaten, die die Appliance von jedem virtuellen Computer sammelt, der für die Anwendungsermittlung aktiviert ist. Diese Daten werden an Azure gesendet.

Daten  | WMI-Klasse  | WMI-Klasseneigenschaft
--- | --- | ---
Name  | Win32_operatingsystem  | Caption
Version  | Win32_operatingsystem  | Version
Aufbau  | Win32_operatingsystem  | OSArchitecture

#### <a name="linux-vm-apps-data"></a>Linux-VM-App-Daten

Dies sind die Daten der installierten Anwendung, die die Appliance von jedem virtuellen Computer sammelt, der für die Anwendungsermittlung aktiviert ist. Basierend auf dem Betriebssystem der VM werden einer oder mehrere der Befehle ausgeführt. Diese Daten werden an Azure gesendet.

Daten  | Get-Help
--- | --- 
Name | rpm, dpkg-query, snap
Version | rpm, dpkg-query, snap
Anbieter | rpm, dpkg-query, snap

#### <a name="linux-vm-operating-system-data"></a>Linux-VM-Betriebssystemdaten

Dies sind die Betriebssystemdaten, die die Appliance von jedem virtuellen Computer sammelt, der für die Anwendungsermittlung aktiviert ist. Diese Daten werden an Azure gesendet.

**Daten**  | **Befehl** 
--- | --- | ---
Name <br/> version | Erfasst aus mindestens einer der folgenden Dateien:<br/> <br/>/etc/os-release  <br> /usr/lib/os-release  <br> /etc/enterprise-release  <br> /etc/redhat-release  <br> /etc/oracle-release  <br> /etc/SuSE-release  <br> /etc/lsb-release  <br> /etc/debian_version 
Aufbau | uname


### <a name="app-dependencies-metadata"></a>Metadaten zu App-Abhängigkeiten

Die Abhängigkeitsanalyse ohne Agent sammelt Verbindungs- und Prozessdaten.

#### <a name="windows-vm-app-dependencies-data"></a>Windows-VM-App-Abhängigkeitendaten

Hier sind die Verbindungsdaten, die die Appliance von jedem virtuellen Computer sammelt, der für eine Abhängigkeitsanalyse ohne Agent aktiviert ist. Diese Daten werden an Azure gesendet.

**Daten** | **Verwendeter Befehl** 
--- | --- 
Lokaler Port | netstat
Lokale IP-Adresse | netstat
Remoteport | netstat
Remote-IP-Adresse | netstat
TCP-Verbindungsstatus | netstat
Prozess-ID | netstat
Anzahl der aktiven Verbindungen | netstat


Hier sind die Prozessdaten, die die Appliance von jedem virtuellen Computer sammelt, der für eine Abhängigkeitsanalyse ohne Agent aktiviert ist. Diese Daten werden an Azure gesendet.

**Daten** | **WMI-Klasse** | **WMI-Klasseneigenschaft**
--- | --- | ---
Prozessname | Win32_Process | ExecutablePath
Prozessargumente | Win32_Process | CommandLine
Anwendungsname | Win32_Process | Parameter „VersionInfo.ProductName“ der ExecutablePath-Eigenschaft

#### <a name="linux-vm-app-dependencies-data"></a>Linux-VM-App-Abhängigkeitendaten

Hier sind die Verbindungs- und Prozessdaten, die die Appliance von jedem virtuellen Linux-Computer sammelt, der für eine Abhängigkeitsanalyse ohne Agent aktiviert ist. Diese Daten werden an Azure gesendet.

**Daten** | **Verwendeter Befehl** 
--- | ---
Lokaler Port | netstat 
Lokale IP-Adresse | netstat 
Remoteport | netstat 
Remote-IP-Adresse | netstat 
TCP-Verbindungsstatus | netstat 
Anzahl der aktiven Verbindungen | netstat
Prozess-ID  | netstat 
Prozessname | ps
Prozessargumente | ps
Anwendungsname | dpkg oder rpm



## <a name="collected-data---hyper-v"></a>Gesammelte Daten – Hyper-V

Die Appliance sammelt Metadaten, Leistungsdaten und Abhängigkeitsanalysedaten (wenn die [Abhängigkeitsanalyse](concepts-dependency-visualization.md) ohne Agent verwendet wird).

### <a name="metadata"></a>Metadaten
Die von der Azure Migrate-Appliance ermittelten Metadaten helfen Ihnen bei der Ermittlung der Bereitschaft von Computern und Apps für die Migration zu Azure, der richtigen Größe für Computer und Apps, der Kosten der Planung und bei der Analyse von Anwendungsabhängigkeiten. Microsoft verwendet diese Daten nicht zur Überprüfung der Compliance von Lizenzen.

Nachstehend finden Sie die vollständige Liste der Hyper-V-VM-Metadaten, die die Appliance sammelt und an Azure sendet.

**DATEN** | **WMI-KLASSE** | **WMI-KLASSENEIGENSCHAFT**
--- | --- | ---
**Computerdetails** | 
Seriennummer von BIOS _ Msvm_BIOSElement | BIOSSerialNumber
VM-Typ (Gen 1 oder 2) | Msvm_VirtualSystemSettingData | VirtualSystemSubType
Anzeigename der VM | Msvm_VirtualSystemSettingData | ElementName
VM-Version | Msvm_ProcessorSettingData | VirtualQuantity
Arbeitsspeicher (Bytes) | Msvm_MemorySettingData | VirtualQuantity
Maximaler Arbeitsspeicher, der von der VM genutzt werden kann | Msvm_MemorySettingData | Begrenzung
Dynamischer Arbeitsspeicher aktiviert | Msvm_MemorySettingData | DynamicMemoryEnabled
Name/Version/FQDN des Betriebssystems | Msvm_KvpExchangeComponent | GuestIntrinsicExchangeItems-Namensdaten
Betriebszustand der VM | Msvm_ComputerSystem | EnabledState
**Details pro Datenträger** | 
Datenträgeridentifikator | Msvm_VirtualHardDiskSettingData | VirtualDiskId
Virtueller Datenträgertyp | Msvm_VirtualHardDiskSettingData | type
Virtuelle Datenträgergröße | Msvm_VirtualHardDiskSettingData | MaxInternalSize
Virtueller übergeordneter Datenträger | Msvm_VirtualHardDiskSettingData | ParentPath
**Details pro NIC** | 
IP-Adressen (synthetische NICs) | Msvm_GuestNetworkAdapterConfiguration | IPAddresses
DHCP aktiviert (synthetische NICs) | Msvm_GuestNetworkAdapterConfiguration | DHCPEnabled
NIC-ID (synthetische NICs) | Msvm_SyntheticEthernetPortSettingData | InstanceID
NIC-MAC-Adresse (synthetische NICs) | Msvm_SyntheticEthernetPortSettingData | Adresse
NIC-ID (Legacy-NICs) | MsvmEmulatedEthernetPortSetting-Daten | InstanceID
NIC-MAC-ID (Legacy-NICs) | MsvmEmulatedEthernetPortSetting-Daten | Adresse

### <a name="performance-data"></a>Leistungsdaten

Nachstehend finden Sie die Hyper-VM-Leistungsdaten, die die Appliance sammelt und an Azure sendet.

**Leistungsindikatorklasse** | **Leistungsindikator** | **Auswirkungen auf die Bewertung**
--- | --- | ---
Virtueller Hyper-V-Hypervisor-Prozessor | % Gastausführungszeit | Empfohlene VM-Größe/Kosten
Hyper-V-VM mit dynamischem Arbeitsspeicher | Aktueller Druck (%)<br/> Sichtbarer physischer Speicher des Gastcomputers (MB) | Empfohlene VM-Größe/Kosten
Virtuelles Hyper-V-Speichergerät | Lesebytes/Sekunde | Berechnung von Datenträgergröße, Speicherkosten und VM-Größe
Virtuelles Hyper-V-Speichergerät | Schreibbytes/Sekunde | Berechnung von Datenträgergröße, Speicherkosten und VM-Größe
Virtueller Hyper-V-Netzwerkadapter | Empfangene Bytes/Sekunde | Berechnung der VM-Größe
Virtueller Hyper-V-Netzwerkadapter | Gesendete Bytes/Sekunde | Berechnung der VM-Größe

- Die CPU-Auslastung ist die Summe aller Auslastungen für alle an einen virtuellen Computer angeschlossenen virtuellen Prozessoren.
- Die Speichernutzung ist (Aktueller Druck * Sichtbarer physischer Speicher des Gastcomputers) / 100.
- Die Werte für die Festplatten- und Netzwerkauslastung werden von den aufgeführten Hyper-V-Leistungsindikatoren gesammelt.


## <a name="collected-data---physical"></a>Gesammelte Daten – physisch

Die Appliance sammelt Metadaten, Leistungsdaten und Abhängigkeitsanalysedaten (wenn die [Abhängigkeitsanalyse](concepts-dependency-visualization.md) ohne Agent verwendet wird).

### <a name="windows-metadata"></a>Windows-Metadaten

Die von der Azure Migrate-Appliance ermittelten Metadaten helfen Ihnen bei der Ermittlung der Bereitschaft von Computern und Apps für die Migration zu Azure, der richtigen Größe für Computer und Apps, der Kosten der Planung und bei der Analyse von Anwendungsabhängigkeiten. Microsoft verwendet diese Daten nicht zur Überprüfung der Compliance von Lizenzen.

Nachstehend finden Sie die vollständige Liste der Windows-Servermetadaten, die die Appliance sammelt und an Azure sendet.

**DATEN** | **WMI-KLASSE** | **WMI-KLASSENEIGENSCHAFT**
--- | --- | ---
FQDN | Win32_ComputerSystem | Domain, Name, PartOfDomain
Anzahl der Prozessorkerne | Win32_PRocessor | NumberOfCores
Zugeordneter Arbeitsspeicher | Win32_ComputerSystem | TotalPhysicalMemory
Seriennummer des BIOS | Win32_ComputerSystemProduct | IdentifyingNumber
BIOS-GUID | Win32_ComputerSystemProduct | UUID
Starttyp | Win32_DiskPartition | Überprüfen auf Partition mit Type = **GPT:System** für EFI/BIOS
Betriebssystemname | Win32_OperatingSystem | Caption
Betriebssystemversion |Win32_OperatingSystem | Version
Betriebssystemarchitektur | Win32_OperatingSystem | OSArchitecture
Datenträgeranzahl | Win32_DiskDrive | Model, Size, DeviceID, MediaType, Name
Datenträgergröße | Win32_DiskDrive | Size
NIC-Liste | Win32_NetworkAdapterConfiguration | Description, Index
NIC-IP-Adresse | Win32_NetworkAdapterConfiguration | IPAddress
NIC-MAC-Adresse | Win32_NetworkAdapterConfiguration | MACAddress

### <a name="linux-metadata"></a>Linux-Metadaten

Nachstehend finden Sie die vollständige Liste der Linux-Servermetadaten, die die Appliance sammelt und an Azure sendet.

**DATEN** | **LINUX** 
--- | --- 
FQDN | cat /proc/sys/kernel/hostname, hostname -f
Anzahl der Prozessorkerne |  /proc/cpuinfo \| awk '/^processor/{print $3}' \| wc -l
Zugeordneter Arbeitsspeicher | cat /proc/meminfo \| grep MemTotal \| awk '{printf "%.0f", $2/1024}'
Seriennummer des BIOS | lshw \| grep "serial:" \| head -n1 \| awk '{print $2}' <br/> /usr/sbin/dmidecode -t 1 \| grep 'Serial' \| awk '{ $1="" ; $2=""; print}'
BIOS-GUID | cat /sys/class/dmi/id/product_uuid
Starttyp | [ -d /sys/firmware/efi ] && echo EFI \|\| echo BIOS
Betriebssystemname/-version | Für die Version und den Namen des Betriebssystems wird auf folgende Dateien zugegriffen:<br/><br/> /etc/os-release<br/> /usr/lib/os-release <br/> /etc/enterprise-release <br/> /etc/redhat-release<br/> /etc/oracle-release<br/>  /etc/SuSE-release<br/>  /etc/lsb-release  <br/> /etc/debian_version
Betriebssystemarchitektur | Uname -m
Datenträgeranzahl | fdisk -l \| egrep 'Disk.*bytes' \| awk '{print $2}' \| cut -f1 -d ':'
Startdatenträger | df /boot \| sed -n 2p \| awk '{print $1}'
Datenträgergröße | fdisk -l \| egrep 'Disk.*bytes' \| egrep $disk: \| awk '{print $5}'
NIC-Liste | ip -o -4 addr show \| awk '{print $2}'
NIC-IP-Adresse | ip addr show $nic \| grep inet \| awk '{print $2}' \| cut -f1 -d "/" 
NIC-MAC-Adresse | ip addr show $nic \| grep ether  \| awk '{print $2}'

### <a name="windows-performance-data"></a>Windows-Leistungsdaten

Nachstehend finden Sie die Windows-Serverleistungsdaten, die die Appliance sammelt und an Azure sendet.

**Daten** | **WMI-Klasse** | **WMI-Klasseneigenschaft**
--- | --- | ---
CPU-Auslastung | Win32_PerfFormattedData_PerfOS_Processor | PercentIdleTime
Speicherauslastung | Win32_PerfFormattedData_PerfOS_Memory | AvailableMBytes
NIC-Anzahl | Win32_PerfFormattedData_Tcpip_NetworkInterface | Ruft die Anzahl der Netzwerkgeräte ab.
Pro NIC empfangene Daten | Win32_PerfFormattedData_Tcpip_NetworkInterface  | BytesReceivedPerSec
Pro NIC übertragene Daten | BWin32_PerfFormattedData_Tcpip_NetworkInterface | BytesSentPersec
Datenträgeranzahl | BWin32_PerfFormattedData_PerfDisk_PhysicalDisk | Anzahl der Datenträger
Datenträgerdetails | Win32_PerfFormattedData_PerfDisk_PhysicalDisk | DiskWritesPerSec, DiskWriteBytesPerSec, DiskReadsPerSec, DiskReadBytesPerSec.

### <a name="linux-performance-data"></a>Linux-Leistungsdaten

Nachstehend finden Sie die Linux-Serverleistungsdaten, die die Appliance sammelt und an Azure sendet.

**Daten** | **Linux** 
--- | --- 
CPU-Auslastung | cat /proc/stat/| grep 'cpu' /proc/stat
Speicherauslastung | free \| grep Mem \| awk '{print $3/$2 * 100.0}'
NIC-Anzahl | lshw -class network \| grep eth[0-60] \| wc -l
Pro NIC empfangene Daten | cat /sys/class/net/eth$nic/statistics/rx_bytes
Pro NIC übertragene Daten | cat /sys/class/net/eth$nic/statistics/tx_bytes
Datenträgeranzahl | fdisk -l \| egrep 'Disk.*bytes' \| awk '{print $2}' \| cut -f1 -d ':'
Datenträgerdetails | cat /proc/diskstats


## <a name="appliance-upgrades"></a>Appliance-Upgrades

Die Appliance wird aktualisiert, wenn die auf der Appliance ausgeführten Azure Migrate-Agents aktualisiert werden. Dies geschieht automatisch, da auf der Appliance standardmäßig die automatische Aktualisierung aktiviert ist. Sie können diese Standardeinstellung ändern, um die Appliancedienste manuell zu aktualisieren.

### <a name="turn-off-auto-update"></a>Deaktivieren der automatischen Aktualisierung

1. Öffnen Sie auf dem Computer, auf dem die Appliance ausgeführt wird, den Registrierungs-Editor.
2. Navigieren Sie zu **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Zum Deaktivieren der automatischen Aktualisierung erstellen Sie einen Registrierungsschlüssel **AutoUpdate** mit dem DWORD-Wert 0.

    ![Festlegen des Registrierungsschlüssels](./media/migrate-appliance/registry-key.png)


### <a name="turn-on-auto-update"></a>Aktivieren der automatischen Aktualisierung

Sie können die automatische Aktualisierung über eine der folgenden Methoden aktivieren:

- Löschen des AutoUpdate-Registrierungsschlüssels unter „HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance“.
- Klicken Sie in den Überprüfungen auf aktuelle Updates im Bereich **Erforderliche Komponenten einrichten** auf **Appliancedienste anzeigen**, um die automatische Aktualisierung zu aktivieren.

So löschen Sie den Registrierungsschlüssel:

1. Öffnen Sie auf dem Computer, auf dem die Appliance ausgeführt wird, den Registrierungs-Editor.
2. Navigieren Sie zu **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureAppliance**.
3. Löschen Sie den Registrierungsschlüssel **AutoUpdate**, der zuvor zum Deaktivieren der automatischen Aktualisierung erstellt wurde.

Aktivierung im Appliance-Konfigurations-Manager nach Abschluss der Ermittlung:

1. Wechseln Sie im Appliance-Konfigurations-Manager zum Bereich **Erforderliche Komponenten einrichten**.
2. Klicken Sie in der Überprüfung auf aktuelle Updates auf **Appliancedienste anzeigen** und dann auf den Link zum Aktivieren der automatischen Aktualisierung.

    ![Aktivieren der automatischen Aktualisierung](./media/migrate-appliance/autoupdate-off.png)

### <a name="check-the-appliance-services-version"></a>Überprüfen der Appliancediensteversion

Sie können die Version der Appliancedienste über eine der folgenden Methoden überprüfen:

- Wechseln Sie im Appliance-Konfigurations-Manager zum Bereich **Erforderliche Komponenten einrichten**.
- Auf dem Computer mit der Appliance unter **Systemsteuerung** > **Programme und Features**.

So überprüfen Sie die Version im Appliance-Konfigurations-Manager:

1. Wechseln Sie im Appliance-Konfigurations-Manager zum Bereich **Erforderliche Komponenten einrichten**.
2. Klicken Sie in der Überprüfung auf aktuelle Updates auf **Appliancedienste anzeigen**.

    ![Überprüfen der Version](./media/migrate-appliance/versions.png)

So überprüfen Sie die Version in der Systemsteuerung:

1. Klicken Sie in der Appliance auf **Start** > **Systemsteuerung** > **Programme und Features**
2. Überprüfen Sie die Version der Appliancedienste in der Liste.

    ![Überprüfen der Version in der Systemsteuerung](./media/migrate-appliance/programs-features.png)

### <a name="manually-update-an-older-version"></a>Manuelle Aktualisierung einer älteren Version

Wenn bei einer oder mehreren Komponenten eine ältere Version vorhanden ist, müssen Sie den Dienst deinstallieren und eine manuelle Aktualisierung auf die aktuelle Version durchführen.

1. In diesem [Download](https://aka.ms/latestapplianceservices) der LatestComponents.json-Datei finden Sie die jeweils aktuelle Version der Appliancedienste.
2.    Öffnen Sie die Datei „LatestComponents.json“ nach dem Herunterladen im Editor.
3. Suchen Sie in der Datei nach der aktuellen Dienstversion und dem zugehörigen Downloadlink. Beispiel:

    "Name": "ASRMigrationWebApp", "DownloadLink": "https://download.microsoft.com/download/f/3/4/f34b2eb9-cc8d-4978-9ffb-17321ad9b7ed/MicrosoftAzureApplianceConfigurationManager.msi", "Version": "6.0.211.2", "Md5Hash": "e00a742acc35e78a64a6a81e75469b84"

4.    Laden Sie die aktuelle Version eines überholten Diensts über den Downloadlink in der Datei herunter.
5. Führen Sie nach dem Herunterladen den folgenden Befehl in einem Befehlsfenster für Administratoren aus, um die Integrität der heruntergeladenen MSI-Datei zu überprüfen.

    Beispiel: ``` C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm] ```  C:\>CertUtil -HashFile C:\Users\public\downloads\MicrosoftAzureApplianceConfigurationManager.MSI MD5

5. Überprüfen Sie, ob die Befehlsausgabe dem Hashwerteintrag für den Dienst in der Datei (z. B. der oben stehende Hashwert MD5) entspricht.
6. Führen Sie dann die MSI-Datei aus, um den Dienst zu installieren. Es handelt sich um eine unbeaufsichtigte Installation, und das Installationsfenster wird geschlossen, sobald der Vorgang beendet ist.
7. Überprüfen Sie nach Abschluss der Installation die Version des Diensts unter **Systemsteuerung** > **Programme und Features**. Die Dienstversion sollte auf die aktuelle Version aus der JSON-Datei aktualisiert worden sein.



## <a name="next-steps"></a>Nächste Schritte

- [Erfahren Sie, wie](how-to-set-up-appliance-vmware.md) Sie die Appliance für VMware einrichten.
- [Erfahren Sie, wie](how-to-set-up-appliance-hyper-v.md) Sie die Appliance für Hyper-V einrichten.
- [Erfahren Sie, wie](how-to-set-up-appliance-physical.md) Sie die Appliance für physische Server einrichten.

