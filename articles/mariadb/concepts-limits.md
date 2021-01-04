---
title: Einschränkungen – Azure Database for MariaDB
description: Dieser Artikel beschreibt die Einschränkungen in Azure Database for MariaDB, z.B. die Anzahl von Verbindungs- und Speicher-Engine-Optionen.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 10/2/2020
ms.openlocfilehash: d4546732f067988c9d7dd2d11c718a15fbe32d23
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540653"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Einschränkungen in Azure Database for MariaDB
In den folgenden Abschnitten werden die Kapazitäts- und funktionalen Beschränkungen sowie Beschränkungen bei der Unterstützung der Speicher-Engine und von Datenmanipulationsanweisungen im Datenbankdienst beschrieben.

## <a name="server-parameters"></a>Serverparameter

> [!NOTE]
> Wenn Sie nach Minimal-/Maximalwerten für Serverparameter wie `max_connections` und `innodb_buffer_pool_size` suchen, finden Sie diese Informationen nun im Artikel zu **[Serverparametern](./concepts-server-parameters.md)** .

Azure Database for MariaDB unterstützt das Anpassen einiger Serverparameter. Die Minimal- und Maximalwerte einiger Parameter (z. B. `max_connections`, `join_buffer_size`, `query_cache_size`) werden durch den Tarif und die virtuellen Kerne des Servers bestimmt. Im Artikel [Serverparameter](./concepts-server-parameters.md) finden Sie weitere Informationen zu diesen Grenzwerten.

Bei der ersten Bereitstellung enthält ein Server für Azure for MariaDB Systemtabellen für Zeitzoneninformationen, die jedoch nicht aufgefüllt sind. Die Zeitzonentabellen können durch Aufrufen der gespeicherten Prozedur `mysql.az_load_timezone` über ein Tool wie die MySQL-Befehlszeile oder MySQL Workbench aufgefüllt werden. Informationen zum Aufrufen der gespeicherten Prozedur und zum Festlegen der globalen Zeitzonen oder Zeitzonen auf Sitzungsebene finden Sie in den Artikeln für das [Azure-Portal](howto-server-parameters.md#working-with-the-time-zone-parameter) und die [Azure CLI](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter).

Kennwort-Plug-Ins wie „validate_password“ und „caching_sha2_password“ werden vom Dienst nicht unterstützt.

## <a name="storage-engine-support"></a>Speicher-Engine-Unterstützung

### <a name="supported"></a>Unterstützt
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [MEMORY](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Nicht unterstützt
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/)
- [ARCHIVE](https://mariadb.com/kb/en/library/archive/)

## <a name="privileges--data-manipulation-support"></a>Berechtigungen und Unterstützung der Datenbearbeitung

Viele Serverparameter und -einstellungen können unbeabsichtigterweise die Serverleistung beeinträchtigen oder ACID-Eigenschaften des MariaDB-Servers verschlechtern. Um die Dienstintegrität und die SLA auf Produktebene aufrechtzuerhalten, macht dieser Dienst nicht mehrere Rollen verfügbar. 

Der MariaDB-Dienst gestattet keinen direkten Zugriff auf das zugrunde liegende Dateisystem. Einige Befehle zur Datenbearbeitung werden nicht unterstützt. 

## <a name="privilege-support"></a>Berechtigungsunterstützung

### <a name="unsupported"></a>Nicht unterstützt

Folgendes wird nicht unterstützt:
- DBA-Rolle: Eingeschränkt. Alternativ können Sie den Administratorbenutzer verwenden (der bei Erstellung eines neuen Servers erstellt wird), der Ihnen die Ausführung der meisten DDL- und DML-Anweisungen ermöglicht. 
- SUPER-Berechtigung: Auf ähnliche Weise ist die [SUPER-Berechtigung](https://mariadb.com/kb/en/library/grant/#global-privileges) ebenfalls eingeschränkt.
- DEFINER: Erfordert erhöhte Berechtigungen zum Erstellen und ist beschränkt. Entfernen Sie beim Importieren von Daten mithilfe einer Sicherung die `CREATE DEFINER`-Befehle manuell oder mithilfe des Befehls `--skip-definer`, wenn Sie einen mysqldump ausführen.
- Systemdatenbanken: Die [Systemdatenbank mysql](https://mariadb.com/kb/en/the-mysql-database-tables/) ist schreibgeschützt und wird zur Unterstützung verschiedener PaaS-Funktionen eingesetzt. Die `mysql`-Systemdatenbank kann nicht geändert werden.
- `SELECT ... INTO OUTFILE`: Wird im Dienst nicht unterstützt.

### <a name="supported"></a>Unterstützt
- `LOAD DATA INFILE` wird unterstützt, jedoch muss der Parameter `[LOCAL]` angegeben und an einen UNC-Pfad (über das SMB-Protokoll eingebundene Azure Storage-Instanz) weitergeleitet werden.

## <a name="functional-limitations"></a>Funktionale Beschränkungen

### <a name="scale-operations"></a>Skalierungsvorgänge
- Die dynamische Skalierung auf den oder vom Basic-Tarif aus wird zurzeit nicht unterstützt.
- Die Verringerung der Größe des Serverspeichers wird nicht unterstützt.

### <a name="server-version-upgrades"></a>Upgrades von Serverversionen
- Die automatisierte Migration zwischen Hauptversionen von Datenbank-Engines wird derzeit nicht unterstützt.

### <a name="point-in-time-restore"></a>Point-in-Time-Wiederherstellung
- Wenn Sie das PITR-Feature verwenden, wird der neue Server mit den gleichen Konfigurationen erstellt wie der Server, auf dem er basiert.
- Die Wiederherstellung eines gelöschten Servers wird nicht unterstützt.

### <a name="subscription-management"></a>Abonnementverwaltung
- Die dynamische Verschiebung von vorab erstellten Servern zwischen Abonnement- und Ressourcengruppen wird derzeit nicht unterstützt.

### <a name="vnet-service-endpoints"></a>VNET-Dienstendpunkte
- VNET-Dienstendpunkte werden nur für Server vom Typ „Universell“ und „Arbeitsspeicheroptimiert“ unterstützt.

### <a name="storage-size"></a>Speichergröße
- Informationen zu den Beschränkungen der Speichergröße pro Tarif finden Sie unter [Tarife](concepts-pricing-tiers.md).

## <a name="current-known-issues"></a>Aktuelle bekannte Probleme
- Die MariaDB-Serverinstanz zeigt die falsche Serverversion an, nachdem die Verbindung hergestellt wurde. Um die richtige Engine-Version der Serverinstanzen abzurufen, verwenden Sie den Befehl `select version();`.

## <a name="next-steps"></a>Nächste Schritte
- [Verfügbaren Funktionen auf den einzelnen Dienstebenen](concepts-pricing-tiers.md)
- [Unterstützte MariaDB-Datenbankversionen](concepts-supported-versions.md)
