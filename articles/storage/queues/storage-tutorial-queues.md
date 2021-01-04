---
title: Tutorial – Arbeiten mit Azure Storage-Warteschlangen in .NET
description: Ein Tutorial zur Verwendung des Azure-Warteschlangendiensts zum Erstellen von Warteschlangen sowie zum Einfügen, Abrufen und Löschen von Nachrichten mithilfe von .NET-Code.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 06/09/2020
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.reviewer: dineshm
ms.custom: devx-track-csharp
ms.openlocfilehash: 9d661800c53cc0795efde1f411675d17661fb968
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93345532"
---
# <a name="tutorial-work-with-azure-storage-queues-in-net"></a>Tutorial: Arbeiten mit Azure Storage-Warteschlangen in .NET

Azure Queue Storage implementiert cloudbasierte Warteschlangen, um eine Kommunikation zwischen Komponenten einer verteilten Anwendung zu ermöglichen. Jede Warteschlange verwaltet eine Liste von Nachrichten, die von einer Absenderkomponente hinzugefügt und einer Empfängerkomponente verarbeitet werden kann. Bei einer Warteschlange kann Ihre Anwendung entsprechend den Anforderungen sofort skaliert werden. In diesem Artikel werden die grundlegenden Schritte für die Arbeit mit einer Azure Storage-Warteschlange gezeigt.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> - Erstellen eines Azure-Speicherkontos
> - Erstellen der App
> - Hinzufügen der Azure-Clientbibliotheken
> - Hinzufügen von Unterstützung für asynchronen Code
> - Erstellen einer Warteschlange
> - Einfügen von Nachrichten in eine Warteschlange
> - Entfernen von Nachrichten aus Warteschlangen
> - Löschen einer leeren Warteschlange
> - Überprüfen auf Befehlszeilenargumente
> - Erstellen und Ausführen der App

## <a name="prerequisites"></a>Voraussetzungen

- Laden Sie Ihre kostenlose Kopie des plattformübergreifenden [Visual Studio Code](https://code.visualstudio.com/download)-Editors herunter.
- Laden Sie die [.NET Core SDK](https://dotnet.microsoft.com/download)-Version 3.1 oder höher herunter, und installieren Sie sie.
- Wenn Sie über kein aktuelles Azure-Abonnement verfügen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/free/), bevor Sie beginnen.

## <a name="create-an-azure-storage-account"></a>Erstellen eines Azure-Speicherkontos

Erstellen Sie zunächst ein Azure-Speicherkonto. Eine schrittweise Anleitung zum Erstellen eines Speicherkontos finden Sie im Schnellstart [Erstellen eines Speicherkontos](../common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json). Dies ist ein separater Schritt, den Sie nach dem Erstellen eines kostenlosen Azure-Kontos in den Voraussetzungen ausführen.

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

   Die Ergebnisse sollten in etwa wie in der folgenden Ausgabe aussehen:

   ```output
   C:\Tutorials>dotnet new console -n QueueApp
   The template "Console Application" was created successfully.

   Processing post-creation actions...
   Running 'dotnet restore' on QueueApp\QueueApp.csproj...
     Restore completed in 155.63 ms for C:\Tutorials\QueueApp\QueueApp.csproj.

   Restore succeeded.

   C:\Tutorials>cd QueueApp

   C:\Tutorials\QueueApp>dotnet build
   Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
   Copyright (C) Microsoft Corporation. All rights reserved.

     Restore completed in 40.87 ms for C:\Tutorials\QueueApp\QueueApp.csproj.
     QueueApp -> C:\Tutorials\QueueApp\bin\Debug\netcoreapp3.1\QueueApp.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:02.40

   C:\Tutorials\QueueApp>_
   ```

<!-- markdownlint-disable MD023 -->

## <a name="add-the-azure-client-libraries"></a>Hinzufügen der Azure-Clientbibliotheken

1. Fügen Sie dem Projekt mit dem Befehl `dotnet add package` die Azure Storage Client-Bibliotheken hinzu.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   Führen Sie den folgenden Befehl aus dem Projektordner im Konsolenfenster aus.

   ```console
   dotnet add package Azure.Storage.Queues
   ```

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   Führen Sie die folgenden Befehle aus dem Projektordner im Konsolenfenster aus.

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   ```

   ```console
   dotnet add package Microsoft.Azure.Storage.Queue
   ```
   ---

### <a name="add-using-statements"></a>Hinzufügen mit Anweisungen

1. Geben Sie in der Befehlszeile im Projektverzeichnis `code .` ein, um Visual Studio Code im aktuellen Verzeichnis zu öffnen. Lassen Sie das Befehlszeilenfenster geöffnet. Später müssen darin noch weitere Befehle ausgeführt werden. Wenn Sie zum Hinzufügen von C#-Ressourcen aufgefordert werden, die zum Erstellen und Debuggen erforderlich sind, klicken Sie auf die Schaltfläche **Ja**.

1. Öffnen Sie die Quelldatei **Program.cs**, und fügen Sie die folgenden Namespaces direkt nach der Anweisung `using System;` hinzu. Diese App verwendet Typen aus diesen Namespaces zum Herstellen einer Verbindung mit Azure Storage und zum Arbeiten mit Warteschlangen.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_UsingStatements":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_UsingStatements":::

1. Speichern Sie die Datei **Program.cs**.

## <a name="add-support-for-asynchronous-code"></a>Hinzufügen von Unterstützung für asynchronen Code

Da die App Cloud-Ressourcen verwendet, wird der Code asynchron ausgeführt.

1. Aktualisieren Sie die Methode **Main**, damit sie asynchron ausgeführt wird. Ersetzen Sie **void** durch einen Rückgabewert von **async Task**.

   ```csharp
   static async Task Main(string[] args)
   ```

1. Speichern Sie die Datei **Program.cs**.

## <a name="create-a-queue"></a>Erstellen einer Warteschlange

Bevor Sie Aufrufe in Azure-APIs ausführen, müssen Sie Ihre Anmeldeinformationen aus dem Azure-Portal abrufen.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

### <a name="add-the-connection-string-to-the-app"></a>Hinzufügen der Verbindungszeichenfolge zur App

Fügen Sie die Verbindungszeichenfolge in die App ein, damit sie auf das Speicherkonto zugreifen kann.

1. Wechseln Sie zurück zu Visual Studio Code.

1. Ersetzen Sie in der Methode **Main** den Code `Console.WriteLine("Hello World!");` durch die folgende Zeile, die die Verbindungszeichenfolge aus der Umgebungsvariablen abruft.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_DeclareConnectionString":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_DeclareConnectionString":::

1. Fügen Sie **Main** den folgenden Code zum Erstellen eines Warteschlangenobjekts hinzu, das später an die Methoden zum Senden und Empfangen übergeben wird.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_CreateQueueClient":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_CreateQueueClient":::

1. Speichern Sie die Datei .

## <a name="insert-messages-into-the-queue"></a>Einfügen von Nachrichten in die Warteschlange

Erstellen Sie eine neue Methode zum Senden einer Nachricht in die Warteschlange.

1. Fügen Sie Ihrer **Program**-Klasse die folgende Methode **InsertMessageAsync** hinzu.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   Dieser Methode wird ein Warteschlangenverweis übergeben. Wenn sie nicht bereits vorhanden ist, wird durch Aufrufen von [CreateIfNotExistsAsync](/dotnet/api/azure.storage.queues.queueclient.createifnotexistsasync) eine neue Warteschlange erstellt. Anschließend fügt sie die *newMessage* durch Aufrufen von [SendMessageAsync](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync) zur Warteschlange hinzu.

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_InsertMessage":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   Dieser Methode wird ein Warteschlangenverweis übergeben. Wenn sie nicht bereits vorhanden ist, wird durch Aufrufen von [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync) eine neue Warteschlange erstellt. Anschließend wird durch Aufrufen von [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync) *newMessage* zur Warteschlange hinzugefügt.

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_InsertMessage":::

1. **Optional** Die maximale Gültigkeitsdauer für eine Nachricht wird standardmäßig auf sieben Tage festgelegt. Sie können für die Gültigkeitsdauer der Nachricht eine beliebige positive Zahl angeben. Der folgende Codeausschnitt fügt eine Nachricht hinzu, die *nie* abläuft.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

    Zum Hinzufügen einer Nachricht, die nicht abläuft, geben Sie in Ihrem Aufruf von **SendMessageAsync** den Wert `Timespan.FromSeconds(-1)` an.

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Initial.cs" id="snippet_SendNonExpiringMessage":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

    Zum Hinzufügen einer Nachricht, die nicht abläuft, geben Sie in Ihrem Aufruf von **AddMessageAsync** den Wert `Timespan.FromSeconds(-1)` an.

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Initial.cs" id="snippet_SendNonExpiringMessage":::

1. Speichern Sie die Datei .

Eine Warteschlangennachricht muss in einem Format vorliegen, das mit einer XML-Anforderung mit UTF-8-Codierung kompatibel ist. Eine Nachricht kann bis zu 64 KB groß sein. Wenn eine Nachricht Binärdaten enthält, codieren Sie die Nachricht mit [Base64-encode](/dotnet/api/system.convert.tobase64string).

## <a name="dequeue-messages"></a>Entfernen von Nachrichten aus Warteschlangen

Erstellen Sie eine neue Methode zum Abrufen einer Nachricht aus der Warteschlange. Sobald die Nachricht erfolgreich empfangen wurde, muss sie aus der Warteschlange gelöscht werden, damit sie nicht mehrmals verarbeitet wird.

1. Fügen Sie Ihrer **Program**-Klasse die neue Methode **RetrieveNextMessageAsync** hinzu.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   Diese Methode empfängt durch Aufrufen von [ReceiveMessagesAsync](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync) eine Nachricht aus der Warteschlange. Dabei wird im ersten Parameter der Wert „1“ zum Abrufen nur der nächsten Nachricht in der Warteschlange übergeben. Löschen Sie die empfangene Nachricht daher durch Aufrufen von [DeleteMessageAsync](/dotnet/api/azure.storage.queues.queueclient.deletemessageasync) aus der Warteschlange.

   Wenn eine Nachricht mit einer niedrigeren Version des SDK als v12 an die Warteschlange gesendet wird, wird Sie automatisch Base64-codiert. Ab v12 wurde diese Funktion entfernt. Wenn Sie eine Nachricht mit dem v12-SDK abrufen, wird diese nicht automatisch Base64-decodiert. Sie müssen den Inhalt selbst explizit [Base64-decodieren](/dotnet/api/system.convert.frombase64string).

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Initial.cs" id="snippet_InitialRetrieveMessage":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   Diese Methode empfängt durch Aufrufen von [GetMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync) eine Nachricht aus der Warteschlange. Löschen Sie die empfangene Nachricht daher durch Aufrufen von [DeleteMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync) aus der Warteschlange.

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Initial.cs" id="snippet_InitialRetrieveMessage":::

1. Speichern Sie die Datei .

## <a name="delete-an-empty-queue"></a>Löschen einer leeren Warteschlange

Es empfiehlt sich, am Ende eines Projekts zu ermitteln, ob Sie die erstellten Ressourcen noch benötigen. Ressourcen, die weiterhin ausgeführt werden, können Sie Geld kosten. Wenn die Warteschlange noch vorhanden, aber leer ist, fragen Sie den Benutzer, ob sie gelöscht werden soll.

1. Erweitern Sie die Methode **RetrieveNextMessageAsync**, um eine Aufforderung zum Löschen der leeren Warteschlange einzubeziehen.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_RetrieveMessage":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_RetrieveMessage":::

1. Speichern Sie die Datei .

## <a name="check-for-command-line-arguments"></a>Überprüfen auf Befehlszeilenargumente

Wenn es irgendwelche Befehlszeilenargumente gibt, die an die App übergeben werden, stellen Sie sich vor, dass es sich dabei um eine Nachricht handelt, die der Warteschlange hinzugefügt werden muss. Verknüpfen Sie die Argumente zu einer Zeichenfolge. Fügen Sie diese Zeichenfolge durch Aufrufen der zuvor hinzugefügten Methode **InsertMessageAsync** der Nachrichtenwarteschlange hinzu.

Wenn es keine Befehlszeilenargumente gibt, versuchen Sie es mit einem Abrufvorgang. Rufen Sie die Methode **RetrieveNextMessageAsync** zum Abrufen der nächsten Nachricht in der Warteschlange auf.

Warten Sie schließlich auf die Benutzereingabe, bevor Sie den Vorgang durch Aufrufen von **Console.ReadLine** beenden.

1. Erweitern Sie die Methode **Main**, um auf Befehlszeilenargumente zu überprüfen und auf die Benutzereingabe zu warten.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_Main":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_Main":::

1. Speichern Sie die Datei .

## <a name="complete-code"></a>Vollständiger Code

Hier ist die vollständige Codeauflistung für dieses Projekt.

   # <a name="net-v12"></a>[\.NET v12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_AllCode":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_AllCode":::
   ---

## <a name="build-and-run-the-app"></a>Erstellen und Ausführen der App

1. Führen Sie über die Befehlszeile im Projektverzeichnis den folgenden dotnet-Befehl zum Erstellen des Projekts aus.

   ```console
   dotnet build
   ```

1. Nachdem das Projekt erfolgreich erstellt wurde, führen Sie den folgenden Befehl aus, um der Warteschlange die erste Nachricht hinzuzufügen.

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

1. Führen Sie die App ohne Befehlszeilenargumente aus, um die erste Nachricht in der Warteschlange zu empfangen und zu entfernen.

   ```console
   dotnet run
   ```

1. Setzen Sie die Ausführung der App fort, bis alle Nachrichten entfernt wurden. Wenn Sie sie dann noch einmal ausführen, wird eine Meldung, dass die Warteschlange leer ist, und eine Aufforderung zum Löschen der Warteschlange angezeigt.

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
1. Hinzufügen und Entfernen von Nachrichten zu bzw. aus einer Warteschlange
1. Löschen einer Azure Storage-Warteschlange

In den Schnellstarts zu Azure-Warteschlangen finden Sie weitere Informationen.

> [!div class="nextstepaction"]
> [Warteschlangen – Schnellstart für das Portal](storage-quickstart-queues-portal.md)

- [Warteschlangen – Schnellstart für .NET](storage-quickstart-queues-dotnet.md)
- [Warteschlangen – Schnellstart für Java](storage-quickstart-queues-java.md)
- [Warteschlangen – Schnellstart für Python](storage-quickstart-queues-python.md)
- [Warteschlangen – Schnellstart für JavaScript](storage-quickstart-queues-nodejs.md)
