---
title: 'Schnellstart: Tabellen-API mit Java: Azure Cosmos DB'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe der Table-API von Azure Cosmos DB eine Anwendung mit dem Azure-Portal und Java erstellen.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: java
ms.topic: quickstart
ms.date: 04/10/2018
ms.author: sngun
ms.openlocfilehash: 22d5aea119d71553afc02ee923fab5cf5ead5ce0
ms.sourcegitcommit: 17411cbf03c3fa3602e624e641099196769d718b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/10/2019
ms.locfileid: "65519047"
---
# <a name="quickstart-build-a-table-api-app-with-java-and-azure-cosmos-db"></a>Schnellstart: Erstellen einer Tabellen-API-App mit Java und Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](create-table-python.md)
> 

In dieser Schnellstartanleitung erfahren Sie, wie Sie mithilfe von Java und der [Table-API](table-introduction.md) von Azure Cosmos DB eine App erstellen, indem Sie ein Beispiel von GitHub klonen. Außerdem wird in dieser Schnellstartanleitung gezeigt, wie Sie ein Azure Cosmos DB-Konto erstellen und mithilfe des Daten-Explorers Tabellen und Entitäten im webbasierten Azure-Portal erstellen.

Azure Cosmos DB ist der global verteilte Microsoft-Datenbankdienst mit mehreren Modellen. Sie können schnell Dokument-, Schlüssel/Wert- und Graph-Datenbanken erstellen und abfragen und dabei stets die Vorteile der globalen Verteilung und der horizontalen Skalierung nutzen, die Azure Cosmos DB zugrunde liegen. 

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Außerdem haben Sie folgende Möglichkeiten: 

* [Java Development Kit (JDK) 8](https://aka.ms/azure-jdks)
    * Achten Sie darauf, dass die Umgebungsvariable „JAVA_HOME“ auf den Ordner verweist, in dem das JDK installiert ist.
* Ein binäres [Maven](https://maven.apache.org/)-Archiv ([Download](https://maven.apache.org/download.cgi)/[Installationsanleitung](https://maven.apache.org/install.html))
    * Unter Ubuntu können Sie `apt-get install maven` ausführen, um Maven zu installieren.
* [Git-Client](https://www.git-scm.com/)
    * Unter Ubuntu können Sie `sudo apt-get install git` ausführen, um Git zu installieren.

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
    git clone https://github.com/Azure-Samples/storage-table-java-getting-started.git 
    ```

## <a name="update-your-connection-string"></a>Aktualisieren der Verbindungszeichenfolge

Wechseln Sie nun zurück zum Azure-Portal, um die Informationen der Verbindungszeichenfolge abzurufen und in die App zu kopieren. Dadurch kann Ihre App mit Ihrer gehosteten Datenbank kommunizieren. 

1. Klicken Sie im [Azure-Portal](https://portal.azure.com/) auf **Verbindungszeichenfolge**. 

   ![Anzeigen und Kopieren der erforderlichen Verbindungszeichenfolgeninformationen aus dem Bereich „Verbindungszeichenfolge“](./media/create-table-java/connection-string.png)

2. Kopieren Sie die primäre Verbindungszeichenfolge mithilfe der Kopierschaltfläche auf der rechten Seite.

3. Öffnen Sie „config.properties“ aus dem Ordner „C:\git-samples\storage-table-java-getting-started\src\main\resources“. 

5. Kommentieren Sie die erste Zeile aus, und heben Sie die Auskommentierung in der zweiten Zeile auf. Die beiden ersten Zeilen sollten nun wie folgt aussehen.

    ```
    #StorageConnectionString = UseDevelopmentStorage=true
    StorageConnectionString = DefaultEndpointsProtocol=https;AccountName=[ACCOUNTNAME];AccountKey=[ACCOUNTKEY]
    ```

6. Fügen Sie die primäre Verbindungszeichenfolge aus dem Portal in den Wert „StorageConnectionString“ in Zeile 2 ein. 

    > [!IMPORTANT]
    > Wenn Ihr Endpunkt „documents.azure.com“ verwendet, bedeutet dies, dass Sie über ein Vorschaukonto verfügen und ein [neues Tabellen-API-Konto](#create-a-database-account) erstellen müssen, um mit dem allgemein verfügbaren Tabellen-API-SDK zu arbeiten.
    >

7. Speichern Sie die Datei „config.properties“.

Sie haben die App nun mit allen erforderlichen Informationen für die Kommunikation mit Azure Cosmos DB aktualisiert. 

## <a name="run-the-app"></a>Ausführen der App

1. Wechseln Sie im Terminalfenster von Git mithilfe von `cd` zum Ordner „storage-table-java-getting-started“.

    ```git
    cd "C:\git-samples\storage-table-java-getting-started"
    ```

2. Führen Sie im Terminalfenster von Git die folgenden Befehle aus, um die Java-Anwendung zu starten.

    ```git
    mvn compile exec:java 
    ```

    Im Konsolenfenster werden die Tabellendaten angezeigt, die der neuen Tabellendatenbank in Azure Cosmos DB hinzugefügt werden.

    Jetzt können Sie zum Daten-Explorer zurückkehren, um diese neue Daten anzuzeigen, abzufragen, anzupassen und mit ihnen zu arbeiten. 

## <a name="review-slas-in-the-azure-portal"></a>Überprüfen von SLAs im Azure-Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie gelernt, wie Sie ein Azure Cosmos DB-Konto erstellen, eine Tabelle mit dem Daten-Explorer erstellen und eine App ausführen.  Jetzt können Sie Ihre Daten mit der Table-API abfragen.  

> [!div class="nextstepaction"]
> [Importieren von Daten in die Table-API](table-import.md)
