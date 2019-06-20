---
title: 'Azure Backup: Wiederherstellen des Systemstatus auf einem Windows Server-Computer'
description: Ausführliche Anleitung zum Wiederherstellen des Windows Server-Systemstatus aus einer Sicherung in Azure.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 8/18/2017
ms.author: saurse
ms.openlocfilehash: 6619611bee96089e465feb6f50d38caeada06dd9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65472502"
---
# <a name="restore-system-state-to-windows-server"></a>Wiederherstellen des Systemstatus von Windows Server

In diesem Artikel wird erläutert, wie Sicherungen des Windows Server-Systemstatus aus einem Azure Recovery Services-Tresor wiederhergestellt werden. Um den Systemstatus wiederherstellen zu können, benötigen Sie eine Sicherung des Systemstatus (erstellt mithilfe der Anweisungen in [Sichern des Systemstatus](backup-azure-system-state.md#back-up-windows-server-system-state)). Stellen Sie außerdem sicher, dass die [neueste Version des Microsoft Azure Recovery Services-Agents (MARS)](https://aka.ms/azurebackup_agent) installiert ist. Das Wiederherstellen von Daten des Windows Server-Systemstatus aus einem Azure Recovery Services-Tresor ist ein zweistufiger Prozess:

1. Stellen Sie den Systemstatus als Dateien aus Azure Backup wieder her. Beim Wiederherstellen des Systemstatus als Dateien aus Azure Backup haben Sie folgende Möglichkeiten:
   * Wiederherstellen des Systemstatus auf dem gleichen Server, auf dem die Sicherungen erstellt wurden, oder
   * Stellen Sie die Systemstatusdatei auf einem anderen Server wieder her.

2. Wenden Sie die wiederhergestellten Systemstatusdateien auf Windows Server an.


## <a name="recover-system-state-files-to-the-same-server"></a>Wiederherstellen der Systemstatusdateien auf dem gleichen Server
Die folgenden Schritte erläutern das Ausführen eines Rollbacks Ihrer Windows Server-Konfiguration zu einem früheren Zustand. Ein Rollback Ihrer Serverkonfiguration zu einem bekannten, stabilen Zustand kann sehr hilfreich sein. Mit den folgenden Schritten stellen Sie den Systemstatus des Servers aus einem Recovery Services-Tresor wieder her.

1. Öffnen Sie das Snap-In **Microsoft Azure Backup**. Falls Sie nicht wissen, wo das Snap-In installiert wurde, können Sie den für **Microsoft Azure Backup** verwendeten Computer oder Server durchsuchen.

    Die Desktop-App sollten in den Suchergebnissen angezeigt werden.

2. Klicken Sie auf **Daten wiederherstellen**, um den Assistenten zu starten.

    ![Daten wiederherstellen](./media/backup-azure-restore-windows-server/recover.png)

3. Wählen Sie im Bereich **Erste Schritte** die Option **Dieser Server (`<server name>`)** , um die Daten auf demselben Server oder Computer wiederherzustellen, und klicken Sie auf **Weiter**.

    ![Auswählen der Option „Dieser Server“ zum Wiederherstellen der Daten auf demselben Computer](./media/backup-azure-restore-system-state/samemachine.png)

4. Wählen Sie im Bereich **Wiederherstellungsmodus auswählen** die Option **Systemstatus** aus, und klicken Sie auf **Weiter**.

    ![Dateien durchsuchen](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. Wählen Sie im Kalender im Bereich **Volume und Datum auswählen** einen Wiederherstellungspunkt aus.

    Sie können den Zustand eines beliebigen Wiederherstellungszeitpunkts wiederherstellen. Wenn Datumsangaben in **Fettdruck** angezeigt werden, ist dies ein Hinweis darauf, dass mindestens ein Wiederherstellungszeitpunkt verfügbar ist. Falls mehrere Wiederherstellungspunkte verfügbar sind, müssen Sie nach dem Auswählen eines Datums im Dropdownmenü **Uhrzeit** den jeweiligen Wiederherstellungspunkt auswählen.

    ![Volume und Datum](./media/backup-azure-restore-system-state/select-date.png)

6. Klicken Sie auf **Weiter**, nachdem Sie den Wiederherstellungspunkt ausgewählt haben, der wiederhergestellt werden soll.

    Azure Backup stellt den lokalen Wiederherstellungspunkt bereit und verwendet ihn als Wiederherstellungsvolume.

7. Geben Sie im nächsten Bereich das Ziel für die wiederhergestellten Systemstatusdateien an, und klicken Sie auf **Durchsuchen**, um Windows-Explorer zu öffnen und die gewünschten Dateien und Ordner zu suchen. Die Option **Kopien erstellen, sodass Sie über beide Versionen verfüge** erstellt Kopien der einzelnen Dateien in einem vorhandenen Systemstatus-Dateiarchiv, anstatt eine Kopie des gesamten Systemstatusarchivs zu erstellen.

    ![Wiederherstellungsoptionen](./media/backup-azure-restore-system-state/recover-as-files.png)

8. Überprüfen Sie die Details der Wiederherstellung im Bereich **Bestätigung**, und klicken Sie auf **Wiederherstellen**.

   ![Auf „Wiederherstellen“ klicken, um die Wiederherstellungsaktion zu bestätigen](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. Kopieren Sie das Verzeichnis *WindowsImageBackup* am Wiederherstellungsziel auf ein weniger wichtiges Volume des Servers. Normalerweise ist das Windows-Betriebssystemvolume das wichtigste Volume.

10. Sobald die Wiederherstellung erfolgreich ist, führen Sie die Schritte im Abschnitt [Anwenden der wiederhergestellten Systemstatusdateien auf Windows Server](backup-azure-restore-system-state.md) aus, um den Wiederherstellungsprozess für den Systemstatus abzuschließen.

## <a name="recover-system-state-files-to-an-alternate-server"></a>Wiederherstellen von Systemstatusdateien auf einem anderen Server

Wenn Windows Server beschädigt ist oder darauf nicht zugegriffen werden kann und Sie das System durch das Wiederherstellen des Systemstatus von Windows Server wieder in einen stabilen Zustand bringen möchten, können Sie auf einem beschädigten System den Systemstatus von einem anderen Server wiederherstellen. Gehen Sie anhand der folgenden Schritte vor, um den Systemstatus auf einem anderen Server wiederherzustellen.  

In diesen Schritten wird folgende Terminologie verwendet:

- *Quellcomputer* – Der ursprüngliche Computer, von dem die Sicherung erstellt wurde und der zurzeit nicht verfügbar ist.
- *Zielcomputer* – Der Computer, auf dem die Daten wiederhergestellt werden.
- *Beispieltresor* – Der Recovery Services-Tresor, bei dem der *Quellcomputer* und der *Zielcomputer* registriert sind. <br/>

> [!NOTE]
> Sicherungen von einem Computer können nicht auf einem Computer wiederhergestellt werden, auf dem eine frühere Version des Betriebssystems ausgeführt wird. Beispielsweise können Sicherungen von einem Windows Server 2016-Computer nicht unter Windows Server 2012 R2 wiederhergestellt werden. In umgekehrter Richtung ist dies jedoch möglich. Sie können Sicherungen von Windows Server 2012 R2 zum Wiederherstellen von Windows Server 2016 verwenden.
>

1. Öffnen Sie das Snap-In **Microsoft Azure Backup** auf dem *Zielcomputer*.
2. Stellen Sie sicher, dass der *Zielcomputer* und der *Quellcomputer* bei dem gleichen Recovery Services-Tresor registriert sind.
3. Klicken Sie auf **Daten wiederherstellen** , um den Workflow zu initiieren.
4. Wählen Sie **Anderer Server**

    ![Anderer Server](./media/backup-azure-restore-system-state/anotherserver.png)

5. Geben Sie die Datei mit den Tresoranmeldeinformationen an, die dem *Beispieltresor*entspricht. Wenn die Datei mit den Tresoranmeldeinformationen ungültig (oder abgelaufen) ist, laden Sie eine neue Anmeldeinformationsdatei für den Tresor aus dem *Beispieltresor* im Azure-Portal herunter. Sobald die Anmeldeinformationsdatei für den Tresor bereitgestellt wurde, wird der Recovery Services-Tresor angezeigt, der der Anmeldeinformationsdatei für den Tresor zugeordnet ist.

6. Wählen Sie im Bereich „Sicherungsserver auswählen“ in der Liste mit den angezeigten Computern den *Quellcomputer* aus.
7. Wählen Sie im Bereich „Wiederherstellungsmodus auswählen“ die Option **Systemstatus** aus, und klicken Sie auf **Weiter**.

    ![Suchen,](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. Wählen Sie im Kalender im Bereich **Volume und Datum auswählen** einen Wiederherstellungspunkt aus. Sie können den Zustand eines beliebigen Wiederherstellungszeitpunkts wiederherstellen. Wenn Datumsangaben in **Fettdruck** angezeigt werden, ist dies ein Hinweis darauf, dass mindestens ein Wiederherstellungszeitpunkt verfügbar ist. Falls mehrere Wiederherstellungspunkte verfügbar sind, müssen Sie nach dem Auswählen eines Datums im Dropdownmenü **Uhrzeit** den jeweiligen Wiederherstellungspunkt auswählen.

    ![Elemente suchen](./media/backup-azure-restore-system-state/select-date.png)

9. Klicken Sie auf **Weiter**, nachdem Sie den Wiederherstellungspunkt ausgewählt haben, der wiederhergestellt werden soll.

10. Geben Sie im Bereich **Modus für die Systemstatuswiederherstellung auswählen** das Ziel für die Wiederherstellung der Systemstatusdateien an, und klicken Sie dann auf **Weiter**.

    ![Verschlüsselung](./media/backup-azure-restore-system-state/recover-as-files.png)

    Die Option **Kopien erstellen, sodass Sie über beide Versionen verfüge** erstellt Kopien der einzelnen Dateien in einem vorhandenen Systemstatus-Dateiarchiv, anstatt eine Kopie des gesamten Systemstatusarchivs zu erstellen.

11. Überprüfen Sie die Details der Wiederherstellung im Bereich „Bestätigung“, und klicken Sie auf **Wiederherstellen**.

    ![Auf die Schaltfläche „Wiederherstellen“ klicken, um den Wiederherstellungsprozess zu bestätigen](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. Kopieren Sie das Verzeichnis *WindowsImageBackup* auf ein weniger wichtiges Volume des Servers (z.B. „D:\)“. Normalerweise ist das Windows-Betriebssystemvolume das wichtigste Volume.

13. Um den Wiederherstellungsprozess abzuschließen, befolgen Sie die Schritte im folgenden Abschnitt zum [Anwenden der wiederhergestellten Systemstatusdateien auf Windows Server](#apply-restored-system-state-on-a-windows-server).




## <a name="apply-restored-system-state-on-a-windows-server"></a>Anwenden der wiederhergestellten Systemstatusdateien auf Windows Server

Nachdem Sie mit dem Azure Recovery Services-Agent den Systemstatus als Dateien wiederhergestellt haben, wenden Sie den wiederhergestellten Systemstatus mithilfe des Hilfsprogramms Windows Server-Sicherung auf Windows Server an. Das Hilfsprogramm Windows Server-Sicherung ist bereits auf dem Server verfügbar. Die folgenden Schritte erläutern die Anwendung des wiederhergestellten Systemstatus.

1. Verwenden Sie die folgenden Befehle, um den Server im *Reparaturmodus für Verzeichnisdienste* neu zu starten. Führen Sie an einer Eingabeaufforderung mit erhöhten Rechten Folgendes durch:

    ```
    PS C:\> Bcdedit /set safeboot dsrepair
    PS C:\> Shutdown /r /t 0
    ```

2. Öffnen Sie nach dem Neustart das Snap-In Windows Server-Sicherung. Falls Sie nicht wissen, wo das Snap-In installiert wurde, können Sie den für **Windows Server-Sicherung** verwendeten Computer oder Server durchsuchen.

    Die Desktop-App wird in den Suchergebnissen angezeigt.

3. Wählen Sie im Snap-In **Lokale Sicherung** aus.

    ![„Lokale Sicherung“ für die Wiederherstellung auswählen](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

4. Klicken Sie in der Konsole „Lokale Sicherung“ im **Aktionsbereich** auf **Wiederherstellen**, um den Wiederherstellungs-Assistenten zu öffnen.

5. Wählen Sie die Option **Eine an einem anderen Speicherort gespeicherte Sicherung** aus, und klicken Sie auf **Weiter**.

   ![Wiederherstellung auf einem anderen Server auswählen](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

6. Wenn Sie den Speicherort angeben, wählen Sie **Freigegebener Remoteordner** aus, wenn die Sicherung des Systemstatus auf einem anderen Server wiederhergestellt wurde. Wenn der Systemstatus lokal wiederhergestellt wurde, wählen Sie **Lokale Datenträger** aus.

    ![Wiederherstellung vom lokalen oder einem anderen Server auswählen](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

7. Geben Sie den Pfad zum Verzeichnis *WindowsImageBackup* ein, oder wählen Sie das lokale Laufwerk mit diesem Verzeichnis aus (z.B. „D:\WindowsImageBackup“), das bei der Wiederherstellung der Systemstatusdateien mithilfe des Azure Recovery Services-Agents wiederhergestellt wurde, und klicken Sie auf **Weiter**.

    ![Pfad zur freigegebenen Datei](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

8. Wählen Sie die Systemstatusversion aus, die Sie wiederherstellen möchten, und klicken Sie auf **Weiter**.

9. Wählen Sie im Bereich „Wiederherstellungsmodus auswählen“ die Option **Systemstatus** aus, und klicken Sie auf **Weiter**.

10. Wählen Sie als Speicherort für die Systemstatuswiederherstellung **Ursprünglicher Speicherort** aus, und klicken Sie auf **Weiter**.

11. Überprüfen Sie die Bestätigungsdetails und die Neustarteinstellungen, und klicken Sie dann auf **Wiederherstellen**, um die wiederhergestellten Systemstatusdateien anzuwenden.

    ![Wiederherstellung der Systemstatusdateien starten](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

## <a name="special-considerations-for-system-state-recovery-on-active-directory-server"></a>Besondere Überlegungen bei der Wiederherstellung des Systemstatus auf Active Directory-Servern

Sicherungen des Systemstatus umfassen auch Active Directory-Daten. Anhand der folgenden Schritte können Sie Active Directory Domain Services (AD DS) vom aktuellen Zustand in einen früheren Zustand wiederherstellen.

1. Starten Sie den Domänencontroller im Modus „Verzeichnisdienstwiederherstellung“ (Directory Services Restore Mode, DSRM) neu.
2. Führen Sie die [hier](https://technet.microsoft.com/library/cc794755(v=ws.10).aspx) beschriebenen Schritte durch, um AD DS mithilfe von Cmdlets für die Windows Server-Sicherung wiederherzustellen.


## <a name="troubleshoot-failed-system-state-restore"></a>Wiederherstellen nach einer nicht erfolgreichen Problembehandlung des Systemstatus

Wenn der vorherige Vorgang der Anwendung des Systemstatus nicht erfolgreich abgeschlossen wurde, verwenden Sie die Windows-Wiederherstellungsumgebung (Windows Recovery Environment, Windows RE), um Windows Server wiederherzustellen. In den folgenden Schritten wird die Wiederherstellung mit Win RE erläutert. Verwenden Sie diese Option nur, wenn Windows Server nach einer Wiederherstellung des Systemstatus nicht normal gestartet wird. Beim folgenden Prozess werden nicht zum System gehörende Daten gelöscht. Seien Sie deshalb äußerst vorsichtig.

1. Starten Sie Windows Server mit der Windows-Wiederherstellungsumgebung (Windows RE).

2. Wählen Sie aus den drei verfügbaren Optionen die Problembehandlung aus.

    ![Startmenü](./media/backup-azure-restore-system-state/winre-1.png)

3. Wählen Sie auf dem Bildschirm **Erweiterte Optionen** die Option **Eingabeaufforderung** aus, und geben Sie den Benutzernamen und das Kennwort des Serveradministrators an.

   ![Startmenü](./media/backup-azure-restore-system-state/winre-2.png)

4. Geben Sie den Benutzernamen und das Kennwort des Serveradministrators an.

    ![Startmenü](./media/backup-azure-restore-system-state/winre-3.png)

5. Wenn Sie die Eingabeaufforderung im Administratormodus öffnen, führen Sie folgenden Befehl aus, um die Versionen der Systemstatussicherung abzurufen.

    ```
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```
    ![Versionen der Systemstatussicherung abrufen](./media/backup-azure-restore-system-state/winre-4.png)

6. Führen Sie den folgenden Befehl aus, um alle in der Sicherung verfügbaren Volumes abzurufen.

    ```
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![Versionen der Systemstatussicherung abrufen](./media/backup-azure-restore-system-state/winre-5.png)

7. Der folgende Befehl stellt alle Volumes, die Teil der Systemstatussicherung sind, wieder her. Beachten Sie, dass bei diesem Schritt nur die wichtigen Volumes wiederhergestellt werden, die Teil des Systemstatus sind. Alle nicht zum System gehörenden Daten werden gelöscht.

    ```
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```
     ![Versionen der Systemstatussicherung abrufen](./media/backup-azure-restore-system-state/winre-6.png)



## <a name="next-steps"></a>Nächste Schritte
* Nachdem Sie nun Ihre Dateien und Ordner wiederhergestellt haben, können Sie [Ihre Sicherungen verwalten](backup-azure-manage-windows-server.md).
