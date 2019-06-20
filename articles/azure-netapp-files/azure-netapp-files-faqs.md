---
title: Häufig gestellte Fragen zu Azure NetApp Files | Microsoft-Dokumentation
description: Hier erhalten Sie Antworten auf häufig gestellte Fragen zu Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: b-juche
ms.openlocfilehash: 6f1ca3398678b59a81e5c22b51b36a1f5505d4c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65804864"
---
# <a name="faqs-about-azure-netapp-files"></a>Häufig gestellte Fragen zu Azure NetApp Files

In diesem Artikel werden häufig gestellte Fragen zu Azure NetApp Files beantwortet. 

## <a name="networking-faqs"></a>Häufig gestellte Fragen zum Netzwerk

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>Wird der NFS-Datenpfad über das Internet geleitet?  

Nein. Der NFS-Datenpfad wird nicht über das Internet geleitet. Azure NetApp Files ist ein nativer Azure-Dienst, der in dem Azure Virtual Network (VNET) bereitgestellt wird, in dem der Dienst verfügbar ist. Azure NetApp Files verwendet ein delegiertes Subnetz und stellt eine Netzwerkschnittstelle direkt im VNET bereit. 

Ausführliche Informationen dazu finden Sie unter [Richtlinien für die Azure NetApp Files-Netzwerkplanung](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies).  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Kann ich eine Verbindung zu einem VNET herstellen, das ich für den Azure NetApp Files-Dienst bereits erstellt habe?

Ja, Sie können VNETs verbinden, die Sie für den Dienst erstellt haben. 

Ausführliche Informationen dazu finden Sie unter [Richtlinien für die Azure NetApp Files-Netzwerkplanung](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-network-topologies).  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Kann ich ein NFS-Volume von Azure NetApp Files anhand des vollqualifizierten DNS-Domänennamens einbinden?

Ja, wenn Sie die erforderlichen DNS-Einträge erstellen. Azure NetApp Files liefert die Dienst-IP-Adresse für das bereitgestellte Volume. 

> [!NOTE] 
> Azure NetApp Files kann bei Bedarf weitere IP-Adressen für den Dienst bereitstellen.  DNS-Einträge müssen möglicherweise in regelmäßigen Abständen aktualisiert werden.

## <a name="security-faqs"></a>Häufig gestellte Fragen zur Sicherheit

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Kann der Netzwerkdatenverkehr zwischen Azure-VM und Speicher verschlüsselt werden?

Datenverkehr (vom NFSv3- oder SMBv3-Client zu Azure NetApp Files-Volumes) ist nicht verschlüsselt. Der Datenverkehr von einer Azure-VM (auf der ein NFS- oder SMB-Client ausgeführt wird) zu Azure NetApp Files ist jedoch so sicher wie jeder andere Datenverkehr zwischen Azure-VMs. Dieser Datenverkehr wird lokal im Azure-Rechenzentrumsnetzwerk ausgeführt. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>Kann der Speicher im Ruhezustand verschlüsselt werden?

Alle Azure NetApp Files-Volumes werden mit dem FIPS 140-2-Standard verschlüsselt. Alle Schlüssel werden vom Azure NetApp Files-Dienst verwaltet. 

### <a name="how-are-encryption-keys-managed"></a>Wie werden Verschlüsselungsschlüssel verwaltet? 

Die Schlüsselverwaltung für Azure NetApp Files wird vom Dienst verarbeitet.  Vom Benutzer verwaltete Schlüssel (Bring Your Own Key, BYOK) werden derzeit nicht unterstützt.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Kann ich Regeln für NFS-Exportrichtlinien konfigurieren, um den Zugriff auf das Azure NetApp Files-Diensteinbindungsziel zu steuern?


Ja, Sie können bis zu fünf Regeln in einer einzigen NFS-Exportrichtlinie konfigurieren.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Unterstützt Azure NetApp Files Netzwerksicherheitsgruppen?

Nein, Sie können derzeit keine Netzwerksicherheitsgruppen auf das delegierte Azure NetApp Files-Subnetz oder die vom Dienst erstellten Netzwerkschnittstellen anwenden.

### <a name="can-i-use-azure-iam-with-azure-netapp-files"></a>Kann ich Azure IAM mit Azure NetApp Files verwenden?

Ja, Azure NetApp Files unterstützt RBAC-Features mit Azure IAM (Identity & Access Management).

## <a name="performance-faqs"></a>Häufig gestellte Fragen zur Leistung

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Was kann ich tun, um die Leistung von Azure NetApp Files zu optimieren?

Je nach Leistungsanforderungen können Sie folgende Aktionen ausführen: 
- Stellen Sie sicher, dass die VM eine geeignete Größe aufweist.
- Aktivieren Sie den beschleunigten Netzwerkbetrieb für die VM.
- Wählen Sie die gewünschte Dienstebene und -größe für den Kapazitätspool.
- Erstellen Sie ein Volume mit der gewünschten Kontingentgröße für Kapazität und Leistung.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Wie konvertiere ich durchsatzbasierte Dienstebenen von Azure NetApp Files zu IOPS?

Sie können MB/s mit der folgenden Formel zu IOPS konvertieren:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Wie ändere ich die Dienstebene eines Volumes?

Das Ändern der Dienstebene eines Volumes wird derzeit nicht unterstützt.

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Wie überwache ich die Leistung von Azure NetApp Files?

Azure NetApp Files bietet Metriken zum Messen der Volumeleistung. Sie können auch Azure Monitor verwenden, um die Nutzungsmetriken für Azure NetApp Files zu überwachen.  Eine Liste der Leistungsmetriken für Azure NetApp Files finden Sie unter [Metriken für Azure NetApp Files](azure-netapp-files-metrics.md).

## <a name="nfs-faqs"></a>Häufig gestellte Fragen zu NFS

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Ich möchte, dass automatisch ein Volume eingebunden wird, wenn eine Azure-VM gestartet oder neu gestartet wird.  Wie konfiguriere ich meinen Host für persistente NFS-Volumes?

Damit beim Starten oder Neustarten einer VM automatisch ein NFS-Volume eingebunden wird, fügen Sie einen Eintrag für die Datei `/etc/fstab` zum Host hinzu. 

Beispiel: `$ANFIP:/$FILEPATH      /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

- $ANFIP  
    Die IP-Adresse des Azure NetApp Files-Volumes, die Sie auf dem Eigenschaftenblatt für das Volume finden
- $FILEPATH  
    Der Exportpfad zum Azure NetApp Files-Volume
- $MOUNTPOINT  
    Das auf dem Linux-Host erstellte Verzeichnis, das zum Einbinden des NFS-Exports verwendet wird

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Warum zeigt der DF-Befehl auf dem NFS-Client die Größe des bereitgestellten Volumes nicht an?

Die in DF gemeldete Volumegröße ist die maximale Größe, auf die das Azure NetApp Files-Volume vergrößert werden kann. Die Größe des Azure NetApp Files-Volumes im DF-Befehl spiegelt nicht das Kontingent oder die Größe des Volumes wieder.  Sie können die Größe und das Kontingent des Azure NetApp Files-Volumes über das Azure-Portal oder die API abrufen.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Welche NFS-Version wird von Azure NetApp Files unterstützt?

Azure NetApp Files unterstützt derzeit NFSv3.

### <a name="how-do-i-enable-root-squashing"></a>Wie aktiviere ich das Root Squashing?

Das Root Squashing wird derzeit nicht unterstützt.

## <a name="smb-faqs"></a>Häufig gestellte Fragen zu SMB

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Unterstützt Azure NetApp Files Azure Active Directory?

Nein, dies wird derzeit nicht unterstützt.  Azure NetApp Files unterstützt Active Directory Domain Services (Bring Your Own AD), wobei vorhandene Active Directory-Domänencontroller mit Azure NetApp Files verwendet werden können. Domänencontroller können als virtuelle Computer in Azure oder als lokale Computer mit ExpressRoute-Verbindung existieren.

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>Ist für den SMB-Zugriff eine Active Directory-Verbindung erforderlich? 

Ja, Sie müssen eine Active Directory-Verbindung erstellen, bevor Sie ein SMB-Volume bereitstellen. Damit erfolgreich eine Verbindung hergestellt werden kann, muss das delegierte Subnetz von Azure NetApp Files auf die angegebenen Domänencontroller zugreifen können.  Ausführliche Informationen dazu finden Sie unter [Erstellen eines SMB-Volumes](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes#create-an-smb-volume). 

### <a name="how-many-active-directory-connections-are-supported"></a>Wie viele Active Directory-Verbindungen werden unterstützt?

Azure NetApp Files unterstützt derzeit eine Active Directory-Verbindung pro Abonnement. Darüber hinaus gilt die Active Directory-Verbindung spezifisch für ein einzelnes NetApp-Konto, sie wird nicht von mehreren Konten gemeinsam genutzt. 

### <a name="what-versions-of-windows-ad-are-supported"></a>Welche Versionen von Windows AD werden unterstützt?

Azure NetApp Files unterstützt die Windows Server 2008r2SP1-2016-Version von Active Directory Domain Services.

## <a name="capacity-management-faqs"></a>Häufig gestellte Fragen zur Kapazitätsverwaltung

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Wie überwache ich die Nutzung für den Kapazitätspool und das Volume von Azure NetApp Files? 

Azure NetApp Files stellt Nutzungsmetriken für Kapazitätspool und Volume bereit. Sie können auch Azure Monitor verwenden, um die Nutzung für Azure NetApp Files zu überwachen. Ausführliche Informationen finden Sie unter [Metriken für Azure NetApp Files](azure-netapp-files-metrics.md). 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Kann ich Azure NetApp Files über den Azure Storage-Explorer verwalten?

Nein. Azure NetApp Files wird vom Azure Storage-Explorer nicht unterstützt.

## <a name="data-migration-and-protection-faqs"></a>Häufig gestellte Fragen zu Datenmigration und -schutz

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Wie migriere ich Daten zu Azure NetApp Files?
Azure NetApp Files stellt NFS- und SMB-Volumes bereit.  Sie können jedes dateibasierte Kopiertool zum Migrieren von Daten zum Dienst verwenden. 

NetApp bietet eine SaaS-basierte Lösung: [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service).  Die Lösung ermöglicht es Ihnen, NFS- oder SMB-Daten in Azure NetApp Files-NFS-Exporte oder SMB-Dateifreigaben zu replizieren. 

Sie können auch eine Vielzahl kostenloser Tools zum Kopieren von Daten verwenden. Bei NFS können Sie Workloadtools wie [rsync](https://rsync.samba.org/examples.html) verwenden, um Quelldaten in ein Azure NetApp Files-Volume zu kopieren und zu synchronisieren. Bei SMB können Sie das Workloadtool [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) auf die gleiche Weise verwenden.  Diese Tools können auch Datei- oder Ordnerberechtigungen replizieren. 

Für die Datenmigration aus einer lokalen Umgebung nach Azure NetApp Files gelten folgende Anforderungen: 

- Stellen Sie sicher, dass Azure NetApp Files in der Azure-Zielregion verfügbar ist.
- Überprüfen Sie die Netzwerkkonnektivität zwischen der Quelle und der IP-Adresse des Azure NetApp Files-Zielvolumes. Die Datenübertragung zwischen der lokalen Umgebung und dem Azure NetApp Files-Dienst wird über ExpressRoute unterstützt.
- Erstellen Sie das Azure NetApp Files-Zielvolume.
- Übertragen Sie die Quelldaten mit Ihrem bevorzugten Tool zum Kopieren von Dateien in das Zielvolume.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Wie erstelle ich eine Kopie eines Azure NetApp Files-Volumes in einer anderen Azure-Region?
    
Azure NetApp Files stellt NFS- und SMB-Volumes bereit.  Sie können jedes dateibasierte Kopiertool zum Replizieren von Daten zwischen Azure-Regionen verwenden. 

NetApp bietet eine SaaS-basierte Lösung: [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service).  Die Lösung ermöglicht es Ihnen, NFS- oder SMB-Daten in Azure NetApp Files-NFS-Exporte oder SMB-Dateifreigaben zu replizieren. 

Sie können auch eine Vielzahl kostenloser Tools zum Kopieren von Daten verwenden. Bei NFS können Sie Workloadtools wie [rsync](https://rsync.samba.org/examples.html) verwenden, um Quelldaten in ein Azure NetApp Files-Volume zu kopieren und zu synchronisieren. Bei SMB können Sie das Workloadtool [robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) auf die gleiche Weise verwenden.  Diese Tools können auch Datei- oder Ordnerberechtigungen replizieren. 

Für die Replikation eines Azure NetApp Files-Volumes in eine andere Azure-Region gelten folgende Anforderungen: 
- Stellen Sie sicher, dass Azure NetApp Files in der Azure-Zielregion verfügbar ist.
- Überprüfen Sie die Netzwerkkonnektivität zwischen VNETs in jeder Region. Derzeit wird ein globales Peering zwischen VNETs nicht unterstützt.  Sie können Konnektivität zwischen VNETs herstellen, indem Sie eine Verbindung über eine ExpressRoute-Leitung erstellen oder eine S2S-VPN-Verbindung verwenden. 
- Erstellen Sie das Azure NetApp Files-Zielvolume.
- Übertragen Sie die Quelldaten mit Ihrem bevorzugten Tool zum Kopieren von Dateien in das Zielvolume.

### <a name="is-migration-with-azure-data-box-supported"></a>Wird die Migration mit Azure Data Box unterstützt?

Nein. Azure Data Box unterstützt Azure NetApp Files derzeit nicht. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Wird die Migration mit dem Azure Import/Export-Dienst unterstützt?

Nein. Der Azure Import/Export-Dienst unterstützt Azure NetApp Files derzeit nicht.

## <a name="next-steps"></a>Nächste Schritte  

- [Häufig gestellte Fragen zu Microsoft Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-faqs)
- [Häufig gestellte Fragen zu Microsoft Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq)
- [Erstellen einer Azure-Supportanfrage](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)
- [Azure Data Box](https://docs.microsoft.com/azure/databox-family/)