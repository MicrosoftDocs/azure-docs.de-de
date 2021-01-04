---
title: Wie funktioniert die Hyper-V-Migration in Azure Migrate?
description: Hier wird die Hyper-V-Migration mit Azure Migrate erläutert.
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 239918cc19eefbef9e3c3f12d5ddd3bb5434b490
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96751018"
---
# <a name="how-does-hyper-v-replication-work"></a>Wie funktioniert die Hyper-V-Replikation?

Dieser Artikel bietet eine Übersicht über die Architektur und die Prozesse, die beim Migrieren von Hyper-V-VMs mit dem Azure Migrate-Servermigrationstool verwendet werden.

[Azure Migrate](migrate-services-overview.md) ist ein zentraler Hub zum Nachverfolgen der Ermittlung, Bewertung und Migration Ihrer lokalen Apps und Workloads sowie von VMs in der privaten/öffentlichen Cloud zu Azure. Der Hub stellt Azure Migrate-Tools für die Bewertung und Migration sowie Angebote von unabhängigen Drittanbietern (Independent Software Vendors, ISVs) bereit.

## <a name="agentless-migration"></a>Migration ohne Agent

Das Azure Migrate-Servermigrationstool ermöglicht die Replikation ohne Agents für lokale Hyper-V-VMs mithilfe eines für Hyper-V optimierten Migrationsworkflows. Sie installieren nur auf Hyper-V-Hosts oder Clusterknoten einen Software-Agent. Auf Hyper-V-VMs muss nichts installiert werden.

## <a name="server-migration-and-azure-site-recovery"></a>Servermigration und Azure Site Recovery

Azure Migrate-Servermigration ist ein Tool zum Migrieren lokaler Workloads und cloudbasierter VMS zu Azure. Site Recovery ist ein Tool für die Notfallwiederherstellung. Die Tools verfügen über einige gemeinsame Technologiekomponenten, die für die Datenreplikation verwendet werden, aber erfüllen unterschiedliche Zwecke. 


## <a name="architectural-components"></a>Komponenten der Architektur

![Das Diagramm zeigt ein Hyper-V-Quellnetzwerk mit einem HTTPS-Datenkanal zu Microsoft Azure. Details dazu werden in einer Tabelle erläutert.](./media/hyper-v-replication-architecture/architecture.png)



**Komponente** | **Bereitstellung** | 
--- | --- 
**Replikationsanbieter** | Der Microsoft Azure Site Recovery-Anbieter wird auf Hyper-V-Hosts installiert und bei der Azure Migrate-Servermigration registriert.<br/> Der Anbieter orchestriert die Replikation für Hyper-V-VMs.
**Recovery Services-Agent** | Der Microsoft Azure Recovery Services-Agent verarbeitet die Datenreplikation. Es arbeitet mit dem Anbieter zusammen, um Daten von Hyper-V-VMs nach Azure zu replizieren.<br/> Die replizierten Daten werden in ein Speicherkonto Ihres Azure-Abonnements hochgeladen. Das Servermigrationstool verarbeitet dann die replizierten Daten und wendet sie auf Replikatdatenträger im Abonnement an. Die Replikatdatenträger werden bei der Migration zum Erstellen der Azure-VMs verwendet.

- Komponenten werden über eine einzige Setupdatei installiert und aus „Azure Migrate-Servermigration“ im Portal heruntergeladen.
- Der Anbieter und die Appliance verwenden ausgehende HTTPS-Port 443-Verbindungen für die Kommunikation mit Azure Migrate-Servermigration.
- Sowohl die Kommunikation vom Anbieter als auch vom Agent ist sicher und verschlüsselt.


## <a name="replication-process"></a>Replikationsprozess

1. Wenn Sie die Replikation für eine Hyper-V-VM aktivieren, beginnt die erste Replikation.
2. Eine Hyper-V-VM-Momentaufnahme wird erstellt.
3. VHDs auf der VM werden nacheinander repliziert, bis alle in Azure kopiert wurden. Die Dauer der ersten Replikation hängt von der VM-Größe und der Netzwerkbandbreite ab.
4. Datenträgeränderungen, die während der ersten Replikation auftreten, werden mithilfe des Hyper-V-Replikats nachverfolgt und in Protokolldateien (HRL-Dateien) gespeichert.
    - Die Protokolldateien befinden sich im gleichen Ordner wie die Datenträger.
    - Jeder Datenträger verfügt über eine zugeordnete HRL-Datei, die an den sekundären Speicher gesendet wird.
    - Beachten Sie, dass die Momentaufnahme- und Protokolldateien Festplattenressourcen belegen, während die anfängliche Replikation durchgeführt wird.
4. Nach Abschluss der ersten Replikation wird die Momentaufnahme des virtuellen Computers gelöscht, und die Deltareplikation beginnt.
5. Inkrementelle Datenträgeränderungen werden in HRL-Dateien verfolgt. Replikationsprotokolle werden vom Recovery Services-Agent in regelmäßigen Abständen in ein Azure Storage-Konto hochgeladen.


## <a name="performance-and-scaling"></a>Leistung und Skalierung

Die Replikationsleistung für Hyper-V wird von Faktoren wie der VM-Größe, den Datenänderungsraten der VMs, dem auf dem Hyper-V-Host für die Speicherung von Protokolldateien verfügbaren Speicherplatz, der Uploadbandbreite für Replikationsdaten und dem Zielspeicher in Azure beeinflusst.

- Wenn Sie mehrere Computer gleichzeitig replizieren, verwenden Sie den [Azure Site Recovery-Bereitstellungsplaner](../site-recovery/hyper-v-deployment-planner-overview.md) für Hyper-V, um die Replikation zu optimieren.
- Planen Sie Ihre Hyper-V-Replikation, und verteilen Sie die Replikation abhängig von den Kapazitäten auf Azure Storage-Konten.

### <a name="control-upload-throughput"></a>Steuern des Uploaddurchsatzes

Sie können die Bandbreite, die zum Hochladen von Daten in Azure verwendet wird, auf jedem Hyper-V-Host einschränken. Aber Vorsicht! Wenn Sie die Werte zu niedrig festlegen, wirkt sich dies negativ auf die Replikation aus und verzögert die Migration.


1. Melden Sie sich beim Hyper-V-Host oder -Clusterknoten an.
2. Führen Sie **C:\Programme\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc** aus, um das MMC-Snap-In Windows Azure Backup zu öffnen.
3. Wählen Sie im Snap-In **Eigenschaften ändern** aus.
4. Wählen Sie unter **Drosselung** die Option **Internet-Bandbreiteneinschränkung für Sicherungsvorgänge aktivieren** aus. Legen Sie die Grenzwerte für Arbeitsstunden und arbeitsfreie Stunden fest. Der gültige Bereich reicht von 512 KBit/s bis 1,023 MBit/s.
I

### <a name="influence-upload-efficiency"></a>Beeinflussen der Effizienz beim Hochladen

Wenn Sie über freie Bandbreite für die Replikation verfügen und Uploads beschleunigen möchten, können Sie die Anzahl der für den Uploadtask reservierten Threads wie folgt erhöhen:

1. Öffnen Sie die Registrierung mit „Regedit“.
2. Navigieren Sie zum Schlüssel „HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM“.
3. Erhöhen Sie den Wert für die Anzahl der Threads, die für den Datenupload für jede replizierende VM verwendet werden. Der Standardwert beträgt 4, der Maximalwert 32. 




## <a name="next-steps"></a>Nächste Schritte

Testen der [Hyper-V-Migration](tutorial-migrate-hyper-v.md) unter Verwendung von Azure Migrate-Servermigration.
