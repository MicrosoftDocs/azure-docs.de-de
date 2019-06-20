---
title: Migration nach Azure Database for MySQL mit minimaler Ausfallzeit
description: In diesem Artikel wird beschrieben, wie Sie eine Migration einer MySQL-Datenbank-Instanz nach Azure Database for MySQL mit dem Azure Database Migration Service durchführen können.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/07/2018
ms.openlocfilehash: 49e2662f215d845d416e46246b03e4408fae118b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61424161"
---
# <a name="minimal-downtime-migration-to-azure-database-for-mysql"></a>Migration nach Azure Database for MySQL mit minimaler Ausfallzeit
Mit der neu eingeführten **fortlaufenden Synchronisationsfunktion** für den [Azure Database Migration Service](https://aka.ms/get-dms) (DMS) können Sie MySQL-Migrationen nach Azure Database for MySQL mit minimaler Ausfallzeit ausführen. Diese Funktion beschränkt die Ausfallzeit der Anwendung.

## <a name="overview"></a>Übersicht
Azure DMS führt einen ersten Ladevorgang Ihrer lokalen Datenbankinstanz nach Azure Database for MySQL durch und synchronisiert dann kontinuierlich alle neuen Transaktionen mit Azure, während die Anwendung weiterhin ausgeführt wird. Nachdem die Daten auf der Azure-Zielseite erfasst wurden, stoppen Sie die Anwendung für einen kurzen Moment (minimale Ausfallzeit), warten auf den letzten Datenbatch (von dem Zeitpunkt an, an dem Sie die Anwendung stoppen, bis zu dem Moment, in dem die Anwendung tatsächlich nicht mehr verfügbar ist, um neuen Datenverkehr aufzunehmen), um die Erfassung im Ziel aufzuholen. Aktualisieren Sie dann Ihre Verbindungszeichenfolge, um auf Azure zu verweisen. Danach ist Ihre Anwendung auf Azure live geschaltet!

![Fortlaufende Synchronisierung mit dem Azure Database Migration Service](./media/howto-migrate-online/ContinuousSync.png)

## <a name="next-steps"></a>Nächste Schritte
- Sehen Sie sich das Video [Easily migrate MySQL/PostgreSQL apps to Azure managed service](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201) (in englischer Sprache) an, das Ihnen anhand einer Demo erläutert, wie man MySQL-Anwendungen nach Azure Database for MySQL migriert.
- Sehen Sie sich das Tutorial [Ausführen einer Onlinemigration von MySQL zu Azure Database for MySQL mithilfe von DMS](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online) an.
