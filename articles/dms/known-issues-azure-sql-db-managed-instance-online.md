---
title: Bekannte Probleme und Einschränkungen bei Onlinemigrationsvorgängen zu verwalteten Azure SQL-Instanzen
description: Erfahren Sie mehr über bekannte Probleme/Einschränkungen bei Onlinemigrationsvorgängen zu verwalteten Azure SQL-Instanzen.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 1d5c79a141dbe1310762dc90b447fe78848ac10d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94962483"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-managed-instance"></a>Bekannte Probleme/Einschränkungen bei Onlinemigrationsvorgängen zu verwalteten Azure SQL-Instanzen

Im Folgenden werden bekannte Probleme und Einschränkungen in Bezug auf Onlinemigrationsvorgängen von SQL Server zu verwalteten Azure SQL-Instanzen beschrieben.

> [!IMPORTANT]
> Bei Onlinemigrationen von SQL Server zu Azure SQL-Datenbank wird die Migration von SQL_variant-Datentypen nicht unterstützt.

## <a name="backup-requirements"></a>Sicherungsanforderungen

- **Sicherungen mit Prüfsumme**

    Azure Database Migration Service verwendet die Sicherungs- und Wiederherstellungsmethode, um Ihre lokalen Datenbanken zur verwalteten SQL-Instanz zu migrieren. Azure Database Migration Service unterstützt nur Sicherungen, die mit einer Prüfsumme erstellt wurden.

    [Aktivieren oder Deaktivieren von Sicherungsprüfsummen während der Sicherung oder Wiederherstellung (SQL Server)](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017)

    > [!NOTE]
    > Wenn Sie die Datenbanksicherungen mit Komprimierung durchführen, wird die Prüfsumme standardmäßig ausgeführt, sofern Sie sie nicht explizit deaktiviert haben.

    Wenn Sie bei Offlinemigrationsvorgängen **Azure Database Migration Service soll …** auswählen, verwendet Azure Database Migration Service die Datenbanksicherung mit aktivierter Prüfsumme.

- **Sicherungsmedien**

    Stellen Sie sicher, dass sich jede Sicherung auf einem separaten Sicherungsmedium (Sicherungsdateien) befindet. Azure Database Migration Service unterstützt keine Sicherungen, die an eine einzelne Sicherungsdatei angehängt werden. Erstellen Sie vollständige Sicherungen und Protokollsicherungen in getrennten Sicherungsdateien.

## <a name="data-and-log-file-layout"></a>Daten- und Protokolldateilayout

- **Anzahl der Protokolldateien**

    Azure Database Migration Service unterstützt keine Datenbanken mit mehreren Protokolldateien. Wenn Sie über mehrere Protokolldateien verfügen, müssen Sie diese verkleinern und in einer einzelnen Transaktionsprotokolldatei neu organisieren. Da kein Remotezugriff auf Protokolldateien möglich ist, die nicht leer sind, müssen Sie zunächst die Protokolldatei sichern.

## <a name="sql-server-features"></a>SQL Server-Funktionen

- **Filestream/Dateitabellen**

    Verwaltete SQL-Instanzen unterstützen derzeit weder FileStream noch FileTables. Bei Workloads, für die diese Features erforderlich sind, empfiehlt es sich, SQL-Server, die auf Azure-VMs ausgeführt werden, als Azure-Ziel auszuwählen.

- **In-Memory-Tabellen**

    In-Memory-OLTP ist in den Tarifen „Premium“ und „Unternehmenskritisch“ für verwaltete SQL-Instanzen verfügbar. Beim Tarif „Universell“ wird In-Memory OLTP nicht unterstützt.

## <a name="migration-resets"></a>Migrationszurücksetzungen

- **Bereitstellungen**

    Verwaltete SQL-Instanzen sind ein PaaS-Dienst mit automatischen Patches und Versionsupdates. Während der Migration von SQL Managed Instance werden nicht kritische Updates bis zu 36 Stunden zurückgehalten. Danach (und bei kritischen Updates) wird der Prozess bei einer Unterbrechung der Migration auf den vollständigen Wiederherstellungszustand zurückgesetzt.

    Die Migrationsübernahme kann erst nach der Wiederherstellung der vollständigen Sicherung aufgerufen werden, wobei dann auch alle Protokollsicherungen erfasst werden. Wenden Sie sich an den [Azure DMS-Feedbackalias](mailto:dmsfeedback@microsoft.com), wenn Migrationsübernahmen aus der Produktion betroffen sind.