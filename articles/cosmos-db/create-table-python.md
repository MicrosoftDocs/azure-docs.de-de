---
title: 'Schnellstart: Tabellen-API mit Python: Azure Cosmos DB'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe der Tabellen-API von Azure Cosmos DB eine Anwendung mit dem Azure-Portal und Python erstellen.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: python
ms.topic: quickstart
ms.date: 04/10/2018
ms.author: sngun
ms.openlocfilehash: 2425d14e43abd98d80bef8ce01e1c1c5f73a78a0
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65833080"
---
# <a name="quickstart-build-a-table-api-app-with-python-and-azure-cosmos-db"></a>Schnellstart: Erstellen einer Tabellen-API-App mit Python und Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](create-table-python.md)
> 

In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe von Python und der [Tabellen-API](table-introduction.md) von Azure Cosmos DB eine App erstellen, indem Sie ein Beispiel von GitHub klonen. Außerdem wird in dieser Schnellstartanleitung gezeigt, wie Sie ein Azure Cosmos DB-Konto erstellen und mithilfe des Daten-Explorers Tabellen und Entitäten im webbasierten Azure-Portal erstellen.

Azure Cosmos DB ist der global verteilte Microsoft-Datenbankdienst mit mehreren Modellen. Sie können schnell Datenbanken mit Dokumenten, Schlüsseln/Werten, breiten Spalten und Graphen erstellen und abfragen und dabei stets die Vorteile der globalen Verteilung und der horizontalen Skalierung nutzen, die Azure Cosmos DB zugrunde liegen. 

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Außerdem haben Sie folgende Möglichkeiten:

* Falls Sie Visual Studio 2019 noch nicht installiert haben, können Sie die **kostenlose** [Visual Studio 2019 Community-Edition](https://www.visualstudio.com/downloads/) herunterladen und verwenden. Stellen Sie sicher, dass Sie während des Visual Studio-Setups die Workloads **Azure-Entwicklung** und **Python-Entwicklung** auswählen.
* Wählen Sie ebenso die Option „Python 2“ in der **Python-Entwicklungsworkload**, oder laden Sie Python 2.7 von [python.org](https://www.python.org/downloads/release/python-2712/) herunter.

## <a name="create-a-database-account"></a>Erstellen eines Datenbankkontos

> [!IMPORTANT] 
> Zum Arbeiten mit den allgemein verfügbaren Tabellen-API-SDKs müssen Sie ein neues Tabellen-API-Konto erstellen. Tabellen-API-Konten, die während der Vorschau erstellt wurden, werden von den allgemein verfügbaren SDKs nicht unterstützt.
>

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Hinzufügen einer Tabelle

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Hinzufügen von Beispieldaten

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>Klonen der Beispielanwendung

Klonen Sie jetzt eine Tabellen-App aus GitHub, legen Sie die Verbindungszeichenfolge fest, und führen Sie die App aus. Sie werden feststellen, wie einfach Sie programmgesteuert mit Daten arbeiten können. 

1. Öffnen Sie eine Eingabeaufforderung, erstellen Sie einen neuen Ordner namens „git-samples“, und schließen Sie die Eingabeaufforderung.

    ```bash
    md "C:\git-samples"
    ```

2. Öffnen Sie ein Git-Terminalfenster (z.B. git bash), und verwenden Sie den Befehl `cd`, um in den neuen Ordner zu gelangen und dort die Beispiel-App zu installieren.

    ```bash
    cd "C:\git-samples"
    ```

3. Führen Sie den folgenden Befehl aus, um das Beispielrepository zu klonen. Dieser Befehl erstellt eine Kopie der Beispiel-App auf Ihrem Computer. 

    ```bash
    git clone https://github.com/Azure-Samples/storage-python-getting-started.git
    ```

3. Öffnen Sie anschließend die Projektmappendatei in Visual Studio. 

## <a name="update-your-connection-string"></a>Aktualisieren der Verbindungszeichenfolge

Wechseln Sie nun zurück zum Azure-Portal, um die Informationen der Verbindungszeichenfolge abzurufen und in die App zu kopieren. Dadurch kann Ihre App mit Ihrer gehosteten Datenbank kommunizieren. 

1. Klicken Sie im [Azure-Portal](https://portal.azure.com/) auf **Verbindungszeichenfolge**. 

    ![Anzeigen und Kopieren der Verbindungszeichenfolge im Bereich „Verbindungszeichenfolge“](./media/create-table-python/connection-string.png)

2. Kopieren Sie den Kontonamen mithilfe der Schaltfläche auf der rechten Seite.

3. Öffnen Sie die Datei „config.py“, und fügen Sie den Kontonamen aus dem Portal in den STORAGE_ACCOUNT_NAME-Wert in Zeile 19 ein.

4. Kehren Sie zum Portal zurück, und kopieren Sie den Primärschlüssel.

5. Fügen Sie den Primärschlüssel aus dem Portal in den STORAGE_ACCOUNT_KEY-Wert in Zeile 20 ein.

3. Speichern Sie die Datei „config.py“.

## <a name="run-the-app"></a>Ausführen der App

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Projekt im **Projektmappen-Explorer**, wählen Sie die aktuelle Python-Umgebung aus, und klicken Sie anschließend mit der rechten Maustaste.

2. Wählen Sie „Python-Paket installieren“ aus, und geben Sie anschließend **azure-storage-table** ein.

3. Drücken Sie F5, um die Anwendung auszuführen. Ihre App wird im Browser angezeigt. 

Jetzt können Sie zum Daten-Explorer zurückkehren, um diese neue Daten anzuzeigen, abzufragen, anzupassen und mit ihnen zu arbeiten. 

## <a name="review-slas-in-the-azure-portal"></a>Überprüfen von SLAs im Azure-Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie gelernt, wie Sie ein Azure Cosmos DB-Konto erstellen, eine Tabelle mit dem Daten-Explorer erstellen und eine App ausführen.  Jetzt können Sie Ihre Daten mit der Table-API abfragen.  

> [!div class="nextstepaction"]
> [Importieren von Daten in die Table-API](table-import.md)
