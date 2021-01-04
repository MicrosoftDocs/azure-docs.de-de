---
title: Datenreplikation – Azure Database for MySQL
description: Erfahren Sie mehr über das Verwenden der Datenreplikation zum Synchronisieren von Daten von einem externen Server mit dem Dienst Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 8/7/2020
ms.openlocfilehash: 99beddba470f73d6eadb448dfe1b77453ce6426d
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996218"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Replizieren von Daten in Azure Database for MySQL

Die Datenreplikation ermöglicht das Synchronisieren von Daten von einem externen MySQL-Server mit dem Dienst Azure Database for MySQL. Der externe Server kann lokal, in virtuellen Computern oder in einem Datenbankdienst vorhanden sein, der von anderen Cloudanbietern gehostet wird. Die Replikation eingehender Daten basiert auf der nativen MySQL-Replikation, die wiederum auf der Position der binären Protokolldatei (binlog) basiert. Weitere Informationen zur binlog-Replikation finden Sie unter [Binary Log File Position Based Replication Configuration Overview](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html) (Konfiguration der auf der Position der binären Protokolldatei basierenden Replikation – Übersicht). 

## <a name="when-to-use-data-in-replication"></a>Szenarien für die Verwendung der Datenreplikation für Azure Database for MySQL
Zu den wichtigsten Szenarien, bei denen die Datenreplikation für Azure Database for MySQL infrage kommt, zählen Folgende:

- **Synchronisierung von Hybriddaten:** Mit der Datenreplikation für Azure Database for MySQL können Sie Daten zwischen Ihren lokalen Servern und Azure Database for MySQL synchron halten. Eine solche Synchronisierung ist für die Erstellung von Hybridanwendungen nützlich. Diese Methode ist optimal geeignet, wenn Sie über einen lokalen Datenbankserver verfügen, die Daten jedoch in eine Region verschieben möchten, die sich näher bei den Endbenutzern befindet.
- **Synchronisierung von Daten in verschiedenen Clouds:** Verwenden Sie bei komplexen Cloudlösungen die Datenreplikation für Azure Database for MySQL, um Daten zwischen Azure Database for MySQL und unterschiedlichen Cloudanbietern zu synchronisieren, einschließlich virtueller Computer und Datenbankdienste, die in diesen Clouds gehostet werden.
 
Verwenden Sie bei Migrationsszenarien den [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/)(DMS).

## <a name="limitations-and-considerations"></a>Einschränkungen und Aspekte

### <a name="data-not-replicated"></a>Nicht replizierte Daten
Die [*Systemdatenbank „mysql“*](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) auf dem Quellserver wird nicht repliziert. Änderungen an Konten und Berechtigungen auf dem Quellserver werden nicht repliziert. Wenn Sie ein Konto auf dem Quellserver erstellen und für dieses Konto Zugriff auf den Replikatserver erforderlich ist, erstellen Sie dasselbe Konto manuell auf dem Replikatserver. Einen Überblick über die Tabellen, die in der Systemdatenbank enthalten sind, finden Sie im [Leitfaden zu MySQL](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html).

### <a name="filtering"></a>Filterung
Um das Replizieren von Tabellen vom Quellserver (lokal, auf virtuellen Computern oder in einem von anderen Cloudanbietern gehosteten Datenbankdienst gehostet) zu überspringen, wird der `replicate_wild_ignore_table`-Parameter unterstützt. Optional können Sie diesen Parameter auf dem Replikatserver, der in Azure gehostet wird, mithilfe des [Azure-Portals](howto-server-parameters.md) oder der [Azure CLI](howto-configure-server-parameters-using-cli.md) aktualisieren.

Weitere Informationen zu diesem Parameter finden Sie in der [MySQL-Dokumentation](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table).

### <a name="requirements"></a>Requirements (Anforderungen)
- Der Quellserver muss mindestens die MySQL-Version 5.6 aufweisen. 
- Die Versionen des Quell- und des Replikatservers müssen identisch sein. Beispielsweise müssen beide die Version MySQL 5.6 oder beide MySQL 5.7 aufweisen.
- Jede Tabelle muss über einen Primärschlüssel verfügen.
- Der Quellserver sollte die MySQL InnoDB-Engine verwenden.
- Benutzer müssen über Berechtigungen zum Konfigurieren der binären Protokollierung und zum Erstellen neuer Benutzer auf dem Quellserver verfügen.
- Wenn für den Quellserver SSL aktiviert ist, vergewissern Sie sich, dass das für die Domäne bereitgestellte SSL-Zertifizierungsstellenzertifikat in die gespeicherte Prozedur `mysql.az_replication_change_master` eingefügt wurde. Sehen Sie sich die folgenden [Beispiele](./howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication) und den Parameter `master_ssl_ca` an.
- Vergewissern Sie sich, dass die IP-Adresse des Quellservers den Firewallregeln des Azure Database for MySQL-Replikatservers hinzugefügt wurde. Aktualisieren Sie Firewallregeln über das [Azure-Portal](./howto-manage-firewall-using-portal.md) oder über die [Azure-Befehlszeilenschnittstelle](./howto-manage-firewall-using-cli.md).
- Vergewissern Sie sich, dass für den Computer, der den Quellserver hostet, sowohl ein- als auch ausgehender Datenverkehr am Port 3306 zugelassen wird.
- Stellen Sie sicher, dass der Quellserver eine **öffentliche IP-Adresse** aufweist, das DNS öffentlich zugänglich ist oder ein vollqualifizierter Domänenname (FQDN) verfügbar ist.

### <a name="other"></a>Andere
- Die Datenreplikation wird nur in den Tarifen Universell und Arbeitsspeicheroptimiert unterstützt.
- IDs globaler Transaktionen (GTIDs) werden nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie, wie Sie [die Datenreplikation für Azure Database for MySQL einrichten](howto-data-in-replication.md).
- Erfahren Sie mehr über das [Replizieren in Azure mit Lesereplikaten](concepts-read-replicas.md).
- Erfahren Sie, wie Sie [Daten mit minimalen Ausfallzeiten mithilfe von DMS migrieren](howto-migrate-online.md) können.