---
title: Lesereplikate – Azure Database for PostgreSQL – Einzelserver
description: In diesem Artikel wird das Feature für Lesereplikate in Azure Database for PostgreSQL (Einzelserver) beschrieben.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/05/2020
ms.openlocfilehash: 8fabf8169270c3162604b6535a6cf2fb07cd9a9d
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422143"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Lesereplikate in Azure Database for PostgreSQL – Einzelserver

Mit dem Feature für Lesereplikate können Sie Daten von einem Azure Database for PostgreSQL-Server auf einen schreibgeschützten Server replizieren. Replikate werden **asynchron** mithilfe der nativen physischen Replikationstechnologie der PostgreSQL-Engine aktualisiert. Sie können vom primären Server auf bis zu fünf Replikate replizieren.

Replikate sind neue Server, die ähnlich wie reguläre Azure Database for PostgreSQL-Server verwaltet werden. Für jedes Lesereplikat werden Ihnen die bereitgestellten Computeressourcen in Form von virtuellen Kernen sowie der Speicher in GB/Monat in Rechnung gestellt.

Erfahren Sie, wie Sie [Replikate erstellen und verwalten](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Einsatzmöglichkeiten von Lesereplikaten
Das Feature für Lesereplikate kann die Leistung und Skalierung von leseintensiven Workloads verbessern. Leseworkloads können in den Replikaten isoliert werden, während Schreibworkloads an den primären Server weitergeleitet werden können. Lesereplikate können auch in einer anderen Region bereitgestellt werden. Außerdem können sie im Fall einer Notfallwiederherstellung zu einem Lese-/Schreibserver hochgestuft werden.

In einem häufig anzutreffenden Szenario verwenden BI- und Analyseworkloads das Lesereplikat als Datenquelle für die Berichterstellung.

Da Replikate schreibgeschützt sind, führen sie nicht direkt zu einer verringerten Auslastung der Schreibkapazität auf dem primären Server.

### <a name="considerations"></a>Überlegungen
Das Feature ist für Szenarien vorgesehen, in denen die Verzögerung akzeptabel und für das Abladen von Abfragen vorgesehen ist. Es ist nicht für Szenarien mit synchroner Replikation gedacht, in denen die Replikatdaten auf dem neuesten Stand sein müssen. Zwischen dem primären Server und dem Replikat entsteht eine messbare Verzögerung. Diese kann abhängig von der Workload und der Latenz zwischen dem primären Server und dem Replikat Minuten oder sogar Stunden betragen. Letztendlich sind die Daten auf dem Replikat mit den Daten auf dem primären Server konsistent. Verwenden Sie das Feature für Workloads, für die diese Verzögerung akzeptabel ist. 

> [!NOTE]
> Bei den meisten Workloads bieten Lesereplikate Updates vom primären Server nahezu in Echtzeit. Bei persistenten, sehr schreibintensiven primären Workloads kann die Verzögerung der Replikation jedoch immer weiter anwachsen, bis der Stand des primären Servers möglicherweise gar nicht mehr erreicht werden kann. Damit kann auch die Speicherauslastung auf dem primären Server ansteigen, da die WAL-Dateien erst gelöscht werden, wenn sie im Replikat empfangen wurden. Wenn diese Situation andauert, können Sie das Replikat durch das Löschen und erneute Erstellen des Lesereplikats nach Abschluss der schreibintensiven Workloads wieder in einen Zustand mit akzeptabler Verzögerung versetzen.
> Asynchrone Lesereplikate eignen sich nicht für solche schreibintensiven Workloads. Wenn Sie Lesereplikate für Ihre Anwendung auswerten, überwachen Sie die Verzögerung im Replikat über einen vollständigen Workloadzyklus der App. Bewerten Sie dann anhand der Zeiten mit und ohne Spitzenwerte die mögliche Verzögerung und die erwartete RTO/RPO zu den einzelnen Zeitpunkten im Workloadzyklus.
> 
## <a name="cross-region-replication"></a>Regionsübergreifende Replikation
Sie können über Ihren primären Server ein Lesereplikat in einer anderen Region erstellen. Die regionsübergreifende Replikation kann beispielsweise hilfreich sein, um die Notfallwiederherstellung zu planen oder Daten näher beim Benutzer bereitzustellen.

>[!NOTE]
> Server im Tarif „Basic“ unterstützen nur die Replikation in derselben Region.

Ein primärer Server kann in jeder beliebigen [Azure Database for PostgreSQL-Region](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql) vorhanden sein. Ein primärer Server kann ein Replikat in der gekoppelten Region oder den universellen Replikatregionen besitzen. Die folgende Abbildung zeigt, welche Replikatregionen entsprechend Ihrer primären Region verfügbar sind.

[ :::image type="content" source="media/concepts-read-replica/read-replica-regions.png" alt-text="Lesereplikatregionen":::](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Universelle Replikatregionen
Sie können jederzeit ein Lesereplikat in einer der folgenden Regionen erstellen, unabhängig davon, wo sich der primäre Server befindet. Dies sind die universellen Replikatregionen:

Australien, Osten; Australien, Südosten; Brasilien, Süden; Kanada, Mitte; Kanada, Osten; USA, Mitte; Asien, Osten; USA, Osten; USA, Osten 2; Japan, Osten; Japan, Westen; Südkorea, Mitte; Südkorea, Süden; USA, Norden-Mitte; Europa, Norden; USA, Süden-Mitte; Asien, Südosten; Vereinigtes Königreich, Süden; Vereinigtes Königreich, Westen; Europa, Westen; USA, Westen; USA, Westen 2; USA, Westen-Mitte.

### <a name="paired-regions"></a>Regionspaare
Zusätzlich zu den universellen Replikatregionen können Sie ein Lesereplikat in der gekoppelten Azure-Region Ihres primären Servers erstellen. Sollte Ihnen Ihr Regionspaar nicht bekannt sein, lesen Sie den Artikel [Gekoppelte Azure-Regionen](../best-practices-availability-paired-regions.md).

Wenn Sie für die Notfallwiederherstellungsplanung regionsübergreifende Replikate verwenden, empfehlen wir Ihnen, das Replikat in der gekoppelten Region und nicht in einer der anderen Regionen zu erstellen. Regionspaare vermeiden gleichzeitige Aktualisierungen und priorisieren die physische Isolation und die Datenresidenz.  

Es gibt Einschränkungen: 

* Regionale Verfügbarkeit: Azure Database for PostgreSQL ist in den Regionen „Frankreich, Mitte“ und „VAE, Norden“ und „Deutschland, Mitte“ verfügbar. Die entsprechenden gekoppelten Regionen sind allerdings nicht verfügbar.
    
* Unidirektionale Paare: Einige Azure-Regionen werden nur in eine Richtung gekoppelt. Zu diesen Regionen zählen „Indien, Westen“ und „Brasilien, Süden“. 
   Das bedeutet, dass ein primärer Server in der Region „Indien, Westen“ ein Replikat in „Indien, Süden“ erstellen kann. Ein primärer Server in der Region „Indien, Süden“ kann jedoch kein Replikat in „Indien, Westen“ erstellen. Der Grund: Die sekundäre Region von „Indien, Westen“ ist zwar „Indien, Süden“, die sekundäre Region von „Indien, Süden“ ist jedoch nicht „Indien, Westen“.


## <a name="create-a-replica"></a>Erstellen eines Replikats
Wenn Sie den Workflow zum Erstellen eines Replikats starten, wird ein leerer Azure Database for PostgreSQL-Server erstellt. Der neue Server wird mit den Daten gefüllt, die auf dem primären Server vorhanden waren. Die Erstellungszeit hängt von der Datenmenge auf dem primären Server und der verstrichenen Zeit seit der letzten wöchentlichen vollständigen Sicherung ab. Dieser Zeitraum kann wenige Minuten bis zu mehrere Stunden umfassen.

Jedes Replikat ist für die [automatische Vergrößerung](concepts-pricing-tiers.md#storage-auto-grow) von Speicher aktiviert. Das Feature der automatischen Vergrößerung sorgt dafür, dass im Replikat genügend Speicherplatz für die replizierten Daten vorhanden ist, und verhindert damit Unterbrechungen bei der Replikation durch Fehler aufgrund von unzureichendem Speicher.

Das Feature für Lesereplikate verwendet die physische Replikation von PostgreSQL, nicht die logische Replikation. Die Streamingreplikation anhand von Replikationsslots ist der Standardbetriebsmodus. Bei Bedarf wird zum Aufholen der Protokollversand verwendet.

[Erfahren Sie, wie Sie ein Lesereplikat im Azure-Portal erstellen](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Herstellen einer Verbindung mit einem Replikat
Wenn Sie ein Replikat erstellen, erbt dieses weder die Firewallregeln noch den VNET-Dienstendpunkt des primären Servers. Diese Regeln müssen separat für das Replikat eingerichtet werden.

Das Replikat erbt das Administratorkonto vom primären Server. Alle Benutzerkonten auf dem primären Server werden auf die Lesereplikate repliziert. Sie können nur mit denjenigen Benutzerkonten eine Verbindung mit einem Lesereplikat herstellen, die auf dem primären Server verfügbar sind.

Sie können wie bei einem normalen Azure Database for PostgreSQL-Server anhand des Hostnamens und eines gültigen Benutzerkontos eine Verbindung mit dem Replikat herstellen. Für einen Server namens **my replica** mit dem Administratorbenutzernamen **myadmin** können Sie mit psql eine Verbindung mit dem Replikat herstellen:

```bash
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

Geben Sie an der Eingabeaufforderung das Kennwort für das Benutzerkonto ein.

## <a name="monitor-replication"></a>Überwachen der Replikation
Azure Database for PostgreSQL stellt zwei Metriken zum Überwachen der Replikation bereit. Die beiden Metriken sind **Maximale Verzögerung zwischen Replikaten** und **Replikatverzögerung**. Informationen zum Anzeigen dieser Metriken finden Sie im Abschnitt **Monitor a replica** (Überwachen eines Replikats) im Artikel [Howto read replicas](howto-read-replicas-portal.md) (Gewusst wie: Lesereplikate).

Die Metrik **Maximale Verzögerung zwischen Replikaten** zeigt die Verzögerung in Bytes zwischen dem primären Server und dem Replikat mit der größten Verzögerung an. Diese Metrik ist nur auf dem primären Server verfügbar und anwendbar. Außerdem trifft dies nur dann zu, wenn mindestens eines der Lesereplikate mit dem primären Server verbunden ist und sich der primäre Server im Streamingreplikationsmodus befindet. Die Informationen zur Verzögerung enthalten keine Details, wenn das Replikat gerade mithilfe der archivierten Protokolle vom primären Server auf den aktuellen Stand des primären Servers gebracht wird und sich dazu im Dateireplikationsmodus befindet.

Die Metrik **Replikatverzögerung** zeigt die Zeit seit der letzten wiedergegebenen Transaktion an. Wenn auf dem primären Server keine Transaktionen stattfinden, gibt die Metrik diese Verzögerungszeit wieder. Diese Metrik ist nur auf Replikatservern verfügbar und anwendbar. Die Metrik „Replikatverzögerung“ wird aus der Ansicht `pg_stat_wal_receiver` berechnet:

```SQL
SELECT EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Richten Sie eine Benachrichtigung ein, die Sie informiert, wenn die Replikatverzögerung einen für Ihre Workload nicht akzeptablen Wert erreicht. 

Um weitere Erkenntnisse zu erhalten, können Sie die Replikationsverzögerung in Bytes auf allen Replikaten direkt vom primären Server abfragen.

> [!NOTE]
> Wenn ein primärer Server oder ein Lesereplikat neu gestartet wird, spiegelt die Metrik „Replikatverzögerung“ die benötigte Zeit zum Neustarten und anschließenden Aufholen wider.

## <a name="stop-replication--promote-replica"></a>Beenden der Replikation/Höherstufen des Replikats
Sie können die Replikation zwischen einem primären Server und einem Replikat jederzeit beenden. Durch das Beenden wird das Replikat neu gestartet und auf einen unabhängigen, eigenständigen Server mit Lese- und Schreibzugriff heraufgestuft. Bei den Daten auf diesem eigenständigen Server handelt es sich um die Daten, die zum Zeitpunkt der Beendigung der Replikation auf dem Replikatserver vorhanden waren. Alle nachfolgenden Updates auf dem primären Server werden nicht an das Replikat weitergegeben. Möglicherweise gibt es jedoch auf dem Replikatserver akkumulierte Protokolle, die noch nicht angewandt wurden. Während des Neustarts wendet das Replikat alle ausstehenden Protokolle an, bevor Clientverbindungen akzeptiert werden.  

### <a name="considerations"></a>Überlegungen
- Überprüfen Sie vor dem Beenden der Replikation auf einem Lesereplikat die Replikationsverzögerung, um sicherzustellen, dass das Replikat alle erforderlichen Daten enthält. 
- Da das Lesereplikat alle ausstehenden Protokolle anwenden muss, bevor es in einen eigenständigen Server umgewandelt werden kann, ist die RTO für schreibintensive Workloads beim Beenden der Replikation möglicherweise höher, da für das Replikat eine erhebliche Verzögerung auftreten kann. Beachten Sie dies, wenn Sie planen, ein Replikat höher zu stufen.
- Der höher gestufte Replikatserver kann nicht wieder in ein Replikat umgewandelt werden.
- Wenn Sie ein Replikat auf den primären Server höher stufen, können Sie die Replikation mit dem alten primären Server nicht erneut einrichten. Wenn Sie zur alten primären Region zurückwechseln möchten, können Sie entweder einen neuen Replikatserver mit einem neuen Namen erstellen oder den alten primären Server löschen und ein Replikat mit dem Namen des alten primären Servers erstellen.
- Wenn Sie über mehrere Lesereplikate verfügen und eines davon als primären Server höher stufen möchten, bleiben die anderen Replikatserver weiterhin mit dem alten primären Server verbunden. Sie müssen eventuell Replikate vom neuen, höher gestuften Server neu erstellen.

Wenn Sie die Replikation beenden, verliert das Replikat alle Links zu seinem vorherigen primären Server und zu anderen Replikaten.

Erfahren Sie, wie Sie die [Replikation auf ein Replikat beenden](howto-read-replicas-portal.md).

## <a name="failover-to-replica"></a>Failover zum Replikat

Bei einem Fehler des primären Servers erfolgt **kein** automatisches Failover zum Lesereplikat. 

Da die Replikation asynchron erfolgt, könnte eine signifikante Verzögerung zwischen dem primären Server und dem Replikat auftreten. Die Verzögerungsdauer wird durch eine Reihe von Faktoren beeinflusst, z. B. durch den Typ der Workload auf dem primären Server und die Latenz zwischen dem primären und dem Replikatserver. In typischen Fällen mit nominaler Schreibworkload wird von einer Replikatverzögerung zwischen einigen Sekunden und einigen Minuten ausgegangen. In Fällen, in denen auf dem primären Server sehr schreibintensive Workloads ausgeführt werden und das Replikat nicht schnell genug ist, kann die Verzögerung jedoch deutlich höher sein. Sie können die Replikatverzögerung für jedes Replikat mithilfe der Metrik *Replikatverzögerung* nachverfolgen. Die Metrik zeigt die Zeit seit der letzten wiedergegebenen Transaktion im Replikat an. Es wird empfohlen, die durchschnittliche Verzögerung zu ermitteln, indem Sie die Replikatverzögerung über einen bestimmten Zeitraum beobachten. Sie können eine Warnung für die Replikatverzögerung festlegen, sodass Sie benachrichtigt werden und Maßnahmen ergreifen können, wenn sie außerhalb des erwarteten Bereichs liegt.

> [!Tip]
> Wenn Sie ein Failover auf das Replikat durchführen, zeigt die Verzögerung zum Zeitpunkt der Trennung des Replikats vom primären Server an, wie viel Daten verloren gehen.

Gehen Sie folgendermaßen vor, nachdem Sie entschieden haben, ein Failover auf ein Replikat durchzuführen: 

1. Beenden der Replikation auf das Replikat<br/>
   Dieser Schritt ist erforderlich, damit der Replikatserver ein eigenständiger Server werden und Schreibvorgänge akzeptieren kann. Im Rahmen dieses Vorgangs wird der Replikatserver neu gestartet und vom primären Server getrennt. Nachdem Sie die Beendigung der Replikation initiiert haben, dauert es in der Regel einige Minuten, bis die noch nicht angewandten übrigen Protokolle angewandt wurden und die Datenbank als Server mit Lese- und Schreibzugriff geöffnet wird. Informationen zu den Auswirkungen dieser Aktion finden Sie im Abschnitt [Beenden der Replikation](#stop-replication--promote-replica) in diesem Artikel.
    
2. Verweisen der Anwendung auf das (ehemalige) Replikat<br/>
   Jeder Server verfügt über eine eindeutige Verbindungszeichenfolge. Aktualisieren Sie die Verbindungszeichenfolge Ihrer Anwendung so, dass sie auf das (ehemalige) Replikat und nicht auf den primären Server verweist.
    
Wenn die Anwendung erfolgreich Lese- und Schreibvorgänge verarbeitet, haben Sie das Failover abgeschlossen. Die Ausfallzeit Ihrer Anwendung hängt davon ab, ob Sie ein Problem erkennen und die oben beschriebenen Schritte 1 und 2 ausführen.

### <a name="disaster-recovery"></a>Notfallwiederherstellung

Bei einem schwerwiegenden Zwischenfall wie etwa bei Ausfällen auf Zonenebene oder bei regionalen Ausfällen können Sie eine Notfallwiederherstellung durchführen, indem Sie Ihr Lesereplikat höherstufen. Navigieren Sie über das UI-Portal zum Lesereplikatserver. Klicken Sie auf die Registerkarte „Replikation“. Hier können Sie das Replikat beenden, um es auf einen unabhängigen Server hochzustufen. Alternativ können Sie die [Azure-Befehlszeilenschnittstelle](/cli/azure/postgres/server/replica#az_postgres_server_replica_stop) verwenden, um den Replikatserver zu beenden und höherzustufen.

## <a name="considerations"></a>Überlegungen

In diesem Abschnitt werden Aspekte des Features für Lesereplikate zusammengefasst.

### <a name="prerequisites"></a>Voraussetzungen
Lesereplikate und [logische Decodierung](concepts-logical.md) sind beide vom Write-Ahead-Protokoll von Postgres abhängig. Diese beiden Features erfordern unterschiedliche Ebenen der Protokollierung durch Postgres. Die logische Decodierung erfordert einen höheren Protokolliergrad als Lesereplikate.

Um den richtigen Protokolliergrad zu konfigurieren, verwenden Sie den Parameter für die Unterstützung der Azure-Replikation. Für die Unterstützung der Azure-Replikation gibt es drei Einstellungsoptionen:

* **Off**: Speichert am wenigsten Informationen im Write-Ahead-Protokoll. Diese Einstellung ist auf den meisten Azure Database for PostgreSQL-Servern nicht verfügbar.  
* **Replica**: Ausführlichere Informationen als bei **Off**. Dies ist der mindestens erforderliche Protokolliergrad, damit [Lesereplikate](concepts-read-replicas.md) funktionieren. Auf den meisten Servern ist dies die Standardeinstellung.
* **Logical**: Noch ausführlichere Informationen als bei **Replica**. Dies ist der mindestens erforderliche Protokolliergrad, damit die logische Decodierung funktioniert. Lesereplikate funktionieren bei dieser Einstellung ebenfalls.


### <a name="new-replicas"></a>Neue Replikate
Ein Lesereplikat wird als neuer Azure Database for PostgreSQL-Server erstellt. Ein vorhandener Server kann nicht in ein Replikat umgewandelt werden. Es kann kein Replikat eines anderen Lesereplikats erstellt werden.

### <a name="replica-configuration"></a>Replikatkonfiguration
Ein Replikat wird mit den gleichen Compute- und Speichereinstellungen erstellt wie der primäre Server. Nach der Erstellung eines Replikats können einige Einstellungen geändert werden, einschließlich des Aufbewahrungszeitraums für Speicher und Sicherungen.

Firewallregeln, VNET-Regeln und Parametereinstellungen werden beim Erstellen eines Replikats oder danach nicht vom primären Server geerbt.

### <a name="scaling"></a>Skalierung
Skalieren virtueller Kerne zwischen „Universell“ und „Arbeitsspeicheroptimiert“:
* PostgreSQL erfordert, dass die Einstellung `max_connections` auf einem sekundären Server [größer oder gleich der Einstellung auf dem primären Server](https://www.postgresql.org/docs/current/hot-standby.html) ist. Andernfalls wird der sekundäre Server nicht gestartet.
* In Azure Database for PostgreSQL ist die maximal zulässige Anzahl von Verbindungen für jeden Server an die Compute-SKU gebunden, da Verbindungen Speicher belegen. Weitere Informationen zur [Zuordnung zwischen „max_connections“ und Compute-SKUs](concepts-limits.md)
* **Hochskalieren**: Skalieren Sie zuerst die Computekapazität eines Replikats und dann den primären Server hoch. Diese Reihenfolge verhindert, dass die `max_connections`-Anforderung durch Fehler beeinträchtigt wird.
* **Herunterskalieren**: Skalieren Sie zuerst die Computekapazität des primären Servers und dann das Replikat herunter. Wenn Sie auf das Replikat eine geringere Skalierung anwenden als auf den primären Server, tritt ein Fehler auf, weil die `max_connections`-Anforderung verletzt wird.

Skalieren des Speichers:
* Für alle Replikate ist die automatische Speichervergrößerung aktiviert. Dies soll verhindern, dass aufgrund eines vollen Replikatspeichers Replikationsprobleme auftreten. Diese Einstellung kann nicht deaktiviert werden.
* Sie können den Speicher wie bei jedem anderen Server auch manuell hochskalieren.


### <a name="basic-tier"></a>Basic-Tarif
Server im Tarif „Basic“ unterstützen nur die Replikation in derselben Region.

### <a name="max_prepared_transactions"></a>max_prepared_transactions
Für PostgreSQL [muss](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) der Wert des Parameters `max_prepared_transactions` auf dem Lesereplikat mindestens so groß sein wie der Wert auf dem primären Server. Andernfalls wird das Replikat nicht gestartet. Wenn Sie `max_prepared_transactions` auf dem primären Server ändern möchten, ändern Sie den Wert zunächst auf den Replikaten.

### <a name="stopped-replicas"></a>Beendete Replikate
Wenn Sie die Replikation zwischen einem primären Server und einem Lesereplikat beenden, wird das Replikat neu gestartet, um diese Änderung anzuwenden. Das beendete Replikat wird zu einem eigenständigen Server, der sowohl Lese- als auch Schreibvorgänge akzeptiert. Der eigenständige Server kann nicht wieder in ein Replikat umgewandelt werden.

### <a name="deleted-primary-and-standalone-servers"></a>Gelöschte primäre und eigenständige Server
Wenn ein primärer Server gelöscht wird, werden alle zugehörigen Lesereplikate zu eigenständigen Servern. Die Replikate werden neu gestartet, um diese Änderung anzuwenden.

## <a name="next-steps"></a>Nächste Schritte
* Erhalten Sie Informationen zum [Erstellen und Verwalten von Lesereplikaten im Azure-Portal](howto-read-replicas-portal.md).
* Erfahren Sie, wie Sie [Lesereplikate in der Azure-Befehlszeilenschnittstelle (Azure CLI) und der REST-API erstellen und verwalten](howto-read-replicas-cli.md).