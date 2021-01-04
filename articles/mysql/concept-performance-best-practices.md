---
title: 'Best Practices für die Leistung: Azure Database for MySQL'
description: In diesem Artikel werden bewährte Methoden beim Überwachen und Optimieren Ihrer Azure Database for MySQL-Instanzen beschrieben.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: c29c043a3af46086751629b31ce68217e7226442
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96354872"
---
# <a name="best-practices-for-optimal-performance-of-your-azure-database-for-mysql---single-server"></a>Best Practices für eine optimale Leistung von Azure Database for MySQL: Einzelserver

Erfahren Sie mehr über die bewährten Methoden für eine optimale Leistung bei der Arbeit mit Ihrem Azure Database for MySQL-Einzelserver. Die in diesem Abschnitt beschriebenen bewährten Methoden werden aktualisiert, wenn der Plattform neue Funktionen hinzugefügt werden.

## <a name="physical-proximity"></a>Geografische Nähe

 Achten Sie darauf, eine Anwendung und die Datenbank in derselben Region bereitzustellen. Als schnelle Überprüfung vor dem Starten von Benchmarktests zur Leistung ermitteln Sie die Netzwerklatenz zwischen Client und Datenbank mithilfe einer einfachen SELECT 1-Abfrage. 

## <a name="accelerated-networking"></a>Beschleunigter Netzwerkbetrieb

Nutzen Sie den beschleunigten Netzwerkbetrieb für den Anwendungsserver, wenn Sie Azure Virtual Machines, Azure Kubernetes oder App Services verwenden. Der beschleunigte Netzwerkbetrieb ermöglicht die E/A-Virtualisierung mit Einzelstamm (Single Root I/O Virtualization, SR-IOV) auf einem virtuellen Computer und somit eine erhebliche Steigerung der Netzwerkleistung. Über diesen für Hochleistung konzipierten Pfad wird der Host des Datenpfads umgangen, um Latenzen, Jitter und CPU-Auslastung zu verringern. So können mit unterstützten VM-Typen die anspruchsvollsten Netzwerkworkloads genutzt werden.

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

- Anhand der [Metriken für den MySQL-Server](https://docs.microsoft.com/azure/mysql/concepts-monitoring) können Sie überprüfen, ob der Prozentsatz an Arbeitsspeicher die [Grenzwerte](https://docs.microsoft.com/azure/mysql/concepts-pricing-tiers) erreicht. 
- Richten Sie Warnungen zu solchen Messwerten ein, damit Sie bei Erreichen der Servergrenzwerte Maßnahmen zur Behebung ergreifen können. Überprüfen Sie basierend auf den definierten Grenzwerten, ob das Hochskalieren der Datenbank-SKU – auf eine höhere Computegröße oder einen besseren Tarif – zu einer deutlichen Leistungssteigerung führt. 
- Setzen Sie das Hochskalieren so lange fort, bis Ihre Leistungsdaten nach einem Skalierungsvorgang nicht mehr deutlich sinken. Weitere Informationen zum Überwachen der Metriken einer Datenbankinstanz finden Sie unter [Metriken für Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/concepts-monitoring#metrics).

## <a name="next-steps"></a>Nächste Schritte

- [Best Practices für Servervorgänge mit Azure Database for MySQL](concept-operation-excellence-best-practices.md) <br/>
- [Best Practices für die Überwachung von Azure Database for MySQL](concept-monitoring-best-practices.md)<br/>
- [Erste Schritte mit Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)<br/>
