---
title: Häufig gestellte Fragen zur Azure Migrate-Servermigration
description: Hier erhalten Sie Antworten auf häufig gestellte Fragen zur Verwendung der Azure Migrate-Servermigration für das Migrieren von Computern.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 08/28/2020
ms.openlocfilehash: 1f32e9788b2255be373a4db8c159ef2a6b6ccf04
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753840"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Azure Migrate-Servermigration: Häufig gestellte Fragen

Dieser Artikel beantwortet häufige Fragen zu Azure Migrate: Servermigration“ kommunizieren kann. Wenn Sie weitere Fragen haben, sehen Sie sich die folgenden Ressourcen an:

- [Allgemeine Fragen](resources-faq.md) zu Azure Migrate
- Fragen zur [Azure Migrate-Appliance](common-questions-appliance.md)
- Fragen zur [Ermittlung, Bewertung und Abhängigkeitsvisualisierung](common-questions-discovery-assessment.md)
- Antworten auf Fragen im [Azure Migrate-Forum](https://aka.ms/AzureMigrateForum)

## <a name="does-azure-migrate-convert-uefi-based-machines-to-bios-based-machines-and-migrate-them-to-azure-as-azure-generation-1-vms"></a>Konvertiert Azure Migrate auf UEFI basierende Computer in BIOS-basierte Computer und migriert Sie als virtuelle Azure-Computer der Generation 1 zu Azure?
Von der Azure Das Servermigrationstool migriert alle UEFI-basierten Computer als virtuelle Azure-Computer der Generation 2 zu Azure. Die Konvertierung von UEFI-basierten VMs in BIOS-basierte VMS wird nicht mehr unterstützt. Beachten Sie, dass alle BIOS-basierten Computer nur als Azure-VMs der Generation 1 zu Azure migriert werden.

## <a name="how-can-i-migrate-uefi-based-machines-to-azure-as-azure-generation-1-vms"></a>Wie kann ich UEFI-basierte Computer als virtuelle Azure-Computer der Generation 1 zu Azure migrieren?
Von der Azure Das Servermigrationstool migriert UEFI-basierte Computer als virtuelle Azure-Computer der Generation 2 zu Azure. Wenn Sie diese zu virtuellen Computern der Azure-Generation 1 migrieren möchten, konvertieren Sie den Starttyp vor dem Starten der Replikation in BIOS, und verwenden Sie dann Azure Migrate: Servermigrationstool für die Migration zu Azure.
 
## <a name="which-operating-systems-are-supported-for-migration-of-uefi-based-machines-to-azure"></a>Welche Betriebssysteme werden für die Migration von UEFI-basierten Computern zu Azure unterstützt?

| **Für UEFI-basierte Computer unterstützte Betriebssysteme** | **VMware ohne Agent zu Azure**                                                                                                             | **Hyper-V ohne Agent zu Azure** | **Agent-basierte VMware, physische und andere Clouds zu Azure** |
| ------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------ | ---------------------------------------------------------- |
| Windows Server 2019, 2016, 2012 R2, 2012                 | J                                                                                                                                         | J                              | J                                                          |
| Windows 10 Pro, Windows 10 Enterprise                   | J                                                                                                                                         | J                              | J                                                          |
| SUSE Linux Enterprise Server 15 SP1                     | J                                                                                                                                         | J                              | J                                                          |
| SUSE Linux Enterprise Server 12 SP4                     | J                                                                                                                                         | J                              | J                                                          |
| Ubuntu Server 16.04, 18.04, 19.04, 19.10                | J                                                                                                                                         | J                              | J                                                          |
| RHEL 8.1, 8.0, 7.8, 7.7, 7.6, 7.5, 7.4, 7.0, 6.x        | J<br>                 _RHEL 8.x erfordert [manuelle Vorbereitung](./prepare-for-migration.md#linux-machines)_   | J                              | J                                                          |
| Cent OS 8.1, 8.0, 7.7, 7.6, 7.5, 7.4, 6.x               | J<br>_Cent OS 8.x erfordert [manuelle Vorbereitung](./prepare-for-migration.md#linux-machines)_ | J                              | J                                                          |
| Oracle Linux 7.7, 7.7-CI                                |  J                                                                                                                                        | J                              | J                                                          |

## <a name="can-i-use-the-recovery-services-vault-created-by-azure-migrate-for-disaster-recovery-scenarios"></a>Kann ich den Recovery Services-Tresor verwenden, der von Azure Migrate für Notfallwiederherstellungsszenarien erstellt wurde?
Die Verwendung des Recovery Services-Tresors, der von Azure Migrate für Notfallwiederherstellungsszenarien erstellt wurde, wird nicht empfohlen. Dies kann zu Fehlern beim Starten der Replikation in Azure Migrate führen. 

## <a name="where-should-i-install-the-replication-appliance-for-agent-based-migrations"></a>Wo sollte ich die Replikationsappliance für Agent-basierte Migrationen installieren?

Die Replikationsappliance sollte auf einem dedizierten Computer installiert werden. Die Replikationsappliance sollte nicht auf einem zu replizierenden Quellcomputer oder auf der Ermittlungs- und Bewertungsappliance von Azure Migrate installiert werden, die Sie unter Umständen bereits installiert haben. Weitere Einzelheiten finden Sie im [Tutorial](./tutorial-migrate-physical-virtual-machines.md).

## <a name="how-can-i-migrate-my-aws-ec2-instances-to-azure"></a>Wie kann ich meine AWS EC2-Instanzen zu Azure migrieren?

Lesen Sie diesen [Artikel](./tutorial-migrate-aws-virtual-machines.md), um Ihre AWS EC2-Instanzen zu ermitteln, zu bewerten und zu Azure zu migrieren.

## <a name="can-i-migrate-aws-vms-running-amazon-linux-operating-system"></a>Kann ich AWS-VMs migrieren, die das Betriebssystem Amazon Linux ausführen?

VMs, auf denen Amazon Linux ausgeführt wird, können nicht unverändert migriert werden, da das Betriebssystem Amazon Linux nur unter AWS unterstützt wird.
Zum Migrieren von Workloads, die unter Amazon Linux ausgeführt werden, können Sie eine CentOS/RHEL-VM in Azure einrichten und die Workload, die auf dem AWS Linux-Computer ausgeführt wird, mithilfe eines relevanten Migrationsansatzes migrieren. Abhängig von der Workload gibt es z. B. möglicherweise workloadspezifische Tools zur Unterstützung der Migration – etwa für Datenbanken oder Bereitstellungstools im Fall von Webservern.

## <a name="what-geographies-are-supported-for-migration-with-azure-migrate"></a>Welche geografischen Regionen werden für die Migration mit Azure Migrate unterstützt?

In der Unterstützungsmatrix für Azure Migrate finden Sie die unterstützten geografischen Regionen für die [öffentliche Cloud](migrate-support-matrix.md#supported-geographies-public-cloud) und für [Azure Government](migrate-support-matrix.md#supported-geographies-azure-government).

## <a name="can-we-use-the-same-azure-migrate-project-to-migrate-to-multiple-regions"></a>Kann das gleiche Azure Migrate-Projekt verwendet werden, um zu mehreren Regionen zu migrieren?

Obwohl Sie in einem Azure Migrate-Projekt Bewertungen für mehrere Regionen erstellen können, kann ein einzelnes Azure Migrate-Projekt nur verwendet werden, um Server zu einer Azure-Region zu migrieren. Sie können zusätzliche Azure Migrate-Projekte für jede Region erstellen, zu der Sie migrieren müssen.

- Für VMware-Migrationen ohne Agent ist die Zielregion gesperrt, sobald Sie die erste Replikation aktivieren.
- Bei Agent-basierten Migrationen (VMware, physische Server und Server aus anderen Clouds) wird die Zielregion gesperrt, sobald beim Einrichten der Replikationsappliance im Portal auf die Schaltfläche „Ressourcen erstellen“ geklickt wird.
- Für Hyper-V-Migrationen ohne Agent wird die Zielregion gesperrt, sobald beim Einrichten des Hyper-V-Replikationsanbieters im Portal auf die Schaltfläche „Ressourcen erstellen“ geklickt wird.

## <a name="can-we-use-the-same-azure-migrate-project-to-migrate-to-multiple-subscriptions"></a>Kann das gleiche Azure Migrate-Projekt verwendet werden, um zu mehreren Abonnements zu migrieren? 

Ja, Sie können zu mehreren Abonnements in der gleichen Zielregion für ein Azure Migrate-Projekt migrieren. Sie können das Zielabonnement auswählen, während Sie die Replikation für einen Computer oder eine Gruppe von Computern aktivieren. Die Zielregion wird nach der ersten Replikation für VMware-Migrationen ohne Agent und während der Replikationsappliance- und Hyper-V-Anbieterinstallation für Agent-basierte Migrationen bzw. für Hyper-V-Migrationen ohne Agent gesperrt.

## <a name="what-are-the-migration-options-in-azure-migrate-server-migration"></a>Welche Migrationsoptionen bietet Azure Migrate: Servermigration?

Azure Migrate: Das Servermigrationstool bietet zwei Optionen zum Durchführen von Migrationen von Quellservern/VMs zu Azure – Migration ohne Agent und Agent-basierte Migration.

Unabhängig von der gewählten Migrationsoption ist der erste Schritt zur Migration eines Servers mit Azure Migration: Servermigration das Aktivieren von Replikation für den Server. Dadurch wird eine anfängliche Replikation ihrer VM/Serverdaten in Azure durchgeführt. Nachdem die anfängliche Replikation abgeschlossen wurde, wird eine fortlaufende Replikation (fortlaufende Deltasynchronisierung) zum Migrieren von inkrementellen Daten zu Azure eingerichtet. Sobald der Vorgang die Deltasynchronisierungsphase erreicht hat, können Sie jederzeit zu Azure migrieren.  

Im Folgenden finden Sie einige Überlegungen, die Sie berücksichtigen sollten, wenn Sie sich für eine Migrationsoption entscheiden.

**Bei der Migration ohne Agent** müssen auf den zu migrierenden Quell-VMS/-Servern keine Software (Agents) bereitgestellt werden. Die Option ohne Agent orchestriert die Replikation durch Integration in die vom Virtualisierungsanbieter bereitgestellte Funktionalität.
Die Replikationsoptionen ohne Agent sind für [VMware-VMs](./tutorial-migrate-vmware.md) und [Hyper-V-VMs](./tutorial-migrate-hyper-v.md) verfügbar.

**Bei Agent-basierten Migrationen** muss Azure Migrate-Software (Agents) auf den zu migrierenden Quell-VMs/-computern installiert werden. Die Agent-basierte Option ist für die Replikationsfunktionalität nicht von der Virtualisierungsplattform abhängig und kann daher mit jedem Server mit x86-/x64-Architektur und einer Version eines Betriebssystems verwendet werden, das von der Agent-basierten Replikationsmethode unterstützt wird.

Die Agent-basierte Migrationsoption kann für [VMware-VMs](./tutorial-migrate-vmware-agent.md), [Hyper-V-VMs](./tutorial-migrate-physical-virtual-machines.md), [physische Server](./tutorial-migrate-physical-virtual-machines.md), [VMs, die unter AWS](./tutorial-migrate-aws-virtual-machines.md), VMs, die unter GCP oder VMs, die unter einem anderen Virtualisierungsanbieter ausgeführt werden, verwendet werden. Bei der Agent-basierten Migration werden Ihre Computer zum Zweck der Migration als physische Server behandelt.

Während die Migration ohne Agents zusätzliche Bequemlichkeit und Einfachheit gegenüber den Agent-basierten Replikationsoptionen für die unterstützten Szenarien (VMWare und Hyper-V) bietet, sollten Sie die Verwendung des Agent-basierten Szenarios für die folgenden Anwendungsfälle in Betracht ziehen:

- Eingeschränkte IOPS-Umgebung: Die Replikation ohne Agent verwendet Momentaufnahmen und beansprucht Speicher-IOPS/Bandbreite. Wir empfehlen die Agent-basierte Migrationsmethode, wenn es in Ihrer Umgebung Einschränkungen bezüglich Speicher/IOPS gibt.
- Wenn Sie über keinen vCenter-Server verfügen, können Sie Ihre VMware-VMs wie physische Server behandeln und den Agent-basierten Migrationsworkflow verwenden.

Weitere Informationen finden Sie in diesem [Artikel](./server-migrate-overview.md), in dem Migrationsoptionen für VMware-Migrationen verglichen werden.

## <a name="how-does-agentless-migration-work"></a>Wie funktioniert eine Migration ohne Agent?

Azure Migrate: Servermigration bietet Replikationsoptionen ohne Agent für die Migration von virtuellen VMware-Computern und virtuellen Hyper-V-Computern unter Windows oder Linux. Das Tool bietet auch eine zusätzliche Agent-basierte Replikationsoption für Windows- und Linux-Server, die zum Migrieren physischer Server sowie von x86-/x64-VMs für VMware, Hyper-V, AWS, GCP usw. verwendet werden kann. Die Agent-basierte Replikationsoption erfordert die Installation von Agent-Software auf dem Server/virtuellen Computer, der migriert werden soll, während bei der Option ohne Agent keine Software auf den virtuellen Computern selbst installiert werden muss, was gegenüber der Agent-basierten Replikationsoption zusätzlichen Komfort und Einfachheit bietet.

Die Replikationsoption ohne Agent funktioniert mithilfe von Mechanismen, die vom Virtualisierungsanbieter (VMware, Hyper-V) bereitgestellt werden. Im Fall von virtuellen VMware-Computern verwendet der Replikationsmechanismus ohne Agent VMware-Momentaufnahmen und die VMware-Nachverfolgungstechnologie für geänderte Blöcke, um Daten von Datenträgern virtueller Computer zu replizieren. Dieser Mechanismus ähnelt dem Mechanismus, der von vielen Sicherungsprodukten verwendet wird. Bei virtuellen Hyper-V-Computern verwendet der Replikationsmechanismus ohne Agent VM-Momentaufnahmen und die Änderungsnachverfolgungsfunktion des Hyper-V-Replikats, um Daten von Datenträgern virtueller Computer zu replizieren.

Wenn Replikation für einen virtuellen Computer konfiguriert ist, wird zuerst eine anfängliche Replikationsphase durchlaufen. Während der ersten Replikation wird eine VM-Momentaufnahme erstellt, und eine vollständige Kopie der Daten von den Momentaufnahmedatenträgern wird auf verwaltete Datenträger in Ihrem Abonnement repliziert. Nach Abschluss der ersten Replikation für die VM geht der Replikationsprozess in eine inkrementelle Replikationsphase (Deltareplikation) über. In der inkrementellen Replikationsphase werden Datenänderungen, die seit dem letzten abgeschlossenen Replikationszyklus eingetreten sind, periodisch repliziert und auf die verwalteten Datenträger des Replikats angewendet, wodurch die Replikation mit den Änderungen auf der VM synchron gehalten wird. Im Fall von virtuellen VMware-Computern werden Änderungen zwischen Replikationszyklen mithilfe der VMware-Nachverfolgungstechnologie für geänderte Blöcke nachverfolgt. Zu Beginn des Replikationszyklus wird eine VM-Momentaufnahme erstellt, und die Nachverfolgung geänderter Blöcke wird verwendet, um die Änderungen zwischen der aktuellen Momentaufnahme und der zuletzt erfolgreich replizierten Momentaufnahme abzurufen. Auf diese Weise müssen nur Daten repliziert werden, die seit dem letzten abgeschlossenen Replikationszyklen geändert wurden, um die Replikation für die VM synchron zu halten. Am Ende jedes Replikationszyklus wird die Momentaufnahme freigegeben und eine Momentaufnahmekonsolidierung für den virtuellen Computer ausgeführt. Analog dazu wird im Fall von virtuellen Hyper-V-Computern die Hyper-V-Replikatänderungsnachverfolgungs-Engine verwendet, um Änderungen zwischen aufeinanderfolgenden Replikationszyklen nachzuverfolgen.
Wenn Sie den Migrationsvorgang auf einem replizierenden virtuellen Computer durchführen, können Sie den lokalen virtuellen Computer herunterfahren und eine abschließende inkrementelle Replikation ausführen, um zu gewährleisten, dass kein Datenverlust auftritt. Beim Ausführen der Migrationsoption werden die dem virtuellen Computer entsprechenden verwalteten Datenträger des Replikats verwendet, um den virtuellen Computer in Azure zu erstellen.

Informationen zu den ersten Schritten finden Sie in den Tutorials [VMware-Migration ohne Agent](./tutorial-migrate-vmware.md) und [Hyper-V-Migration ohne Agent](./tutorial-migrate-hyper-v.md).

## <a name="how-does-agent-based-migration-work"></a>Wie funktioniert eine Agent-basierte Migration?

Zusätzlich zu den Agent-losen Migrationsoptionen für virtuelle VMware-Computer und virtuelle Hyper-V-Computer bietet das Servermigrationstool eine Agent-basierte Migrationsoption für die Migration von Windows- und Linux-Servern, die auf physischen Servern oder als virtuelle x86-/x64-Computer unter VMware, Hyper-V, AWS, Google Cloud Platform usw. ausgeführt werden.

Bei der Agent-basierten Migrationsmethode wird auf dem zu migrierenden Server installierte Agent-Software verwendet, um Serverdaten in Azure zu replizieren. Der Replikationsprozess verwendet eine Auslagerungsarchitektur, bei der der Agent Replikationsdaten an einen dedizierten Replikationsserver weiterleitet, der als Replikationsappliance oder Konfigurationsserver bezeichnet wird (oder an einen Prozessserver für horizontale Skalierung). [Erfahren Sie mehr darüber](./agent-based-migration-architecture.md), wie die Agent-basierte Migrationsoption funktioniert. 

Hinweis: Die Replikationsappliance unterscheidet sich von der Azure Migrate-Erkennungsappliance und muss auf einem separaten/dedizierten Computer installiert werden.

## <a name="how-do-i-gauge-the-bandwidth-requirement-for-my-migrations"></a>Wie schätze ich den Bandbreitenbedarf für meine Migrationen ein?

Die Bandbreite für die Replikation von Daten in Azure hängt von einer Reihe von Faktoren ab und richtet sich danach, wie schnell die lokale Azure Migrate-Appliance die Daten lesen und in Azure replizieren kann. Die Replikation besteht aus zwei Phasen: anfängliche Replikation und Deltareplikation.

Wenn die Replikation für einen virtuellen Computer gestartet wird, werden in einem ersten Replikationszyklus vollständige Kopien der Datenträger repliziert. Nachdem die erste Replikation abgeschlossen wurde, werden in regelmäßigen Abständen inkrementelle Replikationszyklen (Deltazyklen) geplant, um Änderungen zu übertragen, die seit dem vorherigen Replikationszyklus aufgetreten sind.

### <a name="agentless-vmware-vm-migration"></a>VMware-VM-Migration ohne Agent

Sie können die Bandbreitenanforderung auf Grundlage des Datenvolumens berechnen, das im Vorgang verschoben werden muss, und des Zeitrahmens, in dem die anfängliche Replikation abgeschlossen werden soll (idealerweise sollte die anfängliche Replikation mindestens 3 bis 4 Tage vor dem eigentlichen Migrationsfenster abgeschlossen sein, damit Sie genügend Zeit haben, vor dem eigentlichen Zeitfenster eine Testmigration durchzuführen und die Ausfallzeit während des Zeitfensters auf ein Minimum zu beschränken).

Mithilfe der folgenden Formel können Sie die Bandbreite oder die benötigte Zeit für eine Migration von VMware-VMs ohne Agent schätzen:

Zeit bis zum Abschluss der anfänglichen Replikation = {Größe der Datenträger (oder verwendete Größe, falls verfügbar) * 0,7 (unter der Annahme einer durchschnittlichen Komprimierung von 30 Prozent – konservative Schätzung)}/Bandbreite, die für die Replikation zur Verfügung steht.

### <a name="agent-based-vmware-vm-migration"></a>Agent-basierte Migration von VMware-VMs

Bei einer Agent-basierten Replikationsmethode kann der Bereitstellungsplaner die Ermittlung der Umgebung für die Datenänderung unterstützen und die erforderliche Bandbreitenanforderung vorhersagen. Weitere Informationen finden Sie in diesem [Artikel](./agent-based-migration-architecture.md#plan-vmware-deployment). 

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>Wie kann ich die Replikation mithilfe der Azure Migrate-Appliance für die VMware-Replikation ohne Agent drosseln?  

Sie können die Replikation mithilfe von NetQosPolicy drosseln. Beispiel:

Das in der NetQosPolicy zu verwendende AppNamePrefix ist „GatewayWindowsService“. Sie können eine Richtlinie auf der Azure Migrate-Appliance erstellen, um den Replikationsdatenverkehr von der Appliance zu drosseln, indem Sie eine Richtlinie wie die folgende erstellen:

New-NetQosPolicy -Name "ThrottleReplication" -AppPathNameMatchCondition "GatewayWindowsService.exe" -ThrottleRateActionBitsPerSecond 1 MB

## <a name="how-is-the-data-transmitted-from-on-prem-environment-to-azure-is-it-encrypted-before-transmission"></a>Wie werden die Daten von der lokalen Umgebung zu Azure übertragen? Werden sie vor der Übertragung verschlüsselt?

Die Azure Migrate-Appliance komprimiert Daten bei der Replikation ohne Agent und verschlüsselt sie vor dem Hochladen. Daten werden über einen sicheren Kommunikationskanal über HTTPS übertragen und verwenden TLS 1.2 oder höher. Zudem werden Ihre Daten von Azure Storage beim Speichern in der Cloud automatisch verschlüsselt (Verschlüsselung ruhender Daten).  

## <a name="how-does-churn-rate-affect-agentless-replication"></a>Wie wirkt sich die Änderungsrate auf die Replikation ohne Agent aus?

Da bei der Replikation ohne Agent Daten zusammengelegt werden, ist das *Änderungsmuster* wichtiger als die *Änderungsrate*. Wenn eine Datei immer wieder geschrieben wird, hat die Rate keine große Auswirkung. Dagegen verursacht ein Muster, bei dem in jeden zweiten Sektor geschrieben wird, im nächsten Zyklus viele Änderungen. Da die zu übertragende Datenmenge minimiert werden soll, sollen die Daten so weit wie möglich zusammengelegt werden, bevor der nächste Zyklus geplant wird.

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Wie häufig wird ein Replikationszyklus geplant?

Die Formel zur Planung des nächsten Replikationszyklus ist (Vorherige Zyklusdauer / 2) oder eine Stunde, je nachdem, welcher Wert höher ist.

Beispiel: Wenn ein Deltazyklus für eine VM vier Stunden dauert, wird der nächste Zyklus in zwei Stunden geplant, nicht in der nächsten Stunde. Das Verfahren unterscheidet sich unmittelbar nach der ersten Replikation, wenn sofort der erste Deltazyklus geplant wird.

## <a name="how-do-i-migrate-windows-server-2003-running-on-vmwarehyper-v-to-azure"></a>Wie migriere ich Windows Server 2003 unter VMware/Hyper-V zu Azure?

[Der erweiterte Support für Windows Server 2003](/troubleshoot/azure/virtual-machines/run-win-server-2003#microsoft-windows-server-2003-end-of-support) wurde am 14. Juli 2015 beendet.  Das Azure-Supportteam hilft weiterhin bei der Behebung von Problemen, die sich auf die Ausführung von Windows Server 2003 in Azure beziehen. Diese Unterstützung ist jedoch auf Probleme beschränkt, die keine Problembehandlung oder Patches auf Betriebssystemebene erfordern.
Die Migration Ihrer Anwendungen zu Azure-Instanzen, die eine neuere Version von Windows Server ausführen, ist die empfohlene Vorgehensweise, um sicherzustellen, dass Sie die Flexibilität und Zuverlässigkeit der Azure-Cloud effektiv nutzen.

Wenn Sie sich dennoch für die Migration von Windows Server 2003 zu Azure entscheiden, können Sie das Azure Migrate: Servermigrationstool verwenden, wenn es sich bei Ihrem Windows-Server um eine VM unter VMware oder Hyper-V handelt. Lesen Sie diesen Artikel, um die [Windows Server 2003-Computer auf die Migration vorzubereiten](./prepare-windows-server-2003-migration.md).

## <a name="what-is-the-difference-between-the-test-migration-and-migrate-operations"></a>Worin besteht der Unterschied zwischen der Testmigration und den Migrationsvorgängen?

Die Test Migration bietet eine Möglichkeit zum Testen und Überprüfen von Migrationen vor der eigentlichen Migration. Die Testmigration ermöglicht Ihnen das Erstellen von Testkopien replizierender VMs in einer Sandboxumgebung in Azure. Die Sandboxumgebung wird von einem virtuellen Testnetzwerk abgegrenzt, das Sie angeben. Der Testmigrationsvorgang ist unterbrechungsfrei. Dabei werden Anwendungen weiterhin auf dem Quellcomputer ausgeführt, während Sie Tests an einer geklonten Kopie in einer isolierten Sandboxumgebung durchführen können. Sie können nach Bedarf mehrere Tests durchführen, um die Migration zu validieren, App-Tests durchzuführen und alle Probleme vor der eigentlichen Migration zu beheben.

## <a name="will-windows-server-2008-and-2008-r2-be-supported-in-azure-after-migration"></a>Werden Windows Server 2008 und 2008 R2 nach der Migration in Azure unterstützt?

Sie können Ihre lokalen Windows Server 2008- und 2008 R2-Server zu virtuellen Computern in Azure migrieren und erweiterte Sicherheitsupdates für einen Zeitraum von drei Jahren nach dem Ende des Supportvertrags ohne zusätzliche Kosten zu den Kosten für den Betrieb des virtuellen Computers erhalten. Sie können Azure Migrate: Servermigrationstool zum Migrieren Ihrer Windows Server 2008- und 2008 R2-Workloads verwenden.

## <a name="is-there-a-rollback-option-for-azure-migrate"></a>Gibt es eine Rollbackoption für Azure Migrate?

Sie können die Testmigrationsoption verwenden, um die Anwendungsfunktionalität und die Leistung in Azure zu überprüfen. Sie können eine beliebige Anzahl von Testmigrationen und dann die endgültige Migration ausführen, nachdem Sie durch den Testmigrationsvorgang Vertrauen gewonnen haben. Eine Testmigration wirkt sich nicht auf den lokalen Computer aus, der weiterhin betriebsbereit ist und die Replikation fortsetzt, bis Sie die tatsächliche Migration durchführen. Wenn während der Testmigration-UAT Fehler aufgetreten sind, können Sie die endgültige Migration verschieben und den virtuellen Quellcomputer/Server weiterhin ausführen und in Azure replizieren. Nachdem Sie die Fehler behoben haben, können Sie die endgültige Migration erneut versuchen.  
Hinweis: Wenn Sie eine endgültige Migration zu Azure durchgeführt haben und der lokale Quellcomputer heruntergefahren wurde, können Sie kein Rollback aus Azure in Ihre lokale Umgebung durchführen.

## <a name="can-i-select-the-virtual-network-and-subnet-to-use-for-test-migrations"></a>Kann ich das virtuelle Netzwerk und Subnetz auswählen, das für Testmigrationen verwendet werden soll?

Sie können ein virtuelles Netzwerk für Testmigrationen auswählen. Das Subnetz wird automatisch basierend auf der folgenden Logik ausgewählt:

- Wenn bei der Aktivierung der Replikation ein Zielsubnetz (ein anderes Subnetz als das Standardsubnetz) als Eingabe angegeben wurde, dann priorisiert Azure Migrate die Verwendung eines gleichnamigen Subnetzes in dem für die Testmigration ausgewählten virtuellen Netzwerk.
- Wenn das gleichnamige Subnetz nicht gefunden wird, wählt Azure Migrate alphabetisch das erste verfügbare Subnetz aus, das kein Subnetz vom Typ Gateway/Anwendungsgateway/Firewall/Bastion ist.

## <a name="why-is-the-test-migration-button-disabled-for-my-server"></a>Warum ist die Schaltfläche „Testmigration“ für meinen Server deaktiviert?

Die Schaltfläche „Testmigration“ kann in den folgenden Szenarien einen deaktivierten Zustand aufweisen:

- Sie können eine Testmigration erst starten, wenn für die VM eine anfängliche Replikation (Initial Replication, IR) abgeschlossen wurde. Die Schaltfläche „Testmigration“ bleibt deaktiviert, bis der IR-Prozess abgeschlossen wurde. Sie können eine Testmigration durchführen, wenn sich Ihre VM in einer Deltasynchronisierungsphase befindet.
- Die Schaltfläche kann deaktiviert sein, wenn eine Testmigration bereits abgeschlossen wurde, für diese VM jedoch noch keine Bereinigung der Testmigration durchgeführt wurde. Führen Sie eine Bereinigung der Testmigration durch, und wiederholen Sie den Vorgang.

## <a name="what-happens-if-i-dont-clean-up-my-test-migration"></a>Was geschieht, wenn ich meine Testmigration nicht bereinige?

Eine Testmigration simuliert die tatsächliche Migration durch Erstellen eines virtuellen Azure-Testcomputers mithilfe von replizierten Daten. Der Server wird mit einer Zeitpunktkopie der replizierten Daten mit dem Suffix „-test“ in der (bei der Aktivierung der Replikation ausgewählten) Zielressourcengruppe bereitgestellt. Testmigrationen sind zum Überprüfen der Server Funktionalität vorgesehen, sodass Probleme nach der Migration minimiert werden. Wenn die Testmigration nach dem Testen nicht bereinigt wird, wird der virtuelle Testcomputer weiterhin in Azure ausgeführt, und es fallen Gebühren an. Um die Bereinigung nach einer Testmigration durchführen zu können, wechseln Sie im Servermigrationstool zur Ansicht der replizierenden Computer, und verwenden Sie die Aktion „Testmigration bereinigen“ auf dem Computer.

## <a name="can-i-migrate-active-directory-domain-controllers-using-azure-migrate"></a>Kann ich Active Directory-Domänencontroller mithilfe von Azure Migrate migrieren?

Das Servermigrationstool ist anwendungsagnostisch und funktioniert für die meisten Anwendungen. Wenn Sie einen Server mit dem Servermigrationstool migrieren, werden alle auf dem Server installierten Anwendungen zusammen mit dem Server migriert. Für einige Anwendungen sind jedoch andere Migrationsmethoden als die Servermigration möglicherweise besser für die Migration geeignet.  Für Active Directory können Sie im Fall von Hybridumgebungen, in denen der lokale Standort mit Ihrer Azure-Umgebung verbunden ist, Ihr Verzeichnis in Azure erweitern, indem Sie zusätzliche Domänencontroller in Azure hinzufügen und Active Directory-Replikation einrichten. Wenn Sie zu einer isolierten Umgebung in Azure migrieren, die eigene Domänencontroller erfordert (oder Anwendungen in einer Sandboxumgebung testen), können Sie Server mit dem Servermigrationstool migrieren.

## <a name="what-happens-if-i-dont-stop-replication-after-migration"></a>Was geschieht, wenn ich die Replikation nach der Migration nicht beende?

Wenn Sie die Replikation beenden, bereinigt das Azure Migrate: Servermigrationstool die verwalteten Datenträger im Abonnement, die für die Replikation erstellt wurden. Wenn Sie die Replikation nach einer Migration nicht beenden, fallen weiterhin Gebühren für diese Datenträger an. Das Beenden der Replikation wirkt sich nicht auf die Datenträger aus, die an bereits migrierte Computer angefügt sind.

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>Benötige ich VMware vCenter, um VMware-VMs zu migrieren?

Zum [Migrieren von VMware-VMs](server-migrate-overview.md) mithilfe der VMware-Migration mit oder ohne Agent müssen ESXi-Hosts, auf denen sich VMs befinden, von vCenter Server verwaltet werden. Wenn Sie nicht über vCenter Server verfügen, können Sie VMware-VMs migrieren, indem Sie sie als physische Server migrieren. [Weitere Informationen](migrate-support-matrix-physical-migration.md)

## <a name="can-i-upgrade-my-os-while-migrating"></a>Kann ich mein Betriebssystem während der Migration aktualisieren?

Das Azure Migrate: Servermigrationstool unterstützt derzeit nur Migrationen für identische Betriebssysteme. Das Tool unterstützt kein Upgrade der Betriebssystemversion während der Migration. Der migrierte Computer weist das gleiche Betriebssystem wie der Quellcomputer auf.

## <a name="i-deployed-two-or-more-appliances-to-discover-vms-in-my-vcenter-server-however-when-i-try-to-migrate-the-vms-i-only-see-vms-corresponding-to-one-of-the-appliances"></a>Ich habe zwei (oder mehr) Appliances zum Entdecken von VMs auf meiner vCenter Server-Instanz bereitgestellt. Wenn ich versuche, die VMs zu migrieren, werden mir nur VMs angezeigt, die einer der Appliances entsprechen.

Wenn mehrere Appliances eingerichtet sind, ist es erforderlich, dass es keine Überlappungen zwischen den VMs auf den bereitgestellten vCenter-Konten gibt. Eine Entdeckung mit einer solchen Überlappung ist ein nicht unterstütztes Szenario.

## <a name="how-do-i-know-if-my-vm-was-successfully-migrated"></a>Woran erkenne ich, ob meine VM erfolgreich migriert wurde?

Nachdem Sie Ihren virtuellen Computer/Server erfolgreich migriert haben, können Sie die VM über die Seite „Virtual Machines“ anzeigen und verwalten. Stellen Sie eine Verbindung mit der migrierten VM her, um dies zu überprüfen.
Alternativ können Sie den „Auftragsstatus“ für den Vorgang überprüfen, um zu ermitteln, ob die Migration erfolgreich abgeschlossen wurde. Wenn Fehler angezeigt werden, beheben Sie diese, und wiederholen Sie den Migrationsvorgang.

## <a name="can-i-consolidate-multiple-source-vms-into-one-vm-while-migrating"></a>Kann ich bei der Migration mehrere Quell-VMs in einer VM konsolidieren?

Die Azure Migrate-Servermigrationsfunktionen unterstützen derzeit nur Migrationen in identische VMs. Das Konsolidieren von Servern oder ein Upgrade des Betriebssystems im Rahmen der Migration wird nicht unterstützt. 

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>Welche Auswirkungen hat die Replikation ohne Agent auf VMware-Server?

Die Replikation ohne Agent hat einige Auswirkungen auf die Leistung von VMware vCenter Server- und VMware ESXi-Hosts. Da die Replikation ohne Agent Momentaufnahmen verwendet, verbraucht sie IOPS im Speicher, sodass eine gewisse IOPS-Speicherbandbreite erforderlich ist. Wir raten davon ab, die Replikation ohne Agent zu verwenden, wenn es in Ihrer Umgebung Einschränkungen hinsichtlich Speicher oder IOPS gibt.


## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich die [Übersicht zu Azure Migrate](migrate-services-overview.md) an.