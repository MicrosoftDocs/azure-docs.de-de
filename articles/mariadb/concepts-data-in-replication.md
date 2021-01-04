---
title: Datenreplikation – Azure Database for MariaDB
description: Erfahren Sie mehr über das Verwenden der Datenreplikation zum Synchronisieren von Daten von einem externen Server mit dem Dienst Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 331e064bcf11af31a778cb8dd06c463712421b7c
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533428"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>Replizieren von Daten in Azure Database for MariaDB

Mithilfe der Datenreplikation können Sie Daten von einem MariaDB-Server, der lokal, auf virtuellen Computern oder von Datenbankdiensten ausgeführt wird, die von anderen Cloudanbietern gehostet werden, mit dem Azure Database for MariaDB-Dienst synchronisieren. Die Datenreplikation basiert auf der nativen MariaDB-Replikation, die wiederum auf der Position der binären Protokolldatei (binlog) basiert. Weitere Informationen zur binlog-Replikation finden Sie [Übersicht über die binlog Replikation](https://mariadb.com/kb/en/library/replication-overview/).

## <a name="when-to-use-data-in-replication"></a>Szenarien für die Verwendung der Datenreplikation für Azure Database for MySQL
Zu den wichtigsten Szenarien, bei denen die Datenreplikation für Azure Database for MySQL infrage kommt, zählen Folgende:

- **Synchronisierung von Hybriddaten:** Mit der Datenreplikation für Azure Database for MySQL können Sie Daten zwischen Ihren lokalen Servern und Azure Database for MariaDB synchron halten. Eine solche Synchronisierung ist für die Erstellung von Hybridanwendungen nützlich. Diese Methode ist optimal geeignet, wenn Sie über einen lokalen Datenbankserver verfügen, die Daten jedoch in eine Region verschieben möchten, die sich näher bei den Endbenutzern befindet.
- **Synchronisierung von Daten in verschiedenen Clouds:** Verwenden Sie bei komplexen Cloudlösungen die Datenreplikation für Azure Database for MariaDB, um Daten zwischen Azure Database for MySQL und unterschiedlichen Cloudanbietern zu synchronisieren, einschließlich virtueller Computer und Datenbankdienste, die in diesen Clouds gehostet werden.

## <a name="limitations-and-considerations"></a>Einschränkungen und Aspekte

### <a name="data-not-replicated"></a>Nicht replizierte Daten
Die [*Systemdatenbank „mysql“*](https://mariadb.com/kb/en/library/the-mysql-database-tables/) auf dem Quellserver wird nicht repliziert. Änderungen an Konten und Berechtigungen auf dem Quellserver werden nicht repliziert. Wenn Sie ein Konto auf dem Quellserver erstellen und dieses Konto über Zugriff auf den Replikatserver verfügen muss, erstellen Sie dasselbe Konto manuell auf dem Replikatserver. Einen Überblick über die Tabellen, die in der Systemdatenbank enthalten sind, finden Sie in der [Dokumentation zu MariaDB](https://mariadb.com/kb/en/library/the-mysql-database-tables/).

### <a name="requirements"></a>Requirements (Anforderungen)
- Der Quellserver muss mindestens die MariaDB-Version 10.2 aufweisen.
- Die Versionen des Quell- und des Replikatservers müssen identisch sein. Beispiel: Auf beiden muss MariaDB-Version 10.2 vorliegen.
- Jede Tabelle muss über einen Primärschlüssel verfügen.
- Der Quellserver sollte die InnoDB-Engine verwenden.
- Benutzer müssen über Berechtigungen zum Konfigurieren der binären Protokollierung und zum Erstellen neuer Benutzer auf dem Quellserver verfügen.
- Wenn für den Quellserver SSL aktiviert ist, vergewissern Sie sich, dass das für die Domäne bereitgestellte SSL-Zertifizierungsstellenzertifikat in die gespeicherte Prozedur `mariadb.az_replication_change_master` eingefügt wurde. Sehen Sie sich die folgenden [Beispiele](howto-data-in-replication.md#link-the-source-and-replica-servers-to-start-data-in-replication) und den Parameter `master_ssl_ca` an.
- Vergewissern Sie sich, dass die IP-Adresse des Quellservers den Firewallregeln des Azure Database for MariaDB-Replikatservers hinzugefügt wurde. Aktualisieren Sie Firewallregeln über das [Azure-Portal](howto-manage-firewall-portal.md) oder über die [Azure-Befehlszeilenschnittstelle](howto-manage-firewall-cli.md).
- Vergewissern Sie sich, dass für den Computer, der den Quellserver hostet, sowohl ein- als auch ausgehender Datenverkehr am Port 3306 zugelassen wird.
- Stellen Sie sicher, dass der Quellserver eine **öffentliche IP-Adresse** aufweist, das DNS öffentlich zugänglich ist oder ein vollqualifizierter Domänenname (FQDN) verfügbar ist.

### <a name="other"></a>Andere
- Die Datenreplikation wird nur in den Tarifen Universell und Arbeitsspeicheroptimiert unterstützt.

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie, wie Sie [die Datenreplikation einrichten](howto-data-in-replication.md).
