---
title: Was ist Azure Backup?
description: Hier finden Sie eine Übersicht über den Azure Backup-Dienst und erfahren, wie er zu Ihrer Strategie für Geschäftskontinuität und Notfallwiederherstellung (Business Continuity & Disaster Recovery, BCDR) beiträgt.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: overview
ms.date: 04/24/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 9e926ca2625f98522652ae7e7d245ecf2ed576c4
ms.sourcegitcommit: 6932af4f4222786476fdf62e1e0bf09295d723a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/05/2019
ms.locfileid: "66688717"
---
# <a name="what-is-azure-backup"></a>Was ist Azure Backup?

Der Azure Backup-Dienst dient zum Sichern von Daten in der Microsoft Azure-Cloud. Sie können sowohl lokale Computer und Workloads als auch virtuelle Azure-Computer (Virtual Machines, VMs) sichern.


## <a name="why-use-azure-backup"></a>Gründe für Azure Backup

Azure Backup bietet im Wesentlichen folgende Vorteile:

- **Auslagerung lokaler Sicherungen:** Azure Backup bietet eine einfache Lösung, mit der Sie Ihre lokalen Ressourcen in der Cloud sichern können. Mit Azure Backup erhalten Sie kurz- und langfristige Sicherungen, ohne komplexe lokale Sicherungslösungen einrichten zu müssen.
- **Sicherung virtueller Azure-IaaS-Computer:** Azure Backup bietet unabhängige und isolierte Sicherungen zum Schutz vor dem versehentlichen Löschen von Originaldaten. Sicherungen werden in einem Recovery Services-Tresor mit integrierten verwalteten Wiederherstellungspunkten gespeichert. Konfiguration und Skalierung sind unkompliziert. Sicherungen werden außerdem optimiert und können bei Bedarf problemlos wiederhergestellt werden.
- **Unkomplizierte Skalierung:** Dank der Leistung und unbegrenzten Skalierbarkeit der Azure-Cloud bietet Azure Backup Hochverfügbarkeit ohne Wartungs- oder Überwachungsaufwand.
- **Unbegrenzte Datenübertragungen:** Die Menge an übertragenen eingehenden und ausgehenden Daten wird von Azure Backup nicht beschränkt, und es fallen keine Kosten für die übertragenen Daten an.
    - Ausgehende Daten sind Daten, die während eines Wiederherstellungsvorgangs aus einem Recovery Services-Tresor übertragen werden.
    - Wenn Sie mit dem Azure Import/Export-Dienst eine erste Sicherung im Offlinemodus ausführen, um große Datenmengen zu importieren, fallen Kosten für eingehende Daten an.  [Weitere Informationen](backup-azure-backup-import-export.md)
- **Datenschutz:** Azure Backup bietet Lösungen zur Sicherung von Daten während der Übertragung und im Ruhezustand.
- **Anwendungskonsistente Sicherungen:** Eine anwendungskonsistente Sicherung bedeutet, dass ein Wiederherstellungspunkt alle erforderlichen Daten zum Wiederherstellen der Sicherungskopie enthält. Azure Backup umfasst anwendungskonsistente Sicherungen, sodass sichergestellt ist, dass zum Wiederherstellen der Daten keine zusätzlichen Fixes benötigt werden. Durch die Wiederherstellung von anwendungskonsistenten Daten wird die Wiederherstellungsdauer reduziert, sodass Sie schnell zum Zustand der normalen Ausführung zurückkehren können.
- **Kurz- und Langzeitaufbewahrung von Daten:** Sie können Recovery Services-Tresore für die kurzfristige und langfristige Datenaufbewahrung verwenden. Die Zeit, für die Sie Daten im Recovery Services-Tresor aufbewahren können, wird von Azure nicht begrenzt. Daten können also beliebig lange aufbewahrt werden. Bei Azure Backup gilt pro geschützter Instanz ein Limit von 9999 Wiederherstellungspunkten. 
- **Automatische Speicherverwaltung**: Für Hybridumgebungen ist häufig heterogener Speicher erforderlich – teilweise lokal und teilweise in der Cloud. Bei Azure Backup fallen keine Kosten für die Verwendung von lokalen Speichergeräten an. Azure Backup sorgt im Rahmen eines Modells mit nutzungsbasierter Bezahlung für die automatische Zuteilung und Verwaltung von Sicherungsspeicher. Dadurch zahlen Sie nur für den Speicher, den Sie tatsächlich verwenden. [Erfahren Sie mehr](https://azure.microsoft.com/pricing/details/backup) zu den Preisen.
- **Mehrere Speicheroptionen:** Azure Backup bietet zwei Replikationsarten, um die Hochverfügbarkeit Ihres Speichers bzw. Ihrer Daten sicherzustellen.
    - [Lokal redundanter Speicher (Locally Redundant Storage, LRS)](../storage/common/storage-redundancy-lrs.md) repliziert Ihre Daten dreimal in einer Speicherskalierungseinheit in einem Datencenter. (Es werden also drei Kopien Ihrer Daten erstellt.) Alle Kopien der Daten befinden sich in derselben Region. LRS ist eine kostengünstige Möglichkeit, um Daten vor lokalen Hardwarefehlern zu schützen.
    - [Geografisch redundanter Speicher (Geo-Redundant Storage, GRS)](../storage/common/storage-redundancy-grs.md) ist die standardmäßige und empfohlene Replikationsoption. GRS repliziert Ihre Daten in einer sekundären Region, die mehrere hundert Kilometer vom primären Speicherort der Quelldaten entfernt ist. GRS führt zu höheren Kosten als LRS, bietet aber eine höhere Haltbarkeit Ihrer Daten (auch im Falle eines regionalen Ausfalls).


## <a name="whats-the-difference-between-azure-backup-and-azure-site-recovery"></a>Was ist der Unterschied zwischen Azure Backup und Azure Site Recovery?

Der Azure Backup-Dienst und der Azure Site Recovery-Dienst tragen beide zur Strategie für Geschäftskontinuität und Notfallwiederherstellung (Business Continuity & Disaster Recovery, BCDR) Ihres Unternehmens bei. BCDR hat zwei weit gefasste Ziele:

- Schützen Ihrer Geschäftsdaten und Ermöglichen der Wiederherstellung bei einem Ausfall
- Gewährleisten der Verfügbarkeit von Apps und Workloads während geplanter und ungeplanter Downtime

Beide Dienste bieten unterschiedliche Funktionen, die sich gegenseitig ergänzen.

- **Azure Site Recovery**: Site Recovery bietet eine Notfallwiederherstellungslösung für lokale Computer und virtuelle Azure-Computer. Hierzu werden Computer eines primären Standorts an einem sekundären Standort repliziert. Bei einem Notfall wird für die Computer ein Failover zum sekundären Standort ausgeführt, sodass Sie von dort aus auf sie zugreifen können. Wenn später alles wieder wie gewohnt funktioniert, wird für die Computer ein Failback ausgeführt, um sie am primären Standort wiederherzustellen.
- **Azure Backup**: Der Azure Backup-Dienst sichert Daten von lokalen Computern und virtuellen Azure-Computern. Daten können präzise gesichert und wiederhergestellt werden. So können Sie unter anderem Dateien, Ordner und den Systemstatus des Computers sichern sowie App-fähige Datensicherungen erstellen. Azure Backup ermöglicht einen präziseren Umgang mit Daten als Site Recovery. Wenn also beispielsweise eine Präsentation auf Ihrem Laptop beschädigt wurde, können Sie sie mithilfe von Azure Backup wiederherstellen. Mit Site Recovery können Sie eine VM-Konfiguration und die Daten des virtuellen Computers schützen und deren Verfügbarkeit sicherstellen.  

Die folgende Tabelle enthält hilfreiche Punkte, um Sie bei der Ermittlung Ihrer BCDR-Anforderungen zu unterstützen:

**Ziel** | **Details** | **Vergleich**
--- | --- | ---
**Datensicherung/-aufbewahrung** | Sicherungsdaten können mehrere Tage, Monate oder sogar Jahre aufbewahrt werden, falls dies aus Compliancegründen erforderlich ist. | Mit Sicherungslösungen wie Azure Backup können Sie exakt die Daten auswählen, die Sie sichern möchten, und die Sicherungs- und Aufbewahrungsrichtlinien präzise anpassen.<br/><br/> Diese Präzision steht bei Site Recovery nicht zur Verfügung.
**Recovery Point Objective (RPO)** | Dies ist der Umfang des zulässigen Datenverlusts, falls eine Wiederherstellung durchgeführt werden muss. | Bei Sicherungen kann das RPO variieren.<br/><br/> VM-Sicherungen haben in der Regel ein RPO von einem Tag, während es bei Datenbanksicherungen durchaus bei lediglich 15 Minuten liegen kann.<br/><br/> Site Recovery bietet ein niedriges RPO, da die Replikation kontinuierlich oder häufig stattfindet, was für ein geringes Delta zwischen Quelle und Replikatkopie sorgt.
**Recovery Time Objective (RTO)** |Der Zeitraum, der für eine Wiederherstellung erforderlich ist. | Aufgrund des höheren RPO-Werts ist die Datenmenge, die eine Sicherungslösung verarbeiten muss, meist wesentlich größer, was zu längeren RTOs führt. Beispielsweise kann das Wiederherstellen von Daten von Bändern Tage dauern, was davon abhängt, wie lange der Transport des Bands von einem standortexternen Aufbewahrungsort dauert.

## <a name="what-backup-scenarios-are-supported"></a>Welche Sicherungsszenarien werden unterstützt?

Mit Azure Backup können sowohl lokale Computer als auch virtuelle Azure-Computer gesichert werden.

**Computer** | **Sicherungsszenario**
--- | ---
**Lokale Sicherung** |  (1) Führen Sie den MARS-Agent (Microsoft Azure Recovery Services) von Azure Backup auf einem lokalen Windows-Computer aus, um einzelne Dateien und den Systemzustand zu sichern. <br/><br/>(2) Sichern Sie lokale Computer auf einem Sicherungsserver (System Center Data Protection Manager (DPM) oder Microsoft Azure Backup Server (MABS)), und konfigurieren Sie dann den Sicherungsserver für die Sicherung in einem Azure Backup Recovery Services-Tresor in Azure.
**Virtuelle Azure-Computer** | (1) Aktivieren Sie die Sicherung für einzelne virtuelle Azure-Computer. Wenn Sie die Sicherung aktivieren, installiert Azure Backup eine Erweiterung für den Azure-VM-Agent, der auf dem virtuellen Computer ausgeführt wird. Der Agent sichert den gesamten virtuellen Computer.<br/><br/> 2) Führen Sie den MARS-Agent auf einem virtuellen Azure-Computer aus. Diese Vorgehensweise ist hilfreich, wenn Sie einzelne Dateien und Ordner auf dem virtuellen Computer sichern möchten.<br/><br/> (3) Sichern Sie einen virtuellen Azure-Computer auf einem in Azure ausgeführten Server (DPM-Server oder MABS). Sichern Sie dann den DPM-Server/MABS mithilfe von Azure Backup in einem Tresor.


## <a name="why-use-a-backup-server"></a>Gründe für die Verwendung eines Sicherungsservers
Die Sicherung von Computern und Apps in MABS/DPM-Speicher mit anschließender Sicherung des DPM/MABS-Speichers in einem Tresor hat folgende Vorteile:

- Die Sicherung in MABS/DPM ermöglicht neben Datei-/Ordner-/Volumesicherungen und Sicherungen des Computerzustands (Bare-Metal-/Systemstatussicherungen) auch App-fähige Sicherungen, die für gängige Apps wie SQL Server, Exchange und SharePoint optimiert sind.
- Bei lokalen Computern müssen Sie den MARS-Agent nicht auf jedem Computer installieren, den Sie sichern möchten. Auf den einzelnen Computern wird jeweils der DPM/MABS-Schutz-Agent ausgeführt, und der MARS-Agent wird nur in der MABS/DPM-Instanz ausgeführt.
- Sie profitieren von mehr Flexibilität und präziseren Planungsoptionen für die Sicherungsausführung.
- Sie können Sicherungen für mehrere Computer in einer zentralen Konsole verwalten und die Computer dazu in Schutzgruppen zusammenfassen. Das ist besonders hilfreich, wenn Apps auf mehrere Computer verteilt sind und gemeinsam gesichert werden sollen.

Informieren Sie sich ausführlicher über die [Funktionsweise der Sicherung](backup-architecture.md#architecture-back-up-to-dpmmabs) bei Verwendung eines Sicherungsservers sowie über die [Supportanforderungen](backup-support-matrix-mabs-dpm.md) für Sicherungsserver.

## <a name="what-can-i-back-up"></a>Was kann gesichert werden?

**Computer** | **Sicherungsmethode** | **Sichern**
--- | --- | ---
**Lokale virtuelle Windows-Computer** | Ausführen des MARS-Agents | Sicherung von Dateien, Ordnern und Systemstatus<br/><br/> Linux-Computer werden nicht unterstützt.
**Lokale Computer** | Sicherung mit DPM/MABS | Sicherung von allem, was durch [DPM](backup-support-matrix-mabs-dpm.md#supported-backups-to-dpm) oder [MABS](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs) geschützt wird (einschließlich Dateien, Ordner, Freigaben, Volumes und App-spezifische Daten)
**Virtuelle Azure-Computer** | Ausführen der Sicherungserweiterung des Azure-VM-Agents | Sicherung des gesamten virtuellen Computers
**Virtuelle Azure-Computer** | Ausführen des MARS-Agents | Sicherung von Dateien, Ordnern und Systemstatus<br/><br/> Linux-Computer werden nicht unterstützt.
**Virtuelle Azure-Computer** | Sicherung mit MABS/DPM (ausgeführt in Azure) | Sicherung von allem, was durch [MABS](backup-support-matrix-mabs-dpm.md#supported-backups-to-mabs) oder [DPM](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-1807) geschützt wird (einschließlich Dateien, Ordner, Freigaben, Volumes und App-spezifische Daten)

## <a name="what-backup-agents-do-i-need"></a>Welche Sicherungs-Agents benötige ich?

**Szenario** | **Agent**
--- | ---
**Sichern virtueller Azure-Computer** | Kein Agent erforderlich. Die Azure-VM-Erweiterung für die Sicherung wird auf dem virtuellen Azure-Computer installiert, wenn Sie die erste Azure-VM-Sicherung ausführen.<br/><br/> Diese Option wird für Windows und Linux unterstützt.
**Sichern lokaler Windows-Computer** | Für dieses Szenario muss der MARS-Agent direkt auf den Computer heruntergeladen und dort installiert und ausgeführt werden.
**Sichern virtueller Azure-Computer mit dem MARS-Agent** | Für dieses Szenario muss der MARS-Agent direkt auf den Computer heruntergeladen und dort installiert und ausgeführt werden. Der MARS-Agent kann parallel zur Sicherungserweiterung ausgeführt werden.
**Sichern lokaler Computer und virtueller Azure-Computer mit DPM/MABS** | Der DPM- oder MABS-Schutz-Agent wird auf den Computern ausgeführt, die Sie schützen möchten. Der MARS-Agent wird auf dem DPM-Server/in der MABS-Instanz für die Sicherung in Azure ausgeführt.

## <a name="which-backup-agent-should-i-use"></a>Welchen Sicherungs-Agent soll ich verwenden?

**Sicherung** | **Lösung** | **Einschränkung**
--- | --- | ---
**Ich möchte einen vollständigen virtuellen Azure-Computer sichern.** | Aktivieren Sie die Sicherung für den virtuellen Azure-Computer. Die Sicherungserweiterung wird auf dem virtuellen Azure-Computer unter Windows oder Linux automatisch konfiguriert. | Der gesamte virtuelle Computer wird gesichert. <br/><br/> Bei virtuellen Windows-Computern ist die Sicherung App-konsistent. Für Linux ist die Sicherung dateikonsistent. Wenn Sie eine App-fähige Sicherung für virtuelle Linux-Computer benötigen, müssen Sie dies mithilfe benutzerdefinierter Skripts konfigurieren.
**Ich möchte bestimmte Dateien/Ordner auf einem virtuellen Azure-Computer sichern.** | Stellen Sie den MARS-Agent auf dem virtuellen Computer bereit.
**Ich möchte lokale Windows-Computer direkt sichern.** | Installieren Sie den MARS-Agent auf dem Computer. | Sie können Dateien, Ordner und den Systemstatus in Azure sichern. Die Sicherungen sind nicht App-fähig.
**Ich möchte lokale Linux-Computer direkt sichern.** | Sie müssen DPM oder MABS bereitstellen, um Sicherungen in Azure zu ermöglichen. | Das Sichern des Linux-Hosts wird nicht unterstützt. Sie können nur den in Hyper-V oder VMWare gehosteten Linux-Gastcomputer sichern.
**Ich möchte lokal ausgeführte Apps sichern.** | Für App-fähige Sicherungen müssen Computer durch DPM oder MABS geschützt werden.
**Ich benötige präzise und flexible Sicherungs- und Wiederherstellungseinstellungen für virtuelle Azure-Computer.** | Schützen Sie virtuelle Azure-Computer mit MABS/DPM (ausgeführt in Azure), um mehr Flexibilität bei der Sicherungsplanung sowie uneingeschränkte Flexibilität für den Schutz und die Wiederherstellung von Dateien, Ordnern, Volumes, Apps und Systemstatus zu erhalten.

## <a name="backup-and-retention"></a>Sicherung und Aufbewahrung

Für Azure Backup gilt pro *geschützter Instanz* eine Obergrenze von 9999 Wiederherstellungspunkten (auch Sicherungskopien oder Momentaufnahmen genannt).

- Geschützte Instanzen sind Computer, Server (physisch oder virtuell) oder Workloads, die Daten in Azure sichern. Eine Instanz ist geschützt, sobald eine Sicherungskopie der Daten gespeichert wurde.
- Die Sicherungskopie der Daten ist der Schutz. Wenn die Quelldaten verloren gehen oder beschädigt werden, können sie mithilfe der Sicherungskopie wiederhergestellt werden.

Die folgende Tabelle zeigt die maximale Sicherungshäufigkeit für jede Komponente. Die Konfiguration der Sicherungsrichtlinie bestimmt, wie schnell die Wiederherstellungspunkte verwendet werden. Wenn Sie beispielsweise jeden Tag einen Wiederherstellungspunkt erstellen, können Sie Wiederherstellungspunkte 27 Jahre lang nutzen, bevor der Vorrat erschöpft ist. Wenn Sie einen monatlichen Wiederherstellungspunkt verwenden, reicht der Vorrat für 833 Jahre. Der Backup-Dienst legt keine Ablaufzeitgrenze für einen Wiederherstellungspunkt fest.

|  | Azure Backup-Agent | System Center DPM | Azure Backup Server | Azure IaaS-VM-Sicherung |
| --- | --- | --- | --- | --- |
| Sicherungshäufigkeit<br/> (zum Recovery Services-Tresor) |Drei Sicherungen pro Tag |Zwei Sicherungen pro Tag |Zwei Sicherungen pro Tag |Eine Sicherung pro Tag |
| Sicherungshäufigkeit<br/> (auf Datenträger) |Nicht zutreffend |Alle 15 Minuten für SQL Server<br/><br/> Stündlich für andere Workloads |Alle 15 Minuten für SQL Server<br/><br/> Stündlich für andere Workloads |Nicht zutreffend |
| Aufbewahrungsoptionen |Täglich, wöchentlich, monatlich, jährlich |Täglich, wöchentlich, monatlich, jährlich |Täglich, wöchentlich, monatlich, jährlich |Täglich, wöchentlich, monatlich, jährlich |
| Maximale Wiederherstellungspunkte pro geschützter Instanz |9999|9999|9999|9999|
| Maximale Aufbewahrungsdauer |Abhängig von der Sicherungshäufigkeit |Abhängig von der Sicherungshäufigkeit |Abhängig von der Sicherungshäufigkeit |Abhängig von der Sicherungshäufigkeit |
| Wiederherstellungspunkte auf lokalem Datenträger |Nicht zutreffend | 64 für Dateiserver<br/><br/> 448 für Anwendungsserver | 64 für Dateiserver<br/><br/> 448 für Anwendungsserver |Nicht zutreffend |
| Wiederherstellungspunkte auf Band |Nicht zutreffend |Unbegrenzt |Nicht zutreffend |Nicht zutreffend |

## <a name="how-does-azure-backup-work-with-encryption"></a>Wie funktioniert Azure Backup mit Verschlüsselung?

**Verschlüsselung** | **Lokale Sicherung** | **Sichern virtueller Azure-Computer** | **Sichern von SQL auf virtuellen Azure-Computern**
--- | --- | --- | ---
Verschlüsselung ruhender Daten<br/> (Verschlüsselung von Daten dort, wo sie beibehalten werden/gespeichert sind) | Kundenspezifische Passphrase wird zum Verschlüsseln von Daten verwendet. | Azure [ Speicherdienstverschlüsselung (Storage Service Encryption, SSE)](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) wird zum Verschlüsseln von Daten verwendet, die im Tresor gespeichert sind.<br/><br/> Bei der Sicherung werden Daten vor dem Speichern automatisch verschlüsselt. Azure Storage entschlüsselt Daten vor dem Abrufen. Die Verwendung von vom Kunden verwalteten Schlüsseln für SSE wird derzeit nicht unterstützt.<br/><br/> Sie können virtuelle Computer sichern, bei denen [Azure Disk Encryption (ADE)](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview)zum Verschlüsseln von Betriebssystemdatenträgern und sonstigen Datenträgern verwendet wird. Azure Backup unterstützt virtuelle Computer, die nur mit BEK verschlüsselt wurden, sowie solche, die sowohl mit BEK als auch [KEK](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/) verschlüsselt wurden. Informieren Sie sich über die [Einschränkungen](backup-azure-vms-encryption.md#encryption-support). | Azure Backup unterstützt die Sicherung von SQL Server-Datenbanken oder Servern mit aktivierter [TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017). Backup unterstützt TDE mit Schlüsseln, die von Azure verwaltet werden, oder mit vom Kunden verwalteten Schlüssen (Bring Your Own Key, BYOK).<br/><br/> Backup führt im Rahmen des Sicherungsvorgangs keine SQL-Verschlüsselung durch.
Verschlüsselung während der Übertragung<br/> (Verschlüsselung von Daten, die von einem Speicherort in einen anderen bewegt werden) | Daten werden mit AES256 verschlüsselt und über HTTPS an den Tresor in Azure gesendet. | In Azure werden Daten zwischen Azure Storage und dem Tresor durch HTTPS geschützt. Diese Daten bleiben im Azure-Backbone-Netzwerk.<br/><br/> Bei der Dateiwiederherstellung sichert iSCSI die zwischen dem Tresor und dem virtuellen Azure-Computer übertragenen Daten. Sicheres Tunneling schützt den iSCSI-Kanal. | In Azure werden Daten zwischen Azure Storage und dem Tresor durch HTTPS geschützt.<br/><br/> Die Dateiwiederherstellung ist für SQL nicht relevant.

## <a name="next-steps"></a>Nächste Schritte

- Machen Sie sich [hier](backup-architecture.md) mit der Architektur und den Komponenten für verschiedene Sicherungsszenarien vertraut.
- [Überprüfen Sie die](backup-support-matrix.md) Unterstützungsanforderungen und Einschränkungen für Azure Backup und für die [Sicherung virtueller Computer](backup-support-matrix-iaas.md).

[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png
