---
title: Hochverfügbarkeit – Azure Database for MariaDB
description: Dieser Artikel bietet Informationen zur Hochverfügbarkeit in Azure Database for MariaDB.
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: conceptual
ms.date: 7/7/2020
ms.openlocfilehash: 4dcb1ac7ce4b468374993c11578bce553f766a42
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241343"
---
# <a name="high-availability-in-azure-database-for-mariadb"></a>Hochverfügbarkeit in Azure Database for MariaDB
Der Dienst Azure Database for MariaDB bietet eine garantierte hohe Verfügbarkeit mit der finanziell abgesicherten Vereinbarung zum Servicelevel (Service Level Agreement, SLA) über eine Uptime von [99,99 %](https://azure.microsoft.com/support/legal/sla/MariaDB). Azure Database for MariaDB bietet Hochverfügbarkeit sowohl bei geplanten Ereignissen wie benutzerseitig initiierten Skalierungscomputevorgängen als auch bei ungeplanten Ereignissen wie Ausfällen von zugrunde liegender Hardware oder Software oder Netzwerkausfällen. Der Dienst kann auch nach höchst kritischen Situationen schnell wiederhergestellt werden – so ist gewährleistet, dass für Anwendungen so gut wie keine Downtime auftritt.

Azure Database for MariaDB eignet sich zum Ausführen von unternehmenskritischen Datenbanken, die eine hohe Uptime erfordern. Der Dienst basiert auf der Azure-Architektur und verfügt über inhärente Hochverfügbarkeit, Redundanz und Resilienz, um die Datenbankdowntime aufgrund von geplanten und ungeplanten Ausfällen zu minimieren, ohne dass Sie zusätzliche Komponenten konfigurieren müssen. 

## <a name="components-in-azure-database-for-mariadb"></a>Komponenten in Azure Database for MariaDB

| **Komponente** | **Beschreibung**|
| ------------ | ----------- |
| <b>MariaDB-Datenbankserver | Azure Database for MariaDB bietet Sicherheit, Isolation und Ressourcenschutzvorrichtungen und ermöglicht einen schnellen Neustart von Datenbankservern. Diese Funktionen ermöglichen das Ausführen von Vorgängen wie Skalierung und Wiederherstellung von Datenbankservern nach einem Ausfall innerhalb weniger Sekunden. <br/> Änderungen an Daten auf dem Datenbankserver finden in der Regel im Rahmen von Datenbanktransaktionen statt. Alle Änderungen an der Datenbank werden synchron in Form von Write-Ahead-Protokollen (ib_log) im Azure Storage-Dienst aufgezeichnet, der an den Datenbankserver angefügt ist. Während des [Prüfpunktprozesses](https://mariadb.com/kb/innodb-redo-log/#checkpoints) für die Datenbank werden außerdem Datenseiten aus dem Arbeitsspeicher des Datenbankservers in den Speicher geleert. |
| <b>Remotespeicher | Alle physischen MariaDB-Datendateien und Protokolldateien werden in Azure Storage gespeichert. Dieser Dienst ist so konzipiert, dass drei Kopien der Daten in einer Region gespeichert werden, um die Redundanz, Verfügbarkeit und Zuverlässigkeit der Daten sicherzustellen. Außerdem ist die Speicherebene unabhängig vom Datenbankserver. Sie kann von einem ausgefallenen Datenbankserver getrennt und innerhalb weniger Sekunden an einen neuen Datenbankserver angefügt werden. Darüber hinaus prüft Azure Storage die Daten kontinuierlich auf Speicherfehler. Wenn eine Blockbeschädigung erkannt wird, wird diese automatisch durch Instanziieren einer neuen Speicherkopie behoben. |
| <b>Gateway | Das Gateway fungiert als Datenbankproxy und leitet alle Clientverbindungen an den Datenbankserver weiter. |

## <a name="planned-downtime-mitigation"></a>Minimierung von geplanter Downtime
Azure Database for MariaDB ist so konzipiert, dass bei einer geplanten Downtime weiterhin Hochverfügbarkeit gewährleistet ist. 

![Übersicht über die elastische Skalierung in Azure Database for MariaDB](./media/concepts-high-availability/elastic-scaling-mariadb-server.png)

Hier finden Sie einige Szenarien mit geplanter Wartung:

| **Szenario** | **Beschreibung**|
| ------------ | ----------- |
| <b>Hoch-/Herunterskalieren von Computeressourcen | Wenn ein Benutzer den Vorgang zum Hoch- bzw. Herunterskalieren von Computeressourcen ausführt, wird ein neuer Datenbankserver mit der skalierten Computekonfiguration bereitgestellt. Auf dem alten Datenbankserver können aktive Prüfpunkte abgeschlossen werden, Clientverbindungen werden entladen, alle Transaktionen ohne Commit werden abgebrochen, und anschließend wird der Server heruntergefahren. Der Speicher wird dann vom alten Datenbankserver getrennt und an den neuen Datenbankserver angefügt. Wenn die Clientanwendung versucht, die Verbindung wiederherzustellen oder eine neue Verbindung herzustellen, leitet das Gateway die Verbindungsanforderung an den neuen Datenbankserver weiter.|
| <b>Hochskalieren des Speichers | Das Hochskalieren des Speichers ist ein Onlinevorgang, bei dem der Datenbankserver nicht unterbrochen wird.|
| <b>Bereitstellung neuer Software (Azure) | Rollouts von neuen Features oder Fehlerbehebungen werden automatisch im Rahmen der geplanten Wartung des Diensts durchgeführt. Weitere Informationen finden Sie in der [Dokumentation](concepts-monitoring.md#planned-maintenance-notification) und im [Portal](https://aka.ms/servicehealthpm).|
| <b>Upgrades auf Nebenversionen | Azure Database for MariaDB patcht Datenbankserver automatisch auf die von Azure festgelegte Nebenversion. Dies erfolgt im Rahmen der geplanten Wartung des Diensts. Dabei kommt es zu einer kurzen Downtime von ein paar Sekunden. Danach wird der Datenbankserver automatisch mit der neuen Nebenversion neu gestartet. Weitere Informationen finden Sie in der [Dokumentation](concepts-monitoring.md#planned-maintenance-notification) und im [Portal](https://aka.ms/servicehealthpm).|


##  <a name="unplanned-downtime-mitigation"></a>Minimierung von ungeplanter Downtime

Ungeplante Downtime kann aufgrund von unvorhergesehenen Fehlern auftreten, darunter Fehler in der zugrunde liegenden Hardware, Netzwerkprobleme und Softwarefehler. Wenn der Datenbankserver unerwartet ausfällt, wird automatisch innerhalb weniger Sekunden ein neuer Datenbankserver bereitgestellt. Der Remotespeicher wird automatisch an den neuen Datenbankserver angefügt. Die MariaDB-Engine führt den Wiederherstellungsvorgang mithilfe von WAL-Dateien und Datenbankdateien durch und öffnet den Datenbankserver, sodass Clients eine Verbindung herstellen können. Transaktionen ohne Commit gehen verloren und müssen von der Anwendung erneut ausgeführt werden. Ungeplante Downtime lässt sich zwar nicht vollständig vermeiden, wird aber von Azure Database for MariaDB durch automatisches Ausführen von Wiederherstellungsvorgängen sowohl auf Ebene der Datenbankserver als auch des Speichers minimiert, ohne dass ein Eingreifen durch einen Benutzer erforderlich ist. 


![Übersicht über Hochverfügbarkeit in Azure Database for MariaDB](./media/concepts-high-availability/availability-mariadb-server.png)

### <a name="unplanned-downtime-failure-scenarios-and-service-recovery"></a>Ungeplante Downtime: Fehlerszenarien und Dienstwiederherstellung
Im Folgenden finden Sie einige Fehlerszenarien und die Aktionen von Azure Database for MariaDB zur automatischen Wiederherstellung:

| **Szenario** | **Automatische Wiederherstellung** |
| ---------- | ---------- |
| <B>Ausfall des Datenbankservers | Wenn der Datenbankserver aufgrund eines Fehlers an der zugrunde liegenden Hardware ausfällt, werden die aktiven Verbindungen getrennt, und alle Transaktionen, die gerade ausgeführt werden, werden abgebrochen. Ein neuer Datenbankserver wird automatisch bereitgestellt, und der Remotedatenspeicher wird an den neuen Datenbankserver angefügt. Sobald die Datenbank wiederhergestellt wurde, können Clients über das Gateway eine Verbindung mit dem neuen Datenbankserver herstellen. <br /> <br /> Anwendungen, die die MariaDB-Datenbanken verwenden, müssen so konzipiert sein, dass sie getrennte Verbindungen und Transaktionsfehler erkennen und entsprechende Wiederholungsversuche durchführen.  Wenn die Anwendung einen Wiederholungsversuch durchführt, leitet das Gateway die Verbindung transparent an den neu erstellten Datenbankserver um. |
| <B>Speicherfehler | Speicherbezogene Probleme wie der Ausfall eines Datenträgers oder physische Blockbeschädigungen haben keine Auswirkungen auf Anwendungen. Da drei Kopien der Daten gespeichert werden, wird eine Kopie der Daten vom verbleibenden Speicher bereitgestellt. Blockbeschädigungen werden automatisch behoben. Wenn eine Kopie der Daten verloren geht, wird automatisch eine neue erstellt. |

Im Folgenden finden Sie einige Fehlerszenarien, bei denen für die Wiederherstellung eine Benutzeraktion erforderlich ist:

| **Szenario** | **Plan für die Wiederherstellung** |
| ---------- | ---------- |
| <b> Regionsausfall | Regionen fallen nur selten aus. Wenn Sie jedoch Schutz vor einem Regionsausfall benötigen, können Sie ein oder mehrere Lesereplikate in anderen Regionen für die Notfallwiederherstellung konfigurieren. (Weitere Informationen finden Sie in [diesem Artikel](howto-read-replicas-portal.md) zum Erstellen und Verwalten von Lesereplikaten.) Bei einem Ausfall auf Regionsebene können Sie das in der anderen Region konfigurierte Lesereplikat manuell zum Produktionsdatenbankserver höher stufen. |
| <b> Logische Fehler/Benutzerfehler | Die Wiederherstellung nach Benutzerfehlern, z. B. versehentlich gelöschten Tabellen oder falsch aktualisierten Daten, umfasst das Durchführen einer [Zeitpunktwiederherstellung](concepts-backup.md), bei der die Daten auf einen Zeitpunkt kurz vor dem Fehler wiederhergestellt werden.<br> <br>  Wenn Sie anstelle aller Datenbanken auf dem Datenbankserver nur einen Teil der Datenbanken oder bestimmte Tabellen wiederherstellen möchten, können Sie den Datenbankserver in einer neuen Instanz wiederherstellen, die Tabellen über [mysqldump](howto-migrate-dump-restore.md) exportieren und sie dann über [restore](howto-migrate-dump-restore.md#restore-your-mariadb-database) in Ihrer Datenbank wiederherstellen. |



## <a name="summary"></a>Zusammenfassung

Azure Database for MariaDB bietet einen schnellen Neustart von Datenbankservern, redundanten Speicher und effizientes Routing über das Gateway. Um Ihre Daten zusätzlich zu schützen, können Sie Sicherungen so konfigurieren, dass sie georepliziert werden, und ein oder mehrere Lesereplikate in anderen Regionen bereitstellen. Mithilfe der inhärenten Hochverfügbarkeitsfunktionen schützt Azure Database for MariaDB Ihre Datenbanken vor den häufigsten Arten von Ausfällen und bietet eine branchenführende, finanziell abgesicherte [SLA über eine Uptime von 99,99 %](https://azure.microsoft.com/support/legal/sla/MariaDB). Dank all dieser Verfügbarkeits- und Zuverlässigkeitsfunktionen ist Azure die ideale Plattform für Ihre unternehmenskritischen Anwendungen.

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über [Azure-Regionen](../availability-zones/az-overview.md).
- Erfahren Sie mehr über [Behandlung vorübergehender Verbindungsfehler](concepts-connectivity.md)
- Erfahren Sie, wie Sie [Ihre Daten mit Lesereplikaten replizieren](howto-read-replicas-portal.md)