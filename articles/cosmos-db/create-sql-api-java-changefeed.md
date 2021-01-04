---
title: Erstellen eines End-to-End-Azure Cosmos DB Java SDK v4-Anwendungsbeispiel mit Änderungsfeed
description: In diesem Leitfaden wird Schritt für Schritt eine einfache Java-SQL-API-Anwendung beschrieben, bei der Dokumente in einen Azure Cosmos DB-Container eingefügt werden, während per Änderungsfeed eine materialisierte Sicht des Containers angezeigt wird.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 06/11/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 765fd3afc7fe688d3e6b0e3394e7dc8c39af69b3
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096851"
---
# <a name="how-to-create-a-java-application-that-uses-azure-cosmos-db-sql-api-and-change-feed-processor"></a>Erstellen einer Java-Anwendung, die die Azure Cosmos DB SQL-API und den Änderungsfeedprozessor verwendet
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Dieser Leitfaden führt Sie durch eine einfache Java-Anwendung, die die Azure Cosmos DB SQL-API verwendet, um Dokumente in einen Azure Cosmos DB-Container einzufügen, während mit Änderungsfeed und Änderungsfeedprozessor eine materialisierte Ansicht des Containers beibehalten wird. Die Java-Anwendung kommuniziert mit der Azure Cosmos DB SQL-API unter Verwendung des Azure Cosmos DB Java SDK V4.

> [!IMPORTANT]  
> Dieses Tutorial gilt nur für Azure Cosmos DB Java SDK V4. Weitere Informationen finden Sie in den [Versionshinweisen](sql-api-sdk-java-v4.md) zu Azure Cosmos DB Java SDK V4, im [Maven-Repository](https://mvnrepository.com/artifact/com.azure/azure-cosmos), in den [Tipps zur Leistungssteigerung](performance-tips-java-sdk-v4-sql.md) für Azure Cosmos DB Java SDK V4 und im [Leitfaden zur Problembehandlung](troubleshoot-java-sdk-v4-sql.md) für Azure Cosmos DB Java SDK V4. Wenn Sie aktuell eine ältere Version als V4 verwenden, lesen Sie den Leitfaden [Migrieren zu Azure Cosmos DB Java SDK V4](migrate-java-v4-sdk.md), um Hilfe beim Aktualisieren auf V4 zu erhalten.
>

## <a name="prerequisites"></a>Voraussetzungen

* URI und Schlüssel für Ihr Azure Cosmos DB-Konto

* Maven

* Java 8

## <a name="background"></a>Hintergrund

Über den Azure Cosmos DB-Änderungsfeed wird eine ereignisgesteuerte Schnittstelle zum Auslösen von Aktionen als Reaktion auf Dokumenteinfügungen bereitgestellt. Hierfür gibt es viele Anwendungsfälle. Bei Anwendungen mit hoher Lese- und Schreibintensität wird der Änderungsfeed beispielsweise vor allem genutzt, um eine **materialisierte Echtzeitsicht** eines Containers zu erstellen, während dafür Dokumente erfasst werden. Die materialisierte Sicht des Containers enthält dieselben Daten, aber sie ist partitioniert, um effiziente Lesevorgänge zu ermöglichen. So werden für die Anwendung sowohl effiziente Lese- als auch Schreibvorgänge erzielt.

Die Verwaltung der Ereignisse von Änderungsfeeds wird größtenteils von der Änderungsfeedprozessor-Bibliothek durchgeführt, die in das SDK integriert ist. Diese Bibliothek ist leistungsfähig genug, um die Ereignisse von Änderungsfeeds bei Bedarf auf mehrere Worker zu verteilen. Hierfür müssen Sie die Änderungsfeedbibliothek lediglich mit einem Rückruf versehen.

In diesem einfachen Beispiel wird die Änderungsfeedprozessor-Bibliothek mit nur einem Worker veranschaulicht, über den Dokumente für eine materialisierte Sicht erstellt und gelöscht werden.

## <a name="setup"></a>Einrichten

Klonen Sie das Repository mit dem App-Beispiel, falls Sie dies noch nicht getan haben:

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example.git
```

Öffnen Sie im Repositoryverzeichnis ein Terminal. Führen Sie den Buildvorgang für die App durch, indem Sie Folgendes ausführen:

```bash
mvn clean package
```

## <a name="walkthrough"></a>Exemplarische Vorgehensweise

1. Prüfen Sie als Erstes, ob Sie über ein Azure Cosmos DB-Konto verfügen. Öffnen Sie in Ihrem Browser das **Azure-Portal** , und navigieren Sie zu Ihrem Azure Cosmos DB-Konto und dann im linken Bereich zu **Daten-Explorer**.

   :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_account_empty.JPG" alt-text="Azure Cosmos DB-Konto":::

1. Führen Sie die App im Terminal mit dem folgenden Befehl aus:

    ```bash
    mvn exec:java -Dexec.mainClass="com.azure.cosmos.workedappexample.SampleGroceryStore" -DACCOUNT_HOST="your-account-uri" -DACCOUNT_KEY="your-account-key" -Dexec.cleanupDaemonThreads=false
    ```

1. Drücken Sie die EINGABETASTE, wenn Folgendes angezeigt wird:

    ```bash
    Press enter to create the grocery store inventory system...
    ```

    Wechseln Sie anschließend in Ihrem Browser zurück zum Daten-Explorer im Azure-Portal. Sie sehen, dass die Datenbank **GroceryStoreDatabase** mit drei leeren Containern hinzugefügt wurde: 

    * **InventoryContainer** : Der Bestandsdatensatz für unser Lebensmittelgeschäft-Beispiel partitioniert nach ```id``` (eine UUID).
    * **InventoryContainer-pktype** : Eine materialisierte Sicht des Bestandsdatensatzes, die für Abfragen nach ```type``` optimiert ist.
    * **InventoryContainer-leases** : Ein Leasecontainer wird für Änderungsfeeds immer benötigt. Mit Leases wird der Status der App beim Lesen des Änderungsfeeds nachverfolgt.

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_account_resources_lease_empty.JPG" alt-text="Leere Container":::

1. Im Terminal wird eine Eingabeaufforderung angezeigt:

    ```bash
    Press enter to start creating the materialized view...
    ```

    Drücken Sie die EINGABETASTE. Der unten angegebene Codeblock wird ausgeführt, und der Änderungsfeedprozessor wird in einem anderen Thread initialisiert: 

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Asynchrone API

    [!code-java[](~/azure-cosmos-java-sql-app-example/src/main/java/com/azure/cosmos/workedappexample/SampleGroceryStore.java?name=InitializeCFP)]

    ```"SampleHost_1"``` ist der Name des Workers für den Änderungsfeedprozessor. Mit ```changeFeedProcessorInstance.start()``` wird der Änderungsfeedprozessor gestartet.

    Wechseln Sie in Ihrem Browser zurück zum Daten-Explorer im Azure-Portal. Klicken Sie unter dem Container **InventoryContainer-leases** auf **items** , um den Inhalt anzuzeigen. Sie sehen, dass über den Änderungsfeedprozessor Daten in den Leasecontainer eingefügt wurden. Der Prozessor hat dem Worker ```SampleHost_1``` eine Lease für einige Partitionen von **InventoryContainer** zugewiesen.

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_leases.JPG" alt-text="Leases":::

1. Drücken Sie im Terminal erneut die EINGABETASTE. In **InventoryContainer** werden zehn Dokumente eingefügt. Jede Dokumenteinfügung wird im Änderungsfeed als JSON-Code angezeigt. Mit dem folgenden Rückrufcode werden diese Ereignisse verarbeitet, indem die JSON-Dokumente in einer materialisierten Sicht gespiegelt werden:

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Asynchrone API

    [!code-java[](~/azure-cosmos-java-sql-app-example/src/main/java/com/azure/cosmos/workedappexample/SampleGroceryStore.java?name=CFPCallback)]

1. Warten Sie kurz ab, bis der Code fünf bis zehn Sekunden lang ausgeführt wurde. Wechseln Sie dann zurück zum Daten-Explorer im Azure-Portal, und navigieren Sie zu **InventoryContainer > items**. Sie sehen, dass Elemente (items) in den Bestandscontainer eingefügt werden. Achten Sie auf den Partitionsschlüssel (```id```).

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_items.JPG" alt-text="Feedcontainer":::

1. Navigieren Sie im Daten-Explorer zu **InventoryContainer-pktype > items**. Dies ist die materialisierte Sicht. Die Elemente in diesem Container spiegeln **InventoryContainer** wider, weil sie vom Änderungsfeed programmgesteuert eingefügt wurden. Notieren Sie sich den Partitionsschlüssel (```type```). Diese materialisierte Sicht ist für Abfragen optimiert, die nach ```type``` gefiltert werden. Für **InventoryContainer** wäre dies nicht effizient, weil der Container basierend auf ```id``` partitioniert ist.

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_materializedview2.JPG" alt-text="Screenshot: Seite „Daten-Explorer“ für ein Azure Cosmos DB-Konto, auf der „Elemente“ ausgewählt ist":::

1. Wir löschen ein Dokument gleichzeitig aus **InventoryContainer** und aus **InventoryContainer-pktype** , indem wir nur einmal den Aufruf ```upsertItem()``` verwenden. Sehen Sie sich zunächst den Daten-Explorer im Azure-Portal an. Wir löschen das Dokument, für das ```/type == "plums"``` gilt. Es ist unten rot markiert.

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_materializedview-emph-todelete.JPG" alt-text="Screenshot: Seite „Daten-Explorer“ für ein Azure Cosmos DB-Konto, auf der eine bestimmte Element-ID ausgewählt ist":::

    Drücken Sie erneut die EINGABETASTE, um die Funktion ```deleteDocument()``` im Beispielcode aufzurufen. Mit dieser Funktion (siehe unten) wird ein Upsert-Vorgang auf eine neue Version des Dokuments mit ```/ttl == 5``` durchgeführt, um die Gültigkeitsdauer (Time-To-Live, TTL) für das Dokument auf fünf Sekunden festzulegen. 
    
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK V4 (Maven com.azure::azure-cosmos) Asynchrone API

    [!code-java[](~/azure-cosmos-java-sql-app-example/src/main/java/com/azure/cosmos/workedappexample/SampleGroceryStore.java?name=DeleteWithTTL)]

    Der Änderungsfeed ```feedPollDelay``` ist auf 100 ms festgelegt. Aus diesem Grund reagiert der Änderungsfeed auf dieses Update nahezu sofort und ruft wie oben gezeigt ```updateInventoryTypeMaterializedView()``` auf. Mit diesem letzten Funktionsaufruf wird für das neue Dokument mit der Gültigkeitsdauer von fünf Sekunden ein Upsert-Vorgang in **InventoryContainer-pktype** durchgeführt.

    Das Ergebnis ist, dass das Dokument nach fünf Sekunden abläuft und aus beiden Containern gelöscht wird.

    Diese Vorgehensweise ist erforderlich, da vom Änderungsfeed nur beim Einfügen oder Aktualisieren von Elementen Ereignisse ausgegeben werden (und beim Löschen nicht).

1. Drücken Sie noch einmal die EINGABETASTE, um das Programm zu schließen und die Ressourcen zu bereinigen.
