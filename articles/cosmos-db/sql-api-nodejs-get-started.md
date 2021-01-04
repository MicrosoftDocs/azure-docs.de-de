---
title: Node.js-Tutorial für die SQL-API für Azure Cosmos DB
description: Ein Node.js-Tutorial, in dem veranschaulicht wird, wie Sie die SQL-API zum Herstellen einer Verbindung mit und zum Abfragen von Azure Cosmos DB verwenden
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: dech
ms.custom: devx-track-js
ms.openlocfilehash: 1115d7bb0d6857aa39f246743df54a6cf3fd3676
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93098656"
---
# <a name="tutorial-build-a-nodejs-console-app-with-the-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Tutorial: Erstellen einer Node.js-Konsolen-App mit dem JavaScript SDK zum Verwalten von Daten der Azure Cosmos DB-SQL-API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](./create-sql-api-java.md)
> * [Async Java](./create-sql-api-java.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Als Entwickler besitzen Sie möglicherweise Anwendungen, die NoSQL-Dokumentdaten verwenden. Sie können ein SQL-API-Konto in Azure Cosmos DB verwenden, um diese Dokumentdaten zu speichern und darauf zuzugreifen. In diesem Tutorial wird gezeigt, wie Sie eine Node.js-Konsolenanwendung zum Erstellen und Abfragen von Azure Cosmos DB-Ressourcen erstellen.

In diesem Lernprogramm lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines Azure Cosmos DB-Kontos und Herstellen einer Verbindung
> * Einrichten der Anwendung
> * Erstellen einer Datenbank
> * Erstellen Sie einen Container.
> * Hinzufügen von Elementen zum Container
> * Ausführen einfacher Vorgänge für die Elemente, den Container und die Datenbank

## <a name="prerequisites"></a>Voraussetzungen 

Stellen Sie sicher, dass Sie über die folgenden Ressourcen verfügen:

* Ein aktives Azure-Konto. Wenn Sie kein Konto haben, können Sie sich für eine [kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/)registrieren. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js](https://nodejs.org/) v6.0.0 oder höher

## <a name="create-azure-cosmos-db-account"></a>Erstellen eines Azure Cosmos DB-Kontos

Wir erstellen nun ein Azure Cosmos DB-Konto. Wenn Sie bereits über ein Konto verfügen, das Sie verwenden möchten, können Sie diesen Schritt überspringen und mit [Einrichten der Node.js-Anwendung](#SetupNode) fortfahren. Wenn Sie den Azure Cosmos DB-Emulator verwenden, führen Sie die Schritte unter [Azure Cosmos DB-Emulator](local-emulator.md) zum Einrichten des Emulators aus, und fahren Sie dann mit [Einrichten der Node.js-Anwendung](#SetupNode) fort. 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="set-up-your-nodejs-application"></a><a id="SetupNode"></a>Einrichten der Node.js-Anwendung

Bevor Sie Code zum Erstellen der Anwendung schreiben, können Sie das Framework für Ihre App erstellen. Führen Sie die folgenden Schritte aus, um Ihre Node.js-Anwendung mit dem Frameworkcode einzurichten:

1. Öffnen Sie den bevorzugten Terminaldienst.
2. Suchen Sie nach dem Ordner oder Verzeichnis, in dem Sie die Node.js-Anwendung speichern möchten.
3. Erstellen Sie mit den folgenden Befehlen leere JavaScript-Dateien:

   * Windows:
     * `fsutil file createnew app.js 0`
     * `fsutil file createnew config.js 0`
     * `md data`
     * `fsutil file createnew data\databaseContext.js 0`

   * Linux/OS X:
     * `touch app.js`
     * `touch config.js`
     * `mkdir data`
     * `touch data/databaseContext.js`

4. Erstellen und initialisieren Sie eine Datei vom Typ `package.json`. Verwenden Sie den folgenden Befehl:
   * ```npm init -y```

5. Installieren Sie das @azure/cosmos-Modul über npm. Verwenden Sie den folgenden Befehl:
   * ```npm install @azure/cosmos --save```

## <a name="set-your-apps-configurations"></a><a id="Config"></a>Festlegen der Konfigurationen der App

Ihre App ist erstellt. Stellen Sie nun sicher, dass sie mit Azure Cosmos DB kommunizieren kann. Sie können die Kommunikation zwischen Ihrer App und Azure Cosmos DB einrichten, indem Sie einige Konfigurationseinstellungen wie in den folgenden Schritten gezeigt aktualisieren:

1. Öffnen Sie die Datei *config.js* in einem Text-Editor Ihrer Wahl.

1. Kopieren Sie den folgenden Codeausschnitt, und fügen Sie ihn in die Datei *config.js* ein. Legen Sie die Eigenschaften `endpoint` und `key` auf den Endpunkt-URI und den Primärschlüssel Ihrer Azure Cosmos DB-Instanz fest. Die Datenbank- und Containernamen werden auf **Aufgaben** und **Elemente** festgelegt. Der Partitionsschlüssel, den Sie für diese Anwendung verwenden, ist **/category**.

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/config.js":::

   Sie finden den Endpunkt und die Schlüsseldetails im Bereich **Schlüssel** des [Azure-Portals](https://portal.azure.com).

   :::image type="content" source="media/sql-api-nodejs-get-started/node-js-tutorial-keys.png" alt-text="Screenshot: Abrufen von Schlüsseln über das Azure-Portal":::

Das JavaScript SDK verwendet die generischen Begriffe *Container* und *Element*. Ein Container kann eine Sammlung, ein Graph oder eine Tabelle sein. Ein Element kann ein Dokument, ein Edge/Vertex oder eine Zeile sein und stellt den Inhalt eines Containers dar. Im vorherigen Codeausschnitt wird der `module.exports = config;`-Code verwendet, um das Konfigurationsobjekt zu exportieren, sodass Sie in der Datei *app. js* darauf verweisen können.

## <a name="create-a-database-and-a-container"></a>Erstellen einer Datenbank und eines Containers

1. Öffnen Sie die Datei *databaseContext.js* in einem Text-Editor Ihrer Wahl.

1. Kopieren Sie den folgenden Code, und fügen Sie ihn in die Datei *databaseContext.js* ein. Dieser Code definiert eine Funktion, die die Datenbanken „Aufgaben“ und „Elemente“ sowie den Container erstellt, wenn sie nicht bereits in Ihrem Azure Cosmos-Konto vorhanden sind:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/data/databaseContext.js" id="createDatabaseAndContainer":::

   Eine Datenbank ist ein logischer Container für Elemente, die auf Container aufgeteilt sind. Sie erstellen eine Datenbank mithilfe der Funktion `createIfNotExists` oder „create“ der **Databases** -Klasse. Ein Container besteht aus Elementen. Im Falle der SQL-API sind dies JSON-Dokumente. Sie erstellen einen Container mithilfe der Funktion `createIfNotExists` oder „create“ der **Containers** -Klasse. Nachdem Sie einen Container erstellt haben, können Sie die Daten speichern und abfragen.

   > [!WARNING]
   > Die Erstellung eines Containers hat Auswirkungen auf die Preise. Auf der Seite mit den [Preisen](https://azure.microsoft.com/pricing/details/cosmos-db/) finden Sie weitere Informationen.

## <a name="import-the-configuration"></a>Importieren der Konfiguration

1. Öffnen Sie die Datei *app.js* in einem Text-Editor Ihrer Wahl.

1. Kopieren Sie den folgenden Code, und fügen Sie ihn ein, um das Modul `@azure/cosmos`, die Konfiguration und die Datei „databaseContext“, die Sie in den vorherigen Schritten definiert haben, zu importieren. 

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="ImportConfiguration":::

## <a name="connect-to-the-azure-cosmos-account"></a>Herstellen einer Verbindung mit dem Azure Cosmos-Konto

Kopieren Sie in der Datei *app.js* den folgenden Code, und fügen Sie ihn ein, um den zuvor gespeicherten Endpunkt und Schlüssel zum Erstellen eines neuen CosmosClient-Objekts zu verwenden.

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="CreateClientObjectDatabaseContainer":::

> [!Note]
> Deaktivieren Sie beim Herstellen einer Verbindung mit dem **Cosmos DB-Emulator** die TLS-Überprüfung für Ihren Knotenprozess:
>   ```javascript
>   process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
>   const client = new CosmosClient({ endpoint, key });
>   ```

Nachdem Sie nun über den Code zum Initialisieren des Azure Cosmos DB-Clients verfügen, beschäftigen wir uns im nächsten Schritt mit der Verwendung von Azure Cosmos DB-Ressourcen.

## <a name="query-items"></a><a id="QueryItem"></a>Abfrageelemente

Azure Cosmos DB unterstützt umfassende Abfragen der in jedem Container gespeicherten JSON-Elemente. Der folgende Beispielcode zeigt eine Abfrage, die Sie für die Elemente in Ihrem Container ausführen können. Sie können die Elemente abfragen, indem Sie die Abfragefunktion der `Items`-Klasse verwenden. Fügen Sie den folgenden Code in die Datei *app.js* ein, um die Elemente aus Ihrem Azure Cosmos-Konto abzufragen:

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="QueryItems":::

## <a name="create-an-item"></a><a id="CreateItem"></a>Erstellen eines Elements

Ein Element kann mithilfe der create-Funktion der `Items`-Klasse erstellt werden. Bei Verwendung der SQL-API werden Elemente als Dokumente projiziert. Dabei handelt es sich um benutzerdefinierten (beliebigen) JSON-Inhalt. In diesem Tutorial erstellen Sie ein neues Element innerhalb der Datenbank „Aufgaben“.

1. Geben Sie in der Datei „app.js“ die Elementdefinition an:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="DefineNewItem":::

1. Fügen Sie den folgenden Code hinzu, um das zuvor definierte Element zu erstellen:

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="CreateItem":::

## <a name="update-an-item"></a><a id="ReplaceItem"></a>Aktualisieren eines Elements

Azure Cosmos DB unterstützt das Ersetzen von Elementinhalten. Kopieren Sie den folgenden Code, und fügen Sie ihn in die Datei *app.js* ein. Dieser Code ruft ein Element aus dem Container ab und aktualisiert das Feld *isComplete* auf „true“.

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="UpdateItem":::

## <a name="delete-an-item"></a><a id="DeleteItem"></a>Löschen eines Elements

Azure Cosmos DB unterstützt das Löschen von JSON-Elementen. Der folgende Code zeigt, wie ein Element anhand seiner ID abgerufen und gelöscht wird. Kopieren Sie den folgenden Code, und fügen Sie ihn in die Datei *app.js* ein.

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="DeleteItem":::

## <a name="run-your-nodejs-application"></a><a id="Run"></a>Ausführen der Node.js-Anwendung

Der Code sollte insgesamt wie folgt aussehen:

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js":::

Suchen Sie in Ihrem Terminal nach der Datei ```app.js```, und führen Sie den folgenden Befehl aus:

```bash 
node app.js
```

Die Ausgabe der GetStarted-Anwendung sollte angezeigt werden. Die Ausgabe sollte dem folgenden Beispieltext entsprechen.

```
Created database:
Tasks

Created container:
Items

Querying container: Items
1 - Pick up apples and strawberries.

Created new item: 3 - Complete Cosmos DB Node.js Quickstart ⚡

Updated item: 3 - Complete Cosmos DB Node.js Quickstart ⚡
Updated isComplete to true

Deleted item with id: 3
```

## <a name="get-the-complete-nodejs-tutorial-solution"></a><a id="GetSolution"></a>Abrufen der vollständigen Projektmappe für das Node.js-Tutorial

Wenn Sie keine Zeit hatten, die Schritte in diesem Tutorial auszuführen, oder nur den Code herunterladen möchten, finden Sie ihn auf [GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ).

Zum Ausführen der Lösung für die ersten Schritte, die den gesamten Code dieses Artikels enthält, ist Folgendes erforderlich:

* Ein [Azure Cosmos DB-Konto][create-account]
* Lösung [Erste Schritte](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started) (erhältlich auf GitHub)

Installieren Sie die Abhängigkeiten des Projekts über npm. Verwenden Sie den folgenden Befehl:

* ```npm install``` 

Aktualisieren Sie dann in der Datei ```config.js``` die Werte für „config.endpoint“ und „config.key“, wie unter [Schritt 3: Festlegen der Konfigurationen der App](#Config) beschrieben.  

Suchen Sie dann auf Ihrem Terminal nach der Datei ```app.js```, und führen Sie den folgenden Befehl aus:  

```bash  
node app.js 
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Ressourcengruppe, das Azure Cosmos DB-Konto und die dazugehörigen Ressourcen nicht mehr benötigt werden, können Sie sie löschen. Wählen Sie dazu die Ressourcengruppe für das Azure Cosmos DB-Konto und anschließend **Löschen** aus, und bestätigen Sie den Namen der zu löschenden Ressourcengruppe.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Überwachen eines Azure Cosmos DB-Kontos](./monitor-cosmos-db.md)

[create-account]: create-sql-api-dotnet.md#create-account