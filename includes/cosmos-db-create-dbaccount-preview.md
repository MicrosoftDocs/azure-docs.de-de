---
title: Includedatei
description: Includedatei
services: cosmos-db
author: deborahc
ms.service: cosmos-db
ms.topic: include
ms.date: 11/24/2018
ms.author: dech
ms.custom: include file
ms.openlocfilehash: 3915df46495e754843009a761e9a07aa2daddae2
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66153743"
---
1. Melden Sie sich in einem neuen Browserfenster beim [Azure-Portal](https://portal.azure.com/) an.
2. Klicken Sie auf **Ressourcen erstellen** > **Datenbanken** > **Azure Cosmos DB**.
   
   ![Die Bereich „Datenbanken“ im Azure-Portal](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

3. Geben Sie auf der Seite **Azure Cosmos DB-Konto erstellen** die Einstellungen für das neue Azure Cosmos DB-Konto ein. 
 
    Einstellung|Wert|BESCHREIBUNG
    ---|---|---
    Abonnement|*Ihr Abonnement*|Wählen Sie das Azure-Abonnement aus, das Sie für dieses Azure Cosmos DB-Konto verwenden möchten. 
    Ressourcengruppe|Neu erstellen<br><br>*Ein eindeutiger Name*|Wählen Sie **Neu erstellen** aus, und geben Sie dann einen neuen Ressourcengruppenname für Ihr Konto ein. Der Einfachheit halber können Sie denselben Namen wie für Ihr Konto verwenden. 
    Kontoname|*Ein eindeutiger Name*|Geben Sie einen eindeutigen Namen ein, der Ihr Azure Cosmos DB-Konto identifiziert. Da *documents.azure.com* an die ID angefügt wird, die Sie für die URI-Erstellung angeben, muss die ID eindeutig sein.<br><br>Die ID darf nur Kleinbuchstaben, Zahlen und den Bindestrich (-) enthalten und muss zwischen 3 und 31 Zeichen lang sein.
    API|Core (SQL)|Die API bestimmt den Typ des zu erstellenden Kontos. Azure Cosmos DB stellt fünf APIs bereit: SQL-API (Dokumentdatenbank), Gremlin-API (Graphdatenbank), MongoDB-API (Dokumentdatenbank), Tabellen-API und Cassandra-API. Jede API erfordert derzeit die Erstellung eines separaten Kontos. <br><br>In diesem Artikel wird eine Dokumentdatenbank erstellt und unter Verwendung der SQL-Syntax abgefragt. Wählen Sie daher **Core (SQL)** aus. <br><br>[Weitere Informationen zur SQL-API](../articles/cosmos-db/documentdb-introduction.md)|
    Location|*Die Region, die Ihren Benutzern am nächsten liegt*|Wählen Sie einen geografischen Standort aus, an dem Ihr Azure Cosmos DB-Konto gehostet werden soll. Verwenden Sie einen Standort, der Ihren Benutzern am nächsten liegt, um ihnen einen schnellen Zugriff auf die Daten zu gewähren.
    Georedundanz aktivieren| Nicht ausfüllen | Dadurch wird eine replizierte Version Ihrer Datenbank in einer zweiten (zugeordneten) Region erstellt. Lassen Sie diese Einstellung leer.  
    Schreibvorgänge in mehreren Regionen| Nicht ausfüllen | Dadurch kann jede Ihrer Datenbankregionen sowohl eine Lese- als auch eine Schreibregion sein. Lassen Sie diese Einstellung leer.  

    Klicken Sie dann auf **Überprüfen + erstellen**. Sie können die Abschnitte **Netzwerk** und **Tags** überspringen. 

    ![Die Seite „Neues Konto“ für Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-preview.png)

    Lesen Sie die Zusammenfassungsinformationen, und klicken Sie auf **Erstellen**. 

    ![Zusammenfassungsseite für die Kontoüberprüfung](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-summary-preview.png)

4. Die Kontoerstellung dauert einige Minuten. Warten Sie, bis im Portal die Meldung **Ihre Bereitstellung wurde abgeschlossen.** angezeigt wird, und klicken Sie auf **Zu Ressource wechseln**.     

    ![Seite mit der Meldung, dass das Speicherkonto erfolgreich erstellt wurde](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account-complete-preview.png)

5. Im Portal wird nun die Seite **Glückwunsch! Ihr Azure Cosmos DB-Konto wurde erstellt** anzeigt.

    ![Der Bereich „Benachrichtigungen“ im Azure-Portal](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

