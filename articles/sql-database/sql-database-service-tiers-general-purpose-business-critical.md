---
title: Azure SQL-Datenbank – „Universell“ und „Unternehmenskritisch“ | Microsoft-Dokumentation
description: In diesem Artikel werden die Dienstebenen „Universell“ und „Unternehmenskritisch“ im vCore-basierten Kaufmodell erläutert.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
manager: craigg
ms.date: 02/23/2019
ms.openlocfilehash: e5426bb7c8eba9d58dbf0472360c6ce0b19c9bc4
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66431330"
---
# <a name="azure-sql-database-service-tiers"></a>Dienstebenen für Azure SQL-Datenbank

Azure SQL-Datenbank basiert auf der an die Cloudumgebung angepasste Architektur der SQL Server-Datenbank-Engine, um selbst bei Infrastrukturausfällen eine Verfügbarkeit von 99,99 Prozent sicherzustellen. In Azure SQL-Datenbank werden drei Dienstebenen verwendet, die jeweils unterschiedliche Architekturmodelle aufweisen. Diese Dienstebenen lauten:

- [Universell](sql-database-service-tier-general-purpose.md) ist für die meisten generischen Workloads konzipiert.
- [Unternehmenskritisch](sql-database-service-tier-business-critical.md) ist für Workloads mit geringer Latenz und einem lesbaren Replikat konzipiert.
- [Hyperscale](sql-database-service-tier-hyperscale.md) ist für sehr große Datenbanken (bis zu 100 TB) mit mehreren lesbaren Replikaten konzipiert.

In diesem Artikel werden die Speicher- und Sicherungsaspekte für die Dienstebenen „Universell“ und „Unternehmenskritisch“ im vCore-basierten Kaufmodell beschrieben.

> [!NOTE]
> Informationen zur Dienstebene „Hyperscale“ im vCore-basierten Kaufmodell finden Sie unter [Dienstebene „Hyperscale“](sql-database-service-tier-hyperscale.md). Einen Vergleich zwischen vCore-basiertem Kaufmodell und DTU-basiertem Kaufmodell finden Sie unter [Kaufmodelle für Azure SQL-Datenbank und Ressourcen](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Daten- und Protokollspeicher

Die folgenden Faktoren haben Einfluss darauf, wie viel Speicher für Daten-und Protokolldateien verwendet wird:

- Der zugeordnete Speicher wird für Datendateien (MDF) und Protokolldateien (LDF) verwendet.
- Jede Einzeldatenbank-Computegröße unterstützt eine maximale Datenbankgröße, die standardmäßig bei 32 GB liegt.
- Wenn Sie die erforderliche Einzeldatenbankgröße (die Größe der MDF-Datei) konfigurieren, werden automatisch 30 Prozent zusätzlicher Speicher hinzugefügt, um LDF-Dateien zu unterstützen.
- Die Speichergröße für eine verwaltete SQL-Datenbank-Instanz muss als Vielfaches von 32 GB angegeben werden.
- Sie können eine beliebige Einzeldatenbankgröße zwischen 10 GB und dem unterstützten Maximum auswählen.
  - In den Dienstebenen „Standard“ oder „Universell“ erhöhen oder verringern Sie die Speichergröße in Schritten von 10 GB.
  - In den Dienstebenen „Premium“ oder „Unternehmenskritisch“ erhöhen oder verringern Sie die Speichergröße in Schritten von 250 GB.
- In der Dienstebene „Universell“ wird für `tempdb` eine angefügte SSD verwendet, und diese Speicherkosten sind im V-Kern-Preis enthalten.
- In der Dienstebene „Unternehmenskritisch“ wird für `tempdb` die angefügte SSD für MDF- und LDF-Dateien gemeinsam genutzt, und die `tempdb`-Speicherkosten sind im V-Kern-Preis enthalten.

> [!IMPORTANT]
> Ihnen wird der gesamte Speicher berechnet, der für MDF- und LDF-Dateien zugeordnet ist.

Verwenden Sie [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql) zum Überwachen der aktuellen Gesamtgröße Ihrer MDF- und LDF-Dateien. Verwenden Sie [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql), um die aktuelle Größe der einzelnen MDF- und LDF-Dateien zu überwachen.

> [!IMPORTANT]
> Unter bestimmten Umständen müssen Sie ggf. eine Datenbank verkleinern, um ungenutzten Speicherplatz freizugeben. Weitere Informationen finden Sie unter [Verwalten von Dateispeicherplatz in Azure SQL-Datenbank](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Sicherungen und Speicher

Der Speicher für Datenbanksicherungen wird zugeordnet, um die Funktionen „Point-in-Time-Wiederherstellung“ (Point in Time Restore, PITR) und [Langzeitaufbewahrung](sql-database-long-term-retention.md) (Long Term Retention, LTR) von SQL-Datenbank zu unterstützen. Dieser Speicher wird für jede Datenbank separat zugeordnet und als zwei Arten von getrennten Datenbankgebühren berechnet.

- **PITR**: Einzelne Datenbanksicherungen werden automatisch in den [georedundanten Speicher mit Lesezugriff](../storage/common/storage-designing-ha-apps-with-ragrs.md) (Read-Access Geo-Redundant, RA-GRS) kopiert. Die Speichergröße wird dynamisch erhöht, wenn neue Sicherungen erstellt werden. Der Speicher wird für wöchentliche vollständige Sicherungen, tägliche differenzielle Sicherungen und im 5-Minuten-Takt kopierte Sicherungen von Transaktionsprotokollen verwendet. Der Speicherverbrauch richtet sich nach der Änderungsrate der Datenbank und nach dem Aufbewahrungszeitraum für Sicherungen. Sie können für jede Datenbank eine separate Aufbewahrungsdauer konfigurieren, die zwischen 7 und 35 Tagen liegt. Eine Mindestspeichermenge, die 100 Prozent (1x) der Datenbankgröße entspricht, wird kostenlos zur Verfügung gestellt. Für die meisten Datenbanken reicht diese Menge aus, um Sicherungen für sieben Tage aufzubewahren.
- **LTR**: SQL-Datenbank verfügt über eine Option zum Konfigurieren der Langzeitaufbewahrung von vollständigen Sicherungen für eine Dauer von bis zu zehn Jahren. Wenn Sie eine LTR-Richtlinie einrichten, werden diese Sicherungen automatisch im RA-GRS-Speicher gespeichert. Sie können jedoch steuern, wie häufig die Sicherungen kopiert werden. Zur Einhaltung unterschiedlicher Konformitätsanforderungen können Sie verschiedene Aufbewahrungszeiträume für wöchentliche, monatliche und/oder jährliche Sicherungen auswählen. Wie viel Speicher für LTR-Sicherungen verwendet wird, richtet sich nach der ausgewählten Konfiguration. Sie können den LTR-Preisrechner verwenden, um die Kosten für LTR-Speicher zu schätzen. Weitere Informationen finden Sie unter dem Thema zur [Langzeitaufbewahrung von SQL-Datenbank](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Nächste Schritte

- Ausführliche Informationen zu bestimmten Computegrößen und Speichergrößen für Einzeldatenbanken in den Dienstebenen „Universell“ und „Unternehmenskritisch“ finden Sie unter [Ressourcenlimits für Einzeldatenbanken, die das auf virtuellen Kernen (V-Kernen) basierende (vCore-basierte) Kaufmodell verwenden](sql-database-vcore-resource-limits-single-databases.md).
- Ausführliche Informationen zu bestimmten Computegrößen und Speichergrößen für Pools für elastische Datenbanken in den Dienstebenen „Universell“ und „Unternehmenskritisch“ finden Sie unter [Ressourcenlimits für Pools für elastische Datenbanken, die das vCore-basierte Kaufmodell verwenden](sql-database-vcore-resource-limits-elastic-pools.md).
