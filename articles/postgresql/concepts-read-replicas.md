---
title: Lesereplikate in Azure Database for PostgreSQL – Einzelserver
description: In diesem Artikel wird das Feature für Lesereplikate in Azure Database for PostgreSQL (Einzelserver) beschrieben.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 13580289144d798a57e636f15ab5bce629ff3572
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242291"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Lesereplikate in Azure Database for PostgreSQL – Einzelserver

Mit dem Feature für Lesereplikate können Sie Daten von einem Azure Database for PostgreSQL-Server auf einen schreibgeschützten Server replizieren. Sie können vom Masterserver bis zu fünf Replikate replizieren. Replikate werden mithilfe der nativen Replikationstechnologie der PostgreSQL-Engine asynchron aktualisiert.

> [!IMPORTANT]
> Sie können ein Lesereplikat in derselben Region wie Ihren Masterserver oder in einer anderen beliebigen Azure-Region erstellen. Die regionsübergreifende Replikation befindet sich derzeit in der öffentlichen Vorschauversion.

Replikate sind neue Server, die ähnlich wie reguläre Azure Database for PostgreSQL-Server verwaltet werden. Für jedes Lesereplikat werden Ihnen die bereitgestellten Computeressourcen in Form von virtuellen Kernen sowie der Speicher in GB/Monat in Rechnung gestellt.

Erfahren Sie, wie Sie [Replikate erstellen und verwalten](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Einsatzmöglichkeiten von Lesereplikaten
Das Feature für Lesereplikate kann die Leistung und Skalierung von leseintensiven Workloads verbessern. Leseworkloads können in den Replikaten isoliert werden, während Schreibworkloads an den Masterserver weitergeleitet werden können.

In einem häufig anzutreffenden Szenario verwenden BI- und Analyseworkloads das Lesereplikat als Datenquelle für die Berichterstellung.

Da Replikate schreibgeschützt sind, führen sie nicht direkt zu einer verringerten Auslastung der Schreibkapazität auf dem Master. Dieses Feature ist nicht auf schreibintensive Workloads ausgerichtet.

Das Feature für Lesereplikate verwendet die asynchrone Replikation von PostgreSQL. Das Feature ist nicht für synchrone Replikationsszenarien vorgesehen. Zwischen dem Masterserver und dem Replikat entsteht eine messbare Verzögerung. Letztendlich sind die Daten auf dem Replikat mit den Daten auf dem Masterserver konsistent. Verwenden Sie das Feature für Workloads, für die diese Verzögerung akzeptabel ist.

Lesereplikate können Ihren Notfallwiederherstellungsplan verbessern. Sie müssen zuerst über ein Replikat in einer anderen Azure-Region als dem Master verfügen. Bei einem Regionsnotfall können Sie die Replikation in dieses Replikat beenden und Ihren Workload dorthin umleiten. Das Beenden der Replikation ermöglicht es dem Replikat, sowohl Schreib- als auch Lesezugriffe zu akzeptieren. Weitere Informationen finden Sie im Abschnitt [Beenden der Replikation](#stop-replication). 

## <a name="create-a-replica"></a>Erstellen eines Replikats
Für den Masterserver muss der Parameter `azure.replication_support` auf **REPLICA** festgelegt werden. Bei Änderung dieses Parameters ist ein Neustart des Servers erforderlich, damit die Änderung wirksam wird. (Der Parameter `azure.replication_support` gilt nur für die Tarife „Universell“ und „Arbeitsspeicheroptimiert“.)

Wenn Sie den Workflow zum Erstellen eines Replikats starten, wird ein leerer Azure Database for PostgreSQL-Server erstellt. Der neue Server wird mit den Daten gefüllt, die auf dem Masterserver vorhanden waren. Die Erstellungszeit hängt von der Datenmenge auf dem Masterserver und der verstrichenen Zeit seit der letzten wöchentlichen vollständigen Sicherung ab. Dieser Zeitraum kann wenige Minuten bis zu mehrere Stunden umfassen.

Jedes Replikat ist für die [automatische Vergrößerung](concepts-pricing-tiers.md#storage-auto-grow) von Speicher aktiviert. Das Feature der automatischen Vergrößerung sorgt dafür, dass im Replikat genügend Speicherplatz für die replizierten Daten vorhanden ist, und verhindert damit Unterbrechungen bei der Replikation durch Fehler aufgrund von unzureichendem Speicher.

Das Feature für Lesereplikate verwendet die physische Replikation von PostgreSQL, nicht die logische Replikation. Die Streamingreplikation anhand von Replikationsslots ist der Standardbetriebsmodus. Bei Bedarf wird zum Aufholen der Protokollversand verwendet.

[Erfahren Sie, wie Sie ein Lesereplikat im Azure-Portal erstellen](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Herstellen einer Verbindung mit einem Replikat
Wenn Sie ein Replikat erstellen, erbt dieses weder die Firewallregeln noch den VNET-Dienstendpunkt des Masterservers. Diese Regeln müssen separat für das Replikat eingerichtet werden.

Das Replikat erbt das Administratorkonto vom Masterserver. Alle Benutzerkonten auf dem Masterserver werden auf die Lesereplikate repliziert. Sie können nur mit denjenigen Benutzerkonten eine Verbindung mit einem Lesereplikat herstellen, die auf dem Masterserver verfügbar sind.

Sie können wie bei einem normalen Azure Database for PostgreSQL-Server anhand des Hostnamens und eines gültigen Benutzerkontos eine Verbindung mit dem Replikat herstellen. Für einen Server namens **my replica** mit dem Administratorbenutzernamen **myadmin** können Sie mit psql eine Verbindung mit dem Replikat herstellen:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

Geben Sie an der Eingabeaufforderung das Kennwort für das Benutzerkonto ein.

## <a name="monitor-replication"></a>Überwachen der Replikation
Azure Database for PostgreSQL bietet die Metrik **Maximale Verzögerung für Replikate** in Azure Monitor. Diese Metrik steht nur auf dem Masterserver zur Verfügung. Die Metrik zeigt die Verzögerung in Byte zwischen dem Masterserver und dem Replikat mit der größten Verzögerung. 

Azure Database for PostgreSQL bietet außerdem die Metrik **Replikatverzögerung** in Azure Monitor. Diese Metrik steht nur für Replikate zur Verfügung. 

Die Metrik wird aus der Ansicht `pg_stat_wal_receiver` berechnet:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Die Metrik „Replikatverzögerung“ zeigt die Zeit seit der letzten wiedergegebenen Transaktion an. Wenn auf dem Masterserver keine Transaktionen stattfinden, gibt die Metrik diese Verzögerungszeit wieder.

Richten Sie eine Benachrichtigung ein, die Sie informiert, wenn die Replikatverzögerung einen für Ihre Workload nicht akzeptablen Wert erreicht. 

Um weitere Erkenntnisse zu erhalten, können Sie die Replikationsverzögerung in Bytes auf allen Replikaten direkt vom Masterserver abfragen.

In PostgreSQL, Version 10:

```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), stat.replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

In PostgreSQL, Version 9.6 und früher:

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), stat.replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> Wenn ein Masterserver oder ein Lesereplikat neu gestartet wird, spiegelt die Metrik „Replikatverzögerung“ die benötigte Zeit zum Neustarten und anschließenden Aufholen wider.

## <a name="stop-replication"></a>Beenden der Replikation
Sie können die Replikation zwischen einem Masterserver und einem Replikat beenden. Durch die Beendigungsaktion wird das Replikat neu gestartet, und die zugehörigen Replikationseinstellungen werden entfernt. Sobald die Replikation zwischen einem Masterserver und einem Lesereplikat beendet wurde, wird das Replikat zu einem eigenständigen Server. Bei den Daten auf diesem eigenständigen Server handelt es sich um die Daten, die zu dem Zeitpunkt, als der Befehl zum Beenden der Replikation gestartet wurde, auf dem Replikatserver vorhanden waren. Der eigenständige Server wird nicht zusammen mit dem Masterserver aktualisiert.

> [!IMPORTANT]
> Der eigenständige Server kann nicht wieder in ein Replikat umgewandelt werden.
> Stellen Sie vor dem Beenden der Replikation auf einem Lesereplikat sicher, dass das Replikat alle erforderlichen Daten enthält.

Wenn Sie die Replikation beenden, verliert das Replikat alle Links zu seinem vorherigen Master und zu anderen Replikaten. Zwischen Master und Replikat erfolgt kein automatisierter Failover. 

Erfahren Sie, wie Sie die [Replikation auf ein Replikat beenden](howto-read-replicas-portal.md).


## <a name="considerations"></a>Überlegungen

In diesem Abschnitt werden Aspekte des Features für Lesereplikate zusammengefasst.

### <a name="prerequisites"></a>Voraussetzungen
Vor der Erstellung eines Lesereplikats muss der Parameter `azure.replication_support` auf dem Masterserver auf **REPLICA** festgelegt werden. Bei Änderung dieses Parameters ist ein Neustart des Servers erforderlich, damit die Änderung wirksam wird. Der Parameter `azure.replication_support` gilt nur für die Tarife „Universell“ und „Arbeitsspeicheroptimiert“.

### <a name="new-replicas"></a>Neue Replikate
Ein Lesereplikat wird als neuer Azure Database for PostgreSQL-Server erstellt. Ein vorhandener Server kann nicht in ein Replikat umgewandelt werden. Es kann kein Replikat eines anderen Lesereplikats erstellt werden.

### <a name="replica-configuration"></a>Replikatkonfiguration
Ein Replikat wird mit der gleichen Serverkonfiguration wie der Masterserver erstellt. Nachdem ein Replikat erstellt wurde, können mehrere Einstellungen unabhängig vom Masterserver geändert werden: die Computegeneration, die virtuellen Kerne, der Speicher und der Aufbewahrungszeitraum für Sicherungen. Auch der Tarif kann unabhängig geändert werden, allerdings nicht in den oder aus dem Tarif „Basic“.

> [!IMPORTANT]
> Bevor Sie die Konfiguration eines Masterservers mit neuen Werten aktualisieren, ändern Sie die Replikatkonfiguration in gleiche oder größere Werte. Durch diese Aktion wird sichergestellt, dass das Replikat mit allen Änderungen, die auf dem Masterserver durchgeführt werden, Schritt halten kann.

Für PostgreSQL muss der Wert des Parameters `max_connections` auf dem Lesereplikat mindestens so groß sein wie der Wert auf dem Masterserver. Andernfalls wird das Replikat nicht gestartet. In Azure Database for PostgreSQL basiert der Parameterwert `max_connections` auf der SKU. Weitere Informationen finden Sie unter [Einschränkungen in Azure Database for PostgreSQL](concepts-limits.md). 

Wenn Sie versuchen, die Serverwerte zu aktualisieren, dabei aber nicht die Grenzwerte einhalten, erhalten Sie eine Fehlermeldung.

### <a name="stopped-replicas"></a>Beendete Replikate
Wenn Sie die Replikation zwischen einem Masterserver und einem Lesereplikat beenden, wird das Replikat neu gestartet, um diese Änderung anzuwenden. Das beendete Replikat wird zu einem eigenständigen Server, der sowohl Lese- als auch Schreibvorgänge akzeptiert. Der eigenständige Server kann nicht wieder in ein Replikat umgewandelt werden.

### <a name="deleted-master-and-standalone-servers"></a>Gelöschte Masterserver und eigenständige Server
Wenn ein Masterserver gelöscht wird, werden alle zugehörigen Lesereplikate zu eigenständigen Servern. Die Replikate werden neu gestartet, um diese Änderung anzuwenden.

## <a name="next-steps"></a>Nächste Schritte
Erhalten Sie Informationen zum [Erstellen und Verwalten von Lesereplikaten im Azure-Portal](howto-read-replicas-portal.md).
