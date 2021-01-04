---
title: Häufig gestellte Fragen (FAQ) zu Azure Files | Microsoft-Dokumentation
description: Hier erhalten Sie Antworten auf häufig zu Azure Files gestellte Fragen. Sie können Azure-Dateifreigaben gleichzeitig unter Cloud- und lokalen Bereitstellungen von Windows, Linux oder macOS einbinden.
author: roygara
ms.service: storage
ms.date: 02/23/2020
ms.author: rogarana
ms.subservice: files
ms.topic: conceptual
ms.openlocfilehash: 266862a1691387caf1b1ffa9a47b0e60e84970d8
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492130"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Häufig gestellte Fragen (FAQ) zu Azure Files
[Azure Files](storage-files-introduction.md) bietet vollständig verwaltete Dateifreigaben in der Cloud, auf die über das Branchenstandardprotokoll [Server Message Block (SMB)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) sowie über das [Network File System-Protokoll (NFS)](https://en.wikipedia.org/wiki/Network_File_System) (Vorschau) zugegriffen werden kann. Sie können Azure-Dateifreigaben gleichzeitig unter Cloud- und lokalen Bereitstellungen von Windows, Linux und macOS einbinden. Azure-Dateifreigaben können auch auf Windows Server-Computern zwischengespeichert werden, indem die Azure-Dateisynchronisierung verwendet wird, um den schnellen Zugriff in der Nähe der Datennutzung zu ermöglichen.

In diesem Artikel werden häufig gestellte Fragen zu Azure Files-Features und -Funktionen beantwortet, z.B. die Nutzung der Azure-Dateisynchronisierung mit Azure Files. Wenn Sie hier keine Antwort auf Ihre Frage finden, können Sie sich über die folgenden Kanäle an uns wenden (Eskalationsreihenfolge):

1. Im Abschnitt „Kommentare“ dieses Artikels.
2. [Frageseite von Microsoft Q&A (Fragen und Antworten) zu Azure Storage](/answers/topics/azure-file-storage.html).
3. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) 
4. Microsoft-Support. Wählen Sie zum Erstellen einer neuen Supportanfrage im Azure-Portal auf der Registerkarte **Hilfe** die Schaltfläche **Hilfe und Support** und anschließend die Option **Neue Supportanfrage**.

## <a name="general"></a>Allgemein
* <a id="why-files-useful"></a>
  **Auf welche Weise ist Azure Files nützlich?**  
   Sie können Azure Files zum Erstellen von Dateifreigaben in der Cloud verwenden, ohne für die Verwaltung eines physischen Servers oder Geräts verantwortlich zu sein. Wir übernehmen die monotone Arbeit für Sie, z.B. das Anwenden von Betriebssystemupdates und das Austauschen von fehlerhaften Datenträgern. Lesen Sie [Nützlichkeit von Azure Files](storage-files-introduction.md#why-azure-files-is-useful), um mehr über die Szenarien zu erfahren, in denen Azure Files hilfreich ist.

* <a id="file-access-options"></a>
  **Auf welche Arten kann auf Dateien in Azure Files zugegriffen werden?**  
    Sie können SMB-Dateifreigaben mithilfe des SMB 3.0-Protokolls auf Ihrem lokalen Computer einbinden oder mithilfe von Tools wie dem [Storage-Explorer](https://storageexplorer.com/) auf Dateien in Ihrer Dateifreigabe zugreifen. NFS-Dateifreigaben können auf dem lokalen Computer durch Kopieren und Einfügen des im Azure-Portal bereitgestellten Skripts eingebunden werden. In Ihrer Anwendung können Sie über Speicherclientbibliotheken, REST-APIs, PowerShell oder die Azure CLI auf Ihre Dateien in der Azure-Dateifreigabe zugreifen.

* <a id="what-is-afs"></a>
  **Was ist Azure-Dateisynchronisierung?**  
    Sie können die Azure-Dateisynchronisierung verwenden, um die Dateifreigaben Ihrer Organisation in Azure Files zu zentralisieren, ohne auf die Flexibilität, Leistung und Kompatibilität eines lokalen Dateiservers verzichten zu müssen. Mit der Azure-Dateisynchronisierung werden Ihre Windows Server-Computer zu einem schnellen Cache für Ihre Azure-Dateifreigabe. Sie können ein beliebiges Protokoll verwenden, das unter Windows Server verfügbar ist, um lokal auf Ihre Daten zuzugreifen, z.B. SMB, Network File System (NFS) und File Transfer Protocol Service (FTPS). Sie können weltweit so viele Caches wie nötig nutzen.

* <a id="files-versus-blobs"></a>
  **Warum sollte ich für meine Daten eine Azure-Dateifreigabe anstelle von Azure Blob Storage verwenden?**  
    Azure Files und Azure Blob Storage bieten beide die Möglichkeit, große Datenmengen in der Cloud zu speichern, unterscheiden sich in ihrem Einsatzzweck aber leicht voneinander. 
    
    Der Azure Blob Storage ist für umfangreiche native Cloudanwendungen geeignet, die unstrukturierte Daten speichern müssen. In Bezug auf die Maximierung von Leistung und Skalierbarkeit stellt der Azure Blob Storage eine einfachere Speicherabstraktion dar als ein echtes Dateisystem. Sie können auf den Azure Blob Storage nur über REST-basierte Clientbibliotheken (oder direkt über das REST-basierte Protokoll) zugreifen.

    Azure Files ist eigentlich ein Dateisystem. Azure Files verfügt über alle Datei-Abstracts, die Sie bei der jahrelangen Arbeit mit lokalen Betriebssystemen kennen und schätzen gelernt haben. Wie Azure Blob Storage auch, verfügt Azure Files über eine REST-Schnittstelle und REST-basierte Clientbibliotheken. Im Gegensatz zu Azure Blob Storage ermöglicht Azure Files den SMB- oder NFS-Zugriff auf Azure-Dateifreigaben. Dateifreigaben können direkt unter Windows, Linux oder macOS (lokal oder auf virtuellen Cloudcomputern) eingebunden werden, ohne dass Sie dafür Code schreiben oder dem Dateisystem spezielle Treiber hinzufügen müssen. Außerdem können Sie Azure SMB-Dateifreigaben auf lokalen Dateiservern mithilfe der Azure-Dateisynchronisierung zwischenspeichern, um schnellen Zugriff in der Nähe der Datennutzung zu ermöglichen. 
   
    Eine ausführlichere Beschreibung der Unterschiede zwischen Azure Files und Azure Blob Storage finden Sie in der [Einführung in die zentralen Azure Storage-Dienste](../common/storage-introduction.md). Weitere Informationen zum Azure Blob Storage finden Sie unter [Einführung in Blob Storage](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**Warum sollte ich eine Azure-Dateifreigabe anstelle von Azure-Datenträgern verwenden?**  
    Unter Azure-Datenträger ist ein Datenträger nicht mehr als ein einfacher Datenträger. Sie erhöhen den Nutzen von Azure-Datenträgern, indem Sie einen Datenträger an einen virtuellen Computer anfügen, der in Azure ausgeführt wird. Azure-Datenträger können für alle Zwecke verwendet werden, die auch für einen Datenträger auf einem lokalen Server gelten. Sie können sie als Betriebssystemdatenträger, als Auslagerungsbereich für ein Betriebssystem oder als dedizierten Speicher für eine Anwendung einsetzen. Ein interessanter Einsatzzweck von Azure-Datenträgern ist die Erstellung eines Dateiservers in der Cloud, der in denselben Situationen wie bei einer Azure-Dateifreigabe verwendet wird. Die Bereitstellung eines Dateiservers in Azure Virtual Machines ist ein auf hohe Leistung ausgelegtes Verfahren, um Dateispeicher in Azure freizugeben, wenn Sie Bereitstellungsoptionen benötigen, die derzeit nicht von Azure Files unterstützt werden. 

    Das Ausführen eines Dateiservers mit Azure-Datenträgern als Back-End-Speicher ist aus einigen Gründen normalerweise aber deutlich teurer als die Verwendung einer Azure-Dateifreigabe. Zunächst einmal müssen Sie nicht nur für die Kosten des Datenträgerspeichers aufkommen, sondern auch für die Ausgaben zum Betrieb von einer oder mehreren Azure-VMs. Zweitens müssen Sie auch die VMs verwalten, die zum Ausführen des Dateiservers verwendet werden. Beispielsweise sind Sie für Upgrades des Betriebssystems verantwortlich. Und falls Daten lokal zwischengespeichert werden müssen, sind Sie auch für die Einrichtung und Verwaltung der Replikationstechnologien (z.B. DFS-Replikation (Distributed File System Replication)) zuständig.

    Ein Ansatz, bei dem Sie jeweils die Vorteile von Azure Files sowie eines unter Azure Virtual Machines gehosteten Dateiservers (zusätzlich zur Verwendung von Azure-Datenträgern als Back-End-Speicher) nutzen können, ist die Installation der Azure-Dateisynchronisierung auf einem Dateiserver, der auf einer Cloud-VM gehostet wird. Wenn sich die Azure-Dateifreigabe in derselben Region wie Ihr Dateiserver befindet, können Sie das Cloudtiering aktivieren und den Prozentsatz des freien Volumespeicherplatzes auf den Maximalwert (99%) festlegen. So wird die minimale Duplizierung von Daten sichergestellt. Außerdem können Sie für Ihre Dateiserver beliebige Anwendungen nutzen, z.B. Anwendungen, die eine Unterstützung des NFS-Protokolls benötigen.

    Informationen zu einer Möglichkeit, wie Sie einen leistungsstarken und hochverfügbaren Dateiserver in Azure einrichten, finden Sie unter [Deploying IaaS VM Guest Clusters in Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/) (Bereitstellen von IaaS-VM-Gastclustern in Microsoft Azure). Eine ausführlichere Beschreibung der Unterschiede zwischen Azure Files und Azure-Datenträgern finden Sie in der [Einführung in die zentralen Azure Storage-Dienste](../common/storage-introduction.md). Weitere Informationen zu Azure-Datenträgern finden Sie unter [Azure Managed Disks – Übersicht](../../virtual-machines/managed-disks-overview.md).

* <a id="get-started"></a>
  **Wie kann ich mich mit Azure Files vertraut machen?**  
   Die ersten Schritte mit Azure Files sind leicht. Erstellen Sie zunächst entweder eine [SMB-Dateifreigabe](storage-how-to-create-file-share.md) oder eine [NFS-Dateifreigabe](storage-files-how-to-create-nfs-shares.md), die Sie anschließend in Ihr bevorzugtes Betriebssystem einbinden: 

  * [Einbinden einer SMB-Freigabe in Windows](storage-how-to-use-files-windows.md)
  * [Einbinden einer SMB-Freigabe in Linux](storage-how-to-use-files-linux.md)
  * [Einbinden einer SMB-Freigabe in macOS](storage-how-to-use-files-mac.md)
  * [Einbinden einer NFS-Dateifreigabe](storage-files-how-to-mount-nfs-shares.md)

    Eine ausführlichere Anleitung zum Bereitstellen einer Azure-Dateifreigabe als Ersatz für Produktionsdateifreigaben in Ihrer Organisation finden Sie unter [Planung für eine Azure Files-Bereitstellung](storage-files-planning.md).

* <a id="redundancy-options"></a>
  **Welche Speicherredundanzoptionen werden von Azure Files unterstützt?**  
    Azure Files unterstützt derzeit lokal redundanten Speicher (LRS), zonenredundanten Speicher (ZRS), georedundanten Speicher (GRS) und geozonenredundanten Speicher (GZRS). Im Premium-Tarif von Azure Files werden derzeit nur LRS und ZRS unterstützt.

* <a id="tier-options"></a>
  **Welche Speicherebenen werden in Azure Files unterstützt?**  
    Azure Files unterstützt zwei Speichertarife: Premium und Standard. Standard-Dateifreigaben werden in universellen Speicherkonten (GPv1 oder GPv2) und Premium-Dateifreigaben in FileStorage-Speicherkonten erstellt. Erfahren Sie mehr darüber, wie Sie [Standard-Dateifreigaben](storage-how-to-create-file-share.md) und [Premium-Dateifreigaben](storage-how-to-create-premium-fileshare.md) erstellen. 
    
    > [!NOTE]
    > Es ist nicht möglich, Azure-Dateifreigaben basierend auf Blobspeicherkonten oder universellen *Premium*-Speicherkonten (GPv1 oder GPv2) zu erstellen. Standard-Azure-Dateifreigaben können nur in universellen *Standard*-Konten und Premium-Azure-Dateifreigaben nur in FileStorage-Speicherkonten erstellt werden. Universelle *Premium*-Speicherkonten (GPv1 und GPv2) sind nur für Premium-Seitenblobs bestimmt. 

* <a id="file-locking"></a>
  **Unterstützt Azure Files das Sperren von Dateien?**  
    Ja. Die Dateisperrung im SMB-/Windows-Stil wird von Azure Files vollständig unterstützt. Ausführliche Informationen finden Sie [hier](/rest/api/storageservices/managing-file-locks).

* <a id="give-us-feedback"></a>
  **Ich wünsche mir, dass Azure Files ein bestimmtes Feature hinzugefügt wird. Können Sie es hinzufügen?**  
    Das Azure Files-Team legt großen Wert darauf, Ihr Feedback zu unserem Dienst zu erhalten. Stimmen Sie in [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) über Funktionsanfragen ab! Wir hoffen, dass wir Sie mit vielen neuen Funktionen begeistern können.

## <a name="azure-file-sync"></a>Azure-Dateisynchronisierung

* <a id="afs-region-availability"></a>
  **Welche Regionen werden für die Azure-Dateisynchronisierung unterstützt?**  
    Eine Liste der verfügbaren Regionen finden Sie im Abschnitt [Regionsverfügbarkeit](storage-sync-files-planning.md#azure-file-sync-region-availability) im Planungshandbuch für die Azure-Dateisynchronisierung. Wir erweitern den Support kontinuierlich auf weitere Regionen, einschließlich nicht öffentliche Regionen.

* <a id="cross-domain-sync"></a>
  **Kann ich in Domänen eingebundene Server und nicht in Domänen eingebundene Server in derselben Synchronisierungsgruppe verwenden?**  
    Ja. Eine Synchronisierungsgruppe kann Serverendpunkte mit unterschiedlichen Active Directory-Mitgliedschaften auch dann umfassen, wenn diese nicht in Domänen eingebunden sind. Diese Konfiguration funktioniert zwar technisch einwandfrei, aber es wird davon abgeraten, diese als normale Konfiguration zu verwenden. Der Grund ist, dass Zugriffssteuerungslisten (ACLs), die für Dateien und Ordner auf einem Server definiert sind, unter Umständen von anderen Servern in der Synchronisierungsgruppe nicht durchgesetzt werden können. Die besten Ergebnisse erzielen Sie, wenn Sie sich an die folgende Empfehlung halten: Führen Sie die Synchronisierung zwischen Servern durch, die in derselben Active Directory-Gesamtstruktur enthalten sind, zwischen Servern in unterschiedlichen Active Directory-Gesamtstrukturen, die aber über eingerichtete Vertrauensstellungen verfügen, oder zwischen Servern, die keiner Domäne angehören. Hierbei ist es nicht ratsam, diese Konfigurationen zu mischen.

* <a id="afs-change-detection"></a>
  **Ich habe eine Datei in meiner Azure-Dateifreigabe direkt per SMB oder über das Portal erstellt. Wie lange dauert es, bis die Datei für die Server der Synchronisierungsgruppe synchronisiert wurde?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]


* <a id="afs-sync-time"></a>
  **Wieviel Zeit benötigt die Azure-Dateisynchronisierung, um 1 TiB Daten hochzuladen?**
  
    Die Leistung variiert abhängig von den Umgebungseinstellungen und der Konfiguration sowie davon, ob es sich um eine anfängliche oder laufende Synchronisierung handelt. Weitere Informationen finden Sie unter [Leistungsmetriken der Azure-Dateisynchronisierung](storage-files-scale-targets.md#azure-file-sync-performance-metrics).

* <a id="afs-conflict-resolution"></a>**Was passiert, wenn dieselbe Datei ungefähr zur gleichen Zeit auf zwei Servern geändert wird?**  
    Für die Azure-Dateisynchronisierung wird eine einfache Strategie zur Konfliktlösung verwendet: Die Änderungen der Dateien, die gleichzeitig in zwei Endpunkten vorgenommen werden, werden jeweils beibehalten. Für die zuletzt vorgenommene Änderung wird der ursprüngliche Dateiname beibehalten. Die ältere Datei (ermittelt durch LastWriteTime) weist den Endpunktnamen und die Konfliktnummer auf, die an den Dateinamen angefügt werden. Bei Serverendpunkten ist der Endpunktname der Name des Servers. Bei Cloudendpunkten lautet der Endpunktname **Cloud**. Für den Namen wird die folgende Taxonomie verwendet: 
   
    \<FileNameWithoutExtension\>-\<endpointName\>\[-#\].\<ext\>  

    Beim ersten Konflikt für „CompanyReport.docx“ wird beispielsweise „CompanyReport-CentralServer.docx“ verwendet, wenn CentralServer der Ort ist, an dem der ältere Schreibvorgang durchgeführt wurde. Der zweite Konflikt hat dann den Namen „CompanyReport-CentralServer-1.docx“. Die Azure-Dateisynchronisierung unterstützt 100 Konfliktdateien pro Datei. Sobald die maximale Anzahl von Konfliktdateien erreicht ist, kann die Datei nicht mehr synchronisiert werden, bis die Anzahl der Konfliktdateien weniger als 100 beträgt.

* <a id="afs-storage-redundancy"></a>
  **Wird georedundanter Speicher von der Azure-Dateisynchronisierung unterstützt?**  
    Ja. Azure Files unterstützt sowohl lokal redundanten Speicher (LRS) als auch georedundanten Speicher (GRS). Wenn Sie das Failover eines Speicherkontos zwischen gekoppelten Regionen aus einem für GRS konfigurierten Konto auslösen, empfiehlt Microsoft, die neue Region nur als Sicherung der Daten zu behandeln. Die Azure-Dateisynchronisierung beginnt nicht automatisch die Synchronisierung mit der neuen primären Region. 

* <a id="sizeondisk-versus-size"></a>
  **Warum stimmt die Eigenschaft *Größe auf Datenträger* einer Datei nach der Verwendung der Azure-Dateisynchronisierung nicht mit der Eigenschaft *Größe* überein?**  
  Siehe [Grundlegendes zum Cloudtiering](storage-sync-cloud-tiering.md#sizeondisk-versus-size).

* <a id="is-my-file-tiered"></a>
  **Woran erkenne ich, ob eine Datei per Tiering ausgelagert wurde?**  
  Siehe [Grundlegendes zum Cloudtiering](storage-sync-cloud-tiering.md#is-my-file-tiered).

* <a id="afs-recall-file"></a>**Eine Datei, die ich verwenden möchte, wurde per Tiering ausgelagert. Wie kann ich die Datei auf den Datenträger zurückrufen, um sie lokal zu verwenden?**  
  Siehe [Grundlegendes zum Cloudtiering](storage-sync-cloud-tiering.md#afs-recall-file).

* <a id="afs-force-tiering"></a>
  **Wie kann ich das Tiering einer Datei oder eines Verzeichnisses erzwingen?**  
  Siehe [Grundlegendes zum Cloudtiering](storage-sync-cloud-tiering.md#afs-force-tiering).

* <a id="afs-effective-vfs"></a>
  **Wie wird *freier Speicherplatz auf Volume* interpretiert, wenn ich über mehrere Serverendpunkte auf einem Volume verfüge?**  
  Siehe [Grundlegendes zum Cloudtiering](storage-sync-cloud-tiering.md#afs-effective-vfs).
  
* <a id="afs-tiered-files-tiering-disabled"></a>
  **Ich habe Cloudtiering deaktiviert. Warum befinden sich mehrstufige Dateien am Speicherort des Serverendpunkts?**  
  Siehe [Grundlegendes zum Cloudtiering](storage-sync-cloud-tiering.md#afs-tiering-disabled).

* <a id="afs-files-excluded"></a>
  **Welche Dateien oder Ordner werden automatisch von der Azure-Dateisynchronisierung ausgeschlossen?**  
  Siehe [Übersprungene Dateien](storage-sync-files-planning.md#files-skipped).

* <a id="afs-os-support"></a>
  **Kann ich die Azure-Dateisynchronisierung entweder mit Windows Server 2008 R2, Linux oder meinem NAS-Gerät (Network Attached Storage) verwenden?**  
    Gegenwärtig unterstützt die Azure-Dateisynchronisierung ausschließlich Windows Server 2019, Windows Server 2016 und Windows Server 2012 R2. Zurzeit sind keine anderen Optionen geplant. Je nach Kundennachfrage sind wir aber offen dafür, weitere Plattformen zu unterstützen. Teilen Sie uns über [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) mit, für welche Plattformen Sie Unterstützung von uns wünschen.

* <a id="afs-tiered-files-out-of-endpoint"></a>
  **Warum sind mehrstufige Dateien außerhalb des Serverendpunkt-Namespaces vorhanden?**  
    Vor der Agent-Version 3 der Azure-Dateisynchronisierung blockierte die Azure-Dateisynchronisierung die Verschiebung mehrstufiger Dateien außerhalb des Serverendpunkts, aber auf demselben Volume wie der Serverendpunkt. Kopiervorgänge, Verschiebungen nicht mehrstufiger Dateien und Verschiebungen mehrstufiger Dateien in andere Volumes waren hiervon nicht betroffen. Der Grund für dieses Verhalten war die implizite Annahme, dass Datei-Explorer und andere Windows-APIs Verschiebungen auf dem gleichen Volume durchführen, die (fast) unmittelbare Umbenennungsvorgänge sind. Dies bedeutet, dass Datei-Explorer oder andere Methoden zum Verschieben (z.B. über die Befehlszeile oder PowerShell) scheinbar nicht mehr reagieren, während die Azure-Dateisynchronisierung die Daten aus der Cloud abruft. Beginnend mit [Version 3.0.12.0 des Azure-Dateisynchronisierungs-Agents](storage-files-release-notes.md#supported-versions) lässt die Azure-Dateisynchronisierung zu, dass Sie eine mehrstufige Datei außerhalb des Serverendpunkts verschieben. Wir vermeiden die bereits erwähnten negativen Auswirkungen, indem wir zulassen, dass die mehrstufige Datei außerhalb des Serverendpunkts als mehrstufige Datei vorhanden ist, und dann die Datei im Hintergrund abrufen. Dies bedeutet, dass Verschiebungen auf dem gleichen Volume unmittelbar sind, und wir erledigen die gesamte Arbeit zum Abrufen der Datei auf den Datenträger, nachdem der Verschiebevorgang abgeschlossen ist. 

* <a id="afs-do-not-delete-server-endpoint"></a>
  **Es besteht ein Problem mit der Azure-Dateisynchronisierung auf dem Server (Synchronisierung, Cloudtiering usw.). Soll der Serverendpunkt entfernt und neu erstellt werden?**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
  **Kann ich den Speichersynchronisierungsdienst und/oder das Speicherkonto in eine andere Ressourcengruppe, ein anderes Abonnement oder einen anderen Azure AD-Mandanten verschieben?**  
   Ja, der Speichersynchronisierungsdienst und/oder das Speicherkonto kann in eine andere Ressourcengruppe, ein anderes Abonnement oder einen anderen Azure AD-Mandanten verschoben werden. Wenn der Speichersynchronisierungsdienst oder das Speicherkonto verschoben wurde, müssen Sie der Anwendung „Microsoft.StorageSync“ Zugriff auf das Speicherkonto gewähren (siehe [Sicherstellen, dass die Azure-Dateisynchronisierung über Zugriff auf das Speicherkonto verfügt](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Beim Erstellen des Cloudendpunkts müssen sich der Speichersynchronisierungsdienst und das Speicherkonto im selben Azure AD-Mandanten befinden. Nach der Erstellung des Cloudendpunkts können der Speichersynchronisierungsdienst und das Speicherkonto in verschiedene Azure AD Mandanten verschoben werden.
    
* <a id="afs-ntfs-acls"></a>
  **Werden NTFS-ACLs auf Verzeichnis-/Dateiebene in der Azure-Dateisynchronisierung zusammen mit den in Azure Files gespeicherten Daten beibehalten?**

    Ab dem 24. Februar 2020 werden neue und vorhandene ACLs, die durch die Azure-Dateisynchronisierung gestaffelt sind, im NTFS-Format persistent gespeichert, und ACL-Änderungen, die direkt an der Azure-Dateifreigabe vorgenommen werden, werden mit allen Servern in der Synchronisierungsgruppe synchronisiert. Alle Änderungen an den ACLs, die an Azure Files vorgenommen werden, werden über die Azure-Dateisynchronisierung synchronisiert. Wenn Sie Daten in Azure Files kopieren, stellen Sie sicher, dass Sie ein Kopiertool verwenden, das die erforderliche „Genauigkeit“ unterstützt, um Attribute, Zeitstempel und ACLs in eine Azure-Dateifreigabe zu kopieren – über SMB oder über REST. Wenn Sie Azure-Kopiertools wie AzCopy nutzen, ist es wichtig, die neueste Version zu verwenden. Überprüfen Sie die [Tabelle zu den Dateikopiertools](storage-files-migration-overview.md#file-copy-tools), um einen Überblick über die Azure-Kopiertools zu erhalten und sicherzustellen, dass Sie alle wichtigen Metadaten einer Datei kopieren können.

    Wenn Sie Azure Backup für Ihre von der Datensynchronisierung verwalteten Dateifreigaben aktiviert haben, können Datei-ACLs weiterhin als Teil des Sicherungs- und Wiederherstellungsworkflows wiederhergestellt werden. Dies funktioniert für die gesamte Freigabe oder für einzelne Dateien/Verzeichnisse.

    Wenn Sie Momentaufnahmen als Teil der selbstverwalteten Sicherungslösung für Dateifreigaben verwenden, die von der Dateisynchronisierung verwaltet werden, werden die ACLs möglicherweise nicht ordnungsgemäß als NTFS-ACLs wiederhergestellt, wenn die Momentaufnahmen vor dem 24. Februar 2020 erstellt wurden. In diesem Fall sollten Sie sich an den Azure-Support wenden.
    
## <a name="security-authentication-and-access-control"></a>Sicherheit, Authentifizierung und Zugriffssteuerung
* <a id="ad-support"></a>
**Unterstützt Azure Files die identitätsbasierte Authentifizierung und Zugriffssteuerung?**  
    
    Ja, Azure Files unterstützt identitätsbasierte Authentifizierung und Zugriffssteuerung. Sie können die identitätsbasierte Zugriffssteuerung auf zwei unterschiedliche Weisen nutzen: über lokale Active Directory Domain Services oder über Azure Active Directory Domain Services (Azure AD DS). Lokale Active Directory Domain Services (AD DS) unterstützen die Authentifizierung unter Verwendung von Computern, die entweder lokal oder in Azure in eine AD DS-Domäne eingebunden sind, für den Zugriff auf Azure-Dateifreigaben über SMB. Die Azure AD DS-Authentifizierung per SMB für Azure Files ermöglicht es in die Domäne eingebundenen Azure AD DS-Windows-VMs, mit Azure AD-Anmeldeinformationen auf Freigaben, Verzeichnisse und Dateien zuzugreifen. Weitere Informationen finden Sie unter [Übersicht über die Unterstützung der identitätsbasierten Authentifizierung mit Azure Files für den SMB-Zugriff](storage-files-active-directory-overview.md). 

    Azure Files verfügt noch über zwei weitere Möglichkeiten zur Verwaltung der Zugriffssteuerung:

    - Sie können Shared Access Signatures (SAS) zum Generieren von Token verwenden, die über bestimmte Berechtigungen verfügen und für ein angegebenes Zeitintervall gültig sind. Sie können beispielsweise ein Token mit Lesezugriff auf eine bestimmte Datei generieren, das nach zehn Minuten abläuft. Jeder Benutzer, der während des Gültigkeitszeitraums über das Token verfügt, hat für einen Zeitraum von zehn Minuten Lesezugriff auf die Datei. SAS-Schlüssel (Shared Access Signature) werden nur per REST-API oder in Clientbibliotheken unterstützt. Sie müssen die Azure-Dateifreigabe per SMB einbinden, indem Sie die Speicherkontoschlüssel verwenden.

    - Für die Azure-Dateisynchronisierung werden alle besitzerverwalteten ACLs bzw. DACLs (ob Active Directory-basiert oder lokal) für alle Serverendpunkte beibehalten und repliziert, für die die Synchronisierung durchgeführt wird. 
    
    Eine umfassende Darstellung aller in Azure Storage-Diensten unterstützten Protokolle finden Sie unter [Autorisierung des Zugriffs auf Azure Storage](../common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). 
    
* <a id="encryption-at-rest"></a>
**Wie kann ich sicherstellen, dass meine Azure-Dateifreigabe im ruhenden Zustand verschlüsselt wird?**  

    Ja. Weitere Informationen finden Sie unter [Azure Storage-Verschlüsselung für ruhende Daten](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>
**Wie kann ich mithilfe eines Webbrowsers Zugriff auf eine bestimmte Datei gewähren?**  

    Sie können Shared Access Signatures zum Generieren von Token verwenden, die über bestimmte Berechtigungen verfügen und für ein angegebenes Zeitintervall gültig sind. Beispielsweise können Sie ein Token, das Lesezugriff auf eine bestimmte Datei ermöglicht, für einen festen Zeitraum generieren. Jeder Benutzer, der über diese URL verfügt, kann bis zum Ablauf der Tokengültigkeit direkt über einen beliebigen Webbrowser auf die Datei zugreifen. Sie können einen Shared Access Signature-Schlüssel über eine Benutzeroberfläche, z.B. Storage-Explorer, leicht generieren.

* <a id="file-level-permissions"></a>
**Ist es möglich, Lese- oder Schreibberechtigungen für Ordner der Freigabe anzugeben?**  

    Wenn Sie die Dateifreigabe per SMB einbinden, haben Sie auf Ordnerebene keine Kontrolle über die Berechtigungen. Aber wenn Sie eine Shared Access Signature mit der REST-API oder Clientbibliotheken erstellen, können Sie für Ordner der Freigabe Berechtigungen für Lese- oder Schreibzugriff angeben.

* <a id="ip-restrictions"></a>
**Können IP-Einschränkungen für eine Azure-Dateifreigabe implementiert werden?**  

    Ja. Der Zugriff auf Ihre Azure-Dateifreigabe kann auf Speicherkontoebene eingeschränkt werden. Weitere Informationen finden Sie unter [Konfigurieren von Firewalls und virtuellen Netzwerken in Azure Storage](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>
**Welche Richtlinien zur Datenkonformität werden von Azure Files unterstützt?**  

   Azure Files wird zusätzlich zu der gleichen Speicherarchitektur ausgeführt, die auch in anderen Speicherdiensten in Azure Storage genutzt wird. Für Azure Files werden die gleichen Richtlinien zur Datenkonformität wie in anderen Azure-Speicherdiensten angewendet. Weitere Informationen zur Datenkonformität von Azure Storage finden Sie unter [Azure Storage-Complianceangebote](../common/storage-compliance-offerings.md) und im [Microsoft Trust Center](https://microsoft.com/trustcenter/default.aspx).

* <a id="file-auditing"></a>
**Wie kann ich den Dateizugriff und Änderungen in Azure Files überwachen?**

  Bei den Überwachungsfunktionen für Azure Files haben Sie zwei Optionen:
  - Wenn Benutzer direkt auf die Azure-Dateifreigabe zugreifen, können [Azure Storage-Protokolle (Vorschau)](../blobs/monitor-blob-storage.md?tabs=azure-powershell#analyzing-logs) verwendet werden, um Dateiänderungen und den Benutzerzugriff nachzuverfolgen. Diese Protokolle können für die Problembehandlung genutzt werden, und die Anforderungen werden bestmöglich protokolliert.
  - Wenn Benutzer auf die Azure-Dateifreigabe über eine Windows Server-Instanz zugreifen, auf der der Azure-Dateisynchronisierungs-Agent installiert ist, sollten Sie eine [Überwachungsrichtlinie](/windows/security/threat-protection/auditing/apply-a-basic-audit-policy-on-a-file-or-folder) oder ein Drittanbieterprodukt verwenden, um Dateiänderungen und den Benutzerzugriff auf der Windows Server-Instanz nachzuverfolgen. 
   
### <a name="ad-ds--azure-ad-ds-authentication"></a>AD DS- und Azure AD DS-Authentifizierung
* <a id="ad-support-devices"></a>
**Unterstützt die Azure Files Azure Active Directory Domain Services-Authentifizierung (Azure AD DS) über SMB für Azure Files den SMB-Zugriff mit Azure AD-Anmeldeinformationen von Geräten, die in Azure AD eingebunden oder dafür registriert sind?**

    Nein. Dieses Szenario wird nicht unterstützt.

* <a id="ad-vm-subscription"></a>
**Kann ich auf Azure-Dateifreigaben mit Azure AD-Anmeldeinformationen von einer VM unter einem anderen Abonnement zugreifen?**

    Wenn das Abonnement, unter dem die Dateifreigabe bereitgestellt wurde, demselben Azure AD-Mandanten wie die Azure AD DS-Bereitstellung zugeordnet ist, in die die VM eingebunden ist, können Sie mit denselben Azure AD-Anmeldeinformationen auf Azure-Dateifreigaben zugreifen. Die Einschränkung bezieht sich nicht auf das Abonnement, sondern auf den zugeordneten Azure AD-Mandanten.
    
* <a id="ad-support-subscription"></a>
**Kann ich die Authentifizierung mit Azure AD DS oder lokalen AD DS für Azure-Dateifreigaben mit einem Azure AD-Mandanten aktivieren, der sich vom primären Mandanten der Azure-Dateifreigabe unterscheidet?**

    Nein. Azure Files unterstützen nur die Integration von Azure AD DS oder lokalen AD DS und einem Azure AD-Mandanten, der sich im selben Abonnement wie die Dateifreigabe befindet. Einem Azure AD-Mandanten kann nur ein Abonnement zugeordnet sein. Diese Einschränkung gilt für Authentifizierungsmethoden für Azure AD DS und lokale AD DS. Wenn Sie lokale AD DS für die Authentifizierung verwenden, [müssen die AD DS-Anmeldeinformationen mit dem Azure AD synchronisiert werden](../../active-directory/hybrid/how-to-connect-install-roadmap.md), dem das Speicherkonto zugeordnet ist.

* <a id="ad-linux-vms"></a>
**Unterstützt die Authentifizierung für Azure-Dateifreigaben mit Azure AD DS oder lokalen AD DS virtuelle Linux-Computer?**

    Nein. Die Authentifizierung von virtuellen Linux-Computern wird nicht unterstützt.

* <a id="ad-aad-smb-afs"></a>
**Unterstützen von der Azure-Dateisynchronisierung verwaltete Dateifreigaben die Authentifizierung mit Azure AD DS oder lokalen AD DS?**

    Ja, Sie können die Authentifizierung mit Azure AD DS oder lokalen AD DS für eine von der Azure-Dateisynchronisierung verwaltete Dateifreigabe aktivieren. Änderungen an den NTFS-ACLs der Verzeichnisse/Dateien auf lokalen Dateiservern werden in Azure Files gestaffelt und umgekehrt.

* <a id="ad-aad-smb-files"></a>
**Wie kann ich überprüfen, ob die AD DS-Authentifizierung für mein Speicherkonto aktiviert wurde, und die Domäneninformationen abrufen?**

    Anweisungen dazu finden Sie [hier](./storage-files-identity-ad-ds-enable.md#confirm-the-feature-is-enabled).

* <a id=""></a>
**Unterstützt die Azure AD-Authentifizierung für Azure Files virtuelle Linux-Computer?**

    Nein. Die Authentifizierung von virtuellen Linux-Computern wird nicht unterstützt.

* <a id="ad-multiple-forest"></a>
**Unterstützt die Authentifizierung mit lokalen AD DS für Azure-Dateifreigaben die Integration in eine AD DS-Umgebung mit mehreren Gesamtstrukturen?**    

    Die Azure Files-Authentifizierung mit lokalen AD DS ist nur in die Gesamtstruktur des Domänendiensts integriert, bei dem das Speicherkonto registriert ist. Zur Unterstützung der Authentifizierung von einer anderen Gesamtstruktur muss die Gesamtstruktur-Vertrauensstellung in Ihrer Umgebung ordnungsgemäß konfiguriert sein. Die Registrierung von Azure Files bei AD DS entspricht größtenteils der Registrierung eines regulären Dateiservers. Dabei wird in AD DS eine Identität (Computer- oder Dienstanmeldekonto) für die Authentifizierung erstellt. Der einzige Unterschied besteht darin, dass der registrierte SPN des Speicherkontos auf „file.core.windows.net“ endet, was nicht mit dem Domänensuffix übereinstimmt. Wenden Sie sich an Ihren Domänenadministrator, um festzustellen, ob ein Update Ihrer Suffix-Routingrichtlinie erforderlich ist, um die Authentifizierung mit mehreren Gesamtstrukturen aufgrund des unterschiedlichen Domänensuffixes zu aktivieren. Unten finden Sie ein Beispiel für das Konfigurieren der Suffix-Routingrichtlinie.
    
    Beispiel: Wenn Benutzer in der Domäne der Gesamtstruktur A eine Dateifreigabe mit dem Speicherkonto erreichen möchten, das für eine Domäne in der Gesamtstruktur B registriert ist, funktioniert dies nicht automatisch, da der Dienstprinzipal des Speicherkontos kein Suffix hat, das mit dem Suffix einer Domäne in Gesamtstruktur A übereinstimmt. Sie können dieses Problem beheben, indem Sie manuell eine Suffix-Routingregel von Gesamtstruktur A nach Gesamtstruktur B für das benutzerdefinierte Suffix „file.core.windows.net“ konfigurieren.
    Zunächst müssen Sie in Gesamtstruktur B ein neues benutzerdefiniertes Suffix hinzufügen. Stellen Sie sicher, dass Sie über die entsprechenden Administratorberechtigungen zum Ändern der Konfiguration verfügen, und führen Sie dann die folgenden Schritte aus:   
    1. Melden Sie sich auf einem Computer in der Domäne der Gesamtstruktur B an.
    2.  Öffnen Sie die Konsole „Active Directory-Domänen und -Vertrauensstellungen“.
    3.  Klicken Sie mit der rechten Maustaste auf „Active Directory-Domänen und -Vertrauensstellungen“.
    4.  Klicken Sie auf „Eigenschaften“.
    5.  Klicken Sie auf „Hinzufügen“.
    6.  Fügen Sie „file.core.windows.net“ als UPN-Suffix hinzu.
    7.  Klicken Sie auf „Anwenden“ und dann auf „OK“, um den Assistenten zu schließen.
    
    Fügen Sie als Nächstes die Suffix-Routingregel für Gesamtstruktur A hinzu, damit eine Weiterleitung zu Gesamtstruktur B erfolgt.
    1.  Melden Sie sich auf einem Computer in der Domäne der Gesamtstruktur A an.
    2.  Öffnen Sie die Konsole „Active Directory-Domänen und -Vertrauensstellungen“.
    3.  Klicken Sie mit der rechten Maustaste auf die Domäne, die auf die Dateifreigabe zugreifen soll. Klicken Sie dann auf die Registerkarte „Vertrauensstellungen“, und wählen Sie für ausgehende Vertrauensstellungen eine Domäne der Gesamtstruktur B aus. Wenn Sie noch keine Vertrauensstellung zwischen den beiden Gesamtstrukturen konfiguriert haben, müssen Sie diese zunächst einrichten.
    4.  Klicken Sie auf „Eigenschaften“ und dann auf „Namensuffixrouting“.
    5.  Überprüfen Sie, ob das Suffix „*.file.core.windows.net“ angezeigt wird. Klicken Sie andernfalls auf „Aktualisieren“.
    6.  Wählen Sie „*.file.core.windows.net“ aus, und klicken Sie dann auf „Aktivieren“ und „Anwenden“.

* <a id=""></a>
**Welche Regionen sind für die AD DS-Authentifizierung von Azure Files verfügbar?**

    Ausführliche Informationen finden Sie unter [Regionale AD DS-Verfügbarkeit](storage-files-identity-auth-active-directory-enable.md#regional-availability).
    
* <a id="ad-aad-smb-afs"></a>
**Kann ich die Azure Files Active Directory-Authentifizierung (AD) für Dateifreigaben nutzen, die durch Azure-Dateisynchronisierung verwaltet werden?**

    Ja, Sie können die AD-Authentifizierung für eine von der Azure-Dateisynchronisierung verwaltete Dateifreigabe aktivieren. Änderungen an den NTFS-ACLs der Verzeichnisse/Dateien auf lokalen Dateiservern werden in Azure Files gestaffelt und umgekehrt.

* <a id="ad-aad-smb-files"></a>
**Macht es für die Darstellung meines Speicherkontos in AD einen Unterschied, ob ich ein Computerkonto oder ein Dienstanmeldekonto erstelle?**

    Ob Sie ein [Computerkonto](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (Standard) oder ein [Dienstanmeldekonto](/windows/win32/ad/about-service-logon-accounts) erstellen, hat keine Auswirkung darauf, wie die Authentifizierung mit Azure Files erfolgt. Sie können festlegen, wie ein Speicherkonto als Identität in Ihrer AD-Umgebung dargestellt werden soll. Der im Join-AzStorageAccountForAuth-Cmdlet standardmäßig festgelegte DomainAccountType ist Computerkonto. Das in Ihrer AD-Umgebung konfigurierte Kennwort für den Ablauf des Kennworts kann jedoch für das Computer- oder Dienstanmeldekonto abweichen. Berücksichtigen Sie dies beim [Aktualisieren des Kennworts Ihrer Speicherkontoidentität in AD](./storage-files-identity-ad-ds-update-password.md).
 
* <a id="ad-support-rest-apis"></a>
**Sind REST-APIs zur Unterstützung von Get/Set/Copy-Vorgängen für Windows-ACLs auf Verzeichnis-/Dateiebene vorhanden?**

    Ja, wir unterstützen REST-APIs, die NTFS-ACLs für Verzeichnisse oder Dateien bei Verwendung der REST-API [2019-07-07](/rest/api/storageservices/versioning-for-the-azure-storage-services#version-2019-07-07) (oder höher) erfassen, festlegen oder kopieren. Außerdem wird das Beibehalten von Windows-ACLs in REST-basierten Tools unterstützt: [AzCopy v10.4+](https://github.com/Azure/azure-storage-azcopy/releases).

## <a name="network-file-system"></a>Network File System

* <a id="when-to-use-nfs"></a>
**Wann sollte ich Azure Files NFS verwenden?**

    Informationen hierzu finden Sie unter [NFS-Freigaben (Vorschau)](storage-files-compare-protocols.md#nfs-shares-preview).

* <a id="backup-nfs-data"></a>
**Wie sichere ich Daten, die in NFS-Freigaben gespeichert sind?**

    Die Sicherung von Daten in NFS-Freigaben kann entweder mit bekannten Tools wie rsync oder mit Produkten eines unserer Partnerunternehmen zur Datensicherung orchestriert werden. Mehrere dieser Partner zur Datensicherung, wie etwa [Commvault](https://documentation.commvault.com/commvault/v11/article?p=92634.htm), [Veeam](https://www.veeam.com/blog/?p=123438) und [Veritas](https://players.brightcove.net/4396107486001/default_default/index.html?videoId=6189967101001), waren Teil der ersten Vorschauversion und haben ihre Lösungen auf SMB 3.0 sowie NFS 4.1 für Azure Files erweitert.

* <a id="migrate-nfs-data"></a>
**Kann ich vorhandene Daten zu einer NFS-Freigabe migrieren?**

    Innerhalb derselben Region können Sie Daten mit Standardtools wie scp, rsync oder SSHFS verschieben. Da auf Azure Files NFS von mehreren Compute-Instanzen aus gleichzeitig zugegriffen werden kann, können Sie über parallele Uploads die Kopiergeschwindigkeit erhöhen. Wenn Sie Daten von außerhalb einer Region migrieren möchten, verwenden Sie zur Einbindung in Ihr Dateisystem aus Ihrem lokalen Rechenzentrum eine VPN- oder ExpressRoute-Verbindung.

## <a name="on-premises-access"></a>Lokaler Zugriff

* <a id="port-445-blocked"></a>
**Mein ISP oder meine IT-Abteilung blockiert Port 445, wodurch das Bereitstellen von Azure Files fehlschlägt. Wie soll ich vorgehen?**

    Informationen zu verschiedenen Möglichkeiten, den blockierten Port 445 zu umgehen, finden Sie [hier](./storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked). Azure Files erlaubt von außerhalb der Region oder des Rechenzentrums nur Verbindungen mit SMB 3.0 (mit Verschlüsselungsunterstützung). In das Protokoll SMB 3.0 wurden viele Sicherheitsfunktionen integriert, darunter die Kanalverschlüsselung, die sich sehr sicher über das Internet nutzen lässt. Es ist jedoch möglich, dass Port 445 aufgrund von Schwachstellen in niedrigeren SMB-Versionen blockiert wurde. Im Idealfall sollte der Port nur für SMB 1.0-Datenverkehr gesperrt werden und SMB 1.0 auf allen Clients deaktiviert sein.

* <a id="expressroute-not-required"></a>
**Muss ich Azure ExpressRoute verwenden, wenn ich eine Verbindung mit Azure Files herstellen oder die Azure-Dateisynchronisierung lokal verwenden möchte?**  

    Nein. ExpressRoute ist für den Zugriff auf eine Azure-Dateifreigabe nicht erforderlich. Wenn Sie eine Azure-Dateifreigabe direkt lokal bereitstellen, muss lediglich Port 445 (TCP ausgehend) für den Internetzugriff geöffnet sein (dies ist der Port, über den SMB kommuniziert). Wenn Sie die Azure-Dateisynchronisierung verwenden, ist lediglich Port 443 (TCP ausgehend) für den HTTPS-Zugriff erforderlich (SMB ist nicht erforderlich). Sie *können* ExpressRoute aber mit beiden Zugriffsoptionen verwenden.

* <a id="mount-locally"></a>
**Wie kann ich eine Azure-Dateifreigabe auf meinem lokalen Computer bereitstellen?**  

    Sie können die Dateifreigabe über das SMB-Protokoll bereitstellen, sofern Port 445 (TCP ausgehend) geöffnet ist und Ihr Client das SMB 3.0-Protokoll unterstützt (beispielsweise unter Windows 10 oder Windows Server 2016 der Fall). Wenn Port 445 durch eine Richtlinie Ihrer Organisation oder Ihres ISP blockiert ist, können Sie über die Azure-Dateisynchronisierung auf Ihre Azure-Dateifreigabe zugreifen.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**Wie sichere ich meine Azure-Dateifreigabe?**  
    Sie können regelmäßige [Freigabemomentaufnahmen](storage-snapshots-files.md) zum Schutz vor einer versehentlichen Löschung verwenden. Außerdem können Sie AzCopy, Robocopy oder ein Sicherungstool eines Drittanbieters nutzen, das zum Sichern einer eingebundenen Dateifreigabe geeignet ist. Azure Backup ermöglicht die Sicherung von Azure Files. Erfahren Sie mehr über das [Sichern von Azure-Dateifreigaben mit Azure Backup](../../backup/backup-afs.md).

## <a name="share-snapshots"></a>Freigabemomentaufnahmen

### <a name="share-snapshots-general"></a>Freigabemomentaufnahmen: Allgemein
* <a id="what-are-snaphots"></a>
**Was sind Dateifreigaben-Momentaufnahmen?**  
    Sie können Momentaufnahmen von Azure-Dateifreigaben verwenden, um eine schreibgeschützte Version Ihrer Dateifreigaben zu erstellen. Außerdem können Sie Azure Files nutzen, um eine frühere Version Ihres Inhalts zurück auf dieselbe Freigabe, an einen anderen Speicherort in Azure oder zur weiteren Bearbeitung an einen lokalen Speicherort zu kopieren. Weitere Informationen zu Freigabemomentaufnahmen finden Sie unter [Freigabemomentaufnahme – Übersicht](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>
**Wo werden meine Freigabemomentaufnahmen gespeichert?**  
    Freigabemomentaufnahmen werden im gleichen Speicherkonto gespeichert wie die Dateifreigabe.

* <a id="snapshot-consistency"></a>
**Sind Freigabemomentaufnahmen anwendungskonsistent?**  
    Nein. Freigabemomentaufnahmen sind nicht anwendungskonsistent. Benutzer müssen vor dem Erstellen von Freigabemomentaufnahmen die Schreibvorgänge von der Anwendung in die Freigabe leeren.

* <a id="snapshot-limits"></a>
**Gibt es Beschränkungen hinsichtlich der Anzahl von Freigabemomentaufnahmen, die verwendet werden können?**  
    Ja. In Azure Files können maximal 200 Freigabemomentaufnahmen verwendet werden. Freigabemomentaufnahmen werden nicht zum Kontingent für die Dateifreigabe gezählt, sodass es für den von allen Freigabemomentaufnahmen genutzten Gesamtspeicherplatz keine Begrenzung pro Freigabe gibt. Die Beschränkungen für Speicherkonten gelten weiterhin. Wenn die Zahl von 200 Freigabemomentaufnahmen erreicht ist, müssen Sie ältere Momentaufnahmen löschen, um neue Freigabemomentaufnahmen erstellen zu können.

* <a id="snapshot-cost"></a>
**Wie viel kosten Freigabemomentaufnahmen?**  
    Für Momentaufnahmen gelten die standardmäßigen Transaktions- und Speicherkosten. Momentaufnahmen sind in der Regel inkrementell. Bei der Basismomentaufnahme handelt es sich um die Freigabe selbst. Alle nachfolgenden Momentaufnahmen sind inkrementell und speichern lediglich die Differenz zur vorherigen Momentaufnahme. Dies bedeutet, dass die in der Rechnung angezeigten Deltaänderungen minimal ausfallen, wenn Ihre Workloadänderungen geringfügig sind. Preisinformationen finden Sie auf der [Preisgestaltungsseite](https://azure.microsoft.com/pricing/details/storage/files/) für Azure Files im Standard-Tarif. Heutzutage wird die von Freigabemomentaufnahmen genutzte Größe durch das Vergleichen der abgerechneten Kapazität mit der verwendeten Kapazität ermittelt. Wir arbeiten derzeit an Tools, um die Berichterstellung zu verbessern.

* <a id="ntfs-acls-snaphsots"></a>
**Werden NTFS-ACLs in Verzeichnissen und Dateien in Freigabemomentaufnahmen beibehalten?**  
    NTFS-ACLs in Verzeichnissen und Dateien werden in Freigabemomentaufnahmen beibehalten.

### <a name="create-share-snapshots"></a>Erstellen von Freigabemomentaufnahmen
* <a id="file-snaphsots"></a>
**Kann ich Freigabemomentaufnahmen von den einzelnen Dateien erstellen?**  
    Freigabemomentaufnahmen werden auf der Dateifreigabeebene erstellt. Sie können einzelne Dateien über die Dateifreigabemomentaufnahme wiederherstellen, aber das Erstellen von Freigabemomentaufnahmen auf Dateiebene ist nicht möglich. Wenn Sie aber eine Freigabemomentaufnahme auf der Freigabeebene erstellt haben und Freigabemomentaufnahmen, bei denen sich eine bestimmte Datei geändert hat, auflisten möchten, können Sie hierfür die Option **Vorherige Versionen** auf einer bereitgestellten Windows-Freigabe nutzen. 
    
    Wenden Sie sich unter [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) an uns, falls Sie ein Feature für Dateimomentaufnahmen benötigen.

* <a id="encrypted-snapshots"></a>
**Kann ich Freigabemomentaufnahmen von verschlüsselten Dateifreigaben erstellen?**  
    Sie können eine Freigabemomentaufnahme von Azure-Dateifreigaben erstellen, bei der die Verschlüsselung ruhender Daten aktiviert ist. Sie können Dateien von einer Freigabemomentaufnahme in einer verschlüsselten Dateifreigabe wiederherstellen. Wenn Ihre Freigabe verschlüsselt ist, wird Ihre Freigabemomentaufnahme ebenfalls verschlüsselt.

* <a id="geo-redundant-snaphsots"></a>
**Sind meine Freigabemomentaufnahmen georedundant?**  
    Freigabemomentaufnahmen weisen dieselbe Redundanz wie die Azure-Dateifreigabe auf, für die sie erstellt wurden. Wenn Sie für Ihr Konto einen georedundanten Speicher ausgewählt haben, wird Ihre Freigabemomentaufnahme ebenfalls redundant in der gekoppelten Region gespeichert.

### <a name="manage-share-snapshots"></a>Verwalten von Freigabemomentaufnahmen
* <a id="browse-snapshots-linux"></a>
**Kann ich meine Freigabemomentaufnahmen unter Linux durchsuchen?**  
    Sie können mit der Azure CLI unter Linux Freigabemomentaufnahmen erstellen, auflisten, durchsuchen und wiederherstellen.

* <a id="copy-snapshots-to-other-storage-account"></a>
**Kann ich die Freigabemomentaufnahmen in ein anderes Speicherkonto kopieren?**  
    Sie können Dateien von Freigabemomentaufnahmen an einen anderen Speicherort kopieren, aber nicht die Freigabemomentaufnahmen selbst.

### <a name="restore-data-from-share-snapshots"></a>Wiederherstellen von Daten aus Freigabemomentaufnahmen
* <a id="promote-share-snapshot"></a>
**Kann ich eine Freigabemomentaufnahme auf die Basisfreigabe höher stufen?**  
    Sie können Daten aus einer Freigabemomentaufnahme an ein anderes Ziel kopieren. Eine Freigabemomentaufnahme kann nicht auf die Basisfreigabe höher gestuft werden.

* <a id="restore-snapshotted-file-to-other-share"></a>
**Kann ich Daten aus meiner Freigabemomentaufnahme in einem anderen Speicherkonto wiederherstellen?**  
    Ja. Dateien einer Freigabemomentaufnahme können an den ursprünglichen Speicherort oder einen anderen Speicherort kopiert werden, der entweder dasselbe Speicherkonto oder ein anderes Speicherkonto in derselben oder in anderen Regionen enthält. Außerdem können Sie Dateien an einen lokalen Speicherort oder in eine beliebige andere Cloud kopieren.    
  
### <a name="clean-up-share-snapshots"></a>Bereinigen von Freigabemomentaufnahmen
* <a id="delete-share-keep-snapshots"></a>
**Kann ich meine Freigabe löschen, Freigabemomentaufnahmen aber nicht?**  
    Wenn Sie auf Ihrer Freigabe über aktive Freigabemomentaufnahmen verfügen, können Sie die Freigabe nicht löschen. Sie können eine API verwenden, um Freigabemomentaufnahmen zusammen mit der Freigabe zu löschen. Außerdem können Sie die Freigabemomentaufnahmen und die Freigabe im Azure-Portal löschen.

* <a id="delete-share-with-snapshots"></a>
**Was geschieht mit meinen Freigabemomentaufnahmen, wenn ich mein Speicherkonto lösche?**  
    Wenn Sie Ihr Speicherkonto löschen, werden die Freigabemomentaufnahmen ebenfalls gelöscht.

## <a name="billing-and-pricing"></a>Abrechnung und Preise
* <a id="vm-file-share-network-traffic"></a>
**Zählt der Netzwerkdatenverkehr zwischen einer Azure-VM und einer Dateifreigabe als externe Bandbreite, die für das Abonnement berechnet wird?**  
    Wenn sich die Dateifreigabe und die VM in derselben Azure-Region befinden, fallen für den Datenverkehr zwischen der Dateifreigabe und der VM keine zusätzlichen Kosten an. Wenn sich die Dateifreigabe und die VM in verschiedenen Regionen befinden, wird der dazwischen ausgetauschte Datenverkehr als externe Bandbreite berechnet.

* <a id="share-snapshot-price"></a>
**Wie viel kosten Freigabemomentaufnahmen?**  
     Während der Vorschauphase fallen keine Gebühren für die Kapazität von Freigabemomentaufnahmen an. Für regulären ausgehenden Speicher und Transaktionen fallen die üblichen Kosten an. Wenn die allgemeine Verfügbarkeit erreicht ist, werden für Abonnements sowohl die Kapazität als auch Transaktionen für Freigabemomentaufnahmen berechnet.
     
     Freigabemomentaufnahmen sind in der Regel inkrementell. Bei der Basisfreigabemomentaufnahme handelt es sich um die Freigabe selbst. Alle nachfolgenden Freigabemomentaufnahmen sind inkrementell und enthalten nur die Differenzdaten gegenüber der vorherigen Freigabemomentaufnahme. Ihnen wird nur der geänderte Inhalt in Rechnung gestellt. Wenn Sie über eine Freigabe mit 100 GiB an Daten verfügen, aber nur 5 GiB nach Ihrer letzten Freigabemomentaufnahme geändert wurden, belegt die Freigabemomentaufnahme nur zusätzliche 5 GiB, und Ihnen werden 105 GiB berechnet. Weitere Informationen zu Gebühren für Transaktionen und Standardausgangsgebühren finden Sie auf der Seite [Azure Files – Preise](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="scale-and-performance"></a>Skalierung und Leistung
* <a id="files-scale-limits"></a>
**Welche Skalierungsgrenzwerte gelten für Azure Files?**  
    Informationen zu Skalierbarkeits- und Leistungszielen für Azure Files finden Sie unter [Skalierbarkeits- und Leistungsziele für Azure Files](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>
**Welche Größen sind für Azure-Dateifreigaben verfügbar?**  
    Azure-Dateifreigaben (Premium und Standard) können bis zu 100TiB hochskaliert werden. Im Abschnitt [Onboarding für größere Dateifreigaben (Standard-Tarif)](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) des Planungshandbuchs finden Sie Onboardinganweisungen zu größeren Dateifreigaben für den Standard-Tarif.

* <a id="lfs-performance-impact"></a>
**Wirkt sich eine Erweiterung meines Dateifreigabekontingents auf meine Workloads oder die Azure-Dateisynchronisierung aus?**
    
    Nein. Eine Kontingenterweiterung hat keine Auswirkungen auf Ihre Workloads oder die Azure-Dateisynchronisierung.

* <a id="open-handles-quota"></a>
**Wie viele Clients können gleichzeitig auf dieselbe Datei zugreifen?**    
    Für eine einzelne Datei gilt ein Kontingent von 2.000 geöffneten Handles. Wenn Sie über 2.000 geöffnete Handles verfügen, wird eine Fehlermeldung mit dem Hinweis angezeigt, dass das Kontingent erreicht ist.

* <a id="zip-slow-performance"></a>
**Die Leistung verlangsamt sich, wenn ich in Azure Files Dateien entzippe. Wie soll ich vorgehen?**  
    Zum Übertragen einer großen Zahl von Dateien nach Azure Files empfehlen wir Ihnen die Verwendung von AzCopy (für Windows, in der Vorschauphase für Linux und UNIX) oder Azure PowerShell. Diese Tools wurden für die Netzwerkübertragung optimiert.

* <a id="slow-perf-windows-81-2012r2"></a>
**Warum verschlechtert sich die Leistung, nachdem ich meine Azure-Dateifreigabe unter Windows Server 2012 R2 oder Windows 8.1 bereitgestellt habe?**  
    Es gibt ein bekanntes Problem, das beim Bereitstellen einer Azure-Dateifreigabe unter Windows Server 2012 R2 und Windows 8.1 auftritt. Für das Problem wurde im April 2014 ein kumulatives Update für Windows 8.1 und Windows Server 2012 R2 als Patch veröffentlicht. Stellen Sie zur Erzielung einer optimalen Leistung sicher, dass dieser Patch für alle Instanzen von Windows Server 2012 R2 und Windows 8.1 angewendet wurde. (Sie sollten Windows-Patches immer per Windows Update erhalten.) Weitere Informationen finden Sie im zugehörigen Microsoft Knowledge Base-Artikel [Niedrige Leistung beim Zugriff auf Azure Files von Windows 8.1 oder Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Features und Interoperabilität mit anderen Diensten
* <a id="cluster-witness"></a>
**Kann ich meine Azure-Dateifreigabe als *Dateifreigabenzeugen* für meinen Windows Server-Failovercluster verwenden?**  
    Diese Konfiguration wird für eine Azure-Dateifreigabe derzeit nicht unterstützt. Weitere Informationen zum Einrichten dieser Funktion für den Azure Blob Storage finden Sie unter [Bereitstellen eines Cloudzeugen für einen Failovercluster](/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>
**Kann ich eine Azure-Dateifreigabe in einer Azure-Containerinstanz bereitstellen?**  
    Ja. Azure-Dateifreigaben sind eine gute Möglichkeit, um Informationen über die Lebensdauer einer Containerinstanz hinaus dauerhaft zu speichern. Weitere Informationen finden Sie unter [Einbinden einer Azure-Dateifreigabe in Azure Container Instances](../../container-instances/container-instances-volume-azure-files.md).

* <a id="rest-rename"></a>
**Steht in der REST-API ein Umbenennungsvorgang zur Verfügung?**  
    Derzeit leider nicht.

* <a id="nested-shares"></a>
**Kann ich geschachtelte Freigaben einrichten? Anders ausgedrückt: Kann eine Freigabe unter einer Freigabe angeordnet werden?**  
    Nein. Die Dateifreigabe *ist* der virtuelle Treiber, den Sie bereitstellen können. Geschachtelte Freigaben werden nicht unterstützt.

* <a id="ibm-mq"></a>
**Wie kann ich Azure Files mit IBM MQ nutzen?**  
    IBM hat ein Dokument mit einer Beschreibung veröffentlicht, wie IBM MQ-Kunden Azure Files mit dem IBM-Dienst konfigurieren können. Weitere Informationen finden Sie unter [How to setup IBM MQ Multi instance queue manager with Microsoft Azure File Service](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service)(Gewusst wie: Einrichten eines IBM MQ-Warteschlangen-Managers für mehrere Instanzen mit dem Microsoft Azure Files-Dienst).

## <a name="see-also"></a>Weitere Informationen
* [Behandeln von Azure Files-Problemen unter Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Behandeln von Azure Files-Problemen unter Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Problembehandlung für die Azure-Dateisynchronisierung](storage-sync-files-troubleshoot.md)