---
title: Optimieren der Sammlung mit Abfragestatistiken auf einem Azure Database for PostgreSQL-Einzelserver
description: In diesem Artikel wird beschrieben, wie Sie die Sammlung mit Abfragestatistiken auf einem Azure Database for PostgreSQL-Einzelserver optimieren können.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 7425ee7916fd71625f336a7af35f6481d1ed2474
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65068960"
---
# <a name="optimize-query-statistics-collection-on-an-azure-database-for-postgresql---single-server"></a>Optimieren der Sammlung mit Abfragestatistiken auf einem Azure Database for PostgreSQL-Einzelserver
In diesem Artikel wird beschrieben, wie Sie die Sammlung mit Abfragestatistiken auf einem Azure Database for PostgreSQL-Server optimieren können.

## <a name="use-pgstatsstatements"></a>Verwenden von „pg_stats_statements“
**Pg_stat_statements** ist eine PostgreSQL-Erweiterung, die für Azure Database for PostgreSQL standardmäßig aktiviert ist. Die Erweiterung ermöglicht das Nachverfolgen von Ausführungsstatistiken zu allen SQL-Anweisungen, die auf einem Server ausgeführt werden. Dieses Modul greift in jede Abfrageausführung ein und ist mit nicht unerheblichen Leistungseinbußen verbunden. Wenn Sie **pg_stat_statements** aktivieren, wird Abfragetext in Dateien auf dem Datenträger geschrieben.

Bei eindeutigen Abfragen mit langem Abfragetext oder wenn Sie **pg_stat_statements** nicht aktiv überwachen, empfehlen wir, **pg_stat_statements** aus Leistungsgründen zu deaktivieren. Ändern Sie dazu die Einstellung zu `pg_stat_statements.track = NONE`.

Bei einigen Kundenworkloads hat sich eine Leistungsverbesserung in Höhe von 50 Prozent ergeben, nachdem **pg_stat_statements** deaktiviert wurde. Der Nachteil der Deaktivierung von „pg_stat_statements“ besteht darin, das keine Problembehandlung für Leistungsprobleme durchgeführt werden kann.

Gehen Sie wie folgt vor, um `pg_stat_statements.track = NONE` festzulegen:

- Wechseln Sie im Azure-Portal zur [Seite für die PostgreSQL-Ressourcenverwaltung, und wählen Sie das Blatt mit den Serverparametern aus](howto-configure-server-parameters-using-portal.md).

  ![Blatt mit PostgreSQL-Serverparametern](./media/howto-optimize-query-stats-collection/pg_stats_statements_portal.png)

- Gehen Sie mit der [Azure CLI](howto-configure-server-parameters-using-cli.md) wie folgt vor: az postgres server configuration set to `--name pg_stat_statements.track --resource-group myresourcegroup --server mydemoserver --value NONE`.

## <a name="use-the-query-store"></a>Verwenden des Abfragedatenspeichers 
Das Feature [Abfragespeicher](concepts-query-store.md) in Azure Database for PostgreSQL bietet eine effektivere Methode zur Nachverfolgung von Abfragestatistiken. Dieses Feature wird als Alternative zur Verwendung von *pg_stats_statements* empfohlen. 

## <a name="next-steps"></a>Nächste Schritte
Überlegen Sie sich, ob Sie `pg_stat_statements.track = NONE` im [Azure-Portal](howto-configure-server-parameters-using-portal.md) festlegen oder die [Azure CLI](howto-configure-server-parameters-using-cli.md) verwenden sollen.

Weitere Informationen finden Sie unter 
- [Verwendungsszenarien für den Abfragespeicher](concepts-query-store-scenarios.md) 
- [Bewährte Methoden für den Abfragespeicher](concepts-query-store-best-practices.md) 
