---
title: HANA-Failover zu einem Standort für die Notfallwiederherstellung für SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Ausführen von Failover zu einem Standort für die Notfallwiederherstellung für SAP HANA in Azure (große Instanzen)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1daba0819e0164602ae78ece87421834f36fb09a
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967821"
---
# <a name="disaster-recovery-failover-procedure"></a>Failoverprozedur für die Notfallwiederherstellung


>[!IMPORTANT]
>Dieser Artikel ist kein Ersatz für die Verwaltungsdokumentation zu SAP HANA oder für SAP Notes. Es wird davon ausgegangen, dass Sie über solide Kenntnisse in Bezug auf die SAP HANA-Verwaltung und den Betrieb verfügen, insbesondere in den Bereichen Sicherung, Wiederherstellung, Hochverfügbarkeit und Notfallwiederherstellung (DR). In diesem Artikel werden Screenshots von SAP HANA Studio gezeigt. Inhalt, Struktur und grundlegender Aufbau der Bildschirme in den SAP-Verwaltungstools sowie auch die Tools selbst können sich von Release zu Release von SAP HANA ändern.

Beim Failover auf einen Standort für die Notfallwiederherstellung müssen zwei Fälle berücksichtigt werden:

- Die SAP HANA-Datenbank muss zum aktuellen Status der Daten zurückgeführt werden. Für diesen Fall steht ein Self-Service-Skript zur Verfügung, mit dem Sie das Failover ausführen können, ohne sich an Microsoft zu wenden. Für das Failback müssen Sie jedoch mit Microsoft zusammenarbeiten.
- Sie möchten eine Speichermomentaufnahme wiederherstellen, bei der es sich nicht um die letzte replizierte Momentaufnahme handelt. In diesem Fall müssen Sie mit Microsoft zusammenarbeiten. 

>[!NOTE]
>Die folgenden Schritte müssen auf der HANA-Einheit (große Instanzen) ausgeführt werden, die der Notfallwiederherstellungseinheit entspricht. 
 
Um die letzte replizierte Speichermomentaufnahme wiederherzustellen, befolgen Sie die Schritte unter „Perform full DR failover – azure_hana_dr_failover“ (Ausführen eines vollständigen Failovers für die Notfallwiederherstellung – azure_hana_dr_failover) in [Microsoft snapshot tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf) (Microsoft-Momentaufnahmetools für SAP HANA in Azure). 

Wenn Sie für mehrere SAP HANA-Instanzen ein Failover ausführen möchten, führen Sie den Befehl azure_hana_dr_failover mehrfach aus. Geben Sie bei entsprechender Aufforderung die SAP HANA-SID ein, für die Sie das Failover und die Wiederherstellung ausführen möchten. 


Sie können das Notfallwiederherstellungs-Failover auch ohne Auswirkungen auf die tatsächliche Replikationsbeziehung testen. Um ein Testfailover auszuführen, befolgen Sie die Schritte unter „Perform a test DR failover – azure_hana_test_dr_failover“ (Ausführen eines Testfailovers für die Notfallwiederherstellung – azure_hana_test_dr_failover) in [Microsoft snapshot tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf) (Microsoft-Momentaufnahmetools für SAP HANA in Azure). 

>[!IMPORTANT]
>Führen Sie auf der Instanz, die Sie am Standort der Notfallwiederherstellung beim **Testen eines Failovers** erstellt haben, *keine* Produktionstransaktionen durch. Der Befehl azure_hana_test_dr_failover erstellt einen Satz von Volumes, die keine Beziehung zum primären Standort haben. Eine Synchronisierung zurück an den primären Standort ist daher *nicht* möglich. 

Wenn Sie Tests für mehrere SAP HANA-Instanzen durchführen möchten, führen Sie das Skript mehrfach aus. Geben Sie bei entsprechender Aufforderung die SAP HANA-SID der Instanz ein, die Sie für das Failover testen möchten. 

>[!NOTE]
>Wenn Sie ein Failover am Standort der Notfallwiederherstellung ausführen müssen, um einige vor mehreren Stunden gelöschte Daten zu retten, und die Notfallwiederherstellungsvolumes auf eine frühere Momentaufnahme festgelegt werden müssen, wenden Sie dieses Verfahren an. 

1. Fahren Sie die nicht für die Produktion bestimmte Instanz von HANA auf der ausgeführten Einheit für die Notfallwiederherstellung von HANA (große Instanzen) herunter. Eine ruhende HANA-Produktionsinstanz ist vorinstalliert.
1. Stellen Sie sicher, dass keine SAP HANA-Prozesse ausgeführt werden. Verwenden Sie für diese Überprüfung den folgenden Befehl:

      `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`.

      In der Ausgabe muss der Prozess **hdbdaemon** den Zustand „Beendet“ aufweisen, und es dürfen keine anderen HANA-Prozesse mit dem Zustand „Gestartet“ vorhanden sein.
1. Überprüfen Sie, unter welchen Momentaufnahmenamen bzw. welcher SAP HANA-Sicherungs-ID der Standort für die Notfallwiederherstellung wiederhergestellt werden soll. In der Praxis handelt es sich dabei in der Regel um die neueste Momentaufnahme. Wenn Sie verlorene Daten wiederherstellen müssen, wählen Sie eine ältere Momentaufnahme.
1. Wenden Sie sich mit einer Supportanfrage mit hoher Priorität an den Azure-Support. Bitten Sie um die Wiederherstellung dieser Momentaufnahme (mit dem entsprechenden Namen und Datum) oder der HANA-Sicherungs-ID am Standort der Notfallwiederherstellung. In der Standardeinstellung wird auf der Betriebsseite nur das Volume „/hana/data“ wiederhergestellt. Wenn auch die Volumes für „/hana/logbackups“ wiederhergestellt werden sollen, müssen Sie dies ausdrücklich angeben. *Stellen Sie das Volume „/hana/shared“ nicht wieder her.* Wählen Sie stattdessen bestimmte Dateien wie etwa „global.ini“ aus dem Verzeichnis **.snapshot** und den zugehörigen Unterverzeichnissen aus, nachdem Sie das Volume „/hana/shared“ für das PRD-Volume erneut eingebunden haben. 

   Auf der Betriebsseite werden die folgenden Schritte ausgeführt:

   a. Die Replikation von Momentaufnahmen aus dem Produktionsvolume auf den Notfallwiederherstellungsvolumes wird beendet. Diese Unterbrechung könnte bereits stattgefunden haben, wenn ein Ausfall am Produktionsstandort der Grund dafür ist, dass Sie das Notfallwiederherstellungsverfahren durchführen müssen.
   
   b. Der Name der Speichermomentaufnahme oder die Momentaufnahme mit der Sicherungs-ID, den bzw. die Sie auswählen, wird auf den Notfallwiederherstellungsvolumes wiederhergestellt.
   
   c. Nach der Wiederherstellung können die Notfallwiederherstellungsvolumes in die Einheiten von HANA (große Instanzen) in der Notfallwiederherstellungsregion eingebunden werden.
      
1. Binden Sie die Notfallwiederherstellungsvolumes in die Einheit von HANA (große Instanzen) am Standort für die Notfallwiederherstellung ein. 
1. Starten Sie die ruhende SAP HANA-Produktionsinstanz.
1. Wenn Sie zur Verringerung der RPO-Zeit Protokolle der Transaktionsprotokollsicherung kopiert haben, führen Sie diese Transaktionsprotokollsicherungen mit dem neu eingebundenen Notfallwiederherstellungsverzeichnis „hana/logbackups“ zusammen. Überschreiben Sie keine vorhandenen Sicherungen. Kopieren Sie neuere Sicherungen, die nicht mit der neuesten Replikation einer Speichermomentaufnahme repliziert wurden.
1. Sie können auch einzelne Dateien über die Momentaufnahmen wiederherstellen, die nicht auf dem Volume „/hana/shared/PRD“ in der Azure-Region für die Notfallwiederherstellung repliziert wurden.

Die folgenden Schritte zeigen die Wiederherstellung der SAP HANA-Produktionsinstanz auf der Grundlage der wiederhergestellten Speichermomentaufnahme und der verfügbaren Transaktionsprotokollsicherungen.

1. Ändern Sie mithilfe von SAP HANA Studio das Sicherungsverzeichnis in **/hana/logbackups**.

   ![Ändern des Sicherungsspeicherorts für die Notfallwiederherstellung](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA überprüft alle Sicherungsdateispeicherorte und schlägt für die Wiederherstellung die neueste Transaktionsprotokollsicherung vor. Die Überprüfung kann einige Minuten dauern. Danach wird ein Bildschirm wie der folgende angezeigt:

   ![Liste mit Transaktionsprotokollsicherungen für die Notfallwiederherstellung](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Passen Sie einige Standardeinstellungen an:

      - Deaktivieren Sie **Deltasicherungen verwenden**.
      - Wählen Sie **Protokollbereich initialisieren**.

   ![Aktivieren der Option „Protokollbereich initialisieren“](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Wählen Sie **Fertig stellen** aus.

   ![Fertigstellen der Notfallwiederherstellung](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Daraufhin sollte ein Statusfenster wie das folgende erscheinen. Beachten Sie, dass es sich bei diesem Beispiel um eine Notfallwiederherstellung einer SAP HANA-Konfiguration mit drei Knoten für horizontales Hochskalieren handelt.

![Status der Wiederherstellung](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Falls die Wiederherstellung auf dem Bildschirm **Fertig stellen** nicht mehr reagiert und der Statusbildschirm nicht angezeigt wird, überprüfen Sie, ob alle SAP HANA-Instanzen auf den Workerknoten ausgeführt werden. Starten Sie die SAP HANA-Instanzen bei Bedarf manuell.


## <a name="failback-from-a-dr-to-a-production-site"></a>Failback von einem Standort für die Notfallwiederherstellung auf den Produktionsstandort
Sie können ein Failback von einem Notfallwiederherstellungsstandort auf einen Produktionsstandort durchführen. Wir sehen uns nun ein Szenario an, bei dem das Failover auf den Standort für die Notfallwiederherstellung auf Probleme in der Azure-Produktionsregion zurückzuführen ist (und nicht darauf, dass Sie verloren gegangene Daten wiederherstellen müssen). 

Sie haben Ihre SAP-Produktionsworkload für einen gewissen Zeitraum am Standort für die Notfallwiederherstellung ausgeführt. Nachdem die Probleme am Produktionsstandort behoben wurden, sollten Sie ein Failback auf Ihren Produktionsstandort durchführen. Da hierbei keine Daten verloren gehen dürfen, umfasst der Wechsel zum Produktionsstandort mehrere Schritte und eine enge Zusammenarbeit mit dem Betriebsteam für SAP HANA in Azure. Nach der Behebung der Probleme müssen Sie sich mit dem Betriebsteam in Verbindung setzen, um die Rücksynchronisierung mit dem Produktionsstandort anhand eines Triggers zu initiieren.

Folgen Sie diesen Schritten:

1. Das Betriebsteam für SAP HANA in Azure ruft den Trigger zum Synchronisieren der Produktionsspeichervolumes aus den Speichervolumes für die Notfallwiederherstellung ab, die jetzt den Produktionszustand darstellen. In diesem Zustand wird die Einheit von HANA (große Instanzen) am Produktionsstandort heruntergefahren.
1. Das Betriebsteam für SAP HANA in Azure überwacht die Replikation und stellt sicher, dass die Daten auf dem neuesten Stand sind, bevor es Sie informiert.
1. Sie fahren die Anwendungen herunter, die die HANA-Produktionsinstanz am Standort für die Notfallwiederherstellung verwenden. Anschließend führen Sie eine HANA-Transaktionsprotokollsicherung durch. Beenden Sie als Nächstes die HANA-Instanz, die am Standort für die Notfallwiederherstellung in Einheiten von HANA (große Instanzen) ausgeführt wird.
1. Nach dem Herunterfahren der HANA-Instanz, die am Standort für die Notfallwiederherstellung in der Einheit von HANA (große Instanzen) ausgeführt wurde, synchronisiert das Betriebsteam erneut manuell die Datenträgervolumes.
1. Das Betriebsteam für SAP HANA in Azure startet die Einheit von HANA (große Instanzen) am Produktionsstandort wieder. Dann übergibt es sie an Sie. Sie vergewissern sich, dass sich die SAP HANA-Instanz beim Start der Einheit von HANA (große Instanzen) im Zustand „Heruntergefahren“ befindet.
1. Sie führen die gleichen Datenbankwiederherstellungsschritte durch wie zuvor beim Failover auf den Standort für die Notfallwiederherstellung.

## <a name="monitor-disaster-recovery-replication"></a>Überwachen der Replikation für die Notfallwiederherstellung

Um den Status der Speicherreplikation zu überwachen, führen Sie das Skript `azure_hana_replication_status` aus. Dieser Befehl muss über eine Einheit ausgeführt werden, die am Standort für die Notfallwiederherstellung ausgeführt wird, damit es erwartungsgemäß funktioniert. Der Befehl funktioniert unabhängig davon, ob die Replikation aktiv ist. Der Befehl kann für jede HANA-Einheit (große Instanzen) Ihres Mandanten am Standort für die Notfallwiederherstellung ausgeführt werden. Er kann nicht zum Abrufen von Details über das Startvolume verwendet werden. 

Weitere Informationen zu dem Befehl und seiner Ausgabe finden Sie unter „Get DR replication status – azure_hana_replication_status“ (Abrufen des Status der Notfallwiederherstellungsreplikation – azure_hana_replication_status) in [Microsoft snapshot tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf) (Microsoft-Momentaufnahmetools für SAP HANA in Azure).


## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen finden Sie unter [Überwachung und Problembehandlung auf HANA-Seite](hana-monitor-troubleshoot.md).
