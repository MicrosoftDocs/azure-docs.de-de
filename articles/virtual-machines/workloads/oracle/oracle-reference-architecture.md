---
title: Referenzarchitekturen für Oracle-Datenbanken in Azure | Microsoft-Dokumentation
description: Informationen zu Referenzarchitekturen für die Ausführung von Datenbanken vom Typ „Oracle Database Enterprise Edition“ auf Microsoft Azure Virtual Machines.
author: dbakevlar
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 12/13/2019
ms.author: kegorman
ms.reviewer: cynthn
ms.openlocfilehash: 83da8cbf3a87570cfb967e0a6c8da3f0f2ed1766
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486741"
---
# <a name="reference-architectures-for-oracle-database-enterprise-edition-on-azure"></a>Referenzarchitekturen für Oracle Database Enterprise Edition in Azure

Dieser Leitfaden enthält Informationen zur Bereitstellung einer hoch verfügbaren Oracle-Datenbank in Azure. Darüber hinaus werden in diesem Leitfaden auch Aspekte der Notfallwiederherstellung beschrieben. Diese Architekturen wurden basierend auf Kundenbereitstellungen erstellt. Der Leitfaden gilt nur für Oracle Database Enterprise Edition.

Weitere Informationen zur Steigerung der Leistung Ihrer Oracle-Datenbank finden Sie unter [Entwerfen und Implementieren einer Oracle-Datenbank in Azure](oracle-design.md).

## <a name="assumptions"></a>Annahmen

- Sie sind mit den verschiedenen Konzepten von Azure, z. B. [Verfügbarkeitszonen](../../../availability-zones/az-overview.md), vertraut.
- Sie führen Oracle Database Enterprise Edition 12C oder höher aus.
- Sie sind mit den Lizenzierungsanforderungen für die Nutzung der Lösungen im Rahmen dieses Artikels vertraut und erkennen sie an.

## <a name="high-availability-for-oracle-databases"></a>Hochverfügbarkeit für Oracle-Datenbanken

Die Erzielung von Hochverfügbarkeit in der Cloud ist ein wichtiger Teil jeglicher Planung und Entwurfsarbeit einer Organisation. Microsoft Azure umfasst [Verfügbarkeitszonen](../../../availability-zones/az-overview.md) und Verfügbarkeitsgruppen (für die Verwendung in Regionen, in denen keine Verfügbarkeitszonen verfügbar sind). Lesen Sie die weiteren Informationen zum [Verwalten der Verfügbarkeit Ihrer virtuellen Computer](../../manage-availability.md) im Rahmen der Entwurfsarbeit für die Cloud.

Zusätzlich zu cloudnativen Tools und Angeboten verfügt Oracle auch über Lösungen für Hochverfügbarkeit, z. B. [Oracle Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7), [Data Guard mit FSFO](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html), [Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html) und [GoldenGate](https://www.oracle.com/middleware/technologies/goldengate.html). Diese Lösungen können in Azure eingerichtet werden. In diesem Leitfaden werden Referenzarchitekturen für diese Lösungen beschrieben.

Beim Migrieren oder Erstellen von Anwendungen für die Cloud ist es wichtig, Ihren Anwendungscode für das Hinzufügen von cloudnativen Mustern, z. B. [Wiederholungsmuster](/azure/architecture/patterns/retry) und [Trennschalter-Muster](/azure/architecture/patterns/circuit-breaker), zu optimieren. Zusätzliche Muster, die im [Leitfaden „Cloudentwurfsmuster“](/azure/architecture/patterns/) definiert sind, können dazu beitragen, dass Ihre Anwendung resilienter wird.

### <a name="oracle-rac-in-the-cloud"></a>Oracle RAC in der Cloud

Oracle Real Application Cluster (RAC) ist eine Lösung von Oracle, mit der Kunden einen hohen Durchsatz erzielen können, indem viele Instanzen auf einen Datenbankspeicher zugreifen (Architekturmuster für allgemeine Freigabe). Oracle RAC kann auch für lokale Hochverfügbarkeit verwendet werden, aber allein kann Oracle RAC nicht zur Erzielung von Hochverfügbarkeit in der Cloud eingesetzt werden. Der Grund ist, dass nur ein Schutz vor Fehlern auf Instanzebene vorhanden ist, und nicht vor Fehlern auf Rack- oder Datencenterebene. Daher empfiehlt Oracle die Nutzung von Oracle Data Guard für Ihre Datenbank (Einzelinstanz oder RAC), um für Hochverfügbarkeit zu sorgen. Kunden benötigen zur Ausführung ihrer unternehmenskritischen Anwendungen im Allgemeinen einen hohen Servicelevel (SLA). Für Oracle RAC besteht in Azure derzeit keine Zertifizierung oder Unterstützung durch Oracle. Azure verfügt aber über Features, z. B. Verfügbarkeitszonen und geplante Wartungsfenster, um einen Schutz vor Fehlern auf Instanzebene bereitzustellen. Darüber hinaus können Kunden Technologien wie Oracle Data Guard, Oracle GoldenGate und Oracle Sharding zur Erzielung von Hochleistung und Resilienz nutzen, indem sie ihre Datenbanken vor Ausfällen auf Rackebene sowie auf Rechenzentrumsebene und geopolitischer Ebene schützen.

Beim übergreifenden Ausführen von Oracle-Datenbanken in mehreren [Verfügbarkeitszonen](../../../availability-zones/az-overview.md) zusammen mit Oracle Data Guard oder GoldenGate können Kunden eine Betriebszeit-SLA von 99,99 % erreichen. In Azure-Regionen, in denen noch keine Verfügbarkeitszonen vorhanden sind, können Kunden [Verfügbarkeitsgruppen](../../manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) nutzen und so eine Betriebszeit-SLA von 99,95 % erreichen.

>HINWEIS:  Sie können ein Betriebszeitziel anstreben, das deutlich über der Betriebszeit-SLA von Microsoft liegt.

## <a name="disaster-recovery-for-oracle-databases"></a>Notfallwiederherstellung für Oracle-Datenbanken

Wenn Sie Ihre unternehmenskritischen Anwendungen in der Cloud hosten, ist es wichtig, dass Sie beim Entwerfen die Hochverfügbarkeit und Notfallwiederherstellung berücksichtigen.

Für Oracle Database Enterprise Edition ist Oracle Data Guard ein nützliches Feature für die Notfallwiederherstellung. Sie können in einer [gekoppelten Azure-Region](../../../best-practices-availability-paired-regions.md) eine Standbydatenbank-Instanz und ein Data Guard-Failover für die Notfallwiederherstellung einrichten. Zur vollständigen Vermeidung von Datenverlust empfehlen wir Ihnen, zusätzlich zu Active Data Guard eine Oracle Data Guard Far Sync-Instanz bereitzustellen. 

Erwägen Sie, die Data Guard Far Sync-Instanz in einer anderen Verfügbarkeitszone als für Ihre primäre Oracle-Datenbank einzurichten, wenn Ihre Anwendung die Latenz toleriert. (Hierfür sind eingehende Tests erforderlich.) Verwenden Sie den Modus für **maximale Verfügbarkeit**, um den synchronen Transport Ihrer Wiederholungsdateien für die Far Sync-Instanz einzurichten. Diese Dateien werden anschließend asynchron in die Standbydatenbank übertragen. 

Falls Ihre Anwendung den Leistungsverlust nicht toleriert, zu dem es beim Einrichten der Far Sync-Instanz in einer anderen Verfügbarkeitszone im Modus **Maximale Verfügbarkeit** (Synchron) kommt, können Sie eine Far Sync-Instanz in derselben Verfügbarkeitszone wie für Ihre primäre Datenbank einrichten. Erwägen Sie, zur Verbesserung der Verfügbarkeit mehrere Far Sync-Instanzen in der Nähe Ihrer primären Datenbank und mindestens eine Instanz in der Nähe Ihrer Standbydatenbank (bei Rollenübergängen) einzurichten. Weitere Informationen zu Oracle Data Guard Far Sync finden Sie in diesem [Whitepaper zu Oracle Active Data Guard Far Sync](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf).

Bei Verwendung von Oracle Standard Edition-Datenbanken können Sie ISV-Lösungen wie DBVisit Standby verwenden, mit denen Sie für Hochverfügbarkeit und Notfallwiederherstellung sorgen können.

## <a name="reference-architectures"></a>Referenzarchitekturen

### <a name="oracle-data-guard"></a>Oracle Data Guard

Mit Oracle Data Guard werden Hochverfügbarkeit, Schutz von Daten und Notfallwiederherstellung für Unternehmensdaten sichergestellt. Mit Data Guard werden Standbydatenbanken als transaktionskonsistente Kopien der primären Datenbank verwaltet. Je nach Entfernung zwischen den primären und sekundären Datenbanken und der Toleranz der Anwendung in Bezug auf die Latenz können Sie die synchrone oder die asynchrone Replikation einrichten. Wenn die primäre Datenbank dann aufgrund eines geplanten oder ungeplanten Ausfalls nicht verfügbar ist, kann Data Guard alle Standbydatenbanken auf die primäre Rolle umstellen, um die Ausfallzeit zu minimieren. 

Bei Verwendung von Oracle Data Guard können Sie auch Ihre sekundäre Datenbank für schreibgeschützte Vorgänge öffnen. Diese Konfiguration wird als „Active Data Guard“ bezeichnet. Mit Oracle Database 12c wurde das Feature „Data Guard Far Sync-Instanz“ eingeführt. Mit dieser Instanz können Sie für Ihre Oracle-Datenbank eine Konfiguration zur Vermeidung jeglichen Datenverlusts einrichten, ohne dass die Leistung beeinträchtigt wird.

> [!NOTE]
> Für Active Data Guard wird eine zusätzliche Lizenzierung benötigt. Diese Lizenz wird auch für die Verwendung des Far Sync-Features benötigt. Wenden Sie sich an Ihren Oracle-Mitarbeiter, um die Fragen zur Lizenzierung zu klären.

#### <a name="oracle-data-guard-with-fsfo"></a>Oracle Data Guard mit FSFO
Oracle Data Guard mit FSFO (Fast-Start Failover) kann für zusätzliche Resilienz sorgen, indem der Broker auf einem separaten Computer eingerichtet wird. Für Data Guard Broker und die sekundäre Datenbank wird jeweils das Beobachtungselement (Observer) ausgeführt und die primäre Datenbank auf Ausfallzeiten überwacht. Dies ermöglicht zusätzlich eine Redundanz für Ihre Einrichtung des Data Guard-Observers. 

Mit Oracle Database Version 12.2 und höher können Sie außerdem mit nur einer Oracle Data Guard Broker-Konfiguration mehrere Observer konfigurieren. Mit diesem Setup erzielen Sie zusätzliche Verfügbarkeit, falls es für einen Observer und die sekundäre Datenbank zu Ausfallzeiten kommt. Data Guard Broker ist nicht umfangreich und kann auf einem relativ kleinen virtuellen Computer gehostet werden. Weitere Informationen zu Data Guard Broker und den damit verbundenen Vorteilen finden Sie im entsprechenden Thema in der [Oracle-Dokumentation](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html).

Das folgende Diagramm zeigt eine empfohlene Architektur für die Verwendung von Oracle Data Guard in Azure mit Verfügbarkeitszonen. Mit dieser Architektur können Sie für die VM eine Betriebszeit-SLA von 99,99 % erzielen.

![Diagramm einer empfohlenen Architektur für die Verwendung von Oracle Data Guard in Azure mit Verfügbarkeitszonen](./media/oracle-reference-architecture/oracledb_dg_fsfo_az.png)

Im obigen Diagramm greift das Clientsystem auf eine benutzerdefinierte Anwendung per Oracle-Back-End über das Web zu. Das Web-Front-End ist in einem Lastenausgleichsmodul konfiguriert. Zur Verarbeitung sendet das Web-Front-End einen Aufruf an den entsprechenden Anwendungsserver. Der Anwendungsserver fragt die primäre Oracle-Datenbank ab. Die Oracle-Datenbank wurde mit einer [arbeitsspeicheroptimierten Hyperthread-VM](../../sizes-memory.md) mit [vCPUs mit eingeschränkten Kerngrößen](../../../virtual-machines/constrained-vcpu.md) konfiguriert, um Lizenzierungskosten zu sparen und die Leistung zu steigern. Es werden mehrere Premium/Ultra Disks (Managed Disks) verwendet, um die Leistung zu steigern und Hochverfügbarkeit zu erzielen.

Die Oracle-Datenbanken werden in mehreren Verfügbarkeitszonen angeordnet, um für Hochverfügbarkeit zu sorgen. Jede Zone besteht aus mindestens einem Rechenzentrum, dessen Stromversorgung, Kühlung und Netzwerkbetrieb unabhängig funktionieren. Zur Sicherstellung der Resilienz sind in allen aktivierten Regionen mindestens drei separate Zonen vorhanden. Die physische Trennung von Verfügbarkeitszonen innerhalb einer Region schützt die Daten vor Ausfällen von Rechenzentren. Darüber hinaus werden über zwei Verfügbarkeitszonen hinweg zwei FSFO-Observer eingerichtet, um die Datenbank zu initiieren und ein Failover zur sekundären Datenbank auszuführen, wenn es zu einem Ausfall kommt. 

Sie können zusätzliche Observer bzw. Standbydatenbanken in einer anderen Verfügbarkeitszone (hier Verfügbarkeitszone 1) als der Zone einrichten, die in der vorherigen Architektur dargestellt ist. Außerdem werden für Oracle-Datenbanken mit Oracle Enterprise Manager (OEM) die Betriebszeit und die Leistung überwacht. Mit OEM können Sie auch verschiedene Leistungs- und Nutzungsberichte generieren.

In Regionen, in denen Verfügbarkeitszonen nicht unterstützt werden, können Sie Verfügbarkeitsgruppen nutzen, um Ihre Oracle-Datenbank mit Hochverfügbarkeit bereitzustellen. Mit Verfügbarkeitsgruppen können Sie eine VM-Betriebszeit von 99,95 % erzielen. Das folgende Diagramm zeigt eine Referenzarchitektur dieser Vorgehensweise:

![Verwendung von Verfügbarkeitsgruppen mit Data Guard Broker (FSFO) für Oracle Database](./media/oracle-reference-architecture/oracledb_dg_fsfo_as.png)

> [!NOTE]
> * Die Oracle Enterprise Manager-VM muss nicht in einer Verfügbarkeitsgruppe angeordnet werden, weil nur eine Instanz von OEM bereitgestellt wird.
> * Ultra Disks werden in einer Verfügbarkeitsgruppen-Konfiguration derzeit nicht unterstützt.

#### <a name="oracle-data-guard-far-sync"></a>Oracle Data Guard Far Sync

Oracle Data Guard Far Sync ermöglicht für Oracle-Datenbanken eine Schutzfunktion ohne jeglichen Datenverlust. Diese Funktion ermöglicht einen Schutz vor Datenverlust, wenn für Ihren Datenbankcomputer ein Fehler auftritt. Oracle Data Guard Far Sync muss auf einer separaten VM installiert werden. Far Sync ist eine einfache Oracle-Instanz, die nur über eine Steuerdatei, Kennwortdatei, spfile und Standbyprotokolle verfügt. Es sind keine Datendateien oder Rego-Protokolldateien vorhanden. 

Für den vollständigen Schutz vor Datenverlust muss eine synchrone Kommunikation zwischen Ihrer primären Datenbank und der Far Sync-Instanz erfolgen. Die Far Sync-Instanz empfängt von der primären Datenbank auf synchrone Weise eine Wiederholungsaufforderung und leitet diese sofort asynchron an alle Standbydatenbanken weiter. Bei diesem Setup wird auch der Mehraufwand für die primäre Datenbank reduziert, weil die Wiederholungsanforderung nur an die Far Sync-Instanz gesendet werden muss, anstatt an alle Standbydatenbanken. Wenn für eine Far Sync-Instanz ein Fehler auftritt, nutzt Data Guard automatisch den asynchronen Transport von der primären zur sekundären Datenbank, um nahezu datenverlustfreien Schutz zu ermöglichen. Zur Verbesserung der Resilienz können Kunden mehrere Far Sync-Instanzen pro Datenbankinstanz (primär und sekundär) bereitstellen.

Im folgenden Diagramm ist eine Hochverfügbarkeitsarchitektur mit Oracle Data Guard Far Sync dargestellt:

![Verwendung von Verfügbarkeitszonen mit Data Guard Far Sync und Broker (FSFO) für Oracle Database](./media/oracle-reference-architecture/oracledb_dg_fs_az.png)

In der obigen Architektur wird eine Far Sync-Instanz in derselben Verfügbarkeitszone wie die Datenbankinstanz bereitgestellt, um die Latenz zwischen den Instanzen zu verringern. Falls die Anwendung empfindlich auf Latenz reagiert, sollten Sie erwägen, Ihre Datenbank und eine oder mehrere Far Sync-Instanzen in einer [Näherungsplatzierungsgruppe](../../../virtual-machines/linux/proximity-placement-groups.md) bereitzustellen.

Im folgenden Diagramm ist eine Architektur dargestellt, in der Oracle Data Guard FSFO und Far Sync eingesetzt werden, um für Hochverfügbarkeit zu sorgen und die Notfallwiederherstellung zu ermöglichen:

![Verwendung von Verfügbarkeitszonen für die Notfallwiederherstellung mit Data Guard Far Sync & Broker (FSFO) für Oracle Database](./media/oracle-reference-architecture/oracledb_dg_fs_az_dr.png)

### <a name="oracle-goldengate"></a>Oracle GoldenGate

Mit GoldenGate können Sie Daten auf Transaktionsebene für mehrere heterogene Plattformen eines Unternehmens austauschen und bearbeiten. Mit der Anwendung werden Transaktionen, für die ein Commit ausgeführt wurde, unter Bewahrung der Transaktionsintegrität und mit minimalem Aufwand für Ihre vorhandene Infrastruktur verschoben. Aufgrund der modularen Architektur können Sie ausgewählte Datensätze, Transaktionsänderungen und DDL-Änderungen (Datenbeschreibungssprache) für viele verschiedene Topologien flexibel extrahieren und replizieren.

Mit Oracle GoldenGate können Sie Ihre Datenbank für Hochverfügbarkeit konfigurieren, indem Sie die bidirektionale Replikation einrichten. Sie können eine Konfiguration vom Typ **Multimaster** oder **Aktiv/Aktiv** einrichten. Das folgende Diagramm zeigt eine empfohlene Architektur für die Einrichtung des Aktiv/Aktiv-Modus in Azure. In der folgenden Architektur wurde die Oracle-Datenbank mit einer [arbeitsspeicheroptimierten Hyperthread-VM](../../sizes-memory.md) mit [vCPUs mit eingeschränkten Kerngrößen](../../../virtual-machines/constrained-vcpu.md) konfiguriert, um Lizenzierungskosten zu sparen und die Leistung zu steigern. Es werden mehrere Premium-Datenträger bzw. Ultra Disks (Managed Disks) verwendet, um die Leistung zu steigern und eine hohe Verfügbarkeit zu erzielen.

![Verwendung von Verfügbarkeitszonen mit Data Guard Broker (FSFO) für Oracle Database](./media/oracle-reference-architecture/oracledb_gg_az.png)

> [!NOTE]
> Eine ähnliche Architektur kann mit Verfügbarkeitsgruppen in Regionen eingerichtet werden, in denen derzeit keine Verfügbarkeitszonen verfügbar sind.

Oracle GoldenGate verfügt über Prozesse vom Typ „Extract“, „Pump“ und „Replicat“, mit denen Sie Ihre Daten asynchron von einem Oracle-Datenbankserver auf einem anderen replizieren können. Mit diesen Prozessen können Sie eine bidirektionale Replikation einrichten, um für Ihre Datenbank Hochverfügbarkeit zu erzielen, falls es auf der Ebene der Verfügbarkeitszone zu Ausfällen kommt. Im obigen Diagramm wird der Prozess „Extract“ auf demselben Server wie Ihre Oracle-Datenbank ausgeführt, während die Prozesse „Data Pump“ und „Replicat“ auf einem separaten Server in derselben Verfügbarkeitszone ausgeführt werden. Der Prozess „Replicat“ wird verwendet, um Daten aus der Datenbank in der anderen Verfügbarkeitszone zu empfangen und die Daten für die Oracle-Datenbank in der zugehörigen Verfügbarkeitszone zu committen. Entsprechend werden beim Prozess „Data Pump“ Daten, die mit dem Prozess „Extract“ extrahiert wurden, an den Prozess „Replicat“ in der anderen Verfügbarkeitszone gesendet. 

Im obigen Architekturdiagramm sind die Prozesse „Data Pump“ und „Replicat“ zwar auf einem separaten Server konfiguriert, aber Sie können alle Oracle GoldenGate-Prozesse auch auf demselben Server einrichten – je nach Kapazität und Nutzung Ihres Servers. Ziehen Sie immer Ihren AWR-Bericht und die Metriken in Azure zurate, um die Nutzungsmuster Ihres Servers zu verstehen.

Beim Einrichten der bidirektionalen Oracle GoldenGate-Replikation in unterschiedlichen Verfügbarkeitszonen oder Regionen sollten Sie unbedingt sicherstellen, dass die Latenz zwischen den einzelnen Komponenten für Ihre Anwendung akzeptabel ist. Die Latenz zwischen Verfügbarkeitszonen und Regionen kann variieren und hängt von mehreren Faktoren ab. Wir empfehlen Ihnen, Leistungstests zwischen Ihrer Anwendungs- und Datenbankebene in unterschiedlichen Verfügbarkeitszonen bzw. Regionen einzurichten, um sich zu vergewissern, dass Ihre Anforderungen an die Anwendungsleistung erfüllt werden.

Die Anwendungsebene kann in einem eigenen Subnetz eingerichtet werden, und die Datenbankebene kann ebenfalls in einem eigenen Subnetz abgetrennt werden. Erwägen Sie, nach Möglichkeit [Azure Application Gateway](../../../application-gateway/overview.md) für einen Lastenausgleich des Datenverkehrs zwischen Ihren Anwendungsservern zu verwenden. Bei Azure Application Gateway handelt es sich um ein stabiles Lastenausgleichsmodul für Datenverkehr. Es ermöglicht eine cookiebasierte Sitzungsaffinität, bei der eine Benutzersitzung auf demselben Server verbleibt, um Konflikte für die Datenbank zu minimieren. Alternativen zu Application Gateway sind [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) und [Azure Traffic Manager](../../../traffic-manager/traffic-manager-overview.md).

### <a name="oracle-sharding"></a>Oracle Sharding

Sharding ist ein Datenebenenmuster, das mit Oracle 12.2 eingeführt wurde. Mit diesem Verfahren können Sie Ihre Daten für unabhängige Datenbanken horizontal partitionieren und skalieren. Es handelt sich hierbei um eine Architektur ohne Freigaben. Jede Datenbank wird auf einem dedizierten virtuellen Computer gehostet, um zusätzlich zu Resilienz und einer höheren Verfügbarkeit einen hohen Lese- und Schreibdurchsatz zu ermöglichen. Bei diesem Muster werden Single Points of Failure beseitigt, und die Isolation von Fehlern wird ermöglicht. Außerdem können parallele Upgrades ohne Ausfallzeiten durchgeführt werden. Die Ausfallzeit aufgrund des Ausfalls eines Shards oder auf Datencenterebene hat keine Auswirkung auf die Leistung oder Verfügbarkeit der Shards in anderen Datencentern. 

Sharding ist für OLTP-Anwendungen mit hohem Durchsatz geeignet, für die keine Ausfallzeiten auftreten dürfen. Für alle Zeilen mit demselben Shardschlüssel ist immer sichergestellt, dass sich diese auf demselben Shard befinden. Auf diese Weise wird die Leistung gesteigert und eine hohe Konsistenz erzielt. Anwendungen, für die Sharding genutzt wird, müssen über ein gut definiertes Datenmodell und eine Strategie für die Datenverteilung (konsistenter Hash, Bereich, Liste oder zusammengesetzt) verfügen, wobei auf Daten hauptsächlich mit einem Shardschlüssel (z. B. *customerId* oder *accountNum*) zugegriffen wird. Beim Sharding können Sie bestimmte Gruppen von Daten auch näher an den Endkunden speichern, damit Sie Ihre Anforderungen an die Leistung und Konformität leichter erfüllen können.

Wir empfehlen Ihnen, Ihre Shards zu replizieren, um Hochverfügbarkeit zu erzielen und die Notfallwiederherstellung zu ermöglichen. Sie können diese Einrichtung mit Oracle-Technologie wie Oracle Data Guard oder Oracle GoldenGate durchführen. Bei einer Replikationseinheit kann es sich um einen Shard, einen Teil eines Shards oder eine Gruppe mit Shards handeln. Die Verfügbarkeit einer Sharddatenbank wird durch den Ausfall oder die Verlangsamung von einem oder mehreren Shards nicht beeinträchtigt. Zur Erzielung von Hochverfügbarkeit können die Standby-Shards in derselben Verfügbarkeitszone angeordnet werden, in der sich auch die primären Shards befinden. Für die Notfallwiederherstellung können die Standby-Shards in einer anderen Region angeordnet werden. Sie können Shards auch in mehreren Regionen anordnen, um Datenverkehr für diese Regionen bereitzustellen. Weitere Informationen zum Konfigurieren der Hochverfügbarkeit und Replikation für Ihre Sharddatenbank finden Sie in der [Oracle Sharding-Dokumentation](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html).

Oracle Sharding umfasst hauptsächlich die folgenden Komponenten: Weitere Informationen zu diesen Komponenten finden Sie in der [Oracle Sharding-Dokumentation](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html):

- **Shardkatalog**: Spezielle Oracle-Datenbank, die als beständiger Speicher für alle Konfigurationsdaten einer Sharddatenbank dient. Alle Konfigurationsänderungen, z. B. das Hinzufügen oder Entfernen von Shards, das Zuordnen der Daten sowie die DDLS in einer Sharddatenbank, werden über den Shardkatalog initiiert. Zudem enthält der Shardkatalog auch die Masterkopie aller duplizierten Tabellen in einer SDB. 

  Im Shardkatalog werden materialisierte Sichten verwendet, um Änderungen für alle Shards automatisch in duplizierten Tabellen zu replizieren. Die Shardkatalog-Datenbank fungiert auch als Abfragekoordinator, der zum Verarbeiten von Abfragen mit mehreren Shards und Abfragen ohne Angabe eines Shardschlüssels dient. 
  
  Die Verwendung von Oracle Data Guard in Verbindung mit Verfügbarkeitszonen oder Verfügbarkeitsgruppen für die Hochverfügbarkeit des Shardkatalogs wird als bewährte Methode empfohlen. Die Verfügbarkeit des Shardkatalogs hat keine Auswirkung auf die Verfügbarkeit der Sharddatenbank. Eine Ausfallzeit im Shardkatalog wirkt sich auf Wartungsvorgänge und Abfragen mit mehreren Shards nur während des kurzen Zeitraums aus, in dem das Data Guard-Failover abgeschlossen wird. Onlinetransaktionen werden von der SDB weiterhin weitergeleitet und ausgeführt und sind von einem Ausfall des Katalogs nicht betroffen.

- **Shard Directors**: Einfache Dienste, die in jeder Region bzw. Verfügbarkeitszone bereitgestellt werden müssen, in der Ihre Shards angeordnet sind. Bei Shard Directors handelt es sich um so genannte Global Service Manager (GSM), die im Rahmen von Oracle Sharding bereitgestellt werden. Zur Erzielung von Hochverfügbarkeit empfehlen wir Ihnen, in jeder Verfügbarkeitszone, in denen Ihre Shards enthalten sind, mindestens einen Shard Director bereitzustellen. 

  Bei der ersten Verbindungsherstellung mit der Datenbank werden die Routinginformationen per Shard Director eingerichtet und für nachfolgende Anforderungen zwischengespeichert, wobei der Shard Director umgangen wird. Nachdem die Sitzung mit einem Shard eingerichtet wurde, werden alle SQL-Abfragen und DMLs unterstützt und im Bereich des jeweiligen Shards ausgeführt. Dieses Routing ist schnell und wird für alle OLTP-Workloads verwendet, für die shardinterne Transaktionen durchgeführt werden. Wir empfehlen Ihnen, das direkte Routing für alle OLTP-Workloads zu verwenden, für die höchste Ansprüche an die Leistung und Verfügbarkeit gelten. Der Routingcache wird automatisch aktualisiert, wenn ein Shard nicht verfügbar ist oder Änderungen an der Shardingtopologie vorgenommen werden. 
  
  Für das datenabhängige Routing mit hoher Leistung empfiehlt Oracle die Verwendung eines Verbindungspools, wenn auf Daten in der Sharddatenbank zugegriffen wird. Für Oracle-Verbindungspools, sprachspezifische Bibliotheken und Treiber wird Oracle Sharding unterstützt. Weitere Informationen finden Sie in der [Oracle Sharding-Dokumentation](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html#GUID-3D41F762-BE04-486D-8018-C7A210D809F9).

- **Global Service**: „Global Service“ (Globaler Dienst) ähnelt dem regulären Datenbankdienst. Zusätzlich zu allen Eigenschaften eines Datenbankdiensts verfügt ein globaler Dienst über Eigenschaften für Sharddatenbanken, z. B. Regionsaffinität zwischen Clients und dem Shard und Verzögerungstoleranz bei der Replikation. Es muss nur ein globaler Dienst erstellt werden, damit für eine Sharddatenbank Daten gelesen bzw. geschrieben werden können. Bei Verwendung von Active Data Guard und der Einrichtung von schreibgeschützten Replikaten der Shards können Sie einen weiteren globalen Dienst für schreibgeschützte Workloads erstellen. Der Client kann diese globalen Dienste verwenden, um eine Verbindung mit der Datenbank herzustellen.

- **Sharddatenbanken**: Sharddatenbanken sind Ihre Oracle-Datenbanken. Jede Datenbank wird mit Oracle Data Guard in einer Broker-Konfiguration mit FSFO (Fast-Start Failover) aktiviert. Sie müssen das Data Guard-Failover und die Replikation nicht für jeden Shard einrichten. Dies wird automatisch konfiguriert und bereitgestellt, wenn die Sharddatenbank erstellt wird. Wenn ein bestimmter Shard ausfällt, wird von Oracle Sharing für Datenbankverbindungen automatisch ein Failover von der primären zur Standbydatenbank ausgeführt.

Sie können Oracle-Sharddatenbanken mit zwei Oberflächen bereitstellen und verwalten: Oracle Enterprise Manager Cloud Control GUI bzw. `GDSCTL`-Befehlszeilen-Hilfsprogramm. Per Cloud Control haben Sie sogar die Möglichkeit, die unterschiedlichen Shards auf Verfügbarkeit und Leistung zu überwachen. Mit dem Befehl `GDSCTL DEPLOY` werden die Shards und die zugehörigen Listener automatisch erstellt. Außerdem wird mit diesem Befehl automatisch die Replikationskonfiguration bereitgestellt, die für die vom Administrator angegebene Hochverfügbarkeit auf Shardebene verwendet wird.

Es gibt verschiedene Möglichkeiten, Shards für eine Datenbank einzurichten:

* Vom System verwaltetes Sharding: Wird per Partitionierung automatisch auf die Shards verteilt.
* Benutzerdefiniertes Sharding: Ermöglicht Ihnen das Angeben der Zuordnung der Daten zu den Shards. (Dies funktioniert gut, falls gesetzliche Anforderungen oder Lokalisierungsanforderungen für Daten erfüllt werden müssen.)
* Zusammengesetztes Sharding: Dies ist eine Kombination aus vom System verwaltetem und benutzerdefiniertem Sharding für verschiedene _Shardspaces_.
* Untergeordnete Tabellenpartitionen: Diese ähneln einer regulären partitionierten Tabelle.

Weitere Informationen zu den [Shardingverfahren](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-methods.html) finden Sie in der Oracle-Dokumentation.

Eine Sharddatenbank kann für Anwendungen und Entwickler zwar wie eine Einzeldatenbank erscheinen, aber beim Migrieren von einer Datenbank ohne Sharding zu einer Sharddatenbank muss sorgfältig geplant und ermittelt werden, welche Tabellen dupliziert bzw. dem Sharding unterzogen werden. 

Duplizierte Tabellen werden auf allen Shards gespeichert, während Shardtabellen auf unterschiedliche Shards verteilt werden. Wir empfehlen Ihnen, kleinere und dimensionale Tabellen zu duplizieren und die Faktentabellen zu verteilen bzw. dem Sharding zu unterziehen. Sie können die Daten in Ihre Sharddatenbank laden, indem Sie entweder den Shardkatalog als zentrales Koordinierungstool verwenden oder für jeden Shard den Prozess „Data Pump“ ausführen. Weitere Informationen finden Sie unter [Migrieren von Daten zu einer Sharddatenbank](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-loading-data.html) in der Oracle-Dokumentation.

#### <a name="oracle-sharding-with-data-guard"></a>Oracle Sharding mit Data Guard

Oracle Data Guard kann für das Sharding mit vom System verwalteten, benutzerdefinierten und zusammengesetzten Shardingverfahren verwendet werden.

Das folgende Diagramm zeigt eine Referenzarchitektur für Oracle Sharding mit Oracle Data Guard, um für jeden Shard Hochverfügbarkeit zu erzielen. Im Architekturdiagramm ist ein _Verfahren für zusammengesetztes Sharding_ dargestellt. Das Architekturdiagramm unterscheidet sich wahrscheinlich für Anwendungen, die über unterschiedliche Anforderungen in Bezug auf Datenort, Lastenausgleich, Hochverfügbarkeit, Notfallwiederherstellung usw. verfügen, und ggf. wird ein anderes Shardingverfahren genutzt. Mit Oracle Sharding können Sie diese Anforderungen erfüllen und effizient horizontal skalieren, indem Sie diese Optionen bereitstellen. Sie können eine ähnliche Architektur auch mit Oracle GoldenGate bereitstellen.

![Verwendung von Verfügbarkeitszonen mit Data Guard Broker (FSFO) für Oracle Database Sharding](./media/oracle-reference-architecture/oracledb_dg_sh_az.png)

Das vom System verwaltete Sharding ist am einfachsten zu konfigurieren und zu verwalten. Das benutzerdefinierte Sharding oder zusammengesetzte Sharding eignet sich aber gut für Szenarien, in denen Ihre Daten und Anwendungen geografisch weit verteilt sind oder in denen Sie die Kontrolle über die Replikation jedes einzelnen Shards haben müssen. 

In der obigen Architektur wird das zusammengesetzte Sharding verwendet, um die Daten geografisch zu verteilen und Ihre Anwendungsebenen horizontal hochzuskalieren. Beim zusammengesetzten Sharding wird eine Kombination aus vom System verwaltetem und benutzerdefiniertem Sharding genutzt, sodass Sie in den Genuss der Vorteile beider Verfahren kommen. Im obigen Szenario wird für Daten zuerst das Sharding in mehreren Shardspaces durchgeführt, die nach Region getrennt sind. Anschließend werden die Daten weiter partitioniert, indem auf mehreren Shards im Shardspace ein einheitlicher Hash verwendet wird. Jeder Shardspace enthält mehrere Shardgroups. Jede Shardgroup verfügt über mehrere Shards und stellt in diesem Fall eine „Replikationseinheit“ dar. Jede Shardgroup enthält alle Daten des Shardspace. Die Shardgroups A1 und B1 sind primäre Shardgroups, und bei A2 und B2 handelt es sich um Standbygruppen. Sie können auswählen, dass einzelne Shards als Replikationseinheit fungieren sollen, und müssen nicht die gesamte Shardgroup verwenden.

In der obigen Architektur wird in jeder Verfügbarkeitszone ein GSM/Shard Director bereitgestellt, um Hochverfügbarkeit zu erzielen. Wir empfehlen Ihnen, mindestens einen GSM/Shard Director pro Datencenter bzw. Region bereitzustellen. Zusätzlich wird in jeder Verfügbarkeitszone, die eine Shardgroup enthält, eine Instanz des Anwendungsservers bereitgestellt. Diese Einrichtung ermöglicht es, die Latenz zwischen dem Anwendungsserver und der Datenbank bzw. Shardgroup gering zu halten. Wenn für eine Datenbank ein Fehler auftritt, kann der Anwendungsserver in derselben Zone wie die Standbydatenbank Anforderungen verarbeiten, nachdem der Übergang der Datenbankrolle erfolgt ist. Mit Azure Application Gateway und dem Shard Director wird die Latenz der Anforderungen und Antworten nachverfolgt, und Anforderungen werden entsprechend weitergeleitet.

Aus Sicht der Anwendung sendet das Clientsystem eine Anforderung an Azure Application Gateway (oder eine andere Technologie für den Lastenausgleich in Azure), von wo aus diese dann an die Region umgeleitet wird, die dem Client am nächsten ist. Von Azure Application Gateway werden auch beständige Sitzungen (Sticky Sessions) unterstützt. Alle Anforderungen, die von demselben Client stammen, werden an denselben Anwendungsserver weitergeleitet. Der Anwendungsserver verwendet das Verbindungspooling über Treiber für den Datenzugriff. Dieses Feature ist für Treiber wie JDBC, ODP.NET, OCI usw. verfügbar. Die Treiber können Shardschlüssel erkennen, die im Rahmen der Anforderung angegeben werden. Mit [Oracle Universal Connection Pool (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) für JDBC-Clients können nicht von Oracle stammende Anwendungsclients, z. B. Apache Tomcat und IIS, für die Zusammenarbeit mit Oracle Sharding aktiviert werden. 

Während der ersten Anforderung stellt der Anwendungsserver eine Verbindung mit dem Shard Director in der zugehörigen Region her, um Routinginformationen für den Shard abzurufen, an den die Anforderung weitergeleitet werden muss. Basierend auf dem übergebenen Shardschlüssel leitet der Director den Anwendungsserver an den entsprechenden Shard weiter. Der Anwendungsserver speichert diese Informationen zwischen, indem er eine Zuordnung erstellt. Für nachfolgende Anforderungen wird der Shard Director umgangen, und Anforderungen werden direkt an den Shard weitergeleitet.

#### <a name="oracle-sharding-with-goldengate"></a>Oracle Sharding mit GoldenGate

Das folgende Diagramm zeigt eine Referenzarchitektur für Oracle Sharding mit Oracle GoldenGate, um für jeden Shard regionsinterne Hochverfügbarkeit zu erzielen. Im Gegensatz zur vorherigen Architektur wird Hochverfügbarkeit bei dieser Architektur nur innerhalb einer Azure-Region (mehrere Verfügbarkeitszonen) dargestellt. Sie haben die Möglichkeit, mit Oracle GoldenGate eine Sharddatenbank mit mehreren Regionen zu erstellen, die auf Hochverfügbarkeit ausgelegt ist (ähnlich wie im obigen Beispiel).

![Oracle Database Sharding mit Verfügbarkeitszonen per GoldenGate](./media/oracle-reference-architecture/oracledb_gg_sh_az.png)

In der obigen Referenzarchitektur wird das _vom System verwaltete_ Shardverfahren genutzt, um das Sharding für die Daten durchzuführen. Da die Oracle GoldenGate-Replikation auf Blockebene erfolgt, kann die Hälfte der Daten, für die die Replikation durchgeführt werden soll, auf einem ersten Shard repliziert werden. Die andere Hälfte kann dann auf einem anderen Shard repliziert werden. 

Die Art und Weise, wie die Daten repliziert werden, hängt vom Replikationsfaktor ab. Bei einem Replikationsfaktor von „2“ verfügen Sie über zwei Kopien jedes Datenblocks für Ihre drei Shards der Shardgroup. Bei einem Replikationsfaktor von „3“ und drei Shards in der Shardgroup werden entsprechend alle Daten jedes Shards jeweils auf den anderen Shards der Shardgroup repliziert. Jeder Shard der Shardgroup kann einen anderen Replikationsfaktor aufweisen. Mit dieser Einrichtung können Sie Ihren Entwurf zur Erzielung von Hochverfügbarkeit und Notfallwiederherstellung in einer Shardgroup und über mehrere Shardgroups hinweg effizient definieren.

In der obigen Architektur enthalten Shardgroup A und Shardgroup B die gleichen Daten, aber sie sind in unterschiedlichen Verfügbarkeitszonen angeordnet. Wenn sowohl Shardgroup A als auch Shardgroup B den Replikationsfaktor „3“ aufweist, wird jede Zeile bzw. jeder Block Ihrer Shardtabelle für die beiden Shardgroups sechsmal repliziert. Wenn Shardgroup A über den Replikationsfaktor „3“ und Shardgroup B über den Replikationsfaktor „2“ verfügt, wird jede Zeile bzw. jeder Block für die beiden Shardgroups fünfmal repliziert.

Mit dieser Einrichtung kann Datenverlust verhindert werden, wenn auf Instanzebene oder Verfügbarkeitszonenebene ein Fehler auftritt. Die Anwendungsschicht kann für jeden Shard Lese- und Schreibvorgänge durchführen. Zur Verringerung von Konflikten weist Oracle Sharding für jeden Hashwertbereich einen „Masterblock“ zu. Mit dieser Funktion wird sichergestellt, dass Schreibanforderungen für einen bestimmten Block an den entsprechenden Block weitergeleitet werden. Außerdem verfügt Oracle GoldenGate über eine Funktion zum automatischen Erkennen und Lösen von Konflikten, um eventuell auftretende Konflikte zu behandeln. Weitere Informationen und Einschränkungen bei der Implementierung von GoldenGate mit Oracle Sharding finden Sie in der Oracle-Dokumentation unter [Oracle GoldenGate mit einer Sharddatenbank](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html#GUID-4FC0AC46-0B8B-4670-BBE4-052228492C72).

In der obigen Architektur wird in jeder Verfügbarkeitszone ein GSM/Shard Director bereitgestellt, um Hochverfügbarkeit zu erzielen. Wir empfehlen Ihnen, mindestens einen GSM/Shard Director pro Datencenter bzw. Region bereitzustellen. Zusätzlich wird in jeder Verfügbarkeitszone, die eine Shardgroup enthält, eine Instanz des Anwendungsservers bereitgestellt. Diese Einrichtung ermöglicht es, die Latenz zwischen dem Anwendungsserver und der Datenbank bzw. Shardgroup gering zu halten. Wenn für eine Datenbank ein Fehler auftritt, kann der Anwendungsserver in derselben Zone wie die Standbydatenbank Anforderungen verarbeiten, nachdem der Übergang der Datenbankrolle erfolgt ist. Mit Azure Application Gateway und dem Shard Director wird die Latenz der Anforderungen und Antworten nachverfolgt, und Anforderungen werden entsprechend weitergeleitet.

Aus Sicht der Anwendung sendet das Clientsystem eine Anforderung an Azure Application Gateway (oder eine andere Technologie für den Lastenausgleich in Azure), von wo aus diese dann an die Region umgeleitet wird, die dem Client am nächsten ist. Von Azure Application Gateway werden auch beständige Sitzungen (Sticky Sessions) unterstützt. Alle Anforderungen, die von demselben Client stammen, werden an denselben Anwendungsserver weitergeleitet. Der Anwendungsserver verwendet das Verbindungspooling über Treiber für den Datenzugriff. Dieses Feature ist für Treiber wie JDBC, ODP.NET, OCI usw. verfügbar. Die Treiber können Shardschlüssel erkennen, die im Rahmen der Anforderung angegeben werden. Mit [Oracle Universal Connection Pool (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) für JDBC-Clients können nicht von Oracle stammende Anwendungsclients, z. B. Apache Tomcat und IIS, für die Zusammenarbeit mit Oracle Sharding aktiviert werden. 

Während der ersten Anforderung stellt der Anwendungsserver eine Verbindung mit dem Shard Director in der zugehörigen Region her, um Routinginformationen für den Shard abzurufen, an den die Anforderung weitergeleitet werden muss. Basierend auf dem übergebenen Shardschlüssel leitet der Director den Anwendungsserver an den entsprechenden Shard weiter. Der Anwendungsserver speichert diese Informationen zwischen, indem er eine Zuordnung erstellt. Für nachfolgende Anforderungen wird der Shard Director umgangen, und Anforderungen werden direkt an den Shard weitergeleitet.

## <a name="patching-and-maintenance"></a>Patchen und Wartung

Beim Bereitstellen Ihrer Oracle-Workloads in Azure übernimmt Microsoft das gesamte Patchen auf der Ebene des Hostbetriebssystems. Kunden werden im Voraus über alle geplanten Wartungsarbeiten auf Betriebssystemebene informiert, damit sich der Kunde darauf einstellen kann. Zwei Server, die in zwei unterschiedlichen Verfügbarkeitszonen angeordnet sind, werden niemals gleichzeitig gepatcht. Weitere Informationen zur Wartung und zum Patchen von virtuellen Computern finden Sie unter [Verwalten der Verfügbarkeit virtueller Computer](../../manage-availability.md). 

Das Patchen des Betriebssystems Ihres virtuellen Computers kann mit der [Updateverwaltung von Azure Automation](../../../automation/update-management/overview.md) automatisiert werden. Das Patchen und Warten Ihrer Oracle-Datenbank kann mit [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines?view=azure-devops) oder der [Updateverwaltung von Azure Automation](../../../automation/update-management/overview.md) automatisiert und geplant werden, um das Auftreten von Ausfallzeiten zu minimieren. Im Artikel zum Thema [Continuous Delivery und Blau/Grün-Bereitstellung](/azure/devops/learn/what-is-continuous-delivery) können Sie sich damit vertraut machen, wie Sie dies für Ihre Oracle-Datenbanken nutzen können.

## <a name="architecture-and-design-considerations"></a>Architektur- und Entwurfsaspekte

- Erwägen Sie den Einsatz einer [arbeitsspeicheroptimierten Hyperthread-VM](../../sizes-memory.md) mit [vCPUs mit eingeschränkten Kerngrößen](../../../virtual-machines/constrained-vcpu.md) für Ihre Oracle Database-VM, um Lizenzierungskosten zu sparen und die Leistung zu steigern. Verwenden Sie mehrere Premium-Datenträger bzw. Ultra Disks (Managed Disks), um eine gute Leistung und Verfügbarkeit zu erzielen.
- Bei Verwendung von verwalteten Datenträgern kann es vorkommen, dass sich der Name eines Datenträgers bzw. Geräts ändert. Wir empfehlen Ihnen, anstelle des Namens die Geräte-UUID zu verwenden, um sicherzustellen, dass Ihre Bereitstellungen über Neustartvorgänge hinweg beibehalten werden. Weitere Informationen finden Sie [hier](/previous-versions/azure/virtual-machines/linux/configure-raid#add-the-new-file-system-to-etcfstab).
- Verwenden Sie Verfügbarkeitszonen, um für eine Region Hochverfügbarkeit zu erzielen.
- Verwenden Sie ggf. Ultra Disks (falls verfügbar) oder Premium-Datenträger für Ihre Oracle-Datenbank.
- Erwägen Sie, mit Oracle Data Guard eine Oracle-Standbydatenbank in einer anderen Azure-Region einzurichten.
- Erwägen Sie den Einsatz von [Näherungsplatzierungsgruppen](../../../virtual-machines/linux/co-location.md#proximity-placement-groups), um die Latenz zwischen Ihrer Anwendungs- und Datenbankebene zu reduzieren.
- Richten Sie [Oracle Enterprise Manager](https://docs.oracle.com/en/enterprise-manager/) für die Verwaltung, Überwachung und Protokollierung ein.
- Erwägen Sie, Oracle Automatic Storage Management (ASM) zu verwenden, um eine optimierte Speicherverwaltung für Ihre Datenbank zu ermöglichen.
- Nutzen Sie [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines), um das Patchen und Updates für Ihre Datenbank ohne Ausfallzeiten zu verwalten.
- Optimieren Sie Ihren Anwendungscode, indem Sie cloudnative Muster hinzufügen, z. B. [Wiederholungsmuster](/azure/architecture/patterns/retry), [Trennschalter-Muster](/azure/architecture/patterns/circuit-breaker) und andere Muster, die im [Leitfaden „Cloudentwurfsmuster“](/azure/architecture/patterns/) definiert sind, um Ihre Anwendung resilienter zu machen.

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die folgenden Oracle-Referenzartikel, die für Ihr Szenario zutreffen.

- [Einführung in Oracle Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7)
- [Oracle Data Guard Broker: Konzepte](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html)
- [Konfigurieren von Oracle GoldenGate für Hochverfügbarkeit vom Typ „Aktiv/Aktiv“](https://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_bidirectional.htm#GWUAD282)
- [Übersicht über Oracle Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)
- [Oracle Active Data Guard Far Sync: Kein Datenverlust unabhängig von der Entfernung](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)