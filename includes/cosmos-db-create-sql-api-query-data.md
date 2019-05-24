---
title: Includedatei
description: Includedatei
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/05/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 9971b16da42cdf1de0464857291c74a947535735
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66151371"
---
Sie können zum Abrufen und Filtern Ihrer Daten Abfragen im Daten-Explorer verwenden.

1. Sehen Sie sich im Daten-Explorer oben auf der Registerkarte **Dokumente** die Standardabfrage `SELECT * FROM c` an. Diese Abfrage ruft alle Dokumente in der Sammlung ab und zeigt sie nach ID sortiert an. 
   
   ![Die Standardabfrage im Daten-Explorer ist „SELECT * FROM c“.](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png)
   
1. Wählen Sie zum Ändern der Abfrage **Filter bearbeiten** aus, ersetzen Sie die Standardabfrage durch`ORDER BY c._ts DESC`, und wählen Sie dann **Filter anwenden** aus.
   
   ![Ändern Sie die Standardabfrage, indem Sie ORDER BY c._ts DESC hinzufügen und auf „Filter anwenden“ klicken.](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png)

   Mithilfe der geänderten Abfrage werden die Dokumente auf Basis ihrer Zeitstempel in absteigender Reihenfolge angezeigt, sodass Ihr zweites Dokument nun zuerst aufgeführt wird. 
   
   ![Abfrage in „ORDER BY c._ts DESC“ geändert und Klicken auf „Filter anwenden“](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png)

Wenn Sie mit der SQL-Syntax vertraut sind, können Sie eine der unterstützten [SQL-Abfragen](../articles/cosmos-db/sql-api-sql-query.md) in das Feld für das Abfrageprädikat eingeben. Der Daten-Explorer kann auch zum Erstellen von gespeicherten Prozeduren, UDFs und Triggern für serverseitige Geschäftslogik verwendet werden. 

Der Daten-Explorer ermöglicht über das Azure-Portal einfachen Zugriff auf die gesamten integrierten programmgesteuerten Datenzugriffsfunktionen in den APIs. Sie verwenden das Portal außerdem zum Skalieren des Durchsatzes, zum Abrufen von Schlüsseln und Verbindungszeichenfolgen sowie zum Überprüfen von Metriken und SLAs für Ihr Azure Cosmos DB-Konto. 

