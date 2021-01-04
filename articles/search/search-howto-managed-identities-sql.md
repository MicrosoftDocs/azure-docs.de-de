---
title: Einrichten einer Verbindung mit Azure SQL-Datenbank mithilfe einer verwalteten Identität
titleSuffix: Azure Cognitive Search
description: Erfahren Sie, wie Sie eine Indexerverbindung mit Azure SQL-Datenbank mithilfe einer verwalteten Identität einrichten.
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: b940da2cf754e7e1cac91df6b517ecebe55e8c40
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358421"
---
# <a name="set-up-an-indexer-connection-to-azure-sql-database-using-a-managed-identity"></a>Einrichten einer Indexerverbindung mit Azure SQL-Datenbank mithilfe einer verwalteten Identität

Auf dieser Seite wird beschrieben, wie Sie eine Indexerverbindung mit Azure SQL-Datenbank mithilfe einer verwalteten Identität einrichten, anstatt Anmeldeinformationen in der Verbindungszeichenfolge des Datenquellenobjekts anzugeben.

Bevor Sie mehr über diese Funktion erfahren, sollten Sie wissen, was ein Indexer ist, und wie Sie einen Indexer für Ihre Datenquelle einrichten. Weitere Informationen finden Sie unter den folgenden Links:

* [Indexer (Übersicht)](search-indexer-overview.md)
* [Azure SQL-Indexer](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>Einrichten einer Verbindung mithilfe einer verwalteten Identität

### <a name="1---turn-on-system-assigned-managed-identity"></a>1\. Aktivieren einer systemseitig zugewiesenen verwalteten Identität

Wenn eine systemseitig zugewiesene verwaltete Identität aktiviert ist, wird in Azure eine Identität für den Suchdienst erstellt, die für die Authentifizierung bei anderen Azure-Diensten innerhalb desselben Mandanten und desselben Abonnements verwendet werden kann. Anschließend können Sie diese Identität in Azure RBAC-Zuweisungen (rollenbasierte Zugriffssteuerung) verwenden, die den Zugriff auf Daten während der Indizierung ermöglichen.

![Aktivieren einer systemseitig zugewiesenen verwalteten Identität](./media/search-managed-identities/turn-on-system-assigned-identity.png "Aktivieren einer systemseitig zugewiesenen verwalteten Identität")

Nach dem Auswählen von **Speichern** wird eine Objekt-ID angezeigt, die dem Suchdienst zugewiesen wurde.

![Objekt-ID](./media/search-managed-identities/system-assigned-identity-object-id.png "ObjectID")

### <a name="2---provision-azure-active-directory-admin-for-sql-server"></a>2\. Bereitstellen eines Azure Active Directory-Administrators für SQL Server

Beim Herstellen einer Verbindung mit der Datenbank im nächsten Schritt müssen Sie eine Verbindung mit einem Azure Active Directory-Konto (Azure AD-Konto) herstellen, das über Administratorzugriff für die Datenbank verfügt, damit dem Suchdienst die Berechtigung zum Zugriff auf die Datenbank erteilt wird.

Befolgen Sie die [hier](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#provision-azure-ad-admin-sql-database) aufgeführten Anweisungen, um Ihrem Azure AD-Konto Administratorzugriff für die Datenbank zu erteilen.

### <a name="3---assign-the-search-service-permissions"></a>3\. Zuweisen der Berechtigungen für den Suchdienst

Führen Sie die folgenden Schritte aus, um dem Suchdienst die Berechtigung zum Lesen der Datenbank zuzuweisen.

1. Herstellen der Verbindung mit Visual Studio

    ![Herstellen der Verbindung mit Visual Studio](./media/search-managed-identities/connect-with-visual-studio.png "Herstellen der Verbindung mit Visual Studio")

2. Authentifizierung mit dem Azure AD-Konto

    ![Authentifizieren](./media/search-managed-identities/visual-studio-authentication.png "Authenticate")

3. Führen Sie die folgenden Befehle aus:

    Setzen Sie den Namen des Suchdiensts in Klammern.
    
    ```
    CREATE USER [your search service name here] FROM EXTERNAL PROVIDER;
    EXEC sp_addrolemember 'db_datareader', [your search service name here];
    ```

    ![Neue Abfrage](./media/search-managed-identities/visual-studio-new-query.png "Neue Abfrage")

    ![Ausführen der Abfrage](./media/search-managed-identities/visual-studio-execute-query.png "Abfrage ausführen")

>[!NOTE]
> Wenn die Identität des Suchdiensts aus Schritt 1 nach dem Ausführen dieses Schritts geändert wird, müssen Sie die Rollenmitgliedschaft und den Benutzer in der SQL-Datenbank entfernen und dann durch erneutes Ausführen von Schritt 3 die Berechtigungen erneut hinzufügen.
> Die Rollenmitgliedschaft und der Benutzer können durch Ausführen der folgenden Befehle entfernt werden:
> ```
> sp_droprolemember 'db_datareader', [your search service name];
> DROP USER IF EXISTS [your search service name];
> ```

### <a name="4---add-a-role-assignment"></a>4\. Hinzufügen einer Rollenzuweisung

In diesem Schritt erteilen Sie dem Azure Cognitive Search-Dienst die Berechtigung, Daten von Ihrem SQL Server zu lesen.

1. Navigieren Sie im Azure-Portal zur Seite des Azure SQL Servers.
2. Wählen Sie **Zugriffssteuerung (IAM)** aus.
3. Klicken Sie auf **Hinzufügen** und dann auf **Rollenzuweisung hinzufügen**.

    ![Hinzufügen der Rollenzuweisung](./media/search-managed-identities/add-role-assignment-sql-server.png "Rollenzuweisung hinzufügen")

4. Wählen Sie die entsprechende Rolle **Leser** aus.
5. Behalten Sie unter **Zugriff zuweisen zu** die Option **Azure AD-Benutzer, -Gruppe oder -Dienstprinzipal** bei.
6. Suchen Sie nach Ihrem Suchdienst, wählen Sie ihn aus, und wählen Sie dann **Speichern** aus.

    ![Hinzufügen der Rollenzuweisung für „Leser“](./media/search-managed-identities/add-role-assignment-sql-server-reader-role.png "Hinzufügen der Rollenzuweisung für „Leser“")

### <a name="5---create-the-data-source"></a>5\. Erstellen der Datenquelle

Die Verbindungszeichenfolge für verwaltete Identitäten wird von der [REST-API](/rest/api/searchservice/create-data-source), dem Azure-Portal und dem [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection) unterstützt. Im Folgenden finden Sie ein Beispiel für das Erstellen einer Datenquelle zum Indizieren von Daten aus einer Azure SQL-Datenbank mithilfe der [REST-API](/rest/api/searchservice/create-data-source) und einer Verbindungszeichenfolge für verwaltete Identitäten. Das Format der Verbindungszeichenfolge für verwaltete Identitäten ist für die REST-API, das .NET SDK und das Azure-Portal identisch.

Beim Erstellen einer Datenquelle mit der [REST-API](/rest/api/searchservice/create-data-source) muss die Datenquelle die folgenden erforderlichen Eigenschaften aufweisen:

* **name** ist der eindeutige Name der Datenquelle im Suchdienst.
* **type** ist `azuresql`.
* **credentials**
    * Beim Verwenden einer verwalteten Identität zur Authentifizierung unterscheidet sich das Format für **credentials** von dem bei der Authentifizierung ohne verwaltete Identität. Hier geben Sie den Namen für „Initial Catalog“ oder „Initial Database“ und eine „ResourceId“ an, der kein Kontoschlüssel und kein Kennwort zugewiesen ist. Die „ResourceId“ muss die Abonnement-ID von Azure SQL-Datenbank, die Ressourcengruppe der SQL-Datenbank und den Namen der SQL-Datenbank enthalten. 
    * Format der Verbindungszeichenfolge für verwaltete Identitäten:
        * *Initial Catalog|Database= **Datenbankname** ;ResourceId=/subscriptions/ **Ihre Abonnement-ID** /resourceGroups/ **Name Ihrer Ressourcengruppe** /providers/Microsoft.Sql/servers/ **Name Ihres SQL-Servers** /;Connection Timeout= **Dauer des Verbindungstimeouts** ;*
* **container** gibt den Namen der Tabelle oder Ansicht an, die indiziert werden soll.

Beispiel für die Erstellung eines Azure SQL-Datenquellenobjekts über die [REST-API](/rest/api/searchservice/create-data-source):

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-datasource",
    "type" : "azuresql",
    "credentials" : { "connectionString" : "Database=sql-database;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azure-sql-resource-group/providers/Microsoft.Sql/servers/sql-server-search-demo;Connection Timeout=30;" },
    "container" : { "name" : "my-table" }
} 
```

### <a name="6---create-the-index"></a>6\. Erstellen des Index

Mit dem Index werden die Felder in einem Dokument, Attribute und andere Konstrukte für die Suchoberfläche angegeben.

So erstellen Sie einen Index mit einem durchsuchbaren `booktitle`-Feld   

```
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
    { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

Weitere Informationen zum Erstellen von Indizes finden Sie unter [Create Index](/rest/api/searchservice/create-index) (Index erstellen).

### <a name="7---create-the-indexer"></a>7\. Erstellen des Indexers

Ein Indexer verbindet eine Datenquelle mit einem Zielsuchindex und stellt einen Zeitplan zur Automatisierung der Datenaktualisierung bereit.

Nach der Erstellung von Index und Datenquelle können Sie den Indexer erstellen.

Beispiel für die Indexerdefinition eines Azure SQL-Indexers:

```
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "sql-indexer",
    "dataSourceName" : "sql-datasource",
    "targetIndexName" : "my-target-index",
    "schedule" : { "interval" : "PT2H" }
}
```    

Dieser Indexer wird alle zwei Stunden ausgeführt (das Planungsintervall wird auf „PT2H“ festgelegt). Um einen Indexer alle 30 Minuten auszuführen, legen Sie das Intervall auf „PT30M“ fest. Das kürzeste unterstützte Intervall beträgt fünf Minuten. Der Zeitplan ist optional. Ohne Zeitplan wird ein Indexer nur einmal bei seiner Erstellung ausgeführt. Allerdings können Sie ein Indexer bei Bedarf jederzeit ausführen.   

Weitere Informationen zur API zum Erstellen eines Indexers finden Sie unter [Erstellen eines Indexers](/rest/api/searchservice/create-indexer).

Weitere Informationen zum Definieren von Indexerzeitplänen finden Sie unter [Festlegen eines Zeitplans für Indexer in der kognitiven Azure-Suche](search-howto-schedule-indexers.md).

## <a name="troubleshooting"></a>Problembehandlung

Wenn beim Versuch des Indexers, eine Verbindung mit der Datenquelle herzustellen, eine Fehlermeldung angezeigt wird, in der angegeben wird, dass der Zugriff des Clients auf den Server nicht zulässig ist, finden Sie entsprechende Informationen unter den [häufigen Fehlern bei Indexern](./search-indexer-troubleshooting.md).

## <a name="see-also"></a>Weitere Informationen

Weitere Informationen zum Azure SQL-Indexer:
* [Azure SQL-Indexer](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)