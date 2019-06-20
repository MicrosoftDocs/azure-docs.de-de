---
title: Tutorial – Arbeiten mit Azure-Speicherwarteschlangen – Azure Storage
description: Ein Tutorial zur Verwendung des Azure-Warteschlangendiensts zum Erstellen von Warteschlangen sowie zum Einfügen, Abrufen und Löschen von Nachrichten.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: cbrooks
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.date: 04/24/2019
ms.openlocfilehash: 8d108e1683be03a79e87990b983f2eda3eadba90
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65797537"
---
# <a name="tutorial-work-with-azure-storage-queues"></a>Tutorial: Arbeiten mit Azure-Speicherwarteschlangen

Azure Queue Storage implementiert cloudbasierte Warteschlangen, um eine Kommunikation zwischen Komponenten einer verteilten Anwendung zu ermöglichen. Jede Warteschlange verwaltet eine Liste von Nachrichten, die von einer Absenderkomponente hinzugefügt und einer Empfängerkomponente verarbeitet werden kann. Bei einer Warteschlange kann Ihre Anwendung entsprechend den Anforderungen sofort skaliert werden. In diesem Artikel werden die grundlegenden Schritte für die Arbeit mit einer Azure Storage-Warteschlange gezeigt.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> - Erstellen eines Azure-Speicherkontos
> - Erstellen der App
> - Hinzufügen von Unterstützung für asynchronen Code
> - Erstellen einer Warteschlange
> - Einfügen von Nachrichten in eine Warteschlange
> - Entfernen von Nachrichten aus Warteschlangen
> - Löschen einer leeren Warteschlange
> - Überprüfen auf Befehlszeilenargumente
> - Erstellen und Ausführen der App

## <a name="prerequisites"></a>Voraussetzungen

- Laden Sie Ihre kostenlose Kopie des plattformübergreifenden [Visual Studio Code](https://code.visualstudio.com/download)-Editors herunter.
- Laden Sie das [.NET Core SDK](https://dotnet.microsoft.com/download) herunter, und installieren Sie es.
- Wenn Sie über kein aktuelles Azure-Abonnement verfügen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/), bevor Sie beginnen.

## <a name="create-an-azure-storage-account"></a>Erstellen eines Azure-Speicherkontos

Erstellen Sie zunächst ein Azure-Speicherkonto. Eine schrittweise Anleitung zum Erstellen eines Speicherkontos finden Sie im Schnellstart [Erstellen eines Speicherkontos](../common/storage-quickstart-create-account.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json).

## <a name="create-the-app"></a>Erstellen der App

Erstellen Sie eine .NET Core-Anwendung mit dem Namen **QueueApp**. Der Einfachheit halber wird diese App Nachrichten über die Warteschlange sowohl senden als auch empfangen.

1. Verwenden Sie in einem Konsolenfenster (z. B. CMD, PowerShell oder Azure CLI) den Befehl `dotnet new` zum Erstellen einer neuen Konsolen-App mit dem Namen **QueueApp**. Dieser Befehl erstellt ein einfaches „Hallo Welt“-C#-Projekt mit einer einzigen Quelldatei: **Program.cs**.

   ```console
   dotnet new console -n QueueApp
   ```

2. Wechseln Sie zum neu erstellten Ordner **QueueApp**, und erstellen Sie die App, um zu überprüfen, ob alles funktioniert.

   ```console
   cd QueueApp
   ```

   ```console
   dotnet build
   ```

   Die Ergebnisse sollten ähnlich wie hier aussehen:

   ```output
   C:\Tutorials>dotnet new console -n QueueApp
   The template "Console Application" was created successfully.

   Processing post-creation actions...
   Running 'dotnet restore' on QueueApp\QueueApp.csproj...
     Restore completed in 155.62 ms for C:\Tutorials\QueueApp\QueueApp.csproj.

   Restore succeeded.

   C:\Tutorials>cd QueueApp

   C:\Tutorials\QueueApp>dotnet build
   Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
   Copyright (C) Microsoft Corporation. All rights reserved.

     Restore completed in 40.87 ms for C:\Tutorials\QueueApp\QueueApp.csproj.
     QueueApp -> C:\Tutorials\QueueApp\bin\Debug\netcoreapp2.1\QueueApp.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:02.40

   C:\Tutorials\QueueApp>_
   ```

## <a name="add-support-for-asynchronous-code"></a>Hinzufügen von Unterstützung für asynchronen Code

Da die App Cloud-Ressourcen verwendet, wird der Code asynchron ausgeführt. Allerdings waren **async** und **await** von C# in **Main**-Methoden bis #C 7.1 keine gültigen Schlüsselwörter. Sie können über ein Flag in der Datei **csproj** problemlos zu diesem Compiler wechseln.

1. Geben Sie in der Befehlszeile im Projektverzeichnis `code .` ein, um Visual Studio Code im aktuellen Verzeichnis zu öffnen. Lassen Sie das Befehlszeilenfenster geöffnet. Später müssen darin noch weitere Befehle ausgeführt werden. Wenn Sie zum Hinzufügen von C#-Ressourcen aufgefordert werden, die zum Erstellen und Debuggen erforderlich sind, klicken Sie auf die Schaltfläche **Ja**.

2. Öffnen Sie die Datei **QueueApp.csproj** im Editor.

3. Fügen in der Builddatei in die erste **PropertyGroup** `<LangVersion>7.1</LangVersion>` ein. Achten Sie darauf, dass Sie nur das Tag **LangVersion** hinzufügen, da Ihr **TargetFramework** je nach der installierten .NET-Version anders sein kann.

   ```xml
   <Project Sdk="Microsoft.NET.Sdk">

     <PropertyGroup>
       <OutputType>Exe</OutputType>
       <TargetFramework>netcoreapp2.1</TargetFramework>
       <LangVersion>7.1</LangVersion>
     </PropertyGroup>

   ...

   ```

4. Speichern Sie die Datei **QueueApp.csproj**.

5. Öffnen Sie die Quelldatei **Program.cs**, und aktualisieren Sie die Methode **Main**, damit sie asynchron ausgeführt wird. Ersetzen Sie **void** durch einen Rückgabewert von **async Task**.

   ```csharp
   static async Task Main(string[] args)
   ```

6. Speichern Sie die Datei **Program.cs**.

## <a name="create-a-queue"></a>Erstellen einer Warteschlange

1. Installieren Sie die Pakete **Microsoft.Azure.Storage.Common** und **Microsoft.Azure.Storage.Queue** mit dem Befehl `dotnet add package` in das Projekt. Führen Sie die folgenden dotnet-Befehle aus dem Projektordner im Konsolenfenster aus.

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   dotnet add package Microsoft.Azure.Storage.Queue
   ```

2. Fügen Sie am Anfang der Datei **Program.cs** die folgenden Namespaces direkt nach der Anweisung `using System;` hinzu. Diese App verwendet Typen aus diesen Namespaces zum Herstellen einer Verbindung mit Azure Storage und zum Arbeiten mit Warteschlangen.

   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;
   ```

3. Speichern Sie die Datei **Program.cs**.

### <a name="get-your-connection-string"></a>Abrufen Ihrer Verbindungszeichenfolge

Die Clientbibliothek verwendet eine Verbindungszeichenfolge zum Herstellen Ihrer Verbindung. Ihre Verbindungszeichenfolge finden Sie im Abschnitt **Einstellungen** Ihres Speicherkontos im Azure-Portal.

1. Melden Sie sich in Ihrem Webbrowser beim [Azure-Portal](https://portal.azure.com/) an.

2. Navigieren Sie zum Speicherkonto im Azure-Portal.

3. Wählen Sie **Zugriffsschlüssel** aus.

4. Klicken Sie rechts neben dem Feld **Verbindungszeichenfolge** auf die Schaltfläche **Kopieren**.

![Verbindungszeichenfolge](media/storage-tutorial-queues/get-connection-string.png)

Die Verbindungszeichenfolge ist in folgendem Format:

   ```
   "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your key>;EndpointSuffix=core.windows.net"
   ```

### <a name="add-the-connection-string-to-the-app"></a>Hinzufügen der Verbindungszeichenfolge zur App

Fügen Sie die Verbindungszeichenfolge in die App ein, damit sie auf das Speicherkonto zugreifen kann.

1. Wechseln Sie zurück zu Visual Studio Code.

2. Fügen Sie in der Klasse **Program** ein Member des Typs `private const string connectionString =` zum Speichern der Verbindungszeichenfolge hinzu.

3. Fügen Sie hinter dem Gleichheitszeichen den Zeichenfolgenwert ein, den Sie zuvor in Ihrem Azure-Portal kopiert haben. Der Wert für **connectionString** ist für Ihr Konto eindeutig.

4. Entfernen Sie den "Hallo Welt"-Code aus **Main**. Ihr Code sollte ähnlich wie der nachstehende aussehen, allerdings Ihren eindeutigen Wert für die Verbindungszeichenfolge enthalten.

   ```csharp
   namespace QueueApp
   {
       class Program
       {
           private const string connectionString = "DefaultEndpointsProtocol=https; ...";

           static async Task Main(string[] args)
           {
           }
       }
   }
   ```

5. Aktualisieren Sie **Main** zum Erstellen eines **CloudQueue**-Objekts, das später an die Methoden zum Senden und Empfangen übergeben wird.

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");
        }
   ```

6. Speichern Sie die Datei .

## <a name="insert-messages-into-the-queue"></a>Einfügen von Nachrichten in die Warteschlange

Erstellen Sie eine neue Methode zum Senden einer Nachricht in die Warteschlange. Fügen Sie Ihrer **Program**-Klasse die folgende Methode hinzu. Diese Methode ruft einen Warteschlangenverweis ab und erstellt dann durch Aufrufen von [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync) eine neue Warteschlange, sofern es noch keine gibt. Anschließend fügt sie die Nachricht durch Aufrufen von [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync) zur Warteschlange hinzu.

1. Fügen Sie Ihrer **Program**-Klasse die folgende **SendMessageAsync**-Methode hinzu.

   ```csharp
   static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
   {
       bool createdQueue = await theQueue.CreateIfNotExistsAsync();

       if (createdQueue)
       {
           Console.WriteLine("The queue was created.");
       }

       CloudQueueMessage message = new CloudQueueMessage(newMessage);
       await theQueue.AddMessageAsync(message);
   }
   ```

2. Speichern Sie die Datei .

## <a name="dequeue-messages"></a>Entfernen von Nachrichten aus Warteschlangen

Erstellen Sie eine neue Methode namens **ReceiveMessageAsync**. Diese Methode empfängt durch Aufrufen von [GetMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync) eine Nachricht aus der Warteschlange. Sobald die Nachricht erfolgreich empfangen wurde, muss sie aus der Warteschlange gelöscht werden, damit sie nicht mehrmals verarbeitet wird. Löschen Sie die empfangene Nachricht daher durch Aufrufen von [DeleteMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync) aus der Warteschlange.

1. Fügen Sie Ihrer **Program**-Klasse die folgende **ReceiveMessageAsync**-Methode hinzu.

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
       }
   }
   ```

2. Speichern Sie die Datei .

## <a name="delete-an-empty-queue"></a>Löschen einer leeren Warteschlange

Es empfiehlt sich, am Ende eines Projekts zu ermitteln, ob Sie die erstellten Ressourcen noch benötigen. Ressourcen, die weiterhin ausgeführt werden, können Sie Geld kosten. Wenn die Warteschlange noch vorhanden, aber leer ist, fragen Sie den Benutzer, ob sie gelöscht werden soll.

1. Erweitern Sie die **ReceiveMessageAsync** Methode, um eine Aufforderung zum Löschen der leeren Warteschlange einzufügen.

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
           else
           {
               Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
               string response = Console.ReadLine();

               if (response == "Y" || response == "y")
               {
                   await theQueue.DeleteIfExistsAsync();
                   return "The queue was deleted.";
               }
               else
               {
                   return "The queue was not deleted.";
               }
           }
       }
       else
       {
           return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
       }
   }
   ```

2. Speichern Sie die Datei .

## <a name="check-for-command-line-arguments"></a>Überprüfen auf Befehlszeilenargumente

Wenn es irgendwelche Befehlszeilenargumente gibt, die an die App übergeben werden, stellen Sie sich vor, dass es sich dabei um eine Nachricht handelt, die der Warteschlange hinzugefügt werden muss. Verknüpfen Sie die Argumente zu einer Zeichenfolge. Fügen Sie diese Zeichenfolge durch Aufrufen der zuvor hinzugefügten **SendMessageAsync**-Methode der Nachrichtenwarteschlange hinzu.

Wenn es keine Befehlszeilenargumente gibt, führen Sie einen Abrufvorgang aus. Rufen Sie die **ReceiveMessageAsync**-Methode auf, um die erste Nachricht in der Warteschlange abzurufen.

Warten Sie schließlich auf die Benutzereingabe, bevor Sie den Vorgang durch Aufrufen von **Console.ReadLine** beenden.

1. Erweitern Sie die Methode **Main**, um auf Befehlszeilenargumente zu überprüfen und auf die Benutzereingabe zu warten.

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received: {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }
   ```

2. Speichern Sie die Datei .

## <a name="complete-code"></a>Vollständiger Code

Hier ist die vollständige Codeauflistung für dieses Projekt.

   ```csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;

   namespace QueueApp
   {
    class Program
    {
        // The string value is broken up for better onscreen formatting
        private const string connectionString = "DefaultEndpointsProtocol=https;" +
                                                "AccountName=<your storage account name>;" +
                                                "AccountKey=<your key>;" +
                                                "EndpointSuffix=core.windows.net";

        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }

        static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
        {
            bool createdQueue = await theQueue.CreateIfNotExistsAsync();

            if (createdQueue)
            {
                Console.WriteLine("The queue was created.");
            }

            CloudQueueMessage message = new CloudQueueMessage(newMessage);
            await theQueue.AddMessageAsync(message);
        }

        static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
        {
            bool exists = await theQueue.ExistsAsync();

            if (exists)
            {
                CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

                if (retrievedMessage != null)
                {
                    string theMessage = retrievedMessage.AsString;
                    await theQueue.DeleteMessageAsync(retrievedMessage);
                    return theMessage;
                }
                else
                {
                    Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
                    string response = Console.ReadLine();

                    if (response == "Y" || response == "y")
                    {
                        await theQueue.DeleteIfExistsAsync();
                        return "The queue was deleted.";
                    }
                    else
                    {
                        return "The queue was not deleted.";
                    }
                }
            }
            else
            {
                return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
            }
        }
    }
   }
   ```

## <a name="build-and-run-the-app"></a>Erstellen und Ausführen der App

1. Führen Sie über die Befehlszeile im Projektverzeichnis den folgenden dotnet-Befehl zum Erstellen des Projekts aus.

   ```console
   dotnet build
   ```

2. Nachdem das Projekt erfolgreich erstellt wurde, führen Sie den folgenden Befehl aus, um der Warteschlange die erste Nachricht hinzuzufügen.

   ```console
   dotnet run First queue message
   ```

Die folgende Ausgabe sollte angezeigt werden:

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter..._
   ```

3. Führen Sie die App ohne Befehlszeilenargumente aus, um die erste Nachricht in der Warteschlange zu empfangen und zu entfernen.

   ```console
   dotnet run
   ```

4. Setzen Sie die Ausführung der App fort, bis alle Nachrichten entfernt wurden. Wenn Sie sie dann noch einmal ausführen, wird eine Meldung, dass die Warteschlange leer ist, und eine Aufforderung zum Löschen der Warteschlange angezeigt.

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Second queue message
   Sent: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Third queue message
   Sent: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   The queue is empty. Attempt to delete it? (Y/N) Y
   Received: The queue was deleted.
   Press Enter...

   C:\Tutorials\QueueApp>_
   ```

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

1. Erstellen einer Warteschlange
2. Hinzufügen und Entfernen von Nachrichten zu bzw. aus einer Warteschlange
3. Löschen einer Azure Storage-Warteschlange

Im Schnellstart zu Azure-Warteschlangen finden Sie weitere Informationen.

> [!div class="nextstepaction"]
> [Schnellstart zu Warteschlangen](storage-quickstart-queues-portal.md)
