---
title: Gespeicherte Prozeduren für die Verwaltung – Azure Database for MariaDB
description: Erfahren Sie, welche gespeicherten Prozeduren in Azure Database for MariaDB hilfreich sind, um die Datenreplikation zu konfigurieren, die Zeitzone festzulegen und Abfragen abzubrechen.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 103bba37f5574185f10f5c4e28e66268da0c7f39
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94536845"
---
# <a name="azure-database-for-mariadb-management-stored-procedures"></a>Gespeicherte Prozeduren für die Azure Database for MariaDB-Verwaltung

Gespeicherte Prozeduren sind auf Azure Database for MariaDB-Servern verfügbar, um Sie beim Verwalten des MariaDB-Servers unterstützen. Dies schließt die Verwaltung der Verbindungen und Abfragen Ihres Servers sowie das Einrichten von Datenreplikation ein.  

## <a name="data-in-replication-stored-procedures"></a>Gespeicherte Prozeduren für die Datenreplikation

Mithilfe der Datenreplikation können Sie Daten von einem MariaDB-Server, der lokal, auf virtuellen Computern oder von Datenbankdiensten ausgeführt wird, die von anderen Cloudanbietern gehostet werden, mit dem Azure Database for MariaDB-Dienst synchronisieren.

Mit den folgenden gespeicherten Prozeduren wird die Datenreplikation zwischen einer Quelle und einem Replikat eingerichtet oder entfernt.

|**Name der gespeicherten Prozedur**|**Eingabeparameter**|**Ausgabeparameter**|**Hinweis zur Verwendung**|
|-----|-----|-----|-----|
|*mysql.az_replication_change_master*|master_host<br/>master_user<br/>master_password<br/>master_port<br/>master_log_file<br/>master_log_pos<br/>master_ssl_ca|–|Um Daten im SSL-Modus zu übertragen, übergeben Sie im Parameter „master_ssl_ca“ den Kontext des Zertifizierungsstellenzertifikats. </br><br>Um Daten ohne SSL zu übertragen, übergeben Sie im Parameter „master_ssl_ca“ eine leere Zeichenfolge.|
|*mysql.az_replication _start*|–|–|Startet die Replikation.|
|*mysql.az_replication _stop*|–|–|Beendet die Replikation.|
|*mysql.az_replication _remove_master*|–|–|Entfernt die Replikationsbeziehung zwischen der Quelle und dem Replikat.|
|*mysql.az_replication_skip_counter*|–|–|Überspringt einen Replikationsfehler.|

Informationen zum Einrichten der Datenreplikation zwischen einer Quelle und einem Replikat in Azure Database for MariaDB finden Sie unter [Konfigurieren der Datenreplikation in Azure Database for MariaDB](howto-data-in-replication.md).

## <a name="other-stored-procedures"></a>Andere gespeicherte Prozeduren

Die folgenden gespeicherten Prozeduren sind in Azure Database for MariaDB zum Verwalten Ihres Servers verfügbar.

|**Name der gespeicherten Prozedur**|**Eingabeparameter**|**Ausgabeparameter**|**Hinweis zur Verwendung**|
|-----|-----|-----|-----|
|*mysql.az_kill*|processlist_id|–|Entspricht dem Befehl [`KILL CONNECTION`](https://dev.mysql.com/doc/refman/8.0/en/kill.html). Beendet die der angegebenen „processlist_id“ zugeordnete Verbindung nach dem Beenden einer beliebigen Anweisung, die von der Verbindung ausgeführt wird.|
|*mysql.az_kill_query*|processlist_id|–|Entspricht dem Befehl [`KILL QUERY`](https://dev.mysql.com/doc/refman/8.0/en/kill.html). Beendet die Anweisung, die derzeit von der Verbindung ausgeführt wird. Die Verbindung bleibt unverändert erhalten.|
|*mysql.az_load_timezone*|–|–|Lädt Zeitzonentabellen, damit der `time_zone`-Parameter auf benannte Werte festgelegt werden kann (z.B. „USA/Pazifik“).|

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie, wie Sie die [Datenreplikation einrichten](howto-data-in-replication.md).
- Erfahren Sie, wie Sie die [Zeitzonentabellen](howto-server-parameters.md#working-with-the-time-zone-parameter) verwenden.