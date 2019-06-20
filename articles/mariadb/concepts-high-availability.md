---
title: Hochverfügbarkeitskonzepte in Azure Database for MariaDB
description: Dieses Thema biete Informationen zur Hochverfügbarkeit beim Verwenden von Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: ba561cba21b0b83b6a19fffc2fdfebe8ec8b8ed9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61042145"
---
# <a name="high-availability-concepts-in-azure-database-for-mariadb"></a>Hochverfügbarkeitskonzepte in Azure Database for MariaDB
Der Dienst Azure Database for MariaDB garantiert eine hohe Verfügbarkeit. Die finanziell abgesicherte Vereinbarung zum Servicelevel (SLA) beträgt 99,99 % bei allgemeiner Verfügbarkeit. Es gibt praktisch keine Ausfallzeiten bei der Nutzung dieses Diensts.

## <a name="high-availability"></a>Hochverfügbarkeit
Das Hochverfügbarkeitsmodell (HA) basiert auf integrierten Failovermechanismen, die bei einer Unterbrechung auf Knotenebene greifen. Eine Unterbrechung auf Knotenebene kann aufgrund eines Hardwarefehlers oder als Reaktion auf eine Dienstbereitstellung auftreten.

Änderungen, die an einem Azure Database for MariaDB-Datenbankserver vorgenommen werden, treten immer in Zusammenhang mit einer Transaktion auf. Änderungen werden in Azure Storage synchron aufgezeichnet, wenn die Transaktion commitet wird. Tritt eine Unterbrechung auf Knotenebene auf, erstellt der Datenbankserver automatisch einen neuen Knoten und fügt Datenspeicher an den neuen Knoten an. Alle aktiven Verbindungen werden beendet, und für alle In-flight-Transaktionen wird kein Commit ausgeführt.

## <a name="application-retry-logic-is-essential"></a>Wiederholungslogik für die Anwendung ist unerlässlich
Es ist wichtig, dass MariaDB-Datenbankanwendungen so erstellt werden, dass abgebrochene Verbindungen und Transaktionsfehler erkannt und folglich Wiederholungsversuche ausgeführt werden. Wenn die Anwendung Wiederholungsversuche ausführt, wird die Verbindung der Anwendung transparent an die neu erstellte Instanz umgeleitet, die die Aufgaben der fehlerhaften Instanz übernimmt.

Intern wird in Azure ein Gateway verwendet, um die Verbindungen an die neue Instanz umzuleiten. Bei einer Unterbrechung dauert der gesamte Failovervorgang normalerweise wenige Sekunden. Da die Umleitung intern vom Gateway behandelt wird, bleibt die externe Verbindungszeichenfolge für die Clientanwendung unverändert.

## <a name="scaling-up-or-down"></a>Zentrales Hoch- oder Herunterskalieren
Wenn Azure Database for MariaDB zentral hoch- oder herunterskaliert wird, wird ähnlich wie beim Hochverfügbarkeitsmodell eine neue Serverinstanz mit der angegebenen Größe erstellt. Der vorhandene Datenspeicher wird von der ursprünglichen Instanz getrennt und an die neue Instanz angefügt.

Während des Skalierungsvorgangs tritt eine Unterbrechung der Datenbankverbindungen auf. Die Verbindung der Clientanwendungen wird getrennt, und offene Transaktionen ohne Commit werden abgebrochen. Sobald die Clientanwendung die Verbindung wiederherstellt oder eine neue Verbindung herstellt, leitet das Gateway die Verbindung an die Instanz mit der neuen Größe weiter.

## <a name="next-steps"></a>Nächste Schritte
- Eine Dienstübersicht finden Sie im  [Überblick über Azure Database for MariaDB](overview.md).
