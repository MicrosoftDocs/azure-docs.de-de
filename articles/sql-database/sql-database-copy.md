---
title: Kopieren einer Azure SQL-Datenbank | Microsoft Docs
description: Erstellen Sie eine hinsichtlich Transaktionen konsistente Kopie einer vorhandenen Azure SQL-Datenbank-Instanz auf demselben oder einem anderen Server.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sahsan
ms.reviewer: carlrab
manager: craigg
ms.date: 04/11/2019
ms.openlocfilehash: 4516d61c1bf8a32e807bcbc01ea1e5b283e31060
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65762688"
---
# <a name="copy-a-transactionally-consistent-copy-of-an-azure-sql-database"></a>Kopieren einer transaktionskonsistenten Kopie einer Azure SQL-Datenbank-Instanz

Azure SQL-Datenbank bietet mehrere Methoden, eine hinsichtlich Transaktionen konsistente Kopie einer vorhandenen Azure SQL-Datenbank auf demselben Server oder auf einem anderen Server zu erstellen. Sie können eine SQL-Datenbank über das Azure-Portal, über PowerShell oder über T-SQL kopieren. 

## <a name="overview"></a>Übersicht

Eine Datenbankkopie ist eine Momentaufnahme der Quelldatenbank zum Zeitpunkt der Kopieranforderung. Sie können denselben Server oder einen anderen auswählen. Sie können auch wahlweise Dienstebene und Computegröße des Servers beibehalten oder eine andere Computegröße innerhalb derselben Dienstebene (Edition) verwenden. Nachdem der Kopiervorgang abgeschlossen ist, wird die Kopie zu einer voll funktionsfähigen, unabhängigen Datenbank. Zu diesem Zeitpunkt können Sie ein Upgrade oder Downgrade auf jede beliebige Edition durchführen. Anmeldungen, Benutzer und Berechtigungen können unabhängig verwaltet werden.  

> [!NOTE]
> [Automatische Datenbanksicherungen](sql-database-automated-backups.md) werden verwendet, wenn Sie eine Datenbankkopie erstellen.

## <a name="logins-in-the-database-copy"></a>Anmeldungen in der Datenbankkopie

Wenn Sie eine Datenbank auf denselben SQL-Datenbank-Server kopieren, können für beide Datenbanken die gleichen Anmeldedaten verwendet werden. Der Sicherheitsprinzipal, den Sie zum Kopieren der Datenbank verwenden, wird zum Datenbankbesitzer der neuen Datenbank. Alle Datenbankbenutzer, ihre Berechtigungen und ihre Sicherheits-IDs (SIDs) werden in die Kopie der Datenbank kopiert.  

Wenn Sie eine Datenbank auf einen anderen SQL-Datenbank-Server kopieren, wird der Sicherheitsprinzipal auf dem neuen Server zum Datenbankbesitzer für die neue Datenbank. Wenn Sie [eigenständige Datenbankbenutzer](sql-database-manage-logins.md) für den Datenzugriff verwenden, stellen Sie sicher, dass sowohl für die primäre als auch für die sekundäre Datenbank immer die gleichen Anmeldeinformationen verwendet werden, sodass Sie nach Abschluss des Kopiervorgang mit diesen Anmeldeinformationen sofort auf die Datenbank zugreifen können. 

Wenn Sie [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) verwenden, müssen Sie die Anmeldeinformationen in der Kopie überhaupt nicht verwalten. Wenn Sie jedoch die Datenbank auf einen neuen Server kopieren, funktioniert der anmeldungsbasierte Zugriff eventuell nicht, da die Anmeldungen auf dem neuen Server nicht vorhanden sind. Informationen zum Verwalten von Anmeldungen beim Kopieren einer Datenbank auf einen anderen SQL-Datenbank-Server finden Sie unter [Verwalten der Sicherheit der Azure SQL-Datenbank nach der Notfallwiederherstellung](sql-database-geo-replication-security-config.md). 

Nachdem der Kopiervorgang erfolgreich abgeschlossen wurde und bevor andere Benutzer neu zugeordnet werden, kann nur die Anmeldung, über die der Kopiervorgang initiiert wurde (der Datenbankbesitzer), zum Anmelden bei der neuen Datenbank verwendet werden. Informationen zum Auflösen von Anmeldungen, nachdem der Kopiervorgang abgeschlossen wurde, finden Sie unter [Auflösen von Anmeldungen](#resolve-logins).

## <a name="copy-a-database-by-using-the-azure-portal"></a>Kopieren einer Datenbank über das Azure-Portal

Um eine Datenbank über das Azure-Portal zu kopieren, öffnen Sie die Seite für Ihre Datenbank, und klicken Sie auf **Kopieren**. 

   ![Datenbankkopie](./media/sql-database-copy/database-copy.png)

## <a name="copy-a-database-by-using-powershell"></a>Kopieren einer Datenbank mit PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Das PowerShell Azure Resource Manager-Modul wird von der Azure SQL-Datenbank weiterhin unterstützt, aber alle zukünftigen Entwicklungen erfolgen für das Az.Sql-Modul. Informationen zu diesen Cmdlets finden Sie unter [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Die Argumente für die Befehle im Az- und den AzureRm-Modulen sind im Wesentlichen identisch.

Um eine Datenbank über PowerShell zu kopieren, verwenden Sie das [New-AzSqlDatabaseCopy](/powershell/module/az.sql/new-azsqldatabasecopy)-Cmdlet. 

```powershell
New-AzSqlDatabaseCopy -ResourceGroupName "myResourceGroup" `
    -ServerName $sourceserver `
    -DatabaseName "MySampleDatabase" `
    -CopyResourceGroupName "myResourceGroup" `
    -CopyServerName $targetserver `
    -CopyDatabaseName "CopyOfMySampleDatabase"
```

Ein vollständiges Beispielskript finden Sie unter [Kopieren einer Datenbank auf einen neuen Server](scripts/sql-database-copy-database-to-new-server-powershell.md).

## <a name="copy-a-database-by-using-transact-sql"></a>Kopieren einer Datenbank mit Transact-SQL

Melden Sie sich mit der Prinzipalanmeldung auf Serverebene oder der Anmeldung, mit der die zu kopierende Datenbank erstellt wurde, bei der Datenbank master an. Damit der Datenbankkopiervorgang funktioniert, müssen Anmeldungen, bei denen der Prinzipal auf Serverebene nicht verwendet wird, Mitglieder der Rolle „dbmanager“ sein. Weitere Informationen zu Anmeldungen und zum Herstellen einer Verbindung mit dem Server finden Sie unter [Verwalten von Anmeldungen](sql-database-manage-logins.md).

Starten Sie das Kopieren der Quelldatenbank mit der Anweisung [CREATE DATABASE](https://msdn.microsoft.com/library/ms176061.aspx) . Durch das Ausführen dieser Anweisung wird der Kopiervorgang für die Datenbank initiiert. Da das Kopieren einer Datenbank ein asynchroner Prozess ist, erfolgt die Rückgabe der CREATE DATABASE-Anweisung, bevor die Datenbank den Kopiervorgang abgeschlossen hat.

### <a name="copy-a-sql-database-to-the-same-server"></a>Kopieren einer SQL-Datenbank auf denselben Server

Melden Sie sich mit der Prinzipalanmeldung auf Serverebene oder der Anmeldung, mit der die zu kopierende Datenbank erstellt wurde, bei der Datenbank master an. Damit der Datenbankkopiervorgang funktioniert, müssen Anmeldungen, bei denen der Prinzipal auf Serverebene nicht verwendet wird, Mitglieder der Rolle „dbmanager“ sein.

Mit diesem Befehl wird Database1 in eine neue Datenbank mit dem Namen „Database2“ auf demselben Server kopiert. Je nach Größe Ihrer Datenbank kann es einige Zeit dauern, bis der Kopiervorgang abgeschlossen ist.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database2 AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>Kopieren einer SQL-Datenbank auf einen anderen Server

Melden Sie sich bei der Masterdatenbank auf dem Zielserver an. Dies ist der SQL-Datenbank-Server, auf dem die neue Datenbank erstellt werden soll. Verwenden Sie eine Anmeldung, die den gleichen Namen und das gleiche Kennwort wie der Datenbankbesitzer der Quelldatenbank auf dem SQL-Datenbank-Quellserver verwendet. Die Anmeldung auf dem Zielserver muss auch ein Mitglied der Rolle „dbmanager“ sein, oder es muss sich um die Prinzipalanmeldung auf Serverebene handeln.

Mit diesem Befehl wird Database1 auf server1 in eine neue Datenbank mit dem Namen „Database2“ auf server2 kopiert. Je nach Größe Ihrer Datenbank kann es einige Zeit dauern, bis der Kopiervorgang abgeschlossen ist.

    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database2 AS COPY OF server1.Database1;
    
> [!IMPORTANT]
> Die Firewalls beider Server müssen so konfiguriert werden, dass sie eingehende Verbindungen über die IP-Adresse des Clients zulassen, der den T-SQL-COPY-Befehl ausgibt.

### <a name="copy-a-sql-database-to-a-different-subscription"></a>Kopieren einer SQL-Datenbank in ein anderes Abonnement

Sie können mit den im vorherigen Abschnitt beschriebenen Schritten Ihre Datenbank in eine SQL-Datenbank-Serverinstanz in einem anderen Abonnement kopieren. Verwenden Sie unbedingt eine Anmeldung, bei der Name und Kennwort mit denen des Datenbankbesitzers der Quelldatenbank identisch sind, und die ein Mitglied der DBManager-Rolle oder die Prinzipalanmeldung auf Serverebene ist. 

> [!NOTE]
> Das [Azure-Portal](https://portal.azure.com) unterstützt das Kopieren in ein anderes Abonnement nicht, da das Portal die ARM-API aufruft, und es verwendet die Abonnementzertifikate, um auf beide an der Georeplikation beteiligten Server zuzugreifen.  

### <a name="monitor-the-progress-of-the-copying-operation"></a>Überwachen des Fortschritts des Kopiervorgangs

Überwachen Sie den Kopiervorgang, indem Sie die Ansichten „sys.databases“ und „sys.dm_database_copies“ abfragen. Während des Kopiervorgangs wird die Spalte **state_desc** der Ansicht „sys.databases“ für die neue Datenbank auf **COPYING** gesetzt.

* Wenn beim Kopieren ein Fehler auftritt, wird die Spalte **state_desc** der Ansicht „sys.databases“ für die neue Datenbank auf **SUSPECT** gesetzt. Führen Sie die DROP-Anweisung in der neuen Datenbank aus, und wiederholen Sie den Vorgang später noch einmal.
* Wenn der Kopiervorgang erfolgreich ist, wird die Spalte **state_desc** der Ansicht „sys.databases“ für die neue Datenbank auf **ONLINE** gesetzt. Der Kopiervorgang ist abgeschlossen, und die neue Datenbank ist eine normale Datenbank, die unabhängig von der Quelldatenbank geändert werden kann.

> [!NOTE]
> Wenn Sie den Kopiervorgang während der Ausführung abbrechen möchten, führen Sie die Anweisung [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) für die neue Datenbank aus. Alternativ dazu kann der Kopiervorgang auch abgebrochen werden, indem die DROP DATABASE-Anweisung in der Quelldatenbank ausgeführt wird.

## <a name="resolve-logins"></a>Auflösen von Anmeldungen

Nachdem die neue Datenbank auf dem Zielserver online ist, können Sie die [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) -Anweisung verwenden, um die Benutzer aus der neuen Datenbank den Anmeldungen auf dem Zielserver zuzuordnen. Informationen zum Auflösen von verwaisten Benutzern finden Sie unter [Problembehandlung bei verwaisten Benutzern](https://msdn.microsoft.com/library/ms175475.aspx). Siehe auch [Verwalten der Sicherheit der Azure SQL-Datenbank nach der Notfallwiederherstellung](sql-database-geo-replication-security-config.md).

Für alle Benutzer werden in der neuen Datenbank die Berechtigungen beibehalten, über die sie auch in der Quelldatenbank verfügt haben. Der Benutzer, der das Kopieren der Datenbank initiiert hat, wird zum Datenbankbesitzer der neuen Datenbank und erhält eine neue Sicherheits-ID (SID). Nachdem der Kopiervorgang erfolgreich abgeschlossen wurde und bevor andere Benutzer neu zugeordnet werden, kann nur die Anmeldung, über die der Kopiervorgang initiiert wurde (der Datenbankbesitzer), zum Anmelden bei der neuen Datenbank verwendet werden.

Informationen zum Verwalten von Benutzern und Anmeldungen beim Kopieren einer Datenbank auf einen anderen SQL-Datenbank-Server finden Sie unter [Verwalten der Sicherheit der Azure SQL-Datenbank nach der Notfallwiederherstellung](sql-database-geo-replication-security-config.md).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu Anmeldungen finden Sie unter [Verwalten von Anmeldungen](sql-database-manage-logins.md) und [Verwalten der Sicherheit der Azure SQL-Datenbank nach der Notfallwiederherstellung](sql-database-geo-replication-security-config.md).
* Informationen zum Exportieren einer Datenbank finden Sie unter [Exportieren der Datenbank in eine BACPAC-Datei](sql-database-export.md).
