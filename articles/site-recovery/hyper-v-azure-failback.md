---
title: Durchführen eines Failbacks während der Notfallwiederherstellung für virtuelle Hyper-V-Computer aus Azure in eine lokale Umgebung | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie für virtuelle Hyper-V-Computer während der Notfallwiederherstellung in Azure mit dem Azure Site Recovery-Dienst ein Failback zu einem lokalen Standort durchführen.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 4030b1905f8d5b50ef6be3ffa61eda74d8a27951
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60552415"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Durchführen eines Failbacks für virtuelle Hyper-V-Computer

In diesem Artikel wird beschrieben, wie Sie für virtuelle Hyper-V-Computer, die von Site Recovery geschützt werden, ein Failback durchführen.

## <a name="prerequisites"></a>Voraussetzungen
1. Sie sollten vorher unbedingt die Informationen zu den [verschiedenen Typen von Failbacks](concepts-types-of-failback.md) und den entsprechenden Einschränkungen lesen.
1. Stellen Sie sicher, dass der VMM-Server oder Hyper-V-Hostserver am primären Standort mit Azure verbunden ist.
2. Sie sollten auf dem virtuellen Computer einen **Commit** ausgeführt haben.

## <a name="perform-failback"></a>Durchführen von Failbacks
Nach dem Failover vom primären zum sekundären Standort sind die virtuellen Replikatcomputer nicht durch Site Recovery geschützt, und der sekundäre Standort fungiert nun als der aktive Standort. Für ein Failback virtueller Computer in einem Wiederherstellungsplan führen Sie wie nachfolgend beschrieben ein geplantes Failover vom sekundären Standort zum primären Standort aus. 
1. Wählen Sie **Wiederherstellungspläne** > *Name des Wiederherstellungsplans* aus. Klicken Sie auf **Failover** > **Planned Failover**veröffentlichen.
2. Wählen Sie auf der Seite **Geplantes Failover bestätigen** den Quell- und Zielort aus. Beachten Sie die Failover-Richtung. Wenn das Failover vom primären Standort erwartungsgemäß funktioniert hat und sich alle virtuellen Computer am sekundären Standort befinden, dient diese Angabe nur zu Informationszwecken.
3. Wählen Sie bei einem Failback von Azure Einstellungen unter **Datensynchronisierung**aus:
    - **Daten vor dem Failover synchronisieren (nur Deltaänderungen synchronisieren)** : Diese Option minimiert die Ausfallzeiten der virtuellen Computer, da diese für die Synchronisierung nicht heruntergefahren werden. Die folgenden Schritte werden ausgeführt:
        - Phase 1: Es wird eine Momentaufnahme der VM in Azure erstellt und auf den lokalen Hyper-V-Host kopiert. Der Computer wird weiterhin in Azure ausgeführt.
        - Phase 2: Die VM wird in Azure heruntergefahren, damit keine neuen Änderungen vorgenommen werden. Die letzten Deltaänderungen werden an den lokalen Server übertragen, und der lokale virtuelle Computer wird gestartet.

    - **Daten nur während Failover synchronisieren (vollständiger Download)** : Diese Option ist schneller.
        - Diese Option ist schneller, da erwartet wird, dass die meisten Daten auf dem Datenträger geändert wurden, und keine Zeit mit der Prüfsummenberechnung verschwendet werden soll. Mit der Option wird ein Download des Datenträgers durchgeführt. Sie ist auch nützlich, wenn der lokale virtuelle Computer gelöscht wurde.
        - Es wird empfohlen, diese Option zu verwenden, wenn Sie Azure bereits seit einer Weile nutzen (mindestens einen Monat) oder wenn der lokale virtuelle Computer gelöscht wurde. Mit dieser Option werden keine Prüfsummenberechnungen durchgeführt.


4. Wenn die Datenverschlüsselung für die Cloud aktiviert ist, wählen Sie unter **Verschlüsselungsschlüssel** das Zertifikat aus, das beim Aktivieren der Datenverschlüsselung im Rahmen der Anbieterinstallation auf dem VMM-Server ausgestellt wurde.
5. Initiieren Sie das Failover. Der Fortschritt des Failovers wird auf der Registerkarte **Aufträge** angezeigt.
6. Falls Sie die Option ausgewählt haben, mit der die Daten vor dem Failover synchronisiert werden, klicken Sie unter **Aufträge** auf „Auftragsname“ und wählen dann die Option **Failover abschließen**, sobald die erste Datensynchronisierung abgeschlossen ist und Sie zum Herunterfahren der virtuellen Computer in Azure bereit sind. Dadurch wird der Azure-Computer heruntergefahren, die neuesten Änderungen werden an den lokalen virtuellen Computer übertragen, und er wird gestartet.
7. Anschließend können Sie sich am virtuellen Computer anmelden und sich vergewissern, dass er wie erwartet verfügbar ist.
8. Der Status des virtuellen Computers weist darauf hin, dass ein Commit aussteht. Klicken Sie auf **Commit**, um ein Commit für das Failover auszuführen.
9. Klicken Sie anschließend auf **Umgekehrt replizieren**, um das Failback abzuschließen und den Schutz des virtuellen Computers am primären Standort zu starten.


Gehen Sie wie folgt vor, um ein Failback zum ursprünglichen primären Standort durchzuführen. Hier erfahren Sie, wie Sie ein geplantes Failover für einen Wiederherstellungsplan durchführen. Alternativ können Sie das Failover über die Registerkarte **Virtuelle Computer** auch für einen einzelnen virtuellen Computer durchführen.


## <a name="failback-to-an-alternate-location-in-hyper-v-environment"></a>Failback zu einem alternativen Standort in der Hyper-V-Umgebung
Wenn Sie den Schutz zwischen einem [Hyper-V-Standort und Azure](site-recovery-hyper-v-site-to-azure.md) bereitgestellt haben, können Sie ein Failback von Azure zu einem alternativen lokalen Standort durchführen. Dies ist hilfreich, wenn Sie neue lokale Hardware einrichten müssen. Gehen Sie hierzu wie folgt vor:

1. Wenn Sie neue Hardware einrichten möchten, installieren Sie Windows Server 2012 R2 und die Hyper-V-Rolle auf dem Server.
2. Erstellen Sie einen virtuellen Netzwerkswitch mit dem gleichen Namen wie auf dem ursprünglichen Server.
3. Wählen Sie **Geschützte Elemente** -> **Schutzgruppe** -> \<NameDerSchutzgruppe> -> \<Name des virtuellen Computers für das Failback> und anschließend die Option **Geplantes Failover** aus.
4. Erfassen und speichern Sie unter **Geplantes Failover bestätigen** select **veröffentlichen**.
5. Wählen Sie unter *Hostname* den neuen Hyper-V-Hostserver aus, auf dem sich der virtuelle Computer befinden soll.
6. Für die Datensynchronisierung empfiehlt sich die Option zum Synchronisieren der Daten vor dem Failover. Diese Option minimiert die Ausfallzeiten der virtuellen Computer, da diese für die Synchronisierung nicht heruntergefahren werden. Die Option bewirkt Folgendes:

    - Phase 1: Es wird eine Momentaufnahme der VM in Azure erstellt und auf den lokalen Hyper-V-Host kopiert. Der Computer wird weiterhin in Azure ausgeführt.
    - Phase 2: Die VM wird in Azure heruntergefahren, damit keine neuen Änderungen vorgenommen werden. Die letzten Änderungen werden an den lokalen Server übertragen, und der lokale virtuelle Computer wird gestartet.
    
7. Klicken Sie auf das Häkchen, um das Failover (Failback) zu starten.
8. Klicken Sie auf **Aufträge** > \<Geplanter Failoverauftrag> > **Failover abschließen**, wenn die anfängliche Synchronisierung beendet ist und Sie den virtuellen Computer in Azure herunterfahren können. Dadurch wird der Azure-Computer heruntergefahren, die neuesten Änderungen werden an den lokalen virtuellen Computer übertragen, und der lokale virtuelle Computer wird gestartet.
9. Sie können sich am lokalen virtuellen Computer anmelden und sich vergewissern, das alles wie erwartet funktioniert. Klicken Sie anschließend auf **Commit** , um das Failover abzuschließen. Der Commit löscht den virtuellen Azure-Computer und seine Datenträger und bereitet den virtuellen Computer dafür vor, erneut geschützt zu werden.
10. Klicken Sie auf **Umgekehrt replizieren** , um den Schutz des lokalen virtuellen Computers zu starten.

    > [!NOTE]
    > Wenn Sie den Failbackauftrag während der Datensynchronisierung abbrechen, wird der lokale virtuelle Computer als beschädigt angezeigt. Das liegt daran, dass bei der Datensynchronisierung die aktuellen Daten von den Datenträgern virtueller Azure-Computer auf die lokalen Datenträger kopiert werden. Bis zum Abschluss der Synchronisierung befinden sich die Datenträgerdaten unter Umständen nicht in einem konsistenten Zustand. Wenn der lokale virtuelle Computer nach dem Abbruch der Datensynchronisierung gestartet wird, ist das Starten unter Umständen nicht möglich. Lösen Sie zum Abschließen der Datensynchronisierung das Failover erneut aus.


## <a name="why-is-there-no-button-called-failback"></a>Warum gibt es keine Failback-Schaltfläche?
Im Portal ist keine explizite Geste mit der Bezeichnung „Failback“ vorhanden. Failback ist ein Schritt, bei dem Sie an den primären Standort zurückkehren. Definitionsgemäß ist ein Failback ein Failover von virtuellen Computern aus der Wiederherstellung zum primären Standort.

Beim Initiieren eines Failovers informiert Sie das Blatt über die Richtung, in der die virtuellen Computer verschoben werden. Wenn die Richtung von Azure zu einem lokalen Standort ist, findet ein Failback statt.

## <a name="why-is-there-only-a-planned-failover-gesture-to-failback"></a>Warum ist nur eine Geste für ein geplantes Failover zum Ausführen eines Failbacks vorhanden?
Azure ist eine hoch verfügbare Umgebung, und Ihre virtuellen Computer sind immer verfügbar. Ein Failback ist eine geplante Aktivität, bei der eine geringfügige Ausfallzeit auftritt, damit die Workloads wieder lokal ausgeführt werden können. Dabei wird kein Datenverlust erwartet. Daher ist nur eine Geste für ein geplantes Failover verfügbar, mit der die virtuellen Computer in Azure deaktiviert und die aktuellen Änderungen heruntergeladen werden und sichergestellt wird, dass kein Datenverlust auftritt.

## <a name="do-i-need-a-process-server-in-azure-to-failback-to-hyper-v"></a>Benötige ich für ein Failback auf Hyper-V einen Prozessserver in Azure?
Nein, ein Prozessserver ist nur erforderlich, wenn virtuelle VMware-Computer geschützt werden. Es müssen keine zusätzlichen Komponenten für den Schutz bzw. das Failback von virtuellen Hyper-V-Computern bereitgestellt werden.


## <a name="time-taken-to-failback"></a>Failbackdauer
Die zum Abschließen der Datensynchronisierung und zum Starten des virtuellen Computers erforderliche Zeit, hängt von verschiedenen Faktoren ab. Sie erhalten eine Erläuterung zum Ablauf während der Datensynchronisierung, um einen Einblick hinsichtlich der Dauer zu erhalten.

Die Datensynchronisierung erstellt eine Momentaufnahme der Datenträger des virtuellen Computers und beginnt mit der blockweisen Überprüfung und berechnet dann die Prüfsumme. Diese berechnete Prüfsumme wird an einen lokalen Standort gesendet, um sie mit der lokalen Prüfsumme desselben Blocks zu vergleichen. Wenn die Prüfsummen übereinstimmen, wird der Datenblock nicht übertragen. Wenn sie nicht übereinstimmen, wird der Datenblock an den lokalen Standort übertragen. Diese Übertragungszeit hängt von der verfügbaren Bandbreite ab. Die Geschwindigkeit bei der Prüfsummenberechnung beträgt einige GB pro Minute. 

Um das Herunterladen von Daten zu beschleunigen, können Sie Ihren MARS-Agent konfigurieren, um mehr Threads zu verwenden und den Download zu parallelisieren. Informationen zum Ändern der Downloadthreads im Agent finden Sie in [diesem Dokument](https://support.microsoft.com/en-us/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage).


## <a name="next-steps"></a>Nächste Schritte

Nach dem **Commit** können Sie das *umgekehrte Replizieren* initiieren. Dadurch wird der erneute Schutz des virtuellen Computers von einem lokalen Standort nach Azure gestartet. Dadurch werden nur die Änderungen repliziert, da der virtuelle Computer in Azure deaktiviert wurde und daher nur differenzielle Änderungen sendet.
