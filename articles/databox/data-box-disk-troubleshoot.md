---
title: Problembehandlung des Azure Data Box-Datenträgers | Microsoft Docs
description: Beschreibt, wie Sie Probleme mit dem Azure Data Box-Datenträger beheben können.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/2/2019
ms.author: alkohli
ms.openlocfilehash: f9d01b56da2650be395878ce07e4aae73495061f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64939644"
---
# <a name="troubleshoot-issues-in-azure-data-box-disk"></a>Behandeln von Problemen mit Azure Data Box Disk

Dieser Artikel gilt für Microsoft Azure Data Box Disk. Er beschreibt die Workflows zum Beheben von Problemen, die beim Bereitstellen dieser Lösung auftreten können. 

Dieser Artikel enthält die folgenden Abschnitte:

- Herunterladen von Diagnoseprotokollen
- Abfragen von Aktivitätsprotokollen
- Fehler des Data Box Disk-Tools zum Entsperren
- Fehler des Data Box Disk-Tools zum Aufteilen/Kopieren

## <a name="download-diagnostic-logs"></a>Herunterladen von Diagnoseprotokollen

Wenn während des Datenkopiervorgangs Fehler auftreten, zeigt das Portal einen Pfad zu dem Ordner an, in dem sich die Diagnoseprotokolle befinden. 

Folgende Diagnoseprotokolle sind möglich:
- Fehlerprotokolle
- Ausführliche Protokolle  

Um zum Pfad zum Kopieren der Protokolle zu navigieren, navigieren Sie zu dem Speicherkonto, das Ihrer Data Box-Bestellung zugeordnet ist. 

1.  Navigieren Sie zu **Allgemein > Bestelldetails**, und notieren Sie sich das Speicherkonto, das Ihrer Bestellung zugeordnet ist.
 

2.  Navigieren Sie zu **Alle Ressourcen**, und suchen Sie nach dem Speicherkonto, das Sie im vorherigen Schritt identifiziert haben. Wählen Sie das Speicherkonto aus, und klicken Sie darauf.

    ![Kopieren von Protokollen 1](./media/data-box-disk-troubleshoot/data-box-disk-copy-logs1.png)

3.  Navigieren Sie zu **Blob-Dienst > Blobs durchsuchen**, und suchen Sie nach dem Blob, das dem Speicherkonto entspricht. Navigieren Sie zu **diagnosticslogcontainer > waies**. 

    ![Kopieren von Protokollen 2](./media/data-box-disk-troubleshoot/data-box-disk-copy-logs2.png)

    Sowohl die Fehlerprotokolle als auch die ausführlichen Protokolle sollten zum Kopieren von Daten angezeigt werden. Wählen Sie jede Datei aus, und klicken Sie darauf, und laden Sie dann eine lokale Kopie herunter.

## <a name="query-activity-logs"></a>Abfragen von Aktivitätsprotokollen

Verwenden Sie die Aktivitätsprotokolle zum Ermitteln eines Fehlers oder zum Nachverfolgen, welche Änderungen an einer Ressource ein Benutzer in Ihrer Organisation vorgenommen hat. Mithilfe von Aktivitätsprotokollen können Sie Folgendes ermitteln:

- Welche Vorgänge in den Ressourcen in Ihrem Abonnement ausgeführt wurden.
- Der Benutzer oder das System, von dem der Vorgang initiiert wurde.
- Wann der Vorgang ausgeführt wurde.
- Der Status des Vorgangs.
- Werte anderer Eigenschaften, mit denen Sie den Vorgang ggf. untersuchen können.

Das Aktivitätsprotokoll enthält alle Schreibvorgänge (z.B. PUT, POST, DELETE) für Ihre Ressourcen, nicht aber die Lesevorgänge (z.B. GET).

Aktivitätsprotokolle werden 90 Tage lang aufbewahrt. Sie können beliebige Datumsbereiche abfragen, das Startdatum darf jedoch maximal 90 Tage zurückliegen. Sie können auch mit einer der integrierten Abfragen in Insights filtern. Klicken Sie z.B. auf „Fehler“, und klicken Sie dann auf bestimmte Fehler, um die Grundursache zu verstehen.

## <a name="data-box-disk-unlock-tool-errors"></a>Fehler des Data Box Disk-Tools zum Entsperren


| Fehlermeldung/Toolverhalten      | Empfehlungen                                                                                               |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| Keine<br><br>Das Tool zum Entsperren von Data Box-Datenträgern stürzt ab.                                                                            | BitLocker ist nicht installiert. Stellen Sie sicher, dass auf dem Hostcomputer, der das Tool zum Entsperren des Data Box-Datenträgers ausführt, BitLocker installiert ist.                                                                            |
| Die aktuelle .NET Framework-Version wird nicht unterstützt. Die unterstützten Versionen sind 4.5 oder höher.<br><br>Das Tool wird mit einer Meldung beendet.  | .NET 4.5 ist nicht installiert. Installieren Sie .NET 4.5 oder höher auf dem Hostcomputer, der das Tool zum Entsperren des Data Box-Datenträgers ausführt.                                                                            |
| Volumes konnten nicht entsperrt oder überprüft werden. Wenden Sie sich an den Microsoft Support.  <br><br>Das Tool entsperrt bzw. überprüft keine gesperrten Laufwerke. | Das Tool konnte keines der entsperrten Laufwerke mit dem angegebenen Hauptschlüssel entsperren. Erkundigen Sie sich beim Support von Microsoft nach den weiteren Schritten.                                                |
| Folgende Volumes sind entsperrt und überprüft. <br>Volumelaufwerkbuchstaben: E:<br>Es konnten keine Volumes mit den folgenden Hauptschlüsseln entsperrt werden: werwerqomnf, qwerwerqwdfda <br><br>Das Tool entsperrt einige Laufwerke und zeigt die erfolgreichen und fehlerhaften Laufwerkbuchstaben an.| Teilweise erfolgreich. Einige der Laufwerke konnten mit dem angegebenen Hauptschlüssel nicht entsperrt werden. Erkundigen Sie sich beim Support von Microsoft nach den weiteren Schritten. |
| Es wurden keine gesperrten Laufwerke gefunden. Stellen Sie sicher, dass der von Microsoft erhaltene Datenträger ordnungsgemäß verbunden ist und einen gesperrten Zustand aufweist.          | Das Tool findet keine gesperrten Laufwerke. Die Laufwerke sind bereits entsperrt, oder sie werden nicht erkannt. Stellen Sie sicher, dass die Laufwerke verbunden und gesperrt sind.                                                           |
| Schwerwiegender Fehler: Ungültiger Parameter<br>Parametername: invalid_arg<br>SYNTAX:<br>DataBoxDiskUnlock /PassKeys:<Durch_Semicolons_getrennte_Liste_der_Hauptschlüssel><br><br>Beispiel: DataBoxDiskUnlock /PassKeys:Hauptschlüssel1;Hauptschlüssel2;Hauptschlüssel3<br>Beispiel: DataBoxDiskUnlock /SystemCheck<br>Beispiel: DataBoxDiskUnlock /Help<br><br>/PassKeys:       Abrufen dieses Schlüssels aus dem Azure Data Box Disk-Auftrag. Der Hauptschlüssel entsperrt Ihre Datenträger.<br>/Help:           Diese Option bietet Hilfe und Beispiele für die Cmdlet-Verwendung.<br>/SystemCheck:    Diese Option überprüft, ob Ihr System die Anforderungen zum Ausführen des Tools erfüllt.<br><br>Drücken Sie zum Beenden eine beliebige Taste. | Ungültige Parametereingabe. Der einzigen zulässigen Parameter sind /SystemCheck, /PassKey und /Help.                                                                            |

## <a name="data-box-disk-split-copy-tool-errors"></a>Fehler des Data Box Disk-Tools zum Aufteilen/Kopieren

|Fehlermeldung/Warnungen  |Empfehlungen |
|---------|---------|
|[Info] Retrieving BitLocker password for volume: m <br>[Error] Exception caught while retrieving BitLocker key for volume m:<br> Sequence contains no elements. ([Information] BitLocker-Kennwort für folgendes Volume wird abgerufen: M.[Fehler] Ausnahme beim Abrufen des BitLocker-Schlüssels für das Volume M abgefangen:Die Sequenz enthält keine Elemente.)|Dieser Fehler tritt auf, wenn das Data Box Disk-Ziel offline ist. <br> Verwenden Sie das Tool `diskmgmt.msc`, um Datenträger online zu schalten.|
|[Error] Exception thrown: WMI operation failed:<br> Method=UnlockWithNumericalPassword, ReturnValue=2150694965, <br>Win32Message=The format of the recovery password provided is invalid. <br>BitLocker recovery passwords are 48 digits. <br>Verify that the recovery password is in the correct format and then try again.([Fehler] Eine Ausnahme wurde ausgelöst: Fehler beim WMI-Vorgang: Methode: UnlockWithNumericalPassword, Rückgabewert: 2150694965, Win32-Meldung: Das Format des angegebenen Wiederherstellungskennworts ist ungültig. BitLocker-Wiederherstellungskennwörter müssen 48-stellig sein. Vergewissern Sie sich, dass das Wiederherstellungskennwort das richtige Format hat, und wiederholen Sie den Vorgang.)|Entsperren Sie die Datenträger zunächst mit dem Data Box Disk-Tool zum Entsperren, und führen Sie den Befehl anschließend erneut aus. Weitere Informationen finden Sie unter: <li> [Entsperren von Datenträgern auf Windows-Client](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) </li><li> [Entsperren von Datenträgern auf Linux-Client](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client) </li>|
|[Error] Exception thrown: A DriveManifest.xml file exists on the target drive. <br> This indicates the target drive may have been prepared with a different journal file. <br>To add more data to the same drive, use the previous journal file. To delete existing data and reuse target drive for a new import job, delete the DriveManifest.xml on the drive. Rerun this command with a new journal file. ([Fehler] Eine Ausnahme wurde ausgelöst: Auf dem Ziellaufwerk befindet sich eine Datei vom Typ „DriveManifest.xml“. Das Ziellaufwerk wurde möglicherweise mit einer anderen Journaldatei vorbereitet. Wenn Sie dem gleichen Laufwerk weitere Daten hinzufügen möchten, verwenden Sie die vorherige Journaldatei. Wenn Sie die vorhandenen Daten löschen und das Ziellaufwerk für einen neuen Importauftrag verwenden möchten, löschen Sie die Datei „DriveManifest.xml“ auf dem Laufwerk, und führen Sie diesen Befehl mit einer neuen Journaldatei aus.)| Dieser Fehler tritt auf, wenn Sie versuchen, die gleiche Gruppe von Laufwerken für mehrere Importsitzungen zu verwenden. <br> Verwenden Sie pro Aufteilungs-/Kopiersitzung immer nur eine Gruppe von Laufwerken.|
|[Error] Exception thrown: CopySessionId importdata-sept-test-1 refers to a previous copy session and cannot be reused for a new copy session. ([Fehler] Eine Ausnahme wurde ausgelöst: „CopySessionId importdata-sept-test-1“ bezieht sich auf eine vorherige Kopiersitzung und kann nicht für diese neue Kopiersitzung wiederverwendet werden.)|Dieser Fehler tritt auf, wenn Sie versuchen, den Auftragsnamen eines bereits erfolgreich abgeschlossenen Auftrags für einen neuen Auftrag zu verwenden.<br> Weisen Sie Ihrem neuen Auftrag einen eindeutigen Namen zu.|
|[Info] Destination file or directory name exceeds the NTFS length limit. ([Information] Der Name der Zieldatei oder des Zielverzeichnisses übersteigt die NTFS-Längenbeschränkung.) |Diese Meldung wird zurückgegeben, wenn die Zieldatei aufgrund eines langen Dateipfads umbenannt wurde.<br> Ändern Sie die Dispositionsoption in der Datei `config.json`, um dieses Verhalten zu steuern.|
|[Error] Exception thrown: Bad JSON escape sequence. ([Fehler] Eine Ausnahme wurde ausgelöst: fehlerhafte JSON-Escapesequenz.) |Diese Meldung wird zurückgegeben, wenn das Format von „config.json“ ungültig ist. <br> Überprüfen Sie die Datei `config.json` vor dem Speichern mithilfe von [JSONLint](https://jsonlint.com/).|

## <a name="deployment-issues-for-linux"></a>Bereitstellungsprobleme unter Linux

In diesem Abschnitt werden einige der wichtigsten Probleme bei der Bereitstellung von Data Box Disk unter Verwendung eines Linux-Clients zum Kopieren von Daten beschrieben.

### <a name="issue-drive-getting-mounted-as-read-only"></a>Problem: Das Laufwerk wird schreibgeschützt eingebunden.
 
**Ursache** 

Dies kann auf ein fehlerhaftes Dateisystem zurückzuführen sein. 

Das erneute Einbinden eines Laufwerks mit Lese-/Schreibzugriff funktioniert nicht mit Data Box Disk-Datenträgern. Dieses Szenario wird mit Laufwerken, die mit dislocker entschlüsselt werden, nicht unterstützt. Möglicherweise haben Sie das Gerät mithilfe des folgenden Befehls erfolgreich erneut eingebunden:

    `# mount -o remount, rw /mnt/DataBoxDisk/mountVol1`

Das erneute Einbinden war zwar erfolgreich, die Daten werden jedoch nicht beibehalten.

**Lösung**

Führen Sie die folgenden Schritte auf Ihrem Linux-System durch:

1. Installieren Sie das `ntfsprogs`-Paket für das Hilfsprogramm ntfsfix.
2. Heben Sie die Einbindung der Bereitstellungspunkte für das Laufwerk mit dem Tool zum Entsperren auf. Die Anzahl der Bereitstellungspunkte variiert bei Laufwerken.

    ```
    unmount /mnt/DataBoxDisk/mountVol1
    ```

3. Führen Sie `ntfsfix` im entsprechenden Pfad aus. Die hervorgehobene Anzahl sollte mit Schritt 2 übereinstimmen.

    ```
    ntfsfix /mnt/DataBoxDisk/bitlockerVol1/dislocker-file
    ```

4. Führen Sie den folgenden Befehl aus, um die Metadaten für den Ruhezustand zu entfernen, die möglicherweise Ursache des Bereitstellungsproblems sind.

    ```
    ntfs-3g -o remove_hiberfile /mnt/DataBoxDisk/bitlockerVol1/dislocker-file /mnt/DataBoxDisk/mountVol1
    ```

5. Heben Sie die Bereitstellung auf.

    ```
    ./DataBoxDiskUnlock_x86_64 /unmount
    ```

6. Führen Sie die Entsperrung und die Bereitstellung durch.
7. Testen Sie den Bereitstellungspunkt durch das Schreiben einer Datei.
8. Heben Sie die Bereitstellung auf und führen Sie sie anschließend erneut durch, um die Dateipersistenz zu überprüfen.
9. Fahren Sie mit der Datenkopie fort.
 
### <a name="issue-error-with-data-not-persisting-after-copy"></a>Problem: Fehler mit Daten, die nach dem Kopieren nicht beibehalten werden
 
**Ursache** 

Wenn Sie feststellen, dass nach dem Aufheben der Einbindung auf dem Laufwerk keine Daten mehr vorhanden sind (obwohl Daten darauf kopiert wurden), ist es möglich, dass Sie das Laufwerk mit Lese-/ Schreibzugriff eingebunden haben, nachdem es schreibgeschützt eingebunden wurde.

**Lösung**
 
Wenn dies der Fall ist, finden Sie weitere Informationen in der Lösung für Probleme durch [schreibgeschützt eingebundene Laufwerke](#issue-drive-getting-mounted-as-read-only).

Wenn dies nicht der Fall war, kopieren Sie die Protokolle aus dem Ordner, in dem sich das Data Box Disk-Tool zum Entsperren befindet, und [wenden Sie sich an den Microsoft-Support](data-box-disk-contact-microsoft-support.md).

## <a name="deployment-issues-for-windows"></a>Bereitstellungsprobleme unter Windows

In diesem Abschnitt werden einige der wichtigsten Probleme beschrieben, die bei der Bereitstellung von Data Box Disk auftreten können, wenn ein Windows-Client zum Kopieren von Daten verwendet wird.

### <a name="issue-could-not-unlock-drive-from-bitlocker"></a>Problem: Laufwerk von BitLocker konnte nicht entsperrt werden.
 
**Ursache** 

Sie haben das Kennwort im BitLocker-Dialogfeld verwendet und versuchen, den Datenträger über das BitLocker-Dialogfeld zum Entsperren von Laufwerken zu entsperren. Dies funktioniert nicht. 

**Lösung**

Um die Data Box Disk-Datenträger zu entsperren, müssen Sie das Data Box Disk-Entsperrtool verwenden und das Kennwort aus dem Azure-Portal angeben. Weitere Informationen finden Sie unter [Tutorial: Entpacken, Verbinden und Entsperren von Azure Data Box Disk](data-box-disk-deploy-set-up.md#connect-to-disks-and-get-the-passkey).
 
### <a name="issue-could-not-unlock-or-verify-some-volumes-contact-microsoft-support"></a>Problem: Einige Volumes konnten nicht entsperrt oder überprüft werden. Wenden Sie sich an den Microsoft Support.
 
**Ursache** 

Möglicherweise ist der folgende Fehler im Fehlerprotokoll aufgeführt, und Sie können einige Volumes nicht entsperren oder überprüfen.

`Exception System.IO.FileNotFoundException: Could not load file or assembly 'Microsoft.Management.Infrastructure, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies. The system cannot find the file specified.`
 
Dies deutet darauf hin, dass wahrscheinlich die geeignete Version von Windows PowerShell auf Ihrem Windows-Client fehlt.

**Lösung**

Sie können [Windows PowerShell v5.0](https://www.microsoft.com/download/details.aspx?id=54616) installieren und den Vorgang wiederholen.
 
Wenn es Ihnen weiterhin nicht möglich ist, die Volumes zu entsperren, kopieren Sie die Protokolle aus dem Ordner, in dem sich das Data Box Disk-Tool zum Entsperren befindet, und [wenden Sie sich an den Microsoft-Support](data-box-disk-contact-microsoft-support.md).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum [Verwalten des Data Box-Datenträgers über das Azure-Portal](data-box-portal-ui-admin.md).
