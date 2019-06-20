---
title: Grundlegendes zum Systemneustart für virtuelle Azure-Computer | Microsoft-Dokumentation
description: Hier werden die Ereignisse aufgeführt, die zum Neustart eines virtuellen Computers führen können.
services: virtual-machines
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 70a6845349b90cf614a84e13680ebb6fc6b3e2a9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60443754"
---
# <a name="understand-a-system-reboot-for-azure-vm"></a>Informationen zum Systemneustart für virtuelle Azure-Computer

Virtuelle Azure-Computer (VMs) werden unter Umständen manchmal ohne erkennbaren Grund neu gestartet, ohne dass ein Nachweis dafür vorliegt, dass der Neustartvorgang von Ihnen initiiert wurde. In diesem Artikel sind die Aktionen und Ereignisse aufgeführt, die dazu führen können, dass virtuelle Computer neu gestartet werden. Außerdem erfahren Sie, wie Sie unerwartete Neustartprobleme vermeiden oder die Auswirkungen dieser Probleme reduzieren können.

## <a name="configure-the-vms-for-high-availability"></a>Konfigurieren von virtuellen Computern für Hochverfügbarkeit

Die beste Möglichkeit zum Schützen einer in Azure ausgeführten Anwendung vor Neustarts von virtuellen Computern und damit Ausfallzeiten besteht in der Konfiguration der virtuellen Computer für Hochverfügbarkeit.

Um eine solche Redundanzebene für Ihre Anwendung zu gewährleisten, empfehlen wir die Gruppierung von zwei oder mehr virtuellen Computern in einer Verfügbarkeitsgruppe. Durch diese Konfiguration wird sichergestellt, dass während eines geplanten oder ungeplanten Wartungsereignisses mindestens ein virtueller Computer verfügbar ist und die von der [Azure-SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_5/) zugesicherte Verfügbarkeit von 99,95 Prozent eingehalten wird.

Weitere Informationen zu Verfügbarkeitsgruppen finden Sie in den folgenden Artikeln:

- [Verwalten der Verfügbarkeit von virtuellen Computern](../windows/manage-availability.md)
- [Konfigurieren der Verfügbarkeit von virtuellen Computern](../windows/classic/configure-availability.md)

## <a name="resource-health-information"></a>Informationen zu Resource Health

Azure Resource Health ist ein Dienst, der die Integrität von einzelnen Azure-Ressourcen offenlegt und wertvolle Hinweise zur Behandlung von Problemen bietet. Das Ziel von Resource Health in einer Cloudumgebung, in der es nicht möglich ist, direkt auf Server oder Infrastrukturelemente zuzugreifen, ist die Reduzierung der Zeit, die Sie für die Problembehandlung aufbringen müssen. Das vorrangigste Ziel ist die Reduzierung der Zeit, die Sie mit der Bestimmung verbringen, ob die Hauptursache des Problems die Anwendung oder ein Ereignis auf der Azure-Plattform ist. Weitere Informationen finden Sie unter [Grundlegendes zu Resource Health](../../resource-health/resource-health-overview.md).

## <a name="actions-and-events-that-can-cause-the-vm-to-reboot"></a>Aktionen und Ereignisse, die zu einem Neustart virtueller Computer führen können

### <a name="planned-maintenance"></a>Geplante Wartung

Microsoft Azure führt regelmäßig weltweit Updates aus, um die Zuverlässigkeit, Leistung und Sicherheit der Hostinfrastruktur zu verbessern, die virtuellen Computern zugrunde liegt. Viele dieser Updates, einschließlich speichererhaltender Updates, werden ohne Beeinträchtigung Ihrer virtuellen Computer oder Clouddienste ausgeführt.

Allerdings erfordern einige Updates einen Neustart. In diesen Fällen werden die VMs heruntergefahren, während wir die Infrastruktur aktualisieren, und anschließend neu gestartet.

Grundlagen zur geplanten Wartung in Azure – was sie ist und wie sie sich auf die Verfügbarkeit Ihrer virtuellen Linux-Computer auswirken kann – finden Sie in den hier aufgeführten Artikeln. Die Artikel bieten Hintergrundinformationen zu den Prozessen bei der geplanten Wartung in Azure und zum Erstellen eines Zeitplans für die geplante Wartung, um die Auswirkungen weiter zu verringern.

- [Geplante Wartung für virtuelle Computer in Azure](../windows/planned-maintenance.md)
- [Planen der Wartung auf virtuellen Azure-Computern](../windows/classic/planned-maintenance-schedule.md)

### <a name="memory-preserving-updates"></a>Speichererhaltende Updates

Für diese Klasse von Updates in Microsoft Azure kommt es für Benutzer nicht zu Auswirkungen auf ihre ausgeführten VMs. Viele dieser Updates betreffen Komponenten oder Dienste, die ohne Beeinträchtigung der ausgeführten Instanz aktualisiert werden können. Bei einigen handelt es sich um Plattforminfrastrukturupdates für das Hostbetriebssystem, die ohne Neustart der virtuellen Computer angewendet werden können.

Diese speichererhaltenden Updates werden mit Techniken durchgeführt, die eine direkte Livemigration ermöglichen. Bei der Aktualisierung wird die VM in den Zustand *Angehalten* versetzt. In diesem Zustand wird der Speicherinhalt im RAM beibehalten, während das zugrunde liegende Hostbetriebssystem die erforderlichen Updates und Patches erhält. Der virtuelle Computer wird nach 30 Sekunden Pause wieder fortgesetzt. Nachdem der Betrieb des virtuellen Computers fortgesetzt wird, wird seine Uhr automatisch synchronisiert.

Aufgrund des kurzen Pausenzeitraums werden die Auswirkungen auf die virtuellen Computer durch die Bereitstellung von Updates über diesen Mechanismus stark reduziert. Aber nicht alle Updates können auf diese Weise bereitgestellt werden. 

Updates für mehrere Instanzen (VMs in einer Verfügbarkeitsgruppe) werden nacheinander auf die einzelnen Updatedomänen angewendet.

> [!NOTE]
> Linux-Computer mit alten Kernelversionen werden während dieser Updatemethode durch eine Kernel Panic beeinträchtigt. Um dieses Problem zu vermeiden, aktualisieren Sie den Kernel auf die Version 3.10.0 - 327.10.1 oder höher. Weitere Informationen finden Sie unter [An Azure Linux VM on a 3.10-based kernel panics after a host node upgrade](https://support.microsoft.com/help/3212236) (Kernel Panic auf einer Azure-VM unter Linux mit Kernelversion 3.10 nach einem Upgrade des Hostknotens).

### <a name="user-initiated-reboot-or-shutdown-actions"></a>Vom Benutzer eingeleitete Aktionen zum Neustarten oder Herunterfahren

Wenn Sie einen Neustart über das Azure-Portal, Azure PowerShell, die Befehlszeilenschnittstelle oder die Zurücksetzungs-API durchführen, finden Sie das zugehörige Ereignis im [Azure-Aktivitätsprotokoll](../../azure-monitor/platform/activity-logs-overview.md).

Falls Sie die Aktion über das Betriebssystem des virtuellen Computers durchführen, finden Sie das Ereignis in den Systemprotokollen.

Zu weiteren Szenarien, in denen in der Regel der virtuelle Computer neu gestartet wird, gehören verschiedene Änderungen an der Konfiguration. Meist werden Sie in einer Warnmeldung darüber informiert, dass die Ausführung einer bestimmten Aktion zu einem Neustart des virtuellen Computers führt. Beispiele sind Änderungen an der Größe virtueller Computer, das Ändern des Kennworts für das Administratorkonto und das Festlegen einer statischen IP-Adresse.

### <a name="azure-security-center-and-windows-update"></a>Azure Security Center und Windows Update

Azure Security Center überprüft virtuelle Windows- und Linux-Computer täglich auf fehlende Betriebssystemupdates. Security Center ruft eine Liste mit verfügbaren Sicherheitsupdates und wichtigen Updates von Windows Update oder WSUS (Windows Server Update Services) ab – je nachdem, welcher Dienst für einen virtuellen Windows-Computer konfiguriert ist. Darüber hinaus prüft Security Center auch die neuesten Updates für Linux-Systeme. Falls auf Ihrem virtuellen Computer ein Systemupdate fehlt, empfiehlt Security Center die Anwendung von Systemupdates. Die Anwendung dieser Systemupdates wird über das Security Center im Azure-Portal gesteuert. Nachdem Sie einige Updates angewendet haben, sind unter Umständen Neustarts der virtuellen Computer erforderlich. Weitere Informationen finden Sie unter [Anwenden von Systemupdates in Azure Security Center](../../security-center/security-center-apply-system-updates.md).

Wie bei lokalen Servern überträgt Azure Updates von Windows Update nicht per Push auf virtuelle Windows-Computer, da diese Computer durch ihre Benutzer verwaltet werden sollen. Es empfiehlt sich jedoch, die Einstellung für automatische Windows-Updates aktiviert zu lassen. Die automatische Installation von Updates über Windows Update kann auch dazu führen, dass Neustarts durchgeführt werden, nachdem die Updates angewendet wurden. Weitere Informationen finden Sie unter [Windows Update: FAQ](https://support.microsoft.com/help/12373/windows-update-faq).

### <a name="other-situations-affecting-the-availability-of-your-vm"></a>Andere Situationen mit Einfluss auf die Verfügbarkeit von virtuellen Computern

Es gibt andere Fälle, in denen Azure die Verwendung eines virtuellen Computers aktiv anhalten kann. Sie erhalten E-Mail-Benachrichtigungen, bevor eine solche Aktion durchgeführt wird, damit Sie die zugrunde liegenden Probleme lösen können. Beispiele für Probleme, die sich auf die Verfügbarkeit von virtuellen Computern auswirken, sind Sicherheitsverstöße und der Ablauf von Zahlungsmethoden.

### <a name="host-server-faults"></a>Hostserverfehler

Der virtuelle Computer wird auf einem physischen Server gehostet, der in einem Azure-Rechenzentrum ausgeführt wird. Der physische Server wird als Agent ausgeführt, der als Host-Agent bezeichnet und zusätzlich zu einigen anderen Azure-Komponenten ausgeführt wird. Wenn diese Azure-Softwarekomponenten auf dem physischen Server nicht mehr reagieren, löst das Überwachungssystem einen Neustart des Hostservers aus, um eine Wiederherstellung zu versuchen. Der virtuelle Computer ist in der Regel innerhalb von fünf Minuten wieder verfügbar und wird auf demselben Host wie zuvor fortgesetzt.

Serverfehler werden normalerweise durch einen Hardwarefehler, z.B. den Ausfall einer Festplatte oder eines Solid State Drives, verursacht. Azure überwacht fortlaufend diese Vorkommen, identifiziert die zugrunde liegenden Fehler und führt Updates durch, nachdem die Fehlerbehebung implementiert und getestet wurde.

Da einige Hostserverfehler spezifisch für den jeweiligen Server sein können, kann bei wiederholten Neustarts des virtuellen Computers eine manuelle neue Bereitstellung des virtuellen Computers auf einem anderen Hostserver die Situation verbessern. Dieser Vorgang kann mithilfe der Option **Erneut bereitstellen** auf der Detailseite des virtuellen Computers oder durch Anhalten und Neustarten des virtuellen Computers im Azure-Portal ausgelöst werden.

### <a name="auto-recovery"></a>Automatische Wiederherstellung

Für den Fall, dass der Hostserver aus bestimmten Gründen nicht neu gestartet werden kann, initiiert die Azure-Plattform eine automatische Wiederherstellungsaktion, um den fehlerhaften Hostserver aus der Rotation zu entfernen und genauer zu untersuchen. 

Alle virtuellen Computer auf diesem Host werden automatisch auf einen anderen, fehlerfreien Hostserver verschoben. Dieser Vorgang ist normalerweise innerhalb von 15 Minuten abgeschlossen. Weitere Informationen zum Prozess der automatischen Wiederherstellung finden Sie unter [Auto-recovery of Virtual Machines](https://azure.microsoft.com/blog/service-healing-auto-recovery-of-virtual-machines) (Automatische Wiederherstellung von virtuellen Computern).

### <a name="unplanned-maintenance"></a>Nicht geplante Wartung

In seltenen Fällen muss das Azure-Betriebsteam Wartungsaktivitäten durchführen, um die allgemeine Integrität der Azure-Plattform sicherzustellen. Dieses Verhalten wirkt sich unter Umständen auf die VM-Verfügbarkeit aus. Das Ergebnis ist in der Regel die oben beschriebene automatische Wiederherstellungsaktion.  

Zur nicht geplanten Wartung gehört Folgendes:

- Dringende Knotendefragmentierungen
- Dringende Updates für Netzwerkswitches

### <a name="vm-crashes"></a>VM-Abstürze

Virtuelle Computer werden unter Umständen neu gestartet, falls Probleme in den virtuellen Computern selbst vorliegen. Die Workload oder Rolle, die auf dem virtuellen Computer ausgeführt wird, kann ggf. eine Fehlerüberprüfung innerhalb des Gastbetriebssystems auslösen. Hilfe beim Ermitteln der Ursache für den Absturz finden Sie bei virtuellen Windows-Computern in den System- und Anwendungsprotokollen und bei virtuellen Linux-Computern in den seriellen Protokollen.

### <a name="storage-related-forced-shutdowns"></a>Vom Speicher erzwungenes Herunterfahren

Virtuelle Computer in Azure basieren auf virtuellen Datenträgern für das Betriebssystem und die Speicherung von Daten, die in der Azure Storage-Infrastruktur gehostet werden. Wenn Probleme mit der Verfügbarkeit oder Konnektivität zwischen dem virtuellen Computer und den zugehörigen virtuellen Datenträgern auftreten, die länger als 120 Sekunden anhalten, erzwingt die Azure-Plattform das Herunterfahren des virtuellen Computers, um Datenbeschädigungen zu vermeiden. Die virtuellen Computer werden automatisch wieder eingeschaltet, nachdem die Speicherkonnektivität wiederhergestellt wurde. 

Das Herunterfahren kann lediglich fünf Minuten dauern, aber auch deutlich länger. Im Folgenden finden Sie einen speziellen Fall für das durch den Speicher erzwungene Herunterfahren: 

**Überschreiten der E/A-Grenzwerte**

Virtuelle Computer werden ggf. vorübergehend heruntergefahren, wenn E/A-Anforderungen dauerhaft aufgrund einer großen Menge von E/A-Vorgängen pro Sekunde (IOPS), die die E/A-Grenzwerte für den Datenträger überschreiten, gedrosselt sind. (Der Standard-Datenträgerspeicher ist auf 500 IOPS beschränkt.) Verwenden Sie zum Umgehen dieses Problems abhängig von der Workload das Datenträgerstriping, oder konfigurieren Sie den Speicherplatz auf der Gast-VM. Weitere Informationen finden Sie unter [Configuring Azure VMs for Optimal Storage Performance](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx) (Konfigurieren von virtuellen Azure-Computern für optimale Speicherleistung).

### <a name="other-incidents"></a>Sonstige Vorfälle

In seltenen Fällen kann sich ein großflächig auftretendes Problem auf mehrere Server in einem Azure-Datencenter auswirken. In diesem Fall sendet das Azure-Team E-Mail-Benachrichtigungen an die betroffenen Abonnements. Im [Dashboard zur Azure-Dienstintegrität](https://azure.microsoft.com/status/) und im Azure-Portal finden Sie Informationen zum Status aktueller Ausfälle und von Vorfällen in der Vergangenheit.
