---
title: Migration mit minimaler Ausfallzeit – Azure Database for MySQL
description: In diesem Artikel wird beschrieben, wie Sie eine Migration einer MySQL-Datenbank-Instanz nach Azure Database for MySQL mit dem Azure Database Migration Service durchführen können.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 75b548b53de4c0f4ea39959c3d70e9ddb361d64c
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540330"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Migration nach Azure Database for MySQL mit minimaler Ausfallzeit
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

Mit der neu eingeführten **fortlaufenden Synchronisationsfunktion** für den [Azure Database Migration Service](https://aka.ms/get-dms) (DMS) können Sie MySQL-Migrationen nach Azure Database for MySQL mit minimaler Ausfallzeit ausführen. Diese Funktion beschränkt die Ausfallzeit der Anwendung.

Ausführliche Informationen und Anwendungsfälle für die Migration von Datenbanken zu Azure Database for MySQL finden Sie auch im [Leitfaden zur Datenbankmigration](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide). Dieser Leitfaden bietet wichtige Informationen zur erfolgreichen Planung und Ausführung einer MySQL-Migration zu Azure.

## <a name="overview"></a>Übersicht
Azure DMS führt einen ersten Ladevorgang Ihrer lokalen Datenbankinstanz nach Azure Database for MySQL durch und synchronisiert dann kontinuierlich alle neuen Transaktionen mit Azure, während die Anwendung weiterhin ausgeführt wird. Nachdem die Daten auf der Azure-Zielseite erfasst wurden, stoppen Sie die Anwendung für einen kurzen Moment (minimale Ausfallzeit), warten auf den letzten Datenbatch (von dem Zeitpunkt an, an dem Sie die Anwendung stoppen, bis zu dem Moment, in dem die Anwendung tatsächlich nicht mehr verfügbar ist, um neuen Datenverkehr aufzunehmen), um die Erfassung im Ziel aufzuholen. Aktualisieren Sie dann Ihre Verbindungszeichenfolge, um auf Azure zu verweisen. Danach ist Ihre Anwendung auf Azure live geschaltet!

:::image type="content" source="./media/howto-migrate-online/ContinuousSync.png" alt-text="Fortlaufende Synchronisierung mit dem Azure Database Migration Service":::

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zum Migrieren von Datenbanken zu Azure Database for MySQL finden Sie im [Leitfaden zur Datenbankmigration](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).
- Sehen Sie sich das Video [Easily migrate MySQL/PostgreSQL apps to Azure managed service](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201) (in englischer Sprache) an, das Ihnen anhand einer Demo erläutert, wie man MySQL-Anwendungen nach Azure Database for MySQL migriert.
- Sehen Sie sich das Tutorial [Ausführen einer Onlinemigration von MySQL zu Azure Database for MySQL mithilfe von DMS](../dms/tutorial-mysql-azure-mysql-online.md) an.