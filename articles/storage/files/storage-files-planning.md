---
title: Planung für eine Azure Files-Bereitstellung | Microsoft-Dokumentation
description: Hier finden Sie grundlegende Informationen zur Planung einer Azure Files-Bereitstellung. Sie können entweder eine Azure-Dateifreigabe direkt einbinden oder Azure-Dateifreigaben mit Azure-Dateisynchronisierung lokal zwischenspeichern.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 98cc72f85499481ba3841ce82fe307740d5e9fab
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96842704"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planung für eine Azure Files-Bereitstellung
[Azure Files](storage-files-introduction.md) kann auf zwei Arten bereitgestellt werden: durch direktes Einbinden der serverlosen Azure-Dateifreigaben oder durch lokales Zwischenspeichern von Azure-Dateifreigaben mithilfe von Azure-Dateisynchronisierung. Welche Bereitstellungsoption Sie auswählen, ändert die Aspekte, die Sie beim Planen der Bereitstellung berücksichtigen müssen. 

- **Direktes Einbinden einer Azure-Dateifreigabe**: Da Azure Files entweder Zugriff auf den Server Message Block (SMB) oder das Network File System (NFS) bereitstellt, können Sie Azure-Dateifreigaben lokal oder in der Cloud mithilfe der SMB- oder NFS-Standardclients einbinden, die in Ihrem Betriebssystem verfügbar sind. Da Azure-Dateifreigaben serverlos sind, erfordert die Bereitstellung für Produktionsszenarien keine Verwaltung eines Dateiservers oder NAS-Geräts. Dies bedeutet, dass Sie keine Softwarepatches anwenden oder physische Datenträger austauschen müssen. 

- **Lokales Zwischenspeichern von Azure-Dateifreigaben mit Azure-Dateisynchronisierung**: Die Azure-Dateisynchronisierung ermöglicht das Zentralisieren der Dateifreigaben Ihrer Organisation in Azure Files, ohne auf die Flexibilität, Leistung und Kompatibilität eines lokalen Dateiservers verzichten zu müssen. Die Azure-Dateisynchronisierung transformiert Ihre lokalen Windows Server-Computer in einen schnellen Cache für Ihre Azure-SMB-Dateifreigabe. 

Dieser Artikel befasst sich hauptsächlich mit Bereitstellungsüberlegungen zum Bereitstellen einer Azure-Dateifreigabe, die direkt von einem lokalen oder cloudbasierten Client eingebunden werden soll. Weitere Informationen zum Planen einer Azure-Dateisynchronisierungsbereitstellung finden Sie unter [Planung für die Bereitstellung einer Azure-Dateisynchronisierung](storage-sync-files-planning.md).

## <a name="available-protocols"></a>Verfügbare Protokolle

Azure Files bietet zwei Protokolle, die beim Einbinden von Dateifreigaben verwendet werden können: den SMB und das NFS. Ausführliche Informationen zu diesen Protokollen finden Sie unter [Azure-Dateifreigabeprotokolle](storage-files-compare-protocols.md).

> [!IMPORTANT]
> Ein Großteil des Inhalts dieses Artikels gilt nur für SMB-Freigaben. Wenn etwas für NFS-Freigaben gilt, wird ausdrücklich darauf hingewiesen.

## <a name="management-concepts"></a>Verwaltungskonzepte
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

Beim Bereitstellen von Azure-Dateifreigaben in Speicherkonten wird Folgendes empfohlen:

- Ausschließliches Bereitstellen von Azure-Dateifreigaben in Speicherkonten mit anderen Azure-Dateifreigaben. Auch wenn GPv2-Speicherkonten Speicherkonten mit verschiedenen Zwecken ermöglichen, kann das Mischen von Ressourcen das spätere Beheben von Leistungsproblemen erschweren, da für Speicherressourcen wie z. B. Azure-Dateifreigaben und Blobcontainer die Grenzen des Speicherkontos gemeinsam sind. 

- Beachten Sie beim Bereitstellen von Azure-Dateifreigaben die IOPS-Einschränkungen eines Speicherkontos. Im Idealfall würden Sie Dateifreigaben Speicherkonten im Verhältnis 1:1 zuordnen. Dies ist jedoch ggf. aufgrund verschiedener Beschränkungen und Einschränkungen in Ihrer Organisation oder in Azure nicht immer möglich. Wenn es nicht möglich ist, dass nur eine Dateifreigabe in einem Speicherkonto bereitgestellt wird, sollten Sie berücksichtigen, welche Freigaben sehr aktiv sein werden und welche Freigaben weniger aktiv sind, um sicherzustellen, dass die aktivsten Dateifreigaben nicht zusammen im gleichen Speicherkonto gruppiert werden.

- Stellen Sie nur GPv2- und FileStorage-Konten bereit, und aktualisieren Sie GPv1- und klassische Speicherkonten, wenn diese in Ihrer Umgebung vorhanden sind. 

## <a name="identity"></a>Identity
Für den Zugriff auf eine Azure-Dateifreigabe muss der Benutzer der Dateifreigabe authentifiziert sein und über Autorisierung für den Zugriff auf die Freigabe verfügen. Dies erfolgt basierend auf der Identität des Benutzers, der auf die Dateifreigabe zugreift. Azure Files ist in drei Hauptidentitätsanbieter integriert:
- **Lokale Active Directory Domain Services (AD DS oder lokale AD DS)** : Azure Storage-Konten können über Domänenbeitritt wie ein Windows Server-Dateiserver oder ein NAS-Gerät mit einem kundeneigenen Active Directory Domain Services verbunden werden. Sie können einen Domänencontroller lokal, auf einer Azure-VM oder sogar als VM bei einem anderen Cloudanbieter bereitstellen. Azure Files ist unabhängig vom Speicherort des Domänencontrollers. Sobald ein Speicherkonto einer Domäne beigetreten ist, kann der Endbenutzer eine Dateifreigabe mit dem Benutzerkonto einbinden, mit dem er sich bei seinem PC angemeldet hat. Bei der AD-basierten Authentifizierung wird das Kerberos-Authentifizierungsprotokoll verwendet.
- **Azure Active Directory Domain Services (Azure AD DS)** : Azure AD DS bietet einen von Microsoft verwalteten Domänencontroller, der für Azure-Ressourcen verwendet werden kann. Der Domänenbeitritt Ihres Speicherkontos zu Azure AD DS bietet ähnliche Vorteile wie der Domänenbeitritt zu einem Active Directory im Besitz eines Kunden. Diese Bereitstellungsoption ist besonders nützlich für Lift-and-Shift-Anwendungsszenarien, die AD-basierte Berechtigungen erfordern. Da Azure AD DS AD-basierte Authentifizierung bereitstellt, verwendet diese Option auch das Kerberos-Authentifizierungsprotokoll.
- **Azure-Speicherkontoschlüssel**: Azure-Dateifreigaben können auch mit einem Azure-Speicherkontoschlüssel bereitgestellt werden. Bei einer solchen Einbindung einer Dateifreigabe wird der Speicherkontoname als Benutzername und der Speicherkontoschlüssel als Kennwort verwendet. Die Verwendung des Speicherkontoschlüssels zum Einbinden der Azure-Dateifreigabe ist praktisch ein Administratorvorgang, da die eingebundene Dateifreigabe über vollständige Berechtigungen für alle Dateien und Ordner auf der Freigabe verfügt, auch wenn diese über ACLs verfügen. Wenn Sie den Speicherkontoschlüssel zum Einbinden über SMB verwenden, wird das NTLMv2-Authentifizierungsprotokoll verwendet.

Für Kunden, die von lokalen Dateiservern migrieren oder neue Dateifreigaben in Azure Files erstellen, die sich wie Windows-Dateiserver oder NAS-Geräte verhalten sollen, ist der Domänenbeitritt des Speicherkontos zum **Active Directory im Besitz des Kunden** die empfohlene Option. Weitere Informationen zum Domänenbeitritt Ihres Speicherkontos zu einem Active Directory im Besitz des Kunden finden Sie unter [Azure Files Active Directory: Übersicht](storage-files-active-directory-overview.md).

Wenn Sie beabsichtigen, den Speicherkontoschlüssel für den Zugriff auf Ihre Azure-Dateifreigaben zu verwenden, empfehlen wir die Verwendung von Dienstendpunkten, wie im Abschnitt [Netzwerk](#networking) beschrieben.

## <a name="networking"></a>Netzwerk
Auf Azure-Dateifreigaben kann über den öffentlichen Endpunkt des Speicherkontos von überall aus zugegriffen werden. Dies bedeutet, dass authentifizierte Anforderungen (etwa Anforderungen, die durch die Anmeldeidentität eines Benutzer autorisiert wurden) auf sichere Weise verwendet werden können – ganz gleich, ob ihr Ursprung innerhalb oder außerhalb von Azure liegt. In vielen Kundenumgebungen ist die Einbindung der Azure-Dateifreigabe in die lokale Arbeitsstation zunächst nicht erfolgreich, obwohl Einbindungen von virtuellen Azure-Computern problemlos funktionieren. Dies liegt daran, dass viele Organisationen und Internetdienstanbieter (Internet Service Providers, ISPs) Port 445 blockieren, der von SMB für die Kommunikation verwendet wird. Um eine Zusammenfassung der ISPs anzuzeigen, die den Zugang von Port 445 aus zulassen oder verweigern, gehen Sie auf [TechNet](https://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx).

Zum Aufheben der Blockierung des Zugriffs auf Ihre Azure-Dateifreigabe stehen Ihnen zwei Hauptoptionen zur Verfügung:

- Entsperren von Port 445 für das lokale Netzwerk Ihrer Organisation. Auf Azure-Dateifreigaben kann nur extern über den öffentlichen Endpunkt zugegriffen werden, indem internetsichere Protokolle wie SMB 3.0 und die FileREST-API verwendet werden. Dies ist die einfachste Möglichkeit, vom lokalen Standort aus auf Ihre Azure-Dateifreigabe zuzugreifen, da keine erweiterte Netzwerkkonfiguration erforderlich ist, die über das Ändern von Regeln für den Port für ausgehenden Datenverkehr Ihres Unternehmens hinausgeht. Es wird jedoch empfohlen, Legacy- und veraltete Versionen des SMB-Protokolls zu entfernen (also SMB 1.0). Weitere Informationen hierzu finden Sie unter [Sichern von Windows/Windows Server](storage-how-to-use-files-windows.md#securing-windowswindows-server) und [Sichern von Linux](storage-how-to-use-files-linux.md#securing-linux).

- Zugreifen auf Azure-Dateifreigaben über eine ExpressRoute- oder VPN-Verbindung. Wenn Sie über einen Netzwerktunnel auf Ihre Azure-Dateifreigabe zugreifen, können Sie Ihre Azure-Dateifreigabe wie eine lokale Dateifreigabe einbinden, da der SMB-Datenverkehr Ihre Organisationsgrenze nicht überschreitet.   

Obwohl es aus technischer Sicht wesentlich einfacher ist, ihre Azure-Dateifreigaben über den öffentlichen Endpunkt einzubinden, erwarten wir, dass die meisten Kunden ihre Azure-Dateifreigaben über eine ExpressRoute- oder VPN-Verbindung einbinden. Die Einbindung mit diesen Optionen ist mit SMB- und NFS-Freigaben möglich. Zu diesem Zweck müssen Sie Folgendes für Ihre Umgebung konfigurieren:  

- **Netzwerktunnelung mithilfe von ExpressRoute, Site-to-Site- oder Point-to-Site-VPN**: Das Tunneln in ein virtuelles Netzwerk ermöglicht den Zugriff auf Azure-Dateifreigaben vom lokalen Standort aus, auch wenn Port 445 blockiert ist.
- **Private Endpunkte**: Private Endpunkte stellen Ihrem Speicherkonto eine dedizierte IP-Adresse innerhalb des Adressraums des virtuellen Netzwerks zur Verfügung. Dies ermöglicht Netzwerktunnelung, ohne dass lokale Netzwerke für alle IP-Adressbereiche, die sich im Besitz der Azure-Speichercluster befinden, geöffnet werden müssen. 
- **DNS--Weiterleitung**: Konfigurieren Sie das lokale DNS, um den Namen Ihres Speicherkontos (also `storageaccount.file.core.windows.net` für die öffentlichen Cloudregionen) aufzulösen, um die IP-Adresse Ihrer privaten Endpunkte aufzulösen.

Informationen zum Planen der Netzwerke, die mit der Bereitstellung einer Azure-Dateifreigabe verbunden sind, finden Sie unter [Überlegungen zum Azure Files-Netzwerk](storage-files-networking-overview.md).

## <a name="encryption"></a>Verschlüsselung
Azure Files unterstützt zwei verschiedene Verschlüsselungstypen: Verschlüsselung während der Übertragung, die sich auf die Verschlüsselung bezieht, die beim Einbinden/Zugreifen auf die Azure-Dateifreigabe verwendet wird, sowie die Verschlüsselung ruhender Daten, die sich darauf bezieht, wie die Daten beim Speichern auf einem Datenträger verschlüsselt werden. 

### <a name="encryption-in-transit"></a>Verschlüsselung während der Übertragung

> [!IMPORTANT]
> In diesem Abschnitt wird die Verschlüsselung während der Übertragung für SMB-Freigaben behandelt. Ausführliche Informationen zur Verschlüsselung während der Übertragung mit NFS-Freigaben finden Sie unter [Sicherheit](storage-files-compare-protocols.md#security).

Standardmäßig ist in allen Azure-Speicherkonten die Verschlüsselung während der Übertragung aktiviert. Das bedeutet Folgendes: Wenn Sie eine Dateifreigabe über SMB einbinden oder über FileREST darauf zugreifen (per Azure-Portal, PowerShell/CLI oder Azure-SDKs), lässt Azure Files die Verbindung nur dann zu, wenn sie über SMB 3.0 oder höher mit Verschlüsselung oder über HTTPS hergestellt wird. Clients, die SMB 3.0 nicht unterstützen, oder Clients, die zwar SMB 3.0, aber nicht die SMB-Verschlüsselung unterstützen, können die Azure-Dateifreigabe nicht einbinden, wenn die Verschlüsselung während der Übertragung aktiviert ist. Weitere Informationen dazu, welche Betriebssysteme SMB 3.0 mit Verschlüsselung unterstützen, finden Sie in der ausführlichen Dokumentation zu [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md) und [Linux](storage-how-to-use-files-linux.md). Alle aktuellen PowerShell-, CLI- und SDK-Versionen unterstützen HTTPS.  

Sie können die Verschlüsselung während der Übertragung für ein Azure-Speicherkonto deaktivieren. Wenn die Verschlüsselung deaktiviert ist, lässt Azure Files auch SMB 2.1, SMB 3.0 ohne Verschlüsselung und nicht verschlüsselte FileREST-API-Aufrufe über HTTP zu. Der Hauptgrund für die Deaktivierung der Verschlüsselung während der Übertragung ist die Unterstützung einer älteren Anwendung, die unter einem älteren Betriebssystem wie z. B. Windows Server 2008 R2 oder einer älteren Linux-Distribution ausgeführt werden muss. Azure Files lässt nur SMB 2.1-Verbindungen innerhalb der gleichen Region zu, in der sich auch die Azure-Dateifreigabe befindet. Ein SMB 2.1-Client außerhalb der Azure-Region der Azure-Dateifreigabe – z. B. ein lokales System oder eine andere Azure-Region – kann nicht auf die Dateifreigabe zugreifen.

Wir empfehlen dringend, sicherzustellen, dass die Verschlüsselung von Daten während der Übertragung aktiviert ist.

Weitere Informationen zur Verschlüsselung während der Übertragung finden Sie unter [Vorschreiben einer sicheren Übertragung in Azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="encryption-at-rest"></a>Verschlüsselung ruhender Daten
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="data-protection"></a>Schutz von Daten
Azure Files umfasst einen mehrschichtigen Ansatz, um sicherzustellen, dass Ihre Daten gesichert, wiederhergestellt und vor Sicherheitsbedrohungen geschützt werden können.

### <a name="soft-delete"></a>Vorläufiges Löschen
Das vorläufige Löschen von Dateifreigaben (Vorschauversion) ist eine Einstellung auf Speicherkontoebene, die es Ihnen ermöglicht, Ihre Dateifreigabe wiederherzustellen, wenn diese versehentlich gelöscht wird. Wenn eine Dateifreigabe gelöscht wird, geht sie in einen vorläufig gelöschten Zustand über, anstatt dauerhaft gelöscht zu werden. Sie können den Zeitraum konfigurieren, in dem vorläufig gelöschte Daten wiederhergestellt werden können, ehe sie dauerhaft gelöscht werden, und die Löschung der Freigabe während dieses Aufbewahrungszeitraums jederzeit rückgängig machen. 

Es wird empfohlen, das vorläufige Löschen für die meisten Dateifreigaben zu aktivieren. Wenn Sie über einen Workflow verfügen, bei dem das Löschen von Dateifreigaben üblich und zu erwarten ist, können Sie sich für einen sehr kurzen Aufbewahrungszeitraum oder die Deaktivierung der Funktion für das vorläufige Löschen entscheiden.

Weitere Informationen zum vorläufigen Löschen finden Sie unter [Verhindern eines versehentlichen Löschens von Azure-Dateifreigaben](./storage-files-prevent-file-share-deletion.md).

### <a name="backup"></a>Backup
Sie können Ihre Azure-Dateifreigabe mithilfe von [Freigabemomentaufnahmen](./storage-snapshots-files.md) sichern, die schreibgeschützte Zeitpunktwiederherstellungskopien Ihrer Freigaben sind. Momentaufnahmen sind inkrementell, d. h., sie enthalten nur so viele Daten, wie seit der vorherigen Momentaufnahme geändert wurden. Sie können bis zu 200 Momentaufnahmen pro Dateifreigabe machen und diese bis zu zehn Jahre lang aufbewahren. Sie können die Momentaufnahmen entweder manuell im Azure-Portal, über PowerShell oder die Befehlszeilenschnittstelle (CLI) machen oder [Azure Backup](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) verwenden. Momentaufnahmen werden in der Dateifreigabe gespeichert. Wenn Sie die Dateifreigabe also löschen, werden die Momentaufnahmen ebenfalls gelöscht. Stellen Sie sicher, dass das vorläufige Löschen für Ihre Dateifreigabe aktiviert ist, um Ihre Momentaufnahmensicherungen vor dem unbeabsichtigten Löschen zu schützen.

Im Artikel [Informationen zum Sichern von Azure-Dateifreigaben](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) finden Sie Informationen zum Planen und Aufbewahren von Momentaufnahmen. Durch die Generationenprinzipfunktionen (grandfather-father-son, GFS) können Sie täglich, wöchentlich, monatlich oder jährlich Momentaufnahmen machen, die jeweils einen eigenen Aufbewahrungszeitraum haben. Azure Backup orchestriert außerdem die Aktivierung des vorläufigen Löschens und übernimmt eine Löschsperre für ein Speicherkonto, sobald eine beliebige Dateifreigabe für die Sicherung konfiguriert ist. Schließlich stellt Azure Backup bestimmte wichtige Überwachungs- und Warnungsfunktionen bereit, durch die Kunden eine konsolidierte Ansicht ihres Sicherungsbestands haben.

Mithilfe von Azure Backup können Sie im Azure-Portal Wiederherstellungen sowohl auf Element- als auch auf Freigabeebene durchführen. Sie müssen lediglich den Wiederherstellungspunkt (eine bestimmte Momentaufnahme), die bestimmte Datei oder das bestimmte Verzeichnis und dann den Speicherort (ursprünglich oder alternativ) auswählen, in dem die Ressourcen wiederhergestellt werden sollen. Der Sicherungsdienst übernimmt das Kopieren der Momentaufnahmedaten und zeigt den Wiederherstellungsfortschritt im Portal an.

Weitere Informationen zur Sicherung finden Sie unter [Informationen zum Sichern von Azure-Dateifreigaben](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="advanced-threat-protection-for-azure-files-preview"></a>Advanced Threat Protection für Azure Files (Vorschauversion)
Advanced Threat Protection (ATP) für Azure Storage bietet eine zusätzliche Ebene der Sicherheitsintelligenz, durch die Warnungen ausgegeben werden, wenn in Ihrem Speicherkonto anomale Aktivitäten wie beispielsweise ungewöhnliche Zugriffsversuche auf das Speicherkonto erkannt werden. ATP führt außerdem eine Analyse in Bezug auf die Malwarehashzuverlässigkeit durch und benachrichtigt Sie über bekannte Schadsoftware. Sie können ATP über das Azure Security Center auf Abonnement- oder Speicherkontoebene konfigurieren. 

Weitere Informationen finden Sie unter [Konfigurieren von Advanced Threat Protection für Azure Storage](../common/azure-defender-storage-configure.md).

## <a name="storage-tiers"></a>Speicherebenen
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="understanding-provisioning-for-premium-file-shares"></a>Grundlegendes zur Bereitstellung für Premium-Dateifreigaben
Premium-Dateifreigaben werden basierend auf einem festen Verhältnis aus GiB/IOPS/Durchsatz bereitgestellt. Für alle Freigaben wird Mindestgrundwert/-durchsatz angeboten, und Burst ist zulässig. Für jedes bereitgestellte GiB erhält die Freigabe Mindest-IOPS/-durchsatz sowie ein IOPS und einen Durchsatz von 0,1 MiB/s bis zum maximalen Grenzwert pro Freigabe. Die kleinste zulässige Bereitstellung beträgt 100 GiB mit den minimalen Werten für Mindest-IOPS/-durchsatz. 

Für alle Premium-Freigaben werden nach bestem Bemühen kostenlose Bursts angeboten. Alle Freigabegrößen können Bursts bis zu 4.000 IOPS oder bis zu drei IOPS pro bereitgestellter GiB-Größe ausführen, je nachdem, welcher Wert einen größeren Burst-IOPS-Wert für die Freigabe bereitstellt. Alle Freigaben unterstützen für Bursts eine maximale Dauer von 60 Minuten bei einem Spitzenburstgrenzwert. Neue Freigaben beginnen mit dem vollständigen Burstguthaben, basierend auf der bereitgestellten Kapazität.

Freigaben müssen in Schritten von 1GiB bereitgestellt werden. Die Mindestgröße beträgt 100 GiB, die nächste Größe ist 101 GiB usw.

> [!TIP]
> IOPS-Grundwert = 400 + 1 * bereitgestellte GiB. (Bis zu 100.000 IOPS).
>
> Burstgrenzwert = MAX (4.000, 3 * IOPS-Grundwert). (je nachdem, welcher Grenzwert größer ist, bis zu einem Maximum von 100.000 IOPS).
>
> Ausgangsrate = 60MiB/s + 0,06 * bereitgestellte GiB
>
> Eingangsrate = 40MiB/s + 0,04 * bereitgestellte GiB

Die Größe bereitgestellter Freigaben wird durch ein Freigabekontingent angegeben. Das Freigabekontingent kann jederzeit heraufgesetzt, jedoch erst 24 Stunden nach der letzten Heraufsetzung herabgesetzt werden. Wenn in einer 24-stündigen Wartezeit keine Heraufsetzung aufgetreten ist, können Sie das Freigabekontingent beliebig oft herabsetzen, bis Sie es erneut heraufsetzen. Änderungen von IOPS/Durchsatz werden innerhalb weniger Minuten nach der Größenänderung wirksam.

Es ist möglich, die Größe Ihrer bereitgestellten Freigabe unter Ihre verbrauchten GiB zu reduzieren. Wenn Sie dies tun, gehen Ihnen keine Daten verloren, sondern es wird Ihnen weiterhin die verwendete Größe in Rechnung gestellt. Sie erhalten die Leistung (IOPS-Grundwert, Durchsatz und Burst-IOPS) der bereitgestellten Freigabe, nicht die der verwendeten Größe.

Die folgende Tabelle zeigt einige Beispiele dieser Formeln für die bereitgestellten Freigabengrößen:

|Kapazität (GiB) | IOPS-Grundwert | Burst-IOPS | Ausgehend (MiB/s) | Eingehend (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 500     | Bis zu 4.000     | 66   | 44   |
|500         | 900     | Bis zu 4.000  | 90   | 60   |
|1\.024       | 1\.424   | Bis zu 4.000   | 122   | 81   |
|5\.120       | 5\.520   | Bis zu 15.360  | 368   | 245   |
|10.240      | 10.640  | Bis zu 30.720  | 675   | 450   |
|33.792      | 34.192  | Bis zu 100.000 | 2\.088 | 1\.392   |
|51.200      | 51.600  | Bis zu 100.000 | 3\.132 | 2\.088   |
|102.400     | 100.000 | Bis zu 100.000 | 6\.204 | 4\.136   |

Wichtiger Hinweis: Die Leistung der Dateifreigabe hängt unter anderem von den Computernetzwerklimits, der verfügbaren Netzwerkbandbreite, den E/A-Größen und der Parallelität ab. Beispielsweise kann ein einzelner virtueller Windows-Computer ohne aktivierte Funktion SMB Multichannel namens *Standard F16s_v2*, der mit einer Premium-Dateifreigabe über SMB verbunden ist, laut internen Tests mit Lese-/Schreibvorgängen mit einer E/A-Größe von 8 KiB 20 K Lese-IOPS und 15 K Schreib-IOPS erzielen. Bei Lese-/Schreibvorgängen mit einer E/A-Größe von 512 MiB kann derselbe virtuelle Computer einen Durchsatz von 1,1 GiB/s ausgehend und 370 MiB/s eingehend erzielen. Der gleiche Client kann eine bis zu \~dreifache Leistung erzielen, wenn SMB Multichannel für die Premium-Freigaben aktiviert ist. Um eine maximale Leistung zu erreichen, [aktivieren Sie SMB Multichannel](storage-files-enable-smb-multichannel.md), und verteilen Sie die Last auf mehrere VMs. Weitere Informationen zur [Leistung von SMB Multichannel](storage-troubleshooting-files-performance.md) und gängigen Leistungsproblemen sowie deren Lösungen finden Sie im [Leitfaden zur Problembehandlung](storage-files-smb-multichannel-performance.md).

#### <a name="bursting"></a>Bursting
Wenn Ihre Workload die zusätzliche Leistung benötigt, um den Spitzenbedarf zu erfüllen, kann Ihre Freigabe Burstgutschriften verwenden, um den IOPS-Grundwert für die Freigabe zu überschreiten und die erforderliche Freigabeleistung bereitzustellen. Premium-Dateifreigaben können ihren IOPS-Wert bis auf 4.000 oder um den Faktor drei erhöhen (je nachdem, welcher Wert höher ist). Bursting wird automatisiert und funktioniert auf Basis eines Guthabensystems. Die Burstübertragung funktioniert auf Best-Effort-Basis, und der Burstgrenzwert ist keine Garantie. Bei Dateifreigaben ist eine Burstübertragung *bis zum* Grenzwert für eine maximale Dauer von 60 Minuten möglich.

Guthaben sammeln sich in einem Burstbucket an, wenn Datenverkehr für Ihre Dateifreigabe unterhalb des IOPS-Grundwerts liegt. Beispielsweise weist eine Freigabe mit 100 GiB 500 IOPS-Grundwerte auf. Wenn der tatsächliche Datenverkehr auf der Freigabe 100 IOPS für ein bestimmtes 1-Sekunden-Intervall betrug, werden die 400 nicht verwendeten IOPS einem Burstbucket gutgeschrieben. Auf ähnliche Weise fällt eine Burstgutschrift in Höhe von 1.424 IOPS für eine 1-TiB-Leerlauffreigabe an. Diese Guthaben werden dann später verwendet, wenn Vorgänge die IOPS-Grundwerte überschreiten.

Wenn eine Freigabe den IOPS-Grundwert überschreitet und Guthaben in einem Burstbucket hat, führt sie Burstübertragungen mit der maximal zulässigen Spitzenburstrate durch. Freigaben können bis zu einer Dauer von maximal 60 Minuten weiterhin Bursts ausführen, solange Guthaben verbleibt. Dies hängt aber von der Höhe des aufgelaufenen Burstguthabens ab. Jede E/A über dem IOPS-Grundwert verbraucht ein Guthaben, und wenn alle Guthaben verbraucht sind, kehrt die Freigabe zum IOPS-Grundwert zurück.

Freigabeguthaben können drei Zustände aufweisen:

- Anwachsend, wenn die Dateifreigabe weniger als den IOPS-Grundwert verwendet.
- Abnehmend, wenn die Dateifreigabe mehr als den IOPS-Grundwert verwendet und sich im Burstmodus befindet.
- Konstant, wenn die Dateifreigabe genau den IOPS-Grundwert verwendet. Es sind keine Guthaben aufgelaufen oder werden verwendet.

Neue Dateifreigaben beginnen mit der vollen Anzahl von Guthaben im Burstbucket. Burstguthaben werden nicht angesammelt, wenn der Freigabe-IOPS aufgrund einer Einschränkung durch den Server unter den IOPS-Grundwert fällt.

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Aktivieren von Standard-Dateifreigaben für bis zu 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="limitations"></a>Einschränkungen
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="redundancy"></a>Redundanz
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

## <a name="migration"></a>Migration
In vielen Fällen werden Sie keine ganz neue Dateifreigabe für Ihre Organisation einrichten, sondern stattdessen eine vorhandene Dateifreigabe von einem lokalen Dateiserver oder NAS-Gerät zu Azure Files migrieren. Für den Erfolg der Migration ist es wichtig, die richtige Migrationsstrategie und das richtige Tool für Ihr Szenario auszuwählen. 

Der[Artikel zur Migrationsübersicht](storage-files-migration-overview.md) behandelt kurz die Grundlagen und enthält eine Tabelle, die Verweise auf Migrationsleitfäden enthält, die Ihr Szenario wahrscheinlich abdecken.

## <a name="next-steps"></a>Nächste Schritte
* [Planung für die Bereitstellung einer Azure-Dateisynchronisierung](storage-sync-files-planning.md)
* [Bereitstellen von Azure Files](storage-files-deployment-guide.md)
* [Bereitstellen der Azure-Dateisynchronisierung](storage-sync-files-deployment-guide.md)
* [Ermitteln des Migrationsleitfadens für Ihr Szenario anhand des Artikels zur Migrationsübersicht](storage-files-migration-overview.md)
