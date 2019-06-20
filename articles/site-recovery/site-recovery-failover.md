---
title: Failover während der Notfallwiederherstellung mit Azure Site Recovery | Microsoft-Dokumentation
description: Hier erhalten Sie Informationen zum Ausführen eines Failovers für virtuelle Computer und physische Server während der Notfallwiederherstellung mit dem Azure Site Recovery-Dienst.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: a02a2be7fb3ed942b1359949e18ba7d3dee824ae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399972"
---
# <a name="fail-over-vms-and-physical-servers"></a>Ausführen eines Failovers für virtuelle Computer und physische Server 

In diesem Artikel wird beschrieben, wie Sie für virtuelle Computer und physische Server, die per Site Recovery geschützt werden, ein Failover durchführen.

## <a name="prerequisites"></a>Voraussetzungen
1. Führen Sie vor einem Failover ein [Testfailover](site-recovery-test-failover-to-azure.md) durch, um sicherzustellen, dass alles wie erwartet funktioniert.
1. [Bereiten Sie das Netzwerk am Zielspeicherort vor](site-recovery-network-design.md), bevor Sie ein Failover durchführen.  

Verwenden Sie folgende Tabelle, um mehr über die Failoveroptionen zu erfahren, die von Azure Site Recovery bereitgestellt werden. Diese Optionen werden auch für andere Failoverszenarien aufgeführt.

| Szenario | Anforderungen für die Anwendungswiederherstellung | Workflow für Hyper-V | Workflow für VMware
|---|--|--|--|
|Geplantes Failover aufgrund einer bevorstehenden Ausfallzeit des Datencenters| Kein Datenverlust für die Anwendung, wenn eine geplante Aktivität ausgeführt wird| Für Hyper-V repliziert ASR Daten mit einer vom Benutzer angegebenen Kopierfrequenz. Ein geplantes Failover wird verwendet, um die Frequenz zu überschreiben und die letzten Änderungen zu replizieren, bevor ein Failover initiiert wird. <br/> <br/> 1.    Planen Sie ein Wartungsfenster gemäß des Change-Management-Prozesses Ihres Unternehmens. <br/><br/> 2. Informieren Sie die Benutzer über bevorstehende Ausfallzeiten. <br/><br/> 3. Schalten Sie die benutzerseitige Anwendung offline.<br/><br/>4. Initiieren Sie das geplante Failover mithilfe des ASR-Portals. Der lokale virtuelle Computer wird automatisch heruntergefahren.<br/><br/>Effektiver Datenverlust der Anwendung = 0 <br/><br/>Ein Journal der Wiederherstellungspunkte wird in einem Aufbewahrungsfenster bereitgestellt, falls ein Benutzer einen älteren Wiederherstellungspunkt verwenden möchte. (Vermerkdauer: 24 Stunden für Hyper-V) Wenn die Replikation außerhalb des Zeitrahmens des Aufbewahrungsfensters beendet wurde, können Kunden möglicherweise weiterhin mit den neuesten verfügbaren Wiederherstellungspunkten Failover durchführen. | Für VMware repliziert ASR kontinuierlich Daten mithilfe von CDP. Ein Failover ermöglicht es dem Benutzer, ein Failover auf die neuesten Daten auszuführen (auch nach Herunterfahren der Anwendung).<br/><br/> 1. Planen Sie ein Wartungsfenster gemäß des Change-Management-Prozesses. <br/><br/>2. Informieren Sie die Benutzer über bevorstehende Ausfallzeiten. <br/><br/>3.    Schalten Sie die benutzerseitige Anwendung offline. <br/><br/>4.  Sobald die Anwendung offline ist, initiieren Sie mithilfe des ASR-Portals ein geplantes Failover zum letzten Punkt. Verwenden Sie im Portal die Option „Ungeplantes Failover“, und wählen Sie den letzten Punkt für das Failover aus. Der lokale virtuelle Computer wird automatisch heruntergefahren.<br/><br/>Effektiver Datenverlust der Anwendung = 0 <br/><br/>Ein Journal der Wiederherstellungspunkte wird in einem Aufbewahrungsfenster bereitgestellt, falls ein Kunde einen älteren Wiederherstellungspunkt verwenden möchte. (Vermerkdauer: 72 Stunden für VMware) Wenn die Replikation außerhalb des Zeitrahmens des Aufbewahrungsfensters beendet wurde, können Kunden möglicherweise weiterhin mit den neuesten verfügbaren Wiederherstellungspunkten Failover durchführen.
|Failover aufgrund einer ungeplanten Ausfallzeit des Datencenters (natürlich oder IT-Notfall) | Minimaler Datenverlust für die Anwendung | 1. Initiieren Sie den BCP-Plan der Organisation. <br/><br/>2. Initiieren Sie ein ungeplantes Failover zum letzten Punkt oder zu einem Punkt im Aufbewahrungsfenster (Journal) mithilfe des ASR-Portals.| 1. Initiieren Sie den BCP-Plan der Organisation. <br/><br/>2.  Initiieren Sie ein ungeplantes Failover zum letzten Punkt oder zu einem Punkt im Aufbewahrungsfenster (Journal) mithilfe des ASR-Portals.


## <a name="run-a-failover"></a>Ausführen eines Failovers
Hier erfahren Sie, wie Sie ein Failover für einen [Wiederherstellungsplan](site-recovery-create-recovery-plans.md) durchführen. Alternativ können Sie das Failover auch über die Seite **Replizierte Elemente** für einen einzelnen virtuellen Computer oder physischen Server durchführen, wie [hier](vmware-azure-tutorial-failover-failback.md#run-a-failover-to-azure) beschrieben wird.


![Failover](./media/site-recovery-failover/Failover.png)

1. Wählen Sie **Wiederherstellungspläne** > *Name des Wiederherstellungsplans* aus. Klicken Sie auf **Failover**.
2. Wählen Sie auf dem Bildschirm **Failover** einen **Wiederherstellungspunkt** für das Failover aus. Sie können eine der folgenden Optionen auswählen:
   1. **Letzter Zeitpunkt**: Diese Option startet den Auftrag, indem zunächst alle Daten verarbeitet werden, die an den Site Recovery-Dienst gesendet wurden. Durch die Verarbeitung der Daten wird einen Wiederherstellungspunkt für die einzelnen virtuellen Computer erstellt. Dieser Wiederherstellungspunkt wird während des Failovers vom virtuellen Computer verwendet. Die Option verfügt über die niedrigste RPO (Recovery Point Objective), da der nach dem Failover erstellte virtuelle Computer über alle Daten verfügt, die bei Auslösung des Failovers im Site Recovery-Dienst repliziert wurden.
   1. **Letzte Verarbeitung**: Diese Option führt ein Failover für alle virtuellen Computer des Wiederherstellungsplans auf den letzten Wiederherstellungspunkt durch, der bereits vom Site Recovery-Dienst verarbeitet wurde. Wenn Sie das Testfailover für einen virtuellen Computer durchführen, wird zusätzlich der Zeitstempel des zuletzt verarbeiteten Wiederherstellungspunkts angezeigt. Wenn Sie das Failover eines Wiederherstellungsplans durchführen, können Sie zu einzelnen virtuellen Computern wechseln und die Kachel **Neueste Wiederherstellungspunkte** anzeigen, um die entsprechenden Informationen abzurufen. Da keine Zeit mit der Verarbeitung nicht verarbeiteter Daten verbracht wird, bietet diese Option die Möglichkeit zum Failover mit geringem RTO-Wert (Recovery Time Objective, angestrebte Wiederherstellungszeit).
   1. **Letzter anwendungskonsistenter Zeitpunkt**: Diese Option führt ein Failover für alle virtuellen Computer des Wiederherstellungsplans auf den letzten anwendungskonsistenten Wiederherstellungspunkt durch, der bereits von Site Recovery verarbeitet wurde. Wenn Sie das Testfailover für einen virtuellen Computer ausführen, wird zusätzlich der Zeitstempel des letzten anwendungskonsistenten Wiederherstellungspunkts angezeigt. Wenn Sie das Failover eines Wiederherstellungsplans durchführen, können Sie zu einzelnen virtuellen Computern wechseln und die Kachel **Neueste Wiederherstellungspunkte** anzeigen, um die entsprechenden Informationen abzurufen.
   1. **Letzte Verarbeitung mit mehreren VMs**: Diese Option steht nur für Wiederherstellungspläne zur Verfügung, bei denen für mindestens einen virtuellen Computer die Multi-VM-Konsistenz aktiviert ist. Virtuelle Computer, die Teil einer Replikationsgruppe sind, führen ein Failover auf den neuesten allgemeinen Wiederherstellungspunkt mit Multi-VM-Konsistenz durch. Andere virtuelle Computer führen ein Failover auf ihren neuesten verarbeiteten Wiederherstellungspunkt durch.  
   1. **Letzter anwendungskonsistenter Zeitpunkt (mehrere VMs)** : Diese Option steht nur für Wiederherstellungspläne zur Verfügung, bei denen für mindestens einen virtuellen Computer die Multi-VM-Konsistenz aktiviert ist. Virtuelle Computer, die Teil einer Replikationsgruppe sind, führen ein Failover auf den neuesten allgemeinen Wiederherstellungspunkt mit Multi-VM-Anwendungskonsistenz durch. Andere virtuelle Computer führen ein Failover auf ihren neuesten anwendungskonsistenten Wiederherstellungspunkt durch.
   1. **Benutzerdefiniert**: Beim Ausführen des Testfailovers für einen virtuellen Computer können Sie diese Option verwenden, um ein Failover auf einen bestimmten Wiederherstellungspunkt durchzuführen.

      > [!NOTE]
      > Die Option zum Auswählen eines Wiederherstellungspunkts ist nur verfügbar, wenn Sie ein Failover zu Azure durchführen.
      >
      >


1. Falls für einige virtuelle Computer des Wiederherstellungsplans bei einer vorherigen Ausführung ein Failover durchgeführt wurde und die virtuellen Computer jetzt sowohl am Quell- als auch am Zielspeicherort aktiv sind, können Sie die Option **Richtung ändern** wählen, um die Richtung für das Failover anzugeben.
1. Wenn Sie ein Failover zu Azure durchführen und die Datenverschlüsselung für die Cloud aktiviert ist (gilt nur, wenn Sie über geschützte virtuelle Hyper-V-Computer eines VMM-Servers verfügen), müssen Sie unter **Verschlüsselungsschlüssel** das Zertifikat auswählen, das während des Setups auf dem VMM-Server beim Aktivieren der Datenverschlüsselung ausgestellt wurde.
1. Klicken Sie auf **Der Computer wird vor Beginn des Failovers heruntergefahren**, wenn Site Recovery versuchen soll, virtuelle Quellcomputer herunterzufahren, bevor das Failover ausgelöst wird. Das Failover wird auch dann fortgesetzt, wenn das Herunterfahren nicht erfolgreich war.  

    > [!NOTE]
    > Wenn virtuelle Hyper-V-Computer geschützt sind, versucht die Option zum Herunterfahren vor dem Auslösen des Failovers ebenfalls, die lokalen Daten, die noch nicht an den Dienst gesendet wurden, zu synchronisieren.
    >
    >

1. Der Fortschritt des Failovers wird auf der Seite **Aufträge** angezeigt. Der Wiederherstellungsplan wird bei einem nicht geplanten Failover auch dann bis zum Abschluss ausgeführt, wenn Fehler auftreten.
1. Überprüfen Sie den virtuellen Computer nach Abschluss des Failovers, indem Sie sich am Computer anmelden. Wenn Sie einen anderen Wiederherstellungspunkt für den virtuellen Computer verwenden möchten, können Sie die Option **Wiederherstellungspunkt ändern** verwenden.
1. Nachdem Sie mit dem virtuellen Computer, für den das Failover durchgeführt wurde, zufrieden sind, können Sie für das Failover die Option **Commit** wählen. Die Option **Commit** löscht alle verfügbaren Wiederherstellungspunkte des Diensts, und die Option **Wiederherstellungspunkt ändern** ist nicht mehr verfügbar.

## <a name="planned-failover"></a>Geplantes Failover
Virtuelle Computer/physische Server, die mit Site Recovery geschützt sind, unterstützen auch das **geplante Failover**. Der geplante Failover ist eine Failoveroption ohne jeglichen Datenverlust. Bei Auslösung eines geplanten Failovers werden zuerst die virtuellen Quellcomputer heruntergefahren, die letzten Daten werden synchronisiert, und dann wird ein Failover ausgelöst.

> [!NOTE]
> Während Sie für virtuelle Hyper-V-Computer ein Failover von einem lokalen Standort zu einem anderen lokalen Standort durchführen, müssen Sie wie folgt vorgehen, um zurück zum primären lokalen Standort zu gelangen: Wählen Sie zuerst die Option **Umgekehrt replizieren**, um die Daten des virtuellen Computers zurück an den primären Standort zu replizieren, und lösen Sie dann ein Failover aus. Wenn der primäre virtuelle Computer nicht verfügbar ist, müssen Sie den virtuellen Computer vor Beginn des Vorgangs **Umgekehrt replizieren** aus einer Sicherung wiederherstellen.   
> 
> 
> ## <a name="failover-job"></a>Failoverauftrag

![Failover](./media/site-recovery-failover/FailoverJob.png)

Die Auslösung eines Failovers ist mit den folgenden Schritten verbunden:

1. Voraussetzungsüberprüfung: Mit diesem Schritt wird sichergestellt, dass alle erforderlichen Bedingungen für das Failover erfüllt sind.
1. Failover: In diesem Schritt werden die Daten verarbeitet und vorbereitet, damit daraus ein virtueller Azure-Computer erstellt werden kann. Wenn Sie den Wiederherstellungspunkt **Latest** (Neueste) gewählt haben, wird in diesem Schritt ein Wiederherstellungspunkt aus den Daten erstellt, die an den Dienst gesendet wurden.
1. Start: In diesem Schritt wird eine Azure-VM aus den Daten erstellt, die im vorherigen Schritt verarbeitet wurden.

> [!WARNING]
> **Laufendes Failover nicht abbrechen**: Bevor das Failover gestartet wird, wird die Replikation für den virtuellen Computer beendet. Wenn Sie für einen laufenden Auftrag die Option **Abbrechen** wählen, wird das Failover beendet, aber die Replikation für den virtuellen Computer wird nicht gestartet. Die Replikation kann nicht neu gestartet werden.
>
>

## <a name="time-taken-for-failover-to-azure"></a>Zeitaufwand für das Failover in Azure

In bestimmten Fällen erfordert das Failover virtueller Computer einen zusätzliche Zwischenschritt, der in der Regel ca. 8 bis 10 Minuten dauert. In den folgenden Fällen dauert die Zeit für den Failover länger als normal:

* VMware-VMs mit Mobility Service-Version niedriger als 9.8
* Physische Server
* VMware-Linux-VMs
* Als physische Server geschützte Hyper-V-VMs
* VMware-VMs, bei denen die folgenden Treiber nicht als Starttreiber vorhanden sind
    * storvsc
    * vmbus
    * storflt
    * intelide
    * atapi
* VMware-VMs, bei denen der DHCP-Dienst nicht aktiviert ist, unabhängig davon, ob DHCP oder statischen IP-Adressen verwendet werden

In allen anderen Fällen ist dieser Zwischenschritt nicht erforderlich und der Zeitaufwand für das Failover niedriger.

## <a name="using-scripts-in-failover"></a>Verwenden von Skripts im Failover
Es kann ratsam sein, bei einem Failover bestimmte Aktionen zu automatisieren. Hierfür können Sie Skripts oder [Azure Automation-Runbooks](site-recovery-runbook-automation.md) in [Wiederherstellungsplänen](site-recovery-create-recovery-plans.md) verwenden.

## <a name="post-failover-considerations"></a>Überlegungen nach dem Failover
Orientieren Sie sich nach einem Failover an den folgenden Empfehlungen:
### <a name="retaining-drive-letter-after-failover"></a>Beibehalten von Laufwerkbuchstaben nach einem Failover
Wenn Sie nach dem Failover den Laufwerkbuchstaben des virtuellen Computers beibehalten möchten, können Sie die **SAN-Richtlinie** für den virtuellen Computer auf **OnlineAll** festlegen. [Weitere Informationen](https://support.microsoft.com/help/3031135/how-to-preserve-the-drive-letter-for-protected-virtual-machines-that-are-failed-over-or-migrated-to-azure).

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Vorbereiten der Verbindungsherstellung mit Azure-VMs nach dem Failover

Wenn Sie nach dem Failover per RDP/SSH eine Verbindung mit Azure-VMs herstellen möchten, müssen Sie die in dieser [Tabelle](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) zusammengefassten Anforderungen erfüllen.

Führen Sie die [hier](site-recovery-failover-to-azure-troubleshoot.md) beschriebenen Schritte aus, um nach dem Failover ggf. Konnektivitätsprobleme zu beheben.


## <a name="next-steps"></a>Nächste Schritte

> [!WARNING]
> Nachdem Sie das Failover für virtuelle Computer durchgeführt haben und das lokale Rechenzentrum verfügbar ist, sollten Sie virtuelle VMware-Computer im lokalen Rechenzentrum [**erneut schützen**](vmware-azure-reprotect.md).

Verwenden Sie die Option [**Geplantes Failover**](hyper-v-azure-failback.md), um für virtuelle Hyper-V-Computer ein **Failback** von Azure zum lokalen Standort durchzuführen.

Wenn Sie für einen virtuellen Hyper-V-Computer ein Failover in ein anderes lokales Datencenter durchgeführt haben, das von einem VMM-Server verwaltet wird, und das primäre Datencenter verfügbar ist, können Sie die Option **Umgekehrt replizieren** verwenden, um die Replikation zurück in das primäre Datencenter zu starten.
