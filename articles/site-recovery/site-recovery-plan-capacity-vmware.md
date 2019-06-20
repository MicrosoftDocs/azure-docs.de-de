---
title: Planen der Kapazität und Skalierung der VMware-Notfallwiederherstellung mit Azure Site Recovery | Microsoft-Dokumentation
description: Dieser Artikel kann Ihnen helfen, die Kapazität und Skalierung zu planen, wenn Sie die Notfallwiederherstellung von VMware-VMs für Azure mithilfe von Azure Site Recovery einrichten.
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.date: 4/9/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 9a77b3982d8aed6ae694c32baecd7ae194c51724
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64924846"
---
# <a name="plan-capacity-and-scaling-for-vmware-disaster-recovery-to-azure"></a>Planen der Kapazität und Skalierung der VMware-Notfallwiederherstellung für Azure

Verwenden Sie diesen Artikel, um die Kapazität und Skalierung zu planen, wenn Sie VMware-VMs und physische Server lokal in Azure replizieren, indem Sie [Azure Site Recovery](site-recovery-overview.md) verwenden.

## <a name="how-do-i-start-capacity-planning"></a>Wie beginne ich mit der Kapazitätsplanung?

Erfassen Sie mit dem [Azure Site Recovery-Bereitstellungsplaner](https://aka.ms/asr-deployment-planner-doc) für die VMware-Replikation Informationen zu Ihrer Replikationsumgebung, um die Anforderungen für die Azure Site Recovery-Infrastruktur zu ermitteln. Weitere Informationen finden Sie unter [Azure Site Recovery-Bereitstellungsplaner für VMware in Azure](site-recovery-deployment-planner.md). 

Der Site Recovery-Bereitstellungsplaner stellt einen Bericht mit vollständigen Informationen zu kompatiblen und inkompatiblen VMs, zu den Datenträgern pro VM und zur Datenänderungsrate pro Datenträger bereit. Außerdem stellt das Tool eine Zusammenfassung der Netzwerkbandbreitenanforderungen zum Erreichen des RPO-Ziels und der erforderlichen Azure-Infrastruktur bereit, die für erfolgreiche Replikation und Testfailover benötigt wird.

## <a name="capacity-considerations"></a>Überlegungen zur Kapazität

Komponente | Details
--- | ---
**Replikation** | **Maximale tägliche Änderungsrate**: Für einen geschützten Computer kann nur ein Prozessserver verwendet werden. Ein einzelner Prozessserver kann eine tägliche Änderungsrate von bis zu 2 TB verarbeiten. Daher beträgt die maximale Datenänderungsrate pro Tag, die für einen geschützten Computer unterstützt wird, 2 TB.<br /><br /> **Maximaler Durchsatz**: Ein replizierter Computer kann zu einem einzigen Speicherkonto in Azure gehören. Ein Azure Storage-Standardkonto kann maximal 20.000 Anforderungen pro Sekunde verarbeiten. Es wird empfohlen, die Anzahl der Ein-/Ausgabevorgänge pro Sekunde (IOPS) auf einem Quellcomputer auf 20.000 zu begrenzen. Wenn Ihr Quellcomputer z.B. fünf Datenträger aufweist und jeder Datenträger 120 IOPS (mit einer Größe von 8 KB) auf dem Quellcomputer generiert, wird das Azure-Limit von 500 IOPS pro Datenträger für den Quellcomputer eingehalten. (Die Anzahl der erforderlichen Speicherkonten entspricht dem IOPS-Gesamtwert der Quellcomputer, geteilt durch 20.000.)
**Konfigurationsserver** | Der Konfigurationsserver muss in der Lage sein, die tägliche Änderungsratenkapazität über alle Workloads hinweg zu bewältigen, die auf geschützten Computern ausgeführt werden. Der Konfigurationscomputer muss über eine ausreichende Bandbreite verfügen, um Daten kontinuierlich in Azure Storage zu replizieren.<br /><br /> Als bewährte Methode sollten Sie den Konfigurationsserver im selben Netzwerk und LAN-Segment platzieren, in dem sich auch die zu schützenden Computer befinden. Sie können den Konfigurationsserver in einem anderen Netzwerk platzieren, aber die zu schützenden Computer sollten für ihn über Layer-3-Netzwerksichtbarkeit verfügen.<br /><br /> Die empfohlenen Größen für den Konfigurationsserver werden in der Tabelle im folgenden Abschnitt zusammengefasst.
**Prozessserver** | Der erste Prozessserver wird standardmäßig auf dem Konfigurationsserver installiert. Sie können zusätzliche Prozessserver bereitstellen, um Ihre Umgebung zu skalieren. <br /><br /> Der Prozessserver empfängt Replikationsdaten von den geschützten Computern. Der Prozessserver optimiert Daten mithilfe von Zwischenspeicherung, Komprimierung und Verschlüsselung. Anschließend sendet der Prozessserver die Daten an Azure. Der Prozessservercomputer muss über ausreichende Ressourcen zum Ausführen dieser Aufgaben verfügen.<br /><br /> Der Prozessserver verwendet einen datenträgerbasierten Cache. Verwenden Sie einen separaten Cachedatenträger mit einer Größe von mindestens 600 GB, um Datenänderungen bei einem Netzwerkengpass oder -ausfall zwischenspeichern und verarbeiten zu können.

## <a name="size-recommendations-for-the-configuration-server-and-inbuilt-process-server"></a>Empfohlene Größen für den Konfigurationsserver bzw. integrierte Prozessserver

Ein Konfigurationsserver, für den integrierte Prozessserver zum Schutz der Arbeitsauslastung verwendet werden, kann basierend auf den folgenden Konfigurationen bis zu 200 virtuelle Computer ausführen:

CPU | Arbeitsspeicher | Größe des Cachedatenträgers | Datenänderungsrate | Geschützte Computer
--- | --- | --- | --- | ---
8 vCPUs (2 Sockets * 4 Kerne \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB oder weniger | Wird verwendet, um weniger als 100 Computer zu replizieren.
12 vCPUs (2 Sockets * 6 Kerne \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB bis 1 TB | Wird verwendet, um 100 bis 150 Computer zu replizieren.
16 vCPUs (2 Sockets * 8 Kerne \@ 2,5 GHz) | 32 GB | 1 TB | Mehr als 1 TB bis 2 TB | Wird verwendet, um 151 bis 200 Computer zu replizieren.
Bereitstellen eines weiteren Konfigurationsservers mit einer [OVF-Vorlage](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template). | | | | Stellen Sie einen neuen Konfigurationsserver bereit, wenn Sie mehr als 200 Computer replizieren.
Bereitstellen eines weiteren [Prozessservers](vmware-azure-set-up-process-server-scale.md#download-installation-file). | | | >2 TB| Stellen Sie einen neuen Prozessserver für die horizontale Skalierung bereit, wenn die gesamte tägliche Datenänderungsrate 2 TB überschreitet.

In diesen Konfigurationen gilt Folgendes:

* Jeder Quellcomputer verfügt über drei Datenträger mit jeweils 100 GB.
* Bei den Benchmarkmessungen für den Cachedatenträger wurde ein Speicher aus acht SAS-Laufwerken mit 10.000 U/min und RAID 10 verwendet.

## <a name="size-recommendations-for-the-process-server"></a>Empfohlene Größen für den Prozessserver

Der Prozessserver ist die Komponente, die die Datenreplikation in Azure Site Recovery verarbeitet. Wenn die tägliche Änderungsrate 2 TB überschreitet, müssen Sie einen Prozessserver für die horizontale Skalierung hinzufügen, um die Replikationslast zu verarbeiten. Zum horizontalen Hochskalieren haben Sie folgende Möglichkeiten:

* Erhöhen Sie die Anzahl der Konfigurationsserver, indem Sie die Bereitstellung mit einer [OVF-Vorlage](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template) ausführen. Beispielsweise können Sie mit zwei Konfigurationsservern bis zu 400 Computer schützen.
* Fügen Sie [horizontal skalierte Prozessserver](vmware-azure-set-up-process-server-scale.md#download-installation-file) hinzu. Verwenden Sie die Prozessserver für horizontale Skalierung anstelle des Konfigurationsservers (oder als Ergänzung zu diesem) für die Verarbeitung des Replikationsdatenverkehrs.

Die folgende Tabelle beschreibt dieses Szenario:

* Sie haben einen Prozessserver für horizontale Skalierung eingerichtet.
* Sie haben geschützte VMs für die Verwendung des Prozessservers für horizontale Skalierung konfiguriert.
* Jeder geschützte Quellcomputer verfügt über drei Datenträger mit jeweils 100 GB.

Zusätzlicher Prozessserver | Größe des Cachedatenträgers | Datenänderungsrate | Geschützte Computer
--- | --- | --- | ---
4 vCPUs (2 Sockets mit jeweils 2 Kernen mit 2,5 GHz), 8 GB Arbeitsspeicher | 300 GB | 250 GB oder weniger | Wird verwendet, um maximal 85 Computer zu replizieren.
8 vCPUs (2 Sockets mit jeweils 4 Kernen mit 2,5 GHz), 12 GB Arbeitsspeicher | 600 GB | 251 GB bis 1 TB | Wird verwendet, um 86 bis 150 Computer zu replizieren.
12 vCPUs (2 Sockets mit jeweils 6 Kernen mit 2,5 GHz), 24 GB Arbeitsspeicher | 1 TB | Mehr als 1 TB bis 2 TB | Wird verwendet, um 151 bis 225 Computer zu replizieren.

Wie Sie Ihre Server skalieren, hängt davon ab, ob Sie das zentrale Hochskalieren oder das horizontale Hochskalieren als Modell bevorzugen. Zum zentralen Hochskalieren stellen Sie einige High-End-Konfigurationsserver und Prozessserver bereit. Zum horizontalen Hochskalieren stellen Sie weitere Server mit weniger Ressourcen bereit. Wenn Sie beispielsweise 200 Computer mit einer täglichen Datenänderungsrate von insgesamt 1,5 TB schützen möchten, können Sie eine der folgenden Maßnahmen ergreifen:

* Richten Sie einen einzelnen Prozessserver (16 vCPU, 24 GB RAM) ein.
* Richten Sie zwei Prozessserver ein (jeweils 8 vCPUs und 12 GB RAM).

## <a name="control-network-bandwidth"></a>Steuern der Netzwerkbandbreite

Nachdem Sie den [Site Recovery-Bereitstellungsplaner](site-recovery-deployment-planner.md) verwendet haben, um die Bandbreite zu berechnen, die Sie für die Replikation benötigen (anfängliche Replikation plus Delta), bestehen einige Möglichkeiten, die Bandbreite zu steuern, die für die Replikation verwendet wird:

* **Bandbreite drosseln**: VMware-Datenverkehr, der nach Azure repliziert wird, wird über einen speziellen Prozessserver geleitet. Sie können die Bandbreite auf den Computern begrenzen, die als Prozessserver ausgeführt werden.
* **Bandbreite beeinflussen**: Die für die Replikation genutzte Bandbreite lässt sich mithilfe einiger Registrierungsschlüssel beeinflussen:
  * Der Registrierungswert **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** gibt die Anzahl von Threads an, die für die Datenübertragung (erste Replikation oder Deltareplikation) eines Datenträgers verwendet werden. Bei einem höheren Wert wird die Netzwerkbandbreite für die Replikation erhöht.
  * Der Registrierungswert **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** gibt die Anzahl von Threads an, die für die Datenübertragung während eines Failbacks verwendet werden.

### <a name="throttle-bandwidth"></a>Bandbreite einschränken

1. Öffnen Sie das Azure Backup-MMC-Snap-In auf dem Computer, den Sie als Prozessserver verwenden. Standardmäßig ist eine Verknüpfung für Backup auf dem Desktop oder im folgenden Ordner verfügbar: C:\Programme\Microsoft Azure Recovery Services Agent\bin.
2. Wählen Sie im Snap-In **Eigenschaften ändern** aus.

    ![Screenshot der Azure Backup-MMC-Snap-In-Option zum Ändern von Eigenschaften](./media/site-recovery-vmware-to-azure/throttle1.png)
3. Aktivieren Sie auf der Registerkarte **Drosselung** die Option **Internet-Bandbreiteneinschränkung für Sicherungsvorgänge aktivieren**. Legen Sie die Grenzwerte für Arbeitsstunden und arbeitsfreie Stunden fest. Der gültige Bereich reicht von 512 KBit/s bis 1,023 MBit/s.

    ![Screenshot des Dialogfelds „Eigenschaften von Azure Backup“](./media/site-recovery-vmware-to-azure/throttle2.png)

Sie können auch das Cmdlet [Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) verwenden, um die Drosselung festzulegen. Hier sehen Sie ein Beispiel:

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** gibt an, dass keine Drosselung erforderlich ist.

### <a name="alter-the-network-bandwidth-for-a-vm"></a>Ändern der Netzwerkbandbreite für eine VM

1. Navigieren Sie in der Registrierung der VM zu **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication**.
   * Um die Bandbreite für den Datenverkehr auf einem replizierenden Datenträger zu ändern, ändern Sie den Wert von **UploadThreadsPerVM**. Erstellen Sie den Schlüssel, falls dieser nicht vorhanden ist.
   * Um die Bandbreite für den Failbackdatenverkehr von Azure zu ändern, ändern Sie den Wert von **DownloadThreadsPerVM**.
2. Der Standardwert für jeden dieser Schlüssel ist **4**. In einem absichtlich mit großen Reserven ausgestatteten Netzwerk müssen die Standardwerte dieser Registrierungsschlüssel geändert werden. Der maximale Wert, der verwendet werden kann, ist **32**. Überwachen Sie den Datenverkehr, um den Wert zu optimieren.

## <a name="set-up-the-site-recovery-infrastructure-to-protect-more-than-500-vms"></a>Einrichten der Site Recovery-Infrastruktur zum Schützen von mehr als 500 VMs

Bevor Sie die Site Recovery-Infrastruktur einrichten, greifen Sie auf die Umgebung zu, um die folgenden Faktoren zu messen: die kompatiblen virtuellen Computer, die tägliche Datenänderungsrate, die erforderliche Netzwerkbandbreite für das zu erreichende RPO-Ziel, die Anzahl der erforderlichen Site Recovery-Komponenten und die Zeit, die benötigt wird, um die anfängliche Replikation abzuschließen. Führen Sie die folgenden Schritte aus, um die erforderlichen Informationen zu erfassen:

1. Um diese Parameter zu messen, führen Sie den Site Recovery-Bereitstellungsplaner für Ihre Umgebung aus. Hilfreiche Leitfäden dazu finden Sie unter [Azure Site Recovery-Bereitstellungsplaner für VMware in Azure](site-recovery-deployment-planner.md).
2. Stellen Sie einen Konfigurationsserver bereit, der die [Größenempfehlungen für den Konfigurationsserver](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) erfüllt. Stellen Sie einen weiteren Konfigurationsserver bereit, wenn Ihre Produktionsarbeitsauslastung 650 VMs überschreitet.
3. Stellen Sie [Prozessserver für die horizontale Skalierung](vmware-azure-set-up-process-server-scale.md#download-installation-file) basierend auf der ermittelten täglichen Datenänderungsrate mithilfe der [Größenleitfäden](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server) bereit.
4. Wenn Sie erwarten, dass die Datenänderungsrate für einen VM-Datenträger 2 MBit/s überschreitet, stellen Sie sicher, dass Sie verwaltete Premium-Datenträger verwenden. Der Site Recovery-Bereitstellungsplaner wird für einen bestimmten Zeitraum ausgeführt. Spitzenwerte in der Datenänderungsrate zu anderen Zeiten werden möglicherweise nicht im Bericht erfasst.
5. [Legen Sie die Netzwerkbandbreite](site-recovery-plan-capacity-vmware.md#control-network-bandwidth) basierend auf dem RPO-Wert fest, den Sie erzielen möchten.
6. Nachdem die Infrastruktur eingerichtet wurde, aktivieren Sie Notfallwiederherstellung für Ihre Workload. Weitere Informationen dazu finden Sie unter [Einrichten der Quellumgebung für die Replikation von VMware in Azure](vmware-azure-set-up-source.md).

## <a name="deploy-additional-process-servers"></a>Bereitstellen zusätzlicher Prozessserver

Wenn Sie Ihre Bereitstellung über 200 Quellcomputer oder eine gesamte tägliche Änderungsrate von 2 TB hinaus horizontal hochskalieren, müssen Sie weitere Prozessserver zur Bewältigung des Datenverkehrsaufkommens hinzufügen. Wir haben das Produkt in Version 9.24 erweitert, um beim Einrichten eines Prozessservers für die horizontale Skalierung das [Verarbeiten von Serverwarnungen](vmware-physical-azure-monitor-process-server.md#process-server-alerts) zu ermöglichen. [Richten Sie den Prozessserver so ein](vmware-azure-set-up-process-server-scale.md), dass er neue Quellcomputer schützt oder [für einen Lastenausgleich sorgt](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load).

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrieren der Computer für die Verwendung des neuen Prozessservers

1. Wählen Sie **Einstellungen** > **Site Recovery-Server** aus. Wählen Sie den Konfigurationsserver aus, und erweitern Sie dann die **Prozessserver**.

    ![Screenshot des Dialogfelds „Prozessserver“](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Klicken Sie mit der rechten Maustaste auf den aktuell verwendeten Prozessserver, und klicken Sie dann auf **Wechseln**.

    ![Screenshot des Dialogfelds „Konfigurationsserver“](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. Wählen Sie unter **Zielprozessserver auswählen** den neuen Prozessserver aus, den Sie verwenden möchten. Wählen Sie anschließend die virtuellen Computer aus, die vom neuen Server verarbeitet werden. Um weitere Informationen zum Server zu erhalten, wählen Sie das Informationssymbol aus. Der durchschnittliche zum Replizieren jedes ausgewählten virtuellen Computers auf den neuen Prozessserver erforderliche Speicherplatz wird angezeigt, um Sie bei Lastenentscheidungen zu unterstützen. Klicken Sie auf das Häkchen, um mit dem Replizieren auf den neuen Prozessserver zu beginnen.

## <a name="deploy-additional-master-target-servers"></a>Bereitstellen von zusätzlichen Masterzielservern

In den folgenden Szenarien sind mehrere Masterzielserver erforderlich:

*   Sie möchten einen Linux-basierten virtuellen Computer schützen.
*   Der auf dem Konfigurationsserver verfügbare Masterzielserver besitzt keinen Zugriff auf den Datenspeicher auf dem virtuellen Computer.
*   Die Gesamtzahl der Datenträger auf dem Masterzielserver (die Anzahl der lokalen Datenträger auf dem Server zuzüglich der Anzahl der zu schützenden Datenträger) ist größer als 60 Datenträger.

Informationen zum Hinzufügen eines Masterzielservers für einen Linux-basierten virtuellen Computer finden Sie unter [Installieren eines Linux-Masterzielservers für Failback](vmware-azure-install-linux-master-target.md).

So fügen Sie einen neuen Masterzielserver für einen Linux-basierten virtuellen Computer hinzu:

1. Navigieren Sie zu **Recovery Services-Tresor** > **Site Recovery-Infrastruktur** > **Konfigurationsserver**.
2. Wählen Sie den erforderlichen Konfigurationsserver aus, und wählen Sie dann **Masterzielserver** aus.

    ![Screenshot der Schaltfläche „Masterzielserver hinzufügen“](media/site-recovery-plan-capacity-vmware/add-master-target-server.png)
3. Laden Sie die vereinheitlichte Setupdatei herunter, und führen Sie die Datei dann auf dem virtuellen Computer aus, um den Masterzielserver einzurichten.
4. Wählen Sie **Masterziel installieren** > **Weiter** aus.

    ![Screenshot mit Auswahl der Option „Masterziel installieren“](media/site-recovery-plan-capacity-vmware/choose-MT.PNG)
5. Wählen Sie den Standardspeicherort für die Installation aus, und wählen Sie dann **Installieren** aus.

     ![Screenshot mit dem Standardspeicherort für die Installation](media/site-recovery-plan-capacity-vmware/MT-installation.PNG)
6. Um das Masterziel beim Konfigurationsserver zu registrieren, klicken Sie auf **Mit der Konfiguration fortfahren**.

    ![Screenshot mit der Schaltfläche „Mit der Konfiguration fortfahren“](media/site-recovery-plan-capacity-vmware/MT-proceed-configuration.PNG)
7. Geben Sie die IP-Adresse des Konfigurationsservers und dann die Passphrase ein. Informationen zum Generieren einer Passphrase finden Sie unter [Generieren einer Passphrase für den Konfigurationsserver](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase). 

    ![Screenshot, der zeigt, wo die IP-Adresse und die Passphrase für den Konfigurationsserver eingegeben wird.](media/site-recovery-plan-capacity-vmware/cs-ip-passphrase.PNG)
8. Wählen Sie **Registrieren**. Wenn die Registrierung abgeschlossen ist, wählen Sie **Fertig stellen** aus.

Wenn die Registrierung erfolgreich abgeschlossen wurde, wird der Server im Azure-Portal unter **Recovery Services-Tresor** > **Site Recovery-Infrastruktur** > **Konfigurationsserver** in den Masterzielservern des Konfigurationsservers aufgeführt.

 > [!NOTE]
 > Laden Sie die neueste Version der [vereinheitlichten Setupdatei des Master-Zielservers für Windows](https://aka.ms/latestmobsvc) herunter.

## <a name="next-steps"></a>Nächste Schritte

Laden Sie den [Site Recovery-Bereitstellungsplaner](https://aka.ms/asr-deployment-planner) herunter, und führen Sie ihn aus.
