---
title: 'Best Practices für MySQL-Servervorgänge: Azure Database for MySQL'
description: In diesem Artikel werden die Best Practices für den Betrieb Ihrer MySQL-Datenbank auf Azure beschrieben.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: e1d84483a701bf7fb4e6c50b9dc4f4f89993c64d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96354883"
---
# <a name="best-practices-for-server-operations-on-azure-database-for-mysql--single-server"></a>Best Practices für Servervorgänge in Azure Database for MySQL – Einzelserver

Lernen Sie die Best Practices für die Arbeit mit Azure Database for MySQL kennen. Die in diesem Abschnitt beschriebenen Best Practices werden aktualisiert, wenn der Plattform neue Funktionen hinzugefügt werden.

## <a name="azure-database-for-mysql-operational-guidelines"></a>Azure Database for MySQL: Betriebsrichtlinien 

Es empfiehlt sich, bei der Arbeit mit Azure Database for MySQL die folgenden Betriebsrichtlinien zu befolgen, um die Leistung Ihrer Datenbank zu verbessern: 

* **Zusammenstellung**: Zum Reduzieren der Netzwerklatenz sollten sich Client und Datenbankserver in derselben Azure-Region befinden.

* **Überwachen der Arbeitsspeicher-, CPU- und Speicherauslastung**: Sie können [Warnungen einrichten](howto-alert-on-metric.md), um eine Benachrichtigung zu erhalten, wenn sich Nutzungsmuster ändern oder das Kapazitätslimit der Bereitstellung erreicht wird. So können Sie Maßnahmen ergreifen, um Systemleistung und -verfügbarkeit sicherzustellen. 

* **Hochskalieren Ihrer Datenbankinstanz**: Wenn die Kapazitätsgrenzen des Speichers erreicht werden, können Sie den Speicher [hochskalieren](howto-create-manage-server-portal.md). Sie sollten sowohl beim Arbeitsspeicher als auch beim Massenspeicher einen Puffer einrechnen, um ein unvorhergesehenes Anwachsen der Anforderungen Ihrer Anwendungen abdecken zu können. Sie können auch das Feature der [automatischen Speichervergrößerung](howto-auto-grow-storage-portal.md) aktivieren, um sicherzustellen, dass der Dienst den Speicher automatisch skaliert, wenn dieser sich den Grenzwerten nähert. 

* **Konfigurieren von Sicherungen**: Aktivieren Sie [lokale oder georedundante Sicherungen](howto-restore-server-portal.md#set-backup-configuration), je nach Anforderungen des Unternehmens. Über den Aufbewahrungszeitraum können Sie festlegen, wie lange die Sicherungen aus Gründen der Geschäftskontinuität verfügbar sein sollen. 

* **Erhöhen der E/A-Kapazität**: Wenn Ihre Datenbankworkload mehr E/A-Vorgänge erfordert als bereitgestellt, werden Wiederherstellungs- oder andere Transaktionsvorgänge für Ihre Datenbank nur langsam ausgeführt. Um die E/A-Kapazität einer Serverinstanz zu erhöhen, führen Sie einige oder alle der folgenden Aktionen aus: 

    * Azure Database for MySQL bietet eine IOPS-Skalierung mit einer Geschwindigkeit von drei IOPS pro bereitgestelltem GB Speicherplatz. [Vergrößern Sie den bereitgestellten Speicher](howto-create-manage-server-portal.md#scale-storage-up), um den IOPS-Wert zu skalieren und eine bessere Leistung zu erzielen. 

    * Wenn Sie bereits Speicher mit bereitgestellten IOPS verwenden, stellen Sie [zusätzliche Durchsatzkapazität](howto-create-manage-server-portal.md#scale-storage-up) bereit. 

* **Skalieren von Computeressourcen**: Die Datenbankworkload kann auch durch CPU oder Arbeitsspeicher eingeschränkt sein. Dies kann schwerwiegende Auswirkungen auf die Transaktionsverarbeitung haben. Beachten Sie, dass die Computerleistung (Tarif) nur zwischen den Tarifen [„Universell“ und „Arbeitsspeicheroptimiert“](concepts-pricing-tiers.md) hoch- oder herunterskaliert werden kann. 

* **Testen des Failovers**: Testen Sie das Failover für Ihre Serverinstanz manuell, um zu ermitteln, wie lang der Prozess für Ihren Anwendungsfall dauert, und um sicherzustellen, dass die Anwendung, die auf die Serverinstanz zugreift, nach dem Failover automatisch eine Verbindung mit der neuen Serverinstanz herstellen kann.

* **Verwenden eines Primärschlüssels**: Stellen Sie sicher, dass Ihre Tabellen über einen primären oder eindeutigen Schlüssel verfügen, wenn Sie mit der Azure Database for MySQL arbeiten. Ein solcher Schlüssel ist beim Erstellen von Sicherungen, Replikaten und Ähnlichem sehr nützlich und verbessert die Leistung.

* **Konfigurieren des TTL-Werts**: Wenn Ihre Clientanwendung die DNS-Daten (Domain Name Service) Ihrer Serverinstanz zwischenspeichert, legen Sie einen TTL-Wert (Time-To-Live) von weniger als 30 Sekunden fest. Die zugrunde liegende IP-Adresse einer Serverinstanz kann sich nach einem Failover ändern. Daher kann eine Zwischenspeicherung der DNS-Daten über einen längeren Zeitraum zu Verbindungsfehlern führen, wenn Ihre Anwendung versucht, eine Verbindung mit einer IP-Adresse herzustellen, die nicht mehr verwendet wird.

* Verwenden Sie Verbindungspools, um ein Erreichen der [maximalen Anzahl von Verbindungen](concepts-server-parameters.md#max_connections) zu vermeiden. Verwenden Sie außerdem eine Wiederholungslogik, um vorübergehende Verbindungsprobleme zu vermeiden. 

* Wenn Sie Replikate verwenden, können Sie mit [ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847) die Last zwischen dem primären und dem lesbaren sekundären Replikatserver ausgleichen. Im erwähnten Artikel finden Sie die Schritte zur Einrichtung. </br> 

* Wenn Sie die Ressource bereitstellen, vergewissern Sie sich, dass die [automatische Speichervergrößerung in Azure Database for MySQL](howto-auto-grow-storage-portal.md) aktiviert ist. Dies zieht keine zusätzlichen Kosten nach sich und schützt die Datenbank vor möglicherweise auftretenden Speicherengpässen. </br> 


### <a name="using-innodb-with-azure-database-for-mysql"></a>Verwenden von InnoDB mit Azure Database for MySQL

*   Wenn Sie das `ibdata1`-Feature verwenden – einen Systemtabellenbereich –, kann eine Datendatei nicht verkleinert oder endgültig gelöscht werden, indem die Daten aus der Tabelle gelöscht werden oder die Tabelle in `tablespaces` „file-per-table“ verschoben wird.

* Bei einer Datenbankgröße von mehr als 1 TB sollten Sie die Tabelle im `tablespace` **innodb_file_per_table** erstellen. Bei einer einzelnen Tabelle mit mehr als 1 TB sollten Sie die [Partitionstabelle](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html) verwenden.

*   Bei einem Server mit einer großen `tablespace`-Anzahl startet die Engine aufgrund der sequenziellen Tabellenbereichsüberprüfung während des Starts oder Failovers von MySQL nur sehr langsam. 

* Wenn die Gesamtanzahl von Tabellen weniger als 500 beträgt, legen Sie „innodb_file_per_table = ON“ fest, bevor Sie eine Tabelle erstellen.

* Wenn Sie über mehr als 500 Tabellen in einer Datenbank verfügen, überprüfen Sie die Tabellengröße jeder einzelnen Tabelle. Bei großen Tabellen sollten Sie weiterhin den Tabellenbereich „file-per-table“ in Erwägung ziehen, um zu verhindern, dass der Systemtabellenbereich das maximale Speicherlimit erreicht.

> [!NOTE]
> Ziehen Sie bei Tabellen mit einer Größe von unter 5 GB den Systemtabellenbereich in Erwägung: 
> ```sql
>    CREATE TABLE tbl_name ... *TABLESPACE* = *innodb_system*;
> ```

* [Partitionieren](https://dev.mysql.com/doc/refman/5.7/en/partitioning.html) Sie Ihre Tabelle während der Erstellung, wenn die Tabelle sehr groß ist und möglicherweise auf mehr als 1 TB anwächst.

* Verwenden Sie mehrere MySQL-Server, und verteilen Sie die Tabellen auf die Server. Wenn Sie 10.000 Tabellen oder mehr haben, stellen Sie sicher, dass Sie nicht zu viele Tabellen auf einem einzelnen Server platzieren. 

## <a name="next-steps"></a>Nächste Schritte
- [Best Practices für die Leistung von Azure Database for MySQL](concept-performance-best-practices.md)
- [Best Practices für die Überwachung von Azure Database for MySQL](concept-monitoring-best-practices.md)
