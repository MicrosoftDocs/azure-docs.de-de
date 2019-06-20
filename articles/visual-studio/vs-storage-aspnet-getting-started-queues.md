---
title: Erste Schritte mit Queue Storage und verbundenen Visual Studio-Diensten (ASP.NET) | Microsoft-Dokumentation
description: Erfahren Sie mehr über die ersten Schritte mit Azure Queue Storage in einem ASP.NET 5-Projekt in Visual Studio, nachdem Sie mithilfe von verbundenen Visual Studio-Diensten eine Verbindung mit einem Speicherkonto hergestellt haben.
services: storage
author: ghogen
manager: douge
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/23/2016
ms.author: ghogen
ms.openlocfilehash: 3f3a649308449a8ba4e1dbaebd4db4afbf731c5b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62110484"
---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Erste Schritte mit Azure Queue Storage und verbundenen Visual Studio-Diensten (ASP.NET)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Übersicht

Azure Queue Storage ermöglicht Cloudmessaging zwischen Anwendungskomponenten. Bei der Entwicklung skalierbarer Anwendungen werden häufig einzelne Anwendungskomponenten entkoppelt, um eine unabhängige Skalierung zu ermöglichen. Queue Storage bietet asynchrones Messaging für die Kommunikation zwischen Anwendungskomponenten, egal ob diese in der Cloud, auf dem Desktop, auf einem lokalen Server oder einem mobilen Gerät ausgeführt werden. Queue Storage unterstützt auch die Verwaltung asynchroner Aufgaben und den Aufbau von Prozessworkflows.

In diesem Tutorial wird gezeigt, wie Sie ASP.NET-Code für einige häufig verwendete Szenarien mit Azure Queue Storage-Entitäten schreiben. Zu diesen Szenarien gehören allgemeine Aufgaben wie das Erstellen einer Azure-Warteschlange und das Hinzufügen, Ändern, Lesen und Entfernen von Nachrichten in Warteschlangen.

## <a name="prerequisites"></a>Voraussetzungen

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure-Speicherkonto](../storage/common/storage-quickstart-create-account.md)

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Erstellen eines MVC-Controllers 

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Controller**, und wählen Sie im Kontextmenü **Hinzufügen > Controller** aus.

    ![Hinzufügen eines Controllers zu einer ASP.NET-MVC-App](./media/vs-storage-aspnet-getting-started-queues/add-controller-menu.png)

1. Wählen Sie im Dialogfeld **Gerüst hinzufügen** die Option **MVC 5 Controller - Leer** und dann **Hinzufügen**.

    ![Angeben des MVC-Controllertyps](./media/vs-storage-aspnet-getting-started-queues/add-controller.png)

1. Benennen Sie den Controller im Dialogfeld **Controller hinzufügen** mit *QueuesController*, und wählen Sie **Hinzufügen**.

    ![Benennen des MVC-Controllers](./media/vs-storage-aspnet-getting-started-queues/add-controller-name.png)

1. Fügen Sie die folgenden *using*-Anweisungen der `QueuesController.cs`-Datei hinzu:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
   ## <a name="create-a-queue"></a>Erstellen einer Warteschlange

Die folgenden Schritte veranschaulichen, wie Sie eine Warteschlange erstellen:

> [!NOTE]
> 
> In diesem Abschnitt wird vorausgesetzt, dass Sie die Schritte zum [Einrichten der Entwicklungsumgebung](#set-up-the-development-environment) abgeschlossen haben. 

1. Öffnen Sie die Datei `QueuesController.cs` . 

1. Fügen Sie eine Methode namens **CreateQueue** hinzu, die ein **ActionResult** zurückgibt.

    ```csharp
    public ActionResult CreateQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. Rufen Sie in der **CreateQueue**-Methode ein **CloudStorageAccount**-Objekt ab, das die Informationen zu Ihrem Speicherkonto enthält. Verwenden Sie den folgenden Code, um die Speicherverbindungszeichenfolge und Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen: (Ändern Sie *&lt;storage-account-name>* in den Namen des Azure-Speicherkontos, auf das Sie zugreifen.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Rufen Sie ein **CloudQueueClient**-Objekt ab, das einen Warteschlangendienstclient darstellt.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Rufen Sie ein **CloudQueue**-Objekt ab, das einen Verweis auf den gewünschten Warteschlangennamen darstellt. Die **CloudQueueClient.GetQueueReference**-Methode stellt keine Anforderung an den Warteschlangenspeicher. Der Verweis wird zurückgegeben, und zwar unabhängig davon, ob die Warteschlange vorhanden ist oder nicht. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Rufen Sie die **CloudQueue.CreateIfNotExists**-Methode auf, um die Warteschlange zu erstellen, falls noch nicht vorhanden. Die **CloudQueue.CreateIfNotExists**-Methode gibt **true** zurück, wenn die Warteschlange nicht vorhanden ist und erfolgreich erstellt wurde. Andernfalls wird **false** zurückgegeben.    

    ```csharp
    ViewBag.Success = queue.CreateIfNotExists();
    ```

1. Aktualisieren Sie **ViewBag** mit dem Namen der Warteschlange.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Ansichten**, klicken Sie mit der rechten Maustaste auf **Warteschlangen**, und wählen Sie im Kontextmenü **Hinzufügen > Ansicht** aus.

1. Geben Sie im Dialogfeld **Ansicht hinzufügen** für den Ansichtsnamen **CreateQueue** ein, und wählen Sie **Hinzufügen**.

1. Öffnen Sie `CreateQueue.cshtml`, und ändern Sie die Datei so, dass sie folgendem Codeausschnitt entspricht:

    ```csharp
    @{
        ViewBag.Title = "Create Queue";
    }
    
    <h2>Create Queue results</h2>

    Creation of @ViewBag.QueueName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Views > Shared**, und öffnen Sie `_Layout.cshtml`.

1. Fügen Sie nach dem letzten **Html.ActionLink** den folgenden **Html.ActionLink** hinzu:

    ```html
    <li>@Html.ActionLink("Create queue", "CreateQueue", "Queues")</li>
    ```

1. Führen Sie die Anwendung aus, und wählen Sie **Warteschlange erstellen** aus, um ähnliche Ergebnisse wie im folgenden Screenshot zu sehen:
  
    ![Erstellen einer Warteschlange](./media/vs-storage-aspnet-getting-started-queues/create-queue-results.png)

    Wie bereits erwähnt, gibt die **CloudQueue.CreateIfNotExists**-Methode nur dann **true** zurück, wenn die Warteschlange nicht vorhanden ist und erstellt wird. Aus diesem Grund gibt die Methode **false** zurück, wenn Sie die App ausführen, wenn die Warteschlange vorhanden ist. Um die App mehrere Male auszuführen, müssen Sie die Warteschlange vor dem erneuten Ausführen der App löschen. Das Löschen der Warteschlange kann mit der **CloudQueue.Delete**-Methode durchgeführt werden. Sie können die Warteschlange auch über das [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) oder den [Microsoft Azure Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) löschen.  

## <a name="add-a-message-to-a-queue"></a>Hinzufügen von Nachrichten zu einer Warteschlange

Sobald Sie [eine Warteschlange erstellt](#create-a-queue) haben, können Sie dieser Warteschlange Nachrichten hinzufügen. Dieser Abschnitt führt Sie durch das Hinzufügen einer Nachricht zur Warteschlange *test-queue*. 

> [!NOTE]
> 
> In diesem Abschnitt wird vorausgesetzt, dass Sie die Schritte zum [Einrichten der Entwicklungsumgebung](#set-up-the-development-environment) abgeschlossen haben. 

1. Öffnen Sie die Datei `QueuesController.cs` .

1. Fügen Sie eine Methode namens **AddMessage** hinzu, die ein **ActionResult** zurückgibt.

    ```csharp
    public ActionResult AddMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Rufen Sie in der **AddMessage**-Methode ein **CloudStorageAccount**-Objekt ab, das die Informationen zu Ihrem Speicherkonto enthält. Verwenden Sie den folgenden Code, um die Speicherverbindungszeichenfolge und Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen: (Ändern Sie *&lt;storage-account-name>* in den Namen des Azure-Speicherkontos, auf das Sie zugreifen.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Rufen Sie ein **CloudQueueClient**-Objekt ab, das einen Warteschlangendienstclient darstellt.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Rufen Sie ein **CloudQueueContainer**-Objekt ab, das einen Verweis auf die Warteschlange darstellt. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Erstellen Sie das **CloudQueueMessage**-Objekt, das die Nachricht darstellt, die Sie der Warteschlange hinzufügen möchten. Ein **CloudQueueMessage** -Objekt kann aus einer Zeichenfolge (im UTF-8-Format) oder aus einem Bytearray erstellt werden.

    ```csharp
    CloudQueueMessage message = new CloudQueueMessage("Hello, Azure Queue Storage");
    ```

1. Rufen Sie die **CloudQueue.AddMessage**-Methode auf, um die Nachricht der Warteschlange hinzuzufügen.

    ```csharp
    queue.AddMessage(message);
    ```

1. Erstellen Sie eine Reihe von **ViewBag**-Eigenschaften und legen Sie sie zur Anzeige in der Ansicht fest.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Ansichten**, klicken Sie mit der rechten Maustaste auf **Warteschlangen**, und wählen Sie im Kontextmenü **Hinzufügen > Ansicht** aus.

1. Geben Sie im Dialogfeld **Ansicht hinzufügen** für den Ansichtsnamen **AddMessage** ein, und wählen Sie **Hinzufügen**.

1. Öffnen Sie `AddMessage.cshtml`, und ändern Sie die Datei so, dass sie folgendem Codeausschnitt entspricht:

    ```csharp
    @{
        ViewBag.Title = "Add Message";
    }
    
    <h2>Add Message results</h2>
    
    The message '@ViewBag.Message' was added to the queue '@ViewBag.QueueName'.
    ```

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Views > Shared**, und öffnen Sie `_Layout.cshtml`.

1. Fügen Sie nach dem letzten **Html.ActionLink** den folgenden **Html.ActionLink** hinzu:

    ```html
    <li>@Html.ActionLink("Add message", "AddMessage", "Queues")</li>
    ```

1. Führen Sie die Anwendung aus, und wählen Sie **Nachricht hinzufügen** aus, um ähnliche Ergebnisse wie im folgenden Screenshot zu sehen:
  
    ![Hinzufügen einer Nachricht](./media/vs-storage-aspnet-getting-started-queues/add-message-results.png)

Die beiden Abschnitte [Lesen einer Nachricht in einer Warteschlange, ohne sie zu entfernen](#read-a-message-from-a-queue-without-removing-it) und [Lesen und Entfernen von Nachrichten aus einer Warteschlange](#read-and-remove-a-message-from-a-queue) veranschaulichen, wie Nachrichten aus einer Warteschlange gelesen werden.    

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Lesen einer Nachricht in einer Warteschlange, ohne sie zu entfernen

Dieser Abschnitt zeigt, wie Sie eine Nachricht in der Warteschlange in der Vorschau anzeigen (die erste Nachricht lesen, ohne sie zu entfernen).  

> [!NOTE]
> 
> In diesem Abschnitt wird vorausgesetzt, dass Sie die Schritte zum [Einrichten der Entwicklungsumgebung](#set-up-the-development-environment) abgeschlossen haben. 

1. Öffnen Sie die Datei `QueuesController.cs` .

1. Fügen Sie eine Methode namens **PeekMessage** hinzu, die ein **ActionResult** zurückgibt.

    ```csharp
    public ActionResult PeekMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Rufen Sie in der **PeekMessage**-Methode ein **CloudStorageAccount**-Objekt ab, das die Informationen zu Ihrem Speicherkonto enthält. Verwenden Sie den folgenden Code, um die Speicherverbindungszeichenfolge und Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen: (Ändern Sie *&lt;storage-account-name>* in den Namen des Azure-Speicherkontos, auf das Sie zugreifen.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Rufen Sie ein **CloudQueueClient**-Objekt ab, das einen Warteschlangendienstclient darstellt.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Rufen Sie ein **CloudQueueContainer**-Objekt ab, das einen Verweis auf die Warteschlange darstellt. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Rufen Sie die **CloudQueue.PeekMessage**-Methode auf, um die erste Nachricht in der Warteschlange zu lesen, ohne sie daraus zu entfernen. 

    ```csharp
    CloudQueueMessage message = queue.PeekMessage();
    ```

1. Aktualisieren Sie **ViewBag** mit zwei Werten: dem Namen der Warteschlange und der Nachricht, die gelesen wurde. Das **CloudQueueMessage**-Objekt macht zwei Eigenschaften für den Abruf des Objektwerts verfügbar: **CloudQueueMessage.AsBytes** und **CloudQueueMessage.AsString**. **AsString** (in diesem Beispiel verwendet) gibt eine Zeichenfolge zurück, während **AsBytes** ein Bytearray zurückgibt.

    ```csharp
    ViewBag.QueueName = queue.Name; 
    ViewBag.Message = (message != null ? message.AsString : "");
    ```

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Ansichten**, klicken Sie mit der rechten Maustaste auf **Warteschlangen**, und wählen Sie im Kontextmenü **Hinzufügen > Ansicht** aus.

1. Geben Sie im Dialogfeld **Ansicht hinzufügen** für den Ansichtsnamen **PeekMessage** ein, und wählen Sie **Hinzufügen**.

1. Öffnen Sie `PeekMessage.cshtml`, und ändern Sie die Datei so, dass sie folgendem Codeausschnitt entspricht:

    ```csharp
    @{
        ViewBag.Title = "PeekMessage";
    }
    
    <h2>Peek Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Peeked Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>    
    ```

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Views > Shared**, und öffnen Sie `_Layout.cshtml`.

1. Fügen Sie nach dem letzten **Html.ActionLink** den folgenden **Html.ActionLink** hinzu:

    ```html
    <li>@Html.ActionLink("Peek message", "PeekMessage", "Queues")</li>
    ```

1. Führen Sie die Anwendung aus, und wählen Sie **Nachricht einsehen** aus, um ähnliche Ergebnisse wie im folgenden Screenshot zu sehen:
  
    ![Einsehen einer Nachricht](./media/vs-storage-aspnet-getting-started-queues/peek-message-results.png)

## <a name="read-and-remove-a-message-from-a-queue"></a>Lesen und Entfernen von Nachrichten aus einer Warteschlange

In diesem Abschnitt erfahren Sie, wie Sie eine Nachricht lesen und aus einer Warteschlange entfernen.   

> [!NOTE]
> 
> In diesem Abschnitt wird vorausgesetzt, dass Sie die Schritte zum [Einrichten der Entwicklungsumgebung](#set-up-the-development-environment) abgeschlossen haben. 

1. Öffnen Sie die Datei `QueuesController.cs` .

1. Fügen Sie eine Methode namens **ReadMessage** hinzu, die ein **ActionResult** zurückgibt.

    ```csharp
    public ActionResult ReadMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Rufen Sie in der **ReadMessage**-Methode ein **CloudStorageAccount**-Objekt ab, das die Informationen zu Ihrem Speicherkonto enthält. Verwenden Sie den folgenden Code, um die Speicherverbindungszeichenfolge und Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen: (Ändern Sie *&lt;storage-account-name>* in den Namen des Azure-Speicherkontos, auf das Sie zugreifen.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Rufen Sie ein **CloudQueueClient**-Objekt ab, das einen Warteschlangendienstclient darstellt.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Rufen Sie ein **CloudQueueContainer**-Objekt ab, das einen Verweis auf die Warteschlange darstellt. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Rufen Sie die **CloudQueue.GetMessage**-Methode auf, um die erste Nachricht in der Warteschlange zu lesen. Die **CloudQueue.GetMessage**-Methode macht die Nachricht (standardmäßig) für anderen Code zum Lesen von Nachrichten für 30 Sekunden unsichtbar, sodass kein anderer Code die Nachricht ändern oder lesen kann, während Sie sie verarbeiten. Um die Zeitspanne zu ändern, in der die Nachricht nicht sichtbar ist, ändern Sie den **VisibilityTimeout**-Parameter, der an die **CloudQueue.GetMessage**-Methode übergeben wurde.

    ```csharp
    // This message will be invisible to other code for 30 seconds.
    CloudQueueMessage message = queue.GetMessage();     
    ```

1. Rufen Sie die **CloudQueueMessage.Delete**-Methode auf, um die Nachricht aus der Warteschlange zu löschen.

    ```csharp
    queue.DeleteMessage(message);
    ```

1. Aktualisieren Sie **ViewBag** mit der gelöschten Nachricht und dem Namen der Warteschlange.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```
 
1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Ansichten**, klicken Sie mit der rechten Maustaste auf **Warteschlangen**, und wählen Sie im Kontextmenü **Hinzufügen > Ansicht** aus.

1. Geben Sie im Dialogfeld **Ansicht hinzufügen** für den Ansichtsnamen **ReadMessage** ein, und wählen Sie **Hinzufügen**.

1. Öffnen Sie `ReadMessage.cshtml`, und ändern Sie die Datei so, dass sie folgendem Codeausschnitt entspricht:

    ```csharp
    @{
        ViewBag.Title = "ReadMessage";
    }
    
    <h2>Read Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Read (and Deleted) Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>
    ```

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Views > Shared**, und öffnen Sie `_Layout.cshtml`.

1. Fügen Sie nach dem letzten **Html.ActionLink** den folgenden **Html.ActionLink** hinzu:

    ```html
    <li>@Html.ActionLink("Read/Delete message", "ReadMessage", "Queues")</li>
    ```

1. Führen Sie die Anwendung aus, und wählen Sie **Nachricht lesen/löschen** aus, um ähnliche Ergebnisse wie im folgenden Screenshot zu sehen:
  
    ![Lesen und Löschen der Nachricht](./media/vs-storage-aspnet-getting-started-queues/read-message-results.png)

## <a name="get-the-queue-length"></a>Abrufen der Warteschlangenlänge

Dieser Abschnitt zeigt, wie die Warteschlangenlänge (Anzahl der Nachrichten) abgerufen wird. 

> [!NOTE]
> 
> In diesem Abschnitt wird vorausgesetzt, dass Sie die Schritte zum [Einrichten der Entwicklungsumgebung](#set-up-the-development-environment) abgeschlossen haben. 

1. Öffnen Sie die Datei `QueuesController.cs` .

1. Fügen Sie eine Methode namens **GetQueueLength** hinzu, die ein **ActionResult** zurückgibt.

    ```csharp
    public ActionResult GetQueueLength()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Rufen Sie in der **ReadMessage**-Methode ein **CloudStorageAccount**-Objekt ab, das die Informationen zu Ihrem Speicherkonto enthält. Verwenden Sie den folgenden Code, um die Speicherverbindungszeichenfolge und Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen: (Ändern Sie *&lt;storage-account-name>* in den Namen des Azure-Speicherkontos, auf das Sie zugreifen.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Rufen Sie ein **CloudQueueClient**-Objekt ab, das einen Warteschlangendienstclient darstellt.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Rufen Sie ein **CloudQueueContainer**-Objekt ab, das einen Verweis auf die Warteschlange darstellt. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Rufen Sie die **CloudQueue.FetchAttributes**-Methode auf, um die Attribute der Warteschlange (einschließlich Länge) abzurufen. 

    ```csharp
    queue.FetchAttributes();
    ```

1. Greifen Sie auf die **CloudQueue.ApproximateMessageCount**-Eigenschaft zu, um die Länge der Warteschlange abzurufen.
 
    ```csharp
    int? nMessages = queue.ApproximateMessageCount;
    ```

1. Aktualisieren Sie **ViewBag** mit dem Namen der Warteschlange und ihrer Länge.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Length = nMessages;
    ```
 
1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Ansichten**, klicken Sie mit der rechten Maustaste auf **Warteschlangen**, und wählen Sie im Kontextmenü **Hinzufügen > Ansicht** aus.

1. Geben Sie im Dialogfeld **Ansicht hinzufügen** für den Ansichtsnamen **GetQueueLength** ein, und wählen Sie **Hinzufügen**.

1. Öffnen Sie `GetQueueLengthMessage.cshtml`, und ändern Sie die Datei so, dass sie folgendem Codeausschnitt entspricht:

    ```csharp
    @{
        ViewBag.Title = "GetQueueLength";
    }
    
    <h2>Get Queue Length results</h2>
    
    The queue '@ViewBag.QueueName' has a length of (number of messages): @ViewBag.Length
    ```

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Views > Shared**, und öffnen Sie `_Layout.cshtml`.

1. Fügen Sie nach dem letzten **Html.ActionLink** den folgenden **Html.ActionLink** hinzu:

    ```html
    <li>@Html.ActionLink("Get queue length", "GetQueueLength", "Queues")</li>
    ```

1. Führen Sie die Anwendung aus, und wählen Sie **Warteschlangenlänge abrufen** aus, um ähnliche Ergebnisse wie im folgenden Screenshot zu sehen:
  
    ![Abrufen der Warteschlangenlänge](./media/vs-storage-aspnet-getting-started-queues/get-queue-length-results.png)


## <a name="delete-a-queue"></a>Löschen einer Warteschlange
Dieser Abschnitt zeigt das Löschen einer Warteschlange. 

> [!NOTE]
> 
> In diesem Abschnitt wird vorausgesetzt, dass Sie die Schritte zum [Einrichten der Entwicklungsumgebung](#set-up-the-development-environment) abgeschlossen haben. 

1. Öffnen Sie die Datei `QueuesController.cs` .

1. Fügen Sie eine Methode namens **DeleteQueue** hinzu, die ein **ActionResult** zurückgibt.

    ```csharp
    public ActionResult DeleteQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. Rufen Sie in der **DeleteQueue**-Methode ein **CloudStorageAccount**-Objekt ab, das die Informationen zu Ihrem Speicherkonto enthält. Verwenden Sie den folgenden Code, um die Speicherverbindungszeichenfolge und Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen: (Ändern Sie *&lt;storage-account-name>* in den Namen des Azure-Speicherkontos, auf das Sie zugreifen.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Rufen Sie ein **CloudQueueClient**-Objekt ab, das einen Warteschlangendienstclient darstellt.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Rufen Sie ein **CloudQueueContainer**-Objekt ab, das einen Verweis auf die Warteschlange darstellt. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Rufen Sie die **CloudQueue.Delete**-Methode zum Löschen der Warteschlange auf, die vom **CloudQueue**-Objekt dargestellt wird.

    ```csharp
    queue.Delete();
    ```

1. Aktualisieren Sie **ViewBag** mit dem Namen der Warteschlange und ihrer Länge.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```
 
1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Ansichten**, klicken Sie mit der rechten Maustaste auf **Warteschlangen**, und wählen Sie im Kontextmenü **Hinzufügen > Ansicht** aus.

1. Geben Sie im Dialogfeld **Ansicht hinzufügen** für den Ansichtsnamen **DeleteQueue** ein, und wählen Sie **Hinzufügen**.

1. Öffnen Sie `DeleteQueue.cshtml`, und ändern Sie die Datei so, dass sie folgendem Codeausschnitt entspricht:

    ```csharp
    @{
        ViewBag.Title = "DeleteQueue";
    }
    
    <h2>Delete Queue results</h2>
    
    @ViewBag.QueueName deleted.
    ```

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner **Views > Shared**, und öffnen Sie `_Layout.cshtml`.

1. Fügen Sie nach dem letzten **Html.ActionLink** den folgenden **Html.ActionLink** hinzu:

    ```html
    <li>@Html.ActionLink("Delete queue", "DeleteQueue", "Queues")</li>
    ```

1. Führen Sie die Anwendung aus, und wählen Sie **Warteschlangenlänge abrufen** aus, um ähnliche Ergebnisse wie im folgenden Screenshot zu sehen:
  
    ![Löschen der Warteschlange](./media/vs-storage-aspnet-getting-started-queues/delete-queue-results.png)

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu zusätzlichen Optionen für das Speichern von Daten in Azure finden Sie in den anderen Featureleitfäden.

  * [Erste Schritte mit Azure Blob Storage und verbundenen Visual Studio-Diensten (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Erste Schritte mit Tabellenspeicher und verbundenen Visual Studio-Diensten (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
