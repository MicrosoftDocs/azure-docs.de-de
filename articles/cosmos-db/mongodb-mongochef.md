---
title: Herstellen einer Verbindung mit der API für MongoDB von Azure Cosmos DB mithilfe von Studio 3T
titleSuffix: Azure Cosmos DB
description: Erfahren Sie, wie eine Verbindung mit Cosmos DB mithilfe von Studio 3T und der API für MongoDB von Azure Cosmos DB herstellen.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.custom: seodec18
ms.openlocfilehash: 9b0a6393d2372c831fdc964dee18acbccfd39a77
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61331949"
---
# <a name="connect-to-cosmos-account-using-studio-3t"></a>Herstellen einer Verbindung mit einem Cosmos-Konto mithilfe von Studio 3T

Um eine Verbindung mit einem Cosmos-Konto mit der API für MongoDB von Azure Cosmos DB herstellen zu können, müssen Sie die folgenden Aktionen ausführen:

* Herunterladen und Installieren von [Studio 3T](https://studio3t.com/)
* Abrufen der Informationen zur Cosmos DB-[Verbindungszeichenfolge](connect-mongodb-account.md)

## <a name="create-the-connection-in-studio-3t"></a>Sie müssen die Verbindung in Studio 3T herstellen
Führen Sie die folgenden Schritte aus, um dem Studio 3T-Verbindungs-Manager Ihr Cosmos-Konto hinzuzufügen:

1. Rufen Sie die Verbindungsinformationen für Ihr Cosmos-Konto ab, das mit der API für MongoDB von Azure Cosmos DB konfiguriert wurde. Verwenden Sie dazu die Anweisungen im Artikel [Herstellen einer Verbindung zwischen einer MongoDB-Anwendung und Azure Cosmos DB](connect-mongodb-account.md).

    ![Screenshot der Seite „Verbindungszeichenfolge“](./media/mongodb-mongochef/ConnectionStringBlade.png)
2. Klicken Sie auf **Connect** (Verbinden), um den Verbindungs-Manager zu öffnen, und klicken Sie anschließend auf **New Connection** (Neue Verbindung).

    ![Screenshot des Verbindungs-Managers von Studio 3T](./media/mongodb-mongochef/ConnectionManager.png)
3. Geben Sie im Fenster **Neue Verbindung** auf der Registerkarte **Server** den HOST (FQDN) des Azure Cosmos DB-Kontos sowie den Port ein.

    ![Screenshot der Serverregisterkarte im Verbindungs-Manager von Studio 3T](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. Wählen Sie im Fenster **New Connection** (Neue Verbindung) auf der Registerkarte **Authentication** (Authentifizierung) den Authentifizierungsmodus **Standard (MONGODB-CR or SCARM-SHA-1)** (Standard [MONGODB-CR oder SCARM-SHA-1]) aus, und geben Sie Benutzername und Kennwort ein.  Übernehmen Sie den Standardwert für Authentifizierungs-DB („admin“), oder geben Sie einen eigenen Wert ein.

    ![Screenshot der Authentifizierungsregisterkarte im Studio 3T-Verbindungs-Manager](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. Aktivieren Sie im Fenster **New Connection** (Neue Verbindung) auf der Registerkarte **SSL** das Kontrollkästchen **Use SSL protocol to connect** (Verbindung über SSL-Protokoll herstellen) und das Optionsfeld **Accept server self-signed SSL certificates** (Selbstsignierte SSL-Serverzertifikate akzeptieren).

    ![Screenshot der SSL-Registerkarte im Verbindungs-Manager von Studio 3T](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Klicken Sie auf die Schaltfläche **Test Connection** (Verbindung testen), um die Verbindungsinformationen zu überprüfen. Klicken Sie auf **OK**, um zum Fenster für neue Verbindungen zurückzukehren, und klicken Sie anschließend auf **Save** (Speichern).

    ![Screenshot des Fensters „Verbindung testen“ von Studio 3T](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Verwenden von Studio 3T zum Erstellen von Datenbanken, Sammlungen und Dokumenten
Führen Sie die folgenden Schritte aus, um Datenbanken, Sammlungen und Dokumente mithilfe von Studio 3T zu erstellen:

1. Markieren Sie die Verbindung im **Verbindungs-Manager**, und klicken Sie auf **Connect** (Verbinden).

    ![Screenshot des Verbindungs-Managers von Studio 3T](./media/mongodb-mongochef/ConnectToAccount.png)
2. Klicken Sie mit der rechten Maustaste auf den Host, und wählen Sie **Add Database**(Datenbank hinzufügen) aus.  Geben Sie einen Datenbanknamen an, und klicken Sie auf **OK**.

    ![Screenshot der Studio 3T-Option „Add Database“](./media/mongodb-mongochef/AddDatabase1.png)
3. Klicken Sie mit der rechten Maustaste auf die Datenbank, und wählen Sie **Add Collection**(Sammlung hinzufügen) aus.  Geben Sie einen Sammlungsnamen an, und klicken Sie auf **Create**(Erstellen).

    ![Screenshot der Studio 3T-Option „Add Collection“ (Sammlung hinzufügen)](./media/mongodb-mongochef/AddCollection.png)
4. Klicken Sie auf das Menüelement **Collection** (Sammlung) und anschließend auf **Add Document** (Dokument hinzufügen).

    ![Screenshot des Studio 3T-Menüelements „Add Document“ (Dokument hinzufügen)](./media/mongodb-mongochef/AddDocument1.png)
5. Fügen Sie im Dialogfeld zum Hinzufügen von Dokumenten Folgendes ein, und klicken Sie dann auf **Add Document**(Dokument hinzufügen).

        {
        "_id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
               { "firstName": "Thomas" },
               { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
        }
6. Fügen Sie ein weiteres Dokument mit dem folgenden Inhalt hinzu:

        {
        "_id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                 "givenName": "Jesse",
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            {
                "familyName": "Miller",
                 "givenName": "Lisa",
                 "gender": "female",
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
        }
7. Führen Sie eine Beispielabfrage aus. Suchen Sie z.B. nach Familien mit dem Nachnamen „Andersen“, und geben Sie übergeordnete Felder und Felder mit Angaben zum Staat zurück.

    ![Screenshot der MongoChef-Abfrageergebnisse](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [Robo 3T](mongodb-robomongo.md) mit der API für MongoDB von Azure Cosmos DB verwenden.
- Untersuchen Sie MongoDB-[Beispiele](mongodb-samples.md) mit der API für MongoDB von Azure Cosmos DB.
