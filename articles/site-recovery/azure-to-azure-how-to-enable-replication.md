---
title: Konfigurieren der Replikation für virtuelle Azure-Computer in Azure Site Recovery
description: Erfahren Sie, wie Sie die Replikation in einer anderen Region für Azure-VMs mit Site Recovery konfigurieren.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 04/29/2018
ms.openlocfilehash: fe5feed4bb6f9b84a3f161692310922f7a6d2f00
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424798"
---
# <a name="replicate-azure-vms-to-another-azure-region"></a>Replizieren virtueller Azure-Computer zu einer anderen Azure-Region


In diesem Artikel erfahren Sie, wie Sie die Replikation virtueller Azure-Computer zwischen Azure-Regionen aktivieren.

## <a name="before-you-start"></a>Vorbereitung

In diesem Artikel wird vorausgesetzt, dass Sie die Site Recovery-Bereitstellung entsprechend der Beschreibung im [Tutorial zur Azure-zu-Azure-Notfallwiederherstellung](azure-to-azure-tutorial-enable-replication.md) vorbereitet haben.

Die Voraussetzungen sollten erfüllt sein, und Sie sollten einen Recovery Services-Tresor erstellt haben.


## <a name="enable-replication"></a>Aktivieren der Replikation

Aktivieren Sie die Replikation. In diesem Verfahren wird davon ausgegangen, dass als primäre Azure-Region „Asien, Osten“ und als sekundäre Region „Asien, Südosten“ verwendet wird.

1. Klicken Sie im Tresor auf **+Replizieren**.
2. Beachten Sie die folgenden Felder:
   - **Quelle** : Der Ursprung der virtuellen Computer, in diesem Fall **Azure**.
   - **Quellstandort** : Die Azure-Region, in der Sie Ihre virtuellen Computer schützen möchten. In diesem Beispiel ist der Quellspeicherort „Asien, Osten“.
   - **Bereitstellungsmodell** : Das Azure-Bereitstellungsmodell der Quellcomputer.
   - **Quellabonnement** : Das Abonnement, zu dem Ihre Quell-VMs gehören. Dies kann ein beliebiges Abonnement im gleichen Azure Active Directory-Mandanten sein, in dem sich auch Ihr Recovery Services-Tresor befindet.
   - **Ressourcengruppe** : Die Ressourcengruppe, der Ihre virtuellen Quellcomputer angehören. Alle virtuellen Computer der ausgewählten Ressourcengruppe werden im nächsten Schritt für den Schutz aufgeführt.

     ![Screenshot, auf dem die erforderlichen Felder zum Konfigurieren der Replikation hervorgehoben sind](./media/site-recovery-replicate-azure-to-azure/enabledrwizard1.png)

3. Klicken Sie auf **Virtuelle Computer > Virtuelle Computer auswählen** , und wählen Sie die virtuellen Computer aus, die Sie replizieren möchten. Sie können nur Computer auswählen, für die die Replikation aktiviert werden kann. Klicken Sie dann auf **OK**.
    ![Screenshot, der zeigt, wo Sie virtuelle Computer auswählen](./media/site-recovery-replicate-azure-to-azure/virtualmachine_selection.png)

4. Unter **Einstellungen** können Sie optional Einstellungen für den Zielstandort konfigurieren:

   - **Zielstandort** : Der Standort, an den die Daten Ihrer virtuellen Quellcomputer repliziert werden. Abhängig vom ausgewählten Computerstandort, stellt Site Recovery eine Liste der geeigneten Zielregionen bereit. Es empfiehlt sich, als Zielstandort den gleichen Standort zu verwenden wie für den Recovery Services-Tresor.
   - **Zielabonnement** : Das Zielabonnement für die Notfallwiederherstellung. Zielabonnement und Quellabonnement sind standardmäßig identisch.
   - **Zielressourcengruppe** : Die Ressourcengruppe, der all Ihre replizierten virtuellen Computer angehören.
       - Site Recovery erstellt in der Zielregion standardmäßig eine neue Ressourcengruppe mit dem Suffix „asr“ im Namen.
       - Falls eine von Site Recovery erstellte Ressourcengruppe bereits vorhanden ist, wird sie wiederverwendet.
       - Sie können die Einstellungen der Ressourcengruppe anpassen.
       - Der Speicherort der Zielressourcengruppe kann eine beliebige Azure-Region sein – mit Ausnahme der Region, in der die Quell-VMs gehostet werden.
   - **Virtuelles Zielnetzwerk** : Site Recovery erstellt in der Zielregion standardmäßig ein neues virtuelles Netzwerk mit dem Suffix „asr“ im Namen. Dieses wird Ihrem Quellnetzwerk zugeordnet und für alle zukünftigen Schutzaktivitäten verwendet werden. Informationen zur Netzwerkzuordnung finden Sie [hier](./azure-to-azure-network-mapping.md).
   - **Zielspeicherkonten (Quell-VM verwendet keine verwalteten Datenträger)** : Standardmäßig erstellt Site Recovery ein neues Zielspeicherkonto und übernimmt dabei die Speicherkonfiguration Ihrer Quell-VM. Sollte bereits ein Speicherkonto vorhanden sein, wird dieses wiederverwendet.
   - **Verwaltete Replikatdatenträger (Quell-VM verwendet verwaltete Datenträger)** : Site Recovery erstellt in der Zielregion neue verwaltete Replikatdatenträger, um die verwalteten Datenträger der Quell-VM zu spiegeln. Dabei wird der gleiche Speichertyp („Standard“ oder „Premium“) wie für den verwalteten Datenträger der Quell-VM verwendet.
   - **Cachespeicherkonten** : Site Recovery benötigt als zusätzliches Speicherkonto in der Quellregion ein so genanntes Cachespeicherkonto. Alle Änderungen an den virtuellen Quellcomputern werden nachverfolgt und vor der Replikation dieser Computer am Zielspeicherort an das Cachespeicherkonto gesendet. Es sollte sich um ein Speicherkonto des Typs Standard handeln.
   - **Zielverfügbarkeitsgruppen** : Standardmäßig erstellt Site Recovery in der Zielregion eine neue Verfügbarkeitsgruppe mit dem Suffix „asr“ im Namen für die virtuellen Computer, die zu einer Verfügbarkeitsgruppe in der Quellregion gehören. Falls die von Site Recovery erstellte Verfügbarkeitsgruppe bereits vorhanden ist, wird sie wiederverwendet.
   - **Zielverfügbarkeitszonen** : Site Recovery weist in der Zielregion standardmäßig die gleiche Anzahl von Zonen zu wie in der Quellregion, sofern die Zielregion Verfügbarkeitszonen unterstützt.

     Unterstützt die Zielregion keine Verfügbarkeitszonen, werden die virtuellen Zielcomputer standardmäßig als einzelne Instanzen konfiguriert. Sie können solche virtuellen Computer bei Bedarf auch als Teil von Verfügbarkeitsgruppen in der Zielregion konfigurieren. Klicken Sie dazu auf „Anpassen“.

     >[!NOTE]
     >Nach der Aktivierung der Replikation können Sie den Verfügbarkeitstyp (einzelne Instanz, Verfügbarkeitsgruppe oder Verfügbarkeitszone) nicht mehr ändern. Wenn Sie den Verfügbarkeitstyp ändern möchten, müssen Sie die Replikation deaktivieren und wieder aktivieren.
     >

   - **Replikationsrichtlinie** : Eine Replikationsrichtlinie definiert die Einstellungen für den Aufbewahrungsverlauf des Wiederherstellungspunkts und die Häufigkeit von anwendungskonsistenten Momentaufnahmen. Standardmäßig erstellt Azure Site Recovery eine neue Replikationsrichtlinie mit der Standardeinstellung „24 Stunden“ für den Aufbewahrungszeitraum des Wiederherstellungspunkts und „4 Stunden“ für die App-konsistente Momentaufnahmenhäufigkeit.

     ![Aktivieren der Replikation](./media/site-recovery-replicate-azure-to-azure/enabledrwizard3.PNG)

### <a name="enable-replication-for-added-disks"></a>Aktivieren der Replikation für hinzugefügte Datenträger

Wenn Sie Datenträger zu einer Azure-VM hinzufügen, für die die Replikation aktiviert ist, geschieht Folgendes:
-   Die Replikationsintegrität für die VM zeigt eine Warnung an, und ein Hinweis informiert sie, dass ein oder mehrere Datenträger für den Schutz zur Verfügung stehen.
-   Wenn Sie den Schutz für die hinzugefügten Datenträger aktivieren, wird die Warnung nach der ersten Replikation des Datenträgers nicht mehr angezeigt.
-   Wenn Sie wählen, dass die Replikation für den Datenträger nicht aktiviert werden soll, können Sie die Warnung wahlweise verwerfen.


    ![Neuer Datenträger hinzugefügt](./media/azure-to-azure-how-to-enable-replication/newdisk.png)

Führen Sie folgende Schritte aus, um die Replikation für einen hinzugefügten Datenträger zu aktivieren:

1.  Klicken Sie im Tresor > **Replizierte Elemente**  auf die VM, der Sie den Datenträger hinzugefügt haben.
2.  Klicken Sie auf **Datenträger** , und wählen Sie den Datenträger aus, für den Sie die Replikation aktivieren möchten (diese Datenträger haben den Status **Nicht geschützt** ).
3.  Klicken Sie in **Datenträgerdetails** auf **Replikation aktivieren**.

    ![Aktivieren der Replikation für einen hinzugefügten Datenträger](./media/azure-to-azure-how-to-enable-replication/enabled-added.png)

Nachdem die Ausführung des Auftrags zum Aktivieren der Replikation begonnen hat und die erste Replikation beendet wurde, wird die Warnung zur Replikationsintegrität für das Datenträgerproblem entfernt.



## <a name="customize-target-resources"></a>Anpassen der Zielressourcen

Sie können die von Site Recovery verwendeten Standardzieleinstellungen ändern.

1. Klicken Sie neben „Zielabonnement“ auf **Anpassen:** , um das Standardzielabonnement zu ändern. Wählen Sie das Abonnement aus der Liste aller Abonnements aus, die im gleichen Azure Active Directory-Mandanten (AAD) verfügbar sind.

2. Klicken Sie zum Ändern der Standardeinstellungen auf **Anpassen** :
    - Wählen Sie unter **Zielressourcengruppe** aus der Liste mit allen Ressourcengruppen, die innerhalb des Abonnements am Zielspeicherort vorhanden sind, die gewünschte Ressourcengruppe aus.
    - Wählen Sie unter **Virtuelles Zielnetzwerk** in der Liste mit allen virtuellen Netzwerken am Zielspeicherort das gewünschte Netzwerk aus.
    - Unter **Verfügbarkeitsgruppe** können Sie dem virtuellen Computer Verfügbarkeitsgruppeneinstellungen hinzufügen, sofern dieser einer Verfügbarkeitsgruppe in der Quellregion angehört.
    - Wählen Sie unter **Zielspeicherkonten** das Konto aus, das Sie verwenden möchten.

        ![Screenshot, der zeigt, wie die Einstellungen des Zielabonnements angepasst werden](./media/site-recovery-replicate-azure-to-azure/customize.PNG)
3. Klicken Sie zum Ändern der Replikationseinstellungen auf **Anpassen**.
4. Wählen Sie in **Multi-VM-Konsistenz** die VMs aus, die Sie zusammen replizieren möchten.
    - Alle Computer in einer Replikationsgruppe verfügen beim Failover über absturz- und anwendungskonsistente Wiederherstellungspunkte.
    - Das Aktivieren von Multi-VM-Konsistenz kann die Workloadleistung beeinträchtigen (da sie CPU-intensiv ist). Sie sollte nur aktiviert werden, wenn Computer dieselbe Workload ausführen und Konsistenz für mehrere Computer erforderlich ist.
    - Wenn eine Anwendung beispielsweise über 2 virtuelle SQL Server-Computer und zwei Webserver verfügt, sollten Sie nur die SQL Server-VMs zu einer Replikationsgruppe hinzufügen.
    - Sie können wählen, dass eine Replikationsgruppe maximal 16 VMs enthalten kann.
    - Wenn Sie die Multi-VM-Konsistenz aktivieren, kommunizieren Computer in der Replikationsgruppe über den Port 20004 miteinander.
    - Stellen Sie sicher, dass die interne Kommunikation zwischen den VMs über Port 20004 nicht durch eine Firewallappliance blockiert wird.
    - Wenn Sie Linux-VMs in eine Replikationsgruppe einschließen möchten, stellen Sie sicher, dass der ausgehende Datenverkehr auf Port 20004 entsprechend den Anweisungen für die jeweilige Linux-Version manuell geöffnet wird.
![Screenshot: Einstellungen für die Konsistenz mehrerer virtueller Computer](./media/site-recovery-replicate-azure-to-azure/multivmsettings.PNG)

5. Klicken Sie auf **Zielressource erstellen** > **Replikation aktivieren**.
6. Nachdem Sie die Replikation für die virtuellen Computer aktiviert haben, können Sie unter **Replizierte Elemente** den VM-Integritätsstatus überprüfen.

>[!NOTE]
>
> - Die Aktualisierung des Status kann während der Erstreplikation einige Zeit dauern, und zu Beginn wird unter Umständen kein Fortschritt angezeigt. Klicken Sie auf die Schaltfläche **Aktualisieren** , um den aktuellen Status abzurufen.
> - Wenn in den letzten 60 Minuten kein ein Wiederherstellungspunkt generiert wurde, nimmt der virtuelle Computer einen kritischen Replikationszustand an.

## <a name="next-steps"></a>Nächste Schritte

[Erfahren Sie mehr](site-recovery-test-failover-to-azure.md) über die Ausführung von Testfailovervorgängen.
