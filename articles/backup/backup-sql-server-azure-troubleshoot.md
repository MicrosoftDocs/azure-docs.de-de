---
title: Problembehandlung für die SQL Server-Datenbanksicherung mit Azure Backup | Microsoft-Dokumentation
description: Informationen zur Problembehandlung beim Sichern von SQL Server-Datenbanken auf virtuellen Azure-Computern mit Azure Backup
services: backup
author: anuragm
manager: shivamg
ms.service: backup
ms.topic: article
ms.date: 05/27/2019
ms.author: anuragm
ms.openlocfilehash: 8459bb451c4ff462ee816b986cafdbf776603917
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306972"
---
# <a name="troubleshoot-back-up-sql-server-on-azure"></a>Problembehandlung zum Sichern von SQL Server in Azure

Dieser Artikel enthält Informationen zur Problembehandlung zum Schutz von SQL Server-VMs in Azure.

## <a name="feature-consideration-and-limitations"></a>Funktionsaspekte und Einschränkungen

Informationen zum Anzeigen der Funktionsaspekte finden Sie im Artikel [Informationen zur SQL Server-Sicherung auf virtuellen Azure-Computern](backup-azure-sql-database.md#feature-consideration-and-limitations).

## <a name="sql-server-permissions"></a>SQL Server-Berechtigungen

Zum Konfigurieren des Schutzes für eine SQL Server-Datenbank auf einem virtuellen Computer, muss auf diesem virtuellen Computer die Erweiterung **AzureBackupWindowsWorkload** installiert sein. Wenn die Fehlermeldung **UserErrorSQLNoSysadminMembership** angezeigt wird, bedeutet das, dass Ihre SQL-Instanz nicht über die erforderlichen Berechtigungen für die Sicherung verfügt. Um diesen Fehler zu beheben, führen Sie die Schritte in [Festlegen von Berechtigungen für Nicht-Marketplace-SQL-VMs](backup-azure-sql-database.md#fix-sql-sysadmin-permissions) aus.


## <a name="backup-type-unsupported"></a>Nicht unterstützter Sicherungstyp

| Severity | BESCHREIBUNG | Mögliche Ursachen | Empfohlene Maßnahme |
|---|---|---|---|
| Warnung | Die aktuellen Einstellungen für diese Datenbank unterstützen einen bestimmten Sicherungstyp nicht, der in der zugehörigen Richtlinie enthalten ist. | <li>**Master-Datenbank**: In der Masterdatenbank kann nur eine vollständige Datenbanksicherung durchgeführt werden. Es sind weder **differenzielle** Sicherungen noch Sicherungen von **Transaktionsprotokollen** möglich. </li> <li>Datenbanken im **einfachen Wiederherstellungsmodell** erlauben nicht die Sicherung von **Transaktionsprotokollen**.</li> | Ändern Sie die Datenbankeinstellungen so, dass alle Sicherungstypen in der Richtlinie unterstützt werden. Alternativ können Sie die aktuelle Richtlinie so ändern, dass nur die unterstützten Sicherungstypen berücksichtigt werden. Andernfalls werden die nicht unterstützten Sicherungstypen bei der geplanten Sicherung übersprungen oder der Sicherungsauftrag schlägt bei einer Ad-hoc-Sicherung fehl.


## <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Fehlermeldung | Mögliche Ursachen | Empfohlene Maßnahme |
|---|---|---|
| Diese SQL-Datenbank unterstützt nicht den angeforderten Sicherungstyp. | Tritt auf, wenn das Wiederherstellungsmodell der Datenbank den angeforderten Sicherungstyp nicht zulässt. Der Fehler kann in den folgenden Situationen auftreten: <br/><ul><li>Eine Datenbank, die ein einfaches Wiederherstellungsmodell verwendet, lässt keine Protokollsicherung zu.</li><li>Die differenzielle Sicherung und die Protokollsicherung sind für eine Masterdatenbank nicht zulässig.</li></ul>Weitere Einzelheiten finden Sie in der Dokumentation [Wiederherstellungsmodelle (SQL Server)](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server). | Wenn bei der Protokollsicherung für die DB im einfachen Wiederherstellungsmodell ein Fehler auftritt, probieren Sie eine der folgenden Optionen aus:<ul><li>Wenn sich die Datenbank im einfachen Wiederherstellungsmodus befindet, deaktivieren Sie die Protokollsicherungen.</li><li>Verwenden Sie [Anzeigen oder Ändern des Wiederherstellungsmodells einer Datenbank (SQL Server)](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server), um das Wiederherstellungsmodell für die Datenbank in „Vollständig“ oder „Massenprotokolliert“ zu ändern. </li><li> Wenn Sie das Wiederherstellungsmodell nicht ändern möchten und bei der Sicherung mehrerer Datenbanken mit einer Standardrichtlinie arbeiten, die nicht geändert werden kann, ignorieren Sie den Fehler. Die vollständigen und differenziellen Sicherungen werden gemäß Zeitplan ausgeführt. Die Protokollsicherungen werden übersprungen, was in diesem Fall erwartet wird.</li></ul>Wird es sich jedoch um eine Masterdatenbank handelt, und Sie differenzielle Sicherungen oder Protokollsicherungen konfiguriert haben, verwenden Sie einen der folgenden Schritte:<ul><li>Verwenden Sie das Portal, um den Sicherungsrichtlinienzeitplan für die Masterdatenbank in „Vollständig“ zu ändern.</li><li>Wenn Sie bei der Sicherung mehrerer Datenbanken mit einer Standardrichtlinie arbeiten, die nicht geändert werden kann, ignorieren Sie den Fehler. Die vollständige Sicherung wird gemäß Zeitplan ausgeführt. Differenzielle Sicherungen oder Protokollsicherungen werden nicht ausgeführt, was in diesem Fall erwartet wird.</li></ul> |
| Der Vorgang wurde abgebrochen, da auf der gleichen Datenbank bereits ein widersprüchlicher Vorgang ausgeführt wurde. | Weitere Informationen finden Sie im [Blogeintrag zum Sichern und Wiederherstellen von Einschränkungen](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database), die gleichzeitig ausgeführt werden.| [Verwenden Sie SQL Server Management Studio (SSMS) zum Überwachen von Sicherungsaufträgen.](manage-monitor-sql-database-backup.md) Nachdem bei dem widersprüchlichen Vorgang ein Fehler aufgetreten ist, wiederholen Sie den Vorgang.|

## <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Fehlermeldung | Mögliche Ursachen | Empfohlene Maßnahme |
|---|---|---|
| Die SQL-Datenbank ist nicht vorhanden. | Die Datenbank wurde entweder gelöscht oder umbenannt. | Überprüfen Sie, ob die Datenbank versehentlich gelöscht oder umbenannt wurde.<br/><br/> Wenn die Datenbank versehentlich gelöscht wurde, stellen Sie sie am ursprünglichen Speicherort wieder her, um weiter Sicherungen anzulegen.<br/><br/> Wenn Sie die Datenbank gelöscht haben und zukünftig keine weiteren Sicherungen benötigen, klicken Sie im Recovery Services-Tresor auf [Sicherung beenden mit „Daten löschen/beibehalten“](manage-monitor-sql-database-backup.md).

## <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Fehlermeldung | Mögliche Ursachen | Empfohlene Maßnahme |
|---|---|---|
| Protokollkette ist unterbrochen. | Die Datenbank oder der virtuelle Computer wird mit einer anderen Sicherungslösung gesichert, die die Protokollkette abschneidet.|<ul><li>Überprüfen Sie, ob eine andere Sicherungslösung oder ein anderes Skript verwendet wird. Wenn dies der Fall ist, beenden Sie die anderen Sicherungslösung. </li><li>Wenn die Sicherung eine Ad-hoc-Protokollsicherung war, sollten Sie eine vollständige Sicherung auslösen, um eine neue Protokollkette zu starten. Für geplante Protokollsicherungen ist keine Aktion erforderlich, da Azure Backup-Dienst zur Behebung des Problems automatisch eine vollständige Sicherung auslöst.</li>|

## <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Fehlermeldung | Mögliche Ursachen | Empfohlene Maßnahme |
|---|---|---|
| Azure Backup kann keine Verbindung mit der SQL-Instanz herstellen. | Azure Backup kann keine Verbindung mit der SQL-Instanz herstellen. | Verwenden Sie die zusätzlichen Details im Fehlermenü im Azure-Portal, um die Grundursache einzugrenzen. Lesen Sie [Problembehandlung bei SQL-Sicherung](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine), um den Fehler zu beheben.<br/><ul><li>Wenn die SQL-Standardeinstellungen Remoteverbindungen nicht zulassen, ändern Sie die Einstellungen. Informationen zum Ändern der Einstellungen finden Sie in den folgenden Artikeln.<ul><li>[MSSQLSERVER_-1](/previous-versions/sql/sql-server-2016/bb326495(v=sql.130))</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Bei Anmeldeproblemen erhalten Sie weitere Informationen zu Behebung über die Links unten.<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

## <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Fehlermeldung | Mögliche Ursachen | Empfohlene Maßnahme |
|---|---|---|
| Die erste vollständige Sicherung für diese Datenquelle ist nicht vorhanden. | Vollständige Sicherung für die Datenbank ist nicht vorhanden. Protokollsicherungen und differenzielle Sicherungen sind einer vollständigen Sicherung übergeordnet. Daher müssen erst vollständige Sicherungen erstellt werden, bevor differenzielle Sicherungen oder Protokollsicherungen ausgelöst werden. | Lösen Sie eine vollständige Ad-hoc-Sicherung aus.   |

## <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Fehlermeldung | Mögliche Ursachen | Empfohlene Maßnahme |
|---|---|---|
| Sicherung nicht möglich, da das Transaktionsprotokoll für die Datenquelle voll ist. | Das Transaktionsprotokoll der Datenbank ist voll. | Um dieses Problem zu beheben, lesen Sie die[SQL-Dokumentation](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |

## <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Fehlermeldung | Mögliche Ursachen | Empfohlene Maßnahme |
|---|---|---|
| Eine Datenbank mit demselben Namen ist am Zielspeicherort bereits vorhanden. | Im Ziel für die Wiederherstellung ist bereits eine Datenbank mit demselben Namen vorhanden.  | <ul><li>Ändern Sie den Zieldatenbanknamen.</li><li>Oder verwenden Sie alternativ die Option „Überschreiben erzwingen“ auf der Seite „Wiederherstellung“.</li> |

## <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Fehlermeldung | Mögliche Ursachen | Empfohlene Maßnahme |
|---|---|---|
| Fehler bei der Wiederherstellung, wie die Datenbank konnte nicht offline geschaltet werden konnte. | Während einer Wiederherstellung muss die Zieldatenbank offline geschaltet werden. Azure Backup kann diese Daten nicht offline schalten. | Verwenden Sie die zusätzlichen Details im Fehlermenü im Azure-Portal, um die Grundursache einzugrenzen. Weitere Informationen finden Sie in der [SQL-Dokumentation](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |

##  <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Fehlermeldung | Mögliche Ursachen | Empfohlene Maßnahme |
|---|---|---|
| Serverzertifikat mit dem Fingerabdruck kann im Ziel nicht gefunden werden. | Die Masterdatenbank auf der Zielinstanz verfügt nicht über einen gültigen Verschlüsselungsfingerabdruck. | Importieren Sie den in der Quellinstanz verwendeten gültigen Zertifikatfingerabdruck in die Zielinstanz. |

## <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Fehlermeldung | Mögliche Ursachen | Empfohlene Maßnahme |
|---|---|---|
| Die für die Wiederherstellung verwendete Protokollsicherung enthält massenprotokollierte Änderungen. Sie kann gemäß SQL-Richtlinien nicht verwendet werden, um an einem beliebigen Punkt anzuhalten. | Wenn sich eine Datenbank im massenprotokollierten Wiederherstellungsmodus befindet, können die Daten zwischen einer massenprotokollierten Transaktion und der nächsten protokollierten Transaktion nicht wiederhergestellt werden. | Wählen Sie einen anderen Zeitpunkt für die Wiederherstellung aus. [Weitere Informationen](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms186229(v=sql.105))


## <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Fehlermeldung | Mögliche Ursachen | Empfohlene Maßnahme |
|---|---|---|
| Sicherungsvoreinstellungen für die SQL Always On-Verfügbarkeitsgruppe kann nicht erfüllt werden, da einige Knoten der Verfügbarkeitsgruppe nicht registriert sind. | Knoten, die zum Durchführen der Sicherungen erforderlich sind, sind nicht registriert oder nicht erreichbar. | <ul><li>Stellen Sie sicher, dass alle Knoten, die zum Durchführen der Sicherungen dieser Datenbank erforderlich sind, registriert und fehlerfrei sind, und wiederholen Sie dann den Vorgang.</li><li>Ändern Sie die Sicherungsvoreinstellung der SQL Always On-Verfügbarkeitsgruppe.</li></ul> |

## <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Fehlermeldung | Mögliche Ursachen | Empfohlene Maßnahme |
|---|---|---|
| Der virtuelle SQL Server-Computer ist herunterfahren und für Azure Backup-Dienst nicht verfügbar. | Der virtuelle Computer ist heruntergefahren. | Stellen Sie sicher, dass der SQL Server ausgeführt wird. |

## <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Fehlermeldung | Mögliche Ursachen | Empfohlene Maßnahme |
|---|---|---|
| Der Azure Backup-Dienst verwendet den Azure VM-Gast-Agent für die Sicherung, der Gast-Agent ist auf dem Zielserver jedoch nicht verfügbar. | Gast-Agent nicht aktiviert oder ist fehlerhaft. | [Installieren Sie den VM-Gast-Agent](../virtual-machines/extensions/agent-windows.md) manuell. |

## <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Fehlermeldung | Mögliche Ursachen | Empfohlene Maßnahme |
|---|---|---|
| Der beabsichtigte automatische Schutz wurde entweder entfernt oder ist nicht mehr gültig. | Wenn Sie den automatischen Schutz für eine SQL-Instanz aktivieren, werden Aufträge des Typs **Sicherung konfigurieren** für alle Datenbanken in dieser Instanz ausgeführt. Wenn Sie den automatischen Schutz während der Ausführung der Aufträge deaktivieren, werden die Aufträge des Typs **In Bearbeitung** mit diesem Fehlercode abgebrochen. | Aktivieren Sie den automatischen Schutz erneut, um alle restlichen Datenbanken zu schützen. |

## <a name="re-registration-failures"></a>Fehler bei der erneuten Registrierung

Überprüfen Sie vor dem Auslösen der erneuten Registrierung, ob ein oder mehrere [Symptome](#symptoms) vorhanden sind.

### <a name="symptoms"></a>Symptome

* Für alle Vorgänge, z. B. Sicherung, Wiederherstellung und Sicherungskonfiguration, tritt auf der VM ein Fehler mit einem der folgenden Fehlercodes auf: **WorkloadExtensionNotReachable**, **UserErrorWorkloadExtensionNotInstalled**, **WorkloadExtensionNotPresent**, **WorkloadExtensionDidntDequeueMsg**
* Als **Sicherungsstatus** für das Sicherungselement wird **Nicht erreichbar** angezeigt. Sie müssen aber alle anderen Ursachen ausschließen, die ggf. zu demselben Status führen können:

  * Fehlende Berechtigung zur Durchführung von sicherungsbezogenen Vorgängen auf der VM  
  * VM wurde heruntergefahren, sodass keine Sicherungen durchgeführt werden können
  * Netzwerkfehler  

    ![Erneutes Registrieren der VM](./media/backup-azure-sql-database/re-register-vm.png)

* Bei einer Always On-Verfügbarkeitsgruppe: Für die Sicherungen ist ein Fehler aufgetreten, nachdem Sie die Sicherungseinstellung geändert haben oder als ein Failover durchgeführt wurde

### <a name="causes"></a>Ursachen
Zu diesen Symptomen kann es aufgrund von einer oder mehreren der folgenden Ursachen kommen:

  * Erweiterung wurde gelöscht oder im Portal deinstalliert 
  * Erweiterung wurde in der **Systemsteuerung** der VM unter **Programm deinstallieren oder ändern** deinstalliert
  * Wiederherstellung der VM wurde über einen oder mehrere direkte Datenträger durchgeführt
  * VM wurde für längere Zeit heruntergefahren, da die Erweiterungskonfiguration dafür abgelaufen ist
  * VM wurde gelöscht, und eine andere VM wurde mit dem gleichen Namen und in derselben Ressourcengruppe wie die gelöschte VM erstellt
  * Einer der Knoten der Verfügbarkeitsgruppe hat nicht die vollständige Sicherungskonfiguration erhalten. Dies kann entweder bei der Registrierung der Verfügbarkeitsgruppe im Tresor oder beim Hinzufügen eines neuen Knotens passieren.  <br>
    Für die obigen Szenarien wird empfohlen, auf der VM den Vorgang für die erneute Registrierung auszulösen. Diese Option ist nur über PowerShell verfügbar. Sie wird in Kürze auch im Azure-Portal verfügbar sein.

## <a name="files-size-limit-beyond-which-restore-happens-to-default-path"></a>Maximale Dateigröße, ab der die Wiederherstellung im Standardpfad erfolgt

Die gesamte Zeichenfolgengröße von Dateien hängt nicht nur von der Anzahl der Dateien, sondern auch von deren Namen und Pfaden ab. Rufen Sie für jede der Datenbankdateien den logischen Dateinamen und den physischen Pfad ab.
Dafür können Sie die unten angegebene SQL-Abfrage verwenden:

  ```
  SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
                 INNER JOIN sys.databases db ON db.database_id = mf.database_id
                 WHERE db.name = N'<Database Name>'"
 ```

Ordnen Sie sie jetzt im unten angegebenen Format an:

  ```[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
  ```

Beispiel:

  ```[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
  ```

Wenn die Zeichenfolgenlänge des oben angegebenen Inhalts 20.000 Byte überschreitet, werden die Datenbankdateien anders gespeichert, und Sie können bei der Wiederherstellung den Zieldateipfad nicht festlegen. Die Dateien werden in dem von SQL Server bereitgestellten Standard-SQL-Pfad wiederhergestellt.

### <a name="override-the-default-target-restore-file-path"></a>Außerkraftsetzen des Standard-Zieldateipfads für die Wiederherstellung

Sie können den Zieldateipfad für die Wiederherstellung während des Wiederherstellungsvorgangs außer Kraft setzen, indem Sie eine JSON-Datei mit der Zuordnung der Datenbankdatei zum Wiederherstellungszielpfad platzieren. Erstellen Sie zu diesem Zweck eine Datei `database_name.json`, und speichern Sie sie am Speicherort *C:\Programme\Azure Workload Backup\bin\plugins\SQL*.

Der Inhalt der Datei muss dem folgenden Format entsprechen:
  ```[
    {
      "Path": "<Restore_Path>",
      "LogicalName": "<LogicalName>",
      "IsDir": "false"
    },
    {
      "Path": "<Restore_Path>",
      "LogicalName": "LogicalName",
      "IsDir": "false"
    },  
  ]
  ```

Beispiel:

  ```[
      {
        "Path": "F:\\Data\\testdb2_1546408741449456.mdf",
        "LogicalName": "testdb7",
       "IsDir": "false"
      },
      {
        "Path": "F:\\Log\\testdb2_log_1546408741449456.ldf",
        "LogicalName": "testdb7_log",
        "IsDir": "false"
      },  
    ]
  ```

 
Im oben angegebenen Inhalt können Sie den logischen Namen der Datenbankdatei mit der folgenden SQL-Abfrage abrufen:

```
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```


Diese Datei muss abgelegt werden, bevor Sie den Wiederherstellungsvorgang auslösen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Backup für SQL Server-VMs (öffentliche Vorschau), finden Sie unter [Azure Backup für SQL-VMs (öffentliche Vorschau)](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).
