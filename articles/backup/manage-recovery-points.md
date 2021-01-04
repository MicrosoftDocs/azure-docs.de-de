---
title: Verwalten von Wiederherstellungspunkten
description: Hier erfahren Sie, wie der Azure Backup-Dienst Wiederherstellungspunkte für virtuelle Computer verwaltet.
ms.topic: conceptual
ms.date: 11/08/2020
ms.openlocfilehash: 256df693aba0f799c24bcba6defe846e5c37ccaa
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428488"
---
# <a name="manage-recovery-points"></a>Verwalten von Wiederherstellungspunkten

In diesem Artikel wird beschrieben, wie die Datenaufbewahrung für virtuelle Computer funktioniert. Beim Durchführen von Sicherungen werden Wiederherstellungspunkte erstellt, die als Ausgangspunkte für Wiederherstellungsvorgänge dienen.

Bei virtuellen Computern ist die erste Sicherung eine vollständige Sicherung, und die nachfolgenden Sicherungen sind inkrementelle Sicherungen.

## <a name="recovery-points-and-retention"></a>Wiederherstellungspunkte und Datenaufbewahrung

### <a name="scheduled-initial-and-incremental-backup"></a>Geplante erste und inkrementelle Sicherung

Betrachten wir ein vereinfachtes Beispiel des virtuellen Computers *V1* mit einem aus vier Blöcken bestehenden Datenträger: Block 1, Block 2, Block 3 und Block 4. Jeder Block hat eine Größe von 16 KB.

![Virtueller Computer mit vier Blöcken](./media/manage-recovery-points/four-blocks.png)

**Schritt 1 – Erste Sicherung:** Bei der ersten Sicherung handelt es sich um eine vollständige Sicherung. Auf dieser Grundlage werden nachfolgende inkrementelle Sicherungen durchgeführt. Angenommen, auf dem virtuellen Quellcomputer werden Daten in Block 1 und Block 2 geschrieben. Die gleichen Daten werden im Speicher des Recovery Services-Tresors als D1 und D2 repliziert.

![Erste Sicherung wird repliziert](./media/manage-recovery-points/initial-backup.png)

**Schritt 2 – Inkrementelle Sicherung 1:** Beachten Sie, dass neue Daten in Block 3 der VM hinzugefügt wurden. Die gleichen Daten werden bei der nächsten inkrementellen Sicherung repliziert, und nur der geänderte Block wird als D3 gespeichert.  Bei jedem Schritt wird der gesamte 16-KB-Block in den Wiederherstellungspunkt hochgeladen, selbst wenn nur 1 KB davon geändert wurde.

![Erste inkrementelle Sicherung](./media/manage-recovery-points/first-incremental-backup.png)

**Schritt 3 – Inkrementelle Sicherung 2:**  Nun wird angenommen, dass auf dem virtuellen Quellcomputer Daten in Block 3 und Block 2 geändert werden. Diese Änderungen werden bei der nächsten inkrementellen Sicherung als D3' und D2' repliziert.

![Zweite inkrementelle Sicherung](./media/manage-recovery-points/second-incremental-backup.png)

### <a name="on-demand-backup"></a>Bedarfsgesteuerte Sicherung

Sie können jederzeit eine bedarfsgesteuerte Sicherung eines virtuellen Computers durchführen, nachdem Sie den Schutz für diesen konfiguriert haben.

- Bei der bedarfsgesteuerten Sicherung handelt es sich um eine vollständige Sicherung, wenn sie vor der geplanten ersten Sicherung ausgelöst wird.
- Wird eine bedarfsgesteuerte Sicherung ausgelöst, wenn die erste Sicherung bereits abgeschlossen ist, handelt es sich dabei um eine inkrementelle Sicherung.
- Die Aufbewahrungsdauer für die bei einer bedarfsgesteuerten Sicherung erstellten Wiederherstellungspunkte ist der Aufbewahrungswert, den Sie beim Auslösen der Sicherung festlegen.

### <a name="storage-cost"></a>Speicherkosten

Der für die erste Sicherung erstellte **Wiederherstellungspunkt** enthält alle Blöcke mit Daten. Die Wiederherstellungspunkte der nachfolgenden inkrementellen Sicherungen enthalten nur die Blöcke mit geänderten Daten. Die Speicherkosten entsprechen der Summe aller Blöcke in allen Wiederherstellungspunkten.

Anhand des obigen Beispiels können wir die Speicherkosten nach jedem Schritt nachvollziehen:

|Schritt  |Sicherungstyp  |Geänderte Blöcke  |Speichertyp |
|------|---------|---------|---------|
|1     |     Erste Sicherung    | Block 1, Block 2        |    Entspricht Wiederherstellungspunkt 1 (D1 + D2)     |
|2     |  Inkrementelle Sicherung 1       |  Block 3       |   Entspricht Wiederherstellungspunkt 1 (D1 + D2) + Wiederherstellungspunkt 2 (D3)      |
|3     |    Inkrementelle Sicherung 2     |    Block 2, Block 3     |   Entspricht Wiederherstellungspunkt 1 (D1 + D2) + Wiederherstellungspunkt 2 (D3) + Wiederherstellungspunkt 3 (D2' + D3')      |

### <a name="recovery-point-expiration"></a>Ablauf des Wiederherstellungspunkts

Für jeden Wiederherstellungspunkt gilt eine in der Sicherungsrichtlinie festgelegte Aufbewahrungsdauer. Bei der regelmäßigen Bereinigung werden alle Wiederherstellungspunkte bereinigt, die abgelaufen sind.

Wenn der Wiederherstellungspunkt abläuft, wird er entweder gelöscht oder zusammengeführt.

### <a name="case-1-initial-recovery-point-expires"></a>Fall 1: Ablauf des ersten Wiederherstellungspunkts

Wenn der erste Wiederherstellungspunkt abläuft, wird er mit dem nächsten inkrementellen Wiederherstellungspunkt zusammengeführt. Alle Datenblöcke, die im inkrementellen Wiederherstellungspunkt überschrieben wurden, werden gelöscht. Der Rest wird zusammengeführt. Die inkrementelle Sicherung wird dann zur ersten vollständigen Sicherung. Im folgenden Beispiel wird dies veranschaulicht:

- Der bei der ersten Sicherung erstellte *Wiederherstellungspunkt 1* beinhaltet die vollständige Sicherung der VM.
- Wenn der *Wiederherstellungspunkt 1* abläuft, wird der *Wiederherstellungspunkt 2* zur nächsten vollständigen Sicherung.
- Block D1 wird mit dem *Wiederherstellungspunkt 2* zusammengeführt, und D2 wird gelöscht, da die Daten in Block 2 im *Wiederherstellungspunkt 2* überschrieben wurden. Diese Änderung wird als Block D2' erfasst.
- Block D1 wird in den nachfolgenden Wiederherstellungspunkten unverändert aufbewahrt, bis vor der nächsten Sicherung Änderungen daran vorgenommen werden.

![Erster Fall](./media/manage-recovery-points/first-case.png)

### <a name="case-2-in-between-incremental-recovery-point-expires"></a>Fall 2: Ablauf des zweiten Wiederherstellungspunkts

- Wenn der *Wiederherstellungspunkt 2* vor dem *Wiederherstellungspunkt 1* abläuft, werden die Daten aus dem *Wiederherstellungspunkt 2* mit dem nächsten verfügbaren Wiederherstellungspunkt zusammengeführt: *Wiederherstellungspunkt 3*. Block D3 wird also mit dem *Wiederherstellungspunkt 3* zusammengeführt.
- Der *Wiederherstellungspunkt 1* ist nach wie vor die vollständige Sicherung mit Block D1 und D2.

![Zweiter Fall](./media/manage-recovery-points/second-case.png)

### <a name="case-3-on-demand-recovery-point-expires"></a>Fall 3: Ablauf des bedarfsgesteuerten Wiederherstellungspunkts

In diesem Beispiel wird in der Richtlinie zu geplanten (täglichen) Sicherungen ein Aufbewahrungszeitraum von *n* Tagen festgelegt.  Wenn eine bedarfsgesteuerte Sicherung am vierten Tag vor der nächsten geplanten Sicherung ausgelöst wird und als Aufbewahrungszeitraum 10 Tage angegeben wurden, handelt es sich dabei immer noch um eine inkrementelle Sicherung. Es wird ein Wiederherstellungspunkt (*Bedarfsgesteuerte Sicherung 1*) nach dem *Wiederherstellungspunkt 3* und vor dem *Wiederherstellungspunkt 4* erstellt.  Am Ende von Tag 14 läuft der Wiederherstellungspunkt der bedarfsgesteuerten Sicherung (*Bedarfsgesteuerte Sicherung 1*) ab und wird mit dem nächsten verfügbaren Wiederherstellungspunkt zusammengeführt. Die Datenblöcke, die sich noch auf dem Server befinden, werden zusammengeführt. Die Datenblöcke, die sich geändert haben (also überschrieben oder gelöscht wurden), werden dagegen von dem abgelaufenen Wiederherstellungspunkt gelöscht.

![Dritter Fall](./media/manage-recovery-points/third-case.png)

### <a name="impact-of-policy-change-on-recovery-points"></a>Auswirkung von Richtlinienänderungen auf Wiederherstellungspunkte

Eine geänderte Richtlinie wird sowohl auf neue als auch auf vorhandene Wiederherstellungspunkte angewendet. Weitere Informationen finden Sie unter [Auswirkung von Richtlinienänderungen auf Wiederherstellungspunkte](backup-architecture.md#impact-of-policy-change-on-recovery-points).

### <a name="impact-of-stop-protection-on-recovery-points"></a>Auswirkung der Beendigung des Schutzes auf Wiederherstellungspunkte

Sie haben zwei Möglichkeiten, den Schutz einer virtuellen Maschine zu beenden:

- **Schutz beenden und Sicherungsdaten löschen:** Mit dieser Option werden alle zukünftigen Sicherungsaufträge zum Schutz Ihres virtuellen Computers beendet und alle Wiederherstellungspunkte gelöscht. Wenn [vorläufiges Löschen](backup-azure-security-feature-cloud.md) aktiviert ist, werden die gelöschten Daten 14 Tage lang aufbewahrt. Für vorläufig gelöschte Elemente fallen keine Gebühren an. Die Daten können innerhalb des Zeitraums von 14 Tagen wiederhergestellt werden. Wenn „vorläufiges Löschen“ nicht aktiviert ist, werden die Daten sofort gelöscht, und Sie können weder die VM wiederherstellen noch die Option **Sicherung fortsetzen** verwenden.
- **Schutz beenden und Sicherungsdaten beibehalten:** Mit dieser Option wird der Schutz Ihres virtuellen Computers durch alle zukünftigen Sicherungsaufträge beendet. Der Azure Backup-Dienst behält die bereits gesicherten Wiederherstellungspunkte jedoch dauerhaft bei. Die Aufbewahrung der Wiederherstellungspunkte im Tresor wird Ihnen in Rechnung gestellt (Einzelheiten finden Sie unter [Azure Backup – Preise](https://azure.microsoft.com/pricing/details/backup/)). Sie können den virtuellen Computer bei Bedarf wiederherstellen. Mit der Option **Sicherung fortsetzen** können Sie bei Bedarf den VM-Schutz fortsetzen. Wenn die Sicherung fortgesetzt wird, werden die Aufbewahrungsregeln auf die Wiederherstellungspunkte angewendet. Mithilfe der Option **Sicherungsdaten löschen** können Sie die gesicherten Daten auch löschen.

## <a name="impact-of-deleting-a-vm-without-stop-protection"></a>Auswirkung des Löschens eines virtuellen Computers ohne Beenden des Schutzes

Das Löschen einer VM ohne Beenden des Schutzes wirkt sich auf die Wiederherstellungspunkte aus und sollte vermieden werden. Idealerweise sollten Sicherungen beendet werden, bevor der virtuelle Computer gelöscht wird. Da die Ressource nicht vorhanden ist, tritt bei den geplanten Sicherungen der Fehler [VMNotFoundV2](backup-azure-vms-troubleshoot.md#320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found) (VM nicht gefunden) auf. Die Wiederherstellungspunkte werden regelmäßig entsprechend der Aufbewahrungsrichtlinie bereinigt. Die letzte Kopie des virtuellen Computers wird jedoch dauerhaft beibehalten, wofür entsprechende Gebühren anfallen. Abhängig von Ihrem Szenario verfügen Sie über die folgenden zwei Optionen:

- **Option 1:** Stellen Sie die VM mithilfe eines beliebigen Wiederherstellungspunkts wieder her. Wenn Sie den gelöschten virtuellen Computer wiederherstellen möchten, verwenden Sie dabei denselben Namen und dieselbe Ressourcengruppe. Wenn Sie die wiederhergestellte VM im selben Tresor schützen, werden die vorhandenen Wiederherstellungspunkte automatisch angefügt.
- **Option 2:** Wechseln Sie zum Recovery Services-Tresor, und beenden Sie den Schutz mit Löschen der Daten.

### <a name="impact-of-expired-recovery-points-for-items-in-soft-deleted-state"></a>Auswirkung abgelaufener Wiederherstellungspunkte auf vorläufig gelöschte Elemente

Wenn [vorläufiges Löschen](backup-azure-security-feature-cloud.md) für den Recovery Services-Tresor aktiviert ist, verbleibt der abgelaufene Wiederherstellungspunkt im Zustand „vorläufig gelöscht“ und wird nicht gelöscht. Für einen vorläufig gelöschten Wiederherstellungspunkt fallen keine Gebühren an.

### <a name="impact-of-churn-on-backup-performance"></a>Auswirkung von Änderungen auf die Sicherungsleistung

Angenommen, eine VM verfügt über einen Gesamtspeicher von 8 TB, und die Änderungsrate liegt bei 5 %. Für die inkrementelle Sicherung wird somit entsprechend 5 % von 8 TB Speicherplatz benötigt, also 0,4 TB. Bei höheren Änderungsraten wird für nachfolgende inkrementelle Sicherungen entsprechend mehr Speicherplatz belegt. Die Änderungsrate wirkt sich also auf die Sicherungsleistung aus. Je höher die Änderungsrate, desto langsamer wird der Sicherungsprozess und desto mehr Back-End-Speicher wird verbraucht.

Anhand des folgenden Szenarios wird veranschaulicht, wie sich die Änderungsrate auf die Sicherungsleistung auswirkt:

|Virtuelle Computer  |VM1  |VM2  |VM3  |
|---------|---------|---------|---------|
|Anzahl von Datenträgern für Daten    | 4 (A1, A2, A3, A4)        | 4 (B1, B2, B3, B4)        |  4 (C1, C2, C3, C4)       |
|Größe der einzelnen Datenträger   |      4 TB   | 4 TB        |  4 TB       |
|Änderungsrate der Sicherungsdaten    |   A1: 4 TB      | B1: 1 TB; B2: 1 TB <br> B3: 1 TB; B4: 1 TB  |   C1: 2 TB; C4: 2 TB      |

Die Reihenfolge der Sicherungsleistung ist VM2 > VM3 > VM1. Dies liegt daran, dass die geänderten Daten auf die verschiedenen Datenträger verteilt sind. Da die Sicherung von Datenträgern parallel erfolgt, weist VM2 die beste Leistung auf.

## <a name="next-steps"></a>Nächste Schritte

- [Azure Backup-Architektur und -Komponenten](backup-architecture.md)
