---
title: Abfragespeicherszenarios – Azure Database for PostgreSQL – Flex Server
description: In diesem Artikel werden einige Szenarios für den Abfragespeicher in Azure Database for PostgreSQL - Flex Server beschrieben.
author: ssen-msft
ms.author: ssen
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: ac0374cf91229e7f1b3f84d967d9825e2a3090f2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107558732"
---
# <a name="usage-scenarios-for-query-store---flexible-server"></a>Verwendungsszenarien für Abfragespeicher – Flexible Server

**Anwendungsbereich:** Azure Database for PostgreSQL – Einzelserverversionen 11, 12

Sie können den Abfragespeicher in verschiedenen Szenarios verwenden, in denen das Nachverfolgen und Verwalten der vorhersagbaren Leistung einer Workload von Bedeutung ist. Betrachten Sie die folgenden Beispiele: 
- Erkennen und Optimieren besonders aufwendiger Abfragen 
- A/B-Tests 
- Stabile Leistung bei der Ausführung von Upgrades 
- Erkennen und Verbessern von Ad-hoc-Workloads 

## <a name="identify-and-tune-expensive-queries"></a>Erkennen und Optimieren von aufwendigen Abfragen 

### <a name="identify-longest-running-queries"></a>Erkennen von Abfragen mit der längsten Ausführungsdauer 
Verwenden Sie Abfragespeicher Ansichten in der azure_sys Datenbank Ihres Servers, um schnell die Abfragen mit der längsten Ausführung zu identifizieren. Diese Abfragen beanspruchen in der Regel die meisten Ressourcen. Wenn Sie die Abfragen mit der längsten Ausführungsdauer optimieren, kann das die Leistung verbessern, da dadurch Ressourcen freigegeben werden, die dann von anderen Abfragen verwendet werden können, die auf dem System ausgeführt werden. 

### <a name="target-queries-with-performance-deltas"></a>Identifizieren von Abfragen, die zu Leistungsabweichungen führen 
Der Abfragespeicher teilt die Leistungsdaten in Zeitfenster auf, damit Sie die Leistung einer Abfrage im Laufe der Zeit nachverfolgen können. Dadurch können Sie genau ermitteln, welche Abfragen dazu beitragen, dass insgesamt mehr Zeit für Abfragen aufgewendet wird. So können Sie gezielt Fehler Ihrer Workload beheben.

### <a name="tuning-expensive-queries"></a>Optimieren von aufwendigen Abfragen 
Wenn Sie feststellen, dass die Leistung einer Abfrage gering ist, ist die zu ergreifende Maßnahme von dem jeweiligen Grund für dieses Problem abhängig: 
- Vergewissern Sie sich, dass die Statistiken für die zugrunde liegenden Tabellen aktuell sind, die von der Abfrage verwendet werden.
- Prüfen Sie, ob aufwendige Abfragen neu geschrieben werden müssen. Nutzen Sie beispielsweise die Abfrageparametrisierung, und verwenden Sie weniger dynamischen SQL-Code. Implementieren Sie eine geeignete Logik für das Lesen von Daten. Dabei geht es z.B. darum, Datenfilter nicht auf Anwendungsseite, sondern auf Datenbankseite anzuwenden. 


## <a name="ab-testing"></a>A/B-Tests 
Verwenden Sie den Abfragespeicher zum Vergleichen der Workloadleistung vor und nach einer Anwendungsänderung, die Sie vor oder nach einer Migration einführen möchten. Nachfolgend werden Beispielszenarios für die Verwendung des Abfragespeichers aufgeführt, um den Einfluss der Anwendung auf die Workloadleistung zu bewerten: 
- Migration zwischen PostgreSQL-Versionen. 
- Veröffentlichen einer neuen Anwendungsversion 
- Hinzufügen von zusätzlichen Ressourcen zum Server 
- Erstellen von fehlenden Indizes in Tabellen, auf die von aufwendigen Abfragen verwiesen wird 
- Migration von Single Server zu Flex Server. 
 
Wenden Sie im Zusammenhang mit diesen Szenarios den folgenden Workflow an: 
1. Führen Sie vor der geplanten Änderung Ihre Workload mit dem Abfragespeicher aus, um eine Baseline für die Leistung zu erstellen. 
2. Wenden Sie die Anwendungsänderungen nur zu einem vorgesehen Zeitpunkt an. 
3. Führen Sie die Workload so lange weiter aus, dass nach Anwendung der Änderungen ein Leistungsimage des Systems erstellt werden kann. 
4. Vergleichen Sie die Ergebnisse, die vor der Änderung erfasst wurden, mit den Ergebnissen, die danach ermittelt werden. 
5. Entscheiden Sie sich, ob die Änderungen beibehalten werden sollen, oder ob Sie ein Rollback ausführen möchten. 


## <a name="identify-and-improve-ad-hoc-workloads"></a>Erkennen und Verbessern von Ad-hoc-Workloads 
Einige Workloads weisen keine besonders häufig ausgeführten Abfragen auf, die Sie optimieren können, um die Gesamtleistung einer Anwendung zu verbessern. Diese Workloads umfassen in der Regel eine große Anzahl an eindeutigen Abfragen, die jeweils einen gewissen Anteil der Systemressourcen verbrauchen. Die eindeutigen Abfragen werden individuell in unregelmäßigen Abständen ausgeführt. Daher hat die Runtimenutzung der einzelnen Abfragen keinen Einfluss auf die Gesamtleistung. Ein erheblicher Anteil an Systemressourcen wird allerdings durch die Abfragekompilierung beansprucht, weil die Anwendung konstant immer neue Abfragen erstellt. Dies stellt einen Nachteil dar. In der Regel kommt es zu diesem Szenario, wenn Ihre Anwendung Abfragen generiert, anstatt gespeicherte Prozeduren oder parametrisierte Abfragen zu verwenden, oder wenn diese von Frameworks für objektrelationale Zuordnungen abhängig sind, die standardmäßig Abfragen erstellen. 
 
Wenn Sie die Möglichkeit haben, den Anwendungscode anzupassen, können Sie ggf. die Datenzugriffsebene neu schreiben, damit Sie gespeicherte Prozeduren und parametrisierte Abfragen verwenden können. Sie können das Problem aber auch lösen, ohne Änderungen an der Anwendung vorzunehmen, indem Sie für die gesamte Datenbank (bzw. für alle Abfragen) oder für einzelne Abfragevorlagen mit einem Abfragehash parametrisierte Abfragen erzwingen. 

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zu den [bewährten Methoden für die Verwendung des Abfragespeichers](concepts-query-store-best-practices.md)
