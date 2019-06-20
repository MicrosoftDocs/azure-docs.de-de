---
title: Häufig gestellte Fragen zur elastischen Skalierung von Azure SQL | Microsoft Docs
description: Häufig gestellte Fragen zur Elastic Scale-Funktion der Azure SQL-Datenbank.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: b5ba5fadd229fa7119f9af791f7eaedbc984c92a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60584946"
---
# <a name="elastic-database-tools-frequently-asked-questions-faq"></a>Häufig gestellte Fragen (FAQ) zu Tools für elastische Datenbanken

## <a name="if-i-have-a-single-tenant-per-shard-and-no-sharding-key-how-do-i-populate-the-sharding-key-for-the-schema-info"></a>Wie wird der Shardingschlüssel für die Schemainformationen mit Daten aufgefüllt, wenn eine Einzelinstanz pro Shard und kein Shardingschlüssel verwendet wird?

Das Schemainformationenobjekt wird zum Aufteilen von Zusammenführungsszenarien verwendet. Wenn eine Anwendung prinzipiell eine Einzelinstanz darstellt, ist das Split-Merge-Tool nicht erforderlich. Daher besteht auch keine Notwendigkeit, das Schemainformationenobjekt mit Daten aufzufüllen.

## <a name="ive-provisioned-a-database-and-i-already-have-a-shard-map-manager-how-do-i-register-this-new-database-as-a-shard"></a>Ich habe eine Datenbank bereitgestellt und verfüge bereits über einen Shardzuordnungs-Manager. Wie registriere ich diese neue Datenbank als Shard?

Weitere Informationen finden Sie unter [Hinzufügen eines Shards zu einer Anwendung mithilfe der Clientbibliothek für elastische Datenbanken](sql-database-elastic-scale-add-a-shard.md).

## <a name="how-much-do-elastic-database-tools-cost"></a>Was kosten Tools für elastische Datenbanken?

Für die Verwendung der Clientbibliothek für elastische Datenbanken fallen keine Kosten an. Es entstehen nur Kosten für die Azure SQL-Datenbanken, die Sie für Shards verwenden, und für den Shard-Zuordnungs-Manager sowie für die Web-/Workerrollen, die für das Split-Merge-Tool bereitgestellt werden.

## <a name="why-are-my-credentials-not-working-when-i-add-a-shard-from-a-different-server"></a>Warum funktionieren meine Anmeldeinformationen nicht, wenn ich einen Shard von einem anderen Server hinzufüge?

Verwenden Sie keine Anmeldeinformationen im Format „Benutzer-ID=username@servername“, sondern einfach „Benutzer-ID = benutzername“.  Stellen Sie außerdem sicher, dass die Anmeldung "benutzername" über Berechtigungen für den Shard verfügt.

## <a name="do-i-need-to-create-a-shard-map-manager-and-populate-shards-every-time-i-start-my-applications"></a>Muss ich bei jedem Starten meiner Anwendung einen Shardzuordnungs-Manager erstellen und Shards mit Daten auffüllen?

Nein. Das Erstellen eines Shardzuordnungs-Managers (Beispiel: [ShardMapManagerFactory.CreateSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)) ist ein einmaliger Vorgang.  Ihre Anwendung sollte den Aufruf [ShardMapManagerFactory.TryGetSqlShardMapManager()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) beim Start der Anwendung verwenden.  Pro Anwendungsdomäne sollte nur ein solcher Aufruf erfolgen.

## <a name="i-have-questions-about-using-elastic-database-tools-how-do-i-get-them-answered"></a>Ich habe Fragen zur Verwendung von Tools für elastische Datenbanken. Wie erhalte ich Antworten?

Wenden Sie sich bitte über das [SQL-Datenbank-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) an uns.

## <a name="when-i-get-a-database-connection-using-a-sharding-key-i-can-still-query-data-for-other-sharding-keys-on-the-same-shard--is-this-by-design"></a>Wenn ich eine Datenbankverbindung mithilfe eines Shardingschlüssels abrufe, kann ich weiterhin Daten für andere Shardingschlüssel auf dem gleichen Shard abfragen.  Ist dies beabsichtigt?

Die APIs für elastische Skalierung stellen Ihnen eine Verbindung mit der richtigen Datenbank für Ihren Shardingschlüssel zur Verfügung, jedoch keine Filterung von Shardingschlüsseln.  Fügen Sie Ihrer Abfrage bei Bedarf **WHERE** -Klauseln hinzu, um den Bereich für den bereitgestellten Shardingschlüssel einzuschränken.

## <a name="can-i-use-a-different-sql-database-edition-for-each-shard-in-my-shard-set"></a>Kann ich eine andere SQL-Datenbank-Edition für jeden Shard in meiner Shardsammlung verwenden?

Ja, das ist möglich. Ein Shard ist eine einzelne Datenbank, daher kann ein Shard z. B. eine Premium-Edition verwenden, ein anderer eine Standard-Edition. Außerdem kann die Edition eines Shards während der Lebensdauer eines Shards mehrmals horizontal oder vertikal skaliert werden.

## <a name="does-the-split-merge-tool-provision-or-delete-a-database-during-a-split-or-merge-operation"></a>Stellt das Split-Merge-Tool eine Datenbank während eines Aufteilungs- oder Zusammenführungsvorgangs bereit bzw. löscht sie?

Nein. Bei **split** -Vorgängen muss die Zieldatenbank mit dem entsprechenden Schema vorhanden und beim Shard-Zuordnungs-Manager registriert sein.  Für **merge** -Vorgänge müssen Sie den Shard aus dem Shard-Zuordnungs-Manager löschen und dann die Datenbank löschen.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]