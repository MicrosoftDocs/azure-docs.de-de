---
title: Planen der Kapazität für die Hyper-V-Notfallwiederherstellung mit Azure Site Recovery | Microsoft-Dokumentation
description: Verwenden Sie diesen Artikel, um die Kapazität zu schätzen, wenn Sie die Notfallwiederherstellung mit dem Azure Site Recovery-Dienst einrichten.
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: eeadfd6a57ff8a26f3f124e2a807fcd66e77b85f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61036750"
---
# <a name="plan-capacity-for-hyper-v-vm-disaster-recovery"></a>Planen der Kapazität für die Notfallwiederherstellung von Hyper-V-VMs 

Eine neue, erweiterte Version des [Azure Site Recovery-Bereitstellungsplaners für Hyper-V zu Azure](site-recovery-hyper-v-deployment-planner.md) ist jetzt verfügbar. Sie ersetzt das alte Tool. Verwenden Sie das neue Tool für Ihre Bereitstellungsplanung.
Das Tool verfügt über Richtlinien für Folgendes:

* Bewertung der Eignung von virtuellen Computern basierend auf Datenträgeranzahl, Datenträgergröße, IOPS, Änderungsrate und einigen VM-Merkmalen
* Vergleich von erforderlicher Netzwerkbandbreite und RPO-Bewertung
* Anforderungen an die Azure-Infrastruktur
* Anforderungen an die lokale Infrastruktur
* Anleitung zur Batchverarbeitung für die erste Replikation
* Geschätzte Gesamtkosten für die Notfallwiederherstellung in Azure


Mit dem Tool Azure Site Recovery Capacity Planner können Sie ermitteln, welche Kapazitätsanforderungen für die Replikation von Hyper-V-VMs mit Azure Site Recovery gelten.

Verwenden Sie Site Recovery Capacity Planner zum Analysieren Ihrer Quellumgebung und der Workloads. Das Tool hilft beim Schätzen des Bedarfs an Bandbreite, der Serverressourcen für den Quellstandort und der Ressourcen (z.B. VMs und Speicher), die Sie am Zielspeicherort benötigen.

Sie können das Tool in zwei Modi ausführen:

* **Schnelle Planung:** Das Tool stellt Netzwerk- und Serverhochrechnungen basierend auf der durchschnittlichen Anzahl/Größe von VMs, Datenträgern, Speicher und der Änderungsrate bereit.
* **Detaillierte Planung:** Das Tool stellt Details der einzelnen Workloads auf VM-Ebene bereit. Analysieren Sie die VM-Kompatibilität, und rufen Sie Netzwerk- und Serverhochrechnungen ab.

## <a name="before-you-start"></a>Vorbereitung

* Sammeln Sie Informationen zu Ihrer Umgebung, z. B. VMs, Festplatten pro VM, Speicher pro Datenträger.
* Ermitteln Sie Ihre tägliche Änderungsrate für replizierte Daten. Laden Sie das [Tool für die Hyper-V-Kapazitätsplanung](https://www.microsoft.com/download/details.aspx?id=39057) herunter, um die Änderungsrate zu erhalten. [Hier](site-recovery-capacity-planning-for-hyper-v-replication.md) finden Sie weitere Informationen zu diesem Tool. Es wird empfohlen, das Tool eine Woche lang auszuführen, um Durchschnittswerte zu erfassen.


## <a name="run-the-quick-planner"></a>Ausführen des Quick Planner-Tools
1. Laden Sie das Tool [Site Recovery Capacity Planner](https://aka.ms/asr-capacity-planner-excel) herunter, und öffnen Sie es. Sie müssen Makros ausführen. Entscheiden Sie, wenn Sie dazu aufgefordert werden, ob Sie das Bearbeiten und die Inhalte aktivieren möchten.

2. Wählen Sie im Listenfeld **Select a planner type** (Planertyp auswählen) die Option **Quick Planner** (Schnellplaner) aus.

   ![Erste Schritte](./media/site-recovery-capacity-planner/getting-started.png)

3. Geben Sie im Arbeitsblatt **Capacity Planner** die erforderlichen Informationen ein. Sie müssen alle Felder ausfüllen, die im folgenden Screenshot rot eingekreist sind:

   a. Wählen Sie unter **Select your scenario** (Ihr Szenario auswählen) die Option **Hyper-V to Azure** (Hyper-V zu Azure) oder **VMware/Physical to Azure** (VMware/Physisch zu Azure) aus.

   b. Geben Sie unter **Average daily data change rate (%)** (Durchschnittliche tägliche Datenänderungsrate [%]) die Informationen ein, die Sie mit dem [Tool für die Hyper-V-Kapazitätsplanung](site-recovery-capacity-planning-for-hyper-v-replication.md) oder mit [Azure Site Recovery Deployment Planner](./site-recovery-deployment-planner.md) erfasst haben.

   c. Die Einstellung **Komprimierung** wird bei der Replikation von Hyper-V-VMs in Azure nicht verwendet. Verwenden Sie für die Komprimierung die Anwendung eines Drittanbieters, z.B. Riverbed.

   d. Geben Sie unter **Aufbewahrung (Tage)** an, wie viele Tage Replikate beibehalten werden sollen.

   e. Geben Sie unter **Number of hours in which initial replication for the batch of virtual machines should complete** (Anzahl der Stunden für den Abschluss der anfänglichen Replikation für den Batch virtueller Computer) und **Number of virtual machines per initial replication batch** (Anzahl virtueller Computer pro anfänglichem Replikationsbatch) die Einstellungen ein, die zum Berechnen der Anforderungen für die erste Replikation verwendet werden. Bei der Bereitstellung von Site Recovery wird das gesamte Anfangsdataset hochgeladen.

   ![Eingaben](./media/site-recovery-capacity-planner/inputs.png)

4. Nachdem Sie die Werte für die Quellumgebung eingegeben haben, enthält die angezeigte Ausgabe Folgendes:

   * **Bandwidth required for delta replication (MBit/s)** (Erforderliche Bandbreite für die Deltareplikation [MBit/s]): Die Netzwerkbandbreite für die Deltareplikation wird basierend auf der durchschnittlichen täglichen Datenänderungsrate berechnet.
   * **Bandwidth required for initial replication (MBit/s)** (Erforderliche Bandbreite für die Erstreplikation [MBit/s]): Die Netzwerkbandbreite für die erste Replikation wird basierend auf den Werten für die erste Replikation berechnet, die Sie eingeben.
   * **Storage required (in GBs)** (Erforderlicher Speicher [in GB]): Erforderlicher Gesamtspeicher in Azure.
   * **Total IOPS on Standard Storage** (Gesamt-IOPS in Storage Standard): Die Anzahl wird basierend auf der IOPS-Einheitengröße 8K für alle Storage Standard-Konten berechnet. Für den Quick Planner (Schnellplaner) wird die Anzahl basierend auf allen VM-Quelldatenträgern und der täglichen Datenänderungsrate berechnet. Für den Detailed Planner (Detaillierter Planer) wird die Anzahl basierend auf der Gesamtzahl der VMs, die Azure-Standard-VMs zugeordnet sind, und der Datenänderungsrate auf diesen VMs berechnet.
   * **Number of standard storage accounts** (Anzahl von Storage Standard-Konten): Gesamtzahl von Storage Standard-Konten, die zum Schützen der VMs benötigt werden. Ein Storage Standard-Konto kann bis zu 20.000 IOPS über alle VMs in einem Standardspeicher hinweg leisten. Pro Datenträger werden dabei maximal 500 IOPS unterstützt.
   * **Number of Blob disks required** (Anzahl erforderlicher Blobdatenträger): Anzahl von Datenträgern, die in Azure Storage erstellt werden
   * **Number of premium storage accounts required** (Anzahl erforderlicher Storage Premium-Konten): Gesamtanzahl von Storage Premium-Konten, die zum Schützen der VMs benötigt werden. Beachten Sie, dass für eine Quell-VM mit hohem IOPS-Wert (höher als 20.000) ein Storage Premium-Konto erforderlich ist. Ein Storage Premium-Konto kann bis zu 80.000 IOPS leisten.
   * **Total IOPS on premium storage** (Gesamt-IOPS in Storage Premium): Die Anzahl wird basierend auf der IOPS-Einheitengröße 256K für alle Storage Standard-Konten berechnet. Für den Quick Planner (Schnellplaner) wird die Anzahl basierend auf allen VM-Quelldatenträgern und der täglichen Datenänderungsrate berechnet. Für den Detailed Planner (Detaillierter Planer) wird die Anzahl basierend auf der Gesamtzahl der VMs, die Azure-Premium-VMs (DS- und GS-Reihe) zugeordnet sind, und der Datenänderungsrate auf diesen VMs berechnet.
   * **Number of configuration servers required** (Anzahl erforderlicher Konfigurationsserver): Zeigt an, wie viele Konfigurationsserver für die Bereitstellung erforderlich sind.
   * **Number of additional process servers required** (Anzahl zusätzlich erforderlicher Prozessserver): Gibt an, ob weitere Prozessserver außer dem Prozessserver, der standardmäßig auf dem Konfigurationsserver ausgeführt wird, erforderlich sind
   * **100% additional storage on the source** (100% zusätzlicher Speicher auf Quelle): Zeigt an, ob am Quellspeicherort zusätzlicher Speicher erforderlich ist

      ![Output](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>Ausführen des Detailed Planner-Tools

1. Laden Sie das Tool [Site Recovery Capacity Planner](https://aka.ms/asr-capacity-planner-excel) herunter, und öffnen Sie es. Sie müssen Makros ausführen. Entscheiden Sie, wenn Sie dazu aufgefordert werden, ob Sie das Bearbeiten und die Inhalte aktivieren möchten.

2. Wählen Sie unter **Select a planner type** im Listenfeld die Option **Detailed Planner** aus.

   ![Leitfaden zu den ersten Schritten](./media/site-recovery-capacity-planner/getting-started-2.png)

3. Geben Sie im Arbeitsblatt **Workload Qualification** (Workload-Qualifikation) die erforderlichen Informationen ein. Sie müssen alle markierten Felder ausfüllen.

   a. Geben Sie unter **Processor Cores** (Prozessorkerne) die Gesamtzahl der Kerne auf einem Quellserver an.

   b. Geben Sie unter **Memory allocation (in MBs)** (Speicherbelegung [in MB]) die RAM-Größe eines Quellservers an.

   c. Geben Sie unter **Number of NICs** (Anzahl der Netzwerkadapter) die Anzahl der Netzwerkadapter auf einem Quellserver an.

   d. Geben Sie unter **Total storage (in GB)** (Gesamtspeicher [in GB]) die Gesamtgröße des VM-Speichers an. Wenn der Quellserver beispielsweise über drei Datenträger mit jeweils 500 GB verfügt, beträgt die Gesamtgröße 1.500 GB.

   e. Geben Sie unter **Number of disks attached** (Anzahl der angefügten Datenträger) die Gesamtzahl der Datenträger eines Quellservers an.

   f. Geben Sie unter **Disk capacity utilization (%)** (Auslastung der Datenträgerkapazität [%]) die durchschnittliche Auslastung an.

   g. Geben Sie unter **Daily change rate (%)** (Tägliche Datenänderungsrate [%]) die tägliche Datenänderungsrate eines Quellservers an.

   h. Geben Sie unter **Mapping Azure VM size** (Azure-VM-Größe für Zuordnung) die Größe der Azure-VM ein, die Sie zuordnen möchten. Wenn Sie keine manuelle Eingabe durchführen möchten, wählen Sie **Compute IaaS VMs** (IaaS-VMs berechnen) aus. Wenn Sie eine manuelle Einstellung vorgenommen haben und dann **Compute IaaS VMs** (IaaS-VMs berechnen) auswählen, wird die manuelle Einstellung möglicherweise überschrieben. Bei der Berechnung wird automatisch die beste Übereinstimmung für die Azure-VM-Größe ermittelt.

   ![Arbeitsblatt „Workload-Qualifikation“](./media/site-recovery-capacity-planner/workload-qualification.png)

4. Wenn Sie **Compute IaaS VMs** (IaaS-VMs berechnen) auswählen, geschieht Folgendes:

   * Die obligatorischen Eingaben werden überprüft.
   * Der IOPS-Wert wird berechnet, und es wird die beste Azure-VM-Größenübereinstimmung für jede VM ermittelt, die für die Replikation zu Azure geeignet ist. Falls keine passende Größe einer Azure-VM ermittelt werden kann, wird ein Fehler angezeigt. Wenn die Anzahl der angefügten Datenträger beispielsweise 65 beträgt, wird ein Fehler angezeigt, da der höchste zulässige Wert für eine Azure-VM-Größe 64 beträgt.
   * Es wird ein Speicherkonto vorgeschlagen, das für eine Azure-VM verwendet werden kann.
   * Es wird berechnet, wie viele Standard-Speicherkonten und wie viele Premium-Speicherkonten für die Workload insgesamt erforderlich sind. Scrollen Sie nach unten, um den Azure Storage-Typ und das Speicherkonto anzuzeigen, die für einen Quellserver verwendet werden können.
   * Der Rest der Tabelle wird basierend auf dem erforderlichen Speichertyp (Standard oder Premium), der einer VM zugewiesen ist, und der Anzahl von Datenträgern ausgefüllt und sortiert. Für alle VMs, die die Anforderungen für Azure erfüllen, wird in der Spalte **Is VM qualified?** (VM qualifiziert?) **Yes** (Ja) angezeigt. Wenn eine VM nicht in Azure gesichert werden kann, wird ein Fehler angezeigt.

Die Spalten AA bis AE werden ausgegeben und enthalten Informationen für jeden virtuellen Computer.

![Ausgabespalten AA bis AE](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Beispiel
Beispielsweise berechnet das Tool für sechs VMs mit den in der Tabelle angezeigten Werten die beste Azure-VM-Übereinstimmung und Azure Storage-Anforderungen und weist sie zu.

![Workload-Qualifikation – Zuweisungen](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* Beachten Sie Folgendes in der Beispielausgabe:

  * Die erste Spalte enthält die Validierung für die VMS, die Datenträger und die Wechselrate.
  * Zwei Standard-Speicherkonten und ein Premium-Speicherkonto werden für fünf VMs benötigt.
  * VM3 erfüllt die Qualifikation für den Schutz nicht, da mindestens ein Datenträger größer als 1 TB ist.
  * Für VM1 und VM2 kann das erste Standard-Speicherkonto verwendet werden.
  * Für VM4 kann das zweite Standard-Speicherkonto verwendet werden.
  * VM5 und VM6 benötigen ein Storage Premium-Konto, und beide können ein einzelnes Konto verwenden.

    > [!NOTE]
    > Der IOPS-Wert für Standardspeicher und Storage Premium wird auf VM-Ebene und nicht auf Datenträgerebene berechnet. Eine Standard-VM kann bis zu 500 IOPS pro Datenträger verarbeiten. Wenn der IOPS-Wert für einen Datenträger mehr als 500 beträgt, benötigen Sie einen Premium-Speicher. Wenn der IOPS-Wert für einen Datenträger über 500 liegt, aber der IOPS-Wert für alle VM-Datenträger innerhalb der standardmäßigen Unterstützungsgrenzen für Azure-VMs liegt, wählt der Planer statt der DS- oder GS-Reihe eine Standard-VM aus. (Die Azure-VM-Grenzwerte sind VM-Größe, Anzahl von Datenträgern, Anzahl von Netzwerkadaptern, CPU und Arbeitsspeicher.) Sie müssen die Zuordnung der Azure-Größenzelle manuell mit der geeigneten VM aus der DS- oder GS-Reihe aktualisieren.


Nachdem alle Informationen eingegeben wurden, wählen Sie **Submit data to the planner tool** (Daten an Planertool übermitteln) aus, um Capacity Planner zu öffnen. Workloads werden hervorgehoben, um anzuzeigen, ob sie für den Schutz berechtigt sind.

### <a name="submit-data-in-capacity-planner"></a>Senden von Daten in Capacity Planner
1. Beim Öffnen wird das Arbeitsblatt **Capacity Planner** basierend auf den Einstellungen, die Sie angegeben haben, ausgefüllt. In der Zelle **Infra inputs source** (Infrastruktureingabenquelle) wird „Workload“ angezeigt, um zu zeigen, dass die Eingabe das Arbeitsblatt **Workload Qualification** (Workload-Qualifikation) ist.

2. Änderungen müssen Sie ggf. im Arbeitsblatt **Workload Qualification** (Workload-Qualifikation) vornehmen. Wählen Sie anschließend erneut **Submit data to the planner tool** (Daten an Planertool übermitteln) aus.

   ![Capacity Planner](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>Nächste Schritte
[Erfahren Sie, wie Sie das Tool Capacity Planner ausführen.](site-recovery-capacity-planning-for-hyper-v-replication.md)
