---
title: Verfügbarkeit von SAP HANA innerhalb einer Azure-Region | Microsoft-Dokumentation
description: In diesem Artikel werden SAP HANA-Vorgänge auf nativen Azure-VMs in einer Azure-Region beschrieben.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e522e358a1b76cea08dac550b33d7a2dfa7d926d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94950226"
---
# <a name="sap-hana-availability-within-one-azure-region"></a>Verfügbarkeit von SAP HANA innerhalb einer Azure-Region
In diesem Artikel werden mehrere Verfügbarkeitsszenarien innerhalb einer Azure-Region beschrieben. Azure bietet viele Regionen auf der ganzen Welt. Die Liste mit Azure-Regionen finden Sie unter [Azure-Regionen](https://azure.microsoft.com/regions/). Für die Bereitstellung von SAP HANA auf VMs in einer Azure-Region bietet Microsoft die Bereitstellung einer einzelnen VM mit einer HANA-Instanz. Zur Erhöhung der Verfügbarkeit können Sie zwei VMs mit zwei HANA-Instanzen innerhalb einer [Azure-Verfügbarkeitsgruppe](../../windows/tutorial-availability-sets.md) bereitstellen, die aus Verfügbarkeitsgründen die HANA-Systemreplikation verwenden. 

Azure bietet derzeit [Azure-Verfügbarkeitszonen](../../../availability-zones/az-overview.md). In diesem Artikel werden Verfügbarkeitszonen nicht im Detail beschrieben. Es wird jedoch allgemein erläutert, wie Verfügbarkeitsgruppen im Vergleich zu Verfügbarkeitszonen verwendet werden.

Azure-Regionen, in denen Verfügbarkeitszonen angeboten werden, umfassen mehrere Rechenzentren. Die Rechenzentren sind in Bezug auf Stromversorgung, Kühlung und Netzwerk autark. Es werden unterschiedliche Zonen in einer einzelnen Azure-Region angeboten, um die Bereitstellung von Anwendungen über zwei oder drei angebotene Verfügbarkeitszonen hinweg zu ermöglichen. Wenn sich Probleme bei der Stromversorgung und bei Netzwerken nur auf die Infrastruktur einer Azure-Verfügbarkeitszone auswirken, ist Ihre Anwendungsbereitstellung innerhalb einer Azure-Region durch das Bereitstellen über verschiedene Zonen weiterhin voll funktionsfähig. Es kann zu einer Kapazitätsbeeinträchtigung kommen. VMs in einer Zone könnten zwar beispielsweise ausfallen, doch VMs in den anderen beiden Zonen wären nach wie vor im Betrieb. 
 
Eine Azure-Verfügbarkeitsgruppe ist eine Funktion zur logischen Gruppierung, mit der sichergestellt werden kann, dass die in der Verfügbarkeitsgruppe enthaltenen VM-Ressourcen voneinander fehlerisoliert sind, wenn sie in einem Azure-Rechenzentrum bereitgestellt werden. Azure stellt sicher, dass die virtuellen Computer innerhalb einer Verfügbarkeitsgruppe auf mehrere physische Server, Compute-Racks, Speichereinheiten und Netzwerkswitches verteilt werden. Diese Konfiguration wird in einigen Azure-Dokumentationen alternativ als Platzierungen in verschiedene [Update- und Fehlerdomänen](../../manage-availability.md) bezeichnet. Diese Platzierungen erfolgen normalerweise in einem Azure-Rechenzentrum. Unter der Annahme, dass Probleme an Stromversorgungsquellen und/oder Netzwerken das Rechenzentrum beeinträchtigen, würde Ihre gesamte Kapazität in einer Azure-Region betroffen werden.

Bei der Platzierung von Rechenzentren, die Azure-Verfügbarkeitszonen darstellen, wird ein Mittelweg zwischen akzeptablen Netzwerklatenzen zwischen Diensten, die in unterschiedlichen Zonen bereitgestellt werden, und einer Entfernung zwischen den Rechenzentren verfolgt. Damit soll im Idealfall erreicht werden, dass die Stromversorgung, das Netzwerk und die Infrastruktur für alle Verfügbarkeitszonen in dieser Region vor Naturkatastrophen geschützt bleiben. Doch angesichts des gewaltigen Ausmaßes, das sich bei Naturkatastrophen abgezeichnet hat, können Verfügbarkeitszonen nicht immer die von Ihnen gewünschte Verfügbarkeit innerhalb einer Region bieten. Denken Sie daran, als Hurrikan Maria am 20. September 2017 die Insel Puerto Rico durchzog. Der Hurrikan richtete fast einen kompletten Stromausfall auf der etwa 150 km langen Insel an.

## <a name="single-vm-scenario"></a>Szenario mit einer einzigen VM

In einem Szenario mit einer einzigen VM erstellen Sie eine Azure-VM für die SAP HANA-Instanz. Sie verwenden Azure Storage Premium zum Hosten des Betriebssystemdatenträgers und aller Datenträger. Die SLA von Azure mit einer Betriebszeit von 99,9 % und die SLAs anderer Azure-Komponenten reichen für Sie aus, um Ihren Verfügbarkeits-SLAs für Ihre Kunden gerecht zu werden. In diesem Szenario besteht kein Bedarf dafür, eine Azure-Verfügbarkeitsgruppe für VMs, die die DBMS-Ebene ausführen, zu nutzen. In diesem Szenario kommen zwei verschiedene Features zum Einsatz:

- Automatischer Neustart von Azure-VMs (auch als „Azure-Dienstreparatur“ bezeichnet)
- Automatischer Neustart von SAP HANA

Die Funktion „Automatischer Neustart von Azure-VMs“ bzw. „Dienstreparatur“ funktioniert in Azure auf zwei Ebenen:

- Der Azure-Serverhost überprüft die Integrität einer VM, die auf dem Serverhost gehostet wird.
- Der Azure Fabric Controller überwacht die Integrität und Verfügbarkeit des Serverhosts.

Bei jeder VM, die von einem Azure-Serverhost gehostet wird, wird die Integrität der gehosteten VMs durch eine Funktion zur Integritätsprüfung überwacht. Falls eine VM in einen fehlerhaften Zustand wechseln sollte, kann ein VM-Neustart durch den Azure-Host-Agent initiiert werden, der die Integrität der VM überprüft. Der Fabric Controller überwacht die Integrität des Hosts durch Überprüfung zahlreicher verschiedener Parameter, die auf Probleme mit der Hosthardware hinweisen könnten. Zudem überprüft er den Zugriff auf den Host über das Netzwerk. Ein Hinweis auf Probleme mit dem Host kann zu folgenden Ereignissen führen:

- Ein Neustart des Hosts und der VMs, die auf dem Host ausgeführt wurden, wird ausgelöst, wenn der Host einen fehlerhaften Zustand signalisiert.
- Wenn sich der Host nach dem erfolgreichen Neustart nicht in einem fehlerfreien Zustand befindet, wird eine erneute Bereitstellung der VMs, die sich ursprünglich auf dem nun fehlerhaften Knoten befunden haben, auf einem fehlerfreien Hostserver initiiert. In diesem Fall wird der ursprüngliche Host als fehlerhaft gekennzeichnet. Er wird erst für weitere Bereitstellungen verwendet, wenn er gelöscht oder ersetzt wird.
- Ein sofortiger Neustart der VMs auf einem fehlerfreien Host wird in Fällen ausgelöst, in denen beim fehlerhaften Host Probleme im Zusammenhang mit dem Neustart auftreten. 

Durch die von Azure bereitgestellte Host- und VM-Überwachung werden Azure-VMs, bei denen Probleme mit dem Host auftreten, automatisch auf einem fehlerfreien Azure-Host neu gestartet. 

>[!IMPORTANT]
>Die Azure-Dienstreparatur startet Linux-VMs nicht neu, wenn sich das Gastbetriebssystem in einem Kernel Panic-Status befindet. Die Standardeinstellungen der häufig verwendeten Linux-Versionen starten VMs oder Server nicht automatisch neu, wenn der Linux-Kernel im Panic-Status ist. Stattdessen sorgt die Standardeinstellung dafür, dass das Betriebssystem im Kernel Panic-Status verbleibt, um einen Kernel-Debugger für die Analyse anzufügen. Azure berücksichtigt dieses Verhalten, indem VMs mit dem Gastbetriebssystem in einem solchen Status nicht automatisch neu gestartet werden. Es wird davon ausgegangen, dass solche Vorkommnisse äußerst selten sind. Sie können das Standardverhalten außer Kraft setzen, um einen Neustart der VM zu ermöglichen. Aktivieren Sie zum Ändern des Standardverhaltens den Parameter „kernel.panic“ in „/etc/sysctl.conf“. Die Zeit, die Sie für diesen Parameter festlegen, wird in Sekunden angegeben. Es wird allgemein empfohlen, 20 bis 30 Sekunden zu warten, bevor der Neustart mit diesem Parameter ausgelöst wird. Siehe auch <https://gitlab.com/procps-ng/procps/blob/master/sysctl.conf>.

Das zweite Feature, auf das Sie in einem solchen Szenario angewiesen sind, betrifft den Vorgang, bei dem der auf einer neu gestarteten VM ausgeführte HANA-Dienst nach dem Neustart der VM automatisch gestartet wird. Sie können das Feature [Automatischer Neustart des HANA-Diensts](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) über die Watchdogdienste der verschiedenen HANA-Dienste konfigurieren.

Sie können dieses Szenario mit einer einzelnen VM durch Hinzufügen eines inaktiven Failoverknotens zu einer SAP HANA-Konfiguration optimieren. In der SAP HANA-Dokumentation wird diese Einrichtung als [Automatisches Hostfailover](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html) bezeichnet. Diese Konfiguration kann bei einer lokalen Bereitstellung sinnvoll sein, bei der die Serverhardware begrenzt ist und Sie für eine Gruppe von Produktionshosts einen einzelnen Serverknoten als Knoten zum automatischen Hostfailover reservieren. In Azure, in dem die zugrunde liegende Infrastruktur von Azure einen fehlerfreien Zielserver für einen erfolgreichen Neustart einer VM bereitstellt, ist die Bereitstellung des SAP HANA-Szenarios mit dem automatischen Hostfailover nicht sinnvoll. Wegen der Azure-Dienstreparatur gibt es keine Referenzarchitektur, in der das automatische Hostfailover von HANA für einen Standby-Knoten vorgesehen ist. 

### <a name="special-case-of-sap-hana-scale-out-configurations-in-azure"></a>Sonderfall von SAP HANA-Konfigurationen mit horizontaler Skalierung in Azure
Die hohe Verfügbarkeit für SAP HANA-Konfigurationen mit horizontaler Skalierung basiert auf der Dienstreparatur von Azure-VMs und dem Neustart der SAP HANA-Instanz, wenn die VM wieder betriebsbereit ist. Hochverfügbarkeitsarchitekturen auf der Basis der HANA-Systemreplikation werden zu einem späteren Zeitpunkt eingeführt. 


## <a name="availability-scenarios-for-two-different-vms"></a>Verfügbarkeitsszenarien mit zwei verschiedenen VMs

Wenn Sie zwei Azure-VMs in einer Azure-Verfügbarkeitsgruppe verwenden, können Sie die Betriebszeit zwischen diesen beiden VMs erhöhen, wenn sie in einer Azure-Verfügbarkeitsgruppe innerhalb einer Azure-Region platziert werden. Die grundlegende Einrichtung in Azure würde wie folgt aussehen:

![Abbildung mit zwei VMs mit allen Ebenen](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

Um die verschiedenen Verfügbarkeitsszenarien zu veranschaulichen, wurden einige Ebenen in der Abbildung ausgelassen. In der Abbildung werden nur die Ebenen dargestellt, die VMs, Hosts, Verfügbarkeitsgruppen und Azure-Regionen zeigen. Instanzen, Ressourcengruppen und Abonnements virtueller Azure-Netzwerke spielen bei den in diesem Abschnitt beschriebenen Szenarien keine Rolle.

### <a name="replicate-backups-to-a-second-virtual-machine"></a>Replizieren von Sicherungen auf einen zweiten virtuellen Computer

Eine der elementarsten Einrichtungen ist die Verwendung von Sicherungen. Dies ist insbesondere dann sinnvoll, wenn Sie Transaktionsprotokollsicherungen von einer VM für eine andere Azure-VM bereitstellen. Sie können den Azure Storage-Typ auswählen. Bei diesem Setup sind Sie für die Skripterstellung der Kopie der geplanten Sicherungen verantwortlich, die für die erste und zweite VM durchgeführt werden. Wenn Sie die Instanzen der zweiten VM verwenden müssen, müssen Sie die vollständigen inkrementellen bzw. differenziellen Sicherungen von Transaktionsprotokollen am erforderlichen Zeitpunkt wiederherstellen. 

Die Architektur sieht wie folgt aus:

![Diagramm: Architektur der beiden VMs mit Speicherreplikation.](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

Diese Einrichtung ist nicht geeignet, um hervorragende Zeiten hinsichtlich der Recovery Point Objective (RPO) und Recovery Time Objective (RTO) zu erzielen. Insbesondere bei den RTO-Zeiten müssen Einbußen in Kauf genommen werden, da die gesamte Datenbank mit den kopierten Sicherungen vollständig wiederhergestellt werden muss. Dieses Setup ist jedoch nützlich, um nach einer unbeabsichtigten Datenlöschung in den Hauptinstanzen Wiederherstellungen durchzuführen. Mit dieser Einrichtung können Sie jederzeit eine Wiederherstellung zu einem bestimmten Zeitpunkt durchführen, die Daten extrahieren und die gelöschten Daten in Ihre Hauptinstanz importieren. Daher kann es sinnvoll sein, eine solche Methode zur Sicherungskopie in Kombination mit anderen Hochverfügbarkeitsfunktionen zu verwenden. 

Während des Kopiervorgangs der Sicherungen, können Sie möglicherweise eine kleineren VM als die Haupt-VM verwenden, auf der die SAP HANA-Instanz ausgeführt wird. Denken Sie daran, dass Sie eine kleinere Anzahl von VHDs an kleinere VMs anfügen können. Informationen zu den Grenzwerte für die einzelnen VM-Typen finden Sie unter [Größen für virtuelle Linux-Computer in Azure](../../sizes.md).

### <a name="sap-hana-system-replication-without-automatic-failover"></a>SAP HANA-Systemreplikation ohne automatisches Failover

Bei den Szenarien, die in diesem Abschnitt beschrieben werden, wird die SAP HANA-Replikation verwendet. Die SAP-Dokumentation finden Sie unter [Systemreplikation](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html). Szenarien ohne automatisches Failover sind für Konfigurationen innerhalb einer Azure-Region nicht üblich. Bei einer Konfiguration ohne automatisches Failover, bei der jedoch ein Pacemaker-Setup vermieden wird, müssen Sie die Überwachung und das Failover manuell durchführen. Da dies auch mit Aufwand verbunden ist, verlassen sich die meisten Kunden stattdessen auf die Azure-Dienstreparatur. In einigen Grenzfällen kann eine Konfiguration dieser Art bei Ausfallszenarien Abhilfe leisten. In anderen Fällen sollte ein Kunde eventuell eine größere Effizienz realisieren.

#### <a name="sap-hana-system-replication-without-auto-failover-and-without-data-preload"></a>SAP HANA-Systemreplikation ohne automatisches Failover und ohne Laden von Daten im Voraus

In diesem Szenario verwenden Sie die SAP HANA-Systemreplikation zum synchronen Verschieben von Daten, um eine RPO von 0 zu erzielen. Auf der anderen Seite benötigen Sie eine RTO, die lang genug ist, dass ein Failover oder das Laden von Daten im Voraus in den HANA-Instanzcache überflüssig ist. In diesem Fall ist es möglich, Ihre Konfiguration durch die folgenden Aktionen weiter zu optimieren:

- Führen Sie eine andere SAP HANA-Instanz auf der zweiten VM aus. Die SAP HANA-Instanz auf der zweiten VM belegt den Großteil des VM-Speichers. Bei einem Failover auf die zweite VM müssen Sie die aktive SAP HANA-Instanz herunterfahren, die die Daten vollständig in der zweiten VM geladen hat, damit die replizierten Daten in den Cache der Ziel-HANA-Instanz auf der zweiten VM geladen werden können.
- Verwenden Sie eine kleinere VM als zweite VM. Wenn ein Failover auftritt, müssen Sie vor dem manuellen Failover einen zusätzlichen Schritt durchführen. Bei diesem Schritt ändern Sie die Größe der VM in die der Quell-VM. 
 
Das Szenario sieht folgendermaßen aus:

![Abbildung mit zwei VMs mit Speicherreplikation](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> Selbst wenn Sie Daten im Replikationsziel für das HANA-System nicht im Voraus laden, benötigen Sie mindestens 64 GB Arbeitsspeicher. Sie benötigen zusätzlich zu den 64 GB auch genügend Arbeitsspeicher, um die Rowstore-Daten im Arbeitsspeicher der Zielinstanz beizubehalten.

#### <a name="sap-hana-system-replication-without-auto-failover-and-with-data-preload"></a>SAP HANA-Systemreplikation ohne automatisches Failover und mit Laden von Daten im Voraus

In diesem Szenario werden Daten, die auf der zweiten VM in der HANA-Instanz repliziert wurden, im Voraus geladen. Hierdurch fallen die beiden Vorteile weg, dass Daten nicht im Voraus geladen werden müssen. In diesem Fall können Sie kein weiteres SAP HANA-System auf der zweiten VM ausführen. Zudem kann auch keine kleinere VM-Größe verwendet werden. Kunden implementieren dieses Szenario daher nur selten.

### <a name="sap-hana-system-replication-with-automatic-failover"></a>SAP HANA-Systemreplikation mit automatischem Failover

In der standardmäßigen und am häufigsten verwendeten Verfügbarkeitskonfiguration innerhalb einer Azure-Region ist bei zwei Azure-VMs, auf denen SLES (SUSE Linux Enterprise Server) ausgeführt wird, ein Failovercluster definiert. Der SLES-Cluster (SUSE Linux Enterprise Server) basiert auf dem [Pacemaker](./high-availability-guide-suse-pacemaker.md)-Framework in Verbindung mit einem [STONITH](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device)-Gerät. 

Aus SAP HANA-Sicht wird der verwendete Replikationsmodus synchronisiert, und ein automatisches Failover wird konfiguriert. Auf der zweiten VM agiert die SAP HANA-Instanz als Hot Standby-Knoten. Der Standby-Knoten empfängt einen synchronen Datenstrom von Änderungsdatensätzen aus der primären SAP HANA-Instanz. Da Transaktionen von der Anwendung auf dem primären HANA-Knoten committed werden, wartet dieser mit der Bestätigung des Commits in der Anwendung, bis der sekundäre SAP HANA-Knoten den Commitdatensatz bestätigt hat. SAP HANA bietet zwei verschiedene Modi für die synchrone Replikation. Einzelheiten und eine Beschreibung zu den Unterschieden zwischen diesen beiden Modi für die synchrone Replikation finden Sie im Artikel [Replication modes for SAP HANA System Replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html) (Replikationsmodi für die SAP HANA-Systemreplikation).

Die allgemeine Konfiguration sieht wie folgt aus:

![Abbildung mit zwei VMs mit Speicherreplikation und Failover](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

Diese Lösung ist für Sie empfehlenswert, da Sie hierüber eine RPO von 0 und kurze RTO-Zeiten erzielen. Konfigurieren Sie die SAP HANA-Clientkonnektivität so, dass die SAP HANA-Clients zum Herstellen einer Verbindung mit der HANA-Systemreplikationskonfiguration die virtuelle IP-Adresse verwenden. Durch eine solche Konfiguration entfällt die Notwendigkeit, die Anwendung bei einem Failover auf den sekundären Knoten erneut zu konfigurieren. Bei diesem Szenario müssen die Azure-VM-SKUs für die primären und sekundären VMs identisch sein.

## <a name="next-steps"></a>Nächste Schritte

Eine ausführliche Anleitung zum Einrichten dieser Konfigurationen in Azure finden Sie in folgenden Artikeln:

- [Hochverfügbarkeit von SAP HANA auf virtuellen Azure-Computern (VMs)](sap-hana-high-availability.md)
- [Your SAP on Azure – Part 4 – High Availability for SAP HANA using System Replication](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/) (SAP in Azure – Teil 4: Hochverfügbarkeit für SAP HANA durch Systemreplikation)

Weitere Informationen zur SAP HANA-Verfügbarkeit in Azure-Regionen finden Sie im folgenden Artikel:

- [SAP HANA-Verfügbarkeit in den Azure-Regionen](./sap-hana-availability-across-regions.md)