---
title: include file
description: include file
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/05/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 448c445f49fb4baa300ce6636288a39080da3baf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026939"
---
Sie können zum Abrufen und Filtern Ihrer Daten Abfragen im Daten-Explorer verwenden.

1. Sehen Sie sich im Daten-Explorer oben auf der Registerkarte **Elemente** die Standardabfrage `SELECT * FROM c` an. Diese Abfrage ruft alle Dokumente aus dem Container ab und zeigt sie nach ID sortiert an. 
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png" alt-text="Die Standardabfrage im Daten-Explorer ist „SELECT * FROM c“.":::
   
1. Wählen Sie zum Ändern der Abfrage **Filter bearbeiten** aus, ersetzen Sie die Standardabfrage durch`ORDER BY c._ts DESC`, und wählen Sie dann **Filter anwenden** aus.
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png" alt-text="Ändern Sie die Standardabfrage, indem Sie „ORDER BY c._ts DESC“ hinzufügen und auf „Filter anwenden“ klicken.":::

   Mithilfe der geänderten Abfrage werden die Dokumente auf Basis ihrer Zeitstempel in absteigender Reihenfolge angezeigt, sodass Ihr zweites Dokument nun zuerst aufgeführt wird. 
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png" alt-text="Abfrage in „ORDER BY c._ts DESC“ geändert und Klicken auf „Filter anwenden“":::

Wenn Sie mit der SQL-Syntax vertraut sind, können Sie eine der unterstützten [SQL-Abfragen](../articles/cosmos-db/sql-query-getting-started.md) in das Feld für das Abfrageprädikat eingeben. Der Daten-Explorer kann auch zum Erstellen von gespeicherten Prozeduren, UDFs und Triggern für serverseitige Geschäftslogik verwendet werden. 

Der Daten-Explorer ermöglicht über das Azure-Portal einfachen Zugriff auf die gesamten integrierten programmgesteuerten Datenzugriffsfunktionen in den APIs. Sie verwenden das Portal außerdem zum Skalieren des Durchsatzes, zum Abrufen von Schlüsseln und Verbindungszeichenfolgen sowie zum Überprüfen von Metriken und SLAs für Ihr Azure Cosmos DB-Konto.