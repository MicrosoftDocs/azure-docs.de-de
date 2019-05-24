---
title: Includedatei
description: Includedatei
services: cosmos-db
author: rimman
ms.service: cosmos-db
ms.topic: include
ms.date: 04/08/2019
ms.author: rimman
ms.custom: include file
ms.openlocfilehash: 5d57d7e18befba175a5a8a825494ce512644b5a2
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131771"
---
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Wählen Sie **Ressourcen erstellen** > **Datenbanken** > **Azure Cosmos DB** aus.
   
   ![Die Bereich „Datenbanken“ im Azure-Portal](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

1. Geben Sie auf der Seite **Azure Cosmos DB-Konto erstellen** die grundlegenden Einstellungen für das neue Azure Cosmos-Konto ein. 
 
    |Einstellung|Wert|BESCHREIBUNG |
    |---|---|---|
    |Abonnement|Abonnementname|Wählen Sie das Azure-Abonnement aus, das Sie für dieses Azure Cosmos-Konto verwenden möchten. |
    |Ressourcengruppe|Ressourcengruppenname|Wählen Sie eine Ressourcengruppe aus, oder wählen Sie **Neu erstellen** aus, und geben Sie einen eindeutigen Namen für die Ressourcengruppe ein. |
    | Kontoname|Geben Sie einen eindeutigen Namen ein.|Geben Sie einen Namen ein, der Ihr Azure Cosmos-Konto identifiziert. Da *documents.azure.com* an die ID angefügt wird, die Sie für die URI-Erstellung angeben, muss die ID eindeutig sein.<br><br>Die ID darf nur Kleinbuchstaben, Zahlen und den Bindestrich (-) enthalten. Sie muss zwischen drei und 31 Zeichen lang sein.|
    | API|Core (SQL)|Die API bestimmt den Typ des zu erstellenden Kontos. Azure Cosmos DB stellt fünf APIs bereit: Core (SQL) und MongoDB für Dokumentdaten, Gremlin für Diagrammdaten sowie Azure Table und Cassandra. Derzeit müssen Sie ein separates Konto für jede API erstellen. <br><br>Wählen Sie **Core (SQL)** aus, um eine Dokumentdatenbank und eine Abfrage mit SQL-Syntax zu erstellen. <br><br>[Weitere Informationen zur SQL-API](../articles/cosmos-db/documentdb-introduction.md)|
    | Location|Wählen Sie die Region aus, die Ihren Benutzern am nächsten liegt.|Wählen Sie einen geografischen Standort aus, an dem Ihr Azure Cosmos DB-Konto gehostet werden soll. Verwenden Sie den Standort, der Ihren Benutzern am nächsten ist, damit sie möglichst schnell auf die Daten zugreifen können.|
   
   ![Die Seite „Neues Konto“ für Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account.png)

1. Klicken Sie auf **Überprüfen + erstellen**. Sie können die Abschnitte **Netzwerk** und **Tags** überspringen. 

1. Überprüfen Sie die Kontoeinstellungen, und wählen Sie anschließend **Erstellen** aus. Die Erstellung des Kontos dauert einige Minuten. Warten Sie, bis auf der Portalseite **Ihre Bereitstellung wurde abgeschlossen.** angezeigt wird. 

    ![Der Bereich „Benachrichtigungen“ im Azure-Portal](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

1. Wählen Sie **Zu Ressource wechseln** aus, um zur Seite des Azure Cosmos DB-Kontos zu wechseln. 

    ![Seite des Azure Cosmos DB-Kontos](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png)
