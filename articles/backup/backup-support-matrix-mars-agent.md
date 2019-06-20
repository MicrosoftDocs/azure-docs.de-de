---
title: Supportmatrix für die Sicherung mit Azure Backup von Computern, auf denen der Microsoft Azure Recovery Services (MARS)-Agents ausgeführt wird
description: Dieser Artikel enthält eine Übersicht über die Azure Backup-Unterstützung beim Sichern von Computern, auf denen der MARS-Agent (Microsoft Azure Recovery Services) ausgeführt wird.
services: backup
author: rayne-wiselman
ms.service: backup
ms.date: 02/17/2019
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 9799914cdabf1f64fccfd6bfd891f9498b860e39
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64923004"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Supportmatrix für die Sicherung mit dem Microsoft Azure Recovery Services (MARS)-Agent

Mit dem [Azure Backup-Dienst](backup-overview.md) können Sie lokale Computer und Apps sowie virtuelle Azure-Computer (VMs) sichern. Dieser Artikel enthält eine Übersicht über die Supporteinstellungen und Einschränkungen beim Sichern von Computern mithilfe des MARS-Agents (Microsoft Azure Recovery Services).

## <a name="the-mars-agent"></a>Der MARS-Agent

Der MARS-Agent wird von Azure Backup verwendet, um Daten von lokalen Computern und Azure-VMs in einem Backup Recovery Services-Tresor in Azure zu sichern. Mit dem MARS-Agent haben Sie folgende Möglichkeiten:
- Sie können den Agent auf lokalen Windows-Computern ausführen, sodass diese direkt in einem Backup Recovery Services-Tresor in Azure gesichert werden können.
- Sie können den Agent auf Windows-VMs ausführen, sodass diese direkt in einem Tresor gesichert werden können.
- Sie können den Agent auf einem MABS-Server (Microsoft Azure Backup Server) oder System Center DPM-Server (Data Protection Manager) ausführen. In diesem Szenario werden Computer und Workloads auf einem MABS- oder DPM-Server gesichert. Anschließend wird dieser Server vom MARS-Agent in einem Tresor in Azure gesichert.

Welche Möglichkeiten zur Sicherung bestehen, hängt davon ab, welcher Agent installiert ist. Weitere Informationen finden Sie unter [Architektur: Direkte Sicherung von lokalen Windows Server-Computern oder Azure-VM-Dateien oder -Ordnern](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders). Informationen zur Architektur für die Sicherung mit MABS und DPM finden Sie unter [Architektur: Sicherung mit DPM/MABS](backup-architecture.md#architecture-back-up-to-dpmmabs). Weitere Informationen zur Sicherungsarchitektur finden Sie auch unter den [Anforderungen](backup-support-matrix-mabs-dpm.md).

**Installation** | **Details**
--- | ---
Aktuellen MARS-Agent herunterladen | Sie können die neueste Version des Agenten aus dem Tresor herunterladen oder [direkt herunterladen](https://aka.ms/azurebackup_agent).
Direkt auf dem Computer installieren | Sie können den MARS-Agent direkt auf einem lokalen Windows-Server oder einem virtuellen Windows-Computer installieren, auf dem eines der [unterstützten Betriebssysteme](https://docs.microsoft.com/azure/backup/backup-support-matrix-mabs-dpm#supported-mabs-and-dpm-operating-systems) ausgeführt wird.
Auf einem Sicherungsserver installieren | Wenn Sie DPM oder MABS für die Sicherung auf Azure einrichten, laden Sie den MARS-Agent herunter und installieren ihn auf dem Server. Sie können den Agent gemäß den [unterstützten Betriebssystemen](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) in der Supportmatrix des Sicherungsservers installieren.

> [!NOTE]
> Standardmäßig verfügen Azure-VMs, die für die Sicherung aktiviert sind, über eine Azure Backup-Erweiterungsinstallation. Diese Erweiterung sichert die gesamte VM. Sie können den MARS-Agent auf einer Azure-VM neben der Erweiterung installieren und ausführen, wenn Sie nur bestimmte Ordner und Dateien und nicht die gesamte VM sichern möchten.
> Wenn Sie den MARS-Agent auf einer Azure-VM ausführen, werden Dateien oder Ordner gesichert, die sich im temporären Speicher auf der VM befinden. Sicherungen schlagen fehl, wenn die Dateien oder Ordner aus dem temporären Speicher entfernt werden oder wenn der temporäre Speicher entfernt wird.


## <a name="cache-folder-support"></a>Unterstützung für Cacheordner

Wenn Sie Daten mit dem MARS-Agent sichern, wird eine Momentaufnahme der Daten erstellt und in einem lokalen Cacheordner speichert, bevor die Daten vom Agent an Azure gesendet werden. Für den Cacheordner (neu) gelten verschiedene Anforderungen:

**Cache** | **Details**
--- | ---
Größe |  Der freie Speicherplatz im Cacheordner muss mindestens 5 bis 10 Prozent der Gesamtgröße Ihrer Sicherungsdaten betragen.
Location | Der Cacheordner muss lokal auf dem Computer gespeichert werden, der gesichert wird, und er muss online sein. Der Cacheordner darf sich nicht in einer Netzwerkfreigabe, auf Wechselmedien oder in einem Offline-Volume befinden.
Ordner | Der Cacheordner muss verschlüsselt sein und sich auf einem deduplizierten Volume oder in einem Ordner befinden, der komprimiert ist, eine geringe Dichte aufweist oder einen Analysepunkt hat.
Andere Speicherorte | Sie können den Cachespeicherort ändern, indem Sie die Sicherungs-Engine beenden (`net stop bengine`) und den Cacheordner in ein neues Laufwerk kopieren. (Stellen Sie sicher, dass genügend Platz vorhanden ist.) Dann aktualisieren Sie zwei Registrierungseinträge unter **HKLM\SOFTWARE\Microsoft\Windows Azure Backup** (**Config/ScratchLocation** und **Config/CloudBackupProvider/ScratchLocation**) auf den neuen Speicherort und starten die Engine neu.

## <a name="networking-and-access-support"></a>Netzwerk und Zugriffsunterstützung

### <a name="url-access"></a>URL-Zugriff

Der MARS-Agent benötigt Zugriff auf diese URLs:

- http://www.msftncsi.com/ncsi.txt
- *.Microsoft.com
- *.WindowsAzure.com
- *.MicrosoftOnline.com
- *.Windows.net

### <a name="throttling-support"></a>Unterstützung für Drosselung

**Feature** | **Details**
--- | ---
Bandbreitensteuerung | Unterstützt. Verwenden Sie im MARS-Agent **Eigenschaften ändern**, um die Bandbreite anzupassen.
Netzwerkdrosselung | Nicht verfügbar für gesicherte Computer, auf denen Windows Server 2008 R2, Windows Server 2008 SP2 oder Windows 7 ausgeführt wird.

## <a name="support-for-direct-backups"></a>Unterstützung für direkte Sicherungen

Mit dem MARS-Agent können Sie unter einigen Betriebssystemen, die auf lokalen Computern und Azure-VMs ausgeführt werden, direkt in Azure sichern. Dabei muss es sich um 64-Bit-Betriebssysteme handeln, die mit den neuesten Service Packs und Updates ausgeführt werden müssen. Eine Übersicht über diese Betriebssysteme finden in der folgenden Tabelle:

**Betriebssystem** | **Dateien/Ordner** | **Systemstatus**
--- | --- | ---
Windows 10 (Enterprise, Pro, Home) | Ja | Nein
Windows 8.1 (Enterprise, Pro)| Ja |Nein
Windows 8 (Enterprise, Pro) | Ja | Nein
Windows 7 (Ultimate, Enterprise, Pro, Home Premium/Basic, Starter) | Ja | Nein
Windows Server 2016 (Standard, Datacenter, Essentials) | Ja | Ja
Windows Server 2012 R2 (Standard, Datacenter, Foundation, Essentials) | Ja | Ja
Windows Server 2012 (Standard, Datacenter, Foundation) | Ja | Ja
Windows Server 2008 R2 (Standard, Enterprise, Datacenter, Foundation) | Ja | Ja
Windows Server 2008 SP2 (Standard, Datacenter, Foundation) | Ja | Nein
Windows Storage Server 2016/2012 R2/2012 (Standard, Workgroup) | Ja | Nein

Weitere Informationen finden Sie unter [Unterstützte MABS- und DPM-Betriebssysteme](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

## <a name="backup-limits"></a>Backup-Grenzwerte

Bei Azure Backup ist die Größe einer Datei- oder Ordnerdatenquelle, die gesichert werden kann, begrenzt. Die Elemente, die von einem einzelnen Volume gesichert werden, dürfen die in der folgenden Tabelle angegebenen Größen nicht überschreiten:

**Betriebssystem** | **Größenlimit**
--- | ---
Windows Server 2012 oder höher |  54\.400 GB
Windows Server 2008 R2 SP1 |    1\.700 GB
Windows Server 2008 SP2 | 1\.700 GB
Windows 8 oder höher  | 54\.400 GB
Windows 7   | 1\.700 GB


## <a name="supported-file-types-for-backup"></a>Unterstützte Dateitypen für die Sicherung

**Typ** | **Unterstützung**
--- | ---
Verschlüsselt   | Unterstützt.
Komprimiert | Unterstützt.
Platzsparend | Unterstützt.
Komprimiert und geringe Dichte | Unterstützt.
Feste Links  | Nicht unterstützt. Übersprungen.
Analysepunkt   | Nicht unterstützt. Übersprungen.
Verschlüsselt und geringe Dichte |  Nicht unterstützt. Übersprungen.
Komprimierter Stream   | Nicht unterstützt. Übersprungen.
Platzsparender Stream   | Nicht unterstützt. Übersprungen.
OneDrive (synchronisierte Dateien sind Streams mit geringer Dichte)  | Nicht unterstützt.

## <a name="supported-drives-or-volumes-for-backup"></a>Unterstützte Laufwerke oder Volumes für die Sicherung

**Laufwerk/Volume** | **Unterstützung** | **Details**
--- | --- | ---
Schreibgeschützte Volumes   | Nicht unterstützt | Der Volumeschattenkopie-Dienst (VSS) funktioniert nur, wenn das Volume beschreibbar ist.
Offlinevolumes | Nicht unterstützt |   VSS funktioniert nur, wenn das Volume online ist.
Netzwerkfreigabe   | Nicht unterstützt |   Das Volume muss sich lokal auf dem Server befinden.
Mit BitLocker geschützte Volumes | Nicht unterstützt |   Das Volume muss entsperrt werden, damit die Sicherung starten kann.
Dateisystemidentifikation  | Nicht unterstützt |   Es wird nur NTFS unterstützt.
Wechselmedien | Nicht unterstützt |   Alle Quellen für Sicherungselemente müssen ein *festes* Medium sein.
Deduplizierte Laufwerke | Unterstützt | Azure Backup konvertiert die deduplizierten Daten in reguläre Daten. Dabei werden die Daten optimiert, verschlüsselt, gespeichert und an den Tresor gesendet.

## <a name="support-for-initial-offline-backup"></a>Unterstützung für die erste Offlinesicherung

Azure Backup unterstützt *Offline-Seeding*, um erste Sicherungsdaten über Datenträger an Azure zu übertragen. Dieser Support ist hilfreich, wenn Ihre erste Sicherung wahrscheinlich im Größenbereich von Terabyte (TBs) liegt. Die Offlinesicherung wird unterstützt für:

- Direkte Sicherung von Dateien und Ordnern auf lokalen Computern, auf denen der MARS-Agent ausgeführt wird.
- Sicherung von Workloads und Dateien von einem DPM-Server oder MABS.

Die Offlinesicherung kann nicht für Systemstatusdateien verwendet werden.

## <a name="support-for-data-restoration"></a>Support für die Datenwiederherstellung

Mithilfe der Funktion [Sofortige Wiederherstellung](backup-instant-restore-capability.md) von Azure Backup können Sie Daten vor dem Kopieren in den Tresor wiederherstellen. Auf dem Computer, den Sie sichern, muss .NET Framework 4.5.2 oder höher ausgeführt werden.

Sicherungen können nicht auf einem Zielcomputer wiederhergestellt werden, auf dem eine ältere Version des Betriebssystems ausgeführt wird. So kann eine Sicherung, die von einem Computer erstellt wurde, auf dem Windows 7 ausgeführt wird, beispielsweise unter Windows 8 oder höher wiederhergestellt werden. Eine Sicherung, die von einem Computer erstellt wurde, auf dem Windows 8 ausgeführt wird, kann dagegen nicht auf einem Computer wiederhergestellt werden, auf dem Windows 7 ausgeführt wird.

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über die [Sicherungsarchitektur, für die der MARS-Agent verwendet wird](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
- Erfahren Sie, was unterstützt wird, wenn Sie [den MARS-Agent auf einem MABS- oder DPM-Server ausführen](backup-support-matrix-mabs-dpm.md).
