---
title: Was ist Azure Database Migration Service?
description: Übersicht über Azure Database Migration Service, ein Dienst, der die nahtlose Migration von vielen Datenbankquellen zu Azure-Datenplattformen ermöglicht.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.topic: overview
ms.date: 02/20/2020
ms.openlocfilehash: 328c29afee3752ecb11b83f22d67f20aa3a2c93e
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94963010"
---
# <a name="what-is-azure-database-migration-service"></a>Was ist Azure Database Migration Service?

Azure Database Migration Service ist ein vollständig verwalteter Dienst, der die nahtlose Migration von mehreren Datenbankquellen zu Azure-Datenplattformen mit minimaler Ausfallzeit ermöglicht (Onlinemigrationen).

## <a name="migrate-databases-to-azure-with-familiar-tools"></a>Migrieren von Datenbanken zu Azure mit vertrauten Tools

Azure Database Migration Service integriert einige Funktionen unserer vorhandenen Tools und Dienste. Der Dienst bietet Kunden eine umfassende, hoch verfügbare Lösung. Der Dienst verwendet den [Data Migration Assistant](/sql/dma/dma-overview), um Bewertungsberichte zu generieren, die Empfehlungen bezüglich der Änderungen enthalten, die vor einer Migration erforderlich sind. Sie müssen ggf. erforderliche Wartungsarbeiten durchführen. Wenn Sie bereit sind, den Migrationsprozess zu starten, führt Azure Database Migration Service alle erforderlichen Schritte aus. Sie können den Prozess starten und müssen sich nicht weiter um Ihre Migrationsprojekte kümmern, da Sie sich darauf verlassen können, dass die Migration mit den von Microsoft bestimmten bewährten Methoden erfolgt. 

> [!NOTE]
> Die Verwendung von Azure Database Migration Service zum Ausführen einer Onlinemigration erfordert das Erstellen einer Instanz auf der Grundlage des Premium-Tarifs.

## <a name="regional-availability"></a>Regionale Verfügbarkeit

Neueste Informationen zur regionalen Verfügbarkeit von Azure Database Migration Service finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration).

## <a name="pricing"></a>Preise

Neueste Informationen zu den Preisen von Azure Database Migration Service finden Sie unter [Azure Database Migration Service – Preise](https://azure.microsoft.com/pricing/details/database-migration/).

## <a name="next-steps"></a>Nächste Schritte

* [Status von Migrationsszenarien, die in Azure Database Migration Service unterstützt werden](resource-scenario-status.md)
* [Erstellen einer Azure Database Migration Service-Instanz über das Azure-Portal](quickstart-create-data-migration-service-portal.md)
* [Migrieren von SQL Server zu Azure SQL-Datenbank](tutorial-sql-server-to-azure-sql.md)
* [Übersicht über die Voraussetzungen für die Verwendung von Azure Database Migration Service](pre-reqs.md)
* [Häufig gestellte Fragen zur Verwendung von Azure Database Migration Service](faq.md)
* [Für Datenmigrationsszenarien verfügbare Dienste und Tools](dms-tools-matrix.md)