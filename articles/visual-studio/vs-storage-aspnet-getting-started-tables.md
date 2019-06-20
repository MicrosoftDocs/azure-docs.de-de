---
title: Erste Schritte mit Azure Table Storage und verbundenen Visual Studio-Diensten (ASP.NET) | Microsoft-Dokumentation
description: Erfahren Sie mehr über die ersten Schritte mit Azure Table Storage in einem ASP.NET-Projekt in Visual Studio, nachdem Sie mithilfe von verbundenen Visual Studio-Diensten eine Verbindung mit einem Speicherkonto hergestellt haben.
services: storage
author: ghogen
manager: douge
ms.assetid: af81a326-18f4-4449-bc0d-e96fba27c1f8
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/21/2016
ms.author: ghogen
ms.openlocfilehash: ea50506df53bfd586656d0030be4536d9d3b907d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62122977"
---
# <a name="get-started-with-azure-table-storage-and-visual-studio-connected-services-aspnet"></a>Erste Schritte mit Azure Table Storage und verbundenen Visual Studio-Diensten (ASP.NET)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Übersicht

Azure-Tabellenspeicher ermöglicht das Speichern großer Mengen von strukturierten Daten. Der Dienst ist ein NoSQL-Datenspeicher, der authentifizierte Aufrufe von innerhalb und außerhalb der Azure-Cloud akzeptiert. Azure-Tabellen sind hervorragend zur Speicherung strukturierter nicht relationaler Daten geeignet.

In diesem Tutorial wird gezeigt, wie Sie ASP.NET-Code für einige häufig verwendete Szenarien mit Azure-Tabellenspeicherentitäten schreiben. Die behandelten Szenarien umfassen das Erstellen einer Tabelle sowie das Hinzufügen, Abfragen und Löschen von Tabellenentitäten. 

## <a name="prerequisites"></a>Voraussetzungen

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure-Speicherkonto](../storage/common/storage-quickstart-create-account.md)

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Erstellen eines MVC-Controllers 

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Controller**, und wählen Sie im Kontextmenü **Hinzufügen > Controller** aus.

    ![Hinzufügen eines Controllers zu einer ASP.NET-MVC-App](./media/vs-storage-aspnet-getting-started-tables/add-controller-menu.png)

1. Wählen Sie im Dialogfeld **Gerüst hinzufügen** die Option **MVC 5 Controller - Leer** und dann **Hinzufügen**.

    ![Angeben des MVC-Controllertyps](./media/vs-storage-aspnet-getting-started-tables/add-controller.png)

1. Benennen Sie den Controller im Dialogfeld **Controller hinzufügen** mit *TablesController*, und wählen Sie **Hinzufügen**.

    ![Benennen des MVC-Controllers](./media/vs-storage-aspnet-getting-started-tables/add-controller-name.png)

1. Fügen Sie die folgenden *using*-Anweisungen der `TablesController.cs`-Datei hinzu:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;
    ```

### <a name="create-a-model-class"></a>Erstellen einer Modellklasse

In vielen der Beispiele in diesem Artikel wird eine **TableEntity**-abgeleitete Klasse mit dem Namen **CustomerEntity** verwendet. Die folgenden Schritte führen Sie durch das Deklarieren dieser Klasse als Modellklasse:

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Modelle**, und wählen Sie im Kontextmenü **Hinzufügen > Klasse** aus.

1. Geben Sie der Klasse im Dialogfeld **Neues Element hinzufügen** den Namen **CustomerEntity**.

1. Öffnen Sie die `CustomerEntity.cs`-Datei, und fügen Sie die folgenden **using**-Direktiven hinzu:

    ```csharp
    using Microsoft.WindowsAzure.Storage.Table;
    ```

1. Ändern Sie die Klasse so, dass sie anschließend wie im folgenden Code deklariert wird. Die Klasse deklariert eine Entitätsklasse namens **CustomerEntity**, die den Vornamen des Kunden als Zeilenschlüssel und den Nachnamen als Partitionsschlüssel verwendet.

    ```csharp
    public class CustomerEntity : TableEntity
    {
        public CustomerEntity(string lastName, string firstName)
        {
            this.PartitionKey = lastName;
            this.RowKey = firstName;
        }

        public CustomerEntity() { }

        public string Email { get; set; }
    }
    ```

## <a name="create-a-table"></a>Erstellen einer Tabelle

Die folgenden Schritte veranschaulichen, wie Sie eine Tabelle erstellen:

> [!NOTE]
> 
> In diesem Abschnitt wird vorausgesetzt, dass Sie die Schritte zum [Einrichten der Entwicklungsumgebung](#set-up-the-development-environment) abgeschlossen haben. 

1. Öffnen Sie die Datei `TablesController.cs` .

1. Fügen Sie eine Methode namens **CreateTable** hinzu, die ein **ActionResult** zurückgibt.

    ```csharp
    public ActionResult CreateTable()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Rufen Sie in der **CreateTable**-Methode ein **CloudStorageAccount**-Objekt ab, das die Informationen zu Ihrem Speicherkonto enthält. Verwenden Sie den folgenden Code, um die Speicherverbindungszeichenfolge und Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen: (Ändern Sie *&lt;storage-account-name>* in den Namen des Azure-Speicherkontos, auf das Sie zugreifen.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Rufen Sie ein **CloudTableClient**-Objekt ab, das einen Tabellenspeicherdienst-Client darstellt.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Rufen Sie ein **CloudTable**-Objekt ab, das einen Verweis auf den gewünschten Tabellennamen darstellt. Die **CloudTableClient.GetTableReference**-Methode stellt keine Anforderung an den Tabellenspeicher. Der Verweis wird zurückgegeben, und zwar unabhängig davon, ob die Tabelle vorhanden ist oder nicht. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Rufen Sie die **CloudTable.CreateIfNotExists**-Methode auf, um die Tabelle zu erstellen, falls noch nicht vorhanden. Die **CloudTable.CreateIfNotExists**-Methode gibt **true** zurück, wenn die Tabelle nicht vorhanden ist und erfolgreich erstellt wurde. Andernfalls wird **false** zurückgegeben.    

    ```csharp
    ViewBag.Success = table.CreateIfNotExists();
    ```

1. Aktualisieren Sie **ViewBag** mit dem Namen der Tabelle.

    ```csharp
    ViewBag.TableName = table.Name;
    ```

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Ansichten**, klicken Sie mit der rechten Maustaste auf **Tabellen**, und wählen Sie im Kontextmenü **Hinzufügen > Ansicht** aus.

1. Geben Sie im Dialogfeld **Ansicht hinzufügen** für den Ansichtsnamen **CreateTable** ein, und wählen Sie **Hinzufügen**.

1. Öffnen Sie `CreateTable.cshtml`, und ändern Sie die Datei so, dass sie folgendem Codeausschnitt entspricht:

    ```csharp
    @{
        ViewBag.Title = "Create Table";
    }
    
    <h2>Create Table results</h2>

    Creation of @ViewBag.TableName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Views > Shared**, und öffnen Sie `_Layout.cshtml`.

1. Fügen Sie nach dem letzten **Html.ActionLink** den folgenden **Html.ActionLink** hinzu:

    ```html
    <li>@Html.ActionLink("Create table", "CreateTable", "Tables")</li>
    ```

1. Führen Sie die Anwendung aus, und wählen Sie **Tabelle erstellen** aus, um ähnliche Ergebnisse wie im folgenden Screenshot zu sehen:
  
    ![Tabelle erstellen](./media/vs-storage-aspnet-getting-started-tables/create-table-results.png)

    Wie bereits erwähnt, gibt die **CloudTable.CreateIfNotExists**-Methode nur dann **true** zurück, wenn die Tabelle nicht vorhanden ist und erstellt wird. Aus diesem Grund gibt die Methode **false** zurück, wenn Sie die App ausführen, wenn die Tabelle vorhanden ist. Um die App mehrere Male auszuführen, müssen Sie die Tabelle vor dem erneuten Ausführen der App löschen. Das Löschen der Tabelle kann mit der **CloudTable.Delete**-Methode durchgeführt werden. Sie können die Tabelle auch über das [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) oder den [Microsoft Azure Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) löschen.  

## <a name="add-an-entity-to-a-table"></a>Hinzufügen einer Entität zu einer Tabelle

*Entitäten* werden C\#-Objekten zugeordnet, indem eine benutzerdefinierte Klasse verwendet wird, die aus **TableEntity** abgeleitet wurde. Erstellen Sie eine Klasse, mit der die Eigenschaften der Entität definiert werden, um eine Entität zu einer Tabelle hinzuzufügen. In diesem Abschnitt erfahren Sie, wie eine Entitätsklasse definiert wird, die den Vornamen des Kunden als Zeilenschlüssel und den Nachnamen als Partitionsschlüssel verwendet. In Kombination miteinander wird mit dem Partitions- und Zeilenschlüssel eine Entität in der Tabelle eindeutig identifiziert. Entitäten mit dem gleichen Partitionsschlüssel können schneller abgerufen werden als Entitäten mit unterschiedlichen Partitionsschlüsseln, die Verwendung verschiedener Partitionsschlüssel ermöglicht jedoch eine größere Skalierbarkeit paralleler Vorgänge. Bei jeder Eigenschaft, die im Tabellendienst gespeichert werden soll, muss es sich um eine öffentliche Eigenschaft eines unterstützten Typs handeln, für die sowohl Werte festgelegt als auch Werte abgerufen werden können.
Die Entitätsklasse *muss* einen öffentlichen parameterlosen Konstruktor deklarieren.

> [!NOTE]
> 
> In diesem Abschnitt wird vorausgesetzt, dass Sie die Schritte zum [Einrichten der Entwicklungsumgebung](#set-up-the-development-environment) abgeschlossen haben.

1. Öffnen Sie die Datei `TablesController.cs` .

1. Fügen Sie die folgende Direktive hinzu, damit der Code in der `TablesController.cs`-Datei auf die **CustomerEntity**-Klasse zugreifen kann:

    ```csharp
    using StorageAspnet.Models;
    ```

1. Fügen Sie eine Methode namens **AddEntity** hinzu, die ein **ActionResult** zurückgibt.

    ```csharp
    public ActionResult AddEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Rufen Sie in der **AddEntity**-Methode ein **CloudStorageAccount**-Objekt ab, das die Informationen zu Ihrem Speicherkonto enthält. Verwenden Sie den folgenden Code, um die Speicherverbindungszeichenfolge und Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen: (Ändern Sie *&lt;storage-account-name>* in den Namen des Azure-Speicherkontos, auf das Sie zugreifen.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Rufen Sie ein **CloudTableClient**-Objekt ab, das einen Tabellenspeicherdienst-Client darstellt.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Rufen Sie ein **CloudTable**-Objekt ab, das einen Verweis auf die Tabelle darstellt, der Sie die neue Entität hinzufügen möchten. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Instanziieren und initialisieren Sie die **CustomerEntity**-Klasse.

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    ```

1. Erstellen Sie ein **TableOperation**-Objekt, das die Kundenentität einfügt.

    ```csharp
    TableOperation insertOperation = TableOperation.Insert(customer1);
    ```

1. Führen Sie den Einfügevorgang durch Aufrufen der **CloudTable.Execute**-Methode aus. Sie können das Ergebnis des Vorgangs überprüfen, indem Sie die **TableResult.HttpStatusCode**-Eigenschaft untersuchen. Der Statuscode 2xx gibt an, dass die vom Client angeforderte Aktion erfolgreich verarbeitet wurde. Erfolgreiche Einfügungen neuer Entitäten führen z.B. zum HTTP-Statuscode 204, was bedeutet, dass der Vorgang erfolgreich verarbeitet wurde und der Server keine Inhalte zurückgegeben hat.

    ```csharp
    TableResult result = table.Execute(insertOperation);
    ```

1. Aktualisieren Sie **ViewBag** mit dem Tabellennamen und den Ergebnissen des Einfügevorgangs.

    ```csharp
    ViewBag.TableName = table.Name;
    ViewBag.Result = result.HttpStatusCode;
    ```

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Ansichten**, klicken Sie mit der rechten Maustaste auf **Tabellen**, und wählen Sie im Kontextmenü **Hinzufügen > Ansicht** aus.

1. Geben Sie im Dialogfeld **Ansicht hinzufügen** für den Ansichtsnamen **AddEntity** ein, und wählen Sie **Hinzufügen**.

1. Öffnen Sie `AddEntity.cshtml`, und ändern Sie die Datei so, dass sie folgendem Codeausschnitt entspricht:

    ```csharp
    @{
        ViewBag.Title = "Add entity";
    }
    
    <h2>Add entity results</h2>

    Insert of entity into @ViewBag.TableName @(ViewBag.Result == 204 ? "succeeded" : "failed")
    ```
1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Views > Shared**, und öffnen Sie `_Layout.cshtml`.

1. Fügen Sie nach dem letzten **Html.ActionLink** den folgenden **Html.ActionLink** hinzu:

    ```html
    <li>@Html.ActionLink("Add entity", "AddEntity", "Tables")</li>
    ```

1. Führen Sie die Anwendung aus, und wählen Sie **Entität hinzufügen** aus, um ähnliche Ergebnisse wie im folgenden Screenshot zu sehen:
  
    ![Hinzufügen einer Entität](./media/vs-storage-aspnet-getting-started-tables/add-entity-results.png)

    Sie können anhand der Schritte im Abschnitt [Abrufen einer einzelnen Entität](#get-a-single-entity) überprüfen, ob die Entität hinzugefügt wurde. Sie können auch mit dem [Microsoft Azure Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) alle Entitäten für Ihre Tabellen anzeigen.

## <a name="add-a-batch-of-entities-to-a-table"></a>Hinzufügen eines Entitätsbatchs zu einer Tabelle

Sie können nicht nur Entitäten [einzeln nacheinander einer Tabelle hinzufügen](#add-an-entity-to-a-table), sondern auch Entitäten als Batch hinzufügen. Das Hinzufügen von Entitäten als Batch reduziert die Anzahl von Roundtrips zwischen Ihrem Code und dem Azure-Tabellenspeicherdienst. Die folgenden Schritte zeigen, wie Sie einer Tabelle mehrere Entitäten in einem einzigen Vorgang hinzufügen:

> [!NOTE]
> 
> In diesem Abschnitt wird vorausgesetzt, dass Sie die Schritte zum [Einrichten der Entwicklungsumgebung](#set-up-the-development-environment) abgeschlossen haben.

1. Öffnen Sie die Datei `TablesController.cs` .

1. Fügen Sie eine Methode namens **AddEntities** hinzu, die ein **ActionResult** zurückgibt.

    ```csharp
    public ActionResult AddEntities()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Rufen Sie in der **AddEntities**-Methode ein **CloudStorageAccount**-Objekt ab, das die Informationen zu Ihrem Speicherkonto enthält. Verwenden Sie den folgenden Code, um die Speicherverbindungszeichenfolge und Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen: (Ändern Sie *&lt;storage-account-name>* in den Namen des Azure-Speicherkontos, auf das Sie zugreifen.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Rufen Sie ein **CloudTableClient**-Objekt ab, das einen Tabellenspeicherdienst-Client darstellt.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Rufen Sie ein **CloudTable**-Objekt ab, das einen Verweis auf die Tabelle darstellt, der Sie die neuen Entitäten hinzufügen möchten. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Instanziieren Sie einige Kundenobjekte, die auf der **CustomerEntity**-Modellklasse basieren, die im Abschnitt [Hinzufügen einer Entität zu einer Tabelle](#add-an-entity-to-a-table) vorgestellt werden.

    ```csharp
    CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
    customer1.Email = "Jeff@contoso.com";

    CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
    customer2.Email = "Ben@contoso.com";
    ```

1. Rufen Sie ein **TableBatchOperation**-Objekt ab.

    ```csharp
    TableBatchOperation batchOperation = new TableBatchOperation();
    ```

1. Fügen Sie Entitäten dem Batcheinfügevorgangs-Objekt hinzu.

    ```csharp
    batchOperation.Insert(customer1);
    batchOperation.Insert(customer2);
    ```

1. Führen Sie den Batcheinfügevorgang durch Aufrufen der **CloudTable.ExecuteBatch**-Methode aus.   

    ```csharp
    IList<TableResult> results = table.ExecuteBatch(batchOperation);
    ```

1. Die **CloudTable.ExecuteBatch**-Methode gibt eine Liste von **TableResult**-Objekten zurück, wobei jedes **TableResult**-Objekt untersucht werden kann, um festzustellen, ob die einzelnen Vorgänge erfolgreich verlaufen, oder ob Fehler aufgetreten sind. In diesem Beispiel übergeben Sie die Liste einer Ansicht, und lassen Sie die Ergebnisse jedes Vorgangs in der Ansicht anzeigen. 
 
    ```csharp
    return View(results);
    ```

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Ansichten**, klicken Sie mit der rechten Maustaste auf **Tabellen**, und wählen Sie im Kontextmenü **Hinzufügen > Ansicht** aus.

1. Geben Sie im Dialogfeld **Ansicht hinzufügen** für den Ansichtsnamen **AddEntities** ein, und wählen Sie **Hinzufügen**.

1. Öffnen Sie `AddEntities.cshtml`, und ändern Sie die Datei folgendermaßen:

    ```csharp
    @model IEnumerable<Microsoft.WindowsAzure.Storage.Table.TableResult>
    @{
        ViewBag.Title = "AddEntities";
    }
    
    <h2>Add-entities results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        @foreach (var result in Model)
        {
        <tr>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((result.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@result.HttpStatusCode</td>
        </tr>
        }
    </table>
    ```

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Views > Shared**, und öffnen Sie `_Layout.cshtml`.

1. Fügen Sie nach dem letzten **Html.ActionLink** den folgenden **Html.ActionLink** hinzu:

    ```html
    <li>@Html.ActionLink("Add entities", "AddEntities", "Tables")</li>
    ```

1. Führen Sie die Anwendung aus, und wählen Sie **Entitäten hinzufügen** aus, um ähnliche Ergebnisse wie im folgenden Screenshot zu sehen:
  
    ![Hinzufügen von Entitäten](./media/vs-storage-aspnet-getting-started-tables/add-entities-results.png)

    Sie können anhand der Schritte im Abschnitt [Abrufen einer einzelnen Entität](#get-a-single-entity) überprüfen, ob die Entität hinzugefügt wurde. Sie können auch mit dem [Microsoft Azure Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) alle Entitäten für Ihre Tabellen anzeigen.

## <a name="get-a-single-entity"></a>Abrufen einer einzelnen Entität

Dieser Abschnitt veranschaulicht, wie eine einzelne Entität aus einer Tabelle mithilfe des Zeilenschlüssels und Partitionsschlüssels der Entität abgerufen wird. 

> [!NOTE]
> 
> Dieser Abschnitt setzt voraus, dass Sie die Schritte in [Einrichten der Entwicklungsumgebung](#set-up-the-development-environment) abgeschlossen haben, und verwendet Daten aus [Hinzufügen eines Entitätsbatchs zu einer Tabelle](#add-a-batch-of-entities-to-a-table). 

1. Öffnen Sie die Datei `TablesController.cs` .

1. Fügen Sie eine Methode namens **GetSingle** hinzu, die ein **ActionResult** zurückgibt.

    ```csharp
    public ActionResult GetSingle()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Rufen Sie in der **GetSingle**-Methode ein **CloudStorageAccount**-Objekt ab, das die Informationen zu Ihrem Speicherkonto enthält. Verwenden Sie den folgenden Code, um die Speicherverbindungszeichenfolge und Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen: (Ändern Sie *&lt;storage-account-name>* in den Namen des Azure-Speicherkontos, auf das Sie zugreifen.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Rufen Sie ein **CloudTableClient**-Objekt ab, das einen Tabellenspeicherdienst-Client darstellt.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Rufen Sie ein **CloudTable**-Objekt ab, das einen Verweis auf die Tabelle darstellt, aus der Sie die Entität abrufen. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Erstellen Sie ein Abrufvorgangsobjekt, das ein von **TableEntity** abgeleitetes Entitätsobjekt verwendet. Der erste Parameter ist *partitionKey* und der zweite Parameter *rowKey*. Mithilfe der **CustomerEntity**-Klasse und den Daten aus dem Abschnitt [Hinzufügen eines Entitätsbatchs zu einer Tabelle](#add-a-batch-of-entities-to-a-table) fragt der folgende Codeausschnitt die Tabelle auf eine **CustomerEntity**-Entität mit dem *partitionKey*-Wert „Smith“ und *rowKey*-Wert „Ben“ ab:

    ```csharp
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
    ```

1. Führen Sie den Abrufvorgang aus.   

    ```csharp
    TableResult result = table.Execute(retrieveOperation);
    ```

1. Übergeben Sie das Ergebnis der Ansicht zur Anzeige.

    ```csharp
    return View(result);
    ```

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Ansichten**, klicken Sie mit der rechten Maustaste auf **Tabellen**, und wählen Sie im Kontextmenü **Hinzufügen > Ansicht** aus.

1. Geben Sie im Dialogfeld **Ansicht hinzufügen** für den Ansichtsnamen **GetSingle** ein, und wählen Sie **Hinzufügen**.

1. Öffnen Sie `GetSingle.cshtml`, und ändern Sie die Datei so, dass sie folgendem Codeausschnitt entspricht:

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "GetSingle";
    }
    
    <h2>Get Single results</h2>
    
    <table border="1">
        <tr>
            <th>HTTP result</th>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        <tr>
            <td>@Model.HttpStatusCode</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).Email)</td>
        </tr>
    </table>
    ```

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Views > Shared**, und öffnen Sie `_Layout.cshtml`.

1. Fügen Sie nach dem letzten **Html.ActionLink** den folgenden **Html.ActionLink** hinzu:

    ```html
    <li>@Html.ActionLink("Get single", "GetSingle", "Tables")</li>
    ```

1. Führen Sie die Anwendung aus, und wählen Sie **Einzelne Entität abrufen** aus, um ähnliche Ergebnisse wie im folgenden Screenshot zu sehen:
  
    ![Abrufen einer einzelnen Entität](./media/vs-storage-aspnet-getting-started-tables/get-single-results.png)

## <a name="get-all-entities-in-a-partition"></a>Abrufen aller Entitäten einer Partition

Wie im Abschnitt [Hinzufügen einer Entität zu einer Tabelle](#add-an-entity-to-a-table) erwähnt, identifiziert die Kombination aus einer Partition und einem Zeilenschlüssel eine Entität in einer Tabelle eindeutig. Entitäten mit dem gleichen Partitionsschlüssel können schneller abgefragt werden als Entitäten mit verschiedenen Schlüsseln. Dieser Abschnitt veranschaulicht das Abfragen einer Tabelle für alle Entitäten einer angegebenen Partition.  

> [!NOTE]
> 
> Dieser Abschnitt setzt voraus, dass Sie die Schritte in [Einrichten der Entwicklungsumgebung](#set-up-the-development-environment) abgeschlossen haben, und verwendet Daten aus [Hinzufügen eines Entitätsbatchs zu einer Tabelle](#add-a-batch-of-entities-to-a-table). 

1. Öffnen Sie die Datei `TablesController.cs` .

1. Fügen Sie eine Methode namens **GetPartition** hinzu, die ein **ActionResult** zurückgibt.

    ```csharp
    public ActionResult GetPartition()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Rufen Sie in der **GetPartition**-Methode ein **CloudStorageAccount**-Objekt ab, das die Informationen zu Ihrem Speicherkonto enthält. Verwenden Sie den folgenden Code, um die Speicherverbindungszeichenfolge und Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen: (Ändern Sie *&lt;storage-account-name>* in den Namen des Azure-Speicherkontos, auf das Sie zugreifen.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Rufen Sie ein **CloudTableClient**-Objekt ab, das einen Tabellenspeicherdienst-Client darstellt.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Rufen Sie ein **CloudTable**-Objekt ab, das einen Verweis auf die Tabelle darstellt, aus der Sie die Entitäten abrufen. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Instanziieren Sie ein **TableQuery**-Objekt, und geben Sie die Abfrage in der **Where**-Klausel an. Mithilfe der **CustomerEntity**-Klasse und den Daten aus dem Abschnitt [Add a batch of entities to a table](#add-a-batch-of-entities-to-a-table) (Hinzufügen eines Entitätsbatchs zu einer Tabelle) fragt der folgende Codeausschnitt die Tabelle auf alle Entitäten ab, deren **PartitionKey** (Nachname des Kunden) den Wert „Smith“ hat:

    ```csharp
    TableQuery<CustomerEntity> query = 
        new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));
    ```

1. Rufen Sie in einer Schleife die **CloudTable.ExecuteQuerySegmented**-Methode auf, um das Abfrageobjekt zu übergeben, das Sie im vorherigen Schritt instanziiert haben.  Die **CloudTable.ExecuteQuerySegmented**-Methode gibt ein **TableContinuationToken**-Objekt zurück. Wenn diese den Wert **null** hat, bedeutet dies, dass es keine weiteren abzurufenden Entitäten gibt. Verwenden Sie innerhalb der Schleife eine andere Schleife zum Durchlaufen der zurückgegebenen Entitäten. Im folgenden Codebeispiel wird jede zurückgegebene Entität einer Liste hinzugefügt. Sobald die Schleife beendet ist, wird die Liste einer Ansicht zur Anzeige übergeben: 

    ```csharp
    List<CustomerEntity> customers = new List<CustomerEntity>();
    TableContinuationToken token = null;
    do
    {
        TableQuerySegment<CustomerEntity> resultSegment = table.ExecuteQuerySegmented(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity customer in resultSegment.Results)
        {
            customers.Add(customer);
        }
    } while (token != null);

    return View(customers);
    ```

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Ansichten**, klicken Sie mit der rechten Maustaste auf **Tabellen**, und wählen Sie im Kontextmenü **Hinzufügen > Ansicht** aus.

1. Geben Sie im Dialogfeld **Ansicht hinzufügen** für den Ansichtsnamen **GetPartition** ein, und wählen Sie **Hinzufügen**.

1. Öffnen Sie `GetPartition.cshtml`, und ändern Sie die Datei so, dass sie folgendem Codeausschnitt entspricht:

    ```csharp
    @model IEnumerable<StorageAspnet.Models.CustomerEntity>
    @{
        ViewBag.Title = "GetPartition";
    }
    
    <h2>Get Partition results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>Email</th>
        </tr>
        @foreach (var customer in Model)
        {
        <tr>
            <td>@(customer.RowKey)</td>
            <td>@(customer.PartitionKey)</td>
            <td>@(customer.Email)</td>
        </tr>
        }
    </table>
    ```

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Views > Shared**, und öffnen Sie `_Layout.cshtml`.

1. Fügen Sie nach dem letzten **Html.ActionLink** den folgenden **Html.ActionLink** hinzu:

    ```html
    <li>@Html.ActionLink("Get partition", "GetPartition", "Tables")</li>
    ```

1. Führen Sie die Anwendung aus, und wählen Sie **Partition abrufen** aus, um ähnliche Ergebnisse wie im folgenden Screenshot zu sehen:
  
    ![Abrufen einer Partition](./media/vs-storage-aspnet-getting-started-tables/get-partition-results.png)

## <a name="delete-an-entity"></a>Löschen einer Entität

Dieser Abschnitt zeigt das Löschen einer Entität aus einer Tabelle.

> [!NOTE]
> 
> Dieser Abschnitt setzt voraus, dass Sie die Schritte in [Einrichten der Entwicklungsumgebung](#set-up-the-development-environment) abgeschlossen haben, und verwendet Daten aus [Hinzufügen eines Entitätsbatchs zu einer Tabelle](#add-a-batch-of-entities-to-a-table). 

1. Öffnen Sie die Datei `TablesController.cs` .

1. Fügen Sie eine Methode namens **DeleteEntity** hinzu, die ein **ActionResult** zurückgibt.

    ```csharp
    public ActionResult DeleteEntity()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Rufen Sie in der **DeleteEntity**-Methode ein **CloudStorageAccount**-Objekt ab, das die Informationen zu Ihrem Speicherkonto enthält. Verwenden Sie den folgenden Code, um die Speicherverbindungszeichenfolge und Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen: (Ändern Sie *&lt;storage-account-name>* in den Namen des Azure-Speicherkontos, auf das Sie zugreifen.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Rufen Sie ein **CloudTableClient**-Objekt ab, das einen Tabellenspeicherdienst-Client darstellt.
   
    ```csharp
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Rufen Sie ein **CloudTable**-Objekt ab, das einen Verweis auf die Tabelle darstellt, aus der Sie die Entität löschen. 
   
    ```csharp
    CloudTable table = tableClient.GetTableReference("TestTable");
    ```

1. Erstellen Sie ein Löschvorgangsobjekt, das ein von **TableEntity** abgeleitetes Entitätsobjekt verwendet. In diesem Fall werden die **CustomerEntity**-Klasse und Daten verwendet, die im Abschnitt [Hinzufügen eines Entitätsbatchs zu einer Tabelle](#add-a-batch-of-entities-to-a-table) gezeigt wurden. Für das **ETag** der Entität muss ein gültiger Wert festgelegt werden.  

    ```csharp
    TableOperation deleteOperation = 
        TableOperation.Delete(new CustomerEntity("Smith", "Ben") { ETag = "*" } );
    ```

1. Führen Sie den Löschvorgang aus.   

    ```csharp
    TableResult result = table.Execute(deleteOperation);
    ```

1. Übergeben Sie das Ergebnis der Ansicht zur Anzeige.

    ```csharp
    return View(result);
    ```

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Ansichten**, klicken Sie mit der rechten Maustaste auf **Tabellen**, und wählen Sie im Kontextmenü **Hinzufügen > Ansicht** aus.

1. Geben Sie im Dialogfeld **Ansicht hinzufügen** für den Ansichtsnamen **DeleteEntity** ein, und wählen Sie **Hinzufügen**.

1. Öffnen Sie `DeleteEntity.cshtml`, und ändern Sie die Datei so, dass sie folgendem Codeausschnitt entspricht:

    ```csharp
    @model Microsoft.WindowsAzure.Storage.Table.TableResult
    @{
        ViewBag.Title = "DeleteEntity";
    }
    
    <h2>Delete Entity results</h2>
    
    <table border="1">
        <tr>
            <th>First name</th>
            <th>Last name</th>
            <th>HTTP result</th>
        </tr>
        <tr>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).RowKey)</td>
            <td>@((Model.Result as StorageAspnet.Models.CustomerEntity).PartitionKey)</td>
            <td>@Model.HttpStatusCode</td>
        </tr>
    </table>

    ```

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Views > Shared**, und öffnen Sie `_Layout.cshtml`.

1. Fügen Sie nach dem letzten **Html.ActionLink** den folgenden **Html.ActionLink** hinzu:

    ```html
    <li>@Html.ActionLink("Delete entity", "DeleteEntity", "Tables")</li>
    ```

1. Führen Sie die Anwendung aus, und wählen Sie **Entität löschen** aus, um ähnliche Ergebnisse wie im folgenden Screenshot zu sehen:
  
    ![Abrufen einer einzelnen Entität](./media/vs-storage-aspnet-getting-started-tables/delete-entity-results.png)

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu zusätzlichen Optionen für das Speichern von Daten in Azure finden Sie in den anderen Featureleitfäden.

  * [Erste Schritte mit Azure Blob Storage und verbundenen Visual Studio-Diensten (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Erste Schritte mit Azure-Warteschlangenspeicher und verbundenen Visual Studio-Diensten](../storage/vs-storage-aspnet-getting-started-queues.md)
