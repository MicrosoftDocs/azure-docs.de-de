---
title: Lesereplikate – Azure Database for MySQL – Flexible Server
description: 'Weitere Informationen zu Lesereplikaten in Azure Database for MySQL Flexible Server: Erstellen von Replikaten, Herstellen einer Verbindung mit Replikaten und Beenden der Replikation.'
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 10/26/2020
ms.openlocfilehash: 3fe63deb8115c0043023301c6d0dc3731e97743f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492624"
---
# <a name="read-replicas-in-azure-database-for-mysql---flexible-server"></a>Lesereplikate in Azure Database for MySQL – Flexible Server

> [!IMPORTANT]
> Lesereplikate auf flexiblen Azure Database for MySQL-Servern befinden sich in der Vorschauphase.

MySQL ist eine der beliebtesten Datenbank-Engines für die Ausführung von Web- und mobilen Anwendungen im Internet. Viele unserer Kunden verwenden es für ihre Onlinebildungsdienste, Videostreamingdienste, digitalen Zahlungslösungen, E-Commerce-Plattformen, Gamingdienste, Nachrichtenportale sowie für Websites für Behörden und das Gesundheitswesen. Diese Dienste müssen in dem Maße, wie der Datenverkehr im Web oder bei mobilen Anwendungen zunimmt, genutzt und skaliert werden.

Auf der Anwendungsseite wird die Anwendung typischerweise in Java oder PHP entwickelt und migriert, um auf Azure-VM-Skalierungsgruppen oder Azure App Services ausgeführt zu werden, oder sie wird containerisiert, um unter Azure Kubernetes Service (AKS) ausgeführt zu werden. Mit VM-Skalierungsgruppen, App Service oder AKS als zugrunde liegende Infrastruktur wird die Anwendungsskalierung vereinfacht, indem neue virtuelle Computer sofort bereitgestellt und die zustandslosen Komponenten von Anwendungen repliziert werden, um die Anforderungen zu erfüllen, aber oft wird die Datenbank als zentrale zustandsbehaftete Komponente zum Engpass.

Mithilfe des Lesereplikatfeatures können Sie Daten von einer Azure Database for MySQL Flexible Server-Instanz auf einem schreibgeschützten Server replizieren. Sie können vom Quellserver auf bis zu **10** Replikate replizieren. Replikate werden asynchron mithilfe des auf der Position der nativen, binären Protokolldatei (binlog) basierenden Replikationsverfahrens der MySQL-Engine aktualisiert. Weitere Informationen zur binlog-Replikation finden Sie unter [Binary Log File Position Based Replication Configuration Overview](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html) (Konfiguration der auf der Position der binären Protokolldatei basierenden Replikation – Übersicht).

Replikate sind neue Server, die Sie ähnlich wie Ihre flexiblen Azure Database for MySQL-Quellserver verwalten. Für jedes gelesene Replikat fallen Gebühren an, die auf der bereitgestellten Compute-Instanz hinsichtlich der virtuellen Kerne und dem Speicherplatz in GB/Monat basieren. Weitere Informationen finden Sie unter [Preise](./concepts-compute-storage.md#pricing).

Weitere Informationen zu Features und Problemen der MySQL-Replikation finden Sie in der [Dokumentation zur MySQL-Replikation](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

> [!NOTE]
> Unvoreingenommene Kommunikation
>
> Microsoft setzt sich für Diversität und Inklusion ein. In diesem Artikel wird das Wort _Slave_ (Sklave) verwendet. Laut [Microsoft-Styleguide für unvoreingenommene Kommunikation](https://github.com/MicrosoftDocs/microsoft-style-guide/blob/master/styleguide/bias-free-communication.md) sollte dieses Wort jedoch vermieden werden. In diesem Artikel wird es aus Konsistenzgründen verwendet, da das Wort derzeit noch in der Software vorkommt. Wenn die Software aktualisiert und um dieses Wort bereinigt wird, wird auch der Artikel entsprechend aktualisiert.
>

## <a name="common-use-cases-for-read-replica"></a>Allgemeine Anwendungsfälle für Lesereplikate

Das Feature für Lesereplikate kann die Leistung und Skalierung von leseintensiven Workloads verbessern. Leseworkloads können in den Replikaten isoliert werden, während Schreibworkloads an den Quellserver weitergeleitet werden können.

Häufige Szenarien:

* Skalierung von Leseworkloads, die von der Anwendung stammen, unter Verwendung eines einfachen Verbindungsproxys wie [ProxySQL](https://aka.ms/ProxySQLLoadBalanceReplica) oder von auf Microservices basierenden Mustern zum Aufskalieren Ihrer Leseabfragen, die von der Anwendung stammen, um Replikate zu lesen.
* BI- oder analytische Workloads zur Berichterstellung können Lesereplikate als Datenquelle für die Berichterstellung verwenden.
* Für ein IoT- oder Produktionsszenario, bei dem Telemetrieinformationen in der MySQL-Datenbank-Engine erfasst werden, während mehrere Lesereplikate für die Datenberichterstellung verwendet werden.

Da Replikate schreibgeschützt sind, führen sie nicht direkt zu einer verringerten Auslastung der Schreibkapazität auf der Quelle. Dieses Feature ist nicht auf schreibintensive Workloads ausgerichtet.

Das Lesereplikatfeature verwendet die asynchrone MySQL-Replikation. Das Feature ist nicht für synchrone Replikationsszenarien vorgesehen. Zwischen der Quelle und dem Replikat entsteht eine messbare Verzögerung. Letztendlich sind die Daten auf dem Replikat mit den Daten auf dem Quellserver konsistent. Verwenden Sie das Feature für Workloads, für die diese Verzögerung akzeptabel ist.

## <a name="create-a-replica"></a>Erstellen eines Replikats

Wenn ein Quellserver keine vorhandenen Replikatserver aufweist, wird die Quelle zunächst neu gestartet, um sich auf die Replikation vorzubereiten.

Wenn Sie den Workflow zum Erstellen von Replikaten starten, wird ein leerer Azure Database for MySQL-Server erstellt. Der neue Server wird mit den Daten gefüllt, die auf dem Quellserver vorhanden waren. Die Erstellungszeit hängt von der Datenmenge in der Quelle und der verstrichenen Zeit seit der letzten wöchentlichen vollständigen Sicherung ab. Dieser Zeitraum kann wenige Minuten bis zu mehrere Stunden umfassen.

> [!NOTE]
> Lesereplikate werden mit der gleichen Serverkonfiguration wie die Quelle erstellt. Die Replikatserverkonfiguration kann nach der Erstellung geändert werden. Der Replikatserver wird immer in derselben Ressourcengruppe, am selben Standort und im selben Abonnement wie der Quellserver erstellt. Wenn Sie einen Replikatserver in einer anderen Ressourcengruppe oder einem anderen Abonnement erstellen möchten, können Sie nach der Erstellung den [Replikatserver verschieben](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Für die Konfiguration des Replikatservers sollten mindestens die gleichen Werte verwendet werden wie für den Quellserver, damit das Replikat über genügend Kapazität verfügt.

[Erfahren Sie, wie Sie ein Lesereplikat im Azure-Portal erstellen](how-to-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Herstellen einer Verbindung mit einem Replikat

Bei der Erstellung erbt ein Replikat die Konnektivitätsmethode des Quellservers. Sie können die Konnektivitätsmethode des Replikats nicht ändern. Wenn der Quellserver z. B. über **privaten Zugriff (VNet-Integration)** verfügt, kann das Replikat nicht den **öffentlichen Zugriff (zulässige IP-Adressen)** aufweisen.

Das Replikat erbt das Administratorkonto vom Quellserver. Alle Benutzerkonten auf dem Quellserver werden auf die Lesereplikate repliziert. Sie können nur mit denjenigen Benutzerkonten eine Verbindung mit einem Lesereplikat herstellen, die auf dem Quellserver verfügbar sind.

Sie können eine Verbindung zum Replikat herstellen, indem Sie wie bei einer normalen Azure Database for MySQL Flexible Server-Instanz seinen Hostnamen und ein gültiges Benutzerkonto verwenden. Für einen Server namens **myreplica** mit dem Administratorbenutzernamen **myadmin** können Sie eine Verbindung mit dem Replikat herstellen, indem Sie die mysql-Befehlszeilenschnittstelle verwenden:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin -p
```

Geben Sie an der Eingabeaufforderung das Kennwort für das Benutzerkonto ein.

## <a name="monitor-replication"></a>Überwachen der Replikation

Azure Database for MySQL Flexible Server stellt die Metrik „Replication lag in seconds“ (Replikationsverzögerung in Sekunden) in **Azure Monitor** bereit. Diese Metrik steht nur für Replikate zur Verfügung. Diese Metrik wird mithilfe der `seconds_behind_master`-Metrik berechnet, die im MySQL-Befehl `SHOW SLAVE STATUS` verfügbar ist. Legen Sie eine Warnung fest, um Sie zu benachrichtigen, wenn die Replikationsverzögerung einen Wert erreicht, der für Ihre Workload nicht annehmbar ist.

Wenn Sie eine längere Replikationsverzögerung beobachten, lesen Sie [Behandeln von Problemen mit der Replikationswartezeit](./../howto-troubleshoot-replication-latency.md), um mögliche Ursachen zu verstehen und zu beheben.

## <a name="stop-replication"></a>Beenden der Replikation

Sie können die Replikation zwischen einer Quelle und einem Replikat beenden. Sobald die Replikation zwischen einem Quellserver und einem Lesereplikat beendet wurde, wird das Replikat zu einem eigenständigen Server. Bei den Daten auf diesem eigenständigen Server handelt es sich um die Daten, die zu dem Zeitpunkt, als der Befehl zum Beenden der Replikation gestartet wurde, auf dem Replikatserver vorhanden waren. Der eigenständige Server wird nicht zusammen mit dem Quellserver aktualisiert.

Wenn Sie die Replikation zu einem Replikat stoppen, gehen alle Links zu seiner vorherigen Quelle und zu anderen Replikaten verloren. Zwischen einer Quelle und seinem Replikat gibt es kein automatisiertes Failover.

> [!IMPORTANT]
> Der eigenständige Server kann nicht wieder in ein Replikat umgewandelt werden.
> Stellen Sie vor dem Beenden der Replikation auf einem Lesereplikat sicher, dass das Replikat alle erforderlichen Daten enthält.

Erfahren Sie, wie Sie die [Replikation auf ein Replikat beenden](how-to-read-replicas-portal.md).

## <a name="failover"></a>Failover

Zwischen Quell- und Replikatservern erfolgt kein automatisiertes Failover. 

Lesereplikate sind für die Skalierung leseintensiver Workloads gedacht und sind nicht dafür ausgelegt, die Hochverfügbarkeitsanforderungen eines Servers zu erfüllen. Zwischen Quell- und Replikatservern erfolgt kein automatisiertes Failover. Das Beenden der Replikation bei einem Lesereplikat, um es im Lese-/Schreibmodus online zu schalten, ist die Methode, mit der dieser manuelle Failover durchgeführt wird.

Da die Replikation asynchron erfolgt, gibt es eine Verzögerung zwischen der Quelle und dem Replikat. Die Verzögerungsdauer kann durch eine Reihe von Faktoren beeinflusst werden, z. B. durch den Umfang der Workload auf dem Quellserver und die Wartezeit zwischen Rechenzentren. In den meisten Fällen beträgt die Replikatverzögerung einige Sekunden bis zu einigen Minuten. Sie können die tatsächliche Replikatverzögerung mithilfe der Metrik *Replikatverzögerung* nachverfolgen, die für jedes Replikat verfügbar ist. Diese Metrik zeigt die seit der letzten wiedergegebenen Transaktion verstrichene Zeit an. Es wird empfohlen, die durchschnittliche Verzögerung zu ermitteln, indem Sie die Replikatverzögerung über einen bestimmten Zeitraum hinweg beobachten. Sie können eine Warnung für die Replikatverzögerung festlegen, sodass Sie Maßnahmen ergreifen können, wenn sie sich außerhalb des erwarteten Bereichs befindet.

> [!Tip]
> Wenn Sie ein Failover auf das Replikat durchführen, zeigt die Verzögerung zum Zeitpunkt der Trennung des Replikats von der Quelle an, wie viel Daten verloren gehen.

Gehen Sie folgendermaßen vor, nachdem Sie entschieden haben, ein Failover auf ein Replikat durchzuführen: 

1. Beenden der Replikation auf das Replikat<br/>
   Dieser Schritt ist erforderlich, damit der Replikatserver Schreibvorgänge akzeptieren kann. Im Rahmen dieses Vorgangs wird der Replikatserver von der Quelle getrennt. Nachdem Sie die das Beenden der Replikation gestartet haben, dauert der Abschluss des Back-End-Prozesse in der Regel etwa 2 Minuten. Informationen zu den Auswirkungen dieser Aktion finden Sie im Abschnitt [Beenden der Replikation](#stop-replication) in diesem Artikel.
    
2. Verweisen der Anwendung auf das (ehemalige) Replikat<br/>
   Jeder Server verfügt über eine eindeutige Verbindungszeichenfolge. Aktualisieren Sie Ihre Anwendung so, dass Sie auf das (ehemalige) Replikat und nicht auf die Quelle verweist.
    
Wenn die Anwendung erfolgreich Lese- und Schreibvorgänge verarbeitet, haben Sie das Failover abgeschlossen. Die Ausfallzeit Ihrer Anwendung hängt davon ab, ob Sie ein Problem erkennen und die oben beschriebenen Schritte 1 und 2 ausführen.

## <a name="considerations-and-limitations"></a>Überlegungen und Einschränkungen

| Szenario | Einschränkung/Überlegung |
|:-|:-|
| Replikat auf Server mit aktivierter zonenredundanter Hochverfügbarkeit | Nicht unterstützt |
| Regionsübergreifende Lesereplikation | Nicht unterstützt |
| Preise | Die Kosten für den Betrieb des Replikatservers richten sich nach der Region, in der der Replikatserver betrieben wird. |
| Quellserverneustart | Wenn Sie ein Replikat für eine Quelle erstellen, die keine vorhandenen Replikate hat, startet die Quelle zunächst neu, um sich auf die Replikation vorzubereiten. Beachten Sie dies, und führen Sie diese Vorgänge nicht zu Spitzenzeiten durch. |
| Neue Replikate | Ein Lesereplikat wird als neue Azure Database for MySQL Flexible Server-Instanz erstellt. Ein vorhandener Server kann nicht in ein Replikat umgewandelt werden. Es kann kein Replikat eines anderen Lesereplikats erstellt werden. |
| Replikatkonfiguration | Ein Replikat wird mit der gleichen Serverkonfiguration wie der Quellserver erstellt. Nachdem ein Replikat erstellt wurde, können mehrere Einstellungen unabhängig vom Quellserver geändert werden: die Computegeneration, die virtuellen Kerne, der Speicher und der Aufbewahrungszeitraum für Sicherungen. Die Computeebene kann auch unabhängig geändert werden.<br> <br> **WICHTIG**  <br> – Bevor Sie die Konfiguration eines Quellservers mit neuen Werten aktualisieren, sollten Sie die Replikatkonfiguration in die gleichen oder höhere Werte ändern. Durch diese Aktion wird sichergestellt, dass das Replikat mit allen Änderungen, die auf dem Quellserver durchgeführt werden, Schritt halten kann. <br/> Konnektivitätsmethode und Parametereinstellungen werden beim Erstellen eines Replikats vom Quellserver geerbt. Danach sind die Regeln des Replikats unabhängig. |
| Beendete Replikate | Wenn Sie die Replikation zwischen einem Quellserver und einem Lesereplikat beenden, wird das beendete Replikat zu einem eigenständigen Server, der sowohl Lese- als auch Schreibzugriffe akzeptiert. Der eigenständige Server kann nicht wieder in ein Replikat umgewandelt werden. |
| Gelöschte Quellserver und eigenständige Server | Wenn ein Quellserver gelöscht wird, wird die Replikation an alle Lesereplikate beendet. Diese Replikate werden automatisch zu eigenständigen Servern und können sowohl Lese- als auch Schreibvorgänge akzeptieren. Der Quellserver selbst wird gelöscht. |
| Benutzerkonten | Benutzer auf dem Quellserver werden an die Lesereplikate repliziert. Sie können nur mit denjenigen Benutzerkonten eine Verbindung mit einem Lesereplikat herstellen, die auf dem Quellserver verfügbar sind. |
| Serverparameter | Um zu verhindern, dass Daten nicht synchronisiert werden und um einen möglichen Datenverlust oder eine Beschädigung zu vermeiden, sind einige Serverparameter bei der Verwendung von Lesereplikaten für die Aktualisierung gesperrt. <br> Die folgenden Serverparameter sind sowohl auf dem Quell- als auch auf dem Replikatserver gesperrt:<br> - [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/8.0/en/innodb-file-per-table-tablespaces.html) <br> - [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) <br> Der Parameter [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) ist auf den Replikatservern gesperrt. <br> Löschen Sie Replikatserver, aktualisieren Sie den Parameterwert für die Quelle, und erstellen Sie Replikate neu, um einen der oben genannten Parameter auf dem Quellserver zu aktualisieren. |
| Sonstige | – Die Erstellung des Replikats eines Replikats wird nicht unterstützt. <br> – In-Memory-Tabellen können dazu führen, dass Replikate nicht mehr synchron sind. Dies ist eine Einschränkung der MySQL-Replikationstechnologie. Weitere Informationen finden Sie in der [MySQL-Referenzdokumentation](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html). <br>– Stellen Sie sicher, dass die Quellservertabellen über Primärschlüssel verfügen. Das Fehlen von Primärschlüsseln kann zu Replikationslatenz zwischen der Quelle und den Replikaten führen.<br>– Eine vollständige Liste aller Einschränkungen der MySQL-Replikation finden Sie in der [MySQL-Dokumentation](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html). |

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [Lesereplikate über das Azure-Portal erstellen und verwalten](how-to-read-replicas-portal.md).
- Erfahren Sie, wie Sie [Lesereplikate über die Azure CLI erstellen und verwalten](how-to-read-replicas-cli.md).