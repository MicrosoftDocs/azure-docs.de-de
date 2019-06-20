---
title: Überwachen von Leistungs- und Speichermetriken in Azure Cosmos DB
description: Erfahren Sie, wie Sie Ihr Azure Cosmos DB-Konto anhand von Leistungsmetriken (z. B. Anforderungen und Serverfehler) und Nutzungsmetriken (z. B. Speicherbelegung) überwachen.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 02bbde9a2d744c79cc8a7e95b0732b775c4dc695
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241624"
---
# <a name="monitor-performance-and-storage-metrics-in-azure-cosmos-db"></a>Überwachen von Leistungs- und Speichermetriken in Azure Cosmos DB

Sie können Ihre Azure Cosmos DB-Konten im [Azure-Portal](https://portal.azure.com/) überwachen. Für jedes Konto in Azure Cosmos DB ist eine vollständige Sammlung von Metriken verfügbar, mit der der Durchsatz, der Speicher, die Verfügbarkeit, die Latenz und die Konsistenz überwacht werden kann.

Metriken können auf der Seite „Konto“, der neuen Seite „Metriken“ oder in Azure Monitor überprüft werden.

## <a name="view-performance-metrics-on-the-metrics-page"></a>Anzeigen von Leistungsmetriken auf der Seite „Metriken“
1. Klicken Sie im [Azure-Portal](https://portal.azure.com/) auf **Alle Dienste**, scrollen Sie zu **Datenbanken**, klicken Sie auf **Azure Cosmos DB** und dann auf den Namen des Azure Cosmos DB-Kontos, dessen Leistungsmetriken Sie anzeigen möchten.
2. Wenn die neue Seite geladen wird, klicken Sie im Ressourcenmenü unter **Überwachung** auf **Metriken**.
3. Wenn sich die Seite „Metriken“ öffnet, wählen Sie aus der Dropdownliste **Sammlung(en)** die Sammlung aus, die überprüft werden soll.

   Im Azure-Portal werden die verfügbaren Sammlungsmetriken angezeigt. Beachten Sie, dass Durchsatz-, Speicher-, Verfügbarkeits-, Latenz- und Konsistenzmetriken auf separaten Registerkarten bereitgestellt werden. Um zusätzliche Informationen zu den bereitgestellten Metriken zu erhalten, klicken Sie auf den Doppelpfeil oben rechts im Bereich „Metriken“.

   ![Screenshot des Fokus „Überwachung“, in dem die Sammlung von Metriken angezeigt wird](./media/monitor-accounts/metrics-suite.png)

## <a name="view-performance-metrics-by-using-azure-monitoring"></a>Anzeigen von Leistungsmetriken mithilfe der Azure-Überwachung
1. Klicken Sie im [Azure-Portal](https://portal.azure.com/) auf der linken Leiste auf **Überwachen**.
2. Klicken Sie im Ressourcenmenü auf **Metriken**.
3. Wählen Sie im Fenster **Monitor - Metrics** (Überwachung – Metriken) im Dropdownmenü **Ressourcengruppe** die Ressourcengruppe aus, die dem Azure Cosmos DB-Konto zugeordnet ist, das Sie überwachen möchten. 
4. Wählen Sie im Dropdownmenü **Ressource** das zu überwachende Datenbankkonto aus.
5. Wählen Sie in der Liste mit **verfügbaren Metriken** die anzuzeigenden Metriken aus. Verwenden Sie die STRG-TASTE, um mehrere Metriken auszuwählen. 

## <a name="view-performance-metrics-on-the-account-page"></a>Anzeigen von Leistungsmetriken auf der Seite „Konto“
1. Klicken Sie im [Azure-Portal](https://portal.azure.com/) auf **Alle Dienste**, scrollen Sie zu **Datenbanken**, klicken Sie auf **Azure Cosmos DB** und dann auf den Namen des Azure Cosmos DB-Kontos, dessen Leistungsmetriken Sie anzeigen möchten.
2. Der Fokus **Überwachung** zeigt standardmäßig folgende Kacheln an:
   
   * Gesamtanforderungen des aktuellen Tags.
   * Verwendeter Speicher.
   
   ![Screenshot des Fokus „Überwachung“, in dem die Gesamtzahl der Anforderungen und die Speichernutzung angezeigt werden](./media/monitor-accounts/documentdb-total-requests-and-usage.png)
3. Durch Klicken auf den Doppelpfeil in der oberen rechten Ecke der Kachel **Anforderungen** öffnet sich eine Detailseite namens **Metrik**.
4. Auf der Seite **Metrik** werden die Details zur Gesamtzahl der Anforderungen angezeigt. 

## <a name="set-up-alerts-in-the-portal"></a>Einrichten von Warnungen im Portal
1. Klicken Sie im [Azure-Portal](https://portal.azure.com/) auf **Alle Dienste**, dann auf **Azure Cosmos DB** und anschließend auf den Namen des Azure Cosmos DB-Kontos, für das Sie Warnungen zu Leistungsmetriken einrichten möchten.
2. Klicken Sie im Ressourcenmenü auf **Warnungsregeln**, um die Seite „Warnungsregeln“ zu öffnen.  
   ![Screenshot des ausgewählten Warnungsregelnteils](./media/monitor-accounts/madocdb10.5.png)
3. Klicken Sie auf der Seite **Warnungsregeln** auf **Warnung hinzufügen**.  
   ![Screenshot der Seite „Warnungsregeln“ mit hervorgehobener Schaltfläche „Warnung hinzufügen“](./media/monitor-accounts/madocdb11.png)
4. Geben Sie im Fenster **Warnungsregel hinzufügen** Folgendes an:
   
   * Den Namen der Warnungsregel, die Sie einrichten möchten.
   * Eine Beschreibung der neuen Warnungsregel.
   * Die Metrik für die Warnungsregel.
   * Bedingung, Schwellenwert und Zeitraum die festlegen, wann die Warnung aktiviert wird. Zum Beispiel mehr als 5 Serverfehler im Verlauf der letzten 15 Minuten.
   * Ob der Dienstadministrator oder Coadministratoren bei Auslösung der Warnung eine E-Mail erhalten.
   * Weitere E-Mail-Adressen für Warnbenachrichtigungen.  
     ![Screenshot der Seite „Warnungsregel hinzufügen“](./media/monitor-accounts/madocdb12.png)

## <a name="monitor-azure-cosmos-db-programmatically"></a>Programmgesteuertes Überwachen von Azure Cosmos DB
Die im Portal für Konten verfügbaren Metriken, z. B. für die Speichernutzung von Konten und die Gesamtzahl der Anforderungen, stehen über die SQL-APIs nicht zur Verfügung. Sie können jedoch mithilfe der SQL-APIs die Nutzungsdaten auf Sammlungsebene abrufen. Gehen Sie zum Abrufen von Daten auf Sammlungsebene wie folgt vor:

* Führen Sie zur Verwendung der REST-API [einen GET-Befehl für die Sammlung](https://msdn.microsoft.com/library/mt489073.aspx)aus. Die Kontingent- und Nutzungsinformationen für die Sammlung werden in den Headern „x-ms-resource-quota“ und „x-ms-resource-usage“ in der Antwort zurückgegeben.
* Verwenden Sie für das .NET SDK die Methode [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx), die eine [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) mit mehreren Nutzungseigenschaften zurückgibt, z.B. **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage** sowie weitere Eigenschaften.

Für den Zugriff auf weitere Metriken verwenden Sie das [Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights). Verfügbare Metrikdefinitionen können folgendermaßen aufgerufen werden:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

Abfragen zum Abrufen einzelner Metriken verwenden das folgende Format:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z

Weitere Informationen finden Sie unter [Retrieving Resource Metrics via the Azure Monitor REST API](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/02/23/retrieving-resource-metrics-via-the-azure-insights-api/)(Abrufen von Ressourcenmetriken über die Azure Monitor-REST-API). Beachten Sie, dass „Azure Insights“ in „Azure Monitor“ umbenannt wurde.  In diesem Blogbeitrag wird der ältere Name verwendet.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Kapazitätsplanung für Azure Cosmos DB finden Sie unter [Azure Cosmos DB capacity planner calculator](https://www.documentdb.com/capacityplanner) (Azure Cosmos DB Capacity Planner-Rechner).

