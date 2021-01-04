---
title: 'Best Practices für die Überwachung: Azure Database for MySQL'
description: In diesem Artikel werden bewährte Methoden zum Überwachen Ihrer Azure Database for MySQL-Instanzen beschrieben.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: 59301e26f4d42056322ba5a7cdaff1555c531096
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96354871"
---
# <a name="best-practices-for-monitoring-azure-database-for-mysql--single-server"></a>Best Practices für die Überwachung von Azure Database for MySQL: Einzelserver

Erfahren Sie mehr über die bewährten Methoden zum Überwachen von Datenbankvorgängen und wie Sie sicherstellen, dass die Leistung bei steigendem Datenvolumen nicht beeinträchtigt wird. Die in diesem Abschnitt beschriebenen bewährten Methoden werden aktualisiert, wenn der Plattform neue Funktionen hinzugefügt werden.

## <a name="layout-of-the-current-monitoring-toolkit"></a>Layout des derzeitigen Überwachungstoolkits

Azure Database for MySQL bietet Tools und Methoden, mit denen Sie problemlos die Nutzung überwachen, Ressourcen (z. B. CPU, Arbeitsspeicher, E/A) hinzufügen oder entfernen, potenzielle Probleme behandeln und die Leistung einer Datenbank verbessern können. Sie können die [Leistungsmetriken regelmäßig überwachen](concepts-monitoring.md#metrics), um die durchschnittlichen, maximalen und minimalen Werte für eine Vielzahl von Zeitbereichen anzuzeigen.

Durch das [Einrichten von Warnungen](howto-alert-on-metric.md#create-an-alert-rule-on-a-metric-from-the-azure-portal) bei einem Schwellenwert für eine Metrik werden Sie sofort informiert, wenn der Server diese Grenzwerte erreicht, und können geeignete Maßnahmen ergreifen.  

Überwachen Sie den Datenbankserver, um sicherzustellen, dass die Ressourcen, die der Datenbank zugewiesen wurden, die Anwendungsworkload verarbeiten können. Wenn die Datenbank an ihre Ressourcengrenzen stößt, haben Sie folgende Möglichkeiten:
    * Ermitteln und Optimieren der Abfragen mit dem höchsten Ressourcenverbrauch 
    * Hinzufügen weiterer Ressourcen durch ein Upgrade der Dienstebene

### <a name="cpu-utilization"></a>CPU-Auslastung
Überwachen Sie die CPU-Auslastung und ob die Datenbank die CPU-Ressourcen erschöpft. Wenn die CPU-Auslastung 90 % oder mehr beträgt, sollten Sie Ihre Computekapazität hochskalieren, indem Sie die Anzahl der virtuellen Kerne erhöhen oder den nächsthöheren Tarif auswählen.  Stellen Sie beim Hoch- oder Herunterskalieren sicher, dass der Durchsatz bzw. die Parallelität den Erwartungen entspricht. 

### <a name="memory"></a>Arbeitsspeicher 
Die Menge des für den Datenbankserver verfügbaren Arbeitsspeichers ist proportional zur [Anzahl virtueller Kerne](concepts-pricing-tiers.md). Vergewissern Sie sich, dass der Arbeitsspeicher für die Workload ausreichend ist. Führen Sie Auslastungstests für Ihre Anwendung durch, um sicherzustellen, dass der Arbeitsspeicher für die Lese- und Schreibvorgänge ausreicht. Wenn der Arbeitsspeicherverbrauch der Datenbank häufig einen festgelegten Schwellenwert überschreitet, bedeutet dies, dass Sie ein Upgrade Ihrer Instanz durchführen sollten, indem Sie die Anzahl virtueller Kerne oder die Leistungsstufe erhöhen. Verwenden Sie den [Abfragedatenspeicher](concepts-query-store.md) und die [Empfehlungen zur Abfrageleistung](concepts-performance-recommendations.md), um Abfragen mit der längsten Dauer zu identifizieren, die am häufigsten ausgeführt werden. Erkunden Sie Möglichkeiten zur Optimierung. 

### <a name="storage"></a>Storage 
Die [Menge an Speicher](howto-create-manage-server-portal.md#scale-compute-and-storage), die für den MySQL-Server bereitgestellt wird, legt die IOPS für Ihren Server fest. Der vom Dienst verwendete Speicher umfasst Datenbankdateien, Transaktionsprotokolle, Serverprotokolle und Sicherungsmomentaufnahmen. Stellen Sie sicher, dass der verbrauchte Speicherplatz nicht dauerhaft 85 % des insgesamt bereitgestellten Speicherplatzes überschreitet. Wenn dies der Fall ist, müssen Sie Daten vom Datenbankserver löschen oder archivieren, um Speicherplatz freizugeben. 

### <a name="network-traffic"></a>Netzwerkdatenverkehr 

**Ein- und ausgehender Netzwerkdurchsatz:** Rate des Netzwerkdatenverkehrs zu und von der MySQL-Instanz in Megabyte pro Sekunde. Sie müssen die Anforderungen an den Durchsatz für Server bewerten und den Datenverkehr ständig überwachen, wenn der Durchsatz unter den Erwartungen liegt. 

### <a name="database-connections"></a>Datenbankverbindungen 
**Datenbankverbindungen:** Die Anzahl von Clientsitzungen, die mit der Azure Database for MySQL-Instanz verbunden sind, sollte an den [Verbindungsbeschränkungen der ausgewählten SKU](concepts-server-parameters.md#max_connections) ausgerichtet werden. 


## <a name="next-steps"></a>Nächste Schritte

- [Best Practices für die Leistung von Azure Database for MySQL](concept-performance-best-practices.md)
- [Best Practices für Servervorgänge mit Azure Database for MySQL](concept-operation-excellence-best-practices.md)
