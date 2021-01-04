---
title: Einschränkungen – Azure Database for MySQL – Flexible Server
description: Dieser Artikel beschreibt die Einschränkungen in Azure Database for MySQL – Flexible Server, z. B. die Anzahl der Verbindungs- und Speicher-Engine-Optionen.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/1/2020
ms.openlocfilehash: a3abde9092519be057dcd73ec63318f970fd5c74
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94543084"
---
# <a name="limitations-in-azure-database-for-mysql---flexible-server-preview"></a>Einschränkungen in Azure Database for MySQL – Flexible Server (Vorschau)

> [!IMPORTANT] 
> Azure Database for MySQL Flexible Server befindet sich aktuell in der öffentlichen Vorschau.

Dieser Artikel beschreibt Einschränkungen im Azure Database for MySQL Flexible Server-Dienst. [Allgemeine Einschränkungen](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.7/en/limits.html) in der MySQL-Datenbank-Engine sind ebenfalls anwendbar. Informationen zu den Tarifen für Ressourcen (Compute, Arbeitsspeicher, Speicher) finden Sie im Artikel [Compute und Speicher](concepts-compute-storage.md).

## <a name="server-parameters"></a>Serverparameter

> [!NOTE]
> Wenn Sie nach Minimal-/Maximalwerten für Serverparameter wie `max_connections` und `innodb_buffer_pool_size` suchen, finden Sie diese nun in den Konzepten zu Serverparametern im Artikel zu [Serverparametern](./concepts-server-parameters.md).

Azure Database für MySQL unterstützt das Anpassen einiger Serverparameter. Die Minimal- und Maximalwerte einiger Parameter (z. B. `max_connections`, `join_buffer_size`, `query_cache_size`) wird durch die Computeebene und die Computegröße des Servers bestimmt. Im Artikel [Serverparameter](./concepts-server-parameters.md) finden Sie weitere Informationen zu diesen Grenzwerten.

Kennwort-Plug-ins wie „validate_password“ und „caching_sha2_password“ werden vom Dienst nicht unterstützt.

## <a name="storage-engines"></a>Speicher-Engines

MySQL unterstützt viele Speicher-Engines. Für Azure Database for MySQL Flexible Server werden die folgenden Speicher-Engines unterstützt und nicht unterstützt:

### <a name="supported"></a>Unterstützt
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMORY](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Nicht unterstützt
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARCHIVE](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERATED](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privileges--data-manipulation-support"></a>Berechtigungen und Unterstützung der Datenbearbeitung

Viele Serverparameter und -einstellungen können unbeabsichtigterweise die Serverleistung beeinträchtigen oder ACID-Eigenschaften des MySQL-Servers verschlechtern. Um die Dienstintegrität und die SLA auf Produktebene aufrechtzuerhalten, macht dieser Dienst nicht mehrere Rollen verfügbar. 

Der MySQL-Dienst gestattet keinen direkten Zugriff auf das zugrunde liegende Dateisystem. Einige Befehle zur Datenbearbeitung werden nicht unterstützt. 

### <a name="unsupported"></a>Nicht unterstützt

Folgendes wird nicht unterstützt:
- DBA-Rolle: Eingeschränkt. Alternativ können Sie den Administratorbenutzer verwenden (der bei Erstellung eines neuen Servers erstellt wird), der Ihnen die Ausführung der meisten DDL- und DML-Anweisungen ermöglicht. 
- SUPER-Berechtigung: Auf ähnliche Weise ist die [SUPER-Berechtigung](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) eingeschränkt.
- DEFINER: Erfordert erhöhte Berechtigungen zum Erstellen und ist beschränkt. Entfernen Sie beim Importieren von Daten mithilfe einer Sicherung die `CREATE DEFINER`-Befehle manuell oder mithilfe des Befehls `--skip-definer`, wenn Sie einen mysqldump ausführen.
- Systemdatenbanken: Die [Systemdatenbank mysql](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) ist schreibgeschützt und wird zur Unterstützung verschiedener PaaS-Funktionen eingesetzt. Die `mysql`-Systemdatenbank kann nicht geändert werden.
- `SELECT ... INTO OUTFILE`: Wird im Dienst nicht unterstützt.

### <a name="supported"></a>Unterstützt
- `LOAD DATA INFILE` wird unterstützt, jedoch muss der Parameter `[LOCAL]` angegeben und an einen UNC-Pfad (über das SMB-Protokoll eingebundene Azure Storage-Instanz) weitergeleitet werden.

## <a name="functional-limitations"></a>Funktionale Beschränkungen

### <a name="zone-redundant-ha"></a>Zonenredundante Hochverfügbarkeit
- Diese Konfiguration kann nur während der Servererstellung eingestellt werden.
- Nicht unterstützt auf der Computeebene „Burstfähig“.

### <a name="networking"></a>Netzwerk
- Die Konnektivitätsmethode kann nicht geändert werden, nachdem der Server erstellt wurde. Wenn der Server mit *Privater Zugriff (VNet-Integration)* erstellt wurde, kann er nach der Erstellung nicht in *Öffentlicher Zugriff (zugelassene IP-Adressen)* geändert werden und umgekehrt.
- TLS/SSL ist standardmäßig aktiviert und kann nicht deaktiviert werden.
- Die auf dem Server unterstützte TLS-Mindestversion ist TLS1.2. Weitere Informationen finden Sie unter [Herstellen einer Verbindung per TLS/SSL](./how-to-connect-tls-ssl.md).

### <a name="stopstart-operation"></a>Vorgang „Anhalten/Starten“
- Nicht unterstützt bei zonenredundanten HA-Konfigurationen (sowohl primär als auch Standby).
- Nicht unterstützt mit Lesereplikatkonfigurationen (sowohl Quelle als auch Replikate).

### <a name="scale-operations"></a>Skalierungsvorgänge
- Die Verringerung des bereitgestellten Serverspeichers wird nicht unterstützt.

### <a name="read-replicas"></a>Lesereplikate
- Nicht unterstützt bei zonenredundanten HA-Konfigurationen (sowohl primär als auch Standby).

### <a name="server-version-upgrades"></a>Upgrades von Serverversionen
- Die automatisierte Migration zwischen Hauptversionen von Datenbank-Engines wird nicht unterstützt. Wenn Sie die Hauptversion upgraden möchten, führen Sie eine [Sicherung und Wiederherstellung](../concepts-migrate-dump-restore.md) auf einem Server aus, der mit der neuen Engine-Version erstellt wurde.

### <a name="restoring-a-server"></a>Wiederherstellen eines Servers
- Bei der Zeitpunktwiederherstellung werden neue Server mit den gleichen Compute- und Speicherkonfigurationen wie der zugrunde liegende Quellserver erstellt. Das Compute des neu wiederhergestellten Servers kann nach der Erstellung des Servers herunterskaliert werden.
- Die Wiederherstellung eines gelöschten Servers wird nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

- Grundlegendes zu [verfügbaren Optionen für Compute- und Speicheroptionen](concepts-compute-storage.md)
- Weitere Informationen zu [Unterstützte MySQL-Versionen](concepts-supported-versions.md)
- Lesen Sie, [wie ein Server mithilfe des Azure-Portals gesichert und wiederhergestellt wird](how-to-restore-server-portal.md).