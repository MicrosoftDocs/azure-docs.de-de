---
title: 'Tutorial: Abfragen von Daten von einem Cassandra-API-Konto in Azure Cosmos DB'
description: In diesem Tutorial wird das Abfragen von Benutzerdaten von einem Cassandra-API-Konto von Azure Cosmos DB mithilfe einer Java-Anwendung gezeigt.
ms.service: cosmos-db
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: 693984ceaef1a71ebfbe4122c18a45a563c9c101
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100430"
---
# <a name="tutorial-query-data-from-a-cassandra-api-account-in-azure-cosmos-db"></a>Tutorial: Abfragen von Daten von einem Cassandra-API-Konto in Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Als Entwickler besitzen Sie möglicherweise Anwendungen, die Schlüssel/Wert-Paare verwenden. Sie können ein Cassandra-API-Konto in Azure Cosmos DB zum Speichern und Abfragen der Schlüssel-Wert-Daten verwenden. In diesem Tutorial wird das Abfragen von Benutzerdaten von einem Cassandra-API-Konto in Azure Cosmos DB mithilfe einer Java-Anwendung gezeigt. Die Java-Anwendung verwendet den [Java-Treiber](https://github.com/datastax/java-driver) und fragt Benutzerdaten wie Benutzer-ID, Benutzername und Benutzerort ab. 

Dieses Tutorial enthält die folgenden Aufgaben:

> [!div class="checklist"]
> * Abfragen von Daten aus einer Cassandra-Tabelle
> * Ausführen der App

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

* Dieser Artikel gehört zu einem mehrteiligen Tutorial. Bevor Sie beginnen, führen Sie die vorherigen Schritte zum Erstellen des Cassandra-API-Kontos, Keyspace oder der Tabelle und zum [Laden von Beispieldaten in die Tabelle](cassandra-api-load-data.md) aus. 

## <a name="query-data"></a>Daten abfragen

Führen Sie zum Abfragen von Daten aus Ihrem Cassandra-API-Konto die folgenden Schritte aus:

1. Öffnen Sie die Datei `UserRepository.java` unter dem Ordner `src\main\java\com\azure\cosmosdb\cassandra`. Fügen Sie folgenden Codeblock an. Dieser Code bietet drei Methoden: 

   * Abfragen aller Benutzer in der Datenbank
   * Abfragen eines bestimmten Benutzers, gefiltert nach Benutzer-ID
   * So löschen Sie eine Tabelle

   ```java
   /**
   * Select all rows from user table
   */
   public void selectAllUsers() {

     final String query = "SELECT * FROM uprofile.user";
     List<Row> rows = session.execute(query).all();

     for (Row row : rows) {
        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
     }
   }

   /**
   * Select a row from user table
   *
   * @param id user_id
   */
   public void selectUser(int id) {
      final String query = "SELECT * FROM uprofile.user where user_id = 3";
      Row row = session.execute(query).one();

      LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
   }

   /**
   * Delete user table.
   */
   public void deleteTable() {
     final String query = "DROP TABLE IF EXISTS uprofile.user";
     session.execute(query);
   }
   ```

2. Öffnen Sie die Datei `UserProfile.java` unter dem Ordner `src\main\java\com\azure\cosmosdb\cassandra`. Diese Klasse enthält die main-Methode, die die Methoden zum Einfügen von Daten createKeyspace und createTable aufruft, die Sie zuvor definiert haben. Fügen Sie nun den folgenden Code an, der alle Benutzer oder einen bestimmten Benutzer abfragt:

   ```java
   LOGGER.info("Select all users");
   repository.selectAllUsers();

   LOGGER.info("Select a user by id (3)");
   repository.selectUser(3);

   LOGGER.info("Delete the users profile table");
   repository.deleteTable();
   ```

## <a name="run-the-java-app"></a>Ausführen der Java-App
1. Öffnen Sie eine Eingabeaufforderung oder ein Terminalfenster. Fügen Sie folgenden Codeblock ein. 

   Dieser Code ändert das Verzeichnis (cd) in den Ordnerpfad, in dem das Projekt erstellt wurde. Anschließend führt er den Befehl `mvn clean install` zum Generieren der Datei `cosmosdb-cassandra-examples.jar` im Zielordner aus. Abschließend führt er die Java-Anwendung aus.

   ```bash
   cd "cassandra-demo"
   
   mvn clean install
   
   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
   ```

2. Öffnen Sie nun im Azure-Portal den **Daten-Explorer** , und vergewissern Sie sich, dass die Benutzertabelle gelöscht ist.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Ressourcengruppe, das Azure Cosmos-Konto und die dazugehörigen Ressourcen nicht mehr benötigt werden, können Sie sie löschen. Wählen Sie dazu die Ressourcengruppe für den virtuellen Computer und anschließend **Löschen** aus, und bestätigen Sie den Namen der zu löschenden Ressourcengruppe.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, Ihre Daten aus einem Cassandra-API-Konto in Azure Cosmos DB abzufragen. Sie können jetzt mit dem nächsten Artikel fortfahren:

> [!div class="nextstepaction"]
> [Daten zum Cassandra-API-Konto migrieren](cassandra-import-data.md)


