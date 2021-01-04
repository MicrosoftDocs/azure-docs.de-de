---
title: StorSimple Virtual Array – Bewährte Methoden | Microsoft Docs
description: Es werden die bewährten Methoden für die Bereitstellung und Verwaltung des StorSimple Virtual Array beschrieben.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 57ac6eeb-c47c-442d-a5f4-b360d81a76a6
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 71b018da6b54ebf2b45a261378ea521a397159e5
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964982"
---
# <a name="storsimple-virtual-array-best-practices"></a>StorSimple Virtual Array – Bewährte Methoden

## <a name="overview"></a>Übersicht

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

Bei Microsoft Azure StorSimple Virtual Array handelt es sich um eine integrierte Speicherlösung, mit der Speicheraufgaben zwischen einem lokalen virtuellen Gerät, das in einem Hypervisor ausgeführt wird, und dem Microsoft Azure-Cloudspeicher verwaltet werden. Das StorSimple Virtual Array ist eine effiziente und kostengünstige Alternative zum physischen Array der 8000er Serie. Das virtuelle Array kann unter Ihrer vorhandenen Hypervisor-Infrastruktur ausgeführt werden, unterstützt sowohl das iSCSI- als auch das SMB-Protokoll und ist gut für Szenarien mit weit entfernten Büros oder Büros in Zweigniederlassungen geeignet. Weitere Informationen zu StorSimple-Lösungen finden Sie unter [Microsoft Azure StorSimple Overview](https://www.microsoft.com/en-us/server-cloud/products/storsimple/overview.aspx)(Übersicht über Microsoft Azure StorSimple).

In diesem Artikel werden die bewährten Methoden beschrieben, die während der Ersteinrichtung, Bereitstellung und Verwaltung des StorSimple Virtual Array implementiert werden. Diese bewährten Methoden umfassen überprüfte Richtlinien für die Einrichtung und Verwaltung Ihres virtuellen Arrays. Dieser Artikel richtet sich an IT-Administratoren, die die virtuellen Arrays in den Datencentern bereitstellen und verwalten.

Es ist ratsam, die bewährten Methoden regelmäßig zu überprüfen. So können Sie sicherstellen, dass Ihr Gerät noch kompatibel ist, wenn Änderungen am Setup oder am Vorgangsfluss vorgenommen werden. Falls beim Implementieren dieser bewährten Methoden auf dem Array Probleme auftreten, können Sie den [Microsoft-Support](storsimple-virtual-array-log-support-ticket.md) um Hilfe bitten.

## <a name="configuration-best-practices"></a>Bewährte Methoden für die Konfiguration
Diese bewährten Methoden decken die Richtlinien ab, die bei der Ersteinrichtung und der Bereitstellung der virtuellen Arrays befolgt werden müssen. Hierzu gehören auch die bewährten Methoden für die Bereitstellung des virtuellen Computers, Gruppenrichtlinieneinstellungen, Größenauslegung, Netzwerkeinrichtung, Speicherkontokonfiguration und Erstellung von Freigaben und Volumes für das virtuelle Array. 

### <a name="provisioning"></a>Bereitstellung
Das StorSimple Virtual Array ist ein virtueller Computer (VM), der unter dem Hypervisor (Hyper-V oder VMware) Ihres Hostservers bereitgestellt wird. Stellen Sie beim Bereitstellen des virtuellen Computers sicher, dass Ihr Host genügend Ressourcen zur Verfügung stellen kann. Weitere Informationen zur Bereitstellung eines Arrays finden Sie unter den [Mindestanforderungen für Ressourcen](storsimple-virtual-array-deploy2-provision-hyperv.md#step-1-ensure-that-the-host-system-meets-minimum-virtual-array-requirements) .

Implementieren Sie die folgenden bewährten Methoden, wenn Sie das virtuelle Array bereitstellen:

|  | Hyper-V | VMware |
| --- | --- | --- |
| **Typ des virtuellen Computers** |**2. Generation** für die Verwendung mit Windows Server 2012 oder höher und ein *VHDX* -Image. <br></br> **1. Generation** für die Verwendung mit Windows Server 2008 oder höher und ein *VHD* -Image. |Setzen Sie einen virtuellen Computer der Version 8 ein, wenn Sie das *VMDK*-Image verwenden. |
| **Speichertyp** |Konfigurieren Sie dies als **statischen Speicher**. <br></br> Verwenden Sie nicht die Option **Dynamischer Arbeitsspeicher** . | |
| **Datenträger für Daten** |Führen Sie die Bereitstellung als **Dynamisch erweiterbar** durch.<br></br> **Feste Größe** ist mit einem größeren Zeitaufwand verbunden. <br></br> Verwenden Sie nicht die Option **Differenzierend** . |Verwenden Sie die Option **Thin Provision** (Schlanke Speicherzuweisung). |
| **Datenträgeränderung** |Eine Erweiterung oder Verkleinerung ist nicht zulässig. Wenn ein Versuch dieser Art gestartet wird, gehen alle lokalen Daten auf dem Gerät verloren. |Eine Erweiterung oder Verkleinerung ist nicht zulässig. Wenn ein Versuch dieser Art gestartet wird, gehen alle lokalen Daten auf dem Gerät verloren. |

### <a name="sizing"></a>Festlegen der Größe
Berücksichtigen Sie beim Festlegen der Größe für das StorSimple Virtual Array die folgenden Punkte:

* Lokale Reservierung für Volumes oder Freigaben: Ungefähr 12% des Speicherplatzes sind auf der lokalen Ebene für jedes bereitgestellte mehrstufige Volume oder eine Freigabe reserviert. Ungefähr 10% des Speicherplatzes sind außerdem für ein lokales Volume für das Dateisystem reserviert.
* Mehraufwand für Momentaufnahmen: Ungefähr 15% des Speicherplatzes auf der lokalen Ebene sind für Momentaufnahmen reserviert.
* Erforderliche Wiederherstellungen: Beim Festlegen der Größe sollte der Speicherplatz berücksichtigt werden, der für die Wiederherstellung erforderlich ist, falls die Wiederherstellung als neuer Vorgang durchgeführt wird. Die Wiederherstellung erfolgt auf einer Freigabe oder einem Volume gleicher Größe.
* Rechnen Sie einen Puffer für eine unerwartete Zunahme der Größe ein.

Basierend auf den oben genannten Punkten lassen sich die Anforderungen der Größenfestlegung mit der folgenden Gleichung ausdrücken:

`Total usable local disk size = (Total provisioned locally pinned volume/share size including space for file system) + (Max (local reservation for a volume/share) for all tiered volumes/share) + (Local reservation for all tiered volumes/shares)`

`Data disk size = Total usable local disk size + Snapshot overhead + buffer for unexpected growth or new share or volume`

Die folgenden Beispiele veranschaulichen, wie Sie die Größe für ein virtuelles Array gemäß Ihren Anforderungen bemessen können.

#### <a name="example-1"></a>Beispiel 1:
Sie möchten auf Ihrem virtuellen Array Folgendes durchführen können:

* Bereitstellen eines mehrstufigen Volumes oder einer Freigabe mit 2 TB
* Bereitstellen eines mehrstufigen Volumes oder einer Freigabe mit 1 TB
* Bereitstellen eines lokalen Volumes oder einer Freigabe mit 300 GB

Wir berechnen für die obigen Volumes oder Freigaben die Speicherplatzanforderungen auf der lokalen Ebene.

Erstens: Für jedes mehrstufige Volume bzw. jede Freigabe entspricht die lokale Reservierung 12% der Volume-/Freigabegröße. Die lokale Reservierung für das lokale Volume bzw. die lokale Freigabe beträgt 10 % des lokalen Volumes bzw. der lokalen Freigabe (zusätzlich zur bereitgestellten Größe). Für dieses Beispiel benötigen Sie Folgendes:

* Lokale Reservierung von 240 GB (für ein mehrstufiges Volume bzw. eine Freigabe mit 2 TB)
* Lokale Reservierung von 120 GB (für ein mehrstufiges Volume bzw. eine Freigabe mit 1 TB)
* 330 GB für lokales Volume bzw. Freigabe (der bereitgestellten Größe von 300 GB werden 10 % der lokalen Reservierung hinzugefügt)

Gesamter Speicherplatz für die lokale Ebene bis zu diesem Punkt: 240 GB + 120 GB + 330 GB = 690 GB.

Zweitens: Wir benötigen mindestens so viel Speicherplatz auf der lokalen Ebene wie für die größte Einzelreservierung. Diese zusätzliche Menge wird verwendet, falls Sie die Wiederherstellung aus einer Cloudmomentaufnahme durchführen müssen. In diesem Beispiel beträgt die größte lokale Reservierung 330 GB (einschließlich der Reservierung für das Dateisystem). Diesen Betrag müssen Sie zu den 690 GB addieren: 690 GB + 330 GB = 1020 GB.
Wenn wir danach weitere Wiederherstellungen durchführen, können wir jeweils den Speicherplatz aus dem vorherigen Wiederherstellungsvorgang bereitstellen.

Drittens: Wir benötigen 15% des bisherigen lokalen Gesamtspeicherplatzes, um lokale Momentaufnahmen zu speichern, sodass nur 85% davon verfügbar sind. In diesem Beispiel gilt daher: 1020 GB = 0,85&ast;bereitgestellte TB für Datenträger für Daten. Für den bereitgestellten Datenträger für Daten ergibt sich also: (1020&ast;(1/0,85)) = 1200 GB = 1,20 TB ~ 1,25 TB (Rundung auf das nächste Quartil).

Wenn Sie eine unerwartete Zunahme und neue Wiederherstellungen berücksichtigen, sollten Sie also einen lokalen Datenträger mit ca. 1,25 bis 1,5 TB bereitstellen.

> [!NOTE]
> Außerdem wird empfohlen, für den lokalen Datenträger die Thin-Bereitstellung (schlanke Speicherzuweisung) zu wählen. Der Grund für diese Empfehlung ist, dass der Wiederherstellungsspeicherplatz nur benötigt wird, wenn Sie Daten wiederherstellen möchten, die älter als fünf Tage sind. Bei der Wiederherstellung auf Elementebene können Sie die Daten für die letzten fünf Tage wiederherstellen, ohne dass der zusätzliche Speicherplatz für die Wiederherstellung benötigt wird.


#### <a name="example-2"></a>Beispiel 2:
Sie möchten auf Ihrem virtuellen Array Folgendes durchführen können:

* Bereitstellen eines mehrstufigen Volumes mit 2 TB
* Bereitstellen eines lokalen Volumes mit 300 GB

Basierend auf 12% der lokalen Speicherplatzreservierung für mehrstufige Volumes/Freigaben und 10% für lokale Volumes/Freigaben benötigen wir Folgendes:

* Lokale Reservierung von 240 GB (für ein mehrstufiges Volume bzw. eine Freigabe mit 2 TB)
* 330 GB für lokales Volume oder lokale Freigabe (der bereitgestellten Größe von 300 GB werden 10 % der lokalen Reservierung hinzugefügt)

Gesamter Speicherplatz für die lokale Ebene: 240 GB + 330 GB = 570 GB

Der mindestens erforderliche Speicherplatz für die Wiederherstellung beträgt 330 GB.

15% des gesamten Datenträgers werden zum Speichern von Momentaufnahmen verwendet, sodass nur 85% verfügbar sind. Die Größe des Datenträgers beträgt also: (900&ast;(1/0,85)) = 1,06 TB ~ 1,25 TB (Rundung auf das nächste Quartil)

Wenn Sie eine unerwartete Zunahme berücksichtigen, sollten Sie also einen lokalen Datenträger mit einer Größe von 1,25 bis 1,5 TB bereitstellen.

### <a name="group-policy"></a>Gruppenrichtlinie
Gruppenrichtlinien sind eine Infrastruktur, die Ihnen das Implementieren von speziellen Konfigurationen für Benutzer und Computer ermöglicht. Gruppenrichtlinieneinstellungen sind in Gruppenrichtlinienobjekten (GPOs) enthalten, die mit den folgenden Active Directory-Domänendienste-Containern verknüpft sind: Websites, Domänen oder Organisationseinheiten (OUs). 

Wenn Ihr virtuelles Array in die Domäne eingebunden ist, können GPOs darauf angewendet werden. Mit diesen GPOs können Anwendungen installiert werden, z.B. eine Antivirensoftware, die sich negativ auf den Betrieb des StorSimple Virtual Array auswirken.

Daher empfehlen wir Folgendes:

* Stellen Sie sicher, dass sich das virtuelle Array in einer eigenen Organisationseinheit für Active Directory befindet.
* Stellen Sie sicher, dass keine Gruppenrichtlinienobjekte (GPOs) auf das virtuelle Array angewendet werden. Sie können die Vererbung blockieren, um sicherzustellen, dass das virtuelle Array (untergeordneter Knoten) nicht automatisch GPOs vom übergeordneten Element erbt. Weitere Informationen finden Sie unter [Blockieren der Vererbung](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731076(v=ws.11)).

### <a name="networking"></a>Netzwerk
Die Netzwerkkonfiguration für Ihr virtuelles Array wird über die lokale Webbenutzeroberfläche durchgeführt. Eine virtuelle Netzwerkschnittstelle wird über den Hypervisor aktiviert, in dem das virtuelle Array bereitgestellt wird. Verwenden Sie die Seite [Netzwerkeinstellungen](storsimple-virtual-array-deploy3-fs-setup.md) , um die IP-Adresse, das Subnetz und das Gateway für die virtuelle Netzwerkschnittstelle zu konfigurieren.  Sie können auch den primären und sekundären DNS-Server, die Zeiteinstellungen und optional die Proxyeinstellungen für Ihr Gerät konfigurieren. Der größte Teil der Netzwerkkonfiguration ist eine einmalige Einrichtung. Lesen Sie [StorSimple-Netzwerkanforderungen](storsimple-ova-system-requirements.md#networking-requirements) , bevor Sie das virtuelle Array bereitstellen.

Für die Bereitstellung des virtuellen Arrays empfehlen wir Ihnen, diese bewährten Methoden zu befolgen:

* Stellen Sie sicher, dass das Netzwerk, in dem das virtuelle Array bereitgestellt wird, jederzeit über die Kapazität zum Bereitstellen einer Internetbandbreite von (mindestens) 5 MBit/s verfügt.
  
  * Die erforderliche Internetbandbreite variiert je nach den Workloadmerkmalen und der Datenänderungsrate.
  * Die Menge an Datenänderungen, die verarbeitet werden können, ist direkt proportional zu Ihrer Internetbandbreite. Beispiel: Beim Erstellen einer Sicherung ist mit einer Bandbreite von 5 MBit/s eine Datenänderungsrate von ca. 18 GB in acht Stunden möglich. Beim Vierfachen an Bandbreite (20 MBit/s) vervierfacht sich auch die Datenänderungsrate (72 GB).
* Stellen Sie sicher, dass jederzeit eine Internetverbindung verfügbar ist. Sporadische oder unzuverlässige Internetverbindungen mit den Geräten können zu einem Verlust des Zugriffs auf die Daten in der Cloud und einer nicht unterstützten Konfiguration führen.
* Bei geplanter Bereitstellung des Geräts als iSCSI-Server:
  
  * Es wird empfohlen, die Option **IP-Adresse automatisch abrufen** (DHCP) zu deaktivieren.
  * Konfigurieren Sie statische IP-Adressen. Sie müssen einen primären und einen sekundären DNS-Server konfigurieren.
  * Wenn Sie auf dem virtuellen Array mehrere Netzwerkschnittstellen definieren, kann nur die erste Netzwerkschnittstelle (standardmäßig die **Ethernet**-Schnittstelle) die Cloud erreichen. Zum Steuern der Art des Datenverkehrs können Sie mehrere virtuelle Netzwerkschnittstellen auf dem virtuellen Array erstellen (als iSCSI-Server konfiguriert) und mit unterschiedlichen Subnetzen verbinden.
* Um nur die (vom virtuellen Array verwendete) Cloudbandbreite zu drosseln, konfigurieren Sie die Drosselung auf dem Router oder in der Firewall. Wenn Sie die Drosselung im Hypervisor definieren, werden alle Protokolle gedrosselt, z.B. auch iSCSI und SMB, anstatt nur die Cloudbandbreite.
* Stellen Sie sicher, dass die Zeitsynchronisierung für Hypervisoren aktiviert ist. Wählen Sie bei Verwendung von Hyper-V Ihr virtuelles Array im Hyper-V-Manager, navigieren Sie zu **Einstellungen &gt; Integrationsdienste**, und vergewissern Sie sich, dass **Zeitsynchronisierung** aktiviert ist.

### <a name="storage-accounts"></a>Speicherkonten
Das StorSimple Virtual Array kann einem einzelnen Speicherkonto zugeordnet werden. Bei diesem Speicherkonto kann es sich um ein automatisch generiertes Speicherkonto, ein Konto in demselben Abonnement wie der Dienst oder ein Speicherkonto handeln, das zu einem anderen Abonnement gehört. Weitere Informationen finden Sie unter [Verwalten von Speicherkonten für Ihr virtuelles Array](storsimple-virtual-array-manage-storage-accounts.md).

Verwenden Sie die folgenden Empfehlungen für Speicherkonten, die Ihrem virtuellen Array zugeordnet sind.

* Rechnen Sie beim Verknüpfen mehrerer virtueller Arrays mit einem einzelnen Speicherkonto die maximale Kapazität (64 TB) für ein virtuelles Array und die maximale Größe (500 TB) für ein Speicherkonto ein. Hierdurch wird die Anzahl von virtuellen Arrays mit voller Größe, die dem Speicherkonto zugeordnet werden können, auf ca. sieben Arrays beschränkt.
* Bei Erstellung eines neuen Speicherkontos
  
  * Wir empfehlen Ihnen, das Speicherkonto in der Region zu erstellen, die dem entfernten Büro bzw. der Zweigniederlassung mit der Bereitstellung des StorSimple Virtual Array am nächsten liegt, um die Latenzzeiten möglichst gering zu halten.
  * Beachten Sie hierbei, dass Sie ein Speicherkonto nicht über Regionen hinweg verschieben können. Es ist auch nicht möglich, einen Dienst abonnementübergreifend zu verschieben.
  * Verwenden Sie ein Speicherkonto, mit dem die Redundanz zwischen den Datencentern implementiert wird. Georedundanter Speicher (GRS), zonenredundanter Speicher (ZRS) und lokal redundanter Speicher (LRS) werden für die Verwendung mit dem virtuellen Array unterstützt. Weitere Informationen zu den verschiedenen Arten von Speicherkonten finden Sie unter [Azure Storage-Replikation](../storage/common/storage-redundancy.md).

### <a name="shares-and-volumes"></a>Freigaben und Volumes
Für Ihr StorSimple Virtual Array können Sie Freigaben bereitstellen, wenn es als Dateiserver konfiguriert ist, und Volumes, wenn es als iSCSI-Server konfiguriert ist. Die bewährten Methoden für das Erstellen von Freigaben und Volumes beziehen sich auf die Größe und auf den konfigurierten Typ.

#### <a name="volumeshare-size"></a>Größe des Volumes bzw. der Freigabe
Für Ihr virtuelles Array können Sie Freigaben bereitstellen, wenn es als Dateiserver konfiguriert ist, und Volumes, wenn es als iSCSI-Server konfiguriert ist. Die bewährten Methoden für das Erstellen von Freigaben und Volumes beziehen sich auf die Größe und auf den konfigurierten Typ. 

Beachten Sie die folgenden bewährten Methoden, wenn Sie Freigaben oder Volumes auf Ihrem virtuellen Gerät bereitstellen:

* Die Dateigrößen relativ zur bereitgestellten Größe einer mehrstufigen Freigabe können sich auf die Leistung der Anordnung in Ebenen auswirken. Das Arbeiten mit großen Dateien kann zu einer langsamen Anordnung führen. Bei der Arbeit mit großen Dateien sollte die größte Datei nach Möglichkeit kleiner als 3 % der Größe der Dateifreigabe sein.
* Im virtuellen Array können maximal 16 Volumes/Freigaben erstellt werden. Informationen zu den Größengrenzwerten für lokal angefügte und mehrstufige Volumes/Freigaben finden Sie unter [Einschränkungen von StorSimple Virtual Array](storsimple-ova-limits.md).
* Rechnen Sie beim Erstellen eines Volumes den erwarteten Datenverbrauch und das zukünftige Wachstum ein. Das Volume oder die Freigabe kann nicht nachträglich vergrößert werden.
* Nach der Erstellung des Volumes bzw. der Freigabe können Sie die Größe unter StorSimple nicht mehr verringern.
* Beim Schreiben auf ein mehrstufiges Volume unter StorSimple wird eine E/A-Drosselung durchgeführt, wenn die Volumedaten einen bestimmten Schwellenwert erreichen (relativ zum lokalen Speicherplatz, der für das Volume reserviert ist). Wenn weiter auf dieses Volume geschrieben wird, wird der E/A-Vorgang erheblich verlangsamt. Es ist zwar möglich, über die bereitgestellte Kapazität hinaus auf ein mehrstufiges Volume zu schreiben (es wird nicht aktiv verhindert, dass der Benutzer das Schreiben über die Kapazität hinaus durchführt), aber es wird eine Warnungsbenachrichtigung mit dem Hinweis angezeigt, dass die abonnierte Menge überschritten ist. Wenn die Warnung angezeigt wird, sollten Sie unbedingt Korrekturmaßnahmen ergreifen, z.B. das Löschen der Volumedaten (Volumeerweiterung wird derzeit nicht unterstützt).
* Für Anwendungsfälle der Notfallwiederherstellung gilt Folgendes: Da sowohl die Anzahl von zulässigen Freigaben/Volumes als auch die maximale Anzahl von Freigaben/Volumes, die parallel verarbeitet werden können, 16 beträgt, wirkt sich die Anzahl von Freigaben/Volumes nicht auf RPO und RTOs aus.

#### <a name="volumeshare-type"></a>Volumen-/Freigabetyp
StorSimple unterstützt basierend auf der Nutzung zwei Volume-/Freigabetypen: lokal und mehrstufig. Für lokale Volumes/Freigaben wird die Thick-Bereitstellung verwendet, und für mehrstufige Volumes/Freigaben die Thin-Bereitstellung. Sie können kein lokal angeheftete Volumen bzw. keine lokal angeheftete Freigabe in eine mehrstufige oder *andersherum* konvertieren, sobald sie erstellt ist.

Es wird empfohlen, beim Konfigurieren von StorSimple-Volumes bzw. -Freigaben die folgenden bewährten Methoden zu implementieren:

* Identifizieren Sie den Volumetyp basierend auf den Workloads, die Sie bereitstellen möchten, bevor Sie ein Volume erstellen. Verwenden Sie lokale Volumes für Workloads, für die lokale Garantien für Daten (auch während eines Cloudausfalls) und geringe Cloudlatenzen erforderlich sind. Nachdem Sie auf Ihrem virtuellen Array ein Volume erstellt haben, können Sie den Volumetyp nicht von lokal in mehrstufig bzw. *umgekehrt* ändern. Erstellen Sie beispielsweise lokale Volumes, wenn Sie SQL-Workloads oder Workloads mit Hosting von virtuellen Computern (VMs) bereitstellen. Verwenden Sie mehrstufige Volumes für Dateifreigabe-Workloads.


#### <a name="volume-format"></a>Volumeformat
Nachdem Sie auf Ihrem iSCSI-Server StorSimple-Volumes erstellt haben, müssen Sie die Volumes initialisieren, bereitstellen und formatieren. Dieser Vorgang wird auf dem Host durchgeführt, der mit Ihrem StorSimple-Gerät verbunden ist. Die folgenden bewährten Methoden werden beim Bereitstellen und Formatieren von Volumes auf dem StorSimple-Host empfohlen.

* Führen Sie eine Schnellformatierung für alle StorSimple-Volumes durch.
* Verwenden Sie beim Formatieren eines StorSimple-Volumes für die Zuordnungseinheit (AUS) eine Größe von 64 KB (Standard ist 4 KB). Die Zuordnungseinheit mit einer Größe von 64 KB basiert auf Tests, die intern für häufige StorSimple-Workloads und für andere Workloads durchgeführt wurden.
* Verwenden Sie für ein StorSimple Virtual Array, das als iSCSI-Server konfiguriert ist, keine übergreifenden Volumes oder dynamischen Datenträger, da diese Volumes oder Datenträger von StorSimple nicht unterstützt werden.

#### <a name="share-access"></a>Zugriff auf Freigaben
Befolgen Sie diese Richtlinien, wenn Sie Freigaben auf dem Dateiserver des virtuellen Arrays erstellen:

* Weisen Sie eine Benutzergruppe beim Erstellen einer Freigabe als Freigabeadministrator und nicht als einzelnen Benutzer zu.
* Sie können die NTFS-Berechtigungen verwalten, nachdem die Freigabe erstellt wurde, indem Sie Freigaben mit dem Windows-Explorer bearbeiten.

#### <a name="volume-access"></a>Zugriff auf Volumes
Beim Konfigurieren der iSCSI-Volumes auf dem StorSimple Virtual Array ist es wichtig, den Zugriff zu steuern, sofern dies erforderlich ist. Erstellen Sie Access Control-Datensätze (ACRs), und ordnen Sie sie StorSimple-Volumes zu, um festzulegen, welche Hostserver auf Volumes zugreifen können.

Nutzen Sie die folgenden bewährten Methoden, wenn Sie ACRs für StorSimple-Volumes konfigurieren:

* Ordnen Sie einem Volume immer mindestens einen ACR zu.

* Stellen Sie beim Zuweisen von mehr als einem ACR zu einem Volume sicher, dass das Volume nicht so verfügbar gemacht wird, dass darauf gleichzeitig mit mehr als einem nicht gruppierten Host zugegriffen werden kann. Wenn Sie einem Volume mehrere ACRs zugewiesen haben, wird eine Warnmeldung angezeigt, damit Sie die Konfiguration überprüfen können.

### <a name="data-security-and-encryption"></a>Datensicherheit und -verschlüsselung
Das StorSimple Virtual Array verfügt über Sicherheits- und Verschlüsselungsfeatures, mit denen die Vertraulichkeit und Integrität Ihrer Daten sichergestellt wird. Für die Verwendung dieser Features empfehlen wir Ihnen die Befolgung der folgenden bewährten Methoden: 

* Definieren Sie einen Cloudspeicher-Verschlüsselungsschlüssel zum Generieren einer AES-256-Verschlüsselung, bevor die Daten aus Ihrem virtuellen Array in die Cloud gesendet werden. Dieser Schlüssel ist nicht erforderlich, wenn die Daten bereits verschlüsselt sind. Der Schlüssel kann mit einem Schlüsselverwaltungssystem, z. B. [Azure Key Vault](../key-vault/general/overview.md), generiert und sicher aufbewahrt werden.
* Stellen Sie beim Konfigurieren des Speicherkontos über den StorSimple Manager-Dienst sicher, dass Sie den TLS-Modus aktivieren, um einen sicheren Kanal für die Netzwerkkommunikation zwischen Ihrem StorSimple-Gerät und der Cloud zu erstellen.
* Generieren Sie die Schlüssel für Ihre Speicherkonten in regelmäßigen Abständen neu (per Zugriff auf den Azure Storage-Dienst), um basierend auf der geänderten Administratorliste alle Zugriffsänderungen abzudecken.
* Die Daten in Ihrem virtuellen Array werden komprimiert und dedupliziert, bevor sie an Azure gesendet werden. Es ist nicht zu empfehlen, den Rollendienst für die Datendeduplizierung auf dem Windows Server-Host zu verwenden.

## <a name="operational-best-practices"></a>Bewährte Methoden für den Betrieb
Die bewährten Methoden für den Betrieb sind Richtlinien, die bei der täglichen Verwaltung bzw. beim Betrieb des virtuellen Arrays eingehalten werden sollten. Hierbei geht es um bestimmte Verwaltungsaufgaben, z.B. das Erstellen von Sicherungen, Wiederherstellen aus einem Sicherungssatz, Durchführen eines Failovers, Deaktivieren und Löschen des Arrays, Überwachen der Systemnutzung und -integrität und Ausführen von Virenscans auf dem virtuellen Array.

### <a name="backups"></a>Backups
Die Daten auf dem virtuellen Array werden auf zwei Arten in der Cloud gesichert: per täglicher automatisierter Standardsicherung des gesamten Geräts mit Beginn um 22:30 Uhr oder per manueller bedarfsgesteuerter Sicherung. Standardmäßig erstellt das Gerät automatisch tägliche Cloudmomentaufnahmen aller Daten, die sich darauf befinden. Weitere Informationen finden Sie unter [Sichern des StorSimple Virtual Array](storsimple-virtual-array-backup.md).

Häufigkeit und Aufbewahrungsdauer von Standardsicherungen können nicht geändert werden, aber Sie können die Uhrzeit konfigurieren, zu der die Sicherungen jeden Tag initiiert werden. Wir empfehlen folgende Vorgehensweise zum Konfigurieren der Startzeit für die automatisierten Sicherungen:

* Planen Sie Sicherungen für Zeiten außerhalb der Zeiträume mit Spitzenlast. Die Startzeit der Sicherung sollte nicht mit einer hohen E/A-Auslastung des Hosts zusammentreffen.
* Initiieren Sie eine manuelle bedarfsgesteuerte Sicherung, wenn Sie die Durchführung eines Gerätefailovers planen, oder vor dem Wartungsfenster, um die Daten in Ihrem virtuellen Array zu schützen.

### <a name="restore"></a>Restore
Sie können die Wiederherstellung für einen Sicherungssatz auf zwei Arten durchführen: Führen Sie die Wiederherstellung auf einem anderen Volume oder einer anderen Freigabe durch, oder wählen Sie die Wiederherstellung auf Elementebene (nur für ein virtuelles Array verfügbar, das als Dateiserver konfiguriert ist). Bei der Wiederherstellung auf Elementebene können Sie die Dateien und Ordner einer Cloudsicherung für alle Freigaben des StorSimple-Geräts präzise wiederherstellen. Weitere Informationen finden Sie unter [Wiederherstellen aus einer Sicherung des StorSimple Virtual Array](storsimple-virtual-array-clone.md).

Beachten Sie beim Durchführen einer Wiederherstellung die folgenden Richtlinien:

* Das StorSimple Virtual Array unterstützt keine direkte Wiederherstellung an demselben Ort. Dies ist aber leicht möglich, indem Sie einen Prozess mit zwei Schritten verwenden: Stellen Sie Speicherplatz auf dem virtuellen Array bereit, und führen Sie anschließend die Bereitstellung auf einem anderen Volume bzw. einer Freigabe durch.
* Beachten Sie bei der Wiederherstellung von einem lokalen Volume, dass die Wiederherstellung ein Vorgang mit langer Ausführungsdauer ist. Das Volume kann zwar schnell online geschaltet werden, aber die Daten werden im Hintergrund dann weiter aufgefüllt.
* Der Volumetyp bleibt während des Wiederherstellungsvorgangs gleich. Ein mehrstufiges Volume wird auf einem anderen mehrstufigen Volume wiederhergestellt, und ein lokales Volume wird auf einem anderen lokalen Volume wiederhergestellt.
* Wenn Sie versuchen, ein Volume oder eine Freigabe aus einer Sicherung wiederherzustellen, und der Wiederherstellungsauftrag fehlschlägt, wird möglicherweise dennoch ein Zielvolume bzw. eine -freigabe im Portal erstellt. Es ist wichtig, dass Sie dieses nicht verwendete Zielvolume bzw. die Zielfreigabe im Portal löschen, um zukünftige Probleme mit diesem Element zu minimieren.

### <a name="failover-and-disaster-recovery"></a>Failover und Notfallwiederherstellung
Ein Gerätefailover ermöglicht das Migrieren Ihrer Daten von einem *Quellgerät* im Datencenter auf ein anderes *Zielgerät* an demselben oder einem anderen geografischen Standort. Das Gerätefailover gilt für das gesamte Gerät. Während des Failovers gehen die Clouddaten für das Quellgerät in den Besitz des Zielgeräts über.

Für das StorSimple Virtual Array können Sie das Failover nur zu einem anderen virtuellen Array durchführen, das mit demselben StorSimple Manager-Dienst verwaltet wird. Ein Failover auf ein Gerät der 8000er Serie oder ein Array, das von einem anderen StorSimple Manager-Dienst (als für das Quellgerät) verwaltet wird, ist nicht zulässig. Weitere Informationen zu den Failoveraspekten finden Sie unter [Voraussetzungen für das Gerätefailover](storsimple-virtual-array-failover-dr.md).

Beachten Sie Folgendes, wenn Sie ein Failover für Ihr virtuelles Array durchführen:

* Bei einem geplanten Failover wird als bewährte Methode empfohlen, vor dem Initiieren des Failovers alle Volumes bzw. Freigaben offline zu schalten. Führen Sie die betriebssystemspezifischen Anleitungsschritte aus, um zuerst die Volumes/Freigaben auf dem Host offline zu schalten, und schalten Sie anschließend die Volumes/Freigaben auf Ihrem virtuellen Gerät offline.
* Für die Notfallwiederherstellung eines Dateiservers empfehlen wir Ihnen, für das Zielgerät den Beitritt zu derselben Domäne wie der Domäne der Quelle durchzuführen, damit die Freigabeberechtigungen automatisch aufgelöst werden. In dieser Version wird nur das Failover auf ein Zielgerät in derselben Domäne unterstützt.
* Nachdem die Notfallwiederherstellung erfolgreich abgeschlossen wurde, wird das Quellgerät automatisch gelöscht. Auch wenn das Gerät nicht mehr verfügbar ist, werden von der virtuellen Maschine, die Sie auf dem Hostsystem bereitgestellt haben, noch Ressourcen verbraucht. Es wird empfohlen, diesen virtuellen Computer aus Ihrem Hostsystem zu löschen, um zu verhindern, dass Kosten anfallen.
* Beachten Sie, dass **die Daten in der Cloud immer sicher sind** und dass dies auch gilt, wenn das Failover nicht erfolgreich durchgeführt werden kann. Sehen Sie sich die folgenden drei Szenarien an, in denen das Failover nicht erfolgreich abgeschlossen wurde:
  
  * Bei den ersten Schritten des Failovers ist ein Fehler aufgetreten, z.B. beim Durchführen der Vorabprüfungen für die Notfallwiederherstellung. In diesem Fall ist das Zielgerät weiterhin verwendbar. Sie können versuchen, das Failover auf demselben Zielgerät durchzuführen.
  * Während des eigentlichen Failoverprozesses ist ein Fehler aufgetreten. In diesem Fall wird das Gerät als nicht verwendbar gekennzeichnet. Sie müssen ein anderes virtuelles Zielarray bereitstellen und konfigurieren und für das Failover verwenden.
  * Das Failover wurde abgeschlossen, und das Quellgerät wurde gelöscht, aber das Zielgerät weist Fehler auf, und Sie können nicht auf Daten zugreifen. Die Daten sind in der Cloud weiterhin sicher und können leicht abgerufen werden, indem ein weiteres virtuelles Array erstellt und dann als Zielgerät für die Notfallwiederherstellung verwendet wird.

### <a name="deactivate"></a>Deaktivieren
Wenn Sie ein StorSimple Virtual Array deaktivieren, trennen Sie die Verbindung zwischen dem Gerät und dem entsprechenden StorSimple Manager-Dienst. Die Deaktivierung ist ein **endgültiger** Vorgang und kann nicht rückgängig gemacht werden. Ein deaktiviertes Gerät kann nicht wieder für den StorSimple Manager-Dienst registriert werden. Weitere Informationen finden Sie unter [Deaktivieren und Löschen eines StorSimple-Geräts](storsimple-virtual-array-deactivate-and-delete-device.md).

Beachten Sie beim Deaktivieren des virtuellen Arrays die folgenden bewährten Methoden:

* Erstellen Sie eine Cloudmomentaufnahme aller Daten, bevor Sie ein virtuelles Gerät deaktivieren. Wenn Sie ein virtuelles Array deaktivieren, gehen alle lokalen Gerätedaten verloren. Das Erstellen einer Cloudmomentaufnahme ermöglicht Ihnen die Wiederherstellung der Daten zu einem späteren Zeitpunkt.
* Vor dem Deaktivieren eines StorSimple Virtual Array müssen alle von diesem Gerät abhängigen Clients und Hosts beendet oder entfernt werden.
* Löschen Sie ein deaktiviertes Gerät, wenn Sie es nicht mehr verwenden, damit keine Gebühren anfallen.

### <a name="monitoring"></a>Überwachung
Um sicherzustellen, dass sich das StorSimple Virtual Array fortlaufend in einem fehlerfreien Zustand befindet, müssen Sie das Array überwachen und dafür sorgen, dass Sie vom System Informationen erhalten, z.B. Warnungen. Implementieren Sie die folgenden bewährten Methoden, um den Gesamtzustand des virtuellen Arrays zu überwachen:

* Konfigurieren Sie die Überwachung, um die Datenträgernutzung des virtuellen Arrays und für den Betriebssystem-Datenträger nachzuverfolgen. Bei Ausführung von Hyper-V können Sie eine Kombination aus System Center Virtual Machine Manager (SCVMM) und System Center Operations Manager verwenden, um die Virtualisierungshosts zu überwachen.
* Konfigurieren Sie E-Mail-Benachrichtigungen auf dem virtuellen Array, damit Benachrichtigungen für bestimmte Nutzungsebenen gesendet werden.                                                                                                                                                                                                

### <a name="index-search-and-virus-scan-applications"></a>Anwendungen für Indexsuche und Virenscan
Ein StorSimple Virtual Array kann Daten automatisch von der lokalen Ebene bis in die Microsoft Azure-Cloud staffeln. Wenn eine Anwendung verwendet wird (z.B. eine Indexsuche oder ein Virenscan), um die unter StorSimple gespeicherten Daten zu scannen, müssen Sie darauf achten, dass nicht auf die Clouddaten zugegriffen wird und dass sie nicht zurück auf die lokale Ebene verlagert werden.

Es wird empfohlen, die folgenden bewährten Methoden zu implementieren, wenn Sie die Indexsuche oder den Virenscan auf dem virtuellen Array konfigurieren:

* Deaktivieren Sie alle automatisch konfigurierten Vorgänge mit vollständiger Überprüfung.
* Konfigurieren Sie für mehrstufige Volumes die Anwendung für die Indexsuche oder den Virenscan, um einen inkrementellen Scan durchzuführen. Hierbei werden nur die neuen Daten gescannt, die sich normalerweise auf der lokalen Ebene befinden. Auf die Daten, die in der Cloud angeordnet werden, wird während eines inkrementellen Vorgangs nicht zugegriffen.
* Stellen Sie sicher, dass die richtigen Suchfilter und Einstellungen konfiguriert sind, damit nur die gewünschten Arten von Dateien gescannt werden. Beispielsweise sollten Bilddateien (JPEG, GIF und TIFF) und technische Zeichnungen während der inkrementellen oder vollständigen Indexneuerstellung nicht gescannt werden.

Beachten Sie die folgenden Richtlinien, wenn Sie den Windows-Indizierungsprozess verwenden:

* Verwenden Sie die Windows-Indizierung nicht für mehrstufige Volumes, da hierbei große Mengen von Daten (TB-Bereich) aus der Cloud abgerufen werden, wenn der Index häufig neu erstellt werden muss. Beim Neuerstellen des Index werden alle Arten von Dateien abgerufen, um ihren Inhalt zu indizieren.
* Verwenden Sie die Windows-Indizierung für lokale Volumes, da hierbei nur auf Daten auf den lokalen Ebenen zugegriffen wird, um den Index zu erstellen (auf Clouddaten wird nicht zugegriffen).

### <a name="byte-range-locking"></a>Bytebereichsperren
Anwendungen können einen angegebenen Bytebereich in den Dateien sperren. Wenn Bytebereichsperren für die Anwendungen aktiviert sind, die in StorSimple schreiben, funktioniert die Anordnung auf Ebenen für Ihr virtuelles Array nicht. Damit diese Anordnung funktioniert, müssen alle Bereiche der Dateien, auf die zugegriffen wird, entsperrt werden. Bytebereichsperren werden für mehrstufige Volumes im virtuellen Array nicht unterstützt.

Empfohlene Maßnahmen zur Entschärfung von Bytebereichsperren sind:

* Deaktivieren Sie Bytebereichsperren in der Anwendungslogik.
* Verwenden Sie lokale Volumes (anstelle von mehrstufigen Volumes) für die Daten, die der Anwendung zugeordnet sind. Lokale Volumes werden nicht in der Cloud angeordnet.
* Wenn Sie lokale Volumes mit Bytebereichsperren verwenden, kann das Volume unter Umständen online geschaltet werden, bevor die Wiederherstellung abgeschlossen ist. In diesen Fällen müssen Sie warten, bis der Wiederherstellungsvorgang abgeschlossen ist.

## <a name="multiple-arrays"></a>Mehrere Arrays
Ggf. müssen mehrere virtuelle Arrays bereitgestellt werden, um einen wachsenden Arbeitssatz mit Daten abzudecken, die in die Cloud „überlaufen“ und daher die Leistung des Geräts beeinträchtigen können. In diesen Fällen ist es ratsam, Geräte zu skalieren, wenn sich der Arbeitssatz vergrößert. Hierfür ist es erforderlich, im lokalen Rechenzentrum mindestens ein weiteres Gerät hinzuzufügen. Beim Hinzufügen der Geräte haben Sie folgende Möglichkeiten:

* Teilen Sie den aktuellen Satz mit Daten.
* Stellen Sie neue Workloads für neue Geräte bereit.
* Für die Bereitstellung mehrerer virtueller Arrays empfehlen wir, das Array aus Sicht des Lastenausgleichs auf mehrere Hypervisor-Hosts aufzuteilen.
* Mehrere virtuelle Arrays (bei Konfiguration als Dateiserver oder iSCSI-Server) können in einem Namespace für das verteilte Dateisystem (Distributed File System, DFS) bereitgestellt werden. Ausführliche Schritte finden Sie unter [Distributed File System Namespace Solution with Hybrid Cloud Storage Deployment Guide](https://www.microsoft.com/download/details.aspx?id=45507)(DFS-Namespacelösung mit Hybridcloudspeicher – Leitfaden zur Bereitstellung). Die DFS-Replikation ist für die Verwendung mit dem virtuellen Array derzeit nicht zu empfehlen. 

## <a name="see-also"></a>Weitere Informationen
Informieren Sie sich über das [Verwalten des StorSimple Virtual Array mithilfe des StorSimple Manager-Diensts](storsimple-virtual-array-manager-service-administration.md) .