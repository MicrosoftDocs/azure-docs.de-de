---
title: Häufig gestellte Fragen zu Azure NetApp Files | Microsoft-Dokumentation
description: Häufig gestellte Fragen zu Azure NetApp Files, z. B. Netzwerk, Sicherheit, Leistung, Kapazitätsverwaltung und Datenmigration und -schutz.
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
ms.date: 11/16/2020
ms.author: b-juche
ms.openlocfilehash: 1537a87999f9a8eecf83a2431b2f53d3ceaedacb
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854698"
---
# <a name="faqs-about-azure-netapp-files"></a>Häufig gestellte Fragen zu Azure NetApp Files

In diesem Artikel werden häufig gestellte Fragen zu Azure NetApp Files beantwortet. 

## <a name="networking-faqs"></a>Häufig gestellte Fragen zum Netzwerk

### <a name="does-the-nfs-data-path-go-over-the-internet"></a>Wird der NFS-Datenpfad über das Internet geleitet?  

Nein. Der NFS-Datenpfad wird nicht über das Internet geleitet. Azure NetApp Files ist ein nativer Azure-Dienst, der in dem Azure Virtual Network (VNET) bereitgestellt wird, in dem der Dienst verfügbar ist. Azure NetApp Files verwendet ein delegiertes Subnetz und stellt eine Netzwerkschnittstelle direkt im VNET bereit. 

Ausführliche Informationen dazu finden Sie unter [Richtlinien für die Azure NetApp Files-Netzwerkplanung](./azure-netapp-files-network-topologies.md).  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Kann ich eine Verbindung zu einem VNET herstellen, das ich für den Azure NetApp Files-Dienst bereits erstellt habe?

Ja, Sie können VNETs verbinden, die Sie für den Dienst erstellt haben. 

Ausführliche Informationen dazu finden Sie unter [Richtlinien für die Azure NetApp Files-Netzwerkplanung](./azure-netapp-files-network-topologies.md).  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Kann ich ein NFS-Volume von Azure NetApp Files anhand des vollqualifizierten DNS-Domänennamens einbinden?

Ja, wenn Sie die erforderlichen DNS-Einträge erstellen. Azure NetApp Files liefert die Dienst-IP-Adresse für das bereitgestellte Volume. 

> [!NOTE] 
> Azure NetApp Files kann bei Bedarf weitere IP-Adressen für den Dienst bereitstellen.  DNS-Einträge müssen möglicherweise in regelmäßigen Abständen aktualisiert werden.

### <a name="can-i-set-or-select-my-own-ip-address-for-an-azure-netapp-files-volume"></a>Kann ich meine eigene IP-Adresse für ein Azure NetApp Files-Volume festlegen oder auswählen?  

Nein. Die IP-Zuweisung zu Azure NetApp Files-Volumes ist dynamisch. Die statische IP-Zuweisung wird nicht unterstützt. 

### <a name="does-azure-netapp-files-support-dual-stack-ipv4-and-ipv6-vnet"></a>Unterstützt Azure NetApp Files Doppelstapel-VNet (IPv4 und IPv6)?

Nein, Azure NetApp Files unterstützt derzeit kein Doppelstapel-VNet (IPv4 und IPv6).  
 
## <a name="security-faqs"></a>Häufig gestellte Fragen zur Sicherheit

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Kann der Netzwerkdatenverkehr zwischen Azure-VM und Speicher verschlüsselt werden?

Der Datenverkehr zwischen NFSv4.1-Clients und Azure NetApp Files-Volumes kann mit Kerberos per AES-256-Verschlüsselung verschlüsselt werden. Weitere Informationen finden Sie unter [Konfigurieren der NFSv4.1-Kerberos-Verschlüsselung für Azure NetApp Files](configure-kerberos-encryption.md).   

Datenverkehr zwischen NFSv3- oder SMB3-Clients und Azure NetApp Files-Volumes ist nicht verschlüsselt. Der Datenverkehr von einer Azure-VM (auf der ein NFS- oder SMB-Client ausgeführt wird) zu Azure NetApp Files ist jedoch so sicher wie jeder andere Datenverkehr zwischen Azure-VMs. Dieser Datenverkehr wird lokal im Azure-Rechenzentrumsnetzwerk ausgeführt. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>Kann der Speicher im Ruhezustand verschlüsselt werden?

Alle Azure NetApp Files-Volumes werden mit dem FIPS 140-2-Standard verschlüsselt. Alle Schlüssel werden vom Azure NetApp Files-Dienst verwaltet. 

### <a name="how-are-encryption-keys-managed"></a>Wie werden Verschlüsselungsschlüssel verwaltet? 

Die Schlüsselverwaltung für Azure NetApp Files wird vom Dienst verarbeitet. Für jedes Volume wird ein eindeutiger XTS-AES-256-Datenverschlüsselungsschlüssel generiert. Zum Verschlüsseln und Schützen aller Volumeschlüssel wird eine Verschlüsselungsschlüsselhierarchie verwendet. Diese Verschlüsselungsschlüssel werden niemals in unverschlüsseltem Format angezeigt oder gemeldet. Verschlüsselungsschlüssel werden sofort gelöscht, wenn ein Volume gelöscht wird.

Unterstützung von kundenseitig verwalteten Schlüsseln (Bring Your Own Key) mithilfe von Azure Dedicated HSM ist in den Regionen „USA, Osten“, „USA, Süden-Mitte“, „USA, Westen2“ und „US Gov Virginia“ unter Kontrolle verfügbar. Sie können den Zugriff unter [anffeedback@microsoft.com](mailto:anffeedback@microsoft.com) anfordern. Entsprechend der zunehmenden Verfügbarkeit von Kapazitäten werden Anforderungen genehmigt.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Kann ich Regeln für NFS-Exportrichtlinien konfigurieren, um den Zugriff auf das Azure NetApp Files-Diensteinbindungsziel zu steuern?

Ja, Sie können bis zu fünf Regeln in einer einzigen NFS-Exportrichtlinie konfigurieren.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Unterstützt Azure NetApp Files Netzwerksicherheitsgruppen?

Nein, Sie können derzeit keine Netzwerksicherheitsgruppen auf das delegierte Azure NetApp Files-Subnetz oder die vom Dienst erstellten Netzwerkschnittstellen anwenden.

### <a name="can-i-use-azure-rbac-with-azure-netapp-files"></a>Kann ich Azure RBAC mit Azure NetApp Files verwenden?

Ja, Azure NetApp Files unterstützt Azure RBAC-Features.

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

Sie können den Servicelevel eines vorhandenen Volumes ändern, indem Sie das Volume in einen anderen Kapazitätspool verschieben, der den von Ihnen für das Volume gewünschten [Servicelevel](azure-netapp-files-service-levels.md) verwendet. Siehe [Dynamisches Ändern der Dienstebene eines Volumes](dynamic-change-volume-service-level.md). 

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Wie überwache ich die Leistung von Azure NetApp Files?

Azure NetApp Files bietet Metriken zum Messen der Volumeleistung. Sie können auch Azure Monitor verwenden, um die Nutzungsmetriken für Azure NetApp Files zu überwachen.  Eine Liste der Leistungsmetriken für Azure NetApp Files finden Sie unter [Metriken für Azure NetApp Files](azure-netapp-files-metrics.md).

### <a name="whats-the-performance-impact-of-kerberos-on-nfsv41"></a>Was sind die Leistungsauswirkungen von Kerberos auf NFSv4.1?

Informationen zu den Sicherheitsoptionen für NFSv4.1, den getesteten Leistungsvektoren und den erwarteten Leistungsauswirkungen finden Sie unter [Leistungsauswirkungen von Kerberos auf NFSv4.1](configure-kerberos-encryption.md#kerberos_performance). 

## <a name="nfs-faqs"></a>Häufig gestellte Fragen zu NFS

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Ich möchte, dass automatisch ein Volume eingebunden wird, wenn eine Azure-VM gestartet oder neu gestartet wird.  Wie konfiguriere ich meinen Host für persistente NFS-Volumes?

Damit beim Starten oder Neustarten einer VM automatisch ein NFS-Volume eingebunden wird, fügen Sie einen Eintrag für die Datei `/etc/fstab` zum Host hinzu. 

Weitere Informationen finden Sie unter [Einbinden oder Aufheben der Einbindung eines Volumes auf virtuellen Windows- oder Linux-Computern](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Warum zeigt der DF-Befehl auf dem NFS-Client die Größe des bereitgestellten Volumes nicht an?

Die in DF gemeldete Volumegröße ist die maximale Größe, auf die das Azure NetApp Files-Volume vergrößert werden kann. Die Größe des Azure NetApp Files-Volumes im DF-Befehl spiegelt nicht das Kontingent oder die Größe des Volumes wieder.  Sie können die Größe und das Kontingent des Azure NetApp Files-Volumes über das Azure-Portal oder die API abrufen.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Welche NFS-Version wird von Azure NetApp Files unterstützt?

Azure NetApp Files unterstützt NFSv3 und NFSv4.1. Für die [Volumeerstellung](azure-netapp-files-create-volumes.md) können beide NFS-Versionen verwendet werden. 

### <a name="how-do-i-enable-root-squashing"></a>Wie aktiviere ich das Root Squashing?

Sie können angeben, ob für das Stammkonto (Root) Zugriff auf das Volume besteht, indem Sie die Exportrichtlinie des Volumes verwenden. Weitere Informationen finden Sie unter [Konfigurieren der Exportrichtlinie für ein NFS-Volume](azure-netapp-files-configure-export-policy.md).

### <a name="can-i-use-the-same-file-path-volume-creation-token-for-multiple-volumes"></a>Kann ich den gleichen Dateipfad (Volumeerstellungstoken) für mehrere Volumes verwenden?

Ja, das ist möglich. Der Dateipfad muss jedoch entweder in einem anderen Abonnement oder in einer anderen Region verwendet werden.   

Ein Beispiel: Angenommen, Sie erstellen ein Volume namens `vol1`. Anschließend erstellen Sie ein weiteres Volume mit dem Namen `vol1` in einem anderen Kapazitätspool, aber im gleichen Abonnement und in der gleichen Region. In diesem Fall führt die Verwendung des gleichen Volumenamens (`vol1`) zu einem Fehler. Wenn Sie den gleichen Dateipfad verwenden möchten, muss sich der Name in einer anderen Region oder in einem anderen Abonnement befinden.

## <a name="smb-faqs"></a>Häufig gestellte Fragen zu SMB

### <a name="which-smb-versions-are-supported-by-azure-netapp-files"></a>Welche SMB-Versionen werden von Azure NetApp Files unterstützt?

Azure NetApp Files unterstützt SMB 2.1 und SMB 3.1 (Unterstützung für SMB 3.0 ist hierbei eingeschlossen).    

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>Ist für den SMB-Zugriff eine Active Directory-Verbindung erforderlich? 

Ja, Sie müssen eine Active Directory-Verbindung erstellen, bevor Sie ein SMB-Volume bereitstellen. Damit erfolgreich eine Verbindung hergestellt werden kann, muss das delegierte Subnetz von Azure NetApp Files auf die angegebenen Domänencontroller zugreifen können.  Ausführliche Informationen dazu finden Sie unter [Erstellen eines SMB-Volumes](./azure-netapp-files-create-volumes-smb.md). 

### <a name="how-many-active-directory-connections-are-supported"></a>Wie viele Active Directory-Verbindungen werden unterstützt?

Azure NetApp Files bietet keine Unterstützung für mehrere Active Directory-Verbindungen in einer einzelnen *Region*. Dies gilt auch dann, wenn die AD-Verbindungen zu unterschiedlichen NetApp-Konten gehören. Sie können jedoch über mehrere AD-Verbindungen in einem einzelnen *Abonnement* verfügen, sofern sich die AD-Verbindungen in unterschiedlichen Regionen befinden. Wenn Sie mehrere AD-Verbindungen in einer einzelnen Region benötigen, können Sie hierfür separate Abonnements verwenden. 

AD-Verbindungen werden für jeweils ein NetApp-Konto konfiguriert. Die AD-Verbindung ist ausschließlich über das NetApp-Konto sichtbar, in dem sie erstellt wurde.

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Unterstützt Azure NetApp Files Azure Active Directory? 

Sowohl [Azure Active Directory (AD) Domain Services](../active-directory-domain-services/overview.md) als auch [Active Directory Domain Services (AD DS)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) wird unterstützt. Bereits vorhandene Active Directory-Domänencontroller können mit Azure NetApp Files verwendet werden. Domänencontroller können als virtuelle Computer in Azure oder lokal über ExpressRoute oder S2S-VPN betrieben werden. Azure NetApp Files unterstützt aktuell keine AD-Einbindung für [Azure Active Directory](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/).

Bei Verwendung von Azure NetApp Files mit Azure Active Directory Domain Services lautet der Pfad der Organisationseinheit `OU=AADDC Computers`, wenn Sie Active Directory für Ihr NetApp-Konto konfigurieren.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>Welche Versionen von Windows Server Active Directory werden unterstützt?

Azure NetApp Files unterstützt die Windows Server-Versionen 2008 R2 SP1 – 2019 von Active Directory Domain Services.

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>Warum zeigt der verfügbare Speicherplatz auf meinem SMB-Client die bereitgestellte Größe nicht an?

Die vom SMB-Client gemeldete Volumegröße ist die maximale Größe, auf die das Azure NetApp Files-Volume vergrößert werden kann. Die Größe des Azure NetApp Files-Volumes, die auf dem SMB-Client angezeigt wird, spiegelt nicht das Kontingent oder die Größe des Volumes wieder. Sie können die Größe und das Kontingent des Azure NetApp Files-Volumes über das Azure-Portal oder die API abrufen.

### <a name="im-having-issues-connecting-to-my-smb-share-what-should-i-do"></a>Ich habe Probleme beim Herstellen einer Verbindung mit meiner SMB-Freigabe.   Wie sollte ich vorgehen?

Legen Sie als bewährte Vorgehensweise die maximale Toleranz für die Synchronisierung der Computeruhr auf fünf Minuten fest. Weite Informationen finden Sie unter [Maximale Toleranz für die Synchronisierung der Computeruhr](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj852172(v=ws.11)). 

<!--
### Does Azure NetApp Files support LDAP signing? 

Yes, Azure NetApp Files supports LDAP signing by default. This functionality enables secure LDAP lookups between the Azure NetApp Files service and the user-specified [Active Directory Domain Services domain controllers](/windows/win32/ad/active-directory-domain-services). For more information, see [ADV190023 | Microsoft Guidance for Enabling LDAP Channel Binding and LDAP Signing](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).
--> 

## <a name="capacity-management-faqs"></a>Häufig gestellte Fragen zur Kapazitätsverwaltung

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Wie überwache ich die Nutzung für den Kapazitätspool und das Volume von Azure NetApp Files? 

Azure NetApp Files stellt Nutzungsmetriken für Kapazitätspool und Volume bereit. Sie können auch Azure Monitor verwenden, um die Nutzung für Azure NetApp Files zu überwachen. Ausführliche Informationen finden Sie unter [Metriken für Azure NetApp Files](azure-netapp-files-metrics.md). 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Kann ich Azure NetApp Files über den Azure Storage-Explorer verwalten?

Nein. Azure NetApp Files wird vom Azure Storage-Explorer nicht unterstützt.

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>Wie bestimme ich, ob ein Verzeichnis dabei ist, sein Größenlimit zu erreichen?

Sie können den Befehl `stat` von einem Client aus verwenden, um festzustellen, ob sich ein Verzeichnis der maximal zulässigen Größe für Verzeichnismetadaten (320 MB) nähert.

Bei einem Verzeichnis mit 320 MB beträgt die Anzahl der Blöcke 655360, wobei jeder Block die Größe von 512 Bytes hat.  (D. h., 320x1024x1024/512.)  

Beispiele:

```console
[makam@cycrh6rtp07 ~]$ stat bin
File: 'bin'
Size: 4096            Blocks: 8          IO Block: 65536  directory

[makam@cycrh6rtp07 ~]$ stat tmp
File: 'tmp'
Size: 12288           Blocks: 24         IO Block: 65536  directory
 
[makam@cycrh6rtp07 ~]$ stat tmp1
File: 'tmp1'
Size: 4096            Blocks: 8          IO Block: 65536  directory
```


## <a name="data-migration-and-protection-faqs"></a>Häufig gestellte Fragen zu Datenmigration und -schutz

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Wie migriere ich Daten zu Azure NetApp Files?
Azure NetApp Files stellt NFS- und SMB-Volumes bereit.  Sie können jedes dateibasierte Kopiertool zum Migrieren von Daten zum Dienst verwenden. 

NetApp bietet eine SaaS-basierte Lösung: [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service).  Die Lösung ermöglicht es Ihnen, NFS- oder SMB-Daten in Azure NetApp Files-NFS-Exporte oder SMB-Dateifreigaben zu replizieren. 

Sie können auch eine Vielzahl kostenloser Tools zum Kopieren von Daten verwenden. Bei NFS können Sie Workloadtools wie [rsync](https://rsync.samba.org/examples.html) verwenden, um Quelldaten in ein Azure NetApp Files-Volume zu kopieren und zu synchronisieren. Bei SMB können Sie das Workloadtool [robocopy](/windows-server/administration/windows-commands/robocopy) auf die gleiche Weise verwenden.  Diese Tools können auch Datei- oder Ordnerberechtigungen replizieren. 

Für die Datenmigration aus einer lokalen Umgebung nach Azure NetApp Files gelten folgende Anforderungen: 

- Stellen Sie sicher, dass Azure NetApp Files in der Azure-Zielregion verfügbar ist.
- Überprüfen Sie die Netzwerkkonnektivität zwischen der Quelle und der IP-Adresse des Azure NetApp Files-Zielvolumes. Die Datenübertragung zwischen der lokalen Umgebung und dem Azure NetApp Files-Dienst wird über ExpressRoute unterstützt.
- Erstellen Sie das Azure NetApp Files-Zielvolume.
- Übertragen Sie die Quelldaten mit Ihrem bevorzugten Tool zum Kopieren von Dateien in das Zielvolume.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Wie erstelle ich eine Kopie eines Azure NetApp Files-Volumes in einer anderen Azure-Region?
    
Azure NetApp Files stellt NFS- und SMB-Volumes bereit.  Sie können jedes dateibasierte Kopiertool zum Replizieren von Daten zwischen Azure-Regionen verwenden. 

NetApp bietet eine SaaS-basierte Lösung: [NetApp Cloud Sync](https://cloud.netapp.com/cloud-sync-service).  Die Lösung ermöglicht es Ihnen, NFS- oder SMB-Daten in Azure NetApp Files-NFS-Exporte oder SMB-Dateifreigaben zu replizieren. 

Sie können auch eine Vielzahl kostenloser Tools zum Kopieren von Daten verwenden. Bei NFS können Sie Workloadtools wie [rsync](https://rsync.samba.org/examples.html) verwenden, um Quelldaten in ein Azure NetApp Files-Volume zu kopieren und zu synchronisieren. Bei SMB können Sie das Workloadtool [robocopy](/windows-server/administration/windows-commands/robocopy) auf die gleiche Weise verwenden.  Diese Tools können auch Datei- oder Ordnerberechtigungen replizieren. 

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

- [Häufig gestellte Fragen zu Microsoft Azure ExpressRoute](../expressroute/expressroute-faqs.md)
- [Häufig gestellte Fragen zu Microsoft Azure Virtual Network](../virtual-network/virtual-networks-faq.md)
- [Erstellen einer Azure-Supportanfrage](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Azure Data Box](../databox/index.yml)
- [Häufig gestellte Fragen zur Leistung von SMB für Azure NetApp Files](azure-netapp-files-smb-performance.md)
