---
title: Aktivieren der Offlinesynchronisierung für Ihre Azure Mobile App (Xamarin iOS)
description: Erfahren Sie, wie Sie mobile App Service-Apps verwenden, um Offlinedaten in Ihrer Xamarin iOS-Anwendung zwischenzuspeichern und zu synchronisieren.
documentationcenter: xamarin
author: conceptdev
manager: cfowler
editor: ''
services: app-service\mobile
ms.assetid: 828a287c-5d58-4540-9527-1309ebb0f32b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 10/01/2016
ms.author: crdun
ms.openlocfilehash: 6a43ed0a50082cc37587752631c707bf9b5059ab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62097443"
---
# <a name="enable-offline-sync-for-your-xamarinios-mobile-app"></a>Aktivieren der Offlinesynchronisierung für Ihre mobile Xamarin.iOS-App
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>Übersicht
In diesem Lernprogramm wird die Funktion zur Offlinesynchronisierung von Azure Mobile Apps für Xamarin.iOS eingeführt. Die Offlinesynchronisierung ermöglicht Endbenutzern die Interaktion mit einer mobilen App (Anzeigen, Hinzufügen und Ändern von Daten) auch ohne Netzwerkverbindung. Änderungen werden in einer lokalen Datenbank gespeichert. Sobald das Gerät wieder online ist, werden diese Änderungen mit dem Remotedienst synchronisiert.

In diesem Tutorial aktualisieren Sie das Xamarin.iOS-App-Projekt aus [Erstellen einer Xamarin iOS-App] zur Unterstützung der Offlinefunktionen von Azure Mobile Apps. Wenn Sie das heruntergeladene Schnellstart-Serverprojekt nicht verwenden, müssen Sie Ihrem Projekt die Datenzugriffs-Erweiterungspakete hinzufügen. Weitere Informationen zu Servererweiterungspaketen finden Sie unter [Work with the .NET backend server SDK for Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)(in englischer Sprache).

Weitere Informationen zur Offlinesynchronisierungsfunktion finden Sie im Thema [Offlinedatensynchronisierung in Azure Mobile Apps].

## <a name="update-the-client-app-to-support-offline-features"></a>Aktualisieren der Client-App für die Unterstützung von Offlinefunktionen
Offlinefunktionen der mobilen Azure-App ermöglichen Ihnen die Interaktion mit einer lokalen Datenbank, wenn Sie sich in einem Offlineszenario befinden. Zur Verwendung dieser Funktionen in Ihrer App initialisieren Sie einen [SyncContext] in einem lokalen Speicher. Erstellen für die Tabelle einen Verweis über die [IMobileServiceSyncTable]-Schnittstelle. Als lokaler Speicher auf dem Gerät wird SQLite verwendet.

1. Öffnen Sie den NuGet-Paket-Manager im Projekt, das Sie im Tutorial [Erstellen einer Xamarin iOS-App] abgeschlossen haben. Suchen und installieren Sie anschließend das NuGet-Paket **Microsoft.Azure.Mobile.Client.SQLiteStore**.
2. Öffnen Sie die Datei „QSTodoService.cs“, und heben Sie die Auskommentierung der `#define OFFLINE_SYNC_ENABLED`-Definition auf.
3. Erstellen Sie die Client-App erneut, und führen Sie sie aus. Die App funktioniert genauso wie vor dem Aktivieren der Offlinesynchronisierung. Allerdings wird die lokale Datenbank jetzt mit Daten aufgefüllt, die in einem Offlineszenario verwendet werden können.

## <a name="update-sync"></a>Aktualisieren der App zur Trennung der Verbindung mit dem Back-End
In diesem Abschnitt trennen Sie die Verbindung mit Ihrem Mobile App-Back-End, um ein Offlineszenario zu simulieren. Wenn Sie Datenelemente hinzufügen, teilt Ihnen der Ausnahmehandler mit, dass sich die App im Offlinemodus befindet. In diesem Zustand werden neue Elemente im lokalen Speicher hinzugefügt. Sie werden mit dem Back-End für die mobile App synchronisiert, sobald nach dem Wiederherstellen der Verbindung der nächste Push ausgeführt wird.

1. Bearbeiten Sie „QSToDoService.cs“ im freigegebenen Projekt. Ändern Sie die Variable **applicationURL** so, dass Sie auf eine ungültige URL verweist:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Sie können auch Offlineverhalten demonstrieren, indem Sie WLAN und Mobilfunknetze auf dem Gerät deaktivieren oder den Flugzeugmodus verwenden.
2. Erstellen Sie die App, und führen Sie sie aus. Beachten Sie, dass die Synchronisierung bei der Aktualisierung fehlgeschlagen ist, als die App gestartet wurde.
3. Geben Sie neue Elemente ein. Beachten Sie, dass der Pushvorgang bei jedem Klick auf **Speichern** mit dem Status [CancelledByNetworkError] fehlschlägt. Die neuen Todo-Elemente sind jedoch im lokalen Speicher vorhanden und bleiben dort, bis sie per Push zum Back-End für die mobile App übertragen werden können.  Wenn Sie in einer Produktions-App diese Ausnahmen unterdrücken, verhält sich die Client-App, als ob sie immer noch mit dem Back-End für die mobile App verbunden wäre.
4. Schließen Sie die App und starten Sie sie erneut, um zu überprüfen, ob die neuen Elemente dauerhaft im lokalen Speicher gespeichert wurden.
5. (Optional) Wenn Sie Visual Studio auf einem PC installiert haben, öffnen Sie **Server-Explorer**. Navigieren Sie in **Azure**-> **SQL-Datenbanken** zu Ihrer Datenbank. Klicken Sie mit der rechten Maustaste auf Ihre Datenbank, und wählen Sie **In SQL Server-Objekt-Explorer öffnen**aus. Jetzt können nach Ihrer SQL-Datenbanktabelle und seinen Inhalten suchen. Stellen Sie sicher, dass die Daten in der Back-End-Datenbank nicht geändert wurden.
6. (Optional) Verwenden Sie ein REST-Tool wie Fiddler oder Postman, um Ihr mobiles Back-End mit einer GET-Abfrage in Form von `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`abzufragen.

## <a name="update-online-app"></a>Aktualisieren der App zum erneuten Verbinden des mobilen App-Back-Ends
In diesem Abschnitt verbinden Sie die App erneut mit dem mobilen App-Back-End. Dies simuliert, dass die App von einem Offlinestatus in einen Onlinestatus mit dem mobilen App-Back-End wechselt.   Wenn Sie die Netzwerkunterbrechung durch Deaktivieren der Netzwerkkonnektivität simuliert haben, sind keine Codeänderungen erforderlich.
Aktivieren Sie das Netzwerk erneut.  Beim ersten Ausführen der Anwendung wird die `RefreshDataAsync`-Methode aufgerufen. Dadurch wird wiederum `SyncAsync` aufgerufen, um den lokalen Speicher mit der Back-End-Datenbank zu synchronisieren.

1. Öffnen Sie „QSToDoService.cs“ im freigegebenen Projekt, und machen Sie Ihre Änderung der **applicationURL**-Eigenschaft rückgängig.
2. Erstellen Sie die App erneut, und führen Sie sie aus. Die App synchronisiert die lokalen Änderungen mit dem Azure Mobile App-Back-End mithilfe von Push- und Pullvorgängen, wenn die `OnRefreshItemsSelected`-Methode ausgeführt wird.
3. (Optional) Zeigen Sie die aktualisierten Daten mithilfe von SQL Server-Objekt-Explorer oder einem REST-Tool wie Fiddler an. Beachten Sie, dass die Daten zwischen der Azure Mobile App-Back-End-Datenbank und dem lokalen Speicher synchronisiert wurden.
4. Klicken Sie in der App auf das Kontrollkästchen neben einigen Elementen, um sie im lokalen Speicher abzuschließen.

   `CompleteItemAsync` ruft `SyncAsync` auf, um jedes abgeschlossene Element mit dem Mobile App-Back-End zu synchronisieren. `SyncAsync` ruft sowohl Push- als auch Pullvorgänge auf.
   **Bei jeder Ausführung eines Pullvorgangs gegen eine Tabelle, die der Client geändert hat, wird zunächst immer automatisch ein Pushvorgang im Clientsynchronisierungskontext ausgeführt**. Durch diesen impliziten Push wird sichergestellt, dass alle Tabellen im lokalen Speicher und die Beziehungen konsistent bleiben. Weitere Informationen zu diesem Verhalten finden Sie unter [Offlinedatensynchronisierung in Azure Mobile Apps].

## <a name="review-the-client-sync-code"></a>Überprüfen des Clientcodes für die Synchronisierung
Das Xamarin-Clientprojekt, das Sie heruntergeladen haben, nachdem Sie das Lernprogramm [Erstellen einer Xamarin iOS-App] abgeschlossen haben, enthält bereits Code zur Unterstützung der Offlinesynchronisierung mithilfe einer lokalen SQLite-Datenbank. Dies ist eine kurze Übersicht darüber, was bereits im Code des Lernprogramms enthalten ist. Eine grundlegende Übersicht über die Funktion finden Sie unter [Offlinedatensynchronisierung in Azure Mobile Apps].

* Bevor Tabellenvorgänge durchgeführt werden können, muss der lokale Speicher initialisiert werden. Die lokale Datenbank wird initialisiert, wenn `QSTodoService.InitializeStoreAsync()` von `QSTodoListViewController.ViewDidLoad()` ausgeführt wird. Mit dieser Methode wird eine neue lokale SQLite-Datenbank mit der `MobileServiceSQLiteStore`-Klasse erstellt, die vom Azure Mobile Apps-Client-SDK bereitgestellt wird.

    Die `DefineTable`-Methode erstellt eine Tabelle im lokalen Speicher, die mit den Feldern im bereitgestellten Typ übereinstimmt (in diesem Fall `ToDoItem`). Der Typ muss nicht alle Spalten der Remotedatenbank enthalten. Es ist möglich, nur eine Teilmenge der Spalten zu speichern.

        // QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }
* Das `todoTable`-Mitglied von `QSTodoService` ist vom Typ `IMobileServiceSyncTable` und nicht vom Typ `IMobileServiceTable`. IMobileServiceSyncTable leitet alle Erstellungs-, Lese-, Aktualisierungs- und Löschtabellenvorgänge (CRUD) an die lokale Datenbank.

    Sie legen fest, wann diese Änderungen per Pushvorgang durch Aufrufen von `IMobileServiceSyncContext.PushAsync()` an das Azure Mobile App-Back-End übertragen werden. Der Synchronisierungskontext hilft dabei, Tabellenbeziehungen durch das Nachverfolgen und Übertragen von Änderungen in allen Tabellen beizubehalten, die eine Clientanwendung geändert hat, wenn `PushAsync` aufgerufen wird.

    Der bereitgestellte Code ruft `QSTodoService.SyncAsync()` zum Synchronisieren ab, wenn die TodoItem-Liste aktualisiert wird oder ein TodoItem hinzugefügt wird oder abgeschlossen ist. Die App wird nach jeder lokalen Änderung synchronisiert. Bei einem Pullvorgang für eine Tabelle mit ausstehenden lokalen Updates, die durch den Kontext verfolgt werden, löst dieser Pullvorgang zuerst automatisch einen Kontextpush aus.

    Im bereitgestellten Code werden alle Datensätze in der `TodoItem`-Remotetabelle abgefragt, es ist aber auch möglich, Datensätze durch Übergeben einer Abfrage-ID und Abfrage an `PushAsync` zu filtern. Weitere Informationen finden Sie unter *Offlinedatensynchronisierung in Azure Mobile Apps* im Abschnitt [Offlinedatensynchronisierung in Azure Mobile Apps].

        // QSTodoService.cs
        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }

            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
        }

## <a name="additional-resources"></a>Weitere Ressourcen
* [Offlinedatensynchronisierung in Azure Mobile Apps]
* [Verwenden des verwalteten Clients für Azure Mobile Apps][8]

<!-- Images -->

<!-- URLs. -->
[Erstellen einer Xamarin iOS-App]: app-service-mobile-xamarin-ios-get-started.md
[Offlinedatensynchronisierung in Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
