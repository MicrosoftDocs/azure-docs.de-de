---
title: include file
description: include file
services: cosmos-db
ms.custom: include file
ms.openlocfilehash: ad4445cbea6553a7a96299e1276dbe8f3816e166
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994547"
---
1. Melden Sie sich in einem neuen Browserfenster beim [Azure-Portal](https://portal.azure.com/) an.

2. Wählen Sie im Menü auf der linken Seite **Ressource erstellen** aus.
   
   ![Erstellen einer Ressource im Azure-Portal](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-0.png)
   
3. Wählen Sie auf der Seite **Neu** die Optionen **Datenbanken** > **Azure Cosmos DB** aus.
   
   ![Die Bereich „Datenbanken“ im Azure-Portal](./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png)
   
3. Geben Sie auf der Seite **Azure Cosmos DB-Konto erstellen** die Einstellungen für das neue Azure Cosmos DB-Konto ein: 
 
    Einstellung|Wert|BESCHREIBUNG
    ---|---|---
    Subscription|Ihr Abonnement|Wählen Sie das Azure-Abonnement aus, das Sie für dieses Azure Cosmos DB-Konto verwenden möchten. 
    Ressourcengruppe|Neu erstellen<br><br>Geben Sie dann den gleichen Namen als Kontonamen ein.|Wählen Sie **Neu erstellen**. Geben Sie dann einen neuen Ressourcengruppenname für Ihr Konto ein. Verwenden Sie der Einfachheit halber den gleichen Namen als Azure Cosmos DB-Kontonamen. 
    Kontoname|Geben Sie einen eindeutigen Namen ein.|Geben Sie einen eindeutigen Namen ein, der Ihr Azure Cosmos DB-Konto identifiziert. Der Konto-URI lautet *mongo.cosmos.azure.com* und wird an Ihren eindeutigen Kontonamen angehängt.<br><br>Der Kontoname darf nur Kleinbuchstaben, Ziffern und Bindestriche (-) enthalten und muss zwischen 3 und 31 Zeichen lang sein.
    API|Azure Cosmos DB-API für Mongo DB|Die API bestimmt den Typ des zu erstellenden Kontos. Azure Cosmos DB stellt fünf APIs bereit: Kern-API (SQL) für Dokumentdatenbanken, Gremlin-API für Graphdatenbanken, API für Mongo DB von Azure Cosmos DB für Dokumentdatenbanken, Azure-Tabellen-API und Cassandra-API. Derzeit müssen Sie ein separates Konto für jede API erstellen. <br><br>Wählen Sie **Azure Cosmos DB-API für Mongo DB** aus, da Sie in dieser Schnellstartanleitung eine Sammlung erstellen, die mit MongoDB verwendet werden kann.<br><br>[Weitere Informationen zur Azure Cosmos DB-API für MongoDB](../articles/cosmos-db/mongodb-introduction.md)|
    Position|Wählen Sie die Region aus, die Ihren Benutzern am nächsten liegt.|Wählen Sie einen geografischen Standort aus, an dem Ihr Azure Cosmos DB-Konto gehostet werden soll. Verwenden Sie einen Standort, der Ihren Benutzern am nächsten liegt, um ihnen einen schnellen Zugriff auf die Daten zu gewähren.|
    Kapazitätsmodus|„Bereitgestellter Durchsatz“ oder „Serverlos“|Wählen Sie **Bereitgestellter Durchsatz** aus, um ein Konto im Modus [Bereitgestellter Durchsatz](../articles/cosmos-db/set-throughput.md) zu erstellen. Wählen Sie **Serverlos** aus, um ein Konto im Modus [Serverlos](../articles/cosmos-db/serverless.md) zu erstellen.<br><br>**Hinweis**: Nur Version 3.6 der MongoDB-API wird von serverlosen Konten unterstützt. Wenn Sie 3.2 als Version auswählen, wird das Konto im Modus für bereitgestellten Durchsatz erzwungen.

    Wählen Sie **Bewerten + erstellen** aus. Sie können die Abschnitte **Netzwerk** und **Tags** überspringen. 

    ![Die Seite „Neues Konto“ für Azure Cosmos DB](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png)

4. Die Kontoerstellung dauert einige Minuten. Warten Sie, bis das Portal die Seite **Herzlichen Glückwunsch! Ihr Azure Cosmos DB für Mongo DB-API-Konto ist jetzt bereit.** anzeigt.

    ![Der Bereich „Benachrichtigungen“ im Azure-Portal](./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png)
