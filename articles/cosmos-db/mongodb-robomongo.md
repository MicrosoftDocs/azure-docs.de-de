---
title: Herstellen einer Verbindung mit Azure Cosmos DB mithilfe von Robo 3T
description: Erfahren Sie, wie eine Verbindung mit Azure Cosmos DB mithilfe von Robo 3T und der API für MongoDB von Azure Cosmos DB herstellen.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/23/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: e096de5eea9bf5bbd47b976976192e62eb6aa33e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096409"
---
# <a name="use-robo-3t-with-azure-cosmos-dbs-api-for-mongodb"></a>Verwenden von Robo 3T mit der API für MongoDB von Azure Cosmos DB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Um mithilfe von Robo 3T eine Verbindung mit einem Cosmos-Konto herzustellen, müssen Sie die folgenden Aktionen ausführen:

* Herunterladen und Installieren von [Robo 3T](https://robomongo.org/)
* Abrufen der Informationen zur Cosmos DB-[Verbindungszeichenfolge](connect-mongodb-account.md)

> [!NOTE]
> Zurzeit werden Robo 3T v1.2 und niedrigere Versionen mit der Cosmos DB-API für MongoDB unterstützt.

## <a name="connect-using-robo-3t"></a>Herstellen einer Verbindung mit Robo 3T

Führen Sie die folgenden Schritte aus, um dem Robo 3T-Verbindungs-Manager Ihr Cosmos-Konto hinzuzufügen:

1. Rufen Sie die Verbindungsinformationen für Ihr Cosmos-Konto ab, das mit der API für MongoDB von Azure Cosmos DB konfiguriert wurde. Verwenden Sie dazu die [hier](connect-mongodb-account.md) beschriebenen Anweisungen.

    :::image type="content" source="./media/mongodb-robomongo/connectionstringblade.png" alt-text="Screenshot des Blatts „Verbindungszeichenfolge“":::
2. Führen Sie die Anwendung *Robomongo* aus.

3. Klicken Sie unter **Datei** auf die Verbindungsschaltfläche, um Ihre Verbindungen zu verwalten. Klicken Sie anschließend im Fenster mit den **MongoDB-Verbindungen** auf **Erstellen**. Daraufhin öffnet sich das Fenster mit den **Verbindungseinstellungen**.

4. Wählen Sie im Fenster mit den **Verbindungseinstellungen** einen Namen aus. Ermitteln Sie anschließend den **Host** und den **Port** in Ihren Verbindungsinformationen aus Schritt 1, und geben Sie sie unter **Adresse** bzw. **Port** ein.

    :::image type="content" source="./media/mongodb-robomongo/manageconnections.png" alt-text="Screenshot der Verbindungsverwaltung von Robomongo":::
5. Klicken Sie auf der Registerkarte **Authentifizierung** auf **Perform authentication** (Authentifizierung durchführen). Geben Sie anschließend die Datenbank (Standardeinstellung: *Admin* ) sowie **Benutzername** und **Kennwort** ein.
Sowohl **Benutzername** als auch **Kennwort** finden Sie in den Verbindungsinformationen aus Schritt 1.

    :::image type="content" source="./media/mongodb-robomongo/authentication.png" alt-text="Screenshot der Authentifizierungsregisterkarte von Robomongo":::
6. Aktivieren Sie auf der Registerkarte **SSL** das Kontrollkästchen **Use SSL protocol** (SSL-Protokoll verwenden), und ändern Sie anschließend die **Authentifizierungsmethode** in **Selbstsigniertes Zertifikat**.

    :::image type="content" source="./media/mongodb-robomongo/SSL.png" alt-text="Screenshot der SSL-Registerkarte von Robomongo":::
7. Klicken Sie abschließend auf **Test** , um sich zu vergewissern, dass die Verbindung hergestellt werden kann, und klicken Sie schließlich auf **Speichern**.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [Studio 3T](mongodb-mongochef.md) mit der API für MongoDB von Azure Cosmos DB verwenden.
- Untersuchen Sie MongoDB-[Beispiele](mongodb-samples.md) mit der API für MongoDB von Azure Cosmos DB.
