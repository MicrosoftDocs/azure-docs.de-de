---
title: 'Senden und Empfangen von Ereignissen mit .NET Framework: Azure Event Hubs | Microsoft-Dokumentation'
description: Dieser Artikel enthält eine exemplarische Vorgehensweise für die Erstellung einer .NET Framework-Anwendung, die Ereignisse an Azure Event Hubs sendet.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: shvija
ms.openlocfilehash: 96ce71a7b3076adec169f103060a167b61c42d5c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65603509"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-net-framework"></a>Senden von Ereignissen an oder Empfangen von Ereignissen aus Event Hubs mithilfe von .NET Framework
Azure Event Hubs ist eine Big Data-Streamingplattform und ein Ereigniserfassungsdienst, der pro Sekunde Millionen von Ereignissen empfangen und verarbeiten kann. Event Hubs kann Ereignisse, Daten oder Telemetriedaten, die von verteilter Software und verteilten Geräten erzeugt wurden, verarbeiten und speichern. An einen Event Hub gesendete Daten können transformiert und mit einem beliebigen Echtzeitanalyse-Anbieter oder Batchverarbeitungs-/Speicheradapter gespeichert werden. Eine ausführliche Übersicht über Event Hubs finden Sie unter [Was ist Azure Event Hubs?](event-hubs-about.md) und [Event Hubs-Features im Überblick](event-hubs-features.md).

In diesem Tutorial wird gezeigt, wie Sie .NET Framework-Konsolenanwendungen in C# erstellen, die Ereignisse an einen Event Hub senden oder von diesem empfangen. 

## <a name="prerequisites"></a>Voraussetzungen
Zum Durchführen dieses Tutorials benötigen Sie Folgendes:

- [Microsoft Visual Studio 2019](https://visualstudio.com).
- **Erstellen Sie einen Event Hubs-Namespace und einen Event Hub**. Verwenden Sie zunächst das [Azure-Portal](https://portal.azure.com), um einen Namespace vom Typ „Event Hubs“ zu erstellen, und beschaffen Sie die Verwaltungsanmeldeinformationen, die Ihre Anwendung für die Kommunikation mit dem Event Hub benötigt. Erstellen Sie anhand der Anleitung in [diesem Artikel](event-hubs-create.md) einen Namespace und einen Event Hub. Gehen Sie dann wie im folgenden Artikel beschrieben vor, um die **Verbindungszeichenfolge für den Event Hub-Namespace** abzurufen: [Abrufen der Verbindungszeichenfolge](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Die Verbindungszeichenfolge wird im weiteren Verlauf dieses Tutorials benötigt.

## <a name="send-events"></a>Senden von Ereignisse 
In diesem Abschnitt erfahren Sie, wie Sie eine .NET Framework-Anwendung zum Senden von Ereignissen an einen Event Hub erstellen. 

### <a name="create-a-console-application"></a>Erstellen einer Konsolenanwendung

Erstellen Sie in Visual Studio mithilfe der Projektvorlage **Konsolenanwendung** ein neues Visual C#-Desktopanwendungsprojekt. Geben Sie dem Projekt den Namen **Sender**.
   
![Erstellen einer Konsolenanwendung](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp1.png)

### <a name="add-the-event-hubs-nuget-package"></a>Hinzufügen des Event Hubs-NuGet-Pakets

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das **Sender**-Projekt, und klicken Sie dann auf **NuGet-Pakete für Projektmappe verwalten**. 
2. Klicken Sie auf die Registerkarte **Durchsuchen**, und suchen Sie nach `WindowsAzure.ServiceBus`. Klicken Sie auf **Installieren**, und akzeptieren Sie die Nutzungsbedingungen. 
   
    ![Installieren des Service Bus-NuGet-Pakets](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp2.png)
   
    Visual Studio lädt das [NuGet-Paket mit der Azure Service Bus-Bibliothek](https://www.nuget.org/packages/WindowsAzure.ServiceBus)herunter, installiert es und fügt dem Projekt einen Verweis auf das Paket hinzu.

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Schreiben von Code zum Senden von Nachrichten an den Event Hub

1. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:
   
    ```csharp
    using System.Threading;
    using Microsoft.ServiceBus.Messaging;
    ```
2. Fügen Sie der **Program**-Klasse die folgenden Felder hinzu, und ersetzen Sie dabei die Platzhalterwerte durch den Namen des im vorigen Abschnitt erstellten Event Hubs und die zuvor gespeicherte Verbindungszeichenfolge auf Namespace-Ebene. Die Verbindungszeichenfolge für Ihren Event Hub können Sie im Azure-Portal auf der Event Hub-Seite unter **RootManageSharedAccessKey** aus **Verbindungszeichenfolge – Primärschlüssel** kopieren. Ausführliche Schritte finden Sie unter [Abrufen der Verbindungszeichenfolge](event-hubs-get-connection-string.md#get-connection-string-from-the-portal).
   
    ```csharp
    static string eventHubName = "Your Event Hub name";
    static string connectionString = "namespace connection string";
    ```
3. Fügen Sie der **Program** -Klasse die folgende Methode hinzu:
   
      ```csharp
      static void SendingRandomMessages()
      {
          var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
          while (true)
          {
              try
              {
                  var message = Guid.NewGuid().ToString();
                  Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
                  eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
              }
              catch (Exception exception)
              {
                  Console.ForegroundColor = ConsoleColor.Red;
                  Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
                  Console.ResetColor();
              }
       
              Thread.Sleep(200);
          }
      }
      ```
   
      Diese Methode sendet kontinuierlich Ereignisse mit einer Verzögerung von 200 ms an den Event Hub.
4. Fügen Sie abschließend der **Main** -Methode die folgenden Zeilen hinzu:
   
      ```csharp
      Console.WriteLine("Press Ctrl-C to stop the sender process");
      Console.WriteLine("Press Enter to start now");
      Console.ReadLine();
      SendingRandomMessages();
      ```
5. Führen Sie das Programm aus, und stellen Sie sicher, dass keine Fehler auftreten.
  
## <a name="receive-events"></a>Empfangen von Ereignissen
In diesem Abschnitt schreiben Sie eine .NET Framework-Konsolenanwendung zum Empfangen von Nachrichten von einem Event Hub mithilfe des [Ereignisprozessorhosts](event-hubs-event-processor-host.md). Der [Ereignisprozessorhost](event-hubs-event-processor-host.md) ist eine .NET-Klasse, die das Empfangen von Ereignissen von Event Hubs durch die Verwaltung von permanenten Prüfpunkten und parallelen Empfangsvorgängen von diesen Event Hubs vereinfacht. Mit dem Ereignisprozessorhost können Sie Ereignisse selbst dann auf mehrere Empfänger aufteilen, wenn sie in verschiedenen Knoten gehostet werden. 

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

### <a name="create-a-console-application"></a>Erstellen einer Konsolenanwendung

Erstellen Sie in Visual Studio mithilfe der Projektvorlage **Konsolenanwendung** ein neues Visual C#-Desktopanwendungsprojekt. Geben Sie dem Projekt den Namen **Receiver**.
   
![Erstellen einer Konsolenanwendung](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp1.png)

### <a name="add-the-event-hubs-nuget-package"></a>Hinzufügen des Event Hubs-NuGet-Pakets

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das **Receiver**-Projekt, und klicken Sie dann auf **NuGet-Pakete für Projektmappe verwalten**.
2. Klicken Sie auf die Registerkarte **Durchsuchen**, und suchen Sie nach `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Klicken Sie auf **Installieren**, und akzeptieren Sie die Nutzungsbedingungen.
   
    ![Suchen nach dem NuGet-Paket „EventProcessorHost“](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-eph-csharp1.png)
   
    Visual Studio lädt das NuGet-Paket [Azure Service Bus Event Hub – EventProcessorHost](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost)mit allen Abhängigkeiten herunter, installiert es und fügt dem Projekt einen Verweis auf das Paket hinzu.

### <a name="implement-the-ieventprocessor-interface"></a>Implementieren der IEventProcessor-Schnittstelle

1. Klicken Sie mit der rechten Maustaste auf das Projekt **Receiver**, klicken Sie auf **Hinzufügen** und dann auf **Klasse**. Geben Sie der neuen Klasse den Namen **SimpleEventProcessor**, und klicken Sie auf **Hinzufügen**, um die Klasse zu erstellen.
   
    ![Hinzufügen der SimpleEventProcessor-Klasse](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp2.png)
2. Fügen Sie die folgenden Anweisungen am Anfang der Datei "SimpleEventProcessor.cs" hinzu:
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      using System.Diagnostics;
      ```
    
3. Ersetzen Sie den Text der Klasse durch folgenden Code:
    
      ```csharp
      class SimpleEventProcessor : IEventProcessor
      {
        Stopwatch checkpointStopWatch;
        
        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }
        
        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }
        
        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
        
                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                    context.Lease.PartitionId, data));
            }
        
            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
      }
      ```
    
      Diese Klasse wird von **EventProcessorHost** zur Verarbeitung der vom Event Hub empfangenen Ereignisse aufgerufen. Die `SimpleEventProcessor`-Klasse verwendet eine Stoppuhr, um in regelmäßigen Abständen die „checkpoint“-Methode für den **EventProcessorHost**-Kontext aufzurufen. Dadurch wird sichergestellt, dass der Empfänger bei einem Neustart maximal nur die Daten verlieren kann, die in den letzten fünf Minuten verarbeitet wurden.

### <a name="update-the-main-method-to-use-simpleeventprocessor"></a>Aktualisieren der Main-Methode zur Verwendung von „SimpleEventProcessor“

1. Fügen Sie in der **Program**-Klasse die folgende `using`-Anweisungen am Anfang der Datei hinzu:
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      ```
    
2. Überschreiben Sie die `Main`-Methode in der `Program`-Klasse mit dem folgenden Code. Ersetzen Sie dabei den Event Hub-Namen und die zuvor gespeicherte Verbindungszeichenfolge auf Namespace-Ebene sowie das Speicherkonto und den Speicherschlüssel, die Sie in den vorherigen Abschnitten kopiert haben. 
    
      ```csharp
      static void Main(string[] args)
      {
        string eventHubConnectionString = "{Event Hubs namespace connection string}";
        string eventHubName = "{Event Hub name}";
        string storageAccountName = "{storage account name}";
        string storageAccountKey = "{storage account key}";
        string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);
        
        string eventProcessorHostName = Guid.NewGuid().ToString();
        EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
        Console.WriteLine("Registering EventProcessor...");
        var options = new EventProcessorOptions();
        options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
        eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();
        
        Console.WriteLine("Receiving. Press enter key to stop worker.");
        Console.ReadLine();
        eventProcessorHost.UnregisterEventProcessorAsync().Wait();
      }
      ```
    
3. Führen Sie das Programm aus, und stellen Sie sicher, dass keine Fehler auftreten.
  
## <a name="next-steps"></a>Nächste Schritte
Lesen Sie die folgenden Artikel: 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Features und Terminologie in Azure Event Hubs](event-hubs-features.md)
- [Event Hubs – häufig gestellte Fragen](event-hubs-faq.md)


<!-- Links -->
[EventProcessorHost]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
[Event Hubs overview]: event-hubs-about.md
[Event Hubs Programming Guide]: event-hubs-programming-guide.md
[Azure Storage account]:../storage/common/storage-create-storage-account.md
[Event Processor Host]: event-hubs-event-processor-host.md
[Azure portal]: https://portal.azure.com
