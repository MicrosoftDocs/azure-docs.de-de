---
title: Verwenden von Modern Backup Storage mit Azure Backup Server
description: In diesem Artikel erfahren Sie mehr über die neuen Features in Azure Backup Server. Dieser Artikel beschreibt, wie Sie Ihre Backup-Server-Installation aktualisieren.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: adigan
ms.openlocfilehash: 621d071f98701ff3a949f4172fef1d13819d7192
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60813124"
---
# <a name="add-storage-to-azure-backup-server"></a>Hinzufügen von Speicher zu Azure Backup Server

Azure Backup Server v2 und höhere Versionen unterstützen Modern Backup Storage. Dieses Feature ermöglicht Speichereinsparungen von bis zu 50 %, dreimal schnellere Sicherungen und eine effizientere Speicherung. Zudem wird workloadorientierter Speicher geboten.

> [!NOTE]
> Zum Verwenden von Modern Backup Storage müssen Sie Backup Server v2 oder v3 unter Windows Server 2016 oder v3 unter Windows Server 2019 ausführen.
> Wenn Sie Backup Server v2 unter einer früheren Version von Windows Server ausführen, kann Modern Backup Storage nicht von Azure Backup Server genutzt werden. Workloads werden dann wie mit Backup Server v1 geschützt. Weitere Informationen finden Sie in der [Schutzmatrix](backup-mabs-protection-matrix.md) der Backup Server-Versionen.

## <a name="volumes-in-backup-server"></a>Volumes in Backup Server

Backup Server v2 oder höhere Versionen unterstützen Speichervolumes. Wenn Sie ein Volume hinzufügen, formatiert Azure Backup das Volume mit ReFS (Resilient File System), was für Modern Backup Storage erforderlich ist. Um ein Volume hinzuzufügen und es zu einem späteren Zeitpunkt zu erweitern, sollten Sie diesen Workflow befolgen:

1.  Richten Sie Backup Server auf einer VM ein.
2.  Erstellen Sie ein Volume auf einem virtuellen Datenträger in einem Speicherpool:
    1.  Fügen Sie einen Datenträger zu einem Speicherpool hinzu, und erstellen Sie einen virtuellen Datenträger mit einfachem Layout.
    2.  Fügen Sie zusätzliche Datenträger hinzu, und erweitern Sie den virtuellen Datenträger.
    3.  Erstellen Sie Volumes auf dem virtuellen Datenträger.
3.  Fügen Sie Backup Server die Volumes hinzu.
4.  Konfigurieren Sie workloadorientierten Speicher.

## <a name="create-a-volume-for-modern-backup-storage"></a>Erstellen eines Volumes für Modern Backup Storage

Durch Verwenden von Backup Server v2 oder höheren Versionen mit Volumes als Datenträgerspeicher können Sie besser die Kontrolle über die Speicherung behalten. Ein Volume kann ein einzelner Datenträger sein. Wenn Sie jedoch den Speicher in Zukunft erweitern möchten, erstellen Sie ein Volume aus einem mit dem Feature „Speicherplätze“ erstellten Datenträger. Dies kann hilfreich sein, wenn Sie das Volume für Sicherungsspeicher erweitern möchten. Dieser Abschnitt bietet bewährte Methoden zum Erstellen eines Volumes mit diesem Setup.

1. Wählen Sie in Server-Manager nacheinander **Datei- und Speicherdienste** > **Volumes** > **Speicherpools** aus. Klicken Sie unter **PHYSISCHE DATENTRÄGER** auf **Neuer Speicherpool**.

    ![Erstellen eines neuen Speicherpools](./media/backup-mabs-add-storage/mabs-add-storage-1.png)

2. Klicken Sie in der Dropdownliste **AUFGABEN** auf **Neuer virtueller Datenträger**.

    ![Hinzufügen eines virtuellen Datenträgers](./media/backup-mabs-add-storage/mabs-add-storage-2.png)

3. Wählen Sie den Speicherpool aus, und klicken Sie dann auf **Physischen Datenträger hinzufügen**.

    ![Hinzufügen eines physischen Datenträgers](./media/backup-mabs-add-storage/mabs-add-storage-3.png)

4. Wählen Sie den physischen Datenträger aus, und klicken Sie dann auf **Virtuellen Datenträger erweitern**.

    ![Erweitern des virtuellen Datenträgers](./media/backup-mabs-add-storage/mabs-add-storage-4.png)

5. Wählen Sie den virtuellen Datenträger aus, und klicken Sie dann auf **Neues Volume**.

    ![Erstellen eines neuen Volumes](./media/backup-mabs-add-storage/mabs-add-storage-5.png)

6. Wählen Sie im Dialogfeld **Server und Datenträger auswählen** den Server und den neuen Datenträger aus. Klicken Sie anschließend auf **Weiter**.

    ![Auswählen des Servers und Datenträgers](./media/backup-mabs-add-storage/mabs-add-storage-6.png)

## <a name="add-volumes-to-backup-server-disk-storage"></a>Hinzufügen von Volumes zu Azure Backup-Datenträgerspeicher

Um Backup Server ein Volume hinzuzufügen, durchsuchen Sie im Bereich **Verwaltung** erneut den Speicher, und klicken Sie dann auf **Hinzufügen**. Eine Liste aller Volumes wird angezeigt, die dem Azure Backup-Speicher hinzugefügt werden können. Nachdem verfügbare Volumes der Liste ausgewählter Volumes hinzugefügt wurden, können Sie sie mit einem Anzeigenamen versehen, damit sie besser verwaltet werden können. Um diese Volumes mit ReFS zu formatieren, damit Backup Server die Vorteile von Modern Backup Storage nutzen kann, klicken Sie auf **OK**.

![Hinzufügen verfügbarer Volumes](./media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="set-up-workload-aware-storage"></a>Einrichten von workloadorientiertem Speicher

Mithilfe von workloadorientiertem Speicher können Sie die Volumes auswählen, auf denen bestimmte Arten von Workloads vorzugsweise gespeichert werden sollen. Sie können beispielsweise kostenintensive Volumes festlegen, die eine hohe Anzahl von Ein- und Ausgabevorgängen pro Sekunde (IOPS) unterstützen, um nur die Workloads zu speichern, die häufige Sicherungen mit großem Volumen erfordern. Ein Beispiel ist SQL Server mit Transaktionsprotokollen. Andere Workloads, die weniger häufig gesichert werden, wie z.B. VMs, können auf kostengünstigen Volumes gesichert werden.

### <a name="update-dpmdiskstorage"></a>Update-DPMDiskStorage

Sie können workloadorientierten Speicher mithilfe des PowerShell-Cmdlets „Update-DPMDiskStorage“ einrichten, das die Eigenschaften eines Volumes im Speicherpool einer Azure Backup Server-Instanz aktualisiert. 

Syntax:

`Parameter Set: Volume`

```
Update-DPMDiskStorage [-Volume] <Volume> [[-FriendlyName] <String> ] [[-DatasourceType] <VolumeTag[]> ] [-Confirm] [-WhatIf] [ <CommonParameters>]
```
Der folgende Screenshot zeigt das Cmdlet „Update-DPMDiskStorage“ im PowerShell-Fenster.

![Das Cmdlet „Update DPMDiskStorage“ im PowerShell-Fenster](./media/backup-mabs-add-storage/mabs-add-storage-8.png)

Die Änderungen, die Sie mithilfe von PowerShell vornehmen, werden in der Backup-Server-Administratorkonsole angezeigt.

![Datenträger und Volumes in der Administratorkonsole](./media/backup-mabs-add-storage/mabs-add-storage-9.png)


## <a name="migrate-legacy-storage-to-modern-backup-storage"></a>Migrieren von Legacyspeicher zu Modern Backup Storage
Nach dem Upgrade auf oder der Installation von Backup Server v2 und dem Upgrade des Betriebssystems auf Windows Server 2016 müssen Sie Ihre Schutzgruppen für die Verwendung von Modern Backup Storage aktualisieren. Standardmäßig werden Schutzgruppen nicht geändert. Sie funktionieren weiterhin, wie sie ursprünglich eingerichtet wurden.

Das Aktualisieren von Schutzgruppen zur Verwendung von Modern Backup Storage ist optional. Beenden Sie zum Aktualisieren der Schutzgruppe den Schutz aller Datenquellen mithilfe der Option „Daten beibehalten“. Fügen Sie dann die Datenquellen einer neuen Schutzgruppe hinzu.

1. Wählen Sie in der Backup Server-Administratorkonsole **Schutz** aus. Klicken Sie in der Liste **Schutzgruppenmitglied** mit der rechten Maustaste auf das Mitglied, und wählen Sie dann **Schutz des Mitglieds beenden** aus.

   ![Schutz des Mitglieds beenden](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-stop-protection1.png)

2. Überprüfen Sie im Dialogfeld **Aus Gruppe entfernen** den belegten Speicherplatz und den verfügbaren freien Speicherplatz für den Speicherpool. Die Standardeinstellung ist das Belassen der Wiederherstellungspunkte auf dem Datenträger und sie gemäß der zugehörigen Beibehaltungsrichtlinie ablaufen zu lassen. Klicken Sie auf **OK**.

   Wenn der belegte Speicherplatz sofort an den Pool mit freiem Speicherplatz zurückgegeben werden soll, aktivieren Sie das Kontrollkästchen **Replikat auf Datenträger löschen**, um die Sicherungsdaten (und Wiederherstellungspunkte) zu löschen, die zu diesem Mitglied gehören.

   ![Dialogfeld „Aus Gruppe entfernen“](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-retain-data.png)

3. Erstellen Sie eine Schutzgruppe, die Modern Backup Storage verwendet. Fügen Sie die ungeschützten Datenquellen hinzu.

## <a name="add-disks-to-increase-legacy-storage"></a>Hinzufügen von Datenträgern zum Vergrößern des Legacyspeichers

Wenn Sie Legacyspeicher mit Backup Server verwenden möchten, müssen Sie möglicherweise Datenträger hinzufügen, um den Legacyspeicher zu vergrößern.

So fügen Sie Datenspeicher hinzu

1. Wählen Sie in der Verwaltungskonsole **Verwaltung** > **Datenspeicher** > **Hinzufügen** aus.

    ![Dialogfeld „Datenspeicher hinzufügen“](https://docs.microsoft.com/system-center/dpm/media/upgrade-to-dpm-2016/dpm-2016-add-disk-storage.png)

4. Wählen Sie im Dialogfeld **Datenspeicher hinzufügen** den Befehl **Datenträger hinzufügen** aus.

5. Wählen Sie in der Liste der verfügbaren Datenträger die Datenträger aus, die Sie hinzufügen möchten. Klicken Sie auf **Hinzufügen** und dann auf **OK**.

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie, wie Sie nach der Installation von Backup Server Ihren Server vorbereiten oder mit dem Schutz einer Workload beginnen.

- [Vorbereiten von Backup Server-Workloads](backup-azure-microsoft-azure-backup.md)
- [Sichern eines VMware-Servers mit Backup Server](backup-azure-backup-server-vmware.md)
- [Sichern von SQL Server mit Backup Server](backup-azure-sql-mabs.md)
