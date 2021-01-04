---
title: Erstellen einer durch Azure Cosmos DB ausgelösten Funktion
description: Erstellen Sie mithilfe von Azure Functions eine serverlose Funktion, die aufgerufen wird, wenn in Azure Cosmos DB Daten einer Datenbank hinzugefügt werden.
ms.assetid: bc497d71-75e7-47b1-babd-a060a664adca
ms.topic: how-to
ms.date: 04/28/2020
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 907836b0e45ccc9e9481e605b1ebf4180f7650d6
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182579"
---
# <a name="create-a-function-triggered-by-azure-cosmos-db"></a>Erstellen einer durch Azure Cosmos DB ausgelösten Funktion

Informationen zum Erstellen einer Funktion, die ausgelöst wird, wenn in Azure Cosmos DB einer Datenbank Daten hinzugefügt oder Daten in ihr geändert werden. Weitere Informationen zu Azure Cosmos DB finden Sie unter [Azure Cosmos DB: Serverloses Datenbank-Computing mithilfe von Azure Functions](../cosmos-db/serverless-computing-database.md).

:::image type="content" source="./media/functions-create-cosmos-db-triggered-function/quickstart-completed.png" alt-text="Azure Cosmos DB-Code":::

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

+ Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

> [!NOTE]
> [!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="sign-in-to-azure"></a>Anmelden bei Azure
Melden Sie sich mit Ihrem Azure-Konto beim [Azure-Portal](https://portal.azure.com/) an.

## <a name="create-an-azure-cosmos-db-account"></a>Erstellen eines Azure Cosmos DB-Kontos

Sie müssen über ein Azure Cosmos DB-Konto verfügen, für das die SQL-API verwendet wird, um den Trigger erstellen zu können.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="create-an-azure-function-app"></a>Erstellen einer Azure Function-App

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Erstellen Sie als Nächstes eine Funktion in der neuen Funktions-App.

<a name="create-function"></a>

## <a name="create-azure-cosmos-db-trigger"></a>Erstellen eines Azure Cosmos DB-Triggers

1. Wählen Sie in Ihrer Funktions-App im linken Menü **Funktionen** und dann im obersten Menü **Funktionen** aus. 

1. Geben Sie auf der Seite **Neue Funktion** `cosmos` in das Suchfeld ein, und wählen Sie dann die Vorlage **Azure Cosmos DB-Trigger** aus.

   :::image type="content" source="./media/functions-create-cosmos-db-triggered-function/function-choose-cosmos.png" alt-text="Seite „Funktionen“ im Azure-Portal":::


1. Konfigurieren Sie den neuen Trigger mit den Einstellungen, die in der folgenden Tabelle angegeben sind:

    | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Neue Funktion** | Übernehmen Sie den Standardnamen. | Der Name der Funktion. |
    | **Cosmos DB-Kontoverbindung** | Übernehmen Sie den neuen Standardnamen. | Wählen Sie **Neu**, das zuvor von Ihnen erstellte **Datenbankkonto** und dann **OK** aus. Durch diese Aktion wird eine Anwendungseinstellung für Ihre Kontoverbindung erstellt. Diese Einstellung wird von der Bindung verwendet, um die Verbindung mit der Datenbank herzustellen. |
    | **Datenbankname** | Aufgaben | Name der Datenbank, die die zu überwachende Sammlung enthält. |
    | **Sammlungsname** | Items | Name der zu überwachenden Sammlung. |
    | **Sammlungsname für Leases** | Leases | Name der Sammlung zum Speichern der Leases. |
    | **Leasesammlung erstellen, sofern nicht vorhanden** | Ja | Überprüft das Vorhandensein der Leasesammlung und erstellt sie automatisch. |

    :::image type="content" source="./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-settings.png" alt-text="Erstellen der ausgelösten Azure Cosmos DB-Funktion":::

1. Wählen Sie **Funktion erstellen** aus. 

    Azure erstellt die Cosmos DB-Triggerfunktion.

1. Um den vorlagenbasierten Funktionscode anzuzeigen, wählen Sie **Code + testen** aus.

    :::image type="content" source="./media/functions-create-cosmos-db-triggered-function/function-cosmosdb-template.png" alt-text="Cosmos DB-Funktionsvorlage in C#":::

    Diese Funktionsvorlage schreibt die Anzahl von Dokumenten und die erste Dokument-ID in die Protokolle.

Als Nächstes stellen Sie eine Verbindung mit Ihrem Azure Cosmos DB-Konto her und erstellen den Container `Items` in der Datenbank `Tasks`.

## <a name="create-the-items-container"></a>Erstellen des Containers „Items“

1. Öffnen Sie im Browser in einer neuen Registerkarte eine zweite Instanz des [Azure-Portals](https://portal.azure.com).

1. Erweitern Sie links im Portal die Symbolleiste, geben Sie `cosmos` in das Suchfeld ein, und wählen Sie **Azure Cosmos DB** aus.

    ![Suchen nach dem Azure Cosmos DB-Dienst](./media/functions-create-cosmos-db-triggered-function/functions-search-cosmos-db.png)

1. Wählen Sie Ihr Azure Cosmos DB-Konto aus, und wählen Sie dann den **Daten-Explorer**. 

1. Wählen Sie unter **SQL-API** die Datenbank **Aufgaben** und dann **Neuer Container** aus.

    ![Erstellen eines Containers](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-container.png)

1. Verwenden Sie in **Container hinzufügen** die Einstellungen, die in der Tabelle unten in der Abbildung gezeigt werden. 

    ![Definieren des Containers „Aufgaben“](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-container2.png)

    | Einstellung|Vorgeschlagener Wert|BESCHREIBUNG |
    | ---|---|--- |
    | **Datenbank-ID** | Aufgaben |Der Name Ihrer neuen Datenbank. Dieser muss mit dem in der Funktionsbindung definierten Namen übereinstimmen. |
    | **Container-ID** | Items | Der Name für den neuen Container. Dieser muss mit dem in der Funktionsbindung definierten Namen übereinstimmen.  |
    | **[Partitionsschlüssel](../cosmos-db/partitioning-overview.md)** | /category|Ein Partitionsschlüssel, der Daten gleichmäßig auf alle Partitionen verteilt. Die Auswahl des richtigen Partitionsschlüssels ist wichtig für die Erstellung eines leistungsfähigen Containers. | 
    | **Durchsatz** |400 RU| Verwenden Sie den Standardwert. Sie können den Durchsatz später hochskalieren, wenn Sie Wartezeiten reduzieren möchten. |    

1. Klicken Sie auf **OK**, um den Container „Items“ zu erstellen. Es dauert möglicherweise kurze Zeit, bis der Container erstellt ist.

Sobald der in der Funktionsbindung angegebene Container vorhanden ist, können Sie die Funktion durch Hinzufügen von Elementen zu diesem neuen Container testen.

## <a name="test-the-function"></a>Testen der Funktion

1. Erweitern Sie in Data Explorer den neuen Container **Items**, und wählen Sie **Elemente** und dann **Neues Element** aus.

    :::image type="content" source="./media/functions-create-cosmos-db-triggered-function/create-item-in-container.png" alt-text="Erstellen eines Elements im Container „Items“":::

1. Ersetzen Sie den Inhalt des neuen Elements durch den folgenden Inhalt, und wählen Sie dann **Speichern** aus.

    ```yaml
    {
        "id": "task1",
        "category": "general",
        "description": "some task"
    }
    ```

1. Wechseln Sie zur ersten Browserregisterkarte, die die Funktion im Portal enthält. Erweitern Sie die Funktionsprotokolle, und stellen Sie sicher, dass das neue Dokument die Funktion ausgelöst hat. Vergewissern Sie sich, dass der `task1`-Dokument-ID-Wert in die Protokolle geschrieben wurde. 

    ![Zeigen Sie die Meldung in den Protokollen an.](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-view-logs.png)

1. (Optional) Wechseln Sie zurück zu Ihrem Dokument, nehmen Sie eine Änderung vor, und klicken Sie auf **Aktualisieren**. Wechseln Sie anschließend zurück zu den Funktionsprotokollen, und stellen Sie sicher, dass die Aktualisierung auch die Funktion ausgelöst hat.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nächste Schritte

Sie haben eine Funktion erstellt, die ausgeführt wird, wenn Ihrer Azure Cosmos DB ein Dokument hinzugefügt oder ein Dokument darin geändert wird. Weitere Informationen zu Azure Cosmos DB-Triggern finden Sie unter [Azure Cosmos DB-Bindungen für Azure Functions](functions-bindings-cosmosdb.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]