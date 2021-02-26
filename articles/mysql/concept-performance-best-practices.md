---
title: 'Best Practices für die Leistung: Azure Database for MySQL'
description: In diesem Artikel werden einige Empfehlungen im Zusammenhang mit der Überwachung und Optimierung Ihrer Azure Database for MySQL-Instanzen beschrieben.
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: conceptual
ms.date: 1/28/2021
ms.openlocfilehash: 46c7952247babd528b230dfa0e70b0eb47878912
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99217753"
---
# <a name="best-practices-for-optimal-performance-of-your-azure-database-for-mysql---single-server"></a>Best Practices für eine optimale Leistung von Azure Database for MySQL: Einzelserver

Hier erfahren Sie, wie Sie bei der Verwendung Ihrer Azure Database for MySQL Single Server-Instanz eine optimale Leistung erzielen. Die Empfehlungen in diesem Abschnitt werden von uns aktualisiert, wenn der Plattform neue Funktionen hinzugefügt werden.

## <a name="physical-proximity"></a>Geografische Nähe

 Achten Sie darauf, eine Anwendung und die Datenbank in derselben Region bereitzustellen. Als schnelle Überprüfung vor dem Starten von Benchmarktests zur Leistung ermitteln Sie die Netzwerklatenz zwischen Client und Datenbank mithilfe einer einfachen SELECT 1-Abfrage. 

## <a name="accelerated-networking"></a>Beschleunigter Netzwerkbetrieb

Nutzen Sie den beschleunigten Netzwerkbetrieb für den Anwendungsserver, wenn Sie virtuelle Azure-Computer, Azure Kubernetes oder App Services verwenden. Der beschleunigte Netzwerkbetrieb ermöglicht die E/A-Virtualisierung mit Einzelstamm (Single Root I/O Virtualization, SR-IOV) auf einem virtuellen Computer und somit eine erhebliche Steigerung der Netzwerkleistung. Über diesen für Hochleistung konzipierten Pfad wird der Host des Datenpfads umgangen, um Latenzen, Jitter und CPU-Auslastung zu verringern. So können mit unterstützten VM-Typen die anspruchsvollsten Netzwerkworkloads genutzt werden.

## <a name="connection-efficiency"></a>Verbindungseffizienz

Das Einrichten einer neuen Verbindung ist immer eine arbeits- und zeitaufwendige Aufgabe. Wenn eine Anwendung eine Datenbankverbindung anfordert, priorisiert sie die Zuordnung vorhandener Datenbankverbindungen im Leerlauf, statt eine neue Verbindung zu erstellen.  Im Folgenden finden Sie einige Optionen für bewährte Verbindungsmethoden:

- **ProxySQL:** Verwenden Sie [ProxySQL](https://proxysql.com/). Das Tool bietet integriertes Verbindungspooling und einen [Lastenausgleich für Ihre Workload](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) mit mehreren Lesereplikaten nach Bedarf mit Änderungen am Anwendungscode.

- **Heimdall-Datenproxy:** Alternativ dazu können Sie auch den Heimdall-Datenproxy nutzen, eine herstellerneutrale, proprietäre Proxylösung. Er unterstützt das Zwischenspeichern von Abfragen und das Aufteilen von Lese-/Schreibvorgängen mit Erkennung von Replikationsverzögerungen. Weitere Informationen finden Sie unter [Accelerate MySQL Performance with the Heimdall Proxy](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/accelerate-mysql-performance-with-the-heimdall-proxy/ba-p/1063349) (Verbessern der MySQL-Leistung mit dem Heimdall-Proxy).  

- **Persistente oder langlebige Verbindung:** Wenn Ihre Anwendung nur kurze Transaktionen oder Abfragen mit einer Ausführungszeit unter 5–10 ms aufweist, ersetzen Sie kurze Verbindungen durch persistente Verbindungen. Zum Ersetzen kurzer Verbindungen durch dauerhafte Verbindungen sind nur geringfügige Änderungen am Code erforderlich, doch dies wirkt sich auf die Leistung in vielen typischen Anwendungsszenarien deutlich positiv aus. Stellen Sie sicher, dass Sie ein Timeout festlegen oder die Verbindung trennen, wenn die Transaktion abgeschlossen ist.

- **Replikat:** Wenn Sie ein Replikat verwenden, können Sie mit [ProxySQL](https://proxysql.com/) die Last zwischen dem primären und dem lesbaren sekundären Replikatserver ausgleichen. Erfahren Sie, wie Sie [ProxySQL einrichten](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/scaling-an-azure-database-for-mysql-workload-running-on/ba-p/1105847).

## <a name="data-import-configurations"></a>Konfigurationen für den Datenimport

- Sie können Ihre Instanz temporär auf eine höhere SKU-Größe skalieren, bevor Sie einen Datenimportvorgang starten, und sie nach dem erfolgreichen Import wieder herunterskalieren.
- Sie können Ihre Daten mit minimaler Ausfallzeit importieren, indem Sie [Azure Database Migration Service (DMS)](https://datamigration.microsoft.com/) für Online- oder Offlinemigrationsvorgänge verwenden. 

## <a name="azure-database-for-mysql-memory-recommendations"></a>Azure Database for MySQL: Arbeitsspeicherempfehlungen

Eine bewährte Methode zur Verbesserung der Leistung von Azure Database for MySQL besteht darin, genügend Arbeitsspeicher (RAM) zuzuweisen, damit sich der bearbeitete Satz nahezu vollständig im Arbeitsspeicher befindet. 

- Anhand der [Metriken für den MySQL-Server](./concepts-monitoring.md) können Sie überprüfen, ob der Prozentsatz an Arbeitsspeicher die [Grenzwerte](./concepts-pricing-tiers.md) erreicht. 
- Richten Sie Warnungen zu solchen Messwerten ein, damit Sie bei Erreichen der Servergrenzwerte Maßnahmen zur Behebung ergreifen können. Überprüfen Sie basierend auf den definierten Grenzwerten, ob das Hochskalieren der Datenbank-SKU (auf eine höhere Computegröße oder einen besseren Tarif) zu einer deutlichen Leistungssteigerung führt. 
- Setzen Sie das Hochskalieren so lange fort, bis Ihre Leistungsdaten nach einem Skalierungsvorgang nicht mehr deutlich sinken. Weitere Informationen zum Überwachen der Metriken einer Datenbankinstanz finden Sie unter [Metriken für Azure Database for MySQL](./concepts-monitoring.md#metrics).
 
## <a name="use-innodb-buffer-pool-warmup"></a>„Aufwärmen“ des InnoDB-Pufferpools

Nach dem Neustarten des Azure Database for MySQL-Servers werden die Datenseiten im Speicher geladen, wenn die Tabellen abgefragt werden. Dies hat eine längere Wartezeit und eine geringere Leistung bei der ersten Abfrageausführung zur Folge. Bei Workloads, für die die Latenz eine besondere Rolle spielt, ist dies möglicherweise nicht akzeptabel. 

Durch das „Aufwärmen“ des InnoDB-Pufferpools wird die Startzeit verkürzt, indem Datenträgerseiten, die sich bereits vor dem Neustart im Pufferpool befanden, erneut geladen werden, ohne darauf zu warten, dass DML- oder SELECT-Vorgänge auf die entsprechenden Zeilen zugreifen.

Sie können die Aufwärmzeit nach dem Neustart des Azure Database for MySQL-Servers verkürzen und damit einen Leistungsvorteil erzielen, indem Sie [Serverparameter für den InnoDB-Pufferpool](https://dev.mysql.com/doc/refman/8.0/en/innodb-preload-buffer-pool.html) konfigurieren. InnoDB speichert einen Prozentsatz der zuletzt verwendeten Seiten für jeden Pufferpool beim Herunterfahren des Servers und stellt diese Seiten beim Serverstart wieder her.

Beachten Sie dabei allerdings, dass die verbesserte Leistung mit einer längeren Startzeit für den Server einhergeht. Wenn dieser Parameter aktiviert ist, müssen Sie davon ausgehen, dass sich die Start- und Neustartzeiten des Servers abhängig von den auf dem Server bereitgestellten IOPS erhöhen. 

Es wird empfohlen, die Neustartzeit zu testen und zu überwachen, um sicherzustellen, dass die Leistung bei Starts bzw. Neustarts akzeptabel ist, da der Server während dieser Zeit nicht verfügbar ist. Wenn weniger als 1.000 IOPS bereitgestellt werden (oder anders ausgedrückt: wenn der bereitgestellte Speicher kleiner als 335 GB ist), sollte von der Verwendung dieses Parameters abgesehen werden.

Wenn Sie den Zustand des Pufferpools beim Herunterfahren des Servers speichern möchten, legen Sie den Serverparameter `innodb_buffer_pool_dump_at_shutdown` auf `ON` fest. Legen Sie analog dazu den Serverparameter `innodb_buffer_pool_load_at_startup` auf `ON` fest, um den Pufferpoolzustand beim Serverstart wiederherzustellen. Sie können die Auswirkung auf die Start-/Neustartdauer steuern, indem Sie den Wert des Serverparameters `innodb_buffer_pool_dump_pct` verringern und optimieren. Dieser Parameter ist standardmäßig auf `25` festgelegt.

> [!Note]
> Die Parameter zum Aufwärmen des InnoDB-Pufferpools werden nur auf universellen Speicherservern mit maximal 16 TB Speicher unterstützt. Weitere Informationen zu Speicheroptionen für Azure Database for MySQL finden Sie [hier](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers#storage).

## <a name="next-steps"></a>Nächste Schritte

- [Best Practices für Servervorgänge mit Azure Database for MySQL](concept-operation-excellence-best-practices.md) <br/>
- [Best Practices für die Überwachung von Azure Database for MySQL](concept-monitoring-best-practices.md)<br/>
- [Erste Schritte mit Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)<br/>