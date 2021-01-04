---
title: Lesereplikate – Azure Database for MariaDB
description: 'Weitere Informationen zu Lesereplikaten in Azure Database for MariaDB: Auswählen von Regionen, Erstellen von Replikaten, Herstellen einer Verbindung mit Replikaten und Beenden der Replikation.'
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: b2dbaa932c01c96582cb038143fa7686707be67d
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541163"
---
# <a name="read-replicas-in-azure-database-for-mariadb"></a>Lesereplikate in Azure Database for MariaDB

Mithilfe des Lesereplikatfeatures können Sie Daten von einem Azure Database for MariaDB-Server auf einen schreibgeschützten Server replizieren. Sie können vom Quellserver auf bis zu fünf Replikate replizieren. Replikate werden asynchron mithilfe des auf der Position der binären Protokolldatei (binlog) basierenden Replikationsverfahrens der MariaDB-Engine und der globalen Transaktions-ID (GTID) aktualisiert. Weitere Informationen zur binlog-Replikation finden Sie [Übersicht über die binlog Replikation](https://mariadb.com/kb/en/library/replication-overview/).

Replikate sind neue Server, die Sie ähnlich wie normale Azure Database for MariaDB-Server verwalten. Für jedes Lesereplikat werden Ihnen die bereitgestellten Computeressourcen in Form von virtuellen Kernen sowie der Speicher in GB/Monat in Rechnung gestellt.

Weitere Informationen zur GTID-Replikation finden Sie in der [Dokumentation zur MariaDB-Replikation](https://mariadb.com/kb/en/library/gtid/).

> [!NOTE]
> Unvoreingenommene Kommunikation
>
> Microsoft setzt sich für Diversität und Inklusion ein. In diesem Artikel wird das Wort _Slave_ (Sklave) verwendet. Laut [Microsoft-Styleguide für unvoreingenommene Kommunikation](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) sollte dieses Wort jedoch vermieden werden. In diesem Artikel wird es aus Konsistenzgründen verwendet, da das Wort derzeit noch in der Software vorkommt. Wenn die Software aktualisiert und um dieses Wort bereinigt wird, wird auch der Artikel entsprechend aktualisiert.
>

## <a name="when-to-use-a-read-replica"></a>Einsatzmöglichkeiten von Lesereplikaten

Das Feature für Lesereplikate kann die Leistung und Skalierung von leseintensiven Workloads verbessern. Leseworkloads können in den Replikaten isoliert werden, während Schreibworkloads an den Masterserver weitergeleitet werden können.

In einem häufig anzutreffenden Szenario verwenden BI- und Analyseworkloads das Lesereplikat als Datenquelle für die Berichterstellung.

Da Replikate schreibgeschützt sind, führen sie nicht direkt zu einer verringerten Auslastung der Schreibkapazität auf dem Master. Dieses Feature ist nicht auf schreibintensive Workloads ausgerichtet.

Das Lesereplikatfeature verwendet die asynchrone Replikation. Das Feature ist nicht für synchrone Replikationsszenarien vorgesehen. Zwischen der Quelle und dem Replikat entsteht eine messbare Verzögerung. Letztendlich sind die Daten auf dem Replikat mit den Daten auf dem Masterserver konsistent. Verwenden Sie das Feature für Workloads, für die diese Verzögerung akzeptabel ist.

## <a name="cross-region-replication"></a>Regionsübergreifende Replikation
Sie können ein Lesereplikat erstellen, das sich in einer anderen Region als Ihr Quellserver befindet. Die regionsübergreifende Replikation kann beispielsweise hilfreich sein, um die Notfallwiederherstellung zu planen oder Daten näher beim Benutzer bereitzustellen.

Sie können einen Quellserver in jeder [Azure Database for MariaDB-Region](https://azure.microsoft.com/global-infrastructure/services/?products=mariadb) haben.  Ein Quellserver kann ein Replikat in der gekoppelten Region oder den universellen Replikatregionen besitzen. Die folgende Abbildung zeigt, welche Replikatregionen entsprechend Ihrer Quellregion verfügbar sind.

[ ![Lesereplikatregionen](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Universelle Replikatregionen
Sie können ein Lesereplikat in einer der folgenden Regionen erstellen, unabhängig davon, wo sich der Quellserver befindet. Folgende universelle Replikatregionen werden unterstützt:

Australien, Osten; Australien, Südosten; Brasilien, Süden; Kanada, Mitte; Kanada, Osten; USA, Mitte; Asien, Osten; USA, Osten; USA, Osten 2; Japan, Osten; Japan, Westen; Südkorea, Mitte; Südkorea, Süden; USA, Norden-Mitte; Europa, Norden; USA, Süden-Mitte; Asien, Südosten; Vereinigtes Königreich, Süden; Vereinigtes Königreich, Westen; Europa, Westen; USA, Westen; USA, Westen 2; USA, Westen-Mitte.

### <a name="paired-regions"></a>Regionspaare
Zusätzlich zu den universellen Replikatregionen können Sie ein Lesereplikat in der gekoppelten Azure-Region Ihres Quellservers erstellen. Sollte Ihnen Ihr Regionspaar nicht bekannt sein, lesen Sie den Artikel [Gekoppelte Azure-Regionen](../best-practices-availability-paired-regions.md).

Wenn Sie für die Notfallwiederherstellungsplanung regionsübergreifende Replikate verwenden, empfehlen wir Ihnen, das Replikat in der gekoppelten Region und nicht in einer der anderen Regionen zu erstellen. Regionspaare vermeiden gleichzeitige Aktualisierungen und priorisieren die physische Isolation und die Datenresidenz.  

Es gibt jedoch einige Einschränkungen: 

* Regionale Verfügbarkeit: Azure Database for MariaDB ist in den Regionen „Frankreich, Mitte“ und „VAE, Norden“ und „Deutschland, Mitte“ verfügbar. Die entsprechenden gekoppelten Regionen sind allerdings nicht verfügbar.
    
* Unidirektionale Paare: Einige Azure-Regionen werden nur in eine Richtung gekoppelt. Zu diesen Regionen zählen „Indien, Westen“, „Brasilien, Süden“ und „US Gov Virginia“. 
   Das bedeutet, dass ein Quellserver in der Region „Indien, Westen“ ein Replikat in „Indien, Süden“ erstellen kann. Ein Quellserver in der Region „Indien, Süden“ kann jedoch kein Replikat in der Region „Indien, Westen“ erstellen. Der Grund: Die sekundäre Region von „Indien, Westen“ ist zwar „Indien, Süden“, die sekundäre Region von „Indien, Süden“ ist jedoch nicht „Indien, Westen“.

## <a name="create-a-replica"></a>Erstellen eines Replikats

> [!IMPORTANT]
> Das Feature für Lesereplikate ist nur für Azure Database for MariaDB-Server in den Tarifen „Universell“ oder „Arbeitsspeicheroptimiert“ verfügbar. Stellen Sie sicher, dass für den Quellserver einer der folgenden Tarife festgelegt ist.

Wenn ein Quellserver keine vorhandenen Replikatserver aufweist, wird die Quelle zunächst neu gestartet, um sich auf die Replikation vorzubereiten.

Wenn Sie den Workflow zum Erstellen von Replikaten starten, wird ein leerer Azure Database for MariaDB-Server erstellt. Der neue Server wird mit den Daten gefüllt, die auf dem Quellserver vorhanden waren. Die Erstellungszeit hängt von der Datenmenge in der Quelle und der verstrichenen Zeit seit der letzten wöchentlichen vollständigen Sicherung ab. Dieser Zeitraum kann wenige Minuten bis zu mehrere Stunden umfassen.

> [!NOTE]
> Wenn Sie auf Ihren Servern keine Speicherwarnung eingerichtet haben, empfehlen wir, dies nachzuholen. Die Warnung informiert Sie, wenn ein Server sich dem Speicherlimit nähert und dadurch die Replikation beeinträchtigt wird.

[Erfahren Sie, wie Sie ein Lesereplikat im Azure-Portal erstellen](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Herstellen einer Verbindung mit einem Replikat

Ein Replikat erbt bei der Erstellung die Firewallregeln des Quellservers. Danach sind diese Regeln vom Quellserver unabhängig.

Das Replikat erbt das Administratorkonto vom Quellserver. Alle Benutzerkonten auf dem Quellserver werden auf die Lesereplikate repliziert. Sie können nur mit denjenigen Benutzerkonten eine Verbindung mit einem Lesereplikat herstellen, die auf dem Quellserver verfügbar sind.

Sie können eine Verbindung mit dem Replikat herstellen, indem Sie wie bei einem normalen Azure Database for MariaDB-Server seinen Hostnamen und ein gültiges Benutzerkonto verwenden. Für einen Server namens **myreplica** mit dem Administratorbenutzernamen **myadmin** können Sie eine Verbindung mit dem Replikat herstellen, indem Sie die mysql-Befehlszeilenschnittstelle verwenden:

```bash
mysql -h myreplica.mariadb.database.azure.com -u myadmin@myreplica -p
```

Geben Sie an der Eingabeaufforderung das Kennwort für das Benutzerkonto ein.

## <a name="monitor-replication"></a>Überwachen der Replikation

Azure Database for MariaDB bietet die Metrik **Replikationsverzögerung in Sekunden** in Azure Monitor. Diese Metrik steht nur für Replikate zur Verfügung.

Diese Metrik wird mithilfe der `seconds_behind_master`-Metrik berechnet, die im MariaDB-Befehl `SHOW SLAVE STATUS` verfügbar ist.

Legen Sie eine Warnung fest, um Sie zu benachrichtigen, wenn die Replikationsverzögerung einen Wert erreicht, der für Ihre Workload nicht annehmbar ist.

## <a name="stop-replication"></a>Beenden der Replikation

Sie können die Replikation zwischen einer Quelle und einem Replikat beenden. Sobald die Replikation zwischen einem Quellserver und einem Lesereplikat beendet wurde, wird das Replikat zu einem eigenständigen Server. Bei den Daten auf diesem eigenständigen Server handelt es sich um die Daten, die zu dem Zeitpunkt, als der Befehl zum Beenden der Replikation gestartet wurde, auf dem Replikatserver vorhanden waren. Der eigenständige Server wird nicht zusammen mit dem Quellserver aktualisiert.

Wenn Sie die Replikation zu einem Replikat stoppen, gehen alle Links zu seiner vorherigen Quelle und zu anderen Replikaten verloren. Zwischen einer Quelle und seinem Replikat gibt es kein automatisiertes Failover.

> [!IMPORTANT]
> Der eigenständige Server kann nicht wieder in ein Replikat umgewandelt werden.
> Stellen Sie vor dem Beenden der Replikation auf einem Lesereplikat sicher, dass das Replikat alle erforderlichen Daten enthält.

Erfahren Sie, wie Sie die [Replikation auf ein Replikat beenden](howto-read-replicas-portal.md).

## <a name="failover"></a>Failover

Zwischen Quell- und Replikatservern erfolgt kein automatisiertes Failover. 

Da die Replikation asynchron erfolgt, gibt es eine Verzögerung zwischen der Quelle und dem Replikat. Die Verzögerungsdauer kann durch eine Reihe von Faktoren beeinflusst werden, z. B. durch den Umfang der Workload auf dem Quellserver und die Wartezeit zwischen Rechenzentren. In den meisten Fällen beträgt die Replikatverzögerung einige Sekunden bis zu einigen Minuten. Sie können die tatsächliche Replikatverzögerung mithilfe der Metrik *Replikatverzögerung* nachverfolgen, die für jedes Replikat verfügbar ist. Diese Metrik zeigt die seit der letzten wiedergegebenen Transaktion verstrichene Zeit an. Es wird empfohlen, die durchschnittliche Verzögerung zu ermitteln, indem Sie die Replikatverzögerung über einen bestimmten Zeitraum hinweg beobachten. Sie können eine Warnung für die Replikatverzögerung festlegen, sodass Sie Maßnahmen ergreifen können, wenn sie sich außerhalb des erwarteten Bereichs befindet.

> [!Tip]
> Wenn Sie ein Failover auf das Replikat durchführen, zeigt die Verzögerung zum Zeitpunkt der Trennung des Replikats von der Quelle an, wie viel Daten verloren gehen.

Gehen Sie folgendermaßen vor, nachdem Sie entschieden haben, ein Failover auf ein Replikat durchzuführen: 

1. Beenden der Replikation auf das Replikat<br/>
   Dieser Schritt ist erforderlich, damit der Replikatserver Schreibvorgänge akzeptieren kann. Im Rahmen dieses Vorgangs wird der Replikatserver vom Master getrennt. Nachdem Sie die das Beenden der Replikation gestartet haben, dauert der Abschluss des Back-End-Prozesse in der Regel etwa 2 Minuten. Informationen zu den Auswirkungen dieser Aktion finden Sie im Abschnitt [Beenden der Replikation](#stop-replication) in diesem Artikel.
    
2. Verweisen der Anwendung auf das (ehemalige) Replikat<br/>
   Jeder Server verfügt über eine eindeutige Verbindungszeichenfolge. Aktualisieren Sie Ihre Anwendung so, dass Sie auf das (ehemalige) Replikat und nicht auf den Master verweist.
    
Wenn die Anwendung erfolgreich Lese- und Schreibvorgänge verarbeitet, haben Sie das Failover abgeschlossen. Die Ausfallzeit Ihrer Anwendung hängt davon ab, ob Sie ein Problem erkennen und die oben beschriebenen Schritte 1 und 2 ausführen.

## <a name="considerations-and-limitations"></a>Überlegungen und Einschränkungen

### <a name="pricing-tiers"></a>Tarife

Lesereplikate sind zurzeit nur in den Tarifen „Universell“ und „Arbeitsspeicheroptimiert“ verfügbar.

> [!NOTE]
> Die Kosten für den Betrieb des Replikatservers richten sich nach der Region, in der der Replikatserver betrieben wird.

### <a name="source-server-restart"></a>Quellserverneustart

Wenn Sie ein Replikat für eine Quelle erstellen, die keine vorhandenen Replikate hat, startet die Quelle zunächst neu, um sich auf die Replikation vorzubereiten. Beachten Sie dies, und führen Sie diese Vorgänge nicht zu Spitzenzeiten durch.

### <a name="new-replicas"></a>Neue Replikate

Ein Lesereplikat wird als neuer Azure Database for MariaDB-Server erstellt. Ein vorhandener Server kann nicht in ein Replikat umgewandelt werden. Es kann kein Replikat eines anderen Lesereplikats erstellt werden.

### <a name="replica-configuration"></a>Replikatkonfiguration

Ein Replikat wird mit der gleichen Serverkonfiguration wie der Masterserver erstellt. Nachdem ein Replikat erstellt wurde, können mehrere Einstellungen unabhängig vom Quellserver geändert werden: die Computegeneration, die virtuellen Kerne, der Speicher, der Aufbewahrungszeitraum für Sicherungen und die Version der MariaDB-Engine. Auch der Tarif kann unabhängig geändert werden, allerdings nicht in den oder aus dem Tarif „Basic“.

> [!IMPORTANT]
> Bevor Sie die Konfiguration eines Quellservers mit neuen Werten aktualisieren, sollten Sie die Replikatkonfiguration in die gleichen oder in höhere Werte ändern. Durch diese Aktion wird sichergestellt, dass das Replikat mit allen Änderungen, die auf dem Masterserver durchgeführt werden, Schritt halten kann.

Firewallregeln und Parametereinstellungen werden beim Erstellen eines Replikats vom Quellserver geerbt. Danach sind die Regeln des Replikats unabhängig.

### <a name="stopped-replicas"></a>Beendete Replikate

Wenn Sie die Replikation zwischen einem Quellserver und einem Lesereplikat beenden, wird das beendete Replikat zu einem eigenständigen Server, der sowohl Lese- als auch Schreibzugriffe akzeptiert. Der eigenständige Server kann nicht wieder in ein Replikat umgewandelt werden.

### <a name="deleted-source-and-standalone-servers"></a>Gelöschte Quellserver und eigenständige Server

Wenn ein Quellserver gelöscht wird, wird die Replikation an alle Lesereplikate beendet. Diese Replikate werden automatisch zu eigenständigen Servern und können sowohl Lese- als auch Schreibvorgänge akzeptieren. Der Quellserver selbst wird gelöscht.

### <a name="user-accounts"></a>Benutzerkonten

Benutzer auf dem Quellserver werden an die Lesereplikate repliziert. Sie können nur mit denjenigen Benutzerkonten eine Verbindung mit einem Lesereplikat herstellen, die auf dem Quellserver verfügbar sind.

### <a name="server-parameters"></a>Serverparameter

Um zu verhindern, dass Daten nicht synchronisiert werden und um einen möglichen Datenverlust oder eine Beschädigung zu vermeiden, sind einige Serverparameter bei der Verwendung von Lesereplikaten für die Aktualisierung gesperrt.

Die folgenden Serverparameter sind sowohl auf dem Quell- als auch auf dem Replikatserver gesperrt:
- [`innodb_file_per_table`](https://mariadb.com/kb/en/library/innodb-system-variables/#innodb_file_per_table) 
- [`log_bin_trust_function_creators`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#log_bin_trust_function_creators)

Der Parameter [`event_scheduler`](https://mariadb.com/kb/en/library/server-system-variables/#event_scheduler) ist auf den Replikatservern gesperrt.

Löschen Sie Replikatserver, aktualisieren Sie den Parameterwert auf dem Master, und erstellen Sie Replikate neu, um einen der oben genannten Parameter auf dem Quellserver zu aktualisieren.

### <a name="other"></a>Andere

- Die Erstellung des Replikats eines Replikats wird nicht unterstützt.
- In-Memory-Tabellen können dazu führen, dass Replikate nicht mehr synchron sind. Dies ist eine Einschränkung der MariaDB-Replikationstechnologie.
- Stellen Sie sicher, dass die Quellservertabellen über Primärschlüssel verfügen. Das Fehlen von Primärschlüsseln kann zu Replikationslatenz zwischen der Quelle und den Replikaten führen.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [Lesereplikate über das Azure-Portal erstellen und verwalten](howto-read-replicas-portal.md).
- Erfahren Sie, wie Sie [Lesereplikate über die Azure CLI und REST-API erstellen und verwalten](howto-read-replicas-cli.md).
