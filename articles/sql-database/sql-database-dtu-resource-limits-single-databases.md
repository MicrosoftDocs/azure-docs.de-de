---
title: Azure SQL-Datenbank – auf DTU basierende Ressourcenlimits für Einzeldatenbanken | Microsoft-Dokumentation
description: Diese Seite beschreibt einige allgemeine auf DTU basierende Ressourcenlimits für Einzeldatenbanken in Azure SQL-Datenbank.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/20/2019
ms.openlocfilehash: 0e4d87ee0d0d09a84e960d511ded87dc226515ea
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65762660"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-based-purchasing-model"></a>Ressourcenlimits für Einzeldatenbanken, die das DTU-basierte Kaufmodell verwenden

Dieser Artikel enthält ausführliche Angaben zu Ressourcenlimits für Einzeldatenbanken in Azure SQL-Datenbank, die das DTU-basierte Kaufmodell verwenden.

Informationen zu Ressourcenlimits für Pools für elastische Datenbanken beim DTU-basierten Kaufmodell finden Sie unter [Einschränkungen beim DTU-basierten Kaufmodell für Azure SQL-Datenbank für Pools für elastische Datenbanken (Vorschau)](sql-database-dtu-resource-limits-elastic-pools.md). Informationen zu Einschränkungen beim auf virtuellen Kernen basierenden Kaufmodell finden Sie unter [Einschränkungen beim auf virtuellen Kernen basierenden Kaufmodell für Azure SQL-Datenbank (Vorschau) für eine einzelne Datenbank](sql-database-vcore-resource-limits-single-databases.md) und [Einschränkungen beim auf virtuellen Kernen basierenden Kaufmodell für Azure SQL-Datenbank (Vorschau) für elastische Pools](sql-database-vcore-resource-limits-elastic-pools.md). Weitere Informationen zu den verschiedenen Kaufmodellen finden Sie unter [Kaufmodelle und Dienstebenen](sql-database-purchase-models.md).

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Einzeldatenbank: Speicher- und Computegrößen

Die folgende Tabelle enthält die verfügbaren Ressourcen für Einzeldatenbanken für alle Dienstebenen und Computegrößen. Sie können mit dem [Azure-Portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), der [Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases) oder der [REST-API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases) Dienstebene, Computegröße und Speichermenge für eine einzelne Datenbank festlegen.

> [!IMPORTANT]
> Anleitungen und Überlegungen zur Skalierung finden Sie unter [Skalieren einer Einzeldatenbank](sql-database-single-database-scale.md).

### <a name="basic-service-tier"></a>Basic-Dienstebene

| **Computegröße** | **Basic** |
| :--- | --: |
| Max. DTU-Anzahl | 5 |
| Inbegriffener Speicher (GB) | 2 |
| Max. Speicherlösungen (GB) | 2 |
| Max. In-Memory-OLTP-Speicher (GB) |– |
| Max. gleichzeitige Worker (Anforderungen) | 30 |
| Max. gleichzeitige Sitzungen | 300 |
|||

### <a name="standard-service-tier"></a>Standard-Dienstebene

| **Computegröße** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| Max. DTU-Anzahl | 10 | 20 | 50 | 100 |
| Inbegriffener Speicher (GB) | 250 | 250 | 250 | 250 |
| Max. Speicherlösungen (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Max. In-Memory-OLTP-Speicher (GB) | – | – | – | – |
| Max. gleichzeitige Worker (Anforderungen)| 60 | 90 | 120 | 200 |
| Max. gleichzeitige Sitzungen |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Standard-Dienstebene (Fortsetzung)

| **Computegröße** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| Max. DTU-Anzahl | 200 | 400 | 800 | 1600 | 3000 |
| Inbegriffener Speicher (GB) | 250 | 250 | 250 | 250 | 250 |
| Max. Speicherlösungen (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Max. In-Memory-OLTP-Speicher (GB) | – | – | – | – |– |
| Max. gleichzeitige Worker (Anforderungen)| 400 | 800 | 1600 | 3200 |6000 |
| Max. gleichzeitige Sitzungen |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Premium-Dienstebene

| **Computegröße** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| Max. DTU-Anzahl | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Inbegriffener Speicher (GB) | 500 | 500 | 500 | 500 | 4096* | 4096* |
| Max. Speicherlösungen (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096* | 4096* |
| Max. In-Memory-OLTP-Speicher (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Max. gleichzeitige Worker (Anforderungen)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Max. gleichzeitige Sitzungen | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

\* Von 1024 GB bis zu 4096 GB in Inkrementen von 256 GB

> [!IMPORTANT]
> In allen Regionen außer den folgenden ist im Premium-Tarif derzeit mehr als 1 TB Speicher verfügbar: Regionen „China, Osten“, „China, Norden“, „Deutschland, Mitte“, „Deutschland, Nordosten“, „USA, Westen-Mitte“, „US DoD“ und „US Government, Mitte“. In diesen Regionen ist der Speicher im Tarif „Premium“ auf 1 TB begrenzt.  Weitere Informationen finden Sie unter [Einschränkungen von P11 und P15](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!NOTE]
> Weitere Informationen zu `tempdb`-Einschränkungen finden Sie unter [tempdb-Grenzwerte](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu V-Kern-Ressourcenlimits bei Singletons finden Sie unter [Ressourcenlimits bei Singletons, die das vCore-basierte Kaufmodell verwenden](sql-database-vcore-resource-limits-single-databases.md).
- Informationen zu V-Kern-Ressourcenlimits bei Pools für elastische Datenbanken finden Sie unter [Ressourcenlimits bei Pools für elastische Datenbanken, die das vCore-basierte Kaufmodell verwenden](sql-database-vcore-resource-limits-elastic-pools.md).
- Informationen zu Ressourcenlimits für Pools für elastische Datenbanken finden Sie unter [Ressourcenlimits für Pools für elastische Datenbanken, die das DTU-basierte Kaufmodell verwenden](sql-database-dtu-resource-limits-elastic-pools.md).
- Informationen zu den Ressourcenlimits für verwaltete Instanzen finden Sie unter [Ressourcenlimits bei verwalteten Instanzen](sql-database-managed-instance-resource-limits.md).
- Informationen zu allgemeinen Azure-Einschränkungen finden Sie unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-subscription-service-limits.md).
- Informationen zu Ressourcenlimits auf Server- und Abonnementebene auf einem Datenbankserver finden Sie unter [Übersicht über Ressourcenlimits für einen SQL-Datenbank-Server](sql-database-resource-limits-database-server.md).
