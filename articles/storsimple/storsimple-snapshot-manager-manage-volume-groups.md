---
title: StorSimple Snapshot Manager-Volumegruppen | Microsoft Docs
description: Beschreibt, wie das MMC-Snap-In StorSimple Snapshot Manager zum Erstellen und Verwalten von Volumegruppen verwendet wird.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 7a232414-6a28-4b81-bd7b-cf61e28b33d7
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 805ab0e78f0679ce483af3a73203b41132be2a59
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022954"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-volume-groups"></a>Verwenden des StorSimple Snapshot Manager zum Erstellen und Verwalten von Volumegruppen
## <a name="overview"></a>Übersicht
Mit dem Knoten **Volumegruppen** im Fensterbereich **Bereich** können Sie Volumes Volumegruppen zuordnen, Informationen zu einer Volumegruppe anzeigen, Sicherungen planen und Volumegruppen bearbeiten.

Volumegruppen sind Pools zusammengehöriger Volumes, mit denen sichergestellt werden soll, dass Sicherungen anwendungskonsistent sind. Weitere Informationen finden Sie unter [Volumes und Volumegruppen](storsimple-what-is-snapshot-manager.md#volumes-and-volume-groups) und [Integration in den Windows-Volumeschattenkopie-Dienst](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).

> [!IMPORTANT]
> * Alle Volumes in einer Volumegruppe müssen von einem Cloud-Service Provider stammen.
> * Beim Konfigurieren von Volumegruppen sollten Sie nicht freigegebene Clustervolumes (CSV) und nicht freigegebene Clustervolumes in derselben Volumegruppe zusammenfassen. Der StorSimple Snapshot Manager unterstützt nicht das Zusammenfassen von freigegebenen Clustervolumes und nicht freigegebenen Clustervolumes in derselben Momentaufnahme.

![Knoten "Volumegruppen"](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Volume_groups.png)

**Abbildung 1: StorSimple Snapshot Manager-Knoten "Volumegruppen"** 

In diesem Lernprogramm erfahren Sie, wie Sie den StorSimple Snapshot Manager für Folgendes verwenden:

* Anzeigen von Informationen zu den Volumegruppen
* Erstellen einer Volumegruppe
* Sichern einer Volumegruppe
* Bearbeiten einer Volumegruppe
* Löschen einer Volumegruppe

Alle diese Aktionen stehen auch im Bereich **Aktionen** zur Verfügung.

## <a name="view-volume-groups"></a>Anzeigen von Volumegruppen
Wenn Sie auf den Knoten **Volumegruppen** klicken, werden im Bereich **Ergebnisse** je nach den ausgewählten Spalten die folgenden Informationen über jede Volumegruppe angezeigt. (Die Spalten im Bereich **Ergebnis** sind konfigurierbar. (Klicken Sie mit der rechten Maustaste auf den Knoten **Volumes**, und wählen Sie **Ansicht** und dann **Spalten hinzufügen/entfernen** aus.)

| Ergebnisspalte | BESCHREIBUNG |
|:--- |:--- |
| Name |Die Spalte **Name** enthält den Namen der Volumegruppe. |
| Application |Die Spalte **Anwendungen** zeigt die Anzahl der VSS-Writer an, die zurzeit auf dem Windows-Host installiert sind und ausgeführt werden. |
| Aktiviert |Die Spalte **Ausgewählt** zeigt die Anzahl der Volumes in der Volumegruppe an. Eine Null (0) gibt an, dass den Volumes in der Volumegruppe keine Anwendung zugeordnet ist. |
| Importiert |Die Spalte **Importiert** zeigt die Anzahl der importierten Volumes an. Bei Festlegung auf **True** gibt diese Spalte an, dass eine Volumegruppe aus dem Azure-Portal importiert und nicht im StorSimple Snapshot Manager erstellt wurde. |

> [!NOTE]
> StorSimple Snapshot Manager-Volumegruppen werden ebenfalls auf der Registerkarte **Sicherungsrichtlinien** im Azure-Portal angezeigt.
> 
> 

## <a name="create-a-volume-group"></a>Erstellen einer Volumegruppe
Gehen Sie folgendermaßen vor, um eine neue Vorlumegruppe zu erstellen.

#### <a name="to-create-a-volume-group"></a>So erstellen Sie eine Volumegruppe
1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten.
2. Klicken Sie im Fensterbereich **Bereich** mit der rechten Maustaste auf **Volumegruppen**, und klicken Sie dann auf **Volumegruppe** erstellen.
   
    ![Volumegruppe erstellen](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Create_volume_group.png)
   
    Das Dialogfeld **Volumegruppe erstellen** wird angezeigt.
   
    ![Dialogfeld "Create Volume Group"](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_CreateVolumeGroup_dialog.png)
3. Geben Sie Folgendes ein:
   
   1. Geben Sie im Feld **Name** einen eindeutigen Namen für die neue Volumegruppe ein.
   2. Wählen Sie im Feld **Anwendungen** die Anwendungen aus, die den Volumes, die Sie der Volumegruppe hinzufügen werden, zugeordnet sind.
      
       Das Feld **Anwendungen** enthält nur die Anwendungen, die StorSimple-Volumes verwenden und für die VSS-Writer aktiviert sind. Ein VSS-Writer wird nur aktiviert, wenn alle vom Writer erkannten Volumes StorSimple-Volumes sind. Wenn das Feld "Anwendungen" leer ist, sind keine Anwendungen installiert, die Azure StorSimple-Volumes und VSS-Writer verwenden. (Zurzeit unterstützt Azure StorSimple Microsoft Exchange und SQL Server.) Weitere Informationen zu VSS-Writern finden Sie unter [Integration in den Windows-Volumeschattenkopie-Dienst](storsimple-what-is-snapshot-manager.md#integration-with-windows-volume-shadow-copy-service).
      
       Wenn Sie eine Anwendung auswählen, werden alle zugeordneten Volumes automatisch ausgewählt. Wenn Sie umgekehrt Volumes auswählen, die einer bestimmten Anwendung zugeordnet sind, wird die Anwendung automatisch im Feld **Anwendungen** ausgewählt. 
   3. Wählen Sie im Feld **Volumes** die StorSimple-Volumes aus, die der Volumegruppe hinzugefügt werden sollen. 
      
      * Sie können Volumes mit einer oder mehreren Partitionen hinzufügen. (Volumes mit mehreren Partitionen können dynamische Datenträger oder Basisdatenträger mit mehreren Partitionen sein.) Ein Volume, das mehrere Partitionen enthält, wird als einzelne Einheit behandelt. Wenn Sie also nur eine der Partitionen einer Volumegruppe hinzufügen, werden alle anderen Partitionen automatisch auch dieser Volumegruppe hinzugefügt. Auch nach dem Hinzufügen eines Volumes mit mehreren Partitionen zu einer Volumegruppe wird das aus mehreren Partitionen bestehende Volume als eine Einheit behandelt.
      * Sie können leere Volumegruppen erstellen, indem Sie keine Volumes zuweisen. 
      * Fassen Sie keine freigegebenen Clustervolumes (CSV) und nicht freigegebenen Clustervolumes in derselben Volumegruppe zusammen. Der StorSimple Snapshot Manager unterstützt nicht das Zusammenfassen von freigegebenen Clustervolumes und nicht freigegebenen Clustervolumes in derselben Momentaufnahme.
4. Klicken Sie zum Speichern der Volumegruppe auf **OK** .

## <a name="back-up-a-volume-group"></a>Sichern einer Volumegruppe
Gehen Sie folgendermaßen vor, um eine Vorlumegruppe zu sichern.

#### <a name="to-back-up-a-volume-group"></a>So sichern Sie eine Volumegruppe
1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten.
2. Erweitern Sie im Fensterbereich **Bereich** den Knoten **Volumegruppen**, klicken Sie mit der rechten Maustaste auf einen Volumegruppennamen, und klicken Sie dann auf **Sicherung anlegen**.
   
    ![Sofortiges Sichern einer Volumegruppe](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_Take_backup.png)
3. Wählen Sie im Dialogfeld **Sicherung anlegen** entweder **Lokale Momentaufnahme** oder **Cloudmomentaufnahme** aus, und klicken Sie dann auf **Erstellen**.
   
    ![Dialogfeld "Sicherung anlegen"](./media/storsimple-snapshot-manager-manage-volume-groups/HCS_SSM_TakeBackup_dialog.png)
4. Um die Ausführung der Sicherung zu bestätigen, erweitern Sie den Knoten **Aufträge**, und klicken Sie dann auf **Wird ausgeführt**. Die Sicherung sollte aufgeführt sein.
5. Um die abgeschlossene Momentaufnahme anzuzeigen, erweitern Sie den Knoten **Sicherungskatalog** und dann den Volumegruppennamen, und klicken Sie dann auf **Lokale Momentaufnahme** oder **Cloudmomentaufnahme**. Die Sicherung wird aufgeführt, wenn sie erfolgreich abgeschlossen wurde.

## <a name="edit-a-volume-group"></a>Bearbeiten einer Volumegruppe
Gehen Sie folgendermaßen vor, um eine Vorlumegruppe zu bearbeiten.

#### <a name="to-edit-a-volume-group"></a>So bearbeiten Sie eine Volumegruppe
1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten.
2. Erweitern Sie im Fensterbereich **Bereich** den Knoten **Volumegruppen**, klicken Sie mit der rechten Maustaste auf einen Volumegruppennamen, und klicken Sie dann auf **Bearbeiten**.
3. Das Dialogfeld Volumegruppe erstellen wird angezeigt. Sie können die Einträge für **Name**, **Anwendungen** und **Volumes** ändern.
4. Klicken Sie auf **OK** , um die Änderungen zu speichern.

## <a name="delete-a-volume-group"></a>Löschen einer Volumegruppe
Gehen Sie folgendermaßen vor, um eine Volumegruppe zu löschen. 

> [!WARNING]
> Dadurch werden auch alle Sicherungen der Volumegruppe gelöscht.
> 
> 

#### <a name="to-delete-a-volume-group"></a>So löschen Sie eine Volumegruppe
1. Klicken Sie auf das Desktopsymbol, um den StorSimple Snapshot Manager zu starten.
2. Erweitern Sie im Fensterbereich **Bereich** den Knoten **Volumegruppen**, klicken Sie mit der rechten Maustaste auf einen Volumegruppennamen, und klicken Sie dann auf **Löschen**.
3. Das Dialogfeld **Volumegruppe löschen** wird angezeigt. Geben Sie in das Textfeld den Text **Bestätigen** ein, und klicken Sie dann auf **OK**.
   
    Die gelöschte Volumegruppe wird aus der Liste im **Ergebnisbereich** entfernt, und alle Sicherungen, die dieser Volumegruppe zugeordnet sind, werden aus dem Sicherungskatalog gelöscht.

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über das [Verwenden von StorSimple Snapshot Manager zum Verwalten der StorSimple-Lösung](storsimple-snapshot-manager-admin.md).
* Weitere Informationen zum [Verwenden des StorSimple Snapshot Managers zum Erstellen und Verwalten von Sicherungsrichtlinien](storsimple-snapshot-manager-manage-backup-policies.md).

