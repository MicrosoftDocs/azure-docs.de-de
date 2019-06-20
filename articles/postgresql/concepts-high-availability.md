---
title: Hochverfügbarkeitskonzepte von Azure Database for PostgreSQL – Einzelserver
description: Dieser Artikel enthält Informationen zur Hochverfügbarkeit bei der Verwendung von Azure Database for PostgreSQL – Einzelserver.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: f54c83099957b4d8795c4049be52d70e8a0e2a61
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65073449"
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql---single-server"></a>Hochverfügbarkeitskonzepte von Azure Database for PostgreSQL – Einzelserver
Der Dienst „Azure Database for PostgreSQL“ bietet eine garantiert hohes Verfügbarkeitsniveau. Die finanziell abgesicherte Vereinbarung zum Servicelevel (SLA) beträgt 99,99 % bei allgemeiner Verfügbarkeit. Es gibt praktisch keine Ausfallzeiten bei der Nutzung dieses Diensts.

## <a name="high-availability"></a>Hochverfügbarkeit
Das Hochverfügbarkeitsmodell (HA) basiert auf integrierten Failovermechanismen, die bei einer Unterbrechung auf Knotenebene greifen. Eine Unterbrechung auf Knotenebene kann aufgrund eines Hardwarefehlers oder als Reaktion auf eine Dienstbereitstellung auftreten.

Änderungen, die an einem Datenbankserver von Azure Database for PostgreSQL vorgenommen werden, treten immer im Kontext einer Transaktion auf. Änderungen werden in Azure Storage synchron aufgezeichnet, wenn die Transaktion commitet wird. Tritt eine Unterbrechung auf Knotenebene auf, erstellt der Datenbankserver automatisch einen neuen Knoten und fügt Datenspeicher an den neuen Knoten an. Alle aktiven Verbindungen werden beendet, und für alle In-flight-Transaktionen wird kein Commit ausgeführt.

## <a name="application-retry-logic-is-essential"></a>Wiederholungslogik für die Anwendung ist unerlässlich
Es ist wichtig, dass PostgreSQL-Datenbankanwendungen so erstellt werden, dass beendete Verbindungen und Transaktionsfehler erkannt und für diese Wiederholungsversuche ausgeführt werden. Wenn die Anwendung Wiederholungsversuche ausführt, wird die Verbindung der Anwendung transparent an die neu erstellte Instanz umgeleitet, die die Aufgaben der fehlerhaften Instanz übernimmt.

Intern wird in Azure ein Gateway verwendet, um die Verbindungen an die neue Instanz umzuleiten. Bei einer Unterbrechung dauert der gesamte Failovervorgang normalerweise wenige Sekunden. Da die Umleitung intern vom Gateway behandelt wird, bleibt die externe Verbindungszeichenfolge für die Clientanwendung unverändert.

## <a name="scaling-up-or-down"></a>Zentrales Hoch- oder Herunterskalieren
Wenn Azure Database for PostgreSQL zentral hoch- oder herunterskaliert wird, wird ähnlich wie beim HA-Modell eine neue Serverinstanz mit der angegebenen Größe erstellt. Der vorhandene Datenspeicher wird von der ursprünglichen Instanz getrennt und an die neue Instanz angefügt.

Während des Skalierungsvorgangs tritt eine Unterbrechung der Datenbankverbindungen auf. Die Verbindung der Clientanwendungen wird getrennt, und offene Transaktionen ohne Commit werden abgebrochen. Sobald die Clientanwendung die Verbindung wiederherstellt oder eine neue Verbindung herstellt, leitet das Gateway die Verbindung an die Instanz mit der neuen Größe weiter. 

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über [Behandlung vorübergehender Verbindungsfehler](concepts-connectivity.md)
- Erfahren Sie, wie Sie [Ihre Daten mit Lesereplikaten replizieren](howto-read-replicas-portal.md)
