---
title: Architektur der Hyper-V-Notfallwiederherstellung in Azure Site Recovery
description: Dieser Artikel bietet einen Überblick über die Komponenten und Architektur, die beim Bereitstellen von Notfallwiederherstellung für lokale virtuelle Hyper-V-Computer (ohne VMM) in Azure mit dem Azure Site Recovery-Dienst verwendet werden.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: af387b063a3c07d8b6b6c544814565e2a5ebdd46
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993770"
---
# <a name="hyper-v-to-azure-disaster-recovery-architecture"></a>Architektur der Notfallwiederherstellung von Hyper-V zu Azure


Dieser Artikel beschreibt die Architektur und Prozesse, die mithilfe des Diensts [Azure Site Recovery](site-recovery-overview.md) bei der Replikation, bei der Ausführung eines Failovers und beim Wiederherstellen von virtuellen Hyper-V-Computern (VMs) zwischen lokalen Hyper-V-Hosts und Azure verwendet werden.

Hyper-V-Hosts können optional in den privaten Clouds von System Center Virtual Machine Manager (VMM) verwaltet werden.



## <a name="architectural-components---hyper-v-without-vmm"></a>Architekturkomponenten – Hyper-V ohne VMM

Die folgende Tabelle und Grafik bietet eine Übersicht der Komponenten, die für die Hyper-V-Replikation in Azure verwendet werden, wenn die Hyper-V-Hosts nicht durch VMM verwaltet werden.

**Komponente** | **Anforderung** | **Details**
--- | --- | ---
**Azure** | Ein Azure-Abonnement, ein Azure-Speicherkonto und ein Azure-Netzwerk | Replizierte Daten von lokalen VM-Workloads werden im Speicherkonto gespeichert. Azure-VMs werden mit den replizierten Workloaddaten erstellt, wenn ein Failover an Ihrem lokalen Standort auftritt.<br/><br/> Für die Azure-VMs wird eine Verbindung mit dem virtuellen Azure-Netzwerk hergestellt, wenn diese erstellt werden.
**Hyper-V** | Während der Bereitstellung von Site Recovery sammeln Sie Hyper-V-Hosts und -Cluster in Hyper-V-Standorten. Sie installieren den Azure Site Recovery-Anbieter und den Recovery Services-Agent auf jedem eigenständigen Hyper-V-Host bzw. jedem Hyper-V-Clusterknoten. | Der Anbieter orchestriert die Replikation mit Site Recovery über das Internet. Der Recovery Services-Agent verarbeitet die Datenreplikation.<br/><br/> Sowohl die Kommunikation vom Anbieter als auch vom Agent ist sicher und verschlüsselt. Die replizierten Daten im Azure-Speicher werden ebenfalls verschlüsselt.
**Virtuelle Hyper-V-Computer** | Eine oder mehrere VMs, die in Hyper-V ausgeführt werden | Auf virtuellen Computern muss nichts explizit installiert werden.


**Hyper-V-in-Azure-Architektur (ohne VMM)**

![Diagramm: Lokale Hyper-V-Standort-in-Azure-Architektur ohne VMM](./media/hyper-v-azure-architecture/arch-onprem-azure-hypervsite.png)


## <a name="architectural-components---hyper-v-with-vmm"></a>Architekturkomponenten – Hyper-V mit VMM

Die folgende Tabelle und Grafik bieten eine Übersicht der Komponenten, die für die Hyper-V-Replikation in Azure verwendet werden, wenn die Hyper-V-Hosts durch VMM-Clouds verwaltet werden.

**Komponente** | **Anforderung** | **Details**
--- | --- | ---
**Azure** | Ein Azure-Abonnement, ein Azure-Speicherkonto und ein Azure-Netzwerk | Replizierte Daten von lokalen VM-Workloads werden im Speicherkonto gespeichert. Azure-VMs werden mit den replizierten Daten erstellt, wenn ein Failover an Ihrem lokalen Standort auftritt.<br/><br/> Für die Azure-VMs wird eine Verbindung mit dem virtuellen Azure-Netzwerk hergestellt, wenn diese erstellt werden.
**VMM-Server** | Der VMM-Server verfügt über mindestens eine Cloud mit Hyper-V-Hosts. | Auf dem VMM-Server installieren Sie den Site Recovery-Anbieter, um die Replikation mit Site Recovery zu orchestrieren. Außerdem registrieren Sie den Server im Recovery Services-Tresor.
**Hyper-V-Host** | Mindestens ein von VMM verwalteter Hyper-V-Host/-Cluster. |  Sie installieren den Recovery Services-Agent auf jedem Hyper-V-Host bzw. Clusterknoten.
**Virtuelle Hyper-V-Computer** | Mindestens ein virtueller Computer, der auf einem Hyper-V-Hostserver ausgeführt wird. | Auf virtuellen Computern muss nichts explizit installiert werden.
**Netzwerk** | Eingerichtetes logisches Netzwerk und VM-Netzwerk auf dem VMM-Server. Das VM-Netzwerk sollte mit einem logischen Netzwerk verbunden sein, das der Cloud zugeordnet ist. | VM-Netzwerke sind virtuellen Azure-Netzwerken zugeordnet. Wenn Azure-VMs nach einem Failover erstellt werden, werden Sie dem Azure-Netzwerk hinzugefügt, welches dem VM-Netzwerk zugeordnet ist.

**Hyper-V-in-Azure-Architektur (mit VMM)**

![Diagramm: Lokale Hyper-V-Standort-in-Azure-Architektur mit VMM](./media/hyper-v-azure-architecture/arch-onprem-onprem-azure-vmm.png)

## <a name="set-up-outbound-network-connectivity"></a>Einrichten der ausgehenden Netzwerkkonnektivität

Damit Site Recovery erwartungsgemäß funktioniert, müssen Sie die ausgehende Netzwerkkonnektivität ändern, um Ihrer Umgebung das Replizieren zu ermöglichen.

> [!NOTE]
> Site Recovery unterstützt die Verwendung eines Authentifizierungsproxys zur Steuerung der Netzwerkkonnektivität nicht.

### <a name="outbound-connectivity-for-urls"></a>Ausgehende Konnektivität für URLs

Lassen Sie den Zugriff auf die folgenden URLs zu, wenn Sie einen URL-basierten Firewallproxy zum Steuern der ausgehenden Konnektivität verwenden:

| **Name**                  | **Kommerziell**                               | **Behörden**                                 | **Beschreibung** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Storage                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net`              | Ermöglicht das Schreiben von Daten aus der VM in das Cachespeicherkonto in der Quellregion |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Stellt die Autorisierung und Authentifizierung für Site Recovery-Dienst-URLs bereit. |
| Replikation               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Ermöglicht die Kommunikation der VM mit Site Recovery |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Ermöglicht es der VM, die Site Recovery-Überwachung und -Diagnosedaten zu schreiben |


## <a name="replication-process"></a>Replikationsprozess

![Diagramm: Hyper-V-in-Azure-Replikationsprozess](./media/hyper-v-azure-architecture/arch-hyperv-azure-workflow.png)

**Replikations- und Wiederherstellungsprozess**


### <a name="enable-protection"></a>Schutz aktivieren

1. Nachdem Sie den Schutz für einen virtuellen Hyper-V-Computer über das Azure-Portal oder lokal aktiviert haben, wird der Auftrag **Schutz aktivieren** gestartet.
2. Im Rahmen des Auftrags wird überprüft, ob der Computer die Voraussetzungen erfüllt. Anschließend wird [CreateReplicationRelationship](/windows/win32/hyperv_v2/createreplicationrelationship-msvm-replicationservice) aufgerufen, um die Replikation mit den konfigurierten Einstellungen einzurichten.
3. Der Auftrag startet die erste Replikation durch Aufrufen der [StartReplication](/windows/win32/hyperv_v2/startreplication-msvm-replicationservice)-Methode, um eine vollständige VM-Replikation zu initiieren, und übermittelt die virtuellen Datenträger des virtuellen Computers an Azure.
4. Sie können den Auftrag auf der Registerkarte **Aufträge** überwachen.      ![Screenshot: Auf der Registerkarte „Aufträge“ aufgelistete Aufträge](media/hyper-v-azure-architecture/image1.png) ![Screenshot: Bildschirm „Schutz aktivieren“ mit weiteren Details](media/hyper-v-azure-architecture/image2.png)


### <a name="initial-data-replication"></a>Erste Datenreplikation

1. Wenn die erste Replikation ausgelöst wird, wird eine [Momentaufnahem der Hyper-V-VM](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560637(v=ws.10)) erstellt.
2. Virtuelle Festplatten auf der VM werden nacheinander repliziert, bis alle in Azure kopiert wurden. Die Dauer ist abhängig von der Größe der VM und von der Netzwerkbandbreite. [Erfahren Sie, wie](https://support.microsoft.com/kb/3056159) die Netzwerkbandbreite erhöht werden kann.
3. Falls während der ersten Replikation Datenträgeränderungen auftreten, werden die Änderungen mit dem Replication Tracker für Hyper-V-Replikate in Form von Hyper-V-Replikationsprotokollen (.hrl) nachverfolgt. Diese Protokolldateien befinden sich im gleichen Ordner wie die Datenträger. Jeder Datenträger verfügt über eine zugeordnete HRL-Datei, die an den sekundären Speicher gesendet wird. Beachten Sie, dass die Momentaufnahme- und Protokolldateien Festplattenressourcen belegen, während die anfängliche Replikation durchgeführt wird.
4. Nach Abschluss der ersten Replikation wird die Momentaufnahme des virtuellen Computers gelöscht.
5. Die im Protokoll erfassten Datenträgeränderungen werden synchronisiert und mit dem übergeordneten Datenträger zusammengeführt.


### <a name="finalize-protection-process"></a>Abschließen des Schutzprozesses

1. Nach Abschluss der ersten Replikation wird der Auftrag **Schutz auf dem virtuellen Computer abschließen** ausgeführt. Er konfiguriert Einstellungen für das Netzwerk und andere Einstellungen für die Zeit nach der Replikation, sodass die VM geschützt ist.
2. In dieser Phase können Sie die Einstellungen der VM überprüfen, um sicherzustellen, dass sie bereit für das Failover ist. Sie können einen Notfallwiederherstellungsdrill (Testfailover) für die VM ausführen, um zu überprüfen, ob das Failover wie erwartet ausgeführt wird. 


## <a name="delta-replication"></a>Deltareplikation

1. Nach der ersten Replikation beginnt die Deltareplikation gemäß der Replikationsrichtlinie.
2. Der Replication Tracker für Hyper-V-Replikate verfolgt die Änderungen einer virtuellen Festplatte als HRL-Dateien. Jedem für die Replikation konfigurierten Datenträger ist eine HRL-Datei zugeordnet.
3. Das Protokoll wird an das Speicherkonto des Kunden gesendet. Beim Übermitteln eines Protokolls an Azure werden Änderungen am primären Datenträger in einer anderen Protokolldatei im gleichen Ordner nachverfolgt.
4. Während der Erst- und Deltareplikation können Sie die VM im Azure-Portal überwachen.

### <a name="resynchronization-process"></a>Neusynchronisierungsprozess

1. Falls die Deltareplikation nicht erfolgreich ist und eine vollständige Replikation viel Bandbreite oder Zeit beanspruchen würde, wird der virtuelle Computer für eine Neusynchronisierung markiert.
    - Wenn die HRL-Dateien beispielsweise 50% des Festplattenspeichers füllen, wird die VM für die Neusynchronisierung gekennzeichnet.
    -  Standardmäßig ist die Neusynchronisierung so geplant, dass sie automatisch außerhalb der Geschäftszeiten durchgeführt wird.
1.  Die Neusynchronisierung sendet nur Deltadaten.
    - Sie minimiert die Menge der gesendeten Daten durch die Berechnung von Prüfsummen der Quell- und Ziel-VMs.
    - Sie verwendet einen Blockerstellungsalgorithmus mit festen Blöcken, durch den Quell-und Zieldateien in feste Blöcke unterteilt werden.
    - Für jeden Block werden Prüfsummen generiert. Sie werden verglichen, um zu ermitteln, welche Blöcke aus der Quelle auf das Ziel angewendet werden müssen.
2. Nach Abschluss der Neusynchronisierung wird die normale Deltareplikation fortgesetzt.
3. Wenn Sie nicht auf die Standardneusynchronisierung außerhalb der Geschäftszeiten warten möchten, können Sie eine VM manuell neu synchronisieren. Wenn z.B. ein Ausfall auftritt. Klicken Sie dafür im Azure-Portal auf „VM“ > **Resynchronisieren**.

    ![Screenshot: Option „Resynchronisieren“](./media/hyper-v-azure-architecture/image4-site.png)


### <a name="retry-process"></a>Wiederholungsprozess

Im Falle eines Replikationsfehlers wird die integrierte Wiederholungsfunktion verwendet. Das Wiederholen wird wie in der Tabelle beschrieben klassifiziert.

**Kategorie** | **Details**
--- | ---
**Nicht behebbare Fehler** | Es wird kein erneuter Versuch unternommen. Der Status des virtuellen Computers lautet **Kritisch**, und ein Administrator muss eingreifen.<br/><br/> Beispiele für diese Fehler: Eine unterbrochene VHD-Kette, ein ungültiger Zustand der virtuellen Replikat-VM, Netzwerkauthentifizierungsfehler, Autorisierungsfehler und Fehler aufgrund einer nicht gefundenen VM (bei eigenständigen Hyper-V-Servern)
**Behebbare Fehler** | In jedem Replikationsintervall wird ein weiterer erneuter Versuch unternommen. Dabei wird ein exponentielles Backoff verwendet, durch das sich das Wiederholungsintervall ab dem ersten Versuch immer weiter verlängert (1, 2, 4, 8 und 10 Minute(n)). Wenn ein Fehler auftritt, versuchen Sie es jede halbe Stunde erneut. Beispiele: Netzwerkfehler, Fehler aufgrund von geringem Speicherplatz und wenig Arbeitsspeicher.



## <a name="failover-and-failback-process"></a>Failover- und Failbackprozesse

1. Sie können ein geplantes oder ungeplantes Failover von lokalen Hyper-V-VMs zu Azure ausführen. Wenn Sie ein geplantes Failover durchführen, werden die Quell-VMs heruntergefahren, um sicherzustellen, dass kein Datenverlust auftritt. Führen Sie ein ungeplantes Failover aus, wenn kein Zugriff auf Ihren primären Standort möglich ist.
2. Sie können ein Failover für einen einzelnen Computer ausführen oder Wiederherstellungspläne erstellen, um das Failover von mehreren Computern zu orchestrieren.
3. Sie führen ein Failover aus. Nachdem die erste Phase des Failovers abgeschlossen ist, sollten Sie die erstellten Replikat-VMs in Azure sehen können. Sie können der VM bei Bedarf eine öffentliche IP-Adresse zuweisen.
4. Anschließend führen Sie ein Commit für das Failover durch, um von der Replikat-VM in Azure auf die Workload zuzugreifen.

Sobald Ihre lokale Infrastruktur wieder funktioniert und ausgeführt werden kann, können Sie ein Failback ausführen. Das Failback erfolgt in drei Phasen:

1. Sie starten ein geplantes Failover von Azure zum lokalen Standort:
    - **Weniger Ausfallzeiten:** Wenn Sie diese Option verwenden, synchronisiert Site Recovery Daten vor dem Failover. und überprüft auf geänderte Datenblocks. Diese werden dann an den lokalen Standort heruntergeladen, während die Azure-VM weiterhin ausgeführt wird und die Downtime minimiert. Wenn Sie manuell angeben, dass das Failover abgeschlossen werden soll, wird die Azure-VM heruntergefahren, endgültige Deltaänderungen werden kopiert und das Failover startet.
    - **Vollständiger Download:** Bei dieser Option werden Daten während des Failovers synchronisiert. Mit dieser Option wird der gesamte Datenträger heruntergeladen. Dies erfolgt schneller, da keine Prüfsummen berechnet werden. Jedoch kommt es zu mehr Downtime. Verwenden Sie diese Option, wenn Sie die Azure-Replikat-VMs für einige Zeit ausgeführt haben oder die lokale VM gelöscht wurde.
    - **Virtuellen Computer erstellen:** Sie können wählen, ob Sie das Failback zur gleichen VM oder zu einer alternativen VM ausführen. Sie können angeben, dass Site Recovery die VM erstellen sollte, wenn sie nicht bereits vorhanden ist.

2. Nach Beendigung der Erstsynchronisierung wählen Sie aus, dass das Failover abgeschlossen werden soll. Sobald es abgeschlossen ist, können Sie sich bei der lokalen VM anmelden, um zu überprüfen, ob alles wie erwartet funktioniert. Im Azure-Portal können Sie sehen, dass die Azure-VMs angehalten wurden.
3.  Anschließend committen Sie das Failover, um es abzuschließen, und greifen wieder von der lokalen VM auf die Workload zu.
4. Nachdem Failbacks für die Workloads ausgeführt wurden, aktivieren Sie die umgekehrte Replikation, sodass die lokalen VMs erneut in Azure replizieren.



## <a name="next-steps"></a>Nächste Schritte


Absolvieren Sie [dieses Tutorial](tutorial-prepare-azure.md), um sich mit der Hyper-V-zu-Azure-Replikation vertraut zu machen.


